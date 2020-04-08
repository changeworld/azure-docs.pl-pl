---
title: Konfigurowanie usługi Azure ExpressRoute w wersji 1
description: Konfiguracja sieci dla środowiska usługi App Service dla usługi PowerApps za pomocą usługi Azure ExpressRoute. Ten doc jest dostępna tylko dla klientów, którzy używają starszej wersji ASE w wersji 1.
author: stefsch
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: fc11c6932d625b119ad933f5d4d128b4355530c5
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804439"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>Szczegóły konfiguracji sieci dla środowiska usługi App Service dla usługi PowerApps za pomocą usługi Azure ExpressRoute

Klienci mogą połączyć obwód [usługi Azure ExpressRoute][ExpressRoute] z infrastrukturą sieci wirtualnej, aby rozszerzyć swoją sieć lokalną na platformę Azure. Środowisko usługi aplikacji jest tworzony w podsieci infrastruktury [sieci wirtualnej.][virtualnetwork] Aplikacje uruchamiane w środowisku usługi app service ustanawiają bezpieczne połączenia z zasobami zaplecza, które są dostępne tylko za pośrednictwem połączenia usługi ExpressRoute.  

Środowisko usługi app service można utworzyć w następujących scenariuszach:
- Sieci wirtualne usługi Azure Resource Manager.
- Sieci wirtualne modelu wdrażania klasycznego.
- Sieci wirtualne korzystające z zakresów adresów publicznych lub przestrzeni adresowych RFC1918 (czyli adresów prywatnych). 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Wymagana łączność sieciowa

Środowisko usługi aplikacji ma wymagania dotyczące łączności sieciowej, które początkowo mogą nie być spełnione w sieci wirtualnej, która jest połączona z usługą ExpressRoute.

Środowisko usługi app service wymaga następujących ustawień łączności sieciowej, aby działać poprawnie:

* Wychodząca łączność sieciowa z punktami końcowymi usługi Azure Storage na całym świecie na porcie 80 i porcie 443. Te punkty końcowe znajdują się w tym samym regionie co środowisko usługi app service, a także w innych regionach platformy Azure. Punkty końcowe usługi Azure Storage są rozpoznawane w następujących domenach DNS: table.core.windows.net, blob.core.windows.net, queue.core.windows.net i file.core.windows.net.  

* Wychodząca łączność sieciowa z usługą Azure Files na porcie 445.

