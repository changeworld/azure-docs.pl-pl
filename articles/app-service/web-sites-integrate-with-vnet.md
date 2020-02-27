---
title: Integrowanie aplikacji z usługą Azure Virtual Network
description: Integruj aplikacje w Azure App Service z sieciami wirtualnymi platformy Azure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 76139716fe11536faa0ff792185ba1643801c641
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649046"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrowanie aplikacji z usługą Azure Virtual Network
W tym dokumencie opisano funkcję integracji Azure App Service sieci wirtualnej i sposób jej konfigurowania z aplikacjami w [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). [Usługa Azure Virtual Networks][VNETOverview] (sieci wirtualnych) umożliwia umieszczanie wielu zasobów platformy Azure w sieci bez obsługi Internetu.  

Azure App Service ma dwie odmiany. 

1. Systemy wielodostępne, które obsługują pełny zakres planów cenowych, z wyjątkiem izolowanych
2. App Service Environment (ASE), który wdraża w sieci wirtualnej i obsługuje aplikacje izolowanego planu cenowego

Ten dokument przechodzi przez funkcję integracji sieci wirtualnej, która jest używana w App Service z wieloma dzierżawcami. Jeśli Twoja aplikacja znajduje się w [App Service Environment][ASEintro], jest już w sieci wirtualnej i nie wymaga użycia funkcji integracji sieci wirtualnej w celu uzyskania dostępu do zasobów w tej samej sieci wirtualnej. Aby uzyskać szczegółowe informacje na temat wszystkich App Service funkcji sieciowych, Przeczytaj [App Service funkcje sieciowe](networking-features.md)

Integracja z siecią wirtualną umożliwia aplikacji sieci Web dostęp do zasobów w sieci wirtualnej, ale nie przyznaje prywatnego dostępu do aplikacji internetowej z sieci VNet. Dostęp do lokacji prywatnej dotyczy udostępniania aplikacji tylko z sieci prywatnej, takiej jak z poziomu sieci wirtualnej platformy Azure. Integracja z siecią wirtualną dotyczy tylko wykonywania połączeń wychodzących z aplikacji do sieci wirtualnej. Funkcja integracji sieci wirtualnej działa inaczej, gdy jest używana z sieci wirtualnych w tym samym regionie i z sieci wirtualnych w innych regionach. Funkcja integracji sieci wirtualnej ma dwie odmiany.

1. Integracja regionalnej sieci wirtualnej — w przypadku nawiązywania połączenia z usługą Menedżer zasobów sieci wirtualnych w tym samym regionie należy mieć dedykowaną podsieć w ramach integracji z usługą. 
2. Integracja sieci wirtualnej wymagana przez bramę — w przypadku nawiązywania połączenia z usługą sieci wirtualnych w innych regionach lub w klasycznej sieci wirtualnej w tym samym regionie potrzebna jest brama Virtual Network obsługiwana w docelowej sieci wirtualnej.

Funkcje integracji sieci wirtualnej:

* Wymagaj planu cenowego w warstwie Standardowa, Premium, PremiumV2 lub elastycznym 
* Obsługa protokołów TCP i UDP
* Pracuj z aplikacjami App Service i aplikacjami funkcji

Istnieje kilka rzeczy, które nie są obsługiwane przez integrację sieci wirtualnej, w tym:

* Instalowanie dysku
* Integracja z usługą AD 
* NetBios

Integracja sieci wirtualnej wymagana przez bramę zapewnia tylko dostęp do zasobów w docelowej sieci wirtualnej lub w sieciach połączonych z docelową siecią wirtualną za pomocą komunikacji równorzędnej lub sieci VPN. Integracja sieci wirtualnej wymagana przez bramę nie umożliwia dostępu do zasobów dostępnych w połączeniach ExpressRoute lub współpracuje z punktami końcowymi usługi. 

Niezależnie od używanej wersji integracja z siecią wirtualną umożliwia aplikacji sieci Web dostęp do zasobów w sieci wirtualnej, ale nie przyznaje prywatnego dostępu do aplikacji sieci Web z sieci wirtualnej. Dostęp do lokacji prywatnej dotyczy udostępniania aplikacji tylko z sieci prywatnej, takiej jak z poziomu sieci wirtualnej platformy Azure. Integracja z siecią wirtualną dotyczy tylko wykonywania połączeń wychodzących z aplikacji do sieci wirtualnej. 

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

Użycie integracji regionalnej sieci wirtualnej umożliwia aplikacji dostęp do:

