---
title: Zależność śledzenia w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Analizowanie użycia, dostępności i wydajności aplikacji lokalnej lub aplikacji internetowej na platformie Microsoft Azure za pomocą usługi Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: mbullwin
ms.openlocfilehash: c54d8b870d6d8c187dcf145b56e3cbae257889a5
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51259296"
---
# <a name="set-up-application-insights-dependency-tracking"></a>Konfigurowanie usługi Application Insights: Śledzenie zależności
A *zależności* jest składnik zewnętrzny, która jest wywoływana przez aplikację. Zazwyczaj jest wywoływany przy użyciu protokołu HTTP lub bazy danych lub systemu plików usługi. [Usługa Application Insights](app-insights-overview.md) mierzy czas oczekiwania zależności aplikacji i jak często wywołanie zależności nie powiedzie się. Można zbadać określonych wywołań i dotyczą żądań i wyjątków.

![przykładowe wykresy](./media/app-insights-asp-net-dependencies/10-intro.png)

Monitor zależności poza pole zgłasza aktualnie wywołań do tych typów zależności:

* Serwer
  * Bazy danych SQL
  * Sieci web platformy ASP.NET i usługi WCF, które korzystają z powiązań oparty na protokole HTTP
  * Lokalnych lub zdalnych połączeń HTTP
  * Usługa Azure Cosmos DB, tabeli, magazynu obiektów blob i kolejki
* Strony sieci Web
  * Wywołania AJAX

