---
title: Parametry połączenia na platformie Azure Application Insights | Microsoft Docs
description: Jak używać parametrów połączenia.
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.reviewer: mbullwin
ms.openlocfilehash: ad56f1f15936bc3f3f1ac75396352206f68e7a61
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671481"
---
# <a name="connection-strings"></a>Parametry połączeń

## <a name="overview"></a>Omówienie

Parametry połączenia zapewniają użytkownikom usługi Application Insights jedno ustawienie konfiguracji, eliminując konieczność stosowania wielu ustawień serwera proxy. Wysoce przydatne w przypadku intranetowych serwerów sieci Web, suwerennych lub hybrydowych środowisk w chmurze, które chcą wysyłać dane do usługi monitorowania.

Pary klucz wartość umożliwiają użytkownikom łatwe definiowanie kombinacji sufiksu prefiksu dla każdej usługi Application Insights (AI)/produktu.

> [!IMPORTANT]
> Nie zalecamy ustawiania parametrów połączenia i klucza Instrumentacji. W przypadku, gdy użytkownik ustawił oba elementy, w zależności od ustawienia ustawiono pierwszeństwo. 


## <a name="scenario-overview"></a>Omówienie scenariusza 

Scenariusze klientów, które są wizualizowane z największym wpływem:

- Wyjątki zapory lub przekierowania serwera proxy 

    W przypadkach, gdy wymagane jest monitorowanie intranetowego serwera sieci Web, nasze wcześniejsze rozwiązanie poprosiło klientów o dodanie poszczególnych punktów końcowych usługi do konfiguracji. Aby uzyskać więcej informacji, zobacz [tutaj](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#can-i-monitor-an-intranet-web-server). 
    Parametry połączenia oferują lepszą alternatywę, skracając ten nakład pracy do jednego ustawienia. Prosty prefiks, zmiana sufiksu, umożliwia automatyczne zapełnianie i przekierowywanie wszystkich punktów końcowych do odpowiednich usług. 

- Suwerenne lub hybrydowe środowiska chmury

    Użytkownicy mogą wysyłać dane do zdefiniowanego [regionu Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights).
    Parametry połączeń umożliwiają definiowanie ustawień punktu końcowego dla serwerów intranetowych lub ustawień chmury hybrydowej. 

## <a name="getting-started"></a>Wprowadzenie

### <a name="finding-my-connection-string"></a>Szukasz moich parametrów połączenia?

Parametry połączenia są wyświetlane w bloku przegląd zasobu Application Insights.

![parametry połączenia w bloku przeglądu](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>Schemat

#### <a name="max-length"></a>Maksymalna długość

Połączenie ma maksymalną obsługiwaną długość wynoszącą 4096 znaków.

#### <a name="key-value-pairs"></a>Pary klucz-wartość

Parametry połączenia składają się z listy ustawień reprezentowanych jako pary klucz-wartość oddzielone średnikami: `key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>Składnia

- `InstrumentationKey` (np. 00000000-0000-0000-0000-000000000000) parametry połączenia są polem **wymaganym** .
- `Authorization` (np.: iKey) (to ustawienie jest opcjonalne, ponieważ dzisiaj obsługujemy tylko autoryzację iKey).
- `EndpointSuffix` (np.: applicationinsights.azure.cn) ustawienie sufiksu punktu końcowego spowoduje nawiązanie połączenia z zestawem SDK z chmurą platformy Azure. Zestaw SDK będzie gromadzić pozostałe punkty końcowe dla poszczególnych usług.
- Jawne punkty końcowe.
  Każdą usługę można jawnie przesłaniać w parametrach połączenia.
   - `IngestionEndpoint` (np.: https://dc.applicationinsights.azure.com)
   - `LiveEndpoint` (np.: https://live.applicationinsights.azure.com)
   - `ProfilerEndpoint` (np.: https://profiler.applicationinsights.azure.com)
   - `SnapshotEndpoint` (np.: https://snapshot.applicationinsights.azure.com)

#### <a name="endpoint-schema"></a>Schemat punktu końcowego

`<prefix>.<suffix>`
- Prefiks: Określa usługę. 
- Sufiks: definiuje wspólną nazwę domeny.

##### <a name="valid-suffixes"></a>Prawidłowe sufiksy

Oto lista prawidłowych sufiksów 
- applicationinsights.azure.cn
- applicationinsights.us


Zobacz również: https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification


##### <a name="valid-prefixes"></a>Prawidłowe prefiksy

- Pozyskiwanie danych [telemetrycznych](./app-insights-overview.md): `dc`
- [Metryki na żywo](./live-stream.md): `live`
- [Profiler](./profiler-overview.md): `profiler`
- [Migawka](./snapshot-debugger.md): `snapshot`



## <a name="connection-string-examples"></a>Przykłady parametrów połączenia


### <a name="minimal-valid-connection-string"></a>Minimalne prawidłowe parametry połączenia

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

W tym przykładzie ustawiono tylko klucz Instrumentacji.

- Domyślna wartość schematu autoryzacji to "iKey" 
- Klucz Instrumentacji: 00000000-0000-0000-0000-000000000000
- Identyfikatory URI usługi regionalnej są oparte na [ustawieniach domyślnych zestawu SDK](https://github.com/microsoft/ApplicationInsights-dotnet/blob/e50d569cebf485e72e98f4a08a0bc0e30cdf42bc/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs#L6) i łączą się z publiczną globalną platformą Azure:
   - Pozyskiwanie: https://dc.services.visualstudio.com/
   - Metryki na żywo: https://rt.services.visualstudio.com/
   - Profiler: https://agent.azureserviceprofiler.net/
   - Debuger: https://agent.azureserviceprofiler.net/  



### <a name="connection-string-with-endpoint-suffix"></a>Parametry połączenia z sufiksem punktu końcowego

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

W tym przykładzie parametry połączenia określają sufiks punktu końcowego, a zestaw SDK będzie konstruować punkty końcowe usługi.

- Domyślna wartość schematu autoryzacji to "iKey" 
- Klucz Instrumentacji: 00000000-0000-0000-0000-000000000000
- Identyfikatory URI usługi regionalnej są oparte na podanym sufiksie punktu końcowego: 
   - Pozyskiwanie: https://dc.ai.contoso.com
   - Metryki na żywo: https://live.ai.contoso.com
   - Profiler: https://profiler.ai.contoso.com 
   - Debuger: https://snapshot.ai.contoso.com   



### <a name="connection-string-with-explicit-endpoint-overrides"></a>Parametry połączenia z jawnym zastąpień punktu końcowego 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

W tym przykładzie parametry połączenia określają jawne zastąpienia dla każdej usługi. Zestaw SDK będzie używać dokładnych punktów końcowych, które nie są modyfikowane.

- Domyślna wartość schematu autoryzacji to "iKey" 
- Klucz Instrumentacji: 00000000-0000-0000-0000-000000000000
- Identyfikatory URI usługi regionalnej są oparte na wartościach jawnego zastąpienia: 
   - Pozyskiwanie: https:\//custom.com:111/
   - Metryki na żywo: https:\//custom.com:222/
   - Profiler: https:\//custom.com:333/ 
   - Debuger: https:\//custom.com:444/   


## <a name="how-to-set-a-connection-string"></a>Jak ustawić parametry połączenia

Parametry połączenia są obsługiwane w następujących wersjach zestawu SDK:
- .NET i .NET Core v 2.12.0
- Java v, 2.5.1
- 2\.3.0 JavaScript v
- NodeJS v 1.5.0
- 1\.0.0 Python v

Parametry połączenia można ustawić za pomocą kodu, zmiennej środowiskowej lub pliku konfiguracji.



### <a name="environment-variable"></a>Zmienna środowiskowa

- Parametry połączenia: `APPLICATIONINSIGHTS_CONNECTION_STRING`

### <a name="net-sdk-example"></a>Przykład zestawu SDK platformy .NET

TelemetryConfiguration. ConnectionString: https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274

Zestaw .NET jawnie ustawiony:
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

Plik konfiguracji .NET:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```


Plik config. JSON: 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


### <a name="java-sdk-example"></a>Przykład zestawu SDK języka Java


Język Java jawnie ustawiony:
```java
TelemetryConfiguration.getActive().setConnectionString("InstrumentationKey=00000000-0000-0000-0000-000000000000");
```

ApplicationInsights.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000;</ConnectionString>
</ApplicationInsights>
```

### <a name="javascript-sdk-example"></a>Przykład zestawu SDK języka JavaScript

Ważne: język JavaScript nie obsługuje użycia zmiennych środowiskowych.

Za pomocą fragmentu kodu:

```javascript
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("addTelemetryInitializer"),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),t.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```


Konfiguracja ręczna:
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

### <a name="node-sdk-example"></a>Przykład zestawu SDK węzła

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

### <a name="python-sdk-example"></a>Przykład zestawu SDK języka Python

Zalecamy, aby użytkownicy ustawili zmienną środowiskową.

Aby jawnie ustawić parametry połączenia:

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```


## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę w czasie wykonywania za pomocą rozwiązań:

* [Maszyna wirtualna platformy Azure i zestaw skalowania maszyn wirtualnych platformy Azure — aplikacje hostowane](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Serwer usług IIS](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Web Apps](../../azure-monitor/app/azure-web-apps.md)

Rozpocznij pracę w czasie programowania za pomocą rozwiązań:

* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python (wersja zapoznawcza)](../../azure-monitor/app/opencensus-python.md)
