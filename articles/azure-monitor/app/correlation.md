---
title: Korelacja telemetrii w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Korelacja telemetrii usługi Application Insights
services: application-insights
documentationcenter: .net
author: lgayhardt
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/14/2019
ms.reviewer: sergkanz
ms.author: lagayhar
ms.openlocfilehash: cc2d45aee170517d7e41cbda6d92bc21067732d1
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59493641"
---
# <a name="telemetry-correlation-in-application-insights"></a>Korelacja telemetrii w usłudze Application Insights

W świecie mikrousług każdej operacji logicznej wymaga pracy do wykonania w różnych składników usługi. Osobno przez każdego z tych składników można monitorować [usługi Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Składnik aplikacji sieci web komunikuje się za pomocą składnika dostawcy uwierzytelniania na przeprowadzenie walidacji poświadczeń użytkownika, a także ze składnikiem interfejsu API można pobrać danych dla wizualizacji. Składnik interfejsu API można wysyłania zapytań dotyczących danych z innych usług i składników pamięci podręcznej dostawcy jest używany do powiadamiania rozliczeń składnika temat tego wywołania. Korelacja telemetrii rozproszonych obsługuje szczegółowe informacje aplikacji, którego można użyć do wykrywania określają składnik jest odpowiedzialny za niepowodzeń lub zmniejszenie wydajności.

W tym artykule opisano model danych używany przez usługę Application Insights do korelowanie danych telemetrycznych wysyłanych przez wiele składników. Poruszono w nim techniki propagacji kontekstu i protokołów. Obejmuje ona również implementację korelacji pojęcia dotyczące różnych języków i platform.

## <a name="data-model-for-telemetry-correlation"></a>Model danych dla korelacja telemetrii

Definiuje usługę Application Insights [modelu danych](../../azure-monitor/app/data-model.md) dla korelacja telemetrii rozproszonych. Aby skojarzyć dane telemetryczne z operacji logicznej, każdy element danych telemetrycznych ma kontekście pole o nazwie `operation_Id`. Ten identyfikator jest udostępniany każdego elementu telemetrii w rozproszonego śledzenia. Tak nawet w przypadku utraty danych telemetrycznych z jednej warstwy, można nadal skojarzyć dane telemetryczne zgłoszone przez inne składniki.

Rozproszone operacji logicznej zwykle składa się z szeregu mniejszych operacje, które są żądań przetworzonych przez jeden ze składników. Te operacje są definiowane przez [dane telemetryczne dotyczące żądań](../../azure-monitor/app/data-model-request-telemetry.md). Dane telemetryczne co żądania ma swój własny `id` określający jednoznacznie i globalnie. Należy określić wszystkie elementy danych telemetrycznych (na przykład śladów i wyjątków), które są skojarzone z tym żądaniem `operation_parentId` wartości żądania `id`.

Każda operacja wychodzących, takie jak wywołania HTTP do innego składnika jest reprezentowany przez [telemetrii zależności](../../azure-monitor/app/data-model-dependency-telemetry.md). Telemetria zależności usługi definiuje również swój własny `id` jest unikatowa w skali globalnej. Żądanie używa tej wartości dane telemetryczne, które są inicjowane przez to wywołanie zależności `id` jako jego `operation_parentId`.

Widok rozproszonych operacji logicznej można tworzyć przy użyciu `operation_Id`, `operation_parentId`, i `request.id` z `dependency.id`. Te pola określają również kolejność przyczynowości wywołań telemetrii.

W środowisku mikrousług śladów ze składników można przejść do innego magazynu elementów. Każdy składnik może mieć własny klucz Instrumentacji w usłudze Application Insights. Aby uzyskać dane telemetryczne dla operacji logicznej, można zapytania danych z każdego elementu magazynu. Gdy liczba elementów magazynu jest bardzo duży, konieczne będzie wskazówkę o tym, gdzie należy spojrzeć dalej. Model danych usługi Application Insights definiuje dwa pola, aby rozwiązać ten problem: `request.source` i `dependency.target`. Pierwsze pole identyfikuje składnik, który zainicjował żądanie zależności, a druga identyfikuje, które składnik zwrócił odpowiedź na wywołania zależności.

## <a name="example"></a>Przykład

Spójrzmy na przykład aplikacji o nazwie cen akcji, które pokazuje bieżące ceny rynku akcji przy użyciu zewnętrznego interfejsu API o nazwie `Stock`. Aplikacja ceny akcji ma stronę o nazwie `Stock page` otwartym w przeglądarce sieci web klienta przy użyciu `GET /Home/Stock`. Zapytania aplikacji `Stock` interfejsu API przy użyciu wywołania HTTP `GET /api/stock/value`.

Możesz analizować wynikowe dane telemetryczne, uruchamiając zapytanie:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

W wynikach, należy pamiętać, że wszystkie elementy danych telemetrycznych udostępnić katalog główny `operation_Id`. Nawiązaniem wywołania Ajax ze strony, nowy unikatowy identyfikator (`qJSXU`) jest przypisany do telemetrii zależności i używany jako identyfikator pageView `operation_ParentId`. Żądanie serwera następnie używa Identyfikatora Ajax jako `operation_ParentId`.

| itemType   | name                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Podstawowy strony                |              | STYz               | STYz         |
| zależność | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| żądanie    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| zależność | Pobierz /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Podczas wywołania `GET /api/stock/value` składa się z zewnętrzną usługę, chcesz muszą znać tożsamość tego serwera, dzięki czemu można ustawić `dependency.target` odpowiednio pola. Gdy usługi zewnętrznej nie obsługuje monitorowania, `target` jest ustawiona na nazwę hosta usługi (na przykład `stock-prices-api.com`). Jednakże, jeśli usługa identyfikuje, zwracając wstępnie zdefiniowanych nagłówków HTTP, `target` zawiera tożsamości usługi, która umożliwia usługi Application Insights do utworzenia rozproszonego śledzenia, badając dane telemetryczne z tej usługi.

## <a name="correlation-headers"></a>Nagłówki korelacji

Pracujemy nad RFC propozycję [korelacji protokołu HTTP](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Propozycja ta definiuje dwa nagłówki:

- `Request-Id`: Przenosi globalnie unikatowy identyfikator połączenia.
- `Correlation-Context`: Kolekcja par nazwa wartość właściwości rozproszonego śledzenia niesie ze sobą.

Standardowa definiuje również dwie schematów dla `Request-Id` generacji: daje płaski i hierarchicznych. Ze schematem prostego, dobrze znane `Id` zdefiniowano klucza `Correlation-Context` kolekcji.

Definiuje usługę Application Insights [rozszerzenia](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) dla korelacji protokołu HTTP. Używa ona `Request-Context` pary nazwa wartość do propagowania zbiór właściwości, które korzystają z bezpośredniego obiektu wywołującego lub obiekt wywoływany. Zestaw SDK usługi Application Insights używa tego pliku nagłówkowego w celu ustawienia `dependency.target` i `request.source` pola.

### <a name="w3c-distributed-tracing"></a>W3C rozproszonego śledzenia

Firma Microsoft jest przejście do [format rozproszonego śledzenia W3C](https://w3c.github.io/trace-context/). Definiuje on:

- `traceparent`: Niesie ze sobą, globalnie unikatowy identyfikator i unikatowy identyfikator połączenia.
- `tracestate`: Wykonuje śledzenie kontekstu specyficzne dla systemu.

#### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Włącz obsługę śledzenia W3C rozproszone w przypadku klasycznych aplikacji ASP.NET

Ta funkcja jest dostępna w `Microsoft.ApplicationInsights.Web` i `Microsoft.ApplicationInsights.DependencyCollector` pakietów, począwszy od wersji 2.8.0-beta1.
On jest domyślnie wyłączony. Aby ją włączyć, należy zmienić `ApplicationInsights.config`:

- W obszarze `RequestTrackingTelemetryModule`, Dodaj `EnableW3CHeadersExtraction` element z wartością ustawioną na `true`.
- W obszarze `DependencyTrackingTelemetryModule`, Dodaj `EnableW3CHeadersInjection` element z wartością ustawioną na `true`.

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Włącz obsługę śledzenia W3C rozproszonych dla aplikacji platformy ASP.NET Core

Ta funkcja jest w `Microsoft.ApplicationInsights.AspNetCore` 2.5.0-beta1 wersji i `Microsoft.ApplicationInsights.DependencyCollector` 2.8.0-beta1 wersji.
On jest domyślnie wyłączony. Aby ją włączyć, ustaw `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` do `true`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

#### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Włącz obsługę śledzenia W3C rozproszonych dla aplikacji w języku Java

- **Konfiguracja przychodzącej**

  - W przypadku aplikacji Java EE, Dodaj następujący kod do `<TelemetryModules>` wewnątrz ApplicationInsights.xml:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
  - W przypadku aplikacji Spring Boot Dodaj następujące właściwości:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Konfiguracja wychodzące**

  Dodaj następujący kod do sztucznej Inteligencji — Agent.xml:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > Domyślnie jest włączony tryb zgodności z poprzednimi wersjami i `enableW3CBackCompat` parametr jest opcjonalny. Użyj go tylko wtedy, gdy chcesz wyłączyć zgodności z poprzednimi wersjami.
  >
  > W idealnym przypadku będzie to wyłączyć funkcję po wszystkie usługi zostały zaktualizowane do nowszych wersji zestawów SDK, które obsługują protokół W3C. Zdecydowanie zaleca się jak najszybciej przenieść do tych nowszej zestawów SDK.

> [!IMPORTANT]
> Upewnij się, że konfiguracje przychodzących i wychodzących są dokładnie takie same.

## <a name="opentracing-and-application-insights"></a>OpenTracing i Application Insights

[Specyfikacją modelu danych OpenTracing](https://opentracing.io/) i modeli danych usługi Application Insights mapowanie w następujący sposób:

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`,  `PageView`                 | `Span` z `span.kind = server`                  |
| `Dependency`                          | `Span` z `span.kind = client`                  |
| `Id` z `Request` i `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` typu `ChildOf` (zakres nadrzędny)   |

Aby uzyskać więcej informacji, zobacz [modelu danych telemetrycznych usługi Application Insights](../../azure-monitor/app/data-model.md). 

Definicje OpenTracing pojęć, zobacz OpenTracing [specyfikacji](https://github.com/opentracing/specification/blob/master/specification.md) i [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-net"></a>Korelacja telemetrii na platformie .NET

Wraz z upływem czasu .NET zdefiniowane korelowanie danych telemetrycznych i diagnostycznych dzienników na kilka sposobów:

- `System.Diagnostics.CorrelationManager` pozwala na śledzenie [LogicalOperationStack i ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). 
- `System.Diagnostics.Tracing.EventSource` i śledzenie zdarzeń dla Windows (ETW) Zdefiniuj [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) metody.
- `ILogger` używa [zakresy dziennika](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). 
- Windows Communication Foundation (WCF) i HTTP o komunikacji sieciowej się "bieżący" propagacji kontekstu.

Jednak tych metod nie została włączona obsługa automatycznego rozproszonego śledzenia. `DiagnosticSource` jest to sposób obsługi automatycznego korelacji między komputerami. Zezwalaj na automatyczne propagacji między komputerami kontekstu korelacji za pomocą transportu, takich jak HTTP i pomocy technicznej "DiagnosticSource" bibliotek programu .NET.

[Przewodnik dotyczący działań](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) w `DiagnosticSource` objaśnia podstawy działania śledzenia.

Platforma ASP.NET Core 2.0 obsługuje wyodrębniania nagłówków HTTP i uruchamianie nowego działania.

`System.Net.HttpClient`, począwszy od wersji 4.1.0, obsługuje automatyczne iniekcji nagłówków korelacji HTTP i śledzenia, HTTP wywołania jako działania.

Moduł HTTP jest nowy, [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), dla klasycznej platformy ASP.NET. Ten moduł stanowi wdrożenie korelacja telemetrii za pomocą `DiagnosticSource`. Rozpoczyna działanie od nagłówków żądań przychodzących. Zestawia dane telemetryczne z różnych etapów przetwarzania żądania, nawet w przypadkach, jeśli każdy etap przetwarzania Internet Information Services (IIS) jest uruchomiony na inny wątek.

Zestaw Application Insights SDK, począwszy od wersji 2.4.0-beta1, używa `DiagnosticSource` i `Activity` do zbierania danych telemetrycznych i skojarzyć go z bieżącego działania.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Korelacja telemetrii w zestawie SDK dla języka Java

[Zestaw Application Insights SDK dla języka Java](../../azure-monitor/app/java-get-started.md) obsługuje automatyczne korelacja telemetrii rozpoczynający się od wersji 2.0.0. Automatycznie wypełni `operation_id` dla wszystkich danych telemetrycznych (na przykład śladów, wyjątków i zdarzeń niestandardowych) wydane w zakresie żądania. On również dba o propagowanie nagłówki korelacji (opisanym wcześniej) dla wywołań do usługi za pośrednictwem protokołu HTTP, jeśli [agenta zestawu Java SDK](../../azure-monitor/app/java-agent.md) jest skonfigurowany.

> [!NOTE]
> Tylko wywołania za pośrednictwem Apache HTTPClient są obsługiwane w przypadku funkcji korelacji. Jeśli używasz środowiska Spring RestTemplate lub Feign zarówno może służyć za pomocą Apache HTTPClient pod maską.

Obecnie usługa propagacji automatyczne kontekstu między komunikatów technologii (takie platformy Kafka, RabbitMQ lub Azure Service Bus) nie jest obsługiwane. Jednak jest możliwe ręcznie kodu takich scenariuszy za pomocą `trackDependency` i `trackRequest` interfejsów API. W tych interfejsów API telemetrii zależności reprezentuje komunikat jest umieszczonych w kolejce przez producenta i żądanie reprezentuje komunikat jest przetwarzany przez odbiorcę. W takim przypadku zarówno `operation_id` i `operation_parentId` powinny być propagowane we właściwościach komunikatu.

### <a name="telemetry-correlation-in-asynchronous-java-application"></a>Korelacja telemetrii w asynchronicznej aplikacji Java

W celu skorelowania danych telemetrycznych w asynchronicznej aplikacji Spring Boot, postępuj zgodnie z [to](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications) szczegółowego artykułu. Wskazówki dotyczące Instrumentacji firmy Spring [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) także [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html). 


<a name="java-role-name"></a>
## <a name="role-name"></a>Nazwa roli

Czasami możesz chcieć dostosować sposób nazw składników są wyświetlane w [mapy aplikacji](../../azure-monitor/app/app-map.md). Aby to zrobić, można ręcznie ustawić `cloud_RoleName` , wykonując jedną z następujących czynności:

- Jeśli używasz aplikacji Spring Boot za pomocą Application Insights platformy Spring Boot starter tylko wymagana zmiana jest można ustawić nazwę niestandardowego dla aplikacji w pliku application.properties pliku.

  `spring.application.name=<name-of-app>`

  Spring Boot starter automatycznie przypisuje `cloudRoleName` wartość wprowadzona dla `spring.application.name` właściwości.

- Jeśli używasz `WebRequestTrackingFilter`, `WebAppNameContextInitializer` automatycznie ustawia nazwę aplikacji. Dodaj następujący element do pliku konfiguracji (plik ApplicationInsights.xml):

  ```XML
  <ContextInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebAppNameContextInitializer" />
  </ContextInitializers>
  ```

- Jeśli korzystasz z klasy kontekstu chmury:

  ```Java
  telemetryClient.getContext().getCloud().setRole("My Component Name");
  ```

## <a name="next-steps"></a>Kolejne kroki

- Zapis [niestandardowych danych telemetrycznych](../../azure-monitor/app/api-custom-events-metrics.md).
- Dowiedz się więcej o [ustawienie cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud_rolename) dla innych zestawów SDK.
- Dołączanie wszystkich składników usługi mikrousług w usłudze Application Insights. Zapoznaj się z [obsługiwanych platform](../../azure-monitor/app/platforms.md).
- Zobacz [modelu danych](../../azure-monitor/app/data-model.md) dla typów usługi Application Insights.
- Dowiedz się, jak [rozszerzyć i filtrowanie danych telemetrycznych](../../azure-monitor/app/api-filtering-sampling.md).
- Przegląd [odniesienia konfiguracji usługi Application Insights](configuration-with-applicationinsights-config.md).
