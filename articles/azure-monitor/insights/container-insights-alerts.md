---
title: Tworzenie alertów wydajności przy użyciu usługi Azure Monitor dla kontenerów | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak tworzyć niestandardowe alerty bazujące na zapytaniach dzienników dla pamięci i Procesora CPU za pomocą usługi Azure Monitor dla kontenerów.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: magoedte
ms.openlocfilehash: ebe2c2b488e3d71597dd24f5504a14dd7ce6671e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791545"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Jak skonfigurować alerty dotyczące problemów z wydajnością w usłudze Azure Monitor dla kontenerów
Usługa Azure Monitor dla kontenerów monitoruje wydajność obciążeń kontenerów, które są wdrażane w usłudze Azure Container Instances lub zarządzanych klastrów Kubernetes, które są hostowane na platformie Azure Kubernetes Service (AKS).

W tym artykule opisano sposób włączania alertów w następujących sytuacjach:

* Gdy wykorzystanie procesora CPU lub pamięci na węzłach klastra przekracza określoną wartość progową
* Gdy użycie procesora CPU lub pamięci na każdy kontener w kontrolerze przekracza określoną wartość progową porównaniu limit, który jest ustawiony na odpowiadający jej zasób
* *Niegotowe* zlicza stan węzła
*  *Nie powiodło się*, *oczekujące*, *nieznany*, *systemem*, lub *Powodzenie* liczby zasobników fazy

Aby otrzymywać alerty dla wysokie użycie procesora CPU i użycie pamięci na węzłach klastra, użyj zapytań, które są dostarczane do utworzenia alertu dotyczącego metryki lub alertu pomiaru metryki. Alerty metryki mają mniejsze opóźnienia niż alertów dzienników. Ale alertów dzienników zapewnia zaawansowane zapytania i większego zaawansowania. Alerty zapytania porównania datetime bieżącej przy użyciu dzienników *teraz* operatora i będzie godzinę wstecz. (Usługa azure Monitor dla kontenerów przechowuje wszystkie daty w formacie uniwersalnego czasu koordynowanego (UTC)).

