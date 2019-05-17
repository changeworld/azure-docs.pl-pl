---
title: Usługa Azure Monitor obsługiwane metryki według typu zasobów
description: Lista dostępnych dla każdego typu zasobu z usługą Azure Monitor metryk.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/13/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: dae280f86abce47bfcc029f4d81e4ca3a7b696f4
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595434"
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
|qpu_metric|QPU|Count|Średnia|QPU. Zakres 0 – 100 dla S1, 0 – 200 dla S2 oraz 0 – 400 dla S4|ServerResourceType|
|memory_metric|Memory (Pamięć)|Bajty|Średnia|Ilość pamięci. Należeć do zakresu 0 – 25 GB dla S1, 0 – 50 GB dla S2 oraz 0 – 100 GB dla S4|ServerResourceType|
|TotalConnectionRequests|Łączna liczba żądań połączenia|Count|Średnia|Łączna liczba żądań połączenia. Są to połączenia przyjmowane.|ServerResourceType|
|SuccessfullConnectionsPerSec|Udane połączenia na sekundę|CountPerSecond|Średnia|Współczynnik pomyślnie zakończonych połączeń.|ServerResourceType|
|TotalConnectionFailures|Łączna liczba błędów połączenia|Count|Średnia|Łączna liczba nieudanych prób nawiązania połączenia.|ServerResourceType|
|CurrentUserSessions|Bieżące sesje użytkowników|Count|Średnia|Bieżąca liczba ustanowionych sesji użytkowników.|ServerResourceType|
|QueryPoolBusyThreads|Zajęte wątki puli zapytania|Count|Średnia|Liczba zajętych wątków w puli wątków zapytania.|ServerResourceType|
|CommandPoolJobQueueLength|Długość kolejki zadań puli polecenia|Count|Średnia|Liczba zadań w kolejce puli wątków poleceń.|ServerResourceType|
|ProcessingPoolJobQueueLength|Długość kolejki zadań puli przetwarzania|Count|Średnia|Liczba zadań innych niż we/wy w kolejce puli wątków przetwarzania.|ServerResourceType|
|Wartości CurrentConnections|Połączenie: Bieżąca liczba połączeń|Count|Średnia|Bieżąca liczba ustanowionych połączeń klientów.|ServerResourceType|
|CleanerCurrentPrice|Pamięć: Bieżąca cena oczyszczarki|Count|Średnia|Bieżąca cena pamięci $/ bajty/czas, znormalizowana do 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Pamięć: Pamięć oczyszczarki zmniejszania|Bajty|Średnia|Ilość pamięci w bajtach, podlegająca przeczyszczaniu przez w tle czyszcząca.|ServerResourceType|
|CleanerMemoryNonshrinkable|Pamięć: Pamięć oczyszczarki|Bajty|Średnia|Ilość pamięci w bajtach, niepodlegająca przeczyszczaniu przez w tle czyszcząca.|ServerResourceType|
|MemoryUsage|Pamięć: Użycie pamięci|Bajty|Średnia|Użycie pamięci przez proces serwera używane podczas obliczania cena oczyszczarki pamięci. Równe licznikowi Process\PrivateBytes plus rozmiar danych zamapowanych w pamięci, ignorując wszystkie pamięci zamapowanej lub przydzielonej przez aparat analityczny w pamięci xVelocity (VertiPaq) powyżej limitu pamięci aparatu xVelocity.|ServerResourceType|
|MemoryLimitHard|Pamięć: Stały Limit pamięci|Bajty|Średnia|Stały limit pamięci z pliku konfiguracji.|ServerResourceType|
|MemoryLimitHigh|Pamięć: Górny Limit pamięci|Bajty|Średnia|Górny limit pamięci z pliku konfiguracji.|ServerResourceType|
|MemoryLimitLow|Pamięć: Dolny Limit pamięci|Bajty|Średnia|Dolny limit pamięci z pliku konfiguracji.|ServerResourceType|
|MemoryLimitVertiPaq|Pamięć: Limit pamięci aparatu VertiPaq|Bajty|Średnia|Limit w pamięci z pliku konfiguracji.|ServerResourceType|
|Limit przydziału|Pamięć: Limit przydziału|Bajty|Średnia|Bieżący limit przydziału pamięci, w bajtach. Limit przydziału pamięci jest również nazywany rezerwacji pamięci lub przydział pamięci.|ServerResourceType|
|QuotaBlocked|Pamięć: Zablokowany limit przydziału|Count|Średnia|Bieżąca liczba żądań dotyczących limitu przydziału blokowanych do czasu są zwalniane innych limitów przydziału pamięci.|ServerResourceType|
|VertiPaqNonpaged|Pamięć: VertiPaq Nonpaged|Bajty|Średnia|Bajty pamięci zablokowane w zestawie roboczym do użycia przez aparat w pamięci.|ServerResourceType|
|VertiPaqPaged|Pamięć: VertiPaq Paged|Bajty|Średnia|Bajty stronicowanej pamięci dla danych w pamięci.|ServerResourceType|
|RowsReadPerSec|W toku: Odczytane wiersze na sekundę|CountPerSecond|Średnia|Współczynnik wierszy odczytanych ze wszystkich relacyjnych baz danych.|ServerResourceType|
|RowsConvertedPerSec|W toku: Wiersze przekonwertowane na sekundę|CountPerSecond|Średnia|Współczynnik wierszy przekonwertowanych podczas przetwarzania.|ServerResourceType|
|RowsWrittenPerSec|W toku: Zapisane wiersze na sekundę|CountPerSecond|Średnia|Współczynnik wierszy zapisanych podczas przetwarzania.|ServerResourceType|
|CommandPoolBusyThreads|Wątki: Zajęte wątki puli polecenia|Count|Średnia|Liczba zajętych wątków w puli wątków poleceń.|ServerResourceType|
|CommandPoolIdleThreads|Wątki: Bezczynne wątki puli polecenia|Count|Średnia|Liczba bezczynnych wątków w puli wątków poleceń.|ServerResourceType|
|LongParsingBusyThreads|Wątki: Długotrwałej analizie zajęte wątki|Count|Średnia|Liczba zajętych wątków w puli wątków o długotrwałej analizie.|ServerResourceType|
|LongParsingIdleThreads|Wątki: Długotrwałej analizie bezczynne wątki|Count|Średnia|Liczba bezczynnych wątków w puli wątków o długotrwałej analizie.|ServerResourceType|
|LongParsingJobQueueLength|Wątki: Długotrwałej analizie długość kolejki zadań|Count|Średnia|Liczba zadań w kolejce puli wątków o długotrwałej analizie.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Wątki: Zajęte wątki zadań we/wy puli przetwarzania|Count|Średnia|Liczba wątków uruchamiających zadania we/wy w puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Wątki: Zajęte wątki zadań innych we/wy puli przetwarzania|Count|Średnia|Liczba wątków uruchomionych zadań innych niż we/wy w puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Wątki: Długość kolejki zadań we/wy puli przetwarzania|Count|Średnia|Liczba zadań we/wy w kolejce puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Wątki: Bezczynne wątki zadań we/wy puli przetwarzania|Count|Średnia|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Wątki: Bezczynne wątki innego niż we/wy puli przetwarzania|Count|Średnia|Liczba bezczynnych wątków w puli wątków przetwarzania dedykowanych dla zadań innych niż we/wy.|ServerResourceType|
|QueryPoolIdleThreads|Wątki: Bezczynne wątki puli zapytań|Count|Średnia|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|ServerResourceType|
|QueryPoolJobQueueLength|Wątki: Długość kolejki zadań puli zapytań|Count|Średnia|Liczba zadań w kolejce puli wątków zapytania.|ServerResourceType|
|ShortParsingBusyThreads|Wątki: Krótkie analizy zajęte wątki|Count|Średnia|Liczba zajętych wątków w puli wątków krótkotrwałej analizie.|ServerResourceType|
|ShortParsingIdleThreads|Wątki: Krótkie analizy bezczynne wątki|Count|Średnia|Liczba bezczynnych wątków w puli wątków krótkotrwałej analizie.|ServerResourceType|
|ShortParsingJobQueueLength|Wątki: Krótkie analizy długość kolejki zadań|Count|Średnia|Liczba zadań w kolejce puli wątków krótkotrwałej analizie.|ServerResourceType|
|memory_thrashing_metric|Przeładowywanie pamięci|Wartość procentowa|Średnia|Średnie przeładowywanie pamięci.|ServerResourceType|
|mashup_engine_qpu_metric|Jednostka QPU aparatu M|Count|Średnia|Użycie jednostek QPU przez procesy aparatu mashupów|ServerResourceType|
|mashup_engine_memory_metric|Pamięć aparatu M|Bajty|Średnia|Użycie pamięci przez procesy aparatu mashupów|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalRequests|Łącznie żądań bramy|Count|Łączna|Liczba żądań bramy|Lokalizacja i nazwa hosta|
|SuccessfulRequests|Pomyślne żądania bramy|Count|Łączna|Liczba pomyślne żądania bramy|Lokalizacja i nazwa hosta|
|UnauthorizedRequests|Nieautoryzowane żądania bramy|Count|Łączna|Liczba nieautoryzowane żądania bramy|Lokalizacja i nazwa hosta|
|FailedRequests|Żądania bramy zakończone niepowodzeniem|Count|Łączna|Liczba błędów w żądaniach bramy|Lokalizacja i nazwa hosta|
|OtherRequests|Inne żądania bramy|Count|Łączna|Liczba inne żądania bramy|Lokalizacja i nazwa hosta|
|Czas trwania|Całkowity czas trwania żądań bramy|Milisekundy|Średnia|Ogólny czas trwania dla żądania bramy (w milisekundach)|Lokalizacja i nazwa hosta|
|Pojemność|Pojemność|Wartość procentowa|Średnia|Metryki dla usługi ApiManagement|Lokalizacja|
|EventHubTotalEvents|Łączna liczba centrów zdarzeń|Count|Łączna|Liczba zdarzeń wysłanych do Centrum zdarzeń|Lokalizacja|
|EventHubSuccessfulEvents|Pomyślne centrów zdarzeń|Count|Łączna|Liczba pomyślnych zdarzeń Centrum zdarzeń|Lokalizacja|
|EventHubTotalFailedEvents|Zdarzenia Centrum zdarzeń nie powiodło się|Count|Łączna|Liczba zakończonych niepowodzeniem zdarzeń usługi Event Hub|Lokalizacja|
|EventHubRejectedEvents|Odrzucone centrów zdarzeń|Count|Łączna|Liczba odrzuconych zdarzeń usługi Event Hub (niewłaściwa konfiguracja lub brak autoryzacji)|Lokalizacja|
|EventHubThrottledEvents|Zdarzenia ograniczenia usługi EventHub|Count|Łączna|Numer zdarzenia ograniczenia usługi EventHub|Lokalizacja|
|EventHubTimedoutEvents|Przekroczono limit zdarzeń usługi Event Hub|Count|Łączna|Liczba Przekroczono limit zdarzeń usługi Event Hub|Lokalizacja|
|EventHubDroppedEvents|Zdarzenia porzucone Centrum zdarzeń|Count|Łączna|Liczba zdarzeń pominięte z powodu osiągnięto limit rozmiaru w kolejce|Lokalizacja|
|EventHubTotalBytesSent|Rozmiar zdarzeń usługi Event Hub|Bajty|Łączna|Całkowity rozmiar zdarzeń Centrum zdarzeń w bajtach|Lokalizacja|
|Żądania|Żądania|Count|Łączna|Żądania bramy|Lokalizacja, BackendResponseCode, LastErrorReason, GatewayResponseCode|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalJob|Łączna liczba zadań|Count|Łączna|Całkowita liczba zadań|Element Runbook, stan|
|TotalUpdateDeploymentRuns|Przebiegi wdrażania aktualizacji całkowity|Count|Łączna|Uruchamia wdrożenie aktualizacji oprogramowania całkowity|SoftwareUpdateConfigurationName, Status|
|TotalUpdateDeploymentMachineRuns|Łączna liczba aktualizacji wdrożenia maszyny przebiegów|Count|Łączna|Komputer do wdrożenia aktualizacji oprogramowania całkowita jest uruchamiany w uruchomienia wdrożenia aktualizacji oprogramowania|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CoreCount|Liczba rdzeni dedykowanych|Count|Łączna|Całkowita liczba dedykowanych rdzeni na koncie usługi batch|Nie wymiarów|
|TotalNodeCount|Liczba dedykowanych węzłów|Count|Łączna|Całkowita liczba dedykowanych węzłów na koncie usługi batch|Nie wymiarów|
|LowPriorityCoreCount|Liczba rdzeni LowPriority|Count|Łączna|Łączna liczba rdzeni o niskim priorytecie na koncie usługi batch|Nie wymiarów|
|TotalLowPriorityNodeCount|Liczba węzłów o niskim priorytecie|Count|Łączna|Całkowita liczba węzłów o niskim priorytecie na koncie usługi batch|Nie wymiarów|
|CreatingNodeCount|Tworzenie liczba węzłów|Count|Łączna|Liczbę węzłów tworzonych|Nie wymiarów|
|StartingNodeCount|Początkowa liczba węzłów|Count|Łączna|Liczba węzłów uruchamiania|Nie wymiarów|
|WaitingForStartTaskNodeCount|Oczekiwanie liczba węzła zadania uruchamiania|Count|Łączna|Liczba węzłów oczekiwanie na zakończenie zadania Start|Nie wymiarów|
|StartTaskFailedNodeCount|Zadanie podrzędne uruchamiania nie powiodło się liczba węzłów|Count|Łączna|Liczba węzłów, w którym Rozpocznij zadanie nie powiodło się|Nie wymiarów|
|IdleNodeCount|Liczba bezczynnych węzłów|Count|Łączna|Liczba bezczynnych węzłów|Nie wymiarów|
|OfflineNodeCount|Liczba węzłów w trybie offline|Count|Łączna|Liczba węzłów w trybie offline|Nie wymiarów|
|RebootingNodeCount|Ponowne uruchamianie liczba węzłów|Count|Łączna|Liczba ponownego uruchomienia węzłów|Nie wymiarów|
|ReimagingNodeCount|Odtwarzanie z obrazu liczba węzłów|Count|Łączna|Liczba węzłów odtwarzanie z obrazu|Nie wymiarów|
|RunningNodeCount|Liczba węzłów|Count|Łączna|Liczba uruchomionych węzłach|Nie wymiarów|
|LeavingPoolNodeCount|Pozostawienie liczby węzłów w puli|Count|Łączna|Liczba węzłów opuszczanie puli|Nie wymiarów|
|UnusableNodeCount|Korzystanie z tej liczby węzłów|Count|Łączna|Liczba węzłów bezużyteczne|Nie wymiarów|
|PreemptedNodeCount|Przerywane liczba węzłów|Count|Łączna|Liczba węzłów przeniesiona|Nie wymiarów|
|TaskStartEvent|Zadanie rozpoczęcia zdarzenia|Count|Łączna|Całkowita liczba zadań, które zostały uruchomione|Nie wymiarów|
|TaskCompleteEvent|Zakończenie zdarzenia zadań|Count|Łączna|Całkowita liczba zadań, które zostały ukończone|Nie wymiarów|
|TaskFailEvent|Zdarzenia błędów zadań|Count|Łączna|Całkowita liczba zadań, które zostały wykonane w stanie Niepowodzenie|Nie wymiarów|
|PoolCreateEvent|Zdarzenia utworzenia puli|Count|Łączna|Łączna liczba pul, które zostały utworzone|Nie wymiarów|
|PoolResizeStartEvent|Zdarzenia rozpoczęcia zmiany rozmiaru puli|Count|Łączna|Łączna liczba zmienia rozmiar puli, które zostały uruchomione|Nie wymiarów|
|PoolResizeCompleteEvent|Zdarzenia pełną zmiany rozmiaru puli|Count|Łączna|Łączna liczba zmienia rozmiar puli, które zostały ukończone|Nie wymiarów|
|PoolDeleteStartEvent|Zdarzenia rozpoczęcia usuwania puli|Count|Łączna|Łączna liczba usuwa puli, które zostały uruchomione|Nie wymiarów|
|PoolDeleteCompleteEvent|Zdarzenia pełną usuwania puli|Count|Łączna|Łączna liczba usuwa puli, które zostały ukończone|Nie wymiarów|
|JobDeleteCompleteEvent|Zadania Usuń zakończenie zdarzenia|Count|Łączna|Całkowita liczba zadań, które zostały pomyślnie usunięte.|Nie wymiarów|
|JobDeleteStartEvent|Zadania Usuń rozpoczęcia zdarzenia|Count|Łączna|Całkowita liczba zadań, które zostały wymagane do usunięcia.|Nie wymiarów|
|JobDisableCompleteEvent|Zdarzenia pełne wyłączenie zadań|Count|Łączna|Całkowita liczba zadań, które zostały pomyślnie wyłączone.|Nie wymiarów|
|JobDisableStartEvent|Zadanie Wyłącz rozpoczęcia zdarzenia|Count|Łączna|Całkowita liczba zadań, wymagające być wyłączona.|Nie wymiarów|
|JobStartEvent|Zadanie rozpoczęcia zdarzenia|Count|Łączna|Całkowita liczba zadań, które zostało uruchomione pomyślnie.|Nie wymiarów|
|JobTerminateCompleteEvent|Zadanie zakończenia zdarzenia ukończone|Count|Łączna|Całkowita liczba zadań, które zostały pomyślnie zakończone.|Nie wymiarów|
|JobTerminateStartEvent|Zadanie zakończenia zdarzenia rozpoczęcia|Count|Łączna|Całkowita liczba zadań, wymagające ma zostać zakończony.|Nie wymiarów|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|connectedclients|Podłączeni klienci|Count|Maksimum||ShardId|
|totalcommandsprocessed|Łączna liczba operacji|Count|Łączna||ShardId|
|Trafienia w pamięci podręcznej|Trafienia w pamięci podręcznej|Count|Łączna||ShardId|
|cachemisses|Chybienia w pamięci podręcznej|Count|Łączna||ShardId|
|getcommands|Pobrania|Count|Łączna||ShardId|
|setcommands|Zestawy|Count|Łączna||ShardId|
|operationsPerSecond|Operacje na sekundę|Count|Maksimum||ShardId|
|evictedkeys|Wykluczone klucze|Count|Łączna||ShardId|
|totalkeys|Całkowita liczba kluczy|Count|Maksimum||ShardId|
|expiredkeys|Wygasłe klucze|Count|Łączna||ShardId|
|usedmemory|Użyta pamięć|Bajty|Maksimum||ShardId|
|usedmemorypercentage|Procent używanej pamięci|Wartość procentowa|Maksimum||ShardId|
|usedmemoryRss|Używana pamięć RSS|Bajty|Maksimum||ShardId|
|serverLoad|Obciążenie serwera|Wartość procentowa|Maksimum||ShardId|
|cacheWrite|Zapis w pamięci podręcznej|Bajty na sekundę|Maksimum||ShardId|
|cacheRead|Odczyt pamięci podręcznej|Bajty na sekundę|Maksimum||ShardId|
|percentProcessorTime|CPU|Wartość procentowa|Maksimum||ShardId|
|cacheLatency|Pamięć podręczna opóźnienie mikrosekund (wersja zapoznawcza)|Count|Średnia||ShardId, SampleType|
|błędy|Błędy|Count|Maksimum||ShardId, ErrorType|
|connectedclients0|Podłączeni klienci (fragmencie 0)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed0|Łączna liczba operacji (fragmencie 0)|Count|Łączna||Nie wymiarów|
|cachehits0|Trafienia w pamięci podręcznej (fragmencie 0)|Count|Łączna||Nie wymiarów|
|cachemisses0|Chybienia w pamięci podręcznej (fragmencie 0)|Count|Łączna||Nie wymiarów|
|getcommands0|Pobiera (fragmencie 0)|Count|Łączna||Nie wymiarów|
|setcommands0|Zestawy (fragmencie 0)|Count|Łączna||Nie wymiarów|
|operationsPerSecond0|Operacje na sekundę (fragmencie 0)|Count|Maksimum||Nie wymiarów|
|evictedkeys0|Wykluczone klucze (fragmencie 0)|Count|Łączna||Nie wymiarów|
|totalkeys0|Całkowita liczba kluczy (fragmencie 0)|Count|Maksimum||Nie wymiarów|
|expiredkeys0|Wygasłe klucze (fragmencie 0)|Count|Łączna||Nie wymiarów|
|usedmemory0|Używana pamięć (fragmencie 0)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss0|Używana pamięć RSS (fragmencie 0)|Bajty|Maksimum||Nie wymiarów|
|serverLoad0|Obciążenie serwera (fragmencie 0)|Wartość procentowa|Maksimum||Nie wymiarów|
|cacheWrite0|Zapis w pamięci podręcznej (fragmencie 0)|Bajty na sekundę|Maksimum||Nie wymiarów|
|cacheRead0|Odczyt pamięci podręcznej (fragmencie 0)|Bajty na sekundę|Maksimum||Nie wymiarów|
|percentProcessorTime0|Procesor (fragmencie 0)|Wartość procentowa|Maksimum||Nie wymiarów|
|connectedclients1|Podłączeni klienci (fragmencie 1)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed1|Łączna liczba operacji (fragmencie 1)|Count|Łączna||Nie wymiarów|
|cachehits1|Trafienia w pamięci podręcznej (fragmencie 1)|Count|Łączna||Nie wymiarów|
|cachemisses1|Chybienia w pamięci podręcznej (fragmencie 1)|Count|Łączna||Nie wymiarów|
|getcommands1|Pobiera (fragmencie 1)|Count|Łączna||Nie wymiarów|
|setcommands1|Zestawy (fragmencie 1)|Count|Łączna||Nie wymiarów|
|operationsPerSecond1|Operacje na sekundę (fragmencie 1)|Count|Maksimum||Nie wymiarów|
|evictedkeys1|Wykluczone klucze (fragmencie 1)|Count|Łączna||Nie wymiarów|
|totalkeys1|Całkowita liczba kluczy (fragmencie 1)|Count|Maksimum||Nie wymiarów|
|expiredkeys1|Wygasłe klucze (fragmencie 1)|Count|Łączna||Nie wymiarów|
|usedmemory1|Używana pamięć (fragmencie 1)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss1|Używana pamięć RSS (fragmencie 1)|Bajty|Maksimum||Nie wymiarów|
|serverLoad1|Obciążenie serwera (fragmencie 1)|Wartość procentowa|Maksimum||Nie wymiarów|
|cacheWrite1|Zapis w pamięci podręcznej (fragmencie 1)|Bajty na sekundę|Maksimum||Nie wymiarów|
|cacheRead1|Odczyt pamięci podręcznej (fragmencie 1)|Bajty na sekundę|Maksimum||Nie wymiarów|
|percentProcessorTime1|Procesor (fragmencie 1)|Wartość procentowa|Maksimum||Nie wymiarów|
|connectedclients2|Podłączeni klienci (fragmencie 2)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed2|Łączna liczba operacji (fragmencie 2)|Count|Łączna||Nie wymiarów|
|cachehits2|Trafienia w pamięci podręcznej (fragmencie 2)|Count|Łączna||Nie wymiarów|
|cachemisses2|Chybienia w pamięci podręcznej (fragmencie 2)|Count|Łączna||Nie wymiarów|
|getcommands2|Pobiera (fragmencie 2)|Count|Łączna||Nie wymiarów|
|setcommands2|Zestawy (fragmencie 2)|Count|Łączna||Nie wymiarów|
|operationsPerSecond2|Operacje na sekundę (fragmencie 2)|Count|Maksimum||Nie wymiarów|
|evictedkeys2|Wykluczone klucze (fragmencie 2)|Count|Łączna||Nie wymiarów|
|totalkeys2|Całkowita liczba kluczy (fragmencie 2)|Count|Maksimum||Nie wymiarów|
|expiredkeys2|Wygasłe klucze (fragmencie 2)|Count|Łączna||Nie wymiarów|
|usedmemory2|Używana pamięć (fragmencie 2)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss2|Używana pamięć RSS (fragmencie 2)|Bajty|Maksimum||Nie wymiarów|
|serverLoad2|Obciążenie serwera (fragmencie 2)|Wartość procentowa|Maksimum||Nie wymiarów|
|cacheWrite2|Zapis w pamięci podręcznej (fragmencie 2)|Bajty na sekundę|Maksimum||Nie wymiarów|
|cacheRead2|Odczyt pamięci podręcznej (fragmencie 2)|Bajty na sekundę|Maksimum||Nie wymiarów|
|percentProcessorTime2|Procesor (fragmencie 2)|Wartość procentowa|Maksimum||Nie wymiarów|
|connectedclients3|Podłączeni klienci (fragmencie 3)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed3|Łączna liczba operacji (fragmencie 3)|Count|Łączna||Nie wymiarów|
|cachehits3|Trafienia w pamięci podręcznej (fragmencie 3)|Count|Łączna||Nie wymiarów|
|cachemisses3|Chybienia w pamięci podręcznej (fragmencie 3)|Count|Łączna||Nie wymiarów|
|getcommands3|Pobiera (fragmencie 3)|Count|Łączna||Nie wymiarów|
|setcommands3|Zestawy (fragmencie 3)|Count|Łączna||Nie wymiarów|
|operationsPerSecond3|Operacje na sekundę (fragmencie 3)|Count|Maksimum||Nie wymiarów|
|evictedkeys3|Wykluczone klucze (fragmencie 3)|Count|Łączna||Nie wymiarów|
|totalkeys3|Całkowita liczba kluczy (fragmencie 3)|Count|Maksimum||Nie wymiarów|
|expiredkeys3|Wygasłe klucze (fragmencie 3)|Count|Łączna||Nie wymiarów|
|usedmemory3|Używana pamięć (fragmencie 3)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss3|Używana pamięć RSS (fragmencie 3)|Bajty|Maksimum||Nie wymiarów|
|serverLoad3|Obciążenie serwera (fragmencie 3)|Wartość procentowa|Maksimum||Nie wymiarów|
|cacheWrite3|Zapis w pamięci podręcznej (fragmencie 3)|Bajty na sekundę|Maksimum||Nie wymiarów|
|cacheRead3|Odczyt pamięci podręcznej (fragmencie 3)|Bajty na sekundę|Maksimum||Nie wymiarów|
|percentProcessorTime3|Procesor (fragmencie 3)|Wartość procentowa|Maksimum||Nie wymiarów|
|connectedclients4|Podłączeni klienci (fragmencie 4)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed4|Łączna liczba operacji (fragmencie 4)|Count|Łączna||Nie wymiarów|
|cachehits4|Trafienia w pamięci podręcznej (fragmencie 4)|Count|Łączna||Nie wymiarów|
|cachemisses4|Chybienia w pamięci podręcznej (fragmencie 4)|Count|Łączna||Nie wymiarów|
|getcommands4|Pobiera (fragmencie 4)|Count|Łączna||Nie wymiarów|
|setcommands4|Zestawy (fragmencie 4)|Count|Łączna||Nie wymiarów|
|operationsPerSecond4|Operacje na sekundę (fragmencie 4)|Count|Maksimum||Nie wymiarów|
|evictedkeys4|Wykluczone klucze (fragmencie 4)|Count|Łączna||Nie wymiarów|
|totalkeys4|Całkowita liczba kluczy (fragmencie 4)|Count|Maksimum||Nie wymiarów|
|expiredkeys4|Wygasłe klucze (fragmencie 4)|Count|Łączna||Nie wymiarów|
|usedmemory4|Używana pamięć (fragmencie 4)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss4|Używana pamięć RSS (fragmencie 4)|Bajty|Maksimum||Nie wymiarów|
|serverLoad4|Obciążenie serwera (fragmencie 4)|Wartość procentowa|Maksimum||Nie wymiarów|
|cacheWrite4|Zapis w pamięci podręcznej (fragmencie 4)|Bajty na sekundę|Maksimum||Nie wymiarów|
|cacheRead4|Odczyt pamięci podręcznej (fragmencie 4)|Bajty na sekundę|Maksimum||Nie wymiarów|
|percentProcessorTime4|Procesor (fragmencie 4)|Wartość procentowa|Maksimum||Nie wymiarów|
|connectedclients5|Podłączeni klienci (fragmencie 5)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed5|Łączna liczba operacji (fragmencie 5)|Count|Łączna||Nie wymiarów|
|cachehits5|Trafienia w pamięci podręcznej (fragmencie 5)|Count|Łączna||Nie wymiarów|
|cachemisses5|Chybienia w pamięci podręcznej (fragmencie 5)|Count|Łączna||Nie wymiarów|
|getcommands5|Pobiera (fragmencie 5)|Count|Łączna||Nie wymiarów|
|setcommands5|Zestawy (fragmencie 5)|Count|Łączna||Nie wymiarów|
|operationsPerSecond5|Operacje na sekundę (fragmencie 5)|Count|Maksimum||Nie wymiarów|
|evictedkeys5|Wykluczone klucze (fragmencie 5)|Count|Łączna||Nie wymiarów|
|totalkeys5|Całkowita liczba kluczy (fragmencie 5)|Count|Maksimum||Nie wymiarów|
|expiredkeys5|Wygasłe klucze (fragmencie 5)|Count|Łączna||Nie wymiarów|
|usedmemory5|Używana pamięć (fragmencie 5)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss5|Używana pamięć RSS (fragmencie 5)|Bajty|Maksimum||Nie wymiarów|
|serverLoad5|Obciążenie serwera (fragmencie 5)|Wartość procentowa|Maksimum||Nie wymiarów|
|cacheWrite5|Zapis w pamięci podręcznej (fragmencie 5)|Bajty na sekundę|Maksimum||Nie wymiarów|
|cacheRead5|Odczyt pamięci podręcznej (fragmencie 5)|Bajty na sekundę|Maksimum||Nie wymiarów|
|percentProcessorTime5|Procesor (fragmencie 5)|Wartość procentowa|Maksimum||Nie wymiarów|
|connectedclients6|Podłączeni klienci (fragmencie 6)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed6|Łączna liczba operacji (fragmencie 6)|Count|Łączna||Nie wymiarów|
|cachehits6|Trafienia w pamięci podręcznej (fragmencie 6)|Count|Łączna||Nie wymiarów|
|cachemisses6|Chybienia w pamięci podręcznej (fragmencie 6)|Count|Łączna||Nie wymiarów|
|getcommands6|Pobiera (fragmencie 6)|Count|Łączna||Nie wymiarów|
|setcommands6|Zestawy (fragmencie 6)|Count|Łączna||Nie wymiarów|
|operationsPerSecond6|Operacje na sekundę (fragmencie 6)|Count|Maksimum||Nie wymiarów|
|evictedkeys6|Wykluczone klucze (fragmencie 6)|Count|Łączna||Nie wymiarów|
|totalkeys6|Całkowita liczba kluczy (fragmencie 6)|Count|Maksimum||Nie wymiarów|
|expiredkeys6|Wygasłe klucze (fragmencie 6)|Count|Łączna||Nie wymiarów|
|usedmemory6|Używana pamięć (fragmencie 6)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss6|Używana pamięć RSS (fragmencie 6)|Bajty|Maksimum||Nie wymiarów|
|serverLoad6|Obciążenie serwera (fragmencie 6)|Wartość procentowa|Maksimum||Nie wymiarów|
|cacheWrite6|Zapis w pamięci podręcznej (fragmencie 6)|Bajty na sekundę|Maksimum||Nie wymiarów|
|cacheRead6|Odczyt pamięci podręcznej (fragmencie 6)|Bajty na sekundę|Maksimum||Nie wymiarów|
|percentProcessorTime6|Procesor (fragmencie 6)|Wartość procentowa|Maksimum||Nie wymiarów|
|connectedclients7|Podłączeni klienci (fragmencie 7)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed7|Łączna liczba operacji (fragmencie 7)|Count|Łączna||Nie wymiarów|
|cachehits7|Trafienia w pamięci podręcznej (fragmencie 7)|Count|Łączna||Nie wymiarów|
|cachemisses7|Chybienia w pamięci podręcznej (fragmencie 7)|Count|Łączna||Nie wymiarów|
|getcommands7|Pobiera (fragmencie 7)|Count|Łączna||Nie wymiarów|
|setcommands7|Zestawy (fragmencie 7)|Count|Łączna||Nie wymiarów|
|operationsPerSecond7|Operacje na sekundę (fragmencie 7)|Count|Maksimum||Nie wymiarów|
|evictedkeys7|Wykluczone klucze (fragmencie 7)|Count|Łączna||Nie wymiarów|
|totalkeys7|Całkowita liczba kluczy (fragmencie 7)|Count|Maksimum||Nie wymiarów|
|expiredkeys7|Wygasłe klucze (fragmencie 7)|Count|Łączna||Nie wymiarów|
|usedmemory7|Używana pamięć (fragmencie 7)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss7|Używana pamięć RSS (fragmencie 7)|Bajty|Maksimum||Nie wymiarów|
|serverLoad7|Obciążenie serwera (fragmencie 7)|Wartość procentowa|Maksimum||Nie wymiarów|
|cacheWrite7|Zapis w pamięci podręcznej (fragmencie 7)|Bajty na sekundę|Maksimum||Nie wymiarów|
|cacheRead7|Odczyt pamięci podręcznej (fragmencie 7)|Bajty na sekundę|Maksimum||Nie wymiarów|
|percentProcessorTime7|Procesor (fragmencie 7)|Wartość procentowa|Maksimum||Nie wymiarów|
|connectedclients8|Podłączeni klienci (fragmencie 8)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed8|Łączna liczba operacji (fragmencie 8)|Count|Łączna||Nie wymiarów|
|cachehits8|Trafienia w pamięci podręcznej (fragmencie 8)|Count|Łączna||Nie wymiarów|
|cachemisses8|Chybienia w pamięci podręcznej (fragmencie 8)|Count|Łączna||Nie wymiarów|
|getcommands8|Pobiera (fragmencie 8)|Count|Łączna||Nie wymiarów|
|setcommands8|Zestawy (fragmencie 8)|Count|Łączna||Nie wymiarów|
|operationsPerSecond8|Operacje na sekundę (fragmencie 8)|Count|Maksimum||Nie wymiarów|
|evictedkeys8|Wykluczone klucze (fragmencie 8)|Count|Łączna||Nie wymiarów|
|totalkeys8|Całkowita liczba kluczy (fragmencie 8)|Count|Maksimum||Nie wymiarów|
|expiredkeys8|Wygasłe klucze (fragmencie 8)|Count|Łączna||Nie wymiarów|
|usedmemory8|Używana pamięć (fragmencie 8)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss8|Używana pamięć RSS (fragmencie 8)|Bajty|Maksimum||Nie wymiarów|
|serverLoad8|Obciążenie serwera (fragmencie 8)|Wartość procentowa|Maksimum||Nie wymiarów|
|cacheWrite8|Zapis w pamięci podręcznej (fragmencie 8)|Bajty na sekundę|Maksimum||Nie wymiarów|
|cacheRead8|Odczyt pamięci podręcznej (fragmencie 8)|Bajty na sekundę|Maksimum||Nie wymiarów|
|percentProcessorTime8|Procesor (fragmencie 8)|Wartość procentowa|Maksimum||Nie wymiarów|
|connectedclients9|Podłączeni klienci (fragmencie 9)|Count|Maksimum||Nie wymiarów|
|totalcommandsprocessed9|Łączna liczba operacji (fragmencie 9)|Count|Łączna||Nie wymiarów|
|cachehits9|Trafienia w pamięci podręcznej (fragmencie 9)|Count|Łączna||Nie wymiarów|
|cachemisses9|Chybienia w pamięci podręcznej (fragmencie 9)|Count|Łączna||Nie wymiarów|
|getcommands9|Pobiera (fragmencie 9)|Count|Łączna||Nie wymiarów|
|setcommands9|Zestawy (fragmencie 9)|Count|Łączna||Nie wymiarów|
|operationsPerSecond9|Operacje na sekundę (fragmencie 9)|Count|Maksimum||Nie wymiarów|
|evictedkeys9|Wykluczone klucze (fragmencie 9)|Count|Łączna||Nie wymiarów|
|totalkeys9|Całkowita liczba kluczy (fragmencie 9)|Count|Maksimum||Nie wymiarów|
|expiredkeys9|Wygasłe klucze (fragmencie 9)|Count|Łączna||Nie wymiarów|
|usedmemory9|Używana pamięć (fragmencie 9)|Bajty|Maksimum||Nie wymiarów|
|usedmemoryRss9|Używana pamięć RSS (fragmencie 9)|Bajty|Maksimum||Nie wymiarów|
|serverLoad9|Obciążenie serwera (fragmencie 9)|Wartość procentowa|Maksimum||Nie wymiarów|
|cacheWrite9|Zapis w pamięci podręcznej (fragmencie 9)|Bajty na sekundę|Maksimum||Nie wymiarów|
|cacheRead9|Odczyt pamięci podręcznej (fragmencie 9)|Bajty na sekundę|Maksimum||Nie wymiarów|
|percentProcessorTime9|Procesor (fragmencie 9)|Wartość procentowa|Maksimum||Nie wymiarów|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Średnia|Procent przydzielonych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne.|Nie wymiarów|
|Sieć — wejście|Sieć — wejście|Bajty|Łączna|Liczba bajtów odebranych ze wszystkich interfejsów sieciowych przez maszyny wirtualne (ruch przychodzący).|Nie wymiarów|
|Sieć — wyjście|Sieć — wyjście|Bajty|Łączna|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący).|Nie wymiarów|
|Bajty odczytu dysku/s|Odczyt dysku|Bajty na sekundę|Średnia|Średnia liczba bajtów odczytanych z dysku w okresie monitorowania.|Nie wymiarów|
|Bajty zapisu dysku/s|Zapis dysku|Bajty na sekundę|Średnia|Średnia liczba bajtów zapisanych na dysku w okresie monitorowania.|Nie wymiarów|
|Operacje odczytu dysku/s|Operacje odczytu dysku/s|CountPerSecond|Średnia|Operacje we/wy na sekundę dotyczące odczytu z dysku.|Nie wymiarów|
|Operacje zapisu dysku/s|Operacje zapisu dysku/s|CountPerSecond|Średnia|Operacje we/wy na sekundę dotyczące zapisu na dysku.|Nie wymiarów|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Średnia|Procent przydzielonych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne.|RoleInstanceId|
|Sieć — wejście|Sieć — wejście|Bajty|Łączna|Liczba bajtów odebranych ze wszystkich interfejsów sieciowych przez maszyny wirtualne (ruch przychodzący).|RoleInstanceId|
|Sieć — wyjście|Sieć — wyjście|Bajty|Łączna|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący).|RoleInstanceId|
|Bajty odczytu dysku/s|Odczyt dysku|Bajty na sekundę|Średnia|Średnia liczba bajtów odczytanych z dysku w okresie monitorowania.|RoleInstanceId|
|Bajty zapisu dysku/s|Zapis dysku|Bajty na sekundę|Średnia|Średnia liczba bajtów zapisanych na dysku w okresie monitorowania.|RoleInstanceId|
|Operacje odczytu dysku/s|Operacje odczytu dysku/s|CountPerSecond|Średnia|Operacje we/wy na sekundę dotyczące odczytu z dysku.|RoleInstanceId|
|Operacje zapisu dysku/s|Operacje zapisu dysku/s|CountPerSecond|Średnia|Operacje we/wy na sekundę dotyczące zapisu na dysku.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalCalls|Łączna liczba wywołań|Count|Łączna|Łączna liczba wywołań.|Region ApiName, OperationName,|
|SuccessfulCalls|Wywołania zakończone powodzeniem|Count|Łączna|Liczba wywołań zakończonych powodzeniem.|Region ApiName, OperationName,|
|TotalErrors|Łączna liczba błędów|Count|Łączna|Łączna liczba wywołań z odpowiedzią oznaczającą błąd (odpowiedź HTTP o kodzie 4xx lub 5xx).|Region ApiName, OperationName,|
|BlockedCalls|Zablokowane wywołania|Count|Łączna|Liczba wywołań, które przekraczają limit szybkości lub przydziału.|Region ApiName, OperationName,|
|ServerErrors|Błędy serwera|Count|Łączna|Liczba wywołań z błędem wewnętrznym usługi (odpowiedź HTTP o kodzie 5xx).|Region ApiName, OperationName,|
|ClientErrors|Błędy klienta|Count|Łączna|Liczba wywołań z błędem po stronie klienta (odpowiedź HTTP o kodzie 4xx).|Region ApiName, OperationName,|
|DataIn|Dane wejściowe|Bajty|Łączna|Rozmiar danych przychodzących (w bajtach).|Region ApiName, OperationName,|
|DataOut|Dane wyjściowe|Bajty|Łączna|Rozmiar danych wychodzących (w bajtach).|Region ApiName, OperationName,|
|Opóźnienie|Opóźnienie|MilliSeconds|Średnia|Opóźnienie w milisekundach.|Region ApiName, OperationName,|
|CharactersTranslated|Przetłumaczone znaki|Count|Łączna|Łączna liczba znaków w przychodzącym żądaniu tekstowym.|Region ApiName, OperationName,|
|CharactersTrained|Skonfigurowanych pod kątem znaków|Count|Łączna|Całkowita liczba skonfigurowanych pod kątem znaków.|Region ApiName, OperationName,|
|SpeechSessionDuration|Czas trwania sesji mowy|Sekundy|Łączna|Łączny czas trwania sesji mowy w sekundach.|Region ApiName, OperationName,|
|TotalTransactions|Łączna liczba transakcji|Count|Łączna|Łączna liczba transakcji.|Nie wymiarów|
|TotalTokenCalls|Łączna liczba wywołań tokenów|Count|Łączna|Łączna liczba wywołań tokenów.|Region ApiName, OperationName,|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Średnia|Procent przydzielonych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne|Nie wymiarów|
|Sieć — wejście|Ruch przychodzący do sieci (płatny)|Bajty|Łączna|Liczba płatnych bajtów odebranych za pośrednictwem wszystkich interfejsów sieciowych przez maszyny wirtualne (ruch przychodzący)|Nie wymiarów|
|Sieć — wyjście|Ruch wychodzący z sieci (płatny)|Bajty|Łączna|Liczba płatnych bajtów wysłanych za pośrednictwem wszystkich interfejsów sieciowych przez maszyny wirtualne (ruch wychodzący)|Nie wymiarów|
|Bajty odczytane z dysku|Bajty odczytane z dysku|Bajty|Łączna|Liczba bajtów odczytanych z dysku w okresie monitorowania|Nie wymiarów|
|Bajty zapisane na dysku|Bajty zapisane na dysku|Bajty|Łączna|Liczba bajtów zapisanych na dysku w okresie monitorowania|Nie wymiarów|
|Operacje odczytu dysku/s|Operacje odczytu dysku/s|CountPerSecond|Średnia|Operacje we/wy odczytu dysku|Nie wymiarów|
|Operacje zapisu dysku/s|Operacje zapisu dysku/s|CountPerSecond|Średnia|Operacje we/wy zapisu dysku|Nie wymiarów|
|Pozostałe środki na procesory CPU|Pozostałe środki na procesory CPU|Count|Średnia|Łączna ilość środków dostępnych na potrzeby serii|Nie wymiarów|
|Wykorzystane środki na procesory CPU|Wykorzystane środki na procesory CPU|Count|Średnia|Łączna ilość środków wykorzystanych przez maszynę wirtualną|Nie wymiarów|
|Bajty odczytu z dysku/s|Liczba bajtów odczytywanych z dysku danych na sekundę (przestarzałe)|CountPerSecond|Średnia|Liczba bajtów na sekundę odczytywanych z pojedynczego dysku w okresie monitorowania|SlotId|
|Bajty zapisu na dysku/s|Liczba bajtów zapisywanych na dysku danych na sekundę (przestarzałe)|CountPerSecond|Średnia|Liczba bajtów na sekundę zapisywanych na pojedynczym dysku w okresie monitorowania|SlotId|
|Operacje odczytu z dysku/s|Operacje odczytu z dysku danych na sekundę (przestarzałe)|CountPerSecond|Średnia|Liczba operacji we/wy na sekundę dla odczytu z pojedynczego dysku w okresie monitorowania|SlotId|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku danych na sekundę (przestarzałe)|CountPerSecond|Średnia|Liczba operacji we/wy na sekundę dla zapisu na pojedynczym dysku w okresie monitorowania|SlotId|
|Głębokość kolejki dysku|Głębokość kolejki dysku danych (przestarzałe)|Count|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|SlotId|
|Bajty odczytu z dysku systemu operacyjnego/s|Liczba bajtów odczytywanych z dysku systemu operacyjnego na sekundę (przestarzałe)|CountPerSecond|Średnia|Liczba bajtów na sekundę odczytywanych z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Bajty zapisu na dysku systemu operacyjnego/s|Liczba bajtów zapisywanych na dysku systemu operacyjnego na sekundę (przestarzałe)|CountPerSecond|Średnia|Liczba bajtów na sekundę zapisywanych na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Operacje odczytu z dysku systemu operacyjnego/s|Operacje odczytu z dysku systemu operacyjnego na sekundę (przestarzałe)|CountPerSecond|Średnia|Liczba operacji we/wy na sekundę dla odczytu z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Operacje zapisu na dysku systemu operacyjnego/s|Operacje zapisu na dysku systemu operacyjnego na sekundę (przestarzałe)|CountPerSecond|Średnia|Liczba operacji we/wy na sekundę dla zapisu na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Głębokość kolejki dysku systemu operacyjnego|Głębokość kolejki dysku systemu operacyjnego (przestarzałe)|Count|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|Nie wymiarów|
|Liczba bajtów odczytywanych z dysku danych na sekundę|Bajty odczytu z dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Liczba bajtów na sekundę odczytywanych z pojedynczego dysku w okresie monitorowania|LUN|
|Liczba bajtów zapisywanych na dysku danych na sekundę|Bajty zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Liczba bajtów na sekundę zapisywanych na pojedynczym dysku w okresie monitorowania|LUN|
|Operacje odczytu z dysku danych/s|Operacje odczytu z dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Liczba operacji we/wy na sekundę dla odczytu z pojedynczego dysku w okresie monitorowania|LUN|
|Operacje zapisu na dysku danych/s|Operacje zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Liczba operacji we/wy na sekundę dla zapisu na pojedynczym dysku w okresie monitorowania|LUN|
|Głębokość kolejki dysku danych|Głębokość kolejki dysku danych (wersja zapoznawcza)|Count|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|LUN|
|Liczba bajtów odczytywanych z dysku systemu operacyjnego na sekundę|Bajty odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Liczba bajtów na sekundę odczytywanych z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Liczba bajtów zapisywanych na dysku systemu operacyjnego na sekundę|Bajty zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Liczba bajtów na sekundę zapisywanych na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Operacje odczytu z dysku systemu operacyjnego/s|Operacje odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Liczba operacji we/wy na sekundę dla odczytu z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Operacje zapisu na dysku systemu operacyjnego/s|Operacje zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Liczba operacji we/wy na sekundę dla zapisu na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Głębokość kolejki dysku systemu operacyjnego|Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza)|Count|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|Nie wymiarów|
|Przepływy przychodzące|Przepływy przychodzące (wersja zapoznawcza)|Count|Średnia|Przepływy przychodzące to liczba bieżących przepływów z ruchem przychodzącym (ruchem skierowanym do maszyny wirtualnej)|Nie wymiarów|
|Przepływy wychodzące|Przepływy wychodzące (wersja zapoznawcza)|Count|Średnia|Przepływy wychodzące to liczba bieżących przepływów z ruchem wychodzącym (ruchem skierowanym z maszyny wirtualnej)|Nie wymiarów|
|Maksymalna szybkość tworzenia przepływów przychodzących|Maksymalna szybkość tworzenia przepływów przychodzących (wersja zapoznawcza)|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów przychodzących (ruchu przychodzącego do maszyny wirtualnej)|Nie wymiarów|
|Maksymalna szybkość tworzenia przepływów wychodzących|Maksymalna szybkość tworzenia przepływów wychodzących (wersja zapoznawcza)|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów wychodzących (ruchu wychodzącego z maszyny wirtualnej)|Nie wymiarów|
|Liczba trafień w pamięci podręcznej dysku danych w warstwie Premium dla odczytu|Liczba trafień w pamięci podręcznej dysku danych w warstwie Premium dla odczytu (wersja zapoznawcza)|Wartość procentowa|Średnia|Liczba trafień w pamięci podręcznej dysku danych w warstwie Premium dla odczytu|LUN|
|Liczba chybień w pamięci podręcznej dysku danych w warstwie Premium dla odczytu|Liczba chybień w pamięci podręcznej dysku danych w warstwie Premium dla odczytu (wersja zapoznawcza)|Wartość procentowa|Średnia|Liczba chybień w pamięci podręcznej dysku danych w warstwie Premium dla odczytu|LUN|
|Liczba trafień w pamięci podręcznej dysku systemu operacyjnego w warstwie Premium dla odczytu|Liczba trafień w pamięci podręcznej dysku systemu operacyjnego w warstwie Premium dla odczytu (wersja zapoznawcza)|Wartość procentowa|Średnia|Liczba trafień w pamięci podręcznej dysku systemu operacyjnego w warstwie Premium dla odczytu|Nie wymiarów|
|Liczba chybień w pamięci podręcznej dysku systemu operacyjnego w warstwie Premium dla odczytu|Liczba chybień w pamięci podręcznej dysku systemu operacyjnego w warstwie Premium dla odczytu (wersja zapoznawcza)|Wartość procentowa|Średnia|Liczba chybień w pamięci podręcznej dysku systemu operacyjnego w warstwie Premium dla odczytu|Nie wymiarów|
|Ruch przychodzący do sieci (łącznie)|Ruch przychodzący do sieci (łącznie)|Bajty|Łączna|Liczba bajtów odebranych ze wszystkich interfejsów sieciowych przez maszyny wirtualne (ruch przychodzący)|Nie wymiarów|
|Ruch wychodzący z sieci (łącznie)|Ruch wychodzący z sieci (łącznie)|Bajty|Łączna|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący)|Nie wymiarów|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Średnia|Procent przydzielonych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne|Nazwa maszyny wirtualnej|
|Sieć — wejście|Ruch przychodzący do sieci (płatny)|Bajty|Łączna|Liczba płatnych bajtów odebranych za pośrednictwem wszystkich interfejsów sieciowych przez maszyny wirtualne (ruch przychodzący)|Nazwa maszyny wirtualnej|
|Sieć — wyjście|Ruch wychodzący z sieci (płatny)|Bajty|Łączna|Liczba płatnych bajtów wysłanych za pośrednictwem wszystkich interfejsów sieciowych przez maszyny wirtualne (ruch wychodzący)|Nazwa maszyny wirtualnej|
|Bajty odczytane z dysku|Bajty odczytane z dysku|Bajty|Łączna|Liczba bajtów odczytanych z dysku w okresie monitorowania|Nazwa maszyny wirtualnej|
|Bajty zapisane na dysku|Bajty zapisane na dysku|Bajty|Łączna|Liczba bajtów zapisanych na dysku w okresie monitorowania|Nazwa maszyny wirtualnej|
|Operacje odczytu dysku/s|Operacje odczytu dysku/s|CountPerSecond|Średnia|Operacje we/wy odczytu dysku|Nazwa maszyny wirtualnej|
|Operacje zapisu dysku/s|Operacje zapisu dysku/s|CountPerSecond|Średnia|Operacje we/wy zapisu dysku|Nazwa maszyny wirtualnej|
|Pozostałe środki na procesory CPU|Pozostałe środki na procesory CPU|Count|Średnia|Łączna ilość środków dostępnych na potrzeby serii|Nie wymiarów|
|Wykorzystane środki na procesory CPU|Wykorzystane środki na procesory CPU|Count|Średnia|Łączna ilość środków wykorzystanych przez maszynę wirtualną|Nie wymiarów|
|Bajty odczytu z dysku/s|Liczba bajtów odczytywanych z dysku danych na sekundę (przestarzałe)|CountPerSecond|Średnia|Liczba bajtów na sekundę odczytywanych z pojedynczego dysku w okresie monitorowania|SlotId|
|Bajty zapisu na dysku/s|Liczba bajtów zapisywanych na dysku danych na sekundę (przestarzałe)|CountPerSecond|Średnia|Liczba bajtów na sekundę zapisywanych na pojedynczym dysku w okresie monitorowania|SlotId|
|Operacje odczytu z dysku/s|Operacje odczytu z dysku danych na sekundę (przestarzałe)|CountPerSecond|Średnia|Liczba operacji we/wy na sekundę dla odczytu z pojedynczego dysku w okresie monitorowania|SlotId|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku danych na sekundę (przestarzałe)|CountPerSecond|Średnia|Liczba operacji we/wy na sekundę dla zapisu na pojedynczym dysku w okresie monitorowania|SlotId|
|Głębokość kolejki dysku|Głębokość kolejki dysku danych (przestarzałe)|Count|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|SlotId|
|Bajty odczytu z dysku systemu operacyjnego/s|Liczba bajtów odczytywanych z dysku systemu operacyjnego na sekundę (przestarzałe)|CountPerSecond|Średnia|Liczba bajtów na sekundę odczytywanych z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Bajty zapisu na dysku systemu operacyjnego/s|Liczba bajtów zapisywanych na dysku systemu operacyjnego na sekundę (przestarzałe)|CountPerSecond|Średnia|Liczba bajtów na sekundę zapisywanych na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Operacje odczytu z dysku systemu operacyjnego/s|Operacje odczytu z dysku systemu operacyjnego na sekundę (przestarzałe)|CountPerSecond|Średnia|Liczba operacji we/wy na sekundę dla odczytu z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Operacje zapisu na dysku systemu operacyjnego/s|Operacje zapisu na dysku systemu operacyjnego na sekundę (przestarzałe)|CountPerSecond|Średnia|Liczba operacji we/wy na sekundę dla zapisu na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Nie wymiarów|
|Głębokość kolejki dysku systemu operacyjnego|Głębokość kolejki dysku systemu operacyjnego (przestarzałe)|Count|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|Nie wymiarów|
|Liczba bajtów odczytywanych z dysku danych na sekundę|Bajty odczytu z dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Liczba bajtów na sekundę odczytywanych z pojedynczego dysku w okresie monitorowania|Jednostki LUN, VMName|
|Liczba bajtów zapisywanych na dysku danych na sekundę|Bajty zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Liczba bajtów na sekundę zapisywanych na pojedynczym dysku w okresie monitorowania|Jednostki LUN, VMName|
|Operacje odczytu z dysku danych/s|Operacje odczytu z dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Liczba operacji we/wy na sekundę dla odczytu z pojedynczego dysku w okresie monitorowania|Jednostki LUN, VMName|
|Operacje zapisu na dysku danych/s|Operacje zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Liczba operacji we/wy na sekundę dla zapisu na pojedynczym dysku w okresie monitorowania|Jednostki LUN, VMName|
|Głębokość kolejki dysku danych|Głębokość kolejki dysku danych (wersja zapoznawcza)|Count|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|Jednostki LUN, VMName|
|Liczba bajtów odczytywanych z dysku systemu operacyjnego na sekundę|Bajty odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Liczba bajtów na sekundę odczytywanych z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Nazwa maszyny wirtualnej|
|Liczba bajtów zapisywanych na dysku systemu operacyjnego na sekundę|Bajty zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Liczba bajtów na sekundę zapisywanych na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Nazwa maszyny wirtualnej|
|Operacje odczytu z dysku systemu operacyjnego/s|Operacje odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Liczba operacji we/wy na sekundę dla odczytu z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Nazwa maszyny wirtualnej|
|Operacje zapisu na dysku systemu operacyjnego/s|Operacje zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Liczba operacji we/wy na sekundę dla zapisu na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Nazwa maszyny wirtualnej|
|Głębokość kolejki dysku systemu operacyjnego|Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza)|Count|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|Nazwa maszyny wirtualnej|
|Przepływy przychodzące|Przepływy przychodzące (wersja zapoznawcza)|Count|Średnia|Przepływy przychodzące to liczba bieżących przepływów z ruchem przychodzącym (ruchem skierowanym do maszyny wirtualnej)|Nazwa maszyny wirtualnej|
|Przepływy wychodzące|Przepływy wychodzące (wersja zapoznawcza)|Count|Średnia|Przepływy wychodzące to liczba bieżących przepływów z ruchem wychodzącym (ruchem skierowanym z maszyny wirtualnej)|Nazwa maszyny wirtualnej|
|Maksymalna szybkość tworzenia przepływów przychodzących|Maksymalna szybkość tworzenia przepływów przychodzących (wersja zapoznawcza)|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów przychodzących (ruchu przychodzącego do maszyny wirtualnej)|Nazwa maszyny wirtualnej|
|Maksymalna szybkość tworzenia przepływów wychodzących|Maksymalna szybkość tworzenia przepływów wychodzących (wersja zapoznawcza)|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów wychodzących (ruchu wychodzącego z maszyny wirtualnej)|Nazwa maszyny wirtualnej|
|Liczba trafień w pamięci podręcznej dysku danych w warstwie Premium dla odczytu|Liczba trafień w pamięci podręcznej dysku danych w warstwie Premium dla odczytu (wersja zapoznawcza)|Wartość procentowa|Średnia|Liczba trafień w pamięci podręcznej dysku danych w warstwie Premium dla odczytu|Jednostki LUN, VMName|
|Liczba chybień w pamięci podręcznej dysku danych w warstwie Premium dla odczytu|Liczba chybień w pamięci podręcznej dysku danych w warstwie Premium dla odczytu (wersja zapoznawcza)|Wartość procentowa|Średnia|Liczba chybień w pamięci podręcznej dysku danych w warstwie Premium dla odczytu|Jednostki LUN, VMName|
|Liczba trafień w pamięci podręcznej dysku systemu operacyjnego w warstwie Premium dla odczytu|Liczba trafień w pamięci podręcznej dysku systemu operacyjnego w warstwie Premium dla odczytu (wersja zapoznawcza)|Wartość procentowa|Średnia|Liczba trafień w pamięci podręcznej dysku systemu operacyjnego w warstwie Premium dla odczytu|Nazwa maszyny wirtualnej|
|Liczba chybień w pamięci podręcznej dysku systemu operacyjnego w warstwie Premium dla odczytu|Liczba chybień w pamięci podręcznej dysku systemu operacyjnego w warstwie Premium dla odczytu (wersja zapoznawcza)|Wartość procentowa|Średnia|Liczba chybień w pamięci podręcznej dysku systemu operacyjnego w warstwie Premium dla odczytu|Nazwa maszyny wirtualnej|
|Ruch przychodzący do sieci (łącznie)|Ruch przychodzący do sieci (łącznie)|Bajty|Łączna|Liczba bajtów odebranych ze wszystkich interfejsów sieciowych przez maszyny wirtualne (ruch przychodzący)|Nazwa maszyny wirtualnej|
|Ruch wychodzący z sieci (łącznie)|Ruch wychodzący z sieci (łącznie)|Bajty|Łączna|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący)|Nazwa maszyny wirtualnej|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuUsage|Użycie procesora CPU|Count|Średnia|Użycie procesora CPU na wszystkich rdzeniach (w tysięcznych częściach rdzenia).|containerName|
|MemoryUsage|Użycie pamięci|Bajty|Średnia|Całkowite użycie pamięci (w bajtach).|containerName|
|NetworkBytesReceivedPerSecond|Liczba bajtów odebranych przez sieć na sekundę|Bajty|Średnia|Liczba bajtów odebranych przez sieć na sekundę.|Nie wymiarów|
|NetworkBytesTransmittedPerSecond|Liczba bajtów przesłanych przez sieć na sekundę|Bajty|Średnia|Liczba bajtów przesłanych przez sieć na sekundę.|Nie wymiarów|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalPullCount|Liczba całkowita ściągania|Count|Średnia|Liczba obrazów ściąga ogółem|Nie wymiarów|
|SuccessfulPullCount|Liczba pomyślnych ściągania|Count|Średnia|Liczba ściąga obraz pomyślnie|Nie wymiarów|
|TotalPushCount|Liczba całkowita wypychania|Count|Średnia|Liczba obrazów wypycha ogółem|Nie wymiarów|
|SuccessfulPushCount|Liczba pomyślnych wypychania|Count|Średnia|Liczba wypchnięć pomyślne obrazu|Nie wymiarów|
|RunDuration|Czas trwania przebiegu|Milisekundy|Łączna|Czas trwania (w milisekundach)|Nie wymiarów|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Łączna liczba dostępnych rdzeni procesora cpu w zarządzanym klastrze|Count|Łączna|Łączna liczba dostępnych rdzeni procesora cpu w zarządzanym klastrze|Nie wymiarów|
|kube_node_status_allocatable_memory_bytes|Całkowita ilość dostępnej pamięci w zarządzanym klastrze|Bajty|Łączna|Całkowita ilość dostępnej pamięci w zarządzanym klastrze|Nie wymiarów|
|kube_pod_status_ready|Liczba zasobników w stanie gotowości|Count|Łączna|Liczba zasobników w stanie gotowości|przestrzeń nazw, zasobników|
|kube_node_status_condition|Stany dla różnych warunków węzła|Count|Łączna|Stany dla różnych warunków węzła|warunek, stan, status2, węzeł|
|kube_pod_status_phase|Liczba zasobników przez fazy|Count|Łączna|Liczba zasobników przez fazy|Faza, przestrzeń nazw, zasobników|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|DCIApiCalls|Wywołania interfejsu API usługi Insights klienta|Count|Łączna||Nie wymiarów|
|DCIMappingImportOperationSuccessfulLines|Mapowanie wierszy pomyślnych operacji importu|Count|Łączna||Nie wymiarów|
|DCIMappingImportOperationFailedLines|Mapowanie operacji importowania nie powiodła się wierszy|Count|Łączna||Nie wymiarów|
|DCIMappingImportOperationTotalLines|Łączna liczba wierszy operacji importu mapowania|Count|Łączna||Nie wymiarów|
|DCIMappingImportOperationRuntimeInSeconds|Mapowanie środowiska uruchomieniowego operacji importu w ciągu kilku sekund|Sekundy|Łączna||Nie wymiarów|
|DCIOutboundProfileExportSucceeded|Eksportowanie profilu ruchu wychodzącego powiodło się.|Count|Łączna||Nie wymiarów|
|DCIOutboundProfileExportFailed|Eksportowanie profilu ruchu wychodzącego nie powiodło się|Count|Łączna||Nie wymiarów|
|DCIOutboundProfileExportDuration|Czas trwania Eksportowanie profilu ruchu wychodzącego|Sekundy|Łączna||Nie wymiarów|
|DCIOutboundKpiExportSucceeded|Wychodzące eksportu kluczowy wskaźnik wydajności zakończyło się pomyślnie.|Count|Łączna||Nie wymiarów|
|DCIOutboundKpiExportFailed|Eksportowanie wychodzącego kluczowy wskaźnik wydajności nie powiodło się|Count|Łączna||Nie wymiarów|
|DCIOutboundKpiExportDuration|Czas trwania wychodzącego eksportu kluczowy wskaźnik wydajności|Sekundy|Łączna||Nie wymiarów|
|DCIOutboundKpiExportStarted|Eksportowanie wychodzącego kluczowy wskaźnik wydajności pracy|Sekundy|Łączna||Nie wymiarów|
|DCIOutboundKpiRecordCount|Liczba rekordów wychodzącego kluczowego wskaźnika wydajności.|Sekundy|Łączna||Nie wymiarów|
|DCIOutboundProfileExportCount|Liczba Eksportowanie profilu ruchu wychodzącego|Sekundy|Łączna||Nie wymiarów|
|DCIOutboundInitialProfileExportFailed|Eksportowanie profilu początkowej ruchu wychodzącego nie powiodło się|Sekundy|Łączna||Nie wymiarów|
|DCIOutboundInitialProfileExportSucceeded|Eksportowanie profilu początkowej ruchu wychodzącego zakończyło się pomyślnie|Sekundy|Łączna||Nie wymiarów|
|DCIOutboundInitialKpiExportFailed|Wychodzące początkowej eksportowanie kluczowy wskaźnik wydajności nie powiodło się|Sekundy|Łączna||Nie wymiarów|
|DCIOutboundInitialKpiExportSucceeded|Wychodzące początkowej eksportu kluczowy wskaźnik wydajności zakończyło się pomyślnie.|Sekundy|Łączna||Nie wymiarów|
|DCIOutboundInitialProfileExportDurationInSeconds|Wychodzące wstępnego profilu eksportowania czas w sekundach|Sekundy|Łączna||Nie wymiarów|
|AdlaJobForStandardKpiFailed|Zadanie Adla dla standardowych wskaźnika Kpi nie powiodło się w ciągu kilku sekund|Sekundy|Łączna||Nie wymiarów|
|AdlaJobForStandardKpiTimeOut|Adla Job For Standard Kpi TimeOut In Seconds|Sekundy|Łączna||Nie wymiarów|
|AdlaJobForStandardKpiCompleted|Zadanie Adla dla standardowych kluczowy wskaźnik wydajności, które są ukończone w ciągu kilku sekund|Sekundy|Łączna||Nie wymiarów|
|ImportASAValuesFailed|Liczba nieudanych wartości ASA importu|Count|Łączna||Nie wymiarów|
|ImportASAValuesSucceeded|Wartości ASA Importowanie powiodło się. liczba|Count|Łączna||Nie wymiarów|
|DCIProfilesCount|Liczba wystąpień profilu|Count|Ostatnia||Nie wymiarów|
|DCIInteractionsPerMonthCount|Interakcje za liczbę miesięcznie|Count|Ostatnia||Nie wymiarów|
|DCIKpisCount|Liczba kluczowy wskaźnik wydajności|Count|Ostatnia||Nie wymiarów|
|DCISegmentsCount|Liczba segmentów|Count|Ostatnia||Nie wymiarów|
|DCIPredictiveMatchPoliciesCount|Liczba dopasowań predykcyjne|Count|Ostatnia||Nie wymiarów|
|DCIPredictionsCount|Liczba prognoz|Count|Ostatnia||Nie wymiarów|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|NICReadThroughput|Przepływność odczytu (sieć)|Bajty na sekundę|Średnia|Przepływność odczytu interfejsu sieciowego na urządzeniu w okresie raportowania dla wszystkich woluminów w bramie.|InstanceName|
|NICWriteThroughput|Przepływność zapisu (sieć)|Bajty na sekundę|Średnia|Przepływność zapisu interfejsu sieciowego na urządzeniu w okresie raportowania dla wszystkich woluminów w bramie.|InstanceName|
|CloudReadThroughputPerShare|Przepływność pobierania w chmurze (udział)|Bajty na sekundę|Średnia|Przepływność pobierania z udziału na platformę Azure w okresie raportowania.|Udostępnij|
|CloudUploadThroughputPerShare|Przepływność przekazywania w chmurze (udział)|Bajty na sekundę|Średnia|Przepływność przekazywania z udziału na platformę Azure w okresie raportowania.|Udostępnij|
|BytesUploadedToCloudPerShare|Bajty przekazane w chmurze (udział)|Bajty|Średnia|Łączna liczba bajtów przekazanych do platformy Azure z udziału w okresie raportowania.|Udostępnij|
|Łączna pojemność|Całkowita pojemność|Bajty|Średnia|Całkowita pojemność|Nie wymiarów|
|AvailableCapacity|Dostępna pojemność|Bajty|Średnia|Dostępna pojemność w bajtach, w tym okresie raportowania.|Nie wymiarów|
|CloudUploadThroughput|Przepływność przekazywania w chmurze|Bajty na sekundę|Średnia|Przepływność przekazywania w chmurze na platformę Azure w okresie raportowania.|Nie wymiarów|
|CloudReadThroughput|Przepływność pobierania w chmurze|Bajty na sekundę|Średnia|Przepływność pobierania w chmurze na platformę Azure w okresie raportowania.|Nie wymiarów|
|BytesUploadedToCloud|Bajty przekazane w chmurze (urządzenie)|Bajty|Średnia|Łączna liczba bajtów przekazanych do platformy Azure z urządzenia w okresie raportowania.|Nie wymiarów|
|HyperVVirtualProcessorUtilization|Funkcja obliczeniowa Edge — procent procesora CPU|Wartość procentowa|Średnia|Procent użycia procesora CPU|InstanceName|
|HyperVMemoryUtilization|Funkcja obliczeniowa Edge — użycie pamięci|Wartość procentowa|Średnia|Ilość pamięci RAM w użyciu|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|FailedRuns|Nieudane uruchomienia|Count|Łączna||Nazwa potoku:, nazwa działania:|
|SuccessfulRuns|Pomyślne uruchomienia|Count|Łączna||Nazwa potoku:, nazwa działania:|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PipelineFailedRuns|Nie powiodło się metryki uruchomienia potoku|Count|Łączna||FailureType, nazwa|
|PipelineSucceededRuns|Powodzenie metryki uruchomienia potoku|Count|Łączna||FailureType, nazwa|
|ActivityFailedRuns|Nie powiodło się metryki uruchomień działań|Count|Łączna||Nazwa właściwości ActivityType, Nazwa potoku: FailureType,|
|ActivitySucceededRuns|Powodzenie metryki uruchomień działań|Count|Łączna||Nazwa właściwości ActivityType, Nazwa potoku: FailureType,|
|TriggerFailedRuns|Nie powiodło się metryki uruchomień wyzwalacza|Count|Łączna||Nazwa, FailureType|
|TriggerSucceededRuns|Powodzenie metryki uruchomień wyzwalacza|Count|Łączna||Nazwa, FailureType|
|IntegrationRuntimeCpuPercentage|Wykorzystanie procesora CPU w czasie wykonywania integracji|Wartość procentowa|Średnia||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Dostępna pamięć środowiska Integration runtime|Bajty|Średnia||IntegrationRuntimeName, NodeName|
|MaxAllowedResourceCount|Liczba jednostek dozwolony maksymalny|Count|Maksimum||Nie wymiarów|
|MaxAllowedFactorySizeInGbUnits|Maksymalny dozwolony rozmiar fabryki (jednostka GB)|Count|Maksimum||Nie wymiarów|
|Liczba zasobów|Liczba całkowita jednostek|Count|Maksimum||Nie wymiarów|
|FactorySizeInGbUnits|Fabryka całkowity rozmiar (jednostka GB)|Count|Maksimum||Nie wymiarów|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|JobEndedSuccess|Zadania zakończone pomyślnie|Count|Łączna|Liczba zadań zakończonych pomyślnie.|Nie wymiarów|
|JobEndedFailure|Zadania zakończone niepowodzeniem|Count|Łączna|Liczba zadań zakończonych niepowodzeniem.|Nie wymiarów|
|JobEndedCancelled|Anulowane zadania|Count|Łączna|Liczba anulowanych zadań.|Nie wymiarów|
|JobAUEndedSuccess|Pomyślne godziny korzystania z jednostki analizy|Sekundy|Łączna|Łączny czas korzystania z jednostki analizy dla przypadku pomyślnie zakończonych zadań.|Nie wymiarów|
|JobAUEndedFailure|Nie powiodło się czas korzystania z jednostki analizy|Sekundy|Łączna|Łączny czas korzystania z jednostki analizy zadania zakończone niepowodzeniem.|Nie wymiarów|
|JobAUEndedCancelled|Anulowano godziny korzystania z jednostki analizy|Sekundy|Łączna|Łączny czas korzystania z jednostki analizy dla anulowanych zadań.|Nie wymiarów|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalStorage|Całkowita ilość miejsca do magazynowania|Bajty|Maksimum|Całkowita ilość danych przechowywanych na koncie.|Nie wymiarów|
|DataWritten|Zapisane dane|Bajty|Łączna|Całkowita ilość danych zapisywanych na koncie.|Nie wymiarów|
|Odczyt danych|Odczyt danych|Bajty|Łączna|Łączna ilość danych odczytanych z konta.|Nie wymiarów|
|WriteRequests|Żądania zapisu|Count|Łączna|Liczba danych liczbę żądań zapisu do konta.|Nie wymiarów|
|ReadRequests|Żądania odczytu|Count|Łączna|Liczba danych odczytu żądań do konta.|Nie wymiarów|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia Procesora|Wartość procentowa|Średnia|Procent użycia Procesora|Nie wymiarów|
|memory_percent|Procent pamięci|Wartość procentowa|Średnia|Procent pamięci|Nie wymiarów|
|io_consumption_percent|% Operacji We/Wy|Wartość procentowa|Średnia|% Operacji We/Wy|Nie wymiarów|
|storage_percent|Procent pamięci masowej|Wartość procentowa|Średnia|Procent pamięci masowej|Nie wymiarów|
|storage_used|Używane miejsce|Bajty|Średnia|Używane miejsce|Nie wymiarów|
|storage_limit|Limit magazynu|Bajty|Średnia|Limit magazynu|Nie wymiarów|
|serverlog_storage_percent|Procent magazynu dziennika serwera|Wartość procentowa|Średnia|Procent magazynu dziennika serwera|Nie wymiarów|
|serverlog_storage_usage|Używany Magazyn dzienników serwera|Bajty|Średnia|Używany Magazyn dzienników serwera|Nie wymiarów|
|serverlog_storage_limit|Limit magazynu dziennika serwera|Bajty|Średnia|Limit magazynu dziennika serwera|Nie wymiarów|
|active_connections|Aktywne połączenia|Count|Średnia|Aktywne połączenia|Nie wymiarów|
|connections_failed|Połączenia zakończone niepowodzeniem|Count|Łączna|Połączenia zakończone niepowodzeniem|Nie wymiarów|
|seconds_behind_master|Opóźnienie replikacji w ciągu kilku sekund|Count|Średnia|Opóźnienie replikacji w ciągu kilku sekund|Nie wymiarów|
|backup_storage_used|Używany magazyn kopii zapasowych|Bajty|Średnia|Używany magazyn kopii zapasowych|Nie wymiarów|
|network_bytes_egress|Sieć — wyjście|Bajty|Łączna|Sieć się między aktywnych połączeń|Nie wymiarów|
|network_bytes_ingress|Sieć — wejście|Bajty|Łączna|W sieci, między aktywnych połączeń|Nie wymiarów|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia Procesora|Wartość procentowa|Średnia|Procent użycia Procesora|Nie wymiarów|
|memory_percent|Procent pamięci|Wartość procentowa|Średnia|Procent pamięci|Nie wymiarów|
|io_consumption_percent|% Operacji We/Wy|Wartość procentowa|Średnia|% Operacji We/Wy|Nie wymiarów|
|storage_percent|Procent pamięci masowej|Wartość procentowa|Średnia|Procent pamięci masowej|Nie wymiarów|
|storage_used|Używane miejsce|Bajty|Średnia|Używane miejsce|Nie wymiarów|
|storage_limit|Limit magazynu|Bajty|Średnia|Limit magazynu|Nie wymiarów|
|serverlog_storage_percent|Procent magazynu dziennika serwera|Wartość procentowa|Średnia|Procent magazynu dziennika serwera|Nie wymiarów|
|serverlog_storage_usage|Używany Magazyn dzienników serwera|Bajty|Średnia|Używany Magazyn dzienników serwera|Nie wymiarów|
|serverlog_storage_limit|Limit magazynu dziennika serwera|Bajty|Średnia|Limit magazynu dziennika serwera|Nie wymiarów|
|active_connections|Aktywne połączenia|Count|Średnia|Aktywne połączenia|Nie wymiarów|
|connections_failed|Połączenia zakończone niepowodzeniem|Count|Łączna|Połączenia zakończone niepowodzeniem|Nie wymiarów|
|seconds_behind_master|Opóźnienie replikacji w ciągu kilku sekund|Count|Średnia|Opóźnienie replikacji w ciągu kilku sekund|Nie wymiarów|
|backup_storage_used|Używany magazyn kopii zapasowych|Bajty|Średnia|Używany magazyn kopii zapasowych|Nie wymiarów|
|network_bytes_egress|Sieć — wyjście|Bajty|Łączna|Sieć się między aktywnych połączeń|Nie wymiarów|
|network_bytes_ingress|Sieć — wejście|Bajty|Łączna|W sieci, między aktywnych połączeń|Nie wymiarów|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia Procesora|Wartość procentowa|Średnia|Procent użycia Procesora|Nie wymiarów|
|memory_percent|Procent pamięci|Wartość procentowa|Średnia|Procent pamięci|Nie wymiarów|
|io_consumption_percent|% Operacji We/Wy|Wartość procentowa|Średnia|% Operacji We/Wy|Nie wymiarów|
|storage_percent|Procent pamięci masowej|Wartość procentowa|Średnia|Procent pamięci masowej|Nie wymiarów|
|storage_used|Używane miejsce|Bajty|Średnia|Używane miejsce|Nie wymiarów|
|storage_limit|Limit magazynu|Bajty|Średnia|Limit magazynu|Nie wymiarów|
|serverlog_storage_percent|Procent magazynu dziennika serwera|Wartość procentowa|Średnia|Procent magazynu dziennika serwera|Nie wymiarów|
|serverlog_storage_usage|Używany Magazyn dzienników serwera|Bajty|Średnia|Używany Magazyn dzienników serwera|Nie wymiarów|
|serverlog_storage_limit|Limit magazynu dziennika serwera|Bajty|Średnia|Limit magazynu dziennika serwera|Nie wymiarów|
|active_connections|Aktywne połączenia|Count|Średnia|Aktywne połączenia|Nie wymiarów|
|connections_failed|Połączenia zakończone niepowodzeniem|Count|Łączna|Połączenia zakończone niepowodzeniem|Nie wymiarów|
|backup_storage_used|Używany magazyn kopii zapasowych|Bajty|Średnia|Używany magazyn kopii zapasowych|Nie wymiarów|
|network_bytes_egress|Sieć — wyjście|Bajty|Łączna|Sieć się między aktywnych połączeń|Nie wymiarów|
|network_bytes_ingress|Sieć — wejście|Bajty|Łączna|W sieci, między aktywnych połączeń|Nie wymiarów|
|pg_replica_log_delay_in_seconds|Opóźnienie repliki|Sekundy|Maksimum|Opóźnienie repliki w ciągu kilku sekund|Nie wymiarów|
|pg_replica_log_delay_in_bytes|Maksymalna liczba opóźnienie między replikami|Bajty|Maksimum|Funkcja Lag w bajtach najbardziej opóźnione repliki bazy danych|Nie wymiarów|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia Procesora|Wartość procentowa|Średnia|Procent użycia Procesora|Nie wymiarów|
|memory_percent|Procent pamięci|Wartość procentowa|Średnia|Procent pamięci|Nie wymiarów|
|operacje We/Wy|Operacje wejścia/wyjścia|Count|Średnia|Operacje We/Wy na sekundę|Nie wymiarów|
|storage_percent|Procent pamięci masowej|Wartość procentowa|Średnia|Procent pamięci masowej|Nie wymiarów|
|storage_used|Używane miejsce|Bajty|Średnia|Używane miejsce|Nie wymiarów|
|active_connections|Aktywne połączenia|Count|Średnia|Aktywne połączenia|Nie wymiarów|
|network_bytes_egress|Sieć — wyjście|Bajty|Łączna|Sieć się między aktywnych połączeń|Nie wymiarów|
|network_bytes_ingress|Sieć — wejście|Bajty|Łączna|W sieci, między aktywnych połączeń|Nie wymiarów|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Próby wysłania komunikatu telemetrii|Count|Łączna|Liczba komunikatów danych telemetrycznych z urządzenia do chmury podjęto próbę wysłania do Centrum IoT hub|Nie wymiarów|
|d2c.telemetry.ingress.success|Komunikaty telemetryczne wysyłane|Count|Łączna|Liczba komunikatów danych telemetrycznych z urządzenia do chmury pomyślnie wysłany do usługi IoT hub|Nie wymiarów|
|c2d.commands.egress.complete.success|Polecenia zakończona|Count|Łączna|Wiele poleceń z chmury do urządzenia zakończyło się powodzeniem, urządzenie|Nie wymiarów|
|c2d.commands.egress.abandon.success|Polecenia porzucone|Count|Łączna|Wiele poleceń chmura urządzenie porzucone przez urządzenie|Nie wymiarów|
|c2d.commands.egress.reject.success|Polecenia odrzucone|Count|Łączna|Wiele poleceń chmura urządzenie odrzucone przez urządzenie|Nie wymiarów|
|devices.totalDevices|Łączna liczba urządzeń (przestarzałe)|Count|Łączna|Liczba urządzeń zarejestrowanych do Centrum IoT hub|Nie wymiarów|
|devices.connectedDevices.allProtocol|Połączone urządzenia (przestarzałe) |Count|Łączna|Liczba urządzeń podłączonych do Centrum IoT hub|Nie wymiarów|
|d2c.telemetry.egress.success|Routingu: wydana komunikaty telemetryczne|Count|Łączna|Liczba przypadków, gdy wiadomości zostały pomyślnie dostarczone do wszystkich punktów końcowych przy użyciu routingu w usłudze IoT Hub. Jeśli komunikat jest kierowany do wielu punktów końcowych, ta wartość zwiększa się o jeden dla każdego skutecznej. Jeśli komunikat jest dostarczane do tego samego punktu końcowego wiele razy, ta wartość zwiększa się o jeden dla każdego skutecznej.|Nie wymiarów|
|d2c.telemetry.egress.dropped|Routingu: porzucone komunikaty telemetryczne |Count|Łączna|Liczba przypadków, gdy komunikaty zostały porzucone przez usługę IoT Hub routing z powodu braku punktów końcowych. Ta wartość nie jest liczony komunikaty dostarczane do trasy rezerwowej, gdy porzuconych wiadomości nie są dostarczane istnieje.|Nie wymiarów|
|d2c.telemetry.egress.orphaned|Routingu: porzucone komunikaty telemetryczne |Count|Łączna|Liczba przypadków, gdy komunikaty zostały oddzielone kierując usługi IoT Hub, ponieważ nie pasują do żadnych reguł routingu (w tym reguły rezerwowego). |Nie wymiarów|
|d2c.telemetry.egress.invalid|Routing: komunikaty telemetryczne niezgodne|Count|Łączna|Liczba razy routingu w usłudze IoT Hub nie można dostarczać komunikatów z powodu niezgodności z punktem końcowym. Ta wartość nie obejmuje ponownych prób.|Nie wymiarów|
|d2c.telemetry.egress.fallback|Routing: komunikaty dostarczane do rezerwowego|Count|Łączna|Liczba przypadków, dostarczenia komunikatów w Centrum IoT Hub routingu do skojarzonej z trasą rezerwowego punktu końcowego.|Nie wymiarów|
|d2c.endpoints.egress.eventHubs|Routing: komunikaty dostarczane do Centrum zdarzeń|Count|Łączna|Liczba przypadków, gdy Centrum IoT Hub routingu pomyślnie dostarczone wiadomości do punktów końcowych Centrum zdarzeń.|Nie wymiarów|
|d2c.endpoints.latency.eventHubs|Routing: komunikat o opóźnieniu dla Centrum zdarzeń|Milisekundy|Średnia|Średnie opóźnienie (w milisekundach) między komunikatów przychodzących do usługi IoT Hub i ruch przychodzący komunikat do punktu końcowego Centrum zdarzeń.|Nie wymiarów|
|d2c.endpoints.egress.serviceBusQueues|Routing: komunikaty dostarczane do kolejki usługi Service Bus|Count|Łączna|Liczba przypadków, gdy usługi IoT Hub routingu pomyślnie dostarczone wiadomości do punktów końcowych kolejki usługi Service Bus.|Nie wymiarów|
|d2c.endpoints.latency.serviceBusQueues|Routing: opóźnienie komunikat do kolejki usługi Service Bus|Milisekundy|Średnia|Średnie opóźnienie (w milisekundach) między komunikatów przychodzących do usługi IoT Hub i transferu danych przychodzących danych telemetrycznych komunikat do punktu końcowego kolejki usługi Service Bus.|Nie wymiarów|
|d2c.endpoints.egress.serviceBusTopics|Routing: komunikaty dostarczane do tematu usługi Service Bus|Count|Łączna|Liczba przypadków, gdy Centrum IoT Hub routingu pomyślnie dostarczone wiadomości do punktów końcowych z tematu usługi Service Bus.|Nie wymiarów|
|d2c.endpoints.latency.serviceBusTopics|Routing: opóźnienie komunikat do tematu usługi Service Bus|Milisekundy|Średnia|Średnie opóźnienie (w milisekundach) między komunikatów przychodzących do usługi IoT Hub i transferu danych przychodzących danych telemetrycznych komunikat do punktu końcowego tematu usługi Service Bus.|Nie wymiarów|
|d2c.endpoints.egress.builtIn.events|Routing: komunikaty dostarczane do komunikaty/zdarzenia|Count|Łączna|Liczba przypadków, gdy Centrum IoT Hub routingu pomyślnie dostarczone wiadomości do wbudowany punkt końcowy (komunikaty/zdarzenia).|Nie wymiarów|
|d2c.endpoints.latency.builtIn.events|Routing: komunikat o opóźnieniu komunikaty/zdarzenia|Milisekundy|Średnia|Średnie opóźnienie (w milisekundach) między komunikatów przychodzących do usługi IoT Hub i transferu danych przychodzących danych telemetrycznych wiadomości do wbudowany punkt końcowy (komunikaty/zdarzenia).|Nie wymiarów|
|d2c.endpoints.egress.storage|Routing: komunikaty dostarczane do magazynu|Count|Łączna|Liczba przypadków, gdy usługi IoT Hub routingu pomyślnie dostarczone wiadomości do punktów końcowych magazynu.|Nie wymiarów|
|d2c.endpoints.latency.storage|Routing: komunikat o opóźnieniu magazynu|Milisekundy|Średnia|Średnie opóźnienie (w milisekundach) między komunikatów przychodzących do usługi IoT Hub i transferu danych przychodzących danych telemetrycznych komunikat do punktu końcowego magazynu.|Nie wymiarów|
|d2c.endpoints.egress.storage.bytes|Routing: dane dostarczane do magazynu|Bajty|Łączna|Ilość danych (w bajtach) routingu w usłudze IoT Hub dostarczone do punktów końcowych magazynu.|Nie wymiarów|
|d2c.endpoints.egress.storage.blobs|Routing: obiektów blob jest dostarczane do magazynu|Count|Łączna|Liczba przypadków, gdy routingu w usłudze IoT Hub dostarczane obiekty BLOB do przechowywania punktów końcowych.|Nie wymiarów|
|EventGridDeliveries|Event Grid dostaw (wersja zapoznawcza)|Count|Łączna|Liczba żądań usługi IoT Hub emitować zdarzenia do usługi Event Grid. Liczba ta obejmuje udane i nieudane żądania. Wymiar wyniku na użytek numer innego typu odpowiedzi. Aby zobaczyć, gdzie pochodzą żądania, należy użyć w wymiarze typ zdarzenia.|Wynik, typ zdarzenia|
|EventGridLatency|Czas oczekiwania na zdarzenie siatki (wersja zapoznawcza)|Milisekundy|Średnia|Średnie opóźnienie (w milisekundach) między danych zdarzeń przychodzących do usługi IoT Hub i zdarzeń przychodzących do usługi Event Grid. Ta liczba jest średnia pomiędzy wszystkich typów zdarzeń. Użyj w wymiarze typ zdarzenia, aby zobaczyć opóźnienie określonych typach zdarzenia.|EventType|
|d2c.twin.read.success|Pomyślne bliźniacze odczyty z urządzenia|Count|Łączna|Liczba wszystkich pomyślnie zainicjował urządzenia bliźniacze odczyty na dysku.|Nie wymiarów|
|d2c.twin.read.failure|Nie powiodło się bliźniacze odczyty z urządzenia|Count|Łączna|Zainicjowane przez urządzenie bliźniacze odczyty liczbę wszystkich, nie powiodło się.|Nie wymiarów|
|d2c.twin.read.size|Rozmiar odpowiedzi bliźniacze odczyty z urządzenia|Bajty|Średnia|Średnią, minimalna i maksymalna elementu wszystkie udane, inicjowane przez urządzenie bliźniaczej reprezentacji odczytów.|Nie wymiarów|
|d2c.twin.update.success|Liczba aktualizacji bliźniaczej reprezentacji pomyślnie z urządzeń|Count|Łączna|Liczba wszystkich pomyślnie zainicjował urządzenia aktualizacji bliźniaczej reprezentacji.|Nie wymiarów|
|d2c.twin.update.failure|Niepowodzenie aktualizacji bliźniaczej reprezentacji urządzenia|Count|Łączna|Liczba wszystkich nie powiodło się aktualizacji inicjowane przez urządzenie bliźniaczej reprezentacji.|Nie wymiarów|
|d2c.twin.update.size|Rozmiar aktualizacji bliźniaczej reprezentacji urządzenia|Bajty|Średnia|Średnia, minimum i maksymalny rozmiar wszystkich pomyślnie, inicjowane przez urządzenie bliźniaczej reprezentacji aktualizacji.|Nie wymiarów|
|c2d.methods.success|Pomyślne bezpośrednie wywołania metod|Count|Łączna|Liczba wszystkich pomyślnych wywołań metody bezpośredniej.|Nie wymiarów|
|c2d.methods.failure|Niepowodzenie wywołania metody bezpośredniej|Count|Łączna|Liczba wszystkich nie wywołania metody bezpośredniej.|Nie wymiarów|
|c2d.methods.requestSize|Rozmiar żądania wywołania metody bezpośredniej|Bajty|Średnia|Średnia, minimum i maksimum wszystkich pomyślnych żądań metody bezpośredniej.|Nie wymiarów|
|c2d.methods.responseSize|Rozmiar odpowiedzi z wywołania metody bezpośredniej|Bajty|Średnia|Średnia, minimum i maksimum wszystkich pomyślne odpowiedzi metody bezpośredniej.|Nie wymiarów|
|c2d.twin.read.success|Pomyślne bliźniacze odczyty z zaplecza|Count|Łączna|Liczba wszystkich pomyślnie zainicjował zakończenia kopii bliźniacze odczyty na dysku.|Nie wymiarów|
|c2d.twin.read.failure|Nie powiodło się bliźniacze odczyty z zaplecza|Count|Łączna|Zainicjowano zakończenia kopii bliźniacze odczyty liczbę wszystkich, nie powiodło się.|Nie wymiarów|
|c2d.twin.read.size|Rozmiar odpowiedzi bliźniacze odczyty z zaplecza|Bajty|Średnia|Średnią, minimalna i maksymalna elementu wszystkie udane, zainicjowanych zakończenia wstecz bliźniaczej reprezentacji odczytów.|Nie wymiarów|
|c2d.twin.update.success|Liczba aktualizacji bliźniaczej reprezentacji pomyślnie z zaplecza|Count|Łączna|Liczba wszystkich pomyślnie zainicjował zakończenia kopii aktualizacji bliźniaczej reprezentacji.|Nie wymiarów|
|c2d.twin.update.failure|Liczba aktualizacji bliźniaczej reprezentacji nie powiodło się z zaplecza|Count|Łączna|Liczba wszystkich nie powiodło się aktualizacji inicjowane przez zakończenia wstecz bliźniaczej reprezentacji.|Nie wymiarów|
|c2d.twin.update.size|Rozmiar aktualizacji bliźniaczej reprezentacji z zaplecza|Bajty|Średnia|Średnia, minimum i maksymalny rozmiar wszystkich pomyślnie, zainicjowanych zakończenia wstecz bliźniaczej reprezentacji aktualizacji.|Nie wymiarów|
|twinQueries.success|Zapytania dotyczące bliźniaczych reprezentacji pomyślne|Count|Łączna|Liczba wszystkich zapytań bliźniaczych reprezentacji się pomyślnie.|Nie wymiarów|
|twinQueries.failure|Zapytania dotyczące bliźniaczych reprezentacji nie powiodło się|Count|Łączna|Liczba wszystkich zapytań bliźniaczych reprezentacji nie powiodło się.|Nie wymiarów|
|twinQueries.resultSize|Rozmiar wyników zapytań bliźniaczych reprezentacji|Bajty|Średnia|Średnia, minimum i maksymalny rozmiar wyników wszystkich zapytań bliźniaczych pomyślne.|Nie wymiarów|
|jobs.createTwinUpdateJob.success|Pomyślne utworzenie kont zadań aktualizacji bliźniaczej reprezentacji|Count|Łączna|Liczba wszystkich pomyślne utworzenie zadania aktualizacji bliźniaczej reprezentacji.|Nie wymiarów|
|jobs.createTwinUpdateJob.failure|Nie powiodło się tworzenie zadania aktualizacji bliźniaczej reprezentacji|Count|Łączna|Liczba wszystkich nie powiodło się utworzenie zadania aktualizacji bliźniaczej reprezentacji.|Nie wymiarów|
|jobs.createDirectMethodJob.success|Pomyślne utworzenie kont zadań wywołania metody|Count|Łączna|Liczba wszystkich pomyślnym utworzeniu zadania wywołania metody bezpośredniej.|Nie wymiarów|
|jobs.createDirectMethodJob.failure|Nie powiodło się tworzenie zadań wywołania metody|Count|Łączna|Liczba wszystkich nie powiodło się tworzenie zadań wywołania metody bezpośredniej.|Nie wymiarów|
|jobs.listJobs.success|Zakończonych powodzeniem wywołań do wyświetlania listy zadań|Count|Łączna|Liczba wszystkich zakończonych powodzeniem wywołań do wyświetlania listy zadań.|Nie wymiarów|
|jobs.listJobs.failure|Niepowodzenie wywołania do wyświetlania listy zadań|Count|Łączna|Liczba wszystkich wywołań zakończonych niepowodzeniem wyświetlania listy zadań.|Nie wymiarów|
|jobs.cancelJob.success|Anulowane zadania pomyślne|Count|Łączna|Liczba wszystkich pomyślnych wywołań, aby anulować zadanie.|Nie wymiarów|
|jobs.cancelJob.failure|Anulowane zadania nie powiodło się|Count|Łączna|Liczba wszystkich wywołań zakończonych niepowodzeniem można anulować zadania.|Nie wymiarów|
|jobs.queryJobs.success|Pomyślnie wykonane zadanie zapytań|Count|Łączna|Liczba wszystkich zakończonych powodzeniem wywołań do zadania kwerendy.|Nie wymiarów|
|jobs.queryJobs.failure|Nie powiodło się zadanie odpytuje|Count|Łączna|Liczba wszystkich wywołań zakończonych niepowodzeniem zadania kwerendy.|Nie wymiarów|
|Jobs.Completed|Ukończone zadania|Count|Łączna|Liczba wszystkich zakończonych zadań.|Nie wymiarów|
|Jobs.failed|Zadania zakończone niepowodzeniem|Count|Łączna|Liczba wszystkich zadań zakończonych niepowodzeniem.|Nie wymiarów|
|d2c.telemetry.ingress.sendThrottle|Liczby błędów ograniczania dostępności|Count|Łączna|Ogranicza liczby błędów ograniczania dostępności z powodu przepływności urządzenia|Nie wymiarów|
|dailyMessageQuotaUsed|Całkowita liczba komunikatów używane|Count|Średnia|Liczba całkowita liczba wiadomości używanych obecnie. Jest to wartość zbiorcza, która jest resetowany do zera UTC 00:00 każdego dnia.|Nie wymiarów|
|deviceDataUsage|Użycie danych łączna liczba urządzeń|Bajty|Łączna|Bajty przesłane do i z wszelkie urządzenia podłączone do usługi IotHub|Nie wymiarów|
|totalDeviceCount|Łączna liczba urządzeń (wersja zapoznawcza)|Count|Średnia|Liczba urządzeń zarejestrowanych do Centrum IoT hub|Nie wymiarów|
|connectedDeviceCount|Połączone urządzenia (wersja zapoznawcza)|Count|Średnia|Liczba urządzeń podłączonych do Centrum IoT hub|Nie wymiarów|
|Konfiguracje|Konfiguracja metryki|Count|Łączna|Metryki dla operacji konfiguracji|Nie wymiarów|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RegistrationAttempts|Prób rejestracji|Count|Łączna|Numer rejestracji urządzeń, które podjęto próbę|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Przypisano urządzenia|Count|Łączna|Liczba urządzeń przypisanych do usługi IoT hub|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Próby zaświadczania|Count|Łączna|Liczba poświadczenia urządzenia, które podjęto próbę|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AvailableStorage|Dostępna pamięć|Bajty|Łączna|Całkowita ilość miejsca dostępna na stopień szczegółowości 5 minut|Region CollectionName, DatabaseName,|
|CassandraConnectionClosures|Zamknięć połączenia bazy danych Cassandra|Count|Łączna|Liczba połączeń bazy danych Cassandra, które zostały zamknięte zgłoszone w szczegółowości 1 minuta|Region, ClosureReason|
|CassandraRequestCharges|Opłaty za żądania bazy danych Cassandra|Count|Łączna|RU dla żądań bazy danych Cassandra|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Żądania bazy danych Cassandra|Count|Count|Liczba żądań bazy danych Cassandra|DatabaseName CollectionName, Region, OperationType, ResourceType, kod błędu|
|DataUsage|Użycie danych|Bajty|Łączna|Łączna ilość danych użycia zgłoszonych na stopień szczegółowości 5 minut|Region CollectionName, DatabaseName,|
|DocumentCount|Liczba dokumentów|Count|Łączna|Łączną liczbę dokumentów zgłoszone w 5 minut stopień szczegółowości|Region CollectionName, DatabaseName,|
|DocumentQuota|Limit przydziału dokumentu|Bajty|Łączna|Łączny przydział pamięci masowej zgłoszone w 5 minut stopień szczegółowości|Region CollectionName, DatabaseName,|
|IndexUsage|Użycie indeksu|Bajty|Łączna|Użycie całkowita indeksu zgłoszone w 5 minut stopień szczegółowości|Region CollectionName, DatabaseName,|
|MetadataRequests|Żądania metadanych|Count|Count|Liczba żądań metadanych. Usługa cosmos DB obsługuje kolekcji metadanych systemowych dla każdego konta, które pozwala wyliczyć kolekcje, bazami danych itp i ich konfiguracji bezpłatnie.|DatabaseName, CollectionName, Region, StatusCode, |
|MongoRequestCharge|Opłata za żądanie MONGO|Count|Łączna|Wykorzystane jednostki żądania MONGO|DatabaseName CollectionName, Region, CommandName, kod błędu|
|MongoRequests|Żądania MONGO|Count|Count|Liczba żądań Mongo|DatabaseName CollectionName, Region, CommandName, kod błędu|
|ProvisionedThroughput|Aprowizowana przepływność|Count|Maksimum|Aprowizowana przepływność|DatabaseName, CollectionName|
|ReplicationLatency|Poziomie P99 Czas oczekiwania replikacji|MilliSeconds|Średnia|Poziomie P99 opóźnienie replikacji między regionami źródłowych i docelowych dla konta włączono geograficznie|SourceRegion, TargetRegion|
|ServiceAvailability|Dostępność usługi|Wartość procentowa|Średnia|Dostępność żądania konta na jedną godzinę, dzień lub miesiąc stopień szczegółowości|Nie wymiarów|
|TotalRequestUnits|Jednostki łączna liczba żądań|Count|Łączna|Używane jednostki żądania|DatabaseName, CollectionName, Region, StatusCode, OperationType|
|TotalRequests|Łączna liczba żądań|Count|Count|Liczba żądań|DatabaseName, CollectionName, Region, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PublishSuccessCount|Opublikowane zdarzenia|Count|Łączna|Łączna liczba zdarzeń opublikowane w tym temacie|Nie wymiarów|
|PublishFailCount|Publikowanie zdarzeń nie powiodło się|Count|Łączna|Łączna liczba zdarzeń, nie można opublikować w tym temacie|ErrorType, Error|
|UnmatchedEventCount|Niedopasowanych zdarzeń|Count|Łączna|Łączna liczba zdarzeń, które nie pasują dowolnej subskrypcji zdarzeń dla tego tematu|Nie wymiarów|
|PublishSuccessLatencyInMs|Publikowanie opóźnienie powodzenia|Count|Łączna|Publikowanie opóźnienie powodzenia (w milisekundach)|Nie wymiarów|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|MatchedEventCount|Dopasowane zdarzenia|Count|Łączna|Łączna liczba zdarzeń do tej subskrypcji zdarzeń|Nie wymiarów|
|DeliveryAttemptFailCount|Dostarczanie zdarzeń nie powiodło się|Count|Łączna|Łączna liczba zdarzeń, nie można dostarczyć do tej subskrypcji zdarzeń|Błąd, ErrorType|
|DeliverySuccessCount|Działanie jest gwarantowane zdarzenia|Count|Łączna|Łączna liczba zdarzeń dostarczane do tej subskrypcji zdarzeń|Nie wymiarów|
|DestinationProcessingDurationInMs|Docelowy czas przetwarzania|Milisekundy|Średnia|Czas przetwarzania docelowego (w milisekundach)|Nie wymiarów|
|DroppedEventCount|Zdarzenia porzucone|Count|Łączna|Całkowita liczba porzuconych zdarzeń pasujących do tej subskrypcji zdarzeń|DropReason|
|DeadLetteredCount|Dostępnie zdarzenia|Count|Łączna|Łączna liczba zdarzeń dostępnie dopasowany do tej subskrypcji zdarzeń|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PublishSuccessCount|Opublikowane zdarzenia|Count|Łączna|Łączna liczba zdarzeń opublikowane w tym temacie|Nie wymiarów|
|PublishFailCount|Zdarzenia nie powiodło się|Count|Łączna|Łączna liczba zdarzeń, nie można opublikować w tym temacie|ErrorType, Error|
|UnmatchedEventCount|Niedopasowanych zdarzeń|Count|Łączna|Łączna liczba zdarzeń, które nie pasują dowolnej subskrypcji zdarzeń dla tego tematu|Nie wymiarów|
|PublishSuccessLatencyInMs|Publikowanie opóźnienie powodzenia|Count|Łączna|Publikowanie opóźnienie powodzenia (w milisekundach)|Nie wymiarów|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SuccessfulRequests|Żądania zakończone powodzeniem|Count|Łączna|Żądania zakończone powodzeniem dla elementu Microsoft.EventHub.|EntityName, |
|ServerErrors|Błędy serwera.|Count|Łączna|Błędy serwera dla elementu Microsoft.EventHub.|EntityName, |
|UserErrors|Błędy użytkowników.|Count|Łączna|Błędy użytkowników dla elementu Microsoft.EventHub.|EntityName, |
|QuotaExceededErrors|Błędy przekroczenia przydziału.|Count|Łączna|Błędy przekroczenia przydziału dla elementu Microsoft.EventHub.|EntityName, |
|ThrottledRequests|Żądania ograniczone.|Count|Łączna|Żądania ograniczone dla elementu Microsoft.EventHub.|EntityName, |
|IncomingRequests|Żądania przychodzące|Count|Łączna|Żądania przychodzące dla elementu Microsoft.EventHub.|EntityName|
|IncomingMessages|Wiadomości przychodzące|Count|Łączna|Komunikaty przychodzące dla elementu Microsoft.EventHub.|EntityName|
|OutgoingMessages|Wiadomości wychodzące|Count|Łączna|Komunikaty wychodzące dla elementu Microsoft.EventHub.|EntityName|
|IncomingBytes|Bajty przychodzące.|Bajty|Łączna|Bajty przychodzące dla elementu Microsoft.EventHub.|EntityName|
|OutgoingBytes|Bajty wychodzące.|Bajty|Łączna|Bajty wychodzące dla elementu Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Count|Średnia|Całkowita liczba aktywnych połączeń dla elementu Microsoft.EventHub.|Nie wymiarów|
|ConnectionsOpened|Połączenia otwarte.|Count|Średnia|Połączenia otwarte dla elementu Microsoft.EventHub.|EntityName|
|ConnectionsClosed|Połączenia zamknięte.|Count|Średnia|Połączenia zamknięte dla elementu Microsoft.EventHub.|EntityName|
|CaptureBacklog|Lista prac funkcji Capture.|Count|Łączna|Lista prac funkcji Capture dla elementu Microsoft.EventHub.|EntityName|
|CapturedMessages|Przechwycone komunikaty.|Count|Łączna|Przechwycone komunikaty dla elementu Microsoft.EventHub.|EntityName|
|CapturedBytes|Przechwycone bajty.|Bajty|Łączna|Przechwycone bajty dla elementu Microsoft.EventHub.|EntityName|
|Rozmiar|Rozmiar|Bajty|Średnia|Rozmiar Centrum zdarzeń w bajtach.|EntityName|
|INREQS|Żądania przychodzące (przestarzałe)|Count|Łączna|Łączna liczba żądań wysłania dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|SUCCREQ|Żądania zakończone powodzeniem (przestarzałe)|Count|Łączna|Łączna liczba udanych żądań dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|FAILREQ|Żądania zakończone niepowodzeniem (przestarzałe)|Count|Łączna|Łączna liczba nieudanych żądań dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|SVRBSY|Błędy typu serwer zajęty (przestarzałe)|Count|Łączna|Łączna liczba zajętych błędów serwera dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|INTERR|Błędy wewnętrzne serwera (przestarzałe)|Count|Łączna|Łączna liczba wewnętrznych błędów serwera dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|MISCERR|Inne błędy (przestarzałe)|Count|Łączna|Łączna liczba nieudanych żądań dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|INMSGS|Komunikaty przychodzące (przestarzałe) (przestarzałe)|Count|Łączna|Łączna liczba komunikatów przychodzących dla przestrzeni nazw. Ta metryka jest przestarzała. Użyj zamiast niej metryki komunikaty przychodzące (przestarzałe)|Nie wymiarów|
|EHINMSGS|Komunikaty przychodzące (przestarzałe)|Count|Łączna|Łączna liczba komunikatów przychodzących dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|OUTMSGS|Komunikaty wychodzące (przestarzałe) (przestarzałe)|Count|Łączna|Łączna liczba komunikatów wychodzących dla przestrzeni nazw. Ta metryka jest przestarzała. Użyj zamiast niej metryki komunikaty wychodzące (przestarzałe)|Nie wymiarów|
|EHOUTMSGS|Komunikaty wychodzące (przestarzałe)|Count|Łączna|Łączna liczba komunikatów wychodzących dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|EHINMBS|Bajty przychodzące (przestarzałe) (przestarzałe)|Bajty|Łączna|Event Hub przepływność komunikatów przychodzących dla przestrzeni nazw. Ta metryka jest przestarzała. Użyj zamiast niej metryki Bajty przychodzące (przestarzałe)|Nie wymiarów|
|EHINBYTES|Bajty przychodzące (przestarzałe)|Bajty|Łączna|Event Hub przepływność komunikatów przychodzących dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|EHOUTMBS|Bajty wychodzące (przestarzałe) (przestarzałe)|Bajty|Łączna|Event Hub przepływność komunikatów wychodzących dla przestrzeni nazw. Ta metryka jest przestarzała. Użyj zamiast niej metryki bajty wychodzące (przestarzałe)|Nie wymiarów|
|EHOUTBYTES|Bajty wychodzące (przestarzałe)|Bajty|Łączna|Zdarzenia Centrum przepływność komunikatów wychodzących dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|EHABL|Archiwizuj komunikaty listy prac (przestarzałe)|Count|Łączna|Zarchiwizowane komunikaty Centrum zdarzeń na liście prac dla przestrzeni nazw (przestarzałe)|Nie wymiarów|
|EHAMSGS|Archiwum wiadomości (przestarzałe)|Count|Łączna|Centrum zdarzeń zarchiwizowane komunikaty w przestrzeni nazw (przestarzałe)|Nie wymiarów|
|EHAMBS|Przepływność archiwizowania komunikatów (przestarzałe)|Bajty|Łączna|Przepływność zarchiwizowanych komunikatów Centrum zdarzeń w przestrzeni nazw (przestarzałe)|Nie wymiarów|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SuccessfulRequests|Liczba pomyślnych żądań (wersja zapoznawcza)|Count|Łączna|Żądania zakończone powodzeniem dla elementu Microsoft.EventHub. (wersja zapoznawcza)|Nie wymiarów|
|ServerErrors|Błędy serwera. (wersja zapoznawcza)|Count|Łączna|Błędy serwera dla elementu Microsoft.EventHub. (wersja zapoznawcza)|Nie wymiarów|
|UserErrors|Błędy użytkowników. (wersja zapoznawcza)|Count|Łączna|Błędy użytkowników dla elementu Microsoft.EventHub. (wersja zapoznawcza)|Nie wymiarów|
|QuotaExceededErrors|Błędy przekroczenia przydziału. (wersja zapoznawcza)|Count|Łączna|Błędy przekroczenia przydziału dla elementu Microsoft.EventHub. (wersja zapoznawcza)|Nie wymiarów|
|ThrottledRequests|Żądania ograniczone. (wersja zapoznawcza)|Count|Łączna|Żądania ograniczone dla elementu Microsoft.EventHub. (wersja zapoznawcza)|Nie wymiarów|
|IncomingRequests|Przychodzące żądania (wersja zapoznawcza)|Count|Łączna|Żądania przychodzące dla elementu Microsoft.EventHub. (wersja zapoznawcza)|Nie wymiarów|
|IncomingMessages|Komunikaty przychodzące (wersja zapoznawcza)|Count|Łączna|Komunikaty przychodzące dla elementu Microsoft.EventHub. (wersja zapoznawcza)|Nie wymiarów|
|OutgoingMessages|Komunikaty wychodzące (wersja zapoznawcza)|Count|Łączna|Komunikaty wychodzące dla elementu Microsoft.EventHub. (wersja zapoznawcza)|Nie wymiarów|
|IncomingBytes|Bajty przychodzące. (wersja zapoznawcza)|Bajty|Łączna|Bajty przychodzące dla elementu Microsoft.EventHub. (wersja zapoznawcza)|Nie wymiarów|
|OutgoingBytes|Bajty wychodzące. (wersja zapoznawcza)|Bajty|Łączna|Bajty wychodzące dla elementu Microsoft.EventHub. (wersja zapoznawcza)|Nie wymiarów|
|ActiveConnections|Połączeń ActiveConnections (wersja zapoznawcza)|Count|Średnia|Całkowita liczba aktywnych połączeń dla elementu Microsoft.EventHub. (wersja zapoznawcza)|Nie wymiarów|
|ConnectionsOpened|Połączenia otwarte. (wersja zapoznawcza)|Count|Średnia|Połączenia otwarte dla elementu Microsoft.EventHub. (wersja zapoznawcza)|Nie wymiarów|
|ConnectionsClosed|Połączenia zamknięte. (wersja zapoznawcza)|Count|Średnia|Połączenia zamknięte dla elementu Microsoft.EventHub. (wersja zapoznawcza)|Nie wymiarów|
|CaptureBacklog|Lista prac funkcji Capture. (wersja zapoznawcza)|Count|Łączna|Lista prac funkcji Capture dla elementu Microsoft.EventHub. (wersja zapoznawcza)|Nie wymiarów|
|CapturedMessages|Przechwycone komunikaty. (wersja zapoznawcza)|Count|Łączna|Przechwycone komunikaty dla elementu Microsoft.EventHub. (wersja zapoznawcza)|Nie wymiarów|
|CapturedBytes|Przechwycone bajty. (wersja zapoznawcza)|Bajty|Łączna|Przechwycone bajty dla elementu Microsoft.EventHub. (wersja zapoznawcza)|Nie wymiarów|
|CPU|Procesor (wersja zapoznawcza)|Wartość procentowa|Maksimum|Użycie procesora CPU dla klastra Centrum zdarzeń w procentach|Rola|
|AvailableMemory|Dostępna pamięć (wersja zapoznawcza)|Count|Maksimum|Dostępna pamięć dla klastra Centrum zdarzeń w bajtach|Rola|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|GatewayRequests|Żądania bramy|Count|Łączna|Liczba żądań bramy|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Żądania bramy skategoryzowane|Count|Łączna|Liczba żądań bramy według kategorii (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|
|Skalowanie automatyczne|Metryki automatycznego skalowania|Count|Maksimum|Metryki automatycznego skalowania|ClusterDnsName, MetricName|
|AllocatedMB|Liczba przydzielonych MB|Count|Maksimum|Liczba przydzielonych MB|ClusterDnsName, MetricName|
|AvailableMB|Liczba dostępnych MB|Count|Maksimum|Liczba dostępnych MB|ClusterDnsName, MetricName|
|AppsPending|Oczekujące aplikacje|Count|Maksimum|Oczekujące aplikacje|ClusterDnsName, MetricName|
|AppsRunning|Aplikacje działające|Count|Maksimum|Aplikacje działające|ClusterDnsName, MetricName|
|AppsSubmitted|Przesłane aplikacje|Count|Maksimum|Przesłane aplikacje|ClusterDnsName, MetricName|
|NumActiveWorkers|Liczba aktywnych procesów roboczych|Count|Maksimum|Liczba aktywnych procesów roboczych|ClusterDnsName, MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ObservedMetricValue|Zaobserwowana wartość metryki|Count|Średnia|Wartość obliczona przez autoskalowanie podczas wykonywania|MetricTriggerSource|
|MetricThreshold|Próg metryki|Count|Średnia|Skonfigurowany próg autoskalowania podczas uruchamiania autoskalowania.|MetricTriggerRule|
|ObservedCapacity|Zaobserwowana wydajność|Count|Średnia|Wydajność zgłoszona do autoskalowania podczas jego wykonywania.|Nie wymiarów|
|ScaleActionsInitiated|Zainicjowane akcje skalowania|Count|Łączna|Kierunek operacji skalowania.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(Publiczna wersja zapoznawcza)

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Dostępność|Procent|Średnia|Procent pomyślnie ukończonych testów dostępności|availabilityResult/name availabilityResult/lokalizacja|
|availabilityResults/count|Testy dostępności|Count|Count|Liczba testów dostępności|availabilityResult/name, availabilityResult/lokalizacji, availabilityResult/Powodzenie|
|availabilityResults/czas trwania|Czas trwania testu dostępności|MilliSeconds|Średnia|Czas trwania testu dostępności|availabilityResult/name, availabilityResult/lokalizacji, availabilityResult/Powodzenie|
|browserTimings/networkDuration|Czas połączenia sieciowego podczas ładowania strony|MilliSeconds|Średnia|Czas od użytkownika żądania i połączenie sieciowe. Obejmuje DNS wyszukiwania i połączenie transportu.|Nie wymiarów|
|browserTimings/processingDuration|Czas przetwarzania klienta|MilliSeconds|Średnia|Czas między odebraniem ostatniego bajtu dokumentu, do momentu załadowania modelu DOM. Żądania asynchroniczne mogą nadal być przetwarzane.|Nie wymiarów|
|browserTimings/receiveDuration|Czas odpowiedzi odbierania|MilliSeconds|Średnia|Czas między pierwszym i ostatnim bajtem lub czas do zakończenia połączenia.|Nie wymiarów|
|browserTimings/sendDuration|Czas żądania wysyłania|MilliSeconds|Średnia|Czas między nawiązaniem połączenia sieciowego i odebraniem pierwszego bajtu.|Nie wymiarów|
|browserTimings/totalDuration|Czas ładowania strony przeglądarki|MilliSeconds|Średnia|Czas od wysłania żądania użytkownika do załadowania modelu DOM, arkuszy stylów, skryptów i obrazów.|Nie wymiarów|
|zależności/liczby|Wywołania zależności|Count|Count|Liczba wywołań zasobów zewnętrznych wykonanych przez aplikację.|zależności/typu zależności/performanceBucket, zależności/Powodzenie, operacji/syntetycznych, chmura/roleInstance, chmury/roleName|
|zależności/czas trwania|Czas trwania zależności|MilliSeconds|Średnia|Czas trwania wywołań zasobów zewnętrznych wykonanych przez aplikację.|zależności/typu zależności/performanceBucket, zależności/Powodzenie, operacji/syntetycznych, chmura/roleInstance, chmury/roleName|
|zależności lub nie powiodła się|Błędy wywołań zależności|Count|Count|Liczba wywołań zależności zasobów zewnętrznych zakończonych niepowodzeniem wykonanych przez aplikację.|Typ zależności/zależności/performanceBucket, operacji/syntetycznych, chmury/roleInstance, chmury/roleName|
|Liczba/wyświetleń stron|Wyświetlenia strony|Count|Count|Liczba wyświetleń strony.|Operacja/syntetycznych|
|wyświetleń stron/czas trwania|Wyświetlenie strony — czas ładowania|MilliSeconds|Średnia|Wyświetlenie strony — czas ładowania|Operacja/syntetycznych|
|performanceCounters/requestExecutionTime|Czas wykonywania żądania HTTP|MilliSeconds|Średnia|Czas wykonywania najnowszego żądania.|cloud/roleInstance|
|performanceCounters/requestsInQueue|Żądania HTTP w kolejce aplikacji|Count|Średnia|Długość kolejki żądań aplikacji.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Liczba żądań HTTP|CountPerSecond|Średnia|Liczba wszystkich żądań wysłanych do aplikacji z platformy ASP.NET na sekundę.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Częstotliwość występowania wyjątków|CountPerSecond|Średnia|Liczba obsługiwanych i nieobsługiwanych wyjątków zgłoszonych w systemie Windows, łącznie z wyjątkami platformy .NET i niezarządzanymi wyjątkami przekonwertowanymi na wyjątki platformy .NET.|cloud/roleInstance|
|liczniki wydajności/processIOBytesPerSecond|Liczba operacji we/wy procesu|BytesPerSecond|Średnia|Łączna liczba bajtów odczytanych i zapisanych w ciągu sekundy w plikach i sieci oraz na urządzeniach.|cloud/roleInstance|
|liczniki wydajności/processCpuPercentage|Procesy — procesor CPU|Wartość procentowa|Średnia|Procent minionego czasu wszystkich wątków procesów używały procesora w celu wykonania instrukcji. To może się różnić od 0 do 100. Ta Metryka wskazuje wydajność samego procesu w3wp.|cloud/roleInstance|
|liczniki wydajności/processorCpuPercentage|Czas procesora|Wartość procentowa|Średnia|Procent czasu poświęconego przez procesor na aktywne wątki.|cloud/roleInstance|
|liczniki wydajności/memoryAvailableBytes|Dostępna pamięć|Bajty|Średnia|Pamięć fizyczna dostępna natychmiast do przydzielenia do procesu lub do użycia przez system.|cloud/roleInstance|
|liczniki wydajności/processPrivateBytes|Prywatne bajty procesu|Bajty|Średnia|Pamięć przypisana wyłącznie do procesów monitorowanej aplikacji.|cloud/roleInstance|
|żądania/czas trwania|Czas odpowiedzi serwera|MilliSeconds|Średnia|Czas między odebraniem żądania HTTP i zakończeniem wysyłania odpowiedzi.|żądanie/performanceBucket żądania/resultCode, operacji/syntetycznych, chmura/roleInstance, Powodzenie/na żądanie, chmury/roleName|
|żądania/liczby|Żądania serwera|Count|Count|Liczba ukończonych żądań HTTP.|żądanie/performanceBucket żądania/resultCode, operacji/syntetycznych, chmura/roleInstance, Powodzenie/na żądanie, chmury/roleName|
|requests/failed|Żądania zakończone niepowodzeniem|Count|Count|Liczba HTTP żądania oznaczone jako zakończone niepowodzeniem. W większości przypadków są to żądania z kodem odpowiedzi > = 400 i innym niż 401.|żądanie/performanceBucket żądania/resultCode, operacji/syntetycznych, chmura/roleInstance, chmury/roleName|
|requests/rate|Liczba żądań serwera|CountPerSecond|Średnia|Liczba żądań serwera, na sekundę|żądanie/performanceBucket żądania/resultCode, operacji/syntetycznych, chmura/roleInstance, Powodzenie/na żądanie, chmury/roleName|
|Wyjątki/liczby|Wyjątki|Count|Count|Łączna liczba wszystkich nieprzechwyconych wyjątków.|Chmura/roleName, chmura/roleInstance, typu/klienta|
|Wyjątki/przeglądarki|Wyjątki przeglądarki|Count|Count|Liczba nieprzechwyconych wyjątków zgłoszonych w przeglądarce.|Nie wymiarów|
|Wyjątki serwera|Wyjątki serwera|Count|Count|Liczba nieprzechwyconych wyjątków zgłoszonych w aplikacji serwera.|Chmura/roleName, chmura/roleInstance|
|dane śledzenia/liczby|Ślady|Count|Count|Liczba dokumentów śladów|trace/severityLevel, operacji/syntetycznych, roleName/chmura, chmura/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ServiceApiHit|Całkowita liczba trafień interfejsu API usługi|Count|Count|Całkowita liczba trafień interfejsu API usługi|ActivityType, ActivityName|
|ServiceApiLatency|Ogólny czas oczekiwania żądań interfejsu API usługi|Milisekundy|Średnia|Ogólny czas oczekiwania żądań interfejsu API usługi|Właściwości ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Całkowita liczba wyników interfejsu API usługi|Count|Count|Całkowita liczba wyników interfejsu API usługi|Właściwości ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ClusterDataCapacityFactor|Wykorzystanie pamięci podręcznej|Wartość procentowa|Średnia|Poziom użycia w zakresie klastra|Nie wymiarów|
|QueryDuration|Czas trwania zapytania|Milisekundy|Średnia|Zapytania czas w sekundach|QueryStatus|
|IngestionsLoadFactor|Wykorzystanie pozyskiwania|Wartość procentowa|Średnia|Współczynnik miejsc pozyskiwania używanych w klastrze|Nie wymiarów|
|IsEngineAnsweringQuery|Podtrzymanie|Count|Średnia|Poprawnością wyboru wskazuje, że klastra odpowiada na kwerendy|Nie wymiarów|
|IngestCommandOriginalSizeInMb|Pozyskiwanie woluminu (w MB)|Count|Łączna|Łączna ilość pozyskiwanych danych do klastra (w MB)|Nie wymiarów|
|IngestedEventAgeSeconds|Opóźnienia w pozyskiwaniu danych (w sekundach)|Sekundy|Średnia|Czas wprowadzania od źródła (np. komunikat jest w Centrum zdarzeń) do klastra w ciągu kilku sekund|Nie wymiarów|
|EventRecievedFromEventHub|Zdarzenia przetwarzane (dla usługi Event Hubs)|Count|Łączna|Liczba zdarzeń przetwarzany przez klaster, gdy wprowadzane z Centrum zdarzeń|Nie wymiarów|
|IngestionResult|Wynik pozyskiwania|Count|Count|Liczby operacji pozyskiwania|IngestionResultDetails|
|EngineCPU|CPU|Wartość procentowa|Średnia|Poziom użycia procesora CPU|Nie wymiarów|

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Sposób użycia|Sposób użycia|Count|Count|Liczba z interfejsu API|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RunsStarted|Przebiegi rozpoczęte|Count|Łączna|Liczba rozpoczętych przebiegów przepływu pracy.|Nie wymiarów|
|RunsCompleted|Przebiegi ukończone|Count|Łączna|Liczba ukończonych przebiegów przepływu pracy.|Nie wymiarów|
|RunsSucceeded|Przebiegi zakończone powodzeniem|Count|Łączna|Liczba przebiegów przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|RunsFailed|Przebiegi zakończone niepowodzeniem|Count|Łączna|Liczba przebiegów przepływu pracy zakończonych niepowodzeniem.|Nie wymiarów|
|RunsCancelled|Przebiegi anulowane|Count|Łączna|Liczba przebiegów przepływu pracy anulowane.|Nie wymiarów|
|RunLatency|Opóźnienie przebiegów|Sekundy|Średnia|Opóźnienie ukończonych przebiegów przepływu pracy.|Nie wymiarów|
|RunSuccessLatency|Opóźnienie przebiegów zakończonych powodzeniem|Sekundy|Średnia|Opóźnienie przebiegów przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|RunThrottledEvents|Zdarzenia ograniczenia przebiegu|Count|Łączna|Liczba zdarzeń ograniczenia akcji lub wyzwalacza przepływu pracy.|Nie wymiarów|
|RunFailurePercentage|Procent niepowodzeń przebiegów|Wartość procentowa|Łączna|Procent przebiegów przepływu pracy zakończonych niepowodzeniem.|Nie wymiarów|
|ActionsStarted|Rozpoczęte akcje |Count|Łączna|Liczba rozpoczętych akcji przepływu pracy.|Nie wymiarów|
|ActionsCompleted|Ukończone akcje |Count|Łączna|Liczba ukończonych akcji przepływu pracy.|Nie wymiarów|
|ActionsSucceeded|Akcje zakończone powodzeniem |Count|Łączna|Liczba akcji przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|ActionsFailed|Akcje zakończone niepowodzeniem|Count|Łączna|Liczba akcji przepływu pracy zakończonych niepowodzeniem.|Nie wymiarów|
|ActionsSkipped|Pominięte akcje |Count|Łączna|Liczba pominiętych akcji przepływu pracy.|Nie wymiarów|
|ActionLatency|Opóźnienie akcji |Sekundy|Średnia|Opóźnienie ukończonych akcji przepływu pracy.|Nie wymiarów|
|ActionSuccessLatency|Opóźnienie akcji – powodzenie |Sekundy|Średnia|Opóźnienie akcji przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|ActionThrottledEvents|Zdarzenia ograniczenia akcji|Count|Łączna|Liczba zdarzeń ograniczenia akcji przepływu pracy.|Nie wymiarów|
|TriggersStarted|Rozpoczęte wyzwalacze |Count|Łączna|Liczba rozpoczętych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggersCompleted|Ukończone wyzwalacze |Count|Łączna|Liczba ukończonych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggersSucceeded|Udane wyzwalacze |Count|Łączna|Liczba wyzwalaczy przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|TriggersFailed|Nieudane wyzwalacze |Count|Łączna|Liczba wyzwalaczy przepływu pracy zakończonych niepowodzeniem.|Nie wymiarów|
|TriggersSkipped|Pominięte wyzwalacze|Count|Łączna|Liczba pominiętych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggersFired|Uruchomione wyzwalacze |Count|Łączna|Liczba uaktywnionych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggerLatency|Opóźnienie wyzwalacza |Sekundy|Średnia|Opóźnienie ukończonych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggerFireLatency|Opóźnienie uruchomienia wyzwalacza |Sekundy|Średnia|Opóźnienie uaktywnionych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggerSuccessLatency|Opóźnienie udanego wyzwalacza |Sekundy|Średnia|Opóźnienie wyzwalaczy przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|TriggerThrottledEvents|Zdarzenia ograniczenia wyzwalacza|Count|Łączna|Liczba zdarzeń ograniczenia wyzwalacza przepływu pracy.|Nie wymiarów|
|BillableActionExecutions|Rozliczane wykonania akcji|Count|Łączna|Liczba rozliczanych wykonań akcji przepływu pracy.|Nie wymiarów|
|BillableTriggerExecutions|Rozliczane wykonania wyzwalaczy|Count|Łączna|Liczba rozliczanych wykonań wyzwalaczy przepływu pracy.|Nie wymiarów|
|TotalBillableExecutions|Łączna liczba rozliczanych wykonań|Count|Łączna|Liczba rozliczanych wykonań przepływu pracy.|Nie wymiarów|
|BillingUsageNativeOperation|Użycie rozliczeń dla wykonań operacji macierzystych|Count|Łączna|Liczba rozliczanych wykonań natywnych operacji.|Nie wymiarów|
|BillingUsageStandardConnector|Użycie rozliczeń dla wykonań łącznika standardowego|Count|Łączna|Liczba rozliczanych wykonań łączników standardowych.|Nie wymiarów|
|BillingUsageStorageConsumption|Użycie rozliczeń dla wykonań wykorzystania magazynu|Count|Łączna|Liczba rozliczanych wykonań wykorzystania magazynu.|Nie wymiarów|
|BillingUsageNativeOperation|Użycie rozliczeń dla wykonań operacji macierzystych|Count|Łączna|Liczba rozliczanych wykonań natywnych operacji.|Nie wymiarów|
|BillingUsageStandardConnector|Użycie rozliczeń dla wykonań łącznika standardowego|Count|Łączna|Liczba rozliczanych wykonań łączników standardowych.|Nie wymiarów|
|BillingUsageStorageConsumption|Użycie rozliczeń dla wykonań wykorzystania magazynu|Count|Łączna|Liczba rozliczanych wykonań wykorzystania magazynu.|Nie wymiarów|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RunsStarted|Przebiegi rozpoczęte|Count|Łączna|Liczba rozpoczętych przebiegów przepływu pracy.|Nie wymiarów|
|RunsCompleted|Przebiegi ukończone|Count|Łączna|Liczba ukończonych przebiegów przepływu pracy.|Nie wymiarów|
|RunsSucceeded|Przebiegi zakończone powodzeniem|Count|Łączna|Liczba przebiegów przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|RunsFailed|Przebiegi zakończone niepowodzeniem|Count|Łączna|Liczba przebiegów przepływu pracy zakończonych niepowodzeniem.|Nie wymiarów|
|RunsCancelled|Przebiegi anulowane|Count|Łączna|Liczba przebiegów przepływu pracy anulowane.|Nie wymiarów|
|RunLatency|Opóźnienie przebiegów|Sekundy|Średnia|Opóźnienie ukończonych przebiegów przepływu pracy.|Nie wymiarów|
|RunSuccessLatency|Opóźnienie przebiegów zakończonych powodzeniem|Sekundy|Średnia|Opóźnienie przebiegów przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|RunThrottledEvents|Zdarzenia ograniczenia przebiegu|Count|Łączna|Liczba zdarzeń ograniczenia akcji lub wyzwalacza przepływu pracy.|Nie wymiarów|
|RunStartThrottledEvents|Zdarzenia ograniczenia rozpoczęcia przebiegu|Count|Łączna|Liczba zdarzeń ograniczenia rozpoczęcia przebiegu przepływu pracy.|Nie wymiarów|
|RunFailurePercentage|Procent niepowodzeń przebiegów|Wartość procentowa|Łączna|Procent przebiegów przepływu pracy zakończonych niepowodzeniem.|Nie wymiarów|
|ActionsStarted|Rozpoczęte akcje |Count|Łączna|Liczba rozpoczętych akcji przepływu pracy.|Nie wymiarów|
|ActionsCompleted|Ukończone akcje |Count|Łączna|Liczba ukończonych akcji przepływu pracy.|Nie wymiarów|
|ActionsSucceeded|Akcje zakończone powodzeniem |Count|Łączna|Liczba akcji przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|ActionsFailed|Akcje zakończone niepowodzeniem |Count|Łączna|Liczba akcji przepływu pracy zakończonych niepowodzeniem.|Nie wymiarów|
|ActionsSkipped|Pominięte akcje |Count|Łączna|Liczba pominiętych akcji przepływu pracy.|Nie wymiarów|
|ActionLatency|Opóźnienie akcji |Sekundy|Średnia|Opóźnienie ukończonych akcji przepływu pracy.|Nie wymiarów|
|ActionSuccessLatency|Opóźnienie akcji – powodzenie |Sekundy|Średnia|Opóźnienie akcji przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|ActionThrottledEvents|Zdarzenia ograniczenia akcji|Count|Łączna|Liczba zdarzeń ograniczenia akcji przepływu pracy.|Nie wymiarów|
|TriggersStarted|Rozpoczęte wyzwalacze |Count|Łączna|Liczba rozpoczętych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggersCompleted|Ukończone wyzwalacze |Count|Łączna|Liczba ukończonych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggersSucceeded|Udane wyzwalacze |Count|Łączna|Liczba wyzwalaczy przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|TriggersFailed|Nieudane wyzwalacze |Count|Łączna|Liczba wyzwalaczy przepływu pracy zakończonych niepowodzeniem.|Nie wymiarów|
|TriggersSkipped|Pominięte wyzwalacze|Count|Łączna|Liczba pominiętych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggersFired|Uruchomione wyzwalacze |Count|Łączna|Liczba uaktywnionych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggerLatency|Opóźnienie wyzwalacza |Sekundy|Średnia|Opóźnienie ukończonych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggerFireLatency|Opóźnienie uruchomienia wyzwalacza |Sekundy|Średnia|Opóźnienie uaktywnionych wyzwalaczy przepływu pracy.|Nie wymiarów|
|TriggerSuccessLatency|Opóźnienie udanego wyzwalacza |Sekundy|Średnia|Opóźnienie wyzwalaczy przepływu pracy zakończonych powodzeniem.|Nie wymiarów|
|TriggerThrottledEvents|Zdarzenia ograniczenia wyzwalacza|Count|Łączna|Liczba zdarzeń ograniczenia wyzwalacza przepływu pracy.|Nie wymiarów|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Użycie procesora przez przepływ pracy na potrzeby środowiska usługi integracji|Wartość procentowa|Średnia|Użycie procesora przez przepływ pracy na potrzeby środowiska usługi integracji.|Nie wymiarów|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Użycie pamięci przez przepływ pracy na potrzeby środowiska usługi integracji|Wartość procentowa|Średnia|Użycie pamięci przez przepływ pracy na potrzeby środowiska usługi integracji.|Nie wymiarów|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Użycie procesora przez łącznik na potrzeby środowiska usługi integracji|Wartość procentowa|Średnia|Użycie procesora przez łącznik na potrzeby środowiska usługi integracji.|Nie wymiarów|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Użycie pamięci przez łącznik na potrzeby środowiska usługi integracji|Wartość procentowa|Średnia|Użycie pamięci przez łącznik na potrzeby środowiska usługi integracji.|Nie wymiarów|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Zakończone przebiegi|Zakończone przebiegi|Count|Łączna|Liczba przebiegów zakończonych pomyślnie dla tego obszaru roboczego|Scenariusz|
|Przebiegi rozpoczęte|Przebiegi rozpoczęte|Count|Łączna|Liczba przebiegów wprowadzenie dla tego obszaru roboczego|Scenariusz|
|Nieudane uruchomienia|Nieudane uruchomienia|Count|Łączna|Liczba przebiegów zakończonych niepowodzeniem dla tego obszaru roboczego|Scenariusz|

## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Sposób użycia|Sposób użycia|Count|Count|Liczba z interfejsu API|ApiCategory, ApiName, ResultType, ResponseCode|
|Dostępność|Dostępność|Procent|Średnia|Dostępność interfejsów API|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AverageOtherLatency|Średnie opóźnienie innych|ms/op|Średnia|Średni innych czas oczekiwania (który nie jest odczyt lub zapis) w MS na operację|Nie wymiarów|
|AverageReadLatency|Średnia, opóźnienie odczytu|ms/op|Średnia|Średni czas oczekiwania (w milisekundach) dla operacji odczytu|Nie wymiarów|
|AverageTotalLatency|Średni całkowity czas oczekiwania|ms/op|Średnia|Średnie opóźnienie całkowite w MS na operację|Nie wymiarów|
|AverageWriteLatency|Opóźnienie zapisu średni|ms/op|Średnia|Opóźnienie zapisu średnia w MS na operację|Nie wymiarów|
|FilesystemOtherOps|System plików innych operacjach|Platforma OPS|Średnia|Liczba plików inne operacje (nie jest odczyt lub zapis)|Nie wymiarów|
|FilesystemReadOps|Operacje odczytu dla systemu plików|Platforma OPS|Średnia|Liczba plików, operacje odczytu|Nie wymiarów|
|FilesystemTotalOps|Łączna liczba operacji systemu plików|Platforma OPS|Średnia|Suma wszystkich operacji systemu plików|Nie wymiarów|
|FilesystemWriteOps|Operacje zapisu systemu plików|Platforma OPS|Średnia|Liczba operacji zapisu w systemie plików|Nie wymiarów|
|IoBytesPerOtherOps|Bajty We/Wy na innych operacjach|Bajty/op|Średnia|Numer elementu w pionie i w poziomie bajtów na inne operacje (nie jest odczyt lub zapis)|Nie wymiarów|
|IoBytesPerReadOps|Bajty We/Wy na operacje odczytu|Bajty/op|Średnia|Liczba bajtów na operację odczytu We/Wy|Nie wymiarów|
|IoBytesPerTotalOps|Bajty We/Wy na op we wszystkich operacjach|Bajty/op|Średnia|Suma wszystkich we/wy operacji bajtów|Nie wymiarów|
|IoBytesPerWriteOps|Bajty We/Wy dla operacji zapisu|Bajty/op|Średnia|Numer elementu w pionie i w poziomie bajtów na operację zapisu|Nie wymiarów|
|OtherIops|Inne operacje We/Wy|operacje na sekundę|Średnia|Inne we/wy operacji na sekundę|Nie wymiarów|
|OtherThroughput|Inne przepływności|MB/s|Średnia|Inne przepływności (nie jest odczyt lub zapis) w MB na sekundę|Nie wymiarów|
|ReadIops|Operacje odczytu We/Wy|operacje na sekundę|Średnia|Odczyt/Ściemnianie operacji na sekundę|Nie wymiarów|
|ReadThroughput|Przepływność odczytu|MB/s|Średnia|Przeczytaj przepustowość w MB na sekundę|Nie wymiarów|
|TotalIops|Łączna liczba operacji We/Wy|operacje na sekundę|Średnia|Suma wszystkich we/wy operacji na sekundę|Nie wymiarów|
|TotalThroughput|Łączna przepływność|MB/s|Średnia|Suma wszystkich przepustowość w MB na sekundę|Nie wymiarów|
|VolumeAllocatedSize|Przydzielony rozmiar woluminu|B|Średnia|Przydzielony rozmiar woluminu (nie rzeczywiste używane w bajtach)|Nie wymiarów|
|VolumeLogicalSize|Rozmiar logiczny woluminu|B|Średnia|Rozmiar logiczny woluminu (używanych w bajtach)|Nie wymiarów|
|VolumeSnapshotSize|Rozmiar migawki woluminu|B|Średnia|Rozmiar wszystkich migawek w woluminie|Nie wymiarów|
|WriteIops|Operacje We/Wy zapisu|operacje na sekundę|Średnia|Zapisu We/Wy operacji na sekundę|Nie wymiarów|
|WriteThroughput|Przepływność zapisu|MB/s|Średnia|Zapisywanie przepustowość w MB na sekundę|Nie wymiarów|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Rozmiar puli przydzielona woluminu|B|Średnia|Przydzielony rozmiar puli (nie rzeczywiste używane w bajtach)|Nie wymiarów|
|VolumePoolAllocatedUsed|Pula woluminów przydzielonych używane|B|Średnia|Przydzielony rozmiar używane w puli|Nie wymiarów|
|VolumePoolTotalLogicalSize|Całkowity rozmiar logiczny woluminu puli|B|Średnia|Suma rozmiaru logicznego wszystkich woluminów należących do puli|Nie wymiarów|
|VolumePoolTotalSnapshotSize|Rozmiar migawki całkowitej puli woluminu|B|Średnia|Suma wszystkich migawek w puli|Nie wymiarów|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BytesSentRate|Bajty wysłane|Count|Łączna|Liczba bajtów wysyłane interfejsu sieciowego|Nie wymiarów|
|BytesReceivedRate|Odebrane bajty|Count|Łączna|Liczba bajtów odebranych interfejsu sieciowego|Nie wymiarów|
|PacketsSentRate|Pakiety wysłane|Count|Łączna|Liczba pakietów interfejsu sieciowego, wysłane|Nie wymiarów|
|PacketsReceivedRate|Odebrane pakiety|Count|Łączna|Liczba pakietów na interfejsie sieciowym otrzymane|Nie wymiarów|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|VipAvailability|Dostępność ścieżki danych|Count|Średnia|Średnia dostępność ścieżki danych modułu równoważenia obciążenia na czas trwania|FrontendIPAddress, FrontendPort|
|DipAvailability|Stan sondy kondycji|Count|Średnia|Średnia stan sondy kondycji modułu równoważenia obciążenia na czas trwania|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Liczba bajtów|Count|Łączna|Całkowita liczba bajtów przesłanych w przedziale czasu|Kierunek FrontendIPAddress, FrontendPort,|
|PacketCount|Liczba pakietów|Count|Łączna|Całkowita liczba pakietów przesyłanych w przedziale czasu|Kierunek FrontendIPAddress, FrontendPort,|
|SYNCount|Liczba SYN|Count|Łączna|Całkowita liczba pakietów SYN przesyłane w przedziale czasu|Kierunek FrontendIPAddress, FrontendPort,|
|SnatConnectionCount|Liczba połączeń SNAT|Count|Łączna|Całkowita liczba nowych połączeń SNAT utworzone w przedziale czasu|Element ConnectionState FrontendIPAddress, BackendIPAddress,|
|AllocatedSnatPorts|Przydzielonych portów SNAT (wersja zapoznawcza)|Count|Łączna|Całkowita liczba portów SNAT przydzielone w przedziale czasu|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Używane porty SNAT (wersja zapoznawcza)|Count|Łączna|Całkowita liczba portów SNAT używane w przedziale czasu|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QueryVolume|Wolumin zapytań|Count|Łączna|Liczba zapytań dla strefy DNS|Nie wymiarów|
|RecordSetCount|Liczba zestawu rekordów|Count|Maksimum|Liczba zestawów rekordów w strefie DNS|Nie wymiarów|
|RecordSetCapacityUtilization|Wykorzystanie pojemności zestawu rekordów|Wartość procentowa|Maksimum|Wartość procentowa pojemności zestawu rekordów, wykorzystywany przez strefę DNS|Nie wymiarów|

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
|VipAvailability|Dostępność ścieżki danych|Count|Średnia|Średnia dostępność adresu IP na czas trwania|Port|
|ByteCount|Liczba bajtów|Count|Łączna|Całkowita liczba bajtów przesłanych w przedziale czasu|Portu i kierunek|
|PacketCount|Liczba pakietów|Count|Łączna|Całkowita liczba pakietów przesyłanych w przedziale czasu|Portu i kierunek|
|SynCount|Liczba SYN|Count|Łączna|Całkowita liczba pakietów SYN przesyłane w przedziale czasu|Portu i kierunek|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ApplicationRuleHit|Liczba trafień reguł aplikacji|Count|Łączna|Ile razy zostały trafień reguł aplikacji|Protokół stanu, powodu|
|NetworkRuleHit|Liczba trafień reguł sieciowych|Count|Łączna|Ile razy zostały trafień reguł sieciowych|Protokół stanu, powodu|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Przepływność|Przepływność|BytesPerSecond|Łączna|Liczba bajtów na sekundę, który obsługiwał Application Gateway|Nie wymiarów|
|UnhealthyHostCount|Liczba hostów w złej kondycji|Count|Średnia|Liczba hostów w złej kondycji wewnętrznej bazy danych|BackendSettingsPool|
|HealthyHostCount|Liczba hostów w dobrej kondycji|Count|Średnia|Liczba hostów zaplecza w dobrej kondycji|BackendSettingsPool|
|TotalRequests|Łączna liczba żądań|Count|Łączna|Liczba pomyślnych żądań, które usługa Application Gateway jest obsługiwane|BackendSettingsPool|
|FailedRequests|Żądania zakończone niepowodzeniem|Count|Łączna|Liczba nieudanych żądań, które usługa Application Gateway jest obsługiwane|BackendSettingsPool|
|ResponseStatus|Stan odpowiedzi|Count|Łączna|Stan odpowiedzi HTTP zwrócony przez usługę Application Gateway|HttpStatusGroup|
|Wartości CurrentConnections|Bieżąca liczba połączeń|Count|Łączna|Liczba bieżących połączeń ustanowionych z usługą Application Gateway|Nie wymiarów|
|CapacityUnits|Bieżące jednostki pojemności|Count|Średnia|Wykorzystane jednostki pojemności|Nie wymiarów|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AverageBandwidth|Brama S2S przepustowości|BytesPerSecond|Średnia|Średnia przepustowość lokacja lokacja bramy w bajtach na sekundę|Nie wymiarów|
|P2SBandwidth|Przepustowość P2S bramy|BytesPerSecond|Średnia|Średnia przepustowość punkt lokacja bramy w bajtach na sekundę|Nie wymiarów|
|P2SConnectionCount|Liczba połączeń P2S|Count|Maksimum|Liczba połączeń punkt lokacja bramy|Protocol|
|TunnelAverageBandwidth|Przepustowość tunelu|BytesPerSecond|Średnia|Średnia przepustowość tunelu w bajtach na sekundę|ConnectionName, RemoteIP|
|TunnelEgressBytes|Bajty wychodzące tunelu|Bajty|Łączna|Wychodzące bajty tunel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Bajty przychodzące tunelu|Bajty|Łączna|Przychodzące bajty tunel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Pakiety wychodzące tunelu|Count|Łączna|Liczba wychodzących pakietów tunelu|ConnectionName, RemoteIP|
|TunnelIngressPackets|Pakiety przychodzące tunelu|Count|Łączna|Liczba przychodzących pakietów tunelu|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Porzucanie pakietów niezgodność ruchu wychodzącego usług terminalowych tunelu|Count|Łączna|Liczba wychodzących pakietów listy z niezgodności selektor ruchu tunelu|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Porzucanie pakietów niezgodność ruch przychodzący serwera terminali tunelu|Count|Łączna|Liczba przychodzących pakietów listy z niezgodności selektor ruchu tunelu|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Średnia|Usługa BITS ingressing platformy Azure na sekundę|Elementowi PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Średnia|Usługa BITS egressing platformy Azure na sekundę|Elementowi PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Średnia|Usługa BITS ingressing platformy Azure na sekundę|Nie wymiarów|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Średnia|Usługa BITS egressing platformy Azure na sekundę|Nie wymiarów|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Średnia|Usługa BITS ingressing platformy Azure na sekundę|Nie wymiarów|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Średnia|Usługa BITS egressing platformy Azure na sekundę|Nie wymiarów|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QpsByEndpoint|Zapytania według zwracany punkt końcowy|Count|Łączna|Liczba przypadków, gdy punkt końcowy usługi Traffic Manager zwrócił się w określonym przedziale czasu|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Stan punktu końcowego na punkt końcowy|Count|Maksimum|1, jeśli punkt końcowy sondowania stanu jest "włączone", w przeciwnym razie 0.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Sondy nie powiodło się|Wartość procentowa|Średnia|% łączności sondy monitorujące nie powiodło się.|Nie wymiarów|
|AverageRoundtripMs|Średni Czas obustronnej konwersji (ms)|MilliSeconds|Średnia|Czas błądzenia średni sieci (ms) dla łączności przesyłane między źródłowym i docelowym sondy monitorujące|Nie wymiarów|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RequestCount|Liczba żądań|Count|Łączna|Liczba żądań klientów obsługiwanych przez serwer proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Rozmiar żądania|Bajty|Łączna|Liczba bajtów wysłanych jako żądania od klientów z serwerem proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Rozmiar odpowiedzi|Bajty|Łączna|Liczba bajtów wysłanych jako odpowiedzi z serwera proxy HTTP/Https na klientach|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Liczba żądań wewnętrznej bazy danych|Count|Łączna|Liczba żądań wysyłanych z serwera proxy HTTP/Https do zaplecza|HttpStatus, HttpStatusGroup, wewnętrznej bazy danych|
|BackendRequestLatency|Opóźnienia żądania wewnętrznej bazy danych|MilliSeconds|Średnia|Czas obliczonym na podstawie Jeśli żądanie zostało wysłane przez serwer proxy HTTP/Https do wewnętrznej bazy danych do momentu otrzymania przez serwer proxy HTTP/Https ostatni bajt odpowiedzi z wewnętrznej bazy danych|Zaplecze|
|TotalLatency|Całkowity czas oczekiwania|MilliSeconds|Średnia|Czas obliczonym na podstawie żądań klienta otrzymania przez serwer proxy HTTP/Https momentu klient potwierdzenia ostatni bajt odpowiedzi z serwera proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Wartość procentowa kondycję wewnętrznej bazy danych|Wartość procentowa|Średnia|Procent pomyślnych kondycji sondy z serwera proxy HTTP/Https do zaplecza|Backend, BackendPool|
|WebApplicationFirewallRequestCount|Liczba żądań zapory aplikacji sieci Web|Count|Łączna|Liczba żądań klienta przetwarzanych przez zaporę aplikacji sieci Web|PolicyName, Nazwa_reguły, akcja|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Registration.all|Operacje rejestracji|Count|Łączna|Liczba wszystkich operacji rejestracji zakończonych powodzeniem (operacje tworzenia, aktualizacje, zapytania i operacje usuwania). |Nie wymiarów|
|registration.create|Operacje tworzenia rejestracji|Count|Łączna|Liczba wszystkich operacji tworzenia rejestracji zakończonych powodzeniem.|Nie wymiarów|
|registration.update|Operacje aktualizacji operacji|Count|Łączna|Liczba wszystkich aktualizacji rejestracji zakończonych powodzeniem.|Nie wymiarów|
|registration.get|Operacje odczytu operacji|Count|Łączna|Liczba wszystkich zapytań dotyczących rejestracji zakończonych powodzeniem.|Nie wymiarów|
|registration.delete|Operacje usuwania rejestracji|Count|Łączna|Liczba wszystkich operacji usuwania rejestracji zakończonych powodzeniem.|Nie wymiarów|
|przychodzące|Wiadomości przychodzące|Count|Łączna|Liczba wszystkich operacji wysyłania wywołania interfejsu API zakończonych powodzeniem. |Nie wymiarów|
|incoming.scheduled|Wysłano zaplanowane powiadomienia wypychane|Count|Łączna|Zaplanowane powiadomienia wypychane zostało anulowane|Nie wymiarów|
|incoming.scheduled.cancel|Zaplanowane powiadomienia wypychane zostało anulowane|Count|Łączna|Zaplanowane powiadomienia wypychane zostało anulowane|Nie wymiarów|
|scheduled.pending|Oczekujące zaplanowane powiadomienia|Count|Łączna|Oczekujące zaplanowane powiadomienia|Nie wymiarów|
|Installation.all|Operacje zarządzania instalacją|Count|Łączna|Operacje zarządzania instalacją|Nie wymiarów|
|installation.get|Pobierz operacje instalacji|Count|Łączna|Pobierz operacje instalacji|Nie wymiarów|
|installation.upsert|Utwórz lub zaktualizuj operacje instalacji|Count|Łączna|Utwórz lub zaktualizuj operacje instalacji|Nie wymiarów|
|installation.patch|Operacje instalacji poprawki|Count|Łączna|Operacje instalacji poprawki|Nie wymiarów|
|installation.delete|Usuń operacje instalacji|Count|Łączna|Usuń operacje instalacji|Nie wymiarów|
|outgoing.allpns.success|Powiadomienia zakończone powodzeniem|Count|Łączna|Liczba wszystkich powiadomień zakończonych powodzeniem.|Nie wymiarów|
|Outgoing.allpns.invalidpayload|Błędy ładunku|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ system powiadomień platformy zwrócił błąd oznaczający nieprawidłowy ładunek.|Nie wymiarów|
|outgoing.allpns.pnserror|Błędy zewnętrznych systemów powiadamiania|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ wystąpił problem podczas komunikowania się z systemem powiadomień platformy (nie obejmuje problemów z uwierzytelnianiem).|Nie wymiarów|
|outgoing.allpns.channelerror|Błędy kanału|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ kanał był nieprawidłowy i nie został skojarzony z poprawną aplikacją (ograniczoną lub wygasłą).|Nie wymiarów|
|outgoing.allpns.badorexpiredchannel|Błędy nieprawidłowych lub wygasłych kanałów|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ kanał/token/identyfikator rejestracji w rejestracji wygasł lub był nieprawidłowy.|Nie wymiarów|
|outgoing.wns.success|Powiadomienia usługi WNS zakończone powodzeniem|Count|Łączna|Liczba wszystkich powiadomień zakończonych powodzeniem.|Nie wymiarów|
|outgoing.wns.invalidcredentials|Błędy autoryzacji usługi WNS (nieprawidłowe poświadczenia)|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ system powiadomień platformy nie zaakceptował podanych poświadczeń lub poświadczenia zostały zablokowane. (Windows Live nie rozpoznaje poświadczeń).|Nie wymiarów|
|outgoing.wns.badchannel|Błąd nieprawidłowego kanału usługi WNS|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ identyfikator ChannelURI w rejestracji nie został rozpoznany (stan usługi WNS: 404 Nie znaleziono).|Nie wymiarów|
|outgoing.wns.expiredchannel|Błąd wygasłego kanału usługi WNS|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ identyfikator ChannelURI wygasł (stan usługi WNS: 410 Gone).|Nie wymiarów|
|Outgoing.wns.throttled|Powiadomienia usługi WNS z ograniczoną przepływnością|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ usługi WNS ogranicza tę aplikację (stan usługi WNS: 406 nie do przyjęcia).|Nie wymiarów|
|outgoing.wns.tokenproviderunreachable|Błędy autoryzacji usługi WNS (niedostępna)|Count|Łączna|Nie można połączyć się z usługą Windows Live.|Nie wymiarów|
|outgoing.wns.invalidtoken|Błędy autoryzacji usługi WNS (nieprawidłowy token)|Count|Łączna|Token przekazany do usługi WNS jest nieprawidłowy (stan usługi WNS: 401 Brak autoryzacji).|Nie wymiarów|
|outgoing.wns.wrongtoken|Błędy autoryzacji usługi WNS (niepoprawny token)|Count|Łączna|Token przekazany do usługi WNS jest prawidłowy, ale dla innej aplikacji (stan usługi WNS: 403 Forbidden). Może to nastąpić, jeśli identyfikator ChannelURI w rejestracji jest skojarzony z inną aplikacją. Upewnij się, że aplikacja klienta została skojarzona z aplikacją, której poświadczenia znajdują się w Centrum powiadomień.|Nie wymiarów|
|outgoing.wns.invalidnotificationformat|Nieprawidłowy format powiadomienia usługi WNS|Count|Łączna|Format powiadomienia jest nieprawidłowy (stan usługi WNS: 400). Należy pamiętać, że usługa WNS nie odrzuca wszystkich nieprawidłowych ładunków.|Nie wymiarów|
|outgoing.wns.invalidnotificationsize|Błąd nieprawidłowego rozmiaru powiadomienia usługi WNS|Count|Łączna|Ładunek powiadomienia jest zbyt duży (stan usługi WNS: 413).|Nie wymiarów|
|outgoing.wns.channelthrottled|Ograniczono przepływność kanału usługi WNS|Count|Łączna|Powiadomienie zostało porzucone, ponieważ identyfikator ChannelURI w rejestracji jest ograniczany (nagłówek odpowiedzi usługi WNS: X-WNS-NotificationStatus:channelThrottled).|Nie wymiarów|
|outgoing.wns.channeldisconnected|Rozłączono kanał usługi WNS|Count|Łączna|Powiadomienie zostało porzucone, ponieważ identyfikator ChannelURI w rejestracji jest ograniczany (nagłówek odpowiedzi usługi WNS: X-WNS-DeviceConnectionStatus: disconnected).|Nie wymiarów|
|outgoing.wns.dropped|Porzucone powiadomienia usługi WNS|Count|Łączna|Powiadomienie zostało porzucone, ponieważ identyfikator ChannelURI w rejestracji jest ograniczany (X-WNS-NotificationStatus: porzucono, ale nie X-WNS-DeviceConnectionStatus: rozłączono).|Nie wymiarów|
|outgoing.wns.pnserror|Błędy usługi WNS|Count|Łączna|Nie dostarczono powiadomienia z powodu błędów podczas komunikowania się z usługą WNS.|Nie wymiarów|
|outgoing.wns.authenticationerror|Błędy uwierzytelniania usługi WNS|Count|Łączna|Nie dostarczono powiadomienia z powodu błędów podczas komunikowania się z błędnym tokenem lub nieprawidłowymi poświadczeniami usługi Windows Live.|Nie wymiarów|
|outgoing.apns.success|Powiadomienia usługi APNS zakończone powodzeniem|Count|Łączna|Liczba wszystkich powiadomień zakończonych powodzeniem.|Nie wymiarów|
|outgoing.apns.invalidcredentials|Błędy autoryzacji usługi APNS|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ system powiadomień platformy nie zaakceptował podanych poświadczeń lub poświadczenia zostały zablokowane.|Nie wymiarów|
|outgoing.apns.badchannel|Błąd nieprawidłowego kanału usługi APNS|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ token jest nieprawidłowy (kod stanu usługi APNS: 8).|Nie wymiarów|
|outgoing.apns.expiredchannel|Błąd nieprawidłowego kanału usługi APNS|Count|Łączna|Liczba tokenów unieważnionych przy użyciu kanału sprzężenia zwrotnego usługi APNS.|Nie wymiarów|
|outgoing.apns.invalidnotificationsize|Błąd nieprawidłowego rozmiaru powiadomienia usługi APNS|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ ładunek powiadomienia był zbyt duży (kod stanu usługi APNS: 7).|Nie wymiarów|
|outgoing.apns.pnserror|Błędy usługi APNS|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem z powodu błędów podczas komunikowania się z usługą APNS.|Nie wymiarów|
|outgoing.gcm.success|Powiadomienia usługi GCM zakończone powodzeniem|Count|Łączna|Liczba wszystkich powiadomień zakończonych powodzeniem.|Nie wymiarów|
|outgoing.gcm.invalidcredentials|Błędy autoryzacji usługi GCM (nieprawidłowe poświadczenia)|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ system powiadomień platformy nie zaakceptował podanych poświadczeń lub poświadczenia zostały zablokowane.|Nie wymiarów|
|outgoing.gcm.badchannel|Błąd nieprawidłowego kanału usługi GCM|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ identyfikator rejestracji nie został rozpoznany (wynik usługi GCM: Nieprawidłowa rejestracja).|Nie wymiarów|
|outgoing.gcm.expiredchannel|Błąd wygasłego kanału usługi GCM|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ identyfikator rejestracji wygasł (wynik usługi GCM: NotRegistered).|Nie wymiarów|
|outgoing.gcm.throttled|Powiadomienia usługi GCM z ograniczoną przepływnością|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ usługa GCM ograniczyła tę aplikację (kod stanu usługi GCM: 501 – 599 lub wynik: niedostępne).|Nie wymiarów|
|outgoing.gcm.invalidnotificationformat|Nieprawidłowy format powiadomienia usługi GCM|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ ładunek został nieprawidłowo sformatowany (wynik usługi GCM: InvalidDataKey lub InvalidTtl).|Nie wymiarów|
|outgoing.gcm.invalidnotificationsize|Błąd nieprawidłowego rozmiaru powiadomienia usługi GCM|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ ładunek powiadomienia był zbyt duży (wynik usługi GCM: MessageTooBig).|Nie wymiarów|
|outgoing.gcm.wrongchannel|Błąd nieprawidłowego kanału usługi GCM|Count|Łączna|Liczba wypchnięć, które nie powiodło się, ponieważ identyfikator rejestracji nie jest skojarzony z bieżącą aplikacją (wynik usługi GCM: InvalidPackageName).|Nie wymiarów|
|outgoing.gcm.pnserror|Błędy usługi GCM|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem z powodu błędów podczas komunikowania się z usługą GCM.|Nie wymiarów|
|outgoing.gcm.authenticationerror|Błędy uwierzytelniania usługi GCM|Count|Łączna|Liczba wypchnięć, które nie powiodło się, ponieważ system powiadomień platformy nie zaakceptował podanych poświadczeń poświadczenia zostały zablokowane lub element SenderId nie jest poprawnie skonfigurowana w aplikacji (wynik usługi GCM: MismatchedSenderId).|Nie wymiarów|
|outgoing.mpns.success|Powiadomienia usługi MPNS zakończone powodzeniem|Count|Łączna|Liczba wszystkich powiadomień zakończonych powodzeniem.|Nie wymiarów|
|outgoing.mpns.invalidcredentials|Nieprawidłowe poświadczenia usługi MPNS|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ system powiadomień platformy nie zaakceptował podanych poświadczeń lub poświadczenia zostały zablokowane.|Nie wymiarów|
|outgoing.mpns.badchannel|Błąd nieprawidłowego kanału usługi MPNS|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ identyfikator ChannelURI w rejestracji nie został rozpoznany (stan usługi MPNS: 404 Nie znaleziono).|Nie wymiarów|
|Outgoing.mpns.throttled|Powiadomienia usługi MPNS z ograniczoną przepływnością|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ usługi MPNS ogranicza tę aplikację (WNS MPNS: 406 nie do przyjęcia).|Nie wymiarów|
|outgoing.mpns.invalidnotificationformat|Nieprawidłowy format powiadomienia usługi MPNS|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ ładunek powiadomienia był zbyt duży.|Nie wymiarów|
|outgoing.mpns.channeldisconnected|Rozłączono kanał usługi MPNS|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ identyfikator ChannelURI w rejestracji został rozłączony (stan usługi MPNS: 412 nie znaleziono).|Nie wymiarów|
|outgoing.mpns.dropped|Porzucone powiadomienia usługi MPNS|Count|Łączna|Liczba wypchnięć, które zostały porzucone przez usługę MPNS (nagłówek odpowiedzi MPNS: X-NotificationStatus: QueueFull lub pominięty).|Nie wymiarów|
|outgoing.mpns.pnserror|Błędy usługi MPNS|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem z powodu błędów podczas komunikowania się z usługą MPNS.|Nie wymiarów|
|outgoing.mpns.authenticationerror|Błędy uwierzytelniania usługi MPNS|Count|Łączna|Liczba wypchnięć, które zakończyły się niepowodzeniem, ponieważ system powiadomień platformy nie zaakceptował podanych poświadczeń lub poświadczenia zostały zablokowane.|Nie wymiarów|
|notificationhub.pushes|Wszystkie powiadomienia wychodzące|Count|Łączna|Wszystkie powiadomienia wychodzące w centrum powiadomień|Nie wymiarów|
|incoming.all.requests|Wszystkie żądania przychodzące|Count|Łączna|Łączna liczba żądań przychodzących dla centrum powiadomień|Nie wymiarów|
|incoming.all.failedrequests|Wszystkie nieudane żądania przychodzące|Count|Łączna|Łączna liczba nieudanych żądań przychodzących dla centrum powiadomień|Nie wymiarów|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Average_ % wolnych węzłów i|% Wolnych węzłów i|Count|Średnia|Average_ % wolnych węzłów i|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % wolnego miejsca|% Wolnego miejsca|Count|Średnia|Average_ % wolnego miejsca|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % użytych węzłów i|% Użytych węzłów i|Count|Średnia|Average_ % użytych węzłów i|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Używany obszar Average_ %|Procent wykorzystania miejsca|Count|Średnia|Używany obszar Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Odczytane bajty/s|Bajty odczytu z dysku/s|Count|Średnia|Average_Disk Odczytane bajty/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk odczyty/s|Odczyty dysku/s|Count|Średnia|Average_Disk odczyty/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk dyskowe/s|Transfery dyskowe/s|Count|Średnia|Average_Disk dyskowe/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk zapisane bajty/s|Bajty zapisu na dysku/s|Count|Średnia|Average_Disk zapisane bajty/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk zapisy/s|Zapisy dysku/s|Count|Średnia|Average_Disk zapisy/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free (MB)|Wolne megabajty|Count|Średnia|Average_Free (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Bajty dysku/s|Bajty dysku logicznego/s|Count|Średnia|Average_Logical Bajty dysku/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % dostępnej pamięci|% Dostępnej pamięci.|Count|Średnia|Average_ % dostępnej pamięci|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % dostępnego obszaru wymiany|% Dostępnego obszaru wymiany|Count|Średnia|Average_ % dostępnego obszaru wymiany|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ procent wykorzystania pamięci|Procent wykorzystania pamięci|Count|Średnia|Average_ procent wykorzystania pamięci|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Używany obszar wymiany Average_ %|Używany obszar wymiany %|Count|Średnia|Używany obszar wymiany Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pamięć (MB) Average_Available|Dostępna pamięć (MB)|Count|Średnia|Pamięć (MB) Average_Available|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available obszar wymiany|Dostępny obszar wymiany|Count|Średnia|Average_Available obszar wymiany|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page odczyty/s|Odczyty stron/s|Count|Średnia|Average_Page odczyty/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page zapisy/s|Zapisy stron/s|Count|Średnia|Average_Page zapisy/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages na sekundę|Strony/s|Count|Średnia|Average_Pages na sekundę|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Obszar wymiany (MB) Average_Used|Miejsce używany obszar wymiany (MB)|Count|Średnia|Obszar wymiany (MB) Average_Used|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used pamięć (MB)|Używana pamięć (MB)|Count|Średnia|Average_Used pamięć (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bajty przesłane|Całkowita liczba przesłanych bajtów|Count|Średnia|Average_Total Bajty przesłane|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odebrane bajty Average_Total|Całkowita liczba odebranych bajtów|Count|Średnia|Odebrane bajty Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total bajtów|Całkowita liczba bajtów|Count|Średnia|Average_Total bajtów|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total pakiety przesyłane|Wszystkie pakiety przesyłane|Count|Średnia|Average_Total pakiety przesyłane|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odebrane pakiety Average_Total|Łączna liczba odebranych pakietów|Count|Średnia|Odebrane pakiety Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Błędy Rx Average_Total|Rx łączna liczba błędów|Count|Średnia|Błędy Rx Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Błędy Tx Average_Total|Tx łączna liczba błędów|Count|Średnia|Błędy Tx Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total kolizji|Łączna liczba konfliktów|Count|Średnia|Average_Total kolizji|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Czas dysku w s/Odczyt|Średni Czas dysku w s/Odczyt|Count|Średnia|Average_Avg. Czas dysku w s/Odczyt|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Dysku w s/Transfer|Średni Dysku w s/Transfer|Count|Średnia|Average_Avg. Dysku w s/Transfer|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Dysku w s/Zapis|Średni Dysku w s/Zapis|Count|Średnia|Average_Avg. Dysku w s/Zapis|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Bajty dysku/s|Bajty dysku fizycznego/s|Count|Średnia|Average_Physical Bajty dysku/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas uprzywilejowany Average_Pct|Czas uprzywilejowany PCT|Count|Średnia|Czas uprzywilejowany Average_Pct|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas użytkownika Average_Pct|Czas użytkownika protokołu PCT|Count|Średnia|Czas użytkownika Average_Pct|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|KB pamięci Average_Used|Używana pamięć (KB pamięci)|Count|Średnia|KB pamięci Average_Used|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pamięć współużytkowaną Average_Virtual|Wirtualnej pamięci wspólnej|Count|Średnia|Pamięć współużytkowaną Average_Virtual|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ czas DPC (%)|Czas DPC (%)|Count|Średnia|Average_ czas DPC (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas bezczynności (%) Average_|Czas bezczynności (%)|Count|Średnia|Czas bezczynności (%) Average_|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ czas przerwań (%)|Czas przerwań (%)|Count|Średnia|Average_ czas przerwań (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas oczekiwania operacji We/Wy dla % Average_|Czas oczekiwania operacji We/Wy %|Count|Średnia|Czas oczekiwania operacji We/Wy dla % Average_|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas nieuprzywilejowany (%) Average_|Czas nieuprzywilejowany (%)|Count|Średnia|Czas nieuprzywilejowany (%) Average_|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas uprzywilejowany Average_ %|Czas uprzywilejowany %|Count|Średnia|Czas uprzywilejowany Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ czas procesora (%)|Czas procesora (%)|Count|Średnia|Average_ czas procesora (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas użytkownika % Average_|Czas użytkownika (%)|Count|Średnia|Czas użytkownika % Average_|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pamięć fizyczna Average_Free|Wolna pamięć fizyczna|Count|Średnia|Pamięć fizyczna Average_Free|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free miejsce w plikach stronicowania|Wolne miejsce w plikach stronicowania|Count|Średnia|Average_Free miejsce w plikach stronicowania|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free pamięci wirtualnej|Wolna pamięć wirtualna|Count|Średnia|Average_Free pamięci wirtualnej|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Procesy|Count|Średnia|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size przechowywane w plikach stronicowania|Rozmiar zapisanych w plikach stronicowania|Count|Średnia|Average_Size przechowywane w plikach stronicowania|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Czas pracy|Count|Średnia|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Użytkownicy|Count|Średnia|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Czas dysku w s/Odczyt|Średni Czas dysku w s/Odczyt|Count|Średnia|Average_Avg. Czas dysku w s/Odczyt|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Dysku w s/Zapis|Średni Dysku w s/Zapis|Count|Średnia|Average_Avg. Dysku w s/Zapis|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Długość kolejki dysku Average_Current|Bieżąca długość kolejki dysku|Count|Średnia|Długość kolejki dysku Average_Current|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk odczyty/s|Odczyty dysku/s|Count|Średnia|Average_Disk odczyty/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk dyskowe/s|Transfery dyskowe/s|Count|Średnia|Average_Disk dyskowe/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk zapisy/s|Zapisy dysku/s|Count|Średnia|Average_Disk zapisy/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free (MB)|Wolne megabajty|Count|Średnia|Average_Free (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % wolnego miejsca|% Wolnego miejsca|Count|Średnia|Average_ % wolnego miejsca|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available pamięć (MB)|Dostępna pamięć (MB)|Count|Średnia|Average_Available pamięć (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % Zadeklarowane bajty w użyciu|% Przydzielonych bajtów w użyciu|Count|Średnia|Average_ % Zadeklarowane bajty w użyciu|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes odebrane/s|Bajty odebrane/s|Count|Średnia|Average_Bytes odebrane/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes wysłane/s|Bajty wysłane/s|Count|Średnia|Average_Bytes wysłane/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes liczba bajtów/s|Całkowita liczba bajtów/s|Count|Średnia|Average_Bytes liczba bajtów/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ czas procesora (%)|Czas procesora (%)|Count|Średnia|Average_ czas procesora (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Długość kolejki Average_Processor|Długość kolejki procesora|Count|Średnia|Długość kolejki Average_Processor|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Puls|Puls|Count|Łączna|Puls|Computer, OSType, Version, SourceComputerId|
|Aktualizacja|Aktualizacja|Count|Średnia|Aktualizacja|Komputer, produktów, klasyfikacji, UpdateState, opcjonalnie, zatwierdzone|
|Zdarzenie|Zdarzenie|Count|Średnia|Zdarzenie|Źródło dziennika zdarzeń, komputer, EventCategory, EventLevel EventLevelName, identyfikator zdarzenia|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QueryDuration|Czas trwania zapytania|Milisekundy|Średnia|Czas trwania zapytania języka DAX w ostatnim interwale|Nie wymiarów|
|QueryPoolJobQueueLength|Wątki: Długość kolejki zadań puli zapytań|Count|Średnia|Liczba zadań w kolejce puli wątków zapytania.|Nie wymiarów|
|qpu_high_utilization_metric|Wysokie wykorzystanie jednostek QPU|Count|Łączna|Wysokie wykorzystanie jednostek QPU w ostatniej chwili 1 QPU wysokie wykorzystanie, w przeciwnym razie 0.|Nie wymiarów|
|memory_metric|Memory (Pamięć)|Bajty|Średnia|Ilość pamięci. Zakres 0 – 3 GB A1, 0 – 5 GB dla opcji A2, A3 0-10 GB, 0 – 25 GB dla formatu A4, 0 – 50 GB dla A5 i 0 – 100 GB w przypadku A6|Nie wymiarów|
|memory_thrashing_metric|Przeładowywanie pamięci|Wartość procentowa|Średnia|Średnie przeładowywanie pamięci.|Nie wymiarów|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Count|Łączna|Pomyślne połączenia ListenerConnections dla elementu Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Count|Łączna|Błąd ClientError w połączeniach ListenerConnections dla elementu Microsoft.Relay.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Count|Łączna|Błąd ServerError w połączeniach ListenerConnections dla elementu Microsoft.Relay.|EntityName|
|SenderConnections-Success|SenderConnections-Success|Count|Łączna|Pomyślne połączenia SenderConnections dla elementu Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Count|Łączna|Błąd ClientError w połączeniach SenderConnections dla elementu Microsoft.Relay.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Count|Łączna|Błąd ServerError w elemencie SenderConnections dla elementu Microsoft.Relay.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Count|Łączna|Łączna liczba połączeń ListenerConnections dla elementu Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Count|Łączna|Łączna liczba żądań SenderConnections dla elementu Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Count|Łączna|Łączna liczba połączeń ActiveConnections dla elementu Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Count|Łączna|Łączna liczba odbiorników ActiveListeners dla elementu Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Count|Łączna|Łączna liczba bajtów BytesTransferred dla elementu Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Count|Łączna|Łączna liczba rozłączeń ListenerDisconnects dla elementu Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Count|Łączna|Łączna liczba rozłączeń SenderDisconnects dla elementu Microsoft.Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SearchLatency|Opóźnienie wyszukiwania|Sekundy|Średnia|Opóźnienie wyszukiwania średnia dla usługi wyszukiwania|Nie wymiarów|
|SearchQueriesPerSecond|Zapytania wyszukiwania na sekundę|CountPerSecond|Średnia|Zapytania wyszukiwania na sekundę usługi wyszukiwania|Nie wymiarów|
|ThrottledSearchQueriesPercentage|Procent zapytań wyszukiwania z ograniczoną przepustowością|Wartość procentowa|Średnia|Procent zapytań wyszukiwania, które były ograniczone do usługi wyszukiwania|Nie wymiarów|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SuccessfulRequests|Liczba pomyślnych żądań (wersja zapoznawcza)|Count|Łączna|Łączna liczba udanych żądań dla przestrzeni nazw (wersja zapoznawcza)|EntityName|
|ServerErrors|Błędy serwera. (wersja zapoznawcza)|Count|Łączna|Błędy serwera dla elementu Microsoft.ServiceBus. (wersja zapoznawcza)|EntityName|
|UserErrors|Błędy użytkowników. (wersja zapoznawcza)|Count|Łączna|Błędy użytkowników dla elementu Microsoft.ServiceBus. (wersja zapoznawcza)|EntityName|
|ThrottledRequests|Żądania ograniczone. (wersja zapoznawcza)|Count|Łączna|Żądania ograniczone dla elementu Microsoft.ServiceBus. (wersja zapoznawcza)|EntityName|
|IncomingRequests|Przychodzące żądania (wersja zapoznawcza)|Count|Łączna|Żądania przychodzące dla elementu Microsoft.ServiceBus. (wersja zapoznawcza)|EntityName|
|IncomingMessages|Komunikaty przychodzące (wersja zapoznawcza)|Count|Łączna|Komunikaty przychodzące dla elementu Microsoft.ServiceBus. (wersja zapoznawcza)|EntityName|
|OutgoingMessages|Komunikaty wychodzące (wersja zapoznawcza)|Count|Łączna|Komunikaty wychodzące dla elementu Microsoft.ServiceBus. (wersja zapoznawcza)|EntityName|
|ActiveConnections|Połączeń ActiveConnections (wersja zapoznawcza)|Count|Łączna|Całkowita liczba aktywnych połączeń dla elementu Microsoft.ServiceBus. (wersja zapoznawcza)|Nie wymiarów|
|Rozmiar|Rozmiar (wersja zapoznawcza)|Bajty|Średnia|Rozmiar kolejki/tematu w bajtach. (wersja zapoznawcza)|EntityName|
|Wiadomości|Liczba komunikatów w kolejce/temacie. (wersja zapoznawcza)|Count|Średnia|Liczba komunikatów w kolejce/temacie. (wersja zapoznawcza)|EntityName|
|ActiveMessages|Liczba aktywnych komunikatów w kolejce/temacie. (wersja zapoznawcza)|Count|Średnia|Liczba aktywnych komunikatów w kolejce/temacie. (wersja zapoznawcza)|EntityName|
|DeadletteredMessages|Liczba komunikatów utraconych w kolejce/temacie. (wersja zapoznawcza)|Count|Średnia|Liczba komunikatów utraconych w kolejce/temacie. (wersja zapoznawcza)|EntityName|
|ScheduledMessages|Liczba komunikatów zaplanowanych w kolejce/temacie. (wersja zapoznawcza)|Count|Średnia|Liczba komunikatów zaplanowanych w kolejce/temacie. (wersja zapoznawcza)|EntityName|
|CPUXNS|Użycie procesora na przestrzeń nazw|Wartość procentowa|Maksimum|Metryka użycia procesora przestrzeni nazw w usłudze Service Bus w warstwie Premium|Nie wymiarów|
|WSXNS|Użycie rozmiaru pamięci na przestrzeń nazw|Wartość procentowa|Maksimum|Metryka użycia pamięci przestrzeni nazw w usłudze Service Bus w warstwie Premium|Nie wymiarów|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Count|Średnia|Procesor CPU przydzielonych do tego kontenera w millicores|ApplicationName, ServiceReplicaName ServiceName, CodePackageName,|
|AllocatedMemory|AllocatedMemory|Bajty|Średnia|Pamięć przydzielona do tego kontenera w MB|ApplicationName, ServiceReplicaName ServiceName, CodePackageName,|
|ActualCpu|ActualCpu|Count|Średnia|Rzeczywiste użycie procesora CPU w millicores|ApplicationName, ServiceReplicaName ServiceName, CodePackageName,|
|ActualMemory|ActualMemory|Bajty|Średnia|Użycie pamięci Rzeczywiste w MB|ApplicationName, ServiceReplicaName ServiceName, CodePackageName,|
|CpuUtilization|CpuUtilization|Wartość procentowa|Średnia|Wykorzystanie procesora CPU dla tego kontenera jako procent AllocatedCpu|ApplicationName, ServiceReplicaName ServiceName, CodePackageName,|
|MemoryUtilization|MemoryUtilization|Wartość procentowa|Średnia|Wykorzystanie procesora CPU dla tego kontenera jako procent AllocatedCpu|ApplicationName, ServiceReplicaName ServiceName, CodePackageName,|
|ApplicationStatus|ApplicationStatus|Count|Średnia|Stan aplikacji usługi Service Fabric siatki|ApplicationName, stan|
|Bajty|Bajty|Count|Średnia|Kondycja usługi w aplikacji usługi Service Fabric siatki|ServiceName ApplicationName, stan,|
|ServiceReplicaStatus|ServiceReplicaStatus|Count|Średnia|Kondycja usługi repliki bazy danych w aplikacji usługi Service Fabric siatki|ApplicationName, stan, ServiceName, ServiceReplicaName|
|ContainerStatus|ContainerStatus|Count|Średnia|Stan kontenera w aplikacji usługi Service Fabric siatki|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, stan|
|RestartCount|RestartCount|Count|Średnia|Uruchom ponownie licznik kontenera, w aplikacji usługi Service Fabric siatki|ApplicationName, stan, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ConnectionCount|Liczba połączeń|Count|Maksimum|Liczba połączeń użytkowników.|Endpoint|
|MessageCount|Liczba komunikatów|Count|Łączna|Łączna liczba wiadomości.|Nie wymiarów|
|InboundTraffic|Ruch przychodzący|Bajty|Łączna|Ruch przychodzący usługi|Nie wymiarów|
|OutboundTraffic|Ruch wychodzący|Bajty|Łączna|Ruch wychodzący z usługi|Nie wymiarów|
|UserErrors|Błędy użytkowników|Wartość procentowa|Maksimum|Procent błędów użytkownika|Nie wymiarów|
|SystemErrors|Błędy systemu|Wartość procentowa|Maksimum|Procent błędów systemu|Nie wymiarów|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Wartość procentowa|Średnia|Procent użycia procesora CPU|Nie wymiarów|
|physical_data_read_percent|We/wy danych (procent)|Wartość procentowa|Średnia|We/wy danych (procent)|Nie wymiarów|
|log_write_percent|We/wy dziennika (procent)|Wartość procentowa|Średnia|We/wy dziennika (procent)|Nie wymiarów|
|dtu_consumption_percent|Procent jednostek DTU|Wartość procentowa|Średnia|Procent jednostek DTU|Nie wymiarów|
|magazyn|Używane miejsce na dane|Bajty|Maksimum|Łączny rozmiar bazy danych|Nie wymiarów|
|connection_successful|Pomyślnie nawiązane połączenia|Count|Łączna|Pomyślnie nawiązane połączenia|Nie wymiarów|
|connection_failed|Połączenia zakończone niepowodzeniem|Count|Łączna|Połączenia zakończone niepowodzeniem|Nie wymiarów|
|blocked_by_firewall|Blokowane przez zaporę|Count|Łączna|Blokowane przez zaporę|Nie wymiarów|
|Zakleszczenia|Zakleszczenia|Count|Łączna|Zakleszczenia|Nie wymiarów|
|storage_percent|Procent miejsca danych|Wartość procentowa|Maksimum|Procent użycia rozmiaru bazy danych|Nie wymiarów|
|xtp_storage_percent|Procent magazynu OLTP w pamięci|Wartość procentowa|Średnia|Procent magazynu OLTP w pamięci|Nie wymiarów|
|workers_percent|Procent procesów roboczych|Wartość procentowa|Średnia|Procent procesów roboczych|Nie wymiarów|
|sessions_percent|Procent sesji|Wartość procentowa|Średnia|Procent sesji|Nie wymiarów|
|dtu_limit|DTU Limit|Count|Średnia|DTU Limit|Nie wymiarów|
|dtu_used|Używane jednostki DTU|Count|Średnia|Używane jednostki DTU|Nie wymiarów|
|cpu_limit|Limitu procesora CPU|Count|Średnia|Limitu procesora CPU|Nie wymiarów|
|cpu_used|Użycie Procesora|Count|Średnia|Użycie Procesora|Nie wymiarów|
|dwu_limit|Limit jednostek DWU|Count|Maksimum|Limit jednostek DWU|Nie wymiarów|
|dwu_consumption_percent|Procent jednostek DWU|Wartość procentowa|Maksimum|Procent jednostek DWU|Nie wymiarów|
|dwu_used|Używane jednostki DWU|Count|Maksimum|Używane jednostki DWU|Nie wymiarów|
|dw_cpu_percent|DW poziomu węzła procent użycia procesora CPU|Wartość procentowa|Średnia|DW poziomu węzła procent użycia procesora CPU|DwLogicalNodeId|
|dw_physical_data_read_percent|Procentowy poziom we/wy danych węzła magazynu danych|Wartość procentowa|Średnia|Procentowy poziom we/wy danych węzła magazynu danych|DwLogicalNodeId|
    |cache_hit_percent|Procent liczby trafień pamięci podręcznej|Wartość procentowa|Maksimum|Procent liczby trafień pamięci podręcznej|Nie wymiarów|
|cache_used_percent|Pamięć podręczna używana wartość procentowa|Wartość procentowa|Maksimum|Pamięć podręczna używana wartość procentowa|Nie wymiarów|
|local_tempdb_usage_percent|Wartość procentowa lokalnej bazy danych tempdb|Wartość procentowa|Średnia|Wartość procentowa lokalnej bazy danych tempdb|Nie wymiarów|
|app_cpu_billed|Aplikacja rozliczana na podstawie procesora|Count|Łączna|Aplikacja rozliczana na podstawie procesora|Nie wymiarów|
|app_cpu_percent|Procent użycia Procesora aplikacji|Wartość procentowa|Średnia|Procent użycia Procesora aplikacji|Nie wymiarów|
|app_memory_percent|Procent używanej pamięci w aplikacji|Wartość procentowa|Średnia|Procent używanej pamięci w aplikacji|Nie wymiarów|
|allocated_data_storage|Przydzielone miejsce na dane|Bajty|Średnia|Przydzielone miejsce na dane|Nie wymiarów|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Wartość procentowa|Średnia|Procent użycia procesora CPU|Nie wymiarów|
|physical_data_read_percent|We/wy danych (procent)|Wartość procentowa|Średnia|We/wy danych (procent)|Nie wymiarów|
|log_write_percent|We/wy dziennika (procent)|Wartość procentowa|Średnia|We/wy dziennika (procent)|Nie wymiarów|
|dtu_consumption_percent|Procent jednostek DTU|Wartość procentowa|Średnia|Procent jednostek DTU|Nie wymiarów|
|storage_percent|Procent miejsca danych||Wartość procentowa|Średnia|Procent użycia magazynu|Nie wymiarów|
|workers_percent|Procent procesów roboczych|Wartość procentowa|Średnia|Procent procesów roboczych|Nie wymiarów|
|sessions_percent|Procent sesji|Wartość procentowa|Średnia|Procent sesji|Nie wymiarów|
|eDTU_limit|eDTU limit|Count|Średnia|eDTU limit|Nie wymiarów|
|storage_limit|Maksymalny rozmiar danych|Bajty|Średnia|Limit magazynu|Nie wymiarów|
|eDTU_used|używane jednostki eDTU|Count|Średnia|używane jednostki eDTU|Nie wymiarów|
|storage_used|Używane miejsce na dane|Bajty|Średnia|Używane miejsce|Nie wymiarów|
|xtp_storage_percent|Procent magazynu OLTP w pamięci|Wartość procentowa|Średnia|Procent magazynu OLTP w pamięci|Nie wymiarów|
|cpu_limit|Limitu procesora CPU|Count|Średnia|Limitu procesora CPU|Nie wymiarów|
|cpu_used|Użycie Procesora|Count|Średnia|Użycie Procesora|Nie wymiarów|
|allocated_data_storage|Przydzielone miejsce na dane|Bajty|Średnia|Przydzielone miejsce na dane|Nie wymiarów|
|allocated_data_storage_percent|Procent miejsce przydzielone dane|Wartość procentowa|Maksimum|Procent miejsce przydzielone dane|Nie wymiarów|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|virtual_core_count|Liczba rdzeni wirtualnych|Count|Średnia|Liczba rdzeni wirtualnych|Nie wymiarów|
|avg_cpu_percent|Średni procent użycia procesora CPU|Wartość procentowa|Średnia|Średni procent użycia procesora CPU|Nie wymiarów|
|reserved_storage_mb|Zarezerwowane miejsce do magazynowania|Count|Średnia|Zarezerwowane miejsce do magazynowania|Nie wymiarów|
|storage_space_used_mb|Używane miejsce w magazynie|Count|Średnia|Używane miejsce w magazynie|Nie wymiarów|
|io_requests|Liczba żądań We/Wy|Count|Średnia|Liczba żądań We/Wy|Nie wymiarów|
|io_bytes_read|Bajty We/Wy odczytu|Bajty|Średnia|Bajty We/Wy odczytu|Nie wymiarów|
|io_bytes_written|Zapisano bajtów we/wy|Bajty|Średnia|Zapisano bajtów we/wy|Nie wymiarów|

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
|BlobCapacity|Pojemność obiektu blob|Bajty|Średnia|Ilość miejsca w magazynie (w bajtach) używana przez usługę obiektów blob konta magazynu.|BlobType, warstwa|
|BlobCount|Liczba obiektów blob|Licznik|Łącznie|Liczba obiektów Blob w usłudze obiektów Blob konta magazynu.|BlobType|       |BlobCount|Liczba obiektów blob|Count|Średnia|Liczba obiektów blob w usłudze obiektów blob konta magazynu.|BlobType, warstwa|
|ContainerCount|Liczba kontenerów obiektów blob|Licznik|Średnia|Liczba kontenerów w usłudze obiektów Blob konta magazynu.|Nie wymiarów|
|IndexCapacity|Pojemność indeksu|Bajty|Średnia|Ilość miejsca w magazynie (w bajtach) używanego przez indeks (hierarchiczny) usługi ADLS Gen2.|Nie wymiarów|
|Transakcje|Transakcje|Licznik|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje udane i nieudane żądania, a także żądania, które wygenerowany błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi.|Wartość ResponseType, GeoType, ApiName, uwierzytelniania|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Uwierzytelnianie GeoType, ApiName,|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Uwierzytelnianie GeoType, ApiName,|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|MS|Średnia|Średni czas oczekiwania, używane przez usługę Azure Storage do przetwarzania żądania zakończonego powodzeniem w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego w elemencie AverageE2ELatency.|Uwierzytelnianie GeoType, ApiName,|
|SuccessE2ELatency|Opóźnienie e2e dla powodzenia|MS|Średnia|Średnie opóźnienie end-to-end żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Uwierzytelnianie GeoType, ApiName,|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests i podzielenie go przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy skutkują od ograniczoną dostępnością usługi magazynu lub określonej operacji interfejsu API.|Uwierzytelnianie GeoType, ApiName,|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|FileCapacity|Pojemność plików|Bajty|Średnia|Ilość miejsca w magazynie (w bajtach) używanego przez usługę plików konta magazynu.|Nie wymiarów|
|FileCount|Liczba plików|Count|Średnia|Liczba plików w usłudze plików konta magazynu.|Nie wymiarów|
|FileShareCount|Liczba udziałów plików|Count|Średnia|Liczba udziałów plików w usłudze plików konta magazynu.|Nie wymiarów|
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
|ServerSyncSessionResult|Wynik sesji synchronizacji|Count|Średnia|Metryka dzienniki, a wartość 1 każdego pomyślnie czas punktu końcowego serwera kończy sesję synchronizacji, korzystając z punktu końcowego w chmurze|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Pliki zsynchronizowane|Count|Łączna|Liczba plików synchronizowane|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Pliki nie są synchronizowane|Count|Łączna|Liczba plików synchronizowanie nie powiodło się|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Bajty synchronizowane|Bajty|Łączna|Całkowity rozmiar plików przesłanych dla sesji synchronizacji|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Stan online serwera|Count|Maksimum|Metryka dzienniki, a wartość 1 każdego pomyślnie czasu zarejestrowanego serwera rekordy pulsu z punktu końcowego w chmurze|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Wycofanie obsługi warstw w chmurze|Bajty|Łączna|Całkowity rozmiar danych przypomnieć przez serwer|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ResourceUtilization|% wykorzystania SU|Wartość procentowa|Maksimum|% wykorzystania SU|LogicalName, PartitionId|
|Liczba|Zdarzenia wejściowe|Count|Łączna|Zdarzenia wejściowe|LogicalName, PartitionId|
|InputEventBytes|Zdarzenia wejściowe (bajty)|Bajty|Łączna|Zdarzenia wejściowe (bajty)|LogicalName, PartitionId|
|LateInputEvents|Opóźnione zdarzenia wejściowe|Count|Łączna|Opóźnione zdarzenia wejściowe|LogicalName, PartitionId|
|Liczba zdarzeń wyjściowych|Zdarzenia wyjściowe|Count|Łączna|Zdarzenia wyjściowe|LogicalName, PartitionId|
|ConversionErrors|Błędy konwersji danych|Count|Łączna|Błędy konwersji danych|LogicalName, PartitionId|
|Błędy|Błędy w czasie wykonywania|Count|Łączna|Błędy w czasie wykonywania|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Zdarzenia poza kolejnością|Count|Łączna|Zdarzenia poza kolejnością|LogicalName, PartitionId|
|AMLCalloutRequests|Żądania funkcji|Count|Łączna|Żądania funkcji|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Żądania funkcji zakończone niepowodzeniem|Count|Łączna|Żądania funkcji zakończone niepowodzeniem|LogicalName, PartitionId|
|AMLCalloutInputEvents|Zdarzenia funkcji|Count|Łączna|Zdarzenia funkcji|LogicalName, PartitionId|
|DeserializationError|Błędy deserializacji danych wejściowych|Count|Łączna|Błędy deserializacji danych wejściowych|LogicalName, PartitionId|
|EarlyInputEvents|Wczesne zdarzenia wejściowe|Count|Łączna|Wczesne zdarzenia wejściowe|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Opóźnienie znaku wodnego|Sekundy|Maksimum|Opóźnienie znaku wodnego|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Zaległe zdarzenia wejściowe|Count|Maksimum|Zaległe zdarzenia wejściowe|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Odebrane źródła wejściowe|Count|Łączna|Odebrane źródła wejściowe|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ruch przychodzący odebranych komunikatów|Count|Łączna|Liczba komunikatów odczytywać wszystkie Centrum zdarzeń lub usługi IoT hub źródła zdarzeń|Nie wymiarów|
|IngressReceivedInvalidMessages|Ruch przychodzący Odebrano nieprawidłowy komunikaty|Count|Łączna|Liczba komunikatów nieprawidłowy odczytywać wszystkie Centrum zdarzeń lub usługi IoT hub źródła zdarzeń|Nie wymiarów|
|IngressReceivedBytes|Ruch przychodzący odebrały bajty|Bajty|Łączna|Liczba bajtów odczytanych ze wszystkich źródeł zdarzeń|Nie wymiarów|
|IngressStoredBytes|Ruch przychodzący przechowywane bajtów|Bajty|Łączna|Całkowity rozmiar zdarzeń pomyślnie przetworzone i dostępne dla zapytania|Nie wymiarów|
|IngressStoredEvents|Ruch przychodzący przechowywanych zdarzeń|Count|Łączna|Liczba zdarzeń spłaszczonych pomyślnie przetworzone i dostępne dla zapytania|Nie wymiarów|
|IngressReceivedMessagesTimeLag|Opóźnienie czasowe Odebrane komunikaty przychodzące|Sekundy|Maksimum|Różnica między czasem, który komunikat jest dodawanych do kolejki w źródle zdarzenia i czasu, gdy są przetwarzane w ruchu przychodzącego|Nie wymiarów|
|IngressReceivedMessagesCountLag|Opóźnienie liczba Odebrane komunikaty przychodzące|Count|Średnia|Różnica między liczba sekwencji ostatniej wiadomości w kolejce w zdarzeniu źródła partycji i numer sekwencji przetwarzanego w ruchu przychodzącego komunikatu|Nie wymiarów|
|WarmStorageMaxProperties|Właściwości Max magazynu bez wyłączania zasilania|Count|Maksimum|Maksymalna liczba właściwości używanych dozwolona przez środowisko dla S1/S2 SKU i maksymalną liczbę dozwoloną przez ciepło Store w ramach jednostki SKU PAYG właściwości|Nie wymiarów|
|WarmStorageUsedProperties|Magazynu ciepłego używanych właściwości |Count|Maksimum|Wiele właściwości, używany przez środowisko dla S1/S2 SKU i liczbę właściwości używanych przez Store ciepło PAYG jednostki SKU|Nie wymiarów|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ruch przychodzący odebranych komunikatów|Count|Łączna|Liczba komunikatów odczytu ze źródła zdarzeń|Nie wymiarów|
|IngressReceivedInvalidMessages|Ruch przychodzący Odebrano nieprawidłowy komunikaty|Count|Łączna|Liczba komunikatów Nieprawidłowy odczyt z źródła zdarzeń|Nie wymiarów|
|IngressReceivedBytes|Ruch przychodzący odebrały bajty|Bajty|Łączna|Liczba bajtów odczytanych ze źródła zdarzeń|Nie wymiarów|
|IngressStoredBytes|Ruch przychodzący przechowywane bajtów|Bajty|Łączna|Całkowity rozmiar zdarzeń pomyślnie przetworzone i dostępne dla zapytania|Nie wymiarów|
|IngressStoredEvents|Ruch przychodzący przechowywanych zdarzeń|Count|Łączna|Liczba zdarzeń spłaszczonych pomyślnie przetworzone i dostępne dla zapytania|Nie wymiarów|
|IngressReceivedMessagesTimeLag|Opóźnienie czasowe Odebrane komunikaty przychodzące|Sekundy|Maksimum|Różnica między czasem, który komunikat jest dodawanych do kolejki w źródle zdarzenia i czasu, gdy są przetwarzane w ruchu przychodzącego|Nie wymiarów|
|IngressReceivedMessagesCountLag|Opóźnienie liczba Odebrane komunikaty przychodzące|Count|Średnia|Różnica między liczba sekwencji ostatniej wiadomości w kolejce w zdarzeniu źródła partycji i numer sekwencji przetwarzanego w ruchu przychodzącego komunikatu|Nie wymiarów|
|WarmStorageMaxProperties|Właściwości Max magazynu bez wyłączania zasilania|Count|Maksimum|Maksymalna liczba właściwości używanych dozwolona przez środowisko dla S1/S2 SKU i maksymalną liczbę dozwoloną przez ciepło Store w ramach jednostki SKU PAYG właściwości|Nie wymiarów|
|WarmStorageUsedProperties|Magazynu ciepłego używanych właściwości |Count|Maksimum|Wiele właściwości, używany przez środowisko dla S1/S2 SKU i liczbę właściwości używanych przez Store ciepło PAYG jednostki SKU|Nie wymiarów|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Bajty odczytu dysku/s|Bajty na sekundę|Średnia|Przepływność dysku ze względu na operacje odczytu w okresie próbki.|Nie wymiarów|
|DiskWriteBytesPerSecond|Bajty zapisu dysku/s|Bajty na sekundę|Średnia|Przepływność dysku ze względu na operacje zapisu w okresie próbki.|Nie wymiarów|
|Bajty odczytane z dysku|Bajty odczytane z dysku|Bajty|Łączna|Łączna przepływność dysku z powodu operacji odczytu przez okres próbkowania.|Nie wymiarów|
|Bajty zapisane na dysku|Bajty zapisane na dysku|Bajty|Łączna|Łączna przepływność dysku z powodu operacji zapisu w okresie próbki.|Nie wymiarów|
|DiskReadOperations|Operacje odczytu z dysku|Count|Łączna|Liczba operacji We/Wy operacji odczytu w poprzednim okresie próbki. Należy pamiętać, że te operacje mogą być zmienną o rozmiarze.|Nie wymiarów|
|DiskWriteOperations|Operacje zapisu na dysku|Count|Łączna|Liczba operacji We/Wy operacji zapisu są w poprzednim okresie próbki. Należy pamiętać, że te operacje mogą być zmienną o rozmiarze.|Nie wymiarów|
|Operacje odczytu dysku/s|Operacje odczytu dysku/s|CountPerSecond|Średnia|Średnia liczba operacji We/Wy operacji odczytu są w poprzednim okresie próbki. Należy pamiętać, że te operacje mogą być zmienną o rozmiarze.|Nie wymiarów|
|Operacje zapisu dysku/s|Operacje zapisu dysku/s|CountPerSecond|Średnia|Średnia liczba operacji We/Wy operacji zapisu są w poprzednim okresie próbki. Należy pamiętać, że te operacje mogą być zmienną o rozmiarze.|Nie wymiarów|
|DiskReadLatency|Opóźnienie odczytu z dysku|Milisekundy|Średnia|Łączna liczba opóźnienie odczytu. Suma urządzenia i jądra odczytu opóźnienia.|Nie wymiarów|
|DiskWriteLatency|Opóźnienie zapisu na dysku|Milisekundy|Średnia|Czas oczekiwania, łączna liczba operacji zapisu. Suma urządzenia i jądra opóźnienia zapisu.|Nie wymiarów|
|NetworkInBytesPerSecond|Sieć w bajtach na sekundę|Bajty na sekundę|Średnia|Przepływność sieci średni odebranego ruchu.|Nie wymiarów|
|NetworkOutBytesPerSecond|Sieć się bajty/s|Bajty na sekundę|Średnia|Przepływność sieci średni przesyłane ruchu.|Nie wymiarów|
|Sieć — wejście|Sieć — wejście|Bajty|Łączna|Przepływność sieci łączna liczba odebranych ruchu.|Nie wymiarów|
|Sieć — wyjście|Sieć — wyjście|Bajty|Łączna|Przepływność sieci łączna liczba przesłanych ruchu.|Nie wymiarów|
|MemoryUsed|Pamięć użyta|Bajty|Średnia|Ilość pamięci komputera, który jest używany przez maszynę Wirtualną.|Nie wymiarów|
|MemoryGranted|Udzielone pamięci|Bajty|Średnia|Ilość pamięci, który został przyznany do maszyny Wirtualnej przez hosta. Nie otrzymuje pamięci do hosta, dopóki nie jest wspomnieliśmy, jeden raz i przyznanych pamięci może być wymieniane lub ballooned natychmiast, jeśli VMkernel wymaga pamięci.|Nie wymiarów|
|MemoryActive|Aktywna w pamięci|Bajty|Średnia|Ilość pamięci używanej przez maszynę Wirtualną w ciągu ostatnich niewielki przedział czasu. To jest "true" Liczba ilość pamięci maszyny Wirtualnej ma obecnie potrzeb. Dodatkowe, nieużywanej pamięci może być wymieniane lub ballooned bez wpływu na wydajność przez gościa.|Nie wymiarów|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Średnia|Użycie procesora CPU. Ta wartość jest zgłaszany w 100% reprezentujący wszystkich rdzeni procesora w systemie. Na przykład sposób 2 maszyny Wirtualnej przy użyciu 50% systemu cztery podstawowego całkowicie korzysta z dwóch rdzeni.|Nie wymiarów|
|PercentageCpuReady|Procent użycia Procesora gotowe|Milisekundy|Łączna|Czas gotowości jest czas wydania oczekiwanie na CPU(s) staną się dostępne w ciągu ostatnich interwał aktualizacji.|Nie wymiarów|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuPercentage|Procent użycia procesora CPU|Wartość procentowa|Średnia|Procent użycia procesora CPU|Wystąpienie|
|MemoryPercentage|Procent pamięci|Wartość procentowa|Średnia|Procent pamięci|Wystąpienie|
|DiskQueueLength|Długość kolejki dysku|Count|Średnia|Długość kolejki dysku|Wystąpienie|
|HttpQueueLength|Długość kolejki HTTP|Count|Średnia|Długość kolejki HTTP|Wystąpienie|
|BytesReceived|Dane wejściowe|Bajty|Łączna|Dane wejściowe|Wystąpienie|
|BytesSent|Dane wyjściowe|Bajty|Łączna|Dane wyjściowe|Wystąpienie|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (z wyjątkiem funkcji)

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuTime|Czas procesora CPU|Sekundy|Łączna|Czas procesora CPU|Wystąpienie|
|Żądania|Żądania|Count|Łączna|Żądania|Wystąpienie|
|BytesReceived|Dane wejściowe|Bajty|Łączna|Dane wejściowe|Wystąpienie|
|BytesSent|Dane wyjściowe|Bajty|Łączna|Dane wyjściowe|Wystąpienie|
|Http101|HTTP 101|Count|Łączna|HTTP 101|Wystąpienie|
|Http2xx|HTTP 2xx|Count|Łączna|HTTP 2xx|Wystąpienie|
|Http3xx|HTTP 3xx|Count|Łączna|HTTP 3xx|Wystąpienie|
|Http401|HTTP 401|Count|Łączna|HTTP 401|Wystąpienie|
|Http403|HTTP 403|Count|Łączna|HTTP 403|Wystąpienie|
|Http404|HTTP 404|Count|Łączna|HTTP 404|Wystąpienie|
|Http406|HTTP 406|Count|Łączna|HTTP 406|Wystąpienie|
|Http4xx|HTTP 4xx|Count|Łączna|HTTP 4xx|Wystąpienie|
|Http5xx|Błędy serwera HTTP|Count|Łączna|Błędy serwera HTTP|Wystąpienie|
|MemoryWorkingSet|Zestaw roboczy pamięci|Bajty|Średnia|Zestaw roboczy pamięci|Wystąpienie|
|AverageMemoryWorkingSet|Średni zestaw roboczy pamięci|Bajty|Średnia|Średni zestaw roboczy pamięci|Wystąpienie|
|AverageResponseTime|Średni czas odpowiedzi|Sekundy|Średnia|Średni czas odpowiedzi|Wystąpienie|
|AppConnections|Połączenia|Count|Średnia|Połączenia|Wystąpienie|
|Dojścia|Liczba dojść|Count|Średnia|Liczba dojść|Wystąpienie|
|Wątki|Liczba wątków|Count|Średnia|Liczba wątków|Wystąpienie|
|PrivateBytes|Bajty prywatne|Bajty|Średnia|Bajty prywatne|Wystąpienie|
|IoReadBytesPerSecond|Odczytane bajty We/Wy na sekundę|Bajty na sekundę|Łączna|Odczytane bajty We/Wy na sekundę|Wystąpienie|
|IoWriteBytesPerSecond|Zapisane bajty We/Wy na sekundę|Bajty na sekundę|Łączna|Zapisane bajty We/Wy na sekundę|Wystąpienie|
|IoOtherBytesPerSecond|Inne bajty We/Wy na sekundę|Bajty na sekundę|Łączna|Inne bajty We/Wy na sekundę|Wystąpienie|
|IoReadOperationsPerSecond|Inne operacje odczytu We/Wy na sekundę|Bajty na sekundę|Łączna|Inne operacje odczytu We/Wy na sekundę|Wystąpienie|
|IoWriteOperationsPerSecond|Operacje zapisu We/Wy na sekundę|Bajty na sekundę|Łączna|Operacje zapisu We/Wy na sekundę|Wystąpienie|
|IoOtherOperationsPerSecond|Inne operacje We/Wy na sekundę|Bajty na sekundę|Łączna|Inne operacje We/Wy na sekundę|Wystąpienie|
|RequestsInApplicationQueue|Żądania w kolejce aplikacji|Count|Średnia|Żądania w kolejce aplikacji|Wystąpienie|
|CurrentAssemblies|Aktualna liczba zestawów|Count|Średnia|Aktualna liczba zestawów|Wystąpienie|
|TotalAppDomains|Łączna liczba domen aplikacji|Count|Średnia|Łączna liczba domen aplikacji|Wystąpienie|
|TotalAppDomainsUnloaded|Łączna liczba nie załadowanych domen aplikacji|Count|Średnia|Łączna liczba nie załadowanych domen aplikacji|Wystąpienie|
|Gen0Collections|Zbieranie elementów bezużytecznych generacji 0|Count|Łączna|Zbieranie elementów bezużytecznych generacji 0|Wystąpienie|
|Gen1Collections|Zbieranie elementów bezużytecznych generacji 1|Count|Łączna|Zbieranie elementów bezużytecznych generacji 1|Wystąpienie|
|Gen2Collections|Zbieranie elementów bezużytecznych generacji 2|Count|Łączna|Zbieranie elementów bezużytecznych generacji 2|Wystąpienie|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funkcje)

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BytesReceived|Dane wejściowe|Bajty|Łączna|Dane wejściowe|Wystąpienie|
|BytesSent|Dane wyjściowe|Bajty|Łączna|Dane wyjściowe|Wystąpienie|
|Http5xx|Błędy serwera HTTP|Count|Łączna|Błędy serwera HTTP|Wystąpienie|
|MemoryWorkingSet|Zestaw roboczy pamięci|Bajty|Średnia|Zestaw roboczy pamięci|Wystąpienie|
|AverageMemoryWorkingSet|Średni zestaw roboczy pamięci|Bajty|Średnia|Średni zestaw roboczy pamięci|Wystąpienie|
|FunctionExecutionUnits|Jednostki wykonawcze funkcji|MB / MS|Łączna|[Jednostki wykonawcze funkcji](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Wystąpienie|
|FunctionExecutionCount|Liczba wykonań funkcji|Count|Łączna|Liczba wykonań funkcji|Wystąpienie|
|PrivateBytes|Bajty prywatne|Bajty|Średnia|Bajty prywatne|Wystąpienie|
|IoReadBytesPerSecond|Odczytane bajty We/Wy na sekundę|Bajty na sekundę|Łączna|Odczytane bajty We/Wy na sekundę|Wystąpienie|
|IoWriteBytesPerSecond|Zapisane bajty We/Wy na sekundę|Bajty na sekundę|Łączna|Zapisane bajty We/Wy na sekundę|Wystąpienie|
|IoOtherBytesPerSecond|Inne bajty We/Wy na sekundę|Bajty na sekundę|Łączna|Inne bajty We/Wy na sekundę|Wystąpienie|
|IoReadOperationsPerSecond|Inne operacje odczytu We/Wy na sekundę|Bajty na sekundę|Łączna|Inne operacje odczytu We/Wy na sekundę|Wystąpienie|
|IoWriteOperationsPerSecond|Operacje zapisu We/Wy na sekundę|Bajty na sekundę|Łączna|Operacje zapisu We/Wy na sekundę|Wystąpienie|
|IoOtherOperationsPerSecond|Inne operacje We/Wy na sekundę|Bajty na sekundę|Łączna|Inne operacje We/Wy na sekundę|Wystąpienie|
|RequestsInApplicationQueue|Żądania w kolejce aplikacji|Count|Średnia|Żądania w kolejce aplikacji|Wystąpienie|
|CurrentAssemblies|Aktualna liczba zestawów|Count|Średnia|Aktualna liczba zestawów|Wystąpienie|
|TotalAppDomains|Łączna liczba domen aplikacji|Count|Średnia|Łączna liczba domen aplikacji|Wystąpienie|
|TotalAppDomainsUnloaded|Łączna liczba nie załadowanych domen aplikacji|Count|Średnia|Łączna liczba nie załadowanych domen aplikacji|Wystąpienie|
|Gen0Collections|Zbieranie elementów bezużytecznych generacji 0|Count|Łączna|Zbieranie elementów bezużytecznych generacji 0|Wystąpienie|
|Gen1Collections|Zbieranie elementów bezużytecznych generacji 1|Count|Łączna|Zbieranie elementów bezużytecznych generacji 1|Wystąpienie|
|Gen2Collections|Zbieranie elementów bezużytecznych generacji 2|Count|Łączna|Zbieranie elementów bezużytecznych generacji 2|Wystąpienie|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuTime|Czas procesora CPU|Sekundy|Łączna|Czas procesora CPU|Wystąpienie|
|Żądania|Żądania|Count|Łączna|Żądania|Wystąpienie|
|BytesReceived|Dane wejściowe|Bajty|Łączna|Dane wejściowe|Wystąpienie|
|BytesSent|Dane wyjściowe|Bajty|Łączna|Dane wyjściowe|Wystąpienie|
|Http101|HTTP 101|Count|Łączna|HTTP 101|Wystąpienie|
|Http2xx|HTTP 2xx|Count|Łączna|HTTP 2xx|Wystąpienie|
|Http3xx|HTTP 3xx|Count|Łączna|HTTP 3xx|Wystąpienie|
|Http401|HTTP 401|Count|Łączna|HTTP 401|Wystąpienie|
|Http403|HTTP 403|Count|Łączna|HTTP 403|Wystąpienie|
|Http404|HTTP 404|Count|Łączna|HTTP 404|Wystąpienie|
|Http406|HTTP 406|Count|Łączna|HTTP 406|Wystąpienie|
|Http4xx|HTTP 4xx|Count|Łączna|HTTP 4xx|Wystąpienie|
|Http5xx|Błędy serwera HTTP|Count|Łączna|Błędy serwera HTTP|Wystąpienie|
|MemoryWorkingSet|Zestaw roboczy pamięci|Bajty|Średnia|Zestaw roboczy pamięci|Wystąpienie|
|AverageMemoryWorkingSet|Średni zestaw roboczy pamięci|Bajty|Średnia|Średni zestaw roboczy pamięci|Wystąpienie|
|AverageResponseTime|Średni czas odpowiedzi|Sekundy|Średnia|Średni czas odpowiedzi|Wystąpienie|
|FunctionExecutionUnits|Jednostki wykonawcze funkcji|Count|Łączna|Jednostki wykonawcze funkcji|Wystąpienie|
|FunctionExecutionCount|Liczba wykonań funkcji|Count|Łączna|Liczba wykonań funkcji|Wystąpienie|
|AppConnections|Połączenia|Count|Średnia|Połączenia|Wystąpienie|
|Dojścia|Liczba dojść|Count|Średnia|Liczba dojść|Wystąpienie|
|Wątki|Liczba wątków|Count|Średnia|Liczba wątków|Wystąpienie|
|PrivateBytes|Bajty prywatne|Bajty|Średnia|Bajty prywatne|Wystąpienie|
|IoReadBytesPerSecond|Odczytane bajty We/Wy na sekundę|Bajty na sekundę|Łączna|Odczytane bajty We/Wy na sekundę|Wystąpienie|
|IoWriteBytesPerSecond|Zapisane bajty We/Wy na sekundę|Bajty na sekundę|Łączna|Zapisane bajty We/Wy na sekundę|Wystąpienie|
|IoOtherBytesPerSecond|Inne bajty We/Wy na sekundę|Bajty na sekundę|Łączna|Inne bajty We/Wy na sekundę|Wystąpienie|
|IoReadOperationsPerSecond|Inne operacje odczytu We/Wy na sekundę|Bajty na sekundę|Łączna|Inne operacje odczytu We/Wy na sekundę|Wystąpienie|
|IoWriteOperationsPerSecond|Operacje zapisu We/Wy na sekundę|Bajty na sekundę|Łączna|Operacje zapisu We/Wy na sekundę|Wystąpienie|
|IoOtherOperationsPerSecond|Inne operacje We/Wy na sekundę|Bajty na sekundę|Łączna|Inne operacje We/Wy na sekundę|Wystąpienie|
|RequestsInApplicationQueue|Żądania w kolejce aplikacji|Count|Średnia|Żądania w kolejce aplikacji|Wystąpienie|
|CurrentAssemblies|Aktualna liczba zestawów|Count|Średnia|Aktualna liczba zestawów|Wystąpienie|
|TotalAppDomains|Łączna liczba domen aplikacji|Count|Średnia|Łączna liczba domen aplikacji|Wystąpienie|
|TotalAppDomainsUnloaded|Łączna liczba nie załadowanych domen aplikacji|Count|Średnia|Łączna liczba nie załadowanych domen aplikacji|Wystąpienie|
|Gen0Collections|Zbieranie elementów bezużytecznych generacji 0|Count|Łączna|Zbieranie elementów bezużytecznych generacji 0|Wystąpienie|
|Gen1Collections|Zbieranie elementów bezużytecznych generacji 1|Count|Łączna|Zbieranie elementów bezużytecznych generacji 1|Wystąpienie|
|Gen2Collections|Zbieranie elementów bezużytecznych generacji 2|Count|Łączna|Zbieranie elementów bezużytecznych generacji 2|Wystąpienie|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Żądania|Żądania|Count|Łączna|Żądania|Wystąpienie|
|BytesReceived|Dane wejściowe|Bajty|Łączna|Dane wejściowe|Wystąpienie|
|BytesSent|Dane wyjściowe|Bajty|Łączna|Dane wyjściowe|Wystąpienie|
|Http101|HTTP 101|Count|Łączna|HTTP 101|Wystąpienie|
|Http2xx|HTTP 2xx|Count|Łączna|HTTP 2xx|Wystąpienie|
|Http3xx|HTTP 3xx|Count|Łączna|HTTP 3xx|Wystąpienie|
|Http401|HTTP 401|Count|Łączna|HTTP 401|Wystąpienie|
|Http403|HTTP 403|Count|Łączna|HTTP 403|Wystąpienie|
|Http404|HTTP 404|Count|Łączna|HTTP 404|Wystąpienie|
|Http406|HTTP 406|Count|Łączna|HTTP 406|Wystąpienie|
|Http4xx|HTTP 4xx|Count|Łączna|HTTP 4xx|Wystąpienie|
|Http5xx|Błędy serwera HTTP|Count|Łączna|Błędy serwera HTTP|Wystąpienie|
|AverageResponseTime|Średni czas odpowiedzi|Sekundy|Średnia|Średni czas odpowiedzi|Wystąpienie|
|CpuPercentage|Procent użycia procesora CPU|Wartość procentowa|Średnia|Procent użycia procesora CPU|Wystąpienie|
|MemoryPercentage|Procent pamięci|Wartość procentowa|Średnia|Procent pamięci|Wystąpienie|
|DiskQueueLength|Długość kolejki dysku|Count|Średnia|Długość kolejki dysku|Wystąpienie|
|HttpQueueLength|Długość kolejki HTTP|Count|Średnia|Długość kolejki HTTP|Wystąpienie|
|ActiveRequests|Aktywne żądania|Count|Łączna|Aktywne żądania|Wystąpienie|
|TotalFrontEnds|Łącznie frontonów|Count|Średnia|Łącznie frontonów|Nie wymiarów|
|SmallAppServicePlanInstances|Mała liczba procesów roboczych dla planu usługi App Service|Count|Średnia|Mała liczba procesów roboczych dla planu usługi App Service|Nie wymiarów|
|MediumAppServicePlanInstances|Średnia liczba procesów roboczych dla planu usługi App Service|Count|Średnia|Średnia liczba procesów roboczych dla planu usługi App Service|Nie wymiarów|
|LargeAppServicePlanInstances|Duża liczba procesów roboczych dla planu usługi App Service|Count|Średnia|Duża liczba procesów roboczych dla planu usługi App Service|Nie wymiarów|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Łączna liczba procesów roboczych|Łączna liczba procesów roboczych|Count|Średnia|Łączna liczba procesów roboczych|Nie wymiarów|
|WorkersAvailable|Dostępne procesy robocze|Count|Średnia|Dostępne procesy robocze|Nie wymiarów|
|WorkersUsed|Używane procesy robocze|Count|Średnia|Używane procesy robocze|Nie wymiarów|
|CpuPercentage|Procent użycia procesora CPU|Wartość procentowa|Średnia|Procent użycia procesora CPU|Wystąpienie|
|MemoryPercentage|Procent pamięci|Wartość procentowa|Średnia|Procent pamięci|Wystąpienie|

## <a name="next-steps"></a>Kolejne kroki
* [Przeczytaj o metrykach w usłudze Azure Monitor](data-platform.md)
* [Tworzenie alertów dotyczących metryk](alerts-overview.md)
* [Eksportowanie metryk do magazynu, Centrum zdarzeń lub usługi Log Analytics](diagnostic-logs-overview.md)
