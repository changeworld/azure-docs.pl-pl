---
title: Odbierzesz alerty dziennika działania dotyczące powiadomień dotyczących usługi Azure
description: Otrzymuj powiadomienia za pomocą programu SMS, wiadomości e-mail lub elementu webhook w przypadku wystąpienia usługi Azure.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/09/2018
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: 01dc3a3c6489b694af26c78ae3b4756f3e8f00b7
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263120"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Utwórz działanie alertów dziennika na powiadomień usługi
## <a name="overview"></a>Przegląd
W tym artykule pokazano, jak skonfigurować alerty dziennika działania dotyczące powiadomień o kondycji usługi za pomocą portalu Azure.  

Użytkownik otrzyma alert, gdy Azure wysyła powiadomienia o kondycji usługi do subskrypcji platformy Azure. Można skonfigurować alerty na podstawie:

- Klasa powiadomienia usługi kondycji (usługa problemy, planowana konserwacja klasyfikatory kondycji).
- Subskrypcja, których to dotyczy.
- Usług, których to dotyczy.
- Regionów, których to dotyczy.

Można także skonfigurować kogo wysyłane alertu:

- Wybierz istniejącą grupę akcji.
- Utwórz nową grupę akcję (która może służyć do następnych alertów).

Aby dowiedzieć się więcej o grupach akcji, zobacz [Utwórz i Zarządzaj grupami akcji](monitoring-action-groups.md).

Aby uzyskać informacje na temat konfigurowania alertów powiadomień kondycji usługi przy użyciu szablonów usługi Azure Resource Manager, zobacz [szablonów Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md).

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-by-using-the-azure-portal"></a>Utwórz alert na powiadomienia usługi kondycji dla nowej grupy akcji przy użyciu portalu Azure
1. W [portal](https://portal.azure.com), wybierz pozycję **kondycja usługi**.

    ![Usługa "Usługi kondycji"](./media/monitoring-activity-log-alerts-on-service-notifications/home-servicehealth.png)

2. W **alerty** zaznacz **alerty dotyczące kondycji**.

    ![Na karcie "Alerty dotyczące kondycji"](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades-sh.png)

3. Wybierz **alertu health usługi Utwórz** i wypełnij pola.

    ![Polecenie "Utwórz usługi kondycji alert"](./media/monitoring-activity-log-alerts-on-service-notifications/service-health-alert.png)

4. Wybierz **subskrypcji**, **usług**, i **regionów** chcesz otrzymywać alerty dla.

    ![Okno dialogowe "Dodaj alert dziennika aktywności"](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-new-ux.png)

> [!NOTE]
> Ta subskrypcja służy do zapisania alert dziennika aktywności. Zasób alertu jest wdrażana dla tej subskrypcji i monitorować zdarzenia w dzienniku aktywności.

5. Wybierz **typów zdarzeń** chcesz otrzymywać alerty dla: *usługi problem*, *zaplanowanej konserwacji*, i *klasyfikatory kondycji* 

6. Zdefiniuj szczegóły alertu, wprowadzając **Nazwa reguły alertów** i **opis**.

7. Wybierz **grupy zasobów** miejscu alert do zapisania.

8. Utwórz nową grupę akcji, wybierając **nową grupę akcji**. Wprowadź nazwę w **nazwy grupy akcji** i wpisz nazwę w **krótką nazwę** pole. Krótka nazwa odwołuje się do powiadomień wysyłanych po zgłoszeniu tego alertu.

    ![Utwórz nową grupę akcji](./media/monitoring-activity-log-alerts-on-service-notifications/action-group-creation.png)

9. Zdefiniować listę odbiornikami, zapewniając odbiorcy:

    a. **Nazwa**: Wprowadź nazwę odbiorcy, aliasu lub identyfikator.

    b. **Typ akcji**: Wybierz programu SMS, wiadomości e-mail, webhook i aplikacji usługi Azure.

    c. **Szczegóły**: oparte na wybrany typ akcji, wprowadź numer telefonu, adres e-mail, identyfikator URI elementu webhook,... itd.

10. Wybierz **OK** Aby utworzyć grupę akcji, a następnie **Utwórz regułę alertu** przeprowadzenie alertu.

W ciągu kilku minut alert jest aktywny i rozpoczyna się do wyzwalania na podstawie wybranych warunków, które zostały określone podczas tworzenia.

Dowiedz się, jak [skonfigurować powiadomienia elementu webhook dla istniejących systemów zarządzania problem](../service-health/service-health-alert-webhook-guide.md). Uzyskać informacji o schemacie elementu webhook dla alertów dotyczących działań w dzienniku, zobacz [elementów Webhook dla działania Azure rejestrowania alertów](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>Do ponownego użycia istniejącej grupy akcji dla wszystkich przyszłych definicji alertu jest grupa akcji zdefiniowane w tych krokach.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-by-using-the-azure-portal"></a>Utwórz alert na powiadomienia usługi kondycji dla istniejącej grupy akcji przy użyciu portalu Azure

1. Wykonaj kroki od 1 do 7 w poprzedniej sekcji, aby utworzyć powiadomienia usługi kondycji. 

2. W obszarze **grupy akcji Definiuj**, kliknij przycisk **grupy wybrana akcja** przycisku. Wybierz grupę odpowiednią akcję.

3. Wybierz **Dodaj** można dodać grupy akcji, a następnie **Utwórz regułę alertu** przeprowadzenie alertu.

W ciągu kilku minut alert jest aktywny i rozpoczyna się do wyzwalania na podstawie wybranych warunków, które zostały określone podczas tworzenia.

## <a name="manage-your-alerts"></a>Zarządzanie alertami

Po utworzeniu alertu nie jest widoczna **alerty** sekcji **Monitor**. Wybierz alert, który ma być zarządzany do:

* Czy chcesz go edytować.
* Usuń go.
* Wyłącz lub włącz go, jeśli chcesz tymczasowo zatrzymać lub wznowić odbieranie powiadomień o alercie.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, jak [skonfigurować powiadomienia elementu webhook dla istniejących systemów zarządzania problem](../service-health/service-health-alert-webhook-guide.md).
- Dowiedz się więcej o [usługi powiadomień o kondycji](monitoring-service-notifications.md).
- Dowiedz się więcej o [limitów szybkości powiadomień](monitoring-alerts-rate-limiting.md).
- Przegląd [schemat alertu elementu webhook dziennika aktywności](monitoring-activity-log-alerts-webhook.md).
- Pobierz [Przegląd alertów dotyczących działań w dzienniku](monitoring-overview-alerts.md)i dowiedzieć się, jak otrzymywać alerty. 
- Dowiedz się więcej o [grupy akcji](monitoring-action-groups.md).
