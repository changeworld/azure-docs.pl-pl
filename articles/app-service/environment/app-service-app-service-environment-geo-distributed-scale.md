---
title: Skala rozproszona geograficzna
description: Dowiedz się, jak skalować aplikacje w poziomie przy użyciu dystrybucji geograficznej za pomocą usługi Traffic Manager i app service environments.
author: stefsch
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 7ab04e23b838f2dfd39b73476db7492947d62e6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688815"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Rozproszona geograficznie skala przy użyciu środowisk usługi App Service
## <a name="overview"></a>Omówienie

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Scenariusze aplikacji, które wymagają bardzo dużej skali może przekroczyć pojemność zasobów obliczeniowych dostępnych dla pojedynczego wdrożenia aplikacji.  Wnioski o głosowanie, wydarzenia sportowe i telewizyjne wydarzenia rozrywkowe to przykłady scenariuszy, które wymagają bardzo dużej skali. Wymagania dotyczące dużej skali mogą być spełnione przez skalowanie w poziomie aplikacji, z wielu wdrożeń aplikacji w jednym regionie, a także w różnych regionach, do obsługi wymagań ekstremalnych obciążenia.

Środowiska usługi app service są idealną platformą do skalowania poziomego w poziomie.  Po wybraniu konfiguracji środowiska usługi app service, która może obsługiwać znaną szybkość żądania, deweloperzy mogą wdrożyć dodatkowe środowiska usługi app service w sposób "cookie cutter", aby osiągnąć żądaną maksymalną pojemność obciążenia.

Załóżmy na przykład, że aplikacja uruchomiona w konfiguracji środowiska usługi app service została przetestowana do obsługi żądań 20K na sekundę (RPS).  Jeśli żądana maksymalna pojemność obciążenia wynosi 100K RPS, można utworzyć pięć (5) środowisk usługi app service i skonfigurować je tak, aby zapewnić, że aplikacja może obsłużyć maksymalne przewidywane obciążenie.

Ponieważ klienci zazwyczaj uzyskują dostęp do aplikacji przy użyciu domeny niestandardowej (lub próżności), deweloperzy potrzebują sposobu dystrybucji żądań aplikacji we wszystkich wystąpieniach środowiska usługi App Service.  Doskonałym sposobem na osiągnięcie tego celu jest rozwiązanie domeny niestandardowej przy użyciu [profilu usługi Azure Traffic Manager.][AzureTrafficManagerProfile]  Profil usługi Traffic Manager można skonfigurować tak, aby wskazywał wszystkie poszczególne środowiska usługi app service.  Usługa Traffic Manager będzie automatycznie obsługiwać klientów dystrybucji we wszystkich środowiskach usługi app service na podstawie ustawień równoważenia obciążenia w profilu usługi Traffic Manager.  To podejście działa niezależnie od tego, czy wszystkie środowiska usługi app service są wdrażane w jednym regionie platformy Azure lub wdrażane na całym świecie w wielu regionach platformy Azure.

Ponadto ponieważ klienci uzyskują dostęp do aplikacji za pośrednictwem domeny próżności, klienci nie są świadomi liczby środowisk usługi app z uruchomień aplikacji.  W rezultacie deweloperzy mogą szybko i łatwo dodawać i usuwać środowiska usługi app service na podstawie obserwowanego obciążenia ruchem.

Poniższy diagram koncepcyjny przedstawia aplikację skalowane w poziomie w trzech środowiskach usługi app service w jednym regionie.

![Architektura konceptualna][ConceptualArchitecture] 

W dalszej części tego tematu przechodzi przez kroki związane z konfigurowaniem topologii rozproszonej dla przykładowej aplikacji przy użyciu wielu środowisk usługi app service.

## <a name="planning-the-topology"></a>Planowanie topologii
Przed zbudowaniem rozproszonego śladu aplikacji, pomaga mieć kilka informacji z wyprzedzeniem.

