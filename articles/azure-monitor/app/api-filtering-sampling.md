---
title: Filtrowanie i wstępne przetwarzanie w zestaw SDK usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Napisz Telemetrii procesorów i inicjatory danych Telemetrycznych zestawu SDK, aby filtrować lub dodać właściwości do danych przed wysłaniem telemetrii do portalu usługi Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 38a9e454-43d5-4dba-a0f0-bd7cd75fb97b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/23/2016
ms.author: mbullwin
ms.openlocfilehash: 062b565369c3b6e877d36f883a152ca6c013e0cf
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67479654"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtrowanie i wstępne przetwarzanie danych telemetrycznych w zestaw SDK usługi Application Insights


Można napisać i konfigurować wtyczki zestaw Application Insights SDK dostosować dane telemetryczne jest przechwytywane i przetworzone przed wysłaniem ich do usługi Application Insights.

* [Próbkowanie](../../azure-monitor/app/sampling.md) powoduje zmniejszenie ilości danych telemetrycznych, bez wywierania wpływu na statystyk. Przechowuje się ze sobą powiązane, punktów danych, dzięki czemu możesz przechodzić między ich podczas diagnozowania problemu. W portalu całkowitej liczby są mnożone celu kompensacji dla pobierania próbek.
* Filtrowanie danych Telemetrycznych procesorów [dla platformy ASP.NET](#filtering) lub [Java](../../azure-monitor/app/java-filter-telemetry.md) umożliwia wybranie lub modyfikowanie danych telemetrycznych w zestawie SDK przed wysłaniem ich do serwera. Na przykład można zmniejszyć ilość danych telemetrycznych przesyłanych przez wykluczenie żądań z robotów. Jednak filtrowanie jest bardziej podstawowe podejście do zmniejszenia ruchu niż próbkowanie. Umożliwia większą kontrolę nad czym są przesyłane, ale trzeba należy pamiętać, że zmiany będą dotyczyć statystyk — na przykład, jeśli odfiltrować wszystkie żądania zakończone powodzeniem.
* [Inicjatory dane telemetryczne, dodawać właściwości](#add-properties) do żadnych danych telemetrycznych wysyłanych z aplikacji, w tym dane telemetryczne z modułów standardowych. Na przykład można dodać obliczone wartości; lub numery wersji do filtrowania danych w portalu.
* [Interfejs API zestawu SDK](../../azure-monitor/app/api-custom-events-metrics.md) służy do wysyłania niestandardowych zdarzeń i metryk.

Przed rozpoczęciem:

* Zainstaluj usługę Application Insights [zestawu SDK dla platformy ASP.NET](../../azure-monitor/app/asp-net.md) lub [zestawu SDK dla języka Java](../../azure-monitor/app/java-get-started.md) w swojej aplikacji.

<a name="filtering"></a>

## <a name="filtering-itelemetryprocessor"></a>Filtrowanie: ITelemetryProcessor
Ta technika umożliwia bardziej bezpośrednią kontrolę nad czym jest dołączone lub wykluczone z strumień danych telemetrycznych. Można go użyć w połączeniu z włączonym próbkowaniem, lub oddzielnie.

Aby filtrować dane telemetryczne, zapis podmiot przetwarzający dane telemetryczne i zarejestruj je przy użyciu zestawu SDK. Wszystkie dane telemetryczne przechodzi przez procesor, a użytkownik może usunąć ją z strumienia lub dodać właściwości. W tym dane telemetryczne z standardowe moduły, takie jak moduł zbierający żądania HTTP i moduł zbierający zależności, a także dane telemetryczne, które zostały napisane samodzielnie. Na przykład można odfiltrować telemetrii dotyczącej żądań z roboty lub wywołania zależności pomyślnie.

> [!WARNING]
> Filtrowanie danych telemetrycznych wysyłanych z zestawu SDK przy użyciu procesorów można pochylanie statystyk, które pojawi się w portalu i utrudnia postępuj zgodnie z powiązanych elementów.
>
> Zamiast tego Rozważ użycie [próbkowania](../../azure-monitor/app/sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Utworzyć procesor danych telemetrycznych (C#)
1. Sprawdź, czy zestaw SDK usługi Application Insights w projekcie jest w wersji 2.0.0 lub nowszej. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań w usłudze Visual Studio i wybierz polecenie Zarządzaj pakietami NuGet. Menedżer pakietów NuGet Sprawdź Microsoft.ApplicationInsights.Web.
2. Aby utworzyć filtr, należy zaimplementować ITelemetryProcessor. Jest to inny punkt rozszerzalności, takich jak moduł danych telemetrycznych, inicjatora telemetrii i kanału danych telemetrycznych.

    Należy zauważyć, że procesorów Telemetrii utworzenia łańcucha przetwarzania. Podczas tworzenia wystąpienia procesora telemetrii polega na przekazaniu łącze do następnej procesora w łańcuchu. Gdy punkt danych telemetrii jest przekazywany do metody procesu, wykonuje swoją pracę, a następnie wywołuje następny procesor danych Telemetrycznych w łańcuchu.

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

public class SuccessfulDependencyFilter : ITelemetryProcessor
{

    private ITelemetryProcessor Next { get; set; }

    // You can pass values from .config
    public string MyParamFromConfigFile { get; set; }

    // Link processors to each other in a chain.
    public SuccessfulDependencyFilter(ITelemetryProcessor next)
    {
        this.Next = next;
    }
    public void Process(ITelemetry item)
    {
        // To filter out an item, just return
        if (!OKtoSend(item)) { return; }
        // Modify the item if required
        ModifyItem(item);

        this.Next.Process(item);
    }

    // Example: replace with your own criteria.
    private bool OKtoSend (ITelemetry item)
    {
        var dependency = item as DependencyTelemetry;
        if (dependency == null) return true;

        return dependency.Success != true;
    }

    // Example: replace with your own modifiers.
    private void ModifyItem (ITelemetry item)
    {
        item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
    }
}
```
3. W pliku ApplicationInsights.config, Wstaw to:

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

(Jest to tej samej sekcji, w którym zainicjować filtr próbkowania).

Dostarczając publicznych nazwane właściwości w klasie, można przekazać wartości ciągu z pliku Config.

> [!WARNING]
> Należy zadbać, aby dopasować nazwę typu, a wszystkie nazwy właściwości w pliku config nazwy klasy i właściwości w kodzie. Jeśli plik .config odwołuje się do nieistniejącego typu lub właściwości, dyskretnie może nie wysyła żadnych danych telemetrycznych zestawu SDK.
>
>

**Alternatywnie** można zainicjować filtru w kodzie. Podczas inicjowania odpowiedniej klasy — na przykład AppStart Global.asax.cs - Wstaw procesor do łańcucha:

```csharp
var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

TelemetryClients utworzony po tym punkcie użyje procesorów.

### <a name="example-filters"></a>Przykład filtrów
#### <a name="synthetic-requests"></a>Syntetyczne żądania
Odfiltruj testy sieci web i botów. Chociaż Eksplorator metryk udostępnia opcję, aby odfiltrować syntetycznego źródła, ta opcja zmniejsza ruch, filtrując ją w zestawie SDK.

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>Uwierzytelnianie nie powiodło się
Filtrowanie żądań z odpowiedź "401".

```csharp
public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain:
        return;
    }
    // Send everything else:
    this.Next.Process(item);
}
```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Filtrowanie wywołania zależności szybko zdalnego
Jeśli chcesz zdiagnozować wywołań, które działają wolno, filtrować te, które szybko.

> [!NOTE]
> Pochylanie przypadku statystyk, który zostanie wyświetlony w portalu. Wykres zależności będzie wyglądać tak, jakby wywołania zależności są wszystkie błędy.
>
>

```csharp
public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}
```

#### <a name="diagnose-dependency-issues"></a>Diagnozowanie problemów z zależnością
[Ten blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) opisuje projekt do diagnozowania problemów z zależnością przy regularnych polecenia ping są automatycznie wysyłane do zależności.


<a name="add-properties"></a>

## <a name="add-properties-itelemetryinitializer"></a>Dodaj właściwości: ITelemetryInitializer
Użyj danych telemetrycznych inicjatory, aby zdefiniować właściwości globalne, które są wysyłane z wszystkie dane telemetryczne; i zastąpić zachowanie wybranych modułów standardowe dane telemetryczne.

Na przykład pakiet sieci Web w usłudze Application Insights gromadzi dane telemetryczne dotyczące żądań HTTP. Domyślnie flagi jako zakończony niepowodzeniem, wszystkie żądania z kodem odpowiedzi > = 400. Ale zaliczenie 400 sukcesu, możesz podać inicjatora telemetrii, która ustawia właściwość sukces.

Jeśli podasz inicjatora telemetrii, jest ona wywoływana zawsze wtedy, gdy noszą nazwę jednej z metod Track*(). Dotyczy to również metody wywołane przez moduły standardowe dane telemetryczne. Zgodnie z Konwencją te moduły, nie należy ustawiać dowolnej właściwości, która została już ustawiona przez inicjatora.

**Zdefiniuj swoje inicjatora**

*C#*

```csharp
using System;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that overrides the default SDK
   * behavior of treating response codes >= 400 as failed requests
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        // Is this a TrackRequest() ?
        if (requestTelemetry == null) return;
        int code;
        bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
        if (!parsed) return;
        if (code >= 400 && code < 500)
        {
            // If we set the Success property, the SDK won't change it:
            requestTelemetry.Success = true;
            // Allow us to filter these requests in the portal:
            requestTelemetry.Context.Properties["Overridden400s"] = "true";
        }
        // else leave the SDK to set the Success property      
    }
  }
}
```

**Ładowanie usługi inicjatora**

In ApplicationInsights.config:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

*Alternatywnie* można utworzyć wystąpienie inicjatora w kodzie, na przykład w pliku Global.aspx.cs:

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers
    .Add(new MyTelemetryInitializer());
}
```


[Zobacz więcej w tym przykładzie.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>

### <a name="java-telemetry-initializers"></a>Inicjatory telemetrii języka Java

[Dokumentacja zestawu SDK dla języka Java](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.telemetryinitializer?view=azure-java-stable)

```Java
public interface TelemetryInitializer
{ /** Initializes properties of the specified object. * @param telemetry The {@link com.microsoft.applicationinsights.telemetry.Telemetry} to initialize. */

void initialize(Telemetry telemetry); }
```

Następnie należy zarejestrować inicjatora niestandardowej w pliku applicationinsights.xml.

```xml
<Add type="mypackage.MyConfigurableContextInitializer">
    <Param name="some_config_property" value="some_value" />
</Add>
```

### <a name="javascript-telemetry-initializers"></a>Inicjatory telemetrii JavaScript
*JavaScript*

Wstaw inicjatora telemetrii natychmiast po kod inicjujący, która jest wyświetlana w portalu:

```JS
<script type="text/javascript">
    // ... initialization code
    ...({
        instrumentationKey: "your instrumentation key"
    });
    window.appInsights = appInsights;


    // Adding telemetry initializer.
    // This is called whenever a new telemetry item
    // is created.

    appInsights.queue.push(function () {
        appInsights.context.addTelemetryInitializer(function (envelope) {
            var telemetryItem = envelope.data.baseData;

            // To check the telemetry items type - for example PageView:
            if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                // this statement removes url from all page view documents
                telemetryItem.url = "URL CENSORED";
            }

            // To set custom properties:
            telemetryItem.properties = telemetryItem.properties || {};
            telemetryItem.properties["globalProperty"] = "boo";

            // To set custom metrics:
            telemetryItem.measurements = telemetryItem.measurements || {};
            telemetryItem.measurements["globalMetric"] = 100;
        });
    });

    // End of inserted code.

    appInsights.trackPageView();
</script>
```

Podsumowanie właściwości niestandardowe nie, dostępne na telemetryItem, zobacz [Insights aplikacji eksportowanie modelu danych](../../azure-monitor/app/export-data-model.md).

Możesz dodać dowolną liczbę inicjatory, jak chcesz.

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor i ITelemetryInitializer
Jaka jest różnica między procesorami telemetrii i danych telemetrycznych inicjatory?

* Istnieją pewne nakładania się w co można zrobić z nimi: zarówno można dodać właściwości do danych telemetrycznych.
* TelemetryInitializers są zawsze uruchamiane przed TelemetryProcessors.
* TelemetryProcessors umożliwiają całkowitego zastąpienia lub odrzucenia elementu telemetrii.
* TelemetryProcessors nie Przetwarzaj telemetryczne licznika wydajności.

## <a name="troubleshooting-applicationinsightsconfig"></a>Rozwiązywanie problemów z pliku ApplicationInsights.config
* Upewnij się, że w pełni kwalifikowanej nazwy typu i nazwy zestawu są poprawne.
* Upewnij się, że plik applicationinsights.config znajduje się w katalogu danych wyjściowych i zawiera wszystkie najnowsze zmiany.

## <a name="reference-docs"></a>Dokumentacja
* [Przegląd interfejsu API](../../azure-monitor/app/api-custom-events-metrics.md)
* [Dokumentacja platformy ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>Kod zestawu SDK
* [Zestaw SDK platformy ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Zestaw SDK dla języka JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>Następne kroki
* [Wyszukiwanie zdarzeń i dzienników](../../azure-monitor/app/diagnostic-search.md)
* [Próbkowanie](../../azure-monitor/app/sampling.md)
* [Rozwiązywanie problemów](../../azure-monitor/app/troubleshoot-faq.md)
