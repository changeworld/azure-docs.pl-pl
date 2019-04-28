---
title: Szczegóły konfiguracji sieci dla usługi Azure ExpressRoute — usługa App Service
description: Szczegóły konfiguracji sieci dla środowiska App Service Environment dla usługi PowerApps w sieciach wirtualnych połączonych z obwodem usługi ExpressRoute platformy Azure.
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
ms.custom: seodec18
ms.openlocfilehash: e0fa87facec73efdfff1a9908dcba92838215425
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130674"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>Szczegóły konfiguracji sieci dla środowiska App Service Environment dla usługi PowerApps z usługą Azure ExpressRoute

Klienci mogą się łączyć [usługi Azure ExpressRoute] [ ExpressRoute] obwodu ich infrastruktury sieci wirtualnej, aby rozszerzyć ich w sieci lokalnej na platformę Azure. Środowisko usługi App Service jest tworzony w podsieci [sieci wirtualnej] [ virtualnetwork] infrastruktury. Aplikacje działające w środowisku App Service Environment ustanawiać bezpieczne połączenia z zasobami zaplecza, które są dostępne tylko za pośrednictwem połączenia usługi ExpressRoute.  

Środowisko App Service Environment można utworzyć w następujących scenariuszach:
- Sieci wirtualne platformy Azure Resource Manager.
- Sieci wirtualne modelu wdrożenia klasycznego.
- Sieci wirtualne, które używają zakresów adresów publicznych lub RFC1918 przestrzenie adresowe (czyli prywatnych adresów). 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Połączenie sieciowe wymagane

Środowisko usługi App Service ma wymagań dotyczących łączności sieciowej, które początkowo nie mogą zostać spełnione w sieci wirtualnej, która jest połączona z usługą ExpressRoute.

Środowiska App Service Environment wymaga następujących ustawienia łączności sieciowej do poprawnego działania:

* Połączenia sieciowego ruchu wychodzącego do usługi Azure Storage punktów końcowych na całym świecie na porcie 80 i portu 443. Te punkty końcowe znajdują się w tym samym regionie, co środowisko usługi App Service, a także do innych regionów platformy Azure. Punkty końcowe usługi Azure Storage rozwiązania w ramach następujących domen DNS: table.core.windows.net, blob.core.windows.net, queue.core.windows.net i file.core.windows.net.  

* Połączenia sieciowego ruchu wychodzącego do usługi Azure Files na porcie 445.

