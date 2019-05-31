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
ms.date: 12/06/2018
ms.author: mbullwin
ms.openlocfilehash: 479b810c5a66917bde5754d32991fb489ea26c9b
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299289"
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
| Azure Cloud Service |[Zadanie uruchamiania użyj](../../azure-monitor/app/cloudservices.md) do [Instalowanie Monitora stanu](monitor-performance-live-website-now.md#download) |
| IIS Express | Nieobsługiwane

W powyższych przypadkach prawidłowy sposób sprawdzania poprawności silnika Instrumentacji jest poprawnie zainstalowany jest weryfikując, czy wersja zestawu SDK są zbierane `DependencyTelemetry` jest "rddp". "rdddsd" lub "rddf" wskazuje zależności są zbierane za pomocą wywołania zwrotne DiagnosticSource lub źródła zdarzeń i dlatego nie będzie można przechwycić pełne zapytanie SQL.

## <a name="where-to-find-dependency-data"></a>Gdzie można znaleźć dane zależności

* [Mapa aplikacji](app-map.md) wizualizuje zależności między aplikacji i składników sąsiednich.
* [Diagnostyka transakcji](transaction-diagnostics.md) pokazuje unified, skorelowanych danych serwera.
* [Blok przeglądarki](javascript.md#ajax-performance) przedstawia wywołania AJAX z przeglądarek użytkowników.
* Klikaj elementy z żądań wolno lub nie powiodło się, aby sprawdzić, wywołania ich zależności.
* [Analiza](#analytics) może służyć do zależności zapytania o dane.

## <a name="diagnosis"></a> Diagnozowanie wolne żądania

Każde zdarzenie żądania jest skojarzony z wywołania zależności, wyjątki i inne zdarzenia, które są śledzone podczas przetwarzania żądania przez aplikację. Dlatego jeśli niektóre żądania są nieprawidłowo w inny sposób, możesz dowiedzieć się czy jest to ze względu na powolne odpowiedzi od zależności.

Przejdźmy teraz przez przykład.

### <a name="tracing-from-requests-to-dependencies"></a>Śledzenie za pomocą żądań do zależności

Otwórz blok wydajność i spójrz na siatce żądań:

![Lista żądań ze średnimi lub liczby](./media/asp-net-dependencies/02-reqs.png)

Co najważniejsze, trwa długo. Zobaczmy, jeśli firma Microsoft może Dowiedz się, gdzie jest zużywany.

Kliknij ten wiersz, aby wyświetlić pojedynczego żądania zdarzenia:

![Lista wystąpień na żądanie](./media/asp-net-dependencies/03-instances.png)

Kliknij dowolne wystąpienie długotrwałych sprawdź go dalej i przewiń w dół do wywołania zdalnej zależności związane z tym żądaniem:

![Znajdź wywołania zależności zdalnych, identyfikowanie nietypowego czas trwania](./media/asp-net-dependencies/04-dependencies.png)

Wygląda na to najbardziej obsługi czas, który tego żądania spędzono w wywołaniu do lokalnej usługi.

Wybierz ten wiersz, aby uzyskać więcej informacji:

![Klikaj elementy tej zdalnego zależności, aby zidentyfikować Przyczyna nadmiernego](./media/asp-net-dependencies/05-detail.png)

Wygląda na tę zależność jest, gdzie jest problem. Firma Microsoft została przeprowadzana na ten problem, więc teraz możemy po prostu musisz dowiedzieć się, dlaczego to wywołanie jest trwa tak długo.

### <a name="request-timeline"></a>Osi czasu żądania

W przypadku różnych ma Brak wywołania zależności, który jest wyjątkowo długie. Jednak, przełączając się widok osi czasu, okaże się, gdy opóźnienie podczas naszego wewnętrznego przetwarzania:

![Znajdź wywołania zależności zdalnych, identyfikowanie nietypowego czas trwania](./media/asp-net-dependencies/04-1.png)

Prawdopodobnie duży odstęp po pierwszą zależnością wywołać, więc należy przyjrzymy się naszego kodu, aby zobaczyć, dlaczego jest.

### <a name="profile-your-live-site"></a>Profil witryny na żywo

Nie wiadomo, gdzie przejdzie czasu? [Profiler usługi Application Insights](../../azure-monitor/app/profiler.md) ślady protokołu HTTP wywołania witryny na żywo i przedstawiono funkcje w kodzie, który trwało najdłużej.

## <a name="failed-requests"></a>Żądania zakończone niepowodzeniem

Żądania zakończone niepowodzeniem, może być również skojarzona z zakończonymi niepowodzeniem wywołaniami do zależności. Ponownie firma Microsoft może kliknij, aby śledzić problem.

![Kliknij wykres, żądań zakończonych niepowodzeniem](./media/asp-net-dependencies/06-fail.png)

Kliknij, aby wystąpienia żądań zakończonych niepowodzeniem i spójrz na jego powiązanych zdarzeń.

![Kliknij typ żądania, kliknij wystąpienie aby przejść do innego widoku tego samego wystąpienia, kliknij je, aby uzyskać szczegóły wyjątku.](./media/asp-net-dependencies/07-faildetail.png)

## <a name="analytics"></a>Analiza

Możesz śledzić zależności w [język zapytania Kusto](/azure/kusto/query/). Oto kilka przykładów.

* Znajdź wszystkie nieudane wywołania zależności:

```

    dependencies | where success != "True" | take 10
```

* Znajdź wywołania AJAX:

```

    dependencies | where client_Type == "Browser" | take 10
```

* Znajdź wywołania zależności związanych z żądaniami:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Znajdź wywołania AJAX skojarzone z wyświetleń stron:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Jak wywołania zależności nie ma zależności automatycznego modułu zbierającego raportu?*

* Wywołania zależności zakończone niepowodzeniem mają pola 'success' ustawiony na wartość False. `DependencyTrackingTelemetryModule` nie raportuje `ExceptionTelemetry`. Opisano modelu pełnych danych dla zależności [tutaj](data-model-dependency-telemetry.md).

## <a name="open-source-sdk"></a>Zestaw SDK typu open-source
Podobnie jak każdy zestaw Application Insights SDK moduł zbierania zależności jest również typu open source. Odczyt i przyczynić się do kodu lub zgłosić problemy w czasie [oficjalne repozytorium GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet-server).


## <a name="next-steps"></a>Kolejne kroki

* [Wyjątki](../../azure-monitor/app/asp-net-exceptions.md)
* [Dane użytkownika i strony](../../azure-monitor/app/javascript.md)
* [Dostępność](../../azure-monitor/app/monitor-web-app-availability.md)
