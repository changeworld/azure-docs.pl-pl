---
title: Przewodnik po przykładzie za pomocą analizy w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Krótkie przykłady wszystkich głównych zapytań w usłudze Analytics, Narzędzia zaawansowane wyszukiwanie usługi Application Insights.
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
ms.openlocfilehash: 470779f80e998c3908cf28328cfb415d98c5e06c
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39579259"
---
# <a name="a-tour-of-analytics-in-application-insights"></a>Przewodnik po analizie w usłudze Application Insights
[Analiza](app-insights-analytics.md) jest funkcją zaawansowanej funkcji przeszukiwania [usługi Application Insights](app-insights-overview.md). Strony te opisują język zapytań usługi Log Analytics.

* **[Obejrzyj klip wideo](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Testowanie usługi Analytics na naszych symulowane dane](https://analytics.applicationinsights.io/demo)**  aplikacji nie jest wysyłania danych do usługi Application Insights jeszcze.
* **[Ściągawka SQL użytkowników](https://aka.ms/sql-analytics)**  tłumaczy idiomy najczęściej.

Weźmy przewodnik niektóre podstawowe zapytania, które ułatwią rozpoczęcie pracy.

## <a name="connect-to-your-application-insights-data"></a>Łączenie z danymi usługi Application Insights
Otwórz analizę ze swojej aplikacji [bloku przeglądu](app-insights-dashboards.md) w usłudze Application Insights:

![Otwórz portal.azure.com otwórz zasób usługi Application Insights, a następnie kliknij przycisk Analiza.](./media/app-insights-analytics-tour/001.png)

## <a name="takehttpsdocsloganalyticsiodocslanguage-referencetabular-operators-show-me-n-rows"></a>[Wykonaj](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators): Pokaż n wierszy
Punkty danych, która rejestruje operacje użytkownika (zwykle żądania HTTP odebrane przez aplikację sieci web) są przechowywane w tabeli o nazwie `requests`. Każdy wiersz jest punktem danych telemetrii otrzymane od zestawu SDK usługi Application Insights w swojej aplikacji.

Zacznijmy od badanie kilka wierszy przykładowych tabeli:

![wyniki](./media/app-insights-analytics-tour/010.png)

> [!NOTE]
> Gdzieś umieścić kursor w instrukcji przed kliknięciem przycisku z rzeczywistym użyciem. Można podzielić instrukcję przez więcej niż jeden wiersz, ale nie należy umieszczać puste wiersze w instrukcji. Puste wiersze są wygodnym sposobem zachować kilka oddzielne zapytania w oknie.
>
>

Wybierz kolumny, przeciągnij je, Grupuj według kolumn i filtrowania:

![Kliknij kolumnę zaznaczenia w prawym górnym rogu wyników](./media/app-insights-analytics-tour/030.png)

Rozwiń dowolny element, aby wyświetlić szczegółowe informacje:

![Wybierz tabelę, a następnie użyj konfigurowania kolumn](./media/app-insights-analytics-tour/040.png)

> [!NOTE]
> Kliknij nagłówek kolumny, aby zmienić kolejność wyników, które są dostępne w przeglądarce sieci web. Należy jednak pamiętać, że dla zestawu wyników dużych, liczba wierszy, pobrać w przeglądarce jest ograniczony. Po prostu sortowania w ten sposób sortuje zestaw wyników zwrócony i nie zawsze pokazano rzeczywisty elementów najwyższy ani najniższy. Aby posortować elementy niezawodne, należy użyć `top` lub `sort` operatora.
>
>

## <a name="query-across-applications"></a>Zapytania w aplikacjach
Jeśli chcesz połączyć dane z wielu aplikacji usługi Application Insights, użyj **aplikacji** słowo kluczowe, aby określić aplikację wraz z nazwą tabeli.  To zapytanie łączy żądania z dwóch różnych aplikacji przy użyciu **Unii** polecenia.


```AIQL

    union app('fabrikamstage').requests, app('fabrikamprod').requests
    
```

## <a name="tophttpsdocsloganalyticsiodocslanguage-referencetabular-operatorstop-operator-and-sorthttpsdocsloganalyticsiodocslanguage-referencetabular-operatorssort-operator"></a>[TOP](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator) i [sortowania](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator)
`take` Dzięki takiemu grupowaniu można pobrać próbkę szybkiego wyników, ale pokazuje wiersze z tabeli w losowej kolejności. Aby uzyskać widok uporządkowanym, należy użyć `top` (na przykład) lub `sort` (za pośrednictwem całej tabeli).

Pokaż pierwsze n wierszy uporządkowane według określonej kolumny:

```AIQL

    requests | top 10 by timestamp desc
```

* *Składnia:* większość operatorów mają parametry — słowo kluczowe, takie jak `by`.
* `desc` = Malejąco według `asc` = rosnąco.

![](./media/app-insights-analytics-tour/260.png)

`top...` jest więcej wydajny sposób z informacją o tym `sort ... | take...`. Firma Microsoft może być napisane tak:

```AIQL

    requests | sort by timestamp desc | take 10
```

Wynik będzie taki sam, ale aplikacja może działać nieco wolniej. (Można również napisać `order`, która jest aliasem `sort`.)

Nagłówki kolumn w widoku tabeli można również sortować wyniki na ekranie. Ale oczywiście, jeśli wcześniej używano `take` lub `top` można pobrać tylko część tabeli, klikając nagłówek kolumny zostanie tylko kolejność rekordów zostały pobrane.

## <a name="wherehttpsdocsloganalyticsiodocslanguage-referencetabular-operatorswhere-operator-filtering-on-a-condition"></a>[Gdzie](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator): filtrowanie warunku

Sprawdźmy, po prostu żądań, które spowodowało zwrócenie kodu określonego wyniku:

```AIQL

    requests
    | where resultCode  == "404"
    | take 10
```

![](./media/app-insights-analytics-tour/250.png)

`where` Operator przyjmuje wyrażenia logicznego. Poniżej przedstawiono niektóre punkty klucza o nich:

* `and`, `or`: Operatory logiczne
* `==`, `<>`, `!=` : są równe, a nie równa się
* `=~`, `!~` : bez uwzględniania wielkości liter ciągu równy i nie jest równa. Istnieje wiele więcej operatorów porównywania ciągów.

<!---Read all about [scalar expressions]().--->

### <a name="find-unsuccessful-requests"></a>Znajdź niepomyślne żądania

Przekonwertować wartości ciągu na liczbę całkowitą do użycia z większą-niż porównania:

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```
<!---
`resultCode` has type string, so we must cast it app-insights-analytics-reference.md#casts for a numeric comparison.
--->

## <a name="time"></a>Time

Domyślnie zapytania są ograniczone do ostatnich 24 godzin. Można jednak zmienić ten zakres:

![](./media/app-insights-analytics-tour/change-time-range.png)

Zastąp przedział czasu, pisząc dowolnego zapytania, który wymienia `timestamp` w klauzuli where. Na przykład:

```AIQL

    // What were the slowest requests over the past 3 days?
    requests
    | where timestamp > ago(3d)  // Override the time range
    | top 5 by duration
```

Funkcja zakres czasu jest odpowiednikiem klauzuli "where" po każdym odpowiednią wzmianką w jednej z tabel źródłowych.

`ago(3d)` oznacza, że "trzy dni temu". Inne jednostki czasu obejmują godzin (`2h`, `2.5h`), minuty (`25m`), a w ciągu kilku sekund (`10s`).

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

[Daty i godziny — dokumentacja](https://docs.loganalytics.io/docs/Language-Reference/Data-types/datetime).


## <a name="projecthttpsdocsloganalyticsiodocslanguage-referencetabular-operatorsproject-operator-select-rename-and-compute-columns"></a>[Projekt](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator): Wybierz, zmiana nazwy i kolumn obliczeniowych
Użyj [ `project` ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator) celu wybrania tylko potrzebne kolumny, które chcesz:

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-insights-analytics-tour/240.png)

Możesz również zmienić nazwę kolumny i zdefiniować nowe:

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

* Nazwy kolumn może zawierać spacje lub symboli, jeśli są one oddzielona podobnie do następującego: `['...']` lub `["..."]`
* `%` jest zwykle modulo operator.
* `1d` (to cyfrę, jedną, a następnie miał ") jest oznacza element timespan literału ciągu jednego dnia. Poniżej przedstawiono niektóre więcej literały przedział czasu: `12h`, `30m`, `10s`, `0.01s`.
* `floor` (alias `bin`) zaokrągla w dół do najbliższej wielokrotności wartości bazowej należy podać wartość. Dlatego `floor(aTime, 1s)` zaokrągla czasu w dół do najbliższej sekundy.

Wyrażenia mogą zawierać zwykłych operatorów (`+`, `-`,...), a szeroką gamę przydatnych funkcji.

## <a name="extend"></a>Rozszerzanie
Jeśli chcesz dodać kolumny z istniejącymi, użyj [ `extend` ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/extend-operator):

```AIQL

    requests
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

Za pomocą [ `extend` ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/extend-operator) mniej szczegółowe informacje, niż [ `project` ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator) Jeśli chcesz zachować istniejące kolumny.

### <a name="convert-to-local-time"></a>Konwertuj na czas lokalny

Sygnatury czasowe są zawsze w formacie UTC. Dlatego jeśli jesteś na brzegu pacyficznego USA, jest Igrzyska czasu lokalnego jest-8 godzin względem czasu UTC, może Cię zainteresować to:

```AIQL

    requests
    | top 10 by timestamp desc
    | extend localTime = timestamp - 8h
```

## <a name="summarizehttpsdocsloganalyticsiodocslanguage-referencetabular-operatorssummarize-operator-aggregate-groups-of-rows"></a>[Podsumowanie](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator): agregacji grup wierszy
`Summarize` stosuje określoną *funkcję agregacji* za pośrednictwem grupy wierszy.

Na przykład czas aplikacji sieci web, jaki zajmuje się odpowiedzi na żądanie jest zgłaszana w polu `duration`. Sprawdźmy, Średni czas odpowiedzi dla wszystkich żądań:

![](./media/app-insights-analytics-tour/410.png)

Lub firma Microsoft może dzielenie wyniku żądań inne nazwy:

![](./media/app-insights-analytics-tour/420.png)

`Summarize` zbiera punktów danych w strumieniu w grupach, dla którego `by` klauzuli ocenia równomiernie. Każda wartość w `by` wyrażenie — każda nazwa unikatowa operacji w powyższym przykładzie - skutkuje wiersz w tabeli wyników.

Lub firma Microsoft może pogrupować wyniki według porze dnia:

![](./media/app-insights-analytics-tour/430.png)

Zwróć uwagę, jak korzystamy `bin` — funkcja (zwane również `floor`). Jeśli po prostu użyliśmy `by timestamp`, każdy wiersz danych wejściowych pojawiłyby się w jego własnej grupie niewielkie. Dla dowolnego ciągłe scalar, takie jak czas lub liczb, musimy przerwać ciągłego zakresu w zarządzaniu liczbę wartości dyskretnych. `bin` — czyli po prostu znanych zaokrąglania — dół `floor` function - jest najprostszym sposobem wykonania tego zadania.

Możemy użyć tej samej techniki, aby zmniejszyć zakresów ciągów:

![](./media/app-insights-analytics-tour/440.png)

Należy zauważyć, że można użyć `name=` można ustawić nazwy kolumny wynik, w wyrażeniach agregacji lub klauzuli by.

## <a name="counting-sampled-data"></a>Zliczanie próbce danych
`sum(itemCount)` to zalecana agregacji do zliczenia zdarzenia. W wielu przypadkach: itemCount == 1, więc funkcja po prostu zliczane liczbę wierszy w grupie. Ale gdy [próbkowania](app-insights-sampling.md) jest operacji jedynie ułamek zdarzeń, oryginalnym są przechowywane jako punkty danych w usłudze Application Insights, aby dla każdego punktu danych zostanie wyświetlona `itemCount` zdarzenia.

Na przykład, jeśli próbkowania odrzuca 75%, oryginalnym zdarzenia, a następnie: itemCount == 4 w rekordach zachowanej — oznacza to, że dla każdego rekordu zachowane, były cztery oryginalnego rekordy.

Próbkowanie adaptacyjne powoduje: itemCount mogą być wyższe w okresach, gdy aplikacja jest używana często.

W związku z tym sumowania: itemCount zapewnia dobre oszacowania oryginalna liczba zdarzeń.

![](./media/app-insights-analytics-tour/510.png)

Istnieje również `count()` agregacji (i liczby operacji), w przypadkach, w której na pewno chcesz policzyć liczbę wierszy w grupie.

Istnieje szereg [funkcje agregacji](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions).

## <a name="charting-the-results"></a>Tworzenie wykresów wyników
```AIQL

    exceptions
       | summarize count=sum(itemCount)  
         by bin(timestamp, 1h)
```

Domyślnie wyniki są wyświetlane jako tabelę:

![](./media/app-insights-analytics-tour/225.png)

Możemy zrobić lepiej niż widok tabeli. Przyjrzyjmy się wyników w widoku wykresu za pomocą pionowy pasek opcji:

![Kliknij wykres, a następnie wybierz wykres słupkowy pionowy i przypisać x i y osi](./media/app-insights-analytics-tour/230.png)

Należy zauważyć, że chociaż firma Microsoft nie sortować wyniki według czasu (jak pokazano w tabeli), wykres zawsze wyświetli Data/Godzina w poprawnej kolejności.


## <a name="timecharts"></a>Timecharts
Pokaż liczbę zdarzeń są każdą godzinę używania:

```AIQL

    requests
      | summarize event_count=sum(itemCount)
        by bin(timestamp, 1h)
```

Wybierz opcję wyświetlania wykresu:

![Wykres czasu](./media/app-insights-analytics-tour/080.png)

## <a name="multiple-series"></a>Wiele serii
Wiele wyrażeń w `summarize` klauzuli tworzy wiele kolumn.

Wiele wyrażeń w `by` klauzuli tworzy wiele wierszy, po jednym dla każdej kombinacji wartości.

```AIQL

    requests
    | summarize count_=sum(itemCount), avg(duration)
      by bin(timestamp, 1h), client_StateOrProvince, client_City
    | order by timestamp asc, client_StateOrProvince, client_City
```

![Tabela żądań według godziny i lokalizacji](./media/app-insights-analytics-tour/090.png)

### <a name="segment-a-chart-by-dimensions"></a>Segmentowanie wykres według wymiarów
Jeśli na wykresie tabeli, która ma kolumny ciągów i numeryczne, ciąg można podzielić dane liczbowe na oddzielnych serii punktów. Jeśli istnieje więcej niż jednej kolumny ciągów, można wybrać kolumny do użycia jako dyskryminatora.

![Segment wykres analizy](./media/app-insights-analytics-tour/100.png)

#### <a name="bounce-rate"></a>Współczynnik odbicia

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

### <a name="display-multiple-metrics"></a>Wyświetlanie wielu metryk
Jeśli wykres tabeli, która ma więcej niż jednej kolumny liczbowe, oprócz sygnaturę czasową, można wyświetlić dowolną kombinację.

![Segment wykres analizy](./media/app-insights-analytics-tour/110.png)

Musisz wybrać **nie podziału** zanim będzie można wybrać wiele kolumn liczbowych. Nie można podzielić według kolumny ciągów, w tym samym czasie jako wyświetlanie więcej niż jednej kolumny liczbowej.

## <a name="daily-average-cycle"></a>Dzienny średni cyklu
Jak ostrzeżenie o użycia różnią się w ciągu dnia średniej?

Liczba żądań według czasu modulo jeden dzień kwanty na godziny:

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
> Należy zauważyć, że obecnie mamy przekonwertować czasów Data/Godzina, aby wyświetlić na wykresie liniowym.
>
>

## <a name="compare-multiple-daily-series"></a>Porównaj z wielu serii codzienne
Jak użycie w zależności od wraz z upływem czasu dnia w różnych krajach?

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

![Podziel według client_CountryOrRegion](./media/app-insights-analytics-tour/130.png)

## <a name="plot-a-distribution"></a>Wykreślania dystrybucji
Ile sesji istnieją o różnej długości?

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

Ostatni wiersz jest wymagana do konwertowania do daty/godziny. Obecnie osi x wykresu jest wyświetlana jako skalarną, tylko wtedy, gdy jest wartość typu datetime.

`where` Klauzuli wyklucza jednorazowej sesji (sessionDuration == 0) i ustawia długość osi x.

![](./media/app-insights-analytics-tour/290.png)

## <a name="percentileshttpsdocsloganalyticsiodocslanguage-referenceaggregation-functionspercentiles"></a>[Percentyle](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/percentiles())
Jakie zakresy czasu trwania obejmuje różne wartości procentowych sesji?

Użyj zapytania powyżej, ale zastąp ostatni wiersz:

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

Górny limit usunęliśmy także w przypadku gdy klauzulę, aby uzyskać poprawne dane, w tym wszystkich sesji z więcej niż jedno żądanie:

![wynik](./media/app-insights-analytics-tour/180.png)

Z którego można widzimy, że:

* 5% sesje mają czas trwania więcej niż trzy minuty 34s;
* 50% sesji usługi ostatni mniej niż 36 minut
* 5% sesje ostatniej dłużej niż 7 dni

Można pobrać podział osobne dla każdego kraju, możemy po prostu musisz przenieść kolumny client_CountryOrRegion oddzielnie za pośrednictwem zarówno Podsumowując operatory:

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
Mamy dostęp do kilku tabel, w tym żądań i wyjątków.

Aby znaleźć wyjątki związane z żądaniem, który zwrócił odpowiedź błędu, możemy łączenie tabl w `operation_Id`:

```AIQL

    requests
    | where toint(resultCode) >= 500
    | join (exceptions) on operation_Id
    | take 30
```


Jest dobrą praktyką było używanie `project` można wybrać tylko kolumny, których potrzebujemy przed wykonaniem sprzężenia.
W tym samym klauzulach możemy zmienić nazwy kolumny sygnatur czasowych.

## <a name="lethttpsdocsloganalyticsiodocslanguage-referencequery-statementslet-statement-assign-a-result-to-a-variable"></a>[Pozwól](https://docs.loganalytics.io/docs/Language-Reference/Query-statements/Let-statement): Przypisz wynik do zmiennej

Użyj `let` do oddzielenia części poprzedniego wyrażenia. Wyniki są bez zmian:

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

Użyj `toscalar` przekonwertować jedną komórkę z wartością:

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

Użyj *umożliwiają* do definiowania funkcji:

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
Obiekty zagnieżdżone możliwy jest łatwe. Na przykład w usłudze stream wyjątki, można wyświetlić obiektów ze strukturą następująco:

![wynik](./media/app-insights-analytics-tour/520.png)

Można jej spłaszczenia, wybierając pozycję Właściwości, które interesują Cię:

```AIQL

    exceptions | take 10
    | extend method1 = tostring(details[0].parsedStack[1].method)
```

Należy zauważyć, że musisz Rzutuj wynik metody do odpowiedniego typu.


## <a name="custom-properties-and-measurements"></a>Właściwości niestandardowe i pomiarów
Jeśli aplikacja łączy [wymiary niestandardowe (właściwości) i pomiary niestandardowe](app-insights-api-custom-events-metrics.md#properties) na zdarzenia, następnie zobaczysz je w `customDimensions` i `customMeasurements` obiektów.

Na przykład, jeśli aplikacja zawiera:

```csharp

    var dimensions = new Dictionary<string, string>
                     {{"p1", "v1"},{"p2.d2", "v2"}};
    var measurements = new Dictionary<string, double>
                     {{"m1", 42.0}, {"m2", 43.2}};
    telemetryClient.TrackEvent("myEvent", dimensions, measurements);
```

Aby wyodrębnić te wartości w usłudze Analytics:

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      m1 = todouble(customMeasurements.m1) // cast to expected type
```

Aby sprawdzić, czy wymiaru niestandardowego jest określonego typu:

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      iff(notnull(todouble(customMeasurements.m1)), ...
```

### <a name="special-characters"></a>Znaki specjalne

Przy użyciu znaków specjalnych ani słów kluczowych języka w nazwach identyfikatorów potrzebujesz dostępu do nich za pośrednictwem `['` i `']` lub za pomocą `["` i `"]`.

```AIQL

    customEvents
    | extend p2d2 = customDimensions.['p2.d2'], ...
```

[Identyfikator reguły nazewnictwa odwołania](https://docs.loganalytics.io/docs/Learn/References/Naming-principles)

## <a name="dashboards"></a>Pulpity nawigacyjne
Wyniki do pulpitu nawigacyjnego można przypiąć, aby zebrać wszystkie najważniejsze wykresów i tabel.

* [Usługa Azure udostępniony pulpit nawigacyjny](app-insights-dashboards.md#share-dashboards): kliknij ikonę pinezki. Zanim to zrobisz, konieczne jest posiadanie udostępnionego pulpitu nawigacyjnego. W witrynie Azure portal Otwórz lub Utwórz pulpit nawigacyjny, a następnie kliknij przycisk Udostępnij.
* [Pulpit nawigacyjny usługi Power BI](app-insights-export-power-bi.md): kliknij eksportu, usługa Power BI zapytania. Zaletą tej alternatywy jest wyświetlić zapytanie wraz z innych wyników z różnych źródeł.

## <a name="combine-with-imported-data"></a>Łączenie z zaimportowanymi danymi

Raport analityczny wyglądał świetnie na pulpicie nawigacyjnym, ale czasami tłumaczenie danych do arkusza formularza. Na przykład załóżmy, że użytkownicy uwierzytelnieni są identyfikowane w danych telemetrycznych za alias. Czy chcesz pokazać ich rzeczywiste nazwy w wynikach. Aby to zrobić, należy pliku CSV, który mapuje z aliasy rzeczywiste nazwy.

Można importować plik danych i używać go, podobnie jak tabele standardowe (żądania, wyjątki i tak dalej). Wykonuje zapytania samodzielnie lub przyłącz ją z innymi tabelami. Na przykład, jeśli masz tabelę o nazwie usermap i ma kolumny `realName` i `userId`, możesz go użyć do translacji `user_AuthenticatedId` pole dane telemetryczne żądania:

```AIQL

    requests
    | where notempty(user_AuthenticatedId)
    | project userId = user_AuthenticatedId
      // get the realName field from the usermap table:
    | join kind=leftouter ( usermap ) on userId
      // count transactions by name:
    | summarize count() by realName
```

Aby zaimportować tabelę, w bloku schematu w obszarze **innych źródeł danych**, postępuj zgodnie z instrukcjami, aby dodać nowe źródło danych, przekazując próbki danych. Następnie przy użyciu tej definicji do przekazania tabel.

Funkcji importu jest obecnie w wersji zapoznawczej, więc początkowo zostanie wyświetlony link "Skontaktuj się z nami" w obszarze "inne"źródła danych. Użyj tego, aby zarejestrować się do programu wersji zapoznawczej, a link zostanie następnie zastąpione przez przycisk "Dodaj nowe źródło danych".


## <a name="tables"></a>Tabele
Strumień danych telemetrycznych odebrane z aplikacji jest dostępny za pośrednictwem kilku tabel. Schemat właściwości dostępne dla każdej tabeli jest widoczny w lewej części okna.

### <a name="requests-table"></a>Tabela żądań
Żądania HTTP liczba aplikacji sieci web i posegmentuj te dane według nazwy strony:

![Liczba żądań posegmentowana według nazwy](./media/app-insights-analytics-tour/analytics-count-requests.png)

Znajdź żądań, które nie spełniają najbardziej:

![Liczba żądań posegmentowana według nazwy](./media/app-insights-analytics-tour/analytics-failed-requests.png)

### <a name="custom-events-table"></a>Tabela zdarzeń niestandardowych
Jeśli używasz [poleceń TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent) wysyłać swoje własne zdarzenia, będzie można je odczytać w tej tabeli.

Spójrzmy na przykład gdy kod aplikacji zawiera następujące wiersze:

```csharp

    telemetry.TrackEvent("Query",
       new Dictionary<string,string> {{"query", sqlCmd}},
       new Dictionary<string,double> {
           {"retry", retryCount},
           {"querytime", totalTime}})
```

Wyświetl częstotliwość tych zdarzeń:

![Wyświetlana liczba zdarzeń niestandardowych](./media/app-insights-analytics-tour/analytics-custom-events-rate.png)

Prowadzenie pomiary i wymiary zdarzenia:

![Wyświetlana liczba zdarzeń niestandardowych](./media/app-insights-analytics-tour/analytics-custom-events-dimensions.png)

### <a name="custom-metrics-table"></a>Tabela metryki niestandardowe
Jeśli używasz [TrackMetric()](app-insights-api-custom-events-metrics.md#trackmetric) do wysłania własnych wartości metryk, można znaleźć jego wyniki w **customMetrics** strumienia. Na przykład:  

![Metryki niestandardowe w usłudze Application Insights analytics](./media/app-insights-analytics-tour/analytics-custom-metrics.png)

> [!NOTE]
> W [Eksploratora metryk](app-insights-metrics-explorer.md), wszystkie pomiary niestandardowe dołączone do dowolnego typu dane telemetryczne pojawiają się razem w bloku metryk wraz z metrykami, wysyłane przy użyciu `TrackMetric()`. Ale w usłudze Analytics, pomiary niestandardowe nadal są dołączone do niezależnie od typu były przenoszone na — zdarzenia lub żądania itd. — gdy metryki wysyłane przez TrackMetric pojawiają się w ich własnych strumień danych telemetrycznych.
>
>

### <a name="performance-counters-table"></a>Tabela dotycząca liczników wydajności
[Liczniki wydajności](app-insights-performance-counters.md) przedstawiają system podstawowych metryk dla aplikacji, takich jak procesor CPU, pamięć i wykorzystanie sieci. Można skonfigurować zestaw SDK, aby wysłać dodatkowe liczniki, w tym własne niestandardowe liczniki.

**Liczniki wydajności** udostępnia schematu `category`, `counter` nazwy i `instance` nazwę każdego licznika wydajności. Nazwy wystąpienia licznika dotyczą tylko niektóre liczniki wydajności, a zwykle wskazują nazwę procesu, którego dotyczy liczby. W danych telemetrycznych dla każdej aplikacji zobaczysz tylko liczniki dla tej aplikacji. Na przykład aby zobaczyć, jakie liczniki są dostępne:

![Liczniki wydajności w usłudze Application Insights analytics](./media/app-insights-analytics-tour/analytics-performance-counters.png)

Aby wyświetlić wykres dostępnej pamięci w wybranym okresie:

![Wykres czasu pamięci, w usłudze Application Insights analytics](./media/app-insights-analytics-tour/analytics-available-memory.png)

Inne telemetrii, takie jak **liczniki wydajności** również zawiera kolumnę `cloud_RoleInstance` oznacza tożsamość komputera hosta, na którym uruchomiona jest aplikacja. Na przykład, aby porównać wydajność aplikacji na różnych komputerach:

![Wydajność posegmentowana według wystąpienia roli w usłudze Application Insights analytics](./media/app-insights-analytics-tour/analytics-metrics-role-instance.png)

### <a name="exceptions-table"></a>Tabeli wyjątków
[Wyjątki zgłoszone przez aplikację](app-insights-asp-net-exceptions.md) są dostępne w tej tabeli.

Aby znaleźć żądanie HTTP, które zostało obsługi aplikacji, gdy wyjątek został zgłoszony, odbędzie operation_Id:

![Dołącz do wyjątków z żądaniami operation_Id](./media/app-insights-analytics-tour/analytics-exception-request.png)

### <a name="browser-timings-table"></a>Tabela chronometrażu przeglądarki
`browserTimings` Pokazuje danych ładowania strony zebranych w przeglądarce użytkownika.

[Konfigurowanie aplikacji dla telemetrii po stronie klienta](app-insights-javascript.md) aby można było wyświetlić te metryki.

Zawiera schemat [metryki wskazujący długości różnych etapach procesu ładowania strony](app-insights-javascript.md#page-load-performance). (Nie pokazują czas, który użytkownicy Przeczytaj stronę.)  

Pokaż popularities różnych stron i załadować razy dla każdej strony:

![Czasy ładowania stron w usłudze Analytics](./media/app-insights-analytics-tour/analytics-page-load.png)

### <a name="availability-results-table"></a>Wyniki w tabeli Dostępność
`availabilityResults` Pokazuje wyniki Twojego [testy sieci web](app-insights-monitor-web-app-availability.md). Każde uruchomienie testów w poszczególnych lokalizacjach testowych jest zgłaszany oddzielnie.

![Czasy ładowania stron w usłudze Analytics](./media/app-insights-analytics-tour/analytics-availability.png)

### <a name="dependencies-table"></a>Tabela zależności
Zawiera wyniki wywołań, że aplikacja sprawia, że do baz danych i interfejsów API REST i inne wywołania TrackDependency(). Zawiera także wywołania AJAX w przeglądarce.

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

Zawsze pokazuj zależności po stronie serwera, wyniki `success==False` Jeśli nie zainstalowano agenta programu Application Insights. Jednak inne dane są poprawne.

### <a name="traces-table"></a>Tabela śledzenia
Zawiera telemetrii wysyłanej przez aplikację przy użyciu metody TrackTrace(), lub [innych struktur rejestrowania](app-insights-asp-net-trace-logs.md).

## <a name="video"></a>Połączenia wideo 

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

Zaawansowane zapytania:

> [!VIDEO https://channel9.msdn.com/Events/Build/2016/P591/player]


## <a name="next-steps"></a>Kolejne kroki
* [Dokumentacja języka Analytics](app-insights-analytics-reference.md)
* [Ściągawka SQL użytkowników](https://aka.ms/sql-analytics) tłumaczy idiomy najczęściej.

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]
