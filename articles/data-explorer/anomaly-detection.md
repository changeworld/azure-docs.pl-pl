---
title: Wykrywanie anomalii szeregów czasowych & prognozowania w Eksploratorze danych platformy Azure
description: Dowiedz się, jak analizować dane szeregów czasowych pod kątem wykrywania anomalii i prognozowania przy użyciu Eksploratora danych platformy Azure.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: a482fef93d43f92257608b65c9c0e2ade535bcca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194161"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Wykrywanie anomalii i prognozowanie w Eksploratorze danych platformy Azure

Usługa Azure Data Explorer wykonuje w toku zbieranie danych telemetrycznych z usług w chmurze lub urządzeń IoT. Te dane są analizowane pod kątem różnych szczegółowych informacji, takich jak monitorowanie kondycji usługi, fizycznych procesów produkcyjnych, trendów użycia i prognozy obciążenia. Analiza jest wykonywana w szeregach czasowych wybranych metryk, aby zlokalizować wzorzec odchylenia metryki względem jej typowego wzorca linii bazowej. Usługa Azure Data Explorer zawiera natywną obsługę tworzenia, manipulowania i analizy wielu szeregów czasowych. Może tworzyć i analizować tysiące szeregów czasowych w ciągu kilku sekund, umożliwiając niemal w czasie rzeczywistym rozwiązania do monitorowania i przepływy pracy.

W tym artykule opisano funkcje wykrywania i prognozowania anomalii szeregów czasowych usługi Azure Data Explorer. Odpowiednie funkcje szeregów czasowych są oparte na solidnym, dobrze znanym modelu rozkładu, w którym każda oryginalna seria czasowa jest rozłożona na składniki sezonowe, trendowe i resztkowe. Anomalie są wykrywane przez odstające na składniku resztkowym, podczas gdy prognozowanie odbywa się przez ekstrapolację składników sezonowych i trendów. Implementacja usługi Azure Data Explorer znacznie rozszerza podstawowy model rozkładu dzięki automatycznemu wykrywaniu sezonowości, niezawodnej analizie odstajnej i implementacji wektorowanej w celu przetworzenia tysięcy szeregów czasowych w ciągu kilku sekund.

## <a name="prerequisites"></a>Wymagania wstępne

Przeczytaj [analizę szeregów czasowych w Eksploratorze danych platformy Azure,](/azure/data-explorer/time-series-analysis) aby uzyskać omówienie możliwości szeregów czasowych.

## <a name="time-series-decomposition-model"></a>Model rozkładu szeregów czasowych

Implementacja natywna usługi Azure Data Explorer do przewidywania szeregów czasowych i wykrywania anomalii używa dobrze znanego modelu rozkładu. Ten model jest stosowany do szeregów czasowych metryk, które mają manifestować okresowe i trendowe zachowanie, takie jak ruch usługi, pulsy składników i okresowe pomiary IoT, aby prognozować przyszłe wartości metryk i wykrywać nietypowe. Założenie tego procesu regresji jest to, że inne niż wcześniej znane zachowanie sezonowe i trend, szeregi czasowe są losowo dystrybuowane. Następnie można prognozować przyszłe wartości metryk ze składników sezonowych i trendów, zbiorczo nazwanych według planu bazowego, i zignorować część rezydualną. Można również wykryć nietypowe wartości na podstawie analizy odstającej przy użyciu tylko części resztkowej.
Aby utworzyć model rozkładu, użyj [`series_decompose()`](/azure/kusto/query/series-decomposefunction)funkcji . Funkcja `series_decompose()` przyjmuje zestaw szeregów czasowych i automatycznie rozkłada każdą serię czasową do jej sezonowych, trendowych, resztkowych i wyjściowych składników. 

