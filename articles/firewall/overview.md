---
title: Co to jest usługa Azure Firewall?
description: Azure Firewall to zarządzana, sieciowa usługa zabezpieczeń oparta na chmurze, która zabezpiecza zasoby usługi Azure Virtual Network.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 04/08/2020
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: bb4b654bd0b3591ebaa1bd217020095319a4938c
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381913"
---
# <a name="what-is-azure-firewall"></a>Co to jest usługa Azure Firewall?

![Certyfikacja ICSA](media/overview/icsa-cert-firewall-small.png)

Azure Firewall to zarządzana, sieciowa usługa zabezpieczeń oparta na chmurze, która zabezpiecza zasoby usługi Azure Virtual Network. Jest to w pełni stanowa zapora jako usługa z wbudowaną wysoką dostępnością i możliwością nieograniczonego skalowania w chmurze.

![Omówienie zapory](media/overview/firewall-threat.png)

Możesz centralnie tworzyć, wymuszać i rejestrować zasady łączności aplikacji i sieci w subskrypcjach i sieciach wirtualnych. Usługa Azure Firewall korzysta ze statycznego publicznego adresu IP dla zasobów sieci wirtualnej, co umożliwia zewnętrznym zaporom identyfikowanie ruchu pochodzącego z sieci wirtualnej.  Usługa jest w pełni zintegrowana z usługą Azure Monitor na potrzeby rejestrowania i analiz.

Usługa Azure Firewall oferuje następujące funkcje:

## <a name="built-in-high-availability"></a>Wbudowana wysoka dostępność

Wysoka dostępność jest wbudowana, więc nie są wymagane żadne dodatkowe moduły równoważenia obciążenia i nie ma nic, co należy skonfigurować.

## <a name="availability-zones"></a>Strefy dostępności

