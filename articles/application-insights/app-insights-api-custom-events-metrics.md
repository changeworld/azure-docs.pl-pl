---
title: Aplikacji interfejsu API Insights dla niestandardowych zdarzeń i metryk | Dokumentacja firmy Microsoft
description: Wstaw kilku wierszy kodu w aplikacji pulpitu lub urządzenia, strona sieci Web lub usługi, śledzenie użycia i diagnozowanie problemów.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 80400495-c67b-4468-a92e-abf49793a54d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/17/2017
ms.author: mbullwin
ms.openlocfilehash: 072ce2952e3cdea47b02ef7656ca67d4bc0ae8f1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>Aplikacji interfejsu API Insights dla niestandardowych zdarzeń i metryk

Wstaw kilku wierszy kodu w aplikacji, aby dowiedzieć się, co robią użytkownicy z nim lub do zdiagnozowania problemów. Może wysłać dane telemetryczne z aplikacji urządzenia i pulpitu, klientów w sieci web i serwerów sieci web. Użyj [Azure Application Insights](app-insights-overview.md) podstawowe interfejsu API telemetrii do wysyłania niestandardowych zdarzeń i metryk i własnych wersji standard telemetrii. Ten interfejs API jest tego samego interfejsu API Użyj standardowe moduły zbierające dane usługi Application Insights.

## <a name="api-summary"></a>Podsumowanie interfejsu API
Interfejs API jest jednolita na wszystkich platformach, oprócz kilku małe różnice.

