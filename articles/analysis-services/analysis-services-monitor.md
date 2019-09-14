---
title: Monitorowanie metryk serwera Azure Analysis Services | Microsoft Docs
description: Dowiedz się, jak monitorować metryki serwera Analysis Services w Azure Portal.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7c7aaf911930f83775f66c47377bc68edb059519
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70958987"
---
# <a name="monitor-server-metrics"></a>Monitorowanie metryk serwera

Analysis Services dostarcza metryki na platformie Azure Eksplorator metryk, bezpłatnego narzędzia w portalu, które ułatwia monitorowanie wydajności i kondycji serwerów. Na przykład Monitoruj użycie pamięci i procesora, liczbę połączeń klientów i użycie zasobów zapytań. Analysis Services korzysta z tego samego środowiska monitorowania co w przypadku większości innych usług platformy Azure. Aby dowiedzieć się więcej, zobacz Rozpoczynanie [pracy z usługą Azure Eksplorator metryk](../azure-monitor/platform/metrics-getting-started.md).

Aby bardziej szczegółowo przeprowadzić diagnostykę, śledzić wydajność i identyfikować trendy wielu zasobów usług w grupie zasobów lub subskrypcji, użyj [Azure monitor](../azure-monitor/overview.md). Azure Monitor (usługa) może spowodować powstanie usługi do rozliczenia.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Aby monitorować metryki dla serwera Analysis Services

1. W Azure Portal wybierz pozycję **metryki**.

    ![Monitorowanie w witrynie Azure Portal](./media/analysis-services-monitor/aas-monitor-portal.png)

