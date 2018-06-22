---
title: Analiza zdarzeń sieci szkieletowej usług Azure z analizy dzienników | Dokumentacja firmy Microsoft
description: Więcej informacji na temat wizualizacji i analizowania zdarzeń za pomocą analizy dzienników dla monitorowania i diagnostyki klastrów sieci szkieletowej usług Azure.
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
ms.date: 05/29/2018
ms.author: srrengar
ms.openlocfilehash: 49d9b5306a0fcf51cc0de036c725fca8345cd0ec
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36302186"
---
# <a name="event-analysis-and-visualization-with-log-analytics"></a>Analiza zdarzeń i wizualizacji z analizy dzienników
Analiza dzienników zbiera i analizuje dane telemetryczne z aplikacji i usług hostowanych w chmurze oraz zapewnia narzędzia analizy do zwiększenia ich dostępności i wydajności. W tym artykule opisano sposób uruchamiania kwerend w analizy dzienników, aby uzyskać wgląd i rozwiązywanie problemów z tym, co dzieje się w klastrze. Poniższe często zadawane pytania dotyczą:

* Jak rozwiązywać zdarzenia kondycji?
* Jak sprawdzić, gdy węzeł ulegnie awarii?
* Jak sprawdzić, czy Moja aplikacja usług ma została zatrzymana lub uruchomiona?

## <a name="log-analytics-workspace"></a>Obszar roboczy usługi Log Analytics

Analiza dzienników zbiera dane z zarządzanych zasobów, włącznie z tabeli magazynu systemu Azure lub agenta i przechowuje ją w centralnym repozytorium. Dane można następnie używana do analizy, alerty i wizualizacji lub dodatkowe eksportowanie. Analiza dzienników obsługuje zdarzeń, danych wydajności lub innych danych niestandardowych. Zapoznaj się z [kroki, aby skonfigurować rozszerzenia diagnostyki w celu agregowania zdarzeń](service-fabric-diagnostics-event-aggregation-wad.md) i [kroki, aby utworzyć obszaru roboczego analizy dzienników można odczytać ze zdarzeń w magazynie](service-fabric-diagnostics-oms-setup.md) się upewnić, że dane jest otrzymywanych analizy dzienników .

Po odebraniu danych przez analizy dzienników, platforma Azure ma kilka *rozwiązań do zarządzania* , które są opakowaniach jednostkowych rozwiązań do przychodzących danych, dostosować, tak aby kilka scenariuszy monitorowania. Obejmują one *usługi sieć szkieletowa Analytics* rozwiązania i *kontenery* rozwiązania, które są najbardziej odpowiednie dwóch te Diagnostyka i monitorowanie w przypadku używania klastrów sieci szkieletowej usług. W tym artykule opisano sposób użycia rozwiązania analizy sieci szkieletowej usług, który jest tworzony z obszaru roboczego.

## <a name="access-the-service-fabric-analytics-solution"></a>Dostęp do rozwiązania analizy sieci szkieletowej usług

1. W portalu Azure przejdź do grupy zasobów, w której utworzono rozwiązania analizy sieci szkieletowej usług.

2. Wybierz zasób **ServiceFabric\<nameOfOMSWorkspace\>**.

