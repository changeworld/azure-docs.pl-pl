---
title: Filtrowanie i wstępne przetwarzanie w zestawie SDK Application Insights platformy Azure | Microsoft Docs
description: Napisz procesory telemetrii i inicjatory telemetrii dla zestawu SDK w celu filtrowania lub dodawania właściwości do danych przed wysłaniem telemetrii do portalu Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/23/2016
ms.openlocfilehash: 550ac9ff3b425e682fdda16501613aa41a80d765
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847245"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtrowanie i wstępne przetwarzanie danych telemetrycznych w zestawie Application Insights SDK

Można napisać i skonfigurować wtyczki dla zestawu SDK Application Insights, aby dostosować sposób wzbogacania i przetwarzania danych telemetrycznych przed wysłaniem ich do usługi Application Insights.

* [Próbkowanie](sampling.md) zmniejsza ilość danych telemetrycznych bez wpływu na dane statystyczne. Przechowuje on wspólnie powiązane punkty danych, dzięki czemu można przechodzić między nimi podczas diagnozowania problemu. W portalu łączna liczba jest mnożona, aby skompensować próbkowanie.
* Filtrowanie za pomocą procesorów telemetrii umożliwia filtrowanie danych telemetrycznych w zestawie SDK przed wysłaniem ich do serwera. Na przykład można zmniejszyć ilość danych telemetrycznych, wykluczając żądania od robotów. Filtrowanie to bardziej podstawowe podejście do ograniczania ruchu niż próbkowanie. Pozwala ona na większą kontrolę nad tym, co jest przesyłane, ale należy pamiętać, że ma to wpływ na dane statystyczne — na przykład w przypadku filtrowania wszystkich pomyślnych żądań.
* [Inicjatory telemetrii dodają lub modyfikują właściwości](#add-properties) do wszelkich danych telemetrycznych wysyłanych z aplikacji, w tym danych telemetrycznych z modułów standardowych. Można na przykład dodać wartości obliczone; lub numery wersji, przez które mają być filtrowane dane w portalu.
* [Interfejs API zestawu SDK](../../azure-monitor/app/api-custom-events-metrics.md) służy do wysyłania niestandardowych zdarzeń i metryk.

Przed rozpoczęciem:

* Zainstaluj odpowiedni zestaw SDK dla aplikacji: [ASP.NET](asp-net.md), [ASP.NET Core](asp-net-core.md), [nie http/Worker dla platformy .NET/.NET Core](worker-service.md), [Java](../../azure-monitor/app/java-get-started.md) lub [JavaScript](javascript.md)

<a name="filtering"></a>

## <a name="filtering"></a>Filtrowanie

Ta technika zapewnia bezpośrednią kontrolę nad tym, co jest uwzględnione lub wykluczone ze strumienia telemetrii. Filtrowanie może służyć do porzucania elementów telemetrycznych z wysyłania do Application Insights. Można jej używać w połączeniu z próbkami lub oddzielnie.

Aby odfiltrować dane telemetryczne, napisz procesor telemetrii i zarejestruj go przy użyciu `TelemetryConfiguration`. Wszystkie dane telemetryczne przechodzą przez procesor i można je usunąć ze strumienia lub przekazać do następnego procesora w łańcuchu. Obejmuje to dane telemetryczne z modułów standardowych, takich jak moduł zbierający żądania HTTP i moduł zbierający zależności oraz dane telemetryczne, które zostały przez Ciebie śledzone. Można na przykład odfiltrować dane telemetryczne dotyczące żądań z robotów lub pomyślnych wywołań zależności.

> [!WARNING]
> Filtrowanie danych telemetrycznych wysyłanych z zestawu SDK przy użyciu procesorów może spowodować pochylenie statystyk, które są widoczne w portalu, i utrudnia obserwowanie powiązanych elementów.
>
> Zamiast tego należy rozważyć użycie [próbkowania](../../azure-monitor/app/sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Tworzenie procesora telemetriiC#()

1. Aby utworzyć filtr, zaimplementuj `ITelemetryProcessor`.

    Należy zauważyć, że procesory telemetrii konstruują łańcuch przetwarzania. Podczas tworzenia wystąpienia procesora telemetrii otrzymujesz odwołanie do następnego procesora w łańcuchu. Gdy punkt danych telemetrii jest przesyłany do metody procesu, wykonuje jego działanie, a następnie wywołuje (lub nie wywołuje) następnego procesora telemetrii w łańcuchu.

    ```csharp
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
    {
        private ITelemetryProcessor Next { get; set; }

        // next will point to the next TelemetryProcessor in the chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }

        public void Process(ITelemetry item)
        {
            // To filter out an item, return without calling the next processor.
            if (!OKtoSend(item)) { return; }

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }
    }
    ```

2. Dodaj procesor.

**Aplikacje ASP.NET** Wstaw ten fragment kodu w pliku ApplicationInsights. config:

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

Można przekazać wartości ciągu z pliku. config, podając publiczne właściwości o nazwie w klasie.

> [!WARNING]
> Należy zachować ostrożność dopasowania nazwy typu i wszystkich nazw właściwości w pliku. config do nazw klas i właściwości w kodzie. Jeśli plik. config odwołuje się do nieistniejącego typu lub właściwości, zestaw SDK może dyskretnie niepowodzeniem wysyłać żadnych danych telemetrycznych.
>

**Alternatywnie** można zainicjować filtr w kodzie. W odpowiedniej klasie inicjującej — na przykład AppStart w `Global.asax.cs` — Włóż procesor do łańcucha:

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

TelemetryClients utworzony po tym punkcie będzie używać procesorów.

**ASP.NET Core/aplikacje usługi Worker**

> [!NOTE]
> Dodawanie procesora przy użyciu `ApplicationInsights.config` lub użycie `TelemetryConfiguration.Active` jest nieprawidłowe dla aplikacji ASP.NET Core lub jeśli używasz zestawu Microsoft. ApplicationInsights. WorkerService SDK.

W przypadku aplikacji pisanych przy użyciu [ASP.NET Core](asp-net-core.md#adding-telemetry-processors) lub [WorkerService](worker-service.md#adding-telemetry-processors)Dodawanie nowych `TelemetryProcessor` odbywa się przy użyciu metody rozszerzenia `AddApplicationInsightsTelemetryProcessor` na `IServiceCollection`, jak pokazano poniżej. Ta metoda jest wywoływana w metodzie `ConfigureServices` klasy `Startup.cs`.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<SuccessfulDependencyFilter>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<AnotherProcessor>();
    }
```

### <a name="example-filters"></a>Przykładowe filtry

#### <a name="synthetic-requests"></a>Żądania syntetyczne

Odfiltruj testy botów i sieci Web. Mimo że Eksplorator metryk umożliwia odfiltrowanie źródeł syntetycznych, ta opcja zmniejsza ilość ruchu i rozmiaru pozyskiwania przez filtrowanie ich w samym zestawie SDK.

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>Niepowodzenie uwierzytelniania

Odfiltruj żądania z odpowiedzią "401".

```csharp
public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, return without calling the next processor.
        return;
    }

    // Send everything else
    this.Next.Process(item);
}
```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Odfiltruj szybkie zdalne wywołania zależności

Jeśli chcesz tylko zdiagnozować wywołania, które są powolne, odfiltruj szybkie.

> [!NOTE]
> Spowoduje to pochylenie statystyk widocznych w portalu.
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

W [tym blogu](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) opisano projekt służący do diagnozowania problemów z zależnościami przez automatyczne wysyłanie zwykłych poleceń ping do zależności.

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>Aplikacje sieci Web w języku JavaScript

**Filtrowanie przy użyciu ITelemetryInitializer**

1. Utwórz funkcję wywołania zwrotnego inicjatora telemetrii. Funkcja wywołania zwrotnego przyjmuje `ITelemetryItem` jako parametr, który jest przetwarzanym zdarzeniem. Zwrócenie `false` z tego wywołania zwrotnego spowoduje odfiltrowanie elementu telemetrii.  

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
        return false;
     }
  
     return true;
   };
   ```

