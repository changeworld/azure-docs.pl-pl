---
title: Praca z Express Route szczegóły konfiguracji sieci
description: Szczegóły konfiguracji sieci do uruchamiania środowiska usługi App Service w sieciach wirtualnych połączone z obwodem usługi ExpressRoute.
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.openlocfilehash: fcb9fa9004039205fa49f63c50d5907a8029a079
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="network-configuration-details-for-app-service-environments-with-expressroute"></a>Szczegóły konfiguracji sieci dla środowisk usługi App Service przy użyciu rozwiązania ExpressRoute
## <a name="overview"></a>Przegląd
Klienci mogą się łączyć [Azure ExpressRoute] [ ExpressRoute] obwód do infrastruktury sieci wirtualnej, a więc rozszerzenia sieci lokalnej na platformie Azure.  Środowiska usługi aplikacji mogą być tworzone w podsieci tego [sieci wirtualnej] [ virtualnetwork] infrastruktury.  Aplikacje działające w środowisku usługi aplikacji może następnie ustanowić bezpiecznego połączenia z zasobami zaplecza dostępne tylko za pośrednictwem połączenia ExpressRoute.  

Środowiska usługi aplikacji mogą być tworzone w **albo** sieci wirtualnej platformy Azure Resource Manager **lub** sieci wirtualnej wdrożenia klasycznego modelu.  Z ostatnie zmiany wprowadzone w czerwca 2016 ASEs również teraz można wdrożyć w sieci wirtualnych, które używają zakresy publicznych adresów lub RFC1918 przestrzeni adresów (czyli adresy prywatne). 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Wymagana jest łączność
Istnieją wymagania dotyczące łączności sieciowej środowiska usługi aplikacji, który nie będzie początkowo spełniony w sieci wirtualnej podłączone do usługi ExpressRoute.  Środowiska usługi aplikacji wymagają następujących prawidłowego działania:

* Połączenie sieciowe wychodzące z usługi Azure Storage punkty końcowe na całym świecie oba porty 80 i 443.  Obejmuje to punkty końcowe znajduje się w tym samym regionie co środowiska usługi aplikacji, a także punkty końcowe usługi storage znajduje się w **innych** regiony platformy Azure.  Punkty końcowe usługi Azure Storage rozwiązania w obszarze następujące domeny DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* i *file.core.windows.net*.  
* Wychodzące łączność z usługą Azure pliki na port 445.
* Połączenie sieciowe ruchu wychodzącego punkty końcowe bazy danych Sql znajduje się w tym samym regionie co środowiska usługi aplikacji.  Rozwiąż punkty końcowe bazy danych SQL w obszarze następującej domeny: *database.windows.net*.  Wymaga to dostępu do portów 1433, 11000 11999 i 14000 14999 otwierania.  Więcej informacji można znaleźć [w tym artykule na użycie portu bazy danych Sql V12](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).
* Połączenie sieciowe ruchu wychodzącego punkty końcowe płaszczyzny zarządzania platformy Azure (punkty końcowe zarówno ASM i ARM).  Dotyczy to zarówno łączność wychodząca *management.core.windows.net* i *management.azure.com*. 
* Połączenie sieciowe ruchu wychodzącego *ocsp.msocsp.com*, *mscrl.microsoft.com* i *crl.microsoft.com*.  Jest to niezbędne do obsługi funkcji protokołu SSL.
* Konfiguracja DNS dla sieci wirtualnej musi umożliwiać rozpoznawania wszystkich punktów końcowych i domen wspomnianego z wcześniejszych punktów.  Jeśli te punkty końcowe nie powiedzie się, próby tworzenia środowiska usługi aplikacji zakończą się niepowodzeniem i istniejącego środowiska usługi App Service zostanie oznaczona jako w złej kondycji.
* Wychodzący dostęp przez port 53 jest wymagana do komunikacji przy użyciu serwerów DNS.
* Jeśli istnieje niestandardowy serwer DNS na drugim końcu bramy sieci VPN, serwer DNS musi być dostępny w podsieci zawierający środowiska usługi aplikacji. 
* Ścieżka sieciowa ruchu wychodzącego nie może przechodzić przez wewnętrzny firmowych serwerów proxy ani może być życie tunel do lokalnego.  Spowoduje to zmianę obowiązujący adres NAT ruchu wychodzącego ruchu sieciowego ze środowiska usługi aplikacji.  Zmiana adresu NAT wychodzącego ruchu sieciowego w środowisku usługi aplikacji spowoduje połączeniami do wielu punktów końcowych wymienionych powyżej.  Powoduje to nieudanych prób tworzenia środowiska usługi aplikacji, a także wcześniej dobrej kondycji środowiska usługi App Service jest oznaczona jako w złej kondycji.  
* Przychodzące dostępu do sieci, aby wymagane porty dla środowiska usługi App Service musi być dozwolone, zgodnie z opisem w tym [artykułu][requiredports].

