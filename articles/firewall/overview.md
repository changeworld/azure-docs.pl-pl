---
title: Co to jest usługa Azure Firewall?
description: Dowiedz się więcej o funkcjach usługi Azure Firewall.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 7/10/2019
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: da82f6c93045b38aed887860c6d5c45c93b2260b
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703950"
---
# <a name="what-is-azure-firewall"></a>Co to jest usługa Azure Firewall?

Azure Firewall to zarządzana, sieciowa usługa zabezpieczeń oparta na chmurze, która zabezpiecza zasoby usługi Azure Virtual Network. Jest w pełni stanowych zapory jako usługa z wbudowaną wysoką dostępność i skalowalność chmury bez ograniczeń.

![Omówienie zapory](media/overview/firewall-threat.png)

Możesz centralnie tworzyć, wymuszać i rejestrować zasady łączności aplikacji i sieci w subskrypcjach i sieciach wirtualnych. Usługa Azure Firewall korzysta ze statycznego publicznego adresu IP dla zasobów sieci wirtualnej, co umożliwia zewnętrznym zaporom identyfikowanie ruchu pochodzącego z sieci wirtualnej.  Usługa jest w pełni zintegrowana z usługą Azure Monitor na potrzeby rejestrowania i analiz.

Usługa Azure Firewall oferuje następujące funkcje:

## <a name="built-in-high-availability"></a>Wbudowana wysoka dostępność

Wysoka dostępność jest wbudowany, nie dodatkowych modułów równoważenia obciążenia są wymagane i nie ma nic, które trzeba skonfigurować.

## <a name="availability-zones"></a>Strefy dostępności

