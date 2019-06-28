---
title: Co to jest usługa Azure Firewall?
description: Dowiedz się więcej o funkcjach usługi Azure Firewall.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 6/21/2019
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 2567c47e41306a7940b6d065feb49ae80bb16198
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312697"
---
# <a name="what-is-azure-firewall"></a>Co to jest usługa Azure Firewall?

Azure Firewall to zarządzana, sieciowa usługa zabezpieczeń oparta na chmurze, która zabezpiecza zasoby usługi Azure Virtual Network. Jest w pełni stanowych zapory jako usługa z wbudowaną wysoką dostępność i skalowalność chmury bez ograniczeń.

![Omówienie zapory](media/overview/firewall-threat.png)

Możesz centralnie tworzyć, wymuszać i rejestrować zasady łączności aplikacji i sieci w subskrypcjach i sieciach wirtualnych. Usługa Azure Firewall korzysta ze statycznego publicznego adresu IP dla zasobów sieci wirtualnej, co umożliwia zewnętrznym zaporom identyfikowanie ruchu pochodzącego z sieci wirtualnej.  Usługa jest w pełni zintegrowana z usługą Azure Monitor na potrzeby rejestrowania i analiz.

## <a name="features"></a>Funkcje

Usługa Azure Firewall oferuje następujące funkcje:

### <a name="built-in-high-availability"></a>Wbudowana wysoka dostępność

Wysoka dostępność jest wbudowany, nie dodatkowych modułów równoważenia obciążenia są wymagane i nie ma nic, które trzeba skonfigurować.

### <a name="availability-zones-public-preview"></a>Strefy dostępności (publiczna wersja zapoznawcza)