2. Podsumowując zobaczysz Kafelki w formie wykresu dla każdego z rozwiązań włączone, w tym dla sieci szkieletowej usług. Kliknij przycisk **sieci szkieletowej usług** graph (pierwszy obraz poniżej) w dalszym ciągu rozwiązania analizy sieci szkieletowej usług (drugi obraz poniżej).

    ![Rozwiązanie sieci szkieletowej usług](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

    ![Rozwiązanie sieci szkieletowej usług](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

Powyższy obraz jest stroną główną rozwiązania analizy sieci szkieletowej usług. Jest to widok miniatur, co się dzieje w klastrze. Jeśli włączono diagnostyki po utworzeniu klastra można przejrzeć zdarzenia dla 

* [Kanał operacyjne](service-fabric-diagnostics-event-generation-operational.md): operacje wyższego poziomu, które wykonuje platformy Service Fabric (kolekcja usługi systemowe).
* [Reliable Actors zdarzeń modelu programowania](service-fabric-reliable-actors-diagnostics.md)
* [Niezawodne usługi zdarzeń modelu programowania](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Oprócz operacyjne kanału bardziej szczegółowe zdarzenia systemowe mogą być zbierane przez [aktualizowania konfiguracji rozszerzenia diagnostyki](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Wyświetl zdarzenia sieci szkieletowej usług łącznie z czynności w węzłach

1. Na stronie usługi sieć szkieletowa Analytics kliknij na wykresie dla **zdarzenia sieci szkieletowej usług**.

    ![Usługa sieci szkieletowej rozwiązania operacyjne kanału](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

2. Kliknij przycisk **listy** do wyświetlania na liście zdarzeń. Gdy w tym miejscu zobaczysz wszystkich zdarzeń systemowych, które zostały zebrane. Odwołania są one z WADServiceFabricSystemEventsTable na koncie magazynu Azure, a podobnie reliable services i zdarzeń podmiotów, które widać obok są z tych tabel odpowiednich.
    
    ![Kanał operacyjny kwerendy](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Można kliknąć Lupa po lewej stronie i używać języka kwerend Kusto można znaleźć, czego szukasz. Na przykład aby znaleźć wszystkie akcje wykonywane w węzłach w klastrze, używając następujące zapytanie. Identyfikatory zdarzeń, poniżej znajdują się w [dotyczące zdarzeń operacyjnych kanału](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Umożliwia wysyłanie zapytań o wiele więcej pól, takich jak określonych węzłów (komputer) w usłudze system (TaskName).

### <a name="view-service-fabric-reliable-service-and-actor-events"></a>Usługa widoku sieci szkieletowej usług niezawodnych i aktora zdarzenia

1. Na stronie usługi sieć szkieletowa Analytics kliknij wykresu dla **niezawodne usługi**.

    ![Usługa sieci szkieletowej rozwiązania niezawodne usługi](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

2. Kliknij przycisk **listy** do wyświetlania na liście zdarzeń. Tutaj można zobaczyć zdarzenia z niezawodne usługi. Widoczny różnych zdarzeń dla runasync usługi zostanie rozpoczęte i zakończone, zwykle nastąpi na wdrożenia i uaktualnienia. 

    ![Niezawodne usługi zapytania](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

W podobny sposób można wyświetlać zdarzenia niezawodnego aktora. Aby skonfigurować więcej szczegółowych informacji o zdarzeniach dla elementów reliable actors, musisz zmienić `scheduledTransferKeywordFilter` w pliku config diagnostycznych rozszerzenia (pokazana poniżej). Szczegółowe informacje o wartości te są w [dotyczące zdarzeń niezawodnej podmiotów](service-fabric-reliable-actors-diagnostics.md#keywords).

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

Język kwerendy Kusto jest zaawansowanym. Inne przydatne zapytanie można uruchamiać jest aby dowiedzieć się węzły, które generują najwięcej zdarzeń. Zapytanie na poniższym zrzucie ekranu wyświetla zdarzenia operacyjne sieci szkieletowej usług, łącznie z określonej usługi i węzła.

![Zdarzenia na węzeł](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Kolejne kroki

* Aby włączyć monitorowanie, tj. liczniki wydajności infrastruktury, przejdź do [dodanie agenta analizy dzienników](service-fabric-diagnostics-oms-agent.md). Agent zbiera dane liczników wydajności i dodaje je do swojego istniejącego obszaru roboczego.
* W przypadku klastrów lokalnymi analizy dzienników oferuje bramy (do przodu serwer Proxy HTTP), która może służyć do wysyłania danych do analizy dzienników. Dowiedz się więcej o tym, że w [łączenie komputerów bez dostępu do Internetu z analizy dzienników przy użyciu bramy OMS](../log-analytics/log-analytics-oms-gateway.md)
* Skonfiguruj [automatycznego alerty](../log-analytics/log-analytics-alerts.md) ułatwiających wykrywania i Diagnostyka
* Pobierz zapoznaniu się z [wyszukiwania i badania dziennika](../log-analytics/log-analytics-log-searches.md) funkcje dostępne w ramach analizy dzienników
* Uzyskać bardziej szczegółowy przegląd analizy dzienników, jakie oferuje, przeczytaj [co to jest analiza dziennika?](../operations-management-suite/operations-management-suite-overview.md)
