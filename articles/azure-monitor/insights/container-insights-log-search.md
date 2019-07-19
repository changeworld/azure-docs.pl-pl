---
title: Jak wykonywać zapytania dotyczące dzienników z Azure Monitor kontenerów | Microsoft Docs
description: Azure Monitor dla kontenerów zbiera dane dotyczące metryk i dzienników, a w tym artykule opisano rekordy i zawiera przykładowe zapytania.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: d6e65331db53be5ba13a75e6b03b271f1071716d
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67989833"
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
| Metryki wydajności dla części węzłów klastra Kubernetes | Perf &#124; gdzie ObjectName == "K8SNode" | Computer, ObjectName, &#40;CounterName CpuAllocatableBytes, MemoryAllocatableBytes, CpuCapacityNanoCores, MemoryCapacityBytes, MemoryRssBytes, CpuUsageNanoCores, MemoryWorkingsetBytes,&#41;restartTimeEpoch, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Metryki wydajności dla kontenerów częścią klastra Kubernetes | Perf &#124; gdzie ObjectName == "K8SContainer" | CounterName &#40; CpuRequestNanoCores, MemoryRequestBytes, CpuLimitNanoCores, MemoryWorkingSetBytes, RestartTimeEpoch, CpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Metryki niestandardowe |`InsightsMetrics` | Komputer, nazwa, przestrzeń nazw, pochodzenie, SourceSystem, Tagi<sup>1</sup>, TimeGenerated, typ, VA, _ResourceId | 

