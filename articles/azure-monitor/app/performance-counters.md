---
title: Liczniki wydajności w usłudze Application Insights | Dokumentacja firmy Microsoft
description: Monitorowanie systemu i niestandardowych liczników wydajności platformy .NET w usłudze Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mbullwin
ms.openlocfilehash: cff4aaaab97fdcecab9cdf1d0dff2786f86b604b
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65966715"
---
# <a name="system-performance-counters-in-application-insights"></a>Liczniki wydajności systemu w usłudze Application Insights

W systemie Windows dostępne są różne [liczniki wydajności](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters), takie jak zajętości procesora CPU, pamięci, dysku i użycia sieci. Można także zdefiniować własne liczniki wydajności. Zbieranie danych liczników wydajności jest obsługiwany, tak długo, jak aplikacja jest uruchomiona w środowisku usług IIS na hosta w środowisku lokalnym lub maszynie wirtualnej, do których masz dostęp administracyjny. Mimo że działających jako aplikacje sieci Web platformy Azure nie mają bezpośredniego dostępu do liczników wydajności podzbiór dostępne liczniki są zbierane przez usługę Application Insights.

## <a name="view-counters"></a>Wyświetlanie liczników

W okienku metryki zawiera domyślny zestaw liczników wydajności.

![Liczniki wydajności zgłoszonych w usłudze Application Insights](./media/performance-counters/performance-counters.png)

Bieżące domyślne liczniki, które są skonfigurowane mają być zbierane dla aplikacji sieci web ASP.NET/ASP.NET Core są:

         - % Process\\Processor Time
         - % Process\\Processor Time Normalized
         - Memory\\Available Bytes
         - ASP.NET Requests/Sec
         - .NET CLR Exceptions Thrown / sec
         - ASP.NET ApplicationsRequest Execution Time
         - Process\\Private Bytes
         - Process\\IO Data Bytes/sec
         - ASP.NET Applications\\Requests In Application Queue
         - Processor(_Total)\\% Processor Time

Aby zobaczyć wszystkie wykresy najbardziej przydatne w jednym miejscu, Utwórz [pulpit nawigacyjny](../../azure-monitor/app/app-insights-dashboards.md) nie i przypinania ich do niego.

## <a name="add-counters"></a>Dodawanie liczników

Jeśli licznik wydajności, które mają nie jest uwzględniony na liście metryk, możesz dodać go.

