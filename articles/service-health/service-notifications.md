---
title: Wyświetlanie powiadomień dotyczących kondycji usługi za pomocą witryny Azure Portal
description: Powiadomienia o kondycji usługi umożliwiają wyświetlanie komunikatów o kondycji usługi opublikowanych przez platformę Microsoft Azure.
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 21416edc95d345eb183030a9b0cccb7529305d6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75748656"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Wyświetlanie powiadomień dotyczących kondycji usługi za pomocą witryny Azure Portal

Powiadomienia o kondycji usługi są publikowane przez infrastrukturę platformy Azure w [dzienniku aktywności platformy Azure](../azure-monitor/platform/platform-logs-overview.md).  Powiadomienia zawierają informacje o zasobach w ramach subskrypcji. Biorąc pod uwagę potencjalnie dużą ilość informacji przechowywanych w dzienniku działań, istnieje oddzielny interfejs użytkownika, aby ułatwić wyświetlanie i konfigurowanie alertów w powiadomieniach o kondycji usługi. 

Powiadomienia o kondycji usługi mogą być informacyjne lub zasłużenia, w zależności od klasy.

Aby uzyskać więcej informacji na temat różnych klas powiadomień o kondycji usługi, zobacz [Właściwości powiadomień o kondycji usługi](service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Wyświetlanie powiadomień o kondycji usługi w witrynie Azure portal

1. W [witrynie Azure portal](https://portal.azure.com)wybierz pozycję **Monitor**.

    ![Zrzut ekranu przedstawiający menu portalu platformy Azure z wybraną wybraną wybraną wybraną wybraną](./media/service-notifications/home-monitor.png)

    Usługa Azure Monitor łączy wszystkie ustawienia monitorowania i dane w jeden widok skonsolidowany. Na początku widoczna jest sekcja **Dziennik aktywności**.

1. Wybierz **pozycję Alerty**.

    ![Zrzut ekranu przedstawiający dziennik aktywności monitora z wybraną wybraną wybraną wybraną wybraną wybran](./media/service-notifications/service-health-summary.png)

1. Wybierz **+Dodaj alert dziennika aktywności**i skonfiguruj alert, aby upewnić się, że użytkownik zostanie powiadomiony o przyszłych powiadomieniach o usługach. Aby uzyskać więcej informacji, zobacz [Tworzenie alertów dziennika aktywności w powiadomieniach o usługach](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [alertach dziennika aktywności](../azure-monitor/platform/activity-log-alerts.md).
