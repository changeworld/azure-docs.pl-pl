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
ms.openlocfilehash: ba3535679f37916a18aae5fe7dbe4e9114cea695
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2019
ms.locfileid: "74664917"
---
# <a name="supported-metrics-with-azure-monitor"></a>Obsługiwane metryki z Azure Monitor

Azure Monitor oferuje kilka sposobów współpracy z metrykami, w tym wykresów w portalu, uzyskiwania do nich dostępu za pośrednictwem interfejsu API REST lub wykonywania zapytań przy użyciu programu PowerShell lub interfejsu wiersza polecenia. Poniżej znajduje się pełna lista wszystkich metryk dostępnych obecnie z potoku metryk Azure Monitor. Inne metryki mogą być dostępne w portalu lub przy użyciu starszych interfejsów API. Ta lista zawiera tylko metryki dostępne przy użyciu potoku metryki Azure Monitor skonsolidowanej. Aby wykonać zapytanie o i uzyskać dostęp do tych metryk, użyj [wersji interfejsu api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Wysyłanie metryk wielowymiarowych za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
>
> *Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak w przypadku eksportowania za pomocą ustawień diagnostycznych metryka ta jest przedstawiana jako wszystkie komunikaty przychodzące we wszystkich kolejkach w centrum zdarzeń.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/serwery

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|qpu_metric|QPU|Liczba|Średnia|QPU. Zakres 0-100 dla S1, 0-200 dla S2 i 0-400 dla S4|ServerResourceType|
|memory_metric|Pamięć|Szybkość|Średnia|Rozmiar. Zakres 0-25 GB dla S1, 0-50 GB dla S2 i 0-100 GB dla S4|ServerResourceType|
|private_bytes_metric|Bajty prywatne |Szybkość|Średnia|Całkowita ilość pamięci przydzielona przez proces aparatu Analysis Services i procesy kontenera mashupów, bez uwzględnienia pamięci współdzielonej z innymi procesami.|ServerResourceType|
|virtual_bytes_metric|Bajty wirtualne |Szybkość|Średnia|Bieżący rozmiar wirtualnej przestrzeni adresowej używanej przez proces aparatu i procesy kontenera mashupów Analysis Services.|ServerResourceType|
|TotalConnectionRequests|Łączna liczba żądań połączenia|Liczba|Średnia|Łączna liczba żądań połączenia. Są to wejścia.|ServerResourceType|
|SuccessfullConnectionsPerSec|Udane połączenia na sekundę|CountPerSecond|Średnia|Szybkość pomyślnych połączeń.|ServerResourceType|
|TotalConnectionFailures|Łączna liczba błędów połączenia|Liczba|Średnia|Łączna liczba nieudanych prób połączenia.|ServerResourceType|
|CurrentUserSessions|Bieżące sesje użytkowników|Liczba|Średnia|Bieżąca liczba ustanowionych sesji użytkowników.|ServerResourceType|
|QueryPoolBusyThreads|Wątki zajęte w puli zapytań|Liczba|Średnia|Liczba zajętych wątków w puli wątków zapytań.|ServerResourceType|
|CommandPoolJobQueueLength|Długość kolejki zadań puli poleceń|Liczba|Średnia|Liczba zadań w kolejce puli wątków poleceń.|ServerResourceType|
|ProcessingPoolJobQueueLength|Długość kolejki zadań puli przetwarzania|Liczba|Średnia|Liczba zadań innych niż zadania we/wy w kolejce puli wątków przetwarzania.|ServerResourceType|
|Wartości CurrentConnections|Połączenie: bieżące połączenia|Liczba|Średnia|Bieżąca liczba ustanowionych połączeń klientów.|ServerResourceType|
|CleanerCurrentPrice|Pamięć: bieżąca cena oczyszczarki|Liczba|Średnia|Bieżąca cena pamięci, $/Byte/Time, znormalizowana do 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Pamięć: zmniejszanie ilości pamięci czyszczącej|Szybkość|Średnia|Ilość pamięci, w bajtach, podlegająca przeczyszczaniu przez oczyszczarkę w tle.|ServerResourceType|
|CleanerMemoryNonshrinkable|Pamięć: pamięć oczyszczarki niemożliwa do zmniejszenia|Szybkość|Średnia|Ilość pamięci (w bajtach), która nie podlega wyprzeczyszczaniu przez oczyszczarkę w tle.|ServerResourceType|
|MemoryUsage|Pamięć: użycie pamięci|Szybkość|Średnia|Użycie pamięci przez proces serwera jako używany do obliczania ceny pamięci czyszczącej. Równa się licznikowi Process\PrivateBytes i rozmiarowi danych mapowanych na pamięć, ignorowanie wszelkich pamięci, które zostały zamapowane lub przydzielone przez aparat analityczny w pamięci xVelocity (VertiPaq) przekraczający limit pamięci aparatu xVelocity.|ServerResourceType|
|MemoryLimitHard|Pamięć: sztywny limit pamięci|Szybkość|Średnia|Limit pamięci twardej z pliku konfiguracji.|ServerResourceType|
|MemoryLimitHigh|Pamięć: limit pamięci jest wysoki|Szybkość|Średnia|Górny limit pamięci z pliku konfiguracji.|ServerResourceType|
|MemoryLimitLow|Pamięć: limit pamięci — niski|Szybkość|Średnia|Limit braku pamięci z pliku konfiguracji.|ServerResourceType|
|MemoryLimitVertiPaq|Pamięć: limit pamięci — tryb VertiPaq|Szybkość|Średnia|Limit w pamięci z pliku konfiguracji.|ServerResourceType|
|Przydział|Pamięć: limit przydziału|Szybkość|Średnia|Bieżący limit pamięci (w bajtach). Przydział pamięci jest również nazywany przydziałem pamięci lub rezerwacją pamięci.|ServerResourceType|
|QuotaBlocked|Pamięć: zablokowany limit przydziału|Liczba|Średnia|Bieżąca liczba żądań przydziałów blokowanych do momentu zwolnienia innych limitów przydziału pamięci.|ServerResourceType|
|VertiPaqNonpaged|Pamięć: VertiPaq niestronicowana|Szybkość|Średnia|Bajty pamięci zablokowane w zestawie roboczym do użycia przez aparat w pamięci.|ServerResourceType|
|VertiPaqPaged|Pamięć: stronicowana na stronie VertiPaq|Szybkość|Średnia|Bajty stronicowanej pamięci w użyciu dla danych w pamięci.|ServerResourceType|
|RowsReadPerSec|Przetwarzanie: odczytane wiersze na sekundę|CountPerSecond|Średnia|Współczynnik wierszy odczytanych ze wszystkich relacyjnych baz danych.|ServerResourceType|
|RowsConvertedPerSec|Przetwarzanie: przekonwertowane wiersze na sekundę|CountPerSecond|Średnia|Szybkość wierszy przekonwertowanych podczas przetwarzania.|ServerResourceType|
|RowsWrittenPerSec|Przetwarzanie: liczba wierszy na sekundę|CountPerSecond|Średnia|Częstotliwość wierszy zapisywana podczas przetwarzania.|ServerResourceType|
|CommandPoolBusyThreads|Wątki: zajęte wątki w puli poleceń|Liczba|Średnia|Liczba zajętych wątków w puli wątków poleceń.|ServerResourceType|
|CommandPoolIdleThreads|Wątki: bezczynne wątki w puli poleceń|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków poleceń.|ServerResourceType|
|LongParsingBusyThreads|Wątki: zajęte wątki o długotrwałej analizie|Liczba|Średnia|Liczba zajętych wątków w puli wątków o długotrwałej analizie.|ServerResourceType|
|LongParsingIdleThreads|Wątki: długotrwałe wątki bezczynne analizy|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków o długotrwałej analizie.|ServerResourceType|
|LongParsingJobQueueLength|Wątki: Długość kolejki zadań o długotrwałej analizie|Liczba|Średnia|Liczba zadań w kolejce puli wątków o długotrwałej analizie.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Wątki: wątki zadań we/wy puli przetwarzania|Liczba|Średnia|Liczba wątków uruchamiających zadania we/wy w puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Wątki: zajęte wątki innych niż we/wy puli przetwarzania|Liczba|Średnia|Liczba wątków uruchamiających zadania inne niż we/wy w puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Wątki: Długość kolejki zadań we/wy puli przetwarzania|Liczba|Średnia|Liczba zadań we/wy w kolejce puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Wątki: wątki zadań we/wy puli przetwarzania|Liczba|Średnia|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Wątki: bezczynne wątki inne niż we/wy puli przetwarzania|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków przetwarzania dedykowanych dla zadań innych niż zadania we/wy.|ServerResourceType|
|QueryPoolIdleThreads|Wątki: bezczynne wątki puli zapytań|Liczba|Średnia|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|ServerResourceType|
|QueryPoolJobQueueLength|Wątki: Długość kolejki zadań puli zapytań|Liczba|Średnia|Liczba zadań w kolejce puli wątków zapytań.|ServerResourceType|
|ShortParsingBusyThreads|Wątki: zajęte wątki z krótkim analizowaniem|Liczba|Średnia|Liczba zajętych wątków w puli wątków o krótkiej analizie.|ServerResourceType|
|ShortParsingIdleThreads|Wątki: bezczynne wątki z krótkim analizowaniem|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków o krótkiej analizie.|ServerResourceType|
|ShortParsingJobQueueLength|Wątki: Długość kolejki zadań o krótkiej analizie|Liczba|Średnia|Liczba zadań w kolejce puli wątków o krótkiej analizie.|ServerResourceType|
|memory_thrashing_metric|Migotanie pamięci|Procent|Średnia|Średnia pamięć migotanie.|ServerResourceType|
|mashup_engine_qpu_metric|M QPU aparatu|Liczba|Średnia|QPU użycie przez procesy aparatu mashupów|ServerResourceType|
|mashup_engine_memory_metric|Pamięć aparatu M|Szybkość|Średnia|Użycie pamięci przez procesy aparatu mashupów|ServerResourceType|
|mashup_engine_private_bytes_metric|Liczba prywatnych bajtów aparatu M |Szybkość|Średnia|Łączna ilość pamięci w procesach kontenera programu mashup pamięć została przydzielona, bez uwzględnienia pamięci współdzielonej z innymi procesami.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Liczba bajtów wirtualnych aparatu M |Szybkość|Średnia|Bieżący rozmiar wirtualnej przestrzeni adresowej, z której korzystają procesy kontenera mashupu.|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Żądania|Żądania|Liczba|Łącznie|Całkowita liczba żądań bramy w danym okresie. Można je podzielić na różne wymiary, aby ułatwić diagnozowanie problemów. |Location, BackendResponseCode, LastErrorReason, GatewayResponseCode|
|TotalRequests|Łączna liczba żądań bramy|Liczba|Łącznie|Całkowita liczba żądań bramy w danym okresie. Ta Metryka jest przestarzała, zalecamy użycie nowej metryki `Requests`. |Lokalizacja, nazwa hosta|
|Żądania successfulrequests|Pomyślne żądania bramy|Liczba|Łącznie|Całkowita liczba pomyślnych żądań bramy w danym okresie. Ta Metryka jest przestarzała, zalecamy użycie nowej metryki `Requests`.|Lokalizacja, nazwa hosta|
|UnauthorizedRequests|Nieautoryzowane żądania bramy|Liczba|Łącznie| Całkowita liczba nieautoryzowanych żądań bramy w danym okresie. Ta Metryka jest przestarzała, zalecamy użycie nowej metryki `Requests`.|Lokalizacja, nazwa hosta|
|FailedRequests|Nieudane żądania bramy|Liczba|Łącznie|Całkowita liczba żądań bramy zakończonych niepowodzeniem w danym okresie. Ta Metryka jest przestarzała, zalecamy użycie nowej metryki `Requests`.|Lokalizacja, nazwa hosta|
|OtherRequests|Inne żądania bramy|Liczba|Łącznie|Całkowita liczba żądań bramy w danym okresie, które nie znajdują się w kategorii pomyślne, nieautoryzowane lub niepowodzenie. Ta Metryka jest przestarzała, zalecamy użycie nowej metryki `Requests`. |Lokalizacja, nazwa hosta|
|Czas trwania|Całkowity czas trwania żądań bramy|)|Średnia|Czas między API Management odbiera żądanie od klienta i zwraca odpowiedź do klienta.|Lokalizacja, nazwa hosta|
|Pojemność|Pojemność|Procent|Średnia|Wskaźnik obciążenia API Management wystąpienia w celu podejmowania świadomych decyzji o tym, czy skalować wystąpienie w celu zwiększenia obciążenia.|Lokalizacja|
|EventHubTotalEvents|Łączna liczba zdarzeń EventHub|Liczba|Łącznie|Całkowita liczba zdarzeń wysyłanych do centrum EventHub z API Management w danym okresie.|Lokalizacja|
|EventHubSuccessfulEvents|Pomyślne zdarzenia EventHub|Liczba|Łącznie|Całkowita liczba pomyślnych zdarzeń EventHub w danym okresie.|Lokalizacja|
|EventHubTotalFailedEvents|Niepowodzenie zdarzeń EventHub|Liczba|Łącznie|Całkowita liczba nieudanych zdarzeń EventHub w danym okresie.|Lokalizacja|
|EventHubRejectedEvents|Odrzucone zdarzenia EventHub|Liczba|Łącznie|Całkowita liczba odrzuconych zdarzeń EventHub (niewłaściwa konfiguracja lub nieautoryzowana) w danym okresie.|Lokalizacja|
|EventHubThrottledEvents|Ograniczone zdarzenia EventHub|Liczba|Łącznie|Całkowita liczba zdarzeń zdarzenia EventHub z ograniczeniami w danym okresie.|Lokalizacja|
|EventHubTimedoutEvents|Przekroczono limit czasu zdarzeń EventHub|Liczba|Łącznie|Całkowita liczba przekroczeń limitu czasu zdarzeń EventHub w danym okresie.|Lokalizacja|
|EventHubDroppedEvents|Opuszczone zdarzenia EventHub|Liczba|Łącznie|Łączna liczba zdarzeń pominiętych ze względu na limit rozmiaru kolejki osiągnięty w danym okresie.|Lokalizacja|
|EventHubTotalBytesSent|Rozmiar zdarzeń EventHub|Szybkość|Łącznie|Łączny rozmiar zdarzeń EventHub w bajtach w danym okresie.|Lokalizacja|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalJob|Łączna liczba zadań|Liczba|Łącznie|Całkowita liczba zadań|Element Runbook, stan|
|TotalUpdateDeploymentRuns|Łączna liczba przebiegów wdrożenia aktualizacji|Liczba|Łącznie|Łączna liczba przebiegów wdrażania aktualizacji oprogramowania|. Softwareupdateconfigurationname, stan|
|TotalUpdateDeploymentMachineRuns|Łączna liczba uruchomień maszyn wdrożenia aktualizacji|Liczba|Łącznie|Łączna liczba uruchomień maszyn wdrożenia aktualizacji oprogramowania w ramach wdrożenia aktualizacji oprogramowania|. Softwareupdateconfigurationname, status, TargetComputer,. Softwareupdateconfigurationrunid|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft. Batch/batchAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CoreCount|Liczba dedykowanych rdzeni|Liczba|Łącznie|Łączna liczba rdzeni dedykowanych na koncie wsadowym|Brak wymiarów|
|TotalNodeCount|Liczba dedykowanych węzłów|Liczba|Łącznie|Łączna liczba węzłów dedykowanych na koncie wsadowym|Brak wymiarów|
|LowPriorityCoreCount|LowPriority rdzeń|Liczba|Łącznie|Łączna liczba rdzeni o niskim priorytecie na koncie wsadowym|Brak wymiarów|
|TotalLowPriorityNodeCount|Liczba węzłów o niskim priorytecie|Liczba|Łącznie|Łączna liczba węzłów o niskim priorytecie na koncie wsadowym|Brak wymiarów|
|CreatingNodeCount|Tworzenie liczby węzłów|Liczba|Łącznie|Liczba tworzonych węzłów|Brak wymiarów|
|StartingNodeCount|Początkowa liczba węzłów|Liczba|Łącznie|Liczba początkowych węzłów|Brak wymiarów|
|WaitingForStartTaskNodeCount|Oczekiwanie na liczbę węzłów zadania uruchamiania|Liczba|Łącznie|Liczba węzłów oczekujących na zakończenie zadania uruchamiania|Brak wymiarów|
|StartTaskFailedNodeCount|Uruchamianie zadania nie powiodło się liczba węzłów|Liczba|Łącznie|Liczba węzłów, w których zadanie uruchomieniowe nie powiodło się|Brak wymiarów|
|IdleNodeCount|Liczba węzłów bezczynności|Liczba|Łącznie|Liczba bezczynnych węzłów|Brak wymiarów|
|OfflineNodeCount|Liczba węzłów w trybie offline|Liczba|Łącznie|Liczba węzłów offline|Brak wymiarów|
|RebootingNodeCount|Ponowny rozruch liczby węzłów|Liczba|Łącznie|Liczba ponownych uruchomień węzłów|Brak wymiarów|
|ReimagingNodeCount|Liczba węzłów regraficznych|Liczba|Łącznie|Liczba węzłów regraficznych|Brak wymiarów|
|RunningNodeCount|Liczba uruchomionych węzłów|Liczba|Łącznie|Liczba uruchomionych węzłów|Brak wymiarów|
|LeavingPoolNodeCount|Opuszczanie liczby węzłów puli|Liczba|Łącznie|Liczba węzłów opuszczających pulę|Brak wymiarów|
|UnusableNodeCount|Liczba węzłów, których nie można użyć|Liczba|Łącznie|Liczba nieużywanych węzłów|Brak wymiarów|
|PreemptedNodeCount|Liczba przeniesiona węzłów|Liczba|Łącznie|Liczba przeniesiona węzłów|Brak wymiarów|
|TaskStartEvent|Zdarzenia uruchamiania zadania|Liczba|Łącznie|Łączna liczba rozpoczętych zadań|Brak wymiarów|
|TaskCompleteEvent|Zdarzenia ukończenia zadania|Liczba|Łącznie|Całkowita liczba ukończonych zadań|Brak wymiarów|
|TaskFailEvent|Zdarzenia błędów zadań|Liczba|Łącznie|Łączna liczba zadań zakończonych niepowodzeniem w stanie niepowodzenia|Brak wymiarów|
|PoolCreateEvent|Zdarzenia tworzenia puli|Liczba|Łącznie|Łączna liczba utworzonych pul|Brak wymiarów|
|PoolResizeStartEvent|Zdarzenia rozpoczęcia zmiany rozmiaru puli|Liczba|Łącznie|Łączna liczba rozpoczętych rozmiarów puli|Brak wymiarów|
|PoolResizeCompleteEvent|Zdarzenia ukończenia zmiany rozmiaru puli|Liczba|Łącznie|Łączna liczba zakończono zmienionych rozmiarów puli|Brak wymiarów|
|PoolDeleteStartEvent|Zdarzenia uruchamiania usuwania puli|Liczba|Łącznie|Łączna liczba uruchomionych usunięć pul|Brak wymiarów|
|PoolDeleteCompleteEvent|Zdarzenia ukończenia usuwania puli|Liczba|Łącznie|Łączna liczba ukończonych usunięć pul|Brak wymiarów|
|JobDeleteCompleteEvent|Zdarzenia ukończenia usuwania zadania|Liczba|Łącznie|Całkowita liczba pomyślnie usuniętych zadań.|Brak wymiarów|
|JobDeleteStartEvent|Zdarzenia rozpoczęcia usuwania zadania|Liczba|Łącznie|Łączna liczba zadań, które zostały zażądane do usunięcia.|Brak wymiarów|
|JobDisableCompleteEvent|Zdarzenia ukończenia wyłączania zadania|Liczba|Łącznie|Łączna liczba zadań, które zostały pomyślnie wyłączone.|Brak wymiarów|
|JobDisableStartEvent|Wyłącz zdarzenia uruchamiania dla zadania|Liczba|Łącznie|Łączna liczba zadań, które zostały zlecone do wyłączenia.|Brak wymiarów|
|JobStartEvent|Zdarzenia uruchamiania zadania|Liczba|Łącznie|Łączna liczba zadań, które zostały pomyślnie uruchomione.|Brak wymiarów|
|JobTerminateCompleteEvent|Zdarzenia ukończenia kończenia zadania|Liczba|Łącznie|Łączna liczba zadań zakończonych pomyślnie.|Brak wymiarów|
|JobTerminateStartEvent|Zdarzenia rozpoczęcia zakończenia zadania|Liczba|Łącznie|Całkowita liczba zadań, które zostały zażądane do zakończenia.|Brak wymiarów|

