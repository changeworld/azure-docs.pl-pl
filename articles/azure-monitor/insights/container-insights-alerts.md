---
title: Tworzenie alertów o wydajności dla usługi Azure Monitor dla kontenerów | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób tworzenia alertów niestandardowych na podstawie zapytań dziennika dotyczących wykorzystania pamięci i procesora CPU z usługi Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 5d73f4399d10683597fb2a2e8a3a2ab4ba0d1165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75730929"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Jak skonfigurować alerty dotyczące problemów z wydajnością w usłudze Azure Monitor dla kontenerów

Usługa Azure Monitor dla kontenerów monitoruje wydajność obciążeń kontenerów, które są wdrażane w wystąpieniach kontenerów platformy Azure lub w zarządzanych klastrach kubernetes, które są hostowane w usłudze Azure Kubernetes Service (AKS).

W tym artykule opisano sposób włączania alertów w następujących sytuacjach:

- Gdy wykorzystanie procesora lub pamięci w węzłach klastra przekracza próg
- Gdy wykorzystanie procesora lub pamięci w dowolnym kontenerze w kontrolerze przekracza próg w porównaniu z limitem ustawionym na odpowiednim zasobie
- Liczba węzłów stanu *NotReady*
- Liczba faz podsyłek *nie powiodła się,* *Oczekujące,* *Nieznane,* *Uruchomione*lub *Pomyślnie*
- Gdy wolne miejsce na dysku w węzłach klastra przekracza próg 

