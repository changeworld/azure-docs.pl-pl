---
title: Korelacja telemetrii Application Insights platformy Azure | Microsoft Docs
description: Application Insights korelacji telemetrii
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 06897fffda490cdfcbb2a9cf6f55c7945e8afda0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78367662"
---
# <a name="telemetry-correlation-in-application-insights"></a>Korelacja telemetrii w Application Insights

Na świecie mikrousług każda operacja logiczna wymaga wykonania pracy w różnych składnikach usługi. Każdy z tych składników można monitorować osobno przy użyciu [Application Insights](../../azure-monitor/app/app-insights-overview.md). Application Insights obsługuje korelację rozproszonej telemetrii, za pomocą której można wykryć, który składnik jest odpowiedzialny za błędy lub spadek wydajności.

W tym artykule opisano model danych używany przez Application Insights do skorelowania telemetrii wysyłanej przez wiele składników. Obejmuje to techniki i protokoły propagacji kontekstowej. Obejmuje to również implementację korelacji taktykę w różnych językach i platformach.

## <a name="data-model-for-telemetry-correlation"></a>Model danych korelacji telemetrii

Application Insights definiuje [model danych](../../azure-monitor/app/data-model.md) dla korelacji rozproszonej telemetrii. Aby skojarzyć dane telemetryczne z operacją logiczną, każdy element telemetrii ma pole kontekstu o nazwie `operation_Id`. Ten identyfikator jest współużytkowany przez każdy element telemetrii rozproszonego śledzenia. Nawet jeśli utracisz dane telemetryczne z pojedynczej warstwy, nadal możesz skojarzyć telemetrię zgłoszoną przez inne składniki.

Rozproszone operacje logiczne zwykle składają się z zestawu mniejszych operacji, które są żądaniami przetworzonymi przez jeden ze składników. Te operacje są definiowane przez dane [telemetryczne żądania](../../azure-monitor/app/data-model-request-telemetry.md). Każdy element telemetrii żądania ma własne `id`, które jednoznacznie identyfikują i globalnie. Wszystkie elementy telemetrii (takie jak ślady i wyjątki), które są skojarzone z żądaniem, powinny ustawić `operation_parentId` na wartość `id`żądania.

Każda operacja wychodząca, taka jak wywołanie HTTP w innym składniku, jest reprezentowana przez dane [telemetryczne zależności](../../azure-monitor/app/data-model-dependency-telemetry.md). Dane telemetryczne zależności również definiują własne `id`, które są globalnie unikatowe. Dane telemetryczne żądania inicjowane przez to wywołanie zależności używa tego `id` jako `operation_parentId`.

Można utworzyć widok rozproszonej operacji logicznej przy użyciu `operation_Id`, `operation_parentId`i `request.id` z `dependency.id`. Te pola definiują również kolejność wywoływania wywołań telemetrycznych.

W środowisku mikrousług ślady składników mogą przechodzić do różnych elementów magazynu. Każdy składnik może mieć własny klucz Instrumentacji w Application Insights. Aby uzyskać dane telemetryczne dla operacji logicznej, Application Insights wysyła zapytanie do danych z każdego elementu magazynu. Gdy liczba elementów magazynu jest duża, musisz mieć wskazówkę dotyczącą miejsca, w którym będzie wyglądać dalej. Application Insights model danych definiuje dwa pola, aby rozwiązać ten problem: `request.source` i `dependency.target`. Pierwsze pole identyfikuje składnik inicjujący żądanie zależności. Drugie pole określa, który składnik zwrócił odpowiedź wywołania zależności.

## <a name="example"></a>Przykład

Przyjrzyjmy się przykładowi. Aplikacja o nazwie ceny giełdowe pokazuje aktualną cenę rynkową przy użyciu zewnętrznego interfejsu API o nazwie Stock. Aplikacja do cen giełdowych ma stronę o nazwie Strona giełdowa, którą otwiera przeglądarka klienta sieci Web przy użyciu `GET /Home/Stock`. Aplikacja wysyła zapytanie do interfejsu API spisu przy użyciu `GET /api/stock/value`wywołania HTTP.

Dane telemetryczne mogą być analizowane przez uruchomienie zapytania:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Zwróć uwagę, że wszystkie elementy telemetrii współużytkują główny `operation_Id`. Po wykonaniu wywołania AJAX ze strony nowy unikatowy identyfikator (`qJSXU`) jest przypisywany do telemetrii zależności, a identyfikator pageView jest używany jako `operation_ParentId`. Żądanie serwera używa identyfikatora AJAX jako `operation_ParentId`.

