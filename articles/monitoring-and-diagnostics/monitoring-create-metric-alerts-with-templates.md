---
title: Tworzenie alertu metryki za pomocą szablonu usługi Resource Manager
description: Dowiedz się, jak tworzenie alertu metryki za pomocą szablonu usługi Resource Manager.
author: snehithm
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/26/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: be0ec31a05c0c1454e1af58b4a9c4ca959acbecf
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45577164"
---
# <a name="create-a-metric-alert-with-a-resource-manager-template"></a>Tworzenie alertu metryki za pomocą szablonu usługi Resource Manager
W tym artykule pokazano, jak za pomocą [szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) skonfigurować [nowszych alertów metryk](monitoring-near-real-time-metric-alerts.md) w usłudze Azure Monitor. Szablony usługi Resource Manager umożliwiają programowe Konfigurowanie alertów w sposób spójny i odtworzenia w środowiskach. Nowszych alertów metryk są obecnie dostępne na [tego zestawu typów zasobów](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported).

> [!IMPORTANT]
> Szablon usługi Resource Manager, określony dla alertu metryki nie będzie działać dla typu zasobu: Microsoft.OperationalInsights/workspaces; Obsługa metryki z usługi Log Analytics jest dostępna w wersji zapoznawczej. Użytkownicy zainteresowani korzystając z funkcji wersji zapoznawczej za pomocą szablonu zasobów mogą kontaktować się z [opinii alertów platformy Azure](mailto:azurealertsfeedback@microsoft.com)


Podstawowe kroki są następujące:

1. Użyj jednego z szablonów poniżej jako plik JSON, który opisuje sposób tworzenia alertu.
2. Edytuj i użyć odpowiedniego pliku parametrów jako dane JSON, aby dostosować ten alert
3. Wdrażanie przy użyciu szablonu [dowolnej metody wdrażania](../azure-resource-manager/resource-group-template-deploy.md).


## <a name="resource-manager-template-for-a-simple-metric-alert"></a>Szablon usługi Resource Manager proste alertu metryki
Aby utworzyć alert przy użyciu szablonu usługi Resource Manager, należy utworzyć zasób typu `Microsoft.Insights/metricAlerts` i wypełnij wszystkie powiązane właściwości. Poniżej przedstawiono przykładowy szablon, który tworzy regułę alertu metryki.

Zapisz poniższe dane json jako simplemetricalert.json na potrzeby ten przewodnik.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],   
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"                
                    }
                ]
            }
        }
    ]
}
```

Opis schematu i właściwości dla reguły alertu [znajduje się tutaj](https://docs.microsoft.com/rest/api/monitor/metricalerts/createorupdate).

Można ustawić wartości dla parametrów, w wierszu polecenia lub przy użyciu pliku parametrów. Przykładowy plik parametrów znajduje się poniżej. 

Zapisz poniższe dane json jako simplemetricalert.parameters.json i zmodyfikuj go zgodnie z potrzebami.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "New Metric Alert"
        },
        "alertDescription": {
            "value": "New metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourceGroup-name/providers/Microsoft.Compute/virtualMachines/replace-with-resource-name"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterThan"
        },
        "threshold": {
            "value": "80"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group"
        }
    }
}
```


Można utworzyć alertu metryki za pomocą pliku szablon i parametry przy użyciu programu PowerShell lub wiersza polecenia platformy Azure.

Korzystanie z programu Azure PowerShell

```powershell
Connect-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzureRmResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile simplemetricalert.json -TemplateParametersFile simplemetricalert.parameters.json
```


Korzystanie z interfejsu wiersza polecenia platformy Azure
```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file simplemetricalert.json \
    --parameters @simplemetricalert.parameters.json
```

> [!NOTE]
>
> Chociaż może zostać utworzony alert dotyczący metryki w innej grupie zasobów do zasobu docelowego, zaleca się przy użyciu tej samej grupie zasobów jako zasobu docelowego.

## <a name="resource-manager-template-for-a-more-advanced-metric-alert"></a>Szablon usługi Resource Manager dla bardziej zaawansowanych alertu metryki
Nowszych alertów metryk obsługuje alertów dotyczących metryk wielowymiarowych, a także obsługuje wiele kryteriów. Następujący szablon służy do tworzenia bardziej zaawansowanych alert dotyczący metryki na metryki jednowymiarowe i określić wiele kryteriów.

Zapisz poniższe dane json jako advancedmetricalert.json na potrzeby ten przewodnik.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Resource ID of the resource emitting the metric that will be used for the comparison."
            }
        },
        "criterion1":{
            "type": "object",
            "metadata": {
                "description": "Criterion includes metric name, dimension values, threshold and an operator. The alert rule fires when ALL criteria are met"
            }
        },
        "criterion2": {
            "type": "object",
            "metadata": {
                "description": "Criterion includes metric name, dimension values, threshold and an operator. The alert rule fires when ALL criteria are met"
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": { 
        "criterion1": "[array(parameters('criterion1'))]",
        "criterion2": "[array(parameters('criterion2'))]",
        "criteria": "[concat(variables('criterion1'),variables('criterion2'))]"
     },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": "[variables('criteria')]"
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"                
                    }
                ]
            }
        }
    ]
}
```

Używając powyższego szablonu wraz z plikiem parametrów podanych poniżej. 

Zapisz i zmodyfikować poniższe dane json jako advancedmetricalert.parameters.json na potrzeby ten przewodnik.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "New Multi-dimensional Metric Alert (Replace with your alert name)"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template (Replace with your alert description)"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourcegroup-name/providers/Microsoft.Storage/storageAccounts/replace-with-storage-account"
        },
        "criterion1": {
            "value": {
                    "name": "1st criterion",
                    "metricName": "Transactions",
                    "dimensions": [
                        {
                            "name":"ResponseType",
                            "operator": "Include",
                            "values": ["Success"]
                        },
                        {
                            "name":"ApiName",
                            "operator": "Include",
                            "values": ["GetBlob"]
                        }
                    ],
                    "operator": "GreaterThan",
                    "threshold": "5",
                    "timeAggregation": "Total"
                }
        },
        "criterion2": {
            "value":{
                "name": "2nd criterion",
                "metricName": "SuccessE2ELatency",
                "dimensions": [
                    {
                        "name":"ApiName",
                        "operator": "Include",
                        "values": ["GetBlob"]
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "250",
                "timeAggregation": "Average"
            }      
        },          
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/Default-ActivityLogAlerts/providers/Microsoft.Insights/actionGroups/replace-with-actiongroup-name"
        }
    }    
}
```


Można utworzyć alertu metryki za pomocą pliku szablon i parametry przy użyciu programu PowerShell lub wiersza polecenia platformy Azure z Twojego bieżącego katalogu roboczego

Korzystanie z programu Azure PowerShell
```powershell
Connect-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzureRmResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile advancedmetricalert.json -TemplateParametersFile advancedmetricalert.parameters.json
```



Korzystanie z interfejsu wiersza polecenia platformy Azure
```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file advancedmetricalert.json \
    --parameters @advancedmetricalert.parameters.json
```

>[!NOTE]
>
> Chociaż może zostać utworzony alert dotyczący metryki w innej grupie zasobów do zasobu docelowego, zaleca się przy użyciu tej samej grupie zasobów jako zasobu docelowego.

## <a name="next-steps"></a>Kolejne kroki
* Przeczytaj więcej na temat [alertów na platformie Azure](monitoring-overview-unified-alerts.md)
* Dowiedz się, jak [Tworzenie grupy akcji za pomocą szablonów usługi Resource Manager](monitoring-create-action-group-with-resource-manager-template.md)
