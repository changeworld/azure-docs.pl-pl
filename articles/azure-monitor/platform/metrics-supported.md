---
title: Obsługiwane dane usługi Azure Monitor według typu zasobu
description: Lista metryk dostępnych dla każdego typu zasobu za pomocą usługi Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 03/17/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 79bae9712cea04425cc36414ec56fdddd4345eab
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586024"
---
# <a name="supported-metrics-with-azure-monitor"></a>Obsługiwane metryki za pomocą usługi Azure Monitor

> [!NOTE]
> Ta lista jest w dużej mierze generowane automatycznie z interfejsu API REST metryki usługi Azure Monitor. Wszelkie modyfikacje tej listy za pośrednictwem Github mogą być zapisywane bez ostrzeżenia. Skontaktuj się z autorem tego artykułu, aby uzyskać szczegółowe informacje na temat wprowadzania stałych aktualizacji.

Usługa Azure Monitor udostępnia kilka sposobów interakcji z metrykami, w tym wykresów w portalu, uzyskiwanie do nich dostępu za pośrednictwem interfejsu API REST lub wykonywanie zapytań o nie przy użyciu programu PowerShell lub interfejsu wiersza polecenia. Poniżej znajduje się pełna lista wszystkich metryk obecnie dostępnych w potoku metryki usługi Azure Monitor. Inne metryki mogą być dostępne w portalu lub przy użyciu starszych interfejsów API. Ta lista poniżej zawiera tylko metryki dostępne przy użyciu skonsolidowanego potoku metryk usługi Azure Monitor. Metryki są zorganizowane według obszaru nazw. Aby uzyskać listę usług i obszarów nazw, które do nich należą, zobacz [Dostawcy zasobów dla usług platformy Azure](../../azure-resource-manager/management/azure-services-resource-providers.md). Aby uzyskać zapytania i uzyskać dostęp do tych metryk programowo, użyj [wersji api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Wysyłanie metryk wielowymiarowych za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
>
> *Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak w przypadku eksportowania za pomocą ustawień diagnostycznych metryka ta jest przedstawiana jako wszystkie komunikaty przychodzące we wszystkich kolejkach w centrum zdarzeń.
>
> Aby uzyskać listę metryk platformy, które można eksportować za pomocą ustawień diagnostycznych, zobacz [ten artykuł](metrics-supported-export-diagnostic-settings.md).


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisSługa/serwery

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|qpu_metric|Funkcja QPU|Liczba|Średnia|QPU. Zakres 0-100 dla S1, 0-200 dla S2 i 0-400 dla S4|Typ serwera|
|memory_metric|Memory (Pamięć)|Bajty|Średnia|Memory (pamięć). Zakres 0-25 GB dla S1, 0-50 GB dla S2 i 0-100 GB dla S4|Typ serwera|
|private_bytes_metric|Bajty prywatne|Bajty|Średnia|Bajty prywatne.|Typ serwera|
|virtual_bytes_metric|Bajty wirtualne|Bajty|Średnia|Bajty wirtualne.|Typ serwera|
|Wnioski o ponowne połączenie ogółem|Łączna liczba żądań połączeń|Liczba|Średnia|Łączna liczba żądań połączeń. Są to przyjazdy.|Typ serwera|
|SuccessfullConnectionsPerSec|Pomyślne połączenia na sekundę|Połów połówek|Średnia|Szybkość pomyślnego ukończenia połączenia.|Typ serwera|
|TotalConnectionFailures (Całkowita liczba połączeń)|Całkowita liczba niepowodzeń połączeń|Liczba|Średnia|Całkowita liczba nieudanych prób połączenia.|Typ serwera|
|CurrentUserSessions|Bieżące sesje użytkowników|Liczba|Średnia|Bieżąca liczba sesji użytkowników ustalona.|Typ serwera|
|QueryPoolBusyThreads|Wątki zajętych puli zapytań|Liczba|Średnia|Liczba zajęty wątków w puli wątków kwerendy.|Typ serwera|
|CommandPoolJobQueueLength (PoleceniePoolJobQueueLength)|Długość kolejki zadania puli poleceń|Liczba|Średnia|Liczba zadań w kolejce puli wątków poleceń.|Typ serwera|
|PrzetwarzaniePoolJobQueueLength|Długość kolejki zadania puli przetwarzania|Liczba|Średnia|Liczba zadań innych niż we/wy w kolejce puli wątków przetwarzania.|Typ serwera|
|CurrentConnections (Połączenia prądowe)|Połączenie: Bieżące połączenia|Liczba|Średnia|Bieżąca liczba nawiązanych połączeń klientów.|Typ serwera|
|CleanerCurrentPrice|Pamięć: Czystsza aktualna cena|Liczba|Średnia|Aktualna cena pamięci, $/byte/time, znormalizowana do 1000.|Typ serwera|
|CleanerMemoryShrinkable|Pamięć: Czystsza pamięć z kurczliwa|Bajty|Średnia|Ilość pamięci w bajtach, podlega przeczyszczaniu przez środek czyszczący tło.|Typ serwera|
|CleanerMemoryNiejmkliwi|Pamięć: Czystsza pamięć nierozskawalna|Bajty|Średnia|Ilość pamięci w bajtach, nie podlega przeczyszczaniu przez środek czyszczący tło.|Typ serwera|
|MemoryUsage (Narzędzie pamięci)|Pamięć: Użycie pamięci|Bajty|Średnia|Użycie pamięci przez proces serwera, używane do obliczania ceny czystszej pamięci. Równe licznikowi Process\PrivateBytes plus rozmiar danych mapowanych w pamięci, ignorując dowolną pamięć, która została mapowana lub przydzielona przez aparat analizy w pamięci xVelocity (VertiPaq) w nadmiarze limitu pamięci silnika xVelocity.|Typ serwera|
|MemoryLimitHard (Funkcja MemoryLimitHard)|Pamięć: Czas ogranicznik pamięci|Bajty|Średnia|Limit pamięci twardej z pliku konfiguracyjnego.|Typ serwera|
|MemoryLimitHigh|Pamięć: Limit pamięci wysoki|Bajty|Średnia|Wysoki limit pamięci z pliku konfiguracyjnego.|Typ serwera|
|MemoryLimitLow (Funkcja MemoryLimitLow)|Pamięć: Niski limit pamięci|Bajty|Średnia|Niski limit pamięci z pliku konfiguracyjnego.|Typ serwera|
|MemoryLimitVertiPaq|Pamięć: VertiPaq limitu pamięci|Bajty|Średnia|Limit w pamięci, z pliku konfiguracyjnego.|Typ serwera|
|Limit przydziału|Pamięć: Przydział|Bajty|Średnia|Bieżący przydział pamięci w bajtach. Przydział pamięci jest również znany jako przyznanie pamięci lub rezerwacja pamięci.|Typ serwera|
|Blokada przydziału|Pamięć: Przydział zablokowany|Liczba|Średnia|Bieżąca liczba żądań przydziału, które są blokowane do momentu, gdy inne przydziały pamięci zostaną zwolnione.|Typ serwera|
|VertiPaqNonpaged ( VertiPaqNonpaged )|Pamięć: VertiPaq Nonpaged|Bajty|Średnia|Bajty pamięci zablokowane w zestawie roboczym do użytku przez aparat w pamięci.|Typ serwera|
|VertiPaqPaged ( VertiPaqPaged )|Pamięć: VertiPaq Paged|Bajty|Średnia|Bajty pamięci stronicowanych w użyciu dla danych w pamięci.|Typ serwera|
|WierszeReadPerSec|Przetwarzanie: wiersze odczytywane na sekundę|Połów połówek|Średnia|Szybkość wierszy odczytywanych ze wszystkich relacyjnych baz danych.|Typ serwera|
|WierszeConvertedPerSec|Przetwarzanie: wiersze konwertowane na sekundę|Połów połówek|Średnia|Szybkość wierszy przekonwertowanych podczas przetwarzania.|Typ serwera|
|WierszeWrittenPerSec|Przetwarzanie: wiersze zapisane na sekundę|Połów połówek|Średnia|Szybkość wierszy zapisanych podczas przetwarzania.|Typ serwera|
|CommandPoolBusyThreads (PoleceniaPoolBusyThreads)|Wątki: Wątki zajęty puli poleceń|Liczba|Średnia|Liczba zajęty wątków w puli wątków polecenia.|Typ serwera|
|CommandPoolIdleThreads (PoleceniaPoolIdleThreads)|Wątki: Wątki bezczynności puli poleceń|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków poleceń.|Typ serwera|
|LongParsingBusyThreads|Wątki: Długie analizowanie zajętych wątków|Liczba|Średnia|Liczba wątków zajęty w puli wątków analizowania długich.|Typ serwera|
|LongParsingIdleTyty|Wątki: Długie parsowanie bezczynnych wątków|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków analizowania długich.|Typ serwera|
|LongParsingJobQueueLength|Wątki: Długa długość kolejki zadań analizowania|Liczba|Średnia|Liczba zadań w kolejce puli wątków analizowania długich.|Typ serwera|
|ProcessingPoolBusyIOJobTyty|Wątki: Przetwarzanie puli zajętych wątków zadań we/wy|Liczba|Średnia|Liczba wątków z uruchomionymi zadaniami we/wy w puli wątków przetwarzania.|Typ serwera|
|ProcessingPoolBusyNonIOTyty|Wątki: Przetwarzanie puli zajętych wątków innych niż We/Wy|Liczba|Średnia|Liczba wątków z uruchomionymi zadaniami nieujętymi/wy w puli wątków przetwarzania.|Typ serwera|
|ProcessingPoolIOJobQueueLength|Wątki: Długość kolejki we/wy puli przetwarzania|Liczba|Średnia|Liczba zadań we/wy w kolejce puli wątków przetwarzania.|Typ serwera|
|ProcessingPoolIdleIOJobThreads|Wątki: Przetwarzanie wątków zadań bezczynności puli bezczynności we/wy|Liczba|Średnia|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|Typ serwera|
|ProcessingPoolIdleNonIOTyty|Wątki: Przetwarzanie bezczynności wątków bezczynności puli|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków przetwarzania przeznaczonych do zadań innych niż we/wy.|Typ serwera|
|QueryPoolIdleThreads|Wątki: wątki bezczynności puli zapytań|Liczba|Średnia|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|Typ serwera|
|QueryPoolJobQueueLength|Wątki: Lengt kolejki zadań puli zapytań|Liczba|Średnia|Liczba zadań w kolejce puli wątków kwerend.|Typ serwera|
|ShortParsingBusyThreads|Wątki: Krótkie analizowanie zajętych wątków|Liczba|Średnia|Liczba wątków zajęty w puli wątków krótkiej analizy.|Typ serwera|
|ShortParsingIdleThreads|Wątki: Krótkie parsowanie bezczynnych wątków|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków krótkich analizowania.|Typ serwera|
|ShortParsingJobQueueLength|Wątki: Krótka długość kolejki zadań analizowania|Liczba|Średnia|Liczba zadań w kolejce puli wątków krótkiej analizy.|Typ serwera|
|memory_thrashing_metric|Przeładowywanie pamięci|Wartość procentowa|Średnia|Średnia pamięć thrashing.|Typ serwera|
|mashup_engine_qpu_metric|Silnik M QPU|Liczba|Średnia|Użycie QPU przez procesy silnika mashup|Typ serwera|
|mashup_engine_memory_metric|Pamięć silnika M|Bajty|Średnia|Użycie pamięci przez procesy silnika mashup|Typ serwera|
|mashup_engine_private_bytes_metric|Prywatne bajty silnika M|Bajty|Średnia|Użycie bajtów prywatnych przez procesy aparatu mashup.|Typ serwera|
|mashup_engine_virtual_bytes_metric|Wirtualne bajty silnika M|Bajty|Średnia|Użycie bajtów wirtualnych przez procesy aparatu mashup.|Typ serwera|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalRequests|Całkowita liczba żądań bramy (przestarzała)|Liczba|Łącznie|Liczba żądań bramy — zamiast tego użyj metryki żądania wielowymiarowego z wymiarem GatewayResponseCodeCategory|Lokalizacja,Nazwa hosta|
|Udane prośby|Pomyślne żądania bramy (przestarzałe)|Liczba|Łącznie|Liczba pomyślnych żądań bramy — zamiast tego użyj metryki żądania wielowymiarowego z wymiarem GatewayResponseCodeCategory|Lokalizacja,Nazwa hosta|
|Nieautoryzowanezapytania|Nieautoryzowane żądania bramy (przestarzałe)|Liczba|Łącznie|Liczba nieautoryzowanych żądań bramy — zamiast tego użyj metryki żądania wielowymiarowego z wymiarem GatewayResponseCodeCategory|Lokalizacja,Nazwa hosta|
|Nieudane zapisy|Żądania bramy nie powiodły się (przestarzałe)|Liczba|Łącznie|Liczba błędów w żądaniach bramy — zamiast tego użyj metryki żądania wielowymiarowego z wymiarem GatewayResponseCodeCategory|Lokalizacja,Nazwa hosta|
|InneRequests|Inne żądania bramy (przestarzałe)|Liczba|Łącznie|Liczba innych żądań bramy — zamiast tego użyj metryki żądania wielowymiarowego z wymiarem GatewayResponseCodeCategory|Lokalizacja,Nazwa hosta|
|Czas trwania|Całkowity czas trwania żądań bramy|Milisekund|Średnia|Całkowity czas trwania żądań bramy w milisekundach|Lokalizacja,Nazwa hosta|
|Cofanie wsteczneDuration|Czas trwania żądań wewnętrznej bazy danych|Milisekund|Średnia|Czas trwania żądań wewnętrznej bazy danych w milisekundach|Lokalizacja,Nazwa hosta|
|Pojemność|Pojemność|Wartość procentowa|Średnia|Metryka wykorzystania usługi ApiManagement|Lokalizacja|
|EventHubTotalEvents|Łączna liczba zdarzeń EventHub|Liczba|Łącznie|Liczba zdarzeń wysłanych do eventhub|Lokalizacja|
|EventHubSuccessfulEvents|Udane wydarzenia EventHub|Liczba|Łącznie|Liczba udanych zdarzeń EventHub|Lokalizacja|
|EventHubTotalFailedAjnieje|Nieudane zdarzenia EventHub|Liczba|Łącznie|Liczba nieudanych zdarzeń eventhub|Lokalizacja|
|EventHubRejectedWydarzenia|Odrzucone zdarzenia EventHub|Liczba|Łącznie|Liczba odrzuconych zdarzeń EventHub (nieprawidłowa konfiguracja lub nieautoryzowane)|Lokalizacja|
|EventHubThrottledWydarzenia|Ograniczone zdarzenia EventHub|Liczba|Łącznie|Liczba zdarzeń usługi EventHub z ograniczeniem|Lokalizacja|
|EventHubTimedoutEvents|Zdarzenia EventHub z przesunąłem czas|Liczba|Łącznie|Liczba zdarzeń eventhub o przeliczona o przesuń|Lokalizacja|
|EventHubDroppedWydarzenia|Porzucone zdarzenia EventHub|Liczba|Łącznie|Liczba zdarzeń pominiętych z powodu osiągniętego limitu rozmiaru kolejki|Lokalizacja|
|Usługa EventHubTotalBytesSent|Rozmiar zdarzeń EventHub|Bajty|Łącznie|Całkowity rozmiar zdarzeń EventHub w bajtach|Lokalizacja|
|Żądania|Żądania|Liczba|Łącznie|Metryki żądań bramy z wieloma wymiarami|Lokalizacja,Nazwa hosta,LastErrorReason,BackendResponseCode,GatewayResponseCode,BackendResponseCodeCategory,GatewayResponseCodeCategory|
|Łączność sieciowa|Stan łączności sieciowej zasobów (wersja zapoznawcza)|Liczba|Łącznie|Stan łączności sieciowej zależnych typów zasobów z usługi API Management|Lokalizacja,Typ zasobu|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Konto HttpIncomingRequestCount|Konto HttpIncomingRequestCount|Liczba|Liczba|Całkowita liczba przychodzących żądań http.|Statuscode|
|HttpIncomingRequestDuration (HttpIncomingRequestDuration)|HttpIncomingRequestDuration (HttpIncomingRequestDuration)|Liczba|Średnia|Opóźnienie żądania http.|Statuscode|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Wiosna

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SystemCpuUsagePercentage|Procent użycia procesora CPU systemu|Wartość procentowa|Średnia|Ostatnie użycie procesora dla całego systemu|AppName,Pod|
|AppCpuUsagePercentage|Procent użycia procesora CPU aplikacji|Wartość procentowa|Średnia|Procent użycia procesora JVM aplikacji|AppName,Pod|
|AppMemoryZatwierdzone|Przypisana pamięć aplikacji|Bajty|Średnia|Pamięć przypisana do jvm w bajtach|AppName,Pod|
|AppMemoryUżywane|Używana pamięć aplikacji|Bajty|Średnia|Pamięć aplikacji używana w bajtach|AppName,Pod|
|AppMemoryMax (AppMemoryMax)|Maksymalna ilość pamięci aplikacji|Bajty|Maksimum|Maksymalna ilość pamięci w bajtach, która może być używana do zarządzania pamięcią|AppName,Pod|
|MaxOldGenMemoryBajki|Maksymalny dostępny rozmiar danych starej generacji|Bajty|Średnia|Maksymalny rozmiar puli pamięci starej generacji|AppName,Pod|
|OldGenMemoryBajki|Rozmiar danych starej generacji|Bajty|Średnia|Rozmiar puli pamięci starej generacji po pełnym GC|AppName,Pod|
|OldGenPromotedBytes ( OldGenPromotedBytes )|Promowanie rozmiaru danych starej generacji|Bajty|Maksimum|Liczba dodatnich wzrostów wielkości puli pamięci starej generacji przed GC do po GC|AppName,Pod|
|YoungGenPromotedBytes|Promuj rozmiar danych młodego pokolenia|Bajty|Maksimum|Zwiększa się w celu zwiększenia wielkości puli pamięci młodego pokolenia po jednym GC do przed następnym|AppName,Pod|
|GCPauseTotalCount|Liczba wstrzymania GC|Liczba|Łącznie|Liczba wstrzymania GC|AppName,Pod|
|GCPauseTotalTime|Całkowity czas pauzy GC|Milisekund|Łącznie|Całkowity czas pauzy GC|AppName,Pod|
|TomcatsentBytes (TomcatSentBytes)|Tomcat całkowita wysłana bajty|Bajty|Łącznie|Tomcat całkowita wysłana bajty|AppName,Pod|
|TomcatReceivedBytes|Tomcat Całkowita liczba odebranych bajtów|Bajty|Łącznie|Tomcat Całkowita liczba odebranych bajtów|AppName,Pod|
|TomcatRequestTotalCzas|Tomcat Żądanie Całkowity czas|Milisekund|Łącznie|Tomcat Żądanie Całkowity czas|AppName,Pod|
|TomcatRequestTotalCount|Całkowita liczba żądań Tomcat|Liczba|Łącznie|Całkowita liczba żądań Tomcat|AppName,Pod|
|TomcatResponseAvgCzas|Tomcat żąda średni czas|Milisekund|Średnia|Tomcat żąda średni czas|AppName,Pod|
|TomcatRequestMaxTime|Tomcat Wniosek Maksymalny czas|Milisekund|Maksimum|Tomcat Wniosek Maksymalny czas|AppName,Pod|
|TomcatErrorCount|Błąd globalny Tomcat|Liczba|Łącznie|Błąd globalny Tomcat|AppName,Pod|
|TomcatSessionActiveMaxCount|Tomcat Session Max Aktywna liczba|Liczba|Łącznie|Tomcat Session Max Aktywna liczba|AppName,Pod|
|TomcatSessionAliveMaxTime|Tomcat Sesja Max Alive Czas|Milisekund|Maksimum|Tomcat Sesja Max Alive Czas|AppName,Pod|
|TomcatSessionActiveCurrentCount|Tomcat Sesja Żywa Liczba|Liczba|Łącznie|Tomcat Sesja Żywa Liczba|AppName,Pod|
|TomcatSessionCreatedCount|Liczba utworów sesji Tomcat|Liczba|Łącznie|Liczba utworów sesji Tomcat|AppName,Pod|
|TomcatSessionExpiredCount|Liczba wygasłych sesji Tomcat|Liczba|Łącznie|Liczba wygasłych sesji Tomcat|AppName,Pod|
|TomcatSessionRejectedCount|Liczba odrzuconych sesji Tomcat|Liczba|Łącznie|Liczba odrzuconych sesji Tomcat|AppName,Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationKontacje

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalJob (TotalJob)|Łączna liczba miejsc pracy|Liczba|Łącznie|Całkowita liczba miejsc pracy|Runbook,Stan|
|TotalUpdateDeploymentRuns (Liczbarunów rozmieszczenia totalupdate|Łączna liczba przebiegów wdrażania aktualizacji|Liczba|Łącznie|Całkowita liczba uruchomień wdrażania aktualizacji oprogramowania|Nazwa,Stan usługi SoftwareUpdateConfiguration|
|TotalUpdateDeploymentMachineRuns|Łączna liczba przebiegów wdrażania aktualizacji maszyny|Liczba|Łącznie|Całkowita aktualizacja oprogramowania komputer wdrożeniowy działa w przebiegu wdrażania aktualizacji oprogramowania|SoftwareUpdateConfigurationName,Status,TargetComputer,SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchKonkludy

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Liczba rdzeni|Dedykowana liczba rdzeni|Liczba|Łącznie|Łączna liczba dedykowanych rdzeni na koncie wsadowym|Brak|
|TotalNodeCount (Liczba Całkowita)|Liczba dedykowanych węzłów|Liczba|Łącznie|Łączna liczba dedykowanych węzłów na koncie wsadowym|Brak|
|Licznik NiskipriorityCoreCount|Liczba rdzeni o niskiej pozycjności|Liczba|Łącznie|Łączna liczba rdzeni o niskim priorytecie na koncie wsadowym|Brak|
|TotalLowPriorityNodeCount|Liczba węzłów o niskim priorytecie|Liczba|Łącznie|Łączna liczba węzłów o niskim priorytecie na koncie wsadowym|Brak|
|Tworzenie KontaNodeCount|Tworzenie liczby węzłów|Liczba|Łącznie|Liczba tworzonych węzłów|Brak|
|StartingNodeCount|Liczba początkowa węzłów|Liczba|Łącznie|Liczba węzłów od rozpoczęcia|Brak|
|WaitingForStartTaskNodeCount|Oczekiwanie na uruchomienie liczby węzłów zadań|Liczba|Łącznie|Liczba węzłów oczekujących na ukończenie zadania startowego|Brak|
|StartTaskFailedNodeCount|Uruchom liczbę węzłów nie powiodło się zadania|Liczba|Łącznie|Liczba węzłów, w których zadanie uruchamiania nie powiodło się|Brak|
|Liczba IdleNodeCount|Liczba węzłów bezczynnych|Liczba|Łącznie|Liczba bezczynnych węzłów|Brak|
|Liczba plików offline|Liczba węzłów w trybie offline|Liczba|Łącznie|Liczba węzłów w trybie offline|Brak|
|Ponowne uruchamianienodecount|Ponowna liczba węzłów|Liczba|Łącznie|Liczba węzłów ponownego uruchamiania|Brak|
|ReimagingNodeCount (Liczba reimagingnodecount)|Liczba węzłów reimaging|Liczba|Łącznie|Liczba węzłów ponownego zagosnania|Brak|
|RunningNodeCount (RunningNodeCount)|Liczba uruchomionych węzłów|Liczba|Łącznie|Liczba uruchomionych węzłów|Brak|
|LeavingPoolNodeCount|Opuszczanie liczby węzłów puli|Liczba|Łącznie|Liczba węzłów opuszczających pulę|Brak|
|BezużytecznyNodeCount|Liczba węzłów bezużytecznych|Liczba|Łącznie|Liczba węzłów bezużytecznych|Brak|
|WywłaszczoneNodeCount|Wywłaszczona liczba węzłów|Liczba|Łącznie|Liczba wywłaszczonych węzłów|Brak|
|ZadanieStartEvent|Zdarzenia rozpoczęcia zadania|Liczba|Łącznie|Całkowita liczba uruchomionych zadań|poolId,jobId|
|ZadanieZamyknięcie|Zdarzenia ukończenia zadania|Liczba|Łącznie|Całkowita liczba ukończonych zadań|poolId,jobId|
|ZadanieFailEvent (Zadanie)|Zdarzenia niepowodzenia zadania|Liczba|Łącznie|Całkowita liczba zadań, które zostały wykonane w stanie awarii|poolId,jobId|
|PoolCreateEvent (Twórstwo na basenie)|Zdarzenia tworzenia puli|Liczba|Łącznie|Łączna liczba utworzonych puli|pulaId|
|Proces poolresizestartevent|Zdarzenia rozpoczęcia ponownego rozmiaru puli|Liczba|Łącznie|Całkowita liczba rozmiarów puli, które zostały uruchomione|pulaId|
|Impreza PoolResizeCompleteEvent|Zdarzenia ukończenia ponownego rozmiaru rozmiaru puli|Liczba|Łącznie|Całkowita liczba przesiąknów puli, które zostały ukończone|pulaId|
|Impreza PoolDeleteStartEvent|Zdarzenia rozpoczęcia usuwania puli|Liczba|Łącznie|Całkowita liczba usuniętych puli, które zostały rozpoczęte|pulaId|
|PoolDeleteCompleteEVent|Zdarzenia usuwania puli|Liczba|Łącznie|Całkowita liczba usuniętych puli, które zostały ukończone|pulaId|
|JobDeleteCompleteEVent|Zdarzenia zakończenia usuwania zadania|Liczba|Łącznie|Całkowita liczba zadań, które zostały pomyślnie usunięte.|Jobid|
|JobDeleteStartEvent (ZadanieDeletStartEvent)|Zdarzenia rozpoczęcia usuwania zadań|Liczba|Łącznie|Całkowita liczba zadań, które zostały żądane do usunięcia.|Jobid|
|JobDisableCompleteEVent (JobDisableCompleteEvent)|Zadanie wyłączanie zakończonych zdarzeń|Liczba|Łącznie|Całkowita liczba zadań, które zostały pomyślnie wyłączone.|Jobid|
|JobDisableStartEvent (ZadanieDisableStartEvent)|Wyłączanie zdarzeń startowych zadania|Liczba|Łącznie|Całkowita liczba zadań, które zostały żądane do wyłączenia.|Jobid|
|JobStartEvent (Początek zadania)|Zdarzenia rozpoczęcia zadania|Liczba|Łącznie|Całkowita liczba zadań, które zostały pomyślnie uruchomione.|Jobid|
|JobTerminateCompleteEvent (JobTerminateCompleteEvent)|Zakończenie zadania zdarzenia zakończone|Liczba|Łącznie|Całkowita liczba zadań, które zostały pomyślnie zakończone.|Jobid|
|JobTerminateStartEvent (ZadanieTerminateStartEvent)|Zakończenie zadania zdarzenia startowe|Liczba|Łącznie|Całkowita liczba zadań, które zostały żądane do zakończenia.|Jobid|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/obszary robocze

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Przesłane zadanie|Przesłane zadanie|Liczba|Łącznie|Liczba zgłoszonych ofert pracy|Scenariusz,Nazwa klastra|
|Zadanie zakończone|Zadanie zakończone|Liczba|Łącznie|Liczba ukończonych zadań|Scenariusz,Nazwa klastra,Typ wyniku|
|Łączna liczba węzłów|Łączna liczba węzłów|Liczba|Średnia|Liczba węzłów całkowitych|Scenariusz,Nazwa klastra|
|Aktywne węzły|Aktywne węzły|Liczba|Średnia|Liczba uruchomionych węzłów|Scenariusz,Nazwa klastra|
|Węzły bezczynne|Węzły bezczynne|Liczba|Średnia|Liczba bezczynnych węzłów|Scenariusz,Nazwa klastra|
|Niezniszalne węzły|Niezniszalne węzły|Liczba|Średnia|Liczba węzłów bezużytecznych|Scenariusz,Nazwa klastra|
|Wstępnie wywłaszczone węzły|Wstępnie wywłaszczone węzły|Liczba|Średnia|Liczba wywłaszczonych węzłów|Scenariusz,Nazwa klastra|
|Opuszczanie węzłów|Opuszczanie węzłów|Liczba|Średnia|Liczba węzłów opuszczających|Scenariusz,Nazwa klastra|
|Całkowita liczba rdzeni|Całkowita liczba rdzeni|Liczba|Średnia|Liczba całkowitej liczby rdzeni|Scenariusz,Nazwa klastra|
|Aktywne rdzenie|Aktywne rdzenie|Liczba|Średnia|Liczba aktywnych rdzeni|Scenariusz,Nazwa klastra|
|Rdzenie bezczynne|Rdzenie bezczynne|Liczba|Średnia|Liczba bezczynnych rdzeni|Scenariusz,Nazwa klastra|
|Niezniszalne rdzenie|Niezniszalne rdzenie|Liczba|Średnia|Liczba niezniszalnych rdzeni|Scenariusz,Nazwa klastra|
|Wywłaszczone rdzenie|Wywłaszczone rdzenie|Liczba|Średnia|Liczba wywłaszczonych rdzeni|Scenariusz,Nazwa klastra|
|Opuszczanie rdzeni|Opuszczanie rdzeni|Liczba|Średnia|Liczba rdzeni opuszczających|Scenariusz,Nazwa klastra|
|Procent wykorzystania przydziału|Procent wykorzystania przydziału|Liczba|Średnia|Procent wykorzystanego przydziału|Scenariusz,Nazwa klastra,Nazwa wirtualna,VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainCzłonek

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|Procent użycia procesora CPU|Wartość procentowa|Maksimum|Procent użycia procesora CPU|Węzeł|
|MemoryUsage (Narzędzie pamięci)|Użycie pamięci|Bajty|Średnia|Użycie pamięci|Węzeł|
|Memorylimit|Limit pamięci|Bajty|Średnia|Limit pamięci|Węzeł|
|MemoryUsagePercentageInDouble|Procent użycia pamięci|Wartość procentowa|Średnia|Procent użycia pamięci|Węzeł|
|StorageUsage|Użycie pamięci masowej|Bajty|Średnia|Użycie pamięci masowej|Węzeł|
|IOReadBytes (IOReadBytes)|Bajty odczytu we/wy|Bajty|Łącznie|Bajty odczytu we/wy|Węzeł|
|Bajty IOWrite|Bajty zapisu we/wy|Bajty|Łącznie|Bajty zapisu we/wy|Węzeł|
|PołączenieAkceptowane|Akceptowane połączenia|Liczba|Łącznie|Akceptowane połączenia|Węzeł|
|PołączenieHandled|Obsługiwane połączenia|Liczba|Łącznie|Obsługiwane połączenia|Węzeł|
|ConnectionActive (Aktywny połączenie)|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Węzeł|
|RequestHandled (Nieobsługiwowany)|Obsługiwane żądania|Liczba|Łącznie|Obsługiwane żądania|Węzeł|
|ProcessedBlocks|Przetworzone bloki|Liczba|Łącznie|Przetworzone bloki|Węzeł|
|PrzetworzoneTransakcje|Przetworzone transakcje|Liczba|Łącznie|Przetworzone transakcje|Węzeł|
|OczekująceTransakcje|Oczekujące transakcje|Liczba|Średnia|Oczekujące transakcje|Węzeł|
|Usługi w kolejce|Transakcje w kolejce|Liczba|Średnia|Transakcje w kolejce|Węzeł|



## <a name="microsoftcacheredis"></a>Pamięć podręczna/redis firmy Microsoft.Cache

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|podłączoneclients|Podłączeni klienci|Liczba|Maksimum||ShardId|
|totalcommandsprocessed|Łączna liczba operacji|Liczba|Łącznie||ShardId|
|cachehits (cachehits)|Trafienia w pamięci podręcznej|Liczba|Łącznie||ShardId|
|cachemisses|Chybienia w pamięci podręcznej|Liczba|Łącznie||ShardId|
|buforowaniemissrate|Współczynnik pominięcia pamięci podręcznej|Wartość procentowa|buforowaniemissrate||ShardId|
|getcommands ( getcommands )|Pobiera|Liczba|Łącznie||ShardId|
|setcommands (ustawianie pokańczów)|Zestawy|Liczba|Łącznie||ShardId|
|operacjePerSekunda|Operacje na sekundę|Liczba|Maksimum||ShardId|
|eksmitowane klucze|Eksmitowane klucze|Liczba|Łącznie||ShardId|
|totalkeys (całkowita liczba kluczy)|Całkowita liczba kluczy|Liczba|Maksimum||ShardId|
|expiredkeys|Wygasłe klucze|Liczba|Łącznie||ShardId|
|używanememory|Używana pamięć|Bajty|Maksimum||ShardId|
|usedmemorypercentage|Procent używanej pamięci|Wartość procentowa|Maksimum||ShardId|
|używane memoryrss|Używane pamięci RSS|Bajty|Maksimum||ShardId|
|serverLoad (Obciążenie serwerem)|Obciążenie serwera|Wartość procentowa|Maksimum||ShardId|
|cacheWrite (buforowanie)|Zapis w pamięci podręcznej|PrzeztówPerSekunda|Maksimum||ShardId|
|cacheCzytaj|Odczyt pamięci podręcznej|PrzeztówPerSekunda|Maksimum||ShardId|
|procentProcesorTime|Procesor CPU|Wartość procentowa|Maksimum||ShardId|
|cacheLatency (CacheLatency)|Mikrosekundy opóźnienia pamięci podręcznej (wersja zapoznawcza)|Liczba|Średnia||ShardId|
|błędy|Errors|Liczba|Maksimum||Identyfikator niezależnego fragmentu,typ błędu|
|connectedclients0|Połączeni klienci (odłamek 0)|Liczba|Maksimum||Brak|
|totalcommandsprocessed0|Całkowita liczba operacji (odłamek 0)|Liczba|Łącznie||Brak|
|cachehits0|Trafienia w pamięci podręcznej (odłamek 0)|Liczba|Łącznie||Brak|
|cachemisses0|Chybienia pamięci podręcznej (odłamek 0)|Liczba|Łącznie||Brak|
|getcommands0|Pobiera (odłamek 0)|Liczba|Łącznie||Brak|
|setcommands0 setcommands0 setcommands0 setcom|Zestawy (odłamek 0)|Liczba|Łącznie||Brak|
|operacjePerSecond0|Operacje na sekundę (odłamek 0)|Liczba|Maksimum||Brak|
|eksmitowany klucze0|Eksmitowane klucze (odłamek 0)|Liczba|Łącznie||Brak|
|totalkeys0|Całkowita liczba kluczy (odłamek 0)|Liczba|Maksimum||Brak|
|expiredkeys0|Wygasłe klucze (odłamek 0)|Liczba|Łącznie||Brak|
|używanememory0|Używana pamięć (odłamek 0)|Bajty|Maksimum||Brak|
|używanememoryRss0|Używane pamięci RSS (odłamek 0)|Bajty|Maksimum||Brak|
|serverLoad0|Obciążenie serwera (fragment 0)|Wartość procentowa|Maksimum||Brak|
|cacheWrite0|Zapis pamięci podręcznej (fragment 0)|PrzeztówPerSekunda|Maksimum||Brak|
|cacheRead0|Odczyt pamięci podręcznej (fragment 0)|PrzeztówPerSekunda|Maksimum||Brak|
|procentProcesorCzas0|Procesor (odłamek 0)|Wartość procentowa|Maksimum||Brak|
|connectedclients1|Połączeni klienci (odłamek 1)|Liczba|Maksimum||Brak|
|totalcommandsprocessed1|Całkowita liczba operacji (odłamek 1)|Liczba|Łącznie||Brak|
|cachehits1|Trafienia w pamięci podręcznej (odłamek 1)|Liczba|Łącznie||Brak|
|cachemisses1|Chybienia w pamięci podręcznej (odłamek 1)|Liczba|Łącznie||Brak|
|getcommands1|Pobiera (odłamek 1)|Liczba|Łącznie||Brak|
|setcommands1|Zestawy (odłamek 1)|Liczba|Łącznie||Brak|
|operacjePerSecond1|Operacje na sekundę (odłamek 1)|Liczba|Maksimum||Brak|
|eksmitowane klucze1|Eksmitowane klucze (odłamek 1)|Liczba|Łącznie||Brak|
|totalkeys1|Całkowita liczba kluczy (odłamek 1)|Liczba|Maksimum||Brak|
|expiredkeys1|Wygasłe klucze (odłamek 1)|Liczba|Łącznie||Brak|
|używanememory1|Używana pamięć (odłamek 1)|Bajty|Maksimum||Brak|
|używanememoryRss1|Używane pamięci RSS (odłamek 1)|Bajty|Maksimum||Brak|
|serverLoad1|Obciążenie serwera (fragment 1)|Wartość procentowa|Maksimum||Brak|
|cacheWrite1|Zapis pamięci podręcznej (fragment 1)|PrzeztówPerSekunda|Maksimum||Brak|
|pamięć podręcznaCzytaj1|Odczyt pamięci podręcznej (fragment 1)|PrzeztówPerSekunda|Maksimum||Brak|
|procentProcesorTime1|Procesor (odłamek 1)|Wartość procentowa|Maksimum||Brak|
|connectedclients2|Połączeni klienci (odłamek 2)|Liczba|Maksimum||Brak|
|totalcommandsprocessed2|Całkowita liczba operacji (odłamek 2)|Liczba|Łącznie||Brak|
|cachehits2|Trafienia w pamięci podręcznej (odłamek 2)|Liczba|Łącznie||Brak|
|cachemisses2|Chybienia w pamięci podręcznej (odłamek 2)|Liczba|Łącznie||Brak|
|getcommands2|Pobiera (odłamek 2)|Liczba|Łącznie||Brak|
|setcommands2|Zestawy (odłamek 2)|Liczba|Łącznie||Brak|
|operacjePerSecond2|Operacje na sekundę (odłamek 2)|Liczba|Maksimum||Brak|
|eksmitowanykeys2|Eksmitowane klucze (odłamek 2)|Liczba|Łącznie||Brak|
|totalkeys2|Całkowita liczba kluczy (odłamek 2)|Liczba|Maksimum||Brak|
|expiredkeys2|Wygasłe klucze (odłamek 2)|Liczba|Łącznie||Brak|
|używanememory2|Używana pamięć (odłamek 2)|Bajty|Maksimum||Brak|
|używanememoryRss2|Używane pamięci RSS (odłamek 2)|Bajty|Maksimum||Brak|
|serverLoad2|Obciążenie serwera (fragment 2)|Wartość procentowa|Maksimum||Brak|
|cacheWrite2|Zapis pamięci podręcznej (fragment 2)|PrzeztówPerSekunda|Maksimum||Brak|
|pamięć podręcznaCzytaj2|Odczyt pamięci podręcznej (fragment 2)|PrzeztówPerSekunda|Maksimum||Brak|
|procentProcesorTime2|Procesor (odłamek 2)|Wartość procentowa|Maksimum||Brak|
|connectedclients3|Połączeni klienci (odłamek 3)|Liczba|Maksimum||Brak|
|totalcommandsprocessed3|Całkowita liczba operacji (odłamek 3)|Liczba|Łącznie||Brak|
|cachehits3|Trafienia w pamięci podręcznej (odłamek 3)|Liczba|Łącznie||Brak|
|cachemisses3|Chybienia w pamięci podręcznej (odłamek 3)|Liczba|Łącznie||Brak|
|getcommands3|Pobiera (odłamek 3)|Liczba|Łącznie||Brak|
|setcommands3|Zestawy (odłamek 3)|Liczba|Łącznie||Brak|
|operacjePerSecond3|Operacje na sekundę (odłamek 3)|Liczba|Maksimum||Brak|
|eksmitowanykeys3|Eksmitowane klucze (odłamek 3)|Liczba|Łącznie||Brak|
|totalkeys3|Całkowita liczba kluczy (odłamek 3)|Liczba|Maksimum||Brak|
|expiredkeys3|Wygasłe klucze (odłamek 3)|Liczba|Łącznie||Brak|
|używanememory3|Używana pamięć (odłamek 3)|Bajty|Maksimum||Brak|
|używanememoryRss3|Używane pamięci RSS (odłamek 3)|Bajty|Maksimum||Brak|
|serverLoad3|Obciążenie serwera (fragment 3)|Wartość procentowa|Maksimum||Brak|
|cacheWrite3|Zapis pamięci podręcznej (fragment 3)|PrzeztówPerSekunda|Maksimum||Brak|
|pamięć cacheRead3|Odczyt pamięci podręcznej (fragment 3)|PrzeztówPerSekunda|Maksimum||Brak|
|procentProcesorTime3|Procesor (odłamek 3)|Wartość procentowa|Maksimum||Brak|
|connectedclients4|Połączeni klienci (odłamek 4)|Liczba|Maksimum||Brak|
|totalcommandsprocessed4|Całkowita liczba operacji (odłamek 4)|Liczba|Łącznie||Brak|
|cachehits4|Trafienia w pamięci podręcznej (odłamek 4)|Liczba|Łącznie||Brak|
|cachemisses4|Chybienia w pamięci podręcznej (odłamek 4)|Liczba|Łącznie||Brak|
|getcommands4|Pobiera (odłamek 4)|Liczba|Łącznie||Brak|
|setcommands4|Zestawy (odłamek 4)|Liczba|Łącznie||Brak|
|operacjePerSecond4|Operacje na sekundę (odłamek 4)|Liczba|Maksimum||Brak|
|eksmitowanykeys4|Eksmitowane klucze (odłamek 4)|Liczba|Łącznie||Brak|
|totalkeys4|Całkowita liczba kluczy (odłamek 4)|Liczba|Maksimum||Brak|
|expiredkeys4|Wygasłe klucze (odłamek 4)|Liczba|Łącznie||Brak|
|używanememory4|Używana pamięć (odłamek 4)|Bajty|Maksimum||Brak|
|używanememoryRss4|Używane pamięci RSS (odłamek 4)|Bajty|Maksimum||Brak|
|serverLoad4|Obciążenie serwera (fragment 4)|Wartość procentowa|Maksimum||Brak|
|cacheWrite4|Zapis pamięci podręcznej (fragment 4)|PrzeztówPerSekunda|Maksimum||Brak|
|pamięć cacheRead4|Odczyt pamięci podręcznej (fragment 4)|PrzeztówPerSekunda|Maksimum||Brak|
|procentProcesorTime4|Procesor (odłamek 4)|Wartość procentowa|Maksimum||Brak|
|connectedclients5|Połączeni klienci (odłamek 5)|Liczba|Maksimum||Brak|
|totalcommandsprocessed5|Całkowita liczba operacji (odłamek 5)|Liczba|Łącznie||Brak|
|cachehits5|Trafienia w pamięci podręcznej (odłamek 5)|Liczba|Łącznie||Brak|
|cachemisses5|Chybienia w pamięci podręcznej (odłamek 5)|Liczba|Łącznie||Brak|
|getcommands5|Pobiera (odłamek 5)|Liczba|Łącznie||Brak|
|setcommands5|Zestawy (odłamek 5)|Liczba|Łącznie||Brak|
|operacjePerSecond5|Operacje na sekundę (odłamek 5)|Liczba|Maksimum||Brak|
|eksmitowane klucze5|Eksmitowane klucze (odłamek 5)|Liczba|Łącznie||Brak|
|totalkeys5|Całkowita liczba kluczy (odłamek 5)|Liczba|Maksimum||Brak|
|expiredkeys5|Wygasłe klucze (odłamek 5)|Liczba|Łącznie||Brak|
|używanememory5|Używana pamięć (odłamek 5)|Bajty|Maksimum||Brak|
|używanememoryRss5|Używane pamięci RSS (odłamek 5)|Bajty|Maksimum||Brak|
|serverLoad5|Obciążenie serwera (fragment 5)|Wartość procentowa|Maksimum||Brak|
|cacheWrite5|Zapis pamięci podręcznej (fragment 5)|PrzeztówPerSekunda|Maksimum||Brak|
|pamięć podręcznaCzytaj5|Odczyt pamięci podręcznej (fragment 5)|PrzeztówPerSekunda|Maksimum||Brak|
|procentProcesorTime5|Procesor (odłamek 5)|Wartość procentowa|Maksimum||Brak|
|connectedclients6|Połączeni klienci (odłamek 6)|Liczba|Maksimum||Brak|
|totalcommandsprocessed6|Całkowita liczba operacji (odłamek 6)|Liczba|Łącznie||Brak|
|cachehits6|Trafienia w pamięci podręcznej (odłamek 6)|Liczba|Łącznie||Brak|
|cachemisses6|Chybienia w pamięci podręcznej (odłamek 6)|Liczba|Łącznie||Brak|
|getcommands6|Pobiera (odłamek 6)|Liczba|Łącznie||Brak|
|setcommands6|Zestawy (odłamek 6)|Liczba|Łącznie||Brak|
|operacjePerSecond6|Operacje na sekundę (odłamek 6)|Liczba|Maksimum||Brak|
|eksmitowane klucze6|Eksmitowane klucze (odłamek 6)|Liczba|Łącznie||Brak|
|totalkeys6|Całkowita liczba kluczy (odłamek 6)|Liczba|Maksimum||Brak|
|expiredkeys6|Wygasłe klucze (odłamek 6)|Liczba|Łącznie||Brak|
|używanememory6|Używana pamięć (odłamek 6)|Bajty|Maksimum||Brak|
|używanememoryRss6|Używane pamięci RSS (odłamek 6)|Bajty|Maksimum||Brak|
|serverLoad6|Obciążenie serwera (fragment 6)|Wartość procentowa|Maksimum||Brak|
|cacheWrite6|Zapis pamięci podręcznej (fragment 6)|PrzeztówPerSekunda|Maksimum||Brak|
|pamięć cacheRead6|Odczyt pamięci podręcznej (fragment 6)|PrzeztówPerSekunda|Maksimum||Brak|
|procentProcesorTime6|Procesor (odłamek 6)|Wartość procentowa|Maksimum||Brak|
|connectedclients7|Połączeni klienci (odłamek 7)|Liczba|Maksimum||Brak|
|totalcommandsprocessed7|Całkowita liczba operacji (odłamek 7)|Liczba|Łącznie||Brak|
|cachehits7|Trafienia w pamięci podręcznej (odłamek 7)|Liczba|Łącznie||Brak|
|cachemisses7|Chybienia w pamięci podręcznej (odłamek 7)|Liczba|Łącznie||Brak|
|getcommands7|Pobiera (odłamek 7)|Liczba|Łącznie||Brak|
|setcommands7|Zestawy (odłamek 7)|Liczba|Łącznie||Brak|
|operacjePerSecond7|Operacje na sekundę (odłamek 7)|Liczba|Maksimum||Brak|
|eksmitowane klucze7|Eksmitowane klucze (odłamek 7)|Liczba|Łącznie||Brak|
|totalkeys7|Całkowita liczba kluczy (odłamek 7)|Liczba|Maksimum||Brak|
|expiredkeys7|Wygasłe klucze (odłamek 7)|Liczba|Łącznie||Brak|
|używanememory7|Używana pamięć (odłamek 7)|Bajty|Maksimum||Brak|
|używanememoryRss7|Używane pamięci RSS (odłamek 7)|Bajty|Maksimum||Brak|
|serverLoad7|Obciążenie serwera (fragment 7)|Wartość procentowa|Maksimum||Brak|
|cacheWrite7|Zapis pamięci podręcznej (fragment 7)|PrzeztówPerSekunda|Maksimum||Brak|
|pamięć podręcznaCzytaj7|Odczyt pamięci podręcznej (fragment 7)|PrzeztówPerSekunda|Maksimum||Brak|
|procentProcesorTime7|Procesor (odłamek 7)|Wartość procentowa|Maksimum||Brak|
|connectedclients8|Połączeni klienci (odłamek 8)|Liczba|Maksimum||Brak|
|totalcommandsprocessed8|Całkowita liczba operacji (odłamek 8)|Liczba|Łącznie||Brak|
|cachehits8|Trafienia w pamięci podręcznej (odłamek 8)|Liczba|Łącznie||Brak|
|cachemisses8|Chybienia w pamięci podręcznej (odłamek 8)|Liczba|Łącznie||Brak|
|getcommands8|Pobiera (odłamek 8)|Liczba|Łącznie||Brak|
|setcommands8|Zestawy (odłamek 8)|Liczba|Łącznie||Brak|
|operacjePerSecond8|Operacje na sekundę (odłamek 8)|Liczba|Maksimum||Brak|
|eksmitowane klucze8|Eksmitowane klucze (odłamek 8)|Liczba|Łącznie||Brak|
|totalkeys8|Całkowita liczba kluczy (odłamek 8)|Liczba|Maksimum||Brak|
|expiredkeys8|Wygasłe klucze (odłamek 8)|Liczba|Łącznie||Brak|
|używanememory8|Używana pamięć (odłamek 8)|Bajty|Maksimum||Brak|
|używanememoryRss8|Używane pamięci RSS (odłamek 8)|Bajty|Maksimum||Brak|
|serverLoad8|Obciążenie serwera (fragment 8)|Wartość procentowa|Maksimum||Brak|
|cacheWrite8|Zapis pamięci podręcznej (fragment 8)|PrzeztówPerSekunda|Maksimum||Brak|
|pamięć podręcznaCzytaj8|Odczyt pamięci podręcznej (fragment 8)|PrzeztówPerSekunda|Maksimum||Brak|
|procentProcesorTime8|Procesor (odłamek 8)|Wartość procentowa|Maksimum||Brak|
|connectedclients9|Połączeni klienci (odłamek 9)|Liczba|Maksimum||Brak|
|totalcommandsprocessed9|Całkowita liczba operacji (odłamek 9)|Liczba|Łącznie||Brak|
|cachehits9|Trafienia w pamięci podręcznej (odłamek 9)|Liczba|Łącznie||Brak|
|cachemisses9|Chybienia w pamięci podręcznej (odłamek 9)|Liczba|Łącznie||Brak|
|getcommands9|Pobiera (odłamek 9)|Liczba|Łącznie||Brak|
|setcommands9|Zestawy (odłamek 9)|Liczba|Łącznie||Brak|
|operacjePerSecond9|Operacje na sekundę (odłamek 9)|Liczba|Maksimum||Brak|
|eksmitowane klucze9|Eksmitowane klucze (odłamek 9)|Liczba|Łącznie||Brak|
|totalkeys9|Całkowita liczba kluczy (odłamek 9)|Liczba|Maksimum||Brak|
|expiredkeys9|Wygasłe klucze (odłamek 9)|Liczba|Łącznie||Brak|
|używanememory9|Używana pamięć (odłamek 9)|Bajty|Maksimum||Brak|
|używanememoryRss9|Używane pamięci RSS (odłamek 9)|Bajty|Maksimum||Brak|
|serverLoad9|Obciążenie serwera (niezależnego fragmentu 9)|Wartość procentowa|Maksimum||Brak|
|cacheWrite9|Zapis pamięci podręcznej (fragment 9)|PrzeztówPerSekunda|Maksimum||Brak|
|pamięć cacheRead9|Odczyt pamięci podręcznej (fragment 9)|PrzeztówPerSekunda|Maksimum||Brak|
|procentProcesorTime9|Procesor (odłamek 9)|Wartość procentowa|Maksimum||Brak|




## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Konto WebApplicationFirewallRequestCount|Liczba żądań zapory aplikacji sieci Web|Liczba|Łącznie|Liczba żądań klientów przetworzonych przez Zaporę aplikacji sieci Web|Nazwa zasad,Nazwa reguły,Akcja|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMacyny

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Wartość procentowa|Średnia|Procent przydzielonych jednostek obliczeniowych, które są obecnie używane przez maszynę wirtualną.|Brak|
|Sieć — wejście|Sieć — wejście|Bajty|Łącznie|Liczba bajtów odebranych we wszystkich interfejsach sieciowych przez maszynę wirtualną (Ruch przychodzący).|Brak|
|Sieć — wyjście|Sieć — wyjście|Bajty|Łącznie|Liczba bajtów na wszystkich interfejsach sieciowych przez maszynę wirtualną (ruch wychodzący).|Brak|
|Bajty odczytu dysku/s|Odczyt dysku|PrzeztówPerSekunda|Średnia|Średni bajtów odczytywanych z dysku w okresie monitorowania.|Brak|
|Bajty zapisu dysku/s|Zapis dysku|PrzeztówPerSekunda|Średnia|Średnie bajty zapisane na dysku w okresie monitorowania.|Brak|
|Operacje odczytu dysku/s|Operacje odczytu dysku/s|Połów połówek|Średnia|Odczyt dysku IOPS.|Brak|
|Operacje zapisu dysku/s|Operacje zapisu dysku/s|Połów połówek|Średnia|Wyw.|Brak|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Wartość procentowa|Średnia|Procent przydzielonych jednostek obliczeniowych, które są obecnie używane przez maszynę wirtualną.|Nieumiejęt roli|
|Sieć — wejście|Sieć — wejście|Bajty|Łącznie|Liczba bajtów odebranych we wszystkich interfejsach sieciowych przez maszynę wirtualną (Ruch przychodzący).|Nieumiejęt roli|
|Sieć — wyjście|Sieć — wyjście|Bajty|Łącznie|Liczba bajtów na wszystkich interfejsach sieciowych przez maszynę wirtualną (ruch wychodzący).|Nieumiejęt roli|
|Bajty odczytu dysku/s|Odczyt dysku|PrzeztówPerSekunda|Średnia|Średni bajtów odczytywanych z dysku w okresie monitorowania.|Nieumiejęt roli|
|Bajty zapisu dysku/s|Zapis dysku|PrzeztówPerSekunda|Średnia|Średnie bajty zapisane na dysku w okresie monitorowania.|Nieumiejęt roli|
|Operacje odczytu dysku/s|Operacje odczytu dysku/s|Połów połówek|Średnia|Odczyt dysku IOPS.|Nieumiejęt roli|
|Operacje zapisu dysku/s|Operacje zapisu dysku/s|Połów połówek|Średnia|Wyw.|Nieumiejęt roli|



## <a name="microsoftclassicstoragestorageaccounts"></a>Konta programu Microsoft.ClassicStorage/storageKonta

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|UżywaneCapacity|Używana pojemność|Bajty|Średnia|Pojemność używanego konta|Brak|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru ResponseType dla liczby różnych typów odpowiedzi.|Typ odpowiedzi,Typ geograficzny,Nazwa api,Uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|Milisekund|Średnia|Opóźnienie używane przez usługę Azure Storage do przetwarzania pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie E2E dla powodzenia|Milisekund|Średnia|Opóźnienie end-to-end pomyślnych żądań złożonych do usługi magazynu lub określonej operacji interfejsu API w milisekundach. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|Dostępność|Dostępność|Wartość procentowa|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Typ geograficzny,Nazwa api,Uwierzytelnianie|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageKondyfika/obiektdusługi

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BlobCapacity (BlobCapacity)|Pojemność obiektu blob|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez usługę obiektów Blob konta magazynu w bajtach.|Typ obiektu blob,warstwa|
|Liczba obiektów BlobCount|Liczba obiektów blob|Liczba|Średnia|Liczba obiektów blob w usłudze obiektów Blob konta magazynu.|Typ obiektu blob,warstwa|
|Liczba kontenerów|Liczba kontenerów obiektów blob|Liczba|Średnia|Liczba kontenerów w usłudze obiektów Blob konta magazynu.|Brak|
|Indeks Zdolności|Pojemność indeksu|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez indeks ADLS Gen2 (hierarchiczny) w bajtach.|Brak|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru ResponseType dla liczby różnych typów odpowiedzi.|Typ odpowiedzi,Typ geograficzny,Nazwa api,Uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|Milisekund|Średnia|Opóźnienie używane przez usługę Azure Storage do przetwarzania pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie E2E dla powodzenia|Milisekund|Średnia|Opóźnienie end-to-end pomyślnych żądań złożonych do usługi magazynu lub określonej operacji interfejsu API w milisekundach. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|Dostępność|Dostępność|Wartość procentowa|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Typ geograficzny,Nazwa api,Uwierzytelnianie|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageKonta/tableSługi

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TableCapacity (Zdolność do pońoliczy)|Pojemność tabeli|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez usługę tabel konta magazynu w bajtach.|Brak|
|Liczba tabel|Liczba tabel|Liczba|Średnia|Liczba tabeli w usłudze tabel konta magazynu.|Brak|
|Liczba entity tabeli|Liczba jednostek tabeli|Liczba|Średnia|Liczba jednostek tabeli w usłudze tabel konta magazynu.|Brak|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru ResponseType dla liczby różnych typów odpowiedzi.|Typ odpowiedzi,Typ geograficzny,Nazwa api,Uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|Milisekund|Średnia|Opóźnienie używane przez usługę Azure Storage do przetwarzania pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie E2E dla powodzenia|Milisekund|Średnia|Opóźnienie end-to-end pomyślnych żądań złożonych do usługi magazynu lub określonej operacji interfejsu API w milisekundach. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|Dostępność|Dostępność|Wartość procentowa|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Typ geograficzny,Nazwa api,Uwierzytelnianie|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageKonta/fileServices

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Zdolność do pończęcia plików|Pojemność pliku|Bajty|Średnia|Ilość miejsca używanego przez usługę plików konta magazynu w bajtach.|Fileshare|
|Liczba plików|Liczba plików|Liczba|Średnia|Liczba plików w usłudze Plik konta magazynu.|Fileshare|
|Liczba plików ShareCount|Liczba udziałów plików|Liczba|Średnia|Liczba udziałów plików w usłudze Plik konta magazynu.|Brak|
|Konto FileShareSnapshotCount|Liczba migawek udziału plików|Liczba|Średnia|Liczba migawek obecnych w udziale w usłudze plików konta magazynu.|Fileshare|
|Rozmiar plikuShareSnapshotSize|Rozmiar migawki udziału plików|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez migawki w usłudze plików na koncie magazynu w bajtach.|Fileshare|
|FileShareQuota (Polski)|Rozmiar przydziału udziału plików|Bajty|Średnia|Górny limit ilości magazynu, który może być używany przez usługę Azure Files Service w bajtach.|Fileshare|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru ResponseType dla liczby różnych typów odpowiedzi.|Typ odpowiedzi,Typ geograficzny,Nazwa api,Uwierzytelnianie,Udostępnianie pliku|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Typ geograficzny,Nazwa api,Uwierzytelnianie,Udostępnianie plików|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Typ geograficzny,Nazwa api,Uwierzytelnianie,Udostępnianie plików|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|Milisekund|Średnia|Opóźnienie używane przez usługę Azure Storage do przetwarzania pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|Typ geograficzny,Nazwa api,Uwierzytelnianie,Udostępnianie plików|
|SuccessE2ELatency|Opóźnienie E2E dla powodzenia|Milisekund|Średnia|Opóźnienie end-to-end pomyślnych żądań złożonych do usługi magazynu lub określonej operacji interfejsu API w milisekundach. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Typ geograficzny,Nazwa api,Uwierzytelnianie,Udostępnianie plików|
|Dostępność|Dostępność|Wartość procentowa|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Typ geograficzny,Nazwa api,Uwierzytelnianie,Udostępnianie plików|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageKonta/kolejkaUsługi

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QueueCapacity (Zdolność do kolejek)|Pojemność kolejki|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez usługę kolejka konta magazynu w bajtach.|Brak|
|Liczba kolejek|Liczba kolejek|Liczba|Średnia|Liczba kolejek w usłudze Kolejka konta magazynu.|Brak|
|KolejkaLiczna liczba|Liczba wiadomości w kolejce|Liczba|Średnia|Przybliżona liczba komunikatów kolejki w usłudze Kolejka konta magazynu.|Brak|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru ResponseType dla liczby różnych typów odpowiedzi.|Typ odpowiedzi,Typ geograficzny,Nazwa api,Uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|Milisekund|Średnia|Opóźnienie używane przez usługę Azure Storage do przetwarzania pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie E2E dla powodzenia|Milisekund|Średnia|Opóźnienie end-to-end pomyślnych żądań złożonych do usługi magazynu lub określonej operacji interfejsu API w milisekundach. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|Dostępność|Dostępność|Wartość procentowa|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Typ geograficzny,Nazwa api,Uwierzytelnianie|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Całkowita liczba powołów|Łączna liczba połączeń|Liczba|Łącznie|Całkowita liczba połączeń.|Nazwa api,Nazwa operacji,Region|
|Udane rozmowy|Udane połączenia|Liczba|Łącznie|Liczba udanych połączeń.|Nazwa api,Nazwa operacji,Region|
|TotalErrors ( TotalErrors )|Całkowita liczba błędów|Liczba|Łącznie|Całkowita liczba połączeń z odpowiedzią na błąd (kod odpowiedzi HTTP 4xx lub 5xx).|Nazwa api,Nazwa operacji,Region|
|BlockedCalls (Zablokowane telefony)|Zablokowane połączenia|Liczba|Łącznie|Liczba wywołań, które przekroczyły limit stawki lub przydziału.|Nazwa api,Nazwa operacji,Region|
|Serwery serwerowe|Błędy serwera|Liczba|Łącznie|Liczba połączeń z wewnętrznym błędem usługi (kod odpowiedzi HTTP 5xx).|Nazwa api,Nazwa operacji,Region|
|KlientErrors (KlientErrors)|Błędy klienta|Liczba|Łącznie|Liczba połączeń z błędem po stronie klienta (kod odpowiedzi HTTP 4xx).|Nazwa api,Nazwa operacji,Region|
|DataIn (wyjdą|Dane w|Bajty|Łącznie|Rozmiar przychodzących danych w bajtach.|Nazwa api,Nazwa operacji,Region|
|DataOut (DataOut)|Wyjście danych|Bajty|Łącznie|Rozmiar danych wychodzących w bajtach.|Nazwa api,Nazwa operacji,Region|
|Opóźnienie|Opóźnienie|Milisekund|Średnia|Opóźnienie w milisekundach.|Nazwa api,Nazwa operacji,Region|
|TotalTokenCalls (Liczba powołów)|Łączna liczba wywołań tokenów|Liczba|Łącznie|Całkowita liczba wywołań tokenu.|Nazwa api,Nazwa operacji,Region|
|Znaki Przetłumaczone|Przetłumaczone znaki|Liczba|Łącznie|Całkowita liczba znaków w przychodzącym żądaniu tekstowym.|Nazwa api,Nazwa operacji,Region|
|ZnakiSzkolone|Postacie wyszkolone|Liczba|Łącznie|Całkowita liczba przeszkolonych znaków.|Nazwa api,Nazwa operacji,Region|
|MowaSessionDuration|Czas trwania sesji mowy|Sekundy|Łącznie|Całkowity czas trwania sesji mowy w sekundach.|Nazwa api,Nazwa operacji,Region|
|TotalTransakcje|Łączna liczba transakcji|Liczba|Łącznie|Łączna liczba transakcji.|Brak|
|Przetworzonematyges|Przetworzone obrazy|Liczba|Łącznie| Liczba transakcji do przetwarzania obrazu.|ApiName,FeatureName,Kanał,Region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Wartość procentowa|Średnia|Procent przydzielonych jednostek obliczeniowych, które są obecnie używane przez maszynę wirtualną|Brak|
|Sieć — wejście|Sieć w rozliczanej (przestarzała)|Bajty|Łącznie|Liczba bajtów podlegaalnych z obciążeniem dla wszystkich interfejsów sieciowych przez maszynę wirtualną (ruch przychodzący) (przestarzały)|Brak|
|Sieć — wyjście|Wyjęty z sieci (przestarzały)|Bajty|Łącznie|Liczba bajtów podlegających rozliczaniu na wszystkich interfejsach sieciowych przez maszynę wirtualną (ruch wychodzący) (przestarzały)|Brak|
|Bajty odczytu dysku|Bajty odczytu dysku|Bajty|Łącznie|Bajty odczytywane z dysku w okresie monitorowania|Brak|
|Bajty zapisu dysku|Bajty zapisu dysku|Bajty|Łącznie|Bajty zapisywane na dysku w okresie monitorowania|Brak|
|Operacje odczytu dysku/s|Operacje odczytu dysku/s|Połów połówek|Średnia|Odczyt dysku — we/wy|Brak|
|Operacje zapisu dysku/s|Operacje zapisu dysku/s|Połów połówek|Średnia|We/Wy zapisu dysku|Brak|
|Pozostałe kredyty CPU|Pozostałe kredyty CPU|Liczba|Średnia|Łączna liczba kredytów dostępnych do rozerwania|Brak|
|Zużyte kredyty CPU|Zużyte kredyty CPU|Liczba|Średnia|Łączna liczba kredytów wykorzystanych przez maszynę wirtualną|Brak|
|Bajty odczytu dysku/s|Bajty odczytu dysku danych/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|Połów połówek|Średnia|Odczyt bajtów/s z jednego dysku w okresie monitorowania|SlotId (SlotId)|
|Bajty zapisu na dysku/s|Bajty zapisu dysku danych/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|Połów połówek|Średnia|Bajty/s zapisane na jednym dysku w okresie monitorowania|SlotId (SlotId)|
|Operacje odczytu dysku/s|Operacje odczytu dysku danych/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|Połów połówek|Średnia|Odczyt IOPS z jednego dysku w okresie monitorowania|SlotId (SlotId)|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku danych/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|Połów połówek|Średnia|Zapisywanie we/wy na jednym dysku podczas monitorowania|SlotId (SlotId)|
|Na dysk QD|QD dysku danych [(przestarzałe)](portal-disk-metrics-deprecation.md)](portal-disk-metrics-deprecation.md)|Liczba|Średnia|Głębokość kolejki dysku danych(lub długość kolejki)|SlotId (SlotId)|
|Bajty odczytu systemu operacyjnego na dysk/s|Bajty odczytu dysku systemu operacyjnego/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|Połów połówek|Średnia|Odczyt bajtów/s z jednego dysku w okresie monitorowania dysku systemu operacyjnego|Brak|
|Bajty zapisu systemu operacyjnego na dysk/s|Bajty zapisu dysku systemu operacyjnego/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|Połów połówek|Średnia|Bajty/S zapisane na jednym dysku w okresie monitorowania dysku systemu operacyjnego|Brak|
|Operacje odczytu systemu operacyjnego na dysku/s|Operacje odczytu dysku systemu operacyjnego/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|Połów połówek|Średnia|Odczyt IOPS z jednego dysku w okresie monitorowania dysku systemu operacyjnego|Brak|
|Operacje zapisu systemu operacyjnego na dysk/s|Operacje zapisu dysku systemu operacyjnego/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|Połów połówek|Średnia|Zapisywanie we/wy na jednym dysku z jednego dysku podczas monitorowania dysku systemu operacyjnego|Brak|
|System operacyjny na dysk QD|QD dysku systemu operacyjnego [(przestarzałe)](portal-disk-metrics-deprecation.md)|Liczba|Średnia|Głębokość kolejki systemu operacyjnego (lub długość kolejki)|Brak|
|Bajty odczytu dysku danych/s|Bajty odczytu dysku danych/s (wersja zapoznawcza)|Połów połówek|Średnia|Odczyt bajtów/s z jednego dysku w okresie monitorowania|Jednostki lun|
|Bajty zapisu dysku danych/s|Bajty zapisu dysku danych/s (wersja zapoznawcza)|Połów połówek|Średnia|Bajty/s zapisane na jednym dysku w okresie monitorowania|Jednostki lun|
|Operacje odczytu dysku danych/s|Operacje odczytu dysku danych/s (wersja zapoznawcza)|Połów połówek|Średnia|Odczyt IOPS z jednego dysku w okresie monitorowania|Jednostki lun|
|Operacje zapisu na dysku danych/s|Operacje zapisu na dysku danych/s (wersja zapoznawcza)|Połów połówek|Średnia|Zapisywanie we/wy na jednym dysku podczas monitorowania|Jednostki lun|
|Głębokość kolejki dysku danych|Głębokość kolejki dysków danych (wersja zapoznawcza)|Liczba|Średnia|Głębokość kolejki dysku danych(lub długość kolejki)|Jednostki lun|
|Bajty odczytu dysku systemu operacyjnego na sekundę|Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)|Połów połówek|Średnia|Odczyt bajtów/s z jednego dysku w okresie monitorowania dysku systemu operacyjnego|Brak|
|Bajty zapisu dysku systemu operacyjnego na sekundę|Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)|Połów połówek|Średnia|Bajty/S zapisane na jednym dysku w okresie monitorowania dysku systemu operacyjnego|Brak|
|Operacje odczytu dysku systemu operacyjnego/s|Operacje odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)|Połów połówek|Średnia|Odczyt IOPS z jednego dysku w okresie monitorowania dysku systemu operacyjnego|Brak|
|Operacje zapisu dysku systemu operacyjnego/s|Operacje zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)|Połów połówek|Średnia|Zapisywanie we/wy na jednym dysku z jednego dysku podczas monitorowania dysku systemu operacyjnego|Brak|
|Głębokość kolejki dysku systemu operacyjnego|Głębokość kolejki systemu operacyjnego (wersja zapoznawcza)|Liczba|Średnia|Głębokość kolejki systemu operacyjnego (lub długość kolejki)|Brak|
|Przepływy przychodzące|Przepływy przychodzące|Liczba|Średnia|Przepływy przychodzące to liczba przepływów bieżących w kierunku przychodzącym (ruch przechodzący do maszyny Wirtualnej)|Brak|
|Przepływy wychodzące|Przepływy wychodzące|Liczba|Średnia|Przepływy wychodzące to liczba przepływów bieżących w kierunku wychodzącym (ruch wychodzący z maszyny Wirtualnej)|Brak|
|Maksymalna szybkość tworzenia przepływów przychodzących|Maksymalna szybkość tworzenia przepływów przychodzących|Połów połówek|Średnia|Maksymalna szybkość tworzenia przepływów przychodzących (ruch przechodzący do maszyny Wirtualnej)|Brak|
|Maksymalna szybkość tworzenia przepływów wychodzących|Maksymalna szybkość tworzenia przepływów wychodzących|Połów połówek|Średnia|Maksymalna szybkość tworzenia przepływów wychodzących (ruch wychodzący z maszyny Wirtualnej)|Brak|
|Trafienie odczytu pamięci podręcznej dysków danych premium|Trafienie odczytu pamięci podręcznej dysków premium (wersja zapoznawcza)|Wartość procentowa|Średnia|Trafienie odczytu pamięci podręcznej dysków danych premium|Jednostki lun|
|Odczyt pamięci podręcznej dysku z danymi w warstwie Premium|Odczyt pamięci podręcznej dysków premium (wersja zapoznawcza)|Wartość procentowa|Średnia|Odczyt pamięci podręcznej dysku z danymi w warstwie Premium|Jednostki lun|
|Trafienie odczytu pamięci podręcznej dysku systemu operacyjnego premium|Trafienie odczytu pamięci podręcznej dysku premium OS (wersja zapoznawcza)|Wartość procentowa|Średnia|Trafienie odczytu pamięci podręcznej dysku systemu operacyjnego premium|Brak|
|Odczyt pamięci podręcznej systemu operacyjnego Premium|Odczyt pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Wartość procentowa|Średnia|Odczyt pamięci podręcznej systemu operacyjnego Premium|Brak|
|Sieć w sumie|Sieć w sumie|Bajty|Łącznie|Liczba bajtów odebranych we wszystkich interfejsach sieciowych przez maszynę wirtualną (ruch przychodzący)|Brak|
|Całkowita liczba wyjść sieci|Całkowita liczba wyjść sieci|Bajty|Łącznie|Liczba bajtów na wszystkich interfejsach sieciowych przez maszynę wirtualną (ruch wychodzący)|Brak|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Wartość procentowa|Średnia|Procent przydzielonych jednostek obliczeniowych, które są obecnie używane przez maszynę wirtualną|VMName|
|Sieć — wejście|Sieć w rozliczanej (przestarzała)|Bajty|Łącznie|Liczba bajtów podlegaalnych z obciążeniem dla wszystkich interfejsów sieciowych przez maszynę wirtualną (ruch przychodzący) (przestarzały)|VMName|
|Sieć — wyjście|Wyjęty z sieci (przestarzały)|Bajty|Łącznie|Liczba bajtów podlegających rozliczaniu na wszystkich interfejsach sieciowych przez maszynę wirtualną (ruch wychodzący) (przestarzały)|VMName|
|Bajty odczytu dysku|Bajty odczytu dysku|Bajty|Łącznie|Bajty odczytywane z dysku w okresie monitorowania|VMName|
|Bajty zapisu dysku|Bajty zapisu dysku|Bajty|Łącznie|Bajty zapisywane na dysku w okresie monitorowania|VMName|
|Operacje odczytu dysku/s|Operacje odczytu dysku/s|Połów połówek|Średnia|Odczyt dysku — we/wy|VMName|
|Operacje zapisu dysku/s|Operacje zapisu dysku/s|Połów połówek|Średnia|We/Wy zapisu dysku|VMName|
|Pozostałe kredyty CPU|Pozostałe kredyty CPU|Liczba|Średnia|Łączna liczba kredytów dostępnych do rozerwania|Brak|
|Zużyte kredyty CPU|Zużyte kredyty CPU|Liczba|Średnia|Łączna liczba kredytów wykorzystanych przez maszynę wirtualną|Brak|
|Bajty odczytu dysku/s|Bajty odczytu dysku danych/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|Połów połówek|Średnia|Odczyt bajtów/s z jednego dysku w okresie monitorowania|SlotId (SlotId)|
|Bajty zapisu na dysku/s|Bajty zapisu dysku danych/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|Połów połówek|Średnia|Bajty/s zapisane na jednym dysku w okresie monitorowania|SlotId (SlotId)|
|Operacje odczytu dysku/s|Operacje odczytu dysku danych/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|Połów połówek|Średnia|Odczyt IOPS z jednego dysku w okresie monitorowania|SlotId (SlotId)|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku danych/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|Połów połówek|Średnia|Zapisywanie we/wy na jednym dysku podczas monitorowania|SlotId (SlotId)|
|Na dysk QD|QD dysku danych [(przestarzałe)](portal-disk-metrics-deprecation.md)|Liczba|Średnia|Głębokość kolejki dysku danych(lub długość kolejki)|SlotId (SlotId)|
|Bajty odczytu systemu operacyjnego na dysk/s|Bajty odczytu dysku systemu operacyjnego/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|Połów połówek|Średnia|Odczyt bajtów/s z jednego dysku w okresie monitorowania dysku systemu operacyjnego|Brak|
|Bajty zapisu systemu operacyjnego na dysk/s|Bajty zapisu dysku systemu operacyjnego/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|Połów połówek|Średnia|Bajty/S zapisane na jednym dysku w okresie monitorowania dysku systemu operacyjnego|Brak|
|Operacje odczytu systemu operacyjnego na dysku/s|Operacje odczytu dysku systemu operacyjnego/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|Połów połówek|Średnia|Odczyt IOPS z jednego dysku w okresie monitorowania dysku systemu operacyjnego|Brak|
|Operacje zapisu systemu operacyjnego na dysk/s|Operacje zapisu dysku systemu operacyjnego/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|Połów połówek|Średnia|Zapisywanie we/wy na jednym dysku z jednego dysku podczas monitorowania dysku systemu operacyjnego|Brak|
|System operacyjny na dysk QD|QD dysku systemu operacyjnego [(przestarzałe)](portal-disk-metrics-deprecation.md)|Liczba|Średnia|Głębokość kolejki systemu operacyjnego (lub długość kolejki)|Brak|
|Bajty odczytu dysku danych/s|Bajty odczytu dysku danych/s (wersja zapoznawcza)|Połów połówek|Średnia|Odczyt bajtów/s z jednego dysku w okresie monitorowania|LUN,Nazwa VM|
|Bajty zapisu dysku danych/s|Bajty zapisu dysku danych/s (wersja zapoznawcza)|Połów połówek|Średnia|Bajty/s zapisane na jednym dysku w okresie monitorowania|LUN,Nazwa VM|
|Operacje odczytu dysku danych/s|Operacje odczytu dysku danych/s (wersja zapoznawcza)|Połów połówek|Średnia|Odczyt IOPS z jednego dysku w okresie monitorowania|LUN,Nazwa VM|
|Operacje zapisu na dysku danych/s|Operacje zapisu na dysku danych/s (wersja zapoznawcza)|Połów połówek|Średnia|Zapisywanie we/wy na jednym dysku podczas monitorowania|LUN,Nazwa VM|
|Głębokość kolejki dysku danych|Głębokość kolejki dysków danych (wersja zapoznawcza)|Liczba|Średnia|Głębokość kolejki dysku danych(lub długość kolejki)|LUN,Nazwa VM|
|Bajty odczytu dysku systemu operacyjnego na sekundę|Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)|Połów połówek|Średnia|Odczyt bajtów/s z jednego dysku w okresie monitorowania dysku systemu operacyjnego|VMName|
|Bajty zapisu dysku systemu operacyjnego na sekundę|Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)|Połów połówek|Średnia|Bajty/S zapisane na jednym dysku w okresie monitorowania dysku systemu operacyjnego|VMName|
|Operacje odczytu dysku systemu operacyjnego/s|Operacje odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)|Połów połówek|Średnia|Odczyt IOPS z jednego dysku w okresie monitorowania dysku systemu operacyjnego|VMName|
|Operacje zapisu dysku systemu operacyjnego/s|Operacje zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)|Połów połówek|Średnia|Zapisywanie we/wy na jednym dysku z jednego dysku podczas monitorowania dysku systemu operacyjnego|VMName|
|Głębokość kolejki dysku systemu operacyjnego|Głębokość kolejki systemu operacyjnego (wersja zapoznawcza)|Liczba|Średnia|Głębokość kolejki systemu operacyjnego (lub długość kolejki)|VMName|
|Przepływy przychodzące|Przepływy przychodzące|Liczba|Średnia|Przepływy przychodzące to liczba przepływów bieżących w kierunku przychodzącym (ruch przechodzący do maszyny Wirtualnej)|VMName|
|Przepływy wychodzące|Przepływy wychodzące|Liczba|Średnia|Przepływy wychodzące to liczba przepływów bieżących w kierunku wychodzącym (ruch wychodzący z maszyny Wirtualnej)|VMName|
|Maksymalna szybkość tworzenia przepływów przychodzących|Maksymalna szybkość tworzenia przepływów przychodzących|Połów połówek|Średnia|Maksymalna szybkość tworzenia przepływów przychodzących (ruch przechodzący do maszyny Wirtualnej)|VMName|
|Maksymalna szybkość tworzenia przepływów wychodzących|Maksymalna szybkość tworzenia przepływów wychodzących|Połów połówek|Średnia|Maksymalna szybkość tworzenia przepływów wychodzących (ruch wychodzący z maszyny Wirtualnej)|VMName|
|Trafienie odczytu pamięci podręcznej dysków danych premium|Trafienie odczytu pamięci podręcznej dysków premium (wersja zapoznawcza)|Wartość procentowa|Średnia|Trafienie odczytu pamięci podręcznej dysków danych premium|LUN,Nazwa VM|
|Odczyt pamięci podręcznej dysku z danymi w warstwie Premium|Odczyt pamięci podręcznej dysków premium (wersja zapoznawcza)|Wartość procentowa|Średnia|Odczyt pamięci podręcznej dysku z danymi w warstwie Premium|LUN,Nazwa VM|
|Trafienie odczytu pamięci podręcznej dysku systemu operacyjnego premium|Trafienie odczytu pamięci podręcznej dysku premium OS (wersja zapoznawcza)|Wartość procentowa|Średnia|Trafienie odczytu pamięci podręcznej dysku systemu operacyjnego premium|VMName|
|Odczyt pamięci podręcznej systemu operacyjnego Premium|Odczyt pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Wartość procentowa|Średnia|Odczyt pamięci podręcznej systemu operacyjnego Premium|VMName|
|Sieć w sumie|Sieć w sumie|Bajty|Łącznie|Liczba bajtów odebranych we wszystkich interfejsach sieciowych przez maszynę wirtualną (ruch przychodzący)|VMName|
|Całkowita liczba wyjść sieci|Całkowita liczba wyjść sieci|Bajty|Łącznie|Liczba bajtów na wszystkich interfejsach sieciowych przez maszynę wirtualną (ruch wychodzący)|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Wartość procentowa|Średnia|Procent przydzielonych jednostek obliczeniowych, które są obecnie używane przez maszynę wirtualną|Brak|
|Sieć — wejście|Sieć w rozliczanej (przestarzała)|Bajty|Łącznie|Liczba bajtów podlegaalnych z obciążeniem dla wszystkich interfejsów sieciowych przez maszynę wirtualną (ruch przychodzący) (przestarzały)|Brak|
|Sieć — wyjście|Wyjęty z sieci (przestarzały)|Bajty|Łącznie|Liczba bajtów podlegających rozliczaniu na wszystkich interfejsach sieciowych przez maszynę wirtualną (ruch wychodzący) (przestarzały)|Brak|
|Bajty odczytu dysku|Bajty odczytu dysku|Bajty|Łącznie|Bajty odczytywane z dysku w okresie monitorowania|Brak|
|Bajty zapisu dysku|Bajty zapisu dysku|Bajty|Łącznie|Bajty zapisywane na dysku w okresie monitorowania|Brak|
|Operacje odczytu dysku/s|Operacje odczytu dysku/s|Połów połówek|Średnia|Odczyt dysku — we/wy|Brak|
|Operacje zapisu dysku/s|Operacje zapisu dysku/s|Połów połówek|Średnia|We/Wy zapisu dysku|Brak|
|Pozostałe kredyty CPU|Pozostałe kredyty CPU|Liczba|Średnia|Łączna liczba kredytów dostępnych do rozerwania|Brak|
|Zużyte kredyty CPU|Zużyte kredyty CPU|Liczba|Średnia|Łączna liczba kredytów wykorzystanych przez maszynę wirtualną|Brak|
|Bajty odczytu dysku/s|Bajty odczytu dysku danych/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|Połów połówek|Średnia|Odczyt bajtów/s z jednego dysku w okresie monitorowania|SlotId (SlotId)|
|Bajty zapisu na dysku/s|Bajty zapisu dysku danych/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|Połów połówek|Średnia|Bajty/s zapisane na jednym dysku w okresie monitorowania|SlotId (SlotId)|
|Operacje odczytu dysku/s|Operacje odczytu dysku danych/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|Połów połówek|Średnia|Odczyt IOPS z jednego dysku w okresie monitorowania|SlotId (SlotId)|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku danych/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|Połów połówek|Średnia|Zapisywanie we/wy na jednym dysku podczas monitorowania|SlotId (SlotId)|
|Na dysk QD|QD dysku danych [(przestarzałe)](portal-disk-metrics-deprecation.md)|Liczba|Średnia|Głębokość kolejki dysku danych(lub długość kolejki)|SlotId (SlotId)|
|Bajty odczytu systemu operacyjnego na dysk/s|Bajty odczytu dysku systemu operacyjnego/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|Połów połówek|Średnia|Odczyt bajtów/s z jednego dysku w okresie monitorowania dysku systemu operacyjnego|Brak|
|Bajty zapisu systemu operacyjnego na dysk/s|Bajty zapisu dysku systemu operacyjnego/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|Połów połówek|Średnia|Bajty/S zapisane na jednym dysku w okresie monitorowania dysku systemu operacyjnego|Brak|
|Operacje odczytu systemu operacyjnego na dysku/s|Operacje odczytu dysku systemu operacyjnego/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|Połów połówek|Średnia|Odczyt IOPS z jednego dysku w okresie monitorowania dysku systemu operacyjnego|Brak|
|Operacje zapisu systemu operacyjnego na dysk/s|Operacje zapisu dysku systemu operacyjnego/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|Połów połówek|Średnia|Zapisywanie we/wy na jednym dysku z jednego dysku podczas monitorowania dysku systemu operacyjnego|Brak|
|System operacyjny na dysk QD|QD dysku systemu operacyjnego [(przestarzałe)](portal-disk-metrics-deprecation.md)|Liczba|Średnia|Głębokość kolejki systemu operacyjnego (lub długość kolejki)|Brak|
|Bajty odczytu dysku danych/s|Bajty odczytu dysku danych/s (wersja zapoznawcza)|Połów połówek|Średnia|Odczyt bajtów/s z jednego dysku w okresie monitorowania|Jednostki lun|
|Bajty zapisu dysku danych/s|Bajty zapisu dysku danych/s (wersja zapoznawcza)|Połów połówek|Średnia|Bajty/s zapisane na jednym dysku w okresie monitorowania|Jednostki lun|
|Operacje odczytu dysku danych/s|Operacje odczytu dysku danych/s (wersja zapoznawcza)|Połów połówek|Średnia|Odczyt IOPS z jednego dysku w okresie monitorowania|Jednostki lun|
|Operacje zapisu na dysku danych/s|Operacje zapisu na dysku danych/s (wersja zapoznawcza)|Połów połówek|Średnia|Zapisywanie we/wy na jednym dysku podczas monitorowania|Jednostki lun|
|Głębokość kolejki dysku danych|Głębokość kolejki dysków danych (wersja zapoznawcza)|Liczba|Średnia|Głębokość kolejki dysku danych(lub długość kolejki)|Jednostki lun|
|Bajty odczytu dysku systemu operacyjnego na sekundę|Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)|Połów połówek|Średnia|Odczyt bajtów/s z jednego dysku w okresie monitorowania dysku systemu operacyjnego|Brak|
|Bajty zapisu dysku systemu operacyjnego na sekundę|Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)|Połów połówek|Średnia|Bajty/S zapisane na jednym dysku w okresie monitorowania dysku systemu operacyjnego|Brak|
|Operacje odczytu dysku systemu operacyjnego/s|Operacje odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)|Połów połówek|Średnia|Odczyt IOPS z jednego dysku w okresie monitorowania dysku systemu operacyjnego|Brak|
|Operacje zapisu dysku systemu operacyjnego/s|Operacje zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)|Połów połówek|Średnia|Zapisywanie we/wy na jednym dysku z jednego dysku podczas monitorowania dysku systemu operacyjnego|Brak|
|Głębokość kolejki dysku systemu operacyjnego|Głębokość kolejki systemu operacyjnego (wersja zapoznawcza)|Liczba|Średnia|Głębokość kolejki systemu operacyjnego (lub długość kolejki)|Brak|
|Przepływy przychodzące|Przepływy przychodzące|Liczba|Średnia|Przepływy przychodzące to liczba przepływów bieżących w kierunku przychodzącym (ruch przechodzący do maszyny Wirtualnej)|Brak|
|Przepływy wychodzące|Przepływy wychodzące|Liczba|Średnia|Przepływy wychodzące to liczba przepływów bieżących w kierunku wychodzącym (ruch wychodzący z maszyny Wirtualnej)|Brak|
|Maksymalna szybkość tworzenia przepływów przychodzących|Maksymalna szybkość tworzenia przepływów przychodzących|Połów połówek|Średnia|Maksymalna szybkość tworzenia przepływów przychodzących (ruch przechodzący do maszyny Wirtualnej)|Brak|
|Maksymalna szybkość tworzenia przepływów wychodzących|Maksymalna szybkość tworzenia przepływów wychodzących|Połów połówek|Średnia|Maksymalna szybkość tworzenia przepływów wychodzących (ruch wychodzący z maszyny Wirtualnej)|Brak|
|Trafienie odczytu pamięci podręcznej dysków danych premium|Trafienie odczytu pamięci podręcznej dysków premium (wersja zapoznawcza)|Wartość procentowa|Średnia|Trafienie odczytu pamięci podręcznej dysków danych premium|Jednostki lun|
|Odczyt pamięci podręcznej dysku z danymi w warstwie Premium|Odczyt pamięci podręcznej dysków premium (wersja zapoznawcza)|Wartość procentowa|Średnia|Odczyt pamięci podręcznej dysku z danymi w warstwie Premium|Jednostki lun|
|Trafienie odczytu pamięci podręcznej dysku systemu operacyjnego premium|Trafienie odczytu pamięci podręcznej dysku premium OS (wersja zapoznawcza)|Wartość procentowa|Średnia|Trafienie odczytu pamięci podręcznej dysku systemu operacyjnego premium|Brak|
|Odczyt pamięci podręcznej systemu operacyjnego Premium|Odczyt pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Wartość procentowa|Średnia|Odczyt pamięci podręcznej systemu operacyjnego Premium|Brak|
|Sieć w sumie|Sieć w sumie|Bajty|Łącznie|Liczba bajtów odebranych we wszystkich interfejsach sieciowych przez maszynę wirtualną (ruch przychodzący)|Brak|
|Całkowita liczba wyjść sieci|Całkowita liczba wyjść sieci|Bajty|Łącznie|Liczba bajtów na wszystkich interfejsach sieciowych przez maszynę wirtualną (ruch wychodzący)|Brak|

## <a name="microsoftcontainerinstancecontainergroups"></a>Instancja kontenera firmy Microsoft/kontenerGrupy

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuUsage (|Użycie procesora|Liczba|Średnia|Użycie procesora na wszystkich rdzeniach w milikores.|containerName (Nazwa kontenera)|
|MemoryUsage (Narzędzie pamięci)|Użycie pamięci|Bajty|Średnia|Całkowite użycie pamięci w bajtach.|containerName (Nazwa kontenera)|
|NetworkBytesReceivedPerSecond|Bajty sieci odebrane na sekundę|Bajty|Średnia|Bajty sieciowe odebrane na sekundę.|Brak|
|NetworkBytesTransmittedPerSecond|Bajty sieciowe przesyłane na sekundę|Bajty|Średnia|Bajty sieciowe przesyłane na sekundę.|Brak|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/rejestry

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalPullCount (Liczba całkowita)|Całkowita liczba pociągniętych|Liczba|Średnia|Całkowita liczba wyciągniętych obrazów|Brak|
|SuccessfulPullCount (Liczba powiodła się)|Pomyślna liczba ściągnięcia|Liczba|Średnia|Liczba udanych ściągań obrazu|Brak|
|Suma PushCount|Całkowita liczba wypycheń|Liczba|Średnia|Całkowita liczba wypychanych obrazów|Brak|
|UdanyPulszcount|Pomyślna liczba wypycheń|Liczba|Średnia|Liczba udanych wypychań obrazu|Brak|
|UruchomienieDuration|Czas trwania biegu|Milisekund|Łącznie|Uruchom czas trwania w milisekundach|Brak|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Całkowita liczba dostępnych rdzeni procesora w zarządzanym klastrze|Liczba|Średnia|Całkowita liczba dostępnych rdzeni procesora w zarządzanym klastrze|Brak|
|kube_node_status_allocatable_memory_bytes|Całkowita ilość dostępnej pamięci w klastrze zarządzanym|Bajty|Średnia|Całkowita ilość dostępnej pamięci w klastrze zarządzanym|Brak|
|kube_pod_status_ready|Liczba zasobników w stanie Gotowy|Liczba|Średnia|Liczba zasobników w stanie Gotowy|obszar nazw,zasobnik|
|kube_node_status_condition|Stany dla różnych warunków węzła|Liczba|Średnia|Stany dla różnych warunków węzła|warunek,stan,stan2,węzeł|
|kube_pod_status_phase|Liczba strąków według fazy|Liczba|Średnia|Liczba strąków według fazy|faza,obszar nazw,zasobnik|



## <a name="microsoftcustomprovidersresourceproviders"></a>Dostawcy/dostawcy zasobów microsoft.custom

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Udane prośby|Pomyślne żądania|Liczba|Łącznie|Pomyślne żądania złożone przez dostawcę niestandardowego|Httpmethod,CallPath,Kod stanu|
|Nieudane zapisy|Żądania zakończone niepowodzeniem|Liczba|Łącznie|Pobiera dostępne dzienniki dla dostawców zasobów niestandardowych|Httpmethod,CallPath,Kod stanu|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|NicReadThroughput|Przepływność odczytu (sieć)|PrzeztówPerSekunda|Średnia|Przepływność odczytu interfejsu sieciowego na urządzeniu w okresie raportowania dla wszystkich woluminów w bramie.|InstanceName|
|NicWriteThroughput|Przepływność zapisu (sieć)|PrzeztówPerSekunda|Średnia|Przepływność zapisu interfejsu sieciowego na urządzeniu w okresie raportowania dla wszystkich woluminów w bramie.|InstanceName|
|ChmuraReadThroughPutPerShare|Przepływność pobierania w chmurze (udział)|PrzeztówPerSekunda|Średnia|Przepływność pobierania na platformę Azure z udziału w okresie raportowania.|Udostępnij|
|CloudUploadThroughPutPerShare|Przepływność przekazywania w chmurze (udział)|PrzeztówPerSekunda|Średnia|Przepływność przekazywania do platformy Azure z udziału w okresie raportowania.|Udostępnij|
|BytesUploadedToCloudPerShare|Przesłane bajty w chmurze (udostępnianie)|Bajty|Średnia|Całkowita liczba bajtów, które są przekazywane do platformy Azure z udziału w okresie raportowania.|Udostępnij|
|Całkowita zdolność produkcyjna|Całkowita pojemność|Bajty|Średnia|Całkowita pojemność|Brak|
|DostępneJastość|Dostępna pojemność|Bajty|Średnia|Dostępna pojemność w bajtach w okresie sprawozdawczym.|Brak|
|CloudUploadThroughput|Przepływność przekazywania w chmurze|PrzeztówPerSekunda|Średnia|Przepływność przekazywania w chmurze na platformę Azure w okresie raportowania.|Brak|
|ChmuraReadThroughput|Przepływność pobierania w chmurze|PrzeztówPerSekunda|Średnia|Przepływność pobierania w chmurze na platformę Azure w okresie raportowania.|Brak|
|BytesUploadedToCloud|Przesłane bajty w chmurze (urządzenie)|Bajty|Średnia|Całkowita liczba bajtów, które są przekazywane na platformę Azure z urządzenia w okresie raportowania.|Brak|
|HyperVVirtualProcessorUtilization|Obliczeń krawędziowych — procent procesora CPU|Wartość procentowa|Średnia|Procent użycia procesora CPU|InstanceName|
|HyperVMemoryutilization|Obliczanie krawędzi — użycie pamięci|Wartość procentowa|Średnia|Ilość używanej pamięci RAM|InstanceName|


## <a name="microsoftdatacatalogdatacatalogs"></a>Microsoft.DataCatalog/datacatalogs

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Klasyfikacja danych assetdistributionByClassification|Podział aktywów według klasyfikacji|Liczba|Łącznie|Wskazuje liczbę aktywów z określoną przypisaną klasyfikacją, to znaczy są one klasyfikowane z tą etykietą.|Klasyfikacja,Źródło|
|AssetDistributionByStorageType|Dystrybucja zasobów według typu magazynu|Liczba|Łącznie|Wskazuje liczbę zasobów o określonym typie magazynu.|Rodzaj magazynu|
|NumberOfAssetsWithClassifications|Liczba aktywów o co najmniej jednej klasyfikacji|Liczba|Średnia|Wskazuje liczbę zasobów z co najmniej jedną klasyfikacją znaczników.|Brak|
|SkanowanieCancelled|Skanowanie anulowane|Liczba|Łącznie|Wskazuje liczbę anulowanych skanów.|Brak|
|SkanowanieNieukoletowane|Skanowanie zakończone|Liczba|Łącznie|Wskazuje liczbę skanów zakończonych pomyślnie.|Brak|
|ScanFailed (Nieumiejętnieszone|Skanowanie nie powiodło się|Liczba|Łącznie|Wskazuje liczbę skanów, które nie powiodły się.|Brak|
|Czas skanowaniaWyjętego|Czas skanowania|Sekundy|Łącznie|Wskazuje całkowity czas skanowania w sekundach.|Brak|
|CatalogActiveUsers|Codziennie aktywni użytkownicy|Liczba|Łącznie|Liczba aktywnych użytkowników dziennie|Brak|
|KatalogPodań|Rozkład użycia według operacji|Liczba|Łącznie|Wskazać liczbę operacji, które użytkownik wykonuje w katalogu, tj.|Operacja|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories Microsoft.DataFactory/datafactories Microsoft.DataFactory/datafactories Microsoft.

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Nieudaneruny|Nieudane przebiegi|Liczba|Łącznie||nazwa potoku,nazwa działania|
|Udaneruny|Udane przebiegi|Liczba|Łącznie||nazwa potoku,nazwa działania|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/fabryki

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PipelineFailedRuns (Nieupowłożone runy potoku)|Nieudane przebiegi potoku|Liczba|Łącznie||Typ błędu,Nazwa|
|PipelineSucceedRuns|Powiodło się, że potok uruchamia metryki|Liczba|Łącznie||Typ błędu,Nazwa|
|PipelineCancelledRuns|Anulowane przebiegi potoku metryki|Liczba|Łącznie||Typ błędu,Nazwa|
|DziałalnośćFailedRuns|Działanie nie powiodło się uruchamia metryki|Liczba|Łącznie||ActivityType,Nazwa potoku,Typ błędu,Nazwa|
|DziałalnośćSucceedRuns|Działanie, które powiodło się, uruchamia metryki|Liczba|Łącznie||ActivityType,Nazwa potoku,Typ błędu,Nazwa|
|ActivityCancelledRuns (ujedn.|Anulowane działanie uruchamia dane|Liczba|Łącznie||ActivityType,Nazwa potoku,Typ błędu,Nazwa|
|TriggerFailedRuns (Nieugiętyrun)|Nieudany wyzwalacz uruchamia metryki|Liczba|Łącznie||Nazwa,Typ błędu|
|WyzwalaczeSucceedRuns|Pomyślne uruchomienie wyzwalacza powoduje|Liczba|Łącznie||Nazwa,Typ błędu|
|TriggerCancelledRuns (TriggerCancelledRuns)|Anulowane uruchomienia wyzwalacza metryki|Liczba|Łącznie||Nazwa,Typ błędu|
|IntegracjaRuntimeCpuPercentage|Wykorzystanie procesora CPU w czasie wykonywania integracji|Wartość procentowa|Średnia||IntegrationRuntimeName,Nazwa węzła|
|IntegracjaRuntimeAvailableMemory|Dostępna pamięć środowiska wykonawczego integracji|Bajty|Średnia||IntegrationRuntimeName,Nazwa węzła|
|IntegracjaRuntimeAverageTaskPickupDelay|Czas trwania kolejki środowiska uruchomieniowego integracji|Sekundy|Średnia||Nazwa integrationruntimename|
|IntegracjaRuntimeQueueLength|Długość kolejki środowiska uruchomieniowego integracji|Liczba|Średnia||Nazwa integrationruntimename|
|IntegracjaDostępna Dosyłów.|Liczba dostępnych węzłów środowiska uruchomieniowego integracji|Liczba|Średnia||Nazwa integrationruntimename|
|Maksymalna liczba zasobów maksymalnych|Liczba maksymalnych dozwolonych encji|Liczba|Maksimum||Brak|
|Jednostki MaxAllowedFactorySizeInGbUnits|Maksymalny dozwolony rozmiar fabryki (jednostka GB)|Liczba|Maksimum||Brak|
|Liczba zasobów|Łączna liczba jednostek|Liczba|Maksimum||Brak|
|Jednostki FactorySizeInGbUnits|Całkowity rozmiar fabryki (jednostka GB)|Liczba|Maksimum||Brak|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Sukces w zadaniach|Udane zadania|Liczba|Łącznie|Liczba udanych miejsc pracy.|Brak|
|JobEndedFailure (JobEndedFailure)|Zadania nie powiodły się|Liczba|Łącznie|Liczba zadań, które nie powiodły się.|Brak|
|JobEndedCancelled (Nieumiejeć)|Anulowane zadania|Liczba|Łącznie|Liczba anulowanych ofert pracy.|Brak|
|Sukces w jobauendedsukces|Udany czas AU|Sekundy|Łącznie|Całkowity czas UA dla udanych zadań.|Brak|
|JobAUEndedFailure|Nieudany czas AU|Sekundy|Łącznie|Całkowity czas UA dla zadań po awarii.|Brak|
|JobAUEndedCancelled|Anulowany czas AU|Sekundy|Łącznie|Całkowity czas UA dla anulowanych zadań.|Brak|
|JobStage (Etap pracy)|Praca na etapie|Liczba|Łącznie|Liczba zadań na każdym etapie.|Brak|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/konta

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Całkowitastorage|Łączny rozmiar magazynu|Bajty|Maksimum|Łączna ilość danych przechowywanych na koncie.|Brak|
|DataWritten (DaneWritten)|Zapisane dane|Bajty|Łącznie|Łączna ilość danych zapisanych na koncie.|Brak|
|Dataread|Odczyt danych|Bajty|Łącznie|Łączna ilość danych odczytanych z konta.|Brak|
|WriteRequests (Zapisy)|Żądania zapisu|Liczba|Łącznie|Liczba żądań zapisu danych na konto.|Brak|
|OdczytRequests|Żądania odczytu|Liczba|Łącznie|Liczba żądań odczytu danych do konta.|Brak|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/konta

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|UdziałCount|Wysłane udziały|Liczba|Maksimum|Liczba wysłanych akcji na koncie|Nazwaudziału|
|Konto ShareSubscription|Odebrane udziały|Liczba|Maksimum|Liczba otrzymanych akcji na rachunku|Nazwa sharesubscription|
|PomyślnieSynchronizacja akcji|Wysłane migawki udziału powiodło się|Liczba|Liczba|Liczba wysłanych migawek z udziałem powiodła się na koncie|Brak|
|Nieudana synchronizacja udostępnień|Wysłane migawki udziału nie powiodły się|Liczba|Liczba|Liczba wysłanych migawek udziału nie powiodło się na koncie|Brak|
|PomyślnieShareSubscriptionSynchronizations|Odebrane migawki udziału powiodło się|Liczba|Liczba|Liczba odebranych migawek udziału powiodło się na koncie|Brak|
|Nieudane synchronizacje sharesubscription|Odebrane migawki udziału nie powiodły się|Liczba|Liczba|Liczba odebranych migawek udziału nie powiodło się na koncie|Brak|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/serwery

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent procesora|Wartość procentowa|Średnia|Procent procesora|Brak|
|memory_percent|Procent pamięci|Wartość procentowa|Średnia|Procent pamięci|Brak|
|io_consumption_percent|Procent IO|Wartość procentowa|Średnia|Procent IO|Brak|
|storage_percent|Procent miejsca|Wartość procentowa|Średnia|Procent miejsca|Brak|
|storage_used|Używany magazyn|Bajty|Średnia|Używany magazyn|Brak|
|storage_limit|Limit miejsca do magazynowania|Bajty|Maksimum|Limit miejsca do magazynowania|Brak|
|serverlog_storage_percent|Procent magazynu dziennika serwera|Wartość procentowa|Średnia|Procent magazynu dziennika serwera|Brak|
|serverlog_storage_usage|Używany magazyn dzienników serwera|Bajty|Średnia|Używany magazyn dzienników serwera|Brak|
|serverlog_storage_limit|Limit magazynu dziennika serwera|Bajty|Średnia|Limit magazynu dziennika serwera|Brak|
|active_connections|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Brak|
|connections_failed|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|Brak|
|seconds_behind_master|Opóźnienie replikacji w sekundach|Liczba|Maksimum|Opóźnienie replikacji w sekundach|Brak|
|backup_storage_used|Używany magazyn kopii zapasowych|Bajty|Średnia|Używany magazyn kopii zapasowych|Brak|
|network_bytes_egress|Sieć — wyjście|Bajty|Łącznie|Wyjście sieciowe w aktywnych połączeniach|Brak|
|network_bytes_ingress|Sieć — wejście|Bajty|Łącznie|Sieć w różnych aktywnych połączeniach|Brak|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent procesora|Wartość procentowa|Średnia|Procent procesora|Brak|
|memory_percent|Procent pamięci|Wartość procentowa|Średnia|Procent pamięci|Brak|
|io_consumption_percent|Procent IO|Wartość procentowa|Średnia|Procent IO|Brak|
|storage_percent|Procent miejsca|Wartość procentowa|Średnia|Procent miejsca|Brak|
|storage_used|Używany magazyn|Bajty|Średnia|Używany magazyn|Brak|
|storage_limit|Limit miejsca do magazynowania|Bajty|Maksimum|Limit miejsca do magazynowania|Brak|
|serverlog_storage_percent|Procent magazynu dziennika serwera|Wartość procentowa|Średnia|Procent magazynu dziennika serwera|Brak|
|serverlog_storage_usage|Używany magazyn dzienników serwera|Bajty|Średnia|Używany magazyn dzienników serwera|Brak|
|serverlog_storage_limit|Limit magazynu dziennika serwera|Bajty|Maksimum|Limit magazynu dziennika serwera|Brak|
|active_connections|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Brak|
|connections_failed|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|Brak|
|seconds_behind_master|Opóźnienie replikacji w sekundach|Liczba|Maksimum|Opóźnienie replikacji w sekundach|Brak|
|backup_storage_used|Używany magazyn kopii zapasowych|Bajty|Średnia|Używany magazyn kopii zapasowych|Brak|
|network_bytes_egress|Sieć — wyjście|Bajty|Łącznie|Wyjście sieciowe w aktywnych połączeniach|Brak|
|network_bytes_ingress|Sieć — wejście|Bajty|Łącznie|Sieć w różnych aktywnych połączeniach|Brak|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent procesora|Wartość procentowa|Średnia|Procent procesora|Brak|
|memory_percent|Procent pamięci|Wartość procentowa|Średnia|Procent pamięci|Brak|
|io_consumption_percent|Procent IO|Wartość procentowa|Średnia|Procent IO|Brak|
|storage_percent|Procent miejsca|Wartość procentowa|Średnia|Procent miejsca|Brak|
|storage_used|Używany magazyn|Bajty|Średnia|Używany magazyn|Brak|
|storage_limit|Limit miejsca do magazynowania|Bajty|Maksimum|Limit miejsca do magazynowania|Brak|
|serverlog_storage_percent|Procent magazynu dziennika serwera|Wartość procentowa|Średnia|Procent magazynu dziennika serwera|Brak|
|serverlog_storage_usage|Używany magazyn dzienników serwera|Bajty|Średnia|Używany magazyn dzienników serwera|Brak|
|serverlog_storage_limit|Limit magazynu dziennika serwera|Bajty|Maksimum|Limit magazynu dziennika serwera|Brak|
|active_connections|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Brak|
|connections_failed|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|Brak|
|backup_storage_used|Używany magazyn kopii zapasowych|Bajty|Średnia|Używany magazyn kopii zapasowych|Brak|
|network_bytes_egress|Sieć — wyjście|Bajty|Łącznie|Wyjście sieciowe w aktywnych połączeniach|Brak|
|network_bytes_ingress|Sieć — wejście|Bajty|Łącznie|Sieć w różnych aktywnych połączeniach|Brak|
|pg_replica_log_delay_in_seconds|Opóźnienie repliki|Sekundy|Maksimum|Opóźnienie repliki w sekundach|Brak|
|pg_replica_log_delay_in_bytes|Maksymalne opóźnienie w replikach|Bajty|Maksimum|Opóźnienie w bajtach najbardziej opóźnionych replik|Brak|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent procesora|Wartość procentowa|Średnia|Procent procesora|Brak|
|memory_percent|Procent pamięci|Wartość procentowa|Średnia|Procent pamięci|Brak|
|Iops|Liczba operacji we/wy na sekundę|Liczba|Średnia|Operacje we/wy na sekundę|Brak|
|storage_percent|Procent miejsca|Wartość procentowa|Średnia|Procent miejsca|Brak|
|storage_used|Używany magazyn|Bajty|Średnia|Używany magazyn|Brak|
|active_connections|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Brak|
|network_bytes_egress|Sieć — wyjście|Bajty|Łącznie|Wyjście sieciowe w aktywnych połączeniach|Brak|
|network_bytes_ingress|Sieć — wejście|Bajty|Łącznie|Sieć w różnych aktywnych połączeniach|Brak|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft.DBforPostgreSQL/singleservers

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent procesora|Wartość procentowa|Średnia|Procent procesora|Brak|
|memory_percent|Procent pamięci|Wartość procentowa|Średnia|Procent pamięci|Brak|
|Iops|Liczba operacji we/wy na sekundę|Liczba|Średnia|Operacje we/wy na sekundę|Brak|
|storage_percent|Procent miejsca|Wartość procentowa|Średnia|Procent miejsca|Brak|
|storage_used|Używany magazyn|Bajty|Średnia|Używany magazyn|Brak|
|active_connections|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Brak|
|network_bytes_egress|Sieć — wyjście|Bajty|Łącznie|Wyjście sieciowe w aktywnych połączeniach|Brak|
|network_bytes_ingress|Sieć — wejście|Bajty|Łącznie|Sieć w różnych aktywnych połączeniach|Brak|
|connections_failed|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|Brak|
|connections_succeeded|Połączenia z powodzeniem|Liczba|Łącznie|Połączenia z powodzeniem|Brak|
|maximum_used_transactionIDs|Maksymalna liczba używanych identyfikatorów transakcji|Liczba|Średnia|Maksymalna liczba używanych identyfikatorów transakcji|Brak|





## <a name="microsoftdevicesiothubs"></a>Urządzenia firmy Microsoft.Devices/IotHubs

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Próby wysyłania wiadomości telemetrycznych|Liczba|Łącznie|Liczba komunikatów telemetrycznych z urządzenia do chmury, które próbowano wysłać do centrum IoT hub|Brak|
|d2c.telemetry.ingress.success|Wysłane wiadomości telemetryczne|Liczba|Łącznie|Liczba komunikatów telemetrycznych z urządzenia do chmury wysłanych pomyślnie do centrum IoT hub|Brak|
|c2d.commands.egress.complete.success|Zakończono dostarczanie wiadomości C2D|Liczba|Łącznie|Liczba dostaw komunikatów z chmury do urządzenia zakończonych pomyślnie przez urządzenie|Brak|
|c2d.commands.egress.abandon.success|Porzucone wiadomości C2D|Liczba|Łącznie|Liczba komunikatów z chmury do urządzenia porzuconych przez urządzenie|Brak|
|c2d.commands.egress.reject.success|Wiadomości C2D odrzucone|Liczba|Łącznie|Liczba komunikatów z chmury do urządzenia odrzuconych przez urządzenie|Brak|
|C2DMessagesExpired|Wiadomości C2D wygasły (wersja zapoznawcza)|Liczba|Łącznie|Liczba wygasłych komunikatów z chmury do urządzenia|Brak|
|devices.totalUrządzenia|Łączna liczba urządzeń (przestarzała)|Liczba|Łącznie|Liczba urządzeń zarejestrowanych w centrum IoT|Brak|
|devices.connectedDevices.allProtocol|Podłączone urządzenia (przestarzałe) |Liczba|Łącznie|Liczba urządzeń podłączonych do centrum IoT|Brak|
|d2c.telemetry.egress.success|Routing: dostarczane wiadomości telemetryczne|Liczba|Łącznie|Liczba wiadomości zostały pomyślnie dostarczone do wszystkich punktów końcowych przy użyciu routingu Usługi IoT Hub. Jeśli wiadomość jest kierowana do wielu punktów końcowych, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczania. Jeśli wiadomość jest dostarczana do tego samego punktu końcowego wiele razy, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczania.|Brak|
|d2c.telemetry.egress.dropped d2c.telemetry.egress.dropped d2c.telemetry.egress.dropped d2|Routing: wiadomości telemetryczne porzucone |Liczba|Łącznie|Liczba wiadomości zostały usunięte przez routing usługi IoT Hub z powodu martwych punktów końcowych. Ta wartość nie zlicza wiadomości dostarczonych do trasy rezerwowej, ponieważ porzucone wiadomości nie są tam dostarczane.|Brak|
|d2c.telemetry.egress.orphaned|Routing: wiadomości telemetryczne oddzielone |Liczba|Łącznie|Liczba wiadomości były oddzielone przez routing usługi IoT Hub, ponieważ nie pasują do żadnych reguł routingu (w tym reguły rezerwowej). |Brak|
|d2c.telemetry.egress.invalid d2c.telemetry.egress.invalid d2c.telemetry.egress.invalid d2|Routing: komunikaty telemetryczne niezgodne|Liczba|Łącznie|Liczba razy routingu Usługi IoT Hub nie można dostarczyć wiadomości z powodu niezgodności z punktem końcowym. Ta wartość nie obejmuje ponownych prób.|Brak|
|d2c.telemetry.egress.fallback|Routing: wiadomości dostarczane do rezerwowego|Liczba|Łącznie|Liczba razy routing usługi IoT Hub dostarczane wiadomości do punktu końcowego skojarzonego z trasy rezerwowej.|Brak|
|d2c.endpoints.egress.eventHubs|Routing: wiadomości dostarczane do Centrum zdarzeń|Liczba|Łącznie|Ile razy routing usługi IoT Hub pomyślnie dostarczał wiadomości do punktów końcowych usługi Event Hub.|Brak|
|d2c.endpoints.latency.eventHubs|Routing: opóźnienie komunikatów dla Centrum zdarzeń|Milisekund|Średnia|Średnie opóźnienie (milisekundy) między wejściem w błąd i transferu komunikatu do usługi IoT Hub i transferu komunikatu przychodzącego do punktu końcowego usługi Event Hub.|Brak|
|d2c.endpoints.egress.serviceBusQueues|Routing: wiadomości dostarczane do kolejki magistrali usług|Liczba|Łącznie|Ile razy routing usługi IoT Hub pomyślnie dostarczył wiadomości do punktów końcowych kolejki usługi Service Bus.|Brak|
|d2c.endpoints.latency.serviceBusQueues|Routing: opóźnienie komunikatu dla kolejki magistrali usług|Milisekund|Średnia|Średnie opóźnienie (milisekundy) między transferem przychodzącym wiadomości do usługi IoT Hub a transferem danych wejściowych wiadomości telemetrycznych do punktu końcowego kolejki usługi Service Bus.|Brak|
|d2c.endpoints.egress.serviceBusTopics|Routing: wiadomości dostarczane do tematu magistrali usług|Liczba|Łącznie|Ile razy routing usługi IoT Hub pomyślnie dostarczył wiadomości do punktów końcowych tematu usługi Service Bus.|Brak|
|d2c.endpoints.latency.serviceBusTopics|Routing: opóźnienie komunikatu dla tematu magistrali usług|Milisekund|Średnia|Średnie opóźnienie (milisekundy) między transferem przychodzącym wiadomości do usługi IoT Hub a transferem danych wejściowych wiadomości telemetrycznych do punktu końcowego tematu usługi Service Bus.|Brak|
|d2c.endpoints.egress.builtIn.events|Routing: wiadomości dostarczane do wiadomości/zdarzeń|Liczba|Łącznie|Ile razy routing usługi IoT Hub pomyślnie dostarczył wiadomości do wbudowanego punktu końcowego (wiadomości/zdarzenia).|Brak|
|d2c.endpoints.latency.builtIn.events|Routing: opóźnienie wiadomości/zdarzeń|Milisekund|Średnia|Średnie opóźnienie (milisekundy) między wejściem w błąd i transferu danych przychodzących do usługi IoT Hub i transferu danych wejściowych do wbudowanego punktu końcowego (wiadomości/zdarzenia).|Brak|
|d2c.endpoints.egress.storage|Routing: wiadomości dostarczane do magazynu|Liczba|Łącznie|Ile razy routing usługi IoT Hub pomyślnie dostarczał wiadomości do punktów końcowych magazynu.|Brak|
|d2c.endpoints.latency.storage|Routing: opóźnienie komunikatu dla magazynu|Milisekund|Średnia|Średnie opóźnienie (milisekundy) między wejściem w błąd do usługi IoT Hub a wejściem transferu danych wejściowych do punktu końcowego magazynu.|Brak|
|d2c.endpoints.egress.storage.bytes|Routing: dane dostarczane do magazynu|Bajty|Łącznie|Ilość routingu usługi IoT Hub danych dostarczonych do punktów końcowych magazynu.|Brak|
|d2c.endpoints.egress.storage.blob|Routing: obiekty blob dostarczane do magazynu|Liczba|Łącznie|Liczba czasów routingu usługi IoT Hub dostarczane obiekty blob do punktów końcowych magazynu.|Brak|
|EventGridDeliveries|Dostawy w siatce zdarzeń (wersja zapoznawcza)|Liczba|Łącznie|Liczba zdarzeń usługi IoT Hub opublikowanych w usztywnieniu zdarzeń. Użyj wymiaru Wynik dla liczby pomyślnych i nieudanych żądań. Wymiar EventType pokazuje typhttps://aka.ms/ioteventgrid)zdarzenia ( .|Identyfikator zasobu,Wynik,Typ zdarzenia|
|EventGridLatency|Opóźnienie siatki zdarzeń (wersja zapoznawcza)|Milisekund|Średnia|Średnie opóźnienie (milisekundy) od momentu wygenerowania zdarzenia Iot Hub do momentu opublikowania zdarzenia w uścisku zdarzeń. Liczba ta jest średnią między wszystkimi typami zdarzeń. Użyj eventtype wymiar, aby zobaczyć opóźnienie określonego typu zdarzenia.|Identyfikator zasobu,Typ zdarzenia|
|Usługi routingu|Dostawy routingu (wersja zapoznawcza)|Milisekund|Łącznie|Ile razy Usługa IoT Hub próbowała dostarczać wiadomości do wszystkich punktów końcowych przy użyciu routingu. Aby wyświetlić liczbę udanych lub nieudanych prób, użyj wymiaru Wynik. Aby wyświetlić przyczynę błędu, takich jak nieprawidłowe, porzucone lub oddzielone, należy użyć FailureReasonCategory wymiaru. Można również użyć endpointname i EndpointType wymiary, aby zrozumieć, ile wiadomości zostały dostarczone do różnych punktów końcowych. Wartość metryki zwiększa się o jeden dla każdej próby dostarczenia, w tym jeśli wiadomość jest dostarczana do wielu punktów końcowych lub jeśli wiadomość jest dostarczana do tego samego punktu końcowego wiele razy.|Identyfikator zasobu,typ punktu końcowego,Nazwa punktu końcowego,Kategoria nazwa sezonu awarii,Wynik,Źródło routingu|
|RoutingDeliveryLatency|Opóźnienie dostawy routingu (wersja zapoznawcza)|Milisekund|Średnia|Średnie opóźnienie (milisekundy) między wejściem w błąd do usługi IoT Hub a wejściem transferu danych wejściowych do punktu końcowego. Można użyć endpointname i EndpointType wymiary, aby zrozumieć opóźnienie do różnych punktów końcowych.|Identyfikator zasobu,Typ punktu końcowego,Nazwa końcowa,Źródło routingu|
|d2c.twin.read.success|Udane bliźniacze odczyty z urządzeń|Liczba|Łącznie|Liczba wszystkich pomyślnych odczytów bliźniaczych zainicjowanych przez urządzenie.|Brak|
|d2c.twin.read.failure|Nieudane bliźniacze odczyty z urządzeń|Liczba|Łącznie|Liczba wszystkich odczytów bliźniaczej inicjowanych przez urządzenie nie powiodło się.|Brak|
|d2c.twin.read.size|Rozmiar odpowiedzi bliźniaczych odczytów z urządzeń|Bajty|Średnia|Średnia, min i max wszystkich udanych urządzeń zainicjowane bliźniaczej odsłony.|Brak|
|d2c.twin.update.success|Pomyślne bliźniacze aktualizacje z urządzeń|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji bliźniaczych zainicjowanych przez urządzenie.|Brak|
|d2c.twin.update.failure|Nieudane bliźniacze aktualizacje z urządzeń|Liczba|Łącznie|Liczba wszystkich aktualizacji bliźniaczych zainicjowanych przez urządzenie nie powiodło się.|Brak|
|d2c.twin.update.size|Rozmiar bliźniaczych aktualizacji z urządzeń|Bajty|Średnia|Średni, min i maksymalny rozmiar wszystkich pomyślnych aktualizacji bliźniaczych zainicjowanych przez urządzenie.|Brak|
|c2d.methods.success|Pomyślne wywołanie metody bezpośredniej|Liczba|Łącznie|Liczba wszystkich udanych wywołań metody bezpośredniej.|Brak|
|c2d.methods.failure|Wywołania metod bezpośrednich nie powiodły się|Liczba|Łącznie|Liczba wszystkich nieudanych wywołań metody bezpośredniej.|Brak|
|c2d.methods.requestSize|Rozmiar żądania wywołań metody bezpośredniej|Bajty|Średnia|Średnia, min i maks.|Brak|
|c2d.methods.responseZmializowanie|Rozmiar odpowiedzi wywołań metody bezpośredniej|Bajty|Średnia|Średnia, min i maks.|Brak|
|c2d.twin.read.success|Udane bliźniacze odczyty z zaplecza|Liczba|Łącznie|Liczba wszystkich pomyślnych wstecznych zainicjowanych odczytów bliźniaczych.|Brak|
|c2d.twin.read.failure|Nieudane bliźniacze odczyty z zaplecza|Liczba|Łącznie|Liczba wszystkich nieudanych odczytów bliźniaczych zainicjowanych zapleczem.|Brak|
|c2d.twin.read.size|Rozmiar odpowiedzi bliźniaczych odczytów z zaplecza|Bajty|Średnia|Średnia, min i max wszystkich udanych back-end inicjowane bliźniacze odczyty.|Brak|
|c2d.twin.update.success|Pomyślne podwójne aktualizacje z zaplecza|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji bliźniaczych zainicjowanych zapleczem.|Brak|
|c2d.twin.update.failure|Nieudane bliźniacze aktualizacje z zaplecza|Liczba|Łącznie|Liczba wszystkich aktualizacji bliźniaczych zainicjowanych zapleczem nie powiodło się.|Brak|
|c2d.twin.update.size|Rozmiar bliźniaczych aktualizacji z zaplecza|Bajty|Średnia|Średni, min i maksymalny rozmiar wszystkich pomyślnych aktualizacji bliźniaczych zainicjowanych zapleczem.|Brak|
|twinQueries.success|Pomyślne zapytania bliźniaczej reprezentacji|Liczba|Łącznie|Liczba wszystkich pomyślnych zapytań bliźniaczych reprezentacji.|Brak|
|twinQueries.failure|Nieudane zapytania bliźniaczej reprezentacji|Liczba|Łącznie|Liczba wszystkich nieudanych zapytań bliźniaczych reprezentacji.|Brak|
|twinQueries.resultSize|Rozmiar wyników zapytań bliźniaczych|Bajty|Średnia|Średnia, min i max rozmiaru wyniku wszystkich pomyślnych zapytań bliźniaczych.|Brak|
|jobs.createTwinUpdateJob.success|Udane tworzenie zadań bliźniaczej aktualizacji|Liczba|Łącznie|Liczba wszystkich pomyślnego tworzenia zadań aktualizacji bliźniaczej.|Brak|
|zadania.createTwinUpdateJob.failure|Nieudane tworzenie zadań aktualizacji bliźniaczej|Liczba|Łącznie|Liczba wszystkich nie powiodło się utworzenie zadań aktualizacji bliźniaczej.|Brak|
|jobs.createDirectMethodJob.success|Pomyślne tworzenie zadań wywoływania metody|Liczba|Łącznie|Liczba wszystkich pomyślnego tworzenia zadań wywołania metody bezpośredniej.|Brak|
|zadania.createDirectMethodJob.failure|Nieudane tworzenie zadań wywoływania metody|Liczba|Łącznie|Liczba wszystkich nieudanych tworzenia zadań wywołania metody bezpośredniej.|Brak|
|jobs.listJobs.success|Pomyślne wywołania listy zadań|Liczba|Łącznie|Liczba wszystkich udanych wywołań do listy zadań.|Brak|
|zadania.listJobs.failure|Nieudane wywołania listy zadań|Liczba|Łącznie|Liczba wszystkich nieudanych wywołań do listy zadań.|Brak|
|jobs.cancelJob.success|Pomyślne anulowanie zadania|Liczba|Łącznie|Liczba wszystkich pomyślnych wywołań anulowania zadania.|Brak|
|jobs.cancelJob.failure|Anulowanie zadania nie powiodło się|Liczba|Łącznie|Liczba wszystkich nieudanych wywołań anulowania zadania.|Brak|
|jobs.queryJobs.success|Pomyślne kwerendy o zadania|Liczba|Łącznie|Liczba wszystkich pomyślnych wywołań zadań zapytań.|Brak|
|zadania.queryJobs.failure|Nieudane kwerendy zadań|Liczba|Łącznie|Liczba wszystkich nieudanych wywołań zadań zapytań.|Brak|
|zadania.ukończone|Ukończone zadania|Liczba|Łącznie|Liczba wszystkich ukończonych zadań.|Brak|
|zadania.failed|Zadania nie powiodły się|Liczba|Łącznie|Liczba wszystkich zadań nie powiodło się.|Brak|
|d2c.telemetry.ingress.sendTrottle|Liczba błędów ograniczania przepustowości|Liczba|Łącznie|Liczba błędów ograniczania przepustowości z powodu przepustów przepływności urządzenia|Brak|
|dailyMessageQuotaUżywane|Łączna liczba używanych wiadomości|Liczba|Średnia|Łączna liczba używanych wiadomości|Brak|
|deviceDataUsage|Całkowite użycie danych urządzenia|Bajty|Łącznie|Bajty przesyłane do i z wszystkich urządzeń podłączonych do usługi IotHub|Brak|
|deviceDataUsageV2|Całkowite użycie danych urządzenia (podgląd)|Bajty|Łącznie|Bajty przesyłane do i z wszystkich urządzeń podłączonych do usługi IotHub|Brak|
|totalDeviceCount|Łączna liczba urządzeń (wersja zapoznawcza)|Liczba|Średnia|Liczba urządzeń zarejestrowanych w centrum IoT|Brak|
|connectedDeviceCount|Podłączone urządzenia (wersja zapoznawcza)|Liczba|Średnia|Liczba urządzeń podłączonych do centrum IoT|Brak|
|Konfiguracji|Metryki konfiguracji|Liczba|Łącznie|Metryki dla operacji konfiguracyjnych|Brak|


## <a name="microsoftdevicesprovisioningservices"></a>Urządzenia firmy Microsoft.Devices/inicjowanie obsługi administracyjnejUsługi

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RejestracjaPróbuje|Próby rejestracji|Liczba|Łącznie|Liczba prób rejestracji urządzeń|Nazwa usługi inicjowania obsługi administracyjnej,IotHubName,Stan|
|Przypisania urządzenia|Przypisane urządzenia|Liczba|Łącznie|Liczba urządzeń przypisanych do centrum IoT|Nazwa usługi inicjowania obsługi administracyjnej,IotHubName|
|AtestyPty|Próby zaświadczania|Liczba|Łącznie|Liczba prób uzyskania zaświadczeń o urządzeniu|Nazwa usługi inicjowania obsługi administracyjnej,Stan,Protokół|




## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseKonta kont

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Dodaj region|Dodano region|Liczba|Liczba|Dodano region|Region|
|DostępneStorage|Dostępna pamięć|Bajty|Łącznie|Całkowita dostępna pamięć masowa zgłoszona po 5 minutach ziarnistości|Nazwa kolekcji,Nazwa bazy danych,Region|
|CassandraConnectionClosures|Zamknięcia połączeń Cassandra|Liczba|Łącznie|Liczba połączeń Cassandra, które zostały zamknięte, zgłoszone po 1 minutze|Typ interfejsu API,Region,ZamknięciePorezon|
|CassandraKeyspaceDelete|Kasandra Keyspace usunięta|Liczba|Liczba|Kasandra Keyspace usunięta|Nazwa zasobów,ApiKind,ApiKindResourceType,OperationType|
|CassandraKeyspaceThroughputUpdate|Zaktualizowano przepływność przestrzeni kluczy Cassandra|Liczba|Liczba|Zaktualizowano przepływność przestrzeni kluczy Cassandra|Nazwa zasobów,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CassandraKeyspaceUpdate|Zaktualizowano przestrzeń kluczy Cassandra|Liczba|Liczba|Zaktualizowano przestrzeń kluczy Cassandra|Nazwa zasobów,ApiKind,ApiKindResourceType,IsThroughputRequest|
|Ładunki CassandraRequestCharge|Opłaty za żądanie Cassandra|Liczba|Łącznie|RUs zużyte dla cassandra wniosków złożonych|TYP INTERFEJSU API,Nazwa bazy danych,Nazwa kolekcji,Region,OperationType,Typ zasobu|
|CassandraSwoki|Prośby Cassandra|Liczba|Liczba|Liczba złożonych wniosków cassandra|APIType,Nazwa bazy danych,Nazwa kolekcji,Region,OperationType,ResourceType,ErrorCode|
|CassandraTableDelete (CassandraTableDelete)|Tabela Cassandra usunięta|Liczba|Liczba|Tabela Cassandra usunięta|Nazwa zasobów,ChildResourceName,ApiKind,ApiKindResourceType,OperationType|
|CassandraTableThroughputUpdate|Zaktualizowano przepustowość tabeli Cassandra|Liczba|Liczba|Zaktualizowano przepustowość tabeli Cassandra|Nazwa zasobów,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CassandraTableUpdate|Zaktualizowano tabelę Cassandra|Liczba|Liczba|Zaktualizowano tabelę Cassandra|Nazwa zasobów,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|Utwórz konto|Utworzone konto|Liczba|Liczba|Utworzone konto|Brak|
|DataUsage (DataUsage)|Użycie danych|Bajty|Łącznie|Całkowite wykorzystanie danych zgłoszone po 5 minutach szczegółowości|Nazwa kolekcji,Nazwa bazy danych,Region|
|Usuńkonta|Usunięte konto|Liczba|Liczba|Usunięte konto|Brak|
|Liczba dokumentów|Liczba dokumentów|Liczba|Łącznie|Całkowita liczba dokumentów zgłoszonych przy 5 minutach szczegółowości|Nazwa kolekcji,Nazwa bazy danych,Region|
|DocumentQuota (DokumentQuota)|Przydział dokumentu|Bajty|Łącznie|Całkowity przydział magazynowania zgłoszony przy 5 minutach szczegółowości|Nazwa kolekcji,Nazwa bazy danych,Region|
|GremlinDatabaseDelete|Baza danych Gremlin usunięta|Liczba|Liczba|Baza danych Gremlin usunięta|Nazwa zasobów,ApiKind,ApiKindResourceType,OperationType|
|GremlinDatabaseThroughputUpdate|Aktualizacja przepływności bazy danych Gremlin|Liczba|Liczba|Aktualizacja przepływności bazy danych Gremlin|Nazwa zasobów,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinDatabaseUpdate|Zaktualizowano bazę danych Gremlin|Liczba|Liczba|Zaktualizowano bazę danych Gremlin|Nazwa zasobów,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinGraphDelete|Gremlin Graph Deleted|Liczba|Liczba|Gremlin Graph Deleted|Nazwa zasobów,ChildResourceName,ApiKind,ApiKindResourceType,OperationType|
|GremlinGraphThroughputUpdate|Zaktualizowano przepustowość wykresu Gremlin|Liczba|Liczba|Zaktualizowano przepustowość wykresu Gremlin|Nazwa zasobów,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinGraphUpdate|Gremlin Graph Zaktualizowano|Liczba|Liczba|Gremlin Graph Zaktualizowano|Nazwa zasobów,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|IndeksUsage|Użycie indeksu|Bajty|Łącznie|Całkowite użycie indeksu zgłoszone po 5 minutach szczegółowości|Nazwa kolekcji,Nazwa bazy danych,Region|
|MetadataRequests|Żądania metadanych|Liczba|Liczba|Liczba żądań metadanych. Usługa Cosmos DB przechowuje zbieranie metadanych systemu dla każdego konta, co pozwala na bezpłatne wyliczanie kolekcji, baz danych itp.|Nazwa bazy danych,Nazwa kolekcji,Region,Kod stanu,Rola|
|MongoCollectionDelete|Kolekcja Mongo usunięta|Liczba|Liczba|Kolekcja Mongo usunięta|Nazwa zasobów,ChildResourceName,ApiKind,ApiKindResourceType,OperationType|
|MongoCollectionThroughputUpdate|Zaktualizowano przepustowość kolekcji Mongo|Liczba|Liczba|Zaktualizowano przepustowość kolekcji Mongo|Nazwa zasobów,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoCollectionUpdate|Zaktualizowano kolekcję Mongo|Liczba|Liczba|Zaktualizowano kolekcję Mongo|Nazwa zasobów,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoDBDatabaseUpdate|Zaktualizowano bazę danych Mongo|Liczba|Liczba|Zaktualizowano bazę danych Mongo|Nazwa zasobów,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoDatabaseDelete|Usunięto bazę danych Mongo|Liczba|Liczba|Usunięto bazę danych Mongo|Nazwa zasobów,ApiKind,ApiKindResourceType,OperationType|
|MongoDatabaseThroughputUpdate|Zaktualizowano przepływność bazy danych Mongo|Liczba|Liczba|Zaktualizowano przepływność bazy danych Mongo|Nazwa zasobów,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoRequestCharge|Opłata za żądanie Mongo|Liczba|Łącznie|Jednostki żądające Mongo zużyte|Nazwa bazy danych,Nazwa kolekcji,Region,Nazwa polecenia,Kod błędu,Stan|
|MongoRequests|Prośby Mongo|Liczba|Liczba|Liczba złożonych wniosków mongo|Nazwa bazy danych,Nazwa kolekcji,Region,Nazwa polecenia,Kod błędu,Stan|
|MongoRequestsCount|Mongo Cena żądania|Połów połówek|Średnia|Liczba żądań Mongo na sekundę|Nazwa bazy danych,Nazwa kolekcji,Region,Nazwa polecenia,Kod błędu|
|MongoRequestsDelete|Mongo Delete Cena żądania|Połów połówek|Średnia|Mongo Usuń żądanie na sekundę|Nazwa bazy danych,Nazwa kolekcji,Region,Nazwa polecenia,Kod błędu|
|MongoRequestsInsert|Mongo Wstawić cena żądania|Połów połówek|Średnia|Mongo Liczba wstawiania na sekundę|Nazwa bazy danych,Nazwa kolekcji,Region,Nazwa polecenia,Kod błędu|
|Klasztor MongoRequestsQuery|Szybkość żądania kwerendy Mongo|Połów połówek|Średnia|Żądanie mongo kwerendy na sekundę|Nazwa bazy danych,Nazwa kolekcji,Region,Nazwa polecenia,Kod błędu|
|MongoRequestsUpdate|Mongo Update Cena żądania|Połów połówek|Średnia|Żądanie aktualizacji Mongo na sekundę|Nazwa bazy danych,Nazwa kolekcji,Region,Nazwa polecenia,Kod błędu|
|Znormalizowana konsumpcjaRU|Znormalizowane zużycie RU|Wartość procentowa|Maksimum|Maksymalny procent zużycia RU na minutę|Nazwa kolekcji,Nazwa bazy danych,Region|
|AprowizowanaWyt.|Aprowizowana przepływność|Liczba|Maksimum|Aprowizowana przepływność|Nazwa bazy danych,Nazwa kolekcji|
|RegionUuUchomor|Przemij regionu po awarii|Liczba|Liczba|Przemij regionu po awarii|Brak|
|UsuńRegion|Usunięto region|Liczba|Liczba|Usunięto region|Region|
|ReplikacjaLatency|Opóźnienie replikacji P99|Milisekund|Średnia|P99 Opóźnienie replikacji w regionach źródłowych i docelowych dla konta z obsługą geograficzną|Region źródłowy,Region docelowy|
|ServerSideLatency (ServerSideLatency)|Opóźnienie po stronie serwera|Milisekund|Średnia|Opóźnienie po stronie serwera|Nazwa bazy danych,Nazwa kolekcji,Region,Tryb połączenia,OperationType,PublicAPIType|
|Dostępność usług|Dostępność usług|Wartość procentowa|Średnia|Dostępność żądań konta z dokładnością do jednej godziny, dnia lub miesiąca|Brak|
|SqlContainerDelete|Usunięty kontener SQL|Liczba|Liczba|Usunięty kontener SQL|Nazwa zasobów,ChildResourceName,ApiKind,ApiKindResourceType,OperationType|
|Dodatek SqlContainerThroughputUpdate|Zaktualizowano przepływność kontenera SQL|Liczba|Liczba|Zaktualizowano przepływność kontenera SQL|Nazwa zasobów,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|Dodatek SqlContainer|Zaktualizowano kontener SQL|Liczba|Liczba|Zaktualizowano kontener SQL|Nazwa zasobów,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|SqlDatabaseDelete|Usunięto bazę danych SQL|Liczba|Liczba|Usunięto bazę danych SQL|Nazwa zasobów,ApiKind,ApiKindResourceType,OperationType|
|SqlDatabaseThroughputUpdate|Zaktualizowano przepływność bazy danych SQL|Liczba|Liczba|Zaktualizowano przepływność bazy danych SQL|Nazwa zasobów,ApiKind,ApiKindResourceType,IsThroughputRequest|
|Dodatek SqlDatabase|Zaktualizowano bazę danych SQL|Liczba|Liczba|Zaktualizowano bazę danych SQL|Nazwa zasobów,ApiKind,ApiKindResourceType,IsThroughputRequest|
|TableTableDelete (TableTableDelete)|Usunięto tabelę usługi AzureTable|Liczba|Liczba|Usunięto tabelę usługi AzureTable|Nazwa zasobów,ApiKind,ApiKindResourceType,OperationType|
|Tabela TabelaThroughputUpdate|Zaktualizowano przepływność tabeli usługi AzureTable|Liczba|Liczba|Zaktualizowano przepływność tabeli usługi AzureTable|Nazwa zasobów,ApiKind,ApiKindResourceType,IsThroughputRequest|
|Tabela TabelaUpdate|Zaktualizowano tabelę usługi AzureTable|Liczba|Liczba|Zaktualizowano tabelę usługi AzureTable|Nazwa zasobów,ApiKind,ApiKindResourceType,IsThroughputRequest|
|Jednostki TotalRequestUnits|Łączna liczba jednostek żądań|Liczba|Łącznie|Zużyte jednostki żądań|Nazwa bazy danych,Nazwa kolekcji,Region,Kod stanu,Typ operacji,Stan|
|TotalRequests|Łączna liczba żądań|Liczba|Liczba|Liczba złożonych wniosków|Nazwa bazy danych,Nazwa kolekcji,Region,Kod stanu,Typ operacji,Stan|
|Aktualizujnaklucza konta|Zaktualizowano klucze konta|Liczba|Liczba|Zaktualizowano klucze konta|Keytype|
|AktualizacjaAccountNetworkSettings|Zaktualizowano ustawienia sieciowe konta|Liczba|Liczba|Zaktualizowano ustawienia sieciowe konta|Brak|
|UpdateAccountReplicationSettings|Zaktualizowano ustawienia replikacji konta|Liczba|Liczba|Zaktualizowano ustawienia replikacji konta|Brak|
|UpdateDiagnosticsSettings|Zaktualizowano ustawienia diagnostyczne konta|Liczba|Liczba|Zaktualizowano ustawienia diagnostyczne konta|Nazwa diagnostyki,Nazwa grupy zasobów|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft.EnterpriseKnowledgeGraph/usługi

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Liczba transakcji|Liczba transakcji|Liczba|Liczba|Łączna liczba transakcji|Liczba transakcji|
|Liczba sukcesów|Liczba powodzeń|Liczba|Liczba|Liczba transakcji powiodła się|Liczba sukcesów|
|Liczba błędów|Liczba niepowodzeń|Liczba|Liczba|Liczba nieudanych transakcji|Liczba błędów|
|SukcesLatywa|Opóźnienie sukcesu|Milisekund|Średnia|Opóźnienie udanych transakcji|Liczba sukcesów|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domeny

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PublishSuccessCount|Opublikowane wydarzenia|Liczba|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Temat|
|Konto PublishFailCount|Publikowanie zdarzeń nie powiodło się|Liczba|Łącznie|Całkowita liczba zdarzeń nie może opublikować w tym temacie|Temat,Typ błędu,Błąd|
|PublishSuccessLatencyInMs|Publikowanie opóźnienia sukcesu|Milisekund|Łącznie|Publikowanie opóźnienia sukcesu w milisekundach|Brak|
|MatchedEventCount|Dopasowane wydarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dopasowanych do tej subskrypcji wydarzenia|Temat,Nazwa pliku EventSubscriptionName,DomainEventsubscriptionName|
|DeliveryAttemptFailCount|Zdarzenia nieudane dostawy|Liczba|Łącznie|Całkowita liczba zdarzeń nie została do dostarczenia do tej subskrypcji zdarzenia|Temat,EventSubscriptionName,DomainEventsubscriptionName,Error,ErrorType|
|Liczba dostawsukcesy|Dostarczone zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dostarczonych do tej subskrypcji wydarzenia|Temat,Nazwa pliku EventSubscriptionName,DomainEventsubscriptionName|
|Miejsca doceloweProcessingDurationInMs|Docelowy czas przetwarzania|Milisekund|Średnia|Docelowy czas przetwarzania w milisekundach|Temat,Nazwa pliku EventSubscriptionName,DomainEventsubscriptionName|
|Liczba porzuconychwentów|Porzucone zdarzenia|Liczba|Łącznie|Łączna liczba usuniętych zdarzeń pasujących do tej subskrypcji zdarzenia|Temat,Nazwa pliku EventSubscriptionName,DomainEventSubscriptionName,DropReason|
|Liczba nienaliczonych liter|Zdarzenia z martwymi literami|Liczba|Łącznie|Łączna liczba martwych zdarzeń z literami pasującymi do tej subskrypcji zdarzenia|Temat,EventSubscriptionName,DomainEventSubscriptionName,DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/tematy

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PublishSuccessCount|Opublikowane wydarzenia|Liczba|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Brak|
|Konto PublishFailCount|Publikowanie zdarzeń nie powiodło się|Liczba|Łącznie|Całkowita liczba zdarzeń nie może opublikować w tym temacie|Typ błędu,Błąd|
|Niedoścignionalicznik|Niedopasowane wydarzenia|Liczba|Łącznie|Łączna liczba zdarzeń, które nie pasują do żadnej z subskrypcji zdarzeń w tym temacie|Brak|
|PublishSuccessLatencyInMs|Publikowanie opóźnienia sukcesu|Milisekund|Łącznie|Publikowanie opóźnienia sukcesu w milisekundach|Brak|
|MatchedEventCount|Dopasowane wydarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dopasowanych do tej subskrypcji wydarzenia|Nazwa zdarzenia|
|DeliveryAttemptFailCount|Zdarzenia nieudane dostawy|Liczba|Łącznie|Całkowita liczba zdarzeń nie została do dostarczenia do tej subskrypcji zdarzenia|Błąd,Typ błędu,Nazwasubscription Zdarzenia|
|Liczba dostawsukcesy|Dostarczone zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dostarczonych do tej subskrypcji wydarzenia|Nazwa zdarzenia|
|Miejsca doceloweProcessingDurationInMs|Docelowy czas przetwarzania|Milisekund|Średnia|Docelowy czas przetwarzania w milisekundach|Nazwa zdarzenia|
|Liczba porzuconychwentów|Porzucone zdarzenia|Liczba|Łącznie|Łączna liczba usuniętych zdarzeń pasujących do tej subskrypcji zdarzenia|DropReason,Nazwa zdarzenia|
|Liczba nienaliczonych liter|Zdarzenia z martwymi literami|Liczba|Łącznie|Łączna liczba martwych zdarzeń z literami pasującymi do tej subskrypcji zdarzenia|DeadLetterReason,Nazwa zdarzenia|

## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PublishSuccessCount|Opublikowane wydarzenia|Liczba|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Brak|
|Konto PublishFailCount|Publikowanie zdarzeń nie powiodło się|Liczba|Łącznie|Całkowita liczba zdarzeń nie może opublikować w tym temacie|Typ błędu,Błąd|
|Niedoścignionalicznik|Niedopasowane wydarzenia|Liczba|Łącznie|Łączna liczba zdarzeń, które nie pasują do żadnej z subskrypcji zdarzeń w tym temacie|Brak|
|PublishSuccessLatencyInMs|Publikowanie opóźnienia sukcesu|Milisekund|Łącznie|Publikowanie opóźnienia sukcesu w milisekundach|Brak|
|MatchedEventCount|Dopasowane wydarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dopasowanych do tej subskrypcji wydarzenia|Nazwa zdarzenia|
|DeliveryAttemptFailCount|Zdarzenia nieudane dostawy|Liczba|Łącznie|Całkowita liczba zdarzeń nie została do dostarczenia do tej subskrypcji zdarzenia|Błąd,Typ błędu,Nazwasubscription Zdarzenia|
|Liczba dostawsukcesy|Dostarczone zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dostarczonych do tej subskrypcji wydarzenia|Nazwa zdarzenia|
|Miejsca doceloweProcessingDurationInMs|Docelowy czas przetwarzania|Milisekund|Średnia|Docelowy czas przetwarzania w milisekundach|Nazwa zdarzenia|
|Liczba porzuconychwentów|Porzucone zdarzenia|Liczba|Łącznie|Łączna liczba usuniętych zdarzeń pasujących do tej subskrypcji zdarzenia|DropReason,Nazwa zdarzenia|
|Liczba nienaliczonych liter|Zdarzenia z martwymi literami|Liczba|Łącznie|Łączna liczba martwych zdarzeń z literami pasującymi do tej subskrypcji zdarzenia|DeadLetterReason,Nazwa zdarzenia|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|MatchedEventCount|Dopasowane wydarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dopasowanych do tej subskrypcji wydarzenia|Brak|
|DeliveryAttemptFailCount|Zdarzenia nieudane dostawy|Liczba|Łącznie|Całkowita liczba zdarzeń nie została do dostarczenia do tej subskrypcji zdarzenia|Błąd,Typ błędu|
|Liczba dostawsukcesy|Dostarczone zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dostarczonych do tej subskrypcji wydarzenia|Brak|
|Miejsca doceloweProcessingDurationInMs|Docelowy czas przetwarzania|Milisekund|Średnia|Docelowy czas przetwarzania w milisekundach|Brak|
|Liczba porzuconychwentów|Porzucone zdarzenia|Liczba|Łącznie|Łączna liczba usuniętych zdarzeń pasujących do tej subskrypcji zdarzenia|DropReason ( DropReason )|
|Liczba nienaliczonych liter|Zdarzenia z martwymi literami|Liczba|Łącznie|Łączna liczba martwych zdarzeń z literami pasującymi do tej subskrypcji zdarzenia|DeadLetterReason (Nie żyje)|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PublishSuccessCount|Opublikowane wydarzenia|Liczba|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Brak|
|Konto PublishFailCount|Publikowanie zdarzeń nie powiodło się|Liczba|Łącznie|Całkowita liczba zdarzeń nie może opublikować w tym temacie|Typ błędu,Błąd|
|Niedoścignionalicznik|Niedopasowane wydarzenia|Liczba|Łącznie|Łączna liczba zdarzeń, które nie pasują do żadnej z subskrypcji zdarzeń w tym temacie|Brak|
|PublishSuccessLatencyInMs|Publikowanie opóźnienia sukcesu|Milisekund|Łącznie|Publikowanie opóźnienia sukcesu w milisekundach|Brak|




## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/przestrzenie nazw

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Udane prośby|Pomyślne żądania|Liczba|Łącznie|Pomyślne żądania dla witryny Microsoft.EventHub.|Nazwa elementu,OperacjaWysoki|
|Serwery serwerowe|Błędy serwera.|Liczba|Łącznie|Błędy serwera dla witryny Microsoft.EventHub.|Nazwa elementu,OperacjaWysoki|
|UserErrors (UżytkownikErrors)|Błędy użytkownika.|Liczba|Łącznie|Błędy użytkownika dla witryny Microsoft.EventHub.|Nazwa elementu,OperacjaWysoki|
|QuotaExceedErrors|Przekroczone błędy przydziału.|Liczba|Łącznie|Przekroczono limit przydziału dla witryny Microsoft.EventHub.|Nazwa elementu,OperacjaWysoki|
|ThrottledRequests|Ograniczone żądania.|Liczba|Łącznie|Ograniczone żądania dla witryny Microsoft.EventHub.|Nazwa elementu,OperacjaWysoki|
|Przychodzącerepy|Żądania przychodzące|Liczba|Łącznie|Przychodzące żądania dla witryny Microsoft.EventHub.|Nazwa elementu|
|Przychodzącenaśniacje|Wiadomości przychodzące|Liczba|Łącznie|Przychodzące wiadomości dla witryny Microsoft.EventHub.|Nazwa elementu|
|Wychodzące Wiadomości|Wiadomości wychodzące|Liczba|Łącznie|Wiadomości wychodzące dla witryny Microsoft.EventHub.|Nazwa elementu|
|Przychodzących bajtów|Bajty przychodzące.|Bajty|Łącznie|Bajty przychodzące dla witryny Microsoft.EventHub.|Nazwa elementu|
|Bajty wychodzące|Bajty wychodzące.|Bajty|Łącznie|Bajty wychodzące dla witryny Microsoft.EventHub.|Nazwa elementu|
|ActiveConnections (Aktywne połączenia)|ActiveConnections (Aktywne połączenia)|Liczba|Średnia|Łączna liczba aktywnych połączeń dla witryny Microsoft.EventHub.|Brak|
|ConnectionsOtwarte|Połączenia otwarte.|Liczba|Średnia|Połączenia otwarte dla witryny Microsoft.EventHub.|Nazwa elementu|
|Połączenia Zamknięte|Połączenia zamknięte.|Liczba|Średnia|Połączenia zamknięte dla witryny Microsoft.EventHub.|Nazwa elementu|
|Dziennik CaptureBacklog|Przechwytywanie zaległości.|Liczba|Łącznie|Przechwytywanie zaległości dla microsoft.eventhub.|Nazwa elementu|
|Przechwyconewydacje|Przechwycone wiadomości.|Liczba|Łącznie|Przechwycone wiadomości dla witryny Microsoft.EventHub.|Nazwa elementu|
|Przechwycone bajty|Przechwycone bajty.|Bajty|Łącznie|Przechwycone bajty dla witryny Microsoft.EventHub.|Nazwa elementu|
|Rozmiar|Rozmiar|Bajty|Średnia|Rozmiar eventhubu w bajtach.|Nazwa elementu|
|INREQS ( INREQS )|Przychodzące żądania (przestarzałe)|Liczba|Łącznie|Całkowita liczba przychodzących żądań wysyłania dla obszaru nazw (przestarzała)|Brak|
|SUCCREQ ( SUCCREQ )|Pomyślne żądania (przestarzałe)|Liczba|Łącznie|Łączna liczba pomyślnych żądań dla obszaru nazw (przestarzała)|Brak|
|FailreQ ( failreq )|Żądania nie powiodły się (przestarzałe)|Liczba|Łącznie|Całkowita liczba nieudanych żądań dla obszaru nazw (przestarzała)|Brak|
|SVRBSY ( SVRBSY )|Błędy zajętości serwera (przestarzałe)|Liczba|Łącznie|Całkowita liczba błędów zajętych serwerów dla obszaru nazw (przestarzała)|Brak|
|Interr (w przedr.|Wewnętrzne błędy serwera (przestarzałe)|Liczba|Łącznie|Całkowita liczba błędów serwera wewnętrznego dla obszaru nazw (przestarzała)|Brak|
|MISCERR (MISCERR)|Inne błędy (przestarzałe)|Liczba|Łącznie|Całkowita liczba nieudanych żądań dla obszaru nazw (przestarzała)|Brak|
|INMSGS (WŁASK.|Wiadomości przychodzące (przestarzałe) (przestarzałe)|Liczba|Łącznie|Całkowita liczba wiadomości przychodzących dla obszaru nazw. Ta metryka jest przestarzała. Zamiast tego użyj danych Wiadomości przychodzące (przestarzałe)|Brak|
|EHINMSGS ( EHINMSGS )|Wiadomości przychodzące (przestarzałe)|Liczba|Łącznie|Całkowita liczba wiadomości przychodzących dla obszaru nazw (przestarzała)|Brak|
|OUTMSGS (WYJDĄCE)|Wiadomości wychodzące (przestarzałe) (przestarzałe)|Liczba|Łącznie|Łączna liczba wiadomości wychodzących dla obszaru nazw. Ta metryka jest przestarzała. Zamiast tego użyj danych Wiadomości wychodzących (przestarzałe)|Brak|
|EHOUTMSGS|Wiadomości wychodzące (przestarzałe)|Liczba|Łącznie|Całkowita liczba wiadomości wychodzących dla obszaru nazw (przestarzała)|Brak|
|EHINMBS ( EHINMBS )|Bajty przychodzące (przestarzałe) (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów przychodzących centrum zdarzeń dla obszaru nazw. Ta metryka jest przestarzała. Zamiast tego użyj danych Bajtów przychodzących (przestarzałe)|Brak|
|EHINBYTES (EHINBYTES)|Bajty przychodzące (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów przychodzących centrum zdarzeń dla obszaru nazw (przestarzała)|Brak|
|EHOUTMBS ( EHOUTMBS )|Bajty wychodzące (przestarzałe) (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów wychodzących usługi Event Hub dla obszaru nazw. Ta metryka jest przestarzała. Zamiast tego użyj danych bajtów wychodzących (przestarzałe)|Brak|
|EHOUTBYTES|Bajty wychodzące (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów wychodzących usługi Event Hub dla obszaru nazw (przestarzała)|Brak|
|EHABL|Archiwizuj komunikaty zaległości (przestarzałe)|Liczba|Łącznie|Usługa Event Hub archiwizacji wiadomości w zaległości dla obszaru nazw (przestarzałe)|Brak|
|EHAMSGS ( EHAMSGS )|Archiwizowanie wiadomości (przestarzałe)|Liczba|Łącznie|Usługa Event Hub archiwizowała wiadomości w obszarze nazw (przestarzałe)|Brak|
|EHAMBS ( EHAMBS )|Przepływność wiadomości archiwum (przestarzała)|Bajty|Łącznie|Usługa Event Hub zarchiwizowana przepływność wiadomości w obszarze nazw (przestarzała)|Brak|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/klastry

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Udane prośby|Pomyślne żądania|Liczba|Łącznie|Pomyślne żądania dla witryny Microsoft.EventHub.|Operationresult|
|Serwery serwerowe|Błędy serwera.|Liczba|Łącznie|Błędy serwera dla witryny Microsoft.EventHub.|Operationresult|
|UserErrors (UżytkownikErrors)|Błędy użytkownika.|Liczba|Łącznie|Błędy użytkownika dla witryny Microsoft.EventHub.|Operationresult|
|QuotaExceedErrors|Przekroczone błędy przydziału.|Liczba|Łącznie|Przekroczono limit przydziału dla witryny Microsoft.EventHub.|Operationresult|
|ThrottledRequests|Ograniczone żądania.|Liczba|Łącznie|Ograniczone żądania dla witryny Microsoft.EventHub.|Operationresult|
|Przychodzącerepy|Żądania przychodzące|Liczba|Łącznie|Przychodzące żądania dla witryny Microsoft.EventHub.|Brak|
|Przychodzącenaśniacje|Wiadomości przychodzące|Liczba|Łącznie|Przychodzące wiadomości dla witryny Microsoft.EventHub.|Brak|
|Wychodzące Wiadomości|Wiadomości wychodzące|Liczba|Łącznie|Wiadomości wychodzące dla witryny Microsoft.EventHub.|Brak|
|Przychodzących bajtów|Bajty przychodzące.|Bajty|Łącznie|Bajty przychodzące dla witryny Microsoft.EventHub.|Brak|
|Bajty wychodzące|Bajty wychodzące.|Bajty|Łącznie|Bajty wychodzące dla witryny Microsoft.EventHub.|Brak|
|ActiveConnections (Aktywne połączenia)|ActiveConnections (Aktywne połączenia)|Liczba|Średnia|Łączna liczba aktywnych połączeń dla witryny Microsoft.EventHub.|Brak|
|ConnectionsOtwarte|Połączenia otwarte.|Liczba|Średnia|Połączenia otwarte dla witryny Microsoft.EventHub.|Brak|
|Połączenia Zamknięte|Połączenia zamknięte.|Liczba|Średnia|Połączenia zamknięte dla witryny Microsoft.EventHub.|Brak|
|Dziennik CaptureBacklog|Przechwytywanie zaległości.|Liczba|Łącznie|Przechwytywanie zaległości dla microsoft.eventhub.|Brak|
|Przechwyconewydacje|Przechwycone wiadomości.|Liczba|Łącznie|Przechwycone wiadomości dla witryny Microsoft.EventHub.|Brak|
|Przechwycone bajty|Przechwycone bajty.|Bajty|Łącznie|Przechwycone bajty dla witryny Microsoft.EventHub.|Brak|
|Procesor CPU|Procesor CPU|Wartość procentowa|Maksimum|Wykorzystanie procesora CPU dla klastra centrum zdarzeń jako wartość procentowa|Rola|
|DostępneMemory|Dostępna pamięć|Wartość procentowa|Maksimum|Dostępna pamięć dla klastra Centrum zdarzeń jako procent pamięci całkowitej.|Rola|
|Rozmiar|Rozmiar eventhubu w bajtach.|Bajty|Średnia|Rozmiar eventhubu w bajtach.|Rola|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/klastry

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Prośby o bramę|Żądania bramy|Liczba|Łącznie|Liczba żądań bramy|Stan http|
|CategorizedGatewayZastynia|Kategoryzowane żądania bramy|Liczba|Łącznie|Liczba żądań bramy według kategorii (1xx/2xx/3xx/4xx/5xx)|Stan http|
|Liczba aktywnych pracowników|Liczba aktywnych pracowników|Liczba|Maksimum|Liczba aktywnych pracowników|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/Skalowanie automatyczneStawienia

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Wartość ObservedMetricValue|Obserwowana wartość metryczna|Liczba|Średnia|Wartość obliczona przez skalowanie automatyczne podczas wykonywania|MetricTriggerSource (Źródło metryki)|
|MetricThreshold (MetricThreshold)|Próg metryczny|Liczba|Średnia|Skonfigurowany próg skalowania automatycznego po uruchomieniu skalowania automatycznego.|MetricTriggerRule (Reguła metrictriggera)|
|ObservedCapacity (Zdolność obserwowana)|Obserwowana pojemność|Liczba|Średnia|Pojemność zgłoszona do skalowania automatycznego podczas wykonywania.|Brak|
|ScaleActionsInitiated|Zainicjowano akcje skalowania|Liczba|Łącznie|Kierunek operacji skalowania.|SkalaKierunkowa|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Składniki

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|dostępnośćWyniki/dostępnośćPercentage|Dostępność|Wartość procentowa|Średnia|Procent pomyślnie zakończonych testów dostępności|availabilityResult/name,availabilityResult/location|
|dostępnośćWyniki/liczba|Testy dostępności|Liczba|Liczba|Liczba testów dostępności|availabilityResult/name,availabilityResult/location,availabilityResult/success|
|dostępnośćWyniki/czas trwania|Czas trwania testu dostępności|Milisekund|Średnia|Czas trwania testu dostępności|availabilityResult/name,availabilityResult/location,availabilityResult/success|
|przeglądarkaTimings/networkDuration|Czas ładowania strony sieciowej|Milisekund|Średnia|Czas między żądaniem użytkownika a połączeniem sieciowym. Zawiera wyszukiwanie DNS i połączenie transportowe.|Brak|
|przeglądarkaTimings/processingDuration|Czas przetwarzania klienta|Milisekund|Średnia|Czas między odebraniem ostatniego bajtu dokumentu do załadowania modelu DOM. Żądania asynchronii mogą nadal być przetwarzane.|Brak|
|przeglądarkaTimings/receiveDuration|Odbieranie czasu odpowiedzi|Milisekund|Średnia|Czas między pierwszym i ostatnim bajtami lub do rozłączenia.|Brak|
|przeglądarkaTimings/sendDuration|Wyślij czas żądania|Milisekund|Średnia|Czas między połączeniem sieciowym a odebraniem pierwszego bajtu.|Brak|
|przeglądarkaTimings/totalDuration|Czas ładowania strony przeglądarki|Milisekund|Średnia|Czas od żądania użytkownika do DOM, arkusze stylów, skrypty i obrazy są ładowane.|Brak|
|zależności/liczba|Wywołania zależności|Liczba|Liczba|Liczba wywołań wykonanych przez aplikację do zasobów zewnętrznych.|zależność/typ,zależność/wydajnośćBucket,zależność/sukces,zależność/cel,zależność/wynikKod,operacja/syntetyczny,chmura/rolaInstacja,chmura/roleName|
|zależności/czas trwania|Czas trwania zależności|Milisekund|Średnia|Czas trwania wywołań składanych przez aplikację do zasobów zewnętrznych.|zależność/typ,zależność/wydajnośćBucket,zależność/sukces,zależność/cel,zależność/wynikKod,operacja/syntetyczny,chmura/rolaInstacja,chmura/roleName|
|zależności/nie powiodło się|Niepowodzenia wywołania zależności|Liczba|Liczba|Liczba nieudanych wywołań zależności wykonanych przez aplikację do zasobów zewnętrznych.|zależność/typ,zależność/wydajnośćBucket,zależność/sukces,zależność/cel,zależność/wynikKod,operacja/syntetyczny,chmura/rolaInstacja,chmura/roleName|
|odsłony/liczba|Wyświetlenia strony|Liczba|Liczba|Liczba wyświetleń strony.|operacja/syntetyczna,chmura/nazwa roli|
|odsłon/czasu trwania strony|Czas ładowania widoku strony|Milisekund|Średnia|Czas ładowania widoku strony|operacja/syntetyczna,chmura/nazwa roli|
|performanceCounters/requestExecutionTime|Czas wykonania żądania HTTP|Milisekund|Średnia|Czas wykonania ostatniego żądania.|chmura/rolaInstance|
|performanceCounters/requestsInQueue|Żądania HTTP w kolejce aplikacji|Liczba|Średnia|Długość kolejki żądań aplikacji.|chmura/rolaInstance|
|performanceCounters/requestsPerSekund|Stawka żądania HTTP|Połów połówek|Średnia|Szybkość wszystkich żądań do aplikacji na sekundę z ASP.NET.|chmura/rolaInstance|
|performanceCounters/exceptionsPerSekund|Współczynnik wyjątków|Połów połówek|Średnia|Liczba obsługiwanych i nieobsługiwał wyjątków zgłoszonych do okien, w tym wyjątków .NET i niezarządzanych wyjątków, które są konwertowane na wyjątki .NET.|chmura/rolaInstance|
|performanceCounters/processIOBytesPerSekund|Szybkość we/wy procesu|PrzeztówPerSekunda|Średnia|Całkowita liczba bajtów na sekundę odczytu i zapisania w plikach, sieci i urządzeniach.|chmura/rolaInstance|
|performanceCounters/processCpuPercentage|Przetwarzanie procesora|Wartość procentowa|Średnia|Procent czasu, który upłynął, że wszystkie wątki procesu używane procesora do wykonywania instrukcji. Może to wynosić od 0 do 100. Ta metryka wskazuje wydajność samego procesu w3wp.|chmura/rolaInstance|
|performanceCounters/processorCpuPercentage|Czas procesora|Wartość procentowa|Średnia|Procent czasu, który procesor spędza w wątkach nie bezczynnych.|chmura/rolaInstance|
|performanceCounters/memoryDostępne bajty|Dostępna pamięć|Bajty|Średnia|Pamięć fizyczna natychmiast dostępna do alokacji do procesu lub do użytku systemowego.|chmura/rolaInstance|
|performanceCounters/processPrivateBytes|Przetwarzanie bajtów prywatnych|Bajty|Średnia|Pamięć przypisana wyłącznie do procesów monitorowanych aplikacji.|chmura/rolaInstance|
|żądania/czas trwania|Czas odpowiedzi serwera|Milisekund|Średnia|Czas między odebraniem żądania HTTP a zakończeniem wysyłania odpowiedzi.|żądanie/wydajnośćBucket,żądanie/wynikKod,operacja/syntetyczny,chmura/rolaInstancja,żądanie/sukces,chmura/roleName|
|żądania/liczba|Żądania serwera|Liczba|Liczba|Liczba ukończonych żądań HTTP.|żądanie/wydajnośćBucket,żądanie/wynikKod,operacja/syntetyczny,chmura/rolaInstancja,żądanie/sukces,chmura/roleName|
|żądania/niepowodzenie|Żądania zakończone niepowodzeniem|Liczba|Liczba|Liczba żądań HTTP oznaczonych jako nieudane. W większości przypadków są to żądania z kodem odpowiedzi >= 400 i nie równa 401.|żądanie/wydajnośćBucket,żądanie/wynikKod,żądanie/sukces,operacja/syntetyczny,chmura/rolaInstacja,chmura/roleName|
|żądania/stawka|Szybkość żądań serwera|Połów połówek|Średnia|Szybkość żądań serwera na sekundę|żądanie/wydajnośćBucket,żądanie/wynikKod,operacja/syntetyczny,chmura/rolaInstancja,żądanie/sukces,chmura/roleName|
|wyjątki/liczba|Wyjątki|Liczba|Liczba|Łączna liczba wszystkich nieprzechoczonych wyjątków.|chmura/nazwa roli,chmura/rolaInstance,klient/typ|
|wyjątki/przeglądarka|Wyjątki przeglądarki|Liczba|Liczba|Liczba nieprzechoczonych wyjątków zgłaszanych w przeglądarce.|klient/isServer,chmura/nazwa roli|
|wyjątki/serwer|Wyjątki serwera|Liczba|Liczba|Liczba nieprzechoczonych wyjątków zgłaszanych w aplikacji serwera.|klient/isServer,chmura/roleName,chmura/rolaInstance|
|ślady/liczba|Ślady|Liczba|Liczba|Śledzenie liczby dokumentów|śledzenie/severityPoziomowanie,operacja/syntetyczne,chmura/rolaname,chmura/rolaInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft.IoTCentral/IoTApps

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|connectedDeviceCount|Całkowita liczba podłączonych urządzeń|Liczba|Średnia|Liczba urządzeń podłączonych do IoT Central|Brak|
|c2d.property.read.success|Pomyślne odczyty właściwości urządzenia z usługi IoT Central|Liczba|Łącznie|Liczba wszystkich pomyślnych odczytów właściwości zainicjowanych z usługi IoT Central|Brak|
|c2d.property.read.failure|Odczyty właściwości urządzenia nie powiodło się z centrum IoT|Liczba|Łącznie|Liczba wszystkich odczytów właściwości nie powiodło się zainicjowanych z usługi IoT Central|Brak|
|d2c.property.read.success|Pomyślne odczyty właściwości urządzenia z urządzeń|Liczba|Łącznie|Liczba wszystkich pomyślnych odczytów właściwości zainicjowanych z urządzeń|Brak|
|d2c.property.read.failure|Odczyt właściwości urządzenia nie powiodło się z urządzeń|Liczba|Łącznie|Liczba wszystkich odczytów właściwości nie powiodło się zainicjowane z urządzeń|Brak|
|c2d.property.update.success|Pomyślne aktualizacje właściwości urządzenia z usługi IoT Central|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji właściwości zainicjowanych z usługi IoT Central|Brak|
|c2d.property.update.failure|Aktualizacje właściwości urządzenia nie powiodło się z usługi IoT Central|Liczba|Łącznie|Liczba wszystkich aktualizacji właściwości nie powiodło się zainicjowanych z usługi IoT Central|Brak|
|d2c.property.update.success|Pomyślne aktualizacje właściwości urządzenia z urządzeń|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji właściwości zainicjowanych z urządzeń|Brak|
|d2c.property.update.failure|Aktualizacje właściwości urządzenia nie powiodło się z urządzeń|Liczba|Łącznie|Liczba wszystkich aktualizacji właściwości nie powiodło się zainicjowanych z urządzeń|Brak|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/przechowalnia

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SerwisApiHit|Całkowita liczba trafień interfejsu API usługi|Liczba|Liczba|Liczba całkowitych trafień api usługi|Typ działania,Nazwa działania|
|ServiceApiLatency|Ogólne opóźnienie interfejsu api usługi|Milisekund|Średnia|Ogólne opóźnienie żądań interfejsu API usługi|ActivityType,ActivityName,StatusCode,StatusCodeClass|
|ServiceApiResult (ServiceApiResult)|Wyniki interfejsu API usługi razem|Liczba|Liczba|Liczba całkowitych wyników api usługi|ActivityType,ActivityName,StatusCode,StatusCodeClass|
|NasycenieShoebox|Ogólne nasycenie skarbca|Wartość procentowa|Średnia|Wykorzystana pojemność przechowalni|Typ działania,Nazwa działania,Typ transakcji|
|Dostępność|Ogólna dostępność magazynu|Wartość procentowa|Średnia|Dostępność żądań vault|ActivityType,ActivityName,StatusCode,StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/klastry

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Buforowanie alokacji|Wykorzystanie pamięci podręcznej|Wartość procentowa|Średnia|Poziom wykorzystania w zakresie klastra|Brak|
|Wyszukiwanie zapytań|Czas trwania kwerendy|Milisekund|Średnia|Czas trwania zapytań w sekundach|Querystatus|
|Połknieniauutilization|Wykorzystanie spożycia|Wartość procentowa|Średnia|Stosunek używanych gniazd połkowych w klastrze|Brak|
|Keepalive|Utrzymać przy życiu|Liczba|Średnia|Sprawdzanie poczytalności wskazuje, że klaster odpowiada na zapytania|Brak|
|PołkliwienieVolumeInMB|Głośność pozyskiwania (w MB)|Liczba|Łącznie|Całkowita ilość przyjmowanych danych do klastra (w MB)|baza danych|
|PołkwianieLatencyInSekundy|Opóźnienie połknięcia (w sekundach)|Sekundy|Średnia|Czas pozyskiwania ze źródła (np. komunikat znajduje się w uliczce EventHub) do klastra w sekundach|Brak|
|WydarzeniaProcessedForEventHubs|Przetwarzane zdarzenia (dla centrów zdarzeń/IoT)|Liczba|Łącznie|Liczba zdarzeń przetwarzanych przez klaster podczas pozyskiwania z usługi Event/IoT Hub|EventStatus|
|PołknienieWyskuj|Wynik spożycia|Liczba|Liczba|Liczba operacji pozyskiwania|PołknięciaWyskłości szczegółowe|
|Procesor CPU|Procesor CPU|Wartość procentowa|Średnia|Poziom wykorzystania procesora|Brak|
|ContinuousExportNumOfRecordsExported|Ciągły eksport – liczba eksportowanych rekordów|Liczba|Łącznie|Liczba wyeksportowanych rekordów, wystrzelonych dla każdego artefaktu magazynu zapisanego podczas operacji eksportowania|Ciągła nazwa ekspedycjowa,Baza danych|
|Eksportutilization|Wykorzystanie eksportu|Wartość procentowa|Maksimum|Wykorzystanie eksportu|Brak|
|Ciągła liczba ekspportów|Ciągła liczba oczekujących na eksport|Liczba|Maksimum|Liczba oczekujących zadań ciągłego eksportu gotowych do wykonania|Brak|
|ContinuousExportMaxLatenessMinutes|Ciągły eksport Max Lateness|Liczba|Maksimum|Opóźnienie (w minutach) zgłoszone przez ciągłe zadania eksportowe w klastrze|Brak|
|CiągłyWydajnik|Ciągły wynik eksportu|Liczba|Liczba|Wskazuje, czy eksport ciągły zakończył się pomyślnie, czy nie powiodło się|Ciągłanana nazwa ekspedycjowa,Wynik,Baza danych|
|StreamingOwanieDuration|Czas pozyskiwania przesyłania strumieniowego|Milisekund|Średnia|Czas pozyskiwania przesyłania strumieniowego w milisekundach|Brak|
|Szybkość pozyskiwania danych w strumieniu strumieniowym|Szybkość pozyskiwania danych przesyłania strumieniowego|Liczba|Średnia|Szybkość przesyłania strumieniowego danych pozyskiwania (MB na sekundę)|Brak|
|SteamingIngestRequestRate (SteamingIngestRequestRate)|Szybkość pozyskiwania przesyłania strumieniowego|Liczba|RateRequestsPerSecond|Szybkość pozyskiwania przesyłania strumieniowego (żądania na sekundę)|Brak|
|Wyniki pozyskiwania strumieniowego|Streaming Ingest Wynik|Liczba|Średnia|Wynik pozyskiwania przesyłania strumieniowego|Wynik|
|TotalNumberOfConcurrentQueries|Całkowita liczba równoczesnych zapytań|Liczba|Łącznie|Całkowita liczba równoczesnych zapytań|Brak|
|TotalNumberOfThrottledQueries (Liczba NumerOfThrottledQueries)|Całkowita liczba kwerend z ograniczeniami|Liczba|Łącznie|Całkowita liczba kwerend z ograniczeniami|Brak|
|TotalNumberOfThrottledCommands|Całkowita liczba poleceń z ograniczeniem|Liczba|Łącznie|Całkowita liczba poleceń z ograniczeniem|Commandtype|
|TotalNumberOfExtents|Łączna liczba zakresów|Liczba|Łącznie|Łączna liczba zakresów danych|Brak|
|Liczba wystąpień|Liczba wystąpień|Liczba|Średnia|Całkowita liczba wystąpień|Brak|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/przepływy pracy

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|UruchamianieRozpoczęło się|Uruchomiono|Liczba|Łącznie|Liczba uruchomionych uruchomień przepływu pracy.|Brak|
|RunsCompleted (Wykończone trasy)|Ukończono przebiegi|Liczba|Łącznie|Liczba ukończonych przebiegów przepływu pracy.|Brak|
|BiegiSucceededed|Przebiegi powiodły się|Liczba|Łącznie|Liczba przebiegów przepływu pracy powiodła się.|Brak|
|DziałaFailed|Przebiegi nie powiodły się|Liczba|Łącznie|Liczba uruchomień przepływu pracy nie powiodła się.|Brak|
|DziałaCancelled|Przebiegi anulowane|Liczba|Łącznie|Liczba anulowanych uruchomień przepływu pracy.|Brak|
|RunLatency (RunLatency)|Opóźnienie uruchamiania|Sekundy|Średnia|Opóźnienie zakończonych przebiegów przepływu pracy.|Brak|
|RunSuccessLatency|Uruchamianie opóźnienia sukcesu|Sekundy|Średnia|Opóźnienie pomyślnych przebiegów przepływu pracy.|Brak|
|RunThrottledWydarzenia|Uruchamianie zdarzeń z ograniczeniem|Liczba|Łącznie|Liczba akcji przepływu pracy lub wyzwalacza zdarzeń z ograniczeniem.|Brak|
|RunStartThrottledWydarzenia|Uruchamianie zdarzeń z ograniczeniem startu|Liczba|Łącznie|Liczba uruchomionych zdarzeń uruchamiania przepływu pracy.|Brak|
|RunFailurePercentage|Procent niepowodzenia uruchomienia|Wartość procentowa|Łącznie|Procent przebiegów przepływu pracy nie powiodło się.|Brak|
|AkcjeStartowane|Rozpoczęto akcje |Liczba|Łącznie|Liczba uruchomionych akcji przepływu pracy.|Brak|
|AkcjeUpełnione|Akcje zakończone |Liczba|Łącznie|Liczba zakończonych akcji przepływu pracy.|Brak|
|DziałaniaSucceeded|Akcje zakończyły się pomyślnie |Liczba|Łącznie|Liczba akcji przepływu pracy powiodła się.|Brak|
|AkcjeNiewiększone|Akcje nie powiodły się |Liczba|Łącznie|Liczba akcji przepływu pracy nie powiodła się.|Brak|
|AkcjeWysłane|Pominięte akcje |Liczba|Łącznie|Liczba pominiętych akcji przepływu pracy.|Brak|
|ActionLatency (Włastwerwość|Opóźnienie akcji |Sekundy|Średnia|Opóźnienie zakończonych akcji przepływu pracy.|Brak|
|DziałanieSukceslatywa|Opóźnienie sukcesu akcji |Sekundy|Średnia|Opóźnienie akcji przepływu pracy, które zakończyły się pomyślnie.|Brak|
|AkcjaTrocesy|Zdarzenia ograniczone do akcji|Liczba|Łącznie|Liczba zdarzeń ograniczonej akcją przepływu pracy..|Brak|
|WyzwalaczeStarted|Uruchomiono wyzwalacze |Liczba|Łącznie|Liczba uruchomionych wyzwalaczy przepływu pracy.|Brak|
|WyzwalaczeUkoletowane|Ukończono wyzwalacze |Liczba|Łącznie|Liczba ukończonych wyzwalaczy przepływu pracy.|Brak|
|TriggersSucceeded|Wyzwalacze powiodły się |Liczba|Łącznie|Liczba wyzwalaczy przepływu pracy powiodła się.|Brak|
|WyzwalaczeNieNie niesie zezonych|Wyzwalacze nie powiodły się |Liczba|Łącznie|Liczba wyzwalaczy przepływu pracy nie powiodła się.|Brak|
|WyzwalaczeSsekpped|Pominięte wyzwalacze|Liczba|Łącznie|Liczba pominiętych wyzwalaczy przepływu pracy.|Brak|
|WyzwalaczePalione|Wyzwalacze opalane |Liczba|Łącznie|Liczba wyzwalaczy przepływu pracy.|Brak|
|TriggerLatency (TriggerLatency)|Opóźnienie wyzwalacza |Sekundy|Średnia|Opóźnienie ukończonych wyzwalaczy przepływu pracy.|Brak|
|TriggerFireLatency (TriggerFireLatency)|Opóźnienie pożaru wyzwalacza |Sekundy|Średnia|Opóźnienie wyzwalaczy wyzwalaczy przepływu pracy.|Brak|
|TriggerSuccessLatency|Opóźnienie sukcesu wyzwalacza |Sekundy|Średnia|Opóźnienie pomyślnych wyzwalaczy przepływu pracy.|Brak|
|TriggerThrottledWydarzenia|Wyzwalacz ograniczone zdarzenia|Liczba|Łącznie|Liczba zdarzeń z ograniczeniem wyzwalacza przepływu pracy.|Brak|
|BillableActionWykonań|Egzekucje akcji podlegające rozliczaniu|Liczba|Łącznie|Liczba wykonań akcji przepływu pracy, które są rozliczane.|Brak|
|BillableTriggerWykonania|Egzekucje wyzwalaczy podlegające rozliczani|Liczba|Łącznie|Liczba wykonań wyzwalaczy przepływu pracy, które są rozliczane.|Brak|
|TotalBillableWykonań|Łączna liczba wykonywalnych wykonań|Liczba|Łącznie|Liczba wykonań przepływu pracy rozliczanych.|Brak|
|RozliczeniaUsageNativeOperation|Użycie rozliczeń dla natywnych wykonań operacji|Liczba|Łącznie|Liczba natywnych wykonań operacji, które są rozliczane.|Brak|
|RozliczenieUsageStandardconnector|Użycie rozliczeń dla wykonania łącznika standardowego|Liczba|Łącznie|Liczba standardowych wykonań łącznika, które są rozliczane.|Brak|
|RozliczeniaUsageStorageKonsumpcja|Użycie rozliczeń dla wykonań zużycia magazynu|Liczba|Łącznie|Liczba wykonań zużycia magazynu, które są rozliczane.|Brak|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|UruchamianieRozpoczęło się|Uruchomiono|Liczba|Łącznie|Liczba uruchomionych uruchomień przepływu pracy.|Brak|
|RunsCompleted (Wykończone trasy)|Ukończono przebiegi|Liczba|Łącznie|Liczba ukończonych przebiegów przepływu pracy.|Brak|
|BiegiSucceededed|Przebiegi powiodły się|Liczba|Łącznie|Liczba przebiegów przepływu pracy powiodła się.|Brak|
|DziałaFailed|Przebiegi nie powiodły się|Liczba|Łącznie|Liczba uruchomień przepływu pracy nie powiodła się.|Brak|
|DziałaCancelled|Przebiegi anulowane|Liczba|Łącznie|Liczba anulowanych uruchomień przepływu pracy.|Brak|
|RunLatency (RunLatency)|Opóźnienie uruchamiania|Sekundy|Średnia|Opóźnienie zakończonych przebiegów przepływu pracy.|Brak|
|RunSuccessLatency|Uruchamianie opóźnienia sukcesu|Sekundy|Średnia|Opóźnienie pomyślnych przebiegów przepływu pracy.|Brak|
|RunThrottledWydarzenia|Uruchamianie zdarzeń z ograniczeniem|Liczba|Łącznie|Liczba akcji przepływu pracy lub wyzwalacza zdarzeń z ograniczeniem.|Brak|
|RunStartThrottledWydarzenia|Uruchamianie zdarzeń z ograniczeniem startu|Liczba|Łącznie|Liczba uruchomionych zdarzeń uruchamiania przepływu pracy.|Brak|
|RunFailurePercentage|Procent niepowodzenia uruchomienia|Wartość procentowa|Łącznie|Procent przebiegów przepływu pracy nie powiodło się.|Brak|
|AkcjeStartowane|Rozpoczęto akcje |Liczba|Łącznie|Liczba uruchomionych akcji przepływu pracy.|Brak|
|AkcjeUpełnione|Akcje zakończone |Liczba|Łącznie|Liczba zakończonych akcji przepływu pracy.|Brak|
|DziałaniaSucceeded|Akcje zakończyły się pomyślnie |Liczba|Łącznie|Liczba akcji przepływu pracy powiodła się.|Brak|
|AkcjeNiewiększone|Akcje nie powiodły się |Liczba|Łącznie|Liczba akcji przepływu pracy nie powiodła się.|Brak|
|AkcjeWysłane|Pominięte akcje |Liczba|Łącznie|Liczba pominiętych akcji przepływu pracy.|Brak|
|ActionLatency (Włastwerwość|Opóźnienie akcji |Sekundy|Średnia|Opóźnienie zakończonych akcji przepływu pracy.|Brak|
|DziałanieSukceslatywa|Opóźnienie sukcesu akcji |Sekundy|Średnia|Opóźnienie akcji przepływu pracy, które zakończyły się pomyślnie.|Brak|
|AkcjaTrocesy|Zdarzenia ograniczone do akcji|Liczba|Łącznie|Liczba zdarzeń ograniczonej akcją przepływu pracy..|Brak|
|WyzwalaczeStarted|Uruchomiono wyzwalacze |Liczba|Łącznie|Liczba uruchomionych wyzwalaczy przepływu pracy.|Brak|
|WyzwalaczeUkoletowane|Ukończono wyzwalacze |Liczba|Łącznie|Liczba ukończonych wyzwalaczy przepływu pracy.|Brak|
|TriggersSucceeded|Wyzwalacze powiodły się |Liczba|Łącznie|Liczba wyzwalaczy przepływu pracy powiodła się.|Brak|
|WyzwalaczeNieNie niesie zezonych|Wyzwalacze nie powiodły się |Liczba|Łącznie|Liczba wyzwalaczy przepływu pracy nie powiodła się.|Brak|
|WyzwalaczeSsekpped|Pominięte wyzwalacze|Liczba|Łącznie|Liczba pominiętych wyzwalaczy przepływu pracy.|Brak|
|WyzwalaczePalione|Wyzwalacze opalane |Liczba|Łącznie|Liczba wyzwalaczy przepływu pracy.|Brak|
|TriggerLatency (TriggerLatency)|Opóźnienie wyzwalacza |Sekundy|Średnia|Opóźnienie ukończonych wyzwalaczy przepływu pracy.|Brak|
|TriggerFireLatency (TriggerFireLatency)|Opóźnienie pożaru wyzwalacza |Sekundy|Średnia|Opóźnienie wyzwalaczy wyzwalaczy przepływu pracy.|Brak|
|TriggerSuccessLatency|Opóźnienie sukcesu wyzwalacza |Sekundy|Średnia|Opóźnienie pomyślnych wyzwalaczy przepływu pracy.|Brak|
|TriggerThrottledWydarzenia|Wyzwalacz ograniczone zdarzenia|Liczba|Łącznie|Liczba zdarzeń z ograniczeniem wyzwalacza przepływu pracy.|Brak|
|IntegrationServiceEnvironmentPracflowProcessorUsage|Użycie procesora przepływu pracy w środowisku usługi integracji|Wartość procentowa|Średnia|Użycie procesora przepływu pracy dla środowiska usługi integracji.|Brak|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Użycie pamięci przepływu pracy dla środowiska usługi integracji|Wartość procentowa|Średnia|Użycie pamięci przepływu pracy dla środowiska usługi integracji.|Brak|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Użycie procesora łącznika w środowisku usługi integracji|Wartość procentowa|Średnia|Użycie procesora łącznika dla środowiska usługi integracji.|Brak|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Użycie pamięci łącznika dla środowiska usługi integracji|Wartość procentowa|Średnia|Użycie pamięci łącznika dla środowiska usługi integracji.|Brak|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningUsługi/obszary robocze

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Anulowane przebiegi|Anulowane przebiegi|Liczba|Łącznie|Liczba uruchomień anulowanych dla tego obszaru roboczego|Scenariusz,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Anuluj żądane przebiegi|Anuluj żądane przebiegi|Liczba|Łącznie|Liczba przebiegów, w których zażądano anulowania dla tego obszaru roboczego|Scenariusz,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Ukończone przebiegi|Ukończone przebiegi|Liczba|Łącznie|Liczba przebiegów zakończonych pomyślnie dla tego obszaru roboczego|Scenariusz,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Nieudane przebiegi|Nieudane przebiegi|Liczba|Łącznie|Liczba uruchomień nie powiodła się dla tego obszaru roboczego|Scenariusz,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Finalizowanie przebiegów|Finalizowanie przebiegów|Liczba|Łącznie|Liczba uruchomień wprowadzonych w stanie finalizacji dla tego obszaru roboczego|Scenariusz,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Nie odpowiada uruchamia|Nie odpowiada uruchamia|Liczba|Łącznie|Liczba uruchomień, które nie odpowiadają dla tego obszaru roboczego|Scenariusz,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Nie rozpoczęto uruchomień|Nie rozpoczęto uruchomień|Liczba|Łącznie|Liczba uruchomień w stanie nieu uruchomiony dla tego obszaru roboczego|Scenariusz,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Przygotowanie przebiegów|Przygotowanie przebiegów|Liczba|Łącznie|Liczba uruchomień przygotowujących się do tego obszaru roboczego|Scenariusz,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Przebiegi inicjowania obsługi administracyjnej|Przebiegi inicjowania obsługi administracyjnej|Liczba|Łącznie|Liczba uruchomień, które są inicjowania obsługi administracyjnej dla tego obszaru roboczego|Scenariusz,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Przebiegi w kolejce|Przebiegi w kolejce|Liczba|Łącznie|Liczba uruchomień, które są umieszczane w kolejce dla tego obszaru roboczego|Scenariusz,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Rozpoczęte biegi|Rozpoczęte biegi|Liczba|Łącznie|Liczba uruchomień rozpoczętych dla tego obszaru roboczego|Scenariusz,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Uruchamianie przebiegów|Uruchamianie przebiegów|Liczba|Łącznie|Liczba uruchomień rozpoczętych dla tego obszaru roboczego|Scenariusz,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Errors|Errors|Liczba|Łącznie|Liczba błędów uruchamiania w tym obszarze roboczym|Scenariusz|
|Ostrzeżenia|Ostrzeżenia|Liczba|Łącznie|Liczba ostrzeżeń o przebiegu w tym obszarze roboczym|Scenariusz|
|Rejestr modelu zakończył się pomyślnie|Rejestr modelu zakończył się pomyślnie|Liczba|Łącznie|Liczba rejestracji modelu, które powiodły się w tym obszarze roboczym|Scenariusz|
|Rejestr modelu nie powiódł się|Rejestr modelu nie powiódł się|Liczba|Łącznie|Liczba rejestracji modelu, które nie powiodły się w tym obszarze roboczym|Scenariusz,Kod stanu|
|Rozpoczęto wdrażanie modelu|Rozpoczęto wdrażanie modelu|Liczba|Łącznie|Liczba wdrożeń modelu rozpoczętych w tym obszarze roboczym|Scenariusz|
|Wdrożenie modelu powiodło się|Wdrożenie modelu powiodło się|Liczba|Łącznie|Liczba wdrożeń modelu, które powiodły się w tym obszarze roboczym|Scenariusz|
|Wdrażanie modelu nie powiodło się|Wdrażanie modelu nie powiodło się|Liczba|Łącznie|Liczba wdrożeń modelu, które nie powiodły się w tym obszarze roboczym|Scenariusz,Kod stanu|
|Łączna liczba węzłów|Łączna liczba węzłów|Liczba|Średnia|Liczba węzłów ogółem. Suma ta obejmuje niektóre aktywne węzły, węzły bezczynności, węzły bezużyteczne, wstępnie wypoprawione węzły, wychodzące węzły|Scenariusz,Nazwa klastra|
|Aktywne węzły|Aktywne węzły|Liczba|Średnia|Liczba węzłów Acitve. Są to węzły, które aktywnie uruchamiają zadanie.|Scenariusz,Nazwa klastra|
|Węzły bezczynne|Węzły bezczynne|Liczba|Średnia|Liczba bezczynnych węzłów. Bezczynne węzły są węzłami, które nie są uruchomione żadne zadania, ale można zaakceptować nowe zadanie, jeśli są dostępne.|Scenariusz,Nazwa klastra|
|Niezniszalne węzły|Niezniszalne węzły|Liczba|Średnia|Liczba węzłów bezużytecznych. Nieużywalne węzły nie są funkcjonalne z powodu pewnego problemu nierozwiązywalne. Platforma Azure będzie przetwarzać te węzły.|Scenariusz,Nazwa klastra|
|Wstępnie wywłaszczone węzły|Wstępnie wywłaszczone węzły|Liczba|Średnia|Liczba wywłaszczonych węzłów. Te węzły są węzły o niskim priorytecie, które są zabierane z puli dostępnych węzłów.|Scenariusz,Nazwa klastra|
|Opuszczanie węzłów|Opuszczanie węzłów|Liczba|Średnia|Liczba węzłów opuszczających. Pozostawiając węzły są węzły, które właśnie zakończył przetwarzania zadania i przejdzie do stanu bezczynnego.|Scenariusz,Nazwa klastra|
|Całkowita liczba rdzeni|Całkowita liczba rdzeni|Liczba|Średnia|Liczba całkowitej liczby rdzeni|Scenariusz,Nazwa klastra|
|Aktywne rdzenie|Aktywne rdzenie|Liczba|Średnia|Liczba aktywnych rdzeni|Scenariusz,Nazwa klastra|
|Rdzenie bezczynne|Rdzenie bezczynne|Liczba|Średnia|Liczba bezczynnych rdzeni|Scenariusz,Nazwa klastra|
|Niezniszalne rdzenie|Niezniszalne rdzenie|Liczba|Średnia|Liczba niezniszalnych rdzeni|Scenariusz,Nazwa klastra|
|Wywłaszczone rdzenie|Wywłaszczone rdzenie|Liczba|Średnia|Liczba wywłaszczonych rdzeni|Scenariusz,Nazwa klastra|
|Opuszczanie rdzeni|Opuszczanie rdzeni|Liczba|Średnia|Liczba rdzeni opuszczających|Scenariusz,Nazwa klastra|
|Procent wykorzystania przydziału|Procent wykorzystania przydziału|Liczba|Średnia|Procent wykorzystanego przydziału|Scenariusz,Nazwa klastra,Nazwa wirtualna,VmPriority|
|Utilizacja procesora|Utilizacja procesora|Liczba|Średnia|Procesor (wersja zapoznawcza)|Scenariusz,runId,Identyfikator węzła,CreatedTime|
|GpuUtilization|GpuUtilization|Liczba|Średnia|GPU (wersja zapoznawcza)|Scenariusz,runId,Identyfikator węzła,CreatedTime,Identyfikator urządzenia|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts Microsoft.Maps/accounts Microsoft.Maps/accounts Microsoft.

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Sposób użycia|Sposób użycia|Liczba|Liczba|Liczba wywołań interfejsu API|Kategoria api,ApiName,Typ wyniku,Kod odpowiedzi|
|Dostępność|Dostępność|Wartość procentowa|Średnia|Dostępność interfejsów API|Kategoria api,Nazwa api|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingWypunkty końcowe

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych ruchu wychodzącego w bajtach.|Format wyjściowy|
|SuccessE2ELatency|Sukces koniec do końca Latencja|Milisekund|Średnia|Średnie opóźnienie dla pomyślnych żądań w milisekundach.|Format wyjściowy|
|Żądania|Żądania|Liczba|Łącznie|Żądania do punktu końcowego przesyłania strumieniowego.|Dataformat,HttpStatusCode,Kod błędu|


## <a name="microsoftmediamediaservices"></a>Usługi Microsoft.Media/media

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AssetQuota (Quota aktywów)|Przydział środków trwałych|Liczba|Średnia|Ile zasobów jest dozwolonych dla bieżącego konta usługi multimediów|Brak|
|AssetCount (licz assetcount)|Liczba zasobów|Liczba|Średnia|Ile zasobów jest już utworzonych na bieżącym koncie usługi multimediów|Brak|
|AssetQuotaUsedPercentage|Użyta wartość procentowa przydziału zasobów|Wartość procentowa|Średnia|Procent użycia zasobu na bieżącym koncie usługi multimediów|Brak|
|ContentKeyPolicyQuota (ContentKeyPolicyQuota)|Przydział zasad klucza zawartości|Liczba|Średnia|Ile treści kluczowych zasady są dozwolone dla bieżącego konta usługi multimedialnej|Brak|
|ContentKeyPolicyCount (Liczba klawiatur contentkeypolicycount)|Liczba zasad klucza zawartości|Liczba|Średnia|Ile zasad klucza zawartości jest już utworzonych na bieżącym koncie usługi multimedialnej|Brak|
|ContentKeyPolicyQuotaUsedPercentage|Użyto wartości procentowej przydziału zasad klucza zawartości|Wartość procentowa|Średnia|Zasady klucza zawartości używane procentowe na bieżącym koncie usługi multimedialnej|Brak|
|StreamingPolicyQuota|Przydział zasad przesyłania strumieniowego|Liczba|Średnia|Ile zasad przesyłania strumieniowego jest dozwolonych dla bieżącego konta usługi multimediów|Brak|
|StreamingPolicyCount|Liczba zasad przesyłania strumieniowego|Liczba|Średnia|Ile zasad przesyłania strumieniowego jest już utworzonych na bieżącym koncie usługi multimediów|Brak|
|StreamingPolicyQuotaUsedPercentage|Użyty procent przydziału zasad przesyłania strumieniowego|Wartość procentowa|Średnia|Polityka przesyłania strumieniowego używana procent na bieżącym koncie usługi multimedialnej|Brak|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Konta Microsoft.MixedReality/remoteRenderingAccounts

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AktywaPrzekonwertowane|Przekonwertowane zasoby|Liczba|Łącznie|Łączna liczba przeliczonych aktywów|Identyfikator aplikacji,Identyfikator zasobu,SDKVersion|
|ActiveRenderingSessions (Liczba spotkań aktywnych)|Aktywne sesje renderowania|Liczba|Łącznie|Całkowita liczba aktywnych sesji renderowania|Identyfikator aplikacji,Identyfikator zasobu,Typ sesji,SDKVersion|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppKonakony/pojemnośćPuly/woluminy

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ŚredniareadLatency|Średnie opóźnienie odczytu|Milisekund|Średnia|Średnie opóźnienie odczytu w milisekundach na operację|Brak|
|Średnia Średniawłasność|Średnie opóźnienie zapisu|Milisekund|Średnia|Średnie opóźnienie zapisu w milisekundach na operację|Brak|
|Rozmiar volumeLogicalSize|Rozmiar zużytej objętości|Bajty|Średnia|Rozmiar logiczny woluminu (używane bajty)|Brak|
|Rozmiar pliku VolumeSnapshotSize|Rozmiar migawki woluminu|Bajty|Średnia|Rozmiar wszystkich migawek w woluminie|Brak|
|ReadIops (ReadIops)|Przeczytaj iops|Połów połówek|Średnia|Operacje odczytu/wyjścia na sekundę|Brak|
|WriteIops (WriteIops)|Napisz iops|Połów połówek|Średnia|Zapis operacji wpuszczania/wyjścia na sekundę|Brak|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppKonakony/pojemność Pools

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|VolumePoolPrzydzieliłUżywane|Pula przydzielona do rozmiaru woluminu|Bajty|Średnia|Przydzielony używany rozmiar puli|Brak|
|Rozmiar woluminuPoolTotalLogicalSize|Rozmiar zużytego basenu|Bajty|Średnia|Suma logicznego rozmiaru wszystkich tomów należących do puli|Brak|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BytesSentRate (BytesSentRate)|Bajty wysłane|Bajty|Łącznie|Liczba bajtów wysłanych przez interfejs sieciowy|Brak|
|BytesReceivedRate (Właso)|Bajty odebrane|Bajty|Łącznie|Liczba bajtów odebranych przez interfejs sieciowy|Brak|
|PacketsSentRate (Szybkość pakietu)|Wysłane pakiety|Liczba|Łącznie|Liczba pakietów wysłanych przez interfejs sieciowy|Brak|
|PakietyReceivedRate|Odebrane pakiety|Liczba|Łącznie|Liczba pakietów odebranych przez interfejs sieciowy|Brak|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Dostępność Vip|Dostępność ścieżki danych|Liczba|Średnia|Dostępność ścieżki danych średniego modułu równoważenia obciążenia na czas trwania|FrontendIPAddress,FrontendPort|
|DipDostępność|Stan sondy kondycji|Liczba|Średnia|Średnia kondycja modułu równoważenia obciążenia stan na czas trwania|ProtocolType,BackendPort,FrontendIPAddress,FrontendPort,BackendIPAddress|
|Bytecount|Liczba bajtów|Liczba|Łącznie|Całkowita liczba bajtów przesłanych w okresie czasu|FrontendIPAddress,FrontendPort,Kierunek|
|Liczba pakietów|Liczba pakietów|Liczba|Łącznie|Całkowita liczba pakietów przesłanych w okresie czasu|FrontendIPAddress,FrontendPort,Kierunek|
|Konto SYNCount|Liczba SYN|Liczba|Łącznie|Całkowita liczba pakietów SYN przesłanych w okresie czasu|FrontendIPAddress,FrontendPort,Kierunek|
|SnatConnectionCount (Liczba połączeń SnatConnection)|Liczba połączeń SNAT|Liczba|Łącznie|Łączna liczba nowych połączeń SNAT utworzonych w okresie czasu|FrontendIPAddress,BackendIPAddress,ConnectionState|
|AllocatedSnatPorts (Porty AllocatedSnat)|Przydzielone porty SNAT (wersja zapoznawcza)|Liczba|Łącznie|Łączna liczba portów SNAT przydzielonych w okresie czasu|FrontendIPAddress,BackendIPAddress,ProtocolType,IsAwaitingRemoval|
|UżywanePorty sznat|Używane porty SNAT (wersja zapoznawcza)|Liczba|Łącznie|Całkowita liczba portów SNAT używanych w okresie czasu|FrontendIPAddress,BackendIPAddress,ProtocolType,IsAwaitingRemoval|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QueryVolume (Wyw.|Wolumin kwerendy|Liczba|Łącznie|Liczba zapytań obsługiwanych dla strefy DNS|Brak|
|Konto RecordSetCount|Liczba zestawów rekordów|Liczba|Maksimum|Liczba zestawów rekordów w strefie DNS|Brak|
|Ulatnianie recordSetCapacityUtilization|Rekordowe wykorzystanie pojemności zestawu mocy produkcyjnych|Wartość procentowa|Maksimum|Procent pojemności zestawu rekordów wykorzystywanej przez strefę DNS|Brak|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PacketsInDDoS (Usługi wg.|Pakiety przychodzące DDoS|Połów połówek|Maksimum|Pakiety przychodzące DDoS|Brak|
|PakietyDroppedDDoS|Pakiety przychodzące porzucone DDoS|Połów połówek|Maksimum|Pakiety przychodzące porzucone DDoS|Brak|
|PacketsForwardedDDoS|Pakiety przychodzące przesyłane dalej DDoS|Połów połówek|Maksimum|Pakiety przychodzące przesyłane dalej DDoS|Brak|
|Protokół TCPPacketsIndDoS|Przychodzące pakiety TCP DDoS|Połów połówek|Maksimum|Przychodzące pakiety TCP DDoS|Brak|
|Protokół TCPPacketsDroppedDDoS|Przychodzące pakiety TCP porzucone DDoS|Połów połówek|Maksimum|Przychodzące pakiety TCP porzucone DDoS|Brak|
|TCPPacketsForwardedDDoS|Przychodzące pakiety TCP przesyłane dalej DDoS|Połów połówek|Maksimum|Przychodzące pakiety TCP przesyłane dalej DDoS|Brak|
|UDPPacketsIndDoS|Przychodzące pakiety UDP DDoS|Połów połówek|Maksimum|Przychodzące pakiety UDP DDoS|Brak|
|UDPPacketsDroppedDDoS|Przychodzące pakiety UDP porzucone DDoS|Połów połówek|Maksimum|Przychodzące pakiety UDP porzucone DDoS|Brak|
|UDPPacketsForwardedDDoS|Przychodzące pakiety UDP przekazywane ddos|Połów połówek|Maksimum|Przychodzące pakiety UDP przekazywane ddos|Brak|
|BytesIndDoS (BytesInDDoS)|Bajty przychodzące DDoS|PrzeztówPerSekunda|Maksimum|Bajty przychodzące DDoS|Brak|
|BajtyDroppedDDoS|Bajty przychodzące porzucone DDoS|PrzeztówPerSekunda|Maksimum|Bajty przychodzące porzucone DDoS|Brak|
|BajtówForwardedDDoS|Bajty przychodzące przekazywane dalej DDoS|PrzeztówPerSekunda|Maksimum|Bajty przychodzące przekazywane dalej DDoS|Brak|
|Protokół TCPBytesInDDoS|Przychodzące bajty TCP DDoS|PrzeztówPerSekunda|Maksimum|Przychodzące bajty TCP DDoS|Brak|
|Protokół TCPBytesDroppedDDoS|Przychodzące bajty TCP porzucone DDoS|PrzeztówPerSekunda|Maksimum|Przychodzące bajty TCP porzucone DDoS|Brak|
|TCPBytesForwardedDDoS|Przychodzące bajty TCP przekazywane dalej DDoS|PrzeztówPerSekunda|Maksimum|Przychodzące bajty TCP przekazywane dalej DDoS|Brak|
|UDPBytesIndDoS|Przychodzące bajty UDP DDoS|PrzeztówPerSekunda|Maksimum|Przychodzące bajty UDP DDoS|Brak|
|UDPBytesDroppedDDoS|Przychodzące bajty UDP porzucone DDoS|PrzeztówPerSekunda|Maksimum|Przychodzące bajty UDP porzucone DDoS|Brak|
|UDPBytesForwardedDDoS|Przychodzące bajty UDP przekazywane dalej DDoS|PrzeztówPerSekunda|Maksimum|Przychodzące bajty UDP przekazywane dalej DDoS|Brak|
|Atak IfUnderDDoS|W ramach ataku DDoS lub nie|Liczba|Maksimum|W ramach ataku DDoS lub nie|Brak|
|Pakiety DDoSTriggerTCP|Przychodzące pakiety TCP wyzwalające środki ograniczające DDoS|Połów połówek|Maksimum|Przychodzące pakiety TCP wyzwalające środki ograniczające DDoS|Brak|
|Pakiety DDoSTriggeruDP|Przychodzące pakiety UDP w celu wyzwolenia ograniczania DDoS|Połów połówek|Maksimum|Przychodzące pakiety UDP w celu wyzwolenia ograniczania DDoS|Brak|
|Pakiety DDoSTriggerSYN|Przychodzące pakiety SYN w celu wyzwolenia łagodzenia DDoS|Połów połówek|Maksimum|Przychodzące pakiety SYN w celu wyzwolenia łagodzenia DDoS|Brak|
|Dostępność Vip|Dostępność ścieżki danych|Liczba|Średnia|Średnia dostępność adresu IP na czas trwania|Port|
|Bytecount|Liczba bajtów|Liczba|Łącznie|Całkowita liczba bajtów przesłanych w okresie czasu|Port,Kierunek|
|Liczba pakietów|Liczba pakietów|Liczba|Łącznie|Całkowita liczba pakietów przesłanych w okresie czasu|Port,Kierunek|
|Konto SynCount|Liczba SYN|Liczba|Łącznie|Całkowita liczba pakietów SYN przesłanych w okresie czasu|Port,Kierunek|



## <a name="microsoftnetworkvirtualnetworks"></a>Sieć Microsoft.Network/virtual Sieci

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Czas podróży w obie strony dla pingów na maszynę wirtualną|Milisekund|Średnia|Czas podróży w obie strony dla pingów wysłanych do docelowej maszyny Wirtualnej|ŹródłoCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Nieudane pingi do maszyny Wirtualnej|Wartość procentowa|Średnia|Procent liczby nieudanych pingów do sumy wysłanych pingów docelowej maszyny Wirtualnej|ŹródłoCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ApplicationRuleHit (Reguła aplikacji)|Liczba trafień reguł aplikacji|Liczba|Łącznie|Ile razy zostały trafione reguły aplikacji|Stan,Przyczyna,Protokół|
|NetworkRuleHit (Zasady sieciowe)|Liczba trafień reguł sieciowych|Liczba|Łącznie|Liczba trafień reguł sieciowych|Stan,Przyczyna,Protokół|
|FirewallHealth (FirewallHealth)|Stan kondycji zapory|Wartość procentowa|Średnia|Stan kondycji zapory|Stan,Przyczyna|
|Przetwarzanie danych|Przetwarzane dane|Bajty|Łącznie|Łączna ilość danych przetwarzanych przez zaporę sieciową|Brak|
|SNATPortutilization (SNATPortutilization)|Wykorzystanie portu SNAT|Wartość procentowa|Średnia|Wykorzystanie portu SNAT|Brak|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Przepływność|Przepływność|PrzeztówPerSekunda|Średnia|Liczba bajtów na sekundę, które obsługiwała brama aplikacji|Brak|
|UnhealthyHostCount|Liczba niezdrowych hostów|Liczba|Średnia|Liczba niezdrowych hostów zaplecza|BackendSettingsPool|
|HealthyHostCount|Liczba zdrowych hostów|Liczba|Średnia|Liczba zdrowych hostów zaplecza|BackendSettingsPool|
|TotalRequests|Łączna liczba żądań|Liczba|Łącznie|Liczba pomyślnych żądań obsługiwanych przez bramę aplikacji|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|Żądania na minutę na zdrowego hosta|Liczba|Średnia|Średnia liczba żądań na minutę na gospodarza zaplecza w dobrej kondycji w puli|BackendSettingsPool|
|Nieudane zapisy|Żądania zakończone niepowodzeniem|Liczba|Łącznie|Liczba nieudanych żądań obsługiwanych przez bramę aplikacji|BackendSettingsPool|
|Status odpowiedzi|Stan odpowiedzi|Liczba|Łącznie|Stan odpowiedzi http zwrócony przez bramę aplikacji|Grupa HttpStatus|
|CurrentConnections (Połączenia prądowe)|Bieżące połączenia|Liczba|Łącznie|Liczba bieżących połączeń nawiązanych za pomocą bramy aplikacji|Brak|
|NewConnectionsPerSecond|Nowe połączenia na sekundę|Połów połówek|Średnia|Nowe połączenia na sekundę ustanowione za pomocą bramy aplikacji|Brak|
|Utilizacja procesora|Wykorzystanie procesora|Wartość procentowa|Średnia|Bieżące wykorzystanie procesora bramy aplikacji|Brak|
|Jednostki pojemnościowe|Bieżące jednostki pojemności|Liczba|Średnia|Zużywane jednostki pojemnościowe|Brak|
|Jednostki o stałej niezdolności do pracy|Stałe jednostki mocy wyładowowej do wyładowania|Liczba|Średnia|Minimalne jednostki pojemności, które będą|Brak|
|Jednostki o szacowanej zdolności produkcyjnej|Szacowane rozliczane jednostki zdolności produkcyjnych|Liczba|Średnia|Szacowana pojemność jednostek, które będą naliczane|Brak|
|Jednostki obliczeniowe|Bieżące jednostki obliczeniowe|Liczba|Średnia|Zużyte jednostki obliczeniowe|Brak|
|Stan wewnętrznej odpowiedzi|Stan odpowiedzi wewnętrznej bazy danych|Liczba|Łącznie|Liczba kodów odpowiedzi HTTP generowanych przez członków wewnętrznej bazy danych. Nie obejmuje to żadnych kodów odpowiedzi generowanych przez bramę aplikacji.|Serwer zaplecza,backendpool,zapleczehttpsetting,httpStatusGroup|
|TlsProtocol ( TlsProtocol )|Protokół TLS klienta|Liczba|Łącznie|Liczba żądań TLS i innych niż TLS zainicjowanych przez klienta, który nawiązał połączenie z bramą aplikacji. Aby wyświetlić dystrybucję protokołu TLS, filtruj według wymiaru protokołu TLS.|Słuchacz,TlsProtocol|
|Bajtów|Bajty wysłane|Bajty|Łącznie|Całkowita liczba bajtów wysłanych przez bramę aplikacji do klientów|Odbiornik|
|Bytesreceived|Bajty odebrane|Bajty|Łącznie|Całkowita liczba bajtów odebranych przez bramę aplikacji od klientów|Odbiornik|
|KlientRtt|RtT klienta|Milisekund|Średnia|Średni czas podróży w obie strony między klientami a bramą aplikacji. Ta metryka wskazuje, jak długo trwa ustanawianie połączeń i zwracanie potwierdzeń|Odbiornik|
|ApplicationGatewayTotalTime|Całkowita liczba czasu bramy aplikacji|Milisekund|Średnia|Średni czas przetwarzania żądania i wysłania odpowiedzi. Jest to obliczane jako średnia interwału od momentu, gdy brama aplikacji odbiera pierwszy bajt żądania HTTP do czasu zakończenia operacji wysyłania odpowiedzi. Należy pamiętać, że zwykle obejmuje to czas przetwarzania bramy aplikacji, czas, przez który pakiety żądań i odpowiedzi są przesyłane przez sieć, oraz czas, przez jaki serwer wewnętrznej bazy danych potrzebował odpowiedzi.|Odbiornik|
|BackendConnectTime (Czas połączenia wsteczne)|Czas połączenia wewnętrznej bazy danych|Milisekund|Średnia|Czas spędzony na nawiązywaniu połączenia z serwerem zaplecza|Odbiornik,Serwer zaplecza,BackendPool,Zapleczehttpsetting|
|BackendFirstByteResponseTime|Czas odpowiedzi pierwszego bajtu wewnętrznej bazy danych|Milisekund|Średnia|Przedział czasu między rozpoczęciem ustanawiania połączenia z serwerem wewnętrznej bazy danych a odbieraniem pierwszego bajtu nagłówka odpowiedzi, przybliżając czas przetwarzania serwera wewnętrznej bazy danych|Odbiornik,Serwer zaplecza,BackendPool,Zapleczehttpsetting|
|Czas wewnętrznej bazy DanychLastByteResponse|Czas odpowiedzi ostatniego bajtu wewnętrznej bazy danych|Milisekund|Średnia|Przedział czasu między rozpoczęciem ustanawiania połączenia z serwerem wewnętrznej bazy danych a odbieraniem ostatniego bajtu treści odpowiedzi|Odbiornik,Serwer zaplecza,BackendPool,Zapleczehttpsetting|
|MatchedCount|Dystrybucja reguł całkowitej zapory aplikacji sieci Web w wersji 1|Liczba|Łącznie|Zapora aplikacji sieci Web w wersji 1 Całkowita dystrybucja reguł dla ruchu przychodzącego|Grupa reguł,RuleId|
|BlockedCount|Dystrybucja reguł zablokowanych żądań zapory aplikacji sieci Web w wersji 1|Liczba|Łącznie|Zapora aplikacji sieci Web w wersji 1 zablokowana dystrybucja reguł żądań|Grupa reguł,RuleId|
|BlockedReqCount|Liczba zablokowanych żądań zapory aplikacji sieci Web w wersji 1|Liczba|Łącznie|Liczba zablokowanych żądań zapory aplikacji sieci Web w wersji 1|Brak|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ŚredniaPaswidth|Przepustowość bramy S2S|PrzeztówPerSekunda|Średnia|Średnia przepustowość bramy między lokacjami w bajtach na sekundę|Brak|
|P2SBandwidth (P2SBandwidth)|Przepustowość bramy P2S|PrzeztówPerSekunda|Średnia|Średnia przepustowość bramy w punkt-lokacja w bajtach na sekundę|Brak|
|P2SConnectionCount|Liczba połączeń P2S|Liczba|Maksimum|Liczba połączeń z punktem lokacji bramy|Protocol (Protokół)|
|TunelAverageBandwidth|Przepustowość tunelu|PrzeztówPerSekunda|Średnia|Średnia przepustowość tunelu w bajtach na sekundę|Nazwa połączenia,RemoteIP|
|TunelEgressbytes|Bajty ruchu wychodzącego tunelu|Bajty|Łącznie|Bajty wychodzące tunelu|Nazwa połączenia,RemoteIP|
|TunelowanieBytes|Bajty w ruchu tunelowym|Bajty|Łącznie|Przychodzące bajty tunelu|Nazwa połączenia,RemoteIP|
|Pakiety pakietów tunelowych|Pakiety wychodzące tunelu|Liczba|Łącznie|Liczba pakietów wychodzących tunelu|Nazwa połączenia,RemoteIP|
|Pakiety tunelowe|Pakiety przychodzące tunelu|Liczba|Łącznie|Przychodząca liczba pakietów tunelu|Nazwa połączenia,RemoteIP|
|TunnelEgressPacketDropTSMismatch|Spadek pakietu niezgodności usługi TS w ruchu tunelowym|Liczba|Łącznie|Liczba upuszczania pakietów wychodzących z niezgodności selektora ruchu w tunelu|Nazwa połączenia,RemoteIP|
|TunelowaniePacketDropTSMismatch|Spadek pakietu niezgodności ts w tunelu|Liczba|Łącznie|Przychodząca liczba upuszczania pakietów z niezgodności selektora ruchu tunelu|Nazwa połączenia,RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Usługi Microsoft.Network/expressRoutePorts

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RxLightPoziom|RxLightPoziom|Liczba|Średnia|Rx Poziom światła w dBm|Link,Lane|
|Poziom TxLight|Poziom TxLight|Liczba|Średnia|Poziom światła TX w dBm|Link,Lane|
|Stan administracyjny|Stan administracyjny|Liczba|Średnia|Stan administracyjny portu|Link|
|LiniaProtocol|LiniaProtocol|Liczba|Średnia|Stan protokołu wiersza portu|Link|
|PortBitsInPerSekund|BitsInPerSekund|Połów połówek|Średnia|Bity przychodzące na sekundę z platformą Azure|Link|
|PortBitsOutPerSekunda|BitsOutPerSekunda|Połów połówek|Średnia|Bity wychodzące z platformy Azure na sekundę|Link|



## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BitsInPerSekund|BitsInPerSekund|Połów połówek|Średnia|Bity przychodzące na sekundę z platformą Azure|Typ komunikacji równorzędnej|
|BitsOutPerSekunda|BitsOutPerSekunda|Połów połówek|Średnia|Bity wychodzące z platformy Azure na sekundę|Typ komunikacji równorzędnej|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|Połów połówek|Średnia|Bity przychodzące na sekundę z platformą Azure|Klucz PeeredCircuitSKey|
|GlobalReachBitsOutPerSekunda|GlobalReachBitsOutPerSekunda|Połów połówek|Średnia|Bity wychodzące z platformy Azure na sekundę|Klucz PeeredCircuitSKey|
|BgpWakość|Dostępność BGP|Wartość procentowa|Średnia|Dostępność protokołu BGP z msee do wszystkich elementów równorzędnych.|Typ komunikacji równorzędnej,Element równorzędny|
|ArpDostępność|Dostępność Arp|Wartość procentowa|Średnia|Dostępność ARP z MSEE do wszystkich peers. 1904.|Typ komunikacji równorzędnej,Element równorzędny|
|QosDropBitsInPerSekund|PołówNieWywiększona połów|Połów połówek|Średnia|Liczba danych porzuconych na sekundę|Brak|
|QosDropBitsOutPerSekunda|Połów PołówNiesieniaka|Połów połówek|Średnia|Przepływy bitów danych porzuconych na sekundę|Brak|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BitsInPerSekund|BitsInPerSekund|Połów połówek|Średnia|Bity przychodzące na sekundę z platformą Azure|Brak|
|BitsOutPerSekunda|BitsOutPerSekunda|Połów połówek|Średnia|Bity wychodzące z platformy Azure na sekundę|Brak|

## <a name="microsoftnetworkconnections"></a>Sieć/połączenia firmy Microsoft.Network

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BitsInPerSekund|BitsInPerSekund|Połów połówek|Średnia|Bity przychodzące na sekundę z platformą Azure|Brak|
|BitsOutPerSekunda|BitsOutPerSekunda|Połów połówek|Średnia|Bity wychodzące z platformy Azure na sekundę|Brak|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSekund|BitsInPerSekund|Połów połówek|Średnia|Bity przychodzące na sekundę z platformą Azure|ConnectionName|
|ErGatewayConnectionBitsOutPerSekunda|BitsOutPerSekunda|Połów połówek|Średnia|Bity wychodzące z platformy Azure na sekundę|ConnectionName|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Punkt QpsByEnd|Zwracane zapytania według punktu końcowego|Liczba|Łącznie|Ile razy punkt końcowy programu Traffic Manager został zwrócony w danym przedziale czasowym|Nazwa punktu końcowego|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Stan punktu końcowego według punktu końcowego|Liczba|Maksimum|1, jeśli stan sondy punktu końcowego jest "Włączone", 0 w przeciwnym razie.|Nazwa punktu końcowego|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ProbesFailedPercent (SondaFailedPercent)|% sond nie powiodło się|Wartość procentowa|Średnia|% sond monitorujących łączność nie powiodło się|Brak|
|ŚredniaRoundtripMs|Śr. Czas podróży w obie strony (ms)|Milisekund|Średnia|Średni czas przejazdu sieciowego (ms) dla sond monitorowania łączności wysyłanych między źródłem a miejscem docelowym|Brak|
|ChecksFailedPercent (SprawdzanieFailedPercent)|Sprawdzanie procentu nie powiodło się (wersja zapoznawcza)|Wartość procentowa|Średnia|% kontroli monitorowania łączności nie powiodło się|SourceAddress,SourceName,SourceResourceId,SourceType,Protocol,DestinationAddress,DestinationName,DestinationResourceId,DestinationType,DestinationPort,TestGroupName,TestConfigurationName|
|RoundTripTimeMs|Czas podróży w obie strony (ms) (wersja zapoznawcza)|Milisekund|Średnia|Czas podróży w milisekundach do kontroli monitorowania łączności|SourceAddress,SourceName,SourceResourceId,SourceType,Protocol,DestinationAddress,DestinationName,DestinationResourceId,DestinationType,DestinationPort,TestGroupName,TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoory

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Liczba żądań|Liczba żądań|Liczba|Łącznie|Liczba żądań klientów obsługiwanych przez serwer proxy HTTP/S|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|Rozmiar żądania|Rozmiar żądania|Bajty|Łącznie|Liczba bajtów wysyłanych jako żądania od klientów do serwera proxy HTTP/S|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|Rozmiar odpowiedzi|Rozmiar odpowiedzi|Bajty|Łącznie|Liczba bajtów wysłanych jako odpowiedzi z serwera proxy HTTP/S do klientów|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|Rozmiar billableResponseSize|Rozmiar odpowiedzi podlegania rozliczania|Bajty|Łącznie|Liczba bajtów podlega chytralnych (co najmniej 2 KB na żądanie) wysłanych jako odpowiedzi z serwera proxy HTTP/S do klientów.|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|ZapleczeRequestCount|Liczba żądań wewnętrznej bazy danych|Liczba|Łącznie|Liczba żądań wysłanych z serwera proxy HTTP/S do zaplecza|HttpStatus,HttpStatusGroup,Zaplecze|
|BackendRequestLatency (BackendRequestLatency)|Opóźnienie żądania wewnętrznej bazy danych|Milisekund|Średnia|Czas obliczony od momentu wysłania żądania przez serwer proxy HTTP/S do wewnętrznej bazy danych do momentu odebranie przez serwer proxy HTTP/S ostatniego bajtu odpowiedzi z wewnętrznej bazy danych|Zaplecze|
|TotalLatency (TotalLatency)|Całkowite opóźnienie|Milisekund|Średnia|Czas obliczony od momentu odebraniem żądania klienta przez serwer proxy HTTP/S do momentu potwierdzenia przez klienta ostatniego bajtu odpowiedzi z serwera proxy HTTP/S|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|BackendHealthPercentage|Procent kondycji wewnętrznej bazy danych|Wartość procentowa|Średnia|Procent pomyślnych sond kondycji od serwera proxy HTTP/S do zaplecza|Zaplecze,Zaplecze|
|Konto WebApplicationFirewallRequestCount|Liczba żądań zapory aplikacji sieci Web|Liczba|Łącznie|Liczba żądań klientów przetworzonych przez Zaporę aplikacji sieci Web|Nazwa zasad,Nazwa reguły,Akcja|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft.Network/privateDnsZones

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QueryVolume (Wyw.|Wolumin kwerendy|Liczba|Łącznie|Liczba zapytań obsługiwanych dla prywatnej strefy DNS|Brak|
|Konto RecordSetCount|Liczba zestawów rekordów|Liczba|Maksimum|Liczba zestawów rekordów w prywatnej strefie DNS|Brak|
|Ulatnianie recordSetCapacityUtilization|Rekordowe wykorzystanie pojemności zestawu mocy produkcyjnych|Wartość procentowa|Maksimum|Procent pojemności zestawu rekordów wykorzystywanej przez prywatną strefę DNS|Brak|
|Wirtualna siećlinkcount|Liczba łączy sieci wirtualnej|Liczba|Maksimum|Liczba sieci wirtualnych połączonych z prywatną strefą DNS|Brak|
|Wirtualna SiećLinkCapacityUtilization|Wykorzystanie pojemności łącza sieci wirtualnej|Wartość procentowa|Maksimum|Procent pojemności łącza sieci wirtualnej wykorzystywanej przez prywatną strefę DNS|Brak|
|VirtualNetworkWithRegistrationLinkCount|Liczba łączy rejestracji sieci wirtualnej|Liczba|Maksimum|Liczba sieci wirtualnych połączonych z prywatną strefą DNS z włączoną automatyczną rejestracją|Brak|
|VirtualNetworkWithRegistrationCapacityUtilization (Wirtualna sieć z rejestracjązduszność|Wykorzystanie pojemności łącza rejestracji sieci wirtualnej|Wartość procentowa|Maksimum|Procent łącza sieci wirtualnej z pojemnością automatycznej rejestracji wykorzystywaną przez prywatną strefę DNS|Brak|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Przestrzenie nazw/NotificationHubs

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|rejestracja.wszystko|Operacje rejestracji|Liczba|Łącznie|Liczba wszystkich pomyślnych operacji rejestracji (kreacje aktualizuje kwerendy i usunięcia). |Brak|
|rejestracja.tworzenie|Rejestracja tworzenie operacji|Liczba|Łącznie|Liczba wszystkich pomyślnych kreacji rejestracji.|Brak|
|rejestracja.update|Operacje aktualizacji rejestracji|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji rejestracji.|Brak|
|rejestracja.get|Operacje odczytu rejestracji|Liczba|Łącznie|Liczba wszystkich pomyślnych zapytań rejestracyjnych.|Brak|
|rejestracja.delete|Operacje usuwania rejestracji|Liczba|Łącznie|Liczba wszystkich pomyślnych usunień rejestracji.|Brak|
|Przychodzące|Wiadomości przychodzące|Liczba|Łącznie|Liczba wszystkich pomyślnych wywołań interfejsu API wysyłania. |Brak|
|incoming.scheduled|Wysłane zaplanowane powiadomienia wypychane|Liczba|Łącznie|Anulowane zaplanowane powiadomienia wypychane|Brak|
|incoming.scheduled.cancel|Anulowane zaplanowane powiadomienia wypychane|Liczba|Łącznie|Anulowane zaplanowane powiadomienia wypychane|Brak|
|scheduled.pending|Oczekujące zaplanowane powiadomienia|Liczba|Łącznie|Oczekujące zaplanowane powiadomienia|Brak|
|instalacja.wszystko|Operacje zarządzania instalacją|Liczba|Łącznie|Operacje zarządzania instalacją|Brak|
|installation.get|Pobierz operacje instalacyjne|Liczba|Łącznie|Pobierz operacje instalacyjne|Brak|
|instalacja.upsert|Tworzenie lub aktualizowanie operacji instalacji|Liczba|Łącznie|Tworzenie lub aktualizowanie operacji instalacji|Brak|
|installation.patch|Operacje instalacji poprawek|Liczba|Łącznie|Operacje instalacji poprawek|Brak|
|installation.delete|Usuń operacje instalacji|Liczba|Łącznie|Usuń operacje instalacji|Brak|
|outgoing.allpns.success|Pomyślne powiadomienia|Liczba|Łącznie|Liczba wszystkich pomyślnych powiadomień.|Brak|
|outgoing.allpns.invalidpayload|Błędy ładunku|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się, ponieważ system PNS zwrócił błąd nieprawidłowego ładunku.|Brak|
|outgoing.allpns.pnserror|Błędy zewnętrznego systemu powiadomień|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się, ponieważ wystąpił problem z komunikacją z systemem PNS (wyklucza problemy z uwierzytelnianiem).|Brak|
|outgoing.allpns.channelerror|Błędy kanału|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się, ponieważ kanał był nieprawidłowy, nie jest skojarzony z poprawną aplikacją, która została ograniczona lub wygasła.|Brak|
|outgoing.allpns.badorexpiredchannel|Nieprawidłowe lub wygasłe błędy kanału|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się, ponieważ kanał/token/registrationId w rejestracji wygasła lub nieprawidłowa.|Brak|
|outgoing.wns.success|Pomyślne powiadomienia w ucho.|Liczba|Łącznie|Liczba wszystkich pomyślnych powiadomień.|Brak|
|outgoing.wns.invalidcredentials|Błędy autoryzacji WNS (nieprawidłowe poświadczenia)|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się, ponieważ numer PNS nie zaakceptował podanych poświadczeń lub poświadczenia są blokowane. (Usługa Windows Live nie rozpoznaje poświadczeń).|Brak|
|outgoing.wns.badchannel|Błąd nieprawidłowego kanału WNS|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się, ponieważ ChannelURI w rejestracji nie został rozpoznany (stan WNS: 404 nie znaleziono).|Brak|
|plik outgoing.wns.expiredchannel|Błąd wygasłego kanału WNS|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się, ponieważ ChannelURI wygasł (stan WNS: 410 Gone).|Brak|
|outgoing.wns.throttled|Powiadomienia w sieci WNS z ograniczeniem|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się, ponieważ WNS jest ograniczanie tej aplikacji (stan WNS: 406 Nie do przyjęcia).|Brak|
|outgoing.wns.tokenproviderunreachable|Błędy autoryzacji WNS (nieosiągalne)|Liczba|Łącznie|Usługa Windows Live jest niedoścignięci.|Brak|
|outgoing.wns.invalidtoken|Błędy autoryzacji WNS (nieprawidłowy token)|Liczba|Łącznie|Token dostarczony do sieci WNS jest nieprawidłowy (stan WNS: 401 Unauthorized).|Brak|
|outgoing.wns.wrongtoken|Błędy autoryzacji WNS (nieprawidłowy token)|Liczba|Łącznie|Token dostarczony do usługi WNS jest prawidłowy, ale dla innej aplikacji (stan WNS: 403 Zabronione). Może się tak zdarzyć, jeśli ChannelURI w rejestracji jest skojarzony z inną aplikacją. Sprawdź, czy aplikacja kliencka jest skojarzona z tą samą aplikacją, której poświadczenia znajdują się w Centrum powiadomień.|Brak|
|outgoing.wns.invalidnotificationformat|Nieprawidłowy format powiadomienia WNS|Liczba|Łącznie|Format powiadomienia jest nieprawidłowy (stan WNS: 400). Należy zauważyć, że WNS nie odrzuca wszystkich nieprawidłowych ładunków.|Brak|
|rozmiar pliku outgoing.wns.invalidnotificationsize|Błąd nieprawidłowego rozmiaru powiadomienia WNS|Liczba|Łącznie|Ładunek powiadomień jest za duży (stan WNS: 413).|Brak|
|outgoing.wns.channelthrottled|Kanał WNS ograniczona|Liczba|Łącznie|Powiadomienie zostało usunięte, ponieważ ChannelURI w rejestracji jest ograniczona (nagłówek odpowiedzi WNS: X-WNS-NotificationStatus:channelThrottled).|Brak|
|outgoing.wns.channelzłączone|Kanał WNS rozłączony|Liczba|Łącznie|Powiadomienie zostało usunięte, ponieważ ChannelURI w rejestracji jest ograniczona (nagłówek odpowiedzi WNS: X-WNS-DeviceConnectionStatus: disconnected).|Brak|
|outgoing.wns.dropped|Powiadomienia o upuszczeniu w umorzone w sieci WNS|Liczba|Łącznie|Powiadomienie zostało usunięte, ponieważ ChannelURI w rejestracji jest ograniczona (X-WNS-NotificationStatus: porzucone, ale nie X-WNS-DeviceConnectionStatus: disconnected).|Brak|
|outgoing.wns.pnserror|Błędy WNS|Liczba|Łącznie|Powiadomienie nie zostało dostarczone z powodu błędów komunikacji z siecią WNS.|Brak|
|plik outgoing.wns.authenticationeror|Błędy uwierzytelniania WNS|Liczba|Łącznie|Powiadomienie nie zostało dostarczone z powodu błędów komunikujących się z nieprawidłowymi poświadczeniami usługi Windows Live lub nieprawidłowym tokenem.|Brak|
|outgoing.apns.success|Pomyślne powiadomienia apns|Liczba|Łącznie|Liczba wszystkich pomyślnych powiadomień.|Brak|
|outgoing.apns.invalidedentials|Błędy autoryzacji apns|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się, ponieważ numer PNS nie zaakceptował podanych poświadczeń lub poświadczenia są blokowane.|Brak|
|outgoing.apns.badchannel|Błąd nieprawidłowego kanału APNS|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się, ponieważ token jest nieprawidłowy (kod stanu APNS: 8).|Brak|
|plik outgoing.apns.expiredchannel|Błąd wygasłego kanału APNS|Liczba|Łącznie|Liczba tokenów, które zostały unieważnione przez kanał opinii APNS.|Brak|
|plik outgoing.apns.invalidnotificationsize|Błąd nieprawidłowego rozmiaru powiadomienia apns|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się, ponieważ ładunek był zbyt duży (kod stanu APNS: 7).|Brak|
|outgoing.apns.pnserror|Błędy apns|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się z powodu błędów komunikujących się z programem APNS.|Brak|
|outgoing.gcm.success|Pomyślne powiadomienia gcm|Liczba|Łącznie|Liczba wszystkich pomyślnych powiadomień.|Brak|
|outgoing.gcm.invalidcredentials|Błędy autoryzacji GCM (nieprawidłowe poświadczenia)|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się, ponieważ numer PNS nie zaakceptował podanych poświadczeń lub poświadczenia są blokowane.|Brak|
|outgoing.gcm.badchannel|Błąd nieprawidłowego kanału GCM|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się, ponieważ identyfikator registrationId w rejestracji nie został rozpoznany (wynik GCM: Nieprawidłowa rejestracja).|Brak|
|outgoing.gcm.expiredchannel|Błąd wygasłego kanału GCM|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się, ponieważ identyfikator registrationId w rejestracji wygasł (wynik GCM: NotRegistered).|Brak|
|outgoing.gcm.throttled|Powiadomienia o ograniczeniu gcm|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się, ponieważ GCM ograniczył tę aplikację (kod stanu GCM: 501-599 lub result:Unavailable).|Brak|
|outgoing.gcm.invalidnotificationformat|Nieprawidłowy format powiadomienia GCM|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się, ponieważ ładunek nie został poprawnie sformatowany (wynik GCM: InvalidDataKey lub InvalidTtl).|Brak|
|rozmiar pliku outgoing.gcm.invalidnotificationsize|Błąd nieprawidłowego rozmiaru powiadomienia GCM|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się, ponieważ ładunek był zbyt duży (wynik GCM: MessageTooBig).|Brak|
|plik wychodzący.gcm.wrongchannel|Błąd nieprawidłowego kanału GCM|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się, ponieważ identyfikator registrationId w rejestracji nie jest skojarzony z bieżącą aplikacją (wynik GCM: InvalidPackageName).|Brak|
|outgoing.gcm.pnserror|Błędy GCM|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się z powodu błędów komunikujących się z GCM.|Brak|
|outgoing.gcm.authenticationerror|Błędy uwierzytelniania GCM|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się, ponieważ numer PNS nie zaakceptował podanych poświadczeń, poświadczenia są blokowane lub SenderId nie jest poprawnie skonfigurowany w aplikacji (wynik GCM: Niezgodność ZD).|Brak|
|outgoing.mpns.success|Pomyślne powiadomienia MPNS|Liczba|Łącznie|Liczba wszystkich pomyślnych powiadomień.|Brak|
|outgoing.mpns.invaliddedentials|Nieprawidłowe poświadczenia MPNS|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się, ponieważ numer PNS nie zaakceptował podanych poświadczeń lub poświadczenia są blokowane.|Brak|
|outgoing.mpns.badchannel|Błąd nieprawidłowego kanału MPNS|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się, ponieważ channeluri w rejestracji nie został rozpoznany (stan MPNS: 404 nie znaleziono).|Brak|
|outgoing.mpns.throttled|Powiadomienia z ograniczeniem mpns|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się, ponieważ MPNS jest ograniczanie tej aplikacji (WNS MPNS: 406 Nie do przyjęcia).|Brak|
|outgoing.mpns.invalidnotificationformat|Nieprawidłowy format powiadomienia MPNS|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się, ponieważ ładunek powiadomienia był zbyt duży.|Brak|
|outgoing.mpns.channelzłączone|Kanał MPNS odłączony|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się, ponieważ channeluri w rejestracji został rozłączony (stan MPNS: 412 nie znaleziono).|Brak|
|outgoing.mpns.dropped|Powiadomienia o porzuceniu mpns|Liczba|Łącznie|Liczba wypycheń, które zostały usunięte przez MPNS (nagłówek odpowiedzi MPNS: X-NotificationStatus: QueueFull lub Suppressed).|Brak|
|outgoing.mpns.pnserror|Błędy MPNS|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się z powodu błędów komunikujących się z mpns.|Brak|
|outgoing.mpns.authenticationeroror|Błędy uwierzytelniania MPNS|Liczba|Łącznie|Liczba wypycheń, które nie powiodły się, ponieważ numer PNS nie zaakceptował podanych poświadczeń lub poświadczenia są blokowane.|Brak|
|notificationhub.pushes|Wszystkie powiadomienia wychodzące|Liczba|Łącznie|Wszystkie powiadomienia wychodzące centrum powiadomień|Brak|
|incoming.all.requests|Wszystkie przychodzące żądania|Liczba|Łącznie|Łączna liczba przychodzących żądań dla centrum powiadomień|Brak|
|przychodzące.all.failedrequests|Wszystkie przychodzące żądania nieudane|Liczba|Łącznie|Łączna liczba przychodzących nieudanych żądań dla centrum powiadomień|Brak|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/obszary robocze

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Average_% darmowe i-węzły|% wolnych i-węzłów|Liczba|Średnia|Average_% darmowe i-węzły|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_% wolnego miejsca|% wolnego miejsca|Liczba|Średnia|Average_% wolnego miejsca|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_% używanych iwody|% zużytych i-węzłów|Liczba|Średnia|Average_% używanych iwody|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_% używanej przestrzeni|% używanej przestrzeni|Liczba|Średnia|Average_% używanej przestrzeni|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Disk odczytu bajtów/s|Bajty odczytu dysku/s|Liczba|Średnia|Average_Disk odczytu bajtów/s|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|odczyty Average_Disk/s|Odczyty dysku/s|Liczba|Średnia|odczyty Average_Disk/s|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Disk transfery/s|Transfery dysków/s|Liczba|Średnia|Average_Disk transfery/s|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Disk bajtów zapisu/s|Bajty zapisu dysku na sekundę|Liczba|Średnia|Average_Disk bajtów zapisu/s|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Disk zapisy/s|Zapisy na dysku/s|Liczba|Średnia|Average_Disk zapisy/s|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|megabajty Average_Free|Darmowe megabajty|Liczba|Średnia|megabajty Average_Free|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Logical bajtów dysku na sekundę|Bajty dysku logicznego na sekundę|Liczba|Średnia|Average_Logical bajtów dysku na sekundę|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_% dostępnej pamięci|% dostępnej pamięci|Liczba|Średnia|Average_% dostępnej pamięci|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_% dostępnej przestrzeni wymiany|% dostępnej przestrzeni wymiany|Liczba|Średnia|Average_% dostępnej przestrzeni wymiany|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_% używanej pamięci|% używanej pamięci|Liczba|Średnia|Average_% używanej pamięci|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_% używanej przestrzeni wymiany|% używanej przestrzeni wymiany|Liczba|Średnia|Average_% używanej przestrzeni wymiany|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Available pamięci MBytes|Dostępna pamięć MBytes|Liczba|Średnia|Average_Available pamięci MBytes|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Available MBytes Swap|Dostępne wymiany bajtów|Liczba|Średnia|Average_Available MBytes Swap|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|odczyty Average_Page/s|Odczyty strony/s|Liczba|Średnia|odczyty Average_Page/s|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Page Zapisy/s|Zapis strony/s|Liczba|Średnia|Average_Page Zapisy/s|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Pages/s|Strony/s|Liczba|Średnia|Average_Pages/s|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Used przestrzeni wymiany bajtów|Używana przestrzeń wymiany bajtów MBytes|Liczba|Średnia|Average_Used przestrzeni wymiany bajtów|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Used MBytes pamięci|Używane mbyty pamięci|Liczba|Średnia|Average_Used MBytes pamięci|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Total bajtów przesyłanych|Łączna liczba przesłanych bajtów|Liczba|Średnia|Average_Total bajtów przesyłanych|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Odebrano bajty Average_Total|Całkowita liczba odebranych bajtów|Liczba|Średnia|Odebrano bajty Average_Total|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|bajty Average_Total|Całkowita liczba bajtów|Liczba|Średnia|bajty Average_Total|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Total przesyłane pakiety|Łączna liczba przesłanych pakietów|Liczba|Średnia|Average_Total przesyłane pakiety|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Odebrane Average_Total pakiety|Łączna liczba odebranych pakietów|Liczba|Średnia|Odebrane Average_Total pakiety|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Total błędy Rx|Całkowita liczba błędów Rx|Liczba|Średnia|Average_Total błędy Rx|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Total błędy TX|Całkowita liczba błędów TX|Liczba|Średnia|Average_Total błędy TX|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Kolizje Average_Total|Łączna liczba kolizji|Liczba|Średnia|Kolizje Average_Total|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Avg. wartość operacji dysku na sek./odczyt|Średnia dysk s/odczyt|Liczba|Średnia|Average_Avg. wartość operacji dysku na sek./odczyt|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Avg. Transfery dysku/s|Średnia s/transfer dysku|Liczba|Średnia|Average_Avg. Transfery dysku/s|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Avg. dysku na sek./zapis|Średnia sek./zapis|Liczba|Średnia|Average_Avg. dysku na sek./zapis|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Physical bajtów dysku na sekundę|Bajty dysku fizycznego/s|Liczba|Średnia|Average_Physical bajtów dysku na sekundę|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Pct uprzywilejowany czas|Pct Uprzywilejowany czas|Liczba|Średnia|Average_Pct uprzywilejowany czas|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Pct czas użytkownika|Pct Czas użytkownika|Liczba|Średnia|Average_Pct czas użytkownika|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Used pamięci kBytes|Używane pamięci kBytes|Liczba|Średnia|Average_Used pamięci kBytes|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Virtual pamięć udostępniona|Wirtualna pamięć współdzielona|Liczba|Średnia|Average_Virtual pamięć udostępniona|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_% czasu DPC|% czasu DPC|Liczba|Średnia|Average_% czasu DPC|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_% czasu bezczynnego|Czas bezczynności (%)|Liczba|Średnia|Average_% czasu bezczynnego|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_% czasu przerwania|% czasu przerwania|Liczba|Średnia|Average_% czasu przerwania|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_% czasu oczekiwania we/wy|% czasu oczekiwania we/wy|Liczba|Średnia|Average_% czasu oczekiwania we/wy|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_% miły czas|% Miły czas|Liczba|Średnia|Average_% miły czas|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_% czasu uprzywilejowanego|% czasu uprzywilejowanego|Liczba|Średnia|Average_% czasu uprzywilejowanego|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_% czasu procesora|Czas procesora (%)|Liczba|Średnia|Average_% czasu procesora|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_% czasu użytkownika|% czasu użytkownika|Liczba|Średnia|Average_% czasu użytkownika|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Free pamięć fizyczna|Wolna pamięć fizyczna|Liczba|Średnia|Average_Free pamięć fizyczna|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Free miejsca w plikach stronicowania|Wolne miejsce w plikach stronicowania|Liczba|Średnia|Average_Free miejsca w plikach stronicowania|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Free pamięci wirtualnej|Darmowa pamięć wirtualna|Liczba|Średnia|Average_Free pamięci wirtualnej|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Processes|Procesy|Liczba|Średnia|Average_Processes|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Size przechowywane w plikach stronicowania|Rozmiar przechowywany w plikach stronicowania|Liczba|Średnia|Average_Size przechowywane w plikach stronicowania|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Uptime|Uptime|Liczba|Średnia|Average_Uptime|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Users|Użytkownicy|Liczba|Średnia|Average_Users|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Current długość kolejki dysku|Bieżąca długość kolejki dysku|Liczba|Średnia|Average_Current długość kolejki dysku|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Available MBytes|Dostępne MBytes|Liczba|Średnia|Average_Available MBytes|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_% używanych bajtów|% używanych bajtów na które|Liczba|Średnia|Average_% używanych bajtów|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Bytes odebrane/s|Bajty odebrane/s|Liczba|Średnia|Average_Bytes odebrane/s|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Bytes wysłane/s|Bajty wysłane/s|Liczba|Średnia|Average_Bytes wysłane/s|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Average_Bytes Suma/s|Suma bajtów/s|Liczba|Średnia|Average_Bytes Suma/s|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Długość Average_Processor kolejki|Długość kolejki procesora|Liczba|Średnia|Długość Average_Processor kolejki|Komputer,Nazwa obiektu,Nazwa wystąpienia,Ścieżka licznika,System źródła|
|Puls|Puls|Liczba|Łącznie|Puls|Komputer,Typ systemu operacyjnego,Wersja,Komputer źródłowy|
|Aktualizacja|Aktualizacja|Liczba|Średnia|Aktualizacja|Komputer,Produkt,Klasyfikacja,UpdateState,Opcjonalnie,Zatwierdzone|
|Wydarzenie|Wydarzenie|Liczba|Średnia|Wydarzenie|Źródło,EventLog,Komputer,Kategoria zdarzeń,EventLevel,EventLevelName,EventID|

## <a name="microsoftpeeringpeeringservices"></a>Usługi komunikacji równorzędnej/komunikacji firmy Microsoft

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PrefiksLatency|Opóźnienie prefiksu|Milisekund|Średnia|Mediana opóźnienia prefiksu|Nazwa_prefiksu|

## <a name="microsoftpeeringpeerings"></a>Komunikacja równorzędna/komunikacja równorzędna firmy Microsoft

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SesjaDostępnośćV4|Dostępność sesji v4|Wartość procentowa|Średnia|Dostępność sesji V4|Connectionid|
|SesjaDostępnośćV6|Dostępność sesji V6|Wartość procentowa|Średnia|Dostępność sesji V6|Connectionid|
|IngressTrafficRate (Wzrost ruchu ingresicrate)|Szybkość ruchu przychodzącego|BitsPerSecond (BitsPerSecond)|Średnia|Szybkość ruchu przychodzącego w bitach na sekundę|Connectionid|
|Ruch wychodzącyAterwa|Szybkość ruchu wychodzącego|BitsPerSecond (BitsPerSecond)|Średnia|Szybkość ruchu wychodzącego w bitach na sekundę|Connectionid|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacitys

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Wyszukiwanie zapytań|Czas trwania zapytania|Milisekund|Średnia|Czas trwania kwerendy języka DAX w ostatnim interwale|Brak wymiarów|
|QueryPoolJobQueueLength|Wątki: Długość kolejki zadań puli zapytań|Liczba|Średnia|Liczba zadań w kolejce puli wątków kwerend.|Brak wymiarów|
|qpu_high_utilization_metric|Wysokie wykorzystanie jednostek QPU|Liczba|Łącznie|QPU High Utilization In Last Minute, 1 dla wysokiego wykorzystania QPU, w przeciwnym razie 0|Brak wymiarów|
|memory_metric|Memory (Pamięć)|Bajty|Średnia|Memory (pamięć). Zakres 0-3 GB dla A1, 0-5 GB dla A2, 0-10 GB dla A3, 0-25 GB dla A4, 0-50 GB dla A5 i 0-100 GB dla A6|Brak wymiarów|
|memory_thrashing_metric|Przeładowywanie pamięci|Wartość procentowa|Średnia|Średnia pamięć thrashing.|Brak wymiarów|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft.ProjectBabylon/konta

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Klasyfikacja danych assetdistributionByClassification|Podział aktywów według klasyfikacji|Liczba|Łącznie|Wskazuje liczbę aktywów z określoną przypisaną klasyfikacją, to znaczy są one klasyfikowane z tą etykietą.|Klasyfikacja,Źródło,Identyfikator zasobu|
|AssetDistributionByStorageType|Dystrybucja zasobów według typu magazynu|Liczba|Łącznie|Wskazuje liczbę zasobów o określonym typie magazynu.|Typ magazynu,Identyfikator zasobu|
|CatalogActiveUsers|Codziennie aktywni użytkownicy|Liczba|Łącznie|Liczba aktywnych użytkowników dziennie|ResourceId|
|KatalogPodań|Rozkład użycia według operacji|Liczba|Łącznie|Wskazać liczbę operacji, które użytkownik wykonuje w katalogu, tj.|Operacja,Identyfikator zasobu|
|NumberOfAssetsWithClassifications|Liczba aktywów o co najmniej jednej klasyfikacji|Liczba|Średnia|Wskazuje liczbę zasobów z co najmniej jedną klasyfikacją znaczników.|ResourceId|
|SkanowanieCancelled|Skanowanie anulowane|Liczba|Łącznie|Wskazuje liczbę anulowanych skanów.|ResourceId|
|SkanowanieNieukoletowane|Skanowanie zakończone|Liczba|Łącznie|Wskazuje liczbę skanów zakończonych pomyślnie.|ResourceId|
|ScanFailed (Nieumiejętnieszone|Skanowanie nie powiodło się|Liczba|Łącznie|Wskazuje liczbę skanów, które nie powiodły się.|ResourceId|
|Czas skanowaniaWyjętego|Czas skanowania|Sekundy|Łącznie|Wskazuje całkowity czas skanowania w sekundach.|ResourceId|




## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/przestrzenie nazw

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ListenerConnections-Sukces|ListenerConnections-Sukces|Liczba|Łącznie|Pomyślne połączenia listener dla programu Microsoft.Relay.|Nazwa elementu,OperacjaWysoki|
|OdbiornikConnections-ClientError|OdbiornikConnections-ClientError|Liczba|Łącznie|ClientError na ListenerConnections dla microsoft.relay.|Nazwa elementu,OperacjaWysoki|
|OdbiornikConnections-ServerError|OdbiornikConnections-ServerError|Liczba|Łącznie|ServerError na ListenerConnections dla microsoft.relay.|Nazwa elementu,OperacjaWysoki|
|SenderConnections-Sukces|SenderConnections-Sukces|Liczba|Łącznie|Pomyślne połączenia nadawcze dla microsoft.relay.|Nazwa elementu,OperacjaWysoki|
|SenderConnections-ClientError|SenderConnections-ClientError|Liczba|Łącznie|ClientError na SenderConnections dla microsoft.relay.|Nazwa elementu,OperacjaWysoki|
|SenderConnections-ServerError|SenderConnections-ServerError|Liczba|Łącznie|ServerError na SenderConnections dla microsoft.relay.|Nazwa elementu,OperacjaWysoki|
|Połączenia słuchaczy-TotalRequests|Połączenia słuchaczy-TotalRequests|Liczba|Łącznie|Całkowita liczba połączeń listenerconnections dla programu Microsoft.Relay.|Nazwa elementu|
|Połączenia nadawców-TotalRequests|Połączenia nadawców-TotalRequests|Liczba|Łącznie|Łączna liczba żądań SenderConnections dla programu Microsoft.Relay.|Nazwa elementu|
|ActiveConnections (Aktywne połączenia)|ActiveConnections (Aktywne połączenia)|Liczba|Łącznie|Całkowita liczba activeconnections dla microsoft.relay.|Nazwa elementu|
|AktywneListenery|AktywneListenery|Liczba|Łącznie|Całkowita liczba activelisteners dla microsoft.relay.|Nazwa elementu|
|BajtyPrzejstrzej|BajtyPrzejstrzej|Liczba|Łącznie|Całkowita liczba bajtówprzejęta dla programu Microsoft.Relay.|Nazwa elementu|
|OdbiornikRozłącza|OdbiornikRozłącza|Liczba|Łącznie|Całkowita liczba odbiornikówRozłącza dla microsoft.relay.|Nazwa elementu|
|SenderDisconnects|SenderDisconnects|Liczba|Łącznie|Całkowita liczba połączeń senderdisconnects dla microsoft.relay.|Nazwa elementu|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchSługs

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SearchLatency (WyszukiwanieLatency)|Opóźnienie wyszukiwania|Sekundy|Średnia|Średnie opóźnienie wyszukiwania usługi wyszukiwania|Brak|
|SearchQueriesPerSecond|Zapytania wyszukiwania na sekundę|Połów połówek|Średnia|Zapytania na sekundę dla usługi wyszukiwania|Brak|
|ThrottledSearchQueriesPercentage|Ograniczona wartość procentowa zapytań|Wartość procentowa|Średnia|Procent zapytań, które zostały ograniczone dla usługi wyszukiwania|Brak|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/przestrzenie nazw

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Udane prośby|Pomyślne żądania|Liczba|Łącznie|Łączna liczba pomyślnych żądań dotyczących obszaru nazw|Nazwa elementu,OperacjaWysoki|
|Serwery serwerowe|Błędy serwera.|Liczba|Łącznie|Błędy serwera dla microsoft.servicebus.|Nazwa elementu,OperacjaWysoki|
|UserErrors (UżytkownikErrors)|Błędy użytkownika.|Liczba|Łącznie|Błędy użytkownika dla microsoft.servicebus.|Nazwa elementu,OperacjaWysoki|
|ThrottledRequests|Ograniczone żądania.|Liczba|Łącznie|Ograniczone żądania dla microsoft.ServiceBus.|Nazwa elementu,OperacjaWysoki|
|Przychodzącerepy|Żądania przychodzące|Liczba|Łącznie|Przychodzące żądania dla microsoft.servicebus.|Nazwa elementu|
|Przychodzącenaśniacje|Wiadomości przychodzące|Liczba|Łącznie|Wiadomości przychodzące dla usługi Microsoft.ServiceBus.|Nazwa elementu|
|Wychodzące Wiadomości|Wiadomości wychodzące|Liczba|Łącznie|Wiadomości wychodzące dla usługi Microsoft.ServiceBus.|Nazwa elementu|
|ActiveConnections (Aktywne połączenia)|ActiveConnections (Aktywne połączenia)|Liczba|Łącznie|Całkowita liczba aktywnych połączeń dla usługi Microsoft.ServiceBus.|Brak|
|ConnectionsOtwarte|Połączenia otwarte.|Liczba|Średnia|Połączenia otwarte dla microsoft.servicebus.|Nazwa elementu|
|Połączenia Zamknięte|Połączenia zamknięte.|Liczba|Średnia|Połączenia zamknięte dla microsoft.servicebus.|Nazwa elementu|
|Rozmiar|Rozmiar|Bajty|Średnia|Rozmiar kolejki/tematu w bajtach.|Nazwa elementu|
|Komunikaty|Liczba wiadomości w kolejce/temacie.|Liczba|Średnia|Liczba wiadomości w kolejce/temacie.|Nazwa elementu|
|ActiveMessages (Aktywne"|Liczba aktywnych wiadomości w kolejce/temacie.|Liczba|Średnia|Liczba aktywnych wiadomości w kolejce/temacie.|Nazwa elementu|
|DeadletteredNadycje|Liczba wiadomości utraconych w kolejce/temacie.|Liczba|Średnia|Liczba wiadomości utraconych w kolejce/temacie.|Nazwa elementu|
|Zaplanowane-Messages|Liczba zaplanowanych wiadomości w kolejce/temacie.|Liczba|Średnia|Liczba zaplanowanych wiadomości w kolejce/temacie.|Nazwa elementu|
|NamespaceCpuUsage|Procesor CPU|Wartość procentowa|Maksimum|Metryka użycia procesora CPU dla obszaru nazw usługi premium.|Replika|
|NamespaceMemoryUsage|Użycie pamięci|Wartość procentowa|Maksimum|Metryka użycia pamięci obszaru nazw usługi premium.|Replika|
|CPUXNS ( CPUXNS )|Procesor (przestarzały)|Wartość procentowa|Maksimum|Metryka użycia procesora CPU dla obszaru nazw usługi premium. Ta metryka jest depricated. Zamiast tego użyj metryki procesora (NamespaceCpuUsage).|Replika|
|WSXNS ( WSXNS )|Użycie pamięci (przestarzałe)|Wartość procentowa|Maksimum|Metryka użycia pamięci obszaru nazw usługi premium. Ta metryka jest przestarzała. Zamiast tego użyj metryki Użycie pamięci (NamespaceMemoryUsage).|Replika|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AllocatedCpu (Protokół zaalokowany)|AllocatedCpu (Protokół zaalokowany)|Liczba|Średnia|Procesor przydzielony do tego kontenera w rdzeniach milli|Nazwa aplikacji,Nazwa usługi,Nazwa pakietu kodu,Nazwa usługi|
|AllocatedMemory (Memory)|AllocatedMemory (Memory)|Bajty|Średnia|Pamięć przydzielona do tego kontenera w MB|Nazwa aplikacji,Nazwa usługi,Nazwa pakietu kodu,Nazwa usługi|
|Rzeczywista protokółCpu|Rzeczywista protokółCpu|Liczba|Średnia|Rzeczywiste użycie procesora w rdzeniach milli|Nazwa aplikacji,Nazwa usługi,Nazwa pakietu kodu,Nazwa usługi|
|Wartość rzeczywista|Wartość rzeczywista|Bajty|Średnia|Rzeczywiste użycie pamięci w MB|Nazwa aplikacji,Nazwa usługi,Nazwa pakietu kodu,Nazwa usługi|
|Utilizacja procesora|Utilizacja procesora|Wartość procentowa|Średnia|Wykorzystanie procesora DLA tego kontenera jako procent allocatedCpu|Nazwa aplikacji,Nazwa usługi,Nazwa pakietu kodu,Nazwa usługi|
|MemoryUtilization (Funkcja MemoryUtilization)|MemoryUtilization (Funkcja MemoryUtilization)|Wartość procentowa|Średnia|Wykorzystanie procesora DLA tego kontenera jako procent allocatedCpu|Nazwa aplikacji,Nazwa usługi,Nazwa pakietu kodu,Nazwa usługi|
|Stan aplikacji|Stan aplikacji|Liczba|Średnia|Stan aplikacji siatki sieci szkieletowej usług|Nazwa aplikacji,Stan|
|Stan serwisu|Stan serwisu|Liczba|Średnia|Stan kondycji usługi w aplikacji sieci szkieletowej usług|Nazwa aplikacji,Stan,Nazwa usługi|
|ServiceReplicaStatus (Stan ServiceReplica)|ServiceReplicaStatus (Stan ServiceReplica)|Liczba|Średnia|Stan kondycji repliki usługi w aplikacji sieci szkieletowej usługi|Nazwa aplikacji,Stan,Nazwa usługi,Nazwa usługi|
|Stan kontenera|Stan kontenera|Liczba|Średnia|Stan kontenera w aplikacji sieci szkieletowej usługi|Nazwa aplikacji,Nazwa usługi,Nazwa pakietu kodu,Nazwa usługi,Nazwa usługi|
|Liczba restartów|Liczba restartów|Liczba|Średnia|Ponowna liczba kontenera w aplikacji sieci szkieletowej usługi|Nazwa aplikacji,Stan,Nazwa usługi,Nazwa usługi,Nazwa usługi,Nazwa pakietu kodu CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalrService/Signalr

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Liczba połączeń|Liczba połączeń|Liczba|Maksimum|Ilość połączenia użytkownika.|Endpoint|
|Liczba wiadomości|Liczba wiadomości|Liczba|Łącznie|Całkowita ilość wiadomości.|Brak|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ruch przychodzący usługi|Brak|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ruch wychodzący usługi|Brak|
|UserErrors (UżytkownikErrors)|Błędy użytkownika|Wartość procentowa|Maksimum|Procent błędów użytkownika|Brak|
|SystemErrors (SystemErrors)|Błędy systemu|Wartość procentowa|Maksimum|Procent błędów systemu|Brak|



## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/serwery/bazy danych

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Wartość procentowa|Średnia|Procent użycia procesora CPU|Brak|
|physical_data_read_percent|Procent użycia operacji we/wy na danych|Wartość procentowa|Średnia|Procent użycia operacji we/wy na danych|Brak|
|log_write_percent|Log Procent we/wy|Wartość procentowa|Średnia|Log IO procent. Nie dotyczy hurtowni danych.|Brak|
|dtu_consumption_percent|Procent użycia jednostek DTU|Wartość procentowa|Średnia|Procent YTW. Dotyczy baz danych opartych na UJ.|Brak|
|magazyn|Wykorzystano miejsce na dane|Bajty|Maksimum|Wykorzystano miejsce na dane. Nie dotyczy hurtowni danych.|Brak|
|connection_successful|Udane połączenia|Liczba|Łącznie|Udane połączenia|Brak|
|connection_failed|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|Brak|
|blocked_by_firewall|Zablokowane przez zaporę sieciową|Liczba|Łącznie|Zablokowane przez zaporę sieciową|Brak|
|Zakleszczenie|Zakleszczenia|Liczba|Łącznie|Zakleszczenia. Nie dotyczy hurtowni danych.|Brak|
|storage_percent|Procent wykorzystanej przestrzeni danych|Wartość procentowa|Maksimum|Ilość używanego miejsca na dane. Nie dotyczy magazynów danych lub baz danych w hiperskali.|Brak|
|xtp_storage_percent|Procent pamięci OLTP|Wartość procentowa|Średnia|Procent magazynu OLTP w pamięci. Nie dotyczy hurtowni danych.|Brak|
|workers_percent|Procent pracowników|Wartość procentowa|Średnia|Procent pracowników. Nie dotyczy hurtowni danych.|Brak|
|sessions_percent|Procent sesji|Wartość procentowa|Średnia|Procent sesji. Nie dotyczy hurtowni danych.|Brak|
|dtu_limit|DTU Limit|Liczba|Średnia|Limit DTU. Dotyczy baz danych opartych na UJ.|Brak|
|dtu_used|Używane DTU|Liczba|Średnia|Używane przy użyciu DTU. Dotyczy baz danych opartych na UJ.|Brak|
|cpu_limit|Limit procesora|Liczba|Średnia|limit procesora. Dotyczy baz danych opartych na wynikach wirtualnych.|Brak|
|cpu_used|Używany procesor|Liczba|Średnia|Używany procesor. Dotyczy baz danych opartych na wynikach wirtualnych.|Brak|
|dwu_limit|Limit DWU|Liczba|Maksimum|LIMIT DWU. Dotyczy tylko magazynów danych.|Brak|
|dwu_consumption_percent|Dwu procent|Wartość procentowa|Maksimum|DWU procent. Dotyczy tylko magazynów danych.|Brak|
|dwu_used|DWU używany|Liczba|Maksimum|DWU używane. Dotyczy tylko magazynów danych.|Brak|
|cache_hit_percent|Procent trafienia w pamięci podręcznej|Wartość procentowa|Maksimum|Procent trafienia w pamięci podręcznej. Dotyczy tylko magazynów danych.|Brak|
|cache_used_percent|Wartość procentowa używanej pamięci podręcznej|Wartość procentowa|Maksimum|Pamięć podręczna używana procent. Dotyczy tylko magazynów danych.|Brak|
|sqlserver_process_core_percent|Procent rdzenia procesu programu SQL Server|Wartość procentowa|Maksimum|Procent użycia procesora CPU dla procesu programu SQL Server, mierzony przez system operacyjny.|Brak|
|sqlserver_process_memory_percent|Procent pamięci procesu programu SQL Server|Wartość procentowa|Maksimum|Procent użycia pamięci dla procesu programu SQL Server, mierzony przez system operacyjny.|Brak|
|tempdb_data_size|Kilobajty rozmiaru pliku danych tempdb|Liczba|Maksimum|Kilobajty o rozmiarze pliku danych tempdb. Nie dotyczy hurtowni danych.|Brak|
|tempdb_log_size|Kilobajty rozmiaru pliku dziennika tempdb|Liczba|Maksimum|Kilobajty o rozmiarze pliku dziennika tempdb. Nie dotyczy hurtowni danych.|Brak|
|tempdb_log_used_percent|Używany dziennik procentu tempdb|Wartość procentowa|Maksimum|Używany dziennik procentu tempdb. Nie dotyczy hurtowni danych.|Brak|
|local_tempdb_usage_percent|Lokalny procent tempdb|Wartość procentowa|Średnia|Lokalny procent tempdb. Dotyczy tylko magazynów danych.|Brak|
|app_cpu_billed|Obciążenie procesora aplikacji naliczane|Liczba|Łącznie|Obciążenie procesora aplikacji naliczane. Dotyczy baz danych bezserwerowych.|Brak|
|app_cpu_percent|Procent procesora aplikacji|Wartość procentowa|Średnia|Procent procesora aplikacji. Dotyczy baz danych bezserwerowych.|Brak|
|app_memory_percent|Procent pamięci aplikacji|Wartość procentowa|Średnia|Procent pamięci aplikacji. Dotyczy baz danych bezserwerowych.|Brak|
|allocated_data_storage|Przydzielone miejsce na dane|Bajty|Średnia|Przydzielone przechowywanie danych. Nie dotyczy hurtowni danych.|Brak|
|memory_usage_percent|Procent pamięci|Wartość procentowa|Maksimum|Procent pamięci. Dotyczy tylko magazynów danych.|Brak|
|dw_backup_size_gb|Rozmiar magazynu danych|Liczba|Łącznie|Rozmiar magazynu danych składa się z rozmiaru danych i dziennika transakcji. Dane są wliczane do części "Magazyn" rachunku. Dotyczy tylko magazynów danych.|Brak|
|dw_snapshot_size_gb|Rozmiar magazynu migawki|Liczba|Łącznie|Rozmiar magazynu migawek to rozmiar przyrostowych zmian przechwyconych przez migawki w celu utworzenia zdefiniowanych przez użytkownika i automatycznych punktów przywracania. Dane są wliczane do części "Magazyn" rachunku. Dotyczy tylko magazynów danych.|Brak|
|dw_geosnapshot_size_gb|Rozmiar magazynu odzyskiwania po awarii|Liczba|Łącznie|Rozmiar magazynu odzyskiwania po awarii jest odzwierciedlany jako "Magazyn odzyskiwania po awarii" na rachunku. Dotyczy tylko magazynów danych.|Brak|
|wlg_allocation_relative_to_system_percent|Alokacja grupy obciążenia według procentu systemu|Wartość procentowa|Maksimum|Przydzielony procent zasobów w stosunku do całego systemu na grupę obciążenia. Dotyczy tylko magazynów danych.|WorkloadGroupName,IsUserDefiniowane|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Alokacja grupy obciążenia według procentu zasobów cap|Wartość procentowa|Maksimum|Zaalokowany procent zasobów w stosunku do określonych zasobów limitu na grupę obciążenia. Dotyczy tylko magazynów danych.|WorkloadGroupName,IsUserDefiniowane|
|wlg_active_queries|Aktywne kwerendy grupy obciążenia|Liczba|Łącznie|Aktywne zapytania w grupie obciążenia. Dotyczy tylko magazynów danych.|WorkloadGroupName,IsUserDefiniowane|
|wlg_queued_queries|Kwerendy w kolejce grupy obciążeń|Liczba|Łącznie|Kwerendy umieszczone w kolejce w grupie obciążenia. Dotyczy tylko magazynów danych.|WorkloadGroupName,IsUserDefiniowane|
|active_queries|Aktywne zapytania|Liczba|Łącznie|Aktywne zapytania we wszystkich grupach obciążenia. Dotyczy tylko magazynów danych.|Brak|
|queued_queries|Kwerendy w kolejce|Liczba|Łącznie|Kwerendy w kolejce we wszystkich grupach obciążenia. Dotyczy tylko magazynów danych.|Brak|
|wlg_active_queries_timeouts|Limity czasu kwerend grupy obciążenia|Liczba|Łącznie|Kwerendy, które zostały przesunęły limit czasu dla grupy obciążenia. Dotyczy tylko magazynów danych.|WorkloadGroupName,IsUserDefiniowane|
|wlg_effective_min_resource_percent|Efektywny procent minimalnego zasobu|Wartość procentowa|Maksimum|Minimalny procent zasobów zarezerwowanych i odizolowanych dla grupy obciążenia, biorąc pod uwagę minimalny poziom usług. Dotyczy tylko magazynów danych.|WorkloadGroupName,IsUserDefiniowane|
|wlg_effective_cap_resource_percent|Efektywny procent zasobów cap|Wartość procentowa|Maksimum|Twardy limit procentu zasobów dozwolonych dla grupy obciążenia, biorąc pod uwagę efektywny procent minimalnego zasobu przydzielony dla innych grup obciążenia. Dotyczy tylko magazynów danych.|WorkloadGroupName,IsUserDefiniowane|
|full_backup_size_bytes|Pełny rozmiar pamięci masowej kopii zapasowej|Bajty|Maksimum|Skumulowany pełny rozmiar magazynu kopii zapasowej. Dotyczy baz danych opartych na wynikach wirtualnych. Nie dotyczy baz danych na dużą skalę.|Brak|
|diff_backup_size_bytes|Rozmiar magazynu kopii zapasowych różnicowego|Bajty|Maksimum|Skumulowany rozmiar magazynu różnicowego kopii zapasowej. Dotyczy baz danych opartych na wynikach wirtualnych. Nie dotyczy baz danych na dużą skalę.|Brak|
|log_backup_size_bytes|Rozmiar magazynu kopii zapasowej dziennika|Bajty|Maksimum|Skumulowany rozmiar magazynu kopii zapasowej dziennika. Dotyczy baz danych opartych nacorach wirtualnych i hiperskalach.|Brak|
|snapshot_backup_size_bytes|Rozmiar magazynu kopii zapasowej migawki|Bajty|Maksimum|Skumulowany rozmiar magazynu kopii zapasowej migawki. Dotyczy baz danych na dużą skalę.|Brak|
|base_blob_size_bytes|Rozmiar magazynu podstawowego obiektu blob|Bajty|Maksimum|Rozmiar magazynu podstawowego obiektu blob. Dotyczy baz danych na dużą skalę.|Brak|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Wartość procentowa|Średnia|Procent użycia procesora CPU|Brak|
|database_cpu_percent|Procent użycia procesora CPU|Wartość procentowa|Średnia|Procent użycia procesora CPU|DataResourceId|
|physical_data_read_percent|Procent użycia operacji we/wy na danych|Wartość procentowa|Średnia|Procent użycia operacji we/wy na danych|Brak|
|database_physical_data_read_percent|Procent użycia operacji we/wy na danych|Wartość procentowa|Średnia|Procent użycia operacji we/wy na danych|DataResourceId|
|log_write_percent|Log Procent we/wy|Wartość procentowa|Średnia|Log Procent we/wy|Brak|
|database_log_write_percent|Log Procent we/wy|Wartość procentowa|Średnia|Log Procent we/wy|DataResourceId|
|dtu_consumption_percent|Procent użycia jednostek DTU|Wartość procentowa|Średnia|Procent YTW. Dotyczy elastycznych pul opartych na UJ.|Brak|
|database_dtu_consumption_percent|Procent użycia jednostek DTU|Wartość procentowa|Średnia|Procent użycia jednostek DTU|DataResourceId|
|storage_percent|Procent wykorzystanej przestrzeni danych|Wartość procentowa|Średnia|Procent wykorzystanej przestrzeni danych|Brak|
|workers_percent|Procent pracowników|Wartość procentowa|Średnia|Procent pracowników|Brak|
|database_workers_percent|Procent pracowników|Wartość procentowa|Średnia|Procent pracowników|DataResourceId|
|sessions_percent|Procent sesji|Wartość procentowa|Średnia|Procent sesji|Brak|
|database_sessions_percent|Procent sesji|Wartość procentowa|Średnia|Procent sesji|DataResourceId|
|eDTU_limit|Limit eDTU|Liczba|Średnia|limitu eDTU. Dotyczy elastycznych pul opartych na UJ.|Brak|
|storage_limit|Maksymalny rozmiar danych|Bajty|Średnia|Maksymalny rozmiar danych|Brak|
|eDTU_used|EDTU używane|Liczba|Średnia|eDTU. Dotyczy elastycznych pul opartych na UJ.|Brak|
|database_eDTU_used|EDTU używane|Liczba|Średnia|EDTU używane|DataResourceId|
|storage_used|Wykorzystano miejsce na dane|Bajty|Średnia|Wykorzystano miejsce na dane|Brak|
|database_storage_used|Wykorzystano miejsce na dane|Bajty|Średnia|Wykorzystano miejsce na dane|DataResourceId|
|xtp_storage_percent|Procent pamięci OLTP|Wartość procentowa|Średnia|Procent pamięci OLTP|Brak|
|cpu_limit|Limit procesora|Liczba|Średnia|limit procesora. Dotyczy elastycznych pul opartych na pulach opartych na pulach typu vCore.|Brak|
|database_cpu_limit|Limit procesora|Liczba|Średnia|Limit procesora|DataResourceId|
|cpu_used|Używany procesor|Liczba|Średnia|Używany procesor. Dotyczy elastycznych pul opartych na pulach opartych na pulach typu vCore.|Brak|
|database_cpu_used|Używany procesor|Liczba|Średnia|Używany procesor|DataResourceId|
|sqlserver_process_core_percent|Procent rdzenia procesu programu SQL Server|Wartość procentowa|Maksimum|Procent użycia procesora CPU dla procesu programu SQL Server, mierzony przez system operacyjny. Dotyczy basenów elastycznych.|Brak|
|sqlserver_process_memory_percent|Procent pamięci procesu programu SQL Server|Wartość procentowa|Maksimum|Procent użycia pamięci dla procesu programu SQL Server, mierzony przez system operacyjny. Dotyczy basenów elastycznych.|Brak|
|tempdb_data_size|Kilobajty rozmiaru pliku danych tempdb|Liczba|Maksimum|Kilobajty rozmiaru pliku danych tempdb|Brak|
|tempdb_log_size|Kilobajty rozmiaru pliku dziennika tempdb|Liczba|Maksimum|Kilobajty rozmiaru pliku dziennika tempdb|Brak|
|tempdb_log_used_percent|Używany dziennik procentu tempdb|Wartość procentowa|Maksimum|Używany dziennik procentu tempdb|Brak|
|allocated_data_storage|Przydzielone miejsce na dane|Bajty|Średnia|Przydzielone miejsce na dane|Brak|
|database_allocated_data_storage|Przydzielone miejsce na dane|Bajty|Średnia|Przydzielone miejsce na dane|DataResourceId|
|allocated_data_storage_percent|Procent przydzielonego miejsca na dane|Wartość procentowa|Maksimum|Procent przydzielonego miejsca na dane|Brak|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/serwery

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|dtu_consumption_percent|Procent użycia jednostek DTU|Wartość procentowa|Średnia|Procent użycia jednostek DTU|Elastyczny PulszaResourceId|
|database_dtu_consumption_percent|Procent użycia jednostek DTU|Wartość procentowa|Średnia|Procent użycia jednostek DTU|DataResourceId,ElasticPoolResourceId|
|storage_used|Wykorzystano miejsce na dane|Bajty|Średnia|Wykorzystano miejsce na dane|Elastyczny PulszaResourceId|
|database_storage_used|Wykorzystano miejsce na dane|Bajty|Średnia|Wykorzystano miejsce na dane|DataResourceId,ElasticPoolResourceId|
|dtu_used|Używane DTU|Liczba|Średnia|Używane DTU|DataResourceId|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|virtual_core_count|Liczba rdzeni wirtualnych|Liczba|Średnia|Liczba rdzeni wirtualnych|Brak|
|avg_cpu_percent|Średni procent procesora|Wartość procentowa|Średnia|Średni procent procesora|Brak|
|reserved_storage_mb|Zarezerwowane miejsce do przechowywania|Liczba|Średnia|Zarezerwowane miejsce do przechowywania|Brak|
|storage_space_used_mb|Wykorzystane miejsce do przechowywania|Liczba|Średnia|Wykorzystane miejsce do przechowywania|Brak|
|io_requests|Liczba żądań we/wy|Liczba|Średnia|Liczba żądań we/wy|Brak|
|io_bytes_read|Odczyt bajtów we/wy|Bajty|Średnia|Odczyt bajtów we/wy|Brak|
|io_bytes_written|Bajty we/wy napisane|Bajty|Średnia|Bajty we/wy napisane|Brak|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageKondyje

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|UżywaneCapacity|Używana pojemność|Bajty|Średnia|Pojemność używanego konta|Brak|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru ResponseType dla liczby różnych typów odpowiedzi.|Typ odpowiedzi,Typ geograficzny,Nazwa api,Uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|Milisekund|Średnia|Średnie opóźnienie używane przez usługę Azure Storage do przetwarzania pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci uwzględnionego w wartości AverageE2ELatency.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie E2E dla powodzenia|Milisekund|Średnia|Średnie opóźnienie end-to-end pomyślnych żądań złożonych do usługi magazynu lub określonej operacji interfejsu API w milisekundach. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|Dostępność|Dostępność|Wartość procentowa|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Typ geograficzny,Nazwa api,Uwierzytelnianie|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Magazyn/magazyn firmy MicrosoftKonserwowanie/obiekt usługi

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BlobCapacity (BlobCapacity)|Pojemność obiektu blob|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez usługę obiektów Blob konta magazynu w bajtach.|Typ obiektu blob,warstwa|
|Liczba obiektów BlobCount|Liczba obiektów blob|Liczba|Średnia|Liczba obiektów blob w usłudze obiektów Blob konta magazynu.|Typ obiektu blob,warstwa|
|Liczba kontenerów|Liczba kontenerów obiektów blob|Liczba|Średnia|Liczba kontenerów w usłudze obiektów Blob konta magazynu.|Brak|
|Indeks Zdolności|Pojemność indeksu|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez indeks ADLS Gen2 (hierarchiczny) w bajtach.|Brak|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru ResponseType dla liczby różnych typów odpowiedzi.|Typ odpowiedzi,Typ geograficzny,Nazwa api,Uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|Milisekund|Średnia|Średnie opóźnienie używane przez usługę Azure Storage do przetwarzania pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci uwzględnionego w wartości AverageE2ELatency.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie E2E dla powodzenia|Milisekund|Średnia|Średnie opóźnienie end-to-end pomyślnych żądań złożonych do usługi magazynu lub określonej operacji interfejsu API w milisekundach. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|Dostępność|Dostępność|Wartość procentowa|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Typ geograficzny,Nazwa api,Uwierzytelnianie|

## <a name="microsoftstoragestorageaccountstableservices"></a>Magazyn/magazyn firmy MicrosoftKonserwowanie/tabelaUsługi

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TableCapacity (Zdolność do pońoliczy)|Pojemność tabeli|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez usługę tabel konta magazynu w bajtach.|Brak|
|Liczba tabel|Liczba tabel|Liczba|Średnia|Liczba tabeli w usłudze tabel konta magazynu.|Brak|
|Liczba entity tabeli|Liczba jednostek tabeli|Liczba|Średnia|Liczba jednostek tabeli w usłudze tabel konta magazynu.|Brak|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru ResponseType dla liczby różnych typów odpowiedzi.|Typ odpowiedzi,Typ geograficzny,Nazwa api,Uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|Milisekund|Średnia|Średnie opóźnienie używane przez usługę Azure Storage do przetwarzania pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci uwzględnionego w wartości AverageE2ELatency.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie E2E dla powodzenia|Milisekund|Średnia|Średnie opóźnienie end-to-end pomyślnych żądań złożonych do usługi magazynu lub określonej operacji interfejsu API w milisekundach. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|Dostępność|Dostępność|Wartość procentowa|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Typ geograficzny,Nazwa api,Uwierzytelnianie|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Magazyn microsoft.storage/storageKonta/fileSługi

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Zdolność do pończęcia plików|Pojemność pliku|Bajty|Średnia|Ilość miejsca używanego przez usługę plików konta magazynu w bajtach.|Fileshare|
|Liczba plików|Liczba plików|Liczba|Średnia|Liczba plików w usłudze Plik konta magazynu.|Fileshare|
|Liczba plików ShareCount|Liczba udziałów plików|Liczba|Średnia|Liczba udziałów plików w usłudze Plik konta magazynu.|Brak|
|Konto FileShareSnapshotCount|Liczba migawek udziału plików|Liczba|Średnia|Liczba migawek obecnych w udziale w usłudze plików konta magazynu.|Fileshare|
|Rozmiar plikuShareSnapshotSize|Rozmiar migawki udziału plików|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez migawki w usłudze plików na koncie magazynu w bajtach.|Fileshare|
|FileShareQuota (Polski)|Rozmiar przydziału udziału plików|Bajty|Średnia|Górny limit ilości magazynu, który może być używany przez usługę Azure Files Service w bajtach.|Fileshare|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru ResponseType dla liczby różnych typów odpowiedzi.|Typ odpowiedzi,Typ geograficzny,Nazwa api,Uwierzytelnianie,Udostępnianie pliku|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Typ geograficzny,Nazwa api,Uwierzytelnianie,Udostępnianie plików|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Typ geograficzny,Nazwa api,Uwierzytelnianie,Udostępnianie plików|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|Milisekund|Średnia|Średnie opóźnienie używane przez usługę Azure Storage do przetwarzania pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci uwzględnionego w wartości AverageE2ELatency.|Typ geograficzny,Nazwa api,Uwierzytelnianie,Udostępnianie plików|
|SuccessE2ELatency|Opóźnienie E2E dla powodzenia|Milisekund|Średnia|Średnie opóźnienie end-to-end pomyślnych żądań złożonych do usługi magazynu lub określonej operacji interfejsu API w milisekundach. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Typ geograficzny,Nazwa api,Uwierzytelnianie,Udostępnianie plików|
|Dostępność|Dostępność|Wartość procentowa|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Typ geograficzny,Nazwa api,Uwierzytelnianie,Udostępnianie plików|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Magazyn/magazyn firmy MicrosoftKonserwowanie/kolejkaUsługi

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QueueCapacity (Zdolność do kolejek)|Pojemność kolejki|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez usługę kolejka konta magazynu w bajtach.|Brak|
|Liczba kolejek|Liczba kolejek|Liczba|Średnia|Liczba kolejek w usłudze Kolejka konta magazynu.|Brak|
|KolejkaLiczna liczba|Liczba wiadomości w kolejce|Liczba|Średnia|Przybliżona liczba komunikatów kolejki w usłudze Kolejka konta magazynu.|Brak|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru ResponseType dla liczby różnych typów odpowiedzi.|Typ odpowiedzi,Typ geograficzny,Nazwa api,Uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|Milisekund|Średnia|Średnie opóźnienie używane przez usługę Azure Storage do przetwarzania pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci uwzględnionego w wartości AverageE2ELatency.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie E2E dla powodzenia|Milisekund|Średnia|Średnie opóźnienie end-to-end pomyślnych żądań złożonych do usługi magazynu lub określonej operacji interfejsu API w milisekundach. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Typ geograficzny,Nazwa api,Uwierzytelnianie|
|Dostępność|Dostępność|Wartość procentowa|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Typ geograficzny,Nazwa api,Uwierzytelnianie|





## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/caches

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|KlientIOPS|Całkowita liczba we/wy klienta|Liczba|Średnia|Szybkość operacji plików klienta przetwarzanych przez pamięć podręczną.|Brak|
|KlientLatency|Średnie opóźnienie klienta|Milisekund|Średnia|Średnie opóźnienie operacji plików klienta w pamięci podręcznej magazynu.|Brak|
|ClientReadIOPS (ClientReadIOPS)|Odczyt klienta We/Wy|Połów połówek|Średnia|Operacje odczytu klienta na sekundę.|Brak|
|Przepustowość doczynienia obsługi klienta|Średnia przepływność odczytu pamięci podręcznej|PrzeztówPerSekunda|Średnia|Szybkość przesyłania danych odczytu przez klienta.|Brak|
|KlientWriteIOPS|Zapis klienta We/Wy|Połów połówek|Średnia|Operacje zapisu klienta na sekundę.|Brak|
|KlientWriteThroughput|Średnia przepływność zapisu w pamięci podręcznej|PrzeztówPerSekunda|Średnia|Szybkość transferu danych zapisu klienta.|Brak|
|ClientMetadataReadIOPS (ClientMetadataReadIOPS)|Odczytywanie przez nieprzechodnień we/wy między metadanych klienta|Połów połówek|Średnia|Szybkość operacji na plikach klienta wysyłanych do pamięci podręcznej, z wyłączeniem odczytów danych, które nie modyfikują stanu trwałego.|Brak|
|ClientMetadataWriteIOPS|Zapis iOPS metadanych klienta|Połów połówek|Średnia|Szybkość operacji na plikach klienta wysyłanych do pamięci podręcznej, z wyłączeniem zapisów danych, które modyfikują stan trwały.|Brak|
|ClientLockIOPS|We/wy blokady klienta|Połów połówek|Średnia|Operacje blokowania plików klienta na sekundę.|Brak|
|StorageTargetHealth (Zdrowie celów pamięci masowej)|Kondycja obiektu docelowego magazynu|Liczba|Średnia|Wyniki logiczne testu łączności między pamięcią podręczną a docelowymi magazynami.|Brak|
|Uptime|Uptime|Liczba|Średnia|Wyniki logiczne testu łączności między pamięcią podręczną a systemem monitorowania.|Brak|
|StorageTargetIOPS|Całkowita liczba we/wy dla magazynu|Liczba|Średnia|Szybkość wszystkich operacji plików, które pamięć podręczna wysyła do określonego programu StorageTarget.|Nośnik danych magazynu|
|StorageTargetWriteIOPS|PrzechowywanieTarget Zapisu We/Wy|Liczba|Średnia|Szybkość operacji zapisu pliku, które pamięć podręczna wysyła do określonego programu StorageTarget.|Nośnik danych magazynu|
|PrzechowywanieTargetAsyncWriteThroughput|Przepływność zapisu asynchronitycznego StorageTarget|PrzeztówPerSekunda|Średnia|Szybkość pamięci podręcznej asynchronicznie zapisuje dane do określonego StorageTarget. Są to zapisy oportunistyczne, które nie powodują blokowania klientów.|Nośnik danych magazynu|
|PrzechowywanieTargetSyncWriteThroughput|Synchronicz zapisu StorageTarget|PrzeztówPerSekunda|Średnia|Szybkość pamięci podręcznej synchronicznie zapisuje dane do określonego StorageTarget. Są to zapisy, które powodują, że klienci blokują.|Nośnik danych magazynu|
|StorageTargetTotalWriteThroughput|Całkowita przepływność zapisu w celu zasysanie celu|PrzeztówPerSekunda|Średnia|Całkowita szybkość, że pamięć podręczna zapisuje dane do określonego StorageTarget.|Nośnik danych magazynu|
|Sposób działania celów magazynu|Opóźnienie celu magazynu|Milisekund|Średnia|Średnie opóźnienie podróży w obie strony wszystkich operacji plików, które pamięć podręczna wysyła do partricular StorageTarget.|Nośnik danych magazynu|
|StorageTargetMetadataReadIOPS|Odczyt iops metadanych storagetarget|Połów połówek|Średnia|Szybkość operacji plików, które nie modyfikują stanu trwałego i z wyłączeniem operacji odczytu, która jest wysyłana przez pamięć podręczną do określonego programu StorageTarget.|Nośnik danych magazynu|
|StorageTargetMetadataEeEiteIOPS|Identyfikatory We/Wy dotyczące zapisu metadanych storagetarget|Połów połówek|Średnia|Szybkość operacji plików, które modyfikują stan trwały i z wyłączeniem operacji zapisu, który pamięć podręczna wysyła do określonego StorageTarget.|Nośnik danych magazynu|
|StorageTargetReadIOPS|Odczyt iOPS z odczytem storagetarget|Połów połówek|Średnia|Szybkość operacji odczytu pliku, które pamięć podręczna wysyła do określonego programu StorageTarget.|Nośnik danych magazynu|
|StorageTargetReadAheadThroughput|Przepływność odczytu do odczytu z wyprzedzeniem w celu przechowywania|PrzeztówPerSekunda|Średnia|Szybkość cache oportunistyczny odczytuje dane z StorageTarget.|Nośnik danych magazynu|
|PrzechowywanieTargetFillThroughput|Przepływność wypełniania nastawy dla celów magazynu|PrzeztówPerSekunda|Średnia|Szybkość cache odczytuje dane z StorageTarget do obsługi miss pamięci podręcznej.|Nośnik danych magazynu|
|StorageTargetTotalReadThroughput|Całkowita przepływność odczytu w celu całkowitej dojście do magazynu|PrzeztówPerSekunda|Średnia|Całkowita szybkość, że pamięć podręczna odczytuje dane z określonego StorageTarget.|Nośnik danych magazynu|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Wynik sesji synchronizacji|Liczba|Średnia|Metryka, która rejestruje wartość 1 za każdym razem, gdy punkt końcowy serwera pomyślnie kończy sesję synchronizacji z punktem końcowym chmury|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Pliki zsynchronizowane|Liczba|Łącznie|Liczba zsynchronizowanych plików|SyncGroupName,ServerEndpointName,SyncDirection|
|Pamięć Programu StorageSyncSyncSessionPerItemErrorsCount|Pliki niezsynchronizowane|Liczba|Łącznie|Nie można zsynchronizować liczby plików|SyncGroupName,ServerEndpointName,SyncDirection|
|Pamięć masowa SynchronizcBatchTransferredFileBytes|Bajty zsynchronizowane|Bajty|Łącznie|Całkowity rozmiar pliku przesłany dla sesji synchronizacji|SyncGroupName,ServerEndpointName,SyncDirection|
|Pamięć Pamięci Masowego SerweraHeartbeat|Stan usługi Online serwera|Liczba|Maksimum|Metryka, która rejestruje wartość 1 za każdym razem, gdy serwer resigtered pomyślnie rejestruje bicie serca z punktem końcowym w chmurze|ServerName|
|Pamięć masowaSyncRecallIOTotalSizeBytes|Przywoływanie warstw w chmurze|Bajty|Łącznie|Całkowity rozmiar danych przywoływaniu przez serwer|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Rozmiar odwołania warstwowego chmury|Bajty|Łącznie|Wielkość przywoływania danych|Nazwa syncgroup,Nazwa serwera|
|StorageSyncRecallThroughPutBytesPerSecond|Przepływność wycofywania warstwowego w chmurze|PrzeztówPerSekunda|Średnia|Rozmiar przepływności wycofywania danych|Nazwa syncgroup,Nazwa serwera|
|StorageSyncRecalledNetworkBytesApplication|Rozmiar odwołania warstwowego w chmurze według aplikacji|Bajty|Łącznie|Rozmiar danych przywoływaniu przez aplikację|Nazwa syncgroup,Nazwa serwera,Nazwa aplikacji|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|Pliki zsynchronizowane|Liczba|Łącznie|Liczba zsynchronizowanych plików|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Pliki niezsynchronizowane|Liczba|Łącznie|Nie można zsynchronizować liczby plików|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupBatchTransferredFileBytes|Bajty zsynchronizowane|Bajty|Łącznie|Całkowity rozmiar pliku przesłany dla sesji synchronizacji|SyncGroupName,ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|Pliki zsynchronizowane|Liczba|Łącznie|Liczba zsynchronizowanych plików|ServerEndpointName,SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Pliki niezsynchronizowane|Liczba|Łącznie|Nie można zsynchronizować liczby plików|ServerEndpointName,SyncDirection|
|ServerEndpointBatchTransferredFileBytes|Bajty zsynchronizowane|Bajty|Łącznie|Całkowity rozmiar pliku przesłany dla sesji synchronizacji|ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ServerHeartbeat (ServerHeartbeat)|Stan usługi Online serwera|Liczba|Maksimum|Metryka, która rejestruje wartość 1 za każdym razem, gdy serwer resigtered pomyślnie rejestruje bicie serca z punktem końcowym w chmurze|ServerResourceId,Nazwa serwera|
|ServerRecallIOTotalSizeBytes|Przywoływanie warstw w chmurze|Bajty|Łącznie|Całkowity rozmiar danych przywoływaniu przez serwer|ServerResourceId,Nazwa serwera|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalityka/szybkość przesyłania strumieniowego

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Alokacja zasobów|Wykorzystanie SU %|Wartość procentowa|Maksimum|Wykorzystanie SU %|Nazwa logiczna,Identyfikator partycji|
|WejściaUchny|Zdarzenia wejściowe|Liczba|Łącznie|Zdarzenia wejściowe|Nazwa logiczna,Identyfikator partycji|
|InputEventBytes (WejściaEwentnebyty)|Bajty zdarzenia wejściowego|Bajty|Łącznie|Bajty zdarzenia wejściowego|Nazwa logiczna,Identyfikator partycji|
|LateInputEvents|Zdarzenia późnego wprowadzania|Liczba|Łącznie|Zdarzenia późnego wprowadzania|Nazwa logiczna,Identyfikator partycji|
|Dane wyjścioweWzdowy|Zdarzenia wyjściowe|Liczba|Łącznie|Zdarzenia wyjściowe|Nazwa logiczna,Identyfikator partycji|
|KonwersjeErrors|Błędy konwersji danych|Liczba|Łącznie|Błędy konwersji danych|Nazwa logiczna,Identyfikator partycji|
|Errors|Błędy środowiska uruchomieniowego|Liczba|Łącznie|Błędy środowiska uruchomieniowego|Nazwa logiczna,Identyfikator partycji|
|DroppedOrAdjustedWydarzenia|Zdarzenia poza kolejnością|Liczba|Łącznie|Zdarzenia poza kolejnością|Nazwa logiczna,Identyfikator partycji|
|AMLCalloutZasuty|Żądania funkcji|Liczba|Łącznie|Żądania funkcji|Nazwa logiczna,Identyfikator partycji|
|AMLCalloutFailedZasty|Nieudane żądania funkcji|Liczba|Łącznie|Nieudane żądania funkcji|Nazwa logiczna,Identyfikator partycji|
|AMLCalloutInputAvents|Zdarzenia funkcji|Liczba|Łącznie|Zdarzenia funkcji|Nazwa logiczna,Identyfikator partycji|
|DeserializacjaError|Błędy deserializacji danych wejściowych|Liczba|Łącznie|Błędy deserializacji danych wejściowych|Nazwa logiczna,Identyfikator partycji|
|EarlyInputEvents|Zdarzenia wczesnego wprowadzania|Liczba|Łącznie|Zdarzenia wczesnego wprowadzania|Nazwa logiczna,Identyfikator partycji|
|WyjścieWodawodnikDelaySekundy|Opóźnienie znaku wodnego|Sekundy|Maksimum|Opóźnienie znaku wodnego|Nazwa logiczna,Identyfikator partycji|
|Dane wejściaEventsSourcesBacklogged|Zaległe zdarzenia wejściowe|Liczba|Maksimum|Zaległe zdarzenia wejściowe|Nazwa logiczna,Identyfikator partycji|
|Dane wejścioweSourcesPerSecond|Odebrane źródła wejściowe|Liczba|Łącznie|Odebrane źródła wejściowe|Nazwa logiczna,Identyfikator partycji|

## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/obszary robocze

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AranżacjaPipelineRunsEnded|Zakończono przebiegi rurociągu|Liczba|Łącznie|Liczba uruchomień potoku aranżacji, które zakończyły się pomyślnie, nie powiodły się lub zostały anulowane|Wynik,Typ błędu,Potok|
|AranżacjaActivityRunsEnded|Zakończono przebiegi aktywności|Liczba|Łącznie|Liczba działań aranżacji, które zakończyły się pomyślnie, nie powiodły się lub zostały anulowane|Wynik,Typ błędu,Działanie,Typ działania,Potok|
|AranżacjaTriggersEnded|Wyzwalacze zakończone|Liczba|Łącznie|Liczba wyzwalaczy aranżacji, które zakończyły się pomyślnie, nie powiodły się lub zostały anulowane|Wynik,Typ błędu,Wyzwalacz|
|SQLOnDemandLoginPróbuje|Próby logowania|Liczba|Łącznie|Liczba prób logowania, które zakończyły się powodzeniem lub nie powiodły się|Wynik|
|SQLOnDemandQueriesEnded|Zakończyły się kwerendy|Liczba|Łącznie|Liczba kwerend, które zakończyły się pomyślnie, nie powiodły się lub zostały anulowane|Wynik|
|SQLOnDemandQueryProcessedBytes|Przetwarzane dane|Bajty|Łącznie|Ilość danych przetwarzanych przez kwerendy|Brak|

## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/obszary robocze/duże puli danych

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SparkJobsEnded (SparkJobsEnded)|Wnioski zakończone|Liczba|Łącznie|Liczba zakończonych wniosków|Typ zadania,Wynik zadania|
|RdzenieCapacity|Pojemność rdzeni|Liczba|Maksimum|Pojemność rdzeni|Brak|
|MemoryCapacityGB|Pojemność pamięci (GB)|Liczba|Maksimum|Pojemność pamięci (GB)|Brak|

## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/obszary robocze/sqlPools

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|DWULimit|Limit DWU|Liczba|Maksimum|Cel poziomu usług puli SQL|Brak|
|DWUUżytowany|DWU używany|Liczba|Maksimum|Reprezentuje reprezentację wysokiego poziomu użycia w puli SQL. Mierzona przez limit DWU * PROCENT DWU|Brak|
|DWUUsedPercent|DWU używany procent|Wartość procentowa|Maksimum|Reprezentuje reprezentację wysokiego poziomu użycia w puli SQL. Mierzona przez użycie maksymalnej wartości procentowej między procentem procesora a procentem we/wy danych|Brak|
|PołączeniaBlockedByFirewall|Połączenia zablokowane przez zaporę|Liczba|Łącznie|Liczba połączeń zablokowanych przez reguły zapory. Ponowne przeglądanie zasad kontroli dostępu dla puli SQL i monitorowanie tych połączeń, jeśli liczba jest wysoka|Brak|
|AdaptiveCacheHitPercent|Adaptacyjna wartość procentowa trafień pamięci podręcznej|Wartość procentowa|Maksimum|Mierzy, jak dobrze obciążenia wykorzystują adaptacyjną pamięć podręczną. Użyj tej metryki z metryką procentową trafienia w pamięci podręcznej, aby określić, czy mają być skalowane dla dodatkowej pojemności, czy ponownie uruchomić obciążenia, aby nawilżyć pamięć podręczną|Brak|
|AdaptiveCacheUsedPercent|Adaptacyjna pamięć podręczna używana procentowa|Wartość procentowa|Maksimum|Mierzy, jak dobrze obciążenia wykorzystują adaptacyjną pamięć podręczną. Użyj tej metryki z metryką procentową użytą w pamięci podręcznej, aby określić, czy skalować dodatkowe obciążenia pojemnościowe lub ponownie nawilżać pamięć podręczną, aby nawilżyć pamięć podręczną|Brak|
|LocalTempDBUsedPercent|Lokalny tempdb używany procent|Wartość procentowa|Maksimum|Lokalne wykorzystanie bazy danych tempdb we wszystkich węzłach obliczeniowych — wartości są emitowane co pięć minut|Brak|
|MemoryUsedPercent|Wartość procentowa używanej pamięci|Wartość procentowa|Maksimum|Wykorzystanie pamięci we wszystkich węzłach w puli SQL|Brak|
|Połączenia|Połączenia|Liczba|Łącznie|Liczba całkowitych logowań do puli SQL|Wynik|
|WLGActiveQueries (WLGActiveQueries)|Aktywne kwerendy grupy obciążenia|Liczba|Łącznie|Aktywne zapytania w grupie obciążenia. Za pomocą tej metryki niefiltrowane i nieskładka wyświetla wszystkie aktywne zapytania uruchomione w systemie|IsUserDefiniowany,WorkloadGroup|
|WLGActiveQueriesTimeouts|Limity czasu kwerend grupy obciążenia|Liczba|Łącznie|Zapytania dla grupy obciążenia, które zostały przesunął limit czasu. Limity czasu kwerendy zgłoszone przez tę metrykę są tylko wtedy, gdy kwerenda rozpoczęła wykonywanie (nie obejmuje czasu oczekiwania z powodu blokowania lub oczekiwania zasobów)|IsUserDefiniowany,WorkloadGroup|
|WLGAllocationBySystemPercent|Alokacja grupy obciążenia według procentu systemu|Wartość procentowa|Maksimum|Procent alokacji zasobów w stosunku do całego systemu|IsUserDefiniowany,WorkloadGroup|
|WLGAllocationByMaxResourcePercent|Alokacja grupy obciążenia według maksymalnego procentu zasobów|Wartość procentowa|Maksimum|Wyświetla procent alokacji zasobów względem procentu zasobu Efektywnego limitu na grupę obciążenia. Ta metryka zapewnia efektywne wykorzystanie grupy obciążenia|IsUserDefiniowany,WorkloadGroup|
|WLGEffectiveCapResourcePercent|Efektywny procent zasobów cap|Wartość procentowa|Maksimum|Procent zasobu efektywnego limitu dla grupy obciążenia. Jeśli istnieją inne grupy obciążenia z min_percentage_resource > 0, effective_cap_percentage_resource jest obniżana proporcjonalnie|IsUserDefiniowany,WorkloadGroup|
|wlg_effective_min_resource_percent|Efektywny procent minimalnego zasobu|Wartość procentowa|Minimalne|Efektywne ustawienie procentowe minimalnego zasobu jest dozwolone, biorąc pod uwagę poziom usługi i ustawienia grupy obciążenia. Efektywny min_percentage_resource można regulować na niższych poziomach usług|IsUserDefiniowany,WorkloadGroup|
|WLGQueuedQueries|Kwerendy w kolejce grupy obciążeń|Liczba|Łącznie|Skumulowana liczba żądań w kolejce po osiągnięciu maksymalnego limitu współbieżności|IsUserDefiniowany,WorkloadGroup|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|IngressReceivedMessages (IngressReceivedMessages)|Odebrane wiadomości przychodzące|Liczba|Łącznie|Liczba wiadomości odczytywanych ze wszystkich źródeł zdarzeń Centrum zdarzeń usługi Event hub lub IoT Hub|Brak|
|IngressReceivedInvalidMessages|Odebrane nieprawidłowe wiadomości przychodzące|Liczba|Łącznie|Liczba nieprawidłowych wiadomości odczytywanych ze wszystkich źródeł zdarzeń Centrum zdarzeń usługi Event Hub lub IoT Hub|Brak|
|IngressReceivedBytes (Wnikanie)|Odebrane bajty przychodzące|Bajty|Łącznie|Liczba bajtów odczytanych ze wszystkich źródeł zdarzeń|Brak|
|IngressStoredBytes (WnikajsiestoredBytes)|Bajty przechowywane przy ruchu przychodzącego|Bajty|Łącznie|Całkowity rozmiar zdarzeń pomyślnie przetworzonych i dostępnych dla kwerendy|Brak|
|IngressStoredEvents|Zdarzenia przechowywane w ruchu przychodzącym|Liczba|Łącznie|Liczba spłaszczonych zdarzeń pomyślnie przetworzonych i dostępnych dla kwerendy|Brak|
|IngressReceivedMessagesTimeLag|Opóźnienie czasu odebranych wiadomości przychodzących|Sekundy|Maksimum|Różnica między czasem, w której wiadomość jest ujmowana w kolejce do źródła zdarzenia, a czasem przetwarzania w u danych przychodzących|Brak|
|IngressReceivedMessagesCountLag|Opóźnienie liczby odebranych wiadomości przychodzących|Liczba|Średnia|Różnica między numerem sekwencyjnym ostatniej wiadomości w kolejce w partycji źródłowej zdarzenia a numerem sekwencyjnym komunikatów przetwarzanych w u danych przychodzących|Brak|
|Właściwości WarmStorageMaxProperties|Właściwości maksymalnej pamięci masowej|Liczba|Maksimum|Maksymalna liczba właściwości używanych przez środowisko dla SKU S1/S2 i maksymalna liczba właściwości dozwolona przez Ciepły magazyn dla payg sku|Brak|
|WarmStorageUżywanewłaściwości|Właściwości używane do ciepłej pamięci masowej |Liczba|Maksimum|Liczba właściwości używanych przez środowisko dla jednostki SKU S1/S2 i liczba właściwości używanych przez Warm Store dla payg sku|Brak|



## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|IngressReceivedMessages (IngressReceivedMessages)|Odebrane wiadomości przychodzące|Liczba|Łącznie|Liczba wiadomości odczytanych ze źródła zdarzeń|Brak|
|IngressReceivedInvalidMessages|Odebrane nieprawidłowe wiadomości przychodzące|Liczba|Łącznie|Liczba nieprawidłowych wiadomości odczytanych ze źródła zdarzeń|Brak|
|IngressReceivedBytes (Wnikanie)|Odebrane bajty przychodzące|Bajty|Łącznie|Liczba bajtów odczytanych ze źródła zdarzeń|Brak|
|IngressStoredBytes (WnikajsiestoredBytes)|Bajty przechowywane przy ruchu przychodzącego|Bajty|Łącznie|Całkowity rozmiar zdarzeń pomyślnie przetworzonych i dostępnych dla kwerendy|Brak|
|IngressStoredEvents|Zdarzenia przechowywane w ruchu przychodzącym|Liczba|Łącznie|Liczba spłaszczonych zdarzeń pomyślnie przetworzonych i dostępnych dla kwerendy|Brak|
|IngressReceivedMessagesTimeLag|Opóźnienie czasu odebranych wiadomości przychodzących|Sekundy|Maksimum|Różnica między czasem, w której wiadomość jest ujmowana w kolejce do źródła zdarzenia, a czasem przetwarzania w u danych przychodzących|Brak|
|IngressReceivedMessagesCountLag|Opóźnienie liczby odebranych wiadomości przychodzących|Liczba|Średnia|Różnica między numerem sekwencyjnym ostatniej wiadomości w kolejce w partycji źródłowej zdarzenia a numerem sekwencyjnym komunikatów przetwarzanych w u danych przychodzących|Brak|
|Właściwości WarmStorageMaxProperties|Właściwości maksymalnej pamięci masowej|Liczba|Maksimum|Maksymalna liczba właściwości używanych przez środowisko dla SKU S1/S2 i maksymalna liczba właściwości dozwolona przez Ciepły magazyn dla payg sku|Brak|
|WarmStorageUżywanewłaściwości|Właściwości używane do ciepłej pamięci masowej |Liczba|Maksimum|Liczba właściwości używanych przez środowisko dla jednostki SKU S1/S2 i liczba właściwości używanych przez Warm Store dla payg sku|Brak|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Bajty odczytu dysku/s|PrzeztówPerSekunda|Średnia|Średnia przepustowość dysku z powodu operacji odczytu w okresie próbkowania.|Brak|
|DiskWriteBytesPerSekund|Bajty zapisu dysku/s|PrzeztówPerSekunda|Średnia|Średnia przepływność dysku z powodu operacji zapisu w okresie próbkowania.|Brak|
|Bajty odczytu dysku|Bajty odczytu dysku|Bajty|Łącznie|Całkowita przepustowość dysku z powodu operacji odczytu w okresie próbkowania.|Brak|
|Bajty zapisu dysku|Bajty zapisu dysku|Bajty|Łącznie|Całkowita przepływność dysku z powodu operacji zapisu w okresie próbkowania.|Brak|
|DiskReadOperations (Działania diskread)|Operacje odczytu dysku|Liczba|Łącznie|Liczba operacji odczytu we/wy w poprzednim okresie próbkowania. Należy zauważyć, że te operacje mogą mieć zmienny rozmiar.|Brak|
|Działania diskwrite|Operacje zapisu dysku|Liczba|Łącznie|Liczba operacji zapisu we/wy w poprzednim okresie próbkowania. Należy zauważyć, że te operacje mogą mieć zmienny rozmiar.|Brak|
|Operacje odczytu dysku/s|Operacje odczytu dysku/s|Połów połówek|Średnia|Średnia liczba operacji odczytu we/wy w poprzednim okresie próby. Należy zauważyć, że te operacje mogą mieć zmienny rozmiar.|Brak|
|Operacje zapisu dysku/s|Operacje zapisu dysku/s|Połów połówek|Średnia|Średnia liczba operacji zapisu we/wy w poprzednim okresie próbkowania. Należy zauważyć, że te operacje mogą mieć zmienny rozmiar.|Brak|
|DiskReadLatency (DiskReadLatency)|Opóźnienie odczytu dysku|Milisekund|Średnia|Całkowite opóźnienie odczytu. Suma opóźnień odczytu urządzenia i jądra.|Brak|
|DiskWriteLatency (Właswoenie dysku)|Opóźnienie zapisu dysku|Milisekund|Średnia|Całkowite opóźnienie zapisu. Suma opóźnień zapisu urządzenia i jądra.|Brak|
|NetworkInBytesPerSecond|Sieć w bajtach/s|PrzeztówPerSekunda|Średnia|Średnia przepustowość sieci dla odebranego ruchu.|Brak|
|NetworkOutBytesPerSecond|Bajty wyjścia sieci/s|PrzeztówPerSekunda|Średnia|Średnia przepustowość sieci dla ruchu przesyłanych.|Brak|
|Sieć — wejście|Sieć — wejście|Bajty|Łącznie|Całkowita przepustowość sieci dla odebranego ruchu.|Brak|
|Sieć — wyjście|Sieć — wyjście|Bajty|Łącznie|Całkowita przepustowość sieci dla przesyłu przesyłanym.|Brak|
|Użycie pamięci|Używana pamięć|Bajty|Średnia|Ilość pamięci komputera, która jest używana przez maszynę wirtualną.|Brak|
|PamięćGranted|Przyznano pamięć|Bajty|Średnia|Ilość pamięci, która została przyznana maszynie Wirtualnej przez hosta. Pamięć nie jest przyznawana do hosta, dopóki nie zostanie dotknięty jeden raz i przyznane pamięci mogą być zamienione lub balonem od, jeśli VMkernel potrzebuje pamięci.|Brak|
|PamięćAktywna|Pamięć aktywna|Bajty|Średnia|Ilość pamięci używanej przez maszynę wirtualną w ciągu ostatnich małych przedział czasu. Jest to "true" liczba, ile pamięci maszyny Wirtualnej obecnie potrzebuje. Dodatkowa, nieużywana pamięć może zostać zamieniona lub odbita bez wpływu na wydajność gościa.|Brak|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Wartość procentowa|Średnia|Wykorzystanie procesora CPU. Ta wartość jest raportowana w 100% reprezentującym wszystkie rdzenie procesora w systemie. Na przykład 2-drożna maszyna wirtualna używająca 50% systemu czterordzeniowego całkowicie używa dwóch rdzeni.|Brak|
|PercentageCpuReady (ProcentcpuReady)|Procentowa gotowość procesora|Milisekund|Łącznie|Czas gotowości to czas oczekiwania na udostępnienie procesorów CPU w poprzednim przedziale aktualizacji.|Brak|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/farmy serwerów

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuPercentage (CpuPercentage)|Procent użycia procesora CPU|Wartość procentowa|Średnia|Procent użycia procesora CPU|Wystąpienie|
|MemoryPercentage (PamięćPrzestńczliwienie)|Procent pamięci|Wartość procentowa|Średnia|Procent pamięci|Wystąpienie|
|Funkcja DiskQueueLength|Długość kolejki dysku|Liczba|Średnia|Długość kolejki dysku|Wystąpienie|
|Funkcja HttpQueueLength|Długość kolejki http|Liczba|Średnia|Długość kolejki http|Wystąpienie|
|Bytesreceived|Dane w|Bajty|Łącznie|Dane w|Wystąpienie|
|Bajtów|Wyjście danych|Bajty|Łącznie|Wyjście danych|Wystąpienie|
|TcpSynsent (wytłk, w tym)|Wysłane syn TCP|Liczba|Średnia|Wysłane syn TCP|Wystąpienie|
|TcpSynReceived|Odebrano syn TCP|Liczba|Średnia|Odebrano syn TCP|Wystąpienie|
|TcpNaestablishowany|TCP ustanowiony|Liczba|Średnia|TCP ustanowiony|Wystąpienie|
|TcpFinWait1 (Wacik.|TCP Fin Czekaj 1|Liczba|Średnia|TCP Fin Czekaj 1|Wystąpienie|
|TcpFinWait2 (Wacik.|TCP Fin Czekaj 2|Liczba|Średnia|TCP Fin Czekaj 2|Wystąpienie|
|TcpClosing (Nie ma)|Zamykanie protokołu TCP|Liczba|Średnia|Zamykanie protokołu TCP|Wystąpienie|
|TcpCloseWait (TcpCloseWait)|Oczekiwanie zamknięcia protokołu TCP|Liczba|Średnia|Oczekiwanie zamknięcia protokołu TCP|Wystąpienie|
|TcpLastAck (TcpLastAck)|TCP Ostatni Ack|Liczba|Średnia|TCP Ostatni Ack|Wystąpienie|
|TcpTimeWait (TcpTimeWait)|Oczekiwanie czasu TCP|Liczba|Średnia|Oczekiwanie czasu TCP|Wystąpienie|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (z wyłączeniem funkcji) 

> [!NOTE]
> **Użycie systemu plików** to nowa metryka wdrażana globalnie, nie oczekuje się żadnych danych, chyba że zostałasz na białej liście do prywatnej wersji zapoznawczej.

> [!IMPORTANT]
> **Średni czas odpowiedzi** zostanie przestarzały, aby uniknąć pomyłek z agregacjami metryk. Użyj **czasu odpowiedzi** jako zamiennika.

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuTime (Czas procesora)|Czas procesora|Sekundy|Łącznie|Czas procesora|Wystąpienie|
|Żądania|Żądania|Liczba|Łącznie|Żądania|Wystąpienie|
|Bytesreceived|Dane w|Bajty|Łącznie|Dane w|Wystąpienie|
|Bajtów|Wyjście danych|Bajty|Łącznie|Wyjście danych|Wystąpienie|
|Http101|Http 101|Liczba|Łącznie|Http 101|Wystąpienie|
|Protokół Http2xx|Http 2xx|Liczba|Łącznie|Http 2xx|Wystąpienie|
|Protokół Http3xx|Http 3xx|Liczba|Łącznie|Http 3xx|Wystąpienie|
|Http401|Http 401|Liczba|Łącznie|Http 401|Wystąpienie|
|Http403|Http 403|Liczba|Łącznie|Http 403|Wystąpienie|
|Http404|Http 404|Liczba|Łącznie|Http 404|Wystąpienie|
|Http406|Http 406|Liczba|Łącznie|Http 406|Wystąpienie|
|Protokół Http4xx (polski)|Http 4xx|Liczba|Łącznie|Http 4xx|Wystąpienie|
|Protokół Http5xx|Błędy serwera http|Liczba|Łącznie|Błędy serwera http|Wystąpienie|
|Zestaw pamięci|Zestaw roboczy pamięci|Bajty|Średnia|Zestaw roboczy pamięci|Wystąpienie|
|ŚredniaZapraca Zestaw|Zestaw pracy średniej pamięci|Bajty|Średnia|Zestaw pracy średniej pamięci|Wystąpienie|
|Czas reakcji|Czas reakcji|Sekundy|Łącznie|Czas reakcji|Wystąpienie|
|ŚredniaResponseTime|Średni czas odpowiedzi (przestarzały)|Sekundy|Średnia|Średni czas reakcji|Wystąpienie|
|Połączenia z aplikacjami|Połączenia|Liczba|Średnia|Połączenia|Wystąpienie|
|Handles|Liczba dojść|Liczba|Średnia|Liczba dojść|Wystąpienie|
|Wątki|Liczba wątków|Liczba|Średnia|Liczba wątków|Wystąpienie|
|Bajty prywatne|Bajty prywatne|Bajty|Średnia|Bajty prywatne|Wystąpienie|
|IoReadBytesPerSekund|Bajty odczytu we/wy na sekundę|PrzeztówPerSekunda|Łącznie|Bajty odczytu we/wy na sekundę|Wystąpienie|
|IoWriteBytesPerSekund|Bajty zapisu we/wy na sekundę|PrzeztówPerSekunda|Łącznie|Bajty zapisu we/wy na sekundę|Wystąpienie|
|IoOtherBytesPerSekund|IO inne bajty na sekundę|PrzeztówPerSekunda|Łącznie|IO inne bajty na sekundę|Wystąpienie|
|IoReadOperationsPerSecond|Operacje odczytu we/wy na sekundę|PrzeztówPerSekunda|Łącznie|Operacje odczytu we/wy na sekundę|Wystąpienie|
|IoWriteOperationsPerSecond|Operacje zapisu we/wy na sekundę|PrzeztówPerSekunda|Łącznie|Operacje zapisu we/wy na sekundę|Wystąpienie|
|IoOtherOperationsPerSecond|Inne operacje we/wy na sekundę|PrzeztówPerSekunda|Łącznie|Inne operacje we/wy na sekundę|Wystąpienie|
|RequestsInApplicationQueue|Żądania w kolejce aplikacji|Liczba|Średnia|Żądania w kolejce aplikacji|Wystąpienie|
|CurrentAssemblies|Bieżące złożenia|Liczba|Średnia|Bieżące złożenia|Wystąpienie|
|TotalAppDomains (TotalAppDomains)|Łączna liczba domen aplikacji|Liczba|Średnia|Łączna liczba domen aplikacji|Wystąpienie|
|TotalAppDomainsNiezaładkłano|Całkowita liczba niezaładowanych domen aplikacji|Liczba|Średnia|Całkowita liczba niezaładowanych domen aplikacji|Wystąpienie|
|Gen0Kolekcje|Odpady odśmieskowe Gen 0|Liczba|Łącznie|Odpady odśmieskowe Gen 0|Wystąpienie|
|Gen1Kolekcje|Wyrzucanie elementów bezużytecznych gen 1|Liczba|Łącznie|Wyrzucanie elementów bezużytecznych gen 1|Wystąpienie|
|Gen2Kolekcje|Odpady odśmieskowe gen 2|Liczba|Łącznie|Odpady odśmieskowe gen 2|Wystąpienie|
|Stan zdrowiaSeksus|Stan kontroli kondycji|Liczba|Średnia|Stan kontroli kondycji|Wystąpienie|
|System PlikówUsage|Użycie systemu plików|Bajty|Średnia|Użycie systemu plików|Brak|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funkcje)

> [!NOTE]
> **Użycie systemu plików** to nowa metryka wdrażana globalnie, nie oczekuje się żadnych danych, chyba że zostałasz na białej liście do prywatnej wersji zapoznawczej.

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Bytesreceived|Dane w|Bajty|Łącznie|Dane w|Wystąpienie|
|Bajtów|Wyjście danych|Bajty|Łącznie|Wyjście danych|Wystąpienie|
|Protokół Http5xx|Błędy serwera http|Liczba|Łącznie|Błędy serwera http|Wystąpienie|
|Zestaw pamięci|Zestaw roboczy pamięci|Bajty|Średnia|Zestaw roboczy pamięci|Wystąpienie|
|ŚredniaZapraca Zestaw|Zestaw pracy średniej pamięci|Bajty|Średnia|Zestaw pracy średniej pamięci|Wystąpienie|
|Jednostki funkcyjne|Jednostki wykonawcze funkcji|MB / Milisekundy|Łącznie|[Jednostki wykonawcze funkcji](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Wystąpienie|
|FunkcjaWykonaniaLicznik|Liczba wykonania funkcji|Liczba|Łącznie|Liczba wykonania funkcji|Wystąpienie|
|Bajty prywatne|Bajty prywatne|Bajty|Średnia|Bajty prywatne|Wystąpienie|
|IoReadBytesPerSekund|Bajty odczytu we/wy na sekundę|PrzeztówPerSekunda|Łącznie|Bajty odczytu we/wy na sekundę|Wystąpienie|
|IoWriteBytesPerSekund|Bajty zapisu we/wy na sekundę|PrzeztówPerSekunda|Łącznie|Bajty zapisu we/wy na sekundę|Wystąpienie|
|IoOtherBytesPerSekund|IO inne bajty na sekundę|PrzeztówPerSekunda|Łącznie|IO inne bajty na sekundę|Wystąpienie|
|IoReadOperationsPerSecond|Operacje odczytu we/wy na sekundę|PrzeztówPerSekunda|Łącznie|Operacje odczytu we/wy na sekundę|Wystąpienie|
|IoWriteOperationsPerSecond|Operacje zapisu we/wy na sekundę|PrzeztówPerSekunda|Łącznie|Operacje zapisu we/wy na sekundę|Wystąpienie|
|IoOtherOperationsPerSecond|Inne operacje we/wy na sekundę|PrzeztówPerSekunda|Łącznie|Inne operacje we/wy na sekundę|Wystąpienie|
|RequestsInApplicationQueue|Żądania w kolejce aplikacji|Liczba|Średnia|Żądania w kolejce aplikacji|Wystąpienie|
|CurrentAssemblies|Bieżące złożenia|Liczba|Średnia|Bieżące złożenia|Wystąpienie|
|TotalAppDomains (TotalAppDomains)|Łączna liczba domen aplikacji|Liczba|Średnia|Łączna liczba domen aplikacji|Wystąpienie|
|TotalAppDomainsNiezaładkłano|Całkowita liczba niezaładowanych domen aplikacji|Liczba|Średnia|Całkowita liczba niezaładowanych domen aplikacji|Wystąpienie|
|Gen0Kolekcje|Odpady odśmieskowe Gen 0|Liczba|Łącznie|Odpady odśmieskowe Gen 0|Wystąpienie|
|Gen1Kolekcje|Wyrzucanie elementów bezużytecznych gen 1|Liczba|Łącznie|Wyrzucanie elementów bezużytecznych gen 1|Wystąpienie|
|Gen2Kolekcje|Odpady odśmieskowe gen 2|Liczba|Łącznie|Odpady odśmieskowe gen 2|Wystąpienie|
|Stan zdrowiaSeksus|Stan kontroli kondycji|Liczba|Średnia|Stan kontroli kondycji|Wystąpienie|
|System PlikówUsage|Użycie systemu plików|Bajty|Średnia|Użycie systemu plików|Brak|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuTime (Czas procesora)|Czas procesora|Sekundy|Łącznie|Czas procesora|Wystąpienie|
|Żądania|Żądania|Liczba|Łącznie|Żądania|Wystąpienie|
|Bytesreceived|Dane w|Bajty|Łącznie|Dane w|Wystąpienie|
|Bajtów|Wyjście danych|Bajty|Łącznie|Wyjście danych|Wystąpienie|
|Http101|Http 101|Liczba|Łącznie|Http 101|Wystąpienie|
|Protokół Http2xx|Http 2xx|Liczba|Łącznie|Http 2xx|Wystąpienie|
|Protokół Http3xx|Http 3xx|Liczba|Łącznie|Http 3xx|Wystąpienie|
|Http401|Http 401|Liczba|Łącznie|Http 401|Wystąpienie|
|Http403|Http 403|Liczba|Łącznie|Http 403|Wystąpienie|
|Http404|Http 404|Liczba|Łącznie|Http 404|Wystąpienie|
|Http406|Http 406|Liczba|Łącznie|Http 406|Wystąpienie|
|Protokół Http4xx (polski)|Http 4xx|Liczba|Łącznie|Http 4xx|Wystąpienie|
|Protokół Http5xx|Błędy serwera http|Liczba|Łącznie|Błędy serwera http|Wystąpienie|
|Zestaw pamięci|Zestaw roboczy pamięci|Bajty|Średnia|Zestaw roboczy pamięci|Wystąpienie|
|ŚredniaZapraca Zestaw|Zestaw pracy średniej pamięci|Bajty|Średnia|Zestaw pracy średniej pamięci|Wystąpienie|
|ŚredniaResponseTime|Średni czas reakcji|Sekundy|Średnia|Średni czas reakcji|Wystąpienie|
|Funkcja HttpResponseTime|Czas reakcji|Sekundy|Średnia|Czas reakcji|Wystąpienie|
|Jednostki funkcyjne|Jednostki wykonawcze funkcji|Liczba|Łącznie|Jednostki wykonawcze funkcji|Wystąpienie|
|FunkcjaWykonaniaLicznik|Liczba wykonania funkcji|Liczba|Łącznie|Liczba wykonania funkcji|Wystąpienie|
|Połączenia z aplikacjami|Połączenia|Liczba|Średnia|Połączenia|Wystąpienie|
|Handles|Liczba dojść|Liczba|Średnia|Liczba dojść|Wystąpienie|
|Wątki|Liczba wątków|Liczba|Średnia|Liczba wątków|Wystąpienie|
|Bajty prywatne|Bajty prywatne|Bajty|Średnia|Bajty prywatne|Wystąpienie|
|IoReadBytesPerSekund|Bajty odczytu we/wy na sekundę|PrzeztówPerSekunda|Łącznie|Bajty odczytu we/wy na sekundę|Wystąpienie|
|IoWriteBytesPerSekund|Bajty zapisu we/wy na sekundę|PrzeztówPerSekunda|Łącznie|Bajty zapisu we/wy na sekundę|Wystąpienie|
|IoOtherBytesPerSekund|IO inne bajty na sekundę|PrzeztówPerSekunda|Łącznie|IO inne bajty na sekundę|Wystąpienie|
|IoReadOperationsPerSecond|Operacje odczytu we/wy na sekundę|PrzeztówPerSekunda|Łącznie|Operacje odczytu we/wy na sekundę|Wystąpienie|
|IoWriteOperationsPerSecond|Operacje zapisu we/wy na sekundę|PrzeztówPerSekunda|Łącznie|Operacje zapisu we/wy na sekundę|Wystąpienie|
|IoOtherOperationsPerSecond|Inne operacje we/wy na sekundę|PrzeztówPerSekunda|Łącznie|Inne operacje we/wy na sekundę|Wystąpienie|
|RequestsInApplicationQueue|Żądania w kolejce aplikacji|Liczba|Średnia|Żądania w kolejce aplikacji|Wystąpienie|
|CurrentAssemblies|Bieżące złożenia|Liczba|Średnia|Bieżące złożenia|Wystąpienie|
|TotalAppDomains (TotalAppDomains)|Łączna liczba domen aplikacji|Liczba|Średnia|Łączna liczba domen aplikacji|Wystąpienie|
|TotalAppDomainsNiezaładkłano|Całkowita liczba niezaładowanych domen aplikacji|Liczba|Średnia|Całkowita liczba niezaładowanych domen aplikacji|Wystąpienie|
|Gen0Kolekcje|Odpady odśmieskowe Gen 0|Liczba|Łącznie|Odpady odśmieskowe Gen 0|Wystąpienie|
|Gen1Kolekcje|Wyrzucanie elementów bezużytecznych gen 1|Liczba|Łącznie|Wyrzucanie elementów bezużytecznych gen 1|Wystąpienie|
|Gen2Kolekcje|Odpady odśmieskowe gen 2|Liczba|Łącznie|Odpady odśmieskowe gen 2|Wystąpienie|
|Stan zdrowiaSeksus|Stan kontroli kondycji|Liczba|Średnia|Stan kontroli kondycji|Wystąpienie|
|System PlikówUsage|Użycie systemu plików|Bajty|Średnia|Użycie systemu plików|Brak|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingŚrodowiska/multiRolePools

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Żądania|Żądania|Liczba|Łącznie|Żądania|Wystąpienie|
|Bytesreceived|Dane w|Bajty|Łącznie|Dane w|Wystąpienie|
|Bajtów|Wyjście danych|Bajty|Łącznie|Wyjście danych|Wystąpienie|
|Http101|Http 101|Liczba|Łącznie|Http 101|Wystąpienie|
|Protokół Http2xx|Http 2xx|Liczba|Łącznie|Http 2xx|Wystąpienie|
|Protokół Http3xx|Http 3xx|Liczba|Łącznie|Http 3xx|Wystąpienie|
|Http401|Http 401|Liczba|Łącznie|Http 401|Wystąpienie|
|Http403|Http 403|Liczba|Łącznie|Http 403|Wystąpienie|
|Http404|Http 404|Liczba|Łącznie|Http 404|Wystąpienie|
|Http406|Http 406|Liczba|Łącznie|Http 406|Wystąpienie|
|Protokół Http4xx (polski)|Http 4xx|Liczba|Łącznie|Http 4xx|Wystąpienie|
|Protokół Http5xx|Błędy serwera http|Liczba|Łącznie|Błędy serwera http|Wystąpienie|
|ŚredniaResponseTime|Średni czas reakcji|Sekundy|Średnia|Średni czas reakcji|Wystąpienie|
|CpuPercentage (CpuPercentage)|Procent użycia procesora CPU|Wartość procentowa|Średnia|Procent użycia procesora CPU|Wystąpienie|
|MemoryPercentage (PamięćPrzestńczliwienie)|Procent pamięci|Wartość procentowa|Średnia|Procent pamięci|Wystąpienie|
|Funkcja DiskQueueLength|Długość kolejki dysku|Liczba|Średnia|Długość kolejki dysku|Wystąpienie|
|Funkcja HttpQueueLength|Długość kolejki http|Liczba|Średnia|Długość kolejki http|Wystąpienie|
|Prośby o activerequests|Aktywne żądania|Liczba|Łącznie|Aktywne żądania|Wystąpienie|
|TotalFrontEnds (Całkowita liczba podań)|Całkowite przednie końce|Liczba|Średnia|Całkowite przednie końce|Brak|
|SmallAppServicePlanInstans (Nienasycenie smallappserviceplan)|Pracownicy planu usługi aplikacji dla małych aplikacji|Liczba|Średnia|Pracownicy planu usługi aplikacji dla małych aplikacji|Brak|
|ŚredniAppServicePlanInstans|Pracownicy średniego planu usługi aplikacji|Liczba|Średnia|Pracownicy średniego planu usługi aplikacji|Brak|
|Duże usługi Nainstancje MapAppServicePlan|Pracownicy planu dużych usług aplikacji|Liczba|Średnia|Pracownicy planu dużych usług aplikacji|Brak|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingŚrodowienia/pracownicy Pools

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PracownicyWzrzesi|Łączna liczba pracowników|Liczba|Średnia|Łączna liczba pracowników|Brak|
|PracownicyDostępni|Dostępni pracownicy|Liczba|Średnia|Dostępni pracownicy|Brak|
|PracownicyUżywane|Pracownicy zaustosowni|Liczba|Średnia|Pracownicy zaustosowni|Brak|
|CpuPercentage (CpuPercentage)|Procent użycia procesora CPU|Wartość procentowa|Średnia|Procent użycia procesora CPU|Wystąpienie|
|MemoryPercentage (PamięćPrzestńczliwienie)|Procent pamięci|Wartość procentowa|Średnia|Procent pamięci|Wystąpienie|
## <a name="next-steps"></a>Następne kroki
* [Przeczytaj o metrykach w usłudze Azure Monitor](data-platform.md)
* [Tworzenie alertów dotyczących metryk](alerts-overview.md)
* [Eksportowanie danych do magazynu, Centrum zdarzeń lub analizy dzienników](platform-logs-overview.md)

