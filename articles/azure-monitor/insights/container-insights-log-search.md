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
ms.openlocfilehash: ae8dd4cccb6795faa02e6705404644f6ccc24864
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71948044"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Jak wykonywać zapytania dotyczące dzienników z Azure Monitor dla kontenerów

Azure Monitor dla kontenerów gromadzi informacje o metrykach wydajności, danych spisu i informacjach o stanie kondycji z hostów kontenerów i kontenerów, a następnie przekazuje je do obszaru roboczego Log Analytics w Azure Monitor. Dane są zbierane co trzy minuty. Te dane są dostępne dla [zapytań](../../azure-monitor/log-query/log-query-overview.md) w Azure monitor. Te dane można zastosować do scenariuszy, które obejmują Planowanie migracji, analizę pojemności, odnajdywanie i rozwiązywanie problemów z wydajnością na żądanie.

## <a name="container-records"></a>Rekordy kontenerów

Przykłady rekordów zbieranych przez Azure Monitor kontenerów i typów danych, które są wyświetlane w wynikach przeszukiwania dzienników, są wyświetlane w poniższej tabeli:

| Typ danych | Typ danych w przeszukiwaniu dzienników | Pola |
| --- | --- | --- |
| Wydajność dla hostów i kontenerów | `Perf` | Komputer, nazwa obiektu, &#40;CounterName czas procesora (%), odczyty dysku MB, zapisy dysku MB, użycie pamięci MB, bajty odbioru sieci, bajty wysyłania w sieci&#41;, użycie procesora SEC, Sieć, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Spis kontenerów | `ContainerInventory` | TimeGenerated, Computer, nazwa kontenera, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, polecenie, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Dziennik kontenera | `ContainerLog` | TimeGenerated, komputer, identyfikator obrazu, nazwa kontenera, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Spis węzłów kontenera | `ContainerNodeInventory`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Spis magazynów w klastrze Kubernetes | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus,  ContainerStatusReason, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Spis węzłów części klastra Kubernetes | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Zdarzenia Kubernetes | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message, SourceSystem | 
| Usługi w klastrze Kubernetes | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Metryki wydajności dla węzłów części klastra Kubernetes | Wydajność &#124; , gdzie ObjectName = = "K8SNode" | Computer, ObjectName, &#40;CounterName CpuAllocatableBytes, MemoryAllocatableBytes, CpuCapacityNanoCores, MemoryCapacityBytes, MemoryRssBytes, CpuUsageNanoCores, MemoryWorkingsetBytes,&#41;restartTimeEpoch, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Metryki wydajności dotyczące kontenerów części klastra Kubernetes | Wydajność &#124; , gdzie ObjectName = = "K8SContainer" | CounterName &#40; CpuRequestNanoCores, MemoryRequestBytes, CpuLimitNanoCores, MemoryWorkingSetBytes, RestartTimeEpoch, CpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Metryki niestandardowe |`InsightsMetrics` | Komputer, nazwa, przestrzeń nazw, pochodzenie, SourceSystem, Tagi<sup>1</sup>, TimeGenerated, typ, VA, _ResourceId | 

<sup>1</sup> Właściwość *Tags* reprezentuje [wiele wymiarów](../platform/data-platform-metrics.md#multi-dimensional-metrics) dla odpowiedniej metryki. Aby uzyskać dodatkowe informacje o metrykach zbieranych i przechowywanych w tabeli `InsightsMetrics` i opisie właściwości rekordu, zobacz [InsightsMetrics Overview (przegląd](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md)).

>[!NOTE]
>Obsługa usługi Prometheus jest w tej chwili funkcją w publicznej wersji zapoznawczej.
>

## <a name="search-logs-to-analyze-data"></a>Wyszukaj dzienniki, aby analizować dane

Dzienniki Azure Monitor mogą ułatwić wyszukiwanie trendów, diagnozowanie wąskich gardeł, prognozowanie lub skorelowanie danych, które mogą pomóc w ustaleniu, czy bieżąca Konfiguracja klastra działa optymalnie. Wstępnie zdefiniowane przeszukiwania dzienników są udostępniane, aby od razu zacząć korzystać z programu lub, aby dostosować je do swoich potrzeb.

Interaktywną analizę danych można wykonać w obszarze roboczym, wybierając opcję **Wyświetl dzienniki zdarzeń Kubernetes** lub **Wyświetl dzienniki kontenerów** w okienku podglądu. Strona **przeszukiwanie dzienników** pojawia się po prawej stronie Azure Portal.

![Analiza danych w usłudze Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

Dane wyjściowe dzienników kontenerów, które są przekazywane do obszaru roboczego, to STDOUT i STDERR. Ponieważ Azure Monitor jest monitorowany przez usługę Azure Managed Kubernetes (AKS), system polecenia nie jest zbierany dzisiaj ze względu na dużą ilość wygenerowanych danych. 

### <a name="example-log-search-queries"></a>Przykładowe zapytania dotyczące przeszukiwania dzienników

Często warto tworzyć zapytania, które zaczynają się od przykładu lub dwa, a następnie modyfikują je w celu dopasowania do własnych wymagań. Aby ułatwić tworzenie bardziej zaawansowanych zapytań, można eksperymentować z następującymi przykładowymi zapytaniami:

| Zapytanie | Opis | 
|-------|-------------|
| ContainerInventory<br> &#124;Komputer projektu, nazwa, obraz, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124;Renderowanie tabeli | Wyświetl listę wszystkich informacji o cyklu życia kontenera| 
| KubeEvents_CL<br> &#124;gdzie nie (IsEmpty (Namespace_s))<br> &#124;Sortuj według TimeGenerated DESC<br> &#124;Renderowanie tabeli | Zdarzenia Kubernetes|
| ContainerImageInventory<br> &#124;podsumowujący AggregatedValue = Count () według obrazu, ImageTag, uruchomione | Spis obrazów | 
| **Wybierz opcję wyświetlania wykresu liniowego**:<br> Wydajność<br> &#124;gdzie ObjectName = = "K8SContainer" i CounterName = = "cpuUsageNanoCores &#124; " podsumowuje AvgCPUUsageNanoCores = AVG (CounterValue) według bin (TimeGenerated, – min), InstanceName | Procesor CPU kontenera | 
| **Wybierz opcję wyświetlania wykresu liniowego**:<br> Wydajność<br> &#124;gdzie ObjectName = = "K8SContainer" i CounterName = = "memoryRssBytes &#124; " podsumowuje AvgUsedRssMemoryBytes = AVG (CounterValue) według bin (TimeGenerated, – min), InstanceName | Pamięć kontenera |
| InsightsMetrics<br> &#124;WHERE name = = "requests_count"<br> &#124;Sumuj wartość Val = any (Val) przez TimeGenerated = bin (TimeGenerated, 1M)<br> &#124;Sortuj według TimeGenerated ASC<br> &#124;Project RequestsPerMinute = Val-poprzedni (Val), TimeGenerated <br> &#124;Renderowanie BarChart  | Liczba żądań na minutę z metrykami niestandardowymi |

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