2. Dodaj wywołanie zwrotne inicjatora telemetrii:

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>Dodaj/Modyfikuj właściwości: ITelemetryInitializer


Użyj inicjatorów telemetrii, aby wzbogacić telemetrię z dodatkowymi informacjami i/lub aby zastąpić właściwości telemetryczne ustawione przez standardowe moduły telemetrii.

Na przykład Application Insights dla pakietu sieci Web zbiera dane telemetryczne o żądaniach HTTP. Domyślnie flaguje jako nieudane wszystkie żądania z kodem odpowiedzi > = 400. Jeśli jednak chcesz traktować 400 jako pomyślne, możesz podać inicjator telemetrii, który ustawia właściwość Success.

W przypadku dostarczania inicjatora telemetrii jest on wywoływany za każdym razem, gdy wywoływana jest jakakolwiek metoda Track * (). Obejmuje to `Track()` metod wywoływanych przez standardowe moduły telemetrii. Zgodnie z Konwencją te moduły nie ustawiają żadnej właściwości, która została już ustawiona przez inicjator. Inicjatory telemetrii są wywoływane przed wywołaniem procesorów telemetrycznych. W związku z tym wszystkie wzbogacania wykonywane przez inicjatorów są widoczne dla procesorów.

**Definiowanie inicjatora**

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
            requestTelemetry.Properties["Overridden400s"] = "true";
        }
        // else leave the SDK to set the Success property
    }
  }
}
```

**ASP.NET Apps: Załaduj inicjator**

W pliku ApplicationInsights. config:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

*Alternatywnie* można utworzyć wystąpienie inicjatora w kodzie, na przykład w Global.aspx.cs:

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

[Zobacz więcej tego przykładu.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

**ASP.NET Core/aplikacje usługi Worker: ładowanie inicjatora**

> [!NOTE]
> Dodawanie inicjatora przy użyciu `ApplicationInsights.config` lub użycie `TelemetryConfiguration.Active` jest nieprawidłowe dla aplikacji ASP.NET Core lub jeśli używasz zestawu Microsoft. ApplicationInsights. WorkerService SDK.

W przypadku aplikacji pisanych przy użyciu [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) lub [WorkerService](worker-service.md#adding-telemetryinitializers)Dodawanie nowego `TelemetryInitializer` odbywa się przez dodanie go do kontenera iniekcji zależności, jak pokazano poniżej. Odbywa się to w `Startup.ConfigureServices` metodzie.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

### <a name="java-telemetry-initializers"></a>Inicjatory telemetrii Java

[Dokumentacja zestawu Java SDK](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.telemetryinitializer?view=azure-java-stable)

```Java
public interface TelemetryInitializer
{ /** Initializes properties of the specified object. * @param telemetry The {@link com.microsoft.applicationinsights.telemetry.Telemetry} to initialize. */

void initialize(Telemetry telemetry); }
```

Następnie zarejestruj inicjator niestandardowy w pliku ApplicationInsights. XML.

```xml
<Add type="mypackage.MyConfigurableContextInitializer">
    <Param name="some_config_property" value="some_value" />
