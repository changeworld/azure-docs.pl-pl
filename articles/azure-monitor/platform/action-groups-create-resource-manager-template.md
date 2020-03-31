---
title: Tworzenie grup akcji z szablonami Menedżera zasobów
description: Dowiedz się, jak utworzyć grupę akcji przy użyciu szablonu usługi Azure Resource Manager.
author: dkamstra
services: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 50ad9d57b24fab9ee57c2f9caae8f4c39d2681f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669084"
---
# <a name="create-an-action-group-with-a-resource-manager-template"></a>Tworzenie grupy akcji z szablonem Menedżera zasobów
W tym artykule pokazano, jak skonfigurować grupy akcji za pomocą [szablonu usługi Azure Resource Manager.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) Za pomocą szablonów można automatycznie skonfigurować grupy akcji, które mogą być ponownie używane w niektórych typach alertów. Te grupy akcji zapewniają, że wszystkie poprawne strony są powiadamiane o wyzwoleniu alertu.

Podstawowe kroki to:

1. Utwórz szablon jako plik JSON, który opisuje sposób tworzenia grupy akcji.

2. Wdrażanie szablonu przy użyciu [dowolnej metody wdrażania](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

Najpierw opisano, jak utworzyć szablon Menedżera zasobów dla grupy akcji, w której definicje akcji są zakodowane na miejscu w szablonie. Po drugie opisano sposób tworzenia szablonu, który przyjmuje informacje o konfiguracji elementu webhook jako parametry wejściowe podczas wdrażania szablonu.

## <a name="resource-manager-templates-for-an-action-group"></a>Szablony Menedżera zasobów dla grupy akcji

Aby utworzyć grupę akcji przy użyciu szablonu Menedżera `Microsoft.Insights/actionGroups`zasobów, należy utworzyć zasób tego typu . Następnie należy wypełnić wszystkie powiązane właściwości. Oto dwa przykładowe szablony, które tworzą grupę akcji.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2018-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
          {
            "name": "contosoSMS",
            "countryCode": "1",
            "phoneNumber": "5555551212"
          },
          {
            "name": "contosoSMS2",
            "countryCode": "1",
            "phoneNumber": "5555552121"
          }
        ],
        "emailReceivers": [
          {
            "name": "contosoEmail",
            "emailAddress": "devops@contoso.com"
          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com"
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1"
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    },
    "webhookReceiverName": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service Name."
      }
    },    
    "webhookServiceUri": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    }    
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2018-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
        ],
        "emailReceivers": [
        ],
        "webhookReceivers": [
          {
            "name": "[parameters('webhookReceiverName')]",
            "serviceUri": "[parameters('webhookServiceUri')]"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupResourceId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [grupach akcji](../../azure-monitor/platform/action-groups.md).
* Dowiedz się więcej o [alertach](alerts-overview.md).
* Dowiedz się, jak dodawać [alerty przy użyciu szablonu Menedżera zasobów](../../azure-monitor/platform/alerts-activity-log.md).

