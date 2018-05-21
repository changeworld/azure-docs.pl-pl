---
title: Sieć platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat usługi sieciowe Azure i możliwości.
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
ms.openlocfilehash: 2e5862e99b3e883554b42341d2c1dbe9d8b8ec72
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="azure-networking"></a>Sieć platformy Azure

Platforma Azure oferuje szeroką gamę możliwości sieciowych, które mogą być używane razem lub oddzielnie. Kliknij dowolną następujące kluczowe funkcje, aby dowiedzieć się więcej o nich:
- [Łączność między zasobami Azure](#connectivity): Połącz Azure zasobów w bezpieczny, prywatnej sieci wirtualnej w chmurze.
- [Połączenie z Internetem](#internet-connectivity): komunikowanie się z zasobów platformy Azure i w Internecie.
- [Połączenie lokalne](#on-premises-connectivity): połączyć sieć lokalną do zasobów platformy Azure za pośrednictwem wirtualnej sieci prywatnej (VPN) przez Internet lub za pomocą dedykowanego połączenia z platformą Azure.
- [Kierunek ruchu i równoważenie obciążenia](#load-balancing): ruch równoważenia obciążenia do serwerów w tej samej lokalizacji i bezpośredniego ruch do serwerów w różnych lokalizacjach.
- [Zabezpieczenia](#security): filtrowania ruchu sieciowego między podsieciami sieci lub poszczególnych maszyn wirtualnych (VM).
- [Routing](#routing): korzystać z routingu domyślne lub pełnej kontroli routingu między zasobami platformy Azure i lokalnymi.
- [Możliwości zarządzania](#manageability): Monitor i zarządzania zasobami sieci platformy Azure.
- [Wdrażanie i Konfigurowanie narzędzi](#tools): portalu sieci web lub narzędzia wiersza polecenia i platform wdrażania i konfigurowania zasobów sieciowych.

## <a name="Connectivity"></a>Łączność między zasobami Azure

Zasobów platformy Azure, takich jak maszyny wirtualne, usługi w chmurze, zestawy skalowania maszyn wirtualnych i środowiska usługi Azure App Service może komunikować się przez użytkowników ze sobą za pośrednictwem sieci wirtualnej platformy Azure (VNet). Sieci wirtualnej jest logiczną izolacją chmury Azure w wersji dedykowanej do Twojej [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fnetworking%2ftoc.json). Można zaimplementować wiele sieci wirtualnych w ramach każdej subskrypcji platformy Azure i Azure [region](https://azure.microsoft.com/regions). Każda sieć wirtualna jest odizolowana od innych sieci wirtualnych. Dla każdej sieci wirtualnej można:

- Określenie niestandardowej prywatnej przestrzeni adresowej IP przy użyciu adresów publicznych i prywatnych (RFC 1918). Przypisuje Azure zasoby podłączone do sieci wirtualnej prywatnego adresu IP z przestrzeń adresów, które można przypisać.
- Segment sieci wirtualnej do co najmniej jednej podsieci i przydzielić część przestrzeni adresowej sieci wirtualnej dla każdej podsieci.
- Użyj rozpoznawania nazw platformy Azure lub Podaj własny serwer DNS do użytku przez zasoby podłączone do sieci wirtualnej.

Aby dowiedzieć się więcej o usłudze Azure Virtual Network, przeczytaj [omówienie sieci wirtualnej](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu. Sieci wirtualne można połączyć ze sobą, włączanie zasoby podłączone do jednej sieci wirtualnej do komunikują się ze sobą za pośrednictwem sieci wirtualnych. Do sieci wirtualnych się ze sobą łączyć, można użyć jednego lub obu z następujących opcji:

- **Komunikacja równorzędna:** umożliwia zasoby podłączone do różnych sieci wirtualnych platformy Azure w ramach tego samego regionu Azure do komunikowania się ze sobą. Przepustowości i opóźnień między sieciami wirtualnymi jest taka sama, jak gdyby zasoby były podłączone do tej samej sieci wirtualnej. Aby dowiedzieć się więcej na temat komunikacji równorzędnej, przeczytaj [komunikacji równorzędnej omówienie sieci wirtualnej](../virtual-network/virtual-network-peering-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
- **Brama sieci VPN:** umożliwia zasoby podłączone do różnych sieci wirtualnych platformy Azure w różnych regionach platformy Azure do komunikowania się ze sobą. Przepływ ruchu między sieciami wirtualnymi za pośrednictwem bramy sieci VPN platformy Azure. Przepustowość między sieciami wirtualnymi jest ograniczona do przepustowości bramy. Aby dowiedzieć się więcej o nawiązywaniu połączeń sieci wirtualnych z bramą sieci VPN, przeczytaj [skonfigurować połączenia do wirtualnymi w regionach](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.

## <a name="internet-connectivity"></a>Połączenie z Internetem

Domyślnie wszystkie Azure zasoby podłączone do sieci wirtualnej mają łączność wychodząca z Internetem. Prywatny adres IP zasobu jest źródłowy adres sieciowy translacji (SNAT) do publicznego adresu IP przez infrastrukturę platformy Azure. Aby dowiedzieć się więcej na temat wychodzące połączenie z Internetem, przeczytaj [Opis połączeń wychodzących na platformie Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.

Do komunikacji przychodzącej zasobów platformy Azure z Internetu lub komunikacji ruch wychodzący do Internetu bez SNAT, musi on mieć przypisaną publicznego adresu IP. Aby dowiedzieć się więcej na temat publiczne adresy IP, przeczytaj [publicznego adresu IP, adresy](../virtual-network/virtual-network-public-ip-address.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.

## <a name="on-premises-connectivity"></a>Połączenie lokalne

Dostępne zasoby w sieci wirtualnej bezpiecznie za pośrednictwem połączenia sieci VPN lub bezpośrednie połączenie prywatne. Aby wysyłać ruch sieciowy między sieci wirtualnej platformy Azure i sieci lokalnej, należy utworzyć bramę sieci wirtualnej. Możesz skonfigurować ustawienia dla bramy można utworzyć typu połączenia, które chcesz, sieci VPN lub usługi ExpressRoute.

Sieci lokalnej można nawiązać sieć wirtualną przy użyciu dowolnej kombinacji następujących opcji:

**Punkt lokacja (sieć VPN przez protokół SSTP)**

Na poniższej ilustracji przedstawiono oddzielne punktu połączenia do lokacji między wiele komputerów i sieci wirtualnej:

![Punkt-lokacja](./media/networking-overview/point-to-site.png)

To połączenie zostanie nawiązane między jednego komputera i sieci wirtualnej. Ten typ połączenia jest świetny, jeśli dopiero rozpoczynasz pracę z platformą Azure. Jest też odpowiedni dla deweloperów, ponieważ wymaga niewielkich zmian w istniejącej sieci lub nie wymaga ich wcale. Istnieje również wygodne, gdy nawiązujesz połączenie z lokalizacji zdalnej, takich jak konferencji lub macierzystego. Połączenia punkt lokacja często są połączone z połączenia lokacja lokacja za pomocą tej samej bramy sieci wirtualnej. Połączenie korzysta z protokołu SSTP zapewnienie szyfrowaną komunikację przez Internet między komputerem a sieci wirtualnej. Opóźnienie dla sieci VPN punkt lokacja jest nieprzewidziany, ponieważ dane są przesyłane za pośrednictwem Internetu.

**Lokacja lokacja (tunel VPN IPsec i IKE)**

![Lokacja-lokacja](./media/networking-overview/site-to-site.png)

To połączenie zostanie nawiązane między lokalnego urządzenia sieci VPN i bramy sieci VPN platformy Azure. Ten typ połączenia pozwala dowolnego zasobu lokalnego, które zezwalają na dostęp do sieci wirtualnej. Połączenie jest IPSec i IKE sieci VPN, który zapewnia szyfrowaną komunikację przez Internet między urządzeniem lokalnymi i bramy sieci VPN platformy Azure. Do tej samej bramy sieci VPN można połączyć wiele lokacji lokalnej. Lokalnego urządzenia sieci VPN w każdej lokacji musi mieć zewnętrznie uwzględniającym publiczny adres IP, który nie znajduje się za urządzeniem NAT Czas oczekiwania na połączenie lokacja lokacja jest nieprzewidywalne, ponieważ dane są przesyłane za pośrednictwem Internetu.

**ExpressRoute (dedykowane połączenie prywatne)**

![ExpressRoute](./media/networking-overview/expressroute.png)

Tego typu połączenie zostanie nawiązane między siecią a Azure, za pośrednictwem partner usługi ExpressRoute. To połączenie jest prywatne. Ruch nie przechodzą przez Internet. Czas oczekiwania na połączenie ExpressRoute jest przewidywalne, ponieważ ruchu nie przechodzenie przez Internet. ExpressRoute można łączyć za pomocą połączenia lokacja lokacja.

Aby dowiedzieć się więcej na temat wszystkich poprzednich opcji połączenia, przeczytaj [Diagram topologii połączenia](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.

## <a name="load-balancing"></a>Kierunek ruchu i równoważenia obciążenia

Microsoft Azure udostępnia wiele usług do zarządzania ruchem w sieci i równoważenia obciążenia. Można użyć dowolnego z następujących funkcji, oddzielnie lub razem:

**Równoważenie obciążenia DNS**

Usługa Menedżer ruchu Azure udostępnia DNS globalnego równoważenia obciążenia. Menedżer ruchu odpowiada klientów przy użyciu adresu IP w dobrej kondycji punktu końcowego, na podstawie jednej z następujących metod routingu:
- **Geograficzne:** klienci są kierowani do określonych punktów końcowych (Azure, zewnętrznego lub zagnieżdżonych), na podstawie których lokalizacji geograficznej ich zapytanie DNS pochodzi z. Ta metoda umożliwia realizację scenariuszy, których wiedzy o region geograficzny klienta i ich na jego podstawie routingu jest ważna. Przykładami zgodnych z danych suwerenności zleceń, lokalizacja środowiska użytkownika & zawartości i pomiar ruchu z różnych regionów.
- **Wydajność:** adres IP, do klienta zwracany jest "najbardziej" do klienta. "Najbliższy" punktu końcowego nie jest niekoniecznie najbliższego mierzony odległość geograficznej. Jednak ta metoda określa najbliższy punkt końcowy mierząc opóźnienia sieci. Menedżer ruchu zapisuje tabeli opóźnienia Internet na śledzenie czasu Rundy między zakresów adresów IP i każdego centrum danych Azure.
- **Priorytet:** ruch jest kierowany do podstawowego punktu końcowego (najwyższy priorytet). Jeśli podstawowy punkt końcowy nie jest dostępna, Menedżer ruchu kieruje ruch do drugiego punktu końcowego. Jeśli zarówno podstawowe i pomocnicze punkty końcowe nie są dostępne, ruch przechodzi do innej i tak dalej. Dostępność punktu końcowego jest na podstawie skonfigurowanego stanu (włączona lub wyłączona) i monitorowania bieżących punktów końcowych.
- **Ważone działanie okrężne:** dla każdego żądania usługi Traffic Manager losowo wybiera dostępnego punktu końcowego. Prawdopodobieństwo wybrać punkt końcowy jest oparta na wag przypisane do wszystkich dostępnych punktów końcowych. Wszystkie punkty końcowe wyniki w Dystrybucja ruchu nawet przy użyciu tymi samymi wagami. Za pomocą wag wyższe lub niższe w określonych punktach końcowych sprawia, że te punkty końcowe zwracaną częściej lub rzadziej w odpowiedzi DNS.

Na poniższej ilustracji przedstawiono żądania dla aplikacji sieci web skierowane do punktu końcowego aplikacji sieci Web. Punkty końcowe można także innych usług Azure, takich jak maszyny wirtualne i usługi w chmurze.

![Traffic Manager](./media/networking-overview/traffic-manager.png)

Klient łączy się bezpośrednio do tego punktu końcowego. Menedżer ruchu Azure wykrywa punkt końcowy jest zła, a następnie przekierowuje klientów do punktu końcowego innych, dobrej kondycji. Aby dowiedzieć się więcej na temat Menedżera ruchu, przeczytaj [Omówienie usługi Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.

**Równoważenie obciążenia aplikacji**

Usługa Azure Application Gateway udostępnia kontroler dostarczania aplikacji (ADC) jako usługa. Brama aplikacji umożliwia różnych warstwy 7 (HTTP/HTTPS) równoważenia obciążenia dla aplikacji, w tym zapory aplikacji sieci web w celu ochrony aplikacji sieci web z luk w zabezpieczeniach i luk w zabezpieczeniach. Brama aplikacji można też w celu zoptymalizowania wydajności kolektywu serwerów sieci web dzięki przeniesieniu kończenia żądań SSL użycie Procesora CPU na bramie aplikacji. 

Inne funkcje routingu warstwy 7 obejmują rozdzielanie ruchu przychodzącego, koligacji na podstawie plików cookie sesji, routingu adresów URL na podstawie ścieżki i może obsługiwać wiele witryn sieci Web za bramy pojedynczej aplikacji. Brama aplikacji można skonfigurować jako bramy łączącej z Internetem, bramę wyłącznie wewnętrznym lub obie te grupy. Brama aplikacji jest w pełni Azure zarządzanych, skalowalności i wysokiej dostępności. Zapewnia ona bogaty zestaw funkcji diagnostyki i rejestrowania, aby uprościć zarządzanie. Aby dowiedzieć się więcej na temat bramy aplikacji, przeczytaj [omówienie bramy aplikacji](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.

Na poniższej ilustracji przedstawiono adresu URL ścieżki na podstawie routingu z bramy aplikacji:

![Application Gateway](./media/networking-overview/application-gateway.png)

**Równoważenie obciążenia sieciowego**

Moduł równoważenia obciążenia Azure udostępnia wysokiej wydajności i małych opóźnieniach 4 warstwy równoważenia obciążenia do wszystkich protokołów UDP i TCP. Zarządza połączeń przychodzących i wychodzących. Można skonfigurować publicznych oraz wewnętrznych końcowych z równoważeniem obciążenia. Można zdefiniować reguły mapowania połączenia przychodzące do miejsc docelowych puli zaplecza przy użyciu opcji TCP i HTTP badania kondycji dostępności usługi zarządzania. Aby dowiedzieć się więcej na temat równoważenia obciążenia, przeczytaj [Omówienie usługi równoważenia obciążenia](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.

Na poniższej ilustracji przedstawiono aplikację wielowarstwową internetowy, który używa zarówno usługi równoważenia obciążenia wewnętrznych i zewnętrznych:

![Moduł równoważenia obciążenia](./media/networking-overview/load-balancer.png)

## <a name="security"></a>Zabezpieczeń

Można filtrować ruch do i z zasobów platformy Azure, korzystając z następujących opcji:

- **Sieć:** można zaimplementować Azure sieciowych grup zabezpieczeń (NSG) do filtrowania ruchu przychodzącego i wychodzącego do zasobów platformy Azure. Każda grupa NSG zawiera jedną lub więcej reguł ruchu przychodzącego i wychodzącego. Każda reguła określa źródłowych adresów IP, docelowe adresy IP, port i protokół, który ruch jest filtrowany za pomocą. Grupy NSG można zastosować do poszczególnych podsieci i poszczególnych maszyn wirtualnych. Aby dowiedzieć się więcej na temat grup NSG, przeczytaj [Przegląd grup zabezpieczeń sieci](../virtual-network/security-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
- **Aplikacja:** przy użyciu bramy aplikacji z zapory aplikacji sieci web może chronić aplikacje sieci web z luk w zabezpieczeniach i luk w zabezpieczeniach. Typowe przykłady są SQL iniekcji atakami, skryptów krzyżowych oraz źle sformułowane nagłówki. Brama aplikacji w odfiltrowuje ten ruch i go zatrzyma dotrze do serwerów sieci web. Jesteś w stanie skonfigurować zasady, jakie mają być włączone. Możliwość konfigurowania zasad negocjacji SSL umożliwiającej określone zasady mają zostać wyłączone. Aby dowiedzieć się więcej na temat zapory aplikacji sieci web, przeczytaj [zapory aplikacji sieci Web](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.

Jeśli potrzebujesz możliwości sieci Azure nie zapewniają lub chcesz użyć używasz lokalnych aplikacji sieci można zaimplementować produktów na maszynach wirtualnych i podłącz je do sieci wirtualnej. [Portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) zawiera kilka wstępnie skonfigurowane z aplikacjami sieci można obecnie używać różnych maszyn wirtualnych. Te wstępnie skonfigurowanych maszyn wirtualnych zwykle są określane jako urządzenie wirtualne sieci (NVA). NVAs są dostępne z aplikacjami, takie jak zapory i Optymalizacja sieci WAN.

## <a name="routing"></a>Routing

Platforma Azure tworzy domyślne tabele tras, które umożliwiają zasoby podłączone do żadnej podsieci w dowolnej sieci wirtualnej do komunikowania się ze sobą. Można zaimplementować jedną lub obie następujące typy trasy do przesłonięcia trasy domyślne, które tworzy Azure:
- **Zdefiniowane przez użytkownika:** tworzenia tabel tras niestandardowych trasy tego formantu, których ruch jest kierowany do dla każdej podsieci. Aby dowiedzieć się więcej o trasach definiowanych przez użytkownika, przeczytaj artykuł [Trasy definiowane przez użytkownika](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Border gateway protocol (BGP):** połączenie Twojej sieci wirtualnej sieci lokalnej przy użyciu połączenia bramy sieci VPN platformy Azure lub usługi ExpressRoute, podczas rozpowszechniania tras BGP do Twojej sieci wirtualnych. BGP to standardowy protokół routingu używany często w Internecie do wymiany informacji o routingu i osiągalności między dwiema lub wieloma sieciami. Używana w kontekście sieci wirtualnych Azure, Protokół BGP umożliwia bramy sieci VPN platformy Azure i wymagane urządzenia sieci VPN lokalnego o nazwie elementów równorzędnych BGP lub sąsiadów do programu exchange "tras" informujących zarówno bramy na dostępność i uzyskiwanie dla tych prefiksów podąża bram czy routery. Protokół BGP można także włączyć routing tranzytowy między wieloma sieciami przez propagowania tras BGP bramy uczy się z jednego elementu równorzędnego protokołu BGP do wszystkich innych elementów równorzędnych protokołu BGP. Aby dowiedzieć się więcej na temat protokołu BGP, zobacz [protokołu BGP z bramy sieci VPN platformy Azure — omówienie](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.

## <a name="manageability"></a>Możliwości zarządzania

Platforma Azure oferuje następujące narzędzia do monitorowania i zarządzania siecią:
- **Dzienniki aktywności:** Azure wszystkie zasoby zawierają dzienniki aktywności, które zawierają informacje o operacji przeprowadzeniu stanu operacji i kto zainicjował operację. Aby dowiedzieć się więcej o Dzienniki aktywności, przeczytaj [działania rejestruje omówienie](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
- **Dzienniki diagnostyczne:** okresowo i spontanicznych zdarzenia są tworzone przez zasobów sieciowych i rejestrowane na kontach magazynu Azure, wysyłane do usługi Azure Event Hub lub wysyłane do usługi Analiza dzienników Azure. Dzienniki diagnostyczne zapewniają wgląd w kondycję zasobu. Dzienniki diagnostyczne są dostępne dla usługi równoważenia obciążenia (internetowy), grup zabezpieczeń sieci tras i bramy aplikacji. Aby dowiedzieć się więcej na temat dzienników diagnostycznych, przeczytaj [Przegląd dzienników diagnostycznych](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
- **Metryki:** metryki są miar wydajności i liczniki zebrane w danym okresie czasu na zasobach. Metryki może służyć do wyzwalania na podstawie progów alertów. Obecnie dostępne dla bramy aplikacji są metryki. Aby dowiedzieć się więcej na temat metryki, przeczytaj [omówienie metryki](../monitoring-and-diagnostics/monitoring-overview-metrics.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
- **Rozwiązywanie problemów:** informacje dotyczące rozwiązywania problemów jest dostępny bezpośrednio w portalu Azure. Informacje te pomagają zdiagnozować typowych problemów z usługi ExpressRoute, Brama sieci VPN bramy aplikacji, dzienniki zabezpieczeń sieci, tras, DNS, usługi równoważenia obciążenia i Menedżera ruchu.
- **Kontrola dostępu oparta na rolach (RBAC):** kontrolowania, kto może utworzyć zasobów i zarządzanie nimi sieci przy użyciu kontroli dostępu opartej na rolach (RBAC). Dowiedz się więcej o RBAC odczytując [wprowadzenie RBAC](../role-based-access-control/overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu. 
- **Przechwytywania pakietów:** usługa obserwatora sieciowego Azure oferuje możliwość uruchamiania przechwytywania pakietów na maszynie Wirtualnej za pomocą rozszerzenia w Maszynie wirtualnej. Ta funkcja jest dostępna dla systemów Linux i maszyn wirtualnych systemu Windows. Aby dowiedzieć się więcej na temat przechwytywania pakietów, przeczytaj [omówienie przechwytywania pakietów](../network-watcher/network-watcher-packet-capture-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
- **Sprawdź IP przepływów:** obserwatora sieciowego pozwala sprawdzić przepływów IP między maszyny Wirtualnej platformy Azure i zdalnego zasobu, aby określić, czy pakiety zezwolono na dostęp lub odmowa dostępu. Ta funkcja umożliwia administratorom szybkie diagnozowanie problemów z łącznością. Aby dowiedzieć się więcej o tym, jak można zweryfikować przepływów IP, przeczytaj [przepływu IP sprawdzić Przegląd](../network-watcher/network-watcher-ip-flow-verify-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
- **Rozwiązywania problemów z połączeniami sieci VPN:** do rozwiązywania problemów z sieci VPN z obserwatora sieciowego umożliwia zapytanie połączenia lub bramy i sprawdzić kondycję zasobów. Aby dowiedzieć się więcej na temat rozwiązywania problemów z połączenia sieci VPN, należy przeczytać [połączenie z siecią VPN Rozwiązywanie problemów — omówienie](../network-watcher/network-watcher-troubleshoot-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
- **Wyświetl topologii sieci:** wyświetlić graficzną reprezentację zasobów sieciowych w sieci wirtualnej z obserwatora sieciowego. Aby dowiedzieć się więcej o wyświetlaniu topologii sieci, przeczytaj [omówienie topologii](../network-watcher/network-watcher-topology-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.

## <a name="tools"></a>Wdrażanie i Konfigurowanie narzędzi

Można wdrożyć i skonfigurować zasoby sieci platformy Azure za pomocą dowolnego z następujących narzędzi:

- **Portalu Azure:** graficzny interfejs użytkownika uruchomiony w przeglądarce. Otwórz [portal Azure](http://portal.azure.com).
- **Program Azure PowerShell:** wiersza polecenia narzędzia do zarządzania Azure, komputery z systemem Windows. Więcej informacji na temat programu Azure PowerShell odczytując [Omówienie programu Azure PowerShell](/powershell/azure/overview?view=azurermps-3.8.0?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
- **Azure interfejsu wiersza polecenia (CLI):** narzędzia wiersza polecenia do zarządzania z komputerów z systemem Linux, macOS lub Windows Azure. Dowiedz się więcej o wiersza polecenia platformy Azure, odczytując [omówienie interfejsu wiersza polecenia Azure](/cli/azure/get-started-with-azure-cli?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
- **Szablony usługi Azure Resource Manager:** pliku (w formacie JSON), który definiuje infrastrukturę i konfigurację rozwiązania Azure. Dzięki szablonowi można wielokrotnie wdrażać rozwiązanie w całym jego cyklu życia z gwarancją spójnego stanu zasobów po każdym wdrożeniu. Aby dowiedzieć się więcej na temat tworzenia szablonów, przeczytaj [najlepszych rozwiązań dotyczących tworzenia szablonów](../azure-resource-manager/resource-manager-template-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu. Szablony można wdrożyć z portalu Azure, interfejsu wiersza polecenia lub środowiska PowerShell. Aby rozpocząć pracę od razu z szablonami, Wdróż jedne z wielu szablonów wstępnie skonfigurowane w [szablonów Szybki Start Azure](https://azure.microsoft.com/resources/templates/?term=network) biblioteki. 

## <a name="pricing"></a>Cennik

Niektóre sieci usług Azure mają opłat, a inne są wolne. Widok [sieci wirtualnej](https://azure.microsoft.com/pricing/details/virtual-network), [bramy sieci VPN](https://azure.microsoft.com/pricing/details/vpn-gateway), [brama aplikacji w](https://azure.microsoft.com/pricing/details/application-gateway/), [modułu równoważenia obciążenia](https://azure.microsoft.com/pricing/details/load-balancer), [obserwatora sieciowego](https://azure.microsoft.com/pricing/details/network-watcher), [DNS](https://azure.microsoft.com/pricing/details/dns), [Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager) i [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) cennik strony, aby uzyskać więcej informacji.

## <a name="next-steps"></a>Kolejne kroki

- Tworzenie pierwszej sieci wirtualnej i połączyć kilka maszyn wirtualnych, wykonując kroki opisane w [tworzenie pierwszej sieci wirtualnej](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
- Połączenia komputera z sieci wirtualnej, wykonując kroki opisane w [skonfigurować połączenie punkt lokacja](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
- Równoważenie obciążenia ruchu internetowego na serwerach publicznych, wykonując kroki opisane w [utworzyć moduł równoważenia obciążenia internetowy](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) artykułu.
