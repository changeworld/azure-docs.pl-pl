---
title: Wykrywanie anomalii w czasie serii i przygotowywać trafniejsze prognozy w Eksploratorze danych platformy Azure
description: Dowiedz się, jak i analizowanie danych szeregów czasowych, wykrywanie anomalii i prognozowania, za pomocą Eksploratora danych platformy Azure.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 934dfd3334b6f433c7acdf9816a3fb5e24f0430f
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64872006"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Wykrywanie anomalii i przygotowywać trafniejsze prognozy w Eksploratorze danych platformy Azure

Eksplorator danych usługi Azure wykonuje kolekcję bieżące dane telemetryczne z usług w chmurze lub urządzeń IoT. Te dane są analizowane różnych szczegółowych danych, takich jak monitorowanie kondycji usługi, procesy produkcji fizycznej, trendów użycia i prognozy obciążenia. Analiza odbywa się w szeregu czasowym wybrane metryki, aby zlokalizować wzorca odchylenie metryki względem jego wzór typowej normalny punkt odniesienia. Eksplorator usługi Azure Data zawiera natywną obsługę tworzenia, manipulowanie i analizę wielu szeregach czasowych. Może utworzyć i analizowanie tysiące szeregów czasowych w ciągu kilku sekund, umożliwiając niemal w czasie rzeczywistym, monitorowanie rozwiązań i przepływów pracy.

Ten artykuł szczegółowo opisuje Eksploratora danych usługi Azure serii anomalii wykrywania i przygotowywać trafniejsze prognozy funkcji czasu. Funkcje serii czasu stosowane są oparte na modelu niezawodne dekompozycji dobrze znanego, gdzie każdy oryginalnego szereg czasowy zostanie podzielona na sezonowe, trend, a pozostałe składniki. Anomalie są wykrywane przez elementy odstające w składniku pozostałych, podczas prognozowania odbywa się przez ekstrapolacji sezonowych i trendów składników. Implementacja Eksploratora danych usługi Azure znacznie zwiększa modelu podstawowe dekompozycji sezonowości automatycznego wykrywania, niezawodne odstające analizy i zwektoryzowane implementację procesu tysięcy szeregów czasowych w ciągu kilku sekund.

## <a name="prerequisites"></a>Wymagania wstępne

Odczyt [czas analizy serii w Eksploratorze usługi Azure Data](/azure/data-explorer/time-series-analysis) z omówieniem funkcji serii czasu.

## <a name="time-series-decomposition-model"></a>Model rozkładu szeregów czasowych

Azure Eksplorator danych natywnych implementacji prognozowania szeregu czasowego i wykrywanie anomalii korzysta z modelu dekompozycji dobrze znane. Ten model jest stosowany do szeregu czasowym Oczekiwano manifestu okresowe i zachowanie trend, takie jak ruch w ramach usługi, pulsów składnika i pomiarów okresowych IoT prognozowanie przyszłych wartości metryk i wykrywać nietypowe te metryki. Założenia tego procesu regresji jest to, że inne niż wcześniej znane sezonowych i zachowanie trend, czas serii losowo dystrybucji. Można następnie prognozowanie przyszłych wartości metryk z sezonowych i trend składników, zbiorczo o nazwie linii bazowej i Zignoruj pozostałe części. Może również wykrywać nietypowe wartości na podstawie analizy odstające przy użyciu tylko końcowej części.
Aby utworzyć model dekompozycji, należy użyć funkcji [ `series_decompose()` ](/azure/kusto/query/series-decomposefunction). `series_decompose()` Funkcja pobiera zestaw szeregów czasowych i automatycznie rozkłada każdego Szeregi czasowe do jego sezonowe, trend, pozostały i składników linii bazowej. 