</Add>
```

### <a name="javascript-telemetry-initializers"></a>Inicjatory telemetrii JavaScript
*JavaScript*

Wstaw inicjatora telemetrii bezpośrednio po kodzie inicjalizacji uzyskanym z portalu:

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

Podsumowanie właściwości nieniestandardowych dostępnych w telemetryItem można znaleźć w temacie [Application Insights Export Data Model](../../azure-monitor/app/export-data-model.md).

Można dodać tyle inicjatorów, ile chcesz, i są one wywoływane w kolejności, w jakiej zostały dodane.

### <a name="example-telemetryinitializers"></a>Przykład TelemetryInitializers

#### <a name="add-custom-property"></a>Dodaj właściwość niestandardową

Poniższy przykładowy inicjator dodaje właściwość niestandardową do wszystkich śledzonych danych telemetrycznych.

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-cloud-role-name"></a>Dodaj nazwę roli w chmurze

Poniższy przykładowy inicjator ustawia nazwę roli chmury dla każdej śledzonej telemetrii.

```csharp
public void Initialize(ITelemetry telemetry)
{
    if(string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor i ITelemetryInitializer

Jaka jest różnica między procesorami telemetrii i inicjatorami telemetrii?

* Istnieją pewne nakładanie się na to, co można zrobić z nimi: obie mogą służyć do dodawania lub modyfikowania właściwości telemetrii, ale zalecane jest używanie inicjatorów w tym celu.
* TelemetryInitializers zawsze uruchamiaj przed TelemetryProcessors.
* TelemetryInitializers może być wywoływana więcej niż raz. Według Konwencji nie ustawili żadnej właściwości, która została już ustawiona.
* TelemetryProcessors umożliwia całkowite zastępowanie lub odrzucanie elementu telemetrii.
* Wszystkie zarejestrowane TelemetryInitializers są gwarantowane dla każdego elementu telemetrii. W przypadku procesorów telemetrii zestaw SDK gwarantuje wywołanie bardzo pierwszego procesora telemetrii. Bez względu na to, czy pozostała część procesorów jest wywoływana, czy nie, jest określana przez poprzednie procesory telemetrii.
* Użyj TelemetryInitializers, aby wzbogacić dane telemetryczne z dodatkowymi właściwościami, lub Zastąp istniejący. Użyj TelemetryProcessor, aby odfiltrować dane telemetryczne.

## <a name="troubleshooting-applicationinsightsconfig"></a>Rozwiązywanie problemów z ApplicationInsights. config

* Upewnij się, że w pełni kwalifikowana nazwa typu i nazwa zestawu są poprawne.
* Upewnij się, że plik ApplicationInsights. config znajduje się w katalogu wyjściowym i zawiera wszystkie ostatnie zmiany.

## <a name="reference-docs"></a>Dokumentacja

* [Przegląd interfejsu API](../../azure-monitor/app/api-custom-events-metrics.md)
* [Odwołanie ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>Kod zestawu SDK

* [Zestaw SDK platformy ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Zestaw SDK dla języka JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>Następne kroki
* [Wyszukaj zdarzenia i dzienniki](../../azure-monitor/app/diagnostic-search.md)
* [Próbkowanie](../../azure-monitor/app/sampling.md)
* [Rozwiązywanie problemów](../../azure-monitor/app/troubleshoot-faq.md)
