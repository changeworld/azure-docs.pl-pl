---
title: Skala geograficznie rozproszona ze środowisk usługi App Service, Azure
description: Dowiedz się, jak skalować w poziomie aplikacje przy użyciu rozkładu geograficznego usługi Traffic Manager i środowiska usługi App Service.
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
ms.custom: seodec18
ms.openlocfilehash: 769e6b9936ad6d3cb963e208cec4c49813f2b6d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130725"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Rozproszona geograficznie skala przy użyciu środowisk usługi App Service
## <a name="overview"></a>Omówienie

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Scenariusze aplikacji, które wymagają bardzo dużej skali będą mogli przekraczać pojemność zasobów obliczeniowych dostępne dla pojedynczego wdrożenia aplikacji.  Głosowanie aplikacji, imprez sportowych i zdarzenia programu Rozrywka są wszystkie przykładowe scenariusze, które wymagają bardzo dużej skali. Mogą być spełnione wymogi dużej skali, skalując w poziomie aplikacje z wieloma wdrożeniami aplikacji, które zostaną wprowadzone w jednym regionie, a także w regionach, aby zaspokoić wymagania ekstremalne obciążenie.

Środowiska usługi App Service jest idealną platformą do skalowanie w poziomie.  Po środowiska usługi App Service została wybrana konfiguracja obsługujące znanych żądań zakończonych, deweloperzy można wdrożyć dodatkowe środowiska usługi App Service w sposób "plików cookie krajarki" do osiągnięcia żądanego szczytowe obciążenie.

Na przykład załóżmy, że aplikację uruchomioną w konfiguracji usługi App Service Environment został przetestowany w celu obsługi żądań 20 K na sekundę (RPS).  Jeśli pojemność obciążenia szczytowego żądany jest 100 KB RPS, następnie pięciu (5) środowisk usługi App Service można tworzyć i skonfigurowany tak, aby upewnić się, że aplikacja może obsługiwać przewidywane maksymalnego obciążenia.

Ponieważ klienci zwykle dostępu do aplikacji przy użyciu domeny niestandardowej (lub znaczących), deweloperzy muszą sposób rozkładania żądań aplikacji na wszystkich wystąpień środowiska App Service Environment.  Doskonały sposób, aby osiągnąć ten cel jest do rozwiązania przy użyciu domeny niestandardowej [profilu usługi Azure Traffic Manager][AzureTrafficManagerProfile].  Profil usługi Traffic Manager można skonfigurować tak, aby wskazywała na wszystkie poszczególnych środowisk App Service Environment.  Usługa Traffic Manager będzie automatycznie obsługiwać klientów rozproszone we wszystkich środowiskach usługi App Service na podstawie obciążenia równoważenia ustawień w profilu usługi Traffic Manager.  Ta metoda działa niezależnie od tego, czy wszystkie środowiska usługi App Service są wdrożone w jednym regionie platformy Azure i wdrożony na całym świecie, w wielu regionach platformy Azure.

Ponadto ponieważ klientom dostęp do aplikacji przy użyciu domeny znaczącej, klienci są niebranie pod uwagę liczbę środowisk usługi App Service z aplikacją.  W wyniku deweloperzy mogą szybko i łatwo Dodawanie i usuwanie, na podstawie zaobserwowanego ruchu obciążenia środowisk usługi App Service.

Poniższy diagram koncepcyjny przedstawia aplikacji w poziomie skalowane do wewnątrz na trzech środowisk App Service Environment w jednym regionie.

![Architektura koncepcyjna][ConceptualArchitecture] 

W pozostałej części tego tematu przedstawiono kroki związane z konfiguracją topologii rozproszonej dla przykładowej aplikacji za pomocą wielu środowisk usługi App Service.

## <a name="planning-the-topology"></a>Planowanie topologii
Przed kompilacją limit rozmiaru aplikacji rozproszonej, warto ma kilka informacji fragmentów.

