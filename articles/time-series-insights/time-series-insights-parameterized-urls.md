---
title: Udostępnianie widoków niestandardowych z sparametryzowanymi adresami URL — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak utworzyć sparametryzowane adresy URL, aby łatwo udostępniać niestandardowe widoki Eksploratora w Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.workload: big-data
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 9dfe499a7d6084a23fd71ab98db472befe71fc04
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024368"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Udostępnianie widoku niestandardowego przy użyciu sparametryzowanego adresu URL

Aby udostępnić widok niestandardowy w Eksploratorze Time Series Insights, można programowo utworzyć sparametryzowany adres URL widoku niestandardowego.

Eksplorator Time Series Insights obsługuje parametry zapytania URL, aby określić widoki w środowisku bezpośrednio z adresu URL. Na przykład przy użyciu tylko adresu URL możesz określić środowisko docelowe, predykat wyszukiwania i żądany przedział czasu. Gdy użytkownik wybierze dostosowany adres URL, Interfejs udostępnia link bezpośrednio do tego zasobu w portalu Time Series Insights. Obowiązują zasady dostępu do danych.

> [!TIP]
> * Obejrzyj pokaz bezpłatnej [Time Series Insights](https://insights.timeseries.azure.com/samples).
> * Przeczytaj towarzyszącą dokumentację programu [Time Series Insights Explorer](./time-series-insights-explorer.md) .

## <a name="environment-id"></a>Identyfikator środowiska

Parametr `environmentId=<guid>` określa identyfikator środowiska docelowego. Jest to składnik nazwy FQDN dostępu do danych, który można znaleźć w prawym górnym rogu omówienia środowiska w Azure Portal. Jest to cała część poprzedzająca ciąg `env.timeseries.azure.com`.

Przykładowy parametr identyfikatora środowiska to `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Czas

Za pomocą sparametryzowanego adresu URL możesz określać bezwzględne lub względne wartości czasu.

### <a name="absolute-time-values"></a>Bezwzględne wartości czasu

Aby podać bezwzględne wartości czasu, użyj parametrów `from=<integer>` i `to=<integer>`.

* Parametr `from=<integer>` to wartość w milisekundach JavaScript określająca czas początkowy dla zakresu wyszukiwania.
* Parametr `to=<integer>` to wartość w milisekundach JavaScript określająca czas końcowy dla zakresu wyszukiwania.

> [!TIP]
> Aby łatwo przetłumaczyć daty na JavaScript w milisekundach, wypróbuj [konwertera Timestamp & UNIX](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Względne wartości czasu

Dla względnej wartości czasu Użyj `relativeMillis=<value>`, gdzie *wartość* jest w języku JavaScript w milisekundach od ostatniego znacznika czasu otrzymanego z interfejsu API.

Na przykład parametr `&relativeMillis=3600000` spowoduje wyświetlenie danych z ostatnich 60 minut.

Akceptowane wartości odpowiadają menu **szybkiego czasu** Eksploratora Time Series Insights i obejmują:

* `1800000` (ostatnie 30 minut)
* `3600000` (ostatnie 60 min)
* `10800000` (ostatnie 3 godziny)
* `21600000` (ostatnie 6 godzin)
* `43200000` (ostatnie 12 godzin)
* `86400000` (ostatnie 24 godziny)
* `604800000` (ostatnie 7 dni)
* `2592000000` (ostatnie 30 godzin)

### <a name="optional-parameters"></a>Parametry opcjonalne

`timeSeriesDefinitions=<collection of term objects>` parametr określa terminy predykatu, które będą wyświetlane w widoku Time Series Insights:

| Parametr | Element adresu URL | Opis |
| --- | --- | --- |
| **Nazwa** | `\<string>` | Nazwa *terminu*. |
| **splitBy** | `\<string>` | Nazwa kolumny, według której ma zostać wykonany *podział*. |
| **measureName** | `\<string>` | Nazwa kolumny *miary*. |
| **predykatu** | `\<string>` | Klauzula *where* na potrzeby filtrowania po stronie serwera. |
| **useSum** | `true` | Opcjonalny parametr, który określa użycie sum dla miary. |

> [!NOTE]
> Jeśli `Events` jest wybrana miara **useSum** , liczba jest zaznaczona domyślnie.  
> Jeśli nie wybrano `Events`, wartość średnia jest domyślnie zaznaczona. |

* Para klucz-wartość `multiChartStack=<true/false>` umożliwia tworzenie stosów na wykresie.
* Para klucz-wartość `multiChartSameScale=<true/false>` umożliwia tej samej skali osi Y w warunkach w ramach opcjonalnego parametru.  
* `timeBucketUnit=<Unit>&timeBucketSize=<integer>` umożliwia dostosowanie suwaka interwału, aby zapewnić bardziej szczegółowy lub gładszy, bardziej Zagregowany widok wykresu.  
* Parametr `timezoneOffset=<integer>` umożliwia ustawienie strefy czasowej na potrzeby wyświetlania wykresu jako przesunięcia do czasu UTC.

| Pary | Opis |
| --- | --- |
| `multiChartStack=false` | `true` jest domyślnie włączona, więc Przekaż `false` do stosu. |
| `multiChartStack=false&multiChartSameScale=true` | Układanie na stosie musi mieć możliwość użycia takiej samej skali osi Y dla warunków.  Jest ona domyślnie `false`, więc przekazywanie `true` włącza tę funkcję. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Units = `days`, `hours`, `minutes`, `seconds`, `milliseconds`.  Jednostka zawsze wielką literą. </br> Zdefiniuj liczbę jednostek, przekazując żądaną liczbę całkowitą dla **timeBucketSize**.  |
| `timezoneOffset=-<integer>` | Liczba całkowita jest zawsze w milisekundach. |

> [!NOTE]
> wartości **timeBucketUnit** można wygładzić do 7 dni.
> wartości **timezoneOffset** nie są czasem UTC ani czasem lokalnym.

### <a name="examples"></a>Przykłady

Aby dodać definicje szeregów czasowych do środowiska Time Series Insights jako parametr adresu URL, Dołącz:

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Użyj przykładowych definicji szeregów czasowych dla:

* Identyfikator środowiska
* Ostatnie 60 minut danych
* Warunki (**F1PressureID**, **F2TempStation**i **F3VibrationPL**), które składają się na parametry opcjonalne

Można utworzyć następujący sparametryzowany adres URL dla widoku:

```URL
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![sparametryzowany adres URL programu Time Series Insights Explorer](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> Zapoznaj się z Eksploratorem na żywo przy użyciu powyższego przykładu [adresu URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]) .

Powyższy adres URL opisuje i wyświetla sparametryzowany widok Eksploratora Time Series Insights. 

* Predykaty sparametryzowane.

  [![predykaty sparametryzowane Time Series Insights Explorer.](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* Widok udostępnionego wykresu pełny.

  [![udostępniony widok pełnego wykresu.](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [wykonywać C#zapytania dotyczące danych przy użyciu programu ](time-series-insights-query-data-csharp.md).

* Dowiedz się więcej o [eksploratorze Time Series Insights](./time-series-insights-explorer.md).