* Połączenia sieciowego ruchu wychodzącego do punktów końcowych usługi Azure SQL Database, które znajdują się w tym samym regionie, co środowisko App Service Environment. Punkty końcowe bazy danych SQL rozwiązanie w domenie database.windows.net, co wymaga otwartego dostępu do portów 1433, 11000 11999 i 14000 14999. Aby uzyskać szczegółowe informacje na temat użycia portów SQL Database V12, zobacz [portów wyższych niż 1433 dla platformy ADO.NET 4.5](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

* Połączenia sieciowego ruchu wychodzącego do płaszczyzny zarządzania punkty końcowe platformy Azure (klasycznego modelu wdrażania platformy i punktów końcowych usługi Azure Resource Manager). Łączność z tymi punktami końcowymi obejmuje domen management.core.windows.net i management.azure.com. 

* Połączenia sieciowego ruchu wychodzącego do domen ocsp.msocsp.com mscrl.microsoft.com i crl.microsoft.com. Łączność z tych domen jest potrzebny do obsługi funkcji protokołu SSL.

* Konfiguracja DNS dla sieci wirtualnej musi mieć możliwość rozpoznania wszystkie punkty końcowe i domen wymienionych w tym artykule. Nie można rozpoznać punkty końcowe, tworzenie środowiska App Service Environment nie powiedzie się. Wszelkie istniejące środowisko usługi App Service jest oznaczony jako w złej kondycji.

* Dostęp ruchu wychodzącego przez port 53 jest wymagany do komunikacji przy użyciu serwerów DNS.

* Jeśli niestandardowego serwera DNS znajduje się na drugiej stronie bramy sieci VPN, serwer DNS musi być dostępny z poziomu podsieci zawierającej środowisko App Service Environment. 

* Ścieżka sieciowego ruchu wychodzącego nie może przechodzić przez wewnętrzny firmowych serwerów proxy i nie można wymusić tunelowania w środowisku lokalnym. Te akcje zmienić skuteczne adresów NAT ruchu wychodzącego ruchu sieciowego ze środowiska App Service Environment. Zmiany adresów NAT wychodzącego ruchu sieciowego w środowisku App Service Environment powodować błędy łączności do wielu punktów końcowych. Tworzenie środowiska usługi App Service zakończy się niepowodzeniem. Wszelkie istniejące środowisko usługi App Service jest oznaczony jako w złej kondycji.

* Dostęp do sieci dla ruchu przychodzącego do wymagane porty dla środowiska App Service Environment muszą być dozwolone. Aby uzyskać więcej informacji, zobacz [jak kontrolować ruch przychodzący do środowiska App Service Environment][requiredports].

Aby spełnić wymagania dotyczące usługi DNS, upewnij się, prawidłowy infrastruktura DNS jest konfigurowane i obsługiwane dla sieci wirtualnej. Zmiana konfiguracji DNS po utworzeniu środowiska App Service Environment deweloperów można wymusić środowiska App Service Environment w celu zastosowania nowej konfiguracji DNS. Możesz wyzwolić stopniowe ponowny rozruch środowiska za pomocą **ponowne uruchomienie** ikonę w obszarze zarządzania środowiska App Service Environment w [witryny Azure portal][NewPortal]. Ponowne uruchomienie powoduje, że do środowiska, aby wczytać nowej konfiguracji DNS.

Aby spełnić wymagania dotyczące dostępu do sieci dla ruchu przychodzącego, należy skonfigurować [sieciowej grupy zabezpieczeń (NSG)] [ NetworkSecurityGroups] w podsieci środowiska App Service Environment. Sieciowa grupa zabezpieczeń zezwala na dostęp wymagany [kontrolować ruch przychodzący do środowiska App Service Environment][requiredports].

## <a name="outbound-network-connectivity"></a>Połączenia sieciowego ruchu wychodzącego

Domyślnie nowo utworzony obwód usługi ExpressRoute anonsuje trasę domyślną, umożliwiająca wychodzące połączenie internetowe. Środowisko usługi App Service można użyć tej konfiguracji, połączyć się z innymi punkty końcowe platformy Azure.

Typowa konfiguracja klienta jest definiowanie własnych trasy domyślnej (0.0.0.0/0), która wymusza ruch wychodzący z Internetu do przepływu w środowisku lokalnym. Ten przepływ ruchu przerywa niezmiennie środowiska App Service Environment. Ruch wychodzący jest zablokowane w środowisku lokalnym lub translatora adresów Sieciowych będzie nierozpoznawalną zbiór adresów, które nie będą działać z różnymi punkty końcowe platformy Azure.

To rozwiązanie jest zdefiniować jedną (lub więcej) tras zdefiniowanych przez użytkownika (Udr) dla podsieci zawierającej środowisko App Service Environment. Trasa zdefiniowana przez użytkownika definiuje trasy specyficzne dla podsieci, które są uznawane zamiast trasy domyślnej.

Jeśli to możliwe użyj następującej konfiguracji:

* Konfiguracji usługi ExpressRoute anonsuje 0.0.0.0/0. Domyślnie uruchomionego z wymuszeniem konfiguracji tuneli całego ruchu wychodzącego środowiska lokalnego.
* Trasa zdefiniowana przez użytkownika zastosowane do podsieci zawierającej środowisko App Service Environment definiuje 0.0.0.0/0 z typem następnego przeskoku Internet. Przykładem tę konfigurację opisano w dalszej części tego artykułu.

Połączone ta konfiguracja powoduje, że trasa zdefiniowana przez użytkownika w poziomie w podsieci ma pierwszeństwo przed tunelowania wymuszonego usługi ExpressRoute. Ruch wychodzący do Internetu z środowiska App Service Environment jest gwarantowana.

> [!IMPORTANT]
> Trasy zdefiniowane przez użytkownika muszą być wystarczająco szczegółowe, pierwszeństwo względem wszelkich tras, które są anonsowane przez konfigurację usługi ExpressRoute. W przykładzie, opisane w następnej sekcji użyto szerokiego zakresu adresów 0.0.0.0/0. Ten zakres przypadkowo mogą być zastąpione przez anonsy tras, korzystających z bardziej szczegółowymi zakresami adresów.
> 
> Środowisko usługi App Service nie jest obsługiwana za pomocą konfiguracji usługi ExpressRoute z anonsowaniem krzyżowym tras ze ścieżki publicznej komunikacji równorzędnej do ścieżki prywatnej komunikacji równorzędnej. Konfiguracje usługi ExpressRoute, które mają skonfigurowaną publiczną komunikacją równorzędną otrzymują anonsy tras od firmy Microsoft dla duży zestaw zakresów adresów IP platformy Microsoft Azure. Jeśli te zakresy adresów anonsowania krzyżowego ścieżką prywatnej sieci równorzędnej, wszystkie pakiety sieciowe wychodzące z podsieci środowiska App Service Environment są wymuszony tunelowane do infrastruktury sieci lokalnej klienta. Ten przepływ sieciowy nie jest obecnie obsługiwane za pomocą środowiska App Service Environment. Jednym rozwiązaniem jest zatrzymanie anonsowania krzyżowego tras ze ścieżki publicznej komunikacji równorzędnej do ścieżki prywatnej komunikacji równorzędnej.
> 
> 

Aby uzyskać ogólne informacje o trasach definiowanych przez użytkownika, zobacz [routing ruchu w sieci wirtualnej][UDROverview].  

Aby dowiedzieć się, jak utworzyć i skonfigurować trasy zdefiniowane przez użytkownika, zobacz [kierowanie ruchu sieciowego z tabelą tras przy użyciu programu PowerShell][UDRHowTo].

## <a name="udr-configuration"></a>Konfiguracja trasy zdefiniowanej przez użytkownika

W tej sekcji przedstawiono przykładową konfigurację trasy zdefiniowanej przez użytkownika dla środowiska App Service Environment.

### <a name="prerequisites"></a>Wymagania wstępne

* Instalowanie programu Azure PowerShell z [strony plików do pobrania Azure][AzureDownloads]. Wybierz dostępny do pobrania z datą, czerwiec 2015 lub nowszego. W obszarze **narzędzia wiersza polecenia** > **programu Windows PowerShell**, wybierz opcję **zainstalować** zainstalować najnowsze polecenia cmdlet programu PowerShell.

* Tworzenie unikatowej podsieci do wyłącznego użytku przez usługę App Service Environment. Unikatowej podsieci zapewnia, że stosowane do podsieci Otwórz ruchu wychodzącego dla środowiska App Service Environment tylko tras zdefiniowanych przez użytkownika.

> [!IMPORTANT]
> App Service Environment należy wdrażać tylko po wykonaniu kroków konfiguracji. Czynności upewnij się, że połączenia sieciowego ruchu wychodzącego jest dostępna, przed podjęciem próby wdrożenia środowiska App Service Environment.

### <a name="step-1-create-a-route-table"></a>Krok 1: Tworzenie tabeli tras

Utwórz tabelę tras o nazwie **DirectInternetRouteTable** w regionie zachodnie stany USA Azure, jak pokazano w tym fragmencie kodu:

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>Krok 2: Tworzenie tras w tabeli

Dodawanie tras do tabeli tras, aby umożliwić ruch wychodzący do Internetu.  

Konfigurowanie wychodzącego dostępu do Internetu. Należy zdefiniować trasę dla 0.0.0.0/0, jak pokazano w tym fragmencie kodu:

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

0.0.0.0/0 jest zakresem adresów szerokiego. Zakres jest zastępowany przez anonsowane przez usługę ExpressRoute zakresów adresów, które są bardziej szczegółowe. Trasa zdefiniowana przez użytkownika trasa 0.0.0.0/0 należy używać w połączeniu z konfiguracją usługi ExpressRoute, który anonsuje tylko 0.0.0.0/0. 

Jako alternatywę Pobierz bieżący, wszechstronnymi listę zakresy CIDR używane przez platformę Azure. Plik XML dla wszystkich zakresów adresów IP platformy Azure jest dostępna z [Microsoft Download Center][DownloadCenterAddressRanges].  

> [!NOTE]
>
> Zmień zakresy adresów IP platformy Azure wraz z upływem czasu. Trasy zdefiniowane przez użytkownika muszą okresowo ręczne aktualizacje, aby zachować synchronizację.
>
> Pojedynczy trasy zdefiniowanej przez użytkownika ma domyślne górny limit 100 tras. Musisz "Podsumowanie" zakresy adresów IP platformy Azure, aby mieściły się w limicie limit 100 tras. Trasy zdefiniowane przez użytkownika muszą być bardziej szczegółowe niż trasy, które są anonsowane przez połączenie usługi ExpressRoute.
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>Krok 3: Kojarzenie tabeli do podsieci

Kojarzenie tabeli tras do podsieci, których zamierzasz wdrożyć środowisko App Service Environment. To polecenie powoduje skojarzenie **DirectInternetRouteTable** do tabeli **ASESubnet** podsieci, która będzie zawierać środowiska App Service Environment.

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>Krok 4: Testowanie i upewnij się, trasy

Po tabelę tras jest powiązana z podsiecią, testowanie i upewnij się, trasy.

Wdrażanie maszyny wirtualnej w podsieci i upewnij się, te warunki:

* Ruch wychodzący do platformy Azure i punktów końcowych spoza platformy Azure opisanych w tym artykule jest **nie** zstępują w dół obwód usługi ExpressRoute. Jeśli ruch wychodzący z podsieci jest życie tunelowany w środowisku lokalnym, tworzenie środowiska App Service Environment zawsze kończy się niepowodzeniem.
* Wyszukiwanie DNS dla punktów końcowych, które opisano w tym artykule wszystkie rozwiązania prawidłowo. 

Po ukończeniu czynności konfiguracyjnych i upewnij się, trasy, należy usunąć maszynę wirtualną. Podsieć musi być "puste", po utworzeniu środowiska App Service Environment.

Teraz można przystąpić do wdrażania środowiska App Service Environment!

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć pracę z środowisko usługi App Service w usłudze PowerApps, zobacz [wprowadzenie do środowiska App Service Environment][IntroToAppServiceEnvironment].

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
