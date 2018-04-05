---
title: Wprowadzenie do Monitora sieci platformy Azure | Dokumentacja firmy Microsoft
description: Ta strona zawiera omówienie usługi obserwatora sieciowego do monitorowania i Środek wywołujący sieć połączona zasobami na platformie Azure
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: 14bc2266-99e3-42a2-8d19-bd7257fec35e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: jdial
ms.openlocfilehash: 792b96e4f5ba5dc0f2f943f099a2fee339407d66
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="azure-network-monitoring-overview"></a>Omówienie monitorowania sieci platformy Azure

Klienci kompilacji na trasie sieci na platformie Azure poprzez organizowanie i tworzenia różnych zasobów poszczególnych sieci, takich jak sieci wirtualnej, ExpressRoute, bramy aplikacji i usług równoważenia obciążenia. Monitorowanie jest dostępne na każdym z zasobów sieciowych. Firma Microsoft odwoływać się do monitorowania jako poziomu monitorowania zasobów.

Kompleksowe sieci może mieć złożonych konfiguracji i interakcje między zasobami, tworzenie złożonych scenariuszy, które wymagają oparta na scenariuszu monitorowanie za pomocą Monitora sieci.

W tym artykule opisano scenariusz oraz monitorowania poziomu zasobów. Monitorowanie sieci na platformie Azure jest kompleksowy i obejmuje dwie szerokie kategorie:

* [**Monitor sieci** ](#network-watcher) -oparta na scenariuszu monitorowania jest dostarczana z funkcji obserwatora sieciowego. Ta usługa obejmuje przechwytywania pakietów, następnego przeskoku, przepływ IP Sprawdź widok grupy zabezpieczeń, dzienniki przepływu NSG. Scenariusz poziomu monitorowania udostępnia widok pełnego zasobów sieciowych, w przeciwieństwie do monitorowania zasobów poszczególnych sieci.
* [**Monitorowanie zasobów** ](#network-resource-level-monitoring) -poziomu monitorowania zasobów składa się z czterech funkcji, dzienników diagnostycznych metryki, rozwiązywanie problemów i kondycji zasobów. Te funkcje są tworzone na poziomie zasobów sieciowych.

## <a name="network-watcher"></a>Network Watcher

Monitor sieci jest regionalnych usługa, która umożliwia monitorowanie i diagnozowanie warunki na poziomie sieci scenariusz w, do i z platformy Azure. Diagnostyka sieci i narzędzi wizualizacji dostępnych z obserwatora sieciowego pomagają zrozumieć, diagnozowanie i Uzyskaj wgląd do sieci na platformie Azure.

Obserwatora sieciowego ma obecnie następujące możliwości:

* **[Topologia](network-watcher-topology-overview.md)**  -Wyświetla sieci poziomu pokazywanie różnych połączeń i skojarzenia między zasobami sieci w grupie zasobów.
* **[Zmienna przechwytywania pakietów](network-watcher-packet-capture-overview.md)**  -przechwytuje pakiet danych do i z maszyny wirtualnej. Zaawansowane opcje filtrowania i dostosować formanty, takie jak ustawianie czasu i rozmiaru ograniczenia zapewniają wszechstronność. Dane pakietu mogą być przechowywane w magazynie obiektów blob lub na dysku lokalnym w formacie CAP.
* **[Sprawdź przepływ IP](network-watcher-ip-flow-verify-overview.md)**  — sprawdza, czy pakiet jest dozwolony lub niedozwolony na podstawie przepływ informacji 5-elementowej pakietu parametrów (docelowy adres IP, źródłowy adres IP, Port docelowy, Port źródłowy i Protocol). Jeśli pakiet jest zabroniony przez grupę zabezpieczeń, zwracany jest reguła i grupy, którego pakiet.
* **[Następny przeskok](network-watcher-next-hop-overview.md)**  — określa następnego skoku dla pakietów przesyłane w sieci szkieletowej Azure, dzięki któremu można zdiagnozować wszelkie błędnie skonfigurowane trasy zdefiniowane przez użytkownika.
* **[Widok grupy zabezpieczeń](network-watcher-security-group-view-overview.md)**  -pobiera reguły skuteczne i zastosowane zabezpieczeń, które są stosowane na maszynie Wirtualnej.
* **[Rejestrowanie przepływu NSG](network-watcher-nsg-flow-logging-overview.md)**  — dzienniki przepływu dla grup zabezpieczeń sieci umożliwiają przechwytywanie dzienniki związane z ruchu, który ma być dozwolony lub odrzucany przez zasady zabezpieczeń w grupie. Przepływ został zdefiniowany przez informacji 5-elementowej — źródłowy adres IP, docelowy adres IP, Port źródłowy, Port docelowy i protokołu.
* **[Brama sieci wirtualnej i rozwiązywanie problemów z połączenia](network-watcher-troubleshoot-manage-rest.md)**  — umożliwia rozwiązywanie problemów z bramy sieci wirtualnej i połączenia.
* **[Limity subskrypcji sieci](#network-subscription-limits)**  — umożliwia wyświetlenie wykorzystania zasobów sieci ograniczeń.
* **[Konfigurowanie dziennika diagnostyki](#diagnostic-logs)**  — zapewnia jeden Aby włączyć lub wyłączyć dzienników diagnostycznych do zasobów sieciowych w grupie zasobów.
* **[Rozwiązywanie problemów dotyczących połączeń](network-watcher-connectivity-overview.md)**  -sprawdza możliwość nawiązywania bezpośredniego połączenia TCP z maszyny wirtualnej z danym punktem końcowym wzbogaconych Azure kontekstu.
* **[Monitor połączenia](connection-monitor.md)**  — monitorowanie problemy opóźnienia i konfigurację między maszyny wirtualnej platformy Azure i adres IP, za pomocą źródłowego i docelowego adresu IP i portu.

### <a name="role-based-access-control-rbac-in-network-watcher"></a>Kontrola dostępu oparta na rolach (RBAC) w obserwatora sieciowego

Sieć obserwatora [modelu based kontroli dostępu (RBAC)](../active-directory/role-based-access-control-what-is.md). Następujące uprawnienia są wymagane przez obserwatora sieciowego. Należy się upewnić, że rola używane do inicjowania interfejsów API obserwatora sieciowego lub za pomocą Monitora sieci z portalu ma wymagane uprawnienia dostępu.

|Zasób| Uprawnienie|
|---|---| 
|Microsoft.Storage/ |Odczyt|
|Microsoft.Authorization/| Odczyt| 
|Microsoft.Resources/subscriptions/resourceGroups/| Odczyt|
|Microsoft.Storage/storageAccounts/listServiceSas/ | Akcja|
|Microsoft.Storage/storageAccounts/listAccountSas/ |Akcja|
|Microsoft.Storage/storageAccounts/listKeys/ | Akcja|
|Microsoft.Compute/virtualMachines/ |Odczyt|
|Microsoft.Compute/virtualMachines/ |Zapisywanie|
|Microsoft.Compute/virtualMachineScaleSets/ |Odczyt|
|Microsoft.Compute/virtualMachineScaleSets/ |Zapisywanie|
|Microsoft.Network/networkWatchers/packetCaptures/ |Odczyt|
|Microsoft.Network/networkWatchers/packetCaptures/| Zapisywanie|
|Microsoft.Network/networkWatchers/packetCaptures/| Usuwanie|
|Microsoft.Network/networkWatchers/ |Zapisywanie |
|Microsoft.Network/networkWatchers/| Odczyt |
|Microsoft.Insights/alertRules/ |*|
|Microsoft.Support/ | *|

### <a name="network-subscription-limits"></a>Limity subskrypcji sieci

Limity subskrypcji sieci zapewniają szczegółowe informacje dotyczące użycia każdego z zasobów sieciowych w ramach subskrypcji w regionie przed maksymalną liczbę dostępnych zasobów.

![limit subskrypcji sieci][nsl]

## <a name="network-resource-level-monitoring"></a>Poziom monitorowania zasobów sieciowych

Następujące funkcje są dostępne dla poziomu monitorowania zasobów:

### <a name="audit-log"></a>Dziennik inspekcji

Operacje wykonywane w ramach konfiguracji sieci są rejestrowane. Dzienniki te mogą być wyświetlane w portalu Azure lub pobrany przy użyciu narzędzi firmy Microsoft, takich jak usługi Power BI lub narzędzi innych firm. Dzienniki inspekcji są dostępne za pośrednictwem portalu, programu PowerShell, interfejsu wiersza polecenia i interfejsu API Rest. Aby uzyskać więcej informacji na dziennikach inspekcji, zobacz [inspekcji operacji za pomocą Menedżera zasobów](../resource-group-audit.md)

Dzienniki inspekcji są dostępne dla operacje wykonywane na wszystkich zasobów sieciowych.

### <a name="metrics"></a>Metryki

Metryki są miar wydajności i liczniki zebrane w danym okresie czasu. Metryki są obecnie dostępne dla bramy aplikacji. Metryki może służyć do wyzwolenia alerty oparte na wartości progowej. Zobacz [diagnostyki bramy aplikacji](../application-gateway/application-gateway-diagnostics.md) do wyświetlania, jak metryki może służyć do tworzenia alertów.

![Widok wskaźników][metrics]

### <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Okresowe i spontanicznych zdarzenia są tworzone przez zasobów sieciowych i zarejestrowane na kontach magazynu, wysyłane do Centrum zdarzeń lub analizy dzienników. Te dzienniki wgląd w kondycję zasobu. Te dzienniki można wyświetlać w narzędzi, takich jak Power BI i analizy dzienników. Aby dowiedzieć się wyświetlić dzienniki diagnostyczne, odwiedź stronę [analizy dzienników](../log-analytics/log-analytics-azure-networking-analytics.md).

Dzienniki diagnostyczne są dostępne dla [modułu równoważenia obciążenia](../load-balancer/load-balancer-monitor-log.md), [grup zabezpieczeń sieci](../virtual-network/virtual-network-nsg-manage-log.md), trasy i [brama aplikacji w](../application-gateway/application-gateway-diagnostics.md).

Wyświetl dzienniki diagnostyczne zawiera obserwatora sieciowego. Ten widok zawiera wszystkich zasobów sieciowych, które obsługują rejestrowania diagnostycznego. Z tego widoku można włączyć i wyłączyć zasobów sieciowych, łatwo i szybko.

![dzienniki][logs]

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Rozwiązywania problemów z bloku obsługi w portalu jest dostarczany z zasobów sieciowych dzisiaj zdiagnozować typowe problemy związane z pojedynczego zasobu. To środowisko jest dostępne dla następujących zasobów sieci - ExpressRoute, Brama sieci VPN bramy aplikacji, dzienniki zabezpieczeń sieciowych, trasy, DNS, usługi równoważenia obciążenia i Menedżera ruchu. Aby dowiedzieć się więcej na temat rozwiązywania problemów poziomu zasobów, odwiedź stronę [diagnozowanie i rozwiązywanie problemów z rozwiązywaniem problemów Azure](https://azure.microsoft.com/blog/azure-troubleshoot-diagonse-resolve-issues/)

![informacje dotyczące rozwiązywania problemów][TS]

### <a name="resource-health"></a>Kondycja zasobów

Kondycja zasobu sieciowego znajduje się w regularnych odstępach czasu. Takie zasoby obejmują bramy sieci VPN i tunel VPN. Kondycja zasobów są dostępne w portalu Azure. Aby dowiedzieć się więcej na temat kondycji zasobów, odwiedź stronę [Przegląd kondycji zasobów](../resource-health/resource-health-overview.md)

## <a name="next-steps"></a>Następne kroki

Po zapoznawanie obserwatora sieciowego, aby dowiedzieć się do:

Czy przechwytywania pakietów, na maszynie Wirtualnej, odwiedzając [przechwytywania pakietów zmiennej w portalu Azure](network-watcher-packet-capture-manage-portal.md)

Wykonywanie aktywnego monitorowania i diagnostyki za pomocą [alert wyzwolony przechwytywania pakietów](network-watcher-alert-triggered-packet-capture.md).

Wykrywanie luk w zabezpieczeniach z [analizowanie przechwytywania pakietów z programu Wireshark](network-watcher-deep-packet-inspection.md), za pomocą narzędzi typu open source.

Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) platformy Azure.

<!--Image references-->
[TS]: ./media/network-watcher-monitoring-overview/troubleshooting.png
[logs]: ./media/network-watcher-monitoring-overview/logs.png
[metrics]: ./media/network-watcher-monitoring-overview/metrics.png
[nsl]: ./media/network-watcher-monitoring-overview/nsl.png











