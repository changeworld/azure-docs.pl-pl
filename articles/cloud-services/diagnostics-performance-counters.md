---
title: Zbieraj na liczniki wydajności usług w chmurze platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak odnajdywanie, używanie i utworzyć liczniki wydajności usług w chmurze z diagnostyki Azure i usługi Application Insights.
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: ''
ms.assetid: ''
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/18
ms.author: adegeo
ms.openlocfilehash: 3e0af48c172fa912f0ac9e05b7b761dd7eaad795
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
ms.locfileid: "29134351"
---
# <a name="collect-performance-counters-for-your-azure-cloud-service"></a>Zebrać liczników wydajności dla usługi w chmurze Azure

Liczniki wydajności umożliwiają można śledzić, jak działają hosta i aplikacji. Windows Server zawiera wiele liczników wydajności różnych dotyczące sprzętu, aplikacje, system operacyjny i. Zbieranie i wysyłanie liczników wydajności na platformie Azure, można analizować te informacje ułatwiające podejmowanie lepszych decyzji. 

## <a name="discover-available-counters"></a>Odnajdywanie dostępne liczniki

Licznik wydajności składa się z dwóch części: Nazwa zestawu (kategoria) oraz co najmniej jeden licznik. Program PowerShell w celu uzyskania listy dostępnych liczników wydajności:

```PowerShell
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

`CounterSetName` Właściwość reprezentuje zestaw (lub kategoria) i jest dobry wskaźnik co liczniki wydajności są powiązane. `Paths` Właściwość reprezentuje zbiór liczników dla zestawu. Można również pobrać `Description` właściwości, aby uzyskać więcej informacji o zestawie liczników.

Aby uzyskać wszystkie liczniki dla zestawu, należy użyć `CounterSetName` wartość, a następnie rozwiń węzeł `Paths` kolekcji. Każdy element ścieżki jest licznik, który można wykonać zapytanie. Na przykład, aby uzyskać dostępne liczniki związane z `Processor` ustawić, rozwiń węzeł `Paths` kolekcji:

```PowerShell
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

Usługi w chmurze korzysta z tych poszczególnych liczników, które można dodać ścieżek do struktura diagnostyki. Aby uzyskać więcej informacji o sposobie ścieżkę licznika wydajności jest tworzony, zobacz [określania ścieżki licznika](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85)).

## <a name="collect-a-performance-counter"></a>Zbieraj dane licznika wydajności

Licznik wydajności można dodać do usługi w chmurze dla diagnostyki Azure lub usługi Application Insights.

### <a name="application-insights"></a>Application Insights

