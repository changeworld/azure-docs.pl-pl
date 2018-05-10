---
title: Korelacji Telemetrii Insights aplikacji Azure | Dokumentacja firmy Microsoft
description: Application Insights telemetrii korelacji
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/09/2018
ms.author: mbullwin; sergkanz
ms.openlocfilehash: 12b46b4abaa17fe9dd0e9055bca5463312bbd15d
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="telemetry-correlation-in-application-insights"></a>Korelacji telemetrii w usłudze Application Insights

W świecie usługi micro każdej operacji logicznych wymaga pracować w różnych składników usługi. Każdy z tych składników mogą być oddzielnie monitorowane przez [usługi Application Insights](app-insights-overview.md). Składnik aplikacji sieci web komunikuje się za pomocą składnika dostawcy uwierzytelniania można sprawdzić poprawności poświadczeń użytkownika, a za pomocą składnika interfejsu API można pobrać danych dla wizualizacji. Składnik interfejsu API, który z kolei może zapytania na danych z innych usług i użyj składników dostawcy pamięci podręcznej i powiadom rozliczeń składnik o tego wywołania. Aplikacji Insights obsługuje rozproszonej telemetrii korelacji. Umożliwia wykrywanie, który składnik jest odpowiedzialny za błędy lub spadek wydajności.

W tym artykule opisano model danych używany przez usługi Application Insights do skorelowania danych telemetrycznych wysłanych przez kilka składników. Obejmuje ona techniki propagacji kontekstu i protokołów. Obejmuje ona również implementacja korelacji pojęcia dotyczące różnych języków i platform.

## <a name="telemetry-correlation-data-model"></a>Model danych telemetrycznych korelacji

Definiuje usługi Application Insights [modelu danych](application-insights-data-model.md) dla rozproszonych telemetrii korelacji. Aby skojarzyć dane telemetryczne z operacji logicznej, każdy element telemetrii ma pole kontekstu o nazwie `operation_Id`. Ten identyfikator jest współużytkowany przez każdy element dane telemetryczne w śladzie rozproszonych. Dlatego nawet w przypadku utraty danych telemetrycznych z jedną warstwę nadal można skojarzyć dane telemetryczne zgłoszone przez inne składniki.

Operacja logiczna rozproszonej zazwyczaj składa się z zestaw operacji mniejsze - żądań przetwarzanych przez jeden ze składników. Te operacje są definiowane przez [żądanie telemetrii](application-insights-data-model-request-telemetry.md). Co dane telemetryczne żądania ma własną `id` identyfikującym jednoznacznie globalnie. Należy określić wszystkie dane telemetryczne - śladów, wyjątków, itp., skojarzony z tym żądaniem `operation_parentId` wartości żądania `id`.

Każdej operacji wychodzących, takie jak wywołanie http do innego elementu reprezentowanego przez [dane telemetryczne zależności](application-insights-data-model-dependency-telemetry.md). Dane telemetryczne zależności definiuje również własną `id` jest globalnie unikatowa. Dane telemetryczne żądania, inicjowane przez wywołanie zależności, używa go jako `operation_parentId`.

Można utworzyć widoku przy użyciu operacji logicznych rozproszonej `operation_Id`, `operation_parentId`, i `request.id` z `dependency.id`. Te pola także zdefiniować kolejność przyczynowości wywołań telemetrii.

W środowisku usług micro śladów ze składników może przejść do różnych miejsc. Każda część może mieć własną klucza Instrumentacji w usłudze Application Insights. Aby uzyskać dane telemetryczne dla tej operacji logicznych, należy wykonać zapytania o dane z każdym magazynu. W przypadku dużych liczbę miejsc, musisz mieć wskazówkę na miejsce wyszukiwania dalej.

Model danych definiuje dwa pola, aby rozwiązać ten problem w usłudze Application Insights: `request.source` i `dependency.target`. Pierwsze pole identyfikuje składnik, który zainicjował żądanie zależności, a drugi identyfikuje, który składnik zwrócił odpowiedź wywołania zależności.


## <a name="example"></a>Przykład

