---
title: Skonfiguruj alerty dotyczące kondycji usługi platformy Azure z PagerDuty | Dokumentacja firmy Microsoft
description: Pobierz spersonalizowany powiadomień dotyczących zdarzeń usługi kondycji do Twojego wystąpienia PagerDuty.
author: shawntabrizi
manager: scotthit
editor: ''
services: service-health
documentationcenter: service-health
ms.assetid: ''
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: 6e9fcf20d368e270f9af4551c539acd873335498
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30178916"
---
# <a name="configure-service-health-alerts-with-pagerduty"></a>Skonfiguruj alerty dotyczące kondycji usługi z PagerDuty

W tym artykule przedstawiono sposób konfigurowania powiadomień o kondycji usługi platformy Azure za pośrednictwem PagerDuty przy użyciu elementu webhook. Za pomocą [PagerDuty](https://www.pagerduty.com/)przez typ niestandardowy integracji Microsoft Azure, alerty dotyczące kondycji usługi wysiłku można dodać do nowej lub istniejącej usługi PagerDuty.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Tworzenie adresu URL usługi kondycji integracji w PagerDuty
1.  Upewnij się, że zalogowali się i zalogowano na Twojej [PagerDuty](https://www.pagerduty.com/) konta.

2.  Przejdź do **usług** części PagerDuty.

    ![W sekcji "Usługi" w PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

3.  Wybierz **Dodaj nową usługę** lub Otwórz istniejącą usługę zostały skonfigurowane.

4.  W **ustawienia integracji**, wykonaj następujące czynności:

    a. **Typ integracji**: Microsoft Azure

    b. **Nazwa integracji**: \<nazwa\>

    !["Ustawienia integracji" w PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

5.  Wypełnij wymagane pola i wybierz **Dodaj**.

6.  Otwórz ten nowy integracji i skopiuj i Zapisz **integracji adres URL**.

    !["Integracji adres URL" PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Utwórz alert przy użyciu PagerDuty w portalu Azure
### <a name="for-a-new-action-group"></a>Dla nowej grupy akcji:
1. Wykonaj kroki od 1 do 8 w [tworzenia alertu na powiadomienia kondycji usługi dla nowej grupy akcji za pomocą portalu Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

2. Zdefiniuj na liście **akcje**:

    a. **Typ akcji:** *elementu Webhook*

    b. **Szczegóły:** PagerDuty **integracji adres URL** należy wcześniej zapisać.

    c. **Nazwa:** dla elementu Webhook nazwy, aliasu lub identyfikator.

3. Wybierz **zapisać** po zakończeniu można utworzyć alertu.

### <a name="for-an-existing-action-group"></a>Dla istniejącej grupy akcji:
1. W [portalu Azure](https://portal.azure.com/), wybierz pozycję **Monitor**.

2. W **ustawienia** zaznacz **grupy akcji**.

3. Znajdź i wybierz grupę akcji, którą chcesz edytować.

4. Dodaj do listy **akcje**:

    a. **Typ akcji:** *elementu Webhook*

    b. **Szczegóły:** PagerDuty **integracji adres URL** należy wcześniej zapisać.

    c. **Nazwa:** dla elementu Webhook nazwy, aliasu lub identyfikator.

5. Wybierz **zapisać** na koniec można zaktualizować grupy działań.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testowanie za pomocą żądania HTTP POST integracją elementu webhook
1. Utwórz ładunku kondycji usługi, który chcesz wysłać. Można znaleźć przykład usługi kondycji elementu webhook ładunku w [elementów Webhook dla działania Azure rejestrowania alertów](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Utwórz żądanie HTTP POST w następujący sposób:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. Powinien zostać wyświetlony `202 Accepted` komunikat zawierający identyfikatora"zdarzenia"

4. Przejdź do [PagerDuty](https://www.pagerduty.com/) aby upewnić się, że integracją zostało pomyślnie skonfigurowane.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, jak [skonfigurować powiadomienia elementu webhook dla istniejących systemów zarządzania problem](service-health-alert-webhook-guide.md).
- Przegląd [schemat alertu elementu webhook dziennika aktywności](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Dowiedz się więcej o [usługi powiadomień o kondycji](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Dowiedz się więcej o [grupy akcji](../monitoring-and-diagnostics/monitoring-action-groups.md).