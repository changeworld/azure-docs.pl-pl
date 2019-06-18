---
title: Interfejs API usługi Application Insights dla niestandardowych zdarzeń i metryk | Dokumentacja firmy Microsoft
description: Wstaw kilka wierszy kodu w aplikacji pulpitu lub urządzenia, strony sieci Web lub usługi, śledzenie użycia i diagnozować problemy z.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 80400495-c67b-4468-a92e-abf49793a54d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: mbullwin
ms.openlocfilehash: 5194b9e6f40dbcd5e48b33c12db4b3cd94f75de3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66478402"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>Interfejs API usługi Application Insights dla niestandardowych zdarzeń i metryk

Wstaw kilka wierszy kodu w aplikacji, aby dowiedzieć się, jak użytkownicy korzystają z nim lub aby łatwiej diagnozować problemy. Za wysyłają dane telemetryczne z aplikacji urządzenia i pulpitu, klienci sieci web i serwerów sieci web. Użyj [usługi Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) core API telemetrii do wysyłania niestandardowych zdarzeń i metryk i własnych wersji standardowa telemetria. Ten interfejs API jest tego samego interfejsu API, używanego przez standardowe moduły zbierające dane usługi Application Insights.

> [!NOTE]
> `TrackMetric()` nie jest już aplikacje oparte na preferowaną metodę wysyłania metryki niestandardowe dla platformy .NET. W [wersji beta 2,60 3](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/CHANGELOG.md#version-260-beta3) zestawu SDK .NET usługi Insights aplikacji nowej metody [ `TelemetryClient.GetMetric()` ](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.getmetric?view=azure-dotnet) została wprowadzona. Począwszy od zestawu SDK .NET usługi Application Insights [wersji 2.72](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.getmetric?view=azure-dotnet) ta funkcja jest teraz częścią stabilnej wersji.

## <a name="api-summary"></a>Podsumowanie interfejsu API

Podstawowego interfejsu API jest jednolita na wszystkich platformach, oprócz kilka zmian, takich jak `GetMetric`(tylko platforma .NET).

| Metoda | Używana do |
| --- | --- |
| [`TrackPageView`](#page-views) |Strony, ekranów, bloków lub formularzy. |
| [`TrackEvent`](#trackevent) |Akcje użytkownika i inne zdarzenia. Używane do śledzenia zachowania użytkownika lub do monitorowania wydajności. |
| [`GetMetric`](#getmetric) |Zero i metryk wielowymiarowych agregacji centralnie skonfigurowany tylko język C#. |
| [`TrackMetric`](#trackmetric) |Miary wydajności, takich jak długości kolejki nie są związane z określonych zdarzeń. |
| [`TrackException`](#trackexception) |Wyjątki rejestrowania diagnostyki. Śledź, gdzie występują w odniesieniu do innych zdarzeń i badać ślady stosu. |
| [`TrackRequest`](#trackrequest) |Rejestrowanie, częstotliwość i czas trwania żądań serwera podczas analizy wydajności. |
| [`TrackTrace`](#tracktrace) |Komunikaty dziennika diagnostycznego. Istnieje również możliwość przechwytywania dzienników innych firm. |
| [`TrackDependency`](#trackdependency) |Rejestrowanie czasu trwania i częstotliwość wywołań do składników zewnętrznych, od których zależy aplikacja. |

Możesz [dołączanie właściwości i metryki](#properties) do większości te wywołania telemetrii.

## <a name="prep"></a>Przed rozpoczęciem

Jeśli nie masz jeszcze odwołanie na zestaw SDK usługi Application Insights:

* Dodaj zestaw Application Insights SDK do projektu:

  * [ASP.NET project](../../azure-monitor/app/asp-net.md)
  * [Projektu w języku Java](../../azure-monitor/app/java-get-started.md)
  * [Projekt node.js](../../azure-monitor/app/nodejs.md)
  * [Język JavaScript na każdej stronie sieci Web](../../azure-monitor/app/javascript.md) 
* W kodzie serwera sieci web lub urządzenia obejmują:

    *C#:* `using Microsoft.ApplicationInsights;`

    *Visual Basic:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

    *Node.js:* `var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Pobierz wystąpienie TelemetryClient

Pobierz wystąpienia `TelemetryClient` (z wyjątkiem JavaScript na stronach sieci Web):

*C#*

```csharp
private TelemetryClient telemetry = new TelemetryClient();
```

*Visual Basic*

```vb
Private Dim telemetry As New TelemetryClient
```

*Java*

```java
private TelemetryClient telemetry = new TelemetryClient();
``` 

*Node.js*

```javascript
var telemetry = applicationInsights.defaultClient;
```

TelemetryClient jest metodą o bezpiecznych wątkach.

W przypadku projektów ASP.NET i Java przychodzących żądań HTTP są przechwytywane automatycznie. Można utworzyć dodatkowe wystąpienia TelemetryClient inny moduł aplikacji. Na przykład może mieć jedno wystąpienie TelemetryClient w klasie oprogramowanie pośredniczące do zgłaszania zdarzeń logiki biznesowej. Można ustawić właściwości, takie jak nazwa użytkownika i DeviceId do identyfikowania maszyny. Informacja ta jest dołączany do wszystkich zdarzeń, które wysyła wystąpienia. 

*C#*

```csharp
TelemetryClient.Context.User.Id = "...";
TelemetryClient.Context.Device.Id = "...";
```

*Java*

```java
telemetry.getContext().getUser().setId("...");
telemetry.getContext().getDevice().setId("...");
```

W projektach środowiska Node.js, możesz użyć `new applicationInsights.TelemetryClient(instrumentationKey?)` do utworzenia nowego wystąpienia, ale jest zalecane tylko w przypadku scenariuszy, które wymagają konfiguracji izolowane od wzorca singleton `defaultClient`.

## <a name="trackevent"></a>Funkcja TrackEvent

W usłudze Application Insights *zdarzenie niestandardowe* jest punkt danych, który można wyświetlić w [Eksploratora metryk](../../azure-monitor/app/metrics-explorer.md) jako zagregowanej liczby, a następnie w [wyszukiwaniu diagnostycznym](../../azure-monitor/app/diagnostic-search.md) indywidualnych wystąpienia. (Nie jest on dotyczących MVC lub innych framework "miara events".)

Wstaw `TrackEvent` wywołuje kod w celu liczba różnych zdarzeń. Jak często użytkownicy wybrać poszczególnych funkcji, jak często osiągną określonego cele lub może być częstotliwość dokonają określonych rodzajów błędów.

Na przykład w aplikację z grą wysłać zdarzenie zawsze wtedy, gdy użytkownik wygrywa:

*JavaScript*

```javascript
appInsights.trackEvent("WinGame");
```

*C#*

```csharp
telemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
telemetry.TrackEvent("WinGame")
```

*Java*

```java
telemetry.trackEvent("WinGame");
```

*Node.js*

```javascript
telemetry.trackEvent({name: "WinGame"});
```

### <a name="custom-events-in-analytics"></a>Zdarzenia niestandardowe w usłudze Analytics

Dane telemetryczne są dostępne w `customEvents` tabelę [analizy usługi Application Insights](analytics.md). Każdy wiersz reprezentuje wywołanie `trackEvent(..)` w swojej aplikacji.

Jeśli [próbkowania](../../azure-monitor/app/sampling.md) trwa, właściwości: itemCount wskazuje wartość większą niż 1. Na przykład: itemCount == 10 oznacza, że liczby wywołań do 10 do poleceń trackEvent(), proces pobierania próbek jedynie przesyłane, jeden z nich. Aby uzyskać poprawny liczba zdarzeń niestandardowych, użytkownik powinien w związku z tym użyć kodu takiego jak `customEvents | summarize sum(itemCount)`.

## <a name="getmetric"></a>GetMetric

### <a name="examples"></a>Przykłady

*C#*

```csharp
#pragma warning disable CA1716  // Namespace naming

namespace User.Namespace.Example01
{
    using System;
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    /// <summary>
    /// Most simple cases are one-liners.
    /// This is all possible without even importing an additional namespace.
    /// </summary>

    public class Sample01
    {
        /// <summary />
        public static void Exec()
        {
            // *** SENDING METRICS ***

            // Recall how you send custom telemetry with Application Insights in other cases, e.g. Events.
            // The following will result in an EventTelemetry object to be sent to the cloud right away.
            TelemetryClient client = new TelemetryClient();
            client.TrackEvent("SomethingInterestingHappened");

            // Metrics work very similar. However, the value is not sent right away.
            // It is aggregated with other values for the same metric, and the resulting summary (aka "aggregate" is sent automatically every minute.
            // To mark this difference, we use a pattern that is similar, but different from the established TrackXxx(..) pattern that sends telemetry right away:

            client.GetMetric("CowsSold").TrackValue(42);

            // *** MULTI-DIMENSIONAL METRICS ***

            // The above example shows a zero-dimensional metric.
            // Metrics can also be multi-dimensional.
            // In the initial version we are supporting up to 2 dimensions, and we will add support for more in the future as needed.
            // Here is an example for a one-dimensional metric:

            Metric animalsSold = client.GetMetric("AnimalsSold", "Species");

            animalsSold.TrackValue(42, "Pigs");
            animalsSold.TrackValue(24, "Horses");

            // The values for Pigs and Horses will be aggregated separately from each other and will result in two distinct aggregates.
            // You can control the maximum number of number data series per metric (and thus your resource usage and cost).
            // The default limits are no more than 1000 total data series per metric, and no more than 100 different values per dimension.
            // We discuss elsewhere how to change them.
            // We use a common .NET pattern: TryXxx(..) to make sure that the limits are observed.
            // If the limits are already reached, Metric.TrackValue(..) will return False and the value will not be tracked. Otherwise it will return True.
            // This is particularly useful if the data for a metric originates from user input, e.g. a file:

            Tuple<int, string> countAndSpecies = ReadSpeciesFromUserInput();
            int count = countAndSpecies.Item1;
            string species = countAndSpecies.Item2;

            if (!animalsSold.TrackValue(count, species))

            {
                client.TrackTrace($"Data series or dimension cap was reached for metric {animalsSold.Identifier.MetricId}.", SeverityLevel.Error);
            }

            // You can inspect a metric object to reason about its current state. For example:
            int currentNumberOfSpecies = animalsSold.GetDimensionValues(1).Count;
        }

        private static void ResetDataStructure()
        {
            // Do stuff
        }

        private static Tuple<int, string> ReadSpeciesFromUserInput()
        {
            return Tuple.Create(18, "Cows");
        }

        private static int AddItemsToDataStructure()
        {
            // Do stuff
            return 5;
        }
    }
}
```

## <a name="trackmetric"></a>TrackMetric

> [!NOTE]
> Microsoft.ApplicationInsights.TelemetryClient.TrackMetric jest przestarzała w zestawie SDK platformy .NET. Metryki powinny zawsze być wstępnie zagregowane w określonym czasie przed wysłaniem. Użyj jednego z przeciążeń GetMetric(..) można uzyskać dostęp do możliwości wstępnego agregowania SDK metryki obiektu. W przypadku wdrażania własnej logiki wstępnego agregowania można użyć metody (ITelemetry metricTelemetry) śledzenie wysyłać wynikowej wartości zagregowanych. Jeśli aplikacja wymaga wysyłania elementu osobne dane telemetryczne w każdym razem, gdy bez agregacji w czasie, prawdopodobnie masz przypadek użycia dla danych telemetrycznych zdarzeń; Zobacz TelemetryClient.TrackEvent (Microsoft.ApplicationInsights.DataContracts.EventTelemetry).

Usługa Application Insights może wykresu metryki, które nie są dołączone do określonych zdarzeń. Na przykład można monitorować długość kolejki w regularnych odstępach czasu. Za pomocą metryk poszczególnymi pomiarami są mniej odsetek niż odmiany i trendów i wykresy więc statystyczne są.

Wysyłanie metryk do usługi Application Insights, można użyć `TrackMetric(..)` interfejsu API. Istnieją dwa sposoby, aby wysłać metryki:

* Pojedyncza wartość. Za każdym razem, gdy miara jest przeprowadzana w aplikacji, możesz wysłać odpowiadająca wartość do usługi Application Insights. Załóżmy na przykład, czy masz metryki opisujące liczbę elementów w kontenerze. W określonym okresie umieścić trzy elementy do kontenera, a następnie usuń dwa elementy. W związku z tym możesz wywołać `TrackMetric` dwa razy: najpierw przekazując wartość `3` i następnie wartość `-2`. Usługa Application Insights obie wartości są przechowywane w Twoim imieniu.

* Agregacja. Podczas pracy z metrykami, co jednej miary jest rzadko. Zamiast tego ważne jest podsumowanie co wydarzyło się w określonym czasie. Takie podsumowanie jest nazywany _agregacji_. W powyższym przykładzie łączna suma metryki dla tego okresu jest `1` liczba wartości metryk `2`. Korzystając z podejścia agregacji, można tylko wywołać `TrackMetric` raz na okres i wysyłać wartości zagregowanych. Jest to zalecane podejście, ponieważ jej mogą znacznie zmniejszyć koszty i wydajność obciążenie przez wysyłanie mniejszej liczby punktów danych do usługi Application Insights podczas nadal będzie zbierać wszystkie istotne informacje.

### <a name="examples"></a>Przykłady

#### <a name="single-values"></a>Pojedyncze wartości

Aby wysłać pojedynczą wartość metryki:

*JavaScript*

 ```javascript
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

```java
telemetry.trackMetric("queueLength", 42.0);
```

*Node.js*

 ```javascript
telemetry.trackMetric({name: "queueLength", value: 42.0});
 ```

### <a name="custom-metrics-in-analytics"></a>Metryki niestandardowe w usłudze Analytics

Dane telemetryczne są dostępne w `customMetrics` tabelę [analizy usługi Application Insights](analytics.md). Każdy wiersz reprezentuje wywołanie `trackMetric(..)` w swojej aplikacji.

* `valueSum` — Jest to suma pomiarów. Aby uzyskać wartość średnią, dzielenie przez `valueCount`.
* `valueCount` -Liczba pomiary, które zostały zagregowane do tego `trackMetric(..)` wywołania.

## <a name="page-views"></a>Wyświetlenia strony

W urządzeniu lub strony sieci Web aplikacji danych telemetrycznych widoku strony są domyślnie wysyłane po załadowaniu każdego ekranu lub strony. Ale można to zmienić, aby śledzić wyświetleń stron w dodatkowym lub innym czasie. Na przykład w aplikacji, która wyświetla karty lub bloków, warto śledzić strony zawsze wtedy, gdy użytkownik otwiera nowy blok.

Dane użytkowników i sesji są wysyłane jako właściwości oraz wyświetleń stron, więc wykresy użytkowników i sesji są aktywne po danych telemetrycznych widoku strony.

### <a name="custom-page-views"></a>Wyświetlenia stron niestandardowych

*JavaScript*

```javascript
appInsights.trackPageView("tab1");
```

*C#*

```csharp
telemetry.TrackPageView("GameReviewPage");
```

*Visual Basic*

```vb
telemetry.TrackPageView("GameReviewPage")
```

*Java*

```java
telemetry.trackPageView("GameReviewPage");
```

Jeśli masz kilka kart na różnych stronach HTML, możesz określić adres URL za:

```javascript
appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");
```

### <a name="timing-page-views"></a>Wyświetlenia strony chronometrażu

Domyślnie czas, w którym są zgłaszane jako **czas ładowania wyświetlenia strony** są mierzone w przeglądarce wysyła żądanie, dopóki nosi nazwę zdarzeń ładowania strony w przeglądarce.

Zamiast tego można:

* Ustaw jawne czasu trwania w [wywołania trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview) wywołania: `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`.
* Użyj widoku strony, czasy wywołań `startTrackPage` i `stopTrackPage`.

*JavaScript*

```javascript
// To start timing a page:
appInsights.startTrackPage("Page1");

...

// To stop timing and log the page:
appInsights.stopTrackPage("Page1", url, properties, measurements);
```

Nazwa która jest używany jako pierwszy parametr kojarzy wywołania rozpoczęcie i zakończenie. Domyślnie nazwa bieżącej strony.

Wynikowy strony obciążenia czasy trwania są wyświetlane w Eksploratorze metryk są uzyskiwane z interwał między wywołaniami rozpoczęcie i zakończenie. To Ty możesz jakiego interwału czasu rzeczywistego.

### <a name="page-telemetry-in-analytics"></a>Strona telemetrii w usłudze Analytics

W [Analytics](analytics.md) dwie tabele wyświetlić dane z operacji przeglądarki:

* `pageViews` Tabela zawiera dane o adres URL i tytuł strony
* `browserTimings` Tabela zawiera dane o wydajności klienta, takie jak czas poświęcony na przetworzenie danych przychodzących

Aby dowiedzieć się, jak długo trwa przeglądarki do przetworzenia różne strony:

```kusto
browserTimings
| summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name
```

Aby odnaleźć popularities różnych przeglądarek:

```kusto
pageViews
| summarize count() by client_Browser
```

Aby skojarzyć wyświetleń stron do wywołań AJAX, Dołącz zależności:

```kusto
pageViews
| join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest

Zestaw SDK używa TrackRequest do rejestrowania żądań HTTP.

Można również wywołać ją samodzielnie, aby symulować żądania w kontekście gdzie nie ma uruchomionego modułu usługi sieci web.

Jednak zalecanym sposobem wysyłają dane telemetryczne żądania jest, gdzie żądanie działa jako <a href="#operation-context">kontekst operacji</a>.

## <a name="operation-context"></a>Kontekst operacji

Elementy danych telemetrycznych można skorelować ze sobą przez skojarzenie ich z kontekstu operacji. Standardowy moduł śledzenia żądań robi to wyjątki i inne zdarzenia, które są wysyłane podczas przetwarzania żądania HTTP. W [wyszukiwania](../../azure-monitor/app/diagnostic-search.md) i [Analytics](analytics.md), możesz łatwo odnaleźć żadnych zdarzeń skojarzony z tym żądaniem, za pomocą jego identyfikatora operacji.

Zobacz [korelacja Telemetrii w usłudze Application Insights](../../azure-monitor/app/correlation.md) więcej informacji na temat korelacji.

Podczas śledzenia telemetrii ręcznie, najprostszym sposobem zapewnienia korelacja telemetrii za pomocą tego wzorca:

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

Wraz z ustawienie kontekst operacji `StartOperation` spowoduje utworzenie elementu telemetrii typu, który określisz. Podczas usuwania operacji, lub jeśli jawnie wywołać elementu telemetrii wysyła `StopOperation`. Jeśli używasz `RequestTelemetry` jako typ telemetrii, jego długość jest równa określony interwał czasu między uruchamianie i zatrzymywanie.

Elementy dane telemetryczne zgłoszone w zakresie operacji stają się dzieci, takich operacji. Konteksty operacji może być zagnieżdżone.

W polu wyszukiwania, kontekst operacji służy do tworzenia **powiązane elementy** listy:

![Elementy pokrewne](./media/api-custom-events-metrics/21.png)

Zobacz [śledzenie operacji niestandardowych za pomocą zestawu SDK .NET usługi Application Insights](../../azure-monitor/app/custom-operations-tracking.md) więcej informacji na temat operacje niestandardowe śledzenia.

### <a name="requests-in-analytics"></a>Żądania w usłudze Analytics

W [analizy usługi Application Insights](analytics.md), żądań show w górę w `requests` tabeli.

Jeśli [próbkowania](../../azure-monitor/app/sampling.md) jest w operacji właściwości: itemCount wyświetli wartość większą niż 1. Na przykład: itemCount == 10 oznacza, że 10 wywołań trackRequest(), proces pobierania próbek jedynie przesyłane, jeden z nich. Aby uzyskać prawidłowe liczby żądań i średniego czasu trwania posegmentowana według nazwy żądania, należy użyć kodu takiego jak:

```kusto
requests
| summarize count = sum(itemCount), avgduration = avg(duration) by name
```

## <a name="trackexception"></a>TrackException

Wyślij wyjątków do usługi Application Insights:

* Aby [policzyć](../../azure-monitor/app/metrics-explorer.md), w celu wskazania, częstotliwości problem.
* Aby [Sprawdź indywidualne wystąpienia](../../azure-monitor/app/diagnostic-search.md).

Raporty zawierają śladów stosu.

*C#*

```csharp
try
{
    ...
}
catch (Exception ex)
{
    telemetry.TrackException(ex);
}
```

*Java*

```java
try {
    ...
} catch (Exception ex) {
    telemetry.trackException(ex);
}
```

*JavaScript*

```javascript
try
{
    ...
}
catch (ex)
{
    appInsights.trackException(ex);
}
```

*Node.js*

```javascript
try
{
    ...
}
catch (ex)
{
    telemetry.trackException({exception: ex});
}
```

Zestawy SDK automatycznie, efektywnej wiele wyjątków, dzięki czemu zawsze nie trzeba jawnie wywołana metoda TrackException.

* ASP.NET: [Napisać kod, aby przechwytywać wyjątki](../../azure-monitor/app/asp-net-exceptions.md).
* Java EE: [Wyjątki są przechwytywane automatycznie](../../azure-monitor/app/java-get-started.md#exceptions-and-request-failures).
* JavaScript: Wyjątki są przechwytywane automatycznie. Jeśli chcesz wyłączyć automatyczne zbieranie, należy dodać wiersz do fragmentu kodu, które zostanie wstawiona strony sieci Web:

```javascript
({
    instrumentationKey: "your key",
    disableExceptionTracking: true
})
```

### <a name="exceptions-in-analytics"></a>Wyjątki w usłudze Analytics

W [analizy usługi Application Insights](analytics.md), wyjątki pojawiają się w `exceptions` tabeli.

Jeśli [próbkowania](../../azure-monitor/app/sampling.md) operacji, `itemCount` właściwość wskazuje wartość większą niż 1. Na przykład: itemCount == 10 oznacza, że 10 wywołań metody trackexception() procesu pobierania próbek jedynie przesyłane, jeden z nich. Aby uzyskać poprawny liczba wyjątków posegmentowana według typu wyjątku, należy użyć kodu takiego jak:

```kusto
exceptions
| summarize sum(itemCount) by type
```

Większość informacji ważnych stosu już jest następnie wyodrębniany do oddzielnych zmiennych, ale możesz ściągnąć od siebie `details` strukturę, aby zyskać jeszcze więcej korzyści. Ponieważ ta struktura jest dynamiczny, należy Rzutuj wynik metody na typ, których oczekujesz. Na przykład:

```kusto
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Aby skojarzyć wyjątków z ich powiązanych żądań, należy użyć sprzężenia:

```kusto
exceptions
| join (requests) on operation_Id
```

## <a name="tracktrace"></a>TrackTrace

Użyj TrackTrace, aby łatwiej diagnozować problemy, wysyłając "nawigacyjną" do usługi Application Insights. Umożliwia wysyłanie dużych ilości danych diagnostycznych i Analizuj aplikacje w [wyszukiwaniu diagnostycznym](../../azure-monitor/app/diagnostic-search.md).

Na platformie .NET [logowania karty](../../azure-monitor/app/asp-net-trace-logs.md) używać tego interfejsu API do wysyłania dzienników innej firmy do portalu.

W języku Java dla [rejestratorów standardowych, takich jak Log4J, Logback](../../azure-monitor/app/java-trace-logs.md) wysyłać za pomocą Application Insights Log4j lub Logback Appenders firm dzienników do portalu.

*C#*

```csharp
telemetry.TrackTrace(message, SeverityLevel.Warning, properties);
```

*Java*

```java
telemetry.trackTrace(message, SeverityLevel.Warning, properties);
```

*Node.js*

```javascript
telemetry.trackTrace({
    message: message,
    severity: applicationInsights.Contracts.SeverityLevel.Warning,
    properties: properties
});
```

*/ Przeglądarki klienta kodu JavaScript*

```javascript
trackTrace(message: string, properties?: {[string]:string}, severityLevel?: AI.SeverityLevel)
```

Zaloguj się zdarzenie diagnostyczne, takie jak przystępowania lub opuszczenie metody.

 Parametr | Opis
---|---
`message` | Dane diagnostyczne. Może być znacznie dłuższy niż nazwę.
`properties` | Mapa ciągu do ciągu: Dodatkowe dane używane do [filtru wyjątków](https://azure.microsoft.com/documentation/articles/app-insights-api-custom-events-metrics/#properties) w portalu. Wartość domyślna to puste.
`severityLevel` | Obsługiwane wartości: [SeverityLevel.ts](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/JavaScript/JavaScriptSDK.Interfaces/Contracts/Generated/SeverityLevel.ts)

Możesz wyszukiwać w treści wiadomości, ale (w przeciwieństwie do wartości właściwości) nie można filtrować na nim.

Limit rozmiaru `message` jest znacząco większa niż limit właściwości.
Zaletą TrackTrace jest umieszczenie stosunkowo długo dane w komunikacie. Na przykład możesz zakodować dane POST.  

Ponadto można dodać jej poziom ważności do wiadomości. I podobnie jak inne dane telemetryczne, można dodać wartości właściwości, które ułatwiają filtrowania lub wyszukiwania dla różnych zestawów danych śledzenia. Na przykład:

*C#*

```csharp
var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
telemetry.TrackTrace("Slow database response",
                SeverityLevel.Warning,
                new Dictionary<string,string> { {"database", db.ID} });
```

*Java*

```java
Map<String, Integer> properties = new HashMap<>();
properties.put("Database", db.ID);
telemetry.trackTrace("Slow Database response", SeverityLevel.Warning, properties);
```

W [wyszukiwania](../../azure-monitor/app/diagnostic-search.md), można następnie łatwo odfiltrować wszystkie komunikaty o określonej ważności, które odnoszą się do określonej bazy danych.

### <a name="traces-in-analytics"></a>Dane śledzenia w usłudze Analytics

W [analizy usługi Application Insights](analytics.md), wywołania TrackTrace pojawiają się w `traces` tabeli.

Jeśli [próbkowania](../../azure-monitor/app/sampling.md) trwa, właściwości: itemCount wskazuje wartość większą niż 1. Na przykład: itemCount == 10 oznacza, że 10 wywołań `trackTrace()`, proces pobierania próbek jedynie przesyłane, jeden z nich. Aby uzyskać poprawny liczba wywołań śledzenia, należy użyć w związku z tym kod taki jak `traces | summarize sum(itemCount)`.

## <a name="trackdependency"></a>TrackDependency

Umożliwia wywołanie TrackDependency śledzenia czasów odpowiedzi i odsetka pomyślnych wywołań zewnętrznego fragmentu kodu. Wyniki są wyświetlane na wykresach zależności w portalu.

*C#*

```csharp
var success = false;
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
try
{
    success = dependency.Call();
}
catch(Exception ex) 
{
    success = false;
    telemetry.TrackException(ex);
    throw new Exception("Operation went wrong", ex);
}
finally
{
    timer.Stop();
    telemetry.TrackDependency("DependencyType", "myDependency", "myCall", startTime, timer.Elapsed, success);
}
```

*Java*

```java
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

```javascript
var success = false;
var startTime = new Date().getTime();
try
{
    success = dependency.Call();
}
finally
{
    var elapsed = new Date() - startTime;
    telemetry.trackDependency({
        dependencyTypeName: "myDependency",
        name: "myCall",
        duration: elapsed,
        success: success
    });
}
```

Należy pamiętać, że te zestawy SDK serwera zawierają [modułu zależności](../../azure-monitor/app/asp-net-dependencies.md) , wykrywa i śledzi niektórych wywołania zależności automatycznie — na przykład z bazami danych i interfejsów API REST. Musisz zainstalować agenta na serwerze, aby utworzyć moduł pracy. 

W języku Java, niektóre wywołania zależności mogą być automatycznie śledzone za pomocą [agenta Java](../../azure-monitor/app/java-agent.md).

To wywołanie jest służy do śledzenia wywołań, które przechwytują nie automatycznego śledzenia, lub jeśli nie chcesz zainstalować agenta.

Aby wyłączyć standardowego modułu śledzenia zależności w języku C#, należy edytować [plik ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) i usunięcie odwołania do `DependencyCollector.DependencyTrackingTelemetryModule`. W języku Java, nie należy instalować agenta java, jeśli nie chcesz automatycznie zbierać standardowy zależności.

### <a name="dependencies-in-analytics"></a>Zależności w usłudze Analytics

W [analizy usługi Application Insights](analytics.md), trackDependency wywołuje Pokaż w `dependencies` tabeli.

Jeśli [próbkowania](../../azure-monitor/app/sampling.md) trwa, właściwości: itemCount wskazuje wartość większą niż 1. Na przykład: itemCount == 10 oznacza, że 10 wywołań trackDependency(), proces pobierania próbek jedynie przesyłane, jeden z nich. Aby uzyskać poprawny liczba zależności segmentowanych przez składnik docelowy, należy użyć kodu takiego jak:

```kusto
dependencies
| summarize sum(itemCount) by target
```

Aby skojarzyć zależności z ich powiązanych żądań, należy użyć sprzężenia:

```kusto
dependencies
| join (requests) on operation_Id
```

## <a name="flushing-data"></a>Opróżnienie danych

Normalnie zestaw SDK wysyła dane w ustalonych odstępach czasu (zwykle 30 sekund) lub zawsze wtedy, gdy rozmiar buforu jest pełny (zazwyczaj 500 elementów). Jednak w niektórych przypadkach możesz chcieć opróżniania buforu — na przykład, jeśli używasz zestawu SDK w aplikacji, która kończy pracę.

*C#*

 ```csharp
telemetry.Flush();
// Allow some time for flushing before shutdown.
System.Threading.Thread.Sleep(5000);
```

*Java*

```java
telemetry.flush();
//Allow some time for flushing before shutting down
Thread.sleep(5000);
```

*Node.js*

```javascript
telemetry.flush();
```

Funkcja jest asynchroniczne dla [kanału dane telemetryczne serwera](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/).

W idealnym przypadku flush() metoda powinna służyć w działaniu zamknięcia aplikacji.

## <a name="authenticated-users"></a>Uwierzytelnieni użytkownicy

W aplikacji sieci web użytkownicy są (domyślnie) identyfikowane przez pliki cookie. Użytkownik może być liczone więcej niż jeden raz dostępu do aplikacji z innej maszyny lub przeglądarki lub ich usuwanie plików cookie.

Jeśli użytkownicy logują się do aplikacji, można uzyskać dokładniejsze liczba, przez ustawienie Identyfikatora uwierzytelnionego użytkownika w kodzie przeglądarki:

*JavaScript*

```javascript
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

We wzorcu ASP.NET web aplikacji MVC, na przykład:

*Razor*

```cshtml
@if (Request.IsAuthenticated)
{
    <script>
        appInsights.setAuthenticatedUserContext("@User.Identity.Name
            .Replace("\\", "\\\\")"
            .replace(/[,;=| ]+/g, "_"));
    </script>
}
```

Nie jest konieczne użycie rzeczywistych logowania nazwy użytkownika. Tylko ma być Identyfikatora, który jest unikatowy dla tego użytkownika. Nie może zawierać spacji ani znaków `,;=|`.

Identyfikator użytkownika jest również ustawiona w pliku cookie sesji i wysyłane do serwera. Jeśli zainstalowano zestaw SDK serwera, Identyfikatora uwierzytelnionego użytkownika są wysyłane w ramach właściwości kontekstu klienta i serwera danych telemetrycznych. Następnie możesz filtrować dane i wyszukaj w nim.

Jeśli aplikacja grup użytkowników do kont, można również przekazać identyfikator konta (za pomocą tego samego ograniczenia dotyczące znaków).

```javascript
appInsights.setAuthenticatedUserContext(validatedId, accountId);
```

W [Eksploratora metryk](../../azure-monitor/app/metrics-explorer.md), można utworzyć wykres, który zlicza **uwierzytelniania użytkowników,** , i **kont użytkowników**.

Możesz również [wyszukiwania](../../azure-monitor/app/diagnostic-search.md) punktów danych klienta przy użyciu nazwy użytkownika i kont.

## <a name="properties"></a>Filtrowanie, wyszukiwanie i dzieląc dane za pomocą właściwości

Można dołączyć właściwości i miary do zdarzenia (i również metryki, stronie widoków, wyjątki i innych danych telemetrii).

*Właściwości* są wartości ciągu, których można użyć do filtrowania danych telemetrycznych w raportach użycia. Na przykład jeśli aplikacja udostępnia kilka gier, można dołączyć nazwa gry do każdego zdarzenia, dzięki czemu można zobaczyć, które gry są bardziej popularne.

Ma limitu 8192 długości ciągu. (Jeśli chcesz wysłać duże zestawy danych, użyj parametru komunikat TrackTrace.)

*Metryki* wartości numeryczne, które mogą być prezentowane w postaci graficznej. Na przykład możesz chcieć sprawdzić, czy jest stopniowy wzrost wyniki, pozwalających na osiągnięcie do graczy. Wykresy można posegmentować za pomocą właściwości, które są wysyłane ze zdarzeniem, dzięki czemu można uzyskać osobne lub skumulowane wykresy dla różnych gier.

Dla wartości metryk być poprawnie wyświetlane powinna być większa lub równa 0.

Istnieją pewne [limity liczby właściwości, wartości właściwości i metryki](#limits) , można użyć.

*JavaScript*

```javascript
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
```

*C#*

```csharp
// Set up some properties and metrics:
var properties = new Dictionary <string, string>
    {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
var metrics = new Dictionary <string, double>
    {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

// Send the event:
telemetry.TrackEvent("WinGame", properties, metrics);
```

*Node.js*

```javascript
// Set up some properties and metrics:
var properties = {"game": currentGame.Name, "difficulty": currentGame.Difficulty};
var metrics = {"Score": currentGame.Score, "Opponents": currentGame.OpponentCount};

// Send the event:
telemetry.trackEvent({name: "WinGame", properties: properties, measurements: metrics});
```

*Visual Basic*

```vb
' Set up some properties:
Dim properties = New Dictionary (Of String, String)
properties.Add("game", currentGame.Name)
properties.Add("difficulty", currentGame.Difficulty)

Dim metrics = New Dictionary (Of String, Double)
metrics.Add("Score", currentGame.Score)
metrics.Add("Opponents", currentGame.OpponentCount)

' Send the event:
telemetry.TrackEvent("WinGame", properties, metrics)
```

*Java*

```java
Map<String, String> properties = new HashMap<String, String>();
properties.put("game", currentGame.getName());
properties.put("difficulty", currentGame.getDifficulty());

Map<String, Double> metrics = new HashMap<String, Double>();
metrics.put("Score", currentGame.getScore());
metrics.put("Opponents", currentGame.getOpponentCount());

telemetry.trackEvent("WinGame", properties, metrics);
```

> [!NOTE]
> Należy zadbać, aby nie rejestrować danych osobowych w oknie właściwości.
>
>

### <a name="alternative-way-to-set-properties-and-metrics"></a>Alternatywny sposób, aby ustawić właściwości i metryki

Jeśli jest to bardziej wygodne, można zebrać parametrów zdarzenia w oddzielnym obiektem:

```csharp
var event = new EventTelemetry();

event.Name = "WinGame";
event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
event.Properties["game"] = currentGame.Name;
event.Properties["difficulty"] = currentGame.Difficulty;
event.Metrics["Score"] = currentGame.Score;
event.Metrics["Opponents"] = currentGame.Opponents.Length;

telemetry.TrackEvent(event);
```

> [!WARNING]
> Nie używaj ponownie plików to samo wystąpienie elementu danych telemetrycznych (`event` w tym przykładzie) do wywołania Track*() wiele razy. Może to spowodować telemetrii do wysłania z nieprawidłowej konfiguracji.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Pomiary niestandardowe i właściwości w usłudze Analytics

W [Analytics](analytics.md), właściwości i metryki niestandardowe, Pokaż w `customMeasurements` i `customDimensions` atrybuty każdego wybranego rekordu danych telemetrycznych.

Na przykład po dodaniu właściwość o nazwie "grę", do telemetrii żądania to zapytanie liczby wystąpień różnych wartości "game" i pokazują średnią niestandardowe metryki "wynik":

```kusto
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game)
```

Należy zauważyć, że:

* Po wyodrębnieniu wartość z tabeli customDimensions lub customMeasurements JSON ma typu dynamicznego, i dlatego należy rzutować go `tostring` lub `todouble`.
* Biorąc pod uwagę możliwość [próbkowania](../../azure-monitor/app/sampling.md), należy użyć `sum(itemCount)`, a nie `count()`.

## <a name="timed"></a> Zdarzenia chronometrażu

Czasami trzeba wykresu, jak długo trwa wykonania akcji. Na przykład możesz chcieć wiedzieć, jak długo użytkowników take, aby skonfigurować opcje w grze. W tym, można użyć parametru miary.

*C#*

```csharp
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

```java
long startTime = System.currentTimeMillis();

// Perform timed action

long endTime = System.currentTimeMillis();
Map<String, Double> metrics = new HashMap<>();
metrics.put("ProcessingTime", endTime-startTime);

// Setup some properties
Map<String, String> properties = new HashMap<>();
properties.put("signalSource", currentSignalSource.getName());

// Send the event
telemetry.trackEvent("SignalProcessed", properties, metrics);
```

## <a name="defaults"></a>Właściwości domyślne dla niestandardowych danych telemetrycznych

Jeśli chcesz ustawić domyślne wartości właściwości dla niektórych zdarzenia niestandardowe, które należy zapisać, można ustawić je w wystąpieniu TelemetryClient. Są one dołączone do każdego elementu telemetrii wysyłanych z tego klienta.

*C#*

```csharp
using Microsoft.ApplicationInsights.DataContracts;

var gameTelemetry = new TelemetryClient();
gameTelemetry.Context.Properties["Game"] = currentGame.Name;
// Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
Dim gameTelemetry = New TelemetryClient()
gameTelemetry.Context.Properties("Game") = currentGame.Name
' Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame")
```

*Java*

```java
import com.microsoft.applicationinsights.TelemetryClient;
import com.microsoft.applicationinsights.TelemetryContext;
...


TelemetryClient gameTelemetry = new TelemetryClient();
TelemetryContext context = gameTelemetry.getContext();
context.getProperties().put("Game", currentGame.Name);

gameTelemetry.TrackEvent("WinGame");
```

*Node.js*

```javascript
var gameTelemetry = new applicationInsights.TelemetryClient();
gameTelemetry.commonProperties["Game"] = currentGame.Name;

gameTelemetry.TrackEvent({name: "WinGame"});
```

Wywołania telemetrii można zastąpić wartości domyślne w słownikach ich właściwości.

*Dla języka JavaScript sieci web klientów*, użyj inicjatorów telemetrii języka JavaScript.

*Aby dodać właściwości do wszystkie dane telemetryczne*, włącznie z danymi z modułów kolekcji standardowych [zaimplementować `ITelemetryInitializer` ](../../azure-monitor/app/api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Próbkowanie, filtrowanie i przetwarzanie danych telemetrycznych

Można napisać kod, aby przetwarzać dane telemetryczne, przed ich wysłaniem z zestawu SDK. Przetwarzanie obejmuje dane, które są wysyłane z modułów standardowych danych telemetrycznych, takich jak zbieranie żądania HTTP i zależności kolekcji.

[Dodaj właściwości](../../azure-monitor/app/api-filtering-sampling.md#add-properties) do telemetrii, implementując `ITelemetryInitializer`. Na przykład można dodać numery wersji lub wartości, które są obliczane przy użyciu innych właściwości.

[Filtrowanie](../../azure-monitor/app/api-filtering-sampling.md#filtering) można zmodyfikować lub odrzucić telemetrii przed wysłaniem go z zestawu SDK poprzez implementację `ITelemetryProcessor`. Możesz kontrolować, co jest wysyłane lub odrzucone, ale trzeba uwzględnić wpływ na metryk. W zależności od tego, jak zostaną odrzucone elementy możesz utracić możliwość nawigować między powiązanymi elementami.

[Próbkowanie](../../azure-monitor/app/api-filtering-sampling.md) jest spakowane rozwiązanie, aby ograniczyć ilość danych wysyłanych z aplikacji do portalu. Robi to bez wywierania wpływu na wyświetlanych metryk. I robi to bez wpływu na możliwość diagnozowania problemów, przechodząc między elementy pokrewne, takie jak wyjątki, żądania i wyświetleń stron.

[Dowiedz się więcej](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Wyłączanie telemetrii

Aby *dynamicznie zatrzymywania i uruchamiania* zbierania i przekazywania danych telemetrycznych:

*C#*

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```java
telemetry.getConfiguration().setTrackingDisabled(true);
```

Aby *wyłączyć wybrany standardowe moduły zbierające*— na przykład liczniki wydajności żądań HTTP lub lub zależności — Usuń komentarz odpowiednie wiersze w [plik ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Można to zrobisz, na przykład, jeśli chcesz wysłać dane TrackRequest.

*Node.js*

```javascript
telemetry.config.disableAppInsights = true;
```

Aby *wyłączyć wybrany standardowe moduły zbierające*— na przykład liczniki wydajności, żądania HTTP lub zależności — w czasie inicjowania łańcucha metody konfiguracji w kodzie inicjowania zestawu SDK:

```javascript
applicationInsights.setup()
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    .setAutoCollectDependencies(false)
    .setAutoCollectConsole(false)
    .start();
```

Aby wyłączyć te moduły zbierające po zainicjowaniu, należy użyć obiektu konfiguracji: `applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="debug"></a>Tryb dewelopera

Podczas debugowania, warto mieć telemetrii przyspieszana w potoku, aby od razu Zobacz wyniki. Możesz również te dodatkowe komunikaty get, które ułatwiają śledzenie problemów z telemetrią. Wyłącz go w środowisku produkcyjnym, ponieważ może to spowolnić aplikację.

*C#*

```csharp
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;
```

*Visual Basic*

```vb
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True
```

*Node.js*

Dla środowiska Node.js, można włączyć tryb dewelopera, włączając rejestrowanie wewnętrznych za pośrednictwem `setInternalLogging` i ustawienie `maxBatchSize` 0, co powoduje, że telemetrii do wysłania zaraz po ich zebraniu.

```js
applicationInsights.setup("ikey")
  .setInternalLogging(true, true)
  .start()
applicationInsights.defaultClient.config.maxBatchSize = 0;
```

## <a name="ikey"></a> Ustawienia klucza instrumentacji dla wybranych niestandardowych danych telemetrycznych

*C#*

```csharp
var telemetry = new TelemetryClient();
telemetry.InstrumentationKey = "---my key---";
// ...
```

## <a name="dynamic-ikey"></a> Klucz Instrumentacji dynamiczne

Unikaj łączenia operacji się dane telemetryczne z programowania, testowania i środowisk produkcyjnych, możesz [należy utworzyć osobne zasoby usługi Application Insights](../../azure-monitor/app/create-new-resource.md ) i zmień ich kluczy, w zależności od środowiska.

Zamiast pobierania klucza Instrumentacji z pliku konfiguracji, jest on ustawiany w kodzie. Ustaw klucz w metodzie inicjalizacji, takich jak pliku global.aspx.cs usługi sieci Web platformy ASP.NET:

*C#*

```csharp
protected void Application_Start()
{
    Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey =
        // - for example -
        WebConfigurationManager.Settings["ikey"];
    ...
}
```

*JavaScript*

```javascript
appInsights.config.instrumentationKey = myKey;
```

Na stronach sieci Web można ustawić go z serwera sieci web stanu zamiast kodowania w dosłownie w skrypcie. Na przykład na stronie sieci Web wygenerowane w aplikacji platformy ASP.NET:

*Język JavaScript w aparacie Razor*

```cshtml
<script type="text/javascript">
// Standard Application Insights webpage script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
    // Generate from server property:
    @Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey;
}) // ...
```

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="telemetrycontext"></a>TelemetryContext

TelemetryClient ma właściwość kontekstu, który zawiera wartości, które są wysyłane wraz ze wszystkimi danymi telemetrii. Zwykle są one ustalane przez moduły standardowe dane telemetryczne, ale można również ustawić je samodzielnie. Na przykład:

```csharp
telemetry.Context.Operation.Name = "MyOperationName";
```

Jeśli ustawisz dowolne z tych wartości samodzielnie, rozważ usunięcie odpowiedni wiersz z [plik ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), dzięki czemu nie pobieranie mylić wartości i wartości domyślnych.

* **Składnik**: Aplikacja i jej wersji.
* **Urządzenie**: Dane o urządzeniu, w którym aplikacja jest uruchomiona. (W aplikacji sieci web, to jest serwer lub urządzenie klienckie, które dane telemetryczne są wysyłane z).
* **InstrumentationKey**: Zasób usługi Application Insights na platformie Azure, w którym pojawia się dane telemetryczne. Zostanie ona zazwyczaj podjęta w pliku ApplicationInsights.config.
* **Lokalizacja**: Lokalizacja geograficzna urządzenia.
* **Operacja**: W aplikacji sieci web, bieżącego żądania HTTP. W innych typów aplikacji możesz ustawić Aby pogrupować zdarzenia ze sobą.
  * **ID**: Wygenerowaną wartość odpowiadająca różnych zdarzeń inspekcji dowolnego zdarzenia w wyszukiwaniu diagnostycznym powiązane elementy mogły zostać odnalezione.
  * **Nazwa**: Identyfikator, zwykle adres URL żądania HTTP.
  * **SyntheticSource**: W przeciwnym razie wartość null lub pusty ciąg, który wskazuje, że źródła żądania została zidentyfikowana jako test sieci web lub robota. Domyślnie jest ona wykluczana z obliczeń w Eksploratorze metryk.
* **Właściwości**: Właściwości, które są wysyłane z wszystkie dane telemetryczne. Może zostać zastąpiona w poszczególne wywołania śledzenia *.
* **Sesja**: Sesja użytkownika. Identyfikator jest równa wygenerowaną wartość jest zmieniany, gdy użytkownik nie był aktywny od pewnego czasu.
* **Użytkownik**: Informacje o użytkowniku.

## <a name="limits"></a>Limits

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

Aby uniknąć osiągnięcia limitu szybkości danych, użyj [próbkowania](../../azure-monitor/app/sampling.md).

Aby określić, jak długo dane są przechowywane, zobacz [retencji danych i prywatności](../../azure-monitor/app/data-retention-privacy.md).

## <a name="reference-docs"></a>Dokumentacja

* [Dokumentacja platformy ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)
* [Dokumentacja języka Java](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [Dokumentacja języka JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Zestaw SDK systemu Android](https://github.com/Microsoft/ApplicationInsights-Android)
* [Zestaw SDK systemu iOS](https://github.com/Microsoft/ApplicationInsights-iOS)

## <a name="sdk-code"></a>Kod zestawu SDK

* [Zestaw SDK platformy ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Pakiety systemu Windows Server](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Zestaw SDK Java](https://github.com/Microsoft/ApplicationInsights-Java)
* [Zestaw SDK dla platformy Node.js](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [Zestaw SDK dla języka JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)
* [Wszystkie platformy](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## <a name="questions"></a>Pytania

* *Jakie wyjątki Track_() wywołań może zgłosić?*

    Brak. Nie trzeba je opakować w klauzulach try-catch. Jeśli zestaw SDK napotyka problemy, zarejestruje wiadomości w danych wyjściowych konsoli debugowania i — jeśli komunikaty za pośrednictwem — w wyszukiwaniu diagnostycznym.
* *Czy istnieje interfejs API REST do pobierania danych z portalu?*

    Tak, [API dostępu do danych](https://dev.applicationinsights.io/). Inne sposoby, aby wyodrębnić dane obejmują [wyeksportować z analizy w usłudze Power BI](../../azure-monitor/app/export-power-bi.md ) i [Eksport ciągły](../../azure-monitor/app/export-telemetry.md).

## <a name="next"></a>Następne kroki

* [Wyszukiwanie zdarzeń i dzienników](../../azure-monitor/app/diagnostic-search.md)
* [Rozwiązywanie problemów](../../azure-monitor/app/troubleshoot-faq.md)
