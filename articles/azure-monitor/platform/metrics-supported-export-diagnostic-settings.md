---
title: Metryki platformy Azure Monitor można eksportować za pomocą ustawień diagnostycznych
description: Lista metryk dostępnych dla każdego typu zasobu za pomocą usługi Azure Monitor.
services: azure-monitor
ms.topic: reference
ms.date: 03/30/2020
ms.subservice: metrics
ms.openlocfilehash: 6be8cb1b7e74301d16a1174f5ca2b774334dac3f
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422109"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Metryki platformy Azure Monitor można eksportować za pomocą ustawień diagnostycznych

Usługa Azure Monitor domyślnie udostępnia [metryki platformy](data-platform-metrics.md) bez konfiguracji. Udostępnia kilka sposobów interakcji z metrykami platformy, w tym wykresów ich w portalu, uzyskiwanie do nich dostępu za pośrednictwem interfejsu API REST lub wykonywanie zapytań o nie za pomocą programu PowerShell lub interfejsu wiersza polecenia. Zobacz [metryki obsługiwane](metrics-supported.md) dla pełnej listy metryk platformy obecnie dostępne z potoku metryk skonsolidowanych usługi Azure Monitor. Aby zapytać o te dane i uzyskać do nich dostęp, użyj [wersji interfejsu api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Inne metryki mogą być dostępne w portalu lub przy użyciu starszych interfejsów API.

Metryki platformy można wyeksportować z potoku monitora platformy Azure do innych lokalizacji na jeden z dwóch sposobów.
1. Używanie [ustawień diagnostycznych](diagnostic-settings.md) do wysyłania do usługi Log Analytics, Event Hubs lub Usługi Azure Storage.
2. Korzystanie z [interfejsu API rest metryki](https://docs.microsoft.com/rest/api/monitor/metrics/list)

Ze względu na zawiłości w wewnętrznej bazy danych usługi Azure Monitor nie wszystkie metryki są eksportowane przy użyciu ustawień diagnostycznych. Poniższa tabela zawiera listę, które można i nie mogą być eksportowane przy użyciu ustawień diagnostycznych.

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>Zmiana zachowania dla nulls i zero wartości 
 
Dla metryk platformy, które mogą być eksportowane za pomocą ustawień diagnostycznych, istnieje kilka metryk, dla których usługa Azure Monitor interpretuje "0s" jako "Nulls". Spowodowało to pewne zamieszanie między rzeczywistym "0s" (emitowane przez zasób) i interpretowane "0s" (Nulls). Wkrótce nastąpi zmiana, a metryki platformy eksportowane za pomocą ustawień diagnostycznych nie będą już eksportować "0", chyba że zostały naprawdę wyemitowane przez zasób bazowy. Zmiana została zaplanowana na 1 kwietnia 2020 r., ale została opóźniona z powodu zmian priorytetowych spowodowanych covid-19. 

Uwaga:

1.  Jeśli usuniesz grupę zasobów lub określony zasób, dane metryki z zrealizowanych zasobów nie będą już wysyłane do celów eksportu ustawień diagnostycznych. Oznacza to, że nie będzie już wyświetlany w centrach zdarzeń, kontach magazynu i obszarach roboczych analizy dzienników.
2.  Poprawa ta będzie dostępna we wszystkich chmurach publicznych i prywatnych.
3.  Ta zmiana nie wpłynie na zachowanie żadnego z następujących środowisk: 
   - Dzienniki zasobów platformy eksportowane za pomocą ustawień diagnostycznych
   - Wykresy metryk w Eksploratorze metryk
   - Alerty dotyczące metryk platformy
 
## <a name="metrics-exportable-table"></a>Tabela eksportowania metryk 

Tabela zawiera następujące kolumny. 
- Można wyeksportować za pomocą ustawień diagnostycznych? 
- Dokonane przez NULL / 0 
- ResourceType 
- Metryka 
- Nazwa metryczna
- Jednostka 
- Aggregationtype


> [!NOTE]
> Poniższa tabela może mieć poziomy pasek przewijania u dołu. Jeśli uważasz, że brakuje informacji, sprawdź, czy pasek przewijania znajduje się aż po lewej stronie.  


| Można wyeksportować za pomocą ustawień diagnostycznych?  | Już emitują NULLs |  ResourceType  |  Metryka  |  Nazwa metryczna  |  Jednostka  |  Aggregationtype | 
|---|---| ---- | ----- | ------ | ---- | ---- | 
| Tak****  | Nie |  Microsoft.AnalysisSługa/serwery  |  CleanerCurrentPrice  |  Pamięć: Czystsza aktualna cena  |  Liczba  |  Średnia | 
| Tak****  | Nie |  Microsoft.AnalysisSługa/serwery  |  CleanerMemoryNiejmkliwi  |  Pamięć: Czystsza pamięć nierozskawalna  |  Bajty  |  Średnia | 
| Tak****  | Nie |  Microsoft.AnalysisSługa/serwery  |  CleanerMemoryShrinkable  |  Pamięć: Czystsza pamięć z kurczliwa  |  Bajty  |  Średnia | 
| Tak****  | Nie |  Microsoft.AnalysisSługa/serwery  |  CommandPoolBusyThreads (PoleceniaPoolBusyThreads)  |  Wątki: Wątki zajęty puli poleceń  |  Liczba  |  Średnia | 
| Tak****  | Nie |  Microsoft.AnalysisSługa/serwery  |  CommandPoolIdleThreads (PoleceniaPoolIdleThreads)  |  Wątki: Wątki bezczynności puli poleceń  |  Liczba  |  Średnia | 
| Tak****  | Nie |  Microsoft.AnalysisSługa/serwery  |  CommandPoolJobQueueLength (PoleceniePoolJobQueueLength)  |  Długość kolejki zadania puli poleceń  |  Liczba  |  Średnia | 
| Tak****  | Nie |  Microsoft.AnalysisSługa/serwery  |  CurrentConnections (Połączenia prądowe)  |  Połączenie: Bieżące połączenia  |  Liczba  |  Średnia | 
| Tak****  | Nie |  Microsoft.AnalysisSługa/serwery  |  CurrentUserSessions  |  Bieżące sesje użytkowników  |  Liczba  |  Średnia | 
| Tak****  | Nie |  Microsoft.AnalysisSługa/serwery  |  LongParsingBusyThreads  |  Wątki: Długie analizowanie zajętych wątków  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  LongParsingIdleTyty  |  Wątki: Długie parsowanie bezczynnych wątków  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  LongParsingJobQueueLength  |  Wątki: Długa długość kolejki zadań analizowania  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  mashup_engine_memory_metric  |  Pamięć silnika M  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  mashup_engine_private_bytes_metric  |  Prywatne bajty silnika M  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  mashup_engine_qpu_metric  |  Silnik M QPU  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  mashup_engine_virtual_bytes_metric  |  Wirtualne bajty silnika M  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  memory_metric  |  Memory (Pamięć)  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  memory_thrashing_metric  |  Przeładowywanie pamięci  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  MemoryLimitHard (Funkcja MemoryLimitHard)  |  Pamięć: Czas ogranicznik pamięci  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  MemoryLimitHigh  |  Pamięć: Limit pamięci wysoki  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  MemoryLimitLow (Funkcja MemoryLimitLow)  |  Pamięć: Niski limit pamięci  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  MemoryLimitVertiPaq  |  Pamięć: VertiPaq limitu pamięci  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  MemoryUsage (Narzędzie pamięci)  |  Pamięć: Użycie pamięci  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  private_bytes_metric  |  Bajty prywatne  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  ProcessingPoolBusyIOJobTyty  |  Wątki: Przetwarzanie puli zajętych wątków zadań we/wy  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  ProcessingPoolBusyNonIOTyty  |  Wątki: Przetwarzanie puli zajętych wątków innych niż We/Wy  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  ProcessingPoolIdleIOJobThreads  |  Wątki: Przetwarzanie wątków zadań bezczynności puli bezczynności we/wy  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  ProcessingPoolIdleNonIOTyty  |  Wątki: Przetwarzanie bezczynności wątków bezczynności puli  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  ProcessingPoolIOJobQueueLength  |  Wątki: Długość kolejki we/wy puli przetwarzania  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  PrzetwarzaniePoolJobQueueLength  |  Długość kolejki zadania puli przetwarzania  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  qpu_metric  |  Funkcja QPU  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  QueryPoolBusyThreads  |  Wątki zajętych puli zapytań  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  QueryPoolIdleThreads  |  Wątki: wątki bezczynności puli zapytań  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  QueryPoolJobQueueLength  |  Wątki: Długość kolejki zadań puli zapytań  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  Limit przydziału  |  Pamięć: Przydział  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  Blokada przydziału  |  Pamięć: Przydział zablokowany  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  WierszeConvertedPerSec  |  Przetwarzanie: wiersze konwertowane na sekundę  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  WierszeReadPerSec  |  Przetwarzanie: wiersze odczytywane na sekundę  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  WierszeWrittenPerSec  |  Przetwarzanie: wiersze zapisane na sekundę  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  ShortParsingBusyThreads  |  Wątki: Krótkie analizowanie zajętych wątków  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  ShortParsingIdleThreads  |  Wątki: Krótkie parsowanie bezczynnych wątków  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  ShortParsingJobQueueLength  |  Wątki: Krótka długość kolejki zadań analizowania  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  SuccessfullConnectionsPerSec  |  Pomyślne połączenia na sekundę  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  TotalConnectionFailures (Całkowita liczba połączeń)  |  Całkowita liczba niepowodzeń połączeń  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  Wnioski o ponowne połączenie ogółem  |  Łączna liczba żądań połączeń  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  VertiPaqNonpaged ( VertiPaqNonpaged )  |  Pamięć: VertiPaq Nonpaged  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  VertiPaqPaged ( VertiPaqPaged )  |  Pamięć: VertiPaq Paged  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AnalysisSługa/serwery  |  virtual_bytes_metric  |  Bajty wirtualne  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  Cofanie wsteczneDuration  |  Czas trwania żądań wewnętrznej bazy danych  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ApiManagement/service  |  Pojemność  |  Pojemność  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  Czas trwania  |  Całkowity czas trwania żądań bramy  |  Milisekund  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  EventHubDroppedWydarzenia  |  Porzucone zdarzenia EventHub  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  EventHubRejectedWydarzenia  |  Odrzucone zdarzenia EventHub  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  EventHubSuccessfulEvents  |  Udane wydarzenia EventHub  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  EventHubThrottledWydarzenia  |  Ograniczone zdarzenia EventHub  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  EventHubTimedoutEvents  |  Zdarzenia EventHub z przesunąłem czas  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  Usługa EventHubTotalBytesSent  |  Rozmiar zdarzeń EventHub  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  EventHubTotalEvents  |  Łączna liczba zdarzeń EventHub  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  EventHubTotalFailedAjnieje  |  Nieudane zdarzenia EventHub  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  Nieudane zapisy  |  Żądania bramy nie powiodły się (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  InneRequests  |  Inne żądania bramy (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  Żądania  |  Żądania  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  Udane prośby  |  Pomyślne żądania bramy (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  TotalRequests  |  Całkowita liczba żądań bramy (przestarzała)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  Nieautoryzowanezapytania  |  Nieautoryzowane żądania bramy (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.AppPlatform/Wiosna  |  AppCpuUsagePercentage  |  Procent użycia procesora CPU aplikacji  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AppPlatform/Wiosna  |  AppMemoryZatwierdzone  |  Przypisana pamięć aplikacji  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AppPlatform/Wiosna  |  AppMemoryMax (AppMemoryMax)  |  Maksymalna ilość pamięci aplikacji  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.AppPlatform/Wiosna  |  AppMemoryUżywane  |  Używana pamięć aplikacji  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AppPlatform/Wiosna  |  GCPauseTotalCount  |  Liczba wstrzymania GC  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.AppPlatform/Wiosna  |  GCPauseTotalTime  |  Całkowity czas pauzy GC  |  Milisekund  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.AppPlatform/Wiosna  |  MaxOldGenMemoryBajki  |  Maksymalny dostępny rozmiar danych starej generacji  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AppPlatform/Wiosna  |  OldGenMemoryBajki  |  Rozmiar danych starej generacji  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AppPlatform/Wiosna  |  OldGenPromotedBytes ( OldGenPromotedBytes )  |  Promowanie rozmiaru danych starej generacji  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.AppPlatform/Wiosna  |  SystemCpuUsagePercentage  |  Procent użycia procesora CPU systemu  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AppPlatform/Wiosna  |  TomcatErrorCount  |  Błąd globalny Tomcat  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.AppPlatform/Wiosna  |  TomcatReceivedBytes  |  Tomcat Całkowita liczba odebranych bajtów  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.AppPlatform/Wiosna  |  TomcatRequestMaxTime  |  Tomcat Wniosek Maksymalny czas  |  Milisekund  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.AppPlatform/Wiosna  |  TomcatRequestTotalCount  |  Całkowita liczba żądań Tomcat  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.AppPlatform/Wiosna  |  TomcatRequestTotalCzas  |  Tomcat Żądanie Całkowity czas  |  Milisekund  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.AppPlatform/Wiosna  |  TomcatResponseAvgCzas  |  Tomcat żąda średni czas  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.AppPlatform/Wiosna  |  TomcatsentBytes (TomcatSentBytes)  |  Tomcat całkowita wysłana bajty  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.AppPlatform/Wiosna  |  TomcatSessionActiveCurrentCount  |  Tomcat Sesja Żywa Liczba  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.AppPlatform/Wiosna  |  TomcatSessionActiveMaxCount  |  Tomcat Session Max Aktywna liczba  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.AppPlatform/Wiosna  |  TomcatSessionAliveMaxTime  |  Tomcat Sesja Max Alive Czas  |  Milisekund  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.AppPlatform/Wiosna  |  TomcatSessionCreatedCount  |  Liczba utworów sesji Tomcat  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.AppPlatform/Wiosna  |  TomcatSessionExpiredCount  |  Liczba wygasłych sesji Tomcat  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.AppPlatform/Wiosna  |  TomcatSessionRejectedCount  |  Liczba odrzuconych sesji Tomcat  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.AppPlatform/Wiosna  |  YoungGenPromotedBytes  |  Promuj rozmiar danych młodego pokolenia  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Automation/automationKontacje  |  TotalJob (TotalJob)  |  Łączna liczba miejsc pracy  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Automation/automationKontacje  |  TotalUpdateDeploymentMachineRuns  |  Łączna liczba przebiegów wdrażania aktualizacji maszyny  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Automation/automationKontacje  |  TotalUpdateDeploymentRuns (Liczbarunów rozmieszczenia totalupdate  |  Łączna liczba przebiegów wdrażania aktualizacji  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchKonkludy  |  Liczba rdzeni  |  Dedykowana liczba rdzeni  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchKonkludy  |  Tworzenie KontaNodeCount  |  Tworzenie liczby węzłów  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchKonkludy  |  Liczba IdleNodeCount  |  Liczba węzłów bezczynnych  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchKonkludy  |  JobDeleteCompleteEVent  |  Zdarzenia zakończenia usuwania zadania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchKonkludy  |  JobDeleteStartEvent (ZadanieDeletStartEvent)  |  Zdarzenia rozpoczęcia usuwania zadań  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchKonkludy  |  JobDisableCompleteEVent (JobDisableCompleteEvent)  |  Zadanie wyłączanie zakończonych zdarzeń  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchKonkludy  |  JobDisableStartEvent (ZadanieDisableStartEvent)  |  Wyłączanie zdarzeń startowych zadania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchKonkludy  |  JobStartEvent (Początek zadania)  |  Zdarzenia rozpoczęcia zadania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchKonkludy  |  JobTerminateCompleteEvent (JobTerminateCompleteEvent)  |  Zakończenie zadania zdarzenia zakończone  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchKonkludy  |  JobTerminateStartEvent (ZadanieTerminateStartEvent)  |  Zakończenie zadania zdarzenia startowe  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchKonkludy  |  LeavingPoolNodeCount  |  Opuszczanie liczby węzłów puli  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchKonkludy  |  Licznik NiskipriorityCoreCount  |  Liczba rdzeni o niskiej pozycjności  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchKonkludy  |  Liczba plików offline  |  Liczba węzłów w trybie offline  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchKonkludy  |  PoolCreateEvent (Twórstwo na basenie)  |  Zdarzenia tworzenia puli  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchKonkludy  |  PoolDeleteCompleteEVent  |  Zdarzenia usuwania puli  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchKonkludy  |  Impreza PoolDeleteStartEvent  |  Zdarzenia rozpoczęcia usuwania puli  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchKonkludy  |  Impreza PoolResizeCompleteEvent  |  Zdarzenia ukończenia ponownego rozmiaru rozmiaru puli  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchKonkludy  |  Proces poolresizestartevent  |  Zdarzenia rozpoczęcia ponownego rozmiaru puli  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchKonkludy  |  WywłaszczoneNodeCount  |  Wywłaszczona liczba węzłów  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchKonkludy  |  Ponowne uruchamianienodecount  |  Ponowna liczba węzłów  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchKonkludy  |  ReimagingNodeCount (Liczba reimagingnodecount)  |  Liczba węzłów reimaging  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchKonkludy  |  RunningNodeCount (RunningNodeCount)  |  Liczba uruchomionych węzłów  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchKonkludy  |  StartingNodeCount  |  Liczba początkowa węzłów  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchKonkludy  |  StartTaskFailedNodeCount  |  Uruchom liczbę węzłów nie powiodło się zadania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchKonkludy  |  ZadanieZamyknięcie  |  Zdarzenia ukończenia zadania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchKonkludy  |  ZadanieFailEvent (Zadanie)  |  Zdarzenia niepowodzenia zadania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Batch/batchKonkludy  |  ZadanieStartEvent  |  Zdarzenia rozpoczęcia zadania  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchKonkludy  |  TotalLowPriorityNodeCount  |  Liczba węzłów o niskim priorytecie  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchKonkludy  |  TotalNodeCount (Liczba Całkowita)  |  Liczba dedykowanych węzłów  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchKonkludy  |  BezużytecznyNodeCount  |  Liczba węzłów bezużytecznych  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Batch/batchKonkludy  |  WaitingForStartTaskNodeCount  |  Oczekiwanie na uruchomienie liczby węzłów zadań  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.BatchAI/obszary robocze  |  Aktywne rdzenie  |  Aktywne rdzenie  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.BatchAI/obszary robocze  |  Aktywne węzły  |  Aktywne węzły  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.BatchAI/obszary robocze  |  Rdzenie bezczynne  |  Rdzenie bezczynne  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.BatchAI/obszary robocze  |  Węzły bezczynne  |  Węzły bezczynne  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.BatchAI/obszary robocze  |  Zadanie zakończone  |  Zadanie zakończone  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.BatchAI/obszary robocze  |  Przesłane zadanie  |  Przesłane zadanie  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.BatchAI/obszary robocze  |  Opuszczanie rdzeni  |  Opuszczanie rdzeni  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.BatchAI/obszary robocze  |  Opuszczanie węzłów  |  Opuszczanie węzłów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.BatchAI/obszary robocze  |  Wywłaszczone rdzenie  |  Wywłaszczone rdzenie  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.BatchAI/obszary robocze  |  Wstępnie wywłaszczone węzły  |  Wstępnie wywłaszczone węzły  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.BatchAI/obszary robocze  |  Procent wykorzystania przydziału  |  Procent wykorzystania przydziału  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.BatchAI/obszary robocze  |  Całkowita liczba rdzeni  |  Całkowita liczba rdzeni  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.BatchAI/obszary robocze  |  Łączna liczba węzłów  |  Łączna liczba węzłów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.BatchAI/obszary robocze  |  Niezniszalne rdzenie  |  Niezniszalne rdzenie  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.BatchAI/obszary robocze  |  Niezniszalne węzły  |  Niezniszalne węzły  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Blockchain/blockchainCzłonek  |  PołączenieAkceptowane  |  Akceptowane połączenia  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Blockchain/blockchainCzłonek  |  ConnectionActive (Aktywny połączenie)  |  Aktywne połączenia  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Blockchain/blockchainCzłonek  |  PołączenieHandled  |  Obsługiwane połączenia  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Blockchain/blockchainCzłonek  |  CpuUsagePercentageInDouble  |  Procent użycia procesora CPU  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Blockchain/blockchainCzłonek  |  IOReadBytes (IOReadBytes)  |  Bajty odczytu we/wy  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Blockchain/blockchainCzłonek  |  Bajty IOWrite  |  Bajty zapisu we/wy  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Blockchain/blockchainCzłonek  |  Memorylimit  |  Limit pamięci  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Blockchain/blockchainCzłonek  |  MemoryUsage (Narzędzie pamięci)  |  Użycie pamięci  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Blockchain/blockchainCzłonek  |  MemoryUsagePercentageInDouble  |  Procent użycia pamięci  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Blockchain/blockchainCzłonek  |  OczekująceTransakcje  |  Oczekujące transakcje  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Blockchain/blockchainCzłonek  |  ProcessedBlocks  |  Przetworzone bloki  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Blockchain/blockchainCzłonek  |  PrzetworzoneTransakcje  |  Przetworzone transakcje  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Blockchain/blockchainCzłonek  |  Usługi w kolejce  |  Transakcje w kolejce  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Blockchain/blockchainCzłonek  |  RequestHandled (Nieobsługiwowany)  |  Obsługiwane żądania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Blockchain/blockchainCzłonek  |  StorageUsage  |  Użycie pamięci masowej  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cachehits (cachehits)  |  Trafienia w pamięci podręcznej  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cachehits0  |  Trafienia w pamięci podręcznej (odłamek 0)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cachehits1  |  Trafienia w pamięci podręcznej (odłamek 1)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cachehits2  |  Trafienia w pamięci podręcznej (odłamek 2)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cachehits3  |  Trafienia w pamięci podręcznej (odłamek 3)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cachehits4  |  Trafienia w pamięci podręcznej (odłamek 4)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cachehits5  |  Trafienia w pamięci podręcznej (odłamek 5)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cachehits6  |  Trafienia w pamięci podręcznej (odłamek 6)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cachehits7  |  Trafienia w pamięci podręcznej (odłamek 7)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cachehits8  |  Trafienia w pamięci podręcznej (odłamek 8)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cachehits9  |  Trafienia w pamięci podręcznej (odłamek 9)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cacheLatency (CacheLatency)  |  Mikrosekundy opóźnienia pamięci podręcznej (wersja zapoznawcza)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cachemisses  |  Chybienia w pamięci podręcznej  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cachemisses0  |  Chybienia pamięci podręcznej (odłamek 0)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cachemisses1  |  Chybienia w pamięci podręcznej (odłamek 1)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cachemisses2  |  Chybienia w pamięci podręcznej (odłamek 2)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cachemisses3  |  Chybienia w pamięci podręcznej (odłamek 3)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cachemisses4  |  Chybienia w pamięci podręcznej (odłamek 4)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cachemisses5  |  Chybienia w pamięci podręcznej (odłamek 5)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cachemisses6  |  Chybienia w pamięci podręcznej (odłamek 6)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cachemisses7  |  Chybienia w pamięci podręcznej (odłamek 7)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cachemisses8  |  Chybienia w pamięci podręcznej (odłamek 8)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cachemisses9  |  Chybienia w pamięci podręcznej (odłamek 9)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cacheCzytaj  |  Odczyt pamięci podręcznej  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cacheRead0  |  Odczyt pamięci podręcznej (fragment 0)  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  pamięć podręcznaCzytaj1  |  Odczyt pamięci podręcznej (fragment 1)  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  pamięć podręcznaCzytaj2  |  Odczyt pamięci podręcznej (fragment 2)  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  pamięć cacheRead3  |  Odczyt pamięci podręcznej (fragment 3)  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  pamięć cacheRead4  |  Odczyt pamięci podręcznej (fragment 4)  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  pamięć podręcznaCzytaj5  |  Odczyt pamięci podręcznej (fragment 5)  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  pamięć cacheRead6  |  Odczyt pamięci podręcznej (fragment 6)  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  pamięć podręcznaCzytaj7  |  Odczyt pamięci podręcznej (fragment 7)  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  pamięć podręcznaCzytaj8  |  Odczyt pamięci podręcznej (fragment 8)  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  pamięć cacheRead9  |  Odczyt pamięci podręcznej (fragment 9)  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cacheWrite (buforowanie)  |  Zapis w pamięci podręcznej  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cacheWrite0  |  Zapis pamięci podręcznej (fragment 0)  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cacheWrite1  |  Zapis pamięci podręcznej (fragment 1)  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cacheWrite2  |  Zapis pamięci podręcznej (fragment 2)  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cacheWrite3  |  Zapis pamięci podręcznej (fragment 3)  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cacheWrite4  |  Zapis pamięci podręcznej (fragment 4)  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cacheWrite5  |  Zapis pamięci podręcznej (fragment 5)  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cacheWrite6  |  Zapis pamięci podręcznej (fragment 6)  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cacheWrite7  |  Zapis pamięci podręcznej (fragment 7)  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cacheWrite8  |  Zapis pamięci podręcznej (fragment 8)  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  cacheWrite9  |  Zapis pamięci podręcznej (fragment 9)  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  podłączoneclients  |  Podłączeni klienci  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  connectedclients0  |  Połączeni klienci (odłamek 0)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  connectedclients1  |  Połączeni klienci (odłamek 1)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  connectedclients2  |  Połączeni klienci (odłamek 2)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  connectedclients3  |  Połączeni klienci (odłamek 3)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  connectedclients4  |  Połączeni klienci (odłamek 4)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  connectedclients5  |  Połączeni klienci (odłamek 5)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  connectedclients6  |  Połączeni klienci (odłamek 6)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  connectedclients7  |  Połączeni klienci (odłamek 7)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  connectedclients8  |  Połączeni klienci (odłamek 8)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  connectedclients9  |  Połączeni klienci (odłamek 9)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  błędy  |  Errors  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  eksmitowane klucze  |  Eksmitowane klucze  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  eksmitowany klucze0  |  Eksmitowane klucze (odłamek 0)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  eksmitowane klucze1  |  Eksmitowane klucze (odłamek 1)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  eksmitowanykeys2  |  Eksmitowane klucze (odłamek 2)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  eksmitowanykeys3  |  Eksmitowane klucze (odłamek 3)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  eksmitowanykeys4  |  Eksmitowane klucze (odłamek 4)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  eksmitowane klucze5  |  Eksmitowane klucze (odłamek 5)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  eksmitowane klucze6  |  Eksmitowane klucze (odłamek 6)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  eksmitowane klucze7  |  Eksmitowane klucze (odłamek 7)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  eksmitowane klucze8  |  Eksmitowane klucze (odłamek 8)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  eksmitowane klucze9  |  Eksmitowane klucze (odłamek 9)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  expiredkeys  |  Wygasłe klucze  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  expiredkeys0  |  Wygasłe klucze (odłamek 0)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  expiredkeys1  |  Wygasłe klucze (odłamek 1)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  expiredkeys2  |  Wygasłe klucze (odłamek 2)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  expiredkeys3  |  Wygasłe klucze (odłamek 3)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  expiredkeys4  |  Wygasłe klucze (odłamek 4)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  expiredkeys5  |  Wygasłe klucze (odłamek 5)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  expiredkeys6  |  Wygasłe klucze (odłamek 6)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  expiredkeys7  |  Wygasłe klucze (odłamek 7)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  expiredkeys8  |  Wygasłe klucze (odłamek 8)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  expiredkeys9  |  Wygasłe klucze (odłamek 9)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  getcommands ( getcommands )  |  Pobiera  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  getcommands0  |  Pobiera (odłamek 0)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  getcommands1  |  Pobiera (odłamek 1)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  getcommands2  |  Pobiera (odłamek 2)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  getcommands3  |  Pobiera (odłamek 3)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  getcommands4  |  Pobiera (odłamek 4)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  getcommands5  |  Pobiera (odłamek 5)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  getcommands6  |  Pobiera (odłamek 6)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  getcommands7  |  Pobiera (odłamek 7)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  getcommands8  |  Pobiera (odłamek 8)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  getcommands9  |  Pobiera (odłamek 9)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  operacjePerSekunda  |  Operacje na sekundę  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  operacjePerSecond0  |  Operacje na sekundę (odłamek 0)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  operacjePerSecond1  |  Operacje na sekundę (odłamek 1)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  operacjePerSecond2  |  Operacje na sekundę (odłamek 2)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  operacjePerSecond3  |  Operacje na sekundę (odłamek 3)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  operacjePerSecond4  |  Operacje na sekundę (odłamek 4)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  operacjePerSecond5  |  Operacje na sekundę (odłamek 5)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  operacjePerSecond6  |  Operacje na sekundę (odłamek 6)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  operacjePerSecond7  |  Operacje na sekundę (odłamek 7)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  operacjePerSecond8  |  Operacje na sekundę (odłamek 8)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  operacjePerSecond9  |  Operacje na sekundę (odłamek 9)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  procentProcesorTime  |  Procesor CPU  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  procentProcesorCzas0  |  Procesor (odłamek 0)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  procentProcesorTime1  |  Procesor (odłamek 1)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  procentProcesorTime2  |  Procesor (odłamek 2)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  procentProcesorTime3  |  Procesor (odłamek 3)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  procentProcesorTime4  |  Procesor (odłamek 4)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  procentProcesorTime5  |  Procesor (odłamek 5)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  procentProcesorTime6  |  Procesor (odłamek 6)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  procentProcesorTime7  |  Procesor (odłamek 7)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  procentProcesorTime8  |  Procesor (odłamek 8)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  procentProcesorTime9  |  Procesor (odłamek 9)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  serverLoad (Obciążenie serwerem)  |  Obciążenie serwera  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  serverLoad0  |  Obciążenie serwera (fragment 0)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  serverLoad1  |  Obciążenie serwera (fragment 1)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  serverLoad2  |  Obciążenie serwera (fragment 2)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  serverLoad3  |  Obciążenie serwera (fragment 3)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  serverLoad4  |  Obciążenie serwera (fragment 4)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  serverLoad5  |  Obciążenie serwera (fragment 5)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  serverLoad6  |  Obciążenie serwera (fragment 6)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  serverLoad7  |  Obciążenie serwera (fragment 7)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  serverLoad8  |  Obciążenie serwera (fragment 8)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  serverLoad9  |  Obciążenie serwera (niezależnego fragmentu 9)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  setcommands (ustawianie pokańczów)  |  Zestawy  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  setcommands0 setcommands0 setcommands0 setcom  |  Zestawy (odłamek 0)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  setcommands1  |  Zestawy (odłamek 1)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  setcommands2  |  Zestawy (odłamek 2)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  setcommands3  |  Zestawy (odłamek 3)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  setcommands4  |  Zestawy (odłamek 4)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  setcommands5  |  Zestawy (odłamek 5)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  setcommands6  |  Zestawy (odłamek 6)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  setcommands7  |  Zestawy (odłamek 7)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  setcommands8  |  Zestawy (odłamek 8)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  setcommands9  |  Zestawy (odłamek 9)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  totalcommandsprocessed  |  Łączna liczba operacji  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  totalcommandsprocessed0  |  Całkowita liczba operacji (odłamek 0)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  totalcommandsprocessed1  |  Całkowita liczba operacji (odłamek 1)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  totalcommandsprocessed2  |  Całkowita liczba operacji (odłamek 2)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  totalcommandsprocessed3  |  Całkowita liczba operacji (odłamek 3)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  totalcommandsprocessed4  |  Całkowita liczba operacji (odłamek 4)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  totalcommandsprocessed5  |  Całkowita liczba operacji (odłamek 5)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  totalcommandsprocessed6  |  Całkowita liczba operacji (odłamek 6)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  totalcommandsprocessed7  |  Całkowita liczba operacji (odłamek 7)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  totalcommandsprocessed8  |  Całkowita liczba operacji (odłamek 8)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  totalcommandsprocessed9  |  Całkowita liczba operacji (odłamek 9)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  totalkeys (całkowita liczba kluczy)  |  Całkowita liczba kluczy  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  totalkeys0  |  Całkowita liczba kluczy (odłamek 0)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  totalkeys1  |  Całkowita liczba kluczy (odłamek 1)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  totalkeys2  |  Całkowita liczba kluczy (odłamek 2)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  totalkeys3  |  Całkowita liczba kluczy (odłamek 3)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  totalkeys4  |  Całkowita liczba kluczy (odłamek 4)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  totalkeys5  |  Całkowita liczba kluczy (odłamek 5)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  totalkeys6  |  Całkowita liczba kluczy (odłamek 6)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  totalkeys7  |  Całkowita liczba kluczy (odłamek 7)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  totalkeys8  |  Całkowita liczba kluczy (odłamek 8)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  totalkeys9  |  Całkowita liczba kluczy (odłamek 9)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  używanememory  |  Używana pamięć  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  używanememory0  |  Używana pamięć (odłamek 0)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  używanememory1  |  Używana pamięć (odłamek 1)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  używanememory2  |  Używana pamięć (odłamek 2)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  używanememory3  |  Używana pamięć (odłamek 3)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  używanememory4  |  Używana pamięć (odłamek 4)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  używanememory5  |  Używana pamięć (odłamek 5)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  używanememory6  |  Używana pamięć (odłamek 6)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  używanememory7  |  Używana pamięć (odłamek 7)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  używanememory8  |  Używana pamięć (odłamek 8)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  używanememory9  |  Używana pamięć (odłamek 9)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  usedmemorypercentage  |  Procent używanej pamięci  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  używane memoryrss  |  Używane pamięci RSS  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  używanememoryRss0  |  Używane pamięci RSS (odłamek 0)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  używanememoryRss1  |  Używane pamięci RSS (odłamek 1)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  używanememoryRss2  |  Używane pamięci RSS (odłamek 2)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  używanememoryRss3  |  Używane pamięci RSS (odłamek 3)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  używanememoryRss4  |  Używane pamięci RSS (odłamek 4)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  używanememoryRss5  |  Używane pamięci RSS (odłamek 5)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  używanememoryRss6  |  Używane pamięci RSS (odłamek 6)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  używanememoryRss7  |  Używane pamięci RSS (odłamek 7)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  używanememoryRss8  |  Używane pamięci RSS (odłamek 8)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Pamięć podręczna/redis firmy Microsoft.Cache  |  używanememoryRss9  |  Używane pamięci RSS (odłamek 9)  |  Bajty  |  Maksimum | 
| Nie  | Nie |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Bajty odczytu dysku/s  |  Odczyt dysku  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Operacje odczytu dysku/s  |  Operacje odczytu dysku/s  |  Połów połówek  |  Średnia | 
| Nie  | Nie |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Bajty zapisu dysku/s  |  Zapis dysku  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Operacje zapisu dysku/s  |  Operacje zapisu dysku/s  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Sieć — wejście  |  Sieć — wejście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Sieć — wyjście  |  Sieć — wyjście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Procentowe użycie procesora CPU  |  Procentowe użycie procesora CPU  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft.ClassicCompute/virtualMacyny  |  Bajty odczytu dysku/s  |  Odczyt dysku  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicCompute/virtualMacyny  |  Operacje odczytu dysku/s  |  Operacje odczytu dysku/s  |  Połów połówek  |  Średnia | 
| Nie  | Nie |  Microsoft.ClassicCompute/virtualMacyny  |  Bajty zapisu dysku/s  |  Zapis dysku  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicCompute/virtualMacyny  |  Operacje zapisu dysku/s  |  Operacje zapisu dysku/s  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicCompute/virtualMacyny  |  Sieć — wejście  |  Sieć — wejście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.ClassicCompute/virtualMacyny  |  Sieć — wyjście  |  Sieć — wyjście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.ClassicCompute/virtualMacyny  |  Procentowe użycie procesora CPU  |  Procentowe użycie procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Konta programu Microsoft.ClassicStorage/storageKonta  |  Dostępność  |  Dostępność  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Konta programu Microsoft.ClassicStorage/storageKonta  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Konta programu Microsoft.ClassicStorage/storageKonta  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Konta programu Microsoft.ClassicStorage/storageKonta  |  SuccessE2ELatency  |  Opóźnienie E2E dla powodzenia  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Konta programu Microsoft.ClassicStorage/storageKonta  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Konta programu Microsoft.ClassicStorage/storageKonta  |  Transakcje  |  Transakcje  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Konta programu Microsoft.ClassicStorage/storageKonta  |  UżywaneCapacity  |  Używana pojemność  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKondyfika/obiektdusługi  |  Dostępność  |  Dostępność  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft.ClassicStorage/storageKondyfika/obiektdusługi  |  BlobCapacity (BlobCapacity)  |  Pojemność obiektu blob  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft.ClassicStorage/storageKondyfika/obiektdusługi  |  Liczba obiektów BlobCount  |  Liczba obiektów blob  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKondyfika/obiektdusługi  |  Liczba kontenerów  |  Liczba kontenerów obiektów blob  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKondyfika/obiektdusługi  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft.ClassicStorage/storageKondyfika/obiektdusługi  |  Indeks Zdolności  |  Pojemność indeksu  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKondyfika/obiektdusługi  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKondyfika/obiektdusługi  |  SuccessE2ELatency  |  Opóźnienie E2E dla powodzenia  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKondyfika/obiektdusługi  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKondyfika/obiektdusługi  |  Transakcje  |  Transakcje  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKonta/fileServices  |  Dostępność  |  Dostępność  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKonta/fileServices  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft.ClassicStorage/storageKonta/fileServices  |  Zdolność do pończęcia plików  |  Pojemność pliku  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft.ClassicStorage/storageKonta/fileServices  |  Liczba plików  |  Liczba plików  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.ClassicStorage/storageKonta/fileServices  |  Liczba plików ShareCount  |  Liczba udziałów plików  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.ClassicStorage/storageKonta/fileServices  |  FileShareQuota (Polski)  |  Rozmiar przydziału udziału plików  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft.ClassicStorage/storageKonta/fileServices  |  Konto FileShareSnapshotCount  |  Liczba migawek udziału plików  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.ClassicStorage/storageKonta/fileServices  |  Rozmiar plikuShareSnapshotSize  |  Rozmiar migawki udziału plików  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKonta/fileServices  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKonta/fileServices  |  SuccessE2ELatency  |  Opóźnienie E2E dla powodzenia  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKonta/fileServices  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKonta/fileServices  |  Transakcje  |  Transakcje  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKonta/kolejkaUsługi  |  Dostępność  |  Dostępność  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKonta/kolejkaUsługi  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKonta/kolejkaUsługi  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKonta/kolejkaUsługi  |  QueueCapacity (Zdolność do kolejek)  |  Pojemność kolejki  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKonta/kolejkaUsługi  |  Liczba kolejek  |  Liczba kolejek  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKonta/kolejkaUsługi  |  KolejkaLiczna liczba  |  Liczba wiadomości w kolejce  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKonta/kolejkaUsługi  |  SuccessE2ELatency  |  Opóźnienie E2E dla powodzenia  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKonta/kolejkaUsługi  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKonta/kolejkaUsługi  |  Transakcje  |  Transakcje  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKonta/tableSługi  |  Dostępność  |  Dostępność  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKonta/tableSługi  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKonta/tableSługi  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKonta/tableSługi  |  SuccessE2ELatency  |  Opóźnienie E2E dla powodzenia  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKonta/tableSługi  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKonta/tableSługi  |  TableCapacity (Zdolność do pońoliczy)  |  Pojemność tabeli  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKonta/tableSługi  |  Liczba tabel  |  Liczba tabel  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKonta/tableSługi  |  Liczba entity tabeli  |  Liczba jednostek tabeli  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ClassicStorage/storageKonta/tableSługi  |  Transakcje  |  Transakcje  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  BlockedCalls (Zablokowane telefony)  |  Zablokowane połączenia  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  ZnakiSzkolone  |  Postacie wyszkolone  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  Znaki Przetłumaczone  |  Przetłumaczone znaki  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  KlientErrors (KlientErrors)  |  Błędy klienta  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  DataIn (wyjdą  |  Dane w  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  DataOut (DataOut)  |  Wyjście danych  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  Opóźnienie  |  Opóźnienie  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  Serwery serwerowe  |  Błędy serwera  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  MowaSessionDuration  |  Czas trwania sesji mowy  |  Sekundy  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  Udane rozmowy  |  Udane połączenia  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  Całkowita liczba powołów  |  Łączna liczba połączeń  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  TotalErrors ( TotalErrors )  |  Całkowita liczba błędów  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  TotalTokenCalls (Liczba powołów)  |  Łączna liczba wywołań tokenów  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.CognitiveServices/accounts  |  TotalTransakcje  |  Łączna liczba transakcji  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Zużyte kredyty CPU  |  Zużyte kredyty CPU  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Pozostałe kredyty CPU  |  Pozostałe kredyty CPU  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Głębokość kolejki dysku danych  |  Głębokość kolejki dysków danych (wersja zapoznawcza)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty odczytu dysku danych/s  |  Bajty odczytu dysku danych/s (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje odczytu dysku danych/s  |  Operacje odczytu dysku danych/s (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty zapisu dysku danych/s  |  Bajty zapisu dysku danych/s (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje zapisu na dysku danych/s  |  Operacje zapisu na dysku danych/s (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty odczytu dysku  |  Bajty odczytu dysku  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje odczytu dysku/s  |  Operacje odczytu dysku/s  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty zapisu dysku  |  Bajty zapisu dysku  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje zapisu dysku/s  |  Operacje zapisu dysku/s  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Przepływy przychodzące  |  Przepływy przychodzące  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Maksymalna szybkość tworzenia przepływów przychodzących  |  Maksymalna szybkość tworzenia przepływów przychodzących (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Sieć — wejście  |  Sieć w rozliczanej (przestarzała)  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Sieć w sumie  |  Sieć w sumie  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Sieć — wyjście  |  Wyjęty z sieci (przestarzały)  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Całkowita liczba wyjść sieci  |  Całkowita liczba wyjść sieci  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Głębokość kolejki dysku systemu operacyjnego  |  Głębokość kolejki systemu operacyjnego (wersja zapoznawcza)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty odczytu dysku systemu operacyjnego na sekundę  |  Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje odczytu dysku systemu operacyjnego/s  |  Operacje odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty zapisu dysku systemu operacyjnego na sekundę  |  Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje zapisu dysku systemu operacyjnego/s  |  Operacje zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  System operacyjny na dysk QD  |  QD dysku systemu operacyjnego (przestarzałe)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty odczytu systemu operacyjnego na dysk/s  |  Bajty odczytu dysku systemu operacyjnego/s (przestarzałe)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje odczytu systemu operacyjnego na dysku/s  |  Operacje odczytu dysku systemu operacyjnego/s (przestarzałe)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty zapisu systemu operacyjnego na dysk/s  |  Bajty zapisu dysku systemu operacyjnego/s (przestarzałe)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje zapisu systemu operacyjnego na dysk/s  |  Operacje zapisu dysku systemu operacyjnego/s (przestarzałe)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Przepływy wychodzące  |  Przepływy wychodzące  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Maksymalna szybkość tworzenia przepływów wychodzących  |  Maksymalna szybkość tworzenia przepływów wychodzących (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Na dysk QD  |  QD dysku danych (przestarzałe)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty odczytu dysku/s  |  Bajty odczytu dysku danych/s (przestarzałe)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje odczytu dysku/s  |  Operacje odczytu dysku danych/s (przestarzałe)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty zapisu na dysku/s  |  Bajty zapisu dysku danych/s (przestarzałe)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje zapisu na dysku/s  |  Operacje zapisu na dysku danych/s (przestarzałe)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Procentowe użycie procesora CPU  |  Procentowe użycie procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Trafienie odczytu pamięci podręcznej dysków danych premium  |  Trafienie odczytu pamięci podręcznej dysków premium (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Odczyt pamięci podręcznej dysku z danymi w warstwie Premium  |  Odczyt pamięci podręcznej dysków premium (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Trafienie odczytu pamięci podręcznej dysku systemu operacyjnego premium  |  Trafienie odczytu pamięci podręcznej dysku premium OS (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Odczyt pamięci podręcznej systemu operacyjnego Premium  |  Odczyt pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Zużyte kredyty CPU  |  Zużyte kredyty CPU  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Pozostałe kredyty CPU  |  Pozostałe kredyty CPU  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Głębokość kolejki dysku danych  |  Głębokość kolejki dysków danych (wersja zapoznawcza)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty odczytu dysku danych/s  |  Bajty odczytu dysku danych/s (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje odczytu dysku danych/s  |  Operacje odczytu dysku danych/s (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty zapisu dysku danych/s  |  Bajty zapisu dysku danych/s (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje zapisu na dysku danych/s  |  Operacje zapisu na dysku danych/s (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty odczytu dysku  |  Bajty odczytu dysku  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje odczytu dysku/s  |  Operacje odczytu dysku/s  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty zapisu dysku  |  Bajty zapisu dysku  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje zapisu dysku/s  |  Operacje zapisu dysku/s  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Przepływy przychodzące  |  Przepływy przychodzące  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Maksymalna szybkość tworzenia przepływów przychodzących  |  Maksymalna szybkość tworzenia przepływów przychodzących (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Sieć — wejście  |  Sieć w rozliczanej (przestarzała)  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Sieć w sumie  |  Sieć w sumie  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Sieć — wyjście  |  Wyjęty z sieci (przestarzały)  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Całkowita liczba wyjść sieci  |  Całkowita liczba wyjść sieci  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Głębokość kolejki dysku systemu operacyjnego  |  Głębokość kolejki systemu operacyjnego (wersja zapoznawcza)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty odczytu dysku systemu operacyjnego na sekundę  |  Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje odczytu dysku systemu operacyjnego/s  |  Operacje odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty zapisu dysku systemu operacyjnego na sekundę  |  Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje zapisu dysku systemu operacyjnego/s  |  Operacje zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  System operacyjny na dysk QD  |  QD dysku systemu operacyjnego (przestarzałe)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty odczytu systemu operacyjnego na dysk/s  |  Bajty odczytu dysku systemu operacyjnego/s (przestarzałe)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje odczytu systemu operacyjnego na dysku/s  |  Operacje odczytu dysku systemu operacyjnego/s (przestarzałe)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty zapisu systemu operacyjnego na dysk/s  |  Bajty zapisu dysku systemu operacyjnego/s (przestarzałe)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje zapisu systemu operacyjnego na dysk/s  |  Operacje zapisu dysku systemu operacyjnego/s (przestarzałe)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Przepływy wychodzące  |  Przepływy wychodzące  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Maksymalna szybkość tworzenia przepływów wychodzących  |  Maksymalna szybkość tworzenia przepływów wychodzących (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Na dysk QD  |  QD dysku danych (przestarzałe)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty odczytu dysku/s  |  Bajty odczytu dysku danych/s (przestarzałe)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje odczytu dysku/s  |  Operacje odczytu dysku danych/s (przestarzałe)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty zapisu na dysku/s  |  Bajty zapisu dysku danych/s (przestarzałe)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje zapisu na dysku/s  |  Operacje zapisu na dysku danych/s (przestarzałe)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Procentowe użycie procesora CPU  |  Procentowe użycie procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Trafienie odczytu pamięci podręcznej dysków danych premium  |  Trafienie odczytu pamięci podręcznej dysków premium (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Odczyt pamięci podręcznej dysku z danymi w warstwie Premium  |  Odczyt pamięci podręcznej dysków premium (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Trafienie odczytu pamięci podręcznej dysku systemu operacyjnego premium  |  Trafienie odczytu pamięci podręcznej dysku premium OS (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Odczyt pamięci podręcznej systemu operacyjnego Premium  |  Odczyt pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Zużyte kredyty CPU  |  Zużyte kredyty CPU  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Pozostałe kredyty CPU  |  Pozostałe kredyty CPU  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Głębokość kolejki dysku danych  |  Głębokość kolejki dysków danych (wersja zapoznawcza)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty odczytu dysku danych/s  |  Bajty odczytu dysku danych/s (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operacje odczytu dysku danych/s  |  Operacje odczytu dysku danych/s (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty zapisu dysku danych/s  |  Bajty zapisu dysku danych/s (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operacje zapisu na dysku danych/s  |  Operacje zapisu na dysku danych/s (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty odczytu dysku  |  Bajty odczytu dysku  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operacje odczytu dysku/s  |  Operacje odczytu dysku/s  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty zapisu dysku  |  Bajty zapisu dysku  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operacje zapisu dysku/s  |  Operacje zapisu dysku/s  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Przepływy przychodzące  |  Przepływy przychodzące  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Maksymalna szybkość tworzenia przepływów przychodzących  |  Maksymalna szybkość tworzenia przepływów przychodzących (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Sieć — wejście  |  Sieć w rozliczanej (przestarzała)  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Sieć w sumie  |  Sieć w sumie  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Sieć — wyjście  |  Wyjęty z sieci (przestarzały)  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Całkowita liczba wyjść sieci  |  Całkowita liczba wyjść sieci  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Głębokość kolejki dysku systemu operacyjnego  |  Głębokość kolejki systemu operacyjnego (wersja zapoznawcza)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty odczytu dysku systemu operacyjnego na sekundę  |  Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operacje odczytu dysku systemu operacyjnego/s  |  Operacje odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty zapisu dysku systemu operacyjnego na sekundę  |  Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operacje zapisu dysku systemu operacyjnego/s  |  Operacje zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  System operacyjny na dysk QD  |  QD dysku systemu operacyjnego (przestarzałe)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty odczytu systemu operacyjnego na dysk/s  |  Bajty odczytu dysku systemu operacyjnego/s (przestarzałe)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operacje odczytu systemu operacyjnego na dysku/s  |  Operacje odczytu dysku systemu operacyjnego/s (przestarzałe)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty zapisu systemu operacyjnego na dysk/s  |  Bajty zapisu dysku systemu operacyjnego/s (przestarzałe)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operacje zapisu systemu operacyjnego na dysk/s  |  Operacje zapisu dysku systemu operacyjnego/s (przestarzałe)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Przepływy wychodzące  |  Przepływy wychodzące  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Maksymalna szybkość tworzenia przepływów wychodzących  |  Maksymalna szybkość tworzenia przepływów wychodzących (wersja zapoznawcza)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Na dysk QD  |  QD dysku danych (przestarzałe)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty odczytu dysku/s  |  Bajty odczytu dysku danych/s (przestarzałe)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operacje odczytu dysku/s  |  Operacje odczytu dysku danych/s (przestarzałe)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty zapisu na dysku/s  |  Bajty zapisu dysku danych/s (przestarzałe)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operacje zapisu na dysku/s  |  Operacje zapisu na dysku danych/s (przestarzałe)  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Procentowe użycie procesora CPU  |  Procentowe użycie procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Trafienie odczytu pamięci podręcznej dysków danych premium  |  Trafienie odczytu pamięci podręcznej dysków premium (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Odczyt pamięci podręcznej dysku z danymi w warstwie Premium  |  Odczyt pamięci podręcznej dysków premium (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Trafienie odczytu pamięci podręcznej dysku systemu operacyjnego premium  |  Trafienie odczytu pamięci podręcznej dysku premium OS (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Odczyt pamięci podręcznej systemu operacyjnego Premium  |  Odczyt pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Instancja kontenera firmy Microsoft/kontenerGrupy  |  CpuUsage (  |  Użycie procesora  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Instancja kontenera firmy Microsoft/kontenerGrupy  |  MemoryUsage (Narzędzie pamięci)  |  Użycie pamięci  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Instancja kontenera firmy Microsoft/kontenerGrupy  |  NetworkBytesReceivedPerSecond  |  Bajty sieci odebrane na sekundę  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Instancja kontenera firmy Microsoft/kontenerGrupy  |  NetworkBytesTransmittedPerSecond  |  Bajty sieciowe przesyłane na sekundę  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ContainerRegistry/rejestry  |  UruchomienieDuration  |  Czas trwania biegu  |  Milisekund  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.ContainerRegistry/rejestry  |  SuccessfulPullCount (Liczba powiodła się)  |  Pomyślna liczba ściągnięcia  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ContainerRegistry/rejestry  |  UdanyPulszcount  |  Pomyślna liczba wypycheń  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ContainerRegistry/rejestry  |  TotalPullCount (Liczba całkowita)  |  Całkowita liczba pociągniętych  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ContainerRegistry/rejestry  |  Suma PushCount  |  Całkowita liczba wypycheń  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_cpu_cores  |  Całkowita liczba dostępnych rdzeni procesora w zarządzanym klastrze  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_memory_bytes  |  Całkowita ilość dostępnej pamięci w klastrze zarządzanym  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft.ContainerService/managedClusters  |  kube_node_status_condition  |  Stany dla różnych warunków węzła  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_phase  |  Liczba strąków według fazy  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_ready  |  Liczba zasobników w stanie Gotowy  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  DostępneJastość  |  Dostępna pojemność  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloud  |  Przesłane bajty w chmurze (urządzenie)  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloudPerShare  |  Przesłane bajty w chmurze (udostępnianie)  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  ChmuraReadThroughput  |  Przepływność pobierania w chmurze  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  ChmuraReadThroughPutPerShare  |  Przepływność pobierania w chmurze (udział)  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughput  |  Przepływność przekazywania w chmurze  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughPutPerShare  |  Przepływność przekazywania w chmurze (udział)  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  HyperVMemoryutilization  |  Obliczanie krawędzi — użycie pamięci  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  HyperVVirtualProcessorUtilization  |  Obliczeń krawędziowych — procent procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  NicReadThroughput  |  Przepływność odczytu (sieć)  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  NicWriteThroughput  |  Przepływność zapisu (sieć)  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  Całkowita zdolność produkcyjna  |  Całkowita pojemność  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataFactory/datafactories Microsoft.DataFactory/datafactories Microsoft.DataFactory/datafactories Microsoft.  |  Nieudaneruny  |  Nieudane przebiegi  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataFactory/datafactories Microsoft.DataFactory/datafactories Microsoft.DataFactory/datafactories Microsoft.  |  Udaneruny  |  Udane przebiegi  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataFactory/fabryki  |  ActivityCancelledRuns (ujedn.  |  Anulowane działanie uruchamia dane  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataFactory/fabryki  |  DziałalnośćFailedRuns  |  Działanie nie powiodło się uruchamia metryki  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataFactory/fabryki  |  DziałalnośćSucceedRuns  |  Działanie, które powiodło się, uruchamia metryki  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataFactory/fabryki  |  Jednostki FactorySizeInGbUnits  |  Całkowity rozmiar fabryki (jednostka GB)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.DataFactory/fabryki  |  IntegracjaRuntimeAvailableMemory  |  Dostępna pamięć środowiska wykonawczego integracji  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataFactory/fabryki  |  IntegracjaRuntimeAverageTaskPickupDelay  |  Czas trwania kolejki środowiska uruchomieniowego integracji  |  Sekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataFactory/fabryki  |  IntegracjaRuntimeCpuPercentage  |  Wykorzystanie procesora CPU w czasie wykonywania integracji  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataFactory/fabryki  |  IntegracjaRuntimeQueueLength  |  Długość kolejki środowiska uruchomieniowego integracji  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DataFactory/fabryki  |  Jednostki MaxAllowedFactorySizeInGbUnits  |  Maksymalny dozwolony rozmiar fabryki (jednostka GB)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.DataFactory/fabryki  |  Maksymalna liczba zasobów maksymalnych  |  Liczba maksymalnych dozwolonych encji  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.DataFactory/fabryki  |  PipelineCancelledRuns  |  Anulowane przebiegi potoku metryki  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataFactory/fabryki  |  PipelineFailedRuns (Nieupowłożone runy potoku)  |  Nieudane przebiegi potoku  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataFactory/fabryki  |  PipelineSucceedRuns  |  Powiodło się, że potok uruchamia metryki  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataFactory/fabryki  |  Liczba zasobów  |  Łączna liczba jednostek  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.DataFactory/fabryki  |  TriggerCancelledRuns (TriggerCancelledRuns)  |  Anulowane uruchomienia wyzwalacza metryki  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataFactory/fabryki  |  TriggerFailedRuns (Nieugiętyrun)  |  Nieudany wyzwalacz uruchamia metryki  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataFactory/fabryki  |  WyzwalaczeSucceedRuns  |  Pomyślne uruchomienie wyzwalacza powoduje  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedCancelled  |  Anulowany czas AU  |  Sekundy  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedFailure  |  Nieudany czas AU  |  Sekundy  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataLakeAnalytics/accounts  |  Sukces w jobauendedsukces  |  Udany czas AU  |  Sekundy  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedCancelled (Nieumiejeć)  |  Anulowane zadania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedFailure (JobEndedFailure)  |  Zadania nie powiodły się  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataLakeAnalytics/accounts  |  Sukces w zadaniach  |  Udane zadania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataLakeStore/konta  |  Dataread  |  Odczyt danych  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataLakeStore/konta  |  DataWritten (DaneWritten)  |  Zapisane dane  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataLakeStore/konta  |  OdczytRequests  |  Żądania odczytu  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DataLakeStore/konta  |  Całkowitastorage  |  Łączny rozmiar magazynu  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.DataLakeStore/konta  |  WriteRequests (Zapisy)  |  Żądania zapisu  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/serwery  |  active_connections  |  Aktywne połączenia  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/serwery  |  backup_storage_used  |  Używany magazyn kopii zapasowych  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/serwery  |  connections_failed  |  Połączenia zakończone niepowodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/serwery  |  cpu_percent  |  Procent procesora  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/serwery  |  io_consumption_percent  |  Procent IO  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/serwery  |  memory_percent  |  Procent pamięci  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/serwery  |  network_bytes_egress  |  Sieć — wyjście  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/serwery  |  network_bytes_ingress  |  Sieć — wejście  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/serwery  |  seconds_behind_master  |  Opóźnienie replikacji w sekundach  |  Liczba  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/serwery  |  serverlog_storage_limit  |  Limit magazynu dziennika serwera  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/serwery  |  serverlog_storage_percent  |  Procent magazynu dziennika serwera  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/serwery  |  serverlog_storage_usage  |  Używany magazyn dzienników serwera  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/serwery  |  storage_limit  |  Limit miejsca do magazynowania  |  Bajty  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/serwery  |  storage_percent  |  Procent miejsca  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMariaDB/serwery  |  storage_used  |  Używany magazyn  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  active_connections  |  Aktywne połączenia  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  backup_storage_used  |  Używany magazyn kopii zapasowych  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  connections_failed  |  Połączenia zakończone niepowodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  cpu_percent  |  Procent procesora  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  io_consumption_percent  |  Procent IO  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  memory_percent  |  Procent pamięci  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  network_bytes_egress  |  Sieć — wyjście  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  network_bytes_ingress  |  Sieć — wejście  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  seconds_behind_master  |  Opóźnienie replikacji w sekundach  |  Liczba  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_limit  |  Limit magazynu dziennika serwera  |  Bajty  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_percent  |  Procent magazynu dziennika serwera  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_usage  |  Używany magazyn dzienników serwera  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  storage_limit  |  Limit miejsca do magazynowania  |  Bajty  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  storage_percent  |  Procent miejsca  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DBforMySQL/servers  |  storage_used  |  Używany magazyn  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  active_connections  |  Aktywne połączenia  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  backup_storage_used  |  Używany magazyn kopii zapasowych  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  connections_failed  |  Połączenia zakończone niepowodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  cpu_percent  |  Procent procesora  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  io_consumption_percent  |  Procent IO  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  memory_percent  |  Procent pamięci  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_egress  |  Sieć — wyjście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_ingress  |  Sieć — wejście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_bytes  |  Maksymalne opóźnienie w replikach  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_seconds  |  Opóźnienie repliki  |  Sekundy  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_limit  |  Limit magazynu dziennika serwera  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_percent  |  Procent magazynu dziennika serwera  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_usage  |  Używany magazyn dzienników serwera  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  storage_limit  |  Limit miejsca do magazynowania  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  storage_percent  |  Procent miejsca  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/servers  |  storage_used  |  Używany magazyn  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/serversv2  |  active_connections  |  Aktywne połączenia  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/serversv2  |  cpu_percent  |  Procent procesora  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/serversv2  |  Iops  |  Liczba operacji we/wy na sekundę  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/serversv2  |  memory_percent  |  Procent pamięci  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_egress  |  Sieć — wyjście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_ingress  |  Sieć — wejście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/serversv2  |  storage_percent  |  Procent miejsca  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.DBforPostgreSQL/serversv2  |  storage_used  |  Używany magazyn  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Devices/Account  |  digitaltwins.telemetry.węzły  |  Symbol zastępczy telemetrii węzła cyfrowych bliźniąt  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  c2d.commands.egress.abandon.success  |  Porzucone wiadomości C2D  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  c2d.commands.egress.complete.success  |  Zakończono dostarczanie wiadomości C2D  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  c2d.commands.egress.reject.success  |  Wiadomości C2D odrzucone  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  c2d.methods.failure  |  Wywołania metod bezpośrednich nie powiodły się  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  c2d.methods.requestSize  |  Rozmiar żądania wywołań metody bezpośredniej  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  c2d.methods.responseZmializowanie  |  Rozmiar odpowiedzi wywołań metody bezpośredniej  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  c2d.methods.success  |  Pomyślne wywołanie metody bezpośredniej  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  c2d.twin.read.failure  |  Nieudane bliźniacze odczyty z zaplecza  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  c2d.twin.read.size  |  Rozmiar odpowiedzi bliźniaczych odczytów z zaplecza  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  c2d.twin.read.success  |  Udane bliźniacze odczyty z zaplecza  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  c2d.twin.update.failure  |  Nieudane bliźniacze aktualizacje z zaplecza  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  c2d.twin.update.size  |  Rozmiar bliźniaczych aktualizacji z zaplecza  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  c2d.twin.update.success  |  Pomyślne podwójne aktualizacje z zaplecza  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  C2DMessagesExpired  |  Wiadomości C2D wygasły (wersja zapoznawcza)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  Konfiguracji  |  Metryki konfiguracji  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Urządzenia firmy Microsoft.Devices/IotHubs  |  connectedDeviceCount  |  Podłączone urządzenia (wersja zapoznawcza)  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.builtIn.events  |  Routing: wiadomości dostarczane do wiadomości/zdarzeń  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.eventHubs  |  Routing: wiadomości dostarczane do Centrum zdarzeń  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusQueues  |  Routing: wiadomości dostarczane do kolejki magistrali usług  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusTopics  |  Routing: wiadomości dostarczane do tematu magistrali usług  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage  |  Routing: wiadomości dostarczane do magazynu  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.blob  |  Routing: obiekty blob dostarczane do magazynu  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.bytes  |  Routing: dane dostarczane do magazynu  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.builtIn.events  |  Routing: opóźnienie wiadomości/zdarzeń  |  Milisekund  |  Średnia | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.eventHubs  |  Routing: opóźnienie komunikatów dla Centrum zdarzeń  |  Milisekund  |  Średnia | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusQueues  |  Routing: opóźnienie komunikatu dla kolejki magistrali usług  |  Milisekund  |  Średnia | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusTopics  |  Routing: opóźnienie komunikatu dla tematu magistrali usług  |  Milisekund  |  Średnia | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.storage  |  Routing: opóźnienie komunikatu dla magazynu  |  Milisekund  |  Średnia | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.dropped d2c.telemetry.egress.dropped d2c.telemetry.egress.dropped d2  |  Routing: wiadomości telemetryczne porzucone   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.fallback  |  Routing: wiadomości dostarczane do rezerwowego  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.invalid d2c.telemetry.egress.invalid d2c.telemetry.egress.invalid d2  |  Routing: komunikaty telemetryczne niezgodne  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.orphaned  |  Routing: wiadomości telemetryczne oddzielone   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.success  |  Routing: dostarczane wiadomości telemetryczne  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.allProtocol  |  Próby wysyłania wiadomości telemetrycznych  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.sendTrottle  |  Liczba błędów ograniczania przepustowości  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.success  |  Wysłane wiadomości telemetryczne  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.twin.read.failure  |  Nieudane bliźniacze odczyty z urządzeń  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.twin.read.size  |  Rozmiar odpowiedzi bliźniaczych odczytów z urządzeń  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.twin.read.success  |  Udane bliźniacze odczyty z urządzeń  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.twin.update.failure  |  Nieudane bliźniacze aktualizacje z urządzeń  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.twin.update.size  |  Rozmiar bliźniaczych aktualizacji z urządzeń  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  d2c.twin.update.success  |  Pomyślne bliźniacze aktualizacje z urządzeń  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  dailyMessageQuotaUżywane  |  Łączna liczba używanych wiadomości  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  deviceDataUsage  |  Całkowite użycie danych urządzenia  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  deviceDataUsageV2  |  Całkowite użycie danych urządzenia (podgląd)  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  devices.connectedDevices.allProtocol  |  Podłączone urządzenia (przestarzałe)   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  devices.totalUrządzenia  |  Łączna liczba urządzeń (przestarzała)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  EventGridDeliveries  |  Dostawy w siatce zdarzeń (wersja zapoznawcza)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  EventGridLatency  |  Opóźnienie siatki zdarzeń (wersja zapoznawcza)  |  Milisekund  |  Średnia | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  jobs.cancelJob.failure  |  Anulowanie zadania nie powiodło się  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  jobs.cancelJob.success  |  Pomyślne anulowanie zadania  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  zadania.ukończone  |  Ukończone zadania  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  zadania.createDirectMethodJob.failure  |  Nieudane tworzenie zadań wywoływania metody  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.success  |  Pomyślne tworzenie zadań wywoływania metody  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  zadania.createTwinUpdateJob.failure  |  Nieudane tworzenie zadań aktualizacji bliźniaczej  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.success  |  Udane tworzenie zadań bliźniaczej aktualizacji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  zadania.failed  |  Zadania nie powiodły się  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  zadania.listJobs.failure  |  Nieudane wywołania listy zadań  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  jobs.listJobs.success  |  Pomyślne wywołania listy zadań  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  zadania.queryJobs.failure  |  Nieudane kwerendy zadań  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  jobs.queryJobs.success  |  Pomyślne kwerendy o zadania  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Urządzenia firmy Microsoft.Devices/IotHubs  |  totalDeviceCount  |  Łączna liczba urządzeń (wersja zapoznawcza)  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  twinQueries.failure  |  Nieudane zapytania bliźniaczej reprezentacji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  twinQueries.resultSize  |  Rozmiar wyników zapytań bliźniaczych  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/IotHubs  |  twinQueries.success  |  Pomyślne zapytania bliźniaczej reprezentacji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/inicjowanie obsługi administracyjnejUsługi  |  AtestyPty  |  Próby zaświadczania  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/inicjowanie obsługi administracyjnejUsługi  |  Przypisania urządzenia  |  Przypisane urządzenia  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Urządzenia firmy Microsoft.Devices/inicjowanie obsługi administracyjnejUsługi  |  RejestracjaPróbuje  |  Próby rejestracji  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseKonta kont  |  DostępneStorage  |  Dostępna pamięć  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseKonta kont  |  CassandraConnectionClosures  |  Zamknięcia połączeń Cassandra  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseKonta kont  |  Ładunki CassandraRequestCharge  |  Opłaty za żądanie Cassandra  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseKonta kont  |  CassandraSwoki  |  Prośby Cassandra  |  Liczba  |  Liczba | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseKonta kont  |  DataUsage (DataUsage)  |  Użycie danych  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.DocumentDB/databaseKonta kont  |  UsuńVirtualNetwork  |  UsuńVirtualNetwork  |  Liczba  |  Liczba | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseKonta kont  |  Liczba dokumentów  |  Liczba dokumentów  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseKonta kont  |  DocumentQuota (DokumentQuota)  |  Przydział dokumentu  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseKonta kont  |  IndeksUsage  |  Użycie indeksu  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseKonta kont  |  MetadataRequests  |  Żądania metadanych  |  Liczba  |  Liczba | 
| **Tak**  | **Tak** |  Microsoft.DocumentDB/databaseKonta kont  |  MongoRequestCharge  |  Opłata za żądanie Mongo  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.DocumentDB/databaseKonta kont  |  MongoRequests  |  Prośby Mongo  |  Liczba  |  Liczba | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseKonta kont  |  MongoRequestsCount  |  Mongo Cena żądania  |  Połów połówek  |  Średnia | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseKonta kont  |  MongoRequestsDelete  |  Mongo Delete Cena żądania  |  Połów połówek  |  Średnia | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseKonta kont  |  MongoRequestsInsert  |  Mongo Wstawić cena żądania  |  Połów połówek  |  Średnia | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseKonta kont  |  Klasztor MongoRequestsQuery  |  Szybkość żądania kwerendy Mongo  |  Połów połówek  |  Średnia | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseKonta kont  |  MongoRequestsUpdate  |  Mongo Update Cena żądania  |  Połów połówek  |  Średnia | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseKonta kont  |  AprowizowanaWyt.  |  Aprowizowana przepływność  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.DocumentDB/databaseKonta kont  |  ReplikacjaLatency  |  Opóźnienie replikacji P99  |  Milisekund  |  Średnia | 
| Nie  | Nie |  Microsoft.DocumentDB/databaseKonta kont  |  Dostępność usług  |  Dostępność usług  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.DocumentDB/databaseKonta kont  |  TotalRequests  |  Łączna liczba żądań  |  Liczba  |  Liczba | 
| **Tak**  | **Tak** |  Microsoft.DocumentDB/databaseKonta kont  |  Jednostki TotalRequestUnits  |  Łączna liczba jednostek żądań  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.EnterpriseKnowledgeGraph/usługi  |  Liczba błędów  |  Liczba niepowodzeń  |  Liczba  |  Liczba | 
| Nie  | Nie |  Microsoft.EnterpriseKnowledgeGraph/usługi  |  Liczba sukcesów  |  Liczba powodzeń  |  Liczba  |  Liczba | 
| Nie  | Nie |  Microsoft.EnterpriseKnowledgeGraph/usługi  |  SukcesLatywa  |  Opóźnienie sukcesu  |  Milisekund  |  Średnia | 
| Nie  | Nie |  Microsoft.EnterpriseKnowledgeGraph/usługi  |  Liczba transakcji  |  Liczba transakcji  |  Liczba  |  Liczba | 
| **Tak**  | **Tak** |  Microsoft.EventGrid/domeny  |  Liczba nienaliczonych liter  |  Zdarzenia z martwymi literami  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventGrid/domeny  |  DeliveryAttemptFailCount  |  Zdarzenia nieudane dostawy  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventGrid/domeny  |  Liczba dostawsukcesy  |  Dostarczone zdarzenia  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventGrid/domeny  |  Miejsca doceloweProcessingDurationInMs  |  Docelowy czas przetwarzania  |  Milisekund  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.EventGrid/domeny  |  Liczba porzuconychwentów  |  Porzucone zdarzenia  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventGrid/domeny  |  MatchedEventCount  |  Dopasowane wydarzenia  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventGrid/domeny  |  Konto PublishFailCount  |  Publikowanie zdarzeń nie powiodło się  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventGrid/domeny  |  PublishSuccessCount  |  Opublikowane wydarzenia  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventGrid/domeny  |  PublishSuccessLatencyInMs  |  Publikowanie opóźnienia sukcesu  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventGrid/eventSubscriptions  |  Liczba nienaliczonych liter  |  Zdarzenia z martwymi literami  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventGrid/eventSubscriptions  |  DeliveryAttemptFailCount  |  Zdarzenia nieudane dostawy  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventGrid/eventSubscriptions  |  Liczba dostawsukcesy  |  Dostarczone zdarzenia  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventGrid/eventSubscriptions  |  Miejsca doceloweProcessingDurationInMs  |  Docelowy czas przetwarzania  |  Milisekund  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.EventGrid/eventSubscriptions  |  Liczba porzuconychwentów  |  Porzucone zdarzenia  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventGrid/eventSubscriptions  |  MatchedEventCount  |  Dopasowane wydarzenia  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventGrid/extensionTopics  |  Konto PublishFailCount  |  Publikowanie zdarzeń nie powiodło się  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventGrid/extensionTopics  |  PublishSuccessCount  |  Opublikowane wydarzenia  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventGrid/extensionTopics  |  PublishSuccessLatencyInMs  |  Publikowanie opóźnienia sukcesu  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventGrid/extensionTopics  |  Niedoścignionalicznik  |  Niedopasowane wydarzenia  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventGrid/tematy  |  Konto PublishFailCount  |  Publikowanie zdarzeń nie powiodło się  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventGrid/tematy  |  PublishSuccessCount  |  Opublikowane wydarzenia  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventGrid/tematy  |  PublishSuccessLatencyInMs  |  Publikowanie opóźnienia sukcesu  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventGrid/tematy  |  Niedoścignionalicznik  |  Niedopasowane wydarzenia  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/klastry  |  ActiveConnections (Aktywne połączenia)  |  ActiveConnections (Aktywne połączenia)  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.EventHub/klastry  |  DostępneMemory  |  Dostępna pamięć  |  Wartość procentowa  |  Maksimum | 
| Nie  | Nie |  Microsoft.EventHub/klastry  |  Dziennik CaptureBacklog  |  Przechwytywanie zaległości.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/klastry  |  Przechwycone bajty  |  Przechwycone bajty.  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/klastry  |  Przechwyconewydacje  |  Przechwycone wiadomości.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/klastry  |  Połączenia Zamknięte  |  Połączenia zamknięte.  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.EventHub/klastry  |  ConnectionsOtwarte  |  Połączenia otwarte.  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.EventHub/klastry  |  Procesor CPU  |  Procesor CPU  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.EventHub/klastry  |  Przychodzących bajtów  |  Bajty przychodzące.  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/klastry  |  Przychodzącenaśniacje  |  Wiadomości przychodzące  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/klastry  |  Przychodzącerepy  |  Żądania przychodzące  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/klastry  |  Bajty wychodzące  |  Bajty wychodzące.  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/klastry  |  Wychodzące Wiadomości  |  Wiadomości wychodzące  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/klastry  |  QuotaExceedErrors  |  Przekroczone błędy przydziału.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/klastry  |  Serwery serwerowe  |  Błędy serwera.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/klastry  |  Udane prośby  |  Pomyślne żądania  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/klastry  |  ThrottledRequests  |  Ograniczone żądania.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/klastry  |  UserErrors (UżytkownikErrors)  |  Błędy użytkownika.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/przestrzenie nazw  |  ActiveConnections (Aktywne połączenia)  |  ActiveConnections (Aktywne połączenia)  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.EventHub/przestrzenie nazw  |  Dziennik CaptureBacklog  |  Przechwytywanie zaległości.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/przestrzenie nazw  |  Przechwycone bajty  |  Przechwycone bajty.  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/przestrzenie nazw  |  Przechwyconewydacje  |  Przechwycone wiadomości.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/przestrzenie nazw  |  Połączenia Zamknięte  |  Połączenia zamknięte.  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.EventHub/przestrzenie nazw  |  ConnectionsOtwarte  |  Połączenia otwarte.  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.EventHub/przestrzenie nazw  |  EHABL  |  Archiwizuj komunikaty zaległości (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/przestrzenie nazw  |  EHAMBS ( EHAMBS )  |  Przepływność wiadomości archiwum (przestarzała)  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/przestrzenie nazw  |  EHAMSGS ( EHAMSGS )  |  Archiwizowanie wiadomości (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/przestrzenie nazw  |  EHINBYTES (EHINBYTES)  |  Bajty przychodzące (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/przestrzenie nazw  |  EHINMBS ( EHINMBS )  |  Bajty przychodzące (przestarzałe) (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/przestrzenie nazw  |  EHINMSGS ( EHINMSGS )  |  Wiadomości przychodzące (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/przestrzenie nazw  |  EHOUTBYTES  |  Bajty wychodzące (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/przestrzenie nazw  |  EHOUTMBS ( EHOUTMBS )  |  Bajty wychodzące (przestarzałe) (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/przestrzenie nazw  |  EHOUTMSGS  |  Wiadomości wychodzące (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/przestrzenie nazw  |  FailreQ ( failreq )  |  Żądania nie powiodły się (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/przestrzenie nazw  |  Przychodzących bajtów  |  Bajty przychodzące.  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/przestrzenie nazw  |  Przychodzącenaśniacje  |  Wiadomości przychodzące  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/przestrzenie nazw  |  Przychodzącerepy  |  Żądania przychodzące  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/przestrzenie nazw  |  INMSGS (WŁASK.  |  Wiadomości przychodzące (przestarzałe) (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/przestrzenie nazw  |  INREQS ( INREQS )  |  Przychodzące żądania (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/przestrzenie nazw  |  Interr (w przedr.  |  Wewnętrzne błędy serwera (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/przestrzenie nazw  |  MISCERR (MISCERR)  |  Inne błędy (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/przestrzenie nazw  |  Bajty wychodzące  |  Bajty wychodzące.  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/przestrzenie nazw  |  Wychodzące Wiadomości  |  Wiadomości wychodzące  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/przestrzenie nazw  |  OUTMSGS (WYJDĄCE)  |  Wiadomości wychodzące (przestarzałe) (przestarzałe)  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/przestrzenie nazw  |  QuotaExceedErrors  |  Przekroczone błędy przydziału.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/przestrzenie nazw  |  Serwery serwerowe  |  Błędy serwera.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/przestrzenie nazw  |  Rozmiar  |  Rozmiar  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft.EventHub/przestrzenie nazw  |  Udane prośby  |  Pomyślne żądania  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/przestrzenie nazw  |  SUCCREQ ( SUCCREQ )  |  Pomyślne żądania (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.EventHub/przestrzenie nazw  |  SVRBSY ( SVRBSY )  |  Błędy zajętości serwera (przestarzałe)  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/przestrzenie nazw  |  ThrottledRequests  |  Ograniczone żądania.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.EventHub/przestrzenie nazw  |  UserErrors (UżytkownikErrors)  |  Błędy użytkownika.  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.HDInsight/klastry  |  CategorizedGatewayZastynia  |  Kategoryzowane żądania bramy  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.HDInsight/klastry  |  Prośby o bramę  |  Żądania bramy  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.HDInsight/klastry  |  Liczba aktywnych pracowników  |  Liczba aktywnych pracowników  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.HDInsight/klastry  |  SkalowanieRequests  |  Skalowanie żądań  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Insights/Skalowanie automatyczneStawienia  |  MetricThreshold (MetricThreshold)  |  Próg metryczny  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Insights/Skalowanie automatyczneStawienia  |  ObservedCapacity (Zdolność obserwowana)  |  Obserwowana pojemność  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Insights/Skalowanie automatyczneStawienia  |  Wartość ObservedMetricValue  |  Obserwowana wartość metryczna  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Insights/Skalowanie automatyczneStawienia  |  ScaleActionsInitiated  |  Zainicjowano akcje skalowania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Insights/Składniki  |  dostępnośćWyniki/dostępnośćPercentage  |  Dostępność  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft.Insights/Składniki  |  dostępnośćWyniki/liczba  |  Testy dostępności  |  Liczba  |  Liczba | 
| **Tak**  | Nie |  Microsoft.Insights/Składniki  |  dostępnośćWyniki/czas trwania  |  Czas trwania testu dostępności  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Insights/Składniki  |  przeglądarkaTimings/networkDuration  |  Czas ładowania strony sieciowej  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Insights/Składniki  |  przeglądarkaTimings/processingDuration  |  Czas przetwarzania klienta  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Insights/Składniki  |  przeglądarkaTimings/receiveDuration  |  Odbieranie czasu odpowiedzi  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Insights/Składniki  |  przeglądarkaTimings/sendDuration  |  Wyślij czas żądania  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Insights/Składniki  |  przeglądarkaTimings/totalDuration  |  Czas ładowania strony przeglądarki  |  Milisekund  |  Średnia | 
| Nie  | Nie |  Microsoft.Insights/Składniki  |  zależności/liczba  |  Wywołania zależności  |  Liczba  |  Liczba | 
| **Tak**  | Nie |  Microsoft.Insights/Składniki  |  zależności/czas trwania  |  Czas trwania zależności  |  Milisekund  |  Średnia | 
| Nie  | Nie |  Microsoft.Insights/Składniki  |  zależności/nie powiodło się  |  Niepowodzenia wywołania zależności  |  Liczba  |  Liczba | 
| Nie  | Nie |  Microsoft.Insights/Składniki  |  wyjątki/przeglądarka  |  Wyjątki przeglądarki  |  Liczba  |  Liczba | 
| **Tak**  | **Tak** |  Microsoft.Insights/Składniki  |  wyjątki/liczba  |  Wyjątki  |  Liczba  |  Liczba | 
| Nie  | Nie |  Microsoft.Insights/Składniki  |  wyjątki/serwer  |  Wyjątki serwera  |  Liczba  |  Liczba | 
| **Tak**  | **Tak** |  Microsoft.Insights/Składniki  |  odsłony/liczba  |  Wyświetlenia strony  |  Liczba  |  Liczba | 
| **Tak**  | Nie |  Microsoft.Insights/Składniki  |  odsłon/czasu trwania strony  |  Czas ładowania widoku strony  |  Milisekund  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Insights/Składniki  |  performanceCounters/exceptionsPerSekund  |  Współczynnik wyjątków  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Insights/Składniki  |  performanceCounters/memoryDostępne bajty  |  Dostępna pamięć  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Insights/Składniki  |  performanceCounters/processCpuPercentage  |  Przetwarzanie procesora  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Insights/Składniki  |  performanceCounters/processIOBytesPerSekund  |  Szybkość we/wy procesu  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Insights/Składniki  |  performanceCounters/processorCpuPercentage  |  Czas procesora  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Insights/Składniki  |  performanceCounters/processPrivateBytes  |  Przetwarzanie bajtów prywatnych  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Insights/Składniki  |  performanceCounters/requestExecutionTime  |  Czas wykonania żądania HTTP  |  Milisekund  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Insights/Składniki  |  performanceCounters/requestsInQueue  |  Żądania HTTP w kolejce aplikacji  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Insights/Składniki  |  performanceCounters/requestsPerSekund  |  Stawka żądania HTTP  |  Połów połówek  |  Średnia | 
| Nie  | Nie |  Microsoft.Insights/Składniki  |  żądania/liczba  |  Żądania serwera  |  Liczba  |  Liczba | 
| **Tak**  | Nie |  Microsoft.Insights/Składniki  |  żądania/czas trwania  |  Czas odpowiedzi serwera  |  Milisekund  |  Średnia | 
| Nie  | Nie |  Microsoft.Insights/Składniki  |  żądania/niepowodzenie  |  Żądania zakończone niepowodzeniem  |  Liczba  |  Liczba | 
| Nie  | Nie |  Microsoft.Insights/Składniki  |  żądania/stawka  |  Szybkość żądań serwera  |  Połów połówek  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Insights/Składniki  |  ślady/liczba  |  Ślady  |  Liczba  |  Liczba | 
| **Tak**  | Nie |  Microsoft.KeyVault/przechowalnia  |  SerwisApiHit  |  Całkowita liczba trafień interfejsu API usługi  |  Liczba  |  Liczba | 
| **Tak**  | Nie |  Microsoft.KeyVault/przechowalnia  |  ServiceApiLatency  |  Ogólne opóźnienie interfejsu api usługi  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.KeyVault/przechowalnia  |  ServiceApiResult (ServiceApiResult)  |  Wyniki interfejsu API usługi razem  |  Liczba  |  Liczba | 
| **Tak**  | **Tak** |  Microsoft.Kusto/klastry  |  Buforowanie alokacji  |  Wykorzystanie pamięci podręcznej  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Kusto/klastry  |  ContinuousExportMaxLatenessMinutes  |  Ciągłe eksportowanie Max Lateness Minut  |  Liczba  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.Kusto/klastry  |  ContinuousExportNumOfRecordsExported  |  Ciągły eksport - liczba eksportowanych rekordów  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Kusto/klastry  |  Ciągła liczba ekspportów  |  Ciągła liczba oczekujących na eksport  |  Liczba  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.Kusto/klastry  |  CiągłyWydajnik  |  Ciągły wynik eksportu  |  Liczba  |  Liczba | 
| **Tak**  | **Tak** |  Microsoft.Kusto/klastry  |  Procesor CPU  |  Procesor CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Kusto/klastry  |  WydarzeniaProcessedForEventHubs  |  Przetwarzane zdarzenia (dla centrów zdarzeń/IoT)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Kusto/klastry  |  Eksportutilization  |  Wykorzystanie eksportu  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.Kusto/klastry  |  PołkwianieLatencyInSekundy  |  Opóźnienie połknięcia (w sekundach)  |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Kusto/klastry  |  PołknienieWyskuj  |  Wynik spożycia  |  Liczba  |  Liczba | 
| **Tak**  | **Tak** |  Microsoft.Kusto/klastry  |  Połknieniauutilization  |  Wykorzystanie spożycia  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Kusto/klastry  |  PołkliwienieVolumeInMB  |  Głośność pozyskiwania (w MB)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Kusto/klastry  |  Keepalive  |  Utrzymać przy życiu  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Kusto/klastry  |  Wyszukiwanie zapytań  |  Czas trwania kwerendy  |  Milisekund  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Kusto/klastry  |  SteamingIngestRequestRate (SteamingIngestRequestRate)  |  Szybkość pozyskiwania przesyłania strumieniowego  |  Liczba  |  RateRequestsPerSecond | 
| **Tak**  | **Tak** |  Microsoft.Kusto/klastry  |  Szybkość pozyskiwania danych w strumieniu strumieniowym  |  Szybkość pozyskiwania danych przesyłania strumieniowego  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Kusto/klastry  |  StreamingOwanieDuration  |  Czas pozyskiwania przesyłania strumieniowego  |  Milisekund  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Kusto/klastry  |  Wyniki pozyskiwania strumieniowego  |  Streaming Ingest Wynik  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Logic/integrationServiceEnvironments  |  ActionLatency (Włastwerwość  |  Opóźnienie akcji   |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  AkcjeUpełnione  |  Akcje zakończone   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  AkcjeNiewiększone  |  Akcje nie powiodły się   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  AkcjeWysłane  |  Pominięte akcje   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  AkcjeStartowane  |  Rozpoczęto akcje   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  DziałaniaSucceeded  |  Akcje zakończyły się pomyślnie   |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Logic/integrationServiceEnvironments  |  DziałanieSukceslatywa  |  Opóźnienie sukcesu akcji   |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  AkcjaTrocesy  |  Zdarzenia ograniczone do akcji  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorMemoryUsage  |  Użycie pamięci łącznika dla środowiska usługi integracji  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorProcessorUsage  |  Użycie procesora łącznika w środowisku usługi integracji  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowMemoryUsage  |  Użycie pamięci przepływu pracy dla środowiska usługi integracji  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentPracflowProcessorUsage  |  Użycie procesora przepływu pracy w środowisku usługi integracji  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  RunFailurePercentage  |  Procent niepowodzenia uruchomienia  |  Wartość procentowa  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Logic/integrationServiceEnvironments  |  RunLatency (RunLatency)  |  Opóźnienie uruchamiania  |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  DziałaCancelled  |  Przebiegi anulowane  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsCompleted (Wykończone trasy)  |  Ukończono przebiegi  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  DziałaFailed  |  Przebiegi nie powiodły się  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  UruchamianieRozpoczęło się  |  Uruchomiono  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  BiegiSucceededed  |  Przebiegi powiodły się  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  RunStartThrottledWydarzenia  |  Uruchamianie zdarzeń z ograniczeniem startu  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Logic/integrationServiceEnvironments  |  RunSuccessLatency  |  Uruchamianie opóźnienia sukcesu  |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  RunThrottledWydarzenia  |  Uruchamianie zdarzeń z ograniczeniem  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerFireLatency (TriggerFireLatency)  |  Opóźnienie pożaru wyzwalacza   |  Sekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerLatency (TriggerLatency)  |  Opóźnienie wyzwalacza   |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  WyzwalaczeUkoletowane  |  Ukończono wyzwalacze   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  WyzwalaczeNieNie niesie zezonych  |  Wyzwalacze nie powiodły się   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  WyzwalaczePalione  |  Wyzwalacze opalane   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  WyzwalaczeSsekpped  |  Pominięte wyzwalacze  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  WyzwalaczeStarted  |  Uruchomiono wyzwalacze   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersSucceeded  |  Wyzwalacze powiodły się   |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerSuccessLatency  |  Opóźnienie sukcesu wyzwalacza   |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerThrottledWydarzenia  |  Wyzwalacz ograniczone zdarzenia  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Logic/przepływy pracy  |  ActionLatency (Włastwerwość  |  Opóźnienie akcji   |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  AkcjeUpełnione  |  Akcje zakończone   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  AkcjeNiewiększone  |  Akcje nie powiodły się   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  AkcjeWysłane  |  Pominięte akcje   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  AkcjeStartowane  |  Rozpoczęto akcje   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  DziałaniaSucceeded  |  Akcje zakończyły się pomyślnie   |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Logic/przepływy pracy  |  DziałanieSukceslatywa  |  Opóźnienie sukcesu akcji   |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  AkcjaTrocesy  |  Zdarzenia ograniczone do akcji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  BillableActionWykonań  |  Egzekucje akcji podlegające rozliczaniu  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  BillableTriggerWykonania  |  Egzekucje wyzwalaczy podlegające rozliczani  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  RozliczeniaUsageNativeOperation  |  Użycie rozliczeń dla natywnych wykonań operacji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  RozliczeniaUsageNativeOperation  |  Użycie rozliczeń dla natywnych wykonań operacji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  RozliczenieUsageStandardconnector  |  Użycie rozliczeń dla wykonania łącznika standardowego  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  RozliczenieUsageStandardconnector  |  Użycie rozliczeń dla wykonania łącznika standardowego  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  RozliczeniaUsageStorageKonsumpcja  |  Użycie rozliczeń dla wykonań zużycia magazynu  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  RozliczeniaUsageStorageKonsumpcja  |  Użycie rozliczeń dla wykonań zużycia magazynu  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  RunFailurePercentage  |  Procent niepowodzenia uruchomienia  |  Wartość procentowa  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Logic/przepływy pracy  |  RunLatency (RunLatency)  |  Opóźnienie uruchamiania  |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  DziałaCancelled  |  Przebiegi anulowane  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  RunsCompleted (Wykończone trasy)  |  Ukończono przebiegi  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  DziałaFailed  |  Przebiegi nie powiodły się  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  UruchamianieRozpoczęło się  |  Uruchomiono  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  BiegiSucceededed  |  Przebiegi powiodły się  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  RunStartThrottledWydarzenia  |  Uruchamianie zdarzeń z ograniczeniem startu  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Logic/przepływy pracy  |  RunSuccessLatency  |  Uruchamianie opóźnienia sukcesu  |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  RunThrottledWydarzenia  |  Uruchamianie zdarzeń z ograniczeniem  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  TotalBillableWykonań  |  Łączna liczba wykonywalnych wykonań  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Logic/przepływy pracy  |  TriggerFireLatency (TriggerFireLatency)  |  Opóźnienie pożaru wyzwalacza   |  Sekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Logic/przepływy pracy  |  TriggerLatency (TriggerLatency)  |  Opóźnienie wyzwalacza   |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  WyzwalaczeUkoletowane  |  Ukończono wyzwalacze   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  WyzwalaczeNieNie niesie zezonych  |  Wyzwalacze nie powiodły się   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  WyzwalaczePalione  |  Wyzwalacze opalane   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  WyzwalaczeSsekpped  |  Pominięte wyzwalacze  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  WyzwalaczeStarted  |  Uruchomiono wyzwalacze   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  TriggersSucceeded  |  Wyzwalacze powiodły się   |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Logic/przepływy pracy  |  TriggerSuccessLatency  |  Opóźnienie sukcesu wyzwalacza   |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Logic/przepływy pracy  |  TriggerThrottledWydarzenia  |  Wyzwalacz ograniczone zdarzenia  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.MachineLearningUsługi/obszary robocze  |  Aktywne rdzenie  |  Aktywne rdzenie  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.MachineLearningUsługi/obszary robocze  |  Aktywne węzły  |  Aktywne węzły  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.MachineLearningUsługi/obszary robocze  |  Ukończone przebiegi  |  Ukończone przebiegi  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.MachineLearningUsługi/obszary robocze  |  Nieudane przebiegi  |  Nieudane przebiegi  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.MachineLearningUsługi/obszary robocze  |  Rdzenie bezczynne  |  Rdzenie bezczynne  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.MachineLearningUsługi/obszary robocze  |  Węzły bezczynne  |  Węzły bezczynne  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.MachineLearningUsługi/obszary robocze  |  Opuszczanie rdzeni  |  Opuszczanie rdzeni  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.MachineLearningUsługi/obszary robocze  |  Opuszczanie węzłów  |  Opuszczanie węzłów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.MachineLearningUsługi/obszary robocze  |  Wdrażanie modelu nie powiodło się  |  Wdrażanie modelu nie powiodło się  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.MachineLearningUsługi/obszary robocze  |  Rozpoczęto wdrażanie modelu  |  Rozpoczęto wdrażanie modelu  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.MachineLearningUsługi/obszary robocze  |  Wdrożenie modelu powiodło się  |  Wdrożenie modelu powiodło się  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.MachineLearningUsługi/obszary robocze  |  Rejestr modelu nie powiódł się  |  Rejestr modelu nie powiódł się  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.MachineLearningUsługi/obszary robocze  |  Rejestr modelu zakończył się pomyślnie  |  Rejestr modelu zakończył się pomyślnie  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.MachineLearningUsługi/obszary robocze  |  Wywłaszczone rdzenie  |  Wywłaszczone rdzenie  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.MachineLearningUsługi/obszary robocze  |  Wstępnie wywłaszczone węzły  |  Wstępnie wywłaszczone węzły  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.MachineLearningUsługi/obszary robocze  |  Procent wykorzystania przydziału  |  Procent wykorzystania przydziału  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.MachineLearningUsługi/obszary robocze  |  Rozpoczęte biegi  |  Rozpoczęte biegi  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.MachineLearningUsługi/obszary robocze  |  Całkowita liczba rdzeni  |  Całkowita liczba rdzeni  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.MachineLearningUsługi/obszary robocze  |  Łączna liczba węzłów  |  Łączna liczba węzłów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.MachineLearningUsługi/obszary robocze  |  Niezniszalne rdzenie  |  Niezniszalne rdzenie  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.MachineLearningUsługi/obszary robocze  |  Niezniszalne węzły  |  Niezniszalne węzły  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Maps/accounts Microsoft.Maps/accounts Microsoft.Maps/accounts Microsoft.  |  Dostępność  |  Dostępność  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft.Maps/accounts Microsoft.Maps/accounts Microsoft.Maps/accounts Microsoft.  |  Sposób użycia  |  Sposób użycia  |  Liczba  |  Liczba | 
| **Tak**  | Nie |  Usługi Microsoft.Media/media  |  AssetCount (licz assetcount)  |  Liczba zasobów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Usługi Microsoft.Media/media  |  AssetQuota (Quota aktywów)  |  Przydział środków trwałych  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Usługi Microsoft.Media/media  |  AssetQuotaUsedPercentage  |  Użyta wartość procentowa przydziału zasobów  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Usługi Microsoft.Media/media  |  ContentKeyPolicyCount (Liczba klawiatur contentkeypolicycount)  |  Liczba zasad klucza zawartości  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Usługi Microsoft.Media/media  |  ContentKeyPolicyQuota (ContentKeyPolicyQuota)  |  Przydział zasad klucza zawartości  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Usługi Microsoft.Media/media  |  ContentKeyPolicyQuotaUsedPercentage  |  Użyto wartości procentowej przydziału zasad klucza zawartości  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Usługi Microsoft.Media/media  |  StreamingPolicyCount  |  Liczba zasad przesyłania strumieniowego  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Usługi Microsoft.Media/media  |  StreamingPolicyQuota  |  Przydział zasad przesyłania strumieniowego  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Usługi Microsoft.Media/media  |  StreamingPolicyQuotaUsedPercentage  |  Użyty procent przydziału zasad przesyłania strumieniowego  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Media/mediaservices/streamingWypunkty końcowe  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Media/mediaservices/streamingWypunkty końcowe  |  Żądania  |  Żądania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Media/mediaservices/streamingWypunkty końcowe  |  SuccessE2ELatency  |  Sukces koniec do końca Latencja  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Microservices4Spring/appClusters  |  GCPauseTotalCount  |  Liczba wstrzymania GC  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Microservices4Spring/appClusters  |  GCPauseTotalTime  |  Całkowity czas pauzy GC  |  Milisekund  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Microservices4Spring/appClusters  |  MaxOldGenMemoryBajki  |  Maksymalny dostępny rozmiar danych starej generacji  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Microservices4Spring/appClusters  |  OldGenMemoryBajki  |  Rozmiar danych starej generacji  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Microservices4Spring/appClusters  |  OldGenPromotedBytes ( OldGenPromotedBytes )  |  Promowanie rozmiaru danych starej generacji  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Microservices4Spring/appClusters  |  ServiceCpuUsagePercentage  |  Procent użycia procesora CPU usługi  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Microservices4Spring/appClusters  |  ServiceMemoryZakochało  |  Przypisana pamięć usługi  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Microservices4Spring/appClusters  |  ServiceMemoryMax  |  Maksymalna ilość pamięci usługi  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Microservices4Spring/appClusters  |  Usługa MemoryUzaużywane  |  Używana pamięć usługi  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Microservices4Spring/appClusters  |  SystemCpuUsagePercentage  |  Procent użycia procesora CPU systemu  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Microservices4Spring/appClusters  |  TomcatErrorCount  |  Błąd globalny Tomcat  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Microservices4Spring/appClusters  |  TomcatReceivedBytes  |  Tomcat Całkowita liczba odebranych bajtów  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestMaxTime  |  Tomcat Wniosek Maksymalny czas  |  Milisekund  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestTotalCount  |  Całkowita liczba żądań Tomcat  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestTotalCzas  |  Tomcat Żądanie Całkowity czas  |  Milisekund  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Microservices4Spring/appClusters  |  TomcatResponseAvgCzas  |  Tomcat żąda średni czas  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Microservices4Spring/appClusters  |  TomcatsentBytes (TomcatSentBytes)  |  Tomcat całkowita wysłana bajty  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionActiveCurrentCount  |  Tomcat Sesja Żywa Liczba  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionActiveMaxCount  |  Tomcat Session Max Aktywna liczba  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionAliveMaxTime  |  Tomcat Sesja Max Alive Czas  |  Milisekund  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionCreatedCount  |  Liczba utworów sesji Tomcat  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionExpiredCount  |  Liczba wygasłych sesji Tomcat  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionRejectedCount  |  Liczba odrzuconych sesji Tomcat  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Microservices4Spring/appClusters  |  YoungGenPromotedBytes  |  Promuj rozmiar danych młodego pokolenia  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.NetApp/netAppKonakony/pojemność Pools  |  VolumePoolPrzydzieliłUżywane  |  Użyta pula woluminów  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.NetApp/netAppKonakony/pojemność Pools  |  Rozmiar woluminuPoolTotalLogicalSize  |  Całkowity rozmiar logiczny puli woluminów  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.NetApp/netAppKonakony/pojemnośćPuly/woluminy  |  ŚredniareadLatency  |  Średnie opóźnienie odczytu  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.NetApp/netAppKonakony/pojemnośćPuly/woluminy  |  Średnia Średniawłasność  |  Średnie opóźnienie zapisu  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.NetApp/netAppKonakony/pojemnośćPuly/woluminy  |  ReadIops (ReadIops)  |  Przeczytaj iops  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.NetApp/netAppKonakony/pojemnośćPuly/woluminy  |  Rozmiar volumeLogicalSize  |  Rozmiar logiczny woluminu  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.NetApp/netAppKonakony/pojemnośćPuly/woluminy  |  Rozmiar pliku VolumeSnapshotSize  |  Rozmiar migawki woluminu  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.NetApp/netAppKonakony/pojemnośćPuly/woluminy  |  WriteIops (WriteIops)  |  Napisz iops  |  Połów połówek  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/applicationGateways  |  ApplicationGatewayTotalTime  |  Całkowita liczba czasu bramy aplikacji  |  Milisekund  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/applicationGateways  |  AvgRequestCountPerHealthyHost  |  Żądania na minutę na zdrowego hosta  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/applicationGateways  |  BackendConnectTime (Czas połączenia wsteczne)  |  Czas połączenia wewnętrznej bazy danych  |  Milisekund  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/applicationGateways  |  BackendFirstByteResponseTime  |  Czas odpowiedzi pierwszego bajtu wewnętrznej bazy danych  |  Milisekund  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/applicationGateways  |  Czas wewnętrznej bazy DanychLastByteResponse  |  Czas odpowiedzi ostatniego bajtu wewnętrznej bazy danych  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  Stan wewnętrznej odpowiedzi  |  Stan odpowiedzi wewnętrznej bazy danych  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  BlockedCount  |  Dystrybucja reguł zapory aplikacji sieci Web  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  BlockedReqCount  |  Liczba zablokowanych żądań zapory aplikacji sieci Web  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  Bytesreceived  |  Bajty odebrane  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  Bajtów  |  Bajty wysłane  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft.Network/applicationGateways  |  Jednostki pojemnościowe  |  Bieżące jednostki pojemności  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/applicationGateways  |  KlientRtt  |  RtT klienta  |  Milisekund  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/applicationGateways  |  Jednostki obliczeniowe  |  Bieżące jednostki obliczeniowe  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  CurrentConnections (Połączenia prądowe)  |  Bieżące połączenia  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  Nieudane zapisy  |  Żądania zakończone niepowodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  HealthyHostCount  |  Liczba zdrowych hostów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  MatchedCount  |  Dystrybucja reguły całkowita zapory aplikacji sieci Web  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  Status odpowiedzi  |  Stan odpowiedzi  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Network/applicationGateways  |  Przepływność  |  Przepływność  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  TlsProtocol ( TlsProtocol )  |  Protokół TLS klienta  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  TotalRequests  |  Łączna liczba żądań  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/applicationGateways  |  UnhealthyHostCount  |  Liczba niezdrowych hostów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/azurefirewalls  |  ApplicationRuleHit (Reguła aplikacji)  |  Liczba trafień reguł aplikacji  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/azurefirewalls  |  Przetwarzanie danych  |  Przetwarzane dane  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/azurefirewalls  |  FirewallHealth (FirewallHealth)  |  Stan kondycji zapory  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/azurefirewalls  |  NetworkRuleHit (Zasady sieciowe)  |  Liczba trafień reguł sieciowych  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/azurefirewalls  |  SNATPortutilization (SNATPortutilization)  |  Wykorzystanie portu SNAT  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Sieć/połączenia firmy Microsoft.Network  |  BitsInPerSekund  |  BitsInPerSekund  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Sieć/połączenia firmy Microsoft.Network  |  BitsOutPerSekunda  |  BitsOutPerSekunda  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/dnszones  |  QueryVolume (Wyw.  |  Wolumin kwerendy  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Network/dnszones  |  Ulatnianie recordSetCapacityUtilization  |  Rekordowe wykorzystanie pojemności zestawu mocy produkcyjnych  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/dnszones  |  Konto RecordSetCount  |  Liczba zestawów rekordów  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/expressRouteCircuits  |  ArpDostępność  |  Dostępność Arp  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/expressRouteCircuits  |  BgpWakość  |  Dostępność BGP  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/expressRouteCircuits  |  BitsInPerSekund  |  BitsInPerSekund  |  Połów połówek  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/expressRouteCircuits  |  BitsOutPerSekunda  |  BitsOutPerSekunda  |  Połów połówek  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsInPerSecond  |  GlobalReachBitsInPerSecond  |  Połów połówek  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsOutPerSekunda  |  GlobalReachBitsOutPerSekunda  |  Połów połówek  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsInPerSekund  |  PołówNieWywiększona połów  |  Połów połówek  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsOutPerSekunda  |  Połów PołówNiesieniaka  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsInPerSekund  |  BitsInPerSekund  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsOutPerSekunda  |  BitsOutPerSekunda  |  Połów połówek  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/expressRouteGateways  |  ErGatewayConnectionBitsInPerSekund  |  BitsInPerSekund  |  Połów połówek  |  Średnia | 
| Nie  | Nie |  Microsoft.Network/expressRouteGateways  |  ErGatewayConnectionBitsOutPerSekunda  |  BitsOutPerSekunda  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Usługi Microsoft.Network/expressRoutePorts  |  Stan administracyjny  |  Stan administracyjny  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Usługi Microsoft.Network/expressRoutePorts  |  LiniaProtocol  |  LiniaProtocol  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Usługi Microsoft.Network/expressRoutePorts  |  PortBitsInPerSekund  |  BitsInPerSekund  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Usługi Microsoft.Network/expressRoutePorts  |  PortBitsOutPerSekunda  |  BitsOutPerSekunda  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Usługi Microsoft.Network/expressRoutePorts  |  RxLightPoziom  |  RxLightPoziom  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Usługi Microsoft.Network/expressRoutePorts  |  Poziom TxLight  |  Poziom TxLight  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Network/frontdoory  |  BackendHealthPercentage  |  Procent kondycji wewnętrznej bazy danych  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Network/frontdoory  |  ZapleczeRequestCount  |  Liczba żądań wewnętrznej bazy danych  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Network/frontdoory  |  BackendRequestLatency (BackendRequestLatency)  |  Opóźnienie żądania wewnętrznej bazy danych  |  Milisekund  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Network/frontdoory  |  Rozmiar billableResponseSize  |  Rozmiar odpowiedzi podlegania rozliczania  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Network/frontdoory  |  Liczba żądań  |  Liczba żądań  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Network/frontdoory  |  Rozmiar żądania  |  Rozmiar żądania  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Network/frontdoory  |  Rozmiar odpowiedzi  |  Rozmiar odpowiedzi  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Network/frontdoory  |  TotalLatency (TotalLatency)  |  Całkowite opóźnienie  |  Milisekund  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Network/frontdoory  |  Konto WebApplicationFirewallRequestCount  |  Liczba żądań zapory aplikacji sieci Web  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Network/loadBalancers  |  AllocatedSnatPorts (Porty AllocatedSnat)  |  Przydzielone porty SNAT (wersja zapoznawcza)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/loadBalancers  |  Bytecount  |  Liczba bajtów  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/loadBalancers  |  DipDostępność  |  Stan sondy kondycji  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/loadBalancers  |  Liczba pakietów  |  Liczba pakietów  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/loadBalancers  |  SnatConnectionCount (Liczba połączeń SnatConnection)  |  Liczba połączeń SNAT  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/loadBalancers  |  Konto SYNCount  |  Liczba SYN  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Network/loadBalancers  |  UżywanePorty sznat  |  Używane porty SNAT (wersja zapoznawcza)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/loadBalancers  |  Dostępność Vip  |  Dostępność ścieżki danych  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/networkInterfaces  |  BytesReceivedRate (Właso)  |  Bajty odebrane  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/networkInterfaces  |  BytesSentRate (BytesSentRate)  |  Bajty wysłane  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/networkInterfaces  |  PakietyReceivedRate  |  Odebrane pakiety  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/networkInterfaces  |  PacketsSentRate (Szybkość pakietu)  |  Wysłane pakiety  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/networkWatchers/connectionMonitors  |  ŚredniaRoundtripMs  |  Śr. Czas podróży w obie strony (ms)  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/networkWatchers/connectionMonitors  |  ChecksFailedPercent (SprawdzanieFailedPercent)  |  Sprawdzanie procentu nie powiodło się (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/networkWatchers/connectionMonitors  |  ProbesFailedPercent (SondaFailedPercent)  |  % sond nie powiodło się  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/networkWatchers/connectionMonitors  |  RoundTripTimeMs  |  Czas podróży w obie strony (ms) (wersja zapoznawcza)  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  Bytecount  |  Liczba bajtów  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  BajtyDroppedDDoS  |  Bajty przychodzące porzucone DDoS  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  BajtówForwardedDDoS  |  Bajty przychodzące przekazywane dalej DDoS  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  BytesIndDoS (BytesInDDoS)  |  Bajty przychodzące DDoS  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  Pakiety DDoSTriggerSYN  |  Przychodzące pakiety SYN w celu wyzwolenia łagodzenia DDoS  |  Połów połówek  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  Pakiety DDoSTriggerTCP  |  Przychodzące pakiety TCP wyzwalające środki ograniczające DDoS  |  Połów połówek  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  Pakiety DDoSTriggeruDP  |  Przychodzące pakiety UDP w celu wyzwolenia ograniczania DDoS  |  Połów połówek  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  Atak IfUnderDDoS  |  W ramach ataku DDoS lub nie  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  Liczba pakietów  |  Liczba pakietów  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  PakietyDroppedDDoS  |  Pakiety przychodzące porzucone DDoS  |  Połów połówek  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  PacketsForwardedDDoS  |  Pakiety przychodzące przesyłane dalej DDoS  |  Połów połówek  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  PacketsInDDoS (Usługi wg.  |  Pakiety przychodzące DDoS  |  Połów połówek  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  Konto SynCount  |  Liczba SYN  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  Protokół TCPBytesDroppedDDoS  |  Przychodzące bajty TCP porzucone DDoS  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  TCPBytesForwardedDDoS  |  Przychodzące bajty TCP przekazywane dalej DDoS  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  Protokół TCPBytesInDDoS  |  Przychodzące bajty TCP DDoS  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  Protokół TCPPacketsDroppedDDoS  |  Przychodzące pakiety TCP porzucone DDoS  |  Połów połówek  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  TCPPacketsForwardedDDoS  |  Przychodzące pakiety TCP przesyłane dalej DDoS  |  Połów połówek  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  Protokół TCPPacketsIndDoS  |  Przychodzące pakiety TCP DDoS  |  Połów połówek  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  UDPBytesDroppedDDoS  |  Przychodzące bajty UDP porzucone DDoS  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  UDPBytesForwardedDDoS  |  Przychodzące bajty UDP przekazywane dalej DDoS  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  UDPBytesIndDoS  |  Przychodzące bajty UDP DDoS  |  PrzeztówPerSekunda  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  UDPPacketsDroppedDDoS  |  Przychodzące pakiety UDP porzucone DDoS  |  Połów połówek  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  UDPPacketsForwardedDDoS  |  Przychodzące pakiety UDP przekazywane ddos  |  Połów połówek  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  UDPPacketsIndDoS  |  Przychodzące pakiety UDP DDoS  |  Połów połówek  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/publicIPAddresses  |  Dostępność Vip  |  Dostępność ścieżki danych  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/trafficManagerProfiles  |  ProbeAgentCurrentEndpointStateByProfileResourceId  |  Stan punktu końcowego według punktu końcowego  |  Liczba  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.Network/trafficManagerProfiles  |  Punkt QpsByEnd  |  Zwracane zapytania według punktu końcowego  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/virtualNetworkGateways  |  ŚredniaPaswidth  |  Przepustowość bramy S2S  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/virtualNetworkGateways  |  P2SBandwidth (P2SBandwidth)  |  Przepustowość bramy P2S  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/virtualNetworkGateways  |  P2SConnectionCount  |  Liczba połączeń P2S  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Network/virtualNetworkGateways  |  TunelAverageBandwidth  |  Przepustowość tunelu  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Network/virtualNetworkGateways  |  TunelEgressbytes  |  Bajty ruchu wychodzącego tunelu  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPacketDropTSMismatch  |  Spadek pakietu niezgodności usługi TS w ruchu tunelowym  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/virtualNetworkGateways  |  Pakiety pakietów tunelowych  |  Pakiety wychodzące tunelu  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/virtualNetworkGateways  |  TunelowanieBytes  |  Bajty w ruchu tunelowym  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/virtualNetworkGateways  |  TunelowaniePacketDropTSMismatch  |  Spadek pakietu niezgodności ts w tunelu  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Network/virtualNetworkGateways  |  Pakiety tunelowe  |  Pakiety przychodzące tunelu  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Sieć Microsoft.Network/virtual Sieci  |  PingMeshAverageRoundtripMs  |  Czas podróży w obie strony dla pingów na maszynę wirtualną  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Sieć Microsoft.Network/virtual Sieci  |  PingMeshProbesFailedPercent  |  Nieudane pingi do maszyny Wirtualnej  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  Przychodzące  |  Wiadomości przychodzące  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  przychodzące.all.failedrequests  |  Wszystkie przychodzące żądania nieudane  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  incoming.all.requests  |  Wszystkie przychodzące żądania  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  incoming.scheduled  |  Wysłane zaplanowane powiadomienia wypychane  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  incoming.scheduled.cancel  |  Anulowane zaplanowane powiadomienia wypychane  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  instalacja.wszystko  |  Operacje zarządzania instalacją  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  installation.delete  |  Usuń operacje instalacji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  installation.get  |  Pobierz operacje instalacyjne  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  installation.patch  |  Operacje instalacji poprawek  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  instalacja.upsert  |  Tworzenie lub aktualizowanie operacji instalacji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  notificationhub.pushes  |  Wszystkie powiadomienia wychodzące  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.allpns.badorexpiredchannel  |  Nieprawidłowe lub wygasłe błędy kanału  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.allpns.channelerror  |  Błędy kanału  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.allpns.invalidpayload  |  Błędy ładunku  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.allpns.pnserror  |  Błędy zewnętrznego systemu powiadomień  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.allpns.success  |  Pomyślne powiadomienia  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.apns.badchannel  |  Błąd nieprawidłowego kanału APNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  plik outgoing.apns.expiredchannel  |  Błąd wygasłego kanału APNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.apns.invalidedentials  |  Błędy autoryzacji apns  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  plik outgoing.apns.invalidnotificationsize  |  Błąd nieprawidłowego rozmiaru powiadomienia apns  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.apns.pnserror  |  Błędy apns  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.apns.success  |  Pomyślne powiadomienia apns  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.gcm.authenticationerror  |  Błędy uwierzytelniania GCM  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.gcm.badchannel  |  Błąd nieprawidłowego kanału GCM  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.gcm.expiredchannel  |  Błąd wygasłego kanału GCM  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.gcm.invalidcredentials  |  Błędy autoryzacji GCM (nieprawidłowe poświadczenia)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.gcm.invalidnotificationformat  |  Nieprawidłowy format powiadomienia GCM  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  rozmiar pliku outgoing.gcm.invalidnotificationsize  |  Błąd nieprawidłowego rozmiaru powiadomienia GCM  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.gcm.pnserror  |  Błędy GCM  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.gcm.success  |  Pomyślne powiadomienia gcm  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.gcm.throttled  |  Powiadomienia o ograniczeniu gcm  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  plik wychodzący.gcm.wrongchannel  |  Błąd nieprawidłowego kanału GCM  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.mpns.authenticationeroror  |  Błędy uwierzytelniania MPNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.mpns.badchannel  |  Błąd nieprawidłowego kanału MPNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.mpns.channelzłączone  |  Kanał MPNS odłączony  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.mpns.dropped  |  Powiadomienia o porzuceniu mpns  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.mpns.invaliddedentials  |  Nieprawidłowe poświadczenia MPNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.mpns.invalidnotificationformat  |  Nieprawidłowy format powiadomienia MPNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.mpns.pnserror  |  Błędy MPNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.mpns.success  |  Pomyślne powiadomienia MPNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.mpns.throttled  |  Powiadomienia z ograniczeniem mpns  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  plik outgoing.wns.authenticationeror  |  Błędy uwierzytelniania WNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.wns.badchannel  |  Błąd nieprawidłowego kanału WNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.wns.channelzłączone  |  Kanał WNS rozłączony  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.wns.channelthrottled  |  Kanał WNS ograniczona  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.wns.dropped  |  Powiadomienia o upuszczeniu w umorzone w sieci WNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  plik outgoing.wns.expiredchannel  |  Błąd wygasłego kanału WNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.wns.invalidcredentials  |  Błędy autoryzacji WNS (nieprawidłowe poświadczenia)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.wns.invalidnotificationformat  |  Nieprawidłowy format powiadomienia WNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  rozmiar pliku outgoing.wns.invalidnotificationsize  |  Błąd nieprawidłowego rozmiaru powiadomienia WNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.wns.invalidtoken  |  Błędy autoryzacji WNS (nieprawidłowy token)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.wns.pnserror  |  Błędy WNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.wns.success  |  Pomyślne powiadomienia w ucho.  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.wns.throttled  |  Powiadomienia w sieci WNS z ograniczeniem  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.wns.tokenproviderunreachable  |  Błędy autoryzacji WNS (nieosiągalne)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  outgoing.wns.wrongtoken  |  Błędy autoryzacji WNS (nieprawidłowy token)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  rejestracja.wszystko  |  Operacje rejestracji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  rejestracja.tworzenie  |  Rejestracja tworzenie operacji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  rejestracja.delete  |  Operacje usuwania rejestracji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  rejestracja.get  |  Operacje odczytu rejestracji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  rejestracja.update  |  Operacje aktualizacji rejestracji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs  |  scheduled.pending  |  Oczekujące zaplanowane powiadomienia  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_% dostępnej pamięci  |  % dostępnej pamięci  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_% dostępnej przestrzeni wymiany  |  % dostępnej przestrzeni wymiany  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_% używanych bajtów  |  % używanych bajtów na które  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_% czasu DPC  |  % czasu DPC  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_% darmowe i-węzły  |  % wolnych i-węzłów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_% wolnego miejsca  |  % wolnego miejsca  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_% wolnego miejsca  |  % wolnego miejsca  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_% czasu bezczynnego  |  Czas bezczynności (%)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_% czasu przerwania  |  % czasu przerwania  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_% czasu oczekiwania we/wy  |  % czasu oczekiwania we/wy  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_% miły czas  |  % Miły czas  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_% czasu uprzywilejowanego  |  % czasu uprzywilejowanego  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_% czasu procesora  |  Czas procesora (%)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_% czasu procesora  |  Czas procesora (%)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_% używanych iwody  |  % zużytych i-węzłów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_% używanej pamięci  |  % używanej pamięci  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_% używanej przestrzeni  |  % używanej przestrzeni  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_% używanej przestrzeni wymiany  |  % używanej przestrzeni wymiany  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_% czasu użytkownika  |  % czasu użytkownika  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Available MBytes  |  Dostępne MBytes  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Available pamięci MBytes  |  Dostępna pamięć MBytes  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Available MBytes Swap  |  Dostępne wymiany bajtów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Avg. wartość operacji dysku na sek./odczyt  |  Średnia dysk s/odczyt  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Avg. wartość operacji dysku na sek./odczyt  |  Średnia dysk s/odczyt  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Avg. Transfery dysku/s  |  Średnia s/transfer dysku  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Avg. dysku na sek./zapis  |  Średnia sek./zapis  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Avg. dysku na sek./zapis  |  Średnia sek./zapis  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Bytes odebrane/s  |  Bajty odebrane/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Bytes wysłane/s  |  Bajty wysłane/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Bytes Suma/s  |  Suma bajtów/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Current długość kolejki dysku  |  Bieżąca długość kolejki dysku  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Disk odczytu bajtów/s  |  Bajty odczytu dysku/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  odczyty Average_Disk/s  |  Odczyty dysku/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  odczyty Average_Disk/s  |  Odczyty dysku/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Disk transfery/s  |  Transfery dysków/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Disk transfery/s  |  Transfery dysków/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Disk bajtów zapisu/s  |  Bajty zapisu dysku na sekundę  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Disk zapisy/s  |  Zapisy na dysku/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Disk zapisy/s  |  Zapisy na dysku/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  megabajty Average_Free  |  Darmowe megabajty  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  megabajty Average_Free  |  Darmowe megabajty  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Free pamięć fizyczna  |  Wolna pamięć fizyczna  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Free miejsca w plikach stronicowania  |  Wolne miejsce w plikach stronicowania  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Free pamięci wirtualnej  |  Darmowa pamięć wirtualna  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Logical bajtów dysku na sekundę  |  Bajty dysku logicznego na sekundę  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  odczyty Average_Page/s  |  Odczyty strony/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Page Zapisy/s  |  Zapis strony/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Pages/s  |  Strony/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Pct uprzywilejowany czas  |  Pct Uprzywilejowany czas  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Pct czas użytkownika  |  Pct Czas użytkownika  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Physical bajtów dysku na sekundę  |  Bajty dysku fizycznego/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Processes  |  Procesy  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Długość Average_Processor kolejki  |  Długość kolejki procesora  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Size przechowywane w plikach stronicowania  |  Rozmiar przechowywany w plikach stronicowania  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  bajty Average_Total  |  Całkowita liczba bajtów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Odebrano bajty Average_Total  |  Całkowita liczba odebranych bajtów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Total bajtów przesyłanych  |  Łączna liczba przesłanych bajtów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Kolizje Average_Total  |  Łączna liczba kolizji  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Odebrane Average_Total pakiety  |  Łączna liczba odebranych pakietów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Total przesyłane pakiety  |  Łączna liczba przesłanych pakietów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Total błędy Rx  |  Całkowita liczba błędów Rx  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Total błędy TX  |  Całkowita liczba błędów TX  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Uptime  |  Uptime  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Used przestrzeni wymiany bajtów  |  Używana przestrzeń wymiany bajtów MBytes  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Used pamięci kBytes  |  Używane pamięci kBytes  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Used MBytes pamięci  |  Używane mbyty pamięci  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Users  |  Użytkownicy  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Average_Virtual pamięć udostępniona  |  Wirtualna pamięć współdzielona  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Wydarzenie  |  Wydarzenie  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.OperationalInsights/obszary robocze  |  Puls  |  Puls  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.OperationalInsights/obszary robocze  |  Aktualizacja  |  Aktualizacja  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.PowerBIDedicated/capacitys  |  memory_metric  |  Memory (Pamięć)  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.PowerBIDedicated/capacitys  |  memory_thrashing_metric  |  Potrząsanie pamięci (zestawy danych)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.PowerBIDedicated/capacitys  |  qpu_high_utilization_metric  |  Wysokie wykorzystanie jednostek QPU  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.PowerBIDedicated/capacitys  |  Wyszukiwanie zapytań  |  Czas trwania kwerendy (zestawy danych)  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.PowerBIDedicated/capacitys  |  QueryPoolJobQueueLength  |  Długość kolejki zadań puli kwerend (zestawy danych)  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.Relay/przestrzenie nazw  |  ActiveConnections (Aktywne połączenia)  |  ActiveConnections (Aktywne połączenia)  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Relay/przestrzenie nazw  |  AktywneListenery  |  AktywneListenery  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Relay/przestrzenie nazw  |  BajtyPrzejstrzej  |  BajtyPrzejstrzej  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Relay/przestrzenie nazw  |  OdbiornikConnections-ClientError  |  OdbiornikConnections-ClientError  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Relay/przestrzenie nazw  |  OdbiornikConnections-ServerError  |  OdbiornikConnections-ServerError  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Relay/przestrzenie nazw  |  ListenerConnections-Sukces  |  ListenerConnections-Sukces  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Relay/przestrzenie nazw  |  Połączenia słuchaczy-TotalRequests  |  Połączenia słuchaczy-TotalRequests  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Relay/przestrzenie nazw  |  OdbiornikRozłącza  |  OdbiornikRozłącza  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Relay/przestrzenie nazw  |  SenderConnections-ClientError  |  SenderConnections-ClientError  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Relay/przestrzenie nazw  |  SenderConnections-ServerError  |  SenderConnections-ServerError  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Relay/przestrzenie nazw  |  SenderConnections-Sukces  |  SenderConnections-Sukces  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Relay/przestrzenie nazw  |  Połączenia nadawców-TotalRequests  |  Połączenia nadawców-TotalRequests  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Relay/przestrzenie nazw  |  SenderDisconnects  |  SenderDisconnects  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Search/searchSługs  |  SearchLatency (WyszukiwanieLatency)  |  Opóźnienie wyszukiwania  |  Sekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Search/searchSługs  |  SearchQueriesPerSecond  |  Zapytania wyszukiwania na sekundę  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Search/searchSługs  |  ThrottledSearchQueriesPercentage  |  Ograniczona wartość procentowa zapytań  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceBus/przestrzenie nazw  |  ActiveConnections (Aktywne połączenia)  |  ActiveConnections (Aktywne połączenia)  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.ServiceBus/przestrzenie nazw  |  ActiveMessages (Aktywne"  |  Liczba aktywnych wiadomości w kolejce/temacie.  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceBus/przestrzenie nazw  |  Połączenia Zamknięte  |  Połączenia zamknięte.  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceBus/przestrzenie nazw  |  ConnectionsOtwarte  |  Połączenia otwarte.  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceBus/przestrzenie nazw  |  CPUXNS ( CPUXNS )  |  Procesor (przestarzały)  |  Wartość procentowa  |  Maksimum | 
| Nie  | Nie |  Microsoft.ServiceBus/przestrzenie nazw  |  DeadletteredNadycje  |  Liczba wiadomości utraconych w kolejce/temacie.  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.ServiceBus/przestrzenie nazw  |  Przychodzącenaśniacje  |  Wiadomości przychodzące  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ServiceBus/przestrzenie nazw  |  Przychodzącerepy  |  Żądania przychodzące  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.ServiceBus/przestrzenie nazw  |  Komunikaty  |  Liczba wiadomości w kolejce/temacie.  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceBus/przestrzenie nazw  |  NamespaceCpuUsage  |  Procesor CPU  |  Wartość procentowa  |  Maksimum | 
| Nie  | Nie |  Microsoft.ServiceBus/przestrzenie nazw  |  NamespaceMemoryUsage  |  Użycie pamięci  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.ServiceBus/przestrzenie nazw  |  Wychodzące Wiadomości  |  Wiadomości wychodzące  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.ServiceBus/przestrzenie nazw  |  Zaplanowane-Messages  |  Liczba zaplanowanych wiadomości w kolejce/temacie.  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceBus/przestrzenie nazw  |  Serwery serwerowe  |  Błędy serwera.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.ServiceBus/przestrzenie nazw  |  Rozmiar  |  Rozmiar  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceBus/przestrzenie nazw  |  Udane prośby  |  Pomyślne żądania  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.ServiceBus/przestrzenie nazw  |  ThrottledRequests  |  Ograniczone żądania.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.ServiceBus/przestrzenie nazw  |  UserErrors (UżytkownikErrors)  |  Błędy użytkownika.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.ServiceBus/przestrzenie nazw  |  WSXNS ( WSXNS )  |  Użycie pamięci (przestarzałe)  |  Wartość procentowa  |  Maksimum | 
| Nie  | Nie |  Microsoft.ServiceFabricMesh/applications  |  Rzeczywista protokółCpu  |  Rzeczywista protokółCpu  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceFabricMesh/applications  |  Wartość rzeczywista  |  Wartość rzeczywista  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceFabricMesh/applications  |  AllocatedCpu (Protokół zaalokowany)  |  AllocatedCpu (Protokół zaalokowany)  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceFabricMesh/applications  |  AllocatedMemory (Memory)  |  AllocatedMemory (Memory)  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceFabricMesh/applications  |  Stan aplikacji  |  Stan aplikacji  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceFabricMesh/applications  |  Stan kontenera  |  Stan kontenera  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceFabricMesh/applications  |  Utilizacja procesora  |  Utilizacja procesora  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceFabricMesh/applications  |  MemoryUtilization (Funkcja MemoryUtilization)  |  MemoryUtilization (Funkcja MemoryUtilization)  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceFabricMesh/applications  |  Liczba restartów  |  Liczba restartów  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceFabricMesh/applications  |  ServiceReplicaStatus (Stan ServiceReplica)  |  ServiceReplicaStatus (Stan ServiceReplica)  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.ServiceFabricMesh/applications  |  Stan serwisu  |  Stan serwisu  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.SignalRService/SignalrService/Signalr  |  Liczba połączeń  |  Liczba połączeń  |  Liczba  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.SignalRService/SignalrService/Signalr  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.SignalRService/SignalrService/Signalr  |  Liczba wiadomości  |  Liczba wiadomości  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.SignalRService/SignalrService/Signalr  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.SignalRService/SignalrService/Signalr  |  SystemErrors (SystemErrors)  |  Błędy systemu  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.SignalRService/SignalrService/Signalr  |  UserErrors (UżytkownikErrors)  |  Błędy użytkownika  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.Sql/managedInstances  |  avg_cpu_percent  |  Średni procent procesora  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/managedInstances  |  io_bytes_read  |  Odczyt bajtów we/wy  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/managedInstances  |  io_bytes_written  |  Bajty we/wy napisane  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/managedInstances  |  io_requests  |  Liczba żądań we/wy  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/managedInstances  |  reserved_storage_mb  |  Zarezerwowane miejsce do przechowywania  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/managedInstances  |  storage_space_used_mb  |  Wykorzystane miejsce do przechowywania  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/managedInstances  |  virtual_core_count  |  Liczba rdzeni wirtualnych  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/serwery  |  database_dtu_consumption_percent  |  Procent użycia jednostek DTU  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/serwery  |  database_storage_used  |  Wykorzystano miejsce na dane  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/serwery  |  dtu_consumption_percent  |  Procent użycia jednostek DTU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/serwery  |  dtu_used  |  Używane DTU  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/serwery  |  storage_used  |  Wykorzystano miejsce na dane  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/serwery/bazy danych  |  allocated_data_storage  |  Przydzielone miejsce na dane  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/serwery/bazy danych  |  app_cpu_billed  |  Obciążenie procesora aplikacji naliczane  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Sql/serwery/bazy danych  |  app_cpu_percent  |  Procent procesora aplikacji  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/serwery/bazy danych  |  app_memory_percent  |  Procent pamięci aplikacji  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/serwery/bazy danych  |  blocked_by_firewall  |  Zablokowane przez zaporę sieciową  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Sql/serwery/bazy danych  |  cache_hit_percent  |  Procent trafienia w pamięci podręcznej  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Sql/serwery/bazy danych  |  cache_used_percent  |  Wartość procentowa używanej pamięci podręcznej  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.Sql/serwery/bazy danych  |  connection_failed  |  Połączenia zakończone niepowodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Sql/serwery/bazy danych  |  connection_successful  |  Udane połączenia  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Sql/serwery/bazy danych  |  cpu_limit  |  Limit procesora  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/serwery/bazy danych  |  cpu_percent  |  Procent użycia procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/serwery/bazy danych  |  cpu_used  |  Używany procesor  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/serwery/bazy danych  |  Zakleszczenie  |  Zakleszczenia  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Sql/serwery/bazy danych  |  dtu_consumption_percent  |  Procent użycia jednostek DTU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/serwery/bazy danych  |  dtu_limit  |  DTU Limit  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/serwery/bazy danych  |  dtu_used  |  Używane DTU  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/serwery/bazy danych  |  dwu_consumption_percent  |  Dwu procent  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Sql/serwery/bazy danych  |  dwu_limit  |  Limit DWU  |  Liczba  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.Sql/serwery/bazy danych  |  dwu_used  |  DWU używany  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Sql/serwery/bazy danych  |  local_tempdb_usage_percent  |  Lokalny procent tempdb  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/serwery/bazy danych  |  log_write_percent  |  Log Procent we/wy  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/serwery/bazy danych  |  memory_usage_percent  |  Procent pamięci  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.Sql/serwery/bazy danych  |  physical_data_read_percent  |  Procent użycia operacji we/wy na danych  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/serwery/bazy danych  |  sessions_percent  |  Procent sesji  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/serwery/bazy danych  |  magazyn  |  Wykorzystano miejsce na dane  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Sql/serwery/bazy danych  |  storage_percent  |  Procent wykorzystanej przestrzeni danych  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Sql/serwery/bazy danych  |  tempdb_data_size  |  Kilobajty rozmiaru pliku danych tempdb  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Sql/serwery/bazy danych  |  tempdb_log_size  |  Kilobajty rozmiaru pliku dziennika tempdb  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Sql/serwery/bazy danych  |  tempdb_log_used_percent  |  Używany dziennik procentu tempdb  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.Sql/serwery/bazy danych  |  workers_percent  |  Procent pracowników  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/serwery/bazy danych  |  xtp_storage_percent  |  Procent pamięci OLTP  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage  |  Przydzielone miejsce na dane  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage_percent  |  Procent przydzielonego miejsca na dane  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Sql/servers/elasticPools  |  cpu_limit  |  Limit procesora  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/elasticPools  |  cpu_percent  |  Procent użycia procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/servers/elasticPools  |  cpu_used  |  Używany procesor  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/servers/elasticPools  |  database_allocated_data_storage  |  Przydzielone miejsce na dane  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/servers/elasticPools  |  database_cpu_limit  |  Limit procesora  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/servers/elasticPools  |  database_cpu_percent  |  Procent użycia procesora CPU  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/servers/elasticPools  |  database_cpu_used  |  Używany procesor  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/servers/elasticPools  |  database_dtu_consumption_percent  |  Procent użycia jednostek DTU  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/servers/elasticPools  |  database_eDTU_used  |  EDTU używane  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/servers/elasticPools  |  database_log_write_percent  |  Log Procent we/wy  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/servers/elasticPools  |  database_physical_data_read_percent  |  Procent użycia operacji we/wy na danych  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/servers/elasticPools  |  database_sessions_percent  |  Procent sesji  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/servers/elasticPools  |  database_storage_used  |  Wykorzystano miejsce na dane  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft.Sql/servers/elasticPools  |  database_workers_percent  |  Procent pracowników  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/elasticPools  |  dtu_consumption_percent  |  Procent użycia jednostek DTU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/servers/elasticPools  |  eDTU_limit  |  Limit eDTU  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/elasticPools  |  eDTU_used  |  EDTU używane  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/elasticPools  |  log_write_percent  |  Log Procent we/wy  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/elasticPools  |  physical_data_read_percent  |  Procent użycia operacji we/wy na danych  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/elasticPools  |  sessions_percent  |  Procent sesji  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/servers/elasticPools  |  storage_limit  |  Maksymalny rozmiar danych  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/servers/elasticPools  |  storage_percent  |  Procent wykorzystanej przestrzeni danych  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/servers/elasticPools  |  storage_used  |  Wykorzystano miejsce na dane  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Sql/servers/elasticPools  |  tempdb_data_size  |  Kilobajty rozmiaru pliku danych tempdb  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_size  |  Kilobajty rozmiaru pliku dziennika tempdb  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_used_percent  |  Używany dziennik procentu tempdb  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/elasticPools  |  workers_percent  |  Procent pracowników  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Sql/servers/elasticPools  |  xtp_storage_percent  |  Procent pamięci OLTP  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageKondyje  |  Dostępność  |  Dostępność  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageKondyje  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Storage/storageKondyje  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Storage/storageKondyje  |  SuccessE2ELatency  |  Opóźnienie E2E dla powodzenia  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageKondyje  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Storage/storageKondyje  |  Transakcje  |  Transakcje  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft.Storage/storageKondyje  |  UżywaneCapacity  |  Używana pojemność  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/obiekt usługi  |  Dostępność  |  Dostępność  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/obiekt usługi  |  BlobCapacity (BlobCapacity)  |  Pojemność obiektu blob  |  Bajty  |  Średnia | 
| Nie  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/obiekt usługi  |  Liczba obiektów BlobCount  |  Liczba obiektów blob  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/obiekt usługi  |  Liczba kontenerów  |  Liczba kontenerów obiektów blob  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/obiekt usługi  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/obiekt usługi  |  Indeks Zdolności  |  Pojemność indeksu  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/obiekt usługi  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/obiekt usługi  |  SuccessE2ELatency  |  Opóźnienie E2E dla powodzenia  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/obiekt usługi  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/obiekt usługi  |  Transakcje  |  Transakcje  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Magazyn microsoft.storage/storageKonta/fileSługi  |  Dostępność  |  Dostępność  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Magazyn microsoft.storage/storageKonta/fileSługi  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Magazyn microsoft.storage/storageKonta/fileSługi  |  Zdolność do pończęcia plików  |  Pojemność pliku  |  Bajty  |  Średnia | 
| Nie  | Nie |  Magazyn microsoft.storage/storageKonta/fileSługi  |  Liczba plików  |  Liczba plików  |  Liczba  |  Średnia | 
| Nie  | Nie |  Magazyn microsoft.storage/storageKonta/fileSługi  |  Liczba plików ShareCount  |  Liczba udziałów plików  |  Liczba  |  Średnia | 
| Nie  | Nie |  Magazyn microsoft.storage/storageKonta/fileSługi  |  FileShareQuota (Polski)  |  Rozmiar przydziału udziału plików  |  Bajty  |  Średnia | 
| Nie  | Nie |  Magazyn microsoft.storage/storageKonta/fileSługi  |  Konto FileShareSnapshotCount  |  Liczba migawek udziału plików  |  Liczba  |  Średnia | 
| Nie  | Nie |  Magazyn microsoft.storage/storageKonta/fileSługi  |  Rozmiar plikuShareSnapshotSize  |  Rozmiar migawki udziału plików  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Magazyn microsoft.storage/storageKonta/fileSługi  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Magazyn microsoft.storage/storageKonta/fileSługi  |  SuccessE2ELatency  |  Opóźnienie E2E dla powodzenia  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Magazyn microsoft.storage/storageKonta/fileSługi  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Magazyn microsoft.storage/storageKonta/fileSługi  |  Transakcje  |  Transakcje  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/kolejkaUsługi  |  Dostępność  |  Dostępność  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/kolejkaUsługi  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/kolejkaUsługi  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/kolejkaUsługi  |  QueueCapacity (Zdolność do kolejek)  |  Pojemność kolejki  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/kolejkaUsługi  |  Liczba kolejek  |  Liczba kolejek  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/kolejkaUsługi  |  KolejkaLiczna liczba  |  Liczba wiadomości w kolejce  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/kolejkaUsługi  |  SuccessE2ELatency  |  Opóźnienie E2E dla powodzenia  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/kolejkaUsługi  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/kolejkaUsługi  |  Transakcje  |  Transakcje  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/tabelaUsługi  |  Dostępność  |  Dostępność  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/tabelaUsługi  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/tabelaUsługi  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/tabelaUsługi  |  SuccessE2ELatency  |  Opóźnienie E2E dla powodzenia  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/tabelaUsługi  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/tabelaUsługi  |  TableCapacity (Zdolność do pońoliczy)  |  Pojemność tabeli  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/tabelaUsługi  |  Liczba tabel  |  Liczba tabel  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/tabelaUsługi  |  Liczba entity tabeli  |  Liczba jednostek tabeli  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Magazyn/magazyn firmy MicrosoftKonserwowanie/tabelaUsługi  |  Transakcje  |  Transakcje  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StorageCache/caches  |  KlientIOPS  |  Całkowita liczba we/wy klienta  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.StorageCache/caches  |  KlientLatency  |  Średnie opóźnienie klienta  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.StorageCache/caches  |  ClientLockIOPS  |  We/wy blokady klienta  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.StorageCache/caches  |  ClientMetadataReadIOPS (ClientMetadataReadIOPS)  |  Odczytywanie przez nieprzechodnień we/wy między metadanych klienta  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.StorageCache/caches  |  ClientMetadataWriteIOPS  |  Zapis iOPS metadanych klienta  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.StorageCache/caches  |  ClientReadIOPS (ClientReadIOPS)  |  Odczyt klienta We/Wy  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.StorageCache/caches  |  Przepustowość doczynienia obsługi klienta  |  Średnia przepływność odczytu pamięci podręcznej  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.StorageCache/caches  |  KlientWriteIOPS  |  Zapis klienta We/Wy  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.StorageCache/caches  |  KlientWriteThroughput  |  Średnia przepływność zapisu w pamięci podręcznej  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.StorageCache/caches  |  PrzechowywanieTargetAsyncWriteThroughput  |  Przepływność zapisu asynchronitycznego StorageTarget  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.StorageCache/caches  |  PrzechowywanieTargetFillThroughput  |  Przepływność wypełniania nastawy dla celów magazynu  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.StorageCache/caches  |  StorageTargetHealth (Zdrowie celów pamięci masowej)  |  Kondycja obiektu docelowego magazynu  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.StorageCache/caches  |  StorageTargetIOPS  |  Całkowita liczba we/wy dla magazynu  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.StorageCache/caches  |  Sposób działania celów magazynu  |  Opóźnienie celu magazynu  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.StorageCache/caches  |  StorageTargetMetadataReadIOPS  |  Odczyt iops metadanych storagetarget  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.StorageCache/caches  |  StorageTargetMetadataEeEiteIOPS  |  Identyfikatory We/Wy dotyczące zapisu metadanych storagetarget  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.StorageCache/caches  |  StorageTargetReadAheadThroughput  |  Przepływność odczytu do odczytu z wyprzedzeniem w celu przechowywania  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.StorageCache/caches  |  StorageTargetReadIOPS  |  Odczyt iOPS z odczytem storagetarget  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.StorageCache/caches  |  PrzechowywanieTargetSyncWriteThroughput  |  Synchronicz zapisu StorageTarget  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.StorageCache/caches  |  StorageTargetTotalReadThroughput  |  Całkowita przepływność odczytu w celu całkowitej dojście do magazynu  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.StorageCache/caches  |  StorageTargetTotalWriteThroughput  |  Całkowita przepływność zapisu w celu zasysanie celu  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.StorageCache/caches  |  StorageTargetWriteIOPS  |  PrzechowywanieTarget Zapisu We/Wy  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.StorageCache/caches  |  Uptime  |  Uptime  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices  |  ServerSyncSessionResult  |  Wynik sesji synchronizacji  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices  |  Pamięć masowa SynchronizcBatchTransferredFileBytes  |  Bajty zsynchronizowane  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledNetworkBytesApplication  |  Rozmiar odwołania warstwowego w chmurze według aplikacji  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledTotalNetworkBytes  |  Rozmiar odwołania warstwowego chmury  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices  |  Pamięć masowaSyncRecallIOTotalSizeBytes  |  Przywoływanie warstw w chmurze  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecallThroughPutBytesPerSecond  |  Przepływność wycofywania warstwowego w chmurze  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices  |  Pamięć Pamięci Masowego SerweraHeartbeat  |  Stan usługi Online serwera  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices  |  StorageSyncSyncSessionAppliedFilesCount  |  Pliki zsynchronizowane  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices  |  Pamięć Programu StorageSyncSyncSessionPerItemErrorsCount  |  Pliki niezsynchronizowane  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices/registeredServers  |  ServerHeartbeat (ServerHeartbeat)  |  Stan usługi Online serwera  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices/registeredServers  |  ServerRecallIOTotalSizeBytes  |  Przywoływanie warstw w chmurze  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupBatchTransferredFileBytes  |  Bajty zsynchronizowane  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionAppliedFilesCount  |  Pliki zsynchronizowane  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionPerItemErrorsCount  |  Pliki niezsynchronizowane  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointBatchTransferredFileBytes  |  Bajty zsynchronizowane  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionAppliedFilesCount  |  Pliki zsynchronizowane  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionPerItemErrorsCount  |  Pliki niezsynchronizowane  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalityka/szybkość przesyłania strumieniowego  |  AMLCalloutFailedZasty  |  Nieudane żądania funkcji  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalityka/szybkość przesyłania strumieniowego  |  AMLCalloutInputAvents  |  Zdarzenia funkcji  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalityka/szybkość przesyłania strumieniowego  |  AMLCalloutZasuty  |  Żądania funkcji  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalityka/szybkość przesyłania strumieniowego  |  KonwersjeErrors  |  Błędy konwersji danych  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalityka/szybkość przesyłania strumieniowego  |  DeserializacjaError  |  Błędy deserializacji danych wejściowych  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalityka/szybkość przesyłania strumieniowego  |  DroppedOrAdjustedWydarzenia  |  Zdarzenia poza kolejnością  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalityka/szybkość przesyłania strumieniowego  |  EarlyInputEvents  |  Zdarzenia wczesnego wprowadzania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalityka/szybkość przesyłania strumieniowego  |  Errors  |  Błędy środowiska uruchomieniowego  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalityka/szybkość przesyłania strumieniowego  |  InputEventBytes (WejściaEwentnebyty)  |  Bajty zdarzenia wejściowego  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalityka/szybkość przesyłania strumieniowego  |  WejściaUchny  |  Zdarzenia wejściowe  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalityka/szybkość przesyłania strumieniowego  |  Dane wejściaEventsSourcesBacklogged  |  Zaległe zdarzenia wejściowe  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.StreamAnalityka/szybkość przesyłania strumieniowego  |  Dane wejścioweSourcesPerSecond  |  Odebrane źródła wejściowe  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalityka/szybkość przesyłania strumieniowego  |  LateInputEvents  |  Zdarzenia późnego wprowadzania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalityka/szybkość przesyłania strumieniowego  |  Dane wyjścioweWzdowy  |  Zdarzenia wyjściowe  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.StreamAnalityka/szybkość przesyłania strumieniowego  |  WyjścieWodawodnikDelaySekundy  |  Opóźnienie znaku wodnego  |  Sekundy  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.StreamAnalityka/szybkość przesyłania strumieniowego  |  Alokacja zasobów  |  Wykorzystanie SU %  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft.VMwareCloudSimple/virtualMachines  |  Bajty odczytu dysku  |  Bajty odczytu dysku  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.VMwareCloudSimple/virtualMachines  |  Operacje odczytu dysku/s  |  Operacje odczytu dysku/s  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.VMwareCloudSimple/virtualMachines  |  Bajty zapisu dysku  |  Bajty zapisu dysku  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.VMwareCloudSimple/virtualMachines  |  Operacje zapisu dysku/s  |  Operacje zapisu dysku/s  |  Połów połówek  |  Średnia | 
| **Tak**  | Nie |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadBytesPerSecond  |  Bajty odczytu dysku/s  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadLatency (DiskReadLatency)  |  Opóźnienie odczytu dysku  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadOperations (Działania diskread)  |  Operacje odczytu dysku  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteBytesPerSekund  |  Bajty zapisu dysku/s  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteLatency (Właswoenie dysku)  |  Opóźnienie zapisu dysku  |  Milisekund  |  Średnia | 
| **Tak**  | Nie |  Microsoft.VMwareCloudSimple/virtualMachines  |  Działania diskwrite  |  Operacje zapisu dysku  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.VMwareCloudSimple/virtualMachines  |  PamięćAktywna  |  Pamięć aktywna  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.VMwareCloudSimple/virtualMachines  |  PamięćGranted  |  Przyznano pamięć  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.VMwareCloudSimple/virtualMachines  |  Użycie pamięci  |  Używana pamięć  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft.VMwareCloudSimple/virtualMachines  |  Sieć — wejście  |  Sieć — wejście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.VMwareCloudSimple/virtualMachines  |  Sieć — wyjście  |  Sieć — wyjście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkInBytesPerSecond  |  Sieć w bajtach/s  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkOutBytesPerSecond  |  Bajty wyjścia sieci/s  |  PrzeztówPerSekunda  |  Średnia | 
| **Tak**  | Nie |  Microsoft.VMwareCloudSimple/virtualMachines  |  Procentowe użycie procesora CPU  |  Procentowe użycie procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.VMwareCloudSimple/virtualMachines  |  PercentageCpuReady (ProcentcpuReady)  |  Procentowa gotowość procesora  |  Milisekund  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowiska/multiRolePools  |  Prośby o activerequests  |  Aktywne żądania  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowiska/multiRolePools  |  ŚredniaResponseTime  |  Średni czas reakcji  |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowiska/multiRolePools  |  Bytesreceived  |  Dane w  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowiska/multiRolePools  |  Bajtów  |  Wyjście danych  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowiska/multiRolePools  |  CpuPercentage (CpuPercentage)  |  Procent użycia procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowiska/multiRolePools  |  Funkcja DiskQueueLength  |  Długość kolejki dysku  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowiska/multiRolePools  |  Http101  |  Http 101  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowiska/multiRolePools  |  Protokół Http2xx  |  Http 2xx  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowiska/multiRolePools  |  Protokół Http3xx  |  Http 3xx  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowiska/multiRolePools  |  Http401  |  Http 401  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowiska/multiRolePools  |  Http403  |  Http 403  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowiska/multiRolePools  |  Http404  |  Http 404  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowiska/multiRolePools  |  Http406  |  Http 406  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowiska/multiRolePools  |  Protokół Http4xx (polski)  |  Http 4xx  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowiska/multiRolePools  |  Protokół Http5xx  |  Błędy serwera http  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowiska/multiRolePools  |  Funkcja HttpQueueLength  |  Długość kolejki http  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowiska/multiRolePools  |  Duże usługi Nainstancje MapAppServicePlan  |  Pracownicy planu dużych usług aplikacji  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowiska/multiRolePools  |  ŚredniAppServicePlanInstans  |  Pracownicy średniego planu usługi aplikacji  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowiska/multiRolePools  |  MemoryPercentage (PamięćPrzestńczliwienie)  |  Procent pamięci  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowiska/multiRolePools  |  Żądania  |  Żądania  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowiska/multiRolePools  |  SmallAppServicePlanInstans (Nienasycenie smallappserviceplan)  |  Pracownicy planu usługi aplikacji dla małych aplikacji  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowiska/multiRolePools  |  TotalFrontEnds (Całkowita liczba podań)  |  Całkowite przednie końce  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowienia/pracownicy Pools  |  CpuPercentage (CpuPercentage)  |  Procent użycia procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowienia/pracownicy Pools  |  MemoryPercentage (PamięćPrzestńczliwienie)  |  Procent pamięci  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowienia/pracownicy Pools  |  PracownicyDostępni  |  Dostępni pracownicy  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowienia/pracownicy Pools  |  PracownicyWzrzesi  |  Łączna liczba pracowników  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/hostingŚrodowienia/pracownicy Pools  |  PracownicyUżywane  |  Pracownicy zaustosowni  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/farmy serwerów  |  Bytesreceived  |  Dane w  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/farmy serwerów  |  Bajtów  |  Wyjście danych  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/farmy serwerów  |  CpuPercentage (CpuPercentage)  |  Procent użycia procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/farmy serwerów  |  Funkcja DiskQueueLength  |  Długość kolejki dysku  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/farmy serwerów  |  Funkcja HttpQueueLength  |  Długość kolejki http  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/farmy serwerów  |  MemoryPercentage (PamięćPrzestńczliwienie)  |  Procent pamięci  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/farmy serwerów  |  TcpCloseWait (TcpCloseWait)  |  Oczekiwanie zamknięcia protokołu TCP  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/farmy serwerów  |  TcpClosing (Nie ma)  |  Zamykanie protokołu TCP  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/farmy serwerów  |  TcpNaestablishowany  |  TCP ustanowiony  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/farmy serwerów  |  TcpFinWait1 (Wacik.  |  TCP Fin Czekaj 1  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/farmy serwerów  |  TcpFinWait2 (Wacik.  |  TCP Fin Czekaj 2  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/farmy serwerów  |  TcpLastAck (TcpLastAck)  |  TCP Ostatni Ack  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/farmy serwerów  |  TcpSynReceived  |  Odebrano syn TCP  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/farmy serwerów  |  TcpSynsent (wytłk, w tym)  |  Wysłane syn TCP  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/farmy serwerów  |  TcpTimeWait (TcpTimeWait)  |  Oczekiwanie czasu TCP  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  Połączenia z aplikacjami  |  Połączenia  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  ŚredniaZapraca Zestaw  |  Zestaw pracy średniej pamięci  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  ŚredniaResponseTime  |  Średni czas reakcji  |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  Bytesreceived  |  Dane w  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  Bajtów  |  Wyjście danych  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  CpuTime (Czas procesora)  |  Czas procesora  |  Sekundy  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  CurrentAssemblies  |  Bieżące złożenia  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  FunkcjaWykonaniaLicznik  |  Liczba wykonania funkcji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  Jednostki funkcyjne  |  Jednostki wykonawcze funkcji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  Gen0Kolekcje  |  Odpady odśmieskowe Gen 0  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  Gen1Kolekcje  |  Wyrzucanie elementów bezużytecznych gen 1  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  Gen2Kolekcje  |  Odpady odśmieskowe gen 2  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  Handles  |  Liczba dojść  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  Stan zdrowiaSeksus  |  Stan kontroli kondycji  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  Http101  |  Http 101  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  Protokół Http2xx  |  Http 2xx  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  Protokół Http3xx  |  Http 3xx  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  Http401  |  Http 401  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  Http403  |  Http 403  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  Http404  |  Http 404  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  Http406  |  Http 406  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  Protokół Http4xx (polski)  |  Http 4xx  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  Protokół Http5xx  |  Błędy serwera http  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  Funkcja HttpResponseTime  |  Czas reakcji  |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  IoOtherBytesPerSekund  |  IO inne bajty na sekundę  |  PrzeztówPerSekunda  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  IoOtherOperationsPerSecond  |  Inne operacje we/wy na sekundę  |  PrzeztówPerSekunda  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  IoReadBytesPerSekund  |  Bajty odczytu we/wy na sekundę  |  PrzeztówPerSekunda  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  IoReadOperationsPerSecond  |  Operacje odczytu we/wy na sekundę  |  PrzeztówPerSekunda  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  IoWriteBytesPerSekund  |  Bajty zapisu we/wy na sekundę  |  PrzeztówPerSekunda  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  IoWriteOperationsPerSecond  |  Operacje zapisu we/wy na sekundę  |  PrzeztówPerSekunda  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  Zestaw pamięci  |  Zestaw roboczy pamięci  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  Bajty prywatne  |  Bajty prywatne  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  Żądania  |  Żądania  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  RequestsInApplicationQueue  |  Żądania w kolejce aplikacji  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  Wątki  |  Liczba wątków  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  TotalAppDomains (TotalAppDomains)  |  Łączna liczba domen aplikacji  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/witryny  |  TotalAppDomainsNiezaładkłano  |  Całkowita liczba niezaładowanych domen aplikacji  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Połączenia z aplikacjami  |  Połączenia  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  ŚredniaZapraca Zestaw  |  Zestaw pracy średniej pamięci  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  ŚredniaResponseTime  |  Średni czas reakcji  |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Bytesreceived  |  Dane w  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Bajtów  |  Wyjście danych  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  CpuTime (Czas procesora)  |  Czas procesora  |  Sekundy  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  CurrentAssemblies  |  Bieżące złożenia  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  FunkcjaWykonaniaLicznik  |  Liczba wykonania funkcji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Jednostki funkcyjne  |  Jednostki wykonawcze funkcji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Gen0Kolekcje  |  Odpady odśmieskowe Gen 0  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Gen1Kolekcje  |  Wyrzucanie elementów bezużytecznych gen 1  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Gen2Kolekcje  |  Odpady odśmieskowe gen 2  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Handles  |  Liczba dojść  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Stan zdrowiaSeksus  |  Stan kontroli kondycji  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Http101  |  Http 101  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Protokół Http2xx  |  Http 2xx  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Protokół Http3xx  |  Http 3xx  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Http401  |  Http 401  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Http403  |  Http 403  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Http404  |  Http 404  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Http406  |  Http 406  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Protokół Http4xx (polski)  |  Http 4xx  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Protokół Http5xx  |  Błędy serwera http  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Funkcja HttpResponseTime  |  Czas reakcji  |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  IoOtherBytesPerSekund  |  IO inne bajty na sekundę  |  PrzeztówPerSekunda  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  IoOtherOperationsPerSecond  |  Inne operacje we/wy na sekundę  |  PrzeztówPerSekunda  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  IoReadBytesPerSekund  |  Bajty odczytu we/wy na sekundę  |  PrzeztówPerSekunda  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  IoReadOperationsPerSecond  |  Operacje odczytu we/wy na sekundę  |  PrzeztówPerSekunda  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  IoWriteBytesPerSekund  |  Bajty zapisu we/wy na sekundę  |  PrzeztówPerSekunda  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  IoWriteOperationsPerSecond  |  Operacje zapisu we/wy na sekundę  |  PrzeztówPerSekunda  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Zestaw pamięci  |  Zestaw roboczy pamięci  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Bajty prywatne  |  Bajty prywatne  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Żądania  |  Żądania  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  RequestsInApplicationQueue  |  Żądania w kolejce aplikacji  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  Wątki  |  Liczba wątków  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  TotalAppDomains (TotalAppDomains)  |  Łączna liczba domen aplikacji  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.Web/sites/slots  |  TotalAppDomainsNiezaładkłano  |  Całkowita liczba niezaładowanych domen aplikacji  |  Liczba  |  Średnia | 
