---
title: Metryki platformy Azure Monitor eksportowane za pośrednictwem ustawień diagnostycznych
description: Lista metryk dostępnych dla każdego typu zasobu z Azure Monitor.
services: azure-monitor
ms.topic: reference
ms.date: 02/10/2020
ms.subservice: metrics
ms.openlocfilehash: 7a75655d1707dd2491065974ed8addc4c2da1a6a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77661366"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Metryki platformy Azure Monitor eksportowane za pośrednictwem ustawień diagnostycznych

Azure Monitor domyślnie udostępnia [metryki platformy](data-platform-metrics.md) bez konfiguracji. Zapewnia kilka sposobów współpracy z metrykami platformy, w tym wykresów w portalu, uzyskiwania dostępu do nich za pośrednictwem interfejsu API REST lub wykonywania zapytań przy użyciu programu PowerShell lub interfejsu wiersza polecenia. Zobacz [metryki — obsługa](metrics-supported.md) pełnej listy metryk platformy dostępnych obecnie w ramach skonsolidowanego potoku metryki Azure monitor. Aby wykonać zapytanie o i uzyskać dostęp do tych metryk, użyj [wersji interfejsu api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Inne metryki mogą być dostępne w portalu lub przy użyciu starszych interfejsów API.

Metryki platformy z potoku usługi Azure monitor można wyeksportować do innych lokalizacji na jeden z dwóch sposobów.
1. Wysyłanie do Log Analytics, Event Hubs lub Azure Storage przy użyciu [ustawień diagnostycznych](diagnostic-settings.md) .
2. Korzystanie z [interfejsu API REST metryk](https://docs.microsoft.com/rest/api/monitor/metrics/list)

Ze względu na złożonego w zapleczu Azure Monitor nie wszystkie metryki są eksportowane przy użyciu ustawień diagnostycznych. W poniższej tabeli wymieniono i nie można eksportować za pomocą ustawień diagnostycznych.

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>Zmiana zachowania dla wartości NULL i zero 
 
W przypadku metryk platformy, które można wyeksportować za pomocą ustawień diagnostycznych, istnieje kilka metryk, dla których Azure Monitor interpretuje wartość "0s" jako "null". Spowodowało to kilka pomyłek między rzeczywistym "0s" (emitowanym przez zasób) i interpretowaniem "0s" (wartości null). Od **1 kwietnia** metryki platformy 2020 wyeksportowane za pośrednictwem ustawień diagnostycznych nie będą już eksportowane "0s", chyba że są one rzeczywiście emitowane przez bazowego zasobu. Uwaga:

1.  W przypadku usunięcia grupy zasobów lub określonego zasobu dane metryk z zasobów, których to dotyczy, nie będą już wysyłane do ustawień diagnostycznych. Oznacza to, że nie będzie już wyświetlany w Event Hubs, konta magazynu i Log Analytics obszary robocze.
2.  To ulepszenie będzie dostępne we wszystkich chmurach publicznych i prywatnych.
3.  Ta zmiana nie wpłynie na zachowanie żadnego z następujących środowisk: 
   - Dzienniki zasobów platformy wyeksportowane za pomocą ustawień diagnostycznych
   - Wykresy metryk w Eksplorator metryk
   - Alerty dotyczące metryk platformy
 
## <a name="metrics-exportable-table"></a>Tabela z możliwością eksportowania metryk 

Tabela zawiera następujące kolumny. 
- Eksportować za pomocą ustawień diagnostycznych? 
- Wpływ na wartość NULL/0 
- ResourceType 
- Metryka 
- MetricDisplayName
- Jednostka 
- Agregacja


> [!NOTE]
> Poniższa tabela może mieć poziomy pasek przewijania u dołu. Jeśli uważasz, że brakuje informacji, sprawdź, czy pasek przewijania jest widoczny w lewo.  


| Eksportować za pomocą ustawień diagnostycznych?  | Emituje wartości NULL |  ResourceType  |  Metryka  |  MetricDisplayName  |  Jednostka  |  Agregacja | 
|---|---| ---- | ----- | ------ | ---- | ---- | 
| Tak * * * *  | Nie |  Microsoft.AnalysisServices/servers  |  CleanerCurrentPrice  |  Pamięć: bieżąca cena oczyszczarki  |  Licznik  |  Średnia | 
| Tak * * * *  | Nie |  Microsoft.AnalysisServices/servers  |  CleanerMemoryNonshrinkable  |  Pamięć: pamięć oczyszczarki niemożliwa do zmniejszenia  |  Bajty  |  Średnia | 
| Tak * * * *  | Nie |  Microsoft.AnalysisServices/servers  |  CleanerMemoryShrinkable  |  Pamięć: zmniejszanie ilości pamięci czyszczącej  |  Bajty  |  Średnia | 
| Tak * * * *  | Nie |  Microsoft.AnalysisServices/servers  |  CommandPoolBusyThreads  |  Wątki: zajęte wątki w puli poleceń  |  Licznik  |  Średnia | 
| Tak * * * *  | Nie |  Microsoft.AnalysisServices/servers  |  CommandPoolIdleThreads  |  Wątki: bezczynne wątki w puli poleceń  |  Licznik  |  Średnia | 
| Tak * * * *  | Nie |  Microsoft.AnalysisServices/servers  |  CommandPoolJobQueueLength  |  Długość kolejki zadań puli poleceń  |  Licznik  |  Średnia | 
| Tak * * * *  | Nie |  Microsoft.AnalysisServices/servers  |  Wartości CurrentConnections  |  Połączenie: bieżące połączenia  |  Licznik  |  Średnia | 
| Tak * * * *  | Nie |  Microsoft.AnalysisServices/servers  |  CurrentUserSessions  |  Bieżące sesje użytkowników  |  Licznik  |  Średnia | 
| Tak * * * *  | Nie |  Microsoft.AnalysisServices/servers  |  LongParsingBusyThreads  |  Wątki: zajęte wątki o długotrwałej analizie  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  LongParsingIdleThreads  |  Wątki: długotrwałe wątki bezczynne analizy  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  LongParsingJobQueueLength  |  Wątki: Długość kolejki zadań o długotrwałej analizie  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  mashup_engine_memory_metric  |  Pamięć aparatu M  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  mashup_engine_private_bytes_metric  |  Liczba prywatnych bajtów aparatu M  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  mashup_engine_qpu_metric  |  M QPU aparatu  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  mashup_engine_virtual_bytes_metric  |  Liczba bajtów wirtualnych aparatu M  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  memory_metric  |  Memory (Pamięć)  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  memory_thrashing_metric  |  Migotanie pamięci  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  MemoryLimitHard  |  Pamięć: sztywny limit pamięci  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  MemoryLimitHigh  |  Pamięć: limit pamięci jest wysoki  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  MemoryLimitLow  |  Pamięć: limit pamięci — niski  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  MemoryLimitVertiPaq  |  Pamięć: limit pamięci — tryb VertiPaq  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  MemoryUsage  |  Pamięć: użycie pamięci  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  private_bytes_metric  |  Prywatne bajty  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyIOJobThreads  |  Wątki: wątki zadań we/wy puli przetwarzania  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyNonIOThreads  |  Wątki: zajęte wątki innych niż we/wy puli przetwarzania  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleIOJobThreads  |  Wątki: wątki zadań we/wy puli przetwarzania  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleNonIOThreads  |  Wątki: bezczynne wątki inne niż we/wy puli przetwarzania  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIOJobQueueLength  |  Wątki: Długość kolejki zadań we/wy puli przetwarzania  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  ProcessingPoolJobQueueLength  |  Długość kolejki zadań puli przetwarzania  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  qpu_metric  |  QPU  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  QueryPoolBusyThreads  |  Wątki zajęte w puli zapytań  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  QueryPoolIdleThreads  |  Wątki: bezczynne wątki puli zapytań  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  QueryPoolJobQueueLength  |  Wątki: Długość kolejki zadań puli zapytań  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  Działa  |  Pamięć: limit przydziału  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  QuotaBlocked  |  Pamięć: zablokowany limit przydziału  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  RowsConvertedPerSec  |  Przetwarzanie: przekonwertowane wiersze na sekundę  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  RowsReadPerSec  |  Przetwarzanie: odczytane wiersze na sekundę  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  RowsWrittenPerSec  |  Przetwarzanie: liczba wierszy na sekundę  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  ShortParsingBusyThreads  |  Wątki: zajęte wątki z krótkim analizowaniem  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  ShortParsingIdleThreads  |  Wątki: bezczynne wątki z krótkim analizowaniem  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  ShortParsingJobQueueLength  |  Wątki: Długość kolejki zadań o krótkiej analizie  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  SuccessfullConnectionsPerSec  |  Udane połączenia na sekundę  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  TotalConnectionFailures  |  Łączna liczba błędów połączenia  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  TotalConnectionRequests  |  Łączna liczba żądań połączenia  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  VertiPaqNonpaged  |  Pamięć: VertiPaq niestronicowana  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  VertiPaqPaged  |  Pamięć: stronicowana na stronie VertiPaq  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisServices/servers  |  virtual_bytes_metric  |  Bajty wirtualne  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  BackendDuration  |  Czas trwania żądań wewnętrznej bazy danych  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ApiManagement/service  |  Pojemność  |  Pojemność  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  Czas trwania  |  Całkowity czas trwania żądań bramy  |  Milisekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  EventHubDroppedEvents  |  Opuszczone zdarzenia EventHub  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  EventHubRejectedEvents  |  Odrzucone zdarzenia EventHub  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  EventHubSuccessfulEvents  |  Pomyślne zdarzenia EventHub  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  EventHubThrottledEvents  |  Ograniczone zdarzenia EventHub  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  EventHubTimedoutEvents  |  Przekroczono limit czasu zdarzeń EventHub  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  EventHubTotalBytesSent  |  Rozmiar zdarzeń EventHub  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  EventHubTotalEvents  |  Łączna liczba zdarzeń EventHub  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  EventHubTotalFailedEvents  |  Niepowodzenie zdarzeń EventHub  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  FailedRequests  |  Nieudane żądania bramy (przestarzałe)  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  OtherRequests  |  Inne żądania bramy (przestarzałe)  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  Żądania  |  Żądania  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  Żądania successfulrequests  |  Pomyślne żądania bramy (przestarzałe)  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  TotalRequests  |  Łączna liczba żądań bramy (przestarzałe)  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  UnauthorizedRequests  |  Nieautoryzowane żądania bramy (przestarzałe)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  AppCpuUsagePercentage  |  Procent użycia procesora aplikacji  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  AppMemoryCommitted  |  Przypisana pamięć aplikacji  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  AppMemoryMax  |  Maksymalna ilość pamięci aplikacji  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  AppMemoryUsed  |  Użyta pamięć aplikacji  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  GCPauseTotalCount  |  Liczba wstrzymań GC  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  GCPauseTotalTime  |  Całkowity czas wstrzymania odzyskiwania pamięci  |  Milisekundy  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  MaxOldGenMemoryPoolBytes  |  Maksymalna dostępna wartość starego rozmiaru danych generacji  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  OldGenMemoryPoolBytes  |  Stary rozmiar danych generacji  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  OldGenPromotedBytes  |  Podwyższ poziom do starego rozmiaru danych generacji  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  SystemCpuUsagePercentage  |  Procent użycia procesora systemu  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatErrorCount  |  Błąd globalny Tomcat  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatReceivedBytes  |  Całkowita liczba odebranych bajtów Tomcat  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatRequestMaxTime  |  Maksymalny czas żądania Tomcat  |  Milisekundy  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatRequestTotalCount  |  Łączna liczba żądań Tomcat  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatRequestTotalTime  |  Łączny czas żądania Tomcat  |  Milisekundy  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatResponseAvgTime  |  Średni czas żądania Tomcat  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatSentBytes  |  Całkowita liczba wysłanych bajtów Tomcat  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatSessionActiveCurrentCount  |  Liczba aktywności sesji Tomcat  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatSessionActiveMaxCount  |  Maksymalna liczba aktywnych sesji Tomcat  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatSessionAliveMaxTime  |  Maksymalny czas aktywności sesji Tomcat  |  Milisekundy  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatSessionCreatedCount  |  Liczba utworzonych sesji Tomcat  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatSessionExpiredCount  |  Liczba wygasłych sesji Tomcat  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatSessionRejectedCount  |  Liczba odrzuconych sesji Tomcat  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  YoungGenPromotedBytes  |  Podwyższ poziom do rozmiaru danych dla małych generacji  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Automation/automationAccounts  |  TotalJob  |  Łączna liczba zadań  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Automation/automationAccounts  |  TotalUpdateDeploymentMachineRuns  |  Łączna liczba uruchomień maszyn wdrożenia aktualizacji  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Automation/automationAccounts  |  TotalUpdateDeploymentRuns  |  Łączna liczba przebiegów wdrożenia aktualizacji  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchAccounts  |  CoreCount  |  Liczba dedykowanych rdzeni  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchAccounts  |  CreatingNodeCount  |  Tworzenie liczby węzłów  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchAccounts  |  IdleNodeCount  |  Liczba węzłów bezczynności  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchAccounts  |  JobDeleteCompleteEvent  |  Zdarzenia ukończenia usuwania zadania  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchAccounts  |  JobDeleteStartEvent  |  Zdarzenia rozpoczęcia usuwania zadania  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchAccounts  |  JobDisableCompleteEvent  |  Zdarzenia ukończenia wyłączania zadania  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchAccounts  |  JobDisableStartEvent  |  Wyłącz zdarzenia uruchamiania dla zadania  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchAccounts  |  JobStartEvent  |  Zdarzenia uruchamiania zadania  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchAccounts  |  JobTerminateCompleteEvent  |  Zdarzenia ukończenia kończenia zadania  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchAccounts  |  JobTerminateStartEvent  |  Zdarzenia rozpoczęcia zakończenia zadania  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchAccounts  |  LeavingPoolNodeCount  |  Opuszczanie liczby węzłów puli  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchAccounts  |  LowPriorityCoreCount  |  LowPriority rdzeń  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchAccounts  |  OfflineNodeCount  |  Liczba węzłów w trybie offline  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchAccounts  |  PoolCreateEvent  |  Zdarzenia tworzenia puli  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchAccounts  |  PoolDeleteCompleteEvent  |  Zdarzenia ukończenia usuwania puli  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchAccounts  |  PoolDeleteStartEvent  |  Zdarzenia uruchamiania usuwania puli  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchAccounts  |  PoolResizeCompleteEvent  |  Zdarzenia ukończenia zmiany rozmiaru puli  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchAccounts  |  PoolResizeStartEvent  |  Zdarzenia rozpoczęcia zmiany rozmiaru puli  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchAccounts  |  PreemptedNodeCount  |  Liczba przeniesiona węzłów  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchAccounts  |  RebootingNodeCount  |  Ponowny rozruch liczby węzłów  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchAccounts  |  ReimagingNodeCount  |  Liczba węzłów regraficznych  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchAccounts  |  RunningNodeCount  |  Liczba uruchomionych węzłów  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchAccounts  |  StartingNodeCount  |  Początkowa liczba węzłów  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchAccounts  |  StartTaskFailedNodeCount  |  Uruchamianie zadania nie powiodło się liczba węzłów  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchAccounts  |  TaskCompleteEvent  |  Zdarzenia ukończenia zadania  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchAccounts  |  TaskFailEvent  |  Zdarzenia błędów zadań  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchAccounts  |  TaskStartEvent  |  Zdarzenia uruchamiania zadania  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchAccounts  |  TotalLowPriorityNodeCount  |  Liczba węzłów o niskim priorytecie  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchAccounts  |  TotalNodeCount  |  Liczba dedykowanych węzłów  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchAccounts  |  UnusableNodeCount  |  Liczba węzłów, których nie można użyć  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchAccounts  |  WaitingForStartTaskNodeCount  |  Oczekiwanie na liczbę węzłów zadania uruchamiania  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Aktywne rdzenie  |  Aktywne rdzenie  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Aktywne węzły  |  Aktywne węzły  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Rdzenie bezczynne  |  Rdzenie bezczynne  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Węzły bezczynne  |  Węzły bezczynne  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Ukończono zadanie  |  Ukończono zadanie  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Przesłane zadanie  |  Przesłane zadanie  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Opuszczanie rdzeni  |  Opuszczanie rdzeni  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Opuszczanie węzłów  |  Opuszczanie węzłów  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Występujące rdzenie  |  Występujące rdzenie  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Zastępujące węzły  |  Zastępujące węzły  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Procent wykorzystania przydziałów  |  Procent wykorzystania przydziałów  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Łączna liczba rdzeni  |  Łączna liczba rdzeni  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Łączna liczba węzłów  |  Łączna liczba węzłów  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Rdzenie, których nie można używać  |  Rdzenie, których nie można używać  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Węzły niezdatne do użytku  |  Węzły niezdatne do użytku  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  ConnectionAccepted  |  Zaakceptowane połączenia  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  ConnectionActive  |  Aktywne połączenia  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  ConnectionHandled  |  Obsłużone połączenia  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  CpuUsagePercentageInDouble  |  Procent użycia procesora CPU  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  IOReadBytes  |  Bajty odczytu we/wy  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  IOWriteBytes  |  Bajty zapisu we/wy  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  MemoryLimit  |  Limit pamięci  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  MemoryUsage  |  Użycie pamięci  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  MemoryUsagePercentageInDouble  |  Procent użycia pamięci  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  PendingTransactions  |  Oczekujące transakcje  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  ProcessedBlocks  |  Przetworzone bloki  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  ProcessedTransactions  |  Przetworzone transakcje  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  QueuedTransactions  |  Transakcje w kolejce  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  RequestHandled  |  Obsłużone żądania  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  StorageUsage  |  Użycie magazynu  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cachehits  |  Trafienia pamięci podręcznej  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cachehits0  |  Trafienia pamięci podręcznej (fragmentu 0)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cachehits1  |  Trafienia pamięci podręcznej (fragmentu 1)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cachehits2  |  Trafienia pamięci podręcznej (fragmentu 2)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cachehits3  |  Trafienia pamięci podręcznej (fragmentu 3)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cachehits4  |  Trafienia pamięci podręcznej (fragmentu 4)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cachehits5  |  Trafienia pamięci podręcznej (fragmentu 5)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cachehits6  |  Trafienia pamięci podręcznej (fragmentu 6)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cachehits7  |  Trafienia pamięci podręcznej (fragmentu 7)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cachehits8  |  Trafienia pamięci podręcznej (fragmentu 8)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cachehits9  |  Trafienia pamięci podręcznej (fragmentu 9)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cacheLatency  |  Mikrosekundy opóźnienia pamięci podręcznej (wersja zapoznawcza)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cachemisses  |  Chybienia w pamięci podręcznej  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cachemisses0  |  Chybienia w pamięci podręcznej (fragmentu 0)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cachemisses1  |  Chybienia w pamięci podręcznej (fragmentu 1)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cachemisses2  |  Chybienia w pamięci podręcznej (fragmentu 2)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cachemisses3  |  Chybienia w pamięci podręcznej (fragmentu 3)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cachemisses4  |  Chybienia w pamięci podręcznej (fragmentu 4)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cachemisses5  |  Chybienia w pamięci podręcznej (fragmentu 5)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cachemisses6  |  Chybienia w pamięci podręcznej (fragmentu 6)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cachemisses7  |  Chybienia w pamięci podręcznej (fragmentu 7)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cachemisses8  |  Chybienia w pamięci podręcznej (fragmentu 8)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cachemisses9  |  Chybienia w pamięci podręcznej (fragmentu 9)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cacheRead  |  Odczyt pamięci podręcznej  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cacheRead0  |  Odczyt pamięci podręcznej (fragmentu 0)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cacheRead1  |  Odczyt pamięci podręcznej (fragmentu 1)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cacheRead2  |  Odczyt pamięci podręcznej (fragmentu 2)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cacheRead3  |  Odczyt pamięci podręcznej (fragmentu 3)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cacheRead4  |  Odczyt pamięci podręcznej (fragmentu 4)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cacheRead5  |  Odczyt pamięci podręcznej (fragmentu 5)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cacheRead6  |  Odczyt pamięci podręcznej (fragmentu 6)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cacheRead7  |  Odczyt pamięci podręcznej (fragmentu 7)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cacheRead8  |  Odczyt pamięci podręcznej (fragmentu 8)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cacheRead9  |  Odczyt pamięci podręcznej (fragmentu 9)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cacheWrite  |  Zapis pamięci podręcznej  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cacheWrite0  |  Zapis pamięci podręcznej (fragmentu 0)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cacheWrite1  |  Zapisywanie pamięci podręcznej (fragmentu 1)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cacheWrite2  |  Zapisywanie pamięci podręcznej (fragmentu 2)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cacheWrite3  |  Zapis w pamięci podręcznej (fragmentu 3)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cacheWrite4  |  Zapis pamięci podręcznej (fragmentu 4)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cacheWrite5  |  Zapis w pamięci podręcznej (fragmentu 5)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cacheWrite6  |  Zapis w pamięci podręcznej (fragmentu 6)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cacheWrite7  |  Zapis w pamięci podręcznej (fragmentu 7)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cacheWrite8  |  Zapis w pamięci podręcznej (fragmentu 8)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  cacheWrite9  |  Zapis w pamięci podręcznej (fragmentu 9)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  connectedclients  |  Podłączeni klienci  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  connectedclients0  |  Połączeni klienci (fragmentu 0)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  connectedclients1  |  Połączeni klienci (fragmentu 1)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  connectedclients2  |  Połączeni klienci (fragmentu 2)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  connectedclients3  |  Połączeni klienci (fragmentu 3)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  connectedclients4  |  Połączeni klienci (fragmentu 4)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  connectedclients5  |  Połączeni klienci (fragmentu 5)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  connectedclients6  |  Połączeni klienci (fragmentu 6)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  connectedclients7  |  Połączeni klienci (fragmentu 7)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  connectedclients8  |  Połączeni klienci (fragmentu 8)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  connectedclients9  |  Połączeni klienci (fragmentu 9)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  błędy  |  Błędy  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  evictedkeys  |  Wykluczone klucze  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  evictedkeys0  |  Wykluczone klucze (fragmentu 0)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  evictedkeys1  |  Wykluczone klucze (fragmentu 1)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  evictedkeys2  |  Wykluczone klucze (fragmentu 2)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  evictedkeys3  |  Wykluczone klucze (fragmentu 3)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  evictedkeys4  |  Wykluczone klucze (fragmentu 4)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  evictedkeys5  |  Wykluczone klucze (fragmentu 5)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  evictedkeys6  |  Wykluczone klucze (fragmentu 6)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  evictedkeys7  |  Wykluczone klucze (fragmentu 7)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  evictedkeys8  |  Wykluczone klucze (fragmentu 8)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  evictedkeys9  |  Wykluczone klucze (fragmentu 9)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  expiredkeys  |  Wygasłe klucze  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  expiredkeys0  |  Wygasłe klucze (fragmentu 0)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  expiredkeys1  |  Wygasłe klucze (fragmentu 1)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  expiredkeys2  |  Wygasłe klucze (fragmentu 2)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  expiredkeys3  |  Wygasłe klucze (fragmentu 3)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  expiredkeys4  |  Wygasłe klucze (fragmentu 4)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  expiredkeys5  |  Wygasłe klucze (fragmentu 5)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  expiredkeys6  |  Wygasłe klucze (fragmentu 6)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  expiredkeys7  |  Wygasłe klucze (fragmentu 7)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  expiredkeys8  |  Wygasłe klucze (fragmentu 8)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  expiredkeys9  |  Wygasłe klucze (fragmentu 9)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  GetCommands  |  Kto  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  getcommands0  |  Pobiera (fragmentu 0)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  getcommands1  |  Pobiera (fragmentu 1)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  getcommands2  |  Pobiera (fragmentu 2)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  getcommands3  |  Pobiera (fragmentu 3)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  getcommands4  |  Pobiera (fragmentu 4)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  getcommands5  |  Pobiera (fragmentu 5)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  getcommands6  |  Pobiera (fragmentu 6)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  getcommands7  |  Pobiera (fragmentu 7)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  getcommands8  |  Pobiera (fragmentu 8)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  getcommands9  |  Pobiera (fragmentu 9)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  operationsPerSecond  |  Liczba operacji na sekundę  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  operationsPerSecond0  |  Operacje na sekundę (fragmentu 0)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  operationsPerSecond1  |  Operacje na sekundę (fragmentu 1)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  operationsPerSecond2  |  Operacje na sekundę (fragmentu 2)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  operationsPerSecond3  |  Operacje na sekundę (fragmentu 3)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  operationsPerSecond4  |  Operacje na sekundę (fragmentu 4)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  operationsPerSecond5  |  Operacje na sekundę (fragmentu 5)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  operationsPerSecond6  |  Operacje na sekundę (fragmentu 6)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  operationsPerSecond7  |  Operacje na sekundę (fragmentu 7)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  operationsPerSecond8  |  Operacje na sekundę (fragmentu 8)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  operationsPerSecond9  |  Operacje na sekundę (fragmentu 9)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  percentProcessorTime  |  Procesor CPU  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  percentProcessorTime0  |  Procesor CPU (fragmentu 0)  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  percentProcessorTime1  |  Procesor CPU (fragmentu 1)  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  percentProcessorTime2  |  Procesor CPU (fragmentu 2)  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  percentProcessorTime3  |  Procesor CPU (fragmentu 3)  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  percentProcessorTime4  |  Procesor CPU (fragmentu 4)  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  percentProcessorTime5  |  Procesor CPU (fragmentu 5)  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  percentProcessorTime6  |  Procesor CPU (fragmentu 6)  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  percentProcessorTime7  |  Procesor CPU (fragmentu 7)  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  percentProcessorTime8  |  Procesor CPU (fragmentu 8)  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  percentProcessorTime9  |  Procesor CPU (fragmentu 9)  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  serverLoad  |  Obciążenie serwera  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  serverLoad0  |  Ładowanie serwera (fragmentu 0)  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  serverLoad1  |  Ładowanie serwera (fragmentu 1)  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  serverLoad2  |  Ładowanie serwera (fragmentu 2)  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  serverLoad3  |  Ładowanie serwera (fragmentu 3)  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  serverLoad4  |  Ładowanie serwera (fragmentu 4)  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  serverLoad5  |  Ładowanie serwera (fragmentu 5)  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  serverLoad6  |  Ładowanie serwera (fragmentu 6)  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  serverLoad7  |  Ładowanie serwera (fragmentu 7)  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  serverLoad8  |  Ładowanie serwera (fragmentu 8)  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  serverLoad9  |  Ładowanie serwera (fragmentu 9)  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  SetCommands  |  Przywraca  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  setcommands0  |  Zestawy (fragmentu 0)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  setcommands1  |  Zestawy (fragmentu 1)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  setcommands2  |  Zestawy (fragmentu 2)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  setcommands3  |  Zestawy (fragmentu 3)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  setcommands4  |  Zestawy (fragmentu 4)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  setcommands5  |  Zestawy (fragmentu 5)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  setcommands6  |  Zestawy (fragmentu 6)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  setcommands7  |  Zestawy (fragmentu 7)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  setcommands8  |  Zestawy (fragmentu 8)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  setcommands9  |  Zestawy (fragmentu 9)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  totalcommandsprocessed  |  Łączna liczba operacji  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  totalcommandsprocessed0  |  Łączna liczba operacji (fragmentu 0)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  totalcommandsprocessed1  |  Łączna liczba operacji (fragmentu 1)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  totalcommandsprocessed2  |  Łączna liczba operacji (fragmentu 2)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  totalcommandsprocessed3  |  Łączna liczba operacji (fragmentu 3)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  totalcommandsprocessed4  |  Łączna liczba operacji (fragmentu 4)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  totalcommandsprocessed5  |  Łączna liczba operacji (fragmentu 5)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  totalcommandsprocessed6  |  Łączna liczba operacji (fragmentu 6)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  totalcommandsprocessed7  |  Łączna liczba operacji (fragmentu 7)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  totalcommandsprocessed8  |  Łączna liczba operacji (fragmentu 8)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  totalcommandsprocessed9  |  Łączna liczba operacji (fragmentu 9)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  totalkeys  |  Łączna liczba kluczy  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  totalkeys0  |  Łączna liczba kluczy (fragmentu 0)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  totalkeys1  |  Łączna liczba kluczy (fragmentu 1)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  totalkeys2  |  Łączna liczba kluczy (fragmentu 2)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  totalkeys3  |  Łączna liczba kluczy (fragmentu 3)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  totalkeys4  |  Łączna liczba kluczy (fragmentu 4)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  totalkeys5  |  Łączna liczba kluczy (fragmentu 5)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  totalkeys6  |  Łączna liczba kluczy (fragmentu 6)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  totalkeys7  |  Łączna liczba kluczy (fragmentu 7)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  totalkeys8  |  Łączna liczba kluczy (fragmentu 8)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  totalkeys9  |  Łączna liczba kluczy (fragmentu 9)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  usedmemory  |  Używana pamięć  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  usedmemory0  |  Używana pamięć (fragmentu 0)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  usedmemory1  |  Używana pamięć (fragmentu 1)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  usedmemory2  |  Używana pamięć (fragmentu 2)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  usedmemory3  |  Używana pamięć (fragmentu 3)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  usedmemory4  |  Używana pamięć (fragmentu 4)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  usedmemory5  |  Używana pamięć (fragmentu 5)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  usedmemory6  |  Używana pamięć (fragmentu 6)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  usedmemory7  |  Używana pamięć (fragmentu 7)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  usedmemory8  |  Używana pamięć (fragmentu 8)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  usedmemory9  |  Używana pamięć (fragmentu 9)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  usedmemorypercentage  |  Wartość procentowa używanej pamięci  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  usedmemoryRss  |  Używana pamięć RSS  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  usedmemoryRss0  |  Używana pamięć RSS (fragmentu 0)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  usedmemoryRss1  |  Używana pamięć RSS (fragmentu 1)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  usedmemoryRss2  |  Używana pamięć RSS (fragmentu 2)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  usedmemoryRss3  |  Używana pamięć RSS (fragmentu 3)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  usedmemoryRss4  |  Używana pamięć RSS (fragmentu 4)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  usedmemoryRss5  |  Używana pamięć RSS (fragmentu 5)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  usedmemoryRss6  |  Używana pamięć RSS (fragmentu 6)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  usedmemoryRss7  |  Używana pamięć RSS (fragmentu 7)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  usedmemoryRss8  |  Używana pamięć RSS (fragmentu 8)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Cache/redis  |  usedmemoryRss9  |  Używana pamięć RSS (fragmentu 9)  |  Bajty  |  Maksimum | 
| Nie  | Nie |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Bajty odczytu dysku/s  |  Odczyt dysku  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Operacje odczytu z dysku/s  |  Operacje odczytu z dysku/s  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Bajty zapisu dysku/s  |  Zapis na dysku  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Operacje zapisu na dysku/s  |  Operacje zapisu na dysku/s  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Sieć — wejście  |  Sieć — wejście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Sieć — wyjście  |  Sieć — wyjście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Procentowe użycie procesora CPU  |  Procentowe użycie procesora CPU  |  Procent  |  Średnia | 
| Nie  | Nie |  Microsoft.ClassicCompute/virtualMachines  |  Bajty odczytu dysku/s  |  Odczyt dysku  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicCompute/virtualMachines  |  Operacje odczytu z dysku/s  |  Operacje odczytu z dysku/s  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft.ClassicCompute/virtualMachines  |  Bajty zapisu dysku/s  |  Zapis na dysku  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicCompute/virtualMachines  |  Operacje zapisu na dysku/s  |  Operacje zapisu na dysku/s  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicCompute/virtualMachines  |  Sieć — wejście  |  Sieć — wejście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.ClassicCompute/virtualMachines  |  Sieć — wyjście  |  Sieć — wyjście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.ClassicCompute/virtualMachines  |  Procentowe użycie procesora CPU  |  Procentowe użycie procesora CPU  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts  |  Dostępność  |  Dostępność  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts  |  SuccessE2ELatency  |  Opóźnienie e2e dla powodzenia  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts  |  Transakcje  |  Transakcje  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft. ClassicStorage/storageAccounts  |  UsedCapacity  |  Używana pojemność  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  Dostępność  |  Dostępność  |  Procent  |  Średnia | 
| Nie  | Nie |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  BlobCapacity  |  Pojemność obiektu blob  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  BlobCount  |  Liczba obiektów blob  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  ContainerCount  |  Liczba kontenerów obiektów blob  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  IndexCapacity  |  Pojemność indeksu  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Opóźnienie e2e dla powodzenia  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  Transakcje  |  Transakcje  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  Dostępność  |  Dostępność  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  FileCapacity  |  Pojemność plików  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  FileCount  |  Liczba plików  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  FileShareCount  |  Liczba udziałów plików  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  FileShareQuota  |  Rozmiar przydziału udziału plików  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  Liczba migawek udziału plików  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  FileShareSnapshotSize  |  Rozmiar migawki udziału plików  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  SuccessE2ELatency  |  Opóźnienie e2e dla powodzenia  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  Transakcje  |  Transakcje  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  Dostępność  |  Dostępność  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  QueueCapacity  |  Pojemność kolejki  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  QueueCount  |  Liczba kolejek  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  QueueMessageCount  |  Liczba komunikatów w kolejce  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Opóźnienie e2e dla powodzenia  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  Transakcje  |  Transakcje  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  Dostępność  |  Dostępność  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Opóźnienie e2e dla powodzenia  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  TableCapacity  |  Pojemność tabeli  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  TableCount  |  Liczba tabel  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  TableEntityCount  |  Liczba jednostek tabel  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  Transakcje  |  Transakcje  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  BlockedCalls  |  Zablokowane wywołania  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  CharactersTrained  |  Znaki przeszkolone  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  CharactersTranslated  |  Znaki tłumaczone  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  ClientErrors  |  Błędy klienta  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  Dane  |  Dane w  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  DataOut  |  Dane wychodzące  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  Opóźnienie  |  Opóźnienie  |  MilliSeconds  |  Średnia | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  Błędy servererrors  |  Błędy serwera  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  SpeechSessionDuration  |  Czas trwania sesji mowy  |  S  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  SuccessfulCalls  |  Pomyślne wywołania  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  TotalCalls  |  Łączna liczba wywołań  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  TotalErrors  |  Całkowita liczba błędów  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  TotalTokenCalls  |  Łączna liczba wywołań tokenów  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  TotalTransactions  |  Łączna liczba transakcji  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Wykorzystane środki CPU  |  Wykorzystane środki CPU  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Pozostałe kredyty procesora CPU  |  Pozostałe kredyty procesora CPU  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Głębokość kolejki dysku danych  |  Głębokość kolejki dysku danych (wersja zapoznawcza)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty odczytu dysku danych/s  |  Bajty odczytu dysku danych/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje odczytu z dysku danych/s  |  Operacje odczytu z dysku danych/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty zapisu na dysku danych/s  |  Bajty zapisu na dysku danych/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje zapisu na dysku danych/s  |  Operacje zapisu na dysku danych/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty odczytu dysku  |  Bajty odczytu dysku  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje odczytu z dysku/s  |  Operacje odczytu z dysku/s  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty zapisu dysku  |  Bajty zapisu dysku  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje zapisu na dysku/s  |  Operacje zapisu na dysku/s  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Przepływy przychodzące  |  Przepływy przychodzące  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Maksymalny współczynnik tworzenia przepływów przychodzących  |  Maksymalny współczynnik tworzenia przepływów przychodzących (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Sieć — wejście  |  Sieć jest rozliczana (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Sieć łącznie  |  Sieć łącznie  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Sieć — wyjście  |  Sieć — do rozliczania (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Całkowita liczba sieci  |  Całkowita liczba sieci  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Głębokość kolejki dysku systemu operacyjnego  |  Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty odczytu dysku systemu operacyjnego/s  |  Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje odczytu z dysku systemu operacyjnego/s  |  Operacje odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty zapisu dysku systemu operacyjnego/s  |  Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje zapisu na dysku systemu operacyjnego/s  |  Operacje zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Głębokość kolejki systemu operacyjnego na dysku  |  Głębokość kolejki dysku systemu operacyjnego (przestarzałe)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty odczytu dysku systemu operacyjnego/s  |  Bajty odczytu z dysku systemu operacyjnego/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje odczytu na dysku systemu operacyjnego/s  |  Operacje odczytu z dysku systemu operacyjnego/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty zapisu na dysku systemu operacyjnego/s  |  Bajty zapisu dysku systemu operacyjnego/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje zapisu na dysku systemu operacyjnego/s  |  Operacje zapisu na dysku systemu operacyjnego/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Przepływy wychodzące  |  Przepływy wychodzące  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Maksymalny współczynnik tworzenia przepływów wychodzących  |  Maksymalny współczynnik tworzenia przepływów wychodzących (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Na dysk głębokość kolejki  |  Głębokość kolejki dysku danych (przestarzałe)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty odczytu na dysk/s  |  Bajty odczytu dysku danych/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje odczytu na dysk/s  |  Operacje odczytu z dysku danych/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty zapisu na dysku/s  |  Bajty zapisu na dysku danych/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje zapisu na dysku/s  |  Operacje zapisu na dysku danych/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Procentowe użycie procesora CPU  |  Procentowe użycie procesora CPU  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium  |  Trafienie odczytu pamięci podręcznej dysku danych Premium (wersja zapoznawcza)  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium  |  Chybienia odczytu pamięci podręcznej dysku z danymi Premium (wersja zapoznawcza)  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium  |  Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium  |  Brak chybień w pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Wykorzystane środki CPU  |  Wykorzystane środki CPU  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Pozostałe kredyty procesora CPU  |  Pozostałe kredyty procesora CPU  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Głębokość kolejki dysku danych  |  Głębokość kolejki dysku danych (wersja zapoznawcza)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty odczytu dysku danych/s  |  Bajty odczytu dysku danych/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje odczytu z dysku danych/s  |  Operacje odczytu z dysku danych/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty zapisu na dysku danych/s  |  Bajty zapisu na dysku danych/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje zapisu na dysku danych/s  |  Operacje zapisu na dysku danych/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty odczytu dysku  |  Bajty odczytu dysku  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje odczytu z dysku/s  |  Operacje odczytu z dysku/s  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty zapisu dysku  |  Bajty zapisu dysku  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje zapisu na dysku/s  |  Operacje zapisu na dysku/s  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Przepływy przychodzące  |  Przepływy przychodzące  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Maksymalny współczynnik tworzenia przepływów przychodzących  |  Maksymalny współczynnik tworzenia przepływów przychodzących (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Sieć — wejście  |  Sieć jest rozliczana (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Sieć łącznie  |  Sieć łącznie  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Sieć — wyjście  |  Sieć — do rozliczania (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Całkowita liczba sieci  |  Całkowita liczba sieci  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Głębokość kolejki dysku systemu operacyjnego  |  Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty odczytu dysku systemu operacyjnego/s  |  Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje odczytu z dysku systemu operacyjnego/s  |  Operacje odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty zapisu dysku systemu operacyjnego/s  |  Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje zapisu na dysku systemu operacyjnego/s  |  Operacje zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Głębokość kolejki systemu operacyjnego na dysku  |  Głębokość kolejki dysku systemu operacyjnego (przestarzałe)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty odczytu dysku systemu operacyjnego/s  |  Bajty odczytu z dysku systemu operacyjnego/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje odczytu na dysku systemu operacyjnego/s  |  Operacje odczytu z dysku systemu operacyjnego/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty zapisu na dysku systemu operacyjnego/s  |  Bajty zapisu dysku systemu operacyjnego/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje zapisu na dysku systemu operacyjnego/s  |  Operacje zapisu na dysku systemu operacyjnego/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Przepływy wychodzące  |  Przepływy wychodzące  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Maksymalny współczynnik tworzenia przepływów wychodzących  |  Maksymalny współczynnik tworzenia przepływów wychodzących (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Na dysk głębokość kolejki  |  Głębokość kolejki dysku danych (przestarzałe)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty odczytu na dysk/s  |  Bajty odczytu dysku danych/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje odczytu na dysk/s  |  Operacje odczytu z dysku danych/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty zapisu na dysku/s  |  Bajty zapisu na dysku danych/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje zapisu na dysku/s  |  Operacje zapisu na dysku danych/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Procentowe użycie procesora CPU  |  Procentowe użycie procesora CPU  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium  |  Trafienie odczytu pamięci podręcznej dysku danych Premium (wersja zapoznawcza)  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium  |  Chybienia odczytu pamięci podręcznej dysku z danymi Premium (wersja zapoznawcza)  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium  |  Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium  |  Brak chybień w pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Wykorzystane środki CPU  |  Wykorzystane środki CPU  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Pozostałe kredyty procesora CPU  |  Pozostałe kredyty procesora CPU  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Głębokość kolejki dysku danych  |  Głębokość kolejki dysku danych (wersja zapoznawcza)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty odczytu dysku danych/s  |  Bajty odczytu dysku danych/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operacje odczytu z dysku danych/s  |  Operacje odczytu z dysku danych/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty zapisu na dysku danych/s  |  Bajty zapisu na dysku danych/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operacje zapisu na dysku danych/s  |  Operacje zapisu na dysku danych/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty odczytu dysku  |  Bajty odczytu dysku  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operacje odczytu z dysku/s  |  Operacje odczytu z dysku/s  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty zapisu dysku  |  Bajty zapisu dysku  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operacje zapisu na dysku/s  |  Operacje zapisu na dysku/s  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Przepływy przychodzące  |  Przepływy przychodzące  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Maksymalny współczynnik tworzenia przepływów przychodzących  |  Maksymalny współczynnik tworzenia przepływów przychodzących (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Sieć — wejście  |  Sieć jest rozliczana (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Sieć łącznie  |  Sieć łącznie  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Sieć — wyjście  |  Sieć — do rozliczania (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Całkowita liczba sieci  |  Całkowita liczba sieci  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Głębokość kolejki dysku systemu operacyjnego  |  Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty odczytu dysku systemu operacyjnego/s  |  Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operacje odczytu z dysku systemu operacyjnego/s  |  Operacje odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty zapisu dysku systemu operacyjnego/s  |  Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operacje zapisu na dysku systemu operacyjnego/s  |  Operacje zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Głębokość kolejki systemu operacyjnego na dysku  |  Głębokość kolejki dysku systemu operacyjnego (przestarzałe)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty odczytu dysku systemu operacyjnego/s  |  Bajty odczytu z dysku systemu operacyjnego/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operacje odczytu na dysku systemu operacyjnego/s  |  Operacje odczytu z dysku systemu operacyjnego/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty zapisu na dysku systemu operacyjnego/s  |  Bajty zapisu dysku systemu operacyjnego/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operacje zapisu na dysku systemu operacyjnego/s  |  Operacje zapisu na dysku systemu operacyjnego/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Przepływy wychodzące  |  Przepływy wychodzące  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Maksymalny współczynnik tworzenia przepływów wychodzących  |  Maksymalny współczynnik tworzenia przepływów wychodzących (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Na dysk głębokość kolejki  |  Głębokość kolejki dysku danych (przestarzałe)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty odczytu na dysk/s  |  Bajty odczytu dysku danych/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operacje odczytu na dysk/s  |  Operacje odczytu z dysku danych/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty zapisu na dysku/s  |  Bajty zapisu na dysku danych/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operacje zapisu na dysku/s  |  Operacje zapisu na dysku danych/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Procentowe użycie procesora CPU  |  Procentowe użycie procesora CPU  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium  |  Trafienie odczytu pamięci podręcznej dysku danych Premium (wersja zapoznawcza)  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium  |  Chybienia odczytu pamięci podręcznej dysku z danymi Premium (wersja zapoznawcza)  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium  |  Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium  |  Brak chybień w pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ContainerInstance/containerGroups  |  CpuUsage  |  Użycie procesora CPU  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ContainerInstance/containerGroups  |  MemoryUsage  |  Użycie pamięci  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesReceivedPerSecond  |  Bajty odebrane przez sieć na sekundę  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesTransmittedPerSecond  |  Bajty przesyłane przez sieć na sekundę  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ContainerRegistry/registries  |  RunDuration  |  Czas trwania przebiegu  |  Milisekundy  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.ContainerRegistry/registries  |  SuccessfulPullCount  |  Pomyślna liczba ściągania  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ContainerRegistry/registries  |  SuccessfulPushCount  |  Liczba wypychanych zakończonych powodzeniem  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ContainerRegistry/registries  |  TotalPullCount  |  Łączna liczba ściągania  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ContainerRegistry/registries  |  TotalPushCount  |  Łączna liczba wypychanych  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_cpu_cores  |  Łączna liczba dostępnych rdzeni procesora CPU w zarządzanym klastrze  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_memory_bytes  |  Łączna ilość dostępnej pamięci w zarządzanym klastrze  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft.ContainerService/managedClusters  |  kube_node_status_condition  |  Stany różnych warunków węzła  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_phase  |  Liczba etapów według fazy  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_ready  |  Liczba zasobników w stanie gotowe  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  AvailableCapacity  |  Dostępna pojemność  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloud  |  Przekazane bajty w chmurze (urządzenie)  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloudPerShare  |  Przekazane bajty w chmurze (udział)  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughput  |  Przepływność pobierania w chmurze  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughputPerShare  |  Przepływność pobierania w chmurze (udział)  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughput  |  Przepływność przekazywania w chmurze  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughputPerShare  |  Przepływność przekazywania w chmurze (udział)  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  HyperVMemoryUtilization  |  Obliczenia brzegowe — użycie pamięci  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  HyperVVirtualProcessorUtilization  |  Obliczenia brzegowe — procentowy procesor CPU  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  NICReadThroughput  |  Przepływność odczytu (Sieć)  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  NICWriteThroughput  |  Przepływność zapisu (Sieć)  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  Łączna pojemność  |  Łączna pojemność  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataFactory/datafactories  |  FailedRuns  |  Nieudane uruchomienia  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataFactory/datafactories  |  SuccessfulRuns  |  Pomyślne uruchomienia  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataFactory/factories  |  ActivityCancelledRuns  |  Metryki uruchomień działań  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataFactory/factories  |  ActivityFailedRuns  |  Metryki uruchamiania działań zakończonych niepowodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataFactory/factories  |  ActivitySucceededRuns  |  Metryki uruchamiania działań zakończonych powodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataFactory/factories  |  FactorySizeInGbUnits  |  Łączny rozmiar fabryki (jednostka GB)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.DataFactory/factories  |  IntegrationRuntimeAvailableMemory  |  Dostępna pamięć środowiska Integration Runtime  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataFactory/factories  |  IntegrationRuntimeAverageTaskPickupDelay  |  Czas trwania kolejki Integration Runtime  |  S  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataFactory/factories  |  IntegrationRuntimeCpuPercentage  |  Użycie procesora Integration Runtime  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataFactory/factories  |  IntegrationRuntimeQueueLength  |  Długość kolejki środowiska Integration Runtime  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataFactory/factories  |  MaxAllowedFactorySizeInGbUnits  |  Maksymalny dozwolony rozmiar fabryki (jednostka GB)  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.DataFactory/factories  |  MaxAllowedResourceCount  |  Maksymalna dozwolona liczba jednostek  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.DataFactory/factories  |  PipelineCancelledRuns  |  Metryki uruchomień potoku  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataFactory/factories  |  PipelineFailedRuns  |  Metryki uruchomionych potoków zakończonych niepowodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataFactory/factories  |  PipelineSucceededRuns  |  Metryki uruchamiania potoków zakończonych powodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataFactory/factories  |  ResourceCount  |  Łączna liczba jednostek  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.DataFactory/factories  |  TriggerCancelledRuns  |  Anulowane metryki uruchamiania wyzwalacza  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataFactory/factories  |  TriggerFailedRuns  |  Metryki uruchomienia wyzwalacza zakończonego niepowodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataFactory/factories  |  TriggerSucceededRuns  |  Wyzwalacze uruchomienia wyzwalają metryki  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedCancelled  |  Anulowano czas aktualizacji  |  S  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedFailure  |  Czas niepowodzenia  |  S  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedSuccess  |  Czas pomyślnej aktualizacji  |  S  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedCancelled  |  Anulowane zadania  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedFailure  |  Zadania zakończone niepowodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedSuccess  |  Zadania zakończone powodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataLakeStore/accounts  |  Odczyt DataReady  |  Odczytane dane  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataLakeStore/accounts  |  Zapisywana  |  Zapisywane dane  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataLakeStore/accounts  |  ReadRequests  |  Żądania odczytu  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataLakeStore/accounts  |  TotalStorage  |  Łączny magazyn  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.DataLakeStore/accounts  |  WriteRequests  |  Żądania zapisu  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/servers  |  active_connections  |  Aktywne połączenia  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/servers  |  backup_storage_used  |  Używany magazyn kopii zapasowych  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/servers  |  connections_failed  |  Połączenia zakończone niepowodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/servers  |  cpu_percent  |  Procent użycia procesora CPU  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/servers  |  io_consumption_percent  |  Procent operacji we/wy  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/servers  |  memory_percent  |  Procent pamięci  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/servers  |  network_bytes_egress  |  Sieć — wyjście  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/servers  |  network_bytes_ingress  |  Sieć — wejście  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/servers  |  seconds_behind_master  |  Opóźnienie replikacji w sekundach  |  Licznik  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_limit  |  Limit magazynowania dziennika serwera  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_percent  |  Procent magazynu dzienników serwera  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_usage  |  Używany magazyn dzienników serwera  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/servers  |  storage_limit  |  Limit magazynu  |  Bajty  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/servers  |  storage_percent  |  Procent magazynu  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/servers  |  storage_used  |  Używany magazyn  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  active_connections  |  Aktywne połączenia  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  backup_storage_used  |  Używany magazyn kopii zapasowych  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  connections_failed  |  Połączenia zakończone niepowodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  cpu_percent  |  Procent użycia procesora CPU  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  io_consumption_percent  |  Procent operacji we/wy  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  memory_percent  |  Procent pamięci  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  network_bytes_egress  |  Sieć — wyjście  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  network_bytes_ingress  |  Sieć — wejście  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  seconds_behind_master  |  Opóźnienie replikacji w sekundach  |  Licznik  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_limit  |  Limit magazynowania dziennika serwera  |  Bajty  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_percent  |  Procent magazynu dzienników serwera  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_usage  |  Używany magazyn dzienników serwera  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  storage_limit  |  Limit magazynu  |  Bajty  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  storage_percent  |  Procent magazynu  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  storage_used  |  Używany magazyn  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  active_connections  |  Aktywne połączenia  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  backup_storage_used  |  Używany magazyn kopii zapasowych  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  connections_failed  |  Połączenia zakończone niepowodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  cpu_percent  |  Procent użycia procesora CPU  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  io_consumption_percent  |  Procent operacji we/wy  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  memory_percent  |  Procent pamięci  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_egress  |  Sieć — wyjście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_ingress  |  Sieć — wejście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_bytes  |  Maksymalne opóźnienie między replikami  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_seconds  |  Zwłoka repliki  |  S  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_limit  |  Limit magazynowania dziennika serwera  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_percent  |  Procent magazynu dzienników serwera  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_usage  |  Używany magazyn dzienników serwera  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  storage_limit  |  Limit magazynu  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  storage_percent  |  Procent magazynu  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  storage_used  |  Używany magazyn  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/serversv2  |  active_connections  |  Aktywne połączenia  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/serversv2  |  cpu_percent  |  Procent użycia procesora CPU  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/serversv2  |  Wejścia  |  Liczba operacji we/wy na sekundę  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/serversv2  |  memory_percent  |  Procent pamięci  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_egress  |  Sieć — wyjście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_ingress  |  Sieć — wejście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/serversv2  |  storage_percent  |  Procent magazynu  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/serversv2  |  storage_used  |  Używany magazyn  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Devices/konto  |  digitaltwins. Telemetry. nodes  |  Symbol zastępczy telemetrii cyfrowego węzła bliźniaczych reprezentacji  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.abandon.success  |  Porzucone komunikaty C2D  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.complete.success  |  Zakończono dostarczanie komunikatów C2D  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.reject.success  |  Odrzucone komunikaty C2D  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  C2D. Methods. Failure  |  Nieudane wywołania metody bezpośredniej  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  c2d.methods.requestSize  |  Rozmiar żądania wywołań metody bezpośredniej  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  c2d.methods.responseSize  |  Rozmiar odpowiedzi wywołań metody bezpośredniej  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  c2d.methods.success  |  Pomyślne wywołania metody bezpośredniej  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.failure  |  Nieudane odczyty sznurów z zaplecza  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.size  |  Rozmiar odpowiedzi na odwrocie od zaplecza  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.success  |  Pomyślne odczyty sznurów z zaplecza  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.failure  |  Niepowodzenie aktualizacji bliźniaczych z zaplecza  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.size  |  Rozmiar aktualizacji przędzy od zaplecza  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.success  |  Pomyślne aktualizacje bliźniaczych z zaplecza  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  C2DMessagesExpired  |  Komunikaty C2D wygasły (wersja zapoznawcza)  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  konfiguracje  |  Metryki konfiguracji  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.Devices/IotHubs  |  connectedDeviceCount  |  Podłączone urządzenia (wersja zapoznawcza)  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.builtIn.events  |  Routing: komunikaty dostarczane do komunikatów/zdarzeń  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.eventHubs  |  Routing: komunikaty dostarczane do centrum zdarzeń  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusQueues  |  Routing: komunikaty dostarczone do kolejki Service Bus  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusTopics  |  Routing: komunikaty dostarczane do Service Bus tematu  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage  |  Routing: komunikaty dostarczane do magazynu  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.blobs  |  Routing: obiekty blob dostarczane do magazynu  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.bytes  |  Routing: dane dostarczane do magazynu  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.builtIn.events  |  Routing: opóźnienie komunikatów dla komunikatów/zdarzeń  |  Milisekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.eventHubs  |  Routing: opóźnienie komunikatu dla centrum zdarzeń  |  Milisekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusQueues  |  Routing: opóźnienie komunikatu dla kolejki Service Bus  |  Milisekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusTopics  |  Routing: opóźnienie komunikatu dla Service Bus tematu  |  Milisekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.storage  |  Routing: opóźnienie komunikatu dla magazynu  |  Milisekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.dropped  |  Routing: porzucone komunikaty telemetryczne   |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.fallback  |  Routing: komunikaty dostarczane do powrotu  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.invalid  |  Routing: komunikaty telemetryczne są niezgodne  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.orphaned  |  Routing: oddzielone komunikaty telemetryczne   |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.success  |  Routing: dostarczono komunikaty telemetryczne  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.allProtocol  |  Próby wysłania komunikatów telemetrycznych  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.sendThrottle  |  Liczba błędów ograniczania  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.success  |  Wysłane komunikaty telemetryczne  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.failure  |  Nieudane odczyty sznurów z urządzeń  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.size  |  Rozmiar odpowiedzi dla sznurów odczytanych z urządzeń  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.success  |  Pomyślne odczyty sznurów z urządzeń  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.failure  |  Niepowodzenie aktualizacji bliźniaczych z urządzeń  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.size  |  Rozmiar aktualizacji bliźniaczych z urządzeń  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.success  |  Pomyślne aktualizacje bliźniaczych urządzeń  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  dailyMessageQuotaUsed  |  Całkowita liczba użytych komunikatów  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  deviceDataUsage  |  Całkowite użycie danych urządzenia  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  deviceDataUsageV2  |  Całkowite użycie danych urządzenia (wersja zapoznawcza)  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  devices.connectedDevices.allProtocol  |  Podłączone urządzenia (przestarzałe)   |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  Devices. totalDevices  |  Łączna liczba urządzeń (przestarzałe)  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  EventGridDeliveries  |  Dostawy Event Grid (wersja zapoznawcza)  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  EventGridLatency  |  Opóźnienie Event Grid (wersja zapoznawcza)  |  Milisekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.failure  |  Nieudane anulowania zadań  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.success  |  Pomyślne anulowania zadań  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  zadania. ukończone  |  Ukończone zadania  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.failure  |  Nie można utworzyć zadań wywołania metody  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.success  |  Pomyślne utworzenie zadań wywołania metody  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.failure  |  Nie można utworzyć dwuosiowych zadań aktualizacji  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.success  |  Pomyślne utworzenie dwuosiowych zadań aktualizacji  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  zadania. Niepowodzenie  |  Zadania zakończone niepowodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.failure  |  Wywołania zakończone niepowodzeniem do listy zadań  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.success  |  Pomyślne wywołania do zadań na liście  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.failure  |  Nieudane kwerendy zadań  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.success  |  Pomyślne zapytania dotyczące zadań  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.Devices/IotHubs  |  totalDeviceCount  |  Łączna liczba urządzeń (wersja zapoznawcza)  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  twinQueries.failure  |  Niepowodzenie zapytań bliźniaczych  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  twinQueries.resultSize  |  Rozmiar wyniku zapytań bliźniaczych  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Devices/IotHubs  |  twinQueries.success  |  Pomyślne zapytania bliźniaczy  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/provisioningServices  |  AttestationAttempts  |  Próby zaświadczania  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/provisioningServices  |  DeviceAssignments  |  Przypisane urządzenia  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Devices/provisioningServices  |  RegistrationAttempts  |  Próby rejestracji  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseAccounts  |  AvailableStorage  |  Dostępny magazyn  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseAccounts  |  CassandraConnectionClosures  |  Zamknięcia połączeń Cassandra  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequestCharges  |  Opłaty za żądania Cassandra  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequests  |  Żądania Cassandra  |  Licznik  |  Licznik | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseAccounts  |  Datausage  |  Użycie danych  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DocumentDB/databaseAccounts  |  DeleteVirtualNetwork  |  DeleteVirtualNetwork  |  Licznik  |  Licznik | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseAccounts  |  DocumentCount  |  Liczba dokumentów  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseAccounts  |  DocumentQuota  |  Przydział dokumentu  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseAccounts  |  IndexUsage  |  Użycie indeksu  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseAccounts  |  MetadataRequests  |  Żądania metadanych  |  Licznik  |  Licznik | 
| **Tak**  | **Tak** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestCharge  |  Opłata żądania Mongo  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequests  |  Żądania Mongo  |  Licznik  |  Licznik | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsCount  |  Częstotliwość żądania Mongo  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsDelete  |  Mongo — wskaźnik żądania usunięcia  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsInsert  |  Liczba żądań wstawienia Mongo  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsQuery  |  Częstotliwość żądań zapytań Mongo  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsUpdate  |  Częstotliwość żądań aktualizacji Mongo  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseAccounts  |  ProvisionedThroughput  |  Aprowizowana przepływność  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.DocumentDB/databaseAccounts  |  ReplicationLatency  |  Opóźnienie replikacji poziomie P99  |  MilliSeconds  |  Średnia | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseAccounts  |  Dostępność  |  Dostępność usługi  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequests  |  Łączna liczba żądań  |  Licznik  |  Licznik | 
| **Tak**  | **Tak** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequestUnits  |  Łączna liczba jednostek żądania  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft. EnterpriseKnowledgeGraph/usługi  |  FailureCount  |  Liczba błędów  |  Licznik  |  Licznik | 
| Nie  | Nie |  Microsoft. EnterpriseKnowledgeGraph/usługi  |  SuccessCount  |  Liczba sukcesów  |  Licznik  |  Licznik | 
| Nie  | Nie |  Microsoft. EnterpriseKnowledgeGraph/usługi  |  SuccessLatency  |  Opóźnienie sukcesu  |  MilliSeconds  |  Średnia | 
| Nie  | Nie |  Microsoft. EnterpriseKnowledgeGraph/usługi  |  TransactionCount  |  Liczba transakcji  |  Licznik  |  Licznik | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/domeny  |  DeadLetteredCount  |  Zdarzenia utraconych wiadomości  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventGrid/domeny  |  DeliveryAttemptFailCount  |  Zdarzenia zakończonych niepowodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/domeny  |  DeliverySuccessCount  |  Dostarczone zdarzenia  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventGrid/domeny  |  DestinationProcessingDurationInMs  |  Czas przetwarzania docelowego  |  Milisekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/domeny  |  DroppedEventCount  |  Opuszczone zdarzenia  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/domeny  |  MatchedEventCount  |  Dopasowane zdarzenia  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/domeny  |  PublishFailCount  |  Publikowanie zdarzeń zakończonych niepowodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/domeny  |  PublishSuccessCount  |  Zdarzenia opublikowane  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/domeny  |  PublishSuccessLatencyInMs  |  Czas oczekiwania na pomyślne publikowanie  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventGrid/eventSubscriptions  |  DeadLetteredCount  |  Zdarzenia utraconych wiadomości  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventGrid/eventSubscriptions  |  DeliveryAttemptFailCount  |  Zdarzenia zakończonych niepowodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventGrid/eventSubscriptions  |  DeliverySuccessCount  |  Dostarczone zdarzenia  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventGrid/eventSubscriptions  |  DestinationProcessingDurationInMs  |  Czas przetwarzania docelowego  |  Milisekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.EventGrid/eventSubscriptions  |  DroppedEventCount  |  Opuszczone zdarzenia  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventGrid/eventSubscriptions  |  MatchedEventCount  |  Dopasowane zdarzenia  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/extensionTopics  |  PublishFailCount  |  Publikowanie zdarzeń zakończonych niepowodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/extensionTopics  |  PublishSuccessCount  |  Zdarzenia opublikowane  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/extensionTopics  |  PublishSuccessLatencyInMs  |  Czas oczekiwania na pomyślne publikowanie  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/extensionTopics  |  UnmatchedEventCount  |  Niedopasowane zdarzenia  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/tematy  |  PublishFailCount  |  Publikowanie zdarzeń zakończonych niepowodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/tematy  |  PublishSuccessCount  |  Zdarzenia opublikowane  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/tematy  |  PublishSuccessLatencyInMs  |  Czas oczekiwania na pomyślne publikowanie  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/tematy  |  UnmatchedEventCount  |  Niedopasowane zdarzenia  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  Połączeń ActiveConnections  |  Połączeń ActiveConnections  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  AvailableMemory  |  Dostępna pamięć  |  Procent  |  Maksimum | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  CaptureBacklog  |  Zaległości przechwytywania.  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  CapturedBytes  |  Przechwycone bajty.  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  CapturedMessages  |  Przechwycone komunikaty.  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  Połączenia connectionsclosed  |  Połączenia zamknięte.  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  Połączenia connectionsopened  |  Otwarte połączenia.  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  Procesor CPU  |  Procesor CPU  |  Procent  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft. EventHub/klastry  |  Bajty incomingbytes  |  Bajty przychodzące.  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/klastry  |  Komunikaty incomingmessages  |  Komunikaty przychodzące  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/klastry  |  Żądania incomingrequests  |  Żądania przychodzące  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/klastry  |  Bajty outgoingbytes  |  Bajty wychodzące.  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/klastry  |  Komunikaty outgoingmessages  |  Komunikaty wychodzące  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  Błędy quotaexceedederrors  |  Błędy przekroczenia limitu przydziału.  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  Błędy servererrors  |  Błędy serwera.  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  Żądania successfulrequests  |  Żądania zakończone powodzeniem  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  ThrottledRequests  |  Żądania z ograniczeniami.  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  Błędy usererrors  |  Błędy użytkownika.  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/namespaces  |  Połączeń ActiveConnections  |  Połączeń ActiveConnections  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.EventHub/namespaces  |  CaptureBacklog  |  Zaległości przechwytywania.  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/namespaces  |  CapturedBytes  |  Przechwycone bajty.  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/namespaces  |  CapturedMessages  |  Przechwycone komunikaty.  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/namespaces  |  Połączenia connectionsclosed  |  Połączenia zamknięte.  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.EventHub/namespaces  |  Połączenia connectionsopened  |  Otwarte połączenia.  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.EventHub/namespaces  |  EHABL  |  Archiwum komunikatów zaległości (przestarzałe)  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/namespaces  |  EHAMBS  |  Przepływność komunikatów archiwalnych (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/namespaces  |  EHAMSGS  |  Komunikaty archiwalne (przestarzałe)  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/namespaces  |  EHINBYTES  |  Przychodzące bajty (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/namespaces  |  EHINMBS  |  Przychodzące bajty (przestarzałe) (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/namespaces  |  EHINMSGS  |  Komunikaty przychodzące (przestarzałe)  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/namespaces  |  EHOUTBYTES  |  Bajty wychodzące (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/namespaces  |  EHOUTMBS  |  Bajty wychodzące (przestarzałe) (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/namespaces  |  EHOUTMSGS  |  Wiadomości wychodzące (przestarzałe)  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/namespaces  |  FAILREQ  |  Nieudane żądania (przestarzałe)  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/namespaces  |  Bajty incomingbytes  |  Bajty przychodzące.  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/namespaces  |  Komunikaty incomingmessages  |  Komunikaty przychodzące  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/namespaces  |  Żądania incomingrequests  |  Żądania przychodzące  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/namespaces  |  INMSGS  |  Komunikaty przychodzące (przestarzałe) (przestarzałe)  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/namespaces  |  INREQS  |  Żądania przychodzące (przestarzałe)  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/namespaces  |  MIĘDZY  |  Wewnętrzne błędy serwera (przestarzałe)  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/namespaces  |  MISCERR  |  Inne błędy (przestarzałe)  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/namespaces  |  Bajty outgoingbytes  |  Bajty wychodzące.  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/namespaces  |  Komunikaty outgoingmessages  |  Komunikaty wychodzące  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/namespaces  |  OUTMSGS  |  Wiadomości wychodzące (przestarzałe) (przestarzałe)  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/namespaces  |  Błędy quotaexceedederrors  |  Błędy przekroczenia limitu przydziału.  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/namespaces  |  Błędy servererrors  |  Błędy serwera.  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/namespaces  |  Rozmiar  |  Rozmiar  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft.EventHub/namespaces  |  Żądania successfulrequests  |  Żądania zakończone powodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/namespaces  |  SUCCREQ  |  Żądania zakończone powodzeniem (przestarzałe)  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/namespaces  |  SVRBSY  |  Błędy zajęte przez serwer (przestarzałe)  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/namespaces  |  ThrottledRequests  |  Żądania z ograniczeniami.  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/namespaces  |  Błędy usererrors  |  Błędy użytkownika.  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.HDInsight/clusters  |  CategorizedGatewayRequests  |  Skategoryzowane żądania bramy  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.HDInsight/clusters  |  GatewayRequests  |  Żądania bramy  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.HDInsight/clusters  |  NumActiveWorkers  |  Liczba aktywnych procesów roboczych  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.HDInsight/clusters  |  ScalingRequests  |  Skalowanie żądań  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Insights/AutoscaleSettings  |  MetricThreshold  |  Próg metryki  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Insights/AutoscaleSettings  |  ObservedCapacity  |  Zaobserwowana pojemność  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Insights/AutoscaleSettings  |  ObservedMetricValue  |  Obserwowana wartość metryki  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Insights/AutoscaleSettings  |  ScaleActionsInitiated  |  Zainicjowane akcje skalowania  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  availabilityResults/availabilityPercentage  |  Dostępność  |  Procent  |  Średnia | 
| Nie  | Nie |  Microsoft. Insights/składniki  |  availabilityResults/liczba  |  Testy dostępności  |  Licznik  |  Licznik | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  availabilityResults/czas trwania  |  Czas trwania testu dostępności  |  MilliSeconds  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  browserTimings/networkDuration  |  Czas połączenia sieciowego ładowania strony  |  MilliSeconds  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  browserTimings/processingDuration  |  Czas przetwarzania klienta  |  MilliSeconds  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  browserTimings/receiveDuration  |  Czas odpowiedzi na odebranie  |  MilliSeconds  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  browserTimings/sendDuration  |  Czas żądania wysłania  |  MilliSeconds  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  browserTimings/totalDuration  |  Czas ładowania strony w przeglądarce  |  MilliSeconds  |  Średnia | 
| Nie  | Nie |  Microsoft. Insights/składniki  |  zależności/liczba  |  Wywołania zależności  |  Licznik  |  Licznik | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  zależności/czas trwania  |  Czas trwania zależności  |  MilliSeconds  |  Średnia | 
| Nie  | Nie |  Microsoft. Insights/składniki  |  zależności/niepowodzenie  |  Błędy wywołań zależności  |  Licznik  |  Licznik | 
| Nie  | Nie |  Microsoft. Insights/składniki  |  wyjątki/przeglądarka  |  Wyjątki przeglądarki  |  Licznik  |  Licznik | 
| **Tak**  | **Tak** |  Microsoft. Insights/składniki  |  wyjątki/liczba  |  Wyjątki  |  Licznik  |  Licznik | 
| Nie  | Nie |  Microsoft. Insights/składniki  |  wyjątki/serwer  |  Wyjątki serwera  |  Licznik  |  Licznik | 
| **Tak**  | **Tak** |  Microsoft. Insights/składniki  |  pageViews/liczba  |  Wyświetlenia stron  |  Licznik  |  Licznik | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  pageViews/czas trwania  |  Czas ładowania widoku strony  |  MilliSeconds  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Insights/składniki  |  Liczniki wydajności/exceptionsPerSecond  |  Częstotliwość wyjątków  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  Liczniki wydajności/memoryAvailableBytes  |  Dostępna pamięć  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  Liczniki wydajności/processCpuPercentage  |  Procesor CPU procesu  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  Liczniki wydajności/processIOBytesPerSecond  |  Szybkość operacji we/wy procesu  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  Liczniki wydajności/processorCpuPercentage  |  Czas procesora  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  Liczniki wydajności/processPrivateBytes  |  Prywatne bajty procesu  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  Liczniki wydajności/requestExecutionTime  |  Czas wykonywania żądania HTTP  |  MilliSeconds  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Insights/składniki  |  performanceCounters/requestsInQueue  |  Żądania HTTP w kolejce aplikacji  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Insights/składniki  |  performanceCounters/requestsPerSecond  |  Częstotliwość żądań HTTP  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft. Insights/składniki  |  Liczba żądań na sekundę  |  Żądania serwera  |  Licznik  |  Licznik | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  żądania/czas trwania  |  Czas odpowiedzi serwera  |  MilliSeconds  |  Średnia | 
| Nie  | Nie |  Microsoft. Insights/składniki  |  żądania/niepowodzenie  |  Żądania zakończone niepowodzeniem  |  Licznik  |  Licznik | 
| Nie  | Nie |  Microsoft. Insights/składniki  |  żądania/częstotliwość  |  Liczba żądań serwera  |  CountPerSecond  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Insights/składniki  |  ślady/liczba  |  Ścieżki  |  Licznik  |  Licznik | 
| **Tak**  | Nie |  Microsoft.KeyVault/vaults  |  ServiceApiHit  |  Całkowita liczba trafień interfejsu API usługi  |  Licznik  |  Licznik | 
| **Tak**  | Nie |  Microsoft.KeyVault/vaults  |  ServiceApiLatency  |  Ogólne opóźnienie interfejsu API usługi  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft.KeyVault/vaults  |  ServiceApiResult  |  Łączna liczba wyników interfejsu API usługi  |  Licznik  |  Licznik | 
| **Tak**  | **Tak** |  Microsoft.Kusto/Clusters  |  CacheUtilization  |  Użycie pamięci podręcznej  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Kusto/Clusters  |  ContinuousExportMaxLatenessMinutes  |  Maksymalna liczba minut opóźnienia eksportu  |  Licznik  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.Kusto/Clusters  |  ContinuousExportNumOfRecordsExported  |  Eksport ciągły — liczba eksportowanych rekordów  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Kusto/Clusters  |  ContinuousExportPendingCount  |  Liczba oczekujących eksportu ciągłego  |  Licznik  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.Kusto/Clusters  |  ContinuousExportResult  |  Wynik eksportu ciągłego  |  Licznik  |  Licznik | 
| **Tak**  | **Tak** |  Microsoft.Kusto/Clusters  |  Procesor CPU  |  Procesor CPU  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Kusto/Clusters  |  EventsProcessedForEventHubs  |  Zdarzenia przetwarzane (dla centrów zdarzeń/IoT)  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Kusto/Clusters  |  ExportUtilization  |  Użycie eksportu  |  Procent  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.Kusto/Clusters  |  IngestionLatencyInSeconds  |  Opóźnienie pozyskiwania (w sekundach)  |  S  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Kusto/Clusters  |  IngestionResult  |  Wynik pozyskiwania  |  Licznik  |  Licznik | 
| **Tak**  | **Tak** |  Microsoft.Kusto/Clusters  |  IngestionUtilization  |  Wykorzystanie pozyskiwania  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Kusto/Clusters  |  IngestionVolumeInMB  |  Wolumin pozyskiwania (w MB)  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Kusto/Clusters  |  KeepAlive  |  Utrzymywanie aktywności  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Kusto/Clusters  |  QueryDuration  |  Czas trwania zapytania  |  Milisekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Kusto/Clusters  |  SteamingIngestRequestRate  |  Szybkość żądania pozyskiwania strumieniowego  |  Licznik  |  RateRequestsPerSecond | 
| **Tak**  | **Tak** |  Microsoft.Kusto/Clusters  |  StreamingIngestDataRate  |  Szybkość danych pozyskiwania przesyłania strumieniowego  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Kusto/Clusters  |  StreamingIngestDuration  |  Czas trwania pozyskiwania strumieniowego  |  Milisekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Kusto/Clusters  |  StreamingIngestResults  |  Wynik pozyskiwania strumieniowego  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Logic/integrationServiceEnvironments  |  ActionLatency  |  Opóźnienie akcji   |  S  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsCompleted  |  Wykonane akcje   |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsFailed  |  Akcje zakończone niepowodzeniem   |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsSkipped  |  Pominięte akcje   |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsStarted  |  Akcje uruchomione   |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsSucceeded  |  Akcje zakończone powodzeniem   |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Logic/integrationServiceEnvironments  |  ActionSuccessLatency  |  Opóźnienie sukcesu akcji   |  S  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionThrottledEvents  |  Zdarzenia ograniczające akcję  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorMemoryUsage  |  Użycie pamięci przez łącznik dla środowisko usługi integracji  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorProcessorUsage  |  Użycie procesora przez łącznik dla środowisko usługi integracji  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowMemoryUsage  |  Użycie pamięci przez przepływ pracy dla środowisko usługi integracji  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowProcessorUsage  |  Użycie procesora przepływu pracy dla środowisko usługi integracji  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  RunFailurePercentage  |  Procent niepowodzeń przebiegu  |  Procent  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Logic/integrationServiceEnvironments  |  RunLatency  |  Opóźnienie uruchamiania  |  S  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsCancelled  |  Przebiegi anulowane  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsCompleted  |  Przebiegi zakończone  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsFailed  |  Przebiegi zakończone niepowodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsStarted  |  Uruchomienia uruchomione  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsSucceeded  |  Przebiegi zakończone powodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  RunStartThrottledEvents  |  Uruchom zdarzenia uruchamiania z ograniczeniami  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Logic/integrationServiceEnvironments  |  RunSuccessLatency  |  Opóźnienie sukcesu przebiegu  |  S  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  RunThrottledEvents  |  Uruchamianie zdarzeń z ograniczeniami  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerFireLatency  |  Opóźnienie wyzwalania wyzwalacza   |  S  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerLatency  |  Opóźnienie wyzwalacza   |  S  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersCompleted  |  Ukończone wyzwalacze   |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersFailed  |  Wyzwalacze zakończone niepowodzeniem   |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersFired  |  Wyzwolone wyzwalacze   |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersSkipped  |  Pominięte wyzwalacze  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersStarted  |  Uruchomiono wyzwalacze   |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersSucceeded  |  Wyzwalacze zakończone powodzeniem   |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerSuccessLatency  |  Opóźnienie sukcesu wyzwalacza   |  S  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerThrottledEvents  |  Wyzwalaj zdarzenia ograniczające  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Logic/workflows  |  ActionLatency  |  Opóźnienie akcji   |  S  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  ActionsCompleted  |  Wykonane akcje   |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  ActionsFailed  |  Akcje zakończone niepowodzeniem   |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  ActionsSkipped  |  Pominięte akcje   |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  ActionsStarted  |  Akcje uruchomione   |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  ActionsSucceeded  |  Akcje zakończone powodzeniem   |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Logic/workflows  |  ActionSuccessLatency  |  Opóźnienie sukcesu akcji   |  S  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  ActionThrottledEvents  |  Zdarzenia ograniczające akcję  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  BillableActionExecutions  |  Rozliczane wykonania akcji  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  BillableTriggerExecutions  |  Rozliczane wykonania wyzwalacza  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  BillingUsageNativeOperation  |  Użycie rozliczeń dla natywnych wykonań operacji  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  BillingUsageNativeOperation  |  Użycie rozliczeń dla natywnych wykonań operacji  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  Użycie rozliczeń dla wykonywania łączników standardowych  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  Użycie rozliczeń dla wykonywania łączników standardowych  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  BillingUsageStorageConsumption  |  Użycie rozliczeń dla wykonań zużycia magazynu  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  BillingUsageStorageConsumption  |  Użycie rozliczeń dla wykonań zużycia magazynu  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  RunFailurePercentage  |  Procent niepowodzeń przebiegu  |  Procent  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Logic/workflows  |  RunLatency  |  Opóźnienie uruchamiania  |  S  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  RunsCancelled  |  Przebiegi anulowane  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  RunsCompleted  |  Przebiegi zakończone  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  RunsFailed  |  Przebiegi zakończone niepowodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  RunsStarted  |  Uruchomienia uruchomione  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  RunsSucceeded  |  Przebiegi zakończone powodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  RunStartThrottledEvents  |  Uruchom zdarzenia uruchamiania z ograniczeniami  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Logic/workflows  |  RunSuccessLatency  |  Opóźnienie sukcesu przebiegu  |  S  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  RunThrottledEvents  |  Uruchamianie zdarzeń z ograniczeniami  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  TotalBillableExecutions  |  Łączna Liczba wykonań rozliczanych  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Logic/workflows  |  TriggerFireLatency  |  Opóźnienie wyzwalania wyzwalacza   |  S  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Logic/workflows  |  TriggerLatency  |  Opóźnienie wyzwalacza   |  S  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  TriggersCompleted  |  Ukończone wyzwalacze   |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  TriggersFailed  |  Wyzwalacze zakończone niepowodzeniem   |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  TriggersFired  |  Wyzwolone wyzwalacze   |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  TriggersSkipped  |  Pominięte wyzwalacze  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  TriggersStarted  |  Uruchomiono wyzwalacze   |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  TriggersSucceeded  |  Wyzwalacze zakończone powodzeniem   |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Logic/workflows  |  TriggerSuccessLatency  |  Opóźnienie sukcesu wyzwalacza   |  S  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/workflows  |  TriggerThrottledEvents  |  Wyzwalaj zdarzenia ograniczające  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.MachineLearningServices/workspaces  |  Aktywne rdzenie  |  Aktywne rdzenie  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.MachineLearningServices/workspaces  |  Aktywne węzły  |  Aktywne węzły  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.MachineLearningServices/workspaces  |  Ukończone uruchomienia  |  Ukończone uruchomienia  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.MachineLearningServices/workspaces  |  Nieudane uruchomienia  |  Nieudane uruchomienia  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.MachineLearningServices/workspaces  |  Rdzenie bezczynne  |  Rdzenie bezczynne  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.MachineLearningServices/workspaces  |  Węzły bezczynne  |  Węzły bezczynne  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.MachineLearningServices/workspaces  |  Opuszczanie rdzeni  |  Opuszczanie rdzeni  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.MachineLearningServices/workspaces  |  Opuszczanie węzłów  |  Opuszczanie węzłów  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.MachineLearningServices/workspaces  |  Wdrażanie modelu nie powiodło się  |  Wdrażanie modelu nie powiodło się  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.MachineLearningServices/workspaces  |  Uruchomiono Wdrażanie modelu  |  Uruchomiono Wdrażanie modelu  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.MachineLearningServices/workspaces  |  Wdrażanie modelu powiodło się  |  Wdrażanie modelu powiodło się  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.MachineLearningServices/workspaces  |  Nie można zarejestrować modelu  |  Nie można zarejestrować modelu  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.MachineLearningServices/workspaces  |  Rejestrowanie modelu powiodło się  |  Rejestrowanie modelu powiodło się  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.MachineLearningServices/workspaces  |  Występujące rdzenie  |  Występujące rdzenie  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.MachineLearningServices/workspaces  |  Zastępujące węzły  |  Zastępujące węzły  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.MachineLearningServices/workspaces  |  Procent wykorzystania przydziałów  |  Procent wykorzystania przydziałów  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.MachineLearningServices/workspaces  |  Uruchomione uruchomienia  |  Uruchomione uruchomienia  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.MachineLearningServices/workspaces  |  Łączna liczba rdzeni  |  Łączna liczba rdzeni  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.MachineLearningServices/workspaces  |  Łączna liczba węzłów  |  Łączna liczba węzłów  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.MachineLearningServices/workspaces  |  Rdzenie, których nie można używać  |  Rdzenie, których nie można używać  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.MachineLearningServices/workspaces  |  Węzły niezdatne do użytku  |  Węzły niezdatne do użytku  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Maps/konta  |  Dostępność  |  Dostępność  |  Procent  |  Średnia | 
| Nie  | Nie |  Microsoft. Maps/konta  |  Sposób użycia  |  Sposób użycia  |  Licznik  |  Licznik | 
| **Tak**  | Nie |  Microsoft. Media/MediaServices  |  AssetCount  |  Liczba zasobów  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Media/MediaServices  |  AssetQuota  |  Przydział zasobów  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Media/MediaServices  |  AssetQuotaUsedPercentage  |  Procent użycia przydziału zasobów  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Media/MediaServices  |  ContentKeyPolicyCount  |  Liczba zasad dotyczących kluczy zawartości  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Media/MediaServices  |  ContentKeyPolicyQuota  |  Przydział zasad dotyczących kluczy zawartości  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Media/MediaServices  |  ContentKeyPolicyQuotaUsedPercentage  |  Procent użycia przydziału zasad klucza zawartości  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Media/MediaServices  |  StreamingPolicyCount  |  Liczba zasad przesyłania strumieniowego  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Media/MediaServices  |  StreamingPolicyQuota  |  Limit przydziału zasad przesyłania strumieniowego  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Media/MediaServices  |  StreamingPolicyQuotaUsedPercentage  |  Procent użycia limitu przydziału zasad przesyłania strumieniowego  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Media/MediaServices/streamingEndpoints  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Media/MediaServices/streamingEndpoints  |  Żądania  |  Żądania  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Media/MediaServices/streamingEndpoints  |  SuccessE2ELatency  |  Pomyślne zakończenie oczekiwania  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  GCPauseTotalCount  |  Liczba wstrzymań GC  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  GCPauseTotalTime  |  Całkowity czas wstrzymania odzyskiwania pamięci  |  Milisekundy  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  MaxOldGenMemoryPoolBytes  |  Maksymalna dostępna wartość starego rozmiaru danych generacji  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  OldGenMemoryPoolBytes  |  Stary rozmiar danych generacji  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  OldGenPromotedBytes  |  Podwyższ poziom do starego rozmiaru danych generacji  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  ServiceCpuUsagePercentage  |  Procent użycia procesora usługi  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  ServiceMemoryCommitted  |  Przypisano pamięć usługi  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  ServiceMemoryMax  |  Maksymalna ilość pamięci usługi  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  ServiceMemoryUsed  |  Użyta pamięć usługi  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  SystemCpuUsagePercentage  |  Procent użycia procesora systemu  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatErrorCount  |  Błąd globalny Tomcat  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatReceivedBytes  |  Całkowita liczba odebranych bajtów Tomcat  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatRequestMaxTime  |  Maksymalny czas żądania Tomcat  |  Milisekundy  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatRequestTotalCount  |  Łączna liczba żądań Tomcat  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatRequestTotalTime  |  Łączny czas żądania Tomcat  |  Milisekundy  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatResponseAvgTime  |  Średni czas żądania Tomcat  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatSentBytes  |  Całkowita liczba wysłanych bajtów Tomcat  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionActiveCurrentCount  |  Liczba aktywności sesji Tomcat  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionActiveMaxCount  |  Maksymalna liczba aktywnych sesji Tomcat  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionAliveMaxTime  |  Maksymalny czas aktywności sesji Tomcat  |  Milisekundy  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionCreatedCount  |  Liczba utworzonych sesji Tomcat  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionExpiredCount  |  Liczba wygasłych sesji Tomcat  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionRejectedCount  |  Liczba odrzuconych sesji Tomcat  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  YoungGenPromotedBytes  |  Podwyższ poziom do rozmiaru danych dla małych generacji  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolAllocatedUsed  |  Użyte przydzieloną pulę woluminów  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolTotalLogicalSize  |  Łączny rozmiar logiczny puli woluminów  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. NetApp/netAppAccounts/capacityPools/woluminy  |  AverageReadLatency  |  Średnie opóźnienie odczytu  |  MilliSeconds  |  Średnia | 
| **Tak**  | Nie |  Microsoft. NetApp/netAppAccounts/capacityPools/woluminy  |  AverageWriteLatency  |  Średnie opóźnienie zapisu  |  MilliSeconds  |  Średnia | 
| **Tak**  | Nie |  Microsoft. NetApp/netAppAccounts/capacityPools/woluminy  |  ReadIops  |  Odczyt operacji we/wy  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. NetApp/netAppAccounts/capacityPools/woluminy  |  VolumeLogicalSize  |  Rozmiar logiczny woluminu  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. NetApp/netAppAccounts/capacityPools/woluminy  |  VolumeSnapshotSize  |  Rozmiar migawki woluminu  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. NetApp/netAppAccounts/capacityPools/woluminy  |  WriteIops  |  Zapisz operacje we/wy  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/applicationGateways  |  ApplicationGatewayTotalTime  |  Łączny czas Application Gateway  |  MilliSeconds  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/applicationGateways  |  AvgRequestCountPerHealthyHost  |  Liczba żądań na minutę na hosta w dobrej kondycji  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/applicationGateways  |  BackendConnectTime  |  Czas połączenia z zapleczem  |  MilliSeconds  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/applicationGateways  |  BackendFirstByteResponseTime  |  Czas odpowiedzi na pierwszy bajt zaplecza  |  MilliSeconds  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/applicationGateways  |  BackendLastByteResponseTime  |  Czas odpowiedzi ostatniego bajtu wewnętrznej bazy danych  |  MilliSeconds  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  BackendResponseStatus  |  Stan odpowiedzi zaplecza  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  BlockedCount  |  Dystrybucja reguły zablokowanych żądań zapory aplikacji sieci Web  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  BlockedReqCount  |  Liczba zablokowanych żądań zapory aplikacji sieci Web  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  BytesReceived  |  Bajty odebrane  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  BytesSent  |  Bajty wysłane  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft.Network/applicationGateways  |  CapacityUnits  |  Bieżące jednostki wydajności  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/applicationGateways  |  ClientRtt  |  Czas RTT klienta  |  MilliSeconds  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/applicationGateways  |  ComputeUnits  |  Bieżące jednostki obliczeniowe  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  Wartości CurrentConnections  |  Bieżące połączenia  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  FailedRequests  |  Żądania zakończone niepowodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  HealthyHostCount  |  Liczba hostów w dobrej kondycji  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  MatchedCount  |  Dystrybucja reguł dla zapory aplikacji sieci Web  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  ResponseStatus  |  Stan odpowiedzi  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.Network/applicationGateways  |  Przepływność  |  Przepływność  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  TlsProtocol  |  Protokół TLS klienta  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  TotalRequests  |  Łączna liczba żądań  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  UnhealthyHostCount  |  Liczba hostów w złej kondycji  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/azurefirewalls  |  ApplicationRuleHit  |  Liczba trafień reguł aplikacji  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/azurefirewalls  |  Przetwarzanie dataprocessed  |  Przetworzone dane  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/azurefirewalls  |  FirewallHealth  |  Stan kondycji zapory  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/azurefirewalls  |  NetworkRuleHit  |  Liczba trafień reguł sieci  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/azurefirewalls  |  SNATPortUtilization  |  Wykorzystanie portów przez przytranslatora adresów sieciowych  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/Connections  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/Connections  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/dnszones  |  QueryVolume  |  Wolumin zapytania  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.Network/dnszones  |  RecordSetCapacityUtilization  |  Wykorzystanie pojemności zestawu rekordów  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/dnszones  |  RecordSetCount  |  Liczba zestawów rekordów  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/expressRouteCircuits  |  ArpAvailability  |  Dostępność protokołu ARP  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/expressRouteCircuits  |  BgpAvailability  |  Dostępność protokołu BGP  |  Procent  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/expressRouteCircuits  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/expressRouteCircuits  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsInPerSecond  |  GlobalReachBitsInPerSecond  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsOutPerSecond  |  GlobalReachBitsOutPerSecond  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsInPerSecond  |  DroppedInBitsPerSecond  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsOutPerSecond  |  DroppedOutBitsPerSecond  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/expressRouteCircuits/Komunikacja równorzędna  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/expressRouteCircuits/Komunikacja równorzędna  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft. Network/expressRouteGateways  |  ErGatewayConnectionBitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft. Network/expressRouteGateways  |  ErGatewayConnectionBitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/expressRoutePorts  |  AdminState  |  AdminState  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/expressRoutePorts  |  LineProtocol  |  LineProtocol  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/expressRoutePorts  |  PortBitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/expressRoutePorts  |  PortBitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/expressRoutePorts  |  RxLightLevel  |  RxLightLevel  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/expressRoutePorts  |  TxLightLevel  |  TxLightLevel  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Network/usługi frontdoor  |  BackendHealthPercentage  |  Procent kondycji zaplecza  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Network/usługi frontdoor  |  BackendRequestCount  |  Liczba żądań wewnętrznej bazy danych  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Network/usługi frontdoor  |  BackendRequestLatency  |  Opóźnienie żądania wewnętrznej bazy danych  |  MilliSeconds  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Network/usługi frontdoor  |  BillableResponseSize  |  Rozmiar odpowiedzi do rozliczenia  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Network/usługi frontdoor  |  RequestCount  |  Liczba żądań  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Network/usługi frontdoor  |  RequestSize  |  Rozmiar żądania  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Network/usługi frontdoor  |  ResponseSize  |  Rozmiar odpowiedzi  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Network/usługi frontdoor  |  TotalLatency  |  Łączne opóźnienie  |  MilliSeconds  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Network/usługi frontdoor  |  WebApplicationFirewallRequestCount  |  Liczba żądań zapory aplikacji sieci Web  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.Network/loadBalancers  |  AllocatedSnatPorts  |  Przydzielono porty przydziałów (wersja zapoznawcza)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/loadBalancers  |  ByteCount  |  Liczba bajtów  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/loadBalancers  |  DipAvailability  |  Stan sondy kondycji  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/loadBalancers  |  PacketCount  |  Liczba pakietów  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/loadBalancers  |  SnatConnectionCount  |  Liczba połączeń z translatorem adresów sieciowych  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/loadBalancers  |  SYNCount  |  Liczba SYN  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.Network/loadBalancers  |  UsedSnatPorts  |  Używane porty (wersja zapoznawcza)  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/loadBalancers  |  VipAvailability  |  Dostępność ścieżki danych  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/networkInterfaces  |  BytesReceivedRate  |  Bajty odebrane  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/networkInterfaces  |  BytesSentRate  |  Bajty wysłane  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/networkInterfaces  |  PacketsReceivedRate  |  Odebrane pakiety  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/networkInterfaces  |  PacketsSentRate  |  Wysłane pakiety  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/networkWatchers/connectionMonitors  |  AverageRoundtripMs  |  Średni czas błądzenia (MS)  |  MilliSeconds  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/networkWatchers/connectionMonitors  |  ChecksFailedPercent  |  Sprawdza procent niepowodzenia (wersja zapoznawcza)  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/networkWatchers/connectionMonitors  |  ProbesFailedPercent  |  % Sond nie powiodło się  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/networkWatchers/connectionMonitors  |  RoundTripTimeMs  |  Czas błądzenia (MS) (wersja zapoznawcza)  |  MilliSeconds  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  ByteCount  |  Liczba bajtów  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  BytesDroppedDDoS  |  Bajty przychodzące opuszczone DDoS  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  BytesForwardedDDoS  |  Przekazane bajty przychodzące DDoS  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  BytesInDDoS  |  Bajty przychodzące DDoS  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerSYNPackets  |  Pakiety przychodzących SYN wyzwalające łagodzenie DDoS  |  CountPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerTCPPackets  |  Przychodzące pakiety TCP do wyzwalania łagodzenia DDoS  |  CountPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerUDPPackets  |  Przychodzące pakiety UDP do wyzwalania łagodzenia DDoS  |  CountPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  IfUnderDDoSAttack  |  W obszarze atak DDoS  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  PacketCount  |  Liczba pakietów  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  PacketsDroppedDDoS  |  Odrzucone pakiety przychodzące DDoS  |  CountPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  PacketsForwardedDDoS  |  Przekazane pakiety przychodzące DDoS  |  CountPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  PacketsInDDoS  |  Pakiety przychodzące DDoS  |  CountPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  SynCount  |  Liczba SYN  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  TCPBytesDroppedDDoS  |  Liczba porzuconych bajtów przychodzących protokołu TCP DDoS  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  TCPBytesForwardedDDoS  |  Przekazane DDoS przychodzące bajty TCP  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  TCPBytesInDDoS  |  Przychodzące bajty TCP DDoS  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  TCPPacketsDroppedDDoS  |  Liczba porzuconych pakietów TCP przychodzących DDoS  |  CountPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  TCPPacketsForwardedDDoS  |  Przychodzące pakiety TCP przesłane dalej DDoS  |  CountPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  TCPPacketsInDDoS  |  Przychodzące pakiety TCP DDoS  |  CountPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  UDPBytesDroppedDDoS  |  Liczba porzuconych bajtów przychodzących UDP DDoS  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  UDPBytesForwardedDDoS  |  Przekazane przychodzące bajty UDP DDoS  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  UDPBytesInDDoS  |  Przychodzące bajty UDP DDoS  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  UDPPacketsDroppedDDoS  |  Liczba porzuconych pakietów przychodzących UDP DDoS  |  CountPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  UDPPacketsForwardedDDoS  |  Przychodzące pakiety UDP DDoS przesłane dalej  |  CountPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  UDPPacketsInDDoS  |  Przychodzące pakiety UDP DDoS  |  CountPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  VipAvailability  |  Dostępność ścieżki danych  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/trafficManagerProfiles  |  ProbeAgentCurrentEndpointStateByProfileResourceId  |  Stan punktu końcowego według punktu końcowego  |  Licznik  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.Network/trafficManagerProfiles  |  QpsByEndpoint  |  Zwrócone zapytania według punktu końcowego  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/virtualNetworkGateways  |  AverageBandwidth  |  Przepustowość S2S bramy  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/virtualNetworkGateways  |  P2SBandwidth  |  Przepustowość P2S bramy  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/virtualNetworkGateways  |  P2SConnectionCount  |  Liczba połączeń P2S  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/virtualNetworkGateways  |  TunnelAverageBandwidth  |  Przepustowość tunelu  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressBytes  |  Bajty wychodzące tunelu  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPacketDropTSMismatch  |  Porzucanie pakietów wychodzących przez tunelowanie  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPackets  |  Pakiety wychodzące tuneli  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressBytes  |  Bajty transferu danych wejściowych tunelu  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPacketDropTSMismatch  |  Wyrzucanie niezgodności pakietów przez tunelowanie  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPackets  |  Pakiety przychodzące tunelu  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/virtualNetworks  |  PingMeshAverageRoundtripMs  |  Czas błądzenia dla poleceń ping dla maszyny wirtualnej  |  MilliSeconds  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/virtualNetworks  |  PingMeshProbesFailedPercent  |  Nieudane polecenia ping do maszyny wirtualnej  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  przychodzące  |  Komunikaty przychodzące  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  przychodzące. ALL. failedrequests  |  Wszystkie przychodzące żądania zakończone niepowodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.all.requests  |  Wszystkie żądania przychodzące  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.scheduled  |  Przesłane zaplanowane powiadomienia wypychane  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.scheduled.cancel  |  Anulowano zaplanowane powiadomienia wypychane  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  Instalacja. wszystkie  |  Operacje zarządzania instalacją  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.delete  |  Usuwanie operacji instalacji  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  Instalacja. Get  |  Pobierz operacje instalacji  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.patch  |  Operacje instalacji poprawek  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  Instalacja. upsert  |  Tworzenie lub aktualizowanie operacji instalacji  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  notificationhub.pushes  |  Wszystkie powiadomienia wychodzące  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.badorexpiredchannel  |  Błędy nieprawidłowych lub wygasłych kanałów  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.channelerror  |  Błędy kanałów  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  wychodzący. allpns. invalidpayload  |  Błędy ładunku  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.pnserror  |  Błędy zewnętrznego systemu powiadomień  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.success  |  Powiadomienia zakończone powodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.badchannel  |  Błąd nieprawidłowego kanału usługi APNS  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.expiredchannel  |  Błąd wygasłego kanału usługi APNS  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidcredentials  |  Błędy autoryzacji usługi APNS  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidnotificationsize  |  Błąd nieprawidłowego rozmiaru powiadomienia usługi APNS  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.pnserror  |  Błędy usługi APNS  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.success  |  Pomyślne powiadomienia usługi APNS  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.authenticationerror  |  Błędy uwierzytelniania GCM  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.badchannel  |  Błąd nieprawidłowego kanału GCM  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.expiredchannel  |  Błąd GCM kanału wygasłego  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidcredentials  |  GCM błędy autoryzacji (Nieprawidłowe poświadczenia)  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidnotificationformat  |  Nieprawidłowy format powiadomienia GCM  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidnotificationsize  |  Błąd nieprawidłowego rozmiaru powiadomienia GCM  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.pnserror  |  Błędy GCM  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.success  |  GCM pomyślne powiadomienia  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.throttled  |  GCM powiadomienia z ograniczeniami  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.wrongchannel  |  Błąd nieprawidłowego kanału GCM  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.authenticationerror  |  Błędy uwierzytelniania usługi MPNS  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.badchannel  |  Błąd nieprawidłowego kanału usługi MPNS  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.channeldisconnected  |  Kanał usługi MPNS został odłączony  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.dropped  |  USŁUGI MPNS usunięte powiadomienia  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidcredentials  |  USŁUGI MPNS nieprawidłowe poświadczenia  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidnotificationformat  |  Nieprawidłowy format powiadomienia usługi MPNS  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.pnserror  |  Błędy usługi MPNS  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.success  |  USŁUGI MPNS pomyślne powiadomienia  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  wychodzące. usługi MPNS. dławienia  |  USŁUGI MPNS powiadomienia z ograniczeniami  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.authenticationerror  |  Błędy uwierzytelniania WNS  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.badchannel  |  Błąd nieprawidłowego kanału WNS  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channeldisconnected  |  Kanał WNS został odłączony  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channelthrottled  |  Ograniczenie kanału WNS  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.dropped  |  WNS usunięte powiadomienia  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.expiredchannel  |  Błąd WNS kanału wygasłego  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  wychodzący. WNS. invalidcredentials  |  WNS błędy autoryzacji (Nieprawidłowe poświadczenia)  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationformat  |  Nieprawidłowy format powiadomienia WNS  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationsize  |  Błąd nieprawidłowego rozmiaru powiadomienia WNS  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidtoken  |  Błędy autoryzacji WNS (nieprawidłowy token)  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.pnserror  |  Błędy WNS  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.success  |  WNS pomyślne powiadomienia  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  wychodzące. WNS. dławienia  |  WNS powiadomienia z ograniczeniami  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.tokenproviderunreachable  |  Błędy autoryzacji WNS (nieosiągalne)  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.wrongtoken  |  WNS błędy autoryzacji (nieprawidłowy token)  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  Rejestracja. wszystkie  |  Operacje rejestracji  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.create  |  Operacje tworzenia rejestracji  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.delete  |  Operacje usuwania rejestracji  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.get  |  Operacje odczytu rejestracji  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.update  |  Operacje aktualizacji rejestracji  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  scheduled.pending  |  Oczekujące zaplanowane powiadomienia  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Dostępna pamięć Average_%  |  Dostępna pamięć (%)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Dostępny obszar wymiany Average_%  |  Dostępny obszar wymiany (%)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_% zadeklarowanych bajtów w użyciu  |  % Zadeklarowanych bajtów w użyciu  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Czas DPC Average_%  |  Czas DPC (%)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_% bezpłatne węzłów i  |  % Wolnego węzłów i  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Wolne miejsce w Average_%  |  Wartość procentowa wolnego miejsca  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Wolne miejsce w Average_%  |  Wartość procentowa wolnego miejsca  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Czas bezczynności Average_%  |  Czas bezczynności (%)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_ czas przerwań (%)  |  Czas przerwań (%)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Czas oczekiwania operacji we/wy Average_%  |  Czas oczekiwania operacji we/wy (%)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_% czasu całkiem  |  % Całkiem czasu  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_% czasu uprzywilejowanego  |  Czas uprzywilejowany (%)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_ czas procesora (%)  |  Czas procesora (%)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_ czas procesora (%)  |  Czas procesora (%)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_ użyto węzłów i  |  % Użytych węzłów i  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Użyta pamięć Average_%  |  Używana pamięć (%)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Używane miejsce Average_%  |  Procent wykorzystania miejsca  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Używany obszar wymiany Average_%  |  Używany obszar wymiany (%)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Czas użytkownika Average_%  |  Czas użytkownika (%)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_Available MB  |  Dostępna pamięć (MB)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Pamięć Average_Available MB  |  Dostępna pamięć (MB)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Zamiana Average_Available MB  |  Dostępny obszar wymiany (MB)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. wartość operacji dysku na sek./odczyt  |  Średni czas dysku w s/odczyt  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. wartość operacji dysku na sek./odczyt  |  Średni czas dysku w s/odczyt  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. szybkość transferu z dysku w sekundach  |  Średni czas dysku w s/transfer  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. dysku na sek./zapis  |  Średni czas dysku w s/zapis  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. dysku na sek./zapis  |  Średni czas dysku w s/zapis  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Odebrane Average_Bytes/s  |  Bajty odebrane/s  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Wysłane Average_Bytes/s  |  Bajty wysłane/s  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes całkowita/s  |  Całkowita liczba bajtów/s  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_Current długość kolejki dysku  |  Bieżąca długość kolejki dysku  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Bajty odczytu Average_Disk/s  |  Bajty odczytu dysku/s  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Odczyty Average_Disk/s  |  Odczyty dysku/s  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Odczyty Average_Disk/s  |  Odczyty dysku/s  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Transfery Average_Disk/s  |  Transfery dyskowe/s  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Transfery Average_Disk/s  |  Transfery dyskowe/s  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Bajty zapisu Average_Disk/s  |  Bajty zapisu dysku/s  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Zapisy Average_Disk/s  |  Zapisy dysku/s  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Zapisy Average_Disk/s  |  Zapisy dysku/s  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_Free megabajtów  |  Wolne megabajty  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_Free megabajtów  |  Wolne megabajty  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_Free pamięci fizycznej  |  Wolna pamięć fizyczna  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_Free miejsce w plikach stronicowania  |  Wolne miejsce w plikach stronicowania  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_Free pamięć wirtualną  |  Wolna pamięć wirtualna  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Bajty dysku Average_Logical/s  |  Bajty dysku logicznego/s  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Odczyty Average_Page/s  |  Odczyty stron/s  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Zapisy Average_Page/s  |  Zapisy stron/s  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_Pages/s  |  Strony/s  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_Pct uprzywilejowany czas  |  Czas uprzywilejowany PCT  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Czas użytkownika Average_Pct  |  Czas użytkownika PCT  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Bajty dysku Average_Physical/s  |  Bajty dysku fizycznego/s  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_Processes  |  Procesy  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Długość kolejki Average_Processor  |  Długość kolejki procesora  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_Size przechowywane w plikach stronicowania  |  Rozmiar zapisany w plikach stronicowania  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_Total bajty  |  Łączna liczba bajtów  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Bajty odebrane Average_Total  |  Całkowita liczba odebranych bajtów  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Przesyłane Average_Total bajty  |  Całkowita liczba przesłanych bajtów  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_Total kolizje  |  Łączna liczba kolizji  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Odebrane pakiety Average_Total  |  Całkowita liczba odebranych pakietów  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_Total przesyłane pakiety  |  Całkowita liczba przesłanych pakietów  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_Total błędy odbierania  |  Całkowita liczba błędów odbierania  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Błędy wysyłania Average_Total  |  Całkowita liczba błędów transmisji  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_Uptime  |  Czas działania  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Przestrzeń wymiany Average_Used MB  |  Używany obszar wymiany (MB)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Pamięć Average_Used w kilobajtach  |  Używana pamięć (w kilobajtach)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Pamięć Average_Used pamięci (MB)  |  Używana pamięć (MB)  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_Users  |  Użytkownicy  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Average_Virtual pamięci współdzielonej  |  Wirtualna pamięć udostępniona  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Wydarzenie  |  Wydarzenie  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.OperationalInsights/workspaces  |  Puls  |  Puls  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/workspaces  |  Aktualizacja  |  Aktualizacja  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.PowerBIDedicated/capacities  |  memory_metric  |  Memory (Pamięć)  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.PowerBIDedicated/capacities  |  memory_thrashing_metric  |  Migotanie pamięci (zbiory danych)  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.PowerBIDedicated/capacities  |  qpu_high_utilization_metric  |  QPU wysokie wykorzystanie  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.PowerBIDedicated/capacities  |  QueryDuration  |  Czas trwania zapytania (zestawy danych)  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft.PowerBIDedicated/capacities  |  QueryPoolJobQueueLength  |  Długość kolejki zadań puli zapytań (zbiory danych)  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft. Relay/przestrzenie nazw  |  Połączeń ActiveConnections  |  Połączeń ActiveConnections  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft. Relay/przestrzenie nazw  |  ActiveListeners  |  ActiveListeners  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Relay/przestrzenie nazw  |  BytesTransferred  |  BytesTransferred  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft. Relay/przestrzenie nazw  |  ListenerConnections — błąd clienterror  |  ListenerConnections — błąd clienterror  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft. Relay/przestrzenie nazw  |  ListenerConnections-ServerError  |  ListenerConnections-ServerError  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft. Relay/przestrzenie nazw  |  ListenerConnections-Success  |  ListenerConnections-Success  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft. Relay/przestrzenie nazw  |  ListenerConnections-TotalRequests  |  ListenerConnections-TotalRequests  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft. Relay/przestrzenie nazw  |  ListenerDisconnects  |  ListenerDisconnects  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft. Relay/przestrzenie nazw  |  SenderConnections-ClientError  |  SenderConnections-ClientError  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft. Relay/przestrzenie nazw  |  SenderConnections-ServerError  |  SenderConnections-ServerError  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft. Relay/przestrzenie nazw  |  SenderConnections-Success  |  SenderConnections-Success  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft. Relay/przestrzenie nazw  |  SenderConnections-TotalRequests  |  SenderConnections-TotalRequests  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft. Relay/przestrzenie nazw  |  SenderDisconnects  |  SenderDisconnects  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Search/searchServices  |  SearchLatency  |  Opóźnienie wyszukiwania  |  S  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Search/searchServices  |  SearchQueriesPerSecond  |  Zapytania wyszukiwania na sekundę  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Search/searchServices  |  ThrottledSearchQueriesPercentage  |  Procent zapytań wyszukiwania z ograniczeniami  |  Procent  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceBus/namespaces  |  Połączeń ActiveConnections  |  Połączeń ActiveConnections  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.ServiceBus/namespaces  |  ActiveMessages  |  Liczba aktywnych komunikatów w kolejce/temacie.  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceBus/namespaces  |  Połączenia connectionsclosed  |  Połączenia zamknięte.  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceBus/namespaces  |  Połączenia connectionsopened  |  Otwarte połączenia.  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceBus/namespaces  |  CPUXNS  |  PROCESOR (przestarzałe)  |  Procent  |  Maksimum | 
| Nie  | Nie |  Microsoft.ServiceBus/namespaces  |  DeadletteredMessages  |  Liczba utraconych wiadomości w kolejce/temacie.  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.ServiceBus/namespaces  |  Komunikaty incomingmessages  |  Komunikaty przychodzące  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ServiceBus/namespaces  |  Żądania incomingrequests  |  Żądania przychodzące  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.ServiceBus/namespaces  |  Komunikaty  |  Liczba komunikatów w kolejce/temacie.  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceBus/namespaces  |  NamespaceCpuUsage  |  Procesor CPU  |  Procent  |  Maksimum | 
| Nie  | Nie |  Microsoft.ServiceBus/namespaces  |  NamespaceMemoryUsage  |  Użycie pamięci  |  Procent  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.ServiceBus/namespaces  |  Komunikaty outgoingmessages  |  Komunikaty wychodzące  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.ServiceBus/namespaces  |  ScheduledMessages  |  Liczba zaplanowanych komunikatów w kolejce/temacie.  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceBus/namespaces  |  Błędy servererrors  |  Błędy serwera.  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.ServiceBus/namespaces  |  Rozmiar  |  Rozmiar  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceBus/namespaces  |  Żądania successfulrequests  |  Żądania zakończone powodzeniem  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.ServiceBus/namespaces  |  ThrottledRequests  |  Żądania z ograniczeniami.  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.ServiceBus/namespaces  |  Błędy usererrors  |  Błędy użytkownika.  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.ServiceBus/namespaces  |  WSXNS  |  Użycie pamięci (przestarzałe)  |  Procent  |  Maksimum | 
| Nie  | Nie |  Microsoft.ServiceFabricMesh/applications  |  ActualCpu  |  ActualCpu  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceFabricMesh/applications  |  ActualMemory  |  ActualMemory  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceFabricMesh/applications  |  AllocatedCpu  |  AllocatedCpu  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceFabricMesh/applications  |  AllocatedMemory  |  AllocatedMemory  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceFabricMesh/applications  |  ApplicationStatus  |  ApplicationStatus  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceFabricMesh/applications  |  ContainerStatus  |  ContainerStatus  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceFabricMesh/applications  |  CpuUtilization  |  CpuUtilization  |  Procent  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceFabricMesh/applications  |  MemoryUtilization  |  MemoryUtilization  |  Procent  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceFabricMesh/applications  |  RestartCount  |  RestartCount  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceFabricMesh/applications  |  ServiceReplicaStatus  |  ServiceReplicaStatus  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceFabricMesh/applications  |  Bajty  |  Bajty  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.SignalRService/SignalR  |  ConnectionCount  |  Liczba połączeń  |  Licznik  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.SignalRService/SignalR  |  InboundTraffic  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.SignalRService/SignalR  |  MessageCount  |  Liczba komunikatów  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.SignalRService/SignalR  |  OutboundTraffic  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.SignalRService/SignalR  |  SystemErrors  |  Błędy systemu  |  Procent  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.SignalRService/SignalR  |  Błędy usererrors  |  Błędy użytkownika  |  Procent  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.Sql/managedInstances  |  avg_cpu_percent  |  Średni procent procesora CPU  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/managedInstances  |  io_bytes_read  |  Odczytane bajty we/wy  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/managedInstances  |  io_bytes_written  |  Bajty we/wy zapisywane  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/managedInstances  |  io_requests  |  Liczba żądań we/wy  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/managedInstances  |  reserved_storage_mb  |  Zarezerwowane miejsce w magazynie  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/managedInstances  |  storage_space_used_mb  |  Używane miejsce do magazynowania  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/managedInstances  |  virtual_core_count  |  Liczba rdzeni wirtualnych  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/servers  |  database_dtu_consumption_percent  |  Procent użycia jednostek DTU  |  Procent  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/servers  |  database_storage_used  |  Używane miejsce na dane  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers  |  dtu_consumption_percent  |  Procent użycia jednostek DTU  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers  |  dtu_used  |  Używane jednostki DTU  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/servers  |  storage_used  |  Używane miejsce na dane  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/servers/databases  |  allocated_data_storage  |  Przydzielono miejsce na danych  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/servers/databases  |  app_cpu_billed  |  Rozliczane użycie procesora przez aplikację  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Sql/servers/databases  |  app_cpu_percent  |  Procent użycia procesora CPU aplikacji  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/servers/databases  |  app_memory_percent  |  Procent pamięci aplikacji  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/databases  |  blocked_by_firewall  |  Zablokowane przez zaporę  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/databases  |  cache_hit_percent  |  Procent trafień w pamięci podręcznej  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Sql/servers/databases  |  cache_used_percent  |  Procent użycia pamięci podręcznej  |  Procent  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/databases  |  connection_failed  |  Połączenia zakończone niepowodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/databases  |  connection_successful  |  Udane połączenia  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Sql/servers/databases  |  cpu_limit  |  Limit CPU  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/databases  |  cpu_percent  |  Procent użycia procesora CPU  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/databases  |  cpu_used  |  Użycie procesora CPU  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/databases  |  zakleszczenie  |  Zakleszczenia  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/databases  |  dtu_consumption_percent  |  Procent użycia jednostek DTU  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/servers/databases  |  dtu_limit  |  DTU Limit  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/databases  |  dtu_used  |  Używane jednostki DTU  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/databases  |  dwu_consumption_percent  |  JEDNOSTEK dwu procent  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Sql/servers/databases  |  dwu_limit  |  Limit jednostek dwu  |  Licznik  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/databases  |  dwu_used  |  JEDNOSTEK dwu używane  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Sql/servers/databases  |  local_tempdb_usage_percent  |  Procent lokalnej bazy danych tempdb  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/databases  |  log_write_percent  |  Procent operacji we/wy dziennika  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/servers/databases  |  memory_usage_percent  |  Procent pamięci  |  Procent  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/databases  |  physical_data_read_percent  |  Procent użycia operacji we/wy na danych  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/databases  |  sessions_percent  |  Procent sesji  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/servers/databases  |  Chowan  |  Używane miejsce na dane  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Sql/servers/databases  |  storage_percent  |  Procent użytego miejsca na danych  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Sql/servers/databases  |  tempdb_data_size  |  Rozmiar pliku danych tempdb kilobajtów  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Sql/servers/databases  |  tempdb_log_size  |  Rozmiar pliku dziennika bazy danych tempdb kilobajtów  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Sql/servers/databases  |  tempdb_log_used_percent  |  Użyto dziennika% tempdb  |  Procent  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/databases  |  workers_percent  |  Procent pracowników  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/databases  |  xtp_storage_percent  |  Procent magazynu OLTP w pamięci  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage  |  Przydzielono miejsce na danych  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage_percent  |  Procent przydzielonych przestrzeni danych  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Sql/servers/elasticPools  |  cpu_limit  |  Limit CPU  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/elasticPools  |  cpu_percent  |  Procent użycia procesora CPU  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/servers/elasticPools  |  cpu_used  |  Użycie procesora CPU  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/servers/elasticPools  |  database_allocated_data_storage  |  Przydzielono miejsce na danych  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/servers/elasticPools  |  database_cpu_limit  |  Limit CPU  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/servers/elasticPools  |  database_cpu_percent  |  Procent użycia procesora CPU  |  Procent  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/servers/elasticPools  |  database_cpu_used  |  Użycie procesora CPU  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/servers/elasticPools  |  database_dtu_consumption_percent  |  Procent użycia jednostek DTU  |  Procent  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/servers/elasticPools  |  database_eDTU_used  |  użyta wartość eDTU  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/servers/elasticPools  |  database_log_write_percent  |  Procent operacji we/wy dziennika  |  Procent  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/servers/elasticPools  |  database_physical_data_read_percent  |  Procent użycia operacji we/wy na danych  |  Procent  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/servers/elasticPools  |  database_sessions_percent  |  Procent sesji  |  Procent  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/servers/elasticPools  |  database_storage_used  |  Używane miejsce na dane  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/servers/elasticPools  |  database_workers_percent  |  Procent pracowników  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/elasticPools  |  dtu_consumption_percent  |  Procent użycia jednostek DTU  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/servers/elasticPools  |  eDTU_limit  |  limit liczby jednostek eDTU  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/elasticPools  |  eDTU_used  |  użyta wartość eDTU  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/elasticPools  |  log_write_percent  |  Procent operacji we/wy dziennika  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/elasticPools  |  physical_data_read_percent  |  Procent użycia operacji we/wy na danych  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/elasticPools  |  sessions_percent  |  Procent sesji  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/servers/elasticPools  |  storage_limit  |  Maksymalny rozmiar danych  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/servers/elasticPools  |  storage_percent  |  Procent użytego miejsca na danych  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/servers/elasticPools  |  storage_used  |  Używane miejsce na dane  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/servers/elasticPools  |  tempdb_data_size  |  Rozmiar pliku danych tempdb kilobajtów  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_size  |  Rozmiar pliku dziennika bazy danych tempdb kilobajtów  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_used_percent  |  Użyto dziennika% tempdb  |  Procent  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/elasticPools  |  workers_percent  |  Procent pracowników  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/elasticPools  |  xtp_storage_percent  |  Procent magazynu OLTP w pamięci  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts  |  Dostępność  |  Dostępność  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts  |  SuccessE2ELatency  |  Opóźnienie e2e dla powodzenia  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts  |  Transakcje  |  Transakcje  |  Licznik  |  Łącznie | 
| Nie  | Nie |  Microsoft.Storage/storageAccounts  |  UsedCapacity  |  Używana pojemność  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/blobServices  |  Dostępność  |  Dostępność  |  Procent  |  Średnia | 
| Nie  | Nie |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCapacity  |  Pojemność obiektu blob  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCount  |  Liczba obiektów blob  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/blobServices  |  ContainerCount  |  Liczba kontenerów obiektów blob  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/blobServices  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft.Storage/storageAccounts/blobServices  |  IndexCapacity  |  Pojemność indeksu  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/blobServices  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Opóźnienie e2e dla powodzenia  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/blobServices  |  Transakcje  |  Transakcje  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/fileServices  |  Dostępność  |  Dostępność  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/fileServices  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft.Storage/storageAccounts/fileServices  |  FileCapacity  |  Pojemność plików  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft.Storage/storageAccounts/fileServices  |  FileCount  |  Liczba plików  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareCount  |  Liczba udziałów plików  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareQuota  |  Rozmiar przydziału udziału plików  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  Liczba migawek udziału plików  |  Licznik  |  Średnia | 
| Nie  | Nie |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareSnapshotSize  |  Rozmiar migawki udziału plików  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/fileServices  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessE2ELatency  |  Opóźnienie e2e dla powodzenia  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/fileServices  |  Transakcje  |  Transakcje  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/queueServices  |  Dostępność  |  Dostępność  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/queueServices  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/queueServices  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCapacity  |  Pojemność kolejki  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCount  |  Liczba kolejek  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/queueServices  |  QueueMessageCount  |  Liczba komunikatów w kolejce  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Opóźnienie e2e dla powodzenia  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/queueServices  |  Transakcje  |  Transakcje  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/tableServices  |  Dostępność  |  Dostępność  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/tableServices  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/tableServices  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Opóźnienie e2e dla powodzenia  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/tableServices  |  TableCapacity  |  Pojemność tabeli  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/tableServices  |  TableCount  |  Liczba tabel  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/tableServices  |  TableEntityCount  |  Liczba jednostek tabel  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageAccounts/tableServices  |  Transakcje  |  Transakcje  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  ClientIOPS  |  Łączna liczba operacji we/wy klienta  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  ClientLatency  |  Średnie opóźnienie klienta  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  ClientLockIOPS  |  Liczba operacji we/wy blokady klienta  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  ClientMetadataReadIOPS  |  Liczba operacji we/wy odczytu metadanych klienta  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  ClientMetadataWriteIOPS  |  Liczba IOPS zapisu metadanych klienta  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  ClientReadIOPS  |  Liczba operacji we/wy odczytu klienta  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  ClientReadThroughput  |  Średnia przepływność odczytu pamięci podręcznej  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  ClientWriteIOPS  |  Liczba operacji we/wy zapisu klienta  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  ClientWriteThroughput  |  Średnia przepływność zapisu w pamięci podręcznej  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetAsyncWriteThroughput  |  Przepływność zapisu asynchronicznego StorageTarget  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetFillThroughput  |  Przepływność wypełnienia StorageTarget  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetHealth  |  Kondycja docelowa magazynu  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetIOPS  |  Łączna liczba operacji we/wy StorageTarget  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetLatency  |  Opóźnienie StorageTarget  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetMetadataReadIOPS  |  Liczba operacji we/wy odczytu metadanych StorageTarget  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetMetadataWriteIOPS  |  Liczba IOPS zapisu w metadanych StorageTarget  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetReadAheadThroughput  |  StorageTarget z wyprzedzeniem  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetReadIOPS  |  StorageTarget operacji we/wy odczytu  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetSyncWriteThroughput  |  Przepływność zapisu synchronicznego StorageTarget  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetTotalReadThroughput  |  StorageTarget całkowita przepływność odczytu  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetTotalWriteThroughput  |  StorageTarget całkowita przepływność zapisu  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetWriteIOPS  |  StorageTarget zapisu IOPS  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  Czas działania  |  Czas działania  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices  |  ServerSyncSessionResult  |  Wynik synchronizacji sesji  |  Licznik  |  Średnia | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices  |  StorageSyncBatchTransferredFileBytes  |  Zsynchronizowane bajty  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledNetworkBytesByApplication  |  Rozmiar odwołania do warstw w chmurze według aplikacji  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledTotalNetworkBytes  |  Rozmiar odwołania do warstw w chmurze  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecallIOTotalSizeBytes  |  Odwołanie do warstw w chmurze  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecallThroughputBytesPerSecond  |  Przepływność odwołań do warstw w chmurze  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices  |  StorageSyncServerHeartbeat  |  Stan online serwera  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices  |  StorageSyncSyncSessionAppliedFilesCount  |  Synchronizowane pliki  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices  |  StorageSyncSyncSessionPerItemErrorsCount  |  Pliki, których nie należy synchronizować  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. storagesync/storageSyncServices/registeredServers  |  ServerHeartbeat  |  Stan online serwera  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. storagesync/storageSyncServices/registeredServers  |  ServerRecallIOTotalSizeBytes  |  Odwołanie do warstw w chmurze  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. storagesync/storageSyncServices/syncGroups  |  SyncGroupBatchTransferredFileBytes  |  Zsynchronizowane bajty  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionAppliedFilesCount  |  Synchronizowane pliki  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionPerItemErrorsCount  |  Pliki, których nie należy synchronizować  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointBatchTransferredFileBytes  |  Zsynchronizowane bajty  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionAppliedFilesCount  |  Synchronizowane pliki  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionPerItemErrorsCount  |  Pliki, których nie należy synchronizować  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutFailedRequests  |  Żądania funkcji zakończone niepowodzeniem  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutInputEvents  |  Zdarzenia funkcji  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutRequests  |  Żądania funkcji  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalytics/streamingjobs  |  ConversionErrors  |  Błędy konwersji danych  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalytics/streamingjobs  |  DeserializationError  |  Błędy deserializacji danych wejściowych  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalytics/streamingjobs  |  DroppedOrAdjustedEvents  |  Zdarzenia poza kolejnością  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalytics/streamingjobs  |  EarlyInputEvents  |  Wczesne zdarzenia wejściowe  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalytics/streamingjobs  |  Błędy  |  Błędy w czasie wykonywania  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventBytes  |  Zdarzenia wejściowe (bajty)  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalytics/streamingjobs  |  InputEvents  |  Zdarzenia wejściowe  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesBacklogged  |  Zaległe zdarzenia wejściowe  |  Licznik  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesPerSecond  |  Odebrane źródła wejściowe  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalytics/streamingjobs  |  LateInputEvents  |  Opóźnione zdarzenia wejściowe  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalytics/streamingjobs  |  OutputEvents  |  Zdarzenia wyjściowe  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalytics/streamingjobs  |  OutputWatermarkDelaySeconds  |  Opóźnienie znaku wodnego  |  S  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.StreamAnalytics/streamingjobs  |  ResourceUtilization  |  % wykorzystania SU  |  Procent  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  Bajty odczytu dysku  |  Bajty odczytu dysku  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  Operacje odczytu z dysku/s  |  Operacje odczytu z dysku/s  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  Bajty zapisu dysku  |  Bajty zapisu dysku  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  Operacje zapisu na dysku/s  |  Operacje zapisu na dysku/s  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  DiskReadBytesPerSecond  |  Bajty odczytu dysku/s  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  DiskReadLatency  |  Opóźnienie odczytu dysku  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  DiskReadOperations  |  Operacje odczytu z dysku  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  DiskWriteBytesPerSecond  |  Bajty zapisu dysku/s  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  DiskWriteLatency  |  Opóźnienie zapisu na dysku  |  Milisekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  DiskWriteOperations  |  Operacje zapisu na dysku  |  Licznik  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  MemoryActive  |  Pamięć aktywna  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  MemoryGranted  |  Przydzielone pamięci  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  MemoryUsed  |  Używana pamięć  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  Sieć — wejście  |  Sieć — wejście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  Sieć — wyjście  |  Sieć — wyjście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  NetworkInBytesPerSecond  |  Sieć w bajtach/s  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  NetworkOutBytesPerSecond  |  Bajty wychodzące z sieci/s  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  Procentowe użycie procesora CPU  |  Procentowe użycie procesora CPU  |  Procent  |  Średnia | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  PercentageCpuReady  |  Procent gotowych do użycia procesora  |  Milisekundy  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  ActiveRequests  |  Aktywne żądania  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  AverageResponseTime  |  Średni czas odpowiedzi  |  S  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BytesReceived  |  Dane w  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BytesSent  |  Dane wychodzące  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  CpuPercentage  |  Procent użycia procesora CPU  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  DiskQueueLength  |  Długość kolejki dysku  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http101  |  Http 101  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http2xx  |  Http 2xx  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http3xx  |  Http 3xx  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http401  |  HTTP 401  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http403  |  HTTP 403  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http404  |  HTTP 404  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http406  |  Http 406  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http4xx  |  Http 4xx  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http5xx  |  Błędy serwera http  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  HttpQueueLength  |  Długość kolejki http  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  LargeAppServicePlanInstances  |  Duże App Service planowanie procesów roboczych  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MediumAppServicePlanInstances  |  Średni App Service pracowników planu  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MemoryPercentage  |  Procent pamięci  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Żądania  |  Żądania  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  SmallAppServicePlanInstances  |  Niewielka App Service planowanie procesów roboczych  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  TotalFrontEnds  |  Łączne frontony  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/workerPools  |  CpuPercentage  |  Procent użycia procesora CPU  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/workerPools  |  MemoryPercentage  |  Procent pamięci  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/workerPools  |  WorkersAvailable  |  Dostępni pracownicy  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/workerPools  |  Łączna  |  Łączna liczba procesów roboczych  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/workerPools  |  WorkersUsed  |  Używani pracownicy  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/serverfarms  |  BytesReceived  |  Dane w  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/serverfarms  |  BytesSent  |  Dane wychodzące  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/serverfarms  |  CpuPercentage  |  Procent użycia procesora CPU  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/serverfarms  |  DiskQueueLength  |  Długość kolejki dysku  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/serverfarms  |  HttpQueueLength  |  Długość kolejki http  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/serverfarms  |  MemoryPercentage  |  Procent pamięci  |  Procent  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/serverfarms  |  TcpCloseWait  |  Oczekiwanie na zamknięcie protokołu TCP  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/serverfarms  |  TcpClosing  |  Zamykanie protokołu TCP  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/serverfarms  |  TcpEstablished  |  Nawiązano ruch TCP  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/serverfarms  |  TcpFinWait1  |  Oczekiwanie na TCP FIN 1  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/serverfarms  |  TcpFinWait2  |  Oczekiwanie na TCP fin 2  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/serverfarms  |  TcpLastAck  |  Ostatnie potwierdzenie TCP  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/serverfarms  |  TcpSynReceived  |  Odebrano pakiet TCP syn  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/serverfarms  |  TcpSynSent  |  Wysłano pakiet TCP syn  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/serverfarms  |  TcpTimeWait  |  Czas oczekiwania TCP  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  AppConnections  |  Połączenia  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  AverageMemoryWorkingSet  |  Średni zestaw roboczy pamięci  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  AverageResponseTime  |  Średni czas odpowiedzi  |  S  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  BytesReceived  |  Dane w  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  BytesSent  |  Dane wychodzące  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  CpuTime  |  Czas procesora CPU  |  S  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  CurrentAssemblies  |  Bieżące zestawy  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  FunctionExecutionCount  |  Liczba wykonań funkcji  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  FunctionExecutionUnits  |  Jednostki wykonywania funkcji  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  Gen0Collections  |  Zbieranie elementów bezużytecznych generacji 0  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  Gen1Collections  |  Wyrzucanie elementów bezużytecznych generacji 1  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  Gen2Collections  |  Zbieranie elementów bezużytecznych generacji 2  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  Realizuj  |  Liczba dojść  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  HealthCheckStatus  |  Stan sprawdzania kondycji  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  Http101  |  Http 101  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  Http2xx  |  Http 2xx  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  Http3xx  |  Http 3xx  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  Http401  |  HTTP 401  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  Http403  |  HTTP 403  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  Http404  |  HTTP 404  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  Http406  |  Http 406  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  Http4xx  |  Http 4xx  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  Http5xx  |  Błędy serwera http  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  HttpResponseTime  |  Czas odpowiedzi  |  S  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  IoOtherBytesPerSecond  |  Inne bajty we/wy na sekundę  |  BytesPerSecond  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  IoOtherOperationsPerSecond  |  Inne operacje we/wy na sekundę  |  BytesPerSecond  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  IoReadBytesPerSecond  |  Bajty odczytu we/wy na sekundę  |  BytesPerSecond  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  IoReadOperationsPerSecond  |  Operacje odczytu we/wy na sekundę  |  BytesPerSecond  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  IoWriteBytesPerSecond  |  Bajty zapisu we/wy na sekundę  |  BytesPerSecond  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  IoWriteOperationsPerSecond  |  Operacje zapisu we/wy na sekundę  |  BytesPerSecond  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  MemoryWorkingSet  |  Zestaw roboczy pamięci  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  PrivateBytes  |  Prywatne bajty  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  Żądania  |  Żądania  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  RequestsInApplicationQueue  |  Żądania w kolejce aplikacji  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  Wątki  |  Liczba wątków  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  TotalAppDomains  |  Łączna liczba domen aplikacji  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites  |  TotalAppDomainsUnloaded  |  Całkowita liczba zwolnionych domen aplikacji  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  AppConnections  |  Połączenia  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  AverageMemoryWorkingSet  |  Średni zestaw roboczy pamięci  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  AverageResponseTime  |  Średni czas odpowiedzi  |  S  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  BytesReceived  |  Dane w  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  BytesSent  |  Dane wychodzące  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  CpuTime  |  Czas procesora CPU  |  S  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  CurrentAssemblies  |  Bieżące zestawy  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  FunctionExecutionCount  |  Liczba wykonań funkcji  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  FunctionExecutionUnits  |  Jednostki wykonywania funkcji  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Gen0Collections  |  Zbieranie elementów bezużytecznych generacji 0  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Gen1Collections  |  Wyrzucanie elementów bezużytecznych generacji 1  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Gen2Collections  |  Zbieranie elementów bezużytecznych generacji 2  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Realizuj  |  Liczba dojść  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  HealthCheckStatus  |  Stan sprawdzania kondycji  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Http101  |  Http 101  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Http2xx  |  Http 2xx  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Http3xx  |  Http 3xx  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Http401  |  HTTP 401  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Http403  |  HTTP 403  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Http404  |  HTTP 404  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Http406  |  Http 406  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Http4xx  |  Http 4xx  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Http5xx  |  Błędy serwera http  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  HttpResponseTime  |  Czas odpowiedzi  |  S  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  IoOtherBytesPerSecond  |  Inne bajty we/wy na sekundę  |  BytesPerSecond  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  IoOtherOperationsPerSecond  |  Inne operacje we/wy na sekundę  |  BytesPerSecond  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  IoReadBytesPerSecond  |  Bajty odczytu we/wy na sekundę  |  BytesPerSecond  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  IoReadOperationsPerSecond  |  Operacje odczytu we/wy na sekundę  |  BytesPerSecond  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  IoWriteBytesPerSecond  |  Bajty zapisu we/wy na sekundę  |  BytesPerSecond  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  IoWriteOperationsPerSecond  |  Operacje zapisu we/wy na sekundę  |  BytesPerSecond  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  MemoryWorkingSet  |  Zestaw roboczy pamięci  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  PrivateBytes  |  Prywatne bajty  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Żądania  |  Żądania  |  Licznik  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  RequestsInApplicationQueue  |  Żądania w kolejce aplikacji  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Wątki  |  Liczba wątków  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  TotalAppDomains  |  Łączna liczba domen aplikacji  |  Licznik  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  TotalAppDomainsUnloaded  |  Całkowita liczba zwolnionych domen aplikacji  |  Licznik  |  Średnia | 
