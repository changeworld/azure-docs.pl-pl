---
title: Analiza zdarzeń sieci szkieletowej usługi Azure za pomocą dzienników usługi Azure Monitor
description: Dowiedz się więcej o wizualizacji i analizowaniu zdarzeń przy użyciu dzienników usługi Azure Monitor do monitorowania i diagnostyki klastrów sieci szkieletowej usług Azure.
author: srrengar
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: srrengar
ms.openlocfilehash: 40dd930aa21e3056d5ecc908359215d6874ed8ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464740"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Analiza zdarzeń i wizualizacja za pomocą dzienników usługi Azure Monitor
 Dzienniki usługi Azure Monitor zbierają i analizują dane telemetryczne z aplikacji i usług hostowanych w chmurze i udostępniają narzędzia do analizy, które pomagają zmaksymalizować ich dostępność i wydajność. W tym artykule opisano sposób uruchamiania zapytań w dziennikach usługi Azure Monitor w celu uzyskania szczegółowych informacji i rozwiązywania problemów z tym, co dzieje się w klastrze. Adresowane są następujące typowe pytania:

* Jak rozwiązywać problemy ze zdarzeniami dotyczącymi kondycji?
* Skąd mam wiedzieć, kiedy węzeł ulegnie upadkowi?
* Skąd mam wiedzieć, czy usługi mojej aplikacji zostały uruchomione lub zatrzymane?

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>Omówienie obszaru roboczego usługi Log Analytics

>[!NOTE] 
>Podczas gdy magazyn diagnostyczny jest domyślnie włączony w czasie tworzenia klastra, nadal należy skonfigurować obszar roboczy usługi Log Analytics do odczytu z magazynu diagnostycznego.

Dzienniki usługi Azure Monitor zbierają dane z zasobów zarządzanych, w tym tabeli magazynu platformy Azure lub agenta, i przechowuje je w centralnym repozytorium. Dane mogą być następnie używane do analizy, alertów i wizualizacji lub dalszego eksportowania. Dzienniki usługi Azure Monitor obsługują zdarzenia, dane dotyczące wydajności lub inne dane niestandardowe. Zapoznaj się z [krokami, aby skonfigurować rozszerzenie diagnostyki do agregacji zdarzeń](service-fabric-diagnostics-event-aggregation-wad.md) i [kroków, aby utworzyć obszar roboczy usługi Log Analytics do odczytu ze zdarzeń w magazynie,](service-fabric-diagnostics-oms-setup.md) aby upewnić się, że dane są przesyłane do dzienników usługi Azure Monitor.

Po odebraniu danych przez dzienniki usługi Azure Monitor platforma Azure ma kilka *rozwiązań monitorowania,* które są wstępnie spakowanymi rozwiązaniami lub operacyjnymi pulpitami nawigacyjnymi do monitorowania przychodzących danych, dostosowanych do kilku scenariuszy. Należą do nich rozwiązanie *analizy sieci szkieletowej usług* i rozwiązanie *kontenerów,* które są dwa najbardziej odpowiednie do diagnostyki i monitorowania podczas korzystania z klastrów sieci szkieletowej usług. W tym artykule opisano sposób korzystania z rozwiązania analizy sieci szkieletowej usług, które jest tworzone za pomocą obszaru roboczego.

## <a name="access-the-service-fabric-analytics-solution"></a>Dostęp do rozwiązania Do analizy sieci szkieletowej usług