* **Domeny niestandardowej do aplikacji:**  Jaka jest nazwa domeny niestandardowej, który użytkownicy będą używać do dostępu do aplikacji?  Przykładowa aplikacja jest niestandardowa nazwa domeny `www.scalableasedemo.com`
* **Domena usługi Traffic Manager:**  Nazwa domeny, należy wybrać podczas tworzenia [profilu usługi Azure Traffic Manager][AzureTrafficManagerProfile].  Ta nazwa zostanie połączony z elementem *trafficmanager.net* sufiks Rejestracja zapisu domeny, który jest zarządzany przez usługę Traffic Manager.  Przykładowa aplikacja jest wybrana nazwa *pokaz skalowalne środowisko ase*.  Dzięki temu jest pełna nazwa domeny, który jest zarządzany przez usługę Traffic Manager *demo.trafficmanager.net skalowalne środowisko ase*.
* **Strategia skalowania zasięgu aplikacji:**  Zużycie aplikacji będą rozproszone na wielu środowisk App Service Environment w jednym regionie?  Wiele regionów?  Różne podejścia w obu przypadkach efekt?  Decyzja powinna być oparta na oczekiwania, z której pochodzą ruchu klientów, a także jak można skalować w pozostałej części aplikacji obsługi infrastruktury zaplecza.  Na przykład za pomocą aplikacji bezstanowych w 100% aplikacji może być wysoce skalowana na region platformy Azure, pomnożona przez środowisk usługi App Service wdrożone w wielu regionach platformy Azure przy użyciu kombinacji wielu środowisk usługi App Service.  Klienci z 15 + publicznych regionach platformy Azure dostępne do wyboru naprawdę mogą tworzyć zużycie aplikacja ogromnego na całym świecie.  Przykładowa aplikacja używana w tym artykule, aby uzyskać trzy środowisk usługi App Service zostały utworzone w jednym regionie platformy Azure (południowo-środkowe stany USA).
* **Konwencje nazewnictwa dla środowisk usługi App Service:**  Każdy środowiska App Service Environment wymaga unikatowej nazwy.  Po przekroczeniu jednego lub dwóch środowisk usługi App Service warto mają konwencję nazewnictwa ułatwiają identyfikację każdego środowiska App Service Environment.  Dla przykładowej aplikacji użyto prostych konwencji nazewnictwa.  Nazwy trzech środowisk App Service Environment są *fe1ase*, *fe2ase*, i *fe3ase*.
* **Konwencje nazewnictwa dla aplikacji:**  Ponieważ wiele wystąpień aplikacji zostanie wdrożony, nazwy jest wymagane dla poszczególnych wystąpień wdrożonej aplikacji.  Jedną mało znanego, ale bardzo wygodne funkcji środowiska usługi App Service jest, że taką samą nazwę aplikacji można stosować w wielu środowiskach usługi App Service.  Ponieważ każdy środowiska App Service Environment ma sufiks domeny unikatowy, deweloperom możliwość ponownego użycia otrzyma taką samą nazwę aplikacji w każdym środowisku.  Na przykład deweloper może mieć aplikacji o nazwie w następujący sposób: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*itp.  Dla przykładowej aplikacji do każdego wystąpienia aplikacji również ma unikatową nazwę.  Nazwy wystąpienia aplikacji, które są używane są *webfrontend1*, *webfrontend2*, i *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>Konfigurowanie profilu usługi Traffic Manager
Wiele wystąpień aplikacji, wdrożenie w wielu środowiskach usługi App Service wystąpień poszczególnych aplikacji można zarejestrować za pomocą usługi Traffic Manager.  Przykładowa aplikacja usługi Traffic Manager profil na potrzeby dla *demo.trafficmanager.net skalowalne środowisko ase* która może kierować klientów do żadnego z następujących wystąpień wdrożonej aplikacji:

* **webfrontend1.fe1ase.p.azurewebsites.net:**  Wystąpienie przykładowej aplikacji wdrożonych na pierwszej usługi App Service Environment.
* **webfrontend2.fe2ase.p.azurewebsites.net:**  Wystąpienie przykładowej aplikacji wdrożonych na drugim środowisku usługi App Service.
* **webfrontend3.fe3ase.p.azurewebsites.net:**  Wystąpienie przykładowej aplikacji wdrożonych w trzecim usługi App Service Environment.

Najprostszym sposobem zarejestrować wiele usługi Azure App Service punktów końcowych, działającą w **tego samego** region platformy Azure, to przy użyciu programu Powershell [pomocy technicznej usługi Azure Resource Manager Traffic Manager] [ ARMTrafficManager].  

Pierwszym krokiem jest tworzenie profilu usługi Azure Traffic Manager.  Poniższy kod pokazuje, jak profil został utworzony dla przykładowej aplikacji:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Zwróć uwagę sposób, w jaki *RelativeDnsName* ustawiono parametr *pokaz skalowalne środowisko ase*.  Jest to jak nazwa domeny *demo.trafficmanager.net skalowalne środowisko ase* zostanie utworzony i skojarzone z profilem usługi Traffic Manager.

*TrafficRoutingMethod* parametr określa zasady usługi Traffic Manager będzie używany do określenia sposobu rozkładu obciążenia klientów we wszystkich dostępnych punktów końcowych równoważenia obciążenia.  W tym przykładzie *ważona* wybrano metodę.  Spowoduje to żądania klientów są rozkładają się na wszystkie punkty końcowe zarejestrowanej aplikacji oparte na względnych wag skojarzone z każdego punktu końcowego. 

