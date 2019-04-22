---
title: Zależność śledzenia w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Analizowanie użycia, dostępności i wydajności aplikacji lokalnej lub aplikacji sieci web Microsoft Azure za pomocą usługi Application Insights.
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
ms.openlocfilehash: c77b5810164aef7508f717a0f75d90cf6cba2089
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59273111"
---
# <a name="set-up-application-insights-dependency-tracking"></a>Konfigurowanie usługi Application Insights: Śledzenie zależności
A *zależności* jest składnik zewnętrzny, która jest wywoływana przez aplikację. Zazwyczaj jest wywoływany przy użyciu protokołu HTTP lub bazy danych lub systemu plików usługi. [Usługa Application Insights](../../azure-monitor/app/app-insights-overview.md) mierzy czas oczekiwania zależności aplikacji i jak często wywołanie zależności nie powiedzie się. Można zbadać określonych wywołań i dotyczą żądań i wyjątków.

Monitor zależności poza pole zgłasza aktualnie wywołań do tych typów zależności:

* Serwer
  * Bazy danych SQL
  * Sieci web platformy ASP.NET i usługi WCF, które korzystają z powiązań oparty na protokole HTTP
  * Lokalnych lub zdalnych połączeń HTTP
  * Usługa Azure Cosmos DB, tabeli, magazynu obiektów blob i kolejki 
* Strony sieci Web
  * Wywołania AJAX