W celu zapewnienia prawidłowy infrastruktura DNS jest skonfigurowany i dla sieci wirtualnej można spełnić wymagania dotyczące usługi DNS.  Jeśli z jakiegokolwiek powodu konfiguracji DNS zostaną zmienione po utworzeniu środowiska usługi aplikacji, deweloperzy można wymusić środowiska usługi aplikacji do pobrania dla nowej konfiguracji DNS.  Wyzwolenie stopniowego ponowny rozruch środowiska przy użyciu ikony "Restart" znajduje się w górnej części bloku zarządzania środowiska usługi aplikacji w [portalu Azure] [ NewPortal] spowoduje, że środowisko do pobrania dla nowej konfiguracji DNS.

Wymagania dotyczące dostępu do sieci dla ruchu przychodzącego można spełnić przez skonfigurowanie [sieciowej grupy zabezpieczeń] [ NetworkSecurityGroups] podsieci środowiska usługi aplikacji umożliwiają wymagane uprawnienia dostępu, zgodnie z opisem w tym [ artykuł][requiredports].

## <a name="enabling-outbound-network-connectivity-for-an-app-service-environment"></a>Włączenie łączności sieciowej ruchu wychodzącego dla środowiska usługi aplikacji
Domyślnie nowo utworzony obwodu ExpressRoute anonsuje trasę domyślną, który umożliwia wychodzące połączenie z Internetem.  W tej konfiguracji środowiska usługi aplikacji będzie można nawiązać połączenia z innymi punkty końcowe systemu Azure.

Jednak typowych konfiguracji klienta jest określenie własnych trasa domyślna (0.0.0.0/0), co zmusza wychodzący ruch internetowy, zamiast niego przepływ lokalnymi.  Ten przepływ ruchu niezmiennie dzieli środowiska usługi App Service, ruch wychodzący zablokowanych lokalnie, ponieważ NAT czy nierozpoznawalną zbiór adresów, które nie będą działać z różnymi punkty końcowe systemu Azure.

Rozwiązanie jest określenie jednego (lub więcej) trasy zdefiniowane przez użytkownika (Udr) na podsieci, która zawiera środowiska usługi aplikacji.  PRZEZ definiuje tras specyficzne dla podsieci, które będą honorowane zamiast trasy domyślnej.

Jeśli to możliwe zaleca się następującej konfiguracji:

* Konfiguracji usługi ExpressRoute anonsuje 0.0.0.0/0 i domyślnie życie tuneli wszystkich ruch wychodzący lokalnymi.
* PRZEZ stosowana do podsieci, zawierający środowiska usługi aplikacji definiuje 0.0.0.0/0 z typem następnego przeskoku Internet (na przykład jest dostępne w dół w tym artykule).

Łączna tych kroków powoduje, że poziomie podsieci przez mają wyższy priorytet niż ExpressRoute, wymuszone tunelowanie, w związku z tym zapewnienie wychodzący dostęp do Internetu z poziomu środowiska usługi aplikacji.

> [!IMPORTANT]
> Trasy zdefiniowane w przez **musi** jest wystarczająco konkretny, aby mają pierwszeństwo względem dowolnego trasy anonsowane przez konfiguracji usługi ExpressRoute.  W poniższym przykładzie używa 0.0.0.0/0 szeroki zakres adresów i jako taki może potencjalnie być przypadkowo przesłonięta przez anonsów tras za pomocą bardziej szczegółowych zakresów adresów.
> 
> Środowiska usługi aplikacji nie są obsługiwane w przypadku konfiguracji usługi ExpressRoute który **między anonsować tras z publicznej komunikacji równorzędnej ścieżki do ścieżki prywatnej komunikacji równorzędnej**.  Konfiguracji usługi ExpressRoute, które mają publicznej komunikacji równorzędnej skonfigurowane, otrzyma anonsów tras firmy Microsoft dla dużych zestawów zakresów adresów IP firmy Microsoft Azure.  W przypadku tych zakresów adresów między anonsowany w ścieżce prywatnej komunikacji równorzędnej, wynik końcowy jest, że wszystkie pakiety wychodzącego z podsieci środowiska usługi aplikacji zostaną tunneled życie do infrastruktury sieci lokalnej klienta.  Ten przepływ sieci nie jest obecnie obsługiwane w środowiskach usługi aplikacji.  Jedno rozwiązanie tego problemu jest zatrzymanie tras między reklam z publicznej komunikacji równorzędnej ścieżki do ścieżki prywatnej komunikacji równorzędnej.
> 
> 

Informacje na trasy zdefiniowane przez użytkownika jest dostępna w tym [omówienie][UDROverview].  

Szczegółowe informacje na temat tworzenia i konfigurowania trasy zdefiniowane przez użytkownika jest dostępna w tym [jak do przewodnika][UDRHowTo].