| Metoda | Używany do |
| --- | --- |
| [`TrackPageView`](#page-views) |Stron, ekrany, bloków lub formularzy. |
| [`TrackEvent`](#trackevent) |Akcje użytkownika oraz inne zdarzenia. Używane do śledzenia zachowania użytkownika lub do monitorowania wydajności. |
| [`TrackMetric`](#trackmetric) |Wskaźników wydajności, takich jak długości kolejki nie dotyczą określonych zdarzeń. |
| [`TrackException`](#trackexception) |Wyjątki rejestrowania diagnostyki. Gdy występują inne zdarzenia i sprawdź śladów stosu śledzenia. |
| [`TrackRequest`](#trackrequest) |Rejestrowanie częstotliwość i czas trwania żądania serwera do analizy wydajności. |
| [`TrackTrace`](#tracktrace) |Komunikaty dziennika diagnostycznego. Istnieje również możliwość przechwytywania dzienniki innych firm. |
| [`TrackDependency`](#trackdependency) |Rejestrowanie, czas trwania i częstotliwość wywołania składników zewnętrznych, które zależy od aplikacji. |

Możesz [dołączanie właściwości i metryki](#properties) najczęściej tych wywołań telemetrii.

## <a name="prep"></a>Przed rozpoczęciem
Jeśli to odwołanie nie ma jeszcze na zestaw SDK usługi Application Insights:

* Dodaj zestaw SDK usługi Application Insights do projektu:

  * [Projektu programu ASP.NET](app-insights-asp-net.md)
  * [Projekt języka Java](app-insights-java-get-started.md)
  * [Projekt środowiska node.js](app-insights-nodejs.md)
  * [Język JavaScript w każdej strony sieci Web](app-insights-javascript.md) 
* W kodzie serwera sieci web lub urządzenia obejmują:

    *C#:* `using Microsoft.ApplicationInsights;`

    *Visual Basic:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`
    
    *Node.js:* `var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Pobierz wystąpienia TelemetryClient
Pobranie wystąpienia `TelemetryClient` (z wyjątkiem w języku JavaScript, na stronach sieci Web):

*C#*

    private TelemetryClient telemetry = new TelemetryClient();

*Visual Basic*

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();
    
*Node.js*

    var telemetry = applicationInsights.defaultClient;


TelemetryClient jest wątkowo.

Dla projektów ASP.NET i Java automatycznie są przechwytywane przychodzące żądania HTTP. Możesz utworzyć dodatkowe wystąpienia TelemetryClient inny moduł aplikacji. Na przykład może mieć jedno wystąpienie TelemetryClient w klasie oprogramowanie pośredniczące do zgłaszania zdarzeń logiki biznesowej. Można ustawić właściwości, takie jak nazwa użytkownika i DeviceId do identyfikowania maszyny. Te informacje jest dołączony do wszystkich zdarzeń, które wysyła instace. 

*C#*

    TelemetryClient.Context.User.Id = "...";
    TelemetryClient.Context.Device.Id = "...";

*Java*

    telemetry.getContext().getUser().setId("...);
    telemetry.getContext().getDevice().setId("...");

W projektach Node.js, można użyć `new applicationInsights.TelemetryClient(instrumentationKey?)` do utworzenia nowego wystąpienia, ale jest zalecane tylko w scenariuszach wymagających konfiguracji odizolowane od singleton `defaultClient`.

## <a name="trackevent"></a>TrackEvent
W usłudze Application Insights *zdarzenie niestandardowe* jest punkt danych, które można wyświetlić w [Eksploratora metryk](app-insights-metrics-explorer.md) jako zagregowanej liczby, a następnie w [diagnostycznych wyszukiwania](app-insights-diagnostic-search.md) jako poszczególnych wystąpień. (Nie jest związany z nią MVC lub innych framework "zdarzenia.")

Wstaw `TrackEvent` wywołuje kod w celu liczba różnych zdarzeń. Jak często użytkownicy wybrać określonej funkcji, jak często one celach określonego lub może być częstotliwość tworzenia poszczególnych typów błędów.

Na przykład w aplikacji gry, należy wysłać zdarzenie w przypadku gry wins użytkownika:

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*

    telemetry.TrackEvent("WinGame");

*Visual Basic*

    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");
    
*Node.js*

    telemetry.trackEvent({name: "WinGame"});

### <a name="view-your-events-in-the-microsoft-azure-portal"></a>Wyświetl zdarzeń w portalu Microsoft Azure
Aby wyświetlić liczbę zdarzeń, otwórz [Eksploratora metryk](app-insights-metrics-explorer.md) bloku, Dodaj nowy wykres i wybierz **zdarzenia**.  

![Liczba zdarzeń niestandardowych w temacie](./media/app-insights-api-custom-events-metrics/01-custom.png)

Aby porównać liczbę różnych zdarzeń, Ustaw typ wykresu **siatki**i grupę o nazwie zdarzenia:

![Ustaw typ wykresu i grupowania](./media/app-insights-api-custom-events-metrics/07-grid.png)

W siatce kliknij nazwę zdarzenia, aby wyświetlić poszczególnych wystąpień tego zdarzenia. Aby zobaczyć więcej szczegółów - kliknij każde zdarzenie na liście.

![Drążenie wskroś zdarzenia](./media/app-insights-api-custom-events-metrics/03-instances.png)

Aby skupić się na określonych zdarzeń w wyszukiwania lub Eksploratora metryk, ustaw bloku filtru nazwy zdarzenia, które chcesz:

![Otwórz filtrów, rozwiń nazwę zdarzenia, a następnie wybierz co najmniej jedną wartość](./media/app-insights-api-custom-events-metrics/06-filter.png)

### <a name="custom-events-in-analytics"></a>Niestandardowe zdarzenia w module analiz

Dane telemetryczne są dostępne w `customEvents` tabeli w [Application Insights Analytics](app-insights-analytics.md). Każdy wiersz reprezentuje wywołanie `trackEvent(..)` w aplikacji. 

Jeśli [próbkowania](app-insights-sampling.md) jest używany w operacji, właściwości wartość elementu itemCount wskazuje wartość większą niż 1. Na przykład wartość elementu itemCount == 10 oznacza, że 10 wywołania funkcji trackEvent(), proces pobierania próbek jedynie przesyłane, jeden z nich. Aby uzyskać poprawny liczba zdarzeń niestandardowych, należy używać w związku z tym kod wykorzystania, takich jak `customEvent | summarize sum(itemCount)`.


## <a name="trackmetric"></a>TrackMetric

Usługa Application Insights można wykresu metryk, które nie są dołączone do określonego zdarzenia. Na przykład można monitorować długość kolejki w regularnych odstępach czasu. O metryki pojedynczych pomiarów mogą być przydatne, mniej niż zmian i trendów i wykresy tak statystyczne są przydatne.

Aby można było wysłać metryk usługi Application Insights, można użyć `TrackMetric(..)` interfejsu API. Istnieją dwa sposoby wysyłania metryki: 

* Pojedyncza wartość. Za każdym razem, gdy miary są wykonywane w aplikacji, możesz wysłać odpowiadającej jej wartości do usługi Application Insights. Załóżmy na przykład, że masz metrykę opisujące liczba elementów w kontenerze. W określonym czasie umieść trzy elementy do kontenera., a następnie usuń dwa elementy. W związku z tym spowodowałoby wywołanie `TrackMetric` dwa razy: najpierw przekazując wartość `3` , a następnie wartość `-2`. Usługa Application Insights przechowuje obie wartości w Twoim imieniu. 

* Agregacji. Podczas pracy z metryki, co pojedynczego pomiaru jest rzadko. Zamiast tego ważne jest podsumowanie co się stało w określonym przedziale czasu. Taki skrót jest nazywany _agregacji_. W powyższym przykładzie łączna suma metryki dla tego okresu jest `1` liczba wartości metryki `2`. Korzystając z metody agregacji, można tylko wywołać `TrackMetric` raz na okres czasu i wysyłania wartości zagregowanych. Jest to zalecane podejście, ponieważ go znacznie zmniejszyć koszty i wydajność narzut wysyłając mniej punktów danych do usługi Application Insights podczas nadal zbierania wszystkich odpowiednich informacji.

### <a name="examples"></a>Przykłady:

#### <a name="single-values"></a>Pojedynczych wartości

Aby wysłać pojedynczą wartość metryki:

*JavaScript*

 ```Javascript
     appInsights.trackMetric("queueLength", 42.0);
 ```

*C#*

```csharp
    var sample = new MetricTelemetry();
    sample.Name = "metric name";
    sample.Value = 42.3;
    telemetryClient.TrackMetric(sample);
```

*Java*

```Java
    
    telemetry.trackMetric("queueLength", 42.0);

```

*Node.js*

 ```Javascript
     telemetry.trackMetric({name: "queueLength", value: 42.0});
 ```

#### <a name="aggregating-metrics"></a>Agregowanie metryk

Zaleca się łączny metryki przed ich wysłaniem z aplikacji, aby ograniczyć przepustowość, kosztu i zwiększyć wydajność.
Oto przykład agregację kodu:

*C#*

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;

namespace MetricAggregationExample
{
    /// <summary>
    /// Aggregates metric values for a single time period.
    /// </summary>
    internal class MetricAggregator
    {
        private SpinLock _trackLock = new SpinLock();

        public DateTimeOffset StartTimestamp    { get; }
        public int Count                        { get; private set; }
        public double Sum                       { get; private set; }
        public double SumOfSquares              { get; private set; }
        public double Min                       { get; private set; }
        public double Max                       { get; private set; }
        public double Average                   { get { return (Count == 0) ? 0 : (Sum / Count); } }
        public double Variance                  { get { return (Count == 0) ? 0 : (SumOfSquares / Count)
                                                                                  - (Average * Average); } }
        public double StandardDeviation         { get { return Math.Sqrt(Variance); } }

        public MetricAggregator(DateTimeOffset startTimestamp)
        {
            this.StartTimestamp = startTimestamp;
        }

        public void TrackValue(double value)
        {
            bool lockAcquired = false;

            try
            {
                _trackLock.Enter(ref lockAcquired);

                if ((Count == 0) || (value < Min))  { Min = value; }
                if ((Count == 0) || (value > Max))  { Max = value; }
                Count++;
                Sum += value;
                SumOfSquares += value * value;
            }
            finally
            {
                if (lockAcquired)
                {
                    _trackLock.Exit();
                }
            }
        }
    }   // internal class MetricAggregator

    /// <summary>
    /// Accepts metric values and sends the aggregated values at 1-minute intervals.
    /// </summary>
    public sealed class Metric : IDisposable
    {
        private static readonly TimeSpan AggregationPeriod = TimeSpan.FromSeconds(60);

        private bool _isDisposed = false;
        private MetricAggregator _aggregator = null;
        private readonly TelemetryClient _telemetryClient;

        public string Name { get; }

        public Metric(string name, TelemetryClient telemetryClient)
        {
            this.Name = name ?? "null";
            this._aggregator = new MetricAggregator(DateTimeOffset.UtcNow);
            this._telemetryClient = telemetryClient ?? throw new ArgumentNullException(nameof(telemetryClient));

            Task.Run(this.AggregatorLoopAsync);
        }

        public void TrackValue(double value)
        {
            MetricAggregator currAggregator = _aggregator;
            if (currAggregator != null)
            {
                currAggregator.TrackValue(value);
            }
        }

        private async Task AggregatorLoopAsync()
        {
            while (_isDisposed == false)
            {
                try
                {
                    // Wait for end end of the aggregation period:
                    await Task.Delay(AggregationPeriod).ConfigureAwait(continueOnCapturedContext: false);

                    // Atomically snap the current aggregation:
                    MetricAggregator nextAggregator = new MetricAggregator(DateTimeOffset.UtcNow);
                    MetricAggregator prevAggregator = Interlocked.Exchange(ref _aggregator, nextAggregator);

                    // Only send anything is at least one value was measured:
                    if (prevAggregator != null && prevAggregator.Count > 0)
                    {
                        // Compute the actual aggregation period length:
                        TimeSpan aggPeriod = nextAggregator.StartTimestamp - prevAggregator.StartTimestamp;
                        if (aggPeriod.TotalMilliseconds < 1)
                        {
                            aggPeriod = TimeSpan.FromMilliseconds(1);
                        }

                        // Construct the metric telemetry item and send:
                        var aggregatedMetricTelemetry = new MetricTelemetry(
                                Name,
                                prevAggregator.Count,
                                prevAggregator.Sum,
                                prevAggregator.Min,
                                prevAggregator.Max,
                                prevAggregator.StandardDeviation);
                        aggregatedMetricTelemetry.Properties["AggregationPeriod"] = aggPeriod.ToString("c");

                        _telemetryClient.Track(aggregatedMetricTelemetry);
                    }
                }
                catch(Exception ex)
                {
                    // log ex as appropriate for your application
                }
            }
        }

        void IDisposable.Dispose()
        {
            _isDisposed = true;
            _aggregator = null;
        }
    }   // public sealed class Metric
}
```

### <a name="custom-metrics-in-metrics-explorer"></a>Metryki niestandardowe w Eksploratorze metryk

Aby wyświetlić wyniki, otwórz Eksploratora metryk i dodać nowego wykresu. Edytuj wykres pokazanie Twoje metryki.

> [!NOTE]
> Twoje niestandardowa Metryka może potrwać kilka minut, aby znajdują się na liście dostępne metryki.
>

![Dodawanie nowego wykresu lub wybierz wykres, a w obszarze niestandardowe, wybierz Twoje metryki](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

### <a name="custom-metrics-in-analytics"></a>Metryki niestandardowe w module analiz

Dane telemetryczne są dostępne w `customMetrics` tabeli w [Application Insights Analytics](app-insights-analytics.md). Każdy wiersz reprezentuje wywołanie `trackMetric(..)` w aplikacji.
* `valueSum` — Jest to suma pomiarów. Aby uzyskać wartość średnią, podzielić przez `valueCount`.
* `valueCount` -Liczba pomiarów, które zostały zagregowane w to `trackMetric(..)` wywołania.

## <a name="page-views"></a>Wyświetlenia strony
W aplikacji lub strony sieci Web urządzenia po załadowaniu każdej ekranu lub strony, domyślnie jest wysyłane dane telemetryczne wyświetleń strony. Ale można zmienić, aby śledzić wyświetleń strony w czasie dodatkowych lub innych. Na przykład w aplikacji, która zawiera tabulatory lub bloków, możesz śledzić strony, kiedy użytkownik otwiera nowy blok.

![Obiektyw użycia w bloku — omówienie](./media/app-insights-api-custom-events-metrics/appinsights-47usage-2.png)

Dane użytkownika i sesji jest wysyłany jako właściwości wraz z wyświetleń strony, dlatego wykresów użytkownika i sesji pochodzić aktywności, po dane telemetryczne wyświetleń strony.

### <a name="custom-page-views"></a>Widoki niestandardowe strony
*JavaScript*

    appInsights.trackPageView("tab1");

*C#*

    telemetry.TrackPageView("GameReviewPage");

*Java*

    telemetry.trackPageView("GameReviewPage");

*Visual Basic*

    telemetry.TrackPageView("GameReviewPage")


Jeśli masz kilka kart w różnych stron HTML, można określić adres URL za:

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");

### <a name="timing-page-views"></a>Liczba wyświetleń strony chronometrażu
Domyślnie godziny są zgłaszane jako **czas ładowania strony widoku** są mierzone od, gdy przeglądarka wysyła żądanie, dopóki zdarzeń ładowania strony w przeglądarce jest wywoływana.

Zamiast tego możesz:

* Ustaw jawne czasu trwania [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview) wywołania: `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`.
* Użyj widoku strony chronometrażu wywołania `startTrackPage` i `stopTrackPage`.

*JavaScript*

    // To start timing a page:
    appInsights.startTrackPage("Page1");

Przyciski ...

    // To stop timing and log the page:
    appInsights.stopTrackPage("Page1", url, properties, measurements);

Nazwa, której użyjesz jako pierwszy parametr kojarzy wywołania rozpoczęcia i zakończenia. Domyślnie nazwa bieżącej strony.

Wynikowa okresów obciążenia strony wyświetlany w Eksploratorze metryk pochodzą od interwału wywołania rozpoczęcia i zakończenia. Jest możesz jakie interwał czasu rzeczywistego.

### <a name="page-telemetry-in-analytics"></a>Dane telemetryczne strony w module analiz

W [Analytics](app-insights-analytics.md) dwóch tabel wyświetlane dane z operacji przeglądarki:

* `pageViews` Tabela zawiera dane dotyczące adresu URL i tytuł strony
* `browserTimings` Tabela zawiera dane o wydajności klienta, takie jak czas potrzebny do przetwarzania przychodzących danych

Aby dowiedzieć się, jak długo trwa przeglądarki do przetwarzania na różnych stronach:

```
browserTimings | summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name 
```

Aby odnaleźć popularities różnych przeglądarek:

```
pageViews | summarize count() by client_Browser
```

Aby skojarzyć wyświetleń strony do wywołania AJAX, Dołącz do społeczności zależności:

```
pageViews | join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest
Zestaw SDK używa TrackRequest do rejestrowania żądań HTTP.

Należy także wywołać ją samodzielnie aby symulować żądania w kontekście gdzie nie ma uruchomionego modułu usługi sieci web.

Jednak zalecanym sposobem wysłać dane telemetryczne żądania jest, gdy żądanie działa jako <a href="#operation-context">kontekstu operacji</a>.

## <a name="operation-context"></a>Operacja kontekstu
Elementy danych telemetrycznych można skorelować razem można skojarzyć je z kontekstem operacji. Standardowy moduł żądania śledzenia robi to wyjątków i inne zdarzenia, które są wysyłane podczas przetwarzania żądania HTTP. W [wyszukiwania](app-insights-diagnostic-search.md) i [Analytics](app-insights-analytics.md), łatwo można znaleźć żadnych zdarzeń skojarzony z żądaniem za pomocą jej identyfikatora operacji

Zobacz [korelacji Telemetrii w usłudze Application Insights](application-insights-correlation.md) uzyskać dodatkowe szczegóły dotyczące korelacji.

Podczas śledzenia telemetrii ręcznie, najprostszym sposobem zapewnienia korelacji telemetrii przy użyciu tego wzorca:

*C#*

```csharp
// Establish an operation context and associated telemetry item:
using (var operation = telemetryClient.StartOperation<RequestTelemetry>("operationName"))
{
    // Telemetry sent in here will use the same operation ID.
    ...
    telemetryClient.TrackTrace(...); // or other Track* calls
    ...
    // Set properties of containing telemetry item--for example:
    operation.Telemetry.ResponseCode = "200";

    // Optional: explicitly send telemetry item:
    telemetryClient.StopOperation(operation);

} // When operation is disposed, telemetry item is sent.
```

Wraz z ustawienie kontekstu operacji `StartOperation` tworzy element telemetrii typu, który określisz. Podczas usuwania operacji lub jawnie wywołać wysyła dane telemetryczne elementu `StopOperation`. Jeśli używasz `RequestTelemetry` jako typ danych telemetrycznych jego czas trwania ustawiono interwał czasu między rozpoczęcie i zakończenie.

Elementy dane telemetryczne zgłoszone w zakresie operacji stają się "dzieci" takich operacji. Konteksty operacji może być zagnieżdżone. 

W polu Wyszukaj kontekstu operacji służy do tworzenia **elementy pokrewne** listy:

![Elementy pokrewne](./media/app-insights-api-custom-events-metrics/21.png)

Zobacz [śledzenie działań niestandardowych z zestawu SDK .NET usługi Application Insights](application-insights-custom-operations-tracking.md) uzyskać więcej informacji o niestandardowych operacji śledzenia.

### <a name="requests-in-analytics"></a>Żądań w module analiz 

W [Application Insights Analytics](app-insights-analytics.md), żądań Pokaż się w `requests` tabeli.

Jeśli [próbkowania](app-insights-sampling.md) jest w operacji właściwości wartość elementu itemCount wyświetli wartość większą niż 1. Na przykład wartość elementu itemCount == 10 oznacza, że 10 wywołań trackRequest(), proces pobierania próbek jedynie przesyłane, jeden z nich. Aby uzyskać poprawną liczbę żądań i Średni czas trwania segmentowanych przez żądanie nazwy, takie jak użyć kodu:

```AIQL
requests | summarize count = sum(itemCount), avgduration = avg(duration) by name
```


## <a name="trackexception"></a>TrackException
Wyślij wyjątków do usługi Application Insights:

* Aby [policzyć](app-insights-metrics-explorer.md), jako wskaźnik częstotliwości problem.
* Aby [Sprawdź indywidualne wystąpienia](app-insights-diagnostic-search.md).

Raporty zawierają dane śledzenia stosu.

*C#*

    try
    {
        ...
    }
    catch (Exception ex)
    {
       telemetry.TrackException(ex);
    }

*Java*

    try {
        ...
    } catch (Exception ex) {
        telemetry.trackException(ex);
    }

*JavaScript*

    try
    {
       ...
    }
    catch (ex)
    {
       appInsights.trackException(ex);
    }
    
*Node.js*

    try
    {
       ...
    }
    catch (ex)
    {
       telemetry.trackException({exception: ex});
    }

Zestawy SDK automatycznie, catch wiele wyjątków, dzięki czemu nie zawsze trzeba jawnie wywołać TrackException.

* ASP.NET: [napisać kod, aby przechwytywać wyjątki](app-insights-asp-net-exceptions.md).
* J2EE: [wyjątki są przechwytywane automatycznie](app-insights-java-get-started.md#exceptions-and-request-failures).
* JavaScript: Wyjątki są przechwytywane automatycznie. Jeśli chcesz wyłączyć automatyczne zbieranie, Dodaj linię do fragment kodu, który zostanie wstawiona strony sieci Web:

    ```
    ({
      instrumentationKey: "your key"
      , disableExceptionTracking: true
    })
    ```

### <a name="exceptions-in-analytics"></a>Wyjątki w module analiz

W [Application Insights Analytics](app-insights-analytics.md), wyjątki widoczne w `exceptions` tabeli.

Jeśli [próbkowania](app-insights-sampling.md) jest używany w operacji, `itemCount` właściwość wskazuje wartość większą niż 1. Na przykład wartość elementu itemCount == 10 oznacza, że 10 wywołań funkcji trackexception(), proces pobierania próbek jedynie przesyłane, jeden z nich. Aby uzyskać poprawny liczba wyjątków segmentowanych przez typ wyjątku, takie jak użyć kodu:

```
exceptions | summarize sum(itemCount) by type
```

Większość informacji ważnych stosu już jest wyodrębniany do oddzielnych zmiennych, ale można ściągnąć od siebie `details` struktury, aby uzyskać więcej. Ponieważ ta struktura jest dynamiczny, należy rzutować wynik, który ma typ oczekiwane. Na przykład:

```AIQL
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Aby skojarzyć wyjątki z ich powiązanego żądania, należy użyć sprzężenia:

```
exceptions
| join (requests) on operation_Id 
```

## <a name="tracktrace"></a>TrackTrace
Użyj TrackTrace do diagnozowania problemów, wysyłając "ślad nawigacją" do usługi Application Insights. Umożliwia wysyłanie fragmentów danych diagnostycznych i sprawdzać ich w [diagnostycznych wyszukiwania](app-insights-diagnostic-search.md).

W środowisku .NET [logowania karty](app-insights-asp-net-trace-logs.md) ten interfejs API umożliwia wysyłanie dzienników innych firm do portalu.

W języku Java dla [Log4J, Logback, takich jak standardowe rejestratorów](app-insights-java-trace-logs.md) umożliwia wysyłanie dzienników innych firm do portalu Application Insights Log4j lub Logback Appenders.

*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);

*Java*

    telemetry.trackTrace(message, SeverityLevel.Warning, properties);
    
*Node.js*

    telemetry.trackTrace({message: message, severity:applicationInsights.Contracts.SeverityLevel.Warning, properties:properties});


Można wyszukiwać w treści wiadomości, ale (w przeciwieństwie do wartości właściwości) nie można filtrować na nim.

Limit rozmiaru na `message` jest znacznie wyższa niż limit właściwości.
Zaletą TrackTrace jest umieszczenie danych stosunkowo długo w komunikacie. Na przykład można zakodować danych POST.  

Ponadto można dodać poziomu ważności do wiadomości. I, podobnie jak inne dane telemetryczne, można dodać wartości właściwości, które ułatwiają filtr lub wyszukiwania dla różnych zestawów danych śledzenia. Na przykład:

*C#*

```C#
    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });
```

*Java*

```Java

    Map<String, Integer> properties = new HashMap<>();
    properties.put("Database", db.ID);
    telemetry.trackTrace("Slow Database response", SeverityLevel.Warning, properties);

```

W [wyszukiwania](app-insights-diagnostic-search.md), można następnie łatwo odfiltrować wszystkie komunikaty poziomu ważności w szczególności, które odnoszą się do określonej bazy danych.


### <a name="traces-in-analytics"></a>Dane śledzenia w analityka

W [Application Insights Analytics](app-insights-analytics.md), wywołania TrackTrace widoczne w `traces` tabeli.

Jeśli [próbkowania](app-insights-sampling.md) jest używany w operacji, właściwości wartość elementu itemCount wskazuje wartość większą niż 1. Na przykład wartość elementu itemCount == 10 oznacza, że 10 wywołań `trackTrace()`, proces pobierania próbek przesyłane tylko jeden z nich. Aby uzyskać poprawny liczba wywołań śledzenia, należy używać w związku z tym kod takich jak `traces | summarize sum(itemCount)`.

## <a name="trackdependency"></a>TrackDependency
Użyj wywołania TrackDependency do śledzenia czasów odpowiedzi i odsetka pomyślnych wywołań zewnętrznego fragmentu kodu. Wyniki są wyświetlane na wykresach zależności w portalu.

*C#*

```csharp
var success = false;
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
try
{
    success = dependency.Call();
}
finally
{
    timer.Stop();
    telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
}
```

*Java*

```Java
    boolean success = false;
    long startTime = System.currentTimeMillis();
    try {
        success = dependency.call();
    }
    finally {
        long endTime = System.currentTimeMillis();
        long delta = endTime - startTime;
        RemoteDependencyTelemetry dependencyTelemetry = new RemoteDependencyTelemetry("My Dependency", "myCall", delta, success);
        telemetry.setTimeStamp(startTime);
        telemetry.trackDependency(dependencyTelemetry);
    }

```

*JavaScript*

```Javascript
var success = false;
var startTime = new Date().getTime();
try
{
    success = dependency.Call();
}
finally
{
    var elapsed = new Date() - startTime;
    telemetry.trackDependency({dependencyTypeName: "myDependency", name: "myCall", duration: elapsed, success:success});
}
```

Należy pamiętać, że zestawy SDK serwera obejmuje [modułu zależności](app-insights-asp-net-dependencies.md) która wykrywa i śledzi pewne wywołania zależności automatycznie — na przykład do baz danych i interfejsów API REST. Należy zainstalować agenta na serwerze, aby utworzyć moduł pracy. 

W języku Java, niektóre wywołania zależności można automatycznie śledzić za pomocą [agenta Java](app-insights-java-agent.md).

To wywołanie jest użyć, jeśli chcesz śledzić wywołania, które nie catch automatycznego śledzenia lub jeśli nie chcesz zainstalować agenta.

Aby wyłączyć standardowy moduł śledzenia zależności w języku C#, należy edytować [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) i Usuń odwołanie do `DependencyCollector.DependencyTrackingTelemetryModule`. W języku Java nie Zainstaluj agenta java, jeśli nie chcesz zbierać standardowe zależności automatycznie.

### <a name="dependencies-in-analytics"></a>Zależności w module analiz

W [Application Insights Analytics](app-insights-analytics.md), trackDependency wywołuje Pokaż w `dependencies` tabeli.

Jeśli [próbkowania](app-insights-sampling.md) jest używany w operacji, właściwości wartość elementu itemCount wskazuje wartość większą niż 1. Na przykład wartość elementu itemCount == 10 oznacza, że 10 wywołań trackDependency(), proces pobierania próbek jedynie przesyłane, jeden z nich. Aby uzyskać poprawną liczbę zależności segmentowanych przez składnik docelowy, takich jak użyć kodu:

```
dependencies | summarize sum(itemCount) by target
```

Aby skojarzyć zależności z ich powiązane żądań, należy użyć sprzężenia:

```
dependencies
| join (requests) on operation_Id 
```

## <a name="flushing-data"></a>Opróżnienie danych
Zwykle zestaw SDK wysyła dane wybrane w czasie, aby zminimalizować wpływ na użytkownika. Jednak w niektórych przypadkach możesz chcieć opróżniania buforu — na przykład, jeśli używasz zestawu SDK w aplikacji, która kończy pracę.

*C#*
 
 ```C#
    telemetry.Flush();
    // Allow some time for flushing before shutdown.
    System.Threading.Thread.Sleep(5000);
```

*Java*

```Java
    telemetry.flush();
    //Allow some time for flushing before shutting down
    Thread.sleep(5000);
```

    
*Node.js*

    telemetry.flush();

Należy pamiętać, że funkcja asynchronicznego dla [kanału dane telemetryczne serwera](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/).

W idealnym przypadku metody flush() używanego w działaniu zamknięcia aplikacji.

## <a name="authenticated-users"></a>Użytkownicy uwierzytelnieni
W aplikacji sieci web użytkownicy są (domyślnie) identyfikowani na podstawie plików cookie. Użytkownik może być traktowane więcej niż raz, jeśli uzyskują oni dostęp do aplikacji z innego komputera lub przeglądarki lub one usunąć pliki cookie.

Jeśli logowania użytkownika do aplikacji, możesz uzyskać dokładniejsze liczba, przez ustawienie Identyfikatora uwierzytelnionego użytkownika w kodzie przeglądarki:

*JavaScript*

```JS
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

W sieci web platformy ASP.NET MVC aplikacji, na przykład:

*Razor*

        @if (Request.IsAuthenticated)
        {
            <script>
                appInsights.setAuthenticatedUserContext("@User.Identity.Name
                   .Replace("\\", "\\\\")"
                   .replace(/[,;=| ]+/g, "_"));
            </script>
        }

Nie trzeba używać użytkownika rzeczywistej nazwy logowania. Tylko musi to być identyfikator jest unikatowy dla tego użytkownika. Nie może zawierać spacji ani znaków `,;=|`.

Identyfikator użytkownika jest również ustawiona w pliku cookie sesji i wysłane do serwera. Jeśli jest zainstalowany zestaw SDK, Identyfikatora uwierzytelnionego użytkownika są przesyłane w ramach właściwości kontekstu telemetrii klienta i serwera. Następnie można filtrować i wyszukaj w nim.

Jeśli aplikacja grupy użytkowników do kont, można również przekazać identyfikator konta (za pomocą tego samego ograniczenia dotyczące znaków).

      appInsights.setAuthenticatedUserContext(validatedId, accountId);

W [Eksploratora metryk](app-insights-metrics-explorer.md), można utworzyć wykres obliczającej **uwierzytelnieni użytkownicy,**, i **kont użytkowników**.

Możesz również [wyszukiwania](app-insights-diagnostic-search.md) dla punktów danych klienta z nazwami użytkowników i kont.

## <a name="properties"></a>Filtrowanie, wyszukiwanie i podzielenie danych za pomocą właściwości
Umożliwia dołączanie właściwości i pomiarów do zdarzeń (i również do metryki, strony widoki, wyjątków i innych danych telemetrycznych).

*Właściwości* są wartości ciągu, których można użyć do filtrowania telemetrii w raportach użycia. Na przykład jeśli aplikacja udostępnia kilka gier, możesz dołączyć nazwa gry do każdego zdarzenia, tak aby były widoczne, które gry są bardziej popularne.

Ma limitu 8192 na długość ciągu. (Jeśli chcesz wysłać duże zestawy danych, należy użyć parametru komunikat [TrackTrace](#track-trace).)

*Metryki* są wartości liczbowe, które mogą być prezentowane w postaci graficznej. Na przykład można sprawdzić, czy jest stopniowego zwiększenia wyniki, które osiągnięcia Twojej graczy. Wykresy można być segmentowanych przez właściwości, które są wysyłane z zdarzeń, tak, aby uzyskać oddzielne lub skumulowany wykresy dotyczące różnych gier.

Dla wartości metryki wyświetlane prawidłowo powinna być większa lub równa 0.

Brak niektórych [limity liczby właściwości, wartości właściwości i metryki](#limits) pomocne.

*JavaScript*

    appInsights.trackEvent
      ("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

    appInsights.trackPageView
        ("page name", "http://fabrikam.com/pageurl.html",
          // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );


*C#*

    // Set up some properties and metrics:
    var properties = new Dictionary <string, string>
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var metrics = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics);

*Node.js*

    // Set up some properties and metrics:
    var properties = {"game": currentGame.Name, "difficulty": currentGame.Difficulty};
    var metrics = {"Score": currentGame.Score, "Opponents": currentGame.OpponentCount};

    // Send the event:
    telemetry.trackEvent({name: "WinGame", properties: properties, measurements: metrics});


*Visual Basic*

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim metrics = New Dictionary (Of String, Double)
    metrics.Add("Score", currentGame.Score)
    metrics.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics)


*Java*

    Map<String, String> properties = new HashMap<String, String>();
    properties.put("game", currentGame.getName());
    properties.put("difficulty", currentGame.getDifficulty());

    Map<String, Double> metrics = new HashMap<String, Double>();
    metrics.put("Score", currentGame.getScore());
    metrics.put("Opponents", currentGame.getOpponentCount());

    telemetry.trackEvent("WinGame", properties, metrics);


> [!NOTE]
> Należy zadbać, aby nie rejestrować dane osobowe we właściwościach.
>
>

*Jeśli używasz metryki*, otwórz Eksploratora metryk i wybrać metryki z **niestandardowy** grupy:

![Otwórz Eksploratora metryk, wybierz wykres, a następnie wybierz metrykę](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

> [!NOTE]
> Jeśli nie zostanie wyświetlone Twoje metryki lub **niestandardowy** pozycji nie ma, zamknij blok zaznaczenie i spróbuj ponownie później. Metryki czasami może zająć godzinę należy łączyć za pośrednictwem potoku.

*Jeśli używasz właściwości i metryki*, podzielić Metryka przez właściwość:

![Ustaw grupowanie, a następnie wybierz właściwość w obszarze Grupuj według](./media/app-insights-api-custom-events-metrics/04-segment-metric-event.png)

*W wyszukiwaniu diagnostycznych*, można wyświetlić właściwości i metryki poszczególnych wystąpień tego zdarzenia.

![Wybierz wystąpienie, a następnie wybierz pozycję "..."](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-4.png)

Użyj **wyszukiwania** pola, aby wyświetlić wystąpienia zdarzenia, które mają określoną wartość właściwości.

![Wpisz termin do wyszukiwania](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-5.png)

[Dowiedz się więcej o wyrażeniach wyszukiwania](app-insights-diagnostic-search.md).

### <a name="alternative-way-to-set-properties-and-metrics"></a>Alternatywny sposób, aby ustawić właściwości i metryki
Jeśli jest to bardziej wygodne, można zebrać parametry zdarzenia w oddzielnych obiektów:

    var event = new EventTelemetry();

    event.Name = "WinGame";
    event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
    event.Properties["game"] = currentGame.Name;
    event.Properties["difficulty"] = currentGame.Difficulty;
    event.Metrics["Score"] = currentGame.Score;
    event.Metrics["Opponents"] = currentGame.Opponents.Length;

    telemetry.TrackEvent(event);

> [!WARNING]
> Nie należy używać ponownie to samo wystąpienie elementu telemetrii (`event` w tym przykładzie) do wywołania Track*() wiele razy. Może to spowodować telemetrię, aby można wysłać z nieprawidłowej konfiguracji.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Niestandardowe pomiarów i właściwości w module analiz

W [Analytics](app-insights-analytics.md), właściwości i metryki niestandardowe Pokaż w `customMeasurements` i `customDimensions` atrybuty każdego rekordu telemetrii.

Na przykład po dodaniu właściwość o nazwie "gry" telemetrii żądania to zapytanie liczby wystąpień różne wartości "gry" i Pokaż średnią niestandardowe metryki "wynik":

```
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game) 
```

Zwróć uwagę, że:

* Podczas wyodrębniania wartości z customDimensions lub customMeasurements JSON, ma typ dynamiczne i dlatego należy rzutować go `tostring` lub `todouble`.
* Biorąc pod uwagę możliwość [próbkowania](app-insights-sampling.md), należy użyć `sum(itemCount)`, a nie `count()`.



## <a name="timed"></a> Zdarzenia czasowe
Czasami chcesz wykresu, jak długo trwa do wykonania akcji. Na przykład możesz chcieć wiedzieć, jak długo użytkowników podjęcia, aby skonfigurować opcje w grę. Dla tego można użyć parametru miary.

*C#*

```C#
    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... perform the timed action ...

    stopwatch.Stop();

    var metrics = new Dictionary <string, double>
       {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

    // Set up some properties:
    var properties = new Dictionary <string, string>
       {{"signalSource", currentSignalSource.Name}};

    // Send the event:
    telemetry.TrackEvent("SignalProcessed", properties, metrics);
```

*Java*

```Java
    long startTime = System.currentTimeMillis();

    // perform timed action

    long endTime = System.currentTimeMillis();
    Map<String, Double> metrics = new HashMap<>();
    metrics.put("ProcessingTime", endTime-startTime);

    // Setup some propereties
    Map<String, String> properties = new HashMap<>();
    properties.put("signalSource", currentSignalSource.getName());

    //send the event
    telemetry.trackEvent("SignalProcessed", properties, metrics);

```


## <a name="defaults"></a>Właściwości domyślne telemetria niestandardowa
Jeśli chcesz ustawić wartości właściwości dla niektórych zdarzeń niestandardowych, które należy zapisać domyślne, można ustawić je w wystąpieniu TelemetryClient. Są one dołączone do każdego elementu telemetrii, który został wysłany przez klienta.

*C#*

    using Microsoft.ApplicationInsights.DataContracts;

    var gameTelemetry = new TelemetryClient();
    gameTelemetry.Context.Properties["Game"] = currentGame.Name;
    // Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame");

*Visual Basic*

    Dim gameTelemetry = New TelemetryClient()
    gameTelemetry.Context.Properties("Game") = currentGame.Name
    ' Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame")

*Java*

    import com.microsoft.applicationinsights.TelemetryClient;
    import com.microsoft.applicationinsights.TelemetryContext;
    ...


    TelemetryClient gameTelemetry = new TelemetryClient();
    TelemetryContext context = gameTelemetry.getContext();
    context.getProperties().put("Game", currentGame.Name);

    gameTelemetry.TrackEvent("WinGame");
    
*Node.js*

    var gameTelemetry = new applicationInsights.TelemetryClient();
    gameTelemetry.commonProperties["Game"] = currentGame.Name;

    gameTelemetry.TrackEvent({name: "WinGame"});



Wywołania poszczególnych danych telemetrycznych można zastąpić wartości domyślne w słownikach ich właściwości.

*Dla języka JavaScript sieci web klientów*, [Użyj inicjatory telemetrii JavaScript](#js-initializer).

*Aby dodać właściwości do wszystkie dane telemetryczne*, łącznie z danymi z modułów kolekcji standardowych [zaimplementować `ITelemetryInitializer` ](app-insights-api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Próbkowanie, filtrowania i przetwarzanie telemetrii
Można napisać kod, aby przetworzyć telemetrii przed wysłaniem z zestawu SDK. Przetwarzanie zawiera dane są przesyłane z modułów telemetrii standardowe, takie jak kolekcji żądania HTTP i zależności kolekcji.

[Dodaj właściwości](app-insights-api-filtering-sampling.md#add-properties) do telemetrii zaimplementowanie `ITelemetryInitializer`. Na przykład można dodać numery wersji lub wartości, które mają być obliczane od innych właściwości.

[Filtrowanie](app-insights-api-filtering-sampling.md#filtering) można zmodyfikować lub odrzucić telemetrii przed wysłaniem z zestawu SDK zaimplementowanie `ITelemetryProcesor`. Możesz kontrolować, co jest wysyłana lub odrzucone, ale należy uwzględnić wpływu na Twoje metryki. W zależności od tego, jak można odrzucić elementów może spowodować utratę możliwość przechodzić między elementy powiązane.

[Próbkowanie](app-insights-api-filtering-sampling.md) spakowanych rozwiązanie, aby zmniejszyć ilość danych wysyłanych z aplikacji do portalu. Robi to bez wpływu na wyświetlonej metryki. I kopiuje je bez wpływu na możliwość diagnozowania problemów przechodzenie między powiązane elementy, takie jak wyjątki, żądania i wyświetleń strony.

[Dowiedz się więcej](app-insights-api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Wyłączanie telemetrii
Aby *dynamicznie zatrzymywania i uruchamiania* zbierania i przekazywania danych telemetrii:

*C#*

```csharp

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```Java
    
    telemetry.getConfiguration().setTrackingDisabled(true);

```

Aby *wyłączyć wybranego standardowe moduły zbierające*— na przykład liczniki wydajności żądań HTTP i zależności — Usuń lub komentarz w odpowiednich wierszach [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Można to zrobisz, na przykład, jeśli chcesz wysyłać dane TrackRequest.

*Node.js*

```Javascript

    telemetry.config.disableAppInsights = true;
```

Aby *wyłączyć wybranego standardowe moduły zbierające*— na przykład liczniki wydajności żądań HTTP i zależności — w czasie inicjowania łańcucha metody konfiguracji w kodzie inicjowania zestawu SDK:

```Javascript

    applicationInsights.setup()
        .setAutoCollectRequests(false)
        .setAutoCollectPerformance(false)
        .setAutoCollectExceptions(false)
        .setAutoCollectDependencies(false)
        .setAutoCollectConsole(false)
        .start();
```

Aby wyłączyć te moduły zbierające po zainicjowaniu, użyj obiektu konfiguracji: `applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="debug"></a>Tryb dewelopera
Podczas debugowania, warto mieć telemetrii przyspieszone za pośrednictwem potoku, aby od razu Zobacz wyniki. Możesz również get przejrzeć dodatkowe komunikaty ułatwiające śledzenia problemów z telemetrii. Wyłącz go w środowisku produkcyjnym, ponieważ może to spowolnić aplikacji.

*C#*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*Visual Basic*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True


## <a name="ikey"></a> Ustawienie klucza instrumentacji dla wybranego telemetria niestandardowa
*C#*

    var telemetry = new TelemetryClient();
    telemetry.InstrumentationKey = "---my key---";
    // ...


## <a name="dynamic-ikey"></a> Klucz Instrumentacji dynamiczne
Aby uniknąć łączenie się dane telemetryczne z programowanie, testów i środowisk produkcyjnych, możesz [utworzyć oddzielne zasobów usługi Application Insights](app-insights-create-new-resource.md) i zmiany ich kluczy, w zależności od środowiska.

Zamiast pobierania klucza Instrumentacji z pliku konfiguracji, możesz ustawić w kodzie. Ustaw klucz w metodzie inicjowania, takich jak pliku global.aspx.cs w usługi ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      ...

*JavaScript*

    appInsights.config.instrumentationKey = myKey;



Na stronach sieci Web możesz ustawić go z serwera sieci web stanu zamiast kodowania go bezpośrednio do skryptu. Na przykład na stronie sieci Web wygenerowane w aplikacji ASP.NET:

*Język JavaScript w Razor*

    <script type="text/javascript">
    // Standard Application Insights webpage script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey;
    }) // ...


## <a name="telemetrycontext"></a>TelemetryContext
TelemetryClient ma właściwość kontekstu, która zawiera wartości, które są wysyłane oraz wszystkie dane telemetryczne. Zwykle są ustawione przez moduły telemetrii standardowych, ale można również ustawić je samodzielnie. Na przykład:

    telemetry.Context.Operation.Name = "MyOperationName";

Jeśli ustawisz dowolne z tych wartości samodzielnie, rozważ usunięcie odpowiedni wiersz z [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), dzięki czemu nie pobrać pomylić wartości i wartości domyślnych.

* **Składnik**: aplikacji i jej wersję.
* **Urządzenie**: dane dotyczące urządzenia, na którym aplikacja jest uruchomiona. (W aplikacjach sieci web jest serwera lub klienta urządzenia, które dane telemetryczne są wysyłane z).
* **InstrumentationKey**: zasobu usługi Application Insights na platformie Azure, w którym są wyświetlane dane telemetryczne. On zwykle jest pobierany z ApplicationInsights.config.
* **Lokalizacja**: lokalizację geograficzną urządzenia.
* **Operacja**: W aplikacji sieci web, bieżącego żądania HTTP. W innych typów aplikacji możesz ustawić Aby pogrupować zdarzenia ze sobą.
  * **Identyfikator**: wartość wygenerowaną odpowiadająca różnych zdarzeń, dzięki czemu inspekcji żadnych zdarzeń diagnostycznych wyszukiwania można znaleźć powiązanych elementów.
  * **Nazwa**: identyfikator, zwykle adres URL żądania HTTP.
  * **SyntheticSource**: w przypadku braku wartości null lub pusty ciąg, który wskazuje, że źródło żądania został zidentyfikowany jako test robota lub sieci web. Domyślnie jest ona wykluczana z obliczeń w Eksploratorze metryk.
* **Właściwości**: właściwości, które są wysyłane z wszystkich danych telemetrycznych. Może zostać zastąpiona w poszczególnych Śledź * wywołania.
* **Sesja**: sesja użytkownika. Identyfikator ma ustawioną generowanej wartości jest zmieniany, gdy użytkownik nie była aktywna przez pewien czas.
* **Użytkownik**: informacje o użytkowniku.

## <a name="limits"></a>Limity
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

Aby uniknąć naciśnięcie limit szybkości danych, należy użyć [próbkowania](app-insights-sampling.md).

Aby określić, jak długo są przechowywane dane, zobacz [przechowywanie danych i ochrona prywatności](app-insights-data-retention-privacy.md).

## <a name="reference-docs"></a>Dokumentacja
* [Platformy ASP.NET — dokumentacja](https://msdn.microsoft.com/library/dn817570.aspx)
* [Dokumentacja programu Java](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [JavaScript — odwołanie](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Zestaw SDK systemu Android](https://github.com/Microsoft/ApplicationInsights-Android)
* [Zestaw SDK systemu iOS](https://github.com/Microsoft/ApplicationInsights-iOS)

## <a name="sdk-code"></a>Kod zestawu SDK
* [Platformy ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Pakiety systemu Windows Server](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Zestaw SDK Java](https://github.com/Microsoft/ApplicationInsights-Java)
* [Zestaw SDK dla platformy Node.js](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [Zestaw SDK dla języka JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)
* [Wszystkie platformy](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## <a name="questions"></a>Pytania
* *Jakie wyjątki może zgłosić Track_() wywołania*

    Brak. Nie potrzebujesz opakowywać w klauzulach try-catch. Jeśli zestaw SDK napotyka problemy, będzie rejestrować wiadomości w danych wyjściowych konsoli debugowania i — jeśli komunikaty za pośrednictwem — diagnostycznych wyszukiwania.
* *Czy istnieje interfejsu API REST, aby pobrać dane z portalu?*

    Tak, [dostępu do danych interfejsu API](https://dev.applicationinsights.io/). Inne metody, aby wyodrębnić dane to [wyeksportować z usługi Analytics z usługą Power BI](app-insights-export-power-bi.md) i [Eksport ciągły](app-insights-export-telemetry.md).

## <a name="next"></a>Następne kroki
* [Wyszukiwanie zdarzeń i dzienniki](app-insights-diagnostic-search.md)

* [Rozwiązywanie problemów](app-insights-troubleshoot-faq.md)


