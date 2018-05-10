---
title: Azure Application Insights dla aplikacji konsoli | Dokumentacja firmy Microsoft
description: Monitorowanie aplikacji sieci web dla dostępności, wydajności i użycia.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: lmolkova; mbullwin
ms.openlocfilehash: c0bbb8b7c2f1e4a7609fde1f8c1487794d3d5897
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="application-insights-for-net-console-applications"></a>Aplikacje konsoli usługi Application Insights dla platformy .NET
[Usługa Application Insights](app-insights-overview.md) umożliwia monitorowanie aplikacji sieci web, dostępności, wydajności i użycia.

Konieczna jest subskrypcja z [Microsoft Azure](http://azure.com). Zaloguj się przy użyciu konta Microsoft, które mogą mieć dla systemu Windows, Xbox Live lub innych usług chmurowych firmy Microsoft. Zespół może być organizacyjnej subskrypcji Azure: Poproś właściciela o należy dodać do niej przy użyciu konta Microsoft.

## <a name="getting-started"></a>Wprowadzenie

* W witrynie [Azure Portal](https://portal.azure.com) [utwórz zasób usługi Application Insights](app-insights-create-new-resource.md). Typ aplikacji wybierz opcję **ogólne**.
* Wykonaj kopię klucza instrumentacji. Znajdź klucz w **Essentials** listy rozwijanej nowy zasób został utworzony. 
* Zainstaluj najnowsze [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) pakietu.
* Ustaw klucz Instrumentacji w kodzie przed wszystkie dane telemetryczne śledzenia (lub ustaw dla zmiennej środowiskowej APPINSIGHTS_INSTRUMENTATIONKEY). Po wykonaniu tej powinno być możliwe do ręcznego śledzenia telemetrii i jest widoczny w portalu Azure

```csharp
TelemetryConfiguration.Active.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient();
telemetryClient.TrackTrace("Hello World!");
```

* Zainstaluj najnowszą wersję pakietu [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) package — automatyczne śledzenie HTTP, SQL lub inne wywołania zależności zewnętrznych.

Możesz zainicjować i Konfiguruj usługę Application Insights z kodu lub przy użyciu `ApplicationInsights.config` pliku. Upewnij się, że inicjowania się stanie w możliwie jak najszybciej. 

> [!NOTE]
> Instrukcje dotyczące **ApplicationInsights.config** dotyczą tylko aplikacji przeznaczonych dla platformy .NET Standard, które nie dotyczą aplikacji .NET Core. 

### <a name="using-config-file"></a>Przy użyciu pliku konfiguracji

Domyślnie zestaw SDK usługi Application Insights szuka `ApplicationInsights.config` pliku w katalogu roboczym podczas `TelemetryConfiguration` jest tworzony

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Read ApplicationInsights.config file if present
```

Może również określić ścieżkę do pliku konfiguracji.

```csharp
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration("ApplicationInsights.config");
```

Aby uzyskać więcej informacji, zobacz [odwołania do pliku konfiguracji](app-insights-configuration-with-applicationinsights-config.md).

Pełny przykład pliku konfiguracyjnego może zostać przez zainstalowanie najnowszej wersji [Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) pakietu. Oto **minimalnego** konfiguracji dla kolekcji zależności, która jest odpowiednikiem przykładów kodu.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
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

* Podczas uruchamiania aplikacji należy utworzyć i skonfigurować `DependencyTrackingTelemetryModule` wystąpienie — musi być pojedynczą i muszą zostać zachowane czas ich istnienia aplikacji.

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/NuGet/ApplicationInsights.config.install.xdt#L20
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

* W przypadku aplikacji .NET Framework w systemie Windows może również zainstalować i zainicjować modułów zbierających dane licznika wydajności, zgodnie z opisem [tutaj](http://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)

#### <a name="full-example"></a>Pełny przykład

```csharp
static void Main(string[] args)
{
    TelemetryConfiguration configuration = TelemetryConfiguration.Active;

    configuration.InstrumentationKey = "removed";
    configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
    configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

    var telemetryClient = new TelemetryClient();
    using (IntitializeDependencyTracking(configuration))
    {
        telemetryClient.TrackTrace("Hello World!");

        using (var httpClient = new HttpClient())
        {
            // Http dependency is automatically tracked!
            httpClient.GetAsync("https://microsoft.com").Wait();
        }
    }

    // run app...

    // when application stops or you are done with dependency tracking, do not forget to dispose the module
    dependencyTrackingModule.Dispose();

    telemetryClient.Flush();
}

static DependencyTrackingTelemetryModule IntitializeDependencyTracking(TelemetryConfiguration configuration)
{
    // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");    
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

    // enable known dependency tracking, note that in future versions, we will extend this list. 
    // please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/NuGet/ApplicationInsights.config.install.xdt#L20
    module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
    module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

    // initialize the module
    module.Initialize(configuration);

    return module;
}
```

## <a name="next-steps"></a>Kolejne kroki
* [Monitor zależności](app-insights-asp-net-dependencies.md) aby zobaczyć, jeśli REST, SQL i innych zasobów zewnętrznych są spowolnieniem użytkownik.
* [Za pomocą interfejsu API](app-insights-api-custom-events-metrics.md) wysyłanie własnych zdarzenia i metryki dla bardziej szczegółowy widok wydajności i użycia aplikacji.
