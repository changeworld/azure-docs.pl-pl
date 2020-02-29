---
title: Wykrywanie anomalii szeregów czasowych & prognozowanie na platformie Azure Eksplorator danych
description: Dowiedz się, jak analizować dane szeregów czasowych na potrzeby wykrywania anomalii i prognozowania przy użyciu usługi Azure Eksplorator danych.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: a482fef93d43f92257608b65c9c0e2ade535bcca
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78194161"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Wykrywanie anomalii i prognozowanie na platformie Azure Eksplorator danych

Usługa Azure Eksplorator danych wykonuje zbieranie danych telemetrycznych z usług Cloud Services i IoT. Te dane są analizowane pod kątem różnych szczegółowych informacji, takich jak monitorowanie kondycji usług, fizyczne procesy produkcyjne, trendy użycia i prognoza obciążenia. Analiza jest wykonywana w szeregach czasowych wybranych metryk, aby znaleźć wzorzec odchylenia metryki względem typowego normalnego wzorca linii bazowej. Usługa Azure Eksplorator danych zawiera natywną obsługę tworzenia, manipulowania i analizy wielu szeregów czasowych. Może ona tworzyć i analizować tysiące szeregów czasowych w ciągu kilku sekund, umożliwiając monitorowanie rozwiązań i przepływów pracy niemal w czasie rzeczywistym.

W tym artykule opisano możliwości wykrywania anomalii w usłudze Azure Eksplorator danych i prognozowania. Odpowiednie funkcje szeregów czasowych są oparte na niezawodnym dobrze znanym modelu dekompozycji, gdzie każda oryginalna seria czasowa jest rozmieszczona w ramach sezonowych, trendów i składników resztkowych. Anomalie są wykrywane przez wartości odstające składnika końcowego, podczas gdy prognozowanie odbywa się przez ekstrapolację komponentów sezonowych i trendów. Implementacja platformy Azure Eksplorator danych znacząco rozszerza podstawowy model dekompozycji przez automatyczne wykrywanie sezonowości, niezawodną analizę odstającą i rozbudowaną implementację do przetwarzania tysięcy szeregów czasowych w ciągu kilku sekund.

## <a name="prerequisites"></a>Wymagania wstępne

Przeczytaj [analizę szeregów czasowych w usłudze Azure Eksplorator danych](/azure/data-explorer/time-series-analysis) , aby zapoznać się z omówieniem możliwości szeregów czasowych.

## <a name="time-series-decomposition-model"></a>Model dekompozycji szeregów czasowych

Implementacja natywna Eksplorator danych platformy Azure do prognozowania szeregów czasowych i wykrywania anomalii używa dobrze znanego modelu dekompozycji. Ten model jest stosowany do szeregów czasowych metryk, które powinny być używane do przewidywania zachowań okresowych i trendów, takich jak ruch usługi, pulsy składników oraz pomiary okresowe IoT do prognozowania przyszłych wartości metryk i wykrywania anomalii. Założenie tego procesu regresji jest inne niż poprzednio znane zachowanie sezonowe i trendu, Seria czasowa jest losowo dystrybuowana. Następnie można prognozować przyszłe wartości metryk z komponentów sezonowych i trendów, wspólnie nazwany punkt odniesienia i ignorować część końcową. Można również wykrywać nietypowe wartości na podstawie analizy odstającej, korzystając z tylko części resztkowej.
Aby utworzyć model dekompozycji, użyj funkcji [`series_decompose()`](/azure/kusto/query/series-decomposefunction). Funkcja `series_decompose()` przyjmuje zestaw szeregów czasowych i automatycznie dekomponowa każdą serię czasową do swoich składników sezonowych, trendu, reszty i linii bazowej. 

Na przykład można rozłożyć ruch wewnętrznej usługi sieci Web przy użyciu następującej kwerendy:

