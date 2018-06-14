---
title: Rozproszona geograficznie skala przy użyciu środowisk usługi App Service
description: Dowiedz się, jak skalować w poziomie aplikacji z Menedżera ruchu i środowiska usługi App Service przy użyciu dystrybucji geo.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.openlocfilehash: 21f747239e565aba79a84c8e946a71e306b64968
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836833"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Rozproszona geograficznie skala przy użyciu środowisk usługi App Service
## <a name="overview"></a>Omówienie
Scenariusze aplikacji, które wymagają bardzo dużej skali, może być dłuższa niż pojemność zasobów obliczeniowych dostępne dla pojedynczego wdrożenia aplikacji.  Głosowanie aplikacji, sportowych i zdarzenia programu Rozrywka są wszystkie przykładowe scenariusze, które wymagają bardzo dużej skali. Wymagania dotyczące wysokiej skali można spełnić przez poziomie skalowania aplikacji z wieloma wdrożeniami aplikacji została wprowadzona w pojedynczym regionie, a także w regionach, do obsługi obciążenia extreme wymagania.

Środowiska usługi aplikacji jest idealną platformą dla poziomych skalowania w poziomie.  Raz środowiska usługi aplikacji konfiguracji została wybrana obsługujące współczynnika żądań znane, deweloperzy można wdrożyć dodatkowe środowiska usługi App Service w sposób "krajarki plik cookie" do osiągnięcia obciążenie żądaną godzinami szczytu.

Załóżmy na przykład aplikacji uruchomionej na konfiguracji środowiska usługi aplikacji był testowany do obsługi żądań 20 KB na sekundę (RPS).  Jeśli pojemność obciążenia szczytowego żądaną 100 KB RPS, następnie pięć (5) środowiska usługi App Service można tworzyć i skonfigurowany tak, aby upewnić się, że aplikacja może obsługiwać maksymalne obciążenie planowane.

Ponieważ klienci zwykle uzyskują dostęp do aplikacji przy użyciu domeny niestandardowe (lub niestandardowych), deweloperzy muszą rozdystrybuować żądań aplikacji we wszystkich wystąpień środowiska usługi aplikacji.  Doskonałym sposobem, w tym celu jest do rozwiązania przy użyciu domeny niestandardowej [profilu Menedżera ruchu Azure][AzureTrafficManagerProfile].  Profilu usługi Traffic Manager można skonfigurować tak, aby wskazywały na wszystkich poszczególnych środowisk usługi aplikacji.  Menedżer ruchu obsługiwać automatycznie dystrybucji klienci we wszystkich środowiska usługi aplikacji, które są oparte na ustawienia w profilu usługi Traffic Manager równoważenia obciążenia.  Ta metoda działa niezależnie od tego, czy wszystkie środowiska usługi aplikacji są wdrożone w pojedynczym regionie Azure, lub wdrażanych na całym świecie, w wielu regionach platformy Azure.

Ponadto ponieważ klientom dostęp do aplikacji za pośrednictwem domeny niestandardowych, klienci są bez "świadomości" liczby środowiska usługi App Service uruchomiona aplikacja.  W związku z tym deweloperzy mogą szybko i łatwo dodawania i usuwania, oparte na obciążenie sieci obserwowanych środowiska usługi App Service.

Poniższy diagram koncepcyjny przedstawia aplikacji, skalowanie w poziomie między trzy środowiska usługi App Service w pojedynczym regionie.

![Architektura koncepcyjna][ConceptualArchitecture] 

W pozostałej części tego tematu przeprowadzi Cię przez etapy Konfigurowanie topologii rozproszonej dla przykładowej aplikacji przy użyciu wielu środowiska usługi App Service.

## <a name="planning-the-topology"></a>Planowanie topologii
Przed rozpoczęciem budowania limit rozmiaru aplikacji rozproszonej, pomaga ma kilka części informacji wcześniejsze.

