---
title: Odbieranie alertów dziennika aktywności w powiadomieniach o usłudze platformy Azure
description: Otrzymuuj powiadomienia za pośrednictwem wiadomości SMS, wiadomości e-mail lub elementu webhook po wystąpieniu usługi platformy Azure.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: d318adc76959ac24f4be9946167965a83053f632
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749305"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Tworzenie alertów dziennika aktywności dla powiadomień o usłudze
## <a name="overview"></a>Omówienie

W tym artykule pokazano, jak skonfigurować alerty dziennika aktywności dla powiadomień o kondycji usługi przy użyciu witryny Azure portal.  

Powiadomienia o kondycji usługi są przechowywane w [dzienniku aktywności platformy Azure](../azure-monitor/platform/platform-logs-overview.md) Biorąc pod uwagę prawdopodobnie dużą ilość informacji przechowywanych w dzienniku działań, istnieje oddzielny interfejs użytkownika, aby ułatwić wyświetlanie i konfigurowanie alertów dotyczących powiadomień o kondycji usługi. 

Możesz otrzymać alert, gdy platforma Azure wysyła powiadomienia o kondycji usługi do subskrypcji platformy Azure. Alert można skonfigurować na podstawie:

- Klasa powiadomienia o kondycji usługi (problemy z usługą, planowana konserwacja, porady dotyczące kondycji).
- Dotyczy to subskrypcji.
- Dotyczy to usług.
- Dotyczy to regionu(-ów).

> [!NOTE]
> Powiadomienia o kondycji usługi nie wysyła alertu dotyczące zdarzeń kondycji zasobów.

Można również skonfigurować, do kogo powinien zostać wysłany alert:

- Wybierz istniejącą grupę akcji.
- Utwórz nową grupę akcji (która może służyć do przyszłych alertów).

Aby dowiedzieć się więcej o grupach akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi](../azure-monitor/platform/action-groups.md).

