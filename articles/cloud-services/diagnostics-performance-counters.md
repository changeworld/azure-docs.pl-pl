---
title: Zbieranie na licznikach wydajności w usługach w chmurze azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak wykrywać, używać i tworzyć liczniki wydajności w usługach w chmurze za pomocą diagnostyki platformy Azure i usługi Application Insights.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 02/02/2018
ms.author: tagore
ms.openlocfilehash: 3b4028a09f69acd5d7a6579b4610785ed32e227d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469531"
---
# <a name="collect-performance-counters-for-your-azure-cloud-service"></a>Zbieranie liczników wydajności dla usługi Azure Cloud Service

Liczniki wydajności umożliwiają śledzenie skuteczności aplikacji i hosta. System Windows Server udostępnia wiele różnych liczników wydajności związanych ze sprzętem, aplikacjami, systemem operacyjnym i innymi. Zbierając i wysyłając liczniki wydajności na platformę Azure, można analizować te informacje, aby pomóc w podejmowaniu lepszych decyzji. 

## <a name="discover-available-counters"></a>Odkryj dostępne liczniki

Licznik wydajności składa się z dwóch części: nazwy zestawu (znanej również jako kategoria) i jednego lub kilku liczników. Za pomocą programu PowerShell można uzyskać listę dostępnych liczników wydajności:

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

Właściwość `CounterSetName` reprezentuje zestaw (lub kategorii) i jest dobrym wskaźnikiem tego, co liczniki wydajności są związane z. Właściwość `Paths` reprezentuje kolekcję liczników dla zestawu. Można również uzyskać `Description` właściwość, aby uzyskać więcej informacji na temat zestawu liczników.

Aby uzyskać wszystkie liczniki dla zestawu, `CounterSetName` użyj wartości `Paths` i rozwiń kolekcję. Każdy element ścieżki jest licznikiem, który można zbadać. Na przykład, aby uzyskać dostępne liczniki związane z zestawem, `Processor` rozwiń kolekcję: `Paths`

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

Te ścieżki poszczególnych liczników można dodać do struktury diagnostyki używane przez usługę w chmurze. Aby uzyskać więcej informacji na temat sposobu konstruowania ścieżki licznika wydajności, zobacz [Określanie ścieżki licznika](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85)).

## <a name="collect-a-performance-counter"></a>Zbieranie licznika wydajności

Licznik wydajności można dodać do usługi w chmurze dla usługi Azure Diagnostics lub Application Insights.

### <a name="application-insights"></a>Application Insights

