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
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: 7a775b6d23c144c81650bb3608ee6a117475a9ba
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="monitor-containers-with-log-analytics"></a>Kontenery monitora z analizy dzienników
 
W tym artykule opisano kroki wymagane do skonfigurowania kontenera monitorowania dla klastra. Aby uzyskać więcej informacji o tym, zobacz [monitorowania kontenerów w sieci szkieletowej usług](service-fabric-diagnostics-event-analysis-oms.md#monitoring-containers). Aby wyświetlić to samouczek krok po kroku, można również wykonać [kontenerami Monitor systemu Windows w sieci szkieletowej usług](service-fabric-tutorial-monitoring-wincontainers.md).

## <a name="set-up-the-container-monitoring-solution"></a>Skonfiguruj kontener monitorowania rozwiązania

> [!NOTE]
> Musisz skorzystać zestaw analizy dzienników dla klastra, a także zostały wdrożone na węzły Agent pakietu OMS. Jeśli nie wykonaj kroki opisane w [Konfigurowanie analizy dzienników](service-fabric-diagnostics-oms-setup.md) i [dodać do klastra Agent pakietu OMS](service-fabric-diagnostics-oms-agent.md) pierwszy.

1. Po skonfigurowaniu klastra z funkcją analizy dziennika i Agent pakietu OMS wdrażanie kontenerów. Poczekaj, aż kontenerów do wdrożenia przed przejściem do następnego kroku.

2. W portalu Azure Marketplace, wyszukaj *rozwiązanie monitorowanie kontenera* i wybierz polecenie **rozwiązanie monitorowanie kontenera** zasobów, które zostaną wyświetlone w obszarze monitorowanie i zarządzanie kategorii.

    ![Dodawanie rozwiązania kontenerów](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Tworzenie rozwiązania wewnątrz tego samego obszaru roboczego, który został już utworzony dla klastra. Ta zmiana automatyczne wyzwolenie procesu agenta, aby rozpocząć gromadzenie danych docker na kontenerów. W około 15 minut lub to powinna zostać wyświetlona światła się z dziennikami przychodzące i statystyka rozwiązania.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o aranżacji kontenera w sieci szkieletowej usług - [sieci szkieletowej usług i kontenerów](service-fabric-containers-overview.md)
* Pobierz zapoznaniu się z [wyszukiwania i badania dziennika](../log-analytics/log-analytics-log-searches.md) funkcje dostępne w ramach analizy dzienników
* Konfigurowanie analizy dzienników, aby skonfigurować [automatycznego alerty](../log-analytics/log-analytics-alerts.md) reguły, aby pomóc w wykrywaniu i Diagnostyka