---
title: Zależności śledzenia w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: f2ebd2a021d3803b6e3f7d805b9253d181cb16c3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293643"
---
# <a name="set-up-application-insights-dependency-tracking"></a>Skonfiguruj usługę Application Insights: Śledzenie zależności
A *zależności* jest składnik zewnętrzny, który jest wywoływany przez aplikację. Usługa ta jest zazwyczaj wywoływana przy użyciu protokołu HTTP, lub bazy danych lub systemu plików. [Usługa Application Insights](app-insights-overview.md) mierzy czas oczekiwania zależności aplikacji i jak często wywołanie zależności nie powiedzie się. Zbadaj określonych wywołań i dotyczą żądań i wyjątki.

![przykładowe wykresy](./media/app-insights-asp-net-dependencies/10-intro.png)

Monitor zależności poza pole raportów obecnie wywołań do tych typów zależności:

* Serwer
  * Bazy danych SQL
  * Sieci web ASP.NET i usługi WCF, które używają powiązania oparte na protokole HTTP
  * Lokalne lub zdalne wywołania HTTP
  * Azure DB rozwiązania Cosmos, tabeli magazynu obiektów blob i kolejki
* Strony sieci Web
  * Wywołania AJAX

Monitorowanie działania za pomocą [Instrumentacji kodu bajtów](https://msdn.microsoft.com/library/z9z62c29.aspx) wokół wybrane metody zostaną usunięte. Obciążenie jest minimalne.

Można również napisać własny wywołania SDK do monitorowania innych zależności, zarówno w kodzie klienta i serwera przy użyciu [TrackDependency API](app-insights-api-custom-events-metrics.md#trackdependency).

## <a name="set-up-dependency-monitoring"></a>Konfigurowanie monitorowania zależności
Informacje o zależnościach częściowe są zbierane automatycznie przez [zestaw SDK usługi Application Insights](app-insights-asp-net.md). Aby uzyskać pełne dane, zainstalowanie odpowiedniego agenta dla serwera hosta.

| Platforma | Instalowanie |
| --- | --- |
| Serwer usług IIS |Albo [Zainstaluj Monitor stanu na serwerze](app-insights-monitor-performance-live-website-now.md) lub [uaktualnić aplikacji .NET Framework 4.6 lub nowszy](http://go.microsoft.com/fwlink/?LinkId=528259) i zainstaluj [zestaw SDK usługi Application Insights](app-insights-asp-net.md) w aplikacji. |
| Aplikacja sieci Web platformy Azure |W Panelu sterowania aplikacji sieci web [otwarcie bloku usługi Application Insights w Panelu sterowania aplikacji sieci web](app-insights-azure-web-apps.md) i wybierz opcję instalacji, jeśli zostanie wyświetlony monit. |
| Usługi w chmurze Azure |[Zadanie uruchamiania użyj](app-insights-cloudservices.md) lub [zainstalowania środowiska .NET framework 4.6 +](../cloud-services/cloud-services-dotnet-install-dotnet.md) |

## <a name="where-to-find-dependency-data"></a>Gdzie można znaleźć danych zależności
* [Mapowanie aplikacji](#application-map) wizualizuje zależności między aplikacji i składników sąsiednich.
* [Bloki wydajności, przeglądarki oraz Niepowodzenie](#performance-and-blades) przedstawiono dane zależności serwera.
* [Blok przeglądarki](#ajax-calls) pokazuje wywołania AJAX z przeglądarki użytkownika.
* [Kliknij go, z powolnym działaniem lub nieudanych żądań](#diagnose-slow-requests) do sprawdzania ich zależności wywołania.
* [Analiza](#analytics) można wykonać zapytania o dane zależności.

## <a name="application-map"></a>Mapa aplikacji
Mapowanie aplikacji działa jako pomoc wizualną odnajdywanie zależności między składnikami aplikacji. Jest automatycznie generowany na podstawie danych telemetrycznych z aplikacji. Ten przykład przedstawia wywołania AJAX ze skryptów przeglądarki i wywołania REST z aplikacji serwera do dwóch usług zewnętrznych.

![Mapa aplikacji](./media/app-insights-asp-net-dependencies/08.png)

* **Przejdź z pól** do odpowiednich zależności i innych wykresów.
* **Przypnij mapę** do [pulpitu nawigacyjnego](app-insights-dashboards.md), gdzie będzie pełną funkcjonalność.

[Dowiedz się więcej](app-insights-app-map.md).

## <a name="performance-and-failure-blades"></a>Wydajność i niepowodzenie bloków
Blok wydajności przedstawia czas trwania wywołania zależności za pomocą aplikacji serwera. Brak podsumowania wykres i tabelę segmentowanych przez wywołanie.

![Wykresy zależności bloku wydajności](./media/app-insights-asp-net-dependencies/dependencies-in-performance-blade.png)

Kliknij wykresy podsumowań lub elementów tabeli wyszukiwania raw wystąpień tych wywołań.

![Wystąpienia wywołanie zależności](./media/app-insights-asp-net-dependencies/dependency-call-instance.png)

**Liczba awarii** są wyświetlane na **błędów** bloku. Błąd jest kod powrotny, który nie znajduje się w zakresie 200 – 399, lub nieznany.

> [!NOTE]
> **100% błędów?** -Prawdopodobnie oznacza to są dane z częściowa zależności tylko pierwsze. Musisz [Konfigurowanie zależności monitorowania odpowiednią dla danej platformy](#set-up-dependency-monitoring).
>
>

## <a name="ajax-calls"></a>Wywołania AJAX
Blok przeglądarki przedstawia współczynnik czas trwania i Niepowodzenie wywołania AJAX z [JavaScript na stronach sieci web](app-insights-javascript.md). Są wyświetlane jako zależności.

## <a name="diagnosis"></a> Diagnozowanie powolne żądań
Każde zdarzenie żądania jest skojarzony z wywołania zależności, wyjątków i inne zdarzenia, które są śledzone podczas przetwarzania żądania aplikacji. Dlatego niektórych żądań są wykonywane nieprawidłowo, można ustalić czy jest ze względu na wolne odpowiedzi z zależności.

Przejdźmy przykład tego.

### <a name="tracing-from-requests-to-dependencies"></a>Śledzenie żądań zależności
Otwarcie bloku wydajności i przyjrzyj się siatki żądania:

![Lista żądań ze średnimi lub liczby](./media/app-insights-asp-net-dependencies/02-reqs.png)

Elementem najwyższego trwa bardzo długo. Zobaczmy, jeśli firma Microsoft można ustalić, gdzie jest zużywany czas.

Kliknij ten wiersz, aby wyświetlić poszczególne żądania zdarzenia:

![Lista wystąpień żądania](./media/app-insights-asp-net-dependencies/03-instances.png)

Kliknij pozycję dowolnego wystąpienia długotrwałe, aby sprawdzić dodatkowe, a następnie przewiń w dół do połączenia zdalnego zależności powiązane z tym żądaniem:

![Znajdź wywołania zależności zdalnych, zidentyfikuj nietypowe czas trwania](./media/app-insights-asp-net-dependencies/04-dependencies.png)

Wygląda jak większość obsługi czasu poświęconego tego żądania podczas wywołania usługi lokalnej.

Wybierz ten wiersz, aby uzyskać więcej informacji:

![Kliknij go, że zdalnego zależności do identyfikowania dziedziczonej z istotnymi elementami](./media/app-insights-asp-net-dependencies/05-detail.png)

Wygląda na to, gdzie jest problem. Firma Microsoft już przeprowadzana na ten problem, więc teraz możemy just należy dowiedzieć się, dlaczego tego wywołania trwa tak długo.

### <a name="request-timeline"></a>Oś czasu żądania
W przypadku różnych nie ma żadnych wywołanie zależności, które są szczególnie długie. Jednak przełączyć do widoku osi czasu, możemy stwierdzić, gdzie wystąpił opóźnienie w naszym wewnętrzne przetwarzanie:

![Znajdź wywołania zależności zdalnych, zidentyfikuj nietypowe czas trwania](./media/app-insights-asp-net-dependencies/04-1.png)

Wydaje się duży odstęp po pierwszej zależności wywołać, dlatego należy przyjrzymy się naszego kodu, aby zobaczyć, dlaczego jest.

### <a name="profile-your-live-site"></a>Profil witryny na żywo

Nie wiadomo, gdzie przechodzi czas? [Profilera usługi Application Insights](app-insights-profiler.md) najdłużej trwało śladów HTTP wywołań witryny na żywo i zawiera funkcje, które w kodzie.

## <a name="failed-requests"></a>Żądania zakończone niepowodzeniem
Żądań zakończonych niepowodzeniem może też być skojarzone z niepowodzeniem wywołania zależności. Firma Microsoft ponownie, kliknij go, można wykrywać problem.

![Kliknij wykres nieudanych żądań](./media/app-insights-asp-net-dependencies/06-fail.png)

Kliknij, aby wystąpienie nieudanych żądań i przyjrzyj się jego skojarzonego zdarzenia.

![Kliknij typ żądania, kliknij wystąpienia, aby uzyskać dostęp do innego widoku tego samego wystąpienia, kliknij go, aby uzyskać szczegóły wyjątku.](./media/app-insights-asp-net-dependencies/07-faildetail.png)

## <a name="analytics"></a>Analiza
Można śledzić zależności w [języka zapytań usługi Analiza dzienników](https://docs.loganalytics.io/). Oto kilka przykładów.

* Znajdź wszystkie wywołania zależności nie powiodło się:

```

    dependencies | where success != "True" | take 10
```

* Znajdź wywołania AJAX:

```

    dependencies | where client_Type == "Browser" | take 10
```

* Znajdź związanych z żądaniami wywołania zależności:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Znajdź wywołania AJAX skojarzone z wyświetleń strony:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```



## <a name="custom-dependency-tracking"></a>Niestandardowe śledzenia zależności
Standardowy moduł śledzenia zależności automatycznie odnajduje zależności zewnętrzne, takie jak bazy danych i interfejsów API REST. Jednak może być niektóre dodatkowe składniki należy traktować w taki sam sposób.

Można napisać kod, który wysyła informacje o zależnościach, korzystającej z tego samego [TrackDependency API](app-insights-api-custom-events-metrics.md#trackdependency) używany przez standardowe moduły.

Na przykład jeśli kod jest kompilacji z zestawu, który nie jest pisana samodzielnie, można czasu wszystkie wywołania, aby dowiedzieć się, jakie wkład zgłasza Twoje czasy odpowiedzi. Te dane wyświetlane na wykresach zależności w usłudze Application Insights, aby wysyłać go za pomocą `TrackDependency`.

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

Jeśli chcesz wyłączyć modułu śledzenia zależności standardowe, Usuń odwołanie do DependencyTrackingTelemetryModule w [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów
*Powodzenie zależności Flaga zawsze wyświetla wartość PRAWDA lub FAŁSZ.*

*Zapytania SQL nie są wyświetlane w całości.*

Sprawdź informacje w poniższej tabeli i upewnić się, że wybrano prawidłowej konfiguracji, aby włączyć monitorowanie zależności aplikacji.

| Platforma | Instalowanie |
| --- | --- |
| Serwer usług IIS |Albo [Zainstaluj Monitor stanu na serwerze](app-insights-monitor-performance-live-website-now.md). Lub [uaktualnić aplikacji .NET Framework 4.6 lub nowszy](http://go.microsoft.com/fwlink/?LinkId=528259) i zainstaluj [zestaw SDK usługi Application Insights](app-insights-asp-net.md) w aplikacji. |
| Aplikacja sieci Web platformy Azure |W Panelu sterowania aplikacji sieci web [otwarcie bloku usługi Application Insights w Panelu sterowania aplikacji sieci web](app-insights-azure-web-apps.md) i wybierz opcję instalacji, jeśli zostanie wyświetlony monit. |
| Usługi w chmurze Azure |[Zadanie uruchamiania użyj](app-insights-cloudservices.md) lub [zainstalowania środowiska .NET framework 4.6 +](../cloud-services/cloud-services-dotnet-install-dotnet.md) |

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Kolejne kroki
* [Wyjątki](app-insights-asp-net-exceptions.md)
* [Dane użytkownika i strony](app-insights-javascript.md)
* [Dostępność](app-insights-monitor-web-app-availability.md)
