---
title: Odbieranie alertów dziennika aktywności dla powiadomień dotyczących usług platformy Azure
description: Odbieraj powiadomienia w wiadomościach SMS, wiadomości e-mail lub element webhook po wystąpieniu usługi platformy Azure.
author: shawntabrizi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/09/2018
ms.author: shtabriz
ms.openlocfilehash: 3157cef6952f01d689d3cb4a30c9d7c843095809
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071693"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Tworzenie alertów dziennika aktywności dla powiadomień dotyczących usług
## <a name="overview"></a>Omówienie
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

Aby dowiedzieć się więcej o grupach akcji, zobacz [Create and manage action groups (Tworzenie grup akcji i zarządzanie nimi)](../../azure-monitor/platform/action-groups.md).

Aby uzyskać informacje na temat konfigurowania usługi kondycji powiadomień alertów przy użyciu szablonów usługi Azure Resource Manager, zobacz [szablonów usługi Resource Manager](alerts-activity-log.md).

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Obejrzyj film wideo na temat konfigurowania pierwszy alert usługi Azure Service Health

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="alert-and-new-action-group-using-azure-portal"></a>Akcja alertu i nowe grupy przy użyciu witryny Azure portal
1. W [portal](https://portal.azure.com), wybierz opcję **Service Health**.

    ![Usługa "Service Health"](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. W **alerty** zaznacz **alerty dotyczące kondycji**.

    ![Na karcie "Alerty dotyczące kondycji"](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Wybierz **Tworzenie alertów dotyczących kondycji usługi** i wypełnij pola.

    ![Polecenie "Utwórz alert kondycji usługi"](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Wybierz **subskrypcji**, **usług**, i **regionów** mają dotyczyć alerty.

    ![Okno dialogowe "Dodaj alert dziennika aktywności"](media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)

> [!NOTE]
> Ta subskrypcja jest używana do zapisać alert dziennika aktywności. Alertu zasobów zostanie wdrożone do tej subskrypcji i monitoruje zdarzeń w dzienniku aktywności.

1. Wybierz **typy zdarzeń** chcesz otrzymywać alerty dla: *Usługa problem*, *planowanej konserwacji*, i *porad dotyczących kondycji* 

1. Zdefiniuj informacje dotyczące alertu, wprowadzając **Nazwa reguły alertu** i **opis**.

1. Wybierz **grupy zasobów** miejscu alertu do zapisania.

1. Utwórz nową grupę akcji, wybierając **Nowa grupa akcji**. Wprowadź nazwę w **Nazwa grupy akcji** polu, a następnie wprowadź nazwę w **krótką nazwę** pole. Krótka nazwa odwołuje się do powiadomień, które są wysyłane, gdy zostanie wyzwolony alert.

    ![Utwórz nową grupę akcji](media/alerts-activity-log-service-notifications/action-group-creation.png)

1. Zdefiniuj listę odbiorców, podając odbiorcę firmy:

    a. **Nazwa**: Wprowadź nazwę, alias lub identyfikator odbiorcy.

    b. **Typ akcji**: Wybierz wiadomości SMS, wiadomości e-mail, element webhook i aplikacji platformy Azure.

    c. **Szczegóły**: Oparte na wybrany typ akcji, wprowadź numer telefonu, adres e-mail, identyfikator URI elementu webhook, itp.

1. Wybierz **OK** Aby utworzyć grupę akcji, a następnie **Utwórz regułę alertu** do ukończenia alertu.

W ciągu kilku minut ten alert jest aktywna i rozpoczyna się do wyzwalania na podstawie warunków, które zostały określone podczas tworzenia.

Dowiedz się, jak [Konfigurowanie powiadomień elementu webhook dla istniejących systemów zarządzania problem](../../service-health/service-health-alert-webhook-guide.md). Aby uzyskać informacje dotyczące schematu elementu webhook dla alertów dziennika aktywności, zobacz [alerty dzienników elementy Webhook dla aktywności platformy Azure](../../azure-monitor/platform/activity-log-alerts-webhook.md).

>[!NOTE]
>Grupa akcji zdefiniowane w ramach tej procedury jest do ponownego użycia istniejącej grupy akcji dla wszystkich przyszłych definicji alertu.
>
>

## <a name="alert-with-existing-action-group-using-azure-portal"></a>Alert o istniejącej grupy akcji przy użyciu witryny Azure portal

1. Wykonaj kroki od 1 do 6 w poprzedniej sekcji, aby utworzyć powiadomienia usługi kondycji. 

1. W obszarze **zdefiniuj grupę akcji**, kliknij przycisk **grupy akcji wybierania** przycisku. Wybierz grupę odpowiednie działania.

1. Wybierz **Dodaj** można dodać do grupy akcji, a następnie **Utwórz regułę alertu** do ukończenia alertu.

W ciągu kilku minut ten alert jest aktywna i rozpoczyna się do wyzwalania na podstawie warunków, które zostały określone podczas tworzenia.

## <a name="alert-and-new-action-group-using-the-azure-resource-manager-templates"></a>Akcja alertu i nowe grupy przy użyciu szablonów usługi Azure Resource Manager

Oto przykład, który tworzy grupy akcji z elementem docelowym poczty e-mail i włącza wszystkie powiadomień dotyczących kondycji usługi dla subskrypcji docelowej.

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

Po utworzeniu alertu, jest ona widoczna na **alerty** części **Monitor**. Wybierz alert, który ma być zarządzany do:

* Czy chcesz go edytować.
* Usuń go.
* Wyłączyć lub włączyć, jeśli chcesz tymczasowo zatrzymać lub wznowić odbieranie powiadomień o alercie.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [najlepsze rozwiązania dotyczące konfigurowania alertów usługi Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Dowiedz się, jak [Instalatora mobilnych powiadomień wypychanych dla usługi Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Dowiedz się, jak [Konfigurowanie powiadomień elementu webhook dla istniejących systemów zarządzania problem](../../service-health/service-health-alert-webhook-guide.md).
- Dowiedz się więcej o [usługi powiadomień dotyczących kondycji](../../azure-monitor/platform/service-notifications.md).
- Dowiedz się więcej o [powiadomień szybkości](../../azure-monitor/platform/alerts-rate-limiting.md).
- Przegląd [schemat elementów webhook alertu dziennika aktywności](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Pobierz [Przegląd alertów dziennika aktywności](../../azure-monitor/platform/alerts-overview.md)i Dowiedz się, jak otrzymywać alerty. 
- Dowiedz się więcej o [grup akcji](../../azure-monitor/platform/action-groups.md).
