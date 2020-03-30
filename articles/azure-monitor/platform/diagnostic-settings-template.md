---
title: Tworzenie ustawień diagnostycznych na platformie Azure przy użyciu szablonu Usługi Resource Manager
description: Tworzenie ustawień diagnostycznych przy użyciu szablonu Usługi Resource Manager do przesyłania dalej dzienników platformy Azure do dzienników usługi Azure Monitor, usługi Azure storage lub usługi Azure Event Hubs.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: a2569ca3f998030680bd7dbd872d71ccd372a25d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672433"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>Tworzenie ustawień diagnostycznych na platformie Azure przy użyciu szablonu Usługi Resource Manager
[Ustawienia diagnostyczne](diagnostic-settings.md) w usłudze Azure Monitor określają, gdzie należy wysyłać [dzienniki platformy,](platform-logs-overview.md) które są zbierane przez zasoby platformy Azure i platformę platformy Azure, od której zależą. Ten artykuł zawiera szczegółowe informacje i przykłady dotyczące tworzenia i konfigurowania ustawień diagnostycznych do zbierania dzienników platformy do różnych miejsc docelowych przy użyciu [szablonu usługi Azure Resource Manager.](../../azure-resource-manager/templates/template-syntax.md)

> [!NOTE]
> Ponieważ nie można [utworzyć ustawienia diagnostycznego](diagnostic-settings.md) dla dziennika aktywności platformy Azure przy użyciu programu PowerShell lub interfejsu wiersza polecenia, takich jak ustawienia diagnostyczne dla innych zasobów platformy Azure, utwórz szablon Menedżera zasobów dla dziennika aktywności przy użyciu informacji zawartych w tym artykule i wdrożyć szablon przy użyciu programu PowerShell lub interfejsu wiersza polecenia.

## <a name="deployment-methods"></a>Metody wdrażania
Szablony Menedżera zasobów można wdrażać przy użyciu dowolnej prawidłowej metody, w tym programu PowerShell i interfejsu wiersza polecenia. Ustawienia diagnostyczne dziennika aktywności muszą `az deployment create` zostać wdrożone w ramach subskrypcji przy użyciu interfejsu wiersza polecenia lub `New-AzDeployment` programu PowerShell. Ustawienia diagnostyczne dzienników zasobów muszą zostać `az group deployment create` wdrożone `New-AzResourceGroupDeployment` w grupie zasobów przy użyciu interfejsu wiersza polecenia lub programu PowerShell.

Aby uzyskać szczegółowe [informacje, zobacz Wdrażanie zasobów za pomocą szablonów usługi Resource Manager oraz zasobów programu Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md) i [wdrażanie z szablonami usługi Resource Manager i platformą Azure CLI.](../../azure-resource-manager/templates/deploy-cli.md) 





## <a name="resource-logs"></a>Dzienniki zasobów
W przypadku dzienników zasobów dodaj `<resource namespace>/providers/diagnosticSettings` do szablonu zasób typu. Sekcja właściwości jest zgodna z formatem opisanym w [sekcji Ustawienia diagnostyczne — Tworzenie lub aktualizowanie](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate). Podaj `category` `logs` w sekcji dla każdej kategorii prawidłowe dla zasobu, który chcesz zebrać. Dodaj `metrics` właściwość do zbierania metryk zasobów do tych samych miejsc docelowych, jeśli [zasób obsługuje metryki](metrics-supported.md).

Poniżej znajduje się szablon, który zbiera kategorię dziennika zasobów dla określonego zasobu do obszaru roboczego usługi Log Analytics, konta magazynu i centrum zdarzeń.

```json
"resources": [
  {
    "type": "/<resource namespace>/providers/diagnosticSettings",
    "name": "[concat(parameters('resourceName'),'/microsoft.insights/', parameters('settingName'))]",
    "dependsOn": [
      "[<resource Id for which resource logs will be enabled>]"
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
          "category": "<category name>",
          "enabled": true
        }
      ],
      "metrics": [
        {
          "category": "AllMetrics",
          "enabled": true
        }
      ]
    }
  }
]
```



### <a name="example"></a>Przykład
Oto przykład, który tworzy ustawienie diagnostyczne dla ustawienia skalowania automatycznego, który umożliwia przesyłanie strumieniowe dzienników zasobów do centrum zdarzeń, konta magazynu i obszaru roboczego usługi Log Analytics.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "autoscaleSettingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the autoscale setting"
            }
        },
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceIDl of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
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
        }
    },
    "variables": {},
    "resources": [
    {
      "type": "microsoft.insights/autoscalesettings/providers/diagnosticSettings",
      "apiVersion": "2017-05-01-preview",
      "name": "[concat(parameters('autoscaleSettingName'),'/microsoft.insights/', parameters('settingName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/autoscalesettings', parameters('autoscaleSettingName'))]"
      ],
      "properties": {
        "workspaceId": "[parameters('workspaceId')]",
        "storageAccountId": "[parameters('storageAccountId')]",
        "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
        "eventHubName": "[parameters('eventHubName')]",
        "logs": [
          {
            "category": "AutoscaleScaleActions",
            "enabled": true
          },
          {
            "category": "AutoscaleEvaluations",
            "enabled": true
          }
        ]
      }
    }
  ]
}
```

## <a name="activity-log"></a>Dziennik aktywności
W dzienniku aktywności platformy Azure `Microsoft.Insights/diagnosticSettings`dodaj zasób typu . Dostępne kategorie są wymienione w [kategoriach w dzienniku aktywności](activity-log-view.md#categories-in-the-activity-log). Poniżej znajduje się szablon, który zbiera wszystkie kategorie dziennika aktywności do obszaru roboczego usługi Log Analytics, konta magazynu i centrum zdarzeń.


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceID of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
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
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Insights/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[parameters('settingName')]",
            "location": "global",
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "logs": [
                    {
                        "category": "Administrative",
                        "enabled": true
                    },
                    {
                        "category": "Security",
                        "enabled": true
                    },
                    {
                        "category": "ServiceHealth",
                        "enabled": true
                    },
                    {
                        "category": "Alert",
                        "enabled": true
                    },
                    {
                        "category": "Recommendation",
                        "enabled": true
                    },
                    {
                        "category": "Policy",
                        "enabled": true
                    },
                    {
                        "category": "Autoscale",
                        "enabled": true
                    },
                    {
                        "category": "ResourceHealth",
                        "enabled": true
                    }
                ]
            }
        }
    ]
}
```


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [logach platformy na platformie Azure](platform-logs-overview.md).
* Dowiedz się więcej o [ustawieniach diagnostycznych](diagnostic-settings.md).
