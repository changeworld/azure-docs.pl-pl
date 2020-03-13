---
title: Jak wykonywać zapytania dotyczące dzienników z Azure Monitor kontenerów | Microsoft Docs
description: Azure Monitor dla kontenerów zbiera dane dotyczące metryk i dzienników, a w tym artykule opisano rekordy i zawiera przykładowe zapytania.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: dcd1656673e549b583de26bca897d0055f389d0a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275362"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Jak wykonywać zapytania dotyczące dzienników z Azure Monitor dla kontenerów

Azure Monitor dla kontenerów gromadzi informacje o metrykach wydajności, danych spisu i informacjach o stanie kondycji z hostów kontenerów i kontenerów, a następnie przekazuje je do obszaru roboczego Log Analytics w Azure Monitor. Dane są gromadzone co trzy minuty. Te dane są dostępne dla [zapytań](../../azure-monitor/log-query/log-query-overview.md) w Azure monitor. Te dane można zastosować do scenariuszy, które obejmują Planowanie migracji, analizę pojemności, odnajdywanie i rozwiązywanie problemów z wydajnością na żądanie.

## <a name="container-records"></a>Rekordów kontenera

Przykłady rekordy, które są zbierane przez usługi Azure Monitor dla kontenerów i typy danych, które są wyświetlane w wynikach wyszukiwania w dzienniku są wyświetlane w poniższej tabeli:

| Typ danych | Typ danych podczas wyszukiwania dziennika | Pola |
| --- | --- | --- |
| Wydajność dla hostów i kontenerów | `Perf` | CounterName komputera, nazwa obiektu, &#40;czas procesora (%), dysk odczytuje MB, dysku zapisuje MB, MB użycia pamięci, sieci odbierania bajtów, sieci wysyłania w bajtach, procesor s użycia, sieć&#41;, CounterValue, TimeGenerated, Ścieżka_licznika, system źródłowy |
| Kontener magazynu | `ContainerInventory` | TimeGenerated, komputera, nazwa kontenera, ContainerHostname, obraz, ImageTag, ContainerState, ExitCode, EnvironmentVar, polecenia, wartością CreatedTime, StartedTime, FinishedTime, system źródłowy, ContainerID, ImageID |
| Dziennik kontenera | `ContainerLog` | TimeGenerated, komputer, identyfikator obrazu, pola Nazwa kontenera LogEntrySource, LogEntry, system źródłowy, ContainerID |
| Spis węzłów kontenerów | `ContainerNodeInventory`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Spis zasobników w klastrze Kubernetes | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus,  ContainerStatusReason, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Spis węzłów będących częścią klastra Kubernetes | `KubeNodeInventory` | TimeGenerated, komputera, ClusterName, ClusterId, LastTransitionTimeReady, etykiety, stan, KubeletVersion, KubeProxyVersion, CreationTimeStamp, system źródłowy | 
| Zdarzenia Kubernetes | `KubeEvents` | TimeGenerated, komputera, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, komunikat o błędzie, system źródłowy | 
| Usługi w klastrze Kubernetes | `KubeServices` | TimeGenerated, ServiceName_s, SelectorLabels_s, ServiceType_s, ClusterId_s, ClusterIP_s, Namespace_s, ClusterName_s, system źródłowy | 
| Metryki wydajności dla części węzłów klastra Kubernetes | Perf &#124; gdzie ObjectName == "K8SNode" | Computer, ObjectName, &#40;CounterName CpuAllocatableBytes, MemoryAllocatableBytes, CpuCapacityNanoCores, MemoryCapacityBytes, MemoryRssBytes, CpuUsageNanoCores, MemoryWorkingsetBytes,&#41;RestartTimeEpoch, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Metryki wydajności dla kontenerów częścią klastra Kubernetes | Perf &#124; gdzie ObjectName == "K8SContainer" | CounterName &#40; CpuRequestNanoCores, MemoryRequestBytes, CpuLimitNanoCores, MemoryWorkingSetBytes, RestartTimeEpoch, CpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Metryki niestandardowe |`InsightsMetrics` | Komputer, nazwa, przestrzeń nazw, pochodzenie, SourceSystem, znaczniki<sup>1</sup>, TimeGenerated, typ, Va, _ResourceId | 

