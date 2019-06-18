---
title: Zbieranie dotyczących liczników wydajności w usługach Azure Cloud Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak odnajdywanie, użycia i tworzenie liczników wydajności w usługach w chmurze za pomocą usługi Azure Diagnostics i Application Insights.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: ''
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2018
ms.author: jeconnoc
ms.openlocfilehash: 68101be211335d51eb4bf99361ea36b73fa19218
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60653743"
---
# <a name="collect-performance-counters-for-your-azure-cloud-service"></a>Liczniki wydajności są zbierane dla usługi w chmurze platformy Azure

Liczniki wydajności umożliwiają można śledzić, jak również wykonywanie i hosta aplikacji. System Windows Server udostępnia wiele różnych liczników wydajności związanych z sprzętu, aplikacje, system operacyjny i. Zbieranie i wysyłanie liczników wydajności na platformie Azure, można analizować te informacje ułatwiające podejmowanie lepszych decyzji. 

## <a name="discover-available-counters"></a>Odkryj dostępne liczniki

Licznik wydajności składa się z dwóch części: Nazwa zestawu (kategoria) oraz co najmniej jeden licznik. Aby uzyskać listę dostępnych liczników wydajności, można użyć programu PowerShell:

```powershell
Get-Counter -ListSet * | Select-Object CounterSetName, Paths | Sort-Object CounterSetName

CounterSetName                                  Paths
--------------                                  -----
.NET CLR Data                                   {\.NET CLR Data(*)\SqlClient...
.NET CLR Exceptions                             {\.NET CLR Exceptions(*)\# o...
.NET CLR Interop                                {\.NET CLR Interop(*)\# of C...
.NET CLR Jit                                    {\.NET CLR Jit(*)\# of Metho...
.NET Data Provider for Oracle                   {\.NET Data Provider for Ora...
.NET Data Provider for SqlServer                {\.NET Data Provider for Sql...
.NET Memory Cache 4.0                           {\.NET Memory Cache 4.0(*)\C...
AppV Client Streamed Data Percentage            {\AppV Client Streamed Data ...
ASP.NET                                         {\ASP.NET\Application Restar...
ASP.NET Apps v4.0.30319                         {\ASP.NET Apps v4.0.30319(*)...
ASP.NET State Service                           {\ASP.NET State Service\Stat...
ASP.NET v2.0.50727                              {\ASP.NET v2.0.50727\Applica...
ASP.NET v4.0.30319                              {\ASP.NET v4.0.30319\Applica...
Authorization Manager Applications              {\Authorization Manager Appl...

#... results cut to save space ...
```

`CounterSetName` Właściwość reprezentuje zestaw (lub kategorii) i jest to dobry wskaźnik co to są powiązane z liczników wydajności. `Paths` Właściwość reprezentuje kolekcję liczników dla zestawu. Można również pobrać `Description` właściwości, aby uzyskać więcej informacji na temat zbiór liczników.

Aby uzyskać wszystkie liczniki dla zestawu, należy użyć `CounterSetName` wartości, a następnie rozwiń węzeł `Paths` kolekcji. Każdy element ścieżki jest liczników, które można wykonać zapytanie. Na przykład, aby uzyskać dostępne liczniki związane z `Processor` zestawu, rozwiń węzeł `Paths` kolekcji:

```powershell
Get-Counter -ListSet * | Where-Object CounterSetName -eq "Processor" | Select -ExpandProperty Paths

\Processor(*)\% Processor Time
\Processor(*)\% User Time
\Processor(*)\% Privileged Time
\Processor(*)\Interrupts/sec
\Processor(*)\% DPC Time
\Processor(*)\% Interrupt Time
\Processor(*)\DPCs Queued/sec
\Processor(*)\DPC Rate
\Processor(*)\% Idle Time
\Processor(*)\% C1 Time
\Processor(*)\% C2 Time
\Processor(*)\% C3 Time
\Processor(*)\C1 Transitions/sec
\Processor(*)\C2 Transitions/sec
\Processor(*)\C3 Transitions/sec
```

Używa tych poszczególnych liczników, które ścieżki mogą być dodawane do struktura diagnostyki usługi w chmurze. Aby uzyskać więcej informacji na temat sposobu ścieżkę licznika wydajności jest konstruowany zobacz [określania ścieżki licznika](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85)).

## <a name="collect-a-performance-counter"></a>Zbieranie danych licznika wydajności

Licznik wydajności można dodać do swojej usługi w chmurze dla usługi Azure Diagnostics i Application Insights.

### <a name="application-insights"></a>Application Insights

