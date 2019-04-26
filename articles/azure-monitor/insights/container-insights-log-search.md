---
title: Jak dzienniki zapytań z usługi Azure Monitor dla kontenerów | Dokumentacja firmy Microsoft
description: Usługa Azure Monitor dla kontenerów zbiera dane metryk i dzienników, a w tym artykule opisano rekordy i zawiera przykładowe zapytania.
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
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: 66fc55d8c3dbb8487d1e796d5f30b08a94f717f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60494785"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Tworzenie zapytań względem dzienników z usługi Azure Monitor dla kontenerów
Usługa Azure Monitor dla kontenerów zbiera metryki wydajności, dane spisu i informacji o stanie kondycji hostach kontenerów i kontenery i przekazuje go do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor. Dane są gromadzone co trzy minuty. Dane te są dostępne dla [zapytania](../../azure-monitor/log-query/log-query-overview.md) w usłudze Azure Monitor. Dane te można zastosować do scenariuszy obejmujących planowania migracji, analizy wydajności, wykrywanie i rozwiązywanie problemów z wydajnością na żądanie.

## <a name="container-records"></a>Rekordów kontenera

Przykłady rekordy, które są zbierane przez usługi Azure Monitor dla kontenerów i typy danych, które są wyświetlane w wynikach wyszukiwania w dzienniku są wyświetlane w poniższej tabeli:

| Typ danych | Typ danych podczas wyszukiwania dziennika | Pola |
| --- | --- | --- |
| Wydajność dla hostów i kontenerów | `Perf` | CounterName komputera, nazwa obiektu, &#40;czas procesora (%), dysk odczytuje MB, dysku zapisuje MB, MB użycia pamięci, sieci odbierania bajtów, sieci wysyłania w bajtach, procesor s użycia, sieć&#41;, CounterValue, TimeGenerated, Ścieżka_licznika, system źródłowy |
| Kontener magazynu | `ContainerInventory` | TimeGenerated, komputera, nazwa kontenera, ContainerHostname, obraz, ImageTag, ContainerState, ExitCode, EnvironmentVar, polecenia, wartością CreatedTime, StartedTime, FinishedTime, system źródłowy, ContainerID, ImageID |
| Dziennik kontenera | `ContainerLog` | TimeGenerated, komputer, identyfikator obrazu, pola Nazwa kontenera LogEntrySource, LogEntry, system źródłowy, ContainerID |
| Spis węzłów kontenerów | `ContainerNodeInventory`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Spis zasobników w klastrze Kubernetes | `KubePodInventory` | TimeGenerated, komputer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, element ControllerName, ContainerStatus,  ContainerStatusReason ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, adresem IP zasobnika, system źródłowy |
| Spis węzłów będących częścią klastra Kubernetes | `KubeNodeInventory` | TimeGenerated, komputera, ClusterName, ClusterId, LastTransitionTimeReady, etykiety, stan, KubeletVersion, KubeProxyVersion, CreationTimeStamp, system źródłowy | 
| Zdarzenia Kubernetes | `KubeEvents` | TimeGenerated, komputera, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, komunikat o błędzie, system źródłowy | 
| Usługi w klastrze Kubernetes | `KubeServices` | TimeGenerated, ServiceName_s, SelectorLabels_s, ServiceType_s, ClusterId_s, ClusterIP_s, Namespace_s, ClusterName_s, system źródłowy | 
| Metryki wydajności dla części węzłów klastra Kubernetes | Perf &#124; gdzie ObjectName == "K8SNode" | CounterName komputera, nazwa obiektu, &#40;cpuAllocatableBytes, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, Ścieżka_licznika, system źródłowy | 
| Metryki wydajności dla kontenerów częścią klastra Kubernetes | Perf &#124; gdzie ObjectName == "K8SContainer" | CounterName &#40; cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, Ścieżka_licznika, system źródłowy | 
| InsightsMetrics | Computer, Name, Namespace, Origin, SourceSystem, Tags, TimeGenerated, Type, Value |

## <a name="search-logs-to-analyze-data"></a>Dzienniki wyszukiwania do analizy danych
Dzienniki platformy Azure Monitor może pomóc wyszukiwania trendach, diagnozowanie wąskich gardeł, prognozy lub korelowanie danych, które mogą pomóc ustalić, czy bieżąca konfiguracja klastra działa optymalnie. Wstępnie zdefiniowane wyszukiwań w dziennikach znajdują się za Ciebie, aby od razu rozpocząć korzystanie z lub dostosować do zwracania informacji w żądany sposób. 

W obszarze roboczym można wykonywać interakcyjne analizy danych, zaznaczając **dzienniki zdarzeń Kubernetes widoku** lub **wyświetlanie dzienników kontenera** opcji w okienku podglądu. **Wyszukiwanie w dzienniku** po prawej stronie portalu platformy Azure były na zostanie wyświetlona strona.

![Analiza danych w usłudze Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

Dane wyjściowe dzienników kontenera, który jest przekazywany do swojego obszaru roboczego są STDOUT i STDERR. Ponieważ usługa Azure Monitor jest monitorowanie usługi Azure managed Kubernetes (AKS), system klastra kubernetes w usłudze nie są zbierane już dziś ze względu na duże obciążenie wygenerowane dane. 

### <a name="example-log-search-queries"></a>Przykład zapytania wyszukiwania w Dzienniku
Często jest to przydatne do tworzenia zapytań, które zaczynać się przykładem lub dwa, a następnie zmodyfikuj je zgodnie z wymaganiami. Aby ułatwić tworzenie bardziej zaawansowanych zapytań, możesz eksperymentować z następujące przykładowe zapytania:

| Zapytanie | Opis | 
|-------|-------------|
| ContainerInventory<br> &#124;Projekt komputera, nazwa, obraz, ImageTag, ContainerState, wartością CreatedTime, StartedTime, FinishedTime<br> &#124;Renderowanie tabeli | Wyświetlić listę wszystkich informacji o cyklu życia kontenera| 
| KubeEvents_CL<br> &#124;gdzie not(isempty(Namespace_s))<br> &#124;Sortuj według malejącej TimeGenerated<br> &#124;Renderowanie tabeli | Zdarzenia Kubernetes|
| ContainerImageInventory<br> &#124;summarize AggregatedValue = count() by obrazu, ImageTag, działa | Spis obrazów | 
| **Wybierz opcję wyświetlania wykresu liniowego**:<br> Perf<br> &#124;Gdzie ObjectName == "K8SContainer" i CounterName == "cpuUsageNanoCores" &#124; Podsumuj AvgCPUUsageNanoCores avg(CounterValue) przez bin (TimeGenerated, 30 min), InstanceName = | Procesora CPU kontenera | 
| **Wybierz opcję wyświetlania wykresu liniowego**:<br> Perf<br> &#124;Gdzie ObjectName == "K8SContainer" i CounterName == "memoryRssBytes" &#124; Podsumuj AvgUsedRssMemoryBytes avg(CounterValue) przez bin (TimeGenerated, 30 min), InstanceName = | Pamięci kontenera |

## <a name="next-steps"></a>Kolejne kroki
Usługa Azure Monitor dla kontenerów nie ma wstępnie zdefiniowany zestaw alertów. Przegląd [Tworzenie alertów wydajności dzięki usłudze Azure Monitor dla kontenerów](container-insights-alerts.md) dowiesz się, jak utworzyć zalecane alerty dla wysokie wykorzystanie procesora CPU i pamięci do obsługi infrastruktury DevOps lub procedur i procesy operacyjne. 