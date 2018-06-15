---
title: Skonfiguruj alerty dotyczące kondycji usługi platformy Azure z OpsGenie | Dokumentacja firmy Microsoft
description: Pobierz spersonalizowany powiadomień dotyczących zdarzeń usługi kondycji do Twojego wystąpienia OpsGenie.
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
ms.openlocfilehash: 6b8017f62dd895219f1d2cdac40f0efdf2db6c93
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30179348"
---
# <a name="configure-service-health-alerts-with-opsgenie"></a>Skonfiguruj alerty dotyczące kondycji usługi z OpsGenie

W tym artykule przedstawiono sposób skonfigurować alerty dotyczące kondycji usługi platformy Azure przy użyciu OpsGenie przy użyciu elementu webhook. Za pomocą [OpsGenie](https://www.opsgenie.com/)w integracji kondycji usługi platformy Azure, alerty dotyczące kondycji usługi platformy Azure można przekazywać do OpsGenie. OpsGenie można określić odpowiednich osób do powiadomienia w oparciu o wywołania harmonogramy, za pomocą poczty e-mail, wiadomości tekstowych (SMS), połączeń telefonicznych, iOS i powiadomień wypychanych systemu Android i coraz szybciej alerty do momentu alert potwierdzone lub zamknięty.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Tworzenie adresu URL usługi kondycji integracji w OpsGenie
1.  Upewnij się, że zalogowali się i zalogowano na Twojej [OpsGenie](https://www.opsgenie.com/) konta.

2.  Przejdź do **integracji** części OpsGenie.

    ![W sekcji "Integracji" w OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

3.  Wybierz **kondycja usługi Azure** przycisk integracji.

    !["Przycisk Azure Service Health" w OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

4.  **Nazwa** Twojego alertów i określ **przypisanych do zespołu** pola.

5.  Należy wypełnić pola jak **adresatów**, **włączone**, i **Pomiń powiadomienia**.

6.  Skopiuj i Zapisz **integracji adres URL**, który już powinna zawierać Twojego `apiKey` dołączany na końcu.

    !["Integracji adres URL" OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

7.  Wybierz **zapisać integracji**

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Utwórz alert przy użyciu OpsGenie w portalu Azure
### <a name="for-a-new-action-group"></a>Dla nowej grupy akcji:
1. Wykonaj kroki od 1 do 8 w [tworzenia alertu na powiadomienia kondycji usługi dla nowej grupy akcji za pomocą portalu Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

2. Zdefiniuj na liście **akcje**:

    a. **Typ akcji:** *elementu Webhook*

    b. **Szczegóły:** OpsGenie **integracji adres URL** należy wcześniej zapisać.

    c. **Nazwa:** dla elementu Webhook nazwy, aliasu lub identyfikator.

3. Wybierz **zapisać** po zakończeniu można utworzyć alertu.

### <a name="for-an-existing-action-group"></a>Dla istniejącej grupy akcji:
1. W [portalu Azure](https://portal.azure.com/), wybierz pozycję **Monitor**.

2. W **ustawienia** zaznacz **grupy akcji**.

3. Znajdź i wybierz grupę akcji, którą chcesz edytować.

4. Dodaj do listy **akcje**:

    a. **Typ akcji:** *elementu Webhook*

    b. **Szczegóły:** OpsGenie **integracji adres URL** należy wcześniej zapisać.

    c. **Nazwa:** dla elementu Webhook nazwy, aliasu lub identyfikator.

5. Wybierz **zapisać** na koniec można zaktualizować grupy działań.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testowanie za pomocą żądania HTTP POST integracją elementu webhook
1. Utwórz ładunku kondycji usługi, który chcesz wysłać. Można znaleźć przykład usługi kondycji elementu webhook ładunku w [elementów Webhook dla działania Azure rejestrowania alertów](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Utwórz żądanie HTTP POST w następujący sposób:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. Powinien zostać wyświetlony `200 OK` odpowiedź z komunikatem o stanie "powiodło się."

4. Przejdź do [OpsGenie](https://www.opsgenie.com/) aby upewnić się, że integracją zostało pomyślnie skonfigurowane.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, jak [skonfigurować powiadomienia elementu webhook dla istniejących systemów zarządzania problem](service-health-alert-webhook-guide.md).
- Przegląd [schemat alertu elementu webhook dziennika aktywności](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Dowiedz się więcej o [usługi powiadomień o kondycji](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Dowiedz się więcej o [grupy akcji](../monitoring-and-diagnostics/monitoring-action-groups.md).