Zaporę platformy Azure można skonfigurować podczas wdrażania, aby objąć wiele stref dostępności w celu zwiększenia dostępności. Dzięki strefom dostępności dostępność zwiększa się do 99,99% czasu pracy. Aby uzyskać więcej informacji, zobacz Umowę dotyczącą poziomu usług zapory azure [(SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). 99,99% dostępności SLA jest oferowana po wybraniu dwóch lub więcej stref dostępności.

Możesz również skojarzyć Zaporę platformy Azure z określoną strefą tylko ze względów zbliżeniowych, korzystając ze standardowej umowy SLA 99,95%.

Nie ma żadnych dodatkowych kosztów dla zapory wdrożonej w strefie dostępności. Istnieją jednak dodatkowe koszty dla przychodzących i wychodzących transferów danych skojarzonych ze strefami dostępności. Aby uzyskać więcej informacji, zobacz [Szczegóły dotyczące cen przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/).

Strefy dostępności zapory azure są dostępne w regionach, które obsługują strefy dostępności. Aby uzyskać więcej informacji, zobacz [Co to są strefy dostępności na platformie Azure?](../availability-zones/az-overview.md#services-support-by-region)

> [!NOTE]
> Strefy dostępności można skonfigurować tylko podczas wdrażania. Nie można skonfigurować istniejącej zapory tak, aby zawierała strefy dostępności.

Aby uzyskać więcej informacji na temat stref dostępności, zobacz [Co to są strefy dostępności na platformie Azure?](../availability-zones/az-overview.md)

## <a name="unrestricted-cloud-scalability"></a>Skalowalność w chmurze bez ograniczeń

Usługa Azure Firewall umożliwia skalowanie bez ograniczeń, odpowiednio do zmieniających się przepływów ruchu sieciowego — nie trzeba więc dostosowywać budżetu do okresów szczytowego ruchu.

## <a name="application-fqdn-filtering-rules"></a>Reguły filtrowania w pełni kwalifikowanych nazw domen aplikacji

Ruch HTTP/S wychodzący lub ruch SQL platformy Azure (wersja zapoznawcza) można ograniczyć do określonej listy w pełni kwalifikowanych nazw domen (FQDN), w tym symboli wieloznacznych. Ta funkcja nie wymaga zakończenia protokołu TLS.

## <a name="network-traffic-filtering-rules"></a>Reguły filtrowania ruchu sieciowego

Można centralnie tworzyć reguły filtrowania sieci (*zezwalania* lub *blokowania*) na podstawie źródłowego i docelowego adresu IP, portu i protokołu. Usługa Azure Firewall jest w pełni stanowa, więc możesz rozróżniać autentyczne pakiety w ramach różnych typów połączeń. Reguły są wymuszane i rejestrowane w wielu subskrypcjach i sieciach wirtualnych.

## <a name="fqdn-tags"></a>Tagi w pełni kwalifikowanych nazw domen

Tagi FQDN ułatwiają zezwalanie na dobrze znany ruch sieciowy usługi platformy Azure za pośrednictwem zapory. Załóżmy na przykład, że chcesz zezwolić na ruch sieciowy z witryny Windows Update przez zaporę. Tworzysz regułę aplikacji i dołączasz tag „Windows Update”. Teraz ruch sieciowy z witryny Windows Update może przechodzić przez zaporę.

## <a name="service-tags"></a>Tagi usługi

Tag usługi reprezentuje grupę prefiksów adresów IP, aby zminimalizować złożoność tworzenia reguły zabezpieczeń. Nie można utworzyć własnego tagu usługi ani określić, które adresy IP są zawarte w tagu. Firma Microsoft zarządza prefiksami adresów obejmowanymi przez tag usługi i automatycznie aktualizuje tag usługi, gdy adresy ulegną zmianie.

## <a name="threat-intelligence"></a>Analiza zagrożeń

Filtrowanie oparte na analizie zagrożeń można włączyć, aby zapora ostrzegała i odmawiała ruchu ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z źródła danych microsoft Threat Intelligence.

## <a name="outbound-snat-support"></a>Obsługa translacji adresów sieciowych źródła (SNAT) dla ruchu wychodzącego

Wszystkie adresy IP wychodzącego ruchu sieciowego są tłumaczone na publiczny adres IP usługi Azure Firewall (translacja adresów sieciowych źródła, SNAT). Możesz zidentyfikować ruch pochodzący z sieci wirtualnej do zdalnych internetowych miejsc docelowych i zezwalać na niego. Zapora azure nie SNAT, gdy docelowy adres IP jest prywatny zakres IP na [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). 

Jeśli twoja organizacja używa zakresu publicznego adresu IP dla sieci prywatnych, Zapora Azure spowoduje snat ruchu do jednego z zapory prywatnych adresów IP w usłudze AzureFirewallSubnet. Zaporę platformy Azure można skonfigurować tak, aby **nie** była dostępna w zakresie publicznego adresu IP. Aby uzyskać więcej informacji, zobacz [Zakresy prywatnych adresów IP zapory azure SNAT](snat-private-range.md).

## <a name="inbound-dnat-support"></a>Obsługa technologii DNAT dla ruchu przychodzącego

Przychodzący ruch sieciowy do publicznego adresu IP zapory jest tłumaczony (Tłumaczenie docelowego adresu sieciowego) i filtrowany na prywatne adresy IP w sieciach wirtualnych.

## <a name="multiple-public-ip-addresses"></a>Wiele publicznych adresów IP

Z zaporą można skojarzyć wiele publicznych adresów IP (do 100).

Umożliwia to następujące scenariusze:

- **DNAT** — można przetłumaczyć wiele standardowych wystąpień portów na serwery wewnętrznej bazy danych. Na przykład jeśli masz dwa publiczne adresy IP, możesz przetłumaczyć port TCP 3389 (RDP) dla obu adresów IP.
- **SNAT** — dostępne są dodatkowe porty dla wychodzących połączeń SNAT, co zmniejsza ryzyko wyczerpania portów SNAT. W tej chwili Zapora platformy Azure losowo wybiera źródłowy publiczny adres IP do użycia dla połączenia. Jeśli masz jakiekolwiek filtrowanie podrzędne w sieci, musisz zezwolić na wszystkie publiczne adresy IP skojarzone z zaporą.

## <a name="azure-monitor-logging"></a>Rejestrowanie w usłudze Azure Monitor

Wszystkie zdarzenia są zintegrowane z usługą Azure Monitor, co umożliwia archiwizowanie dzienników na koncie magazynu, przesyłanie strumieniowe zdarzeń do centrum Event Hub lub wysyłanie ich do dzienników usługi Azure Monitor.

## <a name="certifications"></a>Certyfikaty

Zapora Azure jest zgodna z payment card industry (PCI), Service Organization Controls (SOC), International Organization for Standardization (ISO) i ICSA Labs. Aby uzyskać więcej informacji, zobacz [Certyfikaty zgodności zapory platformy Azure](compliance-certifications.md).


## <a name="known-issues"></a>Znane problemy

W usłudze Azure Firewall występują następujące znane problemy:

|Problem  |Opis  |Środki zaradcze  |
|---------|---------|---------|
Reguły filtrowania dla protokołów innych niż TCP/UDP (na przykład ICMP) nie działają dla ruchu powiązanego z Internetem|Reguły filtrowania sieci dla protokołów innych niż TCP/UDP nie działają z SNAT do publicznego adresu IP. Protokoły inne niż TCP/UDP są obsługiwane między podsieciami szprych i sieciami wirtualnymi.|Usługa Azure Firewall korzysta ze standardowego modułu równoważenia obciążenia, [który obecnie nie obsługuje funkcji SNAT dla protokołów IP](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). Analizujemy opcje obsługi tego scenariusza w przyszłej wersji.|
|Brak obsługi protokołu ICMP w programie PowerShell i interfejsie wiersza polecenia|Usługi Azure PowerShell i cli nie obsługują protokołu ICMP jako prawidłowego protokołu w regułach sieciowych.|Nadal można używać protokołu ICMP jako protokołu za pośrednictwem portalu i interfejsu API REST. Pracujemy nad tym, aby wkrótce dodać ICMP w programie PowerShell i CLI.|
|Tagi FQDN wymagają ustawienia protokołu i portu|Reguły aplikacji ze znacznikami FQDN wymagają definicji protokołu port:.|Jako wartości portu i protokołu można użyć wartości **https**. Pracujemy nad tym, aby to pole było opcjonalne, gdy używane są znaczniki FQDN.|
|Przenoszenie zapory do innej grupy zasobów lub subskrypcji nie jest obsługiwane|Przenoszenie zapory do innej grupy zasobów lub subskrypcji nie jest obsługiwane.|Obsługa tej funkcji jest w naszym harmonogramie działania. Aby przenieść zaporę do innej grupy zasobów lub subskrypcji, musisz usunąć bieżące wystąpienie i utworzyć je ponownie w nowej grupie zasobów lub subskrypcji.|
|Alerty analizy zagrożeń mogą zostać zamaskowane|Reguły sieciowe z miejscem docelowym 80/443 dla wychodzących filtrowania maski alerty analizy zagrożeń po skonfigurowaniu do trybu alertów tylko.|Tworzenie filtrowania wychodzącego dla 80/443 przy użyciu reguł aplikacji. Możesz też zmienić tryb analizy zagrożeń na **Alert i Odmów**.|
|Zapora azure używa usługi Azure DNS tylko do rozpoznawania nazw|Zapora azure rozwiązuje nazwy FQDN przy użyciu tylko usługi Azure DNS. Niestandardowy serwer DNS nie jest obsługiwany. Nie ma wpływu na rozpoznawanie dns w innych podsieciach.|Pracujemy nad złagodzeniem tego ograniczenia.|
|Usługa Azure Firewall SNAT/DNAT nie działa w przypadku prywatnych miejsc docelowych adresów IP|Obsługa usługi Azure Firewall SNAT/DNAT jest ograniczona do danych wychodzących/przychodzących w dostępie internetowym. SNAT/DNAT nie działa obecnie w prywatnych miejscach docelowych IP. Na przykład, mówił do mówił.|Jest to aktualne ograniczenie.|
|Nie można usunąć pierwszej publicznej konfiguracji ip|Każdy publiczny adres IP zapory platformy Azure jest przypisany do *konfiguracji IP*.  Pierwsza konfiguracja IP jest przypisywana podczas wdrażania zapory i zazwyczaj zawiera również odwołanie do podsieci zapory (chyba że jest skonfigurowana jawnie inaczej za pośrednictwem wdrożenia szablonu). Nie można usunąć tej konfiguracji IP, ponieważ spowoduje to de-przydzielenie zapory. Nadal można zmienić lub usunąć publiczny adres IP skojarzony z tą konfiguracją IP, jeśli zapora ma co najmniej jeden inny publiczny adres IP dostępny do użycia.|Jest to celowe.|
|Strefy dostępności można skonfigurować tylko podczas wdrażania.|Strefy dostępności można skonfigurować tylko podczas wdrażania. Nie można skonfigurować stref dostępności po wdrożeniu zapory.|Jest to celowe.|
|SNAT na połączeniach przychodzących|Oprócz DNAT połączenia za pośrednictwem publicznego adresu IP zapory (przychodzącego) są sNATed do jednego z prywatnych adresów IP zapory. To wymaganie dzisiaj (również dla aktywnych/aktywnych wynaużania NVA) w celu zapewnienia routingu symetrycznego.|Aby zachować oryginalne źródło dla protokołu HTTP/S, należy rozważyć użycie nagłówków [XFF.](https://en.wikipedia.org/wiki/X-Forwarded-For) Na przykład należy użyć usługi, takich jak [Usługi Azure Drzwi frontowe](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) lub [usługi Azure Application Gateway](../application-gateway/rewrite-http-headers.md) przed zaporą. Można również dodać WAF jako część usługi Azure Front Door i łańcuch do zapory.
|Obsługa filtrowania FQDN SQL tylko w trybie proxy (port 1433)|W przypadku usługi Azure SQL Database, usługi Azure SQL Data Warehouse i wystąpienia zarządzanego sql platformy Azure:<br><br>Podczas podglądu filtrowanie FQDN SQL jest obsługiwane tylko w trybie proxy (port 1433).<br><br>W przypadku usługi Azure SQL IaaS:<br><br>Jeśli używasz portów niestandardowych, można określić te porty w regułach aplikacji.|W przypadku języka SQL w trybie przekierowania (domyślne, jeśli łączysz się z poziomu platformy Azure), można zamiast tego filtrować dostęp przy użyciu tagu usługi SQL jako część reguł sieciowych Zapory platformy Azure.
|Ruch wychodzący na porcie TCP 25 jest niedozwolony| Wychodzące połączenia SMTP korzystające z portu TCP 25 są blokowane. Port 25 jest używany głównie do nieuwierzyleni dostarczania wiadomości e-mail. Jest to domyślne zachowanie platformy dla maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz więcej [Rozwiązywanie problemów z łącznością smtp wychodzących na platformie Azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md). Jednak w przeciwieństwie do maszyn wirtualnych obecnie nie jest możliwe włączenie tej funkcji w zaporze platformy Azure. Uwaga: aby zezwolić na uwierzytelnione smtp (port 587) lub SMTP na porcie innym niż 25, upewnij się, że skonfigurowano regułę sieci, a nie regułę aplikacji, ponieważ inspekcja SMTP nie jest obecnie obsługiwana.|Postępuj zgodnie z zalecaną metodą wysyłania wiadomości e-mail, zgodnie z dokumentami w artykule dotyczącym rozwiązywania problemów z smtp. Możesz też wykluczyć maszynę wirtualną, która potrzebuje wychodzącego dostępu SMTP z domyślnej trasy do zapory. Zamiast tego skonfiguruj dostęp wychodzący bezpośrednio do Internetu.
|Aktywny ftp nie jest obsługiwany|Aktywny protokół FTP jest wyłączony w zaporze platformy Azure w celu ochrony przed atakami odbijania FTP za pomocą polecenia FTP PORT.|Zamiast tego można użyć pasywnego FTP. Nadal należy jawnie otworzyć porty TCP 20 i 21 na zaporze.
|Wskaźnik wykorzystania portów SNAT pokazuje 0%|Metryka wykorzystania portu SNAT zapory platformy Azure może wykazywać 0% użycia nawet wtedy, gdy są używane porty SNAT. W takim przypadku przy użyciu metryki jako część metryki kondycji zapory zapewnia niepoprawny wynik.|Ten problem został rozwiązany i wdrożenie do produkcji jest ukierunkowane na maj 2020. W niektórych przypadkach ponowne wdrożenie zapory rozwiązuje problem, ale nie jest spójne. Jako obejście pośrednie należy używać tylko stanu kondycji zapory, aby wyszukać *stan=zdegradowany*, nie dla *stanu=w złej kondycji*. Wyczerpanie portu będzie wyświetlane jako *zdegradowane*. *Nie w dobrej kondycji* jest zarezerwowany do wykorzystania w przyszłości, gdy są bardziej metryki, aby wpłynąć na kondycję zapory.
|Protokół DNAT nie jest obsługiwany z włączoną obsługą tunelowania wymuszonego|Zapory wdrożone z włączoną obsługą tunelowania wymuszonego nie obsługują dostępu przychodzącego z Internetu z powodu routingu asymetrycznego.|Jest to zgodnie z projektem ze względu na routing asymetryczny. Ścieżka powrotu dla połączeń przychodzących przechodzi przez zaporę lokalną, która nie została nawiązana.
|Wychodzący pasywny protokół FTP nie działa w przypadku zapór z wieloma publicznymi adresami IP.|Pasywny FTP ustanawia różne połączenia dla kanałów sterowania i transmisji danych. Gdy Zapora z wieloma publicznymi adresami IP wysyła dane wychodzące, losowo wybiera jeden ze swoich publicznych adresów IP dla źródłowego adresu IP. Protokół FTP kończy się niepowodzeniem, gdy kanały danych i kontroli używają różnych źródłowych adresów IP.|Planowana jest jawna konfiguracja SNAT. W międzyczasie należy rozważyć użycie jednego adresu IP w tej sytuacji.|
|W metryce NetworkRuleHit brakuje wymiaru protokołu|Metryka ApplicationRuleHit umożliwia filtrowanie protokołu opartego, ale tej funkcji brakuje w odpowiedniej metryki NetworkRuleHit.|Poprawka jest badana.|
|Aktualizacje konfiguracji mogą trwać średnio pięć minut.|Aktualizacja konfiguracji zapory platformy Azure może trwać średnio od trzech do pięciu minut, a aktualizacje równoległe nie są obsługiwane.|Poprawka jest badana.

## <a name="next-steps"></a>Następne kroki

- [Samouczek: wdrażanie i konfigurowanie usługi Azure Firewall w witrynie Azure Portal](tutorial-firewall-deploy-portal.md)
- [Wdrażanie usługi Azure Firewall przy użyciu szablonu](deploy-template.md)
- [Tworzenie środowiska testowego usługi Azure Firewall](scripts/sample-create-firewall-test.md)
