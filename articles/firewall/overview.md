---
title: Co to jest Zapora systemu Azure?
description: Dowiedz się więcej o funkcjach zapory platformy Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 10/08/2019
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 5be1a673ac456b0896ce83afcb469e4ac6b8b40a
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001329"
---
# <a name="what-is-azure-firewall"></a>Co to jest Zapora systemu Azure?

Zapora systemu Azure to zarządzana, oparta na chmurze usługa zabezpieczeń sieci, która chroni zasoby Virtual Network platformy Azure. Jest to w pełni stanowa Zapora jako usługa z wbudowaną wysoką dostępnością i nieograniczoną skalowalnością chmury.

![Omówienie Zapory](media/overview/firewall-threat.png)

Można centralnie tworzyć, wymuszać i rejestrować zasady łączności aplikacji i sieci w ramach subskrypcji i sieci wirtualnych. Zapora platformy Azure używa statycznego publicznego adresu IP dla zasobów sieci wirtualnej, dzięki czemu zapory zewnętrzne mogą identyfikować ruch pochodzący z sieci wirtualnej.  Usługa jest w pełni zintegrowana z Azure Monitor na potrzeby rejestrowania i analizy.

Zapora systemu Azure oferuje następujące funkcje:

## <a name="built-in-high-availability"></a>Wbudowana wysoka dostępność

Wysoka dostępność jest wbudowana, więc nie są wymagane żadne dodatkowe moduły równoważenia obciążenia i nie trzeba konfigurować żadnych usług.

## <a name="availability-zones"></a>Strefy dostępności

Zaporę platformy Azure można skonfigurować podczas wdrażania w celu rozdzielenia wielu Strefy dostępności w celu zwiększenia dostępności. W przypadku Strefy dostępności czas dostępności wzrosną do 99,99% czasu. Aby uzyskać więcej informacji, zobacz Umowa dotycząca poziomu usług zapory platformy Azure [(SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). Umowa SLA na czas działania na 99,99% jest oferowana w przypadku wybrania co najmniej dwóch Strefy dostępności.

Można także skojarzyć zaporę platformy Azure z określoną strefą tylko z przyczyn bliskości przy użyciu standardowej umowy SLA usługi 99,95%.

Dla zapory wdrożonej w strefie dostępności nie ma dodatkowych kosztów. Istnieją jednak dodatkowe koszty dla przychodzących i wychodzących transferów danych skojarzonych z Strefy dostępności. Aby uzyskać więcej informacji, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/bandwidth/)dotyczącego przepustowości.

