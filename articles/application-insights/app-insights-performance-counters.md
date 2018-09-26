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
ms.devlang: na
ms.topic: conceptual
ms.date: 10/11/2016
ms.author: mbullwin
ms.openlocfilehash: bb1b1e2934de052479f39d40bfe143345e91bc26
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094098"
---
# <a name="system-performance-counters-in-application-insights"></a>Liczniki wydajności systemu w usłudze Application Insights
Windows oferuje szeroką gamę [liczniki wydajności](http://www.codeproject.com/Articles/8590/An-Introduction-To-Performance-Counters) takich jak zajętość procesora CPU, pamięci, dysku i użycia sieci. Można również definiować własne. [Usługa Application Insights](app-insights-overview.md) można wyświetlić te liczniki wydajności, jeśli aplikacja jest uruchomiona w środowisku usług IIS na hosta w środowisku lokalnym lub maszynie wirtualnej, do których masz dostęp administracyjny. Wykresy określają zasoby dostępne dla działającej aplikacji, a może pomóc ustalić niezrównoważonego obciążenia między wystąpieniami serwera.

Liczniki wydajności są wyświetlane w bloku serwerów, który zawiera tabelę tego segmenty przez wystąpienie serwera.

![Liczniki wydajności zgłoszonych w usłudze Application Insights](./media/app-insights-performance-counters/counters-by-server-instance.png)

(Liczniki wydajności nie są dostępne dla aplikacji sieci Web platformy Azure. Ale możesz [wysyłanie diagnostyki Azure do usługi Application Insights](app-insights-azure-diagnostics.md).)

## <a name="view-counters"></a>Wyświetlanie liczników
Blok serwerów zawiera domyślny zestaw liczników wydajności. 

Aby wyświetlić liczniki, edytować wykresy w bloku serwerów albo otwarcie nowej [Eksploratora metryk](app-insights-metrics-explorer.md) bloku i dodać nowe wykresy. 

Dostępne liczniki są wyświetlane jako metryki podczas edytowania wykresu.

![Liczniki wydajności zgłoszonych w usłudze Application Insights](./media/app-insights-performance-counters/choose-performance-counters.png)

Aby zobaczyć wszystkie wykresy najbardziej przydatne w jednym miejscu, Utwórz [pulpit nawigacyjny](app-insights-dashboards.md) nie i przypinania ich do niego.

## <a name="add-counters"></a>Dodawanie liczników
Licznik wydajności, które mają nie jest widoczne na liście metryk, który jest ponieważ zestaw SDK usługi Application Insights nie jest zbieranie go na serwerze sieci web. Możesz skonfigurować, tak aby to zrobić.

1. Dowiedz się, jakie liczniki są dostępne na serwerze za pomocą tego polecenia programu PowerShell na serwerze:
   
    `Get-Counter -ListSet *`
   
    (Zobacz [ `Get-Counter` ](https://technet.microsoft.com/library/hh849685.aspx).)
2. Otwórz plik ApplicationInsights.config.
   
   * Jeśli dodano usługę Application Insights do aplikacji podczas programowania, Edytuj plik ApplicationInsights.config w projekcie, a następnie ponownie wdrożyć je na serwerach.
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

Można przechwycić zarówno liczniki standardowe, jak i te udało Ci się wdrożyć samodzielnie. `\Objects\Processes` przykład standardowa licznika, dostępne we wszystkich systemach Windows. `\Sales(photo)\# Items Sold` znajduje się przykład liczników niestandardowych, które mogą zostać zaimplementowane w usłudze sieci web. 

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
Można wyszukiwać i wyświetlać raporty dotyczące licznika wydajności w [analizy](app-insights-analytics.md).

**Liczniki wydajności** udostępnia schematu `category`, `counter` nazwy i `instance` nazwę każdego licznika wydajności.  W danych telemetrycznych dla każdej aplikacji zobaczysz tylko liczniki dla tej aplikacji. Na przykład aby zobaczyć, jakie liczniki są dostępne: 

![Liczniki wydajności w usłudze Application Insights analytics](./media/app-insights-performance-counters/analytics-performance-counters.png)

("Wystąpienie" odnosi się tutaj do wystąpienia licznika wydajności nie wystąpienie maszyny roli lub serwera. Nazwa wystąpienia licznika wydajności zazwyczaj segmenty liczniki, takie jak czas procesora według nazwy procesu lub aplikacji.)

Aby wyświetlić wykres dostępnej pamięci w okresie ostatnich: 

![Wykres czasu pamięci, w usłudze Application Insights analytics](./media/app-insights-performance-counters/analytics-available-memory.png)

Inne telemetrii, takie jak **liczniki wydajności** również zawiera kolumnę `cloud_RoleInstance` oznacza tożsamość wystąpienia serwera hosta, na którym uruchomiona jest aplikacja. Na przykład, aby porównać wydajność aplikacji na różnych komputerach: 

![Wydajność posegmentowana według wystąpienia roli w usłudze Application Insights analytics](./media/app-insights-performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>Program ASP.NET i usługi Application Insights liczników
*Jaka jest różnica między częstotliwość występowania wyjątków i wyjątków metryk?*

* *Częstotliwość występowania wyjątków* jest licznik wydajności systemu. Środowisko CLR jest liczona wszystkich obsługiwanych i nieobsługiwanych wyjątków, które są zgłaszane i podzielenie sumy w interwale próbkowania przez długość interwału. Zestaw SDK usługi Application Insights zbiera ten wynik i wysyła je do portalu.
* *Wyjątki* jest to liczba raportów metoda TrackException odebranych przez portal w interwale próbkowania wykresu. Obejmuje tylko wyjątki obsłużone gdzie zostały napisane metoda TrackException wywołania w kodzie, a nie zawiera wszystkich [nieobsługiwane wyjątki](app-insights-asp-net-exceptions.md). 

## <a name="performance-counters-in-aspnet-core-applications"></a>Liczniki wydajności w aplikacjach Asp.Net Core
Liczniki wydajności są obsługiwane tylko wtedy, gdy aplikacja jest przeznaczony dla pełny program .NET Framework. Istnieje możliwość liczniki wydajności są zbierane dla.Net Core z aplikacji.

## <a name="alerts"></a>Alerty
Podobnie jak inne metryki można [ustawić alert](app-insights-alerts.md) ostrzegania, jeśli licznik wydajności przekroczy limit, można określić. Otwórz blok alertów i kliknij przycisk Dodaj Alert.

## <a name="next"></a>Następne kroki
* [Śledzenie zależności](app-insights-asp-net-dependencies.md)
* [Śledzenie wyjątków](app-insights-asp-net-exceptions.md)

