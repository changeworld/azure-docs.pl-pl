---
title: Liczniki zdarzeń w usłudze Application Insights | Dokumenty firmy Microsoft
description: Monitoruj system i niestandardowe liczniki zdarzeń core .NET/.NET w usłudze Application Insights.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 2094c012e86131073fc66be4f2ac2fb2e81ef4c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663593"
---
# <a name="eventcounters-introduction"></a>Wprowadzenie do eventcounters

`EventCounter`jest .NET/.NET Core mechanizm publikowania i zużywają liczniki lub statystyki. [Ten](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) dokument zawiera `EventCounters` omówienie i przykłady dotyczące sposobu ich publikowania i korzystania z nich. EventCounters są obsługiwane na wszystkich platformach systemu operacyjnego - Windows, Linux i macOS. Można go traktować jako odpowiednik między platformami dla [PerformanceCounters,](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter) który jest obsługiwany tylko w systemach Windows.

Podczas gdy użytkownicy `EventCounters` mogą publikować dowolny niestandardowy, aby spełnić ich potrzeby, środowisko uruchomieniowe .NET Core 3.0 domyślnie publikuje zestaw tych liczników. Dokument przejdzie przez kroki wymagane do zbierania i wyświetlania `EventCounters` (zdefiniowane przez system lub zdefiniowane przez użytkownika) w usłudze Azure Application Insights.

## <a name="using-application-insights-to-collect-eventcounters"></a>Zbieranie liczników zdarzeń za pomocą usługi Application Insights

Usługa Application Insights `EventCounters` obsługuje `EventCounterCollectionModule`zbieranie za pomocą programu , który jest częścią nowo wydanego pakietu nuget [Microsoft.ApplicationInsights.EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector). `EventCounterCollectionModule`jest automatycznie włączana podczas korzystania z [AspNetCore](asp-net-core.md) lub [WorkerService](worker-service.md). `EventCounterCollectionModule`zbiera liczniki z niekonfigurowalną częstotliwością zbierania 60 sekund. Nie ma żadnych specjalnych uprawnień wymaganych do zbierania EventCounters.

## <a name="default-counters-collected"></a>Liczniki domyślne zbierane

W przypadku aplikacji uruchomionych w pliku .NET Core 3.0 następujące liczniki są zbierane automatycznie przez sdk. Nazwa liczników będzie pochodzić z formularza "Kategoria| Licznik".

|Kategoria | Licznik|
|---------------|-------|
|`System.Runtime` | `cpu-usage` |
|`System.Runtime` | `working-set` |
|`System.Runtime` | `gc-heap-size` |
|`System.Runtime` | `gen-0-gc-count` |
|`System.Runtime` | `gen-1-gc-count` |
|`System.Runtime` | `gen-2-gc-count` |
|`System.Runtime` | `time-in-gc` |
|`System.Runtime` | `gen-0-size` |
|`System.Runtime` | `gen-1-size` |
|`System.Runtime` | `gen-2-size` |
|`System.Runtime` | `loh-size` |
|`System.Runtime` | `alloc-rate` |
|`System.Runtime` | `assembly-count` |
|`System.Runtime` | `exception-count` |
|`System.Runtime` | `threadpool-thread-count` |
|`System.Runtime` | `monitor-lock-contention-count` |
|`System.Runtime` | `threadpool-queue-length` |
|`System.Runtime` | `threadpool-completed-items-count` |
|`System.Runtime` | `active-timer-count` |
|`Microsoft.AspNetCore.Hosting` | `requests-per-second` |
|`Microsoft.AspNetCore.Hosting` | `total-requests` |
|`Microsoft.AspNetCore.Hosting` | `current-requests` |
|`Microsoft.AspNetCore.Hosting` | `failed-requests` |

> [!NOTE]
> Liczniki kategorii Microsoft.AspNetCore.Hosting są dodawane tylko w ASP.NET podstawowych aplikacji.

## <a name="customizing-counters-to-be-collected"></a>Dostosowywanie liczników do zebrania

