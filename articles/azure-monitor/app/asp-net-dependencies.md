---
title: Zależność śledzenia w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Monitoruj wywołania zależności z lokalnej lub Microsoft Azure aplikacji sieci Web z Application Insights.
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
ms.openlocfilehash: c2f115564c81f38dd437f1d3ff1e33d7d162a42f
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326449"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Śledzenie zależności w usłudze Azure Application Insights 

A *zależności* jest składnik zewnętrzny, która jest wywoływana przez aplikację. Zazwyczaj jest wywoływany przy użyciu protokołu HTTP lub bazy danych lub systemu plików usługi. [Application Insights](../../azure-monitor/app/app-insights-overview.md) mierzy czas trwania wywołań zależności, bez względu na to, czy wystąpił błąd, czy nie, wraz z dodatkowymi informacjami, takimi jak nazwa zależności i tak dalej. Można zbadać konkretne wywołania zależności i skorelować je z żądaniami i wyjątkami.

## <a name="automatically-tracked-dependencies"></a>Automatycznie śledzone zależności

Zestawy SDK Application Insights dla platform .NET i .NET Core `DependencyTrackingTelemetryModule` , z którymi jest moduł telemetrii, który automatycznie zbiera zależności. Ta kolekcja zależności jest włączana automatycznie dla aplikacji [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) i [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) , gdy jest skonfigurowana zgodnie z powiązanymi oficjalnymi dokumentami. Program jest dostarczany [jako pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) NuGet i zostaje automatycznie uruchomiony w przypadku używania jednego z `Microsoft.ApplicationInsights.Web` pakietów NuGet lub `Microsoft.ApplicationInsights.AspNetCore`. `DependencyTrackingTelemetryModule`

 `DependencyTrackingTelemetryModule`obecnie śledzi następujące zależności automatycznie:

|Zależności |Szczegóły|
|---------------|-------|
|Http/Https | Lokalne lub zdalne wywołania http/https |
|Wywołania WCF| Śledzone automatycznie tylko w przypadku użycia powiązań opartych na protokole HTTP.|
|SQL | Wywołania wykonane przy `SqlClient`użyciu. Zobacz [ten](#advanced-sql-tracking-to-get-full-sql-query) sposób przechwytywania zapytania SQL.  |
|[Azure Storage (obiekt BLOB, tabela, kolejka)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Wywołania wykonane za pomocą klienta usługi Azure Storage. |
|[Zestaw SDK klienta EventHub](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Wersja 1.1.0 i nowsza. |
|[Zestaw SDK klienta ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Wersja 3.0.0 i nowsza. |
|Azure Cosmos DB | Śledzone automatycznie tylko wtedy, gdy jest używany protokół HTTP/HTTPS. Nie można przechwycić trybu TCP przez usługę Application Insights. |


## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Skonfiguruj automatyczne śledzenie zależności w aplikacjach konsolowych

Aby automatycznie śledzić zależności od aplikacji konsolowych platformy .NET/.NET Core, należy zainstalować `Microsoft.ApplicationInsights.DependencyCollector`pakiet NuGet i `DependencyTrackingTelemetryModule` zainicjować go w następujący sposób:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="how-automatic-dependency-monitoring-works"></a>Jak działa automatyczne monitorowanie zależności?

Zależności są zbierane automatycznie przy użyciu jednej z następujących metod:

* Używanie Instrumentacji kodu bajtowego dookoła metod SELECT. (InstrumentationEngine z StatusMonitor lub rozszerzenia aplikacji sieci Web platformy Azure)
* Wywołania zwrotne EventSource
* Wywołania zwrotne DiagnosticSource (w najnowszych zestawach SDK .NET/.NET Core)

## <a name="manually-tracking-dependencies"></a>Ręczne śledzenie zależności

Poniżej przedstawiono kilka przykładów zależności, które nie są zbierane automatycznie, a tym samym wymagają śledzenia ręcznego.

* Usługa Azure Cosmos DB są automatycznie śledzone tylko wtedy, gdy [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) jest używany. Nie można przechwycić trybu TCP przez usługę Application Insights.
* Redis

Dla tych zależności, które nie są automatycznie zbierane przez zestaw SDK, można je śledzić ręcznie przy użyciu [interfejsu API TrackDependency](api-custom-events-metrics.md#trackdependency) , który jest używany przez standardowe moduły automatycznego zbierania danych.

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

Alternatywnie, `TelemetryClient` `StartOperation` dostarcza [metody rozszerzające](custom-operations-tracking.md#outgoing-dependencies-tracking) , któremogąbyćużywanedoręcznegośledzeniazależności,jak`StopOperation` pokazano poniżej

Jeśli chcesz wyłączyć standardowy moduł śledzenia zależności, usuń odwołanie do DependencyTrackingTelemetryModule w [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) dla aplikacji ASP.NET. W przypadku aplikacji ASP.NET Core należy postępować zgodnie z instrukcjami znajdującymi się [tutaj](asp-net-core.md#configuring-or-removing-default-telemetrymodules).

## <a name="tracking-ajax-calls-from-web-pages"></a>Śledzenie wywołań AJAX ze stron sieci Web

W przypadku stron sieci Web zestaw SDK Application Insights JavaScript automatycznie zbiera wywołania AJAX jako zależności, jak opisano [tutaj](javascript.md#ajax-performance). Ten dokument koncentruje się na zależnościach od składników serwera programu.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Zaawansowane śledzenie SQL, aby uzyskać pełne zapytanie SQL

W przypadku wywołań SQL nazwa serwera i bazy danych są zawsze zbierane i przechowywane jako nazwa zebranych `DependencyTelemetry`elementów. Istnieje dodatkowe pole o nazwie "Data", które może zawierać pełny tekst zapytania SQL.

W przypadku aplikacji ASP.NET Core nie jest wymagany żaden dodatkowy krok w celu uzyskania pełnego zapytania SQL.

W przypadku aplikacji ASP.NET pełnych zapytań SQL jest zbieranych za pomocą Instrumentacji kodu bajtowego, co wymaga aparatu Instrumentacji. Wymagane są dodatkowe czynności specyficzne dla platformy, zgodnie z poniższym opisem.

| Platforma | Kroki, które trzeba wykonać, aby uzyskać pełne zapytanie SQL |
| --- | --- |
| Aplikacja sieci Web platformy Azure |W panelu sterowania aplikacji sieci Web [Otwórz blok Application Insights](../../azure-monitor/app/azure-web-apps.md) i Włącz polecenia SQL w obszarze .NET |
| Serwer IIS (maszyna wirtualna platformy Azure, premium itd.) | Użyj modułu monitor stanu PowerShell, aby [zainstalować aparat Instrumentacji](../../azure-monitor/app/status-monitor-v2-api-enable-instrumentation-engine.md) i ponownie uruchomić usługi IIS. |
| Usługa w chmurze platformy Azure | Dodaj [zadanie uruchamiania, aby zainstalować StatusMonitor](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> Twoja aplikacja powinna zostać dołączona do zestawu ApplicationInsights SDK w czasie kompilacji przez zainstalowanie pakietów NuGet dla aplikacji [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) lub [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) |
| IIS Express | Nieobsługiwane

W powyższych przypadkach poprawna metoda sprawdzania poprawności aparatu Instrumentacji jest poprawnie zainstalowana, sprawdzając, czy wersja zestawu SDK zebrana `DependencyTelemetry` to "rddp". element "rdddsd" lub "rddf" wskazuje zależności, które są zbierane za pośrednictwem wywołania zwrotnego DiagnosticSource lub EventSource, i dlatego pełne zapytanie SQL nie zostanie przechwycone.

## <a name="where-to-find-dependency-data"></a>Gdzie można znaleźć dane zależności

* [Mapa aplikacji](app-map.md) wizualizuje zależności między aplikacji i składników sąsiednich.
* [Diagnostyka transakcji](transaction-diagnostics.md) przedstawia ujednolicone, skorelowane dane serwera.
* [Karta przeglądarki](javascript.md#ajax-performance) pokazuje wywołania AJAX z przeglądarek użytkowników.
* Kliknij pozycję przez powolne lub Nieudane żądania, aby sprawdzić ich wywołania zależności.
* [Analiza](#logs-analytics) może służyć do zależności zapytania o dane.

## <a name="diagnosis"></a> Diagnozowanie wolne żądania

Każde zdarzenie żądania jest skojarzone z wywołaniami zależności, wyjątkami i innymi zdarzeniami, które są śledzone podczas przetwarzania żądania przez aplikację. Dlatego jeśli niektóre żądania są wykonywane nieprawidłowo, można sprawdzić, czy jest to spowodowane powolnymi odpowiedziami z zależności.

### <a name="tracing-from-requests-to-dependencies"></a>Śledzenie za pomocą żądań do zależności

Otwórz kartę **wydajność** i przejdź do karty **zależności** u góry obok pozycji operacje.

Kliknij **nazwę zależności** w obszarze Ogólne. Po wybraniu zależności zostanie wyświetlony wykres rozkładu czasu trwania tej zależności po prawej stronie.

![Na karcie Wydajność kliknij kartę zależność u góry, a następnie nazwę zależności na wykresie.](./media/asp-net-dependencies/2-perf-dependencies.png)

Kliknij przycisk niebieskich **przykładów** w prawym dolnym rogu, a następnie na przykład, aby wyświetlić szczegółowe informacje o transakcji.

![Kliknij przykład, aby wyświetlić szczegółowe informacje o transakcji](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Profil witryny na żywo

Nie wiadomo, gdzie przejdzie czasu? [Profiler Application Insights](../../azure-monitor/app/profiler.md) śledzi połączenia HTTP z aktywną witryną i wyświetla funkcje w kodzie, które zajęły najdłuższy czas.

## <a name="failed-requests"></a>Żądania zakończone niepowodzeniem

Żądania zakończone niepowodzeniem, może być również skojarzona z zakończonymi niepowodzeniem wywołaniami do zależności.

Możemy przejść do karty **Błędy** po lewej stronie, a następnie kliknąć kartę **zależności** u góry.

![Kliknij wykres, żądań zakończonych niepowodzeniem](./media/asp-net-dependencies/4-fail.png)

W tym miejscu będzie można zobaczyć liczbę nieudanych zależności. Aby uzyskać więcej szczegółów na temat wystąpienia awarii, spróbuj kliknąć nazwę zależności w dolnej tabeli. Możesz kliknąć przycisk niebieskie **zależności** w prawym dolnym rogu, aby uzyskać szczegółowe informacje o transakcjach.

## <a name="logs-analytics"></a>Dzienniki (analiza)

Zależności można śledzić w [języku zapytań Kusto](/azure/kusto/query/). Oto kilka przykładów.

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

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Jak automatyczne wywołania raportu modułu zbierającego zależności nie powiodły się do zależności?*

* Wywołania zależności zakończone niepowodzeniem mają ustawioną wartość false dla pola "Success". `DependencyTrackingTelemetryModule`nie zgłasza `ExceptionTelemetry`. Pełen model danych dla zależności został opisany [tutaj](data-model-dependency-telemetry.md).

## <a name="open-source-sdk"></a>Zestaw SDK open source
Podobnie jak w przypadku każdego zestawu Application Insights SDK, moduł kolekcji zależności jest również typu open source. Odczytaj i współtworzyj kod lub zgłoś problemy w [oficjalnym repozytorium GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet-server).

## <a name="next-steps"></a>Następne kroki

* [Wyjątki](../../azure-monitor/app/asp-net-exceptions.md)
* [Dane użytkownika i strony](../../azure-monitor/app/javascript.md)
* [Dostępność](../../azure-monitor/app/monitor-web-app-availability.md)
