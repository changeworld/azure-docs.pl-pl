---
title: Analiza zdarzeń sieci szkieletowej usługi platformy Azure z usługą Log Analytics | Dokumentacja firmy Microsoft
description: Więcej informacji na temat wizualizowanie i analizowanie zdarzeń za pomocą usługi Log Analytics do monitorowania i diagnostyki klastrów usługi Azure Service Fabric.
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
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 332939710517e99aaa77642dc5e67256b476bd66
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634579"
---
# <a name="event-analysis-and-visualization-with-log-analytics"></a>Analiza zdarzeń i wizualizacji przy użyciu usługi Log Analytics
 Log Analytics zbiera i analizuje dane telemetryczne z aplikacji i usług w chmurze i udostępnia narzędzia do analizy ułatwiają maksymalne wykorzystanie ich dostępności i wydajności. W tym artykule opisano sposób uruchamiania zapytania w usłudze Log Analytics do uzyskiwania szczegółowych informacji i rozwiązywanie problemów z tym, co dzieje się w klastrze. Poniższe często zadawane pytania dotyczą:

* Jak rozwiązywać zdarzenia dotyczące kondycji?
* Jak sprawdzić, kiedy węzeł ulegnie awarii?
* Jak sprawdzić, jeśli Moja aplikacja usługi mają została zatrzymana lub uruchomiona?

## <a name="log-analytics-workspace"></a>Obszar roboczy usługi Log Analytics

>[!NOTE] 
>Gdy magazynu diagnostyki jest domyślnie włączona w momencie tworzenia klastra, należy nadal ustawić obszaru roboczego usługi Log Analytics do odczytu z magazynu diagnostyki.

Log Analytics zbiera dane z zarządzanych zasobów, w tym tabeli usługi Azure storage lub agenta i przechowuje je w centralnym repozytorium. Dane można następnie używane do analizy, alertów i wizualizację lub dodatkowo eksportowania. Usługa log Analytics obsługuje zdarzenia, dane dotyczące wydajności lub innych danych niestandardowych. Zapoznaj się z [kroki, aby skonfigurować rozszerzenie diagnostyki w celu agregowania zdarzeń](service-fabric-diagnostics-event-aggregation-wad.md) i [kroki, aby utworzyć obszar roboczy usługi Log Analytics do odczytu zdarzeń w magazynie](service-fabric-diagnostics-oms-setup.md) się upewnić, że dane będą przepływać do usługi Log Analytics .

Po odebraniu danych przez usługę Log Analytics, platforma Azure oferuje kilka *rozwiązań do zarządzania* , które są to wstępnie spakowane zestawy rozwiązań lub operacyjne pulpity nawigacyjne mają być monitorowane dane przychodzące, dostosowane do kilku scenariuszy. Obejmują one *analiza usługi Service Fabric* rozwiązania i *kontenery* rozwiązania, które są dwie najbardziej odpowiednie do diagnostyki i monitorowania w przypadku korzystania z klastrów usługi Service Fabric. W tym artykule opisano, jak używać rozwiązania analiza usługi Service Fabric, który jest tworzony z obszarem roboczym.

## <a name="access-the-service-fabric-analytics-solution"></a>Dostęp do rozwiązania analiza usługi Service Fabric

1. W witrynie Azure Portal przejdź do grupy zasobów, w którym utworzono rozwiązania analiza usługi Service Fabric.

2. Wybierz zasób **ServiceFabric\<nameOfOMSWorkspace\>**.

