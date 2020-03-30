---
title: Liczniki wydajności w usłudze Application Insights | Dokumenty firmy Microsoft
description: Monitorowanie systemu i niestandardowych liczników wydajności platformy .NET w usłudze Application Insights.
ms.topic: conceptual
ms.date: 12/13/2018
ms.openlocfilehash: 94d2520c17867f6d70caffd002a76365a425986f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669883"
---
# <a name="system-performance-counters-in-application-insights"></a>Liczniki wydajności systemu w usłudze Application Insights

W systemie Windows dostępne są różne [liczniki wydajności](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters), takie jak zajętości procesora CPU, pamięci, dysku i użycia sieci. Można również zdefiniować własne liczniki wydajności. Zbieranie liczników wydajności jest obsługiwane tak długo, jak aplikacja jest uruchomiona w ramach usług IIS na hoście lokalnym lub na maszynie wirtualnej, do której masz dostęp administracyjny. Chociaż aplikacje działające jako usługi Azure Web Apps nie mają bezpośredniego dostępu do liczników wydajności, podzbiór dostępnych liczników są zbierane przez usługę Application Insights.

## <a name="view-counters"></a>Wyświetlanie liczników

Okienko Metryki zawiera domyślny zestaw liczników wydajności.

![Liczniki wydajności zgłaszane w usłudze Application Insights](./media/performance-counters/performance-counters.png)

Bieżące liczniki domyślne, które są skonfigurowane do zbierania dla ASP.NET/ASP.NET core aplikacji sieci web są:
- %\\czasu procesora procesów
- %\\Znormalizowanego czasu procesora procesu
- Dostępne\\bajty pamięci
- żądania ASP.NET/s
- .NET CLR Wyjątki generowane / s
- ASP.NET czas wykonywania applicationsrequest
- Przetwarzanie\\bajtów prywatnych
- Bajty danych we/wy procesu/s\\
- ASP.NET żądań\\aplikacji w kolejce aplikacji
- Procesor (_Total)\\% czasu procesora

## <a name="add-counters"></a>Dodawanie liczników

Jeśli licznik wydajności, który chcesz, nie znajduje się na liście metryk, możesz go dodać.

