---
title: Konfigurowanie alertów dotyczących kondycji usługi platformy Azure przy użyciu OpsGenie | Dokumentacja firmy Microsoft
description: Uzyskaj Spersonalizowane powiadomienia dotyczące zdarzenia usługi service health do swojego wystąpienia OpsGenie.
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
ms.openlocfilehash: 6e9d44c4b64e41b04c40d0c511a638e539752da4
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435030"
---
# <a name="configure-service-health-alerts-with-opsgenie"></a>Konfigurowanie alertów dotyczących kondycji usługi przy użyciu OpsGenie

W tym artykule pokazano, jak skonfigurować alerty dotyczące kondycji usługi platformy Azure za pomocą OpsGenie przy użyciu elementu webhook. Za pomocą [OpsGenie](https://www.opsgenie.com/)firmy integracji kondycji usługi platformy Azure, w przypadku przekazywania alertów usługi Azure Service Health, aby OpsGenie. OpsGenie można określić odpowiednie osoby do powiadomienia oparte na dyżurów harmonogramy, za pomocą poczty e-mail, wiadomości tekstowych (SMS), połączenia telefoniczne, iOS i powiadomienia wypychane w systemie Android i zamocowaniem alertów, dopóki ten alert zostanie potwierdzone lub zamknięte.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Tworzenie adresu URL usługi kondycji integracji w OpsGenie
1.  Upewnij się, podpisali i zalogowano się do Twojej [OpsGenie](https://www.opsgenie.com/) konta.

1.  Przejdź do **integracje** sekcji OpsGenie.

    ![W sekcji "Integracja" w OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

1.  Wybierz **usługi Azure Service Health** przycisk integracji.

    !["Usługa Azure Service Health button" w OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

1.  **Nazwa** swoje alert, a następnie określ **przypisane do zespołu** pola.

1.  Wypełnianie pól, takich jak **adresatów**, **włączone**, i **Pomijaj powiadomienia**.

1.  Skopiuj i Zapisz **adresów URL integracji**, która już powinna zawierać Twoje `apiKey` dołączany na końcu.

    !["Integracja z adresu URL" w OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

1.  Wybierz **Zapisz integracji**

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Utwórz alert przy użyciu OpsGenie w witrynie Azure portal
### <a name="for-a-new-action-group"></a>Dla nowej grupy akcji:
1. Wykonaj kroki od 1 do 8 w [Tworzenie alertu na powiadomienie usługi kondycji dla nowej grupy akcji przy użyciu witryny Azure portal](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

1. Zdefiniuj na liście **akcje**:

    a. **Typ akcji:** *elementu Webhook*

    b. **Szczegóły:** OpsGenie **adresów URL integracji** zostanie zapisany wcześniej.

    c. **Nazwa:** przez element Webhook nazwy, aliasu lub identyfikator.

1. Wybierz **Zapisz** po zakończeniu tworzenia alertu.

### <a name="for-an-existing-action-group"></a>Dla istniejącej grupy akcji:
1. W [witryny Azure portal](https://portal.azure.com/), wybierz opcję **Monitor**.

1. W **ustawienia** zaznacz **grup akcji**.

1. Znajdź i wybierz grupę akcji, którą chcesz edytować.

1. Dodaj do listy **akcje**:

    a. **Typ akcji:** *elementu Webhook*

    b. **Szczegóły:** OpsGenie **adresów URL integracji** zostanie zapisany wcześniej.

    c. **Nazwa:** przez element Webhook nazwy, aliasu lub identyfikator.

1. Wybierz **Zapisz** po zakończeniu można zaktualizować grupy akcji.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testowanie integracji usługi elementu webhook, za pomocą żądania HTTP POST
1. Utwórz ładunek kondycji usługi, które mają zostać wysłane. Można znaleźć, ładunek elementu webhook przykład usługi kondycji w [alerty dzienników elementy Webhook dla aktywności platformy Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

1. Utwórz żądanie HTTP POST w następujący sposób:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Powinien zostać wyświetlony `200 OK` odpowiedzi z komunikatem o stanie "" pomyślnie ukończono.

1. Przejdź do [OpsGenie](https://www.opsgenie.com/) aby upewnić się, że integracji usługi zostało pomyślnie skonfigurowane.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, jak [Konfigurowanie powiadomień elementu webhook dla istniejących systemów zarządzania problem](service-health-alert-webhook-guide.md).
- Przegląd [schemat elementów webhook alertu dziennika aktywności](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Dowiedz się więcej o [usługi powiadomień dotyczących kondycji](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Dowiedz się więcej o [grup akcji](../monitoring-and-diagnostics/monitoring-action-groups.md).