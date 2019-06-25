---
title: Jak monitorować i zmniejszyć ograniczanie żądań w usłudze Azure Time Series Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak monitorowanie, diagnozowanie i rozwiązać problemy z wydajnością, które powodują opóźnienia i ograniczania przepustowości w usłudze Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 129476c833e596d40daa7081e23c0fd6d1b93b30
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165762"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Monitorowanie i minimalizowanie ograniczania w celu zmniejszenia opóźnienia w usłudze Azure Time Series Insights

Gdy ilość danych przychodzących przekroczy konfiguracji w danym środowisku, mogą wystąpić opóźnienia lub ograniczanie żądań w usłudze Azure Time Series Insights.

Możesz uniknąć opóźnienia i ograniczania przepustowości odpowiednio konfigurując środowiska ilości danych, które mają być analizowane.

Możesz z największym prawdopodobieństwem mogą wystąpić opóźnienia i ograniczania przepustowości, kiedy użytkownik:

- Dodawanie źródła zdarzeń, który zawiera stare dane, które może być dłuższe niż szybkość transferu danych przychodzących w wyznaczonym (usługi Time Series Insights będzie trzeba zapoznać się z nimi).
- Dodaj więcej źródeł zdarzeń do środowiska, wynikiem kolekcji z dodatkowych zdarzeń, (które może przekraczać pojemność w danym środowisku).
- Wypchnij dużych ilości zdarzeń historycznych do źródła zdarzenia skutkuje to opóźnienie (usługi Time Series Insights będzie trzeba zapoznać się z nimi).
- Dołącz do danymi referencyjnymi przy użyciu telemetrii, co skutkuje większym rozmiarze zdarzenia.  Z punktu widzenia ograniczania pakiet ingressed danych o rozmiarze pakietów wynosi 32 KB jest traktowane jako 32 zdarzenia, każdy o rozmiarze 1 KB. Maksymalny dozwolony rozmiar zdarzenia wynosi 32 KB; pakiety danych większe niż 32 KB są obcinane.

## <a name="video"></a>Połączenia wideo

### <a name="learn-about-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Więcej informacji na temat zachowania w zakresie transferu danych przychodzących usługi Time Series Insights danych oraz sposobu planowania dla niego.</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>Monitorowanie opóźnienia i ograniczania przepustowości z alertami

Alerty mogą ułatwić zdiagnozować i rozwiązać problemy z opóźnienia spowodowane przez środowiska.