Aby uzyskać informacje na temat konfigurowania alertów powiadomień o kondycji usługi przy użyciu szablonów usługi Azure Resource Manager, zobacz [Szablony Menedżera zasobów](../azure-monitor/platform/alerts-activity-log.md).

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Obejrzyj klip wideo dotyczący konfigurowania pierwszego alertu usługi Azure Service Health

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="alert-and-new-action-group-using-azure-portal"></a>Alerty i nowa grupa akcji przy użyciu witryny Azure portal
1. W [portalu](https://portal.azure.com)wybierz pozycję **Kondycja usługi**.

    ![Usługa "Service Health"](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. W sekcji **Alerty** wybierz pozycję **Alerty dotyczące kondycji**.

    ![Karta "Alerty o kondycji"](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Wybierz **pozycję Utwórz alert kondycji usługi** i wypełnij pola.

    ![Polecenie "Tworzenie alertu kondycji usługi"](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Wybierz **subskrypcję,** **usługi**i **regiony,** dla których chcesz otrzymywać alerty.

    ![Okno dialogowe "Dodawanie alertu dziennika aktywności"](media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)

    > [!NOTE]
    > Ta subskrypcja służy do zapisywania alertu dziennika aktywności. Zasób alertu jest wdrażany w tej subskrypcji i monitoruje zdarzenia w dzienniku aktywności dla niego.

1. Wybierz **typy zdarzeń,** dla których mają być alerty: *Problem z usługą,* *Planowana konserwacja*i *Porady dotyczące kondycji* 

1. Zdefiniuj szczegóły alertu, wprowadzając **nazwę reguły alertu** i **opis**.

1. Wybierz **grupę Zasobów,** w której chcesz zapisać alert.

1. Utwórz nową grupę akcji, wybierając pozycję **Nowa grupa akcji**. Wprowadź nazwę w polu **Nazwa grupy akcji** i wprowadź jej nazwę w polu Nazwa **krótka.** Krótka nazwa odwołuje się do powiadomień, które są wysyłane po uruchomieniu tego alertu.

    ![Tworzenie nowej grupy akcji](media/alerts-activity-log-service-notifications/action-group-creation.png)

1. Zdefiniuj listę odbiorników, udostępniając odbiornik:

    a. **Nazwa**: Wprowadź nazwę odbiorcy, alias lub identyfikator.

    b. **Typ akcji:** wybierz sms, e-mail, element webhook, aplikację Platformy Azure i inne.

    d. **Szczegóły**: Na podstawie wybranego typu akcji wprowadź numer telefonu, adres e-mail, identyfikator URI elementu webhook itp.

1. Wybierz **przycisk OK,** aby utworzyć grupę akcji, a następnie **utwórz regułę alertu,** aby ukończyć alert.

W ciągu kilku minut alert jest aktywny i zaczyna wyzwalać na podstawie warunków określonych podczas tworzenia.

Dowiedz się, jak [skonfigurować powiadomienia dotyczące elementu webhook dla istniejących systemów zarządzania problemami](service-health-alert-webhook-guide.md). Aby uzyskać informacje na temat schematu elementu webhook dla alertów dziennika aktywności, zobacz [Pliki webhook dla alertów dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

>[!NOTE]
>Grupa akcji zdefiniowana w tych krokach jest wielokrotnego użytku jako istniejąca grupa akcji dla wszystkich przyszłych definicji alertów.
>

## <a name="alert-with-existing-action-group-using-azure-portal"></a>Alert z istniejącą grupą akcji przy użyciu witryny Azure portal

1. Wykonaj kroki od 1 do 6 w poprzedniej sekcji, aby utworzyć powiadomienie o kondycji usługi. 

1. W obszarze **Definiowanie grupy akcji**kliknij przycisk **Wybierz grupę akcji.** Wybierz odpowiednią grupę akcji.

1. Wybierz **pozycję Dodaj,** aby dodać grupę akcji, a następnie **utwórz regułę alertu,** aby ukończyć alert.

W ciągu kilku minut alert jest aktywny i zaczyna wyzwalać na podstawie warunków określonych podczas tworzenia.

## <a name="alert-and-new-action-group-using-the-azure-resource-manager-templates"></a>Alerty i nowa grupa akcji przy użyciu szablonów usługi Azure Resource Manager

Oto przykład, który tworzy grupę akcji z celem wiadomości e-mail i włącza wszystkie powiadomienia o kondycji usługi dla subskrypcji docelowej.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "actionGroups_name": {
            "defaultValue": "SubHealth",
            "type": "String"
        },
        "activityLogAlerts_name": {
            "defaultValue": "ServiceHealthActivityLogAlert",
            "type": "String"
        },
        "emailAddress":{
            "type":"string"
        }
    },
    "variables": {
        "alertScope":"[concat('/','subscriptions','/',subscription().subscriptionId)]"
    },
    "resources": [
        {
            "comments": "Action Group",
            "type": "microsoft.insights/actionGroups",
            "name": "[parameters('actionGroups_name')]",
            "apiVersion": "2017-04-01",
            "location": "Global",
            "tags": {},
            "scale": null,
            "properties": {
                "groupShortName": "[parameters('actionGroups_name')]",
                "enabled": true,
                "emailReceivers": [
                    {
                        "name": "[parameters('actionGroups_name')]",
                        "emailAddress": "[parameters('emailAddress')]"
                    }
                ],
                "smsReceivers": [],
                "webhookReceivers": []
            },
            "dependsOn": []
        },
        {
            "comments": "Service Health Activity Log Alert",
            "type": "microsoft.insights/activityLogAlerts",
            "name": "[parameters('activityLogAlerts_name')]",
            "apiVersion": "2017-04-01",
            "location": "Global",
            "tags": {},
            "scale": null,
            "properties": {
                "scopes": [
                    "[variables('alertScope')]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ServiceHealth"
                        },
                        {
                            "field": "properties.incidentType",
                            "equals": "Incident"
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
                            "webhookProperties": {}
                        }
                    ]
                },
                "enabled": true,
                "description": ""
            },
            "dependsOn": [
                "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
            ]
        }
    ]
}
```

## <a name="manage-your-alerts"></a>Zarządzanie alertami

Po utworzeniu alertu jest on widoczny w sekcji **Alerty** **w monitorze**. Wybierz alert, który ma być odpowiedni:

* Edytuj go.
* Usuń go.
* Wyłącz lub włącz go, jeśli chcesz tymczasowo zatrzymać lub wznowić otrzymywanie powiadomień o alertie.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [najlepszych rozwiązaniach dotyczących konfigurowania alertów usługi Azure Service Health.](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa)
- Dowiedz się, jak [skonfigurować mobilne powiadomienia wypychane dla usługi Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Dowiedz się, jak [skonfigurować powiadomienia dotyczące elementu webhook dla istniejących systemów zarządzania problemami](service-health-alert-webhook-guide.md).
- Dowiedz się więcej o [powiadomieniach o kondycji usługi](service-notifications.md).
- Dowiedz się więcej o [ograniczaniu szybkości powiadomień](../azure-monitor/platform/alerts-rate-limiting.md).
- Przejrzyj [schemat programu webhook alertu dziennika aktywności](../azure-monitor/platform/activity-log-alerts-webhook.md).
- Zapoznaj się [z omówieniem alertów dziennika aktywności](../azure-monitor/platform/alerts-overview.md)i dowiedz się, jak otrzymywać alerty.
- Dowiedz się więcej o [grupach akcji](../azure-monitor/platform/action-groups.md).
