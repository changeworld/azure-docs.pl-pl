---
title: Alerty z usługi Azure Monitor dla maszyn wirtualnych
description: W tym artykule opisano sposób tworzenia reguł alertów na podstawie danych dotyczących wydajności zebranych przez usługę Azure Monitor dla maszyn wirtualnych.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/23/2020
ms.openlocfilehash: 987537d8497b3d8f2728941334d8328320ec6997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289604"
---
# <a name="how-to-create-alerts-from-azure-monitor-for-vms"></a>Jak tworzyć alerty z usługi Azure Monitor dla maszyn wirtualnych
[Alerty w usłudze Azure Monitor](../platform/alerts-overview.md) proaktywnie powiadamiają o interesujących danych i wzorcach w danych monitorowania. Usługa Azure Monitor dla maszyn wirtualnych nie zawiera wstępnie skonfigurowanych reguł alertów, ale można utworzyć własne na podstawie danych, które zbiera. Ten artykuł zawiera wskazówki dotyczące tworzenia reguł alertów, w tym zestaw przykładowych zapytań.


## <a name="alert-rule-types"></a>Typy reguł alertów
Usługa Azure Monitor ma [różne typy reguł alertów](../platform/alerts-overview.md#what-you-can-alert-on) na podstawie danych używanych do tworzenia alertu. Wszystkie dane zbierane przez usługę Azure Monitor dla maszyn wirtualnych są przechowywane w dziennikach usługi Azure Monitor, która obsługuje [alerty dziennika.](../platform/alerts-log.md) Obecnie nie można używać [alertów metryk](../platform/alerts-log.md) z danymi wydajności zebranymi z usługi Azure Monitor dla maszyn wirtualnych, ponieważ dane nie są zbierane w metrykach usługi Azure Monitor. Aby zebrać dane dla alertów metryk, zainstaluj [rozszerzenie diagnostyki](../platform/diagnostics-extension-overview.md) dla maszyn wirtualnych systemu Windows lub [agenta Telegraf](../platform/collect-custom-metrics-linux-telegraf.md) dla maszyn wirtualnych z systemem Linux, aby zebrać dane dotyczące wydajności w metrykach.

Istnieją dwa typy alertów dziennika w usłudze Azure Monitor:

- [Liczba alertów wyników](../platform/alerts-unified-log.md#number-of-results-alert-rules) utworzyć pojedynczy alert, gdy kwerenda zwraca co najmniej określoną liczbę rekordów. Są one idealne dla danych nienuliczalnych, takich jak zdarzenia systemu Windows i Syslog zebrane przez [agenta usługi Log Analytics](../platform/log-analytics-agent.md) lub do analizowania trendów wydajności na wielu komputerach.
- [Alerty pomiaru metryki](../platform/alerts-unified-log.md#metric-measurement-alert-rules) utworzyć oddzielny alert dla każdego rekordu w kwerendzie, która ma wartość, która przekracza próg zdefiniowany w regule alertu. Te reguły alertów są idealne dla danych dotyczących wydajności zebranych przez usługę Azure Monitor dla maszyn wirtualnych, ponieważ mogą tworzyć indywidualne alerty dla każdego komputera.


## <a name="alert-rule-walkthrough"></a>Przewodnik po regule alertu
W tej sekcji oparcie się o utworzeniu reguły alertu pomiaru metryki przy użyciu danych o wydajności z usługi Azure Monitor dla maszyn wirtualnych. Tego podstawowego procesu można użyć z różnych zapytań dziennika do alertów na różnych liczników wydajności.

Zacznij od utworzenia nowej reguły alertów zgodnie z procedurą w [temacie Tworzenie, wyświetlanie i zarządzanie alertami dziennika przy użyciu usługi Azure Monitor](../platform/alerts-log.md). W przypadku **zasobu**wybierz obszar roboczy usługi Log Analytics używany przez maszyny wirtualne usługi Azure Monitor w ramach subskrypcji. Ponieważ zasób docelowy dla reguł alertów dziennika jest zawsze obszarem roboczym usługi Log Analytics, kwerenda dziennika musi zawierać dowolny filtr dla określonych maszyn wirtualnych lub zestawów skalowania maszyny wirtualnej. 

W przypadku **warunku** reguły alertu użyj jednego z zapytań w [poniższej sekcji](#sample-alert-queries) jako **kwerendy wyszukiwania**. Kwerenda musi zwrócić właściwość liczbową o nazwie *AggregatedValue*. Należy podsumować dane według komputera, dzięki czemu można utworzyć oddzielny alert dla każdej maszyny wirtualnej, która przekracza próg.

W **logice Alert**wybierz opcję **Pomiar metryki,** a następnie podaj **wartość progu**. W **obszarze Alert wyzwalacza na podstawie**określić, ile razy próg musi zostać przekroczony przed utworzeniem alertu. Na przykład prawdopodobnie nie obchodzi, czy procesor przekracza próg raz, a następnie wraca do normy, ale zależy Ci, czy nadal przekracza próg przez wiele kolejnych pomiarów.

**Oceniane na podstawie** sekcji określa, jak często kwerenda jest uruchamiana i przedział czasu dla kwerendy. W poniższym przykładzie kwerenda będzie uruchamiana co 15 minut i oceniać wartości wydajności zebrane w ciągu poprzednich 15 minut.


![Reguła alertu pomiaru metryki](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>Przykładowe kwerendy alertów
Następujące zapytania mogą być używane z regułą alertu pomiaru metryki przy użyciu danych dotyczących wydajności zebranych przez usługę Azure Monitor dla maszyn wirtualnych. Każdy podsumowuje dane według komputera, tak aby alert został utworzony dla każdego komputera o wartości przekraczającej próg.

### <a name="cpu-utilization"></a>Wykorzystanie procesora

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-mb"></a>Dostępna pamięć w MB

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-percentage"></a>Dostępna pamięć w procentach

```kusto
InsightsMetrics 
| where Origin == "vm.azm.ms" 
| where Namespace == "Memory" and Name == "AvailableMB" 
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])
| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0 
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---all-disks-on-each-computer"></a>Używany dysk logiczny - wszystkie dyski na każdym komputerze

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---individual-disks"></a>Używany dysk logiczny - pojedyncze dyski

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk
```

### <a name="logical-disk-iops"></a>IOPS dysku logicznego

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk
```

### <a name="logical-disk-data-rate"></a>Szybkość danych dysku logicznego

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk
```

### <a name="network-interfaces-bytes-received---all-interfaces"></a>Odebrane bajty interfejsów sieciowych — wszystkie interfejsy

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="network-interfaces-bytes-received---individual-interfaces"></a>Odebrane bajty interfejsów sieciowych — pojedyncze interfejsy

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="network-interfaces-bytes-sent---all-interfaces"></a>Bajty interfejsów sieciowych - wszystkie interfejsy

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="network-interfaces-bytes-sent---individual-interfaces"></a>Bajty interfejsów sieciowych - indywidualne interfejsy

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="virtual-machine-scale-set"></a>Zestaw skalowania maszyn wirtualnych
Modyfikuj za pomocą identyfikatora subskrypcji, grupy zasobów i nazwy zestawu skalowania maszyny wirtualnej.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/my-vm-scaleset" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="specific-virtual-machine"></a>Określona maszyna wirtualna
Modyfikuj za pomocą identyfikatora subskrypcji, grupy zasobów i nazwy maszyny Wirtualnej.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId =~ "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m)
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-subscription"></a>Wykorzystanie procesora CPU dla wszystkich zasobów obliczeniowych w ramach subskrypcji
Zmodyfikuj za pomocą identyfikatora subskrypcji.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/")
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-resource-group"></a>Wykorzystanie procesora CPU dla wszystkich zasobów obliczeniowych w grupie zasobów
Modyfikuj za pomocą identyfikatora subskrypcji i grupy zasobów.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/"
or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId

```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [alertach w usłudze Azure Monitor](../platform/alerts-overview.md).
- Dowiedz się więcej o [kwerendach dziennika przy użyciu danych z usługi Azure Monitor dla maszyn wirtualnych.](vminsights-log-search.md)