Aby ostrzegać o wysokim wykorzystaniu procesora CPU lub pamięci lub małej ilości wolnego miejsca na dysku w węzłach klastra, użyj zapytań, które są dostarczane, aby utworzyć alert metryki lub alert pomiaru metryki. Alerty metryki mają mniejsze opóźnienie niż alerty dziennika. Ale alerty dziennika zapewniają zaawansowane zapytania i większe wyrafinowanie. Alerty dziennika kwerendy porównać datetime do chwili obecnej przy użyciu operatora *teraz* i cofając się o godzinę. (Usługa Azure Monitor dla kontenerów przechowuje wszystkie daty w formacie skoordynowanego czasu uniwersalnego (UTC).(Azure Monitor for containers stores all dates in Coordinated Universal Time (UTC) format.)

Jeśli nie znasz alertów usługi Azure Monitor, zobacz [Omówienie alertów na platformie Microsoft Azure](../platform/alerts-overview.md) przed rozpoczęciem. Aby dowiedzieć się więcej o alertach korzystających z zapytań dziennika, zobacz [Alerty dziennika w usłudze Azure Monitor](../platform/alerts-unified-log.md). Aby uzyskać więcej informacji o alertach metryk, zobacz [alerty metryki w usłudze Azure Monitor](../platform/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Kwerendy wyszukiwania dziennika wykorzystania zasobów

Zapytania w tej sekcji obsługują każdy scenariusz alertów. Są one używane w kroku 7 sekcji [alertu tworzenia](#create-an-alert-rule) w tym artykule.

Poniższa kwerenda oblicza średnie wykorzystanie procesora CPU jako średnie wykorzystanie procesora CPU węzłów członkowskich co minutę.  

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

Poniższa kwerenda oblicza średnie wykorzystanie pamięci jako średnie wykorzystanie pamięci węzłów członkowskich co minutę.

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
>W poniższych kwerendach użyto wartości \<zastępczych, które \<> nazwa klastra i nazwa kontrolera> do reprezentowania klastra i kontrolera. Zastąp je wartościami specyficznymi dla danego środowiska podczas konfigurowania alertów.

Poniższa kwerenda oblicza średnie wykorzystanie procesora CPU wszystkich kontenerów w kontrolerze jako średnie wykorzystanie procesora CPU każdego wystąpienia kontenera w kontrolerze co minutę. Pomiar jest procentem limitu ustawionego dla kontenera.

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

Poniższa kwerenda oblicza średnie wykorzystanie pamięci wszystkich kontenerów w kontrolerze jako średnie wykorzystanie pamięci każdego wystąpienia kontenera w kontrolerze co minutę. Pomiar jest procentem limitu ustawionego dla kontenera.

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

Następująca kwerenda zwraca wszystkie węzły i liczby, które mają stan *Gotowe* i *NotReady*.

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
Następująca kwerenda zwraca liczbę faz zasobników na podstawie wszystkich faz: *Nie powiodło się,* *Oczekujące,* *Nieznane,* *Uruchomione*lub *Powiodło się*.  

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
>Aby alertować w niektórych fazach zasobników, takich jak *Oczekujące,* *Nie powiodło się*lub *Nieznany,* zmodyfikuj ostatni wiersz kwerendy. Na przykład, aby alert na *FailedCount* użyj: <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

Następująca kwerenda zwraca dyski węzłów klastra, które przekraczają 90% wolnego miejsca. Aby uzyskać identyfikator klastra, najpierw uruchom następującą kwerendę i skopiuj `ClusterId` wartość z właściwości:

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>Tworzenie reguły alertu

Wykonaj następujące kroki, aby utworzyć alert dziennika w usłudze Azure Monitor przy użyciu jednej z reguł wyszukiwania dziennika, która została udostępniona wcześniej. Aby utworzyć przy użyciu szablonu ARM, zobacz [Tworzenie alertów dziennika przykładowego przy użyciu szablonu zasobu platformy Azure](../platform/alerts-log.md#sample-log-alert-creation-using-azure-resource-template).

>[!NOTE]
>Poniższa procedura tworzenia reguły alertów dla wykorzystania zasobów kontenera wymaga przełączenia się do nowego interfejsu API alertów dziennika, zgodnie z opisem w [preferencji interfejsu API przełącznika dla alertów dziennika](../platform/alerts-log-api-switch.md).
>

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. W witrynie Azure portal wyszukaj i wybierz **obszary robocze usługi Log Analytics**.
3. Na liście obszarów roboczych usługi Log Analytics wybierz obszar roboczy obsługujący usługę Azure Monitor dla kontenerów. 
4. W okienku po lewej stronie wybierz **pozycję Dzienniki,** aby otworzyć stronę dzienników usługi Azure Monitor. Ta strona służy do pisania i wykonywania zapytań usługi Azure Log Analytics.
5. Na stronie **Dzienniki** wklej jedno z [zapytań dostarczonych](#resource-utilization-log-search-queries) wcześniej w polu **Kwerenda wyszukiwania,** a następnie wybierz pozycję **Uruchom,** aby sprawdzić poprawność wyników. Jeśli ten krok nie zostanie wykonać, opcja **+Nowy alert** nie jest dostępna do wybrania.
6. Wybierz **+Nowy alert,** aby utworzyć alert dziennika.
7. W sekcji **Warunek** wybierz **opcję Ilekroć \<wyszukiwanie dziennika niestandardowego jest logiką niezdefiniowanym>** wstępnie zdefiniowanym warunkiem dziennika niestandardowego. Typ sygnału **wyszukiwania dziennika niestandardowego** jest wybierany automatycznie, ponieważ tworzymy regułę alertu bezpośrednio ze strony dzienników usługi Azure Monitor.  
8. Wklej jedno z [zapytań dostarczonych](#resource-utilization-log-search-queries) wcześniej w polu **Kwerenda wyszukiwania.**
9. Skonfiguruj alert w następujący sposób:

    1. Z listy rozwijanej **Na podstawie** wybierz opcję **Pomiar metryki**. Miara metryki tworzy alert dla każdego obiektu w kwerendzie, który ma wartość powyżej naszego określonego progu.
    1. W obszarze **Warunek**wybierz **opcję Większa niż**i wprowadź **75** jako początkowy **próg** linii bazowej dla alertów dotyczących wykorzystania procesora CPU i pamięci. W przypadku alertu o małej przestrzeni dyskowej wprowadź **90**. Możesz też wprowadzić inną wartość, która spełnia twoje kryteria.
    1. W sekcji **Alert wyzwalacza na podstawie** wybierz pozycję Kolejne **naruszenia**. Z listy rozwijanej wybierz **pozycję Większa niż**i wprowadź **2**.
    1. Aby skonfigurować alert dotyczący wykorzystania procesora lub pamięci kontenera, w obszarze **Agreguj na**wybierz opcję **ContainerName**. Aby skonfigurować alert o niskim dysku węzła klastra, wybierz pozycję **ClusterId**.
    1. W sekcji **Oceniane na podstawie** ustaw wartość **Okres** na **60 minut**. Reguła będzie uruchamiana co 5 minut i zwraca rekordy, które zostały utworzone w ciągu ostatniej godziny od bieżącego czasu. Ustawianie okresu na szerokie konto okna dla potencjalnego opóźnienia danych. Zapewnia również, że kwerenda zwraca dane, aby uniknąć fałszywie ujemny, w którym alert nigdy nie jest uruchamiany.

10. Wybierz **pozycję Gotowe,** aby ukończyć regułę alertu.
11. Wprowadź nazwę w polu **Nazwa reguły alertu.** Określ **opis,** który zawiera szczegółowe informacje o alertie. I wybierz odpowiedni poziom ważności z podanych opcji.
12. Aby natychmiast aktywować regułę alertu, zaakceptuj domyślną wartość **reguły Włącz po utworzeniu**.
13. Wybierz istniejącą **grupę akcji** lub utwórz nową grupę. Ten krok gwarantuje, że te same akcje są podejmowane za każdym razem, gdy alert jest wyzwalany. Skonfiguruj na podstawie sposobu, w jaki zespół operacyjny IT lub DevOps zarządza zdarzeniami.
14. Wybierz **pozycję Utwórz regułę alertu,** aby ukończyć regułę alertu. Rozpoczyna ona działanie od razu.

## <a name="next-steps"></a>Następne kroki

- Wyświetlanie [przykładów zapytań dziennika,](container-insights-log-search.md#search-logs-to-analyze-data) aby wyświetlić wstępnie zdefiniowane kwerendy i przykłady do oceny lub dostosowania do alertów, wizualizacji lub analizowania klastrów.
- Aby dowiedzieć się więcej o usłudze Azure Monitor i jak monitorować inne aspekty klastra kubernetes, zobacz [Wyświetlanie wydajności klastra Kubernetes](container-insights-analyze.md) i [Wyświetlanie kondycji klastra Kubernetes.](container-insights-health.md)
