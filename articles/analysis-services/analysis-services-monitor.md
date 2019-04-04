---
title: Monitorowanie metryk serwera usług Azure Analysis Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorowanie metryk serwera usług Analysis Services w witrynie Azure portal.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: cdffa8e138062a91bd1876ac6e44728c47d9cdd7
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893184"
---
# <a name="monitor-server-metrics"></a>Monitorowanie metryk serwera

Analysis Services udostępniają metryki, które ułatwiają monitorowanie wydajności i kondycji serwerów. Na przykład można monitorować, pamięć i użycie procesora CPU, liczba połączeń klienta i użycia zasobów zapytania. Usługi Analysis Services używa tej samej struktury monitorowania co większości innych usług platformy Azure. Aby dowiedzieć się więcej, zobacz [metryki na platformie Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

Aby wykonać więcej szczegółowe diagnostyki, śledzić wydajność i identyfikować trendy w wielu zasobach usługi w grupie zasobów lub subskrypcji, użyj [usługi Azure Monitor](https://azure.microsoft.com/services/monitor/). Usługa Azure Monitor (usługa) może spowodować usługi płatnej.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Monitorowanie metryk serwera usług Analysis Services

1. W witrynie Azure portal wybierz **metryki**.

    ![Monitorowanie w witrynie Azure Portal](./media/analysis-services-monitor/aas-monitor-portal.png)

2. W **dostępne metryki**, wybrać metryki, aby uwzględnić w planie. 

    ![Monitor wykresu](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Metryk serwera

Ta tabela służy do określenia, które metryki są najlepsze w przypadku danego scenariusza monitorowania. Wyłącznie metryki tej samej jednostki mogą być wyświetlane na tym samym wykresie.

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Długość kolejki zadań puli polecenia|Licznik|Średnia|Liczba zadań w kolejce puli wątków poleceń.|
|Wartości CurrentConnections|Połączenie: Bieżąca liczba połączeń|Licznik|Średnia|Bieżąca liczba ustanowionych połączeń klientów.|
|CurrentUserSessions|Bieżące sesje użytkowników|Licznik|Średnia|Bieżąca liczba ustanowionych sesji użytkowników.|
|mashup_engine_memory_metric|Pamięć aparatu M|Bajty|Średnia|Użycie pamięci przez procesy aparatu mashupów|
|mashup_engine_qpu_metric|Jednostka QPU aparatu M|Licznik|Średnia|Użycie jednostek QPU przez procesy aparatu mashupów|
|memory_metric|Memory (Pamięć)|Bajty|Średnia|Ilość pamięci. Należeć do zakresu 0 – 25 GB dla S1, 0 – 50 GB dla S2 oraz 0 – 100 GB dla S4|
|memory_thrashing_metric|Przeładowywanie pamięci|Procent|Średnia|Średnie przeładowywanie pamięci.|
|CleanerCurrentPrice|Pamięć: Bieżąca cena oczyszczarki|Licznik|Średnia|Bieżąca cena pamięci $/ bajty/czas, znormalizowana do 1000.|
|CleanerMemoryNonshrinkable|Pamięć: Pamięć oczyszczarki|Bajty|Średnia|Ilość pamięci w bajtach, niepodlegająca przeczyszczaniu przez w tle czyszcząca.|
|CleanerMemoryShrinkable|Pamięć: Pamięć oczyszczarki zmniejszania|Bajty|Średnia|Ilość pamięci w bajtach, podlegająca przeczyszczaniu przez w tle czyszcząca.|
|MemoryLimitHard|Pamięć: Stały Limit pamięci|Bajty|Średnia|Stały limit pamięci z pliku konfiguracji.|
|MemoryLimitHigh|Pamięć: Górny Limit pamięci|Bajty|Średnia|Górny limit pamięci z pliku konfiguracji.|
|MemoryLimitLow|Pamięć: Dolny Limit pamięci|Bajty|Średnia|Dolny limit pamięci z pliku konfiguracji.|
|MemoryLimitVertiPaq|Pamięć: Limit pamięci aparatu VertiPaq|Bajty|Średnia|Limit w pamięci z pliku konfiguracji.|
|MemoryUsage|Pamięć: Użycie pamięci|Bajty|Średnia|Użycie pamięci przez proces serwera używane podczas obliczania cena oczyszczarki pamięci. Równe licznikowi Process\PrivateBytes plus rozmiar danych zamapowanych w pamięci, ignorowanie wszelkich pamięci zamapowanej lub przydzielonej przez aparat analizy w pamięci (VertiPaq) powyżej limitu pamięci aparatu.|
|Przydział|Pamięć: Przydział|Bajty|Średnia|Bieżący limit przydziału pamięci, w bajtach. Limit przydziału pamięci jest również nazywany rezerwacji pamięci lub przydział pamięci.|
|QuotaBlocked|Pamięć: Zablokowany limit przydziału|Licznik|Średnia|Bieżąca liczba żądań dotyczących limitu przydziału blokowanych do czasu są zwalniane innych limitów przydziału pamięci.|
|VertiPaqNonpaged|Pamięć: VertiPaq Nonpaged|Bajty|Średnia|Bajty pamięci zablokowane w zestawie roboczym do użycia przez aparat w pamięci.|
|VertiPaqPaged|Pamięć: VertiPaq Paged|Bajty|Średnia|Bajty stronicowanej pamięci dla danych w pamięci.|
|ProcessingPoolJobQueueLength|Długość kolejki zadań puli przetwarzania|Licznik|Średnia|Liczba zadań innych niż we/wy w kolejce puli wątków przetwarzania.|
|RowsConvertedPerSec|W toku: Wiersze przekonwertowane na sekundę|CountPerSecond|Średnia|Współczynnik wierszy przekonwertowanych podczas przetwarzania.|
|RowsReadPerSec|W toku: Odczytane wiersze na sekundę|CountPerSecond|Średnia|Współczynnik wierszy odczytanych ze wszystkich relacyjnych baz danych.|
|RowsWrittenPerSec|W toku: Zapisane wiersze na sekundę|CountPerSecond|Średnia|Współczynnik wierszy zapisanych podczas przetwarzania.|
|qpu_metric|QPU|Licznik|Średnia|QPU. Zakres 0 – 100 dla S1, 0 – 200 dla S2 oraz 0 – 400 dla S4|
|QueryPoolBusyThreads|Zajęte wątki puli zapytania|Licznik|Średnia|Liczba zajętych wątków w puli wątków zapytania.|
|SuccessfullConnectionsPerSec|Udane połączenia na sekundę|CountPerSecond|Średnia|Współczynnik pomyślnie zakończonych połączeń.|
|CommandPoolBusyThreads|Wątki: Zajęte wątki puli polecenia|Licznik|Średnia|Liczba zajętych wątków w puli wątków poleceń.|
|CommandPoolIdleThreads|Wątki: Bezczynne wątki puli polecenia|Licznik|Średnia|Liczba bezczynnych wątków w puli wątków poleceń.|
|LongParsingBusyThreads|Wątki: Długotrwałej analizie zajęte wątki|Licznik|Średnia|Liczba zajętych wątków w puli wątków o długotrwałej analizie.|
|LongParsingIdleThreads|Wątki: Długotrwałej analizie bezczynne wątki|Licznik|Średnia|Liczba bezczynnych wątków w puli wątków o długotrwałej analizie.|
|LongParsingJobQueueLength|Wątki: Długotrwałej analizie długość kolejki zadań|Licznik|Średnia|Liczba zadań w kolejce puli wątków o długotrwałej analizie.|
|ProcessingPoolIOJobQueueLength|Wątki: Długość kolejki zadań we/wy puli przetwarzania|Licznik|Średnia|Liczba zadań we/wy w kolejce puli wątków przetwarzania.|
|ProcessingPoolBusyIOJobThreads|Wątki: Zajęte wątki zadań we/wy puli przetwarzania|Licznik|Średnia|Liczba wątków uruchamiających zadania we/wy w puli wątków przetwarzania.|
|ProcessingPoolBusyNonIOThreads|Wątki: Zajęte wątki zadań innych we/wy puli przetwarzania|Licznik|Średnia|Liczba wątków uruchomionych zadań innych niż we/wy w puli wątków przetwarzania.|
|ProcessingPoolIdleIOJobThreads|Wątki: Bezczynne wątki zadań we/wy puli przetwarzania|Licznik|Średnia|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|
|ProcessingPoolIdleNonIOThreads|Wątki: Bezczynne wątki innego niż we/wy puli przetwarzania|Licznik|Średnia|Liczba bezczynnych wątków w puli wątków przetwarzania dedykowanych dla zadań innych niż we/wy.|
|QueryPoolIdleThreads|Wątki: Bezczynne wątki puli zapytań|Licznik|Średnia|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|
|QueryPoolJobQueueLength|Wątki: Długość kolejki zadań puli zapytań|Licznik|Średnia|Liczba zadań w kolejce puli wątków zapytania.|
|ShortParsingBusyThreads|Wątki: Krótkie analizy zajęte wątki|Licznik|Średnia|Liczba zajętych wątków w puli wątków krótkotrwałej analizie.|
|ShortParsingIdleThreads|Wątki: Krótkie analizy bezczynne wątki|Licznik|Średnia|Liczba bezczynnych wątków w puli wątków krótkotrwałej analizie.|
|ShortParsingJobQueueLength|Wątki: Krótkie analizy długość kolejki zadań|Licznik|Średnia|Liczba zadań w kolejce puli wątków krótkotrwałej analizie.|
|TotalConnectionFailures|Łączna liczba błędów połączenia|Licznik|Średnia|Łączna liczba nieudanych prób nawiązania połączenia.|
|TotalConnectionRequests|Łączna liczba żądań połączenia|Licznik|Średnia|Łączna liczba żądań połączenia. |

## <a name="next-steps"></a>Kolejne kroki
[Monitorowanie na platformie Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md)   
[Metryki platformy Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)   
[Metryki w usłudze Azure Monitor interfejsu API REST](/rest/api/monitor/metrics)
