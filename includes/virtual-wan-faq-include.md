---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 08/06/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8a4bbe92cc1b34801abffa1e905d873d4382025a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68912452"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Jaka jest różnica między bramą sieci wirtualnej platformy Azure (VPN Gateway) i Azure bramą VPN Gateway wirtualnej sieci WAN platformy Azure?

Wirtualna sieć WAN zapewnia łączność typu lokacja-lokacja w dużej skali oraz oferuje przepływność, skalowalność i łatwość użycia. ExpressRoute na potrzeby łączności wirtualnej sieci WAN jest obecnie w wersji zapoznawczej. Urządzenia gałęzie CPE inicjują obsługę administracyjną i łączą się z usługą Azure Virtual WAN. Te urządzenia są dostępne w rozwijającym się ekosystemie partnerów sieci SD-WAN i VPN. Zobacz [listę preferowanych partnerów](https://go.microsoft.com/fwlink/p/?linkid=2019615).

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Co to jest połączenie gałęzi z wirtualną siecią WAN platformy Azure?

Połączenie z urządzenia rozgałęzienia z wirtualną siecią WAN platformy Azure, składające się z dwóch aktywnych/aktywnych tuneli IPsec.

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

Do 1 000 połączeń są obsługiwane na koncentratorze wirtualnym. Każde połączenie obejmuje dwa tunele w konfiguracji aktywne-aktywne. Tunele kończą się w bramie VPN Gateway koncentratora wirtualnego platformy Azure.

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

Tak. Protokół BGP jest obsługiwany. Podczas tworzenia witryny sieci VPN można podać w niej parametry protokołu BGP. Oznacza to, że dla wszystkich połączeń utworzonych na platformie Azure dla tej lokacji zostanie włączone protokół BGP. Ponadto w przypadku sieci wirtualnej z usługą urządzenie WUS i jeśli sieć wirtualna urządzenie WUS była podłączona do koncentratora sieci WAN, w celu zapewnienia, że trasy z sieci wirtualnej urządzenie WUS są anonsowane odpowiednio, szprychy dołączone do sieci wirtualnej urządzenie WUS muszą wyłączyć protokół BGP. Ponadto Połącz te szprychy sieci wirtualnych z siecią wirtualną koncentratora wirtualnego, aby upewnić się, że trasy sieci wirtualnej szprych są propagowane do systemów lokalnych.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Czy można kierować ruchem przy użyciu trasy zdefiniowanej przez użytkownika w koncentratorze wirtualnym?

Tak, można kierować ruch do sieci wirtualnej za pomocą tabeli tras koncentratora wirtualnego.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Czy są dostępne informacje na temat licencjonowania i cen wirtualnej sieci WAN?
 
Tak. Zobacz stronę z [cennikiem](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-i-calculate-price-of-a-hub"></a>Jak mogę obliczyć ceny centrum?
 
Płacisz za usługę w centrum. Na przykład 10 gałęzi lub urządzeń lokalnych, które wymagają połączenia z wirtualną siecią WAN platformy Azure, spowoduje nawiązanie połączenia z punktami końcowymi sieci VPN w centrum. Można to powiedzieć, że jest to sieć VPN z 1 jednostką skalowania = 500 MB/s, przy czym opłata jest naliczana za $0.361/HR. Każde połączenie jest obciążane stawką $0,08/HR. W przypadku 10 połączeń łączna opłata za usługę/HR byłaby $0,361 + $. 8/ wysoki. Opłaty za dane dotyczące ruchu wychodzącego z platformy Azure. 

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>W jaki sposób partnerzy, którzy nie są wymienieni na liście partnerów uruchomienia, mogą dołączyć do programu?

Wyślij wiadomość e-mail na adres azurevirtualwan@microsoft.com. Idealny partner powinien dysponować urządzeniem, które można aprowizować na potrzeby połączeń IKEv1 lub IKEv2 IPSec.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Co zrobić, jeśli urządzenie, którego używam, nie znajduje się na liście wirtualnych partnerów sieci WAN? Czy nadal mogę używać go do nawiązywania połączenia z wirtualną siecią WAN platformy Azure?

Tak długo, jak urządzenie obsługuje protokół IPsec IKEv1 lub IKEv2. Wirtualne partnerzy sieci WAN automatyzują łączność z urządzeniem z punktami końcowymi sieci VPN platformy Azure. Oznacza to automatyzację kroków, takich jak "przekazywanie informacji o rozgałęzieniu", "IPsec i konfiguracja" i "łączność". Ponieważ urządzenie nie pochodzi z ekosystemu wirtualnego partnera sieci WAN, należy przeprowadzić duże podnoszenie poziomu ręcznie pobierając konfigurację platformy Azure i zaktualizować urządzenie w celu skonfigurowania połączenia IPsec. 

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Czy istnieje możliwość skonstruowania sieci usługi Azure Virtual WAN przy użyciu szablonu usługi Resource Manager?

Prostą konfigurację jednej wirtualnej sieci WAN z jednym koncentratorem i jednym vpnsite można utworzyć przy użyciu [szablonu szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). Usługa Virtual WAN jest głównie obsługiwana przez technologię REST lub witrynę Portal.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Czy łączność między oddziałami jest dozwolona w usłudze Virtual WAN?

Tak. Łączność między oddziałami jest dostępna w usłudze Virtual WAN dla sieci VPN i od sieci VPN do usługi ExpressRoute. W przypadku sieci VPN lokacja-lokacja jest obecnie dostępna w wersji zapoznawczej ExpressRoute.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Czy ruch rozgałęzienia jest przechodzący przez wirtualną sieć WAN platformy Azure?

Tak.

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>Czym różni się usługa Virtual WAN od istniejącej bramy sieci wirtualnej platformy Azure?

Sieć VPN bramy sieci wirtualnej jest ograniczona do 30 tuneli. W przypadku połączeń należy używać usługi Virtual WAN dla sieci VPN na dużą skalę. Można połączyć maksymalnie 1 000 połączeń gałęzi z 20 GB/s w centrum dla wszystkich regionów. Połączenie to tunel typu aktywny-aktywny od lokalnego urządzenia sieci VPN do koncentratora wirtualnego. Można mieć jeden koncentrator na region, co oznacza, że można łączyć więcej niż 1 000 gałęzi w centrach.

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Jak wirtualna sieć WAN obsługuje urządzenia SD-WAN?

Wirtualne partnerzy sieci WAN automatyzują połączenia IPsec z punktami końcowymi sieci VPN platformy Azure. Jeśli wirtualny partner sieci WAN jest dostawcą SD-WAN, należy zastanowić się, że kontroler SD-WAN zarządza automatyzacją i łącznością IPsec z punktami końcowymi sieci VPN platformy Azure. Jeśli urządzenie SD-WAN wymaga własnego punktu końcowego zamiast sieci VPN platformy Azure dla dowolnej funkcjonalnej funkcjonalności SD-WAN, można wdrożyć punkt końcowy SD-WAN w sieci wirtualnej platformy Azure i współistnieć z wirtualną siecią WAN platformy Azure.

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>Czy usługa Virtual WAN wymaga usługi ExpressRoute w każdej lokacji?

Nie. Usługa Virtual WAN nie wymaga usługi ExpressRoute w każdej lokacji. Używa ona standardowej łączności IPsec typu lokacja-lokacja z użyciem linków internetowych z urządzenia do piasty usługi Azure Virtual WAN. Lokacje mogą być połączone z siecią dostawcy przy użyciu obwodu usługi ExpressRoute. Lokacje, które są połączone przy użyciu usługi ExpressRoute w koncentratorze wirtualnym (w wersji zapoznawczej), mogą mieć przepływ ruchu typu oddział-oddział między siecią VPN i usługą ExpressRoute. 

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Czy istnieje limit przepływności sieci podczas korzystania z usługi Azure Virtual WAN?

Liczba oddziałów jest ograniczona do 1000 połączeń na Hub/region, a łączna 20 GB/s w centrum.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-the-portal-how-do-i-configure-that"></a>Nie widzę ustawienia 20 GB/s dla koncentratora wirtualnego w portalu. Jak mogę skonfigurować?

Obecnie można skonfigurować jednostki skalowania bramy dla 20 GB/s przy użyciu polecenia cmdlet [Update-AzVpnGateway](https://docs.microsoft.com/powershell/module/az.network/update-azvpngateway) . To ustawienie ma dostęp do planu, który jest dostępny w portalu.

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>Ile połączeń sieci VPN jest obsługiwana przez wirtualną koncentrator sieci WAN?

Wirtualne Centrum sieci WAN platformy Azure może obsługiwać maksymalnie 1 000 połączeń S2S i 10 000 połączeń P2S jednocześnie.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Jaka jest całkowita przepustowość sieci VPN tunelu VPN i połączenia?

Całkowita przepustowość sieci VPN koncentratora to 20 GB/s w oparciu o wybraną jednostkę skalowania. Przepływność jest współdzielona przez wszystkie istniejące połączenia.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Czy usługa Virtual WAN umożliwia urządzeniu lokalnemu równoległe korzystanie z wielu usługodawców internetowych, czy jest to zawsze pojedynczy tunel sieci VPN?

Połączenie z siecią VPN sieci wirtualnej sieci WAN zawsze jest tunelem aktywnym (w celu odporności w obrębie tego samego centrum/regionu) przy użyciu linku dostępnego w rozgałęzieniu. Ten link może być linkiem usługodawcy internetowego w gałęzi lokalnej. Wirtualna sieć WAN nie zapewnia żadnej specjalnej logiki umożliwiającej równoczesne skonfigurowanie wielu usługodawców internetowych. zarządzanie trybem failover między usługodawcą internetowym w gałęzi jest całkowicie operacją sieci skoncentrowaną na gałęzi. Możesz użyć ulubionego rozwiązania SD-WAN do wybrania ścieżki w rozgałęzieniu.

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>W jaki sposób ruch jest kierowany w sieci szkieletowej platformy Azure?

Ruch jest zgodny ze wzorcem: rozgałęzienie Device-> usługodawca internetowy — > Microsoft Edge-> > > firmy Microsoft

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Które elementy tego modelu muszą znajdować się w każdej lokacji? Czy wystarczy tylko połączenie internetowe?

Tak. Połączenie internetowe i urządzenie fizyczne obsługujące protokół IPsec najlepiej z naszych zintegrowanych [partnerów](https://go.microsoft.com/fwlink/p/?linkid=2019615). Opcjonalnie można ręcznie zarządzać konfiguracją i łącznością z platformą Azure z poziomu preferowanego urządzenia.
