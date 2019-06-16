---
title: Wyświetlanie powiadomień dotyczących kondycji usługi za pomocą witryny Azure Portal
description: Powiadomienia o kondycji usług umożliwiają wyświetlanie komunikatów o kondycji usługi opublikowana przez Microsoft Azure.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2018
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: 8cf8c3eb86f55b5595ef9a1af83ea50580bf638b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069349"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Wyświetlanie powiadomień dotyczących kondycji usługi za pomocą witryny Azure Portal

Powiadomienia o kondycji usług są publikowane przez infrastrukturę platformy Azure. Zawierają one informacje o zasobach w ramach Twojej subskrypcji. Te powiadomienia są podklasą klasy działania z dziennika zdarzeń, a także można znaleźć w dzienniku aktywności. Powiadomienia o kondycji usług może być informacyjne lub informacje z możliwością działania, w zależności od klasy.

Aby uzyskać więcej informacji na temat różnych rodzajów powiadomień dotyczących kondycji usługi, zobacz [właściwości powiadomienia o kondycji usługi](../../service-health/service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Wyświetl powiadomienia usługi kondycji w witrynie Azure portal

1. W [witryny Azure portal](https://portal.azure.com), wybierz opcję **Monitor**.

    ![Zrzut ekranu platformy Azure z menu portalu z monitorem wybrane](./media/service-notifications/home-monitor.png)

    Usługa Azure Monitor gromadzi wszystkie ustawienia monitorowania i danych w jednym skonsolidowanym widoku. Na początku widoczna jest sekcja **Dziennik aktywności**.

1. Wybierz **alerty**.

    ![Zrzut ekranu monitorowanie działań zdarzenia z dziennika wybrane alerty](./media/service-notifications/service-health-summary.png)

1. Wybierz **+ Dodaj alert dziennika aktywności**i ustawić alert, aby upewnić się, zostanie wyświetlone powiadomienie dla przyszłej obsługi powiadomień. Aby uzyskać więcej informacji, zobacz [Tworzenie alertów dziennika aktywności dla powiadomień dotyczących usług](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>Kolejne kroki

* Odbieranie [alertu powiadomień przy każdym powiadomienia kondycji usługi](../../azure-monitor/platform/alerts-activity-log-service-notifications.md) zostanie opublikowany.  
* Dowiedz się więcej o [alertów dziennika aktywności](../../azure-monitor/platform/activity-log-alerts.md).
