---
title: Jak monitorować i zmniejszenia przepustowości w usłudze Azure czas serii Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak monitorowanie, diagnozowanie i ograniczyć problemy z wydajnością powodujących opóźnienia i ograniczania przepustowości w usłudze Azure czas serii Insights.
services: time-series-insights
ms.service: time-series-insights
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 11/27/2017
ms.openlocfilehash: ac59359eb6af268f311534d90e1529fc5e41094f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Monitorowanie i ograniczenia przepustowości w celu zmniejszenia opóźnień w usłudze Azure czas serii Insights
Gdy ilość przychodzących danych przekracza konfiguracji w danym środowisku, mogą wystąpić opóźnienia lub ograniczanie w usłudze Azure czas serii Insights.

Można uniknąć opóźnienia i ograniczania przepustowości, odpowiednio konfigurując środowiska ilości danych, które mają być analizowane.

Najprawdopodobniej będzie występować opóźnienia i ograniczania przepustowości, gdy użytkownik:

- Dodaj źródło zdarzenia, które zawiera stare dane, które może przekroczyć szybkość wyznaczonym transfer danych przychodzących (czas serii Insights należy uwzględnić).
- Dodaj więcej źródła zdarzeń do środowiska, co w kolekcji z dodatkowych zdarzeń (które można przekracza pojemność w danym środowisku).
- Wypchnij dużych ilości historycznych zdarzenia ze źródłem zdarzeń, co powoduje opóźnienie (czas serii Insights należy uwzględnić).
- Dołącz dane referencyjne o telemetrii, co zapewnia zwiększenie rozmiaru zdarzenia.  Z punktu widzenia ograniczania przepustowości ingressed danych o rozmiarze pakiecie 32 KB jest traktowany jako 32 zdarzenia, każdy o rozmiarze 1 KB. Maksymalnego dozwolonego rozmiaru zdarzenia wynosi 32 KB; pakiety danych jest większy niż 32 KB są obcinane.


## <a name="monitor-latency-and-throttling-with-alerts"></a>Monitor opóźnienia i ograniczania przepustowości z alertami

Alerty ułatwiają zdiagnozować i ograniczyć problemy opóźnienia spowodowane przez środowisko. 

1. W portalu Azure kliknij **metryki**. 

   ![Metryki](media/environment-mitigate-latency/add-metrics.png)

2. Kliknij przycisk **Dodaj alert metryki**.  

    ![Dodaj alert metryczny](media/environment-mitigate-latency/add-metric-alert.png)

Z tego miejsca można skonfigurować alerty za pomocą następujących metryk:

|Metryka  |Opis  |
|---------|---------|
|**Transfer danych przychodzących odebrane bajty**     | Liczba bajtów raw odczytywać źródła zdarzeń. Nieprzetworzona liczba zwykle zawiera nazwę właściwości i wartość.  |  
|**Transfer danych przychodzących Odebrano nieprawidłowy wiadomości**     | Liczba wiadomości nieprawidłowy odczytywać wszystkie źródła zdarzeń usługi Azure Event Hubs lub Centrum IoT Azure.      |
|**Transfer danych przychodzących odebranych komunikatów**   | Liczba wiadomości odczytywać wszystkie centra zdarzeń lub centra IoT źródła zdarzeń.        |
|**Transfer danych przychodzących przechowywane bajtów**     | Łączny rozmiar zdarzeń zapisanych i dostępne dla zapytania. Rozmiar jest obliczana tylko na wartość właściwości.        |
|**Transfer danych przychodzących przechowywane zdarzenia**     |   Liczba zdarzeń spłaszczoną przechowywane i dostępne dla zapytania.      |
|**Transfer danych przychodzących komunikatów logicznych opóźnienia**    |  Różnica między czas komunikat umieszczonych w kolejce w źródle zdarzeń i czas przetwarzania w transfer danych przychodzących.      |
|**Transfer danych przychodzących logicznych liczba komunikatów opóźnienie**    |  Różnica między liczba sekwencji ostatniej wiadomości umieszczonych w kolejce zdarzeń źródła partycji i sekwencji liczba komunikatów przetwarzanych w wejściowych.      |


![Opóźnienie](media/environment-mitigate-latency/latency.png)

Jeśli użytkownik jest ograniczane, wyświetlona zostanie wartość dla *zwłokę czasową komunikat logicznych wejściowych*, informujące o ile minut za TSI jest od czasu rzeczywistego komunikat trafienia źródło zdarzenia (z wyłączeniem czasu indeksowania appx. 30 – 60 sekund).  *Liczba komunikatów wejściowych logicznych Lag* również powinien mieć wartość, dzięki czemu można ustalić, ile komunikatów za możesz są.  Najprostszym sposobem aktualizować jest w celu zwiększenia pojemności w środowisku do rozmiaru, która pozwala pokonać różnica.  

Na przykład w środowisku pojedynczego S1 jednostki i dowiedzieć się, że istnieje opóźnienie pięć milionów wiadomości, można zwiększyć rozmiar środowiska do sześciu jednostki dla wokół dziennie można aktualizować.  Możesz można zwiększyć nawet bardziej efektywnej się szybciej.  To jest typowe wystąpienia, gdy początkowo inicjowania obsługi administracyjnej środowisku, szczególnie w przypadku, gdy zostanie ono podłączone do źródła zdarzenia, który ma już zdarzenia w nim lub gdy zbiorczego przekazania duże ilości danych historycznych.

Innej techniki jest skonfigurowanie **wejściowych przechowywanych zdarzenia** alert > = Próg nieco niższy wydajność środowiska łączny okres 2 godziny.  Ten alert może pomóc zrozumieć, jeśli jest stale pojemności, co oznacza wysokie prawdopodobieństwo opóźnienia.  

Na przykład jeśli użytkownik ma trzy jednostki S1 udostępniane (lub 2100 zdarzeń na minutę ruch przychodzący wydajność), możesz ustawić **wejściowych przechowywanych zdarzenia** alertów dla > = 1900 zdarzenia przez 2 godziny. Jeśli są stale powyżej tego progu, a w związku z tym wyzwalania alertu, można prawdopodobnie w obszarze — udostępnieniu.  

Ponadto jeśli podejrzewasz, możesz są ograniczane, możesz porównać z **transfer danych przychodzących komunikatów odebranych** z wydarzenia źródłowego obiektu egressed wiadomości.  Jeśli ruch przychodzący do Centrum zdarzeń jest większa niż Twoje **wejściowych odebranych komunikatów**, prawdopodobnie są ograniczane szczegółowe dane serii czasu.

## <a name="improving-performance"></a>Poprawianie wydajności 
Zmniejszenie przepustowości lub występują opóźnienia, najlepszy sposób, aby go poprawić, jest zwiększenie pojemności w danym środowisku. 

Można uniknąć opóźnienia i ograniczania przepustowości, odpowiednio konfigurując środowiska ilości danych, które mają być analizowane. Aby uzyskać więcej informacji o sposobie dodawania pojemności dla danego środowiska, zobacz [skalowania środowiska](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Kolejne kroki
- Dodatkowe kroki rozwiązywania problemu [diagnozowanie i rozwiązywanie problemów w środowisku czasu serii Insights](time-series-insights-diagnose-and-solve-problems.md).
- Aby uzyskać dodatkową pomoc, należy rozpocząć konwersację na [MSDN forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) lub [przepełnienie stosu](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). Można również skontaktować się [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) opcji z pomocą techniczną.