| ItemType   | name                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Strona giełdowa                |              | STYz               | STYz         |
| zależność | Pobierz/Home/Stock           | qJSXU        | STYz               | STYz         |
| request    | Pobierz domowy/giełdowy            | KqKwlrSt9PA= | qJSXU              | STYz         |
| zależność | Pobierz/API/Stock/Value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Gdy wywołanie `GET /api/stock/value` jest nawiązywane w usłudze zewnętrznej, należy znać tożsamość tego serwera, aby można było odpowiednio ustawić pole `dependency.target`. Gdy usługa zewnętrzna nie obsługuje monitorowania, `target` jest ustawiona na nazwę hosta usługi (na przykład `stock-prices-api.com`). Jeśli jednak usługa identyfikuje siebie przez zwrócenie wstępnie zdefiniowanego nagłówka HTTP, `target` zawiera tożsamość usługi umożliwiającą Application Insights kompilowania rozproszonego śledzenia przez przeszukiwanie danych telemetrycznych z tej usługi.

## <a name="correlation-headers"></a>Nagłówki korelacji

Application Insights przechodzi do [kontekstu śledzenia W3C](https://w3c.github.io/trace-context/), który definiuje:

- `traceparent`: przenosi globalnie unikatowy identyfikator operacji i unikatowy identyfikator wywołania.
- `tracestate`: przenosi specyficzny dla systemu kontekst śledzenia.

Najnowsza wersja zestawu SDK Application Insights obsługuje protokół śledzenia-kontekstu, ale może być konieczne zachodzenie do niego. (Zgodność z poprzednimi wersjami z powyższym protokołem korelacji obsługiwanym przez zestaw Application Insights SDK zostanie zachowana).

[Protokół http korelacji, nazywany również identyfikatorem żądania](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md), jest przestarzały. Ten protokół definiuje dwa nagłówki:

- `Request-Id`: ma unikatowy identyfikator globalny wywołania.
- `Correlation-Context`: przenosi kolekcję par nazwa-wartość dla właściwości rozproszonego śledzenia.

Application Insights również definiuje [rozszerzenie](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) dla protokołu HTTP korelacji. Używa `Request-Context` par nazwa-wartość do propagowania kolekcji właściwości używanych przez bezpośredni obiekt wywołujący lub wywoływany. Zestaw SDK Application Insights używa tego nagłówka do ustawiania pól `dependency.target` i `request.source`.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Włącz obsługę rozproszonego śledzenia W3C dla klasycznych aplikacji ASP.NET
 
  > [!NOTE]
  >  Począwszy od `Microsoft.ApplicationInsights.Web` i `Microsoft.ApplicationInsights.DependencyCollector`, nie jest wymagana żadna konfiguracja.

Obsługa funkcji śledzenia W3C jest zaimplementowana w sposób zgodny z poprzednimi wersjami. Oczekuje się, że korelacja będzie współdziałać z aplikacjami, które są Instrumentacją z poprzednimi wersjami zestawu SDK (bez pomocy technicznej W3C).

Aby nadal korzystać z starszego protokołu `Request-Id`, można wyłączyć kontekst śledzenia przy użyciu tej konfiguracji:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Jeśli uruchamiasz starszą wersję zestawu SDK, zalecamy jej aktualizację lub zastosowanie poniższej konfiguracji w celu włączenia kontekstu śledzenia.
Ta funkcja jest dostępna w pakietach `Microsoft.ApplicationInsights.Web` i `Microsoft.ApplicationInsights.DependencyCollector`, począwszy od wersji 2.8.0-beta1.
Jest on domyślnie wyłączony. Aby je włączyć, wprowadź następujące zmiany w `ApplicationInsights.config`:

- W obszarze `RequestTrackingTelemetryModule`Dodaj element `EnableW3CHeadersExtraction` i ustaw jego wartość na `true`.
- W obszarze `DependencyTrackingTelemetryModule`Dodaj element `EnableW3CHeadersInjection` i ustaw jego wartość na `true`.
- Dodaj `W3COperationCorrelationTelemetryInitializer` w `TelemetryInitializers`. Będzie wyglądać podobnie do tego przykładu:

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Włącz obsługę rozproszonego śledzenia W3C dla aplikacji ASP.NET Core

 > [!NOTE]
  > Począwszy od `Microsoft.ApplicationInsights.AspNetCore` wersja 2.8.0, nie jest wymagana żadna konfiguracja.
 
Obsługa funkcji śledzenia W3C jest zaimplementowana w sposób zgodny z poprzednimi wersjami. Oczekuje się, że korelacja będzie współdziałać z aplikacjami, które są Instrumentacją z poprzednimi wersjami zestawu SDK (bez pomocy technicznej W3C).

Aby nadal korzystać z starszego protokołu `Request-Id`, można wyłączyć kontekst śledzenia przy użyciu tej konfiguracji:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Jeśli uruchamiasz starszą wersję zestawu SDK, zalecamy jej aktualizację lub zastosowanie poniższej konfiguracji w celu włączenia kontekstu śledzenia.

Ta funkcja jest w wersji `Microsoft.ApplicationInsights.AspNetCore` 2.5.0-beta1 i w wersji `Microsoft.ApplicationInsights.DependencyCollector` 2.8.0-beta1.
Jest on domyślnie wyłączony. Aby ją włączyć, ustaw `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` na `true`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Włącz obsługę rozproszonego śledzenia W3C dla aplikacji Java

- **Konfiguracja przychodząca**

  - W przypadku aplikacji Java EE Dodaj następujący kod do tagu `<TelemetryModules>` w ApplicationInsights. XML:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - W przypadku aplikacji do rozruchu sprężynowego Dodaj następujące właściwości:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Konfiguracja wychodząca**

  Dodaj następujący kod do AI-Agent. XML:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > Tryb zgodności z poprzednimi wersjami jest domyślnie włączony, a parametr `enableW3CBackCompat` jest opcjonalny. Użyj go tylko wtedy, gdy chcesz wyłączyć zgodność z poprzednimi wersjami.
  >
  > Najlepiej ją wyłączyć, gdy wszystkie usługi zostały zaktualizowane do nowszych wersji zestawów SDK, które obsługują protokół W3C. Zdecydowanie zalecamy przechodzenie do tych nowszych zestawów SDK najszybciej, jak to możliwe.

> [!IMPORTANT]
> Upewnij się, że konfiguracje przychodzące i wychodzące są dokładnie takie same.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Włącz obsługę rozproszonego śledzenia W3C dla aplikacji sieci Web

Ta funkcja jest dostępna w `Microsoft.ApplicationInsights.JavaScript`. Jest on domyślnie wyłączony. Aby ją włączyć, użyj konfiguracji `distributedTracingMode`. AI_AND_W3C zapewnia zgodność z poprzednimi wersjami ze starszymi usługami przystosowanymi przez Application Insights.

- **Instalator npm (zignoruj, jeśli użyto Instalatora wstawek)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Instalator fragmentu kodu (zignoruj, jeśli jest używany Instalator npm)**

  ```
  <script type="text/javascript">
  var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){i[e]=function(){var n=arguments;i.queue.push(function(){i[e].apply(i,n)})}}var i={config:e};i.initialize=!0;var a=document,t=window;setTimeout(function(){var n=a.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",a.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{i.cookie=a.cookie}catch(e){}i.queue=[],i.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var o="Track"+r[0];if(n("start"+o),n("stop"+o),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var s=t[r];t[r]=function(e,n,a,t,o){var c=s&&s(e,n,a,t,o);return!0!==c&&i["_"+r]({message:e,url:n,lineNumber:a,columnNumber:t,error:o}),c},e.autoExceptionInstrumented=!0}return i}
  (
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      distributedTracingMode: 2 // DistributedTracingModes.W3C
      /* ...other configuration options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="opentracing-and-application-insights"></a>OpenTracing i Application Insights

[Specyfikacja modelu danych OpenTracing](https://opentracing.io/) i Application Insights modele danych są mapowane w następujący sposób:

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` z `span.kind = server`                  |
| `Dependency`                          | `Span` z `span.kind = client`                  |
| `Id` `Request` i `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` typu `ChildOf` (zakres nadrzędny)   |

Aby uzyskać więcej informacji, zobacz [Application Insights model danych telemetrii](../../azure-monitor/app/data-model.md).

Definicje pojęć OpenTracing można znaleźć w temacie [Specyfikacja](https://github.com/opentracing/specification/blob/master/specification.md) OpenTracing i [konwencje semantyczne](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-opencensus-python"></a>Korelacja telemetrii w języku Python OpenCensus

OpenCensus Python jest zgodna ze specyfikacjami modelu danych `OpenTracing` opisanymi wcześniej. Obsługuje również funkcję [śledzenia W3C — kontekst](https://w3c.github.io/trace-context/) nie wymaga żadnej konfiguracji.

### <a name="incoming-request-correlation"></a>Korelacja żądań przychodzących

OpenCensus Python skorelowanie nagłówków kontekstu śledzenia W3C z żądań przychodzących do zakresów, które są generowane na podstawie samych żądań. OpenCensus automatycznie podejmie integrację dla tych popularnych platform aplikacji sieci Web: kolby, Django i ostrosłupowy. Wystarczy wypełnić nagłówki W3C śledzenia-kontekstu [prawidłowym formatem](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) i wysłać je z żądaniem. Oto przykładowa aplikacja do kolby, która ilustruje:

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

Ten kod uruchamia przykładową aplikację do kolby na komputerze lokalnym, nasłuchiwanie portów `8080`. Aby skorelować kontekst śledzenia, Wyślij żądanie do punktu końcowego. W tym przykładzie można użyć `curl` polecenia:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Przeglądając [Format nagłówka-kontekstu śledzenia](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format), można utworzyć następujące informacje:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Jeśli zobaczysz wpis żądania, który został wysłany do Azure Monitor, możesz zobaczyć pola wypełnione informacjami nagłówka śledzenia. Te dane można znaleźć w obszarze Dzienniki (analiza) w zasobie Azure Monitor Application Insights.

![Zażądaj danych telemetrycznych w dziennikach (analiza)](./media/opencensus-python/0011-correlation.png)

Pole `id` ma format `<trace-id>.<span-id>`, gdzie `trace-id` jest pobierany z nagłówka śledzenia, który został przesłany w żądaniu, a `span-id` jest wygenerowaną 8-bajtową tablicą dla tego zakresu.

Pole `operation_ParentId` ma format `<trace-id>.<parent-id>`, gdzie `trace-id` i `parent-id` są pobierane z nagłówka śledzenia, który został przesłany w żądaniu.

### <a name="log-correlation"></a>Korelacja dziennika

OpenCensus Python umożliwia skorelowanie dzienników przez dodanie identyfikatora śledzenia, identyfikatora zakresu i flagi próbkowania w celu rejestrowania rekordów. Te atrybuty są dodawane przez zainstalowanie [integracji rejestrowania](https://pypi.org/project/opencensus-ext-logging/)OpenCensus. Następujące atrybuty zostaną dodane do obiektów `LogRecord` języka Python: `traceId`, `spanId`i `traceSampled`. Należy zauważyć, że ta wartość obowiązuje tylko w przypadku rejestratorów utworzonych po integracji.

Oto przykładowa aplikacja, która ilustruje następujące:

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
Po uruchomieniu tego kodu następujące elementy są drukowane w konsoli programu:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Zwróć uwagę, że istnieje `spanId` dla komunikatu dziennika, który znajduje się w ramach zakresu. Jest to ten sam `spanId`, który należy do zakresu o nazwie `hello`.

Dane dziennika można wyeksportować za pomocą `AzureLogHandler`. Więcej informacji znajduje się w [tym artykule](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#logs).

## <a name="telemetry-correlation-in-net"></a>Korelacja telemetrii w programie .NET

W miarę upływu czasu platforma .NET określiła kilka sposobów skorelowania dzienników telemetrii i diagnostyki:

- `System.Diagnostics.CorrelationManager` umożliwia śledzenie [LogicalOperationStack i ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx).
- `System.Diagnostics.Tracing.EventSource` i śledzenie zdarzeń dla systemu Windows (ETW) definiują metodę [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) .
- `ILogger` używa [zakresów dzienników](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes).
- Windows Communication Foundation (WCF) i HTTP — "bieżące" Propagacja kontekstu.

Jednak te metody nie umożliwiały automatycznej obsługi śledzenia rozproszonego. `DiagnosticSource` obsługuje automatyczną korelację między maszynami. Biblioteki .NET obsługują `DiagnosticSource` i zezwalają na automatyczne propagowanie między maszynami kontekstu korelacji za pośrednictwem transportu, takiego jak HTTP.

[Przewodnik użytkownika działania](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) w `DiagnosticSource` objaśnia podstawy śledzenia działań.

ASP.NET Core 2,0 obsługuje wyodrębnianie nagłówków HTTP i uruchamianie nowych działań.

`System.Net.Http.HttpClient`, począwszy od wersji 4.1.0, obsługuje automatyczne iniekcje nagłówków HTTP korelacji i śledzenie wywołań HTTP jako działania.

Istnieje nowy moduł HTTP, [Microsoft. ASPNET. TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), dla klasycznego ASP.NET. Ten moduł implementuje korelację telemetrii za pomocą `DiagnosticSource`. Uruchamia działanie na podstawie przychodzących nagłówków żądań. Wiąże się to również z danymi telemetrycznymi z różnych etapów przetwarzania żądań, nawet gdy przetwarzanie poszczególnych etapów Internet Information Services (IIS) przebiega w innym zarządzanym wątku.

Zestaw Application Insights SDK, zaczynając od wersji 2.4.0-beta1, używa `DiagnosticSource` i `Activity` do zbierania danych telemetrycznych i kojarzenia ich z bieżącym działaniem.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Korelacja telemetrii w zestawie Java SDK

[Zestaw Application Insights SDK dla języka Java w](../../azure-monitor/app/java-get-started.md) wersji 2.0.0 lub nowszej obsługuje automatyczną korelację danych telemetrycznych. Automatycznie wypełnia `operation_id` wszystkich danych telemetrycznych (takich jak ślady, wyjątki i zdarzenia niestandardowe), które zostały wystawione w zakresie żądania. Propaguje także nagłówki korelacji (opisane wcześniej) dla wywołań między usługami za pośrednictwem protokołu HTTP, jeśli skonfigurowano [agenta zestawu Java SDK](../../azure-monitor/app/java-agent.md) .

> [!NOTE]
> Tylko wywołania realizowane za pośrednictwem platformy Apache HttpClient są obsługiwane dla funkcji korelacji. Zarówno sprężyny RestTemplate, jak i Feign mogą być używane z platformą Apache HttpClient pod okapem.

Obecnie automatyczne propagowanie kontekstu w technologiach obsługi komunikatów (takich jak Kafka, RabbitMQ i Azure Service Bus) nie jest obsługiwane. Takie scenariusze można zakodować ręcznie przy użyciu metod `trackDependency` i `trackRequest`. W tych metodach Telemetria zależności reprezentuje komunikat w kolejce przez producenta. Żądanie reprezentuje komunikat przetwarzany przez konsumenta. W takim przypadku zarówno `operation_id`, jak i `operation_parentId` powinny być propagowane we właściwościach komunikatu.

### <a name="telemetry-correlation-in-asynchronous-java-applications"></a>Korelacja telemetrii w asynchronicznych aplikacjach Java

Aby dowiedzieć się, jak skorelować dane telemetryczne w asynchronicznej aplikacji rozruchowej, zobacz [śledzenie rozproszone w aplikacjach asynchronicznych języka Java](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications). Ten artykuł zawiera wskazówki dotyczące instrumentowania [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) i [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html)sprężyny.


<a name="java-role-name"></a>
## <a name="role-name"></a>Nazwa roli

Można dostosować sposób wyświetlania nazw składników na [mapie aplikacji](../../azure-monitor/app/app-map.md). W tym celu można ręcznie ustawić `cloud_RoleName`, wykonując jedną z następujących czynności:

- Za pomocą Application Insights Java SDK 2.5.0 i nowszych można określić `cloud_RoleName` przez dodanie `<RoleName>` do pliku ApplicationInsights. XML:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- W przypadku korzystania z sieci sprężynowej przy użyciu Application Insights sprężynowego startu, wystarczy ustawić niestandardową nazwę aplikacji w pliku Application. Properties:

  `spring.application.name=<name-of-app>`

  Naruch sprężynowy Starter automatycznie przypisuje `cloudRoleName` do wartości wprowadzonej dla właściwości `spring.application.name`.

## <a name="next-steps"></a>Następne kroki

- Napisz [niestandardową telemetrię](../../azure-monitor/app/api-custom-events-metrics.md).
- Aby uzyskać zaawansowane scenariusze korelacji w ASP.NET Core i ASP.NET, zobacz [śledzenie operacji niestandardowych](custom-operations-tracking.md).
- Dowiedz się więcej o [ustawianiu cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) dla innych zestawów SDK.
- Dołączanie wszystkich składników mikrousługi na Application Insights. Zapoznaj się z [obsługiwanymi platformami](../../azure-monitor/app/platforms.md).
- Zobacz [model danych](../../azure-monitor/app/data-model.md) dla typów Application Insights.
- Dowiedz się [, jak rozciągnąć i filtrować dane telemetryczne](../../azure-monitor/app/api-filtering-sampling.md).
- Przejrzyj [informacje dotyczące konfiguracji Application Insights](configuration-with-applicationinsights-config.md).