* **Domena niestandardowa aplikacji:**  Jaka jest niestandardowa nazwa domeny, której klienci będą używać do uzyskiwania dostępu do aplikacji?  W przykładowej aplikacji niestandardowa nazwa domeny jest`www.scalableasedemo.com`
* **Domena Usługi Traffic Manager:**  Podczas tworzenia profilu usługi Azure Traffic [Manager][AzureTrafficManagerProfile]należy wybrać nazwę domeny.  Ta nazwa zostanie połączona z sufiksem *trafficmanager.net,* aby zarejestrować wpis domeny zarządzany przez Menedżera ruchu.  W przypadku przykładowej aplikacji wybrana nazwa jest *skalowalna-ase-demo*.  W rezultacie pełna nazwa domeny zarządzana przez Traffic Manager jest *scalable-ase-demo.trafficmanager.net*.
* **Strategia skalowania śladu aplikacji:**  Czy ślad aplikacji będzie dystrybuowany w wielu środowiskach usługi app service w jednym regionie?  Wiele regionów?  Mix-and-match obu podejść?  Decyzja powinna opierać się na oczekiwaniach dotyczących tego, skąd będzie pochodzić ruch klientów, a także na tym, jak dobrze można skalować pozostałą część infrastruktury zaplecza obsługującej aplikację.  Na przykład w przypadku aplikacji w 100% bezstanowej aplikacja może być masowo skalowana przy użyciu kombinacji wielu środowisk usługi app service na region platformy Azure, pomnożona przez środowiska usługi aplikacji wdrożone w wielu regionach platformy Azure.  Dzięki ponad 15 publicznym regionom platformy Azure do wyboru klienci mogą naprawdę utworzyć ogólnoświatową infrastrukturę aplikacji na dużą skalę.  Dla przykładowej aplikacji używanej w tym artykule utworzono trzy środowiska usługi app service w jednym regionie platformy Azure (południowo-środkowe stany USA).
* **Konwencja nazewnictwa dla środowisk usługi app service:**  Każde środowisko usługi aplikacji wymaga unikatowej nazwy.  Poza jednym lub dwoma środowiskami usługi app service warto mieć konwencję nazewnictwa, aby ułatwić identyfikację każdego środowiska usługi app service.  Dla przykładowej aplikacji użyto prostej konwencji nazewnictwa.  Nazwy trzech środowisk usługi app service są *fe1ase*, *fe2ase*i *fe3ase*.
* **Konwencja nazewnictwa aplikacji:**  Ponieważ wiele wystąpień aplikacji zostanie wdrożonych, nazwa jest potrzebna dla każdego wystąpienia wdrożonej aplikacji.  Jedną z mało znanych, ale bardzo wygodne funkcje środowiska usługi app service jest to, że ta sama nazwa aplikacji może być używana w wielu środowiskach usługi app service.  Ponieważ każde środowisko usługi aplikacji ma unikatowy sufiks domeny, deweloperzy mogą ponownie użyć dokładnie tej samej nazwy aplikacji w każdym środowisku.  Na przykład deweloper może mieć aplikacje o następującej nazwie: *myapp.foo1.p.azurewebsites.net,* *myapp.foo2.p.azurewebsites.net,* *myapp.foo3.p.azurewebsites.net*itp.  Dla przykładowej aplikacji, chociaż każde wystąpienie aplikacji ma również unikatową nazwę.  Nazwy wystąpień aplikacji używane są *webfrontend1*, *webfrontend2*i *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>Konfigurowanie profilu Usługi Traffic Manager
Po wdrożeniu wielu wystąpień aplikacji w wielu środowiskach usługi app service poszczególne wystąpienia aplikacji mogą być rejestrowane w usłudze Traffic Manager.  W przypadku przykładowej aplikacji potrzebny jest profil usługi Traffic Manager, *scalable-ase-demo.trafficmanager.net,* które mogą kierować klientów do dowolnego z następujących wdrożonych wystąpień aplikacji:

* **webfrontend1.fe1ase.p.azurewebsites.net:**  Wystąpienie przykładowej aplikacji wdrożonej w pierwszym środowisku usługi app service.
* **webfrontend2.fe2ase.p.azurewebsites.net:**  Wystąpienie przykładowej aplikacji wdrożonej w drugim środowisku usługi app service.
* **webfrontend3.fe3ase.p.azurewebsites.net:**  Wystąpienie przykładowej aplikacji wdrożonej w trzecim środowisku usługi app service.

Najprostszym sposobem zarejestrowania wielu punktów końcowych usługi Azure App Service, wszystkie uruchomione w **tym samym** regionie platformy Azure, jest obsługa usługi Powershell Azure Resource Manager [Traffic Manager.][ARMTrafficManager]  

Pierwszym krokiem jest utworzenie profilu usługi Azure Traffic Manager.  Poniższy kod pokazuje, jak profil został utworzony dla przykładowej aplikacji:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Zwróć uwagę, jak *parametr RelativeDnsName* został ustawiony na *skalowalny-ase-demo*.  W ten sposób nazwa domeny *scalable-ase-demo.trafficmanager.net* jest tworzona i skojarzona z profilem usługi Traffic Manager.

*TrafficRoutingMethod* Parametr definiuje zasady równoważenia obciążenia Traffic Manager będzie używany do określenia, jak rozłożyć obciążenia klienta we wszystkich dostępnych punktów końcowych.  W tym przykładzie wybrano metodę *ważoną.*  Spowoduje to, że żądania klientów są rozłożone na wszystkie punkty końcowe zarejestrowanej aplikacji na podstawie względnych wag skojarzonych z każdym punktem końcowym. 