W [witrynie Azure Portal](https://portal.azure.com)przejdź do grupy zasobów, w której utworzono rozwiązanie analizy sieci szkieletowej usług.

Wybierz zasób **ServiceFabric\<\>nameOfOMSWorkspace**.

W `Summary`programze zobaczysz kafelki w postaci wykresu dla każdego z włączonych rozwiązań, w tym jednego dla sieci szkieletowej usług. Kliknij wykres **sieci szkieletowej usług,** aby przejść do rozwiązania analizy sieci szkieletowej usług.

![Rozwiązanie sieci szkieletowej usług](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

Na poniższej ilustracji przedstawiono stronę główną rozwiązania usługi Analizy sieci szkieletowej. Ta strona główna zawiera widok migawki tego, co dzieje się w klastrze.

![Rozwiązanie sieci szkieletowej usług](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 Jeśli diagnostyka została włączona podczas tworzenia klastra, można zobaczyć zdarzenia 

* [Zdarzenia klastra sieci szkieletowej usług](service-fabric-diagnostics-event-generation-operational.md)
* [Reliable Actors modelowanie zdarzeń modelu](service-fabric-reliable-actors-diagnostics.md)
* [Zdarzenia modelu programowania niezawodnych usług](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Oprócz zdarzeń sieci szkieletowej usług po wyjęciu z pudełka, bardziej szczegółowe zdarzenia systemowe mogą być zbierane przez [aktualizację konfiguracji rozszerzenia diagnostyki](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>Wyświetlanie zdarzeń sieci szkieletowej usług, w tym akcji w węzłach

Na stronie Analiza sieci szkieletowej usług kliknij wykres zdarzeń **sieci szkieletowej usług**.

![Kanał operacyjny rozwiązania sieci szkieletowej usług](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

Kliknij **pozycję Lista,** aby wyświetlić zdarzenia na liście. Po tym miejscu zobaczysz wszystkie zdarzenia systemowe, które zostały zebrane. Dla odwołania są one z **WADServiceFabricSystemEventsTable** na koncie usługi Azure Storage i podobnie niezawodne usługi i zdarzenia podmiotów, które widzisz dalej są z tych odpowiednich tabel.
    
![Zapytanie kanał operacyjny](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Alternatywnie możesz kliknąć lupę po lewej stronie i użyć języka zapytania Kusto, aby znaleźć to, czego szukasz. Na przykład, aby znaleźć wszystkie akcje podjęte w węzłach w klastrze, można użyć następującej kwerendy. Identyfikatory zdarzeń użyte poniżej znajdują się w [odwołaniu do zdarzeń kanału operacyjnego](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Można wyszukiwać w wielu innych polach, takich jak określone węzły (Komputer) usługa systemowa (TaskName).

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>Wyświetlanie niezawodnej usługi i zdarzeń usługi Fabric

Na stronie Analiza sieci szkieletowej usług kliknij wykres dla **niezawodnych usług**.

![Niezawodne usługi w zakresie sieci szkieletowej usług](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

Kliknij **pozycję Lista,** aby wyświetlić zdarzenia na liście. Tutaj można zobaczyć zdarzenia z niezawodnych usług. Można zobaczyć różne zdarzenia, gdy runasync usługi jest uruchamiany i ukończony, co zwykle dzieje się na wdrożeniach i uaktualnień. 

![Zapytanie o niezawodne usługi](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Niezawodne wydarzenia aktorskie można oglądać w podobny sposób. Aby skonfigurować bardziej szczegółowe zdarzenia dla wiarygodnych `scheduledTransferKeywordFilter` aktorów, należy zmienić w konfiguracji dla rozszerzenia diagnostycznego (pokazano poniżej). Szczegóły dotyczące wartości dla nich znajdują się w [odwołaniu do zdarzeń wiarygodnych aktorów](service-fabric-reliable-actors-diagnostics.md#keywords).

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

Język zapytania Kusto jest potężny. Innym cennym zapytaniem, które można uruchomić, jest dowiedzieć się, które węzły generują najwięcej zdarzeń. Kwerenda na poniższym zrzucie ekranu przedstawia zdarzenia operacyjne sieci szkieletowej usług zagregowane z określoną usługą i węzłem.

![Zdarzenia kwerendy na węzeł](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Następne kroki

* Aby włączyć monitorowanie infrastruktury, czyli liczniki wydajności, przejdź do [dodawania agenta usługi Log Analytics](service-fabric-diagnostics-oms-agent.md). Agent zbiera liczniki wydajności i dodaje je do istniejącego obszaru roboczego.
* W przypadku klastrów lokalnych dzienniki usługi Azure Monitor oferują bramę (http forward proxy), która może służyć do wysyłania danych do dzienników usługi Azure Monitor. Dowiedz się więcej o tym w [obszarze Łączenie komputerów bez dostępu do Internetu z dziennikami usługi Azure Monitor przy użyciu bramy usługi Log Analytics](../azure-monitor/platform/gateway.md).
* Skonfiguruj [automatyczne alerty,](../log-analytics/log-analytics-alerts.md) aby ułatwić wykrywanie i diagnostykę.
* Zapoznaj się z funkcjami [wyszukiwania i wyszukiwania w dziennikach](../log-analytics/log-analytics-log-searches.md) oferowanych w ramach dzienników usługi Azure Monitor.
* Uzyskaj bardziej szczegółowe omówienie dzienników usługi Azure Monitor i jej oferty, przeczytaj Co to [są dzienniki usługi Azure Monitor?](../operations-management-suite/operations-management-suite-overview.md).
