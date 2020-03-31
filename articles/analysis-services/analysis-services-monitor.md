---
title: Monitorowanie metryk serwera usług Azure Analysis Services | Dokumenty firmy Microsoft
description: Dowiedz się, jak usługi Analysis Services korzystają z Usługi Azure Metrics Explorer, bezpłatnego narzędzia w portalu, aby ułatwić monitorowanie wydajności i kondycji serwerów.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: aaa3a6d128fe7dd466f6f60ab515f05fa38ba63b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252807"
---
# <a name="monitor-server-metrics"></a>Monitorowanie metryk serwera

Analysis Services udostępnia metryki w Usłudze Azure Metrics Explorer, bezpłatne narzędzie w portalu, aby ułatwić monitorowanie wydajności i kondycji serwerów. Na przykład monitorowanie użycia pamięci i procesora CPU, liczby połączeń klientów i zużycia zasobów zapytań. Analysis Services używa tej samej struktury monitorowania, jak większość innych usług platformy Azure. Aby dowiedzieć się więcej, zobacz [Wprowadzenie do Eksploratora metryk platformy Azure](../azure-monitor/platform/metrics-getting-started.md).

Aby przeprowadzić bardziej dogłębną diagnostykę, śledzić wydajność i identyfikować trendy w wielu zasobach usługi w grupie zasobów lub subskrypcji, należy użyć usługi [Azure Monitor](../azure-monitor/overview.md). Usługa Azure Monitor (usługa) może spowodować usługę podlegania rozliczaniu.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Aby monitorować metryki dla serwera usług Analysis Services

1. W witrynie Azure portal wybierz **pozycję Metryki**.

    ![Monitorowanie w witrynie Azure Portal](./media/analysis-services-monitor/aas-monitor-portal.png)

