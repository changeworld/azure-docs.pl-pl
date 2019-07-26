---
title: Włączanie Snapshot Debugger dla aplikacji .NET w usłudze Azure Service Fabric, w chmurze i Virtual Machines | Microsoft Docs
description: Włączanie Snapshot Debugger dla aplikacji .NET w usłudze Azure Service Fabric, w chmurze i Virtual Machines
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: bfung
ms.openlocfilehash: 5a6cf763ae16b55806df2acaf2e03fd8c13d1e76
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359276"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>Włączanie Snapshot Debugger dla aplikacji .NET w usłudze Azure Service Fabric, w chmurze i Virtual Machines

Jeśli aplikacja ASP.NET lub ASP.NET Core działa w Azure App Service, zdecydowanie zaleca się [włączenie Snapshot Debugger za pomocą strony portalu Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json). Jeśli jednak aplikacja wymaga dostosowanej konfiguracji Snapshot Debugger lub wersji zapoznawczej platformy .NET Core, należy postępować ***zgodnie*** z instrukcjami dotyczącymi [włączania za pomocą portalu Application Insights Strona](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json).

Jeśli aplikacja działa na platformie Azure Service Fabric, usłudze w chmurze, Virtual Machines lub maszynach lokalnych, należy użyć poniższych instrukcji. 
    
## <a name="configure-snapshot-collection-for-aspnet-applications"></a>Konfigurowanie zbierania migawek dla aplikacji ASP.NET

1. [Włącz usługę Application Insights w aplikacji sieci web](../../azure-monitor/app/asp-net.md), jeśli nie została ona jeszcze zrobione.

2. Obejmują [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pakietu NuGet w swojej aplikacji.

3. W razie potrzeby dostosowana konfiguracja Snapshot Debugger dodana do [pliku ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Domyślna konfiguracja Snapshot Debugger jest w większości puste i wszystkie ustawienia są opcjonalne. Oto przykład przedstawiający konfigurację równoważną konfiguracji domyślnej:

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

4. Migawki są zbierane tylko na wyjątki, które są zgłaszane do usługi Application Insights. W niektórych przypadkach (na przykład starsze wersje platformy .NET), konieczne może być [Konfigurowanie zbierania wyjątków](../../azure-monitor/app/asp-net-exceptions.md#exceptions) aby zobaczyć wyjątki, przy użyciu migawek w portalu.


## <a name="configure-snapshot-collection-for-applications-using-aspnet-core-20-or-above"></a>Konfigurowanie kolekcji migawek dla aplikacji przy użyciu ASP.NET Core 2,0 lub nowszej

1. [Włącz usługę Application Insights w aplikacji sieci web platformy ASP.NET Core](../../azure-monitor/app/asp-net-core.md), jeśli nie została ona jeszcze zrobione.

    > [!NOTE]
    > Należy się upewnić, że aplikacja odwołuje się do wersji 2.1.1 lub nowszego, pakietu Microsoft.ApplicationInsights.AspNetCore.

2. Obejmują [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pakietu NuGet w swojej aplikacji.

3. Modyfikowanie aplikacji `Startup` klasy można dodawać i konfigurować modułu zbierającego migawki danych telemetrycznych procesora.
    1. Jeśli użyto pakietu NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) w wersji 1.3.5 lub nowszej, Dodaj następujące instrukcje using do `Startup.cs`.

       ```csharp
            using Microsoft.ApplicationInsights.SnapshotCollector;
       ```

       Dodaj następujące na końcu metody ConfigureServices w `Startup` klasie w. `Startup.cs`

       ```csharp
            services.AddSnapshotCollector((configuration) => Configuration.Bind(nameof(SnapshotCollectorConfiguration), configuration));
       ```
    2. Jeśli użyto pakietu NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) w wersji 1.3.4 lub starszej, Dodaj następujące instrukcje using do `Startup.cs`.

       ```csharp
       using Microsoft.ApplicationInsights.SnapshotCollector;
       using Microsoft.Extensions.Options;
       using Microsoft.ApplicationInsights.AspNetCore;
       using Microsoft.ApplicationInsights.Extensibility;
       ```
    
       Dodaj następujący kod `SnapshotCollectorTelemetryProcessorFactory` klasy `Startup` klasy.
    
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
        Dodaj `SnapshotCollectorConfiguration` i `SnapshotCollectorTelemetryProcessorFactory` usług do uruchomienia potoku:
    
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

4. W razie potrzeby dostosuj konfigurację Snapshot Debugger, dodając sekcję SnapshotCollectorConfiguration do pliku appSettings. JSON. Wszystkie ustawienia w konfiguracji Snapshot Debugger są opcjonalne. Oto przykład przedstawiający konfigurację równoważną konfiguracji domyślnej:

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

## <a name="configure-snapshot-collection-for-other-net-applications"></a>Konfigurowanie zbierania migawek dla innych aplikacji .NET

1. Jeśli aplikacja nie jest już za pomocą usługi Application Insights, zacznij od [włączenie usługi Application Insights i ustawić dla klucza Instrumentacji](../../azure-monitor/app/windows-desktop.md).

2. Dodaj [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pakietu NuGet w swojej aplikacji.

3. Migawki są zbierane tylko na wyjątki, które są zgłaszane do usługi Application Insights. Może być konieczne zmodyfikowanie kodu na zgłoszenie ich. Kodu obsługi wyjątków zależy od struktury aplikacji, ale przykład znajduje się poniżej:
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

## <a name="next-steps"></a>Kolejne kroki

- Generuj ruch do aplikacji, która może wyzwolić wyjątek. Następnie odczekaj od 10 do 15 minut na wysłanie migawek do wystąpienia Application Insights.
- Zobacz [migawki](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) w Azure Portal.
- Aby uzyskać pomoc dotyczącą rozwiązywania problemów Snapshot Debugger, zobacz [Snapshot Debugger Rozwiązywanie problemów](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).