**\[** [**kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WQ3WrDMAyF7/sUukvCnDXJGIOVPEULuwxqoixm/gm2+jf28JObFjbYrmyho3M+yRCD1a5jaGFAJtaW8qaqX8qqLqvnYrMySYHnvxRNWT1B07xW1U03JFEzbVYDWd9Z/KAuUtAUm9UXpLJcSnAH2+LxPZe3AO9gJ6ZbRjvDGLy9EbG/BUemOXnvLxD1AOJ1mijQtWhbyHbbOgOA9RogkqGeAaXn3g1BooVb6OiDNHpD6CjAUccDGv2JrL0TSzozuQHyPYqHdqRkDKN3aBRwkJaCQJIoQ4VsuXh2A/Xezj5SWkVBWSvI0vSoOSsWpLtEpyDwY4KTW8nnJ5ws+2+eAhSyOxjkd+HDVVcIfHplp2TYTxgYTpqnnDUbarM32gPO86PY4jjqfmGw3vGkftNlCi5xNprbWW5kYvENQQnqDh8CAAA=) **\]**

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

![Dekompozycja szeregów czasowych](media/anomaly-detection/series-decompose-timechart.png)

* Oryginalna seria czasowa ma etykietę **NUM** (czerwony). 
* Proces jest uruchamiany przez Autowykrywanie sezonowości przy użyciu funkcji [`series_periods_detect()`](/azure/kusto/query/series-periods-detectfunction) i wyodrębnia wzorzec **sezonowy** (w kolorze purpurowym).
* Wzorzec sezonowy jest odejmowany od oryginalnej serii czasu, a regresja liniowa jest uruchamiana przy użyciu funkcji [`series_fit_line()`](/azure/kusto/query/series-fit-linefunction) , aby znaleźć składnik **trendu** (w jasnym kolorze niebieskim).
* Funkcja odejmuje trend, a reszta jest składnikiem **końcowym** (zielonym).
* Na koniec funkcja dodaje składniki sezonowe i trend, aby wygenerować **linię bazową** (niebieską).

## <a name="time-series-anomaly-detection"></a>Wykrywanie anomalii szeregów czasowych

Funkcja [`series_decompose_anomalies()`](/azure/kusto/query/series-decompose-anomaliesfunction) odnajduje punkty nietypowe na zestawie szeregów czasowych. Ta funkcja wywołuje `series_decompose()`, aby skompilować model dekompozycji, a następnie uruchamiać [`series_outliers()`](/azure/kusto/query/series-outliersfunction) na składniku końcowym. `series_outliers()` oblicza wyniki anomalii dla każdego punktu składnika końcowego za pomocą testu ogrodzenia Tukey. Wyniki anomalii powyżej 1,5 lub poniżej — 1,5 wskazują niedelikatny wzrost anomalii lub odrzucanie. Wyniki anomalii powyżej 3,0 lub poniżej-3,0 wskazują silną anomalię. 

Poniższe zapytanie umożliwia wykrycie anomalii w ruchu wewnętrznego usługi sieci Web:

**\[** [**kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WR3W7CMAyF73mKI25KpRbaTmjSUJ8CpF1WoXVptPxUifmb9vBLoGO7GFeR7ePv2I4ihpamYdToBBNLTYuqKF/zosyLdbqZqagQl/8UVV68oKreimLSdVFUDZtZR9o2WnxQ48lJ8tXsCzHM7yHMUdfidFiEN4U12AXoloUe0Turp4nYTsaeaYzs/RVedgis80CObkFdI9ltywTAagV4UtQyRKiZgyLEaTGZ9taFQqtIGHI4SX8USn4KltYEJF2YTIeFMFaHPPkMvrWOMuxFoEpDaVjujmo6aq0erafmIY+7ZCiX6wx5mSGJHb3kJA1sF8jB8q69toNwjLPkYfGTseqoja//eLNkRXXyTnuIcVyCneh72cL2YQdtDQ8ZHvIkDcsfPWH+3AvPvObx0FMXD/RLhfDYW9VhtNKwj/8U69M1b2S//AbRUQMWQQIAAA==) **\]**

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

![Wykrywanie anomalii szeregów czasowych](media/anomaly-detection/series-anomaly-detection.png)

* Oryginalna seria czasowa (w kolorze czerwonym). 
* Składnik linii bazowej (sezonowy i Trend) (w kolorze niebieskim).
* Nietypowe punkty (w kolorze purpurowym) na oryginalną serię czasową. Nietypowe punkty znacząco odbiegają od oczekiwanych wartości bazowych.

## <a name="time-series-forecasting"></a>Prognozowanie szeregów czasowych

Funkcja [`series_decompose_forecast()`](/azure/kusto/query/series-decompose-forecastfunction) przewidywania przyszłych wartości zestawu szeregów czasowych. Ta funkcja wywołuje `series_decompose()`, aby skompilować model dekompozycji, a następnie dla każdej serii czasowej ekstrapolacja składnik linii bazowej w przyszłości.

Poniższe zapytanie umożliwia przewidywanie ruchu usługi sieci Web w następnym tygodniu:

**\[** [**kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA22QzW6DMBCE73mKuQFqKISqitSIW98gkXpEDl5iK9hG9uanUR++dqE99YRGO8x845EYRtuO0UIKJtaG8qbebMt6U9avxW41Joe4/+doyvoFTfNW14tPJlOjZqGc1w9n263crSQZ1xlxpi6Q1xSa1ReSLGcJezGtuJ7y+C3gLA6xZM/CTBi8MwshuxnkaUlGYJpS5/ETQUvEzJsiTz+ibZEd9psMQFUBgUbqGSLe7GkkpBVYygfn46EfSVjyuOpwEaN+CNbOxki6M1mZTNSLkAbOv3WSemcmF6j7vSX8dcTUlvOFsZJcFDHFx4wYnmp7JTzjplnlrHmkNvugI8Q0PYO9GAbdww0RyDjLav1XHLnBimAjEG5E5zQ7vRP284x36hOOTtxZ8Q3The8P2QEAAA==) **\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decomposition')
```

![Prognozowanie szeregów czasowych](media/anomaly-detection/series-forecasting.png)

* Oryginalna Metryka (w kolorze czerwonym). Przyszłe wartości są niedostępne i domyślnie ustawione na 0.
* Ekstrapolacja składnika linii bazowej (niebieska) do przewidywania wartości w następnym tygodniu.

## <a name="scalability"></a>Skalowalność

Składnia języka zapytań Eksplorator danych platformy Azure umożliwia pojedyncze wywołanie przetwarzania wielu szeregów czasowych. Jej unikatowa zoptymalizowana implementacja pozwala na szybką wydajność, która ma kluczowe znaczenie dla efektywnego wykrywania anomalii i prognozowania podczas monitorowania tysięcy liczników w scenariuszach niemal w czasie rzeczywistym.

Następujące zapytanie pokazuje przetwarzanie trzech szeregów czasowych jednocześnie:

**\[** [**kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA21Qy26DMBC85yvmFlChcUirSI34ikTqETl4KVawjfDmqX587UCaHuqLtePxPLYjhtG2YpRQkom1oaQQy3Uulrl4TzezLjLk5T9GkYsViuJDiImnIqlox6F1g745W67VZqbIuMrIA1WeBk2+mH0jjvk4wh5NKU9fSbhTOItdMNmyND2awZkpIbsxyMukDM/UR8/9FV6rIEkXJqvgmsYTl7X0lISHspzvtqt5hjdxPxkeYBHA4gGKFMBiAUilIAfWja617CY1NG4ASX/FSfuj7PRNsg4ZXANz7Fj3HSGuBmOjZ5hYbcSqIBwbZpNk+iQFcQpx4/omrqLamd55qh5v41d22nIybWChOI0qQ9Cg4e5ftyE6zprbhDV3VM4/aQ/Z96/gQTahU4wsYZzlNvs11vYL3BJsCIQz0eHed/W30jz9AUEBI0ktAgAA) **\]**

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

![Skalowalność szeregów czasowych](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>Podsumowanie

Ten dokument zawiera szczegółowe informacje na temat natywnych funkcji platformy Azure Eksplorator danych na potrzeby wykrywania anomalii i prognozowania w szeregach czasowych. Każda oryginalna seria czasowa jest rozłożone na sezonowe, trendy i składniki resztkowe służące do wykrywania anomalii i/lub prognozowania. Te funkcje mogą być używane dla scenariuszy monitorowania niemal w czasie rzeczywistym, takich jak wykrywanie błędów, konserwacja predykcyjna i zapotrzebowanie i prognozowanie obciążeń.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [możliwościach uczenia maszynowego](/azure/data-explorer/machine-learning-clustering) w usłudze Azure Eksplorator danych.