Po utworzeniu profilu każde wystąpienie aplikacji jest dodawane do profilu jako natywny punkt końcowy platformy Azure.  Poniższy kod pobiera odwołanie do każdej aplikacji sieci web frontonu, a następnie dodaje każdą aplikację jako punkt końcowy usługi Traffic Manager za pomocą *parametru TargetResourceId.*

    $webapp1 = Get-AzWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Zwróć uwagę, jak istnieje jedno wywołanie *Add-AzureTrafficManagerEndpointConfig* dla każdego wystąpienia aplikacji.  *Parametr TargetResourceId* w każdym poleceniu programu Powershell odwołuje się do jednego z trzech wdrożonych wystąpień aplikacji.  Profil usługi Traffic Manager rozkłada obciążenie we wszystkich trzech punktach końcowych zarejestrowanych w profilu.

Wszystkie trzy punkty końcowe używają tej samej wartości (10) dla *parametru Waga.*  Powoduje to, że usługa Traffic Manager stosunkowo równomiernie rozmieszcza żądania klientów we wszystkich trzech wystąpieniach aplikacji. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Wskazywanie domeny niestandardowej aplikacji w domenie Menedżera ruchu
Ostatnim krokiem jest skierowanie domeny niestandardowej aplikacji do domeny Usługi Traffic Manager.  Dla przykładowej aplikacji oznacza `www.scalableasedemo.com` `scalable-ase-demo.trafficmanager.net`to wskazanie na .  Ten krok należy wykonać z rejestratorem domen, który zarządza domeną niestandardową.  

Korzystając z narzędzi do zarządzania domeną rejestratora, należy utworzyć rekordy CNAME, które wskazują domenę niestandardową w domenie Usługi Traffic Manager.  Na poniższym rysunku przedstawiono przykład tego, jak wygląda ta konfiguracja CNAME:

![Nazwa CNAME dla domeny niestandardowej][CNAMEforCustomDomain] 

Chociaż nie zostały omówione w tym temacie, należy pamiętać, że każde wystąpienie aplikacji musi mieć domenę niestandardową zarejestrowaną w nim, jak również.  W przeciwnym razie jeśli żądanie udostępnia go do wystąpienia aplikacji, a aplikacja nie ma domeny niestandardowej zarejestrowanej w aplikacji, żądanie zakończy się niepowodzeniem.  

W tym przykładzie `www.scalableasedemo.com`domena niestandardowa jest , a każde wystąpienie aplikacji ma domenę niestandardową skojarzoną z nią.

![Domena niestandardowa][CustomDomain] 

Aby uzyskać podsumowanie rejestrowania domeny niestandardowej w aplikacjach usługi Azure App Service, zobacz następujący artykuł dotyczący [rejestrowania domen niestandardowych][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Wypróbowanie topologii rozproszonej
Efektem końcowym konfiguracji Usługi Traffic Manager i `www.scalableasedemo.com` DNS jest to, że żądania będą przepływać przez następującą sekwencję:

1. Przeglądarka lub urządzenie dokona wyszukiwania DNS dla`www.scalableasedemo.com`
2. Wpis CNAME u rejestratora domen powoduje, że wyszukiwanie DNS zostanie przekierowane do usługi Azure Traffic Manager.
3. Wyszukiwanie DNS jest dla *scalable-ase-demo.trafficmanager.net* względem jednego z serwerów DNS usługi Azure Traffic Manager.
4. Na podstawie zasad równoważenia obciążenia *(TrafficRoutingMethod* parametr używany wcześniej podczas tworzenia profilu usługi Traffic Manager), Usługa Traffic Manager wybierze jeden ze skonfigurowanych punktów końcowych i zwróci FQDN tego punktu końcowego do przeglądarki lub urządzenia.
5. Ponieważ nazwa FQDN punktu końcowego jest adresem URL wystąpienia aplikacji uruchomionego w środowisku usługi app service, przeglądarka lub urządzenie poprosi serwer DNS platformy Microsoft Azure o rozpoznanie nazwy FQDN na adres IP. 
6. Przeglądarka lub urządzenie wyśle żądanie HTTP/S na adres IP.  
7. Żądanie zostanie dotrze do jednego z wystąpień aplikacji uruchomionych w jednym ze środowisk usługi app service.

Poniższy obraz konsoli przedstawia wyszukiwanie DNS dla domeny niestandardowej przykładowej aplikacji pomyślnie rozwiązujące wystąpienie aplikacji uruchomione w jednym z trzech przykładowych środowisk usługi app service (w tym przypadku drugi z trzech środowisk usługi app service):

![Wyszukiwanie DNS][DNSLookup] 

## <a name="additional-links-and-information"></a>Dodatkowe linki i informacje
Dokumentacja dotycząca pomocy technicznej usługi Powershell [Azure Resource Manager Traffic Manager][ARMTrafficManager].  

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