<sup>1</sup> Właściwość *Tags* reprezentuje [wiele wymiarów](../platform/data-platform-metrics.md#multi-dimensional-metrics) dla odpowiedniej metryki. Aby uzyskać dodatkowe informacje o metrykach zbieranych i przechowywanych `InsightsMetrics` w tabeli oraz opisie właściwości rekordu, zobacz [InsightsMetrics Overview (przegląd](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md)).

>[!NOTE]
>Obsługa usługi Prometheus jest w tej chwili funkcją w publicznej wersji zapoznawczej.
>

## <a name="search-logs-to-analyze-data"></a>Dzienniki wyszukiwania do analizy danych

Dzienniki Azure Monitor mogą ułatwić wyszukiwanie trendów, diagnozowanie wąskich gardeł, prognozowanie lub skorelowanie danych, które mogą pomóc w ustaleniu, czy bieżąca Konfiguracja klastra działa optymalnie. Wstępnie zdefiniowane wyszukiwań w dziennikach znajdują się za Ciebie, aby od razu rozpocząć korzystanie z lub dostosować do zwracania informacji w żądany sposób.

W obszarze roboczym można wykonywać interakcyjne analizy danych, zaznaczając **dzienniki zdarzeń Kubernetes widoku** lub **wyświetlanie dzienników kontenera** opcji w okienku podglądu. **Wyszukiwanie w dzienniku** po prawej stronie portalu platformy Azure były na zostanie wyświetlona strona.

![Analiza danych w usłudze Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

Dane wyjściowe dzienników kontenerów, które są przekazywane do obszaru roboczego, to STDOUT i STDERR. Ponieważ usługa Azure Monitor jest monitorowanie usługi Azure managed Kubernetes (AKS), system klastra kubernetes w usłudze nie są zbierane już dziś ze względu na duże obciążenie wygenerowane dane. 

### <a name="example-log-search-queries"></a>Przykład zapytania wyszukiwania w Dzienniku

Często jest to przydatne do tworzenia zapytań, które zaczynać się przykładem lub dwa, a następnie zmodyfikuj je zgodnie z wymaganiami. Aby ułatwić tworzenie bardziej zaawansowanych zapytań, możesz eksperymentować z następujące przykładowe zapytania:

| Zapytanie | Opis | 
|-------|-------------|
| ContainerInventory<br> &#124;Projekt komputera, nazwa, obraz, ImageTag, ContainerState, wartością CreatedTime, StartedTime, FinishedTime<br> &#124;Renderowanie tabeli | Wyświetlić listę wszystkich informacji o cyklu życia kontenera| 
| KubeEvents_CL<br> &#124;gdzie not(isempty(Namespace_s))<br> &#124;Sortuj według malejącej TimeGenerated<br> &#124;Renderowanie tabeli | Zdarzenia Kubernetes|
| ContainerImageInventory<br> &#124;summarize AggregatedValue = count() by obrazu, ImageTag, działa | Spis obrazów | 
| **Wybierz opcję wyświetlania wykresu liniowego**:<br> Perf<br> &#124;Gdzie ObjectName == "K8SContainer" i CounterName == "cpuUsageNanoCores" &#124; Podsumuj AvgCPUUsageNanoCores avg(CounterValue) przez bin (TimeGenerated, 30 min), InstanceName = | Procesora CPU kontenera | 
| **Wybierz opcję wyświetlania wykresu liniowego**:<br> Perf<br> &#124;Gdzie ObjectName == "K8SContainer" i CounterName == "memoryRssBytes" &#124; Podsumuj AvgUsedRssMemoryBytes avg(CounterValue) przez bin (TimeGenerated, 30 min), InstanceName = | Pamięci kontenera |

Poniższy przykład to zapytanie metryk Prometheus. Zbierane metryki są liczbami i aby określić liczbę błędów wystąpiłych w określonym przedziale czasu, należy odjąć od liczby. Zestaw danych jest partycjonowany przez *partitionKey*, znaczenie dla każdego unikatowego zestawu *nazw*, nazwy *hosta*i *OperationType*, uruchamiamy podzapytanie w tym zestawie, które porządkuje dzienniki przez *TimeGenerated*, proces, który umożliwia Znajdź poprzedni *TimeGenerated* oraz liczbę zarejestrowanych dla tego czasu, aby określić stawkę.

```
let data = InsightsMetrics 
| where Namespace contains 'prometheus' 
| where Name == 'kubelet_docker_operations' or Name == 'kubelet_docker_operations_errors'    
| extend Tags = todynamic(Tags) 
| extend OperationType = tostring(Tags['operation_type']), HostName = tostring(Tags.hostName) 
| extend partitionKey = strcat(HostName, '/' , Name, '/', OperationType) 
| partition by partitionKey ( 
    order by TimeGenerated asc 
    | extend PrevVal = prev(Val, 1), PrevTimeGenerated = prev(TimeGenerated, 1) 
    | extend Rate = iif(TimeGenerated == PrevTimeGenerated, 0.0, Val - PrevVal) 
    | where isnull(Rate) == false 
) 
| project TimeGenerated, Name, HostName, OperationType, Rate; 
let operationData = data 
| where Name == 'kubelet_docker_operations' 
| project-rename OperationCount = Rate; 
let errorData = data 
| where Name == 'kubelet_docker_operations_errors' 
| project-rename ErrorCount = Rate; 
operationData 
| join kind = inner ( errorData ) on TimeGenerated, HostName, OperationType 
| project-away TimeGenerated1, Name1, HostName1, OperationType1 
| extend SuccessPercentage = iif(OperationCount == 0, 1.0, 1 - (ErrorCount / OperationCount))
```

Wyniki będą wyglądać podobnie do następujących:

![Wyniki zapytania dziennika woluminu pozyskiwania danych](./media/container-insights-log-search/log-query-example-prometheus-metrics.png)

## <a name="next-steps"></a>Następne kroki

Azure Monitor kontenerów nie zawiera wstępnie zdefiniowanego zestawu alertów. Zapoznaj się z tematem [tworzenie alertów dotyczących wydajności za pomocą Azure monitor dla kontenerów](container-insights-alerts.md) , aby dowiedzieć się, jak utworzyć zalecane alerty dotyczące wysokiego użycia procesora i pamięci, aby zapewnić obsługę procesów i procedur operacyjnych DevOps. 