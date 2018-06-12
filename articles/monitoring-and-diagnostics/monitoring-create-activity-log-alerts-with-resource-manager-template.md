---
title: Utwórz alert dziennika aktywności za pomocą szablonu usługi Resource Manager
description: Otrzymuj powiadomienia w przypadku zasobów platformy Azure są tworzone.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/06/2017
ms.author: ancav
ms.component: alerts
ms.openlocfilehash: a1e28f08231ae1fbef3e0d0306e986c1dc9d1d1c
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262994"
---
# <a name="create-an-activity-log-alert-with-a-resource-manager-template"></a>Utwórz alert dziennika aktywności za pomocą szablonu usługi Resource Manager
W tym artykule przedstawiono sposób użycia [szablonu usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) Konfigurowanie alertów dziennika aktywności. Przy użyciu szablonów, można łatwo skonfigurować wiele alertów, które aktywacji na podstawie określonego działania dziennika zdarzeń warunków w ramach procesu wdrażania automatycznego.

Przedstawiono podstawowe czynności:

1. Tworzenie szablonu w formacie JSON, który opisuje sposób tworzenia alert dziennika aktywności.

2. Wdrażanie szablonu przy użyciu [dowolnej metody wdrażania](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

## <a name="resource-manager-template-for-an-activity-log-alert"></a>Szablon usługi Resource Manager alertu dziennika aktywności
Aby utworzyć alert dziennika aktywności za pomocą szablonu usługi Resource Manager, utwórz zasób typu `microsoft.insights/activityLogAlerts`. Następnie należy wypełnić wszystkie powiązane właściwości. Poniżej przedstawiono szablon, który tworzy alert dziennika aktywności.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

Można znaleźć w naszych [galerii Szybki Start Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Insights) niektóre przykłady szablonów alertu dziennika działań.

> [!NOTE]

> Można również utworzyć działanie reguły alertów dziennika przy użyciu funkcji użytkownika w monitorze > [alertów (wersja zapoznawcza)](monitoring-overview-unified-alerts.md). Aby uzyskać więcej informacji na temat sposobu tworzenia tych zobacz [w tym artykule](monitoring-activity-log-alerts-new-experience.md).

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [alerty](monitoring-overview-alerts.md).
- Dowiedz się, jak dodać [grupy akcji przy użyciu szablonu usługi Resource Manager](monitoring-create-action-group-with-resource-manager-template.md).
- Dowiedz się, jak [Utwórz alert dziennika działania, aby monitorować wszystkie operacje aparat skalowania automatycznego na subskrypcję](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert).
- Dowiedz się, jak [Utwórz alert dziennika działania, aby monitorować wszystkie operacje w/skali skalowalnych w poziomie skalowania automatycznego nie powiodło się w ramach subskrypcji](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).