Za pomocą utworzony profil każdego wystąpienia aplikacji jest dodawany do profilu jako natywny punkt końcowy platformy Azure.  Poniższy kod pobiera odwołanie do każdej aplikacji sieci web frontonu, a następnie dodaje każdą aplikację jako punktu końcowego usługi Traffic Manager za *element TargetResourceId* parametru.

    $webapp1 = Get-AzWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Zwróć uwagę, jak istnieje jedno wywołanie *AzureTrafficManagerEndpointConfig Dodaj* dla poszczególnych wystąpień poszczególnych aplikacji.  *Element TargetResourceId* parametru w każdym poleceniu Powershell odwołuje się do jednego z trzech wystąpień wdrożonej aplikacji.  Profil usługi Traffic Manager rozprzestrzenia obciążenia we wszystkich trzech punktów końcowych zarejestrowany w profilu.

Wszystkie trzy punkty końcowe na użytek taką samą wartość (10) *wagi* parametru.  Skutkuje to usługi Traffic Manager rozproszenie żądania klientów we wszystkich wystąpieniach aplikacji trzy równomierne. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Wskazuje aplikacji niestandardowej domeny na domenę usługi Traffic Manager
Ostatnim krokiem niezbędne jest aby wskazywała domenę niestandardową aplikacji w domeny usługi Traffic Manager.  W przypadku przykładowej aplikacji oznacza to, wskazując `www.scalableasedemo.com` na `scalable-ase-demo.trafficmanager.net`.  Ten krok należy wykonać za pomocą rejestratora domen, która zarządza domeny niestandardowej.  

Rekord CNAME, za pomocą narzędzia do zarządzania domeny u rejestratora swojej firmy, rejestruje potrzeb ma zostać utworzony, który wskazuje niestandardowej domeny na domenę usługi Traffic Manager.  Na rysunku poniżej przedstawiono przykład tej konfiguracji CNAME wygląda następująco:

![Rekord CNAME dla domeny niestandardowej domeny][CNAMEforCustomDomain] 

Mimo że nieuwzględnione w tym temacie, należy pamiętać, że każde wystąpienie poszczególnych aplikacji musi mieć domenę niestandardową, zarejestrowano również.  W przeciwnym razie żądanie sprawia, że do wystąpienia aplikacji, a aplikacja nie ma domeny niestandardowej zarejestrowane z aplikacją, żądanie zakończy się niepowodzeniem.  

W tym przykładzie domena niestandardowa jest `www.scalableasedemo.com`, a każde wystąpienie aplikacji ma domenę niestandardową, skojarzone z nim.

![Domena niestandardowa][CustomDomain] 

Aby uzyskać podsumowanie rejestrowania domeny niestandardowej z usługi Azure App Service, zobacz następujący artykuł w [rejestracji domen niestandardowych][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Wypróbowanie topologii rozproszonej
Wynik końcowy usługi Traffic Manager i DNS konfiguracji jest, że żądania dla `www.scalableasedemo.com` będą przepływać przez następującej sekwencji:

1. Przeglądarki lub urządzenia spowoduje, że wyszukiwanie DNS `www.scalableasedemo.com`
2. Wyszukiwanie DNS, aby zostać przekierowane do usługi Azure Traffic Manager powoduje, że wpis CNAME u rejestratora domen.
3. Wyszukiwanie DNS wysłaniu *demo.trafficmanager.net skalowalne środowisko ase* na jednym z serwerów usługi Azure DNS usługi Traffic Manager.
4. Oparte na zasady równoważenia obciążenia ( *TrafficRoutingMethod* parametru użyta wcześniej podczas tworzenia profilu usługi Traffic Manager), usługa Traffic Manager zostanie wybierz jedną z skonfigurowane punkty końcowe i zwrócić nazwy FQDN tego punktu końcowego przeglądarki lub urządzenia.
5. Ponieważ nazwa FQDN punktu końcowego jest adres Url wystąpienia aplikacji uruchomionych na usługę App Service Environment, przeglądarki lub urządzenia zostanie wyświetlone pytanie, serwer DNS platformy Azure firmy Microsoft, aby rozpoznać nazwę FQDN jako adres IP. 
6. Przeglądarki lub urządzenia będzie wysyłać żądania HTTP/Https na adres IP.  
7. Żądanie dociera do jednej z wystąpień aplikacji uruchomionych na poszczególnych środowisk usługi App Service.

Konsola obraz poniżej przedstawia wyszukiwania DNS dla domeny niestandardowej przykładowej aplikacji, rozwiązania do wystąpienia aplikacji uruchomionej na jednym z trzech przykładowych środowisk usługi App Service (w tym przypadku drugim z trzech środowisk App Service Environment):

![Wyszukiwanie DNS][DNSLookup] 

## <a name="additional-links-and-information"></a>Dodatkowe linki i informacje
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