1. Dowiedz się, jakie liczniki są dostępne na serwerze, używając tego polecenia programu PowerShell na serwerze lokalnym:

    `Get-Counter -ListSet *`

    (Patrz.) [`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx)
2. Otwórz applicationInsights.config.

   * Jeśli usługa Application Insights została dodana do aplikacji podczas tworzenia, edytuj plik ApplicationInsights.config w projekcie, a następnie ponownie wdrożyć go na serwerach.
3. Edytuj dyrektywę modułu zbierającego wydajność:

    ```XML

        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
          <Counters>
            <Add PerformanceCounter="\Objects\Processes"/>
            <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
          </Counters>
        </Add>
    ```

> [!NOTE]
> ASP.NET Podstawowe aplikacje nie `ApplicationInsights.config`mają , a zatem powyższa metoda nie jest prawidłowa dla aplikacji ASP.NET Core.

Możesz przechwycić zarówno standardowe liczniki, jak i te, które zaimplementowałeś samodzielnie. `\Objects\Processes`jest przykładem standardowego licznika, który jest dostępny we wszystkich systemach Windows. `\Sales(photo)\# Items Sold`jest przykładem licznika niestandardowego, który może być zaimplementowany w usłudze sieci web.

Format jest `\Category(instance)\Counter"`, lub dla kategorii, które nie `\Category\Counter`mają wystąpień, po prostu .

`ReportAs`jest wymagane dla nazw liczników, które nie pasują do siebie `[a-zA-Z()/-_ \.]+` - oznacza to, że zawierają znaki, które nie znajdują się w następujących zestawach: litery, nawiasy okrągłe, ukośnik do przodu, łącznik, podkreślenie, spacja, kropka.

Jeśli określisz wystąpienie, zostanie ono zebrane jako wymiar "CounterInstanceName" zgłoszonej metryki.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>Zbieranie liczników wydajności w kodzie ASP.NET aplikacji sieci Web lub aplikacji konsoli podstawowej .NET/.NET
Aby zebrać liczniki wydajności systemu i wysłać je do usługi Application Insights, można dostosować poniższy fragment kodu:


```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

Możesz też zrobić to samo z utworzonymi metrykami niestandardowymi:

```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Zbieranie liczników wydajności w kodzie dla ASP.NET podstawowych aplikacji sieci Web

Zmodyfikuj `ConfigureServices` metodę w swojej klasie, `Startup.cs` jak poniżej.

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

## <a name="performance-counters-in-analytics"></a>Liczniki wydajności w Analytics
Raporty liczników wydajności można wyszukiwać i wyświetlać w [Analytics](../../azure-monitor/app/analytics.md).

Schemat **performanceCounters** udostępnia nazwę `category` `counter` i `instance` nazwę każdego licznika wydajności.  W danych telemetrycznych dla każdej aplikacji zobaczysz tylko liczniki dla tej aplikacji. Na przykład, aby zobaczyć, jakie liczniki są dostępne: 

![Liczniki wydajności w analizie usługi Application Insights](./media/performance-counters/analytics-performance-counters.png)

('Wystąpienie' w tym miejscu odnosi się do wystąpienia licznika wydajności, a nie roli lub wystąpienia komputera serwera. Nazwa wystąpienia licznika wydajności zazwyczaj segmentuje liczniki, takie jak czas procesora według nazwy procesu lub aplikacji.)

Aby uzyskać wykres dostępnej pamięci w ostatnim okresie: 

![Wykres czasu pamięci w analizie usługi Application Insights](./media/performance-counters/analytics-available-memory.png)

Podobnie jak inne dane telemetryczne, `cloud_RoleInstance` **performanceCounters** ma również kolumnę, która wskazuje tożsamość wystąpienia serwera hosta, na którym aplikacja jest uruchomiona. Na przykład, aby porównać wydajność aplikacji na różnych komputerach: 

![Wydajność podzielona na segmenty według wystąpienia roli w analizie usługi Application Insights](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET i usługa Application Insights

*Jaka jest różnica między metryki Współczynnik wyjątków i Wyjątki?*

* *Współczynnik wyjątków* jest licznikiem wydajności systemu. CLR zlicza wszystkie obsługiwane i nieobsługiwał wyjątków, które są generowane i dzieli sumę w interwale próbkowania przez długość interwału. Zestaw SDK usługi Application Insights zbiera ten wynik i wysyła go do portalu.

* *Wyjątki* to liczba raportów TrackException otrzymanych przez portal w interwale próbkowania wykresu. Obejmuje tylko obsługiwane wyjątki, w których napisano wywołania TrackException w kodzie i nie zawiera wszystkich [nieobsługiwane wyjątki.](../../azure-monitor/app/asp-net-exceptions.md) 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Liczniki wydajności dla aplikacji uruchomionych w usłudze Azure Web Apps

Zarówno ASP.NET, jak i ASP.NET aplikacje Core wdrożone w usłudze Azure Web Apps są uruchamiane w specjalnym środowisku piaskownicy. To środowisko nie zezwala na bezpośredni dostęp do liczników wydajności systemu. Jednak ograniczony podzbiór liczników są narażone jako zmienne środowiskowe, jak opisano [w tym miejscu](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables). Zestaw SDK usługi Application Insights dla ASP.NET i ASP.NET Core zbiera liczniki wydajności z usługi Azure Web Apps z tych specjalnych zmiennych środowiskowych. W tym środowisku dostępny jest tylko podzbiór liczników, a pełną listę można znaleźć [tutaj.](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>Liczniki wydajności w aplikacjach ASP.NET Core

Obsługa liczników wydajności w ASP.NET Core jest ograniczona:

* [Zestaw SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) w wersji 2.4.1 i nowszych zbiera liczniki wydajności, jeśli aplikacja jest uruchomiona w usłudze Azure Web Apps (Windows).
* SDK w wersji 2.7.1 i nowszych zbierać liczniki wydajności, jeśli aplikacja jest uruchomiona w systemie Windows i obiektów docelowych `NETSTANDARD2.0` lub nowszych.
* W przypadku aplikacji przeznaczonych dla platformy .NET Framework wszystkie wersje liczników wydajności sdk obsługują.
* SDK w wersji 2.8.0 i nowszych obsługują licznik procesorów/pamięci w systemie Linux. Żaden inny licznik nie jest obsługiwany w systemie Linux. Zalecanym sposobem uzyskania liczników systemowych w systemie Linux (i innych środowiskach innych niż Windows) jest użycie [EventCounters](eventcounters.md)

## <a name="alerts"></a>Alerty
Podobnie jak inne metryki, można [ustawić alert,](../../azure-monitor/app/alerts.md) aby ostrzec, jeśli licznik wydajności wykracza poza limit określony. Otwórz okienko Alerty i kliknij pozycję Dodaj alert.

## <a name="next-steps"></a><a name="next"></a>Następne kroki

* [Śledzenie zależności](../../azure-monitor/app/asp-net-dependencies.md)
* [Śledzenie wyjątków](../../azure-monitor/app/asp-net-exceptions.md)

