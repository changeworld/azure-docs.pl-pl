---
title: Zależność śledzenia w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Monitorowanie wywołań zależności od lokalnej lub aplikacji sieci web Microsoft Azure za pomocą usługi Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: mbullwin
ms.openlocfilehash: d8ba5b19ad5d8f03203e9a028fbc5aec84e5ec06
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565366"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Zależność śledzenia w usłudze Azure Application Insights 

A *zależności* jest składnik zewnętrzny, która jest wywoływana przez aplikację. Zazwyczaj jest wywoływany przy użyciu protokołu HTTP lub bazy danych lub systemu plików usługi. [Usługa Application Insights](../../azure-monitor/app/app-insights-overview.md) mierzy czas trwania wywołania zależności, czy jego kończy się niepowodzeniem lub nie, wraz z dodatkowymi informacjami, takie jak nazwa zależności i tak dalej. Zbadaj wywołania określonej zależności i skoreluj je do żądań i wyjątków.

## <a name="automatically-tracked-dependencies"></a>Automatycznie śledzone zależności

Zestawy SDK usługi Application Insights dla platformy .NET i .NET Core dostarczany z `DependencyTrackingTelemetryModule` czyli moduł danych Telemetrycznych, który automatycznie zbiera zależności. Ta kolekcja zależności jest włączana automatycznie dla [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) i [platformy ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) aplikacji, w przypadku skonfigurowania dla każdego połączonego oficjalna dokumentacja. `DependencyTrackingTelemetryModule` jest dostarczany jako [to](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) pakietu NuGet i zostanie przełączony w tryb automatycznie przy użyciu jednej z pakietów NuGet `Microsoft.ApplicationInsights.Web` lub `Microsoft.ApplicationInsights.AspNetCore`.

 `DependencyTrackingTelemetryModule` obecnie automatycznie śledzi następujące zależności:

|Zależności |Szczegóły|
|---------------|-------|
|Http/Https | Lokalne lub zdalne wywołania http/https |
|Wywołania funkcji WCF| Tylko śledzone automatycznie w przypadku używania powiązań oparty na protokole Http.|
|SQL | Wywołania z `SqlClient`. Zobacz [to](##advanced-sql-tracking-to-get-full-sql-query) przechwytywania SQL zapytań.  |
|[Usługa Azure storage (Blob, tabeli, kolejki)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Wywołania z klienta usługi Storage platformy Azure. |
|[Zestaw SDK klienta usługi EventHub](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Wersji 1.1.0 lub nowszej. |
|[Zestaw SDK klienta usługi ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| W wersji 3.0.0 lub nowszej. |
|Azure Cosmos DB | Tylko śledzone automatycznie w przypadku protokołu HTTP/HTTPS jest używany. Nie można przechwycić trybu TCP przez usługę Application Insights. |


## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Konfigurowanie automatycznego śledzenia w aplikacjach konsoli zależności

Do automatycznego śledzenia zależności od aplikacji konsoli.NET/.NET Core, zainstaluj pakiet Nuget `Microsoft.ApplicationInsights.DependencyCollector`i zainicjuj `DependencyTrackingTelemetryModule` w następujący sposób:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="how-automatic-dependency-monitoring-works"></a>Monitorowanie działa jak automatyczne zależności?

Zależności są zbierane automatycznie przy użyciu jednej z następujących technik:

* Za pomocą Instrumentacji kodu bajt wokół Wybierz metody. (InstrumentationEngine z StatusMonitor lub rozszerzenia aplikacji sieci Web platformy Azure)
* Wywołania zwrotne EventSource
* Wywołania zwrotne DiagnosticSource, (w najnowszej.NET/.NET podstawowych zestawach SDK)

## <a name="manually-tracking-dependencies"></a>Ręczne śledzenie zależności

Poniżej przedstawiono kilka przykładów zależności, które nie są automatycznie zbierane i dlatego wymagają ręcznego śledzenia.

* Usługa Azure Cosmos DB są automatycznie śledzone tylko wtedy, gdy [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) jest używany. Nie można przechwycić trybu TCP przez usługę Application Insights.
* Redis

Te zależności nie są automatycznie gromadzone przez zestaw SDK, można śledzić je ręcznie przy użyciu [TrackDependency API](api-custom-events-metrics.md#trackdependency) używanego przez automatycznie standardowych modułów gromadzenia.

Na przykład kompilowania kodu z zestawu, który nie zostały napisane samodzielnie, można czas wszystkie wywołania, aby dowiedzieć się, jakie wkład kieruje do Twoje czasy odpowiedzi. Aby uzyskać te dane wyświetlane na wykresach zależności w usłudze Application Insights, wyślij go za pomocą `TrackDependency`.

```csharp

    var startTime = DateTime.UtcNow;
    var timer = System.Diagnostics.Stopwatch.StartNew();
    try
    {
        // making dependency call
        success = dependency.Call();
    }
    finally
    {
        timer.Stop();
        telemetryClient.TrackDependency("myDependencyType", "myDependencyCall", "myDependencyData",  startTime, timer.Elapsed, success);
    }
```

Alternatywnie `TelemetryClient` udostępnia metody rozszerzenia `StartOperation` i `StopOperation` który może służyć do ręcznego śledzenia zależności, jak pokazano [tutaj](custom-operations-tracking.md#outgoing-dependencies-tracking)

Jeśli chcesz wyłączyć modułu śledzenia zależności standardowe, Usuń odwołanie do DependencyTrackingTelemetryModule w [plik ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) dla aplikacji ASP.NET. Aplikacje platformy ASP.NET Core, postępuj zgodnie z instrukcjami [tutaj](asp-net-core.md#configuring-or-removing-default-telemetrymodules).

## <a name="tracking-ajax-calls-from-web-pages"></a>Śledzenie wywołania AJAX ze stron sieci Web

Dla stron sieci web, zestaw JavaScript SDK Application Insights automatycznie zbiera wywołania AJAX jako zależności zgodnie z opisem [tutaj](javascript.md#ajax-performance). Ten dokument koncentruje się na zależności z serwera składników.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Zaawansowane SQL śledzenia, aby uzyskać pełne zapytanie SQL

Dla połączeń SQL, nazwę serwera i bazy danych jest zawsze zebrane i przechowywane jako nazwa zebranych `DependencyTelemetry`. Brak dodatkowego pola o nazwie "dane", który może zawierać pełnotekstowe zapytania SQL.

W przypadku aplikacji platformy ASP.NET Core nie ma żadnych dodatkowych czynności, wymagane, aby uzyskać pełne zapytanie SQL.

Aplikacji ASP.NET za pomocą Instrumentacji kodu bajtów, co wymaga Instrumentacji aparat są zbierane pełne zapytanie SQL. Dodatkowe kroki specyficzne dla platformy, jak opisano poniżej, są wymagane.

| Platforma | Czynności wymagane, aby uzyskać pełne zapytanie SQL |
| --- | --- |
| Aplikacja sieci Web platformy Azure |W Panelu sterowania aplikacji sieci web [Otwórz blok usługi Application Insights](../../azure-monitor/app/azure-web-apps.md) i włączyć poleceń SQL w ramach platformy .NET |
| Serwer IIS (maszyn wirtualnych platformy Azure, lokalnych i itd.) | [Zainstaluj Monitor stanu na serwerze, na którym uruchomiona jest aplikacja](../../azure-monitor/app/monitor-performance-live-website-now.md) i uruchom ponownie usługi IIS.
| Azure Cloud Service | Dodaj [zadania uruchamiania, aby zainstalować StatusMonitor](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> Aplikacja powinna być dołączona do dotycząca usługi Application Insights SDK w czasie kompilacji przez zainstalowanie pakietów NuGet dla [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) lub [aplikacje platformy ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) |
| IIS Express | Nieobsługiwane

W powyższych przypadkach prawidłowy sposób sprawdzania poprawności silnika Instrumentacji jest poprawnie zainstalowany jest weryfikując, czy wersja zestawu SDK są zbierane `DependencyTelemetry` jest "rddp". "rdddsd" lub "rddf" wskazuje zależności są zbierane za pomocą wywołania zwrotne DiagnosticSource lub źródła zdarzeń i dlatego nie będzie można przechwycić pełne zapytanie SQL.

## <a name="where-to-find-dependency-data"></a>Gdzie można znaleźć dane zależności

* [Mapa aplikacji](app-map.md) wizualizuje zależności między aplikacji i składników sąsiednich.
* [Diagnostyka transakcji](transaction-diagnostics.md) pokazuje unified, skorelowanych danych serwera.
* [Kartę przeglądarki](javascript.md#ajax-performance) przedstawia wywołania AJAX z przeglądarek użytkowników.
* Klikaj elementy z żądań wolno lub nie powiodło się, aby sprawdzić, wywołania ich zależności.
* [Analiza](#logs-analytics) może służyć do zależności zapytania o dane.

## <a name="diagnosis"></a> Diagnozowanie wolne żądania

Każde zdarzenie żądania jest skojarzone z wywołania zależności, wyjątki i inne zdarzenia, które są śledzone podczas przetwarzania żądania przez aplikację. Dlatego jeśli niektóre żądania są nieprawidłowo w inny sposób, możesz dowiedzieć się czy jest to ze względu na powolne odpowiedzi od zależności.

### <a name="tracing-from-requests-to-dependencies"></a>Śledzenie za pomocą żądań do zależności

Otwórz **wydajności** kartę i przejdź do **zależności** karty u góry strony, obok operacji.

Kliknij pozycję **Nazwa zależności** w ramach ogólnej. Po wybraniu zależność Wykres rozkładu czasów trwania tej zależności pojawią się po prawej stronie.

![Wydajność karty kliknij kartę zależności u góry następnie nazwa zależności na wykresie](./media/asp-net-dependencies/2-perf-dependencies.png)

Kliknij niebieski **przykłady** przycisk w prawym dolnym rogu, a następnie na przykład, aby zobaczyć szczegóły transakcji end-to-end.

![Kliknij na przykład, aby zobaczyć szczegóły transakcji end-to-end](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Profil witryny na żywo

Nie wiadomo, gdzie przejdzie czasu? [Profiler usługi Application Insights](../../azure-monitor/app/profiler.md) ślady protokołu HTTP wywołania witryny na żywo i przedstawiono funkcje w kodzie, który trwało najdłużej.

## <a name="failed-requests"></a>Żądania zakończone niepowodzeniem

Żądania zakończone niepowodzeniem, może być również skojarzona z zakończonymi niepowodzeniem wywołaniami do zależności.

Firma Microsoft może przejść do **błędów** karcie po lewej stronie, a następnie kliknij polecenie **zależności** kartę u góry.

![Kliknij wykres, żądań zakończonych niepowodzeniem](./media/asp-net-dependencies/4-fail.png)

W tym miejscu można wyświetlić liczbę zależność zakończona niepowodzeniem. Aby uzyskać więcej informacji na temat wystąpienia nie powiodło się, próby, klikając nazwę zależności w dolną tabelę. Możesz kliknąć niebieski **zależności** przycisk w prawym dolnym rogu, aby uzyskać szczegóły transakcji end-to-end.

## <a name="logs-analytics"></a>Dzienniki (analiza)

Możesz śledzić zależności w [język zapytania Kusto](/azure/kusto/query/). Oto kilka przykładów.

* Znajdź wszystkie nieudane wywołania zależności:

``` Kusto

    dependencies | where success != "True" | take 10
```

* Znajdź wywołania AJAX:

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* Znajdź wywołania zależności związanych z żądaniami:

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Znajdź wywołania AJAX skojarzone z wyświetleń stron:

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Jak wywołania zależności nie ma zależności automatycznego modułu zbierającego raportu?*

* Wywołania zależności zakończone niepowodzeniem mają pola 'success' ustawiony na wartość False. `DependencyTrackingTelemetryModule` nie raportuje `ExceptionTelemetry`. Opisano modelu pełnych danych dla zależności [tutaj](data-model-dependency-telemetry.md).

## <a name="open-source-sdk"></a>Zestaw SDK typu open-source
Podobnie jak każdy zestaw Application Insights SDK moduł zbierania zależności jest również typu open source. Odczyt i przyczynić się do kodu lub zgłosić problemy w czasie [oficjalne repozytorium GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet-server).

## <a name="next-steps"></a>Kolejne kroki

* [Wyjątki](../../azure-monitor/app/asp-net-exceptions.md)
* [Dane użytkownika i strony](../../azure-monitor/app/javascript.md)
* [Dostępność](../../azure-monitor/app/monitor-web-app-availability.md)