* zasoby w sieci wirtualnej w tym samym regionie, w którym integrujesz się z usługą 
* zasoby w sieci wirtualnych komunikacji równorzędnej z siecią wirtualną znajdującą się w tym samym regionie
* zabezpieczone usługi punktu końcowego usługi
* zasoby w ramach połączeń ExpressRoute
* zasoby w sieci wirtualnej, z którą nawiązano połączenie
* zasoby między połączeniami równorzędnymi, w tym połączenia ExpressRoute
* Prywatne punkty końcowe 

W przypadku korzystania z integracji sieci wirtualnej z usługą sieci wirtualnych w tym samym regionie można korzystać z następujących funkcji sieciowych platformy Azure:

* Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) — można zablokować ruch wychodzący z sieciową grupą zabezpieczeń umieszczoną w podsieci integracji. Reguły ruchu przychodzącego nie są stosowane, ponieważ nie można używać integracji sieci wirtualnej w celu zapewnienia dostępu przychodzącego do aplikacji internetowej.
* Tabele tras (UDR) — tabelę tras można umieścić w podsieci integracji w celu wysłania ruchu wychodzącego w dowolnym miejscu. 

Domyślnie aplikacja będzie kierować ruchem RFC1918 do sieci wirtualnej. Jeśli chcesz skierować cały ruch wychodzący do sieci wirtualnej, Zastosuj ustawienie aplikacji WEBSITE_VNET_ROUTE_ALL do aplikacji. Aby skonfigurować ustawienie aplikacji:

1. Przejdź do interfejsu użytkownika konfiguracji w portalu aplikacji. Wybierz **nowe ustawienie aplikacji**
1. Wpisz **WEBSITE_VNET_ROUTE_ALL** w polu Nazwa i **1** w polu wartość

   ![Podaj ustawienie aplikacji][4]

1. Kliknij przycisk **OK**
1. Wybierz pozycję **Zapisz**.

Jeśli cały ruch wychodzący zostanie rozesłany do sieci wirtualnej, będzie on podlegać sieciowych grup zabezpieczeń i UDR, które są stosowane do podsieci integracji. Gdy przekierowujesz cały ruch wychodzący do sieci wirtualnej, adresy wychodzące nadal będą adresami wychodzącymi wymienionymi we właściwościach aplikacji, chyba że zostaną podane trasy do wysyłania ruchu w innym miejscu. 

Istnieją pewne ograniczenia dotyczące używania integracji sieci wirtualnej z usługą sieci wirtualnych w tym samym regionie:

* Nie można uzyskać dostępu do zasobów w ramach globalnych połączeń komunikacji równorzędnej
* Funkcja jest dostępna tylko w nowszych App Service jednostkach skalowania, które obsługują plany App Service PremiumV2.
* Podsieć integracji może być używana tylko przez jeden plan App Service
* Funkcja nie może być używana przez aplikacje planu izolowanego, które znajdują się w App Service Environment
* Ta funkcja wymaga nieużywanej podsieci, która jest/27 z co najmniej 32 adresów w sieci wirtualnej Menedżer zasobów
* Aplikacja i Sieć wirtualna muszą znajdować się w tym samym regionie
* Nie można usunąć sieci wirtualnej przy użyciu zintegrowanej aplikacji. Usuń integrację przed usunięciem sieci wirtualnej. 
* Integrację z usługą sieci wirtualnych można zintegrować tylko w ramach tej samej subskrypcji, w której znajduje się aplikacja sieci Web
* Możesz mieć tylko jedną regionalną integrację sieci wirtualnej na App Service plan. Wiele aplikacji w tym samym planie App Service może korzystać z tej samej sieci wirtualnej. 
* Nie można zmienić subskrypcji aplikacji ani planu App Service, gdy istnieje aplikacja, która korzysta z integracji regionalnej sieci wirtualnej.

Jeden adres jest używany dla każdego wystąpienia planu App Service. W przypadku skalowania aplikacji do pięciu wystąpień są używane pięć adresów. Ponieważ nie można zmienić rozmiaru podsieci po przypisaniu, należy użyć podsieci, która jest wystarczająco duża, aby można było dowolnie skalować dostęp do aplikacji. Zalecany rozmiar to/26 z 64 adresami. Adresy A/26 z 64mi umożliwią planowanie App Service w warstwie Premium z 30 wystąpieniami. W przypadku skalowania planu App Service w górę lub w dół wystarczy kilka adresów w krótkim czasie. 

Jeśli chcesz, aby aplikacje w innym App Service planować dostęp do sieci wirtualnej, która jest już połączona z aplikacjami w innym planie App Service, musisz wybrać inną podsieć niż ta, która jest używana przez istniejącą integrację z siecią wirtualną.  

