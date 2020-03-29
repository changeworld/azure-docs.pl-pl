---
title: Integracja aplikacji z usługą Azure Virtual Network
description: Integruj aplikacje w usłudze Azure App Service z sieciami wirtualnymi platformy Azure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89aa78e0d26598eacf436ca88cc6c5549f91d2fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673227"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrowanie aplikacji z usługą Azure Virtual Network
W tym dokumencie opisano funkcję integracji sieci wirtualnej usługi Azure App Service i sposób konfigurowania jej za pomocą aplikacji w [usłudze Azure App Service.](https://go.microsoft.com/fwlink/?LinkId=529714) [Sieci wirtualne platformy Azure][VNETOverview] (sieci wirtualne) umożliwiają umieszczenie wielu zasobów platformy Azure w sieci niezwiązanych z Internetem routingu.  

Usługa azure app service ma dwie odmiany.

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Włącz integrację sieci wirtualnej 

1. Przejdź do interfejsu użytkownika sieci w portalu usługi app service. W obszarze Integracja sieci wirtualnej wybierz opcję *"Kliknij tutaj, aby skonfigurować"*. 

1. Wybierz **pozycję Dodaj wirtualną sieci .**  

   ![Wybierz integrację sieci wirtualnej][1]

1. Lista rozwijana będzie zawierać wszystkie sieci wirtualne Menedżera zasobów w ramach subskrypcji w tym samym regionie i poniżej, która jest listą wszystkich sieci wirtualnych Menedżera zasobów we wszystkich innych regionach. Wybierz sieci wirtualnej, z którą chcesz zintegrować.

   ![Wybierz vnet][2]

   * Jeśli sieć wirtualna znajduje się w tym samym regionie, utwórz nową podsieć lub wybierz pustą wcześniej istniejącą podsieć. 

   * Aby wybrać sieć wirtualną w innym regionie, musisz mieć aprowizowana brama sieci wirtualnej z włączoną obsługą punktu do lokacji.

   * Aby zintegrować się z klasyczną siecią wirtualną, zamiast klikać z siecią rozwijaną, wybierz pozycję **Kliknij tutaj, aby połączyć się z klasyczną siecią wirtualną**. Wybierz żądaną klasyczną sieci wirtualną. Docelowa sieć wirtualna musi mieć już aprowizowana brama sieci wirtualnej z włączoną obsługą punktu do lokacji.

    ![Wybierz klasyczną wirtualną sieci][3]
    
Podczas integracji aplikacja zostanie ponownie uruchomiona.  Po zakończeniu integracji zobaczysz szczegóły w sieci wirtualnej, z którą jesteś zintegrowany. 

Gdy aplikacja zostanie zintegrowana z siecią wirtualną, użyje tego samego serwera DNS, z którego jest skonfigurowana sieć wirtualna, chyba że jest to strefa prywatna usługi Azure DNS. Obecnie nie można używać integracji sieci wirtualnej z strefami prywatnymi dns platformy Azure.

## <a name="regional-vnet-integration"></a>Regionalna integracja sieci wirtualnej

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Jak działa regionalna integracja sieci wirtualnych

Aplikacje w usłudze App Service są hostowane w rolach procesu roboczego. Podstawowe i wyższe plany cenowe są dedykowanymi planami hostingowymi, w których nie ma innych obciążeń klientów działających na tych samych pracownikach. Regionalna integracja sieci wirtualnej polega na instalowaniu interfejsów wirtualnych z adresami w delegowanej podsieci. Ponieważ z adresu znajduje się w sieci wirtualnej, można uzyskać dostęp do większości rzeczy w lub za pośrednictwem sieci wirtualnej, podobnie jak maszyny Wirtualnej w sieci wirtualnej będzie. Implementacja sieci różni się od uruchamiania maszyny Wirtualnej w sieci wirtualnej i dlatego niektóre funkcje sieciowe nie są jeszcze dostępne podczas korzystania z tej funkcji.

![Jak działa regionalna integracja sieci wirtualnych][5]

Gdy regionalna integracja sieci wirtualnej jest włączona, aplikacja będzie nadal wykonywać połączenia wychodzące do Internetu za pośrednictwem tych samych kanałów, jak zwykle. Adresy wychodzące wymienione w portalu właściwości aplikacji są nadal adresami używanymi przez aplikację. Jakie zmiany w aplikacji są wywołania usług zabezpieczonych punktu końcowego usługi lub RFC 1918 adresy przechodzi do sieci wirtualnej. Jeśli WEBSITE_VNET_ROUTE_ALL jest ustawiona na 1, następnie cały ruch wychodzący mogą być wysyłane do sieci wirtualnej. 

Funkcja obsługuje tylko jeden interfejs wirtualny na proces roboczy.  Jeden interfejs wirtualny na proces roboczy oznacza jedną regionalną integrację sieci wirtualnej na plan usługi aplikacji. Wszystkie aplikacje w tym samym planie usługi App Service można użyć tej samej integracji sieci wirtualnej, ale jeśli potrzebujesz aplikacji, aby połączyć się z dodatkową siecią wirtualną, należy utworzyć inny plan usługi app service. Używany interfejs wirtualny nie jest zasobem, do którego klienci mają bezpośredni dostęp.

Ze względu na charakter działania tej technologii ruch używany z integracją sieci wirtualnej nie jest pokazywany w dziennikach przepływu obserwatora sieci lub sieci NSG.  

## <a name="gateway-required-vnet-integration"></a>Wymagana integracja sieci wirtualnej bramy

Integracja sieci wirtualnej wymaganej bramy obsługuje łączenie się z siecią wirtualną w innym regionie lub z klasyczną siecią wirtualną. Wymagana integracja sieci wirtualnej bramy: 

* Umożliwia aplikacji łączenie się tylko z 1 siecią wirtualną naraz
* Umożliwia integrację maksymalnie pięciu sieci wirtualnych w planie usługi app service 
* Umożliwia tej samej sieci wirtualnej do użycia przez wiele aplikacji w planie usługi aplikacji bez wpływu na całkowitą liczbę, która może być używana przez plan usługi aplikacji.  Jeśli masz sześć aplikacji przy użyciu tej samej sieci wirtualnej w tym samym planie usługi app service, która liczy się jako 1 sieci wirtualnej używane. 
* Obsługuje 99,9% umowy SLA ze względu na umowy SLA na bramie
* Umożliwia aplikacjom korzystanie z systemu DNS skonfigurowany przez witrynę wirtualną
* Wymaga bramy opartej na trasie sieci wirtualnej skonfigurowaną z siecią VPN typu punkt-lokacja SSTP, zanim będzie można ją połączyć z aplikacją. 

Nie można użyć wymaganej integracji sieci wirtualnej bramy:

* Z aplikacjami Linux
* Z siecią wirtualną połączoną z usługą ExpressRoute 
* Aby uzyskać dostęp do zabezpieczonych zasobów punktów końcowych usługi
* Z bramą współistnienia, która obsługuje zarówno usługę ExpressRoute, jak i sieci VPN punktu do lokacji

### <a name="set-up-a-gateway-in-your-vnet"></a>Konfigurowanie bramy w sieci wirtualnej ###

Aby utworzyć bramę:

1. [Tworzenie podsieci bramy][creategatewaysubnet] w sieci wirtualnej.  

1. [Utwórz bramę sieci VPN][creategateway]. Wybierz typ sieci VPN oparty na trasie.

1. [Ustaw adresy punktu do witryny][setp2saddresses]. Jeśli bramy nie ma podstawowej jednostki SKU, należy wyłączyć protokół IKEV2 w konfiguracji punktu do lokacji i należy wybrać protokół SSTP. Przestrzeń adresowa punktu do miejsca musi znajdować się w blokach adresowych RFC 1918, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Jeśli po prostu tworzysz bramę do użytku z integracją sieci wirtualnej usługi App Service, nie musisz przekazywać certyfikatu. Utworzenie bramy może potrwać 30 minut. Nie będzie można zintegrować aplikacji z siecią wirtualną, dopóki brama nie zostanie zainicjowana. 

### <a name="how-gateway-required-vnet-integration-works"></a>Jak działa wymagana integracja sieci wirtualnej bramy

Wymagana integracja sieci wirtualnej bramy zbudowana na technologii sieci VPN typu punkt-lokacja. Sieci VPN punktu do lokacji ograniczają dostęp do sieci tylko do maszyny wirtualnej obsługującej aplikację. Aplikacje są ograniczone tylko do wysyłania ruchu do Internetu, za pośrednictwem połączeń hybrydowych lub za pośrednictwem integracji sieci wirtualnej. Gdy aplikacja jest skonfigurowana z portalem do używania wymaganej integracji sieci wirtualnej bramy, w Twoim imieniu zarządzana jest złożona negocjacja w celu utworzenia i przypisania certyfikatów po stronie bramy i aplikacji. Efektem końcowym jest to, że pracownicy używane do hosta aplikacji są w stanie bezpośrednio połączyć się z bramą sieci wirtualnej w wybranej sieci wirtualnej. 

![Jak działa wymagana integracja sieci wirtualnej bramy][6]

### <a name="accessing-on-premises-resources"></a>Uzyskiwanie dostępu do zasobów lokalnych

Aplikacje mogą uzyskiwać dostęp do zasobów lokalnych, integrując się z sieciami wirtualnymi, które mają połączenia lokacja-lokacja. Jeśli używasz wymaganej integracji sieci wirtualnej bramy, musisz zaktualizować lokalne trasy bramy sieci VPN za pomocą bloków adresów typu punkt-lokacja. Po pierwszej konfiguracji sieci VPN między lokacjami skrypty używane do jej konfigurowania powinny poprawnie skonfigurować trasy. Jeśli po utworzeniu sieci VPN między lokacjami zostanie dodane adresy typu "punkt-lokacja", należy ręcznie zaktualizować trasy. Szczegółowe informacje na temat tego, jak to zrobić, różnią się w zależności od bramy i nie są opisane w tym miejscu. Nie można skonfigurować protokołu BGP z połączeniem sieci VPN między lokacjami.

Nie ma potrzeby dodatkowej konfiguracji dla regionalnej integracji sieci wirtualnej, aby dotrzeć za pośrednictwem sieci wirtualnej i lokalnie. Wystarczy połączyć sieć wirtualną z siecią lokalną przy użyciu usługi ExpressRoute lub sieci VPN typu lokacja lokacja. 

> [!NOTE]
> Funkcja integracji sieci wirtualnej wymaganej bramy nie integruje aplikacji z siecią wirtualną z bramą usługi ExpressRoute. Nawet jeśli brama usługi ExpressRoute jest skonfigurowana w [trybie współistnienia,][VPNERCoex] integracja sieci wirtualnej nie działa. Jeśli chcesz uzyskać dostęp do zasobów za pośrednictwem połączenia usługi ExpressRoute, możesz użyć regionalnej funkcji integracji sieci wirtualnej lub [środowiska usługi app service,][ASE]która jest uruchamiana w sieci wirtualnej. 
> 
> 

### <a name="peering"></a>Komunikacja równorzędna

Jeśli używasz komunikacji równorzędnej z regionalnej integracji sieci wirtualnej, nie trzeba wykonywać żadnych dodatkowych konfiguracji. 

Jeśli używasz bramy wymaganej integracji sieci wirtualnej z komunikacją równorzędną, należy skonfigurować kilka dodatkowych elementów. Aby skonfigurować komunikację równorzędnej do pracy z aplikacją:

1. Dodaj połączenie komunikacji równorzędnej w sieci wirtualnej, z którymi łączy się aplikacja. Podczas dodawania połączenia równorzędnego włącz opcję **Zezwalaj na dostęp do sieci wirtualnej** i zaznacz pole **wyboru Zezwalaj na ruch przesyłany dalej** i **Zezwalaj na przesyłanie bramy**.
1. Dodaj połączenie komunikacji równorzędnej w sieci wirtualnej, która jest równorzędna z siecią wirtualną, z którą masz połączenie. Podczas dodawania połączenia równorzędnego w docelowej sieci wirtualnej włącz opcję Zezwalaj na **dostęp do sieci wirtualnej** i zaznacz pole wyboru **Zezwalaj na ruch przesyłany dalej** i **Zezwalaj na bramy zdalne**.
1. Przejdź do planu usługi app service > interfejs użytkownika integracji sieci wirtualnej > sieci wirtualnej w portalu.  Wybierz sieci wirtualnej, z którymi łączy się aplikacja. W sekcji routingu dodaj zakres adresów sieci wirtualnej, która jest równorzędna z siecią wirtualną, z którą jest połączona aplikacja.  

## <a name="managing-vnet-integration"></a>Zarządzanie integracją sieci wirtualnej 

Łączenie i rozłączanie za pomocą sieci wirtualnej jest na poziomie aplikacji. Operacje, które mogą mieć wpływ na integrację sieci wirtualnej w wielu aplikacjach są na poziomie planu usługi app service. W portalu integracji sieci wirtualnej > sieci > można uzyskać szczegółowe informacje w sieci wirtualnej. Podobne informacje można zobaczyć na poziomie ASP w portalu integracji sieci > asp > sieci wirtualnej.

Jedyną operacją, którą można wykonać w widoku aplikacji integracji sieci wirtualnej jest odłączenie aplikacji od sieci wirtualnej, z którą jest obecnie połączony. Aby odłączyć aplikację od sieci wirtualnej, wybierz pozycję **Odłącz**. Aplikacja zostanie ponownie uruchomiona po odłączeniu od sieci wirtualnej. Rozłączanie nie zmienia sieci wirtualnej. Podsieć lub brama nie zostanie usunięta. Jeśli następnie chcesz usunąć sieci wirtualnej, należy najpierw odłączyć aplikację od sieci wirtualnej i usunąć zasoby w niej, takie jak bramy. 

Interfejs użytkownika integracji sieci wirtualnej ASP pokaże wszystkie integracje sieci wirtualnej używane przez aplikacje w programie ASP. Aby wyświetlić szczegółowe informacje na temat każdej sieci wirtualnej, kliknij witrynę wirtualną, która Cię interesuje. Istnieją dwie akcje, które można wykonać w tym miejscu dla integracji sieci wirtualnej wymagane bramy.

* **Synchronizacja sieci**. Operacja sieci synchronizacji jest tylko dla funkcji integracji sieci wirtualnej zależnej od bramy. Wykonanie operacji sieci synchronizacji gwarantuje, że certyfikaty i informacje o sieci są zsynchronizowane. Jeśli dodasz lub zmienisz dns sieci wirtualnej, musisz wykonać operację **sieci synchronizacji.** Ta operacja spowoduje ponowne uruchomienie wszystkich aplikacji przy użyciu tej sieci wirtualnej.
* **Dodawanie tras** Dodawanie tras spowoduje ruch wychodzący do sieci wirtualnej. 

**Routing integracji sieci wirtualnej wymaganej bramy** Trasy, które są zdefiniowane w sieci wirtualnej są używane do kierowania ruchu do sieci wirtualnej z aplikacji. Jeśli chcesz wysłać dodatkowy ruch wychodzący do sieci wirtualnej, możesz dodać te bloki adresów w tym miejscu. Ta funkcja działa tylko z integracją sieci wirtualnej wymaganej bramy. Tabele tras nie wpływają na ruch aplikacji podczas korzystania z integracji wymaganej sieci wirtualnej wymagane sposób, w jaki robią z regionalnej integracji sieci wirtualnej.

**Certyfikaty integracji wymaganej bramy sieci wirtualnej** Gdy brama wymagana integracja sieci wirtualnej jest włączona, istnieje wymagana wymiana certyfikatów w celu zapewnienia bezpieczeństwa połączenia. Wraz z certyfikatami są konfiguracja DNS, trasy i inne podobne rzeczy, które opisują sieć.
Jeśli certyfikaty lub informacje o sieci zostały zmienione, należy kliknąć przycisk "Synchronizuj sieć". Po kliknięciu przycisku "Synchronizuj sieć", spowodować krótką awarię łączności między aplikacją a siecią wirtualną. Gdy aplikacja nie zostanie ponownie uruchomiona, utrata łączności może spowodować, że witryna nie będzie działać poprawnie. 

## <a name="pricing-details"></a>Szczegóły cennika
Funkcja regionalnej integracji sieci wirtualnej nie ma żadnych dodatkowych opłat za użycie poza opłatami za warstwę cenową ASP.

Istnieją trzy powiązane opłaty za korzystanie z funkcji integracji wymaganej sieci wirtualnej bramy:

* Opłaty za warstwę cenową ASP — aplikacje muszą być w planie usługi aplikacji Standard, Premium lub PremiumV2. Więcej informacji na temat tych kosztów można znaleźć tutaj: [Ceny usługi app service][ASPricing]. 
* Koszty transferu danych — istnieje opłata za wyjście danych, nawet jeśli sieć wirtualna znajduje się w tym samym centrum danych. Opłaty te są opisane w [szczegóły cen transferu danych][DataPricing]. 
* Koszty bramy sieci VPN — brama sieci wirtualnej jest wymagana dla sieci VPN typu punkt-lokacja. Szczegóły znajdują się na stronie [Cennik bramy sieci VPN.][VNETPricing]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automatyzacja

Istnieje obsługa interfejsu wiersza polecenia dla integracji regionalnej sieci wirtualnej. Aby uzyskać dostęp do następujących poleceń, [zainstaluj narzędzie Azure CLI][installCLI]. 

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

W przypadku integracji sieci wirtualnej wymaganej przez bramę można zintegrować usługę App Service z siecią wirtualną platformy Azure przy użyciu programu PowerShell. Aby uzyskać gotowy do uruchomienia skrypt, zobacz [Łączenie aplikacji w usłudze Azure App Service z siecią wirtualną platformy Azure.](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)


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