Strefy dostępności zapory platformy Azure są dostępne w regionach, które obsługują Strefy dostępności. Aby uzyskać więcej informacji, zobacz [co to jest strefy dostępności na platformie Azure?](../availability-zones/az-overview.md#services-support-by-region)

> [!NOTE]
> Strefy dostępności można skonfigurować tylko podczas wdrażania. Nie można skonfigurować istniejącej zapory do dołączania Strefy dostępności.

Aby uzyskać więcej informacji na temat Strefy dostępności, zobacz [co to jest strefy dostępności na platformie Azure?](../availability-zones/az-overview.md)

## <a name="unrestricted-cloud-scalability"></a>Nieograniczona skalowalność chmury

Zapora systemu Azure można skalować w górę tak, jak potrzeba zmiany przepływów ruchu sieciowego, dzięki czemu nie trzeba budżetować ruchu szczytowego.

## <a name="application-fqdn-filtering-rules"></a>Reguły filtrowania nazw FQDN aplikacji

Możesz ograniczyć ruch wychodzący HTTP/S lub ruch usługi Azure SQL (wersja zapoznawcza) do określonej listy w pełni kwalifikowanych nazw domen (FQDN), w tym symboli wieloznacznych. Ta funkcja nie wymaga zakończenia protokołu SSL.

## <a name="network-traffic-filtering-rules"></a>Reguły filtrowania ruchu sieciowego

Można centralnie *utworzyć reguły* *filtrowania sieci dla* źródła i docelowego adresu IP, portu i protokołu. Zapora platformy Azure jest w pełni stanowa, dzięki czemu może odróżnić legalne pakiety dla różnych typów połączeń. Reguły są wymuszane i rejestrowane między wieloma subskrypcjami i sieciami wirtualnymi.

## <a name="fqdn-tags"></a>Tagi FQDN

Tagi FQDN ułatwiają poznanie dobrze znanego ruchu sieciowego usługi platformy Azure przez zaporę. Załóżmy na przykład, że chcesz zezwolić na Windows Update ruchu sieciowego przez zaporę. Tworzysz regułę aplikacji i Dołącz tag Windows Update. Teraz ruch sieciowy z Windows Update może przepływać przez zaporę.

## <a name="service-tags"></a>Tagi usług

Tag usługi reprezentuje grupę prefiksów adresów IP, która pomaga zminimalizować złożoność tworzenia reguł zabezpieczeń. Nie można utworzyć własnego tagu usługi ani określić, które adresy IP znajdują się w tagu. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

## <a name="threat-intelligence"></a>Analiza zagrożeń

Filtrowanie na podstawie analizy zagrożeń może być włączone, aby zapora mogła zgłaszać i odrzucać ruch z/do znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą ze źródła analizy zagrożeń firmy Microsoft.

## <a name="outbound-snat-support"></a>Obsługa wychodzącego ruchu źródłowego

Wszystkie wirtualne adresy IP ruchu wychodzącego są tłumaczone na publiczny adres IP zapory platformy Azure (translator adresów sieciowych źródła). Możesz identyfikować i zezwalać na ruch pochodzący z sieci wirtualnej do zdalnych miejsc docelowych Internetu. Zapora platformy Azure nie ma protokołu IPSec, gdy docelowy adres IP jest prywatnym zakresem adresów IP na [organizację IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Jeśli Twoja organizacja używa zakresu publicznego adresu IP dla sieci prywatnych, Zapora platformy Azure przywróci ruch do jednego z prywatnych adresów IP zapory w AzureFirewallSubnet.

## <a name="inbound-dnat-support"></a>Obsługa DNAT ruchu przychodzącego

Ruch sieciowy przychodzący do publicznego adresu IP zapory jest tłumaczony (translacja adresów sieciowych dla sieci docelowych) i filtrowany na prywatne adresy IP w sieciach wirtualnych.

## <a name="multiple-public-ip-addresses"></a>Wiele publicznych adresów IP

Za pomocą zapory można skojarzyć wiele publicznych adresów IP (do 100).

Dzięki temu można wykonać następujące scenariusze:

- **DNAT** — wiele standardowych wystąpień portów można przetłumaczyć na serwery zaplecza. Na przykład jeśli masz dwa publiczne adresy IP, możesz przetłumaczyć port TCP 3389 (RDP) dla obu adresów IP.
- Reportcy **adresów sieciowych** — dodatkowe porty są dostępne dla wychodzących połączeń z reportem adresów sieciowych, co zmniejsza prawdopodobieństwo wyczerpania portów dla tego elementu. W tej chwili Zapora platformy Azure losowo wybiera źródłowy publiczny adres IP, który ma być używany w połączeniu. Jeśli masz jakieś filtrowanie podrzędne w sieci, musisz zezwolić na wszystkie publiczne adresy IP skojarzone z zaporą.

## <a name="azure-monitor-logging"></a>Rejestrowanie Azure Monitor

Wszystkie zdarzenia są zintegrowane z Azure Monitor, co pozwala na archiwizowanie dzienników na koncie magazynu, przesyłanie strumieniowe zdarzeń do centrum zdarzeń lub wysyłanie ich do dzienników Azure Monitor.

## <a name="pci-soc-and-iso-compliant"></a>Zgodne ze standardami PCI, SOC i ISO

Zapora platformy Azure to usługa Payment Card Industry (PCI), kontrolki organizacji usług (SOC) i Międzynarodowa Organizacja Normalizacyjna (ISO). Obsługuje ona obecnie SOC 1 typu 2, SOC 2 typu 2, SOC 3, PCI DSS i ISO 27001, 27018, 20000-1, 22301, 9001, 27017.

Aby uzyskać więcej informacji, zobacz [Przewodnik zgodności firmy Microsoft](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide).

## <a name="known-issues"></a>Znane problemy

Zapora platformy Azure ma następujące znane problemy:

|Problem  |Opis  |Ograniczenie  |
|---------|---------|---------|
Reguły filtrowania sieci dla protokołów innych niż TCP/UDP (na przykład ICMP) nie działają w przypadku ruchu związanego z Internetem|Reguły filtrowania sieci dla protokołów innych niż TCP/UDP nie działają z przystawcy do publicznego adresu IP. Protokoły inne niż TCP/UDP są obsługiwane między podsieciami szprych i sieci wirtualnych.|Zapora platformy Azure używa usługa Load Balancer w warstwie Standardowa, [która obecnie nie obsługuje protokołu przesyłania dla protokołów IP](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). Szukamy opcji obsługi tego scenariusza w przyszłej wersji.|
|Brak obsługi protokołu ICMP dla programu PowerShell i interfejsu wiersza polecenia|Azure PowerShell i interfejs wiersza polecenia nie obsługują protokołu ICMP jako prawidłowego protokołu w regułach sieci.|Nadal jest możliwe używanie protokołu ICMP jako protokołu za pośrednictwem portalu i interfejsu API REST. Pracujemy nad dodaniem protokołu ICMP w programie PowerShell i interfejsie wiersza polecenia.|
|Tagi FQDN wymagają protokołu: port do ustawienia|Reguły aplikacji ze znacznikami FQDN wymagają portu: Definicja protokołu.|Możesz użyć **protokołu HTTPS** jako portu: wartość protokołu. Pracujemy nad tym, aby to pole było opcjonalne, gdy używane są Tagi FQDN.|
|Przeniesienie zapory do innej grupy zasobów lub subskrypcji nie jest obsługiwane|Przeniesienie zapory do innej grupy zasobów lub subskrypcji nie jest obsługiwane.|Obsługa tej funkcji znajduje się na naszej mapie drogowej. Aby przenieść zaporę do innej grupy zasobów lub subskrypcji, należy usunąć bieżące wystąpienie i utworzyć je ponownie w nowej grupie zasobów lub subskrypcji.|
|Zakres portów w regułach sieci i aplikacji|Porty są ograniczone do 64 000, ponieważ wysokie porty są zarezerwowane dla sond zarządzania i kondycji. |Pracujemy nad ograniczeniem tego ograniczenia.|
|Alerty analizy zagrożeń mogą zostać zamaskowane|Reguły sieciowe z lokalizacją docelową 80/443 dla wyzwalanych masek filtrowania zdarzeń analizy zagrożeń po skonfigurowaniu trybu tylko alertu.|Utwórz filtrowanie wychodzące dla 80/443 przy użyciu reguł aplikacji. Lub zmień tryb analizy zagrożeń na **alert i Odmów**.|
|Zapora platformy Azure używa Azure DNS tylko do rozpoznawania nazw|Zapora platformy Azure rozwiązuje nazwy FQDN tylko przy użyciu Azure DNS. Niestandardowy serwer DNS nie jest obsługiwany. Nie ma to wpływu na rozpoznawanie nazw DNS w innych podsieciach.|Pracujemy nad ograniczeniem tego ograniczenia.|
|Nie działa prywatne adresy IP zapory platformy Azure/DNAT|Obsługa adresów IP/DNAT zapory platformy Azure jest ograniczona do Internetu/ruchu przychodzącego. W przypadku prywatnych miejsc docelowych IP/DNAT nie działa obecnie. Na przykład satelity.|Jest to bieżące ograniczenie.|
|Nie można usunąć pierwszej konfiguracji publicznego adresu IP|Każdy publiczny adres IP zapory platformy Azure jest przypisywany do *konfiguracji adresu IP*.  Pierwsza konfiguracja protokołu IP jest przypisywana podczas wdrażania zapory, a także zazwyczaj zawiera odwołanie do podsieci zapory (chyba że jest inaczej skonfigurowana za pośrednictwem wdrożenia szablonu). Nie można usunąć tej konfiguracji protokołu IP, ponieważ spowodowałoby to cofnięcie alokacji zapory. Nadal można zmienić lub usunąć publiczny adres IP skojarzony z tą konfiguracją IP, Jeśli Zapora ma co najmniej jeden publiczny adres IP do użycia.|Jest to zaprojektowane.|
|Strefy dostępności można skonfigurować tylko podczas wdrażania.|Strefy dostępności można skonfigurować tylko podczas wdrażania. Nie można skonfigurować Strefy dostępności po wdrożeniu zapory.|Jest to zaprojektowane.|
|Przychodzący translator adresów sieciowych dla połączeń przychodzących|Oprócz DNAT połączenia za pośrednictwem publicznego adresu IP (przychodzącego) zapory są podłączony do jednego z prywatnych IP zapory. Ten wymóg jest obecny (również w przypadku usługi Active/Active urządzeń WUS) w celu zapewnienia symetrycznego routingu.|Aby zachować oryginalne źródło dla protokołu HTTP/S, rozważ użycie nagłówków [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For) . Na przykład użyj usługi, takiej jak moje [drzwi platformy Azure](../frontdoor/front-door-http-headers-protocol.md#front-door-service-to-backend) przed zaporą. Możesz również dodać WAF w ramach frontonu i łańcucha do zapory platformy Azure.
|Obsługa filtrowania nazw FQDN programu SQL tylko w trybie proxy (port 1433)|W przypadku Azure SQL Database, Azure SQL Data Warehouse i wystąpienia zarządzanego Azure SQL:<br><br>W trakcie okresu zapoznawczego filtrowanie FQDN programu SQL jest obsługiwane tylko w trybie proxy (port 1433).<br><br>W przypadku usługi Azure SQL IaaS:<br><br>Jeśli używasz portów niestandardowych, możesz określić te porty w regułach aplikacji.|W przypadku usługi SQL w trybie przekierowywania, który jest domyślnym ustawieniem, jeśli łączysz się z platformy Azure, możesz zamiast tego filtrować dostęp przy użyciu znacznika usługi SQL jako części reguł sieci zapory platformy Azure.
|Ruch wychodzący na porcie TCP 25 jest niedozwolony| Wychodzące połączenia SMTP używające portu 25 TCP są blokowane. Port 25 jest używany głównie do dostarczania nieuwierzytelnionych wiadomości e-mail. Jest to domyślne zachowanie platformy dla maszyn wirtualnych. Aby uzyskać więcej informacji, Zobacz więcej [Rozwiązywanie problemów z łącznością wychodzącą SMTP na platformie Azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md). Jednak w przeciwieństwie do maszyn wirtualnych nie jest obecnie możliwe włączenie tej funkcji w zaporze platformy Azure.|Wykonaj zalecaną metodę wysyłania wiadomości e-mail zgodnie z opisem w artykule dotyczącym rozwiązywania problemów z protokołem SMTP. Alternatywnie Wyklucz maszynę wirtualną wymagającą dostępu wychodzącego SMTP z domyślnej trasy do zapory, a zamiast tego Skonfiguruj dostęp wychodzący bezpośrednio do Internetu.

## <a name="next-steps"></a>Następne kroki

- [Samouczek: wdrażanie i Konfigurowanie zapory platformy Azure przy użyciu Azure Portal](tutorial-firewall-deploy-portal.md)
- [Wdrażanie zapory platformy Azure przy użyciu szablonu](deploy-template.md)
- [Tworzenie środowiska testowego zapory platformy Azure](scripts/sample-create-firewall-test.md)