2. W obszarze **Dostępne metryki**wybierz metryki do uwzględnienia na wykresie. 

    ![Monitorowanie wykresu](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Metryki serwera

Użyj tej tabeli, aby określić, które metryki najlepiej sprawdzają się w scenariuszu monitorowania. Na tym samym wykresie może być pokazywanych tylko metryki tej samej jednostki.

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Długość kolejki zadań puli poleceń|Count|Average|Liczba zadań w kolejce puli wątków poleceń.|
|Wartości CurrentConnections|Połączenie: Bieżące połączenia|Count|Average|Bieżąca liczba ustanowionych połączeń klientów.|
|CurrentUserSessions|Bieżące sesje użytkowników|Count|Average|Bieżąca liczba ustanowionych sesji użytkowników.|
|mashup_engine_memory_metric|Pamięć aparatu M|Bajty|Average|Użycie pamięci przez procesy aparatu mashupów|
|mashup_engine_qpu_metric|M QPU aparatu|Count|Average|QPU użycie przez procesy aparatu mashupów|
|memory_metric|Memory (Pamięć)|Bajty|Average|Rozmiar. Zakres 0-25 GB dla S1, 0-50 GB dla S2 i 0-100 GB dla S4|
|memory_thrashing_metric|Migotanie pamięci|Percent|Average|Średnia pamięć migotanie.|
|CleanerCurrentPrice|Pamięć: Bieżąca cena oczyszczarki|Count|Average|Bieżąca cena pamięci, $/Byte/Time, znormalizowana do 1000.|
|CleanerMemoryNonshrinkable|Pamięć: Pamięć oczyszczarki niemożliwa do zmniejszenia|Bajty|Average|Ilość pamięci (w bajtach), która nie podlega wyprzeczyszczaniu przez oczyszczarkę w tle.|
|CleanerMemoryShrinkable|Pamięć: Zmniejszanie ilości pamięci czyszczącej|Bajty|Average|Ilość pamięci, w bajtach, podlegająca przeczyszczaniu przez oczyszczarkę w tle.|
|MemoryLimitHard|Pamięć: Sztywny limit pamięci|Bajty|Average|Limit pamięci twardej z pliku konfiguracji.|
|MemoryLimitHigh|Pamięć: Limit pamięci — wysoki|Bajty|Average|Górny limit pamięci z pliku konfiguracji.|
|MemoryLimitLow|Pamięć: Limit pamięci — niski|Bajty|Average|Limit braku pamięci z pliku konfiguracji.|
|MemoryLimitVertiPaq|Pamięć: Limit pamięci — tryb VertiPaq|Bajty|Average|Limit w pamięci z pliku konfiguracji.|
|MemoryUsage|Pamięć: Użycie pamięci|Bajty|Average|Użycie pamięci przez proces serwera jako używany do obliczania ceny pamięci czyszczącej. Równe licznikowi Process\PrivateBytes i rozmiar danych mapowanych na pamięć, ignorowanie wszelkich pamięci, które zostały zamapowane lub przydzielone przez aparat analityczny w pamięci (VertiPaq) przekraczające limit pamięci aparatu.|
|private_bytes_metric|Bajty prywatne |Bajty|Average|Całkowita ilość pamięci przydzielona przez proces aparatu Analysis Services i procesy kontenera mashupów, bez uwzględnienia pamięci współdzielonej z innymi procesami.|
|virtual_bytes_metric|Bajty wirtualne |Bajty|Average|Bieżący rozmiar wirtualnej przestrzeni adresowej używanej przez proces aparatu i procesy kontenera mashupów Analysis Services.|
|mashup_engine_private_bytes_metric|Liczba prywatnych bajtów aparatu M |Bajty|Average|Łączna ilość pamięci w procesach kontenera programu mashup pamięć została przydzielona, bez uwzględnienia pamięci współdzielonej z innymi procesami.|
|mashup_engine_virtual_bytes_metric|Liczba bajtów wirtualnych aparatu M |Bajty|Average|Bieżący rozmiar wirtualnej przestrzeni adresowej, z której korzystają procesy kontenera mashupu.|
|Limit przydziału|Pamięć: Limit przydziału|Bajty|Average|Bieżący limit pamięci (w bajtach). Przydział pamięci jest również nazywany przydziałem pamięci lub rezerwacją pamięci.|
|QuotaBlocked|Pamięć: Limit przydziału zablokowany|Count|Average|Bieżąca liczba żądań przydziałów blokowanych do momentu zwolnienia innych limitów przydziału pamięci.|
|VertiPaqNonpaged|Pamięć: Niestronicowana VertiPaq|Bajty|Average|Bajty pamięci zablokowane w zestawie roboczym do użycia przez aparat w pamięci.|
|VertiPaqPaged|Pamięć: Stronicowana VertiPaq|Bajty|Average|Bajty stronicowanej pamięci w użyciu dla danych w pamięci.|
|ProcessingPoolJobQueueLength|Długość kolejki zadań puli przetwarzania|Count|Average|Liczba zadań innych niż zadania we/wy w kolejce puli wątków przetwarzania.|
|RowsConvertedPerSec|Rozpatrywan Przekonwertowane wiersze na sekundę|CountPerSecond|Average|Szybkość wierszy przekonwertowanych podczas przetwarzania.|
|RowsReadPerSec|Rozpatrywan Odczytane wiersze na sekundę|CountPerSecond|Average|Współczynnik wierszy odczytanych ze wszystkich relacyjnych baz danych.|
|RowsWrittenPerSec|Rozpatrywan Wiersze zapisywane na sekundę|CountPerSecond|Average|Częstotliwość wierszy zapisywana podczas przetwarzania.|
|qpu_metric|QPU|Count|Average|QPU. Zakres 0-100 dla S1, 0-200 dla S2 i 0-400 dla S4|
|QueryPoolBusyThreads|Wątki zajęte w puli zapytań|Count|Average|Liczba zajętych wątków w puli wątków zapytań.|
|SuccessfullConnectionsPerSec|Udane połączenia na sekundę|CountPerSecond|Average|Szybkość pomyślnych połączeń.|
|CommandPoolBusyThreads|Wątk Zajęte wątki puli poleceń|Count|Average|Liczba zajętych wątków w puli wątków poleceń.|
|CommandPoolIdleThreads|Wątk Bezczynne wątki w puli poleceń|Count|Average|Liczba bezczynnych wątków w puli wątków poleceń.|
|LongParsingBusyThreads|Wątk Zajęte wątki o długotrwałej analizie|Count|Average|Liczba zajętych wątków w puli wątków o długotrwałej analizie.|
|LongParsingIdleThreads|Wątk Bezczynne wątki do długotrwałej analizy|Count|Average|Liczba bezczynnych wątków w puli wątków o długotrwałej analizie.|
|LongParsingJobQueueLength|Wątk Długość kolejki zadań o długotrwałej analizie|Count|Average|Liczba zadań w kolejce puli wątków o długotrwałej analizie.|
|ProcessingPoolIOJobQueueLength|Wątk Długość kolejki zadań we/wy puli przetwarzania|Count|Average|Liczba zadań we/wy w kolejce puli wątków przetwarzania.|
|ProcessingPoolBusyIOJobThreads|Wątk Zajęte wątki zadań we/wy puli przetwarzania|Count|Average|Liczba wątków uruchamiających zadania we/wy w puli wątków przetwarzania.|
|ProcessingPoolBusyNonIOThreads|Wątk Zajęte wątki innych niż we/wy puli przetwarzania|Count|Average|Liczba wątków uruchamiających zadania inne niż we/wy w puli wątków przetwarzania.|
|ProcessingPoolIdleIOJobThreads|Wątk Wątki zadań we/wy puli przetwarzania|Count|Average|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|
|ProcessingPoolIdleNonIOThreads|Wątk Bezczynne wątki inne niż we/wy puli przetwarzania|Count|Average|Liczba bezczynnych wątków w puli wątków przetwarzania dedykowanych dla zadań innych niż zadania we/wy.|
|QueryPoolIdleThreads|Wątk Bezczynne wątki puli zapytań|Count|Average|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|
|QueryPoolJobQueueLength|Wątk Długość kolejki zadań puli zapytań|Count|Average|Liczba zadań w kolejce puli wątków zapytań.|
|ShortParsingBusyThreads|Wątk Zajęte wątki z krótką analizą|Count|Average|Liczba zajętych wątków w puli wątków o krótkiej analizie.|
|ShortParsingIdleThreads|Wątk Bezczynne wątki analizy|Count|Average|Liczba bezczynnych wątków w puli wątków o krótkiej analizie.|
|ShortParsingJobQueueLength|Wątk Długość kolejki zadań o krótkiej analizie|Count|Average|Liczba zadań w kolejce puli wątków o krótkiej analizie.|
|TotalConnectionFailures|Łączna liczba błędów połączenia|Count|Average|Łączna liczba nieudanych prób połączenia.|
|TotalConnectionRequests|Łączna liczba żądań połączenia|Count|Average|Łączna liczba żądań połączenia. |

## <a name="next-steps"></a>Następne kroki
[Przegląd Azure Monitor](../azure-monitor/overview.md)      
[Wprowadzenie do usługi Azure Eksplorator metryk](../azure-monitor/platform/metrics-getting-started.md)      
[Metryki w interfejsie API REST Azure Monitor](/rest/api/monitor/metrics)
