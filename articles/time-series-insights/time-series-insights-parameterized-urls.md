---
title: Udostępnianie widoków niestandardowych usługi Azure Time Series Insights za pomocą sparametryzowanych adresów URL | Microsoft Docs
description: W tym artykule opisano sposób opracowywania sparametryzowanych adresów URL w usłudze Azure Time Series Insights na potrzeby łatwego udostępniania widoku klienta.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: conceptual
ms.workload: big-data
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: dfc04397b1d7e9f3256810cbe469067ae52c99bd
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66238977"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Udostępnianie widoku niestandardowego przy użyciu sparametryzowanego adresu URL

Aby udostępnić widok niestandardowy w Eksplorator usługi Time Series Insights, można programowo utworzyć sparametryzowany adres URL widoku niestandardowego.

Eksplorator usługi Time Series Insights obsługuje parametry zapytań URL możesz określić widoki w środowisku bezpośrednio z adresu URL. Na przykład przy użyciu tylko adresu URL możesz określić środowisko docelowe, predykat wyszukiwania i żądany przedział czasu. Gdy użytkownik kliknie dostosowany adres URL, interfejs udostępni link bezpośrednio do tego zasobu w portalu usługi Time Series Insights. Obowiązują zasady dostępu do danych.

> [!TIP]
> * Wyświetl bezpłatne [pokaz usługi Time Series Insights](https://insights.timeseries.azure.com/samples).
> * Przeczytaj towarzyszącego [Eksplorator usługi Time Series Insights](./time-series-insights-explorer.md) dokumentacji.

## <a name="environment-id"></a>Identyfikator środowiska

Parametr `environmentId=<guid>` określa identyfikator środowiska docelowego. Jest to składnik nazwy FQDN dostępu do danych i można go znaleźć w prawym górnym rogu przeglądu środowiska w witrynie Azure portal. Jest to cała część poprzedzająca ciąg `env.timeseries.azure.com`.

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

Akceptowane wartości odpowiadają wartościom Eksploratora usługi Time Series Insights **skrócić czas** menu i obejmują:

* `1800000` (Ostatnie 30 min)
* `3600000` (Ostatnie 60 minut)
* `10800000` (Ostatnie 3 godziny)
* `21600000` (Ostatnie 6 godzin)
* `43200000` (Ostatnie 12 godzin)
* `86400000` (Ostatnie 24 godziny)
* `604800000` (Ostatnie 7 dni)
* `2592000000` (Ostatnie 30 godzin)

### <a name="optional-parameters"></a>Parametry opcjonalne

`timeSeriesDefinitions=<collection of term objects>` Parametr określa terminy widoku usługi Time Series Insights:

| Parametr | Adres URL elementu | Opis |
| --- | --- | --- |
| **name** | `\<string>` | Nazwa *terminu*. |
| **splitBy** | `\<string>` | Nazwa kolumny, według której ma zostać wykonany *podział*. |
| **measureName** | `\<string>` | Nazwa kolumny *miary*. |
| **Predykat** | `\<string>` | Klauzula *where* na potrzeby filtrowania po stronie serwera. |
| **useSum** | `true` | Opcjonalny parametr określający użycie sumy dla miary. </br>  Uwaga: Jeśli `Events` wybrana miara, domyślnie jest zaznaczona liczba.  </br>  Jeśli `Events` jest nie jest zaznaczone, domyślnie jest zaznaczona średnia. |

* `multiChartStack=<true/false>` Pary klucz wartość umożliwia umieszczanie na stosie na wykresie.
* `multiChartSameScale=<true/false>` Pary klucz wartość umożliwia takiej samej skali osi y dla warunków w ramach parametru opcjonalnego.  
* `timeBucketUnit=<Unit>&timeBucketSize=<integer>` Umożliwia dostosowanie suwaka odstępu, aby zapewnić bardziej szczegółowy lub płynniejszy, bardziej zagregowany widok wykresu.  
* `timezoneOffset=<integer>` Parametr umożliwia ustawienie strefy czasowej dla wykresu, która jest wyświetlana jako przesunięcie względem czasu UTC.

| Par | Opis |
| --- | --- |
| `multiChartStack=false` | `true` jest domyślnie włączona, więc przekazać `false` na stosie. |
| `multiChartStack=false&multiChartSameScale=true` | Układanie na stosie musi mieć możliwość użycia takiej samej skali osi Y dla warunków.  Ma ona `false` domyślnie, więc przekazanie wartości "true" włącza tę funkcję. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Jednostki = dni, godziny, minuty, sekundy, milisekundy.  Jednostka zawsze wielką literą. </br> Zdefiniuj liczbę jednostek, przekazując żądaną liczbę całkowitą do timeBucketSize.  Zapamiętaj, że zostało wygładzonych do 7 dni.  |
| `timezoneOffset=-<integer>` | Liczba całkowita jest zawsze w milisekundach. </br> Uwaga: Ta funkcja jest nieco inna niż, którą włączyliśmy w Eksploratorze usługi Time Series Insights, gdzie umożliwiliśmy wybranie lokalnego (czasu przeglądarki) lub czasu UTC. |

### <a name="examples"></a>Przykłady

Aby dodać definicje szeregów czasowych do środowiska usługi Time Series Insights jako parametr adresu URL, należy dołączyć:

```plaintext
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Użyj przykładowych definicji szeregów czasowych dla:

* Identyfikator środowiska
* Ostatnie 60 minut danych
* Terminy (F1PressureID, F2TempStation i F3VibrationPL) składające się na następujące parametry opcjonalne

Możesz utworzyć następujący sparametryzowany adres URL dla widoku:

```plaintext
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

> [!TIP]
> Zobacz Explorer live [przy użyciu adresu URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]).

Powyżej adresem URL w tym artykule opisano i tworzy widok Eksplorator usługi Time Series Insights:

[![Terminy Eksploratora usługi Czas Series Insights](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

Pełny widok (w tym wykres):

[![Widok wykresu](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [kwerendy danych przy użyciu C# ](time-series-insights-query-data-csharp.md).

* Dowiedz się więcej o [Time Series Insights Explorer](./time-series-insights-explorer.md).
