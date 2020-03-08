---
title: Integrowanie aplikacji z usługą Azure Virtual Network
description: Integruj aplikacje w Azure App Service z sieciami wirtualnymi platformy Azure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89aa78e0d26598eacf436ca88cc6c5549f91d2fc
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673227"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrowanie aplikacji z usługą Azure Virtual Network
W tym dokumencie opisano funkcję integracji Azure App Service sieci wirtualnej i sposób jej konfigurowania z aplikacjami w [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). [Usługa Azure Virtual Networks][VNETOverview] (sieci wirtualnych) umożliwia umieszczanie wielu zasobów platformy Azure w sieci bez obsługi Internetu.  

Azure App Service ma dwie odmiany.

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Włącz integrację sieci wirtualnej 

1. Przejdź do interfejsu użytkownika sieci w portalu App Service. W obszarze integracja z siecią wirtualną wybierz pozycję *"kliknij tutaj, aby skonfigurować"* . 

1. Wybierz pozycję **Dodaj sieć wirtualną**.  

   ![Wybierz integrację sieci wirtualnej][1]

1. Lista rozwijana będzie zawierać wszystkie Menedżer zasobów sieci wirtualnych w ramach subskrypcji w tym samym regionie i poniżej, który jest listą wszystkich Menedżer zasobów sieci wirtualnych we wszystkich innych regionach. Wybierz sieć wirtualną, z którą chcesz przeprowadzić integrację.

   ![Wybierz sieć wirtualną][2]

   * Jeśli sieć wirtualna znajduje się w tym samym regionie, Utwórz nową podsieć lub Wybierz pustą istniejącą podsieć. 

   * Aby można było wybrać sieć wirtualną w innym regionie, trzeba mieć zainicjowaną bramę Virtual Network z włączoną opcją wskaż lokację.

   * Aby zintegrować z klasyczną siecią wirtualną, zamiast klikać listę rozwijaną sieci wirtualnej, wybierz **pozycję kliknij tutaj, aby nawiązać połączenie z klasyczną siecią wirtualną**. Wybierz żądaną klasyczną sieć wirtualną. Docelowa sieć wirtualna musi mieć już zainicjowaną bramę Virtual Network z włączoną opcją wskaż lokację.

    ![Wybieranie klasycznej sieci wirtualnej][3]
    
W trakcie integracji aplikacja zostanie ponownie uruchomiona.  Po zakończeniu integracji zobaczysz szczegóły dotyczące sieci wirtualnej, z którą jest zintegrowana. 

Gdy aplikacja jest zintegrowana z siecią wirtualną, będzie używać tego samego serwera DNS, z którym jest skonfigurowana Sieć wirtualna, chyba że jest Azure DNS Private Zones. Obecnie nie można używać integracji sieci wirtualnej z usługą Azure DNS Private Zones.

## <a name="regional-vnet-integration"></a>Integracja z regionalną siecią wirtualną

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Sposób działania integracji regionalnej sieci wirtualnej

Aplikacje w App Service są hostowane na rolach procesów roboczych. Plany cenowe w warstwach Podstawowa i wyższa są dedykowanymi planami hostingu, w których nie ma żadnych innych obciążeń klientów działających w ramach tych samych procesów roboczych. Integracja regionalnej sieci wirtualnej działa przez zainstalowanie interfejsów wirtualnych z adresami w podsieci delegowanej. Ponieważ adres od jest w sieci wirtualnej, może uzyskać dostęp do większości rzeczy w lub za pośrednictwem sieci wirtualnej, tak jak w przypadku maszyny wirtualnej w sieci wirtualnej. Implementacja sieci jest inna niż w przypadku uruchamiania maszyny wirtualnej w sieci wirtualnej, co oznacza, że niektóre funkcje sieciowe nie są jeszcze dostępne podczas korzystania z tej funkcji.

![Sposób działania integracji regionalnej sieci wirtualnej][5]

Po włączeniu zintegrowanej integracji sieci wirtualnej aplikacja nadal będzie wyłączać połączenia wychodzące do Internetu za pomocą tych samych kanałów co normalny. Adresy wychodzące, które są wyświetlane w portalu właściwości aplikacji, nadal są adresami używanymi przez aplikację. Jakie zmiany w aplikacji to, wywołania do usług zabezpieczonych przez punkt końcowy usługi lub adresy RFC 1918 są umieszczane w sieci wirtualnej. Jeśli WEBSITE_VNET_ROUTE_ALL jest ustawiona na 1, cały ruch wychodzący może być wysyłany do sieci wirtualnej. 

