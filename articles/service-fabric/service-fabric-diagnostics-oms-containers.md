---
title: Monitorowanie kontenerów w usłudze Azure Service Fabric z usługą Log Analytics | Dokumentacja firmy Microsoft
description: Używanie programu Log Analytics do monitorowania kontenery działające w klastrach usługi Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: aabdae370c28f8fa633372be4505c00c25254408
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2018
ms.locfileid: "49403254"
---
# <a name="monitor-containers-with-log-analytics"></a>Monitorowanie kontenerów za pomocą usługi Log Analytics
 
W tym artykule opisano kroki wymagane do skonfigurowania rozwiązania do monitorowania kontenerów w usłudze Azure Log Analytics, aby wyświetlić zdarzenia kontenera. Aby skonfigurować klaster, aby zbierać zdarzenia kontenera, zobacz ten [samouczek krok po kroku](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Konfigurowanie rozwiązania do monitorowania kontenerów

> [!NOTE]
> Musisz mieć zestawu usługi Log Analytics dla klastra, a także wdrożyć na węzły agenta usługi Log Analytics. Jeśli nie wykonaj kroki opisane w [Konfigurowanie usługi Log Analytics](service-fabric-diagnostics-oms-setup.md) i [dodać agenta usługi Log Analytics do klastra](service-fabric-diagnostics-oms-agent.md) pierwszy.

1. Po skonfigurowaniu klastra za pomocą usługi Log Analytics i agenta usługi Log Analytics wdrażanie kontenerów. Poczekaj, aż kontenerów można wdrożyć przed przejściem do następnego kroku.

2. W portalu Azure Marketplace Wyszukaj *rozwiązanie do monitorowania kontenerów* i kliknij pozycję **rozwiązanie do monitorowania kontenerów** zasób, który pojawia się w obszarze monitorowanie + Zarządzanie kategorii.

    ![Dodawanie rozwiązania kontenerów](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Utwórz rozwiązanie wewnątrz tego samego obszaru roboczego, który został już utworzony dla klastra. Ta zmiana na automatyczne wyzwolenie agenta, aby zacząć gromadzić dane platformy docker na kontenery. W około 15 minut lub tak powinien zostać wyświetlony światła się za pomocą przychodzących dzienników i statystyk, rozwiązania, jak pokazano na poniższej ilustracji.

    ![Pulpit nawigacyjny analizy dziennika podstawowe](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Agenta umożliwia zbieranie kilka dzienników specyficzne dla kontenera, które mogą być wysyłane zapytanie w usłudze Log Analytics lub umożliwia wizualizowanie wskaźników wydajności. Typy dzienników, które są zbierane są:

* ContainerInventory: zawiera informacje o lokalizację kontenera, nazwę i obrazów
* ContainerImageInventory: informacje o wdrożonych obrazy, w tym identyfikatorów lub rozmiarów
* ContainerLog: dzienniki określonych błędów, dzienniki platformy docker (stdout itp.) i inne wpisy
* ContainerServiceLog: polecenia demona platformy docker, które zostały uruchomione
* Danych o wydajności: w tym kontenerze liczniki wydajności Procesor cpu, pamięć, ruch sieciowy na dysku We/Wy i metryki niestandardowe z komputerów-hostów



## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [rozwiązanie Log Analytics Containers](../log-analytics/log-analytics-containers.md).
* Dowiedz się więcej o aranżacji kontenerów w usłudze Service Fabric - [usługi Service Fabric i kontenery](service-fabric-containers-overview.md)
* Zapoznaj się z funkcjami [przeszukiwania dzienników i wykonywania zapytań](../log-analytics/log-analytics-log-searches.md) dostępnymi w ramach usługi Log Analytics
* Skonfiguruj usługę Log Analytics, aby skonfigurować [automatyczne alerty](../log-analytics/log-analytics-alerts.md) reguły, aby ułatwić wykrywanie i przeprowadzanie diagnostyki