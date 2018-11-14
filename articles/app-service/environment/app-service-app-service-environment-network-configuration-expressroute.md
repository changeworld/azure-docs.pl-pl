---
title: Szczegóły konfiguracji sieci do pracy z usługą Express Route
description: Szczegóły konfiguracji sieci do uruchamiania środowisk usługi App Service w sieciach wirtualnych połączonych z obwodem usługi ExpressRoute.
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
ms.openlocfilehash: 7873192e4a66cd2faed5a1a1255377139d33d750
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2018
ms.locfileid: "51616065"
---
# <a name="network-configuration-details-for-app-service-environments-with-expressroute"></a>Szczegóły konfiguracji sieci dla środowisk usługi App Service przy użyciu rozwiązania ExpressRoute
## <a name="overview"></a>Przegląd
Klienci mogą się łączyć [usługi Azure ExpressRoute] [ ExpressRoute] obwód do infrastruktury sieci wirtualnej, w związku z tym rozszerzanie sieci lokalnych na platformę Azure.  Można utworzyć środowisko App Service w podsieci to [sieci wirtualnej] [ virtualnetwork] infrastruktury.  Aplikacje działające w środowisku usługi App Service można następnie ustanawiać bezpieczne połączenia z zasobami zaplecza dostępny tylko za pośrednictwem połączenia usługi ExpressRoute.  

Środowisko usługi App Service mogą być tworzone w **albo** z siecią wirtualną usługi Azure Resource Manager **lub** sieć wirtualną modelu wdrożenia klasycznego.  Z ostatnich zmian wprowadzonych w czerwca 2016 roku za pomocą środowisk ASE teraz również można wdrażać w sieciach wirtualnych, które używają zakresów adresów publicznych lub przestrzeni adresowych RFC1918 (czyli prywatnych adresów). 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Połączenie sieciowe wymagane
Istnieją wymagania łączności sieci dla środowisk App Service Environment nie będzie spełniony początkowo w sieci wirtualnej, która jest połączona z usługą ExpressRoute.  Środowiska usługi App Service wymagane są wszystkie z następujących czynności w celu poprawnego:

* Połączenia sieciowego ruchu wychodzącego do usługi Azure Storage punktów końcowych na całym świecie na oba porty 80 i 443.  Dotyczy to punktów końcowych znajdujących się w tym samym regionie co usługa App Service Environment, jak również punkty końcowe usługi storage znajduje się w **innych** regiony platformy Azure.  Punkty końcowe usługi Azure Storage rozwiązania w ramach następujących domen DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* i *file.core.windows.net*.  
* Połączenia sieciowego ruchu wychodzącego do usługi Azure Files na porcie 445.
* Połączenia sieciowego ruchu wychodzącego do punktów końcowych usługi Sql DB znajduje się w tym samym regionie co usługa App Service Environment.  Punkty końcowe usługi SQL DB rozwiązania w obszarze następującej domeny: *database.windows.net*.  Ta migracja wymaga dostępu do portów 1433, 11000 11999 i 14000 14999 otwierania.  Aby uzyskać więcej informacji, zobacz [ten artykuł dotyczący użycia portów Sql Database V12](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).
* Połączenia sieciowego ruchu wychodzącego do punktów końcowych płaszczyzny zarządzania platformy Azure (ASM i ARM punktów końcowych).  Dotyczy to również łączności wychodzącej do obu *management.core.windows.net* i *management.azure.com*. 
* Połączenia sieciowego ruchu wychodzącego do *ocsp.msocsp.com*, *mscrl.microsoft.com* i *crl.microsoft.com*.  Jest to niezbędne do obsługi funkcji protokołu SSL.
* Konfiguracja DNS dla sieci wirtualnej musi umożliwiać rozpoznawanie wszystkie punkty końcowe i domen wymienionych w starszych punktów.  Jeśli nie można rozpoznać te punkty końcowe, prób tworzenia środowiska App Service Environment zakończy się niepowodzeniem i istniejącego środowiska usługi App Service zostanie oznaczona jako w złej kondycji.
* Dostęp ruchu wychodzącego przez port 53 jest wymagany do komunikacji przy użyciu serwerów DNS.
* Jeśli niestandardowego serwera DNS znajduje się na drugiej stronie bramy sieci VPN, serwer DNS musi być dostępny z poziomu podsieci zawierającej środowisko App Service Environment. 
* Ścieżka sieciowego ruchu wychodzącego nie może przechodzić przez wewnętrzne serwery proxy w firmowych ani nie może być wymuszonego tunelowania do środowiska lokalnego.  Spowoduje to zmianę skuteczne adresów NAT ruchu wychodzącego ruchu sieciowego ze środowiska App Service Environment.  Zmiana adresu translatora adresów Sieciowych wychodzącego ruchu sieciowego w środowisku usługi aplikacji spowoduje, że awarie połączenia do wielu punktów końcowych wymienionych powyżej.  Skutkuje to nieudanych prób tworzenia środowiska App Service Environment, tak jak poprzednio w dobrej kondycji środowisk usługi App Service jest oznaczony jako w złej kondycji.  
* Dostęp sieciowy do wymagane porty dla ruchu przychodzącego dla środowisk usługi App Service, muszą być dozwolone, zgodnie z opisem w tym [artykułu][requiredports].

