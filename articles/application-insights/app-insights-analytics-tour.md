---
title: Samouczek przez analityka w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Krótki próbki wszystkie główne zapytania w module analiz, narzędzie zaawansowane wyszukiwanie usługi Application insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: bddf4a6d-ea8d-4607-8531-1fe197cc57ad
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: mbullwin
ms.openlocfilehash: 01a34eed3ea073deaaf449d28d7f8462f12b5a33
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294313"
---
# <a name="a-tour-of-analytics-in-application-insights"></a>Samouczek analizy w usłudze Application Insights
[Analiza](app-insights-analytics.md) to funkcja wyszukiwania zaawansowanego [usługi Application Insights](app-insights-overview.md). Te strony opisano język zapytań usługi Analiza dzienników.

* **[Obejrzyj klip wideo wprowadzenia](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Przetestuj Analytics na naszych danych symulowane](https://analytics.applicationinsights.io/demo)**  aplikacji nie jest wysyłania danych do usługi Application Insights jeszcze.
* **[Ściągawka SQL użytkowników](https://aka.ms/sql-analytics)**  tłumaczy idioms najczęściej.

Spójrzmy przechodzenia przez niektóre podstawowe kwerendy ułatwiających rozpoczęcie pracy.

## <a name="connect-to-your-application-insights-data"></a>Połącz z danymi usługi Application Insights
Otwórz Analytics z aplikacji [bloku omówienie](app-insights-dashboards.md) w usłudze Application Insights:

![Otwórz portal.azure.com otworzyć zasobu usługi Application Insights, a następnie kliknij przycisk Analytics.](./media/app-insights-analytics-tour/001.png)

## <a name="takehttpsdocsloganalyticsiodocslanguage-referencetabular-operators-show-me-n-rows"></a>[Podejmij](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators): Pokaż n wierszy
Punkty danych, którzy logują się operacji użytkownika (zwykle żądania HTTP odebrane przez aplikację sieci web) są przechowywane w tabeli o nazwie `requests`. Każdy wiersz jest punkt danych telemetrycznych otrzymanych od zestawu SDK usługi Application Insights w aplikacji.

Zacznijmy od badanie kilka przykładowych wiersze w tabeli:

![wyniki](./media/app-insights-analytics-tour/010.png)

> [!NOTE]
> Gdzieś umieść kursor w instrukcji przed kliknięciem przycisku Przejdź. Można podzielić instrukcję przez więcej niż jeden wiersz, ale nie należy umieszczać pustych wierszy w instrukcji. Puste wiersze są wygodny sposób zachować kilka oddzielne zapytania w oknie.
>
>

Wybierz kolumny, przeciągnij je Grupuj według kolumn i filtrowania:

![Kliknij kolumnę zaznaczenia w prawym górnym rogu wyników](./media/app-insights-analytics-tour/030.png)

Rozwiń dowolny element, aby wyświetlić szczegóły:

![Wybierz tabelę i użyj kolumn skonfigurować](./media/app-insights-analytics-tour/040.png)

> [!NOTE]
> Kliknij nagłówek kolumny, aby zmienić kolejność wyników dostępnych w przeglądarce sieci web. Należy jednak pamiętać, że dla zestawu wyników dużą liczbę wierszy pobrane w przeglądarce jest ograniczona. Sortowanie w ten sposób po prostu sortuje zestaw wyników zwrócony i nie zawsze wyświetlić rzeczywiste elementy najwyższej i najniższej. Aby posortować elementy niezawodnie, użyj `top` lub `sort` operatora.
>
>

## <a name="query-across-applications"></a>Zapytania w aplikacjach
Jeśli chcesz połączyć dane z wielu aplikacji usługi Application Insights, użyj **aplikacji** — słowo kluczowe, aby określić aplikacji wraz z nazwy tabeli.  Ta kwerenda łączy żądań z dwóch różnych aplikacji przy użyciu **Unii** polecenia.


```AIQL

    union app('fabrikamstage').requests, app('fabrikamprod').requests
    
```

## <a name="tophttpsdocsloganalyticsiodocslanguage-referencetabular-operatorstop-operator-and-sorthttpsdocsloganalyticsiodocslanguage-referencetabular-operatorssort-operator"></a>[TOP](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator) i [sortowania](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator)
`take` przydaje się uzyskać szybki próbki wyniku, ale zawiera wiersze z tabeli w losowej kolejności. Aby uporządkowane wyświetlać, użyj `top` (na przykład) lub `sort` (za pośrednictwem całej tabeli).

Pokaż pierwsze n wierszy, uporządkowanych według określonej kolumny:

```AIQL

    requests | top 10 by timestamp desc
```

* *Składnia:* większość operatorów mieć — słowo kluczowe parametrów, takich jak `by`.
* `desc` = w kolejności malejącej `asc` = rosnąca.

![](./media/app-insights-analytics-tour/260.png)

`top...` więcej możliwości wydajności z informacją o tym `sort ... | take...`. Firma Microsoft może mieć zapisane:

```AIQL

    requests | sort by timestamp desc | take 10
```

Wynik będzie taki sam, ale może działać nieco wolniej. (Można również napisać `order`, który jest aliasem `sort`.)

Nagłówki kolumn w widoku tabeli można również sortowanie wyników na ekranie. Oczywiście jeśli był używany, ale `take` lub `top` można pobrać tylko część tabeli, klikając nagłówek kolumny zostanie tylko zmienić kolejność rekordów zostały pobrane.

## <a name="wherehttpsdocsloganalyticsiodocslanguage-referencetabular-operatorswhere-operator-filtering-on-a-condition"></a>[Gdzie](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator): filtrowanie warunek

Zobaczmy, po prostu żądań, które zwróciło kod określonego wyniku:

```AIQL

    requests
    | where resultCode  == "404"
    | take 10
```

![](./media/app-insights-analytics-tour/250.png)

`where` Operator przyjmuje wyrażenie logiczne. Poniżej przedstawiono niektóre najważniejszych o nich:

* `and`, `or`: Operatory logiczne
* `==`, `<>`, `!=` : równa i nie ma wartości
* `=~`, `!~` : ciąg bez uwzględniania wielkości liter równy i różne. Istnieje wiele więcej operatorów porównywania ciągów.

<!---Read all about [scalar expressions]().--->

### <a name="find-unsuccessful-requests"></a>Znajdź żądania nie powiodło się

Konwertowanie wartości ciągu na liczba całkowita większa-niż porównania:

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```
<!---
`resultCode` has type string, so we must cast it app-insights-analytics-reference.md#casts for a numeric comparison.
--->

## <a name="time"></a>Time

Domyślnie zapytania są ograniczone do ostatniego 24 godziny. Można jednak zmienić ten zakres:

![](./media/app-insights-analytics-tour/change-time-range.png)

Zastąpienie przedział czasu pisząc każde zapytanie operacji uwzględniającą `timestamp` w klauzuli where. Na przykład:

```AIQL

    // What were the slowest requests over the past 3 days?
    requests
    | where timestamp > ago(3d)  // Override the time range
    | top 5 by duration
```

Funkcja zakresu czasu jest odpowiednikiem klauzula "where" po każdym informację o jednej tabeli źródłowej.

`ago(3d)` oznacza, że "trzy dni temu". Inne jednostki czasu obejmują godzin (`2h`, `2.5h`), minut (`25m`), a sekund (`10s`).

Inne przykłady:

```AIQL

    // Last calendar week:
    requests
    | where timestamp > startofweek(now()-7d)
        and timestamp < startofweek(now())
    | top 5 by duration

    // First hour of every day in past seven days:
    requests
    | where timestamp > ago(7d) and timestamp % 1d < 1h
    | top 5 by duration

    // Specific dates:
    requests
    | where timestamp > datetime(2016-11-19) and timestamp < datetime(2016-11-21)
    | top 5 by duration

    // Between specific day/time range
    requests
    | where timestamp > datetime(2018-05-17T17:06:19.892Z) and timestamp <= datetime(2018-05-18T17:06:19.892Z)
    | where duration > 0

```

[Daty i godziny odwołanie](https://docs.loganalytics.io/docs/Language-Reference/Data-types/datetime).


## <a name="projecthttpsdocsloganalyticsiodocslanguage-referencetabular-operatorsproject-operator-select-rename-and-compute-columns"></a>[Projekt](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator): Wybierz, Zmień nazwę, a kolumny obliczeniowe
Użyj [ `project` ](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html) do wybierania tylko kolumny, które chcesz:

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-insights-analytics-tour/240.png)

Można również zmienić nazwy kolumny i zdefiniować nowe:

```AIQL

    requests
    | top 10 by timestamp desc
    | project  
            name,
            response = resultCode,
            timestamp,
            ['time of day'] = floor(timestamp % 1d, 1s)
```

![wynik](./media/app-insights-analytics-tour/270.png)

* Nazwy kolumny może zawierać spacje lub symbole, jeśli są one oddzielona podobnie do następującej: `['...']` lub `["..."]`
* `%` jest zwykle operatora modulo.
* `1d` (to cyfrę, jedną, a następnie miał ") jest wartość typu timespan literału oznacza jeden dzień. Poniżej przedstawiono niektóre więcej literały timespan: `12h`, `30m`, `10s`, `0.01s`.
* `floor` (alias `bin`) powoduje zaokrąglenie do najbliższej wielokrotności wartości podstawowej, musisz podać wartość. Dlatego `floor(aTime, 1s)` zaokrągla czasu w dół do najbliższej sekundy.

Wyrażenia mogą zawierać zwykłych operatorów (`+`, `-`,...), a istnieje szereg przydatne funkcje.

## <a name="extend"></a>Rozszerzanie
Jeśli chcesz dodać kolumny do istniejące, użyj [ `extend` ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/extend-operator):

```AIQL

    requests
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

Przy użyciu [ `extend` ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/extend-operator) mniej szczegółowe niż [ `project` ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator) Jeśli chcesz zachować istniejące kolumny.

### <a name="convert-to-local-time"></a>Konwertuj na czas lokalny

Sygnatury czasowe są zawsze w formacie UTC. Dlatego jeśli używasz wybrzeże Pacyfiku nam jest zima, może Cię zainteresować to:

```AIQL

    requests
    | top 10 by timestamp desc
    | extend localTime = timestamp - 8h
```

## <a name="summarizehttpsdocsloganalyticsiodocslanguage-referencetabular-operatorssummarize-operator-aggregate-groups-of-rows"></a>[Podsumuj](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator): agregacji grupy wierszy
`Summarize` zastosowanie określonej *funkcji agregacji* za pośrednictwem grupy wierszy.

Na przykład czas życia aplikacji sieci web, odpowiadanie na żądanie jest zgłaszana w polu `duration`. Zobaczmy, Średni czas odpowiedzi na wszystkie żądania:

![](./media/app-insights-analytics-tour/410.png)

Lub można oddzielić wynik do żądań różne nazwy elementu:

![](./media/app-insights-analytics-tour/420.png)

`Summarize` zbiera punktów danych w strumieniu w grupach, dla którego `by` klauzuli ocenia jednakowo. Każda wartość w `by` wyrażenie - nazwy operacji unikatowy w powyższym przykładzie - powoduje wiersz w tabeli wyników.

Lub firma Microsoft może grupowania wyników według pora dnia:

![](./media/app-insights-analytics-tour/430.png)

Zwróć uwagę, jak firma Microsoft korzysta z `bin` — funkcja (alias `floor`). Jeśli będziemy używać `by timestamp`, co wejściowych wiersza pojawiłyby w niewielkim grupy. Dla dowolnego ciągłego skalarną, takie jak czas lub numery, musimy Podziel ciągły zakres na zarządzaniu liczbę wartości dyskretnych. `bin` — co jest po prostu zapoznać zaokrąglania dół `floor` funkcji — jest najprostszym sposobem, w tym celu.

Możemy użyć tę samą metodę, aby zmniejszyć zakresy ciągów:

![](./media/app-insights-analytics-tour/440.png)

Należy zauważyć, że można użyć `name=` można ustawić nazwy kolumny wynik, w wyrażeniach agregacji lub klauzuli by.

## <a name="counting-sampled-data"></a>Zliczanie próbce danych
`sum(itemCount)` jest zalecana agregacji do obliczenia zdarzenia. W wielu przypadkach wartość elementu itemCount == 1, dlatego funkcja po prostu liczy w górę liczbę wierszy w grupie. Ale jeśli [próbkowania](app-insights-sampling.md) jest operacji, tylko część oryginalnego zdarzenia są przechowywane jako punkty danych w usłudze Application Insights, aby dla każdego punktu danych, zostanie wyświetlony, `itemCount` zdarzenia.

Na przykład, jeśli próbkowania odrzuca 75% oryginalnego zdarzenia, a następnie wartość elementu itemCount == 4 w rekordach zachowanych - oznacza to, dla każdego rekordu zachowanych, były cztery oryginalnego rekordy.

Wartość elementu itemCount będzie większa w okresach, gdy aplikacja jest używana często powoduje, że adaptacyjną próbkowania.

W związku z tym sumowania wartość elementu itemCount zapewnia dobre oszacowanie oryginalna liczba zdarzeń.

![](./media/app-insights-analytics-tour/510.png)

Istnieje również `count()` agregacji (i operacji liczby), w przypadku których na pewno chcesz ustalić liczbę wierszy w grupie.

Istnieje szereg [funkcje agregacji](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions).

## <a name="charting-the-results"></a>Wykresy wyników
```AIQL

    exceptions
       | summarize count=sum(itemCount)  
         by bin(timestamp, 1h)
```

Domyślnie wyniki są wyświetlane jako tabelę:

![](./media/app-insights-analytics-tour/225.png)

Możemy lepiej niż widok tabeli. Oto wyniki w widoku wykresu z pionową pasek opcji:

![Kliknij wykres, a następnie wybrać wykres słupkowy pionowy i przypisać x i y osi](./media/app-insights-analytics-tour/230.png)

Zwróć uwagę, że chociaż firma Microsoft nie sortować wyniki wg czasu (jak widać w tabeli), wykres zawsze zawiera dat i godzin w odpowiedniej kolejności.


## <a name="timecharts"></a>Timecharts
Pokaż liczbę zdarzeń są każdej godziny:

```AIQL

    requests
      | summarize event_count=sum(itemCount)
        by bin(timestamp, 1h)
```

Wybierz opcję wyświetlania wykresu:

![timechart](./media/app-insights-analytics-tour/080.png)

## <a name="multiple-series"></a>Wiele serii
Wiele wyrażeń w `summarize` klauzuli tworzy wiele kolumn.

Wiele wyrażeń w `by` klauzuli tworzy wiele wierszy, po jednej dla każdej kombinacji wartości.

```AIQL

    requests
    | summarize count_=sum(itemCount), avg(duration)
      by bin(timestamp, 1h), client_StateOrProvince, client_City
    | order by timestamp asc, client_StateOrProvince, client_City
```

![Tabela żądań przez godzinę i lokalizację](./media/app-insights-analytics-tour/090.png)

### <a name="segment-a-chart-by-dimensions"></a>Segment wykresu wielowymiarowa
Jeśli wykresu tabeli zawierającej kolumny ciągu i numeryczne, ciąg można podzielić dane liczbowe na oddzielnych serii punktów. Jeśli istnieje więcej niż jednej kolumny typu string, można wybrać kolumnę, która ma być używana jako rozróżniacza.

![Segment wykres analizy](./media/app-insights-analytics-tour/100.png)

#### <a name="bounce-rate"></a>Odbijanie szybkości

Konwertuj wartość logiczną na ciąg, aby go użyć jako dyskryminatora:

```AIQL

    // Bounce rate: sessions with only one page view
    requests
    | where notempty(session_Id)
    | where tostring(operation_SyntheticSource) == "" // real users
    | summarize pagesInSession=sum(itemCount), sessionEnd=max(timestamp)
               by session_Id
    | extend isbounce= pagesInSession == 1
    | summarize count()
               by tostring(isbounce), bin (sessionEnd, 1h)
    | render timechart
```

### <a name="display-multiple-metrics"></a>Wyświetlania wielu metryk
Jeśli wykresu tabeli, która ma więcej niż jednej kolumny liczbowe, oprócz timestamp, można wyświetlić dowolną kombinację.

![Segment wykres analizy](./media/app-insights-analytics-tour/110.png)

Musisz wybrać **nie podziału** zanim będzie można wybrać wiele kolumn liczbowych. Nie można podzielić według kolumny ciąg, w tym samym czasie jako wyświetlanie więcej niż jednej kolumny liczbowej.

## <a name="daily-average-cycle"></a>Cykl średni dzienny
Sposób użycia różne za pośrednictwem typowego dnia?

Liczba żądań w czasie modulo jeden dzień, binned na godziny:

```AIQL

    requests
    | where timestamp > ago(30d)  // Override "Last 24h"
    | where tostring(operation_SyntheticSource) == "" // real users
    | extend hour = bin(timestamp % 1d , 1h)
          + datetime("2016-01-01") // Allow render on line chart
    | summarize event_count=sum(itemCount) by hour
```

![Wykres liniowy godzin w typowego dnia](./media/app-insights-analytics-tour/120.png)

> [!NOTE]
> Zwróć uwagę, że obecnie musimy przekonwertować okresach czasu dat i godzin, aby wyświetlić na wykresie wiersza.
>
>

## <a name="compare-multiple-daily-series"></a>Porównywanie wielu serii codziennie
Jak czy użycia różnią się od wraz z upływem czasu dnia w różnych krajach?

```AIQL

     requests  
     | where timestamp > ago(30d)  // Override "Last 24h"
     | where tostring(operation_SyntheticSource) == "" // real users
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=sum(itemCount)
       by hour, client_CountryOrRegion
     | render timechart
```

![Podziel przez client_CountryOrRegion](./media/app-insights-analytics-tour/130.png)

## <a name="plot-a-distribution"></a>Wykreślenia dystrybucji
Ile sesji są dostępne różne długości?

```AIQL

    requests
    | where timestamp > ago(30d) // override "Last 24h"
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sessionDuration = max_timestamp - min_timestamp
    | where sessionDuration > 1s and sessionDuration < 3m
    | summarize count() by floor(sessionDuration, 3s)
    | project d = sessionDuration + datetime("2016-01-01"), count_
```

Ostatni wiersz jest wymagany do przekonwertowania na typ datetime. Obecnie osi x wykresu jest wyświetlana jako skalarnej tylko wtedy, gdy jest wartość datetime.

`where` Klauzuli wyklucza jednorazowej sesji (sessionDuration == 0) i ustawia długość osi x.

![](./media/app-insights-analytics-tour/290.png)

## <a name="percentileshttpsdocsloganalyticsiodocslanguage-referenceaggregation-functionspercentiles"></a>[Percentylu](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/percentiles())
Jakie zakresów czasu trwania obejmują różne wartości procentowe sesji?

Użyj powyższego zapytania, ale zastępuje ostatni wiersz:

```AIQL

    requests
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sesh = max_timestamp - min_timestamp
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s)
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
```

Górny limit usunęliśmy także w przypadku, gdy klauzula, aby uzyskać prawidłowe dane, w tym wszystkie sesje z więcej niż jedno żądanie:

![wynik](./media/app-insights-analytics-tour/180.png)

Z którego możemy stwierdzić, że:

* 5% sesji ma czasu trwania z więcej niż trzy minuty 34s;
* 50% sesji ostatni 36 minut;
* 5% sesji ostatnie więcej niż 7 dni

Uzyskanie podział osobne dla każdego kraju, możemy tylko muszą wprowadzić kolumny client_CountryOrRegion z osobna za pomocą obu Podsumuj operatory:

```AIQL

    requests
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id, client_CountryOrRegion
    | extend sesh = max_timestamp - min_timestamp
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s), client_CountryOrRegion
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
      by client_CountryOrRegion
```

![](./media/app-insights-analytics-tour/190.png)

## <a name="join"></a>Join
Mamy dostęp do kilku tabel, w tym żądania i wyjątki.

Aby znaleźć wyjątki związane z żądania, który zwrócił odpowiedź awarii, firma Microsoft może dołączyć do tabel na `session_Id`:

```AIQL

    requests
    | where toint(resultCode) >= 500
    | join (exceptions) on operation_Id
    | take 30
```


Jest dobrym rozwiązaniem jest użycie `project` zaznacz tylko kolumny, potrzebujemy przed wykonaniem sprzężenia.
W tym samym klauzulach możemy zmienić nazwy kolumny znaczników czasu.

## <a name="lethttpsdocsloganalyticsiodocslanguage-referencequery-statementslet-statement-assign-a-result-to-a-variable"></a>[Let](https://docs.loganalytics.io/docs/Language-Reference/Query-statements/Let-statement): Przypisz wynik do zmiennej

Użyj `let` do rozdzielania części poprzedniego wyrażenia. Wyniki są bez zmian:

```AIQL

    let bad_requests =
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id
    | take 30
```

> [!Tip] 
> W kliencie Analytics nie umieszczaj puste wiersze między części zapytania. Upewnij się, że wszystkie jego wykonania.
>

Użyj `toscalar` można przekonwertować na wartość jedną komórkę tabeli:

```AIQL
let topCities =  toscalar (
   requests
   | summarize count() by client_City 
   | top n by count_ 
   | summarize makeset(client_City));
requests
| where client_City in (topCities(3)) 
| summarize count() by client_City;
```


### <a name="functions"></a>Funkcje

Użyj *Let* Aby zdefiniować funkcję:

```AIQL

    let usdate = (t:datetime)
    {
      strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
      bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
    };
    requests  
    | extend PST = usdate(timestamp-8h)
```

## <a name="accessing-nested-objects"></a>Uzyskiwanie dostępu do obiektów zagnieżdżonych
Zagnieżdżone obiekty są łatwo dostępne. Na przykład w strumieniu wyjątków można wyświetlić obiekty strukturalne następująco:

![wynik](./media/app-insights-analytics-tour/520.png)

Można jej spłaszczenia, wybierając pozycję Właściwości, które chcesz:

```AIQL

    exceptions | take 10
    | extend method1 = tostring(details[0].parsedStack[1].method)
```

Należy pamiętać, że należy rzutować wyniku do odpowiedniego typu.


## <a name="custom-properties-and-measurements"></a>Właściwości niestandardowe i pomiarów
Jeśli aplikacja łączy [niestandardowych wymiarów (właściwości) i niestandardowych miar](app-insights-api-custom-events-metrics.md#properties) do zdarzeń, zostanie wyświetlona je w `customDimensions` i `customMeasurements` obiektów.

Na przykład, jeśli aplikacja zawiera:

```csharp

    var dimensions = new Dictionary<string, string>
                     {{"p1", "v1"},{"p2.d2", "v2"}};
    var measurements = new Dictionary<string, double>
                     {{"m1", 42.0}, {"m2", 43.2}};
    telemetryClient.TrackEvent("myEvent", dimensions, measurements);
```

Aby wyodrębnić te wartości w module analiz:

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      m1 = todouble(customMeasurements.m1) // cast to expected type
```

Aby sprawdzić, czy wymiar niestandardowe określonego typu:

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      iff(notnull(todouble(customMeasurements.m1)), ...
```

### <a name="special-characters"></a>Znaki specjalne

Znaki specjalne lub słowa kluczowe języka w nazwach identyfikatorów należy dostępu do nich za pośrednictwem `['` i `']` lub przy użyciu `["` i `"]`.

```AIQL

    customEvents
    | extend p2d2 = customDimensions.['p2.d2'], ...
```

[Identyfikator reguły nazewnictwa odwołania](https://docs.loganalytics.io/docs/Learn/References/Naming-principles)

## <a name="dashboards"></a>Pulpity nawigacyjne
Wyniki do pulpitu nawigacyjnego można przypiąć w celu zebrania razem wszystkich najważniejszych wykresów i tabel.

* [Azure udostępnionego pulpitu nawigacyjnego](app-insights-dashboards.md#share-dashboards): kliknij ikonę przypinania. Zanim to zrobisz, musisz mieć udostępnionego pulpitu nawigacyjnego. W portalu Azure otworzyć lub utworzyć pulpit nawigacyjny, a następnie kliknij przycisk Udostępnij.
* [Pulpit nawigacyjny programu Power BI](app-insights-export-power-bi.md): kliknij przycisk Eksportuj, Power BI zapytania. Zaletą to alternatywne jest, że można wyświetlić zapytania równolegle z innymi wyniki pochodzące z różnorodnych źródeł.

## <a name="combine-with-imported-data"></a>Łączenie z importowanych danych

Raporty analizy wygląda świetnie na pulpicie nawigacyjnym, ale czasami mają zostać przetłumaczone danych do arkusza formularza. Na przykład załóżmy, że uwierzytelnieni użytkownicy są objęci telemetrii jako alias. Chcesz wyświetlić swoje rzeczywiste nazwy w wynikach. Aby to zrobić, należy mapowanego z aliasów rzeczywistej nazwy pliku CSV.

Możesz zaimportować plik danych i używać go tak samo jak standardowe tabele (żądań, wyjątków i tak dalej). Zapytanie ją samodzielnie albo przyłączenie jej z innych tabel. Na przykład, jeśli tabela o nazwie usermap i zawiera kolumn `realName` i `userId`, możesz go użyć do tłumaczenia `user_AuthenticatedId` w dane telemetryczne żądania:

```AIQL

    requests
    | where notempty(user_AuthenticatedId)
    | project userId = user_AuthenticatedId
      // get the realName field from the usermap table:
    | join kind=leftouter ( usermap ) on userId
      // count transactions by name:
    | summarize count() by realName
```

Aby zaimportować tabelę, w bloku schematu w obszarze **inne źródła danych**, postępuj zgodnie z instrukcjami, aby dodać nowego źródła danych, przekazując przykładowych danych. Następnie możesz użyć tej definicji, aby przekazać tabel.

Funkcja importowania jest obecnie w przeglądzie, dlatego początkowo łącze "Skontaktuj się z nami" w obszarze "Innych źródeł danych." Służy do Zarejestruj się, aby ten program w wersji zapoznawczej, a łącze następnie zostaną zastąpione przycisk "Dodaj nowe źródło danych".


## <a name="tables"></a>Tabele
Strumień danych telemetrycznych otrzymywanych z aplikacji jest dostępna za pośrednictwem kilku tabel. Schemat właściwości dostępne dla każdej tabeli jest widoczny w lewej części okna.

### <a name="requests-table"></a>Tabela żądań
Liczba HTTP żądania do aplikacji sieci web i segmentu, według nazwy strony:

![Liczba żądań segmentem według nazwy](./media/app-insights-analytics-tour/analytics-count-requests.png)

Znajdź żądania, które nie są najbardziej:

![Liczba żądań segmentem według nazwy](./media/app-insights-analytics-tour/analytics-failed-requests.png)

### <a name="custom-events-table"></a>Tabela zdarzeń niestandardowych
Jeśli używasz [funkcji TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent) wysyłać własne zdarzenia, będzie można je odczytać z tej tabeli.

Spójrzmy na przykład gdy kodu aplikacji zawiera następujące wiersze:

```csharp

    telemetry.TrackEvent("Query",
       new Dictionary<string,string> {{"query", sqlCmd}},
       new Dictionary<string,double> {
           {"retry", retryCount},
           {"querytime", totalTime}})
```

Częstotliwość tych zdarzeń do wyświetlenia:

![Częstotliwość wyświetlania zdarzeń niestandardowych](./media/app-insights-analytics-tour/analytics-custom-events-rate.png)

Wyodrębnij miar i wymiary ze zdarzeń:

![Częstotliwość wyświetlania zdarzeń niestandardowych](./media/app-insights-analytics-tour/analytics-custom-events-dimensions.png)

### <a name="custom-metrics-table"></a>Metryki niestandardowe tabeli
Jeśli używasz [TrackMetric()](app-insights-api-custom-events-metrics.md#trackmetric) do wysyłania wartości metryki, można znaleźć jego wyniki w **customMetrics** strumienia. Na przykład:  

![Metryki niestandardowe w module analiz usługi Application Insights](./media/app-insights-analytics-tour/analytics-custom-metrics.png)

> [!NOTE]
> W [Eksploratora metryk](app-insights-metrics-explorer.md), wszystkich miar niestandardowych dołączony do dowolnego typu danych telemetrycznych występować razem w bloku metryki wraz z metryki wysyłane przy użyciu `TrackMetric()`. Jednak w module analiz, miary niestandardowe nadal są dołączone do niezależnie od typu danych telemetrycznych były przenoszone na — zdarzenia lub żądania i tak dalej — gdy metryki wysyłane przez TrackMetric pojawiają się w ich własnych strumienia.
>
>

### <a name="performance-counters-table"></a>Tabela liczniki wydajności
[Liczniki wydajności](app-insights-performance-counters.md) przedstawiają podstawowego systemu metryki dla aplikacji, na przykład procesora CPU, pamięci i wykorzystanie sieci. Można skonfigurować zestaw SDK, aby wysłać dodatkowych liczników, w tym własne niestandardowe liczniki.

**Liczniki wydajności** schemat przedstawia `category`, `counter` nazwa, i `instance` nazwę każdego licznika wydajności. Nazwy wystąpień liczników dotyczą tylko niektóre liczniki wydajności i zwykle wskazuje nazwę procesu, do którego odnosi się wartość licznika. W danych telemetrycznych dla każdej aplikacji zostanie wyświetlony tylko liczniki dla tej aplikacji. Na przykład aby zobaczyć, jakie liczniki są dostępne:

![Liczniki wydajności w module analiz usługi Application Insights](./media/app-insights-analytics-tour/analytics-performance-counters.png)

Aby wyświetlić wykres dostępnej pamięci w wybranym okresie:

![Timechart pamięci w module analiz usługi Application Insights](./media/app-insights-analytics-tour/analytics-available-memory.png)

Inne telemetrii, takich jak **liczniki wydajności** również zawiera kolumnę `cloud_RoleInstance` wskazujące tożsamość komputer hosta, na którym jest uruchomiona aplikacja. Na przykład, aby porównać wydajności aplikacji na różnych komputerach:

![Wydajność segmentowanych przez wystąpienie roli w usłudze Application Insights analityka](./media/app-insights-analytics-tour/analytics-metrics-role-instance.png)

### <a name="exceptions-table"></a>Tabeli wyjątków
[Wyjątki zgłoszone przez aplikację](app-insights-asp-net-exceptions.md) są dostępne w tej tabeli.

Aby znaleźć żądanie HTTP, które aplikacji został obsługi, gdy wyjątek został zgłoszony, Dołącz do operation_Id:

![Dołącz do wyjątków z żądaniami operation_Id](./media/app-insights-analytics-tour/analytics-exception-request.png)

### <a name="browser-timings-table"></a>Tabela chronometrażu przeglądarki
`browserTimings` przedstawia dane ładowania stron zebrane w przeglądarce użytkownika.

[Konfigurowanie aplikacji dla telemetrii po stronie klienta](app-insights-javascript.md) aby zobaczyć te metryki.

Schemat zawiera [metryki wskazujący długości różne etapy procesu ładowania strony](app-insights-javascript.md#page-load-performance). (Nie wskazują one czas użytkownikom odczytu strony.)  

Pokaż popularities różnych stron i załadować razy dla każdej strony:

![Czas ładowania strony w module analiz](./media/app-insights-analytics-tour/analytics-page-load.png)

### <a name="availability-results-table"></a>W tabeli wyników dostępność
`availabilityResults` Pokazuje wyniki z [testów sieci web](app-insights-monitor-web-app-availability.md). Każdy Uruchom testy z każdej lokalizacji testu jest zgłaszana oddzielnie.

![Czas ładowania strony w module analiz](./media/app-insights-analytics-tour/analytics-availability.png)

### <a name="dependencies-table"></a>Tabela zależności
Zawiera wyniki wywołania aplikacji sprawia, że do baz danych i interfejsów API REST, czy inne wywołań TrackDependency(). Zawiera również wywołania AJAX w przeglądarce.

Wywołania AJAX w przeglądarce:

```AIQL

    dependencies | where client_Type == "Browser"
    | take 10
```

Wywołania zależności z serwera:

```AIQL

    dependencies | where client_Type == "PC"
    | take 10
```

Zawsze pokazuj wyniki zależności po stronie serwera `success==False` Jeśli nie zainstalowano agenta programu Application Insights. Jednak inne dane są prawidłowe.

### <a name="traces-table"></a>Tabela danych śledzenia
Zawiera dane telemetryczne, wysyłane przez aplikację przy użyciu TrackTrace(), lub [innych platform rejestrowania](app-insights-asp-net-trace-logs.md).

## <a name="video"></a>Połączenia wideo 

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

Zaawansowane zapytania:

> [!VIDEO https://channel9.msdn.com/Events/Build/2016/P591/player]


## <a name="next-steps"></a>Kolejne kroki
* [Dokumentacja języka analityka](app-insights-analytics-reference.md)
* [Ściągawka SQL użytkowników](https://aka.ms/sql-analytics) tłumaczy idioms najczęściej.

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]