## <a name="microsoftcacheredis"></a>Microsoft. cache/Redis

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|connectedclients|Połączeni klienci|Liczba|Maksimum||ShardId|
|totalcommandsprocessed|Łączna liczba operacji|Liczba|Łącznie||ShardId|
|cachehits|Trafienia pamięci podręcznej|Liczba|Łącznie||ShardId|
|cachemisses|Chybienia w pamięci podręcznej|Liczba|Łącznie||ShardId|
|GetCommands|Kto|Liczba|Łącznie||ShardId|
|SetCommands|Przywraca|Liczba|Łącznie||ShardId|
|operationsPerSecond|Liczba operacji na sekundę|Liczba|Maksimum||ShardId|
|evictedkeys|Wykluczone klucze|Liczba|Łącznie||ShardId|
|totalkeys|Łączna liczba kluczy|Liczba|Maksimum||ShardId|
|expiredkeys|Wygasłe klucze|Liczba|Łącznie||ShardId|
|usedmemory|Używana pamięć|Szybkość|Maksimum||ShardId|
|usedmemorypercentage|Wartość procentowa używanej pamięci|Procent|Maksimum||ShardId|
|usedmemoryRss|Używana pamięć RSS|Szybkość|Maksimum||ShardId|
|serverLoad|Obciążenie serwera|Procent|Maksimum||ShardId|
|cacheWrite|Zapis pamięci podręcznej|BytesPerSecond|Maksimum||ShardId|
|cacheRead|Odczyt pamięci podręcznej|BytesPerSecond|Maksimum||ShardId|
|percentProcessorTime|Procesor CPU|Procent|Maksimum||ShardId|
|cacheLatency|Mikrosekundy opóźnienia pamięci podręcznej (wersja zapoznawcza)|Liczba|Średnia||ShardId, Sampletype|
|błędy|Błędy|Liczba|Maksimum||ShardId, ErrorType|
|connectedclients0|Połączeni klienci (fragmentu 0)|Liczba|Maksimum||Brak wymiarów|
|totalcommandsprocessed0|Łączna liczba operacji (fragmentu 0)|Liczba|Łącznie||Brak wymiarów|
|cachehits0|Trafienia pamięci podręcznej (fragmentu 0)|Liczba|Łącznie||Brak wymiarów|
|cachemisses0|Chybienia w pamięci podręcznej (fragmentu 0)|Liczba|Łącznie||Brak wymiarów|
|getcommands0|Pobiera (fragmentu 0)|Liczba|Łącznie||Brak wymiarów|
|setcommands0|Zestawy (fragmentu 0)|Liczba|Łącznie||Brak wymiarów|
|operationsPerSecond0|Operacje na sekundę (fragmentu 0)|Liczba|Maksimum||Brak wymiarów|
|evictedkeys0|Wykluczone klucze (fragmentu 0)|Liczba|Łącznie||Brak wymiarów|
|totalkeys0|Łączna liczba kluczy (fragmentu 0)|Liczba|Maksimum||Brak wymiarów|
|expiredkeys0|Wygasłe klucze (fragmentu 0)|Liczba|Łącznie||Brak wymiarów|
|usedmemory0|Używana pamięć (fragmentu 0)|Szybkość|Maksimum||Brak wymiarów|
|usedmemoryRss0|Używana pamięć RSS (fragmentu 0)|Szybkość|Maksimum||Brak wymiarów|
|serverLoad0|Ładowanie serwera (fragmentu 0)|Procent|Maksimum||Brak wymiarów|
|cacheWrite0|Zapis pamięci podręcznej (fragmentu 0)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead0|Odczyt pamięci podręcznej (fragmentu 0)|BytesPerSecond|Maksimum||Brak wymiarów|
|percentProcessorTime0|Procesor CPU (fragmentu 0)|Procent|Maksimum||Brak wymiarów|
|connectedclients1|Połączeni klienci (fragmentu 1)|Liczba|Maksimum||Brak wymiarów|
|totalcommandsprocessed1|Łączna liczba operacji (fragmentu 1)|Liczba|Łącznie||Brak wymiarów|
|cachehits1|Trafienia pamięci podręcznej (fragmentu 1)|Liczba|Łącznie||Brak wymiarów|
|cachemisses1|Chybienia w pamięci podręcznej (fragmentu 1)|Liczba|Łącznie||Brak wymiarów|
|getcommands1|Pobiera (fragmentu 1)|Liczba|Łącznie||Brak wymiarów|
|setcommands1|Zestawy (fragmentu 1)|Liczba|Łącznie||Brak wymiarów|
|operationsPerSecond1|Operacje na sekundę (fragmentu 1)|Liczba|Maksimum||Brak wymiarów|
|evictedkeys1|Wykluczone klucze (fragmentu 1)|Liczba|Łącznie||Brak wymiarów|
|totalkeys1|Łączna liczba kluczy (fragmentu 1)|Liczba|Maksimum||Brak wymiarów|
|expiredkeys1|Wygasłe klucze (fragmentu 1)|Liczba|Łącznie||Brak wymiarów|
|usedmemory1|Używana pamięć (fragmentu 1)|Szybkość|Maksimum||Brak wymiarów|
|usedmemoryRss1|Używana pamięć RSS (fragmentu 1)|Szybkość|Maksimum||Brak wymiarów|
|serverLoad1|Ładowanie serwera (fragmentu 1)|Procent|Maksimum||Brak wymiarów|
|cacheWrite1|Zapisywanie pamięci podręcznej (fragmentu 1)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead1|Odczyt pamięci podręcznej (fragmentu 1)|BytesPerSecond|Maksimum||Brak wymiarów|
|percentProcessorTime1|Procesor CPU (fragmentu 1)|Procent|Maksimum||Brak wymiarów|
|connectedclients2|Połączeni klienci (fragmentu 2)|Liczba|Maksimum||Brak wymiarów|
|totalcommandsprocessed2|Łączna liczba operacji (fragmentu 2)|Liczba|Łącznie||Brak wymiarów|
|cachehits2|Trafienia pamięci podręcznej (fragmentu 2)|Liczba|Łącznie||Brak wymiarów|
|cachemisses2|Chybienia w pamięci podręcznej (fragmentu 2)|Liczba|Łącznie||Brak wymiarów|
|getcommands2|Pobiera (fragmentu 2)|Liczba|Łącznie||Brak wymiarów|
|setcommands2|Zestawy (fragmentu 2)|Liczba|Łącznie||Brak wymiarów|
|operationsPerSecond2|Operacje na sekundę (fragmentu 2)|Liczba|Maksimum||Brak wymiarów|
|evictedkeys2|Wykluczone klucze (fragmentu 2)|Liczba|Łącznie||Brak wymiarów|
|totalkeys2|Łączna liczba kluczy (fragmentu 2)|Liczba|Maksimum||Brak wymiarów|
|expiredkeys2|Wygasłe klucze (fragmentu 2)|Liczba|Łącznie||Brak wymiarów|
|usedmemory2|Używana pamięć (fragmentu 2)|Szybkość|Maksimum||Brak wymiarów|
|usedmemoryRss2|Używana pamięć RSS (fragmentu 2)|Szybkość|Maksimum||Brak wymiarów|
|serverLoad2|Ładowanie serwera (fragmentu 2)|Procent|Maksimum||Brak wymiarów|
|cacheWrite2|Zapisywanie pamięci podręcznej (fragmentu 2)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead2|Odczyt pamięci podręcznej (fragmentu 2)|BytesPerSecond|Maksimum||Brak wymiarów|
|percentProcessorTime2|Procesor CPU (fragmentu 2)|Procent|Maksimum||Brak wymiarów|
|connectedclients3|Połączeni klienci (fragmentu 3)|Liczba|Maksimum||Brak wymiarów|
|totalcommandsprocessed3|Łączna liczba operacji (fragmentu 3)|Liczba|Łącznie||Brak wymiarów|
|cachehits3|Trafienia pamięci podręcznej (fragmentu 3)|Liczba|Łącznie||Brak wymiarów|
|cachemisses3|Chybienia w pamięci podręcznej (fragmentu 3)|Liczba|Łącznie||Brak wymiarów|
|getcommands3|Pobiera (fragmentu 3)|Liczba|Łącznie||Brak wymiarów|
|setcommands3|Zestawy (fragmentu 3)|Liczba|Łącznie||Brak wymiarów|
|operationsPerSecond3|Operacje na sekundę (fragmentu 3)|Liczba|Maksimum||Brak wymiarów|
|evictedkeys3|Wykluczone klucze (fragmentu 3)|Liczba|Łącznie||Brak wymiarów|
|totalkeys3|Łączna liczba kluczy (fragmentu 3)|Liczba|Maksimum||Brak wymiarów|
|expiredkeys3|Wygasłe klucze (fragmentu 3)|Liczba|Łącznie||Brak wymiarów|
|usedmemory3|Używana pamięć (fragmentu 3)|Szybkość|Maksimum||Brak wymiarów|
|usedmemoryRss3|Używana pamięć RSS (fragmentu 3)|Szybkość|Maksimum||Brak wymiarów|
|serverLoad3|Ładowanie serwera (fragmentu 3)|Procent|Maksimum||Brak wymiarów|
|cacheWrite3|Zapis w pamięci podręcznej (fragmentu 3)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead3|Odczyt pamięci podręcznej (fragmentu 3)|BytesPerSecond|Maksimum||Brak wymiarów|
|percentProcessorTime3|Procesor CPU (fragmentu 3)|Procent|Maksimum||Brak wymiarów|
|connectedclients4|Połączeni klienci (fragmentu 4)|Liczba|Maksimum||Brak wymiarów|
|totalcommandsprocessed4|Łączna liczba operacji (fragmentu 4)|Liczba|Łącznie||Brak wymiarów|
|cachehits4|Trafienia pamięci podręcznej (fragmentu 4)|Liczba|Łącznie||Brak wymiarów|
|cachemisses4|Chybienia w pamięci podręcznej (fragmentu 4)|Liczba|Łącznie||Brak wymiarów|
|getcommands4|Pobiera (fragmentu 4)|Liczba|Łącznie||Brak wymiarów|
|setcommands4|Zestawy (fragmentu 4)|Liczba|Łącznie||Brak wymiarów|
|operationsPerSecond4|Operacje na sekundę (fragmentu 4)|Liczba|Maksimum||Brak wymiarów|
|evictedkeys4|Wykluczone klucze (fragmentu 4)|Liczba|Łącznie||Brak wymiarów|
|totalkeys4|Łączna liczba kluczy (fragmentu 4)|Liczba|Maksimum||Brak wymiarów|
|expiredkeys4|Wygasłe klucze (fragmentu 4)|Liczba|Łącznie||Brak wymiarów|
|usedmemory4|Używana pamięć (fragmentu 4)|Szybkość|Maksimum||Brak wymiarów|
|usedmemoryRss4|Używana pamięć RSS (fragmentu 4)|Szybkość|Maksimum||Brak wymiarów|
|serverLoad4|Ładowanie serwera (fragmentu 4)|Procent|Maksimum||Brak wymiarów|
|cacheWrite4|Zapis pamięci podręcznej (fragmentu 4)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead4|Odczyt pamięci podręcznej (fragmentu 4)|BytesPerSecond|Maksimum||Brak wymiarów|
|percentProcessorTime4|Procesor CPU (fragmentu 4)|Procent|Maksimum||Brak wymiarów|
|connectedclients5|Połączeni klienci (fragmentu 5)|Liczba|Maksimum||Brak wymiarów|
|totalcommandsprocessed5|Łączna liczba operacji (fragmentu 5)|Liczba|Łącznie||Brak wymiarów|
|cachehits5|Trafienia pamięci podręcznej (fragmentu 5)|Liczba|Łącznie||Brak wymiarów|
|cachemisses5|Chybienia w pamięci podręcznej (fragmentu 5)|Liczba|Łącznie||Brak wymiarów|
|getcommands5|Pobiera (fragmentu 5)|Liczba|Łącznie||Brak wymiarów|
|setcommands5|Zestawy (fragmentu 5)|Liczba|Łącznie||Brak wymiarów|
|operationsPerSecond5|Operacje na sekundę (fragmentu 5)|Liczba|Maksimum||Brak wymiarów|
|evictedkeys5|Wykluczone klucze (fragmentu 5)|Liczba|Łącznie||Brak wymiarów|
|totalkeys5|Łączna liczba kluczy (fragmentu 5)|Liczba|Maksimum||Brak wymiarów|
|expiredkeys5|Wygasłe klucze (fragmentu 5)|Liczba|Łącznie||Brak wymiarów|
|usedmemory5|Używana pamięć (fragmentu 5)|Szybkość|Maksimum||Brak wymiarów|
|usedmemoryRss5|Używana pamięć RSS (fragmentu 5)|Szybkość|Maksimum||Brak wymiarów|
|serverLoad5|Ładowanie serwera (fragmentu 5)|Procent|Maksimum||Brak wymiarów|
|cacheWrite5|Zapis w pamięci podręcznej (fragmentu 5)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead5|Odczyt pamięci podręcznej (fragmentu 5)|BytesPerSecond|Maksimum||Brak wymiarów|
|percentProcessorTime5|Procesor CPU (fragmentu 5)|Procent|Maksimum||Brak wymiarów|
|connectedclients6|Połączeni klienci (fragmentu 6)|Liczba|Maksimum||Brak wymiarów|
|totalcommandsprocessed6|Łączna liczba operacji (fragmentu 6)|Liczba|Łącznie||Brak wymiarów|
|cachehits6|Trafienia pamięci podręcznej (fragmentu 6)|Liczba|Łącznie||Brak wymiarów|
|cachemisses6|Chybienia w pamięci podręcznej (fragmentu 6)|Liczba|Łącznie||Brak wymiarów|
|getcommands6|Pobiera (fragmentu 6)|Liczba|Łącznie||Brak wymiarów|
|setcommands6|Zestawy (fragmentu 6)|Liczba|Łącznie||Brak wymiarów|
|operationsPerSecond6|Operacje na sekundę (fragmentu 6)|Liczba|Maksimum||Brak wymiarów|
|evictedkeys6|Wykluczone klucze (fragmentu 6)|Liczba|Łącznie||Brak wymiarów|
|totalkeys6|Łączna liczba kluczy (fragmentu 6)|Liczba|Maksimum||Brak wymiarów|
|expiredkeys6|Wygasłe klucze (fragmentu 6)|Liczba|Łącznie||Brak wymiarów|
|usedmemory6|Używana pamięć (fragmentu 6)|Szybkość|Maksimum||Brak wymiarów|
|usedmemoryRss6|Używana pamięć RSS (fragmentu 6)|Szybkość|Maksimum||Brak wymiarów|
|serverLoad6|Ładowanie serwera (fragmentu 6)|Procent|Maksimum||Brak wymiarów|
|cacheWrite6|Zapis w pamięci podręcznej (fragmentu 6)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead6|Odczyt pamięci podręcznej (fragmentu 6)|BytesPerSecond|Maksimum||Brak wymiarów|
|percentProcessorTime6|Procesor CPU (fragmentu 6)|Procent|Maksimum||Brak wymiarów|
|connectedclients7|Połączeni klienci (fragmentu 7)|Liczba|Maksimum||Brak wymiarów|
|totalcommandsprocessed7|Łączna liczba operacji (fragmentu 7)|Liczba|Łącznie||Brak wymiarów|
|cachehits7|Trafienia pamięci podręcznej (fragmentu 7)|Liczba|Łącznie||Brak wymiarów|
|cachemisses7|Chybienia w pamięci podręcznej (fragmentu 7)|Liczba|Łącznie||Brak wymiarów|
|getcommands7|Pobiera (fragmentu 7)|Liczba|Łącznie||Brak wymiarów|
|setcommands7|Zestawy (fragmentu 7)|Liczba|Łącznie||Brak wymiarów|
|operationsPerSecond7|Operacje na sekundę (fragmentu 7)|Liczba|Maksimum||Brak wymiarów|
|evictedkeys7|Wykluczone klucze (fragmentu 7)|Liczba|Łącznie||Brak wymiarów|
|totalkeys7|Łączna liczba kluczy (fragmentu 7)|Liczba|Maksimum||Brak wymiarów|
|expiredkeys7|Wygasłe klucze (fragmentu 7)|Liczba|Łącznie||Brak wymiarów|
|usedmemory7|Używana pamięć (fragmentu 7)|Szybkość|Maksimum||Brak wymiarów|
|usedmemoryRss7|Używana pamięć RSS (fragmentu 7)|Szybkość|Maksimum||Brak wymiarów|
|serverLoad7|Ładowanie serwera (fragmentu 7)|Procent|Maksimum||Brak wymiarów|
|cacheWrite7|Zapis w pamięci podręcznej (fragmentu 7)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead7|Odczyt pamięci podręcznej (fragmentu 7)|BytesPerSecond|Maksimum||Brak wymiarów|
|percentProcessorTime7|Procesor CPU (fragmentu 7)|Procent|Maksimum||Brak wymiarów|
|connectedclients8|Połączeni klienci (fragmentu 8)|Liczba|Maksimum||Brak wymiarów|
|totalcommandsprocessed8|Łączna liczba operacji (fragmentu 8)|Liczba|Łącznie||Brak wymiarów|
|cachehits8|Trafienia pamięci podręcznej (fragmentu 8)|Liczba|Łącznie||Brak wymiarów|
|cachemisses8|Chybienia w pamięci podręcznej (fragmentu 8)|Liczba|Łącznie||Brak wymiarów|
|getcommands8|Pobiera (fragmentu 8)|Liczba|Łącznie||Brak wymiarów|
|setcommands8|Zestawy (fragmentu 8)|Liczba|Łącznie||Brak wymiarów|
|operationsPerSecond8|Operacje na sekundę (fragmentu 8)|Liczba|Maksimum||Brak wymiarów|
|evictedkeys8|Wykluczone klucze (fragmentu 8)|Liczba|Łącznie||Brak wymiarów|
|totalkeys8|Łączna liczba kluczy (fragmentu 8)|Liczba|Maksimum||Brak wymiarów|
|expiredkeys8|Wygasłe klucze (fragmentu 8)|Liczba|Łącznie||Brak wymiarów|
|usedmemory8|Używana pamięć (fragmentu 8)|Szybkość|Maksimum||Brak wymiarów|
|usedmemoryRss8|Używana pamięć RSS (fragmentu 8)|Szybkość|Maksimum||Brak wymiarów|
|serverLoad8|Ładowanie serwera (fragmentu 8)|Procent|Maksimum||Brak wymiarów|
|cacheWrite8|Zapis w pamięci podręcznej (fragmentu 8)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead8|Odczyt pamięci podręcznej (fragmentu 8)|BytesPerSecond|Maksimum||Brak wymiarów|
|percentProcessorTime8|Procesor CPU (fragmentu 8)|Procent|Maksimum||Brak wymiarów|
|connectedclients9|Połączeni klienci (fragmentu 9)|Liczba|Maksimum||Brak wymiarów|
|totalcommandsprocessed9|Łączna liczba operacji (fragmentu 9)|Liczba|Łącznie||Brak wymiarów|
|cachehits9|Trafienia pamięci podręcznej (fragmentu 9)|Liczba|Łącznie||Brak wymiarów|
|cachemisses9|Chybienia w pamięci podręcznej (fragmentu 9)|Liczba|Łącznie||Brak wymiarów|
|getcommands9|Pobiera (fragmentu 9)|Liczba|Łącznie||Brak wymiarów|
|setcommands9|Zestawy (fragmentu 9)|Liczba|Łącznie||Brak wymiarów|
|operationsPerSecond9|Operacje na sekundę (fragmentu 9)|Liczba|Maksimum||Brak wymiarów|
|evictedkeys9|Wykluczone klucze (fragmentu 9)|Liczba|Łącznie||Brak wymiarów|
|totalkeys9|Łączna liczba kluczy (fragmentu 9)|Liczba|Maksimum||Brak wymiarów|
|expiredkeys9|Wygasłe klucze (fragmentu 9)|Liczba|Łącznie||Brak wymiarów|
|usedmemory9|Używana pamięć (fragmentu 9)|Szybkość|Maksimum||Brak wymiarów|
|usedmemoryRss9|Używana pamięć RSS (fragmentu 9)|Szybkość|Maksimum||Brak wymiarów|
|serverLoad9|Ładowanie serwera (fragmentu 9)|Procent|Maksimum||Brak wymiarów|
|cacheWrite9|Zapis w pamięci podręcznej (fragmentu 9)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead9|Odczyt pamięci podręcznej (fragmentu 9)|BytesPerSecond|Maksimum||Brak wymiarów|
|percentProcessorTime9|Procesor CPU (fragmentu 9)|Procent|Maksimum||Brak wymiarów|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. ClassicCompute/virtualMachines

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Średnia|Wartość procentowa przyznanych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne.|Brak wymiarów|
|Sieć — wejście|Sieć — wejście|Szybkość|Łącznie|Liczba bajtów odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący).|Brak wymiarów|
|Sieć — wyjście|Sieć — wyjście|Szybkość|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący).|Brak wymiarów|
|Bajty odczytu dysku/s|Odczyt dysku|BytesPerSecond|Średnia|Średnia liczba bajtów odczytanych z dysku w okresie monitorowania.|Brak wymiarów|
|Bajty zapisu dysku/s|Zapis na dysku|BytesPerSecond|Średnia|Średnia liczba bajtów zapisywana na dysku w okresie monitorowania.|Brak wymiarów|
|Operacje odczytu z dysku/s|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Operacje we/wy odczytu na dysku.|Brak wymiarów|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Liczba operacji we/wy zapisu na dysku.|Brak wymiarów|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft. ClassicCompute/domainNames/gniazda/role

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Średnia|Wartość procentowa przyznanych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne.|RoleInstanceId|
|Sieć — wejście|Sieć — wejście|Szybkość|Łącznie|Liczba bajtów odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący).|RoleInstanceId|
|Sieć — wyjście|Sieć — wyjście|Szybkość|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący).|RoleInstanceId|
|Bajty odczytu dysku/s|Odczyt dysku|BytesPerSecond|Średnia|Średnia liczba bajtów odczytanych z dysku w okresie monitorowania.|RoleInstanceId|
|Bajty zapisu dysku/s|Zapis na dysku|BytesPerSecond|Średnia|Średnia liczba bajtów zapisywana na dysku w okresie monitorowania.|RoleInstanceId|
|Operacje odczytu z dysku/s|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Operacje we/wy odczytu na dysku.|RoleInstanceId|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Liczba operacji we/wy zapisu na dysku.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/konta

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalCalls|Łączna liczba wywołań|Liczba|Łącznie|Łączna liczba wywołań.|ApiName, OperationName, region|
|SuccessfulCalls|Pomyślne wywołania|Liczba|Łącznie|Liczba pomyślnych wywołań.|ApiName, OperationName, region|
|TotalErrors|Łączna liczba błędów|Liczba|Łącznie|Łączna liczba wywołań z odpowiedzią na błąd (kod odpowiedzi HTTP 4xx lub 5xx).|ApiName, OperationName, region|
|BlockedCalls|Zablokowane wywołania|Liczba|Łącznie|Liczba wywołań, które przekroczyły limit szybkości lub limitu przydziału.|ApiName, OperationName, region|
|Błędy servererrors|Błędy serwera|Liczba|Łącznie|Liczba wywołań z błędem wewnętrznym usługi (5xx kodu odpowiedzi HTTP).|ApiName, OperationName, region|
|ClientErrors|Błędy klienta|Liczba|Łącznie|Liczba wywołań z błędem po stronie klienta (4xx kodu odpowiedzi HTTP).|ApiName, OperationName, region|
|Dane|Dane w|Szybkość|Łącznie|Rozmiar danych przychodzących w bajtach.|ApiName, OperationName, region|
|DataOut|Dane wychodzące|Szybkość|Łącznie|Rozmiar danych wychodzących w bajtach.|ApiName, OperationName, region|
|Opóźnienie|Opóźnienie|)|Średnia|Opóźnienie w milisekundach.|ApiName, OperationName, region|
|CharactersTranslated|Znaki tłumaczone|Liczba|Łącznie|Całkowita liczba znaków w żądaniu tekstu przychodzącego.|ApiName, OperationName, region|
|CharactersTrained|Znaki przeszkolone|Liczba|Łącznie|Łączna liczba znaków przeszkolonych.|ApiName, OperationName, region|
|SpeechSessionDuration|Czas trwania sesji mowy|Sekundy|Łącznie|Łączny czas trwania sesji mowy (w sekundach).|ApiName, OperationName, region|
|TotalTransactions|Łączna liczba transakcji|Liczba|Łącznie|Łączna liczba transakcji.|Brak wymiarów|
|TotalTokenCalls|Łączna liczba wywołań tokenów|Liczba|Łącznie|Łączna liczba wywołań tokenów.|ApiName, OperationName, region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Średnia|Procent przyznanych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne|Brak wymiarów|
|Sieć — wejście|Sieć jest rozliczana|Szybkość|Łącznie|Liczba bajtów rozliczanych odebranych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący)|Brak wymiarów|
|Sieć — wyjście|Sieć — do rozliczania|Szybkość|Łącznie|Liczba bajtów rozliczanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący)|Brak wymiarów|
|Bajty odczytu dysku|Bajty odczytu dysku|Szybkość|Łącznie|Bajty odczytane z dysku w okresie monitorowania|Brak wymiarów|
|Bajty zapisu dysku|Bajty zapisu dysku|Szybkość|Łącznie|Bajty zapisywane na dysku w okresie monitorowania|Brak wymiarów|
|Operacje odczytu z dysku/s|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Operacje we/wy odczytu dysku|Brak wymiarów|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Operacje we/wy zapisu na dysku|Brak wymiarów|
|Pozostałe kredyty procesora CPU|Pozostałe kredyty procesora CPU|Liczba|Średnia|Łączna liczba kredytów dostępnych dla serii|Brak wymiarów|
|Wykorzystane środki CPU|Wykorzystane środki CPU|Liczba|Średnia|Łączna liczba kredytów zużytych przez maszynę wirtualną|Brak wymiarów|
|Bajty odczytu na dysk/s|Bajty odczytu dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|SlotId|
|Bajty zapisu na dysku/s|Bajty zapisu na dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|SlotId|
|Operacje odczytu na dysk/s|Operacje odczytu z dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Na dysk głębokość kolejki|Głębokość kolejki dysku danych (przestarzałe)|Liczba|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|SlotId|
|Bajty odczytu dysku systemu operacyjnego/s|Bajty odczytu z dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Bajty zapisu na dysku systemu operacyjnego/s|Bajty zapisu dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Operacje odczytu na dysku systemu operacyjnego/s|Operacje odczytu z dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Operacje zapisu na dysku systemu operacyjnego/s|Operacje zapisu na dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Głębokość kolejki systemu operacyjnego na dysku|Głębokość kolejki dysku systemu operacyjnego (przestarzałe)|Liczba|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|Brak wymiarów|
|Bajty odczytu dysku danych/s|Bajty odczytu dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|TWORZONA|
|Bajty zapisu na dysku danych/s|Bajty zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|TWORZONA|
|Operacje odczytu z dysku danych/s|Operacje odczytu z dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|TWORZONA|
|Operacje zapisu na dysku danych/s|Operacje zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|TWORZONA|
|Głębokość kolejki dysku danych|Głębokość kolejki dysku danych (wersja zapoznawcza)|Liczba|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|TWORZONA|
|Bajty odczytu dysku systemu operacyjnego/s|Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Bajty zapisu dysku systemu operacyjnego/s|Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Operacje odczytu z dysku systemu operacyjnego/s|Operacje odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Operacje zapisu na dysku systemu operacyjnego/s|Operacje zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Głębokość kolejki dysku systemu operacyjnego|Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza)|Liczba|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|Brak wymiarów|
|Przepływy przychodzące|Przepływy przychodzące (wersja zapoznawcza)|Liczba|Średnia|Przepływy przychodzące to liczba bieżących przepływów w kierunku przychodzącym (ruch przychodzący do maszyny wirtualnej)|Brak wymiarów|
|Przepływy wychodzące|Przepływy wychodzące (wersja zapoznawcza)|Liczba|Średnia|Przepływy wychodzące to liczba bieżących przepływów w kierunku ruchu wychodzącego (ruch wychodzący z maszyny wirtualnej)|Brak wymiarów|
|Maksymalny współczynnik tworzenia przepływów przychodzących|Maksymalny współczynnik tworzenia przepływów przychodzących (wersja zapoznawcza)|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów przychodzących (ruch przychodzący do maszyny wirtualnej)|Brak wymiarów|
|Maksymalny współczynnik tworzenia przepływów wychodzących|Maksymalny współczynnik tworzenia przepływów wychodzących (wersja zapoznawcza)|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów wychodzących (ruch wychodzący z maszyny wirtualnej)|Brak wymiarów|
|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|Trafienie odczytu pamięci podręcznej dysku danych Premium (wersja zapoznawcza)|Procent|Średnia|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|TWORZONA|
|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|Chybienia odczytu pamięci podręcznej dysku z danymi Premium (wersja zapoznawcza)|Procent|Średnia|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|TWORZONA|
|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Procent|Średnia|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|Brak wymiarów|
|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|Brak chybień w pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Procent|Średnia|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|Brak wymiarów|
|Sieć łącznie|Sieć łącznie|Szybkość|Łącznie|Liczba bajtów odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący)|Brak wymiarów|
|Całkowita liczba sieci|Całkowita liczba sieci|Szybkość|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący)|Brak wymiarów|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Średnia|Procent przyznanych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne|VMName|
|Sieć — wejście|Sieć jest rozliczana|Szybkość|Łącznie|Liczba bajtów rozliczanych odebranych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący)|VMName|
|Sieć — wyjście|Sieć — do rozliczania|Szybkość|Łącznie|Liczba bajtów rozliczanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący)|VMName|
|Bajty odczytu dysku|Bajty odczytu dysku|Szybkość|Łącznie|Bajty odczytane z dysku w okresie monitorowania|VMName|
|Bajty zapisu dysku|Bajty zapisu dysku|Szybkość|Łącznie|Bajty zapisywane na dysku w okresie monitorowania|VMName|
|Operacje odczytu z dysku/s|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Operacje we/wy odczytu dysku|VMName|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Operacje we/wy zapisu na dysku|VMName|
|Pozostałe kredyty procesora CPU|Pozostałe kredyty procesora CPU|Liczba|Średnia|Łączna liczba kredytów dostępnych dla serii|Brak wymiarów|
|Wykorzystane środki CPU|Wykorzystane środki CPU|Liczba|Średnia|Łączna liczba kredytów zużytych przez maszynę wirtualną|Brak wymiarów|
|Bajty odczytu na dysk/s|Bajty odczytu dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|SlotId|
|Bajty zapisu na dysku/s|Bajty zapisu na dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|SlotId|
|Operacje odczytu na dysk/s|Operacje odczytu z dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Na dysk głębokość kolejki|Głębokość kolejki dysku danych (przestarzałe)|Liczba|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|SlotId|
|Bajty odczytu dysku systemu operacyjnego/s|Bajty odczytu z dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Bajty zapisu na dysku systemu operacyjnego/s|Bajty zapisu dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Operacje odczytu na dysku systemu operacyjnego/s|Operacje odczytu z dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Operacje zapisu na dysku systemu operacyjnego/s|Operacje zapisu na dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Głębokość kolejki systemu operacyjnego na dysku|Głębokość kolejki dysku systemu operacyjnego (przestarzałe)|Liczba|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|Brak wymiarów|
|Bajty odczytu dysku danych/s|Bajty odczytu dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|Jednostka LUN, VMName|
|Bajty zapisu na dysku danych/s|Bajty zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|Jednostka LUN, VMName|
|Operacje odczytu z dysku danych/s|Operacje odczytu z dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|Jednostka LUN, VMName|
|Operacje zapisu na dysku danych/s|Operacje zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|Jednostka LUN, VMName|
|Głębokość kolejki dysku danych|Głębokość kolejki dysku danych (wersja zapoznawcza)|Liczba|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|Jednostka LUN, VMName|
|Bajty odczytu dysku systemu operacyjnego/s|Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Bajty zapisu dysku systemu operacyjnego/s|Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Operacje odczytu z dysku systemu operacyjnego/s|Operacje odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Operacje zapisu na dysku systemu operacyjnego/s|Operacje zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Głębokość kolejki dysku systemu operacyjnego|Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza)|Liczba|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|VMName|
|Przepływy przychodzące|Przepływy przychodzące (wersja zapoznawcza)|Liczba|Średnia|Przepływy przychodzące to liczba bieżących przepływów w kierunku przychodzącym (ruch przychodzący do maszyny wirtualnej)|VMName|
|Przepływy wychodzące|Przepływy wychodzące (wersja zapoznawcza)|Liczba|Średnia|Przepływy wychodzące to liczba bieżących przepływów w kierunku ruchu wychodzącego (ruch wychodzący z maszyny wirtualnej)|VMName|
|Maksymalny współczynnik tworzenia przepływów przychodzących|Maksymalny współczynnik tworzenia przepływów przychodzących (wersja zapoznawcza)|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów przychodzących (ruch przychodzący do maszyny wirtualnej)|VMName|
|Maksymalny współczynnik tworzenia przepływów wychodzących|Maksymalny współczynnik tworzenia przepływów wychodzących (wersja zapoznawcza)|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów wychodzących (ruch wychodzący z maszyny wirtualnej)|VMName|
|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|Trafienie odczytu pamięci podręcznej dysku danych Premium (wersja zapoznawcza)|Procent|Średnia|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|Jednostka LUN, VMName|
|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|Chybienia odczytu pamięci podręcznej dysku z danymi Premium (wersja zapoznawcza)|Procent|Średnia|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|Jednostka LUN, VMName|
|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Procent|Średnia|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|VMName|
|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|Brak chybień w pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Procent|Średnia|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|VMName|
|Sieć łącznie|Sieć łącznie|Szybkość|Łącznie|Liczba bajtów odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący)|VMName|
|Całkowita liczba sieci|Całkowita liczba sieci|Szybkość|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft. ContainerInstance/containerGroups

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuUsage|Użycie procesora CPU|Liczba|Średnia|Użycie procesora CPU we wszystkich rdzeniach w millicores.|containerName|
|MemoryUsage|Użycie pamięci|Szybkość|Średnia|Całkowite użycie pamięci w bajcie.|containerName|
|NetworkBytesReceivedPerSecond|Bajty odebrane przez sieć na sekundę|Szybkość|Średnia|Bajty odebrane przez sieć na sekundę.|Brak wymiarów|
|NetworkBytesTransmittedPerSecond|Bajty przesyłane przez sieć na sekundę|Szybkość|Średnia|Bajty przesyłane przez sieć na sekundę.|Brak wymiarów|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/rejestry

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalPullCount|Łączna liczba ściągania|Liczba|Średnia|Całkowita liczba ściągania obrazów|Brak wymiarów|
|SuccessfulPullCount|Pomyślna liczba ściągania|Liczba|Średnia|Liczba pomyślnych operacji ściągania obrazu|Brak wymiarów|
|TotalPushCount|Łączna liczba wypychanych|Liczba|Średnia|Liczba wypchnięciów obrazów łącznie|Brak wymiarów|
|SuccessfulPushCount|Liczba wypychanych zakończonych powodzeniem|Liczba|Średnia|Liczba pomyślnych operacji wypychania obrazu|Brak wymiarów|
|RunDuration|Czas trwania przebiegu|)|Łącznie|Czas trwania przebiegu w milisekundach|Brak wymiarów|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. ContainerService/managedClusters

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Łączna liczba dostępnych rdzeni procesora CPU w zarządzanym klastrze|Liczba|Łącznie|Łączna liczba dostępnych rdzeni procesora CPU w zarządzanym klastrze|Brak wymiarów|
|kube_node_status_allocatable_memory_bytes|Łączna ilość dostępnej pamięci w zarządzanym klastrze|Szybkość|Łącznie|Łączna ilość dostępnej pamięci w zarządzanym klastrze|Brak wymiarów|
|kube_pod_status_ready|Liczba zasobników w stanie gotowe|Liczba|Łącznie|Liczba zasobników w stanie gotowe|Przestrzeń nazw, pod|
|kube_node_status_condition|Stany różnych warunków węzła|Liczba|Łącznie|Stany różnych warunków węzła|warunek, stan, status2, węzeł|
|kube_pod_status_phase|Liczba etapów według fazy|Liczba|Łącznie|Liczba etapów według fazy|faza, przestrzeń nazw, pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft. CustomerInsights/Hubs

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|DCIApiCalls|Wywołania interfejsu API Customer Insights|Liczba|Łącznie||Brak wymiarów|
|DCIMappingImportOperationSuccessfulLines|Pomyślnie zakończono mapowanie wierszy operacji importowania|Liczba|Łącznie||Brak wymiarów|
|DCIMappingImportOperationFailedLines|Niepowodzenie operacji importowania mapowania wierszy|Liczba|Łącznie||Brak wymiarów|
|DCIMappingImportOperationTotalLines|Łączna liczba wierszy operacji importowania mapowania|Liczba|Łącznie||Brak wymiarów|
|DCIMappingImportOperationRuntimeInSeconds|Mapowanie czasu wykonywania operacji importowania w ciągu kilku sekund|Sekundy|Łącznie||Brak wymiarów|
|DCIOutboundProfileExportSucceeded|Eksportowanie profilu wychodzącego zakończyło się pomyślnie|Liczba|Łącznie||Brak wymiarów|
|DCIOutboundProfileExportFailed|Eksport profilu wychodzącego nie powiódł się|Liczba|Łącznie||Brak wymiarów|
|DCIOutboundProfileExportDuration|Czas trwania eksportu profilu wychodzącego|Sekundy|Łącznie||Brak wymiarów|
|DCIOutboundKpiExportSucceeded|Eksport wychodzącego wskaźnika KPI zakończył się pomyślnie|Liczba|Łącznie||Brak wymiarów|
|DCIOutboundKpiExportFailed|Eksport wychodzącego wskaźnika KPI nie powiódł się|Liczba|Łącznie||Brak wymiarów|
|DCIOutboundKpiExportDuration|Czas trwania eksportu KPI wychodzącego|Sekundy|Łącznie||Brak wymiarów|
|DCIOutboundKpiExportStarted|Rozpoczęto eksport wychodzącego wskaźnika KPI|Sekundy|Łącznie||Brak wymiarów|
|DCIOutboundKpiRecordCount|Liczba rekordów wychodzącego wskaźnika KPI|Sekundy|Łącznie||Brak wymiarów|
|DCIOutboundProfileExportCount|Liczba eksportu profilów wychodzących|Sekundy|Łącznie||Brak wymiarów|
|DCIOutboundInitialProfileExportFailed|Wychodzące Eksportowanie profilu początkowego nie powiodło się|Sekundy|Łącznie||Brak wymiarów|
|DCIOutboundInitialProfileExportSucceeded|Eksportowanie wyjściowego profilu początkowego zakończyło się pomyślnie|Sekundy|Łącznie||Brak wymiarów|
|DCIOutboundInitialKpiExportFailed|Nie można wyeksportować wyjściowego kluczowego wskaźnika KPI|Sekundy|Łącznie||Brak wymiarów|
|DCIOutboundInitialKpiExportSucceeded|Wychodzące eksportowanie początkowego wskaźnika KPI powiodło się|Sekundy|Łącznie||Brak wymiarów|
|DCIOutboundInitialProfileExportDurationInSeconds|Czas trwania eksportu profilu początkowego w sekundach|Sekundy|Łącznie||Brak wymiarów|
|AdlaJobForStandardKpiFailed|Niepowodzenie zadania adla dla standardowego wskaźnika KPI w sekundach|Sekundy|Łącznie||Brak wymiarów|
|AdlaJobForStandardKpiTimeOut|Adla zadanie dla standardowego limitu czasu KPI w sekundach|Sekundy|Łącznie||Brak wymiarów|
|AdlaJobForStandardKpiCompleted|Zadanie adla dla standardowego wskaźnika KPI zostało ukończone w sekundach|Sekundy|Łącznie||Brak wymiarów|
|ImportASAValuesFailed|Liczba nieudanych importu wartości ASA|Liczba|Łącznie||Brak wymiarów|
|ImportASAValuesSucceeded|Liczba pomyślnie zaimportowanych wartości ASA|Liczba|Łącznie||Brak wymiarów|
|DCIProfilesCount|Liczba wystąpień profilu|Liczba|ostatniego||Brak wymiarów|
|DCIInteractionsPerMonthCount|Liczba interakcji na miesiąc|Liczba|ostatniego||Brak wymiarów|
|DCIKpisCount|Liczba wskaźników KPI|Liczba|ostatniego||Brak wymiarów|
|DCISegmentsCount|Liczba segmentów|Liczba|ostatniego||Brak wymiarów|
|DCIPredictiveMatchPoliciesCount|Liczba dopasowań predykcyjnych|Liczba|ostatniego||Brak wymiarów|
|DCIPredictionsCount|Liczba prognoz|Liczba|ostatniego||Brak wymiarów|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. DataBoxEdge/dataBoxEdgeDevices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|NICReadThroughput|Przepływność odczytu (Sieć)|BytesPerSecond|Średnia|Przepływność odczytu interfejsu sieciowego na urządzeniu w okresie sprawozdawczym dla wszystkich woluminów w bramie.|Nazwa InstanceName|
|NICWriteThroughput|Przepływność zapisu (Sieć)|BytesPerSecond|Średnia|Przepływność zapisu interfejsu sieciowego na urządzeniu w okresie sprawozdawczym dla wszystkich woluminów w bramie.|Nazwa InstanceName|
|CloudReadThroughputPerShare|Przepływność pobierania w chmurze (udział)|BytesPerSecond|Średnia|Przepływność pobierania na platformę Azure z udziału w okresie raportowania.|Udostępnianie|
|CloudUploadThroughputPerShare|Przepływność przekazywania w chmurze (udział)|BytesPerSecond|Średnia|Przepływność przekazywania do platformy Azure z udziału w okresie raportowania.|Udostępnianie|
|BytesUploadedToCloudPerShare|Przekazane bajty w chmurze (udział)|Szybkość|Średnia|Całkowita liczba bajtów przekazanych do platformy Azure z udziału w okresie raportowania.|Udostępnianie|
|Łączna pojemność|Całkowita pojemność|Szybkość|Średnia|Całkowita pojemność|Brak wymiarów|
|AvailableCapacity|Dostępna pojemność|Szybkość|Średnia|Dostępna pojemność w bajtach w okresie raportowania.|Brak wymiarów|
|CloudUploadThroughput|Przepływność przekazywania w chmurze|BytesPerSecond|Średnia|W chmurze przekazano przepływność na platformę Azure w okresie raportowania.|Brak wymiarów|
|CloudReadThroughput|Przepływność pobierania w chmurze|BytesPerSecond|Średnia|Chmura pobiera przepływność na platformę Azure w okresie raportowania.|Brak wymiarów|
|BytesUploadedToCloud|Przekazane bajty w chmurze (urządzenie)|Szybkość|Średnia|Całkowita liczba bajtów przekazana na platformę Azure z urządzenia w okresie raportowania.|Brak wymiarów|
|HyperVVirtualProcessorUtilization|Obliczenia brzegowe — procentowy procesor CPU|Procent|Średnia|Procent użycia procesora CPU|Nazwa InstanceName|
|HyperVMemoryUtilization|Obliczenia brzegowe — użycie pamięci|Procent|Średnia|Ilość pamięci RAM w użyciu|Nazwa InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft. DataFactory/datafactors

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|FailedRuns|Nieudane uruchomienia|Liczba|Łącznie||potokname, ActivityName|
|SuccessfulRuns|Pomyślne uruchomienia|Liczba|Łącznie||potokname, ActivityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/fabryki

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PipelineFailedRuns|Metryki uruchomionych potoków zakończonych niepowodzeniem|Liczba|Łącznie||FailureType, nazwa|
|PipelineSucceededRuns|Metryki uruchamiania potoków zakończonych powodzeniem|Liczba|Łącznie||FailureType, nazwa|
|ActivityFailedRuns|Metryki uruchamiania działań zakończonych niepowodzeniem|Liczba|Łącznie||ActivityType, Potokname, FailureType, nazwa|
|ActivitySucceededRuns|Metryki uruchamiania działań zakończonych powodzeniem|Liczba|Łącznie||ActivityType, Potokname, FailureType, nazwa|
|TriggerFailedRuns|Metryki uruchomienia wyzwalacza zakończonego niepowodzeniem|Liczba|Łącznie||Nazwa, Niepowodzenie|
|TriggerSucceededRuns|Wyzwalacze uruchomienia wyzwalają metryki|Liczba|Łącznie||Nazwa, Niepowodzenie|
|IntegrationRuntimeCpuPercentage|Użycie procesora Integration Runtime|Procent|Średnia||IntegrationRuntimeName, nodename|
|IntegrationRuntimeAvailableMemory|Dostępna pamięć środowiska Integration Runtime|Szybkość|Średnia||IntegrationRuntimeName, nodename|
|MaxAllowedResourceCount|Maksymalna dozwolona liczba jednostek|Liczba|Maksimum||Brak wymiarów|
|MaxAllowedFactorySizeInGbUnits|Maksymalny dozwolony rozmiar fabryki (jednostka GB)|Liczba|Maksimum||Brak wymiarów|
|ResourceCount|Łączna liczba jednostek|Liczba|Maksimum||Brak wymiarów|
|FactorySizeInGbUnits|Łączny rozmiar fabryki (jednostka GB)|Liczba|Maksimum||Brak wymiarów|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. DataLakeAnalytics/konta

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|JobEndedSuccess|Zadania zakończone powodzeniem|Liczba|Łącznie|Liczba zadań zakończonych powodzeniem.|Brak wymiarów|
|JobEndedFailure|Zadania zakończone niepowodzeniem|Liczba|Łącznie|Liczba zadań zakończonych niepowodzeniem.|Brak wymiarów|
|JobEndedCancelled|Zadania anulowane|Liczba|Łącznie|Liczba anulowanych zadań.|Brak wymiarów|
|JobAUEndedSuccess|Czas pomyślnej aktualizacji|Sekundy|Łącznie|Łączny czas dla zadań zakończonych powodzeniem.|Brak wymiarów|
|JobAUEndedFailure|Czas niepowodzenia|Sekundy|Łącznie|Łączny czas AU zadań zakończonych niepowodzeniem.|Brak wymiarów|
|JobAUEndedCancelled|Anulowano czas aktualizacji|Sekundy|Łącznie|Łączny czas AU dla anulowanych zadań.|Brak wymiarów|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. kontach datalakestore/konta

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalStorage|Całkowita ilość miejsca do magazynowania|Szybkość|Maksimum|Łączna ilość danych przechowywanych na koncie.|Brak wymiarów|
|Zapisywana|Zapisywane dane|Szybkość|Łącznie|Całkowita ilość danych zapisywana na konto.|Brak wymiarów|
|Odczyt DataReady|Odczytane dane|Szybkość|Łącznie|Łączna ilość danych odczytanych z konta.|Brak wymiarów|
|WriteRequests|Żądania zapisu|Liczba|Łącznie|Liczba żądań zapisu danych na koncie.|Brak wymiarów|
|ReadRequests|Żądania odczytu|Liczba|Łącznie|Liczba żądań odczytu danych do konta.|Brak wymiarów|

## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/serwery

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Brak wymiarów|
|memory_percent|Procent pamięci|Procent|Średnia|Procent pamięci|Brak wymiarów|
|io_consumption_percent|Procent operacji we/wy|Procent|Średnia|Procent operacji we/wy|Brak wymiarów|
|storage_percent|Procent magazynu|Procent|Średnia|Procent magazynu|Brak wymiarów|
|storage_used|Użyty magazyn|Szybkość|Średnia|Użyty magazyn|Brak wymiarów|
|storage_limit|Limit magazynu|Szybkość|Średnia|Limit magazynu|Brak wymiarów|
|serverlog_storage_percent|Procent magazynu dzienników serwera|Procent|Średnia|Procent magazynu dzienników serwera|Brak wymiarów|
|serverlog_storage_usage|Używany magazyn dzienników serwera|Szybkość|Średnia|Używany magazyn dzienników serwera|Brak wymiarów|
|serverlog_storage_limit|Limit magazynowania dziennika serwera|Szybkość|Średnia|Limit magazynowania dziennika serwera|Brak wymiarów|
|active_connections|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Brak wymiarów|
|connections_failed|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|Brak wymiarów|
|seconds_behind_master|Opóźnienie replikacji w sekundach|Liczba|Średnia|Opóźnienie replikacji w sekundach|Brak wymiarów|
|backup_storage_used|Używany magazyn kopii zapasowych|Szybkość|Średnia|Używany magazyn kopii zapasowych|Brak wymiarów|
|network_bytes_egress|Sieć — wyjście|Szybkość|Łącznie|Sieć poza aktywnymi połączeniami|Brak wymiarów|
|network_bytes_ingress|Sieć — wejście|Szybkość|Łącznie|Sieć w ramach aktywnych połączeń|Brak wymiarów|

## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/serwery

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Brak wymiarów|
|memory_percent|Procent pamięci|Procent|Średnia|Procent pamięci|Brak wymiarów|
|io_consumption_percent|Procent operacji we/wy|Procent|Średnia|Procent operacji we/wy|Brak wymiarów|
|storage_percent|Procent magazynu|Procent|Średnia|Procent magazynu|Brak wymiarów|
|storage_used|Użyty magazyn|Szybkość|Średnia|Użyty magazyn|Brak wymiarów|
|storage_limit|Limit magazynu|Szybkość|Średnia|Limit magazynu|Brak wymiarów|
|serverlog_storage_percent|Procent magazynu dzienników serwera|Procent|Średnia|Procent magazynu dzienników serwera|Brak wymiarów|
|serverlog_storage_usage|Używany magazyn dzienników serwera|Szybkość|Średnia|Używany magazyn dzienników serwera|Brak wymiarów|
|serverlog_storage_limit|Limit magazynowania dziennika serwera|Szybkość|Średnia|Limit magazynowania dziennika serwera|Brak wymiarów|
|active_connections|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Brak wymiarów|
|connections_failed|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|Brak wymiarów|
|seconds_behind_master|Opóźnienie replikacji w sekundach|Liczba|Średnia|Opóźnienie replikacji w sekundach|Brak wymiarów|
|backup_storage_used|Używany magazyn kopii zapasowych|Szybkość|Średnia|Używany magazyn kopii zapasowych|Brak wymiarów|
|network_bytes_egress|Sieć — wyjście|Szybkość|Łącznie|Sieć poza aktywnymi połączeniami|Brak wymiarów|
|network_bytes_ingress|Sieć — wejście|Szybkość|Łącznie|Sieć w ramach aktywnych połączeń|Brak wymiarów|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/serwery

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Brak wymiarów|
|memory_percent|Procent pamięci|Procent|Średnia|Procent pamięci|Brak wymiarów|
|io_consumption_percent|Procent operacji we/wy|Procent|Średnia|Procent operacji we/wy|Brak wymiarów|
|storage_percent|Procent magazynu|Procent|Średnia|Procent magazynu|Brak wymiarów|
|storage_used|Użyty magazyn|Szybkość|Średnia|Użyty magazyn|Brak wymiarów|
|storage_limit|Limit magazynu|Szybkość|Średnia|Limit magazynu|Brak wymiarów|
|serverlog_storage_percent|Procent magazynu dzienników serwera|Procent|Średnia|Procent magazynu dzienników serwera|Brak wymiarów|
|serverlog_storage_usage|Używany magazyn dzienników serwera|Szybkość|Średnia|Używany magazyn dzienników serwera|Brak wymiarów|
|serverlog_storage_limit|Limit magazynowania dziennika serwera|Szybkość|Średnia|Limit magazynowania dziennika serwera|Brak wymiarów|
|active_connections|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Brak wymiarów|
|connections_failed|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|Brak wymiarów|
|backup_storage_used|Używany magazyn kopii zapasowych|Szybkość|Średnia|Używany magazyn kopii zapasowych|Brak wymiarów|
|network_bytes_egress|Sieć — wyjście|Szybkość|Łącznie|Sieć poza aktywnymi połączeniami|Brak wymiarów|
|network_bytes_ingress|Sieć — wejście|Szybkość|Łącznie|Sieć w ramach aktywnych połączeń|Brak wymiarów|
|pg_replica_log_delay_in_seconds|Zwłoka repliki|Sekundy|Maksimum|Opóźnienie repliki w sekundach|Brak wymiarów|
|pg_replica_log_delay_in_bytes|Maksymalne opóźnienie między replikami|Szybkość|Maksimum|Opóźnienie w bajtach najbardziej opóźnionej repliki|Brak wymiarów|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Brak wymiarów|
|memory_percent|Procent pamięci|Procent|Średnia|Procent pamięci|Brak wymiarów|
|Wejścia|Operacje wejścia/wyjścia|Liczba|Średnia|Operacje we/wy na sekundę|Brak wymiarów|
|storage_percent|Procent magazynu|Procent|Średnia|Procent magazynu|Brak wymiarów|
|storage_used|Użyty magazyn|Szybkość|Średnia|Użyty magazyn|Brak wymiarów|
|active_connections|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Brak wymiarów|
|network_bytes_egress|Sieć — wyjście|Szybkość|Łącznie|Sieć poza aktywnymi połączeniami|Brak wymiarów|
|network_bytes_ingress|Sieć — wejście|Szybkość|Łącznie|Sieć w ramach aktywnych połączeń|Brak wymiarów|

## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|D2C. telemetrię. allProtocol|Próby wysłania komunikatów telemetrycznych|Liczba|Łącznie|Liczba komunikatów telemetrycznych z urządzenia do chmury, które próbowano wysłać do centrum IoT Hub|Brak wymiarów|
|D2C. telemetrię. dane wejściowe. sukces|Wysłane komunikaty telemetryczne|Liczba|Łącznie|Liczba pomyślnie wysłanych komunikatów telemetrycznych z urządzenia do chmury do centrum IoT Hub|Brak wymiarów|
|C2D. Commands. wyjście. Complete. Success|Polecenia ukończone|Liczba|Łącznie|Liczba poleceń z chmury do urządzenia ukończonych pomyślnie przez urządzenie|Brak wymiarów|
|C2D. Commands. wyjście. Abandon. Success|Polecenia porzucone|Liczba|Łącznie|Liczba poleceń z chmury do urządzenia porzuconych przez urządzenie|Brak wymiarów|
|C2D. Commands. wyjście. Odrzuć. sukces|Polecenia odrzucone|Liczba|Łącznie|Liczba poleceń z chmury do urządzenia odrzuconych przez urządzenie|Brak wymiarów|
|Devices. totalDevices|Łączna liczba urządzeń (przestarzałe)|Liczba|Łącznie|Liczba urządzeń zarejestrowanych w usłudze IoT Hub|Brak wymiarów|
|Devices. connectedDevices. allProtocol|Podłączone urządzenia (przestarzałe) |Liczba|Łącznie|Liczba urządzeń podłączonych do centrum IoT Hub|Brak wymiarów|
|D2C. telemetrię. ruch wychodzący. sukces|Routing: dostarczono komunikaty telemetryczne|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do wszystkich punktów końcowych używających routingu IoT Hub. Jeśli komunikat jest kierowany do wielu punktów końcowych, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczenia. Jeśli wiadomość jest przekazywana do tego samego punktu końcowego wiele razy, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczenia.|Brak wymiarów|
|D2C. dane telemetryczne. wychodzące. upuszczone|Routing: porzucone komunikaty telemetryczne |Liczba|Łącznie|Liczba porzuconych komunikatów przez IoT Hub Routing ze względu na martwe punkty końcowe. Ta wartość nie zlicza komunikatów dostarczonych do trasy rezerwowej, ponieważ opuszczone wiadomości nie są tam dostarczane.|Brak wymiarów|
|D2C. dane telemetryczne. wychodzące. oddzielone|Routing: oddzielone komunikaty telemetryczne |Liczba|Łącznie|Liczba oddzielonych komunikatów przez IoT Hub Routing, ponieważ nie są one zgodne z żadną regułą routingu (w tym regułą rezerwową). |Brak wymiarów|
|D2C. Telemetria. ruch wychodzący. nieprawidłowe|Routing: komunikaty telemetryczne są niezgodne|Liczba|Łącznie|Liczba przypadków, gdy Routing IoT Hub nie mógł dostarczyć komunikatów z powodu niezgodności z punktem końcowym. Ta wartość nie obejmuje ponownych prób.|Brak wymiarów|
|D2C. telemetrię. ruch wychodzący. Fallback|Routing: komunikaty dostarczane do powrotu|Liczba|Łącznie|Liczba IoT Hub komunikatów dostarczonych przez funkcję routingu do punktu końcowego skojarzonego z trasą rezerwową.|Brak wymiarów|
|D2C. endpoints. wychodzące. eventHubs|Routing: komunikaty dostarczane do centrum zdarzeń|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do punktów końcowych centrum zdarzeń w usłudze IoT Hub Routing.|Brak wymiarów|
|D2C. endpoints. opóźnienie. eventHubs|Routing: opóźnienie komunikatu dla centrum zdarzeń|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów przychodzących w punkcie końcowym centrum zdarzeń.|Brak wymiarów|
|D2C. endpoints. wychodzące. serviceBusQueues|Routing: komunikaty dostarczone do kolejki Service Bus|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do Service Bus punktów końcowych kolejki w usłudze IoT Hub Routing.|Brak wymiarów|
|D2C. endpoints. opóźnienie. serviceBusQueues|Routing: opóźnienie komunikatu dla kolejki Service Bus|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i telemetrii komunikatów przychodzących do punktu końcowego kolejki Service Bus.|Brak wymiarów|
|D2C. endpoints. wychodzące. serviceBusTopics|Routing: komunikaty dostarczane do Service Bus tematu|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów przez IoT Hub Routing do Service Bus punktów końcowych tematu.|Brak wymiarów|
|D2C. endpoints. opóźnienie. serviceBusTopics|Routing: opóźnienie komunikatu dla Service Bus tematu|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w punkcie końcowym tematu Service Bus.|Brak wymiarów|
|D2C. endpoints. wychodzące. wbudowane. Events|Routing: komunikaty dostarczane do komunikatów/zdarzeń|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do wbudowanego punktu końcowego (komunikaty/zdarzenia) IoT Hub Routing. Ta Metryka uruchamia się tylko wtedy, gdy routing jest włączony (https://aka.ms/iotrouting) dla Centrum IoT.|Brak wymiarów|
|D2C. endpoints. opóźnienie. wbudowane. Events|Routing: opóźnienie komunikatów dla komunikatów/zdarzeń|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w wbudowanym punkcie końcowym (komunikaty/zdarzenia). Ta Metryka uruchamia się tylko wtedy, gdy routing jest włączony (https://aka.ms/iotrouting) dla Centrum IoT.|Brak wymiarów|
|D2C. endpoints. ruch wychodzący. Storage|Routing: komunikaty dostarczane do magazynu|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do punktów końcowych usługi Routing IoT Hub.|Brak wymiarów|
|D2C. endpoints. opóźnienie. Storage|Routing: opóźnienie komunikatu dla magazynu|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w punkcie końcowym magazynu.|Brak wymiarów|
|D2C. endpoints. ruch wychodzący. Storage. Bytes|Routing: dane dostarczane do magazynu|Szybkość|Łącznie|Ilość danych (w bajtach) IoT Hub Routing dostarczany do punktów końcowych magazynu.|Brak wymiarów|
|D2C. endpoints. dane wyjściowe. Storage. blob|Routing: obiekty blob dostarczane do magazynu|Liczba|Łącznie|Ile razy usługa Routing IoT Hub dostarczać obiekty blob do punktów końcowych magazynu.|Brak wymiarów|
|EventGridDeliveries|Dostawy Event Grid (wersja zapoznawcza)|Liczba|Łącznie|Liczba zdarzeń IoT Hub opublikowanych do Event Grid. Użyj wymiaru wynik dla liczby żądań zakończonych powodzeniem i niepowodzeniem. Wymiar EventType przedstawia typ zdarzenia (https://aka.ms/ioteventgrid).|Wynik, typ zdarzenia|
|EventGridLatency|Średnie opóźnienie (w milisekundach) od momentu wygenerowania zdarzenia usługi IoT Hub po opublikowaniu zdarzenia w Event Grid. Ta liczba jest średnia między wszystkimi typami zdarzeń. Użyj wymiaru EventType, aby zobaczyć opóźnienie określonego typu zdarzenia.|Klasę|
|D2C. splot. Read. Success|Pomyślne odczyty sznurów z urządzeń|Liczba|Łącznie|Liczba wszystkich udanych operacji zainicjowanych przez urządzenie.|Brak wymiarów|
|D2C. splot. Read. Failure|Nieudane odczyty sznurów z urządzeń|Liczba|Łącznie|Liczba wszystkich nieudanych operacji zainicjowanych przez urządzenie.|Brak wymiarów|
|D2C. splot. Read. size|Rozmiar odpowiedzi dla sznurów odczytanych z urządzeń|Szybkość|Średnia|Średnia, minimum i maksimum wszystkich udanych zainicjowanych przez urządzenia sznurów.|Brak wymiarów|
|D2C. splot. Update. Success|Pomyślne aktualizacje bliźniaczych urządzeń|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji typu bliźniaczych zainicjowanych przez urządzenie.|Brak wymiarów|
|D2C. splot. Update. Failure|Niepowodzenie aktualizacji bliźniaczych z urządzeń|Liczba|Łącznie|Liczba wszystkich niezakończonych niepowodzeniem aktualizacji dwuosiowych zainicjowanych przez urządzenie.|Brak wymiarów|
|D2C. splot. Update. size|Rozmiar aktualizacji bliźniaczych z urządzeń|Szybkość|Średnia|Średni, minimalny i maksymalny rozmiar wszystkich pomyślnych aktualizacji typu dwuosiowego zainicjowanego przez urządzenie.|Brak wymiarów|
|C2D. Methods. Success|Pomyślne wywołania metody bezpośredniej|Liczba|Łącznie|Liczba wszystkich pomyślnych wywołań metody bezpośredniej.|Brak wymiarów|
|C2D. Methods. Failure|Nieudane wywołania metody bezpośredniej|Liczba|Łącznie|Liczba wszystkich wywołań metod bezpośrednich zakończonych niepowodzeniem.|Brak wymiarów|
|C2D. Methods. requestSize|Rozmiar żądania wywołań metody bezpośredniej|Szybkość|Średnia|Średnia, minimum i maksimum wszystkich pomyślnych żądań metody bezpośredniej.|Brak wymiarów|
|C2D. Methods. responseSize|Rozmiar odpowiedzi wywołań metody bezpośredniej|Szybkość|Średnia|Średnia, minimum i maksimum wszystkich zakończonych powodzeniem odpowiedzi metody bezpośredniej.|Brak wymiarów|
|C2D. splot. Read. Success|Pomyślne odczyty sznurów z zaplecza|Liczba|Łącznie|Liczba wszystkich zakończonych powodzeniem odczytów dwuosiowych zainicjowanych z powrotem.|Brak wymiarów|
|C2D. splot. Read. Failure|Nieudane odczyty sznurów z zaplecza|Liczba|Łącznie|Liczba wszystkich zakończonych niepowodzeniem odczytów dwuosiowych zainicjowanych z powrotem.|Brak wymiarów|
|C2D. splot. Read. size|Rozmiar odpowiedzi na odwrocie od zaplecza|Szybkość|Średnia|Średnia, minimum i maksimum wszystkich udanych zainicjowanych z powrotem sznurów danych.|Brak wymiarów|
|C2D. splot. Update. Success|Pomyślne aktualizacje bliźniaczych z zaplecza|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji typu bliźniaczych zainicjowanych z powrotem.|Brak wymiarów|
|C2D. splot. Update. Failure|Niepowodzenie aktualizacji bliźniaczych z zaplecza|Liczba|Łącznie|Liczba wszystkich niezakończonych niepowodzeniem aktualizacji typu bliźniaczych zainicjowanych z powrotem.|Brak wymiarów|
|C2D. splot. Update. size|Rozmiar aktualizacji przędzy od zaplecza|Szybkość|Średnia|Średni, minimalny i maksymalny rozmiar wszystkich pomyślnych aktualizacji typu sznurka zainicjowanych z powrotem.|Brak wymiarów|
|twinQueries. Success|Pomyślne zapytania bliźniaczy|Liczba|Łącznie|Liczba wszystkich udanych zapytań bliźniaczych.|Brak wymiarów|
|twinQueries. Failure|Niepowodzenie zapytań bliźniaczych|Liczba|Łącznie|Liczba wszystkich zakończonych niepowodzeniem zapytań bliźniaczych.|Brak wymiarów|
|twinQueries.resultSize|Rozmiar wyniku zapytań bliźniaczych|Szybkość|Średnia|Średnia, minimalna i maksymalna wielkość wyniku wszystkich udanych zapytań bliźniaczych.|Brak wymiarów|
|Jobs. createTwinUpdateJob. Success|Pomyślne utworzenie dwuosiowych zadań aktualizacji|Liczba|Łącznie|Liczba wszystkich udanych tworzenia zadań aktualizacji z przędzą.|Brak wymiarów|
|Jobs. createTwinUpdateJob. Failure|Nie można utworzyć dwuosiowych zadań aktualizacji|Liczba|Łącznie|Liczba wszystkich nieudanych operacji tworzenia zadań aktualizacji z przędzą.|Brak wymiarów|
|Jobs. createDirectMethodJob. Success|Pomyślne utworzenie zadań wywołania metody|Liczba|Łącznie|Liczba wszystkich pomyślnych operacji tworzenia zadań wywołania metody bezpośredniej.|Brak wymiarów|
|Jobs. createDirectMethodJob. Failure|Nie można utworzyć zadań wywołania metody|Liczba|Łącznie|Liczba wszystkich nieudanych operacji tworzenia zadań wywołania metody bezpośredniej.|Brak wymiarów|
|Jobs. listJobs. Success|Pomyślne wywołania do zadań na liście|Liczba|Łącznie|Liczba wszystkich udanych wywołań do listy zadań.|Brak wymiarów|
|Jobs. listJobs. Failure|Wywołania zakończone niepowodzeniem do listy zadań|Liczba|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu wyświetlenia listy zadań.|Brak wymiarów|
|Jobs. cancelJob. Success|Pomyślne anulowania zadań|Liczba|Łącznie|Liczba wszystkich udanych wywołań do anulowania zadania.|Brak wymiarów|
|Jobs. cancelJob. Failure|Nieudane anulowania zadań|Liczba|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu anulowania zadania.|Brak wymiarów|
|Jobs. queryJobs. Success|Pomyślne zapytania dotyczące zadań|Liczba|Łącznie|Liczba wszystkich udanych wywołań do zadań zapytań.|Brak wymiarów|
|Jobs. queryJobs. Failure|Nieudane kwerendy zadań|Liczba|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu wykonywania zapytań dotyczących zadań.|Brak wymiarów|
|zadania. ukończone|Ukończone zadania|Liczba|Łącznie|Liczba wszystkich ukończonych zadań.|Brak wymiarów|
|zadania. Niepowodzenie|Zadania zakończone niepowodzeniem|Liczba|Łącznie|Liczba wszystkich zadań zakończonych niepowodzeniem.|Brak wymiarów|
|D2C. telemetrię. sendThrottle|Liczba błędów ograniczania|Liczba|Łącznie|Liczba błędów ograniczania z powodu ograniczeń przepływności urządzenia|Brak wymiarów|
|dailyMessageQuotaUsed|Całkowita liczba użytych komunikatów|Liczba|Średnia|Całkowita liczba użytych komunikatów. Jest to skumulowana wartość, która jest resetowana do zera o godzinie 00:00 czasu każdego dnia.|Brak wymiarów|
|deviceDataUsage|Całkowite użycie danych urządzenia|Szybkość|Łącznie|Bajty przesłane do i z dowolnych urządzeń podłączonych do usługi IotHub|Brak wymiarów|
|totalDeviceCount|Łączna liczba urządzeń (wersja zapoznawcza)|Liczba|Średnia|Liczba urządzeń zarejestrowanych w usłudze IoT Hub|Brak wymiarów|
|connectedDeviceCount|Podłączone urządzenia (wersja zapoznawcza)|Liczba|Średnia|Liczba urządzeń podłączonych do centrum IoT Hub|Brak wymiarów|
|komputerów|Metryki konfiguracji|Liczba|Łącznie|Metryki dla operacji konfiguracji|Brak wymiarów|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RegistrationAttempts|Próby rejestracji|Liczba|Łącznie|Liczba prób przeprowadzenia rejestracji urządzeń|ProvisioningServiceName, IotHubName, status|
|DeviceAssignments|Przypisane urządzenia|Liczba|Łącznie|Liczba urządzeń przypisanych do centrum IoT Hub|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Próby zaświadczania|Liczba|Łącznie|Liczba podjętych prób zaświadczania urządzenia|ProvisioningServiceName, stan, protokół|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft. DocumentDB/databaseAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AvailableStorage|Dostępny magazyn|Szybkość|Łącznie|Łączna ilość dostępnego magazynu zgłoszona z dokładnością do 5 minut|CollectionName, DatabaseName, region|
|CassandraConnectionClosures|Zamknięcia połączeń Cassandra|Liczba|Łącznie|Liczba zamkniętych połączeń Cassandra, które zostały zgłoszone z dokładnością do 1 minuty|Region, ClosureReason|
|CassandraRequestCharges|Opłaty za żądania Cassandra|Liczba|Łącznie|Jednostek ru zużyte dla żądań Cassandra|DatabaseName, CollectionName, region, OperationType, ResourceType|
|CassandraRequests|Żądania Cassandra|Liczba|Liczba|Liczba wykonanych żądań Cassandra|DatabaseName, CollectionName, region, OperationType, ResourceType, ErrorCode|
|Datausage|Użycie danych|Szybkość|Łącznie|Całkowite użycie danych zgłoszone z dokładnością do 5 minut|CollectionName, DatabaseName, region|
|DocumentCount|Liczba dokumentów|Liczba|Łącznie|Łączna liczba dokumentów raportowana z dokładnością do 5 minut|CollectionName, DatabaseName, region|
|DocumentQuota|Przydział dokumentu|Szybkość|Łącznie|Łączny przydział magazynu zgłoszony z dokładnością do 5 minut|CollectionName, DatabaseName, region|
|IndexUsage|Użycie indeksu|Szybkość|Łącznie|Całkowite użycie indeksów zgłoszone na 5 minut|CollectionName, DatabaseName, region|
|MetadataRequests|Żądania metadanych|Liczba|Liczba|Liczba żądań metadanych. Cosmos DB przechowuje zbieranie metadanych systemu dla każdego konta, które pozwala na Wyliczanie kolekcji, baz danych itp. i ich konfiguracji, bez opłat.|DatabaseName, CollectionName, region, StatusCode, |
|MongoRequestCharge|Opłata żądania Mongo|Liczba|Łącznie|Wykorzystane jednostki żądania Mongo|DatabaseName, CollectionName, region, CommandName, ErrorCode|
|MongoRequests|Żądania Mongo|Liczba|Liczba|Liczba wykonanych żądań Mongo|DatabaseName, CollectionName, region, CommandName, ErrorCode|
|ProvisionedThroughput|Aprowizowana przepływność|Liczba|Maksimum|Aprowizowana przepływność|DatabaseName, CollectionName|
|ReplicationLatency|Opóźnienie replikacji poziomie P99|)|Średnia|Opóźnienie replikacji poziomie P99 w regionach źródłowym i docelowym dla konta z obsługą geograficzną|SourceRegion, TargetRegion|
|Dostępność|Dostępność usługi|Procent|Średnia|Dostępność żądania konta o jednej godzinie, dniu lub o dokładności|Brak wymiarów|
|TotalRequestUnits|Łączna liczba jednostek żądania|Liczba|Łącznie|Wykorzystane jednostki żądania|DatabaseName, CollectionName, region, StatusCode, OperationType|
|TotalRequests|Łączna liczba żądań|Liczba|Liczba|Liczba wykonanych żądań|DatabaseName, CollectionName, region, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/tematy

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PublishSuccessCount|Zdarzenia opublikowane|Liczba|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Brak wymiarów|
|PublishFailCount|Publikowanie zdarzeń zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których publikowanie nie powiodło się w tym temacie|ErrorType, błąd|
|UnmatchedEventCount|Niedopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń, które nie pasują do żadnej subskrypcji zdarzeń dla tego tematu|Brak wymiarów|
|PublishSuccessLatencyInMs|Czas oczekiwania na pomyślne publikowanie|Liczba|Łącznie|Opóźnienie sukcesu publikacji w milisekundach|Brak wymiarów|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|MatchedEventCount|Dopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dopasowanych do tej subskrypcji zdarzeń|Brak wymiarów|
|DeliveryAttemptFailCount|Zdarzenia zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których dostarczenie do tej subskrypcji zdarzeń nie powiodło się|Błąd, Błądtype|
|DeliverySuccessCount|Dostarczone zdarzenia|Liczba|Łącznie|Całkowita liczba zdarzeń dostarczonych do tej subskrypcji zdarzeń|Brak wymiarów|
|DestinationProcessingDurationInMs|Czas przetwarzania docelowego|)|Średnia|Czas trwania przetwarzania docelowego w milisekundach|Brak wymiarów|
|DroppedEventCount|Opuszczone zdarzenia|Liczba|Łącznie|Całkowita liczba porzuconych zdarzeń pasujących do tej subskrypcji zdarzeń|DropReason|
|DeadLetteredCount|Zdarzenia utraconych wiadomości|Liczba|Łącznie|Łączna liczba utraconych zdarzeń, które pasują do tej subskrypcji zdarzeń|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PublishSuccessCount|Zdarzenia opublikowane|Liczba|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Brak wymiarów|
|PublishFailCount|Zdarzenia zakończone niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których publikowanie nie powiodło się w tym temacie|ErrorType, błąd|
|UnmatchedEventCount|Niedopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń, które nie pasują do żadnej subskrypcji zdarzeń dla tego tematu|Brak wymiarów|
|PublishSuccessLatencyInMs|Czas oczekiwania na pomyślne publikowanie|Liczba|Łącznie|Opóźnienie sukcesu publikacji w milisekundach|Brak wymiarów|

## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/przestrzenie nazw

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Żądania successfulrequests|Żądania zakończone powodzeniem|Liczba|Łącznie|Pomyślne żądania dla elementu Microsoft. EventHub.|EntityName |
|Błędy servererrors|Błędy serwera.|Liczba|Łącznie|Błędy serwera dla elementu Microsoft. EventHub.|EntityName |
|Błędy usererrors|Błędy użytkownika.|Liczba|Łącznie|Błędy użytkowników dla elementu Microsoft. EventHub.|EntityName |
|Błędy quotaexceedederrors|Błędy przekroczenia limitu przydziału.|Liczba|Łącznie|Przekroczono limit przydziału dla elementu Microsoft. EventHub.|EntityName |
|ThrottledRequests|Żądania z ograniczeniami.|Liczba|Łącznie|Żądania ograniczone dla elementu Microsoft. EventHub.|EntityName |
|Żądania incomingrequests|Żądania przychodzące|Liczba|Łącznie|Żądania przychodzące dla elementu Microsoft. EventHub.|EntityName|
|Komunikaty incomingmessages|Komunikaty przychodzące|Liczba|Łącznie|Komunikaty przychodzące dla elementu Microsoft. EventHub.|EntityName|
|Komunikaty outgoingmessages|Komunikaty wychodzące|Liczba|Łącznie|Komunikaty wychodzące dla elementu Microsoft. EventHub.|EntityName|
|Bajty incomingbytes|Bajty przychodzące.|Szybkość|Łącznie|Przychodzące bajty dla elementu Microsoft. EventHub.|EntityName|
|Bajty outgoingbytes|Bajty wychodzące.|Szybkość|Łącznie|Bajty wychodzące dla elementu Microsoft. EventHub.|EntityName|
|Połączeń ActiveConnections|Połączeń ActiveConnections|Liczba|Średnia|Łączna liczba aktywnych połączeń dla elementu Microsoft. EventHub.|Brak wymiarów|
|Połączenia connectionsopened|Otwarte połączenia.|Liczba|Średnia|Połączenia otwarte dla elementu Microsoft. EventHub.|EntityName|
|Połączenia connectionsclosed|Połączenia zamknięte.|Liczba|Średnia|Połączenia zamknięte dla elementu Microsoft. EventHub.|EntityName|
|CaptureBacklog|Zaległości przechwytywania.|Liczba|Łącznie|Zaległości przechwytywania dla elementu Microsoft. EventHub.|EntityName|
|CapturedMessages|Przechwycone komunikaty.|Liczba|Łącznie|Przechwycone komunikaty dla elementu Microsoft. EventHub.|EntityName|
|CapturedBytes|Przechwycone bajty.|Szybkość|Łącznie|Przechwycone bajty dla elementu Microsoft. EventHub.|EntityName|
|Rozmiar|Rozmiar|Szybkość|Średnia|Rozmiar centrum EventHub w bajtach.|EntityName|
|INREQS|Żądania przychodzące (przestarzałe)|Liczba|Łącznie|Całkowita liczba żądań wysłania przychodzącego dla przestrzeni nazw (przestarzałe)|Brak wymiarów|
|SUCCREQ|Żądania zakończone powodzeniem (przestarzałe)|Liczba|Łącznie|Łączna liczba pomyślnych żądań dla przestrzeni nazw (przestarzałe)|Brak wymiarów|
|FAILREQ|Nieudane żądania (przestarzałe)|Liczba|Łącznie|Łączna liczba nieudanych żądań dla przestrzeni nazw (przestarzałe)|Brak wymiarów|
|SVRBSY|Błędy zajęte przez serwer (przestarzałe)|Liczba|Łącznie|Łączna liczba błędów zajętości serwera dla przestrzeni nazw (przestarzałe)|Brak wymiarów|
|MIĘDZY|Wewnętrzne błędy serwera (przestarzałe)|Liczba|Łącznie|Łączna liczba błędów wewnętrznego serwera dla przestrzeni nazw (przestarzałe)|Brak wymiarów|
|MISCERR|Inne błędy (przestarzałe)|Liczba|Łącznie|Łączna liczba nieudanych żądań dla przestrzeni nazw (przestarzałe)|Brak wymiarów|
|INMSGS|Komunikaty przychodzące (przestarzałe) (przestarzałe)|Liczba|Łącznie|Całkowita liczba komunikatów przychodzących dla przestrzeni nazw. Ta Metryka jest przestarzała. Zamiast tego użyj metryki komunikatów przychodzących (przestarzałe)|Brak wymiarów|
|EHINMSGS|Komunikaty przychodzące (przestarzałe)|Liczba|Łącznie|Całkowita liczba komunikatów przychodzących dla przestrzeni nazw (przestarzałe)|Brak wymiarów|
|OUTMSGS|Wiadomości wychodzące (przestarzałe) (przestarzałe)|Liczba|Łącznie|Całkowita liczba komunikatów wychodzących dla przestrzeni nazw. Ta Metryka jest przestarzała. Zamiast tego użyj metryki komunikatów wychodzących (przestarzałe)|Brak wymiarów|
|EHOUTMSGS|Wiadomości wychodzące (przestarzałe)|Liczba|Łącznie|Całkowita liczba komunikatów wychodzących dla przestrzeni nazw (przestarzałe)|Brak wymiarów|
|EHINMBS|Przychodzące bajty (przestarzałe) (przestarzałe)|Szybkość|Łącznie|Przepływność komunikatów przychodzących centrum zdarzeń dla przestrzeni nazw. Ta Metryka jest przestarzała. Zamiast tego użyj metryki bajtów przychodzących (przestarzałe)|Brak wymiarów|
|EHINBYTES|Przychodzące bajty (przestarzałe)|Szybkość|Łącznie|Przepływność komunikatów przychodzących centrum zdarzeń dla przestrzeni nazw (przestarzałe)|Brak wymiarów|
|EHOUTMBS|Bajty wychodzące (przestarzałe) (przestarzałe)|Szybkość|Łącznie|Przepływność komunikatów wychodzących centrum zdarzeń dla przestrzeni nazw. Ta Metryka jest przestarzała. Zamiast tego użyj metryki bajtów wychodzących (przestarzałe)|Brak wymiarów|
|EHOUTBYTES|Bajty wychodzące (przestarzałe)|Szybkość|Łącznie|Przepływność komunikatów wychodzących centrum zdarzeń dla przestrzeni nazw (przestarzałe)|Brak wymiarów|
|EHABL|Archiwum komunikatów zaległości (przestarzałe)|Liczba|Łącznie|Komunikaty archiwum centrum zdarzeń w zaległości dla przestrzeni nazw (przestarzałe)|Brak wymiarów|
|EHAMSGS|Komunikaty archiwalne (przestarzałe)|Liczba|Łącznie|Zarchiwizowane komunikaty centrum zdarzeń w przestrzeni nazw (przestarzałe)|Brak wymiarów|
|EHAMBS|Przepływność komunikatów archiwalnych (przestarzałe)|Szybkość|Łącznie|Przepływność komunikatów archiwalnych centrum zdarzeń w przestrzeni nazw (przestarzałe)|Brak wymiarów|

## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/klastry

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Żądania successfulrequests|Pomyślne żądania (wersja zapoznawcza)|Liczba|Łącznie|Pomyślne żądania dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Brak wymiarów|
|Błędy servererrors|Błędy serwera. (Wersja zapoznawcza)|Liczba|Łącznie|Błędy serwera dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Brak wymiarów|
|Błędy usererrors|Błędy użytkownika. (Wersja zapoznawcza)|Liczba|Łącznie|Błędy użytkowników dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Brak wymiarów|
|Błędy quotaexceedederrors|Błędy przekroczenia limitu przydziału. (Wersja zapoznawcza)|Liczba|Łącznie|Przekroczono limit przydziału dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Brak wymiarów|
|ThrottledRequests|Żądania z ograniczeniami. (Wersja zapoznawcza)|Liczba|Łącznie|Żądania ograniczone dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Brak wymiarów|
|Żądania incomingrequests|Żądania przychodzące (wersja zapoznawcza)|Liczba|Łącznie|Żądania przychodzące dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Brak wymiarów|
|Komunikaty incomingmessages|Wiadomości przychodzące (wersja zapoznawcza)|Liczba|Łącznie|Komunikaty przychodzące dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Brak wymiarów|
|Komunikaty outgoingmessages|Wiadomości wychodzące (wersja zapoznawcza)|Liczba|Łącznie|Komunikaty wychodzące dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Brak wymiarów|
|Bajty incomingbytes|Bajty przychodzące. (Wersja zapoznawcza)|Szybkość|Łącznie|Przychodzące bajty dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Brak wymiarów|
|Bajty outgoingbytes|Bajty wychodzące. (Wersja zapoznawcza)|Szybkość|Łącznie|Bajty wychodzące dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Brak wymiarów|
|Połączeń ActiveConnections|Połączeń ActiveConnections (wersja zapoznawcza)|Liczba|Średnia|Łączna liczba aktywnych połączeń dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Brak wymiarów|
|Połączenia connectionsopened|Otwarte połączenia. (Wersja zapoznawcza)|Liczba|Średnia|Połączenia otwarte dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Brak wymiarów|
|Połączenia connectionsclosed|Połączenia zamknięte. (Wersja zapoznawcza)|Liczba|Średnia|Połączenia zamknięte dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Brak wymiarów|
|CaptureBacklog|Zaległości przechwytywania. (Wersja zapoznawcza)|Liczba|Łącznie|Zaległości przechwytywania dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Brak wymiarów|
|CapturedMessages|Przechwycone komunikaty. (Wersja zapoznawcza)|Liczba|Łącznie|Przechwycone komunikaty dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Brak wymiarów|
|CapturedBytes|Przechwycone bajty. (Wersja zapoznawcza)|Szybkość|Łącznie|Przechwycone bajty dla elementu Microsoft. EventHub. (Wersja zapoznawcza)|Brak wymiarów|
|Procesor CPU|Procesor CPU (wersja zapoznawcza)|Procent|Maksimum|Użycie procesora CPU przez klaster centrum zdarzeń jako wartość procentowa|Rola|
|AvailableMemory|Dostępna pamięć (wersja zapoznawcza)|Liczba|Maksimum|Dostępna pamięć dla klastra centrum zdarzeń w bajtach|Rola|

## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/Klastry

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|GatewayRequests|Żądania bramy|Liczba|Łącznie|Liczba żądań bramy|ClusterDnsName, wartości httpStatus|
|CategorizedGatewayRequests|Skategoryzowane żądania bramy|Liczba|Łącznie|Liczba żądań bramy według kategorii (1XX/2xx/3xx/4xx/5xx)|ClusterDnsName, wartości httpStatus|
|NumActiveWorkers|Liczba aktywnych procesów roboczych|Liczba|Maksimum|Liczba aktywnych procesów roboczych|ClusterDnsName, Metricname|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. Insights/AutoscaleSettings

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ObservedMetricValue|Obserwowana wartość metryki|Liczba|Średnia|Wartość obliczona przez automatyczne skalowanie podczas wykonywania|MetricTriggerSource|
|MetricThreshold|Próg metryki|Liczba|Średnia|Skonfigurowany próg automatycznego skalowania podczas uruchamiania automatycznego skalowania.|MetricTriggerRule|
|ObservedCapacity|Zaobserwowana pojemność|Liczba|Średnia|Pojemność zgłoszona do automatycznego skalowania podczas jego wykonywania.|Brak wymiarów|
|ScaleActionsInitiated|Zainicjowane akcje skalowania|Liczba|Łącznie|Kierunek operacji skalowania.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/składniki

(Publiczna wersja zapoznawcza)

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Dostępność|Procent|Średnia|Procent pomyślnie ukończonych testów dostępności|availabilityResult/nazwa, availabilityResult/lokalizacja|
|availabilityResults/liczba|Testy dostępności|Liczba|Liczba|Liczba testów dostępności|availabilityResult/nazwa, availabilityResult/lokalizacja, availabilityResult/sukces|
|availabilityResults/czas trwania|Czas trwania testu dostępności|)|Średnia|Czas trwania testu dostępności|availabilityResult/nazwa, availabilityResult/lokalizacja, availabilityResult/sukces|
|browserTimings/networkDuration|Czas połączenia sieciowego ładowania strony|)|Średnia|Czas między żądaniem użytkownika a połączeniem sieciowym. Obejmuje wyszukiwanie DNS i połączenie transportowe.|Brak wymiarów|
|browserTimings/processingDuration|Czas przetwarzania klienta|)|Średnia|Czas między odebraniem ostatniego bajtu dokumentu do momentu załadowania modelu DOM. Żądania asynchroniczne nadal mogą być przetwarzane.|Brak wymiarów|
|browserTimings/receiveDuration|Czas odpowiedzi na odebranie|)|Średnia|Czas między pierwszym i ostatnim bajtem lub do momentu odłączenia.|Brak wymiarów|
|browserTimings/sendDuration|Czas żądania wysłania|)|Średnia|Czas między połączeniem sieciowym i odebraniem pierwszego bajtu.|Brak wymiarów|
|browserTimings/totalDuration|Czas ładowania strony w przeglądarce|)|Średnia|Czas od żądania użytkownika do załadowania modelu DOM, arkuszy stylów, skryptów i obrazów.|Brak wymiarów|
|zależności/liczba|Wywołania zależności|Liczba|Liczba|Liczba wywołań wykonanych przez aplikację do zasobów zewnętrznych.|zależność/typ, zależność/performanceBucket, zależność/sukces, operacja/syntetyczne, Chmura/roleInstance, Chmura/rolename|
|zależności/czas trwania|Czas trwania zależności|)|Średnia|Czas trwania wywołań wykonanych przez aplikację do zasobów zewnętrznych.|zależność/typ, zależność/performanceBucket, zależność/sukces, operacja/syntetyczne, Chmura/roleInstance, Chmura/rolename|
|zależności/niepowodzenie|Błędy wywołań zależności|Liczba|Liczba|Liczba wywołań zależności zakończonych niepowodzeniem wykonanych przez aplikację w zasobach zewnętrznych.|zależność/typ, zależność/performanceBucket, operacja/syntetyczne, Chmura/roleInstance, Chmura/rolename|
|pageViews/liczba|Wyświetlenia stron|Liczba|Liczba|Liczba wyświetleń stron.|Operacja/syntetyczna|
|pageViews/czas trwania|Czas ładowania widoku strony|)|Średnia|Czas ładowania widoku strony|Operacja/syntetyczna|
|Liczniki wydajności/requestExecutionTime|Czas wykonywania żądania HTTP|)|Średnia|Czas wykonywania najnowszego żądania.|Chmura/roleInstance|
|Liczniki wydajności/requestsInQueue|Żądania HTTP w kolejce aplikacji|Liczba|Średnia|Długość kolejki żądań aplikacji.|Chmura/roleInstance|
|Liczniki wydajności/requestsPerSecond|Częstotliwość żądań HTTP|CountPerSecond|Średnia|Szybkość wszystkich żądań wysyłanych do aplikacji w ciągu sekundy od ASP.NET.|Chmura/roleInstance|
|Liczniki wydajności/exceptionsPerSecond|Częstotliwość wyjątków|CountPerSecond|Średnia|Liczba obsłużonych i nieobsłużonych wyjątków zgłoszonych w systemie Windows, łącznie z wyjątkami platformy .NET i niezarządzanymi wyjątkami przekonwertowanymi na wyjątki platformy .NET.|Chmura/roleInstance|
|Liczniki wydajności/processIOBytesPerSecond|Szybkość operacji we/wy procesu|BytesPerSecond|Średnia|Łączna liczba bajtów odczytanych i zapisywana w plikach, sieci i urządzeniach.|Chmura/roleInstance|
|Liczniki wydajności/processCpuPercentage|Procesor CPU procesu|Procent|Średnia|Wyrażony w procentach czas, przez jaki wszystkie wątki procesów używały procesora do wykonywania instrukcji. Może się to różnić od od 0 do 100. Ta Metryka wskazuje na wydajność samego procesu w3wp.|Chmura/roleInstance|
|Liczniki wydajności/processorCpuPercentage|Czas procesora|Procent|Średnia|Procent czasu spędzanego przez procesor w wątkach, które nie są bezczynne.|Chmura/roleInstance|
|Liczniki wydajności/memoryAvailableBytes|Dostępna pamięć|Szybkość|Średnia|Pamięć fizyczna natychmiast dostępna do przydzielenia do procesu lub do użycia przez system.|Chmura/roleInstance|
|Liczniki wydajności/processPrivateBytes|Prywatne bajty procesu|Szybkość|Średnia|Pamięć przypisana wyłącznie do procesów monitorowanej aplikacji.|Chmura/roleInstance|
|żądania/czas trwania|Czas odpowiedzi serwera|)|Średnia|Czas między odebraniem żądania HTTP i zakończeniem wysyłania odpowiedzi.|żądanie/performanceBucket, żądanie/resultCode, Operation/syntetyczne, Cloud/roleInstance, żądanie/powodzenie, Chmura/rolename|
|Liczba żądań na sekundę|Żądania serwera|Liczba|Liczba|Liczba ukończonych żądań HTTP.|żądanie/performanceBucket, żądanie/resultCode, Operation/syntetyczne, Cloud/roleInstance, żądanie/powodzenie, Chmura/rolename|
|żądania/niepowodzenie|Żądania zakończone niepowodzeniem|Liczba|Liczba|Liczba żądań HTTP oznaczonych jako zakończone niepowodzeniem. W większości przypadków są to żądania z kodem odpowiedzi > = 400, a nie równe 401.|żądania/performanceBucket, Request/resultCode, Operation/syntetyczne, Cloud/roleInstance, Cloud/rolename|
|żądania/częstotliwość|Liczba żądań serwera|CountPerSecond|Średnia|Liczba żądań serwera na sekundę|żądanie/performanceBucket, żądanie/resultCode, Operation/syntetyczne, Cloud/roleInstance, żądanie/powodzenie, Chmura/rolename|
|wyjątki/liczba|Wyjątki|Liczba|Liczba|Łączna liczba wszystkich nieprzechwyconych wyjątków.|Chmura/rolename, Cloud/roleInstance, klient/typ|
|wyjątki/przeglądarka|Wyjątki przeglądarki|Liczba|Liczba|Liczba nieprzechwyconych wyjątków zgłoszonych w przeglądarce.|Brak wymiarów|
|wyjątki/serwer|Wyjątki serwera|Liczba|Liczba|Liczba nieprzechwyconych wyjątków zgłoszonych w aplikacji serwera.|Chmura/rolename, Cloud/roleInstance|
|ślady/liczba|Ścieżki|Liczba|Liczba|Liczba dokumentów śledzenia|Trace/severityLevel, Operations/syntetyczne, Cloud/rolename, Cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft./magazyny kluczy

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ServiceApiHit|Całkowita liczba trafień interfejsu API usługi|Liczba|Liczba|Łączna liczba trafień interfejsu API usługi|ActivityType, ActivityName|
|ServiceApiLatency|Ogólne opóźnienie interfejsu API usługi|)|Średnia|Ogólne opóźnienie żądań interfejsu API usługi|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Łączna liczba wyników interfejsu API usługi|Liczba|Liczba|Łączna liczba wyników interfejsu API usługi|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/klastry

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CacheUtilization|Użycie pamięci podręcznej|Procent|Średnia|Poziom użycia w zakresie klastra|Brak|
|QueryDuration|Czas trwania zapytania|)|Średnia|Czas trwania zapytań (w sekundach)|Stan zapytania|
|IngestionUtilization|Wykorzystanie pozyskiwania|Procent|Średnia|Współczynnik używania miejsc pozyskiwania w klastrze|Brak|
|Utrzymywani|Utrzymywanie aktywności|Liczba|Średnia|Sprawdzenie Sanity wskazuje, że klaster reaguje na zapytania|Brak|
|IngestionVolumeInMB|Wolumin pozyskiwania (w MB)|Liczba|Łącznie|Ogólna ilość danych pozyskiwanych w klastrze (w MB)|Database (Baza danych)|
|IngestionLatencyInSeconds|Opóźnienie pozyskiwania (w sekundach)|Sekundy|Średnia|Czas pozyskiwania ze źródła (np. komunikat jest w centrum EventHub) do klastra w ciągu kilku sekund|Brak|
|EventProcessedForEventHubs|Przetworzone zdarzenia (dla Event Hubs)|Liczba|Łącznie|Liczba zdarzeń przetwarzanych przez klaster podczas pozyskiwania danych z centrum zdarzeń|Brak|
|IngestionResult|Wynik pozyskiwania|Liczba|Liczba|Liczba operacji pozyskiwania|Stan|
|Procesor CPU|Procesor CPU|Procent|Średnia|Poziom użycia procesora CPU|Brak|
| ContinuousExportNumOfRecordsExported | Liczba rekordów eksportowanych w ramach eksportu ciągłego | Liczba | Łącznie | Liczba rekordów eksportowanych dla każdego artefaktu magazynu zapisanego podczas operacji eksportowania  | Brak |
| ExportUtilization | Użycie eksportu | Procent | Maksimum | Użycie eksportu | Brak |
| ContinuousExportPendingCount | Liczba oczekujących eksportu ciągłego | Liczba | Maksimum | Liczba oczekujących zadań eksportu ciągłego gotowych do wykonania | Brak |
| ContinuousExportMaxLatenessMinutes | Maksymalna liczba minut opóźnienia eksportu | Liczba | Maksimum | Maksymalny czas (w minutach) wszystkich eksportów ciągłych oczekujących i gotowych do wykonania | Brak |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft. LocationBasedServices/konta

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Użycie|Użycie|Liczba|Liczba|Liczba wywołań interfejsu API|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/przepływy pracy

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RunsStarted|Uruchomienia uruchomione|Liczba|Łącznie|Liczba uruchomionych przebiegów przepływu pracy.|Brak wymiarów|
|RunsCompleted|Przebiegi zakończone|Liczba|Łącznie|Liczba ukończonych przebiegów przepływu pracy.|Brak wymiarów|
|RunsSucceeded|Przebiegi zakończone powodzeniem|Liczba|Łącznie|Liczba przebiegów przepływu pracy zakończonych powodzeniem.|Brak wymiarów|
|RunsFailed|Przebiegi zakończone niepowodzeniem|Liczba|Łącznie|Liczba przebiegów przepływu pracy zakończonych niepowodzeniem.|Brak wymiarów|
|RunsCancelled|Przebiegi anulowane|Liczba|Łącznie|Liczba anulowanych przebiegów przepływu pracy.|Brak wymiarów|
|RunLatency|Opóźnienie uruchamiania|Sekundy|Średnia|Opóźnienie ukończonych przebiegów przepływu pracy.|Brak wymiarów|
|RunSuccessLatency|Opóźnienie sukcesu przebiegu|Sekundy|Średnia|Opóźnienie przebiegów przepływu pracy zakończonych powodzeniem.|Brak wymiarów|
|RunThrottledEvents|Uruchamianie zdarzeń z ograniczeniami|Liczba|Łącznie|Liczba zdarzeń związanych ze zdarzeniami przepływu pracy lub wyzwalaczem.|Brak wymiarów|
|RunFailurePercentage|Procent niepowodzeń przebiegu|Procent|Łącznie|Procent przebiegów przepływu pracy zakończonych niepowodzeniem.|Brak wymiarów|
|ActionsStarted|Akcje uruchomione |Liczba|Łącznie|Liczba rozpoczętych akcji przepływu pracy.|Brak wymiarów|
|ActionsCompleted|Wykonane akcje |Liczba|Łącznie|Liczba ukończonych akcji przepływu pracy.|Brak wymiarów|
|ActionsSucceeded|Akcje zakończone powodzeniem |Liczba|Łącznie|Liczba akcji przepływu pracy zakończonych powodzeniem.|Brak wymiarów|
|ActionsFailed|Akcje zakończone niepowodzeniem|Liczba|Łącznie|Liczba akcji przepływu pracy zakończonych niepowodzeniem.|Brak wymiarów|
|ActionsSkipped|Pominięte akcje |Liczba|Łącznie|Liczba pominiętych akcji przepływu pracy.|Brak wymiarów|
|ActionLatency|Opóźnienie akcji |Sekundy|Średnia|Opóźnienie ukończonych akcji przepływu pracy.|Brak wymiarów|
|ActionSuccessLatency|Opóźnienie sukcesu akcji |Sekundy|Średnia|Opóźnienie akcji przepływu pracy zakończonych powodzeniem.|Brak wymiarów|
|ActionThrottledEvents|Zdarzenia ograniczające akcję|Liczba|Łącznie|Liczba zdarzeń ograniczających przepływ pracy...|Brak wymiarów|
|TriggersStarted|Uruchomiono wyzwalacze |Liczba|Łącznie|Liczba uruchomionych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggersCompleted|Ukończone wyzwalacze |Liczba|Łącznie|Liczba ukończonych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggersSucceeded|Wyzwalacze zakończone powodzeniem |Liczba|Łącznie|Liczba wyzwalaczy przepływu pracy zakończonych powodzeniem.|Brak wymiarów|
|TriggersFailed|Wyzwalacze zakończone niepowodzeniem |Liczba|Łącznie|Liczba wyzwalaczy przepływu pracy zakończonych niepowodzeniem.|Brak wymiarów|
|TriggersSkipped|Pominięte wyzwalacze|Liczba|Łącznie|Liczba pominiętych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggersFired|Wyzwolone wyzwalacze |Liczba|Łącznie|Liczba wyzwolonych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggerLatency|Opóźnienie wyzwalacza |Sekundy|Średnia|Opóźnienie ukończonych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggerFireLatency|Opóźnienie wyzwalania wyzwalacza |Sekundy|Średnia|Opóźnienie wyzwalanych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggerSuccessLatency|Opóźnienie sukcesu wyzwalacza |Sekundy|Średnia|Opóźnienie wyzwalaczy przepływu pracy zakończonych powodzeniem.|Brak wymiarów|
|TriggerThrottledEvents|Wyzwalaj zdarzenia ograniczające|Liczba|Łącznie|Liczba zdarzeń ograniczenia wyzwalacza przepływu pracy.|Brak wymiarów|
|BillableActionExecutions|Rozliczane wykonania akcji|Liczba|Łącznie|Liczba rozliczanych wykonań akcji przepływu pracy.|Brak wymiarów|
|BillableTriggerExecutions|Rozliczane wykonania wyzwalacza|Liczba|Łącznie|Liczba rozliczanych wykonań wyzwalacza przepływu pracy.|Brak wymiarów|
|TotalBillableExecutions|Łączna Liczba wykonań rozliczanych|Liczba|Łącznie|Liczba rozliczanych wykonań przepływu pracy.|Brak wymiarów|
|BillingUsageNativeOperation|Użycie rozliczeń dla natywnych wykonań operacji|Liczba|Łącznie|Liczba rozliczanych wykonań operacji natywnych.|Brak wymiarów|
|BillingUsageStandardConnector|Użycie rozliczeń dla wykonywania łączników standardowych|Liczba|Łącznie|Liczba rozliczanych wykonań łączników standardowych.|Brak wymiarów|
|BillingUsageStorageConsumption|Użycie rozliczeń dla wykonań zużycia magazynu|Liczba|Łącznie|Liczba rozliczanych wykonań zużycia magazynu.|Brak wymiarów|
|BillingUsageNativeOperation|Użycie rozliczeń dla natywnych wykonań operacji|Liczba|Łącznie|Liczba rozliczanych wykonań operacji natywnych.|Brak wymiarów|
|BillingUsageStandardConnector|Użycie rozliczeń dla wykonywania łączników standardowych|Liczba|Łącznie|Liczba rozliczanych wykonań łączników standardowych.|Brak wymiarów|
|BillingUsageStorageConsumption|Użycie rozliczeń dla wykonań zużycia magazynu|Liczba|Łącznie|Liczba rozliczanych wykonań zużycia magazynu.|Brak wymiarów|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/integrationServiceEnvironments

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RunsStarted|Uruchomienia uruchomione|Liczba|Łącznie|Liczba uruchomionych przebiegów przepływu pracy.|Brak wymiarów|
|RunsCompleted|Przebiegi zakończone|Liczba|Łącznie|Liczba ukończonych przebiegów przepływu pracy.|Brak wymiarów|
|RunsSucceeded|Przebiegi zakończone powodzeniem|Liczba|Łącznie|Liczba przebiegów przepływu pracy zakończonych powodzeniem.|Brak wymiarów|
|RunsFailed|Przebiegi zakończone niepowodzeniem|Liczba|Łącznie|Liczba przebiegów przepływu pracy zakończonych niepowodzeniem.|Brak wymiarów|
|RunsCancelled|Przebiegi anulowane|Liczba|Łącznie|Liczba anulowanych przebiegów przepływu pracy.|Brak wymiarów|
|RunLatency|Opóźnienie uruchamiania|Sekundy|Średnia|Opóźnienie ukończonych przebiegów przepływu pracy.|Brak wymiarów|
|RunSuccessLatency|Opóźnienie sukcesu przebiegu|Sekundy|Średnia|Opóźnienie przebiegów przepływu pracy zakończonych powodzeniem.|Brak wymiarów|
|RunThrottledEvents|Uruchamianie zdarzeń z ograniczeniami|Liczba|Łącznie|Liczba zdarzeń związanych ze zdarzeniami przepływu pracy lub wyzwalaczem.|Brak wymiarów|
|RunStartThrottledEvents|Uruchom zdarzenia uruchamiania z ograniczeniami|Liczba|Łącznie|Liczba uruchomionych zdarzeń ograniczających przepływ pracy.|Brak wymiarów|
|RunFailurePercentage|Procent niepowodzeń przebiegu|Procent|Łącznie|Procent przebiegów przepływu pracy zakończonych niepowodzeniem.|Brak wymiarów|
|ActionsStarted|Akcje uruchomione |Liczba|Łącznie|Liczba rozpoczętych akcji przepływu pracy.|Brak wymiarów|
|ActionsCompleted|Wykonane akcje |Liczba|Łącznie|Liczba ukończonych akcji przepływu pracy.|Brak wymiarów|
|ActionsSucceeded|Akcje zakończone powodzeniem |Liczba|Łącznie|Liczba akcji przepływu pracy zakończonych powodzeniem.|Brak wymiarów|
|ActionsFailed|Akcje zakończone niepowodzeniem |Liczba|Łącznie|Liczba akcji przepływu pracy zakończonych niepowodzeniem.|Brak wymiarów|
|ActionsSkipped|Pominięte akcje |Liczba|Łącznie|Liczba pominiętych akcji przepływu pracy.|Brak wymiarów|
|ActionLatency|Opóźnienie akcji |Sekundy|Średnia|Opóźnienie ukończonych akcji przepływu pracy.|Brak wymiarów|
|ActionSuccessLatency|Opóźnienie sukcesu akcji |Sekundy|Średnia|Opóźnienie akcji przepływu pracy zakończonych powodzeniem.|Brak wymiarów|
|ActionThrottledEvents|Zdarzenia ograniczające akcję|Liczba|Łącznie|Liczba zdarzeń ograniczających przepływ pracy...|Brak wymiarów|
|TriggersStarted|Uruchomiono wyzwalacze |Liczba|Łącznie|Liczba uruchomionych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggersCompleted|Ukończone wyzwalacze |Liczba|Łącznie|Liczba ukończonych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggersSucceeded|Wyzwalacze zakończone powodzeniem |Liczba|Łącznie|Liczba wyzwalaczy przepływu pracy zakończonych powodzeniem.|Brak wymiarów|
|TriggersFailed|Wyzwalacze zakończone niepowodzeniem |Liczba|Łącznie|Liczba wyzwalaczy przepływu pracy zakończonych niepowodzeniem.|Brak wymiarów|
|TriggersSkipped|Pominięte wyzwalacze|Liczba|Łącznie|Liczba pominiętych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggersFired|Wyzwolone wyzwalacze |Liczba|Łącznie|Liczba wyzwolonych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggerLatency|Opóźnienie wyzwalacza |Sekundy|Średnia|Opóźnienie ukończonych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggerFireLatency|Opóźnienie wyzwalania wyzwalacza |Sekundy|Średnia|Opóźnienie wyzwalanych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggerSuccessLatency|Opóźnienie sukcesu wyzwalacza |Sekundy|Średnia|Opóźnienie wyzwalaczy przepływu pracy zakończonych powodzeniem.|Brak wymiarów|
|TriggerThrottledEvents|Wyzwalaj zdarzenia ograniczające|Liczba|Łącznie|Liczba zdarzeń ograniczenia wyzwalacza przepływu pracy.|Brak wymiarów|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Użycie procesora przepływu pracy dla środowisko usługi integracji|Procent|Średnia|Użycie procesora przez przepływ pracy dla środowiska usługi integracji.|Brak wymiarów|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Użycie pamięci przez przepływ pracy dla środowisko usługi integracji|Procent|Średnia|Użycie pamięci przez przepływ pracy dla środowiska usługi integracji.|Brak wymiarów|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Użycie procesora przez łącznik dla środowisko usługi integracji|Procent|Średnia|Użycie procesora przez łącznik dla środowiska usługi integracji.|Brak wymiarów|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Użycie pamięci przez łącznik dla środowisko usługi integracji|Procent|Średnia|Użycie pamięci przez łącznik dla środowiska usługi integracji.|Brak wymiarów|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/obszary robocze

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Ukończone uruchomienia|Ukończone uruchomienia|Liczba|Łącznie|Liczba przebiegów pomyślnie ukończonych dla tego obszaru roboczego|Scenariusz|
|Uruchomione uruchomienia|Uruchomione uruchomienia|Liczba|Łącznie|Liczba rozpoczętych uruchomień dla tego obszaru roboczego|Scenariusz|
|Nieudane uruchomienia|Nieudane uruchomienia|Liczba|Łącznie|Liczba uruchomień dla tego obszaru roboczego nie powiodła się|Scenariusz|

## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/konta

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Użycie|Użycie|Liczba|Liczba|Liczba wywołań interfejsu API|ApiCategory, ApiName, ResultType, ResponseCode|
|Dostępność|Dostępność|Procent|Średnia|Dostępność interfejsów API|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/woluminy

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AverageOtherLatency|Średnie inne opóźnienie|MS/op|Średnia|Średnie inne opóźnienie (bez odczytu lub zapisu) w milisekundach na operację|Brak wymiarów|
|AverageReadLatency|Średnie opóźnienie odczytu|MS/op|Średnia|Średnie opóźnienie odczytu w milisekundach na operację|Brak wymiarów|
|AverageTotalLatency|Średnie opóźnienie całkowite|MS/op|Średnia|Średnie całkowite opóźnienie (w milisekundach) na operację|Brak wymiarów|
|AverageWriteLatency|Średnie opóźnienie zapisu|MS/op|Średnia|Średnie opóźnienie zapisu w milisekundach na operację|Brak wymiarów|
|FilesystemOtherOps|Inne operacje dotyczące systemu plików|kompresowania|Średnia|Liczba innych operacji w systemie plików (które nie są odczytywane lub zapisywane)|Brak wymiarów|
|FilesystemReadOps|Operacje odczytu dla systemu plików|kompresowania|Średnia|Liczba operacji odczytu z systemu plików|Brak wymiarów|
|FilesystemTotalOps|Łączna liczba operacji dla systemu plików|kompresowania|Średnia|Suma wszystkich operacji systemu plików|Brak wymiarów|
|FilesystemWriteOps|Operacje zapisu dla systemu plików|kompresowania|Średnia|Liczba operacji zapisu systemu plików|Brak wymiarów|
|IoBytesPerOtherOps|Bajty we/wy na inne operacje|bajty/op|Średnia|Liczba bajtów in/out na inne operacje (które nie są odczytywane lub zapisu)|Brak wymiarów|
|IoBytesPerReadOps|Bajty we/wy na operacje odczytu|bajty/op|Średnia|Liczba bajtów wejściowych/out na operację odczytu|Brak wymiarów|
|IoBytesPerTotalOps|Bajty we/wy na operację we wszystkich operacjach|bajty/op|Średnia|Suma wszystkich operacji we/out bajtów|Brak wymiarów|
|IoBytesPerWriteOps|Bajty we/wy na operacje zapisu|bajty/op|Średnia|Liczba bajtów we/out na operację zapisu|Brak wymiarów|
|OtherIops|Inne operacje we/wy|operacje na sekundę|Średnia|Inne operacje we/wychodzące na sekundę|Brak wymiarów|
|OtherThroughput|Inna przepływność|MB/s|Średnia|Inna przepływność (nie jest odczytywana ani zapisu) w megabajtach na sekundę|Brak wymiarów|
|ReadIops|Odczyt operacji we/wy|operacje na sekundę|Średnia|Operacje odczytu/out na sekundę|Brak wymiarów|
|ReadThroughput|Przepływność odczytu|MB/s|Średnia|Przepływność odczytu (w megabajtach) na sekundę|Brak wymiarów|
|TotalIops|Całkowita liczba operacji we/wy|operacje na sekundę|Średnia|Suma wszystkich operacji we/wychodzącym na sekundę|Brak wymiarów|
|TotalThroughput|Łączna przepływność|MB/s|Średnia|Suma całej przepływności w megabajtach na sekundę|Brak wymiarów|
|VolumeAllocatedSize|Rozmiar przydzielony woluminu|Szybkość|Średnia|Przydzielony rozmiar woluminu (nie są to rzeczywiste używane bajty)|Brak wymiarów|
|VolumeLogicalSize|Rozmiar logiczny woluminu|Szybkość|Średnia|Rozmiar logiczny woluminu (zużyte bajty)|Brak wymiarów|
|VolumeSnapshotSize|Rozmiar migawki woluminu|Szybkość|Średnia|Rozmiar wszystkich migawek w woluminie|Brak wymiarów|
|WriteIops|Zapisz operacje we/wy|operacje na sekundę|Średnia|Operacje zapisu/wylogowania na sekundę|Brak wymiarów|
|WriteThroughput|Przepływność zapisu|MB/s|Średnia|Przepływność zapisu w megabajtach na sekundę|Brak wymiarów|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft. NetApp/netAppAccounts/capacityPools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Rozmiar przydzielony puli woluminów|Szybkość|Średnia|Przydzielony rozmiar puli (nie są to rzeczywiste używane bajty)|Brak wymiarów|
|VolumePoolAllocatedUsed|Użyte przydzieloną pulę woluminów|Szybkość|Średnia|Przydzielono używany rozmiar puli|Brak wymiarów|
|VolumePoolTotalLogicalSize|Łączny rozmiar logiczny puli woluminów|Szybkość|Średnia|Suma rozmiaru logicznego wszystkich woluminów należących do puli|Brak wymiarów|
|VolumePoolTotalSnapshotSize|Łączny rozmiar migawki puli woluminów|Szybkość|Średnia|Suma wszystkich migawek w puli|Brak wymiarów|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft. Network/networkInterfaces

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BytesSentRate|Bajty wysłane|Liczba|Łącznie|Liczba bajtów wysłanych przez interfejs sieciowy|Brak wymiarów|
|BytesReceivedRate|Bajty odebrane|Liczba|Łącznie|Liczba bajtów odebranych przez interfejs sieciowy|Brak wymiarów|
|PacketsSentRate|Wysłane pakiety|Liczba|Łącznie|Liczba pakietów wysłanych przez interfejs sieciowy|Brak wymiarów|
|PacketsReceivedRate|Odebrane pakiety|Liczba|Łącznie|Liczba pakietów odebranych przez interfejs sieciowy|Brak wymiarów|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|VipAvailability|Dostępność ścieżki danych|Liczba|Średnia|Średnia dostępność ścieżki danych Load Balancer na czas trwania|FrontendIPAddress, FrontendPort|
|DipAvailability|Stan sondy kondycji|Liczba|Średnia|Średni stan sondy kondycji Load Balancer na czas trwania|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Liczba bajtów|Liczba|Łącznie|Łączna liczba bajtów przesłanych w okresie|FrontendIPAddress, FrontendPort, kierunek|
|PacketCount|Liczba pakietów|Liczba|Łącznie|Łączna liczba pakietów wysłanych w czasie|FrontendIPAddress, FrontendPort, kierunek|
|SYNCount|Liczba SYN|Liczba|Łącznie|Łączna liczba pakietów SYN wysłanych w czasie|FrontendIPAddress, FrontendPort, kierunek|
|SnatConnectionCount|Liczba połączeń z translatorem adresów sieciowych|Liczba|Łącznie|Łączna liczba nowych połączeń z przyłączaniem do adresów w czasie|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Przydzielono porty przydziałów (wersja zapoznawcza)|Liczba|Łącznie|Łączna liczba portów przydzieloną porty adresów sieciowych w okresie|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Używane porty (wersja zapoznawcza)|Liczba|Łącznie|Łączna liczba portów przydziałów adresów sieciowych używanych w czasie|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft. Network/dnszones

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QueryVolume|Wolumin zapytania|Liczba|Łącznie|Liczba zapytań obsłużonych dla strefy DNS|Brak wymiarów|
|RecordSetCount|Liczba zestawów rekordów|Liczba|Maksimum|Liczba zestawów rekordów w strefie DNS|Brak wymiarów|
|RecordSetCapacityUtilization|Wykorzystanie pojemności zestawu rekordów|Procent|Maksimum|Procent pojemności zestawu rekordów używany przez strefę DNS|Brak wymiarów|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PacketsInDDoS|Pakiety przychodzące DDoS|CountPerSecond|Maksimum|Pakiety przychodzące DDoS|Brak wymiarów|
|PacketsDroppedDDoS|Odrzucone pakiety przychodzące DDoS|CountPerSecond|Maksimum|Odrzucone pakiety przychodzące DDoS|Brak wymiarów|
|PacketsForwardedDDoS|Przekazane pakiety przychodzące DDoS|CountPerSecond|Maksimum|Przekazane pakiety przychodzące DDoS|Brak wymiarów|
|TCPPacketsInDDoS|Przychodzące pakiety TCP DDoS|CountPerSecond|Maksimum|Przychodzące pakiety TCP DDoS|Brak wymiarów|
|TCPPacketsDroppedDDoS|Liczba porzuconych pakietów TCP przychodzących DDoS|CountPerSecond|Maksimum|Liczba porzuconych pakietów TCP przychodzących DDoS|Brak wymiarów|
|TCPPacketsForwardedDDoS|Przychodzące pakiety TCP przesłane dalej DDoS|CountPerSecond|Maksimum|Przychodzące pakiety TCP przesłane dalej DDoS|Brak wymiarów|
|UDPPacketsInDDoS|Przychodzące pakiety UDP DDoS|CountPerSecond|Maksimum|Przychodzące pakiety UDP DDoS|Brak wymiarów|
|UDPPacketsDroppedDDoS|Liczba porzuconych pakietów przychodzących UDP DDoS|CountPerSecond|Maksimum|Liczba porzuconych pakietów przychodzących UDP DDoS|Brak wymiarów|
|UDPPacketsForwardedDDoS|Przychodzące pakiety UDP DDoS przesłane dalej|CountPerSecond|Maksimum|Przychodzące pakiety UDP DDoS przesłane dalej|Brak wymiarów|
|BytesInDDoS|Bajty przychodzące DDoS|BytesPerSecond|Maksimum|Bajty przychodzące DDoS|Brak wymiarów|
|BytesDroppedDDoS|Bajty przychodzące opuszczone DDoS|BytesPerSecond|Maksimum|Bajty przychodzące opuszczone DDoS|Brak wymiarów|
|BytesForwardedDDoS|Przekazane bajty przychodzące DDoS|BytesPerSecond|Maksimum|Przekazane bajty przychodzące DDoS|Brak wymiarów|
|TCPBytesInDDoS|Przychodzące bajty TCP DDoS|BytesPerSecond|Maksimum|Przychodzące bajty TCP DDoS|Brak wymiarów|
|TCPBytesDroppedDDoS|Liczba porzuconych bajtów przychodzących protokołu TCP DDoS|BytesPerSecond|Maksimum|Liczba porzuconych bajtów przychodzących protokołu TCP DDoS|Brak wymiarów|
|TCPBytesForwardedDDoS|Przekazane DDoS przychodzące bajty TCP|BytesPerSecond|Maksimum|Przekazane DDoS przychodzące bajty TCP|Brak wymiarów|
|UDPBytesInDDoS|Przychodzące bajty UDP DDoS|BytesPerSecond|Maksimum|Przychodzące bajty UDP DDoS|Brak wymiarów|
|UDPBytesDroppedDDoS|Liczba porzuconych bajtów przychodzących UDP DDoS|BytesPerSecond|Maksimum|Liczba porzuconych bajtów przychodzących UDP DDoS|Brak wymiarów|
|UDPBytesForwardedDDoS|Przekazane przychodzące bajty UDP DDoS|BytesPerSecond|Maksimum|Przekazane przychodzące bajty UDP DDoS|Brak wymiarów|
|IfUnderDDoSAttack|W obszarze atak DDoS|Liczba|Maksimum|W obszarze atak DDoS|Brak wymiarów|
|DDoSTriggerTCPPackets|Przychodzące pakiety TCP do wyzwalania łagodzenia DDoS|CountPerSecond|Maksimum|Przychodzące pakiety TCP do wyzwalania łagodzenia DDoS|Brak wymiarów|
|DDoSTriggerUDPPackets|Przychodzące pakiety UDP do wyzwalania łagodzenia DDoS|CountPerSecond|Maksimum|Przychodzące pakiety UDP do wyzwalania łagodzenia DDoS|Brak wymiarów|
|DDoSTriggerSYNPackets|Pakiety przychodzących SYN wyzwalające łagodzenie DDoS|CountPerSecond|Maksimum|Pakiety przychodzących SYN wyzwalające łagodzenie DDoS|Brak wymiarów|
|VipAvailability|Dostępność ścieżki danych|Liczba|Średnia|Średnia dostępność adresów IP na czas trwania|Port|
|ByteCount|Liczba bajtów|Liczba|Łącznie|Łączna liczba bajtów przesłanych w okresie|Port, kierunek|
|PacketCount|Liczba pakietów|Liczba|Łącznie|Łączna liczba pakietów wysłanych w czasie|Port, kierunek|
|SynCount|Liczba SYN|Liczba|Łącznie|Łączna liczba pakietów SYN wysłanych w czasie|Port, kierunek|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ApplicationRuleHit|Liczba trafień reguł aplikacji|Liczba|Łącznie|Liczba trafień reguł aplikacji|Stan, powód, protokół|
|NetworkRuleHit|Liczba trafień reguł sieci|Liczba|Łącznie|Liczba trafień reguł sieci|Stan, powód, protokół|
|Przetwarzanie dataprocessed|Ilość przetworzonych danych|Szybkość|Łącznie|Ilość danych przechodzących przez zaporę|Brak wymiarów|
|FirewallHealthState|Stan kondycji zapory|Procent|Średnia|Wskazuje kondycję zapory|Stan, Przyczyna|
|SNATPortUtilization|Wykorzystanie portów przez przytranslatora adresów sieciowych|Procent|Średnia|Procent portów z przystawek adresów sieciowych, które zostały wykorzystane przez zaporę|Brak wymiarów|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Przepływność|Przepływność|BytesPerSecond|Łącznie|Liczba bajtów na sekundę obsłużonych przez Application Gateway|Brak wymiarów|
|UnhealthyHostCount|Liczba hostów w złej kondycji|Liczba|Średnia|Liczba hostów zaplecza w złej kondycji|BackendSettingsPool|
|HealthyHostCount|Liczba hostów w dobrej kondycji|Liczba|Średnia|Liczba hostów zaplecza w dobrej kondycji|BackendSettingsPool|
|TotalRequests|Łączna liczba żądań|Liczba|Łącznie|Liczba pomyślnych żądań obsłużonych przez Application Gateway|BackendSettingsPool|
|FailedRequests|Żądania zakończone niepowodzeniem|Liczba|Łącznie|Liczba żądań zakończonych niepowodzeniem, które zostały obsłużone przez Application Gateway|BackendSettingsPool|
|ResponseStatus|Stan odpowiedzi|Liczba|Łącznie|Stan odpowiedzi HTTP zwrócony przez Application Gateway|HttpStatusGroup|
|Wartości CurrentConnections|Bieżące połączenia|Liczba|Łącznie|Liczba bieżących połączeń ustanowionych z Application Gateway|Brak wymiarów|
|CapacityUnits|Bieżące jednostki wydajności|Liczba|Średnia|Zużyte jednostki wydajności|Brak wymiarów|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AverageBandwidth|Przepustowość S2S bramy|BytesPerSecond|Średnia|Średnia przepustowość lokacja-lokacja bramy w bajtach na sekundę|Brak wymiarów|
|P2SBandwidth|Przepustowość P2S bramy|BytesPerSecond|Średnia|Średnia przepustowość typu punkt-lokacja bramy w bajtach na sekundę|Brak wymiarów|
|P2SConnectionCount|Liczba połączeń P2S|Liczba|Maksimum|Liczba połączeń punkt-lokacja bramy|Protocol (Protokół)|
|TunnelAverageBandwidth|Przepustowość tunelu|BytesPerSecond|Średnia|Średnia przepustowość tunelu w bajtach na sekundę|ConnectionName, RemoteIP|
|TunnelEgressBytes|Bajty wychodzące tunelu|Szybkość|Łącznie|Wychodzące bajty tunelu|ConnectionName, RemoteIP|
|TunnelIngressBytes|Bajty transferu danych wejściowych tunelu|Szybkość|Łącznie|Przychodzące bajty tunelu|ConnectionName, RemoteIP|
|TunnelEgressPackets|Pakiety wychodzące tuneli|Liczba|Łącznie|Liczba wychodzących pakietów tunelu|ConnectionName, RemoteIP|
|TunnelIngressPackets|Pakiety przychodzące tunelu|Liczba|Łącznie|Liczba przychodzących pakietów tunelu|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Porzucanie pakietów wychodzących przez tunelowanie|Liczba|Łącznie|Liczba porzucenia pakietów wychodzących z niezgodności selektora ruchu tunelu|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Wyrzucanie niezgodności pakietów przez tunelowanie|Liczba|Łącznie|Liczba porzucenia pakietów przychodzących z niezgodności selektora ruchu tunelu|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Średnia|Usługa BITS na sekundę na platformie Azure|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Średnia|Ruch przychodzący z platformy Azure na sekundę|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/expressRouteCircuits/Komunikacja równorzędna

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Średnia|Usługa BITS na sekundę na platformie Azure|Brak wymiarów|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Średnia|Ruch przychodzący z platformy Azure na sekundę|Brak wymiarów|

## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Średnia|Usługa BITS na sekundę na platformie Azure|Brak wymiarów|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Średnia|Ruch przychodzący z platformy Azure na sekundę|Brak wymiarów|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QpsByEndpoint|Zwrócone zapytania według punktu końcowego|Liczba|Łącznie|Liczba zwróconych punktów końcowych Traffic Manager w danym przedziale czasu|Nazwapunktukoncowego|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Stan punktu końcowego według punktu końcowego|Liczba|Maksimum|1 Jeśli sonda punktu końcowego ma stan "włączone", 0 w przeciwnym razie.|Nazwapunktukoncowego|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft. Network/networkWatchers/connectionMonitors

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Sond nie powiodło się|Procent|Średnia|% sond monitorowania łączności nie powiodło się|Brak wymiarów|
|AverageRoundtripMs|Średni czas błądzenia (MS)|)|Średnia|Średni czas błądzenia sieci (MS) dla sond monitorowania łączności przesyłanych między źródłem a miejscem docelowym|Brak wymiarów|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/usługi frontdoor

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|requestCount|Liczba żądań|Liczba|Łącznie|Liczba żądań klientów obsłużonych przez serwer proxy HTTP/S|Wartości httpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Rozmiar żądania|Szybkość|Łącznie|Liczba bajtów wysłanych jako żądania od klientów do serwera proxy HTTP/S|Wartości httpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Rozmiar odpowiedzi|Szybkość|Łącznie|Liczba bajtów wysłanych jako odpowiedzi z serwera proxy HTTP/S do klientów|Wartości httpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Liczba żądań wewnętrznej bazy danych|Liczba|Łącznie|Liczba żądań wysyłanych z serwera proxy HTTP/S do frontonu|Wartości httpStatus, HttpStatusGroup, zaplecze|
|BackendRequestLatency|Opóźnienie żądania wewnętrznej bazy danych|)|Średnia|Czas obliczony od momentu wysłania żądania przez serwer proxy HTTP/S do zaplecza do momentu odebrania przez serwer proxy HTTP/S ostatniego bajtu odpowiedzi z zaplecza|Danych|
|TotalLatency|Łączne opóźnienie|)|Średnia|Czas obliczony od momentu odebrania żądania klienta przez serwer proxy HTTP/S do momentu potwierdzenia przez klienta ostatniego bajtu odpowiedzi z serwera proxy HTTP/S|Wartości httpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Procent kondycji zaplecza|Procent|Średnia|Procent pomyślnych sond kondycji z serwera proxy HTTP/S do frontonu|Zaplecze, ustawień httpsettings elementu|
|WebApplicationFirewallRequestCount|Liczba żądań zapory aplikacji sieci Web|Liczba|Łącznie|Liczba żądań klientów przetworzonych przez zaporę aplikacji sieci Web|PolicyName, RuleName, Akcja|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Rejestracja. wszystkie|Operacje rejestracji|Liczba|Łącznie|Liczba wszystkich operacji rejestracji zakończonych powodzeniem (Tworzenie aktualizacji zapytań i usunięć). |Brak wymiarów|
|Rejestracja. Tworzenie|Operacje tworzenia rejestracji|Liczba|Łącznie|Liczba wszystkich operacji tworzenia rejestracji zakończonych powodzeniem.|Brak wymiarów|
|Rejestracja. Aktualizacja|Operacje aktualizacji rejestracji|Liczba|Łącznie|Liczba wszystkich aktualizacji rejestracji zakończonych powodzeniem.|Brak wymiarów|
|Rejestracja. Get|Operacje odczytu rejestracji|Liczba|Łącznie|Liczba wszystkich zapytań rejestracji zakończonych powodzeniem.|Brak wymiarów|
|Rejestracja. Usuwanie|Operacje usuwania rejestracji|Liczba|Łącznie|Liczba wszystkich operacji usunięcia rejestracji zakończonych powodzeniem.|Brak wymiarów|
|przychodzące|Komunikaty przychodzące|Liczba|Łącznie|Liczba wszystkich pomyślnie wysłanych wywołań interfejsu API. |Brak wymiarów|
|przychodzące. zaplanowane|Przesłane zaplanowane powiadomienia wypychane|Liczba|Łącznie|Zaplanowane powiadomienia wypychane zostały anulowane|Brak wymiarów|
|przychodzące. zaplanowane. Cancel|Zaplanowane powiadomienia wypychane zostały anulowane|Liczba|Łącznie|Zaplanowane powiadomienia wypychane zostały anulowane|Brak wymiarów|
|zaplanowane. oczekujące|Oczekujące zaplanowane powiadomienia|Liczba|Łącznie|Oczekujące zaplanowane powiadomienia|Brak wymiarów|
|Instalacja. wszystkie|Operacje zarządzania instalacją|Liczba|Łącznie|Operacje zarządzania instalacją|Brak wymiarów|
|Instalacja. Get|Pobierz operacje instalacji|Liczba|Łącznie|Pobierz operacje instalacji|Brak wymiarów|
|Instalacja. upsert|Tworzenie lub aktualizowanie operacji instalacji|Liczba|Łącznie|Tworzenie lub aktualizowanie operacji instalacji|Brak wymiarów|
|Instalacja. poprawka|Operacje instalacji poprawek|Liczba|Łącznie|Operacje instalacji poprawek|Brak wymiarów|
|Instalacja. Delete|Usuwanie operacji instalacji|Liczba|Łącznie|Usuwanie operacji instalacji|Brak wymiarów|
|wychodzący. allpns. Success|Powiadomienia zakończone powodzeniem|Liczba|Łącznie|Liczba wszystkich udanych powiadomień.|Brak wymiarów|
|wychodzący. allpns. invalidpayload|Błędy ładunku|Liczba|Łącznie|Liczba wypchnięciów zakończonych niepowodzeniem, ponieważ PNS zwrócił zły błąd ładunku.|Brak wymiarów|
|wychodzący. allpns. pnserror|Błędy zewnętrznego systemu powiadomień|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ wystąpił problem z komunikacją z PNS (wyklucza problemy z uwierzytelnianiem).|Brak wymiarów|
|wychodzący. allpns. channelerror|Błędy kanałów|Liczba|Łącznie|Liczba wypchnięciów zakończonych niepowodzeniem, ponieważ kanał był nieprawidłowy, nie jest skojarzony z poprawną aplikacją lub wygasłą.|Brak wymiarów|
|wychodzący. allpns. badorexpiredchannel|Błędy nieprawidłowych lub wygasłych kanałów|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ kanał/token/Identyfikator rejestracji w rejestracji został wygasły lub nieprawidłowy.|Brak wymiarów|
|wychodzący. WNS. Success|WNS pomyślne powiadomienia|Liczba|Łącznie|Liczba wszystkich udanych powiadomień.|Brak wymiarów|
|wychodzący. WNS. invalidcredentials|WNS błędy autoryzacji (Nieprawidłowe poświadczenia)|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń lub poświadczenia są zablokowane. (Usługa Windows Live nie rozpoznaje poświadczeń).|Brak wymiarów|
|wychodzący. WNS. badchannel|Błąd nieprawidłowego kanału WNS|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ identyfikator channeluri w rejestracji nie zostały rozpoznane (WNS status: 404).|Brak wymiarów|
|wychodzący. WNS. expiredchannel|Błąd WNS kanału wygasłego|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem z powodu wygaśnięcia identyfikator channeluri (stan WNS: 410).|Brak wymiarów|
|wychodzące. WNS. dławienia|WNS powiadomienia z ograniczeniami|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ WNS ogranicza tę aplikację (stan WNS: 406 nie akceptowalny).|Brak wymiarów|
|wychodzący. WNS. tokenproviderunreachable|Błędy autoryzacji WNS (nieosiągalne)|Liczba|Łącznie|Usługa Windows Live jest nieosiągalna.|Brak wymiarów|
|wychodzący. WNS. invalidtoken|Błędy autoryzacji WNS (nieprawidłowy token)|Liczba|Łącznie|Token dostarczony do WNS jest nieprawidłowy (stan WNS: 401 Brak autoryzacji).|Brak wymiarów|
|wychodzący. WNS. wrongtoken|WNS błędy autoryzacji (nieprawidłowy token)|Liczba|Łącznie|Token dostarczony do WNS jest prawidłowy, ale dla innej aplikacji (stan WNS: 403 zabroniony). Może się tak zdarzyć, jeśli identyfikator channeluri w rejestracji jest skojarzony z inną aplikacją. Sprawdź, czy aplikacja kliencka jest skojarzona z tą samą aplikacją, której poświadczenia znajdują się w centrum powiadomień.|Brak wymiarów|
|wychodzący. WNS. invalidnotificationformat|Nieprawidłowy format powiadomienia WNS|Liczba|Łącznie|Format powiadomienia jest nieprawidłowy (stan WNS: 400). Należy pamiętać, że WNS nie odrzuca wszystkich nieprawidłowych ładunków.|Brak wymiarów|
|wychodzący. WNS. invalidnotificationsize|Błąd nieprawidłowego rozmiaru powiadomienia WNS|Liczba|Łącznie|Ładunek powiadomienia jest zbyt duży (stan WNS: 413).|Brak wymiarów|
|wychodzący. WNS. channelthrottled|Ograniczenie kanału WNS|Liczba|Łącznie|Powiadomienie zostało porzucone, ponieważ identyfikator channeluri w rejestracji jest ograniczany (nagłówek odpowiedzi WNS: X-WNS-NotificationStatus: channelThrottled).|Brak wymiarów|
|wychodzący. WNS. channeldisconnected|Kanał WNS został odłączony|Liczba|Łącznie|Powiadomienie zostało porzucone, ponieważ identyfikator channeluri w rejestracji jest ograniczany (WNS w nagłówku odpowiedzi: X-WNS-DeviceConnectionStatus: disconnected).|Brak wymiarów|
|wychodzące. WNS. upuszczone|WNS usunięte powiadomienia|Liczba|Łącznie|Powiadomienie zostało porzucone, ponieważ identyfikator channeluri w rejestracji jest ograniczany (X-WNS-NotificationStatus: porzucone, ale nie X-WNS-DeviceConnectionStatus: disconnected).|Brak wymiarów|
|wychodzący. WNS. pnserror|Błędy WNS|Liczba|Łącznie|Powiadomienie nie zostało dostarczone z powodu błędów komunikacji z WNS.|Brak wymiarów|
|wychodzący. WNS. authenticationerror|Błędy uwierzytelniania WNS|Liczba|Łącznie|Powiadomienie nie zostało dostarczone z powodu błędów komunikacji z nieprawidłowymi poświadczeniami usługi Windows Live lub nieprawidłowym tokenem.|Brak wymiarów|
|wychodzące. APN. sukces|Pomyślne powiadomienia usługi APNS|Liczba|Łącznie|Liczba wszystkich udanych powiadomień.|Brak wymiarów|
|wychodzące. APNs. invalidcredentials|Błędy autoryzacji usługi APNS|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń lub poświadczenia są zablokowane.|Brak wymiarów|
|wychodzące. APNs. badchannel|Błąd nieprawidłowego kanału usługi APNS|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ token jest nieprawidłowy (kod stanu protokołu binarnego usługi APNS: 8. Kod stanu protokołu HTTP APN: 400 z "BadDeviceToken").|Brak wymiarów|
|wychodzące. APNs. expiredchannel|Błąd wygasłego kanału usługi APNS|Liczba|Łącznie|Liczba tokenów unieważnionych przez kanał opinii usługi APNS.|Brak wymiarów|
|wychodzące. APNs. invalidnotificationsize|Błąd nieprawidłowego rozmiaru powiadomienia usługi APNS|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ ładunek jest zbyt duży (kod stanu protokołu binarnego usługi APNS: 7).|Brak wymiarów|
|wychodzące. APNs. pnserror|Błędy usługi APNS|Liczba|Łącznie|Liczba wypchnięciów zakończonych niepowodzeniem z powodu błędów komunikacji z usługą APNS.|Brak wymiarów|
|wychodzący. GCM. Success|GCM pomyślne powiadomienia|Liczba|Łącznie|Liczba wszystkich udanych powiadomień.|Brak wymiarów|
|wychodzący. GCM. invalidcredentials|GCM błędy autoryzacji (Nieprawidłowe poświadczenia)|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń lub poświadczenia są zablokowane.|Brak wymiarów|
|wychodzący. GCM. badchannel|Błąd nieprawidłowego kanału GCM|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ identyfikator rejestracji w rejestracji nie został rozpoznany (wynik GCM: nieprawidłowa rejestracja).|Brak wymiarów|
|wychodzący. GCM. expiredchannel|Błąd GCM kanału wygasłego|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem z powodu wygaśnięcia Identyfikator rejestracji w rejestracji (wynik GCM: NotRegistered).|Brak wymiarów|
|wychodzące. GCM. dławienia|GCM powiadomienia z ograniczeniami|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ GCM ograniczenie tej aplikacji (kod stanu GCM: 501-599 lub wynik: niedostępne).|Brak wymiarów|
|wychodzący. GCM. invalidnotificationformat|Nieprawidłowy format powiadomienia GCM|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ ładunek nie został poprawnie sformatowany (wynik GCM: InvalidDataKey lub InvalidTtl).|Brak wymiarów|
|wychodzący. GCM. invalidnotificationsize|Błąd nieprawidłowego rozmiaru powiadomienia GCM|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ ładunek jest zbyt duży (wynik GCM: MessageTooBig).|Brak wymiarów|
|wychodzący. GCM. wrongchannel|Błąd nieprawidłowego kanału GCM|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ identyfikator rejestracji w rejestracji nie jest skojarzony z bieżącą aplikacją (GCM Result: InvalidPackageName).|Brak wymiarów|
|wychodzący. GCM. pnserror|Błędy GCM|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem z powodu błędów komunikacji z GCM.|Brak wymiarów|
|wychodzący. GCM. authenticationerror|Błędy uwierzytelniania GCM|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń, a SenderId nie jest poprawnie skonfigurowana w aplikacji (wynik GCM: MismatchedSenderId).|Brak wymiarów|
|wychodzący. usługi MPNS. Success|USŁUGI MPNS pomyślne powiadomienia|Liczba|Łącznie|Liczba wszystkich udanych powiadomień.|Brak wymiarów|
|wychodzący. usługi MPNS. invalidcredentials|USŁUGI MPNS nieprawidłowe poświadczenia|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń lub poświadczenia są zablokowane.|Brak wymiarów|
|wychodzący. usługi MPNS. badchannel|Błąd nieprawidłowego kanału usługi MPNS|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ identyfikator channeluri w rejestracji nie zostały rozpoznane (usługi MPNS status: 404).|Brak wymiarów|
|wychodzące. usługi MPNS. dławienia|USŁUGI MPNS powiadomienia z ograniczeniami|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ usługi MPNS ogranicza tę aplikację (WNS usługi MPNS: 406 nieakceptowalny).|Brak wymiarów|
|wychodzący. usługi MPNS. invalidnotificationformat|Nieprawidłowy format powiadomienia usługi MPNS|Liczba|Łącznie|Liczba wypchnięciów zakończonych niepowodzeniem, ponieważ ładunek powiadomienia był zbyt duży.|Brak wymiarów|
|wychodzący. usługi MPNS. channeldisconnected|Kanał usługi MPNS został odłączony|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ identyfikator channeluri w rejestracji został rozłączony (stan usługi MPNS: 412 nie został znaleziony).|Brak wymiarów|
|wychodzące. usługi MPNS. upuszczone|USŁUGI MPNS usunięte powiadomienia|Liczba|Łącznie|Liczba wypchnięciów, które zostały porzucone przez usługi MPNS (nagłówek odpowiedzi usługi MPNS: X-NotificationStatus: QueueFull lub pomijane).|Brak wymiarów|
|wychodzący. usługi MPNS. pnserror|Błędy usługi MPNS|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem z powodu błędów komunikacji z usługi MPNS.|Brak wymiarów|
|wychodzący. usługi MPNS. authenticationerror|Błędy uwierzytelniania usługi MPNS|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń lub poświadczenia są zablokowane.|Brak wymiarów|
|notificationhub. pushs|Wszystkie powiadomienia wychodzące|Liczba|Łącznie|Wszystkie powiadomienia wychodzące centrum powiadomień|Brak wymiarów|
|przychodzące. ALL. Requests|Wszystkie żądania przychodzące|Liczba|Łącznie|Łączna liczba żądań przychodzących dla centrum powiadomień|Brak wymiarów|
|przychodzące. ALL. failedrequests|Wszystkie przychodzące żądania zakończone niepowodzeniem|Liczba|Łącznie|Łączna liczba nieudanych żądań przychodzących dla centrum powiadomień|Brak wymiarów|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/obszary robocze

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Average_% bezpłatne węzłów i|% Wolnego węzłów i|Liczba|Średnia|Average_% bezpłatne węzłów i|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Wolne miejsce w Average_%|Wolne miejsce (%)|Liczba|Średnia|Wolne miejsce w Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ użyto węzłów i|% Użytych węzłów i|Liczba|Średnia|Average_ użyto węzłów i|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Używane miejsce Average_%|Zajęte miejsce (%)|Liczba|Średnia|Używane miejsce Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bajty odczytu Average_Disk/s|Bajty odczytu dysku/s|Liczba|Średnia|Bajty odczytu Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odczyty Average_Disk/s|Odczyty dysku/s|Liczba|Średnia|Odczyty Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Transfery Average_Disk/s|Transfery dysku/s|Liczba|Średnia|Transfery Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bajty zapisu Average_Disk/s|Bajty zapisu dysku/s|Liczba|Średnia|Bajty zapisu Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Zapisy Average_Disk/s|Zapisy dysku/s|Liczba|Średnia|Zapisy Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabajtów|Wolne megabajty|Liczba|Średnia|Average_Free megabajtów|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bajty dysku Average_Logical/s|Bajty dysku logicznego/s|Liczba|Średnia|Bajty dysku Average_Logical/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Dostępna pamięć Average_%|Dostępna pamięć (%)|Liczba|Średnia|Dostępna pamięć Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Dostępny obszar wymiany Average_%|Dostępny obszar wymiany (%)|Liczba|Średnia|Dostępny obszar wymiany Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Użyta pamięć Average_%|Używana pamięć (%)|Liczba|Średnia|Użyta pamięć Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Używany obszar wymiany Average_%|Używany obszar wymiany (%)|Liczba|Średnia|Używany obszar wymiany Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pamięć Average_Available MB|Dostępna pamięć (MB)|Liczba|Średnia|Pamięć Average_Available MB|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Zamiana Average_Available MB|Dostępny obszar wymiany (MB)|Liczba|Średnia|Zamiana Average_Available MB|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odczyty Average_Page/s|Odczyty stron/s|Liczba|Średnia|Odczyty Average_Page/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Zapisy Average_Page/s|Zapisy stron/s|Liczba|Średnia|Zapisy Average_Page/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/s|Stron/s|Liczba|Średnia|Average_Pages/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Przestrzeń wymiany Average_Used MB|Używany obszar wymiany (MB)|Liczba|Średnia|Przestrzeń wymiany Average_Used MB|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pamięć Average_Used pamięci (MB)|Używana pamięć (MB)|Liczba|Średnia|Pamięć Average_Used pamięci (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Przesyłane Average_Total bajty|Całkowita liczba przesłanych bajtów|Liczba|Średnia|Przesyłane Average_Total bajty|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bajty odebrane Average_Total|Całkowita liczba odebranych bajtów|Liczba|Średnia|Bajty odebrane Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total bajty|Łączna liczba bajtów|Liczba|Średnia|Average_Total bajty|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total przesyłane pakiety|Całkowita liczba przesłanych pakietów|Liczba|Średnia|Average_Total przesyłane pakiety|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odebrane pakiety Average_Total|Całkowita liczba odebranych pakietów|Liczba|Średnia|Odebrane pakiety Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total błędy odbierania|Całkowita liczba błędów odbierania|Liczba|Średnia|Average_Total błędy odbierania|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Błędy wysyłania Average_Total|Całkowita liczba błędów transmisji|Liczba|Średnia|Błędy wysyłania Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total kolizje|Łączna liczba kolizji|Liczba|Średnia|Average_Total kolizje|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Dysk w s/odczyt|Średni czas dysku w s/odczyt|Liczba|Średnia|Average_Avg. Dysk w s/odczyt|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Dysk w s/transfer|Średni czas dysku w s/transfer|Liczba|Średnia|Average_Avg. Dysk w s/transfer|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Dysk w s/zapis|Średni czas dysku w s/zapis|Liczba|Średnia|Average_Avg. Dysk w s/zapis|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bajty dysku Average_Physical/s|Bajty dysku fizycznego/s|Liczba|Średnia|Bajty dysku Average_Physical/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct uprzywilejowany czas|Czas uprzywilejowany PCT|Liczba|Średnia|Average_Pct uprzywilejowany czas|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas użytkownika Average_Pct|Czas użytkownika PCT|Liczba|Średnia|Czas użytkownika Average_Pct|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pamięć Average_Used w kilobajtach|Używana pamięć (w kilobajtach)|Liczba|Średnia|Pamięć Average_Used w kilobajtach|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual pamięci współdzielonej|Wirtualna pamięć udostępniona|Liczba|Średnia|Average_Virtual pamięci współdzielonej|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas DPC Average_%|Czas DPC (%)|Liczba|Średnia|Czas DPC Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas bezczynności Average_%|Czas bezczynności (%)|Liczba|Średnia|Czas bezczynności Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ czas przerwań (%)|Czas przerwań (%)|Liczba|Średnia|Average_ czas przerwań (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas oczekiwania operacji we/wy Average_%|Czas oczekiwania operacji we/wy (%)|Liczba|Średnia|Czas oczekiwania operacji we/wy Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% czasu całkiem|% Całkiem czasu|Liczba|Średnia|Average_% czasu całkiem|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% czasu uprzywilejowanego|Czas uprzywilejowany (%)|Liczba|Średnia|Average_% czasu uprzywilejowanego|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ czas procesora (%)|Czas procesora (%)|Liczba|Średnia|Average_ czas procesora (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas użytkownika Average_%|Czas użytkownika (%)|Liczba|Średnia|Czas użytkownika Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free pamięci fizycznej|Wolna pamięć fizyczna|Liczba|Średnia|Average_Free pamięci fizycznej|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free miejsce w plikach stronicowania|Wolne miejsce w plikach stronicowania|Liczba|Średnia|Average_Free miejsce w plikach stronicowania|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free pamięć wirtualną|Wolna pamięć wirtualna|Liczba|Średnia|Average_Free pamięć wirtualną|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Procesy|Liczba|Średnia|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size przechowywane w plikach stronicowania|Rozmiar zapisany w plikach stronicowania|Liczba|Średnia|Average_Size przechowywane w plikach stronicowania|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Czas|Liczba|Średnia|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Użytkownicy|Liczba|Średnia|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Dysk w s/odczyt|Średni czas dysku w s/odczyt|Liczba|Średnia|Average_Avg. Dysk w s/odczyt|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Dysk w s/zapis|Średni czas dysku w s/zapis|Liczba|Średnia|Average_Avg. Dysk w s/zapis|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current długość kolejki dysku|Bieżąca długość kolejki dysku|Liczba|Średnia|Average_Current długość kolejki dysku|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odczyty Average_Disk/s|Odczyty dysku/s|Liczba|Średnia|Odczyty Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Transfery Average_Disk/s|Transfery dysku/s|Liczba|Średnia|Transfery Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Zapisy Average_Disk/s|Zapisy dysku/s|Liczba|Średnia|Zapisy Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabajtów|Wolne megabajty|Liczba|Średnia|Average_Free megabajtów|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Wolne miejsce w Average_%|Wolne miejsce (%)|Liczba|Średnia|Wolne miejsce w Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB|Dostępna pamięć (MB)|Liczba|Średnia|Average_Available MB|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% zadeklarowanych bajtów w użyciu|% Zadeklarowanych bajtów w użyciu|Liczba|Średnia|Average_% zadeklarowanych bajtów w użyciu|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odebrane Average_Bytes/s|Bajty odebrane/s|Liczba|Średnia|Odebrane Average_Bytes/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Wysłane Average_Bytes/s|Bajty wysłane/s|Liczba|Średnia|Wysłane Average_Bytes/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes całkowita/s|Całkowita liczba bajtów/s|Liczba|Średnia|Average_Bytes całkowita/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ czas procesora (%)|Czas procesora (%)|Liczba|Średnia|Average_ czas procesora (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Długość kolejki Average_Processor|Długość kolejki procesora|Liczba|Średnia|Długość kolejki Average_Processor|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Sygnały|Sygnały|Liczba|Łącznie|Sygnały|Komputer, OSType, wersja, SourceComputerId|
|Aktualizacja|Aktualizacja|Liczba|Średnia|Aktualizacja|Komputer, produkt, klasyfikacja, UpdateState, opcjonalne, zatwierdzone|
|Wydarzenie|Wydarzenie|Liczba|Średnia|Wydarzenie|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/pojemności

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QueryDuration|Czas trwania zapytania|)|Średnia|Czas trwania zapytania języka DAX w ostatnim interwale|Brak wymiarów|
|QueryPoolJobQueueLength|Wątki: Długość kolejki zadań puli zapytań|Liczba|Średnia|Liczba zadań w kolejce puli wątków zapytań.|Brak wymiarów|
|qpu_high_utilization_metric|QPU wysokie wykorzystanie|Liczba|Łącznie|QPU wysokie użycie w ciągu ostatnich minut, 1 do dużego użycia QPU, w przeciwnym razie 0|Brak wymiarów|
|memory_metric|Pamięć|Szybkość|Średnia|Rozmiar. Zakres 0-3 GB dla a1, 0-5 GB dla a2, 0-10 GB dla a3, 0-25 GB dla A4, 0-50 GB dla A5 i 0-100 GB dla A6|Brak wymiarów|
|memory_thrashing_metric|Migotanie pamięci|Procent|Średnia|Średnia pamięć migotanie.|Brak wymiarów|

## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/przestrzenie nazw

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ListenerConnections — sukces|ListenerConnections — sukces|Liczba|Łącznie|Pomyślne ListenerConnections dla elementu Microsoft. Relay.|EntityName|
|ListenerConnections — błąd clienterror|ListenerConnections — błąd clienterror|Liczba|Łącznie|Błąd clienterror on ListenerConnections dla elementu Microsoft. Relay.|EntityName|
|ListenerConnections — błąd servererror|ListenerConnections — błąd servererror|Liczba|Łącznie|Błąd servererror on ListenerConnections dla elementu Microsoft. Relay.|EntityName|
|SenderConnections — sukces|SenderConnections — sukces|Liczba|Łącznie|Pomyślne SenderConnections dla elementu Microsoft. Relay.|EntityName|
|SenderConnections — błąd clienterror|SenderConnections — błąd clienterror|Liczba|Łącznie|Błąd clienterror on SenderConnections dla elementu Microsoft. Relay.|EntityName|
|SenderConnections — błąd servererror|SenderConnections — błąd servererror|Liczba|Łącznie|Błąd servererror on SenderConnections dla elementu Microsoft. Relay.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Liczba|Łącznie|Łącznie ListenerConnections dla elementu Microsoft. Relay.|EntityName|
|SenderConnections — TotalRequests|SenderConnections — TotalRequests|Liczba|Łącznie|Łączna liczba żądań SenderConnections dla elementu Microsoft. Relay.|EntityName|
|Połączeń ActiveConnections|Połączeń ActiveConnections|Liczba|Łącznie|Łącznie połączeń ActiveConnections dla elementu Microsoft. Relay.|EntityName|
|ActiveListeners|ActiveListeners|Liczba|Łącznie|Łącznie ActiveListeners dla elementu Microsoft. Relay.|EntityName|
|BytesTransferred|BytesTransferred|Liczba|Łącznie|Łącznie BytesTransferred dla elementu Microsoft. Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Liczba|Łącznie|Łącznie ListenerDisconnects dla elementu Microsoft. Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Liczba|Łącznie|Łącznie SenderDisconnects dla elementu Microsoft. Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SearchLatency|Opóźnienie wyszukiwania|Sekundy|Średnia|Średnie opóźnienie wyszukiwania dla usługi wyszukiwania|Brak wymiarów|
|SearchQueriesPerSecond|Zapytania wyszukiwania na sekundę|CountPerSecond|Średnia|Zapytania wyszukiwania na sekundę dla usługi wyszukiwania|Brak wymiarów|
|ThrottledSearchQueriesPercentage|Procent zapytań wyszukiwania z ograniczeniami|Procent|Średnia|Procent zapytań wyszukiwania, które zostały ograniczone dla usługi wyszukiwania|Brak wymiarów|

## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/przestrzenie nazw

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Żądania successfulrequests|Pomyślne żądania (wersja zapoznawcza)|Liczba|Łącznie|Łączna liczba pomyślnych żądań dla przestrzeni nazw (wersja zapoznawcza)|EntityName|
|Błędy servererrors|Błędy serwera. (Wersja zapoznawcza)|Liczba|Łącznie|Błędy serwera dla elementu Microsoft. ServiceBus. (Wersja zapoznawcza)|EntityName|
|Błędy usererrors|Błędy użytkownika. (Wersja zapoznawcza)|Liczba|Łącznie|Błędy użytkowników dla elementu Microsoft. ServiceBus. (Wersja zapoznawcza)|EntityName|
|ThrottledRequests|Żądania z ograniczeniami. (Wersja zapoznawcza)|Liczba|Łącznie|Żądania ograniczone dla elementu Microsoft. ServiceBus. (Wersja zapoznawcza)|EntityName|
|Żądania incomingrequests|Żądania przychodzące (wersja zapoznawcza)|Liczba|Łącznie|Żądania przychodzące dla Microsoft. ServiceBus. (Wersja zapoznawcza)|EntityName|
|Komunikaty incomingmessages|Wiadomości przychodzące (wersja zapoznawcza)|Liczba|Łącznie|Komunikaty przychodzące dla elementu Microsoft. ServiceBus. (Wersja zapoznawcza)|EntityName|
|Komunikaty outgoingmessages|Wiadomości wychodzące (wersja zapoznawcza)|Liczba|Łącznie|Komunikaty wychodzące dla elementu Microsoft. ServiceBus. (Wersja zapoznawcza)|EntityName|
|Połączeń ActiveConnections|Połączeń ActiveConnections (wersja zapoznawcza)|Liczba|Łącznie|Całkowita liczba aktywnych połączeń dla elementu Microsoft. ServiceBus. (Wersja zapoznawcza)|Brak wymiarów|
|Rozmiar|Rozmiar (wersja zapoznawcza)|Szybkość|Średnia|Rozmiar kolejki/tematu w bajtach. (Wersja zapoznawcza)|EntityName|
|Komunikaty|Liczba komunikatów w kolejce/temacie. (Wersja zapoznawcza)|Liczba|Średnia|Liczba komunikatów w kolejce/temacie. (Wersja zapoznawcza)|EntityName|
|ActiveMessages|Liczba aktywnych komunikatów w kolejce/temacie. (Wersja zapoznawcza)|Liczba|Średnia|Liczba aktywnych komunikatów w kolejce/temacie. (Wersja zapoznawcza)|EntityName|
|DeadletteredMessages|Liczba utraconych wiadomości w kolejce/temacie. (Wersja zapoznawcza)|Liczba|Średnia|Liczba utraconych wiadomości w kolejce/temacie. (Wersja zapoznawcza)|EntityName|
|ScheduledMessages|Liczba zaplanowanych komunikatów w kolejce/temacie. (Wersja zapoznawcza)|Liczba|Średnia|Liczba zaplanowanych komunikatów w kolejce/temacie. (Wersja zapoznawcza)|EntityName|
|CPUXNS|Użycie procesora CPU na przestrzeń nazw|Procent|Maksimum|Metryka użycia procesora przestrzeni nazw w warstwie Premium usługi Service Bus|Brak wymiarów|
|WSXNS|Użycie rozmiaru pamięci na przestrzeń nazw|Procent|Maksimum|Metryka użycia pamięci przestrzeni nazw Premium usługi Service Bus|Brak wymiarów|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft. ServiceFabricMesh/aplikacje

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Liczba|Średnia|Procesor CPU przydzielony do tego kontenera w millicores|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|AllocatedMemory|AllocatedMemory|Szybkość|Średnia|Pamięć przypisana do tego kontenera w MB|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|ActualCpu|ActualCpu|Liczba|Średnia|Rzeczywiste użycie procesora CPU w millicores|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|ActualMemory|ActualMemory|Szybkość|Średnia|Rzeczywiste użycie pamięci w MB|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|CpuUtilization|CpuUtilization|Procent|Średnia|Użycie procesora CPU dla tego kontenera jako wartości procentowej AllocatedCpu|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|MemoryUtilization|MemoryUtilization|Procent|Średnia|Użycie procesora CPU dla tego kontenera jako wartości procentowej AllocatedCpu|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|ApplicationStatus|ApplicationStatus|Liczba|Średnia|Stan aplikacji siatki Service Fabric|ApplicationName, status|
|Stan servicestatus|Stan servicestatus|Liczba|Średnia|Stan kondycji usługi w aplikacji Service Fabric siatki|ApplicationName, status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Liczba|Średnia|Stan kondycji repliki usługi w aplikacji Service Fabric siatki|ApplicationName, status, ServiceName, servicereplicaname|
|ContainerStatus|ContainerStatus|Liczba|Średnia|Stan kontenera w aplikacji Service Fabric siatki|ApplicationName, ServiceName, CodePackageName, servicereplicaname, status|
|RestartCount|RestartCount|Liczba|Średnia|Liczba ponownych uruchomień kontenera w aplikacji Service Fabric siatki|ApplicationName, status, ServiceName, servicereplicaname, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/sygnalizujący

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ConnectionCount|Liczba połączeń|Liczba|Maksimum|Ilość połączenia użytkownika.|Punkt końcowy|
|MessageCount|Liczba komunikatów|Liczba|Łącznie|Całkowita ilość komunikatów.|Brak wymiarów|
|InboundTraffic|Ruch przychodzący|Szybkość|Łącznie|Ruch przychodzący usługi|Brak wymiarów|
|OutboundTraffic|Ruch wychodzący|Szybkość|Łącznie|Ruch wychodzący usługi|Brak wymiarów|
|Błędy usererrors|Błędy użytkownika|Procent|Maksimum|Procent błędów użytkownika|Brak wymiarów|
|SystemErrors|Błędy systemu|Procent|Maksimum|Procent błędów systemu|Brak wymiarów|

## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/serwery/bazy danych

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|allocated_data_storage|Przydzielono miejsce na danych|Szybkość|Średnia|Przydzielono miejsce na danych. Nie dotyczy hurtowni danych.|Brak wymiarów|
|app_cpu_billed|Rozliczane użycie procesora przez aplikację|Liczba|Łącznie|Rozliczane użycie procesora przez aplikację. Dotyczy bezserwerowych baz danych.|Brak wymiarów|
|app_cpu_percent|Procent użycia procesora CPU aplikacji|Procent|Średnia|Procent użycia procesora CPU aplikacji. Dotyczy bezserwerowych baz danych.|Brak wymiarów|
|app_memory_percent|Procent użycia pamięci aplikacji|Procent|Średnia|Procent wykorzystania pamięci aplikacji. Dotyczy bezserwerowych baz danych.|Brak wymiarów|
|blocked_by_firewall|Zablokowane przez zaporę|Liczba|Łącznie|Zablokowane przez zaporę|Brak wymiarów|
|cache_hit_percent|Procent trafień w pamięci podręcznej|Procent|Maksimum|Procent trafień w pamięci podręcznej. Dotyczy tylko magazynów danych.|Brak wymiarów|
|cache_used_percent|Procent użycia pamięci podręcznej|Procent|Maksimum|Procent użycia pamięci podręcznej. Dotyczy tylko magazynów danych.|Brak wymiarów|
|connection_failed|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|Brak wymiarów|
|connection_successful|Udane połączenia|Liczba|Łącznie|Udane połączenia|Brak wymiarów|
|cpu_percent|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Brak wymiarów|
|cpu_limit|Limit CPU|Liczba|Średnia|Limit czasu procesora CPU. Dotyczy baz danych opartych na rdzeń wirtualny.|Brak wymiarów|
|cpu_used|Użycie procesora CPU|Liczba|Średnia|Użycie procesora CPU. Dotyczy baz danych opartych na rdzeń wirtualny.|Brak wymiarów|
|stanu|Zakleszczenia|Liczba|Łącznie|Zakleszczenia. Nie dotyczy hurtowni danych.|Brak wymiarów|
|diff_backup_size_bytes|Różnicowy rozmiar magazynu kopii zapasowych|Szybkość|Maksimum|Skumulowany, zróżnicowany rozmiar magazynu kopii zapasowych. Dotyczy Ogólnego przeznaczenia i Krytyczne dla działania firmy baz danych. Obecnie nie dotyczy zarządzania bazami danych wystąpienia.|Brak wymiarów|
|dtu_limit|Limit jednostek DTU|Liczba|Średnia|Limit jednostek DTU. Dotyczy baz danych opartych na jednostkach DTU.|Brak wymiarów|
|dtu_consumption_percent|Procent użycia jednostek DTU|Procent|Średnia|Procent jednostek DTU. Dotyczy baz danych opartych na jednostkach DTU.|Brak wymiarów|
|dtu_used|Używane jednostki DTU|Liczba|Średnia|Użyto jednostek DTU. Dotyczy baz danych opartych na jednostkach DTU.|Brak wymiarów|
|dw_cpu_percent|Wartość procentowa procesora CPU na poziomie węzła DW|Procent|Średnia|Wartość procentowa procesora CPU na poziomie węzła DW|DwLogicalNodeId|
|dw_physical_data_read_percent|Procent operacji we/wy danych na poziomie węzła DW|Procent|Średnia|Procent operacji we/wy danych na poziomie węzła DW|DwLogicalNodeId|
|dwu_consumption_percent|JEDNOSTEK dwu procent|Procent|Maksimum|Procent jednostek dwu. Dotyczy tylko magazynów danych.|Brak wymiarów|
|dwu_limit|Limit jednostek dwu|Liczba|Maksimum|Limit jednostek dwu. Dotyczy tylko magazynów danych.|Brak wymiarów|
|dwu_used|JEDNOSTEK dwu używane|Liczba|Maksimum|JEDNOSTEK dwu. Dotyczy tylko magazynów danych.|Brak wymiarów|
|full_backup_size_bytes|Rozmiar magazynu pełnej kopii zapasowej|Szybkość|Maksimum|Łączny rozmiar magazynu pełnej kopii zapasowej. Dotyczy Ogólnego przeznaczenia i Krytyczne dla działania firmy baz danych. Obecnie nie dotyczy zarządzania bazami danych wystąpienia.|Brak wymiarów|
|local_tempdb_usage_percent|Procent lokalnej bazy danych tempdb|Procent|Średnia|Procent lokalnej bazy danych tempdb. Dotyczy tylko magazynów danych.|Brak wymiarów|
|log_backup_size_bytes|Rozmiar magazynu kopii zapasowej dziennika|Szybkość|Maksimum|Rozmiar magazynu kopii zapasowej dziennika zbiorczego. Dotyczy Ogólnego przeznaczenia i Krytyczne dla działania firmy baz danych. Obecnie nie dotyczy zarządzania bazami danych wystąpienia.|Brak wymiarów|
|log_write_percent|Procent operacji we/wy dziennika|Procent|Średnia|Procent operacji we/wy dziennika. Nie dotyczy hurtowni danych.|Brak wymiarów|
|physical_data_read_percent|Procent użycia operacji we/wy na danych|Procent|Średnia|Procent użycia operacji we/wy na danych|Brak wymiarów|
|sessions_percent|Procent sesji|Procent|Średnia|Procent sesji. Nie dotyczy hurtowni danych.|Brak wymiarów|
|sqlserver_process_core_percent|Procent podstawowych procesów SQL Server|Procent|Maksimum|Ta Metryka jest symbolem zastępczym i nie jest w tej chwili wypełniana.|Brak wymiarów|
|sqlserver_process_memory_percent|Procent pamięci procesu SQL Server|Procent|Maksimum|Ta Metryka jest symbolem zastępczym i nie jest w tej chwili wypełniana.|Brak wymiarów|
|magazyn|Używane miejsce na dane|Szybkość|Maksimum|Łączny rozmiar bazy danych. Nie dotyczy hurtowni danych.|Brak wymiarów|
|storage_percent|Procent użytego miejsca na danych|Procent|Maksimum|Procent rozmiaru bazy danych. Nie dotyczy magazynów danych ani baz danych w skali.|Brak wymiarów|
|tempdb_data_size|Rozmiar pliku danych tempdb kilobajtów|Liczba|Maksimum|Rozmiar pliku danych tempdb kilobajtów. Nie dotyczy hurtowni danych. Ta Metryka będzie dostępna dla baz danych z modelem zakupów rdzeń wirtualny lub 100 jednostek DTU i wyższych dla modeli zakupów opartych na jednostkach DTU.|Brak wymiarów|
|tempdb_log_size|Rozmiar pliku dziennika bazy danych tempdb kilobajtów|Liczba|Maksimum|Rozmiar pliku dziennika bazy danych tempdb kilobajtów. Nie dotyczy hurtowni danych. Ta Metryka będzie dostępna dla baz danych z modelem zakupów rdzeń wirtualny lub 100 jednostek DTU i wyższych dla modeli zakupów opartych na jednostkach DTU.|Brak wymiarów|
|tempdb_log_used_percent|Użyto dziennika% tempdb|Procent|Maksimum|Użyto dziennika bazy danych tempdb. Nie dotyczy hurtowni danych. Ta Metryka będzie dostępna dla baz danych z modelem zakupów rdzeń wirtualny lub 100 jednostek DTU i wyższych dla modeli zakupów opartych na jednostkach DTU.|Brak wymiarów|
|workers_percent|Procent pracowników|Procent|Średnia|Procent pracowników. Nie dotyczy hurtowni danych.|Brak wymiarów|
|xtp_storage_percent|Procent magazynu OLTP w pamięci|Procent|Średnia|Procent magazynu OLTP w pamięci. Nie dotyczy hurtowni danych.|Brak wymiarów|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft. SQL/serwery/elasticPools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|allocated_data_storage|Przydzielono miejsce na danych|Szybkość|Średnia|Przydzielono miejsce na danych|Brak wymiarów|
|allocated_data_storage_percent|Procent przydzielonych przestrzeni danych|Procent|Maksimum|Procent przydzielonych przestrzeni danych|Brak wymiarów|
|cpu_limit|Limit CPU|Liczba|Średnia|Limit czasu procesora CPU. Dotyczy pul elastycznych opartych na rdzeń wirtualny.|Brak wymiarów|
|cpu_percent|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Brak wymiarów|
|cpu_used|Użycie procesora CPU|Liczba|Średnia|Użycie procesora CPU. Dotyczy pul elastycznych opartych na rdzeń wirtualny.|Brak wymiarów|
|dtu_consumption_percent|Procent użycia jednostek DTU|Procent|Średnia|Procent jednostek DTU. Dotyczy pul elastycznych opartych na jednostkach DTU.|Brak wymiarów|
|eDTU_limit|limit liczby jednostek eDTU|Liczba|Średnia|limit liczby jednostek eDTU. Dotyczy pul elastycznych opartych na jednostkach DTU.|Brak wymiarów|
|eDTU_used|użyta wartość eDTU|Liczba|Średnia|użyta wartość eDTU. Dotyczy pul elastycznych opartych na jednostkach DTU.|Brak wymiarów|
|log_write_percent|Procent operacji we/wy dziennika|Procent|Średnia|Procent operacji we/wy dziennika|Brak wymiarów|
|physical_data_read_percent|Procent użycia operacji we/wy na danych|Procent|Średnia|Procent użycia operacji we/wy na danych|Brak wymiarów|
|sessions_percent|Procent sesji|Procent|Średnia|Procent sesji|Brak wymiarów|
|storage_limit|Maksymalny rozmiar danych|Szybkość|Średnia|Limit magazynu|Brak wymiarów|
|storage_percent|Procent użytego miejsca na danych||Procent|Średnia|Procent miejsca do magazynowania|Brak wymiarów|
|storage_used|Używane miejsce na dane|Szybkość|Średnia|Użyty magazyn|Brak wymiarów|
|sqlserver_process_core_percent|Procent podstawowych procesów SQL Server|Procent|Maksimum|Ta Metryka jest symbolem zastępczym i nie jest w tej chwili wypełniana.|Brak wymiarów|
|sqlserver_process_memory_percent|Procent pamięci procesu SQL Server|Procent|Maksimum|Ta Metryka jest symbolem zastępczym i nie jest w tej chwili wypełniana.|Brak wymiarów|
|tempdb_data_size|Rozmiar pliku danych tempdb kilobajtów|Liczba|Maksimum|Rozmiar pliku danych tempdb kilobajtów. Nie dotyczy hurtowni danych. Ta Metryka będzie dostępna dla baz danych z modelem zakupów rdzeń wirtualny lub 100 jednostek DTU i wyższych dla modeli zakupów opartych na jednostkach DTU.|Brak wymiarów|
|tempdb_log_size|Rozmiar pliku dziennika bazy danych tempdb kilobajtów|Liczba|Maksimum|Rozmiar pliku dziennika bazy danych tempdb kilobajtów. Nie dotyczy hurtowni danych. Ta Metryka będzie dostępna dla baz danych z modelem zakupów rdzeń wirtualny lub 100 jednostek DTU i wyższych dla modeli zakupów opartych na jednostkach DTU.|Brak wymiarów|
|tempdb_log_used_percent|Użyto dziennika% tempdb|Procent|Maksimum|Użyto dziennika bazy danych tempdb. Nie dotyczy hurtowni danych. Ta Metryka będzie dostępna dla baz danych z modelem zakupów rdzeń wirtualny lub 100 jednostek DTU i wyższych dla modeli zakupów opartych na jednostkach DTU.|Brak wymiarów|
|workers_percent|Procent pracowników|Procent|Średnia|Procent pracowników|Brak wymiarów|
|xtp_storage_percent|Procent magazynu OLTP w pamięci|Procent|Średnia|Procent magazynu OLTP w pamięci|Brak wymiarów|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|avg_cpu_percent|Średni procent procesora CPU|Procent|Średnia|Średni procent procesora CPU|Brak wymiarów|
|io_bytes_read|Odczytane bajty we/wy|Szybkość|Średnia|Odczytane bajty we/wy|Brak wymiarów|
|io_requests|Liczba żądań we/wy|Liczba|Średnia|Liczba żądań we/wy|Brak wymiarów|
|io_bytes_written|Bajty we/wy zapisywane|Szybkość|Średnia|Bajty we/wy zapisywane|Brak wymiarów|
|reserved_storage_mb|Zarezerwowane miejsce w magazynie|Liczba|Średnia|Zarezerwowane miejsce w magazynie|Brak wymiarów|
|storage_space_used_mb|Używane miejsce do magazynowania|Liczba|Średnia|Używane miejsce do magazynowania|Brak wymiarów|
|virtual_core_count|Liczba rdzeni wirtualnych|Liczba|Średnia|Liczba rdzeni wirtualnych|Brak wymiarów|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|UsedCapacity|Używana pojemność|Szybkość|Średnia|Używana pojemność konta|Brak wymiarów|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru responsetype dla liczby różnych typów odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Zdarzenia związane z transferem danych przychodzących|Zdarzenia związane z transferem danych przychodzących|Szybkość|Łącznie|Ilość danych wejściowych w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Szybkość|Łącznie|Ilość danych wyjściowych w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera sukcesu|)|Średnia|Średnie opóźnienie używane przez usługę Azure Storage do przetworzenia pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci uwzględnionego w wartości AverageE2ELatency.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie E2E sukcesu|)|Średnia|Średni czas oczekiwania na zakończenie pomyślnych żądań skierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BlobCapacity|Pojemność obiektu BLOB|Szybkość|Średnia|Ilość miejsca do magazynowania używanego przez Blob service konta magazynu w bajtach.|Blobtype, warstwa|
|BlobCount|Liczba obiektów BLOB|Liczba|Łącznie|Liczba obiektów BLOB w Blob service konta magazynu.|Obiekt blobtype|       |BlobCount|Liczba obiektów BLOB|Liczba|Średnia|Liczba obiektów BLOB w Blob service konta magazynu.|Blobtype, warstwa|
|ContainerCount|Liczba kontenerów obiektów BLOB|Liczba|Średnia|Liczba kontenerów w Blob service konta magazynu.|Brak wymiarów|
|IndexCapacity|Pojemność indeksu|Szybkość|Średnia|Ilość miejsca do magazynowania używanego przez ADLS Gen2 (hierarchiczny) indeks w bajtach.|Brak wymiarów|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru responsetype dla liczby różnych typów odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Zdarzenia związane z transferem danych przychodzących|Zdarzenia związane z transferem danych przychodzących|Szybkość|Łącznie|Ilość danych wejściowych w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Szybkość|Łącznie|Ilość danych wyjściowych w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera sukcesu|)|Średnia|Średnie opóźnienie używane przez usługę Azure Storage do przetworzenia pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci uwzględnionego w wartości AverageE2ELatency.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie E2E sukcesu|)|Średnia|Średni czas oczekiwania na zakończenie pomyślnych żądań skierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|FileCapacity|Pojemność plików|Szybkość|Średnia|Ilość miejsca do magazynowania używanego przez usługę plików konta magazynu w bajtach.|Brak wymiarów|
|FileCount|Liczba plików|Liczba|Średnia|Liczba plików w usłudze plików konta magazynu.|Brak wymiarów|
|FileShareCount|Liczba udziałów plików|Liczba|Średnia|Liczba udziałów plików w usłudze plików konta magazynu.|Brak wymiarów|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru responsetype dla liczby różnych typów odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Zdarzenia związane z transferem danych przychodzących|Zdarzenia związane z transferem danych przychodzących|Szybkość|Łącznie|Ilość danych wejściowych w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Szybkość|Łącznie|Ilość danych wyjściowych w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera sukcesu|)|Średnia|Średnie opóźnienie używane przez usługę Azure Storage do przetworzenia pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci uwzględnionego w wartości AverageE2ELatency.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie E2E sukcesu|)|Średnia|Średni czas oczekiwania na zakończenie pomyślnych żądań skierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QueueCapacity|Pojemność kolejki|Szybkość|Średnia|Ilość miejsca do magazynowania używanego przez usługa kolejki konta magazynu w bajtach.|Brak wymiarów|
|QueueCount|Liczba kolejek|Liczba|Średnia|Liczba kolejek w usługa kolejki konta magazynu.|Brak wymiarów|
|QueueMessageCount|Liczba komunikatów w kolejce|Liczba|Średnia|Przybliżona liczba komunikatów w kolejce w usługa kolejki konta magazynu.|Brak wymiarów|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru responsetype dla liczby różnych typów odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Zdarzenia związane z transferem danych przychodzących|Zdarzenia związane z transferem danych przychodzących|Szybkość|Łącznie|Ilość danych wejściowych w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Szybkość|Łącznie|Ilość danych wyjściowych w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera sukcesu|)|Średnia|Średnie opóźnienie używane przez usługę Azure Storage do przetworzenia pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci uwzględnionego w wartości AverageE2ELatency.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie E2E sukcesu|)|Średnia|Średni czas oczekiwania na zakończenie pomyślnych żądań skierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TableCapacity|Pojemność tabeli|Szybkość|Średnia|Ilość miejsca do magazynowania używanego przez Table service konta magazynu w bajtach.|Brak wymiarów|
|TableCount|Liczba tabel|Liczba|Średnia|Liczba tabel w Table service konta magazynu.|Brak wymiarów|
|TableEntityCount|Liczba jednostek tabeli|Liczba|Średnia|Liczba jednostek tabeli w Table service konta magazynu.|Brak wymiarów|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru responsetype dla liczby różnych typów odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Zdarzenia związane z transferem danych przychodzących|Zdarzenia związane z transferem danych przychodzących|Szybkość|Łącznie|Ilość danych wejściowych w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Szybkość|Łącznie|Ilość danych wyjściowych w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera sukcesu|)|Średnia|Średnie opóźnienie używane przez usługę Azure Storage do przetworzenia pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci uwzględnionego w wartości AverageE2ELatency.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie E2E sukcesu|)|Średnia|Średni czas oczekiwania na zakończenie pomyślnych żądań skierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|