Na przykład możesz rozłożyć ruchu usługi wewnętrznej sieci web przy użyciu następującej kwerendy:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (baseline, seasonal, trend, residual) = series_decompose(num, -1, 'linefit')  //  decomposition of a set of time series to seasonal, trend, residual, and baseline (seasonal+trend)
| render timechart with(title='Web app. traffic of a month, decomposition', ysplit=panels)
```

![Rozkład serii czasu](media/anomaly-detection/series-decompose-timechart.png)

* Oryginalny szeregu czasowego jest oznaczona etykietą **num** (na czerwono). 
* Proces jest uruchamiany przez automatyczne wykrywanie sezonowości za pomocą funkcji [ `series_periods_detect()` ](/azure/kusto/query/series-periods-detectfunction) i wyodrębnia **sezonowych** wzorca (w kolorze fioletowym).
* Wzorcu sezonowym jest odejmowany od oryginalnego szeregów czasowych i regresji liniowej jest uruchamiane przy użyciu funkcji [ `series_fit_line()` ](/azure/kusto/query/series-fit-linefunction) można znaleźć **trend** składnika (w jasnoniebieski).
* Funkcja odejmuje trend, a reszta wynosi **resztkowe** składnika (w kolorze zielonym).
* Na koniec, funkcja dodaje sezonowych i trend składników, aby wygenerować **linii bazowej** (w kolorze niebieskim).

## <a name="time-series-anomaly-detection"></a>Wykrywanie anomalii w czasie serii

Funkcja [ `series_decompose_anomalies()` ](/azure/kusto/query/series-decompose-anomaliesfunction) wykryje nietypowe punktów na zbiór szeregów czasowych. Wywołuje tę funkcję, `series_decompose()` tworzenie dekompozycji modelu a następnie uruchamia [ `series_outliers()` ](/azure/kusto/query/series-outliersfunction) na pozostałych składników. `series_outliers()` oblicza wyniki anomalii dla każdego punktu pozostałych składników, za pomocą testu horyzont Tukey firmy. Wyniki anomalii powyżej w wersji 1.5 lub poniżej-1.5 wskazuje podstawę łagodne anomalii lub odrzucić odpowiednio. Wyniki anomalii powyżej 3.0 lub poniżej-3.0 wskazują na silne anomalii. 

Następujące zapytanie służy do wykrycia anomalii w ruchu w ramach usługi wewnętrznej sieci web:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (anomalies, score, baseline) = series_decompose_anomalies(num, 1.5, -1, 'linefit')
| render anomalychart with(anomalycolumns=anomalies, title='Web app. traffic of a month, anomalies') //use "| render anomalychart with anomalycolumns=anomalies" to render the anomalies as bold points on the series charts.
```

![Wykrywanie anomalii w czasie serii](media/anomaly-detection/series-anomaly-detection.png)

* Oryginalny szeregów czasowych (na czerwono). 
* Linia bazowa (sezonowych + trendu) składnik (w kolorze niebieskim).
* Nietypowe punkty (w kolorze fioletowym) na podstawie oryginalnego szeregów czasowych. Nietypowe punktów znacznie różni się od wartości oczekiwanej odniesienia.

## <a name="time-series-forecasting"></a>Prognozowanie szeregów czasowych

Funkcja [ `series_decompose_forecast()` ](/azure/kusto/query/series-decompose-forecastfunction) do przewidywania przyszłych wartości zbiór szeregów czasowych. Ta funkcja wywołuje `series_decompose()` tworzenie dekompozycji modelu, a następnie dla każdej serii czasu extrapolates składnika linii bazowej w przyszłości.

Następujące zapytanie służy do prognozowania ruch usługi sieci web w przyszłym tygodniu:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decmposition')
```

![Prognozowanie szeregów czasowych](media/anomaly-detection/series-forecasting.png)

* Oryginalny metryki (na czerwono). Przyszłych wartości brakuje i ustawiona na 0, domyślnie.
* Ekstrapolację składnik punktu odniesienia (w kolorze niebieskim) do przewidywania wartości w następnym tygodniu.

## <a name="scalability"></a>Skalowalność

Składni języka zapytań w usłudze Azure Eksplorator danych umożliwia pojedyncze wywołanie do przetworzenia wielu szeregach czasowych. Jego unikatowy zoptymalizowane implementacji umożliwia wydajne, co ma kluczowe znaczenie dla skutecznego anomalii i przygotowywać trafniejsze prognozy podczas monitorowania tysiące liczników w pobliżu scenariuszy w czasie rzeczywistym.

Następujące zapytanie pokazuje przetwarzania danych szeregów czasowych trzy jednocześnie:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid
| extend offset=case(sid=='TS3', 4000000, sid=='TS2', 2000000, 0)   //  add artificial offset for easy visualization of multiple time series
| extend num=series_add(num, offset)
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week for 3 time series')
```

![Skalowalność serii czasu](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>Podsumowanie

W tym dokumencie szczegóły funkcji natywnych Eksplorator danych platformy Azure do wykrywania anomalii serii czasu i prognozowania, co znacznie zwiększa modelu dekompozycji podstawowe, na którym bazuje. Każdy oryginalnego szereg czasowy zostanie podzielona, wykrycia anomalii i prognozowanie wykonywane. Czas serii anomalii wykrywania i przygotowywać trafniejsze prognozy funkcje są używane na potrzeby prawie w czasie rzeczywistym scenariuszy monitorowania, takich jak wykrywanie błędów, Konserwacja zapobiegawcza i żądanie i obciążenia, funkcja prognozowania.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [możliwości uczenia maszynowego](/azure/data-explorer/machine-learning-clustering) w Eksploratorze danych platformy Azure.