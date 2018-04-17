---
title: Monitorowanie usług Azure Analysis Services serwera metryk | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować metryki serwera usług Analysis Services w portalu Azure.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b07bd41ee3aa67924256d694c1f9dadd39b038cc
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="monitor-server-metrics"></a>Metryki serwera monitora

Usługi Analysis Services zawiera metryki ułatwiają monitorowanie wydajności i kondycji serwerów. Na przykład monitorować pamięć i użycie procesora CPU, liczba połączeń klientów oraz zużycie zasobów zapytania. Usługi Analysis Services używa tej samej struktury monitorowania jako najbardziej innymi usługami Azure. Aby dowiedzieć się więcej, zobacz [metryk w Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

Aby wykonać więcej diagnostyki szczegółowe, śledzić wydajność i trendów w wielu zasobów usługi z grupy zasobów lub subskrypcji, użyj [Azure Monitor](https://azure.microsoft.com/services/monitor/). Monitor Azure (usługa) może spowodować usługą płatną.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Aby monitorować metryki dla serwera usług Analysis Services

1. W portalu Azure, wybierz **metryki**.

    ![Monitorowanie w witrynie Azure Portal](./media/analysis-services-monitor/aas-monitor-portal.png)

2. W **dostępne metryki**, wybrać metryki, aby uwzględnić w planie. 

    ![Monitor wykresu](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>
## <a name="server-metrics"></a>Metryki serwera
Użyj tej tabeli, aby określić, które metryki są najlepsze w przypadku danego scenariusza monitorowania. Na tym samym wykresie można pokazać tylko metryki tę samą jednostkę.

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Długość kolejki zadań puli polecenia|Licznik|Średnia|Liczba zadań w kolejce polecenia puli wątków.|
|CurrentConnections|Połączenia: Bieżąca liczba połączeń|Licznik|Średnia|Bieżąca liczba połączeń klienta.|
|CurrentUserSessions|Bieżące sesje użytkowników|Licznik|Średnia|Bieżąca liczba ustanowionych sesji użytkownika.|
|mashup_engine_memory_metric|M aparat pamięci|Bajty|Średnia|Użycie pamięci przez procesy wyszukiwarek zestawu połączonych danych|
|mashup_engine_qpu_metric|Aparat M QPU|Licznik|Średnia|Użycie QPU przez procesy wyszukiwarek zestawu połączonych danych|
|memory_metric|Memory (Pamięć)|Bajty|Średnia|Pamięci. W zakresie 0 – 25 GB na S1, 0 – 50 GB na S2 i 0 – 100 GB dla S4|
|memory_thrashing_metric|Wykonywania niepotrzebnych replik pamięci|Procent|Średnia|Średnia pamięci wykonywania niepotrzebnych replik.|
|CleanerCurrentPrice|Pamięci: Czyszczący bieżąca cena|Licznik|Średnia|Bieżąca cena pamięci $/ byte/godzina znormalizowane do 1000.|
|CleanerMemoryNonshrinkable|Pamięci: Czyszczący nonshrinkable pamięci|Bajty|Średnia|Ilość pamięci w bajtach, nie może ulec czyszcząca przeczyszczanie w tle.|
|CleanerMemoryShrinkable|Pamięci: Zmniejszania pamięci czyszczący|Bajty|Średnia|Ilość pamięci w bajtach, mogą ulec czyszcząca przeczyszczanie w tle.|
|MemoryLimitHard|Pamięci: Twarde Limit pamięci|Bajty|Średnia|Limit pamięci twardym z pliku konfiguracji.|
|MemoryLimitHigh|Pamięć: Wysoki Limit pamięci|Bajty|Średnia|Limit pamięci wysokiej z pliku konfiguracji.|
|MemoryLimitLow|Pamięci: Niski Limit pamięci|Bajty|Średnia|Limit pamięci z pliku konfiguracji.|
|MemoryLimitVertiPaq|Pamięci: VertiPaq Limit pamięci|Bajty|Średnia|Limit w pamięci z pliku konfiguracji.|
|MemoryUsage|Pamięci: Użycie pamięci|Bajty|Średnia|Użycie pamięci przez proces serwera jako używaną przy obliczaniu czyszczący cena pamięci. Taki sam jak licznika Process\PrivateBytes powiększony o rozmiar danych mapowanych na pamięć, ignorując wszystkie pamięć, która została zamapowana lub przydzielone przez aparat analizy w pamięci (VertiPaq) przekracza Limit pamięci silnik.|
|Przydział|Pamięć: limit przydziału|Bajty|Średnia|Limit przydziału pamięci, w bajtach. Limit przydziału pamięci jest nazywana rezerwacji pamięci grant lub pamięci.|
|QuotaBlocked|Pamięci: Zablokowane przydziału|Licznik|Średnia|Bieżąca liczba żądań przydziału, które są zablokowane, dopóki nie są zwalniane innych przydziałów pamięci.|
|VertiPaqNonpaged|Pamięci: Niestronicowana VertiPaq|Bajty|Średnia|Zablokowane bajtów pamięci zestawu do użycia przez aparat w pamięci roboczego.|
|VertiPaqPaged|Pamięci: Stronicowanej VertiPaq|Bajty|Średnia|Liczba bajtów stronicowanej pamięci dla danych w pamięci.|
|ProcessingPoolJobQueueLength|Długość kolejki zadań przetwarzania w puli|Licznik|Średnia|Liczba zadań z systemem innym niż — I/O kolejki puli wątków przetwarzania.|
|RowsConvertedPerSec|Przetwarzanie: Wierszy przekonwertowane na sekundę|CountPerSecond|Średnia|Liczba wierszy przekonwertować podczas przetwarzania.|
|RowsReadPerSec|Przetwarzanie: Wiersze do odczytu na sekundę|CountPerSecond|Średnia|Liczba wierszy odczytywać wszystkie relacyjnych baz danych.|
|RowsWrittenPerSec|Przetwarzanie: Wierszy, zapisany na sekundę|CountPerSecond|Średnia|Liczba wierszy, zapisany podczas przetwarzania.|
|qpu_metric|QPU|Licznik|Średnia|QPU. Zakres 0-100 S1, 0 – 200 S2 i 0-400 dla S4|
|QueryPoolBusyThreads|Zapytanie z puli wątków zajęty|Licznik|Średnia|Liczba zajętych wątków w puli wątków zapytania.|
|SuccessfullConnectionsPerSec|Udane połączenia na sekundę|CountPerSecond|Średnia|Szybkość zakończeń udane połączenie.|
|CommandPoolBusyThreads|Wątki: Polecenie zajęty z puli wątków|Licznik|Średnia|Liczba zajętych wątków w puli wątków polecenia.|
|CommandPoolIdleThreads|Wątków: Polecenie puli wątków bezczynności|Licznik|Średnia|Liczba bezczynności wątków w puli wątków polecenia.|
|LongParsingBusyThreads|Wątków: Podczas analizowania zajęty wątków Long|Licznik|Średnia|Liczba zajętych wątków w puli wątków długo analizy.|
|LongParsingIdleThreads|Wątków: Podczas analizowania bezczynności wątków Long|Licznik|Średnia|Liczba bezczynności wątków w puli wątków długo analizy.|
|LongParsingJobQueueLength|Wątki: Czas analizowania długość kolejki zadań|Licznik|Średnia|Liczba zadań w kolejce długo analizy puli wątków.|
|ProcessingPoolIOJobQueueLength|Wątki: Przetwarzanie puli długość kolejki zadania we/wy|Licznik|Średnia|Liczba operacji We/Wy zadań w kolejce puli wątków przetwarzania.|
|ProcessingPoolBusyIOJobThreads|Wątki: Przetwarzania puli wątków zajęty zadania we/wy|Licznik|Średnia|Liczba wątków uruchomionych zadań we/wy w puli wątków przetwarzania.|
|ProcessingPoolBusyNonIOThreads|Wątków: Zajęty z puli wątków nie I/O przetwarzania|Licznik|Średnia|Liczba wątków uruchomionych zadań nie I/O w puli wątków przetwarzania.|
|ProcessingPoolIdleIOJobThreads|Wątki: Przetwarzania puli wątków bezczynności zadania we/wy|Licznik|Średnia|Liczba wątków bezczynności zadań we/wy w puli wątków przetwarzania.|
|ProcessingPoolIdleNonIOThreads|Wątki: Przetwarzanie bezczynności z puli wątków nie I/E|Licznik|Średnia|Liczba bezczynności wątków w puli wątków przetwarzania dedykowane dla zadań innych niż — I/O.|
|QueryPoolIdleThreads|Wątków: Wątki bezczynności puli zapytania|Licznik|Średnia|Liczba wątków bezczynności zadań we/wy w puli wątków przetwarzania.|
|QueryPoolJobQueueLength|Wątków: Długość kolejki zadań puli zapytania|Licznik|Średnia|Liczba zadań w kolejce zapytań puli wątków.|
|ShortParsingBusyThreads|Wątków: Podczas analizowania zajęty wątków krótkiej|Licznik|Średnia|Liczba wątków zajęty w krótkim analizy puli wątków.|
|ShortParsingIdleThreads|Wątków: Podczas analizowania bezczynności wątków krótkiej|Licznik|Średnia|Liczba wątków bezczynności w krótkim analizy puli wątków.|
|ShortParsingJobQueueLength|Wątków: Podczas analizowania długość kolejki zadań krótkiej|Licznik|Średnia|Liczba zadań w kolejce krótkich analizy puli wątków.|
|TotalConnectionFailures|Całkowita liczba awarii|Licznik|Średnia|Całkowita liczba nieudanych prób połączenia.|
|TotalConnectionRequests|Całkowita liczba połączeń żądań|Licznik|Średnia|Całkowita liczba połączeń żądania. |

## <a name="next-steps"></a>Kolejne kroki
[Monitorowanie w Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md)   
[Metryki na platformie Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)   
[Metryki w monitorze Azure interfejsu API REST](https://msdn.microsoft.com/library/azure/dn931930.aspx)