Wymagania dotyczące usługi DNS mogą zostać spełnione przez zapewnienie im prawidłowe infrastruktura DNS jest konfigurowane i obsługiwane dla sieci wirtualnej.  Jeśli z jakiegokolwiek powodu zmiany konfiguracji DNS po utworzeniu środowiska usługi App Service, deweloperzy wymusić środowiska usługi App Service w celu zastosowania nowej konfiguracji DNS.  Wyzwalanie stopniowe ponowny rozruch środowiska za pomocą ikony "Restart" znajdujący się w górnej części bloku zarządzania środowiska App Service Environment w [witryny Azure portal] [ NewPortal] spowoduje, że środowisko w celu odebrania nowych DNS Konfiguracja.

Wymagania dotyczące dostępu do sieci dla ruchu przychodzącego, mogą zostać spełnione, konfigurując [sieciowej grupy zabezpieczeń] [ NetworkSecurityGroups] w podsieci środowiska App Service Environment, aby zezwolić na dostęp wymagany zgodnie z opisem w tym [ artykuł][requiredports].

## <a name="enabling-outbound-network-connectivity-for-an-app-service-environment"></a>Włączanie połączenia sieciowego ruchu wychodzącego dla środowiska usługi App Service
Domyślnie nowo utworzony obwód usługi ExpressRoute anonsuje trasę domyślną, umożliwiająca wychodzące połączenie internetowe.  W przypadku tej konfiguracji środowiska usługi App Service będzie można połączyć się z innymi punkty końcowe platformy Azure.

Typowa konfiguracja klienta jest jednak zdefiniować własne trasy domyślnej (0.0.0.0/0), co zmusza wychodzącego ruchu internetowego, aby zamiast tego przepływu w środowisku lokalnym.  Ten przepływ ruchu niezmiennie przerywa środowisk usługi App Service, ponieważ ruch wychodzący jest zablokowane w środowisku lokalnym lub translatora adresów Sieciowych będzie nierozpoznawalną zbiór adresów, które nie będą działać z różnymi punkty końcowe platformy Azure.

To rozwiązanie jest zdefiniować jedną (lub więcej) tras zdefiniowanych przez użytkownika (Udr) dla podsieci zawierającej środowisko App Service Environment.  Trasa zdefiniowana przez użytkownika definiuje trasy specyficzne dla podsieci, które będą honorowane zamiast trasy domyślnej.

Jeśli to możliwe zaleca się użyć następującej konfiguracji:

* Konfiguracji usługi ExpressRoute anonsuje 0.0.0.0/0 i domyślnie życie tuneli całego ruchu wychodzącego środowiska lokalnego.
* Trasa zdefiniowana przez użytkownika zastosowane do podsieci zawierającej środowisko App Service Environment definiuje 0.0.0.0/0 z typem następnego przeskoku Internet (na przykład jest dostępne w dół, w tym artykule).

Połączony wpływ tych kroków jest, czy poziomu podsieci trasy zdefiniowanej przez użytkownika mają wyższy priorytet niż ExpressRoute wymuszonego tunelowania, co pozwala na zapewnienie ruch wychodzący do Internetu z usługi App Service Environment.

> [!IMPORTANT]
> Trasy zdefiniowane przez użytkownika **musi** być wystarczająco szczegółowe, pierwszeństwo względem wszelkich tras anonsowanych przez konfigurację usługi ExpressRoute.  W poniższym przykładzie korzysta z szerokiego zakresu adresów 0.0.0.0/0 i jako takie mogą zostać przypadkowo zastąpione przez anonsy tras przy użyciu bardziej szczegółowymi zakresami adresów.
> 
> Środowiska usługi App Service nie są obsługiwane w przypadku konfiguracji usługi ExpressRoute, **między anonsować tras ze ścieżki publicznej komunikacji równorzędnej do ścieżki prywatnej komunikacji równorzędnej**.  Konfiguracje usługi ExpressRoute, które mają skonfigurowaną, publiczną komunikacją równorzędną otrzymają anonsy tras od firmy Microsoft na duży zestaw zakresów adresów IP platformy Microsoft Azure.  Jeśli te zakresy adresów anonsowania krzyżowego ścieżką prywatnej sieci równorzędnej, efekt jest, że wszystkie pakiety sieciowe wychodzące z podsieci środowiska App Service Environment będą sposób wymuszony tunelowany do infrastruktury sieci lokalnej klienta.  Ten przepływ sieciowy nie jest obecnie obsługiwane przy użyciu środowisk usługi App Service.  Jedno rozwiązanie tego problemu jest zatrzymanie anonsowania krzyżowego tras ze ścieżki publicznej komunikacji równorzędnej do ścieżki prywatnej komunikacji równorzędnej.
> 
> 

Uzyskać informacje ogólne dotyczące tras zdefiniowanych przez użytkownika są dostępne w tym [Przegląd][UDROverview].  

