---
title: Monitorowanie kontenerów na sieć szkieletowa usług Azure z analizy dzienników | Dokumentacja firmy Microsoft
description: Użyj analizy dzienników dla monitorowania kontenery uruchamianych w klastrach sieć szkieletowa usług Azure.
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
ms.openlocfilehash: 79d30a47b017379107b63b0006a35534f68c43b9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210780"
---
# <a name="monitor-containers-with-log-analytics"></a>Kontenery monitora z analizy dzienników
 
W tym artykule opisano kroki wymagane do skonfigurowania kontenera analizy dzienników OMS rozwiązanie monitorujące, aby wyświetlić zdarzenia kontenera. Aby skonfigurować klaster służąca do gromadzenia zdarzeń kontenera, zobacz [samouczek krok po kroku](service-fabric-tutorial-monitoring-wincontainers.md).

## <a name="set-up-the-container-monitoring-solution"></a>Skonfiguruj kontener monitorowania rozwiązania

> [!NOTE]
> Musisz skorzystać zestaw analizy dzienników dla klastra, a także zostały wdrożone na węzły Agent pakietu OMS. Jeśli nie wykonaj kroki opisane w [Konfigurowanie analizy dzienników](service-fabric-diagnostics-oms-setup.md) i [dodać do klastra Agent pakietu OMS](service-fabric-diagnostics-oms-agent.md) pierwszy.

1. Po skonfigurowaniu klastra z funkcją analizy dziennika i Agent pakietu OMS wdrażanie kontenerów. Poczekaj, aż kontenerów do wdrożenia przed przejściem do następnego kroku.

2. W portalu Azure Marketplace, wyszukaj *rozwiązanie monitorowanie kontenera* i wybierz polecenie **rozwiązanie monitorowanie kontenera** zasobów, które zostaną wyświetlone w obszarze monitorowanie i zarządzanie kategorii.

    ![Dodawanie rozwiązania kontenerów](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Tworzenie rozwiązania wewnątrz tego samego obszaru roboczego, który został już utworzony dla klastra. Ta zmiana automatyczne wyzwolenie procesu agenta, aby rozpocząć gromadzenie danych docker na kontenerów. W około 15 minut lub to powinna zostać wyświetlona światła się z dziennikami przychodzące i statystyka rozwiązania. jak pokazano na poniższej ilustracji.

    ![Pulpit nawigacyjny podstawowe OMS](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Agent umożliwia zbieranie kilka dzienników specyficzne dla kontenera, które można wykonać zapytania w OMS, lub używany do wizualizowanego wskaźników. Typy dziennika, które są zbierane są:

* ContainerInventory: zawiera informacje o lokalizacji kontenera, nazwy i obrazów
* ContainerImageInventory: informacje o wdrożonej obrazów, w tym identyfikatory lub rozmiary
* ContainerLog: dzienniki błędu, dzienniki docker (stdout itp.) i innych pozycji
* ContainerServiceLog: docker demon poleceń, które zostały uruchomione
* O wydajności: liczniki wydajności w tym kontenerze procesora cpu, pamięć, ruch sieciowy na dysku We/Wy i metryki niestandardowe z maszyn hosta



## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [rozwiązania kontenery w OMS](../log-analytics/log-analytics-containers.md).
* Dowiedz się więcej o aranżacji kontenera w sieci szkieletowej usług - [sieci szkieletowej usług i kontenerów](service-fabric-containers-overview.md)
* Pobierz zapoznaniu się z [wyszukiwania i badania dziennika](../log-analytics/log-analytics-log-searches.md) funkcje dostępne w ramach analizy dzienników
* Konfigurowanie analizy dzienników, aby skonfigurować [automatycznego alerty](../log-analytics/log-analytics-alerts.md) reguły, aby pomóc w wykrywaniu i Diagnostyka