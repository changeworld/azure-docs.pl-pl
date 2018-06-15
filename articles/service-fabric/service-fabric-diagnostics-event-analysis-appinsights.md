---
title: Analiza zdarzeń sieci szkieletowej usług Azure z usługą Application Insights | Dokumentacja firmy Microsoft
description: Więcej informacji na temat wizualizacji i analizowania zdarzeń za pomocą usługi Application Insights, monitorowania i diagnostyki klastrów sieci szkieletowej usług Azure.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/04/2018
ms.author: srrengar
ms.openlocfilehash: 29adf362fdacdb793af071fa6d7bd59214536374
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207757"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Analiza zdarzeń i wizualizacji z usługą Application Insights

Azure Application Insights to rozszerzalna platforma aplikacji monitorowania i diagnostyki. Obejmuje zaawansowane analizy i badania narzędzia, można dostosować pulpit nawigacyjny i wizualizacje i dalsze opcje w tym automatycznego tworzenia alertu. Jest zalecana platformy do monitorowania i diagnostyki dla aplikacji platformy Service Fabric i usług. W tym artykule opisano adresów poniższe często zadawane pytania

* Jak sprawdzić, co się dzieje w moich usług i aplikacji i zbieranie danych telemetrycznych
* Jak rozwiązywać Moja aplikacja szczególnie usług komunikowania się ze sobą
* Jak uzyskać metryk dotyczących sposobu moich usług są wykonywane, na przykład, czas ładowania strony, żądań http

