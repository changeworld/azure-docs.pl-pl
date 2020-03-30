---
title: Monitoruj kontenery za pomocą dzienników usługi Azure Monitor
description: Użyj dzienników usługi Azure Monitor do monitorowania kontenerów działających w klastrach usługi Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 8d4231de13da3f8b2960bd4852136f803a97a546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614438"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Monitoruj kontenery za pomocą dzienników usługi Azure Monitor
 
W tym artykule opisano kroki wymagane do skonfigurowania rozwiązania do monitorowania kontenerów dziennika usługi Azure Monitor w celu wyświetlania zdarzeń kontenera. Aby skonfigurować klaster do zbierania zdarzeń kontenera, zobacz ten [samouczek krok po kroku](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Konfigurowanie rozwiązania do monitorowania kontenerów

> [!NOTE]
> Musisz mieć skonfigurowane dzienniki usługi Azure Monitor dla klastra, a także wdrożyć agenta usługi Log Analytics w węzłach. Jeśli nie, wykonaj kroki opisane w [Konfigurowanie dzienników usługi Azure Monitor](service-fabric-diagnostics-oms-setup.md) i [najpierw dodaj agenta usługi Log Analytics do klastra.](service-fabric-diagnostics-oms-agent.md)

1. Po skonfigurowaniu klastra za pomocą dzienników usługi Azure Monitor i agenta usługi Log Analytics należy wdrożyć kontenery. Poczekaj na kontenery do wdrożenia przed przejściem do następnego kroku.

2. W portalu Azure Marketplace wyszukaj *rozwiązanie do monitorowania kontenerów* i kliknij zasób **rozwiązania do monitorowania kontenerów,** który jest wyświetlany w kategorii Monitorowanie + Zarządzanie.

    ![Dodawanie rozwiązania kontenerów](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Utwórz rozwiązanie wewnątrz tego samego obszaru roboczego, który został już utworzony dla klastra. Ta zmiana automatycznie wyzwala agenta, aby rozpocząć zbieranie danych docker w kontenerach. W około 15 minut lub tak, powinieneś zobaczyć rozwiązanie zapalić się z przychodzących dzienników i statystyk, jak pokazano na obrazku poniżej.

    ![Podstawowy pulpit nawigacyjny analizy dzienników](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Agent umożliwia zbieranie kilku dzienników specyficznych dla kontenera, które mogą być wyszukiwane w dziennikach usługi Azure Monitor lub używane do wizualizacji wskaźników wydajności. Typy dziennika, które są zbierane są:

* ContainerInventory: pokazuje informacje o lokalizacji kontenera, nazwie i obrazach
* ContainerImageInventory: informacje o wdrożonych obrazach, w tym identyfikatorach lub rozmiarach
* ContainerLog: określone dzienniki błędów, dzienniki docker (stdout, itp.) i inne wpisy
* ContainerServiceLog: polecenia demona platformy docker, które zostały uruchomione
* Perf: liczniki wydajności, w tym procesor kontenera, pamięć, ruch sieciowy, we/wy dysku i niestandardowe metryki z komputerów hosta



## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [rozwiązaniu Kontenery dzienników usługi Azure Monitor.](../azure-monitor/insights/containers.md)
* Dowiedz się więcej o aranżacji kontenerów w sieci szkieletowej usług — [sieci szkieletowej usług i kontenerach](service-fabric-containers-overview.md)
* Zapoznaj się z funkcjami [wyszukiwania i wyszukiwania w dziennikach](../log-analytics/log-analytics-log-searches.md) oferowanych w ramach dzienników usługi Azure Monitor
* Konfigurowanie dzienników usługi Azure Monitor w celu [skonfigurowania automatycznych](../log-analytics/log-analytics-alerts.md) reguł alertów w celu ułatwienia wykrywania i diagnostyki