* **Domeny niestandardowej dla aplikacji:** co to jest nazwa domeny niestandardowej, którego użytkownicy będą używać dostęp do tej aplikacji?  Przykładowa aplikacja niestandardowa nazwa domeny jest *www.scalableasedemo.com*
* **Domeny usługi Traffic Manager:** nazwą domeny należy wybrać podczas tworzenia [profilu Menedżera ruchu Azure][AzureTrafficManagerProfile].  Ta nazwa zostanie połączona z *trafficmanager.net* sufiks zarejestrować wpis domeny, który jest zarządzany przez Menedżera ruchu.  Przykładowa aplikacja jest wybrana nazwa *skalowalne pokaz ase*.  W związku z tym nazwa domeny pełnego, który jest zarządzany przez Menedżera ruchu jest *skalowalne demo.trafficmanager.net ase*.
* **Strategia skalowanie rozmiaru aplikacji:** będą zużycie aplikacji być rozproszone na wielu środowiska usługi App Service w pojedynczym regionie?  Wiele regionów?  Mieszane i dopasowywać w obu przypadkach efekt?  Decyzja powinny być oparte na oczekiwań, z których pochodzą ruchu klientów, a także jak można skalować reszty aplikacji obsługujących infrastruktura wewnętrzna.  Na przykład przy użyciu aplikacji bezstanowych 100% aplikacji może być ogromną skalę skalowany przy użyciu kombinacji wielu środowiska usługi App Service dla regionu Azure, pomnożona przez wdrożone w różnych regionach platformy Azure środowiska usługi App Service.  Z 15 + publicznego regiony platformy Azure dostępna do wyboru klienci mogą tworzyć naprawdę wpływu na całym świecie hiperskali aplikacji.  Dla przykładowej aplikacji używane w tym artykule trzy środowiska usługi aplikacji zostały utworzone w pojedynczym regionie Azure (południowo-środkowe stany).
* **Konwencja nazewnictwa dla środowiska usługi App Service:** każdego środowiska usługi aplikacji wymaga unikatowej nazwy.  Poza jednego lub dwóch środowiska usługi App Service jest pomocne konwencji nazewnictwa, aby ułatwić identyfikację każdego środowiska usługi aplikacji.  Dla przykładowej aplikacji użyto prostych konwencji nazewnictwa.  Nazwy trzy środowiska usługi aplikacji są *fe1ase*, *fe2ase*, i *fe3ase*.
* **Konwencja nazewnictwa dla aplikacji:** ponieważ wiele wystąpień aplikacji zostanie wdrożony, nazwę jest wymagane dla każdego wystąpienia wdrożonej aplikacji.  Jedną mało znanego, ale bardzo wygodny funkcji środowiska usługi App Service jest, że taką samą nazwę aplikacji mogą być używane w wielu środowiskach usługi aplikacji.  Ponieważ każde środowisko usługi aplikacji ma sufiks domeny unikatowy, deweloperzy mogą być ponownie użyć dokładnej nazwy aplikacji w każdym środowisku.  Na przykład deweloper może mieć następujące nazwy aplikacji: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*itp.  Dla przykładowej aplikacji jednak każde wystąpienie aplikacji również ma unikatową nazwę.  Nazwy wystąpienia aplikacji, używane są *webfrontend1*, *webfrontend2*, i *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>Konfigurowanie profilu Menedżera ruchu
Po wielu wystąpień aplikacji są wdrażane na wielu środowiska usługi App Service, wystąpień poszczególnych aplikacji może być zarejestrowany z usługą Traffic Manager.  Przykładowa aplikacja usługi Traffic Manager profilu na potrzeby dla *skalowalne demo.trafficmanager.net ase* która może kierować klientów do żadnego z następujących wystąpień wdrożonej aplikacji:

* **webfrontend1.fe1ase.p.azurewebsites.NET:** wystąpienia przykładowej aplikacji wdrożonych na pierwszym środowiska usługi aplikacji.
* **webfrontend2.fe2ase.p.azurewebsites.NET:** wystąpienia przykładowej aplikacji wdrożonych na drugi środowiska usługi aplikacji.
* **webfrontend3.fe3ase.p.azurewebsites.NET:** wystąpienia przykładowej aplikacji wdrożonych na trzeci środowiska usługi aplikacji.

Najprostszym sposobem, aby zarejestrować wiele usłudze Azure App Service punktów końcowych, wszystkich uruchomionych w **tego samego** region platformy Azure, to przy użyciu programu Powershell [pomocy technicznej usługi Azure Resource Manager Traffic Manager] [ ARMTrafficManager].  

Pierwszym krokiem jest ut worzyć profil Menedżera ruchu Azure.  Poniższy kod przedstawia sposób profil został utworzony dla przykładowej aplikacji:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Powiadomienie jak *RelativeDnsName* ustawiono parametr *skalowalne pokaz ase*.  Jest to jak nazwa domeny *skalowalne demo.trafficmanager.net ase* skojarzonego z profilem Menedżera ruchu.

*TrafficRoutingMethod* parametru definiuje zasady Menedżera ruchu użyje do określenia sposobu rozkładu obciążenia klienta we wszystkich dostępnych punktów końcowych równoważenia obciążenia.  W tym przykładzie *ważone* wybrano metodę.  Spowoduje to żądania klientów są rozkładane wszystkie punkty końcowe zarejestrowanej aplikacji oparte na względnych wag skojarzone z każdym punkcie końcowym. 

