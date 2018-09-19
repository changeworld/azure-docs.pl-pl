---
title: Korelacja Telemetrii usługi usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Korelacja telemetrii usługi Application Insights
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/09/2018
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 696843363bc6617bb11c01cdccb9dbbb7b719a82
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46298204"
---
# <a name="telemetry-correlation-in-application-insights"></a>Korelacja telemetrii w usłudze Application Insights

W świecie wczesnych każdej operacji logicznej wymaga pracy wykonanej w różnych składników usługi. Każda z tych składników mogą być oddzielnie monitorowane przez [usługi Application Insights](app-insights-overview.md). Składnik aplikacji sieci web komunikuje się za pomocą składnika dostawcy uwierzytelniania na przeprowadzenie walidacji poświadczeń użytkownika, a także ze składnikiem interfejsu API można pobrać danych dla wizualizacji. Składnik interfejsu API, który z kolei można wysyłania zapytań dotyczących danych z innych usług i używanie składników Dostawca pamięci podręcznej i powiadom rozliczeń składnika temat tego wywołania. Korelacja telemetrii rozproszonych obsługuje aplikacji w szczegółowych informacji. Umożliwia wykrywanie, który składnik jest odpowiedzialny za niepowodzeń lub zmniejszenie wydajności.

W tym artykule opisano model danych używany przez usługę Application Insights do korelowanie danych telemetrycznych wysyłanych przez wiele składników. Poruszono w nim techniki propagacji kontekstu i protokołów. Obejmuje ona również implementację koncepcje korelacji różnych języków i platform.

## <a name="telemetry-correlation-data-model"></a>Model danych korelacja telemetrii

Definiuje usługę Application Insights [modelu danych](application-insights-data-model.md) dla korelacja telemetrii rozproszonych. Aby skojarzyć dane telemetryczne z operacji logicznej, każdy element danych telemetrycznych ma kontekście pole o nazwie `operation_Id`. Ten identyfikator jest udostępniany każdego elementu telemetrii w rozproszonego śledzenia. Nawet w przypadku utraty danych telemetrycznych z jednej warstwy nadal można skojarzyć dane telemetryczne zgłoszone przez inne składniki.

Rozproszone operacji logicznej zwykle składa się z szeregu mniejszych operacji — żądań przetworzonych przez jeden ze składników. Te operacje są definiowane przez [dane telemetryczne dotyczące żądań](application-insights-data-model-request-telemetry.md). Dane telemetryczne co żądania ma swój własny `id` określający sposób unikatowy globalnie. Należy określić wszystkie dane telemetryczne - śladów, wyjątków, itp., skojarzony z tym żądaniem `operation_parentId` wartości żądania `id`.

Każda operacja wychodzących takich jak wywołania http do innego składnika, reprezentowane przez [telemetrii zależności](application-insights-data-model-dependency-telemetry.md). Telemetria zależności usługi definiuje również swój własny `id` jest unikatowa w skali globalnej. Telemetria żądań, inicjowany przez wywołanie zależności, używa go w formie `operation_parentId`.

Można tworzyć w widoku rozproszonym operacji logicznej przy użyciu `operation_Id`, `operation_parentId`, i `request.id` z `dependency.id`. Te pola również zdefiniować kolejność przyczynowości wywołań telemetrii.

W środowisku wczesnych śladów ze składników mogą być przekazywane do różnych miejsc. Każdy składnik może mieć własny klucz Instrumentacji w usłudze Application Insights. Aby uzyskać dane telemetryczne dla operacji logicznej, należy wykonywać zapytania względem danych z każdego magazynu. Gdy liczba miejsc jest bardzo duży, musisz mieć wskazówkę o tym, gdzie szukać dalej.

Model danych definiuje dwa pola, aby rozwiązać ten problem w usłudze Application Insights: `request.source` i `dependency.target`. Pierwsze pole identyfikuje składnik, który zainicjował żądanie zależności, a druga identyfikuje, które składnik zwrócił odpowiedź na wywołania zależności.


## <a name="example"></a>Przykład

Spójrzmy na przykład cen zasobów aplikacji przedstawiający bieżąca cena rynku zasobu za pomocą zewnętrznego interfejsu API o nazwie zasobów interfejsu API. Aplikacja ceny akcji ma stronę `Stock page` otwartego przy użyciu przeglądarki sieci web klienta `GET /Home/Stock`. Aplikacja wykonuje zapytania interfejsu API zasobów przy użyciu wywołania HTTP `GET /api/stock/value`.

Możesz analizować wynikowe dane telemetryczne, wykonanie kwerendy:

