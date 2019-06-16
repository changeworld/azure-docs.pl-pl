---
title: Tworzenie grup akcji przy użyciu szablonów usługi Resource Manager
description: Dowiedz się, jak utworzyć grupy akcji przy użyciu szablonu usługi Azure Resource Manager.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 638dd8efba5e86bb7e8abb78a41196bfac9524df
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60709970"
---
# <a name="create-an-action-group-with-a-resource-manager-template"></a>Utwórz grupę akcji przy użyciu szablonu usługi Resource Manager
W tym artykule dowiesz się, jak używać [szablonu usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) do skonfigurowania grup akcji. Za pomocą szablonów, można automatycznie skonfigurować grupy akcji, których można użyć ponownie w pewnych typów alertów. Te grupy akcji upewnij się, że poprawne strony powiadomienie, gdy zostanie wyzwolony alert.

Przedstawiono podstawowe kroki:

1. Utwórz szablon jako plik JSON, który opisuje sposób tworzenia grupy akcji.

2. Wdrażanie szablonu przy użyciu [dowolnej metody wdrażania](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

Po pierwsze opisano, jak utworzyć szablon usługi Resource Manager dla grupy akcji, których definicje akcji są zakodowane w szablonie. Po drugie opisano sposób tworzenia szablonu, który pobiera informacje o konfiguracji elementu webhook jako parametry wejściowe podczas wdrażania szablonu.

## <a name="resource-manager-templates-for-an-action-group"></a>Szablony usługi Resource Manager dla grupy akcji

Aby utworzyć grupy akcji przy użyciu szablonu usługi Resource Manager, należy utworzyć zasób tego typu `Microsoft.Insights/actionGroups`. Następnie należy wypełnić wszystkie powiązane właściwości. Poniżej przedstawiono dwa przykładowe szablony, które Utwórz grupę akcji.

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


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [grup akcji](../../azure-monitor/platform/action-groups.md).
* Dowiedz się więcej o [alerty](alerts-overview.md).
* Dowiedz się, jak dodać [alertów przy użyciu szablonu usługi Resource Manager](../../azure-monitor/platform/alerts-activity-log.md).

