---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 03/18/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a89a5d753eaa241b11eb4c7eed9500c9715d405d
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150788"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Jaka jest różnica między bramą sieci wirtualnej platformy Azure (VPN Gateway) i Azure bramą VPN Gateway wirtualnej sieci WAN platformy Azure?

Wirtualna sieć WAN zapewnia łączność typu lokacja-lokacja w dużej skali oraz oferuje przepływność, skalowalność i łatwość użycia. Usługa ExpressRoute i funkcje łączności punkt-lokacja są obecnie dostępne w wersji zapoznawczej. CPE gałęzi autoprovision urządzeń i połączenie z sieci platformy Azure z wirtualnych WAN. Te urządzenia są dostępne w rozwijającym się ekosystemie partnerów sieci SD-WAN i VPN. Zobacz [listę preferowanych partnerów](https://go.microsoft.com/fwlink/p/?linkid=2019615).

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>Którzy dostawcy urządzeń (partnerzy wirtualnej sieci WAN) są obsługiwani w czasie wprowadzenia oferty?

Obecnie wielu partnerów obsługuje w pełni zautomatyzowane środowisko usługi Virtual WAN. Aby uzyskać więcej informacji, zobacz [Virtual WAN partners (Partnerzy wirtualnej sieci WAN)](https://go.microsoft.com/fwlink/p/?linkid=2019615). 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Jakie są kroki automatyzacji dla partnerów usługi Virtual WAN?

Aby uzyskać informacje o krokach automatyzacji dla partnerów, zobacz [Virtual WAN partner automation](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) (Automatyzacja dla partnerów usługi Virtual WAN).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Czy muszę korzystać z urządzenia preferowanego przez partnera?

Nie. Możesz używać dowolnego urządzenia obsługującego sieć VPN, które spełnia wymagania platformy Azure pod kątem obsługi protokołów IKEv2/IKEv1 IPsec.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Jak partnerzy wirtualnej sieci WAN automatyzują połączenie z wirtualną siecią WAN platformy Azure?

Zdefiniowane programowo rozwiązania w zakresie łączności zwykle umożliwiają zarządzanie urządzeniami oddziału przy użyciu kontrolera lub centrum aprowizacji urządzeń. Kontroler może zautomatyzować połączenie z wirtualną siecią WAN platformy Azure za pomocą interfejsów API platformy Azure. Aby uzyskać więcej informacji, zobacz artykuł dotyczący automatyzacji dla partnerów wirtualnej sieci WAN.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Czy wirtualna sieć WAN zmienia istniejące funkcje łączności?   

Nie ma żadnych zmian istniejących funkcji łączności platformy Azure.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Czy są dostępne nowe zasoby usługi Resource Manager dla wirtualnej sieci WAN?
  
Tak. Wraz z wirtualną siecią WAN wprowadzono nowe zasoby usługi Resource Manager. Aby uzyskać więcej informacji, zobacz [Omówienie](https://go.microsoft.com/fwlink/p/?LinkId=2004389).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Ile urządzeń sieci VPN można połączyć z jednym koncentratorem?

Obsługiwanych jest do 1000 połączeń na koncentrator wirtualny. Każde połączenie obejmuje dwa tunele w konfiguracji aktywne-aktywne. Tunele kończą się w bramie VPN Gateway koncentratora wirtualnego platformy Azure.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Czy lokalne urządzenie sieci VPN może łączyć się z wieloma koncentratorami?

Tak. Przepływ ruchu po rozpoczęciu będzie odbywał się od urządzenia lokalnego do najbliższego urządzenia brzegowego firmy Microsoft, a następnie do koncentratora wirtualnego.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Czy wirtualna sieć WAN platformy Azure obsługuje globalne wirtualne sieci równorzędne? 

 Nie.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>Czy sieci wirtualne będące szprychami połączone z koncentratorem wirtualnym mogą komunikować się ze sobą?

Tak. Sieci wirtualne będące szprychami mogą komunikować się bezpośrednio za pomocą komunikacji równorzędnej sieci wirtualnej. Nie obsługujemy jednak sieci wirtualnych komunikujących się przechodnio za pośrednictwem centrum. Aby uzyskać więcej informacji, zobacz [Wirtualne sieci równorzędne](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Czy mogę wdrożyć moje ulubione wirtualne urządzenie sieciowe (w sieci wirtualnej wirtualnego urządzenia sieciowego) i używać go za pomocą wirtualnej sieci WAN platformy Azure?

Tak, można połączyć swoje ulubione wirtualne urządzenie sieciowe (WUS) sieci wirtualnej z usługą Azure Virtual WAN. Najpierw połącz wirtualne urządzenie sieciowe sieci wirtualnej za pomocą połączenia sieci wirtualnej koncentratora. Następnie utwórz trasę koncentratora wirtualnego z kolejnym przeskokiem wskazującym urządzenie wirtualne. Do tabeli tras koncentratora wirtualnego można zastosować wiele tras. Wszystkie szprychy połączone z siecią wirtualną wirtualnego urządzenia sieciowego muszą być dodatkowo połączone z koncentratorem wirtualnym, aby zapewnić propagowanie tras sieci wirtualnych będących szprychami do systemów lokalnych.

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>Czy sieć wirtualna wirtualnego urządzenia sieciowego może zawierać bramę sieci wirtualnej?

Nie. Sieć wirtualna wirtualnego urządzenia sieciowego nie może mieć bramy sieci wirtualnej, jeśli jest połączona z koncentratorem wirtualnym. 

### <a name="is-there-support-for-bgp"></a>Czy jest obsługiwany protokół BGP?

Tak. Protokół BGP jest obsługiwany. Podczas tworzenia witryny sieci VPN, należy podać parametry protokołu BGP w nim. To oznacza, że wszystkie połączenia utworzone na platformie Azure dla tej lokacji mają zostać włączone dla protokołu BGP. Ponadto jeśli masz sieć wirtualną z wirtualnego urządzenia Sieciowego, a ta sieć wirtualna urządzenia WUS został dołączony do koncentratora, wirtualne sieci WAN, w celu zapewnienia, że trasy z sieci wirtualnej urządzenie WUS są anonsowane odpowiednio szprychy, które są dołączone do urządzenia NVA w sieci wirtualnej, należy wyłączyć protokołu BGP. Ponadto nawiązać te satelity, sieciami wirtualnymi w wirtualnych piastą zapewnienie trasy sieci wirtualnej szprychy są propagowane w systemach lokalnych.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Czy można kierować ruchem przy użyciu trasy zdefiniowanej przez użytkownika w koncentratorze wirtualnym?

Tak, można kierować ruch do sieci wirtualnej za pomocą tabeli tras koncentratora wirtualnego.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Czy są dostępne informacje na temat licencjonowania i cen wirtualnej sieci WAN?
 
Tak. Zobacz stronę z [cennikiem](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-i-calculate-price-of-a-hub"></a>Sposób obliczania cen koncentrator?
 
Zapłacisz dla usługi w Centrum. Na przykład 10 gałęzi lub urządzeń lokalnych, które wymaga, aby nawiązać połączenie WAN wirtualnych Azure oznaczałoby nawiązywania połączenia z punktów końcowych sieci VPN w piaście. Umożliwia powiedzieć, to jest sieć VPN jednostki skalowania 1 = 500 MB/s kosztuje $0.361/ godz. Każde połączenie jest rozliczana według 0,08 USD/godz. Połączenia o szybkości 10, Łączna opłata za usługę/godz. będzie 0.361 $ + $. 8 / godz. Dane dla ruchu opuszczającego Azure opłaty. 

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>W jaki sposób partnerzy, którzy nie są wymienieni na liście partnerów uruchomienia, mogą dołączyć do programu?

Wyślij wiadomość e-mail na adres azurevirtualwan@microsoft.com. Idealny partner powinien dysponować urządzeniem, które można aprowizować na potrzeby połączeń IKEv1 lub IKEv2 IPSec.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Co zrobić, jeśli używam urządzenia i nie znajduje się w wirtualnej sieci WAN lista partnerów? Czy mogę nadal służy do nawiązać połączenie wirtualnej sieci WAN sieci VPN platformy Azure?

Tak, jak długo urządzenie obsługuje protokół IPsec IKEv1 lub IKEv2. Wirtualne sieci WAN partnerów zautomatyzować łączności z urządzenia do punktów końcowych sieci VPN platformy Azure. Oznacza to, automatyzację kroków, takich jak "przekazywanie informacji o gałęzi", "IPsec i konfiguracja" i "łączność". Ponieważ urządzenie nie jest z ekosystemu partnera zawierającego wirtualne sieci WAN, konieczne będzie pozostaw trudne zadania ręcznie biorąc konfiguracji platformy Azure i aktualizowania urządzenia do skonfigurowania połączenia IPsec. 

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Czy istnieje możliwość skonstruowania sieci usługi Azure Virtual WAN przy użyciu szablonu usługi Resource Manager?

Prostej konfiguracji jednej wirtualnej sieci WAN, z jednym Centrum i jeden vpnsite mogą być tworzone przy użyciu [szybkiego startu platformy Azure szablonu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). Usługa Virtual WAN jest głównie obsługiwana przez technologię REST lub witrynę Portal.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Czy łączność między oddziałami jest dozwolona w usłudze Virtual WAN?

Tak. Łączność między oddziałami jest dostępna w usłudze Virtual WAN dla sieci VPN i od sieci VPN do usługi ExpressRoute. Połączenie sieci VPN między lokacjami jest ogólnie dostępne, natomiast usługa ExpressRoute i połączenie punkt-lokacja są obecnie dostępne w wersji zapoznawczej.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Czy ruch zachodzący między oddziałami przechodzi przez usługę Azure Virtual WAN?

Tak.

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>Czym różni się usługa Virtual WAN od istniejącej bramy sieci wirtualnej platformy Azure?

Sieć VPN bramy sieci wirtualnej jest ograniczona do 30 tuneli. W przypadku połączeń należy używać usługi Virtual WAN dla sieci VPN na dużą skalę. W koncentratorze można połączyć maksymalnie 1000 połączeń oddziałów z prędkością 2 Gb/s we wszystkich regionach z wyjątkiem regionu Zachodnio-środkowe stany USA. W regionie Zachodnio-środkowe stany USA dostępna jest szybkość 20 Gb/s. W przyszłości będziemy wprowadzać standard 20 Gb/s w dodatkowych regionach. Połączenie to tunel typu aktywny-aktywny od lokalnego urządzenia sieci VPN do koncentratora wirtualnego. Możesz mieć jeden koncentrator na region, co oznacza, że możesz połączyć więcej niż 1000 oddziałów w koncentratorach.

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Jak jest wirtualne sieci WAN obsługi SD-WAN urządzeń?

Wirtualne sieci WAN partnerów zautomatyzować połączenia protokołu IPsec do punktów końcowych sieci VPN platformy Azure. Jeśli wirtualne sieci WAN partnera jest dostawcą SD-WAN jest implikowane, że kontroler SD-WAN zarządza automatyzacji i połączenia protokołu IPsec do punktów końcowych sieci VPN platformy Azure. Jeśli urządzenie SD-WAN wymaga własnej punktu końcowego, zamiast sieci VPN platformy Azure dla dowolnego własnościowych funkcji SD-WAN, można wdrożyć punkt końcowy SD-WAN w sieci wirtualnej platformy Azure i współistnieć przy użyciu Azure wirtualnego WAN.

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>Czy usługa Virtual WAN wymaga usługi ExpressRoute w każdej lokacji?

Nie. Usługa Virtual WAN nie wymaga usługi ExpressRoute w każdej lokacji. Używa ona standardowej łączności IPsec typu lokacja-lokacja z użyciem linków internetowych z urządzenia do piasty usługi Azure Virtual WAN. Lokacje mogą być połączone z siecią dostawcy przy użyciu obwodu usługi ExpressRoute. Lokacje, które są połączone przy użyciu usługi ExpressRoute w koncentratorze wirtualnym (w wersji zapoznawczej), mogą mieć przepływ ruchu typu oddział-oddział między siecią VPN i usługą ExpressRoute. 

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Czy istnieje limit przepływności sieci podczas korzystania z usługi Azure Virtual WAN?

Liczba oddziałów jest ograniczona do 1000 połączeń na koncentrator/region, a łączna szybkość do 2 Gb/s w koncentratorze. Wyjątek stanowi region Zachodnio-środkowe stany USA, w którym łączna szybkość wynosi 20 Gb/s. W przyszłości będziemy wprowadzać standard 20 Gb/s w innych regionach.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Czy usługa Virtual WAN umożliwia urządzeniu lokalnemu równoległe korzystanie z wielu usługodawców internetowych, czy jest to zawsze pojedynczy tunel sieci VPN?

Połączenia do wirtualnej sieci VPN w sieci WAN jest zawsze tunel typu aktywne aktywne (w przypadku odporność w obrębie tego samego Centrum/regionu) przy użyciu łącza, które są dostępne w gałęzi. Ten link może być link usługodawcy internetowego w gałęzi w środowisku lokalnym. Wirtualna sieć WAN nie zapewnia jakąkolwiek logikę specjalną, aby skonfigurować wiele usługodawcy internetowego w sposób równoległy; Zarządzanie trybu failover między usługodawcy internetowego w gałęzi jest całkowicie operacji sieciowych skoncentrowane na gałęzi. Za pomocą ulubionego rozwiązania SD-WAN celu wybór ścieżki w gałęzi.

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>W jaki sposób ruch jest kierowany w sieci szkieletowej platformy Azure?

Ruch jest zgodny ze wzorcem: gałąź urządzenia -> usługodawca Internetowy -> Microsoft edge -> Microsoft DC (piastą) -> Microsoft edge -> usługodawca Internetowy -> urządzenia gałęzi

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Które elementy tego modelu muszą znajdować się w każdej lokacji? Czy wystarczy tylko połączenie internetowe?

Tak. Połączenie z Internetem i urządzenia fizycznego, obsługujący protokół IPsec, najlepiej z naszych zintegrowanych [partnerów](https://go.microsoft.com/fwlink/p/?linkid=2019615). Opcjonalnie można ręcznie zarządzać konfiguracją i łącznością z platformą Azure z poziomu preferowanego urządzenia.
