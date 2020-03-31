---
title: Wysyłanie alertów kondycji usługi platformy Azure za pomocą opsGenie przy użyciu elementów webhook
description: Uzyskaj spersonalizowane powiadomienia o zdarzeniach kondycji usługi do wystąpienia OpsGenie.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: def12d5e7b1b93b8370cd7be61538fca53531ae1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654141"
---
# <a name="send-azure-service-health-alerts-with-opsgenie-using-webhooks"></a>Wysyłanie alertów kondycji usługi platformy Azure za pomocą opsGenie przy użyciu elementów webhook

W tym artykule pokazano, jak skonfigurować alerty kondycji usługi platformy Azure za pomocą opsGenie przy użyciu elementu webhook. Za pomocą [OpsGenie](https://www.opsgenie.com/)'s Azure Service Health Integration, można przesyłać dalej alerty usługi Azure Service Health do OpsGenie. OpsGenie może określić odpowiednie osoby do powiadamiania na podstawie harmonogramów rozmów, za pomocą poczty e-mail, wiadomości tekstowych (SMS), połączeń telefonicznych, powiadomień push z systemem iOS & Android i eskalacji alertów, dopóki alert nie zostanie potwierdzony lub zamknięty.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Tworzenie adresu URL integracji kondycji usługi w OpsGenie
1.  Upewnij się, że zarejestrowałeś się i zalogowałeś się na swoje konto [OpsGenie.](https://www.opsgenie.com/)

1.  Przejdź do sekcji **Integracje** w OpsGenie.

    ![Sekcja "Integracje" w OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

1.  Wybierz przycisk integracji **usługi Azure Service Health.**

    ![Przycisk "Kondycja usługi azure" w opsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

1.  **Nazwij** alert i określ pole **Przypisane do zespołu.**

1.  Wypełnij inne pola, takie jak **Adresaci,** **Włączone**i **Pomiń powiadomienia**.

1.  Skopiuj i zapisz adres `apiKey` URL **integracji,** który powinien już zawierać dołączony do końca.

    !["Adres URL integracji" w OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

1.  Wybierz **pozycję Zapisz integrację**

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Tworzenie alertu przy użyciu usługi OpsGenie w witrynie Azure portal
### <a name="for-a-new-action-group"></a>Dla nowej grupy akcji:
1. Wykonaj kroki od 1 do 8 w [Tworzenie alertu w powiadomieniu o kondycji usługi dla nowej grupy akcji przy użyciu portalu Azure.](../azure-monitor/platform/alerts-activity-log-service-notifications.md)

1. Zdefiniuj na liście **akcji:**

    a. **Typ akcji:** *Element webhook*

    b. **Szczegóły:** Wcześniej zapisany **adres URL integracji** OpsGenie.

    d. **Nazwa:** Nazwa, alias lub identyfikator elementu webhook.

1. Wybierz **pozycję Zapisz** po wykonaniu, aby utworzyć alert.

### <a name="for-an-existing-action-group"></a>W przypadku istniejącej grupy akcji:
1. W [witrynie Azure portal](https://portal.azure.com/)wybierz pozycję **Monitor**.

1. W sekcji **Ustawienia** wybierz pozycję **Grupy akcji**.

1. Znajdź i wybierz grupę akcji, którą chcesz edytować.

1. Dodaj do listy **akcji:**

    a. **Typ akcji:** *Element webhook*

    b. **Szczegóły:** Wcześniej zapisany **adres URL integracji** OpsGenie.

    d. **Nazwa:** Nazwa, alias lub identyfikator elementu webhook.

1. Wybierz **pozycję Zapisz** po zakończeniu, aby zaktualizować grupę akcji.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testowanie integracji elementu webhook za pomocą żądania HTTP POST
1. Utwórz ładunek kondycji usługi, który chcesz wysłać. Przykładowy ładunek elementu webhook kondycji usługi można znaleźć w [witrynach Webhooks dla alertów dziennika aktywności platformy Azure.](../azure-monitor/platform/activity-log-alerts-webhook.md)

1. Utwórz żądanie HTTP POST w następujący sposób:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Powinieneś otrzymać `200 OK` odpowiedź z komunikatem "udany".

1. Przejdź do [OpsGenie,](https://www.opsgenie.com/) aby potwierdzić, że integracja została pomyślnie skonfigurowana.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [skonfigurować powiadomienia dotyczące elementu webhook dla istniejących systemów zarządzania problemami](service-health-alert-webhook-guide.md).
- Przejrzyj [schemat programu webhook alertu dziennika aktywności](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Dowiedz się więcej o [powiadomieniach o kondycji usługi](../azure-monitor/platform/service-notifications.md).
- Dowiedz się więcej o [grupach akcji](../azure-monitor/platform/action-groups.md).