Usługa Azure Application Insights for Cloud Services umożliwia określenie liczników wydajności, które chcesz zebrać. Po [dodaniu usługi Application Insights do projektu](../azure-monitor/app/cloudservices.md#sdk)plik konfiguracji o nazwie **ApplicationInsights.config** zostanie dodany do projektu programu Visual Studio. Ten plik konfiguracyjny określa, jaki typ informacji usługa Application Insights zbiera i wysyła do platformy Azure.

Otwórz plik **ApplicationInsights.config** i znajdź **element ApplicationInsights** > **TelemetryModules.** Każdy `<Add>` element podrzędny definiuje typ danych telemetrycznych do zbierania wraz z jego konfiguracji. Typ modułu telemetryczne `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector`licznika wydajności to . Jeśli ten element jest już zdefiniowany, nie należy dodawać go po raz drugi. Każdy licznik wydajności do zebrania `<Counters>`jest zdefiniowany w węźle o nazwie . Oto przykład, który zbiera liczniki wydajności dysku:

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

Każdy licznik wydajności jest `<Add>` reprezentowany `<Counters>`jako element w obszarze . Atrybut `PerformanceCounter` definiuje licznik wydajności do zbierania. Atrybut `ReportAs` jest tytuł do wyświetlenia w witrynie Azure portal dla licznika wydajności. Każdy licznik wydajności, który zbierasz, jest umieszczany w kategorii o nazwie **Niestandardowe** w portalu. W przeciwieństwie do diagnostyki platformy Azure nie można ustawić interwału, który te liczniki wydajności są zbierane i wysyłane na platformę Azure. Dzięki usłudze Application Insights liczniki wydajności są zbierane i wysyłane co minutę. 

Usługa Application Insights automatycznie zbiera następujące liczniki wydajności:

* \Proces(?? APP_WIN32_PROC?) \% Czas procesora
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Aby uzyskać więcej informacji, zobacz [Liczniki wydajności systemu w usłudze Application Insights](../azure-monitor/app/performance-counters.md) i [Application Insights for Azure Cloud Services](../azure-monitor/app/cloudservices.md#performance-counters).

### <a name="azure-diagnostics"></a>Diagnostyka Azure

> [!IMPORTANT]
> Podczas gdy wszystkie te dane są agregowane do konta magazynu, portal **nie** zapewnia natywnego sposobu wykresu danych. Zdecydowanie zaleca się zintegrowanie innej usługi diagnostycznej, takiej jak usługa Application Insights, z aplikacją.

Rozszerzenie diagnostyki platformy Azure dla usług w chmurze umożliwia określenie, jakie liczniki wydajności chcesz zebrać. Aby skonfigurować diagnostykę platformy Azure, zobacz [Omówienie monitorowania usług w chmurze](cloud-services-how-to-monitor.md#setup-diagnostics-extension).

Liczniki wydajności, które chcesz zebrać, są zdefiniowane w pliku **diagnostics.wadcfgx.** Otwórz ten plik (jest zdefiniowany dla roli) w programie Visual Studio i znajdź **element DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters.** Dodaj nowy element **PerformanceCounterConfiguration** jako element podrzędny. Ten element ma `counterSpecifier` dwa `sampleRate`atrybuty: i . Atrybut `counterSpecifier` określa, który zestaw liczników wydajności systemu (opisane w poprzedniej sekcji) do zbierania. Wartość `sampleRate` wskazuje, jak często ta wartość jest sondowana. Jako całość wszystkie liczniki wydajności są przenoszone `PerformanceCounters` na platformę Azure zgodnie z wartością atrybutu elementu nadrzędnego. `scheduledTransferPeriod`

Aby uzyskać więcej `PerformanceCounters` informacji na temat elementu schematu, zobacz [schemat diagnostyki platformy Azure](../azure-monitor/platform/diagnostics-extension-schema-windows.md#performancecounters-element).

Okres zdefiniowany `sampleRate` przez atrybut używa typu danych czasu trwania XML, aby wskazać, jak często licznik wydajności jest sondowany. W poniższym przykładzie stawka `PT3M`jest ustawiona na , co oznacza: `[P]eriod[T]ime[3][M]inutes`co trzy minuty.

Aby uzyskać więcej `sampleRate` informacji `scheduledTransferPeriod` na temat sposobu definiowania i są definiowane, zobacz **czas trwania typu danych sekcji** w [W3 XML typy daty i godziny typy](https://www.w3schools.com/XML/schema_dtypes_date.asp) samouczka.

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

## <a name="create-a-new-perf-counter"></a>Tworzenie nowego licznika perf

Nowy licznik wydajności może być tworzony i używany przez kod. Kod, który tworzy nowy licznik wydajności musi być uruchomiony podwyższone, w przeciwnym razie zakończy się niepowodzeniem. Kod startowy usługi `OnStart` w chmurze można utworzyć licznik wydajności, wymagające uruchomienia roli w kontekście podwyższonego poziomu. Można też utworzyć zadanie uruchamiania, które uruchamia z podwyższonym poziomem uprawnień i tworzy licznik wydajności. Aby uzyskać więcej informacji o zadaniach uruchamiania, zobacz [Jak skonfigurować i uruchomić zadania uruchamiania usługi w chmurze](cloud-services-startup-tasks.md).

Aby skonfigurować rolę do uruchamiania `<Runtime>` z podwyższonym poziomem uprawnień, dodaj element do pliku [csdef.](cloud-services-model-and-package.md#servicedefinitioncsdef)

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

Można utworzyć i zarejestrować nowy licznik wydajności za pomocą kilku wierszy kodu. Użyj `System.Diagnostics.PerformanceCounterCategory.Create` przeciążenia metody, która tworzy zarówno kategorię, jak i licznik. Poniższy kod najpierw sprawdza, czy kategoria istnieje, a jeśli brakuje, tworzy zarówno kategorii i licznika.

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

Jeśli chcesz użyć licznika, `Increment` wywołaj metodę lub. `IncrementBy`

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

Teraz, gdy aplikacja używa licznika niestandardowego, należy skonfigurować diagnostykę platformy Azure lub usługa Application Insights do śledzenia licznika.


### <a name="application-insights"></a>Application Insights

Jak wcześniej wspomniano, liczniki wydajności usługi Application Insights są zdefiniowane w pliku **ApplicationInsights.config.** Otwórz **ApplicationInsights.config** i znajdź **ApplicationInsights** > **TelemetryModules** > **Dodaj** > **liczniki** element. Utwórz `<Add>` element podrzędny `PerformanceCounter` i ustaw atrybut do kategorii i nazwy licznika wydajności utworzonego w kodzie. Ustaw `ReportAs` atrybut na przyjazną nazwę, którą chcesz wyświetlić w portalu.

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

Jak wcześniej wspomniano, liczniki wydajności, które chcesz zebrać są zdefiniowane w pliku **diagnostics.wadcfgx.** Otwórz ten plik (jest zdefiniowany dla roli) w programie Visual Studio i znajdź **element DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters.** Dodaj nowy element **PerformanceCounterConfiguration** jako element podrzędny. Ustaw `counterSpecifier` atrybut na kategorię i nazwę licznika wydajności utworzonego w kodzie. 

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
- [Określanie ścieżki licznika](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85))
- [Schemat diagnostyki platformy Azure — liczniki wydajności](../azure-monitor/platform/diagnostics-extension-schema-windows.md#performancecounters-element)



