---
title: Wysyłanie alertów dotyczących kondycji usługi platformy Azure przy użyciu usługi PagerDuty przy użyciu elementów webhook
description: Uzyskaj Spersonalizowane powiadomienia dotyczące zdarzenia usługi service health do swojego wystąpienia usługi PagerDuty.
author: stephbaron
ms.author: stbaron
ms.topic: article
ms.service: service-health
ms.date: 06/10/2019
ms.openlocfilehash: ab3bcffb6453b284c3c8bb0d0373c7155fe8ef23
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067152"
---
# <a name="send-azure-service-health-alerts-with-pagerduty-using-webhooks"></a>Wysyłanie alertów dotyczących kondycji usługi platformy Azure przy użyciu usługi PagerDuty przy użyciu elementów webhook

W tym artykule pokazano, jak skonfigurować powiadomienia o kondycji usługi platformy Azure za pośrednictwem usługi PagerDuty przy użyciu elementu webhook. Za pomocą [PagerDuty](https://www.pagerduty.com/)firmy niestandardowego typu integracji Microsoft Azure, alerty dotyczące kondycji usługi bez wysiłku można dodać do nowej lub istniejącej usługi PagerDuty.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Tworzenie adresu URL usługi kondycji integracji w usłudze PagerDuty
1.  Upewnij się, podpisali i zalogowano się do Twojej [PagerDuty](https://www.pagerduty.com/) konta.

1.  Przejdź do **usług** sekcji w usłudze PagerDuty.

    ![W sekcji "Usługi" w usłudze PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

1.  Wybierz **Dodaj nową usługę** lub Otwórz istniejącą usługę zostały skonfigurowane.

1.  W **ustawienia integracji**, wybierz następujące pozycje:

    a. **Typ integracji**: Microsoft Azure

    b. **Nazwa środowiska Integration**: \<Nazwa\>

    !["Ustawienia integracji" w usłudze PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

1.  Wypełnij wymagane pola, a następnie wybierz **Dodaj**.

1.  Otwórz ten nową opcję integracji i skopiuj i Zapisz **adresów URL integracji**.

    !["Integracja z adresu URL" w usłudze PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Utwórz alert przy użyciu usługi PagerDuty w witrynie Azure portal
### <a name="for-a-new-action-group"></a>Dla nowej grupy akcji:
1. Wykonaj kroki od 1 do 8 w [Tworzenie alertu na powiadomienie usługi kondycji dla nowej grupy akcji przy użyciu witryny Azure portal](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

1. Zdefiniuj na liście **akcje**:

    a. **Typ akcji:** *Element Webhook*

    b. **Szczegóły:** Usługi PagerDuty **adresów URL integracji** zostanie zapisany wcześniej.

    c. **Nazwa:** Nazwy, aliasu lub identyfikator dla elementu Webhook.

1. Wybierz **Zapisz** po zakończeniu tworzenia alertu.

### <a name="for-an-existing-action-group"></a>Dla istniejącej grupy akcji:
1. W [witryny Azure portal](https://portal.azure.com/), wybierz opcję **Monitor**.

1. W **ustawienia** zaznacz **grup akcji**.

1. Znajdź i wybierz grupę akcji, którą chcesz edytować.

1. Dodaj do listy **akcje**:

    a. **Typ akcji:** *Element Webhook*

    b. **Szczegóły:** Usługi PagerDuty **adresów URL integracji** zostanie zapisany wcześniej.

    c. **Nazwa:** Nazwy, aliasu lub identyfikator dla elementu Webhook.

1. Wybierz **Zapisz** po zakończeniu można zaktualizować grupy akcji.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testowanie integracji usługi elementu webhook, za pomocą żądania HTTP POST
1. Utwórz ładunek kondycji usługi, które mają zostać wysłane. Można znaleźć, ładunek elementu webhook przykład usługi kondycji w [alerty dzienników elementy Webhook dla aktywności platformy Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Utwórz żądanie HTTP POST w następujący sposób:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Powinien zostać wyświetlony `202 Accepted` z wiadomością zawierającą identyfikatora"zdarzenie"

1. Przejdź do [PagerDuty](https://www.pagerduty.com/) aby upewnić się, że integracji usługi zostało pomyślnie skonfigurowane.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, jak [Konfigurowanie powiadomień elementu webhook dla istniejących systemów zarządzania problem](service-health-alert-webhook-guide.md).
- Przegląd [schemat elementów webhook alertu dziennika aktywności](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Dowiedz się więcej o [usługi powiadomień dotyczących kondycji](../azure-monitor/platform/service-notifications.md).
- Dowiedz się więcej o [grup akcji](../azure-monitor/platform/action-groups.md).