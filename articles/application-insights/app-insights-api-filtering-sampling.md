---
title: Filtrowanie i przetwarzania wstępnego w zestawie SDK Azure Application Insights | Dokumentacja firmy Microsoft
description: Zapisywanie procesorów Telemetrii oraz inicjatory danych Telemetrycznych zestawu SDK, filtrowanie, lub dodać właściwości do danych przed wysłaniem danych telemetrycznych do portalu usługi Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 38a9e454-43d5-4dba-a0f0-bd7cd75fb97b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 11/23/2016
ms.author: mbullwin; borooji
ms.openlocfilehash: 987ae184a0812f24df99a0b6e6543c8be55a9e79
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtrowanie i przetwarzania wstępnego telemetrii w zestaw SDK usługi Application Insights


Możesz wpisać i skonfigurować wtyczek dla zestawu SDK usługi Application Insights do dostosowywania jak przechwycone dane telemetryczne i przetworzone przed wysłaniem ich do usługi Application Insights.

* [Próbkowanie](app-insights-sampling.md) zmniejsza ilość danych telemetrycznych bez wpływu na statystyk. Przechowuje się ze sobą powiązane punktów danych, dzięki czemu można przechodzić między nimi podczas diagnozowania problemu. W portalu całkowitej liczby są mnożone odpowiednio do pobierania próbek.
* Filtrowanie danych Telemetrycznych procesorów [dla platformy ASP.NET](#filtering) lub [Java](app-insights-java-filter-telemetry.md) pozwala wybrać lub zmodyfikować danych telemetrycznych w zestawie SDK przed wysłaniem do serwera. Na przykład można zmniejszyć ilość danych telemetrycznych, wyłączając żądań z robotów. Ale filtrowanie jest bardziej podstawową podejście do zmniejszenie ruchu niż próbkowanie. Umożliwia większą kontrolę nad co to są przesyłane, ale masz należy pamiętać, że ma to wpływ na statystyk — na przykład, jeśli odfiltrowywania wszystkich pomyślnych żądań.
* [Inicjatory telemetrii dodać właściwości](#add-properties) do żadnych danych telemetrycznych wysłanych z aplikacji, w tym dane telemetryczne z modułów standardowych. Na przykład można dodać wartości obliczeniowej. lub numery wersji do filtrowania danych w portalu.
* [Interfejs API zestawu SDK](app-insights-api-custom-events-metrics.md) służy do wysyłania zdarzeń niestandardowych i metryki.

Przed rozpoczęciem:

* Zainstaluj usługi Application Insights [zestawu SDK dla platformy ASP.NET](app-insights-asp-net.md) lub [zestawu SDK dla języka Java](app-insights-java-get-started.md) w aplikacji.

<a name="filtering"></a>

## <a name="filtering-itelemetryprocessor"></a>Filtrowanie: ITelemetryProcessor
Ta metoda umożliwia bardziej bezpośrednią kontrolę nad co to jest dołączone lub wykluczone ze strumienia danych telemetrycznych. Może być używany w połączeniu z włączonym próbkowaniem, lub oddzielnie.

Aby filtrować dane telemetryczne, zapisać procesora telemetrii i zarejestrowanie go za pomocą zestawu SDK. Wszystkie dane telemetryczne przechodzi przez procesor, a użytkownik może usunąć ją z strumienia lub dodać właściwości. W tym dane telemetryczne z modułów standardowych, takich jak moduł zbierający żądania HTTP i zależności modułu zbierającego, a także dane telemetryczne zapisane samodzielnie. Na przykład można odfiltrować dane telemetryczne dotyczące żądań z robotów lub wywołania zależności powiodło się.

> [!WARNING]
> Filtrowanie telemetrycznych wysłanych z zestawu SDK procesorów można pochylanie statystyki, która zostanie wyświetlony w portalu i utrudnia wykonaj elementy powiązane.
>
> Zamiast tego Rozważ użycie [próbkowania](app-insights-sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Utwórz procesora telemetrii (C#)
1. Sprawdź, czy zestaw SDK usługi Application Insights w projekcie wersji 2.0.0 lub nowszej. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań w usłudze Visual Studio i wybierz polecenie Zarządzaj pakietami NuGet. W Menedżerze pakietów NuGet Sprawdź Microsoft.ApplicationInsights.Web.
2. Aby utworzyć filtr, zaimplementuj ITelemetryProcessor. Jest to inny punkt rozszerzalności, takich jak moduł telemetrii, inicjatora telemetrii i kanału danych telemetrycznych.

    Zwróć uwagę, że procesorów Telemetrii utworzyć łańcuch przetwarzania. Można utworzyć wystąpienia procesora telemetrii, należy przekazać łącze do następnej procesora w łańcuchu. Gdy punkt danych telemetrycznych jest przekazywany do metody procesu, nie jego pracy i następnie wywołuje dalej procesora Telemetrii w łańcuchu.

    ``` C#

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
1. Wstaw ApplicationInsights.config to:

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(Jest to tej samej sekcji, w którym zainicjować filtru próbkowania).

Można przekazać wartości ciągu z pliku .config, zapewniając publicznej właściwości o nazwie w klasie.

> [!WARNING]
> Należy zadbać, aby dopasować nazwy typu, a wszystkie nazwy właściwości w pliku .config do nazwy klasy i właściwości w kodzie. Jeśli plik .config odwołuje się do nieistniejącego typu lub właściwości, dyskretnie może uniemożliwić Wyślij wszystkie dane telemetryczne zestawu SDK.
>
>

**Alternatywnie** można zainicjować filtru w kodzie. W klasie inicjowania odpowiedniego — na przykład AppStart Global.asax.cs - Wstaw procesora w łańcuchu:

```csharp

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

TelemetryClients utworzone po tym punkcie będzie używać procesorów.

### <a name="example-filters"></a>Przykładowe filtry
#### <a name="synthetic-requests"></a>Syntetyczne żądań
Filtrowanie testów robotów i sieci web. Mimo że Eksploratora metryk umożliwia filtrowanie syntetycznego źródła, tej opcji zmniejsza ruch przez filtrowanie ich na zestaw SDK.

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else:
      this.Next.Process(item);
    }

```

#### <a name="failed-authentication"></a>Uwierzytelnianie nie powiodło się
Filtrowanie żądań z odpowiedzi "401".

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

#### <a name="filter-out-fast-remote-dependency-calls"></a>Odfiltrować wywołania zależności fast zdalnego
Jeśli chcesz zdiagnozować wywołania, które są przetwarzane wolno, filtrować te fast.

> [!NOTE]
> Pochylanie przypadku statystyk, który zostanie wyświetlony w portalu. Na wykresie zależności będzie wyglądać tak, jakby wywołania zależności są wszystkie błędy.
>
>

``` C#

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
[Ten blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) opisuje projektu do diagnozowania problemów zależności automatycznie przesyłając regularnego polecenia ping zależności.


<a name="add-properties"></a>

## <a name="add-properties-itelemetryinitializer"></a>Dodaj właściwości: ITelemetryInitializer
Inicjatory telemetrii używany do definiowania właściwości globalne, które są wysyłane z wszystkie dane telemetryczne; i zastąpienie zachowania wybranych modułów standardowe telemetrii.

Na przykład pakiet sieci Web w usłudze Application Insights zbiera dane telemetryczne dotyczące żądania HTTP. Domyślnie flagi jako zakończony niepowodzeniem, wszystkie żądania z kodem odpowiedzi > = 400. Ale zaliczenie 400 sukcesu, możesz podać inicjatora telemetrii, która ustawia właściwość Powodzenie.

Jeśli podasz inicjatora telemetrii jest to zawsze, gdy są nazywane dowolnej z metod Track*(). Dotyczy to również metody wywołane przez moduły standardowe telemetrii. Konwencja te moduły nie należy ustawiać dowolnej właściwości, która została już ustawiona przez inicjatora.

**Zdefiniuj z inicjatora**

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

**Ładowanie z inicjatora**

In ApplicationInsights.config:

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*Alternatywnie* można utworzyć wystąpienia inicjatora w kodzie, na przykład w pliku Global.aspx.cs:

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

### <a name="javascript-telemetry-initializers"></a>Inicjatory telemetrii JavaScript
*JavaScript*

Wstaw inicjatora telemetrii bezpośrednio po kodzie inicjowania pochodzący z portalu:

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

                // To check the telemetry item’s type - for example PageView:
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

Podsumowanie właściwości niestandardowych z systemem innym niż dostępna w telemetryItem, zobacz [Model danych eksportu Insights aplikacji](app-insights-export-data-model.md).

Możesz dodać dowolną liczbę inicjatory dowolnie.

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor i ITelemetryInitializer
Jaka jest różnica między procesorami telemetrii i danych telemetrycznych inicjatory?

* Istnieją pewne nakładania się w co można zrobić z nimi: zarówno pozwala dodać właściwości do danych telemetrycznych.
* TelemetryInitializers są zawsze uruchamiane przed TelemetryProcessors.
* TelemetryProcessors umożliwiają całkowicie zastąpić lub odrzucić element telemetrii.
* TelemetryProcessors nie Przetwarzaj telemetrii licznika wydajności.

## <a name="troubleshooting-applicationinsightsconfig"></a>Rozwiązywanie problemów z ApplicationInsights.config
* Upewnij się, że pełni kwalifikowana nazwa typu i nazwy zestawu są poprawne.
* Upewnij się, że plik applicationinsights.config znajduje się w katalogu danych wyjściowych i zawiera wszystkie najnowsze zmiany.

## <a name="reference-docs"></a>Dokumentacja
* [Przegląd interfejsu API](app-insights-api-custom-events-metrics.md)
* [Platformy ASP.NET — dokumentacja](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>Kod zestawu SDK
* [Platformy ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ZESTAW SDK PLATFORMY ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Zestaw SDK dla języka JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>Następne kroki
* [Wyszukiwanie zdarzeń i dzienniki](app-insights-diagnostic-search.md)
* [Próbkowanie](app-insights-sampling.md)
* [Rozwiązywanie problemów](app-insights-troubleshoot-faq.md)
