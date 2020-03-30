---
title: Jak kwerendy dzienników z usługi Azure Monitor dla kontenerów | Dokumenty firmy Microsoft
description: Usługa Azure Monitor dla kontenerów zbiera metryki i dane dziennika, a w tym artykule opisano rekordy i zawiera przykładowe kwerendy.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: ff7cbff708b794847d8be69ca8f829e622d7c7ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333477"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Jak wysyłać zapytania do dzienników z usługi Azure Monitor dla kontenerów

Usługa Azure Monitor dla kontenerów zbiera metryki wydajności, dane spisu i informacje o stanie kondycji z hostów kontenerów i kontenerów i przekazuje je do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor. Dane są zbierane co trzy minuty. Te dane są dostępne dla [kwerendy](../../azure-monitor/log-query/log-query-overview.md) w usłudze Azure Monitor. Te dane można zastosować do scenariuszy, które obejmują planowanie migracji, analizę zdolności produkcyjnych, odnajdowanie i rozwiązywanie problemów z wydajnością na żądanie.

## <a name="container-records"></a>Rekordy kontenerów

Przykłady rekordów, które są zbierane przez usługę Azure Monitor dla kontenerów i typów danych, które pojawiają się w wynikach wyszukiwania dziennika są wyświetlane w poniższej tabeli:

| Typ danych | Typ danych w wyszukiwaniu dzienników | Pola |
| --- | --- | --- |
| Wydajność dla hostów i kontenerów | `Perf` | Komputer, ObjectName, CounterName &#40;%Czas procesora, Odczyty dysku MB, Zapisy dysków MB, Użycie pamięci MB, Bajty odbierania sieci, Bajty wysyłania sieci, Użycie procesora w s,&#41; sieci, Wartość licznika, Czasogenerowany, CounterPath, System źródłowy |
| Zapasy kontenerów | `ContainerInventory` | TimeGenerated, Komputer, nazwa kontenera, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Dziennik kontenerów | `ContainerLog` | CzasGenerowany, Komputer, identyfikator obrazu, nazwa kontenera, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Magazyn węzła kontenera | `ContainerNodeInventory`| CzasGenerowany, Komputer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, System źródłowy|
| Spis zasobników w klastrze Kubernetes | `KubePodInventory` | TimeGenerated, Komputer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus,  ContainerStatusReason, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Spis węzłów należących do klastra Kubernetes | `KubeNodeInventory` | CzasGenerowany, Komputer, ClusterName, ClusterId, LastTransitionTimeReady, Etykiety, Stan, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Zdarzenia Kubernetes | `KubeEvents` | Czasogenerowane, Komputer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, wiadomość, system źródłowy | 
| Usługi w klastrze Kubernetes | `KubeServices` | CzasGenerowany, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, System źródłowy | 
| Metryki wydajności dla węzłów należących do klastra Kubernetes | Perf &#124; gdzie ObjectName == "K8SNode" | Komputer, ObjectName, CounterName &#40;cpuAllocatableBytes, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Metryki wydajności kontenerów części klastra Kubernetes | Perf &#124; gdzie ObjectName == "K8SContainer" | Nazwa przeciwstawna &#40; cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Metryki niestandardowe |`InsightsMetrics` | Komputer, Nazwa, Obszar nazw, Pochodzenie, SourceSystem, Tagi<sup>1</sup>, TimeGenerated, Typ, Va, _ResourceId | 

