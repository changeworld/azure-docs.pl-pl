---
title: Analiza zdarzeń sieci szkieletowej usługi platformy Azure z usługą Application Insights | Dokumentacja firmy Microsoft
description: Więcej informacji na temat wizualizowanie i analizowanie zdarzeń za pomocą usługi Application Insights do monitorowania i diagnostyki klastrów usługi Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: f4c620bbb0e17abfacb504866230786a971ff409
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60393199"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Analiza zdarzeń i wizualizacji przy użyciu usługi Application Insights

Część usługi Azure Monitor usługi Application Insights to rozszerzalna platforma do monitorowania i diagnostyki aplikacji. Zawiera zaawansowane funkcje analityczne i wysyłania zapytań do narzędzia, możliwe do dostosowania pulpitu nawigacyjnego i wizualizacje i dalsze opcje w tym automatyczne alerty. Integracja usługi Application Insights z usługą Service Fabric zawiera narzędzia środowiska dla programu Visual Studio i platforma Azure portal, a także określonych metryk usługi Service Fabric, zapewniają środowisko kompleksowe rejestrowania poza pole. Chociaż wiele dzienników są automatycznie tworzone i zebrane dla Ciebie za pomocą usługi Application Insights, zaleca się dodanie dalsze niestandardowego rejestrowania aplikacji, aby utworzyć bardziej zaawansowane środowisko diagnostyki.

Ten artykuł pomaga rozwiązać poniższe często zadawane pytania:

* Jak sprawdzić, co się dzieje w mojej aplikacji i usług oraz zbieranie danych telemetrycznych?
* Jak rozwiązywać mojej aplikacji, szczególnie usług podczas komunikowania się ze sobą?
* Jak uzyskać metryki dotyczące jak Moje usługi działają, na przykład czas ładowania strony, żądań HTTP?

