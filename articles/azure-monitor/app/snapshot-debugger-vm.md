---
title: Włącz debuger migawek dla aplikacji platformy .NET w sieci szkieletowej usług Azure, usłudze w chmurze i maszynach wirtualnych | Dokumenty firmy Microsoft
description: Włącz debuger migawek dla aplikacji platformy .NET w sieci szkieletowej usług Azure, usłudze w chmurze i maszynach wirtualnych
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 194a2da23c8fb405c492df8f6ee173cc97fde4ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671348"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>Włącz debuger migawek dla aplikacji platformy .NET w sieci szkieletowej usług Azure, usłudze w chmurze i maszynach wirtualnych

Jeśli ASP.NET lub ASP.NET podstawowej aplikacji działa w usłudze Azure App Service, zdecydowanie zaleca się [włączenie debugera migawek za pośrednictwem strony portalu usługi Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json). Jeśli jednak aplikacja wymaga dostosowanej konfiguracji debugera migawek lub wersji zapoznawczej rdzenia .NET, należy wykonać tę instrukcję ***oprócz*** instrukcji [włączania za pośrednictwem strony portalu usługi Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json).

Jeśli aplikacja jest uruchamiana w sieci szkieletowej usługi Azure, usłudze w chmurze, maszynach wirtualnych lub komputerach lokalnych, należy użyć następujących instrukcji. 
    
## <a name="configure-snapshot-collection-for-aspnet-applications"></a>Konfigurowanie kolekcji migawek dla aplikacji ASP.NET

1. [Włącz usługę Application Insights w aplikacji sieci Web](../../azure-monitor/app/asp-net.md), jeśli jeszcze tego nie zrobiono.

2. Dołącz [pakiet Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet w aplikacji.

3. W razie potrzeby dostosowano konfigurację debugera migawek dodaną do [pliku ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Domyślna konfiguracja debugera migawki jest w większości pusta, a wszystkie ustawienia są opcjonalne. Oto przykład przedstawiający konfigurację równoważną domyślnej konfiguracji:

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>3</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Migawki są zbierane tylko w wyjątkach, które są zgłaszane do usługi Application Insights. W niektórych przypadkach (na przykład starsze wersje platformy .NET), może być konieczne [skonfigurowanie kolekcji wyjątków,](../../azure-monitor/app/asp-net-exceptions.md#exceptions) aby wyświetlić wyjątki z migawkami w portalu.


## <a name="configure-snapshot-collection-for-applications-using-aspnet-core-20-or-above"></a>Konfigurowanie kolekcji migawek dla aplikacji korzystających z ASP.NET Core 2.0 lub wyższej

1. [Włącz usługę Application Insights w aplikacji sieci web ASP.NET Core](../../azure-monitor/app/asp-net-core.md), jeśli jeszcze tego nie zrobiono.

    > [!NOTE]
    > Upewnij się, że aplikacja odwołuje się do wersji 2.1.1 lub nowszej pakietu Microsoft.ApplicationInsights.AspNetCore.

2. Dołącz [pakiet Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet w aplikacji.

3. Zmodyfikuj `Startup` klasę aplikacji, aby dodać i skonfigurować procesor telemetrii modułu zbierającego migawki.
    1. Jeśli jest używany pakiet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet w wersji 1.3.5 `Startup.cs`lub wyższej, dodaj następujące instrukcje do .

       ```csharp
            using Microsoft.ApplicationInsights.SnapshotCollector;
       ```

       Dodaj następujące na końcu ConfigureServices metody `Startup` w `Startup.cs`klasie w .

       ```csharp
            services.AddSnapshotCollector((configuration) => Configuration.Bind(nameof(SnapshotCollectorConfiguration), configuration));
       ```
    2. Jeśli jest używany pakiet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet w wersji 1.3.4 `Startup.cs`lub poniżej, dodaj następujące instrukcje do .

       ```csharp
       using Microsoft.ApplicationInsights.SnapshotCollector;
       using Microsoft.Extensions.Options;
       using Microsoft.ApplicationInsights.AspNetCore;
       using Microsoft.ApplicationInsights.Extensibility;
       ```
    
       Dodaj następującą `SnapshotCollectorTelemetryProcessorFactory` `Startup` klasę do klasy.
    
       ```csharp
       class Startup
       {
           private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
           {
               private readonly IServiceProvider _serviceProvider;
    
               public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
                   _serviceProvider = serviceProvider;
    
               public ITelemetryProcessor Create(ITelemetryProcessor next)
               {
                   var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
                   return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
               }
           }
           ...
        ```
        Dodaj `SnapshotCollectorConfiguration` i `SnapshotCollectorTelemetryProcessorFactory` usług do potoku uruchamiania:
    
        ```csharp
           // This method gets called by the runtime. Use this method to add services to the container.
           public void ConfigureServices(IServiceCollection services)
           {
               // Configure SnapshotCollector from application settings
               services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));
    
               // Add SnapshotCollector telemetry processor.
               services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));
    
               // TODO: Add other services your application needs here.
           }
       }
       ```

4. W razie potrzeby dostosowano konfigurację debugera migawek, dodając sekcję SnapshotCollectorConfiguration do pliku appsettings.json. Wszystkie ustawienia w konfiguracji debugera migawki są opcjonalne. Oto przykład przedstawiający konfigurację równoważną domyślnej konfiguracji:

   ```json
   {
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

## <a name="configure-snapshot-collection-for-other-net-applications"></a>Konfigurowanie kolekcji migawek dla innych aplikacji platformy .NET

1. Jeśli aplikacja nie jest jeszcze instrumentowana za pomocą usługi Application Insights, rozpocznij pracę, [włączając aplikację Application Insights i ustawiając klucz instrumentacji.](../../azure-monitor/app/windows-desktop.md)

2. Dodaj pakiet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet w aplikacji.

3. Migawki są zbierane tylko w wyjątkach, które są zgłaszane do usługi Application Insights. Może być konieczne zmodyfikowanie kodu, aby je zgłosić. Kod obsługi wyjątków zależy od struktury aplikacji, ale przykład znajduje się poniżej:
    ```csharp
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```

## <a name="next-steps"></a>Następne kroki

- Generowanie ruchu do aplikacji, która może wyzwolić wyjątek. Następnie poczekaj od 10 do 15 minut, aby migawki zostały wysłane do wystąpienia usługi Application Insights.
- Zobacz [migawki](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) w witrynie Azure portal.
- Aby uzyskać pomoc dotyczącą rozwiązywania problemów z debugerem migawek, zobacz [Rozwiązywanie problemów z debugerem migawek](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).
