---
title: Monitorowanie metryk serwera usług Azure Analysis Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorowanie metryk serwera usług Analysis Services w witrynie Azure portal.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: d10e9d3d2b6fe339b04d3c0754290313312bc2b7
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427284"
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
|Wartości CurrentConnections|Połączenie: Bieżące połączenia|Licznik|Średnia|Bieżąca liczba ustanowionych połączeń klientów.|
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
|MemoryLimitLow|Pamięci: Dolny Limit pamięci|Bajty|Średnia|Dolny limit pamięci z pliku konfiguracji.|
|MemoryLimitVertiPaq|Pamięci: Limit pamięci aparatu VertiPaq|Bajty|Średnia|Limit w pamięci z pliku konfiguracji.|
|MemoryUsage|Pamięć: Użycie pamięci|Bajty|Średnia|Użycie pamięci przez proces serwera używane podczas obliczania cena oczyszczarki pamięci. Równe licznikowi Process\PrivateBytes plus rozmiar danych zamapowanych w pamięci, ignorowanie wszelkich pamięci zamapowanej lub przydzielonej przez aparat analizy w pamięci (VertiPaq) powyżej limitu pamięci aparatu.|
|Przydział|Pamięć: limit przydziału|Bajty|Średnia|Bieżący limit przydziału pamięci, w bajtach. Limit przydziału pamięci jest również nazywany rezerwacji pamięci lub przydział pamięci.|
|QuotaBlocked|Pamięci: Zablokowany limit przydziału|Licznik|Średnia|Bieżąca liczba żądań dotyczących limitu przydziału blokowanych do czasu są zwalniane innych limitów przydziału pamięci.|
|VertiPaqNonpaged|Pamięć: Niestronicowana pamięć aparatu VertiPaq|Bajty|Średnia|Bajty pamięci zablokowane w zestawie roboczym do użycia przez aparat w pamięci.|
|VertiPaqPaged|Pamięć: Stronicowana pamięć aparatu VertiPaq|Bajty|Średnia|Bajty stronicowanej pamięci dla danych w pamięci.|
|ProcessingPoolJobQueueLength|Długość kolejki zadań puli przetwarzania|Licznik|Średnia|Liczba zadań innych niż we/wy w kolejce puli wątków przetwarzania.|
|RowsConvertedPerSec|Przetwarzanie: Przekonwertowane wiersze na sekundę|CountPerSecond|Średnia|Współczynnik wierszy przekonwertowanych podczas przetwarzania.|
|RowsReadPerSec|Przetwarzanie: Odczytane wiersze na sekundę|CountPerSecond|Średnia|Współczynnik wierszy odczytanych ze wszystkich relacyjnych baz danych.|
|RowsWrittenPerSec|Przetwarzanie: Zapisane wiersze na sekundę|CountPerSecond|Średnia|Współczynnik wierszy zapisanych podczas przetwarzania.|
|qpu_metric|QPU|Licznik|Średnia|QPU. Zakres 0 – 100 dla S1, 0 – 200 dla S2 oraz 0 – 400 dla S4|
|QueryPoolBusyThreads|Zajęte wątki puli zapytania|Licznik|Średnia|Liczba zajętych wątków w puli wątków zapytania.|
|SuccessfullConnectionsPerSec|Udane połączenia na sekundę|CountPerSecond|Średnia|Współczynnik pomyślnie zakończonych połączeń.|
|CommandPoolBusyThreads|Wątki: Zajęte wątki puli poleceń|Licznik|Średnia|Liczba zajętych wątków w puli wątków poleceń.|
|CommandPoolIdleThreads|Wątki: Bezczynne wątki puli poleceń|Licznik|Średnia|Liczba bezczynnych wątków w puli wątków poleceń.|
|LongParsingBusyThreads|Wątki: Wątki o długotrwałej analizie zajęty|Licznik|Średnia|Liczba zajętych wątków w puli wątków o długotrwałej analizie.|
|LongParsingIdleThreads|Wątki: Wątki o długotrwałej analizie bezczynności|Licznik|Średnia|Liczba bezczynnych wątków w puli wątków o długotrwałej analizie.|
|LongParsingJobQueueLength|Wątki: Długość kolejki zadań o długotrwałej analizie|Licznik|Średnia|Liczba zadań w kolejce puli wątków o długotrwałej analizie.|
|ProcessingPoolIOJobQueueLength|Wątki: Przetwarzania puli długość kolejki zadań we/wy|Licznik|Średnia|Liczba zadań we/wy w kolejce puli wątków przetwarzania.|
|ProcessingPoolBusyIOJobThreads|Wątki: Zajęte wątki zadania operacji We/Wy w puli przetwarzania|Licznik|Średnia|Liczba wątków uruchamiających zadania we/wy w puli wątków przetwarzania.|
|ProcessingPoolBusyNonIOThreads|Wątki: Zajęte wątki innych niż we/wy puli przetwarzania|Licznik|Średnia|Liczba wątków uruchomionych zadań innych niż we/wy w puli wątków przetwarzania.|
|ProcessingPoolIdleIOJobThreads|Wątki: Przetwarzania bezczynne wątki zadań we/wy w puli|Licznik|Średnia|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|
|ProcessingPoolIdleNonIOThreads|Wątki: Bezczynne wątki innego niż we/wy puli przetwarzania|Licznik|Średnia|Liczba bezczynnych wątków w puli wątków przetwarzania dedykowanych dla zadań innych niż we/wy.|
|QueryPoolIdleThreads|Wątki: Bezczynne wątki puli zapytań|Licznik|Średnia|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|
|QueryPoolJobQueueLength|Wątki: Długość kolejki zadań puli zapytań|Licznik|Średnia|Liczba zadań w kolejce puli wątków zapytania.|
|ShortParsingBusyThreads|Wątki: Wątki o krótkotrwałej analizie zajęty|Licznik|Średnia|Liczba zajętych wątków w puli wątków krótkotrwałej analizie.|
|ShortParsingIdleThreads|Wątki: Wątki o krótkotrwałej analizie bezczynności|Licznik|Średnia|Liczba bezczynnych wątków w puli wątków krótkotrwałej analizie.|
|ShortParsingJobQueueLength|Wątki: O krótkotrwałej analizie długość kolejki zadań|Licznik|Średnia|Liczba zadań w kolejce puli wątków krótkotrwałej analizie.|
|TotalConnectionFailures|Łączna liczba błędów połączenia|Licznik|Średnia|Łączna liczba nieudanych prób nawiązania połączenia.|
|TotalConnectionRequests|Łączna liczba żądań połączenia|Licznik|Średnia|Łączna liczba żądań połączenia. |

## <a name="next-steps"></a>Kolejne kroki
[Monitorowanie na platformie Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md)   
[Metryki platformy Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)   
[Metryki w usłudze Azure Monitor interfejsu API REST](https://msdn.microsoft.com/library/azure/dn931930.aspx)