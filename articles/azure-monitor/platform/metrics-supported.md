---
title: Azure Monitor obsługiwane metryki według typu zasobu
description: Lista metryk dostępnych dla każdego typu zasobu z Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: dbbe5a667b8d467b416e4a4a571d8d3599ec45b6
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051817"
---
# <a name="supported-metrics-with-azure-monitor"></a>Obsługiwane metryki z Azure Monitor

Azure Monitor oferuje kilka sposobów współpracy z metrykami, w tym wykresów w portalu, uzyskiwania do nich dostępu za pośrednictwem interfejsu API REST lub wykonywania zapytań przy użyciu programu PowerShell lub interfejsu wiersza polecenia. Poniżej znajduje się pełna lista wszystkich metryk dostępnych obecnie z potoku metryk Azure Monitor. Inne metryki mogą być dostępne w portalu lub przy użyciu starszych interfejsów API. Ta lista zawiera tylko metryki dostępne przy użyciu potoku metryki Azure Monitor skonsolidowanej. Aby wykonać zapytanie o i uzyskać dostęp do tych metryk, użyj [wersji interfejsu api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Wysyłanie metryk wielowymiarowych za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
>
> *Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak w przypadku eksportowania za pomocą ustawień diagnostycznych metryka ta jest przedstawiana jako wszystkie komunikaty przychodzące we wszystkich kolejkach w centrum zdarzeń.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|Average|QPU. Zakres 0-100 dla S1, 0-200 dla S2 i 0-400 dla S4|ServerResourceType|
|memory_metric|Memory (Pamięć)|Bajty|Average|Rozmiar. Zakres 0-25 GB dla S1, 0-50 GB dla S2 i 0-100 GB dla S4|ServerResourceType|
|TotalConnectionRequests|Łączna liczba żądań połączenia|Count|Average|Łączna liczba żądań połączenia. Są to wejścia.|ServerResourceType|
|SuccessfullConnectionsPerSec|Udane połączenia na sekundę|CountPerSecond|Average|Szybkość pomyślnych połączeń.|ServerResourceType|
|TotalConnectionFailures|Łączna liczba błędów połączenia|Count|Average|Łączna liczba nieudanych prób połączenia.|ServerResourceType|
|CurrentUserSessions|Bieżące sesje użytkowników|Count|Average|Bieżąca liczba ustanowionych sesji użytkowników.|ServerResourceType|
|QueryPoolBusyThreads|Wątki zajęte w puli zapytań|Count|Average|Liczba zajętych wątków w puli wątków zapytań.|ServerResourceType|
|CommandPoolJobQueueLength|Długość kolejki zadań puli poleceń|Count|Average|Liczba zadań w kolejce puli wątków poleceń.|ServerResourceType|
|ProcessingPoolJobQueueLength|Długość kolejki zadań puli przetwarzania|Count|Average|Liczba zadań innych niż zadania we/wy w kolejce puli wątków przetwarzania.|ServerResourceType|
|Wartości CurrentConnections|Połączenie: Bieżące połączenia|Count|Average|Bieżąca liczba ustanowionych połączeń klientów.|ServerResourceType|
|CleanerCurrentPrice|Pamięć: Bieżąca cena oczyszczarki|Count|Average|Bieżąca cena pamięci, $/Byte/Time, znormalizowana do 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Pamięć: Zmniejszanie ilości pamięci czyszczącej|Bajty|Average|Ilość pamięci, w bajtach, podlegająca przeczyszczaniu przez oczyszczarkę w tle.|ServerResourceType|
|CleanerMemoryNonshrinkable|Pamięć: Pamięć oczyszczarki niemożliwa do zmniejszenia|Bajty|Average|Ilość pamięci (w bajtach), która nie podlega wyprzeczyszczaniu przez oczyszczarkę w tle.|ServerResourceType|
|MemoryUsage|Pamięć: Użycie pamięci|Bajty|Average|Użycie pamięci przez proces serwera jako używany do obliczania ceny pamięci czyszczącej. Równa się licznikowi Process\PrivateBytes i rozmiarowi danych mapowanych na pamięć, ignorowanie wszelkich pamięci, które zostały zamapowane lub przydzielone przez aparat analityczny w pamięci xVelocity (VertiPaq) przekraczający limit pamięci aparatu xVelocity.|ServerResourceType|
|MemoryLimitHard|Pamięć: Sztywny limit pamięci|Bajty|Average|Limit pamięci twardej z pliku konfiguracji.|ServerResourceType|
|MemoryLimitHigh|Pamięć: Limit pamięci — wysoki|Bajty|Average|Górny limit pamięci z pliku konfiguracji.|ServerResourceType|
|MemoryLimitLow|Pamięć: Limit pamięci — niski|Bajty|Average|Limit braku pamięci z pliku konfiguracji.|ServerResourceType|
|MemoryLimitVertiPaq|Pamięć: Limit pamięci — tryb VertiPaq|Bajty|Average|Limit w pamięci z pliku konfiguracji.|ServerResourceType|
|Limit przydziału|Pamięć: Limit przydziału|Bajty|Average|Bieżący limit pamięci (w bajtach). Przydział pamięci jest również nazywany przydziałem pamięci lub rezerwacją pamięci.|ServerResourceType|
|QuotaBlocked|Pamięć: Limit przydziału zablokowany|Count|Average|Bieżąca liczba żądań przydziałów blokowanych do momentu zwolnienia innych limitów przydziału pamięci.|ServerResourceType|
|VertiPaqNonpaged|Pamięć: Niestronicowana VertiPaq|Bajty|Average|Bajty pamięci zablokowane w zestawie roboczym do użycia przez aparat w pamięci.|ServerResourceType|
|VertiPaqPaged|Pamięć: Stronicowana VertiPaq|Bajty|Average|Bajty stronicowanej pamięci w użyciu dla danych w pamięci.|ServerResourceType|
|RowsReadPerSec|Rozpatrywan Odczytane wiersze na sekundę|CountPerSecond|Average|Współczynnik wierszy odczytanych ze wszystkich relacyjnych baz danych.|ServerResourceType|
|RowsConvertedPerSec|Rozpatrywan Przekonwertowane wiersze na sekundę|CountPerSecond|Average|Szybkość wierszy przekonwertowanych podczas przetwarzania.|ServerResourceType|
|RowsWrittenPerSec|Rozpatrywan Wiersze zapisywane na sekundę|CountPerSecond|Average|Częstotliwość wierszy zapisywana podczas przetwarzania.|ServerResourceType|
|CommandPoolBusyThreads|Wątk Zajęte wątki puli poleceń|Count|Average|Liczba zajętych wątków w puli wątków poleceń.|ServerResourceType|
|CommandPoolIdleThreads|Wątk Bezczynne wątki w puli poleceń|Count|Average|Liczba bezczynnych wątków w puli wątków poleceń.|ServerResourceType|
|LongParsingBusyThreads|Wątk Zajęte wątki o długotrwałej analizie|Count|Average|Liczba zajętych wątków w puli wątków o długotrwałej analizie.|ServerResourceType|
|LongParsingIdleThreads|Wątk Bezczynne wątki do długotrwałej analizy|Count|Average|Liczba bezczynnych wątków w puli wątków o długotrwałej analizie.|ServerResourceType|
|LongParsingJobQueueLength|Wątk Długość kolejki zadań o długotrwałej analizie|Count|Average|Liczba zadań w kolejce puli wątków o długotrwałej analizie.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Wątk Zajęte wątki zadań we/wy puli przetwarzania|Count|Average|Liczba wątków uruchamiających zadania we/wy w puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Wątk Zajęte wątki innych niż we/wy puli przetwarzania|Count|Average|Liczba wątków uruchamiających zadania inne niż we/wy w puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Wątk Długość kolejki zadań we/wy puli przetwarzania|Count|Average|Liczba zadań we/wy w kolejce puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Wątk Wątki zadań we/wy puli przetwarzania|Count|Average|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Wątk Bezczynne wątki inne niż we/wy puli przetwarzania|Count|Average|Liczba bezczynnych wątków w puli wątków przetwarzania dedykowanych dla zadań innych niż zadania we/wy.|ServerResourceType|
|QueryPoolIdleThreads|Wątk Bezczynne wątki puli zapytań|Count|Average|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|ServerResourceType|
|QueryPoolJobQueueLength|Wątk Długość kolejki zadań puli zapytań|Count|Average|Liczba zadań w kolejce puli wątków zapytań.|ServerResourceType|
|ShortParsingBusyThreads|Wątk Zajęte wątki z krótką analizą|Count|Average|Liczba zajętych wątków w puli wątków o krótkiej analizie.|ServerResourceType|
|ShortParsingIdleThreads|Wątk Bezczynne wątki analizy|Count|Average|Liczba bezczynnych wątków w puli wątków o krótkiej analizie.|ServerResourceType|
|ShortParsingJobQueueLength|Wątk Długość kolejki zadań o krótkiej analizie|Count|Average|Liczba zadań w kolejce puli wątków o krótkiej analizie.|ServerResourceType|
|memory_thrashing_metric|Migotanie pamięci|Percent|Average|Średnia pamięć migotanie.|ServerResourceType|
|mashup_engine_qpu_metric|M QPU aparatu|Count|Average|QPU użycie przez procesy aparatu mashupów|ServerResourceType|
|mashup_engine_memory_metric|Pamięć aparatu M|Bajty|Average|Użycie pamięci przez procesy aparatu mashupów|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Żądania|Żądania|Count|Łącznie|Całkowita liczba żądań bramy w danym okresie. Można je podzielić na różne wymiary, aby ułatwić diagnozowanie problemów. |Location, BackendResponseCode, LastErrorReason, GatewayResponseCode|
|TotalRequests|Łączna liczba żądań bramy|Count|Łącznie|Całkowita liczba żądań bramy w danym okresie. Ta Metryka jest przestarzała, zalecamy użycie nowej `Requests` metryki. |Lokalizacja, nazwa hosta|
|Żądania successfulrequests|Pomyślne żądania bramy|Count|Łącznie|Całkowita liczba pomyślnych żądań bramy w danym okresie. Ta Metryka jest przestarzała, zalecamy użycie nowej `Requests` metryki.|Lokalizacja, nazwa hosta|
|UnauthorizedRequests|Nieautoryzowane żądania bramy|Count|Łącznie| Całkowita liczba nieautoryzowanych żądań bramy w danym okresie. Ta Metryka jest przestarzała, zalecamy użycie nowej `Requests` metryki.|Lokalizacja, nazwa hosta|
|FailedRequests|Nieudane żądania bramy|Count|Łącznie|Całkowita liczba żądań bramy zakończonych niepowodzeniem w danym okresie. Ta Metryka jest przestarzała, zalecamy użycie nowej `Requests` metryki.|Lokalizacja, nazwa hosta|
|OtherRequests|Inne żądania bramy|Count|Łącznie|Całkowita liczba żądań bramy w danym okresie, które nie znajdują się w kategorii pomyślne, nieautoryzowane lub niepowodzenie. Ta Metryka jest przestarzała, zalecamy użycie nowej `Requests` metryki. |Lokalizacja, nazwa hosta|
|Duration|Całkowity czas trwania żądań bramy|MS|Average|Czas między API Management odbiera żądanie od klienta i zwraca odpowiedź do klienta.|Lokalizacja, nazwa hosta|
|Pojemność|Pojemność|Percent|Average|Wskaźnik obciążenia API Management wystąpienia w celu podejmowania świadomych decyzji o tym, czy skalować wystąpienie w celu zwiększenia obciążenia.|Location|
|EventHubTotalEvents|Łączna liczba zdarzeń EventHub|Count|Łącznie|Całkowita liczba zdarzeń wysyłanych do centrum EventHub z API Management w danym okresie.|Location|
|EventHubSuccessfulEvents|Pomyślne zdarzenia EventHub|Count|Łącznie|Całkowita liczba pomyślnych zdarzeń EventHub w danym okresie.|Location|
|EventHubTotalFailedEvents|Niepowodzenie zdarzeń EventHub|Count|Łącznie|Całkowita liczba nieudanych zdarzeń EventHub w danym okresie.|Location|
|EventHubRejectedEvents|Odrzucone zdarzenia EventHub|Count|Łącznie|Całkowita liczba odrzuconych zdarzeń EventHub (niewłaściwa konfiguracja lub nieautoryzowana) w danym okresie.|Location|
|EventHubThrottledEvents|Ograniczone zdarzenia EventHub|Count|Łącznie|Całkowita liczba zdarzeń zdarzenia EventHub z ograniczeniami w danym okresie.|Location|
|EventHubTimedoutEvents|Przekroczono limit czasu zdarzeń EventHub|Count|Łącznie|Całkowita liczba przekroczeń limitu czasu zdarzeń EventHub w danym okresie.|Location|
|EventHubDroppedEvents|Opuszczone zdarzenia EventHub|Count|Łącznie|Łączna liczba zdarzeń pominiętych ze względu na limit rozmiaru kolejki osiągnięty w danym okresie.|Location|
|EventHubTotalBytesSent|Rozmiar zdarzeń EventHub|Bajty|Łącznie|Łączny rozmiar zdarzeń EventHub w bajtach w danym okresie.|Location|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalJob|Łączna liczba zadań|Count|Łącznie|Całkowita liczba zadań|Element Runbook, stan|
|TotalUpdateDeploymentRuns|Łączna liczba przebiegów wdrożenia aktualizacji|Count|Łącznie|Łączna liczba przebiegów wdrażania aktualizacji oprogramowania|. Softwareupdateconfigurationname, stan|
|TotalUpdateDeploymentMachineRuns|Łączna liczba uruchomień maszyn wdrożenia aktualizacji|Count|Łącznie|Łączna liczba uruchomień maszyn wdrożenia aktualizacji oprogramowania w ramach wdrożenia aktualizacji oprogramowania|. Softwareupdateconfigurationname, status, TargetComputer,. Softwareupdateconfigurationrunid|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CoreCount|Liczba dedykowanych rdzeni|Count|Łącznie|Łączna liczba rdzeni dedykowanych na koncie wsadowym|Nie wymiarów|
|TotalNodeCount|Liczba dedykowanych węzłów|Count|Łącznie|Łączna liczba węzłów dedykowanych na koncie wsadowym|Nie wymiarów|
|LowPriorityCoreCount|LowPriority rdzeń|Count|Łącznie|Łączna liczba rdzeni o niskim priorytecie na koncie wsadowym|Nie wymiarów|
|TotalLowPriorityNodeCount|Liczba węzłów o niskim priorytecie|Count|Łącznie|Łączna liczba węzłów o niskim priorytecie na koncie wsadowym|Nie wymiarów|
|CreatingNodeCount|Tworzenie liczby węzłów|Count|Łącznie|Liczba tworzonych węzłów|Nie wymiarów|
|StartingNodeCount|Początkowa liczba węzłów|Count|Łącznie|Liczba początkowych węzłów|Nie wymiarów|
|WaitingForStartTaskNodeCount|Oczekiwanie na liczbę węzłów zadania uruchamiania|Count|Łącznie|Liczba węzłów oczekujących na zakończenie zadania uruchamiania|Nie wymiarów|
|StartTaskFailedNodeCount|Uruchamianie zadania nie powiodło się liczba węzłów|Count|Łącznie|Liczba węzłów, w których zadanie uruchomieniowe nie powiodło się|Nie wymiarów|
|IdleNodeCount|Liczba węzłów bezczynności|Count|Łącznie|Liczba bezczynnych węzłów|Nie wymiarów|
|OfflineNodeCount|Liczba węzłów w trybie offline|Count|Łącznie|Liczba węzłów offline|Nie wymiarów|
|RebootingNodeCount|Ponowny rozruch liczby węzłów|Count|Łącznie|Liczba ponownych uruchomień węzłów|Nie wymiarów|
|ReimagingNodeCount|Liczba węzłów regraficznych|Count|Łącznie|Liczba węzłów regraficznych|Nie wymiarów|
|RunningNodeCount|Liczba uruchomionych węzłów|Count|Łącznie|Liczba uruchomionych węzłów|Nie wymiarów|
|LeavingPoolNodeCount|Opuszczanie liczby węzłów puli|Count|Łącznie|Liczba węzłów opuszczających pulę|Nie wymiarów|
|UnusableNodeCount|Liczba węzłów, których nie można użyć|Count|Łącznie|Liczba nieużywanych węzłów|Nie wymiarów|
|PreemptedNodeCount|Liczba przeniesiona węzłów|Count|Łącznie|Liczba przeniesiona węzłów|Nie wymiarów|
|TaskStartEvent|Zdarzenia uruchamiania zadania|Count|Łącznie|Łączna liczba rozpoczętych zadań|Nie wymiarów|
|TaskCompleteEvent|Zdarzenia ukończenia zadania|Count|Łącznie|Całkowita liczba ukończonych zadań|Nie wymiarów|
|TaskFailEvent|Zdarzenia błędów zadań|Count|Łącznie|Łączna liczba zadań zakończonych niepowodzeniem w stanie niepowodzenia|Nie wymiarów|
|PoolCreateEvent|Zdarzenia tworzenia puli|Count|Łącznie|Łączna liczba utworzonych pul|Nie wymiarów|
|PoolResizeStartEvent|Zdarzenia rozpoczęcia zmiany rozmiaru puli|Count|Łącznie|Łączna liczba rozpoczętych rozmiarów puli|Nie wymiarów|
|PoolResizeCompleteEvent|Zdarzenia ukończenia zmiany rozmiaru puli|Count|Łącznie|Łączna liczba zakończono zmienionych rozmiarów puli|Nie wymiarów|
|PoolDeleteStartEvent|Zdarzenia uruchamiania usuwania puli|Count|Łącznie|Łączna liczba uruchomionych usunięć pul|Nie wymiarów|
|PoolDeleteCompleteEvent|Zdarzenia ukończenia usuwania puli|Count|Łącznie|Łączna liczba ukończonych usunięć pul|Nie wymiarów|
|JobDeleteCompleteEvent|Zdarzenia ukończenia usuwania zadania|Count|Łącznie|Całkowita liczba pomyślnie usuniętych zadań.|Nie wymiarów|
|JobDeleteStartEvent|Zdarzenia rozpoczęcia usuwania zadania|Count|Łącznie|Łączna liczba zadań, które zostały zażądane do usunięcia.|Nie wymiarów|
|JobDisableCompleteEvent|Zdarzenia ukończenia wyłączania zadania|Count|Łącznie|Łączna liczba zadań, które zostały pomyślnie wyłączone.|Nie wymiarów|
|JobDisableStartEvent|Wyłącz zdarzenia uruchamiania dla zadania|Count|Łącznie|Łączna liczba zadań, które zostały zlecone do wyłączenia.|Nie wymiarów|
|JobStartEvent|Zdarzenia uruchamiania zadania|Count|Łącznie|Łączna liczba zadań, które zostały pomyślnie uruchomione.|Nie wymiarów|
|JobTerminateCompleteEvent|Zdarzenia ukończenia kończenia zadania|Count|Łącznie|Łączna liczba zadań zakończonych pomyślnie.|Nie wymiarów|
|JobTerminateStartEvent|Zdarzenia rozpoczęcia zakończenia zadania|Count|Łącznie|Całkowita liczba zadań, które zostały zażądane do zakończenia.|Nie wymiarów|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|connectedclients|Podłączeni klienci|Count|Maksimum||ShardId|
|totalcommandsprocessed|Łączna liczba operacji|Count|Łącznie||ShardId|
|cachehits|Trafienia w pamięci podręcznej|Count|Łącznie||ShardId|
|cachemisses|Chybienia w pamięci podręcznej|Count|Łącznie||ShardId|
|GetCommands|Pobrania|Count|Łącznie||ShardId|
|SetCommands|Zestawy|Count|Łącznie||ShardId|
|operationsPerSecond|Liczba operacji na sekundę|Count|Maksimum||ShardId|
|evictedkeys|Wykluczone klucze|Count|Łącznie||ShardId|
|totalkeys|Całkowita liczba kluczy|Count|Maksimum||ShardId|
|expiredkeys|Wygasłe klucze|Count|Łącznie||ShardId|
|usedmemory|Użyta pamięć|Bajty|Maksimum||ShardId|
|usedmemorypercentage|Wartość procentowa używanej pamięci|Percent|Maksimum||ShardId|
|usedmemoryRss|Używana pamięć RSS|Bajty|Maksimum||ShardId|
|serverLoad|Obciążenie serwera|Percent|Maksimum||ShardId|
|cacheWrite|Zapis w pamięci podręcznej|BytesPerSecond|Maksimum||ShardId|
|cacheRead|Odczyt pamięci podręcznej|BytesPerSecond|Maksimum||ShardId|
|percentProcessorTime|Procesor CPU|Percent|Maksimum||ShardId|
|cacheLatency|Mikrosekundy opóźnienia pamięci podręcznej (wersja zapoznawcza)|Count|Average||ShardId, Sampletype|
|błędy|Błędy|Count|Maksimum||ShardId, ErrorType|
|connectedclients0|Połączeni klienci (fragmentu 0)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed0|Łączna liczba operacji (fragmentu 0)|Count|Łącznie||Nie wymiarów|
|cachehits0|Trafienia pamięci podręcznej (fragmentu 0)|Count|Łącznie||Nie wymiarów|
|cachemisses0|Chybienia w pamięci podręcznej (fragmentu 0)|Count|Łącznie||Nie wymiarów|
|getcommands0|Pobiera (fragmentu 0)|Count|Łącznie||Nie wymiarów|
|setcommands0|Zestawy (fragmentu 0)|Count|Łącznie||Nie wymiarów|
|operationsPerSecond0|Operacje na sekundę (fragmentu 0)|Count|Maksimum||Nie wymiarów|
|evictedkeys0|Wykluczone klucze (fragmentu 0)|Count|Łącznie||Nie wymiarów|
|totalkeys0|Łączna liczba kluczy (fragmentu 0)|Count|Maksimum||Nie wymiarów|
|expiredkeys0|Wygasłe klucze (fragmentu 0)|Count|Łącznie||Nie wymiarów|
|usedmemory0|Używana pamięć (fragmentu 0)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss0|Używana pamięć RSS (fragmentu 0)|Bajty|Maksimum||Nie wymiarów|
|serverLoad0|Ładowanie serwera (fragmentu 0)|Percent|Maksimum||Nie wymiarów|
|cacheWrite0|Zapis pamięci podręcznej (fragmentu 0)|BytesPerSecond|Maksimum||Nie wymiarów|
|cacheRead0|Odczyt pamięci podręcznej (fragmentu 0)|BytesPerSecond|Maksimum||Nie wymiarów|
|percentProcessorTime0|Procesor CPU (fragmentu 0)|Percent|Maksimum||Nie wymiarów|
|connectedclients1|Połączeni klienci (fragmentu 1)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed1|Łączna liczba operacji (fragmentu 1)|Count|Łącznie||Nie wymiarów|
|cachehits1|Trafienia pamięci podręcznej (fragmentu 1)|Count|Łącznie||Nie wymiarów|
|cachemisses1|Chybienia w pamięci podręcznej (fragmentu 1)|Count|Łącznie||Nie wymiarów|
|getcommands1|Pobiera (fragmentu 1)|Count|Łącznie||Nie wymiarów|
|setcommands1|Zestawy (fragmentu 1)|Count|Łącznie||Nie wymiarów|
|operationsPerSecond1|Operacje na sekundę (fragmentu 1)|Count|Maksimum||Nie wymiarów|
|evictedkeys1|Wykluczone klucze (fragmentu 1)|Count|Łącznie||Nie wymiarów|
|totalkeys1|Łączna liczba kluczy (fragmentu 1)|Count|Maksimum||Nie wymiarów|
|expiredkeys1|Wygasłe klucze (fragmentu 1)|Count|Łącznie||Nie wymiarów|
|usedmemory1|Używana pamięć (fragmentu 1)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss1|Używana pamięć RSS (fragmentu 1)|Bajty|Maksimum||Nie wymiarów|
|serverLoad1|Ładowanie serwera (fragmentu 1)|Percent|Maksimum||Nie wymiarów|
|cacheWrite1|Zapisywanie pamięci podręcznej (fragmentu 1)|BytesPerSecond|Maksimum||Nie wymiarów|
|cacheRead1|Odczyt pamięci podręcznej (fragmentu 1)|BytesPerSecond|Maksimum||Nie wymiarów|
|percentProcessorTime1|Procesor CPU (fragmentu 1)|Percent|Maksimum||Nie wymiarów|
|connectedclients2|Połączeni klienci (fragmentu 2)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed2|Łączna liczba operacji (fragmentu 2)|Count|Łącznie||Nie wymiarów|
|cachehits2|Trafienia pamięci podręcznej (fragmentu 2)|Count|Łącznie||Nie wymiarów|
|cachemisses2|Chybienia w pamięci podręcznej (fragmentu 2)|Count|Łącznie||Nie wymiarów|
|getcommands2|Pobiera (fragmentu 2)|Count|Łącznie||Nie wymiarów|
|setcommands2|Zestawy (fragmentu 2)|Count|Łącznie||Nie wymiarów|
|operationsPerSecond2|Operacje na sekundę (fragmentu 2)|Count|Maksimum||Nie wymiarów|
|evictedkeys2|Wykluczone klucze (fragmentu 2)|Count|Łącznie||Nie wymiarów|
|totalkeys2|Łączna liczba kluczy (fragmentu 2)|Count|Maksimum||Nie wymiarów|
|expiredkeys2|Wygasłe klucze (fragmentu 2)|Count|Łącznie||Nie wymiarów|
|usedmemory2|Używana pamięć (fragmentu 2)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss2|Używana pamięć RSS (fragmentu 2)|Bajty|Maksimum||Nie wymiarów|
|serverLoad2|Ładowanie serwera (fragmentu 2)|Percent|Maksimum||Nie wymiarów|
|cacheWrite2|Zapisywanie pamięci podręcznej (fragmentu 2)|BytesPerSecond|Maksimum||Nie wymiarów|
|cacheRead2|Odczyt pamięci podręcznej (fragmentu 2)|BytesPerSecond|Maksimum||Nie wymiarów|
|percentProcessorTime2|Procesor CPU (fragmentu 2)|Percent|Maksimum||Nie wymiarów|
|connectedclients3|Połączeni klienci (fragmentu 3)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed3|Łączna liczba operacji (fragmentu 3)|Count|Łącznie||Nie wymiarów|
|cachehits3|Trafienia pamięci podręcznej (fragmentu 3)|Count|Łącznie||Nie wymiarów|
|cachemisses3|Chybienia w pamięci podręcznej (fragmentu 3)|Count|Łącznie||Nie wymiarów|
|getcommands3|Pobiera (fragmentu 3)|Count|Łącznie||Nie wymiarów|
|setcommands3|Zestawy (fragmentu 3)|Count|Łącznie||Nie wymiarów|
|operationsPerSecond3|Operacje na sekundę (fragmentu 3)|Count|Maksimum||Nie wymiarów|
|evictedkeys3|Wykluczone klucze (fragmentu 3)|Count|Łącznie||Nie wymiarów|
|totalkeys3|Łączna liczba kluczy (fragmentu 3)|Count|Maksimum||Nie wymiarów|
|expiredkeys3|Wygasłe klucze (fragmentu 3)|Count|Łącznie||Nie wymiarów|
|usedmemory3|Używana pamięć (fragmentu 3)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss3|Używana pamięć RSS (fragmentu 3)|Bajty|Maksimum||Nie wymiarów|
|serverLoad3|Ładowanie serwera (fragmentu 3)|Percent|Maksimum||Nie wymiarów|
|cacheWrite3|Zapis w pamięci podręcznej (fragmentu 3)|BytesPerSecond|Maksimum||Nie wymiarów|
|cacheRead3|Odczyt pamięci podręcznej (fragmentu 3)|BytesPerSecond|Maksimum||Nie wymiarów|
|percentProcessorTime3|Procesor CPU (fragmentu 3)|Percent|Maksimum||Nie wymiarów|
|connectedclients4|Połączeni klienci (fragmentu 4)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed4|Łączna liczba operacji (fragmentu 4)|Count|Łącznie||Nie wymiarów|
|cachehits4|Trafienia pamięci podręcznej (fragmentu 4)|Count|Łącznie||Nie wymiarów|
|cachemisses4|Chybienia w pamięci podręcznej (fragmentu 4)|Count|Łącznie||Nie wymiarów|
|getcommands4|Pobiera (fragmentu 4)|Count|Łącznie||Nie wymiarów|
|setcommands4|Zestawy (fragmentu 4)|Count|Łącznie||Nie wymiarów|
|operationsPerSecond4|Operacje na sekundę (fragmentu 4)|Count|Maksimum||Nie wymiarów|
|evictedkeys4|Wykluczone klucze (fragmentu 4)|Count|Łącznie||Nie wymiarów|
|totalkeys4|Łączna liczba kluczy (fragmentu 4)|Count|Maksimum||Nie wymiarów|
|expiredkeys4|Wygasłe klucze (fragmentu 4)|Count|Łącznie||Nie wymiarów|
|usedmemory4|Używana pamięć (fragmentu 4)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss4|Używana pamięć RSS (fragmentu 4)|Bajty|Maksimum||Nie wymiarów|
|serverLoad4|Ładowanie serwera (fragmentu 4)|Percent|Maksimum||Nie wymiarów|
|cacheWrite4|Zapis pamięci podręcznej (fragmentu 4)|BytesPerSecond|Maksimum||Nie wymiarów|
|cacheRead4|Odczyt pamięci podręcznej (fragmentu 4)|BytesPerSecond|Maksimum||Nie wymiarów|
|percentProcessorTime4|Procesor CPU (fragmentu 4)|Percent|Maksimum||Nie wymiarów|
|connectedclients5|Połączeni klienci (fragmentu 5)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed5|Łączna liczba operacji (fragmentu 5)|Count|Łącznie||Nie wymiarów|
|cachehits5|Trafienia pamięci podręcznej (fragmentu 5)|Count|Łącznie||Nie wymiarów|
|cachemisses5|Chybienia w pamięci podręcznej (fragmentu 5)|Count|Łącznie||Nie wymiarów|
|getcommands5|Pobiera (fragmentu 5)|Count|Łącznie||Nie wymiarów|
|setcommands5|Zestawy (fragmentu 5)|Count|Łącznie||Nie wymiarów|
|operationsPerSecond5|Operacje na sekundę (fragmentu 5)|Count|Maksimum||Nie wymiarów|
|evictedkeys5|Wykluczone klucze (fragmentu 5)|Count|Łącznie||Nie wymiarów|
|totalkeys5|Łączna liczba kluczy (fragmentu 5)|Count|Maksimum||Nie wymiarów|
|expiredkeys5|Wygasłe klucze (fragmentu 5)|Count|Łącznie||Nie wymiarów|
|usedmemory5|Używana pamięć (fragmentu 5)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss5|Używana pamięć RSS (fragmentu 5)|Bajty|Maksimum||Nie wymiarów|
|serverLoad5|Ładowanie serwera (fragmentu 5)|Percent|Maksimum||Nie wymiarów|
|cacheWrite5|Zapis w pamięci podręcznej (fragmentu 5)|BytesPerSecond|Maksimum||Nie wymiarów|
|cacheRead5|Odczyt pamięci podręcznej (fragmentu 5)|BytesPerSecond|Maksimum||Nie wymiarów|
|percentProcessorTime5|Procesor CPU (fragmentu 5)|Percent|Maksimum||Nie wymiarów|
|connectedclients6|Połączeni klienci (fragmentu 6)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed6|Łączna liczba operacji (fragmentu 6)|Count|Łącznie||Nie wymiarów|
|cachehits6|Trafienia pamięci podręcznej (fragmentu 6)|Count|Łącznie||Nie wymiarów|
|cachemisses6|Chybienia w pamięci podręcznej (fragmentu 6)|Count|Łącznie||Nie wymiarów|
|getcommands6|Pobiera (fragmentu 6)|Count|Łącznie||Nie wymiarów|
|setcommands6|Zestawy (fragmentu 6)|Count|Łącznie||Nie wymiarów|
|operationsPerSecond6|Operacje na sekundę (fragmentu 6)|Count|Maksimum||Nie wymiarów|
|evictedkeys6|Wykluczone klucze (fragmentu 6)|Count|Łącznie||Nie wymiarów|
|totalkeys6|Łączna liczba kluczy (fragmentu 6)|Count|Maksimum||Nie wymiarów|
|expiredkeys6|Wygasłe klucze (fragmentu 6)|Count|Łącznie||Nie wymiarów|
|usedmemory6|Używana pamięć (fragmentu 6)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss6|Używana pamięć RSS (fragmentu 6)|Bajty|Maksimum||Nie wymiarów|
|serverLoad6|Ładowanie serwera (fragmentu 6)|Percent|Maksimum||Nie wymiarów|
|cacheWrite6|Zapis w pamięci podręcznej (fragmentu 6)|BytesPerSecond|Maksimum||Nie wymiarów|
|cacheRead6|Odczyt pamięci podręcznej (fragmentu 6)|BytesPerSecond|Maksimum||Nie wymiarów|
|percentProcessorTime6|Procesor CPU (fragmentu 6)|Percent|Maksimum||Nie wymiarów|
|connectedclients7|Połączeni klienci (fragmentu 7)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed7|Łączna liczba operacji (fragmentu 7)|Count|Łącznie||Nie wymiarów|
|cachehits7|Trafienia pamięci podręcznej (fragmentu 7)|Count|Łącznie||Nie wymiarów|
|cachemisses7|Chybienia w pamięci podręcznej (fragmentu 7)|Count|Łącznie||Nie wymiarów|
|getcommands7|Pobiera (fragmentu 7)|Count|Łącznie||Nie wymiarów|
|setcommands7|Zestawy (fragmentu 7)|Count|Łącznie||Nie wymiarów|
|operationsPerSecond7|Operacje na sekundę (fragmentu 7)|Count|Maksimum||Nie wymiarów|
|evictedkeys7|Wykluczone klucze (fragmentu 7)|Count|Łącznie||Nie wymiarów|
|totalkeys7|Łączna liczba kluczy (fragmentu 7)|Count|Maksimum||Nie wymiarów|
|expiredkeys7|Wygasłe klucze (fragmentu 7)|Count|Łącznie||Nie wymiarów|
|usedmemory7|Używana pamięć (fragmentu 7)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss7|Używana pamięć RSS (fragmentu 7)|Bajty|Maksimum||Nie wymiarów|
|serverLoad7|Ładowanie serwera (fragmentu 7)|Percent|Maksimum||Nie wymiarów|
|cacheWrite7|Zapis w pamięci podręcznej (fragmentu 7)|BytesPerSecond|Maksimum||Nie wymiarów|
|cacheRead7|Odczyt pamięci podręcznej (fragmentu 7)|BytesPerSecond|Maksimum||Nie wymiarów|
|percentProcessorTime7|Procesor CPU (fragmentu 7)|Percent|Maksimum||Nie wymiarów|
|connectedclients8|Połączeni klienci (fragmentu 8)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed8|Łączna liczba operacji (fragmentu 8)|Count|Łącznie||Nie wymiarów|
|cachehits8|Trafienia pamięci podręcznej (fragmentu 8)|Count|Łącznie||Nie wymiarów|
|cachemisses8|Chybienia w pamięci podręcznej (fragmentu 8)|Count|Łącznie||Nie wymiarów|
|getcommands8|Pobiera (fragmentu 8)|Count|Łącznie||Nie wymiarów|
|setcommands8|Zestawy (fragmentu 8)|Count|Łącznie||Nie wymiarów|
|operationsPerSecond8|Operacje na sekundę (fragmentu 8)|Count|Maksimum||Nie wymiarów|
|evictedkeys8|Wykluczone klucze (fragmentu 8)|Count|Łącznie||Nie wymiarów|
|totalkeys8|Łączna liczba kluczy (fragmentu 8)|Count|Maksimum||Nie wymiarów|
|expiredkeys8|Wygasłe klucze (fragmentu 8)|Count|Łącznie||Nie wymiarów|
|usedmemory8|Używana pamięć (fragmentu 8)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss8|Używana pamięć RSS (fragmentu 8)|Bajty|Maksimum||Nie wymiarów|
|serverLoad8|Ładowanie serwera (fragmentu 8)|Percent|Maksimum||Nie wymiarów|
|cacheWrite8|Zapis w pamięci podręcznej (fragmentu 8)|BytesPerSecond|Maksimum||Nie wymiarów|
|cacheRead8|Odczyt pamięci podręcznej (fragmentu 8)|BytesPerSecond|Maksimum||Nie wymiarów|
|percentProcessorTime8|Procesor CPU (fragmentu 8)|Percent|Maksimum||Nie wymiarów|
|connectedclients9|Połączeni klienci (fragmentu 9)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed9|Łączna liczba operacji (fragmentu 9)|Count|Łącznie||Nie wymiarów|
|cachehits9|Trafienia pamięci podręcznej (fragmentu 9)|Count|Łącznie||Nie wymiarów|
|cachemisses9|Chybienia w pamięci podręcznej (fragmentu 9)|Count|Łącznie||Nie wymiarów|
|getcommands9|Pobiera (fragmentu 9)|Count|Łącznie||Nie wymiarów|
|setcommands9|Zestawy (fragmentu 9)|Count|Łącznie||Nie wymiarów|
|operationsPerSecond9|Operacje na sekundę (fragmentu 9)|Count|Maksimum||Nie wymiarów|
|evictedkeys9|Wykluczone klucze (fragmentu 9)|Count|Łącznie||Nie wymiarów|
|totalkeys9|Łączna liczba kluczy (fragmentu 9)|Count|Maksimum||Nie wymiarów|
|expiredkeys9|Wygasłe klucze (fragmentu 9)|Count|Łącznie||Nie wymiarów|
|usedmemory9|Używana pamięć (fragmentu 9)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss9|Używana pamięć RSS (fragmentu 9)|Bajty|Maksimum||Nie wymiarów|
|serverLoad9|Ładowanie serwera (fragmentu 9)|Percent|Maksimum||Nie wymiarów|
|cacheWrite9|Zapis w pamięci podręcznej (fragmentu 9)|BytesPerSecond|Maksimum||Nie wymiarów|
|cacheRead9|Odczyt pamięci podręcznej (fragmentu 9)|BytesPerSecond|Maksimum||Nie wymiarów|
|percentProcessorTime9|Procesor CPU (fragmentu 9)|Percent|Maksimum||Nie wymiarów|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Average|Wartość procentowa przyznanych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne.|Nie wymiarów|
|Sieć — wejście|Sieć — wejście|Bajty|Łącznie|Liczba bajtów odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący).|Nie wymiarów|
|Sieć — wyjście|Sieć — wyjście|Bajty|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący).|Nie wymiarów|
|Bajty odczytu dysku/s|Odczyt dysku|BytesPerSecond|Average|Średnia liczba bajtów odczytanych z dysku w okresie monitorowania.|Nie wymiarów|
|Bajty zapisu dysku/s|Zapis dysku|BytesPerSecond|Average|Średnia liczba bajtów zapisywana na dysku w okresie monitorowania.|Nie wymiarów|
|Operacje odczytu z dysku/s|Operacje odczytu z dysku/s|CountPerSecond|Average|Operacje we/wy odczytu na dysku.|Nie wymiarów|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku/s|CountPerSecond|Average|Liczba operacji we/wy zapisu na dysku.|Nie wymiarów|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Average|Wartość procentowa przyznanych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne.|RoleInstanceId|
|Sieć — wejście|Sieć — wejście|Bajty|Łącznie|Liczba bajtów odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący).|RoleInstanceId|
|Sieć — wyjście|Sieć — wyjście|Bajty|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący).|RoleInstanceId|
|Bajty odczytu dysku/s|Odczyt dysku|BytesPerSecond|Average|Średnia liczba bajtów odczytanych z dysku w okresie monitorowania.|RoleInstanceId|
|Bajty zapisu dysku/s|Zapis dysku|BytesPerSecond|Average|Średnia liczba bajtów zapisywana na dysku w okresie monitorowania.|RoleInstanceId|
|Operacje odczytu z dysku/s|Operacje odczytu z dysku/s|CountPerSecond|Average|Operacje we/wy odczytu na dysku.|RoleInstanceId|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku/s|CountPerSecond|Average|Liczba operacji we/wy zapisu na dysku.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalCalls|Łączna liczba wywołań|Count|Łącznie|Łączna liczba wywołań.|ApiName, OperationName, region|
|SuccessfulCalls|Wywołania zakończone powodzeniem|Count|Łącznie|Liczba pomyślnych wywołań.|ApiName, OperationName, region|
|TotalErrors|Łączna liczba błędów|Count|Łącznie|Łączna liczba wywołań z odpowiedzią na błąd (kod odpowiedzi HTTP 4xx lub 5xx).|ApiName, OperationName, region|
|BlockedCalls|Zablokowane wywołania|Count|Łącznie|Liczba wywołań, które przekroczyły limit szybkości lub limitu przydziału.|ApiName, OperationName, region|
|Błędy servererrors|Błędy serwera|Count|Łącznie|Liczba wywołań z błędem wewnętrznym usługi (5xx kodu odpowiedzi HTTP).|ApiName, OperationName, region|
|ClientErrors|Błędy klienta|Count|Łącznie|Liczba wywołań z błędem po stronie klienta (4xx kodu odpowiedzi HTTP).|ApiName, OperationName, region|
|Dane|Dane wejściowe|Bajty|Łącznie|Rozmiar danych przychodzących w bajtach.|ApiName, OperationName, region|
|DataOut|Dane wyjściowe|Bajty|Łącznie|Rozmiar danych wychodzących w bajtach.|ApiName, OperationName, region|
|Opóźnienie|Opóźnienie|MilliSeconds|Average|Opóźnienie w milisekundach.|ApiName, OperationName, region|
|CharactersTranslated|Przetłumaczone znaki|Count|Łącznie|Całkowita liczba znaków w żądaniu tekstu przychodzącego.|ApiName, OperationName, region|
|CharactersTrained|Znaki przeszkolone|Count|Łącznie|Łączna liczba znaków przeszkolonych.|ApiName, OperationName, region|
|SpeechSessionDuration|Czas trwania sesji mowy|Sekundy|Łącznie|Łączny czas trwania sesji mowy (w sekundach).|ApiName, OperationName, region|
|TotalTransactions|Łączna liczba transakcji|Count|Łącznie|Łączna liczba transakcji.|Nie wymiarów|
|TotalTokenCalls|Łączna liczba wywołań tokenów|Count|Łącznie|Łączna liczba wywołań tokenów.|ApiName, OperationName, region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Średnia|Procent przydzielonych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne|Nie wymiarów|
|Sieć — wejście|Ruch przychodzący do sieci (płatny)|Bajty|Łącznie|Liczba bajtów rozliczanych odebranych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący)|Nie wymiarów|
|Sieć — wyjście|Ruch wychodzący z sieci (płatny)|Bajty|Łącznie|Liczba bajtów rozliczanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący)|Nie wymiarów|
|Bajty odczytane z dysku|Bajty odczytane z dysku|Bajty|Łącznie|Bajty odczytane z dysku w okresie monitorowania|Nie wymiarów|
|Bajty zapisane na dysku|Bajty zapisane na dysku|Bajty|Łącznie|Bajty zapisywane na dysku w okresie monitorowania|Nie wymiarów|
|Operacje odczytu z dysku/s|Operacje odczytu z dysku/s|CountPerSecond|Average|Operacje we/wy odczytu dysku|Nie wymiarów|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku/s|CountPerSecond|Average|Operacje we/wy zapisu dysku|Nie wymiarów|
|Pozostałe środki na procesory CPU|Pozostałe środki na procesory CPU|Count|Average|Łączna liczba kredytów dostępnych dla serii|Nie wymiarów|
|Wykorzystane środki na procesory CPU|Wykorzystane środki na procesory CPU|Count|Average|Łączna liczba kredytów zużytych przez maszynę wirtualną|Nie wymiarów|
|Bajty odczytu na dysk/s|Bajty odczytu dysku danych/s (przestarzałe)|CountPerSecond|Average|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|SlotId|
|Bajty zapisu na dysku/s|Bajty zapisu na dysku danych/s (przestarzałe)|CountPerSecond|Average|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|SlotId|
|Operacje odczytu na dysk/s|Operacje odczytu z dysku danych/s (przestarzałe)|CountPerSecond|Average|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku danych/s (przestarzałe)|CountPerSecond|Average|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Głębokość kolejki dysku|Głębokość kolejki dysku danych (przestarzałe)|Count|Average|Głębokość kolejki dysku danych (lub długość kolejki)|SlotId|
|Bajty odczytu dysku systemu operacyjnego/s|Bajty odczytu z dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Average|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Bajty zapisu na dysku systemu operacyjnego/s|Bajty zapisu dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Average|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Operacje odczytu na dysku systemu operacyjnego/s|Operacje odczytu z dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Average|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Operacje zapisu na dysku systemu operacyjnego/s|Operacje zapisu na dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Average|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Głębokość kolejki dysku systemu operacyjnego|Głębokość kolejki dysku systemu operacyjnego (przestarzałe)|Count|Average|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|Nie wymiarów|
|Bajty odczytu dysku danych/s|Bajty odczytu dysku danych/s (wersja zapoznawcza)|CountPerSecond|Average|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|JEDNOSTKA LUN|
|Bajty zapisu na dysku danych/s|Bajty zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Average|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|JEDNOSTKA LUN|
|Operacje odczytu z dysku danych/s|Operacje odczytu z dysku danych/s (wersja zapoznawcza)|CountPerSecond|Average|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|JEDNOSTKA LUN|
|Operacje zapisu na dysku danych/s|Operacje zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Average|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|JEDNOSTKA LUN|
|Głębokość kolejki dysku danych|Głębokość kolejki dysku danych (wersja zapoznawcza)|Count|Average|Głębokość kolejki dysku danych (lub długość kolejki)|JEDNOSTKA LUN|
|Bajty odczytu dysku systemu operacyjnego/s|Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Average|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Bajty zapisu dysku systemu operacyjnego/s|Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Average|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Operacje odczytu z dysku systemu operacyjnego/s|Operacje odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Average|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Operacje zapisu na dysku systemu operacyjnego/s|Operacje zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Average|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Głębokość kolejki dysku systemu operacyjnego|Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza)|Count|Average|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|Nie wymiarów|
|Przepływy przychodzące|Przepływy przychodzące (wersja zapoznawcza)|Count|Average|Przepływy przychodzące to liczba bieżących przepływów w kierunku przychodzącym (ruch przychodzący do maszyny wirtualnej)|Nie wymiarów|
|Przepływy wychodzące|Przepływy wychodzące (wersja zapoznawcza)|Count|Average|Przepływy wychodzące to liczba bieżących przepływów w kierunku ruchu wychodzącego (ruch wychodzący z maszyny wirtualnej)|Nie wymiarów|
|Maksymalna szybkość tworzenia przepływów przychodzących|Maksymalny współczynnik tworzenia przepływów przychodzących (wersja zapoznawcza)|CountPerSecond|Average|Maksymalna szybkość tworzenia przepływów przychodzących (ruch przychodzący do maszyny wirtualnej)|Nie wymiarów|
|Maksymalna szybkość tworzenia przepływów wychodzących|Maksymalny współczynnik tworzenia przepływów wychodzących (wersja zapoznawcza)|CountPerSecond|Average|Maksymalna szybkość tworzenia przepływów wychodzących (ruch wychodzący z maszyny wirtualnej)|Nie wymiarów|
|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|Trafienie odczytu pamięci podręcznej dysku danych Premium (wersja zapoznawcza)|Percent|Average|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|JEDNOSTKA LUN|
|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|Chybienia odczytu pamięci podręcznej dysku z danymi Premium (wersja zapoznawcza)|Percent|Average|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|JEDNOSTKA LUN|
|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Percent|Average|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|Nie wymiarów|
|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|Brak chybień w pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Percent|Average|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|Nie wymiarów|
|Ruch przychodzący do sieci (łącznie)|Ruch przychodzący do sieci (łącznie)|Bajty|Łącznie|Liczba bajtów odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący)|Nie wymiarów|
|Ruch wychodzący z sieci (łącznie)|Ruch wychodzący z sieci (łącznie)|Bajty|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący)|Nie wymiarów|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Średnia|Procent przydzielonych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne|VMName|
|Sieć — wejście|Ruch przychodzący do sieci (płatny)|Bajty|Łącznie|Liczba bajtów rozliczanych odebranych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący)|VMName|
|Sieć — wyjście|Ruch wychodzący z sieci (płatny)|Bajty|Łącznie|Liczba bajtów rozliczanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący)|VMName|
|Bajty odczytane z dysku|Bajty odczytane z dysku|Bajty|Łącznie|Bajty odczytane z dysku w okresie monitorowania|VMName|
|Bajty zapisane na dysku|Bajty zapisane na dysku|Bajty|Łącznie|Bajty zapisywane na dysku w okresie monitorowania|VMName|
|Operacje odczytu z dysku/s|Operacje odczytu z dysku/s|CountPerSecond|Average|Operacje we/wy odczytu dysku|VMName|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku/s|CountPerSecond|Average|Operacje we/wy zapisu dysku|VMName|
|Pozostałe środki na procesory CPU|Pozostałe środki na procesory CPU|Count|Average|Łączna liczba kredytów dostępnych dla serii|Nie wymiarów|
|Wykorzystane środki na procesory CPU|Wykorzystane środki na procesory CPU|Count|Average|Łączna liczba kredytów zużytych przez maszynę wirtualną|Nie wymiarów|
|Bajty odczytu na dysk/s|Bajty odczytu dysku danych/s (przestarzałe)|CountPerSecond|Average|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|SlotId|
|Bajty zapisu na dysku/s|Bajty zapisu na dysku danych/s (przestarzałe)|CountPerSecond|Average|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|SlotId|
|Operacje odczytu na dysk/s|Operacje odczytu z dysku danych/s (przestarzałe)|CountPerSecond|Average|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku danych/s (przestarzałe)|CountPerSecond|Average|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Głębokość kolejki dysku|Głębokość kolejki dysku danych (przestarzałe)|Count|Average|Głębokość kolejki dysku danych (lub długość kolejki)|SlotId|
|Bajty odczytu dysku systemu operacyjnego/s|Bajty odczytu z dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Average|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Bajty zapisu na dysku systemu operacyjnego/s|Bajty zapisu dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Average|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Operacje odczytu na dysku systemu operacyjnego/s|Operacje odczytu z dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Average|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Operacje zapisu na dysku systemu operacyjnego/s|Operacje zapisu na dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Average|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Głębokość kolejki dysku systemu operacyjnego|Głębokość kolejki dysku systemu operacyjnego (przestarzałe)|Count|Average|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|Nie wymiarów|
|Bajty odczytu dysku danych/s|Bajty odczytu dysku danych/s (wersja zapoznawcza)|CountPerSecond|Average|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|Jednostka LUN, VMName|
|Bajty zapisu na dysku danych/s|Bajty zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Average|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|Jednostka LUN, VMName|
|Operacje odczytu z dysku danych/s|Operacje odczytu z dysku danych/s (wersja zapoznawcza)|CountPerSecond|Average|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|Jednostka LUN, VMName|
|Operacje zapisu na dysku danych/s|Operacje zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Average|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|Jednostka LUN, VMName|
|Głębokość kolejki dysku danych|Głębokość kolejki dysku danych (wersja zapoznawcza)|Count|Average|Głębokość kolejki dysku danych (lub długość kolejki)|Jednostka LUN, VMName|
|Bajty odczytu dysku systemu operacyjnego/s|Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Average|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Bajty zapisu dysku systemu operacyjnego/s|Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Average|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Operacje odczytu z dysku systemu operacyjnego/s|Operacje odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Average|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Operacje zapisu na dysku systemu operacyjnego/s|Operacje zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Average|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Głębokość kolejki dysku systemu operacyjnego|Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza)|Count|Average|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|VMName|
|Przepływy przychodzące|Przepływy przychodzące (wersja zapoznawcza)|Count|Average|Przepływy przychodzące to liczba bieżących przepływów w kierunku przychodzącym (ruch przychodzący do maszyny wirtualnej)|VMName|
|Przepływy wychodzące|Przepływy wychodzące (wersja zapoznawcza)|Count|Average|Przepływy wychodzące to liczba bieżących przepływów w kierunku ruchu wychodzącego (ruch wychodzący z maszyny wirtualnej)|VMName|
|Maksymalna szybkość tworzenia przepływów przychodzących|Maksymalny współczynnik tworzenia przepływów przychodzących (wersja zapoznawcza)|CountPerSecond|Average|Maksymalna szybkość tworzenia przepływów przychodzących (ruch przychodzący do maszyny wirtualnej)|VMName|
|Maksymalna szybkość tworzenia przepływów wychodzących|Maksymalny współczynnik tworzenia przepływów wychodzących (wersja zapoznawcza)|CountPerSecond|Average|Maksymalna szybkość tworzenia przepływów wychodzących (ruch wychodzący z maszyny wirtualnej)|VMName|
|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|Trafienie odczytu pamięci podręcznej dysku danych Premium (wersja zapoznawcza)|Percent|Average|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|Jednostka LUN, VMName|
|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|Chybienia odczytu pamięci podręcznej dysku z danymi Premium (wersja zapoznawcza)|Percent|Average|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|Jednostka LUN, VMName|
|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Percent|Average|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|VMName|
|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|Brak chybień w pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Percent|Average|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|VMName|
|Ruch przychodzący do sieci (łącznie)|Ruch przychodzący do sieci (łącznie)|Bajty|Łącznie|Liczba bajtów odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący)|VMName|
|Ruch wychodzący z sieci (łącznie)|Ruch wychodzący z sieci (łącznie)|Bajty|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuUsage|Użycie procesora CPU|Count|Average|Użycie procesora CPU we wszystkich rdzeniach w millicores.|containerName|
|MemoryUsage|Użycie pamięci|Bajty|Average|Całkowite użycie pamięci w bajcie.|containerName|
|NetworkBytesReceivedPerSecond|Liczba bajtów odebranych przez sieć na sekundę|Bajty|Average|Bajty odebrane przez sieć na sekundę.|Nie wymiarów|
|NetworkBytesTransmittedPerSecond|Liczba bajtów przesłanych przez sieć na sekundę|Bajty|Average|Bajty przesyłane przez sieć na sekundę.|Nie wymiarów|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalPullCount|Łączna liczba ściągania|Count|Average|Całkowita liczba ściągania obrazów|Nie wymiarów|
|SuccessfulPullCount|Pomyślna liczba ściągania|Count|Average|Liczba pomyślnych operacji ściągania obrazu|Nie wymiarów|
|TotalPushCount|Łączna liczba wypychanych|Count|Average|Liczba wypchnięciów obrazów łącznie|Nie wymiarów|
|SuccessfulPushCount|Liczba wypychanych zakończonych powodzeniem|Count|Average|Liczba pomyślnych operacji wypychania obrazu|Nie wymiarów|
|RunDuration|Czas trwania przebiegu|MS|Łącznie|Czas trwania przebiegu w milisekundach|Nie wymiarów|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Łączna liczba dostępnych rdzeni procesora CPU w zarządzanym klastrze|Count|Łącznie|Łączna liczba dostępnych rdzeni procesora CPU w zarządzanym klastrze|Nie wymiarów|
|kube_node_status_allocatable_memory_bytes|Łączna ilość dostępnej pamięci w zarządzanym klastrze|Bajty|Łącznie|Łączna ilość dostępnej pamięci w zarządzanym klastrze|Nie wymiarów|
|kube_pod_status_ready|Liczba zasobników w stanie gotowe|Count|Łącznie|Liczba zasobników w stanie gotowe|Przestrzeń nazw, pod|
|kube_node_status_condition|Stany różnych warunków węzła|Count|Łącznie|Stany różnych warunków węzła|warunek, stan, status2, węzeł|
|kube_pod_status_phase|Liczba etapów według fazy|Count|Łącznie|Liczba etapów według fazy|faza, przestrzeń nazw, pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|DCIApiCalls|Wywołania interfejsu API Customer Insights|Count|Łącznie||Nie wymiarów|
|DCIMappingImportOperationSuccessfulLines|Pomyślnie zakończono mapowanie wierszy operacji importowania|Count|Łącznie||Nie wymiarów|
|DCIMappingImportOperationFailedLines|Niepowodzenie operacji importowania mapowania wierszy|Count|Łącznie||Nie wymiarów|
|DCIMappingImportOperationTotalLines|Łączna liczba wierszy operacji importowania mapowania|Count|Łącznie||Nie wymiarów|
|DCIMappingImportOperationRuntimeInSeconds|Mapowanie czasu wykonywania operacji importowania w ciągu kilku sekund|Sekundy|Łącznie||Nie wymiarów|
|DCIOutboundProfileExportSucceeded|Eksportowanie profilu wychodzącego zakończyło się pomyślnie|Count|Łącznie||Nie wymiarów|
|DCIOutboundProfileExportFailed|Eksport profilu wychodzącego nie powiódł się|Count|Łącznie||Nie wymiarów|
|DCIOutboundProfileExportDuration|Czas trwania eksportu profilu wychodzącego|Sekundy|Łącznie||Nie wymiarów|
|DCIOutboundKpiExportSucceeded|Eksport wychodzącego wskaźnika KPI zakończył się pomyślnie|Count|Łącznie||Nie wymiarów|
|DCIOutboundKpiExportFailed|Eksport wychodzącego wskaźnika KPI nie powiódł się|Count|Łącznie||Nie wymiarów|
|DCIOutboundKpiExportDuration|Czas trwania eksportu KPI wychodzącego|Sekundy|Łącznie||Nie wymiarów|
|DCIOutboundKpiExportStarted|Rozpoczęto eksport wychodzącego wskaźnika KPI|Sekundy|Łącznie||Nie wymiarów|
|DCIOutboundKpiRecordCount|Liczba rekordów wychodzącego wskaźnika KPI|Sekundy|Łącznie||Nie wymiarów|
|DCIOutboundProfileExportCount|Liczba eksportu profilów wychodzących|Sekundy|Łącznie||Nie wymiarów|
|DCIOutboundInitialProfileExportFailed|Wychodzące Eksportowanie profilu początkowego nie powiodło się|Sekundy|Łącznie||Nie wymiarów|
|DCIOutboundInitialProfileExportSucceeded|Eksportowanie wyjściowego profilu początkowego zakończyło się pomyślnie|Sekundy|Łącznie||Nie wymiarów|
|DCIOutboundInitialKpiExportFailed|Nie można wyeksportować wyjściowego kluczowego wskaźnika KPI|Sekundy|Łącznie||Nie wymiarów|
|DCIOutboundInitialKpiExportSucceeded|Wychodzące eksportowanie początkowego wskaźnika KPI powiodło się|Sekundy|Łącznie||Nie wymiarów|
|DCIOutboundInitialProfileExportDurationInSeconds|Czas trwania eksportu profilu początkowego w sekundach|Sekundy|Łącznie||Nie wymiarów|
|AdlaJobForStandardKpiFailed|Niepowodzenie zadania adla dla standardowego wskaźnika KPI w sekundach|Sekundy|Łącznie||Nie wymiarów|
|AdlaJobForStandardKpiTimeOut|Adla zadanie dla standardowego limitu czasu KPI w sekundach|Sekundy|Łącznie||Nie wymiarów|
|AdlaJobForStandardKpiCompleted|Zadanie adla dla standardowego wskaźnika KPI zostało ukończone w sekundach|Sekundy|Łącznie||Nie wymiarów|
|ImportASAValuesFailed|Liczba nieudanych importu wartości ASA|Count|Łącznie||Nie wymiarów|
|ImportASAValuesSucceeded|Liczba pomyślnie zaimportowanych wartości ASA|Count|Łącznie||Nie wymiarów|
|DCIProfilesCount|Liczba wystąpień profilu|Count|Ostatnia||Nie wymiarów|
|DCIInteractionsPerMonthCount|Liczba interakcji na miesiąc|Count|Ostatnia||Nie wymiarów|
|DCIKpisCount|Liczba wskaźników KPI|Count|Ostatnia||Nie wymiarów|
|DCISegmentsCount|Liczba segmentów|Count|Ostatnia||Nie wymiarów|
|DCIPredictiveMatchPoliciesCount|Liczba dopasowań predykcyjnych|Count|Ostatnia||Nie wymiarów|
|DCIPredictionsCount|Liczba prognoz|Count|Ostatnia||Nie wymiarów|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|NICReadThroughput|Przepływność odczytu (Sieć)|BytesPerSecond|Average|Przepływność odczytu interfejsu sieciowego na urządzeniu w okresie sprawozdawczym dla wszystkich woluminów w bramie.|InstanceName|
|NICWriteThroughput|Przepływność zapisu (Sieć)|BytesPerSecond|Average|Przepływność zapisu interfejsu sieciowego na urządzeniu w okresie sprawozdawczym dla wszystkich woluminów w bramie.|InstanceName|
|CloudReadThroughputPerShare|Przepływność pobierania w chmurze (udział)|BytesPerSecond|Average|Przepływność pobierania na platformę Azure z udziału w okresie raportowania.|Udostępnij|
|CloudUploadThroughputPerShare|Przepływność przekazywania w chmurze (udział)|BytesPerSecond|Average|Przepływność przekazywania do platformy Azure z udziału w okresie raportowania.|Udostępnij|
|BytesUploadedToCloudPerShare|Przekazane bajty w chmurze (udział)|Bajty|Average|Całkowita liczba bajtów przekazanych do platformy Azure z udziału w okresie raportowania.|Udostępnij|
|Łączna pojemność|Całkowita pojemność|Bajty|Average|Całkowita pojemność|Nie wymiarów|
|AvailableCapacity|Dostępna pojemność|Bajty|Average|Dostępna pojemność w bajtach w okresie raportowania.|Nie wymiarów|
|CloudUploadThroughput|Przepływność przekazywania w chmurze|BytesPerSecond|Average|W chmurze przekazano przepływność na platformę Azure w okresie raportowania.|Nie wymiarów|
|CloudReadThroughput|Przepływność pobierania w chmurze|BytesPerSecond|Average|Chmura pobiera przepływność na platformę Azure w okresie raportowania.|Nie wymiarów|
|BytesUploadedToCloud|Przekazane bajty w chmurze (urządzenie)|Bajty|Average|Całkowita liczba bajtów przekazana na platformę Azure z urządzenia w okresie raportowania.|Nie wymiarów|
|HyperVVirtualProcessorUtilization|Obliczenia brzegowe — procentowy procesor CPU|Percent|Average|Procent użycia procesora CPU|InstanceName|
|HyperVMemoryUtilization|Obliczenia brzegowe — użycie pamięci|Percent|Average|Ilość pamięci RAM w użyciu|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|FailedRuns|Nieudane uruchomienia|Count|Łącznie||potokname, ActivityName|
|SuccessfulRuns|Pomyślne uruchomienia|Count|Łącznie||potokname, ActivityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PipelineFailedRuns|Metryki uruchomionych potoków zakończonych niepowodzeniem|Count|Łącznie||FailureType, nazwa|
|PipelineSucceededRuns|Metryki uruchamiania potoków zakończonych powodzeniem|Count|Łącznie||FailureType, nazwa|
|ActivityFailedRuns|Metryki uruchamiania działań zakończonych niepowodzeniem|Count|Łącznie||ActivityType, Potokname, FailureType, nazwa|
|ActivitySucceededRuns|Metryki uruchamiania działań zakończonych powodzeniem|Count|Łącznie||ActivityType, Potokname, FailureType, nazwa|
|TriggerFailedRuns|Metryki uruchomienia wyzwalacza zakończonego niepowodzeniem|Count|Łącznie||Nazwa, Niepowodzenie|
|TriggerSucceededRuns|Wyzwalacze uruchomienia wyzwalają metryki|Count|Łącznie||Nazwa, Niepowodzenie|
|IntegrationRuntimeCpuPercentage|Użycie procesora Integration Runtime|Percent|Average||IntegrationRuntimeName, nodename|
|IntegrationRuntimeAvailableMemory|Dostępna pamięć środowiska Integration Runtime|Bajty|Average||IntegrationRuntimeName, nodename|
|MaxAllowedResourceCount|Maksymalna dozwolona liczba jednostek|Count|Maksimum||Nie wymiarów|
|MaxAllowedFactorySizeInGbUnits|Maksymalny dozwolony rozmiar fabryki (jednostka GB)|Count|Maksimum||Nie wymiarów|
|ResourceCount|Łączna liczba jednostek|Count|Maksimum||Nie wymiarów|
|FactorySizeInGbUnits|Łączny rozmiar fabryki (jednostka GB)|Count|Maksimum||Nie wymiarów|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|JobEndedSuccess|Zadania zakończone powodzeniem|Count|Łącznie|Liczba zadań zakończonych powodzeniem.|Nie wymiarów|
|JobEndedFailure|Zadania zakończone niepowodzeniem|Count|Łącznie|Liczba zadań zakończonych niepowodzeniem.|Nie wymiarów|
|JobEndedCancelled|Zadania anulowane|Count|Łącznie|Liczba anulowanych zadań.|Nie wymiarów|
|JobAUEndedSuccess|Czas pomyślnej aktualizacji|Sekundy|Łącznie|Łączny czas dla zadań zakończonych powodzeniem.|Nie wymiarów|
|JobAUEndedFailure|Czas niepowodzenia|Sekundy|Łącznie|Łączny czas AU zadań zakończonych niepowodzeniem.|Nie wymiarów|
|JobAUEndedCancelled|Anulowano czas aktualizacji|Sekundy|Łącznie|Łączny czas AU dla anulowanych zadań.|Nie wymiarów|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalStorage|Całkowita ilość miejsca do magazynowania|Bajty|Maksimum|Łączna ilość danych przechowywanych na koncie.|Nie wymiarów|
|Zapisywana|Zapisane dane|Bajty|Łącznie|Całkowita ilość danych zapisywana na konto.|Nie wymiarów|
|Odczyt danych|Odczyt danych|Bajty|Łącznie|Łączna ilość danych odczytanych z konta.|Nie wymiarów|
|WriteRequests|Żądania zapisu|Count|Łącznie|Liczba żądań zapisu danych na koncie.|Nie wymiarów|
|ReadRequests|Żądania odczytu|Count|Łącznie|Liczba żądań odczytu danych do konta.|Nie wymiarów|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Percent|Average|Procent użycia procesora CPU|Nie wymiarów|
|memory_percent|Procent pamięci|Percent|Average|Procent pamięci|Nie wymiarów|
|io_consumption_percent|Procent operacji we/wy|Percent|Average|Procent operacji we/wy|Nie wymiarów|
|storage_percent|Procent magazynu|Percent|Average|Procent magazynu|Nie wymiarów|
|storage_used|Używane miejsce|Bajty|Average|Używane miejsce|Nie wymiarów|
|storage_limit|Limit magazynu|Bajty|Average|Limit magazynu|Nie wymiarów|
|serverlog_storage_percent|Procent magazynu dzienników serwera|Percent|Average|Procent magazynu dzienników serwera|Nie wymiarów|
|serverlog_storage_usage|Używany magazyn dzienników serwera|Bajty|Average|Używany magazyn dzienników serwera|Nie wymiarów|
|serverlog_storage_limit|Limit magazynowania dziennika serwera|Bajty|Average|Limit magazynowania dziennika serwera|Nie wymiarów|
|active_connections|Aktywne połączenia|Count|Average|Aktywne połączenia|Nie wymiarów|
|connections_failed|Połączenia zakończone niepowodzeniem|Count|Łącznie|Połączenia zakończone niepowodzeniem|Nie wymiarów|
|seconds_behind_master|Opóźnienie replikacji w sekundach|Count|Average|Opóźnienie replikacji w sekundach|Nie wymiarów|
|backup_storage_used|Używany magazyn kopii zapasowych|Bajty|Average|Używany magazyn kopii zapasowych|Nie wymiarów|
|network_bytes_egress|Sieć — wyjście|Bajty|Łącznie|Sieć poza aktywnymi połączeniami|Nie wymiarów|
|network_bytes_ingress|Sieć — wejście|Bajty|Łącznie|Sieć w ramach aktywnych połączeń|Nie wymiarów|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Percent|Average|Procent użycia procesora CPU|Nie wymiarów|
|memory_percent|Procent pamięci|Percent|Average|Procent pamięci|Nie wymiarów|
|io_consumption_percent|Procent operacji we/wy|Percent|Average|Procent operacji we/wy|Nie wymiarów|
|storage_percent|Procent magazynu|Percent|Average|Procent magazynu|Nie wymiarów|
|storage_used|Używane miejsce|Bajty|Average|Używane miejsce|Nie wymiarów|
|storage_limit|Limit magazynu|Bajty|Average|Limit magazynu|Nie wymiarów|
|serverlog_storage_percent|Procent magazynu dzienników serwera|Percent|Average|Procent magazynu dzienników serwera|Nie wymiarów|
|serverlog_storage_usage|Używany magazyn dzienników serwera|Bajty|Average|Używany magazyn dzienników serwera|Nie wymiarów|
|serverlog_storage_limit|Limit magazynowania dziennika serwera|Bajty|Average|Limit magazynowania dziennika serwera|Nie wymiarów|
|active_connections|Aktywne połączenia|Count|Average|Aktywne połączenia|Nie wymiarów|
|connections_failed|Połączenia zakończone niepowodzeniem|Count|Łącznie|Połączenia zakończone niepowodzeniem|Nie wymiarów|
|seconds_behind_master|Opóźnienie replikacji w sekundach|Count|Average|Opóźnienie replikacji w sekundach|Nie wymiarów|
|backup_storage_used|Używany magazyn kopii zapasowych|Bajty|Average|Używany magazyn kopii zapasowych|Nie wymiarów|
|network_bytes_egress|Sieć — wyjście|Bajty|Łącznie|Sieć poza aktywnymi połączeniami|Nie wymiarów|
|network_bytes_ingress|Sieć — wejście|Bajty|Łącznie|Sieć w ramach aktywnych połączeń|Nie wymiarów|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Percent|Average|Procent użycia procesora CPU|Nie wymiarów|
|memory_percent|Procent pamięci|Percent|Average|Procent pamięci|Nie wymiarów|
|io_consumption_percent|Procent operacji we/wy|Percent|Average|Procent operacji we/wy|Nie wymiarów|
|storage_percent|Procent magazynu|Percent|Average|Procent magazynu|Nie wymiarów|
|storage_used|Używane miejsce|Bajty|Average|Używane miejsce|Nie wymiarów|
|storage_limit|Limit magazynu|Bajty|Average|Limit magazynu|Nie wymiarów|
|serverlog_storage_percent|Procent magazynu dzienników serwera|Percent|Average|Procent magazynu dzienników serwera|Nie wymiarów|
|serverlog_storage_usage|Używany magazyn dzienników serwera|Bajty|Average|Używany magazyn dzienników serwera|Nie wymiarów|
|serverlog_storage_limit|Limit magazynowania dziennika serwera|Bajty|Average|Limit magazynowania dziennika serwera|Nie wymiarów|
|active_connections|Aktywne połączenia|Count|Average|Aktywne połączenia|Nie wymiarów|
|connections_failed|Połączenia zakończone niepowodzeniem|Count|Łącznie|Połączenia zakończone niepowodzeniem|Nie wymiarów|
|backup_storage_used|Używany magazyn kopii zapasowych|Bajty|Average|Używany magazyn kopii zapasowych|Nie wymiarów|
|network_bytes_egress|Sieć — wyjście|Bajty|Łącznie|Sieć poza aktywnymi połączeniami|Nie wymiarów|
|network_bytes_ingress|Sieć — wejście|Bajty|Łącznie|Sieć w ramach aktywnych połączeń|Nie wymiarów|
|pg_replica_log_delay_in_seconds|Zwłoka repliki|Sekundy|Maksimum|Opóźnienie repliki w sekundach|Nie wymiarów|
|pg_replica_log_delay_in_bytes|Maksymalne opóźnienie między replikami|Bajty|Maksimum|Opóźnienie w bajtach najbardziej opóźnionej repliki|Nie wymiarów|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Percent|Average|Procent użycia procesora CPU|Nie wymiarów|
|memory_percent|Procent pamięci|Percent|Average|Procent pamięci|Nie wymiarów|
|Wejścia|IOPS|Count|Average|Operacje we/wy na sekundę|Nie wymiarów|
|storage_percent|Procent magazynu|Percent|Average|Procent magazynu|Nie wymiarów|
|storage_used|Używane miejsce|Bajty|Average|Używane miejsce|Nie wymiarów|
|active_connections|Aktywne połączenia|Count|Average|Aktywne połączenia|Nie wymiarów|
|network_bytes_egress|Sieć — wyjście|Bajty|Łącznie|Sieć poza aktywnymi połączeniami|Nie wymiarów|
|network_bytes_ingress|Sieć — wejście|Bajty|Łącznie|Sieć w ramach aktywnych połączeń|Nie wymiarów|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Próby wysłania komunikatów telemetrycznych|Count|Łącznie|Liczba komunikatów telemetrycznych z urządzenia do chmury, które próbowano wysłać do centrum IoT Hub|Nie wymiarów|
|d2c.telemetry.ingress.success|Wysłane komunikaty telemetryczne|Count|Łącznie|Liczba pomyślnie wysłanych komunikatów telemetrycznych z urządzenia do chmury do centrum IoT Hub|Nie wymiarów|
|c2d.commands.egress.complete.success|Polecenia ukończone|Count|Łącznie|Liczba poleceń z chmury do urządzenia ukończonych pomyślnie przez urządzenie|Nie wymiarów|
|c2d.commands.egress.abandon.success|Polecenia porzucone|Count|Łącznie|Liczba poleceń z chmury do urządzenia porzuconych przez urządzenie|Nie wymiarów|
|c2d.commands.egress.reject.success|Polecenia odrzucone|Count|Łącznie|Liczba poleceń z chmury do urządzenia odrzuconych przez urządzenie|Nie wymiarów|
|Devices. totalDevices|Łączna liczba urządzeń (przestarzałe)|Count|Łącznie|Liczba urządzeń zarejestrowanych w usłudze IoT Hub|Nie wymiarów|
|devices.connectedDevices.allProtocol|Podłączone urządzenia (przestarzałe) |Count|Łącznie|Liczba urządzeń podłączonych do centrum IoT Hub|Nie wymiarów|
|d2c.telemetry.egress.success|Routing: dostarczono komunikaty telemetryczne|Count|Łącznie|Liczba pomyślnie dostarczonych komunikatów do wszystkich punktów końcowych używających routingu IoT Hub. Jeśli komunikat jest kierowany do wielu punktów końcowych, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczenia. Jeśli wiadomość jest przekazywana do tego samego punktu końcowego wiele razy, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczenia.|Nie wymiarów|
|d2c.telemetry.egress.dropped|Routing: porzucone komunikaty telemetryczne |Count|Łącznie|Liczba porzuconych komunikatów przez IoT Hub Routing ze względu na martwe punkty końcowe. Ta wartość nie zlicza komunikatów dostarczonych do trasy rezerwowej, ponieważ opuszczone wiadomości nie są tam dostarczane.|Nie wymiarów|
|d2c.telemetry.egress.orphaned|Routing: oddzielone komunikaty telemetryczne |Count|Łącznie|Liczba oddzielonych komunikatów przez IoT Hub Routing, ponieważ nie są one zgodne z żadną regułą routingu (w tym regułą rezerwową). |Nie wymiarów|
|d2c.telemetry.egress.invalid|Routing: komunikaty telemetryczne są niezgodne|Count|Łącznie|Liczba przypadków, gdy Routing IoT Hub nie mógł dostarczyć komunikatów z powodu niezgodności z punktem końcowym. Ta wartość nie obejmuje ponownych prób.|Nie wymiarów|
|d2c.telemetry.egress.fallback|Routing: komunikaty dostarczane do powrotu|Count|Łącznie|Liczba IoT Hub komunikatów dostarczonych przez funkcję routingu do punktu końcowego skojarzonego z trasą rezerwową.|Nie wymiarów|
|d2c.endpoints.egress.eventHubs|Routing: komunikaty dostarczane do centrum zdarzeń|Count|Łącznie|Liczba pomyślnie dostarczonych komunikatów do punktów końcowych centrum zdarzeń w usłudze IoT Hub Routing.|Nie wymiarów|
|d2c.endpoints.latency.eventHubs|Routing: opóźnienie komunikatu dla centrum zdarzeń|MS|Average|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów przychodzących w punkcie końcowym centrum zdarzeń.|Nie wymiarów|
|d2c.endpoints.egress.serviceBusQueues|Routing: komunikaty dostarczone do kolejki Service Bus|Count|Łącznie|Liczba pomyślnie dostarczonych komunikatów do Service Bus punktów końcowych kolejki w usłudze IoT Hub Routing.|Nie wymiarów|
|d2c.endpoints.latency.serviceBusQueues|Routing: opóźnienie komunikatu dla kolejki Service Bus|MS|Average|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i telemetrii komunikatów przychodzących do punktu końcowego kolejki Service Bus.|Nie wymiarów|
|d2c.endpoints.egress.serviceBusTopics|Routing: komunikaty dostarczane do Service Bus tematu|Count|Łącznie|Liczba pomyślnie dostarczonych komunikatów przez IoT Hub Routing do Service Bus punktów końcowych tematu.|Nie wymiarów|
|d2c.endpoints.latency.serviceBusTopics|Routing: opóźnienie komunikatu dla Service Bus tematu|MS|Average|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w punkcie końcowym tematu Service Bus.|Nie wymiarów|
|d2c.endpoints.egress.builtIn.events|Routing: komunikaty dostarczane do komunikatów/zdarzeń|Count|Łącznie|Liczba pomyślnie dostarczonych komunikatów do wbudowanego punktu końcowego (komunikaty/zdarzenia) IoT Hub Routing. Ta Metryka uruchamia się tylko wtedy, gdy Routing https://aka.ms/iotrouting) jest włączony (dla Centrum IoT Hub).|Nie wymiarów|
|d2c.endpoints.latency.builtIn.events|Routing: opóźnienie komunikatów dla komunikatów/zdarzeń|MS|Average|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w wbudowanym punkcie końcowym (komunikaty/zdarzenia). Ta Metryka uruchamia się tylko wtedy, gdy Routing https://aka.ms/iotrouting) jest włączony (dla Centrum IoT Hub).|Nie wymiarów|
|d2c.endpoints.egress.storage|Routing: komunikaty dostarczane do magazynu|Count|Łącznie|Liczba pomyślnie dostarczonych komunikatów do punktów końcowych usługi Routing IoT Hub.|Nie wymiarów|
|d2c.endpoints.latency.storage|Routing: opóźnienie komunikatu dla magazynu|MS|Average|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w punkcie końcowym magazynu.|Nie wymiarów|
|d2c.endpoints.egress.storage.bytes|Routing: dane dostarczane do magazynu|Bajty|Łącznie|Ilość danych (w bajtach) IoT Hub Routing dostarczany do punktów końcowych magazynu.|Nie wymiarów|
|d2c.endpoints.egress.storage.blobs|Routing: obiekty blob dostarczane do magazynu|Count|Łącznie|Ile razy usługa Routing IoT Hub dostarczać obiekty blob do punktów końcowych magazynu.|Nie wymiarów|
|EventGridDeliveries|Dostawy Event Grid (wersja zapoznawcza)|Count|Łącznie|Liczba zdarzeń IoT Hub opublikowanych do Event Grid. Użyj wymiaru wynik dla liczby żądań zakończonych powodzeniem i niepowodzeniem. Wymiar EventType przedstawia typ zdarzenia (https://aka.ms/ioteventgrid).|Wynik, typ zdarzenia|
|EventGridLatency|Średnie opóźnienie (w milisekundach) od momentu wygenerowania zdarzenia usługi IoT Hub po opublikowaniu zdarzenia w Event Grid. Ta liczba jest średnia między wszystkimi typami zdarzeń. Użyj wymiaru EventType, aby zobaczyć opóźnienie określonego typu zdarzenia.|EventType|
|d2c.twin.read.success|Pomyślne odczyty sznurów z urządzeń|Count|Łącznie|Liczba wszystkich udanych operacji zainicjowanych przez urządzenie.|Nie wymiarów|
|d2c.twin.read.failure|Nieudane odczyty sznurów z urządzeń|Count|Łącznie|Liczba wszystkich nieudanych operacji zainicjowanych przez urządzenie.|Nie wymiarów|
|d2c.twin.read.size|Rozmiar odpowiedzi dla sznurów odczytanych z urządzeń|Bajty|Average|Średnia, minimum i maksimum wszystkich udanych zainicjowanych przez urządzenia sznurów.|Nie wymiarów|
|d2c.twin.update.success|Pomyślne aktualizacje bliźniaczych urządzeń|Count|Łącznie|Liczba wszystkich pomyślnych aktualizacji typu bliźniaczych zainicjowanych przez urządzenie.|Nie wymiarów|
|d2c.twin.update.failure|Niepowodzenie aktualizacji bliźniaczych z urządzeń|Count|Łącznie|Liczba wszystkich niezakończonych niepowodzeniem aktualizacji dwuosiowych zainicjowanych przez urządzenie.|Nie wymiarów|
|d2c.twin.update.size|Rozmiar aktualizacji bliźniaczych z urządzeń|Bajty|Average|Średni, minimalny i maksymalny rozmiar wszystkich pomyślnych aktualizacji typu dwuosiowego zainicjowanego przez urządzenie.|Nie wymiarów|
|c2d.methods.success|Pomyślne wywołania metody bezpośredniej|Count|Łącznie|Liczba wszystkich pomyślnych wywołań metody bezpośredniej.|Nie wymiarów|
|C2D. Methods. Failure|Nieudane wywołania metody bezpośredniej|Count|Łącznie|Liczba wszystkich wywołań metod bezpośrednich zakończonych niepowodzeniem.|Nie wymiarów|
|c2d.methods.requestSize|Rozmiar żądania wywołań metody bezpośredniej|Bajty|Average|Średnia, minimum i maksimum wszystkich pomyślnych żądań metody bezpośredniej.|Nie wymiarów|
|c2d.methods.responseSize|Rozmiar odpowiedzi wywołań metody bezpośredniej|Bajty|Average|Średnia, minimum i maksimum wszystkich zakończonych powodzeniem odpowiedzi metody bezpośredniej.|Nie wymiarów|
|c2d.twin.read.success|Pomyślne odczyty sznurów z zaplecza|Count|Łącznie|Liczba wszystkich zakończonych powodzeniem odczytów dwuosiowych zainicjowanych z powrotem.|Nie wymiarów|
|c2d.twin.read.failure|Nieudane odczyty sznurów z zaplecza|Count|Łącznie|Liczba wszystkich zakończonych niepowodzeniem odczytów dwuosiowych zainicjowanych z powrotem.|Nie wymiarów|
|c2d.twin.read.size|Rozmiar odpowiedzi na odwrocie od zaplecza|Bajty|Average|Średnia, minimum i maksimum wszystkich udanych zainicjowanych z powrotem sznurów danych.|Nie wymiarów|
|c2d.twin.update.success|Pomyślne aktualizacje bliźniaczych z zaplecza|Count|Łącznie|Liczba wszystkich pomyślnych aktualizacji typu bliźniaczych zainicjowanych z powrotem.|Nie wymiarów|
|c2d.twin.update.failure|Niepowodzenie aktualizacji bliźniaczych z zaplecza|Count|Łącznie|Liczba wszystkich niezakończonych niepowodzeniem aktualizacji typu bliźniaczych zainicjowanych z powrotem.|Nie wymiarów|
|c2d.twin.update.size|Rozmiar aktualizacji przędzy od zaplecza|Bajty|Average|Średni, minimalny i maksymalny rozmiar wszystkich pomyślnych aktualizacji typu sznurka zainicjowanych z powrotem.|Nie wymiarów|
|twinQueries.success|Pomyślne zapytania bliźniaczy|Count|Łącznie|Liczba wszystkich udanych zapytań bliźniaczych.|Nie wymiarów|
|twinQueries.failure|Niepowodzenie zapytań bliźniaczych|Count|Łącznie|Liczba wszystkich zakończonych niepowodzeniem zapytań bliźniaczych.|Nie wymiarów|
|twinQueries.resultSize|Rozmiar wyniku zapytań bliźniaczych|Bajty|Average|Średnia, minimalna i maksymalna wielkość wyniku wszystkich udanych zapytań bliźniaczych.|Nie wymiarów|
|jobs.createTwinUpdateJob.success|Pomyślne utworzenie dwuosiowych zadań aktualizacji|Count|Łącznie|Liczba wszystkich udanych tworzenia zadań aktualizacji z przędzą.|Nie wymiarów|
|jobs.createTwinUpdateJob.failure|Nie można utworzyć dwuosiowych zadań aktualizacji|Count|Łącznie|Liczba wszystkich nieudanych operacji tworzenia zadań aktualizacji z przędzą.|Nie wymiarów|
|jobs.createDirectMethodJob.success|Pomyślne utworzenie zadań wywołania metody|Count|Łącznie|Liczba wszystkich pomyślnych operacji tworzenia zadań wywołania metody bezpośredniej.|Nie wymiarów|
|jobs.createDirectMethodJob.failure|Nie można utworzyć zadań wywołania metody|Count|Łącznie|Liczba wszystkich nieudanych operacji tworzenia zadań wywołania metody bezpośredniej.|Nie wymiarów|
|jobs.listJobs.success|Pomyślne wywołania do zadań na liście|Count|Łącznie|Liczba wszystkich udanych wywołań do listy zadań.|Nie wymiarów|
|jobs.listJobs.failure|Wywołania zakończone niepowodzeniem do listy zadań|Count|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu wyświetlenia listy zadań.|Nie wymiarów|
|jobs.cancelJob.success|Pomyślne anulowania zadań|Count|Łącznie|Liczba wszystkich udanych wywołań do anulowania zadania.|Nie wymiarów|
|jobs.cancelJob.failure|Nieudane anulowania zadań|Count|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu anulowania zadania.|Nie wymiarów|
|jobs.queryJobs.success|Pomyślne zapytania dotyczące zadań|Count|Łącznie|Liczba wszystkich udanych wywołań do zadań zapytań.|Nie wymiarów|
|jobs.queryJobs.failure|Nieudane kwerendy zadań|Count|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu wykonywania zapytań dotyczących zadań.|Nie wymiarów|
|zadania. ukończone|Ukończone zadania|Count|Łącznie|Liczba wszystkich ukończonych zadań.|Nie wymiarów|
|zadania. Niepowodzenie|Zadania zakończone niepowodzeniem|Count|Łącznie|Liczba wszystkich zadań zakończonych niepowodzeniem.|Nie wymiarów|
|d2c.telemetry.ingress.sendThrottle|Liczba błędów ograniczania|Count|Łącznie|Liczba błędów ograniczania z powodu ograniczeń przepływności urządzenia|Nie wymiarów|
|dailyMessageQuotaUsed|Całkowita liczba użytych komunikatów|Count|Average|Całkowita liczba użytych komunikatów. Jest to skumulowana wartość, która jest resetowana do zera o godzinie 00:00 czasu każdego dnia.|Nie wymiarów|
|deviceDataUsage|Całkowite użycie danych urządzenia|Bajty|Łącznie|Bajty przesłane do i z dowolnych urządzeń podłączonych do usługi IotHub|Nie wymiarów|
|totalDeviceCount|Łączna liczba urządzeń (wersja zapoznawcza)|Count|Average|Liczba urządzeń zarejestrowanych w usłudze IoT Hub|Nie wymiarów|
|connectedDeviceCount|Podłączone urządzenia (wersja zapoznawcza)|Count|Average|Liczba urządzeń podłączonych do centrum IoT Hub|Nie wymiarów|
|komputerów|Metryki konfiguracji|Count|Łącznie|Metryki dla operacji konfiguracji|Nie wymiarów|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RegistrationAttempts|Próby rejestracji|Count|Łącznie|Liczba prób przeprowadzenia rejestracji urządzeń|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Przypisane urządzenia|Count|Łącznie|Liczba urządzeń przypisanych do centrum IoT Hub|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Próby zaświadczania|Count|Łącznie|Liczba podjętych prób zaświadczania urządzenia|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AvailableStorage|Dostępny magazyn|Bajty|Łącznie|Łączna ilość dostępnego magazynu zgłoszona z dokładnością do 5 minut|CollectionName, DatabaseName, region|
|CassandraConnectionClosures|Zamknięcia połączeń Cassandra|Count|Łącznie|Liczba zamkniętych połączeń Cassandra, które zostały zgłoszone z dokładnością do 1 minuty|Region, ClosureReason|
|CassandraRequestCharges|Opłaty za żądania Cassandra|Count|Łącznie|Jednostek ru zużyte dla żądań Cassandra|DatabaseName, CollectionName, region, OperationType, ResourceType|
|CassandraRequests|Żądania Cassandra|Count|Count|Liczba wykonanych żądań Cassandra|DatabaseName, CollectionName, region, OperationType, ResourceType, ErrorCode|
|Datausage|Użycie danych|Bajty|Łącznie|Całkowite użycie danych zgłoszone z dokładnością do 5 minut|CollectionName, DatabaseName, region|
|DocumentCount|Liczba dokumentów|Count|Łącznie|Łączna liczba dokumentów raportowana z dokładnością do 5 minut|CollectionName, DatabaseName, region|
|DocumentQuota|Przydział dokumentu|Bajty|Łącznie|Łączny przydział magazynu zgłoszony z dokładnością do 5 minut|CollectionName, DatabaseName, region|
|IndexUsage|Użycie indeksu|Bajty|Łącznie|Całkowite użycie indeksów zgłoszone na 5 minut|CollectionName, DatabaseName, region|
|MetadataRequests|Żądania metadanych|Count|Count|Liczba żądań metadanych. Cosmos DB przechowuje zbieranie metadanych systemu dla każdego konta, które pozwala na Wyliczanie kolekcji, baz danych itp. i ich konfiguracji bez opłat.|DatabaseName, CollectionName, region, StatusCode, |
|MongoRequestCharge|Opłata żądania Mongo|Count|Łącznie|Wykorzystane jednostki żądania Mongo|DatabaseName, CollectionName, region, CommandName, ErrorCode|
|MongoRequests|Żądania Mongo|Count|Count|Liczba wykonanych żądań Mongo|DatabaseName, CollectionName, region, CommandName, ErrorCode|
|ProvisionedThroughput|Aprowizowana przepływność|Count|Maksimum|Aprowizowana przepływność|DatabaseName, CollectionName|
|ReplicationLatency|Opóźnienie replikacji poziomie P99|MilliSeconds|Average|Opóźnienie replikacji poziomie P99 w regionach źródłowym i docelowym dla konta z obsługą geograficzną|SourceRegion, TargetRegion|
|Dostępność|Dostępność usługi|Percent|Average|Dostępność żądania konta o jednej godzinie, dniu lub o dokładności|Nie wymiarów|
|TotalRequestUnits|Łączna liczba jednostek żądania|Count|Łącznie|Wykorzystane jednostki żądania|DatabaseName, CollectionName, region, StatusCode, OperationType|
|TotalRequests|Łączna liczba żądań|Count|Count|Liczba wykonanych żądań|DatabaseName, CollectionName, region, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/tematy

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PublishSuccessCount|Zdarzenia opublikowane|Count|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Nie wymiarów|
|PublishFailCount|Publikowanie zdarzeń zakończonych niepowodzeniem|Count|Łącznie|Całkowita liczba zdarzeń, których publikowanie nie powiodło się w tym temacie|ErrorType, błąd|
|UnmatchedEventCount|Niedopasowane zdarzenia|Count|Łącznie|Łączna liczba zdarzeń, które nie pasują do żadnej subskrypcji zdarzeń dla tego tematu|Nie wymiarów|
|PublishSuccessLatencyInMs|Czas oczekiwania na pomyślne publikowanie|Count|Łącznie|Opóźnienie sukcesu publikacji w milisekundach|Nie wymiarów|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|MatchedEventCount|Dopasowane zdarzenia|Count|Łącznie|Łączna liczba zdarzeń dopasowanych do tej subskrypcji zdarzeń|Nie wymiarów|
|DeliveryAttemptFailCount|Zdarzenia zakończonych niepowodzeniem|Count|Łącznie|Całkowita liczba zdarzeń, których dostarczenie do tej subskrypcji zdarzeń nie powiodło się|Błąd, Błądtype|
|DeliverySuccessCount|Dostarczone zdarzenia|Count|Łącznie|Całkowita liczba zdarzeń dostarczonych do tej subskrypcji zdarzeń|Nie wymiarów|
|DestinationProcessingDurationInMs|Czas przetwarzania docelowego|MS|Average|Czas trwania przetwarzania docelowego w milisekundach|Nie wymiarów|
|DroppedEventCount|Opuszczone zdarzenia|Count|Łącznie|Całkowita liczba porzuconych zdarzeń pasujących do tej subskrypcji zdarzeń|DropReason|
|DeadLetteredCount|Zdarzenia utraconych wiadomości|Count|Łącznie|Łączna liczba utraconych zdarzeń, które pasują do tej subskrypcji zdarzeń|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PublishSuccessCount|Zdarzenia opublikowane|Count|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Nie wymiarów|
|PublishFailCount|Zdarzenia zakończone niepowodzeniem|Count|Łącznie|Całkowita liczba zdarzeń, których publikowanie nie powiodło się w tym temacie|ErrorType, błąd|
|UnmatchedEventCount|Niedopasowane zdarzenia|Count|Łącznie|Łączna liczba zdarzeń, które nie pasują do żadnej subskrypcji zdarzeń dla tego tematu|Nie wymiarów|
|PublishSuccessLatencyInMs|Czas oczekiwania na pomyślne publikowanie|Count|Łącznie|Opóźnienie sukcesu publikacji w milisekundach|Nie wymiarów|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Żądania successfulrequests|Żądania zakończone powodzeniem|Count|Łącznie|Pomyślne żądania dla elementu Microsoft. EventHub.|EntityName, |
|Błędy servererrors|Błędy serwera.|Count|Łącznie|Błędy serwera dla elementu Microsoft. EventHub.|EntityName, |
|Błędy usererrors|Błędy użytkownika.|Count|Łącznie|Błędy użytkowników dla elementu Microsoft. EventHub.|EntityName, |
|Błędy quotaexceedederrors|Błędy przekroczenia limitu przydziału.|Count|Łącznie|Przekroczono limit przydziału dla elementu Microsoft. EventHub.|EntityName, |
|Żądania throttledrequests|Żądania z ograniczeniami.|Count|Łącznie|Żądania ograniczone dla elementu Microsoft. EventHub.|EntityName, |
|Żądania incomingrequests|Żądania przychodzące|Count|Łącznie|Żądania przychodzące dla elementu Microsoft. EventHub.|EntityName|
|Komunikaty incomingmessages|Wiadomości przychodzące|Count|Łącznie|Komunikaty przychodzące dla elementu Microsoft. EventHub.|EntityName|
|Komunikaty outgoingmessages|Wiadomości wychodzące|Count|Łącznie|Komunikaty wychodzące dla elementu Microsoft. EventHub.|EntityName|
|Bajty incomingbytes|Bajty przychodzące.|Bajty|Łącznie|Przychodzące bajty dla elementu Microsoft. EventHub.|EntityName|
|Bajty outgoingbytes|Bajty wychodzące.|Bajty|Łącznie|Bajty wychodzące dla elementu Microsoft. EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Count|Average|Łączna liczba aktywnych połączeń dla elementu Microsoft. EventHub.|Nie wymiarów|
|Połączenia connectionsopened|Otwarte połączenia.|Count|Average|Połączenia otwarte dla elementu Microsoft. EventHub.|EntityName|
|Połączenia connectionsclosed|Połączenia zamknięte.|Count|Average|Połączenia zamknięte dla elementu Microsoft. EventHub.|EntityName|
|CaptureBacklog|Zaległości przechwytywania.|Count|Łącznie|Zaległości przechwytywania dla elementu Microsoft. EventHub.|EntityName|
|CapturedMessages|Przechwycone komunikaty.|Count|Łącznie|Przechwycone komunikaty dla elementu Microsoft. EventHub.|EntityName|
|CapturedBytes|Przechwycone bajty.|Bajty|Łącznie|Przechwycone bajty dla elementu Microsoft. EventHub.|EntityName|
|Size|Size|Bajty|Average|Rozmiar centrum EventHub w bajtach.|EntityName|
|INREQS|Żądania przychodzące (przestarzałe)|Count|Łącznie|Całkowita liczba żądań wysłania przychodzącego dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|SUCCREQ|Żądania zakończone powodzeniem (przestarzałe)|Count|Łącznie|Łączna liczba pomyślnych żądań dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|FAILREQ|Nieudane żądania (przestarzałe)|Count|Łącznie|Łączna liczba nieudanych żądań dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|SVRBSY|Błędy zajęte przez serwer (przestarzałe)|Count|Łącznie|Łączna liczba błędów zajętości serwera dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|MIĘDZY|Wewnętrzne błędy serwera (przestarzałe)|Count|Łącznie|Łączna liczba błędów wewnętrznego serwera dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|MISCERR|Inne błędy (przestarzałe)|Count|Łącznie|Łączna liczba nieudanych żądań dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|INMSGS|Komunikaty przychodzące (przestarzałe) (przestarzałe)|Count|Łącznie|Całkowita liczba komunikatów przychodzących dla przestrzeni nazw. Ta Metryka jest przestarzała. Zamiast tego użyj metryki komunikatów przychodzących (przestarzałe)|Nie wymiarów|
|EHINMSGS|Komunikaty przychodzące (przestarzałe)|Count|Łącznie|Całkowita liczba komunikatów przychodzących dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|OUTMSGS|Wiadomości wychodzące (przestarzałe) (przestarzałe)|Count|Łącznie|Całkowita liczba komunikatów wychodzących dla przestrzeni nazw. Ta Metryka jest przestarzała. Zamiast tego użyj metryki komunikatów wychodzących (przestarzałe)|Nie wymiarów|
|EHOUTMSGS|Wiadomości wychodzące (przestarzałe)|Count|Łącznie|Całkowita liczba komunikatów wychodzących dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|EHINMBS|Przychodzące bajty (przestarzałe) (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów przychodzących centrum zdarzeń dla przestrzeni nazw. Ta Metryka jest przestarzała. Zamiast tego użyj metryki bajtów przychodzących (przestarzałe)|Nie wymiarów|
|EHINBYTES|Przychodzące bajty (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów przychodzących centrum zdarzeń dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|EHOUTMBS|Bajty wychodzące (przestarzałe) (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów wychodzących centrum zdarzeń dla przestrzeni nazw. Ta Metryka jest przestarzała. Zamiast tego użyj metryki bajtów wychodzących (przestarzałe)|Nie wymiarów|
|EHOUTBYTES|Bajty wychodzące (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów wychodzących centrum zdarzeń dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|EHABL|Archiwum komunikatów zaległości (przestarzałe)|Count|Łącznie|Komunikaty archiwum centrum zdarzeń w zaległości dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|EHAMSGS|Komunikaty archiwalne (przestarzałe)|Count|Łącznie|Zarchiwizowane komunikaty centrum zdarzeń w przestrzeni nazw (przestarzałe)|Nie wymiarów|
|EHAMBS|Przepływność komunikatów archiwalnych (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów archiwalnych centrum zdarzeń w przestrzeni nazw (przestarzałe)|Nie wymiarów|

## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/klastry

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Żądania successfulrequests|Pomyślne żądania (wersja zapoznawcza)|Count|Łącznie|Pomyślne żądania dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|Błędy servererrors|Błędy serwera. (Wersja zapoznawcza)|Count|Łącznie|Błędy serwera dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|Błędy usererrors|Błędy użytkownika. (Wersja zapoznawcza)|Count|Łącznie|Błędy użytkowników dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|Błędy quotaexceedederrors|Błędy przekroczenia limitu przydziału. (Wersja zapoznawcza)|Count|Łącznie|Przekroczono limit przydziału dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|Żądania throttledrequests|Żądania z ograniczeniami. (Wersja zapoznawcza)|Count|Łącznie|Żądania ograniczone dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|Żądania incomingrequests|Żądania przychodzące (wersja zapoznawcza)|Count|Łącznie|Żądania przychodzące dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|Komunikaty incomingmessages|Wiadomości przychodzące (wersja zapoznawcza)|Count|Łącznie|Komunikaty przychodzące dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|Komunikaty outgoingmessages|Wiadomości wychodzące (wersja zapoznawcza)|Count|Łącznie|Komunikaty wychodzące dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|Bajty incomingbytes|Bajty przychodzące. (Wersja zapoznawcza)|Bajty|Łącznie|Przychodzące bajty dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|Bajty outgoingbytes|Bajty wychodzące. (Wersja zapoznawcza)|Bajty|Łącznie|Bajty wychodzące dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|ActiveConnections|Połączeń ActiveConnections (wersja zapoznawcza)|Count|Average|Łączna liczba aktywnych połączeń dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|Połączenia connectionsopened|Otwarte połączenia. (Wersja zapoznawcza)|Count|Average|Połączenia otwarte dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|Połączenia connectionsclosed|Połączenia zamknięte. (Wersja zapoznawcza)|Count|Average|Połączenia zamknięte dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|CaptureBacklog|Zaległości przechwytywania. (Wersja zapoznawcza)|Count|Łącznie|Zaległości przechwytywania dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|CapturedMessages|Przechwycone komunikaty. (Wersja zapoznawcza)|Count|Łącznie|Przechwycone komunikaty dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|CapturedBytes|Przechwycone bajty. (Wersja zapoznawcza)|Bajty|Łącznie|Przechwycone bajty dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|Procesor CPU|Procesor CPU (wersja zapoznawcza)|Percent|Maksimum|Użycie procesora CPU przez klaster centrum zdarzeń jako wartość procentowa|Role|
|AvailableMemory|Dostępna pamięć (wersja zapoznawcza)|Count|Maksimum|Dostępna pamięć dla klastra centrum zdarzeń w bajtach|Role|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|GatewayRequests|Żądania bramy|Count|Łącznie|Liczba żądań bramy|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Skategoryzowane żądania bramy|Count|Łącznie|Liczba żądań bramy według kategorii (1XX/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Liczba aktywnych procesów roboczych|Count|Maksimum|Liczba aktywnych procesów roboczych|ClusterDnsName, Metricname|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ObservedMetricValue|Zaobserwowana wartość metryki|Count|Average|Wartość obliczona przez automatyczne skalowanie podczas wykonywania|MetricTriggerSource|
|MetricThreshold|Próg metryki|Count|Average|Skonfigurowany próg automatycznego skalowania podczas uruchamiania automatycznego skalowania.|MetricTriggerRule|
|ObservedCapacity|Zaobserwowana wydajność|Count|Average|Pojemność zgłoszona do automatycznego skalowania podczas jego wykonywania.|Nie wymiarów|
|ScaleActionsInitiated|Zainicjowane akcje skalowania|Count|Łącznie|Kierunek operacji skalowania.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/składniki

(Publiczna wersja zapoznawcza)

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Dostępność|Procent|Average|Procent pomyślnie ukończonych testów dostępności|availabilityResult/nazwa, availabilityResult/lokalizacja|
|availabilityResults/liczba|Testy dostępności|Count|Count|Liczba testów dostępności|availabilityResult/nazwa, availabilityResult/lokalizacja, availabilityResult/sukces|
|availabilityResults/czas trwania|Czas trwania testu dostępności|MilliSeconds|Average|Czas trwania testu dostępności|availabilityResult/nazwa, availabilityResult/lokalizacja, availabilityResult/sukces|
|browserTimings/networkDuration|Czas połączenia sieciowego podczas ładowania strony|MilliSeconds|Average|Czas między żądaniem użytkownika a połączeniem sieciowym. Obejmuje wyszukiwanie DNS i połączenie transportowe.|Nie wymiarów|
|browserTimings/processingDuration|Czas przetwarzania klienta|MilliSeconds|Average|Czas między odebraniem ostatniego bajtu dokumentu do momentu załadowania modelu DOM. Żądania asynchroniczne nadal mogą być przetwarzane.|Nie wymiarów|
|browserTimings/receiveDuration|Czas odpowiedzi odbierania|MilliSeconds|Average|Czas między pierwszym i ostatnim bajtem lub do momentu odłączenia.|Nie wymiarów|
|browserTimings/sendDuration|Czas żądania wysyłania|MilliSeconds|Average|Czas między połączeniem sieciowym i odebraniem pierwszego bajtu.|Nie wymiarów|
|browserTimings/totalDuration|Czas ładowania strony przeglądarki|MilliSeconds|Average|Czas od żądania użytkownika do załadowania modelu DOM, arkuszy stylów, skryptów i obrazów.|Nie wymiarów|
|zależności/liczba|Wywołania zależności|Count|Count|Liczba wywołań wykonanych przez aplikację do zasobów zewnętrznych.|zależność/typ, zależność/performanceBucket, zależność/sukces, operacja/syntetyczne, Chmura/roleInstance, Chmura/rolename|
|zależności/czas trwania|Czas trwania zależności|MilliSeconds|Average|Czas trwania wywołań wykonanych przez aplikację do zasobów zewnętrznych.|zależność/typ, zależność/performanceBucket, zależność/sukces, operacja/syntetyczne, Chmura/roleInstance, Chmura/rolename|
|zależności/niepowodzenie|Błędy wywołań zależności|Count|Count|Liczba wywołań zależności zakończonych niepowodzeniem wykonanych przez aplikację w zasobach zewnętrznych.|zależność/typ, zależność/performanceBucket, operacja/syntetyczne, Chmura/roleInstance, Chmura/rolename|
|pageViews/liczba|Wyświetlenia strony|Count|Count|Liczba wyświetleń stron.|Operacja/syntetyczna|
|pageViews/czas trwania|Wyświetlenie strony — czas ładowania|MilliSeconds|Average|Wyświetlenie strony — czas ładowania|Operacja/syntetyczna|
|Liczniki wydajności/requestExecutionTime|Czas wykonywania żądania HTTP|MilliSeconds|Average|Czas wykonywania najnowszego żądania.|Chmura/roleInstance|
|performanceCounters/requestsInQueue|Żądania HTTP w kolejce aplikacji|Count|Average|Długość kolejki żądań aplikacji.|Chmura/roleInstance|
|performanceCounters/requestsPerSecond|Liczba żądań HTTP|CountPerSecond|Average|Szybkość wszystkich żądań wysyłanych do aplikacji w ciągu sekundy od ASP.NET.|Chmura/roleInstance|
|Liczniki wydajności/exceptionsPerSecond|Częstotliwość występowania wyjątków|CountPerSecond|Average|Liczba obsłużonych i nieobsłużonych wyjątków zgłoszonych w systemie Windows, łącznie z wyjątkami platformy .NET i niezarządzanymi wyjątkami przekonwertowanymi na wyjątki platformy .NET.|Chmura/roleInstance|
|Liczniki wydajności/processIOBytesPerSecond|Liczba operacji we/wy procesu|BytesPerSecond|Average|Łączna liczba bajtów odczytanych i zapisywana w plikach, sieci i urządzeniach.|Chmura/roleInstance|
|Liczniki wydajności/processCpuPercentage|Procesy — procesor CPU|Percent|Average|Wyrażony w procentach czas, przez jaki wszystkie wątki procesów używały procesora do wykonywania instrukcji. Może się to różnić od od 0 do 100. Ta Metryka wskazuje na wydajność samego procesu w3wp.|Chmura/roleInstance|
|Liczniki wydajności/processorCpuPercentage|Czas procesora|Percent|Average|Procent czasu spędzanego przez procesor w wątkach, które nie są bezczynne.|Chmura/roleInstance|
|Liczniki wydajności/memoryAvailableBytes|Dostępna pamięć|Bajty|Average|Pamięć fizyczna natychmiast dostępna do przydzielenia do procesu lub do użycia przez system.|Chmura/roleInstance|
|Liczniki wydajności/processPrivateBytes|Prywatne bajty procesu|Bajty|Average|Pamięć przypisana wyłącznie do procesów monitorowanej aplikacji.|Chmura/roleInstance|
|żądania/czas trwania|Czas odpowiedzi serwera|MilliSeconds|Average|Czas między odebraniem żądania HTTP i zakończeniem wysyłania odpowiedzi.|żądanie/performanceBucket, żądanie/resultCode, Operation/syntetyczne, Cloud/roleInstance, żądanie/powodzenie, Chmura/rolename|
|Liczba żądań na sekundę|Żądania serwera|Count|Count|Liczba ukończonych żądań HTTP.|żądanie/performanceBucket, żądanie/resultCode, Operation/syntetyczne, Cloud/roleInstance, żądanie/powodzenie, Chmura/rolename|
|żądania/niepowodzenie|Żądania zakończone niepowodzeniem|Count|Count|Liczba żądań HTTP oznaczonych jako zakończone niepowodzeniem. W większości przypadków są to żądania z kodem odpowiedzi > = 400, a nie równe 401.|żądania/performanceBucket, Request/resultCode, Operation/syntetyczne, Cloud/roleInstance, Cloud/rolename|
|żądania/częstotliwość|Liczba żądań serwera|CountPerSecond|Average|Liczba żądań serwera na sekundę|żądanie/performanceBucket, żądanie/resultCode, Operation/syntetyczne, Cloud/roleInstance, żądanie/powodzenie, Chmura/rolename|
|wyjątki/liczba|Wyjątki|Count|Count|Łączna liczba wszystkich nieprzechwyconych wyjątków.|Chmura/rolename, Cloud/roleInstance, klient/typ|
|wyjątki/przeglądarka|Wyjątki przeglądarki|Count|Count|Liczba nieprzechwyconych wyjątków zgłoszonych w przeglądarce.|Nie wymiarów|
|wyjątki/serwer|Wyjątki serwera|Count|Count|Liczba nieprzechwyconych wyjątków zgłoszonych w aplikacji serwera.|Chmura/rolename, Cloud/roleInstance|
|ślady/liczba|Ślady|Count|Count|Liczba dokumentów śladów|Trace/severityLevel, Operations/syntetyczne, Cloud/rolename, Cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ServiceApiHit|Całkowita liczba trafień interfejsu API usługi|Count|Count|Łączna liczba trafień interfejsu API usługi|ActivityType, ActivityName|
|ServiceApiLatency|Ogólny czas oczekiwania żądań interfejsu API usługi|MS|Average|Ogólne opóźnienie żądań interfejsu API usługi|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Całkowita liczba wyników interfejsu API usługi|Count|Count|Łączna liczba wyników interfejsu API usługi|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CacheUtilization|Użycie pamięci podręcznej|Percent|Average|Poziom użycia w zakresie klastra|Brak|
|QueryDuration|Czas trwania zapytania|MS|Average|Czas trwania zapytań (w sekundach)|Stan zapytania|
|IngestionUtilization|Wykorzystanie pozyskiwania|Percent|Average|Współczynnik używania miejsc pozyskiwania w klastrze|Brak|
|KeepAlive|Utrzymywanie aktywności|Count|Average|Sprawdzenie Sanity wskazuje, że klaster reaguje na zapytania|Brak|
|IngestionVolumeInMB|Wolumin pozyskiwania (w MB)|Count|Łącznie|Ogólna ilość danych pozyskiwanych w klastrze (w MB)|Database (Baza danych)|
|IngestionLatencyInSeconds|Opóźnienie pozyskiwania (w sekundach)|Sekundy|Average|Czas pozyskiwania ze źródła (np. komunikat jest w centrum EventHub) do klastra w ciągu kilku sekund|Brak|
|EventProcessedForEventHubs|Przetworzone zdarzenia (dla Event Hubs)|Count|Łącznie|Liczba zdarzeń przetwarzanych przez klaster podczas pozyskiwania danych z centrum zdarzeń|Brak|
|IngestionResult|Wynik pozyskiwania|Count|Count|Liczba operacji pozyskiwania|State|
|Procesor CPU|Procesor CPU|Percent|Average|Poziom użycia procesora CPU|Brak|
| ContinuousExportNumOfRecordsExported | Liczba rekordów eksportowanych w ramach eksportu ciągłego | Count | Łącznie | Liczba rekordów eksportowanych dla każdego artefaktu magazynu zapisanego podczas operacji eksportowania  | Brak |
| ExportUtilization | Użycie eksportu | Percent | Maksimum | Użycie eksportu | Brak |
| ContinuousExportPendingCount | Liczba oczekujących eksportu ciągłego | Count | Maksimum | Liczba oczekujących zadań eksportu ciągłego gotowych do wykonania | Brak |
| ContinuousExportMaxLatenessMinutes | Maksymalna liczba minut opóźnienia eksportu | Count | Maksimum | Maksymalny czas (w minutach) wszystkich eksportów ciągłych oczekujących i gotowych do wykonania | Brak |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Użycie|Użycie|Count|Count|Liczba wywołań interfejsu API|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RunsStarted|Przebiegi rozpoczęte|Count|Łącznie|Liczba uruchomionych przebiegów przepływu pracy.|Nie wymiarów|
|RunsCompleted|Przebiegi ukończone|Count|Łącznie|Liczba ukończonych przebiegów przepływu pracy.|Nie wymiarów|
|RunsSucceeded|Przebiegi zakończone powodzeniem|Count|Łącznie|Liczba przebiegów przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|RunsFailed|Przebiegi zakończone niepowodzeniem|Count|Łącznie|Liczba przebiegów przepływu pracy zakończonych niepowodzeniem.|Nie wymiarów|
|RunsCancelled|Przebiegi anulowane|Count|Łącznie|Liczba anulowanych przebiegów przepływu pracy.|Nie wymiarów|
|RunLatency|Opóźnienie przebiegów|Sekundy|Average|Opóźnienie ukończonych przebiegów przepływu pracy.|Nie wymiarów|
|RunSuccessLatency|Opóźnienie przebiegów zakończonych powodzeniem|Sekundy|Average|Opóźnienie przebiegów przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|RunThrottledEvents|Zdarzenia ograniczenia przebiegu|Count|Łącznie|Liczba zdarzeń związanych ze zdarzeniami przepływu pracy lub wyzwalaczem.|Nie wymiarów|
|RunFailurePercentage|Procent niepowodzeń przebiegów|Percent|Łącznie|Procent przebiegów przepływu pracy zakończonych niepowodzeniem.|Nie wymiarów|
|ActionsStarted|Rozpoczęte akcje |Count|Łącznie|Liczba rozpoczętych akcji przepływu pracy.|Nie wymiarów|
|ActionsCompleted|Ukończone akcje |Count|Łącznie|Liczba ukończonych akcji przepływu pracy.|Nie wymiarów|
|ActionsSucceeded|Akcje zakończone powodzeniem |Count|Łącznie|Liczba akcji przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|ActionsFailed|Akcje zakończone niepowodzeniem|Count|Łącznie|Liczba akcji przepływu pracy zakończonych niepowodzeniem.|Nie wymiarów|
|ActionsSkipped|Pominięte akcje |Count|Łącznie|Liczba pominiętych akcji przepływu pracy.|Nie wymiarów|
|ActionLatency|Opóźnienie akcji |Sekundy|Average|Opóźnienie ukończonych akcji przepływu pracy.|Nie wymiarów|
|ActionSuccessLatency|Opóźnienie akcji – powodzenie |Sekundy|Average|Opóźnienie akcji przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|ActionThrottledEvents|Zdarzenia ograniczenia akcji|Count|Łącznie|Liczba zdarzeń ograniczających przepływ pracy...|Nie wymiarów|
|TriggersStarted|Rozpoczęte wyzwalacze |Count|Łącznie|Liczba uruchomionych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggersCompleted|Ukończone wyzwalacze |Count|Łącznie|Liczba ukończonych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggersSucceeded|Udane wyzwalacze |Count|Łącznie|Liczba wyzwalaczy przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|TriggersFailed|Nieudane wyzwalacze |Count|Łącznie|Liczba wyzwalaczy przepływu pracy zakończonych niepowodzeniem.|Nie wymiarów|
|TriggersSkipped|Pominięte wyzwalacze|Count|Łącznie|Liczba pominiętych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggersFired|Uruchomione wyzwalacze |Count|Łącznie|Liczba wyzwolonych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggerLatency|Opóźnienie wyzwalacza |Sekundy|Average|Opóźnienie ukończonych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggerFireLatency|Opóźnienie uruchomienia wyzwalacza |Sekundy|Average|Opóźnienie wyzwalanych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggerSuccessLatency|Opóźnienie udanego wyzwalacza |Sekundy|Average|Opóźnienie wyzwalaczy przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|TriggerThrottledEvents|Zdarzenia ograniczenia wyzwalacza|Count|Łącznie|Liczba zdarzeń ograniczenia wyzwalacza przepływu pracy.|Nie wymiarów|
|BillableActionExecutions|Rozliczane wykonania akcji|Count|Łącznie|Liczba rozliczanych wykonań akcji przepływu pracy.|Nie wymiarów|
|BillableTriggerExecutions|Rozliczane wykonania wyzwalaczy|Count|Łącznie|Liczba rozliczanych wykonań wyzwalacza przepływu pracy.|Nie wymiarów|
|TotalBillableExecutions|Łączna liczba rozliczanych wykonań|Count|Łącznie|Liczba rozliczanych wykonań przepływu pracy.|Nie wymiarów|
|BillingUsageNativeOperation|Użycie rozliczeń dla natywnych wykonań operacji|Count|Łącznie|Liczba rozliczanych wykonań operacji natywnych.|Nie wymiarów|
|BillingUsageStandardConnector|Użycie rozliczeń dla wykonywania łączników standardowych|Count|Łącznie|Liczba rozliczanych wykonań łączników standardowych.|Nie wymiarów|
|BillingUsageStorageConsumption|Użycie rozliczeń dla wykonań zużycia magazynu|Count|Łącznie|Liczba rozliczanych wykonań zużycia magazynu.|Nie wymiarów|
|BillingUsageNativeOperation|Użycie rozliczeń dla natywnych wykonań operacji|Count|Łącznie|Liczba rozliczanych wykonań operacji natywnych.|Nie wymiarów|
|BillingUsageStandardConnector|Użycie rozliczeń dla wykonywania łączników standardowych|Count|Łącznie|Liczba rozliczanych wykonań łączników standardowych.|Nie wymiarów|
|BillingUsageStorageConsumption|Użycie rozliczeń dla wykonań zużycia magazynu|Count|Łącznie|Liczba rozliczanych wykonań zużycia magazynu.|Nie wymiarów|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RunsStarted|Przebiegi rozpoczęte|Count|Łącznie|Liczba uruchomionych przebiegów przepływu pracy.|Nie wymiarów|
|RunsCompleted|Przebiegi ukończone|Count|Łącznie|Liczba ukończonych przebiegów przepływu pracy.|Nie wymiarów|
|RunsSucceeded|Przebiegi zakończone powodzeniem|Count|Łącznie|Liczba przebiegów przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|RunsFailed|Przebiegi zakończone niepowodzeniem|Count|Łącznie|Liczba przebiegów przepływu pracy zakończonych niepowodzeniem.|Nie wymiarów|
|RunsCancelled|Przebiegi anulowane|Count|Łącznie|Liczba anulowanych przebiegów przepływu pracy.|Nie wymiarów|
|RunLatency|Opóźnienie przebiegów|Sekundy|Average|Opóźnienie ukończonych przebiegów przepływu pracy.|Nie wymiarów|
|RunSuccessLatency|Opóźnienie przebiegów zakończonych powodzeniem|Sekundy|Average|Opóźnienie przebiegów przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|RunThrottledEvents|Zdarzenia ograniczenia przebiegu|Count|Łącznie|Liczba zdarzeń związanych ze zdarzeniami przepływu pracy lub wyzwalaczem.|Nie wymiarów|
|RunStartThrottledEvents|Zdarzenia ograniczenia rozpoczęcia przebiegu|Count|Łącznie|Liczba uruchomionych zdarzeń ograniczających przepływ pracy.|Nie wymiarów|
|RunFailurePercentage|Procent niepowodzeń przebiegów|Percent|Łącznie|Procent przebiegów przepływu pracy zakończonych niepowodzeniem.|Nie wymiarów|
|ActionsStarted|Rozpoczęte akcje |Count|Łącznie|Liczba rozpoczętych akcji przepływu pracy.|Nie wymiarów|
|ActionsCompleted|Ukończone akcje |Count|Łącznie|Liczba ukończonych akcji przepływu pracy.|Nie wymiarów|
|ActionsSucceeded|Akcje zakończone powodzeniem |Count|Łącznie|Liczba akcji przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|ActionsFailed|Akcje zakończone niepowodzeniem |Count|Łącznie|Liczba akcji przepływu pracy zakończonych niepowodzeniem.|Nie wymiarów|
|ActionsSkipped|Pominięte akcje |Count|Łącznie|Liczba pominiętych akcji przepływu pracy.|Nie wymiarów|
|ActionLatency|Opóźnienie akcji |Sekundy|Average|Opóźnienie ukończonych akcji przepływu pracy.|Nie wymiarów|
|ActionSuccessLatency|Opóźnienie akcji – powodzenie |Sekundy|Average|Opóźnienie akcji przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|ActionThrottledEvents|Zdarzenia ograniczenia akcji|Count|Łącznie|Liczba zdarzeń ograniczających przepływ pracy...|Nie wymiarów|
|TriggersStarted|Rozpoczęte wyzwalacze |Count|Łącznie|Liczba uruchomionych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggersCompleted|Ukończone wyzwalacze |Count|Łącznie|Liczba ukończonych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggersSucceeded|Udane wyzwalacze |Count|Łącznie|Liczba wyzwalaczy przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|TriggersFailed|Nieudane wyzwalacze |Count|Łącznie|Liczba wyzwalaczy przepływu pracy zakończonych niepowodzeniem.|Nie wymiarów|
|TriggersSkipped|Pominięte wyzwalacze|Count|Łącznie|Liczba pominiętych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggersFired|Uruchomione wyzwalacze |Count|Łącznie|Liczba wyzwolonych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggerLatency|Opóźnienie wyzwalacza |Sekundy|Average|Opóźnienie ukończonych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggerFireLatency|Opóźnienie uruchomienia wyzwalacza |Sekundy|Average|Opóźnienie wyzwalanych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggerSuccessLatency|Opóźnienie udanego wyzwalacza |Sekundy|Average|Opóźnienie wyzwalaczy przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|TriggerThrottledEvents|Zdarzenia ograniczenia wyzwalacza|Count|Łącznie|Liczba zdarzeń ograniczenia wyzwalacza przepływu pracy.|Nie wymiarów|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Użycie procesora przepływu pracy dla środowisko usługi integracji|Percent|Average|Użycie procesora przez przepływ pracy dla środowiska usługi integracji.|Nie wymiarów|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Użycie pamięci przez przepływ pracy dla środowisko usługi integracji|Percent|Average|Użycie pamięci przez przepływ pracy dla środowiska usługi integracji.|Nie wymiarów|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Użycie procesora przez łącznik dla środowisko usługi integracji|Percent|Average|Użycie procesora przez łącznik dla środowiska usługi integracji.|Nie wymiarów|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Użycie pamięci przez łącznik dla środowisko usługi integracji|Percent|Average|Użycie pamięci przez łącznik dla środowiska usługi integracji.|Nie wymiarów|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Ukończone uruchomienia|Ukończone uruchomienia|Count|Łącznie|Liczba przebiegów pomyślnie ukończonych dla tego obszaru roboczego|Scenariusz|
|Uruchomione uruchomienia|Uruchomione uruchomienia|Count|Łącznie|Liczba rozpoczętych uruchomień dla tego obszaru roboczego|Scenariusz|
|Nieudane uruchomienia|Nieudane uruchomienia|Count|Łącznie|Liczba uruchomień dla tego obszaru roboczego nie powiodła się|Scenariusz|

## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/konta

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Użycie|Użycie|Count|Count|Liczba wywołań interfejsu API|ApiCategory, ApiName, ResultType, ResponseCode|
|Dostępność|Dostępność|Procent|Average|Dostępność interfejsów API|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AverageOtherLatency|Średnie inne opóźnienie|ms/op|Average|Średnie inne opóźnienie (bez odczytu lub zapisu) w milisekundach na operację|Nie wymiarów|
|AverageReadLatency|Średnie opóźnienie odczytu|ms/op|Average|Średnie opóźnienie odczytu w milisekundach na operację|Nie wymiarów|
|AverageTotalLatency|Średnie opóźnienie całkowite|ms/op|Average|Średnie całkowite opóźnienie (w milisekundach) na operację|Nie wymiarów|
|AverageWriteLatency|Średnie opóźnienie zapisu|ms/op|Average|Średnie opóźnienie zapisu w milisekundach na operację|Nie wymiarów|
|FilesystemOtherOps|Inne operacje dotyczące systemu plików|kompresowania|Average|Liczba innych operacji w systemie plików (które nie są odczytywane lub zapisywane)|Nie wymiarów|
|FilesystemReadOps|Operacje odczytu dla systemu plików|kompresowania|Average|Liczba operacji odczytu z systemu plików|Nie wymiarów|
|FilesystemTotalOps|Łączna liczba operacji dla systemu plików|kompresowania|Average|Suma wszystkich operacji systemu plików|Nie wymiarów|
|FilesystemWriteOps|Operacje zapisu dla systemu plików|kompresowania|Average|Liczba operacji zapisu systemu plików|Nie wymiarów|
|IoBytesPerOtherOps|Bajty we/wy na inne operacje|bajty/op|Average|Liczba bajtów in/out na inne operacje (które nie są odczytywane lub zapisu)|Nie wymiarów|
|IoBytesPerReadOps|Bajty we/wy na operacje odczytu|bajty/op|Average|Liczba bajtów wejściowych/out na operację odczytu|Nie wymiarów|
|IoBytesPerTotalOps|Bajty we/wy na operację we wszystkich operacjach|bajty/op|Average|Suma wszystkich operacji we/out bajtów|Nie wymiarów|
|IoBytesPerWriteOps|Bajty we/wy na operacje zapisu|bajty/op|Average|Liczba bajtów we/out na operację zapisu|Nie wymiarów|
|OtherIops|Inne operacje we/wy|operacje na sekundę|Average|Inne operacje we/wychodzące na sekundę|Nie wymiarów|
|OtherThroughput|Inna przepływność|MB/s|Average|Inna przepływność (nie jest odczytywana ani zapisu) w megabajtach na sekundę|Nie wymiarów|
|ReadIops|Odczyt operacji we/wy|operacje na sekundę|Average|Operacje odczytu/out na sekundę|Nie wymiarów|
|ReadThroughput|Przepływność odczytu|MB/s|Average|Przepływność odczytu (w megabajtach) na sekundę|Nie wymiarów|
|TotalIops|Całkowita liczba operacji we/wy|operacje na sekundę|Average|Suma wszystkich operacji we/wychodzącym na sekundę|Nie wymiarów|
|TotalThroughput|Łączna przepływność|MB/s|Average|Suma całej przepływności w megabajtach na sekundę|Nie wymiarów|
|VolumeAllocatedSize|Rozmiar przydzielony woluminu|B|Average|Przydzielony rozmiar woluminu (nie są to rzeczywiste używane bajty)|Nie wymiarów|
|VolumeLogicalSize|Rozmiar logiczny woluminu|B|Average|Rozmiar logiczny woluminu (zużyte bajty)|Nie wymiarów|
|VolumeSnapshotSize|Rozmiar migawki woluminu|B|Average|Rozmiar wszystkich migawek w woluminie|Nie wymiarów|
|WriteIops|Zapisz operacje we/wy|operacje na sekundę|Average|Operacje zapisu/wylogowania na sekundę|Nie wymiarów|
|WriteThroughput|Przepływność zapisu|MB/s|Average|Przepływność zapisu w megabajtach na sekundę|Nie wymiarów|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Rozmiar przydzielony puli woluminów|B|Average|Przydzielony rozmiar puli (nie są to rzeczywiste używane bajty)|Nie wymiarów|
|VolumePoolAllocatedUsed|Użyte przydzieloną pulę woluminów|B|Average|Przydzielono używany rozmiar puli|Nie wymiarów|
|VolumePoolTotalLogicalSize|Łączny rozmiar logiczny puli woluminów|B|Average|Suma rozmiaru logicznego wszystkich woluminów należących do puli|Nie wymiarów|
|VolumePoolTotalSnapshotSize|Łączny rozmiar migawki puli woluminów|B|Average|Suma wszystkich migawek w puli|Nie wymiarów|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BytesSentRate|Bajty wysłane|Count|Łącznie|Liczba bajtów wysłanych przez interfejs sieciowy|Nie wymiarów|
|BytesReceivedRate|Bajty odebrane|Count|Łącznie|Liczba bajtów odebranych przez interfejs sieciowy|Nie wymiarów|
|PacketsSentRate|Wysłane pakiety|Count|Łącznie|Liczba pakietów wysłanych przez interfejs sieciowy|Nie wymiarów|
|PacketsReceivedRate|Odebrane pakiety|Count|Łącznie|Liczba pakietów odebranych przez interfejs sieciowy|Nie wymiarów|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|VipAvailability|Dostępność ścieżki danych|Count|Average|Średnia dostępność ścieżki danych Load Balancer na czas trwania|FrontendIPAddress, FrontendPort|
|DipAvailability|Stan sondy kondycji|Count|Average|Średni stan sondy kondycji Load Balancer na czas trwania|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Liczba bajtów|Count|Łącznie|Łączna liczba bajtów przesłanych w okresie|FrontendIPAddress, FrontendPort, kierunek|
|PacketCount|Liczba pakietów|Count|Łącznie|Łączna liczba pakietów wysłanych w czasie|FrontendIPAddress, FrontendPort, kierunek|
|SYNCount|Liczba SYN|Count|Łącznie|Łączna liczba pakietów SYN wysłanych w czasie|FrontendIPAddress, FrontendPort, kierunek|
|SnatConnectionCount|Liczba połączeń z translatorem adresów sieciowych|Count|Łącznie|Łączna liczba nowych połączeń z przyłączaniem do adresów w czasie|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Przydzielono porty przydziałów (wersja zapoznawcza)|Count|Łącznie|Łączna liczba portów przydzieloną porty adresów sieciowych w okresie|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Używane porty (wersja zapoznawcza)|Count|Łącznie|Łączna liczba portów przydziałów adresów sieciowych używanych w czasie|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QueryVolume|Wolumin zapytania|Count|Łącznie|Liczba zapytań obsłużonych dla strefy DNS|Nie wymiarów|
|RecordSetCount|Liczba zestawów rekordów|Count|Maksimum|Liczba zestawów rekordów w strefie DNS|Nie wymiarów|
|RecordSetCapacityUtilization|Wykorzystanie pojemności zestawu rekordów|Percent|Maksimum|Procent pojemności zestawu rekordów używany przez strefę DNS|Nie wymiarów|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PacketsInDDoS|Pakiety przychodzące DDoS|CountPerSecond|Maksimum|Pakiety przychodzące DDoS|Nie wymiarów|
|PacketsDroppedDDoS|Odrzucone pakiety przychodzące DDoS|CountPerSecond|Maksimum|Odrzucone pakiety przychodzące DDoS|Nie wymiarów|
|PacketsForwardedDDoS|Przekazane pakiety przychodzące DDoS|CountPerSecond|Maksimum|Przekazane pakiety przychodzące DDoS|Nie wymiarów|
|TCPPacketsInDDoS|Przychodzące pakiety TCP DDoS|CountPerSecond|Maksimum|Przychodzące pakiety TCP DDoS|Nie wymiarów|
|TCPPacketsDroppedDDoS|Liczba porzuconych pakietów TCP przychodzących DDoS|CountPerSecond|Maksimum|Liczba porzuconych pakietów TCP przychodzących DDoS|Nie wymiarów|
|TCPPacketsForwardedDDoS|Przychodzące pakiety TCP przesłane dalej DDoS|CountPerSecond|Maksimum|Przychodzące pakiety TCP przesłane dalej DDoS|Nie wymiarów|
|UDPPacketsInDDoS|Przychodzące pakiety UDP DDoS|CountPerSecond|Maksimum|Przychodzące pakiety UDP DDoS|Nie wymiarów|
|UDPPacketsDroppedDDoS|Liczba porzuconych pakietów przychodzących UDP DDoS|CountPerSecond|Maksimum|Liczba porzuconych pakietów przychodzących UDP DDoS|Nie wymiarów|
|UDPPacketsForwardedDDoS|Przychodzące pakiety UDP DDoS przesłane dalej|CountPerSecond|Maksimum|Przychodzące pakiety UDP DDoS przesłane dalej|Nie wymiarów|
|BytesInDDoS|Bajty przychodzące DDoS|BytesPerSecond|Maksimum|Bajty przychodzące DDoS|Nie wymiarów|
|BytesDroppedDDoS|Bajty przychodzące opuszczone DDoS|BytesPerSecond|Maksimum|Bajty przychodzące opuszczone DDoS|Nie wymiarów|
|BytesForwardedDDoS|Przekazane bajty przychodzące DDoS|BytesPerSecond|Maksimum|Przekazane bajty przychodzące DDoS|Nie wymiarów|
|TCPBytesInDDoS|Przychodzące bajty TCP DDoS|BytesPerSecond|Maksimum|Przychodzące bajty TCP DDoS|Nie wymiarów|
|TCPBytesDroppedDDoS|Liczba porzuconych bajtów przychodzących protokołu TCP DDoS|BytesPerSecond|Maksimum|Liczba porzuconych bajtów przychodzących protokołu TCP DDoS|Nie wymiarów|
|TCPBytesForwardedDDoS|Przekazane DDoS przychodzące bajty TCP|BytesPerSecond|Maksimum|Przekazane DDoS przychodzące bajty TCP|Nie wymiarów|
|UDPBytesInDDoS|Przychodzące bajty UDP DDoS|BytesPerSecond|Maksimum|Przychodzące bajty UDP DDoS|Nie wymiarów|
|UDPBytesDroppedDDoS|Liczba porzuconych bajtów przychodzących UDP DDoS|BytesPerSecond|Maksimum|Liczba porzuconych bajtów przychodzących UDP DDoS|Nie wymiarów|
|UDPBytesForwardedDDoS|Przekazane przychodzące bajty UDP DDoS|BytesPerSecond|Maksimum|Przekazane przychodzące bajty UDP DDoS|Nie wymiarów|
|IfUnderDDoSAttack|W obszarze atak DDoS|Count|Maksimum|W obszarze atak DDoS|Nie wymiarów|
|DDoSTriggerTCPPackets|Przychodzące pakiety TCP do wyzwalania łagodzenia DDoS|CountPerSecond|Maksimum|Przychodzące pakiety TCP do wyzwalania łagodzenia DDoS|Nie wymiarów|
|DDoSTriggerUDPPackets|Przychodzące pakiety UDP do wyzwalania łagodzenia DDoS|CountPerSecond|Maksimum|Przychodzące pakiety UDP do wyzwalania łagodzenia DDoS|Nie wymiarów|
|DDoSTriggerSYNPackets|Pakiety przychodzących SYN wyzwalające łagodzenie DDoS|CountPerSecond|Maksimum|Pakiety przychodzących SYN wyzwalające łagodzenie DDoS|Nie wymiarów|
|VipAvailability|Dostępność ścieżki danych|Count|Average|Średnia dostępność adresów IP na czas trwania|Port|
|ByteCount|Liczba bajtów|Count|Łącznie|Łączna liczba bajtów przesłanych w okresie|Port, kierunek|
|PacketCount|Liczba pakietów|Count|Łącznie|Łączna liczba pakietów wysłanych w czasie|Port, kierunek|
|SynCount|Liczba SYN|Count|Łącznie|Łączna liczba pakietów SYN wysłanych w czasie|Port, kierunek|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ApplicationRuleHit|Liczba trafień reguł aplikacji|Count|Łącznie|Liczba trafień reguł aplikacji|Stan, powód, protokół|
|NetworkRuleHit|Liczba trafień reguł sieci|Count|Łącznie|Liczba trafień reguł sieci|Stan, powód, protokół|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Przepływność|Przepływność|BytesPerSecond|Łącznie|Liczba bajtów na sekundę obsłużonych przez Application Gateway|Nie wymiarów|
|UnhealthyHostCount|Liczba hostów w złej kondycji|Count|Average|Liczba hostów zaplecza w złej kondycji|BackendSettingsPool|
|HealthyHostCount|Liczba hostów w dobrej kondycji|Count|Average|Liczba hostów zaplecza w dobrej kondycji|BackendSettingsPool|
|TotalRequests|Łączna liczba żądań|Count|Łącznie|Liczba pomyślnych żądań obsłużonych przez Application Gateway|BackendSettingsPool|
|FailedRequests|Żądania zakończone niepowodzeniem|Count|Łącznie|Liczba żądań zakończonych niepowodzeniem, które zostały obsłużone przez Application Gateway|BackendSettingsPool|
|ResponseStatus|Stan odpowiedzi|Count|Łącznie|Stan odpowiedzi HTTP zwrócony przez Application Gateway|HttpStatusGroup|
|Wartości CurrentConnections|Bieżące połączenia|Count|Łącznie|Liczba bieżących połączeń ustanowionych z Application Gateway|Nie wymiarów|
|CapacityUnits|Bieżące jednostki wydajności|Count|Average|Zużyte jednostki wydajności|Nie wymiarów|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AverageBandwidth|Przepustowość S2S bramy|BytesPerSecond|Average|Średnia przepustowość lokacja-lokacja bramy w bajtach na sekundę|Nie wymiarów|
|P2SBandwidth|Przepustowość P2S bramy|BytesPerSecond|Average|Średnia przepustowość typu punkt-lokacja bramy w bajtach na sekundę|Nie wymiarów|
|P2SConnectionCount|Liczba połączeń P2S|Count|Maksimum|Liczba połączeń punkt-lokacja bramy|Protocol|
|TunnelAverageBandwidth|Przepustowość tunelu|BytesPerSecond|Average|Średnia przepustowość tunelu w bajtach na sekundę|ConnectionName, RemoteIP|
|TunnelEgressBytes|Bajty wychodzące tunelu|Bajty|Łącznie|Wychodzące bajty tunelu|ConnectionName, RemoteIP|
|TunnelIngressBytes|Bajty transferu danych wejściowych tunelu|Bajty|Łącznie|Przychodzące bajty tunelu|ConnectionName, RemoteIP|
|TunnelEgressPackets|Pakiety wychodzące tuneli|Count|Łącznie|Liczba wychodzących pakietów tunelu|ConnectionName, RemoteIP|
|TunnelIngressPackets|Pakiety przychodzące tunelu|Count|Łącznie|Liczba przychodzących pakietów tunelu|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Porzucanie pakietów wychodzących przez tunelowanie|Count|Łącznie|Liczba porzucenia pakietów wychodzących z niezgodności selektora ruchu tunelu|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Wyrzucanie niezgodności pakietów przez tunelowanie|Count|Łącznie|Liczba porzucenia pakietów przychodzących z niezgodności selektora ruchu tunelu|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Usługa BITS na sekundę na platformie Azure|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Ruch przychodzący z platformy Azure na sekundę|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/expressRouteCircuits/Komunikacja równorzędna

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Usługa BITS na sekundę na platformie Azure|Nie wymiarów|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Ruch przychodzący z platformy Azure na sekundę|Nie wymiarów|

## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Usługa BITS na sekundę na platformie Azure|Nie wymiarów|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Ruch przychodzący z platformy Azure na sekundę|Nie wymiarów|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QpsByEndpoint|Zwrócone zapytania według punktu końcowego|Count|Łącznie|Liczba zwróconych punktów końcowych Traffic Manager w danym przedziale czasu|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Stan punktu końcowego według punktu końcowego|Count|Maksimum|1 Jeśli sonda punktu końcowego ma stan "włączone", 0 w przeciwnym razie.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Sond nie powiodło się|Percent|Average|% sond monitorowania łączności nie powiodło się|Nie wymiarów|
|AverageRoundtripMs|Średni Czas błądzenia (MS)|MilliSeconds|Average|Średni czas błądzenia sieci (MS) dla sond monitorowania łączności przesyłanych między źródłem a miejscem docelowym|Nie wymiarów|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/usługi frontdoor

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RequestCount|Liczba żądań|Count|Łącznie|Liczba żądań klientów obsłużonych przez serwer proxy HTTP/S|Wartości httpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Rozmiar żądania|Bajty|Łącznie|Liczba bajtów wysłanych jako żądania od klientów do serwera proxy HTTP/S|Wartości httpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Rozmiar odpowiedzi|Bajty|Łącznie|Liczba bajtów wysłanych jako odpowiedzi z serwera proxy HTTP/S do klientów|Wartości httpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Liczba żądań wewnętrznej bazy danych|Count|Łącznie|Liczba żądań wysyłanych z serwera proxy HTTP/S do frontonu|Wartości httpStatus, HttpStatusGroup, zaplecze|
|BackendRequestLatency|Opóźnienie żądania wewnętrznej bazy danych|MilliSeconds|Average|Czas obliczony od momentu wysłania żądania przez serwer proxy HTTP/S do zaplecza do momentu odebrania przez serwer proxy HTTP/S ostatniego bajtu odpowiedzi z zaplecza|Zaplecze|
|TotalLatency|Łączne opóźnienie|MilliSeconds|Average|Czas obliczony od momentu odebrania żądania klienta przez serwer proxy HTTP/S do momentu potwierdzenia przez klienta ostatniego bajtu odpowiedzi z serwera proxy HTTP/S|Wartości httpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Procent kondycji zaplecza|Percent|Average|Procent pomyślnych sond kondycji z serwera proxy HTTP/S do frontonu|Zaplecze, ustawień httpsettings elementu|
|WebApplicationFirewallRequestCount|Liczba żądań zapory aplikacji sieci Web|Count|Łącznie|Liczba żądań klientów przetworzonych przez zaporę aplikacji sieci Web|PolicyName, RuleName, Akcja|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Rejestracja. wszystkie|Operacje rejestracji|Count|Łącznie|Liczba wszystkich operacji rejestracji zakończonych powodzeniem (Tworzenie aktualizacji zapytań i usunięć). |Nie wymiarów|
|registration.create|Operacje tworzenia rejestracji|Count|Łącznie|Liczba wszystkich operacji tworzenia rejestracji zakończonych powodzeniem.|Nie wymiarów|
|registration.update|Operacje aktualizacji operacji|Count|Łącznie|Liczba wszystkich aktualizacji rejestracji zakończonych powodzeniem.|Nie wymiarów|
|registration.get|Operacje odczytu operacji|Count|Łącznie|Liczba wszystkich zapytań rejestracji zakończonych powodzeniem.|Nie wymiarów|
|registration.delete|Operacje usuwania rejestracji|Count|Łącznie|Liczba wszystkich operacji usunięcia rejestracji zakończonych powodzeniem.|Nie wymiarów|
|przychodzące|Wiadomości przychodzące|Count|Łącznie|Liczba wszystkich pomyślnie wysłanych wywołań interfejsu API. |Nie wymiarów|
|incoming.scheduled|Wysłano zaplanowane powiadomienia wypychane|Count|Łącznie|Zaplanowane powiadomienia wypychane zostały anulowane|Nie wymiarów|
|incoming.scheduled.cancel|Zaplanowane powiadomienia wypychane zostały anulowane|Count|Łącznie|Zaplanowane powiadomienia wypychane zostały anulowane|Nie wymiarów|
|scheduled.pending|Oczekujące zaplanowane powiadomienia|Count|Łącznie|Oczekujące zaplanowane powiadomienia|Nie wymiarów|
|Instalacja. wszystkie|Operacje zarządzania instalacją|Count|Łącznie|Operacje zarządzania instalacją|Nie wymiarów|
|Instalacja. Get|Pobierz operacje instalacji|Count|Łącznie|Pobierz operacje instalacji|Nie wymiarów|
|Instalacja. upsert|Utwórz lub zaktualizuj operacje instalacji|Count|Łącznie|Utwórz lub zaktualizuj operacje instalacji|Nie wymiarów|
|installation.patch|Operacje instalacji poprawki|Count|Łącznie|Operacje instalacji poprawki|Nie wymiarów|
|installation.delete|Usuń operacje instalacji|Count|Łącznie|Usuń operacje instalacji|Nie wymiarów|
|outgoing.allpns.success|Powiadomienia zakończone powodzeniem|Count|Łącznie|Liczba wszystkich udanych powiadomień.|Nie wymiarów|
|wychodzący. allpns. invalidpayload|Błędy ładunku|Count|Łącznie|Liczba wypchnięciów zakończonych niepowodzeniem, ponieważ PNS zwrócił zły błąd ładunku.|Nie wymiarów|
|outgoing.allpns.pnserror|Błędy zewnętrznych systemów powiadamiania|Count|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ wystąpił problem z komunikacją z PNS (wyklucza problemy z uwierzytelnianiem).|Nie wymiarów|
|outgoing.allpns.channelerror|Błędy kanału|Count|Łącznie|Liczba wypchnięciów zakończonych niepowodzeniem, ponieważ kanał był nieprawidłowy, nie jest skojarzony z poprawną aplikacją lub wygasłą.|Nie wymiarów|
|outgoing.allpns.badorexpiredchannel|Błędy nieprawidłowych lub wygasłych kanałów|Count|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ kanał/token/Identyfikator rejestracji w rejestracji został wygasły lub nieprawidłowy.|Nie wymiarów|
|outgoing.wns.success|Powiadomienia usługi WNS zakończone powodzeniem|Count|Łącznie|Liczba wszystkich udanych powiadomień.|Nie wymiarów|
|wychodzący. WNS. invalidcredentials|WNS błędy autoryzacji (Nieprawidłowe poświadczenia)|Count|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń lub poświadczenia są zablokowane. (Usługa Windows Live nie rozpoznaje poświadczeń).|Nie wymiarów|
|outgoing.wns.badchannel|Błąd nieprawidłowego kanału usługi WNS|Count|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ identyfikator channeluri w rejestracji nie został rozpoznany (stan WNS: nie znaleziono 404).|Nie wymiarów|
|outgoing.wns.expiredchannel|Błąd wygasłego kanału usługi WNS|Count|Łącznie|Liczba wypchnięciów zakończonych niepowodzeniem z powodu wygaśnięcia identyfikator channeluri (stan WNS: 410).|Nie wymiarów|
|wychodzące. WNS. dławienia|Powiadomienia usługi WNS z ograniczoną przepływnością|Count|Łącznie|Liczba wypchnięciów zakończonych niepowodzeniem, ponieważ WNS ogranicza tę aplikację (stan WNS: 406 nie akceptowalny).|Nie wymiarów|
|outgoing.wns.tokenproviderunreachable|Błędy autoryzacji WNS (nieosiągalne)|Count|Łącznie|Usługa Windows Live jest nieosiągalna.|Nie wymiarów|
|outgoing.wns.invalidtoken|Błędy autoryzacji WNS (nieprawidłowy token)|Count|Łącznie|Token dostarczony do WNS jest nieprawidłowy (stan WNS: 401).|Nie wymiarów|
|outgoing.wns.wrongtoken|WNS błędy autoryzacji (nieprawidłowy token)|Count|Łącznie|Token dostarczony do WNS jest prawidłowy, ale dla innej aplikacji (stan WNS: 403 — Dostęp zabroniony). Może się tak zdarzyć, jeśli identyfikator channeluri w rejestracji jest skojarzony z inną aplikacją. Sprawdź, czy aplikacja kliencka jest skojarzona z tą samą aplikacją, której poświadczenia znajdują się w centrum powiadomień.|Nie wymiarów|
|outgoing.wns.invalidnotificationformat|Nieprawidłowy format powiadomienia usługi WNS|Count|Łącznie|Format powiadomienia jest nieprawidłowy (stan WNS: 400). Należy pamiętać, że WNS nie odrzuca wszystkich nieprawidłowych ładunków.|Nie wymiarów|
|outgoing.wns.invalidnotificationsize|Błąd nieprawidłowego rozmiaru powiadomienia usługi WNS|Count|Łącznie|Ładunek powiadomienia jest zbyt duży (stan WNS: 413).|Nie wymiarów|
|outgoing.wns.channelthrottled|Ograniczono przepływność kanału usługi WNS|Count|Łącznie|Powiadomienie zostało porzucone, ponieważ identyfikator channeluri w rejestracji jest ograniczany (nagłówek odpowiedzi WNS: X-WNS-NotificationStatus:channelThrottled).|Nie wymiarów|
|outgoing.wns.channeldisconnected|Rozłączono kanał usługi WNS|Count|Łącznie|Powiadomienie zostało porzucone, ponieważ identyfikator channeluri w rejestracji jest ograniczany (nagłówek odpowiedzi WNS: X-WNS-DeviceConnectionStatus: odłączono.|Nie wymiarów|
|outgoing.wns.dropped|Porzucone powiadomienia usługi WNS|Count|Łącznie|Powiadomienie zostało porzucone, ponieważ identyfikator channeluri w rejestracji jest ograniczany (X-WNS-NotificationStatus: porzucone, ale nie X-WNS-DeviceConnectionStatus: disconnected).|Nie wymiarów|
|outgoing.wns.pnserror|Błędy usługi WNS|Count|Łącznie|Powiadomienie nie zostało dostarczone z powodu błędów komunikacji z WNS.|Nie wymiarów|
|outgoing.wns.authenticationerror|Błędy uwierzytelniania usługi WNS|Count|Łącznie|Powiadomienie nie zostało dostarczone z powodu błędów komunikacji z nieprawidłowymi poświadczeniami usługi Windows Live lub nieprawidłowym tokenem.|Nie wymiarów|
|outgoing.apns.success|Powiadomienia usługi APNS zakończone powodzeniem|Count|Łącznie|Liczba wszystkich udanych powiadomień.|Nie wymiarów|
|outgoing.apns.invalidcredentials|Błędy autoryzacji usługi APNS|Count|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń lub poświadczenia są zablokowane.|Nie wymiarów|
|outgoing.apns.badchannel|Błąd nieprawidłowego kanału usługi APNS|Count|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ token jest nieprawidłowy (kod stanu protokołu binarnego usługi APNS: 8. Kod stanu protokołu HTTP usługi APNS: 400 z "BadDeviceToken").|Nie wymiarów|
|outgoing.apns.expiredchannel|Błąd nieprawidłowego kanału usługi APNS|Count|Łącznie|Liczba tokenów unieważnionych przez kanał opinii usługi APNS.|Nie wymiarów|
|outgoing.apns.invalidnotificationsize|Błąd nieprawidłowego rozmiaru powiadomienia usługi APNS|Count|Łącznie|Liczba wypchnięciów zakończonych niepowodzeniem ze względu na to, że ładunek jest zbyt duży (kod stanu protokołu binarnego usługi APNS: 7).|Nie wymiarów|
|outgoing.apns.pnserror|Błędy usługi APNS|Count|Łącznie|Liczba wypchnięciów zakończonych niepowodzeniem z powodu błędów komunikacji z usługą APNS.|Nie wymiarów|
|outgoing.gcm.success|Powiadomienia usługi GCM zakończone powodzeniem|Count|Łącznie|Liczba wszystkich udanych powiadomień.|Nie wymiarów|
|outgoing.gcm.invalidcredentials|GCM błędy autoryzacji (Nieprawidłowe poświadczenia)|Count|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń lub poświadczenia są zablokowane.|Nie wymiarów|
|outgoing.gcm.badchannel|Błąd nieprawidłowego kanału usługi GCM|Count|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ identyfikator rejestracji w rejestracji nie został rozpoznany (wynik GCM: Nieprawidłowa rejestracja).|Nie wymiarów|
|outgoing.gcm.expiredchannel|Błąd wygasłego kanału usługi GCM|Count|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem z powodu wygaśnięcia Identyfikator rejestracji w rejestracji (wynik GCM: NotRegistered).|Nie wymiarów|
|outgoing.gcm.throttled|Powiadomienia usługi GCM z ograniczoną przepływnością|Count|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ GCM ograniczenie tej aplikacji (kod stanu GCM: 501-599 lub wynik: niedostępne).|Nie wymiarów|
|outgoing.gcm.invalidnotificationformat|Nieprawidłowy format powiadomienia usługi GCM|Count|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ ładunek nie został poprawnie sformatowany (wynik GCM: InvalidDataKey lub InvalidTtl).|Nie wymiarów|
|outgoing.gcm.invalidnotificationsize|Błąd nieprawidłowego rozmiaru powiadomienia usługi GCM|Count|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ ładunek jest zbyt duży (wynik GCM: MessageTooBig).|Nie wymiarów|
|outgoing.gcm.wrongchannel|Błąd nieprawidłowego kanału usługi GCM|Count|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ identyfikator rejestracji w rejestracji nie jest skojarzony z bieżącą aplikacją (wynik GCM: InvalidPackageName).|Nie wymiarów|
|outgoing.gcm.pnserror|Błędy usługi GCM|Count|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem z powodu błędów komunikacji z GCM.|Nie wymiarów|
|outgoing.gcm.authenticationerror|Błędy uwierzytelniania usługi GCM|Count|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń, a SenderId nie został poprawnie skonfigurowany w aplikacji (wynik GCM: MismatchedSenderId).|Nie wymiarów|
|outgoing.mpns.success|Powiadomienia usługi MPNS zakończone powodzeniem|Count|Łącznie|Liczba wszystkich udanych powiadomień.|Nie wymiarów|
|outgoing.mpns.invalidcredentials|Nieprawidłowe poświadczenia usługi MPNS|Count|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń lub poświadczenia są zablokowane.|Nie wymiarów|
|outgoing.mpns.badchannel|Błąd nieprawidłowego kanału usługi MPNS|Count|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ identyfikator channeluri w rejestracji nie został rozpoznany (stan usługi MPNS: nie znaleziono 404).|Nie wymiarów|
|wychodzące. usługi MPNS. dławienia|Powiadomienia usługi MPNS z ograniczoną przepływnością|Count|Łącznie|Liczba wypchnięciów zakończonych niepowodzeniem, ponieważ usługi MPNS ogranicza tę aplikację (WNS usługi MPNS: 406 nie akceptowalny).|Nie wymiarów|
|outgoing.mpns.invalidnotificationformat|Nieprawidłowy format powiadomienia usługi MPNS|Count|Łącznie|Liczba wypchnięciów zakończonych niepowodzeniem, ponieważ ładunek powiadomienia był zbyt duży.|Nie wymiarów|
|outgoing.mpns.channeldisconnected|Rozłączono kanał usługi MPNS|Count|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ identyfikator channeluri w rejestracji został rozłączony (stan usługi MPNS: nie znaleziono 412).|Nie wymiarów|
|outgoing.mpns.dropped|Porzucone powiadomienia usługi MPNS|Count|Łącznie|Liczba wypchnięciów, które zostały porzucone przez usługi MPNS (nagłówek odpowiedzi usługi MPNS: X-NotificationStatus: QueueFull lub pomijane).|Nie wymiarów|
|outgoing.mpns.pnserror|Błędy usługi MPNS|Count|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem z powodu błędów komunikacji z usługi MPNS.|Nie wymiarów|
|outgoing.mpns.authenticationerror|Błędy uwierzytelniania usługi MPNS|Count|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń lub poświadczenia są zablokowane.|Nie wymiarów|
|notificationhub.pushes|Wszystkie powiadomienia wychodzące|Count|Łącznie|Wszystkie powiadomienia wychodzące centrum powiadomień|Nie wymiarów|
|incoming.all.requests|Wszystkie żądania przychodzące|Count|Łącznie|Łączna liczba żądań przychodzących dla centrum powiadomień|Nie wymiarów|
|przychodzące. ALL. failedrequests|Wszystkie nieudane żądania przychodzące|Count|Łącznie|Łączna liczba nieudanych żądań przychodzących dla centrum powiadomień|Nie wymiarów|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Average_% bezpłatne węzłów i|% Wolnego węzłów i|Count|Average|Average_% bezpłatne węzłów i|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Wolne miejsce w Average_%|% Wolnego miejsca|Count|Average|Wolne miejsce w Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% węzłów i użycia|% Użytych węzłów i|Count|Average|Average_% węzłów i użycia|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ zajęte miejsce (%)|Procent wykorzystania miejsca|Count|Average|Average_ zajęte miejsce (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bajty odczytu Average_Disk/s|Bajty odczytu z dysku/s|Count|Average|Bajty odczytu Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odczyty Average_Disk/s|Odczyty dysku/s|Count|Average|Odczyty Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk transfery/s|Transfery dyskowe/s|Count|Average|Average_Disk transfery/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bajty zapisu Average_Disk/s|Bajty zapisu na dysku/s|Count|Average|Bajty zapisu Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk zapisy/s|Zapisy dysku/s|Count|Average|Average_Disk zapisy/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabajtów|Wolne megabajty|Count|Average|Average_Free megabajtów|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bajty dysku Average_Logical/s|Bajty dysku logicznego/s|Count|Average|Bajty dysku Average_Logical/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ dostępna pamięć (%)|Dostępna pamięć (%)|Count|Average|Average_ dostępna pamięć (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Dostępny obszar wymiany (%) Average_|Dostępny obszar wymiany (%)|Count|Average|Dostępny obszar wymiany (%) Average_|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Użyta pamięć Average_%|Używana pamięć (%)|Count|Average|Użyta pamięć Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Używany obszar wymiany (%) Average_|Używany obszar wymiany (%)|Count|Average|Używany obszar wymiany (%) Average_|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pamięć (MB) Average_Available|Dostępna pamięć (MB)|Count|Average|Pamięć (MB) Average_Available|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available (MB)|Dostępny obszar wymiany (MB)|Count|Average|Average_Available (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odczyty Average_Page/s|Odczyty stron/s|Count|Average|Odczyty Average_Page/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page zapisy/s|Zapisy stron/s|Count|Average|Average_Page zapisy/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/s|Stron/s|Count|Average|Average_Pages/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Przestrzeń wymiany Average_Used MB|Używany obszar wymiany (MB)|Count|Average|Przestrzeń wymiany Average_Used MB|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pamięć (MB) Average_Used|Używana pamięć (MB)|Count|Average|Pamięć (MB) Average_Used|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bajty przesłane|Całkowita liczba przesłanych bajtów|Count|Average|Average_Total Bajty przesłane|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odebrane bajty Average_Total|Całkowita liczba odebranych bajtów|Count|Average|Odebrane bajty Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total bajty|Całkowita liczba bajtów|Count|Average|Average_Total bajty|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Przesłane pakiety Average_Total|Całkowita liczba przesłanych pakietów|Count|Average|Przesłane pakiety Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odebrane pakiety Average_Total|Całkowita liczba odebranych pakietów|Count|Average|Odebrane pakiety Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total błędy odbierania|Całkowita liczba błędów odbierania|Count|Average|Average_Total błędy odbierania|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Błędy wysyłania Average_Total|Całkowita liczba błędów transmisji|Count|Average|Błędy wysyłania Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total kolizje|Łączna liczba kolizji|Count|Average|Average_Total kolizje|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Czas dysku w s/Odczyt|Średni Czas dysku w s/Odczyt|Count|Average|Average_Avg. Czas dysku w s/Odczyt|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Dysku w s/Transfer|Średni Dysku w s/Transfer|Count|Average|Average_Avg. Dysku w s/Transfer|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Dysku w s/Zapis|Średni Dysku w s/Zapis|Count|Average|Average_Avg. Dysku w s/Zapis|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bajty dysku Average_Physical/s|Bajty dysku fizycznego/s|Count|Average|Bajty dysku Average_Physical/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct uprzywilejowany|Czas uprzywilejowany PCT|Count|Average|Average_Pct uprzywilejowany|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas użytkownika Average_Pct|Czas użytkownika PCT|Count|Average|Czas użytkownika Average_Pct|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used pamięć (KB)|Używana pamięć (w kilobajtach)|Count|Average|Average_Used pamięć (KB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pamięć współdzielona Average_Virtual|Wirtualna pamięć udostępniona|Count|Average|Pamięć współdzielona Average_Virtual|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas DPC (%) w Average_|Czas DPC (%)|Count|Average|Czas DPC (%) w Average_|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas bezczynności (%) Average_|Czas bezczynności (%)|Count|Average|Czas bezczynności (%) Average_|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas przerwań (%) Average_|Czas przerwań (%)|Count|Average|Czas przerwań (%) Average_|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% czasu oczekiwania operacji we/wy|Czas oczekiwania operacji we/wy (%)|Count|Average|Average_% czasu oczekiwania operacji we/wy|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% czasu (%)|% Całkiem czasu|Count|Average|Average_% czasu (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% czasu uprzywilejowanego|Czas uprzywilejowany (%)|Count|Average|Average_% czasu uprzywilejowanego|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ czas procesora (%)|Czas procesora (%)|Count|Average|Average_ czas procesora (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas użytkownika w Average_u (%)|Czas użytkownika (%)|Count|Average|Czas użytkownika w Average_u (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pamięć fizyczna Average_Free|Wolna pamięć fizyczna|Count|Average|Pamięć fizyczna Average_Free|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free miejsce w plikach stronicowania|Wolne miejsce w plikach stronicowania|Count|Average|Average_Free miejsce w plikach stronicowania|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pamięć wirtualna Average_Free|Wolna pamięć wirtualna|Count|Average|Pamięć wirtualna Average_Free|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Procesy|Count|Average|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size przechowywane w plikach stronicowania|Rozmiar zapisany w plikach stronicowania|Count|Average|Average_Size przechowywane w plikach stronicowania|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Czas pracy|Count|Average|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Użytkownicy|Count|Average|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Czas dysku w s/Odczyt|Średni Czas dysku w s/Odczyt|Count|Average|Average_Avg. Czas dysku w s/Odczyt|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Dysku w s/Zapis|Średni Dysku w s/Zapis|Count|Average|Average_Avg. Dysku w s/Zapis|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Długość kolejki dysku Average_Current|Bieżąca długość kolejki dysku|Count|Average|Długość kolejki dysku Average_Current|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odczyty Average_Disk/s|Odczyty dysku/s|Count|Average|Odczyty Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk transfery/s|Transfery dyskowe/s|Count|Average|Average_Disk transfery/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk zapisy/s|Zapisy dysku/s|Count|Average|Average_Disk zapisy/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabajtów|Wolne megabajty|Count|Average|Average_Free megabajtów|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Wolne miejsce w Average_%|% Wolnego miejsca|Count|Average|Wolne miejsce w Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available (MB)|Dostępna pamięć (MB)|Count|Average|Average_Available (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% zadeklarowanych bajtów w użyciu|% Zadeklarowanych bajtów w użyciu|Count|Average|Average_% zadeklarowanych bajtów w użyciu|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odebrane Average_Bytes/s|Bajty odebrane/s|Count|Average|Odebrane Average_Bytes/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes wysłane/s|Bajty wysłane/s|Count|Average|Average_Bytes wysłane/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes/s|Całkowita liczba bajtów/s|Count|Average|Average_Bytes/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ czas procesora (%)|Czas procesora (%)|Count|Average|Average_ czas procesora (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Długość kolejki Average_Processor|Długość kolejki procesora|Count|Average|Długość kolejki Average_Processor|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Puls|Puls|Count|Łącznie|Puls|Computer, OSType, Version, SourceComputerId|
|Aktualizacja|Aktualizacja|Count|Average|Aktualizacja|Komputer, produkt, klasyfikacja, UpdateState, opcjonalne, zatwierdzone|
|Wydarzenie|Wydarzenie|Count|Average|Wydarzenie|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QueryDuration|Czas trwania zapytania|MS|Average|Czas trwania zapytania języka DAX w ostatnim interwale|Nie wymiarów|
|QueryPoolJobQueueLength|Wątk Długość kolejki zadań puli zapytań|Count|Average|Liczba zadań w kolejce puli wątków zapytań.|Nie wymiarów|
|qpu_high_utilization_metric|QPU wysokie wykorzystanie|Count|Łącznie|QPU wysokie użycie w ciągu ostatnich minut, 1 do dużego użycia QPU, w przeciwnym razie 0|Nie wymiarów|
|memory_metric|Memory (Pamięć)|Bajty|Average|Rozmiar. Zakres 0-3 GB dla a1, 0-5 GB dla a2, 0-10 GB dla a3, 0-25 GB dla A4, 0-50 GB dla A5 i 0-100 GB dla A6|Nie wymiarów|
|memory_thrashing_metric|Migotanie pamięci|Percent|Average|Średnia pamięć migotanie.|Nie wymiarów|

## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/przestrzenie nazw

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Count|Łącznie|Pomyślne ListenerConnections dla elementu Microsoft. Relay.|EntityName|
|ListenerConnections — błąd clienterror|ListenerConnections — błąd clienterror|Count|Łącznie|Błąd clienterror on ListenerConnections dla elementu Microsoft. Relay.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Count|Łącznie|Błąd servererror on ListenerConnections dla elementu Microsoft. Relay.|EntityName|
|SenderConnections-Success|SenderConnections-Success|Count|Łącznie|Pomyślne SenderConnections dla elementu Microsoft. Relay.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Count|Łącznie|Błąd clienterror on SenderConnections dla elementu Microsoft. Relay.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Count|Łącznie|Błąd servererror on SenderConnections dla elementu Microsoft. Relay.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Count|Łącznie|Łącznie ListenerConnections dla elementu Microsoft. Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Count|Łącznie|Łączna liczba żądań SenderConnections dla elementu Microsoft. Relay.|EntityName|
|ActiveConnections|ActiveConnections|Count|Łącznie|Łącznie połączeń ActiveConnections dla elementu Microsoft. Relay.|EntityName|
|ActiveListeners|ActiveListeners|Count|Łącznie|Łącznie ActiveListeners dla elementu Microsoft. Relay.|EntityName|
|BytesTransferred|BytesTransferred|Count|Łącznie|Łącznie BytesTransferred dla elementu Microsoft. Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Count|Łącznie|Łącznie ListenerDisconnects dla elementu Microsoft. Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Count|Łącznie|Łącznie SenderDisconnects dla elementu Microsoft. Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SearchLatency|Opóźnienie wyszukiwania|Sekundy|Average|Średnie opóźnienie wyszukiwania dla usługi wyszukiwania|Nie wymiarów|
|SearchQueriesPerSecond|Zapytania wyszukiwania na sekundę|CountPerSecond|Average|Zapytania wyszukiwania na sekundę dla usługi wyszukiwania|Nie wymiarów|
|ThrottledSearchQueriesPercentage|Procent zapytań wyszukiwania z ograniczoną przepustowością|Percent|Average|Procent zapytań wyszukiwania, które zostały ograniczone dla usługi wyszukiwania|Nie wymiarów|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Żądania successfulrequests|Pomyślne żądania (wersja zapoznawcza)|Count|Łącznie|Łączna liczba pomyślnych żądań dla przestrzeni nazw (wersja zapoznawcza)|EntityName|
|Błędy servererrors|Błędy serwera. (Wersja zapoznawcza)|Count|Łącznie|Błędy serwera dla elementu Microsoft. ServiceBus. (Wersja zapoznawcza)|EntityName|
|Błędy usererrors|Błędy użytkownika. (Wersja zapoznawcza)|Count|Łącznie|Błędy użytkowników dla elementu Microsoft. ServiceBus. (Wersja zapoznawcza)|EntityName|
|Żądania throttledrequests|Żądania z ograniczeniami. (Wersja zapoznawcza)|Count|Łącznie|Żądania ograniczone dla elementu Microsoft. ServiceBus. (Wersja zapoznawcza)|EntityName|
|Żądania incomingrequests|Żądania przychodzące (wersja zapoznawcza)|Count|Łącznie|Żądania przychodzące dla Microsoft. ServiceBus. (Wersja zapoznawcza)|EntityName|
|Komunikaty incomingmessages|Wiadomości przychodzące (wersja zapoznawcza)|Count|Łącznie|Komunikaty przychodzące dla elementu Microsoft. ServiceBus. (Wersja zapoznawcza)|EntityName|
|Komunikaty outgoingmessages|Wiadomości wychodzące (wersja zapoznawcza)|Count|Łącznie|Komunikaty wychodzące dla elementu Microsoft. ServiceBus. (Wersja zapoznawcza)|EntityName|
|ActiveConnections|Połączeń ActiveConnections (wersja zapoznawcza)|Count|Łącznie|Całkowita liczba aktywnych połączeń dla elementu Microsoft. ServiceBus. (Wersja zapoznawcza)|Nie wymiarów|
|Size|Rozmiar (wersja zapoznawcza)|Bajty|Average|Rozmiar kolejki/tematu w bajtach. (Wersja zapoznawcza)|EntityName|
|Wiadomości|Liczba komunikatów w kolejce/temacie. (Wersja zapoznawcza)|Count|Average|Liczba komunikatów w kolejce/temacie. (Wersja zapoznawcza)|EntityName|
|ActiveMessages|Liczba aktywnych komunikatów w kolejce/temacie. (Wersja zapoznawcza)|Count|Average|Liczba aktywnych komunikatów w kolejce/temacie. (Wersja zapoznawcza)|EntityName|
|DeadletteredMessages|Liczba utraconych wiadomości w kolejce/temacie. (Wersja zapoznawcza)|Count|Average|Liczba utraconych wiadomości w kolejce/temacie. (Wersja zapoznawcza)|EntityName|
|ScheduledMessages|Liczba zaplanowanych komunikatów w kolejce/temacie. (Wersja zapoznawcza)|Count|Average|Liczba zaplanowanych komunikatów w kolejce/temacie. (Wersja zapoznawcza)|EntityName|
|CPUXNS|Użycie procesora na przestrzeń nazw|Percent|Maksimum|Metryka użycia procesora przestrzeni nazw w warstwie Premium usługi Service Bus|Nie wymiarów|
|WSXNS|Użycie rozmiaru pamięci na przestrzeń nazw|Percent|Maksimum|Metryka użycia pamięci przestrzeni nazw Premium usługi Service Bus|Nie wymiarów|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Count|Average|Procesor CPU przydzielony do tego kontenera w millicores|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|AllocatedMemory|AllocatedMemory|Bajty|Average|Pamięć przypisana do tego kontenera w MB|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|ActualCpu|ActualCpu|Count|Average|Rzeczywiste użycie procesora CPU w millicores|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|ActualMemory|ActualMemory|Bajty|Average|Rzeczywiste użycie pamięci w MB|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|CpuUtilization|CpuUtilization|Percent|Average|Użycie procesora CPU dla tego kontenera jako wartości procentowej AllocatedCpu|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|MemoryUtilization|MemoryUtilization|Percent|Average|Użycie procesora CPU dla tego kontenera jako wartości procentowej AllocatedCpu|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|ApplicationStatus|ApplicationStatus|Count|Average|Stan aplikacji siatki Service Fabric|ApplicationName, status|
|Bajty|Bajty|Count|Average|Stan kondycji usługi w aplikacji Service Fabric siatki|ApplicationName, status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Count|Average|Stan kondycji repliki usługi w aplikacji Service Fabric siatki|ApplicationName, status, ServiceName, servicereplicaname|
|ContainerStatus|ContainerStatus|Count|Average|Stan kontenera w aplikacji Service Fabric siatki|ApplicationName, ServiceName, CodePackageName, servicereplicaname, status|
|RestartCount|RestartCount|Count|Average|Liczba ponownych uruchomień kontenera w aplikacji Service Fabric siatki|ApplicationName, status, ServiceName, servicereplicaname, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ConnectionCount|Liczba połączeń|Count|Maksimum|Ilość połączenia użytkownika.|Endpoint|
|MessageCount|Liczba komunikatów|Count|Łącznie|Całkowita ilość komunikatów.|Nie wymiarów|
|InboundTraffic|Ruch przychodzący|Bajty|Łącznie|Ruch przychodzący usługi|Nie wymiarów|
|OutboundTraffic|Ruch wychodzący|Bajty|Łącznie|Ruch wychodzący usługi|Nie wymiarów|
|Błędy usererrors|Błędy użytkownika|Percent|Maksimum|Procent błędów użytkownika|Nie wymiarów|
|SystemErrors|Błędy systemu|Percent|Maksimum|Procent błędów systemu|Nie wymiarów|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Percent|Average|Procent użycia procesora CPU|Nie wymiarów|
|physical_data_read_percent|We/wy danych (procent)|Percent|Average|We/wy danych (procent)|Nie wymiarów|
|log_write_percent|We/wy dziennika (procent)|Percent|Average|Procent operacji we/wy dziennika. Nie dotyczy hurtowni danych.|Nie wymiarów|
|dtu_consumption_percent|Procent jednostek DTU|Percent|Average|Procent jednostek DTU. Dotyczy baz danych opartych na jednostkach DTU.|Nie wymiarów|
|magazyn|Używane miejsce na dane|Bajty|Maksimum|Łączny rozmiar bazy danych. Nie dotyczy hurtowni danych.|Nie wymiarów|
|connection_successful|Udane połączenia|Count|Łącznie|Udane połączenia|Nie wymiarów|
|connection_failed|Połączenia zakończone niepowodzeniem|Count|Łącznie|Połączenia zakończone niepowodzeniem|Nie wymiarów|
|blocked_by_firewall|Zablokowane przez zaporę|Count|Łącznie|Zablokowane przez zaporę|Nie wymiarów|
|stanu|Zakleszczenia|Count|Łącznie|Zakleszczenia. Nie dotyczy hurtowni danych.|Nie wymiarów|
|storage_percent|Procent użytego miejsca na danych|Percent|Maksimum|Procent rozmiaru bazy danych. Nie dotyczy magazynów danych ani baz danych w skali.|Nie wymiarów|
|xtp_storage_percent|Procent magazynu OLTP w pamięci|Percent|Average|Procent magazynu OLTP w pamięci. Nie dotyczy hurtowni danych.|Nie wymiarów|
|workers_percent|Procent procesów roboczych|Percent|Average|Procent pracowników. Nie dotyczy hurtowni danych.|Nie wymiarów|
|sessions_percent|Procent sesji|Percent|Average|Procent sesji. Nie dotyczy hurtowni danych.|Nie wymiarów|
|dtu_limit|DTU Limit|Count|Average|Limit jednostek DTU. Dotyczy baz danych opartych na jednostkach DTU.|Nie wymiarów|
|dtu_used|Używane jednostki DTU|Count|Average|Użyto jednostek DTU. Dotyczy baz danych opartych na jednostkach DTU.|Nie wymiarów|
|cpu_limit|Limit CPU|Count|Average|Limit czasu procesora CPU. Dotyczy baz danych opartych na rdzeń wirtualny.|Nie wymiarów|
|cpu_used|Użycie procesora CPU|Count|Average|Użycie procesora CPU. Dotyczy baz danych opartych na rdzeń wirtualny.|Nie wymiarów|
|dwu_limit|Limit jednostek dwu|Count|Maksimum|Limit jednostek dwu. Dotyczy tylko magazynów danych.|Nie wymiarów|
|dwu_consumption_percent|JEDNOSTEK dwu procent|Percent|Maksimum|Procent jednostek dwu. Dotyczy tylko magazynów danych.|Nie wymiarów|
|dwu_used|JEDNOSTEK dwu używane|Count|Maksimum|JEDNOSTEK dwu. Dotyczy tylko magazynów danych.|Nie wymiarów|
|dw_cpu_percent|Wartość procentowa procesora CPU na poziomie węzła DW|Percent|Average|Wartość procentowa procesora CPU na poziomie węzła DW|DwLogicalNodeId|
|dw_physical_data_read_percent|Procent operacji we/wy danych na poziomie węzła DW|Percent|Average|Procent operacji we/wy danych na poziomie węzła DW|DwLogicalNodeId|
|cache_hit_percent|Procent trafień w pamięci podręcznej|Percent|Maksimum|Procent trafień w pamięci podręcznej. Dotyczy tylko magazynów danych.|Nie wymiarów|
|cache_used_percent|Procent użycia pamięci podręcznej|Percent|Maksimum|Procent użycia pamięci podręcznej. Dotyczy tylko magazynów danych.|Nie wymiarów|
|local_tempdb_usage_percent|Procent lokalnej bazy danych tempdb|Percent|Average|Procent lokalnej bazy danych tempdb. Dotyczy tylko magazynów danych.|Nie wymiarów|
|app_cpu_billed|Aplikacja rozliczana na podstawie procesora|Count|Łącznie|Rozliczane użycie procesora przez aplikację. Dotyczy bezserwerowych baz danych.|Nie wymiarów|
|app_cpu_percent|Procent użycia procesora CPU aplikacji|Percent|Average|Procent użycia procesora CPU aplikacji. Dotyczy bezserwerowych baz danych.|Nie wymiarów|
|app_memory_percent|Procent użycia pamięci aplikacji|Percent|Average|Procent wykorzystania pamięci aplikacji. Dotyczy bezserwerowych baz danych.|Nie wymiarów|
|allocated_data_storage|Przydzielone miejsce na dane|Bajty|Average|Przydzielono miejsce na danych. Nie dotyczy hurtowni danych.|Nie wymiarów|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Percent|Average|Procent użycia procesora CPU|Nie wymiarów|
|physical_data_read_percent|We/wy danych (procent)|Percent|Average|We/wy danych (procent)|Nie wymiarów|
|log_write_percent|We/wy dziennika (procent)|Percent|Average|We/wy dziennika (procent)|Nie wymiarów|
|dtu_consumption_percent|Procent jednostek DTU|Percent|Average|Procent jednostek DTU. Dotyczy pul elastycznych opartych na jednostkach DTU.|Nie wymiarów|
|storage_percent|Procent użytego miejsca na danych||Percent|Average|Procent miejsca do magazynowania|Nie wymiarów|
|workers_percent|Procent procesów roboczych|Percent|Average|Procent procesów roboczych|Nie wymiarów|
|sessions_percent|Procent sesji|Percent|Average|Procent sesji|Nie wymiarów|
|eDTU_limit|limit liczby jednostek eDTU|Count|Average|limit liczby jednostek eDTU. Dotyczy pul elastycznych opartych na jednostkach DTU.|Nie wymiarów|
|storage_limit|Maksymalny rozmiar danych|Bajty|Average|Limit magazynu|Nie wymiarów|
|eDTU_used|użyta wartość eDTU|Count|Average|użyta wartość eDTU. Dotyczy pul elastycznych opartych na jednostkach DTU.|Nie wymiarów|
|storage_used|Używane miejsce na dane|Bajty|Average|Używane miejsce|Nie wymiarów|
|xtp_storage_percent|Procent magazynu OLTP w pamięci|Percent|Average|Procent magazynu OLTP w pamięci|Nie wymiarów|
|cpu_limit|Limit CPU|Count|Average|Limit czasu procesora CPU. Dotyczy pul elastycznych opartych na rdzeń wirtualny.|Nie wymiarów|
|cpu_used|Użycie procesora CPU|Count|Average|Użycie procesora CPU. Dotyczy pul elastycznych opartych na rdzeń wirtualny.|Nie wymiarów|
|allocated_data_storage|Przydzielone miejsce na dane|Bajty|Average|Przydzielone miejsce na dane|Nie wymiarów|
|allocated_data_storage_percent|Procent przydzielonych przestrzeni danych|Percent|Maksimum|Procent przydzielonych przestrzeni danych|Nie wymiarów|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|virtual_core_count|Liczba rdzeni wirtualnych|Count|Average|Liczba rdzeni wirtualnych|Nie wymiarów|
|avg_cpu_percent|Średni procent procesora CPU|Percent|Average|Średni procent procesora CPU|Nie wymiarów|
|reserved_storage_mb|Zarezerwowane miejsce w magazynie|Count|Average|Zarezerwowane miejsce w magazynie|Nie wymiarów|
|storage_space_used_mb|Używane miejsce do magazynowania|Count|Average|Używane miejsce do magazynowania|Nie wymiarów|
|io_requests|Liczba żądań we/wy|Count|Average|Liczba żądań we/wy|Nie wymiarów|
|io_bytes_read|Odczytane bajty we/wy|Bajty|Average|Odczytane bajty we/wy|Nie wymiarów|
|io_bytes_written|Bajty we/wy zapisywane|Bajty|Average|Bajty we/wy zapisywane|Nie wymiarów|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|UsedCapacity|Używana pojemność|Bajty|Średnia|Używana pojemność konta|Nie wymiarów|
|Transakcje|Transakcje|Licznik|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje udane i nieudane żądania, a także żądania, które wygenerowany błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|MS|Średnia|Średni czas oczekiwania, używane przez usługę Azure Storage do przetwarzania żądania zakończonego powodzeniem w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego w elemencie AverageE2ELatency.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie e2e dla powodzenia|MS|Średnia|Średnie opóźnienie end-to-end żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests i podzielenie go przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy skutkują od ograniczoną dostępnością usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BlobCapacity|Pojemność obiektu blob|Bajty|Average|Ilość miejsca używanego przez usługę obiektów Blob konta magazynu, w bajtach.|Blobtype, warstwa|
|BlobCount|Liczba obiektów blob|Licznik|Łącznie|Liczba obiektów Blob w usłudze obiektów Blob konta magazynu.|BlobType|       |BlobCount|Liczba obiektów blob|Count|Average|Liczba obiektów Blob w usłudze obiektów Blob konta magazynu.|Blobtype, warstwa|
|ContainerCount|Liczba kontenerów obiektów blob|Licznik|Średnia|Liczba kontenerów w usłudze obiektów Blob konta magazynu.|Nie wymiarów|
|IndexCapacity|Pojemność indeksu|Bajty|Average|Ilość miejsca do magazynowania używanego przez ADLS Gen2 (hierarchiczny) indeks w bajtach.|Nie wymiarów|
|Transakcje|Transakcje|Licznik|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje udane i nieudane żądania, a także żądania, które wygenerowany błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|MS|Średnia|Średni czas oczekiwania, używane przez usługę Azure Storage do przetwarzania żądania zakończonego powodzeniem w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego w elemencie AverageE2ELatency.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie e2e dla powodzenia|MS|Średnia|Średnie opóźnienie end-to-end żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests i podzielenie go przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy skutkują od ograniczoną dostępnością usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|FileCapacity|Pojemność plików|Bajty|Average|Ilość miejsca do magazynowania używanego przez usługę plików konta magazynu w bajtach.|Nie wymiarów|
|FileCount|Liczba plików|Count|Average|Liczba plików w usłudze plików konta magazynu.|Nie wymiarów|
|FileShareCount|Liczba udziałów plików|Count|Average|Liczba udziałów plików w usłudze plików konta magazynu.|Nie wymiarów|
|Transakcje|Transakcje|Licznik|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje udane i nieudane żądania, a także żądania, które wygenerowany błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|MS|Średnia|Średni czas oczekiwania, używane przez usługę Azure Storage do przetwarzania żądania zakończonego powodzeniem w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego w elemencie AverageE2ELatency.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie e2e dla powodzenia|MS|Średnia|Średnie opóźnienie end-to-end żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests i podzielenie go przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy skutkują od ograniczoną dostępnością usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QueueCapacity|Pojemność kolejki|Bajty|Średnia|Ilość miejsca używanego przez usługę kolejki konta magazynu, w bajtach.|Nie wymiarów|
|QueueCount|Liczba kolejek|Licznik|Średnia|Liczba kolejek w usłudze kolejki konta magazynu.|Nie wymiarów|
|QueueMessageCount|Liczba komunikatów w kolejce|Licznik|Średnia|Przybliżona liczba komunikatów w kolejce w usłudze kolejki konta magazynu.|Nie wymiarów|
|Transakcje|Transakcje|Licznik|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje udane i nieudane żądania, a także żądania, które wygenerowany błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|MS|Średnia|Średni czas oczekiwania, używane przez usługę Azure Storage do przetwarzania żądania zakończonego powodzeniem w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego w elemencie AverageE2ELatency.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie e2e dla powodzenia|MS|Średnia|Średnie opóźnienie end-to-end żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests i podzielenie go przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy skutkują od ograniczoną dostępnością usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TableCapacity|Pojemność tabeli|Bajty|Średnia|Ilość miejsca używanego przez usługę tabeli konta magazynu, w bajtach.|Nie wymiarów|
|TableCount|Liczba tabel|Licznik|Średnia|Liczba tabel w usłudze tabeli konta magazynu.|Nie wymiarów|
|TableEntityCount|Liczba jednostek tabel|Licznik|Średnia|Liczba jednostek tabel w usłudze tabeli konta magazynu.|Nie wymiarów|
|Transakcje|Transakcje|Licznik|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje udane i nieudane żądania, a także żądania, które wygenerowany błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|MS|Średnia|Średni czas oczekiwania, używane przez usługę Azure Storage do przetwarzania żądania zakończonego powodzeniem w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego w elemencie AverageE2ELatency.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie e2e dla powodzenia|MS|Średnia|Średnie opóźnienie end-to-end żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests i podzielenie go przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy skutkują od ograniczoną dostępnością usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Wynik synchronizacji sesji|Count|Average|Metryka, która rejestruje wartość 1 za każdym razem, gdy punkt końcowy serwera pomyślnie ukończy sesję synchronizacji z punktem końcowym w chmurze|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Pliki zsynchronizowane|Count|Łącznie|Liczba synchronizowanych plików|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Pliki nie są synchronizowane|Count|Łącznie|Nie można zsynchronizować liczby plików|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Zsynchronizowane bajty|Bajty|Łącznie|Łączny rozmiar pliku transferowanego dla sesji synchronizacji|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Stan online serwera|Count|Maksimum|Metryka, która rejestruje wartość 1 za każdym razem, gdy zarejestrowany serwer pomyślnie rejestruje puls przy użyciu punktu końcowego w chmurze|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Odwołanie do warstw w chmurze|Bajty|Łącznie|Łączny rozmiar danych, które zostały wywołane przez serwer|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ResourceUtilization|% wykorzystania SU|Percent|Maksimum|% wykorzystania SU|Logicznaname, PartitionId|
|InputEvents|Zdarzenia wejściowe|Count|Łącznie|Zdarzenia wejściowe|Logicznaname, PartitionId|
|InputEventBytes|Zdarzenia wejściowe (bajty)|Bajty|Łącznie|Zdarzenia wejściowe (bajty)|Logicznaname, PartitionId|
|LateInputEvents|Opóźnione zdarzenia wejściowe|Count|Łącznie|Opóźnione zdarzenia wejściowe|Logicznaname, PartitionId|
|OutputEvents|Zdarzenia wyjściowe|Count|Łącznie|Zdarzenia wyjściowe|Logicznaname, PartitionId|
|ConversionErrors|Błędy konwersji danych|Count|Łącznie|Błędy konwersji danych|Logicznaname, PartitionId|
|Błędy|Błędy w czasie wykonywania|Count|Łącznie|Błędy w czasie wykonywania|Logicznaname, PartitionId|
|DroppedOrAdjustedEvents|Zdarzenia poza kolejnością|Count|Łącznie|Zdarzenia poza kolejnością|Logicznaname, PartitionId|
|AMLCalloutRequests|Żądania funkcji|Count|Łącznie|Żądania funkcji|Logicznaname, PartitionId|
|AMLCalloutFailedRequests|Żądania funkcji zakończone niepowodzeniem|Count|Łącznie|Żądania funkcji zakończone niepowodzeniem|Logicznaname, PartitionId|
|AMLCalloutInputEvents|Zdarzenia funkcji|Count|Łącznie|Zdarzenia funkcji|Logicznaname, PartitionId|
|DeserializationError|Błędy deserializacji danych wejściowych|Count|Łącznie|Błędy deserializacji danych wejściowych|Logicznaname, PartitionId|
|EarlyInputEvents|Wczesne zdarzenia wejściowe|Count|Łącznie|Wczesne zdarzenia wejściowe|Logicznaname, PartitionId|
|OutputWatermarkDelaySeconds|Opóźnienie znaku wodnego|Sekundy|Maksimum|Opóźnienie znaku wodnego|Logicznaname, PartitionId|
|InputEventsSourcesBacklogged|Zaległe zdarzenia wejściowe|Count|Maksimum|Zaległe zdarzenia wejściowe|Logicznaname, PartitionId|
|InputEventsSourcesPerSecond|Odebrane źródła wejściowe|Count|Łącznie|Odebrane źródła wejściowe|Logicznaname, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/środowiska

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|IngressReceivedMessages|Odebrane komunikaty transferu danych przychodzących|Count|Łącznie|Liczba komunikatów odczytanych ze wszystkich źródeł zdarzeń centrum zdarzeń lub Centrum IoT Hub|Nie wymiarów|
|IngressReceivedInvalidMessages|Odebrano nieprawidłowe komunikaty dotyczące transferu danych przychodzących|Count|Łącznie|Liczba nieprawidłowych komunikatów odczytywanych ze wszystkich centrów zdarzeń lub źródeł zdarzeń usługi IoT Hub|Nie wymiarów|
|IngressReceivedBytes|Bajty odebrane z ruchu przychodzącego|Bajty|Łącznie|Liczba bajtów odczytanych ze wszystkich źródeł zdarzeń|Nie wymiarów|
|IngressStoredBytes|Bajty przechowywane w ruchu przychodzącym|Bajty|Łącznie|Łączny rozmiar zdarzeń pomyślnie przetworzonych i dostępnych dla zapytania|Nie wymiarów|
|IngressStoredEvents|Zdarzenia związane z transferem danych przychodzących|Count|Łącznie|Liczba zdarzeń spłaszczonych pomyślnie przetworzonych i dostępnych dla zapytania|Nie wymiarów|
|IngressReceivedMessagesTimeLag|Zwłoka czasu odbierania komunikatów przychodzących|Sekundy|Maksimum|Różnica między czasem, w którym wiadomość jest przetworzona do kolejki w źródle zdarzeń i czasie, w którym jest przetwarzany w danych wejściowych|Nie wymiarów|
|IngressReceivedMessagesCountLag|Opóźnienie liczby odebranych komunikatów przychodzących|Count|Average|Różnica między numerem sekwencyjnym ostatnio zarejestrowanego komunikatu w partycji źródłowej zdarzenia i sekwencją sekwencji komunikatów przetwarzanych w ramach ruchu przychodzącego|Nie wymiarów|
|WarmStorageMaxProperties|Maksymalna liczba właściwości magazynu ciepłego|Count|Maksimum|Maksymalna liczba właściwości używanych przez środowisko dla jednostki SKU S1/S2 i Maksymalna liczba właściwości dozwolona przez magazyn ciepły dla jednostki SKU|Nie wymiarów|
|WarmStorageUsedProperties|Właściwości używanej pamięci masowej |Count|Maksimum|Liczba właściwości używanych przez środowisko dla jednostki SKU S1/S2 i liczby właściwości używanych przez magazyn ciepły dla jednostek SKU|Nie wymiarów|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/Environments/eventsources

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|IngressReceivedMessages|Odebrane komunikaty transferu danych przychodzących|Count|Łącznie|Liczba komunikatów odczytanych ze źródła zdarzeń|Nie wymiarów|
|IngressReceivedInvalidMessages|Odebrano nieprawidłowe komunikaty dotyczące transferu danych przychodzących|Count|Łącznie|Liczba nieprawidłowych komunikatów odczytanych ze źródła zdarzeń|Nie wymiarów|
|IngressReceivedBytes|Bajty odebrane z ruchu przychodzącego|Bajty|Łącznie|Liczba bajtów odczytanych ze źródła zdarzenia|Nie wymiarów|
|IngressStoredBytes|Bajty przechowywane w ruchu przychodzącym|Bajty|Łącznie|Łączny rozmiar zdarzeń pomyślnie przetworzonych i dostępnych dla zapytania|Nie wymiarów|
|IngressStoredEvents|Zdarzenia związane z transferem danych przychodzących|Count|Łącznie|Liczba zdarzeń spłaszczonych pomyślnie przetworzonych i dostępnych dla zapytania|Nie wymiarów|
|IngressReceivedMessagesTimeLag|Zwłoka czasu odbierania komunikatów przychodzących|Sekundy|Maksimum|Różnica między czasem, w którym wiadomość jest przetworzona do kolejki w źródle zdarzeń i czasie, w którym jest przetwarzany w danych wejściowych|Nie wymiarów|
|IngressReceivedMessagesCountLag|Opóźnienie liczby odebranych komunikatów przychodzących|Count|Average|Różnica między numerem sekwencyjnym ostatnio zarejestrowanego komunikatu w partycji źródłowej zdarzenia i sekwencją sekwencji komunikatów przetwarzanych w ramach ruchu przychodzącego|Nie wymiarów|
|WarmStorageMaxProperties|Maksymalna liczba właściwości magazynu ciepłego|Count|Maksimum|Maksymalna liczba właściwości używanych przez środowisko dla jednostki SKU S1/S2 i Maksymalna liczba właściwości dozwolona przez magazyn ciepły dla jednostki SKU|Nie wymiarów|
|WarmStorageUsedProperties|Właściwości używanej pamięci masowej |Count|Maksimum|Liczba właściwości używanych przez środowisko dla jednostki SKU S1/S2 i liczby właściwości używanych przez magazyn ciepły dla jednostek SKU|Nie wymiarów|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. VMwareCloudSimple/virtualMachines

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Bajty odczytu dysku/s|BytesPerSecond|Average|Średnia przepływność dysku spowodowana operacjami odczytu w okresie próbkowania.|Nie wymiarów|
|DiskWriteBytesPerSecond|Bajty zapisu dysku/s|BytesPerSecond|Average|Średnia przepływność dysku spowodowana operacją zapisu w okresie próbnym.|Nie wymiarów|
|Bajty odczytane z dysku|Bajty odczytane z dysku|Bajty|Łącznie|Łączna przepływność dysku spowodowana operacjami odczytu w okresie próbkowania.|Nie wymiarów|
|Bajty zapisane na dysku|Bajty zapisane na dysku|Bajty|Łącznie|Łączna przepływność dysku spowodowana operacjami zapisu w okresie próbkowania.|Nie wymiarów|
|DiskReadOperations|Operacje odczytu z dysku|Count|Łącznie|Liczba operacji odczytu we/wy w poprzednim okresie próbkowania. Należy pamiętać, że operacje te mogą mieć rozmiar zmienny.|Nie wymiarów|
|DiskWriteOperations|Operacje zapisu na dysku|Count|Łącznie|Liczba operacji zapisu we/wy w poprzednim okresie próbkowania. Należy pamiętać, że operacje te mogą mieć rozmiar zmienny.|Nie wymiarów|
|Operacje odczytu z dysku/s|Operacje odczytu z dysku/s|CountPerSecond|Average|Średnia liczba operacji odczytu we/wy w poprzednim okresie próbkowania. Należy pamiętać, że operacje te mogą mieć rozmiar zmienny.|Nie wymiarów|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku/s|CountPerSecond|Average|Średnia liczba operacji we/wy zapisu w poprzednim okresie próbkowania. Należy pamiętać, że operacje te mogą mieć rozmiar zmienny.|Nie wymiarów|
|DiskReadLatency|Opóźnienie odczytu dysku|MS|Average|Całkowite opóźnienie odczytu. Suma opóźnień odczytu urządzenia i jądra.|Nie wymiarów|
|DiskWriteLatency|Opóźnienie zapisu na dysku|MS|Average|Całkowite opóźnienie zapisu. Suma opóźnień między urządzeniem i zapisem jądra.|Nie wymiarów|
|NetworkInBytesPerSecond|Sieć w bajtach/s|BytesPerSecond|Average|Średnia przepływność sieci dla odebranego ruchu.|Nie wymiarów|
|NetworkOutBytesPerSecond|Bajty wychodzące z sieci/s|BytesPerSecond|Average|Średnia przepływność sieci dla przesyłanego ruchu.|Nie wymiarów|
|Sieć — wejście|Sieć — wejście|Bajty|Łącznie|Całkowita przepływność sieci dla odebranego ruchu.|Nie wymiarów|
|Sieć — wyjście|Sieć — wyjście|Bajty|Łącznie|Całkowita przepustowość sieci przesyłanych danych.|Nie wymiarów|
|MemoryUsed|Używana pamięć|Bajty|Average|Ilość pamięci maszyny używanej przez maszynę wirtualną.|Nie wymiarów|
|MemoryGranted|Przydzielone pamięci|Bajty|Average|Ilość pamięci przydzielonej maszynie wirtualnej przez hosta. Pamięć nie jest przydzielana do hosta, dopóki nie zostanie ona nałożona jeden raz, a przyznana pamięć może zostać zamieniony lub zaznaczono w dymku, jeśli VMkernel wymaga pamięci.|Nie wymiarów|
|MemoryActive|Pamięć aktywna|Bajty|Average|Ilość pamięci używanej przez maszynę wirtualną w minionym małym oknie czasu. Jest to wartość "prawda" ilości pamięci wymaganej przez maszynę wirtualną. Dodatkowa niewykorzystana pamięć może zostać zastąpiona lub zaprowadzona bez wpływu na wydajność gościa.|Nie wymiarów|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Average|Użycie procesora CPU. Ta wartość jest raportowana przy użyciu 100% reprezentujących wszystkie rdzenie procesora w systemie. Przykładowo 2-kierunkowa maszyna wirtualna używająca 50% systemu z czterema rdzeniem korzysta całkowicie z dwóch rdzeni.|Nie wymiarów|
|PercentageCpuReady|Procent gotowych do użycia procesora|MS|Łącznie|Czas gotowości to czas oczekiwania, aż procesor CPU stanie się dostępny w okresie ostatniej aktualizacji.|Nie wymiarów|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuPercentage|Procent użycia procesora CPU|Percent|Average|Procent użycia procesora CPU|Wystąpienie|
|MemoryPercentage|Procent pamięci|Percent|Average|Procent pamięci|Wystąpienie|
|DiskQueueLength|Długość kolejki dysku|Count|Average|Długość kolejki dysku|Wystąpienie|
|HttpQueueLength|Długość kolejki HTTP|Count|Average|Długość kolejki HTTP|Wystąpienie|
|BytesReceived|Dane wejściowe|Bajty|Łącznie|Dane wejściowe|Wystąpienie|
|BytesSent|Dane wyjściowe|Bajty|Łącznie|Dane wyjściowe|Wystąpienie|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/Sites (z wyjątkiem funkcji)

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuTime|Czas procesora CPU|Sekundy|Łącznie|Czas procesora CPU|Wystąpienie|
|Żądania|Żądania|Count|Łącznie|Żądania|Wystąpienie|
|BytesReceived|Dane wejściowe|Bajty|Łącznie|Dane wejściowe|Wystąpienie|
|BytesSent|Dane wyjściowe|Bajty|Łącznie|Dane wyjściowe|Wystąpienie|
|Http101|HTTP 101|Count|Łącznie|HTTP 101|Wystąpienie|
|Http2xx|HTTP 2xx|Count|Łącznie|HTTP 2xx|Wystąpienie|
|Http3xx|HTTP 3xx|Count|Łącznie|HTTP 3xx|Wystąpienie|
|Http401|HTTP 401|Count|Łącznie|HTTP 401|Wystąpienie|
|Http403|HTTP 403|Count|Łącznie|HTTP 403|Wystąpienie|
|Http404|HTTP 404|Count|Łącznie|HTTP 404|Wystąpienie|
|Http406|HTTP 406|Count|Łącznie|HTTP 406|Wystąpienie|
|Http4xx|HTTP 4xx|Count|Łącznie|HTTP 4xx|Wystąpienie|
|Http5xx|Błędy serwera HTTP|Count|Łącznie|Błędy serwera HTTP|Wystąpienie|
|MemoryWorkingSet|Zestaw roboczy pamięci|Bajty|Average|Zestaw roboczy pamięci|Wystąpienie|
|AverageMemoryWorkingSet|Średni zestaw roboczy pamięci|Bajty|Average|Średni zestaw roboczy pamięci|Wystąpienie|
|AverageResponseTime|Średni czas odpowiedzi|Sekundy|Average|Średni czas odpowiedzi|Wystąpienie|
|AppConnections|Połączenia|Count|Average|Połączenia|Wystąpienie|
|Dojścia|Liczba dojść|Count|Average|Liczba dojść|Wystąpienie|
|Wątki|Liczba wątków|Count|Average|Liczba wątków|Wystąpienie|
|PrivateBytes|Bajty prywatne|Bajty|Average|Bajty prywatne|Wystąpienie|
|IoReadBytesPerSecond|Odczytane bajty We/Wy na sekundę|BytesPerSecond|Łącznie|Odczytane bajty We/Wy na sekundę|Wystąpienie|
|IoWriteBytesPerSecond|Zapisane bajty We/Wy na sekundę|BytesPerSecond|Łącznie|Zapisane bajty We/Wy na sekundę|Wystąpienie|
|IoOtherBytesPerSecond|Inne bajty We/Wy na sekundę|BytesPerSecond|Łącznie|Inne bajty We/Wy na sekundę|Wystąpienie|
|IoReadOperationsPerSecond|Inne operacje odczytu We/Wy na sekundę|BytesPerSecond|Łącznie|Inne operacje odczytu We/Wy na sekundę|Wystąpienie|
|IoWriteOperationsPerSecond|Operacje zapisu We/Wy na sekundę|BytesPerSecond|Łącznie|Operacje zapisu We/Wy na sekundę|Wystąpienie|
|IoOtherOperationsPerSecond|Inne operacje We/Wy na sekundę|BytesPerSecond|Łącznie|Inne operacje We/Wy na sekundę|Wystąpienie|
|RequestsInApplicationQueue|Żądania w kolejce aplikacji|Count|Average|Żądania w kolejce aplikacji|Wystąpienie|
|CurrentAssemblies|Aktualna liczba zestawów|Count|Average|Aktualna liczba zestawów|Wystąpienie|
|TotalAppDomains|Łączna liczba domen aplikacji|Count|Average|Łączna liczba domen aplikacji|Wystąpienie|
|TotalAppDomainsUnloaded|Łączna liczba nie załadowanych domen aplikacji|Count|Average|Łączna liczba nie załadowanych domen aplikacji|Wystąpienie|
|Gen0Collections|Zbieranie elementów bezużytecznych generacji 0|Count|Łącznie|Zbieranie elementów bezużytecznych generacji 0|Wystąpienie|
|Gen1Collections|Zbieranie elementów bezużytecznych generacji 1|Count|Łącznie|Zbieranie elementów bezużytecznych generacji 1|Wystąpienie|
|Gen2Collections|Zbieranie elementów bezużytecznych generacji 2|Count|Łącznie|Zbieranie elementów bezużytecznych generacji 2|Wystąpienie|

## <a name="microsoftwebsites-functions"></a>Microsoft. Web/Sites (funkcje)

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BytesReceived|Dane wejściowe|Bajty|Łącznie|Dane wejściowe|Wystąpienie|
|BytesSent|Dane wyjściowe|Bajty|Łącznie|Dane wyjściowe|Wystąpienie|
|Http5xx|Błędy serwera HTTP|Count|Łącznie|Błędy serwera HTTP|Wystąpienie|
|MemoryWorkingSet|Zestaw roboczy pamięci|Bajty|Average|Zestaw roboczy pamięci|Wystąpienie|
|AverageMemoryWorkingSet|Średni zestaw roboczy pamięci|Bajty|Average|Średni zestaw roboczy pamięci|Wystąpienie|
|FunctionExecutionUnits|Jednostki wykonawcze funkcji|MB/milisekundy|Łącznie|[Jednostki wykonywania funkcji](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Wystąpienie|
|FunctionExecutionCount|Liczba wykonań funkcji|Count|Łącznie|Liczba wykonań funkcji|Wystąpienie|
|PrivateBytes|Bajty prywatne|Bajty|Average|Bajty prywatne|Wystąpienie|
|IoReadBytesPerSecond|Odczytane bajty We/Wy na sekundę|BytesPerSecond|Łącznie|Odczytane bajty We/Wy na sekundę|Wystąpienie|
|IoWriteBytesPerSecond|Zapisane bajty We/Wy na sekundę|BytesPerSecond|Łącznie|Zapisane bajty We/Wy na sekundę|Wystąpienie|
|IoOtherBytesPerSecond|Inne bajty We/Wy na sekundę|BytesPerSecond|Łącznie|Inne bajty We/Wy na sekundę|Wystąpienie|
|IoReadOperationsPerSecond|Inne operacje odczytu We/Wy na sekundę|BytesPerSecond|Łącznie|Inne operacje odczytu We/Wy na sekundę|Wystąpienie|
|IoWriteOperationsPerSecond|Operacje zapisu We/Wy na sekundę|BytesPerSecond|Łącznie|Operacje zapisu We/Wy na sekundę|Wystąpienie|
|IoOtherOperationsPerSecond|Inne operacje We/Wy na sekundę|BytesPerSecond|Łącznie|Inne operacje We/Wy na sekundę|Wystąpienie|
|RequestsInApplicationQueue|Żądania w kolejce aplikacji|Count|Average|Żądania w kolejce aplikacji|Wystąpienie|
|CurrentAssemblies|Aktualna liczba zestawów|Count|Average|Aktualna liczba zestawów|Wystąpienie|
|TotalAppDomains|Łączna liczba domen aplikacji|Count|Average|Łączna liczba domen aplikacji|Wystąpienie|
|TotalAppDomainsUnloaded|Łączna liczba nie załadowanych domen aplikacji|Count|Average|Łączna liczba nie załadowanych domen aplikacji|Wystąpienie|
|Gen0Collections|Zbieranie elementów bezużytecznych generacji 0|Count|Łącznie|Zbieranie elementów bezużytecznych generacji 0|Wystąpienie|
|Gen1Collections|Zbieranie elementów bezużytecznych generacji 1|Count|Łącznie|Zbieranie elementów bezużytecznych generacji 1|Wystąpienie|
|Gen2Collections|Zbieranie elementów bezużytecznych generacji 2|Count|Łącznie|Zbieranie elementów bezużytecznych generacji 2|Wystąpienie|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuTime|Czas procesora CPU|Sekundy|Łącznie|Czas procesora CPU|Wystąpienie|
|Żądania|Żądania|Count|Łącznie|Żądania|Wystąpienie|
|BytesReceived|Dane wejściowe|Bajty|Łącznie|Dane wejściowe|Wystąpienie|
|BytesSent|Dane wyjściowe|Bajty|Łącznie|Dane wyjściowe|Wystąpienie|
|Http101|HTTP 101|Count|Łącznie|HTTP 101|Wystąpienie|
|Http2xx|HTTP 2xx|Count|Łącznie|HTTP 2xx|Wystąpienie|
|Http3xx|HTTP 3xx|Count|Łącznie|HTTP 3xx|Wystąpienie|
|Http401|HTTP 401|Count|Łącznie|HTTP 401|Wystąpienie|
|Http403|HTTP 403|Count|Łącznie|HTTP 403|Wystąpienie|
|Http404|HTTP 404|Count|Łącznie|HTTP 404|Wystąpienie|
|Http406|HTTP 406|Count|Łącznie|HTTP 406|Wystąpienie|
|Http4xx|HTTP 4xx|Count|Łącznie|HTTP 4xx|Wystąpienie|
|Http5xx|Błędy serwera HTTP|Count|Łącznie|Błędy serwera HTTP|Wystąpienie|
|MemoryWorkingSet|Zestaw roboczy pamięci|Bajty|Average|Zestaw roboczy pamięci|Wystąpienie|
|AverageMemoryWorkingSet|Średni zestaw roboczy pamięci|Bajty|Average|Średni zestaw roboczy pamięci|Wystąpienie|
|AverageResponseTime|Średni czas odpowiedzi|Sekundy|Average|Średni czas odpowiedzi|Wystąpienie|
|FunctionExecutionUnits|Jednostki wykonawcze funkcji|Count|Łącznie|Jednostki wykonawcze funkcji|Wystąpienie|
|FunctionExecutionCount|Liczba wykonań funkcji|Count|Łącznie|Liczba wykonań funkcji|Wystąpienie|
|AppConnections|Połączenia|Count|Average|Połączenia|Wystąpienie|
|Dojścia|Liczba dojść|Count|Average|Liczba dojść|Wystąpienie|
|Wątki|Liczba wątków|Count|Average|Liczba wątków|Wystąpienie|
|PrivateBytes|Bajty prywatne|Bajty|Average|Bajty prywatne|Wystąpienie|
|IoReadBytesPerSecond|Odczytane bajty We/Wy na sekundę|BytesPerSecond|Łącznie|Odczytane bajty We/Wy na sekundę|Wystąpienie|
|IoWriteBytesPerSecond|Zapisane bajty We/Wy na sekundę|BytesPerSecond|Łącznie|Zapisane bajty We/Wy na sekundę|Wystąpienie|
|IoOtherBytesPerSecond|Inne bajty We/Wy na sekundę|BytesPerSecond|Łącznie|Inne bajty We/Wy na sekundę|Wystąpienie|
|IoReadOperationsPerSecond|Inne operacje odczytu We/Wy na sekundę|BytesPerSecond|Łącznie|Inne operacje odczytu We/Wy na sekundę|Wystąpienie|
|IoWriteOperationsPerSecond|Operacje zapisu We/Wy na sekundę|BytesPerSecond|Łącznie|Operacje zapisu We/Wy na sekundę|Wystąpienie|
|IoOtherOperationsPerSecond|Inne operacje We/Wy na sekundę|BytesPerSecond|Łącznie|Inne operacje We/Wy na sekundę|Wystąpienie|
|RequestsInApplicationQueue|Żądania w kolejce aplikacji|Count|Average|Żądania w kolejce aplikacji|Wystąpienie|
|CurrentAssemblies|Aktualna liczba zestawów|Count|Average|Aktualna liczba zestawów|Wystąpienie|
|TotalAppDomains|Łączna liczba domen aplikacji|Count|Average|Łączna liczba domen aplikacji|Wystąpienie|
|TotalAppDomainsUnloaded|Łączna liczba nie załadowanych domen aplikacji|Count|Average|Łączna liczba nie załadowanych domen aplikacji|Wystąpienie|
|Gen0Collections|Zbieranie elementów bezużytecznych generacji 0|Count|Łącznie|Zbieranie elementów bezużytecznych generacji 0|Wystąpienie|
|Gen1Collections|Zbieranie elementów bezużytecznych generacji 1|Count|Łącznie|Zbieranie elementów bezużytecznych generacji 1|Wystąpienie|
|Gen2Collections|Zbieranie elementów bezużytecznych generacji 2|Count|Łącznie|Zbieranie elementów bezużytecznych generacji 2|Wystąpienie|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Żądania|Żądania|Count|Łącznie|Żądania|Wystąpienie|
|BytesReceived|Dane wejściowe|Bajty|Łącznie|Dane wejściowe|Wystąpienie|
|BytesSent|Dane wyjściowe|Bajty|Łącznie|Dane wyjściowe|Wystąpienie|
|Http101|HTTP 101|Count|Łącznie|HTTP 101|Wystąpienie|
|Http2xx|HTTP 2xx|Count|Łącznie|HTTP 2xx|Wystąpienie|
|Http3xx|HTTP 3xx|Count|Łącznie|HTTP 3xx|Wystąpienie|
|Http401|HTTP 401|Count|Łącznie|HTTP 401|Wystąpienie|
|Http403|HTTP 403|Count|Łącznie|HTTP 403|Wystąpienie|
|Http404|HTTP 404|Count|Łącznie|HTTP 404|Wystąpienie|
|Http406|HTTP 406|Count|Łącznie|HTTP 406|Wystąpienie|
|Http4xx|HTTP 4xx|Count|Łącznie|HTTP 4xx|Wystąpienie|
|Http5xx|Błędy serwera HTTP|Count|Łącznie|Błędy serwera HTTP|Wystąpienie|
|AverageResponseTime|Średni czas odpowiedzi|Sekundy|Average|Średni czas odpowiedzi|Wystąpienie|
|CpuPercentage|Procent użycia procesora CPU|Percent|Average|Procent użycia procesora CPU|Wystąpienie|
|MemoryPercentage|Procent pamięci|Percent|Average|Procent pamięci|Wystąpienie|
|DiskQueueLength|Długość kolejki dysku|Count|Average|Długość kolejki dysku|Wystąpienie|
|HttpQueueLength|Długość kolejki HTTP|Count|Average|Długość kolejki HTTP|Wystąpienie|
|ActiveRequests|Aktywne żądania|Count|Łącznie|Aktywne żądania|Wystąpienie|
|TotalFrontEnds|Łącznie frontonów|Count|Average|Łącznie frontonów|Nie wymiarów|
|SmallAppServicePlanInstances|Mała liczba procesów roboczych dla planu usługi App Service|Count|Average|Mała liczba procesów roboczych dla planu usługi App Service|Nie wymiarów|
|MediumAppServicePlanInstances|Średnia liczba procesów roboczych dla planu usługi App Service|Count|Average|Średnia liczba procesów roboczych dla planu usługi App Service|Nie wymiarów|
|LargeAppServicePlanInstances|Duża liczba procesów roboczych dla planu usługi App Service|Count|Average|Duża liczba procesów roboczych dla planu usługi App Service|Nie wymiarów|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Łączna|Łączna liczba procesów roboczych|Count|Average|Łączna liczba procesów roboczych|Nie wymiarów|
|WorkersAvailable|Dostępne procesy robocze|Count|Average|Dostępne procesy robocze|Nie wymiarów|
|WorkersUsed|Używane procesy robocze|Count|Average|Używane procesy robocze|Nie wymiarów|
|CpuPercentage|Procent użycia procesora CPU|Percent|Average|Procent użycia procesora CPU|Wystąpienie|
|MemoryPercentage|Procent pamięci|Percent|Average|Procent pamięci|Wystąpienie|

## <a name="next-steps"></a>Następne kroki
* [Przeczytaj informacje o metrykach w Azure Monitor](data-platform.md)
* [Tworzenie alertów dotyczących metryk](alerts-overview.md)
* [Eksportowanie metryk do magazynu, centrum zdarzeń lub Log Analytics](diagnostic-logs-overview.md)
