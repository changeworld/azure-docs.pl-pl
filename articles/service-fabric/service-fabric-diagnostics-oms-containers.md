---
title: Monitorowanie kontenerów w usłudze Azure Service Fabric przy użyciu dzienników usługi Azure Monitor | Dokumentacja firmy Microsoft
description: Używać dzienników usługi Azure Monitor na potrzeby monitorowania kontenery działające w klastrach usługi Azure Service Fabric.
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
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: d03d68560502821b9c343be983d9f7b5a83ed977
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60321895"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Monitorowanie kontenerów przy użyciu dzienników usługi Azure Monitor
 
W tym artykule opisano kroki wymagane do skonfigurowania rozwiązania do monitorowania kontenerów dzienniki usługi Azure Monitor, aby wyświetlić zdarzenia kontenera. Aby skonfigurować klaster, aby zbierać zdarzenia kontenera, zobacz ten [samouczek krok po kroku](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Konfigurowanie rozwiązania do monitorowania kontenerów

> [!NOTE]
> Musisz mieć usługi Azure Monitor dzienniki skonfigurować dla klastra, a także wdrożyć na węzły agenta usługi Log Analytics. Jeśli nie wykonaj kroki opisane w [Konfigurowanie dzienników usługi Azure Monitor](service-fabric-diagnostics-oms-setup.md) i [dodać agenta usługi Log Analytics do klastra](service-fabric-diagnostics-oms-agent.md) pierwszy.

1. Po skonfigurowaniu klastra przy użyciu dzienników usługi Azure Monitor i agenta usługi Log Analytics wdrażanie kontenerów. Poczekaj, aż kontenerów można wdrożyć przed przejściem do następnego kroku.

2. W portalu Azure Marketplace Wyszukaj *rozwiązanie do monitorowania kontenerów* i kliknij pozycję **rozwiązanie do monitorowania kontenerów** zasób, który pojawia się w obszarze monitorowanie + Zarządzanie kategorii.

    ![Dodawanie rozwiązania kontenerów](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Utwórz rozwiązanie wewnątrz tego samego obszaru roboczego, który został już utworzony dla klastra. Ta zmiana na automatyczne wyzwolenie agenta, aby zacząć gromadzić dane platformy docker na kontenery. W około 15 minut lub tak powinien zostać wyświetlony światła się za pomocą przychodzących dzienników i statystyk, rozwiązania, jak pokazano na poniższej ilustracji.

    ![Pulpit nawigacyjny analizy dziennika podstawowe](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Agenta umożliwia zbieranie kilka dzienników specyficzne dla kontenera, które mogą być wysyłane zapytanie w dziennikach w usłudze Azure Monitor lub umożliwia wizualizowanie wskaźników wydajności. Typy dzienników, które są zbierane są:

* ContainerInventory: zawiera informacje o lokalizację kontenera, nazwę i obrazów
* ContainerImageInventory: informacje o wdrożonych obrazy, w tym identyfikatorów lub rozmiarów
* ContainerLog: dzienniki określonych błędów, dzienniki platformy docker (stdout itp.) i inne wpisy
* ContainerServiceLog: polecenia demona platformy docker, które zostały uruchomione
* Danych o wydajności: w tym kontenerze liczniki wydajności Procesor cpu, pamięć, ruch sieciowy na dysku We/Wy i metryki niestandardowe z komputerów-hostów



## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [usługi Azure Monitor dzienniki rozwiązania Containers](../azure-monitor/insights/containers.md).
* Dowiedz się więcej o aranżacji kontenerów w usłudze Service Fabric - [usługi Service Fabric i kontenery](service-fabric-containers-overview.md)
* Zapoznaj się z funkcjami [przeszukiwania dzienników i wykonywania zapytań](../log-analytics/log-analytics-log-searches.md) dostępnymi w ramach dzienniki usługi Azure Monitor
* Skonfiguruj dzienniki usługi Azure Monitor, aby skonfigurować [automatyczne alerty](../log-analytics/log-analytics-alerts.md) reguły, aby ułatwić wykrywanie i przeprowadzanie diagnostyki