## <a name="microsoftstoragesyncstoragesyncservices"></a>Microsoft. storagesync/storageSyncServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Wynik synchronizacji sesji|Liczba|Średnia|Metryka, która rejestruje wartość 1 za każdym razem, gdy punkt końcowy serwera pomyślnie ukończy sesję synchronizacji z punktem końcowym w chmurze|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Synchronizowane pliki|Liczba|Łącznie|Liczba synchronizowanych plików|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Pliki, których nie należy synchronizować|Liczba|Łącznie|Nie można zsynchronizować liczby plików|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Zsynchronizowane bajty|Szybkość|Łącznie|Łączny rozmiar pliku transferowanego dla sesji synchronizacji|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Stan online serwera|Liczba|Maksimum|Metryka, która rejestruje wartość 1 za każdym razem, gdy zarejestrowany serwer pomyślnie rejestruje puls przy użyciu punktu końcowego w chmurze|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Odwołanie do warstw w chmurze|Szybkość|Łącznie|Łączny rozmiar danych, które zostały wywołane przez serwer|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ResourceUtilization|Użycie SU%|Procent|Maksimum|Użycie SU%|Logicznaname, PartitionId|
|InputEvents|Zdarzenia wejściowe|Liczba|Łącznie|Zdarzenia wejściowe|Logicznaname, PartitionId|
|InputEventBytes|Bajty zdarzeń wejściowych|Szybkość|Łącznie|Bajty zdarzeń wejściowych|Logicznaname, PartitionId|
|LateInputEvents|Opóźnione zdarzenia wejściowe|Liczba|Łącznie|Opóźnione zdarzenia wejściowe|Logicznaname, PartitionId|
|OutputEvents|Zdarzenia wyjściowe|Liczba|Łącznie|Zdarzenia wyjściowe|Logicznaname, PartitionId|
|ConversionErrors|Błędy konwersji danych|Liczba|Łącznie|Błędy konwersji danych|Logicznaname, PartitionId|
|Błędy|Błędy środowiska uruchomieniowego|Liczba|Łącznie|Błędy środowiska uruchomieniowego|Logicznaname, PartitionId|
|DroppedOrAdjustedEvents|Zdarzenia poza kolejnością|Liczba|Łącznie|Zdarzenia poza kolejnością|Logicznaname, PartitionId|
|AMLCalloutRequests|Żądania funkcji|Liczba|Łącznie|Żądania funkcji|Logicznaname, PartitionId|
|AMLCalloutFailedRequests|Nieudane żądania funkcji|Liczba|Łącznie|Nieudane żądania funkcji|Logicznaname, PartitionId|
|AMLCalloutInputEvents|Zdarzenia funkcji|Liczba|Łącznie|Zdarzenia funkcji|Logicznaname, PartitionId|
|DeserializationError|Błędy deserializacji danych wejściowych|Liczba|Łącznie|Błędy deserializacji danych wejściowych|Logicznaname, PartitionId|
|EarlyInputEvents|Wczesne zdarzenia wejściowe|Liczba|Łącznie|Wczesne zdarzenia wejściowe|Logicznaname, PartitionId|
|OutputWatermarkDelaySeconds|Opóźnienie znaku wodnego|Sekundy|Maksimum|Opóźnienie znaku wodnego|Logicznaname, PartitionId|
|InputEventsSourcesBacklogged|Zaległe zdarzenia wejściowe|Liczba|Maksimum|Zaległe zdarzenia wejściowe|Logicznaname, PartitionId|
|InputEventsSourcesPerSecond|Odebrane źródła danych wejściowych|Liczba|Łącznie|Odebrane źródła danych wejściowych|Logicznaname, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/środowiska

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|IngressReceivedMessages|Odebrane komunikaty transferu danych przychodzących|Liczba|Łącznie|Liczba komunikatów odczytanych ze wszystkich źródeł zdarzeń centrum zdarzeń lub Centrum IoT Hub|Brak wymiarów|
|IngressReceivedInvalidMessages|Odebrano nieprawidłowe komunikaty dotyczące transferu danych przychodzących|Liczba|Łącznie|Liczba nieprawidłowych komunikatów odczytywanych ze wszystkich centrów zdarzeń lub źródeł zdarzeń usługi IoT Hub|Brak wymiarów|
|IngressReceivedBytes|Bajty odebrane z ruchu przychodzącego|Szybkość|Łącznie|Liczba bajtów odczytanych ze wszystkich źródeł zdarzeń|Brak wymiarów|
|IngressStoredBytes|Bajty przechowywane w ruchu przychodzącym|Szybkość|Łącznie|Łączny rozmiar zdarzeń pomyślnie przetworzonych i dostępnych dla zapytania|Brak wymiarów|
|IngressStoredEvents|Zdarzenia związane z transferem danych przychodzących|Liczba|Łącznie|Liczba zdarzeń spłaszczonych pomyślnie przetworzonych i dostępnych dla zapytania|Brak wymiarów|
|IngressReceivedMessagesTimeLag|Zwłoka czasu odbierania komunikatów przychodzących|Sekundy|Maksimum|Różnica między czasem, w którym wiadomość jest przetworzona do kolejki w źródle zdarzeń i czasie, w którym jest przetwarzany w danych wejściowych|Brak wymiarów|
|IngressReceivedMessagesCountLag|Opóźnienie liczby odebranych komunikatów przychodzących|Liczba|Średnia|Różnica między numerem sekwencyjnym ostatnio zarejestrowanego komunikatu w partycji źródłowej zdarzenia i sekwencją sekwencji komunikatów przetwarzanych w ramach ruchu przychodzącego|Brak wymiarów|
|WarmStorageMaxProperties|Maksymalna liczba właściwości magazynu ciepłego|Liczba|Maksimum|Maksymalna liczba właściwości używanych przez środowisko dla jednostki SKU S1/S2 i Maksymalna liczba właściwości dozwolona przez magazyn ciepły dla jednostki SKU|Brak wymiarów|
|WarmStorageUsedProperties|Właściwości używanej pamięci masowej |Liczba|Maksimum|Liczba właściwości używanych przez środowisko dla jednostki SKU S1/S2 i liczby właściwości używanych przez magazyn ciepły dla jednostek SKU|Brak wymiarów|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/Environments/eventsources

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|IngressReceivedMessages|Odebrane komunikaty transferu danych przychodzących|Liczba|Łącznie|Liczba komunikatów odczytanych ze źródła zdarzeń|Brak wymiarów|
|IngressReceivedInvalidMessages|Odebrano nieprawidłowe komunikaty dotyczące transferu danych przychodzących|Liczba|Łącznie|Liczba nieprawidłowych komunikatów odczytanych ze źródła zdarzeń|Brak wymiarów|
|IngressReceivedBytes|Bajty odebrane z ruchu przychodzącego|Szybkość|Łącznie|Liczba bajtów odczytanych ze źródła zdarzenia|Brak wymiarów|
|IngressStoredBytes|Bajty przechowywane w ruchu przychodzącym|Szybkość|Łącznie|Łączny rozmiar zdarzeń pomyślnie przetworzonych i dostępnych dla zapytania|Brak wymiarów|
|IngressStoredEvents|Zdarzenia związane z transferem danych przychodzących|Liczba|Łącznie|Liczba zdarzeń spłaszczonych pomyślnie przetworzonych i dostępnych dla zapytania|Brak wymiarów|
|IngressReceivedMessagesTimeLag|Zwłoka czasu odbierania komunikatów przychodzących|Sekundy|Maksimum|Różnica między czasem, w którym wiadomość jest przetworzona do kolejki w źródle zdarzeń i czasie, w którym jest przetwarzany w danych wejściowych|Brak wymiarów|
|IngressReceivedMessagesCountLag|Opóźnienie liczby odebranych komunikatów przychodzących|Liczba|Średnia|Różnica między numerem sekwencyjnym ostatnio zarejestrowanego komunikatu w partycji źródłowej zdarzenia i sekwencją sekwencji komunikatów przetwarzanych w ramach ruchu przychodzącego|Brak wymiarów|
|WarmStorageMaxProperties|Maksymalna liczba właściwości magazynu ciepłego|Liczba|Maksimum|Maksymalna liczba właściwości używanych przez środowisko dla jednostki SKU S1/S2 i Maksymalna liczba właściwości dozwolona przez magazyn ciepły dla jednostki SKU|Brak wymiarów|
|WarmStorageUsedProperties|Właściwości używanej pamięci masowej |Liczba|Maksimum|Liczba właściwości używanych przez środowisko dla jednostki SKU S1/S2 i liczby właściwości używanych przez magazyn ciepły dla jednostek SKU|Brak wymiarów|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. VMwareCloudSimple/virtualMachines

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Bajty odczytu dysku/s|BytesPerSecond|Średnia|Średnia przepływność dysku spowodowana operacjami odczytu w okresie próbkowania.|Brak wymiarów|
|DiskWriteBytesPerSecond|Bajty zapisu dysku/s|BytesPerSecond|Średnia|Średnia przepływność dysku spowodowana operacją zapisu w okresie próbnym.|Brak wymiarów|
|Bajty odczytu dysku|Bajty odczytu dysku|Szybkość|Łącznie|Łączna przepływność dysku spowodowana operacjami odczytu w okresie próbkowania.|Brak wymiarów|
|Bajty zapisu dysku|Bajty zapisu dysku|Szybkość|Łącznie|Łączna przepływność dysku spowodowana operacjami zapisu w okresie próbkowania.|Brak wymiarów|
|DiskReadOperations|Operacje odczytu z dysku|Liczba|Łącznie|Liczba operacji odczytu we/wy w poprzednim okresie próbkowania. Należy pamiętać, że operacje te mogą mieć rozmiar zmienny.|Brak wymiarów|
|DiskWriteOperations|Operacje zapisu na dysku|Liczba|Łącznie|Liczba operacji zapisu we/wy w poprzednim okresie próbkowania. Należy pamiętać, że operacje te mogą mieć rozmiar zmienny.|Brak wymiarów|
|Operacje odczytu z dysku/s|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Średnia liczba operacji odczytu we/wy w poprzednim okresie próbkowania. Należy pamiętać, że operacje te mogą mieć rozmiar zmienny.|Brak wymiarów|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Średnia liczba operacji we/wy zapisu w poprzednim okresie próbkowania. Należy pamiętać, że operacje te mogą mieć rozmiar zmienny.|Brak wymiarów|
|DiskReadLatency|Opóźnienie odczytu dysku|)|Średnia|Całkowite opóźnienie odczytu. Suma opóźnień odczytu urządzenia i jądra.|Brak wymiarów|
|DiskWriteLatency|Opóźnienie zapisu na dysku|)|Średnia|Całkowite opóźnienie zapisu. Suma opóźnień między urządzeniem i zapisem jądra.|Brak wymiarów|
|NetworkInBytesPerSecond|Sieć w bajtach/s|BytesPerSecond|Średnia|Średnia przepływność sieci dla odebranego ruchu.|Brak wymiarów|
|NetworkOutBytesPerSecond|Bajty wychodzące z sieci/s|BytesPerSecond|Średnia|Średnia przepływność sieci dla przesyłanego ruchu.|Brak wymiarów|
|Sieć — wejście|Sieć — wejście|Szybkość|Łącznie|Całkowita przepływność sieci dla odebranego ruchu.|Brak wymiarów|
|Sieć — wyjście|Sieć — wyjście|Szybkość|Łącznie|Całkowita przepustowość sieci przesyłanych danych.|Brak wymiarów|
|MemoryUsed|Używana pamięć|Szybkość|Średnia|Ilość pamięci maszyny używanej przez maszynę wirtualną.|Brak wymiarów|
|MemoryGranted|Przydzielone pamięci|Szybkość|Średnia|Ilość pamięci przydzielonej maszynie wirtualnej przez hosta. Pamięć nie jest przydzielana do hosta, dopóki nie zostanie ona nałożona jeden raz, a przyznana pamięć może zostać zamieniony lub zaznaczono w dymku, jeśli VMkernel wymaga pamięci.|Brak wymiarów|
|MemoryActive|Pamięć aktywna|Szybkość|Średnia|Ilość pamięci używanej przez maszynę wirtualną w minionym małym oknie czasu. Jest to wartość "prawda" ilości pamięci wymaganej przez maszynę wirtualną. Dodatkowa niewykorzystana pamięć może zostać zastąpiona lub zaprowadzona bez wpływu na wydajność gościa.|Brak wymiarów|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Średnia|Użycie procesora CPU. Ta wartość jest raportowana przy użyciu 100% reprezentujących wszystkie rdzenie procesora w systemie. Przykładowo 2-kierunkowa maszyna wirtualna używająca 50% systemu z czterema rdzeniem korzysta całkowicie z dwóch rdzeni.|Brak wymiarów|
|PercentageCpuReady|Procent gotowych do użycia procesora|)|Łącznie|Czas gotowości to czas oczekiwania, aż procesor CPU stanie się dostępny w okresie ostatniej aktualizacji.|Brak wymiarów|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuPercentage|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Wystąpienie|
|MemoryPercentage|Procent pamięci|Procent|Średnia|Procent pamięci|Wystąpienie|
|DiskQueueLength|Długość kolejki dysku|Liczba|Średnia|Długość kolejki dysku|Wystąpienie|
|HttpQueueLength|Długość kolejki http|Liczba|Średnia|Długość kolejki http|Wystąpienie|
|BytesReceived|Dane w|Szybkość|Łącznie|Dane w|Wystąpienie|
|BytesSent|Dane wychodzące|Szybkość|Łącznie|Dane wychodzące|Wystąpienie|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/Sites (z wyjątkiem funkcji)

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuTime|Czas procesora CPU|Sekundy|Łącznie|Czas procesora CPU|Wystąpienie|
|Żądania|Żądania|Liczba|Łącznie|Żądania|Wystąpienie|
|BytesReceived|Dane w|Szybkość|Łącznie|Dane w|Wystąpienie|
|BytesSent|Dane wychodzące|Szybkość|Łącznie|Dane wychodzące|Wystąpienie|
|Http101|Http 101|Liczba|Łącznie|Http 101|Wystąpienie|
|Http2xx|Http 2xx|Liczba|Łącznie|Http 2xx|Wystąpienie|
|Http3xx|Http 3xx|Liczba|Łącznie|Http 3xx|Wystąpienie|
|Http401|HTTP 401|Liczba|Łącznie|HTTP 401|Wystąpienie|
|Http403|HTTP 403|Liczba|Łącznie|HTTP 403|Wystąpienie|
|Http404|HTTP 404|Liczba|Łącznie|HTTP 404|Wystąpienie|
|Http406|Http 406|Liczba|Łącznie|Http 406|Wystąpienie|
|Http4xx|Http 4xx|Liczba|Łącznie|Http 4xx|Wystąpienie|
|Http5xx|Błędy serwera http|Liczba|Łącznie|Błędy serwera http|Wystąpienie|
|MemoryWorkingSet|Zestaw roboczy pamięci|Szybkość|Średnia|Zestaw roboczy pamięci|Wystąpienie|
|AverageMemoryWorkingSet|Średni zestaw roboczy pamięci|Szybkość|Średnia|Średni zestaw roboczy pamięci|Wystąpienie|
|AverageResponseTime|Średni czas odpowiedzi|Sekundy|Średnia|Średni czas odpowiedzi|Wystąpienie|
|AppConnections|Połączenia|Liczba|Średnia|Połączenia|Wystąpienie|
|Realizuj|Liczba dojść|Liczba|Średnia|Liczba dojść|Wystąpienie|
|Wątk|Liczba wątków|Liczba|Średnia|Liczba wątków|Wystąpienie|
|PrivateBytes|Bajty prywatne|Szybkość|Średnia|Bajty prywatne|Wystąpienie|
|IoReadBytesPerSecond|Bajty odczytu we/wy na sekundę|BytesPerSecond|Łącznie|Bajty odczytu we/wy na sekundę|Wystąpienie|
|IoWriteBytesPerSecond|Bajty zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Bajty zapisu we/wy na sekundę|Wystąpienie|
|IoOtherBytesPerSecond|Inne bajty we/wy na sekundę|BytesPerSecond|Łącznie|Inne bajty we/wy na sekundę|Wystąpienie|
|IoReadOperationsPerSecond|Operacje odczytu we/wy na sekundę|BytesPerSecond|Łącznie|Operacje odczytu we/wy na sekundę|Wystąpienie|
|IoWriteOperationsPerSecond|Operacje zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Operacje zapisu we/wy na sekundę|Wystąpienie|
|IoOtherOperationsPerSecond|Inne operacje we/wy na sekundę|BytesPerSecond|Łącznie|Inne operacje we/wy na sekundę|Wystąpienie|
|RequestsInApplicationQueue|Żądania w kolejce aplikacji|Liczba|Średnia|Żądania w kolejce aplikacji|Wystąpienie|
|CurrentAssemblies|Bieżące zestawy|Liczba|Średnia|Bieżące zestawy|Wystąpienie|
|TotalAppDomains|Łączna liczba domen aplikacji|Liczba|Średnia|Łączna liczba domen aplikacji|Wystąpienie|
|TotalAppDomainsUnloaded|Całkowita liczba zwolnionych domen aplikacji|Liczba|Średnia|Całkowita liczba zwolnionych domen aplikacji|Wystąpienie|
|Gen0Collections|Zbieranie elementów bezużytecznych generacji 0|Liczba|Łącznie|Zbieranie elementów bezużytecznych generacji 0|Wystąpienie|
|Gen1Collections|Wyrzucanie elementów bezużytecznych generacji 1|Liczba|Łącznie|Wyrzucanie elementów bezużytecznych generacji 1|Wystąpienie|
|Gen2Collections|Zbieranie elementów bezużytecznych generacji 2|Liczba|Łącznie|Zbieranie elementów bezużytecznych generacji 2|Wystąpienie|