Azure Application Insights dla usługi w chmurze pozwala określić liczniki wydajności, które mają być zbierane. Po [dodać usługi Application Insights do projektu](../application-insights/app-insights-cloudservices.md#sdk), pliku o nazwie **ApplicationInsights.config** zostanie dodany do projektu programu Visual Studio. Ten plik konfiguracji określa typ informacji Application Insights zbiera i wysyła do usługi Azure.

Otwórz **ApplicationInsights.config** plików i Znajdź **ApplicationInsights** > **TelemetryModules** elementu. Każdy `<Add>` element podrzędny definiuje typ telemetrię, aby zebrać, oraz jej konfigurację. Typ modułu telemetrii licznika wydajności jest `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector`. Jeśli ten element jest już zdefiniowana, nie należy dodawać go po raz drugi. Każdego licznika wydajności, aby zbierać jest zdefiniowany w obszarze węzła o nazwie `<Counters>`. Oto przykład, który zbiera dane liczników wydajności dysku:

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

Każdego licznika wydajności jest reprezentowany jako `<Add>` elementu w obszarze `<Counters>`. `PerformanceCounter` Atrybut Określa licznik wydajności, który do zbierania. `ReportAs` Atrybut jest tytuł, aby wyświetlić w portalu Azure dotyczące licznika wydajności. Wszelkie licznika wydajności zbierane są umieszczane w kategorię o nazwie **niestandardowy** w portalu. W przeciwieństwie do diagnostyki Azure nie można ustawić interwał, te liczniki wydajności są zbierane i wysyłane do usługi Azure. Z usługi Application Insights liczniki wydajności są zbierane i wysyłane co minutę. 

Usługa Application Insights automatycznie zbiera następujące liczniki wydajności:

* \Process(??. APP_WIN32_PROC??)\% Czas procesora
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Aby uzyskać więcej informacji, zobacz [liczników wydajności systemu w usłudze Application Insights](../application-insights/app-insights-performance-counters.md) i [usługi Application Insights dla usługi w chmurze Azure](../application-insights/app-insights-cloudservices.md#performance-counters).

### <a name="azure-diagnostics"></a>Diagnostyka Azure

> [!IMPORTANT]
> Podczas tych danych jest agregowana do konta magazynu, portalu jest **nie** umożliwiają natywnych danych wykresu. Zdecydowanie zaleca się integrowanie innego zadania diagnostyczne, takie jak usługi Application Insights do aplikacji.

Rozszerzenie diagnostyki Azure dla usługi w chmurze pozwala określić liczniki wydajności, które mają być zbierane. Aby skonfigurować diagnostyki Azure, zobacz [omówienie monitorowania usługi chmury](cloud-services-how-to-monitor.md#setup-diagnostics-extension).

Liczniki wydajności mają być zbierane są zdefiniowane w **diagnostics.wadcfgx** pliku. Otwórz ten plik (jest on zdefiniowany dla każdej roli) w programie Visual Studio i Znajdź **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg**  >  **DiagnosticMonitorConfiguration** > **liczniki wydajności** elementu. Dodaj nową **PerformanceCounterConfiguration** element jako element podrzędny. Ten element ma dwa atrybuty: `counterSpecifier` i `sampleRate`. `counterSpecifier` Atrybut definiuje wydajności systemu, które służąca do gromadzenia wartość licznika (obramowane w poprzedniej sekcji). `sampleRate` Wartość wskazuje, jak często jest sondowany tej wartości. Jako całości, wszystkie liczniki wydajności są przekazywane do platformy Azure zgodnie z nadrzędnego `PerformanceCounters` elementu `scheduledTransferPeriod` wartość atrybutu.

Aby uzyskać więcej informacji na temat `PerformanceCounters` elementu schematu, zobacz [schematu diagnostyki Azure](../monitoring-and-diagnostics/azure-diagnostics-schema-1dot3-and-later.md#performancecounters-element).

Okresu zdefiniowanego za pomocą `sampleRate` użycia atrybutu typu danych czas trwania XML, aby wskazać, jak często jest sondowany licznika wydajności. W poniższym przykładzie ustawiono częstotliwość `PT3M`, co oznacza, że `[P]eriod[T]ime[3][M]inutes`: co 3 minuty.

Aby uzyskać więcej informacji na temat sposobu `sampleRate` i `scheduledTransferPeriod` są zdefiniowane, zobacz **czas trwania — typ danych** sekcji [W3 XML daty i czasu Data typy](https://www.w3schools.com/XML/schema_dtypes_date.asp) samouczka.

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

Nowy licznik wydajności można tworzone i używane przez kod. Swój kod, który tworzy nowy licznik wydajności musi być uruchomiona z podwyższonym poziomem uprawnień, w przeciwnym razie zakończy się niepowodzeniem. Usługi w chmurze `OnStart` kod uruchomienia można utworzyć licznika wydajności, konieczności uruchamiania roli w kontekście z podwyższonym poziomem uprawnień. Lub można utworzyć zadanie uruchamiania, który działa z podwyższonym poziomem uprawnień i tworzy licznika wydajności. Aby uzyskać więcej informacji na temat uruchamiania zadań, zobacz [sposób konfigurowania i uruchamiania zadań uruchamiania dla usługi w chmurze](cloud-services-startup-tasks.md).

Aby skonfigurować roli użytkownika do uruchamiania z podwyższonym poziomem uprawnień, Dodaj `<Runtime>` elementu [csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) pliku.

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

Można utworzyć ani zarejestrować nowy licznik wydajności przy użyciu kilku wierszy kodu. Użyj `System.Diagnostics.PerformanceCounterCategory.Create` przeciążenia metody, które tworzy zarówno kategorii i licznik. Poniższy kod najpierw sprawdza, czy kategoria istnieje, a jeśli brak, tworzy zarówno kategorii i licznik.

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

            // Define the cateogry and counter names.
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

Teraz, gdy aplikacja korzysta z liczników niestandardowych, należy skonfigurować diagnostyki Azure lub usługi Application Insights, aby śledzić licznik.


### <a name="application-insights"></a>Application Insights

Jak wcześniej wspomniano, liczniki wydajności dla usługi Application Insights jest zdefiniowany w **ApplicationInsights.config** pliku. Otwórz **ApplicationInsights.config** i Znajdź **ApplicationInsights** > **TelemetryModules** > **Dodaj**  >  **Liczniki** elementu. Utwórz `<Add>` element podrzędny i zestaw `PerformanceCounter` atrybutu kategorii i nazwy licznika wydajności, utworzony w kodzie. Ustaw `ReportAs` atrybutu przyjazną nazwę, aby wyświetlić w portalu.

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

Jak podano wcześniej, liczniki wydajności mają być zbierane są zdefiniowane w **diagnostics.wadcfgx** pliku. Otwórz ten plik (jest on zdefiniowany dla każdej roli) w programie Visual Studio i Znajdź **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg**  >  **DiagnosticMonitorConfiguration** > **liczniki wydajności** elementu. Dodaj nową **PerformanceCounterConfiguration** element jako element podrzędny. Ustaw `counterSpecifier` atrybutu kategorii i nazwy licznika wydajności, utworzony w kodzie. 

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

- [Application Insights dla usługi w chmurze Azure](../application-insights/app-insights-cloudservices.md#performance-counters)
- [Liczniki wydajności systemu w usłudze Application Insights](../application-insights/app-insights-performance-counters.md)
- [Określanie ścieżki licznika](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85))
- [Diagnostyka Azure schematu - liczniki wydajności](../monitoring-and-diagnostics/azure-diagnostics-schema-1dot3-and-later.md#performancecounters-element)