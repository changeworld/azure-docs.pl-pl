---
title: Tworzenie alertów usługi Azure Advisor dla nowych zaleceń
description: Tworzenie alertów usługi Azure Advisor dla nowych zaleceń
ms.topic: article
ms.date: 09/09/2019
ms.openlocfilehash: 07cbc57ef718b6cac104d2b5238ff4e3196f197a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443153"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations"></a>Tworzenie alertów usługi Azure Advisor na nowych zaleceniach 

W tym artykule pokazano, jak skonfigurować alert dla nowych zaleceń z usługi Azure Advisor przy użyciu witryny Azure portal i szablonów usługi Azure Resource Manager. 

Za każdym razem, gdy usługa Azure Advisor wykryje nowe zalecenie dla jednego z zasobów, zdarzenie jest przechowywane w [dzienniku aktywności platformy Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview) Alerty dla tych zdarzeń z usługi Azure Advisor można skonfigurować przy użyciu środowiska tworzenia alertów specyficznych dla zalecenia. Można wybrać subskrypcję i opcjonalnie grupę zasobów, aby określić zasoby, na których mają być odbierane alerty. 

Można również określić typy zaleceń przy użyciu następujących właściwości:

* Kategoria
* Poziom wpływu
* Typ rekomendacji

Można również skonfigurować akcję, która będzie mieć miejsce, gdy alert jest wyzwalany przez:  

* Wybieranie istniejącej grupy akcji
* Tworzenie nowej grupy akcji

Aby dowiedzieć się więcej o grupach akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi](../azure-monitor/platform/action-groups.md).

> [!NOTE] 
> Alerty klasyfikatora są obecnie dostępne tylko dla zaleceń dotyczących wysokiej dostępności, wydajności i kosztów. Zalecenia dotyczące zabezpieczeń nie są obsługiwane. 

## <a name="in-the-azure-portal"></a>W witrynie Azure Portal
1. W **portalu**wybierz pozycję **Azure Advisor**.

    ![Doradca platformy Azure w portalu](./media/advisor-alerts/create1.png)

2. W sekcji **Monitorowanie** w menu po lewej stronie wybierz pozycję **Alerty**. 

    ![Alerty w programie Advisor](./media/advisor-alerts/create2.png)

3. Wybierz **pozycję Nowy alert doradcy**.

    ![Alert nowego doradcy](./media/advisor-alerts/create3.png)

4. W sekcji **Zakres** wybierz subskrypcję i opcjonalnie grupę zasobów, na której chcesz otrzymywać alerty. 

    ![Zakres alertu doradcy](./media/advisor-alerts/create4.png)

5. W sekcji **Warunek** wybierz metodę, której chcesz użyć do skonfigurowania alertu. Jeśli chcesz otrzymywać alerty dotyczące wszystkich zaleceń dotyczących określonej kategorii i/lub poziomu wpływu, wybierz opcję **Kategoria i poziom wpływu**. Jeśli chcesz otrzymywać alerty dotyczące wszystkich zaleceń określonego typu, wybierz **opcję Typ rekomendacji**.

    ![Warunek alertu usługi Azure Advisor](./media/advisor-alerts/create5.png)

6. W zależności od wybranej opcji Konfiguruj według, można określić kryteria. Jeśli chcesz wszystkie zalecenia, po prostu pozostaw pozostałe pola puste. 

    ![Grupa akcji alertów doradcy](./media/advisor-alerts/create6.png)

7. W sekcji **Grupy akcji** wybierz pozycję **Dodaj istniejącą,** aby użyć już utworzonej grupy akcji, lub wybierz pozycję **Utwórz nowy,** aby skonfigurować nową [grupę akcji](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups). 

    ![Alert klasyfikatora dodaj istniejące](./media/advisor-alerts/create7.png)

8. W sekcji Szczegóły alertu nadaj alertowi nazwę i krótki opis. Jeśli chcesz, aby alert był włączony, po ustawieniu wyboru opcji **Tak** **po utworzeniu pozostaw regułę Włącz.** Następnie wybierz grupę zasobów, w aby zapisać alert. Nie wpłynie to na zakres kierowania zalecenia. 

    ![Baner doradcy platformy Azure](./media/advisor-alerts/create8.png)


## <a name="with-an-azure-resource-manager-template"></a>Z szablonem usługi Azure Resource Manager

Ten szablon Menedżera zasobów tworzy alert rekomendacji i nową grupę akcji.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest2",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
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
      "comments": "Azure Advisor Activity Log Alert",
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
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
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

## <a name="configure-recommendation-alerts-to-use-a-webhook"></a>Konfigurowanie alertów rekomendacji do używania elementu webhook
W tej sekcji pokazano, jak skonfigurować alerty usługi Azure Advisor do wysyłania danych rekomendacji za pośrednictwem łączy webhook do istniejących systemów. 

Alerty można skonfigurować, aby otrzymywać powiadomienia, gdy masz nowe zalecenie doradcy dotyczące jednego z zasobów. Te alerty mogą powiadamiać użytkownika za pośrednictwem poczty e-mail lub wiadomości tekstowej, ale mogą być również używane do integracji z istniejącymi systemami za pośrednictwem elementu webhook. 


