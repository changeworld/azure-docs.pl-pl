---
title: Analiza zdarzeń sieci szkieletowej usługi Azure za pomocą usługi Application Insights
description: Dowiedz się więcej o wizualizacji i analizowaniu zdarzeń przy użyciu usługi Application Insights do monitorowania i diagnostyki klastrów sieci szkieletowej usług Azure.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 5c80dc9f350fec30469a9bce2ed836c276ef3a7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464759"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Analiza i wizualizacja zdarzeń za pomocą usługi Application Insights

Część usługi Azure Monitor, usługa Application Insights jest rozszerzalną platformą do monitorowania i diagnostyki aplikacji. Zawiera zaawansowane narzędzie analityczne i zapytania, konfigurowalny pulpit nawigacyjny i wizualizacje oraz inne opcje, w tym automatyczne alerty. Integracja usługi Application Insights z siecią szkieletową usług obejmuje środowisko narzędzi dla programu Visual Studio i witryny Azure portal, a także metryki specyficzne dla sieci szkieletowej usług, zapewniając kompleksowe środowisko rejestrowania gotowe do użycia. Chociaż wiele dzienników są automatycznie tworzone i zbierane dla Ciebie za pomocą usługi Application Insights, zaleca się dodanie dalszego niestandardowego rejestrowania do aplikacji, aby utworzyć bogatsze środowisko diagnostyczne.

Ten artykuł pomaga rozwiązać następujące typowe pytania:

* Skąd mam wiedzieć, co dzieje się w mojej aplikacji i usługach i zbierać dane telemetryczne?
* Jak rozwiązać problem z aplikacją, zwłaszcza usługami komunikującymi się ze sobą?
* Jak uzyskać metryki dotyczące wydajności moich usług, na przykład czasu ładowania strony, żądań HTTP?