Ta funkcja jest dostępna w wersji zapoznawczej dla systemu Linux. Formularz systemu Linux funkcji obsługuje tylko wykonywanie wywołań do adresów RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web-app-for-containers"></a>Web App for Containers

Jeśli używasz App Service w systemie Linux z wbudowanymi obrazami, integracja regionalnej sieci wirtualnej działa bez dodatkowych zmian. W przypadku używania Web App for Containers należy zmodyfikować obraz platformy Docker, aby można było korzystać z integracji z siecią wirtualną. W obrazie platformy Docker Użyj zmiennej środowiskowej portu jako portu nasłuchu głównego serwera sieci Web, zamiast używać numeru portu stałe. Zmienna środowiskowa portu jest automatycznie ustawiana przez App Service platformę w czasie uruchamiania kontenera. Jeśli używasz protokołu SSH, demona SSH musi być skonfigurowana do nasłuchiwania numeru portu określonego przez zmienną środowiskową SSH_PORT podczas korzystania z integracji regionalnej sieci wirtualnej.  W systemie Linux nie jest obsługiwana integracja z siecią wirtualną wymagana przez bramę. 

### <a name="service-endpoints"></a>Punkty końcowe usługi

Integracja z regionalną siecią wirtualną umożliwia korzystanie z punktów końcowych usługi.  Aby korzystać z punktów końcowych usługi w aplikacji, należy użyć regionalnej integracji sieci wirtualnej w celu nawiązania połączenia z wybraną siecią wirtualną, a następnie skonfigurowania punktów końcowych usługi w podsieci użytej do integracji. 

### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

Sieciowe grupy zabezpieczeń umożliwiają zablokowanie ruchu przychodzącego i wychodzącego do zasobów w sieci wirtualnej. Aplikacja internetowa korzystająca z integracji regionalnej sieci wirtualnej może używać [sieciowej grupy zabezpieczeń][VNETnsg] do blokowania ruchu wychodzącego do zasobów w sieci wirtualnej lub w Internecie. Aby zablokować ruch do adresów publicznych, musisz mieć ustawienie aplikacji WEBSITE_VNET_ROUTE_ALL ustawione na 1. Reguły ruchu przychodzącego w sieciowej grupy zabezpieczeń nie mają zastosowania do Twojej aplikacji, ponieważ integracja z siecią wirtualną ma wpływ tylko na ruch wychodzący z aplikacji. Aby kontrolować ruch przychodzący do aplikacji sieci Web, użyj funkcji ograniczenia dostępu. SIECIOWEJ grupy zabezpieczeń zastosowana do podsieci integracji będzie obowiązywać niezależnie od wszelkich tras zastosowanych do podsieci integracji. Jeśli WEBSITE_VNET_ROUTE_ALL została ustawiona na 1 i nie ma żadnych tras wpływających na ruch publiczny w podsieci integracji, cały ruch wychodzący nadal będzie podlegać sieciowych grup zabezpieczeń przypisanemu do podsieci integracji. Jeśli nie ustawiono WEBSITE_VNET_ROUTE_ALL, sieciowych grup zabezpieczeń będzie stosowany tylko do ruchu RFC1918.

### <a name="routes"></a>Trasy

Tabele tras umożliwiają kierowanie ruchu wychodzącego z aplikacji do wszędzie tam, gdzie chcesz. Domyślnie tabele tras mają wpływ tylko na ruch docelowy RFC1918.  Jeśli ustawisz wartość WEBSITE_VNET_ROUTE_ALL na 1, wpłynie to na wszystkie wywołania wychodzące. Trasy ustawione w podsieci integracji nie będą miały wpływu na odpowiedzi na żądania aplikacji przychodzących. Wspólne miejsca docelowe mogą obejmować urządzenia zapory lub bramy. Jeśli chcesz skierować cały ruch wychodzący lokalnie, możesz użyć tabeli tras do wysłania całego ruchu wychodzącego do bramy ExpressRoute. W przypadku kierowania ruchu do bramy należy ustawić trasy w sieci zewnętrznej, aby wysyłać odpowiedzi z powrotem.

