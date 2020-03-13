---
title: Azure Monitor obsługiwane metryki według typu zasobu
description: Lista metryk dostępnych dla każdego typu zasobu z Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.topic: reference
ms.date: 12/18/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 3e43d2baf4337e7a986d59c47f805183a920c7a1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275388"
---
# <a name="supported-metrics-with-azure-monitor"></a>Obsługiwane metryki z Azure Monitor

Azure Monitor oferuje kilka sposobów współpracy z metrykami, w tym wykresów w portalu, uzyskiwania do nich dostępu za pośrednictwem interfejsu API REST lub wykonywania zapytań przy użyciu programu PowerShell lub interfejsu wiersza polecenia. Poniżej znajduje się pełna lista wszystkich metryk dostępnych obecnie z potoku metryk Azure Monitor. Inne metryki mogą być dostępne w portalu lub przy użyciu starszych interfejsów API. Ta lista zawiera tylko metryki dostępne przy użyciu potoku metryki Azure Monitor skonsolidowanej. Metryki są zorganizowane według przestrzeni nazw. Aby uzyskać listę usług i przestrzenie nazw, które należą do nich, zobacz [dostawcy zasobów dla usług platformy Azure](../../azure-resource-manager/management/azure-services-resource-providers.md). Aby wykonać zapytanie o i uzyskać dostęp do tych metryk programowo, użyj [wersji interfejsu api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Wysyłanie metryk wielowymiarowych za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
>
> *Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak w przypadku eksportowania za pomocą ustawień diagnostycznych metryka ta jest przedstawiana jako wszystkie komunikaty przychodzące we wszystkich kolejkach w centrum zdarzeń.
>
> Aby uzyskać listę metryk platformy, które można eksportować za pośrednictwem ustawień diagnostycznych, zobacz [ten artykuł](metrics-supported-export-diagnostic-settings.md).



## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|qpu_metric|QPU|Licznik|Średnia|QPU. Zakres 0-100 dla S1, 0-200 dla S2 i 0-400 dla S4|ServerResourceType|
|memory_metric|Memory (Pamięć)|Bajty|Średnia|Rozmiar. Zakres 0-25 GB dla S1, 0-50 GB dla S2 i 0-100 GB dla S4|ServerResourceType|
|private_bytes_metric|Prywatne bajty|Bajty|Średnia|Bajty prywatne.|ServerResourceType|
|virtual_bytes_metric|Bajty wirtualne|Bajty|Średnia|Bajty wirtualne.|ServerResourceType|
|TotalConnectionRequests|Łączna liczba żądań połączenia|Licznik|Średnia|Łączna liczba żądań połączenia. Są to wejścia.|ServerResourceType|
|SuccessfullConnectionsPerSec|Udane połączenia na sekundę|CountPerSecond|Średnia|Szybkość pomyślnych połączeń.|ServerResourceType|
|TotalConnectionFailures|Łączna liczba błędów połączenia|Licznik|Średnia|Łączna liczba nieudanych prób połączenia.|ServerResourceType|
|CurrentUserSessions|Bieżące sesje użytkowników|Licznik|Średnia|Bieżąca liczba ustanowionych sesji użytkowników.|ServerResourceType|
|QueryPoolBusyThreads|Wątki zajęte w puli zapytań|Licznik|Średnia|Liczba zajętych wątków w puli wątków zapytań.|ServerResourceType|
|CommandPoolJobQueueLength|Długość kolejki zadań puli poleceń|Licznik|Średnia|Liczba zadań w kolejce puli wątków poleceń.|ServerResourceType|
|ProcessingPoolJobQueueLength|Długość kolejki zadań puli przetwarzania|Licznik|Średnia|Liczba zadań innych niż zadania we/wy w kolejce puli wątków przetwarzania.|ServerResourceType|
|Wartości CurrentConnections|Połączenie: bieżące połączenia|Licznik|Średnia|Bieżąca liczba ustanowionych połączeń klientów.|ServerResourceType|
|CleanerCurrentPrice|Pamięć: bieżąca cena oczyszczarki|Licznik|Średnia|Bieżąca cena pamięci, $/Byte/Time, znormalizowana do 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Pamięć: zmniejszanie ilości pamięci czyszczącej|Bajty|Średnia|Ilość pamięci, w bajtach, podlegająca przeczyszczaniu przez oczyszczarkę w tle.|ServerResourceType|
|CleanerMemoryNonshrinkable|Pamięć: pamięć oczyszczarki niemożliwa do zmniejszenia|Bajty|Średnia|Ilość pamięci (w bajtach), która nie podlega wyprzeczyszczaniu przez oczyszczarkę w tle.|ServerResourceType|
|MemoryUsage|Pamięć: użycie pamięci|Bajty|Średnia|Użycie pamięci przez proces serwera jako używany do obliczania ceny pamięci czyszczącej. Równa się licznikowi Process\PrivateBytes i rozmiarowi danych mapowanych na pamięć, ignorowanie wszelkich pamięci, które zostały zamapowane lub przydzielone przez aparat analityczny w pamięci xVelocity (VertiPaq) przekraczający limit pamięci aparatu xVelocity.|ServerResourceType|
|MemoryLimitHard|Pamięć: sztywny limit pamięci|Bajty|Średnia|Limit pamięci twardej z pliku konfiguracji.|ServerResourceType|
|MemoryLimitHigh|Pamięć: limit pamięci jest wysoki|Bajty|Średnia|Górny limit pamięci z pliku konfiguracji.|ServerResourceType|
|MemoryLimitLow|Pamięć: limit pamięci — niski|Bajty|Średnia|Limit braku pamięci z pliku konfiguracji.|ServerResourceType|
|MemoryLimitVertiPaq|Pamięć: limit pamięci — tryb VertiPaq|Bajty|Średnia|Limit w pamięci z pliku konfiguracji.|ServerResourceType|
|Działa|Pamięć: limit przydziału|Bajty|Średnia|Bieżący limit pamięci (w bajtach). Przydział pamięci jest również nazywany przydziałem pamięci lub rezerwacją pamięci.|ServerResourceType|
|QuotaBlocked|Pamięć: zablokowany limit przydziału|Licznik|Średnia|Bieżąca liczba żądań przydziałów blokowanych do momentu zwolnienia innych limitów przydziału pamięci.|ServerResourceType|
|VertiPaqNonpaged|Pamięć: VertiPaq niestronicowana|Bajty|Średnia|Bajty pamięci zablokowane w zestawie roboczym do użycia przez aparat w pamięci.|ServerResourceType|
|VertiPaqPaged|Pamięć: stronicowana na stronie VertiPaq|Bajty|Średnia|Bajty stronicowanej pamięci w użyciu dla danych w pamięci.|ServerResourceType|
|RowsReadPerSec|Przetwarzanie: odczytane wiersze na sekundę|CountPerSecond|Średnia|Współczynnik wierszy odczytanych ze wszystkich relacyjnych baz danych.|ServerResourceType|
|RowsConvertedPerSec|Przetwarzanie: przekonwertowane wiersze na sekundę|CountPerSecond|Średnia|Szybkość wierszy przekonwertowanych podczas przetwarzania.|ServerResourceType|
|RowsWrittenPerSec|Przetwarzanie: liczba wierszy na sekundę|CountPerSecond|Średnia|Częstotliwość wierszy zapisywana podczas przetwarzania.|ServerResourceType|
|CommandPoolBusyThreads|Wątki: zajęte wątki w puli poleceń|Licznik|Średnia|Liczba zajętych wątków w puli wątków poleceń.|ServerResourceType|
|CommandPoolIdleThreads|Wątki: bezczynne wątki w puli poleceń|Licznik|Średnia|Liczba bezczynnych wątków w puli wątków poleceń.|ServerResourceType|
|LongParsingBusyThreads|Wątki: zajęte wątki o długotrwałej analizie|Licznik|Średnia|Liczba zajętych wątków w puli wątków o długotrwałej analizie.|ServerResourceType|
|LongParsingIdleThreads|Wątki: długotrwałe wątki bezczynne analizy|Licznik|Średnia|Liczba bezczynnych wątków w puli wątków o długotrwałej analizie.|ServerResourceType|
|LongParsingJobQueueLength|Wątki: Długość kolejki zadań o długotrwałej analizie|Licznik|Średnia|Liczba zadań w kolejce puli wątków o długotrwałej analizie.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Wątki: wątki zadań we/wy puli przetwarzania|Licznik|Średnia|Liczba wątków uruchamiających zadania we/wy w puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Wątki: zajęte wątki innych niż we/wy puli przetwarzania|Licznik|Średnia|Liczba wątków uruchamiających zadania inne niż we/wy w puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Wątki: Długość kolejki zadań we/wy puli przetwarzania|Licznik|Średnia|Liczba zadań we/wy w kolejce puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Wątki: wątki zadań we/wy puli przetwarzania|Licznik|Średnia|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Wątki: bezczynne wątki inne niż we/wy puli przetwarzania|Licznik|Średnia|Liczba bezczynnych wątków w puli wątków przetwarzania dedykowanych dla zadań innych niż zadania we/wy.|ServerResourceType|
|QueryPoolIdleThreads|Wątki: bezczynne wątki puli zapytań|Licznik|Średnia|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|ServerResourceType|
|QueryPoolJobQueueLength|Wątki: Długość kolejki zadań puli zapytań|Licznik|Średnia|Liczba zadań w kolejce puli wątków zapytań.|ServerResourceType|
|ShortParsingBusyThreads|Wątki: zajęte wątki z krótkim analizowaniem|Licznik|Średnia|Liczba zajętych wątków w puli wątków o krótkiej analizie.|ServerResourceType|
|ShortParsingIdleThreads|Wątki: bezczynne wątki z krótkim analizowaniem|Licznik|Średnia|Liczba bezczynnych wątków w puli wątków o krótkiej analizie.|ServerResourceType|
|ShortParsingJobQueueLength|Wątki: Długość kolejki zadań o krótkiej analizie|Licznik|Średnia|Liczba zadań w kolejce puli wątków o krótkiej analizie.|ServerResourceType|
|memory_thrashing_metric|Migotanie pamięci|Procent|Średnia|Średnia pamięć migotanie.|ServerResourceType|
|mashup_engine_qpu_metric|M QPU aparatu|Licznik|Średnia|QPU użycie przez procesy aparatu mashupów|ServerResourceType|
|mashup_engine_memory_metric|Pamięć aparatu M|Bajty|Średnia|Użycie pamięci przez procesy aparatu mashupów|ServerResourceType|
|mashup_engine_private_bytes_metric|Liczba prywatnych bajtów aparatu M|Bajty|Średnia|Użycie bajtów prywatnych przez procesy aparatu mashupów.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Liczba bajtów wirtualnych aparatu M|Bajty|Średnia|Użycie bajtów wirtualnych przez procesy aparatu mashupów.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalRequests|Łączna liczba żądań bramy (przestarzałe)|Licznik|Łącznie|Liczba żądań bramy — zamiast tego użyj metryki żądania wielowymiarowego z wymiarem GatewayResponseCodeCategory|Lokalizacja, nazwa hosta|
|Żądania successfulrequests|Pomyślne żądania bramy (przestarzałe)|Licznik|Łącznie|Liczba pomyślnych żądań bramy — zamiast tego użyj metryki żądania wielowymiarowego z wymiarem GatewayResponseCodeCategory|Lokalizacja, nazwa hosta|
|UnauthorizedRequests|Nieautoryzowane żądania bramy (przestarzałe)|Licznik|Łącznie|Liczba nieautoryzowanych żądań bramy — zamiast tego użyj metryki żądania wielowymiarowego z wymiarem GatewayResponseCodeCategory|Lokalizacja, nazwa hosta|
|FailedRequests|Nieudane żądania bramy (przestarzałe)|Licznik|Łącznie|Liczba błędów w żądaniach bramy — zamiast tego użyj metryki żądania wielowymiarowego z wymiarem GatewayResponseCodeCategory|Lokalizacja, nazwa hosta|
|OtherRequests|Inne żądania bramy (przestarzałe)|Licznik|Łącznie|Liczba innych żądań bramy — zamiast tego użyj metryki żądania wielowymiarowego z wymiarem GatewayResponseCodeCategory|Lokalizacja, nazwa hosta|
|Czas trwania|Całkowity czas trwania żądań bramy|Milisekundy|Średnia|Całkowity czas trwania żądań bramy w milisekundach|Lokalizacja, nazwa hosta|
|BackendDuration|Czas trwania żądań wewnętrznej bazy danych|Milisekundy|Średnia|Czas trwania żądań zaplecza w milisekundach|Lokalizacja, nazwa hosta|
|Pojemność|Pojemność|Procent|Średnia|Metryka wykorzystania dla usługi ApiManagement|Lokalizacja|
|EventHubTotalEvents|Łączna liczba zdarzeń EventHub|Licznik|Łącznie|Liczba zdarzeń wysyłanych do centrum EventHub|Lokalizacja|
|EventHubSuccessfulEvents|Pomyślne zdarzenia EventHub|Licznik|Łącznie|Liczba pomyślnych zdarzeń EventHub|Lokalizacja|
|EventHubTotalFailedEvents|Niepowodzenie zdarzeń EventHub|Licznik|Łącznie|Liczba zakończonych niepowodzeniem zdarzeń EventHub|Lokalizacja|
|EventHubRejectedEvents|Odrzucone zdarzenia EventHub|Licznik|Łącznie|Liczba odrzuconych zdarzeń EventHub (niewłaściwa konfiguracja lub nieautoryzowana)|Lokalizacja|
|EventHubThrottledEvents|Ograniczone zdarzenia EventHub|Licznik|Łącznie|Liczba zdarzeń zdarzenia EventHub z ograniczeniami|Lokalizacja|
|EventHubTimedoutEvents|Przekroczono limit czasu zdarzeń EventHub|Licznik|Łącznie|Liczba przekroczeń limitu czasu zdarzeń EventHub|Lokalizacja|
|EventHubDroppedEvents|Opuszczone zdarzenia EventHub|Licznik|Łącznie|Liczba pominiętych zdarzeń z powodu osiągnięcia limitu rozmiaru kolejki|Lokalizacja|
|EventHubTotalBytesSent|Rozmiar zdarzeń EventHub|Bajty|Łącznie|Łączny rozmiar zdarzeń EventHub w bajtach|Lokalizacja|
|Żądania|Żądania|Licznik|Łącznie|Metryki żądania bramy z wieloma wymiarami|Location, hostname, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Licznik|Licznik|Łączna liczba przychodzących żądań HTTP.|None|
|FailedHttpRequestCount|FailedHttpRequestCount|Licznik|Licznik|Nieudane żądania HTTP.|None|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Licznik|Średnia|Opóźnienie dla żądania HTTP.|None|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/Sprężyna

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SystemCpuUsagePercentage|Procent użycia procesora systemu|Procent|Średnia|Ostatnie użycie procesora CPU całego systemu|Nazwa_aplikacji, pod|
|AppCpuUsagePercentage|Procent użycia procesora aplikacji|Procent|Średnia|Procent użycia procesora CPU JVM aplikacji|Nazwa_aplikacji, pod|
|AppMemoryCommitted|Przypisana pamięć aplikacji|Bajty|Średnia|Pamięć przypisana do JVM w bajtach|Nazwa_aplikacji, pod|
|AppMemoryUsed|Użyta pamięć aplikacji|Bajty|Średnia|Pamięć aplikacji użyta w bajtach|Nazwa_aplikacji, pod|
|AppMemoryMax|Maksymalna ilość pamięci aplikacji|Bajty|Maksimum|Maksymalna ilość pamięci w bajtach, która może być używana do zarządzania pamięcią|Nazwa_aplikacji, pod|
|MaxOldGenMemoryPoolBytes|Maksymalna dostępna wartość starego rozmiaru danych generacji|Bajty|Średnia|Maksymalny rozmiar puli pamięci starej generacji|Nazwa_aplikacji, pod|
|OldGenMemoryPoolBytes|Stary rozmiar danych generacji|Bajty|Średnia|Rozmiar starej puli pamięci generacji po pełnej operacji GC|Nazwa_aplikacji, pod|
|OldGenPromotedBytes|Podwyższ poziom do starego rozmiaru danych generacji|Bajty|Maksimum|Liczba pozytywnych wzrostów rozmiaru starej puli pamięci generacji przed GC do po GC|Nazwa_aplikacji, pod|
|YoungGenPromotedBytes|Podwyższ poziom do rozmiaru danych dla małych generacji|Bajty|Maksimum|Zwiększa się w celu zwiększenia rozmiaru puli pamięci młodej generacji po jednej operacji GC do wcześniejszej|Nazwa_aplikacji, pod|
|GCPauseTotalCount|Liczba wstrzymań GC|Licznik|Łącznie|Liczba wstrzymań GC|Nazwa_aplikacji, pod|
|GCPauseTotalTime|Całkowity czas wstrzymania odzyskiwania pamięci|Milisekundy|Łącznie|Całkowity czas wstrzymania odzyskiwania pamięci|Nazwa_aplikacji, pod|
|TomcatSentBytes|Całkowita liczba wysłanych bajtów Tomcat|Bajty|Łącznie|Całkowita liczba wysłanych bajtów Tomcat|Nazwa_aplikacji, pod|
|TomcatReceivedBytes|Całkowita liczba odebranych bajtów Tomcat|Bajty|Łącznie|Całkowita liczba odebranych bajtów Tomcat|Nazwa_aplikacji, pod|
|TomcatRequestTotalTime|Łączny czas żądania Tomcat|Milisekundy|Łącznie|Łączny czas żądania Tomcat|Nazwa_aplikacji, pod|
|TomcatRequestTotalCount|Łączna liczba żądań Tomcat|Licznik|Łącznie|Łączna liczba żądań Tomcat|Nazwa_aplikacji, pod|
|TomcatResponseAvgTime|Średni czas żądania Tomcat|Milisekundy|Średnia|Średni czas żądania Tomcat|Nazwa_aplikacji, pod|
|TomcatRequestMaxTime|Maksymalny czas żądania Tomcat|Milisekundy|Maksimum|Maksymalny czas żądania Tomcat|Nazwa_aplikacji, pod|
|TomcatErrorCount|Błąd globalny Tomcat|Licznik|Łącznie|Błąd globalny Tomcat|Nazwa_aplikacji, pod|
|TomcatSessionActiveMaxCount|Maksymalna liczba aktywnych sesji Tomcat|Licznik|Łącznie|Maksymalna liczba aktywnych sesji Tomcat|Nazwa_aplikacji, pod|
|TomcatSessionAliveMaxTime|Maksymalny czas aktywności sesji Tomcat|Milisekundy|Maksimum|Maksymalny czas aktywności sesji Tomcat|Nazwa_aplikacji, pod|
|TomcatSessionActiveCurrentCount|Liczba aktywności sesji Tomcat|Licznik|Łącznie|Liczba aktywności sesji Tomcat|Nazwa_aplikacji, pod|
|TomcatSessionCreatedCount|Liczba utworzonych sesji Tomcat|Licznik|Łącznie|Liczba utworzonych sesji Tomcat|Nazwa_aplikacji, pod|
|TomcatSessionExpiredCount|Liczba wygasłych sesji Tomcat|Licznik|Łącznie|Liczba wygasłych sesji Tomcat|Nazwa_aplikacji, pod|
|TomcatSessionRejectedCount|Liczba odrzuconych sesji Tomcat|Licznik|Łącznie|Liczba odrzuconych sesji Tomcat|Nazwa_aplikacji, pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalJob|Łączna liczba zadań|Licznik|Łącznie|Całkowita liczba zadań|Element Runbook, stan|
|TotalUpdateDeploymentRuns|Łączna liczba przebiegów wdrożenia aktualizacji|Licznik|Łącznie|Łączna liczba przebiegów wdrażania aktualizacji oprogramowania|. Softwareupdateconfigurationname, stan|
|TotalUpdateDeploymentMachineRuns|Łączna liczba uruchomień maszyn wdrożenia aktualizacji|Licznik|Łącznie|Łączna liczba uruchomień maszyn wdrożenia aktualizacji oprogramowania w ramach wdrożenia aktualizacji oprogramowania|. Softwareupdateconfigurationname, status, TargetComputer,. Softwareupdateconfigurationrunid|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CoreCount|Liczba dedykowanych rdzeni|Licznik|Łącznie|Łączna liczba rdzeni dedykowanych na koncie wsadowym|None|
|TotalNodeCount|Liczba dedykowanych węzłów|Licznik|Łącznie|Łączna liczba węzłów dedykowanych na koncie wsadowym|None|
|LowPriorityCoreCount|LowPriority rdzeń|Licznik|Łącznie|Łączna liczba rdzeni o niskim priorytecie na koncie wsadowym|None|
|TotalLowPriorityNodeCount|Liczba węzłów o niskim priorytecie|Licznik|Łącznie|Łączna liczba węzłów o niskim priorytecie na koncie wsadowym|None|
|CreatingNodeCount|Tworzenie liczby węzłów|Licznik|Łącznie|Liczba tworzonych węzłów|None|
|StartingNodeCount|Początkowa liczba węzłów|Licznik|Łącznie|Liczba początkowych węzłów|None|
|WaitingForStartTaskNodeCount|Oczekiwanie na liczbę węzłów zadania uruchamiania|Licznik|Łącznie|Liczba węzłów oczekujących na zakończenie zadania uruchamiania|None|
|StartTaskFailedNodeCount|Uruchamianie zadania nie powiodło się liczba węzłów|Licznik|Łącznie|Liczba węzłów, w których zadanie uruchomieniowe nie powiodło się|None|
|IdleNodeCount|Liczba węzłów bezczynności|Licznik|Łącznie|Liczba bezczynnych węzłów|None|
|OfflineNodeCount|Liczba węzłów w trybie offline|Licznik|Łącznie|Liczba węzłów offline|None|
|RebootingNodeCount|Ponowny rozruch liczby węzłów|Licznik|Łącznie|Liczba ponownych uruchomień węzłów|None|
|ReimagingNodeCount|Liczba węzłów regraficznych|Licznik|Łącznie|Liczba węzłów regraficznych|None|
|RunningNodeCount|Liczba uruchomionych węzłów|Licznik|Łącznie|Liczba uruchomionych węzłów|None|
|LeavingPoolNodeCount|Opuszczanie liczby węzłów puli|Licznik|Łącznie|Liczba węzłów opuszczających pulę|None|
|UnusableNodeCount|Liczba węzłów, których nie można użyć|Licznik|Łącznie|Liczba nieużywanych węzłów|None|
|PreemptedNodeCount|Liczba przeniesiona węzłów|Licznik|Łącznie|Liczba przeniesiona węzłów|None|
|TaskStartEvent|Zdarzenia uruchamiania zadania|Licznik|Łącznie|Łączna liczba rozpoczętych zadań|None|
|TaskCompleteEvent|Zdarzenia ukończenia zadania|Licznik|Łącznie|Całkowita liczba ukończonych zadań|None|
|TaskFailEvent|Zdarzenia błędów zadań|Licznik|Łącznie|Łączna liczba zadań zakończonych niepowodzeniem w stanie niepowodzenia|None|
|PoolCreateEvent|Zdarzenia tworzenia puli|Licznik|Łącznie|Łączna liczba utworzonych pul|None|
|PoolResizeStartEvent|Zdarzenia rozpoczęcia zmiany rozmiaru puli|Licznik|Łącznie|Łączna liczba rozpoczętych rozmiarów puli|None|
|PoolResizeCompleteEvent|Zdarzenia ukończenia zmiany rozmiaru puli|Licznik|Łącznie|Łączna liczba zakończono zmienionych rozmiarów puli|None|
|PoolDeleteStartEvent|Zdarzenia uruchamiania usuwania puli|Licznik|Łącznie|Łączna liczba uruchomionych usunięć pul|None|
|PoolDeleteCompleteEvent|Zdarzenia ukończenia usuwania puli|Licznik|Łącznie|Łączna liczba ukończonych usunięć pul|None|
|JobDeleteCompleteEvent|Zdarzenia ukończenia usuwania zadania|Licznik|Łącznie|Całkowita liczba pomyślnie usuniętych zadań.|None|
|JobDeleteStartEvent|Zdarzenia rozpoczęcia usuwania zadania|Licznik|Łącznie|Łączna liczba zadań, które zostały zażądane do usunięcia.|None|
|JobDisableCompleteEvent|Zdarzenia ukończenia wyłączania zadania|Licznik|Łącznie|Łączna liczba zadań, które zostały pomyślnie wyłączone.|None|
|JobDisableStartEvent|Wyłącz zdarzenia uruchamiania dla zadania|Licznik|Łącznie|Łączna liczba zadań, które zostały zlecone do wyłączenia.|None|
|JobStartEvent|Zdarzenia uruchamiania zadania|Licznik|Łącznie|Łączna liczba zadań, które zostały pomyślnie uruchomione.|None|
|JobTerminateCompleteEvent|Zdarzenia ukończenia kończenia zadania|Licznik|Łącznie|Łączna liczba zadań zakończonych pomyślnie.|None|
|JobTerminateStartEvent|Zdarzenia rozpoczęcia zakończenia zadania|Licznik|Łącznie|Całkowita liczba zadań, które zostały zażądane do zakończenia.|None|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft. Batchai Job/obszary robocze

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Przesłane zadanie|Przesłane zadanie|Licznik|Łącznie|Liczba przesłanych zadań|Scenariusz, ClusterName|
|Ukończono zadanie|Ukończono zadanie|Licznik|Łącznie|Liczba ukończonych zadań|Scenariusz, ClusterName, ResultType|
|Łączna liczba węzłów|Łączna liczba węzłów|Licznik|Średnia|Łączna liczba węzłów|Scenariusz, ClusterName|
|Aktywne węzły|Aktywne węzły|Licznik|Średnia|Liczba uruchomionych węzłów|Scenariusz, ClusterName|
|Węzły bezczynne|Węzły bezczynne|Licznik|Średnia|Liczba bezczynnych węzłów|Scenariusz, ClusterName|
|Węzły niezdatne do użytku|Węzły niezdatne do użytku|Licznik|Średnia|Liczba nieużywanych węzłów|Scenariusz, ClusterName|
|Zastępujące węzły|Zastępujące węzły|Licznik|Średnia|Liczba przeniesiona węzłów|Scenariusz, ClusterName|
|Opuszczanie węzłów|Opuszczanie węzłów|Licznik|Średnia|Liczba wychodzących węzłów|Scenariusz, ClusterName|
|Łączna liczba rdzeni|Łączna liczba rdzeni|Licznik|Średnia|Łączna liczba rdzeni|Scenariusz, ClusterName|
|Aktywne rdzenie|Aktywne rdzenie|Licznik|Średnia|Liczba aktywnych rdzeni|Scenariusz, ClusterName|
|Rdzenie bezczynne|Rdzenie bezczynne|Licznik|Średnia|Liczba rdzeni bezczynnych|Scenariusz, ClusterName|
|Rdzenie, których nie można używać|Rdzenie, których nie można używać|Licznik|Średnia|Liczba rdzeni, których nie można używać|Scenariusz, ClusterName|
|Występujące rdzenie|Występujące rdzenie|Licznik|Średnia|Liczba przeniesiona rdzeni|Scenariusz, ClusterName|
|Opuszczanie rdzeni|Opuszczanie rdzeni|Licznik|Średnia|Liczba rdzeni wychodzących|Scenariusz, ClusterName|
|Procent wykorzystania przydziałów|Procent wykorzystania przydziałów|Licznik|Średnia|Procent wykorzystania przydziałów|Scenariusz, ClusterName, VmFamilyName, VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. łańcucha bloków/blockchainMembers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|Procent użycia procesora CPU|Procent|Maksimum|Procent użycia procesora CPU|Węzeł|
|MemoryUsage|Użycie pamięci|Bajty|Średnia|Użycie pamięci|Węzeł|
|MemoryLimit|Limit pamięci|Bajty|Średnia|Limit pamięci|Węzeł|
|MemoryUsagePercentageInDouble|Procent użycia pamięci|Procent|Średnia|Procent użycia pamięci|Węzeł|
|StorageUsage|Użycie magazynu|Bajty|Średnia|Użycie magazynu|Węzeł|
|IOReadBytes|Bajty odczytu we/wy|Bajty|Łącznie|Bajty odczytu we/wy|Węzeł|
|IOWriteBytes|Bajty zapisu we/wy|Bajty|Łącznie|Bajty zapisu we/wy|Węzeł|
|ConnectionAccepted|Zaakceptowane połączenia|Licznik|Łącznie|Zaakceptowane połączenia|Węzeł|
|ConnectionHandled|Obsłużone połączenia|Licznik|Łącznie|Obsłużone połączenia|Węzeł|
|ConnectionActive|Aktywne połączenia|Licznik|Średnia|Aktywne połączenia|Węzeł|
|RequestHandled|Obsłużone żądania|Licznik|Łącznie|Obsłużone żądania|Węzeł|
|ProcessedBlocks|Przetworzone bloki|Licznik|Łącznie|Przetworzone bloki|Węzeł|
|ProcessedTransactions|Przetworzone transakcje|Licznik|Łącznie|Przetworzone transakcje|Węzeł|
|PendingTransactions|Oczekujące transakcje|Licznik|Średnia|Oczekujące transakcje|Węzeł|
|QueuedTransactions|Transakcje w kolejce|Licznik|Średnia|Transakcje w kolejce|Węzeł|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|connectedclients|Podłączeni klienci|Licznik|Maksimum||ShardId|
|totalcommandsprocessed|Łączna liczba operacji|Licznik|Łącznie||ShardId|
|cachehits|Trafienia pamięci podręcznej|Licznik|Łącznie||ShardId|
|cachemisses|Chybienia w pamięci podręcznej|Licznik|Łącznie||ShardId|
|cachemissrate|Współczynnik chybień w pamięci podręcznej|Procent|cachemissrate||ShardId|
|GetCommands|Kto|Licznik|Łącznie||ShardId|
|SetCommands|Przywraca|Licznik|Łącznie||ShardId|
|operationsPerSecond|Liczba operacji na sekundę|Licznik|Maksimum||ShardId|
|evictedkeys|Wykluczone klucze|Licznik|Łącznie||ShardId|
|totalkeys|Łączna liczba kluczy|Licznik|Maksimum||ShardId|
|expiredkeys|Wygasłe klucze|Licznik|Łącznie||ShardId|
|usedmemory|Używana pamięć|Bajty|Maksimum||ShardId|
|usedmemorypercentage|Wartość procentowa używanej pamięci|Procent|Maksimum||ShardId|
|usedmemoryRss|Używana pamięć RSS|Bajty|Maksimum||ShardId|
|serverLoad|Obciążenie serwera|Procent|Maksimum||ShardId|
|cacheWrite|Zapis pamięci podręcznej|BytesPerSecond|Maksimum||ShardId|
|cacheRead|Odczyt pamięci podręcznej|BytesPerSecond|Maksimum||ShardId|
|percentProcessorTime|Procesor CPU|Procent|Maksimum||ShardId|
|cacheLatency|Mikrosekundy opóźnienia pamięci podręcznej (wersja zapoznawcza)|Licznik|Średnia||ShardId|
|błędy|Błędy|Licznik|Maksimum||ShardId, ErrorType|
|connectedclients0|Połączeni klienci (fragmentu 0)|Licznik|Maksimum||None|
|totalcommandsprocessed0|Łączna liczba operacji (fragmentu 0)|Licznik|Łącznie||None|
|cachehits0|Trafienia pamięci podręcznej (fragmentu 0)|Licznik|Łącznie||None|
|cachemisses0|Chybienia w pamięci podręcznej (fragmentu 0)|Licznik|Łącznie||None|
|getcommands0|Pobiera (fragmentu 0)|Licznik|Łącznie||None|
|setcommands0|Zestawy (fragmentu 0)|Licznik|Łącznie||None|
|operationsPerSecond0|Operacje na sekundę (fragmentu 0)|Licznik|Maksimum||None|
|evictedkeys0|Wykluczone klucze (fragmentu 0)|Licznik|Łącznie||None|
|totalkeys0|Łączna liczba kluczy (fragmentu 0)|Licznik|Maksimum||None|
|expiredkeys0|Wygasłe klucze (fragmentu 0)|Licznik|Łącznie||None|
|usedmemory0|Używana pamięć (fragmentu 0)|Bajty|Maksimum||None|
|usedmemoryRss0|Używana pamięć RSS (fragmentu 0)|Bajty|Maksimum||None|
|serverLoad0|Ładowanie serwera (fragmentu 0)|Procent|Maksimum||None|
|cacheWrite0|Zapis pamięci podręcznej (fragmentu 0)|BytesPerSecond|Maksimum||None|
|cacheRead0|Odczyt pamięci podręcznej (fragmentu 0)|BytesPerSecond|Maksimum||None|
|percentProcessorTime0|Procesor CPU (fragmentu 0)|Procent|Maksimum||None|
|connectedclients1|Połączeni klienci (fragmentu 1)|Licznik|Maksimum||None|
|totalcommandsprocessed1|Łączna liczba operacji (fragmentu 1)|Licznik|Łącznie||None|
|cachehits1|Trafienia pamięci podręcznej (fragmentu 1)|Licznik|Łącznie||None|
|cachemisses1|Chybienia w pamięci podręcznej (fragmentu 1)|Licznik|Łącznie||None|
|getcommands1|Pobiera (fragmentu 1)|Licznik|Łącznie||None|
|setcommands1|Zestawy (fragmentu 1)|Licznik|Łącznie||None|
|operationsPerSecond1|Operacje na sekundę (fragmentu 1)|Licznik|Maksimum||None|
|evictedkeys1|Wykluczone klucze (fragmentu 1)|Licznik|Łącznie||None|
|totalkeys1|Łączna liczba kluczy (fragmentu 1)|Licznik|Maksimum||None|
|expiredkeys1|Wygasłe klucze (fragmentu 1)|Licznik|Łącznie||None|
|usedmemory1|Używana pamięć (fragmentu 1)|Bajty|Maksimum||None|
|usedmemoryRss1|Używana pamięć RSS (fragmentu 1)|Bajty|Maksimum||None|
|serverLoad1|Ładowanie serwera (fragmentu 1)|Procent|Maksimum||None|
|cacheWrite1|Zapisywanie pamięci podręcznej (fragmentu 1)|BytesPerSecond|Maksimum||None|
|cacheRead1|Odczyt pamięci podręcznej (fragmentu 1)|BytesPerSecond|Maksimum||None|
|percentProcessorTime1|Procesor CPU (fragmentu 1)|Procent|Maksimum||None|
|connectedclients2|Połączeni klienci (fragmentu 2)|Licznik|Maksimum||None|
|totalcommandsprocessed2|Łączna liczba operacji (fragmentu 2)|Licznik|Łącznie||None|
|cachehits2|Trafienia pamięci podręcznej (fragmentu 2)|Licznik|Łącznie||None|
|cachemisses2|Chybienia w pamięci podręcznej (fragmentu 2)|Licznik|Łącznie||None|
|getcommands2|Pobiera (fragmentu 2)|Licznik|Łącznie||None|
|setcommands2|Zestawy (fragmentu 2)|Licznik|Łącznie||None|
|operationsPerSecond2|Operacje na sekundę (fragmentu 2)|Licznik|Maksimum||None|
|evictedkeys2|Wykluczone klucze (fragmentu 2)|Licznik|Łącznie||None|
|totalkeys2|Łączna liczba kluczy (fragmentu 2)|Licznik|Maksimum||None|
|expiredkeys2|Wygasłe klucze (fragmentu 2)|Licznik|Łącznie||None|
|usedmemory2|Używana pamięć (fragmentu 2)|Bajty|Maksimum||None|
|usedmemoryRss2|Używana pamięć RSS (fragmentu 2)|Bajty|Maksimum||None|
|serverLoad2|Ładowanie serwera (fragmentu 2)|Procent|Maksimum||None|
|cacheWrite2|Zapisywanie pamięci podręcznej (fragmentu 2)|BytesPerSecond|Maksimum||None|
|cacheRead2|Odczyt pamięci podręcznej (fragmentu 2)|BytesPerSecond|Maksimum||None|
|percentProcessorTime2|Procesor CPU (fragmentu 2)|Procent|Maksimum||None|
|connectedclients3|Połączeni klienci (fragmentu 3)|Licznik|Maksimum||None|
|totalcommandsprocessed3|Łączna liczba operacji (fragmentu 3)|Licznik|Łącznie||None|
|cachehits3|Trafienia pamięci podręcznej (fragmentu 3)|Licznik|Łącznie||None|
|cachemisses3|Chybienia w pamięci podręcznej (fragmentu 3)|Licznik|Łącznie||None|
|getcommands3|Pobiera (fragmentu 3)|Licznik|Łącznie||None|
|setcommands3|Zestawy (fragmentu 3)|Licznik|Łącznie||None|
|operationsPerSecond3|Operacje na sekundę (fragmentu 3)|Licznik|Maksimum||None|
|evictedkeys3|Wykluczone klucze (fragmentu 3)|Licznik|Łącznie||None|
|totalkeys3|Łączna liczba kluczy (fragmentu 3)|Licznik|Maksimum||None|
|expiredkeys3|Wygasłe klucze (fragmentu 3)|Licznik|Łącznie||None|
|usedmemory3|Używana pamięć (fragmentu 3)|Bajty|Maksimum||None|
|usedmemoryRss3|Używana pamięć RSS (fragmentu 3)|Bajty|Maksimum||None|
|serverLoad3|Ładowanie serwera (fragmentu 3)|Procent|Maksimum||None|
|cacheWrite3|Zapis w pamięci podręcznej (fragmentu 3)|BytesPerSecond|Maksimum||None|
|cacheRead3|Odczyt pamięci podręcznej (fragmentu 3)|BytesPerSecond|Maksimum||None|
|percentProcessorTime3|Procesor CPU (fragmentu 3)|Procent|Maksimum||None|
|connectedclients4|Połączeni klienci (fragmentu 4)|Licznik|Maksimum||None|
|totalcommandsprocessed4|Łączna liczba operacji (fragmentu 4)|Licznik|Łącznie||None|
|cachehits4|Trafienia pamięci podręcznej (fragmentu 4)|Licznik|Łącznie||None|
|cachemisses4|Chybienia w pamięci podręcznej (fragmentu 4)|Licznik|Łącznie||None|
|getcommands4|Pobiera (fragmentu 4)|Licznik|Łącznie||None|
|setcommands4|Zestawy (fragmentu 4)|Licznik|Łącznie||None|
|operationsPerSecond4|Operacje na sekundę (fragmentu 4)|Licznik|Maksimum||None|
|evictedkeys4|Wykluczone klucze (fragmentu 4)|Licznik|Łącznie||None|
|totalkeys4|Łączna liczba kluczy (fragmentu 4)|Licznik|Maksimum||None|
|expiredkeys4|Wygasłe klucze (fragmentu 4)|Licznik|Łącznie||None|
|usedmemory4|Używana pamięć (fragmentu 4)|Bajty|Maksimum||None|
|usedmemoryRss4|Używana pamięć RSS (fragmentu 4)|Bajty|Maksimum||None|
|serverLoad4|Ładowanie serwera (fragmentu 4)|Procent|Maksimum||None|
|cacheWrite4|Zapis pamięci podręcznej (fragmentu 4)|BytesPerSecond|Maksimum||None|
|cacheRead4|Odczyt pamięci podręcznej (fragmentu 4)|BytesPerSecond|Maksimum||None|
|percentProcessorTime4|Procesor CPU (fragmentu 4)|Procent|Maksimum||None|
|connectedclients5|Połączeni klienci (fragmentu 5)|Licznik|Maksimum||None|
|totalcommandsprocessed5|Łączna liczba operacji (fragmentu 5)|Licznik|Łącznie||None|
|cachehits5|Trafienia pamięci podręcznej (fragmentu 5)|Licznik|Łącznie||None|
|cachemisses5|Chybienia w pamięci podręcznej (fragmentu 5)|Licznik|Łącznie||None|
|getcommands5|Pobiera (fragmentu 5)|Licznik|Łącznie||None|
|setcommands5|Zestawy (fragmentu 5)|Licznik|Łącznie||None|
|operationsPerSecond5|Operacje na sekundę (fragmentu 5)|Licznik|Maksimum||None|
|evictedkeys5|Wykluczone klucze (fragmentu 5)|Licznik|Łącznie||None|
|totalkeys5|Łączna liczba kluczy (fragmentu 5)|Licznik|Maksimum||None|
|expiredkeys5|Wygasłe klucze (fragmentu 5)|Licznik|Łącznie||None|
|usedmemory5|Używana pamięć (fragmentu 5)|Bajty|Maksimum||None|
|usedmemoryRss5|Używana pamięć RSS (fragmentu 5)|Bajty|Maksimum||None|
|serverLoad5|Ładowanie serwera (fragmentu 5)|Procent|Maksimum||None|
|cacheWrite5|Zapis w pamięci podręcznej (fragmentu 5)|BytesPerSecond|Maksimum||None|
|cacheRead5|Odczyt pamięci podręcznej (fragmentu 5)|BytesPerSecond|Maksimum||None|
|percentProcessorTime5|Procesor CPU (fragmentu 5)|Procent|Maksimum||None|
|connectedclients6|Połączeni klienci (fragmentu 6)|Licznik|Maksimum||None|
|totalcommandsprocessed6|Łączna liczba operacji (fragmentu 6)|Licznik|Łącznie||None|
|cachehits6|Trafienia pamięci podręcznej (fragmentu 6)|Licznik|Łącznie||None|
|cachemisses6|Chybienia w pamięci podręcznej (fragmentu 6)|Licznik|Łącznie||None|
|getcommands6|Pobiera (fragmentu 6)|Licznik|Łącznie||None|
|setcommands6|Zestawy (fragmentu 6)|Licznik|Łącznie||None|
|operationsPerSecond6|Operacje na sekundę (fragmentu 6)|Licznik|Maksimum||None|
|evictedkeys6|Wykluczone klucze (fragmentu 6)|Licznik|Łącznie||None|
|totalkeys6|Łączna liczba kluczy (fragmentu 6)|Licznik|Maksimum||None|
|expiredkeys6|Wygasłe klucze (fragmentu 6)|Licznik|Łącznie||None|
|usedmemory6|Używana pamięć (fragmentu 6)|Bajty|Maksimum||None|
|usedmemoryRss6|Używana pamięć RSS (fragmentu 6)|Bajty|Maksimum||None|
|serverLoad6|Ładowanie serwera (fragmentu 6)|Procent|Maksimum||None|
|cacheWrite6|Zapis w pamięci podręcznej (fragmentu 6)|BytesPerSecond|Maksimum||None|
|cacheRead6|Odczyt pamięci podręcznej (fragmentu 6)|BytesPerSecond|Maksimum||None|
|percentProcessorTime6|Procesor CPU (fragmentu 6)|Procent|Maksimum||None|
|connectedclients7|Połączeni klienci (fragmentu 7)|Licznik|Maksimum||None|
|totalcommandsprocessed7|Łączna liczba operacji (fragmentu 7)|Licznik|Łącznie||None|
|cachehits7|Trafienia pamięci podręcznej (fragmentu 7)|Licznik|Łącznie||None|
|cachemisses7|Chybienia w pamięci podręcznej (fragmentu 7)|Licznik|Łącznie||None|
|getcommands7|Pobiera (fragmentu 7)|Licznik|Łącznie||None|
|setcommands7|Zestawy (fragmentu 7)|Licznik|Łącznie||None|
|operationsPerSecond7|Operacje na sekundę (fragmentu 7)|Licznik|Maksimum||None|
|evictedkeys7|Wykluczone klucze (fragmentu 7)|Licznik|Łącznie||None|
|totalkeys7|Łączna liczba kluczy (fragmentu 7)|Licznik|Maksimum||None|
|expiredkeys7|Wygasłe klucze (fragmentu 7)|Licznik|Łącznie||None|
|usedmemory7|Używana pamięć (fragmentu 7)|Bajty|Maksimum||None|
|usedmemoryRss7|Używana pamięć RSS (fragmentu 7)|Bajty|Maksimum||None|
|serverLoad7|Ładowanie serwera (fragmentu 7)|Procent|Maksimum||None|
|cacheWrite7|Zapis w pamięci podręcznej (fragmentu 7)|BytesPerSecond|Maksimum||None|
|cacheRead7|Odczyt pamięci podręcznej (fragmentu 7)|BytesPerSecond|Maksimum||None|
|percentProcessorTime7|Procesor CPU (fragmentu 7)|Procent|Maksimum||None|
|connectedclients8|Połączeni klienci (fragmentu 8)|Licznik|Maksimum||None|
|totalcommandsprocessed8|Łączna liczba operacji (fragmentu 8)|Licznik|Łącznie||None|
|cachehits8|Trafienia pamięci podręcznej (fragmentu 8)|Licznik|Łącznie||None|
|cachemisses8|Chybienia w pamięci podręcznej (fragmentu 8)|Licznik|Łącznie||None|
|getcommands8|Pobiera (fragmentu 8)|Licznik|Łącznie||None|
|setcommands8|Zestawy (fragmentu 8)|Licznik|Łącznie||None|
|operationsPerSecond8|Operacje na sekundę (fragmentu 8)|Licznik|Maksimum||None|
|evictedkeys8|Wykluczone klucze (fragmentu 8)|Licznik|Łącznie||None|
|totalkeys8|Łączna liczba kluczy (fragmentu 8)|Licznik|Maksimum||None|
|expiredkeys8|Wygasłe klucze (fragmentu 8)|Licznik|Łącznie||None|
|usedmemory8|Używana pamięć (fragmentu 8)|Bajty|Maksimum||None|
|usedmemoryRss8|Używana pamięć RSS (fragmentu 8)|Bajty|Maksimum||None|
|serverLoad8|Ładowanie serwera (fragmentu 8)|Procent|Maksimum||None|
|cacheWrite8|Zapis w pamięci podręcznej (fragmentu 8)|BytesPerSecond|Maksimum||None|
|cacheRead8|Odczyt pamięci podręcznej (fragmentu 8)|BytesPerSecond|Maksimum||None|
|percentProcessorTime8|Procesor CPU (fragmentu 8)|Procent|Maksimum||None|
|connectedclients9|Połączeni klienci (fragmentu 9)|Licznik|Maksimum||None|
|totalcommandsprocessed9|Łączna liczba operacji (fragmentu 9)|Licznik|Łącznie||None|
|cachehits9|Trafienia pamięci podręcznej (fragmentu 9)|Licznik|Łącznie||None|
|cachemisses9|Chybienia w pamięci podręcznej (fragmentu 9)|Licznik|Łącznie||None|
|getcommands9|Pobiera (fragmentu 9)|Licznik|Łącznie||None|
|setcommands9|Zestawy (fragmentu 9)|Licznik|Łącznie||None|
|operationsPerSecond9|Operacje na sekundę (fragmentu 9)|Licznik|Maksimum||None|
|evictedkeys9|Wykluczone klucze (fragmentu 9)|Licznik|Łącznie||None|
|totalkeys9|Łączna liczba kluczy (fragmentu 9)|Licznik|Maksimum||None|
|expiredkeys9|Wygasłe klucze (fragmentu 9)|Licznik|Łącznie||None|
|usedmemory9|Używana pamięć (fragmentu 9)|Bajty|Maksimum||None|
|usedmemoryRss9|Używana pamięć RSS (fragmentu 9)|Bajty|Maksimum||None|
|serverLoad9|Ładowanie serwera (fragmentu 9)|Procent|Maksimum||None|
|cacheWrite9|Zapis w pamięci podręcznej (fragmentu 9)|BytesPerSecond|Maksimum||None|
|cacheRead9|Odczyt pamięci podręcznej (fragmentu 9)|BytesPerSecond|Maksimum||None|
|percentProcessorTime9|Procesor CPU (fragmentu 9)|Procent|Maksimum||None|



## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Liczba żądań zapory aplikacji sieci Web|Licznik|Łącznie|Liczba żądań klientów przetworzonych przez zaporę aplikacji sieci Web|PolicyName, RuleName, Akcja|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Średnia|Wartość procentowa przyznanych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne.|None|
|Sieć — wejście|Sieć — wejście|Bajty|Łącznie|Liczba bajtów odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący).|None|
|Sieć — wyjście|Sieć — wyjście|Bajty|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący).|None|
|Bajty odczytu dysku/s|Odczyt dysku|BytesPerSecond|Średnia|Średnia liczba bajtów odczytanych z dysku w okresie monitorowania.|None|
|Bajty zapisu dysku/s|Zapis na dysku|BytesPerSecond|Średnia|Średnia liczba bajtów zapisywana na dysku w okresie monitorowania.|None|
|Operacje odczytu z dysku/s|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Operacje we/wy odczytu na dysku.|None|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Liczba operacji we/wy zapisu na dysku.|None|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Średnia|Wartość procentowa przyznanych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne.|RoleInstanceId|
|Sieć — wejście|Sieć — wejście|Bajty|Łącznie|Liczba bajtów odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący).|RoleInstanceId|
|Sieć — wyjście|Sieć — wyjście|Bajty|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący).|RoleInstanceId|
|Bajty odczytu dysku/s|Odczyt dysku|BytesPerSecond|Średnia|Średnia liczba bajtów odczytanych z dysku w okresie monitorowania.|RoleInstanceId|
|Bajty zapisu dysku/s|Zapis na dysku|BytesPerSecond|Średnia|Średnia liczba bajtów zapisywana na dysku w okresie monitorowania.|RoleInstanceId|
|Operacje odczytu z dysku/s|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Operacje we/wy odczytu na dysku.|RoleInstanceId|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Liczba operacji we/wy zapisu na dysku.|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft. ClassicStorage/storageAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|UsedCapacity|Używana pojemność|Bajty|Średnia|Używana pojemność konta|None|
|Transakcje|Transakcje|Licznik|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|Milisekundy|Średnia|Opóźnienie używane przez usługę Azure Storage do przetworzenia pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie e2e dla powodzenia|Milisekundy|Średnia|Kompleksowe opóźnienie pomyślnych żądań kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft. ClassicStorage/storageAccounts/blobServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BlobCapacity|Pojemność obiektu blob|Bajty|Średnia|Ilość miejsca używanego przez usługę obiektów Blob konta magazynu, w bajtach.|Blobtype, warstwa|
|BlobCount|Liczba obiektów blob|Licznik|Średnia|Liczba obiektów Blob w usłudze obiektów Blob konta magazynu.|Blobtype, warstwa|
|ContainerCount|Liczba kontenerów obiektów blob|Licznik|Średnia|Liczba kontenerów w usłudze obiektów Blob konta magazynu.|None|
|IndexCapacity|Pojemność indeksu|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez ADLS Gen2 (hierarchiczny) indeks w bajtach.|None|
|Transakcje|Transakcje|Licznik|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|Milisekundy|Średnia|Opóźnienie używane przez usługę Azure Storage do przetworzenia pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie e2e dla powodzenia|Milisekundy|Średnia|Kompleksowe opóźnienie pomyślnych żądań kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft. ClassicStorage/storageAccounts/tableServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TableCapacity|Pojemność tabeli|Bajty|Średnia|Ilość miejsca używanego przez usługę tabeli konta magazynu, w bajtach.|None|
|TableCount|Liczba tabel|Licznik|Średnia|Liczba tabel w usłudze tabeli konta magazynu.|None|
|TableEntityCount|Liczba jednostek tabel|Licznik|Średnia|Liczba jednostek tabel w usłudze tabeli konta magazynu.|None|
|Transakcje|Transakcje|Licznik|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|Milisekundy|Średnia|Opóźnienie używane przez usługę Azure Storage do przetworzenia pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie e2e dla powodzenia|Milisekundy|Średnia|Kompleksowe opóźnienie pomyślnych żądań kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft. ClassicStorage/storageAccounts/fileServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|FileCapacity|Pojemność plików|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez usługę plików konta magazynu w bajtach.|Udziału|
|FileCount|Liczba plików|Licznik|Średnia|Liczba plików w usłudze plików konta magazynu.|Udziału|
|FileShareCount|Liczba udziałów plików|Licznik|Średnia|Liczba udziałów plików w usłudze plików konta magazynu.|None|
|FileShareSnapshotCount|Liczba migawek udziału plików|Licznik|Średnia|Liczba migawek znajdujących się w udziale w usłudze plików konta magazynu.|Udziału|
|FileShareSnapshotSize|Rozmiar migawki udziału plików|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez migawki w usłudze plików konta magazynu w bajtach.|Udziału|
|FileShareQuota|Rozmiar przydziału udziału plików|Bajty|Średnia|Górny limit ilości miejsca do magazynowania, który może być używany przez usługę Azure Files w bajtach.|Udziału|
|Transakcje|Transakcje|Licznik|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi.|Responsetype, geotype, ApiName, Authentication, przeudziale|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|Milisekundy|Średnia|Opóźnienie używane przez usługę Azure Storage do przetworzenia pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|SuccessE2ELatency|Opóźnienie e2e dla powodzenia|Milisekundy|Średnia|Kompleksowe opóźnienie pomyślnych żądań kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotyp, ApiName, uwierzytelnianie, przeudziale|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft. ClassicStorage/storageAccounts/queueServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QueueCapacity|Pojemność kolejki|Bajty|Średnia|Ilość miejsca używanego przez usługę kolejki konta magazynu, w bajtach.|None|
|QueueCount|Liczba kolejek|Licznik|Średnia|Liczba kolejek w usłudze kolejki konta magazynu.|None|
|QueueMessageCount|Liczba komunikatów w kolejce|Licznik|Średnia|Przybliżona liczba komunikatów w kolejce w usłudze kolejki konta magazynu.|None|
|Transakcje|Transakcje|Licznik|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|Milisekundy|Średnia|Opóźnienie używane przez usługę Azure Storage do przetworzenia pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie e2e dla powodzenia|Milisekundy|Średnia|Kompleksowe opóźnienie pomyślnych żądań kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalCalls|Łączna liczba wywołań|Licznik|Łącznie|Łączna liczba wywołań.|ApiName, OperationName, region|
|SuccessfulCalls|Pomyślne wywołania|Licznik|Łącznie|Liczba pomyślnych wywołań.|ApiName, OperationName, region|
|TotalErrors|Całkowita liczba błędów|Licznik|Łącznie|Łączna liczba wywołań z odpowiedzią na błąd (kod odpowiedzi HTTP 4xx lub 5xx).|ApiName, OperationName, region|
|BlockedCalls|Zablokowane wywołania|Licznik|Łącznie|Liczba wywołań, które przekroczyły limit szybkości lub limitu przydziału.|ApiName, OperationName, region|
|Błędy servererrors|Błędy serwera|Licznik|Łącznie|Liczba wywołań z błędem wewnętrznym usługi (5xx kodu odpowiedzi HTTP).|ApiName, OperationName, region|
|ClientErrors|Błędy klienta|Licznik|Łącznie|Liczba wywołań z błędem po stronie klienta (4xx kodu odpowiedzi HTTP).|ApiName, OperationName, region|
|Dane|Dane w|Bajty|Łącznie|Rozmiar danych przychodzących w bajtach.|ApiName, OperationName, region|
|DataOut|Dane wychodzące|Bajty|Łącznie|Rozmiar danych wychodzących w bajtach.|ApiName, OperationName, region|
|Opóźnienie|Opóźnienie|MilliSeconds|Średnia|Opóźnienie w milisekundach.|ApiName, OperationName, region|
|CharactersTranslated|Znaki tłumaczone|Licznik|Łącznie|Całkowita liczba znaków w żądaniu tekstu przychodzącego.|ApiName, OperationName, region|
|CharactersTrained|Znaki przeszkolone|Licznik|Łącznie|Łączna liczba znaków przeszkolonych.|ApiName, OperationName, region|
|SpeechSessionDuration|Czas trwania sesji mowy|S|Łącznie|Łączny czas trwania sesji mowy (w sekundach).|ApiName, OperationName, region|
|TotalTransactions|Łączna liczba transakcji|Licznik|Łącznie|Łączna liczba transakcji.|None|
|TotalTokenCalls|Łączna liczba wywołań tokenów|Licznik|Łącznie|Łączna liczba wywołań tokenów.|ApiName, OperationName, region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Średnia|Procent przydzielonych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne|None|
|Sieć — wejście|Sieć jest rozliczana (przestarzałe)|Bajty|Łącznie|Liczba bajtów rozliczanych odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący) (przestarzałe)|None|
|Sieć — wyjście|Sieć — do rozliczania (przestarzałe)|Bajty|Łącznie|Liczba bajtów rozliczanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący) (przestarzałe)|None|
|Bajty odczytu dysku|Bajty odczytu dysku|Bajty|Łącznie|Bajty odczytane z dysku w okresie monitorowania|None|
|Bajty zapisu dysku|Bajty zapisu dysku|Bajty|Łącznie|Bajty zapisywane na dysku w okresie monitorowania|None|
|Operacje odczytu z dysku/s|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Operacje we/wy odczytu dysku|None|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Operacje we/wy zapisu na dysku|None|
|Pozostałe kredyty procesora CPU|Pozostałe kredyty procesora CPU|Licznik|Średnia|Łączna liczba kredytów dostępnych dla serii|None|
|Wykorzystane środki CPU|Wykorzystane środki CPU|Licznik|Średnia|Łączna liczba kredytów zużytych przez maszynę wirtualną|None|
|Bajty odczytu na dysk/s|Bajty odczytu dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|SlotId|
|Bajty zapisu na dysku/s|Bajty zapisu na dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|SlotId|
|Operacje odczytu na dysk/s|Operacje odczytu z dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Na dysk głębokość kolejki|Głębokość kolejki dysku danych (przestarzałe)|Licznik|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|SlotId|
|Bajty odczytu dysku systemu operacyjnego/s|Bajty odczytu z dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|None|
|Bajty zapisu na dysku systemu operacyjnego/s|Bajty zapisu dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|None|
|Operacje odczytu na dysku systemu operacyjnego/s|Operacje odczytu z dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|None|
|Operacje zapisu na dysku systemu operacyjnego/s|Operacje zapisu na dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|None|
|Głębokość kolejki systemu operacyjnego na dysku|Głębokość kolejki dysku systemu operacyjnego (przestarzałe)|Licznik|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|None|
|Bajty odczytu dysku danych/s|Bajty odczytu dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|JEDNOSTKA LUN|
|Bajty zapisu na dysku danych/s|Bajty zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|JEDNOSTKA LUN|
|Operacje odczytu z dysku danych/s|Operacje odczytu z dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|JEDNOSTKA LUN|
|Operacje zapisu na dysku danych/s|Operacje zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|JEDNOSTKA LUN|
|Głębokość kolejki dysku danych|Głębokość kolejki dysku danych (wersja zapoznawcza)|Licznik|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|JEDNOSTKA LUN|
|Bajty odczytu dysku systemu operacyjnego/s|Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|None|
|Bajty zapisu dysku systemu operacyjnego/s|Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|None|
|Operacje odczytu z dysku systemu operacyjnego/s|Operacje odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|None|
|Operacje zapisu na dysku systemu operacyjnego/s|Operacje zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|None|
|Głębokość kolejki dysku systemu operacyjnego|Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza)|Licznik|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|None|
|Przepływy przychodzące|Przepływy przychodzące|Licznik|Średnia|Przepływy przychodzące to liczba bieżących przepływów w kierunku przychodzącym (ruch przychodzący do maszyny wirtualnej)|None|
|Przepływy wychodzące|Przepływy wychodzące|Licznik|Średnia|Przepływy wychodzące to liczba bieżących przepływów w kierunku ruchu wychodzącego (ruch wychodzący z maszyny wirtualnej)|None|
|Maksymalny współczynnik tworzenia przepływów przychodzących|Maksymalny współczynnik tworzenia przepływów przychodzących (wersja zapoznawcza)|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów przychodzących (ruch przychodzący do maszyny wirtualnej)|None|
|Maksymalny współczynnik tworzenia przepływów wychodzących|Maksymalny współczynnik tworzenia przepływów wychodzących (wersja zapoznawcza)|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów wychodzących (ruch wychodzący z maszyny wirtualnej)|None|
|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|Trafienie odczytu pamięci podręcznej dysku danych Premium (wersja zapoznawcza)|Procent|Średnia|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|JEDNOSTKA LUN|
|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|Chybienia odczytu pamięci podręcznej dysku z danymi Premium (wersja zapoznawcza)|Procent|Średnia|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|JEDNOSTKA LUN|
|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Procent|Średnia|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|None|
|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|Brak chybień w pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Procent|Średnia|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|None|
|Sieć łącznie|Sieć łącznie|Bajty|Łącznie|Liczba bajtów odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący)|None|
|Całkowita liczba sieci|Całkowita liczba sieci|Bajty|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący)|None|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Średnia|Procent przydzielonych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne|VMName|
|Sieć — wejście|Sieć jest rozliczana (przestarzałe)|Bajty|Łącznie|Liczba bajtów rozliczanych odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący) (przestarzałe)|VMName|
|Sieć — wyjście|Sieć — do rozliczania (przestarzałe)|Bajty|Łącznie|Liczba bajtów rozliczanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący) (przestarzałe)|VMName|
|Bajty odczytu dysku|Bajty odczytu dysku|Bajty|Łącznie|Bajty odczytane z dysku w okresie monitorowania|VMName|
|Bajty zapisu dysku|Bajty zapisu dysku|Bajty|Łącznie|Bajty zapisywane na dysku w okresie monitorowania|VMName|
|Operacje odczytu z dysku/s|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Operacje we/wy odczytu dysku|VMName|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Operacje we/wy zapisu na dysku|VMName|
|Pozostałe kredyty procesora CPU|Pozostałe kredyty procesora CPU|Licznik|Średnia|Łączna liczba kredytów dostępnych dla serii|None|
|Wykorzystane środki CPU|Wykorzystane środki CPU|Licznik|Średnia|Łączna liczba kredytów zużytych przez maszynę wirtualną|None|
|Bajty odczytu na dysk/s|Bajty odczytu dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|SlotId|
|Bajty zapisu na dysku/s|Bajty zapisu na dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|SlotId|
|Operacje odczytu na dysk/s|Operacje odczytu z dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Na dysk głębokość kolejki|Głębokość kolejki dysku danych (przestarzałe)|Licznik|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|SlotId|
|Bajty odczytu dysku systemu operacyjnego/s|Bajty odczytu z dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|None|
|Bajty zapisu na dysku systemu operacyjnego/s|Bajty zapisu dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|None|
|Operacje odczytu na dysku systemu operacyjnego/s|Operacje odczytu z dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|None|
|Operacje zapisu na dysku systemu operacyjnego/s|Operacje zapisu na dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|None|
|Głębokość kolejki systemu operacyjnego na dysku|Głębokość kolejki dysku systemu operacyjnego (przestarzałe)|Licznik|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|None|
|Bajty odczytu dysku danych/s|Bajty odczytu dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|Jednostka LUN, VMName|
|Bajty zapisu na dysku danych/s|Bajty zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|Jednostka LUN, VMName|
|Operacje odczytu z dysku danych/s|Operacje odczytu z dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|Jednostka LUN, VMName|
|Operacje zapisu na dysku danych/s|Operacje zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|Jednostka LUN, VMName|
|Głębokość kolejki dysku danych|Głębokość kolejki dysku danych (wersja zapoznawcza)|Licznik|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|Jednostka LUN, VMName|
|Bajty odczytu dysku systemu operacyjnego/s|Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Bajty zapisu dysku systemu operacyjnego/s|Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Operacje odczytu z dysku systemu operacyjnego/s|Operacje odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Operacje zapisu na dysku systemu operacyjnego/s|Operacje zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Głębokość kolejki dysku systemu operacyjnego|Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza)|Licznik|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|VMName|
|Przepływy przychodzące|Przepływy przychodzące|Licznik|Średnia|Przepływy przychodzące to liczba bieżących przepływów w kierunku przychodzącym (ruch przychodzący do maszyny wirtualnej)|VMName|
|Przepływy wychodzące|Przepływy wychodzące|Licznik|Średnia|Przepływy wychodzące to liczba bieżących przepływów w kierunku ruchu wychodzącego (ruch wychodzący z maszyny wirtualnej)|VMName|
|Maksymalny współczynnik tworzenia przepływów przychodzących|Maksymalny współczynnik tworzenia przepływów przychodzących (wersja zapoznawcza)|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów przychodzących (ruch przychodzący do maszyny wirtualnej)|VMName|
|Maksymalny współczynnik tworzenia przepływów wychodzących|Maksymalny współczynnik tworzenia przepływów wychodzących (wersja zapoznawcza)|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów wychodzących (ruch wychodzący z maszyny wirtualnej)|VMName|
|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|Trafienie odczytu pamięci podręcznej dysku danych Premium (wersja zapoznawcza)|Procent|Średnia|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|Jednostka LUN, VMName|
|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|Chybienia odczytu pamięci podręcznej dysku z danymi Premium (wersja zapoznawcza)|Procent|Średnia|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|Jednostka LUN, VMName|
|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Procent|Średnia|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|VMName|
|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|Brak chybień w pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Procent|Średnia|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|VMName|
|Sieć łącznie|Sieć łącznie|Bajty|Łącznie|Liczba bajtów odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący)|VMName|
|Całkowita liczba sieci|Całkowita liczba sieci|Bajty|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący)|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Średnia|Procent przydzielonych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne|None|
|Sieć — wejście|Sieć jest rozliczana (przestarzałe)|Bajty|Łącznie|Liczba bajtów rozliczanych odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący) (przestarzałe)|None|
|Sieć — wyjście|Sieć — do rozliczania (przestarzałe)|Bajty|Łącznie|Liczba bajtów rozliczanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący) (przestarzałe)|None|
|Bajty odczytu dysku|Bajty odczytu dysku|Bajty|Łącznie|Bajty odczytane z dysku w okresie monitorowania|None|
|Bajty zapisu dysku|Bajty zapisu dysku|Bajty|Łącznie|Bajty zapisywane na dysku w okresie monitorowania|None|
|Operacje odczytu z dysku/s|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Operacje we/wy odczytu dysku|None|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Operacje we/wy zapisu na dysku|None|
|Pozostałe kredyty procesora CPU|Pozostałe kredyty procesora CPU|Licznik|Średnia|Łączna liczba kredytów dostępnych dla serii|None|
|Wykorzystane środki CPU|Wykorzystane środki CPU|Licznik|Średnia|Łączna liczba kredytów zużytych przez maszynę wirtualną|None|
|Bajty odczytu na dysk/s|Bajty odczytu dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|SlotId|
|Bajty zapisu na dysku/s|Bajty zapisu na dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|SlotId|
|Operacje odczytu na dysk/s|Operacje odczytu z dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Na dysk głębokość kolejki|Głębokość kolejki dysku danych (przestarzałe)|Licznik|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|SlotId|
|Bajty odczytu dysku systemu operacyjnego/s|Bajty odczytu z dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|None|
|Bajty zapisu na dysku systemu operacyjnego/s|Bajty zapisu dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|None|
|Operacje odczytu na dysku systemu operacyjnego/s|Operacje odczytu z dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|None|
|Operacje zapisu na dysku systemu operacyjnego/s|Operacje zapisu na dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|None|
|Głębokość kolejki systemu operacyjnego na dysku|Głębokość kolejki dysku systemu operacyjnego (przestarzałe)|Licznik|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|None|
|Bajty odczytu dysku danych/s|Bajty odczytu dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|JEDNOSTKA LUN|
|Bajty zapisu na dysku danych/s|Bajty zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|JEDNOSTKA LUN|
|Operacje odczytu z dysku danych/s|Operacje odczytu z dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|JEDNOSTKA LUN|
|Operacje zapisu na dysku danych/s|Operacje zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|JEDNOSTKA LUN|
|Głębokość kolejki dysku danych|Głębokość kolejki dysku danych (wersja zapoznawcza)|Licznik|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|JEDNOSTKA LUN|
|Bajty odczytu dysku systemu operacyjnego/s|Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|None|
|Bajty zapisu dysku systemu operacyjnego/s|Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|None|
|Operacje odczytu z dysku systemu operacyjnego/s|Operacje odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|None|
|Operacje zapisu na dysku systemu operacyjnego/s|Operacje zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|None|
|Głębokość kolejki dysku systemu operacyjnego|Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza)|Licznik|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|None|
|Przepływy przychodzące|Przepływy przychodzące|Licznik|Średnia|Przepływy przychodzące to liczba bieżących przepływów w kierunku przychodzącym (ruch przychodzący do maszyny wirtualnej)|None|
|Przepływy wychodzące|Przepływy wychodzące|Licznik|Średnia|Przepływy wychodzące to liczba bieżących przepływów w kierunku ruchu wychodzącego (ruch wychodzący z maszyny wirtualnej)|None|
|Maksymalny współczynnik tworzenia przepływów przychodzących|Maksymalny współczynnik tworzenia przepływów przychodzących (wersja zapoznawcza)|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów przychodzących (ruch przychodzący do maszyny wirtualnej)|None|
|Maksymalny współczynnik tworzenia przepływów wychodzących|Maksymalny współczynnik tworzenia przepływów wychodzących (wersja zapoznawcza)|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów wychodzących (ruch wychodzący z maszyny wirtualnej)|None|
|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|Trafienie odczytu pamięci podręcznej dysku danych Premium (wersja zapoznawcza)|Procent|Średnia|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|JEDNOSTKA LUN|
|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|Chybienia odczytu pamięci podręcznej dysku z danymi Premium (wersja zapoznawcza)|Procent|Średnia|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|JEDNOSTKA LUN|
|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Procent|Średnia|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|None|
|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|Brak chybień w pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Procent|Średnia|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|None|
|Sieć łącznie|Sieć łącznie|Bajty|Łącznie|Liczba bajtów odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący)|None|
|Całkowita liczba sieci|Całkowita liczba sieci|Bajty|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący)|None|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuUsage|Użycie procesora CPU|Licznik|Średnia|Użycie procesora CPU we wszystkich rdzeniach w millicores.|containerName|
|MemoryUsage|Użycie pamięci|Bajty|Średnia|Całkowite użycie pamięci w bajcie.|containerName|
|NetworkBytesReceivedPerSecond|Bajty odebrane przez sieć na sekundę|Bajty|Średnia|Bajty odebrane przez sieć na sekundę.|None|
|NetworkBytesTransmittedPerSecond|Bajty przesyłane przez sieć na sekundę|Bajty|Średnia|Bajty przesyłane przez sieć na sekundę.|None|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalPullCount|Łączna liczba ściągania|Licznik|Średnia|Całkowita liczba ściągania obrazów|None|
|SuccessfulPullCount|Pomyślna liczba ściągania|Licznik|Średnia|Liczba pomyślnych operacji ściągania obrazu|None|
|TotalPushCount|Łączna liczba wypychanych|Licznik|Średnia|Liczba wypchnięciów obrazów łącznie|None|
|SuccessfulPushCount|Liczba wypychanych zakończonych powodzeniem|Licznik|Średnia|Liczba pomyślnych operacji wypychania obrazu|None|
|RunDuration|Czas trwania przebiegu|Milisekundy|Łącznie|Czas trwania przebiegu w milisekundach|None|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Łączna liczba dostępnych rdzeni procesora CPU w zarządzanym klastrze|Licznik|Średnia|Łączna liczba dostępnych rdzeni procesora CPU w zarządzanym klastrze|None|
|kube_node_status_allocatable_memory_bytes|Łączna ilość dostępnej pamięci w zarządzanym klastrze|Bajty|Średnia|Łączna ilość dostępnej pamięci w zarządzanym klastrze|None|
|kube_pod_status_ready|Liczba zasobników w stanie gotowe|Licznik|Średnia|Liczba zasobników w stanie gotowe|Przestrzeń nazw, pod|
|kube_node_status_condition|Stany różnych warunków węzła|Licznik|Średnia|Stany różnych warunków węzła|warunek, stan, status2, węzeł|
|kube_pod_status_phase|Liczba etapów według fazy|Licznik|Średnia|Liczba etapów według fazy|faza, przestrzeń nazw, pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SuccessfullRequests|Żądania zakończone powodzeniem|Licznik|Łącznie|Pomyślne żądania wykonywane przez niestandardowego dostawcę|HttpMethod, CallPath, StatusCode|
|FailedRequests|Żądania zakończone niepowodzeniem|Licznik|Łącznie|Pobiera dostępne dzienniki dla niestandardowych dostawców zasobów|HttpMethod, CallPath, StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|NICReadThroughput|Przepływność odczytu (Sieć)|BytesPerSecond|Średnia|Przepływność odczytu interfejsu sieciowego na urządzeniu w okresie sprawozdawczym dla wszystkich woluminów w bramie.|InstanceName|
|NICWriteThroughput|Przepływność zapisu (Sieć)|BytesPerSecond|Średnia|Przepływność zapisu interfejsu sieciowego na urządzeniu w okresie sprawozdawczym dla wszystkich woluminów w bramie.|InstanceName|
|CloudReadThroughputPerShare|Przepływność pobierania w chmurze (udział)|BytesPerSecond|Średnia|Przepływność pobierania na platformę Azure z udziału w okresie raportowania.|Udział|
|CloudUploadThroughputPerShare|Przepływność przekazywania w chmurze (udział)|BytesPerSecond|Średnia|Przepływność przekazywania do platformy Azure z udziału w okresie raportowania.|Udział|
|BytesUploadedToCloudPerShare|Przekazane bajty w chmurze (udział)|Bajty|Średnia|Całkowita liczba bajtów przekazanych do platformy Azure z udziału w okresie raportowania.|Udział|
|Łączna pojemność|Łączna pojemność|Bajty|Średnia|Łączna pojemność|None|
|AvailableCapacity|Dostępna pojemność|Bajty|Średnia|Dostępna pojemność w bajtach w okresie raportowania.|None|
|CloudUploadThroughput|Przepływność przekazywania w chmurze|BytesPerSecond|Średnia|W chmurze przekazano przepływność na platformę Azure w okresie raportowania.|None|
|CloudReadThroughput|Przepływność pobierania w chmurze|BytesPerSecond|Średnia|Chmura pobiera przepływność na platformę Azure w okresie raportowania.|None|
|BytesUploadedToCloud|Przekazane bajty w chmurze (urządzenie)|Bajty|Średnia|Całkowita liczba bajtów przekazana na platformę Azure z urządzenia w okresie raportowania.|None|
|HyperVVirtualProcessorUtilization|Obliczenia brzegowe — procentowy procesor CPU|Procent|Średnia|Procent użycia procesora CPU|InstanceName|
|HyperVMemoryUtilization|Obliczenia brzegowe — użycie pamięci|Procent|Średnia|Ilość pamięci RAM w użyciu|InstanceName|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|FailedRuns|Nieudane uruchomienia|Licznik|Łącznie||potokname, ActivityName|
|SuccessfulRuns|Pomyślne uruchomienia|Licznik|Łącznie||potokname, ActivityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PipelineFailedRuns|Metryki uruchomionych potoków zakończonych niepowodzeniem|Licznik|Łącznie||FailureType, nazwa|
|PipelineSucceededRuns|Metryki uruchamiania potoków zakończonych powodzeniem|Licznik|Łącznie||FailureType, nazwa|
|PipelineCancelledRuns|Metryki uruchomień potoku|Licznik|Łącznie||FailureType, nazwa|
|ActivityFailedRuns|Metryki uruchamiania działań zakończonych niepowodzeniem|Licznik|Łącznie||ActivityType, Potokname, FailureType, nazwa|
|ActivitySucceededRuns|Metryki uruchamiania działań zakończonych powodzeniem|Licznik|Łącznie||ActivityType, Potokname, FailureType, nazwa|
|ActivityCancelledRuns|Metryki uruchomień działań|Licznik|Łącznie||ActivityType, Potokname, FailureType, nazwa|
|TriggerFailedRuns|Metryki uruchomienia wyzwalacza zakończonego niepowodzeniem|Licznik|Łącznie||Nazwa, Niepowodzenie|
|TriggerSucceededRuns|Wyzwalacze uruchomienia wyzwalają metryki|Licznik|Łącznie||Nazwa, Niepowodzenie|
|TriggerCancelledRuns|Anulowane metryki uruchamiania wyzwalacza|Licznik|Łącznie||Nazwa, Niepowodzenie|
|IntegrationRuntimeCpuPercentage|Użycie procesora Integration Runtime|Procent|Średnia||IntegrationRuntimeName, nodename|
|IntegrationRuntimeAvailableMemory|Dostępna pamięć środowiska Integration Runtime|Bajty|Średnia||IntegrationRuntimeName, nodename|
|IntegrationRuntimeAverageTaskPickupDelay|Czas trwania kolejki Integration Runtime|S|Średnia||IntegrationRuntimeName|
|IntegrationRuntimeQueueLength|Długość kolejki środowiska Integration Runtime|Licznik|Średnia||IntegrationRuntimeName|
|IntegrationRuntimeAvailableNodeNumber|Liczba dostępnych węzłów Integration Runtime|Licznik|Średnia||IntegrationRuntimeName|
|MaxAllowedResourceCount|Maksymalna dozwolona liczba jednostek|Licznik|Maksimum||None|
|MaxAllowedFactorySizeInGbUnits|Maksymalny dozwolony rozmiar fabryki (jednostka GB)|Licznik|Maksimum||None|
|ResourceCount|Łączna liczba jednostek|Licznik|Maksimum||None|
|FactorySizeInGbUnits|Łączny rozmiar fabryki (jednostka GB)|Licznik|Maksimum||None|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|JobEndedSuccess|Zadania zakończone powodzeniem|Licznik|Łącznie|Liczba zadań zakończonych powodzeniem.|None|
|JobEndedFailure|Zadania zakończone niepowodzeniem|Licznik|Łącznie|Liczba zadań zakończonych niepowodzeniem.|None|
|JobEndedCancelled|Anulowane zadania|Licznik|Łącznie|Liczba anulowanych zadań.|None|
|JobAUEndedSuccess|Czas pomyślnej aktualizacji|S|Łącznie|Łączny czas dla zadań zakończonych powodzeniem.|None|
|JobAUEndedFailure|Czas niepowodzenia|S|Łącznie|Łączny czas AU zadań zakończonych niepowodzeniem.|None|
|JobAUEndedCancelled|Anulowano czas aktualizacji|S|Łącznie|Łączny czas AU dla anulowanych zadań.|None|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalStorage|Łączny magazyn|Bajty|Maksimum|Łączna ilość danych przechowywanych na koncie.|None|
|Zapisywana|Zapisywane dane|Bajty|Łącznie|Całkowita ilość danych zapisywana na konto.|None|
|Odczyt DataReady|Odczytane dane|Bajty|Łącznie|Łączna ilość danych odczytanych z konta.|None|
|WriteRequests|Żądania zapisu|Licznik|Łącznie|Liczba żądań zapisu danych na koncie.|None|
|ReadRequests|Żądania odczytu|Licznik|Łącznie|Liczba żądań odczytu danych do konta.|None|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|None|
|memory_percent|Procent pamięci|Procent|Średnia|Procent pamięci|None|
|io_consumption_percent|Procent operacji we/wy|Procent|Średnia|Procent operacji we/wy|None|
|storage_percent|Procent magazynu|Procent|Średnia|Procent magazynu|None|
|storage_used|Używany magazyn|Bajty|Średnia|Używany magazyn|None|
|storage_limit|Limit magazynu|Bajty|Maksimum|Limit magazynu|None|
|serverlog_storage_percent|Procent magazynu dzienników serwera|Procent|Średnia|Procent magazynu dzienników serwera|None|
|serverlog_storage_usage|Używany magazyn dzienników serwera|Bajty|Średnia|Używany magazyn dzienników serwera|None|
|serverlog_storage_limit|Limit magazynowania dziennika serwera|Bajty|Średnia|Limit magazynowania dziennika serwera|None|
|active_connections|Aktywne połączenia|Licznik|Średnia|Aktywne połączenia|None|
|connections_failed|Połączenia zakończone niepowodzeniem|Licznik|Łącznie|Połączenia zakończone niepowodzeniem|None|
|seconds_behind_master|Opóźnienie replikacji w sekundach|Licznik|Maksimum|Opóźnienie replikacji w sekundach|None|
|backup_storage_used|Używany magazyn kopii zapasowych|Bajty|Średnia|Używany magazyn kopii zapasowych|None|
|network_bytes_egress|Sieć — wyjście|Bajty|Łącznie|Sieć poza aktywnymi połączeniami|None|
|network_bytes_ingress|Sieć — wejście|Bajty|Łącznie|Sieć w ramach aktywnych połączeń|None|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|None|
|memory_percent|Procent pamięci|Procent|Średnia|Procent pamięci|None|
|io_consumption_percent|Procent operacji we/wy|Procent|Średnia|Procent operacji we/wy|None|
|storage_percent|Procent magazynu|Procent|Średnia|Procent magazynu|None|
|storage_used|Używany magazyn|Bajty|Średnia|Używany magazyn|None|
|storage_limit|Limit magazynu|Bajty|Maksimum|Limit magazynu|None|
|serverlog_storage_percent|Procent magazynu dzienników serwera|Procent|Średnia|Procent magazynu dzienników serwera|None|
|serverlog_storage_usage|Używany magazyn dzienników serwera|Bajty|Średnia|Używany magazyn dzienników serwera|None|
|serverlog_storage_limit|Limit magazynowania dziennika serwera|Bajty|Maksimum|Limit magazynowania dziennika serwera|None|
|active_connections|Aktywne połączenia|Licznik|Średnia|Aktywne połączenia|None|
|connections_failed|Połączenia zakończone niepowodzeniem|Licznik|Łącznie|Połączenia zakończone niepowodzeniem|None|
|seconds_behind_master|Opóźnienie replikacji w sekundach|Licznik|Maksimum|Opóźnienie replikacji w sekundach|None|
|backup_storage_used|Używany magazyn kopii zapasowych|Bajty|Średnia|Używany magazyn kopii zapasowych|None|
|network_bytes_egress|Sieć — wyjście|Bajty|Łącznie|Sieć poza aktywnymi połączeniami|None|
|network_bytes_ingress|Sieć — wejście|Bajty|Łącznie|Sieć w ramach aktywnych połączeń|None|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|None|
|memory_percent|Procent pamięci|Procent|Średnia|Procent pamięci|None|
|io_consumption_percent|Procent operacji we/wy|Procent|Średnia|Procent operacji we/wy|None|
|storage_percent|Procent magazynu|Procent|Średnia|Procent magazynu|None|
|storage_used|Używany magazyn|Bajty|Średnia|Używany magazyn|None|
|storage_limit|Limit magazynu|Bajty|Maksimum|Limit magazynu|None|
|serverlog_storage_percent|Procent magazynu dzienników serwera|Procent|Średnia|Procent magazynu dzienników serwera|None|
|serverlog_storage_usage|Używany magazyn dzienników serwera|Bajty|Średnia|Używany magazyn dzienników serwera|None|
|serverlog_storage_limit|Limit magazynowania dziennika serwera|Bajty|Maksimum|Limit magazynowania dziennika serwera|None|
|active_connections|Aktywne połączenia|Licznik|Średnia|Aktywne połączenia|None|
|connections_failed|Połączenia zakończone niepowodzeniem|Licznik|Łącznie|Połączenia zakończone niepowodzeniem|None|
|backup_storage_used|Używany magazyn kopii zapasowych|Bajty|Średnia|Używany magazyn kopii zapasowych|None|
|network_bytes_egress|Sieć — wyjście|Bajty|Łącznie|Sieć poza aktywnymi połączeniami|None|
|network_bytes_ingress|Sieć — wejście|Bajty|Łącznie|Sieć w ramach aktywnych połączeń|None|
|pg_replica_log_delay_in_seconds|Zwłoka repliki|S|Maksimum|Opóźnienie repliki w sekundach|None|
|pg_replica_log_delay_in_bytes|Maksymalne opóźnienie między replikami|Bajty|Maksimum|Opóźnienie w bajtach najbardziej opóźnionej repliki|None|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|None|
|memory_percent|Procent pamięci|Procent|Średnia|Procent pamięci|None|
|Wejścia|Liczba operacji we/wy na sekundę|Licznik|Średnia|Operacje we/wy na sekundę|None|
|storage_percent|Procent magazynu|Procent|Średnia|Procent magazynu|None|
|storage_used|Używany magazyn|Bajty|Średnia|Używany magazyn|None|
|active_connections|Aktywne połączenia|Licznik|Średnia|Aktywne połączenia|None|
|network_bytes_egress|Sieć — wyjście|Bajty|Łącznie|Sieć poza aktywnymi połączeniami|None|
|network_bytes_ingress|Sieć — wejście|Bajty|Łącznie|Sieć w ramach aktywnych połączeń|None|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Próby wysłania komunikatów telemetrycznych|Licznik|Łącznie|Liczba komunikatów telemetrycznych z urządzenia do chmury, które próbowano wysłać do centrum IoT Hub|None|
|d2c.telemetry.ingress.success|Wysłane komunikaty telemetryczne|Licznik|Łącznie|Liczba pomyślnie wysłanych komunikatów telemetrycznych z urządzenia do chmury do centrum IoT Hub|None|
|c2d.commands.egress.complete.success|Zakończono dostarczanie komunikatów C2D|Licznik|Łącznie|Liczba dostaw komunikatów z chmury do urządzenia ukończonych pomyślnie przez urządzenie|None|
|c2d.commands.egress.abandon.success|Porzucone komunikaty C2D|Licznik|Łącznie|Liczba komunikatów z chmury do urządzenia porzuconych przez urządzenie|None|
|c2d.commands.egress.reject.success|Odrzucone komunikaty C2D|Licznik|Łącznie|Liczba komunikatów z chmury do urządzenia odrzuconych przez urządzenie|None|
|C2DMessagesExpired|Komunikaty C2D wygasły (wersja zapoznawcza)|Licznik|Łącznie|Liczba wygasłych komunikatów z chmury do urządzenia|None|
|Devices. totalDevices|Łączna liczba urządzeń (przestarzałe)|Licznik|Łącznie|Liczba urządzeń zarejestrowanych w usłudze IoT Hub|None|
|devices.connectedDevices.allProtocol|Podłączone urządzenia (przestarzałe) |Licznik|Łącznie|Liczba urządzeń podłączonych do centrum IoT Hub|None|
|d2c.telemetry.egress.success|Routing: dostarczono komunikaty telemetryczne|Licznik|Łącznie|Liczba pomyślnie dostarczonych komunikatów do wszystkich punktów końcowych używających routingu IoT Hub. Jeśli komunikat jest kierowany do wielu punktów końcowych, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczenia. Jeśli wiadomość jest przekazywana do tego samego punktu końcowego wiele razy, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczenia.|None|
|d2c.telemetry.egress.dropped|Routing: porzucone komunikaty telemetryczne |Licznik|Łącznie|Liczba porzuconych komunikatów przez IoT Hub Routing ze względu na martwe punkty końcowe. Ta wartość nie zlicza komunikatów dostarczonych do trasy rezerwowej, ponieważ opuszczone wiadomości nie są tam dostarczane.|None|
|d2c.telemetry.egress.orphaned|Routing: oddzielone komunikaty telemetryczne |Licznik|Łącznie|Liczba oddzielonych komunikatów przez IoT Hub Routing, ponieważ nie są one zgodne z żadną regułą routingu (w tym regułą rezerwową). |None|
|d2c.telemetry.egress.invalid|Routing: komunikaty telemetryczne są niezgodne|Licznik|Łącznie|Liczba przypadków, gdy Routing IoT Hub nie mógł dostarczyć komunikatów z powodu niezgodności z punktem końcowym. Ta wartość nie obejmuje ponownych prób.|None|
|d2c.telemetry.egress.fallback|Routing: komunikaty dostarczane do powrotu|Licznik|Łącznie|Liczba IoT Hub komunikatów dostarczonych przez funkcję routingu do punktu końcowego skojarzonego z trasą rezerwową.|None|
|d2c.endpoints.egress.eventHubs|Routing: komunikaty dostarczane do centrum zdarzeń|Licznik|Łącznie|Liczba pomyślnie dostarczonych komunikatów do punktów końcowych centrum zdarzeń w usłudze IoT Hub Routing.|None|
|d2c.endpoints.latency.eventHubs|Routing: opóźnienie komunikatu dla centrum zdarzeń|Milisekundy|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów przychodzących w punkcie końcowym centrum zdarzeń.|None|
|d2c.endpoints.egress.serviceBusQueues|Routing: komunikaty dostarczone do kolejki Service Bus|Licznik|Łącznie|Liczba pomyślnie dostarczonych komunikatów do Service Bus punktów końcowych kolejki w usłudze IoT Hub Routing.|None|
|d2c.endpoints.latency.serviceBusQueues|Routing: opóźnienie komunikatu dla kolejki Service Bus|Milisekundy|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i telemetrii komunikatów przychodzących do punktu końcowego kolejki Service Bus.|None|
|d2c.endpoints.egress.serviceBusTopics|Routing: komunikaty dostarczane do Service Bus tematu|Licznik|Łącznie|Liczba pomyślnie dostarczonych komunikatów przez IoT Hub Routing do Service Bus punktów końcowych tematu.|None|
|d2c.endpoints.latency.serviceBusTopics|Routing: opóźnienie komunikatu dla Service Bus tematu|Milisekundy|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w punkcie końcowym tematu Service Bus.|None|
|d2c.endpoints.egress.builtIn.events|Routing: komunikaty dostarczane do komunikatów/zdarzeń|Licznik|Łącznie|Liczba pomyślnie dostarczonych komunikatów do wbudowanego punktu końcowego (komunikaty/zdarzenia) IoT Hub Routing.|None|
|d2c.endpoints.latency.builtIn.events|Routing: opóźnienie komunikatów dla komunikatów/zdarzeń|Milisekundy|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w wbudowanym punkcie końcowym (komunikaty/zdarzenia).|None|
|d2c.endpoints.egress.storage|Routing: komunikaty dostarczane do magazynu|Licznik|Łącznie|Liczba pomyślnie dostarczonych komunikatów do punktów końcowych usługi Routing IoT Hub.|None|
|d2c.endpoints.latency.storage|Routing: opóźnienie komunikatu dla magazynu|Milisekundy|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w punkcie końcowym magazynu.|None|
|d2c.endpoints.egress.storage.bytes|Routing: dane dostarczane do magazynu|Bajty|Łącznie|Ilość danych (w bajtach) IoT Hub Routing dostarczany do punktów końcowych magazynu.|None|
|d2c.endpoints.egress.storage.blobs|Routing: obiekty blob dostarczane do magazynu|Licznik|Łącznie|Ile razy usługa Routing IoT Hub dostarczać obiekty blob do punktów końcowych magazynu.|None|
|EventGridDeliveries|Dostawy Event Grid (wersja zapoznawcza)|Licznik|Łącznie|Liczba zdarzeń IoT Hub opublikowanych do Event Grid. Użyj wymiaru wynik dla liczby żądań zakończonych powodzeniem i niepowodzeniem. Wymiar EventType przedstawia typ zdarzenia (https://aka.ms/ioteventgrid).|ResourceId, wynik, typ zdarzenia|
|EventGridLatency|Opóźnienie Event Grid (wersja zapoznawcza)|Milisekundy|Średnia|Średnie opóźnienie (w milisekundach) od momentu wygenerowania zdarzenia usługi IoT Hub po opublikowaniu zdarzenia w Event Grid. Ta liczba jest średnia między wszystkimi typami zdarzeń. Użyj wymiaru EventType, aby zobaczyć opóźnienie określonego typu zdarzenia.|ResourceId, EventType|
|d2c.twin.read.success|Pomyślne odczyty sznurów z urządzeń|Licznik|Łącznie|Liczba wszystkich udanych operacji zainicjowanych przez urządzenie.|None|
|d2c.twin.read.failure|Nieudane odczyty sznurów z urządzeń|Licznik|Łącznie|Liczba wszystkich nieudanych operacji zainicjowanych przez urządzenie.|None|
|d2c.twin.read.size|Rozmiar odpowiedzi dla sznurów odczytanych z urządzeń|Bajty|Średnia|Średnia, minimum i maksimum wszystkich udanych zainicjowanych przez urządzenia sznurów.|None|
|d2c.twin.update.success|Pomyślne aktualizacje bliźniaczych urządzeń|Licznik|Łącznie|Liczba wszystkich pomyślnych aktualizacji typu bliźniaczych zainicjowanych przez urządzenie.|None|
|d2c.twin.update.failure|Niepowodzenie aktualizacji bliźniaczych z urządzeń|Licznik|Łącznie|Liczba wszystkich niezakończonych niepowodzeniem aktualizacji dwuosiowych zainicjowanych przez urządzenie.|None|
|d2c.twin.update.size|Rozmiar aktualizacji bliźniaczych z urządzeń|Bajty|Średnia|Średni, minimalny i maksymalny rozmiar wszystkich pomyślnych aktualizacji typu dwuosiowego zainicjowanego przez urządzenie.|None|
|c2d.methods.success|Pomyślne wywołania metody bezpośredniej|Licznik|Łącznie|Liczba wszystkich pomyślnych wywołań metody bezpośredniej.|None|
|C2D. Methods. Failure|Nieudane wywołania metody bezpośredniej|Licznik|Łącznie|Liczba wszystkich wywołań metod bezpośrednich zakończonych niepowodzeniem.|None|
|c2d.methods.requestSize|Rozmiar żądania wywołań metody bezpośredniej|Bajty|Średnia|Średnia, minimum i maksimum wszystkich pomyślnych żądań metody bezpośredniej.|None|
|c2d.methods.responseSize|Rozmiar odpowiedzi wywołań metody bezpośredniej|Bajty|Średnia|Średnia, minimum i maksimum wszystkich zakończonych powodzeniem odpowiedzi metody bezpośredniej.|None|
|c2d.twin.read.success|Pomyślne odczyty sznurów z zaplecza|Licznik|Łącznie|Liczba wszystkich zakończonych powodzeniem odczytów dwuosiowych zainicjowanych z powrotem.|None|
|c2d.twin.read.failure|Nieudane odczyty sznurów z zaplecza|Licznik|Łącznie|Liczba wszystkich zakończonych niepowodzeniem odczytów dwuosiowych zainicjowanych z powrotem.|None|
|c2d.twin.read.size|Rozmiar odpowiedzi na odwrocie od zaplecza|Bajty|Średnia|Średnia, minimum i maksimum wszystkich udanych zainicjowanych z powrotem sznurów danych.|None|
|c2d.twin.update.success|Pomyślne aktualizacje bliźniaczych z zaplecza|Licznik|Łącznie|Liczba wszystkich pomyślnych aktualizacji typu bliźniaczych zainicjowanych z powrotem.|None|
|c2d.twin.update.failure|Niepowodzenie aktualizacji bliźniaczych z zaplecza|Licznik|Łącznie|Liczba wszystkich niezakończonych niepowodzeniem aktualizacji typu bliźniaczych zainicjowanych z powrotem.|None|
|c2d.twin.update.size|Rozmiar aktualizacji przędzy od zaplecza|Bajty|Średnia|Średni, minimalny i maksymalny rozmiar wszystkich pomyślnych aktualizacji typu sznurka zainicjowanych z powrotem.|None|
|twinQueries.success|Pomyślne zapytania bliźniaczy|Licznik|Łącznie|Liczba wszystkich udanych zapytań bliźniaczych.|None|
|twinQueries.failure|Niepowodzenie zapytań bliźniaczych|Licznik|Łącznie|Liczba wszystkich zakończonych niepowodzeniem zapytań bliźniaczych.|None|
|twinQueries.resultSize|Rozmiar wyniku zapytań bliźniaczych|Bajty|Średnia|Średnia, minimalna i maksymalna wielkość wyniku wszystkich udanych zapytań bliźniaczych.|None|
|jobs.createTwinUpdateJob.success|Pomyślne utworzenie dwuosiowych zadań aktualizacji|Licznik|Łącznie|Liczba wszystkich udanych tworzenia zadań aktualizacji z przędzą.|None|
|jobs.createTwinUpdateJob.failure|Nie można utworzyć dwuosiowych zadań aktualizacji|Licznik|Łącznie|Liczba wszystkich nieudanych operacji tworzenia zadań aktualizacji z przędzą.|None|
|jobs.createDirectMethodJob.success|Pomyślne utworzenie zadań wywołania metody|Licznik|Łącznie|Liczba wszystkich pomyślnych operacji tworzenia zadań wywołania metody bezpośredniej.|None|
|jobs.createDirectMethodJob.failure|Nie można utworzyć zadań wywołania metody|Licznik|Łącznie|Liczba wszystkich nieudanych operacji tworzenia zadań wywołania metody bezpośredniej.|None|
|jobs.listJobs.success|Pomyślne wywołania do zadań na liście|Licznik|Łącznie|Liczba wszystkich udanych wywołań do listy zadań.|None|
|jobs.listJobs.failure|Wywołania zakończone niepowodzeniem do listy zadań|Licznik|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu wyświetlenia listy zadań.|None|
|jobs.cancelJob.success|Pomyślne anulowania zadań|Licznik|Łącznie|Liczba wszystkich udanych wywołań do anulowania zadania.|None|
|jobs.cancelJob.failure|Nieudane anulowania zadań|Licznik|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu anulowania zadania.|None|
|jobs.queryJobs.success|Pomyślne zapytania dotyczące zadań|Licznik|Łącznie|Liczba wszystkich udanych wywołań do zadań zapytań.|None|
|jobs.queryJobs.failure|Nieudane kwerendy zadań|Licznik|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu wykonywania zapytań dotyczących zadań.|None|
|zadania. ukończone|Ukończone zadania|Licznik|Łącznie|Liczba wszystkich ukończonych zadań.|None|
|zadania. Niepowodzenie|Zadania zakończone niepowodzeniem|Licznik|Łącznie|Liczba wszystkich zadań zakończonych niepowodzeniem.|None|
|d2c.telemetry.ingress.sendThrottle|Liczba błędów ograniczania|Licznik|Łącznie|Liczba błędów ograniczania z powodu ograniczeń przepływności urządzenia|None|
|dailyMessageQuotaUsed|Całkowita liczba użytych komunikatów|Licznik|Średnia|Łączna liczba użytych komunikatów|None|
|deviceDataUsage|Całkowite użycie danych urządzenia|Bajty|Łącznie|Bajty przesłane do i z dowolnych urządzeń podłączonych do usługi IotHub|None|
|deviceDataUsageV2|Całkowite użycie danych urządzenia (wersja zapoznawcza)|Bajty|Łącznie|Bajty przesłane do i z dowolnych urządzeń podłączonych do usługi IotHub|None|
|totalDeviceCount|Łączna liczba urządzeń (wersja zapoznawcza)|Licznik|Średnia|Liczba urządzeń zarejestrowanych w usłudze IoT Hub|None|
|connectedDeviceCount|Podłączone urządzenia (wersja zapoznawcza)|Licznik|Średnia|Liczba urządzeń podłączonych do centrum IoT Hub|None|
|konfiguracje|Metryki konfiguracji|Licznik|Łącznie|Metryki dla operacji konfiguracji|None|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RegistrationAttempts|Próby rejestracji|Licznik|Łącznie|Liczba prób przeprowadzenia rejestracji urządzeń|ProvisioningServiceName, IotHubName, status|
|DeviceAssignments|Przypisane urządzenia|Licznik|Łącznie|Liczba urządzeń przypisanych do centrum IoT Hub|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Próby zaświadczania|Licznik|Łącznie|Liczba podjętych prób zaświadczania urządzenia|ProvisioningServiceName, stan, protokół|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Addregion|Dodano region|Licznik|Licznik|Dodano region|Region|
|AvailableStorage|Dostępny magazyn|Bajty|Łącznie|Łączna ilość dostępnego magazynu zgłoszona z dokładnością do 5 minut|CollectionName, DatabaseName, region|
|CassandraConnectionClosures|Zamknięcia połączeń Cassandra|Licznik|Łącznie|Liczba zamkniętych połączeń Cassandra, które zostały zgłoszone z dokładnością do 1 minuty|APIType, region, ClosureReason|
|CassandraRequestCharges|Opłaty za żądania Cassandra|Licznik|Łącznie|Jednostek ru zużyte dla żądań Cassandra|APIType, DatabaseName, CollectionName, region, OperationType, ResourceType|
|CassandraRequests|Żądania Cassandra|Licznik|Licznik|Liczba wykonanych żądań Cassandra|APIType, DatabaseName, CollectionName, region, OperationType, ResourceType, ErrorCode|
|Konto|Utworzono konto|Licznik|Licznik|Utworzono konto|None|
|Datausage|Użycie danych|Bajty|Łącznie|Całkowite użycie danych zgłoszone z dokładnością do 5 minut|CollectionName, DatabaseName, region|
|DeleteAccount|Konto zostało usunięte|Licznik|Licznik|Konto zostało usunięte|None|
|DocumentCount|Liczba dokumentów|Licznik|Łącznie|Łączna liczba dokumentów raportowana z dokładnością do 5 minut|CollectionName, DatabaseName, region|
|DocumentQuota|Przydział dokumentu|Bajty|Łącznie|Łączny przydział magazynu zgłoszony z dokładnością do 5 minut|CollectionName, DatabaseName, region|
|IndexUsage|Użycie indeksu|Bajty|Łącznie|Całkowite użycie indeksów zgłoszone na 5 minut|CollectionName, DatabaseName, region|
|MetadataRequests|Żądania metadanych|Licznik|Licznik|Liczba żądań metadanych. Cosmos DB przechowuje zbieranie metadanych systemu dla każdego konta, które pozwala na Wyliczanie kolekcji, baz danych itp. i ich konfiguracji bez opłat.|DatabaseName, CollectionName, region, StatusCode, rola|
|MongoRequestCharge|Opłata żądania Mongo|Licznik|Łącznie|Wykorzystane jednostki żądania Mongo|DatabaseName, CollectionName, region, CommandName, ErrorCode|
|MongoRequests|Żądania Mongo|Licznik|Licznik|Liczba wykonanych żądań Mongo|DatabaseName, CollectionName, region, CommandName, ErrorCode|
|MongoRequestsCount|Częstotliwość żądania Mongo|CountPerSecond|Średnia|Liczba żądań Mongo na sekundę|DatabaseName, CollectionName, region, CommandName, ErrorCode|
|MongoRequestsDelete|Mongo — wskaźnik żądania usunięcia|CountPerSecond|Średnia|Żądanie usunięcia Mongo na sekundę|DatabaseName, CollectionName, region, CommandName, ErrorCode|
|MongoRequestsInsert|Liczba żądań wstawienia Mongo|CountPerSecond|Średnia|Liczba operacji wstawiania Mongo na sekundę|DatabaseName, CollectionName, region, CommandName, ErrorCode|
|MongoRequestsQuery|Częstotliwość żądań zapytań Mongo|CountPerSecond|Średnia|Żądania zapytania Mongo na sekundę|DatabaseName, CollectionName, region, CommandName, ErrorCode|
|MongoRequestsUpdate|Częstotliwość żądań aktualizacji Mongo|CountPerSecond|Średnia|Żądanie aktualizacji Mongo na sekundę|DatabaseName, CollectionName, region, CommandName, ErrorCode|
|ProvisionedThroughput|Aprowizowana przepływność|Licznik|Maksimum|Aprowizowana przepływność|DatabaseName, CollectionName|
|RegionFailover|Region w trybie failover|Licznik|Licznik|Region w trybie failover|None|
|RemoveRegion|Usunięto region|Licznik|Licznik|Usunięto region|Region|
|ReplicationLatency|Opóźnienie replikacji poziomie P99|MilliSeconds|Średnia|Opóźnienie replikacji poziomie P99 w regionach źródłowym i docelowym dla konta z obsługą geograficzną|SourceRegion,TargetRegion|
|Dostępność|Dostępność usługi|Procent|Średnia|Dostępność żądania konta o jednej godzinie, dniu lub o dokładności|None|
|TotalRequestUnits|Łączna liczba jednostek żądania|Licznik|Łącznie|Wykorzystane jednostki żądania|DatabaseName, CollectionName, region, StatusCode, OperationType|
|TotalRequests|Łączna liczba żądań|Licznik|Licznik|Liczba wykonanych żądań|DatabaseName, CollectionName, region, StatusCode, OperationType|
|UpdateAccountKeys|Zaktualizowano klucze konta|Licznik|Licznik|Zaktualizowano klucze konta|keyType|
|UpdateAccountNetworkSettings|Zaktualizowano ustawienia sieci konta|Licznik|Licznik|Zaktualizowano ustawienia sieci konta|None|
|UpdateAccountReplicationSettings|Zaktualizowano ustawienia replikacji konta|Licznik|Licznik|Zaktualizowano ustawienia replikacji konta|None|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft. EnterpriseKnowledgeGraph/usługi

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TransactionCount|Liczba transakcji|Licznik|Licznik|Łączna liczba transakcji|TransactionCount|
|SuccessCount|Liczba sukcesów|Licznik|Licznik|Liczba zakończonych pomyślnie transakcji|SuccessCount|
|FailureCount|Liczba błędów|Licznik|Licznik|Liczba transakcji zakończonych niepowodzeniem|FailureCount|
|SuccessLatency|Opóźnienie sukcesu|MilliSeconds|Średnia|Opóźnienie transakcji zakończonych powodzeniem|SuccessCount|

## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/domeny

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PublishSuccessCount|Zdarzenia opublikowane|Licznik|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Temat|
|PublishFailCount|Publikowanie zdarzeń zakończonych niepowodzeniem|Licznik|Łącznie|Całkowita liczba zdarzeń, których publikowanie nie powiodło się w tym temacie|Temat, Błądtype, błąd|
|PublishSuccessLatencyInMs|Czas oczekiwania na pomyślne publikowanie|Milisekundy|Łącznie|Opóźnienie sukcesu publikacji w milisekundach|None|
|MatchedEventCount|Dopasowane zdarzenia|Licznik|Łącznie|Łączna liczba zdarzeń dopasowanych do tej subskrypcji zdarzeń|Temat, EventSubscriptionName, DomainEventSubscriptionName|
|DeliveryAttemptFailCount|Zdarzenia zakończonych niepowodzeniem|Licznik|Łącznie|Całkowita liczba zdarzeń, których dostarczenie do tej subskrypcji zdarzeń nie powiodło się|Temat, EventSubscriptionName, DomainEventSubscriptionName, Error, ErrorType|
|DeliverySuccessCount|Dostarczone zdarzenia|Licznik|Łącznie|Całkowita liczba zdarzeń dostarczonych do tej subskrypcji zdarzeń|Temat, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Czas przetwarzania docelowego|Milisekundy|Średnia|Czas trwania przetwarzania docelowego w milisekundach|Temat, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Opuszczone zdarzenia|Licznik|Łącznie|Całkowita liczba porzuconych zdarzeń pasujących do tej subskrypcji zdarzeń|Temat, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|DeadLetteredCount|Zdarzenia utraconych wiadomości|Licznik|Łącznie|Łączna liczba utraconych zdarzeń, które pasują do tej subskrypcji zdarzeń|Temat, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/tematy

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PublishSuccessCount|Zdarzenia opublikowane|Licznik|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|None|
|PublishFailCount|Publikowanie zdarzeń zakończonych niepowodzeniem|Licznik|Łącznie|Całkowita liczba zdarzeń, których publikowanie nie powiodło się w tym temacie|ErrorType, błąd|
|UnmatchedEventCount|Niedopasowane zdarzenia|Licznik|Łącznie|Łączna liczba zdarzeń, które nie pasują do żadnej subskrypcji zdarzeń dla tego tematu|None|
|PublishSuccessLatencyInMs|Czas oczekiwania na pomyślne publikowanie|Milisekundy|Łącznie|Opóźnienie sukcesu publikacji w milisekundach|None|
|MatchedEventCount|Dopasowane zdarzenia|Licznik|Łącznie|Łączna liczba zdarzeń dopasowanych do tej subskrypcji zdarzeń|EventSubscriptionName|
|DeliveryAttemptFailCount|Zdarzenia zakończonych niepowodzeniem|Licznik|Łącznie|Całkowita liczba zdarzeń, których dostarczenie do tej subskrypcji zdarzeń nie powiodło się|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Dostarczone zdarzenia|Licznik|Łącznie|Całkowita liczba zdarzeń dostarczonych do tej subskrypcji zdarzeń|EventSubscriptionName|
|DestinationProcessingDurationInMs|Czas przetwarzania docelowego|Milisekundy|Średnia|Czas trwania przetwarzania docelowego w milisekundach|EventSubscriptionName|
|DroppedEventCount|Opuszczone zdarzenia|Licznik|Łącznie|Całkowita liczba porzuconych zdarzeń pasujących do tej subskrypcji zdarzeń|DropReason,EventSubscriptionName|
|DeadLetteredCount|Zdarzenia utraconych wiadomości|Licznik|Łącznie|Łączna liczba utraconych zdarzeń, które pasują do tej subskrypcji zdarzeń|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|MatchedEventCount|Dopasowane zdarzenia|Licznik|Łącznie|Łączna liczba zdarzeń dopasowanych do tej subskrypcji zdarzeń|None|
|DeliveryAttemptFailCount|Zdarzenia zakończonych niepowodzeniem|Licznik|Łącznie|Całkowita liczba zdarzeń, których dostarczenie do tej subskrypcji zdarzeń nie powiodło się|Błąd, Błądtype|
|DeliverySuccessCount|Dostarczone zdarzenia|Licznik|Łącznie|Całkowita liczba zdarzeń dostarczonych do tej subskrypcji zdarzeń|None|
|DestinationProcessingDurationInMs|Czas przetwarzania docelowego|Milisekundy|Średnia|Czas trwania przetwarzania docelowego w milisekundach|None|
|DroppedEventCount|Opuszczone zdarzenia|Licznik|Łącznie|Całkowita liczba porzuconych zdarzeń pasujących do tej subskrypcji zdarzeń|DropReason|
|DeadLetteredCount|Zdarzenia utraconych wiadomości|Licznik|Łącznie|Łączna liczba utraconych zdarzeń, które pasują do tej subskrypcji zdarzeń|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PublishSuccessCount|Zdarzenia opublikowane|Licznik|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|None|
|PublishFailCount|Publikowanie zdarzeń zakończonych niepowodzeniem|Licznik|Łącznie|Całkowita liczba zdarzeń, których publikowanie nie powiodło się w tym temacie|ErrorType, błąd|
|UnmatchedEventCount|Niedopasowane zdarzenia|Licznik|Łącznie|Łączna liczba zdarzeń, które nie pasują do żadnej subskrypcji zdarzeń dla tego tematu|None|
|PublishSuccessLatencyInMs|Czas oczekiwania na pomyślne publikowanie|Milisekundy|Łącznie|Opóźnienie sukcesu publikacji w milisekundach|None|



## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Żądania successfulrequests|Żądania zakończone powodzeniem|Licznik|Łącznie|Pomyślne żądania dla elementu Microsoft. EventHub.|EntityName, klasy OperationResult|
|Błędy servererrors|Błędy serwera.|Licznik|Łącznie|Błędy serwera dla elementu Microsoft. EventHub.|EntityName, klasy OperationResult|
|Błędy usererrors|Błędy użytkownika.|Licznik|Łącznie|Błędy użytkowników dla elementu Microsoft. EventHub.|EntityName, klasy OperationResult|
|Błędy quotaexceedederrors|Błędy przekroczenia limitu przydziału.|Licznik|Łącznie|Przekroczono limit przydziału dla elementu Microsoft. EventHub.|EntityName, klasy OperationResult|
|ThrottledRequests|Żądania z ograniczeniami.|Licznik|Łącznie|Żądania ograniczone dla elementu Microsoft. EventHub.|EntityName, klasy OperationResult|
|Żądania incomingrequests|Żądania przychodzące|Licznik|Łącznie|Żądania przychodzące dla elementu Microsoft. EventHub.|EntityName|
|Komunikaty incomingmessages|Komunikaty przychodzące|Licznik|Łącznie|Komunikaty przychodzące dla elementu Microsoft. EventHub.|EntityName|
|Komunikaty outgoingmessages|Komunikaty wychodzące|Licznik|Łącznie|Komunikaty wychodzące dla elementu Microsoft. EventHub.|EntityName|
|Bajty incomingbytes|Bajty przychodzące.|Bajty|Łącznie|Przychodzące bajty dla elementu Microsoft. EventHub.|EntityName|
|Bajty outgoingbytes|Bajty wychodzące.|Bajty|Łącznie|Bajty wychodzące dla elementu Microsoft. EventHub.|EntityName|
|Połączeń ActiveConnections|Połączeń ActiveConnections|Licznik|Średnia|Łączna liczba aktywnych połączeń dla elementu Microsoft. EventHub.|None|
|Połączenia connectionsopened|Otwarte połączenia.|Licznik|Średnia|Połączenia otwarte dla elementu Microsoft. EventHub.|EntityName|
|Połączenia connectionsclosed|Połączenia zamknięte.|Licznik|Średnia|Połączenia zamknięte dla elementu Microsoft. EventHub.|EntityName|
|CaptureBacklog|Zaległości przechwytywania.|Licznik|Łącznie|Zaległości przechwytywania dla elementu Microsoft. EventHub.|EntityName|
|CapturedMessages|Przechwycone komunikaty.|Licznik|Łącznie|Przechwycone komunikaty dla elementu Microsoft. EventHub.|EntityName|
|CapturedBytes|Przechwycone bajty.|Bajty|Łącznie|Przechwycone bajty dla elementu Microsoft. EventHub.|EntityName|
|Rozmiar|Rozmiar|Bajty|Średnia|Rozmiar centrum EventHub w bajtach.|EntityName|
|INREQS|Żądania przychodzące (przestarzałe)|Licznik|Łącznie|Całkowita liczba żądań wysłania przychodzącego dla przestrzeni nazw (przestarzałe)|None|
|SUCCREQ|Żądania zakończone powodzeniem (przestarzałe)|Licznik|Łącznie|Łączna liczba pomyślnych żądań dla przestrzeni nazw (przestarzałe)|None|
|FAILREQ|Nieudane żądania (przestarzałe)|Licznik|Łącznie|Łączna liczba nieudanych żądań dla przestrzeni nazw (przestarzałe)|None|
|SVRBSY|Błędy zajęte przez serwer (przestarzałe)|Licznik|Łącznie|Łączna liczba błędów zajętości serwera dla przestrzeni nazw (przestarzałe)|None|
|MIĘDZY|Wewnętrzne błędy serwera (przestarzałe)|Licznik|Łącznie|Łączna liczba błędów wewnętrznego serwera dla przestrzeni nazw (przestarzałe)|None|
|MISCERR|Inne błędy (przestarzałe)|Licznik|Łącznie|Łączna liczba nieudanych żądań dla przestrzeni nazw (przestarzałe)|None|
|INMSGS|Komunikaty przychodzące (przestarzałe) (przestarzałe)|Licznik|Łącznie|Całkowita liczba komunikatów przychodzących dla przestrzeni nazw. Ta Metryka jest przestarzała. Zamiast tego użyj metryki komunikatów przychodzących (przestarzałe)|None|
|EHINMSGS|Komunikaty przychodzące (przestarzałe)|Licznik|Łącznie|Całkowita liczba komunikatów przychodzących dla przestrzeni nazw (przestarzałe)|None|
|OUTMSGS|Wiadomości wychodzące (przestarzałe) (przestarzałe)|Licznik|Łącznie|Całkowita liczba komunikatów wychodzących dla przestrzeni nazw. Ta Metryka jest przestarzała. Zamiast tego użyj metryki komunikatów wychodzących (przestarzałe)|None|
|EHOUTMSGS|Wiadomości wychodzące (przestarzałe)|Licznik|Łącznie|Całkowita liczba komunikatów wychodzących dla przestrzeni nazw (przestarzałe)|None|
|EHINMBS|Przychodzące bajty (przestarzałe) (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów przychodzących centrum zdarzeń dla przestrzeni nazw. Ta Metryka jest przestarzała. Zamiast tego użyj metryki bajtów przychodzących (przestarzałe)|None|
|EHINBYTES|Przychodzące bajty (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów przychodzących centrum zdarzeń dla przestrzeni nazw (przestarzałe)|None|
|EHOUTMBS|Bajty wychodzące (przestarzałe) (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów wychodzących centrum zdarzeń dla przestrzeni nazw. Ta Metryka jest przestarzała. Zamiast tego użyj metryki bajtów wychodzących (przestarzałe)|None|
|EHOUTBYTES|Bajty wychodzące (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów wychodzących centrum zdarzeń dla przestrzeni nazw (przestarzałe)|None|
|EHABL|Archiwum komunikatów zaległości (przestarzałe)|Licznik|Łącznie|Komunikaty archiwum centrum zdarzeń w zaległości dla przestrzeni nazw (przestarzałe)|None|
|EHAMSGS|Komunikaty archiwalne (przestarzałe)|Licznik|Łącznie|Zarchiwizowane komunikaty centrum zdarzeń w przestrzeni nazw (przestarzałe)|None|
|EHAMBS|Przepływność komunikatów archiwalnych (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów archiwalnych centrum zdarzeń w przestrzeni nazw (przestarzałe)|None|

## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/klastry

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Żądania successfulrequests|Żądania zakończone powodzeniem|Licznik|Łącznie|Pomyślne żądania dla elementu Microsoft. EventHub.|OperationResult|
|Błędy servererrors|Błędy serwera.|Licznik|Łącznie|Błędy serwera dla elementu Microsoft. EventHub.|OperationResult|
|Błędy usererrors|Błędy użytkownika.|Licznik|Łącznie|Błędy użytkowników dla elementu Microsoft. EventHub.|OperationResult|
|Błędy quotaexceedederrors|Błędy przekroczenia limitu przydziału.|Licznik|Łącznie|Przekroczono limit przydziału dla elementu Microsoft. EventHub.|OperationResult|
|ThrottledRequests|Żądania z ograniczeniami.|Licznik|Łącznie|Żądania ograniczone dla elementu Microsoft. EventHub.|OperationResult|
|Żądania incomingrequests|Żądania przychodzące|Licznik|Łącznie|Żądania przychodzące dla elementu Microsoft. EventHub.|None|
|Komunikaty incomingmessages|Komunikaty przychodzące|Licznik|Łącznie|Komunikaty przychodzące dla elementu Microsoft. EventHub.|None|
|Komunikaty outgoingmessages|Komunikaty wychodzące|Licznik|Łącznie|Komunikaty wychodzące dla elementu Microsoft. EventHub.|None|
|Bajty incomingbytes|Bajty przychodzące.|Bajty|Łącznie|Przychodzące bajty dla elementu Microsoft. EventHub.|None|
|Bajty outgoingbytes|Bajty wychodzące.|Bajty|Łącznie|Bajty wychodzące dla elementu Microsoft. EventHub.|None|
|Połączeń ActiveConnections|Połączeń ActiveConnections|Licznik|Średnia|Łączna liczba aktywnych połączeń dla elementu Microsoft. EventHub.|None|
|Połączenia connectionsopened|Otwarte połączenia.|Licznik|Średnia|Połączenia otwarte dla elementu Microsoft. EventHub.|None|
|Połączenia connectionsclosed|Połączenia zamknięte.|Licznik|Średnia|Połączenia zamknięte dla elementu Microsoft. EventHub.|None|
|CaptureBacklog|Zaległości przechwytywania.|Licznik|Łącznie|Zaległości przechwytywania dla elementu Microsoft. EventHub.|None|
|CapturedMessages|Przechwycone komunikaty.|Licznik|Łącznie|Przechwycone komunikaty dla elementu Microsoft. EventHub.|None|
|CapturedBytes|Przechwycone bajty.|Bajty|Łącznie|Przechwycone bajty dla elementu Microsoft. EventHub.|None|
|Procesor CPU|Procesor CPU|Procent|Maksimum|Użycie procesora CPU przez klaster centrum zdarzeń jako wartość procentowa|Rola|
|AvailableMemory|Dostępna pamięć|Procent|Maksimum|Dostępna pamięć dla klastra centrum zdarzeń jako procent całkowitej ilości pamięci.|Rola|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|GatewayRequests|Żądania bramy|Licznik|Łącznie|Liczba żądań bramy|HttpStatus|
|CategorizedGatewayRequests|Skategoryzowane żądania bramy|Licznik|Łącznie|Liczba żądań bramy według kategorii (1XX/2xx/3xx/4xx/5xx)|HttpStatus|
|NumActiveWorkers|Liczba aktywnych procesów roboczych|Licznik|Maksimum|Liczba aktywnych procesów roboczych|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ObservedMetricValue|Obserwowana wartość metryki|Licznik|Średnia|Wartość obliczona przez automatyczne skalowanie podczas wykonywania|MetricTriggerSource|
|MetricThreshold|Próg metryki|Licznik|Średnia|Skonfigurowany próg automatycznego skalowania podczas uruchamiania automatycznego skalowania.|MetricTriggerRule|
|ObservedCapacity|Zaobserwowana pojemność|Licznik|Średnia|Pojemność zgłoszona do automatycznego skalowania podczas jego wykonywania.|None|
|ScaleActionsInitiated|Zainicjowane akcje skalowania|Licznik|Łącznie|Kierunek operacji skalowania.|ScaleDirection|




## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/składniki

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Dostępność|Procent|Średnia|Procent pomyślnie ukończonych testów dostępności|availabilityResult/nazwa, availabilityResult/lokalizacja|
|availabilityResults/liczba|Testy dostępności|Licznik|Licznik|Liczba testów dostępności|availabilityResult/nazwa, availabilityResult/lokalizacja, availabilityResult/sukces|
|availabilityResults/czas trwania|Czas trwania testu dostępności|MilliSeconds|Średnia|Czas trwania testu dostępności|availabilityResult/nazwa, availabilityResult/lokalizacja, availabilityResult/sukces|
|browserTimings/networkDuration|Czas połączenia sieciowego ładowania strony|MilliSeconds|Średnia|Czas między żądaniem użytkownika a połączeniem sieciowym. Obejmuje wyszukiwanie DNS i połączenie transportowe.|None|
|browserTimings/processingDuration|Czas przetwarzania klienta|MilliSeconds|Średnia|Czas między odebraniem ostatniego bajtu dokumentu do momentu załadowania modelu DOM. Żądania asynchroniczne nadal mogą być przetwarzane.|None|
|browserTimings/receiveDuration|Czas odpowiedzi na odebranie|MilliSeconds|Średnia|Czas między pierwszym i ostatnim bajtem lub do momentu odłączenia.|None|
|browserTimings/sendDuration|Czas żądania wysłania|MilliSeconds|Średnia|Czas między połączeniem sieciowym i odebraniem pierwszego bajtu.|None|
|browserTimings/totalDuration|Czas ładowania strony w przeglądarce|MilliSeconds|Średnia|Czas od żądania użytkownika do załadowania modelu DOM, arkuszy stylów, skryptów i obrazów.|None|
|zależności/liczba|Wywołania zależności|Licznik|Licznik|Liczba wywołań wykonanych przez aplikację do zasobów zewnętrznych.|zależność/typ, zależność/performanceBucket, zależność/powodzenie, zależność/cel, operacja/syntetyczne, Chmura/roleInstance, Chmura/rolename|
|zależności/czas trwania|Czas trwania zależności|MilliSeconds|Średnia|Czas trwania wywołań wykonanych przez aplikację do zasobów zewnętrznych.|zależność/typ, zależność/performanceBucket, zależność/powodzenie, zależność/cel, operacja/syntetyczne, Chmura/roleInstance, Chmura/rolename|
|zależności/niepowodzenie|Błędy wywołań zależności|Licznik|Licznik|Liczba wywołań zależności zakończonych niepowodzeniem wykonanych przez aplikację w zasobach zewnętrznych.|zależność/typ, zależność/performanceBucket, zależność/powodzenie, zależność/cel, operacja/syntetyczne, Chmura/roleInstance, Chmura/rolename|
|pageViews/liczba|Wyświetlenia stron|Licznik|Licznik|Liczba wyświetleń stron.|Operacja/syntetyczne, Chmura/rolename|
|pageViews/czas trwania|Czas ładowania widoku strony|MilliSeconds|Średnia|Czas ładowania widoku strony|Operacja/syntetyczne, Chmura/rolename|
|Liczniki wydajności/requestExecutionTime|Czas wykonywania żądania HTTP|MilliSeconds|Średnia|Czas wykonywania najnowszego żądania.|Chmura/roleInstance|
|performanceCounters/requestsInQueue|Żądania HTTP w kolejce aplikacji|Licznik|Średnia|Długość kolejki żądań aplikacji.|Chmura/roleInstance|
|performanceCounters/requestsPerSecond|Częstotliwość żądań HTTP|CountPerSecond|Średnia|Szybkość wszystkich żądań wysyłanych do aplikacji w ciągu sekundy od ASP.NET.|Chmura/roleInstance|
|Liczniki wydajności/exceptionsPerSecond|Częstotliwość wyjątków|CountPerSecond|Średnia|Liczba obsłużonych i nieobsłużonych wyjątków zgłoszonych w systemie Windows, łącznie z wyjątkami platformy .NET i niezarządzanymi wyjątkami przekonwertowanymi na wyjątki platformy .NET.|Chmura/roleInstance|
|Liczniki wydajności/processIOBytesPerSecond|Szybkość operacji we/wy procesu|BytesPerSecond|Średnia|Łączna liczba bajtów odczytanych i zapisywana w plikach, sieci i urządzeniach.|Chmura/roleInstance|
|Liczniki wydajności/processCpuPercentage|Procesor CPU procesu|Procent|Średnia|Wyrażony w procentach czas, przez jaki wszystkie wątki procesów używały procesora do wykonywania instrukcji. Może się to różnić od od 0 do 100. Ta Metryka wskazuje na wydajność samego procesu w3wp.|Chmura/roleInstance|
|Liczniki wydajności/processorCpuPercentage|Czas procesora|Procent|Średnia|Procent czasu spędzanego przez procesor w wątkach, które nie są bezczynne.|Chmura/roleInstance|
|Liczniki wydajności/memoryAvailableBytes|Dostępna pamięć|Bajty|Średnia|Pamięć fizyczna natychmiast dostępna do przydzielenia do procesu lub do użycia przez system.|Chmura/roleInstance|
|Liczniki wydajności/processPrivateBytes|Prywatne bajty procesu|Bajty|Średnia|Pamięć przypisana wyłącznie do procesów monitorowanej aplikacji.|Chmura/roleInstance|
|żądania/czas trwania|Czas odpowiedzi serwera|MilliSeconds|Średnia|Czas między odebraniem żądania HTTP i zakończeniem wysyłania odpowiedzi.|żądanie/performanceBucket, żądanie/resultCode, Operation/syntetyczne, Cloud/roleInstance, żądanie/powodzenie, Chmura/rolename|
|Liczba żądań na sekundę|Żądania serwera|Licznik|Licznik|Liczba ukończonych żądań HTTP.|żądanie/performanceBucket, żądanie/resultCode, Operation/syntetyczne, Cloud/roleInstance, żądanie/powodzenie, Chmura/rolename|
|żądania/niepowodzenie|Żądania zakończone niepowodzeniem|Licznik|Licznik|Liczba żądań HTTP oznaczonych jako zakończone niepowodzeniem. W większości przypadków są to żądania z kodem odpowiedzi > = 400, a nie równe 401.|żądanie/performanceBucket, żądanie/resultCode, żądanie/sukces, operacja/syntetyczne, Cloud/roleInstance, Cloud/rolename|
|żądania/częstotliwość|Liczba żądań serwera|CountPerSecond|Średnia|Liczba żądań serwera na sekundę|żądanie/performanceBucket, żądanie/resultCode, Operation/syntetyczne, Cloud/roleInstance, żądanie/powodzenie, Chmura/rolename|
|wyjątki/liczba|Wyjątki|Licznik|Licznik|Łączna liczba wszystkich nieprzechwyconych wyjątków.|Chmura/rolename, Cloud/roleInstance, klient/typ|
|wyjątki/przeglądarka|Wyjątki przeglądarki|Licznik|Licznik|Liczba nieprzechwyconych wyjątków zgłoszonych w przeglądarce.|Klient/isserwer, Chmura/rolename|
|wyjątki/serwer|Wyjątki serwera|Licznik|Licznik|Liczba nieprzechwyconych wyjątków zgłoszonych w aplikacji serwera.|Klient/isserwer, Chmura/rolename, Cloud/roleInstance|
|ślady/liczba|Ścieżki|Licznik|Licznik|Liczba dokumentów śledzenia|Trace/severityLevel, Operations/syntetyczne, Cloud/rolename, Cloud/roleInstance|



## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ServiceApiHit|Całkowita liczba trafień interfejsu API usługi|Licznik|Licznik|Łączna liczba trafień interfejsu API usługi|ActivityType, ActivityName|
|ServiceApiLatency|Ogólne opóźnienie interfejsu API usługi|Milisekundy|Średnia|Ogólne opóźnienie żądań interfejsu API usługi|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Łączna liczba wyników interfejsu API usługi|Licznik|Licznik|Łączna liczba wyników interfejsu API usługi|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|Ogólne nasycenie magazynu|Procent|Średnia|Używane pojemność magazynu|ActivityType, ActivityName, TransactionType|
|Dostępność|Ogólna dostępność magazynu|Procent|Średnia|Dostępność żądań magazynu|ActivityType, ActivityName, StatusCode, StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CacheUtilization|Użycie pamięci podręcznej|Procent|Średnia|Poziom użycia w zakresie klastra|None|
|QueryDuration|Czas trwania zapytania|Milisekundy|Średnia|Czas trwania zapytań (w sekundach)|QueryStatus|
|IngestionUtilization|Wykorzystanie pozyskiwania|Procent|Średnia|Współczynnik używania miejsc pozyskiwania w klastrze|None|
|KeepAlive|Utrzymywanie aktywności|Licznik|Średnia|Sprawdzenie Sanity wskazuje, że klaster reaguje na zapytania|None|
|IngestionVolumeInMB|Wolumin pozyskiwania (w MB)|Licznik|Łącznie|Ogólna ilość danych pozyskiwanych w klastrze (w MB)|Baza danych|
|IngestionLatencyInSeconds|Opóźnienie pozyskiwania (w sekundach)|S|Średnia|Czas pozyskiwania ze źródła (np. komunikat jest w centrum EventHub) do klastra w ciągu kilku sekund|None|
|EventsProcessedForEventHubs|Zdarzenia przetwarzane (dla centrów zdarzeń/IoT)|Licznik|Łącznie|Liczba zdarzeń przetwarzanych przez klaster podczas pozyskiwania ze zdarzenia/IoT Hub|EventStatus|
|IngestionResult|Wynik pozyskiwania|Licznik|Licznik|Liczba operacji pozyskiwania|IngestionResultDetails|
|Procesor CPU|Procesor CPU|Procent|Średnia|Poziom użycia procesora CPU|None|
|ContinuousExportNumOfRecordsExported|Eksport ciągły — liczba eksportowanych rekordów|Licznik|Łącznie|Liczba wyeksportowanych rekordów, które są generowane dla każdego artefaktu magazynu zapisanego podczas operacji eksportowania|None|
|ExportUtilization|Użycie eksportu|Procent|Maksimum|Użycie eksportu|None|
|ContinuousExportPendingCount|Liczba oczekujących eksportu ciągłego|Licznik|Maksimum|Liczba oczekujących zadań eksportu ciągłego gotowych do wykonania|None|
|ContinuousExportMaxLatenessMinutes|Maksymalna liczba minut opóźnienia eksportu|Licznik|Maksimum|Maksymalna liczba opóźnień w minutach wszystkich oczekujących eksportów ciągłych i gotowych do wykonania|None|
|ContinuousExportResult|Wynik eksportu ciągłego|Licznik|Licznik|Wskazuje, czy ciągły eksport zakończył się powodzeniem, czy niepowodzeniem|ContinuousExportName, wynik, baza danych|
|StreamingIngestDuration|Czas trwania pozyskiwania strumieniowego|Milisekundy|Średnia|Czas trwania pozyskiwania strumieniowego w milisekundach|None|
|StreamingIngestDataRate|Szybkość danych pozyskiwania przesyłania strumieniowego|Licznik|Średnia|Szybkość danych pozyskiwania przesyłania strumieniowego (MB na sekundę)|None|
|SteamingIngestRequestRate|Szybkość żądania pozyskiwania strumieniowego|Licznik|RateRequestsPerSecond|Szybkość żądania pozyskiwania strumieniowego (żądania na sekundę)|None|
|StreamingIngestResults|Wynik pozyskiwania strumieniowego|Licznik|Średnia|Wynik pozyskiwania strumieniowego|Wynik|
|TotalNumberOfConcurrentQueries|Łączna liczba współbieżnych zapytań|Licznik|Łącznie|Łączna liczba współbieżnych zapytań|None|
|TotalNumberOfThrottledQueries|Łączna liczba zapytań z ograniczeniami|Licznik|Łącznie|Łączna liczba zapytań z ograniczeniami|None|
|TotalNumberOfThrottledCommands|Łączna liczba poleceń z ograniczeniami|Licznik|Łącznie|Łączna liczba poleceń z ograniczeniami|CommandType|
|TotalNumberOfExtents|Łączna Liczba zakresów|Licznik|Łącznie|Łączna Liczba zakresów danych|None|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RunsStarted|Uruchomienia uruchomione|Licznik|Łącznie|Liczba uruchomionych przebiegów przepływu pracy.|None|
|RunsCompleted|Przebiegi zakończone|Licznik|Łącznie|Liczba ukończonych przebiegów przepływu pracy.|None|
|RunsSucceeded|Przebiegi zakończone powodzeniem|Licznik|Łącznie|Liczba przebiegów przepływu pracy zakończonych powodzeniem.|None|
|RunsFailed|Przebiegi zakończone niepowodzeniem|Licznik|Łącznie|Liczba przebiegów przepływu pracy zakończonych niepowodzeniem.|None|
|RunsCancelled|Przebiegi anulowane|Licznik|Łącznie|Liczba anulowanych przebiegów przepływu pracy.|None|
|RunLatency|Opóźnienie uruchamiania|S|Średnia|Opóźnienie ukończonych przebiegów przepływu pracy.|None|
|RunSuccessLatency|Opóźnienie sukcesu przebiegu|S|Średnia|Opóźnienie przebiegów przepływu pracy zakończonych powodzeniem.|None|
|RunThrottledEvents|Uruchamianie zdarzeń z ograniczeniami|Licznik|Łącznie|Liczba zdarzeń związanych ze zdarzeniami przepływu pracy lub wyzwalaczem.|None|
|RunStartThrottledEvents|Uruchom zdarzenia uruchamiania z ograniczeniami|Licznik|Łącznie|Liczba uruchomionych zdarzeń ograniczających przepływ pracy.|None|
|RunFailurePercentage|Procent niepowodzeń przebiegu|Procent|Łącznie|Procent przebiegów przepływu pracy zakończonych niepowodzeniem.|None|
|ActionsStarted|Akcje uruchomione |Licznik|Łącznie|Liczba rozpoczętych akcji przepływu pracy.|None|
|ActionsCompleted|Wykonane akcje |Licznik|Łącznie|Liczba ukończonych akcji przepływu pracy.|None|
|ActionsSucceeded|Akcje zakończone powodzeniem |Licznik|Łącznie|Liczba akcji przepływu pracy zakończonych powodzeniem.|None|
|ActionsFailed|Akcje zakończone niepowodzeniem |Licznik|Łącznie|Liczba akcji przepływu pracy zakończonych niepowodzeniem.|None|
|ActionsSkipped|Pominięte akcje |Licznik|Łącznie|Liczba pominiętych akcji przepływu pracy.|None|
|ActionLatency|Opóźnienie akcji |S|Średnia|Opóźnienie ukończonych akcji przepływu pracy.|None|
|ActionSuccessLatency|Opóźnienie sukcesu akcji |S|Średnia|Opóźnienie akcji przepływu pracy zakończonych powodzeniem.|None|
|ActionThrottledEvents|Zdarzenia ograniczające akcję|Licznik|Łącznie|Liczba zdarzeń ograniczających przepływ pracy...|None|
|TriggersStarted|Uruchomiono wyzwalacze |Licznik|Łącznie|Liczba uruchomionych wyzwalaczy przepływu pracy.|None|
|TriggersCompleted|Ukończone wyzwalacze |Licznik|Łącznie|Liczba ukończonych wyzwalaczy przepływu pracy.|None|
|TriggersSucceeded|Wyzwalacze zakończone powodzeniem |Licznik|Łącznie|Liczba wyzwalaczy przepływu pracy zakończonych powodzeniem.|None|
|TriggersFailed|Wyzwalacze zakończone niepowodzeniem |Licznik|Łącznie|Liczba wyzwalaczy przepływu pracy zakończonych niepowodzeniem.|None|
|TriggersSkipped|Pominięte wyzwalacze|Licznik|Łącznie|Liczba pominiętych wyzwalaczy przepływu pracy.|None|
|TriggersFired|Wyzwolone wyzwalacze |Licznik|Łącznie|Liczba wyzwolonych wyzwalaczy przepływu pracy.|None|
|TriggerLatency|Opóźnienie wyzwalacza |S|Średnia|Opóźnienie ukończonych wyzwalaczy przepływu pracy.|None|
|TriggerFireLatency|Opóźnienie wyzwalania wyzwalacza |S|Średnia|Opóźnienie wyzwalanych wyzwalaczy przepływu pracy.|None|
|TriggerSuccessLatency|Opóźnienie sukcesu wyzwalacza |S|Średnia|Opóźnienie wyzwalaczy przepływu pracy zakończonych powodzeniem.|None|
|TriggerThrottledEvents|Wyzwalaj zdarzenia ograniczające|Licznik|Łącznie|Liczba zdarzeń ograniczenia wyzwalacza przepływu pracy.|None|
|BillableActionExecutions|Rozliczane wykonania akcji|Licznik|Łącznie|Liczba rozliczanych wykonań akcji przepływu pracy.|None|
|BillableTriggerExecutions|Rozliczane wykonania wyzwalacza|Licznik|Łącznie|Liczba rozliczanych wykonań wyzwalacza przepływu pracy.|None|
|TotalBillableExecutions|Łączna Liczba wykonań rozliczanych|Licznik|Łącznie|Liczba rozliczanych wykonań przepływu pracy.|None|
|BillingUsageNativeOperation|Użycie rozliczeń dla natywnych wykonań operacji|Licznik|Łącznie|Liczba rozliczanych wykonań operacji natywnych.|None|
|BillingUsageStandardConnector|Użycie rozliczeń dla wykonywania łączników standardowych|Licznik|Łącznie|Liczba rozliczanych wykonań łączników standardowych.|None|
|BillingUsageStorageConsumption|Użycie rozliczeń dla wykonań zużycia magazynu|Licznik|Łącznie|Liczba rozliczanych wykonań zużycia magazynu.|None|
|BillingUsageNativeOperation|Użycie rozliczeń dla natywnych wykonań operacji|Licznik|Łącznie|Liczba rozliczanych wykonań operacji natywnych.|None|
|BillingUsageStandardConnector|Użycie rozliczeń dla wykonywania łączników standardowych|Licznik|Łącznie|Liczba rozliczanych wykonań łączników standardowych.|None|
|BillingUsageStorageConsumption|Użycie rozliczeń dla wykonań zużycia magazynu|Licznik|Łącznie|Liczba rozliczanych wykonań zużycia magazynu.|None|



## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RunsStarted|Uruchomienia uruchomione|Licznik|Łącznie|Liczba uruchomionych przebiegów przepływu pracy.|None|
|RunsCompleted|Przebiegi zakończone|Licznik|Łącznie|Liczba ukończonych przebiegów przepływu pracy.|None|
|RunsSucceeded|Przebiegi zakończone powodzeniem|Licznik|Łącznie|Liczba przebiegów przepływu pracy zakończonych powodzeniem.|None|
|RunsFailed|Przebiegi zakończone niepowodzeniem|Licznik|Łącznie|Liczba przebiegów przepływu pracy zakończonych niepowodzeniem.|None|
|RunsCancelled|Przebiegi anulowane|Licznik|Łącznie|Liczba anulowanych przebiegów przepływu pracy.|None|
|RunLatency|Opóźnienie uruchamiania|S|Średnia|Opóźnienie ukończonych przebiegów przepływu pracy.|None|
|RunSuccessLatency|Opóźnienie sukcesu przebiegu|S|Średnia|Opóźnienie przebiegów przepływu pracy zakończonych powodzeniem.|None|
|RunThrottledEvents|Uruchamianie zdarzeń z ograniczeniami|Licznik|Łącznie|Liczba zdarzeń związanych ze zdarzeniami przepływu pracy lub wyzwalaczem.|None|
|RunStartThrottledEvents|Uruchom zdarzenia uruchamiania z ograniczeniami|Licznik|Łącznie|Liczba uruchomionych zdarzeń ograniczających przepływ pracy.|None|
|RunFailurePercentage|Procent niepowodzeń przebiegu|Procent|Łącznie|Procent przebiegów przepływu pracy zakończonych niepowodzeniem.|None|
|ActionsStarted|Akcje uruchomione |Licznik|Łącznie|Liczba rozpoczętych akcji przepływu pracy.|None|
|ActionsCompleted|Wykonane akcje |Licznik|Łącznie|Liczba ukończonych akcji przepływu pracy.|None|
|ActionsSucceeded|Akcje zakończone powodzeniem |Licznik|Łącznie|Liczba akcji przepływu pracy zakończonych powodzeniem.|None|
|ActionsFailed|Akcje zakończone niepowodzeniem |Licznik|Łącznie|Liczba akcji przepływu pracy zakończonych niepowodzeniem.|None|
|ActionsSkipped|Pominięte akcje |Licznik|Łącznie|Liczba pominiętych akcji przepływu pracy.|None|
|ActionLatency|Opóźnienie akcji |S|Średnia|Opóźnienie ukończonych akcji przepływu pracy.|None|
|ActionSuccessLatency|Opóźnienie sukcesu akcji |S|Średnia|Opóźnienie akcji przepływu pracy zakończonych powodzeniem.|None|
|ActionThrottledEvents|Zdarzenia ograniczające akcję|Licznik|Łącznie|Liczba zdarzeń ograniczających przepływ pracy...|None|
|TriggersStarted|Uruchomiono wyzwalacze |Licznik|Łącznie|Liczba uruchomionych wyzwalaczy przepływu pracy.|None|
|TriggersCompleted|Ukończone wyzwalacze |Licznik|Łącznie|Liczba ukończonych wyzwalaczy przepływu pracy.|None|
|TriggersSucceeded|Wyzwalacze zakończone powodzeniem |Licznik|Łącznie|Liczba wyzwalaczy przepływu pracy zakończonych powodzeniem.|None|
|TriggersFailed|Wyzwalacze zakończone niepowodzeniem |Licznik|Łącznie|Liczba wyzwalaczy przepływu pracy zakończonych niepowodzeniem.|None|
|TriggersSkipped|Pominięte wyzwalacze|Licznik|Łącznie|Liczba pominiętych wyzwalaczy przepływu pracy.|None|
|TriggersFired|Wyzwolone wyzwalacze |Licznik|Łącznie|Liczba wyzwolonych wyzwalaczy przepływu pracy.|None|
|TriggerLatency|Opóźnienie wyzwalacza |S|Średnia|Opóźnienie ukończonych wyzwalaczy przepływu pracy.|None|
|TriggerFireLatency|Opóźnienie wyzwalania wyzwalacza |S|Średnia|Opóźnienie wyzwalanych wyzwalaczy przepływu pracy.|None|
|TriggerSuccessLatency|Opóźnienie sukcesu wyzwalacza |S|Średnia|Opóźnienie wyzwalaczy przepływu pracy zakończonych powodzeniem.|None|
|TriggerThrottledEvents|Wyzwalaj zdarzenia ograniczające|Licznik|Łącznie|Liczba zdarzeń ograniczenia wyzwalacza przepływu pracy.|None|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Użycie procesora przepływu pracy dla środowisko usługi integracji|Procent|Średnia|Użycie procesora przez przepływ pracy dla środowiska usługi integracji.|None|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Użycie pamięci przez przepływ pracy dla środowisko usługi integracji|Procent|Średnia|Użycie pamięci przez przepływ pracy dla środowiska usługi integracji.|None|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Użycie procesora przez łącznik dla środowisko usługi integracji|Procent|Średnia|Użycie procesora przez łącznik dla środowiska usługi integracji.|None|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Użycie pamięci przez łącznik dla środowisko usługi integracji|Procent|Średnia|Użycie pamięci przez łącznik dla środowiska usługi integracji.|None|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Ukończone uruchomienia|Ukończone uruchomienia|Licznik|Łącznie|Liczba przebiegów pomyślnie ukończonych dla tego obszaru roboczego|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Uruchomione uruchomienia|Uruchomione uruchomienia|Licznik|Łącznie|Liczba rozpoczętych uruchomień dla tego obszaru roboczego|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Nieudane uruchomienia|Nieudane uruchomienia|Licznik|Łącznie|Liczba uruchomień dla tego obszaru roboczego nie powiodła się|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Rejestrowanie modelu powiodło się|Rejestrowanie modelu powiodło się|Licznik|Łącznie|Liczba rejestracji modelu zakończonych powodzeniem w tym obszarze roboczym|Scenariusz|
|Nie można zarejestrować modelu|Nie można zarejestrować modelu|Licznik|Łącznie|Liczba rejestracji modelu zakończonych niepowodzeniem w tym obszarze roboczym|Scenariusz, StatusCode|
|Uruchomiono Wdrażanie modelu|Uruchomiono Wdrażanie modelu|Licznik|Łącznie|Liczba rozpoczętych wdrożeń modelu w tym obszarze roboczym|Scenariusz|
|Wdrażanie modelu powiodło się|Wdrażanie modelu powiodło się|Licznik|Łącznie|Liczba wdrożeń modelu, które zakończyły się pomyślnie w tym obszarze roboczym|Scenariusz|
|Wdrażanie modelu nie powiodło się|Wdrażanie modelu nie powiodło się|Licznik|Łącznie|Liczba wdrożeń modelu, które zakończyły się niepowodzeniem w tym obszarze roboczym|Scenariusz, StatusCode|
|Łączna liczba węzłów|Łączna liczba węzłów|Licznik|Średnia|Łączna liczba węzłów. Ta suma obejmuje niektóre aktywne węzły, bezczynne węzły, węzły niezdatne do użytku, węzły zastępujące, pozostawiając węzły|Scenariusz, ClusterName|
|Aktywne węzły|Aktywne węzły|Licznik|Średnia|Liczba węzłów Active. Są to węzły, w których aktywnie uruchomiono zadanie.|Scenariusz, ClusterName|
|Węzły bezczynne|Węzły bezczynne|Licznik|Średnia|Liczba bezczynnych węzłów. Węzły bezczynne są węzłami, w których nie są uruchomione żadne zadania, ale mogą akceptować nowe zadanie, jeśli jest dostępne.|Scenariusz, ClusterName|
|Węzły niezdatne do użytku|Węzły niezdatne do użytku|Licznik|Średnia|Liczba nieużywanych węzłów. Węzły, które nie są użyteczne, nie działają z powodu problemu z nierozpoznawalne. Platforma Azure przeprowadzi odtwarzanie tych węzłów.|Scenariusz, ClusterName|
|Zastępujące węzły|Zastępujące węzły|Licznik|Średnia|Liczba przeniesiona węzłów. Te węzły są węzłami o niskim priorytecie, które znajdują się poza dostępną pulą węzłów.|Scenariusz, ClusterName|
|Opuszczanie węzłów|Opuszczanie węzłów|Licznik|Średnia|Liczba pozostałych węzłów. Opuszczenie węzłów to węzły, które po prostu zakończyły przetwarzanie zadania i przechodzą w stan bezczynności.|Scenariusz, ClusterName|
|Łączna liczba rdzeni|Łączna liczba rdzeni|Licznik|Średnia|Łączna liczba rdzeni|Scenariusz, ClusterName|
|Aktywne rdzenie|Aktywne rdzenie|Licznik|Średnia|Liczba aktywnych rdzeni|Scenariusz, ClusterName|
|Rdzenie bezczynne|Rdzenie bezczynne|Licznik|Średnia|Liczba rdzeni bezczynnych|Scenariusz, ClusterName|
|Rdzenie, których nie można używać|Rdzenie, których nie można używać|Licznik|Średnia|Liczba rdzeni, których nie można używać|Scenariusz, ClusterName|
|Występujące rdzenie|Występujące rdzenie|Licznik|Średnia|Liczba przeniesiona rdzeni|Scenariusz, ClusterName|
|Opuszczanie rdzeni|Opuszczanie rdzeni|Licznik|Średnia|Liczba rdzeni wychodzących|Scenariusz, ClusterName|
|Procent wykorzystania przydziałów|Procent wykorzystania przydziałów|Licznik|Średnia|Procent wykorzystania przydziałów|Scenariusz, ClusterName, VmFamilyName, VmPriority|
|CpuUtilization|CpuUtilization|Licznik|Średnia|Procesor CPU (wersja zapoznawcza)|Scenariusz, runId, NodeId, CreatedTime|
|GpuUtilization|GpuUtilization|Licznik|Średnia|Procesor GPU (wersja zapoznawcza)|Scenariusz, runId, NodeId, CreatedTime, DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/konta

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Sposób użycia|Sposób użycia|Licznik|Licznik|Liczba wywołań interfejsu API|ApiCategory, ApiName, ResultType, ResponseCode|
|Dostępność|Dostępność|Procent|Średnia|Dostępność interfejsów API|ApiCategory, ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft. Media/MediaServices/streamingEndpoints

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wyjściowych w bajtach.|OutputFormat|
|SuccessE2ELatency|Pomyślne zakończenie oczekiwania|Milisekundy|Średnia|Średnie opóźnienie dla pomyślnych żądań w milisekundach.|OutputFormat|
|Żądania|Żądania|Licznik|Łącznie|Żądania do punktu końcowego przesyłania strumieniowego.|OutputFormat, HttpStatusCode, ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/MediaServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AssetQuota|Przydział zasobów|Licznik|Średnia|Ile zasobów jest dozwolonych dla bieżącego konta usługi multimediów|None|
|AssetCount|Liczba zasobów|Licznik|Średnia|Ile zasobów zostało już utworzonych na bieżącym koncie usługi multimediów|None|
|AssetQuotaUsedPercentage|Procent użycia przydziału zasobów|Procent|Średnia|Procent użycia zasobów w bieżącym koncie usługi multimediów|None|
|ContentKeyPolicyQuota|Przydział zasad dotyczących kluczy zawartości|Licznik|Średnia|Ile zasad kluczy zawartości jest dozwolonych dla bieżącego konta usługi multimediów|None|
|ContentKeyPolicyCount|Liczba zasad dotyczących kluczy zawartości|Licznik|Średnia|Ile zasad kluczy zawartości zostało już utworzonych na bieżącym koncie usługi multimediów|None|
|ContentKeyPolicyQuotaUsedPercentage|Procent użycia przydziału zasad klucza zawartości|Procent|Średnia|Procent użycia zasad klucza zawartości w bieżącym koncie usługi multimediów|None|
|StreamingPolicyQuota|Limit przydziału zasad przesyłania strumieniowego|Licznik|Średnia|Ile zasad przesyłania strumieniowego jest dozwolonych dla bieżącego konta usługi multimediów|None|
|StreamingPolicyCount|Liczba zasad przesyłania strumieniowego|Licznik|Średnia|Ile zasad przesyłania strumieniowego zostało już utworzonych na bieżącym koncie usługi multimediów|None|
|StreamingPolicyQuotaUsedPercentage|Procent użycia limitu przydziału zasad przesyłania strumieniowego|Procent|Średnia|Procent użycia zasad przesyłania strumieniowego w bieżącym koncie usługi multimediów|None|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/woluminy

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AverageReadLatency|Średnie opóźnienie odczytu|MilliSeconds|Średnia|Średnie opóźnienie odczytu w milisekundach na operację|None|
|AverageWriteLatency|Średnie opóźnienie zapisu|MilliSeconds|Średnia|Średnie opóźnienie zapisu w milisekundach na operację|None|
|VolumeLogicalSize|Rozmiar logiczny woluminu|Bajty|Średnia|Rozmiar logiczny woluminu (zużyte bajty)|None|
|VolumeSnapshotSize|Rozmiar migawki woluminu|Bajty|Średnia|Rozmiar wszystkich migawek w woluminie|None|
|ReadIops|Odczyt operacji we/wy|CountPerSecond|Średnia|Operacje odczytu/out na sekundę|None|
|WriteIops|Zapisz operacje we/wy|CountPerSecond|Średnia|Operacje zapisu/wylogowania na sekundę|None|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Użyte przydzieloną pulę woluminów|Bajty|Średnia|Przydzielono używany rozmiar puli|None|
|VolumePoolTotalLogicalSize|Łączny rozmiar logiczny puli woluminów|Bajty|Średnia|Suma rozmiaru logicznego wszystkich woluminów należących do puli|None|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BytesSentRate|Bajty wysłane|Bajty|Łącznie|Liczba bajtów wysłanych przez interfejs sieciowy|None|
|BytesReceivedRate|Bajty odebrane|Bajty|Łącznie|Liczba bajtów odebranych przez interfejs sieciowy|None|
|PacketsSentRate|Wysłane pakiety|Licznik|Łącznie|Liczba pakietów wysłanych przez interfejs sieciowy|None|
|PacketsReceivedRate|Odebrane pakiety|Licznik|Łącznie|Liczba pakietów odebranych przez interfejs sieciowy|None|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|VipAvailability|Dostępność ścieżki danych|Licznik|Średnia|Średnia dostępność ścieżki danych Load Balancer na czas trwania|FrontendIPAddress,FrontendPort|
|DipAvailability|Stan sondy kondycji|Licznik|Średnia|Średni stan sondy kondycji Load Balancer na czas trwania|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Liczba bajtów|Licznik|Łącznie|Łączna liczba bajtów przesłanych w okresie|FrontendIPAddress, FrontendPort, kierunek|
|PacketCount|Liczba pakietów|Licznik|Łącznie|Łączna liczba pakietów wysłanych w czasie|FrontendIPAddress, FrontendPort, kierunek|
|SYNCount|Liczba SYN|Licznik|Łącznie|Łączna liczba pakietów SYN wysłanych w czasie|FrontendIPAddress, FrontendPort, kierunek|
|SnatConnectionCount|Liczba połączeń z translatorem adresów sieciowych|Licznik|Łącznie|Łączna liczba nowych połączeń z przyłączaniem do adresów w czasie|FrontendIPAddress,BackendIPAddress,ConnectionState|
|AllocatedSnatPorts|Przydzielono porty przydziałów (wersja zapoznawcza)|Licznik|Łącznie|Łączna liczba portów przydzieloną porty adresów sieciowych w okresie|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|
|UsedSnatPorts|Używane porty (wersja zapoznawcza)|Licznik|Łącznie|Łączna liczba portów przydziałów adresów sieciowych używanych w czasie|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QueryVolume|Wolumin zapytania|Licznik|Łącznie|Liczba zapytań obsłużonych dla strefy DNS|None|
|RecordSetCount|Liczba zestawów rekordów|Licznik|Maksimum|Liczba zestawów rekordów w strefie DNS|None|
|RecordSetCapacityUtilization|Wykorzystanie pojemności zestawu rekordów|Procent|Maksimum|Procent pojemności zestawu rekordów używany przez strefę DNS|None|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PacketsInDDoS|Pakiety przychodzące DDoS|CountPerSecond|Maksimum|Pakiety przychodzące DDoS|None|
|PacketsDroppedDDoS|Odrzucone pakiety przychodzące DDoS|CountPerSecond|Maksimum|Odrzucone pakiety przychodzące DDoS|None|
|PacketsForwardedDDoS|Przekazane pakiety przychodzące DDoS|CountPerSecond|Maksimum|Przekazane pakiety przychodzące DDoS|None|
|TCPPacketsInDDoS|Przychodzące pakiety TCP DDoS|CountPerSecond|Maksimum|Przychodzące pakiety TCP DDoS|None|
|TCPPacketsDroppedDDoS|Liczba porzuconych pakietów TCP przychodzących DDoS|CountPerSecond|Maksimum|Liczba porzuconych pakietów TCP przychodzących DDoS|None|
|TCPPacketsForwardedDDoS|Przychodzące pakiety TCP przesłane dalej DDoS|CountPerSecond|Maksimum|Przychodzące pakiety TCP przesłane dalej DDoS|None|
|UDPPacketsInDDoS|Przychodzące pakiety UDP DDoS|CountPerSecond|Maksimum|Przychodzące pakiety UDP DDoS|None|
|UDPPacketsDroppedDDoS|Liczba porzuconych pakietów przychodzących UDP DDoS|CountPerSecond|Maksimum|Liczba porzuconych pakietów przychodzących UDP DDoS|None|
|UDPPacketsForwardedDDoS|Przychodzące pakiety UDP DDoS przesłane dalej|CountPerSecond|Maksimum|Przychodzące pakiety UDP DDoS przesłane dalej|None|
|BytesInDDoS|Bajty przychodzące DDoS|BytesPerSecond|Maksimum|Bajty przychodzące DDoS|None|
|BytesDroppedDDoS|Bajty przychodzące opuszczone DDoS|BytesPerSecond|Maksimum|Bajty przychodzące opuszczone DDoS|None|
|BytesForwardedDDoS|Przekazane bajty przychodzące DDoS|BytesPerSecond|Maksimum|Przekazane bajty przychodzące DDoS|None|
|TCPBytesInDDoS|Przychodzące bajty TCP DDoS|BytesPerSecond|Maksimum|Przychodzące bajty TCP DDoS|None|
|TCPBytesDroppedDDoS|Liczba porzuconych bajtów przychodzących protokołu TCP DDoS|BytesPerSecond|Maksimum|Liczba porzuconych bajtów przychodzących protokołu TCP DDoS|None|
|TCPBytesForwardedDDoS|Przekazane DDoS przychodzące bajty TCP|BytesPerSecond|Maksimum|Przekazane DDoS przychodzące bajty TCP|None|
|UDPBytesInDDoS|Przychodzące bajty UDP DDoS|BytesPerSecond|Maksimum|Przychodzące bajty UDP DDoS|None|
|UDPBytesDroppedDDoS|Liczba porzuconych bajtów przychodzących UDP DDoS|BytesPerSecond|Maksimum|Liczba porzuconych bajtów przychodzących UDP DDoS|None|
|UDPBytesForwardedDDoS|Przekazane przychodzące bajty UDP DDoS|BytesPerSecond|Maksimum|Przekazane przychodzące bajty UDP DDoS|None|
|IfUnderDDoSAttack|W obszarze atak DDoS|Licznik|Maksimum|W obszarze atak DDoS|None|
|DDoSTriggerTCPPackets|Przychodzące pakiety TCP do wyzwalania łagodzenia DDoS|CountPerSecond|Maksimum|Przychodzące pakiety TCP do wyzwalania łagodzenia DDoS|None|
|DDoSTriggerUDPPackets|Przychodzące pakiety UDP do wyzwalania łagodzenia DDoS|CountPerSecond|Maksimum|Przychodzące pakiety UDP do wyzwalania łagodzenia DDoS|None|
|DDoSTriggerSYNPackets|Pakiety przychodzących SYN wyzwalające łagodzenie DDoS|CountPerSecond|Maksimum|Pakiety przychodzących SYN wyzwalające łagodzenie DDoS|None|
|VipAvailability|Dostępność ścieżki danych|Licznik|Średnia|Średnia dostępność adresów IP na czas trwania|Port|
|ByteCount|Liczba bajtów|Licznik|Łącznie|Łączna liczba bajtów przesłanych w okresie|Port, kierunek|
|PacketCount|Liczba pakietów|Licznik|Łącznie|Łączna liczba pakietów wysłanych w czasie|Port, kierunek|
|SynCount|Liczba SYN|Licznik|Łącznie|Łączna liczba pakietów SYN wysłanych w czasie|Port, kierunek|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Czas błądzenia dla poleceń ping dla maszyny wirtualnej|MilliSeconds|Średnia|Czas błądzenia dla poleceń ping wysyłanych do docelowej maszyny wirtualnej|SourceCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Nieudane polecenia ping do maszyny wirtualnej|Procent|Średnia|Procent liczby zakończonych niepowodzeniem poleceń ping do całkowitego wysłania poleceń ping dla docelowej maszyny wirtualnej|SourceCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ApplicationRuleHit|Liczba trafień reguł aplikacji|Licznik|Łącznie|Liczba trafień reguł aplikacji|Stan, powód, protokół|
|NetworkRuleHit|Liczba trafień reguł sieci|Licznik|Łącznie|Liczba trafień reguł sieci|Stan, powód, protokół|
|FirewallHealth|Stan kondycji zapory|Procent|Średnia|Stan kondycji zapory|Stan, Przyczyna|
|Przetwarzanie dataprocessed|Przetworzone dane|Bajty|Łącznie|Łączna ilość danych przetworzonych przez zaporę|None|
|SNATPortUtilization|Wykorzystanie portów przez przytranslatora adresów sieciowych|Procent|Średnia|Wykorzystanie portów przez przytranslatora adresów sieciowych|None|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Przepływność|Przepływność|BytesPerSecond|Średnia|Liczba bajtów na sekundę obsłużonych przez Application Gateway|None|
|UnhealthyHostCount|Liczba hostów w złej kondycji|Licznik|Średnia|Liczba hostów zaplecza w złej kondycji|BackendSettingsPool|
|HealthyHostCount|Liczba hostów w dobrej kondycji|Licznik|Średnia|Liczba hostów zaplecza w dobrej kondycji|BackendSettingsPool|
|TotalRequests|Łączna liczba żądań|Licznik|Łącznie|Liczba pomyślnych żądań obsłużonych przez Application Gateway|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|Liczba żądań na minutę na hosta w dobrej kondycji|Licznik|Średnia|Średnia liczba żądań na minutę na hosta zaplecza w dobrej kondycji w puli|BackendSettingsPool|
|FailedRequests|Żądania zakończone niepowodzeniem|Licznik|Łącznie|Liczba żądań zakończonych niepowodzeniem, które zostały obsłużone przez Application Gateway|BackendSettingsPool|
|ResponseStatus|Stan odpowiedzi|Licznik|Łącznie|Stan odpowiedzi HTTP zwrócony przez Application Gateway|HttpStatusGroup|
|Wartości CurrentConnections|Bieżące połączenia|Licznik|Łącznie|Liczba bieżących połączeń ustanowionych z Application Gateway|None|
|CpuUtilization|Użycie procesora CPU|Procent|Średnia|Bieżące wykorzystanie procesora CPU Application Gateway|None|
|CapacityUnits|Bieżące jednostki wydajności|Licznik|Średnia|Zużyte jednostki wydajności|None|
|ComputeUnits|Bieżące jednostki obliczeniowe|Licznik|Średnia|Wykorzystane jednostki obliczeniowe|None|
|BackendResponseStatus|Stan odpowiedzi zaplecza|Licznik|Łącznie|Liczba kodów odpowiedzi HTTP wygenerowanych przez składowe zaplecza. Nie obejmuje to żadnych kodów odpowiedzi wygenerowanych przez Application Gateway.|BackendServer, ustawień httpsettings elementu, BackendHttpSetting, HttpStatusGroup|
|TlsProtocol|Protokół TLS klienta|Licznik|Łącznie|Liczba żądań TLS i innych niż TLS zainicjowanych przez klienta, które ustanowiły połączenie z Application Gateway. Aby wyświetlić dystrybucję protokołu TLS, należy filtrować według protokołu TLS wymiaru.|Odbiornik, TlsProtocol|
|BytesSent|Bajty wysłane|Bajty|Łącznie|Całkowita liczba bajtów wysłanych przez Application Gateway do klientów|Odbiornika|
|BytesReceived|Bajty odebrane|Bajty|Łącznie|Całkowita liczba bajtów odebranych przez Application Gateway od klientów|Odbiornika|
|ClientRtt|Czas RTT klienta|MilliSeconds|Średnia|Średni czas błądzenia między klientami a Application Gateway. Ta Metryka wskazuje, jak długo trwa ustanawianie połączeń i zwracanie potwierdzeń|Odbiornika|
|ApplicationGatewayTotalTime|Łączny czas Application Gateway|MilliSeconds|Średnia|Średni czas przetwarzania żądania i jego odpowiedź do wysłania. Ta wartość jest obliczana jako średnia interwału od momentu, kiedy Application Gateway otrzymuje pierwszy bajt żądania HTTP do momentu zakończenia operacji wysyłania odpowiedzi. Należy pamiętać, że zwykle obejmuje to czas przetwarzania Application Gateway, czas, przez który pakiety żądań i odpowiedzi są przesyłane przez sieć i czas odpowiedzi serwera wewnętrznej bazy danych.|Odbiornika|
|BackendConnectTime|Czas połączenia z zapleczem|MilliSeconds|Średnia|Czas nawiązywania połączenia z serwerem wewnętrznej bazy danych|Listener, BackendServer, ustawień httpsettings elementu, BackendHttpSetting|
|BackendFirstByteResponseTime|Czas odpowiedzi na pierwszy bajt zaplecza|MilliSeconds|Średnia|Interwał czasu między rozpoczęciem ustanawiania połączenia z serwerem zaplecza i otrzymywania pierwszego bajtu nagłówka odpowiedzi, który zbliża czas przetwarzania serwera wewnętrznej bazy danych|Listener, BackendServer, ustawień httpsettings elementu, BackendHttpSetting|
|BackendLastByteResponseTime|Czas odpowiedzi ostatniego bajtu wewnętrznej bazy danych|MilliSeconds|Średnia|Interwał czasu między rozpoczęciem ustanawiania połączenia z serwerem zaplecza i otrzymywania ostatniego bajtu treści odpowiedzi|Listener, BackendServer, ustawień httpsettings elementu, BackendHttpSetting|
|MatchedCount|Dystrybucja reguł dla zapory aplikacji sieci Web|Licznik|Łącznie|Dystrybucja reguły dla ruchu przychodzącego przez zaporę aplikacji sieci Web|Rule, RuleId|
|BlockedCount|Dystrybucja reguły zablokowanych żądań zapory aplikacji sieci Web|Licznik|Łącznie|Dystrybucja reguły zablokowanych żądań zapory aplikacji sieci Web|Rule, RuleId|
|BlockedReqCount|Liczba zablokowanych żądań zapory aplikacji sieci Web|Licznik|Łącznie|Liczba zablokowanych żądań zapory aplikacji sieci Web|None|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AverageBandwidth|Przepustowość S2S bramy|BytesPerSecond|Średnia|Średnia przepustowość lokacja-lokacja bramy w bajtach na sekundę|None|
|P2SBandwidth|Przepustowość P2S bramy|BytesPerSecond|Średnia|Średnia przepustowość typu punkt-lokacja bramy w bajtach na sekundę|None|
|P2SConnectionCount|Liczba połączeń P2S|Licznik|Maksimum|Liczba połączeń punkt-lokacja bramy|Protokół|
|TunnelAverageBandwidth|Przepustowość tunelu|BytesPerSecond|Średnia|Średnia przepustowość tunelu w bajtach na sekundę|ConnectionName, RemoteIP|
|TunnelEgressBytes|Bajty wychodzące tunelu|Bajty|Łącznie|Wychodzące bajty tunelu|ConnectionName, RemoteIP|
|TunnelIngressBytes|Bajty transferu danych wejściowych tunelu|Bajty|Łącznie|Przychodzące bajty tunelu|ConnectionName, RemoteIP|
|TunnelEgressPackets|Pakiety wychodzące tuneli|Licznik|Łącznie|Liczba wychodzących pakietów tunelu|ConnectionName, RemoteIP|
|TunnelIngressPackets|Pakiety przychodzące tunelu|Licznik|Łącznie|Liczba przychodzących pakietów tunelu|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Porzucanie pakietów wychodzących przez tunelowanie|Licznik|Łącznie|Liczba porzucenia pakietów wychodzących z niezgodności selektora ruchu tunelu|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Wyrzucanie niezgodności pakietów przez tunelowanie|Licznik|Łącznie|Liczba porzucenia pakietów przychodzących z niezgodności selektora ruchu tunelu|ConnectionName, RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft. Network/expressRoutePorts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|Licznik|Średnia|Poziom oświetlenia odbierania w dBm|Link, tor|
|TxLightLevel|TxLightLevel|Licznik|Średnia|Poziom oświetlenia TX w dBm|Link, tor|
|AdminState|AdminState|Licznik|Średnia|Stan administratora portu|Link|
|LineProtocol|LineProtocol|Licznik|Średnia|Stan protokołu wiersza portu|Link|
|PortBitsInPerSecond|BitsInPerSecond|CountPerSecond|Średnia|Usługa BITS na sekundę na platformie Azure|Link|
|PortBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Średnia|Ruch przychodzący z platformy Azure na sekundę|Link|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Średnia|Usługa BITS na sekundę na platformie Azure|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Średnia|Ruch przychodzący z platformy Azure na sekundę|PeeringType|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|CountPerSecond|Średnia|Usługa BITS na sekundę na platformie Azure|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|CountPerSecond|Średnia|Ruch przychodzący z platformy Azure na sekundę|PeeredCircuitSKey|
|BgpAvailability|Dostępność protokołu BGP|Procent|Średnia|Dostępność protokołu BGP od MSEE do wszystkich elementów równorzędnych.|PeeringType, element równorzędny|
|ArpAvailability|Dostępność protokołu ARP|Procent|Średnia|Dostępność protokołu ARP od MSEE do wszystkich elementów równorzędnych.|PeeringType, element równorzędny|
|QosDropBitsInPerSecond|DroppedInBitsPerSecond|CountPerSecond|Średnia|Liczba porzuconych bitów danych przychodzących na sekundę|None|
|QosDropBitsOutPerSecond|DroppedOutBitsPerSecond|CountPerSecond|Średnia|Liczba porzuconych bitów danych wyjściowych na sekundę|None|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/expressRouteCircuits/Komunikacja równorzędna

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Średnia|Usługa BITS na sekundę na platformie Azure|None|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Średnia|Ruch przychodzący z platformy Azure na sekundę|None|

## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Średnia|Usługa BITS na sekundę na platformie Azure|None|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Średnia|Ruch przychodzący z platformy Azure na sekundę|None|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft. Network/expressRouteGateways

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond|CountPerSecond|Średnia|Usługa BITS na sekundę na platformie Azure|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Średnia|Ruch przychodzący z platformy Azure na sekundę|ConnectionName|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QpsByEndpoint|Zwrócone zapytania według punktu końcowego|Licznik|Łącznie|Liczba zwróconych punktów końcowych Traffic Manager w danym przedziale czasu|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Stan punktu końcowego według punktu końcowego|Licznik|Maksimum|1 Jeśli sonda punktu końcowego ma stan "włączone", 0 w przeciwnym razie.|EndpointName|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Sond nie powiodło się|Procent|Średnia|% sond monitorowania łączności nie powiodło się|None|
|AverageRoundtripMs|Średni czas błądzenia (MS)|MilliSeconds|Średnia|Średni czas błądzenia sieci (MS) dla sond monitorowania łączności przesyłanych między źródłem a miejscem docelowym|None|
|ChecksFailedPercent|Sprawdza procent niepowodzenia (wersja zapoznawcza)|Procent|Średnia|% testów monitorowania łączności nie powiodło się|SourceAddress, SourceName, identyfikator sourceresourceid, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|
|RoundTripTimeMs|Czas błądzenia (MS) (wersja zapoznawcza)|MilliSeconds|Średnia|Czas błądzenia w milisekundach dla kontroli monitorowania łączności|SourceAddress, SourceName, identyfikator sourceresourceid, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/usługi frontdoor

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RequestCount|Liczba żądań|Licznik|Łącznie|Liczba żądań klientów obsłużonych przez serwer proxy HTTP/S|Wartości httpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Rozmiar żądania|Bajty|Łącznie|Liczba bajtów wysłanych jako żądania od klientów do serwera proxy HTTP/S|Wartości httpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Rozmiar odpowiedzi|Bajty|Łącznie|Liczba bajtów wysłanych jako odpowiedzi z serwera proxy HTTP/S do klientów|Wartości httpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BillableResponseSize|Rozmiar odpowiedzi do rozliczenia|Bajty|Łącznie|Liczba bajtów rozliczanych (minimalna 2KB na żądanie) wysyłanych jako odpowiedzi z serwera proxy HTTP/S do klientów.|Wartości httpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Liczba żądań wewnętrznej bazy danych|Licznik|Łącznie|Liczba żądań wysyłanych z serwera proxy HTTP/S do frontonu|Wartości httpStatus, HttpStatusGroup, zaplecze|
|BackendRequestLatency|Opóźnienie żądania wewnętrznej bazy danych|MilliSeconds|Średnia|Czas obliczony od momentu wysłania żądania przez serwer proxy HTTP/S do zaplecza do momentu odebrania przez serwer proxy HTTP/S ostatniego bajtu odpowiedzi z zaplecza|Danych|
|TotalLatency|Łączne opóźnienie|MilliSeconds|Średnia|Czas obliczony od momentu odebrania żądania klienta przez serwer proxy HTTP/S do momentu potwierdzenia przez klienta ostatniego bajtu odpowiedzi z serwera proxy HTTP/S|Wartości httpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Procent kondycji zaplecza|Procent|Średnia|Procent pomyślnych sond kondycji z serwera proxy HTTP/S do frontonu|Zaplecze, ustawień httpsettings elementu|
|WebApplicationFirewallRequestCount|Liczba żądań zapory aplikacji sieci Web|Licznik|Łącznie|Liczba żądań klientów przetworzonych przez zaporę aplikacji sieci Web|PolicyName, RuleName, Akcja|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Rejestracja. wszystkie|Operacje rejestracji|Licznik|Łącznie|Liczba wszystkich operacji rejestracji zakończonych powodzeniem (Tworzenie aktualizacji zapytań i usunięć). |None|
|registration.create|Operacje tworzenia rejestracji|Licznik|Łącznie|Liczba wszystkich operacji tworzenia rejestracji zakończonych powodzeniem.|None|
|registration.update|Operacje aktualizacji rejestracji|Licznik|Łącznie|Liczba wszystkich aktualizacji rejestracji zakończonych powodzeniem.|None|
|registration.get|Operacje odczytu rejestracji|Licznik|Łącznie|Liczba wszystkich zapytań rejestracji zakończonych powodzeniem.|None|
|registration.delete|Operacje usuwania rejestracji|Licznik|Łącznie|Liczba wszystkich operacji usunięcia rejestracji zakończonych powodzeniem.|None|
|przychodzące|Komunikaty przychodzące|Licznik|Łącznie|Liczba wszystkich pomyślnie wysłanych wywołań interfejsu API. |None|
|incoming.scheduled|Przesłane zaplanowane powiadomienia wypychane|Licznik|Łącznie|Anulowano zaplanowane powiadomienia wypychane|None|
|incoming.scheduled.cancel|Anulowano zaplanowane powiadomienia wypychane|Licznik|Łącznie|Anulowano zaplanowane powiadomienia wypychane|None|
|scheduled.pending|Oczekujące zaplanowane powiadomienia|Licznik|Łącznie|Oczekujące zaplanowane powiadomienia|None|
|Instalacja. wszystkie|Operacje zarządzania instalacją|Licznik|Łącznie|Operacje zarządzania instalacją|None|
|Instalacja. Get|Pobierz operacje instalacji|Licznik|Łącznie|Pobierz operacje instalacji|None|
|Instalacja. upsert|Tworzenie lub aktualizowanie operacji instalacji|Licznik|Łącznie|Tworzenie lub aktualizowanie operacji instalacji|None|
|installation.patch|Operacje instalacji poprawek|Licznik|Łącznie|Operacje instalacji poprawek|None|
|installation.delete|Usuwanie operacji instalacji|Licznik|Łącznie|Usuwanie operacji instalacji|None|
|outgoing.allpns.success|Powiadomienia zakończone powodzeniem|Licznik|Łącznie|Liczba wszystkich udanych powiadomień.|None|
|wychodzący. allpns. invalidpayload|Błędy ładunku|Licznik|Łącznie|Liczba wypchnięciów zakończonych niepowodzeniem, ponieważ PNS zwrócił zły błąd ładunku.|None|
|outgoing.allpns.pnserror|Błędy zewnętrznego systemu powiadomień|Licznik|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ wystąpił problem z komunikacją z PNS (wyklucza problemy z uwierzytelnianiem).|None|
|outgoing.allpns.channelerror|Błędy kanałów|Licznik|Łącznie|Liczba wypchnięciów zakończonych niepowodzeniem, ponieważ kanał był nieprawidłowy, nie jest skojarzony z poprawną aplikacją lub wygasłą.|None|
|outgoing.allpns.badorexpiredchannel|Błędy nieprawidłowych lub wygasłych kanałów|Licznik|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ kanał/token/Identyfikator rejestracji w rejestracji został wygasły lub nieprawidłowy.|None|
|outgoing.wns.success|WNS pomyślne powiadomienia|Licznik|Łącznie|Liczba wszystkich udanych powiadomień.|None|
|wychodzący. WNS. invalidcredentials|WNS błędy autoryzacji (Nieprawidłowe poświadczenia)|Licznik|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń lub poświadczenia są zablokowane. (Usługa Windows Live nie rozpoznaje poświadczeń).|None|
|outgoing.wns.badchannel|Błąd nieprawidłowego kanału WNS|Licznik|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ identyfikator channeluri w rejestracji nie zostały rozpoznane (WNS status: 404).|None|
|outgoing.wns.expiredchannel|Błąd WNS kanału wygasłego|Licznik|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem z powodu wygaśnięcia identyfikator channeluri (stan WNS: 410).|None|
|wychodzące. WNS. dławienia|WNS powiadomienia z ograniczeniami|Licznik|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ WNS ogranicza tę aplikację (stan WNS: 406 nie akceptowalny).|None|
|outgoing.wns.tokenproviderunreachable|Błędy autoryzacji WNS (nieosiągalne)|Licznik|Łącznie|Usługa Windows Live jest nieosiągalna.|None|
|outgoing.wns.invalidtoken|Błędy autoryzacji WNS (nieprawidłowy token)|Licznik|Łącznie|Token dostarczony do WNS jest nieprawidłowy (stan WNS: 401 Brak autoryzacji).|None|
|outgoing.wns.wrongtoken|WNS błędy autoryzacji (nieprawidłowy token)|Licznik|Łącznie|Token dostarczony do WNS jest prawidłowy, ale dla innej aplikacji (stan WNS: 403 zabroniony). Może się tak zdarzyć, jeśli identyfikator channeluri w rejestracji jest skojarzony z inną aplikacją. Sprawdź, czy aplikacja kliencka jest skojarzona z tą samą aplikacją, której poświadczenia znajdują się w centrum powiadomień.|None|
|outgoing.wns.invalidnotificationformat|Nieprawidłowy format powiadomienia WNS|Licznik|Łącznie|Format powiadomienia jest nieprawidłowy (stan WNS: 400). Należy pamiętać, że WNS nie odrzuca wszystkich nieprawidłowych ładunków.|None|
|outgoing.wns.invalidnotificationsize|Błąd nieprawidłowego rozmiaru powiadomienia WNS|Licznik|Łącznie|Ładunek powiadomienia jest zbyt duży (stan WNS: 413).|None|
|outgoing.wns.channelthrottled|Ograniczenie kanału WNS|Licznik|Łącznie|Powiadomienie zostało porzucone, ponieważ identyfikator channeluri w rejestracji jest ograniczany (nagłówek odpowiedzi WNS: X-WNS-NotificationStatus: channelThrottled).|None|
|outgoing.wns.channeldisconnected|Kanał WNS został odłączony|Licznik|Łącznie|Powiadomienie zostało porzucone, ponieważ identyfikator channeluri w rejestracji jest ograniczany (WNS w nagłówku odpowiedzi: X-WNS-DeviceConnectionStatus: disconnected).|None|
|outgoing.wns.dropped|WNS usunięte powiadomienia|Licznik|Łącznie|Powiadomienie zostało porzucone, ponieważ identyfikator channeluri w rejestracji jest ograniczany (X-WNS-NotificationStatus: porzucone, ale nie X-WNS-DeviceConnectionStatus: disconnected).|None|
|outgoing.wns.pnserror|Błędy WNS|Licznik|Łącznie|Powiadomienie nie zostało dostarczone z powodu błędów komunikacji z WNS.|None|
|outgoing.wns.authenticationerror|Błędy uwierzytelniania WNS|Licznik|Łącznie|Powiadomienie nie zostało dostarczone z powodu błędów komunikacji z nieprawidłowymi poświadczeniami usługi Windows Live lub nieprawidłowym tokenem.|None|
|outgoing.apns.success|Pomyślne powiadomienia usługi APNS|Licznik|Łącznie|Liczba wszystkich udanych powiadomień.|None|
|outgoing.apns.invalidcredentials|Błędy autoryzacji usługi APNS|Licznik|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń lub poświadczenia są zablokowane.|None|
|outgoing.apns.badchannel|Błąd nieprawidłowego kanału usługi APNS|Licznik|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ token jest nieprawidłowy (kod stanu usługi APNS: 8).|None|
|outgoing.apns.expiredchannel|Błąd wygasłego kanału usługi APNS|Licznik|Łącznie|Liczba tokenów unieważnionych przez kanał opinii usługi APNS.|None|
|outgoing.apns.invalidnotificationsize|Błąd nieprawidłowego rozmiaru powiadomienia usługi APNS|Licznik|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ ładunek jest zbyt duży (kod stanu usługi APNS: 7).|None|
|outgoing.apns.pnserror|Błędy usługi APNS|Licznik|Łącznie|Liczba wypchnięciów zakończonych niepowodzeniem z powodu błędów komunikacji z usługą APNS.|None|
|outgoing.gcm.success|GCM pomyślne powiadomienia|Licznik|Łącznie|Liczba wszystkich udanych powiadomień.|None|
|outgoing.gcm.invalidcredentials|GCM błędy autoryzacji (Nieprawidłowe poświadczenia)|Licznik|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń lub poświadczenia są zablokowane.|None|
|outgoing.gcm.badchannel|Błąd nieprawidłowego kanału GCM|Licznik|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ identyfikator rejestracji w rejestracji nie został rozpoznany (wynik GCM: nieprawidłowa rejestracja).|None|
|outgoing.gcm.expiredchannel|Błąd GCM kanału wygasłego|Licznik|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem z powodu wygaśnięcia Identyfikator rejestracji w rejestracji (wynik GCM: NotRegistered).|None|
|outgoing.gcm.throttled|GCM powiadomienia z ograniczeniami|Licznik|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ GCM ograniczenie tej aplikacji (kod stanu GCM: 501-599 lub wynik: niedostępne).|None|
|outgoing.gcm.invalidnotificationformat|Nieprawidłowy format powiadomienia GCM|Licznik|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ ładunek nie został poprawnie sformatowany (wynik GCM: InvalidDataKey lub InvalidTtl).|None|
|outgoing.gcm.invalidnotificationsize|Błąd nieprawidłowego rozmiaru powiadomienia GCM|Licznik|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ ładunek jest zbyt duży (wynik GCM: MessageTooBig).|None|
|outgoing.gcm.wrongchannel|Błąd nieprawidłowego kanału GCM|Licznik|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ identyfikator rejestracji w rejestracji nie jest skojarzony z bieżącą aplikacją (GCM Result: InvalidPackageName).|None|
|outgoing.gcm.pnserror|Błędy GCM|Licznik|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem z powodu błędów komunikacji z GCM.|None|
|outgoing.gcm.authenticationerror|Błędy uwierzytelniania GCM|Licznik|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń, a SenderId nie jest poprawnie skonfigurowana w aplikacji (wynik GCM: MismatchedSenderId).|None|
|outgoing.mpns.success|USŁUGI MPNS pomyślne powiadomienia|Licznik|Łącznie|Liczba wszystkich udanych powiadomień.|None|
|outgoing.mpns.invalidcredentials|USŁUGI MPNS nieprawidłowe poświadczenia|Licznik|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń lub poświadczenia są zablokowane.|None|
|outgoing.mpns.badchannel|Błąd nieprawidłowego kanału usługi MPNS|Licznik|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ identyfikator channeluri w rejestracji nie zostały rozpoznane (usługi MPNS status: 404).|None|
|wychodzące. usługi MPNS. dławienia|USŁUGI MPNS powiadomienia z ograniczeniami|Licznik|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ usługi MPNS ogranicza tę aplikację (WNS usługi MPNS: 406 nieakceptowalny).|None|
|outgoing.mpns.invalidnotificationformat|Nieprawidłowy format powiadomienia usługi MPNS|Licznik|Łącznie|Liczba wypchnięciów zakończonych niepowodzeniem, ponieważ ładunek powiadomienia był zbyt duży.|None|
|outgoing.mpns.channeldisconnected|Kanał usługi MPNS został odłączony|Licznik|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ identyfikator channeluri w rejestracji został rozłączony (stan usługi MPNS: 412 nie został znaleziony).|None|
|outgoing.mpns.dropped|USŁUGI MPNS usunięte powiadomienia|Licznik|Łącznie|Liczba wypchnięciów, które zostały porzucone przez usługi MPNS (nagłówek odpowiedzi usługi MPNS: X-NotificationStatus: QueueFull lub pomijane).|None|
|outgoing.mpns.pnserror|Błędy usługi MPNS|Licznik|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem z powodu błędów komunikacji z usługi MPNS.|None|
|outgoing.mpns.authenticationerror|Błędy uwierzytelniania usługi MPNS|Licznik|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń lub poświadczenia są zablokowane.|None|
|notificationhub.pushes|Wszystkie powiadomienia wychodzące|Licznik|Łącznie|Wszystkie powiadomienia wychodzące centrum powiadomień|None|
|incoming.all.requests|Wszystkie żądania przychodzące|Licznik|Łącznie|Łączna liczba żądań przychodzących dla centrum powiadomień|None|
|przychodzące. ALL. failedrequests|Wszystkie przychodzące żądania zakończone niepowodzeniem|Licznik|Łącznie|Łączna liczba nieudanych żądań przychodzących dla centrum powiadomień|None|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Average_% bezpłatne węzłów i|% Wolnego węzłów i|Licznik|Średnia|Average_% bezpłatne węzłów i|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Wolne miejsce w Average_%|Wartość procentowa wolnego miejsca|Licznik|Średnia|Wolne miejsce w Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ użyto węzłów i|% Użytych węzłów i|Licznik|Średnia|Average_ użyto węzłów i|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Używane miejsce Average_%|Procent wykorzystania miejsca|Licznik|Średnia|Używane miejsce Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bajty odczytu Average_Disk/s|Bajty odczytu dysku/s|Licznik|Średnia|Bajty odczytu Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odczyty Average_Disk/s|Odczyty dysku/s|Licznik|Średnia|Odczyty Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Transfery Average_Disk/s|Transfery dyskowe/s|Licznik|Średnia|Transfery Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bajty zapisu Average_Disk/s|Bajty zapisu dysku/s|Licznik|Średnia|Bajty zapisu Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Zapisy Average_Disk/s|Zapisy dysku/s|Licznik|Średnia|Zapisy Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabajtów|Wolne megabajty|Licznik|Średnia|Average_Free megabajtów|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bajty dysku Average_Logical/s|Bajty dysku logicznego/s|Licznik|Średnia|Bajty dysku Average_Logical/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Dostępna pamięć Average_%|Dostępna pamięć (%)|Licznik|Średnia|Dostępna pamięć Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Dostępny obszar wymiany Average_%|Dostępny obszar wymiany (%)|Licznik|Średnia|Dostępny obszar wymiany Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Użyta pamięć Average_%|Używana pamięć (%)|Licznik|Średnia|Użyta pamięć Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Używany obszar wymiany Average_%|Używany obszar wymiany (%)|Licznik|Średnia|Używany obszar wymiany Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pamięć Average_Available MB|Dostępna pamięć (MB)|Licznik|Średnia|Pamięć Average_Available MB|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Zamiana Average_Available MB|Dostępny obszar wymiany (MB)|Licznik|Średnia|Zamiana Average_Available MB|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odczyty Average_Page/s|Odczyty stron/s|Licznik|Średnia|Odczyty Average_Page/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Zapisy Average_Page/s|Zapisy stron/s|Licznik|Średnia|Zapisy Average_Page/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/s|Strony/s|Licznik|Średnia|Average_Pages/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Przestrzeń wymiany Average_Used MB|Używany obszar wymiany (MB)|Licznik|Średnia|Przestrzeń wymiany Average_Used MB|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pamięć Average_Used pamięci (MB)|Używana pamięć (MB)|Licznik|Średnia|Pamięć Average_Used pamięci (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Przesyłane Average_Total bajty|Całkowita liczba przesłanych bajtów|Licznik|Średnia|Przesyłane Average_Total bajty|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bajty odebrane Average_Total|Całkowita liczba odebranych bajtów|Licznik|Średnia|Bajty odebrane Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total bajty|Łączna liczba bajtów|Licznik|Średnia|Average_Total bajty|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total przesyłane pakiety|Całkowita liczba przesłanych pakietów|Licznik|Średnia|Average_Total przesyłane pakiety|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odebrane pakiety Average_Total|Całkowita liczba odebranych pakietów|Licznik|Średnia|Odebrane pakiety Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total błędy odbierania|Całkowita liczba błędów odbierania|Licznik|Średnia|Average_Total błędy odbierania|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Błędy wysyłania Average_Total|Całkowita liczba błędów transmisji|Licznik|Średnia|Błędy wysyłania Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total kolizje|Łączna liczba kolizji|Licznik|Średnia|Average_Total kolizje|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. wartość operacji dysku na sek./odczyt|Średni czas dysku w s/odczyt|Licznik|Średnia|Average_Avg. wartość operacji dysku na sek./odczyt|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. szybkość transferu z dysku w sekundach|Średni czas dysku w s/transfer|Licznik|Średnia|Average_Avg. szybkość transferu z dysku w sekundach|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. dysku na sek./zapis|Średni czas dysku w s/zapis|Licznik|Średnia|Average_Avg. dysku na sek./zapis|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bajty dysku Average_Physical/s|Bajty dysku fizycznego/s|Licznik|Średnia|Bajty dysku Average_Physical/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct uprzywilejowany czas|Czas uprzywilejowany PCT|Licznik|Średnia|Average_Pct uprzywilejowany czas|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas użytkownika Average_Pct|Czas użytkownika PCT|Licznik|Średnia|Czas użytkownika Average_Pct|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pamięć Average_Used w kilobajtach|Używana pamięć (w kilobajtach)|Licznik|Średnia|Pamięć Average_Used w kilobajtach|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual pamięci współdzielonej|Wirtualna pamięć udostępniona|Licznik|Średnia|Average_Virtual pamięci współdzielonej|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas DPC Average_%|Czas DPC (%)|Licznik|Średnia|Czas DPC Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas bezczynności Average_%|Czas bezczynności (%)|Licznik|Średnia|Czas bezczynności Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ czas przerwań (%)|Czas przerwań (%)|Licznik|Średnia|Average_ czas przerwań (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas oczekiwania operacji we/wy Average_%|Czas oczekiwania operacji we/wy (%)|Licznik|Średnia|Czas oczekiwania operacji we/wy Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% czasu całkiem|% Całkiem czasu|Licznik|Średnia|Average_% czasu całkiem|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% czasu uprzywilejowanego|Czas uprzywilejowany (%)|Licznik|Średnia|Average_% czasu uprzywilejowanego|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ czas procesora (%)|Czas procesora (%)|Licznik|Średnia|Average_ czas procesora (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas użytkownika Average_%|Czas użytkownika (%)|Licznik|Średnia|Czas użytkownika Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free pamięci fizycznej|Wolna pamięć fizyczna|Licznik|Średnia|Average_Free pamięci fizycznej|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free miejsce w plikach stronicowania|Wolne miejsce w plikach stronicowania|Licznik|Średnia|Average_Free miejsce w plikach stronicowania|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free pamięć wirtualną|Wolna pamięć wirtualna|Licznik|Średnia|Average_Free pamięć wirtualną|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Procesy|Licznik|Średnia|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size przechowywane w plikach stronicowania|Rozmiar zapisany w plikach stronicowania|Licznik|Średnia|Average_Size przechowywane w plikach stronicowania|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Czas działania|Licznik|Średnia|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Użytkownicy|Licznik|Średnia|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. wartość operacji dysku na sek./odczyt|Średni czas dysku w s/odczyt|Licznik|Średnia|Average_Avg. wartość operacji dysku na sek./odczyt|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. dysku na sek./zapis|Średni czas dysku w s/zapis|Licznik|Średnia|Average_Avg. dysku na sek./zapis|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current długość kolejki dysku|Bieżąca długość kolejki dysku|Licznik|Średnia|Average_Current długość kolejki dysku|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odczyty Average_Disk/s|Odczyty dysku/s|Licznik|Średnia|Odczyty Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Transfery Average_Disk/s|Transfery dyskowe/s|Licznik|Średnia|Transfery Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Zapisy Average_Disk/s|Zapisy dysku/s|Licznik|Średnia|Zapisy Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabajtów|Wolne megabajty|Licznik|Średnia|Average_Free megabajtów|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Wolne miejsce w Average_%|Wartość procentowa wolnego miejsca|Licznik|Średnia|Wolne miejsce w Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB|Dostępna pamięć (MB)|Licznik|Średnia|Average_Available MB|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% zadeklarowanych bajtów w użyciu|% Zadeklarowanych bajtów w użyciu|Licznik|Średnia|Average_% zadeklarowanych bajtów w użyciu|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odebrane Average_Bytes/s|Bajty odebrane/s|Licznik|Średnia|Odebrane Average_Bytes/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Wysłane Average_Bytes/s|Bajty wysłane/s|Licznik|Średnia|Wysłane Average_Bytes/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes całkowita/s|Całkowita liczba bajtów/s|Licznik|Średnia|Average_Bytes całkowita/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ czas procesora (%)|Czas procesora (%)|Licznik|Średnia|Average_ czas procesora (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Długość kolejki Average_Processor|Długość kolejki procesora|Licznik|Średnia|Długość kolejki Average_Processor|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Puls|Puls|Licznik|Łącznie|Puls|Komputer, OSType, wersja, SourceComputerId|
|Aktualizacja|Aktualizacja|Licznik|Średnia|Aktualizacja|Komputer, produkt, klasyfikacja, UpdateState, opcjonalne, zatwierdzone|
|Wydarzenie|Wydarzenie|Licznik|Średnia|Wydarzenie|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft. Komunikacja równorzędna/peeringServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PrefixLatency|Opóźnienie prefiksu|Milisekundy|Średnia|Średnie opóźnienie prefiksu|Prefiksname|

## <a name="microsoftpeeringpeerings"></a>Microsoft. Komunikacja równorzędna/Komunikacja równorzędna

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SessionAvailabilityV4|Dostępność sesji v4|Procent|Średnia|Dostępność sesji v4|ConnectionId|
|SessionAvailabilityV6|Dostępność sesji w wersji 6|Procent|Średnia|Dostępność sesji V6|ConnectionId|
|IngressTrafficRate|Szybkość ruchu przychodzącego|BitsPerSecond|Średnia|Szybkość ruchu przychodzącego w bitach na sekundę|ConnectionId|
|EgressTrafficRate|Szybkość ruchu wychodzącego|BitsPerSecond|Średnia|Szybkość ruchu wychodzącego w bitach na sekundę|ConnectionId|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QueryDuration|Czas trwania zapytania|Milisekundy|Średnia|Czas trwania zapytania języka DAX w ostatnim interwale|Nie wymiarów|
|QueryPoolJobQueueLength|Wątki: Długość kolejki zadań puli zapytań|Licznik|Średnia|Liczba zadań w kolejce puli wątków zapytań.|Nie wymiarów|
|qpu_high_utilization_metric|QPU wysokie wykorzystanie|Licznik|Łącznie|QPU wysokie użycie w ciągu ostatnich minut, 1 do dużego użycia QPU, w przeciwnym razie 0|Nie wymiarów|
|memory_metric|Memory (Pamięć)|Bajty|Średnia|Rozmiar. Zakres 0-3 GB dla a1, 0-5 GB dla a2, 0-10 GB dla a3, 0-25 GB dla A4, 0-50 GB dla A5 i 0-100 GB dla A6|Nie wymiarów|
|memory_thrashing_metric|Migotanie pamięci|Procent|Średnia|Średnia pamięć migotanie.|Nie wymiarów|



## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/przestrzenie nazw

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Licznik|Łącznie|Pomyślne ListenerConnections dla elementu Microsoft. Relay.|EntityName, klasy OperationResult|
|ListenerConnections — błąd clienterror|ListenerConnections — błąd clienterror|Licznik|Łącznie|Błąd clienterror on ListenerConnections dla elementu Microsoft. Relay.|EntityName, klasy OperationResult|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Licznik|Łącznie|Błąd servererror on ListenerConnections dla elementu Microsoft. Relay.|EntityName, klasy OperationResult|
|SenderConnections-Success|SenderConnections-Success|Licznik|Łącznie|Pomyślne SenderConnections dla elementu Microsoft. Relay.|EntityName, klasy OperationResult|
|SenderConnections-ClientError|SenderConnections-ClientError|Licznik|Łącznie|Błąd clienterror on SenderConnections dla elementu Microsoft. Relay.|EntityName, klasy OperationResult|
|SenderConnections-ServerError|SenderConnections-ServerError|Licznik|Łącznie|Błąd servererror on SenderConnections dla elementu Microsoft. Relay.|EntityName, klasy OperationResult|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Licznik|Łącznie|Łącznie ListenerConnections dla elementu Microsoft. Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Licznik|Łącznie|Łączna liczba żądań SenderConnections dla elementu Microsoft. Relay.|EntityName|
|Połączeń ActiveConnections|Połączeń ActiveConnections|Licznik|Łącznie|Łącznie połączeń ActiveConnections dla elementu Microsoft. Relay.|EntityName|
|ActiveListeners|ActiveListeners|Licznik|Łącznie|Łącznie ActiveListeners dla elementu Microsoft. Relay.|EntityName|
|BytesTransferred|BytesTransferred|Licznik|Łącznie|Łącznie BytesTransferred dla elementu Microsoft. Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Licznik|Łącznie|Łącznie ListenerDisconnects dla elementu Microsoft. Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Licznik|Łącznie|Łącznie SenderDisconnects dla elementu Microsoft. Relay.|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SearchLatency|Opóźnienie wyszukiwania|S|Średnia|Średnie opóźnienie wyszukiwania dla usługi wyszukiwania|None|
|SearchQueriesPerSecond|Zapytania wyszukiwania na sekundę|CountPerSecond|Średnia|Zapytania wyszukiwania na sekundę dla usługi wyszukiwania|None|
|ThrottledSearchQueriesPercentage|Procent zapytań wyszukiwania z ograniczeniami|Procent|Średnia|Procent zapytań wyszukiwania, które zostały ograniczone dla usługi wyszukiwania|None|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Żądania successfulrequests|Żądania zakończone powodzeniem|Licznik|Łącznie|Łączna liczba pomyślnych żądań dla przestrzeni nazw|EntityName, klasy OperationResult|
|Błędy servererrors|Błędy serwera.|Licznik|Łącznie|Błędy serwera dla elementu Microsoft. ServiceBus.|EntityName, klasy OperationResult|
|Błędy usererrors|Błędy użytkownika.|Licznik|Łącznie|Błędy użytkowników dla elementu Microsoft. ServiceBus.|EntityName, klasy OperationResult|
|ThrottledRequests|Żądania z ograniczeniami.|Licznik|Łącznie|Żądania ograniczone dla elementu Microsoft. ServiceBus.|EntityName, klasy OperationResult|
|Żądania incomingrequests|Żądania przychodzące|Licznik|Łącznie|Żądania przychodzące dla Microsoft. ServiceBus.|EntityName|
|Komunikaty incomingmessages|Komunikaty przychodzące|Licznik|Łącznie|Komunikaty przychodzące dla elementu Microsoft. ServiceBus.|EntityName|
|Komunikaty outgoingmessages|Komunikaty wychodzące|Licznik|Łącznie|Komunikaty wychodzące dla elementu Microsoft. ServiceBus.|EntityName|
|Połączeń ActiveConnections|Połączeń ActiveConnections|Licznik|Łącznie|Całkowita liczba aktywnych połączeń dla elementu Microsoft. ServiceBus.|None|
|Połączenia connectionsopened|Otwarte połączenia.|Licznik|Średnia|Połączenia otwarte dla elementu Microsoft. ServiceBus.|EntityName|
|Połączenia connectionsclosed|Połączenia zamknięte.|Licznik|Średnia|Połączenia zamknięte dla elementu Microsoft. ServiceBus.|EntityName|
|Rozmiar|Rozmiar|Bajty|Średnia|Rozmiar kolejki/tematu w bajtach.|EntityName|
|Komunikaty|Liczba komunikatów w kolejce/temacie.|Licznik|Średnia|Liczba komunikatów w kolejce/temacie.|EntityName|
|ActiveMessages|Liczba aktywnych komunikatów w kolejce/temacie.|Licznik|Średnia|Liczba aktywnych komunikatów w kolejce/temacie.|EntityName|
|DeadletteredMessages|Liczba utraconych wiadomości w kolejce/temacie.|Licznik|Średnia|Liczba utraconych wiadomości w kolejce/temacie.|EntityName|
|ScheduledMessages|Liczba zaplanowanych komunikatów w kolejce/temacie.|Licznik|Średnia|Liczba zaplanowanych komunikatów w kolejce/temacie.|EntityName|
|NamespaceCpuUsage|Procesor CPU|Procent|Maksimum|Metryka użycia procesora przestrzeni nazw w warstwie Premium usługi Service Bus.|Replika|
|NamespaceMemoryUsage|Użycie pamięci|Procent|Maksimum|Metryka użycia pamięci przestrzeni nazw Premium usługi Service Bus.|Replika|
|CPUXNS|PROCESOR (przestarzałe)|Procent|Maksimum|Metryka użycia procesora przestrzeni nazw w warstwie Premium usługi Service Bus. Ta Metryka to przestarzałe. Użyj zamiast tego metryki procesora (NamespaceCpuUsage).|Replika|
|WSXNS|Użycie pamięci (przestarzałe)|Procent|Maksimum|Metryka użycia pamięci przestrzeni nazw Premium usługi Service Bus. Ta Metryka jest przestarzała. Użyj zamiast tego metryki użycie pamięci (NamespaceMemoryUsage).|Replika|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Licznik|Średnia|Procesor CPU przydzielony do tego kontenera w rdzeniach Milli|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|AllocatedMemory|AllocatedMemory|Bajty|Średnia|Pamięć przypisana do tego kontenera w MB|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|ActualCpu|ActualCpu|Licznik|Średnia|Rzeczywiste użycie procesora CPU w rdzeniach Milli|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|ActualMemory|ActualMemory|Bajty|Średnia|Rzeczywiste użycie pamięci w MB|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|CpuUtilization|CpuUtilization|Procent|Średnia|Użycie procesora CPU dla tego kontenera jako wartości procentowej AllocatedCpu|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|MemoryUtilization|MemoryUtilization|Procent|Średnia|Użycie procesora CPU dla tego kontenera jako wartości procentowej AllocatedCpu|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|ApplicationStatus|ApplicationStatus|Licznik|Średnia|Stan aplikacji siatki Service Fabric|ApplicationName, status|
|Bajty|Bajty|Licznik|Średnia|Stan kondycji usługi w aplikacji Service Fabric siatki|ApplicationName, status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Licznik|Średnia|Stan kondycji repliki usługi w aplikacji Service Fabric siatki|ApplicationName, status, ServiceName, servicereplicaname|
|ContainerStatus|ContainerStatus|Licznik|Średnia|Stan kontenera w aplikacji Service Fabric siatki|ApplicationName, ServiceName, CodePackageName, servicereplicaname, status|
|RestartCount|RestartCount|Licznik|Średnia|Liczba ponownych uruchomień kontenera w aplikacji Service Fabric siatki|ApplicationName, status, ServiceName, servicereplicaname, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ConnectionCount|Liczba połączeń|Licznik|Maksimum|Ilość połączenia użytkownika.|Endpoint|
|MessageCount|Liczba komunikatów|Licznik|Łącznie|Całkowita ilość komunikatów.|None|
|InboundTraffic|Ruch przychodzący|Bajty|Łącznie|Ruch przychodzący usługi|None|
|OutboundTraffic|Ruch wychodzący|Bajty|Łącznie|Ruch wychodzący usługi|None|
|Błędy usererrors|Błędy użytkownika|Procent|Maksimum|Procent błędów użytkownika|None|
|SystemErrors|Błędy systemu|Procent|Maksimum|Procent błędów systemu|None|





## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|None|
|physical_data_read_percent|Procent użycia operacji we/wy na danych|Procent|Średnia|Procent użycia operacji we/wy na danych|None|
|log_write_percent|Procent operacji we/wy dziennika|Procent|Średnia|Procent operacji we/wy dziennika. Nie dotyczy hurtowni danych.|None|
|dtu_consumption_percent|Procent użycia jednostek DTU|Procent|Średnia|Procent jednostek DTU. Dotyczy baz danych opartych na jednostkach DTU.|None|
|Chowan|Używane miejsce na dane|Bajty|Maksimum|Używane miejsce na dane. Nie dotyczy hurtowni danych.|None|
|connection_successful|Udane połączenia|Licznik|Łącznie|Udane połączenia|None|
|connection_failed|Połączenia zakończone niepowodzeniem|Licznik|Łącznie|Połączenia zakończone niepowodzeniem|None|
|blocked_by_firewall|Zablokowane przez zaporę|Licznik|Łącznie|Zablokowane przez zaporę|None|
|zakleszczenie|Zakleszczenia|Licznik|Łącznie|Zakleszczenia. Nie dotyczy hurtowni danych.|None|
|storage_percent|Procent użytego miejsca na danych|Procent|Maksimum|Procent użytego miejsca na danych. Nie dotyczy magazynów danych ani baz danych w skali.|None|
|xtp_storage_percent|Procent magazynu OLTP w pamięci|Procent|Średnia|Procent magazynu OLTP w pamięci. Nie dotyczy hurtowni danych.|None|
|workers_percent|Procent pracowników|Procent|Średnia|Procent pracowników. Nie dotyczy hurtowni danych.|None|
|sessions_percent|Procent sesji|Procent|Średnia|Procent sesji. Nie dotyczy hurtowni danych.|None|
|dtu_limit|DTU Limit|Licznik|Średnia|Limit jednostek DTU. Dotyczy baz danych opartych na jednostkach DTU.|None|
|dtu_used|Używane jednostki DTU|Licznik|Średnia|Użyto jednostek DTU. Dotyczy baz danych opartych na jednostkach DTU.|None|
|cpu_limit|Limit CPU|Licznik|Średnia|Limit czasu procesora CPU. Dotyczy baz danych opartych na rdzeń wirtualny.|None|
|cpu_used|Użycie procesora CPU|Licznik|Średnia|Użycie procesora CPU. Dotyczy baz danych opartych na rdzeń wirtualny.|None|
|dwu_limit|Limit jednostek dwu|Licznik|Maksimum|Limit jednostek dwu. Dotyczy tylko magazynów danych.|None|
|dwu_consumption_percent|JEDNOSTEK dwu procent|Procent|Maksimum|Procent jednostek dwu. Dotyczy tylko magazynów danych.|None|
|dwu_used|JEDNOSTEK dwu używane|Licznik|Maksimum|JEDNOSTEK dwu. Dotyczy tylko magazynów danych.|None|
|cache_hit_percent|Procent trafień w pamięci podręcznej|Procent|Maksimum|Procent trafień w pamięci podręcznej. Dotyczy tylko magazynów danych.|None|
|cache_used_percent|Procent użycia pamięci podręcznej|Procent|Maksimum|Procent użycia pamięci podręcznej. Dotyczy tylko magazynów danych.|None|
|sqlserver_process_core_percent|Procent podstawowych procesów SQL Server|Procent|Maksimum|Procent użycia procesora CPU dla procesu SQL Server, mierzony przez system operacyjny. Obecnie dostępne tylko dla bezserwerowych baz danych.|None|
|sqlserver_process_memory_percent|Procent pamięci procesu SQL Server|Procent|Maksimum|Procent użycia pamięci dla procesu SQL Server, mierzony przez system operacyjny. Obecnie dostępne tylko dla bezserwerowych baz danych.|None|
|tempdb_data_size|Rozmiar pliku danych tempdb kilobajtów|Licznik|Maksimum|Rozmiar pliku danych tempdb kilobajtów. Nie dotyczy hurtowni danych.|None|
|tempdb_log_size|Rozmiar pliku dziennika bazy danych tempdb kilobajtów|Licznik|Maksimum|Rozmiar pliku dziennika bazy danych tempdb kilobajtów. Nie dotyczy hurtowni danych.|None|
|tempdb_log_used_percent|Użyto dziennika% tempdb|Procent|Maksimum|Użyto dziennika bazy danych tempdb. Nie dotyczy hurtowni danych.|None|
|local_tempdb_usage_percent|Procent lokalnej bazy danych tempdb|Procent|Średnia|Procent lokalnej bazy danych tempdb. Dotyczy tylko magazynów danych.|None|
|app_cpu_billed|Rozliczane użycie procesora przez aplikację|Licznik|Łącznie|Rozliczane użycie procesora przez aplikację. Dotyczy bezserwerowych baz danych.|None|
|app_cpu_percent|Procent użycia procesora CPU aplikacji|Procent|Średnia|Procent użycia procesora CPU aplikacji. Dotyczy bezserwerowych baz danych.|None|
|app_memory_percent|Procent pamięci aplikacji|Procent|Średnia|Procent pamięci aplikacji. Dotyczy bezserwerowych baz danych.|None|
|allocated_data_storage|Przydzielono miejsce na danych|Bajty|Średnia|Przydzielony magazyn danych. Nie dotyczy hurtowni danych.|None|
|memory_usage_percent|Procent pamięci|Procent|Maksimum|Procent pamięci. Dotyczy tylko magazynów danych.|None|
|full_backup_size_bytes|Rozmiar magazynu pełnej kopii zapasowej|Bajty|Maksimum|Łączny rozmiar magazynu pełnej kopii zapasowej. Dotyczy baz danych opartych na rdzeń wirtualny. Nie dotyczy baz danych w skali.|None|
|diff_backup_size_bytes|Różnicowy rozmiar magazynu kopii zapasowych|Bajty|Maksimum|Skumulowany, zróżnicowany rozmiar magazynu kopii zapasowych. Dotyczy baz danych opartych na rdzeń wirtualny. Nie dotyczy baz danych w skali.|None|
|log_backup_size_bytes|Rozmiar magazynu kopii zapasowej dziennika|Bajty|Maksimum|Rozmiar magazynu kopii zapasowej dziennika zbiorczego. Dotyczy baz danych opartych na rdzeń wirtualny. Nie dotyczy baz danych w skali.|None|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|None|
|database_cpu_percent|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|DatabaseResourceId|
|physical_data_read_percent|Procent użycia operacji we/wy na danych|Procent|Średnia|Procent użycia operacji we/wy na danych|None|
|database_physical_data_read_percent|Procent użycia operacji we/wy na danych|Procent|Średnia|Procent użycia operacji we/wy na danych|DatabaseResourceId|
|log_write_percent|Procent operacji we/wy dziennika|Procent|Średnia|Procent operacji we/wy dziennika|None|
|database_log_write_percent|Procent operacji we/wy dziennika|Procent|Średnia|Procent operacji we/wy dziennika|DatabaseResourceId|
|dtu_consumption_percent|Procent użycia jednostek DTU|Procent|Średnia|Procent jednostek DTU. Dotyczy pul elastycznych opartych na jednostkach DTU.|None|
|database_dtu_consumption_percent|Procent użycia jednostek DTU|Procent|Średnia|Procent użycia jednostek DTU|DatabaseResourceId|
|storage_percent|Procent użytego miejsca na danych|Procent|Średnia|Procent użytego miejsca na danych|None|
|workers_percent|Procent pracowników|Procent|Średnia|Procent pracowników|None|
|database_workers_percent|Procent pracowników|Procent|Średnia|Procent pracowników|DatabaseResourceId|
|sessions_percent|Procent sesji|Procent|Średnia|Procent sesji|None|
|database_sessions_percent|Procent sesji|Procent|Średnia|Procent sesji|DatabaseResourceId|
|eDTU_limit|limit liczby jednostek eDTU|Licznik|Średnia|limit liczby jednostek eDTU. Dotyczy pul elastycznych opartych na jednostkach DTU.|None|
|storage_limit|Maksymalny rozmiar danych|Bajty|Średnia|Maksymalny rozmiar danych|None|
|eDTU_used|użyta wartość eDTU|Licznik|Średnia|użyta wartość eDTU. Dotyczy pul elastycznych opartych na jednostkach DTU.|None|
|database_eDTU_used|użyta wartość eDTU|Licznik|Średnia|użyta wartość eDTU|DatabaseResourceId|
|storage_used|Używane miejsce na dane|Bajty|Średnia|Używane miejsce na dane|None|
|database_storage_used|Używane miejsce na dane|Bajty|Średnia|Używane miejsce na dane|DatabaseResourceId|
|xtp_storage_percent|Procent magazynu OLTP w pamięci|Procent|Średnia|Procent magazynu OLTP w pamięci|None|
|cpu_limit|Limit CPU|Licznik|Średnia|Limit czasu procesora CPU. Dotyczy pul elastycznych opartych na rdzeń wirtualny.|None|
|database_cpu_limit|Limit CPU|Licznik|Średnia|Limit CPU|DatabaseResourceId|
|cpu_used|Użycie procesora CPU|Licznik|Średnia|Użycie procesora CPU. Dotyczy pul elastycznych opartych na rdzeń wirtualny.|None|
|database_cpu_used|Użycie procesora CPU|Licznik|Średnia|Użycie procesora CPU|DatabaseResourceId|
|sqlserver_process_core_percent|Procent podstawowych procesów SQL Server|Procent|Maksimum|Użycie procesora jako procent procesu bazy danych SQL. Dotyczy pul elastycznych.|None|
|sqlserver_process_memory_percent|Procent pamięci procesu SQL Server|Procent|Maksimum|Użycie pamięci jako procent procesu bazy danych SQL. Dotyczy pul elastycznych.|None|
|tempdb_data_size|Rozmiar pliku danych tempdb kilobajtów|Licznik|Maksimum|Rozmiar pliku danych tempdb kilobajtów|None|
|tempdb_log_size|Rozmiar pliku dziennika bazy danych tempdb kilobajtów|Licznik|Maksimum|Rozmiar pliku dziennika bazy danych tempdb kilobajtów|None|
|tempdb_log_used_percent|Użyto dziennika% tempdb|Procent|Maksimum|Użyto dziennika% tempdb|None|
|allocated_data_storage|Przydzielono miejsce na danych|Bajty|Średnia|Przydzielono miejsce na danych|None|
|database_allocated_data_storage|Przydzielono miejsce na danych|Bajty|Średnia|Przydzielono miejsce na danych|DatabaseResourceId|
|allocated_data_storage_percent|Procent przydzielonych przestrzeni danych|Procent|Maksimum|Procent przydzielonych przestrzeni danych|None|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|dtu_consumption_percent|Procent użycia jednostek DTU|Procent|Średnia|Procent użycia jednostek DTU|ElasticPoolResourceId|
|database_dtu_consumption_percent|Procent użycia jednostek DTU|Procent|Średnia|Procent użycia jednostek DTU|DatabaseResourceId,ElasticPoolResourceId|
|storage_used|Używane miejsce na dane|Bajty|Średnia|Używane miejsce na dane|ElasticPoolResourceId|
|database_storage_used|Używane miejsce na dane|Bajty|Średnia|Używane miejsce na dane|DatabaseResourceId,ElasticPoolResourceId|
|dtu_used|Używane jednostki DTU|Licznik|Średnia|Używane jednostki DTU|DatabaseResourceId|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|virtual_core_count|Liczba rdzeni wirtualnych|Licznik|Średnia|Liczba rdzeni wirtualnych|None|
|avg_cpu_percent|Średni procent procesora CPU|Procent|Średnia|Średni procent procesora CPU|None|
|reserved_storage_mb|Zarezerwowane miejsce w magazynie|Licznik|Średnia|Zarezerwowane miejsce w magazynie|None|
|storage_space_used_mb|Używane miejsce do magazynowania|Licznik|Średnia|Używane miejsce do magazynowania|None|
|io_requests|Liczba żądań we/wy|Licznik|Średnia|Liczba żądań we/wy|None|
|io_bytes_read|Odczytane bajty we/wy|Bajty|Średnia|Odczytane bajty we/wy|None|
|io_bytes_written|Bajty we/wy zapisywane|Bajty|Średnia|Bajty we/wy zapisywane|None|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|UsedCapacity|Używana pojemność|Bajty|Średnia|Używana pojemność konta|None|
|Transakcje|Transakcje|Licznik|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|Milisekundy|Średnia|Średni czas oczekiwania, używane przez usługę Azure Storage do przetwarzania żądania zakończonego powodzeniem w milisekundach. Ta wartość nie obejmuje opóźnienia sieci uwzględnionego w wartości AverageE2ELatency.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie e2e dla powodzenia|Milisekundy|Średnia|Średnie opóźnienie end-to-end żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BlobCapacity|Pojemność obiektu blob|Bajty|Średnia|Ilość miejsca używanego przez usługę obiektów Blob konta magazynu, w bajtach.|Blobtype, warstwa|
|BlobCount|Liczba obiektów blob|Licznik|Średnia|Liczba obiektów Blob w usłudze obiektów Blob konta magazynu.|Blobtype, warstwa|
|ContainerCount|Liczba kontenerów obiektów blob|Licznik|Średnia|Liczba kontenerów w usłudze obiektów Blob konta magazynu.|None|
|IndexCapacity|Pojemność indeksu|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez ADLS Gen2 (hierarchiczny) indeks w bajtach.|None|
|Transakcje|Transakcje|Licznik|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|Milisekundy|Średnia|Średni czas oczekiwania, używane przez usługę Azure Storage do przetwarzania żądania zakończonego powodzeniem w milisekundach. Ta wartość nie obejmuje opóźnienia sieci uwzględnionego w wartości AverageE2ELatency.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie e2e dla powodzenia|Milisekundy|Średnia|Średnie opóźnienie end-to-end żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TableCapacity|Pojemność tabeli|Bajty|Średnia|Ilość miejsca używanego przez usługę tabeli konta magazynu, w bajtach.|None|
|TableCount|Liczba tabel|Licznik|Średnia|Liczba tabel w usłudze tabeli konta magazynu.|None|
|TableEntityCount|Liczba jednostek tabel|Licznik|Średnia|Liczba jednostek tabel w usłudze tabeli konta magazynu.|None|
|Transakcje|Transakcje|Licznik|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|Milisekundy|Średnia|Średni czas oczekiwania, używane przez usługę Azure Storage do przetwarzania żądania zakończonego powodzeniem w milisekundach. Ta wartość nie obejmuje opóźnienia sieci uwzględnionego w wartości AverageE2ELatency.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie e2e dla powodzenia|Milisekundy|Średnia|Średnie opóźnienie end-to-end żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|FileCapacity|Pojemność plików|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez usługę plików konta magazynu w bajtach.|Udziału|
|FileCount|Liczba plików|Licznik|Średnia|Liczba plików w usłudze plików konta magazynu.|Udziału|
|FileShareCount|Liczba udziałów plików|Licznik|Średnia|Liczba udziałów plików w usłudze plików konta magazynu.|None|
|FileShareSnapshotCount|Liczba migawek udziału plików|Licznik|Średnia|Liczba migawek znajdujących się w udziale w usłudze plików konta magazynu.|Udziału|
|FileShareSnapshotSize|Rozmiar migawki udziału plików|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez migawki w usłudze plików konta magazynu w bajtach.|Udziału|
|FileShareQuota|Rozmiar przydziału udziału plików|Bajty|Średnia|Górny limit ilości miejsca do magazynowania, który może być używany przez usługę Azure Files w bajtach.|Udziału|
|Transakcje|Transakcje|Licznik|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi.|Responsetype, geotype, ApiName, Authentication, przeudziale|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|Milisekundy|Średnia|Średni czas oczekiwania, używane przez usługę Azure Storage do przetwarzania żądania zakończonego powodzeniem w milisekundach. Ta wartość nie obejmuje opóźnienia sieci uwzględnionego w wartości AverageE2ELatency.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|SuccessE2ELatency|Opóźnienie e2e dla powodzenia|Milisekundy|Średnia|Średnie opóźnienie end-to-end żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotyp, ApiName, uwierzytelnianie, przeudziale|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QueueCapacity|Pojemność kolejki|Bajty|Średnia|Ilość miejsca używanego przez usługę kolejki konta magazynu, w bajtach.|None|
|QueueCount|Liczba kolejek|Licznik|Średnia|Liczba kolejek w usłudze kolejki konta magazynu.|None|
|QueueMessageCount|Liczba komunikatów w kolejce|Licznik|Średnia|Przybliżona liczba komunikatów w kolejce w usłudze kolejki konta magazynu.|None|
|Transakcje|Transakcje|Licznik|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|Milisekundy|Średnia|Średni czas oczekiwania, używane przez usługę Azure Storage do przetwarzania żądania zakończonego powodzeniem w milisekundach. Ta wartość nie obejmuje opóźnienia sieci uwzględnionego w wartości AverageE2ELatency.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie e2e dla powodzenia|Milisekundy|Średnia|Średnie opóźnienie end-to-end żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|





## <a name="microsoftstoragecachecaches"></a>Microsoft. StorageCache/pamięci podręczne

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ClientIOPS|Łączna liczba operacji we/wy klienta|Licznik|Średnia|Częstotliwość operacji plików klienta przetwarzanych przez pamięć podręczną.|None|
|ClientLatency|Średnie opóźnienie klienta|Milisekundy|Średnia|Średnie opóźnienie operacji plików klienta w pamięci podręcznej magazynu.|None|
|ClientReadIOPS|Liczba operacji we/wy odczytu klienta|CountPerSecond|Średnia|Operacje odczytu klienta na sekundę.|None|
|ClientReadThroughput|Średnia przepływność odczytu pamięci podręcznej|BytesPerSecond|Średnia|Szybkość transferu danych odczytu klienta.|None|
|ClientWriteIOPS|Liczba operacji we/wy zapisu klienta|CountPerSecond|Średnia|Operacje zapisu klienta na sekundę.|None|
|ClientWriteThroughput|Średnia przepływność zapisu w pamięci podręcznej|BytesPerSecond|Średnia|Szybkość transferu danych przez zapis klienta.|None|
|ClientMetadataReadIOPS|Liczba operacji we/wy odczytu metadanych klienta|CountPerSecond|Średnia|Częstotliwość operacji plików klienta wysyłanych do pamięci podręcznej, z wyłączeniem odczytów danych, które nie modyfikują trwałego stanu.|None|
|ClientMetadataWriteIOPS|Liczba IOPS zapisu metadanych klienta|CountPerSecond|Średnia|Szybkość operacji plików klienta wysyłanych do pamięci podręcznej, z wyjątkiem zapisów danych, które modyfikują stan trwały.|None|
|ClientLockIOPS|Liczba operacji we/wy blokady klienta|CountPerSecond|Średnia|Operacje blokowania plików klienta na sekundę.|None|
|StorageTargetHealth|Kondycja docelowa magazynu|Licznik|Średnia|Wartość logiczna wyników testu łączności między pamięcią podręczną i magazynem.|None|
|Czas działania|Czas działania|Licznik|Średnia|Wartość logiczna wyników testu łączności między pamięcią podręczną i systemem monitorowania.|None|
|StorageTargetIOPS|Łączna liczba operacji we/wy StorageTarget|Licznik|Średnia|Szybkość wszystkich operacji na plikach wysyłanych przez pamięć podręczną do określonego StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|StorageTarget zapisu IOPS|Licznik|Średnia|Częstotliwość operacji zapisu plików wysyłanych przez pamięć podręczną do określonego StorageTarget.|StorageTarget|
|StorageTargetAsyncWriteThroughput|Przepływność zapisu asynchronicznego StorageTarget|BytesPerSecond|Średnia|Częstotliwość asynchronicznego zapisywania danych w pamięci podręcznej w określonym StorageTarget. Są to zapisy oportunistyczne, które nie powodują blokowania klientów.|StorageTarget|
|StorageTargetSyncWriteThroughput|Przepływność zapisu synchronicznego StorageTarget|BytesPerSecond|Średnia|Częstotliwość synchronicznego zapisywania danych w pamięci podręcznej w określonym StorageTarget. Są to zapisy, które powodują blokowanie klientów.|StorageTarget|
|StorageTargetTotalWriteThroughput|StorageTarget całkowita przepływność zapisu|BytesPerSecond|Średnia|Łączna szybkość, z jaką pamięć podręczna zapisuje dane do określonego StorageTarget.|StorageTarget|
|StorageTargetLatency|Opóźnienie StorageTarget|Milisekundy|Średnia|Średnie opóźnienie błądzenia wszystkich operacji plików wysyłanych przez pamięć podręczną do partricular StorageTarget.|StorageTarget|
|StorageTargetMetadataReadIOPS|Liczba operacji we/wy odczytu metadanych StorageTarget|CountPerSecond|Średnia|Częstotliwość operacji na plikach, które nie modyfikują stanu trwałego i z wyłączeniem operacji odczytu, którą pamięć podręczna wysyła do określonego StorageTarget.|StorageTarget|
|StorageTargetMetadataWriteIOPS|Liczba IOPS zapisu w metadanych StorageTarget|CountPerSecond|Średnia|Częstotliwość wykonywania operacji na plikach, która modyfikuje trwały stan i wykluczając operację zapisu, którą pamięć podręczna wysyła do określonego StorageTarget.|StorageTarget|
|StorageTargetReadIOPS|StorageTarget operacji we/wy odczytu|CountPerSecond|Średnia|Częstotliwość operacji odczytu plików wysyłanych przez pamięć podręczną do określonego StorageTarget.|StorageTarget|
|StorageTargetReadAheadThroughput|StorageTarget z wyprzedzeniem|BytesPerSecond|Średnia|Częstotliwość odczytywania przez opportunisticly pamięci podręcznej danych z StorageTarget.|StorageTarget|
|StorageTargetFillThroughput|Przepływność wypełnienia StorageTarget|BytesPerSecond|Średnia|Częstotliwość odczytywania przez pamięć podręczną danych z StorageTarget w celu obsługi chybień w pamięci podręcznej.|StorageTarget|
|StorageTargetTotalReadThroughput|StorageTarget całkowita przepływność odczytu|BytesPerSecond|Średnia|Łączna szybkość, z jaką pamięć podręczna odczytuje dane z określonego StorageTarget.|StorageTarget|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Wynik synchronizacji sesji|Licznik|Średnia|Metryka, która rejestruje wartość 1 za każdym razem, gdy punkt końcowy serwera pomyślnie ukończy sesję synchronizacji z punktem końcowym w chmurze|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Synchronizowane pliki|Licznik|Łącznie|Liczba synchronizowanych plików|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Pliki, których nie należy synchronizować|Licznik|Łącznie|Nie można zsynchronizować liczby plików|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncBatchTransferredFileBytes|Zsynchronizowane bajty|Bajty|Łącznie|Łączny rozmiar pliku transferowanego dla sesji synchronizacji|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncServerHeartbeat|Stan online serwera|Licznik|Maksimum|Metryka, która rejestruje wartość 1 za każdym razem, gdy serwer resigtered pomyślnie rejestruje puls przy użyciu punktu końcowego w chmurze|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Odwołanie do warstw w chmurze|Bajty|Łącznie|Łączny rozmiar danych, które zostały wywołane przez serwer|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Rozmiar odwołania do warstw w chmurze|Bajty|Łącznie|Rozmiar danych, które zostały odwołane|SyncGroupName, ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Przepływność odwołań do warstw w chmurze|BytesPerSecond|Średnia|Rozmiar przepływności operacji odwoływania danych|SyncGroupName, ServerName|
|StorageSyncRecalledNetworkBytesByApplication|Rozmiar odwołania do warstw w chmurze według aplikacji|Bajty|Łącznie|Rozmiar danych, które zostały wywołane przez aplikację|SyncGroupName, ServerName, ApplicationName|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>Microsoft. storagesync/storageSyncServices/syncGroups

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|Synchronizowane pliki|Licznik|Łącznie|Liczba synchronizowanych plików|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Pliki, których nie należy synchronizować|Licznik|Łącznie|Nie można zsynchronizować liczby plików|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupBatchTransferredFileBytes|Zsynchronizowane bajty|Bajty|Łącznie|Łączny rozmiar pliku transferowanego dla sesji synchronizacji|SyncGroupName,ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|Synchronizowane pliki|Licznik|Łącznie|Liczba synchronizowanych plików|ServerEndpointName,SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Pliki, których nie należy synchronizować|Licznik|Łącznie|Nie można zsynchronizować liczby plików|ServerEndpointName,SyncDirection|
|ServerEndpointBatchTransferredFileBytes|Zsynchronizowane bajty|Bajty|Łącznie|Łączny rozmiar pliku transferowanego dla sesji synchronizacji|ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>Microsoft. storagesync/storageSyncServices/registeredServers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ServerHeartbeat|Stan online serwera|Licznik|Maksimum|Metryka, która rejestruje wartość 1 za każdym razem, gdy serwer resigtered pomyślnie rejestruje puls przy użyciu punktu końcowego w chmurze|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|Odwołanie do warstw w chmurze|Bajty|Łącznie|Łączny rozmiar danych, które zostały wywołane przez serwer|ServerResourceId, ServerName|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ResourceUtilization|% wykorzystania SU|Procent|Maksimum|% wykorzystania SU|Logicznaname, PartitionId|
|InputEvents|Zdarzenia wejściowe|Licznik|Łącznie|Zdarzenia wejściowe|Logicznaname, PartitionId|
|InputEventBytes|Zdarzenia wejściowe (bajty)|Bajty|Łącznie|Zdarzenia wejściowe (bajty)|Logicznaname, PartitionId|
|LateInputEvents|Opóźnione zdarzenia wejściowe|Licznik|Łącznie|Opóźnione zdarzenia wejściowe|Logicznaname, PartitionId|
|OutputEvents|Zdarzenia wyjściowe|Licznik|Łącznie|Zdarzenia wyjściowe|Logicznaname, PartitionId|
|ConversionErrors|Błędy konwersji danych|Licznik|Łącznie|Błędy konwersji danych|Logicznaname, PartitionId|
|Błędy|Błędy w czasie wykonywania|Licznik|Łącznie|Błędy w czasie wykonywania|Logicznaname, PartitionId|
|DroppedOrAdjustedEvents|Zdarzenia poza kolejnością|Licznik|Łącznie|Zdarzenia poza kolejnością|Logicznaname, PartitionId|
|AMLCalloutRequests|Żądania funkcji|Licznik|Łącznie|Żądania funkcji|Logicznaname, PartitionId|
|AMLCalloutFailedRequests|Żądania funkcji zakończone niepowodzeniem|Licznik|Łącznie|Żądania funkcji zakończone niepowodzeniem|Logicznaname, PartitionId|
|AMLCalloutInputEvents|Zdarzenia funkcji|Licznik|Łącznie|Zdarzenia funkcji|Logicznaname, PartitionId|
|DeserializationError|Błędy deserializacji danych wejściowych|Licznik|Łącznie|Błędy deserializacji danych wejściowych|Logicznaname, PartitionId|
|EarlyInputEvents|Wczesne zdarzenia wejściowe|Licznik|Łącznie|Wczesne zdarzenia wejściowe|Logicznaname, PartitionId|
|OutputWatermarkDelaySeconds|Opóźnienie znaku wodnego|S|Maksimum|Opóźnienie znaku wodnego|Logicznaname, PartitionId|
|InputEventsSourcesBacklogged|Zaległe zdarzenia wejściowe|Licznik|Maksimum|Zaległe zdarzenia wejściowe|Logicznaname, PartitionId|
|InputEventsSourcesPerSecond|Odebrane źródła wejściowe|Licznik|Łącznie|Odebrane źródła wejściowe|Logicznaname, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/środowiska

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|IngressReceivedMessages|Odebrane komunikaty transferu danych przychodzących|Licznik|Łącznie|Liczba komunikatów odczytanych ze wszystkich źródeł zdarzeń centrum zdarzeń lub Centrum IoT Hub|None|
|IngressReceivedInvalidMessages|Odebrano nieprawidłowe komunikaty dotyczące transferu danych przychodzących|Licznik|Łącznie|Liczba nieprawidłowych komunikatów odczytywanych ze wszystkich centrów zdarzeń lub źródeł zdarzeń usługi IoT Hub|None|
|IngressReceivedBytes|Bajty odebrane z ruchu przychodzącego|Bajty|Łącznie|Liczba bajtów odczytanych ze wszystkich źródeł zdarzeń|None|
|IngressStoredBytes|Bajty przechowywane w ruchu przychodzącym|Bajty|Łącznie|Łączny rozmiar zdarzeń pomyślnie przetworzonych i dostępnych dla zapytania|None|
|IngressStoredEvents|Zdarzenia związane z transferem danych przychodzących|Licznik|Łącznie|Liczba zdarzeń spłaszczonych pomyślnie przetworzonych i dostępnych dla zapytania|None|
|IngressReceivedMessagesTimeLag|Zwłoka czasu odbierania komunikatów przychodzących|S|Maksimum|Różnica między czasem, w którym wiadomość jest przetworzona do kolejki w źródle zdarzeń i czasie, w którym jest przetwarzany w danych wejściowych|None|
|IngressReceivedMessagesCountLag|Opóźnienie liczby odebranych komunikatów przychodzących|Licznik|Średnia|Różnica między numerem sekwencyjnym ostatnio zarejestrowanego komunikatu w partycji źródłowej zdarzenia a sekwencją sekwencji komunikatów przetwarzanych w ramach ruchu przychodzącego|None|
|WarmStorageMaxProperties|Maksymalna liczba właściwości magazynu ciepłego|Licznik|Maksimum|Maksymalna liczba właściwości używanych przez środowisko dla jednostki SKU S1/S2 i Maksymalna liczba właściwości dozwolona przez magazyn ciepły dla jednostki SKU|None|
|WarmStorageUsedProperties|Właściwości używanej pamięci masowej |Licznik|Maksimum|Liczba właściwości używanych przez środowisko dla jednostki SKU S1/S2 i liczby właściwości używanych przez magazyn ciepły dla jednostek SKU|None|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/Environments/eventsources

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|IngressReceivedMessages|Odebrane komunikaty transferu danych przychodzących|Licznik|Łącznie|Liczba komunikatów odczytanych ze źródła zdarzeń|None|
|IngressReceivedInvalidMessages|Odebrano nieprawidłowe komunikaty dotyczące transferu danych przychodzących|Licznik|Łącznie|Liczba nieprawidłowych komunikatów odczytanych ze źródła zdarzeń|None|
|IngressReceivedBytes|Bajty odebrane z ruchu przychodzącego|Bajty|Łącznie|Liczba bajtów odczytanych ze źródła zdarzenia|None|
|IngressStoredBytes|Bajty przechowywane w ruchu przychodzącym|Bajty|Łącznie|Łączny rozmiar zdarzeń pomyślnie przetworzonych i dostępnych dla zapytania|None|
|IngressStoredEvents|Zdarzenia związane z transferem danych przychodzących|Licznik|Łącznie|Liczba zdarzeń spłaszczonych pomyślnie przetworzonych i dostępnych dla zapytania|None|
|IngressReceivedMessagesTimeLag|Zwłoka czasu odbierania komunikatów przychodzących|S|Maksimum|Różnica między czasem, w którym wiadomość jest przetworzona do kolejki w źródle zdarzeń i czasie, w którym jest przetwarzany w danych wejściowych|None|
|IngressReceivedMessagesCountLag|Opóźnienie liczby odebranych komunikatów przychodzących|Licznik|Średnia|Różnica między numerem sekwencyjnym ostatnio zarejestrowanego komunikatu w partycji źródłowej zdarzenia a sekwencją sekwencji komunikatów przetwarzanych w ramach ruchu przychodzącego|None|
|WarmStorageMaxProperties|Maksymalna liczba właściwości magazynu ciepłego|Licznik|Maksimum|Maksymalna liczba właściwości używanych przez środowisko dla jednostki SKU S1/S2 i Maksymalna liczba właściwości dozwolona przez magazyn ciepły dla jednostki SKU|None|
|WarmStorageUsedProperties|Właściwości używanej pamięci masowej |Licznik|Maksimum|Liczba właściwości używanych przez środowisko dla jednostki SKU S1/S2 i liczby właściwości używanych przez magazyn ciepły dla jednostek SKU|None|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. VMwareCloudSimple/virtualMachines

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Bajty odczytu dysku/s|BytesPerSecond|Średnia|Średnia przepływność dysku spowodowana operacjami odczytu w okresie próbkowania.|None|
|DiskWriteBytesPerSecond|Bajty zapisu dysku/s|BytesPerSecond|Średnia|Średnia przepływność dysku spowodowana operacją zapisu w okresie próbnym.|None|
|Bajty odczytu dysku|Bajty odczytu dysku|Bajty|Łącznie|Łączna przepływność dysku spowodowana operacjami odczytu w okresie próbkowania.|None|
|Bajty zapisu dysku|Bajty zapisu dysku|Bajty|Łącznie|Łączna przepływność dysku spowodowana operacjami zapisu w okresie próbkowania.|None|
|DiskReadOperations|Operacje odczytu z dysku|Licznik|Łącznie|Liczba operacji odczytu we/wy w poprzednim okresie próbkowania. Należy pamiętać, że operacje te mogą mieć rozmiar zmienny.|None|
|DiskWriteOperations|Operacje zapisu na dysku|Licznik|Łącznie|Liczba operacji zapisu we/wy w poprzednim okresie próbkowania. Należy pamiętać, że operacje te mogą mieć rozmiar zmienny.|None|
|Operacje odczytu z dysku/s|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Średnia liczba operacji odczytu we/wy w poprzednim okresie próbkowania. Należy pamiętać, że operacje te mogą mieć rozmiar zmienny.|None|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Średnia liczba operacji we/wy zapisu w poprzednim okresie próbkowania. Należy pamiętać, że operacje te mogą mieć rozmiar zmienny.|None|
|DiskReadLatency|Opóźnienie odczytu dysku|Milisekundy|Średnia|Całkowite opóźnienie odczytu. Suma opóźnień odczytu urządzenia i jądra.|None|
|DiskWriteLatency|Opóźnienie zapisu na dysku|Milisekundy|Średnia|Całkowite opóźnienie zapisu. Suma opóźnień między urządzeniem i zapisem jądra.|None|
|NetworkInBytesPerSecond|Sieć w bajtach/s|BytesPerSecond|Średnia|Średnia przepływność sieci dla odebranego ruchu.|None|
|NetworkOutBytesPerSecond|Bajty wychodzące z sieci/s|BytesPerSecond|Średnia|Średnia przepływność sieci dla przesyłanego ruchu.|None|
|Sieć — wejście|Sieć — wejście|Bajty|Łącznie|Całkowita przepływność sieci dla odebranego ruchu.|None|
|Sieć — wyjście|Sieć — wyjście|Bajty|Łącznie|Całkowita przepustowość sieci przesyłanych danych.|None|
|MemoryUsed|Używana pamięć|Bajty|Średnia|Ilość pamięci maszyny używanej przez maszynę wirtualną.|None|
|MemoryGranted|Przydzielone pamięci|Bajty|Średnia|Ilość pamięci przydzielonej maszynie wirtualnej przez hosta. Pamięć nie jest przydzielana do hosta, dopóki nie zostanie ona nałożona jeden raz, a przyznana pamięć może zostać zamieniony lub zaznaczono w dymku, jeśli VMkernel wymaga pamięci.|None|
|MemoryActive|Pamięć aktywna|Bajty|Średnia|Ilość pamięci używanej przez maszynę wirtualną w minionym małym oknie czasu. Jest to wartość "prawda" ilości pamięci wymaganej przez maszynę wirtualną. Dodatkowa niewykorzystana pamięć może zostać zastąpiona lub zaprowadzona bez wpływu na wydajność gościa.|None|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Średnia|Użycie procesora CPU. Ta wartość jest raportowana przy użyciu 100% reprezentujących wszystkie rdzenie procesora w systemie. Przykładowo 2-kierunkowa maszyna wirtualna używająca 50% systemu z czterema rdzeniem korzysta całkowicie z dwóch rdzeni.|None|
|PercentageCpuReady|Procent gotowych do użycia procesora|Milisekundy|Łącznie|Czas gotowości to czas oczekiwania, aż procesor CPU stanie się dostępny w okresie ostatniej aktualizacji.|None|










## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuPercentage|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Wystąpienie|
|MemoryPercentage|Procent pamięci|Procent|Średnia|Procent pamięci|Wystąpienie|
|DiskQueueLength|Długość kolejki dysku|Licznik|Średnia|Długość kolejki dysku|Wystąpienie|
|HttpQueueLength|Długość kolejki http|Licznik|Średnia|Długość kolejki http|Wystąpienie|
|BytesReceived|Dane w|Bajty|Łącznie|Dane w|Wystąpienie|
|BytesSent|Dane wychodzące|Bajty|Łącznie|Dane wychodzące|Wystąpienie|
|TcpSynSent|Wysłano pakiet TCP syn|Licznik|Średnia|Wysłano pakiet TCP syn|Wystąpienie|
|TcpSynReceived|Odebrano pakiet TCP syn|Licznik|Średnia|Odebrano pakiet TCP syn|Wystąpienie|
|TcpEstablished|Nawiązano ruch TCP|Licznik|Średnia|Nawiązano ruch TCP|Wystąpienie|
|TcpFinWait1|Oczekiwanie na TCP FIN 1|Licznik|Średnia|Oczekiwanie na TCP FIN 1|Wystąpienie|
|TcpFinWait2|Oczekiwanie na TCP fin 2|Licznik|Średnia|Oczekiwanie na TCP fin 2|Wystąpienie|
|TcpClosing|Zamykanie protokołu TCP|Licznik|Średnia|Zamykanie protokołu TCP|Wystąpienie|
|TcpCloseWait|Oczekiwanie na zamknięcie protokołu TCP|Licznik|Średnia|Oczekiwanie na zamknięcie protokołu TCP|Wystąpienie|
|TcpLastAck|Ostatnie potwierdzenie TCP|Licznik|Średnia|Ostatnie potwierdzenie TCP|Wystąpienie|
|TcpTimeWait|Czas oczekiwania TCP|Licznik|Średnia|Czas oczekiwania TCP|Wystąpienie|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/Sites (z wyjątkiem funkcji)

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuTime|Czas procesora CPU|S|Łącznie|Czas procesora CPU|Wystąpienie|
|Żądania|Żądania|Licznik|Łącznie|Żądania|Wystąpienie|
|BytesReceived|Dane w|Bajty|Łącznie|Dane w|Wystąpienie|
|BytesSent|Dane wychodzące|Bajty|Łącznie|Dane wychodzące|Wystąpienie|
|Http101|Http 101|Licznik|Łącznie|Http 101|Wystąpienie|
|Http2xx|Http 2xx|Licznik|Łącznie|Http 2xx|Wystąpienie|
|Http3xx|Http 3xx|Licznik|Łącznie|Http 3xx|Wystąpienie|
|Http401|HTTP 401|Licznik|Łącznie|HTTP 401|Wystąpienie|
|Http403|HTTP 403|Licznik|Łącznie|HTTP 403|Wystąpienie|
|Http404|HTTP 404|Licznik|Łącznie|HTTP 404|Wystąpienie|
|Http406|Http 406|Licznik|Łącznie|Http 406|Wystąpienie|
|Http4xx|Http 4xx|Licznik|Łącznie|Http 4xx|Wystąpienie|
|Http5xx|Błędy serwera http|Licznik|Łącznie|Błędy serwera http|Wystąpienie|
|MemoryWorkingSet|Zestaw roboczy pamięci|Bajty|Średnia|Zestaw roboczy pamięci|Wystąpienie|
|AverageMemoryWorkingSet|Średni zestaw roboczy pamięci|Bajty|Średnia|Średni zestaw roboczy pamięci|Wystąpienie|
|AverageResponseTime|Średni czas odpowiedzi|S|Średnia|Średni czas odpowiedzi|Wystąpienie|
|AppConnections|Połączenia|Licznik|Średnia|Połączenia|Wystąpienie|
|Realizuj|Liczba dojść|Licznik|Średnia|Liczba dojść|Wystąpienie|
|Wątki|Liczba wątków|Licznik|Średnia|Liczba wątków|Wystąpienie|
|PrivateBytes|Prywatne bajty|Bajty|Średnia|Prywatne bajty|Wystąpienie|
|IoReadBytesPerSecond|Bajty odczytu we/wy na sekundę|BytesPerSecond|Łącznie|Bajty odczytu we/wy na sekundę|Wystąpienie|
|IoWriteBytesPerSecond|Bajty zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Bajty zapisu we/wy na sekundę|Wystąpienie|
|IoOtherBytesPerSecond|Inne bajty we/wy na sekundę|BytesPerSecond|Łącznie|Inne bajty we/wy na sekundę|Wystąpienie|
|IoReadOperationsPerSecond|Operacje odczytu we/wy na sekundę|BytesPerSecond|Łącznie|Operacje odczytu we/wy na sekundę|Wystąpienie|
|IoWriteOperationsPerSecond|Operacje zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Operacje zapisu we/wy na sekundę|Wystąpienie|
|IoOtherOperationsPerSecond|Inne operacje we/wy na sekundę|BytesPerSecond|Łącznie|Inne operacje we/wy na sekundę|Wystąpienie|
|RequestsInApplicationQueue|Żądania w kolejce aplikacji|Licznik|Średnia|Żądania w kolejce aplikacji|Wystąpienie|
|CurrentAssemblies|Bieżące zestawy|Licznik|Średnia|Bieżące zestawy|Wystąpienie|
|TotalAppDomains|Łączna liczba domen aplikacji|Licznik|Średnia|Łączna liczba domen aplikacji|Wystąpienie|
|TotalAppDomainsUnloaded|Całkowita liczba zwolnionych domen aplikacji|Licznik|Średnia|Całkowita liczba zwolnionych domen aplikacji|Wystąpienie|
|Gen0Collections|Zbieranie elementów bezużytecznych generacji 0|Licznik|Łącznie|Zbieranie elementów bezużytecznych generacji 0|Wystąpienie|
|Gen1Collections|Wyrzucanie elementów bezużytecznych generacji 1|Licznik|Łącznie|Wyrzucanie elementów bezużytecznych generacji 1|Wystąpienie|
|Gen2Collections|Zbieranie elementów bezużytecznych generacji 2|Licznik|Łącznie|Zbieranie elementów bezużytecznych generacji 2|Wystąpienie|
|HealthCheckStatus|Stan sprawdzania kondycji|Licznik|Średnia|Stan sprawdzania kondycji|Wystąpienie|
|FileSystemUsage|Użycie systemu plików|Bajty|Średnia|Użycie systemu plików|None|

## <a name="microsoftwebsites-functions"></a>Microsoft. Web/Sites (funkcje)

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BytesReceived|Dane w|Bajty|Łącznie|Dane w|Wystąpienie|
|BytesSent|Dane wychodzące|Bajty|Łącznie|Dane wychodzące|Wystąpienie|
|Http5xx|Błędy serwera http|Licznik|Łącznie|Błędy serwera http|Wystąpienie|
|MemoryWorkingSet|Zestaw roboczy pamięci|Bajty|Średnia|Zestaw roboczy pamięci|Wystąpienie|
|AverageMemoryWorkingSet|Średni zestaw roboczy pamięci|Bajty|Średnia|Średni zestaw roboczy pamięci|Wystąpienie|
|FunctionExecutionUnits|Jednostki wykonywania funkcji|MB/milisekundy|Łącznie|[Jednostki wykonywania funkcji](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Wystąpienie|
|FunctionExecutionCount|Liczba wykonań funkcji|Licznik|Łącznie|Liczba wykonań funkcji|Wystąpienie|
|PrivateBytes|Prywatne bajty|Bajty|Średnia|Prywatne bajty|Wystąpienie|
|IoReadBytesPerSecond|Bajty odczytu we/wy na sekundę|BytesPerSecond|Łącznie|Bajty odczytu we/wy na sekundę|Wystąpienie|
|IoWriteBytesPerSecond|Bajty zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Bajty zapisu we/wy na sekundę|Wystąpienie|
|IoOtherBytesPerSecond|Inne bajty we/wy na sekundę|BytesPerSecond|Łącznie|Inne bajty we/wy na sekundę|Wystąpienie|
|IoReadOperationsPerSecond|Operacje odczytu we/wy na sekundę|BytesPerSecond|Łącznie|Operacje odczytu we/wy na sekundę|Wystąpienie|
|IoWriteOperationsPerSecond|Operacje zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Operacje zapisu we/wy na sekundę|Wystąpienie|
|IoOtherOperationsPerSecond|Inne operacje we/wy na sekundę|BytesPerSecond|Łącznie|Inne operacje we/wy na sekundę|Wystąpienie|
|RequestsInApplicationQueue|Żądania w kolejce aplikacji|Licznik|Średnia|Żądania w kolejce aplikacji|Wystąpienie|
|CurrentAssemblies|Bieżące zestawy|Licznik|Średnia|Bieżące zestawy|Wystąpienie|
|TotalAppDomains|Łączna liczba domen aplikacji|Licznik|Średnia|Łączna liczba domen aplikacji|Wystąpienie|
|TotalAppDomainsUnloaded|Całkowita liczba zwolnionych domen aplikacji|Licznik|Średnia|Całkowita liczba zwolnionych domen aplikacji|Wystąpienie|
|Gen0Collections|Zbieranie elementów bezużytecznych generacji 0|Licznik|Łącznie|Zbieranie elementów bezużytecznych generacji 0|Wystąpienie|
|Gen1Collections|Wyrzucanie elementów bezużytecznych generacji 1|Licznik|Łącznie|Wyrzucanie elementów bezużytecznych generacji 1|Wystąpienie|
|Gen2Collections|Zbieranie elementów bezużytecznych generacji 2|Licznik|Łącznie|Zbieranie elementów bezużytecznych generacji 2|Wystąpienie|
|HealthCheckStatus|Stan sprawdzania kondycji|Licznik|Średnia|Stan sprawdzania kondycji|Wystąpienie|
|FileSystemUsage|Użycie systemu plików|Bajty|Średnia|Użycie systemu plików|None|


## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuTime|Czas procesora CPU|S|Łącznie|Czas procesora CPU|Wystąpienie|
|Żądania|Żądania|Licznik|Łącznie|Żądania|Wystąpienie|
|BytesReceived|Dane w|Bajty|Łącznie|Dane w|Wystąpienie|
|BytesSent|Dane wychodzące|Bajty|Łącznie|Dane wychodzące|Wystąpienie|
|Http101|Http 101|Licznik|Łącznie|Http 101|Wystąpienie|
|Http2xx|Http 2xx|Licznik|Łącznie|Http 2xx|Wystąpienie|
|Http3xx|Http 3xx|Licznik|Łącznie|Http 3xx|Wystąpienie|
|Http401|HTTP 401|Licznik|Łącznie|HTTP 401|Wystąpienie|
|Http403|HTTP 403|Licznik|Łącznie|HTTP 403|Wystąpienie|
|Http404|HTTP 404|Licznik|Łącznie|HTTP 404|Wystąpienie|
|Http406|Http 406|Licznik|Łącznie|Http 406|Wystąpienie|
|Http4xx|Http 4xx|Licznik|Łącznie|Http 4xx|Wystąpienie|
|Http5xx|Błędy serwera http|Licznik|Łącznie|Błędy serwera http|Wystąpienie|
|MemoryWorkingSet|Zestaw roboczy pamięci|Bajty|Średnia|Zestaw roboczy pamięci|Wystąpienie|
|AverageMemoryWorkingSet|Średni zestaw roboczy pamięci|Bajty|Średnia|Średni zestaw roboczy pamięci|Wystąpienie|
|AverageResponseTime|Średni czas odpowiedzi|S|Średnia|Średni czas odpowiedzi|Wystąpienie|
|HttpResponseTime|Czas odpowiedzi|S|Średnia|Czas odpowiedzi|Wystąpienie|
|FunctionExecutionUnits|Jednostki wykonywania funkcji|Licznik|Łącznie|Jednostki wykonywania funkcji|Wystąpienie|
|FunctionExecutionCount|Liczba wykonań funkcji|Licznik|Łącznie|Liczba wykonań funkcji|Wystąpienie|
|AppConnections|Połączenia|Licznik|Średnia|Połączenia|Wystąpienie|
|Realizuj|Liczba dojść|Licznik|Średnia|Liczba dojść|Wystąpienie|
|Wątki|Liczba wątków|Licznik|Średnia|Liczba wątków|Wystąpienie|
|PrivateBytes|Prywatne bajty|Bajty|Średnia|Prywatne bajty|Wystąpienie|
|IoReadBytesPerSecond|Bajty odczytu we/wy na sekundę|BytesPerSecond|Łącznie|Bajty odczytu we/wy na sekundę|Wystąpienie|
|IoWriteBytesPerSecond|Bajty zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Bajty zapisu we/wy na sekundę|Wystąpienie|
|IoOtherBytesPerSecond|Inne bajty we/wy na sekundę|BytesPerSecond|Łącznie|Inne bajty we/wy na sekundę|Wystąpienie|
|IoReadOperationsPerSecond|Operacje odczytu we/wy na sekundę|BytesPerSecond|Łącznie|Operacje odczytu we/wy na sekundę|Wystąpienie|
|IoWriteOperationsPerSecond|Operacje zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Operacje zapisu we/wy na sekundę|Wystąpienie|
|IoOtherOperationsPerSecond|Inne operacje we/wy na sekundę|BytesPerSecond|Łącznie|Inne operacje we/wy na sekundę|Wystąpienie|
|RequestsInApplicationQueue|Żądania w kolejce aplikacji|Licznik|Średnia|Żądania w kolejce aplikacji|Wystąpienie|
|CurrentAssemblies|Bieżące zestawy|Licznik|Średnia|Bieżące zestawy|Wystąpienie|
|TotalAppDomains|Łączna liczba domen aplikacji|Licznik|Średnia|Łączna liczba domen aplikacji|Wystąpienie|
|TotalAppDomainsUnloaded|Całkowita liczba zwolnionych domen aplikacji|Licznik|Średnia|Całkowita liczba zwolnionych domen aplikacji|Wystąpienie|
|Gen0Collections|Zbieranie elementów bezużytecznych generacji 0|Licznik|Łącznie|Zbieranie elementów bezużytecznych generacji 0|Wystąpienie|
|Gen1Collections|Wyrzucanie elementów bezużytecznych generacji 1|Licznik|Łącznie|Wyrzucanie elementów bezużytecznych generacji 1|Wystąpienie|
|Gen2Collections|Zbieranie elementów bezużytecznych generacji 2|Licznik|Łącznie|Zbieranie elementów bezużytecznych generacji 2|Wystąpienie|
|HealthCheckStatus|Stan sprawdzania kondycji|Licznik|Średnia|Stan sprawdzania kondycji|Wystąpienie|
|FileSystemUsage|Użycie systemu plików|Bajty|Średnia|Użycie systemu plików|None|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Żądania|Żądania|Licznik|Łącznie|Żądania|Wystąpienie|
|BytesReceived|Dane w|Bajty|Łącznie|Dane w|Wystąpienie|
|BytesSent|Dane wychodzące|Bajty|Łącznie|Dane wychodzące|Wystąpienie|
|Http101|Http 101|Licznik|Łącznie|Http 101|Wystąpienie|
|Http2xx|Http 2xx|Licznik|Łącznie|Http 2xx|Wystąpienie|
|Http3xx|Http 3xx|Licznik|Łącznie|Http 3xx|Wystąpienie|
|Http401|HTTP 401|Licznik|Łącznie|HTTP 401|Wystąpienie|
|Http403|HTTP 403|Licznik|Łącznie|HTTP 403|Wystąpienie|
|Http404|HTTP 404|Licznik|Łącznie|HTTP 404|Wystąpienie|
|Http406|Http 406|Licznik|Łącznie|Http 406|Wystąpienie|
|Http4xx|Http 4xx|Licznik|Łącznie|Http 4xx|Wystąpienie|
|Http5xx|Błędy serwera http|Licznik|Łącznie|Błędy serwera http|Wystąpienie|
|AverageResponseTime|Średni czas odpowiedzi|S|Średnia|Średni czas odpowiedzi|Wystąpienie|
|CpuPercentage|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Wystąpienie|
|MemoryPercentage|Procent pamięci|Procent|Średnia|Procent pamięci|Wystąpienie|
|DiskQueueLength|Długość kolejki dysku|Licznik|Średnia|Długość kolejki dysku|Wystąpienie|
|HttpQueueLength|Długość kolejki http|Licznik|Średnia|Długość kolejki http|Wystąpienie|
|ActiveRequests|Aktywne żądania|Licznik|Łącznie|Aktywne żądania|Wystąpienie|
|TotalFrontEnds|Łączne frontony|Licznik|Średnia|Łączne frontony|None|
|SmallAppServicePlanInstances|Niewielka App Service planowanie procesów roboczych|Licznik|Średnia|Niewielka App Service planowanie procesów roboczych|None|
|MediumAppServicePlanInstances|Średni App Service pracowników planu|Licznik|Średnia|Średni App Service pracowników planu|None|
|LargeAppServicePlanInstances|Duże App Service planowanie procesów roboczych|Licznik|Średnia|Duże App Service planowanie procesów roboczych|None|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Łączna|Łączna liczba procesów roboczych|Licznik|Średnia|Łączna liczba procesów roboczych|None|
|WorkersAvailable|Dostępni pracownicy|Licznik|Średnia|Dostępni pracownicy|None|
|WorkersUsed|Używani pracownicy|Licznik|Średnia|Używani pracownicy|None|
|CpuPercentage|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Wystąpienie|
|MemoryPercentage|Procent pamięci|Procent|Średnia|Procent pamięci|Wystąpienie|
## <a name="next-steps"></a>Następne kroki
* [Przeczytaj informacje o metrykach w Azure Monitor](data-platform.md)
* [Tworzenie alertów dotyczących metryk](alerts-overview.md)
* [Eksportowanie metryk do magazynu, centrum zdarzeń lub Log Analytics](platform-logs-overview.md)