Usługa Azure Application Insights dla usług w chmurze pozwala określić liczniki wydajności, które mają być zbierane. Po zakończeniu [Dodaj usługę Application Insights do projektu](../azure-monitor/app/cloudservices.md#sdk), plik konfiguracji o nazwie **plik ApplicationInsights.config** zostanie dodany do projektu programu Visual Studio. Ten plik konfiguracji określa, jakiego rodzaju informacje Application Insights zbiera i wysyła do usługi Azure.

Otwórz **plik ApplicationInsights.config** plików i Znajdź **ApplicationInsights** > **TelemetryModules** elementu. Każdy `<Add>` element podrzędny określa typ telemetrii, aby zebrać, wraz z jego konfiguracji. Jest to typ modułu telemetrii licznika wydajności `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector`. Jeśli ten element jest już zdefiniowany, nie należy dodawać go po raz drugi. Każdego licznika wydajności do zbierania jest zdefiniowany w węźle, nazwane `<Counters>`. Oto przykład, który zbiera dane liczników wydajności dysku:

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Write Bytes/sec" ReportAs="Disk write (C:)" />
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Read Bytes/sec" ReportAs="Disk read (C:)" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

Każdego licznika wydajności jest reprezentowany jako `<Add>` elementu w obszarze `<Counters>`. `PerformanceCounter` Atrybut definiuje licznik wydajności, który do zbierania. `ReportAs` Atrybut jest tytuł wyświetlany w witrynie Azure portal dla licznika wydajności. Wszelkie licznika wydajności są zbierane są umieszczane w kategorii o nazwie **niestandardowe** w portalu. W przeciwieństwie do usługi Azure Diagnostics nie można ustawić interwał, te liczniki wydajności są zbierane i wysyłane do platformy Azure. Za pomocą usługi Application Insights liczniki wydajności są zbierane i wysyłane co minutę. 

Usługa Application Insights automatycznie zbiera następujące liczniki wydajności:

* \Process(??. APP_WIN32_PROC??)\% Czas procesora
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Aby uzyskać więcej informacji, zobacz [liczników wydajności systemu w usłudze Application Insights](../azure-monitor/app/performance-counters.md) i [usługi Application Insights dla usług Azure Cloud Services](../azure-monitor/app/cloudservices.md#performance-counters).

### <a name="azure-diagnostics"></a>Diagnostyka Azure

> [!IMPORTANT]
> Gdy te dane są agregowane na konto magazynu, portalu jest **nie** umożliwiają natywnych danych wykresu. Zdecydowanie zaleca się integracji innej usługi Diagnostyka, takie jak usługi Application Insights do aplikacji.

Rozszerzenie diagnostyki platformy Azure dla usług w chmurze pozwala określić liczniki wydajności, które mają być zbierane. Aby skonfigurować diagnostykę platformy Azure, zobacz [omówienie monitorowania usługi chmury](cloud-services-how-to-monitor.md#setup-diagnostics-extension).

Liczniki wydajności, które mają być zbierane są zdefiniowane w **diagnostics.wadcfgx** pliku. Otwórz ten plik (jest on zdefiniowany poszczególnych ról) w programie Visual Studio i Znajdź **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg**  >  **DiagnosticMonitorConfiguration** > **liczniki wydajności** elementu. Dodaj nową **PerformanceCounterConfiguration** element jako element podrzędny. Ten element zawiera dwa atrybuty: `counterSpecifier` i `sampleRate`. `counterSpecifier` Atrybut definiuje wydajności systemu, które można zbierać zestawów liczników (schemat w poprzedniej sekcji). `sampleRate` Wartość wskazuje, jak często jest sondowany tę wartość. Jako całości, wszystkie liczniki wydajności są przesyłane na platformę Azure zgodnie z nadrzędnego `PerformanceCounters` elementu `scheduledTransferPeriod` wartość atrybutu.

Aby uzyskać więcej informacji na temat `PerformanceCounters` elementu schematu, zobacz [schemat usługi Diagnostyka Azure](../azure-monitor/platform/diagnostics-extension-schema-1dot3.md#performancecounters-element).

Okres zdefiniowany przez `sampleRate` używa atrybutu, typ danych czasu trwania XML, aby wskazać, jak często jest sondowany licznika wydajności. W poniższym przykładzie ustawiono częstotliwość `PT3M`, co oznacza, że `[P]eriod[T]ime[3][M]inutes`: co trzy minuty.

Aby uzyskać więcej informacji o tym, jak `sampleRate` i `scheduledTransferPeriod` są zdefiniowane, zobacz **typu danych czasu trwania** sekcji [W3 XML daty i czasu typów daty](https://www.w3schools.com/XML/schema_dtypes_date.asp) samouczka.

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />

          <!-- This is a new perf counter which will track the C: disk read activity in bytes per second, every minute -->
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(C:)\Disk Read Bytes/sec" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="create-a-new-perf-counter"></a>Utwórz nowy licznik wydajności

Nowy licznik wydajności można tworzyć i używane przez kod. Swój kod, który tworzy nowy licznik wydajności musi być uruchomiona z podwyższonym poziomem uprawnień, w przeciwnym razie zakończy się niepowodzeniem. Usługi w chmurze `OnStart` uruchamiania kodu można utworzyć licznika wydajności, wymagając od użytkownika do uruchamiania roli z podniesionymi uprawnieniami. Lub można utworzyć zadania uruchamiania, który jest uruchamiany z podwyższonym poziomem uprawnień i tworzy licznika wydajności. Aby uzyskać więcej informacji na temat uruchamiania zadań, zobacz [sposób konfigurowania i uruchamiania zadania uruchamiania dla usługi w chmurze](cloud-services-startup-tasks.md).

Aby skonfigurować swoje rolę do uruchamiania z podwyższonym poziomem uprawnień, Dodaj `<Runtime>` elementu [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) pliku.

```xml
<ServiceDefinition name="CloudServiceLoadTesting" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Large">

    <!-- ... cut to save space ... -->

    <Runtime executionContext="elevated">
      
    </Runtime>

    <!-- ... cut to save space ... -->

  </WorkerRole>
</ServiceDefinition>
```

Można utworzyć i zarejestrować nowy licznik wydajności przy użyciu kilku wierszy kodu. Użyj `System.Diagnostics.PerformanceCounterCategory.Create` przeciążenia metody, które tworzy zarówno kategorii i wartość licznika. Poniższy kod najpierw sprawdza, jeśli kategoria istnieje, a jeśli brakuje, tworzy kategorii i wartość licznika.

```csharp
using System.Diagnostics;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRoleWithSBQueue1
{
    public class WorkerRole : RoleEntryPoint
    {
        // Perf counter variable representing times service was used.
        private PerformanceCounter counterServiceUsed;

        public override bool OnStart()
        {
            // ... Other startup code here ...

            // Define the category and counter names.
            string perfCounterCatName = "MyService";
            string perfCounterName = "Times Used";

            // Create the counter if needed. Our counter category only has a single counter.
            // Both the category and counter are created in the same method call.
            if (!PerformanceCounterCategory.Exists(perfCounterCatName))
            {
                PerformanceCounterCategory.Create(perfCounterCatName, "Collects information about the cloud service.", 
                                                  PerformanceCounterCategoryType.SingleInstance, 
                                                  perfCounterName, "How many times the cloud service was used.");
            }

            // Get reference to our counter
            counterServiceUsed = new PerformanceCounter(perfCounterCatName, perfCounterName);
            counterServiceUsed.ReadOnly = false;
            
            return base.OnStart();
        }

        // ... cut class code to save space
    }
}
```

Używanie licznika, należy wywołać `Increment` lub `IncrementBy` metody.

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

Teraz, że aplikacja korzysta z Twojego liczników niestandardowych, należy skonfigurować diagnostyki platformy Azure lub usługi Application Insights, aby śledzić licznika.


### <a name="application-insights"></a>Application Insights

Jak wcześniej wspomniano, liczniki wydajności dla usługi Application Insights są zdefiniowane w **plik ApplicationInsights.config** pliku. Otwórz **plik ApplicationInsights.config** i Znajdź **ApplicationInsights** > **TelemetryModules** > **Dodaj**  >  **Liczniki** elementu. Tworzenie `<Add>` element podrzędny i ustaw `PerformanceCounter` atrybutu kategorii i nazwę licznika wydajności, utworzone w kodzie. Ustaw `ReportAs` atrybutu przyjazną nazwę, aby wyświetlić w portalu.

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <!-- ... cut other perf counters to save space ... -->

        <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
        <Add PerformanceCounter="\MyService\Times Used" ReportAs="Service used counter" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

### <a name="azure-diagnostics"></a>Diagnostyka Azure

Jak podano wcześniej, liczniki wydajności, które mają być zbierane są zdefiniowane w **diagnostics.wadcfgx** pliku. Otwórz ten plik (jest on zdefiniowany poszczególnych ról) w programie Visual Studio i Znajdź **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg**  >  **DiagnosticMonitorConfiguration** > **liczniki wydajności** elementu. Dodaj nową **PerformanceCounterConfiguration** element jako element podrzędny. Ustaw `counterSpecifier` atrybutu kategorii i nazwę licznika wydajności, utworzone w kodzie. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <!-- ... cut other perf counters to save space ... -->
          
          <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
          <PerformanceCounterConfiguration counterSpecifier="\MyService\Times Used" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="more-information"></a>Więcej informacji

- [Usługa Application Insights dla usług Azure Cloud Services](../azure-monitor/app/cloudservices.md#performance-counters)
- [Liczniki wydajności systemu w usłudze Application Insights](../azure-monitor/app/performance-counters.md)
- [Określenie ścieżki licznika](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85))
- [Schemat usługi Diagnostyka Azure - liczniki wydajności](../azure-monitor/platform/diagnostics-extension-schema-1dot3.md#performancecounters-element)