Podczas wdrażania na wielu strefach dostępności na potrzeby zwiększonej dostępności można skonfigurować zaporę platformy Azure. Dzięki strefom dostępności poziomu dostępności zwiększa dostępność przez 99,99% czasu. Aby uzyskać więcej informacji, zobacz zapory usługi Azure [Umowa dotycząca poziomu usług (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). Czas działania 99,99% umowy SLA jest oferowana po wybraniu co najmniej dwóch strefach dostępności.

Można również skojarzyć zapory usługi Azure do określonej strefy tylko ze względów odległości między elementami przy użyciu usług standardowa SLA 99,95% czasu.

Nie ma żadnych dodatkowych kosztów, zaporę wdrożonej w strefie dostępności. Jednakże istnieją dodatkowe koszty transferu danych przychodzących i wychodzących skojarzonych stref dostępności. Aby uzyskać więcej informacji, zobacz [przepustowość — szczegóły cennika](https://azure.microsoft.com/pricing/details/bandwidth/).

Strefy dostępności zapory platformy Azure są dostępne w regionach, które obsługują strefy dostępności. Aby uzyskać więcej informacji, zobacz [co to są strefy dostępności na platformie Azure?](../availability-zones/az-overview.md#services-support-by-region)

> [!NOTE]
> Strefy dostępności można skonfigurować tylko podczas wdrażania. Nie można skonfigurować istniejących zaporę, aby uwzględnić stref dostępności.

Aby uzyskać więcej informacji o strefach dostępności, zobacz [co to są strefy dostępności na platformie Azure?](../availability-zones/az-overview.md)

### <a name="unrestricted-cloud-scalability"></a>Skalowalność w chmurze bez ograniczeń

Usługa Azure Firewall umożliwia skalowanie bez ograniczeń, odpowiednio do zmieniających się przepływów ruchu sieciowego — nie trzeba więc dostosowywać budżetu do okresów szczytowego ruchu.

### <a name="application-fqdn-filtering-rules"></a>Reguły filtrowania w pełni kwalifikowanych nazw domen aplikacji

Możesz ograniczyć wychodzący ruch protokołu HTTP/S do konkretnej listy w pełni kwalifikowanych nazw domen, również z użyciem symboli wieloznacznych. Ta funkcja nie wymaga kończenia żądań SSL.

### <a name="network-traffic-filtering-rules"></a>Reguły filtrowania ruchu sieciowego

Można centralnie tworzyć reguły filtrowania sieci (*zezwalania* lub *blokowania*) na podstawie źródłowego i docelowego adresu IP, portu i protokołu. Usługa Azure Firewall jest w pełni stanowa, więc możesz rozróżniać autentyczne pakiety w ramach różnych typów połączeń. Reguły są wymuszane i rejestrowane w wielu subskrypcjach i sieciach wirtualnych.

### <a name="fqdn-tags"></a>Tagi w pełni kwalifikowanych nazw domen

Tagi w pełni kwalifikowanych nazw domen zezwalają na znany ruch sieciowy usługi Azure przez zaporę. Załóżmy na przykład, że chcesz zezwolić na ruch sieciowy z witryny Windows Update przez zaporę. Tworzysz regułę aplikacji i dołączasz tag „Windows Update”. Teraz ruch sieciowy z witryny Windows Update może przechodzić przez zaporę.

### <a name="service-tags"></a>Tagi usługi

Tag usługi reprezentuje grupę prefiksów adresów IP, aby zminimalizować złożoność tworzenia reguły zabezpieczeń. Nie można utworzyć własnego tagu usługi ani określić adresów IP są zawarte w obrębie tag. Firma Microsoft zarządza prefiksami adresów obejmowanymi przez tag usługi i automatycznie aktualizuje tag usługi, gdy adresy ulegną zmianie.

### <a name="threat-intelligence"></a>Analiza zagrożeń

Filtrowanie oparte na analizie zagrożeń można włączyć dla zapory, aby wysyłać alerty oraz blokować ruch ze znanych złośliwych adresów IP i domen i do nich. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft.

### <a name="outbound-snat-support"></a>Obsługa translacji adresów sieciowych źródła (SNAT) dla ruchu wychodzącego

Wszystkie adresy IP wychodzącego ruchu sieciowego są tłumaczone na publiczny adres IP usługi Azure Firewall (translacja adresów sieciowych źródła, SNAT). Możesz zidentyfikować ruch pochodzący z sieci wirtualnej do zdalnych internetowych miejsc docelowych i zezwalać na niego. Zaporę platformy Azure nie SNAT, jeśli docelowy adres IP jest prywatny zakres adresów IP na [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Jeśli Twoja organizacja korzysta z publicznego zakres adresów IP dla sieci prywatnych, zapory usługi Azure będzie użyć funkcji SNAT względem ruchu do jednej z prywatnych adresów IP zapory w AzureFirewallSubnet.

### <a name="inbound-dnat-support"></a>Obsługa technologii DNAT dla ruchu przychodzącego

Ruch sieciowy przychodzący do publicznego adresu IP zapory jest przetwarzany przy użyciu technologii DNAT i filtrowany do prywatnych adresów IP w sieciach wirtualnych.

### <a name="multiple-public-ips-public-preview"></a>Wiele publicznych adresów IP (publiczna wersja zapoznawcza)

Wiele publicznych adresów IP (do 600) można skojarzyć z zaporą.

Umożliwia to obsługę następujących scenariuszy:

- **DNAT** — wielu wystąpień standardowego portu można tłumaczyć na serwerach wewnętrznej bazy danych. Na przykład jeśli masz dwa publiczne adresy IP, może dokonywać translacji portu TCP 3389 (RDP) dla obu adresów IP.
- **SNAT** — dodatkowe porty są dostępne dla połączeń SNAT wychodzących, zmniejszyć prawdopodobieństwo SNAT wyczerpanie portów. W tej chwili zapory usługi Azure losowo wybiera publiczny adres IP źródłowego na potrzeby połączenia. Jeśli masz wszystkie podrzędne filtrowania w sieci, należy zezwolić na wszystkie publiczne adresy IP skojarzone z zaporą.

> [!NOTE]
> W publicznej wersji zapoznawczej możesz dodać lub usunąć publiczny adres IP do zapory uruchomione, istniejące połączenie przychodzące za pomocą reguł DNAT mogą nie działać 40 – 120 sekund. Nie można usunąć publiczny adres IP przypisany do zapory, chyba, że Zapora jest cofnięty lub usunięty.

### <a name="azure-monitor-logging"></a>Rejestrowanie w usłudze Azure Monitor

Wszystkie zdarzenia są zintegrowane z usługą Azure Monitor, co umożliwia archiwizowanie dzienników na koncie magazynu, przesyłanie strumieniowe zdarzeń do centrum Event Hub lub wysyłanie ich do dzienników usługi Azure Monitor.

## <a name="known-issues"></a>Znane problemy

W usłudze Azure Firewall występują następujące znane problemy:

|Problem  |Opis  |Środki zaradcze  |
|---------|---------|---------|
|Konflikt z funkcją Just in time (JIT) usługi Azure Security Center|W przypadku dostępu JIT do maszyny wirtualnej znajdującej się w podsieci z trasą zdefiniowaną przez użytkownika, która wskazuje usługę Azure Firewall jako bramę domyślną, funkcja JIT usługi Azure Security Center nie działa. Jest to wynik z routingiem asymetrycznym — pakiet jest oferowana w za pośrednictwem publicznego adresu IP maszyny wirtualnej (JIT otwartego dostępu), ale ścieżki zwrotu za pośrednictwem zapory, co powoduje pakietu, ponieważ nie istnieje żadne ustanowienie sesji w obrębie zapory.|Aby wyeliminować ten problem, należy umieścić maszyny wirtualne z funkcją JIT w oddzielnej podsieci, w której nie ma zdefiniowanej przez użytkownika trasy do zapory.|
Reguły filtrowania dla protokołów innych niż TCP/UDP (na przykład ICMP) nie działają dla ruchu powiązanego z Internetem|Reguły filtrowania sieci dla protokołów innych niż TCP/UPD nie działają z funkcją SNAT i publicznym adresem IP. Protokoły inne niż TCP/UDP są obsługiwane między podsieciami szprych i sieciami wirtualnymi.|Usługa Azure Firewall korzysta ze standardowego modułu równoważenia obciążenia, [który obecnie nie obsługuje funkcji SNAT dla protokołów IP](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). Firma Microsoft Eksplorowanie opcji, aby umożliwić obsługę tego scenariusza w przyszłej wersji.|
|Brak obsługi protokołu ICMP w programie PowerShell i interfejsie wiersza polecenia|Program PowerShell i interfejs wiersza polecenia nie obsługują protokołu ICMP jako prawidłowego protokołu w regułach sieciowych.|Jest nadal możliwe używać protokołu ICMP jako protokół za pośrednictwem portalu i interfejsu API REST. Pracujemy nad szybko dodać protokołu ICMP w programie PowerShell i interfejsu wiersza polecenia.|
|Tagi FQDN wymagają ustawienia protokołu i portu|Reguły aplikacji przy użyciu tagów w pełni kwalifikowaną nazwę domeny wymagają portu: protokół definicji.|Jako wartości portu i protokołu można użyć wartości **https**. Pracujemy nad Ustaw to pole opcjonalne, gdy nazwa FQDN znaczniki są używane.|
|Przenoszenie zapory do innej grupy zasobów lub subskrypcji nie jest obsługiwane|Przenoszenie zapory do innej grupy zasobów lub subskrypcji nie jest obsługiwane.|Obsługa tej funkcji jest w naszym harmonogramie działania. Aby przenieść zaporę do innej grupy zasobów lub subskrypcji, musisz usunąć bieżące wystąpienie i utworzyć je ponownie w nowej grupie zasobów lub subskrypcji.|
|Zakres portów w regułach sieci i aplikacji|Porty są ograniczone do 64 000, ponieważ porty o dużych numerach są zarezerwowane do zarządzania i sprawdzania kondycji. |Pracujemy nad złagodzenie tego ograniczenia.|
|Może uzyskać maskowane alerty analizy zagrożeń|Reguły sieciowych z lokalizacją docelową 80/443 dla ruchu wychodzącego masek filtrowania zagrożeń analizy alertów w przypadku skonfigurowania do alertu tylko trybu.|Utwórz, filtrowanie ruchu wychodzącego dla 80/443 przy użyciu reguł aplikacji. Możesz też zmienić tryb analizy zagrożeń **alertów i odmawiać go**.|
|Zapora usługi Azure używa usługi Azure DNS tylko do rozpoznawania nazw|Zaporę platformy Azure rozpoznaje tylko przy użyciu usługi Azure DNS nazwy FQDN. Niestandardowego serwera DNS nie jest obsługiwane. Ma to żadnego wpływu na rozpoznawanie nazw DNS w innych podsieciach.|Pracujemy nad złagodzenie tego ograniczenia.|
|Usługa Azure zapory SNAT DNAT nie działa dla prywatnych adresów IP miejsc docelowych.|Pomoc techniczna platformy Azure zapory SNAT/DNAT jest ograniczona do Internetu ruchu wychodzącego/przychodzącego. SNAT/DNAT obecnie nie działa dla prywatnych adresów IP miejsc docelowych. Na przykład typu gwiazda szprychą.|To aktualne ograniczenie.|
|Nie można usunąć pierwsze publiczny adres IP|Nie można usunąć publiczny adres IP przypisany do zapory, chyba, że Zapora jest cofnięty lub usunięty.|To jest celowe.|
|Jeśli dodasz lub usuniesz publiczny adres IP, reguły DNAT może nie działać tymczasowo.| Jeśli dodasz lub usuniesz publicznego adresu IP do zapory uruchomione, istniejące połączenie przychodzące za pomocą reguł DNAT może nie działać na 40 – 120 sekund.|Jest to ograniczenie w publicznej wersji zapoznawczej dla tej funkcji.|
|Strefy dostępności można skonfigurować tylko podczas wdrażania.|Strefy dostępności można skonfigurować tylko podczas wdrażania. Nie można skonfigurować strefy dostępności, po wdrożeniu zapory.|To jest celowe.|

## <a name="next-steps"></a>Kolejne kroki

- [Samouczek: wdrażanie i konfigurowanie usługi Azure Firewall w witrynie Azure Portal](tutorial-firewall-deploy-portal.md)
- [Wdrażanie usługi Azure Firewall przy użyciu szablonu](deploy-template.md)
- [Tworzenie środowiska testowego usługi Azure Firewall](scripts/sample-create-firewall-test.md)
