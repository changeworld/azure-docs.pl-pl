---
title: Tworzenie ustawień diagnostycznych na platformie Azure przy użyciu szablonu Menedżer zasobów
description: Tworzenie ustawień diagnostycznych przy użyciu szablonu Menedżer zasobów do przesyłania dalej dzienników platformy Azure do Azure Monitor dzienników, usługi Azure Storage lub platformy Azure Event Hubs.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 0cb4cee732b1784de489d97769294c455cfd5efd
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200490"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>Tworzenie ustawień diagnostycznych na platformie Azure przy użyciu szablonu Menedżer zasobów
[Dzienniki platformy](platform-logs-overview.md) na platformie Azure zapewniają szczegółowe informacje diagnostyczne i inspekcji dla zasobów platformy Azure i platformy Azure, od których zależą. Ten artykuł zawiera szczegółowe informacje na temat używania [szablonu Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) w celu skonfigurowania ustawień diagnostycznych w celu zbierania dzienników platformy do różnych miejsc docelowych. Dzięki temu można automatycznie rozpocząć zbieranie dzienników platformy podczas tworzenia zasobu.


## <a name="resource-manager-template"></a>Szablon usługi Resource Manager
Istnieją dwie sekcje szablonu Menedżer zasobów, które należy edytować w celu utworzenia ustawień diagnostycznych. Te sekcje zostały opisane w poniższych sekcjach.

### <a name="parameters"></a>Parametry
W zależności od [miejsc docelowych](diagnostic-settings.md#destinations) dla ustawienia diagnostyki Dodaj parametry do obiektu blob parametry dla nazwy konta magazynu, identyfikatora reguły autoryzacji centrum zdarzeń i log Analytics identyfikator obszaru roboczego.
   
```json
"settingName": {
  "type": "string",
  "metadata": {
    "description": "Name for the diagnostic setting resource. Eg. 'archiveToStorage' or 'forSecurityTeam'."
  }
},
"storageAccountName": {
  "type": "string",
  "metadata": {
    "description": "Name of the Storage Account in which platform logs should be saved."
  }
},
"eventHubAuthorizationRuleId": {
  "type": "string",
  "metadata": {
    "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
  }
},
"eventHubName": {
  "type": "string",
  "metadata": {
    "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
  }
},
"workspaceId":{
  "type": "string",
  "metadata": {
    "description": "Azure Resource ID of the Log Analytics workspace to which logs will be sent."
  }
}
```

### <a name="resources"></a>Zasoby
W tablicy zasobów zasobu, dla którego chcesz utworzyć ustawienie diagnostyczne, Dodaj zasób typu `[resource namespace]/providers/diagnosticSettings`. Sekcja właściwości jest zgodna z formatem opisanym w [ustawieniach diagnostycznych — Tworzenie lub aktualizowanie](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate). Dodaj właściwość `metrics`, aby zbierać metryki zasobów do tych samych miejsc docelowych, jeśli [zasób obsługuje metryki](metrics-supported.md).
   
```json
"resources": [
  {
    "type": "providers/diagnosticSettings",
    "name": "[concat('Microsoft.Insights/', parameters('settingName'))]",
    "dependsOn": [
      "[/*resource Id for which resource logs will be enabled>*/]"
    ],
    "apiVersion": "2017-05-01-preview",
    "properties": {
      "name": "[parameters('settingName')]",
      "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
      "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
      "eventHubName": "[parameters('eventHubName')]",
      "workspaceId": "[parameters('workspaceId')]",
      "logs": [ 
        {
          "category": "/* log category name */",
          "enabled": true,
          "retentionPolicy": {
            "days": 0,
            "enabled": false
          }
        }
      ],
      "metrics": [
        {
          "category": "AllMetrics",
          "enabled": true,
          "retentionPolicy": {
            "enabled": false,
            "days": 0
          }
        }
      ]
    }
  }
]
```



## <a name="example"></a>Przykład
Poniżej przedstawiono kompletny przykład tworzenia aplikacji logiki i tworzenia ustawień diagnostycznych, które umożliwiają przesyłanie strumieniowe dzienników zasobów do centrum zdarzeń i magazynu na koncie magazynu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "logicAppName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Logic App that will be created."
      }
    },
    "testUri": {
      "type": "string",
      "defaultValue": "https://azure.microsoft.com/status/feed/"
    },
    "settingName": {
      "type": "string",
      "metadata": {
        "description": "Name of the setting. Name for the diagnostic setting resource. Eg. 'archiveToStorage' or 'forSecurityTeam'."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which resource logs should be saved."
      }
    },
    "eventHubAuthorizationRuleId": {
      "type": "string",
      "metadata": {
        "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
      }
    },
    "eventHubName": {
      "type": "string",
      "metadata": {
        "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
      }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Logic/workflows",
      "name": "[parameters('logicAppName')]",
      "apiVersion": "2016-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "definition": {
          "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "[concat('Microsoft.Insights/', parameters('settingName'))]",
          "dependsOn": [
            "[resourceId('Microsoft.Logic/workflows', parameters('logicAppName'))]"
          ],
          "apiVersion": "2017-05-01-preview",
          "properties": {
            "name": "[parameters('settingName')]",
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
            "eventHubName": "[parameters('eventHubName')]",
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "WorkflowRuntime",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ],
            "metrics": [
              {
                "timeGrain": "PT1M",
                "enabled": true,
                "retentionPolicy": {
                  "enabled": false,
                  "days": 0
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```


## <a name="next-steps"></a>Następne kroki
* Przeczytaj więcej [na temat dzienników platformy na platformie Azure](platform-logs-overview.md).
* Dowiedz się więcej na temat [ustawień diagnostycznych](diagnostic-settings.md).