## <a name="example-udr-configuration-for-an-app-service-environment"></a>Przykładowa konfiguracja przez środowisko usługi aplikacji
**Wymagania wstępne**

1. Instalowanie programu Azure Powershell z [Azure pobiera strony] [ AzureDownloads] (czerwiec 2015 lub nowszego z).  W obszarze "Narzędzia wiersza polecenia" Brak łącza "Zainstaluj", w obszarze "Windows Powershell", która będzie instalować najnowsze poleceń cmdlet programu Powershell.
2. Zaleca się, że unikatowej podsieci został utworzony do wyłącznego użytku przez środowisko usługi aplikacji.  Dzięki temu, że Udr stosowane do podsieci będą otwierane tylko ruchu wychodzącego dla środowiska usługi aplikacji.
3. **Ważne**: nie należy wdrażać środowiska usługi aplikacji do **po** zostaną wykonane następujące kroki konfiguracji.  Dzięki temu, że połączenie sieciowe ruchu wychodzącego jest dostępny przed próbą wdrożenia środowiska usługi aplikacji.

**Krok 1: Tworzenie tabeli tras o nazwie**

Poniższy fragment kodu tworzy tabelę tras o nazwie "DirectInternetRouteTable" regionu zachodnie nam Azure:

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**Krok 2: Utworzenie co najmniej jednej trasy w tabeli tras**

Należy dodać co najmniej jednej trasy do tabeli routingu w celu umożliwienia wychodzący dostęp do Internetu.  

Zalecane podejście do konfigurowania wychodzący dostęp do Internetu jest do definiowania trasy dla 0.0.0.0/0, jak pokazano poniżej.

    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

Należy pamiętać, tym 0.0.0.0/0 jest zakres adresów szerokie i jako takie zostaną zastąpione przez bardziej szczegółowych zakresów adresów anonsowane przez usługi ExpressRoute.  Ponownie iteracyjne wcześniejszego zalecenia, przez trasa 0.0.0.0/0 używanego w połączeniu z konfiguracją ExressRoute, która tylko anonsuje także 0.0.0.0/0. 

Alternatywnie można pobrać listę zakresy CIDR używanymi przez usługę Azure kompleksowe i zaktualizowane.  Plik Xml zawierający wszystkie zakresy adresów IP platformy Azure jest dostępna z [Microsoft Download Center][DownloadCenterAddressRanges].  

Należy jednak zauważyć, że te zakresy ulegną zmianom, co wymaga okresowych ręczne aktualizacje trasy zdefiniowane przez użytkownika, aby zapewnić synchronizację.  Ponadto ponieważ domyślne górny limit 100 trasy w jednym przez, konieczne będzie "Podsumuj" zakresów adresów IP platformy Azure do mieści się w limicie 100 trasy pamiętając, że przez zdefiniowanych tras muszą być bardziej szczegółowy niż trasy anonsowane przez ExpressRo Twojego ute.  

**Krok 3: Skojarz tabeli tras z podsiecią zawierający środowiska usługi aplikacji**

Ostatnim krokiem konfiguracji jest skojarzyć tabeli tras z podsiecią wdrożonym środowiska usługi aplikacji.  Polecenie kojarzy "DirectInternetRouteTable" do "ASESubnet", który będzie zawierać ostatecznie środowiska usługi aplikacji.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Krok 4: Końcowe czynności**

Gdy tabelę tras jest powiązany z podsiecią, zaleca się najpierw przetestować i Potwierdź zamierzonego efektu.  Na przykład wdrożyć maszynę wirtualną na podsieci i upewnij się, że:

* Ruch wychodzący do platformy Azure i innych niż Azure punkty końcowe wcześniej w wymienionych w tym artykule jest **nie** przepływu dół obwodu usługi expressroute.  Jest bardzo ważne, aby sprawdzić to zachowanie, ponieważ w przypadku ruchu wychodzącego z podsieci nadal wymuszone tunelowane lokalnymi, zawsze kończy się niepowodzeniem tworzenie środowiska usługi aplikacji. 
* Wyszukiwania DNS dla punktów końcowych wspomniano wcześniej, wszystkie rozwiązuje poprawnie. 

Po potwierdzeniu są powyższe kroki, należy usunąć maszynę wirtualną, ponieważ podsieć musi być "pusty" w czasie tworzenia środowiska usługi aplikacji.

Następnie kontynuuj tworzenie środowiska usługi aplikacji!

## <a name="getting-started"></a>Wprowadzenie
Wprowadzenie do środowiska usługi App Service, zobacz [wprowadzenie do środowiska usługi aplikacji][IntroToAppServiceEnvironment]

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[AzureDownloads]: http://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653  
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[NewPortal]:  https://portal.azure.com


<!-- IMAGES -->
