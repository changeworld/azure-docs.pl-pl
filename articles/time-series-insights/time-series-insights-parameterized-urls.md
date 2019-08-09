---
title: Udostępnianie widoków niestandardowych usługi Azure Time Series Insights za pomocą sparametryzowanych adresów URL | Microsoft Docs
description: W tym artykule opisano sposób opracowywania sparametryzowanych adresów URL w usłudze Azure Time Series Insights na potrzeby łatwego udostępniania widoku klienta.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.topic: conceptual
ms.workload: big-data
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: ce8c74e5c194dbd971ecb65659c4fc8a7150146d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882924"
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

## <a name="time"></a>Time

Za pomocą sparametryzowanego adresu URL możesz określać bezwzględne lub względne wartości czasu.

### <a name="absolute-time-values"></a>Bezwzględne wartości czasu

Aby podać bezwzględne wartości czasu, użyj parametrów `from=<integer>` i `to=<integer>`.

* Parametr `from=<integer>` to wartość w milisekundach JavaScript określająca czas początkowy dla zakresu wyszukiwania.
* Parametr `to=<integer>` to wartość w milisekundach JavaScript określająca czas końcowy dla zakresu wyszukiwania.

Aby zidentyfikować liczbę milisekund JavaScript dla określonej daty, zobacz [Epoch & Unix Timestamp Converter (Konwerter znaczników czasu systemu Unix i epoki)](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Względne wartości czasu

W przypadku względnej wartości czasu użyj parametru `relativeMillis=<value>`, gdzie *value* to liczba milisekund JavaScript określająca wiek danych (licząc od najnowszych) w wewnętrznej bazie danych.

Na przykład parametr `&relativeMillis=3600000` spowoduje wyświetlenie danych z ostatnich 60 minut.

Akceptowane wartości odpowiadają menu **szybkiego czasu** Eksploratora Time Series Insights i obejmują:

* `1800000`(Ostatnie 30 min)
* `3600000`(Ostatnie 60 min)
* `10800000`(Ostatnie 3 godziny)
* `21600000`(Ostatnie 6 godzin)
* `43200000`(Ostatnie 12 godzin)
* `86400000`(Ostatnie 24 godziny)
* `604800000`(Ostatnie 7 dni)
* `2592000000`(Ostatnie 30 godzin)

### <a name="optional-parameters"></a>Parametry opcjonalne

`timeSeriesDefinitions=<collection of term objects>` Parametr określa warunki widoku Time Series Insights:

| Parametr | Element adresu URL | Opis |
| --- | --- | --- |
| **name** | `\<string>` | Nazwa *terminu*. |
| **splitBy** | `\<string>` | Nazwa kolumny, według której ma zostać wykonany *podział*. |
| **measureName** | `\<string>` | Nazwa kolumny *miary*. |
| **predykatu** | `\<string>` | Klauzula *where* na potrzeby filtrowania po stronie serwera. |
| **useSum** | `true` | Opcjonalny parametr, który określa użycie sum dla miary. </br>  Zwróć uwagę, `Events` jeśli jest wybrana miara, liczba jest zaznaczona domyślnie.  </br>  Jeśli `Events` nie jest zaznaczone, wartość średnia jest domyślnie zaznaczona. |

* Para `multiChartStack=<true/false>` klucz-wartość umożliwia tworzenie stosów na wykresie.
* Para `multiChartSameScale=<true/false>` klucz-wartość umożliwia tej samej skali osi Y w warunkach w ramach opcjonalnego parametru.  
* `timeBucketUnit=<Unit>&timeBucketSize=<integer>` Umożliwia dostosowanie suwaka interwał, aby zapewnić bardziej szczegółowy lub gładszy, bardziej Zagregowany widok wykresu.  
* `timezoneOffset=<integer>` Parametr umożliwia ustawienie strefy czasowej na potrzeby wyświetlania wykresu jako przesunięcia do czasu UTC.

| Pary | Opis |
| --- | --- |
| `multiChartStack=false` | `true`jest domyślnie włączona, dlatego należy `false` przejść do stosu. |
| `multiChartStack=false&multiChartSameScale=true` | Układanie na stosie musi mieć możliwość użycia takiej samej skali osi Y dla warunków.  To ustawienie `false` jest domyślnie, więc wartość "true" powoduje włączenie tej funkcji. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Jednostki = dni, godziny, minuty, sekundy, milisekundy.  Jednostka zawsze wielką literą. </br> Zdefiniuj liczbę jednostek, przekazując żądaną liczbę całkowitą do timeBucketSize.  Zapamiętaj, że zostało wygładzonych do 7 dni.  |
| `timezoneOffset=-<integer>` | Liczba całkowita jest zawsze w milisekundach. </br> Należy pamiętać, że ta funkcja jest nieco inna niż ta, która została włączona w Eksploratorze Time Series Insights, w którym można wybrać lokalny (czas przeglądarki) lub UTC. |

### <a name="examples"></a>Przykłady

Aby dodać definicje szeregów czasowych do środowiska Time Series Insights jako parametr adresu URL, Dołącz:

```plaintext
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Użyj przykładowych definicji szeregów czasowych dla:

* Identyfikator środowiska
* Ostatnie 60 minut danych
* Warunki (F1PressureID, F2TempStation i F3VibrationPL), które składają się na parametry opcjonalne

Można utworzyć następujący sparametryzowany adres URL dla widoku:

```plaintext
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

> [!TIP]
> Zapoznaj się z Eksploratorem na żywo [przy użyciu adresu URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]).

Powyższy adres URL opisuje i kompiluje widok Eksploratora Time Series Insights:

[![Warunki Eksploratora Time Series Insights](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

Widok pełny (łącznie z wykresem):

[![Widok wykresu](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [wykonywać C#zapytania dotyczące danych przy użyciu programu ](time-series-insights-query-data-csharp.md).

* Dowiedz się więcej o [eksploratorze Time Series Insights](./time-series-insights-explorer.md).
