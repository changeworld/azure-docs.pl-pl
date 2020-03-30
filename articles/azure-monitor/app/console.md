---
title: Usługa Azure Application Insights dla aplikacji konsoli | Dokumenty firmy Microsoft
description: Monitoruj aplikacje internetowe pod kątem dostępności, wydajności i użycia.
ms.topic: conceptual
ms.date: 12/02/2019
ms.reviewer: lmolkova
ms.openlocfilehash: baaea0f8055eeff0314fcf5fde00729ea8091d12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655433"
---
# <a name="application-insights-for-net-console-applications"></a>Usługa Application Insights dla aplikacji konsoli .NET

[Usługa Application Insights](../../azure-monitor/app/app-insights-overview.md) umożliwia monitorowanie aplikacji sieci web pod kątem dostępności, wydajności i użycia.

Potrzebujesz subskrypcji za pomocą [platformy Microsoft Azure](https://azure.com). Zaloguj się za pomocą konta Microsoft, które może mieć dla systemu Windows, Xbox Live lub innych usług w chmurze firmy Microsoft. Twój zespół może mieć subskrypcję organizacyjną platformy Azure: poproś właściciela, aby dodał Cię do niego przy użyciu konta Microsoft.

> [!NOTE]
> Istnieje nowy SDK usługi Application Insights o nazwie [Microsoft.ApplicationInsights.WorkerService,](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) który może służyć do włączania usługi Application Insights dla dowolnych aplikacji konsoli. Zaleca się użycie tego pakietu i powiązanych instrukcji [stąd](../../azure-monitor/app/worker-service.md). Ten pakiet [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)jest przeznaczony dla osób docelowych i dlatego może być używany w .NET Core 2.0 lub nowszym oraz .NET Framework 4.7.2 lub nowszym.

## <a name="getting-started"></a>Wprowadzenie

* W witrynie [Azure Portal](https://portal.azure.com)[utwórz zasób usługi Application Insights](../../azure-monitor/app/create-new-resource.md). W przypadku typu aplikacji wybierz opcję **Ogólne**.
* Wykonaj kopię klucza instrumentacji. Znajdź klucz z listy rozwijanej **Essentials** nowego zasobu, który został utworzony.
* Zainstaluj najnowszy pakiet [Microsoft.ApplicationInsights.](https://www.nuget.org/packages/Microsoft.ApplicationInsights)
* Ustaw klucz instrumentacji w kodzie przed śledzeniem danych telemetrycznych (lub ustaw APPINSIGHTS_INSTRUMENTATIONKEY zmienną środowiskową). Następnie powinieneś mieć możliwość ręcznego śledzenia danych telemetrycznych i wyświetlanie jej w witrynie Azure portal

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

> [!NOTE]
> Telemetria nie jest wysyłana natychmiast. Elementy telemetrii są wsadowe i wysyłane przez applicationinsights SDK. W aplikacjach konsoli, która `Track()` kończy się zaraz po wywołaniu metod, telemetria nie może być wysyłana, chyba że `Flush()` i `Sleep` jest wykonywana przed zamknięciem aplikacji, jak pokazano w pełnym [przykładzie](#full-example) w dalszej części tego artykułu.


* Zainstaluj najnowszą wersję pakietu [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) — automatycznie śledzi http, SQL lub inne wywołania zależności zewnętrznych.

Można zainicjować i skonfigurować usługi Application `ApplicationInsights.config` Insights z kodu lub przy użyciu pliku. Upewnij się, że inicjowanie odbywa się tak wcześnie, jak to możliwe. 

> [!NOTE]
> Instrukcje odnoszące się do **ApplicationInsights.config** mają zastosowanie tylko do aplikacji, które są przeznaczone dla programu .NET Framework i nie mają zastosowania do aplikacji .NET Core.

### <a name="using-config-file"></a>Korzystanie z pliku konfiguracyjnego

Domyślnie SDK usługi Application `ApplicationInsights.config` Insights wyszukuje `TelemetryConfiguration` plik w katalogu roboczym podczas tworzenia

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

Można również określić ścieżkę do pliku konfiguracyjnego.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

Aby uzyskać więcej informacji, zobacz [odwołanie do pliku konfiguracji](configuration-with-applicationinsights-config.md).

Możesz uzyskać pełny przykład pliku konfiguracyjnego, instalując najnowszą wersję pakietu [Microsoft.ApplicationInsights.WindowsServer.](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) Oto **minimalna** konfiguracja dla kolekcji zależności, która jest równoważna przykładowi kodu.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>Konfigurowanie kolekcji telemetrii na podstawie kodu
> [!NOTE]
> Odczyt pliku konfiguracyjnego nie jest obsługiwany w programie .NET Core. Można rozważyć użycie [SDK usługi Application Insights dla ASP.NET Core](../../azure-monitor/app/asp-net-core.md)

* Podczas uruchamiania aplikacji tworzenie `DependencyTrackingTelemetryModule` i konfigurowanie wystąpienia — musi być singleton i muszą być zachowane przez okres istnienia aplikacji.

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* Dodawanie typowych inicjatorów telemetrii

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

Jeśli utworzono `TelemetryConfiguration()` konfigurację z konstruktorem zwykły, należy włączyć obsługę korelacji dodatkowo. **Nie jest to potrzebne,** jeśli odczytujesz konfigurację z pliku, używane `TelemetryConfiguration.CreateDefault()` lub `TelemetryConfiguration.Active`.

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* Można również zainstalować i zainicjować moduł kolektora Licznik wydajności, jak opisano [tutaj](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)


#### <a name="full-example"></a>Pełny przykład

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility;
using System.Net.Http;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient(configuration);
            using (InitializeDependencyTracking(configuration))
            {
                // run app...

                telemetryClient.TrackTrace("Hello World!");

                using (var httpClient = new HttpClient())
                {
                    // Http dependency is automatically tracked!
                    httpClient.GetAsync("https://microsoft.com").Wait();
                }

            }

            // before exit, flush the remaining data
            telemetryClient.Flush();

            // flush is not blocking so wait a bit
            Task.Delay(5000).Wait();

        }

        static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
        {
            var module = new DependencyTrackingTelemetryModule();

            // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

            // enable known dependency tracking, note that in future versions, we will extend this list. 
            // please check default settings in https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## <a name="next-steps"></a>Następne kroki
* [Monitorowanie zależności,](../../azure-monitor/app/asp-net-dependencies.md) aby sprawdzić, czy REST, SQL lub inne zasoby zewnętrzne spowalniają.
* [Użyj interfejsu API,](../../azure-monitor/app/api-custom-events-metrics.md) aby wysłać własne zdarzenia i metryki, aby uzyskać bardziej szczegółowy widok wydajności i użycia aplikacji.
