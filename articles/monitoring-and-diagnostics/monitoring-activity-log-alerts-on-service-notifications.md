---
title: Odbieranie alertów dziennika aktywności dla powiadomień dotyczących usług platformy Azure
description: Odbieraj powiadomienia w wiadomościach SMS, wiadomości e-mail lub element webhook po wystąpieniu usługi platformy Azure.
author: shawntabrizi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/09/2018
ms.author: shtabriz
ms.component: alerts
ms.openlocfilehash: 6e1a72c428425c73ff0446fc0d41b1b18333c3e3
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423892"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Tworzenie alertów dziennika aktywności dla powiadomień dotyczących usług
## <a name="overview"></a>Przegląd
W tym artykule pokazano, jak skonfigurować alerty dziennika aktywności dla powiadomień dotyczących kondycji usługi przy użyciu witryny Azure portal.  

Mogą otrzymać alert, gdy platforma Azure wysyła powiadomienia o kondycji usług do Twojej subskrypcji platformy Azure. Można skonfigurować alerty na podstawie:

- Klasa powiadomienia o kondycji usługi (Service problemów planowana konserwacja porad dotyczących kondycji).
- Subskrypcja, w których to dotyczy.
- Usługi, których to dotyczy.
- Regiony, w których to dotyczy.

> [!NOTE]
> Powiadomienia o kondycji usług nie wysyła alert dotyczące zasobów zdarzenia dotyczące kondycji.

Można także skonfigurować kogo wysyłane alertu:

- Wybierz istniejącą grupę akcji.
- Utwórz nową grupę akcji, (które mogą służyć do następnych alertów).

Aby dowiedzieć się więcej o grupach akcji, zobacz [Create and manage action groups (Tworzenie grup akcji i zarządzanie nimi)](monitoring-action-groups.md).

Aby uzyskać informacje na temat konfigurowania usługi kondycji powiadomień alertów przy użyciu szablonów usługi Azure Resource Manager, zobacz [szablonów usługi Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md).

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-by-using-the-azure-portal"></a>Tworzenie alertu na powiadomienie usługi kondycji dla nowej grupy akcji przy użyciu witryny Azure portal
1. W [portal](https://portal.azure.com), wybierz opcję **Service Health**.

    ![Usługa "Service Health"](./media/monitoring-activity-log-alerts-on-service-notifications/home-servicehealth.png)

1. W **alerty** zaznacz **alerty dotyczące kondycji**.

    ![Na karcie "Alerty dotyczące kondycji"](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades-sh.png)

1. Wybierz **Tworzenie alertów dotyczących kondycji usługi** i wypełnij pola.

    ![Polecenie "Utwórz alert kondycji usługi"](./media/monitoring-activity-log-alerts-on-service-notifications/service-health-alert.png)

1. Wybierz **subskrypcji**, **usług**, i **regionów** mają dotyczyć alerty.

    ![Okno dialogowe "Dodaj alert dziennika aktywności"](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-new-ux.png)

> [!NOTE]
> Ta subskrypcja jest używana do zapisać alert dziennika aktywności. Alertu zasobów zostanie wdrożone do tej subskrypcji i monitoruje zdarzeń w dzienniku aktywności.

1. Wybierz **typy zdarzeń** chcesz otrzymywać alerty dla: *usługi problem*, *planowanej konserwacji*, i *porad dotyczących kondycji* 

1. Zdefiniuj informacje dotyczące alertu, wprowadzając **Nazwa reguły alertu** i **opis**.

1. Wybierz **grupy zasobów** miejscu alertu do zapisania.

1. Utwórz nową grupę akcji, wybierając **Nowa grupa akcji**. Wprowadź nazwę w **Nazwa grupy akcji** polu, a następnie wprowadź nazwę w **krótką nazwę** pole. Krótka nazwa odwołuje się do powiadomień, które są wysyłane, gdy zostanie wyzwolony alert.

    ![Utwórz nową grupę akcji](./media/monitoring-activity-log-alerts-on-service-notifications/action-group-creation.png)

1. Zdefiniuj listę odbiorców, podając odbiorcę firmy:

    a. **Nazwa**: Wprowadź nazwę, alias lub identyfikator odbiorcy.

    b. **Typ akcji**: Wybierz wiadomości SMS, wiadomości e-mail, element webhook i aplikacji platformy Azure.

    c. **Szczegóły**: oparte na wybrany typ akcji, wprowadź numer telefonu, adres e-mail, identyfikator URI elementu webhook, itp.

1. Wybierz **OK** Aby utworzyć grupę akcji, a następnie **Utwórz regułę alertu** do ukończenia alertu.

W ciągu kilku minut ten alert jest aktywna i rozpoczyna się do wyzwalania na podstawie warunków, które zostały określone podczas tworzenia.

Dowiedz się, jak [Konfigurowanie powiadomień elementu webhook dla istniejących systemów zarządzania problem](../service-health/service-health-alert-webhook-guide.md). Aby uzyskać informacje dotyczące schematu elementu webhook dla alertów dziennika aktywności, zobacz [alerty dzienników elementy Webhook dla aktywności platformy Azure](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>Grupa akcji zdefiniowane w ramach tej procedury jest do ponownego użycia istniejącej grupy akcji dla wszystkich przyszłych definicji alertu.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-by-using-the-azure-portal"></a>Tworzenie alertu na powiadomienie usługi kondycji dla istniejącej grupy akcji przy użyciu witryny Azure portal

1. Wykonaj kroki od 1 do 7 w poprzedniej sekcji, aby utworzyć powiadomienia usługi kondycji. 

1. W obszarze **zdefiniuj grupę akcji**, kliknij przycisk **grupy akcji wybierania** przycisku. Wybierz grupę odpowiednie działania.

1. Wybierz **Dodaj** można dodać do grupy akcji, a następnie **Utwórz regułę alertu** do ukończenia alertu.

W ciągu kilku minut ten alert jest aktywna i rozpoczyna się do wyzwalania na podstawie warunków, które zostały określone podczas tworzenia.

## <a name="manage-your-alerts"></a>Zarządzanie alertami

Po utworzeniu alertu, jest ona widoczna na **alerty** części **Monitor**. Wybierz alert, który ma być zarządzany do:

* Czy chcesz go edytować.
* Usuń go.
* Wyłączyć lub włączyć, jeśli chcesz tymczasowo zatrzymać lub wznowić odbieranie powiadomień o alercie.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, jak [Konfigurowanie powiadomień elementu webhook dla istniejących systemów zarządzania problem](../service-health/service-health-alert-webhook-guide.md).
- Dowiedz się więcej o [usługi powiadomień dotyczących kondycji](monitoring-service-notifications.md).
- Dowiedz się więcej o [powiadomień szybkości](monitoring-alerts-rate-limiting.md).
- Przegląd [schemat elementów webhook alertu dziennika aktywności](monitoring-activity-log-alerts-webhook.md).
- Pobierz [Przegląd alertów dziennika aktywności](monitoring-overview-alerts.md)i Dowiedz się, jak otrzymywać alerty. 
- Dowiedz się więcej o [grup akcji](monitoring-action-groups.md).
