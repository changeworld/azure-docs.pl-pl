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
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: cad57e3e7e52ec291819110bab9d8d79f51e5a2f
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958158"
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

Aby zidentyfikować liczbę milisekund JavaScript dla określonej daty, zobacz [Epoch & Unix Timestamp Converter (Konwerter znaczników czasu systemu Unix i epoki)](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Względne wartości czasu

W przypadku względnej wartości czasu użyj parametru `relativeMillis=<value>`, gdzie *value* to liczba milisekund JavaScript określająca wiek danych (licząc od najnowszych) w wewnętrznej bazie danych.

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

Parametr `timeSeriesDefinitions=<collection of term objects>` określa warunki widoku Time Series Insights:

| Parametr | Element adresu URL | Opis |
| --- | --- | --- |
| **Nazwij** | `\<string>` | Nazwa *terminu*. |
| **splitBy** | `\<string>` | Nazwa kolumny, według której ma zostać wykonany *podział*. |
| **measureName** | `\<string>` | Nazwa kolumny *miary*. |
| **predykatu** | `\<string>` | Klauzula *where* na potrzeby filtrowania po stronie serwera. |
| **useSum** | `true` | Opcjonalny parametr, który określa użycie sum dla miary. </br>  Zwróć uwagę, że jeśli `Events` to wybrana miara, liczba jest domyślnie zaznaczona.  </br>  Jeśli nie wybrano `Events`, wartość średnia jest domyślnie zaznaczona. |

* Para klucz-wartość `multiChartStack=<true/false>` umożliwia tworzenie stosów na wykresie.
* Para klucz-wartość `multiChartSameScale=<true/false>` umożliwia tej samej skali osi Y w warunkach w ramach opcjonalnego parametru.  
* @No__t-0 umożliwia dostosowanie suwaka interwału, aby zapewnić bardziej szczegółowy lub płynny, bardziej Zagregowany widok wykresu.  
* Parametr `timezoneOffset=<integer>` umożliwia ustawienie strefy czasowej na potrzeby wyświetlania wykresu jako przesunięcia do czasu UTC.

| Pary | Opis |
| --- | --- |
| `multiChartStack=false` | `true` jest domyślnie włączone, więc Przekaż `false` do stosu. |
| `multiChartStack=false&multiChartSameScale=true` | Układanie na stosie musi mieć możliwość użycia takiej samej skali osi Y dla warunków.  Domyślnie jest `false`, więc przekazanie wartości "true" spowoduje włączenie tej funkcji. |
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

[@no__t — warunki Eksploratora usługi 1Time Series Insights](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

Widok pełny (łącznie z wykresem):

[@no__t — widok 1Chart](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [wykonywać C#zapytania dotyczące danych przy użyciu programu ](time-series-insights-query-data-csharp.md).

* Dowiedz się więcej o [eksploratorze Time Series Insights](./time-series-insights-explorer.md).