Trasy Border Gateway Protocol (BGP) wpłyną również na ruch aplikacji. Jeśli masz trasy BGP ze względu na bramę ExpressRoute, wpłynie to na ruch wychodzący aplikacji. Domyślnie trasy protokołu BGP mają wpływ tylko na ruch docelowy RFC1918. Jeśli wartość WEBSITE_VNET_ROUTE_ALL jest równa 1, trasy protokołu BGP mogą mieć wpływ na cały ruch wychodzący. 

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
Chociaż ta funkcja jest łatwa do skonfigurowania, nie oznacza to, że Twoje środowisko nie będzie miało problemu. Jeśli wystąpią problemy z uzyskaniem dostępu do żądanego punktu końcowego, istnieją pewne narzędzia, których można użyć do testowania łączności z poziomu konsoli aplikacji. Istnieją dwie konsole, których można użyć. Jedna z nich jest konsolą kudu, a druga jest konsolą w Azure Portal. Aby nawiązać połączenie z konsolą kudu z poziomu aplikacji, przejdź do pozycji narzędzia-> kudu. Możesz również uzyskać dostęp do konsoli Kudo na stronie [sitename]. SCM. azurewebsites. NET. Po załadowaniu witryny sieci Web przejdź do karty konsola debugowania. Aby przejść do Azure Portal hostowanej konsoli, w aplikacji przejdź do pozycji narzędzia — > Konsola. 

#### <a name="tools"></a>Narzędzia
Narzędzia **ping**, **nslookup**i **tracert** nie będą przełączane przez konsolę ze względu na ograniczenia zabezpieczeń. Aby wypełnić wartość void, dodano dwa osobne narzędzia. W celu przetestowania funkcjonalności DNS dodaliśmy narzędzie o nazwie nameresolver. exe. Składnia jest następująca:

    nameresolver.exe hostname [optional: DNS Server]

Możesz użyć **nameresolver** , aby sprawdzić nazwy hostów, od których zależy aplikacja. W ten sposób możesz sprawdzić, czy masz jakieś nieprawidłowo skonfigurowane usługi DNS lub że nie masz dostępu do serwera DNS. Możesz zobaczyć serwer DNS, który będzie używany przez aplikację w konsoli programu, przeglądając zmienne środowiskowe WEBSITE_DNS_SERVER i WEBSITE_DNS_ALT_SERVER.

Następne narzędzie umożliwia przetestowanie łączności TCP z połączeniem hosta i portu. To narzędzie ma nazwę **tcpping** , a Składnia:

    tcpping.exe hostname [optional: port]

Narzędzie **tcpping** informuje o tym, czy można nawiązać połączenie z określonym hostem i portem. Można wyświetlić tylko sukces, jeśli: istnieje aplikacja nasłuchująca na hoście i w kombinacji portów i istnieje dostęp do sieci z aplikacji do określonego hosta i portu.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Debugowanie dostępu do hostowanych zasobów sieci wirtualnej
Istnieje wiele rzeczy, które mogą uniemożliwić aplikacji osiągnięcie określonego hosta i portu. Większość czasu jest jedną z trzech rzeczy:

* **Zapora jest w ten sposób.** Jeśli w ten sposób masz zaporę, zostanie osiągnięty limit czasu protokołu TCP. Limit czasu protokołu TCP wynosi 21 sekund w tym przypadku. Użyj narzędzia **tcpping** , aby przetestować łączność. Limity czasu protokołu TCP mogą być spowodowane wieloma niektórymi zaporami, ale w tym miejscu. 
* **Usługa DNS jest niedostępna.** Limit czasu DNS wynosi trzy sekundy na serwer DNS. Jeśli masz dwa serwery DNS, limit czasu wynosi 6 sekund. Użyj nameresolver, aby sprawdzić, czy usługa DNS działa. Należy pamiętać, że nie można użyć polecenia nslookup, które nie korzysta z serwera DNS skonfigurowanego dla sieci wirtualnej. W przypadku niedostępności może być dostępna zapora lub sieciowej grupy zabezpieczeń blokujące dostęp do systemu DNS lub może nie działać.

Jeśli te elementy nie odpowiadają na problemy, należy najpierw sprawdzić, jak: 

**Integracja z regionalną siecią wirtualną**
* Czy lokalizacją docelową jest adres inny niż RFC1918, a WEBSITE_VNET_ROUTE_ALL nie ma ustawionej wartości 1
* Czy istnieje sieciowej grupy zabezpieczeń blokujące ruch wychodzący z podsieci integracji
* w przypadku przechodzenia między ExpressRoute lub sieci VPN czy brama lokalna jest skonfigurowana do kierowania ruchu z kopii zapasowej na platformę Azure? Jeśli możesz uzyskać dostęp do punktów końcowych w sieci wirtualnej, ale nie w środowisku lokalnym, sprawdź trasy.
* Czy masz wystarczające uprawnienia do ustawiania delegowania w podsieci integracji? Podczas konfiguracji integracji regionalnej sieci wirtualnej Twoja podsieć integracji zostanie przedelegowana do firmy Microsoft. Web. Interfejs użytkownika integracji sieci wirtualnej automatycznie przekaże podsieć do firmy Microsoft. Web. Jeśli Twoje konto nie ma wystarczających uprawnień sieciowych do ustawiania delegowania, będziesz potrzebować kogoś, kto może ustawić atrybuty w podsieci integracji w celu delegowania podsieci. Aby ręcznie delegować podsieć integracji, przejdź do interfejsu użytkownika podsieci usługi Azure Virtual Network i ustaw delegowanie dla Microsoft. Web. 