Na przykład można rozłożyć ruch wewnętrznej usługi sieci web przy użyciu następującej kwerendy:

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WQ3WrDMAyF7/sUukvCnDXJGIOVPEULuwxqoixm/gm2+jf28JObFjbYrmyho3M+yRCD1a5jaGFAJtaW8qaqX8qqLqvnYrMySYHnvxRNWT1B07xW1U03JFEzbVYDWd9Z/KAuUtAUm9UXpLJcSnAH2+LxPZe3AO9gJ6ZbRjvDGLy9EbG/BUemOXnvLxD1AOJ1mijQtWhbyHbbOgOA9RogkqGeAaXn3g1BooVb6OiDNHpD6CjAUccDGv2JrL0TSzozuQHyPYqHdqRkDKN3aBRwkJaCQJIoQ4VsuXh2A/Xezj5SWkVBWSvI0vSoOSsWpLtEpyDwY4KTW8nnJ5ws+2+eAhSyOxjkd+HDVVcIfHplp2TYTxgYTpqnnDUbarM32gPO86PY4jjqfmGw3vGkftNlCi5xNprbWW5kYvENQQnqDh8CAAA=)**\]**

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

![Time series decomposition](media/anomaly-detection/series-decompose-timechart.png)

* Oryginalna seria czasowa jest oznaczona **jako liczba** (na czerwono). 
* Proces rozpoczyna się od automatycznego wykrywania sezonowości przy użyciu funkcji [`series_periods_detect()`](/azure/kusto/query/series-periods-detectfunction) i wyodrębnia wzorzec **sezonowy** (w kolorze fioletowym).
* Wzorzec sezonowy jest odejmowany od oryginalnych szeregów czasowych, a regresja liniowa jest uruchamiana przy użyciu funkcji [`series_fit_line()`](/azure/kusto/query/series-fit-linefunction) w celu znalezienia składnika **trendu** (w kolorze jasnoniebieskim).
* Funkcja odejmuje trend, a pozostała część jest **składnikiem resztkowym** (na zielono).
* Na koniec funkcja dodaje sezonowe i trend składników do generowania **linii bazowej** (na niebiesko).

## <a name="time-series-anomaly-detection"></a>Wykrywanie anomalii szeregów czasowych

Funkcja [`series_decompose_anomalies()`](/azure/kusto/query/series-decompose-anomaliesfunction) znajduje nietypowe punkty na zestawie szeregów czasowych. Ta funkcja `series_decompose()` wywołuje, aby utworzyć model [`series_outliers()`](/azure/kusto/query/series-outliersfunction) rozkładu, a następnie działa na składniku resztkowym. `series_outliers()`oblicza wyniki anomalii dla każdego punktu resztkowego komponentu przy użyciu testu ogrodzenia Tukeya. Wyniki anomalii powyżej 1,5 lub poniżej -1,5 wskazują odpowiednio na łagodny wzrost lub spadek anomalii. Wyniki anomalii powyżej 3,0 lub poniżej -3,0 wskazują na silną anomalię. 

Następująca kwerenda umożliwia wykrywanie anomalii w wewnętrznym ruchu usługi sieci web:

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WR3W7CMAyF73mKI25KpRbaTmjSUJ8CpF1WoXVptPxUifmb9vBLoGO7GFeR7ePv2I4ihpamYdToBBNLTYuqKF/zosyLdbqZqagQl/8UVV68oKreimLSdVFUDZtZR9o2WnxQ48lJ8tXsCzHM7yHMUdfidFiEN4U12AXoloUe0Turp4nYTsaeaYzs/RVedgis80CObkFdI9ltywTAagV4UtQyRKiZgyLEaTGZ9taFQqtIGHI4SX8USn4KltYEJF2YTIeFMFaHPPkMvrWOMuxFoEpDaVjujmo6aq0erafmIY+7ZCiX6wx5mSGJHb3kJA1sF8jB8q69toNwjLPkYfGTseqoja//eLNkRXXyTnuIcVyCneh72cL2YQdtDQ8ZHvIkDcsfPWH+3AvPvObx0FMXD/RLhfDYW9VhtNKwj/8U69M1b2S//AbRUQMWQQIAAA==)**\]**

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

* Oryginalny szereg czasu (na czerwono). 
* Składnik bazowy (sezonowy + trend) (na niebiesko).
* Nietypowe punkty (w kolorze fioletowym) na wierzchu oryginalnej serii czasowej. Nietypowe punkty znacznie odbiegają od oczekiwanych wartości wyjściowych.

