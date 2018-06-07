---
title: Sieć szkieletowa usług Azure zdiagnozować typowe scenariusze | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać typowe scenariusze z sieci szkieletowej usług Azure
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/16/2018
ms.author: srrengar
ms.openlocfilehash: bd7a7e0288ced0219a0600034b273d1acba6b09b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655743"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Zdiagnozować typowe scenariusze z sieci szkieletowej usług

W tym artykule przedstawiono typowe scenariusze, które napotkała użytkowników w zakresie monitorowania i diagnostyki z sieci szkieletowej usług. Przedstawione scenariusze obejmują wszystkie warstwy 3 sieci szkieletowej usług: aplikacji, klastra i infrastruktury. Każde rozwiązanie używa usługi Application Insights i analizy dzienników, Azure narzędzi do monitorowania, do ukończenia każdego scenariusza. Kroki opisane w poszczególnych rozwiązaniach zapewniają użytkownikom wprowadzenie na temat korzystania z usługi Application Insights i analizy dzienników w kontekście sieci szkieletowej usług.

## <a name="prerequisites-and-recommendations"></a>Wymagania wstępne i zalecenia

Rozwiązania w tym artykule będzie używać następujących narzędzi. Firma Microsoft zaleca się, że te zestawy się i skonfigurowane:

* [Usługa Application Insights z sieci szkieletowej usług](service-fabric-tutorial-monitoring-aspnet.md)
* [Włącz diagnostykę Azure w klastrze](service-fabric-diagnostics-event-aggregation-wad.md)
* [Konfigurowanie OMS obszaru roboczego analizy dzienników](service-fabric-diagnostics-oms-setup.md)
* [Agent pakietu OMS do śledzenia liczników wydajności](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Jak wyświetlić nieobsługiwanych wyjątków we własnej aplikacji

1. Przejdź do skonfigurowanej aplikacji z zasobu usługi Application Insights.
2. Polecenie *wyszukiwania* w lewej górnej części. Następnie kliknij przycisk Filtr na panelu dalej.

    ![Omówienie AI](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Zostanie wyświetlone wiele typów zdarzeń (śladów, żądania, zdarzenia niestandardowe). Wybierz pozycję "Wyjątku" jako filtr.

    ![Lista filtrów AI](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Klikając wyjątek na liście, można wyświetlać więcej szczegółów, łącznie z kontekstem usługi, jeśli używasz zestawu SDK usług sieci szkieletowej Application Insights.

    ![Wyjątek AI](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Jak wyświetlić HTTP wywołania są używane w moich usług?

1. W tym samym zasobu usługi Application Insights można filtrować według "żądań" zamiast wyjątki i wyświetlać wszystkich żądań
2. Jeśli korzystasz z zestawu SDK usług sieci szkieletowej Application Insights, można zobaczyć graficzną reprezentację połączone ze sobą usług, a liczba zakończyło się pomyślnie i żądań zakończonych niepowodzeniem. Po lewej stronie kliknij pozycję "Mapowanie aplikacji"

    ![Mapa aplikacji AI bloku](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![mapy aplikacji AI](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Aby uzyskać więcej informacji na mapie aplikacji, odwiedź stronę [dokumentacji mapowanie aplikacji](../application-insights/app-insights-app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Jak utworzyć alert, gdy węzeł ulegnie awarii

1. Węzeł zdarzenia są śledzone przez klaster sieci szkieletowej usług. Przejdź do zasobu rozwiązania analizy sieci szkieletowej usług o nazwie **ServiceFabric(NameofResourceGroup)**
2. Kliknij na wykresie w dolnej części bloku zatytułowany "Summary"

    ![Rozwiązanie OMS](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. W tym miejscu masz wiele wykresów i wyświetlanie różnych metryk Kafelki. Kliknij na jednym z wykresów i spowoduje przejście do wyszukiwania dziennika. W tym miejscu można wyszukać wszystkie liczniki wydajności lub zdarzenia klastra.
4. Wpisz poniższe zapytanie. Te identyfikatory zdarzeń znajdują się w [dotyczące zdarzeń węzła](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventId >= 25623 or EventId <= 25626
    ```

5. Kliknij przycisk "Nową regułę alertu" u góry, a teraz w dowolnym momencie zdarzenie dotrze na podstawie tego zapytania, zostanie wyświetlony alert w wybranego metody komunikacji.

    ![Nowy Alert OMS](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Jak można I otrzymywać alerty o wycofań uaktualniania aplikacji?

1. Na tym samym oknie wyszukiwania dziennika jako przed wprowadź następujące zapytanie wycofań uaktualnienia. Te identyfikatory zdarzeń znajdują się w obszarze [dotyczące zdarzeń aplikacji](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventId == 29623 or EventId == 29624
    ```

2. Kliknij przycisk "Nową regułę alertu" u góry, a teraz w dowolnym momencie zdarzenie dotrze na podstawie tego zapytania, zostanie wyświetlony alert.

## <a name="how-do-i-see-container-metrics"></a>Jak wyświetlić kontenera metryk?

W jednym widoku wszystkie wykresami zobaczysz Kafelki niektórych wydajności kontenerów. Należy Agent pakietu OMS i [rozwiązanie monitorowanie kontenera](service-fabric-diagnostics-oms-containers.md) dla tych kafelków wypełnić.

![Metryki kontenera OMS](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>Aby telemetrii dokumentu z **wewnątrz** Twojego kontenera, należy dodać [pakietu nuget usługi Application Insights dla kontenerów](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios).

## <a name="how-can-i-monitor-performance-counters"></a>Jak można monitorować liczniki wydajności?

1. Po dodaniu Agent pakietu OMS do klastra należy dodać liczniki wydajności określonych, które mają być śledzone. Przejdź do strony obszar roboczy OMS w portalu — ze strony tego rozwiązania, w której karty Obszar roboczy znajduje się w lewym menu.

    ![Karty Obszar roboczy OMS](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. Po wyświetleniu strony obszaru roboczego, kliknij przycisk "Zaawansowane ustawienia" w tym samym menu po lewej stronie.

    ![Ustawienia zaawansowane OMS](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Polecenie danych > liczników wydajności systemu Windows (danych > liczniki wydajności systemu Linux na komputerach z systemem Linux) można uruchomić zbierania liczników określonych z węzłów za pomocą agenta pakietu OMS. Poniżej przedstawiono przykłady format licznik do dodania

    * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
    * `Processor(_Total)\\% Processor Time`
    * `Service Fabric Service(*)\\Average milliseconds per request`

    Z opcją szybkiego startu, VotingData i VotingWeb są nazwy procesu używane, więc śledzenia tych liczników będzie wyglądać

    * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
    * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

    ![Liczniki wydajności OMS](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. Spowoduje to umożliwiają wyświetlanie jak infrastruktury jest obsługi obciążeń i ustaw odpowiednie alerty na podstawie wykorzystania zasobów. Na przykład — możesz ustawić alert, jeśli łączne użycie procesora przejdzie do ponad 90% lub mniej niż 5%. Nazwa licznika, które będzie używane w tym jest "% czasu procesora". Można to zrobić, tworząc regułę alertu na następujące zapytanie:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Jak śledzić wydajność Moje Reliable Services i złośliwych użytkowników?

Na śledzenie wydajności niezawodnych usług lub złośliwych użytkowników w aplikacji, należy dodać także liczniki usług aktora sieci szkieletowej, metoda aktora, usługi i metody usługi. Można dodać tych liczników w podobny sposób jak w scenariuszu powyżej, poniżej przedstawiono przykłady niezawodnej liczników wydajności usługi i aktora w OMS

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Sprawdź poniższych linków, aby uzyskać pełną listę liczników wydajności na skuteczne [usług](service-fabric-reliable-serviceremoting-diagnostics.md) i [złośliwych użytkowników](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie alertów w AI](../application-insights/app-insights-alerts.md) Aby otrzymywać powiadomienia o zmianach wprowadzonych w wydajności i użycia
* [Inteligentne wykrywania w usłudze Application Insights](../application-insights/app-insights-proactive-diagnostics.md) wykonuje aktywnego analizy telemetrii są wysyłane do AI, aby ostrzec użytkownika o potencjalnych problemów z wydajnością
* Dowiedz się więcej na temat analizy dzienników OMS [alerty](../log-analytics/log-analytics-alerts.md) ułatwiających wykrywania i informacji diagnostycznych.
* W przypadku klastrów lokalnymi OMS oferuje bramy (do przodu serwer Proxy HTTP), która może służyć do wysyłania danych z usługą OMS. Dowiedz się więcej o tym, że w [łączenia komputerów bez dostępu do Internetu z usługą OMS przy użyciu bramy OMS](../log-analytics/log-analytics-oms-gateway.md)
* Pobierz zapoznaniu się z [wyszukiwania i badania dziennika](../log-analytics/log-analytics-log-searches.md) funkcje dostępne w ramach analizy dzienników
* Uzyskać bardziej szczegółowy przegląd analizy dzienników, jakie oferuje, przeczytaj [co to jest analiza dziennika?](../operations-management-suite/operations-management-suite-overview.md)