```
(requests | union dependencies | union pageViews) 
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

W nocie widok wyników, wszystkie elementy danych telemetrycznych udostępnić główny `operation_Id`. Podczas wywołania ajax wprowadzone na stronie — nowy unikatowy identyfikator `qJSXU` jest przypisany do telemetrii zależności i wyświetlenia strony jego identyfikator jest używany jako `operation_ParentId`. Z kolei żądanie serwera używa identyfikatora w technologii ajax jako `operation_ParentId`itp.

| ItemType   | name                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| wyświetlenia strony   | Podstawowy strony                |              | STYz               | STYz         |
| zależność | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| wyślij żądanie    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| zależność | Pobierz /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Teraz po wywołaniu `GET /api/stock/value` wprowadzone do usługi zewnętrznej, aby znać tożsamość tego serwera. Możesz więc ustawić `dependency.target` odpowiednio pola. Gdy usługi zewnętrznej nie obsługuje monitorowania - `target` jest ustawiona na nazwę hosta usługi, takiej jak `stock-prices-api.com`. Jednak jeśli usługi identyfikuje, zwracając wstępnie zdefiniowanej nagłówka HTTP - `target` zawiera tożsamości usługi, która umożliwia usługi Application Insights do utworzenia rozproszonego śledzenia, badając dane telemetryczne z tej usługi. 

## <a name="correlation-headers"></a>Nagłówki korelacji

Firma Microsoft pracuje nad RFC propozycję [korelacji protokołu HTTP](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v1.md). Propozycja ta definiuje dwa nagłówki:

- `Request-Id` przenoszenia globalnie unikatowy identyfikator wywołania
- `Correlation-Context` -wykonuje kolekcję par wartości nazwy we właściwościach rozproszonego śledzenia

Standardowa definiuje również dwa schematy `Request-Id` generowania — daje płaski i hierarchicznych. Za pomocą prostego schematu jest dobrze znanym `Id` klucza zdefiniowane dla `Correlation-Context` kolekcji.

Definiuje usługę Application Insights [rozszerzenia](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) dla korelacji protokołu HTTP. Używa ona `Request-Context` nazwy pary wartości do propagowania zbiór właściwości, które korzystają z bezpośredniego obiektu wywołującego lub obiekt wywoływany. Zestaw SDK usługi Application Insights używa tego pliku nagłówkowego w celu ustawienia `dependency.target` i `request.source` pola.

### <a name="w3c-distributed-tracing"></a>W3C rozproszonego śledzenia

Firma cleardb zmienia się (format W3C rozproszonego śledzenia) [https://w3c.github.io/distributed-tracing/report-trace-context.html]. Definiuje on:
- `traceparent` -niesie ze sobą globalnie unikatowy identyfikator i unikatowy identyfikator wywołania
- `tracestate` -niesie ze sobą w określonym kontekście systemu śledzenia.

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-classic-apps"></a>Włącz obsługę śledzenia W3C rozproszonych dla aplikacji platformy ASP.NET, klasyczne

Ta funkcja jest dostępna w pakietach Microsoft.ApplicationInsights.Web i Microsoft.ApplicationInsights.DependencyCollector, począwszy od wersji 2.8.0-beta1.
Jest **poza** domyślnie, aby ją włączyć, należy zmienić `ApplicationInsights.config`:

* w obszarze `RequestTrackingTelemetryModule` Dodaj `EnableW3CHeadersExtraction` element z wartością ustawioną na `true`
* w obszarze `DependencyTrackingTelemetryModule` Dodaj `EnableW3CHeadersInjection` element z wartością ustawioną na `true`

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Włącz obsługę śledzenia W3C rozproszonych dla aplikacji platformy ASP.NET Core

Ta funkcja jest w Microsoft.ApplicationInsights.AspNetCore 2.5.0-beta1 wersji i 2.8.0-beta1 wersji Microsoft.ApplicationInsights.DependencyCollector.
Jest **poza** domyślnie, aby ją włączyć ustaw `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` do `true`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

## <a name="open-tracing-and-application-insights"></a>Usługa Application Insights i Otwórz śledzenie

[Otwórz śledzenie](http://opentracing.io/) i wygląda modele danych w usłudze Application Insights 

- `request`, `pageView` mapuje **zakres** z `span.kind = server`
- `dependency` mapuje **zakres** z `span.kind = client`
- `id` z `request` i `dependency` mapuje **Span.Id**
- `operation_Id` mapuje **TraceId**
- `operation_ParentId` mapuje **odwołania** typu `ChildOf`

Zobacz [modelu danych](application-insights-data-model.md) dla usługi Application Insights typów i danych modelu.

Zobacz [specyfikacji](https://github.com/opentracing/specification/blob/master/specification.md) i [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md) definicji Otwórz śledzenie pojęcia.


## <a name="telemetry-correlation-in-net"></a>Korelacja telemetrii na platformie .NET

Wraz z upływem czasu .NET zdefiniowane wiele sposobów, w celu skorelowania danych telemetrycznych i diagnostycznych dzienników. Brak `System.Diagnostics.CorrelationManager` umożliwiające śledzenie [LogicalOperationStack i ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). `System.Diagnostics.Tracing.EventSource` i Windows ETW zdefiniować metodę [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx). `ILogger` używa [zakresy dziennika](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). Architektura WCF i Http o komunikacji sieciowej się "bieżący" propagacji kontekstu.

Jednak tych metod nie została włączona obsługa automatycznego rozproszonego śledzenia. `DiagnosticsSource` jest sposobem obsługi automatycznego krzyżowe korelacji maszyny. Biblioteki .NET obsługuje źródło diagnostyki i Zezwalaj na automatyczne krzyżowe propagacji maszyny kontekstu korelacji za pomocą transportu, takich jak http.

[Przewodnik dotyczący działań](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) w źródle diagnostyki objaśnia podstawy działania śledzenia. 

Platforma ASP.NET Core 2.0 obsługuje wyodrębniania nagłówków Http i uruchamianie nowego działania. 

`System.Net.HttpClient` począwszy od wersji `4.1.0` obsługuje automatyczne uruchomienie korelacji nagłówków Http i śledzenie wywołania http jako działania.

Moduł Http jest nowy [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/) klasyczny platformy ASP.NET. Ten moduł stanowi wdrożenie korelacja telemetrii za pomocą DiagnosticsSource. Rozpoczyna działanie od nagłówków żądań przychodzących. Zestawia dane telemetryczne z różnych etapach procesu przetwarzania żądania. Nawet w przypadkach, jeśli każdy etap przetwarzania w usługach IIS jest uruchomiony na wątki innego zarządzanie.

Wersję początkową aplikacji zestawu SDK usługi Insights `2.4.0-beta1` używa DiagnosticsSource i działania, aby zbierać dane telemetryczne i skojarzyć go z bieżącego działania. 

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Korelacja telemetrii w zestawie SDK dla języka Java
[Zestawu SDK Java usługi Application Insights](app-insights-java-get-started.md) obsługuje automatyczne korelacja telemetrii rozpoczynający się od wersji `2.0.0`. Automatycznie wypełni `operation_id` dla wszystkich danych telemetrycznych (śladów, wyjątków, zdarzeń niestandardowych itp.) wydane w zakresie żądania. On również zajmuje się propagowanie nagłówki korelacji (opisane powyżej), aby wywołania usług za pośrednictwem protokołu HTTP Jeśli [agenta zestawu Java SDK](app-insights-java-agent.md) jest skonfigurowany. Uwaga: tylko wywołania za pośrednictwem klienta HTTP Apache są obsługiwane przez funkcję korelacji. Jeśli używasz szablonu Rest Spring lub Feign zarówno może służyć za pomocą klienta HTTP Apache pod maską.

Obecnie usługa propagacji automatyczne kontekstu między technologii obsługi komunikatów (np. Kafka, RabbitMQ, usługi Azure Service Bus) nie jest obsługiwana. Możliwe jest jednak ręczne kodu takich scenariuszy za pomocą `trackDependency` i `trackRequest` interfejsów API, według której telemetrii zależności reprezentuje komunikat jest umieszczonych w kolejce przez producenta, a żądanie komunikatów przetwarzanych przez konsumenta. W takim przypadku zarówno `operation_id` i `operation_parentId` powinny być propagowane we właściwościach komunikatu.

<a name="java-role-name"></a>
### <a name="role-name"></a>Nazwa roli
Czasami możesz chcieć dostosować sposób nazw składników są wyświetlane w [mapy aplikacji](app-insights-app-map.md). Aby to zrobić, można ręcznie ustawić `cloud_roleName` , wykonując jedną z następujących czynności:

Za pomocą inicjatora telemetrii (wszystkie elementy dane telemetryczne są oznakowany)
```Java
public class CloudRoleNameInitializer extends WebTelemetryInitializerBase {

    @Override
    protected void onInitializeTelemetry(Telemetry telemetry) {
        telemetry.getContext().getTags().put(ContextTagKeys.getKeys().getDeviceRoleName(), "My Component Name");
    }
  }
```
Za pomocą [klasy kontekstu urządzenia](https://docs.microsoft.com/et-ee/java/api/com.microsoft.applicationinsights.extensibility.context._device_context) (oznakowany tego elementu telemetrii)
```Java
telemetry.getContext().getDevice().setRoleName("My Component Name");
```

## <a name="next-steps"></a>Kolejne kroki

- [Zapisywanie niestandardowych danych telemetrycznych](app-insights-api-custom-events-metrics.md)
- Dołączanie wszystkich składników wczesnych usługi w usłudze Application Insights. Zapoznaj się z [obsługiwanych platform](app-insights-platforms.md).
- Zobacz [modelu danych](application-insights-data-model.md) dla usługi Application Insights typów i danych modelu.
- Dowiedz się, jak [rozszerzyć i filtrowanie danych telemetrycznych](app-insights-api-filtering-sampling.md).
- [Dokumentacja usługi Application Insights konfigracja](app-insights-configuration-with-applicationinsights-config.md)