1. Dowiedz się, jakie liczniki są dostępne na serwerze za pomocą tego polecenia programu PowerShell na serwerze lokalnym:

    `Get-Counter -ListSet *`

    (Zobacz [ `Get-Counter` ](https://technet.microsoft.com/library/hh849685.aspx).)
2. Otwórz plik ApplicationInsights.config.

   * Jeśli dodano usługę Application Insights do aplikacji podczas programowania, Edytuj plik ApplicationInsights.config w projekcie, a następnie wdrożysz go ponownie do serwerów.
3. Edytuj dyrektywy modułu zbierającego wydajności:

```XML

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

> [!NOTE]
> Aplikacje platformy ASP.NET Core nie mają `ApplicationInsights.config`, a więc powyższej metody nie jest prawidłowa dla aplikacji programu ASP.NET Core.

Można przechwycić zarówno liczniki standardowe, jak i te zastało zaimplementowane samodzielnie. `\Objects\Processes` znajduje się przykład standardowa licznika, która jest dostępna we wszystkich systemach Windows. `\Sales(photo)\# Items Sold` znajduje się przykład liczników niestandardowych, które mogą zostać zaimplementowane w usłudze sieci web.

Format jest `\Category(instance)\Counter"`, lub dla kategorii, które nie mają wystąpienia, po prostu `\Category\Counter`.

`ReportAs` jest wymagany dla nazwy liczników, które nie pasują `[a-zA-Z()/-_ \.]+` — oznacza to, zawiera znaki, które nie znajdują się w następujących zestawach: litery round nawiasy kwadratowe, ukośnika, łącznik, podkreślenie, spację, dot.

Jeśli określisz wystąpienia, zostaną zebrane jako wymiar "CounterInstanceName" zgłoszonych metryki.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>Zbieranie liczników wydajności w kodzie aplikacji sieci Web ASP.NET lub aplikacji konsoli Core.NET/.NET
Aby zebrać liczników wydajności systemu i wysyłać je do usługi Application Insights, można przystosować poniższy fragment kodu:


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

Lub możesz zrobić to samo za pomocą metryk niestandardowych, który został utworzony:

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Zbieranie liczników wydajności w kodzie aplikacji sieci Web programu ASP.NET Core

Modyfikowanie `ConfigureServices` method in Class metoda swoje `Startup.cs` klasy zgodnie z poniższymi instrukcjami.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures PerformanceCollectorModule.
  services.ConfigureTelemetryModule<PerformanceCollectorModule>((module, o) =>
            {
                // the application process name could be "dotnet" for ASP.NET Core self-hosted applications.
                module.Counters.Add(new PerformanceCounterCollectionRequest(
    @"\Process([replace-with-application-process-name])\Page Faults/sec", "DotnetPageFaultsPerfSec"));
            });
    }
```

## <a name="performance-counters-in-analytics"></a>Liczniki wydajności w usłudze Analytics
Można wyszukiwać i wyświetlać raporty dotyczące licznika wydajności w [analizy](../../azure-monitor/app/analytics.md).

**Liczniki wydajności** udostępnia schematu `category`, `counter` nazwy i `instance` nazwę każdego licznika wydajności.  W danych telemetrycznych dla każdej aplikacji zobaczysz tylko liczniki dla tej aplikacji. Na przykład aby zobaczyć, jakie liczniki są dostępne: 

![Liczniki wydajności w usłudze Application Insights analytics](./media/performance-counters/analytics-performance-counters.png)

('Instancja' tutaj odnosi się do wystąpienia licznika wydajności, nie rolę lub serwer wystąpienie maszyny. Nazwa wystąpienia licznika wydajności zazwyczaj segmenty liczniki, takie jak czas procesora według nazwy procesu lub aplikacji.)

Aby wyświetlić wykres dostępnej pamięci w okresie ostatnich: 

![Wykres czasu pamięci, w usłudze Application Insights analytics](./media/performance-counters/analytics-available-memory.png)

Inne telemetrii, takie jak **liczniki wydajności** również zawiera kolumnę `cloud_RoleInstance` oznacza tożsamość wystąpienia serwera hosta, na którym uruchomiona jest aplikacja. Na przykład, aby porównać wydajność aplikacji na różnych komputerach: 

![Wydajność posegmentowana według wystąpienia roli w usłudze Application Insights analytics](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>Program ASP.NET i usługi Application Insights liczników

*Jaka jest różnica między częstotliwość występowania wyjątków i wyjątków metryk?*

* *Częstotliwość występowania wyjątków* jest licznik wydajności systemu. Środowisko CLR jest liczona wszystkich obsługiwanych i nieobsługiwanych wyjątków, które są zgłaszane i podzielenie sumy w interwale próbkowania przez długość interwału. Zestaw SDK usługi Application Insights zbiera ten wynik i wysyła je do portalu.

* *Wyjątki* jest to liczba raportów metoda TrackException odebranych przez portal w interwale próbkowania wykresu. Obejmuje tylko wyjątki obsłużone gdzie zostały napisane metoda TrackException wywołania w kodzie, a nie zawiera wszystkich [nieobsługiwane wyjątki](../../azure-monitor/app/asp-net-exceptions.md). 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Liczniki wydajności dla aplikacji uruchamianych w usłudze Azure Web Apps

Aplikacje ASP.NET i ASP.NET Core, wdrożonych w usłudze Azure Web Apps są uruchamiane w środowisku piaskownicy specjalne. To środowisko nie zezwala na bezpośredni dostęp do liczników wydajności systemu. Jednak ograniczonym podzbiorem liczniki są dostępne jako zmienne środowiskowe, zgodnie z opisem [tutaj](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables). Zestaw SDK usługi Application Insights dla platformy ASP.NET i ASP.NET Core służy do zbierania liczników wydajności z usługi Azure Web Apps z tych zmiennych środowiskowych specjalne. Tylko podzbiór liczniki są dostępne w tym środowisku, a pełną listę można znaleźć [tutaj.](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>Liczniki wydajności w aplikacjach ASP.NET Core

* [Zestaw SDK dla platformy ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) wersji 2.4.1 powyżej zbiera liczników wydajności, jeśli aplikacja jest uruchomiona w usłudze Azure Web App (Windows)

* 2.7.0-beta3 wersji zestawu SDK i powyżej zbiera liczników wydajności, jeśli aplikacja jest uruchomiona w Windows i określanie wartości docelowej `NETSTANDARD2.0` lub nowszej.
* Dla aplikacji przeznaczonych dla platformy .NET Framework liczniki wydajności są obsługiwane we wszystkich wersjach zestawu SDK.
* Ten artykuł zostanie zaktualizowany po dodaniu obsługi licznika wydajności w innych niż Windows.

## <a name="alerts"></a>Alerty
Podobnie jak inne metryki można [ustawić alert](../../azure-monitor/app/alerts.md) ostrzegania, jeśli licznik wydajności przekroczy limit, można określić. Otwórz w okienku alertów i kliknij przycisk Dodaj Alert.

## <a name="next"></a>Następne kroki

* [Śledzenie zależności](../../azure-monitor/app/asp-net-dependencies.md)
* [Śledzenie wyjątków](../../azure-monitor/app/asp-net-exceptions.md)