* Wychodząca łączność sieciowa z punktami końcowymi usługi Azure SQL Database, które znajdują się w tym samym regionie co środowisko usługi app service. Punkty końcowe bazy danych SQL są rozpoznawane w domenie database.windows.net, która wymaga otwartego dostępu do portów 1433, 11000-11999 i 14000-14999. Aby uzyskać szczegółowe informacje na temat użycia portu bazy danych SQL V12, zobacz [Porty poza 1433 dla ADO.NET 4.5](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

* Wychodząca łączność sieciowa z punktami końcowymi płaszczyzny zarządzania platformy Azure (zarówno klasyczny model wdrażania platformy Azure, jak i punkty końcowe usługi Azure Resource Manager). Łączność z tymi punktami końcowymi obejmuje domeny management.core.windows.net i management.azure.com. 

* Wychodząca łączność sieciowa z domenami ocsp.msocsp.com, mscrl.microsoft.com i crl.microsoft.com. Łączność z tymi domenami jest potrzebna do obsługi funkcji TLS.

* Konfiguracja DNS dla sieci wirtualnej musi być w stanie rozpoznać wszystkie punkty końcowe i domeny wymienione w tym artykule. Jeśli nie można rozpoznać punktów końcowych, tworzenie środowiska usługi app service kończy się niepowodzeniem. Wszystkie istniejące środowisko usługi app service jest oznaczony jako w złej kondycji.

* Dostęp wychodzący na porcie 53 jest wymagany do komunikacji z serwerami DNS.

* Jeśli niestandardowy serwer DNS istnieje na drugim końcu bramy sieci VPN, serwer DNS musi być osiągalny z podsieci zawierającej środowisko usługi app service. 

* Wychodząca ścieżka sieci nie może przechodzić przez wewnętrzne serwery proxy firmy i nie można wymusić tunelowania lokalnie. Te akcje zmieniają efektywny adres NAT wychodzącego ruchu sieciowego ze środowiska usługi App Service. Zmiany adresu NAT ruchu wychodzącego sieci usługi App Service powodują błędy łączności z wieloma punktami końcowymi. Tworzenie środowiska usługi app service kończy się niepowodzeniem. Wszystkie istniejące środowisko usługi app service jest oznaczony jako w złej kondycji.

* Dostęp do sieci przychodzącej do wymaganych portów dla środowiska usługi app service musi być dozwolony. Aby uzyskać szczegółowe informacje, zobacz [Jak kontrolować ruch przychodzący do środowiska usługi App Service][requiredports].

Aby spełnić wymagania systemu DNS, upewnij się, że prawidłowa infrastruktura DNS jest skonfigurowana i obsługiwana dla sieci wirtualnej. Jeśli konfiguracja DNS zostanie zmieniona po utworzeniu środowiska usługi App Service, deweloperzy mogą wymusić na środowisku usługi App Service pobranie nowej konfiguracji DNS. Ponowne uruchomienie środowiska stopniowego można wyzwolić przy użyciu ikony **Uruchom ponownie** w obszarze Zarządzanie środowiskiem usługi app service w [witrynie Azure portal][NewPortal]. Ponowne uruchomienie powoduje, że środowisko odebrać nową konfigurację DNS.

Aby spełnić wymagania dotyczące dostępu do sieci przychodzącej, skonfiguruj [sieciową grupę zabezpieczeń (NSG)][NetworkSecurityGroups] w podsieci Środowisko usługi app service. Grupa NSG umożliwia wymagany dostęp [do kontroli ruchu przychodzącego do środowiska usługi App Service.][requiredports]

## <a name="outbound-network-connectivity"></a>Wychodząca łączność sieciowa

Domyślnie nowo utworzony obwód usługi ExpressRoute anonsuje domyślną trasę, która umożliwia wychodzące połączenie z Internetem. Środowisko usługi app service można użyć tej konfiguracji, aby połączyć się z innymi punktami końcowymi platformy Azure.

Wspólną konfiguracją klienta jest zdefiniowanie własnej trasy domyślnej (0.0.0.0/0), która wymusza przepływ wychodzącego ruchu internetowego lokalnie. Ten przepływ ruchu niezmiennie przerywa środowisko usługi app service. Ruch wychodzący jest zablokowany lokalnie lub NAT'd do nierozpoznawalnego zestawu adresów, które nie działają już z różnymi punktami końcowymi platformy Azure.

Rozwiązaniem jest zdefiniowanie jednej (lub więcej) tras zdefiniowanych przez użytkownika (UDR) w podsieci zawierającej środowisko usługi app service. UDR definiuje trasy specyficzne dla podsieci, które są honorowane zamiast trasy domyślnej.

Jeśli to możliwe, należy użyć następującej konfiguracji:

* Konfiguracja usługi ExpressRoute anonsuje 0.0.0.0/0. Domyślnie wymusza konfiguracja tunele całego ruchu wychodzącego lokalnie.
* UDR zastosowane do podsieci, która zawiera środowisko usługi app service definiuje 0.0.0.0/0 z następnym typem przeskoku internetu. Przykład tej konfiguracji jest opisany w dalszej części tego artykułu.

Połączony efekt tej konfiguracji jest, że poziom podsieci UDR ma pierwszeństwo przed tunelowania sił expressroute. Transgraniczny dostęp do Internetu ze środowiska usługi App Service jest gwarantowany.

> [!IMPORTANT]
> Trasy zdefiniowane w UDR musi być wystarczająco szczegółowe, aby mieć pierwszeństwo przed wszystkie trasy, które są anonsowane przez konfigurację usługi ExpressRoute. W przykładzie opisanym w następnej sekcji użyto szerokiego zakresu adresów 0.0.0.0/0. Ten zakres może zostać przypadkowo zastąpiony przez reklamy trasy, które używają bardziej szczegółowych zakresów adresów.
> 
> Środowisko usługi app service nie jest obsługiwane w konfiguracjach usługi ExpressRoute, które współadmuchują trasy ze ścieżki komunikacji równorzędnej do prywatnej ścieżki równorzędnej. Konfiguracje usługi ExpressRoute, które mają skonfigurowaną komunikację równorzędną publiczną, odbierają anonse tras od firmy Microsoft dla dużego zestawu zakresów adresów IP platformy Microsoft Azure. Jeśli te zakresy adresów są anonsowane krzyżowo na prywatnej ścieżce równorzędnej, wszystkie wychodzące pakiety sieciowe z podsieci Środowisko usługi aplikacji są wymuszane na tunelowaniu do lokalnej infrastruktury sieciowej klienta. Ten przepływ sieciowy nie jest obecnie obsługiwany w środowisku usługi app service. Jednym z rozwiązań jest zatrzymanie tras cross-advertising z publicznej ścieżki równorzędnej do prywatnej ścieżki komunikacji równorzędnej.
> 
> 

Aby uzyskać podstawowe informacje o trasach zdefiniowanych przez użytkownika, zobacz [Routing ruchu sieci wirtualnej][UDROverview].  

Aby dowiedzieć się, jak tworzyć i konfigurować trasy zdefiniowane przez użytkownika, zobacz [Kierowanie ruchu sieciowego za pomocą tabeli tras przy użyciu programu PowerShell][UDRHowTo].

## <a name="udr-configuration"></a>Konfiguracja UDR

W tej sekcji przedstawiono przykładową konfigurację UDR dla środowiska usługi app service.

### <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj program Azure PowerShell ze [strony Pobieranie platformy Azure][AzureDownloads]. Wybierz pobieranie z datą czerwca 2015 r. lub nowszą. W obszarze **Narzędzia** > wiersza polecenia systemu Windows**PowerShell**wybierz pozycję **Zainstaluj,** aby zainstalować najnowsze polecenia cmdlet programu PowerShell.

* Utwórz unikatową podsieć do wyłącznego użytku przez środowisko usługi App Service. Unikatowa podsieć zapewnia, że UDR zastosowane do podsieci otwierają ruch wychodzący tylko dla środowiska usługi app service.

> [!IMPORTANT]
> Środowisko usługi app service należy wdrożyć dopiero po wykonaniu czynności konfiguracyjnych. Kroki upewnij się, że wychodząca łączność sieciowa jest dostępna przed podjęciem próby wdrożenia środowiska usługi app service.

### <a name="step-1-create-a-route-table"></a>Krok 1: Tworzenie tabeli tras

Utwórz tabelę marszruty o nazwie **DirectInternetRouteTable** w regionie Zachodnia platforma Azure dla stanów Zjednoczonych, jak pokazano w tym urywek:

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>Krok 2: Tworzenie tras w tabeli

Dodaj trasy do tabeli tras, aby włączyć wychodzący dostęp do Internetu.  

Skonfiguruj dostęp wychodzący do Internetu. Zdefiniuj trasę dla 0.0.0.0/0, jak pokazano w tym urywek:

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

0.0.0.0/0 to szeroki zakres adresów. Zakres jest zastępowany przez zakresy adresów anonsowane przez program ExpressRoute, które są bardziej szczegółowe. UDR z marszem 0.0.0.0/0 trasy powinny być używane w połączeniu z konfiguracją usługi ExpressRoute, która anonsuje tylko 0.0.0.0/0. 

Alternatywnie pobierz bieżącą, kompleksową listę zakresów CIDR używanych przez platformę Azure. Plik XML dla wszystkich zakresów adresów IP platformy Azure jest dostępny w [Centrum pobierania Microsoft][DownloadCenterAddressRanges].  

> [!NOTE]
>
> Zakresy adresów IP platformy Azure zmieniają się wraz z czasem. Trasy zdefiniowane przez użytkownika wymagają okresowych aktualizacji ręcznych, aby zachować synchronizację.
>
> Pojedynczy UDR ma domyślny górny limit 100 tras. Musisz "podsumować" zakresy adresów IP platformy Azure, aby zmieścić się w limicie 100 tras. Trasy zdefiniowane przez UDR muszą być bardziej szczegółowe niż trasy, które są anonsowane przez połączenie usługi ExpressRoute.
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>Krok 3: Skojarzyć tabelę z podsiecią

Skojarz tabelę marszruty z podsiecią, w której zamierzasz wdrożyć środowisko usługi app service. To polecenie kojarzy tabelę **DirectInternetRouteTable** z podsiecią **ASESubnet,** która będzie zawierać środowisko usługi app service.

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>Krok 4: Przetestuj i potwierdź trasę

Po tabeli trasy jest powiązany z podsieci, przetestować i potwierdzić trasę.

Wdrażanie maszyny wirtualnej w podsieci i potwierdzanie następujących warunków:

* Ruch wychodzący do punktu końcowego platformy Azure i punktów końcowych innych niż Azure opisane w tym artykule **nie** przepływa w dół obwodu usługi ExpressRoute. Jeśli ruch wychodzący z podsieci jest wymuszany tunelowanie w środowisku lokalnym, tworzenie środowiska usługi app service zawsze kończy się niepowodzeniem.
* Wyszukiwania DNS dla punktów końcowych opisanych w tym artykule wszystkie rozwiązać poprawnie. 

Po wykonaniu kroków konfiguracji i potwierdzenie trasy usuń maszynę wirtualną. Podsieć musi być "pusta" podczas tworzenia środowiska usługi app service.

Teraz możesz przystąpić do wdrażania środowiska usługi app service!

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę ze środowiskiem usługi App Service dla usługi PowerApps, zobacz [Wprowadzenie do środowiska usługi app service][IntroToAppServiceEnvironment].

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/ 
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/ 
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md 
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ 
[UDROverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/ 
<!-- Old link -- [UDRHowTo]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/ -->

[UDRHowTo]: https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell 
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md 
[AzureDownloads]: https://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: https://www.microsoft.com/download/details.aspx?id=41653 
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ 
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md 
[NewPortal]:  https://portal.azure.com 


<!-- IMAGES -->
