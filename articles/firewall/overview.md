---
title: Co to jest usługa Azure Firewall?
description: Dowiedz się więcej o funkcjach usługi Azure Firewall.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 7/16/2018
ms.author: victorh
ms.openlocfilehash: 5e8048dc6b49a0f6c9a465e82a7278e491351034
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574134"
---
# <a name="what-is-azure-firewall"></a>Co to jest usługa Azure Firewall?

Azure Firewall to zarządzana, sieciowa usługa zabezpieczeń oparta na chmurze, która zabezpiecza zasoby usługi Azure Virtual Network. Jest to w pełni stanowa zapora oferowana jako usługa, z wbudowaną wysoką dostępnością i możliwością nieograniczonego skalowania w chmurze. 

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

![Omówienie zapory](media/overview/firewall-overview.png)



Możesz centralnie tworzyć, wymuszać i rejestrować zasady łączności aplikacji i sieci w subskrypcjach i sieciach wirtualnych. Usługa Azure Firewall korzysta ze statycznego publicznego adresu IP dla zasobów sieci wirtualnej, co umożliwia zewnętrznym zaporom identyfikowanie ruchu pochodzącego z sieci wirtualnej.  Usługa jest w pełni zintegrowana z usługą Azure Monitor na potrzeby rejestrowania i analiz.

## <a name="features"></a>Funkcje

Usługa Azure Firewall w publicznej wersji zapoznawczej oferuje następujące funkcje:

### <a name="built-in-high-availability"></a>Wbudowana wysoka dostępność
Wysoka dostępność jest wbudowana, nie potrzeba więc żadnych dodatkowych modułów równoważenia obciążenia ani konfigurowania żadnych funkcji.

### <a name="unrestricted-cloud-scalability"></a>Skalowalność w chmurze bez ograniczeń 
Usługa Azure Firewall umożliwia skalowanie bez ograniczeń, odpowiednio do zmieniających się przepływów ruchu sieciowego — nie trzeba więc dostosowywać budżetu do okresów szczytowego ruchu.

### <a name="fqdn-filtering"></a>Filtrowanie według nazw FQDN 
Możesz ograniczyć wychodzący ruch protokołu HTTP/S do konkretnej listy w pełni kwalifikowanych nazw domen, również z użyciem symboli wieloznacznych. Ta funkcja nie wymaga kończenia żądań protokołu SSL.

### <a name="network-traffic-filtering-rules"></a>Reguły filtrowania ruchu sieciowego

Można centralnie tworzyć reguły filtrowania sieci (*zezwalania* lub *blokowania*) na podstawie źródłowego i docelowego adresu IP, portu i protokołu. Usługa Azure Firewall jest w pełni stanowa, więc możesz rozróżniać autentyczne pakiety w ramach różnych typów połączeń. Reguły są wymuszane i rejestrowane w wielu subskrypcjach i sieciach wirtualnych.

### <a name="outbound-snat-support"></a>Obsługa translacji adresów sieciowych źródła (SNAT) dla ruchu wychodzącego

Wszystkie adresy IP wychodzącego ruchu sieciowego są tłumaczone na publiczny adres IP usługi Azure Firewall (translacja adresów sieciowych źródła, SNAT). Możesz zidentyfikować ruch pochodzący z sieci wirtualnej do zdalnych internetowych miejsc docelowych i zezwalać na niego.

### <a name="azure-monitor-logging"></a>Rejestrowanie w usłudze Azure Monitor

Wszystkie zdarzenia są zintegrowane z usługą Azure Monitor, co umożliwia archiwizowanie dzienników na koncie magazynu, przesyłanie strumieniowe zdarzeń do centrum Event Hub lub wysyłanie ich do usługi Log Analytics.

## <a name="known-issues"></a>Znane problemy

W usłudze Azure Firewall w publicznej wersji zapoznawczej występują następujące znane problemy:


|Problem  |Opis  |Środki zaradcze  |
|---------|---------|---------|
|Współdziałanie z sieciowymi grupami zabezpieczeń     |Jeśli sieciowa grupa zabezpieczeń zostanie zastosowana w podsieci zapory, może zablokować wychodzącą łączność z Internetem, nawet jeśli sieciowa grupa zabezpieczeń jest skonfigurowana tak, aby zezwalać na wychodzący dostęp do Internetu. Wychodzące połączenia internetowe są oznaczone jako pochodzące z elementu VirtualNetwork, a miejscem docelowym jest Internet. Sieciowa grupa zabezpieczeń domyślnie *zezwala* na ruch między elementami VirtualNetwork, ale nie wtedy, gdy miejscem docelowym jest Internet.|Aby rozwiązać tej problem, dodaj następującą regułę ruchu przychodzącego do sieciowej grupy zabezpieczeń zastosowanej w podsieci zapory:<br><br>Źródło: VirtualNetwork Porty źródłowe: wszystkie <br><br>Miejsce docelowe: wszystkie Porty docelowe: wszystkie <br><br>Protokół: wszystkie Dostęp: zezwalaj|
|Konflikt z funkcją Just in time (JIT) usługi Azure Security Center|W przypadku dostępu JIT do maszyny wirtualnej znajdującej się w podsieci z trasą zdefiniowaną przez użytkownika, która wskazuje usługę Azure Firewall jako bramę domyślną, funkcja JIT usługi Azure Security Center nie działa. Jest to rezultat asymetrycznego trasowania — pakiet przychodzi za pośrednictwem publicznego adresu IP maszyny wirtualnej (funkcja JIT umożliwiła dostęp), ale ścieżka powrotna biegnie przez zaporę, która odrzuca pakiet, ponieważ nie ustanowiono sesji w zaporze.|Aby wyeliminować ten problem, należy umieścić maszyny wirtualne z funkcją JIT w oddzielnej podsieci, w której nie ma zdefiniowanej przez użytkownika trasy do zapory.|
|Model typu gwiazda z globalną komunikacją równorzędną nie działa|Model typu gwiazda, w którym koncentrator i zapora są wdrożone w jednym regionie świadczenia usługi Azure, a szprychy, połączone z koncentratorem za pomocą globalnej wirtualnej sieci równorzędnej, w innym, nie jest obsługiwany.|Aby uzyskać więcej informacji, zobacz temat dotyczący [tworzenia, zmieniania lub usuwania wirtualnych sieci równorzędnych](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints)|
Reguły filtrowania dla protokołów innych niż TCP/UDP (na przykład ICMP) nie działają dla ruchu powiązanego z Internetem|Reguły filtrowania sieci dla protokołów innych niż TCP/UPD nie działają z funkcją SNAT i publicznym adresem IP. Protokoły inne niż TCP/UDP są obsługiwane między podsieciami szprych i sieciami wirtualnymi.|Usługa Azure Firewall korzysta ze standardowego modułu równoważenia obciążenia, [który obecnie nie obsługuje funkcji SNAT dla protokołów IP](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). Firma Microsoft analizuje potencjalne opcje obsługi tego scenariusza w przyszłych wersjach.



## <a name="next-steps"></a>Następne kroki

- [Samouczek: wdrażanie i konfigurowanie usługi Azure Firewall w witrynie Azure Portal](tutorial-firewall-deploy-portal.md)
- [Wdrażanie usługi Azure Firewall przy użyciu szablonu](deploy-template.md)
- [Tworzenie środowiska testowego usługi Azure Firewall](scripts/sample-create-firewall-test.md)