<sup>1</sup> *Właściwość Tags* reprezentuje [wiele wymiarów](../platform/data-platform-metrics.md#multi-dimensional-metrics) dla odpowiedniej metryki. Aby uzyskać dodatkowe informacje na temat metryk zebranych i przechowywanych w `InsightsMetrics` tabeli oraz opis właściwości rekordu, zobacz Przegląd [metryk insights](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

## <a name="search-logs-to-analyze-data"></a>Szukaj dzienników do analizowania danych

Dzienniki usługi Azure Monitor mogą pomóc w poszukiwaniu trendów, diagnozowaniu wąskich gardeł, prognozowaniu lub korelowaniu danych, które mogą pomóc w określeniu, czy bieżąca konfiguracja klastra działa optymalnie. Wstępnie zdefiniowane wyszukiwania dziennika są przewidziane, aby natychmiast rozpocząć korzystanie lub dostosować, aby zwrócić informacje w odpowiedni sposób.

Interaktywną analizę danych w obszarze roboczym można przeprowadzić, wybierając opcję **Wyświetl dzienniki zdarzeń kubernetes** lub **Wyświetl dzienniki kontenerów** w okienku podglądu z listy rozwijanej **Widok w analizie.** Strona **Wyszukiwanie dzienników** jest wyświetlana po prawej stronie strony portalu Azure, na której się znajdowałeś.

![Analiza danych w usłudze Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)

Dane wyjściowe dziennika kontenera, który jest przekazycony do obszaru roboczego są STDOUT i STDERR. Ponieważ usługa Azure Monitor monitoruje kubernetes zarządzane przez platformę Azure (AKS), system Kube nie jest zbierany dzisiaj ze względu na dużą ilość wygenerowanych danych. 

### <a name="example-log-search-queries"></a>Przykładowe zapytania wyszukiwania dziennika

Często jest przydatne do tworzenia zapytań, które zaczynają się od przykładu lub dwóch, a następnie zmodyfikować je, aby dopasować je do wymagań. Aby ułatwić tworzenie bardziej zaawansowanych zapytań, można eksperymentować z następującymi przykładowymi kwerendami:

| Zapytanie | Opis | 
|-------|-------------|
| ContainerInventory (KontenerInwentory)<br> &#124; projektu Komputer, Nazwa, Obraz, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; tabela renderowania | Wyświetlanie wszystkich informacji o cyklu życia kontenera| 
| KubeEvents_CL<br> &#124;, gdzie nie (niedosyt(Namespace_s))<br> &#124; sortowanie według czasuGenerowane desc<br> &#124; tabela renderowania | Zdarzenia Kubernetes|
| ContainerImageInwentory<br> &#124; podsumuj aggregatedValue = count() według obrazu, ImageTag, Running | Inwentaryzacja obrazów | 
| **Wybierz opcję wyświetlania wykresu liniowego:**<br> Wyd.<br> &#124; gdzie ObjectName == "K8SContainer" i CounterName == "cpuUsageNanoCores" &#124; podsumuj AvgCPUUsageNanoCores = avg(CounterValue) przez bin(TimeGenerated, 30m), InstanceName | Procesor kontenerowy | 
| **Wybierz opcję wyświetlania wykresu liniowego:**<br> Wyd.<br> &#124; gdzie ObjectName == "K8SContainer" i CounterName == "memoryRssBytes" &#124; podsumuj AvgUsedRssMemoryBytes = avg(CounterValue) przez bin(TimeGenerated, 30m), InstanceName | Pamięć kontenera |
| Wskaźniki insights<br> &#124; gdzie Nazwa == "requests_count"<br> &#124; podsumuj Val=any(Val) według timegenerated=bin(TimeGenerated, 1m)<br> &#124; sortowanie według timegenerated asc<br> &#124; projektu RequestsPerMinute = Val - prev(Val), TimeGenerated <br> &#124; renderowania wykresu barchart  | Żądania na minutę z niestandardowych metryk |

## <a name="query-prometheus-metrics-data"></a>Zapytanie Prometheus dane metryki

Poniższy przykład jest Prometheus metrics kwerendy pokazujący odczyty dysku na sekundę na dysk na węzeł.

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

Aby wyświetlić metryki Prometheus zeskrobane przez usługę Azure Monitor filtrowane przez obszar nazw, określ "prometheus". Oto przykładowe zapytanie, aby wyświetlić metryki Prometheus z obszaru nazw `default` kubernetes.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Prometheus dane mogą być również bezpośrednio wyszukiwane przez nazwę.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Błędy konfiguracji lub skrobania kwerendy

Aby zbadać wszelkie błędy konfiguracji lub skrobania, poniższa `KubeMonAgentEvents` przykładowa kwerenda zwraca zdarzenia informacyjne z tabeli.

```
KubeMonAgentEvents | where Level != "Info" 
```

Dane wyjściowe pokażą wyniki podobne do następujących:

![Rejestrowanie wyników kwerend zdarzeń informacyjnych z agenta](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Następne kroki

Usługa Azure Monitor dla kontenerów nie zawiera wstępnie zdefiniowanego zestawu alertów. Przejrzyj [tworzenie alertów wydajności za pomocą usługi Azure Monitor dla kontenerów,](container-insights-alerts.md) aby dowiedzieć się, jak utworzyć zalecane alerty dotyczące wysokiego wykorzystania procesora CPU i pamięci w celu obsługi procesów i procedur operacyjnych. 