Monitorowanie działania z zastosowaniem [Instrumentacji kodu bajtów](https://msdn.microsoft.com/library/z9z62c29.aspx) wokół wybranych metod. Obciążenie jest minimalne.

Można również napisać własne wywołania SDK, aby monitorować inne zależności, zarówno w kodzie klienta i serwera przy użyciu [TrackDependency API](app-insights-api-custom-events-metrics.md#trackdependency).

## <a name="set-up-dependency-monitoring"></a>Konfigurowanie monitorowania zależności
Informacje o zależnościach częściowe są zbierane automatycznie przez [zestawu SDK usługi Application Insights](app-insights-asp-net.md). Aby uzyskać kompletne dane, zainstaluj odpowiedniego agenta dla serwera hosta.

| Platforma | Instalowanie |
| --- | --- |
| Serwer usług IIS |Albo [Zainstaluj Monitor stanu na serwerze](app-insights-monitor-performance-live-website-now.md) lub [uaktualnić aplikację .NET Framework 4.6 lub nowszy](https://go.microsoft.com/fwlink/?LinkId=528259) i zainstaluj [zestawu SDK usługi Application Insights](app-insights-asp-net.md) w swojej aplikacji. |
| Aplikacja sieci Web platformy Azure |W Panelu sterowania aplikacji sieci web [otworzyć bloku usługi Application Insights w Panelu sterowania aplikacji sieci web](app-insights-azure-web-apps.md) i wybierz opcję instalacji, jeśli zostanie wyświetlony monit. |
| Usługa w chmurze platformy Azure |[Zadanie uruchamiania użyj](app-insights-cloudservices.md) lub [Instalowanie programu .NET framework 4.6 +](../cloud-services/cloud-services-dotnet-install-dotnet.md) |

## <a name="where-to-find-dependency-data"></a>Gdzie można znaleźć dane zależności
* [Mapa aplikacji](#application-map) wizualizuje zależności między aplikacji i składników sąsiednich.
* [Wydajność, przeglądarki i błędów bloków](#performance-and-blades) Pokaż dane zależności serwera.
* [Blok przeglądarki](#ajax-calls) przedstawia wywołania AJAX z przeglądarek użytkowników.
* [Przejść przez wolne lub zakończone niepowodzeniem żądania](#diagnose-slow-requests) do sprawdzenia ich zależności wywołań.
* [Analiza](#analytics) może służyć do zależności zapytania o dane.

## <a name="application-map"></a>Mapa aplikacji
Mapa aplikacji działa jako pomoc wizualną odnajdywanie zależności między składnikami aplikacji. Jest automatycznie generowany na podstawie danych telemetrycznych z Twojej aplikacji. Ten przykład przedstawia wywołania AJAX ze skryptów przeglądarki i wywołania REST z aplikacji serwera, z dwoma usługami zewnętrznymi.

![Mapa aplikacji](./media/app-insights-asp-net-dependencies/08.png)

* **Przejdź z pól** odpowiednie zależności i inne wykresy.
* **Przypnij mapę** do [pulpit nawigacyjny](app-insights-dashboards.md), gdzie będzie można w pełni funkcjonalne.

[Dowiedz się więcej](app-insights-app-map.md).

## <a name="performance-and-failure-blades"></a>Bloki wydajności i błędów
Blok wydajność pokazuje czas trwania wywołania zależności za pomocą aplikacji serwera. Brak podsumowania wykres i tabelę segmentowanych przez wywołanie.

![Wykresy zależności bloku wydajności](./media/app-insights-asp-net-dependencies/dependencies-in-performance-blade.png)

Klikaj elementy wykresów podsumowujących lub elementy tabeli wyszukiwania pierwotne wystąpień tych wywołań.

![Wystąpienia wywołania zależności](./media/app-insights-asp-net-dependencies/dependency-call-instance.png)

**Liczba awarii** są wyświetlane w **błędy** bloku. Błąd jest zwracany kod, który nie znajduje się w zakresie 200-399, lub nieznany.

> [!NOTE]
> **100% niepowodzeń?** -Prawdopodobnie wskazuje tylko otrzymujesz dane częściowe zależności. Musisz [skonfigurować monitorowanie zależności odpowiednie dla danej platformy](#set-up-dependency-monitoring).
>
>

## <a name="ajax-calls"></a>Wywołania AJAX
Blok przeglądarki przedstawia współczynnik czas trwania i niepowodzeń wywołań AJAX z [JavaScript na stronach sieci web](app-insights-javascript.md). Są one wyświetlane jako zależności.

## <a name="diagnosis"></a> Diagnozowanie wolne żądania
Każde zdarzenie żądania jest skojarzony z wywołania zależności, wyjątki i inne zdarzenia, które są śledzone podczas przetwarzania żądania przez aplikację. Dlatego nieprawidłowo w inny sposób wykonywania niektórych żądań, możesz dowiedzieć się czy jest ono spowodowane powolne odpowiedzi od zależności.

Przejdźmy teraz przez przykład.

### <a name="tracing-from-requests-to-dependencies"></a>Śledzenie za pomocą żądań do zależności
Otwórz blok wydajność i spójrz na siatce żądań:

![Lista żądań ze średnimi lub liczby](./media/app-insights-asp-net-dependencies/02-reqs.png)

Co najważniejsze trwa bardzo długo. Zobaczmy, jeśli firma Microsoft może Dowiedz się, gdzie jest zużywany.

Kliknij ten wiersz, aby wyświetlić pojedynczego żądania zdarzenia:

![Lista wystąpień na żądanie](./media/app-insights-asp-net-dependencies/03-instances.png)

Kliknij dowolne wystąpienie długotrwałych sprawdź go dalej i przewiń w dół do wywołania zdalnej zależności związane z tym żądaniem:

![Znajdź wywołania zależności zdalnych, identyfikowanie nietypowego czas trwania](./media/app-insights-asp-net-dependencies/04-dependencies.png)

Wygląda na to najbardziej obsługi czas, który tego żądania spędzono w wywołaniu do lokalnej usługi.

Wybierz ten wiersz, aby uzyskać więcej informacji:

![Klikaj elementy tej zdalnego zależności, aby zidentyfikować Przyczyna nadmiernego](./media/app-insights-asp-net-dependencies/05-detail.png)

Wygląda na to, gdzie jest problem. Firma Microsoft została przeprowadzana na ten problem, więc teraz możemy po prostu musisz dowiedzieć się, dlaczego to wywołanie jest trwa tak długo.

### <a name="request-timeline"></a>Osi czasu żądania
W przypadku różnych ma Brak wywołania zależności, który jest wyjątkowo długie. Jednak, przełączając się widok osi czasu, okaże się, gdy opóźnienie podczas naszego wewnętrznego przetwarzania:

![Znajdź wywołania zależności zdalnych, identyfikowanie nietypowego czas trwania](./media/app-insights-asp-net-dependencies/04-1.png)

Prawdopodobnie duży odstęp po pierwszą zależnością wywołać, więc należy przyjrzymy się naszego kodu, aby zobaczyć, dlaczego jest.

### <a name="profile-your-live-site"></a>Profil witryny na żywo

Nie wiadomo, gdzie przejdzie czasu? [Profiler usługi Application Insights](app-insights-profiler.md) ślady protokołu HTTP wywołania witryny na żywo i dowiesz się, jakie funkcje w kodzie trwało najdłużej.

## <a name="failed-requests"></a>Żądania zakończone niepowodzeniem
Żądania zakończone niepowodzeniem, może być również skojarzona z zakończonymi niepowodzeniem wywołaniami do zależności. Ponownie firma Microsoft może kliknij, aby śledzić problem.

![Kliknij wykres, żądań zakończonych niepowodzeniem](./media/app-insights-asp-net-dependencies/06-fail.png)

Kliknij, aby wystąpienia żądań zakończonych niepowodzeniem i spójrz na jego powiązanych zdarzeń.

![Kliknij typ żądania, kliknij wystąpienie aby przejść do innego widoku tego samego wystąpienia, kliknij je, aby uzyskać szczegóły wyjątku.](./media/app-insights-asp-net-dependencies/07-faildetail.png)

## <a name="analytics"></a>Analiza
Możesz śledzić zależności w [języka zapytań usługi Log Analytics](https://aka.ms/LogAnalyticsLanguage). Oto kilka przykładów.

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

Można napisać kod, który wysyła informacje o zależnościach, korzystając z tych samych [TrackDependency API](app-insights-api-custom-events-metrics.md#trackdependency) używanego przez standardowe moduły.

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

Jeśli chcesz wyłączyć modułu śledzenia zależności standardowe, Usuń odwołanie do DependencyTrackingTelemetryModule w [plik ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów
*Powodzenie zależności zawsze Flaga pokazuje wartość PRAWDA lub FAŁSZ.*

*Zapytania SQL nie są wyświetlane w całości.*

Zapoznaj się z poniższej tabeli, a następnie upewnij się, że wybrano prawidłowej konfiguracji, aby włączyć monitorowanie zależności aplikacji.

| Platforma | Instalowanie |
| --- | --- |
| Serwer usług IIS |Albo [Zainstaluj Monitor stanu na serwerze](app-insights-monitor-performance-live-website-now.md). Lub [uaktualnić aplikację .NET Framework 4.6 lub nowszy](https://go.microsoft.com/fwlink/?LinkId=528259) i zainstaluj [zestawu SDK usługi Application Insights](app-insights-asp-net.md) w swojej aplikacji. |
| Aplikacja sieci Web platformy Azure |W Panelu sterowania aplikacji sieci web [otworzyć bloku usługi Application Insights w Panelu sterowania aplikacji sieci web](app-insights-azure-web-apps.md) i wybierz opcję instalacji, jeśli zostanie wyświetlony monit. |
| Usługa w chmurze platformy Azure |[Zadanie uruchamiania użyj](app-insights-cloudservices.md) lub [Instalowanie programu .NET framework 4.6 +](../cloud-services/cloud-services-dotnet-install-dotnet.md) |

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Kolejne kroki
* [Wyjątki](app-insights-asp-net-exceptions.md)
* [Dane użytkownika i strony](app-insights-javascript.md)
* [Dostępność](app-insights-monitor-web-app-availability.md)