2. W **obszarze Metryka**wybierz dane, które mają być uwzględnione na wykresie. 

    ![Wykres monitorujący](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Metryki serwera

Ta tabela służy do określenia, które metryki są najlepsze dla scenariusza monitorowania. Tylko dane tej samej jednostki mogą być wyświetlane na tym samym wykresie.

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Typ agregacji|Opis|
|---|---|---|---|---|
|CommandPoolJobQueueLength (PoleceniePoolJobQueueLength)|Długość kolejki zadania puli poleceń|Liczba|Średnia|Liczba zadań w kolejce puli wątków poleceń.|
|CurrentConnections (Połączenia prądowe)|Połączenie: Bieżące połączenia|Liczba|Średnia|Bieżąca liczba nawiązanych połączeń klientów.|
|CurrentUserSessions|Bieżące sesje użytkowników|Liczba|Średnia|Bieżąca liczba sesji użytkowników ustalona.|
|mashup_engine_memory_metric|Pamięć silnika M|Bajty|Średnia|Użycie pamięci przez procesy silnika mashup|
|mashup_engine_qpu_metric|Silnik M QPU|Liczba|Średnia|Użycie QPU przez procesy silnika mashup|
|memory_metric|Memory (Pamięć)|Bajty|Średnia|Memory (pamięć). Zakres 0-25 GB dla S1, 0-50 GB dla S2 i 0-100 GB dla S4|
|memory_thrashing_metric|Przeładowywanie pamięci|Wartość procentowa|Średnia|Średnia pamięć thrashing.|
|CleanerCurrentPrice|Pamięć: Czystsza aktualna cena|Liczba|Średnia|Aktualna cena pamięci, $/byte/time, znormalizowana do 1000.|
|CleanerMemoryNiejmkliwi|Pamięć: Czystsza pamięć nierozskawalna|Bajty|Średnia|Ilość pamięci w bajtach, nie podlega przeczyszczaniu przez środek czyszczący tło.|
|CleanerMemoryShrinkable|Pamięć: Czystsza pamięć z kurczliwa|Bajty|Średnia|Ilość pamięci w bajtach, podlega przeczyszczaniu przez środek czyszczący tło.|
|MemoryLimitHard (Funkcja MemoryLimitHard)|Pamięć: Czas ogranicznik pamięci|Bajty|Średnia|Limit pamięci twardej z pliku konfiguracyjnego.|
|MemoryLimitHigh|Pamięć: Limit pamięci wysoki|Bajty|Średnia|Wysoki limit pamięci z pliku konfiguracyjnego.|
|MemoryLimitLow (Funkcja MemoryLimitLow)|Pamięć: Niski limit pamięci|Bajty|Średnia|Niski limit pamięci z pliku konfiguracyjnego.|
|MemoryLimitVertiPaq|Pamięć: VertiPaq limitu pamięci|Bajty|Średnia|Limit w pamięci, z pliku konfiguracyjnego.|
|MemoryUsage (Narzędzie pamięci)|Pamięć: Użycie pamięci|Bajty|Średnia|Użycie pamięci przez proces serwera, używane do obliczania ceny czystszej pamięci. Równe licznikowi Process\PrivateBytes plus rozmiar danych mapowanych w pamięci, ignorując dowolną pamięć, która została mapowana lub przydzielona przez aparat analizy w pamięci (VertiPaq) w nadmiarze limitu pamięci aparatu.|
|private_bytes_metric|Bajty prywatne |Bajty|Średnia|Całkowita ilość pamięci proces aparatu Usług Analysis Services i procesów kontenera Mashup zostały przydzielone, z wyłączeniem pamięci współużytkowanej z innymi procesami.|
|virtual_bytes_metric|Bajty wirtualne |Bajty|Średnia|Bieżący rozmiar wirtualnej przestrzeni adresowej, który analizuje proces aparatu usług Analysis Services i procesy kontenera Mashup.|
|mashup_engine_private_bytes_metric|Prywatne bajty silnika M |Bajty|Średnia|Całkowita ilość pamięci Mashup procesów kontenera zostały przydzielone, nie w tym pamięci współużytkowanej z innymi procesami.|
|mashup_engine_virtual_bytes_metric|Wirtualne bajty silnika M |Bajty|Średnia|Bieżący rozmiar wirtualnej przestrzeni adresowej Mashup procesów kontenera są przy użyciu.|
|Limit przydziału|Pamięć: Przydział|Bajty|Średnia|Bieżący przydział pamięci w bajtach. Przydział pamięci jest również znany jako przyznanie pamięci lub rezerwacja pamięci.|
|Blokada przydziału|Pamięć: Przydział zablokowany|Liczba|Średnia|Bieżąca liczba żądań przydziału, które są blokowane do momentu, gdy inne przydziały pamięci zostaną zwolnione.|
|VertiPaqNonpaged ( VertiPaqNonpaged )|Pamięć: VertiPaq Nonpaged|Bajty|Średnia|Bajty pamięci zablokowane w zestawie roboczym do użytku przez aparat w pamięci.|
|VertiPaqPaged ( VertiPaqPaged )|Pamięć: VertiPaq Paged|Bajty|Średnia|Bajty pamięci stronicowanych w użyciu dla danych w pamięci.|
|PrzetwarzaniePoolJobQueueLength|Długość kolejki zadania puli przetwarzania|Liczba|Średnia|Liczba zadań innych niż we/wy w kolejce puli wątków przetwarzania.|
|WierszeConvertedPerSec|Przetwarzanie: wiersze konwertowane na sekundę|Połów połówek|Średnia|Szybkość wierszy przekonwertowanych podczas przetwarzania.|
|WierszeReadPerSec|Przetwarzanie: wiersze odczytywane na sekundę|Połów połówek|Średnia|Szybkość wierszy odczytywanych ze wszystkich relacyjnych baz danych.|
|WierszeWrittenPerSec|Przetwarzanie: wiersze zapisane na sekundę|Połów połówek|Średnia|Szybkość wierszy zapisanych podczas przetwarzania.|
|qpu_metric|Funkcja QPU|Liczba|Średnia|QPU. Zakres 0-100 dla S1, 0-200 dla S2 i 0-400 dla S4|
|QueryPoolBusyThreads|Wątki zajętych puli zapytań|Liczba|Średnia|Liczba zajęty wątków w puli wątków kwerendy.|
|SuccessfullConnectionsPerSec|Pomyślne połączenia na sekundę|Połów połówek|Średnia|Szybkość pomyślnego ukończenia połączenia.|
|CommandPoolBusyThreads (PoleceniaPoolBusyThreads)|Wątki: Wątki zajęty puli poleceń|Liczba|Średnia|Liczba zajęty wątków w puli wątków polecenia.|
|CommandPoolIdleThreads (PoleceniaPoolIdleThreads)|Wątki: Wątki bezczynności puli poleceń|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków poleceń.|
|LongParsingBusyThreads|Wątki: Długie analizowanie zajętych wątków|Liczba|Średnia|Liczba wątków zajęty w puli wątków analizowania długich.|
|LongParsingIdleTyty|Wątki: Długie parsowanie bezczynnych wątków|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków analizowania długich.|
|LongParsingJobQueueLength|Wątki: Długa długość kolejki zadań analizowania|Liczba|Średnia|Liczba zadań w kolejce puli wątków analizowania długich.|
|ProcessingPoolIOJobQueueLength|Wątki: Długość kolejki we/wy puli przetwarzania|Liczba|Średnia|Liczba zadań we/wy w kolejce puli wątków przetwarzania.|
|ProcessingPoolBusyIOJobTyty|Wątki: Przetwarzanie puli zajętych wątków zadań we/wy|Liczba|Średnia|Liczba wątków z uruchomionymi zadaniami we/wy w puli wątków przetwarzania.|
|ProcessingPoolBusyNonIOTyty|Wątki: Przetwarzanie puli zajętych wątków innych niż We/Wy|Liczba|Średnia|Liczba wątków z uruchomionymi zadaniami nieujętymi/wy w puli wątków przetwarzania.|
|ProcessingPoolIdleIOJobThreads|Wątki: Przetwarzanie wątków zadań bezczynności puli bezczynności we/wy|Liczba|Średnia|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|
|ProcessingPoolIdleNonIOTyty|Wątki: Przetwarzanie bezczynności wątków bezczynności puli|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków przetwarzania przeznaczonych do zadań innych niż we/wy.|
|QueryPoolIdleThreads|Wątki: wątki bezczynności puli zapytań|Liczba|Średnia|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|
|QueryPoolJobQueueLength|Wątki: Długość kolejki zadań puli zapytań|Liczba|Średnia|Liczba zadań w kolejce puli wątków kwerend.|
|ShortParsingBusyThreads|Wątki: Krótkie analizowanie zajętych wątków|Liczba|Średnia|Liczba wątków zajęty w puli wątków krótkiej analizy.|
|ShortParsingIdleThreads|Wątki: Krótkie parsowanie bezczynnych wątków|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków krótkich analizowania.|
|ShortParsingJobQueueLength|Wątki: Krótka długość kolejki zadań analizowania|Liczba|Średnia|Liczba zadań w kolejce puli wątków krótkiej analizy.|
|TotalConnectionFailures (Całkowita liczba połączeń)|Całkowita liczba niepowodzeń połączeń|Liczba|Średnia|Całkowita liczba nieudanych prób połączenia.|
|Wnioski o ponowne połączenie ogółem|Łączna liczba żądań połączeń|Liczba|Średnia|Łączna liczba żądań połączeń. |

## <a name="next-steps"></a>Następne kroki
[Omówienie usługi Azure Monitor](../azure-monitor/overview.md)      
[Wprowadzenie do Eksploratora metryk platformy Azure](../azure-monitor/platform/metrics-getting-started.md)      
[Metryki w interfejsie API REST monitora platformy Azure](/rest/api/monitor/metrics)
