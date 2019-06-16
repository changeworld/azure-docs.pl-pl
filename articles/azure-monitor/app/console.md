---
title: Usługi Azure Application Insights dla aplikacji konsoli | Dokumentacja firmy Microsoft
description: Monitorowanie aplikacji sieci web dla dostępności, wydajności i użycia.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/30/2019
ms.reviewer: lmolkova
ms.author: mbullwin
ms.openlocfilehash: 602cd9696271931babad9aa962638c5b646c80ac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60901853"
---
# <a name="application-insights-for-net-console-applications"></a>Aplikacje konsoli usługi Application Insights dla platformy .NET
[Usługa Application Insights](../../azure-monitor/app/app-insights-overview.md) umożliwia monitorowanie aplikacji sieci web dla dostępności, wydajności i użycia.

Potrzebna jest subskrypcja z [Microsoft Azure](https://azure.com). Zaloguj się przy użyciu konta Microsoft, które mogą mieć dla Windows, usługi Xbox Live lub innych usług w chmurze firmy Microsoft. Twój zespół może ma subskrypcję organizacyjną na platformie Azure: Poproś właściciela o dodanie Cię do niego przy użyciu konta Microsoft.

## <a name="getting-started"></a>Wprowadzenie

* W witrynie [Azure Portal](https://portal.azure.com) [utwórz zasób usługi Application Insights](../../azure-monitor/app/create-new-resource.md ). Typ aplikacji wybierz opcję **ogólne**.
* Wykonaj kopię klucza instrumentacji. Znajdź klucz w **Essentials** listy rozwijanej nowy zasób został utworzony. 
* Zainstaluj najnowsze [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) pakietu.
* Ustaw klucz Instrumentacji w kodzie przed śledzenia żadnych danych telemetrycznych (lub zmiennej środowiskowej APPINSIGHTS_INSTRUMENTATIONKEY zestawu). Po tym można ręcznie śledzić dane telemetryczne i zobaczyć ją w witrynie Azure portal

```csharp
TelemetryConfiguration.Active.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient();
telemetryClient.TrackTrace("Hello World!");
```

* Zainstaluj najnowszą wersję [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) pakiet — automatycznie śledzi HTTP, SQL lub inne wywołania zależności zewnętrznych.

Możesz zainicjować i skonfigurować usługę Application Insights z kodu lub przy użyciu `ApplicationInsights.config` pliku. Upewnij się, że Inicjalizacja się dzieje w możliwie jak najszybciej. 

> [!NOTE]
> Instrukcje odwołujące się do **plik ApplicationInsights.config** dotyczą tylko aplikacji, które są zgodne z .NET Framework i nie dotyczą aplikacji .NET Core.

### <a name="using-config-file"></a>Przy użyciu pliku konfiguracji

Domyślnie zestaw SDK usługi Application Insights szuka `ApplicationInsights.config` pliku w katalogu roboczym po `TelemetryConfiguration` jest tworzona

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

Można też określić ścieżkę do pliku konfiguracji.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

Aby uzyskać więcej informacji, zobacz [dokumentacja pliku konfiguracji](configuration-with-applicationinsights-config.md).

Pełny przykładowy plik konfiguracji może wystąpić po zainstalowaniu najnowszej wersji [Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) pakietu. Oto **minimalny** konfiguracji dla kolekcji zależności, który jest odpowiednikiem w przykładzie kodu.

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

### <a name="configuring-telemetry-collection-from-code"></a>Konfigurowanie zbierania danych telemetrycznych z kodu

* Podczas uruchamiania aplikacji należy utworzyć i skonfigurować `DependencyTrackingTelemetryModule` wystąpienia — musi być pojedyncze i muszą zostać zachowane na okres istnienia aplikacji.

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

* Dodaj wspólne inicjatory telemetrii

```csharp
// stamps telemetry with correlation identifiers
TelemetryConfiguration.Active.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());

// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
TelemetryConfiguration.Active.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

* Dla aplikacji .NET Framework Windows może również zainstalować i zainicjować modułów zbierających dane licznika wydajności, zgodnie z opisem [tutaj](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)

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
            TelemetryConfiguration configuration = TelemetryConfiguration.Active;

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient();
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
            // please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## <a name="next-steps"></a>Kolejne kroki
* [Monitoruj zależności](../../azure-monitor/app/asp-net-dependencies.md) aby zobaczyć, jeśli REST, programu SQL lub inne zasoby zewnętrzne nie spowalniają.
* [Za pomocą interfejsu API](../../azure-monitor/app/api-custom-events-metrics.md) do wysłania własnych zdarzeń i metryk, aby uzyskać bardziej szczegółowy widok wydajności i użycia Twojej aplikacji.
