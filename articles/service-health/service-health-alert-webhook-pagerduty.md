---
title: Wysyłanie alertów kondycji usługi platformy Azure za pomocą PagerDuty
description: Otrzymuj spersonalizowane powiadomienia o zdarzeniach kondycji usługi do wystąpienia PagerDuty.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: bb449a5279f3cea55e6aec2f72edfd11fb26227a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654073"
---
# <a name="send-azure-service-health-alerts-with-pagerduty-using-webhooks"></a>Wysyłanie alertów kondycji usługi platformy Azure za pomocą usługi PagerDuty przy użyciu elementów webhook

W tym artykule pokazano, jak skonfigurować powiadomienia o kondycji usługi platformy Azure za pośrednictwem PagerDuty przy użyciu elementu webhook. Korzystając z niestandardowego typu integracji platformy Microsoft Azure [pagerDuty,](https://www.pagerduty.com/)można bez wysiłku dodawać alerty kondycji usługi do nowych lub istniejących usług PagerDuty.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Tworzenie adresu URL integracji kondycji usługi w PagerDuty
1.  Upewnij się, że zarejestrowałeś się i zalogowałeś się na swoje konto [PagerDuty.](https://www.pagerduty.com/)

1.  Przejdź do sekcji **Usługi** w PagerDuty.

    ![Sekcja "Usługi" w PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

1.  Wybierz **pozycję Dodaj nową usługę** lub otwórz skonfigurowaną istniejącą usługę.

1.  W **ustawieniach integracji**wybierz następujące opcje:

    a. **Typ integracji**: Microsoft Azure

    b. **Nazwa integracji**: \<Nazwa\>

    !["Ustawienia integracji" w PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

1.  Wypełnij wszystkie inne wymagane pola i wybierz pozycję **Dodaj**.

1.  Otwórz tę nową integrację i skopiuj i zapisz **adres URL integracji**.

    !["Adres URL integracji" w PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Tworzenie alertu przy użyciu pagerDuty w witrynie Azure portal
### <a name="for-a-new-action-group"></a>Dla nowej grupy akcji:
1. Wykonaj kroki od 1 do 8 w [Tworzenie alertu w powiadomieniu o kondycji usługi dla nowej grupy akcji przy użyciu portalu Azure.](../azure-monitor/platform/alerts-activity-log-service-notifications.md)

1. Zdefiniuj na liście **akcji:**

    a. **Typ akcji:** *Element webhook*

    b. **Szczegóły:** Wcześniej zapisany adres **URL integracji** PagerDuty.

    d. **Nazwa:** Nazwa, alias lub identyfikator elementu webhook.

1. Wybierz **pozycję Zapisz** po wykonaniu, aby utworzyć alert.

### <a name="for-an-existing-action-group"></a>W przypadku istniejącej grupy akcji:
1. W [witrynie Azure portal](https://portal.azure.com/)wybierz pozycję **Monitor**.

1. W sekcji **Ustawienia** wybierz pozycję **Grupy akcji**.

1. Znajdź i wybierz grupę akcji, którą chcesz edytować.

1. Dodaj do listy **akcji:**

    a. **Typ akcji:** *Element webhook*

    b. **Szczegóły:** Wcześniej zapisany adres **URL integracji** PagerDuty.

    d. **Nazwa:** Nazwa, alias lub identyfikator elementu webhook.

1. Wybierz **pozycję Zapisz** po zakończeniu, aby zaktualizować grupę akcji.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testowanie integracji elementu webhook za pomocą żądania HTTP POST
1. Utwórz ładunek kondycji usługi, który chcesz wysłać. Przykładowy ładunek elementu webhook kondycji usługi można znaleźć w [witrynach Webhooks dla alertów dziennika aktywności platformy Azure.](../azure-monitor/platform/activity-log-alerts-webhook.md)

1. Utwórz żądanie HTTP POST w następujący sposób:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Powinieneś otrzymać `202 Accepted` komunikat zawierający "identyfikator zdarzenia".

1. Przejdź do [PagerDuty,](https://www.pagerduty.com/) aby potwierdzić, że integracja została pomyślnie skonfigurowana.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [skonfigurować powiadomienia dotyczące elementu webhook dla istniejących systemów zarządzania problemami](service-health-alert-webhook-guide.md).
- Przejrzyj [schemat programu webhook alertu dziennika aktywności](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Dowiedz się więcej o [powiadomieniach o kondycji usługi](../azure-monitor/platform/service-notifications.md).
- Dowiedz się więcej o [grupach akcji](../azure-monitor/platform/action-groups.md).