## <a name="time-series-forecasting"></a>Prognozowanie szeregów czasowych

Funkcja [`series_decompose_forecast()`](/azure/kusto/query/series-decompose-forecastfunction) przewiduje przyszłe wartości zestawu szeregów czasowych. Ta funkcja `series_decompose()` wywołuje do tworzenia modelu rozkładu, a następnie, dla każdej serii czasowej, ekstrapoluje składnik linii bazowej w przyszłości.

Poniższa kwerenda umożliwia przewidywanie ruchu w usłudze sieci Web w przyszłym tygodniu:

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA22QzW6DMBCE73mKuQFqKISqitSIW98gkXpEDl5iK9hG9uanUR++dqE99YRGO8x845EYRtuO0UIKJtaG8qbebMt6U9avxW41Joe4/+doyvoFTfNW14tPJlOjZqGc1w9n263crSQZ1xlxpi6Q1xSa1ReSLGcJezGtuJ7y+C3gLA6xZM/CTBi8MwshuxnkaUlGYJpS5/ETQUvEzJsiTz+ibZEd9psMQFUBgUbqGSLe7GkkpBVYygfn46EfSVjyuOpwEaN+CNbOxki6M1mZTNSLkAbOv3WSemcmF6j7vSX8dcTUlvOFsZJcFDHFx4wYnmp7JTzjplnlrHmkNvugI8Q0PYO9GAbdww0RyDjLav1XHLnBimAjEG5E5zQ7vRP284x36hOOTtxZ8Q3The8P2QEAAA==)**\]**

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

* Oryginalna metryka (na czerwono). Przyszłe wartości są brakujące i domyślnie ustawione na 0.
* Ekstrapolować składnik linii bazowej (na niebiesko), aby przewidzieć wartości w przyszłym tygodniu.

## <a name="scalability"></a>Skalowalność

Składnia języka kwerendy usługi Azure Data Explorer umożliwia pojedyncze wywołanie do przetwarzania wielu szeregów czasowych. Jego unikalna zoptymalizowana implementacja pozwala na szybką wydajność, co ma kluczowe znaczenie dla skutecznego wykrywania anomalii i prognozowania podczas monitorowania tysięcy liczników w scenariuszach w czasie zbliżonym do rzeczywistego.

Następująca kwerenda pokazuje przetwarzanie trzech szeregów czasowych jednocześnie:

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA21Qy26DMBC85yvmFlChcUirSI34ikTqETl4KVawjfDmqX587UCaHuqLtePxPLYjhtG2YpRQkom1oaQQy3Uulrl4TzezLjLk5T9GkYsViuJDiImnIqlox6F1g745W67VZqbIuMrIA1WeBk2+mH0jjvk4wh5NKU9fSbhTOItdMNmyND2awZkpIbsxyMukDM/UR8/9FV6rIEkXJqvgmsYTl7X0lISHspzvtqt5hjdxPxkeYBHA4gGKFMBiAUilIAfWja617CY1NG4ASX/FSfuj7PRNsg4ZXANz7Fj3HSGuBmOjZ5hYbcSqIBwbZpNk+iQFcQpx4/omrqLamd55qh5v41d22nIybWChOI0qQ9Cg4e5ftyE6zprbhDV3VM4/aQ/Z96/gQTahU4wsYZzlNvs11vYL3BJsCIQz0eHed/W30jz9AUEBI0ktAgAA)**\]**

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

Ten dokument zawiera szczegółowe informacje o natywnych funkcjach Eksploratora danych platformy Azure do wykrywania anomalii szeregów czasowych i prognozowania. Każda oryginalna seria czasowa jest rozłożona na sezonowe, trendowe i resztkowe składniki do wykrywania anomalii i/lub prognozowania. Te funkcje mogą być używane do scenariuszy monitorowania w czasie zbliżonym do rzeczywistego, takich jak wykrywanie usterek, konserwacja predykcyjna oraz prognozowanie popytu i obciążenia.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [funkcjach uczenia maszynowego](/azure/data-explorer/machine-learning-clustering) w Eksploratorze danych platformy Azure.