### <a name="using-the-advisor-recommendation-alert-payload"></a>Korzystanie z ładunku alertu alertu zalecenia doradcy
Jeśli chcesz zintegrować alerty Advisor do własnych systemów przy użyciu elementu webhook, należy przeanalizować ładunek JSON, który jest wysyłany z powiadomienia. 

Podczas konfigurowania grupy akcji dla tego alertu, należy wybrać, czy chcesz użyć wspólnego schematu alertu. Jeśli wybierzesz wspólny schemat alertu, ładunek będzie wyglądał następująco: 

```json
{  
   "schemaId":"azureMonitorCommonAlertSchema",
   "data":{  
      "essentials":{  
         "alertId":"/subscriptions/<subid>/providers/Microsoft.AlertsManagement/alerts/<alerted>",
         "alertRule":"Webhhook-test",
         "severity":"Sev4",
         "signalType":"Activity Log",
         "monitorCondition":"Fired",
         "monitoringService":"Activity Log - Recommendation",
         "alertTargetIDs":[  
            "/subscriptions/<subid>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>"
         ],
         "originAlertId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "firedDateTime":"2019-07-17T23:00:57.3858656Z",
         "description":"A new recommendation is available.",
         "essentialsVersion":"1.0",
         "alertContextVersion":"1.0"
      },
      "alertContext":{  
         "channels":"Operation",
         "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
         "caller":"Microsoft.Advisor",
         "correlationId":"8554b847-2a72-48ef-9776-600aca3c3aab",
         "eventSource":"Recommendation",
         "eventTimestamp":"2019-07-17T22:28:54.1566942+00:00",
         "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
         "eventDataId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "level":"Informational",
         "operationName":"Microsoft.Advisor/recommendations/available/action",
         "properties":{  
            "recommendationSchemaVersion":"1.0",
            "recommendationCategory":"Performance",
            "recommendationImpact":"Medium",
            "recommendationName":"Increase the MariaDB server vCores",
            "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%<subscription id>%2FresourceGroups%2<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
            "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
         },
         "status":"Active",
         "subStatus":"",
         "submissionTimestamp":"2019-07-17T22:28:54.1566942+00:00"
      }
   }
}
  ```

Jeśli nie używasz wspólnego schematu, ładunek wygląda następująco: 

```json
{  
   "schemaId":"Microsoft.Insights/activityLogs",
   "data":{  
      "status":"Activated",
      "context":{  
         "activityLog":{  
            "channels":"Operation",
            "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
            "caller":"Microsoft.Advisor",
            "correlationId":"3ea7320f-c002-4062-adb8-96d3bd92a5f4",
            "description":"A new recommendation is available.",
            "eventSource":"Recommendation",
            "eventTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
            "eventDataId":"a12b8e59-0b1d-4003-bfdc-3d8152922e59",
            "level":"Informational",
            "operationName":"Microsoft.Advisor/recommendations/available/action",
            "properties":{  
               "recommendationSchemaVersion":"1.0",
               "recommendationCategory":"Performance",
               "recommendationImpact":"Medium",
               "recommendationName":"Increase the MariaDB server vCores",
               "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%2F<subscription id>%2FresourceGroups%2F<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
               "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
            },
            "resourceId":"/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>",
            "resourceGroupName":"<resource group name>",
            "resourceProviderName":"MICROSOFT.DBFORMARIADB",
            "status":"Active",
            "subStatus":"",
            "subscriptionId":"<subscription id>",
            "submissionTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "resourceType":"MICROSOFT.DBFORMARIADB/SERVERS"
         }
      },
      "properties":{  
 
      }
   }
}
```

W każdym schemacie można zidentyfikować zdarzenia rekomendacji advisor, wyszukując **eventSource** is `Recommendation` i **operationName** jest `Microsoft.Advisor/recommendations/available/action`.

Niektóre z innych ważnych pól, których warto użyć, to: 

* *alertTargetIDs* (w schemacie wspólnym) lub *resourceId* (starszy schemat)
* *zalecenieTyp*
* *nazwa rekomendacji*
* *rekomendacja Kategoria*
* *rekomendacjaNawpływ*
* *rekomendacjaResourceLink*


## <a name="manage-your-alerts"></a>Zarządzanie alertami 

Z usługi Azure Advisor można edytować, usuwać lub wyłączać i włączać alerty rekomendacji. 

1. W **portalu**wybierz pozycję **Azure Advisor**.

    ![Baner doradcy platformy Azure](./media/advisor-alerts/create1.png)

2. W sekcji **Monitorowanie** w menu po lewej stronie wybierz pozycję **Alerty**.

    ![Baner doradcy platformy Azure](./media/advisor-alerts/create2.png)

3. Aby edytować alert, kliknij nazwę alertu, aby otworzyć alert i edytować pola, które chcesz edytować.

4. Aby usunąć, włączyć lub wyłączyć alert, kliknij elipsę na końcu wiersza, a następnie wybierz akcję, którą chcesz podjąć.
 