2. W `Summary`, zobaczysz Kafelki w formie wykresu dla każdego rozwiązania włączone, w tym dla usługi Service Fabric. Kliknij przycisk **usługi Service Fabric** programu graph (pierwszy obraz poniżej), aby przejść do rozwiązania analiza usługi Service Fabric (drugi obraz poniżej).

    ![Rozwiązania usługi Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

    ![Rozwiązania usługi Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

Na powyższej ilustracji jest stroną główną rozwiązania analiza usługi Service Fabric. To jest widokiem migawki, co się dzieje w klastrze. Włączenie diagnostyki po utworzeniu klastra można wyświetlić zdarzenia dla 

* [Zdarzenia klastra usługi Service Fabric](service-fabric-diagnostics-event-generation-operational.md)
* [Elementy Reliable Actors zdarzeń modelu programowania](service-fabric-reliable-actors-diagnostics.md)
* [Zdarzeń modelu programowania usług Reliable Services](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Oprócz zdarzeń usługi Service Fabric gotowych, bardziej szczegółowe zdarzenia systemowe mogą być zbierane przez [aktualizowanie konfiguracji rozszerzenia diagnostyki](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Wyświetl zdarzenia sieci szkieletowej usług łącznie z działaniami w węzłach

1. Na stronie analiza usługi Service Fabric, kliknij wykres dla **usługi Service Fabric zdarzenia**.

    ![Kanał operacyjny rozwiązania usługi Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

2. Kliknij przycisk **listy** Aby wyświetlić zdarzenia na liście. Jeden raz w tym miejscu zobaczysz wszystkie zdarzenia systemowe, które zostały zebrane. Odwołanie są to aplikacje WADServiceFabricSystemEventsTable w ramach konta usługi Azure Storage i podobnie reliable services i zdarzeń uczestników, które zostanie wyświetlony obok pochodzą od tych tabel odpowiednich.
    
    ![Kanał operacyjny zapytania](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Można kliknąć ikonę lupy po lewej stronie i używaj języka zapytania Kusto można znaleźć, czego szukasz. Na przykład aby znaleźć wszystkie akcje wykonywane w węzłach w klastrze, można użyć następującego zapytania. Identyfikatory zdarzeń, poniżej znajdują się w [informacje o zdarzeniach kanał operacyjny](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Można tworzyć zapytania o wiele więcej pól, takich jak określonych węzłów (komputer) usługa systemowa (nazwa zadania).

### <a name="view-service-fabric-reliable-service-and-actor-events"></a>Zdarzenia usługi Reliable Service widok usługi Service Fabric i aktora

1. Na stronie analiza usługi Service Fabric, kliknij wykres na **usług Reliable Services**.

    ![Usług Reliable Services usługi Service Fabric rozwiązania](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

2. Kliknij przycisk **listy** Aby wyświetlić zdarzenia na liście. W tym miejscu można wyświetlić zdarzenia z usług reliable services. Możesz zobaczyć różne zdarzenia dla, gdy runasync usługi zostanie rozpoczęte i zakończone, która zwykle ma miejsce na wdrażania i uaktualniania. 

    ![Zapytanie interfejsu Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Zdarzenia Reliable actors można wyświetlać w taki sposób, w podobny sposób. Aby skonfigurować więcej szczegółowych informacji o zdarzeniach do elementów reliable actors, musisz zmienić `scheduledTransferKeywordFilter` w konfiguracji rozszerzenia diagnostyki (pokazana poniżej). Szczegółowe informacje na podstawie wartości dla zostały one [informacje o zdarzeniach elementów reliable actors](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

Język zapytania Kusto jest skuteczna. Inne przydatne zapytanie, które można uruchomić polega na węzły, które generują większość zdarzeń. Zapytania w poniższy zrzut ekranu przedstawia zdarzenia operacyjne usługi Service Fabric, łącznie z określonej usługi i węzła.

![Zdarzenia powiązane z zapytaniem na węzeł](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Kolejne kroki

* Aby włączyć liczniki wydajności do monitorowania tj infrastruktury, przejdź do [dodanie agenta usługi Log Analytics](service-fabric-diagnostics-oms-agent.md). Agent zbiera dane liczników wydajności i dodaje je do swojego istniejącego obszaru roboczego.
* W przypadku klastrów w środowisku lokalnym usługa Log Analytics oferuje bramy (do przodu serwer Proxy HTTP), który może służyć do wysyłania danych do usługi Log Analytics. Dowiedz się więcej o tym, że w [łączenie komputerów bez dostępu do Internetu z usługi Log Analytics przy użyciu bramy usługi Log Analytics](../azure-monitor/platform/gateway.md).
* Konfigurowanie [automatyczne alerty](../log-analytics/log-analytics-alerts.md) ułatwiające wykrywanie i przeprowadzanie diagnostyki.
* Zapoznaj się z funkcjami [przeszukiwania dzienników i wykonywania zapytań względem nich](../log-analytics/log-analytics-log-searches.md) dostępnymi w ramach usługi Log Analytics.
* Uzyskuj bardziej szczegółowym omówieniem usługi Log Analytics i co oferuje, przeczytaj [co to jest usługa Log Analytics?](../operations-management-suite/operations-management-suite-overview.md).
