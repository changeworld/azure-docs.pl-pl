---
title: Korelacja telemetrii Application Insights platformy Azure | Microsoft Docs
description: Application Insights korelacji telemetrii
services: application-insights
documentationcenter: .net
author: lgayhardt
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.author: lagayhar
ms.openlocfilehash: bb28171ceca9861fb5cc0b7be1db9ab58ef72a1b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70124117"
---
# <a name="telemetry-correlation-in-application-insights"></a>Korelacja telemetrii w Application Insights

Na świecie mikrousług każda operacja logiczna wymaga wykonania pracy w różnych składnikach usługi. Każdy z tych składników może być monitorowany oddzielnie za pomocą [usługi Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Składnik aplikacji sieci Web komunikuje się ze składnikiem dostawcy uwierzytelniania w celu zweryfikowania poświadczeń użytkownika i składnika interfejsu API w celu pobrania danych do wizualizacji. Składnik API może wysyłać zapytania dotyczące danych z innych usług i używać składników dostawcy pamięci podręcznej do powiadamiania składnika rozliczania o tym wywołaniu. Application Insights obsługuje korelację rozproszonej telemetrii, za pomocą której można wykryć, który składnik jest odpowiedzialny za błędy lub spadek wydajności.

W tym artykule opisano model danych używany przez Application Insights do skorelowania telemetrii wysyłanej przez wiele składników. Obejmuje to techniki i protokoły propagacji kontekstowej. Obejmuje to również implementację koncepcji korelacji w różnych językach i platformach.

## <a name="data-model-for-telemetry-correlation"></a>Model danych korelacji telemetrii

Application Insights definiuje [model danych](../../azure-monitor/app/data-model.md) dla korelacji rozproszonej telemetrii. Aby skojarzyć dane telemetryczne z operacją logiczną, każdy element telemetrii `operation_Id`ma pole kontekstu o nazwie. Ten identyfikator jest współużytkowany przez każdy element telemetrii rozproszonego śledzenia. W związku z tym nawet z utratą danych telemetrycznych z pojedynczej warstwy można nadal kojarzyć dane telemetryczne zgłoszone przez inne składniki.

Rozproszone operacje logiczne zwykle składają się z zestawu mniejszych operacji, które są żądaniami przetworzonymi przez jeden ze składników. Te operacje są definiowane przez dane telemetryczne [żądania](../../azure-monitor/app/data-model-request-telemetry.md). Wszystkie dane telemetryczne żądania `id` są własne, które jednoznacznie identyfikują i globalnie. Wszystkie elementy telemetrii (takie jak ślady i wyjątki), które są skojarzone z tym żądaniem `operation_parentId` , powinny ustawiać wartość żądania. `id`

Każda operacja wychodząca, taka jak wywołanie HTTP w innym składniku, jest reprezentowana przez dane telemetryczne [zależności](../../azure-monitor/app/data-model-dependency-telemetry.md). Dane telemetryczne zależności również `id` definiują własną globalnie unikatową. Dane telemetryczne żądania inicjowane przez to wywołanie zależności `id` używa tego `operation_parentId`elementu jako jego elementu.

Możesz utworzyć `operation_Id`widok rozproszonej operacji logicznej przy użyciu, `operation_parentId`, i `request.id` z `dependency.id`. Te pola definiują również kolejność wywoływania wywołań telemetrycznych.

W środowisku mikrousług ślady składników mogą przechodzić do różnych elementów magazynu. Każdy składnik może mieć własny klucz Instrumentacji w Application Insights. Aby uzyskać dane telemetryczne dla operacji logicznej, Application Insights UX wykonuje zapytania dotyczące danych z każdego elementu magazynu. Gdy liczba elementów magazynu jest duża, należy zapoznać się z wskazówką dotyczącą lokalizacji następnej. Application Insights model danych definiuje dwa pola, aby rozwiązać ten problem: `request.source` i `dependency.target`. Pierwsze pole identyfikuje składnik inicjujący żądanie zależności, a drugi identyfikuje składnik, który zwrócił odpowiedź wywołania zależności.

## <a name="example"></a>Przykład

Przyjrzyjmy się przykładowi do aplikacji o nazwie ceny giełdowe, która przedstawia bieżącą cenę rynkową przy użyciu zewnętrznego interfejsu API o nazwie `Stock`. Aplikacja do cen giełdowych ma stronę o `Stock page` nazwie otwierającej przeglądarkę sieci Web klienta przy `GET /Home/Stock`użyciu programu. Aplikacja wysyła zapytanie do `Stock` interfejsu API za pomocą wywołania `GET /api/stock/value`http.

Dane telemetryczne mogą być analizowane przez uruchomienie zapytania:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Zwróć uwagę, że wszystkie elementy telemetrii korzystają z katalogu `operation_Id`głównego. Po wykonaniu wywołania AJAX ze strony nowy unikatowy identyfikator (`qJSXU`) jest przypisywany do telemetrii zależności, a identyfikator pageView jest używany jako. `operation_ParentId` Żądanie serwera używa identyfikatora AJAX jako `operation_ParentId`.

| itemType   | name                      | id           | operation_ParentId | Parametrów |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Strona giełdowa                |              | STYz               | STYz         |
| zależność | Pobierz/Home/Stock           | qJSXU        | STYz               | STYz         |
| request    | Pobierz domowy/giełdowy            | KqKwlrSt9PA= | qJSXU              | STYz         |
| zależność | Pobierz/API/Stock/Value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Gdy wywołanie `GET /api/stock/value` jest nawiązywane w usłudze zewnętrznej, chcesz znać tożsamość tego serwera, aby można było odpowiednio `dependency.target` ustawić pole. Gdy usługa zewnętrzna nie obsługuje monitorowania, `target` jest ustawiona na nazwę hosta usługi (na `stock-prices-api.com`przykład). Jeśli jednak usługa identyfikuje siebie przez zwrócenie wstępnie zdefiniowanego nagłówka HTTP, `target` zawiera tożsamość usługi umożliwiającą Application Insights kompilowania rozproszonego śledzenia przez przeszukiwanie danych telemetrycznych z tej usługi.

## <a name="correlation-headers"></a>Nagłówki korelacji

Pracujemy nad propozycją RFC dotyczącą [protokołu HTTP korelacji](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Ta propozycja definiuje dwa nagłówki:

- `Request-Id`: Przenosi unikatowy identyfikator globalny wywołania.
- `Correlation-Context`: Przenosi kolekcję par nazwa-wartość dla właściwości rozproszonego śledzenia.

Standard definiuje również dwa schematy dla `Request-Id` generacji: Flat i hierarchiczne. Ze schematem płaskim jest zdefiniowany dobrze znany `Id` klucz `Correlation-Context` dla kolekcji.

Application Insights definiuje [rozszerzenie](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) protokołu HTTP korelacji. Używa `Request-Context` par nazwa-wartość do propagowania kolekcji właściwości używanych przez bezpośredni obiekt wywołujący lub wywoływany. Zestaw SDK Application Insights używa tego nagłówka do ustawiania `dependency.target` i `request.source` pól.

### <a name="w3c-distributed-tracing"></a>Śledzenie rozproszone W3C

Przechodzimy do [formatu rozproszonego śledzenia W3C](https://w3c.github.io/trace-context/). Definiuje:

- `traceparent`: Przenosi unikatowy identyfikator operacji globalnie i unikatowy identyfikator wywołania.
- `tracestate`: Wykonuje śledzenie kontekstu specyficznego dla systemu.

#### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Włącz obsługę rozproszonego śledzenia W3C dla klasycznych aplikacji ASP.NET

Ta funkcja jest dostępna w `Microsoft.ApplicationInsights.Web` systemach `Microsoft.ApplicationInsights.DependencyCollector` i, począwszy od wersji 2.8.0-beta1.
Jest on domyślnie wyłączony. Aby ją włączyć, Zmień `ApplicationInsights.config`:

- W `RequestTrackingTelemetryModule`obszarze `EnableW3CHeadersExtraction` Dodaj element z wartością ustawioną na `true`.
- W `DependencyTrackingTelemetryModule`obszarze `EnableW3CHeadersInjection` Dodaj element z wartością ustawioną na `true`.
- `W3COperationCorrelationTelemetryInitializer` Dodaj`TelemetryInitializers` w obszarze podobne do 

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers> 
```

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Włącz obsługę rozproszonego śledzenia W3C dla aplikacji ASP.NET Core

Ta funkcja jest w `Microsoft.ApplicationInsights.AspNetCore` wersji 2.5.0-beta1 i w `Microsoft.ApplicationInsights.DependencyCollector` wersji 2.8.0-beta1.
Jest on domyślnie wyłączony. Aby je włączyć, ustaw `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` opcję `true`na:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

#### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Włącz obsługę rozproszonego śledzenia W3C dla aplikacji Java

- **Konfiguracja przychodząca**

  - W przypadku aplikacji Java EE Dodaj następujący kod do `<TelemetryModules>` znacznika wewnątrz ApplicationInsights. XML:

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
  > Tryb zgodności z poprzednimi wersjami jest domyślnie włączony `enableW3CBackCompat` , a parametr jest opcjonalny. Użyj go tylko wtedy, gdy chcesz wyłączyć zgodność z poprzednimi wersjami.
  >
  > Najlepiej ją wyłączyć, gdy wszystkie usługi zostały zaktualizowane do nowszych wersji zestawów SDK, które obsługują protokół W3C. Zdecydowanie zalecamy przechodzenie do tych nowszych zestawów SDK najszybciej, jak to możliwe.

> [!IMPORTANT]
> Upewnij się, że konfiguracje przychodzące i wychodzące są dokładnie takie same.

#### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Włącz obsługę rozproszonego śledzenia W3C dla aplikacji sieci Web

Ta funkcja jest dostępna `Microsoft.ApplicationInsights.JavaScript`w programie. Jest on domyślnie wyłączony. Aby ją włączyć, użyj `distributedTracingMode` konfiguracji. AI_AND_W3C zapewnia zgodność z poprzednimi wersjami z dowolnymi starszymi usługami Application Insights Instrumentacji:

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
| `Request`, `PageView`                 | `Span`się`span.kind = server`                  |
| `Dependency`                          | `Span`się`span.kind = client`                  |
| `Id`z `Request` i`Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference`typu `ChildOf` (zakres nadrzędny)   |

Aby uzyskać więcej informacji, zobacz [Application Insights model danych](../../azure-monitor/app/data-model.md)telemetrii. 

Definicje koncepcji OpenTracing można znaleźć w temacie OpenTracing [Specification](https://github.com/opentracing/specification/blob/master/specification.md) i [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-net"></a>Korelacja telemetrii w programie .NET

W miarę upływu czasu platforma .NET określiła kilka sposobów skorelowania dzienników telemetrii i diagnostyki:

- `System.Diagnostics.CorrelationManager`umożliwia śledzenie elementów [LogicalOperationStack i ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). 
- `System.Diagnostics.Tracing.EventSource`i śledzenie zdarzeń systemu Windows (ETW) definiują metodę [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) .
- `ILogger`używa [zakresów dzienników](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). 
- Windows Communication Foundation (WCF) i HTTP — "bieżące" Propagacja kontekstu.

Jednak te metody nie umożliwiały automatycznej obsługi śledzenia rozproszonego. `DiagnosticSource`jest sposobem obsługi automatycznej korelacji między maszynami. Biblioteki .NET obsługują funkcję "DiagnosticSource" i umożliwiają automatyczne propagowanie międzymaszynowego kontekstu korelacji przy użyciu transportu, takiego jak HTTP.

[Przewodnik dotyczący działań](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) w `DiagnosticSource` programie objaśnia podstawy śledzenia działań.

ASP.NET Core 2,0 obsługuje wyodrębnianie nagłówków HTTP i uruchamianie nowego działania.

`System.Net.HttpClient`począwszy od wersji 4.1.0, obsługuje automatyczne wstrzyknięcie nagłówków HTTP korelacji i śledzenie wywołania HTTP jako działania.

Istnieje nowy moduł HTTP, [Microsoft. ASPNET. TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), dla klasycznego ASP.NET. Ten moduł implementuje korelację telemetrii za pomocą `DiagnosticSource`. Uruchamia działanie na podstawie przychodzących nagłówków żądań. Wiąże się ona również z różnymi etapami przetwarzania żądań, nawet w przypadku, gdy każdy etap przetwarzania Internet Information Services (IIS) jest uruchamiany w innym zarządzanym wątku.

Zestaw Application Insights SDK, zaczynając od wersji 2.4.0-beta1, używa `DiagnosticSource` i `Activity` do zbierania danych telemetrycznych i kojarzenia go z bieżącym działaniem.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Korelacja telemetrii w zestawie Java SDK

[Zestaw Application Insights SDK dla języka Java](../../azure-monitor/app/java-get-started.md) obsługuje automatyczną korelację danych telemetrycznych rozpoczynającą się od wersji 2.0.0. Automatycznie wypełnia `operation_id` wszystkie dane telemetryczne (takie jak ślady, wyjątki i zdarzenia niestandardowe), które zostały wystawione w zakresie żądania. Należy również zwrócić uwagę na propagowanie nagłówków korelacji (opisanych wcześniej) dla wywołań między usługami za pośrednictwem protokołu HTTP, jeśli skonfigurowano [agenta zestawu Java SDK](../../azure-monitor/app/java-agent.md) .

> [!NOTE]
> Tylko wywołania realizowane za pośrednictwem platformy Apache HTTPClient są obsługiwane dla funkcji korelacji. Jeśli używasz sprężyny RestTemplate lub Feign, oba te elementy mogą być używane z platformą Apache HTTPClient pod wyciągiem.

Obecnie automatyczne propagowanie kontekstu w technologiach obsługi komunikatów (takich jak Kafka, RabbitMQ lub Azure Service Bus) nie jest obsługiwane. Można jednak ręcznie zakodować takie scenariusze przy użyciu `trackDependency` interfejsów API i. `trackRequest` W tych interfejsów API, telemetria zależności reprezentuje komunikat w kolejce przez producenta, a żądanie reprezentuje komunikat przetwarzany przez konsumenta. W tym przypadku obie `operation_id` i `operation_parentId` powinny być propagowane we właściwościach komunikatu.

### <a name="telemetry-correlation-in-asynchronous-java-application"></a>Korelacja telemetrii w asynchronicznej aplikacji Java

Aby skorelować dane telemetryczne w aplikacji do rozruchu asynchronicznego sprężyny, wykonaj [ten](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications) szczegółowy artykuł. Zapewnia wskazówki dotyczące Instrumentacji [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) oraz [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html). 


<a name="java-role-name"></a>
## <a name="role-name"></a>Nazwa roli

Czasami można dostosować sposób wyświetlania nazw składników na [mapie aplikacji](../../azure-monitor/app/app-map.md). Aby to zrobić, można ręcznie ustawić `cloud_RoleName` , wykonując jedną z następujących czynności:

- Jeśli używasz sprężynowego rozruchu z Application Insights sprężynowego rozruchu Starter, jedyną wymaganą zmianą jest ustawienie niestandardowej nazwy aplikacji w pliku Application. Properties.

  `spring.application.name=<name-of-app>`

  Rozruch sprężynowy startowy `cloudRoleName` jest automatycznie przypisywany do wartości wprowadzonej `spring.application.name` dla właściwości.

- Jeśli używasz programu `WebAppNameContextInitializer` ,automatycznieustawianazwęaplikacji.`WebRequestTrackingFilter` Dodaj następujący kod do pliku konfiguracji (ApplicationInsights. xml):

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

- Napisz [](../../azure-monitor/app/api-custom-events-metrics.md)niestandardową telemetrię.
- Aby uzyskać zaawansowane scenariusze korelacji w ASP.NET Core i ASP.NET, zapoznaj się z artykułem [śledzenie niestandardowych operacji](custom-operations-tracking.md) .
- Dowiedz się więcej o [ustawianiu cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) dla innych zestawów SDK.
- Dołączanie wszystkich składników mikrousługi na Application Insights. Zapoznaj się [](../../azure-monitor/app/platforms.md)z obsługiwanymi platformami.
- Zobacz [model danych](../../azure-monitor/app/data-model.md) dla typów Application Insights.
- Dowiedz się [, jak rozciągnąć i filtrować](../../azure-monitor/app/api-filtering-sampling.md)dane telemetryczne.
- Przejrzyj [informacje dotyczące konfiguracji Application Insights](configuration-with-applicationinsights-config.md).