1. W witrynie Azure portal wybierz **metryki**.

   [![Metryki](media/environment-mitigate-latency/add-metrics.png)](media/environment-mitigate-latency/add-metrics.png#lightbox)

1. Wybierz pozycję **Dodaj alert dotyczący metryki**.  

   [![Dodaj alert metryki](media/environment-mitigate-latency/add-metric-alert.png)](media/environment-mitigate-latency/add-metric-alert.png#lightbox)

Z tego miejsca można skonfigurować alertów za pomocą następujących metryk:

|Metryka  |Opis  |
|---------|---------|
|**Ruch przychodzący odebrały bajty**     | Liczba bajtów raw odczytywane z źródła zdarzeń. Liczba nieprzetworzonych zwykle obejmuje nazwy i wartości właściwości.  |  
|**Ruch przychodzący Odebrano nieprawidłowy komunikaty**     | Liczba komunikatów nieprawidłowy odczytu ze wszystkich źródeł zdarzeń usługi Azure Event Hubs lub usługi Azure IoT Hub.      |
|**Ruch przychodzący odebranych komunikatów**   | Liczba komunikatów są odczytywane z źródła zdarzeń wszystkie centra zdarzeń lub centra IoT Hub.        |
|**Ruch przychodzący przechowywane bajtów**     | Całkowity rozmiar zdarzenia zapisane i dostępne dla zapytania. Rozmiar jest obliczana tylko na wartości właściwości.        |
|**Ruch przychodzący przechowywanych zdarzeń**     |   Liczba zdarzeń spłaszczonych przechowywane i dostępne dla zapytania.      |
|**Opóźnienie czasowe odebranego komunikatu przychodzącego**    |  Różnica w sekundach między czasem, że wiadomość jest dodawanych do kolejki zdarzeń źródła i czasu, gdy są przetwarzane w transferu danych przychodzących.      |
|**Ruch przychodzący Odebrano Lag liczba komunikatów**    |  Różnica między liczba sekwencji ostatniej wiadomości w kolejce w zdarzeniu źródła partycji i numer sekwencji przetwarzanego w ruchu przychodzącego komunikatu.      |

![Opóźnienie](media/environment-mitigate-latency/latency.png)

* Jeśli użytkownik są ograniczane, zobaczysz wartość *ruch przychodzący Odebrano komunikat zwłokę czasową obowiązującą*, informujące o liczbę sekund za usługi TSI pochodzi z rzeczywisty czas komunikat trafienia źródło zdarzenia (z wyjątkiem indeksowania czas appx. 30 – 60 sekund).  *Opóźnienie liczby komunikatów odebranych ruch przychodzący* również powinien mieć wartość, co pozwala określić, ile komunikatów za Tobą są.  Najprostszym sposobem aktualizować jest zwiększenie pojemności Twojego środowiska do rozmiar, który umożliwi przezwyciężyć różnica.  

  Na przykład w środowisku jednej jednostki S1 i jest to opóźnienie 5 000 000 wiadomości, można zwiększyć rozmiar środowiska do 6 jednostek wokół jeden dzień można aktualizować.  Użytkownik może zwiększyć nawet bardziej efektywnej się szybciej. Okres zapoznać się ze zmianami często dochodzi podczas początkowego inicjowania obsługi administracyjnej środowiska, szczególnie w przypadku, gdy połączysz ze źródłem zdarzeń, które zawiera zdarzenia w nim lub zbiorcze przekazywanie dużej ilości danych historycznych.

* Inna technika polega na ustawieniu **przyjętych zdarzeń przechowywanych** alert > = Próg nieco poniżej środowiska całkowitej pojemności dla okresu 2 godzin.  Ten alert może ułatwić zrozumienie, jeśli stale się w pojemności, co oznacza wysokie prawdopodobieństwo opóźnienia. 

  Na przykład, jeśli masz trzech jednostek S1 wykorzystanych (lub 2100 zdarzeń na pojemność zdarzeń przychodzących minuty), można ustawić **przyjętych zdarzeń przechowywanych** alert dotyczący > = 1900 zdarzeń przez 2 godziny. Jeśli są stale powyżej wartości progowej, a w związku z tym, wyzwalania alertu, są prawdopodobnie w obszarze aprowizowano.  

* Jeśli podejrzewasz, możesz są ograniczane, można porównać z **transferu danych przychodzących komunikatów odebranych** za pomocą zdarzenia źródło użytkownika egressed wiadomości.  Jeśli ruch przychodzący do Centrum zdarzeń jest większa niż Twoje **transferu danych przychodzących komunikatów odebranych**, prawdopodobnie są ograniczane usługi Time Series Insights.

## <a name="improving-performance"></a>Poprawianie wydajności

Aby zmniejszyć ograniczania przepływności lub występują opóźnienia, najlepszym sposobem, aby go poprawić, jest zwiększenie pojemności w danym środowisku.

Możesz uniknąć opóźnienia i ograniczania przepustowości odpowiednio konfigurując środowiska ilości danych, które mają być analizowane. Aby uzyskać więcej informacji o sposobie dodawania pojemności dla danego środowiska, zobacz [Skalowanie środowiska](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać dodatkowe kroki rozwiązywania problemów [diagnozowanie i rozwiązywanie problemów w danym środowisku usługi Time Series Insights](time-series-insights-diagnose-and-solve-problems.md).

- Aby uzyskać dodatkową pomoc, Rozpocznij konwersację na [forum MSDN dotyczącym](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) lub [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). Możesz również skontaktować się ze [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) opcji asystowanej pomocy technicznej.
