---
title: Metryki platformy Azure Monitor eksportowane za pośrednictwem ustawień diagnostycznych
description: Lista metryk dostępnych dla każdego typu zasobu z Azure Monitor.
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
author: rboucher
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: dcf5276393400be864e738d89bc5713f5aac242b
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963482"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Metryki platformy Azure Monitor eksportowane za pośrednictwem ustawień diagnostycznych

Azure Monitor domyślnie udostępnia [metryki platformy](data-platform-metrics.md) bez konfiguracji. Zapewnia kilka sposobów współpracy z metrykami platformy, w tym wykresów w portalu, uzyskiwania dostępu do nich za pośrednictwem interfejsu API REST lub wykonywania zapytań przy użyciu programu PowerShell lub interfejsu wiersza polecenia. Zobacz [metryki — obsługa](metrics-supported.md) pełnej listy metryk platformy dostępnych obecnie w ramach skonsolidowanego potoku metryki Azure monitor. Aby wykonać zapytanie o i uzyskać dostęp do tych metryk, użyj [wersji interfejsu api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Inne metryki mogą być dostępne w portalu lub przy użyciu starszych interfejsów API.

Metryki platformy z potoku usługi Azure monitor można wyeksportować do innych lokalizacji na jeden z dwóch sposobów.
1. Wysyłanie do Log Analytics, Event Hubs lub Azure Storage przy użyciu [ustawień diagnostycznych](diagnostic-settings.md) .
2. Korzystanie z [interfejsu API REST metryk](https://docs.microsoft.com/rest/api/monitor/metrics/list)

Ze względu na złożonego w zapleczu Azure Monitor nie wszystkie metryki są eksportowane przy użyciu ustawień diagnostycznych. W poniższej tabeli wymieniono i nie można eksportować za pomocą ustawień diagnostycznych.

Tabela zawiera następujące kolumny. 
- Eksportować za pomocą ustawień diagnostycznych? 
- ResourceType 
- Metryka 
- MetricDisplayName
- Jednostka 
- Agregacja


> [!NOTE]
> Poniższa tabela może mieć poziomy pasek przewijania u dołu. Jeśli uważasz, że brakuje informacji, sprawdź, czy pasek przewijania jest widoczny w lewo.  


Eksportować za pomocą ustawień diagnostycznych? | ResourceType | Metryka | MetricDisplayName | Jednostka | Agregacja
|----|-----|------|----|----|-----|
Tak | Microsoft.AnalysisServices/servers | CleanerCurrentPrice | Pamięć: bieżąca cena oczyszczarki | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | CleanerMemoryNonshrinkable | Pamięć: pamięć oczyszczarki niemożliwa do zmniejszenia | Bajty | Średnia
Tak | Microsoft.AnalysisServices/servers | CleanerMemoryShrinkable | Pamięć: zmniejszanie ilości pamięci czyszczącej | Bajty | Średnia
Tak | Microsoft.AnalysisServices/servers | CommandPoolBusyThreads | Wątki: zajęte wątki w puli poleceń | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | CommandPoolIdleThreads | Wątki: bezczynne wątki w puli poleceń | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | CommandPoolJobQueueLength | Długość kolejki zadań puli poleceń | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | Wartości CurrentConnections | Połączenie: bieżące połączenia | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | CurrentUserSessions | Bieżące sesje użytkowników | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | LongParsingBusyThreads | Wątki: zajęte wątki o długotrwałej analizie | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | LongParsingIdleThreads | Wątki: długotrwałe wątki bezczynne analizy | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | LongParsingJobQueueLength | Wątki: Długość kolejki zadań o długotrwałej analizie | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | mashup_engine_memory_metric | Pamięć aparatu M | Bajty | Średnia
Tak | Microsoft.AnalysisServices/servers | mashup_engine_private_bytes_metric | Liczba prywatnych bajtów aparatu M | Bajty | Średnia
Tak | Microsoft.AnalysisServices/servers | mashup_engine_qpu_metric | M QPU aparatu | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | mashup_engine_virtual_bytes_metric | Liczba bajtów wirtualnych aparatu M | Bajty | Średnia
Tak | Microsoft.AnalysisServices/servers | memory_metric | Pamięć | Bajty | Średnia
Tak | Microsoft.AnalysisServices/servers | memory_thrashing_metric | Migotanie pamięci | Procent | Średnia
Tak | Microsoft.AnalysisServices/servers | MemoryLimitHard | Pamięć: sztywny limit pamięci | Bajty | Średnia
Tak | Microsoft.AnalysisServices/servers | MemoryLimitHigh | Pamięć: limit pamięci jest wysoki | Bajty | Średnia
Tak | Microsoft.AnalysisServices/servers | MemoryLimitLow | Pamięć: limit pamięci — niski | Bajty | Średnia
Tak | Microsoft.AnalysisServices/servers | MemoryLimitVertiPaq | Pamięć: limit pamięci — tryb VertiPaq | Bajty | Średnia
Tak | Microsoft.AnalysisServices/servers | MemoryUsage | Pamięć: użycie pamięci | Bajty | Średnia
Tak | Microsoft.AnalysisServices/servers | private_bytes_metric | Bajty prywatne | Bajty | Średnia
Tak | Microsoft.AnalysisServices/servers | ProcessingPoolBusyIOJobThreads | Wątki: wątki zadań we/wy puli przetwarzania | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | ProcessingPoolBusyNonIOThreads | Wątki: zajęte wątki innych niż we/wy puli przetwarzania | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | ProcessingPoolIdleIOJobThreads | Wątki: wątki zadań we/wy puli przetwarzania | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | ProcessingPoolIdleNonIOThreads | Wątki: bezczynne wątki inne niż we/wy puli przetwarzania | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | ProcessingPoolIOJobQueueLength | Wątki: Długość kolejki zadań we/wy puli przetwarzania | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | ProcessingPoolJobQueueLength | Długość kolejki zadań puli przetwarzania | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | qpu_metric | QPU | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | QueryPoolBusyThreads | Wątki zajęte w puli zapytań | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | QueryPoolIdleThreads | Wątki: bezczynne wątki puli zapytań | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | QueryPoolJobQueueLength | Wątki: Długość kolejki zadań puli zapytań | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | Przydział | Pamięć: limit przydziału | Bajty | Średnia
Tak | Microsoft.AnalysisServices/servers | QuotaBlocked | Pamięć: zablokowany limit przydziału | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | RowsConvertedPerSec | Przetwarzanie: przekonwertowane wiersze na sekundę | CountPerSecond | Średnia
Tak | Microsoft.AnalysisServices/servers | RowsReadPerSec | Przetwarzanie: odczytane wiersze na sekundę | CountPerSecond | Średnia
Tak | Microsoft.AnalysisServices/servers | RowsWrittenPerSec | Przetwarzanie: liczba wierszy na sekundę | CountPerSecond | Średnia
Tak | Microsoft.AnalysisServices/servers | ShortParsingBusyThreads | Wątki: zajęte wątki z krótkim analizowaniem | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | ShortParsingIdleThreads | Wątki: bezczynne wątki z krótkim analizowaniem | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | ShortParsingJobQueueLength | Wątki: Długość kolejki zadań o krótkiej analizie | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | SuccessfullConnectionsPerSec | Udane połączenia na sekundę | CountPerSecond | Średnia
Tak | Microsoft.AnalysisServices/servers | TotalConnectionFailures | Łączna liczba błędów połączenia | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | TotalConnectionRequests | Łączna liczba żądań połączenia | Liczba | Średnia
Tak | Microsoft.AnalysisServices/servers | VertiPaqNonpaged | Pamięć: VertiPaq niestronicowana | Bajty | Średnia
Tak | Microsoft.AnalysisServices/servers | VertiPaqPaged | Pamięć: stronicowana na stronie VertiPaq | Bajty | Średnia
Tak | Microsoft.AnalysisServices/servers | virtual_bytes_metric | Bajty wirtualne | Bajty | Średnia
Tak | Microsoft.ApiManagement/service | BackendDuration | Czas trwania żądań wewnętrznej bazy danych | MS | Średnia
Tak | Microsoft.ApiManagement/service | Pojemność | Pojemność | Procent | Średnia
Tak | Microsoft.ApiManagement/service | Czas trwania | Całkowity czas trwania żądań bramy | MS | Średnia
Tak | Microsoft.ApiManagement/service | EventHubDroppedEvents | Opuszczone zdarzenia EventHub | Liczba | Łącznie
Tak | Microsoft.ApiManagement/service | EventHubRejectedEvents | Odrzucone zdarzenia EventHub | Liczba | Łącznie
Tak | Microsoft.ApiManagement/service | EventHubSuccessfulEvents | Pomyślne zdarzenia EventHub | Liczba | Łącznie
Tak | Microsoft.ApiManagement/service | EventHubThrottledEvents | Ograniczone zdarzenia EventHub | Liczba | Łącznie
Tak | Microsoft.ApiManagement/service | EventHubTimedoutEvents | Przekroczono limit czasu zdarzeń EventHub | Liczba | Łącznie
Tak | Microsoft.ApiManagement/service | EventHubTotalBytesSent | Rozmiar zdarzeń EventHub | Bajty | Łącznie
Tak | Microsoft.ApiManagement/service | EventHubTotalEvents | Łączna liczba zdarzeń EventHub | Liczba | Łącznie
Tak | Microsoft.ApiManagement/service | EventHubTotalFailedEvents | Niepowodzenie zdarzeń EventHub | Liczba | Łącznie
Tak | Microsoft.ApiManagement/service | FailedRequests | Nieudane żądania bramy (przestarzałe) | Liczba | Łącznie
Tak | Microsoft.ApiManagement/service | OtherRequests | Inne żądania bramy (przestarzałe) | Liczba | Łącznie
Tak | Microsoft.ApiManagement/service | Żądania | Żądania | Liczba | Łącznie
Tak | Microsoft.ApiManagement/service | Żądania successfulrequests | Pomyślne żądania bramy (przestarzałe) | Liczba | Łącznie
Tak | Microsoft.ApiManagement/service | TotalRequests | Łączna liczba żądań bramy (przestarzałe) | Liczba | Łącznie
Tak | Microsoft.ApiManagement/service | UnauthorizedRequests | Nieautoryzowane żądania bramy (przestarzałe) | Liczba | Łącznie
Tak | Microsoft. AppPlatform/Sprężyna | AppCpuUsagePercentage | Procent użycia procesora aplikacji | Procent | Średnia
Tak | Microsoft. AppPlatform/Sprężyna | AppMemoryCommitted | Przypisana pamięć aplikacji | Bajty | Średnia
Tak | Microsoft. AppPlatform/Sprężyna | AppMemoryMax | Maksymalna ilość pamięci aplikacji | Bajty | Maksimum
Tak | Microsoft. AppPlatform/Sprężyna | AppMemoryUsed | Użyta pamięć aplikacji | Bajty | Średnia
Tak | Microsoft. AppPlatform/Sprężyna | GCPauseTotalCount | Liczba wstrzymań GC | Liczba | Łącznie
Tak | Microsoft. AppPlatform/Sprężyna | GCPauseTotalTime | Całkowity czas wstrzymania odzyskiwania pamięci | MS | Łącznie
Tak | Microsoft. AppPlatform/Sprężyna | MaxOldGenMemoryPoolBytes | Maksymalna dostępna wartość starego rozmiaru danych generacji | Bajty | Średnia
Tak | Microsoft. AppPlatform/Sprężyna | OldGenMemoryPoolBytes | Stary rozmiar danych generacji | Bajty | Średnia
Tak | Microsoft. AppPlatform/Sprężyna | OldGenPromotedBytes | Podwyższ poziom do starego rozmiaru danych generacji | Bajty | Maksimum
Tak | Microsoft. AppPlatform/Sprężyna | SystemCpuUsagePercentage | Procent użycia procesora systemu | Procent | Średnia
Tak | Microsoft. AppPlatform/Sprężyna | TomcatErrorCount | Błąd globalny Tomcat | Liczba | Łącznie
Tak | Microsoft. AppPlatform/Sprężyna | TomcatReceivedBytes | Całkowita liczba odebranych bajtów Tomcat | Bajty | Łącznie
Tak | Microsoft. AppPlatform/Sprężyna | TomcatRequestMaxTime | Maksymalny czas żądania Tomcat | MS | Maksimum
Tak | Microsoft. AppPlatform/Sprężyna | TomcatRequestTotalCount | Łączna liczba żądań Tomcat | Liczba | Łącznie
Tak | Microsoft. AppPlatform/Sprężyna | TomcatRequestTotalTime | Łączny czas żądania Tomcat | MS | Łącznie
Tak | Microsoft. AppPlatform/Sprężyna | TomcatResponseAvgTime | Średni czas żądania Tomcat | MS | Średnia
Tak | Microsoft. AppPlatform/Sprężyna | TomcatSentBytes | Całkowita liczba wysłanych bajtów Tomcat | Bajty | Łącznie
Tak | Microsoft. AppPlatform/Sprężyna | TomcatSessionActiveCurrentCount | Liczba aktywności sesji Tomcat | Liczba | Łącznie
Tak | Microsoft. AppPlatform/Sprężyna | TomcatSessionActiveMaxCount | Maksymalna liczba aktywnych sesji Tomcat | Liczba | Łącznie
Tak | Microsoft. AppPlatform/Sprężyna | TomcatSessionAliveMaxTime | Maksymalny czas aktywności sesji Tomcat | MS | Maksimum
Tak | Microsoft. AppPlatform/Sprężyna | TomcatSessionCreatedCount | Liczba utworzonych sesji Tomcat | Liczba | Łącznie
Tak | Microsoft. AppPlatform/Sprężyna | TomcatSessionExpiredCount | Liczba wygasłych sesji Tomcat | Liczba | Łącznie
Tak | Microsoft. AppPlatform/Sprężyna | TomcatSessionRejectedCount | Liczba odrzuconych sesji Tomcat | Liczba | Łącznie
Tak | Microsoft. AppPlatform/Sprężyna | YoungGenPromotedBytes | Podwyższ poziom do rozmiaru danych dla małych generacji | Bajty | Maksimum
Tak | Microsoft.Automation/automationAccounts | TotalJob | Łączna liczba zadań | Liczba | Łącznie
Tak | Microsoft.Automation/automationAccounts | TotalUpdateDeploymentMachineRuns | Łączna liczba uruchomień maszyn wdrożenia aktualizacji | Liczba | Łącznie
Tak | Microsoft.Automation/automationAccounts | TotalUpdateDeploymentRuns | Łączna liczba przebiegów wdrożenia aktualizacji | Liczba | Łącznie
Nie | Microsoft.Batch/batchAccounts | CoreCount | Liczba dedykowanych rdzeni | Liczba | Łącznie
Nie | Microsoft.Batch/batchAccounts | CreatingNodeCount | Tworzenie liczby węzłów | Liczba | Łącznie
Nie | Microsoft.Batch/batchAccounts | IdleNodeCount | Liczba węzłów bezczynności | Liczba | Łącznie
Tak | Microsoft.Batch/batchAccounts | JobDeleteCompleteEvent | Zdarzenia ukończenia usuwania zadania | Liczba | Łącznie
Tak | Microsoft.Batch/batchAccounts | JobDeleteStartEvent | Zdarzenia rozpoczęcia usuwania zadania | Liczba | Łącznie
Tak | Microsoft.Batch/batchAccounts | JobDisableCompleteEvent | Zdarzenia ukończenia wyłączania zadania | Liczba | Łącznie
Tak | Microsoft.Batch/batchAccounts | JobDisableStartEvent | Wyłącz zdarzenia uruchamiania dla zadania | Liczba | Łącznie
Tak | Microsoft.Batch/batchAccounts | JobStartEvent | Zdarzenia uruchamiania zadania | Liczba | Łącznie
Tak | Microsoft.Batch/batchAccounts | JobTerminateCompleteEvent | Zdarzenia ukończenia kończenia zadania | Liczba | Łącznie
Tak | Microsoft.Batch/batchAccounts | JobTerminateStartEvent | Zdarzenia rozpoczęcia zakończenia zadania | Liczba | Łącznie
Nie | Microsoft.Batch/batchAccounts | LeavingPoolNodeCount | Opuszczanie liczby węzłów puli | Liczba | Łącznie
Nie | Microsoft.Batch/batchAccounts | LowPriorityCoreCount | LowPriority rdzeń | Liczba | Łącznie
Nie | Microsoft.Batch/batchAccounts | OfflineNodeCount | Liczba węzłów w trybie offline | Liczba | Łącznie
Tak | Microsoft.Batch/batchAccounts | PoolCreateEvent | Zdarzenia tworzenia puli | Liczba | Łącznie
Tak | Microsoft.Batch/batchAccounts | PoolDeleteCompleteEvent | Zdarzenia ukończenia usuwania puli | Liczba | Łącznie
Tak | Microsoft.Batch/batchAccounts | PoolDeleteStartEvent | Zdarzenia uruchamiania usuwania puli | Liczba | Łącznie
Tak | Microsoft.Batch/batchAccounts | PoolResizeCompleteEvent | Zdarzenia ukończenia zmiany rozmiaru puli | Liczba | Łącznie
Tak | Microsoft.Batch/batchAccounts | PoolResizeStartEvent | Zdarzenia rozpoczęcia zmiany rozmiaru puli | Liczba | Łącznie
Nie | Microsoft.Batch/batchAccounts | PreemptedNodeCount | Liczba przeniesiona węzłów | Liczba | Łącznie
Nie | Microsoft.Batch/batchAccounts | RebootingNodeCount | Ponowny rozruch liczby węzłów | Liczba | Łącznie
Nie | Microsoft.Batch/batchAccounts | ReimagingNodeCount | Liczba węzłów regraficznych | Liczba | Łącznie
Nie | Microsoft.Batch/batchAccounts | RunningNodeCount | Liczba uruchomionych węzłów | Liczba | Łącznie
Nie | Microsoft.Batch/batchAccounts | StartingNodeCount | Początkowa liczba węzłów | Liczba | Łącznie
Nie | Microsoft.Batch/batchAccounts | StartTaskFailedNodeCount | Uruchamianie zadania nie powiodło się liczba węzłów | Liczba | Łącznie
Tak | Microsoft.Batch/batchAccounts | TaskCompleteEvent | Zdarzenia ukończenia zadania | Liczba | Łącznie
Tak | Microsoft.Batch/batchAccounts | TaskFailEvent | Zdarzenia błędów zadań | Liczba | Łącznie
Tak | Microsoft.Batch/batchAccounts | TaskStartEvent | Zdarzenia uruchamiania zadania | Liczba | Łącznie
Nie | Microsoft.Batch/batchAccounts | TotalLowPriorityNodeCount | Liczba węzłów o niskim priorytecie | Liczba | Łącznie
Nie | Microsoft.Batch/batchAccounts | TotalNodeCount | Liczba dedykowanych węzłów | Liczba | Łącznie
Nie | Microsoft.Batch/batchAccounts | UnusableNodeCount | Liczba węzłów, których nie można użyć | Liczba | Łącznie
Nie | Microsoft.Batch/batchAccounts | WaitingForStartTaskNodeCount | Oczekiwanie na liczbę węzłów zadania uruchamiania | Liczba | Łącznie
Tak | Microsoft. Batchai Job/obszary robocze | Aktywne rdzenie | Aktywne rdzenie | Liczba | Średnia
Tak | Microsoft. Batchai Job/obszary robocze | Aktywne węzły | Aktywne węzły | Liczba | Średnia
Tak | Microsoft. Batchai Job/obszary robocze | Rdzenie bezczynne | Rdzenie bezczynne | Liczba | Średnia
Tak | Microsoft. Batchai Job/obszary robocze | Węzły bezczynne | Węzły bezczynne | Liczba | Średnia
Tak | Microsoft. Batchai Job/obszary robocze | Ukończono zadanie | Ukończono zadanie | Liczba | Łącznie
Tak | Microsoft. Batchai Job/obszary robocze | Przesłane zadanie | Przesłane zadanie | Liczba | Łącznie
Tak | Microsoft. Batchai Job/obszary robocze | Opuszczanie rdzeni | Opuszczanie rdzeni | Liczba | Średnia
Tak | Microsoft. Batchai Job/obszary robocze | Opuszczanie węzłów | Opuszczanie węzłów | Liczba | Średnia
Tak | Microsoft. Batchai Job/obszary robocze | Występujące rdzenie | Występujące rdzenie | Liczba | Średnia
Tak | Microsoft. Batchai Job/obszary robocze | Zastępujące węzły | Zastępujące węzły | Liczba | Średnia
Tak | Microsoft. Batchai Job/obszary robocze | Procent wykorzystania przydziałów | Procent wykorzystania przydziałów | Liczba | Średnia
Tak | Microsoft. Batchai Job/obszary robocze | Łączna liczba rdzeni | Łączna liczba rdzeni | Liczba | Średnia
Tak | Microsoft. Batchai Job/obszary robocze | Łączna liczba węzłów | Łączna liczba węzłów | Liczba | Średnia
Tak | Microsoft. Batchai Job/obszary robocze | Rdzenie, których nie można używać | Rdzenie, których nie można używać | Liczba | Średnia
Tak | Microsoft. Batchai Job/obszary robocze | Węzły niezdatne do użytku | Węzły niezdatne do użytku | Liczba | Średnia
Tak | Microsoft. łańcucha bloków/blockchainMembers | ConnectionAccepted | Zaakceptowane połączenia | Liczba | Łącznie
Tak | Microsoft. łańcucha bloków/blockchainMembers | ConnectionActive | Aktywne połączenia | Liczba | Średnia
Tak | Microsoft. łańcucha bloków/blockchainMembers | ConnectionHandled | Obsłużone połączenia | Liczba | Łącznie
Tak | Microsoft. łańcucha bloków/blockchainMembers | CpuUsagePercentageInDouble | Procent użycia procesora CPU | Procent | Maksimum
Tak | Microsoft. łańcucha bloków/blockchainMembers | IOReadBytes | Bajty odczytu we/wy | Bajty | Łącznie
Tak | Microsoft. łańcucha bloków/blockchainMembers | IOWriteBytes | Bajty zapisu we/wy | Bajty | Łącznie
Tak | Microsoft. łańcucha bloków/blockchainMembers | MemoryLimit | Limit pamięci | Bajty | Średnia
Tak | Microsoft. łańcucha bloków/blockchainMembers | MemoryUsage | Użycie pamięci | Bajty | Średnia
Tak | Microsoft. łańcucha bloków/blockchainMembers | MemoryUsagePercentageInDouble | Procent użycia pamięci | Procent | Średnia
Tak | Microsoft. łańcucha bloków/blockchainMembers | PendingTransactions | Oczekujące transakcje | Liczba | Średnia
Tak | Microsoft. łańcucha bloków/blockchainMembers | ProcessedBlocks | Przetworzone bloki | Liczba | Łącznie
Tak | Microsoft. łańcucha bloków/blockchainMembers | ProcessedTransactions | Przetworzone transakcje | Liczba | Łącznie
Tak | Microsoft. łańcucha bloków/blockchainMembers | QueuedTransactions | Transakcje w kolejce | Liczba | Średnia
Tak | Microsoft. łańcucha bloków/blockchainMembers | RequestHandled | Obsłużone żądania | Liczba | Łącznie
Tak | Microsoft. łańcucha bloków/blockchainMembers | StorageUsage | Użycie magazynu | Bajty | Średnia
Tak | Microsoft.Cache/redis | cachehits | Trafienia pamięci podręcznej | Liczba | Łącznie
Tak | Microsoft.Cache/redis | cachehits0 | Trafienia pamięci podręcznej (fragmentu 0) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | cachehits1 | Trafienia pamięci podręcznej (fragmentu 1) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | cachehits2 | Trafienia pamięci podręcznej (fragmentu 2) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | cachehits3 | Trafienia pamięci podręcznej (fragmentu 3) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | cachehits4 | Trafienia pamięci podręcznej (fragmentu 4) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | cachehits5 | Trafienia pamięci podręcznej (fragmentu 5) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | cachehits6 | Trafienia pamięci podręcznej (fragmentu 6) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | cachehits7 | Trafienia pamięci podręcznej (fragmentu 7) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | cachehits8 | Trafienia pamięci podręcznej (fragmentu 8) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | cachehits9 | Trafienia pamięci podręcznej (fragmentu 9) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | cacheLatency | Mikrosekundy opóźnienia pamięci podręcznej (wersja zapoznawcza) | Liczba | Średnia
Tak | Microsoft.Cache/redis | cachemisses | Chybienia w pamięci podręcznej | Liczba | Łącznie
Tak | Microsoft.Cache/redis | cachemisses0 | Chybienia w pamięci podręcznej (fragmentu 0) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | cachemisses1 | Chybienia w pamięci podręcznej (fragmentu 1) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | cachemisses2 | Chybienia w pamięci podręcznej (fragmentu 2) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | cachemisses3 | Chybienia w pamięci podręcznej (fragmentu 3) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | cachemisses4 | Chybienia w pamięci podręcznej (fragmentu 4) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | cachemisses5 | Chybienia w pamięci podręcznej (fragmentu 5) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | cachemisses6 | Chybienia w pamięci podręcznej (fragmentu 6) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | cachemisses7 | Chybienia w pamięci podręcznej (fragmentu 7) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | cachemisses8 | Chybienia w pamięci podręcznej (fragmentu 8) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | cachemisses9 | Chybienia w pamięci podręcznej (fragmentu 9) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | cacheRead | Odczyt pamięci podręcznej | BytesPerSecond | Maksimum
Tak | Microsoft.Cache/redis | cacheRead0 | Odczyt pamięci podręcznej (fragmentu 0) | BytesPerSecond | Maksimum
Tak | Microsoft.Cache/redis | cacheRead1 | Odczyt pamięci podręcznej (fragmentu 1) | BytesPerSecond | Maksimum
Tak | Microsoft.Cache/redis | cacheRead2 | Odczyt pamięci podręcznej (fragmentu 2) | BytesPerSecond | Maksimum
Tak | Microsoft.Cache/redis | cacheRead3 | Odczyt pamięci podręcznej (fragmentu 3) | BytesPerSecond | Maksimum
Tak | Microsoft.Cache/redis | cacheRead4 | Odczyt pamięci podręcznej (fragmentu 4) | BytesPerSecond | Maksimum
Tak | Microsoft.Cache/redis | cacheRead5 | Odczyt pamięci podręcznej (fragmentu 5) | BytesPerSecond | Maksimum
Tak | Microsoft.Cache/redis | cacheRead6 | Odczyt pamięci podręcznej (fragmentu 6) | BytesPerSecond | Maksimum
Tak | Microsoft.Cache/redis | cacheRead7 | Odczyt pamięci podręcznej (fragmentu 7) | BytesPerSecond | Maksimum
Tak | Microsoft.Cache/redis | cacheRead8 | Odczyt pamięci podręcznej (fragmentu 8) | BytesPerSecond | Maksimum
Tak | Microsoft.Cache/redis | cacheRead9 | Odczyt pamięci podręcznej (fragmentu 9) | BytesPerSecond | Maksimum
Tak | Microsoft.Cache/redis | cacheWrite | Zapis pamięci podręcznej | BytesPerSecond | Maksimum
Tak | Microsoft.Cache/redis | cacheWrite0 | Zapis pamięci podręcznej (fragmentu 0) | BytesPerSecond | Maksimum
Tak | Microsoft.Cache/redis | cacheWrite1 | Zapisywanie pamięci podręcznej (fragmentu 1) | BytesPerSecond | Maksimum
Tak | Microsoft.Cache/redis | cacheWrite2 | Zapisywanie pamięci podręcznej (fragmentu 2) | BytesPerSecond | Maksimum
Tak | Microsoft.Cache/redis | cacheWrite3 | Zapis w pamięci podręcznej (fragmentu 3) | BytesPerSecond | Maksimum
Tak | Microsoft.Cache/redis | cacheWrite4 | Zapis pamięci podręcznej (fragmentu 4) | BytesPerSecond | Maksimum
Tak | Microsoft.Cache/redis | cacheWrite5 | Zapis w pamięci podręcznej (fragmentu 5) | BytesPerSecond | Maksimum
Tak | Microsoft.Cache/redis | cacheWrite6 | Zapis w pamięci podręcznej (fragmentu 6) | BytesPerSecond | Maksimum
Tak | Microsoft.Cache/redis | cacheWrite7 | Zapis w pamięci podręcznej (fragmentu 7) | BytesPerSecond | Maksimum
Tak | Microsoft.Cache/redis | cacheWrite8 | Zapis w pamięci podręcznej (fragmentu 8) | BytesPerSecond | Maksimum
Tak | Microsoft.Cache/redis | cacheWrite9 | Zapis w pamięci podręcznej (fragmentu 9) | BytesPerSecond | Maksimum
Tak | Microsoft.Cache/redis | connectedclients | Połączeni klienci | Liczba | Maksimum
Tak | Microsoft.Cache/redis | connectedclients0 | Połączeni klienci (fragmentu 0) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | connectedclients1 | Połączeni klienci (fragmentu 1) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | connectedclients2 | Połączeni klienci (fragmentu 2) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | connectedclients3 | Połączeni klienci (fragmentu 3) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | connectedclients4 | Połączeni klienci (fragmentu 4) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | connectedclients5 | Połączeni klienci (fragmentu 5) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | connectedclients6 | Połączeni klienci (fragmentu 6) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | connectedclients7 | Połączeni klienci (fragmentu 7) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | connectedclients8 | Połączeni klienci (fragmentu 8) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | connectedclients9 | Połączeni klienci (fragmentu 9) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | błędy | Errors | Liczba | Maksimum
Tak | Microsoft.Cache/redis | evictedkeys | Wykluczone klucze | Liczba | Łącznie
Tak | Microsoft.Cache/redis | evictedkeys0 | Wykluczone klucze (fragmentu 0) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | evictedkeys1 | Wykluczone klucze (fragmentu 1) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | evictedkeys2 | Wykluczone klucze (fragmentu 2) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | evictedkeys3 | Wykluczone klucze (fragmentu 3) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | evictedkeys4 | Wykluczone klucze (fragmentu 4) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | evictedkeys5 | Wykluczone klucze (fragmentu 5) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | evictedkeys6 | Wykluczone klucze (fragmentu 6) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | evictedkeys7 | Wykluczone klucze (fragmentu 7) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | evictedkeys8 | Wykluczone klucze (fragmentu 8) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | evictedkeys9 | Wykluczone klucze (fragmentu 9) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | expiredkeys | Wygasłe klucze | Liczba | Łącznie
Tak | Microsoft.Cache/redis | expiredkeys0 | Wygasłe klucze (fragmentu 0) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | expiredkeys1 | Wygasłe klucze (fragmentu 1) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | expiredkeys2 | Wygasłe klucze (fragmentu 2) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | expiredkeys3 | Wygasłe klucze (fragmentu 3) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | expiredkeys4 | Wygasłe klucze (fragmentu 4) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | expiredkeys5 | Wygasłe klucze (fragmentu 5) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | expiredkeys6 | Wygasłe klucze (fragmentu 6) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | expiredkeys7 | Wygasłe klucze (fragmentu 7) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | expiredkeys8 | Wygasłe klucze (fragmentu 8) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | expiredkeys9 | Wygasłe klucze (fragmentu 9) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | GetCommands | Kto | Liczba | Łącznie
Tak | Microsoft.Cache/redis | getcommands0 | Pobiera (fragmentu 0) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | getcommands1 | Pobiera (fragmentu 1) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | getcommands2 | Pobiera (fragmentu 2) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | getcommands3 | Pobiera (fragmentu 3) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | getcommands4 | Pobiera (fragmentu 4) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | getcommands5 | Pobiera (fragmentu 5) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | getcommands6 | Pobiera (fragmentu 6) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | getcommands7 | Pobiera (fragmentu 7) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | getcommands8 | Pobiera (fragmentu 8) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | getcommands9 | Pobiera (fragmentu 9) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | operationsPerSecond | Liczba operacji na sekundę | Liczba | Maksimum
Tak | Microsoft.Cache/redis | operationsPerSecond0 | Operacje na sekundę (fragmentu 0) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | operationsPerSecond1 | Operacje na sekundę (fragmentu 1) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | operationsPerSecond2 | Operacje na sekundę (fragmentu 2) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | operationsPerSecond3 | Operacje na sekundę (fragmentu 3) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | operationsPerSecond4 | Operacje na sekundę (fragmentu 4) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | operationsPerSecond5 | Operacje na sekundę (fragmentu 5) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | operationsPerSecond6 | Operacje na sekundę (fragmentu 6) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | operationsPerSecond7 | Operacje na sekundę (fragmentu 7) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | operationsPerSecond8 | Operacje na sekundę (fragmentu 8) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | operationsPerSecond9 | Operacje na sekundę (fragmentu 9) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | percentProcessorTime | Procesor CPU | Procent | Maksimum
Tak | Microsoft.Cache/redis | percentProcessorTime0 | Procesor CPU (fragmentu 0) | Procent | Maksimum
Tak | Microsoft.Cache/redis | percentProcessorTime1 | Procesor CPU (fragmentu 1) | Procent | Maksimum
Tak | Microsoft.Cache/redis | percentProcessorTime2 | Procesor CPU (fragmentu 2) | Procent | Maksimum
Tak | Microsoft.Cache/redis | percentProcessorTime3 | Procesor CPU (fragmentu 3) | Procent | Maksimum
Tak | Microsoft.Cache/redis | percentProcessorTime4 | Procesor CPU (fragmentu 4) | Procent | Maksimum
Tak | Microsoft.Cache/redis | percentProcessorTime5 | Procesor CPU (fragmentu 5) | Procent | Maksimum
Tak | Microsoft.Cache/redis | percentProcessorTime6 | Procesor CPU (fragmentu 6) | Procent | Maksimum
Tak | Microsoft.Cache/redis | percentProcessorTime7 | Procesor CPU (fragmentu 7) | Procent | Maksimum
Tak | Microsoft.Cache/redis | percentProcessorTime8 | Procesor CPU (fragmentu 8) | Procent | Maksimum
Tak | Microsoft.Cache/redis | percentProcessorTime9 | Procesor CPU (fragmentu 9) | Procent | Maksimum
Tak | Microsoft.Cache/redis | serverLoad | Obciążenie serwera | Procent | Maksimum
Tak | Microsoft.Cache/redis | serverLoad0 | Ładowanie serwera (fragmentu 0) | Procent | Maksimum
Tak | Microsoft.Cache/redis | serverLoad1 | Ładowanie serwera (fragmentu 1) | Procent | Maksimum
Tak | Microsoft.Cache/redis | serverLoad2 | Ładowanie serwera (fragmentu 2) | Procent | Maksimum
Tak | Microsoft.Cache/redis | serverLoad3 | Ładowanie serwera (fragmentu 3) | Procent | Maksimum
Tak | Microsoft.Cache/redis | serverLoad4 | Ładowanie serwera (fragmentu 4) | Procent | Maksimum
Tak | Microsoft.Cache/redis | serverLoad5 | Ładowanie serwera (fragmentu 5) | Procent | Maksimum
Tak | Microsoft.Cache/redis | serverLoad6 | Ładowanie serwera (fragmentu 6) | Procent | Maksimum
Tak | Microsoft.Cache/redis | serverLoad7 | Ładowanie serwera (fragmentu 7) | Procent | Maksimum
Tak | Microsoft.Cache/redis | serverLoad8 | Ładowanie serwera (fragmentu 8) | Procent | Maksimum
Tak | Microsoft.Cache/redis | serverLoad9 | Ładowanie serwera (fragmentu 9) | Procent | Maksimum
Tak | Microsoft.Cache/redis | SetCommands | Przywraca | Liczba | Łącznie
Tak | Microsoft.Cache/redis | setcommands0 | Zestawy (fragmentu 0) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | setcommands1 | Zestawy (fragmentu 1) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | setcommands2 | Zestawy (fragmentu 2) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | setcommands3 | Zestawy (fragmentu 3) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | setcommands4 | Zestawy (fragmentu 4) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | setcommands5 | Zestawy (fragmentu 5) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | setcommands6 | Zestawy (fragmentu 6) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | setcommands7 | Zestawy (fragmentu 7) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | setcommands8 | Zestawy (fragmentu 8) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | setcommands9 | Zestawy (fragmentu 9) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | totalcommandsprocessed | Łączna liczba operacji | Liczba | Łącznie
Tak | Microsoft.Cache/redis | totalcommandsprocessed0 | Łączna liczba operacji (fragmentu 0) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | totalcommandsprocessed1 | Łączna liczba operacji (fragmentu 1) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | totalcommandsprocessed2 | Łączna liczba operacji (fragmentu 2) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | totalcommandsprocessed3 | Łączna liczba operacji (fragmentu 3) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | totalcommandsprocessed4 | Łączna liczba operacji (fragmentu 4) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | totalcommandsprocessed5 | Łączna liczba operacji (fragmentu 5) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | totalcommandsprocessed6 | Łączna liczba operacji (fragmentu 6) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | totalcommandsprocessed7 | Łączna liczba operacji (fragmentu 7) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | totalcommandsprocessed8 | Łączna liczba operacji (fragmentu 8) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | totalcommandsprocessed9 | Łączna liczba operacji (fragmentu 9) | Liczba | Łącznie
Tak | Microsoft.Cache/redis | totalkeys | Łączna liczba kluczy | Liczba | Maksimum
Tak | Microsoft.Cache/redis | totalkeys0 | Łączna liczba kluczy (fragmentu 0) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | totalkeys1 | Łączna liczba kluczy (fragmentu 1) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | totalkeys2 | Łączna liczba kluczy (fragmentu 2) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | totalkeys3 | Łączna liczba kluczy (fragmentu 3) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | totalkeys4 | Łączna liczba kluczy (fragmentu 4) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | totalkeys5 | Łączna liczba kluczy (fragmentu 5) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | totalkeys6 | Łączna liczba kluczy (fragmentu 6) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | totalkeys7 | Łączna liczba kluczy (fragmentu 7) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | totalkeys8 | Łączna liczba kluczy (fragmentu 8) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | totalkeys9 | Łączna liczba kluczy (fragmentu 9) | Liczba | Maksimum
Tak | Microsoft.Cache/redis | usedmemory | Używana pamięć | Bajty | Maksimum
Tak | Microsoft.Cache/redis | usedmemory0 | Używana pamięć (fragmentu 0) | Bajty | Maksimum
Tak | Microsoft.Cache/redis | usedmemory1 | Używana pamięć (fragmentu 1) | Bajty | Maksimum
Tak | Microsoft.Cache/redis | usedmemory2 | Używana pamięć (fragmentu 2) | Bajty | Maksimum
Tak | Microsoft.Cache/redis | usedmemory3 | Używana pamięć (fragmentu 3) | Bajty | Maksimum
Tak | Microsoft.Cache/redis | usedmemory4 | Używana pamięć (fragmentu 4) | Bajty | Maksimum
Tak | Microsoft.Cache/redis | usedmemory5 | Używana pamięć (fragmentu 5) | Bajty | Maksimum
Tak | Microsoft.Cache/redis | usedmemory6 | Używana pamięć (fragmentu 6) | Bajty | Maksimum
Tak | Microsoft.Cache/redis | usedmemory7 | Używana pamięć (fragmentu 7) | Bajty | Maksimum
Tak | Microsoft.Cache/redis | usedmemory8 | Używana pamięć (fragmentu 8) | Bajty | Maksimum
Tak | Microsoft.Cache/redis | usedmemory9 | Używana pamięć (fragmentu 9) | Bajty | Maksimum
Tak | Microsoft.Cache/redis | usedmemorypercentage | Wartość procentowa używanej pamięci | Procent | Maksimum
Tak | Microsoft.Cache/redis | usedmemoryRss | Używana pamięć RSS | Bajty | Maksimum
Tak | Microsoft.Cache/redis | usedmemoryRss0 | Używana pamięć RSS (fragmentu 0) | Bajty | Maksimum
Tak | Microsoft.Cache/redis | usedmemoryRss1 | Używana pamięć RSS (fragmentu 1) | Bajty | Maksimum
Tak | Microsoft.Cache/redis | usedmemoryRss2 | Używana pamięć RSS (fragmentu 2) | Bajty | Maksimum
Tak | Microsoft.Cache/redis | usedmemoryRss3 | Używana pamięć RSS (fragmentu 3) | Bajty | Maksimum
Tak | Microsoft.Cache/redis | usedmemoryRss4 | Używana pamięć RSS (fragmentu 4) | Bajty | Maksimum
Tak | Microsoft.Cache/redis | usedmemoryRss5 | Używana pamięć RSS (fragmentu 5) | Bajty | Maksimum
Tak | Microsoft.Cache/redis | usedmemoryRss6 | Używana pamięć RSS (fragmentu 6) | Bajty | Maksimum
Tak | Microsoft.Cache/redis | usedmemoryRss7 | Używana pamięć RSS (fragmentu 7) | Bajty | Maksimum
Tak | Microsoft.Cache/redis | usedmemoryRss8 | Używana pamięć RSS (fragmentu 8) | Bajty | Maksimum
Tak | Microsoft.Cache/redis | usedmemoryRss9 | Używana pamięć RSS (fragmentu 9) | Bajty | Maksimum
Nie | Microsoft.ClassicCompute/domainNames/slots/roles | Bajty odczytu dysku/s | Odczyt dysku | BytesPerSecond | Średnia
Tak | Microsoft.ClassicCompute/domainNames/slots/roles | Operacje odczytu z dysku/s | Operacje odczytu z dysku/s | CountPerSecond | Średnia
Nie | Microsoft.ClassicCompute/domainNames/slots/roles | Bajty zapisu dysku/s | Zapis na dysku | BytesPerSecond | Średnia
Tak | Microsoft.ClassicCompute/domainNames/slots/roles | Operacje zapisu na dysku/s | Operacje zapisu na dysku/s | CountPerSecond | Średnia
Tak | Microsoft.ClassicCompute/domainNames/slots/roles | Sieć — wejście | Sieć — wejście | Bajty | Łącznie
Tak | Microsoft.ClassicCompute/domainNames/slots/roles | Sieć — wyjście | Sieć — wyjście | Bajty | Łącznie
Tak | Microsoft.ClassicCompute/domainNames/slots/roles | Procentowe użycie procesora CPU | Procentowe użycie procesora CPU | Procent | Średnia
Nie | Microsoft.ClassicCompute/virtualMachines | Bajty odczytu dysku/s | Odczyt dysku | BytesPerSecond | Średnia
Tak | Microsoft.ClassicCompute/virtualMachines | Operacje odczytu z dysku/s | Operacje odczytu z dysku/s | CountPerSecond | Średnia
Nie | Microsoft.ClassicCompute/virtualMachines | Bajty zapisu dysku/s | Zapis na dysku | BytesPerSecond | Średnia
Tak | Microsoft.ClassicCompute/virtualMachines | Operacje zapisu na dysku/s | Operacje zapisu na dysku/s | CountPerSecond | Średnia
Tak | Microsoft.ClassicCompute/virtualMachines | Sieć — wejście | Sieć — wejście | Bajty | Łącznie
Tak | Microsoft.ClassicCompute/virtualMachines | Sieć — wyjście | Sieć — wyjście | Bajty | Łącznie
Tak | Microsoft.ClassicCompute/virtualMachines | Procentowe użycie procesora CPU | Procentowe użycie procesora CPU | Procent | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts | Dostępność | Dostępność | Procent | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts | Ruch wychodzący | Ruch wychodzący | Bajty | Łącznie
Tak | Microsoft. ClassicStorage/storageAccounts | Zdarzenia związane z transferem danych przychodzących | Zdarzenia związane z transferem danych przychodzących | Bajty | Łącznie
Tak | Microsoft. ClassicStorage/storageAccounts | SuccessE2ELatency | Opóźnienie e2e dla powodzenia | MS | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts | SuccessServerLatency | Opóźnienie serwera dla powodzenia | MS | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts | Transakcje | Transakcje | Liczba | Łącznie
Nie | Microsoft. ClassicStorage/storageAccounts | UsedCapacity | Używana pojemność | Bajty | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts/blobServices | Dostępność | Dostępność | Procent | Średnia
Nie | Microsoft. ClassicStorage/storageAccounts/blobServices | BlobCapacity | Pojemność obiektu blob | Bajty | Średnia
Nie | Microsoft. ClassicStorage/storageAccounts/blobServices | BlobCount | Liczba obiektów blob | Liczba | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts/blobServices | ContainerCount | Liczba kontenerów obiektów blob | Liczba | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts/blobServices | Ruch wychodzący | Ruch wychodzący | Bajty | Łącznie
Nie | Microsoft. ClassicStorage/storageAccounts/blobServices | IndexCapacity | Pojemność indeksu | Bajty | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts/blobServices | Zdarzenia związane z transferem danych przychodzących | Zdarzenia związane z transferem danych przychodzących | Bajty | Łącznie
Tak | Microsoft. ClassicStorage/storageAccounts/blobServices | SuccessE2ELatency | Opóźnienie e2e dla powodzenia | MS | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts/blobServices | SuccessServerLatency | Opóźnienie serwera dla powodzenia | MS | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts/blobServices | Transakcje | Transakcje | Liczba | Łącznie
Tak | Microsoft. ClassicStorage/storageAccounts/fileServices | Dostępność | Dostępność | Procent | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts/fileServices | Ruch wychodzący | Ruch wychodzący | Bajty | Łącznie
Nie | Microsoft. ClassicStorage/storageAccounts/fileServices | FileCapacity | Pojemność plików | Bajty | Średnia
Nie | Microsoft. ClassicStorage/storageAccounts/fileServices | FileCount | Liczba plików | Liczba | Średnia
Nie | Microsoft. ClassicStorage/storageAccounts/fileServices | FileShareCount | Liczba udziałów plików | Liczba | Średnia
Nie | Microsoft. ClassicStorage/storageAccounts/fileServices | FileShareQuota | Rozmiar przydziału udziału plików | Bajty | Średnia
Nie | Microsoft. ClassicStorage/storageAccounts/fileServices | FileShareSnapshotCount | Liczba migawek udziału plików | Liczba | Średnia
Nie | Microsoft. ClassicStorage/storageAccounts/fileServices | FileShareSnapshotSize | Rozmiar migawki udziału plików | Bajty | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts/fileServices | Zdarzenia związane z transferem danych przychodzących | Zdarzenia związane z transferem danych przychodzących | Bajty | Łącznie
Tak | Microsoft. ClassicStorage/storageAccounts/fileServices | SuccessE2ELatency | Opóźnienie e2e dla powodzenia | MS | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts/fileServices | SuccessServerLatency | Opóźnienie serwera dla powodzenia | MS | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts/fileServices | Transakcje | Transakcje | Liczba | Łącznie
Tak | Microsoft. ClassicStorage/storageAccounts/queueServices | Dostępność | Dostępność | Procent | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts/queueServices | Ruch wychodzący | Ruch wychodzący | Bajty | Łącznie
Tak | Microsoft. ClassicStorage/storageAccounts/queueServices | Zdarzenia związane z transferem danych przychodzących | Zdarzenia związane z transferem danych przychodzących | Bajty | Łącznie
Tak | Microsoft. ClassicStorage/storageAccounts/queueServices | QueueCapacity | Pojemność kolejki | Bajty | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts/queueServices | QueueCount | Liczba kolejek | Liczba | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts/queueServices | QueueMessageCount | Liczba komunikatów w kolejce | Liczba | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts/queueServices | SuccessE2ELatency | Opóźnienie e2e dla powodzenia | MS | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts/queueServices | SuccessServerLatency | Opóźnienie serwera dla powodzenia | MS | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts/queueServices | Transakcje | Transakcje | Liczba | Łącznie
Tak | Microsoft. ClassicStorage/storageAccounts/tableServices | Dostępność | Dostępność | Procent | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts/tableServices | Ruch wychodzący | Ruch wychodzący | Bajty | Łącznie
Tak | Microsoft. ClassicStorage/storageAccounts/tableServices | Zdarzenia związane z transferem danych przychodzących | Zdarzenia związane z transferem danych przychodzących | Bajty | Łącznie
Tak | Microsoft. ClassicStorage/storageAccounts/tableServices | SuccessE2ELatency | Opóźnienie e2e dla powodzenia | MS | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts/tableServices | SuccessServerLatency | Opóźnienie serwera dla powodzenia | MS | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts/tableServices | TableCapacity | Pojemność tabeli | Bajty | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts/tableServices | TableCount | Liczba tabel | Liczba | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts/tableServices | TableEntityCount | Liczba jednostek tabel | Liczba | Średnia
Tak | Microsoft. ClassicStorage/storageAccounts/tableServices | Transakcje | Transakcje | Liczba | Łącznie
Tak | Microsoft.CognitiveServices/accounts | BlockedCalls | Zablokowane wywołania | Liczba | Łącznie
Tak | Microsoft.CognitiveServices/accounts | CharactersTrained | Znaki przeszkolone | Liczba | Łącznie
Tak | Microsoft.CognitiveServices/accounts | CharactersTranslated | Znaki tłumaczone | Liczba | Łącznie
Tak | Microsoft.CognitiveServices/accounts | ClientErrors | Błędy klienta | Liczba | Łącznie
Tak | Microsoft.CognitiveServices/accounts | Dane | Dane w | Bajty | Łącznie
Tak | Microsoft.CognitiveServices/accounts | DataOut | Dane wychodzące | Bajty | Łącznie
Tak | Microsoft.CognitiveServices/accounts | Opóźnienie | Opóźnienie | MilliSeconds | Średnia
Tak | Microsoft.CognitiveServices/accounts | Błędy servererrors | Błędy serwera | Liczba | Łącznie
Tak | Microsoft.CognitiveServices/accounts | SpeechSessionDuration | Czas trwania sesji mowy | Sekundy | Łącznie
Tak | Microsoft.CognitiveServices/accounts | SuccessfulCalls | Pomyślne wywołania | Liczba | Łącznie
Tak | Microsoft.CognitiveServices/accounts | TotalCalls | Łączna liczba wywołań | Liczba | Łącznie
Tak | Microsoft.CognitiveServices/accounts | TotalErrors | Łączna liczba błędów | Liczba | Łącznie
Tak | Microsoft.CognitiveServices/accounts | TotalTokenCalls | Łączna liczba wywołań tokenów | Liczba | Łącznie
Tak | Microsoft.CognitiveServices/accounts | TotalTransactions | Łączna liczba transakcji | Liczba | Łącznie
Tak | Microsoft.Compute/virtualMachines | Wykorzystane środki CPU | Wykorzystane środki CPU | Liczba | Średnia
Tak | Microsoft.Compute/virtualMachines | Pozostałe kredyty procesora CPU | Pozostałe kredyty procesora CPU | Liczba | Średnia
Tak | Microsoft.Compute/virtualMachines | Głębokość kolejki dysku danych | Głębokość kolejki dysku danych (wersja zapoznawcza) | Liczba | Średnia
Tak | Microsoft.Compute/virtualMachines | Bajty odczytu dysku danych/s | Bajty odczytu dysku danych/s (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachines | Operacje odczytu z dysku danych/s | Operacje odczytu z dysku danych/s (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachines | Bajty zapisu na dysku danych/s | Bajty zapisu na dysku danych/s (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachines | Operacje zapisu na dysku danych/s | Operacje zapisu na dysku danych/s (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachines | Bajty odczytu dysku | Bajty odczytu dysku | Bajty | Łącznie
Tak | Microsoft.Compute/virtualMachines | Operacje odczytu z dysku/s | Operacje odczytu z dysku/s | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachines | Bajty zapisu dysku | Bajty zapisu dysku | Bajty | Łącznie
Tak | Microsoft.Compute/virtualMachines | Operacje zapisu na dysku/s | Operacje zapisu na dysku/s | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachines | Przepływy przychodzące | Przepływy przychodzące | Liczba | Średnia
Tak | Microsoft.Compute/virtualMachines | Maksymalny współczynnik tworzenia przepływów przychodzących | Maksymalny współczynnik tworzenia przepływów przychodzących (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachines | Sieć — wejście | Sieć jest rozliczana (przestarzałe) | Bajty | Łącznie
Tak | Microsoft.Compute/virtualMachines | Sieć łącznie | Sieć łącznie | Bajty | Łącznie
Tak | Microsoft.Compute/virtualMachines | Sieć — wyjście | Sieć — do rozliczania (przestarzałe) | Bajty | Łącznie
Tak | Microsoft.Compute/virtualMachines | Całkowita liczba sieci | Całkowita liczba sieci | Bajty | Łącznie
Tak | Microsoft.Compute/virtualMachines | Głębokość kolejki dysku systemu operacyjnego | Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza) | Liczba | Średnia
Tak | Microsoft.Compute/virtualMachines | Bajty odczytu dysku systemu operacyjnego/s | Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachines | Operacje odczytu z dysku systemu operacyjnego/s | Operacje odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachines | Bajty zapisu dysku systemu operacyjnego/s | Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachines | Operacje zapisu na dysku systemu operacyjnego/s | Operacje zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachines | Głębokość kolejki systemu operacyjnego na dysku | Głębokość kolejki dysku systemu operacyjnego (przestarzałe) | Liczba | Średnia
Tak | Microsoft.Compute/virtualMachines | Bajty odczytu dysku systemu operacyjnego/s | Bajty odczytu z dysku systemu operacyjnego/s (przestarzałe) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachines | Operacje odczytu na dysku systemu operacyjnego/s | Operacje odczytu z dysku systemu operacyjnego/s (przestarzałe) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachines | Bajty zapisu na dysku systemu operacyjnego/s | Bajty zapisu dysku systemu operacyjnego/s (przestarzałe) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachines | Operacje zapisu na dysku systemu operacyjnego/s | Operacje zapisu na dysku systemu operacyjnego/s (przestarzałe) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachines | Przepływy wychodzące | Przepływy wychodzące | Liczba | Średnia
Tak | Microsoft.Compute/virtualMachines | Maksymalny współczynnik tworzenia przepływów wychodzących | Maksymalny współczynnik tworzenia przepływów wychodzących (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachines | Na dysk głębokość kolejki | Głębokość kolejki dysku danych (przestarzałe) | Liczba | Średnia
Tak | Microsoft.Compute/virtualMachines | Bajty odczytu na dysk/s | Bajty odczytu dysku danych/s (przestarzałe) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachines | Operacje odczytu na dysk/s | Operacje odczytu z dysku danych/s (przestarzałe) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachines | Bajty zapisu na dysku/s | Bajty zapisu na dysku danych/s (przestarzałe) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachines | Operacje zapisu na dysku/s | Operacje zapisu na dysku danych/s (przestarzałe) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachines | Procentowe użycie procesora CPU | Procentowe użycie procesora CPU | Procent | Średnia
Tak | Microsoft.Compute/virtualMachines | Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium | Trafienie odczytu pamięci podręcznej dysku danych Premium (wersja zapoznawcza) | Procent | Średnia
Tak | Microsoft.Compute/virtualMachines | Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium | Chybienia odczytu pamięci podręcznej dysku z danymi Premium (wersja zapoznawcza) | Procent | Średnia
Tak | Microsoft.Compute/virtualMachines | Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium | Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza) | Procent | Średnia
Tak | Microsoft.Compute/virtualMachines | Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium | Brak chybień w pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza) | Procent | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Wykorzystane środki CPU | Wykorzystane środki CPU | Liczba | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Pozostałe kredyty procesora CPU | Pozostałe kredyty procesora CPU | Liczba | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Głębokość kolejki dysku danych | Głębokość kolejki dysku danych (wersja zapoznawcza) | Liczba | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Bajty odczytu dysku danych/s | Bajty odczytu dysku danych/s (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Operacje odczytu z dysku danych/s | Operacje odczytu z dysku danych/s (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Bajty zapisu na dysku danych/s | Bajty zapisu na dysku danych/s (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Operacje zapisu na dysku danych/s | Operacje zapisu na dysku danych/s (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Bajty odczytu dysku | Bajty odczytu dysku | Bajty | Łącznie
Tak | Microsoft.Compute/virtualMachineScaleSets | Operacje odczytu z dysku/s | Operacje odczytu z dysku/s | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Bajty zapisu dysku | Bajty zapisu dysku | Bajty | Łącznie
Tak | Microsoft.Compute/virtualMachineScaleSets | Operacje zapisu na dysku/s | Operacje zapisu na dysku/s | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Przepływy przychodzące | Przepływy przychodzące | Liczba | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Maksymalny współczynnik tworzenia przepływów przychodzących | Maksymalny współczynnik tworzenia przepływów przychodzących (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Sieć — wejście | Sieć jest rozliczana (przestarzałe) | Bajty | Łącznie
Tak | Microsoft.Compute/virtualMachineScaleSets | Sieć łącznie | Sieć łącznie | Bajty | Łącznie
Tak | Microsoft.Compute/virtualMachineScaleSets | Sieć — wyjście | Sieć — do rozliczania (przestarzałe) | Bajty | Łącznie
Tak | Microsoft.Compute/virtualMachineScaleSets | Całkowita liczba sieci | Całkowita liczba sieci | Bajty | Łącznie
Tak | Microsoft.Compute/virtualMachineScaleSets | Głębokość kolejki dysku systemu operacyjnego | Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza) | Liczba | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Bajty odczytu dysku systemu operacyjnego/s | Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Operacje odczytu z dysku systemu operacyjnego/s | Operacje odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Bajty zapisu dysku systemu operacyjnego/s | Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Operacje zapisu na dysku systemu operacyjnego/s | Operacje zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Głębokość kolejki systemu operacyjnego na dysku | Głębokość kolejki dysku systemu operacyjnego (przestarzałe) | Liczba | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Bajty odczytu dysku systemu operacyjnego/s | Bajty odczytu z dysku systemu operacyjnego/s (przestarzałe) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Operacje odczytu na dysku systemu operacyjnego/s | Operacje odczytu z dysku systemu operacyjnego/s (przestarzałe) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Bajty zapisu na dysku systemu operacyjnego/s | Bajty zapisu dysku systemu operacyjnego/s (przestarzałe) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Operacje zapisu na dysku systemu operacyjnego/s | Operacje zapisu na dysku systemu operacyjnego/s (przestarzałe) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Przepływy wychodzące | Przepływy wychodzące | Liczba | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Maksymalny współczynnik tworzenia przepływów wychodzących | Maksymalny współczynnik tworzenia przepływów wychodzących (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Na dysk głębokość kolejki | Głębokość kolejki dysku danych (przestarzałe) | Liczba | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Bajty odczytu na dysk/s | Bajty odczytu dysku danych/s (przestarzałe) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Operacje odczytu na dysk/s | Operacje odczytu z dysku danych/s (przestarzałe) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Bajty zapisu na dysku/s | Bajty zapisu na dysku danych/s (przestarzałe) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Operacje zapisu na dysku/s | Operacje zapisu na dysku danych/s (przestarzałe) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Procentowe użycie procesora CPU | Procentowe użycie procesora CPU | Procent | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium | Trafienie odczytu pamięci podręcznej dysku danych Premium (wersja zapoznawcza) | Procent | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium | Chybienia odczytu pamięci podręcznej dysku z danymi Premium (wersja zapoznawcza) | Procent | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium | Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza) | Procent | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets | Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium | Brak chybień w pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza) | Procent | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Wykorzystane środki CPU | Wykorzystane środki CPU | Liczba | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Pozostałe kredyty procesora CPU | Pozostałe kredyty procesora CPU | Liczba | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Głębokość kolejki dysku danych | Głębokość kolejki dysku danych (wersja zapoznawcza) | Liczba | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bajty odczytu dysku danych/s | Bajty odczytu dysku danych/s (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operacje odczytu z dysku danych/s | Operacje odczytu z dysku danych/s (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bajty zapisu na dysku danych/s | Bajty zapisu na dysku danych/s (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operacje zapisu na dysku danych/s | Operacje zapisu na dysku danych/s (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bajty odczytu dysku | Bajty odczytu dysku | Bajty | Łącznie
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operacje odczytu z dysku/s | Operacje odczytu z dysku/s | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bajty zapisu dysku | Bajty zapisu dysku | Bajty | Łącznie
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operacje zapisu na dysku/s | Operacje zapisu na dysku/s | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Przepływy przychodzące | Przepływy przychodzące | Liczba | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Maksymalny współczynnik tworzenia przepływów przychodzących | Maksymalny współczynnik tworzenia przepływów przychodzących (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Sieć — wejście | Sieć jest rozliczana (przestarzałe) | Bajty | Łącznie
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Sieć łącznie | Sieć łącznie | Bajty | Łącznie
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Sieć — wyjście | Sieć — do rozliczania (przestarzałe) | Bajty | Łącznie
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Całkowita liczba sieci | Całkowita liczba sieci | Bajty | Łącznie
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Głębokość kolejki dysku systemu operacyjnego | Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza) | Liczba | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bajty odczytu dysku systemu operacyjnego/s | Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operacje odczytu z dysku systemu operacyjnego/s | Operacje odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bajty zapisu dysku systemu operacyjnego/s | Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operacje zapisu na dysku systemu operacyjnego/s | Operacje zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Głębokość kolejki systemu operacyjnego na dysku | Głębokość kolejki dysku systemu operacyjnego (przestarzałe) | Liczba | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bajty odczytu dysku systemu operacyjnego/s | Bajty odczytu z dysku systemu operacyjnego/s (przestarzałe) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operacje odczytu na dysku systemu operacyjnego/s | Operacje odczytu z dysku systemu operacyjnego/s (przestarzałe) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bajty zapisu na dysku systemu operacyjnego/s | Bajty zapisu dysku systemu operacyjnego/s (przestarzałe) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operacje zapisu na dysku systemu operacyjnego/s | Operacje zapisu na dysku systemu operacyjnego/s (przestarzałe) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Przepływy wychodzące | Przepływy wychodzące | Liczba | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Maksymalny współczynnik tworzenia przepływów wychodzących | Maksymalny współczynnik tworzenia przepływów wychodzących (wersja zapoznawcza) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Na dysk głębokość kolejki | Głębokość kolejki dysku danych (przestarzałe) | Liczba | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bajty odczytu na dysk/s | Bajty odczytu dysku danych/s (przestarzałe) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operacje odczytu na dysk/s | Operacje odczytu z dysku danych/s (przestarzałe) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bajty zapisu na dysku/s | Bajty zapisu na dysku danych/s (przestarzałe) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operacje zapisu na dysku/s | Operacje zapisu na dysku danych/s (przestarzałe) | CountPerSecond | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Procentowe użycie procesora CPU | Procentowe użycie procesora CPU | Procent | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium | Trafienie odczytu pamięci podręcznej dysku danych Premium (wersja zapoznawcza) | Procent | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium | Chybienia odczytu pamięci podręcznej dysku z danymi Premium (wersja zapoznawcza) | Procent | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium | Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza) | Procent | Średnia
Tak | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium | Brak chybień w pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza) | Procent | Średnia
Tak | Microsoft.ContainerInstance/containerGroups | CpuUsage | Użycie procesora CPU | Liczba | Średnia
Tak | Microsoft.ContainerInstance/containerGroups | MemoryUsage | Użycie pamięci | Bajty | Średnia
Tak | Microsoft.ContainerInstance/containerGroups | NetworkBytesReceivedPerSecond | Bajty odebrane przez sieć na sekundę | Bajty | Średnia
Tak | Microsoft.ContainerInstance/containerGroups | NetworkBytesTransmittedPerSecond | Bajty przesyłane przez sieć na sekundę | Bajty | Średnia
Tak | Microsoft.ContainerRegistry/registries | RunDuration | Czas trwania przebiegu | MS | Łącznie
Tak | Microsoft.ContainerRegistry/registries | SuccessfulPullCount | Pomyślna liczba ściągania | Liczba | Średnia
Tak | Microsoft.ContainerRegistry/registries | SuccessfulPushCount | Liczba wypychanych zakończonych powodzeniem | Liczba | Średnia
Tak | Microsoft.ContainerRegistry/registries | TotalPullCount | Łączna liczba ściągania | Liczba | Średnia
Tak | Microsoft.ContainerRegistry/registries | TotalPushCount | Łączna liczba wypychanych | Liczba | Średnia
Nie | Microsoft.ContainerService/managedClusters | kube_node_status_allocatable_cpu_cores | Łączna liczba dostępnych rdzeni procesora CPU w zarządzanym klastrze | Liczba | Średnia
Nie | Microsoft.ContainerService/managedClusters | kube_node_status_allocatable_memory_bytes | Łączna ilość dostępnej pamięci w zarządzanym klastrze | Bajty | Średnia
Nie | Microsoft.ContainerService/managedClusters | kube_node_status_condition | Stany różnych warunków węzła | Liczba | Średnia
Nie | Microsoft.ContainerService/managedClusters | kube_pod_status_phase | Liczba etapów według fazy | Liczba | Średnia
Nie | Microsoft.ContainerService/managedClusters | kube_pod_status_ready | Liczba zasobników w stanie gotowe | Liczba | Średnia
Tak | Microsoft.DataBoxEdge/dataBoxEdgeDevices | AvailableCapacity | Dostępna pojemność | Bajty | Średnia
Tak | Microsoft.DataBoxEdge/dataBoxEdgeDevices | BytesUploadedToCloud | Przekazane bajty w chmurze (urządzenie) | Bajty | Średnia
Tak | Microsoft.DataBoxEdge/dataBoxEdgeDevices | BytesUploadedToCloudPerShare | Przekazane bajty w chmurze (udział) | Bajty | Średnia
Tak | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudReadThroughput | Przepływność pobierania w chmurze | BytesPerSecond | Średnia
Tak | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudReadThroughputPerShare | Przepływność pobierania w chmurze (udział) | BytesPerSecond | Średnia
Tak | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudUploadThroughput | Przepływność przekazywania w chmurze | BytesPerSecond | Średnia
Tak | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudUploadThroughputPerShare | Przepływność przekazywania w chmurze (udział) | BytesPerSecond | Średnia
Tak | Microsoft.DataBoxEdge/dataBoxEdgeDevices | HyperVMemoryUtilization | Obliczenia brzegowe — użycie pamięci | Procent | Średnia
Tak | Microsoft.DataBoxEdge/dataBoxEdgeDevices | HyperVVirtualProcessorUtilization | Obliczenia brzegowe — procentowy procesor CPU | Procent | Średnia
Tak | Microsoft.DataBoxEdge/dataBoxEdgeDevices | NICReadThroughput | Przepływność odczytu (Sieć) | BytesPerSecond | Średnia
Tak | Microsoft.DataBoxEdge/dataBoxEdgeDevices | NICWriteThroughput | Przepływność zapisu (Sieć) | BytesPerSecond | Średnia
Tak | Microsoft.DataBoxEdge/dataBoxEdgeDevices | Łączna pojemność | Całkowita pojemność | Bajty | Średnia
Tak | Microsoft.DataFactory/datafactories | FailedRuns | Nieudane uruchomienia | Liczba | Łącznie
Tak | Microsoft.DataFactory/datafactories | SuccessfulRuns | Pomyślne uruchomienia | Liczba | Łącznie
Tak | Microsoft.DataFactory/factories | ActivityCancelledRuns | Metryki uruchomień działań | Liczba | Łącznie
Tak | Microsoft.DataFactory/factories | ActivityFailedRuns | Metryki uruchamiania działań zakończonych niepowodzeniem | Liczba | Łącznie
Tak | Microsoft.DataFactory/factories | ActivitySucceededRuns | Metryki uruchamiania działań zakończonych powodzeniem | Liczba | Łącznie
Tak | Microsoft.DataFactory/factories | FactorySizeInGbUnits | Łączny rozmiar fabryki (jednostka GB) | Liczba | Maksimum
Tak | Microsoft.DataFactory/factories | IntegrationRuntimeAvailableMemory | Dostępna pamięć środowiska Integration Runtime | Bajty | Średnia
Tak | Microsoft.DataFactory/factories | IntegrationRuntimeAverageTaskPickupDelay | Czas trwania kolejki Integration Runtime | Sekundy | Średnia
Tak | Microsoft.DataFactory/factories | IntegrationRuntimeCpuPercentage | Użycie procesora Integration Runtime | Procent | Średnia
Tak | Microsoft.DataFactory/factories | IntegrationRuntimeQueueLength | Długość kolejki środowiska Integration Runtime | Liczba | Średnia
Tak | Microsoft.DataFactory/factories | MaxAllowedFactorySizeInGbUnits | Maksymalny dozwolony rozmiar fabryki (jednostka GB) | Liczba | Maksimum
Tak | Microsoft.DataFactory/factories | MaxAllowedResourceCount | Maksymalna dozwolona liczba jednostek | Liczba | Maksimum
Tak | Microsoft.DataFactory/factories | PipelineCancelledRuns | Metryki uruchomień potoku | Liczba | Łącznie
Tak | Microsoft.DataFactory/factories | PipelineFailedRuns | Metryki uruchomionych potoków zakończonych niepowodzeniem | Liczba | Łącznie
Tak | Microsoft.DataFactory/factories | PipelineSucceededRuns | Metryki uruchamiania potoków zakończonych powodzeniem | Liczba | Łącznie
Tak | Microsoft.DataFactory/factories | ResourceCount | Łączna liczba jednostek | Liczba | Maksimum
Tak | Microsoft.DataFactory/factories | TriggerCancelledRuns | Anulowane metryki uruchamiania wyzwalacza | Liczba | Łącznie
Tak | Microsoft.DataFactory/factories | TriggerFailedRuns | Metryki uruchomienia wyzwalacza zakończonego niepowodzeniem | Liczba | Łącznie
Tak | Microsoft.DataFactory/factories | TriggerSucceededRuns | Wyzwalacze uruchomienia wyzwalają metryki | Liczba | Łącznie
Tak | Microsoft.DataLakeAnalytics/accounts | JobAUEndedCancelled | Anulowano czas aktualizacji | Sekundy | Łącznie
Tak | Microsoft.DataLakeAnalytics/accounts | JobAUEndedFailure | Czas niepowodzenia | Sekundy | Łącznie
Tak | Microsoft.DataLakeAnalytics/accounts | JobAUEndedSuccess | Czas pomyślnej aktualizacji | Sekundy | Łącznie
Tak | Microsoft.DataLakeAnalytics/accounts | JobEndedCancelled | Anulowane zadania | Liczba | Łącznie
Tak | Microsoft.DataLakeAnalytics/accounts | JobEndedFailure | Zadania zakończone niepowodzeniem | Liczba | Łącznie
Tak | Microsoft.DataLakeAnalytics/accounts | JobEndedSuccess | Zadania zakończone powodzeniem | Liczba | Łącznie
Tak | Microsoft.DataLakeStore/accounts | Odczyt DataReady | Odczytane dane | Bajty | Łącznie
Tak | Microsoft.DataLakeStore/accounts | Zapisywana | Zapisywane dane | Bajty | Łącznie
Tak | Microsoft.DataLakeStore/accounts | ReadRequests | Żądania odczytu | Liczba | Łącznie
Tak | Microsoft.DataLakeStore/accounts | TotalStorage | Całkowita ilość miejsca do magazynowania | Bajty | Maksimum
Tak | Microsoft.DataLakeStore/accounts | WriteRequests | Żądania zapisu | Liczba | Łącznie
Tak | Microsoft.DBforMariaDB/servers | active_connections | Aktywne połączenia | Liczba | Średnia
Tak | Microsoft.DBforMariaDB/servers | backup_storage_used | Używany magazyn kopii zapasowych | Bajty | Średnia
Tak | Microsoft.DBforMariaDB/servers | connections_failed | Połączenia zakończone niepowodzeniem | Liczba | Łącznie
Tak | Microsoft.DBforMariaDB/servers | cpu_percent | Procent użycia procesora CPU | Procent | Średnia
Tak | Microsoft.DBforMariaDB/servers | io_consumption_percent | Procent operacji we/wy | Procent | Średnia
Tak | Microsoft.DBforMariaDB/servers | memory_percent | Procent pamięci | Procent | Średnia
Tak | Microsoft.DBforMariaDB/servers | network_bytes_egress | Sieć — wyjście | Bajty | Łącznie
Tak | Microsoft.DBforMariaDB/servers | network_bytes_ingress | Sieć — wejście | Bajty | Łącznie
Tak | Microsoft.DBforMariaDB/servers | seconds_behind_master | Opóźnienie replikacji w sekundach | Liczba | Maksimum
Tak | Microsoft.DBforMariaDB/servers | serverlog_storage_limit | Limit magazynowania dziennika serwera | Bajty | Średnia
Tak | Microsoft.DBforMariaDB/servers | serverlog_storage_percent | Procent magazynu dzienników serwera | Procent | Średnia
Tak | Microsoft.DBforMariaDB/servers | serverlog_storage_usage | Używany magazyn dzienników serwera | Bajty | Średnia
Tak | Microsoft.DBforMariaDB/servers | storage_limit | Limit magazynu | Bajty | Maksimum
Tak | Microsoft.DBforMariaDB/servers | storage_percent | Procent magazynu | Procent | Średnia
Tak | Microsoft.DBforMariaDB/servers | storage_used | Użyty magazyn | Bajty | Średnia
Tak | Microsoft.DBforMySQL/servers | active_connections | Aktywne połączenia | Liczba | Średnia
Tak | Microsoft.DBforMySQL/servers | backup_storage_used | Używany magazyn kopii zapasowych | Bajty | Średnia
Tak | Microsoft.DBforMySQL/servers | connections_failed | Połączenia zakończone niepowodzeniem | Liczba | Łącznie
Tak | Microsoft.DBforMySQL/servers | cpu_percent | Procent użycia procesora CPU | Procent | Średnia
Tak | Microsoft.DBforMySQL/servers | io_consumption_percent | Procent operacji we/wy | Procent | Średnia
Tak | Microsoft.DBforMySQL/servers | memory_percent | Procent pamięci | Procent | Średnia
Tak | Microsoft.DBforMySQL/servers | network_bytes_egress | Sieć — wyjście | Bajty | Łącznie
Tak | Microsoft.DBforMySQL/servers | network_bytes_ingress | Sieć — wejście | Bajty | Łącznie
Tak | Microsoft.DBforMySQL/servers | seconds_behind_master | Opóźnienie replikacji w sekundach | Liczba | Maksimum
Tak | Microsoft.DBforMySQL/servers | serverlog_storage_limit | Limit magazynowania dziennika serwera | Bajty | Maksimum
Tak | Microsoft.DBforMySQL/servers | serverlog_storage_percent | Procent magazynu dzienników serwera | Procent | Średnia
Tak | Microsoft.DBforMySQL/servers | serverlog_storage_usage | Używany magazyn dzienników serwera | Bajty | Średnia
Tak | Microsoft.DBforMySQL/servers | storage_limit | Limit magazynu | Bajty | Maksimum
Tak | Microsoft.DBforMySQL/servers | storage_percent | Procent magazynu | Procent | Średnia
Tak | Microsoft.DBforMySQL/servers | storage_used | Użyty magazyn | Bajty | Średnia
Tak | Microsoft.DBforPostgreSQL/servers | active_connections | Aktywne połączenia | Liczba | Średnia
Tak | Microsoft.DBforPostgreSQL/servers | backup_storage_used | Używany magazyn kopii zapasowych | Bajty | Średnia
Tak | Microsoft.DBforPostgreSQL/servers | connections_failed | Połączenia zakończone niepowodzeniem | Liczba | Łącznie
Tak | Microsoft.DBforPostgreSQL/servers | cpu_percent | Procent użycia procesora CPU | Procent | Średnia
Tak | Microsoft.DBforPostgreSQL/servers | io_consumption_percent | Procent operacji we/wy | Procent | Średnia
Tak | Microsoft.DBforPostgreSQL/servers | memory_percent | Procent pamięci | Procent | Średnia
Tak | Microsoft.DBforPostgreSQL/servers | network_bytes_egress | Sieć — wyjście | Bajty | Łącznie
Tak | Microsoft.DBforPostgreSQL/servers | network_bytes_ingress | Sieć — wejście | Bajty | Łącznie
Tak | Microsoft.DBforPostgreSQL/servers | pg_replica_log_delay_in_bytes | Maksymalne opóźnienie między replikami | Bajty | Maksimum
Tak | Microsoft.DBforPostgreSQL/servers | pg_replica_log_delay_in_seconds | Zwłoka repliki | Sekundy | Maksimum
Tak | Microsoft.DBforPostgreSQL/servers | serverlog_storage_limit | Limit magazynowania dziennika serwera | Bajty | Maksimum
Tak | Microsoft.DBforPostgreSQL/servers | serverlog_storage_percent | Procent magazynu dzienników serwera | Procent | Średnia
Tak | Microsoft.DBforPostgreSQL/servers | serverlog_storage_usage | Używany magazyn dzienników serwera | Bajty | Średnia
Tak | Microsoft.DBforPostgreSQL/servers | storage_limit | Limit magazynu | Bajty | Maksimum
Tak | Microsoft.DBforPostgreSQL/servers | storage_percent | Procent magazynu | Procent | Średnia
Tak | Microsoft.DBforPostgreSQL/servers | storage_used | Użyty magazyn | Bajty | Średnia
Tak | Microsoft.DBforPostgreSQL/serversv2 | active_connections | Aktywne połączenia | Liczba | Średnia
Tak | Microsoft.DBforPostgreSQL/serversv2 | cpu_percent | Procent użycia procesora CPU | Procent | Średnia
Tak | Microsoft.DBforPostgreSQL/serversv2 | Wejścia | Operacje wejścia/wyjścia | Liczba | Średnia
Tak | Microsoft.DBforPostgreSQL/serversv2 | memory_percent | Procent pamięci | Procent | Średnia
Tak | Microsoft.DBforPostgreSQL/serversv2 | network_bytes_egress | Sieć — wyjście | Bajty | Łącznie
Tak | Microsoft.DBforPostgreSQL/serversv2 | network_bytes_ingress | Sieć — wejście | Bajty | Łącznie
Tak | Microsoft.DBforPostgreSQL/serversv2 | storage_percent | Procent magazynu | Procent | Średnia
Tak | Microsoft.DBforPostgreSQL/serversv2 | storage_used | Użyty magazyn | Bajty | Średnia
Tak | Microsoft. Devices/konto | digitaltwins. Telemetry. nodes | Symbol zastępczy telemetrii cyfrowego węzła bliźniaczych reprezentacji | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | c2d.commands.egress.abandon.success | Porzucone komunikaty C2D | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | c2d.commands.egress.complete.success | Zakończono dostarczanie komunikatów C2D | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | c2d.commands.egress.reject.success | Odrzucone komunikaty C2D | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | C2D. Methods. Failure | Nieudane wywołania metody bezpośredniej | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | c2d.methods.requestSize | Rozmiar żądania wywołań metody bezpośredniej | Bajty | Średnia
Tak | Microsoft.Devices/IotHubs | c2d.methods.responseSize | Rozmiar odpowiedzi wywołań metody bezpośredniej | Bajty | Średnia
Tak | Microsoft.Devices/IotHubs | c2d.methods.success | Pomyślne wywołania metody bezpośredniej | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | c2d.twin.read.failure | Nieudane odczyty sznurów z zaplecza | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | c2d.twin.read.size | Rozmiar odpowiedzi na odwrocie od zaplecza | Bajty | Średnia
Tak | Microsoft.Devices/IotHubs | c2d.twin.read.success | Pomyślne odczyty sznurów z zaplecza | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | c2d.twin.update.failure | Niepowodzenie aktualizacji bliźniaczych z zaplecza | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | c2d.twin.update.size | Rozmiar aktualizacji przędzy od zaplecza | Bajty | Średnia
Tak | Microsoft.Devices/IotHubs | c2d.twin.update.success | Pomyślne aktualizacje bliźniaczych z zaplecza | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | C2DMessagesExpired | Komunikaty C2D wygasły (wersja zapoznawcza) | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | komputerów | Metryki konfiguracji | Liczba | Łącznie
Nie | Microsoft.Devices/IotHubs | connectedDeviceCount | Podłączone urządzenia (wersja zapoznawcza) | Liczba | Średnia
Tak | Microsoft.Devices/IotHubs | d2c.endpoints.egress.builtIn.events | Routing: komunikaty dostarczane do komunikatów/zdarzeń | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | d2c.endpoints.egress.eventHubs | Routing: komunikaty dostarczane do centrum zdarzeń | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusQueues | Routing: komunikaty dostarczone do kolejki Service Bus | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusTopics | Routing: komunikaty dostarczane do Service Bus tematu | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage | Routing: komunikaty dostarczane do magazynu | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.blobs | Routing: obiekty blob dostarczane do magazynu | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.bytes | Routing: dane dostarczane do magazynu | Bajty | Łącznie
Tak | Microsoft.Devices/IotHubs | d2c.endpoints.latency.builtIn.events | Routing: opóźnienie komunikatów dla komunikatów/zdarzeń | MS | Średnia
Tak | Microsoft.Devices/IotHubs | d2c.endpoints.latency.eventHubs | Routing: opóźnienie komunikatu dla centrum zdarzeń | MS | Średnia
Tak | Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusQueues | Routing: opóźnienie komunikatu dla kolejki Service Bus | MS | Średnia
Tak | Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusTopics | Routing: opóźnienie komunikatu dla Service Bus tematu | MS | Średnia
Tak | Microsoft.Devices/IotHubs | d2c.endpoints.latency.storage | Routing: opóźnienie komunikatu dla magazynu | MS | Średnia
Tak | Microsoft.Devices/IotHubs | d2c.telemetry.egress.dropped | Routing: porzucone komunikaty telemetryczne  | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | d2c.telemetry.egress.fallback | Routing: komunikaty dostarczane do powrotu | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | d2c.telemetry.egress.invalid | Routing: komunikaty telemetryczne są niezgodne | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | d2c.telemetry.egress.orphaned | Routing: oddzielone komunikaty telemetryczne  | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | d2c.telemetry.egress.success | Routing: dostarczono komunikaty telemetryczne | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.allProtocol | Próby wysłania komunikatów telemetrycznych | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.sendThrottle | Liczba błędów ograniczania | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.success | Wysłane komunikaty telemetryczne | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | d2c.twin.read.failure | Nieudane odczyty sznurów z urządzeń | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | d2c.twin.read.size | Rozmiar odpowiedzi dla sznurów odczytanych z urządzeń | Bajty | Średnia
Tak | Microsoft.Devices/IotHubs | d2c.twin.read.success | Pomyślne odczyty sznurów z urządzeń | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | d2c.twin.update.failure | Niepowodzenie aktualizacji bliźniaczych z urządzeń | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | d2c.twin.update.size | Rozmiar aktualizacji bliźniaczych z urządzeń | Bajty | Średnia
Tak | Microsoft.Devices/IotHubs | d2c.twin.update.success | Pomyślne aktualizacje bliźniaczych urządzeń | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | dailyMessageQuotaUsed | Całkowita liczba użytych komunikatów | Liczba | Średnia
Tak | Microsoft.Devices/IotHubs | deviceDataUsage | Całkowite użycie danych urządzenia | Bajty | Łącznie
Tak | Microsoft.Devices/IotHubs | deviceDataUsageV2 | Całkowite użycie danych urządzenia (wersja zapoznawcza) | Bajty | Łącznie
Tak | Microsoft.Devices/IotHubs | devices.connectedDevices.allProtocol | Podłączone urządzenia (przestarzałe)  | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | Devices. totalDevices | Łączna liczba urządzeń (przestarzałe) | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | EventGridDeliveries | Dostawy Event Grid (wersja zapoznawcza) | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | EventGridLatency | Opóźnienie Event Grid (wersja zapoznawcza) | MS | Średnia
Tak | Microsoft.Devices/IotHubs | jobs.cancelJob.failure | Nieudane anulowania zadań | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | jobs.cancelJob.success | Pomyślne anulowania zadań | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | zadania. ukończone | Ukończone zadania | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.failure | Nie można utworzyć zadań wywołania metody | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.success | Pomyślne utworzenie zadań wywołania metody | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.failure | Nie można utworzyć dwuosiowych zadań aktualizacji | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.success | Pomyślne utworzenie dwuosiowych zadań aktualizacji | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | zadania. Niepowodzenie | Zadania zakończone niepowodzeniem | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | jobs.listJobs.failure | Wywołania zakończone niepowodzeniem do listy zadań | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | jobs.listJobs.success | Pomyślne wywołania do zadań na liście | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | jobs.queryJobs.failure | Nieudane kwerendy zadań | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | jobs.queryJobs.success | Pomyślne zapytania dotyczące zadań | Liczba | Łącznie
Nie | Microsoft.Devices/IotHubs | totalDeviceCount | Łączna liczba urządzeń (wersja zapoznawcza) | Liczba | Średnia
Tak | Microsoft.Devices/IotHubs | twinQueries.failure | Niepowodzenie zapytań bliźniaczych | Liczba | Łącznie
Tak | Microsoft.Devices/IotHubs | twinQueries.resultSize | Rozmiar wyniku zapytań bliźniaczych | Bajty | Średnia
Tak | Microsoft.Devices/IotHubs | twinQueries.success | Pomyślne zapytania bliźniaczy | Liczba | Łącznie
Tak | Microsoft.Devices/provisioningServices | AttestationAttempts | Próby zaświadczania | Liczba | Łącznie
Tak | Microsoft.Devices/provisioningServices | DeviceAssignments | Przypisane urządzenia | Liczba | Łącznie
Tak | Microsoft.Devices/provisioningServices | RegistrationAttempts | Próby rejestracji | Liczba | Łącznie
Nie | Microsoft.DocumentDB/databaseAccounts | AvailableStorage | Dostępny magazyn | Bajty | Łącznie
Nie | Microsoft.DocumentDB/databaseAccounts | CassandraConnectionClosures | Zamknięcia połączeń Cassandra | Liczba | Łącznie
Nie | Microsoft.DocumentDB/databaseAccounts | CassandraRequestCharges | Opłaty za żądania Cassandra | Liczba | Łącznie
Nie | Microsoft.DocumentDB/databaseAccounts | CassandraRequests | Żądania Cassandra | Liczba | Liczba
Nie | Microsoft.DocumentDB/databaseAccounts | Datausage | Użycie danych | Bajty | Łącznie
Tak | Microsoft.DocumentDB/databaseAccounts | DeleteVirtualNetwork | DeleteVirtualNetwork | Liczba | Liczba
Nie | Microsoft.DocumentDB/databaseAccounts | DocumentCount | Liczba dokumentów | Liczba | Łącznie
Nie | Microsoft.DocumentDB/databaseAccounts | DocumentQuota | Przydział dokumentu | Bajty | Łącznie
Nie | Microsoft.DocumentDB/databaseAccounts | IndexUsage | Użycie indeksu | Bajty | Łącznie
Nie | Microsoft.DocumentDB/databaseAccounts | MetadataRequests | Żądania metadanych | Liczba | Liczba
Tak | Microsoft.DocumentDB/databaseAccounts | MongoRequestCharge | Opłata żądania Mongo | Liczba | Łącznie
Tak | Microsoft.DocumentDB/databaseAccounts | MongoRequests | Żądania Mongo | Liczba | Liczba
Nie | Microsoft.DocumentDB/databaseAccounts | MongoRequestsCount | Częstotliwość żądania Mongo | CountPerSecond | Średnia
Nie | Microsoft.DocumentDB/databaseAccounts | MongoRequestsDelete | Mongo — wskaźnik żądania usunięcia | CountPerSecond | Średnia
Nie | Microsoft.DocumentDB/databaseAccounts | MongoRequestsInsert | Liczba żądań wstawienia Mongo | CountPerSecond | Średnia
Nie | Microsoft.DocumentDB/databaseAccounts | MongoRequestsQuery | Częstotliwość żądań zapytań Mongo | CountPerSecond | Średnia
Nie | Microsoft.DocumentDB/databaseAccounts | MongoRequestsUpdate | Częstotliwość żądań aktualizacji Mongo | CountPerSecond | Średnia
Nie | Microsoft.DocumentDB/databaseAccounts | ProvisionedThroughput | Aprowizowana przepływność | Liczba | Maksimum
Tak | Microsoft.DocumentDB/databaseAccounts | ReplicationLatency | Opóźnienie replikacji poziomie P99 | MilliSeconds | Średnia
Nie | Microsoft.DocumentDB/databaseAccounts | Dostępność | Dostępność usługi | Procent | Średnia
Tak | Microsoft.DocumentDB/databaseAccounts | TotalRequests | Łączna liczba żądań | Liczba | Liczba
Tak | Microsoft.DocumentDB/databaseAccounts | TotalRequestUnits | Łączna liczba jednostek żądania | Liczba | Łącznie
Nie | Microsoft. EnterpriseKnowledgeGraph/usługi | FailureCount | Liczba błędów | Liczba | Liczba
Nie | Microsoft. EnterpriseKnowledgeGraph/usługi | SuccessCount | Liczba sukcesów | Liczba | Liczba
Nie | Microsoft. EnterpriseKnowledgeGraph/usługi | SuccessLatency | Opóźnienie sukcesu | MilliSeconds | Średnia
Nie | Microsoft. EnterpriseKnowledgeGraph/usługi | TransactionCount | Liczba transakcji | Liczba | Liczba
Tak | Microsoft. EventGrid/domeny | DeadLetteredCount | Zdarzenia utraconych wiadomości | Liczba | Łącznie
Nie | Microsoft. EventGrid/domeny | DeliveryAttemptFailCount | Zdarzenia zakończonych niepowodzeniem | Liczba | Łącznie
Tak | Microsoft. EventGrid/domeny | DeliverySuccessCount | Dostarczone zdarzenia | Liczba | Łącznie
Nie | Microsoft. EventGrid/domeny | DestinationProcessingDurationInMs | Czas przetwarzania docelowego | MS | Średnia
Tak | Microsoft. EventGrid/domeny | DroppedEventCount | Opuszczone zdarzenia | Liczba | Łącznie
Tak | Microsoft. EventGrid/domeny | MatchedEventCount | Dopasowane zdarzenia | Liczba | Łącznie
Tak | Microsoft. EventGrid/domeny | PublishFailCount | Publikowanie zdarzeń zakończonych niepowodzeniem | Liczba | Łącznie
Tak | Microsoft. EventGrid/domeny | PublishSuccessCount | Zdarzenia opublikowane | Liczba | Łącznie
Tak | Microsoft. EventGrid/domeny | PublishSuccessLatencyInMs | Czas oczekiwania na pomyślne publikowanie | Liczba | Łącznie
Tak | Microsoft.EventGrid/eventSubscriptions | DeadLetteredCount | Zdarzenia utraconych wiadomości | Liczba | Łącznie
Nie | Microsoft.EventGrid/eventSubscriptions | DeliveryAttemptFailCount | Zdarzenia zakończonych niepowodzeniem | Liczba | Łącznie
Tak | Microsoft.EventGrid/eventSubscriptions | DeliverySuccessCount | Dostarczone zdarzenia | Liczba | Łącznie
Nie | Microsoft.EventGrid/eventSubscriptions | DestinationProcessingDurationInMs | Czas przetwarzania docelowego | MS | Średnia
Tak | Microsoft.EventGrid/eventSubscriptions | DroppedEventCount | Opuszczone zdarzenia | Liczba | Łącznie
Tak | Microsoft.EventGrid/eventSubscriptions | MatchedEventCount | Dopasowane zdarzenia | Liczba | Łącznie
Tak | Microsoft. EventGrid/extensionTopics | PublishFailCount | Publikowanie zdarzeń zakończonych niepowodzeniem | Liczba | Łącznie
Tak | Microsoft. EventGrid/extensionTopics | PublishSuccessCount | Zdarzenia opublikowane | Liczba | Łącznie
Tak | Microsoft. EventGrid/extensionTopics | PublishSuccessLatencyInMs | Czas oczekiwania na pomyślne publikowanie | Liczba | Łącznie
Tak | Microsoft. EventGrid/extensionTopics | UnmatchedEventCount | Niedopasowane zdarzenia | Liczba | Łącznie
Tak | Microsoft. EventGrid/tematy | PublishFailCount | Publikowanie zdarzeń zakończonych niepowodzeniem | Liczba | Łącznie
Tak | Microsoft. EventGrid/tematy | PublishSuccessCount | Zdarzenia opublikowane | Liczba | Łącznie
Tak | Microsoft. EventGrid/tematy | PublishSuccessLatencyInMs | Czas oczekiwania na pomyślne publikowanie | Liczba | Łącznie
Tak | Microsoft. EventGrid/tematy | UnmatchedEventCount | Niedopasowane zdarzenia | Liczba | Łącznie
Nie | Microsoft. EventHub/klastry | Połączeń ActiveConnections | Połączeń ActiveConnections | Liczba | Średnia
Nie | Microsoft. EventHub/klastry | AvailableMemory | Dostępna pamięć | Procent | Maksimum
Nie | Microsoft. EventHub/klastry | CaptureBacklog | Zaległości przechwytywania. | Liczba | Łącznie
Nie | Microsoft. EventHub/klastry | CapturedBytes | Przechwycone bajty. | Bajty | Łącznie
Nie | Microsoft. EventHub/klastry | CapturedMessages | Przechwycone komunikaty. | Liczba | Łącznie
Nie | Microsoft. EventHub/klastry | Połączenia connectionsclosed | Połączenia zamknięte. | Liczba | Średnia
Nie | Microsoft. EventHub/klastry | Połączenia connectionsopened | Otwarte połączenia. | Liczba | Średnia
Nie | Microsoft. EventHub/klastry | Procesor CPU | Procesor CPU | Procent | Maksimum
Tak | Microsoft. EventHub/klastry | Bajty incomingbytes | Bajty przychodzące. | Bajty | Łącznie
Tak | Microsoft. EventHub/klastry | Komunikaty incomingmessages | Komunikaty przychodzące | Liczba | Łącznie
Tak | Microsoft. EventHub/klastry | Żądania incomingrequests | Żądania przychodzące | Liczba | Łącznie
Tak | Microsoft. EventHub/klastry | Bajty outgoingbytes | Bajty wychodzące. | Bajty | Łącznie
Tak | Microsoft. EventHub/klastry | Komunikaty outgoingmessages | Komunikaty wychodzące | Liczba | Łącznie
Nie | Microsoft. EventHub/klastry | Błędy quotaexceedederrors | Błędy przekroczenia limitu przydziału. | Liczba | Łącznie
Nie | Microsoft. EventHub/klastry | Błędy servererrors | Błędy serwera. | Liczba | Łącznie
Nie | Microsoft. EventHub/klastry | Żądania successfulrequests | Żądania zakończone powodzeniem | Liczba | Łącznie
Nie | Microsoft. EventHub/klastry | ThrottledRequests | Żądania z ograniczeniami. | Liczba | Łącznie
Nie | Microsoft. EventHub/klastry | Błędy usererrors | Błędy użytkownika. | Liczba | Łącznie
Nie | Microsoft.EventHub/namespaces | Połączeń ActiveConnections | Połączeń ActiveConnections | Liczba | Średnia
Nie | Microsoft.EventHub/namespaces | CaptureBacklog | Zaległości przechwytywania. | Liczba | Łącznie
Nie | Microsoft.EventHub/namespaces | CapturedBytes | Przechwycone bajty. | Bajty | Łącznie
Nie | Microsoft.EventHub/namespaces | CapturedMessages | Przechwycone komunikaty. | Liczba | Łącznie
Nie | Microsoft.EventHub/namespaces | Połączenia connectionsclosed | Połączenia zamknięte. | Liczba | Średnia
Nie | Microsoft.EventHub/namespaces | Połączenia connectionsopened | Otwarte połączenia. | Liczba | Średnia
Tak | Microsoft.EventHub/namespaces | EHABL | Archiwum komunikatów zaległości (przestarzałe) | Liczba | Łącznie
Tak | Microsoft.EventHub/namespaces | EHAMBS | Przepływność komunikatów archiwalnych (przestarzałe) | Bajty | Łącznie
Tak | Microsoft.EventHub/namespaces | EHAMSGS | Komunikaty archiwalne (przestarzałe) | Liczba | Łącznie
Tak | Microsoft.EventHub/namespaces | EHINBYTES | Przychodzące bajty (przestarzałe) | Bajty | Łącznie
Tak | Microsoft.EventHub/namespaces | EHINMBS | Przychodzące bajty (przestarzałe) (przestarzałe) | Bajty | Łącznie
Tak | Microsoft.EventHub/namespaces | EHINMSGS | Komunikaty przychodzące (przestarzałe) | Liczba | Łącznie
Tak | Microsoft.EventHub/namespaces | EHOUTBYTES | Bajty wychodzące (przestarzałe) | Bajty | Łącznie
Tak | Microsoft.EventHub/namespaces | EHOUTMBS | Bajty wychodzące (przestarzałe) (przestarzałe) | Bajty | Łącznie
Tak | Microsoft.EventHub/namespaces | EHOUTMSGS | Wiadomości wychodzące (przestarzałe) | Liczba | Łącznie
Tak | Microsoft.EventHub/namespaces | FAILREQ | Nieudane żądania (przestarzałe) | Liczba | Łącznie
Tak | Microsoft.EventHub/namespaces | Bajty incomingbytes | Bajty przychodzące. | Bajty | Łącznie
Tak | Microsoft.EventHub/namespaces | Komunikaty incomingmessages | Komunikaty przychodzące | Liczba | Łącznie
Tak | Microsoft.EventHub/namespaces | Żądania incomingrequests | Żądania przychodzące | Liczba | Łącznie
Tak | Microsoft.EventHub/namespaces | INMSGS | Komunikaty przychodzące (przestarzałe) (przestarzałe) | Liczba | Łącznie
Tak | Microsoft.EventHub/namespaces | INREQS | Żądania przychodzące (przestarzałe) | Liczba | Łącznie
Tak | Microsoft.EventHub/namespaces | MIĘDZY | Wewnętrzne błędy serwera (przestarzałe) | Liczba | Łącznie
Tak | Microsoft.EventHub/namespaces | MISCERR | Inne błędy (przestarzałe) | Liczba | Łącznie
Tak | Microsoft.EventHub/namespaces | Bajty outgoingbytes | Bajty wychodzące. | Bajty | Łącznie
Tak | Microsoft.EventHub/namespaces | Komunikaty outgoingmessages | Komunikaty wychodzące | Liczba | Łącznie
Tak | Microsoft.EventHub/namespaces | OUTMSGS | Wiadomości wychodzące (przestarzałe) (przestarzałe) | Liczba | Łącznie
Nie | Microsoft.EventHub/namespaces | Błędy quotaexceedederrors | Błędy przekroczenia limitu przydziału. | Liczba | Łącznie
Nie | Microsoft.EventHub/namespaces | Błędy servererrors | Błędy serwera. | Liczba | Łącznie
Nie | Microsoft.EventHub/namespaces | Rozmiar | Rozmiar | Bajty | Średnia
Nie | Microsoft.EventHub/namespaces | Żądania successfulrequests | Żądania zakończone powodzeniem | Liczba | Łącznie
Tak | Microsoft.EventHub/namespaces | SUCCREQ | Żądania zakończone powodzeniem (przestarzałe) | Liczba | Łącznie
Tak | Microsoft.EventHub/namespaces | SVRBSY | Błędy zajęte przez serwer (przestarzałe) | Liczba | Łącznie
Nie | Microsoft.EventHub/namespaces | ThrottledRequests | Żądania z ograniczeniami. | Liczba | Łącznie
Nie | Microsoft.EventHub/namespaces | Błędy usererrors | Błędy użytkownika. | Liczba | Łącznie
Tak | Microsoft.HDInsight/clusters | CategorizedGatewayRequests | Skategoryzowane żądania bramy | Liczba | Łącznie
Tak | Microsoft.HDInsight/clusters | GatewayRequests | Żądania bramy | Liczba | Łącznie
Tak | Microsoft.HDInsight/clusters | NumActiveWorkers | Liczba aktywnych procesów roboczych | Liczba | Maksimum
Tak | Microsoft.HDInsight/clusters | ScalingRequests | Skalowanie żądań | Liczba | Maksimum
Tak | Microsoft.Insights/AutoscaleSettings | MetricThreshold | Próg metryki | Liczba | Średnia
Tak | Microsoft.Insights/AutoscaleSettings | ObservedCapacity | Zaobserwowana pojemność | Liczba | Średnia
Tak | Microsoft.Insights/AutoscaleSettings | ObservedMetricValue | Obserwowana wartość metryki | Liczba | Średnia
Tak | Microsoft.Insights/AutoscaleSettings | ScaleActionsInitiated | Zainicjowane akcje skalowania | Liczba | Łącznie
Tak | Microsoft. Insights/składniki | availabilityResults/availabilityPercentage | Dostępność | Procent | Średnia
Nie | Microsoft. Insights/składniki | availabilityResults/liczba | Testy dostępności | Liczba | Liczba
Tak | Microsoft. Insights/składniki | availabilityResults/czas trwania | Czas trwania testu dostępności | MilliSeconds | Średnia
Tak | Microsoft. Insights/składniki | browserTimings/networkDuration | Czas połączenia sieciowego ładowania strony | MilliSeconds | Średnia
Tak | Microsoft. Insights/składniki | browserTimings/processingDuration | Czas przetwarzania klienta | MilliSeconds | Średnia
Tak | Microsoft. Insights/składniki | browserTimings/receiveDuration | Czas odpowiedzi na odebranie | MilliSeconds | Średnia
Tak | Microsoft. Insights/składniki | browserTimings/sendDuration | Czas żądania wysłania | MilliSeconds | Średnia
Tak | Microsoft. Insights/składniki | browserTimings/totalDuration | Czas ładowania strony w przeglądarce | MilliSeconds | Średnia
Nie | Microsoft. Insights/składniki | zależności/liczba | Wywołania zależności | Liczba | Liczba
Tak | Microsoft. Insights/składniki | zależności/czas trwania | Czas trwania zależności | MilliSeconds | Średnia
Nie | Microsoft. Insights/składniki | zależności/niepowodzenie | Błędy wywołań zależności | Liczba | Liczba
Nie | Microsoft. Insights/składniki | wyjątki/przeglądarka | Wyjątki przeglądarki | Liczba | Liczba
Tak | Microsoft. Insights/składniki | wyjątki/liczba | Wyjątki | Liczba | Liczba
Nie | Microsoft. Insights/składniki | wyjątki/serwer | Wyjątki serwera | Liczba | Liczba
Tak | Microsoft. Insights/składniki | pageViews/liczba | Wyświetlenia stron | Liczba | Liczba
Tak | Microsoft. Insights/składniki | pageViews/czas trwania | Czas ładowania widoku strony | MilliSeconds | Średnia
Tak | Microsoft. Insights/składniki | Liczniki wydajności/exceptionsPerSecond | Częstotliwość wyjątków | CountPerSecond | Średnia
Tak | Microsoft. Insights/składniki | Liczniki wydajności/memoryAvailableBytes | Dostępna pamięć | Bajty | Średnia
Tak | Microsoft. Insights/składniki | Liczniki wydajności/processCpuPercentage | Procesor CPU procesu | Procent | Średnia
Tak | Microsoft. Insights/składniki | Liczniki wydajności/processIOBytesPerSecond | Szybkość operacji we/wy procesu | BytesPerSecond | Średnia
Tak | Microsoft. Insights/składniki | Liczniki wydajności/processorCpuPercentage | Czas procesora | Procent | Średnia
Tak | Microsoft. Insights/składniki | Liczniki wydajności/processPrivateBytes | Prywatne bajty procesu | Bajty | Średnia
Tak | Microsoft. Insights/składniki | Liczniki wydajności/requestExecutionTime | Czas wykonywania żądania HTTP | MilliSeconds | Średnia
Tak | Microsoft. Insights/składniki | performanceCounters/requestsInQueue | Żądania HTTP w kolejce aplikacji | Liczba | Średnia
Tak | Microsoft. Insights/składniki | performanceCounters/requestsPerSecond | Częstotliwość żądań HTTP | CountPerSecond | Średnia
Nie | Microsoft. Insights/składniki | Liczba żądań na sekundę | Żądania serwera | Liczba | Liczba
Tak | Microsoft. Insights/składniki | żądania/czas trwania | Czas odpowiedzi serwera | MilliSeconds | Średnia
Nie | Microsoft. Insights/składniki | żądania/niepowodzenie | Żądania zakończone niepowodzeniem | Liczba | Liczba
Nie | Microsoft. Insights/składniki | żądania/częstotliwość | Liczba żądań serwera | CountPerSecond | Średnia
Tak | Microsoft. Insights/składniki | ślady/liczba | Ścieżki | Liczba | Liczba
Tak | Microsoft.KeyVault/vaults | ServiceApiHit | Całkowita liczba trafień interfejsu API usługi | Liczba | Liczba
Tak | Microsoft.KeyVault/vaults | ServiceApiLatency | Ogólne opóźnienie interfejsu API usługi | MS | Średnia
Tak | Microsoft.KeyVault/vaults | ServiceApiResult | Łączna liczba wyników interfejsu API usługi | Liczba | Liczba
Tak | Microsoft.Kusto/Clusters | CacheUtilization | Użycie pamięci podręcznej | Procent | Średnia
Tak | Microsoft.Kusto/Clusters | ContinuousExportMaxLatenessMinutes | Maksymalna liczba minut opóźnienia eksportu | Liczba | Maksimum
Tak | Microsoft.Kusto/Clusters | ContinuousExportNumOfRecordsExported | Eksport ciągły — liczba eksportowanych rekordów | Liczba | Łącznie
Tak | Microsoft.Kusto/Clusters | ContinuousExportPendingCount | Liczba oczekujących eksportu ciągłego | Liczba | Maksimum
Tak | Microsoft.Kusto/Clusters | ContinuousExportResult | Wynik eksportu ciągłego | Liczba | Liczba
Tak | Microsoft.Kusto/Clusters | Procesor CPU | Procesor CPU | Procent | Średnia
Tak | Microsoft.Kusto/Clusters | EventsProcessedForEventHubs | Zdarzenia przetwarzane (dla centrów zdarzeń/IoT) | Liczba | Łącznie
Tak | Microsoft.Kusto/Clusters | ExportUtilization | Użycie eksportu | Procent | Maksimum
Tak | Microsoft.Kusto/Clusters | IngestionLatencyInSeconds | Opóźnienie pozyskiwania (w sekundach) | Sekundy | Średnia
Tak | Microsoft.Kusto/Clusters | IngestionResult | Wynik pozyskiwania | Liczba | Liczba
Tak | Microsoft.Kusto/Clusters | IngestionUtilization | Wykorzystanie pozyskiwania | Procent | Średnia
Tak | Microsoft.Kusto/Clusters | IngestionVolumeInMB | Wolumin pozyskiwania (w MB) | Liczba | Łącznie
Tak | Microsoft.Kusto/Clusters | KeepAlive | Utrzymywanie aktywności | Liczba | Średnia
Tak | Microsoft.Kusto/Clusters | QueryDuration | Czas trwania zapytania | MS | Średnia
Tak | Microsoft.Kusto/Clusters | SteamingIngestRequestRate | Szybkość żądania pozyskiwania strumieniowego | Liczba | RateRequestsPerSecond
Tak | Microsoft.Kusto/Clusters | StreamingIngestDataRate | Szybkość danych pozyskiwania przesyłania strumieniowego | Liczba | Średnia
Tak | Microsoft.Kusto/Clusters | StreamingIngestDuration | Czas trwania pozyskiwania strumieniowego | MS | Średnia
Tak | Microsoft.Kusto/Clusters | StreamingIngestResults | Wynik pozyskiwania strumieniowego | Liczba | Średnia
Tak | Microsoft.Logic/integrationServiceEnvironments | ActionLatency | Opóźnienie akcji  | Sekundy | Średnia
Tak | Microsoft.Logic/integrationServiceEnvironments | ActionsCompleted | Wykonane akcje  | Liczba | Łącznie
Tak | Microsoft.Logic/integrationServiceEnvironments | ActionsFailed | Akcje zakończone niepowodzeniem  | Liczba | Łącznie
Tak | Microsoft.Logic/integrationServiceEnvironments | ActionsSkipped | Pominięte akcje  | Liczba | Łącznie
Tak | Microsoft.Logic/integrationServiceEnvironments | ActionsStarted | Akcje uruchomione  | Liczba | Łącznie
Tak | Microsoft.Logic/integrationServiceEnvironments | ActionsSucceeded | Akcje zakończone powodzeniem  | Liczba | Łącznie
Tak | Microsoft.Logic/integrationServiceEnvironments | ActionSuccessLatency | Opóźnienie sukcesu akcji  | Sekundy | Średnia
Tak | Microsoft.Logic/integrationServiceEnvironments | ActionThrottledEvents | Zdarzenia ograniczające akcję | Liczba | Łącznie
Tak | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentConnectorMemoryUsage | Użycie pamięci przez łącznik dla środowisko usługi integracji | Procent | Średnia
Tak | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentConnectorProcessorUsage | Użycie procesora przez łącznik dla środowisko usługi integracji | Procent | Średnia
Tak | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentWorkflowMemoryUsage | Użycie pamięci przez przepływ pracy dla środowisko usługi integracji | Procent | Średnia
Tak | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentWorkflowProcessorUsage | Użycie procesora przepływu pracy dla środowisko usługi integracji | Procent | Średnia
Tak | Microsoft.Logic/integrationServiceEnvironments | RunFailurePercentage | Procent niepowodzeń przebiegu | Procent | Łącznie
Tak | Microsoft.Logic/integrationServiceEnvironments | RunLatency | Opóźnienie uruchamiania | Sekundy | Średnia
Tak | Microsoft.Logic/integrationServiceEnvironments | RunsCancelled | Przebiegi anulowane | Liczba | Łącznie
Tak | Microsoft.Logic/integrationServiceEnvironments | RunsCompleted | Przebiegi zakończone | Liczba | Łącznie
Tak | Microsoft.Logic/integrationServiceEnvironments | RunsFailed | Przebiegi zakończone niepowodzeniem | Liczba | Łącznie
Tak | Microsoft.Logic/integrationServiceEnvironments | RunsStarted | Uruchomienia uruchomione | Liczba | Łącznie
Tak | Microsoft.Logic/integrationServiceEnvironments | RunsSucceeded | Przebiegi zakończone powodzeniem | Liczba | Łącznie
Tak | Microsoft.Logic/integrationServiceEnvironments | RunStartThrottledEvents | Uruchom zdarzenia uruchamiania z ograniczeniami | Liczba | Łącznie
Tak | Microsoft.Logic/integrationServiceEnvironments | RunSuccessLatency | Opóźnienie sukcesu przebiegu | Sekundy | Średnia
Tak | Microsoft.Logic/integrationServiceEnvironments | RunThrottledEvents | Uruchamianie zdarzeń z ograniczeniami | Liczba | Łącznie
Tak | Microsoft.Logic/integrationServiceEnvironments | TriggerFireLatency | Opóźnienie wyzwalania wyzwalacza  | Sekundy | Średnia
Tak | Microsoft.Logic/integrationServiceEnvironments | TriggerLatency | Opóźnienie wyzwalacza  | Sekundy | Średnia
Tak | Microsoft.Logic/integrationServiceEnvironments | TriggersCompleted | Ukończone wyzwalacze  | Liczba | Łącznie
Tak | Microsoft.Logic/integrationServiceEnvironments | TriggersFailed | Wyzwalacze zakończone niepowodzeniem  | Liczba | Łącznie
Tak | Microsoft.Logic/integrationServiceEnvironments | TriggersFired | Wyzwolone wyzwalacze  | Liczba | Łącznie
Tak | Microsoft.Logic/integrationServiceEnvironments | TriggersSkipped | Pominięte wyzwalacze | Liczba | Łącznie
Tak | Microsoft.Logic/integrationServiceEnvironments | TriggersStarted | Uruchomiono wyzwalacze  | Liczba | Łącznie
Tak | Microsoft.Logic/integrationServiceEnvironments | TriggersSucceeded | Wyzwalacze zakończone powodzeniem  | Liczba | Łącznie
Tak | Microsoft.Logic/integrationServiceEnvironments | TriggerSuccessLatency | Opóźnienie sukcesu wyzwalacza  | Sekundy | Średnia
Tak | Microsoft.Logic/integrationServiceEnvironments | TriggerThrottledEvents | Wyzwalaj zdarzenia ograniczające | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | ActionLatency | Opóźnienie akcji  | Sekundy | Średnia
Tak | Microsoft.Logic/workflows | ActionsCompleted | Wykonane akcje  | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | ActionsFailed | Akcje zakończone niepowodzeniem  | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | ActionsSkipped | Pominięte akcje  | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | ActionsStarted | Akcje uruchomione  | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | ActionsSucceeded | Akcje zakończone powodzeniem  | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | ActionSuccessLatency | Opóźnienie sukcesu akcji  | Sekundy | Średnia
Tak | Microsoft.Logic/workflows | ActionThrottledEvents | Zdarzenia ograniczające akcję | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | BillableActionExecutions | Rozliczane wykonania akcji | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | BillableTriggerExecutions | Rozliczane wykonania wyzwalacza | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | BillingUsageNativeOperation | Użycie rozliczeń dla natywnych wykonań operacji | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | BillingUsageNativeOperation | Użycie rozliczeń dla natywnych wykonań operacji | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | BillingUsageStandardConnector | Użycie rozliczeń dla wykonywania łączników standardowych | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | BillingUsageStandardConnector | Użycie rozliczeń dla wykonywania łączników standardowych | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | BillingUsageStorageConsumption | Użycie rozliczeń dla wykonań zużycia magazynu | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | BillingUsageStorageConsumption | Użycie rozliczeń dla wykonań zużycia magazynu | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | RunFailurePercentage | Procent niepowodzeń przebiegu | Procent | Łącznie
Tak | Microsoft.Logic/workflows | RunLatency | Opóźnienie uruchamiania | Sekundy | Średnia
Tak | Microsoft.Logic/workflows | RunsCancelled | Przebiegi anulowane | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | RunsCompleted | Przebiegi zakończone | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | RunsFailed | Przebiegi zakończone niepowodzeniem | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | RunsStarted | Uruchomienia uruchomione | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | RunsSucceeded | Przebiegi zakończone powodzeniem | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | RunStartThrottledEvents | Uruchom zdarzenia uruchamiania z ograniczeniami | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | RunSuccessLatency | Opóźnienie sukcesu przebiegu | Sekundy | Średnia
Tak | Microsoft.Logic/workflows | RunThrottledEvents | Uruchamianie zdarzeń z ograniczeniami | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | TotalBillableExecutions | Łączna Liczba wykonań rozliczanych | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | TriggerFireLatency | Opóźnienie wyzwalania wyzwalacza  | Sekundy | Średnia
Tak | Microsoft.Logic/workflows | TriggerLatency | Opóźnienie wyzwalacza  | Sekundy | Średnia
Tak | Microsoft.Logic/workflows | TriggersCompleted | Ukończone wyzwalacze  | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | TriggersFailed | Wyzwalacze zakończone niepowodzeniem  | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | TriggersFired | Wyzwolone wyzwalacze  | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | TriggersSkipped | Pominięte wyzwalacze | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | TriggersStarted | Uruchomiono wyzwalacze  | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | TriggersSucceeded | Wyzwalacze zakończone powodzeniem  | Liczba | Łącznie
Tak | Microsoft.Logic/workflows | TriggerSuccessLatency | Opóźnienie sukcesu wyzwalacza  | Sekundy | Średnia
Tak | Microsoft.Logic/workflows | TriggerThrottledEvents | Wyzwalaj zdarzenia ograniczające | Liczba | Łącznie
Tak | Microsoft.MachineLearningServices/workspaces | Aktywne rdzenie | Aktywne rdzenie | Liczba | Średnia
Tak | Microsoft.MachineLearningServices/workspaces | Aktywne węzły | Aktywne węzły | Liczba | Średnia
Tak | Microsoft.MachineLearningServices/workspaces | Ukończone uruchomienia | Ukończone uruchomienia | Liczba | Łącznie
Tak | Microsoft.MachineLearningServices/workspaces | Nieudane uruchomienia | Nieudane uruchomienia | Liczba | Łącznie
Tak | Microsoft.MachineLearningServices/workspaces | Rdzenie bezczynne | Rdzenie bezczynne | Liczba | Średnia
Tak | Microsoft.MachineLearningServices/workspaces | Węzły bezczynne | Węzły bezczynne | Liczba | Średnia
Tak | Microsoft.MachineLearningServices/workspaces | Opuszczanie rdzeni | Opuszczanie rdzeni | Liczba | Średnia
Tak | Microsoft.MachineLearningServices/workspaces | Opuszczanie węzłów | Opuszczanie węzłów | Liczba | Średnia
Tak | Microsoft.MachineLearningServices/workspaces | Wdrażanie modelu nie powiodło się | Wdrażanie modelu nie powiodło się | Liczba | Łącznie
Tak | Microsoft.MachineLearningServices/workspaces | Uruchomiono Wdrażanie modelu | Uruchomiono Wdrażanie modelu | Liczba | Łącznie
Tak | Microsoft.MachineLearningServices/workspaces | Wdrażanie modelu powiodło się | Wdrażanie modelu powiodło się | Liczba | Łącznie
Tak | Microsoft.MachineLearningServices/workspaces | Nie można zarejestrować modelu | Nie można zarejestrować modelu | Liczba | Łącznie
Tak | Microsoft.MachineLearningServices/workspaces | Rejestrowanie modelu powiodło się | Rejestrowanie modelu powiodło się | Liczba | Łącznie
Tak | Microsoft.MachineLearningServices/workspaces | Występujące rdzenie | Występujące rdzenie | Liczba | Średnia
Tak | Microsoft.MachineLearningServices/workspaces | Zastępujące węzły | Zastępujące węzły | Liczba | Średnia
Tak | Microsoft.MachineLearningServices/workspaces | Procent wykorzystania przydziałów | Procent wykorzystania przydziałów | Liczba | Średnia
Tak | Microsoft.MachineLearningServices/workspaces | Uruchomione uruchomienia | Uruchomione uruchomienia | Liczba | Łącznie
Tak | Microsoft.MachineLearningServices/workspaces | Łączna liczba rdzeni | Łączna liczba rdzeni | Liczba | Średnia
Tak | Microsoft.MachineLearningServices/workspaces | Łączna liczba węzłów | Łączna liczba węzłów | Liczba | Średnia
Tak | Microsoft.MachineLearningServices/workspaces | Rdzenie, których nie można używać | Rdzenie, których nie można używać | Liczba | Średnia
Tak | Microsoft.MachineLearningServices/workspaces | Węzły niezdatne do użytku | Węzły niezdatne do użytku | Liczba | Średnia
Tak | Microsoft. Maps/konta | Dostępność | Dostępność | Procent | Średnia
Nie | Microsoft. Maps/konta | Użycie | Użycie | Liczba | Liczba
Tak | Microsoft. Media/MediaServices | AssetCount | Liczba zasobów | Liczba | Średnia
Tak | Microsoft. Media/MediaServices | AssetQuota | Przydział zasobów | Liczba | Średnia
Tak | Microsoft. Media/MediaServices | AssetQuotaUsedPercentage | Procent użycia przydziału zasobów | Procent | Średnia
Tak | Microsoft. Media/MediaServices | ContentKeyPolicyCount | Liczba zasad dotyczących kluczy zawartości | Liczba | Średnia
Tak | Microsoft. Media/MediaServices | ContentKeyPolicyQuota | Przydział zasad dotyczących kluczy zawartości | Liczba | Średnia
Tak | Microsoft. Media/MediaServices | ContentKeyPolicyQuotaUsedPercentage | Procent użycia przydziału zasad klucza zawartości | Procent | Średnia
Tak | Microsoft. Media/MediaServices | StreamingPolicyCount | Liczba zasad przesyłania strumieniowego | Liczba | Średnia
Tak | Microsoft. Media/MediaServices | StreamingPolicyQuota | Limit przydziału zasad przesyłania strumieniowego | Liczba | Średnia
Tak | Microsoft. Media/MediaServices | StreamingPolicyQuotaUsedPercentage | Procent użycia limitu przydziału zasad przesyłania strumieniowego | Procent | Średnia
Tak | Microsoft. Media/MediaServices/streamingEndpoints | Ruch wychodzący | Ruch wychodzący | Bajty | Łącznie
Tak | Microsoft. Media/MediaServices/streamingEndpoints | Żądania | Żądania | Liczba | Łącznie
Tak | Microsoft. Media/MediaServices/streamingEndpoints | SuccessE2ELatency | Pomyślne zakończenie oczekiwania | MS | Średnia
Tak | Microsoft. Microservices4Spring/appClusters | GCPauseTotalCount | Liczba wstrzymań GC | Liczba | Łącznie
Tak | Microsoft. Microservices4Spring/appClusters | GCPauseTotalTime | Całkowity czas wstrzymania odzyskiwania pamięci | MS | Łącznie
Tak | Microsoft. Microservices4Spring/appClusters | MaxOldGenMemoryPoolBytes | Maksymalna dostępna wartość starego rozmiaru danych generacji | Bajty | Średnia
Tak | Microsoft. Microservices4Spring/appClusters | OldGenMemoryPoolBytes | Stary rozmiar danych generacji | Bajty | Średnia
Tak | Microsoft. Microservices4Spring/appClusters | OldGenPromotedBytes | Podwyższ poziom do starego rozmiaru danych generacji | Bajty | Maksimum
Tak | Microsoft. Microservices4Spring/appClusters | ServiceCpuUsagePercentage | Procent użycia procesora usługi | Procent | Średnia
Tak | Microsoft. Microservices4Spring/appClusters | ServiceMemoryCommitted | Przypisano pamięć usługi | Bajty | Średnia
Tak | Microsoft. Microservices4Spring/appClusters | ServiceMemoryMax | Maksymalna ilość pamięci usługi | Bajty | Maksimum
Tak | Microsoft. Microservices4Spring/appClusters | ServiceMemoryUsed | Użyta pamięć usługi | Bajty | Średnia
Tak | Microsoft. Microservices4Spring/appClusters | SystemCpuUsagePercentage | Procent użycia procesora systemu | Procent | Średnia
Tak | Microsoft. Microservices4Spring/appClusters | TomcatErrorCount | Błąd globalny Tomcat | Liczba | Łącznie
Tak | Microsoft. Microservices4Spring/appClusters | TomcatReceivedBytes | Całkowita liczba odebranych bajtów Tomcat | Bajty | Łącznie
Tak | Microsoft. Microservices4Spring/appClusters | TomcatRequestMaxTime | Maksymalny czas żądania Tomcat | MS | Maksimum
Tak | Microsoft. Microservices4Spring/appClusters | TomcatRequestTotalCount | Łączna liczba żądań Tomcat | Liczba | Łącznie
Tak | Microsoft. Microservices4Spring/appClusters | TomcatRequestTotalTime | Łączny czas żądania Tomcat | MS | Łącznie
Tak | Microsoft. Microservices4Spring/appClusters | TomcatResponseAvgTime | Średni czas żądania Tomcat | MS | Średnia
Tak | Microsoft. Microservices4Spring/appClusters | TomcatSentBytes | Całkowita liczba wysłanych bajtów Tomcat | Bajty | Łącznie
Tak | Microsoft. Microservices4Spring/appClusters | TomcatSessionActiveCurrentCount | Liczba aktywności sesji Tomcat | Liczba | Łącznie
Tak | Microsoft. Microservices4Spring/appClusters | TomcatSessionActiveMaxCount | Maksymalna liczba aktywnych sesji Tomcat | Liczba | Łącznie
Tak | Microsoft. Microservices4Spring/appClusters | TomcatSessionAliveMaxTime | Maksymalny czas aktywności sesji Tomcat | MS | Maksimum
Tak | Microsoft. Microservices4Spring/appClusters | TomcatSessionCreatedCount | Liczba utworzonych sesji Tomcat | Liczba | Łącznie
Tak | Microsoft. Microservices4Spring/appClusters | TomcatSessionExpiredCount | Liczba wygasłych sesji Tomcat | Liczba | Łącznie
Tak | Microsoft. Microservices4Spring/appClusters | TomcatSessionRejectedCount | Liczba odrzuconych sesji Tomcat | Liczba | Łącznie
Tak | Microsoft. Microservices4Spring/appClusters | YoungGenPromotedBytes | Podwyższ poziom do rozmiaru danych dla małych generacji | Bajty | Maksimum
Tak | Microsoft.NetApp/netAppAccounts/capacityPools | VolumePoolAllocatedUsed | Użyte przydzieloną pulę woluminów | Bajty | Średnia
Tak | Microsoft.NetApp/netAppAccounts/capacityPools | VolumePoolTotalLogicalSize | Łączny rozmiar logiczny puli woluminów | Bajty | Średnia
Tak | Microsoft. NetApp/netAppAccounts/capacityPools/woluminy | AverageReadLatency | Średnie opóźnienie odczytu | MilliSeconds | Średnia
Tak | Microsoft. NetApp/netAppAccounts/capacityPools/woluminy | AverageWriteLatency | Średnie opóźnienie zapisu | MilliSeconds | Średnia
Tak | Microsoft. NetApp/netAppAccounts/capacityPools/woluminy | ReadIops | Odczyt operacji we/wy | CountPerSecond | Średnia
Tak | Microsoft. NetApp/netAppAccounts/capacityPools/woluminy | VolumeLogicalSize | Rozmiar logiczny woluminu | Bajty | Średnia
Tak | Microsoft. NetApp/netAppAccounts/capacityPools/woluminy | VolumeSnapshotSize | Rozmiar migawki woluminu | Bajty | Średnia
Tak | Microsoft. NetApp/netAppAccounts/capacityPools/woluminy | WriteIops | Zapisz operacje we/wy | CountPerSecond | Średnia
Nie | Microsoft.Network/applicationGateways | ApplicationGatewayTotalTime | Łączny czas Application Gateway | MilliSeconds | Średnia
Nie | Microsoft.Network/applicationGateways | AvgRequestCountPerHealthyHost | Liczba żądań na minutę na hosta w dobrej kondycji | Liczba | Średnia
Nie | Microsoft.Network/applicationGateways | BackendConnectTime | Czas połączenia z zapleczem | MilliSeconds | Średnia
Nie | Microsoft.Network/applicationGateways | BackendFirstByteResponseTime | Czas odpowiedzi na pierwszy bajt zaplecza | MilliSeconds | Średnia
Nie | Microsoft.Network/applicationGateways | BackendLastByteResponseTime | Czas odpowiedzi ostatniego bajtu wewnętrznej bazy danych | MilliSeconds | Średnia
Tak | Microsoft.Network/applicationGateways | BackendResponseStatus | Stan odpowiedzi zaplecza | Liczba | Łącznie
Tak | Microsoft.Network/applicationGateways | BlockedCount | Dystrybucja reguły zablokowanych żądań zapory aplikacji sieci Web | Liczba | Łącznie
Tak | Microsoft.Network/applicationGateways | BlockedReqCount | Liczba zablokowanych żądań zapory aplikacji sieci Web | Liczba | Łącznie
Tak | Microsoft.Network/applicationGateways | BytesReceived | Bajty odebrane | Bajty | Łącznie
Tak | Microsoft.Network/applicationGateways | BytesSent | Bajty wysłane | Bajty | Łącznie
Nie | Microsoft.Network/applicationGateways | CapacityUnits | Bieżące jednostki wydajności | Liczba | Średnia
Nie | Microsoft.Network/applicationGateways | ClientRtt | Czas RTT klienta | MilliSeconds | Średnia
Nie | Microsoft.Network/applicationGateways | ComputeUnits | Bieżące jednostki obliczeniowe | Liczba | Średnia
Tak | Microsoft.Network/applicationGateways | Wartości CurrentConnections | Bieżące połączenia | Liczba | Łącznie
Tak | Microsoft.Network/applicationGateways | FailedRequests | Żądania zakończone niepowodzeniem | Liczba | Łącznie
Tak | Microsoft.Network/applicationGateways | HealthyHostCount | Liczba hostów w dobrej kondycji | Liczba | Średnia
Tak | Microsoft.Network/applicationGateways | MatchedCount | Dystrybucja reguł dla zapory aplikacji sieci Web | Liczba | Łącznie
Tak | Microsoft.Network/applicationGateways | ResponseStatus | Stan odpowiedzi | Liczba | Łącznie
Nie | Microsoft.Network/applicationGateways | Przepływność | Przepływność | BytesPerSecond | Średnia
Tak | Microsoft.Network/applicationGateways | TlsProtocol | Protokół TLS klienta | Liczba | Łącznie
Tak | Microsoft.Network/applicationGateways | TotalRequests | Łączna liczba żądań | Liczba | Łącznie
Tak | Microsoft.Network/applicationGateways | UnhealthyHostCount | Liczba hostów w złej kondycji | Liczba | Średnia
Tak | Microsoft.Network/azurefirewalls | ApplicationRuleHit | Liczba trafień reguł aplikacji | Liczba | Łącznie
Tak | Microsoft.Network/azurefirewalls | Przetwarzanie dataprocessed | Ilość przetworzonych danych | Bajty | Łącznie
Tak | Microsoft.Network/azurefirewalls | FirewallHealth | Stan kondycji zapory | Procent | Średnia
Tak | Microsoft.Network/azurefirewalls | NetworkRuleHit | Liczba trafień reguł sieci | Liczba | Łącznie
Tak | Microsoft.Network/azurefirewalls | SNATPortUtilization | Wykorzystanie portów przez przytranslatora adresów sieciowych | Procent | Średnia
Tak | Microsoft. Network/Connections | BitsInPerSecond | BitsInPerSecond | CountPerSecond | Średnia
Tak | Microsoft. Network/Connections | BitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Średnia
Tak | Microsoft.Network/dnszones | QueryVolume | Wolumin zapytania | Liczba | Łącznie
Nie | Microsoft.Network/dnszones | RecordSetCapacityUtilization | Wykorzystanie pojemności zestawu rekordów | Procent | Maksimum
Tak | Microsoft.Network/dnszones | RecordSetCount | Liczba zestawów rekordów | Liczba | Maksimum
Tak | Microsoft.Network/expressRouteCircuits | ArpAvailability | Dostępność protokołu ARP | Procent | Średnia
Tak | Microsoft.Network/expressRouteCircuits | BgpAvailability | Dostępność protokołu BGP | Procent | Średnia
Nie | Microsoft.Network/expressRouteCircuits | BitsInPerSecond | BitsInPerSecond | CountPerSecond | Średnia
Nie | Microsoft.Network/expressRouteCircuits | BitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Średnia
Nie | Microsoft.Network/expressRouteCircuits | GlobalReachBitsInPerSecond | GlobalReachBitsInPerSecond | CountPerSecond | Średnia
Nie | Microsoft.Network/expressRouteCircuits | GlobalReachBitsOutPerSecond | GlobalReachBitsOutPerSecond | CountPerSecond | Średnia
Nie | Microsoft.Network/expressRouteCircuits | QosDropBitsInPerSecond | DroppedInBitsPerSecond | CountPerSecond | Średnia
Nie | Microsoft.Network/expressRouteCircuits | QosDropBitsOutPerSecond | DroppedOutBitsPerSecond | CountPerSecond | Średnia
Tak | Microsoft. Network/expressRouteCircuits/Komunikacja równorzędna | BitsInPerSecond | BitsInPerSecond | CountPerSecond | Średnia
Tak | Microsoft. Network/expressRouteCircuits/Komunikacja równorzędna | BitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Średnia
Nie | Microsoft. Network/expressRouteGateways | ErGatewayConnectionBitsInPerSecond | BitsInPerSecond | CountPerSecond | Średnia
Nie | Microsoft. Network/expressRouteGateways | ErGatewayConnectionBitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Średnia
Tak | Microsoft. Network/expressRoutePorts | AdminState | AdminState | Liczba | Średnia
Tak | Microsoft. Network/expressRoutePorts | LineProtocol | LineProtocol | Liczba | Średnia
Tak | Microsoft. Network/expressRoutePorts | PortBitsInPerSecond | BitsInPerSecond | CountPerSecond | Średnia
Tak | Microsoft. Network/expressRoutePorts | PortBitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Średnia
Tak | Microsoft. Network/expressRoutePorts | RxLightLevel | RxLightLevel | Liczba | Średnia
Tak | Microsoft. Network/expressRoutePorts | TxLightLevel | TxLightLevel | Liczba | Średnia
Tak | Microsoft. Network/usługi frontdoor | BackendHealthPercentage | Procent kondycji zaplecza | Procent | Średnia
Tak | Microsoft. Network/usługi frontdoor | BackendRequestCount | Liczba żądań wewnętrznej bazy danych | Liczba | Łącznie
Tak | Microsoft. Network/usługi frontdoor | BackendRequestLatency | Opóźnienie żądania wewnętrznej bazy danych | MilliSeconds | Średnia
Tak | Microsoft. Network/usługi frontdoor | BillableResponseSize | Rozmiar odpowiedzi do rozliczenia | Bajty | Łącznie
Tak | Microsoft. Network/usługi frontdoor | RequestCount | Liczba żądań | Liczba | Łącznie
Tak | Microsoft. Network/usługi frontdoor | RequestSize | Rozmiar żądania | Bajty | Łącznie
Tak | Microsoft. Network/usługi frontdoor | ResponseSize | Rozmiar odpowiedzi | Bajty | Łącznie
Tak | Microsoft. Network/usługi frontdoor | TotalLatency | Łączne opóźnienie | MilliSeconds | Średnia
Tak | Microsoft. Network/usługi frontdoor | WebApplicationFirewallRequestCount | Liczba żądań zapory aplikacji sieci Web | Liczba | Łącznie
Nie | Microsoft.Network/loadBalancers | AllocatedSnatPorts | Przydzielono porty przydziałów (wersja zapoznawcza) | Liczba | Łącznie
Tak | Microsoft.Network/loadBalancers | ByteCount | Liczba bajtów | Liczba | Łącznie
Tak | Microsoft.Network/loadBalancers | DipAvailability | Stan sondy kondycji | Liczba | Średnia
Tak | Microsoft.Network/loadBalancers | PacketCount | Liczba pakietów | Liczba | Łącznie
Tak | Microsoft.Network/loadBalancers | SnatConnectionCount | Liczba połączeń z translatorem adresów sieciowych | Liczba | Łącznie
Tak | Microsoft.Network/loadBalancers | SYNCount | Liczba SYN | Liczba | Łącznie
Nie | Microsoft.Network/loadBalancers | UsedSnatPorts | Używane porty (wersja zapoznawcza) | Liczba | Łącznie
Tak | Microsoft.Network/loadBalancers | VipAvailability | Dostępność ścieżki danych | Liczba | Średnia
Tak | Microsoft.Network/networkInterfaces | BytesReceivedRate | Bajty odebrane | Bajty | Łącznie
Tak | Microsoft.Network/networkInterfaces | BytesSentRate | Bajty wysłane | Bajty | Łącznie
Tak | Microsoft.Network/networkInterfaces | PacketsReceivedRate | Odebrane pakiety | Liczba | Łącznie
Tak | Microsoft.Network/networkInterfaces | PacketsSentRate | Wysłane pakiety | Liczba | Łącznie
Tak | Microsoft.Network/networkWatchers/connectionMonitors | AverageRoundtripMs | Średni czas błądzenia (MS) | MilliSeconds | Średnia
Tak | Microsoft.Network/networkWatchers/connectionMonitors | ChecksFailedPercent | Sprawdza procent niepowodzenia (wersja zapoznawcza) | Procent | Średnia
Tak | Microsoft.Network/networkWatchers/connectionMonitors | ProbesFailedPercent | % Sond nie powiodło się | Procent | Średnia
Tak | Microsoft.Network/networkWatchers/connectionMonitors | RoundTripTimeMs | Czas błądzenia (MS) (wersja zapoznawcza) | MilliSeconds | Średnia
Tak | Microsoft.Network/publicIPAddresses | ByteCount | Liczba bajtów | Liczba | Łącznie
Tak | Microsoft.Network/publicIPAddresses | BytesDroppedDDoS | Bajty przychodzące opuszczone DDoS | BytesPerSecond | Maksimum
Tak | Microsoft.Network/publicIPAddresses | BytesForwardedDDoS | Przekazane bajty przychodzące DDoS | BytesPerSecond | Maksimum
Tak | Microsoft.Network/publicIPAddresses | BytesInDDoS | Bajty przychodzące DDoS | BytesPerSecond | Maksimum
Tak | Microsoft.Network/publicIPAddresses | DDoSTriggerSYNPackets | Pakiety przychodzących SYN wyzwalające łagodzenie DDoS | CountPerSecond | Maksimum
Tak | Microsoft.Network/publicIPAddresses | DDoSTriggerTCPPackets | Przychodzące pakiety TCP do wyzwalania łagodzenia DDoS | CountPerSecond | Maksimum
Tak | Microsoft.Network/publicIPAddresses | DDoSTriggerUDPPackets | Przychodzące pakiety UDP do wyzwalania łagodzenia DDoS | CountPerSecond | Maksimum
Tak | Microsoft.Network/publicIPAddresses | IfUnderDDoSAttack | W obszarze atak DDoS | Liczba | Maksimum
Tak | Microsoft.Network/publicIPAddresses | PacketCount | Liczba pakietów | Liczba | Łącznie
Tak | Microsoft.Network/publicIPAddresses | PacketsDroppedDDoS | Odrzucone pakiety przychodzące DDoS | CountPerSecond | Maksimum
Tak | Microsoft.Network/publicIPAddresses | PacketsForwardedDDoS | Przekazane pakiety przychodzące DDoS | CountPerSecond | Maksimum
Tak | Microsoft.Network/publicIPAddresses | PacketsInDDoS | Pakiety przychodzące DDoS | CountPerSecond | Maksimum
Tak | Microsoft.Network/publicIPAddresses | SynCount | Liczba SYN | Liczba | Łącznie
Tak | Microsoft.Network/publicIPAddresses | TCPBytesDroppedDDoS | Liczba porzuconych bajtów przychodzących protokołu TCP DDoS | BytesPerSecond | Maksimum
Tak | Microsoft.Network/publicIPAddresses | TCPBytesForwardedDDoS | Przekazane DDoS przychodzące bajty TCP | BytesPerSecond | Maksimum
Tak | Microsoft.Network/publicIPAddresses | TCPBytesInDDoS | Przychodzące bajty TCP DDoS | BytesPerSecond | Maksimum
Tak | Microsoft.Network/publicIPAddresses | TCPPacketsDroppedDDoS | Liczba porzuconych pakietów TCP przychodzących DDoS | CountPerSecond | Maksimum
Tak | Microsoft.Network/publicIPAddresses | TCPPacketsForwardedDDoS | Przychodzące pakiety TCP przesłane dalej DDoS | CountPerSecond | Maksimum
Tak | Microsoft.Network/publicIPAddresses | TCPPacketsInDDoS | Przychodzące pakiety TCP DDoS | CountPerSecond | Maksimum
Tak | Microsoft.Network/publicIPAddresses | UDPBytesDroppedDDoS | Liczba porzuconych bajtów przychodzących UDP DDoS | BytesPerSecond | Maksimum
Tak | Microsoft.Network/publicIPAddresses | UDPBytesForwardedDDoS | Przekazane przychodzące bajty UDP DDoS | BytesPerSecond | Maksimum
Tak | Microsoft.Network/publicIPAddresses | UDPBytesInDDoS | Przychodzące bajty UDP DDoS | BytesPerSecond | Maksimum
Tak | Microsoft.Network/publicIPAddresses | UDPPacketsDroppedDDoS | Liczba porzuconych pakietów przychodzących UDP DDoS | CountPerSecond | Maksimum
Tak | Microsoft.Network/publicIPAddresses | UDPPacketsForwardedDDoS | Przychodzące pakiety UDP DDoS przesłane dalej | CountPerSecond | Maksimum
Tak | Microsoft.Network/publicIPAddresses | UDPPacketsInDDoS | Przychodzące pakiety UDP DDoS | CountPerSecond | Maksimum
Tak | Microsoft.Network/publicIPAddresses | VipAvailability | Dostępność ścieżki danych | Liczba | Średnia
Tak | Microsoft.Network/trafficManagerProfiles | ProbeAgentCurrentEndpointStateByProfileResourceId | Stan punktu końcowego według punktu końcowego | Liczba | Maksimum
Tak | Microsoft.Network/trafficManagerProfiles | QpsByEndpoint | Zwrócone zapytania według punktu końcowego | Liczba | Łącznie
Tak | Microsoft.Network/virtualNetworkGateways | AverageBandwidth | Przepustowość S2S bramy | BytesPerSecond | Średnia
Tak | Microsoft.Network/virtualNetworkGateways | P2SBandwidth | Przepustowość P2S bramy | BytesPerSecond | Średnia
Tak | Microsoft.Network/virtualNetworkGateways | P2SConnectionCount | Liczba połączeń P2S | Liczba | Maksimum
Tak | Microsoft.Network/virtualNetworkGateways | TunnelAverageBandwidth | Przepustowość tunelu | BytesPerSecond | Średnia
Tak | Microsoft.Network/virtualNetworkGateways | TunnelEgressBytes | Bajty wychodzące tunelu | Bajty | Łącznie
Tak | Microsoft.Network/virtualNetworkGateways | TunnelEgressPacketDropTSMismatch | Porzucanie pakietów wychodzących przez tunelowanie | Liczba | Łącznie
Tak | Microsoft.Network/virtualNetworkGateways | TunnelEgressPackets | Pakiety wychodzące tuneli | Liczba | Łącznie
Tak | Microsoft.Network/virtualNetworkGateways | TunnelIngressBytes | Bajty transferu danych wejściowych tunelu | Bajty | Łącznie
Tak | Microsoft.Network/virtualNetworkGateways | TunnelIngressPacketDropTSMismatch | Wyrzucanie niezgodności pakietów przez tunelowanie | Liczba | Łącznie
Tak | Microsoft.Network/virtualNetworkGateways | TunnelIngressPackets | Pakiety przychodzące tunelu | Liczba | Łącznie
Tak | Microsoft.Network/virtualNetworks | PingMeshAverageRoundtripMs | Czas błądzenia dla poleceń ping dla maszyny wirtualnej | MilliSeconds | Średnia
Tak | Microsoft.Network/virtualNetworks | PingMeshProbesFailedPercent | Nieudane polecenia ping do maszyny wirtualnej | Procent | Średnia
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | przychodzące | Komunikaty przychodzące | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | przychodzące. ALL. failedrequests | Wszystkie przychodzące żądania zakończone niepowodzeniem | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.requests | Wszystkie żądania przychodzące | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled | Przesłane zaplanowane powiadomienia wypychane | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled.cancel | Anulowano zaplanowane powiadomienia wypychane | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | Instalacja. wszystkie | Operacje zarządzania instalacją | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.delete | Usuwanie operacji instalacji | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | Instalacja. Get | Pobierz operacje instalacji | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.patch | Operacje instalacji poprawek | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | Instalacja. upsert | Tworzenie lub aktualizowanie operacji instalacji | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | notificationhub.pushes | Wszystkie powiadomienia wychodzące | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.badorexpiredchannel | Błędy nieprawidłowych lub wygasłych kanałów | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.channelerror | Błędy kanałów | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | wychodzący. allpns. invalidpayload | Błędy ładunku | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.pnserror | Błędy zewnętrznego systemu powiadomień | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.success | Powiadomienia zakończone powodzeniem | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.badchannel | Błąd nieprawidłowego kanału usługi APNS | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.expiredchannel | Błąd wygasłego kanału usługi APNS | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidcredentials | Błędy autoryzacji usługi APNS | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidnotificationsize | Błąd nieprawidłowego rozmiaru powiadomienia usługi APNS | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.pnserror | Błędy usługi APNS | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.success | Pomyślne powiadomienia usługi APNS | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.authenticationerror | Błędy uwierzytelniania GCM | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.badchannel | Błąd nieprawidłowego kanału GCM | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.expiredchannel | Błąd GCM kanału wygasłego | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidcredentials | GCM błędy autoryzacji (Nieprawidłowe poświadczenia) | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationformat | Nieprawidłowy format powiadomienia GCM | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationsize | Błąd nieprawidłowego rozmiaru powiadomienia GCM | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.pnserror | Błędy GCM | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.success | GCM pomyślne powiadomienia | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.throttled | GCM powiadomienia z ograniczeniami | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.wrongchannel | Błąd nieprawidłowego kanału GCM | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.authenticationerror | Błędy uwierzytelniania usługi MPNS | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.badchannel | Błąd nieprawidłowego kanału usługi MPNS | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.channeldisconnected | Kanał usługi MPNS został odłączony | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.dropped | USŁUGI MPNS usunięte powiadomienia | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidcredentials | USŁUGI MPNS nieprawidłowe poświadczenia | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidnotificationformat | Nieprawidłowy format powiadomienia usługi MPNS | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.pnserror | Błędy usługi MPNS | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.success | USŁUGI MPNS pomyślne powiadomienia | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | wychodzące. usługi MPNS. dławienia | USŁUGI MPNS powiadomienia z ograniczeniami | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.authenticationerror | Błędy uwierzytelniania WNS | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.badchannel | Błąd nieprawidłowego kanału WNS | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channeldisconnected | Kanał WNS został odłączony | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channelthrottled | Ograniczenie kanału WNS | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.dropped | WNS usunięte powiadomienia | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.expiredchannel | Błąd WNS kanału wygasłego | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | wychodzący. WNS. invalidcredentials | WNS błędy autoryzacji (Nieprawidłowe poświadczenia) | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationformat | Nieprawidłowy format powiadomienia WNS | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationsize | Błąd nieprawidłowego rozmiaru powiadomienia WNS | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidtoken | Błędy autoryzacji WNS (nieprawidłowy token) | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.pnserror | Błędy WNS | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.success | WNS pomyślne powiadomienia | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | wychodzące. WNS. dławienia | WNS powiadomienia z ograniczeniami | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.tokenproviderunreachable | Błędy autoryzacji WNS (nieosiągalne) | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.wrongtoken | WNS błędy autoryzacji (nieprawidłowy token) | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | Rejestracja. wszystkie | Operacje rejestracji | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.create | Operacje tworzenia rejestracji | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.delete | Operacje usuwania rejestracji | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.get | Operacje odczytu rejestracji | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.update | Operacje aktualizacji rejestracji | Liczba | Łącznie
Tak | Microsoft.NotificationHubs/Namespaces/NotificationHubs | scheduled.pending | Oczekujące zaplanowane powiadomienia | Liczba | Łącznie
Tak | Microsoft.OperationalInsights/workspaces | Dostępna pamięć Average_% | Dostępna pamięć (%) | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Dostępny obszar wymiany Average_% | Dostępny obszar wymiany (%) | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_% zadeklarowanych bajtów w użyciu | % Zadeklarowanych bajtów w użyciu | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Czas DPC Average_% | Czas DPC (%) | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_% bezpłatne węzłów i | % Wolnego węzłów i | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Wolne miejsce w Average_% | % Wolnego miejsca | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Wolne miejsce w Average_% | % Wolnego miejsca | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Czas bezczynności Average_% | Czas bezczynności (%) | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_ czas przerwań (%) | Czas przerwań (%) | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Czas oczekiwania operacji we/wy Average_% | Czas oczekiwania operacji we/wy (%) | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_% czasu całkiem | % Całkiem czasu | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_% czasu uprzywilejowanego | Czas uprzywilejowany (%) | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_ czas procesora (%) | Czas procesora (%) | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_ czas procesora (%) | Czas procesora (%) | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_ użyto węzłów i | % Użytych węzłów i | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Użyta pamięć Average_% | Używana pamięć (%) | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Używane miejsce Average_% | Procent wykorzystania miejsca | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Używany obszar wymiany Average_% | Używany obszar wymiany (%) | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Czas użytkownika Average_% | Czas użytkownika (%) | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_Available MB | Dostępna pamięć (MB) | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Pamięć Average_Available MB | Dostępna pamięć (MB) | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Zamiana Average_Available MB | Dostępny obszar wymiany (MB) | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_Avg. Czas dysku w s/Odczyt | Średni czas dysku w s/odczyt | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_Avg. Czas dysku w s/Odczyt | Średni czas dysku w s/odczyt | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_Avg. Dysku w s/Transfer | Średni czas dysku w s/transfer | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_Avg. Dysku w s/Zapis | Średni czas dysku w s/zapis | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_Avg. Dysku w s/Zapis | Średni czas dysku w s/zapis | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Odebrane Average_Bytes/s | Bajty odebrane/s | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Wysłane Average_Bytes/s | Bajty wysłane/s | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_Bytes całkowita/s | Całkowita liczba bajtów/s | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_Current długość kolejki dysku | Bieżąca długość kolejki dysku | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Bajty odczytu Average_Disk/s | Bajty odczytu z dysku/s | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Odczyty Average_Disk/s | Odczyty dysku/s | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Odczyty Average_Disk/s | Odczyty dysku/s | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Transfery Average_Disk/s | Transfery dyskowe/s | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Transfery Average_Disk/s | Transfery dyskowe/s | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Bajty zapisu Average_Disk/s | Bajty zapisu na dysku/s | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Zapisy Average_Disk/s | Zapisy dysku/s | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Zapisy Average_Disk/s | Zapisy dysku/s | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_Free megabajtów | Wolne megabajty | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_Free megabajtów | Wolne megabajty | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_Free pamięci fizycznej | Wolna pamięć fizyczna | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_Free miejsce w plikach stronicowania | Wolne miejsce w plikach stronicowania | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_Free pamięć wirtualną | Wolna pamięć wirtualna | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Bajty dysku Average_Logical/s | Bajty dysku logicznego/s | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Odczyty Average_Page/s | Odczyty stron/s | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Zapisy Average_Page/s | Zapisy stron/s | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_Pages/s | Stron/s | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_Pct uprzywilejowany czas | Czas uprzywilejowany PCT | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Czas użytkownika Average_Pct | Czas użytkownika PCT | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Bajty dysku Average_Physical/s | Bajty dysku fizycznego/s | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_Processes | Procesy | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Długość kolejki Average_Processor | Długość kolejki procesora | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_Size przechowywane w plikach stronicowania | Rozmiar zapisany w plikach stronicowania | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_Total bajty | Łączna liczba bajtów | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Bajty odebrane Average_Total | Całkowita liczba odebranych bajtów | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Przesyłane Average_Total bajty | Całkowita liczba przesłanych bajtów | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_Total kolizje | Łączna liczba kolizji | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Odebrane pakiety Average_Total | Całkowita liczba odebranych pakietów | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_Total przesyłane pakiety | Całkowita liczba przesłanych pakietów | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_Total błędy odbierania | Całkowita liczba błędów odbierania | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Błędy wysyłania Average_Total | Całkowita liczba błędów transmisji | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_Uptime | Czas pracy | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Przestrzeń wymiany Average_Used MB | Używany obszar wymiany (MB) | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Pamięć Average_Used w kilobajtach | Używana pamięć (w kilobajtach) | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Pamięć Average_Used pamięci (MB) | Używana pamięć (MB) | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_Users | Użytkownicy | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Average_Virtual pamięci współdzielonej | Wirtualna pamięć udostępniona | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Wydarzenie | Wydarzenie | Liczba | Średnia
Tak | Microsoft.OperationalInsights/workspaces | Sygnały | Sygnały | Liczba | Łącznie
Tak | Microsoft.OperationalInsights/workspaces | Aktualizacja | Aktualizacja | Liczba | Średnia
Tak | Microsoft.PowerBIDedicated/capacities | memory_metric | Pamięć | Bajty | Średnia
Tak | Microsoft.PowerBIDedicated/capacities | memory_thrashing_metric | Migotanie pamięci (zbiory danych) | Procent | Średnia
Tak | Microsoft.PowerBIDedicated/capacities | qpu_high_utilization_metric | QPU wysokie wykorzystanie | Liczba | Łącznie
Tak | Microsoft.PowerBIDedicated/capacities | QueryDuration | Czas trwania zapytania (zestawy danych) | MS | Średnia
Tak | Microsoft.PowerBIDedicated/capacities | QueryPoolJobQueueLength | Długość kolejki zadań puli zapytań (zbiory danych) | Liczba | Średnia
Nie | Microsoft. Relay/przestrzenie nazw | Połączeń ActiveConnections | Połączeń ActiveConnections | Liczba | Łącznie
Nie | Microsoft. Relay/przestrzenie nazw | ActiveListeners | ActiveListeners | Liczba | Łącznie
Tak | Microsoft. Relay/przestrzenie nazw | BytesTransferred | BytesTransferred | Liczba | Łącznie
Nie | Microsoft. Relay/przestrzenie nazw | ListenerConnections — błąd clienterror | ListenerConnections — błąd clienterror | Liczba | Łącznie
Nie | Microsoft. Relay/przestrzenie nazw | ListenerConnections-ServerError | ListenerConnections-ServerError | Liczba | Łącznie
Nie | Microsoft. Relay/przestrzenie nazw | ListenerConnections-Success | ListenerConnections-Success | Liczba | Łącznie
Nie | Microsoft. Relay/przestrzenie nazw | ListenerConnections-TotalRequests | ListenerConnections-TotalRequests | Liczba | Łącznie
Nie | Microsoft. Relay/przestrzenie nazw | ListenerDisconnects | ListenerDisconnects | Liczba | Łącznie
Nie | Microsoft. Relay/przestrzenie nazw | SenderConnections-ClientError | SenderConnections-ClientError | Liczba | Łącznie
Nie | Microsoft. Relay/przestrzenie nazw | SenderConnections-ServerError | SenderConnections-ServerError | Liczba | Łącznie
Nie | Microsoft. Relay/przestrzenie nazw | SenderConnections-Success | SenderConnections-Success | Liczba | Łącznie
Nie | Microsoft. Relay/przestrzenie nazw | SenderConnections-TotalRequests | SenderConnections-TotalRequests | Liczba | Łącznie
Nie | Microsoft. Relay/przestrzenie nazw | SenderDisconnects | SenderDisconnects | Liczba | Łącznie
Tak | Microsoft.Search/searchServices | SearchLatency | Opóźnienie wyszukiwania | Sekundy | Średnia
Tak | Microsoft.Search/searchServices | SearchQueriesPerSecond | Zapytania wyszukiwania na sekundę | CountPerSecond | Średnia
Tak | Microsoft.Search/searchServices | ThrottledSearchQueriesPercentage | Procent zapytań wyszukiwania z ograniczeniami | Procent | Średnia
Nie | Microsoft.ServiceBus/namespaces | Połączeń ActiveConnections | Połączeń ActiveConnections | Liczba | Łącznie
Nie | Microsoft.ServiceBus/namespaces | ActiveMessages | Liczba aktywnych komunikatów w kolejce/temacie. | Liczba | Średnia
Nie | Microsoft.ServiceBus/namespaces | Połączenia connectionsclosed | Połączenia zamknięte. | Liczba | Średnia
Nie | Microsoft.ServiceBus/namespaces | Połączenia connectionsopened | Otwarte połączenia. | Liczba | Średnia
Nie | Microsoft.ServiceBus/namespaces | CPUXNS | PROCESOR (przestarzałe) | Procent | Maksimum
Nie | Microsoft.ServiceBus/namespaces | DeadletteredMessages | Liczba utraconych wiadomości w kolejce/temacie. | Liczba | Średnia
Tak | Microsoft.ServiceBus/namespaces | Komunikaty incomingmessages | Komunikaty przychodzące | Liczba | Łącznie
Tak | Microsoft.ServiceBus/namespaces | Żądania incomingrequests | Żądania przychodzące | Liczba | Łącznie
Nie | Microsoft.ServiceBus/namespaces | Komunikaty | Liczba komunikatów w kolejce/temacie. | Liczba | Średnia
Nie | Microsoft.ServiceBus/namespaces | NamespaceCpuUsage | Procesor CPU | Procent | Maksimum
Nie | Microsoft.ServiceBus/namespaces | NamespaceMemoryUsage | Użycie pamięci | Procent | Maksimum
Tak | Microsoft.ServiceBus/namespaces | Komunikaty outgoingmessages | Komunikaty wychodzące | Liczba | Łącznie
Nie | Microsoft.ServiceBus/namespaces | ScheduledMessages | Liczba zaplanowanych komunikatów w kolejce/temacie. | Liczba | Średnia
Nie | Microsoft.ServiceBus/namespaces | Błędy servererrors | Błędy serwera. | Liczba | Łącznie
Nie | Microsoft.ServiceBus/namespaces | Rozmiar | Rozmiar | Bajty | Średnia
Nie | Microsoft.ServiceBus/namespaces | Żądania successfulrequests | Żądania zakończone powodzeniem | Liczba | Łącznie
Nie | Microsoft.ServiceBus/namespaces | ThrottledRequests | Żądania z ograniczeniami. | Liczba | Łącznie
Nie | Microsoft.ServiceBus/namespaces | Błędy usererrors | Błędy użytkownika. | Liczba | Łącznie
Nie | Microsoft.ServiceBus/namespaces | WSXNS | Użycie pamięci (przestarzałe) | Procent | Maksimum
Nie | Microsoft.ServiceFabricMesh/applications | ActualCpu | ActualCpu | Liczba | Średnia
Nie | Microsoft.ServiceFabricMesh/applications | ActualMemory | ActualMemory | Bajty | Średnia
Nie | Microsoft.ServiceFabricMesh/applications | AllocatedCpu | AllocatedCpu | Liczba | Średnia
Nie | Microsoft.ServiceFabricMesh/applications | AllocatedMemory | AllocatedMemory | Bajty | Średnia
Nie | Microsoft.ServiceFabricMesh/applications | ApplicationStatus | ApplicationStatus | Liczba | Średnia
Nie | Microsoft.ServiceFabricMesh/applications | ContainerStatus | ContainerStatus | Liczba | Średnia
Nie | Microsoft.ServiceFabricMesh/applications | CpuUtilization | CpuUtilization | Procent | Średnia
Nie | Microsoft.ServiceFabricMesh/applications | MemoryUtilization | MemoryUtilization | Procent | Średnia
Nie | Microsoft.ServiceFabricMesh/applications | RestartCount | RestartCount | Liczba | Średnia
Nie | Microsoft.ServiceFabricMesh/applications | ServiceReplicaStatus | ServiceReplicaStatus | Liczba | Średnia
Nie | Microsoft.ServiceFabricMesh/applications | Bajty | Bajty | Liczba | Średnia
Tak | Microsoft.SignalRService/SignalR | ConnectionCount | Liczba połączeń | Liczba | Maksimum
Tak | Microsoft.SignalRService/SignalR | InboundTraffic | Ruch przychodzący | Bajty | Łącznie
Tak | Microsoft.SignalRService/SignalR | MessageCount | Liczba komunikatów | Liczba | Łącznie
Tak | Microsoft.SignalRService/SignalR | OutboundTraffic | Ruch wychodzący | Bajty | Łącznie
Tak | Microsoft.SignalRService/SignalR | SystemErrors | Błędy systemu | Procent | Maksimum
Tak | Microsoft.SignalRService/SignalR | Błędy usererrors | Błędy użytkownika | Procent | Maksimum
Tak | Microsoft.Sql/managedInstances | avg_cpu_percent | Średni procent procesora CPU | Procent | Średnia
Tak | Microsoft.Sql/managedInstances | io_bytes_read | Odczytane bajty we/wy | Bajty | Średnia
Tak | Microsoft.Sql/managedInstances | io_bytes_written | Bajty we/wy zapisywane | Bajty | Średnia
Tak | Microsoft.Sql/managedInstances | io_requests | Liczba żądań we/wy | Liczba | Średnia
Tak | Microsoft.Sql/managedInstances | reserved_storage_mb | Zarezerwowane miejsce w magazynie | Liczba | Średnia
Tak | Microsoft.Sql/managedInstances | storage_space_used_mb | Używane miejsce do magazynowania | Liczba | Średnia
Tak | Microsoft.Sql/managedInstances | virtual_core_count | Liczba rdzeni wirtualnych | Liczba | Średnia
Nie | Microsoft.Sql/servers | database_dtu_consumption_percent | Procent użycia jednostek DTU | Procent | Średnia
Nie | Microsoft.Sql/servers | database_storage_used | Używane miejsce na dane | Bajty | Średnia
Tak | Microsoft.Sql/servers | dtu_consumption_percent | Procent użycia jednostek DTU | Procent | Średnia
Tak | Microsoft.Sql/servers | dtu_used | Używane jednostki DTU | Liczba | Średnia
Tak | Microsoft.Sql/servers | storage_used | Używane miejsce na dane | Bajty | Średnia
Tak | Microsoft.Sql/servers/databases | allocated_data_storage | Przydzielono miejsce na danych | Bajty | Średnia
Tak | Microsoft.Sql/servers/databases | app_cpu_billed | Rozliczane użycie procesora przez aplikację | Liczba | Łącznie
Tak | Microsoft.Sql/servers/databases | app_cpu_percent | Procent użycia procesora CPU aplikacji | Procent | Średnia
Tak | Microsoft.Sql/servers/databases | app_memory_percent | Procent pamięci aplikacji | Procent | Średnia
Tak | Microsoft.Sql/servers/databases | blocked_by_firewall | Zablokowane przez zaporę | Liczba | Łącznie
Tak | Microsoft.Sql/servers/databases | cache_hit_percent | Procent trafień w pamięci podręcznej | Procent | Maksimum
Tak | Microsoft.Sql/servers/databases | cache_used_percent | Procent użycia pamięci podręcznej | Procent | Maksimum
Tak | Microsoft.Sql/servers/databases | connection_failed | Połączenia zakończone niepowodzeniem | Liczba | Łącznie
Tak | Microsoft.Sql/servers/databases | connection_successful | Udane połączenia | Liczba | Łącznie
Tak | Microsoft.Sql/servers/databases | cpu_limit | Limit CPU | Liczba | Średnia
Tak | Microsoft.Sql/servers/databases | cpu_percent | Procent użycia procesora CPU | Procent | Średnia
Tak | Microsoft.Sql/servers/databases | cpu_used | Użycie procesora CPU | Liczba | Średnia
Tak | Microsoft.Sql/servers/databases | stanu | Zakleszczenia | Liczba | Łącznie
Tak | Microsoft.Sql/servers/databases | dtu_consumption_percent | Procent użycia jednostek DTU | Procent | Średnia
Tak | Microsoft.Sql/servers/databases | dtu_limit | DTU Limit | Liczba | Średnia
Tak | Microsoft.Sql/servers/databases | dtu_used | Używane jednostki DTU | Liczba | Średnia
Tak | Microsoft.Sql/servers/databases | dwu_consumption_percent | JEDNOSTEK dwu procent | Procent | Maksimum
Tak | Microsoft.Sql/servers/databases | dwu_limit | Limit jednostek dwu | Liczba | Maksimum
Tak | Microsoft.Sql/servers/databases | dwu_used | JEDNOSTEK dwu używane | Liczba | Maksimum
Tak | Microsoft.Sql/servers/databases | local_tempdb_usage_percent | Procent lokalnej bazy danych tempdb | Procent | Średnia
Tak | Microsoft.Sql/servers/databases | log_write_percent | Procent operacji we/wy dziennika | Procent | Średnia
Tak | Microsoft.Sql/servers/databases | memory_usage_percent | Procent pamięci | Procent | Maksimum
Tak | Microsoft.Sql/servers/databases | physical_data_read_percent | Procent użycia operacji we/wy na danych | Procent | Średnia
Tak | Microsoft.Sql/servers/databases | sessions_percent | Procent sesji | Procent | Średnia
Tak | Microsoft.Sql/servers/databases | magazyn | Używane miejsce na dane | Bajty | Maksimum
Tak | Microsoft.Sql/servers/databases | storage_percent | Procent użytego miejsca na danych | Procent | Maksimum
Tak | Microsoft.Sql/servers/databases | tempdb_data_size | Rozmiar pliku danych tempdb kilobajtów | Liczba | Maksimum
Tak | Microsoft.Sql/servers/databases | tempdb_log_size | Rozmiar pliku dziennika bazy danych tempdb kilobajtów | Liczba | Maksimum
Tak | Microsoft.Sql/servers/databases | tempdb_log_used_percent | Użyto dziennika% tempdb | Procent | Maksimum
Tak | Microsoft.Sql/servers/databases | workers_percent | Procent pracowników | Procent | Średnia
Tak | Microsoft.Sql/servers/databases | xtp_storage_percent | Procent magazynu OLTP w pamięci | Procent | Średnia
Tak | Microsoft.Sql/servers/elasticPools | allocated_data_storage | Przydzielono miejsce na danych | Bajty | Średnia
Tak | Microsoft.Sql/servers/elasticPools | allocated_data_storage_percent | Procent przydzielonych przestrzeni danych | Procent | Maksimum
Tak | Microsoft.Sql/servers/elasticPools | cpu_limit | Limit CPU | Liczba | Średnia
Tak | Microsoft.Sql/servers/elasticPools | cpu_percent | Procent użycia procesora CPU | Procent | Średnia
Tak | Microsoft.Sql/servers/elasticPools | cpu_used | Użycie procesora CPU | Liczba | Średnia
Nie | Microsoft.Sql/servers/elasticPools | database_allocated_data_storage | Przydzielono miejsce na danych | Bajty | Średnia
Nie | Microsoft.Sql/servers/elasticPools | database_cpu_limit | Limit CPU | Liczba | Średnia
Nie | Microsoft.Sql/servers/elasticPools | database_cpu_percent | Procent użycia procesora CPU | Procent | Średnia
Nie | Microsoft.Sql/servers/elasticPools | database_cpu_used | Użycie procesora CPU | Liczba | Średnia
Nie | Microsoft.Sql/servers/elasticPools | database_dtu_consumption_percent | Procent użycia jednostek DTU | Procent | Średnia
Nie | Microsoft.Sql/servers/elasticPools | database_eDTU_used | użyta wartość eDTU | Liczba | Średnia
Nie | Microsoft.Sql/servers/elasticPools | database_log_write_percent | Procent operacji we/wy dziennika | Procent | Średnia
Nie | Microsoft.Sql/servers/elasticPools | database_physical_data_read_percent | Procent użycia operacji we/wy na danych | Procent | Średnia
Nie | Microsoft.Sql/servers/elasticPools | database_sessions_percent | Procent sesji | Procent | Średnia
Nie | Microsoft.Sql/servers/elasticPools | database_storage_used | Używane miejsce na dane | Bajty | Średnia
Nie | Microsoft.Sql/servers/elasticPools | database_workers_percent | Procent pracowników | Procent | Średnia
Tak | Microsoft.Sql/servers/elasticPools | dtu_consumption_percent | Procent użycia jednostek DTU | Procent | Średnia
Tak | Microsoft.Sql/servers/elasticPools | eDTU_limit | limit liczby jednostek eDTU | Liczba | Średnia
Tak | Microsoft.Sql/servers/elasticPools | eDTU_used | użyta wartość eDTU | Liczba | Średnia
Tak | Microsoft.Sql/servers/elasticPools | log_write_percent | Procent operacji we/wy dziennika | Procent | Średnia
Tak | Microsoft.Sql/servers/elasticPools | physical_data_read_percent | Procent użycia operacji we/wy na danych | Procent | Średnia
Tak | Microsoft.Sql/servers/elasticPools | sessions_percent | Procent sesji | Procent | Średnia
Tak | Microsoft.Sql/servers/elasticPools | storage_limit | Maksymalny rozmiar danych | Bajty | Średnia
Tak | Microsoft.Sql/servers/elasticPools | storage_percent | Procent użytego miejsca na danych | Procent | Średnia
Tak | Microsoft.Sql/servers/elasticPools | storage_used | Używane miejsce na dane | Bajty | Średnia
Tak | Microsoft.Sql/servers/elasticPools | tempdb_data_size | Rozmiar pliku danych tempdb kilobajtów | Liczba | Maksimum
Tak | Microsoft.Sql/servers/elasticPools | tempdb_log_size | Rozmiar pliku dziennika bazy danych tempdb kilobajtów | Liczba | Maksimum
Tak | Microsoft.Sql/servers/elasticPools | tempdb_log_used_percent | Użyto dziennika% tempdb | Procent | Maksimum
Tak | Microsoft.Sql/servers/elasticPools | workers_percent | Procent pracowników | Procent | Średnia
Tak | Microsoft.Sql/servers/elasticPools | xtp_storage_percent | Procent magazynu OLTP w pamięci | Procent | Średnia
Tak | Microsoft.Storage/storageAccounts | Dostępność | Dostępność | Procent | Średnia
Tak | Microsoft.Storage/storageAccounts | Ruch wychodzący | Ruch wychodzący | Bajty | Łącznie
Tak | Microsoft.Storage/storageAccounts | Zdarzenia związane z transferem danych przychodzących | Zdarzenia związane z transferem danych przychodzących | Bajty | Łącznie
Tak | Microsoft.Storage/storageAccounts | SuccessE2ELatency | Opóźnienie e2e dla powodzenia | MS | Średnia
Tak | Microsoft.Storage/storageAccounts | SuccessServerLatency | Opóźnienie serwera dla powodzenia | MS | Średnia
Tak | Microsoft.Storage/storageAccounts | Transakcje | Transakcje | Liczba | Łącznie
Nie | Microsoft.Storage/storageAccounts | UsedCapacity | Używana pojemność | Bajty | Średnia
Tak | Microsoft.Storage/storageAccounts/blobServices | Dostępność | Dostępność | Procent | Średnia
Nie | Microsoft.Storage/storageAccounts/blobServices | BlobCapacity | Pojemność obiektu blob | Bajty | Średnia
Nie | Microsoft.Storage/storageAccounts/blobServices | BlobCount | Liczba obiektów blob | Liczba | Średnia
Tak | Microsoft.Storage/storageAccounts/blobServices | ContainerCount | Liczba kontenerów obiektów blob | Liczba | Średnia
Tak | Microsoft.Storage/storageAccounts/blobServices | Ruch wychodzący | Ruch wychodzący | Bajty | Łącznie
Nie | Microsoft.Storage/storageAccounts/blobServices | IndexCapacity | Pojemność indeksu | Bajty | Średnia
Tak | Microsoft.Storage/storageAccounts/blobServices | Zdarzenia związane z transferem danych przychodzących | Zdarzenia związane z transferem danych przychodzących | Bajty | Łącznie
Tak | Microsoft.Storage/storageAccounts/blobServices | SuccessE2ELatency | Opóźnienie e2e dla powodzenia | MS | Średnia
Tak | Microsoft.Storage/storageAccounts/blobServices | SuccessServerLatency | Opóźnienie serwera dla powodzenia | MS | Średnia
Tak | Microsoft.Storage/storageAccounts/blobServices | Transakcje | Transakcje | Liczba | Łącznie
Tak | Microsoft.Storage/storageAccounts/fileServices | Dostępność | Dostępność | Procent | Średnia
Tak | Microsoft.Storage/storageAccounts/fileServices | Ruch wychodzący | Ruch wychodzący | Bajty | Łącznie
Nie | Microsoft.Storage/storageAccounts/fileServices | FileCapacity | Pojemność plików | Bajty | Średnia
Nie | Microsoft.Storage/storageAccounts/fileServices | FileCount | Liczba plików | Liczba | Średnia
Nie | Microsoft.Storage/storageAccounts/fileServices | FileShareCount | Liczba udziałów plików | Liczba | Średnia
Nie | Microsoft.Storage/storageAccounts/fileServices | FileShareQuota | Rozmiar przydziału udziału plików | Bajty | Średnia
Nie | Microsoft.Storage/storageAccounts/fileServices | FileShareSnapshotCount | Liczba migawek udziału plików | Liczba | Średnia
Nie | Microsoft.Storage/storageAccounts/fileServices | FileShareSnapshotSize | Rozmiar migawki udziału plików | Bajty | Średnia
Tak | Microsoft.Storage/storageAccounts/fileServices | Zdarzenia związane z transferem danych przychodzących | Zdarzenia związane z transferem danych przychodzących | Bajty | Łącznie
Tak | Microsoft.Storage/storageAccounts/fileServices | SuccessE2ELatency | Opóźnienie e2e dla powodzenia | MS | Średnia
Tak | Microsoft.Storage/storageAccounts/fileServices | SuccessServerLatency | Opóźnienie serwera dla powodzenia | MS | Średnia
Tak | Microsoft.Storage/storageAccounts/fileServices | Transakcje | Transakcje | Liczba | Łącznie
Tak | Microsoft.Storage/storageAccounts/queueServices | Dostępność | Dostępność | Procent | Średnia
Tak | Microsoft.Storage/storageAccounts/queueServices | Ruch wychodzący | Ruch wychodzący | Bajty | Łącznie
Tak | Microsoft.Storage/storageAccounts/queueServices | Zdarzenia związane z transferem danych przychodzących | Zdarzenia związane z transferem danych przychodzących | Bajty | Łącznie
Tak | Microsoft.Storage/storageAccounts/queueServices | QueueCapacity | Pojemność kolejki | Bajty | Średnia
Tak | Microsoft.Storage/storageAccounts/queueServices | QueueCount | Liczba kolejek | Liczba | Średnia
Tak | Microsoft.Storage/storageAccounts/queueServices | QueueMessageCount | Liczba komunikatów w kolejce | Liczba | Średnia
Tak | Microsoft.Storage/storageAccounts/queueServices | SuccessE2ELatency | Opóźnienie e2e dla powodzenia | MS | Średnia
Tak | Microsoft.Storage/storageAccounts/queueServices | SuccessServerLatency | Opóźnienie serwera dla powodzenia | MS | Średnia
Tak | Microsoft.Storage/storageAccounts/queueServices | Transakcje | Transakcje | Liczba | Łącznie
Tak | Microsoft.Storage/storageAccounts/tableServices | Dostępność | Dostępność | Procent | Średnia
Tak | Microsoft.Storage/storageAccounts/tableServices | Ruch wychodzący | Ruch wychodzący | Bajty | Łącznie
Tak | Microsoft.Storage/storageAccounts/tableServices | Zdarzenia związane z transferem danych przychodzących | Zdarzenia związane z transferem danych przychodzących | Bajty | Łącznie
Tak | Microsoft.Storage/storageAccounts/tableServices | SuccessE2ELatency | Opóźnienie e2e dla powodzenia | MS | Średnia
Tak | Microsoft.Storage/storageAccounts/tableServices | SuccessServerLatency | Opóźnienie serwera dla powodzenia | MS | Średnia
Tak | Microsoft.Storage/storageAccounts/tableServices | TableCapacity | Pojemność tabeli | Bajty | Średnia
Tak | Microsoft.Storage/storageAccounts/tableServices | TableCount | Liczba tabel | Liczba | Średnia
Tak | Microsoft.Storage/storageAccounts/tableServices | TableEntityCount | Liczba jednostek tabel | Liczba | Średnia
Tak | Microsoft.Storage/storageAccounts/tableServices | Transakcje | Transakcje | Liczba | Łącznie
Tak | Microsoft. StorageCache/pamięci podręczne | ClientIOPS | Łączna liczba operacji we/wy klienta | Liczba | Średnia
Tak | Microsoft. StorageCache/pamięci podręczne | ClientLatency | Średnie opóźnienie klienta | MS | Średnia
Tak | Microsoft. StorageCache/pamięci podręczne | ClientLockIOPS | Liczba operacji we/wy blokady klienta | CountPerSecond | Średnia
Tak | Microsoft. StorageCache/pamięci podręczne | ClientMetadataReadIOPS | Liczba operacji we/wy odczytu metadanych klienta | CountPerSecond | Średnia
Tak | Microsoft. StorageCache/pamięci podręczne | ClientMetadataWriteIOPS | Liczba IOPS zapisu metadanych klienta | CountPerSecond | Średnia
Tak | Microsoft. StorageCache/pamięci podręczne | ClientReadIOPS | Liczba operacji we/wy odczytu klienta | CountPerSecond | Średnia
Tak | Microsoft. StorageCache/pamięci podręczne | ClientReadThroughput | Średnia przepływność odczytu pamięci podręcznej | BytesPerSecond | Średnia
Tak | Microsoft. StorageCache/pamięci podręczne | ClientWriteIOPS | Liczba operacji we/wy zapisu klienta | CountPerSecond | Średnia
Tak | Microsoft. StorageCache/pamięci podręczne | ClientWriteThroughput | Średnia przepływność zapisu w pamięci podręcznej | BytesPerSecond | Średnia
Tak | Microsoft. StorageCache/pamięci podręczne | StorageTargetAsyncWriteThroughput | Przepływność zapisu asynchronicznego StorageTarget | BytesPerSecond | Średnia
Tak | Microsoft. StorageCache/pamięci podręczne | StorageTargetFillThroughput | Przepływność wypełnienia StorageTarget | BytesPerSecond | Średnia
Tak | Microsoft. StorageCache/pamięci podręczne | StorageTargetHealth | Kondycja docelowa magazynu | Liczba | Średnia
Tak | Microsoft. StorageCache/pamięci podręczne | StorageTargetIOPS | Łączna liczba operacji we/wy StorageTarget | Liczba | Średnia
Tak | Microsoft. StorageCache/pamięci podręczne | StorageTargetLatency | Opóźnienie StorageTarget | MS | Średnia
Tak | Microsoft. StorageCache/pamięci podręczne | StorageTargetMetadataReadIOPS | Liczba operacji we/wy odczytu metadanych StorageTarget | CountPerSecond | Średnia
Tak | Microsoft. StorageCache/pamięci podręczne | StorageTargetMetadataWriteIOPS | Liczba IOPS zapisu w metadanych StorageTarget | CountPerSecond | Średnia
Tak | Microsoft. StorageCache/pamięci podręczne | StorageTargetReadAheadThroughput | StorageTarget z wyprzedzeniem | BytesPerSecond | Średnia
Tak | Microsoft. StorageCache/pamięci podręczne | StorageTargetReadIOPS | StorageTarget operacji we/wy odczytu | CountPerSecond | Średnia
Tak | Microsoft. StorageCache/pamięci podręczne | StorageTargetSyncWriteThroughput | Przepływność zapisu synchronicznego StorageTarget | BytesPerSecond | Średnia
Tak | Microsoft. StorageCache/pamięci podręczne | StorageTargetTotalReadThroughput | StorageTarget całkowita przepływność odczytu | BytesPerSecond | Średnia
Tak | Microsoft. StorageCache/pamięci podręczne | StorageTargetTotalWriteThroughput | StorageTarget całkowita przepływność zapisu | BytesPerSecond | Średnia
Tak | Microsoft. StorageCache/pamięci podręczne | StorageTargetWriteIOPS | StorageTarget zapisu IOPS | Liczba | Średnia
Tak | Microsoft. StorageCache/pamięci podręczne | Czas pracy | Czas pracy | Liczba | Średnia
Tak | microsoft.storagesync/storageSyncServices | ServerSyncSessionResult | Wynik synchronizacji sesji | Liczba | Średnia
Tak | microsoft.storagesync/storageSyncServices | StorageSyncBatchTransferredFileBytes | Zsynchronizowane bajty | Bajty | Łącznie
Tak | microsoft.storagesync/storageSyncServices | StorageSyncRecalledNetworkBytesByApplication | Rozmiar odwołania do warstw w chmurze według aplikacji | Bajty | Łącznie
Tak | microsoft.storagesync/storageSyncServices | StorageSyncRecalledTotalNetworkBytes | Rozmiar odwołania do warstw w chmurze | Bajty | Łącznie
Tak | microsoft.storagesync/storageSyncServices | StorageSyncRecallIOTotalSizeBytes | Odwołanie do warstw w chmurze | Bajty | Łącznie
Tak | microsoft.storagesync/storageSyncServices | StorageSyncRecallThroughputBytesPerSecond | Przepływność odwołań do warstw w chmurze | BytesPerSecond | Średnia
Tak | microsoft.storagesync/storageSyncServices | StorageSyncServerHeartbeat | Stan online serwera | Liczba | Maksimum
Tak | microsoft.storagesync/storageSyncServices | StorageSyncSyncSessionAppliedFilesCount | Synchronizowane pliki | Liczba | Łącznie
Tak | microsoft.storagesync/storageSyncServices | StorageSyncSyncSessionPerItemErrorsCount | Pliki, których nie należy synchronizować | Liczba | Łącznie
Tak | Microsoft. storagesync/storageSyncServices/registeredServers | ServerHeartbeat | Stan online serwera | Liczba | Maksimum
Tak | Microsoft. storagesync/storageSyncServices/registeredServers | ServerRecallIOTotalSizeBytes | Odwołanie do warstw w chmurze | Bajty | Łącznie
Tak | Microsoft. storagesync/storageSyncServices/syncGroups | SyncGroupBatchTransferredFileBytes | Zsynchronizowane bajty | Bajty | Łącznie
Tak | Microsoft. storagesync/storageSyncServices/syncGroups | SyncGroupSyncSessionAppliedFilesCount | Synchronizowane pliki | Liczba | Łącznie
Tak | Microsoft. storagesync/storageSyncServices/syncGroups | SyncGroupSyncSessionPerItemErrorsCount | Pliki, których nie należy synchronizować | Liczba | Łącznie
Tak | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointBatchTransferredFileBytes | Zsynchronizowane bajty | Bajty | Łącznie
Tak | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointSyncSessionAppliedFilesCount | Synchronizowane pliki | Liczba | Łącznie
Tak | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointSyncSessionPerItemErrorsCount | Pliki, których nie należy synchronizować | Liczba | Łącznie
Tak | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutFailedRequests | Żądania funkcji zakończone niepowodzeniem | Liczba | Łącznie
Tak | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutInputEvents | Zdarzenia funkcji | Liczba | Łącznie
Tak | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutRequests | Żądania funkcji | Liczba | Łącznie
Tak | Microsoft.StreamAnalytics/streamingjobs | ConversionErrors | Błędy konwersji danych | Liczba | Łącznie
Tak | Microsoft.StreamAnalytics/streamingjobs | DeserializationError | Błędy deserializacji danych wejściowych | Liczba | Łącznie
Tak | Microsoft.StreamAnalytics/streamingjobs | DroppedOrAdjustedEvents | Zdarzenia poza kolejnością | Liczba | Łącznie
Tak | Microsoft.StreamAnalytics/streamingjobs | EarlyInputEvents | Wczesne zdarzenia wejściowe | Liczba | Łącznie
Tak | Microsoft.StreamAnalytics/streamingjobs | Errors | Błędy w czasie wykonywania | Liczba | Łącznie
Tak | Microsoft.StreamAnalytics/streamingjobs | InputEventBytes | Zdarzenia wejściowe (bajty) | Bajty | Łącznie
Tak | Microsoft.StreamAnalytics/streamingjobs | InputEvents | Zdarzenia wejściowe | Liczba | Łącznie
Tak | Microsoft.StreamAnalytics/streamingjobs | InputEventsSourcesBacklogged | Zaległe zdarzenia wejściowe | Liczba | Maksimum
Tak | Microsoft.StreamAnalytics/streamingjobs | InputEventsSourcesPerSecond | Odebrane źródła wejściowe | Liczba | Łącznie
Tak | Microsoft.StreamAnalytics/streamingjobs | LateInputEvents | Opóźnione zdarzenia wejściowe | Liczba | Łącznie
Tak | Microsoft.StreamAnalytics/streamingjobs | OutputEvents | Zdarzenia wyjściowe | Liczba | Łącznie
Tak | Microsoft.StreamAnalytics/streamingjobs | OutputWatermarkDelaySeconds | Opóźnienie znaku wodnego | Sekundy | Maksimum
Tak | Microsoft.StreamAnalytics/streamingjobs | ResourceUtilization | % wykorzystania SU | Procent | Maksimum
Tak | Microsoft. VMwareCloudSimple/virtualMachines | Bajty odczytu dysku | Bajty odczytu dysku | Bajty | Łącznie
Tak | Microsoft. VMwareCloudSimple/virtualMachines | Operacje odczytu z dysku/s | Operacje odczytu z dysku/s | CountPerSecond | Średnia
Tak | Microsoft. VMwareCloudSimple/virtualMachines | Bajty zapisu dysku | Bajty zapisu dysku | Bajty | Łącznie
Tak | Microsoft. VMwareCloudSimple/virtualMachines | Operacje zapisu na dysku/s | Operacje zapisu na dysku/s | CountPerSecond | Średnia
Tak | Microsoft. VMwareCloudSimple/virtualMachines | DiskReadBytesPerSecond | Bajty odczytu dysku/s | BytesPerSecond | Średnia
Tak | Microsoft. VMwareCloudSimple/virtualMachines | DiskReadLatency | Opóźnienie odczytu dysku | MS | Średnia
Tak | Microsoft. VMwareCloudSimple/virtualMachines | DiskReadOperations | Operacje odczytu z dysku | Liczba | Łącznie
Tak | Microsoft. VMwareCloudSimple/virtualMachines | DiskWriteBytesPerSecond | Bajty zapisu dysku/s | BytesPerSecond | Średnia
Tak | Microsoft. VMwareCloudSimple/virtualMachines | DiskWriteLatency | Opóźnienie zapisu na dysku | MS | Średnia
Tak | Microsoft. VMwareCloudSimple/virtualMachines | DiskWriteOperations | Operacje zapisu na dysku | Liczba | Łącznie
Tak | Microsoft. VMwareCloudSimple/virtualMachines | MemoryActive | Pamięć aktywna | Bajty | Średnia
Tak | Microsoft. VMwareCloudSimple/virtualMachines | MemoryGranted | Przydzielone pamięci | Bajty | Średnia
Tak | Microsoft. VMwareCloudSimple/virtualMachines | MemoryUsed | Używana pamięć | Bajty | Średnia
Tak | Microsoft. VMwareCloudSimple/virtualMachines | Sieć — wejście | Sieć — wejście | Bajty | Łącznie
Tak | Microsoft. VMwareCloudSimple/virtualMachines | Sieć — wyjście | Sieć — wyjście | Bajty | Łącznie
Tak | Microsoft. VMwareCloudSimple/virtualMachines | NetworkInBytesPerSecond | Sieć w bajtach/s | BytesPerSecond | Średnia
Tak | Microsoft. VMwareCloudSimple/virtualMachines | NetworkOutBytesPerSecond | Bajty wychodzące z sieci/s | BytesPerSecond | Średnia
Tak | Microsoft. VMwareCloudSimple/virtualMachines | Procentowe użycie procesora CPU | Procentowe użycie procesora CPU | Procent | Średnia
Tak | Microsoft. VMwareCloudSimple/virtualMachines | PercentageCpuReady | Procent gotowych do użycia procesora | MS | Łącznie
Tak | Microsoft.Web/hostingEnvironments/multiRolePools | ActiveRequests | Aktywne żądania | Liczba | Łącznie
Tak | Microsoft.Web/hostingEnvironments/multiRolePools | AverageResponseTime | Średni czas odpowiedzi | Sekundy | Średnia
Tak | Microsoft.Web/hostingEnvironments/multiRolePools | BytesReceived | Dane w | Bajty | Łącznie
Tak | Microsoft.Web/hostingEnvironments/multiRolePools | BytesSent | Dane wychodzące | Bajty | Łącznie
Tak | Microsoft.Web/hostingEnvironments/multiRolePools | CpuPercentage | Procent użycia procesora CPU | Procent | Średnia
Tak | Microsoft.Web/hostingEnvironments/multiRolePools | DiskQueueLength | Długość kolejki dysku | Liczba | Średnia
Tak | Microsoft.Web/hostingEnvironments/multiRolePools | Http101 | Http 101 | Liczba | Łącznie
Tak | Microsoft.Web/hostingEnvironments/multiRolePools | Http2xx | Http 2xx | Liczba | Łącznie
Tak | Microsoft.Web/hostingEnvironments/multiRolePools | Http3xx | Http 3xx | Liczba | Łącznie
Tak | Microsoft.Web/hostingEnvironments/multiRolePools | Http401 | HTTP 401 | Liczba | Łącznie
Tak | Microsoft.Web/hostingEnvironments/multiRolePools | Http403 | HTTP 403 | Liczba | Łącznie
Tak | Microsoft.Web/hostingEnvironments/multiRolePools | Http404 | HTTP 404 | Liczba | Łącznie
Tak | Microsoft.Web/hostingEnvironments/multiRolePools | Http406 | Http 406 | Liczba | Łącznie
Tak | Microsoft.Web/hostingEnvironments/multiRolePools | Http4xx | Http 4xx | Liczba | Łącznie
Tak | Microsoft.Web/hostingEnvironments/multiRolePools | Http5xx | Błędy serwera http | Liczba | Łącznie
Tak | Microsoft.Web/hostingEnvironments/multiRolePools | HttpQueueLength | Długość kolejki http | Liczba | Średnia
Tak | Microsoft.Web/hostingEnvironments/multiRolePools | LargeAppServicePlanInstances | Duże App Service planowanie procesów roboczych | Liczba | Średnia
Tak | Microsoft.Web/hostingEnvironments/multiRolePools | MediumAppServicePlanInstances | Średni App Service pracowników planu | Liczba | Średnia
Tak | Microsoft.Web/hostingEnvironments/multiRolePools | MemoryPercentage | Procent pamięci | Procent | Średnia
Tak | Microsoft.Web/hostingEnvironments/multiRolePools | Żądania | Żądania | Liczba | Łącznie
Tak | Microsoft.Web/hostingEnvironments/multiRolePools | SmallAppServicePlanInstances | Niewielka App Service planowanie procesów roboczych | Liczba | Średnia
Tak | Microsoft.Web/hostingEnvironments/multiRolePools | TotalFrontEnds | Łączne frontony | Liczba | Średnia
Tak | Microsoft. Web/hostingEnvironments/workerPools | CpuPercentage | Procent użycia procesora CPU | Procent | Średnia
Tak | Microsoft. Web/hostingEnvironments/workerPools | MemoryPercentage | Procent pamięci | Procent | Średnia
Tak | Microsoft. Web/hostingEnvironments/workerPools | WorkersAvailable | Dostępni pracownicy | Liczba | Średnia
Tak | Microsoft. Web/hostingEnvironments/workerPools | Łączna | Łączna liczba procesów roboczych | Liczba | Średnia
Tak | Microsoft. Web/hostingEnvironments/workerPools | WorkersUsed | Używani pracownicy | Liczba | Średnia
Tak | Microsoft.Web/serverfarms | BytesReceived | Dane w | Bajty | Łącznie
Tak | Microsoft.Web/serverfarms | BytesSent | Dane wychodzące | Bajty | Łącznie
Tak | Microsoft.Web/serverfarms | CpuPercentage | Procent użycia procesora CPU | Procent | Średnia
Tak | Microsoft.Web/serverfarms | DiskQueueLength | Długość kolejki dysku | Liczba | Średnia
Tak | Microsoft.Web/serverfarms | HttpQueueLength | Długość kolejki http | Liczba | Średnia
Tak | Microsoft.Web/serverfarms | MemoryPercentage | Procent pamięci | Procent | Średnia
Tak | Microsoft.Web/serverfarms | TcpCloseWait | Oczekiwanie na zamknięcie protokołu TCP | Liczba | Średnia
Tak | Microsoft.Web/serverfarms | TcpClosing | Zamykanie protokołu TCP | Liczba | Średnia
Tak | Microsoft.Web/serverfarms | TcpEstablished | Nawiązano ruch TCP | Liczba | Średnia
Tak | Microsoft.Web/serverfarms | TcpFinWait1 | Oczekiwanie na TCP FIN 1 | Liczba | Średnia
Tak | Microsoft.Web/serverfarms | TcpFinWait2 | Oczekiwanie na TCP fin 2 | Liczba | Średnia
Tak | Microsoft.Web/serverfarms | TcpLastAck | Ostatnie potwierdzenie TCP | Liczba | Średnia
Tak | Microsoft.Web/serverfarms | TcpSynReceived | Odebrano pakiet TCP syn | Liczba | Średnia
Tak | Microsoft.Web/serverfarms | TcpSynSent | Wysłano pakiet TCP syn | Liczba | Średnia
Tak | Microsoft.Web/serverfarms | TcpTimeWait | Czas oczekiwania TCP | Liczba | Średnia
Tak | Microsoft.Web/sites | AppConnections | Połączenia | Liczba | Średnia
Tak | Microsoft.Web/sites | AverageMemoryWorkingSet | Średni zestaw roboczy pamięci | Bajty | Średnia
Tak | Microsoft.Web/sites | AverageResponseTime | Średni czas odpowiedzi | Sekundy | Średnia
Tak | Microsoft.Web/sites | BytesReceived | Dane w | Bajty | Łącznie
Tak | Microsoft.Web/sites | BytesSent | Dane wychodzące | Bajty | Łącznie
Tak | Microsoft.Web/sites | CpuTime | Czas procesora CPU | Sekundy | Łącznie
Tak | Microsoft.Web/sites | CurrentAssemblies | Bieżące zestawy | Liczba | Średnia
Tak | Microsoft.Web/sites | FunctionExecutionCount | Liczba wykonań funkcji | Liczba | Łącznie
Tak | Microsoft.Web/sites | FunctionExecutionUnits | Jednostki wykonywania funkcji | Liczba | Łącznie
Tak | Microsoft.Web/sites | Gen0Collections | Zbieranie elementów bezużytecznych generacji 0 | Liczba | Łącznie
Tak | Microsoft.Web/sites | Gen1Collections | Wyrzucanie elementów bezużytecznych generacji 1 | Liczba | Łącznie
Tak | Microsoft.Web/sites | Gen2Collections | Zbieranie elementów bezużytecznych generacji 2 | Liczba | Łącznie
Tak | Microsoft.Web/sites | Realizuj | Liczba dojść | Liczba | Średnia
Tak | Microsoft.Web/sites | HealthCheckStatus | Stan sprawdzania kondycji | Liczba | Średnia
Tak | Microsoft.Web/sites | Http101 | Http 101 | Liczba | Łącznie
Tak | Microsoft.Web/sites | Http2xx | Http 2xx | Liczba | Łącznie
Tak | Microsoft.Web/sites | Http3xx | Http 3xx | Liczba | Łącznie
Tak | Microsoft.Web/sites | Http401 | HTTP 401 | Liczba | Łącznie
Tak | Microsoft.Web/sites | Http403 | HTTP 403 | Liczba | Łącznie
Tak | Microsoft.Web/sites | Http404 | HTTP 404 | Liczba | Łącznie
Tak | Microsoft.Web/sites | Http406 | Http 406 | Liczba | Łącznie
Tak | Microsoft.Web/sites | Http4xx | Http 4xx | Liczba | Łącznie
Tak | Microsoft.Web/sites | Http5xx | Błędy serwera http | Liczba | Łącznie
Tak | Microsoft.Web/sites | HttpResponseTime | Czas odpowiedzi | Sekundy | Średnia
Tak | Microsoft.Web/sites | IoOtherBytesPerSecond | Inne bajty we/wy na sekundę | BytesPerSecond | Łącznie
Tak | Microsoft.Web/sites | IoOtherOperationsPerSecond | Inne operacje we/wy na sekundę | BytesPerSecond | Łącznie
Tak | Microsoft.Web/sites | IoReadBytesPerSecond | Bajty odczytu we/wy na sekundę | BytesPerSecond | Łącznie
Tak | Microsoft.Web/sites | IoReadOperationsPerSecond | Operacje odczytu we/wy na sekundę | BytesPerSecond | Łącznie
Tak | Microsoft.Web/sites | IoWriteBytesPerSecond | Bajty zapisu we/wy na sekundę | BytesPerSecond | Łącznie
Tak | Microsoft.Web/sites | IoWriteOperationsPerSecond | Operacje zapisu we/wy na sekundę | BytesPerSecond | Łącznie
Tak | Microsoft.Web/sites | MemoryWorkingSet | Zestaw roboczy pamięci | Bajty | Średnia
Tak | Microsoft.Web/sites | PrivateBytes | Bajty prywatne | Bajty | Średnia
Tak | Microsoft.Web/sites | Żądania | Żądania | Liczba | Łącznie
Tak | Microsoft.Web/sites | RequestsInApplicationQueue | Żądania w kolejce aplikacji | Liczba | Średnia
Tak | Microsoft.Web/sites | Wątki | Liczba wątków | Liczba | Średnia
Tak | Microsoft.Web/sites | TotalAppDomains | Łączna liczba domen aplikacji | Liczba | Średnia
Tak | Microsoft.Web/sites | TotalAppDomainsUnloaded | Całkowita liczba zwolnionych domen aplikacji | Liczba | Średnia
Tak | Microsoft.Web/sites/slots | AppConnections | Połączenia | Liczba | Średnia
Tak | Microsoft.Web/sites/slots | AverageMemoryWorkingSet | Średni zestaw roboczy pamięci | Bajty | Średnia
Tak | Microsoft.Web/sites/slots | AverageResponseTime | Średni czas odpowiedzi | Sekundy | Średnia
Tak | Microsoft.Web/sites/slots | BytesReceived | Dane w | Bajty | Łącznie
Tak | Microsoft.Web/sites/slots | BytesSent | Dane wychodzące | Bajty | Łącznie
Tak | Microsoft.Web/sites/slots | CpuTime | Czas procesora CPU | Sekundy | Łącznie
Tak | Microsoft.Web/sites/slots | CurrentAssemblies | Bieżące zestawy | Liczba | Średnia
Tak | Microsoft.Web/sites/slots | FunctionExecutionCount | Liczba wykonań funkcji | Liczba | Łącznie
Tak | Microsoft.Web/sites/slots | FunctionExecutionUnits | Jednostki wykonywania funkcji | Liczba | Łącznie
Tak | Microsoft.Web/sites/slots | Gen0Collections | Zbieranie elementów bezużytecznych generacji 0 | Liczba | Łącznie
Tak | Microsoft.Web/sites/slots | Gen1Collections | Wyrzucanie elementów bezużytecznych generacji 1 | Liczba | Łącznie
Tak | Microsoft.Web/sites/slots | Gen2Collections | Zbieranie elementów bezużytecznych generacji 2 | Liczba | Łącznie
Tak | Microsoft.Web/sites/slots | Realizuj | Liczba dojść | Liczba | Średnia
Tak | Microsoft.Web/sites/slots | HealthCheckStatus | Stan sprawdzania kondycji | Liczba | Średnia
Tak | Microsoft.Web/sites/slots | Http101 | Http 101 | Liczba | Łącznie
Tak | Microsoft.Web/sites/slots | Http2xx | Http 2xx | Liczba | Łącznie
Tak | Microsoft.Web/sites/slots | Http3xx | Http 3xx | Liczba | Łącznie
Tak | Microsoft.Web/sites/slots | Http401 | HTTP 401 | Liczba | Łącznie
Tak | Microsoft.Web/sites/slots | Http403 | HTTP 403 | Liczba | Łącznie
Tak | Microsoft.Web/sites/slots | Http404 | HTTP 404 | Liczba | Łącznie
Tak | Microsoft.Web/sites/slots | Http406 | Http 406 | Liczba | Łącznie
Tak | Microsoft.Web/sites/slots | Http4xx | Http 4xx | Liczba | Łącznie
Tak | Microsoft.Web/sites/slots | Http5xx | Błędy serwera http | Liczba | Łącznie
Tak | Microsoft.Web/sites/slots | HttpResponseTime | Czas odpowiedzi | Sekundy | Średnia
Tak | Microsoft.Web/sites/slots | IoOtherBytesPerSecond | Inne bajty we/wy na sekundę | BytesPerSecond | Łącznie
Tak | Microsoft.Web/sites/slots | IoOtherOperationsPerSecond | Inne operacje we/wy na sekundę | BytesPerSecond | Łącznie
Tak | Microsoft.Web/sites/slots | IoReadBytesPerSecond | Bajty odczytu we/wy na sekundę | BytesPerSecond | Łącznie
Tak | Microsoft.Web/sites/slots | IoReadOperationsPerSecond | Operacje odczytu we/wy na sekundę | BytesPerSecond | Łącznie
Tak | Microsoft.Web/sites/slots | IoWriteBytesPerSecond | Bajty zapisu we/wy na sekundę | BytesPerSecond | Łącznie
Tak | Microsoft.Web/sites/slots | IoWriteOperationsPerSecond | Operacje zapisu we/wy na sekundę | BytesPerSecond | Łącznie
Tak | Microsoft.Web/sites/slots | MemoryWorkingSet | Zestaw roboczy pamięci | Bajty | Średnia
Tak | Microsoft.Web/sites/slots | PrivateBytes | Bajty prywatne | Bajty | Średnia
Tak | Microsoft.Web/sites/slots | Żądania | Żądania | Liczba | Łącznie
Tak | Microsoft.Web/sites/slots | RequestsInApplicationQueue | Żądania w kolejce aplikacji | Liczba | Średnia
Tak | Microsoft.Web/sites/slots | Wątki | Liczba wątków | Liczba | Średnia
Tak | Microsoft.Web/sites/slots | TotalAppDomains | Łączna liczba domen aplikacji | Liczba | Średnia
Tak | Microsoft.Web/sites/slots | TotalAppDomainsUnloaded | Całkowita liczba zwolnionych domen aplikacji | Liczba | Średnia