Podczas wdrażania na wielu strefach dostępności na potrzeby zwiększonej dostępności można skonfigurować zaporę platformy Azure. Dzięki strefom dostępności poziomu dostępności zwiększa dostępność przez 99,99% czasu. Aby uzyskać więcej informacji, zobacz zapory usługi Azure [Umowa dotycząca poziomu usług (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). Czas działania 99,99% umowy SLA jest oferowana po wybraniu co najmniej dwóch strefach dostępności.

Można również skojarzyć zapory usługi Azure do określonej strefy tylko ze względów odległości między elementami przy użyciu usług standardowa SLA 99,95% czasu.

Nie ma żadnych dodatkowych kosztów, zaporę wdrożonej w strefie dostępności. Jednakże istnieją dodatkowe koszty transferu danych przychodzących i wychodzących skojarzonych stref dostępności. Aby uzyskać więcej informacji, zobacz [przepustowość — szczegóły cennika](https://azure.microsoft.com/pricing/details/bandwidth/).

Strefy dostępności zapory platformy Azure są dostępne w regionach, które obsługują strefy dostępności. Aby uzyskać więcej informacji, zobacz [co to są strefy dostępności na platformie Azure?](../availability-zones/az-overview.md#services-support-by-region)

> [!NOTE]
> Strefy dostępności można skonfigurować tylko podczas wdrażania. Nie można skonfigurować istniejących zaporę, aby uwzględnić stref dostępności.

Aby uzyskać więcej informacji o strefach dostępności, zobacz [co to są strefy dostępności na platformie Azure?](../availability-zones/az-overview.md)

## <a name="unrestricted-cloud-scalability"></a>Skalowalność w chmurze bez ograniczeń

Usługa Azure Firewall umożliwia skalowanie bez ograniczeń, odpowiednio do zmieniających się przepływów ruchu sieciowego — nie trzeba więc dostosowywać budżetu do okresów szczytowego ruchu.

## <a name="application-fqdn-filtering-rules"></a>Reguły filtrowania w pełni kwalifikowanych nazw domen aplikacji

Można ograniczyć ruch wychodzący protokołu HTTP/Https lub ruch usługi Azure SQL (wersja zapoznawcza) na określoną listę w pełni kwalifikowanych nazw domen (FQDN) w tym symbole wieloznaczne. Ta funkcja nie wymaga kończenia żądań SSL.

## <a name="network-traffic-filtering-rules"></a>Reguły filtrowania ruchu sieciowego

Można centralnie tworzyć reguły filtrowania sieci (*zezwalania* lub *blokowania*) na podstawie źródłowego i docelowego adresu IP, portu i protokołu. Usługa Azure Firewall jest w pełni stanowa, więc możesz rozróżniać autentyczne pakiety w ramach różnych typów połączeń. Reguły są wymuszane i rejestrowane w wielu subskrypcjach i sieciach wirtualnych.

## <a name="fqdn-tags"></a>Tagi w pełni kwalifikowanych nazw domen

Tagi w pełni kwalifikowanych nazw domen zezwalają na znany ruch sieciowy usługi Azure przez zaporę. Załóżmy na przykład, że chcesz zezwolić na ruch sieciowy z witryny Windows Update przez zaporę. Tworzysz regułę aplikacji i dołączasz tag „Windows Update”. Teraz ruch sieciowy z witryny Windows Update może przechodzić przez zaporę.

## <a name="service-tags"></a>Tagi usługi

Tag usługi reprezentuje grupę prefiksów adresów IP, aby zminimalizować złożoność tworzenia reguły zabezpieczeń. Nie można utworzyć własnego tagu usługi ani określić adresów IP są zawarte w obrębie tag. Firma Microsoft zarządza prefiksami adresów obejmowanymi przez tag usługi i automatycznie aktualizuje tag usługi, gdy adresy ulegną zmianie.

## <a name="threat-intelligence"></a>Analiza zagrożeń

Filtrowanie oparte na analizie zagrożeń można włączyć dla zapory, aby wysyłać alerty oraz blokować ruch ze znanych złośliwych adresów IP i domen i do nich. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft.

## <a name="outbound-snat-support"></a>Obsługa translacji adresów sieciowych źródła (SNAT) dla ruchu wychodzącego

Wszystkie adresy IP wychodzącego ruchu sieciowego są tłumaczone na publiczny adres IP usługi Azure Firewall (translacja adresów sieciowych źródła, SNAT). Możesz zidentyfikować ruch pochodzący z sieci wirtualnej do zdalnych internetowych miejsc docelowych i zezwalać na niego. Zaporę platformy Azure nie SNAT, jeśli docelowy adres IP jest prywatny zakres adresów IP na [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Jeśli Twoja organizacja korzysta z publicznego zakres adresów IP dla sieci prywatnych, zapory usługi Azure będzie użyć funkcji SNAT względem ruchu do jednej z prywatnych adresów IP zapory w AzureFirewallSubnet.

## <a name="inbound-dnat-support"></a>Obsługa technologii DNAT dla ruchu przychodzącego

Ruch sieciowy przychodzący do publicznego adresu IP zapory jest przetwarzany przy użyciu technologii DNAT i filtrowany do prywatnych adresów IP w sieciach wirtualnych.

## <a name="multiple-public-ip-addresses"></a>Wiele publicznych adresów IP

> [!IMPORTANT]
> Zaporę platformy Azure przy użyciu wiele publicznych adresów IP jest dostępna za pośrednictwem programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure, REST i szablony. Interfejs użytkownika portalu jest dodawany do regionów stopniowo i będą dostępne we wszystkich regionach, po zakończeniu wdrożenia.


Można skojarzyć wiele publicznych adresów IP (maksymalnie 100) z zaporą.

Umożliwia to obsługę następujących scenariuszy:

- **DNAT** — wielu wystąpień standardowego portu można tłumaczyć na serwerach wewnętrznej bazy danych. Na przykład jeśli masz dwa publiczne adresy IP, może dokonywać translacji portu TCP 3389 (RDP) dla obu adresów IP.
- **SNAT** — dodatkowe porty są dostępne dla połączeń SNAT wychodzących, zmniejszyć prawdopodobieństwo SNAT wyczerpanie portów. W tej chwili zapory usługi Azure losowo wybiera publiczny adres IP źródłowego na potrzeby połączenia. Jeśli masz wszystkie podrzędne filtrowania w sieci, należy zezwolić na wszystkie publiczne adresy IP skojarzone z zaporą.

## <a name="azure-monitor-logging"></a>Rejestrowanie w usłudze Azure Monitor

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
|Nie można usunąć pierwsze publiczny Konfiguracja protokołu IP|Każdy publiczny adres IP zapory usługi Azure jest przypisany do *konfigurację adresu IP*.  Konfiguracja pierwszego adresu IP jest przypisywana podczas wdrażania zapory, a zwykle zawiera odwołanie do podsieci zapory (o ile nie skonfigurowano jawnie inaczej za pośrednictwem wdrożenia szablonu). Nie można usunąć tej konfiguracji adresu IP, ponieważ jego może cofnąć przydziału zapory. Nadal można zmienić lub usunąć publiczny adres IP skojarzony z tą konfiguracją adresu IP, jeśli Zapora ma co najmniej jedno inne publiczny adres IP dostępne do użycia.|To jest celowe.|
|Strefy dostępności można skonfigurować tylko podczas wdrażania.|Strefy dostępności można skonfigurować tylko podczas wdrażania. Nie można skonfigurować strefy dostępności, po wdrożeniu zapory.|To jest celowe.|
|SNAT dla połączeń przychodzących|Oprócz DNAT, połączeń za pośrednictwem zapory, publiczny adres IP (ruch przychodzący) czy funkcji SNAT i uzyskać do jednego z zapory prywatnych adresów IP. To wymaganie, już dziś (również dla urządzeń WUS aktywny/aktywny) aby upewnić się, symetryczny routing.|Aby zachować oryginalne źródło dla protokołu HTTP/Https, należy rozważyć użycie [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For) nagłówków. Na przykład, takich jak używać usługi [Azure drzwiami frontowymi](../frontdoor/front-door-http-headers-protocol.md#front-door-service-to-backend) przed zaporą. Zapora aplikacji sieci Web można również dodać jako część drzwi wejściowe platformy Azure i sieć, do zapory.
|Obsługa tylko w trybie serwera proxy (port 1433) filtrowania nazwa FQDN programu SQL|Dla usługi Azure SQL Database wystąpienia zarządzanego Azure SQL Data Warehouse i Azure SQL:<br><br>W trakcie okresu zapoznawczego filtrowanie nazwa FQDN programu SQL jest obsługiwana w trybie proxy tylko (port 1433).<br><br>Dla usługi Azure SQL IaaS:<br><br>Jeśli używasz niestandardowych portach można określić te porty w regułach aplikacji.|Dla programu SQL w trybie przekierowania, który jest domyślnym, jeśli połączenie z w obrębie platformy Azure, zamiast tego można filtrować dostępu przy użyciu tagu usługi SQL w ramach reguł sieci zapory usługi Azure.

## <a name="next-steps"></a>Kolejne kroki

- [Samouczek: wdrażanie i konfigurowanie usługi Azure Firewall w witrynie Azure Portal](tutorial-firewall-deploy-portal.md)
- [Wdrażanie usługi Azure Firewall przy użyciu szablonu](deploy-template.md)
- [Tworzenie środowiska testowego usługi Azure Firewall](scripts/sample-create-firewall-test.md)