## <a name="microsoftwebsites-functions"></a>Microsoft. Web/Sites (funkcje)

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BytesReceived|Dane w|Szybkość|Łącznie|Dane w|Wystąpienie|
|BytesSent|Dane wychodzące|Szybkość|Łącznie|Dane wychodzące|Wystąpienie|
|Http5xx|Błędy serwera http|Liczba|Łącznie|Błędy serwera http|Wystąpienie|
|MemoryWorkingSet|Zestaw roboczy pamięci|Szybkość|Średnia|Zestaw roboczy pamięci|Wystąpienie|
|AverageMemoryWorkingSet|Średni zestaw roboczy pamięci|Szybkość|Średnia|Średni zestaw roboczy pamięci|Wystąpienie|
|FunctionExecutionUnits|Jednostki wykonywania funkcji|MB/milisekundy|Łącznie|[Jednostki wykonywania funkcji](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Wystąpienie|
|FunctionExecutionCount|Liczba wykonań funkcji|Liczba|Łącznie|Liczba wykonań funkcji|Wystąpienie|
|PrivateBytes|Bajty prywatne|Szybkość|Średnia|Bajty prywatne|Wystąpienie|
|IoReadBytesPerSecond|Bajty odczytu we/wy na sekundę|BytesPerSecond|Łącznie|Bajty odczytu we/wy na sekundę|Wystąpienie|
|IoWriteBytesPerSecond|Bajty zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Bajty zapisu we/wy na sekundę|Wystąpienie|
|IoOtherBytesPerSecond|Inne bajty we/wy na sekundę|BytesPerSecond|Łącznie|Inne bajty we/wy na sekundę|Wystąpienie|
|IoReadOperationsPerSecond|Operacje odczytu we/wy na sekundę|BytesPerSecond|Łącznie|Operacje odczytu we/wy na sekundę|Wystąpienie|
|IoWriteOperationsPerSecond|Operacje zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Operacje zapisu we/wy na sekundę|Wystąpienie|
|IoOtherOperationsPerSecond|Inne operacje we/wy na sekundę|BytesPerSecond|Łącznie|Inne operacje we/wy na sekundę|Wystąpienie|
|RequestsInApplicationQueue|Żądania w kolejce aplikacji|Liczba|Średnia|Żądania w kolejce aplikacji|Wystąpienie|
|CurrentAssemblies|Bieżące zestawy|Liczba|Średnia|Bieżące zestawy|Wystąpienie|
|TotalAppDomains|Łączna liczba domen aplikacji|Liczba|Średnia|Łączna liczba domen aplikacji|Wystąpienie|
|TotalAppDomainsUnloaded|Całkowita liczba zwolnionych domen aplikacji|Liczba|Średnia|Całkowita liczba zwolnionych domen aplikacji|Wystąpienie|
|Gen0Collections|Zbieranie elementów bezużytecznych generacji 0|Liczba|Łącznie|Zbieranie elementów bezużytecznych generacji 0|Wystąpienie|
|Gen1Collections|Wyrzucanie elementów bezużytecznych generacji 1|Liczba|Łącznie|Wyrzucanie elementów bezużytecznych generacji 1|Wystąpienie|
|Gen2Collections|Zbieranie elementów bezużytecznych generacji 2|Liczba|Łącznie|Zbieranie elementów bezużytecznych generacji 2|Wystąpienie|

## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/miejsca

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuTime|Czas procesora CPU|Sekundy|Łącznie|Czas procesora CPU|Wystąpienie|
|Żądania|Żądania|Liczba|Łącznie|Żądania|Wystąpienie|
|BytesReceived|Dane w|Szybkość|Łącznie|Dane w|Wystąpienie|
|BytesSent|Dane wychodzące|Szybkość|Łącznie|Dane wychodzące|Wystąpienie|
|Http101|Http 101|Liczba|Łącznie|Http 101|Wystąpienie|
|Http2xx|Http 2xx|Liczba|Łącznie|Http 2xx|Wystąpienie|
|Http3xx|Http 3xx|Liczba|Łącznie|Http 3xx|Wystąpienie|
|Http401|HTTP 401|Liczba|Łącznie|HTTP 401|Wystąpienie|
|Http403|HTTP 403|Liczba|Łącznie|HTTP 403|Wystąpienie|
|Http404|HTTP 404|Liczba|Łącznie|HTTP 404|Wystąpienie|
|Http406|Http 406|Liczba|Łącznie|Http 406|Wystąpienie|
|Http4xx|Http 4xx|Liczba|Łącznie|Http 4xx|Wystąpienie|
|Http5xx|Błędy serwera http|Liczba|Łącznie|Błędy serwera http|Wystąpienie|
|MemoryWorkingSet|Zestaw roboczy pamięci|Szybkość|Średnia|Zestaw roboczy pamięci|Wystąpienie|
|AverageMemoryWorkingSet|Średni zestaw roboczy pamięci|Szybkość|Średnia|Średni zestaw roboczy pamięci|Wystąpienie|
|AverageResponseTime|Średni czas odpowiedzi|Sekundy|Średnia|Średni czas odpowiedzi|Wystąpienie|
|FunctionExecutionUnits|Jednostki wykonywania funkcji|Liczba|Łącznie|Jednostki wykonywania funkcji|Wystąpienie|
|FunctionExecutionCount|Liczba wykonań funkcji|Liczba|Łącznie|Liczba wykonań funkcji|Wystąpienie|
|AppConnections|Połączenia|Liczba|Średnia|Połączenia|Wystąpienie|
|Realizuj|Liczba dojść|Liczba|Średnia|Liczba dojść|Wystąpienie|
|Wątk|Liczba wątków|Liczba|Średnia|Liczba wątków|Wystąpienie|
|PrivateBytes|Bajty prywatne|Szybkość|Średnia|Bajty prywatne|Wystąpienie|
|IoReadBytesPerSecond|Bajty odczytu we/wy na sekundę|BytesPerSecond|Łącznie|Bajty odczytu we/wy na sekundę|Wystąpienie|
|IoWriteBytesPerSecond|Bajty zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Bajty zapisu we/wy na sekundę|Wystąpienie|
|IoOtherBytesPerSecond|Inne bajty we/wy na sekundę|BytesPerSecond|Łącznie|Inne bajty we/wy na sekundę|Wystąpienie|
|IoReadOperationsPerSecond|Operacje odczytu we/wy na sekundę|BytesPerSecond|Łącznie|Operacje odczytu we/wy na sekundę|Wystąpienie|
|IoWriteOperationsPerSecond|Operacje zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Operacje zapisu we/wy na sekundę|Wystąpienie|
|IoOtherOperationsPerSecond|Inne operacje we/wy na sekundę|BytesPerSecond|Łącznie|Inne operacje we/wy na sekundę|Wystąpienie|
|RequestsInApplicationQueue|Żądania w kolejce aplikacji|Liczba|Średnia|Żądania w kolejce aplikacji|Wystąpienie|
|CurrentAssemblies|Bieżące zestawy|Liczba|Średnia|Bieżące zestawy|Wystąpienie|
|TotalAppDomains|Łączna liczba domen aplikacji|Liczba|Średnia|Łączna liczba domen aplikacji|Wystąpienie|
|TotalAppDomainsUnloaded|Całkowita liczba zwolnionych domen aplikacji|Liczba|Średnia|Całkowita liczba zwolnionych domen aplikacji|Wystąpienie|
|Gen0Collections|Zbieranie elementów bezużytecznych generacji 0|Liczba|Łącznie|Zbieranie elementów bezużytecznych generacji 0|Wystąpienie|
|Gen1Collections|Wyrzucanie elementów bezużytecznych generacji 1|Liczba|Łącznie|Wyrzucanie elementów bezużytecznych generacji 1|Wystąpienie|
|Gen2Collections|Zbieranie elementów bezużytecznych generacji 2|Liczba|Łącznie|Zbieranie elementów bezużytecznych generacji 2|Wystąpienie|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft. Web/hostingEnvironments/multiRolePools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Żądania|Żądania|Liczba|Łącznie|Żądania|Wystąpienie|
|BytesReceived|Dane w|Szybkość|Łącznie|Dane w|Wystąpienie|
|BytesSent|Dane wychodzące|Szybkość|Łącznie|Dane wychodzące|Wystąpienie|
|Http101|Http 101|Liczba|Łącznie|Http 101|Wystąpienie|
|Http2xx|Http 2xx|Liczba|Łącznie|Http 2xx|Wystąpienie|
|Http3xx|Http 3xx|Liczba|Łącznie|Http 3xx|Wystąpienie|
|Http401|HTTP 401|Liczba|Łącznie|HTTP 401|Wystąpienie|
|Http403|HTTP 403|Liczba|Łącznie|HTTP 403|Wystąpienie|
|Http404|HTTP 404|Liczba|Łącznie|HTTP 404|Wystąpienie|
|Http406|Http 406|Liczba|Łącznie|Http 406|Wystąpienie|
|Http4xx|Http 4xx|Liczba|Łącznie|Http 4xx|Wystąpienie|
|Http5xx|Błędy serwera http|Liczba|Łącznie|Błędy serwera http|Wystąpienie|
|AverageResponseTime|Średni czas odpowiedzi|Sekundy|Średnia|Średni czas odpowiedzi|Wystąpienie|
|CpuPercentage|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Wystąpienie|
|MemoryPercentage|Procent pamięci|Procent|Średnia|Procent pamięci|Wystąpienie|
|DiskQueueLength|Długość kolejki dysku|Liczba|Średnia|Długość kolejki dysku|Wystąpienie|
|HttpQueueLength|Długość kolejki http|Liczba|Średnia|Długość kolejki http|Wystąpienie|
|ActiveRequests|Aktywne żądania|Liczba|Łącznie|Aktywne żądania|Wystąpienie|
|TotalFrontEnds|Łączne frontony|Liczba|Średnia|Łączne frontony|Brak wymiarów|
|SmallAppServicePlanInstances|Niewielka App Service planowanie procesów roboczych|Liczba|Średnia|Niewielka App Service planowanie procesów roboczych|Brak wymiarów|
|MediumAppServicePlanInstances|Średni App Service pracowników planu|Liczba|Średnia|Średni App Service pracowników planu|Brak wymiarów|
|LargeAppServicePlanInstances|Duże App Service planowanie procesów roboczych|Liczba|Średnia|Duże App Service planowanie procesów roboczych|Brak wymiarów|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Łączna|Łączna liczba procesów roboczych|Liczba|Średnia|Łączna liczba procesów roboczych|Brak wymiarów|
|WorkersAvailable|Dostępni pracownicy|Liczba|Średnia|Dostępni pracownicy|Brak wymiarów|
|WorkersUsed|Używani pracownicy|Liczba|Średnia|Używani pracownicy|Brak wymiarów|
|CpuPercentage|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Wystąpienie|
|MemoryPercentage|Procent pamięci|Procent|Średnia|Procent pamięci|Wystąpienie|

## <a name="next-steps"></a>Następne kroki
* [Przeczytaj informacje o metrykach w Azure Monitor](data-platform.md)
* [Tworzenie alertów dotyczących metryk](alerts-overview.md)
* [Eksportowanie metryk do magazynu, centrum zdarzeń lub Log Analytics](resource-logs-overview.md)