Funkcja obsługuje tylko jeden interfejs wirtualny na proces roboczy.  Jeden wirtualny interfejs na proces roboczy oznacza jedną regionalną integrację sieci wirtualnej na App Service plan. Wszystkie aplikacje w tym samym planie App Service mogą korzystać z tej samej integracji sieci wirtualnej, ale jeśli potrzebna jest aplikacja do łączenia się z dodatkową siecią wirtualną, konieczne będzie utworzenie kolejnego planu App Service. Używany interfejs wirtualny nie jest zasobem, do którego klienci mają bezpośredni dostęp.

Ze względu na to, jak działa ta technologia, ruch używany z integracją sieci wirtualnej nie jest wyświetlany w dziennikach przepływów Network Watcher lub sieciowej grupy zabezpieczeń.  

## <a name="gateway-required-vnet-integration"></a>Integracja sieci wirtualnej wymagana przez bramę

Integracja sieci wirtualnej wymagana przez bramę obsługuje łączenie z siecią wirtualną w innym regionie lub w klasycznej sieci wirtualnej. Integracja sieci wirtualnej wymagana przez bramę: 

* Umożliwia aplikacji łączenie tylko z 1 siecią wirtualną jednocześnie
* Umożliwia zintegrowanie maksymalnie pięciu sieci wirtualnych w ramach planu App Service 
* Umożliwia korzystanie z tej samej sieci wirtualnej przez wiele aplikacji w planie App Service bez wpływu na łączną liczbę, która może być używana przez plan App Service.  Jeśli masz sześć aplikacji korzystających z tej samej sieci wirtualnej w ramach tego samego planu App Service, który jest liczony jako 1 Sieć wirtualna. 
* Obsługuje umowę SLA na 99,9% ze względu na umowę SLA dotyczącą bramy
* Umożliwia aplikacjom używanie systemu DNS, z którym skonfigurowano sieć wirtualną
* Wymaga Virtual Network bramy opartej na trasach skonfigurowanej z siecią VPN typu punkt-lokacja, zanim będzie można połączyć się z aplikacją. 

Nie można używać integracji sieci wirtualnej wymaganej przez bramę:

* Z aplikacjami systemu Linux
* Z siecią wirtualną połączoną z ExpressRoute 
* Aby uzyskać dostęp do zabezpieczonych zasobów punktów końcowych usługi
* Z bramą współistnienia, która obsługuje zarówno ExpressRoute, jak i sieci VPN między lokacjami i lokacjami

### <a name="set-up-a-gateway-in-your-vnet"></a>Konfigurowanie bramy w sieci wirtualnej ###

Aby utworzyć bramę:

1. [Utwórz podsieć bramy][creategatewaysubnet] w sieci wirtualnej.  

1. [Utwórz bramę sieci VPN][creategateway]. Wybierz typ sieci VPN opartej na trasach.

1. [Ustaw pozycję adresy witryn][setp2saddresses]. Jeśli Brama nie znajduje się w podstawowej jednostce SKU, protokół IKEV2 musi być wyłączony w punkcie Konfiguracja lokacji i należy wybrać protokół SSTP. Przestrzeń adresowa punktu do lokacji musi znajdować się w blokach adresów RFC 1918, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Jeśli właśnie tworzysz bramę do użycia z integracją App Service sieci wirtualnej, nie musisz przekazywać certyfikatu. Tworzenie bramy może potrwać 30 minut. Nie będzie można zintegrować aplikacji z siecią wirtualną do momentu aprowizacji bramy. 

### <a name="how-gateway-required-vnet-integration-works"></a>Jak działa integracja z siecią wirtualną wymagana przez bramę

Integracja sieci wirtualnej wymagana przez bramę wbudowaną w oparciu o technologię VPN między punktami a lokacją. Wskaż sieci VPN z lokacją ogranicza dostęp do sieci tylko do maszyny wirtualnej obsługującej aplikację. Aplikacje są ograniczone tylko do wysyłania ruchu do Internetu za pomocą Połączenia hybrydowe lub przez integrację sieci wirtualnej. W przypadku skonfigurowania aplikacji przy użyciu portalu do korzystania z integracji sieci wirtualnej wymagane przez bramę w Twoim imieniu zarządza się złożoną negocjacją w celu utworzenia i przypisania certyfikatów na bramie i stronie aplikacji. Wynikiem tego jest to, że pracownicy używający do hostowania aplikacji będą mogli bezpośrednio łączyć się z bramą sieci wirtualnej w ramach wybranej sieci wirtualnej. 

![Jak działa integracja z siecią wirtualną wymagana przez bramę][6]

### <a name="accessing-on-premises-resources"></a>Uzyskiwanie dostępu do zasobów lokalnych

