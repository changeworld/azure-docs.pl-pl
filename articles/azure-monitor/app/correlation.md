---
title: Korelacja telemetrii Application Insights platformy Azure | Microsoft Docs
description: Application Insights korelacji telemetrii
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: df93405940c02affa224fba2d2e6f07ce5278b15
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755375"
---
# <a name="telemetry-correlation-in-application-insights"></a>Korelacja telemetrii w Application Insights

Na świecie mikrousług każda operacja logiczna wymaga wykonania pracy w różnych składnikach usługi. Każdy z tych składników może być monitorowany oddzielnie za pomocą [usługi Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Składnik aplikacji sieci Web komunikuje się ze składnikiem dostawcy uwierzytelniania w celu zweryfikowania poświadczeń użytkownika i składnika interfejsu API w celu pobrania danych do wizualizacji. Składnik API może wysyłać zapytania dotyczące danych z innych usług i używać składników dostawcy pamięci podręcznej do powiadamiania składnika rozliczania o tym wywołaniu. Application Insights obsługuje korelację rozproszonej telemetrii, za pomocą której można wykryć, który składnik jest odpowiedzialny za błędy lub spadek wydajności.

W tym artykule opisano model danych używany przez Application Insights do skorelowania telemetrii wysyłanej przez wiele składników. Obejmuje to techniki i protokoły propagacji kontekstowej. Obejmuje to również implementację koncepcji korelacji w różnych językach i platformach.

## <a name="data-model-for-telemetry-correlation"></a>Model danych korelacji telemetrii

Application Insights definiuje [model danych](../../azure-monitor/app/data-model.md) dla korelacji rozproszonej telemetrii. Aby skojarzyć dane telemetryczne z operacją logiczną, każdy element telemetrii ma pole kontekstu o nazwie `operation_Id`. Ten identyfikator jest współużytkowany przez każdy element telemetrii rozproszonego śledzenia. W związku z tym nawet z utratą danych telemetrycznych z pojedynczej warstwy można nadal kojarzyć dane telemetryczne zgłoszone przez inne składniki.

Rozproszone operacje logiczne zwykle składają się z zestawu mniejszych operacji, które są żądaniami przetworzonymi przez jeden ze składników. Te operacje są definiowane przez dane [telemetryczne żądania](../../azure-monitor/app/data-model-request-telemetry.md). Wszystkie dane telemetryczne żądania mają własne `id`, które jednoznacznie identyfikują i globalnie. Wszystkie elementy telemetrii (takie jak ślady i wyjątki), które są skojarzone z tym żądaniem, powinny ustawić `operation_parentId` na wartość `id` żądania.

Każda operacja wychodząca, taka jak wywołanie HTTP w innym składniku, jest reprezentowana przez dane [telemetryczne zależności](../../azure-monitor/app/data-model-dependency-telemetry.md). Dane telemetryczne zależności również definiują własną `id` globalnie unikatową. Dane telemetryczne żądania inicjowane przez to wywołanie zależności używa tego `id` jako `operation_parentId`.

Można utworzyć widok rozproszonej operacji logicznej przy użyciu `operation_Id`, `operation_parentId` i `request.id` z `dependency.id`. Te pola definiują również kolejność wywoływania wywołań telemetrycznych.

W środowisku mikrousług ślady składników mogą przechodzić do różnych elementów magazynu. Każdy składnik może mieć własny klucz Instrumentacji w Application Insights. Aby uzyskać dane telemetryczne dla operacji logicznej, Application Insights UX wykonuje zapytania dotyczące danych z każdego elementu magazynu. Gdy liczba elementów magazynu jest duża, należy zapoznać się z wskazówką dotyczącą lokalizacji następnej. Application Insights model danych definiuje dwa pola, aby rozwiązać ten problem: `request.source` i `dependency.target`. Pierwsze pole identyfikuje składnik inicjujący żądanie zależności, a drugi identyfikuje składnik, który zwrócił odpowiedź wywołania zależności.

## <a name="example"></a>Przykład

Przyjrzyjmy się przykładowi do aplikacji o nazwie ceny giełdowe, która przedstawia bieżącą cenę rynkową przy użyciu zewnętrznego interfejsu API o nazwie `Stock`. Aplikacja do cen giełdowych ma stronę o nazwie `Stock page`, że przeglądarka sieci Web klienta otwiera się przy użyciu `GET /Home/Stock`. Aplikacja wysyła zapytanie do interfejsu API `Stock` przy użyciu `GET /api/stock/value` wywołania HTTP.

Dane telemetryczne mogą być analizowane przez uruchomienie zapytania:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Zwróć uwagę, że wszystkie elementy telemetrii współużytkują główny `operation_Id`. Po wykonaniu wywołania AJAX ze strony nowy unikatowy identyfikator (`qJSXU`) jest przypisywany do telemetrii zależności, a identyfikator pageView jest używany jako `operation_ParentId`. Żądanie serwera używa identyfikatora AJAX jako `operation_ParentId`.

| ItemType   | name                      | ID           | operation_ParentId | Parametrów |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Strona giełdowa                |              | STYz               | STYz         |
| zależności | Pobierz/Home/Stock           | qJSXU        | STYz               | STYz         |
| Żądając    | Pobierz domowy/giełdowy            | KqKwlrSt9PA = | qJSXU              | STYz         |
| zależności | Pobierz/API/Stock/Value      | bBrf2L7mm2g = | KqKwlrSt9PA =       | STYz         |

Gdy wywołanie `GET /api/stock/value` jest nawiązywane w usłudze zewnętrznej, chcesz znać tożsamość tego serwera, aby można było odpowiednio ustawić pole `dependency.target`. Gdy usługa zewnętrzna nie obsługuje monitorowania, `target` jest ustawiona na nazwę hosta usługi (na przykład `stock-prices-api.com`). Jeśli jednak usługa identyfikuje siebie przez zwrócenie wstępnie zdefiniowanego nagłówka HTTP, `target` zawiera tożsamość usługi umożliwiającą Application Insights kompilowania rozproszonego śledzenia przez przeszukiwanie danych telemetrycznych z tej usługi.

## <a name="correlation-headers"></a>Nagłówki korelacji

Przechodzimy do [kontekstu śledzenia W3C](https://w3c.github.io/trace-context/) , który definiuje:

- `traceparent`: przenosi globalnie unikatowy identyfikator operacji i unikatowy identyfikator wywołania.
- `tracestate`: przenosi kontekst związany z systemem śledzenia.

Najnowsze wersje zestawów SDK Application Insights są obsługiwane przez protokół śledzenia-kontekstu, ale może być konieczne ich zachodzenie do programu (w celu zachowania zgodności z poprzednimi wersjami przy użyciu starego protokołu korelacji obsługiwanego przez zestawy SDK ApplicationInsights).

[Identyfikator żądania protokołu HTTP korelacji](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) jest w ścieżce przestarzałej. Ten protokół definiuje dwa nagłówki:

- `Request-Id`: ma unikatowy identyfikator globalny wywołania.
- `Correlation-Context`: przenosi kolekcję par nazwa-wartość dla właściwości rozproszonego śledzenia.

Application Insights również definiuje [rozszerzenie](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) dla protokołu HTTP korelacji. Używa `Request-Context` par nazwa-wartość do propagowania kolekcji właściwości używanych przez bezpośredni obiekt wywołujący lub wywoływany. Zestaw Application Insights SDK używa tego nagłówka do ustawiania pól `dependency.target` i `request.source`.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Włącz obsługę rozproszonego śledzenia W3C dla klasycznych aplikacji ASP.NET
 
  > [!NOTE]
  > Nie jest wymagana żadna konfiguracja rozpoczynająca się od `Microsoft.ApplicationInsights.Web` i `Microsoft.ApplicationInsights.DependencyCollector` 

Obsługa funkcji śledzenia W3C jest wykonywana w sposób zgodny ze starszymi wersjami, a korelacja powinna współpracować z aplikacjami, które mają instrumentację z poprzednimi wersjami zestawu SDK (bez pomocy technicznej W3C). 

Jeśli z jakiegoś powodu chcesz zachować użycie starszego protokołu `Request-Id`, możesz *wyłączyć* kontekst śledzenia z następującą konfiguracją

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

W przypadku uruchomienia starszej wersji zestawu SDK Zalecamy zaktualizowanie go lub zastosowanie poniższej konfiguracji w celu włączenia kontekstu śledzenia.
Ta funkcja jest dostępna w `Microsoft.ApplicationInsights.Web` i `Microsoft.ApplicationInsights.DependencyCollector` pakietów, począwszy od wersji 2.8.0-beta1.
Jest on domyślnie wyłączony. Aby ją włączyć, Zmień `ApplicationInsights.config`:

- W obszarze `RequestTrackingTelemetryModule` Dodaj element `EnableW3CHeadersExtraction` z wartością ustawioną na `true`.
- W obszarze `DependencyTrackingTelemetryModule` Dodaj element `EnableW3CHeadersInjection` z wartością ustawioną na `true`.
- Dodaj `W3COperationCorrelationTelemetryInitializer` w `TelemetryInitializers` podobne do 

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers> 
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Włącz obsługę rozproszonego śledzenia W3C dla aplikacji ASP.NET Core

 > [!NOTE]
  > Nie jest wymagana żadna konfiguracja rozpoczynająca się od `Microsoft.ApplicationInsights.AspNetCore` wersja 2.8.0.
 
Obsługa funkcji śledzenia W3C jest wykonywana w sposób zgodny ze starszymi wersjami, a korelacja powinna współpracować z aplikacjami, które mają instrumentację z poprzednimi wersjami zestawu SDK (bez pomocy technicznej W3C). 

Jeśli z jakiegoś powodu chcesz zachować użycie starszego protokołu `Request-Id`, możesz *wyłączyć* kontekst śledzenia z następującą konfiguracją

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

W przypadku uruchomienia starszej wersji zestawu SDK Zalecamy zaktualizowanie go lub zastosowanie poniższej konfiguracji w celu włączenia kontekstu śledzenia.

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

  - W przypadku aplikacji Java EE Dodaj następujący kod do tagu `<TelemetryModules>` wewnątrz ApplicationInsights. XML:

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

Ta funkcja jest dostępna w `Microsoft.ApplicationInsights.JavaScript`. Jest on domyślnie wyłączony. Aby ją włączyć, użyj konfiguracji `distributedTracingMode`. AI_AND_W3C zapewnia zgodność z poprzednimi wersjami z dowolnymi starszymi usługami Application Insights Instrumentacji:

- **Instalator NPM (zignoruj, jeśli użyto Instalatora wstawek)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...Other Configuration Options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Instalator fragmentu kodu (zignoruj, jeśli jest używany Instalator NPM)**

  ```
  <script type="text/javascript">
  var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){i[e]=function(){var n=arguments;i.queue.push(function(){i[e].apply(i,n)})}}var i={config:e};i.initialize=!0;var a=document,t=window;setTimeout(function(){var n=a.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",a.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{i.cookie=a.cookie}catch(e){}i.queue=[],i.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var o="Track"+r[0];if(n("start"+o),n("stop"+o),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var s=t[r];t[r]=function(e,n,a,t,o){var c=s&&s(e,n,a,t,o);return!0!==c&&i["_"+r]({message:e,url:n,lineNumber:a,columnNumber:t,error:o}),c},e.autoExceptionInstrumented=!0}return i}
  (
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      distributedTracingMode: 2 // DistributedTracingModes.W3C
      /* ...Other Configuration Options... */
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

Definicje koncepcji OpenTracing można znaleźć w temacie OpenTracing [Specification](https://github.com/opentracing/specification/blob/master/specification.md) i [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-opencensus-python"></a>Korelacja telemetrii w języku Python OpenCensus

OpenCensus Python jest zgodna z specyfikacjami modelu danych `OpenTracing` opisanymi powyżej. Obsługuje ona również [kontekst śledzenia W3C](https://w3c.github.io/trace-context/) , bez konieczności konfigurowania.

### <a name="incoming-request-correlation"></a>Korelacja żądań przychodzących

OpenCensus Python skorelowanie nagłówków kontekstu śledzenia W3C z żądań przychodzących do zakresów, które są generowane na podstawie samych żądań. Program OpenCensus automatycznie podejmie integrację dla popularnych struktur aplikacji sieci Web, takich jak `flask`, `django` i `pyramid`. Nagłówki kontekstowe śledzenia W3C muszą być wypełniane przy użyciu [poprawnego formatu](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)i wysyłane z żądaniem. Poniżej znajduje się przykład `flask` aplikacji.

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

Spowoduje to uruchomienie przykładowej aplikacji `flask` na komputerze lokalnym, nasłuchiwanie `8080` portów. Aby skorelować kontekst śledzenia, wysyłamy żądanie do punktu końcowego. W tym przykładzie możemy użyć `curl` polecenia.
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Przeglądając [Format nagłówka kontekstu śledzenia](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format), firma Microsoft udostępnia następujące informacje: `version`: `00` 
 `trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736` 
 `parent-id/span-id`: `00f067aa0ba902b7` 
 0: 1

Jeśli Przyjrzyjmy się wpisowi żądania, który został wysłany do Azure Monitor, można zobaczyć pola wypełnione informacjami nagłówka śledzenia.

![Zrzut ekranu przedstawiający dane telemetryczne żądania w dziennikach (analiza) z polami nagłówków śledzenia wyróżnionych czerwonym prostokątem](./media/opencensus-python/0011-correlation.png)

Pole `id` ma format `<trace-id>.<span-id>`, gdzie `trace-id` jest pobierany z nagłówka śledzenia, który został przesłany w żądaniu, a `span-id` jest wygenerowaną 8-bajtową tablicą dla tego zakresu. 

Pole `operation_ParentId` ma format `<trace-id>.<parent-id>`, gdzie `trace-id` i `parent-id` są pobierane z nagłówka śledzenia, który został przesłany w żądaniu.

### <a name="logs-correlation"></a>Korelacja dzienników

OpenCensus Python umożliwia korelację dzienników poprzez wzbogacanie rekordów dziennika o identyfikator śledzenia, identyfikator zakresu i flagę próbkowania. W tym celu należy zainstalować [integrację rejestrowania](https://pypi.org/project/opencensus-ext-logging/)OpenCensus. Następujące atrybuty zostaną dodane do `LogRecord`s Python: `traceId`, `spanId` i `traceSampled`. Należy pamiętać, że ta wartość obowiązuje tylko w przypadku rejestratorów utworzonych po integracji.
Poniżej znajduje się przykładowa aplikacja pokazująca to.

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
Po uruchomieniu tego kodu w konsoli zostaną wyświetlone następujące elementy:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Zwróć uwagę na to, w jaki sposób spanId jest obecny dla komunikatu dziennika znajdującego się w obrębie zakresu, który jest tym samym spanId, który należy do zakresu o nazwie `hello`.

## <a name="telemetry-correlation-in-net"></a>Korelacja telemetrii w programie .NET

W miarę upływu czasu platforma .NET określiła kilka sposobów skorelowania dzienników telemetrii i diagnostyki:

- `System.Diagnostics.CorrelationManager` umożliwia śledzenie [LogicalOperationStack i ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). 
- `System.Diagnostics.Tracing.EventSource` i śledzenie zdarzeń dla systemu Windows (ETW) definiują metodę [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) .
- `ILogger` używa [zakresów dzienników](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). 
- Windows Communication Foundation (WCF) i HTTP — "bieżące" Propagacja kontekstu.

Jednak te metody nie umożliwiały automatycznej obsługi śledzenia rozproszonego. `DiagnosticSource` jest sposobem obsługi automatycznej korelacji między maszynami. Biblioteki .NET obsługują funkcję "DiagnosticSource" i umożliwiają automatyczne propagowanie międzymaszynowego kontekstu korelacji przy użyciu transportu, takiego jak HTTP.

[Przewodnik dotyczący działań](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) w `DiagnosticSource` objaśnia podstawy śledzenia działań.

ASP.NET Core 2,0 obsługuje wyodrębnianie nagłówków HTTP i uruchamianie nowego działania.

`System.Net.Http.HttpClient`, począwszy od wersji 4.1.0, obsługuje automatyczne wstrzyknięcie nagłówków HTTP korelacji i śledzenie wywołania HTTP jako działania.

Istnieje nowy moduł HTTP, [Microsoft. ASPNET. TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), dla klasycznego ASP.NET. Ten moduł implementuje korelację telemetrii za pomocą `DiagnosticSource`. Uruchamia działanie na podstawie przychodzących nagłówków żądań. Wiąże się ona również z różnymi etapami przetwarzania żądań, nawet w przypadku, gdy każdy etap przetwarzania Internet Information Services (IIS) jest uruchamiany w innym zarządzanym wątku.

Zestaw Application Insights SDK, zaczynając od wersji 2.4.0-beta1, używa `DiagnosticSource` i `Activity` do zbierania danych telemetrycznych i kojarzenia ich z bieżącym działaniem.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Korelacja telemetrii w zestawie Java SDK

[Zestaw Application Insights SDK dla języka Java](../../azure-monitor/app/java-get-started.md) obsługuje automatyczną korelację danych telemetrycznych rozpoczynającą się od wersji 2.0.0. Automatycznie wypełnia `operation_id` dla wszystkich danych telemetrycznych (takich jak ślady, wyjątki i zdarzenia niestandardowe), które zostały wystawione w zakresie żądania. Należy również zwrócić uwagę na propagowanie nagłówków korelacji (opisanych wcześniej) dla wywołań między usługami za pośrednictwem protokołu HTTP, jeśli skonfigurowano [agenta zestawu Java SDK](../../azure-monitor/app/java-agent.md) .

> [!NOTE]
> Tylko wywołania realizowane za pośrednictwem platformy Apache HTTPClient są obsługiwane dla funkcji korelacji. Jeśli używasz sprężyny RestTemplate lub Feign, oba te elementy mogą być używane z platformą Apache HTTPClient pod wyciągiem.

Obecnie automatyczne propagowanie kontekstu w technologiach obsługi komunikatów (takich jak Kafka, RabbitMQ lub Azure Service Bus) nie jest obsługiwane. Można jednak ręcznie zakodować takie scenariusze przy użyciu interfejsów API `trackDependency` i `trackRequest`. W tych interfejsów API, telemetria zależności reprezentuje komunikat w kolejce przez producenta, a żądanie reprezentuje komunikat przetwarzany przez konsumenta. W takim przypadku zarówno `operation_id`, jak i `operation_parentId` powinny być propagowane we właściwościach komunikatu.

### <a name="telemetry-correlation-in-asynchronous-java-application"></a>Korelacja telemetrii w asynchronicznej aplikacji Java

Aby skorelować dane telemetryczne w aplikacji do rozruchu asynchronicznego sprężyny, wykonaj [ten](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications) szczegółowy artykuł. Zapewnia wskazówki dotyczące Instrumentacji [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) oraz [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html). 


<a name="java-role-name"></a>
## <a name="role-name"></a>Nazwa roli

Czasami można dostosować sposób wyświetlania nazw składników na [mapie aplikacji](../../azure-monitor/app/app-map.md). Aby to zrobić, możesz ręcznie ustawić `cloud_RoleName`, wykonując jedną z następujących czynności:

- Jeśli używasz sprężynowego rozruchu z Application Insights sprężynowego rozruchu Starter, jedyną wymaganą zmianą jest ustawienie niestandardowej nazwy aplikacji w pliku Application. Properties.

  `spring.application.name=<name-of-app>`

  Naruch sprężynowy Starter automatycznie przypisuje `cloudRoleName` do wartości wprowadzonej dla właściwości `spring.application.name`.

- Jeśli używasz `WebRequestTrackingFilter`, `WebAppNameContextInitializer` automatycznie ustawi nazwę aplikacji. Dodaj następujący kod do pliku konfiguracji (ApplicationInsights. xml):

  ```XML
  <ContextInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebAppNameContextInitializer" />
  </ContextInitializers>
  ```

- Jeśli używasz klasy kontekstu chmury:

  ```Java
  telemetryClient.getContext().getCloud().setRole("My Component Name");
  ```

## <a name="next-steps"></a>Następne kroki

- Napisz [niestandardową telemetrię](../../azure-monitor/app/api-custom-events-metrics.md).
- Aby uzyskać zaawansowane scenariusze korelacji w ASP.NET Core i ASP.NET, zapoznaj się z artykułem [śledzenie niestandardowych operacji](custom-operations-tracking.md) .
- Dowiedz się więcej o [ustawianiu cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) dla innych zestawów SDK.
- Dołączanie wszystkich składników mikrousługi na Application Insights. Zapoznaj się z [obsługiwanymi platformami](../../azure-monitor/app/platforms.md).
- Zobacz [model danych](../../azure-monitor/app/data-model.md) dla typów Application Insights.
- Dowiedz się [, jak rozciągnąć i filtrować dane telemetryczne](../../azure-monitor/app/api-filtering-sampling.md).
- Przejrzyj [informacje dotyczące konfiguracji Application Insights](configuration-with-applicationinsights-config.md).