Z profilem utworzone każde wystąpienie aplikacji jest dodawana do profilu jako natywny punktu końcowego platformy Azure.  Poniższy kod pobiera odwołanie do każdej aplikacji sieci web frontonu, a następnie dodanie każdej aplikacji jako punktu końcowego Menedżera ruchu poprzez *element TargetResourceId* parametru.

    $webapp1 = Get-AzureRMWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzureRMWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzureRMWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Zwróć uwagę, jak istnieje jedno wywołanie *AzureTrafficManagerEndpointConfig Dodaj* dla każdego wystąpienia poszczególnych aplikacji.  *Element TargetResourceId* parametr w każdej polecenia programu Powershell odwołuje się do jednego z trzech wystąpień wdrożonej aplikacji.  Profil Menedżera ruchu zostaną rozkładu obciążenia we wszystkich trzech punktów końcowych w zarejestrowany w profilu.

Wszystkie trzy punkty końcowe używać tej samej wartości (10) dla *wagi* parametru.  W efekcie żądania klientów rozmieszczania Menedżer ruchu we wszystkich wystąpieniach aplikacji trzy równomierne. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Wskazuje domeny niestandardowej aplikacji na domeny usługi Traffic Manager
Ostatnim krokiem niezbędne jest punkt domeny niestandardowej aplikacji na domeny usługi Traffic Manager.  Przykładowa aplikacja oznacza to wskazanie *www.scalableasedemo.com* w *skalowalne demo.trafficmanager.net ase*.  Ten krok musi być przeprowadzona za rejestratora domen, która zarządza domeny niestandardowej.  

Rekord CNAME przy użyciu narzędzia do zarządzania rejestratora domen, rejestruje potrzeb ma zostać utworzony, który wskazuje domeny niestandardowej domeny usługi Traffic Manager.  Na ilustracji poniżej przedstawiono przykład konfiguracji tego CNAME wygląda następująco:

![CNAME dla domeny niestandardowej][CNAMEforCustomDomain] 

Mimo że nie omówione w tym temacie, należy pamiętać, że każde wystąpienie poszczególnych aplikacji musi mieć domeny niestandardowej również zarejestrowany z nim.  W przeciwnym razie żądania ułatwia do wystąpienia aplikacji, a aplikacja nie ma domeny niestandardowej zarejestrowany z aplikacją, żądanie zakończy się niepowodzeniem.  

W tym przykładzie domena niestandardowa jest *www.scalableasedemo.com*, i każdego wystąpienia aplikacji ma domeny niestandardowej skojarzonych z nim.

![Domena niestandardowa][CustomDomain] 

Dla drużyn z rejestrowania domeny niestandardowej z aplikacjami Azure App Service, zobacz następujący artykuł w [rejestracji domen niestandardowych][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Testuje topologii rozproszonej
Wynik końcowy Menedżera ruchu i DNS konfiguracji jest, która zażąda *www.scalableasedemo.com* będą przepływać do następującej:

1. Przeglądarki lub urządzenia spowoduje wyszukiwania DNS *www.scalableasedemo.com*
2. Wyszukiwanie DNS, aby zostać przekierowane do usługi Azure Traffic Manager powoduje, że wpis CNAME u rejestratora domen.
3. Wyszukiwania DNS jest przewidzieć *skalowalne demo.trafficmanager.net ase* na jednym z serwerów DNS Menedżera ruchu Azure.
4. Oparte na zasadach równoważenia obciążenia ( *TrafficRoutingMethod* parametru użytego wcześniej podczas tworzenia profilu usługi Traffic Manager), Menedżera ruchu zostaną wybierz jedną z skonfigurowanych punktów końcowych i zwraca nazwę FQDN tego punktu końcowego przeglądarki lub urządzenia.
5. Ponieważ nazwy FQDN punktu końcowego jest adres Url wystąpienia aplikacji uruchomionych na środowisku usługi aplikacji, przeglądarki lub urządzenie zapyta, serwer DNS Azure firmy Microsoft, aby rozpoznać nazwę FQDN jako adres IP. 
6. Przeglądarki lub urządzenie będzie wysyłać żądania HTTP/S do adresu IP.  
7. Żądanie pojawią się w jednym z wystąpień aplikacji uruchomionych na jeden z tych środowisk usługi aplikacji.

Obraz konsoli poniżej przedstawia wyszukiwania DNS dla domeny niestandardowej przykładowej aplikacji, rozwiązania do wystąpienia aplikacji uruchomionych na jeden z trzech środowiska usługi aplikacji przykładowej (w tym przypadku drugi trzy środowiska usługi aplikacji):

![Wyszukiwania DNS][DNSLookup] 

## <a name="additional-links-and-information"></a>Linki do dodatkowych i informacji
Dokumentacja programu Powershell [pomocy technicznej usługi Azure Resource Manager Traffic Manager][ARMTrafficManager].  

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]: ../../traffic-manager/traffic-manager-manage-profiles.md
[ARMTrafficManager]: ../../traffic-manager/traffic-manager-powershell-arm.md
[RegisterCustomDomain]: ../app-service-web-tutorial-custom-domain.md


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
