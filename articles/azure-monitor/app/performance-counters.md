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
ms.openlocfilehash: d38a575af54f044d64efc67b5483a67ffcd2fcd6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60256992"
---
# <a name="system-performance-counters-in-application-insights"></a>Liczniki wydajności systemu w usłudze Application Insights

W systemie Windows dostępne są różne [liczniki wydajności](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters), takie jak zajętości procesora CPU, pamięci, dysku i użycia sieci. Można także zdefiniować własne liczniki wydajności. Tak długo, jak aplikacja jest uruchomiona w środowisku usług IIS na hosta w środowisku lokalnym lub maszynie wirtualnej, do których masz dostęp administracyjny.

## <a name="view-counters"></a>Wyświetlanie liczników

W okienku metryki zawiera domyślny zestaw liczników wydajności.

![Liczniki wydajności zgłoszonych w usłudze Application Insights](./media/performance-counters/performance-counters.png)

Bieżące domyślne liczniki, które są zbierane dla aplikacji sieci web platformy .NET są:

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
   * Jeśli używasz monitora stanu do instrumentacji aplikacji sieci web w czasie wykonywania, Znajdź plik ApplicationInsights.config w katalogu głównym aplikacji w usługach IIS. Aktualizacje dostępne w każdym wystąpieniu serwera.
3. Edytuj dyrektywy modułu zbierającego wydajności:
   
```XML
   
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

Można przechwycić zarówno liczniki standardowe, jak i te zastało zaimplementowane samodzielnie. `\Objects\Processes` znajduje się przykład standardowa licznika, która jest dostępna we wszystkich systemach Windows. `\Sales(photo)\# Items Sold` znajduje się przykład liczników niestandardowych, które mogą zostać zaimplementowane w usłudze sieci web. 

Format jest `\Category(instance)\Counter"`, lub dla kategorii, które nie mają wystąpienia, po prostu `\Category\Counter`.

`ReportAs` jest wymagany dla nazwy liczników, które nie pasują `[a-zA-Z()/-_ \.]+` — oznacza to, zawiera znaki, które nie znajdują się w następujących zestawach: litery round nawiasy kwadratowe, ukośnika, łącznik, podkreślenie, spację, dot.

Jeśli określisz wystąpienia, zostaną zebrane jako wymiar "CounterInstanceName" zgłoszonych metryki.

### <a name="collecting-performance-counters-in-code"></a>Zbieranie liczników wydajności w kodzie
Aby zebrać liczników wydajności systemu i wysyłać je do usługi Application Insights, można przystosować poniższy fragment kodu:


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\.NET CLR Memory([replace-with-application-process-name])\# GC Handles", "GC Handles")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```
Lub możesz zrobić to samo za pomocą metryk niestandardowych, który został utworzony:

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
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

## <a name="performance-counters-in-aspnet-core-applications"></a>Liczniki wydajności w aplikacjach ASP.NET Core
Liczniki wydajności są obsługiwane tylko wtedy, gdy aplikacja jest przeznaczony dla pełny program .NET Framework. Istnieje możliwość zbierania liczników wydajności dla aplikacji .NET Core.

## <a name="alerts"></a>Alerty
Podobnie jak inne metryki można [ustawić alert](../../azure-monitor/app/alerts.md) ostrzegania, jeśli licznik wydajności przekroczy limit, można określić. Otwórz w okienku alertów i kliknij przycisk Dodaj Alert.

## <a name="next"></a>Następne kroki
* [Śledzenie zależności](../../azure-monitor/app/asp-net-dependencies.md)
* [Śledzenie wyjątków](../../azure-monitor/app/asp-net-exceptions.md)