<sup>1</sup> Właściwość *Tags* reprezentuje [wiele wymiarów](../platform/data-platform-metrics.md#multi-dimensional-metrics) dla odpowiedniej metryki. Aby uzyskać dodatkowe informacje na temat metryk zbieranych i przechowywanych w tabeli `InsightsMetrics` i opis właściwości rekordu, zobacz [InsightsMetrics Overview (przegląd](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md)).

>[!NOTE]
>Obsługa usługi Prometheus jest w tej chwili funkcją w publicznej wersji zapoznawczej.
>

## <a name="search-logs-to-analyze-data"></a>Dzienniki wyszukiwania do analizy danych

Dzienniki Azure Monitor mogą ułatwić wyszukiwanie trendów, diagnozowanie wąskich gardeł, prognozowanie lub skorelowanie danych, które mogą pomóc w ustaleniu, czy bieżąca Konfiguracja klastra działa optymalnie. Wstępnie zdefiniowane wyszukiwań w dziennikach znajdują się za Ciebie, aby od razu rozpocząć korzystanie z lub dostosować do zwracania informacji w żądany sposób.

Możesz wykonać interaktywną analizę danych w obszarze roboczym, wybierając opcję **Wyświetl dzienniki zdarzeń Kubernetes** lub **Wyświetl dzienniki kontenerów** w okienku podglądu z listy rozwijanej **Widok w analizie** . Strona **przeszukiwanie dzienników** pojawia się po prawej stronie Azure Portal.

![Analiza danych w usłudze Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

Dane wyjściowe dzienników kontenerów, które są przekazywane do obszaru roboczego, to STDOUT i STDERR. Ponieważ usługa Azure Monitor jest monitorowanie usługi Azure managed Kubernetes (AKS), system klastra kubernetes w usłudze nie są zbierane już dziś ze względu na duże obciążenie wygenerowane dane. 

### <a name="example-log-search-queries"></a>Przykład zapytania wyszukiwania w Dzienniku

Często jest to przydatne do tworzenia zapytań, które zaczynać się przykładem lub dwa, a następnie zmodyfikuj je zgodnie z wymaganiami. Aby ułatwić tworzenie bardziej zaawansowanych zapytań, możesz eksperymentować z następujące przykładowe zapytania:

| Zapytanie | Opis | 
|-------|-------------|
| ContainerInventory<br> &#124;Projekt komputera, nazwa, obraz, ImageTag, ContainerState, wartością CreatedTime, StartedTime, FinishedTime<br> &#124;Renderowanie tabeli | Wyświetlić listę wszystkich informacji o cyklu życia kontenera| 
| KubeEvents_CL<br> &#124;gdzie not(isempty(Namespace_s))<br> &#124;Sortuj według malejącej TimeGenerated<br> &#124;Renderowanie tabeli | Zdarzenia Kubernetes|
| ContainerImageInventory<br> &#124;summarize AggregatedValue = count() by obrazu, ImageTag, działa | Spis obrazów | 
| **Wybierz opcję wyświetlania wykresu liniowego**:<br> Wyd.<br> &#124;Gdzie ObjectName == "K8SContainer" i CounterName == "cpuUsageNanoCores" &#124; Podsumuj AvgCPUUsageNanoCores avg(CounterValue) przez bin (TimeGenerated, 30 min), InstanceName = | Procesora CPU kontenera | 
| **Wybierz opcję wyświetlania wykresu liniowego**:<br> Wyd.<br> &#124;Gdzie ObjectName == "K8SContainer" i CounterName == "memoryRssBytes" &#124; Podsumuj AvgUsedRssMemoryBytes avg(CounterValue) przez bin (TimeGenerated, 30 min), InstanceName = | Pamięci kontenera |
| InsightsMetrics<br> &#124;WHERE name = = "requests_count"<br> &#124;Sumuj wartość Val = any (Val) przez TimeGenerated = bin (TimeGenerated, 1M)<br> &#124;Sortuj według TimeGenerated ASC<br> &#124;Project RequestsPerMinute = Val-poprzedni (Val), TimeGenerated <br> &#124;Renderowanie BarChart  | Liczba żądań na minutę z metrykami niestandardowymi |

## <a name="query-prometheus-metrics-data"></a>Zapytanie danych metryk Prometheus

Poniższy przykład to zapytanie metryki Prometheus przedstawiające Odczyty dysku na sekundę na dysk na węzeł.

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

Aby wyświetlić metryki Prometheus oddzielone przez Azure Monitor filtrowane według przestrzeni nazw, określ wartość "Prometheus". Oto przykładowe zapytanie umożliwiające wyświetlenie metryk Prometheus z przestrzeni nazw `default` Kubernetes.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Dane Prometheus można także zbadać bezpośrednio według nazwy.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Błędy podczas konfigurowania lub wypadków

Aby zbadać wszelkie błędy związane z konfiguracją lub brakiem, następujące przykładowe zapytanie zwraca informacje o zdarzeniach z tabeli `KubeMonAgentEvents`.

```
KubeMonAgentEvents | where Level != "Info" 
```

Wyniki będą wyglądać podobnie do następujących:

![Rejestruj wyniki zapytania dotyczącego zdarzeń informacyjnych z agenta](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Następne kroki

Azure Monitor kontenerów nie zawiera wstępnie zdefiniowanego zestawu alertów. Zapoznaj się z tematem [tworzenie alertów dotyczących wydajności za pomocą Azure monitor dla kontenerów](container-insights-alerts.md) , aby dowiedzieć się, jak utworzyć zalecane alerty dotyczące wysokiego użycia procesora i pamięci, aby zapewnić obsługę procesów i procedur operacyjnych DevOps. 
