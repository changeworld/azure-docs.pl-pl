---
title: Witryna sieci szkieletowej usług Azure— diagnozowanie typowych scenariuszy
description: Dowiedz się więcej o rozwiązywaniu typowych scenariuszy monitorowania i diagnostyki w aplikacjach sieci szkieletowej usług Azure.
ms.topic: article
ms.date: 02/25/2019
ms.openlocfilehash: 3c7f027bad71d48db5fba002f778f23db8225fa5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906953"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Diagnozowanie typowych scenariuszy za pomocą sieci szkieletowej usług

W tym artykule przedstawiono typowe scenariusze, które użytkownicy napotkali w obszarze monitorowania i diagnostyki za pomocą sieci szkieletowej usług. Przedstawione scenariusze obejmują wszystkie 3 warstwy sieci szkieletowej usług: application, cluster i infrastructure. Każde rozwiązanie używa usługi Application Insights i Azure Monitor dzienników, narzędzia do monitorowania platformy Azure, aby zakończyć każdy scenariusz. Kroki w każdym rozwiązaniu dać użytkownikom wprowadzenie na temat korzystania z usługi Application Insights i dzienników usługi Azure Monitor w kontekście sieci szkieletowej usług.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-recommendations"></a>Wymagania wstępne i zalecenia

Rozwiązania w tym artykule będą korzystać z następujących narzędzi. Zalecamy skonfigurowanie i skonfigurowanie następujących konfigurowań:

* [Usługa Application Insights z siecią szkieletową usług](service-fabric-tutorial-monitoring-aspnet.md)
* [Włącz diagnostykę platformy Azure w klastrze](service-fabric-diagnostics-event-aggregation-wad.md)
* [Konfigurowanie obszaru roboczego usługi Log Analytics](service-fabric-diagnostics-oms-setup.md)
* [Agent analizy dzienników do śledzenia liczników wydajności](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Jak mogę zobaczyć nieobsługiwalne wyjątki w mojej aplikacji?

1. Przejdź do zasobu usługi Application Insights, za pomocą których jest skonfigurowana aplikacja.
2. Kliknij *wyszukiwanie* w lewym górnym rogu. Następnie kliknij filtr na następnym panelu.

    ![Przegląd AI](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Zobaczysz wiele typów zdarzeń (ślady, żądania, zdarzenia niestandardowe). Wybierz "Wyjątek" jako filtr.

    ![Lista filtrów AI](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Klikając wyjątek na liście, można spojrzeć na więcej szczegółów, w tym kontekstu usługi, jeśli używasz SDK usługi analizy aplikacji sieci szkieletowej.

    ![Wyjątek dotyczący AI](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Jak wyświetlić, które wywołania HTTP są używane w moich usługach?

1. W tym samym zasobie usługi Application Insights można filtrować według "żądań" zamiast wyjątków i wyświetlać wszystkie żądania
2. Jeśli używasz zestawu SDK usługi Sieci szkieletowej aplikacji, można zobaczyć wizualną reprezentację usług połączonych ze sobą oraz liczbę żądań, które zakończyły się powodzeniem i niepowodzeniem. Po lewej stronie kliknij "Mapa aplikacji"

    ![AI App](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![Map Blade AI App Mapa](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Aby uzyskać więcej informacji na temat mapy aplikacji, odwiedź [dokumentację mapy aplikacji](../azure-monitor/app/app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Jak utworzyć alert, gdy węzeł ulegnie upadkowi

1. Zdarzenia węzłów są śledzone przez klaster sieci szkieletowej usług. Przejdź do zasobu rozwiązania usługi Analizy sieci szkieletowej o nazwie **ServiceFabric(NameofResourceGroup)**
2. Kliknij na wykres na dole ostrza zatytułowany "Podsumowanie"

    ![Rozwiązanie dziennika usługi Azure Monitor](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Tutaj masz wiele wykresów i kafelków wyświetlających różne metryki. Kliknij na jeden z wykresów i zabierze Cię do wyszukiwania dzienników. W tym miejscu można zbadać wszelkie zdarzenia klastra lub liczniki wydajności.
4. Wprowadź następującą kwerendę. Te identyfikatory zdarzeń znajdują się w [odwołaniu do zdarzeń węzła](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID >= 25622 and EventID <= 25626
    ```

5. Kliknij "Nowa reguła alertu" u góry, a teraz w każdej chwili zdarzenie nadejdzie na podstawie tej kwerendy, otrzymasz alert w wybranej metodzie komunikacji.

    ![Monitor Platformy Azure rejestruje nowy alert](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Jak mogę otrzymywać powiadomienia o wycofywaniu uaktualnień aplikacji?

1. W tym samym oknie wyszukiwania dziennika, jak przed wprowadzeniem następującej kwerendy dla wycofywania uaktualnień. Te identyfikatory zdarzeń znajdują się w obszarze [Odwołanie do zdarzeń aplikacji](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID == 29623 or EventID == 29624
    ```

2. Kliknij "Nowa reguła alertu" u góry, a teraz w każdej chwili zdarzenie nadejdzie na podstawie tej kwerendy, otrzymasz alert.

## <a name="how-do-i-see-container-metrics"></a>Jak wyświetlić metryki kontenera?

W tym samym widoku ze wszystkimi wykresami zobaczysz kilka kafelków dla wydajności kontenerów. Potrzebujesz agenta analizy dzienników i [rozwiązania do monitorowania kontenerów](service-fabric-diagnostics-oms-containers.md) dla tych kafelków do wypełniania.

![Metryki kontenera analizy dzienników](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>Aby przywdyskuwać dane telemetryczne z **wnętrza** kontenera, należy dodać [pakiet nuget usługi Application Insights dla kontenerów.](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios)

## <a name="how-can-i-monitor-performance-counters"></a>Jak monitorować liczniki wydajności?

1. Po dodaniu agenta usługi Log Analytics do klastra należy dodać określone liczniki wydajności, które chcesz śledzić. Przejdź do strony obszaru roboczego usługi Log Analytics w portalu — na stronie rozwiązania karta obszaru roboczego znajduje się w menu po lewej stronie.

    ![Karta Obszar roboczy analizy dzienników](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. Gdy jesteś na stronie obszaru roboczego, kliknij na "Ustawienia zaawansowane" w tym samym menu po lewej stronie.

    ![Ustawienia zaawansowane usługi Log Analytics](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Kliknij liczniki wydajności danych > systemu Windows (liczniki wydajności danych > linux dla komputerów z systemem Linux), aby rozpocząć zbieranie określonych liczników z węzłów za pośrednictwem agenta usługi Log Analytics. Oto przykłady formatu liczników do dodania

   * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
   * `Processor(_Total)\\% Processor Time`

     W przewodnikach Szybki start, VotingData i VotingWeb są używane nazwy procesów, więc śledzenie tych liczników będzie wyglądać

   * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
   * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

     ![Liczniki wydajności analizy dzienników](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. Dzięki temu można zobaczyć, jak infrastruktura obsługuje obciążenia i ustawić odpowiednie alerty na podstawie wykorzystania zasobów. Na przykład — można ustawić alert, jeśli całkowite wykorzystanie procesora przekracza 90% lub poniżej 5%. Nazwa licznika, której można użyć w tym celu, to "% czasu procesora". Można to zrobić, tworząc regułę alertu dla następującej kwerendy:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Jak śledzić wydajność moich niezawodnych usług i aktorów?

Aby śledzić wydajność niezawodnych usług lub aktorów w aplikacjach, należy również zebrać liczniki aktora sieci szkieletowej usług, metody aktora, usługi i metody usługi. Oto przykłady niezawodnych liczników wydajności usług i aktorów do zbierania

>[!NOTE]
>Liczniki wydajności sieci szkieletowej usług nie mogą być obecnie zbierane przez agenta usługi Log Analytics, ale mogą być zbierane przez [inne rozwiązania diagnostyczne](service-fabric-diagnostics-partners.md)

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Sprawdź te linki, aby uzyskać pełną listę liczników wydajności na wiarygodne [usługi](service-fabric-reliable-serviceremoting-diagnostics.md) i [podmioty](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>Następne kroki

* [Szukaj typowych błędów aktywacji pakietu kodu](./service-fabric-diagnostics-code-package-errors.md)
* [Konfigurowanie alertów w si.](../azure-monitor/app/alerts.md)
* [Inteligentne wykrywanie w usłudze Application Insights](../azure-monitor/app/proactive-diagnostics.md) przeprowadza proaktywną analizę danych telemetrycznych wysyłanych do si.
* Dowiedz się więcej o dziennikach usługi Azure [Monitor, które pomagają](../log-analytics/log-analytics-alerts.md) w wykrywaniu i diagnostyce.
* W przypadku klastrów lokalnych dzienniki usługi Azure Monitor oferują bramę (http forward proxy), która może służyć do wysyłania danych do dzienników usługi Azure Monitor. Dowiedz się więcej o tym w [łączeniu komputerów bez dostępu do Internetu z dziennikami usługi Azure Monitor przy użyciu bramy usługi Log Analytics](../azure-monitor/platform/gateway.md)
* Zapoznaj się z funkcjami [wyszukiwania i wyszukiwania w dziennikach](../log-analytics/log-analytics-log-searches.md) oferowanych w ramach dzienników usługi Azure Monitor
* Uzyskaj bardziej szczegółowe omówienie dzienników usługi Azure Monitor i jej oferty, przeczytaj Co to [są dzienniki usługi Azure Monitor?](../operations-management-suite/operations-management-suite-overview.md)