Celem tego artykułu jest pokazanie, jak uzyskać szczegółowe informacje i rozwiązywać problemy z poziomu usługi Application Insights. Jeśli chcesz dowiedzieć się, jak skonfigurować i skonfigurować usługę Application Insights za pomocą sieci szkieletowej usług, zapoznaj się z tym [samouczkiem](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-application-insights"></a>Monitorowanie w usłudze Application Insights

Usługa Application Insights ma bogate środowisko po wyjęciu z pudełka podczas korzystania z sieci szkieletowej usług. Na stronie przegląd usługi Application Insights zawiera kluczowe informacje o usłudze, takie jak czas odpowiedzi i liczba przetworzonych żądań. Klikając przycisk "Szukaj" u góry, możesz zobaczyć listę ostatnich żądań w aplikacji. Ponadto można zobaczyć nieudane żądania w tym miejscu i zdiagnozować, jakie błędy mogły wystąpić.

![Omówienie usługi Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

Na prawym panelu na poprzednim obrazie znajdują się dwa główne typy wpisów na liście: żądania i zdarzenia. Żądania są wywołania do interfejsu API aplikacji za pośrednictwem żądań HTTP w tym przypadku, a zdarzenia są zdarzenia niestandardowe, które działają jako telemetrii można dodać w dowolnym miejscu w kodzie. Można dalej eksplorować instrumentowanie aplikacji w [interfejsie API usługi Application Insights dla niestandardowych zdarzeń i metryk.](../azure-monitor/app/api-custom-events-metrics.md) Kliknięcie żądania spowoduje wyświetlenie dalszych szczegółów, jak pokazano na poniższej ilustracji, w tym danych specyficznych dla sieci szkieletowej usług, która jest zbierana w pakiecie nuget sieci szkieletowej usługi application insights. Te informacje są przydatne do rozwiązywania problemów i wiedzy o stanie aplikacji, a wszystkie te informacje można przeszukiwać w usłudze Application Insights

![Szczegóły żądania wglądu w aplikacje](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Usługa Application Insights ma wyznaczony widok do wykonywania zapytań dotyczących wszystkich danych, które są w danym. Kliknij "Eksplorator metryk" w górnej części strony Przegląd, aby przejść do portalu usługi Application Insights. W tym miejscu można uruchamiać kwerendy dotyczące zdarzeń niestandardowych wymienionych wcześniej, żądań, wyjątków, liczników wydajności i innych metryk przy użyciu języka zapytań Kusto. Poniższy przykład pokazuje wszystkie żądania w ciągu ostatniej godziny.

![Szczegóły żądania wglądu w aplikacje](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Aby dokładniej zbadać możliwości portalu usługi Application Insights, przejdź do [dokumentacji portalu usługi Application Insights.](../azure-monitor/app/app-insights-dashboards.md)

### <a name="configuring-application-insights-with-eventflow"></a>Konfigurowanie usługi Application Insights za pomocą usługi EventFlow

Jeśli używasz EventFlow do agregowania zdarzeń, upewnij się, aby zaimportować pakiet `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights`NuGet. Następujący kod jest wymagany w sekcji *wyjścia* *eventFlowConfig.json:*

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Upewnij się, aby wprowadzić wymagane zmiany w filtrach, a także dołączyć inne dane wejściowe (wraz z ich odpowiednich pakietów NuGet).

## <a name="application-insights-sdk"></a>Application Insights SDK

Zaleca się stosowanie EventFlow i WAD jako rozwiązań agregacji, ponieważ pozwalają one na bardziej modułowe podejście do diagnostyki i monitorowania, tj. prosta modyfikacja pliku konfiguracyjnego. Jeśli jednak zdecydujesz się zainwestować w korzystanie z usługi Application Insights i prawdopodobnie nie zmienisz się na inną platformę, należy przyjrzeć się użyciu nowego SDK usługi Application Insights do agregowania zdarzeń i wysyłania ich do usługi Application Insights. Oznacza to, że nie trzeba będzie już konfigurować EventFlow do wysyłania danych do usługi Application Insights, ale zamiast tego zainstaluje pakiet NuGet sieci szkieletowej usługi ApplicationInsight. Szczegóły dotyczące pakietu można znaleźć [tutaj](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[Usługa Application Insights obsługuje mikrousług i kontenerów](https://azure.microsoft.com/blog/app-insights-microservices/) pokazuje niektóre z nowych funkcji, które są opracowywane (obecnie nadal w wersji beta), które pozwalają mieć bogatsze out-of-the-box opcji monitorowania z application insights. Należą do nich śledzenie zależności (używane przy tworzeniu AppMap wszystkich usług i aplikacji w klastrze i komunikacji między nimi) i lepsza korelacja śladów pochodzących z usług (pomaga w lepszym wskazywanie problemu w przepływie pracy aplikacji lub usługi).

Jeśli tworzysz w .NET i prawdopodobnie będziesz używać niektórych modeli programowania sieci szkieletowej usług i chcesz używać usługi Application Insights jako platformy do wizualizacji i analizowania danych zdarzeń i dzienników, zalecamy przejść przez usługę Application Insights Trasa SDK jako przepływ pracy monitorowania i diagnostyki. Przeczytaj [tę](../azure-monitor/app/asp-net-more.md) i [tę,](../azure-monitor/app/asp-net-trace-logs.md) aby rozpocząć korzystanie z usługi Application Insights do zbierania i wyświetlania dzienników.

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Nawigowanie po zasobie usługi Application Insights w witrynie Azure portal

Po skonfigurowaniu usługi Application Insights jako dane wyjściowe dla zdarzeń i dzienników informacje powinny rozpocząć się pojawiać w zasobie usługi Application Insights w ciągu kilku minut. Przejdź do zasobu usługi Application Insights, który spowoduje przejście do pulpitu nawigacyjnego zasobów usługi Application Insights. Kliknij **przycisk Wyszukaj** na pasku zadań usługi Application Insights, aby wyświetlić najnowsze ślady, które otrzymał, i aby móc je filtrować.

*Eksplorator metryk* jest użytecznym narzędziem do tworzenia niestandardowych pulpitów nawigacyjnych na podstawie metryk, które mogą być raportowane przez aplikacje, usługi i klaster. Zobacz [Eksplorowanie metryk w usłudze Application Insights,](../azure-monitor/app/metrics-explorer.md) aby skonfigurować kilka wykresów dla siebie na podstawie zbieranych danych.

Kliknięcie **analytics** spowoduje, że przejdziesz do portalu analizy usługi Application Insights, gdzie możesz wysyłać zapytania o zdarzenia i ślady z większym zakresem i opcjonalnością. Więcej informacji na ten temat można przeczytać w [analytics w usłudze Application Insights](../azure-monitor/app/analytics.md).

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie alertów w si.](../azure-monitor/app/alerts.md)
* [Inteligentne wykrywanie w usłudze Application Insights](../azure-monitor/app/proactive-diagnostics.md) przeprowadza proaktywną analizę danych telemetrycznych wysyłanych do usługi Application Insights, aby ostrzec o potencjalnych problemach z wydajnością
