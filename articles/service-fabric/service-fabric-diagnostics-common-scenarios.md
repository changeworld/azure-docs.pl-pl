---
title: Usługa Azure Service Fabric zdiagnozować typowe scenariusze | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać typowe scenariusze za pomocą usługi Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 265aea1b8873d812859b39175c732c3e7118cbb5
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669286"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Diagnozowanie typowe scenariusze za pomocą usługi Service Fabric

W tym artykule przedstawiono typowe scenariusze, które użytkownicy napotkali w zakresie monitorowania i diagnostyki za pomocą usługi Service Fabric. Scenariusze przedstawione obejmują wszystkie warstwy 3 usługi service fabric: Aplikacja klastra i infrastruktury. Każde rozwiązanie używa usługi Application Insights i dzienniki usługi Azure Monitor, narzędziami do monitorowania platformy Azure do ukończenia każdego scenariusza. Kroki w poszczególnych rozwiązaniach użytkownikom wprowadzenie na temat korzystania z usługi Application Insights i Azure Monitor rejestruje się w kontekście usługi Service Fabric.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-recommendations"></a>Wymagania wstępne i zalecenia

Rozwiązania, w tym artykule użyje następujących narzędzi. Zaleca się, że masz te set up i skonfigurowane:

* [Usługa Application Insights z usługą Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)
* [Włącz diagnostykę platformy Azure w klastrze](service-fabric-diagnostics-event-aggregation-wad.md)
* [Skonfiguruj obszar roboczy usługi Log Analytics](service-fabric-diagnostics-oms-setup.md)
* [Zaloguj się Analytics agentowi Śledzenie liczników wydajności](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Jak sprawdzić nieobsługiwanych wyjątków w mojej aplikacji?

1. Przejdź do zasobu usługi Application Insights, skonfigurowaną aplikację.
2. Kliknij pozycję *wyszukiwania* w lewym górnym rogu. Następnie kliknij filtr na panelu dalej.

    ![Omówienie sztucznej Inteligencji](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Zobaczysz wiele typów zdarzeń (śladów, żądania, zdarzenia niestandardowe). Wybierz opcję "Wyjątki" jako filtr.

    ![Lista filtrów sztucznej Inteligencji](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Klikając wyjątek na liście, możesz obejrzeć więcej szczegółów, łącznie z kontekstem usługi, korzystając z zestawu SDK usługi Service Fabric Application Insights.

    ![Wyjątek sztucznej Inteligencji](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Jak wyświetlić HTTP wywołania są używane w mojej usługi?

1. W ten sam zasób usługi Application Insights można filtrować według "requests", a nie wyjątków i wyświetlać wszystkie żądania
2. Jeśli używasz zestawu SDK usługi Service Fabric Application Insights zobaczyć graficzną reprezentację usługi połączone ze sobą, a liczba zakończyło się pomyślnie i żądań zakończonych niepowodzeniem. Po lewej stronie kliknij pozycję "Mapy aplikacji"

    ![Mapa aplikacji sztucznej Inteligencji bloku](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![mapy aplikacji sztucznej Inteligencji](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Aby uzyskać więcej informacji na mapie aplikacji, odwiedź stronę [dokumentacji Mapa aplikacji](../azure-monitor/app/app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Jak utworzyć alert, jeśli węzeł ulegnie awarii

1. Węzeł zdarzenia są śledzone przez Twój klaster usługi Service Fabric. Przejdź do zasobu rozwiązania analiza usługi Service Fabric o nazwie **ServiceFabric(NameofResourceGroup)**
2. Kliknij na grafie w dolnej części bloku pod tytułem "Summary"

    ![Usługa Azure Monitor dzienniki rozwiązania](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. W tym miejscu masz wiele wykresów i Kafelki wyświetlanie różnych metryk. Kliknij jeden z wykresów i spowoduje przejście do przeszukiwania dzienników. W tym miejscu można wyszukiwać wszystkie liczniki wydajności lub zdarzenia klastra.
4. Wprowadź następujące zapytanie. Te identyfikatory zdarzeń znajdują się w [informacje o zdarzeniach węzła](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID >= 25622 and EventID <= 25626
    ```

5. Kliknij przycisk "Nową regułę alertu" u góry, a teraz w dowolnym momencie zdarzenia na podstawie dociera przy użyciu tego zapytania, otrzymasz alert w wybranego metody komunikacji.

    ![Usługa Azure Monitor rejestruje nowy Alert](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Jak można I otrzymywać alerty o cofnięcia uaktualnienia aplikacji?

1. W tym samym oknie Wyszukiwanie w dzienniku jako przed wprowadź następujące zapytanie dla uaktualnienie funkcji wycofywania. Te identyfikatory zdarzeń znajdują się w obszarze [informacje o zdarzeniach aplikacji](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID == 29623 or EventID == 29624
    ```

2. Kliknij przycisk "Nową regułę alertu" u góry, a teraz w dowolnym momencie zdarzenia na podstawie dociera przy użyciu tego zapytania, zostanie wyświetlony alert.

## <a name="how-do-i-see-container-metrics"></a>Jak sprawdzić metryki kontenera?

W jednym widoku przy użyciu wszystkich wykresów zobaczysz niektóre Kafelki wydajności kontenerów. Potrzebujesz agenta usługi Log Analytics i [rozwiązanie do monitorowania kontenerów](service-fabric-diagnostics-oms-containers.md) dla tych kafelków, aby wypełnić.

![Metryki kontenera usługi log Analytics](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>Do Instrumentacji przetwarzanie danych telemetrycznych z **wewnątrz** kontenera należy dodać [pakietu nuget usługi Application Insights dla kontenerów](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios).

## <a name="how-can-i-monitor-performance-counters"></a>Jak monitorować liczniki wydajności

1. Po dodaniu agenta usługi Log Analytics do klastra, należy dodać liczniki wydajności konkretnego, który chcesz śledzić. Przejdź do strony obszaru roboczego usługi Log Analytics w portalu — ze strony tego rozwiązania, które na karcie obszar roboczy znajduje się w menu po lewej stronie.

    ![Log Analytics Workspace Tab](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. Gdy jesteś na stronie obszaru roboczego, kliknij pozycję "Pokaż ustawienia zaawansowane" w tym samym menu po lewej stronie.

    ![Log Analytics Advanced Settings](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Kliknij pozycję Data > Windows liczników wydajności (danych > liczniki wydajności systemu Linux dla maszyn z systemem Linux) do rozpoczęcia zbierania określone liczniki z węzłów za pomocą agenta usługi Log Analytics. Poniżej przedstawiono przykłady format licznik do dodania

   * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
   * `Processor(_Total)\\% Processor Time`

     W przewodniku Szybki Start VotingData i VotingWeb są nazwy procesu używany, więc wyglądałyby śledzenia tych liczników

   * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
   * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

     ![Liczniki wydajności usługi log Analytics](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. Spowoduje to pozwalają zobaczyć, jak infrastruktury jest obsługa obciążeń i ustaw odpowiednie alerty na podstawie wykorzystania zasobów. Na przykład — można ustawić alert, jeśli łączne użycie procesora przekracza 90% lub mniej niż 5%. Nazwa licznika, który zostanie wykorzystany dla tego jest "% czasu procesora". Można to zrobić, tworząc regułę alertu dla następującej kwerendy:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Jak śledzić wydajność usług Reliable Services i aktorów?

Aby śledzić wydajność usług Reliable Services lub podmiotów w swoich aplikacjach, należy zbierać również liczniki Aktor usługi Service Fabric, metoda aktora, usługi i metody usługi. Poniżej przedstawiono przykłady niezawodnej usługi i aktora liczników wydajności zbierających dane

>[!NOTE]
>Liczniki wydajności usługi Service Fabric nie może obecnie zbieranych przez agenta usługi Log Analytics, ale mogą zostać zebrane przez [innych rozwiązań diagnostyczne](service-fabric-diagnostics-partners.md)

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Sprawdź poniższe linki umożliwiają uzyskanie pełną listę liczników wydajności na niezawodna [usług](service-fabric-reliable-serviceremoting-diagnostics.md) i [aktorów](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie alertów związanych ze sztuczną Inteligencją](../azure-monitor/app/alerts.md) Aby otrzymywać powiadomienia o zmianach wprowadzonych w wydajności lub użycia
* [Inteligentne wykrywanie w usłudze Application Insights](../azure-monitor/app/proactive-diagnostics.md) wykonuje aktywnego analizy telemetrii są wysyłane do sztucznej Inteligencji w celu otrzymania potencjalnych problemów z wydajnością
* Dowiedz się więcej na temat dzienników usługi Azure Monitor [alerty](../log-analytics/log-analytics-alerts.md) ułatwiające wykrywanie i przeprowadzanie diagnostyki.
* W przypadku klastrów w środowisku lokalnym dzienniki usługi Azure Monitor oferuje bramy (do przodu serwer Proxy HTTP), który może służyć do wysyłania danych do usługi Azure Monitor dzienniki. Dowiedz się więcej o tym, że w [łączenie komputerów bez dostępu do Internetu z dzienników usługi Azure Monitor, przy użyciu bramy usługi Log Analytics](../azure-monitor/platform/gateway.md)
* Zapoznaj się z funkcjami [przeszukiwania dzienników i wykonywania zapytań](../log-analytics/log-analytics-log-searches.md) dostępnymi w ramach dzienniki usługi Azure Monitor
* Uzyskuj bardziej szczegółowym omówieniem dzienniki usługi Azure Monitor i co oferuje, przeczytaj [co to jest dzienniki usługi Azure Monitor?](../operations-management-suite/operations-management-suite-overview.md)
