---
title: Usługa Azure Monitor obsługiwane metryki według typu zasobów
description: Lista dostępnych dla każdego typu zasobu z usługą Azure Monitor metryk.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 70f6e26d423781ba53865304a3fe8440fb120a7a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705170"
---
# <a name="supported-metrics-with-azure-monitor"></a>Obsługiwane metryki z usługą Azure Monitor

Usługa Azure Monitor zapewnia kilka sposobów na korzystanie z metryk, w tym wykresy je w portalu, uzyskując dostęp do nich za pośrednictwem interfejsu API REST lub ich zapytań przy użyciu programu PowerShell lub interfejsu wiersza polecenia. Oto Pełna lista wszystkich metryk jest obecnie dostępna z potoku metryk usługi Azure Monitor. Inne metryki mogą być dostępne w portalu lub przy użyciu starszej wersji interfejsów API. Ta lista poniżej zawiera tylko metryk przy użyciu skonsolidowany potoku metryk usługi Azure Monitor. Użyj kwerendy i dostępem tych metryk [2018-01-01-api-version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Wysyłanie metryk wielowymiarowych za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
>
> *Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak w przypadku eksportowania za pomocą ustawień diagnostycznych metryka ta jest przedstawiana jako wszystkie komunikaty przychodzące we wszystkich kolejkach w centrum zdarzeń.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|Average|QPU. Zakres 0 – 100 dla S1, 0 – 200 dla S2 oraz 0 – 400 dla S4|ServerResourceType|
|memory_metric|Memory (Pamięć)|Bajty|Average|Ilość pamięci. Należeć do zakresu 0 – 25 GB dla S1, 0 – 50 GB dla S2 oraz 0 – 100 GB dla S4|ServerResourceType|
|TotalConnectionRequests|Łączna liczba żądań połączenia|Count|Average|Łączna liczba żądań połączenia. Są to połączenia przyjmowane.|ServerResourceType|
|SuccessfullConnectionsPerSec|Udane połączenia na sekundę|CountPerSecond|Average|Współczynnik pomyślnie zakończonych połączeń.|ServerResourceType|
|TotalConnectionFailures|Łączna liczba błędów połączenia|Count|Average|Łączna liczba nieudanych prób nawiązania połączenia.|ServerResourceType|
|CurrentUserSessions|Bieżące sesje użytkowników|Count|Average|Bieżąca liczba ustanowionych sesji użytkowników.|ServerResourceType|
|QueryPoolBusyThreads|Zajęte wątki puli zapytania|Count|Average|Liczba zajętych wątków w puli wątków zapytania.|ServerResourceType|
|CommandPoolJobQueueLength|Długość kolejki zadań puli polecenia|Count|Average|Liczba zadań w kolejce puli wątków poleceń.|ServerResourceType|
|ProcessingPoolJobQueueLength|Długość kolejki zadań puli przetwarzania|Count|Average|Liczba zadań innych niż we/wy w kolejce puli wątków przetwarzania.|ServerResourceType|
|Wartości CurrentConnections|Połączenie: Bieżąca liczba połączeń|Count|Average|Bieżąca liczba ustanowionych połączeń klientów.|ServerResourceType|
|CleanerCurrentPrice|Pamięć: Bieżąca cena oczyszczarki|Count|Average|Bieżąca cena pamięci $/ bajty/czas, znormalizowana do 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Pamięć: Pamięć oczyszczarki zmniejszania|Bajty|Average|Ilość pamięci w bajtach, podlegająca przeczyszczaniu przez w tle czyszcząca.|ServerResourceType|
|CleanerMemoryNonshrinkable|Pamięć: Pamięć oczyszczarki|Bajty|Average|Ilość pamięci w bajtach, niepodlegająca przeczyszczaniu przez w tle czyszcząca.|ServerResourceType|
|MemoryUsage|Pamięć: Użycie pamięci|Bajty|Average|Użycie pamięci przez proces serwera używane podczas obliczania cena oczyszczarki pamięci. Równe licznikowi Process\PrivateBytes plus rozmiar danych zamapowanych w pamięci, ignorując wszystkie pamięci zamapowanej lub przydzielonej przez aparat analityczny w pamięci xVelocity (VertiPaq) powyżej limitu pamięci aparatu xVelocity.|ServerResourceType|
|MemoryLimitHard|Pamięć: Stały Limit pamięci|Bajty|Average|Stały limit pamięci z pliku konfiguracji.|ServerResourceType|
|MemoryLimitHigh|Pamięć: Górny Limit pamięci|Bajty|Average|Górny limit pamięci z pliku konfiguracji.|ServerResourceType|
|MemoryLimitLow|Pamięć: Dolny Limit pamięci|Bajty|Average|Dolny limit pamięci z pliku konfiguracji.|ServerResourceType|
|MemoryLimitVertiPaq|Pamięć: Limit pamięci aparatu VertiPaq|Bajty|Average|Limit w pamięci z pliku konfiguracji.|ServerResourceType|
|Przydział|Pamięć: Przydział|Bajty|Average|Bieżący limit przydziału pamięci, w bajtach. Limit przydziału pamięci jest również nazywany rezerwacji pamięci lub przydział pamięci.|ServerResourceType|
|QuotaBlocked|Pamięć: Zablokowany limit przydziału|Count|Average|Bieżąca liczba żądań dotyczących limitu przydziału blokowanych do czasu są zwalniane innych limitów przydziału pamięci.|ServerResourceType|
|VertiPaqNonpaged|Pamięć: VertiPaq Nonpaged|Bajty|Average|Bajty pamięci zablokowane w zestawie roboczym do użycia przez aparat w pamięci.|ServerResourceType|
|VertiPaqPaged|Pamięć: VertiPaq Paged|Bajty|Average|Bajty stronicowanej pamięci dla danych w pamięci.|ServerResourceType|
|RowsReadPerSec|Przetwarzanie: Odczytane wiersze na sekundę|CountPerSecond|Average|Współczynnik wierszy odczytanych ze wszystkich relacyjnych baz danych.|ServerResourceType|
|RowsConvertedPerSec|Przetwarzanie: Wiersze przekonwertowane na sekundę|CountPerSecond|Average|Współczynnik wierszy przekonwertowanych podczas przetwarzania.|ServerResourceType|
|RowsWrittenPerSec|Przetwarzanie: Zapisane wiersze na sekundę|CountPerSecond|Average|Współczynnik wierszy zapisanych podczas przetwarzania.|ServerResourceType|
|CommandPoolBusyThreads|Wątki: Zajęte wątki puli polecenia|Count|Average|Liczba zajętych wątków w puli wątków poleceń.|ServerResourceType|
|CommandPoolIdleThreads|Wątki: Bezczynne wątki puli polecenia|Count|Average|Liczba bezczynnych wątków w puli wątków poleceń.|ServerResourceType|
|LongParsingBusyThreads|Wątki: Długotrwałej analizie zajęte wątki|Count|Average|Liczba zajętych wątków w puli wątków o długotrwałej analizie.|ServerResourceType|
|LongParsingIdleThreads|Wątki: Długotrwałej analizie bezczynne wątki|Count|Average|Liczba bezczynnych wątków w puli wątków o długotrwałej analizie.|ServerResourceType|
|LongParsingJobQueueLength|Wątki: Długotrwałej analizie długość kolejki zadań|Count|Average|Liczba zadań w kolejce puli wątków o długotrwałej analizie.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Wątki: Zajęte wątki zadań we/wy puli przetwarzania|Count|Average|Liczba wątków uruchamiających zadania we/wy w puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Wątki: Zajęte wątki zadań innych we/wy puli przetwarzania|Count|Average|Liczba wątków uruchomionych zadań innych niż we/wy w puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Wątki: Długość kolejki zadań we/wy puli przetwarzania|Count|Average|Liczba zadań we/wy w kolejce puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Wątki: Bezczynne wątki zadań we/wy puli przetwarzania|Count|Average|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Wątki: Bezczynne wątki innego niż we/wy puli przetwarzania|Count|Average|Liczba bezczynnych wątków w puli wątków przetwarzania dedykowanych dla zadań innych niż we/wy.|ServerResourceType|
|QueryPoolIdleThreads|Wątki: Bezczynne wątki puli zapytań|Count|Average|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|ServerResourceType|
|QueryPoolJobQueueLength|Wątki: Długość kolejki zadań puli zapytań|Count|Average|Liczba zadań w kolejce puli wątków zapytania.|ServerResourceType|
|ShortParsingBusyThreads|Wątki: Krótkie analizy zajęte wątki|Count|Average|Liczba zajętych wątków w puli wątków krótkotrwałej analizie.|ServerResourceType|
|ShortParsingIdleThreads|Wątki: Krótkie analizy bezczynne wątki|Count|Average|Liczba bezczynnych wątków w puli wątków krótkotrwałej analizie.|ServerResourceType|
|ShortParsingJobQueueLength|Wątki: Krótkie analizy długość kolejki zadań|Count|Average|Liczba zadań w kolejce puli wątków krótkotrwałej analizie.|ServerResourceType|
|memory_thrashing_metric|Przeładowywanie pamięci|Percent|Average|Średnie przeładowywanie pamięci.|ServerResourceType|
|mashup_engine_qpu_metric|Jednostka QPU aparatu M|Count|Average|Użycie jednostek QPU przez procesy aparatu mashupów|ServerResourceType|
|mashup_engine_memory_metric|Pamięć aparatu M|Bajty|Average|Użycie pamięci przez procesy aparatu mashupów|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalRequests|Łącznie żądań bramy|Count|Łącznie|Liczba żądań bramy|Lokalizacja i nazwa hosta|
|SuccessfulRequests|Pomyślne żądania bramy|Count|Łącznie|Liczba pomyślne żądania bramy|Lokalizacja i nazwa hosta|
|UnauthorizedRequests|Nieautoryzowane żądania bramy|Count|Łącznie|Liczba nieautoryzowane żądania bramy|Lokalizacja i nazwa hosta|
|FailedRequests|Żądania bramy zakończone niepowodzeniem|Count|Łącznie|Liczba błędów w żądaniach bramy|Lokalizacja i nazwa hosta|
|OtherRequests|Inne żądania bramy|Count|Łącznie|Liczba inne żądania bramy|Lokalizacja i nazwa hosta|
|Duration|Całkowity czas trwania żądań bramy|MS|Average|Ogólny czas trwania dla żądania bramy (w milisekundach)|Lokalizacja i nazwa hosta|
|Pojemność|Pojemność|Percent|Average|Metryki dla usługi ApiManagement|Location|
|EventHubTotalEvents|Łączna liczba centrów zdarzeń|Count|Łącznie|Liczba zdarzeń wysłanych do Centrum zdarzeń|Location|
|EventHubSuccessfulEvents|Pomyślne centrów zdarzeń|Count|Łącznie|Liczba pomyślnych zdarzeń Centrum zdarzeń|Location|
|EventHubTotalFailedEvents|Zdarzenia Centrum zdarzeń nie powiodło się|Count|Łącznie|Liczba zakończonych niepowodzeniem zdarzeń usługi Event Hub|Location|
|EventHubRejectedEvents|Odrzucone centrów zdarzeń|Count|Łącznie|Liczba odrzuconych zdarzeń usługi Event Hub (niewłaściwa konfiguracja lub brak autoryzacji)|Location|
|EventHubThrottledEvents|Zdarzenia ograniczenia usługi EventHub|Count|Łącznie|Numer zdarzenia ograniczenia usługi EventHub|Location|
|EventHubTimedoutEvents|Przekroczono limit zdarzeń usługi Event Hub|Count|Łącznie|Liczba Przekroczono limit zdarzeń usługi Event Hub|Location|
|EventHubDroppedEvents|Zdarzenia porzucone Centrum zdarzeń|Count|Łącznie|Liczba zdarzeń pominięte z powodu osiągnięto limit rozmiaru w kolejce|Location|
|EventHubTotalBytesSent|Rozmiar zdarzeń usługi Event Hub|Bajty|Łącznie|Całkowity rozmiar zdarzeń Centrum zdarzeń w bajtach|Location|
|Żądania|Żądania|Count|Łącznie|Żądania bramy|Lokalizacja, BackendResponseCode, LastErrorReason, GatewayResponseCode|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalJob|Łączna liczba zadań|Count|Łącznie|Całkowita liczba zadań|Element Runbook, stan|
|TotalUpdateDeploymentRuns|Przebiegi wdrażania aktualizacji całkowity|Count|Łącznie|Uruchamia wdrożenie aktualizacji oprogramowania całkowity|SoftwareUpdateConfigurationName, Status|
|TotalUpdateDeploymentMachineRuns|Łączna liczba aktualizacji wdrożenia maszyny przebiegów|Count|Łącznie|Komputer do wdrożenia aktualizacji oprogramowania całkowita jest uruchamiany w uruchomienia wdrożenia aktualizacji oprogramowania|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CoreCount|Liczba rdzeni dedykowanych|Count|Łącznie|Całkowita liczba dedykowanych rdzeni na koncie usługi batch|Nie wymiarów|
|TotalNodeCount|Liczba dedykowanych węzłów|Count|Łącznie|Całkowita liczba dedykowanych węzłów na koncie usługi batch|Nie wymiarów|
|LowPriorityCoreCount|Liczba rdzeni LowPriority|Count|Łącznie|Łączna liczba rdzeni o niskim priorytecie na koncie usługi batch|Nie wymiarów|
|TotalLowPriorityNodeCount|Liczba węzłów o niskim priorytecie|Count|Łącznie|Całkowita liczba węzłów o niskim priorytecie na koncie usługi batch|Nie wymiarów|
|CreatingNodeCount|Tworzenie liczba węzłów|Count|Łącznie|Liczbę węzłów tworzonych|Nie wymiarów|
|StartingNodeCount|Początkowa liczba węzłów|Count|Łącznie|Liczba węzłów uruchamiania|Nie wymiarów|
|WaitingForStartTaskNodeCount|Oczekiwanie liczba węzła zadania uruchamiania|Count|Łącznie|Liczba węzłów oczekiwanie na zakończenie zadania Start|Nie wymiarów|
|StartTaskFailedNodeCount|Zadanie podrzędne uruchamiania nie powiodło się liczba węzłów|Count|Łącznie|Liczba węzłów, w którym Rozpocznij zadanie nie powiodło się|Nie wymiarów|
|IdleNodeCount|Liczba bezczynnych węzłów|Count|Łącznie|Liczba bezczynnych węzłów|Nie wymiarów|
|OfflineNodeCount|Liczba węzłów w trybie offline|Count|Łącznie|Liczba węzłów w trybie offline|Nie wymiarów|
|RebootingNodeCount|Ponowne uruchamianie liczba węzłów|Count|Łącznie|Liczba ponownego uruchomienia węzłów|Nie wymiarów|
|ReimagingNodeCount|Odtwarzanie z obrazu liczba węzłów|Count|Łącznie|Liczba węzłów odtwarzanie z obrazu|Nie wymiarów|
|RunningNodeCount|Liczba węzłów|Count|Łącznie|Liczba uruchomionych węzłach|Nie wymiarów|
|LeavingPoolNodeCount|Pozostawienie liczby węzłów w puli|Count|Łącznie|Liczba węzłów opuszczanie puli|Nie wymiarów|
|UnusableNodeCount|Korzystanie z tej liczby węzłów|Count|Łącznie|Liczba węzłów bezużyteczne|Nie wymiarów|
|PreemptedNodeCount|Przerywane liczba węzłów|Count|Łącznie|Liczba węzłów przeniesiona|Nie wymiarów|
|TaskStartEvent|Zadanie rozpoczęcia zdarzenia|Count|Łącznie|Całkowita liczba zadań, które zostały uruchomione|Nie wymiarów|
|TaskCompleteEvent|Zakończenie zdarzenia zadań|Count|Łącznie|Całkowita liczba zadań, które zostały ukończone|Nie wymiarów|
|TaskFailEvent|Zdarzenia błędów zadań|Count|Łącznie|Całkowita liczba zadań, które zostały wykonane w stanie Niepowodzenie|Nie wymiarów|
|PoolCreateEvent|Zdarzenia utworzenia puli|Count|Łącznie|Łączna liczba pul, które zostały utworzone|Nie wymiarów|
|PoolResizeStartEvent|Zdarzenia rozpoczęcia zmiany rozmiaru puli|Count|Łącznie|Łączna liczba zmienia rozmiar puli, które zostały uruchomione|Nie wymiarów|
|PoolResizeCompleteEvent|Zdarzenia pełną zmiany rozmiaru puli|Count|Łącznie|Łączna liczba zmienia rozmiar puli, które zostały ukończone|Nie wymiarów|
|PoolDeleteStartEvent|Zdarzenia rozpoczęcia usuwania puli|Count|Łącznie|Łączna liczba usuwa puli, które zostały uruchomione|Nie wymiarów|
|PoolDeleteCompleteEvent|Zdarzenia pełną usuwania puli|Count|Łącznie|Łączna liczba usuwa puli, które zostały ukończone|Nie wymiarów|
|JobDeleteCompleteEvent|Zadania Usuń zakończenie zdarzenia|Count|Łącznie|Całkowita liczba zadań, które zostały pomyślnie usunięte.|Nie wymiarów|
|JobDeleteStartEvent|Zadania Usuń rozpoczęcia zdarzenia|Count|Łącznie|Całkowita liczba zadań, które zostały wymagane do usunięcia.|Nie wymiarów|
|JobDisableCompleteEvent|Zdarzenia pełne wyłączenie zadań|Count|Łącznie|Całkowita liczba zadań, które zostały pomyślnie wyłączone.|Nie wymiarów|
|JobDisableStartEvent|Zadanie Wyłącz rozpoczęcia zdarzenia|Count|Łącznie|Całkowita liczba zadań, wymagające być wyłączona.|Nie wymiarów|
|JobStartEvent|Zadanie rozpoczęcia zdarzenia|Count|Łącznie|Całkowita liczba zadań, które zostało uruchomione pomyślnie.|Nie wymiarów|
|JobTerminateCompleteEvent|Zadanie zakończenia zdarzenia ukończone|Count|Łącznie|Całkowita liczba zadań, które zostały pomyślnie zakończone.|Nie wymiarów|
|JobTerminateStartEvent|Zadanie zakończenia zdarzenia rozpoczęcia|Count|Łącznie|Całkowita liczba zadań, wymagające ma zostać zakończony.|Nie wymiarów|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|connectedclients|Podłączeni klienci|Count|Maksimum||ShardId|
|totalcommandsprocessed|Łączna liczba operacji|Count|Łącznie||ShardId|
|Trafienia w pamięci podręcznej|Trafienia w pamięci podręcznej|Count|Łącznie||ShardId|
|cachemisses|Chybienia w pamięci podręcznej|Count|Łącznie||ShardId|
|getcommands|Pobiera|Count|Łącznie||ShardId|
|setcommands|Zestawy|Count|Łącznie||ShardId|
|operationsPerSecond|Operacje na sekundę|Count|Maksimum||ShardId|
|evictedkeys|Wykluczone klucze|Count|Łącznie||ShardId|
|totalkeys|Całkowita liczba kluczy|Count|Maksimum||ShardId|
|expiredkeys|Wygasłe klucze|Count|Łącznie||ShardId|
|usedmemory|Używana pamięć|Bajty|Maksimum||ShardId|
|usedmemorypercentage|Procent używanej pamięci|Percent|Maksimum||ShardId|
|usedmemoryRss|Używana pamięć RSS|Bajty|Maksimum||ShardId|
|serverLoad|Obciążenie serwera|Percent|Maksimum||ShardId|
|cacheWrite|Zapis w pamięci podręcznej|BytesPerSecond|Maksimum||ShardId|
|cacheRead|Odczyt pamięci podręcznej|BytesPerSecond|Maksimum||ShardId|
|percentProcessorTime|Procesor CPU|Percent|Maksimum||ShardId|
|cacheLatency|Pamięć podręczna opóźnienie mikrosekund (wersja zapoznawcza)|Count|Average||ShardId, SampleType|
|błędy|Błędy|Count|Maksimum||ShardId, ErrorType|
|connectedclients0|Podłączeni klienci (fragmencie 0)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed0|Łączna liczba operacji (fragmencie 0)|Count|Łącznie||Nie wymiarów|
|cachehits0|Trafienia w pamięci podręcznej (fragmencie 0)|Count|Łącznie||Nie wymiarów|
|cachemisses0|Chybienia w pamięci podręcznej (fragmencie 0)|Count|Łącznie||Nie wymiarów|
|getcommands0|Pobiera (fragmencie 0)|Count|Łącznie||Nie wymiarów|
|setcommands0|Zestawy (fragmencie 0)|Count|Łącznie||Nie wymiarów|
|operationsPerSecond0|Operacje na sekundę (fragmencie 0)|Count|Maksimum||Nie wymiarów|
|evictedkeys0|Wykluczone klucze (fragmencie 0)|Count|Łącznie||Nie wymiarów|
|totalkeys0|Całkowita liczba kluczy (fragmencie 0)|Count|Maksimum||Nie wymiarów|
|expiredkeys0|Wygasłe klucze (fragmencie 0)|Count|Łącznie||Nie wymiarów|
|usedmemory0|Używana pamięć (fragmencie 0)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss0|Używana pamięć RSS (fragmencie 0)|Bajty|Maksimum||Nie wymiarów|
|serverLoad0|Obciążenie serwera (fragmencie 0)|Percent|Maksimum||Nie wymiarów|
|cacheWrite0|Zapis w pamięci podręcznej (fragmencie 0)|BytesPerSecond|Maksimum||Nie wymiarów|
|cacheRead0|Odczyt pamięci podręcznej (fragmencie 0)|BytesPerSecond|Maksimum||Nie wymiarów|
|percentProcessorTime0|Procesor (fragmencie 0)|Percent|Maksimum||Nie wymiarów|
|connectedclients1|Podłączeni klienci (fragmencie 1)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed1|Łączna liczba operacji (fragmencie 1)|Count|Łącznie||Nie wymiarów|
|cachehits1|Trafienia w pamięci podręcznej (fragmencie 1)|Count|Łącznie||Nie wymiarów|
|cachemisses1|Chybienia w pamięci podręcznej (fragmencie 1)|Count|Łącznie||Nie wymiarów|
|getcommands1|Pobiera (fragmencie 1)|Count|Łącznie||Nie wymiarów|
|setcommands1|Zestawy (fragmencie 1)|Count|Łącznie||Nie wymiarów|
|operationsPerSecond1|Operacje na sekundę (fragmencie 1)|Count|Maksimum||Nie wymiarów|
|evictedkeys1|Wykluczone klucze (fragmencie 1)|Count|Łącznie||Nie wymiarów|
|totalkeys1|Całkowita liczba kluczy (fragmencie 1)|Count|Maksimum||Nie wymiarów|
|expiredkeys1|Wygasłe klucze (fragmencie 1)|Count|Łącznie||Nie wymiarów|
|usedmemory1|Używana pamięć (fragmencie 1)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss1|Używana pamięć RSS (fragmencie 1)|Bajty|Maksimum||Nie wymiarów|
|serverLoad1|Obciążenie serwera (fragmencie 1)|Percent|Maksimum||Nie wymiarów|
|cacheWrite1|Zapis w pamięci podręcznej (fragmencie 1)|BytesPerSecond|Maksimum||Nie wymiarów|
|cacheRead1|Odczyt pamięci podręcznej (fragmencie 1)|BytesPerSecond|Maksimum||Nie wymiarów|
|percentProcessorTime1|Procesor (fragmencie 1)|Percent|Maksimum||Nie wymiarów|
|connectedclients2|Podłączeni klienci (fragmencie 2)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed2|Łączna liczba operacji (fragmencie 2)|Count|Łącznie||Nie wymiarów|
|cachehits2|Trafienia w pamięci podręcznej (fragmencie 2)|Count|Łącznie||Nie wymiarów|
|cachemisses2|Chybienia w pamięci podręcznej (fragmencie 2)|Count|Łącznie||Nie wymiarów|
|getcommands2|Pobiera (fragmencie 2)|Count|Łącznie||Nie wymiarów|
|setcommands2|Zestawy (fragmencie 2)|Count|Łącznie||Nie wymiarów|
|operationsPerSecond2|Operacje na sekundę (fragmencie 2)|Count|Maksimum||Nie wymiarów|
|evictedkeys2|Wykluczone klucze (fragmencie 2)|Count|Łącznie||Nie wymiarów|
|totalkeys2|Całkowita liczba kluczy (fragmencie 2)|Count|Maksimum||Nie wymiarów|
|expiredkeys2|Wygasłe klucze (fragmencie 2)|Count|Łącznie||Nie wymiarów|
|usedmemory2|Używana pamięć (fragmencie 2)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss2|Używana pamięć RSS (fragmencie 2)|Bajty|Maksimum||Nie wymiarów|
|serverLoad2|Obciążenie serwera (fragmencie 2)|Percent|Maksimum||Nie wymiarów|
|cacheWrite2|Zapis w pamięci podręcznej (fragmencie 2)|BytesPerSecond|Maksimum||Nie wymiarów|
|cacheRead2|Odczyt pamięci podręcznej (fragmencie 2)|BytesPerSecond|Maksimum||Nie wymiarów|
|percentProcessorTime2|Procesor (fragmencie 2)|Percent|Maksimum||Nie wymiarów|
|connectedclients3|Podłączeni klienci (fragmencie 3)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed3|Łączna liczba operacji (fragmencie 3)|Count|Łącznie||Nie wymiarów|
|cachehits3|Trafienia w pamięci podręcznej (fragmencie 3)|Count|Łącznie||Nie wymiarów|
|cachemisses3|Chybienia w pamięci podręcznej (fragmencie 3)|Count|Łącznie||Nie wymiarów|
|getcommands3|Pobiera (fragmencie 3)|Count|Łącznie||Nie wymiarów|
|setcommands3|Zestawy (fragmencie 3)|Count|Łącznie||Nie wymiarów|
|operationsPerSecond3|Operacje na sekundę (fragmencie 3)|Count|Maksimum||Nie wymiarów|
|evictedkeys3|Wykluczone klucze (fragmencie 3)|Count|Łącznie||Nie wymiarów|
|totalkeys3|Całkowita liczba kluczy (fragmencie 3)|Count|Maksimum||Nie wymiarów|
|expiredkeys3|Wygasłe klucze (fragmencie 3)|Count|Łącznie||Nie wymiarów|
|usedmemory3|Używana pamięć (fragmencie 3)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss3|Używana pamięć RSS (fragmencie 3)|Bajty|Maksimum||Nie wymiarów|
|serverLoad3|Obciążenie serwera (fragmencie 3)|Percent|Maksimum||Nie wymiarów|
|cacheWrite3|Zapis w pamięci podręcznej (fragmencie 3)|BytesPerSecond|Maksimum||Nie wymiarów|
|cacheRead3|Odczyt pamięci podręcznej (fragmencie 3)|BytesPerSecond|Maksimum||Nie wymiarów|
|percentProcessorTime3|Procesor (fragmencie 3)|Percent|Maksimum||Nie wymiarów|
|connectedclients4|Podłączeni klienci (fragmencie 4)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed4|Łączna liczba operacji (fragmencie 4)|Count|Łącznie||Nie wymiarów|
|cachehits4|Trafienia w pamięci podręcznej (fragmencie 4)|Count|Łącznie||Nie wymiarów|
|cachemisses4|Chybienia w pamięci podręcznej (fragmencie 4)|Count|Łącznie||Nie wymiarów|
|getcommands4|Pobiera (fragmencie 4)|Count|Łącznie||Nie wymiarów|
|setcommands4|Zestawy (fragmencie 4)|Count|Łącznie||Nie wymiarów|
|operationsPerSecond4|Operacje na sekundę (fragmencie 4)|Count|Maksimum||Nie wymiarów|
|evictedkeys4|Wykluczone klucze (fragmencie 4)|Count|Łącznie||Nie wymiarów|
|totalkeys4|Całkowita liczba kluczy (fragmencie 4)|Count|Maksimum||Nie wymiarów|
|expiredkeys4|Wygasłe klucze (fragmencie 4)|Count|Łącznie||Nie wymiarów|
|usedmemory4|Używana pamięć (fragmencie 4)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss4|Używana pamięć RSS (fragmencie 4)|Bajty|Maksimum||Nie wymiarów|
|serverLoad4|Obciążenie serwera (fragmencie 4)|Percent|Maksimum||Nie wymiarów|
|cacheWrite4|Zapis w pamięci podręcznej (fragmencie 4)|BytesPerSecond|Maksimum||Nie wymiarów|
|cacheRead4|Odczyt pamięci podręcznej (fragmencie 4)|BytesPerSecond|Maksimum||Nie wymiarów|
|percentProcessorTime4|Procesor (fragmencie 4)|Percent|Maksimum||Nie wymiarów|
|connectedclients5|Podłączeni klienci (fragmencie 5)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed5|Łączna liczba operacji (fragmencie 5)|Count|Łącznie||Nie wymiarów|
|cachehits5|Trafienia w pamięci podręcznej (fragmencie 5)|Count|Łącznie||Nie wymiarów|
|cachemisses5|Chybienia w pamięci podręcznej (fragmencie 5)|Count|Łącznie||Nie wymiarów|
|getcommands5|Pobiera (fragmencie 5)|Count|Łącznie||Nie wymiarów|
|setcommands5|Zestawy (fragmencie 5)|Count|Łącznie||Nie wymiarów|
|operationsPerSecond5|Operacje na sekundę (fragmencie 5)|Count|Maksimum||Nie wymiarów|
|evictedkeys5|Wykluczone klucze (fragmencie 5)|Count|Łącznie||Nie wymiarów|
|totalkeys5|Całkowita liczba kluczy (fragmencie 5)|Count|Maksimum||Nie wymiarów|
|expiredkeys5|Wygasłe klucze (fragmencie 5)|Count|Łącznie||Nie wymiarów|
|usedmemory5|Używana pamięć (fragmencie 5)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss5|Używana pamięć RSS (fragmencie 5)|Bajty|Maksimum||Nie wymiarów|
|serverLoad5|Obciążenie serwera (fragmencie 5)|Percent|Maksimum||Nie wymiarów|
|cacheWrite5|Zapis w pamięci podręcznej (fragmencie 5)|BytesPerSecond|Maksimum||Nie wymiarów|
|cacheRead5|Odczyt pamięci podręcznej (fragmencie 5)|BytesPerSecond|Maksimum||Nie wymiarów|
|percentProcessorTime5|Procesor (fragmencie 5)|Percent|Maksimum||Nie wymiarów|
|connectedclients6|Podłączeni klienci (fragmencie 6)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed6|Łączna liczba operacji (fragmencie 6)|Count|Łącznie||Nie wymiarów|
|cachehits6|Trafienia w pamięci podręcznej (fragmencie 6)|Count|Łącznie||Nie wymiarów|
|cachemisses6|Chybienia w pamięci podręcznej (fragmencie 6)|Count|Łącznie||Nie wymiarów|
|getcommands6|Pobiera (fragmencie 6)|Count|Łącznie||Nie wymiarów|
|setcommands6|Zestawy (fragmencie 6)|Count|Łącznie||Nie wymiarów|
|operationsPerSecond6|Operacje na sekundę (fragmencie 6)|Count|Maksimum||Nie wymiarów|
|evictedkeys6|Wykluczone klucze (fragmencie 6)|Count|Łącznie||Nie wymiarów|
|totalkeys6|Całkowita liczba kluczy (fragmencie 6)|Count|Maksimum||Nie wymiarów|
|expiredkeys6|Wygasłe klucze (fragmencie 6)|Count|Łącznie||Nie wymiarów|
|usedmemory6|Używana pamięć (fragmencie 6)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss6|Używana pamięć RSS (fragmencie 6)|Bajty|Maksimum||Nie wymiarów|
|serverLoad6|Obciążenie serwera (fragmencie 6)|Percent|Maksimum||Nie wymiarów|
|cacheWrite6|Zapis w pamięci podręcznej (fragmencie 6)|BytesPerSecond|Maksimum||Nie wymiarów|
|cacheRead6|Odczyt pamięci podręcznej (fragmencie 6)|BytesPerSecond|Maksimum||Nie wymiarów|
|percentProcessorTime6|Procesor (fragmencie 6)|Percent|Maksimum||Nie wymiarów|
|connectedclients7|Podłączeni klienci (fragmencie 7)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed7|Łączna liczba operacji (fragmencie 7)|Count|Łącznie||Nie wymiarów|
|cachehits7|Trafienia w pamięci podręcznej (fragmencie 7)|Count|Łącznie||Nie wymiarów|
|cachemisses7|Chybienia w pamięci podręcznej (fragmencie 7)|Count|Łącznie||Nie wymiarów|
|getcommands7|Pobiera (fragmencie 7)|Count|Łącznie||Nie wymiarów|
|setcommands7|Zestawy (fragmencie 7)|Count|Łącznie||Nie wymiarów|
|operationsPerSecond7|Operacje na sekundę (fragmencie 7)|Count|Maksimum||Nie wymiarów|
|evictedkeys7|Wykluczone klucze (fragmencie 7)|Count|Łącznie||Nie wymiarów|
|totalkeys7|Całkowita liczba kluczy (fragmencie 7)|Count|Maksimum||Nie wymiarów|
|expiredkeys7|Wygasłe klucze (fragmencie 7)|Count|Łącznie||Nie wymiarów|
|usedmemory7|Używana pamięć (fragmencie 7)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss7|Używana pamięć RSS (fragmencie 7)|Bajty|Maksimum||Nie wymiarów|
|serverLoad7|Obciążenie serwera (fragmencie 7)|Percent|Maksimum||Nie wymiarów|
|cacheWrite7|Zapis w pamięci podręcznej (fragmencie 7)|BytesPerSecond|Maksimum||Nie wymiarów|
|cacheRead7|Odczyt pamięci podręcznej (fragmencie 7)|BytesPerSecond|Maksimum||Nie wymiarów|
|percentProcessorTime7|Procesor (fragmencie 7)|Percent|Maksimum||Nie wymiarów|
|connectedclients8|Podłączeni klienci (fragmencie 8)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed8|Łączna liczba operacji (fragmencie 8)|Count|Łącznie||Nie wymiarów|
|cachehits8|Trafienia w pamięci podręcznej (fragmencie 8)|Count|Łącznie||Nie wymiarów|
|cachemisses8|Chybienia w pamięci podręcznej (fragmencie 8)|Count|Łącznie||Nie wymiarów|
|getcommands8|Pobiera (fragmencie 8)|Count|Łącznie||Nie wymiarów|
|setcommands8|Zestawy (fragmencie 8)|Count|Łącznie||Nie wymiarów|
|operationsPerSecond8|Operacje na sekundę (fragmencie 8)|Count|Maksimum||Nie wymiarów|
|evictedkeys8|Wykluczone klucze (fragmencie 8)|Count|Łącznie||Nie wymiarów|
|totalkeys8|Całkowita liczba kluczy (fragmencie 8)|Count|Maksimum||Nie wymiarów|
|expiredkeys8|Wygasłe klucze (fragmencie 8)|Count|Łącznie||Nie wymiarów|
|usedmemory8|Używana pamięć (fragmencie 8)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss8|Używana pamięć RSS (fragmencie 8)|Bajty|Maksimum||Nie wymiarów|
|serverLoad8|Obciążenie serwera (fragmencie 8)|Percent|Maksimum||Nie wymiarów|
|cacheWrite8|Zapis w pamięci podręcznej (fragmencie 8)|BytesPerSecond|Maksimum||Nie wymiarów|
|cacheRead8|Odczyt pamięci podręcznej (fragmencie 8)|BytesPerSecond|Maksimum||Nie wymiarów|
|percentProcessorTime8|Procesor (fragmencie 8)|Percent|Maksimum||Nie wymiarów|
|connectedclients9|Podłączeni klienci (fragmencie 9)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed9|Łączna liczba operacji (fragmencie 9)|Count|Łącznie||Nie wymiarów|
|cachehits9|Trafienia w pamięci podręcznej (fragmencie 9)|Count|Łącznie||Nie wymiarów|
|cachemisses9|Chybienia w pamięci podręcznej (fragmencie 9)|Count|Łącznie||Nie wymiarów|
|getcommands9|Pobiera (fragmencie 9)|Count|Łącznie||Nie wymiarów|
|setcommands9|Zestawy (fragmencie 9)|Count|Łącznie||Nie wymiarów|
|operationsPerSecond9|Operacje na sekundę (fragmencie 9)|Count|Maksimum||Nie wymiarów|
|evictedkeys9|Wykluczone klucze (fragmencie 9)|Count|Łącznie||Nie wymiarów|
|totalkeys9|Całkowita liczba kluczy (fragmencie 9)|Count|Maksimum||Nie wymiarów|
|expiredkeys9|Wygasłe klucze (fragmencie 9)|Count|Łącznie||Nie wymiarów|
|usedmemory9|Używana pamięć (fragmencie 9)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss9|Używana pamięć RSS (fragmencie 9)|Bajty|Maksimum||Nie wymiarów|
|serverLoad9|Obciążenie serwera (fragmencie 9)|Percent|Maksimum||Nie wymiarów|
|cacheWrite9|Zapis w pamięci podręcznej (fragmencie 9)|BytesPerSecond|Maksimum||Nie wymiarów|
|cacheRead9|Odczyt pamięci podręcznej (fragmencie 9)|BytesPerSecond|Maksimum||Nie wymiarów|
|percentProcessorTime9|Procesor (fragmencie 9)|Percent|Maksimum||Nie wymiarów|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Average|Procent przydzielonych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne.|Nie wymiarów|
|Sieć — wejście|Sieć — wejście|Bajty|Łącznie|Liczba bajtów odebranych ze wszystkich interfejsów sieciowych przez maszyny wirtualne (ruch przychodzący).|Nie wymiarów|
|Sieć — wyjście|Sieć — wyjście|Bajty|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący).|Nie wymiarów|
|Bajty odczytu dysku/s|Odczyt z dysku|BytesPerSecond|Average|Średnia liczba bajtów odczytanych z dysku w okresie monitorowania.|Nie wymiarów|
|Bajty zapisu dysku/s|Zapis na dysku|BytesPerSecond|Average|Średnia liczba bajtów zapisanych na dysku w okresie monitorowania.|Nie wymiarów|
|Dysku, operacje odczytu/s|Dysku, operacje odczytu/s|CountPerSecond|Average|Disk Read IOPS.|Nie wymiarów|
|Operacje zapisu dysku/s|Operacje zapisu dysku/s|CountPerSecond|Average|Zapisu operacji We/Wy na dysku.|Nie wymiarów|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Average|Procent przydzielonych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne.|RoleInstanceId|
|Sieć — wejście|Sieć — wejście|Bajty|Łącznie|Liczba bajtów odebranych ze wszystkich interfejsów sieciowych przez maszyny wirtualne (ruch przychodzący).|RoleInstanceId|
|Sieć — wyjście|Sieć — wyjście|Bajty|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący).|RoleInstanceId|
|Bajty odczytu dysku/s|Odczyt z dysku|BytesPerSecond|Average|Średnia liczba bajtów odczytanych z dysku w okresie monitorowania.|RoleInstanceId|
|Bajty zapisu dysku/s|Zapis na dysku|BytesPerSecond|Average|Średnia liczba bajtów zapisanych na dysku w okresie monitorowania.|RoleInstanceId|
|Dysku, operacje odczytu/s|Dysku, operacje odczytu/s|CountPerSecond|Average|Disk Read IOPS.|RoleInstanceId|
|Operacje zapisu dysku/s|Operacje zapisu dysku/s|CountPerSecond|Average|Zapisu operacji We/Wy na dysku.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalCalls|Łączna liczba wywołań|Count|Łącznie|Łączna liczba wywołań.|Region ApiName, OperationName,|
|SuccessfulCalls|Pomyślnych wywołań|Count|Łącznie|Liczba wywołań zakończonych powodzeniem.|Region ApiName, OperationName,|
|TotalErrors|Całkowita liczba błędów|Count|Łącznie|Łączna liczba wywołań z odpowiedzią oznaczającą błąd (odpowiedź HTTP o kodzie 4xx lub 5xx).|Region ApiName, OperationName,|
|BlockedCalls|Zablokowane wywołania|Count|Łącznie|Liczba wywołań, że przekroczono limit szybkości lub przydziału.|Region ApiName, OperationName,|
|ServerErrors|Błędy serwera|Count|Łącznie|Liczba wywołań z błędem wewnętrznym usługi (odpowiedź HTTP o kodzie 5xx).|Region ApiName, OperationName,|
|ClientErrors|Błędy klienta|Count|Łącznie|Liczba wywołań z błędem po stronie klienta (HTTP odpowiedzi o kodzie 4xx).|Region ApiName, OperationName,|
|DataIn|Dane w|Bajty|Łącznie|Rozmiar danych przychodzących w bajtach.|Region ApiName, OperationName,|
|DataOut|Dane wyjściowe|Bajty|Łącznie|Rozmiar danych wychodzących w bajtach.|Region ApiName, OperationName,|
|Opóźnienie|Opóźnienie|MilliSeconds|Average|Opóźnienie w milisekundach.|Region ApiName, OperationName,|
|CharactersTranslated|Przetłumaczone znaki|Count|Łącznie|Całkowita liczba znaków w przychodzącym żądaniu tekstowym.|Region ApiName, OperationName,|
|CharactersTrained|Skonfigurowanych pod kątem znaków|Count|Łącznie|Całkowita liczba skonfigurowanych pod kątem znaków.|Region ApiName, OperationName,|
|SpeechSessionDuration|Mowy czas trwania sesji|Sekundy|Łącznie|Łączny czas trwania sesji mowy w sekundach.|Region ApiName, OperationName,|
|TotalTransactions|Łączna liczba transakcji|Count|Łącznie|Całkowita liczba transakcji.|Nie wymiarów|
|TotalTokenCalls|Łączna liczba wywołań tokenu|Count|Łącznie|Łączna liczba wywołań tokenu.|Region ApiName, OperationName,|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Średnia|Procent przydzielonych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne|Nie wymiarów|
|Sieć — wejście|Sieć w płatnych|Bajty|Łącznie|Liczba płatnych bajtów odebranych ze wszystkich interfejsów sieciowych przez maszyny wirtualne (ruch przychodzący)|Nie wymiarów|
|Sieć — wyjście|Sieci poza płatne|Bajty|Łącznie|Liczba płatnych bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący)|Nie wymiarów|
|Bajty odczytane z dysku|Bajty odczytane z dysku|Bajty|Łącznie|Bajty odczytane z dysku w okresie monitorowania|Nie wymiarów|
|Bajty zapisane na dysku|Bajty zapisane na dysku|Bajty|Łącznie|Bajty zapisane na dysku w okresie monitorowania|Nie wymiarów|
|Dysku, operacje odczytu/s|Dysku, operacje odczytu/s|CountPerSecond|Average|Odczyt z dysku na SEKUNDĘ|Nie wymiarów|
|Operacje zapisu dysku/s|Operacje zapisu dysku/s|CountPerSecond|Average|Operacje We/Wy zapisu na dysku|Nie wymiarów|
|Pozostałe środki na korzystanie z procesora CPU|Pozostałe środki na korzystanie z procesora CPU|Count|Average|Łączna ilość środków dostępnych na potrzeby serii|Nie wymiarów|
|Środki na procesory CPU wykorzystany|Środki na procesory CPU wykorzystany|Count|Average|Łączna ilość środków wykorzystanych przez maszynę wirtualną|Nie wymiarów|
|Na bajty odczytu dysku/s|Dane Bajty odczytu dysku/s (przestarzałe)|CountPerSecond|Average|Bajtów na sekundę odczytanych z pojedynczego dysku w okresie monitorowania|Identyfikator_gniazda|
|Na bajty zapisu dysku/s|Dane Bajty zapisu dysku/s (przestarzałe)|CountPerSecond|Average|Bajtów na sekundę zapisanych na jednym dysku w okresie monitorowania|Identyfikator_gniazda|
|Na dysku, operacje odczytu/s|Dysk danych, operacje odczytu/s (przestarzałe)|CountPerSecond|Average|Operacje We/Wy odczytu z pojedynczego dysku w okresie monitorowania|Identyfikator_gniazda|
|Za operacje zapisu dysku/s|Dane dysku operacje zapisu/s (przestarzałe)|CountPerSecond|Average|Zapis operacji We/Wy z pojedynczego dysku w okresie monitorowania|Identyfikator_gniazda|
|Głębokość kolejki dysku|(Przestarzałe) głębokość kolejki dysku danych|Count|Average|Głębokość kolejki dysku danych (lub długość kolejki)|Identyfikator_gniazda|
|System operacyjny na bajty odczytu dysku/s|Dysk systemu operacyjnego odczytu bajtów na sekundę (przestarzałe)|CountPerSecond|Average|Bajtów na sekundę odczytanych z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Bajtów na sekundę zapisu na dysku systemu operacyjnego|System operacyjny Bajty zapisu dysku/s (przestarzałe)|CountPerSecond|Average|Bajtów na sekundę zapisanych na jednym dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|System operacyjny na operacje odczytu dysku/s|Dysk systemu operacyjnego operacje odczytu/s (przestarzałe)|CountPerSecond|Average|Operacje We/Wy odczytu z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Na dysku systemu operacyjnego operacje zapisu/s|Dysk systemu operacyjnego (przestarzałe) operacje zapisu/s|CountPerSecond|Average|Zapis operacji We/Wy z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Na głębokość kolejki dysku systemu operacyjnego|Dysk systemu operacyjnego głębokość kolejki (przestarzałe)|Count|Average|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|Nie wymiarów|
|Dane Bajty odczytu dysku/s|Dane Bajty odczytu dysku/s (wersja zapoznawcza)|CountPerSecond|Average|Bajtów na sekundę odczytanych z pojedynczego dysku w okresie monitorowania|JEDNOSTKA LUN|
|Bajty zapisu dysku danych/s|Dane Bajty zapisu dysku/s (wersja zapoznawcza)|CountPerSecond|Average|Bajtów na sekundę zapisanych na jednym dysku w okresie monitorowania|JEDNOSTKA LUN|
|Dysk danych, operacje odczytu/s|Dysk danych, operacje odczytu/s (wersja zapoznawcza)|CountPerSecond|Average|Operacje We/Wy odczytu z pojedynczego dysku w okresie monitorowania|JEDNOSTKA LUN|
|Operacje zapisu dysku danych/s|Dane dysku operacje zapisu/s (wersja zapoznawcza)|CountPerSecond|Average|Zapis operacji We/Wy z pojedynczego dysku w okresie monitorowania|JEDNOSTKA LUN|
|Głębokość kolejki dysku danych|Głębokość kolejki dysku danych (wersja zapoznawcza)|Count|Average|Głębokość kolejki dysku danych (lub długość kolejki)|JEDNOSTKA LUN|
|Bajtów na sekundę odczytu z dysku systemu operacyjnego|Dysk systemu operacyjnego odczytu bajtów na sekundę (wersja zapoznawcza)|CountPerSecond|Average|Bajtów na sekundę odczytanych z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Bajty zapisu dysku systemu operacyjnego/s|Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Average|Bajtów na sekundę zapisanych na jednym dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Dysk systemu operacyjnego operacje odczytu/s|Dysk systemu operacyjnego operacje odczytu/s (wersja zapoznawcza)|CountPerSecond|Average|Operacje We/Wy odczytu z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Operacje zapisu dysku systemu operacyjnego/s|Operacje zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Average|Zapis operacji We/Wy z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Głębokość kolejki dysku systemu operacyjnego|Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza)|Count|Average|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|Nie wymiarów|
|Przepływy ruchu przychodzącego|Przepływy ruchu przychodzącego (wersja zapoznawcza)|Count|Average|Przepływy ruchu przychodzącego są numer bieżącej przepływów w kierunku ruchu przychodzącego (ruchu kierowanego do maszyny Wirtualnej)|Nie wymiarów|
|Przepływy wychodzące|Przepływy wychodzące (wersja zapoznawcza)|Count|Average|Przepływy wychodzące są numer bieżącej przepływów w kierunku wychodzącego (ruch wychodzące z maszyny Wirtualnej)|Nie wymiarów|
|Szybkość maksymalna tworzenia przepływów ruchu przychodzącego|Szybkość maksymalna tworzenia przepływów ruchu przychodzącego (wersja zapoznawcza)|CountPerSecond|Average|Szybkość maksymalna tworzenia przepływów ruchu przychodzącego (ruchu kierowanego do maszyny Wirtualnej)|Nie wymiarów|
|Szybkość maksymalna tworzenia przepływy wychodzące|Szybkość maksymalna tworzenia przepływy wychodzące (wersja zapoznawcza)|CountPerSecond|Average|Tworzenie maksymalny stopień przepływy wychodzące (ruch wychodzące z maszyny Wirtualnej)|Nie wymiarów|
|Trafienie odczytu pamięci podręcznej dysku danych Premium|Trafienie odczytu pamięci podręcznej dysku danych Premium (wersja zapoznawcza)|Percent|Average|Trafienie odczytu pamięci podręcznej dysku danych Premium|JEDNOSTKA LUN|
|Premium dysku odczytu chybień w pamięci podręcznej|Premium dysku odczytu chybień w pamięci podręcznej (wersja zapoznawcza)|Percent|Average|Premium dysku odczytu chybień w pamięci podręcznej|JEDNOSTKA LUN|
|Trafienia odczytu pamięci podręcznej dysku systemu operacyjnego — wersja Premium|Odczyt pamięci podręcznej dysku systemu operacyjnego Premium trafień (wersja zapoznawcza)|Percent|Average|Trafienia odczytu pamięci podręcznej dysku systemu operacyjnego — wersja Premium|Nie wymiarów|
|Trafienia odczytu pamięci podręcznej dysku systemu operacyjnego — wersja Premium|Trafienia odczytu pamięci podręcznej dysku systemu operacyjnego — wersja Premium (wersja zapoznawcza)|Percent|Average|Trafienia odczytu pamięci podręcznej dysku systemu operacyjnego — wersja Premium|Nie wymiarów|
|Sieć ogółem|Sieć ogółem|Bajty|Łącznie|Liczba bajtów odebranych ze wszystkich interfejsów sieciowych przez maszyny wirtualne (ruch przychodzący)|Nie wymiarów|
|Sieć się łącznie|Sieć się łącznie|Bajty|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący)|Nie wymiarów|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Średnia|Procent przydzielonych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne|VMName|
|Sieć — wejście|Sieć w płatnych|Bajty|Łącznie|Liczba płatnych bajtów odebranych ze wszystkich interfejsów sieciowych przez maszyny wirtualne (ruch przychodzący)|VMName|
|Sieć — wyjście|Sieci poza płatne|Bajty|Łącznie|Liczba płatnych bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący)|VMName|
|Bajty odczytane z dysku|Bajty odczytane z dysku|Bajty|Łącznie|Bajty odczytane z dysku w okresie monitorowania|VMName|
|Bajty zapisane na dysku|Bajty zapisane na dysku|Bajty|Łącznie|Bajty zapisane na dysku w okresie monitorowania|VMName|
|Dysku, operacje odczytu/s|Dysku, operacje odczytu/s|CountPerSecond|Average|Odczyt z dysku na SEKUNDĘ|VMName|
|Operacje zapisu dysku/s|Operacje zapisu dysku/s|CountPerSecond|Average|Operacje We/Wy zapisu na dysku|VMName|
|Pozostałe środki na korzystanie z procesora CPU|Pozostałe środki na korzystanie z procesora CPU|Count|Average|Łączna ilość środków dostępnych na potrzeby serii|Nie wymiarów|
|Środki na procesory CPU wykorzystany|Środki na procesory CPU wykorzystany|Count|Average|Łączna ilość środków wykorzystanych przez maszynę wirtualną|Nie wymiarów|
|Na bajty odczytu dysku/s|Dane Bajty odczytu dysku/s (przestarzałe)|CountPerSecond|Average|Bajtów na sekundę odczytanych z pojedynczego dysku w okresie monitorowania|Identyfikator_gniazda|
|Na bajty zapisu dysku/s|Dane Bajty zapisu dysku/s (przestarzałe)|CountPerSecond|Average|Bajtów na sekundę zapisanych na jednym dysku w okresie monitorowania|Identyfikator_gniazda|
|Na dysku, operacje odczytu/s|Dysk danych, operacje odczytu/s (przestarzałe)|CountPerSecond|Average|Operacje We/Wy odczytu z pojedynczego dysku w okresie monitorowania|Identyfikator_gniazda|
|Za operacje zapisu dysku/s|Dane dysku operacje zapisu/s (przestarzałe)|CountPerSecond|Average|Zapis operacji We/Wy z pojedynczego dysku w okresie monitorowania|Identyfikator_gniazda|
|Głębokość kolejki dysku|(Przestarzałe) głębokość kolejki dysku danych|Count|Average|Głębokość kolejki dysku danych (lub długość kolejki)|Identyfikator_gniazda|
|System operacyjny na bajty odczytu dysku/s|Dysk systemu operacyjnego odczytu bajtów na sekundę (przestarzałe)|CountPerSecond|Average|Bajtów na sekundę odczytanych z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Bajtów na sekundę zapisu na dysku systemu operacyjnego|System operacyjny Bajty zapisu dysku/s (przestarzałe)|CountPerSecond|Average|Bajtów na sekundę zapisanych na jednym dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|System operacyjny na operacje odczytu dysku/s|Dysk systemu operacyjnego operacje odczytu/s (przestarzałe)|CountPerSecond|Average|Operacje We/Wy odczytu z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Na dysku systemu operacyjnego operacje zapisu/s|Dysk systemu operacyjnego (przestarzałe) operacje zapisu/s|CountPerSecond|Average|Zapis operacji We/Wy z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Na głębokość kolejki dysku systemu operacyjnego|Dysk systemu operacyjnego głębokość kolejki (przestarzałe)|Count|Average|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|Nie wymiarów|
|Dane Bajty odczytu dysku/s|Dane Bajty odczytu dysku/s (wersja zapoznawcza)|CountPerSecond|Average|Bajtów na sekundę odczytanych z pojedynczego dysku w okresie monitorowania|Jednostki LUN, VMName|
|Bajty zapisu dysku danych/s|Dane Bajty zapisu dysku/s (wersja zapoznawcza)|CountPerSecond|Average|Bajtów na sekundę zapisanych na jednym dysku w okresie monitorowania|Jednostki LUN, VMName|
|Dysk danych, operacje odczytu/s|Dysk danych, operacje odczytu/s (wersja zapoznawcza)|CountPerSecond|Average|Operacje We/Wy odczytu z pojedynczego dysku w okresie monitorowania|Jednostki LUN, VMName|
|Operacje zapisu dysku danych/s|Dane dysku operacje zapisu/s (wersja zapoznawcza)|CountPerSecond|Average|Zapis operacji We/Wy z pojedynczego dysku w okresie monitorowania|Jednostki LUN, VMName|
|Głębokość kolejki dysku danych|Głębokość kolejki dysku danych (wersja zapoznawcza)|Count|Average|Głębokość kolejki dysku danych (lub długość kolejki)|Jednostki LUN, VMName|
|Bajtów na sekundę odczytu z dysku systemu operacyjnego|Dysk systemu operacyjnego odczytu bajtów na sekundę (wersja zapoznawcza)|CountPerSecond|Average|Bajtów na sekundę odczytanych z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Bajty zapisu dysku systemu operacyjnego/s|Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Average|Bajtów na sekundę zapisanych na jednym dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Dysk systemu operacyjnego operacje odczytu/s|Dysk systemu operacyjnego operacje odczytu/s (wersja zapoznawcza)|CountPerSecond|Average|Operacje We/Wy odczytu z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Operacje zapisu dysku systemu operacyjnego/s|Operacje zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Average|Zapis operacji We/Wy z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Głębokość kolejki dysku systemu operacyjnego|Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza)|Count|Average|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|VMName|
|Przepływy ruchu przychodzącego|Przepływy ruchu przychodzącego (wersja zapoznawcza)|Count|Average|Przepływy ruchu przychodzącego są numer bieżącej przepływów w kierunku ruchu przychodzącego (ruchu kierowanego do maszyny Wirtualnej)|VMName|
|Przepływy wychodzące|Przepływy wychodzące (wersja zapoznawcza)|Count|Average|Przepływy wychodzące są numer bieżącej przepływów w kierunku wychodzącego (ruch wychodzące z maszyny Wirtualnej)|VMName|
|Szybkość maksymalna tworzenia przepływów ruchu przychodzącego|Szybkość maksymalna tworzenia przepływów ruchu przychodzącego (wersja zapoznawcza)|CountPerSecond|Average|Szybkość maksymalna tworzenia przepływów ruchu przychodzącego (ruchu kierowanego do maszyny Wirtualnej)|VMName|
|Szybkość maksymalna tworzenia przepływy wychodzące|Szybkość maksymalna tworzenia przepływy wychodzące (wersja zapoznawcza)|CountPerSecond|Average|Tworzenie maksymalny stopień przepływy wychodzące (ruch wychodzące z maszyny Wirtualnej)|VMName|
|Trafienie odczytu pamięci podręcznej dysku danych Premium|Trafienie odczytu pamięci podręcznej dysku danych Premium (wersja zapoznawcza)|Percent|Average|Trafienie odczytu pamięci podręcznej dysku danych Premium|Jednostki LUN, VMName|
|Premium dysku odczytu chybień w pamięci podręcznej|Premium dysku odczytu chybień w pamięci podręcznej (wersja zapoznawcza)|Percent|Average|Premium dysku odczytu chybień w pamięci podręcznej|Jednostki LUN, VMName|
|Trafienia odczytu pamięci podręcznej dysku systemu operacyjnego — wersja Premium|Odczyt pamięci podręcznej dysku systemu operacyjnego Premium trafień (wersja zapoznawcza)|Percent|Average|Trafienia odczytu pamięci podręcznej dysku systemu operacyjnego — wersja Premium|VMName|
|Trafienia odczytu pamięci podręcznej dysku systemu operacyjnego — wersja Premium|Trafienia odczytu pamięci podręcznej dysku systemu operacyjnego — wersja Premium (wersja zapoznawcza)|Percent|Average|Trafienia odczytu pamięci podręcznej dysku systemu operacyjnego — wersja Premium|VMName|
|Sieć ogółem|Sieć ogółem|Bajty|Łącznie|Liczba bajtów odebranych ze wszystkich interfejsów sieciowych przez maszyny wirtualne (ruch przychodzący)|VMName|
|Sieć się łącznie|Sieć się łącznie|Bajty|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuUsage|Użycie procesora CPU|Count|Average|Użycie procesora CPU na wszystkich rdzeniach w millicores.|containerName|
|MemoryUsage|Użycie pamięci|Bajty|Average|Łączne użycie pamięci w bajtach.|containerName|
|NetworkBytesReceivedPerSecond|Bajty sieci na sekundę|Bajty|Average|Bajty sieci na sekundę.|Nie wymiarów|
|NetworkBytesTransmittedPerSecond|Bajty sieciowe przesyłanych na sekundę|Bajty|Average|Bajty sieci przesłane na sekundę.|Nie wymiarów|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalPullCount|Liczba całkowita ściągania|Count|Average|Liczba obrazów ściąga ogółem|Nie wymiarów|
|SuccessfulPullCount|Liczba pomyślnych ściągania|Count|Average|Liczba ściąga obraz pomyślnie|Nie wymiarów|
|TotalPushCount|Liczba całkowita wypychania|Count|Average|Liczba obrazów wypycha ogółem|Nie wymiarów|
|SuccessfulPushCount|Liczba pomyślnych wypychania|Count|Average|Liczba wypchnięć pomyślne obrazu|Nie wymiarów|
|RunDuration|Czas trwania przebiegu|MS|Łącznie|Czas trwania (w milisekundach)|Nie wymiarów|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Łączna liczba dostępnych rdzeni procesora cpu w zarządzanym klastrze|Count|Łącznie|Łączna liczba dostępnych rdzeni procesora cpu w zarządzanym klastrze|Nie wymiarów|
|kube_node_status_allocatable_memory_bytes|Całkowita ilość dostępnej pamięci w zarządzanym klastrze|Bajty|Łącznie|Całkowita ilość dostępnej pamięci w zarządzanym klastrze|Nie wymiarów|
|kube_pod_status_ready|Liczba zasobników w stanie gotowości|Count|Łącznie|Liczba zasobników w stanie gotowości|przestrzeń nazw, zasobników|
|kube_node_status_condition|Stany dla różnych warunków węzła|Count|Łącznie|Stany dla różnych warunków węzła|warunek, stan, status2, węzeł|
|kube_pod_status_phase|Liczba zasobników przez fazy|Count|Łącznie|Liczba zasobników przez fazy|Faza, przestrzeń nazw, zasobników|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|DCIApiCalls|Wywołania interfejsu API usługi Insights klienta|Count|Łącznie||Nie wymiarów|
|DCIMappingImportOperationSuccessfulLines|Mapowanie wierszy pomyślnych operacji importu|Count|Łącznie||Nie wymiarów|
|DCIMappingImportOperationFailedLines|Mapowanie operacji importowania nie powiodła się wierszy|Count|Łącznie||Nie wymiarów|
|DCIMappingImportOperationTotalLines|Łączna liczba wierszy operacji importu mapowania|Count|Łącznie||Nie wymiarów|
|DCIMappingImportOperationRuntimeInSeconds|Mapowanie środowiska uruchomieniowego operacji importu w ciągu kilku sekund|Sekundy|Łącznie||Nie wymiarów|
|DCIOutboundProfileExportSucceeded|Eksportowanie profilu ruchu wychodzącego powiodło się.|Count|Łącznie||Nie wymiarów|
|DCIOutboundProfileExportFailed|Eksportowanie profilu ruchu wychodzącego nie powiodło się|Count|Łącznie||Nie wymiarów|
|DCIOutboundProfileExportDuration|Czas trwania Eksportowanie profilu ruchu wychodzącego|Sekundy|Łącznie||Nie wymiarów|
|DCIOutboundKpiExportSucceeded|Wychodzące eksportu kluczowy wskaźnik wydajności zakończyło się pomyślnie.|Count|Łącznie||Nie wymiarów|
|DCIOutboundKpiExportFailed|Eksportowanie wychodzącego kluczowy wskaźnik wydajności nie powiodło się|Count|Łącznie||Nie wymiarów|
|DCIOutboundKpiExportDuration|Czas trwania wychodzącego eksportu kluczowy wskaźnik wydajności|Sekundy|Łącznie||Nie wymiarów|
|DCIOutboundKpiExportStarted|Eksportowanie wychodzącego kluczowy wskaźnik wydajności pracy|Sekundy|Łącznie||Nie wymiarów|
|DCIOutboundKpiRecordCount|Liczba rekordów wychodzącego kluczowego wskaźnika wydajności.|Sekundy|Łącznie||Nie wymiarów|
|DCIOutboundProfileExportCount|Liczba Eksportowanie profilu ruchu wychodzącego|Sekundy|Łącznie||Nie wymiarów|
|DCIOutboundInitialProfileExportFailed|Eksportowanie profilu początkowej ruchu wychodzącego nie powiodło się|Sekundy|Łącznie||Nie wymiarów|
|DCIOutboundInitialProfileExportSucceeded|Eksportowanie profilu początkowej ruchu wychodzącego zakończyło się pomyślnie|Sekundy|Łącznie||Nie wymiarów|
|DCIOutboundInitialKpiExportFailed|Wychodzące początkowej eksportowanie kluczowy wskaźnik wydajności nie powiodło się|Sekundy|Łącznie||Nie wymiarów|
|DCIOutboundInitialKpiExportSucceeded|Wychodzące początkowej eksportu kluczowy wskaźnik wydajności zakończyło się pomyślnie.|Sekundy|Łącznie||Nie wymiarów|
|DCIOutboundInitialProfileExportDurationInSeconds|Wychodzące wstępnego profilu eksportowania czas w sekundach|Sekundy|Łącznie||Nie wymiarów|
|AdlaJobForStandardKpiFailed|Zadanie Adla dla standardowych wskaźnika Kpi nie powiodło się w ciągu kilku sekund|Sekundy|Łącznie||Nie wymiarów|
|AdlaJobForStandardKpiTimeOut|Adla Job For Standard Kpi TimeOut In Seconds|Sekundy|Łącznie||Nie wymiarów|
|AdlaJobForStandardKpiCompleted|Zadanie Adla dla standardowych kluczowy wskaźnik wydajności, które są ukończone w ciągu kilku sekund|Sekundy|Łącznie||Nie wymiarów|
|ImportASAValuesFailed|Liczba nieudanych wartości ASA importu|Count|Łącznie||Nie wymiarów|
|ImportASAValuesSucceeded|Wartości ASA Importowanie powiodło się. liczba|Count|Łącznie||Nie wymiarów|
|DCIProfilesCount|Liczba wystąpień profilu|Count|Ostatnia||Nie wymiarów|
|DCIInteractionsPerMonthCount|Interakcje za liczbę miesięcznie|Count|Ostatnia||Nie wymiarów|
|DCIKpisCount|Liczba kluczowy wskaźnik wydajności|Count|Ostatnia||Nie wymiarów|
|DCISegmentsCount|Liczba segmentów.|Count|Ostatnia||Nie wymiarów|
|DCIPredictiveMatchPoliciesCount|Liczba dopasowań predykcyjne|Count|Ostatnia||Nie wymiarów|
|DCIPredictionsCount|Liczba prognoz|Count|Ostatnia||Nie wymiarów|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|NICReadThroughput|Przepływność odczytu (sieć)|BytesPerSecond|Average|Przepływność odczytu interfejsu sieciowego na urządzeniu w okresie raportowania, wszystkie woluminy w bramie.|InstanceName|
|NICWriteThroughput|Zapis przepływności (sieć)|BytesPerSecond|Average|Przepływność zapisu interfejsu sieciowego na urządzeniu w okresie raportowania, wszystkie woluminy w bramie.|InstanceName|
|CloudReadThroughputPerShare|Przepływność pobierania chmurze (udział)|BytesPerSecond|Average|Pobieranie przepływność na platformie Azure z udziału w tym okresie raportowania.|Udostępnij|
|CloudUploadThroughputPerShare|Przepustowość przekazywania w chmurze (udział)|BytesPerSecond|Average|Przepustowość przekazywania na platformie Azure z udziału w tym okresie raportowania.|Udostępnij|
|BytesUploadedToCloudPerShare|Przekazane bajty chmurze (udział)|Bajty|Average|Całkowita liczba bajtów, który zostanie przekazany na platformę Azure z udziału w tym okresie raportowania.|Udostępnij|
|Łączna pojemność|Całkowita pojemność|Bajty|Average|Całkowita pojemność|Nie wymiarów|
|AvailableCapacity|Dostępna pojemność|Bajty|Average|Dostępna pojemność w bajtach, w tym okresie raportowania.|Nie wymiarów|
|CloudUploadThroughput|Przepustowość przekazywania w chmurze|BytesPerSecond|Average|Chmura przepustowość przesyłania do platformy Azure w okresie raportowania.|Nie wymiarów|
|CloudReadThroughput|Przepływność pobierania chmury|BytesPerSecond|Average|Chmura pobierania przepływność na platformie Azure w okresie raportowania.|Nie wymiarów|
|BytesUploadedToCloud|Przekazane bajty chmury (urządzenia)|Bajty|Average|Całkowita liczba bajtów, który zostanie przekazany na platformę Azure z urządzenia po okresie raportowania.|Nie wymiarów|
|HyperVVirtualProcessorUtilization|Krawędź Compute - procentowe użycie procesora CPU|Percent|Average|Procent użycia procesora CPU|InstanceName|
|HyperVMemoryUtilization|Krawędzi obliczeniami — użycie pamięci|Percent|Average|Ilość pamięci RAM w użyciu|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|FailedRuns|Nieudane uruchomienia|Count|Łącznie||Nazwa potoku:, nazwa działania:|
|SuccessfulRuns|Udane uruchomienia|Count|Łącznie||Nazwa potoku:, nazwa działania:|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PipelineFailedRuns|Nie powiodło się metryki uruchomienia potoku|Count|Łącznie||FailureType, nazwa|
|PipelineSucceededRuns|Powodzenie metryki uruchomienia potoku|Count|Łącznie||FailureType, nazwa|
|ActivityFailedRuns|Nie powiodło się metryki uruchomień działań|Count|Łącznie||Nazwa właściwości ActivityType, Nazwa potoku: FailureType,|
|ActivitySucceededRuns|Powodzenie metryki uruchomień działań|Count|Łącznie||Nazwa właściwości ActivityType, Nazwa potoku: FailureType,|
|TriggerFailedRuns|Nie powiodło się metryki uruchomień wyzwalacza|Count|Łącznie||Nazwa, FailureType|
|TriggerSucceededRuns|Powodzenie metryki uruchomień wyzwalacza|Count|Łącznie||Nazwa, FailureType|
|IntegrationRuntimeCpuPercentage|Wykorzystanie procesora CPU w czasie wykonywania integracji|Percent|Average||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Dostępna pamięć środowiska Integration runtime|Bajty|Average||IntegrationRuntimeName, NodeName|
|MaxAllowedResourceCount|Liczba jednostek dozwolony maksymalny|Count|Maksimum||Nie wymiarów|
|MaxAllowedFactorySizeInGbUnits|Maksymalny dozwolony rozmiar fabryki (jednostka GB)|Count|Maksimum||Nie wymiarów|
|Liczba zasobów|Liczba całkowita jednostek|Count|Maksimum||Nie wymiarów|
|FactorySizeInGbUnits|Fabryka całkowity rozmiar (jednostka GB)|Count|Maksimum||Nie wymiarów|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|JobEndedSuccess|Zadania zakończone pomyślnie|Count|Łącznie|Liczba zadań zakończonych pomyślnie.|Nie wymiarów|
|JobEndedFailure|Zadania zakończone niepowodzeniem|Count|Łącznie|Liczba zadań zakończonych niepowodzeniem.|Nie wymiarów|
|JobEndedCancelled|Anulowane zadania|Count|Łącznie|Liczba anulowanych zadań.|Nie wymiarów|
|JobAUEndedSuccess|Pomyślne godziny korzystania z jednostki analizy|Sekundy|Łącznie|Łączny czas korzystania z jednostki analizy dla przypadku pomyślnie zakończonych zadań.|Nie wymiarów|
|JobAUEndedFailure|Nie powiodło się czas korzystania z jednostki analizy|Sekundy|Łącznie|Łączny czas korzystania z jednostki analizy zadania zakończone niepowodzeniem.|Nie wymiarów|
|JobAUEndedCancelled|Anulowano godziny korzystania z jednostki analizy|Sekundy|Łącznie|Łączny czas korzystania z jednostki analizy dla anulowanych zadań.|Nie wymiarów|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalStorage|Całkowita ilość miejsca do magazynowania|Bajty|Maksimum|Całkowita ilość danych przechowywanych na koncie.|Nie wymiarów|
|DataWritten|Zapisane dane|Bajty|Łącznie|Całkowita ilość danych zapisywanych na koncie.|Nie wymiarów|
|DataRead|Odczyt danych|Bajty|Łącznie|Łączna ilość danych odczytanych z konta.|Nie wymiarów|
|WriteRequests|Żądania zapisu|Count|Łącznie|Liczba danych liczbę żądań zapisu do konta.|Nie wymiarów|
|ReadRequests|Żądania odczytu|Count|Łącznie|Liczba danych odczytu żądań do konta.|Nie wymiarów|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia Procesora|Percent|Average|Procent użycia Procesora|Nie wymiarów|
|memory_percent|Procent pamięci|Percent|Average|Procent pamięci|Nie wymiarów|
|io_consumption_percent|% Operacji We/Wy|Percent|Average|% Operacji We/Wy|Nie wymiarów|
|storage_percent|Procent pamięci masowej|Percent|Average|Procent pamięci masowej|Nie wymiarów|
|storage_used|Użyty magazyn|Bajty|Average|Użyty magazyn|Nie wymiarów|
|storage_limit|Limit magazynu|Bajty|Average|Limit magazynu|Nie wymiarów|
|serverlog_storage_percent|Procent magazynu dziennika serwera|Percent|Average|Procent magazynu dziennika serwera|Nie wymiarów|
|serverlog_storage_usage|Używany Magazyn dzienników serwera|Bajty|Average|Używany Magazyn dzienników serwera|Nie wymiarów|
|serverlog_storage_limit|Limit magazynu dziennika serwera|Bajty|Average|Limit magazynu dziennika serwera|Nie wymiarów|
|active_connections|Aktywne połączenia|Count|Average|Aktywne połączenia|Nie wymiarów|
|connections_failed|Połączenia zakończone niepowodzeniem|Count|Łącznie|Połączenia zakończone niepowodzeniem|Nie wymiarów|
|seconds_behind_master|Opóźnienie replikacji w ciągu kilku sekund|Count|Average|Opóźnienie replikacji w ciągu kilku sekund|Nie wymiarów|
|backup_storage_used|Używany magazyn kopii zapasowych|Bajty|Average|Używany magazyn kopii zapasowych|Nie wymiarów|
|network_bytes_egress|Sieć — wyjście|Bajty|Łącznie|Sieć się między aktywnych połączeń|Nie wymiarów|
|network_bytes_ingress|Sieć — wejście|Bajty|Łącznie|W sieci, między aktywnych połączeń|Nie wymiarów|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia Procesora|Percent|Average|Procent użycia Procesora|Nie wymiarów|
|memory_percent|Procent pamięci|Percent|Average|Procent pamięci|Nie wymiarów|
|io_consumption_percent|% Operacji We/Wy|Percent|Average|% Operacji We/Wy|Nie wymiarów|
|storage_percent|Procent pamięci masowej|Percent|Average|Procent pamięci masowej|Nie wymiarów|
|storage_used|Użyty magazyn|Bajty|Average|Użyty magazyn|Nie wymiarów|
|storage_limit|Limit magazynu|Bajty|Average|Limit magazynu|Nie wymiarów|
|serverlog_storage_percent|Procent magazynu dziennika serwera|Percent|Average|Procent magazynu dziennika serwera|Nie wymiarów|
|serverlog_storage_usage|Używany Magazyn dzienników serwera|Bajty|Average|Używany Magazyn dzienników serwera|Nie wymiarów|
|serverlog_storage_limit|Limit magazynu dziennika serwera|Bajty|Average|Limit magazynu dziennika serwera|Nie wymiarów|
|active_connections|Aktywne połączenia|Count|Average|Aktywne połączenia|Nie wymiarów|
|connections_failed|Połączenia zakończone niepowodzeniem|Count|Łącznie|Połączenia zakończone niepowodzeniem|Nie wymiarów|
|seconds_behind_master|Opóźnienie replikacji w ciągu kilku sekund|Count|Average|Opóźnienie replikacji w ciągu kilku sekund|Nie wymiarów|
|backup_storage_used|Używany magazyn kopii zapasowych|Bajty|Average|Używany magazyn kopii zapasowych|Nie wymiarów|
|network_bytes_egress|Sieć — wyjście|Bajty|Łącznie|Sieć się między aktywnych połączeń|Nie wymiarów|
|network_bytes_ingress|Sieć — wejście|Bajty|Łącznie|W sieci, między aktywnych połączeń|Nie wymiarów|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia Procesora|Percent|Average|Procent użycia Procesora|Nie wymiarów|
|memory_percent|Procent pamięci|Percent|Average|Procent pamięci|Nie wymiarów|
|io_consumption_percent|% Operacji We/Wy|Percent|Average|% Operacji We/Wy|Nie wymiarów|
|storage_percent|Procent pamięci masowej|Percent|Average|Procent pamięci masowej|Nie wymiarów|
|storage_used|Użyty magazyn|Bajty|Average|Użyty magazyn|Nie wymiarów|
|storage_limit|Limit magazynu|Bajty|Average|Limit magazynu|Nie wymiarów|
|serverlog_storage_percent|Procent magazynu dziennika serwera|Percent|Average|Procent magazynu dziennika serwera|Nie wymiarów|
|serverlog_storage_usage|Używany Magazyn dzienników serwera|Bajty|Average|Używany Magazyn dzienników serwera|Nie wymiarów|
|serverlog_storage_limit|Limit magazynu dziennika serwera|Bajty|Average|Limit magazynu dziennika serwera|Nie wymiarów|
|active_connections|Aktywne połączenia|Count|Average|Aktywne połączenia|Nie wymiarów|
|connections_failed|Połączenia zakończone niepowodzeniem|Count|Łącznie|Połączenia zakończone niepowodzeniem|Nie wymiarów|
|backup_storage_used|Używany magazyn kopii zapasowych|Bajty|Average|Używany magazyn kopii zapasowych|Nie wymiarów|
|network_bytes_egress|Sieć — wyjście|Bajty|Łącznie|Sieć się między aktywnych połączeń|Nie wymiarów|
|network_bytes_ingress|Sieć — wejście|Bajty|Łącznie|W sieci, między aktywnych połączeń|Nie wymiarów|
|pg_replica_log_delay_in_seconds|Opóźnienie repliki|Sekundy|Maksimum|Opóźnienie repliki w ciągu kilku sekund|Nie wymiarów|
|pg_replica_log_delay_in_bytes|Maksymalna liczba opóźnienie między replikami|Bajty|Maksimum|Funkcja Lag w bajtach najbardziej opóźnione repliki bazy danych|Nie wymiarów|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia Procesora|Percent|Average|Procent użycia Procesora|Nie wymiarów|
|memory_percent|Procent pamięci|Percent|Average|Procent pamięci|Nie wymiarów|
|operacje We/Wy|Liczba operacji we/wy na sekundę|Count|Average|Operacje We/Wy na sekundę|Nie wymiarów|
|storage_percent|Procent pamięci masowej|Percent|Average|Procent pamięci masowej|Nie wymiarów|
|storage_used|Użyty magazyn|Bajty|Average|Użyty magazyn|Nie wymiarów|
|active_connections|Aktywne połączenia|Count|Average|Aktywne połączenia|Nie wymiarów|
|network_bytes_egress|Sieć — wyjście|Bajty|Łącznie|Sieć się między aktywnych połączeń|Nie wymiarów|
|network_bytes_ingress|Sieć — wejście|Bajty|Łącznie|W sieci, między aktywnych połączeń|Nie wymiarów|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Próby wysłania komunikatu telemetrii|Count|Łącznie|Liczba komunikatów danych telemetrycznych z urządzenia do chmury podjęto próbę wysłania do Centrum IoT hub|Nie wymiarów|
|d2c.telemetry.ingress.success|Komunikaty telemetryczne wysyłane|Count|Łącznie|Liczba komunikatów danych telemetrycznych z urządzenia do chmury pomyślnie wysłany do usługi IoT hub|Nie wymiarów|
|c2d.commands.egress.complete.success|Polecenia zakończona|Count|Łącznie|Wiele poleceń z chmury do urządzenia zakończyło się powodzeniem, urządzenie|Nie wymiarów|
|c2d.commands.egress.abandon.success|Polecenia porzucone|Count|Łącznie|Wiele poleceń chmura urządzenie porzucone przez urządzenie|Nie wymiarów|
|c2d.commands.egress.reject.success|Polecenia odrzucone|Count|Łącznie|Wiele poleceń chmura urządzenie odrzucone przez urządzenie|Nie wymiarów|
|devices.totalDevices|Łączna liczba urządzeń (przestarzałe)|Count|Łącznie|Liczba urządzeń zarejestrowanych do Centrum IoT hub|Nie wymiarów|
|devices.connectedDevices.allProtocol|Połączone urządzenia (przestarzałe) |Count|Łącznie|Liczba urządzeń podłączonych do Centrum IoT hub|Nie wymiarów|
|d2c.telemetry.egress.success|Routingu: wydana komunikaty telemetryczne|Count|Łącznie|Liczba przypadków, gdy wiadomości zostały pomyślnie dostarczone do wszystkich punktów końcowych przy użyciu routingu w usłudze IoT Hub. Jeśli komunikat jest kierowany do wielu punktów końcowych, ta wartość zwiększa się o jeden dla każdego skutecznej. Jeśli komunikat jest dostarczane do tego samego punktu końcowego wiele razy, ta wartość zwiększa się o jeden dla każdego skutecznej.|Nie wymiarów|
|d2c.telemetry.egress.dropped|Routingu: porzucone komunikaty telemetryczne |Count|Łącznie|Liczba przypadków, gdy komunikaty zostały porzucone przez usługę IoT Hub routing z powodu braku punktów końcowych. Ta wartość nie jest liczony komunikaty dostarczane do trasy rezerwowej, gdy porzuconych wiadomości nie są dostarczane istnieje.|Nie wymiarów|
|d2c.telemetry.egress.orphaned|Routingu: porzucone komunikaty telemetryczne |Count|Łącznie|Liczba przypadków, gdy komunikaty zostały oddzielone kierując usługi IoT Hub, ponieważ nie pasują do żadnych reguł routingu (w tym reguły rezerwowego). |Nie wymiarów|
|d2c.telemetry.egress.invalid|Routing: komunikaty telemetryczne niezgodne|Count|Łącznie|Liczba razy routingu w usłudze IoT Hub nie można dostarczać komunikatów z powodu niezgodności z punktem końcowym. Ta wartość nie obejmuje ponownych prób.|Nie wymiarów|
|d2c.telemetry.egress.fallback|Routing: komunikaty dostarczane do rezerwowego|Count|Łącznie|Liczba przypadków, dostarczenia komunikatów w Centrum IoT Hub routingu do skojarzonej z trasą rezerwowego punktu końcowego.|Nie wymiarów|
|d2c.endpoints.egress.eventHubs|Routing: komunikaty dostarczane do Centrum zdarzeń|Count|Łącznie|Liczba przypadków, gdy Centrum IoT Hub routingu pomyślnie dostarczone wiadomości do punktów końcowych Centrum zdarzeń.|Nie wymiarów|
|d2c.endpoints.latency.eventHubs|Routing: komunikat o opóźnieniu dla Centrum zdarzeń|MS|Average|Średnie opóźnienie (w milisekundach) między komunikatów przychodzących do usługi IoT Hub i ruch przychodzący komunikat do punktu końcowego Centrum zdarzeń.|Nie wymiarów|
|d2c.endpoints.egress.serviceBusQueues|Routing: komunikaty dostarczane do kolejki usługi Service Bus|Count|Łącznie|Liczba przypadków, gdy usługi IoT Hub routingu pomyślnie dostarczone wiadomości do punktów końcowych kolejki usługi Service Bus.|Nie wymiarów|
|d2c.endpoints.latency.serviceBusQueues|Routing: opóźnienie komunikat do kolejki usługi Service Bus|MS|Average|Średnie opóźnienie (w milisekundach) między komunikatów przychodzących do usługi IoT Hub i transferu danych przychodzących danych telemetrycznych komunikat do punktu końcowego kolejki usługi Service Bus.|Nie wymiarów|
|d2c.endpoints.egress.serviceBusTopics|Routing: komunikaty dostarczane do tematu usługi Service Bus|Count|Łącznie|Liczba przypadków, gdy Centrum IoT Hub routingu pomyślnie dostarczone wiadomości do punktów końcowych z tematu usługi Service Bus.|Nie wymiarów|
|d2c.endpoints.latency.serviceBusTopics|Routing: opóźnienie komunikat do tematu usługi Service Bus|MS|Average|Średnie opóźnienie (w milisekundach) między komunikatów przychodzących do usługi IoT Hub i transferu danych przychodzących danych telemetrycznych komunikat do punktu końcowego tematu usługi Service Bus.|Nie wymiarów|
|d2c.endpoints.egress.builtIn.events|Routing: komunikaty dostarczane do komunikaty/zdarzenia|Count|Łącznie|Liczba przypadków, gdy Centrum IoT Hub routingu pomyślnie dostarczone wiadomości do wbudowany punkt końcowy (komunikaty/zdarzenia). Ta metryka tylko uruchamia działa po włączeniu routingu (https://aka.ms/iotrouting) dla Centrum IoT hub.|Nie wymiarów|
|d2c.endpoints.latency.builtIn.events|Routing: komunikat o opóźnieniu komunikaty/zdarzenia|MS|Average|Średnie opóźnienie (w milisekundach) między komunikatów przychodzących do usługi IoT Hub i transferu danych przychodzących danych telemetrycznych wiadomości do wbudowany punkt końcowy (komunikaty/zdarzenia). Ta metryka tylko uruchamia działa po włączeniu routingu (https://aka.ms/iotrouting) dla Centrum IoT hub.|Nie wymiarów|
|d2c.endpoints.egress.storage|Routing: komunikaty dostarczane do magazynu|Count|Łącznie|Liczba przypadków, gdy usługi IoT Hub routingu pomyślnie dostarczone wiadomości do punktów końcowych magazynu.|Nie wymiarów|
|d2c.endpoints.latency.storage|Routing: komunikat o opóźnieniu magazynu|MS|Average|Średnie opóźnienie (w milisekundach) między komunikatów przychodzących do usługi IoT Hub i transferu danych przychodzących danych telemetrycznych komunikat do punktu końcowego magazynu.|Nie wymiarów|
|d2c.endpoints.egress.storage.bytes|Routing: dane dostarczane do magazynu|Bajty|Łącznie|Ilość danych (w bajtach) routingu w usłudze IoT Hub dostarczone do punktów końcowych magazynu.|Nie wymiarów|
|d2c.endpoints.egress.storage.blobs|Routing: obiektów blob jest dostarczane do magazynu|Count|Łącznie|Liczba przypadków, gdy routingu w usłudze IoT Hub dostarczane obiekty BLOB do przechowywania punktów końcowych.|Nie wymiarów|
|EventGridDeliveries|Event Grid dostaw (wersja zapoznawcza)|Count|Łącznie|Liczba zdarzeń usługi IoT Hub opublikowany usługi Event Grid. Wymiar wyniku na użytek liczba udanych i nieudanych żądań. Typ zdarzenia wymiar zawiera typ zdarzenia (https://aka.ms/ioteventgrid).|Wynik, typ zdarzenia|
|EventGridLatency|Średnie opóźnienie (w milisekundach) od kiedy zdarzeń usługi Iot Hub został wygenerowany podczas zdarzenia zostały opublikowane w usłudze Event Grid. Ta liczba jest średnia pomiędzy wszystkich typów zdarzeń. Użyj w wymiarze typ zdarzenia, aby zobaczyć opóźnienie określonych typach zdarzenia.|Typ zdarzenia|
|d2c.twin.read.success|Pomyślne bliźniacze odczyty z urządzenia|Count|Łącznie|Liczba wszystkich pomyślnie zainicjował urządzenia bliźniacze odczyty na dysku.|Nie wymiarów|
|d2c.twin.read.failure|Nie powiodło się bliźniacze odczyty z urządzenia|Count|Łącznie|Zainicjowane przez urządzenie bliźniacze odczyty liczbę wszystkich, nie powiodło się.|Nie wymiarów|
|d2c.twin.read.size|Rozmiar odpowiedzi bliźniacze odczyty z urządzenia|Bajty|Average|Średnią, minimalna i maksymalna elementu wszystkie udane, inicjowane przez urządzenie bliźniaczej reprezentacji odczytów.|Nie wymiarów|
|d2c.twin.update.success|Liczba aktualizacji bliźniaczej reprezentacji pomyślnie z urządzeń|Count|Łącznie|Liczba wszystkich pomyślnie zainicjował urządzenia aktualizacji bliźniaczej reprezentacji.|Nie wymiarów|
|d2c.twin.update.failure|Niepowodzenie aktualizacji bliźniaczej reprezentacji urządzenia|Count|Łącznie|Liczba wszystkich nie powiodło się aktualizacji inicjowane przez urządzenie bliźniaczej reprezentacji.|Nie wymiarów|
|d2c.twin.update.size|Rozmiar aktualizacji bliźniaczej reprezentacji urządzenia|Bajty|Average|Średnia, minimum i maksymalny rozmiar wszystkich pomyślnie, inicjowane przez urządzenie bliźniaczej reprezentacji aktualizacji.|Nie wymiarów|
|c2d.methods.success|Pomyślne bezpośrednie wywołania metod|Count|Łącznie|Liczba wszystkich pomyślnych wywołań metody bezpośredniej.|Nie wymiarów|
|c2d.methods.failure|Niepowodzenie wywołania metody bezpośredniej|Count|Łącznie|Liczba wszystkich nie wywołania metody bezpośredniej.|Nie wymiarów|
|c2d.methods.requestSize|Rozmiar żądania wywołania metody bezpośredniej|Bajty|Average|Średnia, minimum i maksimum wszystkich pomyślnych żądań metody bezpośredniej.|Nie wymiarów|
|c2d.methods.responseSize|Rozmiar odpowiedzi z wywołania metody bezpośredniej|Bajty|Average|Średnia, minimum i maksimum wszystkich pomyślne odpowiedzi metody bezpośredniej.|Nie wymiarów|
|c2d.twin.read.success|Pomyślne bliźniacze odczyty z zaplecza|Count|Łącznie|Liczba wszystkich pomyślnie zainicjował zakończenia kopii bliźniacze odczyty na dysku.|Nie wymiarów|
|c2d.twin.read.failure|Nie powiodło się bliźniacze odczyty z zaplecza|Count|Łącznie|Zainicjowano zakończenia kopii bliźniacze odczyty liczbę wszystkich, nie powiodło się.|Nie wymiarów|
|c2d.twin.read.size|Rozmiar odpowiedzi bliźniacze odczyty z zaplecza|Bajty|Average|Średnią, minimalna i maksymalna elementu wszystkie udane, zainicjowanych zakończenia wstecz bliźniaczej reprezentacji odczytów.|Nie wymiarów|
|c2d.twin.update.success|Liczba aktualizacji bliźniaczej reprezentacji pomyślnie z zaplecza|Count|Łącznie|Liczba wszystkich pomyślnie zainicjował zakończenia kopii aktualizacji bliźniaczej reprezentacji.|Nie wymiarów|
|c2d.twin.update.failure|Liczba aktualizacji bliźniaczej reprezentacji nie powiodło się z zaplecza|Count|Łącznie|Liczba wszystkich nie powiodło się aktualizacji inicjowane przez zakończenia wstecz bliźniaczej reprezentacji.|Nie wymiarów|
|c2d.twin.update.size|Rozmiar aktualizacji bliźniaczej reprezentacji z zaplecza|Bajty|Average|Średnia, minimum i maksymalny rozmiar wszystkich pomyślnie, zainicjowanych zakończenia wstecz bliźniaczej reprezentacji aktualizacji.|Nie wymiarów|
|twinQueries.success|Zapytania dotyczące bliźniaczych reprezentacji pomyślne|Count|Łącznie|Liczba wszystkich zapytań bliźniaczych reprezentacji się pomyślnie.|Nie wymiarów|
|twinQueries.failure|Zapytania dotyczące bliźniaczych reprezentacji nie powiodło się|Count|Łącznie|Liczba wszystkich zapytań bliźniaczych reprezentacji nie powiodło się.|Nie wymiarów|
|twinQueries.resultSize|Rozmiar wyników zapytań bliźniaczych reprezentacji|Bajty|Average|Średnia, minimum i maksymalny rozmiar wyników wszystkich zapytań bliźniaczych pomyślne.|Nie wymiarów|
|jobs.createTwinUpdateJob.success|Pomyślne utworzenie kont zadań aktualizacji bliźniaczej reprezentacji|Count|Łącznie|Liczba wszystkich pomyślne utworzenie zadania aktualizacji bliźniaczej reprezentacji.|Nie wymiarów|
|jobs.createTwinUpdateJob.failure|Nie powiodło się tworzenie zadania aktualizacji bliźniaczej reprezentacji|Count|Łącznie|Liczba wszystkich nie powiodło się utworzenie zadania aktualizacji bliźniaczej reprezentacji.|Nie wymiarów|
|jobs.createDirectMethodJob.success|Pomyślne utworzenie kont zadań wywołania metody|Count|Łącznie|Liczba wszystkich pomyślnym utworzeniu zadania wywołania metody bezpośredniej.|Nie wymiarów|
|jobs.createDirectMethodJob.failure|Nie powiodło się tworzenie zadań wywołania metody|Count|Łącznie|Liczba wszystkich nie powiodło się tworzenie zadań wywołania metody bezpośredniej.|Nie wymiarów|
|jobs.listJobs.success|Zakończonych powodzeniem wywołań do wyświetlania listy zadań|Count|Łącznie|Liczba wszystkich zakończonych powodzeniem wywołań do wyświetlania listy zadań.|Nie wymiarów|
|jobs.listJobs.failure|Niepowodzenie wywołania do wyświetlania listy zadań|Count|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem wyświetlania listy zadań.|Nie wymiarów|
|jobs.cancelJob.success|Anulowane zadania pomyślne|Count|Łącznie|Liczba wszystkich pomyślnych wywołań, aby anulować zadanie.|Nie wymiarów|
|jobs.cancelJob.failure|Anulowane zadania nie powiodło się|Count|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem można anulować zadania.|Nie wymiarów|
|jobs.queryJobs.success|Pomyślnie wykonane zadanie zapytań|Count|Łącznie|Liczba wszystkich zakończonych powodzeniem wywołań do zadania kwerendy.|Nie wymiarów|
|jobs.queryJobs.failure|Nie powiodło się zadanie odpytuje|Count|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem zadania kwerendy.|Nie wymiarów|
|Jobs.Completed|Ukończone zadania|Count|Łącznie|Liczba wszystkich zakończonych zadań.|Nie wymiarów|
|Jobs.failed|Zadania zakończone niepowodzeniem|Count|Łącznie|Liczba wszystkich zadań zakończonych niepowodzeniem.|Nie wymiarów|
|d2c.telemetry.ingress.sendThrottle|Liczby błędów ograniczania dostępności|Count|Łącznie|Ogranicza liczby błędów ograniczania dostępności z powodu przepływności urządzenia|Nie wymiarów|
|dailyMessageQuotaUsed|Całkowita liczba komunikatów używane|Count|Average|Liczba całkowita liczba wiadomości używanych obecnie. Jest to wartość zbiorcza, która jest resetowany do zera UTC 00:00 każdego dnia.|Nie wymiarów|
|deviceDataUsage|Użycie danych łączna liczba urządzeń|Bajty|Łącznie|Bajty przesłane do i z wszelkie urządzenia podłączone do usługi IotHub|Nie wymiarów|
|totalDeviceCount|Łączna liczba urządzeń (wersja zapoznawcza)|Count|Average|Liczba urządzeń zarejestrowanych do Centrum IoT hub|Nie wymiarów|
|connectedDeviceCount|Połączone urządzenia (wersja zapoznawcza)|Count|Average|Liczba urządzeń podłączonych do Centrum IoT hub|Nie wymiarów|
|Konfiguracje|Konfiguracja metryki|Count|Łącznie|Metryki dla operacji konfiguracji|Nie wymiarów|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RegistrationAttempts|Prób rejestracji|Count|Łącznie|Numer rejestracji urządzeń, które podjęto próbę|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Przypisano urządzenia|Count|Łącznie|Liczba urządzeń przypisanych do usługi IoT hub|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Próby zaświadczania|Count|Łącznie|Liczba poświadczenia urządzenia, które podjęto próbę|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AvailableStorage|Dostępna pamięć|Bajty|Łącznie|Całkowita ilość miejsca dostępna na stopień szczegółowości 5 minut|Region CollectionName, DatabaseName,|
|CassandraConnectionClosures|Zamknięć połączenia bazy danych Cassandra|Count|Łącznie|Liczba połączeń bazy danych Cassandra, które zostały zamknięte zgłoszone w szczegółowości 1 minuta|Region, ClosureReason|
|CassandraRequestCharges|Opłaty za żądania bazy danych Cassandra|Count|Łącznie|RU dla żądań bazy danych Cassandra|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Żądania bazy danych Cassandra|Count|Count|Liczba żądań bazy danych Cassandra|DatabaseName CollectionName, Region, OperationType, ResourceType, kod błędu|
|DataUsage|Użycie danych|Bajty|Łącznie|Łączna ilość danych użycia zgłoszonych na stopień szczegółowości 5 minut|Region CollectionName, DatabaseName,|
|DocumentCount|Liczba dokumentów|Count|Łącznie|Łączną liczbę dokumentów zgłoszone w 5 minut stopień szczegółowości|Region CollectionName, DatabaseName,|
|DocumentQuota|Limit przydziału dokumentu|Bajty|Łącznie|Łączny przydział pamięci masowej zgłoszone w 5 minut stopień szczegółowości|Region CollectionName, DatabaseName,|
|IndexUsage|Użycie indeksu|Bajty|Łącznie|Użycie całkowita indeksu zgłoszone w 5 minut stopień szczegółowości|Region CollectionName, DatabaseName,|
|MetadataRequests|Żądania metadanych|Count|Count|Liczba żądań metadanych. Usługa cosmos DB obsługuje kolekcji metadanych systemowych dla każdego konta, które pozwala wyliczyć kolekcje, bazami danych itp i ich konfiguracji bezpłatnie.|DatabaseName, CollectionName, Region, StatusCode, |
|MongoRequestCharge|Opłata za żądanie MONGO|Count|Łącznie|Wykorzystane jednostki żądania MONGO|DatabaseName CollectionName, Region, CommandName, kod błędu|
|MongoRequests|Żądania MONGO|Count|Count|Liczba żądań Mongo|DatabaseName CollectionName, Region, CommandName, kod błędu|
|ProvisionedThroughput|Aprowizowana przepływność|Count|Maksimum|Aprowizowana przepływność|DatabaseName, CollectionName|
|ReplicationLatency|Poziomie P99 Czas oczekiwania replikacji|MilliSeconds|Average|Poziomie P99 opóźnienie replikacji między regionami źródłowych i docelowych dla konta włączono geograficznie|SourceRegion, TargetRegion|
|ServiceAvailability|Dostępność usługi|Percent|Average|Dostępność żądania konta na jedną godzinę, dzień lub miesiąc stopień szczegółowości|Nie wymiarów|
|TotalRequestUnits|Jednostki łączna liczba żądań|Count|Łącznie|Używane jednostki żądania|DatabaseName, CollectionName, Region, StatusCode, OperationType|
|TotalRequests|Łączna liczba żądań|Count|Count|Liczba żądań|DatabaseName, CollectionName, Region, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PublishSuccessCount|Opublikowane zdarzenia|Count|Łącznie|Łączna liczba zdarzeń opublikowane w tym temacie|Nie wymiarów|
|PublishFailCount|Publikowanie zdarzeń nie powiodło się|Count|Łącznie|Łączna liczba zdarzeń, nie można opublikować w tym temacie|ErrorType, Error|
|UnmatchedEventCount|Niedopasowanych zdarzeń|Count|Łącznie|Łączna liczba zdarzeń, które nie pasują dowolnej subskrypcji zdarzeń dla tego tematu|Nie wymiarów|
|PublishSuccessLatencyInMs|Publikowanie opóźnienie powodzenia|Count|Łącznie|Publikowanie opóźnienie powodzenia (w milisekundach)|Nie wymiarów|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|MatchedEventCount|Dopasowane zdarzenia|Count|Łącznie|Łączna liczba zdarzeń do tej subskrypcji zdarzeń|Nie wymiarów|
|DeliveryAttemptFailCount|Dostarczanie zdarzeń nie powiodło się|Count|Łącznie|Łączna liczba zdarzeń, nie można dostarczyć do tej subskrypcji zdarzeń|Błąd, ErrorType|
|DeliverySuccessCount|Działanie jest gwarantowane zdarzenia|Count|Łącznie|Łączna liczba zdarzeń dostarczane do tej subskrypcji zdarzeń|Nie wymiarów|
|DestinationProcessingDurationInMs|Docelowy czas przetwarzania|MS|Average|Czas przetwarzania docelowego (w milisekundach)|Nie wymiarów|
|DroppedEventCount|Zdarzenia porzucone|Count|Łącznie|Całkowita liczba porzuconych zdarzeń pasujących do tej subskrypcji zdarzeń|DropReason|
|DeadLetteredCount|Dostępnie zdarzenia|Count|Łącznie|Łączna liczba zdarzeń dostępnie dopasowany do tej subskrypcji zdarzeń|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PublishSuccessCount|Opublikowane zdarzenia|Count|Łącznie|Łączna liczba zdarzeń opublikowane w tym temacie|Nie wymiarów|
|PublishFailCount|Zdarzenia nie powiodło się|Count|Łącznie|Łączna liczba zdarzeń, nie można opublikować w tym temacie|ErrorType, Error|
|UnmatchedEventCount|Niedopasowanych zdarzeń|Count|Łącznie|Łączna liczba zdarzeń, które nie pasują dowolnej subskrypcji zdarzeń dla tego tematu|Nie wymiarów|
|PublishSuccessLatencyInMs|Publikowanie opóźnienie powodzenia|Count|Łącznie|Publikowanie opóźnienie powodzenia (w milisekundach)|Nie wymiarów|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SuccessfulRequests|Żądania zakończone powodzeniem|Count|Łącznie|Żądania zakończone powodzeniem dla elementu Microsoft.EventHub.|EntityName, |
|ServerErrors|Błędy serwera.|Count|Łącznie|Błędy serwera dla elementu Microsoft.EventHub.|EntityName, |
|UserErrors|Błędy użytkowników.|Count|Łącznie|Błędy użytkowników dla elementu Microsoft.EventHub.|EntityName, |
|QuotaExceededErrors|Błędy przekroczenia przydziału.|Count|Łącznie|Błędy przekroczenia przydziału dla elementu Microsoft.EventHub.|EntityName, |
|ThrottledRequests|Żądania ograniczone.|Count|Łącznie|Żądania ograniczone dla elementu Microsoft.EventHub.|EntityName, |
|IncomingRequests|Żądania przychodzące|Count|Łącznie|Żądania przychodzące dla elementu Microsoft.EventHub.|EntityName|
|IncomingMessages|Komunikaty przychodzące|Count|Łącznie|Komunikaty przychodzące dla elementu Microsoft.EventHub.|EntityName|
|OutgoingMessages|Komunikaty wychodzące|Count|Łącznie|Komunikaty wychodzące dla elementu Microsoft.EventHub.|EntityName|
|IncomingBytes|Bajty przychodzące.|Bajty|Łącznie|Bajty przychodzące dla elementu Microsoft.EventHub.|EntityName|
|OutgoingBytes|Bajty wychodzące.|Bajty|Łącznie|Bajty wychodzące dla elementu Microsoft.EventHub.|EntityName|
|Połączeń ActiveConnections|Połączeń ActiveConnections|Count|Average|Całkowita liczba aktywnych połączeń dla elementu Microsoft.EventHub.|Nie wymiarów|
|ConnectionsOpened|Połączenia otwarte.|Count|Average|Połączenia otwarte dla elementu Microsoft.EventHub.|EntityName|
|ConnectionsClosed|Połączenia zamknięte.|Count|Average|Połączenia zamknięte dla elementu Microsoft.EventHub.|EntityName|
|CaptureBacklog|Lista prac funkcji Capture.|Count|Łącznie|Przechwyć zaległości dla elementu Microsoft.EventHub.|EntityName|
|CapturedMessages|Przechwycone komunikaty.|Count|Łącznie|Przechwycone komunikaty dla elementu Microsoft.EventHub.|EntityName|
|CapturedBytes|Przechwycone bajty.|Bajty|Łącznie|Przechwycone bajty dla elementu Microsoft.EventHub.|EntityName|
|Size|Size|Bajty|Average|Rozmiar Centrum zdarzeń w bajtach.|EntityName|
|INREQS|Żądania przychodzące (przestarzałe)|Count|Łącznie|Łączna liczba żądań wysłania dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|SUCCREQ|Żądania zakończone powodzeniem (przestarzałe)|Count|Łącznie|Łączna liczba udanych żądań dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|FAILREQ|Żądania zakończone niepowodzeniem (przestarzałe)|Count|Łącznie|Łączna liczba nieudanych żądań dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|SVRBSY|Błędy typu serwer zajęty (przestarzałe)|Count|Łącznie|Łączna liczba zajętych błędów serwera dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|INTERR|Błędy wewnętrzne serwera (przestarzałe)|Count|Łącznie|Łączna liczba wewnętrznych błędów serwera dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|MISCERR|Inne błędy (przestarzałe)|Count|Łącznie|Łączna liczba nieudanych żądań dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|INMSGS|Komunikaty przychodzące (przestarzałe) (przestarzałe)|Count|Łącznie|Łączna liczba komunikatów przychodzących dla przestrzeni nazw. Ta metryka jest przestarzała. Użyj zamiast niej metryki komunikaty przychodzące (przestarzałe)|Nie wymiarów|
|EHINMSGS|Komunikaty przychodzące (przestarzałe)|Count|Łącznie|Łączna liczba komunikatów przychodzących dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|OUTMSGS|Komunikaty wychodzące (przestarzałe) (przestarzałe)|Count|Łącznie|Łączna liczba komunikatów wychodzących dla przestrzeni nazw. Ta metryka jest przestarzała. Użyj zamiast niej metryki komunikaty wychodzące (przestarzałe)|Nie wymiarów|
|EHOUTMSGS|Komunikaty wychodzące (przestarzałe)|Count|Łącznie|Łączna liczba komunikatów wychodzących dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|EHINMBS|Bajty przychodzące (przestarzałe) (przestarzałe)|Bajty|Łącznie|Event Hub przepływność komunikatów przychodzących dla przestrzeni nazw. Ta metryka jest przestarzała. Użyj zamiast niej metryki Bajty przychodzące (przestarzałe)|Nie wymiarów|
|EHINBYTES|Bajty przychodzące (przestarzałe)|Bajty|Łącznie|Event Hub przepływność komunikatów przychodzących dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|EHOUTMBS|Bajty wychodzące (przestarzałe) (przestarzałe)|Bajty|Łącznie|Event Hub przepływność komunikatów wychodzących dla przestrzeni nazw. Ta metryka jest przestarzała. Użyj zamiast niej metryki bajty wychodzące (przestarzałe)|Nie wymiarów|
|EHOUTBYTES|Bajty wychodzące (przestarzałe)|Bajty|Łącznie|Zdarzenia Centrum przepływność komunikatów wychodzących dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|EHABL|Archiwizuj komunikaty listy prac (przestarzałe)|Count|Łącznie|Zarchiwizowane komunikaty Centrum zdarzeń na liście prac dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|EHAMSGS|Archiwum wiadomości (przestarzałe)|Count|Łącznie|Centrum zdarzeń zarchiwizowane komunikaty w przestrzeni nazw (przestarzałe)|Nie wymiarów|
|EHAMBS|Przepływność archiwizowania komunikatów (przestarzałe)|Bajty|Łącznie|Przepływność zarchiwizowanych komunikatów Centrum zdarzeń w przestrzeni nazw (przestarzałe)|Nie wymiarów|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SuccessfulRequests|Liczba pomyślnych żądań (wersja zapoznawcza)|Count|Łącznie|Żądania zakończone powodzeniem dla elementu Microsoft.EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|ServerErrors|Błędy serwera. (Wersja zapoznawcza)|Count|Łącznie|Błędy serwera dla elementu Microsoft.EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|UserErrors|Błędy użytkowników. (Wersja zapoznawcza)|Count|Łącznie|Błędy użytkowników dla elementu Microsoft.EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|QuotaExceededErrors|Błędy przekroczenia przydziału. (Wersja zapoznawcza)|Count|Łącznie|Błędy przekroczenia przydziału dla elementu Microsoft.EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|ThrottledRequests|Żądania ograniczone. (Wersja zapoznawcza)|Count|Łącznie|Żądania ograniczone dla elementu Microsoft.EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|IncomingRequests|Przychodzące żądania (wersja zapoznawcza)|Count|Łącznie|Żądania przychodzące dla elementu Microsoft.EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|IncomingMessages|Komunikaty przychodzące (wersja zapoznawcza)|Count|Łącznie|Komunikaty przychodzące dla elementu Microsoft.EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|OutgoingMessages|Komunikaty wychodzące (wersja zapoznawcza)|Count|Łącznie|Komunikaty wychodzące dla elementu Microsoft.EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|IncomingBytes|Bajty przychodzące. (Wersja zapoznawcza)|Bajty|Łącznie|Bajty przychodzące dla elementu Microsoft.EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|OutgoingBytes|Bajty wychodzące. (Wersja zapoznawcza)|Bajty|Łącznie|Bajty wychodzące dla elementu Microsoft.EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|Połączeń ActiveConnections|Połączeń ActiveConnections (wersja zapoznawcza)|Count|Average|Całkowita liczba aktywnych połączeń dla elementu Microsoft.EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|ConnectionsOpened|Połączenia otwarte. (Wersja zapoznawcza)|Count|Average|Połączenia otwarte dla elementu Microsoft.EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|ConnectionsClosed|Połączenia zamknięte. (Wersja zapoznawcza)|Count|Average|Połączenia zamknięte dla elementu Microsoft.EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|CaptureBacklog|Lista prac funkcji Capture. (Wersja zapoznawcza)|Count|Łącznie|Przechwyć zaległości dla elementu Microsoft.EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|CapturedMessages|Przechwycone komunikaty. (Wersja zapoznawcza)|Count|Łącznie|Przechwycone komunikaty dla elementu Microsoft.EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|CapturedBytes|Przechwycone bajty. (Wersja zapoznawcza)|Bajty|Łącznie|Przechwycone bajty dla elementu Microsoft.EventHub. (Wersja zapoznawcza)|Nie wymiarów|
|Procesor CPU|Procesor (wersja zapoznawcza)|Percent|Maksimum|Użycie procesora CPU dla klastra Centrum zdarzeń jako wartość procentowa|Role|
|AvailableMemory|Dostępna pamięć (wersja zapoznawcza)|Count|Maksimum|Dostępna pamięć dla klastra Centrum zdarzeń w bajtach|Role|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|GatewayRequests|Żądania bramy|Count|Łącznie|Liczba żądań bramy|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Żądania bramy skategoryzowane|Count|Łącznie|Liczba żądań bramy według kategorii (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Liczba aktywnych procesów roboczych|Count|Maksimum|Liczba aktywnych procesów roboczych|ClusterDnsName, MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ObservedMetricValue|Zaobserwowana wartość metryki|Count|Average|Wartość obliczona przez automatyczne skalowanie podczas wykonywania|MetricTriggerSource|
|MetricThreshold|Próg metryki|Count|Average|Skonfigurowany próg automatycznego skalowania podczas uruchamiania automatycznego skalowania.|MetricTriggerRule|
|ObservedCapacity|Zaobserwowana wydajność|Count|Average|Wydajność zgłoszona do automatycznego skalowania podczas jego wykonywania.|Nie wymiarów|
|ScaleActionsInitiated|Zainicjowane akcje skalowania|Count|Łącznie|Kierunek operacji skalowania.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(Publiczna wersja zapoznawcza)

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Dostępność|Procent|Average|Procent testów zakończonych pomyślnie dostępności|availabilityResult/name availabilityResult/lokalizacja|
|availabilityResults/count|Testy dostępności|Count|Count|Liczba testów dostępności|availabilityResult/name, availabilityResult/lokalizacji, availabilityResult/Powodzenie|
|availabilityResults/czas trwania|Czas trwania testu dostępności|MilliSeconds|Average|Czas trwania testu dostępności|availabilityResult/name, availabilityResult/lokalizacji, availabilityResult/Powodzenie|
|browserTimings/networkDuration|Czas połączenia sieciowego podczas ładowania strony|MilliSeconds|Average|Czas od użytkownika żądania i połączenie sieciowe. Obejmuje DNS wyszukiwania i połączenie transportu.|Nie wymiarów|
|browserTimings/processingDuration|Czas przetwarzania klienta|MilliSeconds|Average|Czas między odebraniem ostatniego bajtu dokumentu, do momentu załadowania modelu DOM. Żądania asynchroniczne mogą nadal być przetwarzane.|Nie wymiarów|
|browserTimings/receiveDuration|Czas odpowiedzi odbierania|MilliSeconds|Average|Czas między pierwszym i ostatnim bajtem lub do momentu rozłączenia.|Nie wymiarów|
|browserTimings/sendDuration|Czas żądania wysyłania|MilliSeconds|Average|Czas między połączenia sieciowego i odebraniem pierwszego bajtu.|Nie wymiarów|
|browserTimings/totalDuration|Czas ładowania strony przeglądarki|MilliSeconds|Average|Czas od żądania użytkownika do modelu DOM, arkuszy stylów, skrypty i obrazy są ładowane.|Nie wymiarów|
|zależności/liczby|Wywołania zależności|Count|Count|Liczba wywołań wykonanych przez aplikację do zasobów zewnętrznych.|zależności/typu zależności/performanceBucket, zależności/Powodzenie, operacji/syntetycznych, chmura/roleInstance, chmury/roleName|
|zależności/czas trwania|Czas trwania zależności|MilliSeconds|Average|Czas trwania wywołań wykonanych przez aplikację do zasobów zewnętrznych.|zależności/typu zależności/performanceBucket, zależności/Powodzenie, operacji/syntetycznych, chmura/roleInstance, chmury/roleName|
|zależności lub nie powiodła się|Niepowodzenia wywołania zależności|Count|Count|Liczba wywołań zależności zakończone niepowodzeniem wykonanych przez aplikację do zasobów zewnętrznych.|Typ zależności/zależności/performanceBucket, operacji/syntetycznych, chmury/roleInstance, chmury/roleName|
|Liczba/wyświetleń stron|Wyświetlenia strony|Count|Count|Liczba wyświetleń strony.|Operacja/syntetycznych|
|wyświetleń stron/czas trwania|Czas ładowania wyświetlenia strony|MilliSeconds|Average|Czas ładowania wyświetlenia strony|Operacja/syntetycznych|
|performanceCounters/requestExecutionTime|Czas wykonywania żądania HTTP|MilliSeconds|Average|Godzina wykonania ostatniego żądania.|cloud/roleInstance|
|performanceCounters/requestsInQueue|Żądania HTTP w kolejce aplikacji|Count|Average|Długość kolejki żądań aplikacji.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Liczba żądań HTTP|CountPerSecond|Average|Liczba wszystkich żądań wysłanych do aplikacji na sekundę z platformy ASP.NET.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|częstotliwość występowania wyjątków|CountPerSecond|Average|Liczba obsługiwanych i nieobsługiwanych wyjątków zgłoszonych w systemie windows, łącznie z wyjątkami platformy .NET i niezarządzanymi wyjątkami przekonwertowanymi na wyjątki platformy .NET.|cloud/roleInstance|
|liczniki wydajności/processIOBytesPerSecond|Proces we/wy|BytesPerSecond|Average|Całkowita liczba bajtów na sekundę odczytywanych i zapisywanych na plikach i sieci oraz urządzenia.|cloud/roleInstance|
|liczniki wydajności/processCpuPercentage|Procesy — procesor CPU|Percent|Average|Procent minionego czasu wszystkich wątków procesów używały procesora w celu wykonania instrukcji. To może się różnić od 0 do 100. Ta Metryka wskazuje wydajność samego procesu w3wp.|cloud/roleInstance|
|liczniki wydajności/processorCpuPercentage|Czas procesora|Percent|Average|Procent czasu, jaki procesor zużywa wątki.|cloud/roleInstance|
|liczniki wydajności/memoryAvailableBytes|Dostępna pamięć|Bajty|Average|Pamięć fizyczna dostępna natychmiast do przydzielenia do procesu lub do użycia przez system.|cloud/roleInstance|
|liczniki wydajności/processPrivateBytes|Prywatne bajty procesu|Bajty|Average|Pamięć przypisana wyłącznie do procesów monitorowanej aplikacji.|cloud/roleInstance|
|żądania/czas trwania|Czas odpowiedzi serwera|MilliSeconds|Average|Czas między odebraniem żądania HTTP i zakończeniem wysyłania odpowiedzi.|żądanie/performanceBucket żądania/resultCode, operacji/syntetycznych, chmura/roleInstance, Powodzenie/na żądanie, chmury/roleName|
|żądania/liczby|Żądania serwera|Count|Count|Ukończonych żądań liczba HTTP.|żądanie/performanceBucket żądania/resultCode, operacji/syntetycznych, chmura/roleInstance, Powodzenie/na żądanie, chmury/roleName|
|requests/failed|Żądania zakończone niepowodzeniem|Count|Count|Liczba HTTP żądania oznaczone jako zakończone niepowodzeniem. W większości przypadków są to żądania z kodem odpowiedzi > = 400 i innym niż 401.|żądanie/performanceBucket żądania/resultCode, operacji/syntetycznych, chmura/roleInstance, chmury/roleName|
|requests/rate|Liczba żądań serwera|CountPerSecond|Average|Liczba żądań serwera, na sekundę|żądanie/performanceBucket żądania/resultCode, operacji/syntetycznych, chmura/roleInstance, Powodzenie/na żądanie, chmury/roleName|
|Wyjątki/liczby|Wyjątki|Count|Count|Łączna liczba wszystkich nieprzechwyconych wyjątków.|Chmura/roleName, chmura/roleInstance, typu/klienta|
|Wyjątki/przeglądarki|Wyjątki przeglądarki|Count|Count|Liczba nieprzechwyconych wyjątków zgłoszonych w przeglądarce.|Nie wymiarów|
|Wyjątki serwera|Wyjątki serwera|Count|Count|Liczba nieprzechwyconych wyjątków zgłoszonych w aplikacji serwera.|Chmura/roleName, chmura/roleInstance|
|dane śledzenia/liczby|ślady|Count|Count|Liczba dokumentów śladów|trace/severityLevel, operacji/syntetycznych, roleName/chmura, chmura/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ServiceApiHit|Usługa łączna liczba trafień interfejsu Api|Count|Count|Całkowita liczba trafień interfejsu api usługi|ActivityType, ActivityName|
|ServiceApiLatency|Całkowity czas oczekiwania interfejsu Api usługi|MS|Average|Ogólny czas oczekiwania usługi interfejsu api żądań|Właściwości ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Usługa łączna liczba wyników interfejsu Api|Count|Count|Całkowita liczba wyników interfejsu api usługi|Właściwości ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CacheUtilization|Wykorzystanie pamięci podręcznej|Percent|Average|Poziom użycia w zakresie klastra|Brak|
|QueryDuration|Czas trwania zapytania|MS|Average|Zapytania czas w sekundach|Stan zapytania|
|IngestionUtilization|Wykorzystanie pozyskiwania|Percent|Average|Współczynnik miejsc pozyskiwania używanych w klastrze|Brak|
|KeepAlive|Podtrzymanie|Count|Average|Poprawnością wyboru wskazuje, że klastra odpowiada na kwerendy|Brak|
|IngestionVolumeInMB|Pozyskiwanie woluminu (w MB)|Count|Łącznie|Łączna ilość pozyskiwanych danych do klastra (w MB)|Database (Baza danych)|
|IngestionLatencyInSeconds|Opóźnienia w pozyskiwaniu danych (w sekundach)|Sekundy|Average|Czas wprowadzania od źródła (np. komunikat jest w Centrum zdarzeń) do klastra w ciągu kilku sekund|Brak|
|EventProcessedForEventHubs|Zdarzenia przetwarzane (dla usługi Event Hubs)|Count|Łącznie|Liczba zdarzeń przetwarzany przez klaster, gdy wprowadzane z Centrum zdarzeń|Brak|
|IngestionResult|Wynik pozyskiwania|Count|Count|Liczby operacji pozyskiwania|Stan|
|Procesor CPU|Procesor CPU|Percent|Average|Poziom użycia procesora CPU|Brak|
| ContinuousExportNumOfRecordsExported | Liczba rekordów wyeksportowany w Eksport ciągły | Count | Łącznie | Liczba rekordów wyeksportowane dla każdego artefaktu magazynu zapisywane podczas operacji eksportowania  | Brak |
| ExportUtilization | Eksportuj wykorzystanie | Percent | Maksimum | Eksportuj wykorzystanie | Brak |
| ContinuousExportPendingCount | Eksport ciągły oczekujące liczba | Count | Maksimum | Liczba oczekujących ciągłe eksportowanie gotowy do wykonania zadania | Brak |
| ContinuousExportMaxLatenessMinutes | Eksport ciągły Max opóźnienie w ciągu minut | Count | Maksimum | Maksymalny czas w minutach wszystkich eksporty ciągłego, które są oczekujące i gotowy do wykonania | Brak |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Użycie|Użycie|Count|Count|Liczba z interfejsu API|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RunsStarted|Przebiegi rozpoczęte|Count|Łącznie|Liczba przebiegów przepływu pracy wprowadzenie.|Nie wymiarów|
|RunsCompleted|Przebiegi ukończone|Count|Łącznie|Liczba przebiegów przepływu pracy ukończonej.|Nie wymiarów|
|RunsSucceeded|Przebiegi zakończone powodzeniem|Count|Łącznie|Liczba przebiegów przepływu pracy zostało zakończone pomyślnie.|Nie wymiarów|
|RunsFailed|Przebiegi zakończone niepowodzeniem|Count|Łącznie|Liczba przebiegów przepływu pracy nie powiodło się.|Nie wymiarów|
|RunsCancelled|Przebiegi anulowane|Count|Łącznie|Liczba przebiegów przepływu pracy anulowane.|Nie wymiarów|
|RunLatency|Opóźnienie przebiegów|Sekundy|Average|Opóźnienie przepływu pracy zakończonych przebiegów.|Nie wymiarów|
|RunSuccessLatency|Opóźnienie przebiegów zakończonych powodzeniem|Sekundy|Average|Opóźnienie przepływu pracy zakończonych powodzeniem przebiegów.|Nie wymiarów|
|RunThrottledEvents|Zdarzenia ograniczenia przebiegu|Count|Łącznie|Liczba akcji przepływu pracy lub wyzwalacz zdarzenia ograniczenia.|Nie wymiarów|
|RunFailurePercentage|Procent niepowodzeń przebiegów|Percent|Łącznie|Procent przebiegów przepływu pracy nie powiodło się.|Nie wymiarów|
|ActionsStarted|Rozpoczęte akcje |Count|Łącznie|Liczba akcji przepływu pracy jest uruchomiony.|Nie wymiarów|
|ActionsCompleted|Ukończone akcje |Count|Łącznie|Liczba ukończonych akcji przepływu pracy.|Nie wymiarów|
|ActionsSucceeded|Akcje zakończone powodzeniem |Count|Łącznie|Liczba akcji przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|ActionsFailed|Akcje zakończone niepowodzeniem|Count|Łącznie|Liczba akcji przepływu pracy nie powiodło się.|Nie wymiarów|
|ActionsSkipped|Pominięte akcje |Count|Łącznie|Liczba akcji przepływu pracy są pomijane.|Nie wymiarów|
|ActionLatency|Opóźnienie akcji |Sekundy|Average|Opóźnienie ukończonych akcji przepływu pracy.|Nie wymiarów|
|ActionSuccessLatency|Opóźnienie akcji – Powodzenie |Sekundy|Average|Opóźnienie akcji przepływu pracy zostało zakończone pomyślnie.|Nie wymiarów|
|ActionThrottledEvents|Zdarzenia ograniczenia akcji|Count|Łącznie|Zdarzenia ograniczenia liczby akcji przepływu pracy...|Nie wymiarów|
|TriggersStarted|Rozpoczęte wyzwalacze |Count|Łącznie|Liczba wyzwalaczy przepływu pracy jest uruchomiony.|Nie wymiarów|
|TriggersCompleted|Ukończone wyzwalacze |Count|Łącznie|Liczba ukończonych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggersSucceeded|Udane wyzwalacze |Count|Łącznie|Liczba wyzwalaczy przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|TriggersFailed|Nieudane wyzwalacze |Count|Łącznie|Liczba wyzwalaczy przepływu pracy nie powiodło się.|Nie wymiarów|
|TriggersSkipped|Pominięte wyzwalacze|Count|Łącznie|Liczba wyzwalaczy przepływu pracy są pomijane.|Nie wymiarów|
|TriggersFired|Uruchomione wyzwalacze |Count|Łącznie|Liczba wyzwalaczy przepływu pracy jest uruchamiany.|Nie wymiarów|
|TriggerLatency|Opóźnienie wyzwalacza |Sekundy|Average|Opóźnienie ukończonych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggerFireLatency|Opóźnienie uruchomienia wyzwalacza |Sekundy|Average|Opóźnienie uaktywnionych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggerSuccessLatency|Opóźnienie udanego wyzwalacza |Sekundy|Average|Opóźnienie wyzwalaczy przepływu pracy zostało zakończone pomyślnie.|Nie wymiarów|
|TriggerThrottledEvents|Zdarzenia ograniczenia wyzwalacza|Count|Łącznie|Zdarzenia ograniczenia liczby wyzwalacza przepływu pracy.|Nie wymiarów|
|BillableActionExecutions|Rozliczane wykonania akcji|Count|Łącznie|Liczba rozliczanych wykonań akcji przepływu pracy.|Nie wymiarów|
|BillableTriggerExecutions|Rozliczane wykonania wyzwalaczy|Count|Łącznie|Liczba rozliczanych wykonań wyzwalaczy przepływu pracy.|Nie wymiarów|
|TotalBillableExecutions|Łączna liczba rozliczanych wykonań|Count|Łącznie|Liczba rozliczanych wykonań przepływu pracy.|Nie wymiarów|
|BillingUsageNativeOperation|Fakturowania użycia dla wykonania działania natywnego|Count|Łącznie|Liczba rozliczanych wykonań natywnych operacji.|Nie wymiarów|
|BillingUsageStandardConnector|Fakturowania użycia dla wykonania łącznika standardowego|Count|Łącznie|Liczba rozliczanych wykonań łączników standardowych.|Nie wymiarów|
|BillingUsageStorageConsumption|Fakturowania użycia dla wykonania użycie magazynu|Count|Łącznie|Liczba rozliczanych wykonań zużycie pamięci masowej.|Nie wymiarów|
|BillingUsageNativeOperation|Fakturowania użycia dla wykonania działania natywnego|Count|Łącznie|Liczba rozliczanych wykonań natywnych operacji.|Nie wymiarów|
|BillingUsageStandardConnector|Fakturowania użycia dla wykonania łącznika standardowego|Count|Łącznie|Liczba rozliczanych wykonań łączników standardowych.|Nie wymiarów|
|BillingUsageStorageConsumption|Fakturowania użycia dla wykonania użycie magazynu|Count|Łącznie|Liczba rozliczanych wykonań zużycie pamięci masowej.|Nie wymiarów|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RunsStarted|Przebiegi rozpoczęte|Count|Łącznie|Liczba przebiegów przepływu pracy wprowadzenie.|Nie wymiarów|
|RunsCompleted|Przebiegi ukończone|Count|Łącznie|Liczba przebiegów przepływu pracy ukończonej.|Nie wymiarów|
|RunsSucceeded|Przebiegi zakończone powodzeniem|Count|Łącznie|Liczba przebiegów przepływu pracy zostało zakończone pomyślnie.|Nie wymiarów|
|RunsFailed|Przebiegi zakończone niepowodzeniem|Count|Łącznie|Liczba przebiegów przepływu pracy nie powiodło się.|Nie wymiarów|
|RunsCancelled|Przebiegi anulowane|Count|Łącznie|Liczba przebiegów przepływu pracy anulowane.|Nie wymiarów|
|RunLatency|Opóźnienie przebiegów|Sekundy|Average|Opóźnienie przepływu pracy zakończonych przebiegów.|Nie wymiarów|
|RunSuccessLatency|Opóźnienie przebiegów zakończonych powodzeniem|Sekundy|Average|Opóźnienie przepływu pracy zakończonych powodzeniem przebiegów.|Nie wymiarów|
|RunThrottledEvents|Zdarzenia ograniczenia przebiegu|Count|Łącznie|Liczba akcji przepływu pracy lub wyzwalacz zdarzenia ograniczenia.|Nie wymiarów|
|RunStartThrottledEvents|Zdarzenia ograniczenia rozpoczęcia przebiegu|Count|Łącznie|Zdarzenia ograniczenia liczby rozpoczęcia przebiegu przepływu pracy.|Nie wymiarów|
|RunFailurePercentage|Procent niepowodzeń przebiegów|Percent|Łącznie|Procent przebiegów przepływu pracy nie powiodło się.|Nie wymiarów|
|ActionsStarted|Rozpoczęte akcje |Count|Łącznie|Liczba akcji przepływu pracy jest uruchomiony.|Nie wymiarów|
|ActionsCompleted|Ukończone akcje |Count|Łącznie|Liczba ukończonych akcji przepływu pracy.|Nie wymiarów|
|ActionsSucceeded|Akcje zakończone powodzeniem |Count|Łącznie|Liczba akcji przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|ActionsFailed|Akcje zakończone niepowodzeniem |Count|Łącznie|Liczba akcji przepływu pracy nie powiodło się.|Nie wymiarów|
|ActionsSkipped|Pominięte akcje |Count|Łącznie|Liczba akcji przepływu pracy są pomijane.|Nie wymiarów|
|ActionLatency|Opóźnienie akcji |Sekundy|Average|Opóźnienie ukończonych akcji przepływu pracy.|Nie wymiarów|
|ActionSuccessLatency|Opóźnienie akcji – Powodzenie |Sekundy|Average|Opóźnienie akcji przepływu pracy zostało zakończone pomyślnie.|Nie wymiarów|
|ActionThrottledEvents|Zdarzenia ograniczenia akcji|Count|Łącznie|Zdarzenia ograniczenia liczby akcji przepływu pracy...|Nie wymiarów|
|TriggersStarted|Rozpoczęte wyzwalacze |Count|Łącznie|Liczba wyzwalaczy przepływu pracy jest uruchomiony.|Nie wymiarów|
|TriggersCompleted|Ukończone wyzwalacze |Count|Łącznie|Liczba ukończonych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggersSucceeded|Udane wyzwalacze |Count|Łącznie|Liczba wyzwalaczy przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|TriggersFailed|Nieudane wyzwalacze |Count|Łącznie|Liczba wyzwalaczy przepływu pracy nie powiodło się.|Nie wymiarów|
|TriggersSkipped|Pominięte wyzwalacze|Count|Łącznie|Liczba wyzwalaczy przepływu pracy są pomijane.|Nie wymiarów|
|TriggersFired|Uruchomione wyzwalacze |Count|Łącznie|Liczba wyzwalaczy przepływu pracy jest uruchamiany.|Nie wymiarów|
|TriggerLatency|Opóźnienie wyzwalacza |Sekundy|Average|Opóźnienie ukończonych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggerFireLatency|Opóźnienie uruchomienia wyzwalacza |Sekundy|Average|Opóźnienie uaktywnionych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggerSuccessLatency|Opóźnienie udanego wyzwalacza |Sekundy|Average|Opóźnienie wyzwalaczy przepływu pracy zostało zakończone pomyślnie.|Nie wymiarów|
|TriggerThrottledEvents|Zdarzenia ograniczenia wyzwalacza|Count|Łącznie|Zdarzenia ograniczenia liczby wyzwalacza przepływu pracy.|Nie wymiarów|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Użycie procesora przepływu pracy w środowisku usługi integracji|Percent|Average|Użycie procesora przepływu pracy w środowisku usługi integracji.|Nie wymiarów|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Użycie pamięci przepływu pracy w środowisku usługi integracji|Percent|Average|Użycie pamięci przepływu pracy w środowisku usługi integracji.|Nie wymiarów|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Użycie procesora łącznika dla środowiska usługi integracji|Percent|Average|Użycie procesora łącznika dla środowiska usługi integracji.|Nie wymiarów|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Użycie pamięci łącznika dla środowiska usługi integracji|Percent|Average|Łącznik wykorzystania pamięci w środowisku usługi integracji.|Nie wymiarów|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Zakończone przebiegi|Zakończone przebiegi|Count|Łącznie|Liczba przebiegów zakończonych pomyślnie dla tego obszaru roboczego|Scenariusz|
|Przebiegi rozpoczęte|Przebiegi rozpoczęte|Count|Łącznie|Liczba przebiegów wprowadzenie dla tego obszaru roboczego|Scenariusz|
|Nieudane uruchomienia|Nieudane uruchomienia|Count|Łącznie|Liczba przebiegów zakończonych niepowodzeniem dla tego obszaru roboczego|Scenariusz|

## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Użycie|Użycie|Count|Count|Liczba z interfejsu API|ApiCategory, ApiName, ResultType, ResponseCode|
|Dostępność|Dostępność|Procent|Average|Dostępność interfejsów API|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AverageOtherLatency|Średnie opóźnienie innych|ms/op|Average|Średni innych czas oczekiwania (który nie jest odczyt lub zapis) w MS na operację|Nie wymiarów|
|AverageReadLatency|Średnia, opóźnienie odczytu|ms/op|Average|Średni czas oczekiwania (w milisekundach) dla operacji odczytu|Nie wymiarów|
|AverageTotalLatency|Średni całkowity czas oczekiwania|ms/op|Average|Średnie opóźnienie całkowite w MS na operację|Nie wymiarów|
|AverageWriteLatency|Opóźnienie zapisu średni|ms/op|Average|Opóźnienie zapisu średnia w MS na operację|Nie wymiarów|
|FilesystemOtherOps|System plików innych operacjach|Platforma OPS|Average|Liczba plików inne operacje (nie jest odczyt lub zapis)|Nie wymiarów|
|FilesystemReadOps|Operacje odczytu dla systemu plików|Platforma OPS|Average|Liczba plików, operacje odczytu|Nie wymiarów|
|FilesystemTotalOps|Łączna liczba operacji systemu plików|Platforma OPS|Average|Suma wszystkich operacji systemu plików|Nie wymiarów|
|FilesystemWriteOps|Operacje zapisu systemu plików|Platforma OPS|Average|Liczba operacji zapisu w systemie plików|Nie wymiarów|
|IoBytesPerOtherOps|Bajty We/Wy na innych operacjach|Bajty/op|Average|Numer elementu w pionie i w poziomie bajtów na inne operacje (nie jest odczyt lub zapis)|Nie wymiarów|
|IoBytesPerReadOps|Bajty We/Wy na operacje odczytu|Bajty/op|Average|Liczba bajtów na operację odczytu We/Wy|Nie wymiarów|
|IoBytesPerTotalOps|Bajty We/Wy na op we wszystkich operacjach|Bajty/op|Average|Suma wszystkich we/wy operacji bajtów|Nie wymiarów|
|IoBytesPerWriteOps|Bajty We/Wy dla operacji zapisu|Bajty/op|Average|Numer elementu w pionie i w poziomie bajtów na operację zapisu|Nie wymiarów|
|OtherIops|Inne operacje We/Wy|operacje na sekundę|Average|Inne we/wy operacji na sekundę|Nie wymiarów|
|OtherThroughput|Inne przepływności|MB/s|Average|Inne przepływności (nie jest odczyt lub zapis) w MB na sekundę|Nie wymiarów|
|ReadIops|Operacje odczytu We/Wy|operacje na sekundę|Average|Odczyt/Ściemnianie operacji na sekundę|Nie wymiarów|
|ReadThroughput|Przepływność odczytu|MB/s|Average|Przeczytaj przepustowość w MB na sekundę|Nie wymiarów|
|TotalIops|Łączna liczba operacji We/Wy|operacje na sekundę|Average|Suma wszystkich we/wy operacji na sekundę|Nie wymiarów|
|TotalThroughput|Łączna przepływność|MB/s|Average|Suma wszystkich przepustowość w MB na sekundę|Nie wymiarów|
|VolumeAllocatedSize|Przydzielony rozmiar woluminu|B|Average|Przydzielony rozmiar woluminu (nie rzeczywiste używane w bajtach)|Nie wymiarów|
|VolumeLogicalSize|Rozmiar logiczny woluminu|B|Average|Rozmiar logiczny woluminu (używanych w bajtach)|Nie wymiarów|
|VolumeSnapshotSize|Rozmiar migawki woluminu|B|Average|Rozmiar wszystkich migawek w woluminie|Nie wymiarów|
|WriteIops|Operacje We/Wy zapisu|operacje na sekundę|Average|Zapisu We/Wy operacji na sekundę|Nie wymiarów|
|WriteThroughput|Przepływność zapisu|MB/s|Average|Zapisywanie przepustowość w MB na sekundę|Nie wymiarów|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Rozmiar puli przydzielona woluminu|B|Average|Przydzielony rozmiar puli (nie rzeczywiste używane w bajtach)|Nie wymiarów|
|VolumePoolAllocatedUsed|Pula woluminów przydzielonych używane|B|Average|Przydzielony rozmiar używane w puli|Nie wymiarów|
|VolumePoolTotalLogicalSize|Całkowity rozmiar logiczny woluminu puli|B|Average|Suma rozmiaru logicznego wszystkich woluminów należących do puli|Nie wymiarów|
|VolumePoolTotalSnapshotSize|Rozmiar migawki całkowitej puli woluminu|B|Average|Suma wszystkich migawek w puli|Nie wymiarów|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BytesSentRate|Bajty wysłane|Count|Łącznie|Liczba bajtów wysyłane interfejsu sieciowego|Nie wymiarów|
|BytesReceivedRate|Odebrane bajty|Count|Łącznie|Liczba bajtów odebranych interfejsu sieciowego|Nie wymiarów|
|PacketsSentRate|Pakiety wysłane|Count|Łącznie|Liczba pakietów interfejsu sieciowego, wysłane|Nie wymiarów|
|PacketsReceivedRate|Odebrane pakiety|Count|Łącznie|Liczba pakietów na interfejsie sieciowym otrzymane|Nie wymiarów|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|VipAvailability|Dostępność ścieżki danych|Count|Average|Średnia dostępność ścieżki danych modułu równoważenia obciążenia na czas trwania|FrontendIPAddress, FrontendPort|
|DipAvailability|Stan sondy kondycji|Count|Average|Średnia stan sondy kondycji modułu równoważenia obciążenia na czas trwania|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Liczba bajtów|Count|Łącznie|Całkowita liczba bajtów przesłanych w przedziale czasu|Kierunek FrontendIPAddress, FrontendPort,|
|PacketCount|Liczba pakietów|Count|Łącznie|Całkowita liczba pakietów przesyłanych w przedziale czasu|Kierunek FrontendIPAddress, FrontendPort,|
|SYNCount|Liczba SYN|Count|Łącznie|Całkowita liczba pakietów SYN przesyłane w przedziale czasu|Kierunek FrontendIPAddress, FrontendPort,|
|SnatConnectionCount|Liczba połączeń SNAT|Count|Łącznie|Całkowita liczba nowych połączeń SNAT utworzone w przedziale czasu|Element ConnectionState FrontendIPAddress, BackendIPAddress,|
|AllocatedSnatPorts|Przydzielonych portów SNAT (wersja zapoznawcza)|Count|Łącznie|Całkowita liczba portów SNAT przydzielone w przedziale czasu|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Używane porty SNAT (wersja zapoznawcza)|Count|Łącznie|Całkowita liczba portów SNAT używane w przedziale czasu|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QueryVolume|Wolumin zapytań|Count|Łącznie|Liczba zapytań dla strefy DNS|Nie wymiarów|
|RecordSetCount|Liczba zestawu rekordów|Count|Maksimum|Liczba zestawów rekordów w strefie DNS|Nie wymiarów|
|RecordSetCapacityUtilization|Wykorzystanie pojemności zestawu rekordów|Percent|Maksimum|Wartość procentowa pojemności zestawu rekordów, wykorzystywany przez strefę DNS|Nie wymiarów|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PacketsInDDoS|Liczba przychodzących pakietów przed atakami DDoS|CountPerSecond|Maksimum|Liczba przychodzących pakietów przed atakami DDoS|Nie wymiarów|
|PacketsDroppedDDoS|Liczba porzuconych pakietów przychodzących przed atakami DDoS|CountPerSecond|Maksimum|Liczba porzuconych pakietów przychodzących przed atakami DDoS|Nie wymiarów|
|PacketsForwardedDDoS|Pakiety przychodzące przekazywane przed atakami DDoS|CountPerSecond|Maksimum|Pakiety przychodzące przekazywane przed atakami DDoS|Nie wymiarów|
|TCPPacketsInDDoS|Liczba przychodzących pakietów protokołu TCP przed atakami DDoS|CountPerSecond|Maksimum|Liczba przychodzących pakietów protokołu TCP przed atakami DDoS|Nie wymiarów|
|TCPPacketsDroppedDDoS|Pakiety przychodzące TCP porzucony przed atakami DDoS|CountPerSecond|Maksimum|Pakiety przychodzące TCP porzucony przed atakami DDoS|Nie wymiarów|
|TCPPacketsForwardedDDoS|Pakiety przychodzące TCP przekazywane przed atakami DDoS|CountPerSecond|Maksimum|Pakiety przychodzące TCP przekazywane przed atakami DDoS|Nie wymiarów|
|UDPPacketsInDDoS|Liczba przychodzących pakietów UDP przed atakami DDoS|CountPerSecond|Maksimum|Liczba przychodzących pakietów UDP przed atakami DDoS|Nie wymiarów|
|UDPPacketsDroppedDDoS|Przerywanych pakietów UDP porzucony przed atakami DDoS|CountPerSecond|Maksimum|Przerywanych pakietów UDP porzucony przed atakami DDoS|Nie wymiarów|
|UDPPacketsForwardedDDoS|Przerywanych pakietów UDP przekazywane przed atakami DDoS|CountPerSecond|Maksimum|Przerywanych pakietów UDP przekazywane przed atakami DDoS|Nie wymiarów|
|BytesInDDoS|Przychodzące bajty przed atakami DDoS|BytesPerSecond|Maksimum|Przychodzące bajty przed atakami DDoS|Nie wymiarów|
|BytesDroppedDDoS|Przychodzące bajty porzucony przed atakami DDoS|BytesPerSecond|Maksimum|Przychodzące bajty porzucony przed atakami DDoS|Nie wymiarów|
|BytesForwardedDDoS|Przychodzące bajty przekazywane przed atakami DDoS|BytesPerSecond|Maksimum|Przychodzące bajty przekazywane przed atakami DDoS|Nie wymiarów|
|TCPBytesInDDoS|Przychodzące bajty TCP przed atakami DDoS|BytesPerSecond|Maksimum|Przychodzące bajty TCP przed atakami DDoS|Nie wymiarów|
|TCPBytesDroppedDDoS|Przychodzące bajty TCP porzucony przed atakami DDoS|BytesPerSecond|Maksimum|Przychodzące bajty TCP porzucony przed atakami DDoS|Nie wymiarów|
|TCPBytesForwardedDDoS|Przychodzące bajty TCP przekazywane przed atakami DDoS|BytesPerSecond|Maksimum|Przychodzące bajty TCP przekazywane przed atakami DDoS|Nie wymiarów|
|UDPBytesInDDoS|Przychodzące bajty UDP przed atakami DDoS|BytesPerSecond|Maksimum|Przychodzące bajty UDP przed atakami DDoS|Nie wymiarów|
|UDPBytesDroppedDDoS|Przychodzące bajty UDP porzucony przed atakami DDoS|BytesPerSecond|Maksimum|Przychodzące bajty UDP porzucony przed atakami DDoS|Nie wymiarów|
|UDPBytesForwardedDDoS|Przychodzące bajty UDP przekazywane przed atakami DDoS|BytesPerSecond|Maksimum|Przychodzące bajty UDP przekazywane przed atakami DDoS|Nie wymiarów|
|IfUnderDDoSAttack|W obszarze przed atakami DDoS ataku lub nie|Count|Maksimum|W obszarze przed atakami DDoS ataku lub nie|Nie wymiarów|
|DDoSTriggerTCPPackets|Liczba przychodzących pakietów protokołu TCP do wyzwolenia ataki DDOS|CountPerSecond|Maksimum|Liczba przychodzących pakietów protokołu TCP do wyzwolenia ataki DDOS|Nie wymiarów|
|DDoSTriggerUDPPackets|Liczba przychodzących pakietów UDP do wyzwolenia ataki DDOS|CountPerSecond|Maksimum|Liczba przychodzących pakietów UDP do wyzwolenia ataki DDOS|Nie wymiarów|
|DDoSTriggerSYNPackets|Liczba przychodzących pakietów SYN do wyzwolenia ataki DDOS|CountPerSecond|Maksimum|Liczba przychodzących pakietów SYN do wyzwolenia ataki DDOS|Nie wymiarów|
|VipAvailability|Dostępność ścieżki danych|Count|Average|Średnia dostępność adresu IP na czas trwania|Port|
|ByteCount|Liczba bajtów|Count|Łącznie|Całkowita liczba bajtów przesłanych w przedziale czasu|Portu i kierunek|
|PacketCount|Liczba pakietów|Count|Łącznie|Całkowita liczba pakietów przesyłanych w przedziale czasu|Portu i kierunek|
|SynCount|Liczba SYN|Count|Łącznie|Całkowita liczba pakietów SYN przesyłane w przedziale czasu|Portu i kierunek|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ApplicationRuleHit|Liczba trafień reguł aplikacji|Count|Łącznie|Ile razy zostały trafień reguł aplikacji|Protokół stanu, powodu|
|NetworkRuleHit|Liczba trafień reguł sieciowych|Count|Łącznie|Ile razy zostały trafień reguł sieciowych|Protokół stanu, powodu|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Przepływność|Przepływność|BytesPerSecond|Łącznie|Liczba bajtów na sekundę, który obsługiwał Application Gateway|Nie wymiarów|
|UnhealthyHostCount|Liczba hostów w złej kondycji|Count|Average|Liczba hostów w złej kondycji wewnętrznej bazy danych|BackendSettingsPool|
|HealthyHostCount|Liczba hostów w dobrej kondycji|Count|Average|Liczba hostów zaplecza w dobrej kondycji|BackendSettingsPool|
|TotalRequests|Łączna liczba żądań|Count|Łącznie|Liczba pomyślnych żądań, które usługa Application Gateway jest obsługiwane|BackendSettingsPool|
|FailedRequests|Żądania zakończone niepowodzeniem|Count|Łącznie|Liczba nieudanych żądań, które usługa Application Gateway jest obsługiwane|BackendSettingsPool|
|ResponseStatus|Stan odpowiedzi|Count|Łącznie|Stan odpowiedzi HTTP zwrócony przez usługę Application Gateway|HttpStatusGroup|
|Wartości CurrentConnections|Bieżąca liczba połączeń|Count|Łącznie|Liczba bieżących połączeń ustanowionych z usługą Application Gateway|Nie wymiarów|
|CapacityUnits|Bieżące jednostki pojemności|Count|Average|Wykorzystane jednostki pojemności|Nie wymiarów|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AverageBandwidth|Brama S2S przepustowości|BytesPerSecond|Average|Średnia przepustowość lokacja lokacja bramy w bajtach na sekundę|Nie wymiarów|
|P2SBandwidth|Przepustowość P2S bramy|BytesPerSecond|Average|Średnia przepustowość punkt lokacja bramy w bajtach na sekundę|Nie wymiarów|
|P2SConnectionCount|Liczba połączeń P2S|Count|Maksimum|Liczba połączeń punkt lokacja bramy|Protocol|
|TunnelAverageBandwidth|Przepustowość tunelu|BytesPerSecond|Average|Średnia przepustowość tunelu w bajtach na sekundę|ConnectionName, RemoteIP|
|TunnelEgressBytes|Bajty wychodzące tunelu|Bajty|Łącznie|Wychodzące bajty tunel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Bajty przychodzące tunelu|Bajty|Łącznie|Przychodzące bajty tunel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Pakiety wychodzące tunelu|Count|Łącznie|Liczba wychodzących pakietów tunelu|ConnectionName, RemoteIP|
|TunnelIngressPackets|Pakiety przychodzące tunelu|Count|Łącznie|Liczba przychodzących pakietów tunelu|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Porzucanie pakietów niezgodność ruchu wychodzącego usług terminalowych tunelu|Count|Łącznie|Liczba wychodzących pakietów listy z niezgodności selektor ruchu tunelu|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Porzucanie pakietów niezgodność ruch przychodzący serwera terminali tunelu|Count|Łącznie|Liczba przychodzących pakietów listy z niezgodności selektor ruchu tunelu|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Usługa BITS ingressing platformy Azure na sekundę|Elementowi PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Usługa BITS egressing platformy Azure na sekundę|Elementowi PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Usługa BITS ingressing platformy Azure na sekundę|Nie wymiarów|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Usługa BITS egressing platformy Azure na sekundę|Nie wymiarów|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Usługa BITS ingressing platformy Azure na sekundę|Nie wymiarów|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Usługa BITS egressing platformy Azure na sekundę|Nie wymiarów|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QpsByEndpoint|Zapytania według zwracany punkt końcowy|Count|Łącznie|Liczba przypadków, gdy punkt końcowy usługi Traffic Manager zwrócił się w określonym przedziale czasu|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Stan punktu końcowego na punkt końcowy|Count|Maksimum|1, jeśli punkt końcowy sondowania stanu jest "włączone", w przeciwnym razie 0.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Sondy nie powiodło się|Percent|Average|% łączności sondy monitorujące nie powiodło się.|Nie wymiarów|
|AverageRoundtripMs|Średni Czas obustronnej konwersji (ms)|MilliSeconds|Average|Czas błądzenia średni sieci (ms) dla łączności przesyłane między źródłowym i docelowym sondy monitorujące|Nie wymiarów|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RequestCount|Liczba żądań|Count|Łącznie|Liczba żądań klientów obsługiwanych przez serwer proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Rozmiar żądania|Bajty|Łącznie|Liczba bajtów wysłanych jako żądania od klientów z serwerem proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Rozmiar odpowiedzi|Bajty|Łącznie|Liczba bajtów wysłanych jako odpowiedzi z serwera proxy HTTP/Https na klientach|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Liczba żądań wewnętrznej bazy danych|Count|Łącznie|Liczba żądań wysyłanych z serwera proxy HTTP/Https do zaplecza|HttpStatus, HttpStatusGroup, wewnętrznej bazy danych|
|BackendRequestLatency|Opóźnienia żądania wewnętrznej bazy danych|MilliSeconds|Average|Czas obliczonym na podstawie Jeśli żądanie zostało wysłane przez serwer proxy HTTP/Https do wewnętrznej bazy danych do momentu otrzymania przez serwer proxy HTTP/Https ostatni bajt odpowiedzi z wewnętrznej bazy danych|Wewnętrznej bazy danych|
|TotalLatency|Całkowity czas oczekiwania|MilliSeconds|Average|Czas obliczonym na podstawie żądań klienta otrzymania przez serwer proxy HTTP/Https momentu klient potwierdzenia ostatni bajt odpowiedzi z serwera proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Wartość procentowa kondycję wewnętrznej bazy danych|Percent|Average|Procent pomyślnych kondycji sondy z serwera proxy HTTP/Https do zaplecza|Backend, BackendPool|
|WebApplicationFirewallRequestCount|Liczba żądań zapory aplikacji sieci Web|Count|Łącznie|Liczba żądań klienta przetwarzanych przez zaporę aplikacji sieci Web|PolicyName, Nazwa_reguły, akcja|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Registration.all|Operacje rejestracji|Count|Łącznie|Liczba wszystkich operacji rejestracji zakończonych powodzeniem (operacje tworzenia, aktualizacje, zapytania i operacje usuwania). |Nie wymiarów|
|registration.create|Operacje tworzenia rejestracji|Count|Łącznie|Liczba wszystkie operacje tworzenia rejestracji zakończonych powodzeniem.|Nie wymiarów|
|registration.update|Operacje aktualizacji operacji|Count|Łącznie|Liczba wszystkich aktualizacji rejestracji zakończonych powodzeniem.|Nie wymiarów|
|registration.get|Operacje odczytu operacji|Count|Łącznie|Liczba wszystkich zapytań rejestracji zakończonych powodzeniem.|Nie wymiarów|
|registration.delete|Operacje usuwania rejestracji|Count|Łącznie|Liczba wszystkie operacje usuwania rejestracji zakończonych powodzeniem.|Nie wymiarów|
|przychodzące|Komunikaty przychodzące|Count|Łącznie|Liczba wszystkich pomyślnie wysyłania wywołania interfejsu API. |Nie wymiarów|
|incoming.scheduled|Wysłano zaplanowane powiadomienia wypychane|Count|Łącznie|Zaplanowane powiadomienia wypychane zostało anulowane|Nie wymiarów|
|incoming.scheduled.cancel|Zaplanowane powiadomienia wypychane zostało anulowane|Count|Łącznie|Zaplanowane powiadomienia wypychane zostało anulowane|Nie wymiarów|
|scheduled.pending|Oczekujące zaplanowane powiadomienia|Count|Łącznie|Oczekujące zaplanowane powiadomienia|Nie wymiarów|
|Installation.all|Operacje zarządzania instalacją|Count|Łącznie|Operacje zarządzania instalacją|Nie wymiarów|
|installation.get|Pobierz operacje instalacji|Count|Łącznie|Pobierz operacje instalacji|Nie wymiarów|
|installation.upsert|Utwórz lub zaktualizuj operacje instalacji|Count|Łącznie|Utwórz lub zaktualizuj operacje instalacji|Nie wymiarów|
|installation.patch|Operacje instalacji poprawki|Count|Łącznie|Operacje instalacji poprawki|Nie wymiarów|
|installation.delete|Usuń operacje instalacji|Count|Łącznie|Usuń operacje instalacji|Nie wymiarów|
|outgoing.allpns.success|Powiadomienia zakończone powodzeniem|Count|Łącznie|Liczba wszystkich powiadomień zakończonych powodzeniem.|Nie wymiarów|
|Outgoing.allpns.invalidpayload|Błędy ładunku|Count|Łącznie|Liczba wypchnięć, które nie powiodło się, ponieważ system powiadomień platformy zwrócił błąd oznaczający nieprawidłowy ładunek.|Nie wymiarów|
|outgoing.allpns.pnserror|Błędy zewnętrznych systemów powiadamiania|Count|Łącznie|Liczba wypchnięć, które nie powiodło się, ponieważ wystąpił problem podczas komunikowania się z systemu powiadomień platformy (z wyłączeniem problemy z uwierzytelnianiem).|Nie wymiarów|
|outgoing.allpns.channelerror|Błędy kanału|Count|Łącznie|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ kanał był nieprawidłowy, nie jest skojarzony z poprawną aplikacją ograniczoną lub wygasłą.|Nie wymiarów|
|outgoing.allpns.badorexpiredchannel|Błędy nieprawidłowych lub wygasłych kanałów|Count|Łącznie|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ kanał/token/identyfikator rejestracji wygasł lub był nieprawidłowy.|Nie wymiarów|
|outgoing.wns.success|Powiadomienia zakończone powodzeniem usługi WNS|Count|Łącznie|Liczba wszystkich powiadomień zakończonych powodzeniem.|Nie wymiarów|
|outgoing.wns.invalidcredentials|Błędy autoryzacji usługi WNS (nieprawidłowe poświadczenia)|Count|Łącznie|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ system powiadomień platformy nie zaakceptował podanych poświadczeń lub poświadczenia zostały zablokowane. (Windows Live nie rozpoznaje poświadczeń).|Nie wymiarów|
|outgoing.wns.badchannel|Błąd nieprawidłowego kanału usługi WNS|Count|Łącznie|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ identyfikator ChannelURI w rejestracji nie został rozpoznany (stan usługi WNS: 404 Nie znaleziono).|Nie wymiarów|
|outgoing.wns.expiredchannel|Usługi WNS błąd wygasłego kanału|Count|Łącznie|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ identyfikator ChannelURI wygasł (stan usługi WNS: 410 Gone).|Nie wymiarów|
|Outgoing.wns.throttled|Powiadomienia usługi WNS z ograniczoną przepływnością|Count|Łącznie|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ usługi WNS ogranicza tę aplikację (stan usługi WNS: 406 nie do przyjęcia).|Nie wymiarów|
|outgoing.wns.tokenproviderunreachable|Błędy autoryzacji usługi WNS (niedostępna)|Count|Łącznie|Windows Live jest nieosiągalny.|Nie wymiarów|
|outgoing.wns.invalidtoken|Błędy autoryzacji usługi WNS (nieprawidłowy Token)|Count|Łącznie|Token przekazany do usługi WNS jest nieprawidłowy (stan usługi WNS: 401 Brak autoryzacji).|Nie wymiarów|
|outgoing.wns.wrongtoken|Błędy autoryzacji usługi WNS (niepoprawny Token)|Count|Łącznie|Token przekazany do usługi WNS jest prawidłowy, ale dla innej aplikacji (stan usługi WNS: 403 Forbidden). Może to nastąpić, jeśli identyfikator ChannelURI w rejestracji jest skojarzony z inną aplikacją. Upewnij się, że aplikacja klienta została skojarzona z aplikacją, której poświadczenia znajdują się w Centrum powiadomień.|Nie wymiarów|
|outgoing.wns.invalidnotificationformat|Usługi WNS nieprawidłowy Format powiadomienia|Count|Łącznie|Format powiadomienia jest nieprawidłowy (stan usługi WNS: 400). Należy pamiętać, że usługa WNS nie odrzuca wszystkich nieprawidłowych ładunków.|Nie wymiarów|
|outgoing.wns.invalidnotificationsize|Usługi WNS błąd nieprawidłowego rozmiaru powiadomienia|Count|Łącznie|Ładunek powiadomienia jest zbyt duży (stan usługi WNS: 413).|Nie wymiarów|
|outgoing.wns.channelthrottled|Ograniczono przepływność kanału usługi WNS|Count|Łącznie|Powiadomienie zostało porzucone, ponieważ identyfikator ChannelURI w rejestracji jest ograniczany (nagłówek odpowiedzi usługi WNS: X-WNS-NotificationStatus:channelThrottled).|Nie wymiarów|
|outgoing.wns.channeldisconnected|Rozłączono kanał usługi WNS|Count|Łącznie|Powiadomienie zostało porzucone, ponieważ identyfikator ChannelURI w rejestracji jest ograniczany (nagłówek odpowiedzi usługi WNS: X-WNS-DeviceConnectionStatus: disconnected).|Nie wymiarów|
|outgoing.wns.dropped|Porzucone powiadomienia usługi WNS|Count|Łącznie|Powiadomienie zostało porzucone, ponieważ identyfikator ChannelURI w rejestracji jest ograniczany (X-WNS-NotificationStatus: Porzucono, ale nie X-WNS-DeviceConnectionStatus: Rozłączono).|Nie wymiarów|
|outgoing.wns.pnserror|Błędy usługi WNS|Count|Łącznie|Nie dostarczono powiadomienia z powodu błędów podczas komunikowania się z usługą WNS.|Nie wymiarów|
|outgoing.wns.authenticationerror|Błędy uwierzytelniania usługi WNS|Count|Łącznie|Nie dostarczono powiadomienia z powodu błędów podczas komunikowania się z błędnym tokenem lub nieprawidłowymi poświadczeniami usługi Windows Live.|Nie wymiarów|
|outgoing.apns.success|Powiadomienia zakończone powodzeniem usługi APNS|Count|Łącznie|Liczba wszystkich powiadomień zakończonych powodzeniem.|Nie wymiarów|
|outgoing.apns.invalidcredentials|Błędy autoryzacji usługi APNS|Count|Łącznie|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ system powiadomień platformy nie zaakceptował podanych poświadczeń lub poświadczenia zostały zablokowane.|Nie wymiarów|
|outgoing.apns.badchannel|Błąd nieprawidłowego kanału usługi APNS|Count|Łącznie|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ token jest nieprawidłowy (kod stanu usługi APNS: 8).|Nie wymiarów|
|outgoing.apns.expiredchannel|APNS Expired Channel Error|Count|Łącznie|Liczba tokenów unieważnionych przy kanału sprzężenia zwrotnego usługi APNS.|Nie wymiarów|
|outgoing.apns.invalidnotificationsize|APNS błąd nieprawidłowego rozmiaru powiadomienia|Count|Łącznie|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ ładunek powiadomienia był zbyt duży (kod stanu usługi APNS: 7).|Nie wymiarów|
|outgoing.apns.pnserror|Błędy usługi APNS|Count|Łącznie|Liczba wypchnięć, które zakończyły się niepowodzeniem z powodu błędów podczas komunikowania się z usługą APNS.|Nie wymiarów|
|outgoing.gcm.success|Powiadomienia zakończone powodzeniem usługi GCM|Count|Łącznie|Liczba wszystkich powiadomień zakończonych powodzeniem.|Nie wymiarów|
|outgoing.gcm.invalidcredentials|Błędy autoryzacji usługi GCM (nieprawidłowe poświadczenia)|Count|Łącznie|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ system powiadomień platformy nie zaakceptował podanych poświadczeń lub poświadczenia zostały zablokowane.|Nie wymiarów|
|outgoing.gcm.badchannel|Błąd nieprawidłowego kanału usługi GCM|Count|Łącznie|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ identyfikator rejestracji nie został rozpoznany (wynik usługi GCM: Nieprawidłowa rejestracja).|Nie wymiarów|
|outgoing.gcm.expiredchannel|Usługa GCM błąd wygasłego kanału|Count|Łącznie|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ identyfikator rejestracji wygasł (wynik usługi GCM: NotRegistered).|Nie wymiarów|
|outgoing.gcm.throttled|Powiadomienia usługi GCM z ograniczoną przepływnością|Count|Łącznie|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ usługa GCM ograniczyła tę aplikację (kod stanu usługi GCM: 501 – 599 lub wynik: niedostępne).|Nie wymiarów|
|outgoing.gcm.invalidnotificationformat|Usługa GCM nieprawidłowy Format powiadomienia|Count|Łącznie|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ ładunek został nieprawidłowo sformatowany (wynik usługi GCM: InvalidDataKey lub InvalidTtl).|Nie wymiarów|
|outgoing.gcm.invalidnotificationsize|Usługa GCM błąd nieprawidłowego rozmiaru powiadomienia|Count|Łącznie|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ ładunek powiadomienia był zbyt duży (wynik usługi GCM: MessageTooBig).|Nie wymiarów|
|outgoing.gcm.wrongchannel|Błąd nieprawidłowego kanału usługi GCM|Count|Łącznie|Liczba wypchnięć, które nie powiodło się, ponieważ identyfikator rejestracji nie jest skojarzony z bieżącą aplikacją (wynik usługi GCM: InvalidPackageName).|Nie wymiarów|
|outgoing.gcm.pnserror|Błędy usługi GCM|Count|Łącznie|Liczba wypchnięć, które zakończyły się niepowodzeniem z powodu błędów podczas komunikowania się z usługą GCM.|Nie wymiarów|
|outgoing.gcm.authenticationerror|Błędy uwierzytelniania usługi GCM|Count|Łącznie|Liczba wypchnięć, które nie powiodło się, ponieważ system powiadomień platformy nie zaakceptował podanych poświadczeń poświadczenia zostały zablokowane lub element SenderId nie jest poprawnie skonfigurowana w aplikacji (wynik usługi GCM: MismatchedSenderId).|Nie wymiarów|
|outgoing.mpns.success|Powiadomienia zakończone powodzeniem usługi MPNS|Count|Łącznie|Liczba wszystkich powiadomień zakończonych powodzeniem.|Nie wymiarów|
|outgoing.mpns.invalidcredentials|Nieprawidłowe poświadczenia usługi MPNS|Count|Łącznie|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ system powiadomień platformy nie zaakceptował podanych poświadczeń lub poświadczenia zostały zablokowane.|Nie wymiarów|
|outgoing.mpns.badchannel|MPNS Bad Channel Error|Count|Łącznie|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ identyfikator ChannelURI w rejestracji nie został rozpoznany (stan usługi MPNS: 404 Nie znaleziono).|Nie wymiarów|
|Outgoing.mpns.throttled|Powiadomienia usługi MPNS z ograniczoną przepływnością|Count|Łącznie|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ usługi MPNS ogranicza tę aplikację (WNS MPNS: 406 nie do przyjęcia).|Nie wymiarów|
|outgoing.mpns.invalidnotificationformat|Usługi MPNS nieprawidłowy Format powiadomienia|Count|Łącznie|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ ładunek powiadomienia był zbyt duży.|Nie wymiarów|
|outgoing.mpns.channeldisconnected|MPNS Channel Disconnected|Count|Łącznie|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ identyfikator ChannelURI w rejestracji został rozłączony (stan usługi MPNS: 412 nie znaleziono).|Nie wymiarów|
|outgoing.mpns.dropped|Porzucone powiadomienia usługi MPNS|Count|Łącznie|Liczba wypchnięć, które zostały porzucone przez usługę MPNS (nagłówek odpowiedzi MPNS: X-NotificationStatus: QueueFull lub pominięty).|Nie wymiarów|
|outgoing.mpns.pnserror|Błędy usługi MPNS|Count|Łącznie|Liczba wypchnięć, które zakończyły się niepowodzeniem z powodu błędów podczas komunikowania się z usługą MPNS.|Nie wymiarów|
|outgoing.mpns.authenticationerror|Błędy uwierzytelniania usługi MPNS|Count|Łącznie|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ system powiadomień platformy nie zaakceptował podanych poświadczeń lub poświadczenia zostały zablokowane.|Nie wymiarów|
|notificationhub.pushes|Wszystkie powiadomienia wychodzące|Count|Łącznie|Wszystkie powiadomienia wychodzące w Centrum powiadomień|Nie wymiarów|
|incoming.all.requests|Wszystkie żądania przychodzące|Count|Łącznie|Łączna liczba żądań przychodzących dla Centrum powiadomień|Nie wymiarów|
|incoming.all.failedrequests|Wszystkie nieudane żądania przychodzące|Count|Łącznie|Łączna liczba nieudanych żądań przychodzących dla Centrum powiadomień|Nie wymiarów|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Average_ % wolnych węzłów i|% Wolnych węzłów i|Count|Average|Average_ % wolnych węzłów i|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % wolnego miejsca|% Wolnego miejsca|Count|Average|Average_ % wolnego miejsca|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % użytych węzłów i|% Użytych węzłów i|Count|Average|Average_ % użytych węzłów i|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Używany obszar Average_ %|Procent wykorzystania miejsca|Count|Average|Używany obszar Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Odczytane bajty/s|Bajty odczytu z dysku/s|Count|Average|Average_Disk Odczytane bajty/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk odczyty/s|Odczyty dysku/s|Count|Average|Average_Disk odczyty/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk dyskowe/s|Transfery dyskowe/s|Count|Average|Average_Disk dyskowe/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk zapisane bajty/s|Bajty zapisu na dysku/s|Count|Average|Average_Disk zapisane bajty/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk zapisy/s|Zapisy dysku/s|Count|Average|Average_Disk zapisy/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free (MB)|Wolne megabajty|Count|Average|Average_Free (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Bajty dysku/s|Bajty dysku logicznego/s|Count|Average|Average_Logical Bajty dysku/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % dostępnej pamięci|% Dostępnej pamięci.|Count|Average|Average_ % dostępnej pamięci|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % dostępnego obszaru wymiany|% Dostępnego obszaru wymiany|Count|Average|Average_ % dostępnego obszaru wymiany|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ procent wykorzystania pamięci|Procent wykorzystania pamięci|Count|Average|Average_ procent wykorzystania pamięci|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Używany obszar wymiany Average_ %|Używany obszar wymiany %|Count|Average|Używany obszar wymiany Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pamięć (MB) Average_Available|Dostępna pamięć (MB)|Count|Average|Pamięć (MB) Average_Available|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available obszar wymiany|Dostępny obszar wymiany|Count|Average|Average_Available obszar wymiany|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page odczyty/s|Odczyty stron/s|Count|Average|Average_Page odczyty/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page zapisy/s|Zapisy stron/s|Count|Average|Average_Page zapisy/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages na sekundę|Strony/s|Count|Average|Average_Pages na sekundę|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Obszar wymiany (MB) Average_Used|Miejsce używany obszar wymiany (MB)|Count|Average|Obszar wymiany (MB) Average_Used|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used pamięć (MB)|Używana pamięć (MB)|Count|Average|Average_Used pamięć (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bajty przesłane|Całkowita liczba przesłanych bajtów|Count|Average|Average_Total Bajty przesłane|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odebrane bajty Average_Total|Całkowita liczba odebranych bajtów|Count|Average|Odebrane bajty Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total bajtów|Całkowita liczba bajtów|Count|Average|Average_Total bajtów|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total pakiety przesyłane|Wszystkie pakiety przesyłane|Count|Average|Average_Total pakiety przesyłane|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odebrane pakiety Average_Total|Łączna liczba odebranych pakietów|Count|Average|Odebrane pakiety Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Błędy Rx Average_Total|Rx łączna liczba błędów|Count|Average|Błędy Rx Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Błędy Tx Average_Total|Tx łączna liczba błędów|Count|Average|Błędy Tx Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total kolizji|Łączna liczba konfliktów|Count|Average|Average_Total kolizji|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Czas dysku w s/Odczyt|Średni Czas dysku w s/Odczyt|Count|Average|Average_Avg. Czas dysku w s/Odczyt|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Dysku w s/Transfer|Średni Dysku w s/Transfer|Count|Average|Average_Avg. Dysku w s/Transfer|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Dysku w s/Zapis|Średni Dysku w s/Zapis|Count|Average|Average_Avg. Dysku w s/Zapis|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Bajty dysku/s|Bajty dysku fizycznego/s|Count|Average|Average_Physical Bajty dysku/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas uprzywilejowany Average_Pct|Czas uprzywilejowany PCT|Count|Average|Czas uprzywilejowany Average_Pct|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas użytkownika Average_Pct|Czas użytkownika protokołu PCT|Count|Average|Czas użytkownika Average_Pct|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|KB pamięci Average_Used|Używana pamięć (KB pamięci)|Count|Average|KB pamięci Average_Used|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pamięć współużytkowaną Average_Virtual|Wirtualnej pamięci wspólnej|Count|Average|Pamięć współużytkowaną Average_Virtual|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ czas DPC (%)|Czas DPC (%)|Count|Average|Average_ czas DPC (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas bezczynności (%) Average_|Czas bezczynności (%)|Count|Average|Czas bezczynności (%) Average_|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ czas przerwań (%)|Czas przerwań (%)|Count|Average|Average_ czas przerwań (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas oczekiwania operacji We/Wy dla % Average_|Czas oczekiwania operacji We/Wy %|Count|Average|Czas oczekiwania operacji We/Wy dla % Average_|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas nieuprzywilejowany (%) Average_|Czas nieuprzywilejowany (%)|Count|Average|Czas nieuprzywilejowany (%) Average_|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas uprzywilejowany Average_ %|Czas uprzywilejowany %|Count|Average|Czas uprzywilejowany Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ czas procesora (%)|Czas procesora (%)|Count|Average|Average_ czas procesora (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas użytkownika % Average_|Czas użytkownika (%)|Count|Average|Czas użytkownika % Average_|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pamięć fizyczna Average_Free|Wolna pamięć fizyczna|Count|Average|Pamięć fizyczna Average_Free|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free miejsce w plikach stronicowania|Wolne miejsce w plikach stronicowania|Count|Average|Average_Free miejsce w plikach stronicowania|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free pamięci wirtualnej|Wolna pamięć wirtualna|Count|Average|Average_Free pamięci wirtualnej|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Procesy|Count|Average|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size przechowywane w plikach stronicowania|Rozmiar zapisanych w plikach stronicowania|Count|Average|Average_Size przechowywane w plikach stronicowania|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Czas pracy|Count|Average|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Użytkownicy|Count|Average|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Czas dysku w s/Odczyt|Średni Czas dysku w s/Odczyt|Count|Average|Average_Avg. Czas dysku w s/Odczyt|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Dysku w s/Zapis|Średni Dysku w s/Zapis|Count|Average|Average_Avg. Dysku w s/Zapis|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Długość kolejki dysku Average_Current|Bieżąca długość kolejki dysku|Count|Average|Długość kolejki dysku Average_Current|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk odczyty/s|Odczyty dysku/s|Count|Average|Average_Disk odczyty/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk dyskowe/s|Transfery dyskowe/s|Count|Average|Average_Disk dyskowe/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk zapisy/s|Zapisy dysku/s|Count|Average|Average_Disk zapisy/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free (MB)|Wolne megabajty|Count|Average|Average_Free (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % wolnego miejsca|% Wolnego miejsca|Count|Average|Average_ % wolnego miejsca|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available pamięć (MB)|Dostępna pamięć (MB)|Count|Average|Average_Available pamięć (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % Zadeklarowane bajty w użyciu|% Przydzielonych bajtów w użyciu|Count|Average|Average_ % Zadeklarowane bajty w użyciu|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes odebrane/s|Bajty odebrane/s|Count|Average|Average_Bytes odebrane/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes wysłane/s|Bajty wysłane/s|Count|Average|Average_Bytes wysłane/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes liczba bajtów/s|Całkowita liczba bajtów/s|Count|Average|Average_Bytes liczba bajtów/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ czas procesora (%)|Czas procesora (%)|Count|Average|Average_ czas procesora (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Długość kolejki Average_Processor|Długość kolejki procesora|Count|Average|Długość kolejki Average_Processor|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Puls|Puls|Count|Łącznie|Puls|Computer, OSType, Version, SourceComputerId|
|Aktualizacja|Aktualizacja|Count|Average|Aktualizacja|Komputer, produktów, klasyfikacji, UpdateState, opcjonalnie, zatwierdzone|
|Wydarzenie|Wydarzenie|Count|Average|Wydarzenie|Źródło dziennika zdarzeń, komputer, EventCategory, EventLevel EventLevelName, identyfikator zdarzenia|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QueryDuration|Czas trwania zapytania|MS|Average|Czas trwania zapytania języka DAX w ostatnim interwale|Nie wymiarów|
|QueryPoolJobQueueLength|Wątki: Długość kolejki zadań puli zapytań|Count|Average|Liczba zadań w kolejce puli wątków zapytania.|Nie wymiarów|
|qpu_high_utilization_metric|Wysokie wykorzystanie jednostek QPU|Count|Łącznie|Wysokie wykorzystanie jednostek QPU w ostatniej chwili 1 QPU wysokie wykorzystanie, w przeciwnym razie 0.|Nie wymiarów|
|memory_metric|Memory (Pamięć)|Bajty|Average|Ilość pamięci. Zakres 0 – 3 GB A1, 0 – 5 GB dla opcji A2, A3 0-10 GB, 0 – 25 GB dla formatu A4, 0 – 50 GB dla A5 i 0 – 100 GB w przypadku A6|Nie wymiarów|
|memory_thrashing_metric|Przeładowywanie pamięci|Percent|Average|Średnie przeładowywanie pamięci.|Nie wymiarów|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Count|Łącznie|Pomyślne połączenia ListenerConnections dla elementu Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Count|Łącznie|Błąd ClientError w połączeniach ListenerConnections dla elementu Microsoft.Relay.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Count|Łącznie|Błąd ServerError w połączeniach ListenerConnections dla elementu Microsoft.Relay.|EntityName|
|SenderConnections-Success|SenderConnections-Success|Count|Łącznie|Pomyślne połączenia SenderConnections dla elementu Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Count|Łącznie|Błąd clienterror w połączeniach SenderConnections dla elementu Microsoft.Relay.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Count|Łącznie|Błąd servererror w elemencie SenderConnections dla elementu Microsoft.Relay.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Count|Łącznie|Łączna liczba połączeń ListenerConnections dla elementu Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Count|Łącznie|Łączna liczba żądań SenderConnections dla elementu Microsoft.Relay.|EntityName|
|Połączeń ActiveConnections|Połączeń ActiveConnections|Count|Łącznie|Łączna liczba połączeń ActiveConnections dla elementu Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Count|Łącznie|Łączna liczba ActiveListeners dla elementu Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Count|Łącznie|Łączna liczba bajtów BytesTransferred dla elementu Microsoft.Relay.|EntityName|
|Rozłączeń ListenerDisconnects|Rozłączeń ListenerDisconnects|Count|Łącznie|Łączna liczba rozłączeń ListenerDisconnects dla elementu Microsoft.Relay.|EntityName|
|Rozłączeń SenderDisconnects|Rozłączeń SenderDisconnects|Count|Łącznie|Łączna liczba rozłączeń SenderDisconnects dla elementu Microsoft.Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SearchLatency|Opóźnienie wyszukiwania|Sekundy|Average|Opóźnienie wyszukiwania średnia dla usługi wyszukiwania|Nie wymiarów|
|SearchQueriesPerSecond|Zapytania wyszukiwania na sekundę|CountPerSecond|Average|Zapytania wyszukiwania na sekundę usługi wyszukiwania|Nie wymiarów|
|ThrottledSearchQueriesPercentage|Procent zapytań wyszukiwania z ograniczoną przepustowością|Percent|Average|Procent zapytań wyszukiwania, które były ograniczone do usługi wyszukiwania|Nie wymiarów|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SuccessfulRequests|Liczba pomyślnych żądań (wersja zapoznawcza)|Count|Łącznie|Łączna liczba udanych żądań dla przestrzeni nazw (wersja zapoznawcza)|EntityName|
|ServerErrors|Błędy serwera. (Wersja zapoznawcza)|Count|Łącznie|Błędy serwera dla elementu Microsoft.ServiceBus. (Wersja zapoznawcza)|EntityName|
|UserErrors|Błędy użytkowników. (Wersja zapoznawcza)|Count|Łącznie|Błędy użytkowników dla elementu Microsoft.ServiceBus. (Wersja zapoznawcza)|EntityName|
|ThrottledRequests|Żądania ograniczone. (Wersja zapoznawcza)|Count|Łącznie|Żądania ograniczone dla elementu Microsoft.ServiceBus. (Wersja zapoznawcza)|EntityName|
|IncomingRequests|Przychodzące żądania (wersja zapoznawcza)|Count|Łącznie|Żądania przychodzące dla elementu Microsoft.ServiceBus. (Wersja zapoznawcza)|EntityName|
|IncomingMessages|Komunikaty przychodzące (wersja zapoznawcza)|Count|Łącznie|Komunikaty przychodzące dla elementu Microsoft.ServiceBus. (Wersja zapoznawcza)|EntityName|
|OutgoingMessages|Komunikaty wychodzące (wersja zapoznawcza)|Count|Łącznie|Komunikaty wychodzące dla elementu Microsoft.ServiceBus. (Wersja zapoznawcza)|EntityName|
|Połączeń ActiveConnections|Połączeń ActiveConnections (wersja zapoznawcza)|Count|Łącznie|Całkowita liczba aktywnych połączeń dla elementu Microsoft.ServiceBus. (Wersja zapoznawcza)|Nie wymiarów|
|Size|Rozmiar (wersja zapoznawcza)|Bajty|Average|Rozmiar kolejki/tematu w bajtach. (Wersja zapoznawcza)|EntityName|
|Komunikaty|Liczba komunikatów w kolejce/temacie. (Wersja zapoznawcza)|Count|Average|Liczba komunikatów w kolejce/temacie. (Wersja zapoznawcza)|EntityName|
|ActiveMessages|Liczba aktywnych komunikatów w kolejce/temacie. (Wersja zapoznawcza)|Count|Average|Liczba aktywnych komunikatów w kolejce/temacie. (Wersja zapoznawcza)|EntityName|
|DeadletteredMessages|Liczba lettered Obsługa utraconych komunikatów w kolejce/temacie. (Wersja zapoznawcza)|Count|Average|Liczba lettered Obsługa utraconych komunikatów w kolejce/temacie. (Wersja zapoznawcza)|EntityName|
|ScheduledMessages|Liczba zaplanowanych wiadomości w kolejce/temacie. (Wersja zapoznawcza)|Count|Average|Liczba zaplanowanych wiadomości w kolejce/temacie. (Wersja zapoznawcza)|EntityName|
|CPUXNS|Użycie Procesora na przestrzeń nazw|Percent|Maksimum|Metryka użycia Procesora przestrzeni nazw usługi Service bus premium|Nie wymiarów|
|WSXNS|Użycie rozmiaru pamięci na przestrzeń nazw|Percent|Maksimum|Metryka użycia pamięci przestrzeni nazw usługi Service bus premium|Nie wymiarów|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Count|Average|Procesor CPU przydzielonych do tego kontenera w millicores|ApplicationName, ServiceReplicaName ServiceName, CodePackageName,|
|AllocatedMemory|AllocatedMemory|Bajty|Average|Pamięć przydzielona do tego kontenera w MB|ApplicationName, ServiceReplicaName ServiceName, CodePackageName,|
|ActualCpu|ActualCpu|Count|Average|Rzeczywiste użycie procesora CPU w millicores|ApplicationName, ServiceReplicaName ServiceName, CodePackageName,|
|ActualMemory|ActualMemory|Bajty|Average|Użycie pamięci Rzeczywiste w MB|ApplicationName, ServiceReplicaName ServiceName, CodePackageName,|
|CpuUtilization|CpuUtilization|Percent|Average|Wykorzystanie procesora CPU dla tego kontenera jako procent AllocatedCpu|ApplicationName, ServiceReplicaName ServiceName, CodePackageName,|
|MemoryUtilization|MemoryUtilization|Percent|Average|Wykorzystanie procesora CPU dla tego kontenera jako procent AllocatedCpu|ApplicationName, ServiceReplicaName ServiceName, CodePackageName,|
|ApplicationStatus|ApplicationStatus|Count|Average|Stan aplikacji usługi Service Fabric siatki|ApplicationName, stan|
|Bajty|Bajty|Count|Average|Kondycja usługi w aplikacji usługi Service Fabric siatki|ServiceName ApplicationName, stan,|
|ServiceReplicaStatus|ServiceReplicaStatus|Count|Average|Kondycja usługi repliki bazy danych w aplikacji usługi Service Fabric siatki|ApplicationName, stan, ServiceName, ServiceReplicaName|
|ContainerStatus|ContainerStatus|Count|Average|Stan kontenera w aplikacji usługi Service Fabric siatki|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, stan|
|RestartCount|RestartCount|Count|Average|Uruchom ponownie licznik kontenera, w aplikacji usługi Service Fabric siatki|ApplicationName, stan, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ConnectionCount|Liczba połączeń|Count|Maksimum|Liczba połączeń użytkowników.|Endpoint|
|MessageCount|Liczba komunikatów|Count|Łącznie|Łączna liczba wiadomości.|Nie wymiarów|
|InboundTraffic|Ruch przychodzący|Bajty|Łącznie|Ruch przychodzący usługi|Nie wymiarów|
|OutboundTraffic|Ruch wychodzący|Bajty|Łącznie|Ruch wychodzący z usługi|Nie wymiarów|
|UserErrors|Błędy użytkowników|Percent|Maksimum|Procent błędów użytkownika|Nie wymiarów|
|SystemErrors|Błędy systemu|Percent|Maksimum|Procent błędów systemu|Nie wymiarów|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Percent|Average|Procent użycia procesora CPU|Nie wymiarów|
|physical_data_read_percent|Procent użycia operacji we/wy na danych|Percent|Average|Procent użycia operacji we/wy na danych|Nie wymiarów|
|log_write_percent|Wartość procentowa we/wy dziennika|Percent|Average|Wartość procentowa we/wy dziennika|Nie wymiarów|
|dtu_consumption_percent|Procent użycia jednostek DTU|Percent|Average|Procent użycia jednostek DTU|Nie wymiarów|
|magazyn|Dane miejsca|Bajty|Maksimum|Łączny rozmiar bazy danych|Nie wymiarów|
|connection_successful|Pomyślnie nawiązane połączenia|Count|Łącznie|Pomyślnie nawiązane połączenia|Nie wymiarów|
|connection_failed|Połączenia zakończone niepowodzeniem|Count|Łącznie|Połączenia zakończone niepowodzeniem|Nie wymiarów|
|blocked_by_firewall|Blokowane przez zaporę|Count|Łącznie|Blokowane przez zaporę|Nie wymiarów|
|Zakleszczenia|Zakleszczenia|Count|Łącznie|Zakleszczenia|Nie wymiarów|
|storage_percent|Procent miejsca danych|Percent|Maksimum|Procent użycia rozmiaru bazy danych|Nie wymiarów|
|xtp_storage_percent|Procent magazynu OLTP w pamięci|Percent|Average|Procent magazynu OLTP w pamięci|Nie wymiarów|
|workers_percent|Procent pracowników|Percent|Average|Procent pracowników|Nie wymiarów|
|sessions_percent|Procent sesji|Percent|Average|Procent sesji|Nie wymiarów|
|dtu_limit|DTU Limit|Count|Average|DTU Limit|Nie wymiarów|
|dtu_used|Używane jednostki DTU|Count|Average|Używane jednostki DTU|Nie wymiarów|
|cpu_limit|Limitu procesora CPU|Count|Average|Limitu procesora CPU|Nie wymiarów|
|cpu_used|Użycie Procesora|Count|Average|Użycie Procesora|Nie wymiarów|
|dwu_limit|Limit jednostek DWU|Count|Maksimum|Limit jednostek DWU|Nie wymiarów|
|dwu_consumption_percent|Procent jednostek DWU|Percent|Maksimum|Procent jednostek DWU|Nie wymiarów|
|dwu_used|Używane jednostki DWU|Count|Maksimum|Używane jednostki DWU|Nie wymiarów|
|dw_cpu_percent|DW poziomu węzła procent użycia procesora CPU|Percent|Average|DW poziomu węzła procent użycia procesora CPU|DwLogicalNodeId|
|dw_physical_data_read_percent|Procentowy poziom we/wy danych węzła magazynu danych|Percent|Average|Procentowy poziom we/wy danych węzła magazynu danych|DwLogicalNodeId|
    |cache_hit_percent|Procent liczby trafień pamięci podręcznej|Percent|Maksimum|Procent liczby trafień pamięci podręcznej|Nie wymiarów|
|cache_used_percent|Pamięć podręczna używana wartość procentowa|Percent|Maksimum|Pamięć podręczna używana wartość procentowa|Nie wymiarów|
|local_tempdb_usage_percent|Wartość procentowa lokalnej bazy danych tempdb|Percent|Average|Wartość procentowa lokalnej bazy danych tempdb|Nie wymiarów|
|app_cpu_billed|Aplikacja rozliczane procesora CPU|Count|Łącznie|Aplikacja rozliczane procesora CPU|Nie wymiarów|
|app_cpu_percent|Procent użycia Procesora aplikacji|Percent|Average|Procent użycia Procesora aplikacji|Nie wymiarów|
|app_memory_percent|Procent używanej pamięci w aplikacji|Percent|Average|Procent używanej pamięci w aplikacji|Nie wymiarów|
|allocated_data_storage|Przydzielone miejsce danych|Bajty|Average|Przydzielone miejsce danych|Nie wymiarów|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Percent|Average|Procent użycia procesora CPU|Nie wymiarów|
|physical_data_read_percent|Procent użycia operacji we/wy na danych|Percent|Average|Procent użycia operacji we/wy na danych|Nie wymiarów|
|log_write_percent|Wartość procentowa we/wy dziennika|Percent|Average|Wartość procentowa we/wy dziennika|Nie wymiarów|
|dtu_consumption_percent|Procent użycia jednostek DTU|Percent|Average|Procent użycia jednostek DTU|Nie wymiarów|
|storage_percent|Procent miejsca danych||Percent|Average|Procent użycia magazynu|Nie wymiarów|
|workers_percent|Procent pracowników|Percent|Average|Procent pracowników|Nie wymiarów|
|sessions_percent|Procent sesji|Percent|Average|Procent sesji|Nie wymiarów|
|eDTU_limit|eDTU limit|Count|Average|eDTU limit|Nie wymiarów|
|storage_limit|Maksymalny rozmiar danych|Bajty|Average|Limit magazynu|Nie wymiarów|
|eDTU_used|używane jednostki eDTU|Count|Average|używane jednostki eDTU|Nie wymiarów|
|storage_used|Dane miejsca|Bajty|Average|Użyty magazyn|Nie wymiarów|
|xtp_storage_percent|Procent magazynu OLTP w pamięci|Percent|Average|Procent magazynu OLTP w pamięci|Nie wymiarów|
|cpu_limit|Limitu procesora CPU|Count|Average|Limitu procesora CPU|Nie wymiarów|
|cpu_used|Użycie Procesora|Count|Average|Użycie Procesora|Nie wymiarów|
|allocated_data_storage|Przydzielone miejsce danych|Bajty|Average|Przydzielone miejsce danych|Nie wymiarów|
|allocated_data_storage_percent|Procent miejsce przydzielone dane|Percent|Maksimum|Procent miejsce przydzielone dane|Nie wymiarów|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|virtual_core_count|Liczba rdzeni wirtualnych|Count|Average|Liczba rdzeni wirtualnych|Nie wymiarów|
|avg_cpu_percent|Średni procent użycia procesora CPU|Percent|Average|Średni procent użycia procesora CPU|Nie wymiarów|
|reserved_storage_mb|Zarezerwowane miejsce do magazynowania|Count|Average|Zarezerwowane miejsce do magazynowania|Nie wymiarów|
|storage_space_used_mb|Używane miejsce w magazynie|Count|Average|Używane miejsce w magazynie|Nie wymiarów|
|io_requests|Liczba żądań We/Wy|Count|Average|Liczba żądań We/Wy|Nie wymiarów|
|io_bytes_read|Bajty We/Wy odczytu|Bajty|Average|Bajty We/Wy odczytu|Nie wymiarów|
|io_bytes_written|Zapisano bajtów we/wy|Bajty|Average|Zapisano bajtów we/wy|Nie wymiarów|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|UsedCapacity|Używana pojemność|Bajty|Średnia|Używana pojemność konta|Nie wymiarów|
|Transakcje|Transakcje|Licznik|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje udane i nieudane żądania, a także żądania, które wygenerowany błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi.|Wartość ResponseType, GeoType, ApiName, uwierzytelniania|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Uwierzytelnianie GeoType, ApiName,|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Uwierzytelnianie GeoType, ApiName,|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|MS|Średnia|Średni czas oczekiwania, używane przez usługę Azure Storage do przetwarzania żądania zakończonego powodzeniem w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego w elemencie AverageE2ELatency.|Uwierzytelnianie GeoType, ApiName,|
|SuccessE2ELatency|Opóźnienie e2e dla powodzenia|MS|Średnia|Średnie opóźnienie end-to-end żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Uwierzytelnianie GeoType, ApiName,|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests i podzielenie go przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy skutkują od ograniczoną dostępnością usługi magazynu lub określonej operacji interfejsu API.|Uwierzytelnianie GeoType, ApiName,|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BlobCapacity|Pojemność obiektu blob|Bajty|Average|Ilość miejsca używanego przez usługę obiektów Blob konta magazynu, w bajtach.|BlobType, warstwa|
|BlobCount|Liczba obiektów blob|Licznik|Łącznie|Liczba obiektów Blob w usłudze obiektów Blob konta magazynu.|BlobType|       |BlobCount|Liczba obiektów blob|Count|Average|Liczba obiektów Blob w usłudze obiektów Blob konta magazynu.|BlobType, warstwa|
|ContainerCount|Liczba kontenerów obiektów blob|Licznik|Średnia|Liczba kontenerów w usłudze obiektów Blob konta magazynu.|Nie wymiarów|
|IndexCapacity|Pojemność indeksu|Bajty|Average|Ilość miejsca używanego przez usługi ADLS Gen2 indeksu (hierarchiczne) w bajtach.|Nie wymiarów|
|Transakcje|Transakcje|Licznik|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje udane i nieudane żądania, a także żądania, które wygenerowany błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi.|Wartość ResponseType, GeoType, ApiName, uwierzytelniania|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Uwierzytelnianie GeoType, ApiName,|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Uwierzytelnianie GeoType, ApiName,|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|MS|Średnia|Średni czas oczekiwania, używane przez usługę Azure Storage do przetwarzania żądania zakończonego powodzeniem w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego w elemencie AverageE2ELatency.|Uwierzytelnianie GeoType, ApiName,|
|SuccessE2ELatency|Opóźnienie e2e dla powodzenia|MS|Średnia|Średnie opóźnienie end-to-end żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Uwierzytelnianie GeoType, ApiName,|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests i podzielenie go przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy skutkują od ograniczoną dostępnością usługi magazynu lub określonej operacji interfejsu API.|Uwierzytelnianie GeoType, ApiName,|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|FileCapacity|Pojemność plików|Bajty|Average|Ilość miejsca używanego przez usługę plików konta magazynu, w bajtach.|Nie wymiarów|
|FileCount|Liczba plików|Count|Average|Liczba plików w usłudze plików konta magazynu.|Nie wymiarów|
|FileShareCount|Liczba udziału plików|Count|Average|Liczba udziałów plików w usłudze plików konta magazynu.|Nie wymiarów|
|Transakcje|Transakcje|Licznik|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje udane i nieudane żądania, a także żądania, które wygenerowany błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi.|Wartość ResponseType, GeoType, ApiName, uwierzytelniania|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Uwierzytelnianie GeoType, ApiName,|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Uwierzytelnianie GeoType, ApiName,|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|MS|Średnia|Średni czas oczekiwania, używane przez usługę Azure Storage do przetwarzania żądania zakończonego powodzeniem w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego w elemencie AverageE2ELatency.|Uwierzytelnianie GeoType, ApiName,|
|SuccessE2ELatency|Opóźnienie e2e dla powodzenia|MS|Średnia|Średnie opóźnienie end-to-end żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Uwierzytelnianie GeoType, ApiName,|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests i podzielenie go przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy skutkują od ograniczoną dostępnością usługi magazynu lub określonej operacji interfejsu API.|Uwierzytelnianie GeoType, ApiName,|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QueueCapacity|Pojemność kolejki|Bajty|Średnia|Ilość miejsca używanego przez usługę kolejki konta magazynu, w bajtach.|Nie wymiarów|
|QueueCount|Liczba kolejek|Licznik|Średnia|Liczba kolejek w usłudze kolejki konta magazynu.|Nie wymiarów|
|QueueMessageCount|Liczba komunikatów w kolejce|Licznik|Średnia|Przybliżona liczba komunikatów w kolejce w usłudze kolejki konta magazynu.|Nie wymiarów|
|Transakcje|Transakcje|Licznik|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje udane i nieudane żądania, a także żądania, które wygenerowany błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi.|Wartość ResponseType, GeoType, ApiName, uwierzytelniania|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Uwierzytelnianie GeoType, ApiName,|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Uwierzytelnianie GeoType, ApiName,|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|MS|Średnia|Średni czas oczekiwania, używane przez usługę Azure Storage do przetwarzania żądania zakończonego powodzeniem w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego w elemencie AverageE2ELatency.|Uwierzytelnianie GeoType, ApiName,|
|SuccessE2ELatency|Opóźnienie e2e dla powodzenia|MS|Średnia|Średnie opóźnienie end-to-end żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Uwierzytelnianie GeoType, ApiName,|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests i podzielenie go przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy skutkują od ograniczoną dostępnością usługi magazynu lub określonej operacji interfejsu API.|Uwierzytelnianie GeoType, ApiName,|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TableCapacity|Pojemność tabeli|Bajty|Średnia|Ilość miejsca używanego przez usługę tabeli konta magazynu, w bajtach.|Nie wymiarów|
|TableCount|Liczba tabel|Licznik|Średnia|Liczba tabel w usłudze tabeli konta magazynu.|Nie wymiarów|
|TableEntityCount|Liczba jednostek tabel|Licznik|Średnia|Liczba jednostek tabel w usłudze tabeli konta magazynu.|Nie wymiarów|
|Transakcje|Transakcje|Licznik|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje udane i nieudane żądania, a także żądania, które wygenerowany błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi.|Wartość ResponseType, GeoType, ApiName, uwierzytelniania|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Uwierzytelnianie GeoType, ApiName,|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Uwierzytelnianie GeoType, ApiName,|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|MS|Średnia|Średni czas oczekiwania, używane przez usługę Azure Storage do przetwarzania żądania zakończonego powodzeniem w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego w elemencie AverageE2ELatency.|Uwierzytelnianie GeoType, ApiName,|
|SuccessE2ELatency|Opóźnienie e2e dla powodzenia|MS|Średnia|Średnie opóźnienie end-to-end żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Uwierzytelnianie GeoType, ApiName,|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests i podzielenie go przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy skutkują od ograniczoną dostępnością usługi magazynu lub określonej operacji interfejsu API.|Uwierzytelnianie GeoType, ApiName,|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Wynik sesji synchronizacji|Count|Average|Metryka dzienniki, a wartość 1 każdego pomyślnie czas punktu końcowego serwera kończy sesję synchronizacji, korzystając z punktu końcowego w chmurze|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Pliki synchronizowane|Count|Łącznie|Liczba plików synchronizowane|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Nie synchronizuje pliki|Count|Łącznie|Liczba plików synchronizowanie nie powiodło się|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Bajty synchronizowane|Bajty|Łącznie|Całkowity rozmiar plików przesłanych dla sesji synchronizacji|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Stan Online Server|Count|Maksimum|Metryka dzienniki, a wartość 1 każdego pomyślnie czasu zarejestrowanego serwera rekordy pulsu z punktu końcowego w chmurze|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Wycofanie obsługi warstw w chmurze|Bajty|Łącznie|Całkowity rozmiar danych przypomnieć przez serwer|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ResourceUtilization|% wykorzystania SU|Percent|Maksimum|% wykorzystania SU|LogicalName, PartitionId|
|Liczba|Zdarzenia wejściowe|Count|Łącznie|Zdarzenia wejściowe|LogicalName, PartitionId|
|InputEventBytes|Zdarzenia wejściowe (bajty)|Bajty|Łącznie|Zdarzenia wejściowe (bajty)|LogicalName, PartitionId|
|LateInputEvents|Opóźnione zdarzenia wejściowe|Count|Łącznie|Opóźnione zdarzenia wejściowe|LogicalName, PartitionId|
|Liczba zdarzeń wyjściowych|Zdarzenia wyjściowe|Count|Łącznie|Zdarzenia wyjściowe|LogicalName, PartitionId|
|ConversionErrors|Błędy konwersji danych|Count|Łącznie|Błędy konwersji danych|LogicalName, PartitionId|
|Błędy|Błędy w czasie wykonywania|Count|Łącznie|Błędy w czasie wykonywania|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Zdarzenia poza kolejnością|Count|Łącznie|Zdarzenia poza kolejnością|LogicalName, PartitionId|
|AMLCalloutRequests|Żądania funkcji|Count|Łącznie|Żądania funkcji|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Żądania funkcji zakończone niepowodzeniem|Count|Łącznie|Żądania funkcji zakończone niepowodzeniem|LogicalName, PartitionId|
|AMLCalloutInputEvents|Zdarzenia funkcji|Count|Łącznie|Zdarzenia funkcji|LogicalName, PartitionId|
|DeserializationError|Błędy deserializacji danych wejściowych|Count|Łącznie|Błędy deserializacji danych wejściowych|LogicalName, PartitionId|
|EarlyInputEvents|Wczesne zdarzenia wejściowe|Count|Łącznie|Wczesne zdarzenia wejściowe|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Opóźnienie znaku wodnego|Sekundy|Maksimum|Opóźnienie znaku wodnego|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Zaległe zdarzenia wejściowe|Count|Maksimum|Zaległe zdarzenia wejściowe|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Odebrane źródła wejściowe|Count|Łącznie|Odebrane źródła wejściowe|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ruch przychodzący odebranych komunikatów|Count|Łącznie|Liczba komunikatów odczytywać wszystkie Centrum zdarzeń lub usługi IoT hub źródła zdarzeń|Nie wymiarów|
|IngressReceivedInvalidMessages|Ruch przychodzący Odebrano nieprawidłowy komunikaty|Count|Łącznie|Liczba komunikatów nieprawidłowy odczytywać wszystkie Centrum zdarzeń lub usługi IoT hub źródła zdarzeń|Nie wymiarów|
|IngressReceivedBytes|Ruch przychodzący odebrały bajty|Bajty|Łącznie|Liczba bajtów odczytanych ze wszystkich źródeł zdarzeń|Nie wymiarów|
|IngressStoredBytes|Ruch przychodzący przechowywane bajtów|Bajty|Łącznie|Całkowity rozmiar zdarzeń pomyślnie przetworzone i dostępne dla zapytania|Nie wymiarów|
|IngressStoredEvents|Ruch przychodzący przechowywanych zdarzeń|Count|Łącznie|Liczba zdarzeń spłaszczonych pomyślnie przetworzone i dostępne dla zapytania|Nie wymiarów|
|IngressReceivedMessagesTimeLag|Opóźnienie czasowe Odebrane komunikaty przychodzące|Sekundy|Maksimum|Różnica między czasem, który komunikat jest dodawanych do kolejki w źródle zdarzenia i czasu, gdy są przetwarzane w ruchu przychodzącego|Nie wymiarów|
|IngressReceivedMessagesCountLag|Opóźnienie liczba Odebrane komunikaty przychodzące|Count|Average|Różnica między liczba sekwencji ostatniej wiadomości w kolejce w zdarzeniu źródła partycji i numer sekwencji przetwarzanego w ruchu przychodzącego komunikatu|Nie wymiarów|
|WarmStorageMaxProperties|Właściwości Max magazynu bez wyłączania zasilania|Count|Maksimum|Maksymalna liczba właściwości używanych dozwolona przez środowisko dla S1/S2 SKU i maksymalną liczbę dozwoloną przez ciepło Store w ramach jednostki SKU PAYG właściwości|Nie wymiarów|
|WarmStorageUsedProperties|Magazynu ciepłego używanych właściwości |Count|Maksimum|Wiele właściwości, używany przez środowisko dla S1/S2 SKU i liczbę właściwości używanych przez Store ciepło PAYG jednostki SKU|Nie wymiarów|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ruch przychodzący odebranych komunikatów|Count|Łącznie|Liczba komunikatów odczytu ze źródła zdarzeń|Nie wymiarów|
|IngressReceivedInvalidMessages|Ruch przychodzący Odebrano nieprawidłowy komunikaty|Count|Łącznie|Liczba komunikatów Nieprawidłowy odczyt z źródła zdarzeń|Nie wymiarów|
|IngressReceivedBytes|Ruch przychodzący odebrały bajty|Bajty|Łącznie|Liczba bajtów odczytanych ze źródła zdarzeń|Nie wymiarów|
|IngressStoredBytes|Ruch przychodzący przechowywane bajtów|Bajty|Łącznie|Całkowity rozmiar zdarzeń pomyślnie przetworzone i dostępne dla zapytania|Nie wymiarów|
|IngressStoredEvents|Ruch przychodzący przechowywanych zdarzeń|Count|Łącznie|Liczba zdarzeń spłaszczonych pomyślnie przetworzone i dostępne dla zapytania|Nie wymiarów|
|IngressReceivedMessagesTimeLag|Opóźnienie czasowe Odebrane komunikaty przychodzące|Sekundy|Maksimum|Różnica między czasem, który komunikat jest dodawanych do kolejki w źródle zdarzenia i czasu, gdy są przetwarzane w ruchu przychodzącego|Nie wymiarów|
|IngressReceivedMessagesCountLag|Opóźnienie liczba Odebrane komunikaty przychodzące|Count|Average|Różnica między liczba sekwencji ostatniej wiadomości w kolejce w zdarzeniu źródła partycji i numer sekwencji przetwarzanego w ruchu przychodzącego komunikatu|Nie wymiarów|
|WarmStorageMaxProperties|Właściwości Max magazynu bez wyłączania zasilania|Count|Maksimum|Maksymalna liczba właściwości używanych dozwolona przez środowisko dla S1/S2 SKU i maksymalną liczbę dozwoloną przez ciepło Store w ramach jednostki SKU PAYG właściwości|Nie wymiarów|
|WarmStorageUsedProperties|Magazynu ciepłego używanych właściwości |Count|Maksimum|Wiele właściwości, używany przez środowisko dla S1/S2 SKU i liczbę właściwości używanych przez Store ciepło PAYG jednostki SKU|Nie wymiarów|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Bajty odczytu dysku/s|BytesPerSecond|Average|Przepływność dysku ze względu na operacje odczytu w okresie próbki.|Nie wymiarów|
|DiskWriteBytesPerSecond|Bajty zapisu dysku/s|BytesPerSecond|Average|Przepływność dysku ze względu na operacje zapisu w okresie próbki.|Nie wymiarów|
|Bajty odczytane z dysku|Bajty odczytane z dysku|Bajty|Łącznie|Łączna przepływność dysku z powodu operacji odczytu przez okres próbkowania.|Nie wymiarów|
|Bajty zapisane na dysku|Bajty zapisane na dysku|Bajty|Łącznie|Łączna przepływność dysku z powodu operacji zapisu w okresie próbki.|Nie wymiarów|
|DiskReadOperations|Operacje odczytu z dysku|Count|Łącznie|Liczba operacji We/Wy operacji odczytu w poprzednim okresie próbki. Należy pamiętać, że te operacje mogą być zmienną o rozmiarze.|Nie wymiarów|
|DiskWriteOperations|Operacje zapisu na dysku|Count|Łącznie|Liczba operacji We/Wy operacji zapisu są w poprzednim okresie próbki. Należy pamiętać, że te operacje mogą być zmienną o rozmiarze.|Nie wymiarów|
|Dysku, operacje odczytu/s|Dysku, operacje odczytu/s|CountPerSecond|Average|Średnia liczba operacji We/Wy operacji odczytu są w poprzednim okresie próbki. Należy pamiętać, że te operacje mogą być zmienną o rozmiarze.|Nie wymiarów|
|Operacje zapisu dysku/s|Operacje zapisu dysku/s|CountPerSecond|Average|Średnia liczba operacji We/Wy operacji zapisu są w poprzednim okresie próbki. Należy pamiętać, że te operacje mogą być zmienną o rozmiarze.|Nie wymiarów|
|DiskReadLatency|Opóźnienie odczytu z dysku|MS|Average|Łączna liczba opóźnienie odczytu. Suma urządzenia i jądra odczytu opóźnienia.|Nie wymiarów|
|DiskWriteLatency|Opóźnienie zapisu na dysku|MS|Average|Czas oczekiwania, łączna liczba operacji zapisu. Suma urządzenia i jądra opóźnienia zapisu.|Nie wymiarów|
|NetworkInBytesPerSecond|Sieć w bajtach na sekundę|BytesPerSecond|Average|Przepływność sieci średni odebranego ruchu.|Nie wymiarów|
|NetworkOutBytesPerSecond|Sieć się bajty/s|BytesPerSecond|Average|Przepływność sieci średni przesyłane ruchu.|Nie wymiarów|
|Sieć — wejście|Sieć — wejście|Bajty|Łącznie|Przepływność sieci łączna liczba odebranych ruchu.|Nie wymiarów|
|Sieć — wyjście|Sieć — wyjście|Bajty|Łącznie|Przepływność sieci łączna liczba przesłanych ruchu.|Nie wymiarów|
|MemoryUsed|Pamięć użyta|Bajty|Average|Ilość pamięci komputera, który jest używany przez maszynę Wirtualną.|Nie wymiarów|
|MemoryGranted|Udzielone pamięci|Bajty|Average|Ilość pamięci, który został przyznany do maszyny Wirtualnej przez hosta. Nie otrzymuje pamięci do hosta, dopóki nie jest wspomnieliśmy, jeden raz i przyznanych pamięci może być wymieniane lub ballooned natychmiast, jeśli VMkernel wymaga pamięci.|Nie wymiarów|
|MemoryActive|Aktywna w pamięci|Bajty|Average|Ilość pamięci używanej przez maszynę Wirtualną w ciągu ostatnich niewielki przedział czasu. To jest "true" Liczba ilość pamięci maszyny Wirtualnej ma obecnie potrzeb. Dodatkowe, nieużywanej pamięci może być wymieniane lub ballooned bez wpływu na wydajność przez gościa.|Nie wymiarów|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Average|Użycie procesora CPU. Ta wartość jest zgłaszany w 100% reprezentujący wszystkich rdzeni procesora w systemie. Na przykład sposób 2 maszyny Wirtualnej przy użyciu 50% systemu cztery podstawowego całkowicie korzysta z dwóch rdzeni.|Nie wymiarów|
|PercentageCpuReady|Procent użycia Procesora gotowe|MS|Łącznie|Czas gotowości jest czas wydania oczekiwanie na CPU(s) staną się dostępne w ciągu ostatnich interwał aktualizacji.|Nie wymiarów|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuPercentage|Procent użycia procesora CPU|Percent|Average|Procent użycia procesora CPU|Wystąpienie|
|MemoryPercentage|Procent pamięci|Percent|Average|Procent pamięci|Wystąpienie|
|DiskQueueLength|Długość kolejki dysku|Count|Average|Długość kolejki dysku|Wystąpienie|
|HttpQueueLength|Długość kolejki http|Count|Average|Długość kolejki http|Wystąpienie|
|BytesReceived|Dane w|Bajty|Łącznie|Dane w|Wystąpienie|
|BytesSent|Dane wyjściowe|Bajty|Łącznie|Dane wyjściowe|Wystąpienie|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (z wyjątkiem funkcji)

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuTime|Czas procesora CPU|Sekundy|Łącznie|Czas procesora CPU|Wystąpienie|
|Żądania|Żądania|Count|Łącznie|Żądania|Wystąpienie|
|BytesReceived|Dane w|Bajty|Łącznie|Dane w|Wystąpienie|
|BytesSent|Dane wyjściowe|Bajty|Łącznie|Dane wyjściowe|Wystąpienie|
|Http101|HTTP 101|Count|Łącznie|HTTP 101|Wystąpienie|
|Http2xx|Http 2xx|Count|Łącznie|Http 2xx|Wystąpienie|
|Http3xx|Http 3xx|Count|Łącznie|Http 3xx|Wystąpienie|
|Http401|Http 401|Count|Łącznie|Http 401|Wystąpienie|
|Http403|HTTP 403|Count|Łącznie|HTTP 403|Wystąpienie|
|Http404|HTTP 404|Count|Łącznie|HTTP 404|Wystąpienie|
|Http406|HTTP 406|Count|Łącznie|HTTP 406|Wystąpienie|
|Http4xx|Http 4xx|Count|Łącznie|Http 4xx|Wystąpienie|
|Http5xx|Błędy serwera HTTP|Count|Łącznie|Błędy serwera HTTP|Wystąpienie|
|MemoryWorkingSet|Zestaw roboczy pamięci|Bajty|Average|Zestaw roboczy pamięci|Wystąpienie|
|AverageMemoryWorkingSet|Średni zestaw roboczy pamięci|Bajty|Average|Średni zestaw roboczy pamięci|Wystąpienie|
|AverageResponseTime|Średni czas odpowiedzi|Sekundy|Average|Średni czas odpowiedzi|Wystąpienie|
|AppConnections|Połączenia|Count|Average|Połączenia|Wystąpienie|
|Obsługuje|Liczba dojść|Count|Average|Liczba dojść|Wystąpienie|
|Wątki|Liczba wątków|Count|Average|Liczba wątków|Wystąpienie|
|PrivateBytes|Bajty prywatne|Bajty|Average|Bajty prywatne|Wystąpienie|
|IoReadBytesPerSecond|Bajty odczytu We/Wy na sekundę|BytesPerSecond|Łącznie|Bajty odczytu We/Wy na sekundę|Wystąpienie|
|IoWriteBytesPerSecond|We/Wy zapisu bajtów na sekundę|BytesPerSecond|Łącznie|We/Wy zapisu bajtów na sekundę|Wystąpienie|
|IoOtherBytesPerSecond|We/Wy inne bajty na sekundę|BytesPerSecond|Łącznie|We/Wy inne bajty na sekundę|Wystąpienie|
|IoReadOperationsPerSecond|Operacje odczytu We/Wy na sekundę|BytesPerSecond|Łącznie|Operacje odczytu We/Wy na sekundę|Wystąpienie|
|IoWriteOperationsPerSecond|We/Wy zapisu na sekundę|BytesPerSecond|Łącznie|We/Wy zapisu na sekundę|Wystąpienie|
|IoOtherOperationsPerSecond|Operacje We/Wy innych na sekundę|BytesPerSecond|Łącznie|Operacje We/Wy innych na sekundę|Wystąpienie|
|RequestsInApplicationQueue|Żądania w kolejce aplikacji|Count|Average|Żądania w kolejce aplikacji|Wystąpienie|
|CurrentAssemblies|Aktualna liczba zestawów|Count|Average|Aktualna liczba zestawów|Wystąpienie|
|TotalAppDomains|Łączna liczba domen aplikacji|Count|Average|Łączna liczba domen aplikacji|Wystąpienie|
|TotalAppDomainsUnloaded|Łączna liczba domen aplikacji zwolnione|Count|Average|Łączna liczba domen aplikacji zwolnione|Wystąpienie|
|Gen0Collections|Velikost haldy 0 wyrzucania elementów bezużytecznych|Count|Łącznie|Velikost haldy 0 wyrzucania elementów bezużytecznych|Wystąpienie|
|Gen1Collections|Gen 1 wyrzucania elementów bezużytecznych|Count|Łącznie|Gen 1 wyrzucania elementów bezużytecznych|Wystąpienie|
|Gen2Collections|Odzyskiwanie obiektów pokolenia 2|Count|Łącznie|Odzyskiwanie obiektów pokolenia 2|Wystąpienie|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funkcje)

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BytesReceived|Dane w|Bajty|Łącznie|Dane w|Wystąpienie|
|BytesSent|Dane wyjściowe|Bajty|Łącznie|Dane wyjściowe|Wystąpienie|
|Http5xx|Błędy serwera HTTP|Count|Łącznie|Błędy serwera HTTP|Wystąpienie|
|MemoryWorkingSet|Zestaw roboczy pamięci|Bajty|Average|Zestaw roboczy pamięci|Wystąpienie|
|AverageMemoryWorkingSet|Średni zestaw roboczy pamięci|Bajty|Average|Średni zestaw roboczy pamięci|Wystąpienie|
|FunctionExecutionUnits|Jednostki wykonawcze funkcji|MB / MS|Łącznie|[Jednostki wykonawcze funkcji](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Wystąpienie|
|FunctionExecutionCount|Liczba wykonań funkcji|Count|Łącznie|Liczba wykonań funkcji|Wystąpienie|
|PrivateBytes|Bajty prywatne|Bajty|Average|Bajty prywatne|Wystąpienie|
|IoReadBytesPerSecond|Bajty odczytu We/Wy na sekundę|BytesPerSecond|Łącznie|Bajty odczytu We/Wy na sekundę|Wystąpienie|
|IoWriteBytesPerSecond|We/Wy zapisu bajtów na sekundę|BytesPerSecond|Łącznie|We/Wy zapisu bajtów na sekundę|Wystąpienie|
|IoOtherBytesPerSecond|We/Wy inne bajty na sekundę|BytesPerSecond|Łącznie|We/Wy inne bajty na sekundę|Wystąpienie|
|IoReadOperationsPerSecond|Operacje odczytu We/Wy na sekundę|BytesPerSecond|Łącznie|Operacje odczytu We/Wy na sekundę|Wystąpienie|
|IoWriteOperationsPerSecond|We/Wy zapisu na sekundę|BytesPerSecond|Łącznie|We/Wy zapisu na sekundę|Wystąpienie|
|IoOtherOperationsPerSecond|Operacje We/Wy innych na sekundę|BytesPerSecond|Łącznie|Operacje We/Wy innych na sekundę|Wystąpienie|
|RequestsInApplicationQueue|Żądania w kolejce aplikacji|Count|Average|Żądania w kolejce aplikacji|Wystąpienie|
|CurrentAssemblies|Aktualna liczba zestawów|Count|Average|Aktualna liczba zestawów|Wystąpienie|
|TotalAppDomains|Łączna liczba domen aplikacji|Count|Average|Łączna liczba domen aplikacji|Wystąpienie|
|TotalAppDomainsUnloaded|Łączna liczba domen aplikacji zwolnione|Count|Average|Łączna liczba domen aplikacji zwolnione|Wystąpienie|
|Gen0Collections|Velikost haldy 0 wyrzucania elementów bezużytecznych|Count|Łącznie|Velikost haldy 0 wyrzucania elementów bezużytecznych|Wystąpienie|
|Gen1Collections|Gen 1 wyrzucania elementów bezużytecznych|Count|Łącznie|Gen 1 wyrzucania elementów bezużytecznych|Wystąpienie|
|Gen2Collections|Odzyskiwanie obiektów pokolenia 2|Count|Łącznie|Odzyskiwanie obiektów pokolenia 2|Wystąpienie|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuTime|Czas procesora CPU|Sekundy|Łącznie|Czas procesora CPU|Wystąpienie|
|Żądania|Żądania|Count|Łącznie|Żądania|Wystąpienie|
|BytesReceived|Dane w|Bajty|Łącznie|Dane w|Wystąpienie|
|BytesSent|Dane wyjściowe|Bajty|Łącznie|Dane wyjściowe|Wystąpienie|
|Http101|HTTP 101|Count|Łącznie|HTTP 101|Wystąpienie|
|Http2xx|Http 2xx|Count|Łącznie|Http 2xx|Wystąpienie|
|Http3xx|Http 3xx|Count|Łącznie|Http 3xx|Wystąpienie|
|Http401|Http 401|Count|Łącznie|Http 401|Wystąpienie|
|Http403|HTTP 403|Count|Łącznie|HTTP 403|Wystąpienie|
|Http404|HTTP 404|Count|Łącznie|HTTP 404|Wystąpienie|
|Http406|HTTP 406|Count|Łącznie|HTTP 406|Wystąpienie|
|Http4xx|Http 4xx|Count|Łącznie|Http 4xx|Wystąpienie|
|Http5xx|Błędy serwera HTTP|Count|Łącznie|Błędy serwera HTTP|Wystąpienie|
|MemoryWorkingSet|Zestaw roboczy pamięci|Bajty|Average|Zestaw roboczy pamięci|Wystąpienie|
|AverageMemoryWorkingSet|Średni zestaw roboczy pamięci|Bajty|Average|Średni zestaw roboczy pamięci|Wystąpienie|
|AverageResponseTime|Średni czas odpowiedzi|Sekundy|Average|Średni czas odpowiedzi|Wystąpienie|
|FunctionExecutionUnits|Jednostki wykonawcze funkcji|Count|Łącznie|Jednostki wykonawcze funkcji|Wystąpienie|
|FunctionExecutionCount|Liczba wykonań funkcji|Count|Łącznie|Liczba wykonań funkcji|Wystąpienie|
|AppConnections|Połączenia|Count|Average|Połączenia|Wystąpienie|
|Obsługuje|Liczba dojść|Count|Average|Liczba dojść|Wystąpienie|
|Wątki|Liczba wątków|Count|Average|Liczba wątków|Wystąpienie|
|PrivateBytes|Bajty prywatne|Bajty|Average|Bajty prywatne|Wystąpienie|
|IoReadBytesPerSecond|Bajty odczytu We/Wy na sekundę|BytesPerSecond|Łącznie|Bajty odczytu We/Wy na sekundę|Wystąpienie|
|IoWriteBytesPerSecond|We/Wy zapisu bajtów na sekundę|BytesPerSecond|Łącznie|We/Wy zapisu bajtów na sekundę|Wystąpienie|
|IoOtherBytesPerSecond|We/Wy inne bajty na sekundę|BytesPerSecond|Łącznie|We/Wy inne bajty na sekundę|Wystąpienie|
|IoReadOperationsPerSecond|Operacje odczytu We/Wy na sekundę|BytesPerSecond|Łącznie|Operacje odczytu We/Wy na sekundę|Wystąpienie|
|IoWriteOperationsPerSecond|We/Wy zapisu na sekundę|BytesPerSecond|Łącznie|We/Wy zapisu na sekundę|Wystąpienie|
|IoOtherOperationsPerSecond|Operacje We/Wy innych na sekundę|BytesPerSecond|Łącznie|Operacje We/Wy innych na sekundę|Wystąpienie|
|RequestsInApplicationQueue|Żądania w kolejce aplikacji|Count|Average|Żądania w kolejce aplikacji|Wystąpienie|
|CurrentAssemblies|Aktualna liczba zestawów|Count|Average|Aktualna liczba zestawów|Wystąpienie|
|TotalAppDomains|Łączna liczba domen aplikacji|Count|Average|Łączna liczba domen aplikacji|Wystąpienie|
|TotalAppDomainsUnloaded|Łączna liczba domen aplikacji zwolnione|Count|Average|Łączna liczba domen aplikacji zwolnione|Wystąpienie|
|Gen0Collections|Velikost haldy 0 wyrzucania elementów bezużytecznych|Count|Łącznie|Velikost haldy 0 wyrzucania elementów bezużytecznych|Wystąpienie|
|Gen1Collections|Gen 1 wyrzucania elementów bezużytecznych|Count|Łącznie|Gen 1 wyrzucania elementów bezużytecznych|Wystąpienie|
|Gen2Collections|Odzyskiwanie obiektów pokolenia 2|Count|Łącznie|Odzyskiwanie obiektów pokolenia 2|Wystąpienie|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Żądania|Żądania|Count|Łącznie|Żądania|Wystąpienie|
|BytesReceived|Dane w|Bajty|Łącznie|Dane w|Wystąpienie|
|BytesSent|Dane wyjściowe|Bajty|Łącznie|Dane wyjściowe|Wystąpienie|
|Http101|HTTP 101|Count|Łącznie|HTTP 101|Wystąpienie|
|Http2xx|Http 2xx|Count|Łącznie|Http 2xx|Wystąpienie|
|Http3xx|Http 3xx|Count|Łącznie|Http 3xx|Wystąpienie|
|Http401|Http 401|Count|Łącznie|Http 401|Wystąpienie|
|Http403|HTTP 403|Count|Łącznie|HTTP 403|Wystąpienie|
|Http404|HTTP 404|Count|Łącznie|HTTP 404|Wystąpienie|
|Http406|HTTP 406|Count|Łącznie|HTTP 406|Wystąpienie|
|Http4xx|Http 4xx|Count|Łącznie|Http 4xx|Wystąpienie|
|Http5xx|Błędy serwera HTTP|Count|Łącznie|Błędy serwera HTTP|Wystąpienie|
|AverageResponseTime|Średni czas odpowiedzi|Sekundy|Average|Średni czas odpowiedzi|Wystąpienie|
|CpuPercentage|Procent użycia procesora CPU|Percent|Average|Procent użycia procesora CPU|Wystąpienie|
|MemoryPercentage|Procent pamięci|Percent|Average|Procent pamięci|Wystąpienie|
|DiskQueueLength|Długość kolejki dysku|Count|Average|Długość kolejki dysku|Wystąpienie|
|HttpQueueLength|Długość kolejki http|Count|Average|Długość kolejki http|Wystąpienie|
|ActiveRequests|Aktywne żądania|Count|Łącznie|Aktywne żądania|Wystąpienie|
|TotalFrontEnds|Łącznie frontonów|Count|Average|Łącznie frontonów|Nie wymiarów|
|SmallAppServicePlanInstances|Małe usługi App Service Plan pracowników|Count|Average|Małe usługi App Service Plan pracowników|Nie wymiarów|
|MediumAppServicePlanInstances|Średnie usługi App Service Plan pracowników|Count|Average|Średnie usługi App Service Plan pracowników|Nie wymiarów|
|LargeAppServicePlanInstances|Procesy robocze Plan dużych usługi App Service|Count|Average|Procesy robocze Plan dużych usługi App Service|Nie wymiarów|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Łączna liczba procesów roboczych|Łączna liczba procesów roboczych|Count|Average|Łączna liczba procesów roboczych|Nie wymiarów|
|WorkersAvailable|Dostępne procesy robocze|Count|Average|Dostępne procesy robocze|Nie wymiarów|
|WorkersUsed|Używane procesy robocze|Count|Average|Używane procesy robocze|Nie wymiarów|
|CpuPercentage|Procent użycia procesora CPU|Percent|Average|Procent użycia procesora CPU|Wystąpienie|
|MemoryPercentage|Procent pamięci|Percent|Average|Procent pamięci|Wystąpienie|

## <a name="next-steps"></a>Następne kroki
* [Przeczytaj o metrykach w usłudze Azure Monitor](data-platform.md)
* [Tworzenie alertów dotyczących metryk](alerts-overview.md)
* [Eksportowanie metryk do magazynu, Centrum zdarzeń lub usługi Log Analytics](diagnostic-logs-overview.md)