Szczegółowe informacje na temat tworzenia i konfigurowania tras zdefiniowanych przez użytkownika są dostępne w tym [jak do przewodnika][UDRHowTo].

## <a name="example-udr-configuration-for-an-app-service-environment"></a>Przykładowa konfiguracja trasy zdefiniowanej przez użytkownika dla środowiska usługi App Service
**Wymagania wstępne**

1. Instalowanie programu Azure Powershell z [strony plików do pobrania Azure] [ AzureDownloads] (z czerwca 2015 lub nowszy).  W obszarze "Narzędzia wiersza polecenia" Brak link "Zainstaluj", w obszarze "Windows Powershell", która będzie instalować najnowsze polecenia cmdlet programu Powershell.
2. Zaleca się, że unikatowej podsieci jest tworzony do wyłącznego użytku przez usługę App Service Environment.  Daje to gwarancję, że tras zdefiniowanych przez użytkownika zastosowane do podsieci będą otwierane tylko ruchu wychodzącego dla środowiska App Service Environment.
3. **Ważne**: nie należy wdrażać usługę App Service Environment aż do **po** zostały wykonane następujące kroki konfiguracji.  Dzięki temu, że połączenia sieciowego ruchu wychodzącego jest dostępny, przed podjęciem próby wdrożenia środowiska usługi App Service.

**Krok 1: Tworzenie tabeli tras o nazwie**

Poniższy fragment kodu tworzy tabelę tras o nazwie "DirectInternetRouteTable" w regionie zachodnie stany USA Azure:

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**Krok 2: Utwórz co najmniej jednej trasy w tabeli tras**

Należy dodać co najmniej jednej trasy w tabeli tras, aby włączyć ruch wychodzący do Internetu.  

Zalecane podejście do Konfigurowanie wychodzącego dostępu do Internetu jest aby zdefiniować trasę dla 0.0.0.0/0, jak pokazano poniżej.

    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

Należy pamiętać, że 0.0.0.0/0 jest zakresem adresów szerokiej i jako takie, zostaną zastąpione przez bardziej szczegółowymi zakresami adresów anonsowane przez usługę ExpressRoute.  Ponownie iteracyjne wcześniejszego zalecenia, routingiem zdefiniowanym przez użytkownika trasa 0.0.0.0/0 należy używać w połączeniu z konfiguracją usługi ExpressRoute, który tylko anonsuje także 0.0.0.0/0. 

Alternatywnie możesz pobrać kompleksowe i zaktualizowaną listę zakresy CIDR używane przez platformę Azure.  Plik Xml zawierający wszystkie zakresy adresów IP platformy Azure jest dostępna z [Microsoft Download Center][DownloadCenterAddressRanges].  

Jednak należy pamiętać, że te zakresy zmienić wraz z upływem czasu, co wymaga okresowe ręczne aktualizacje, aby trasy zdefiniowane przez użytkownika, aby zachować synchronizację.  Ponadto ponieważ domyślne górny limit 100 tras w pojedynczej trasy zdefiniowanej przez użytkownika, konieczne będzie "Podsumowanie" zakresy adresów IP platformy Azure, aby mieściły się w limicie limit 100 tras pamiętając o tym, że trasa zdefiniowana przez użytkownika zdefiniowane tras muszą być bardziej szczegółowe niż trasy anonsowane przez Twoje ExpressRo Wykonaj.  

**Krok 3: Skojarz tabelę tras do podsieci zawierającej środowisko App Service Environment**

Ostatnim krokiem konfiguracji jest kojarzenie tabeli tras do podsieci wdrożonym środowiska App Service Environment.  Następujące polecenie powoduje skojarzenie "DirectInternetRouteTable" do "ASESubnet", który ostatecznie będzie zawierać środowiska usługi App Service.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Krok 4: Ostatnie kroki**

Gdy tabelę tras jest powiązana z podsiecią, zaleca się najpierw testów i upewnij się, zamierzony efekt.  Na przykład wdrożyć maszynę wirtualną w tej podsieci i upewnij się, że:

* Ruch wychodzący do platformy Azure i spoza platformy Azure punkty końcowe wymienione wcześniej w ten artykuł stanowi **nie** przepływają w dół obwód usługi ExpressRoute.  Jest to bardzo ważne, aby sprawdzić to zachowanie, ponieważ jeśli ruch wychodzący z podsieci jest nadal wymuszone tunelowanie lokalnie, App Service Environment w przypadku tworzenia zawsze zakończy się niepowodzeniem. 
* Wyszukiwanie DNS dla punktów końcowych, które wspomniano wcześniej, wszystkie rozwiązuje prawidłowo. 

Po potwierdzeniu są powyższe kroki, należy usunąć maszynę wirtualną, ponieważ podsieć musi być "puste" w czasie tworzenia środowiska App Service Environment.

Następnie kontynuuj tworzenie środowiska usługi App Service!

## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć pracę przy użyciu środowisk usługi App Service, zobacz [wprowadzenie do środowiska App Service Environment][IntroToAppServiceEnvironment]

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
