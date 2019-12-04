---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/17/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: acd601512f2240802de47483a015f03a226da115
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795880"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Jaka jest różnica między bramą sieci wirtualnej platformy Azure (VPN Gateway) i bramą sieci VPN Azure Virtual WAN?

Wirtualna sieć WAN zapewnia łączność typu lokacja-lokacja w dużej skali oraz oferuje przepływność, skalowalność i łatwość użycia. Połączenie lokacji z bramą sieci VPN wirtualnej sieci WAN różni się od zwykłej bramy sieci wirtualnej korzystającej z typu bramy "VPN". Podobnie w przypadku łączenia obwodu usługi ExpressRoute z wirtualnym koncentratorem sieci WAN w przypadku bramy usługi ExpressRoute jest stosowany inny zasób niż brama usługi Virtual Network, która używa bramy typu "ExpressRoute". Wirtualna sieć WAN obsługuje maksymalnie 20 GB/s zagregowaną przepływność w przypadku sieci VPN i ExpressRoute. Wirtualna sieć WAN również ma automatyzację łączności z ekosystemem partnerów urządzeń gałęziowych CPE. Urządzenia rozgałęzienia CPE mają wbudowaną funkcję automatyzacji, która automatycznie inicjuje i nawiązuje połączenie z wirtualną siecią WAN platformy Azure. Te urządzenia są dostępne w rozwijającym się ekosystemie partnerów sieci SD-WAN i VPN. Zobacz [listę preferowanych partnerów](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Jak różnią się wirtualne sieci WAN od bramy sieci wirtualnej platformy Azure?

Sieć VPN bramy sieci wirtualnej jest ograniczona do 30 tuneli. W przypadku połączeń należy używać usługi Virtual WAN dla sieci VPN na dużą skalę. Można połączyć maksymalnie 1 000 połączeń gałęzi na region (koncentrator wirtualny) z zagregowaną ilością 20 GB/s na koncentrator. Połączenie to tunel typu aktywny-aktywny od lokalnego urządzenia sieci VPN do koncentratora wirtualnego. Można mieć jeden koncentrator na region, co oznacza, że można łączyć więcej niż 1 000 gałęzi w centrach.

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Którzy dostawcy urządzeń (wirtualne partnerzy sieci WAN) są obsługiwani?

Obecnie wielu partnerów obsługuje w pełni zautomatyzowane środowisko usługi Virtual WAN. Aby uzyskać więcej informacji, zobacz [Virtual WAN partners (Partnerzy wirtualnej sieci WAN)](../articles/virtual-wan/virtual-wan-locations-partners.md). 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Jakie są kroki automatyzacji dla partnerów usługi Virtual WAN?

Aby uzyskać informacje o krokach automatyzacji dla partnerów, zobacz [Virtual WAN partner automation](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) (Automatyzacja dla partnerów usługi Virtual WAN).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Czy muszę korzystać z urządzenia preferowanego przez partnera?

Nie. Możesz używać dowolnego urządzenia obsługującego sieć VPN, które spełnia wymagania platformy Azure pod kątem obsługi protokołów IKEv2/IKEv1 IPsec.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Jak partnerzy wirtualnej sieci WAN automatyzują połączenie z wirtualną siecią WAN platformy Azure?

Zdefiniowane programowo rozwiązania w zakresie łączności zwykle umożliwiają zarządzanie urządzeniami oddziału przy użyciu kontrolera lub centrum aprowizacji urządzeń. Kontroler może zautomatyzować połączenie z wirtualną siecią WAN platformy Azure za pomocą interfejsów API platformy Azure. Automatyzacja obejmuje przekazywanie informacji o gałęzi, pobranie konfiguracji platformy Azure, skonfigurowanie tuneli IPSec do centrów wirtualnych platformy Azure i automatyczne skonfigurowanie połączenia w usłudze Azure Virtual WAN. Jeśli masz setki gałęzi, nawiązywanie połączenia przy użyciu wirtualnych partnerów sieci WAN CPE jest proste, ponieważ środowisko dołączania zajmuje konieczność konfigurowania i konfigurowania łączności z dużą skalą oraz zarządzania nią. Aby uzyskać więcej informacji, zobacz [Virtual WAN partner automation (Automatyzacja dla partnerów usługi Virtual WAN)](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Czy muszę korzystać z urządzenia preferowanego przez partnera?

Nie. Możesz używać dowolnego urządzenia obsługującego sieć VPN, które spełnia wymagania platformy Azure pod kątem obsługi protokołów IKEv2/IKEv1 IPsec.

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Jak wirtualna sieć WAN obsługuje urządzenia SD-WAN?

Wirtualne partnerzy sieci WAN automatyzują połączenia IPsec z punktami końcowymi sieci VPN platformy Azure. Jeśli wirtualny partner sieci WAN jest dostawcą SD-WAN, należy zastanowić się, że kontroler SD-WAN zarządza automatyzacją i łącznością IPsec z punktami końcowymi sieci VPN platformy Azure. Jeśli urządzenie SD-WAN wymaga własnego punktu końcowego zamiast sieci VPN platformy Azure dla dowolnej funkcjonalnej funkcjonalności SD-WAN, można wdrożyć punkt końcowy SD-WAN w sieci wirtualnej platformy Azure i współistnieć z wirtualną siecią WAN platformy Azure.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Czy wirtualna sieć WAN zmienia istniejące funkcje łączności?

Nie ma żadnych zmian istniejących funkcji łączności platformy Azure.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Czy są dostępne nowe zasoby usługi Resource Manager dla wirtualnej sieci WAN?
  
Tak. Wraz z wirtualną siecią WAN wprowadzono nowe zasoby usługi Resource Manager. Aby uzyskać więcej informacji, zobacz [Omówienie](../articles/virtual-wan/virtual-wan-about.md).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Ile urządzeń sieci VPN może się połączyć z jednym koncentratorem?

Do 1 000 połączeń są obsługiwane na koncentratorze wirtualnym. Każde połączenie składa się z czterech linków, a każde połączenie linku obsługuje dwa tunele, które znajdują się w konfiguracji aktywne-aktywne. Tunele kończą się w bramy vpngateway wirtualnego centrum platformy Azure.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Czy lokalne urządzenie sieci VPN może łączyć się z wieloma koncentratorami?

Tak. Przepływ ruchu, po rozpoczęciu, pochodzi z urządzenia lokalnego do najbliższej krawędzi sieci firmy Microsoft, a następnie do koncentratora wirtualnego.

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Czy mogę wdrożyć moje ulubione wirtualne urządzenie sieciowe (w sieci wirtualnej wirtualnego urządzenia sieciowego) i używać go za pomocą wirtualnej sieci WAN platformy Azure?

Tak, można połączyć swoje ulubione wirtualne urządzenie sieciowe (WUS) sieci wirtualnej z usługą Azure Virtual WAN. Najpierw połącz wirtualne urządzenie sieciowe sieci wirtualnej za pomocą połączenia sieci wirtualnej koncentratora. Następnie utwórz trasę koncentratora wirtualnego z następnym przeskokiem wskazującym na urządzenie wirtualne. Do tabeli tras koncentratora wirtualnego można zastosować wiele tras. Wszystkie szprychy połączone z siecią wirtualną wirtualnego urządzenia sieciowego muszą być dodatkowo połączone z koncentratorem wirtualnym, aby zapewnić propagowanie tras sieci wirtualnych będących szprychami do systemów lokalnych.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Czy można utworzyć sieciowe urządzenie wirtualne w koncentratorze wirtualnym?

Nie można wdrożyć sieciowego urządzenia wirtualnego (urządzenie WUS) wewnątrz koncentratora wirtualnego. Można go jednak utworzyć w sieci wirtualnej szprych, która jest połączona z koncentratorem wirtualnym i umożliwić trasie w koncentratorze kierowanie ruchu dla docelowej sieci wirtualnej za pośrednictwem adresu IP urządzenie WUS (karty sieciowej).

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Czy sieć wirtualna szprych ma bramę sieci wirtualnej?

Nie. Sieć wirtualna szprych nie może mieć bramy sieci wirtualnej, jeśli jest ona podłączona do koncentratora wirtualnego.

### <a name="is-there-support-for-bgp"></a>Czy jest obsługiwany protokół BGP?

Tak. Protokół BGP jest obsługiwany. Podczas tworzenia witryny sieci VPN można podać w niej parametry protokołu BGP. Oznacza to, że dla wszystkich połączeń utworzonych na platformie Azure dla tej lokacji zostanie włączone protokół BGP. Ponadto w przypadku sieci wirtualnej z usługą urządzenie WUS i jeśli sieć wirtualna urządzenie WUS była podłączona do koncentratora sieci WAN, w celu zapewnienia, że trasy z sieci wirtualnej urządzenie WUS są anonsowane odpowiednio, szprychy dołączone do sieci wirtualnej urządzenie WUS muszą wyłączyć protokół BGP. Ponadto Połącz te szprychy sieci wirtualnych z siecią wirtualną koncentratora wirtualnego, aby upewnić się, że trasy sieci wirtualnej szprych są propagowane do systemów lokalnych.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Czy można kierować ruchem przy użyciu trasy zdefiniowanej przez użytkownika w koncentratorze wirtualnym?

Tak. możesz kierować ruch do sieci wirtualnej przy użyciu tabeli tras koncentratora wirtualnego. Dzięki temu można ustawić trasy dla sieci wirtualnych docelowego na platformie Azure przy użyciu określonego adresu IP (zazwyczaj karty sieciowej urządzenie WUS).

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Czy są dostępne informacje na temat licencjonowania i cen wirtualnej sieci WAN?

Tak. Zobacz stronę z [cennikiem](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-i-calculate-price-of-a-hub"></a>Jak mogę obliczyć ceny centrum?

* Płacisz za usługi w centrum. Załóżmy na przykład, że masz 10 gałęzi lub urządzeń lokalnych, które nie wymagają połączenia z wirtualną siecią WAN platformy Azure, to oznacza połączenie z punktami końcowymi sieci VPN w centrum. Można to powiedzieć, że jest to sieć VPN z 1 jednostką skalowania = 500 MB/s, przy czym opłata jest naliczana za $0.361/HR. Każde połączenie jest obciążane stawką $0,05/HR. W przypadku 10 połączeń łączna opłata za usługę/HR byłaby $0,361 + $. 5/HR. Opłaty za dane dotyczące ruchu wychodzącego z platformy Azure.

* Dostępna jest dodatkowa opłata za centrum. Zobacz stronę z [cennikiem](https://azure.microsoft.com/pricing/details/virtual-wan/).

* Jeśli usługa ExpressRoute Gateway została połączona z powodu obwodów usługi ExpressRoute łączących się z koncentratorem wirtualnym, płacisz za cenę jednostki skalowania. Każda jednostka skalowania w usłudze ER to 2 GB/s, a każda jednostka połączenia jest rozliczana według stawki za jednostkę połączenia sieci VPN.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>W jaki sposób partnerzy, którzy nie są wymienieni na liście partnerów uruchomienia, mogą dołączyć do programu?

Wszystkie wirtualne interfejsy API sieci WAN są otwarte. Możesz przejść do dokumentacji, aby ocenić wykonalność techniczną. Jeśli masz jakieś pytania, Wyślij wiadomość e-mail do azurevirtualwan@microsoft.com. Idealny partner powinien dysponować urządzeniem, które można aprowizować na potrzeby połączeń IKEv1 lub IKEv2 IPSec.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Co zrobić, jeśli urządzenie, którego używam, nie znajduje się na liście wirtualnych partnerów sieci WAN? Czy nadal mogę używać go do nawiązywania połączenia z wirtualną siecią WAN platformy Azure?

Tak długo, jak urządzenie obsługuje protokół IPsec IKEv1 lub IKEv2. Wirtualne partnerzy sieci WAN automatyzują łączność z urządzeniem z punktami końcowymi sieci VPN platformy Azure. Oznacza to automatyzację kroków, takich jak "przekazywanie informacji o rozgałęzieniu", "IPsec i konfiguracja" i "łączność". Ponieważ urządzenie nie pochodzi z ekosystemu wirtualnego partnera sieci WAN, należy przeprowadzić duże podnoszenie poziomu ręcznie pobierając konfigurację platformy Azure i zaktualizować urządzenie w celu skonfigurowania połączenia IPsec.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Czy istnieje możliwość skonstruowania sieci usługi Azure Virtual WAN przy użyciu szablonu usługi Resource Manager?

Prostą konfigurację jednej wirtualnej sieci WAN z jednym koncentratorem i jedną vpnsite można utworzyć przy użyciu [szablonu szybkiego startu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). Wirtualna sieć WAN jest głównie usługą opartą na usłudze REST lub portalu.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Czy wirtualna sieć WAN platformy Azure obsługuje globalne wirtualne sieci równorzędne? 

Możesz połączyć sieć wirtualną w innym regionie niż wirtualna sieć WAN.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>Czy sieci wirtualne będące szprychami połączone z koncentratorem wirtualnym mogą komunikować się ze sobą?

Tak. Sieci wirtualne będące szprychami mogą komunikować się bezpośrednio za pomocą komunikacji równorzędnej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Wirtualne sieci równorzędne](../articles/virtual-network/virtual-network-peering-overview.md). Obsługujemy również sieci wirtualnychą komunikację przechodnią przez centrum.

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Co to jest połączenie gałęzi z wirtualną siecią WAN platformy Azure?

Połączenie z urządzenia rozgałęzienia z wirtualną siecią WAN platformy Azure obsługuje maksymalnie cztery linki. Łącze jest łączem łączności fizycznej w lokalizacji gałęzi (na przykład: ATT, Verizon itp.). Każde połączenie linku składa się z dwóch aktywnych/aktywnych tuneli IPsec.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Czy łączność między oddziałami jest dozwolona w usłudze Virtual WAN?

Tak. Łączność między oddziałami jest dostępna w usłudze Virtual WAN dla sieci VPN i od sieci VPN do usługi ExpressRoute.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Czy ruch rozgałęzienia jest przechodzący przez wirtualną sieć WAN platformy Azure?

Tak.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Czy wirtualne sieci WAN wymagają ExpressRoute z każdej lokacji?

Nie. Usługa Virtual WAN nie wymaga usługi ExpressRoute w każdej lokacji. Używa on standardowych połączeń IPsec lokacja-lokacja za pośrednictwem linków internetowych z urządzenia do wirtualnego centrum sieci WAN platformy Azure. Lokacje mogą być połączone z siecią dostawcy przy użyciu obwodu usługi ExpressRoute. W przypadku lokacji, które są połączone za pomocą ExpressRoute w koncentratorze wirtualnym, lokacje mogą mieć gałąź do przepływu ruchu rozgałęzienia między sieciami VPN i ExpressRoute.

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Czy istnieje limit przepływności sieci podczas korzystania z usługi Azure Virtual WAN?

Liczba oddziałów jest ograniczona do 1000 połączeń na Hub/region, a łączna 20 GB/s w centrum. Możesz mieć 1 Hub na region.

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>Ile połączeń sieci VPN jest obsługiwana przez wirtualną koncentrator sieci WAN?

Wirtualne Centrum sieci WAN platformy Azure może obsługiwać maksymalnie 1 000 połączeń S2S 10 000, połączeń P2S oraz połączeń 4 ExpressRoute jednocześnie.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Jaka jest całkowita przepustowość sieci VPN tunelu VPN i połączenia?

Całkowita przepustowość sieci VPN koncentratora to 20 GB/s w oparciu o wybraną jednostkę skalowania. Przepływność jest współdzielona przez wszystkie istniejące połączenia. Każdy tunel w połączeniu może obsługiwać do 1 GB/s.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-the-portal-how-do-i-configure-that"></a>Nie widzę ustawienia 20 GB/s dla koncentratora wirtualnego w portalu. Jak mogę skonfigurować?

Przejdź do bramy sieci VPN w centrum w portalu i kliknij jednostkę skalowania, aby zmienić to ustawienie na odpowiednie.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Czy wirtualne sieci WAN zezwalają na urządzenie lokalne w celu użycia wielu usługodawców równoległych, czy też jest to zawsze pojedynczy tunel VPN?

Połączenie z wirtualną siecią VPN sieci WAN zawsze jest tunelem aktywnym (w celu odporności w obrębie tego samego centrum/regionu) przy użyciu linku dostępnego w rozgałęzieniu. Ten link może być linkiem usługodawcy internetowego w gałęzi lokalnej. Wirtualna sieć WAN "VPNSite" umożliwia dodawanie informacji o linku do lokacji. Jeśli masz wielu usługodawców internetowych w oddziale i każdy usługodawca internetowy dostarczył link, te informacje można skonfigurować w informacjach o witrynie sieci VPN na platformie Azure. Jednak zarządzanie trybem failover między usługodawcami internetowymi w gałęzi jest całkowicie operacją routingu skoncentrowanego na gałęzi.

### <a name="what-is-global-transit-architecture"></a>Co to jest globalna architektura tranzytowa?

Aby uzyskać informacje na temat globalnej architektury tranzytowej, zobacz [Globalna architektura sieci tranzytowej i wirtualna sieć WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>W jaki sposób ruch jest kierowany w sieci szkieletowej platformy Azure?

Ruch jest zgodny ze wzorcem: rozgałęzienie Device-> usługodawca internetowy — > Microsoft Network Edge-> Microsoft DC (Hub VNet) — > Microsoft Network Edge-> usługodawca internetowy-> rozgałęzienie

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Które elementy tego modelu muszą znajdować się w każdej lokacji? Czy wystarczy tylko połączenie internetowe?

Tak. Połączenie internetowe i urządzenie fizyczne obsługujące protokół IPsec najlepiej z naszych zintegrowanych [wirtualnych partnerów sieci WAN](../articles/virtual-wan/virtual-wan-locations-partners.md). Opcjonalnie możesz ręcznie zarządzać konfiguracją i łącznością z platformą Azure z poziomu preferowanego urządzenia.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Jak mogę włączyć trasy domyślnej (0.0.0.0/0) w połączeniu (VPN, ExpressRoute lub Virtual Network):

Koncentrator wirtualny może propagować rozpoznajenie trasy domyślnej do sieci wirtualnej/połączenia sieci VPN typu lokacja-lokacja, jeśli flaga jest włączona w połączeniu. Ta flaga jest widoczna, gdy użytkownik edytuje połączenie sieci wirtualnej, połączenie sieci VPN lub połączenie ExpressRoute. Domyślnie ta flaga jest wyłączona, gdy lokacja lub obwód ExpressRoute jest podłączony do centrum. Jest ona domyślnie włączona po dodaniu połączenia sieci wirtualnej w celu podłączenia sieci wirtualnej do koncentratora wirtualnego. Trasa domyślna nie pochodzi z wirtualnego koncentratora sieci WAN; Trasa domyślna jest propagowana, jeśli jest już wykorzystana przez koncentrator wirtualnych sieci WAN w wyniku wdrożenia zapory w centrum lub jeśli w innej połączonej lokacji włączono tunelowanie wymuszone.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Jakie są różnice między wirtualnymi typami sieci WAN (podstawowa i standardowa)?

Typ sieci WAN "Basic" umożliwia utworzenie podstawowego centrum (SKU = Basic). Typ sieci WAN "Standardowa" umożliwia tworzenie centrum standardowego (SKU = standard). Podstawowe centra są ograniczone do funkcjonalności sieci VPN typu lokacja-lokacja. Standardowe centra pozwalają na ExpressRoute, Sieć VPN użytkownika (P2S), koncentrator pełnych sieci i tranzyt między sieciami wirtualnymi za pośrednictwem centrów. W przypadku centrów standardowych i opłaty za przetwarzanie danych na potrzeby przesyłania przez centra podczas łączności między sieciami wirtualnymi jest naliczana Podstawowa opłata za usługę $0,25/HR. Aby uzyskać więcej informacji, zobacz [podstawowe i standardowe wirtualne sieci WAN](../articles/virtual-wan/virtual-wan-about.md#basicstandard). Aby uzyskać cennik, zobacz stronę z [cennikiem](https://azure.microsoft.com/pricing/details/virtual-wan/) .