Monitorowanie działania z zastosowaniem [Instrumentacji kodu bajtów](https://msdn.microsoft.com/library/z9z62c29.aspx) około Wybierz metody lub w oparciu o wywołania zwrotne DiagnosticSource (w najnowsze zestawy SDK platformy .NET) z programu .NET Framework. Obciążenie jest minimalne.

Można również napisać własne wywołania SDK, aby monitorować inne zależności, zarówno w kodzie klienta i serwera przy użyciu [TrackDependency API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

> [!NOTE]
> Usługa Azure Cosmos DB są automatycznie śledzone tylko wtedy, gdy [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) jest używany. Nie można przechwycić trybu TCP przez usługę Application Insights.

## <a name="set-up-dependency-monitoring"></a>Konfigurowanie monitorowania zależności
Informacje o zależnościach częściowe są zbierane automatycznie przez [zestawu SDK usługi Application Insights](asp-net.md). Aby uzyskać kompletne dane, zainstaluj odpowiedniego agenta dla serwera hosta.

| Platforma | Instalowanie |
| --- | --- |
| Serwer usług IIS |Albo [Zainstaluj Monitor stanu na serwerze](../../azure-monitor/app/monitor-performance-live-website-now.md) lub [uaktualnić aplikację .NET Framework 4.6 lub nowszy](https://go.microsoft.com/fwlink/?LinkId=528259) i zainstaluj [zestawu SDK usługi Application Insights](asp-net.md) w swojej aplikacji. |
| Aplikacja sieci Web platformy Azure |W Panelu sterowania aplikacji sieci web [otworzyć bloku usługi Application Insights w Panelu sterowania aplikacji sieci web](../../azure-monitor/app/azure-web-apps.md) i wybierz opcję instalacji, jeśli zostanie wyświetlony monit. |
| Usługa w chmurze platformy Azure |[Zadanie uruchamiania użyj](../../azure-monitor/app/cloudservices.md) lub [Instalowanie programu .NET framework 4.6 +](../../cloud-services/cloud-services-dotnet-install-dotnet.md) |

## <a name="where-to-find-dependency-data"></a>Gdzie można znaleźć dane zależności
* [Mapa aplikacji](#application-map) wizualizuje zależności między aplikacji i składników sąsiednich.
* [Wydajność, przeglądarki i błędów bloków](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-performance) Pokaż dane zależności serwera.
* [Blok przeglądarki](#ajax-calls) przedstawia wywołania AJAX z przeglądarek użytkowników.
* Klikaj elementy z żądań wolno lub nie powiodło się, aby sprawdzić, wywołania ich zależności.
* [Analiza](#analytics) może służyć do zależności zapytania o dane.

## <a name="application-map"></a>Mapa aplikacji
Mapa aplikacji działa jako pomoc wizualną odnajdywanie zależności między składnikami aplikacji. Jest automatycznie generowany na podstawie danych telemetrycznych z Twojej aplikacji. Ten przykład przedstawia wywołania AJAX ze skryptów przeglądarki i wywołania REST z aplikacji serwera, z dwoma usługami zewnętrznymi.

![Mapa aplikacji](./media/asp-net-dependencies/cloud-rolename.png)

* **Przejdź z pól** odpowiednie zależności i inne wykresy.
* **Przypnij mapę** do [pulpit nawigacyjny](../../azure-monitor/app/app-insights-dashboards.md), gdzie będzie można w pełni funkcjonalne.

[Dowiedz się więcej](../../azure-monitor/app/app-map.md).

## <a name="performance-and-failure-blades"></a>Bloki wydajności i błędów
Blok wydajność pokazuje czas trwania wywołania zależności za pomocą aplikacji serwera.

**Liczba awarii** są wyświetlane w **błędy** bloku. Błąd jest zwracany kod, który nie znajduje się w zakresie 200-399, lub nieznany.

> [!NOTE]
> **100% niepowodzeń?** -Prawdopodobnie wskazuje tylko otrzymujesz dane częściowe zależności. Musisz [skonfigurować monitorowanie zależności odpowiednie dla danej platformy](#set-up-dependency-monitoring).
>
>

## <a name="ajax-calls"></a>Wywołania AJAX
Blok przeglądarki przedstawia współczynnik czas trwania i niepowodzeń wywołań AJAX z [JavaScript na stronach sieci web](../../azure-monitor/app/javascript.md). Są one wyświetlane jako zależności.

## <a name="diagnosis"></a> Diagnozowanie wolne żądania
Każde zdarzenie żądania jest skojarzone z wywołania zależności, wyjątki i inne zdarzenia, które są śledzone podczas przetwarzania żądania przez aplikację. Dlatego nieprawidłowo w inny sposób wykonywania niektórych żądań, możesz dowiedzieć się czy jest ono spowodowane powolne odpowiedzi od zależności.

### <a name="profile-your-live-site"></a>Profil witryny na żywo

Nie wiadomo, gdzie przejdzie czasu? [Profiler usługi Application Insights](../../azure-monitor/app/profiler.md) ślady protokołu HTTP wywołania witryny na żywo i pokazuje, jakie funkcje w kodzie trwało najdłużej.

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



## <a name="custom-dependency-tracking"></a>Niestandardowe śledzenia zależności
Standardowy moduł śledzenia zależności automatycznie odnajduje zależności zewnętrznych, takich jak bazy danych i interfejsów API REST. Można jednak pewne dodatkowe składniki, aby być traktowane w taki sam sposób.

Można napisać kod, który wysyła informacje o zależnościach, korzystając z tych samych [TrackDependency API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency) używanego przez standardowe moduły.

Na przykład kompilowania kodu z zestawu, który nie zostały napisane samodzielnie, można czas wszystkie wywołania, aby dowiedzieć się, jakie wkład kieruje do Twoje czasy odpowiedzi. Aby uzyskać te dane wyświetlane na wykresach zależności w usłudze Application Insights, wyślij go za pomocą `TrackDependency`.

```csharp

            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
                // The call above has been made obsolete in the latest SDK. The updated call follows this format:
                // TrackDependency (string dependencyTypeName, string dependencyName, string data, DateTimeOffset startTime, TimeSpan duration, bool success);
            }
```

Jeśli chcesz wyłączyć modułu śledzenia zależności standardowe, Usuń odwołanie do DependencyTrackingTelemetryModule w [plik ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów
*Powodzenie zależności zawsze Flaga pokazuje wartość PRAWDA lub FAŁSZ.*

*Zapytania SQL nie są wyświetlane w całości.*

Zapoznaj się z poniższej tabeli, a następnie upewnij się, że wybrano prawidłowej konfiguracji, aby włączyć monitorowanie zależności aplikacji.

| Platforma | Instalowanie |
| --- | --- |
| Serwer usług IIS |Albo [Zainstaluj Monitor stanu na serwerze](../../azure-monitor/app/monitor-performance-live-website-now.md). Lub [uaktualnić aplikację .NET Framework 4.6 lub nowszy](https://go.microsoft.com/fwlink/?LinkId=528259) i zainstaluj [zestawu SDK usługi Application Insights](asp-net.md) w swojej aplikacji. |
| IIS Express |Zamiast tego użyj serwera IIS. |
| Aplikacja sieci Web platformy Azure |W Panelu sterowania aplikacji sieci web [otworzyć bloku usługi Application Insights w Panelu sterowania aplikacji sieci web](../../azure-monitor/app/azure-web-apps.md) i wybierz opcję instalacji, jeśli zostanie wyświetlony monit. |
| Azure Cloud Service |[Zadanie uruchamiania użyj](../../azure-monitor/app/cloudservices.md) lub [Instalowanie programu .NET framework 4.6 +](../../cloud-services/cloud-services-dotnet-install-dotnet.md). |

## <a name="next-steps"></a>Kolejne kroki
* [Wyjątki](../../azure-monitor/app/asp-net-exceptions.md)
* [Dane użytkownika i strony](../../azure-monitor/app/javascript.md)
* [Dostępność](../../azure-monitor/app/monitor-web-app-availability.md)