Ten artykuł ma na celu przedstawiają sposób uzyskiwania szczegółowych informacji i rozwiązywanie problemów z w ramach usługi Application Insights. Jeśli chcesz dowiedzieć się, jak instalowanie i konfigurowanie usługi Application Insights z usługą Service Fabric, zapoznaj się z tą [samouczek](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-application-insights"></a>Monitorowanie w usłudze Application Insights

Usługa Application Insights ma rozbudowane możliwości pole, korzystając z usługi Service Fabric. Na stronie przeglądu usługi Application Insights zawiera najważniejsze informacje na temat usługi, takie jak czas odpowiedzi i liczba żądań przetworzonych. Klikając przycisk "Wyszukaj" u góry, lista ostatnich żądań w aplikacji. Ponadto będzie mógł zobaczyć tutaj żądań zakończonych niepowodzeniem i diagnozowanie w zidentyfikowaniu błędów, które mogą wystąpić.

![Omówienie usługi Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

Na prawym panelu na wcześniejszej ilustracji, istnieją dwa główne rodzaje wpisy na liście: żądania i zdarzenia. Żądania to wywołania interfejsu API aplikacji, za pomocą żądań HTTP w tym przypadku i zdarzenia są zdarzenia niestandardowe, które działają jako dane telemetryczne, które można dodać w dowolnym miejscu w kodzie. Możesz bliżej zapoznać się z aplikacji w Instrumentacji [interfejsu API usługi Application Insights dla niestandardowych zdarzeń i metryk](../azure-monitor/app/api-custom-events-metrics.md). Kliknięcie na żądanie zostanie wyświetlony dalsze szczegółowe informacje jak pokazano na poniższej ilustracji, w tym dane specyficzne dla usługi Service Fabric, które są zbierane w pakiecie nuget Application Insights Service Fabric. Te informacje jest przydatne w rozwiązywaniu problemów i stan aplikacji jest, a wszystkie te informacje można wyszukiwać w usłudze Application Insights

![Szczegóły żądania szczegółowych informacji w aplikacji](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Usługa Application Insights ma wyznaczony widok do wykonywania zapytań względem wszystkich danych, których można użyć w. Kliknij przycisk "Eksploratora metryk" w górnej części strony Przegląd, aby przejść do portalu usługi Application Insights. W tym miejscu można uruchomić zapytania dla niestandardowych zdarzeń, o których wspomniano wcześniej, żądań, wyjątków, liczniki wydajności i innych metryk za pomocą języka zapytania Kusto. Poniższy przykład pokazuje wszystkich żądań w ciągu ostatniej godziny.

![Szczegóły żądania szczegółowych informacji w aplikacji](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Aby dokładniej zbadać możliwości portalu usługi Application Insights, przejdź do [dokumentacja portalu usługi Application Insights](../azure-monitor/app/app-insights-dashboards.md).

### <a name="configuring-application-insights-with-eventflow"></a>Konfigurowanie usługi Application Insights przy użyciu struktury EventFlow

Jeśli używasz użyciu struktury EventFlow w celu agregowania zdarzeń, upewnij się, że importowanie `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`pakietu NuGet. Poniższy kod jest wymagany w *generuje* części *eventFlowConfig.json*:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Pamiętaj wprowadzić wymagane zmiany w filtry, a także zawierać pozostałych danych wejściowych (wraz z ich odpowiednich pakietów NuGet).

## <a name="application-insights-sdk"></a>Zestaw SDK usługi Application Insights

Zaleca się użyć użyciu struktury EventFlow i WAD rozwiązania agregacji, ponieważ umożliwiają one bardziej modułowej podejścia do diagnostyki i monitorowania, czyli jeśli chcesz zmienić swoje dane wyjściowe z użyciu struktury EventFlow, nie wymaga bez zmian do Twojego rzeczywistego Instrumentacji po prostu prostą modyfikację do pliku konfiguracji. Jeśli jednak podjąć decyzję o wartości inwestycji w za pomocą usługi Application Insights i prawdopodobnie nie można zmienić na innej platformie, powinien wyglądać w celu agregowania zdarzeń i wysyła je do usługi Application Insights, używając nowego zestawu SDK usługi Application Insights. Oznacza to, nie będzie miało skonfigurować użyciu struktury EventFlow to wysyłanie danych do usługi Application Insights, ale zamiast tego zostanie zainstalowany pakiet NuGet usługi Service Fabric ApplicationInsight. Można znaleźć szczegółowe informacje na temat pakietu [tutaj](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[Usługa Application Insights Obsługa Mikrousług i kontenerów](https://azure.microsoft.com/blog/app-insights-microservices/) przedstawia niektóre z nowych funkcji, które są opracowywane (nadal obecnie w wersji beta), które umożliwiają bardziej zaawansowane opcje monitorowania out-of--box z usługą Application Insights. Obejmują one śledzenia zależności (używane w budynku AppMap usług i aplikacji w klastrze i komunikację między nimi) i lepiej korelacji ślady przychodzące z usług (pomaga w lepszej precyzyjne określenie problemu w przepływie pracy programu Aplikacja lub usługa).

Jeśli opracowujesz na platformie .NET i będą najprawdopodobniej używać niektórych modeli programowania usługi Service Fabric i chcesz używać usługi Application Insights jako platformy umożliwiające wizualizowanie i analizowanie danych zdarzeń i dzienników, zalecamy możesz przejść za pomocą usługi Application Insights Zestaw SDK trasa jako przepływ pracy monitorowania i diagnostyki. Odczyt [to](../azure-monitor/app/asp-net-more.md) i [to](../azure-monitor/app/asp-net-trace-logs.md) wprowadzenie przy użyciu usługi Application Insights umożliwiają zbieranie i wyświetlanie dzienników.

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Przejdź do zasobu usługi Application Insights w witrynie Azure portal

Po skonfigurowaniu usługi Application Insights jako dane wyjściowe dla zdarzeń i dzienniki na początek informacje do wyświetlenia na zasób usługi Application Insights w ciągu kilku minut. Przejdź do zasobu usługi Application Insights, co spowoduje przejście do pulpitu nawigacyjnego zasobu usługi Application Insights. Kliknij przycisk **wyszukiwania** na pasku usługi Application Insights, aby zobaczyć najnowsze dane śledzenia, które otrzymała i aby można było filtrować je.

*Eksplorator metryk* jest użytecznym narzędziem do tworzenia niestandardowych pulpitów nawigacyjnych na podstawie metryk, że aplikacje, usługi i klastra może raportowanie. Zobacz [eksplorowania metryk w usłudze Application Insights](../azure-monitor/app/metrics-explorer.md) skonfigurować kilka wykresów dla siebie na podstawie danych są zbierane.

Klikając **Analytics** spowoduje przejście do portalu analiza usługi Application Insights, gdzie można tworzyć zapytania zdarzeniami i śladami większy zakres i opcjonalność. Dowiedz się więcej o [Analytics w usłudze Application Insights](../azure-monitor/app/analytics.md).

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie alertów związanych ze sztuczną Inteligencją](../azure-monitor/app/alerts.md) Aby otrzymywać powiadomienia o zmianach wprowadzonych w wydajności lub użycia
* [Inteligentne wykrywanie w usłudze Application Insights](../azure-monitor/app/proactive-diagnostics.md) wykonuje aktywnego analizę danych telemetrycznych wysyłanych do usługi Application Insights, aby ostrzec o potencjalne problemy z wydajnością