**Integracja sieci wirtualnej wymagana przez bramę**
* czy zakres adresów punkt-lokacja w zakresach RFC 1918 (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* Czy brama ma być wyświetlana w portalu? Jeśli Brama nie działa, przenieś ją z powrotem.
* Czy certyfikaty są wyświetlane jako zsynchronizowane lub czy podejrzewasz, że konfiguracja sieci została zmieniona?  Jeśli certyfikaty nie są zsynchronizowane lub podejrzewasz, że wprowadzono zmiany w konfiguracji sieci wirtualnej, które nie zostały zsynchronizowane z nazywani, naciśnij pozycję "Synchronizuj sieć".
* Jeśli przechodzą przez sieć VPN, czy brama lokalna skonfigurowana do kierowania ruchu z kopii zapasowej na platformę Azure? Jeśli możesz uzyskać dostęp do punktów końcowych w sieci wirtualnej, ale nie w środowisku lokalnym, sprawdź trasy.
* Czy próbujesz użyć bramy współistnienia, która obsługuje oba punkty do lokacji i ExpressRoute? Bramy współistnienia nie są obsługiwane z integracją sieci wirtualnej 

Debugowanie problemów z siecią jest wyzwaniem, ponieważ nie można zobaczyć, co blokuje dostęp do określonego hosta: kombinacja portów. Niektóre z tych przyczyn obejmują:

* na hoście istnieje Zapora uniemożliwiająca dostęp do portu aplikacji z zakresu adresów IP punktu do lokacji. Przekroczenia podsieci często wymagają dostępu publicznego.
* host docelowy nie działa
* Aplikacja nie działa
* masz nieprawidłowy adres IP lub nazwa hosta
* aplikacja nasłuchuje na innym porcie niż oczekiwano. Identyfikator procesu można dopasować do portu nasłuchiwania przy użyciu polecenia "netstat-Aon" na hoście punktu końcowego. 
* sieciowe grupy zabezpieczeń są skonfigurowane w taki sposób, że uniemożliwiają dostęp do hosta i portu aplikacji z zakresu adresów IP punktu do lokacji

Należy pamiętać, że nie wiesz, jaki jest adres używany przez aplikację. Może to być dowolny adres w podsieci integracji lub zakres adresów punktu do lokacji, dlatego należy zezwolić na dostęp z całego zakresu adresów. 

Dodatkowe kroki debugowania obejmują:

* Nawiąż połączenie z maszyną wirtualną w sieci wirtualnej i spróbuj skontaktować się z hostem zasobów w tym miejscu: port. Aby przetestować dostęp do protokołu TCP, użyj polecenia programu PowerShell **test-NetConnection**. Składnia jest następująca:

      test-netconnection hostname [optional: -Port]

* Wywołaj aplikację na maszynie wirtualnej i przetestuj dostęp do tego hosta i portu z konsoli programu z poziomu aplikacji przy użyciu usługi **tcpping**

#### <a name="on-premises-resources"></a>Zasoby lokalne ####

Jeśli aplikacja nie może nawiązać połączenia z lokalnym zasobem, sprawdź, czy możesz uzyskać dostęp do zasobu z sieci wirtualnej. Użyj polecenia **test-NetConnection** PowerShell, aby sprawdzić dostęp do protokołu TCP. Jeśli maszyna wirtualna nie może nawiązać połączenia z zasobem lokalnym, połączenie sieci VPN lub ExpressRoute może być niepoprawnie skonfigurowane.

Jeśli hostowana maszyna wirtualna może nawiązać połączenie z systemem lokalnym, ale aplikacja nie może, przyczyna jest prawdopodobnie jedną z następujących przyczyn:

* trasy nie są skonfigurowane z podsiecią lub wskazują zakresy adresów lokacji w bramie lokalnej
* sieciowe grupy zabezpieczeń blokują dostęp do zakresu adresów IP punkt-lokacja
* Zapora lokalna blokuje ruch z zakresu adresów IP punkt-lokacja
* próbujesz nawiązać połączenie z adresem innym niż RFC 1918 przy użyciu funkcji integracji regionalnej sieci wirtualnej


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
[4]: ./media/web-sites-integrate-with-vnet/vnetint-appsetting.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