Aplikacje mogą uzyskiwać dostęp do zasobów lokalnych przez integrację z usługą sieci wirtualnych, która ma połączenia lokacja-lokacja. W przypadku korzystania z integracji sieci wirtualnej wymagane przez bramę należy zaktualizować trasy lokalnej bramy sieci VPN do bloków adresów punkt-lokacja. Po pierwszym skonfigurowaniu sieci VPN typu lokacja-lokacja, skrypty używane do skonfigurowania należy prawidłowo skonfigurować trasy. W przypadku dodania adresów punkt-lokacja po utworzeniu sieci VPN typu lokacja-lokacja należy ręcznie zaktualizować trasy. Szczegółowe informacje o tym, jak to zrobić, różnią się w zależności od bramy i nie zostały opisane tutaj. Nie można skonfigurować protokołu BGP z połączeniem sieci VPN typu lokacja-lokacja.

Nie jest wymagana dodatkowa konfiguracja funkcji integracji regionalnej sieci wirtualnej z siecią wirtualną ani w środowisku lokalnym. Wystarczy połączyć sieć wirtualną z lokalnymi przy użyciu usługi ExpressRoute lub sieci VPN typu lokacja-lokacja. 

> [!NOTE]
> Funkcja integracji sieci wirtualnej wymagana przez bramę nie integruje aplikacji z siecią wirtualną, która ma bramę ExpressRoute. Nawet jeśli Brama ExpressRoute jest skonfigurowana w [trybie współistnienia][VPNERCoex] , integracja sieci wirtualnej nie działa. Jeśli potrzebujesz dostępu do zasobów za pomocą połączenia ExpressRoute, możesz użyć funkcji integracji regionalnej sieci wirtualnej lub [App Service Environment][ASE], która działa w sieci wirtualnej. 
> 
> 

### <a name="peering"></a>Komunikacja równorzędna

Jeśli używasz komunikacji równorzędnej z integracją regionalnej sieci wirtualnej, nie musisz wykonywać żadnych dodatkowych czynności konfiguracyjnych. 

Jeśli jest używana brama wymagana Integracja sieci wirtualnej z usługą komunikacji równorzędnej, należy skonfigurować kilka dodatkowych elementów. Aby skonfigurować komunikację równorzędną do pracy z aplikacją:

1. Dodaj połączenie komunikacji równorzędnej w sieci wirtualnej, z którą łączy się aplikacja. Podczas dodawania połączenia komunikacji równorzędnej Włącz **dostęp do sieci wirtualnej** i sprawdź, czy **Zezwalaj na ruch przesłany dalej** i **Zezwalaj na tranzyt bramy**.
1. Dodaj połączenie komunikacji równorzędnej w sieci wirtualnej, która jest połączona z siecią wirtualną, z którą nawiązano połączenie. Podczas dodawania połączenia komunikacji równorzędnej w docelowej sieci wirtualnej Włącz **dostęp do usługi Virtual Network** i sprawdź, czy Zezwalaj na **ruch przesłany dalej** i **Zezwalaj na bramy zdalne**.
1. Przejdź do planu App Service > Networking > interfejsie użytkownika integracji sieci wirtualnej w portalu.  Wybierz sieć wirtualną, z którą aplikacja nawiązuje połączenie. W sekcji Routing Dodaj zakres adresów sieci wirtualnej, która jest połączona z siecią wirtualną.  

## <a name="managing-vnet-integration"></a>Zarządzanie integracją sieci wirtualnej 

Łączenie i rozłączanie z siecią wirtualną jest na poziomie aplikacji. Operacje, które mogą mieć wpływ na integrację sieci wirtualnej w wielu aplikacjach, są na poziomie planu App Service. W portalu integracja z aplikacją > Networking > sieci wirtualnej możesz uzyskać szczegółowe informacje dotyczące sieci wirtualnej. Podobne informacje znajdują się na poziomie ASP w portalu integracji sieci wirtualnej ASP > Networking >.

Jedyną operacją, którą można wykonać w widoku aplikacji integracji sieci wirtualnej, jest rozłączenie aplikacji z sieci wirtualnej, z którą jest ona aktualnie połączona. Aby odłączyć aplikację od sieci wirtualnej, wybierz pozycję **Rozłącz**. Aplikacja zostanie uruchomiona ponownie po rozłączeniu z siecią wirtualną. Rozłączanie nie zmienia sieci wirtualnej. Podsieć lub Brama nie jest usuwana. Jeśli chcesz usunąć sieć wirtualną, musisz najpierw odłączyć aplikację od sieci wirtualnej i usunąć z niej zasoby, takie jak bramy. 