Ten artykuł ma na celu pokazują, jak uzyskać wgląd i rozwiązywanie problemów z wewnątrz usługi App Insights. Jeśli chcesz dowiedzieć się, jak instalowanie i konfigurowanie AI z sieci szkieletowej usług, zapoznaj się to [samouczek](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-app-insights"></a>Monitorowanie usługi App Insights

Usługi Application Insights ma bogate możliwości pola, używając usługi sieć szkieletowa. Na stronie przeglądu AI zawiera najważniejsze informacje na temat usługi takie jak czas odpowiedzi i liczba przetwarzanych żądań. Klikając przycisk "Wyszukaj" u góry, lista ostatnich żądań w aplikacji. Ponadto będzie można zobaczyć tutaj nieudanych żądań i diagnozowanie, może wystąpić, jakie błędy.

![Omówienie AI](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

Na prawym panelu powyższej ilustracji, istnieją dwa główne typy wpisów na liście: żądań i zdarzeń. Żądania są wywołania interfejsu API aplikacji za pomocą żądań HTTP w tym przypadku, a zdarzenia są zdarzeń niestandardowych, które działają jako dane telemetryczne, które można dodać dowolne miejsce w kodzie. Można dalszą analizę aplikacji w Instrumentacji [aplikacji interfejsu API Insights dla niestandardowych zdarzeń i metryk](../application-insights/app-insights-api-custom-events-metrics.md). Kliknięcie na żądanie może wyświetlić więcej szczegółów jak pokazano na poniższej ilustracji, w tym dane specyficzne dla sieci szkieletowej usług, które są zbierane w sieci szkieletowej usług AI pakietu nuget. Tych informacji jest przydatna do rozwiązywania problemów i uzyskiwanie informacji stan aplikacji jest, a wszystkie te informacje można wyszukiwać w usłudze Application Insights

![Szczegóły żądania AI](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Usługi Application Insights ma widok wyznaczone do wykonywania zapytań w odniesieniu do wszystkich danych wchodzącej w. Kliknij przycisk "Eksploratora metryk" w górnej części strony Przegląd, aby przejść do portalu AI. W tym miejscu można uruchomić zapytania dla niestandardowych zdarzeń, o których wspomniano wcześniej, żądań, wyjątków, liczniki wydajności i innych metryk przy użyciu języka zapytań Kusto. Poniższy przykład przedstawia wszystkie żądania w ciągu ostatniej godziny.

![Szczegóły żądania AI](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Umożliwiający dalszą analizę możliwości portalu usługi App Insights, przejdź do [dokumentację portalu Application Insights](../application-insights/app-insights-dashboards.md).

### <a name="configuring-ai-with-wad"></a>Konfigurowanie AI z WAD

>[!NOTE]
>Dotyczy tylko klastry z systemem Windows w tej chwili.

Istnieją dwa podstawowe sposoby wysyłania danych z WAD do AI Azure, która jest to osiągane przez dodanie zbiornika AI w konfiguracji WAD, zgodnie z opisem w [w tym artykule](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

#### <a name="add-an-ai-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Dodaj klucz Instrumentacji AI, podczas tworzenia klastra w portalu Azure

![Dodawanie AIKey](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Podczas tworzenia klastra, jeśli włączono diagnostyki "Włączone", zostaną wyświetlone pole opcjonalne, aby wprowadzić klucz Instrumentacji wgląd w aplikacji. Po wklejeniu swój klucz AI zbiornika AI jest automatycznie konfigurowany dla Ciebie w szablonie usługi Resource Manager, który służy do wdrażania klastra.

#### <a name="add-the-ai-sink-to-the-resource-manager-template"></a>Dodaj obiekt Sink AI do szablonu usługi Resource Manager

W "WadCfg" w szablonie usługi Resource Manager należy dodać "Sink", umieszczając w niej następujące dwie zmiany:

1. Dodaj konfigurację zbiornika bezpośrednio po deklarowanie z `DiagnosticMonitorConfiguration` zostało zakończone:

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. Obejmują zbiornika w `DiagnosticMonitorConfiguration` , dodając następujący wiersz w `DiagnosticMonitorConfiguration` z `WadCfg` (tuż przed `EtwProviders` są deklarowane jako):

    ```json
    "sinks": "applicationInsights"
    ```

W obu powyższych fragmenty kodu nazwa "applicationInsights" została użyta do opisywania sink. Nie jest wymagane i tak długo, jak nazwa sink znajduje się w "sink", można ustawić nazwę do dowolnego ciągu.

Obecnie dzienników z klastra wyświetlane jako **śladów** w przeglądarce dzienników AI firmy. Ponieważ większość śladów pochodzących od platformy jest poziom "Informacyjny", użytkownik może należy również rozważyć zmianę konfiguracji zbiornika tylko do wysyłania dzienników typu "Krytyczne" lub "Error". Można to zrobić przez dodanie "węzła kanały" do obiektu sink, jak pokazano w [w tym artykule](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

>[!NOTE]
>Nieprawidłowy klucz AI można użyć w portalu lub w szablonie usługi Resource Manager, trzeba będzie ręcznie zmienić wartość klucza i zaktualizować klaster / wdrożenie.

### <a name="configuring-ai-with-eventflow"></a>Konfigurowanie AI z EventFlow

Jeśli używasz EventFlow celu agregowania zdarzeń, upewnij się, że importowanie `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`pakietu NuGet. Wymagany jest następujący kod w *generuje* sekcji *eventFlowConfig.json*:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Upewnij się wprowadzić wymagane zmiany w filtry, a także zawierać innych danych wejściowych (wraz z ich odpowiednich pakietów NuGet).

## <a name="aisdk"></a>AI. ZESTAW SDK

Zaleca się użyć EventFlow i WAD rozwiązań agregacji, ponieważ pozwalają one na bardziej podejściu Diagnostyka i monitorowanie, tj. Jeśli chcesz zmienić Twoje dane wyjściowe z EventFlow, nie wymaga nie zmieniono Twojego rzeczywiste Instrumentacji tylko prostą modyfikację do pliku konfiguracji. Jeśli jednak podjąć decyzję o inwestycji w za pomocą usługi Application Insights i prawdopodobnie nie można zmienić na innej platformie, należy rozważyć w celu agregowania zdarzeń i wysyłania ich do AI przy użyciu jego AI nowego zestawu SDK. Oznacza to, przestaną być skonfigurować EventFlow do wysyłania danych do AI, ale zamiast tego zostanie zainstalowany pakiet NuGet usługi Service Fabric ApplicationInsight. Szczegółowe informacje dotyczące pakietu można znaleźć [tutaj](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[Obsługa usługi Application Insights Mikrousług i kontenery](https://azure.microsoft.com/blog/app-insights-microservices/) przedstawiono niektóre nowe funkcje działające na (nadal obecnie w wersji beta), które umożliwiają są bardziej rozbudowane możliwości monitorowania poza pole z AI. Obejmują one śledzenia zależności (używanego podczas konstruowania AppMap wszystkich usług i aplikacji w klastrze i komunikację między nimi) oraz lepsze korelacji śladów pochodzących z Twoimi usługami (pomaga w lepiej trafić problemu w przepływie pracy programu aplikacji lub usługi).

Jeśli opracowujesz w .NET i zostanie przy użyciu niektóre usługi sieć szkieletowa programowania modeli i czy chce używać AI jako platformy do wizualizacji i analizowania danych zdarzeń i dzienników, następnie zaleca się możesz przejść za pomocą trasy AI SDK jako monitorowania i diagnos e przepływ pracy. Odczyt [to](../application-insights/app-insights-asp-net-more.md) i [to](../application-insights/app-insights-asp-net-trace-logs.md) wprowadzenie gromadzenie i wyświetlanie dzienników przy użyciu AI.

## <a name="navigating-the-ai-resource-in-azure-portal"></a>Nawigowanie po zasobu AI w portalu Azure

Po skonfigurowaniu AI jako dane wyjściowe dla zdarzeń i dzienniki informacji należy zacząć wyświetlani w zasobie AI za kilka minut. Przejdź do zasobu AI, co spowoduje przejście do pulpitu nawigacyjnego zasobów AI. Kliknij przycisk **wyszukiwania** AI pasku zadań, aby zobaczyć najnowsze dane śledzenia, które otrzymała i można filtrować za ich pośrednictwem.

*Eksploratora metryk* stanowi przydatne narzędzie do tworzenia niestandardowych pulpitów nawigacyjnych w oparciu metryki aplikacji, usług i klaster mogą raportowania. Zobacz [metryki Eksplorowanie w usłudze Application Insights](../application-insights/app-insights-metrics-explorer.md) do skonfigurowania kilku wykresów dla siebie na podstawie danych są zbierane.

Kliknięcie przycisku **Analytics** spowoduje przejście do portalu Application Insights Analytics, gdzie można zbadać zdarzenia i dane śledzenia z większą zakresu i opcjonalność. Dowiedz się więcej o tym w [analizy w usłudze Application Insights](../application-insights/app-insights-analytics.md).

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie alertów w AI](../application-insights/app-insights-alerts.md) Aby otrzymywać powiadomienia o zmianach wprowadzonych w wydajności i użycia
* [Inteligentne wykrywania w usłudze Application Insights](../application-insights/app-insights-proactive-diagnostics.md) wykonuje aktywnego analizy telemetrii są wysyłane do AI, aby ostrzec użytkownika o potencjalnych problemów z wydajnością