Spójrzmy na przykład cen GIEŁDOWYCH aplikacji wyświetlanie bieżącego ceny rynku akcji przy użyciu zewnętrznego interfejsu API wywołuje interfejs API zasobów. CENY zasobów aplikacji jest strona `Stock page` otworzyć za pomocą przeglądarki sieci web klienta `GET /Home/Stock`. Aplikacja odwołuje się interfejs API zasobów przy użyciu wywołania HTTP `GET /api/stock/value`.

Można analizować wynikowy telemetrii kwerendy:

```
(requests | union dependencies | union pageViews) 
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Uwagi widoku wyników, że wszystkie elementy telemetrii udostępnić katalog główny `operation_Id`. Gdy wywołanie ajax wprowadzone na stronie — nowy unikatowy identyfikator `qJSXU` jest przypisany do dane telemetryczne zależności i widok strony jego identyfikator jest używany jako `operation_ParentId`. Z kolei żądanie serwera używa identyfikatora w technologii ajax jako `operation_ParentId`itp.

| ItemType   | name                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| Widok strony   | Strona standardowych                |              | STYz               | STYz         |
| zależności | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| Żądanie    | Strona główna GET/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| zależności | Pobierz /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Teraz po wywołaniu `GET /api/stock/value` do zewnętrznej usługi ma muszą znać tożsamość tego serwera. Można ustawić `dependency.target` odpowiednio do pola. Gdy zewnętrzna usługa nie obsługuje monitorowania - `target` jest ustawiona na nazwę hosta usługi, takiej jak `stock-prices-api.com`. Jednak jeśli czy usługa identyfikuje zwracając wstępnie zdefiniowanej nagłówka HTTP - `target` zawiera tożsamości usługi, która umożliwia usługi Application Insights do tworzenia rozproszonych śledzenia badając dane telemetryczne z tej usługi. 

## <a name="correlation-headers"></a>Nagłówki korelacji

Pracujemy nad RFC propozycję [korelacji protokołu HTTP](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v1.md). Propozycja ta definiuje dwa nagłówki:

- `Request-Id` przenoszenia globalnie unikatowy identyfikator wywołania
- `Correlation-Context` -zawiera kolekcję par wartości nazwy właściwości rozproszonej śledzenia

Standardowe definiuje również dwa schematy `Request-Id` generowania - płaski i hierarchicznej. Ze schematem płaskiej jest dobrze znanym `Id` klucz zdefiniowany dla `Correlation-Context` kolekcji.

Definiuje usługi Application Insights [rozszerzenia](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) dla korelacji protokołu HTTP. Używa `Request-Context` nazwa pary wartości propagację zbiór właściwości używanych przez natychmiastowe wywołujący lub wywoływany. Zestaw SDK usługi Application Insights używa tego nagłówka do ustawienia `dependency.target` i `request.source` pól.

## <a name="open-tracing-and-application-insights"></a>Otwórz śledzenie i usługi Application Insights

[Otwórz śledzenie](http://opentracing.io/) i wygląda modeli danych usługi Application Insights 

- `request`, `pageView` mapuje **zakres** z `span.kind = server`
- `dependency` mapuje **zakres** z `span.kind = client`
- `id` z `request` i `dependency` mapuje **Span.Id**
- `operation_Id` mapuje **TraceId**
- `operation_ParentId` mapuje **odwołania** typu `ChildOf`

Zobacz [modelu danych](application-insights-data-model.md) dla modelu danych i typów usługi Application Insights.

Zobacz [specyfikacji](https://github.com/opentracing/specification/blob/master/specification.md) i [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md) definicji śledzenia Otwórz pojęcia.


## <a name="telemetry-correlation-in-net"></a>Korelacja telemetrii w .NET

Wraz z upływem czasu .NET zdefiniowane kilka sposobów, aby skorelować danych telemetrycznych i diagnostycznych dzienników. Brak `System.Diagnostics.CorrelationManager` umożliwiający śledzenie [LogicalOperationStack i ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). `System.Diagnostics.Tracing.EventSource` i Windows ETW definiują metodę [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx). `ILogger` używa [zakresy dziennika](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). Usługi WCF i Http podczas transmisji się "bieżący" propagacji kontekstu.

Jednak tych metod nie umożliwia automatycznego śledzenia rozproszonych. `DiagnosticsSource` sposób obsługi odbywa się automatycznie cross korelacji maszyny. Biblioteki .NET obsługuje źródła diagnostyki i Zezwalaj na automatyczne cross propagacji maszyny kontekstu korelacji za pomocą transportu, takich jak http.

[Przewodnik dotyczący działań](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) w źródle diagnostyki przedstawiono podstawowe śledzenie działań. 

Platformy ASP.NET Core 2.0 obsługuje wyodrębniania nagłówków Http i uruchamianie nowe działanie. 

`System.Net.HttpClient` wersję początkową `4.1.0` obsługuje automatyczne uruchomienie korelacji nagłówków Http i śledzenie wywołanie http jako działania.

Moduł Http jest nowy [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/) klasycznego ASP.NET. Ten moduł stanowi wdrożenie przy użyciu DiagnosticsSource korelacji telemetrii. Rozpoczyna działania od nagłówków żądań przychodzących. Są również powiązane dane telemetryczne z różnych etapów przetwarzania żądania. Nawet w przypadku przypadków po uruchomieniu każdego etapu przetwarzania usług IIS w wątkach różnych zarządzanie.

Wersję początkową aplikacji zestawu SDK Insights `2.4.0-beta1` używa DiagnosticsSource i działania do zbierania danych telemetrycznych i skojarzyć go z bieżącego działania. 

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Korelacji danych telemetrycznych zestawu SDK Java
[Zestaw SDK Java usługi Application Insights](app-insights-java-get-started.md) obsługuje automatyczne korelacji telemetrii, począwszy od wersji `2.0.0`. Automatycznie wypełni `operation_id` dla wszystkie dane telemetryczne (śladów, wyjątków, niestandardowych zdarzeń itd.) wydane w ramach zakresu żądania. On również zajmuje się propagowanie nagłówki korelacji (opisanej powyżej) dla wywołań usług za pośrednictwem protokołu HTTP Jeśli [agenta zestawu Java SDK](app-insights-java-agent.md) jest skonfigurowany. Uwaga: tylko wywołań za pośrednictwem Apache HTTP klienta są obsługiwane przez funkcję korelacji. Jeśli używasz szablonu Rest Spring lub Feign zarówno może służyć pomocą Apache HTTP klienta pod maską.

Obecnie usługa propagacji automatyczne kontekstu w technologii komunikacji (np. Kafka, RabbitMQ, usługi Azure Service Bus) nie jest obsługiwana. Możliwe jest jednak ręczne kodu za pomocą takich scenariuszy `trackDependency` i `trackRequest` interfejsu API, zgodnie z którymi dane telemetryczne zależności reprezentuje komunikat jest umieszczonych w kolejce przez producenta, a żądanie komunikatów przetwarzanych przez konsumenta. W takim przypadku zarówno `operation_id` i `operation_parentId` powinny propagowane we właściwościach komunikatu.

<a name="java-role-name"></a>
### <a name="role-name"></a>Nazwa roli
W czasie, można dostosować nazwy składnika są wyświetlane w [mapowanie aplikacji](app-insights-app-map.md). Aby to zrobić, można ręcznie ustawić `cloud_roleName` wykonując jedną z następujących czynności:

Za pomocą inicjator telemetrii (wszystkie elementy dane telemetryczne są oznakowany)
```Java
public class CloudRoleNameInitializer extends WebTelemetryInitializerBase {

    @Override
    protected void onInitializeTelemetry(Telemetry telemetry) {
        telemetry.getContext().getTags().put(ContextTagKeys.getKeys().getDeviceRoleName(), "My Component Name");
    }
  }
```
Za pomocą [klasy kontekstu urządzenia](https://docs.microsoft.com/et-ee/java/api/com.microsoft.applicationinsights.extensibility.context._device_context) (oznakowany tego elementu danych telemetrycznych)
```Java
telemetry.getContext().getDevice().setRoleName("My Component Name");
```

## <a name="next-steps"></a>Kolejne kroki

- [Telemetria niestandardowa zapisu](app-insights-api-custom-events-metrics.md)
- Dołączyć wszystkie składniki usługi micro na usługi Application Insights. Zapoznaj się z [obsługiwanych platform](app-insights-platforms.md).
- Zobacz [modelu danych](application-insights-data-model.md) dla modelu danych i typów usługi Application Insights.
- Dowiedz się, jak [rozszerzanie i filtrować dane telemetryczne](app-insights-api-filtering-sampling.md).