Jeśli nie znasz przy użyciu alertów usługi Azure Monitor, zobacz [Przegląd alertów na platformie Microsoft Azure](../platform/alerts-overview.md) przed rozpoczęciem. Aby dowiedzieć się więcej na temat alertów, które używają dziennika zapytań, zobacz [alerty dzienników w usłudze Azure Monitor](../platform/alerts-unified-log.md). Aby uzyskać więcej informacji dotyczących alertów dotyczących metryk, zobacz [alertów dotyczących metryk w usłudze Azure Monitor](../platform/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Zapytania wyszukiwania w dzienniku wykorzystania zasobów
Zapytania w tej sekcji scenariusza każdego alertu. Są one używane w kroku 7 [Utwórz alert](#create-an-alert-rule) dalszej części tego artykułu.

Następujące zapytanie oblicza średnie wykorzystanie procesora CPU jako średnia wykorzystanie procesora CPU w węzłach Członkowskich co minutę.  

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

Następujące zapytanie oblicza średnie wykorzystanie pamięci jako średnia wykorzystania pamięci w węzłach Członkowskich co minutę.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```
>[!IMPORTANT]
>Następujące zapytania, użyj wartości symboli zastępczych \<your nazwa klastra > i \<Twojej nazwy kontrolera > do reprezentowania Twojej klastra i kontrolera. Zastąp je wartościami dla danego środowiska, podczas konfigurowania alertów.

Następujące zapytanie oblicza średnie wykorzystanie Procesora dla wszystkich kontenerów w kontrolerze jako średnia wykorzystanie procesora CPU każdego wystąpienia kontenera w kontrolerze co minutę. Wartość jest wartością procentową limit dla kontenera.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

Następujące zapytanie oblicza średnie użycie pamięci przez program wszystkie kontenery w kontrolerze jako średnia użycie pamięci przez każde wystąpienie kontenera w kontrolerze co minutę. Wartość jest wartością procentową limit dla kontenera.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

Następujące zapytanie zwraca wszystkie węzły i liczniki, które mają stan *gotowe* i *niegotowe*.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
Następujące zapytanie zwraca, który zlicza fazy pod na podstawie wszystkich faz: *Nie powiodło się*, *oczekujące*, *nieznany*, *systemem*, lub *zakończyło się pomyślnie*.  

```kusto
let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let clusterName = '<your-cluster-name>';
    KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                 by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>Aby utworzyć alerty dotyczące niektórych zasobnika faz, takich jak *oczekujące*, *nie powiodło się*, lub *nieznany*, zmodyfikuj ostatni wiersz zapytania. Na przykład w ramach alertu *FailedCount* użyć: <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

## <a name="create-an-alert-rule"></a>Tworzenie reguły alertu
Wykonaj następujące kroki, aby utworzyć alert dziennika w usłudze Azure Monitor przy użyciu jednej z reguł wyszukiwania dziennika, które podano wcześniej.  

>[!NOTE]
>Poniższą procedurę, aby utworzyć regułę alertu dla wykorzystanie zasobów kontenera, musisz przełączyć się do nowego dziennika alertów interfejsu API, zgodnie z opisem w [preferencji Przełącz interfejsu API dla dziennika alertów](../platform/alerts-log-api-switch.md).
>

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **Monitor** w okienku po lewej stronie. W obszarze **Insights**, wybierz opcję **kontenery**.
3. Na **monitorowania klastrów** , a następnie wybierz klaster, z listy.
4. W okienku po lewej stronie w obszarze **monitorowanie**, wybierz opcję **dzienniki** aby otworzyć stronę dzienniki usługi Azure Monitor. Ta strona umożliwia pisanie i wykonywania zapytań w usłudze Azure Log Analytics.
5. Na **dzienniki** wybierz opcję **+ Nowa reguła alertu**.
6. W **warunek** zaznacz **jest zawsze wtedy, gdy dziennik wyszukiwania niestandardowego \<logika niezdefiniowana >** wstępnie zdefiniowanych warunków dziennika niestandardowego. **Przeszukiwania dzienników niestandardowych** typu sygnału jest automatycznie wybierana, ponieważ tworzymy regułę alertu bezpośrednio ze strony dzienniki usługi Azure Monitor.  
7. Wklej jeden z [zapytania](#resource-utilization-log-search-queries) dostarczona wcześniej do **zapytania wyszukiwania** pola.
8. Skonfiguruj alert w następujący sposób:

    1. Z listy rozwijanej **Na podstawie** wybierz pozycję **Pomiar metryki**. Pomiar metryki tworzy alert dla każdego obiektu w zapytaniu, który ma wartość powyżej naszych określoną wartość progową.
    1. Dla **warunek**, wybierz opcję **większa**, a następnie wprowadź **75** jako początkowej, podstawowej **próg**. Lub wprowadź inną wartość, która spełnia podane kryteria.
    1. W **wyzwalacz alertu na podstawie** zaznacz **kolejne naruszenia**. Wybierz z listy rozwijanej **większa**, a następnie wprowadź **2**.
    1. Aby skonfigurować alert dla procesora CPU kontenera lub wykorzystanie pamięci, w obszarze **agregowane na**, wybierz opcję **ContainerName**. 
    1. W **Evaluated na podstawie** sekcji, ustaw **okres** wartość **60 minut**. Reguła będzie uruchamiany co 5 minut i zwracają rekordy, które zostały utworzone w ciągu ostatniej godziny od bieżącego czasu. Ustawianie okresu kontom szerokości okna potencjalnych opóźnieniu przesyłania danych. Gwarantuje również, że zapytanie zwraca dane w celu uniknięcia ujemna wartość false, w którym nigdy nie wyzwala alert.

9. Wybierz **gotowe** aby zakończyć tworzenie reguły alertu.
10. Wprowadź nazwę w **Nazwa reguły alertu** pola. Określ **opis** zapewniająca szczegóły dotyczące alertu. I wybierz poziom ważności odpowiednich opcji dostępnych.
11. Aby od razu aktywować reguły alertu, zaakceptuj wartość domyślną dla **Włącz regułę po utworzeniu**.
12. Wybierz istniejącą **grupy akcji** lub Utwórz nową grupę. Ten krok zapewnia te same akcje przekierowanie ilekroć dany alert zostanie wywołany. Konfigurowanie zależnie od działu IT lub DevOps zespół operacyjny zarządza zdarzenia.
13. Wybierz **Utwórz regułę alertu** aby zakończyć tworzenie reguły alertu. Rozpoczyna ona działanie od razu.

## <a name="next-steps"></a>Kolejne kroki

* Widok [dziennika przykłady zapytań](container-insights-analyze.md#search-logs-to-analyze-data) Aby dowiedzieć się więcej na temat wstępnie zdefiniowanych zapytań i przykłady do oceny lub dostosować w innych sytuacjach alertu.
* Aby dowiedzieć się więcej na temat usługi Azure Monitor i monitorowanie innych aspektów wybranego działania klastra usługi AKS, zobacz [widok usługi Azure Kubernetes Service health](container-insights-analyze.md).