W poniższym przykładzie pokazano, jak dodawać/usuwać liczniki. To dostosowanie zostanie wykonane `ConfigureServices` w metodzie aplikacji po włączeniu zbierania danych `AddApplicationInsightsTelemetry()` `AddApplicationInsightsWorkerService()`telemetrycznych usługi Application Insights przy użyciu jednego lub . Poniżej przedstawiono przykładowy kod z aplikacji ASP.NET Core. W przypadku innych typów aplikacji należy zapoznać się z [tym](worker-service.md#configuring-or-removing-default-telemetrymodules) dokumentem.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows several customizations done to EventCounterCollectionModule.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

        // The following code removes EventCounterCollectionModule to disable the module completely.
        var eventCounterModule = services.FirstOrDefault<ServiceDescriptor>
                    (t => t.ImplementationType == typeof(EventCounterCollectionModule));
        if (eventCounterModule != null)
        {
            services.Remove(eventCounterModule);
        }
    }
```

## <a name="event-counters-in-metric-explorer"></a>Liczniki zdarzeń w Eksploratorze metryk

Aby wyświetlić metryki EventCounter w [Eksploratorze metryk,](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)wybierz zasób usługi Application Insights i wybierz metryki oparte na dzienniku jako obszar nazw metryki. Następnie metryki EventCounter są wyświetlane w kategorii niestandardowej.

> [!div class="mx-imgBorder"]
> ![Liczniki zdarzeń zgłaszane w usłudze Application Insights](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Liczniki zdarzeń w Analytics

Możesz też wyszukiwać i wyświetlać raporty liczników zdarzeń w [Analytics](../../azure-monitor/app/analytics.md)w tabeli **customMetrics.**

Na przykład uruchom następującą kwerendę, aby zobaczyć, jakie liczniki są zbierane i dostępne do kwerendy:

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Liczniki zdarzeń zgłaszane w usłudze Application Insights](./media/event-counters/analytics-event-counters.png)

Aby uzyskać wykres określonego licznika (na przykład: `ThreadPool Completed Work Item Count`) w ostatnim okresie, uruchom następującą kwerendę.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Czat z jednym licznikiem w usłudze Application Insights](./media/event-counters/analytics-completeditems-counters.png)

Podobnie jak inne dane telemetryczne, `cloud_RoleInstance` **customMetrics** ma również kolumnę, która wskazuje tożsamość wystąpienia serwera hosta, na którym aplikacja jest uruchomiona. Powyższa kwerenda pokazuje wartość licznika na wystąpienie i może służyć do porównywania wydajności różnych wystąpień serwera.

## <a name="alerts"></a>Alerty
Podobnie jak inne metryki, można [ustawić alert,](../../azure-monitor/app/alerts.md) aby ostrzec, jeśli licznik zdarzeń wykracza poza limit określony. Otwórz okienko Alerty i kliknij pozycję Dodaj alert.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Czy mogę zobaczyć EventCounters w metrykach na żywo?

Metryki na żywo nie pokazują EventCounters od dzisiaj. Użyj Eksploratora metryk lub Analytics, aby wyświetlić dane telemetryczne.

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>Na jakich platformach można zobaczyć domyślną listę liczników .NET Core 3.0?

EventCounter nie wymaga żadnych specjalnych uprawnień i jest obsługiwany na wszystkich platformach .NET Core 3.0 jest obsługiwany. Obejmuje to:

* **System operacyjny**: Windows, Linux lub macOS.
* **Metoda hostingu:** W procesie lub poza procesem.
* **Metoda wdrażania:** Zależne od struktury lub samodzielne.
* **Serwer www:** IIS (Internet Information Server) lub Kestrel.
* **Platforma hostingowa:** Funkcja aplikacji sieci Web usługi Azure App Service, Azure VM, Docker, Usługa Azure Kubernetes (AKS) i tak dalej.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Włączono usługę Application Insights z usługi Azure Web App Portal. Ale nie widzę EventCounters.?

 [Application Insights dla](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) ASP.NET Core nie obsługuje jeszcze tej funkcji. Ten dokument zostanie zaktualizowany, gdy ta funkcja jest obsługiwana.

## <a name="next-steps"></a><a name="next"></a>Następne kroki

* [Śledzenie zależności](../../azure-monitor/app/asp-net-dependencies.md)
