---
title: Wyświetlanie powiadomień dotyczących kondycji usługi za pomocą witryny Azure Portal
description: Powiadomienia o kondycji usług umożliwiają wyświetlanie komunikatów o kondycji usługi opublikowana przez Microsoft Azure.
author: stephbaron
ms.author: stbaron
services: monitoring
ms.service: service-health
ms.topic: conceptual
ms.date: 6/27/2019
ms.subservice: ''
ms.openlocfilehash: d2e18ae28e79590cb04ee0045341ea817be4a3bc
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538356"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Wyświetlanie powiadomień dotyczących kondycji usługi za pomocą witryny Azure Portal

Powiadomień dotyczących kondycji usługi są publikowane w ramach infrastruktury platformy Azure do [dziennika aktywności platformy Azure](../azure-monitor/platform/activity-logs-overview.md).  Powiadomienia zawierają informacje dotyczące zasobów w ramach Twojej subskrypcji. Biorąc pod uwagę potencjalnie dużą liczbą informacje przechowywane w dzienniku aktywności, istnieje interfejs oddzielnego użytkownika, aby ułatwić wyświetlanie i Konfigurowanie alertów dla powiadomień dotyczących kondycji usługi. 

Powiadomienia o kondycji usług może być informacyjne lub informacje z możliwością działania, w zależności od klasy.

Aby uzyskać więcej informacji na temat różnych rodzajów powiadomień dotyczących kondycji usługi, zobacz [właściwości powiadomienia o kondycji usługi](service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Wyświetl powiadomienia usługi kondycji w witrynie Azure portal

1. W [witryny Azure portal](https://portal.azure.com), wybierz opcję **Monitor**.

    ![Zrzut ekranu platformy Azure z menu portalu z monitorem wybrane](./media/service-notifications/home-monitor.png)

    Usługa Azure Monitor gromadzi wszystkie ustawienia monitorowania i danych w jednym skonsolidowanym widoku. Na początku widoczna jest sekcja **Dziennik aktywności**.

1. Wybierz **alerty**.

    ![Zrzut ekranu monitorowanie działań zdarzenia z dziennika wybrane alerty](./media/service-notifications/service-health-summary.png)

1. Wybierz **+ Dodaj alert dziennika aktywności**i ustawić alert, aby upewnić się, zostanie wyświetlone powiadomienie dla przyszłej obsługi powiadomień. Aby uzyskać więcej informacji, zobacz [Tworzenie alertów dziennika aktywności dla powiadomień dotyczących usług](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [alertów dziennika aktywności](../azure-monitor/platform/activity-log-alerts.md).