Interfejs użytkownika integracji sieci wirtualnej ASP będzie zawierać wszystkie integracje sieci wirtualnej używane przez aplikacje w środowisku ASP. Aby wyświetlić szczegółowe informacje o każdej sieci wirtualnej, kliknij sieć wirtualną, której chcesz zainteresować. Istnieją dwie akcje, które można wykonać w tym miejscu dla integracji z siecią wirtualną wymagana przez bramę.

* **Synchronizuj sieć**. Operacja synchronizacji sieci dotyczy tylko funkcji integracji wirtualnej zależnej od bramy. Wykonanie operacji synchronizacji sieci gwarantuje, że certyfikaty i informacje o sieci są zsynchronizowane. Jeśli dodasz lub zmienisz system DNS sieci wirtualnej, musisz wykonać operację **synchronizacji sieci** . Ta operacja spowoduje ponowne uruchomienie wszystkich aplikacji korzystających z tej sieci wirtualnej.
* **Dodawanie tras** Dodanie tras spowoduje przekierowanie ruchu wychodzącego do sieci wirtualnej. 

**Routing integracji sieci wirtualnej wymaga bramy** Trasy, które są zdefiniowane w sieci wirtualnej, są używane do kierowania ruchu do sieci wirtualnej z aplikacji. Jeśli musisz wysłać dodatkowy ruch wychodzący do sieci wirtualnej, możesz dodać te bloki adresów tutaj. Ta funkcja działa tylko z wirtualną integracją sieci wirtualnej. Tabele tras nie wpływają na ruch aplikacji w przypadku korzystania z integracji sieci wirtualnej wymagane przez bramę w taki sposób, aby były zintegrowane z regionalną integracją sieci wirtualnej.

**Certyfikaty integracji sieci wirtualnej wymagane przez bramę** Gdy brama wymaga integracji sieci wirtualnej, wymagana jest wymiana certyfikatów w celu zapewnienia bezpieczeństwa połączenia. Wraz z certyfikatami są Konfiguracja DNS, trasy i inne podobne elementy opisujące sieć.
Jeśli zostaną zmienione informacje o certyfikatach lub sieci, należy kliknąć pozycję "Synchronizuj sieć". Po kliknięciu przycisku "Synchronizuj sieć" pojawi się krótka awaria połączenia między aplikacją a siecią wirtualną. Gdy aplikacja nie zostanie uruchomiona ponownie, utrata łączności może spowodować, że lokacja nie działa prawidłowo. 

## <a name="pricing-details"></a>Szczegóły cennika
Funkcja integracji regionalnej sieci wirtualnej nie ma dodatkowej opłaty za użycie poza warstwą cenową ASP.

Istnieją trzy powiązane opłaty za korzystanie z funkcji integracji sieci wirtualnej wymagane przez bramę:

* Opłaty za warstwę cenową ASP — Twoje aplikacje muszą być zgodne z planem App Service Standard, Premium lub PremiumV2. Więcej informacji o tych kosztach można znaleźć tutaj: [cennik App Service][ASPricing]. 
* Koszty transferu danych — naliczane są opłaty za wychodzące dane, nawet jeśli sieć wirtualna znajduje się w tym samym centrum danych. Te opłaty są opisane w [transfer danych szczegóły cennika][DataPricing]. 
* VPN Gateway koszty — istnieje koszt dla bramy sieci wirtualnej, która jest wymagana w przypadku połączeń VPN typu punkt-lokacja. Szczegóły znajdują się na stronie [cennika VPN Gateway][VNETPricing] .

## <a name="troubleshooting"></a>Rozwiązywanie problemów

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automatyzacja

Interfejs wiersza polecenia obsługuje regionalną integrację sieci wirtualnej. Aby uzyskać dostęp do następujących poleceń, [Zainstaluj interfejs wiersza polecenia platformy Azure][installCLI]. 

        az webapp vnet-integration --help

        Group
            az webapp vnet-integration : Methods that list, add, and remove virtual network integrations
            from a webapp.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            add    : Add a regional virtual network integration to a webapp.
            list   : List the virtual network integrations on a webapp.
            remove : Remove a regional virtual network integration from webapp.

        az appservice vnet-integration --help

        Group
            az appservice vnet-integration : A method that lists the virtual network integrations used in an
            appservice plan.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            list : List the virtual network integrations used in an appservice plan.

W przypadku integracji z siecią wirtualną wymagana przez bramę można zintegrować App Service z Virtual Network platformy Azure przy użyciu programu PowerShell. Aby uzyskać gotowy do uruchomienia skrypt, zobacz [łączenie aplikacji w Azure App Service z usługą Azure Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
