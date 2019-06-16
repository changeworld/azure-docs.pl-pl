---
title: Sieci platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat funkcji i usług sieci platformy Azure.
services: networking
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2017
ms.author: jdial
ms.openlocfilehash: 02db9f2b8cb2ec71d23ad077b90eeacb905d2a16
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60565858"
---
# <a name="azure-networking"></a>Sieci platformy Azure

System Azure oferuje szeroką gamę funkcji sieciowych, które mogą być używane razem lub oddzielnie. Kliknij dowolną następujące kluczowe funkcje, aby dowiedzieć się więcej o nich:
- [Łączność między zasobami platformy Azure](#connectivity): Połączyć zasoby Azure ze sobą w bezpieczny i prywatnej sieci wirtualnej w chmurze.
- [Łączność z Internetem](#internet-connectivity): Komunikować się z zasobami platformy Azure za pośrednictwem Internetu.
- [Połączeniami lokalnymi](#on-premises-connectivity): Łączenie sieci lokalnej z zasobów platformy Azure za pośrednictwem wirtualnej sieci prywatnej (VPN) za pośrednictwem Internetu lub za pośrednictwem specjalnego połączenia na platformie Azure.
- [Kierunek ruchu i równoważenia obciążenia](#load-balancing): Równoważenie obciążenia ruchu do serwerów w tej samej lokalizacji i bezpośrednie kierowanie ruchu do serwerów w różnych lokalizacjach.
- [Bezpieczeństwo](#security): Filtrowanie ruchu sieciowego między podsieciami sieci lub poszczególnych maszyn wirtualnych (VM).
- [Routing](#routing): Za pomocą domyślny routing lub pełnej kontroli routingu między zasobami platformy Azure i lokalnie.
- [Możliwości zarządzania](#manageability): Monitorowanie i zarządzanie zasobami sieci platformy Azure.
- [Wdrażanie i Konfigurowanie narzędzia](#tools): Wdrażanie i konfigurowanie zasobów sieciowych, należy użyć oparty na sieci web portal lub narzędzi wiersza polecenia dla wielu platform.

## <a name="connectivity"></a>Łączność między zasobami platformy Azure

Zasoby platformy Azure, takie jak maszyny wirtualne, usługi w chmurze, zestawy skalowania maszyn wirtualnych i środowisk Azure App Service Environment może komunikować się prywatnie ze sobą za pośrednictwem usługi Azure Virtual Network (VNet). Sieć wirtualna jest to logiczna izolacja chmury platformy Azure w wersji dedykowanej do Twojej [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fnetworking%2ftoc.json). Można zaimplementować wiele sieci wirtualnych w ramach każdej subskrypcji platformy Azure i Azure [region](https://azure.microsoft.com/regions). Każda sieć wirtualna jest odizolowana od innych sieci wirtualnych. W każdej sieci wirtualnej możesz wykonywać następujące czynności:

- Określenie niestandardowej prywatnej przestrzeni adresowej IP przy użyciu adresów publicznych i prywatnych (RFC 1918). Zasoby platformy Azure przypisuje połączonego z siecią wirtualną prywatny adres IP z przestrzeni adresowej, do których zostanie przypisany.
- Segment sieci wirtualnej do co najmniej jednej podsieci i przydzielić części przestrzeni adresowej sieci wirtualnej w każdej podsieci.
- Korzystanie z rozpoznawania nazw platformy Azure lub określenie własnego serwera DNS do użytku przez zasoby połączone z siecią wirtualną.

Aby dowiedzieć się więcej o usłudze Azure Virtual Network, przeczytaj [Omówienie usługi Virtual network](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu. Łączenie sieci wirtualnych między sobą Włączanie zasobów podłączonej do jednej sieci wirtualnej do komunikowania się ze sobą w ramach sieci wirtualnych. Łączenie sieci wirtualnych między sobą, można użyć jednego lub obu z następujących opcji:

- **Komunikacja równorzędna:** Dzięki zasoby podłączone do różnych sieci wirtualnych platformy Azure w ramach tego samego regionu platformy Azure do komunikowania się ze sobą. Przepustowość i opóźnienie między sieciami wirtualnymi jest taki sam, jakby zasoby były podłączone do tej samej sieci wirtualnej. Aby dowiedzieć się więcej na temat komunikacji równorzędnej, przeczytaj [Omówienie komunikacji równorzędnej sieci wirtualnej](../virtual-network/virtual-network-peering-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
- **Brama sieci VPN:** Dzięki zasoby podłączone do różnych sieci wirtualnych platformy Azure w różnych regionach platformy Azure do komunikowania się ze sobą. Ruch między sieciami wirtualnymi odbywa się za pośrednictwem bramy sieci VPN platformy Azure. Przepustowość między sieciami wirtualnymi jest ograniczona do przepustowości bramy. Aby dowiedzieć się więcej na temat łączenia sieci wirtualnych za pośrednictwem bramy sieci VPN, przeczytaj [Konfigurowanie połączenia sieć wirtualna-sieć wirtualna między regionami](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.

## <a name="internet-connectivity"></a>Łączność z Internetem

Domyślnie wszystkie zasoby platformy Azure, połączone z siecią wirtualną mają łączność wychodząca z Internetem. Prywatny adres IP zasobu jest adres sieci źródłowej translacji (SNAT) na publiczny adres IP w ramach infrastruktury platformy Azure. Aby uzyskać więcej informacji na temat wychodzące połączenie z Internetem, przeczytaj [informacje o połączeniach wychodzących na platformie Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.

Do komunikacji z Internetu dla ruchu przychodzącego z zasobami platformy Azure lub do komunikowania się ruch wychodzący do Internetu bez SNAT, zasób musi posiadać publiczny adres IP. Aby dowiedzieć się więcej na temat publicznych adresów IP, przeczytaj [publiczne adresy IP](../virtual-network/virtual-network-public-ip-address.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.

## <a name="on-premises-connectivity"></a>Łączność lokalna

Dostępne zasoby w sieci wirtualnej bezpiecznie za pośrednictwem połączenia sieci VPN lub bezpośrednie połączenie prywatne. Aby wysyłać ruch sieciowy między siecią wirtualną platformy Azure i siecią lokalną, należy utworzyć bramę sieci wirtualnej. Możesz skonfigurować ustawienia bramy, aby utworzyć typ połączenia, które chcesz, sieci VPN lub usługi ExpressRoute.

Można połączyć sieć lokalną z siecią wirtualną przy użyciu dowolnej kombinacji następujących opcji:

**Punkt lokacja (sieć VPN za pośrednictwem protokołu SSTP)**

Na poniższej ilustracji przedstawiono oddzielnego punktu do lokacji połączenia między wiele komputerów i sieci wirtualnej:

![Punkt-lokacja](./media/networking-overview/point-to-site.png)

To połączenie jest nawiązywane między jednego komputera i sieci wirtualnej. Ten typ połączenia jest świetny, jeśli dopiero rozpoczynasz pracę z platformą Azure. Jest też odpowiedni dla deweloperów, ponieważ wymaga niewielkich zmian w istniejącej sieci lub nie wymaga ich wcale. Jest również wygodne, gdy połączenie jest nawiązywane z lokalizacji zdalnej, np. konferencji lub domu. Połączenia punkt lokacja są często powiązane z połączeniem typu lokacja lokacja przy użyciu tej samej bramy sieci wirtualnej. Połączenie korzysta z protokołu SSTP, zapewnienie szyfrowaną komunikację przez Internet między komputerem a siecią wirtualną. Opóźnienie w sieci VPN punkt lokacja jest nieprzewidywalne, ponieważ ruch przechodzi przez Internet.

**Lokacja do lokacji (tunel VPN protokołu IPsec/IKE)**

![Lokacja-lokacja](./media/networking-overview/site-to-site.png)

To połączenie jest nawiązywane między swoje lokalne urządzenie sieci VPN i bramą sieci VPN platformy Azure. Ten typ połączenia umożliwia dowolnym zasobami lokalnymi, które uzyskają autoryzację dostępu do sieci wirtualnej. Połączenie jest VPN protokołu IPSec/IKE, który zapewnia szyfrowaną komunikację przez Internet między urządzeniem lokalnej i bramy sieci VPN platformy Azure. Możesz połączyć wiele lokacji lokalnych z tą samą bramą sieci VPN. Lokalne urządzenie sieci VPN w każdej lokacji musi mieć zewnętrzny publiczny adres IP, który nie znajduje się za translatorem adresów sieciowych. Czas oczekiwania na połączenie lokacja lokacja jest nieprzewidywalne, ponieważ ruch przechodzi przez Internet.

**ExpressRoute (dedykowane połączenie prywatne)**

![ExpressRoute](./media/networking-overview/expressroute.png)

Ten typ połączenia jest ustanowiona między siecią i platformy Azure, za pośrednictwem partnera usługi ExpressRoute. To połączenie jest prywatne. Ruch nie przechodzi przez Internet. Czas oczekiwania na połączenie usługi ExpressRoute jest przewidywalne, ponieważ ruch nie przechodzi przez Internet. Usługa ExpressRoute można łączyć za pomocą połączenia typu lokacja lokacja.

Aby dowiedzieć się więcej na temat wszystkich poprzednich opcji połączenia, przeczytaj [diagramy topologii połączeń](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.

## <a name="load-balancing"></a>Kierunek ruchu i równoważenia obciążenia

Microsoft Azure oferuje wiele usług zarządzania, w jaki sposób dystrybucji ruchu sieciowego i z równoważeniem obciążenia. Można użyć dowolnego z poniższych możliwości, oddzielnie lub razem:

**Równoważenie obciążenia DNS**

Usługa Azure Traffic Manager udostępnia DNS globalnego równoważenia obciążenia. Usługa Traffic Manager odpowiada klientów o adresie IP w dobrej kondycji punktu końcowego, na podstawie jednej z następujących metod routingu:
- **Geograficzne:** Klienci są kierowani do określonych punktów końcowych (Azure, zewnętrznego lub zagnieżdżone), oparte na lokalizacji geograficznej, do której pochodzi swoje zapytanie DNS. Ta metoda umożliwia realizację scenariuszy, w których jest ważna, wiedząc, regionu geograficznego klienta i routing je na jego podstawie. Przykłady obejmują zgodnych z zleceń niezależność danych, lokalizacja środowiska użytkownika & zawartości i pomiaru ruchu z różnych regionów.
- **Wydajność:** Adres IP zwrócony do klienta jest "najbliższą" do klienta. "Najbliższy" punkt końcowy nie jest koniecznie najbliższego mierzony geograficznej odległości. Zamiast tego ta metoda określa najbliższego punktu końcowego, mierząc opóźnienia sieci. Usługa Traffic Manager obsługuje Internet opóźnienie tabeli, aby śledzić czas obustronnej konwersji między zakresów adresów IP i każdego centrum danych platformy Azure.
- **Priorytet:** Ruch jest kierowany do podstawowego punktu końcowego (najwyższy priorytet). Jeśli podstawowy punkt końcowy jest niedostępny, usługa Traffic Manager kieruje ruchem do drugiego punktu końcowego. Jeśli zarówno podstawowe i pomocnicze punktów końcowych, które nie są dostępne, ruch jest przesyłany do innego i tak dalej. Dostępność punktu końcowego jest oparta na skonfigurowany stan (włączone lub wyłączone) i monitorowania ciągłego punktu końcowego.
- **Ważone działanie okrężne:** Dla każdego żądania usługi Traffic Manager losowo wybiera dostępnego punktu końcowego. Prawdopodobieństwo, że jest wybranie punktu końcowego jest oparty na przypisane wszystkie dostępne punkty końcowe wagi. Przy użyciu tymi samymi wagami dla wszystkich punktów końcowych skutkuje Dystrybucja ruchu nawet. Za pomocą wag wyższe lub niższe w określonych punktach końcowych powoduje, że te punkty końcowe częściej lub rzadziej zwracana w odpowiedzi DNS.

Na poniższej ilustracji przedstawiono żądania dla aplikacji sieci web, przekierowanie do punktu końcowego aplikacji sieci Web. Punkty końcowe można także innych usług platformy Azure, takie jak maszyny wirtualne i usługi w chmurze.

![Traffic Manager](./media/networking-overview/traffic-manager.png)

Klient łączy się bezpośrednio do tego punktu końcowego. Usługa Azure Traffic Manager wykrywa, gdy punkt końcowy jest w złej kondycji, a następnie przekierowuje klientów do innego, dobrej kondycji punktu końcowego. Aby dowiedzieć się więcej o usłudze Traffic Manager, przeczytaj [Omówienie usługi Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.

**Równoważenie obciążenia aplikacji**

Usługa Azure Application Gateway oferuje kontrolera dostarczania aplikacji (ADC) jako usługę. Usługa Application Gateway oferuje różne możliwości warstwy 7 (HTTP/HTTPS) równoważenia obciążenia dla Twoich aplikacji, w tym zapory aplikacji sieci web, aby chronić aplikacje sieci web przed lukami w zabezpieczeniach. Usługa Application Gateway umożliwia również Optymalizowanie wydajności farmy sieci web dzięki przeniesieniu kończenia żądań SSL mocy procesora CPU do usługi application gateway. 

Inne możliwości routingu warstwy 7 obejmują-okrężna Dystrybucja ruchu przychodzącego, koligacja sesji na podstawie plików cookie, routing oparty na ścieżkach URL i możliwość hostowania wielu witryn sieci Web za bramą pojedynczej aplikacji. Usługa Application Gateway można skonfigurować jako bramę dostępnego z Internetu, bramę tylko wewnętrzne lub jako kombinację obu tych. Usługa Application Gateway jest w pełni Azure zarządzane, skalowalne i o wysokiej dostępności. Zapewnia ona bogaty zestaw funkcji diagnostyki i rejestrowania, aby uprościć zarządzanie. Aby dowiedzieć się więcej na temat usługi Application Gateway, przeczytaj [Application Gateway — omówienie](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.

Na poniższej ilustracji przedstawiono adres URL oparty na ścieżkach routing z usługą Application Gateway:

![Application Gateway](./media/networking-overview/application-gateway.png)

**Równoważenie obciążenia sieciowego**

Usługi Azure Load Balancer zapewnia o wysokiej wydajności i niskich opóźnieniach warstwy 4 równoważenia obciążenia dla wszystkich protokołów UDP i TCP. Zarządza połączeń przychodzących i wychodzących. Można skonfigurować public i internal endpoints ze zrównoważonym obciążeniem. Można zdefiniować reguły, aby zamapować połączeń przychodzących do miejsc docelowych w puli zaplecza, zarządzanie za pomocą protokołu TCP i HTTP opcje usługi badania kondycji dostępności usługi. Aby dowiedzieć się więcej o usłudze Load Balancer, przeczytaj [omówienie Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.

Na poniższej ilustracji przedstawiono aplikację wielowarstwową dostępnego z Internetu, która korzysta z obu usług równoważenia obciążenia zewnętrznych i wewnętrznych:

![Moduł równoważenia obciążenia](./media/networking-overview/load-balancer.png)

## <a name="security"></a>Zabezpieczenia

Można filtrować ruch do i z zasobów platformy Azure, korzystając z następujących opcji:

- **Sieć:** Możesz zaimplementować Azure sieciowych grup zabezpieczeń (NSG), aby filtrować ruch przychodzący i wychodzący z zasobami platformy Azure. Każda grupa NSG zawiera jedną lub więcej reguł ruchu przychodzącego i wychodzącego. Każda reguła określa źródłowych adresów IP, docelowe adresy IP, port i protokół, który ruch jest filtrowany za pomocą. Sieciowe grupy zabezpieczeń można zastosować do poszczególnych podsieci, a poszczególnym maszynom wirtualnym. Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, przeczytaj [omówienie sieciowych grup zabezpieczeń](../virtual-network/security-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
- **Aplikacja:** Przy użyciu bramy aplikacji przy użyciu zapory aplikacji sieci web można chronić aplikacje sieci web przed lukami w zabezpieczeniach. Typowe przykłady to SQL ataki przez iniekcję kodu, z użyciem skryptów między witrynami i nieprawidłowo sformułowanych nagłówków. Usługa Application gateway odfiltrowuje ten ruch i zatrzymuje się dotrze do serwerów sieci web. Jesteś w stanie skonfigurować reguły, jakie ma być włączone. Możliwość konfigurowania zasady negocjacji protokołu SSL umożliwiający pewne zasady, które mają zostać wyłączone. Aby dowiedzieć się więcej na temat zapory aplikacji sieci web, przeczytaj [zapory aplikacji sieci Web](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.

Jeśli potrzebujesz możliwości sieci platformy Azure nie zapewniają lub chcesz użyć aplikacji sieciowych, których używasz w środowisku lokalnym, można zaimplementować te produkty na maszynach wirtualnych i łącz je z sieci wirtualnej. [Portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) zawiera kilka wstępnie skonfigurowany przy użyciu aplikacji sieciowych, które można obecnie używać różnych maszyn wirtualnych. Te wstępnie skonfigurowane maszyny wirtualne są zwykle określane jako wirtualne urządzenia sieciowe (WUS). Urządzenia WUS są dostępne z aplikacjami, takie jak Zapora i Optymalizacja sieci WAN.

## <a name="routing"></a>Routing

Platforma Azure utworzy domyślną tabel tras, które umożliwiają zasobów podłączone do żadnej podsieci w dowolnej sieci wirtualnej do komunikowania się ze sobą. Można zaimplementować jedną lub obie następujące rodzaje trasy, aby zastąpić domyślne trasy tworzone przez platformę Azure:
- **Zdefiniowane przez użytkownika:** możesz utworzyć niestandardowe tabele tras z trasami kontrolującymi przekierowywanie ruchu do każdej podsieci. Aby dowiedzieć się więcej o trasach definiowanych przez użytkownika, przeczytaj artykuł [Trasy definiowane przez użytkownika](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Protokół border gateway protocol (BGP):** Jeśli łączysz sieć wirtualną z siecią lokalną przy użyciu usługi Azure VPN Gateway lub połączenia ExpressRoute, możesz propagować trasy protokołu BGP w Twoich sieciach wirtualnych. BGP to standardowy protokół routingu używany często w Internecie do wymiany informacji o routingu i osiągalności między dwiema lub wieloma sieciami. W przypadku użycia w kontekście sieci wirtualnych platformy Azure, Protokół BGP umożliwia bram Azure VPN Gateway i lokalnych urządzeń sieci VPN, o nazwie elementów równorzędnych protokołu BGP lub sąsiednimi wymianę "tras", które informują obu bram informacje na temat dostępności i osiągalności tych prefiksów za pośrednictwem bram lub routerów zaangażowane. Protokół BGP można także włączyć routing tranzytowy między wieloma sieciami poprzez propagowanie tras, których brama BGP uczy się od jednego elementu równorzędnego BGP do wszystkich innych elementów równorzędnych protokołu BGP. Aby dowiedzieć się więcej na temat protokołu BGP, zobacz [protokołu BGP z bramami Azure VPN Gateway — omówienie](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.

## <a name="manageability"></a>Możliwości zarządzania

Platforma Azure udostępnia następujące narzędzia w celu monitorowania sieci i zarządzanie nią:
- **Dzienniki aktywności:** Wszystkie zasoby platformy Azure mają dzienników aktywności, które zawierają informacje o operacjach przeprowadzeniu stanu operacji i kto zainicjował operację. Aby dowiedzieć się więcej na temat dzienników aktywności, przeczytaj [Przegląd Dzienniki aktywności](../azure-monitor/platform/activity-logs-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
- **Dzienniki diagnostyczne:** Okresowe i spontanicznej zdarzeń są tworzone przez zasobów sieciowych i rejestrowane w ramach kont magazynu platformy Azure wysyłane do usługi Azure Event Hub lub wysyłane do usługi Azure Monitor dzienniki. Dzienniki diagnostyczne zawierają szczegółowe informacje o kondycji zasobu. Dzienniki diagnostyczne są dostarczane dla modułu równoważenia obciążenia (dostępnym z Internetu), sieciowe grupy zabezpieczeń, trasy i Application Gateway. Aby dowiedzieć się więcej na temat dzienników diagnostycznych, przeczytaj [Przegląd dzienników diagnostycznych](../azure-monitor/platform/diagnostic-logs-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
- **Metryki:** Metryki są pomiarów wydajności i liczniki zebrane w danym okresie czasu na zasobach. Metryki może służyć do wyzwolenia alertów na podstawie progów. Obecnie metryki są dostępne w usłudze Application Gateway. Aby dowiedzieć się więcej o metrykach, przeczytaj [Przegląd metryk](../monitoring-and-diagnostics/monitoring-overview-metrics.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
- **Rozwiązywanie problemów:** Informacje o rozwiązywaniu problemów są dostępne bezpośrednio w witrynie Azure portal. Informacje te pomagają zdiagnozować typowe problemy przy użyciu usługi ExpressRoute, bramy sieci VPN, usługa Application Gateway, dzienniki zabezpieczeń sieci, trasy, DNS, moduł równoważenia obciążenia i usługi Traffic Manager.
- **Kontrola dostępu oparta na rolach (RBAC):** Formant, który można tworzyć i zarządzać zasobami sieciowymi przy użyciu kontroli dostępu opartej na rolach (RBAC). Dowiedz się więcej o ROLACH, czytając [wprowadzenie RBAC](../role-based-access-control/overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu. 
- **Przechwytywanie pakietów:** Usługa Azure Network Watcher oferuje możliwość uruchamiania przechwytywania pakietów na maszynie Wirtualnej za pomocą rozszerzenia na maszynie wirtualnej. Ta funkcja jest dostępna dla systemów Linux i Windows maszyn wirtualnych. Aby dowiedzieć się więcej na temat przechwytywania pakietów, przeczytaj [omówienie przechwytywania pakietów](../network-watcher/network-watcher-packet-capture-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
- **Sprawdzanie przepływów adresów IP:** Usługa Network Watcher umożliwia sprawdzanie przepływów adresów IP między Maszyną wirtualną platformy Azure i zdalnego zasobu, aby określić, czy pakiety są dozwolone lub odrzucane. Ta funkcja umożliwia administratorom szybkie diagnozowanie problemów z połączeniem. Aby dowiedzieć się więcej na temat sprawdzanie przepływów adresów IP, przeczytaj [weryfikowanie przepływu protokołu IP — omówienie](../network-watcher/network-watcher-ip-flow-verify-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
- **Rozwiązywanie problemów z łącznością sieci VPN:** Rozwiązywanie problemów z sieci VPN usługi Network Watcher Umożliwia zapytanie połączenia lub bramy i sprawdzić kondycję zasobów. Aby dowiedzieć się więcej na temat Rozwiązywanie problemów z połączeniami sieci VPN, przeczytaj [połączenia sieci VPN, rozwiązywanie problemów — omówienie](../network-watcher/network-watcher-troubleshoot-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
- **Wyświetlanie topologii sieci:** Wyświetlić graficzną reprezentację zasobów sieciowych w sieci wirtualnej przy użyciu usługi Network Watcher. Aby dowiedzieć się więcej na temat wyświetlania topologii sieci, przeczytaj [omówienie topologii](../network-watcher/network-watcher-topology-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.

## <a name="tools"></a>Narzędzia do wdrażania i konfiguracji

Możesz wdrożyć i skonfigurować zasoby sieciowe platformy Azure z dowolnymi z następujących narzędzi:

- **Witryna Azure portal:** Graficzny interfejs użytkownika, który działa w przeglądarce. Otwórz [portal Azure](https://portal.azure.com).
- **Azure PowerShell:** Narzędzia wiersza polecenia do zarządzania platformy Azure z komputerów Windows. Więcej informacji na temat programu Azure PowerShell, czytając [Omówienie programu Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
- **Interfejs wiersza polecenia platformy Azure (CLI):** Narzędzia wiersza polecenia do zarządzania platformy Azure z komputerów z systemem Linux, macOS lub Windows. Więcej informacji na temat interfejsu wiersza polecenia platformy Azure, czytając [wiersza polecenia platformy Azure — omówienie](/cli/azure/get-started-with-azure-cli?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
- **Szablony usługi Azure Resource Manager:** Plik (w formacie JSON), który definiuje infrastruktury i konfiguracji rozwiązania platformy Azure. Dzięki szablonowi można wielokrotnie wdrażać rozwiązanie w całym jego cyklu życia z gwarancją spójnego stanu zasobów po każdym wdrożeniu. Aby dowiedzieć się więcej na temat tworzenia szablonów, przeczytaj [najlepsze rozwiązania dotyczące tworzenia szablonów](../azure-resource-manager/resource-manager-template-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu. Szablony można wdrażać za pomocą witryny Azure portal, interfejsu wiersza polecenia lub programu PowerShell. Aby rozpocząć pracę już teraz za pomocą szablonów, Wdróż jedną z wielu wstępnie skonfigurowanych szablonów w [szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?term=network) biblioteki. 

## <a name="pricing"></a>Cennik

Niektórych usług sieci platformy Azure są opłaty, a inne są bezpłatne. Widok [sieć wirtualna](https://azure.microsoft.com/pricing/details/virtual-network), [bramy sieci VPN](https://azure.microsoft.com/pricing/details/vpn-gateway), [Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/), [modułu równoważenia obciążenia](https://azure.microsoft.com/pricing/details/load-balancer), [usługi Network Watcher](https://azure.microsoft.com/pricing/details/network-watcher), [DNS](https://azure.microsoft.com/pricing/details/dns), [usługi Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager) i [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) ceny strony, aby uzyskać więcej informacji.

## <a name="next-steps"></a>Kolejne kroki

- Tworzenie pierwszej sieci wirtualnej i kilka maszyn wirtualnych z nim połączyć, wykonując czynności opisane w [tworzenie pierwszej sieci wirtualnej](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
- Łączenie komputera z siecią wirtualną, wykonując kroki opisane w [Konfigurowanie połączenia typu punkt lokacja](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
- Równoważ obciążenie powodowane przez ruch internetowy do publicznych serwerów, wykonując kroki opisane w [Tworzenie modułu równoważenia obciążenia dostępnego z Internetu](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
