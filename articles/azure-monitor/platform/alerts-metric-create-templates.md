---
title: Tworzenie alertu metryki za pomocą szablonu usługi Resource Manager
description: Dowiedz się, jak tworzenie alertu metryki za pomocą szablonu usługi Resource Manager.
author: snehithm
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 9/27/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 8300baa1afa3e6c738cee4c2a2517463c2af1e88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65978849"
---
# <a name="create-a-metric-alert-with-a-resource-manager-template"></a>Tworzenie alertu metryki za pomocą szablonu usługi Resource Manager

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W tym artykule pokazano, jak za pomocą [szablonu usługi Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) skonfigurować [nowszych alertów metryk](../../azure-monitor/platform/alerts-metric-near-real-time.md) w usłudze Azure Monitor. Szablony usługi Resource Manager umożliwiają programowe Konfigurowanie alertów w sposób spójny i odtworzenia w środowiskach. Nowszych alertów metryk są obecnie dostępne na [tego zestawu typów zasobów](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).

> [!IMPORTANT]
> Zasobu szablon umożliwiający tworzenie alertów dotyczących metryk dla typu zasobu: Usługa Azure obszar roboczy usługi Log Analytics (tj.) `Microsoft.OperationalInsights/workspaces`, wymagane jest wykonanie dodatkowych kroków. Aby uzyskać więcej informacji, zobacz artykuł na [metryki alertów dzienników — szablon zasobu](../../azure-monitor/platform/alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs).

Podstawowe kroki są następujące:

1. Użyj jednego z szablonów poniżej jako plik JSON, który opisuje sposób tworzenia alertu.
2. Edytuj i użyć odpowiedniego pliku parametrów jako dane JSON, aby dostosować ten alert
3. Wdrażanie przy użyciu szablonu [dowolnej metody wdrażania](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="template-for-a-simple-static-threshold-metric-alert"></a>Szablon prosty statyczny próg alertu metryki

Aby utworzyć alert przy użyciu szablonu usługi Resource Manager, należy utworzyć zasób typu `Microsoft.Insights/metricAlerts` i wypełnij wszystkie powiązane właściwości. Poniżej przedstawiono przykładowy szablon, który tworzy regułę alertu metryki.

Zapisz poniższe dane json jako simplestaticmetricalert.json na potrzeby tego przewodnika.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
                "Total",
                "Count"
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

Zapisz poniższe dane json jako simplestaticmetricalert.parameters.json i zmodyfikuj go zgodnie z potrzebami.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile simplestaticmetricalert.json -TemplateParameterFile simplestaticmetricalert.parameters.json
```


Korzystanie z interfejsu wiersza polecenia platformy Azure
```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file simplestaticmetricalert.json \
    --parameters @simplestaticmetricalert.parameters.json
```

> [!NOTE]
>
> Chociaż może zostać utworzony alert dotyczący metryki w innej grupie zasobów do zasobu docelowego, zaleca się przy użyciu tej samej grupie zasobów jako zasobu docelowego.

## <a name="template-for-a-simple-dynamic-thresholds-metric-alert"></a>Szablon dla prostych dynamicznymi progami alertu dotyczącego metryki

Aby utworzyć alert przy użyciu szablonu usługi Resource Manager, należy utworzyć zasób typu `Microsoft.Insights/metricAlerts` i wypełnij wszystkie powiązane właściwości. Poniżej przedstawiono przykładowy szablon, który tworzy regułę alertu metryki.

Zapisz poniższe dane json jako simpledynamicmetricalert.json na potrzeby tego przewodnika.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
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
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
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

Zapisz poniższe dane json jako simpledynamicmetricalert.parameters.json i zmodyfikuj go zgodnie z potrzebami.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "New Metric Alert with Dynamic Thresholds"
        },
        "alertDescription": {
            "value": "New metric alert with Dynamic Thresholds created via template"
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
          "value": "GreaterOrLessThan"
        },
        "alertSensitivity": {
            "value": "Medium"
        },
        "numberOfEvaluationPeriods": {
            "value": "4"
        },
        "minFailingPeriodsToAlert": {
            "value": "3"
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
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile simpledynamicmetricalert.json -TemplateParameterFile simpledynamicmetricalert.parameters.json
```


Korzystanie z interfejsu wiersza polecenia platformy Azure
```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file simpledynamicmetricalert.json \
    --parameters @simpledynamicmetricalert.parameters.json
```

> [!NOTE]
>
> Chociaż może zostać utworzony alert dotyczący metryki w innej grupie zasobów do zasobu docelowego, zaleca się przy użyciu tej samej grupie zasobów jako zasobu docelowego.

## <a name="template-for-a-more-advanced-static-threshold-metric-alert"></a>Szablonu dla bardziej zaawansowanych statyczny próg alertu dotyczącego metryki

Nowszych alertów metryk obsługuje alertów dotyczących metryk wielowymiarowych, a także obsługuje wiele kryteriów. Następujący szablon służy do tworzenia bardziej zaawansowanych alert dotyczący metryki na metryki jednowymiarowe i określić wiele kryteriów.

Zapisz poniższe dane json jako advancedstaticmetricalert.json na potrzeby tego przewodnika.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Zapisz i zmodyfikować poniższe dane json jako advancedstaticmetricalert.parameters.json na potrzeby tego przewodnika.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-actiongroup-name"
        }
    }
}
```


Można utworzyć alertu metryki za pomocą pliku szablon i parametry przy użyciu programu PowerShell lub wiersza polecenia platformy Azure z Twojego bieżącego katalogu roboczego

Korzystanie z programu Azure PowerShell
```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile advancedstaticmetricalert.json -TemplateParameterFile advancedstaticmetricalert.parameters.json
```



Korzystanie z interfejsu wiersza polecenia platformy Azure
```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file advancedstaticmetricalert.json \
    --parameters @advancedstaticmetricalert.parameters.json
```

>[!NOTE]
>
> Chociaż może zostać utworzony alert dotyczący metryki w innej grupie zasobów do zasobu docelowego, zaleca się przy użyciu tej samej grupie zasobów jako zasobu docelowego.

## <a name="template-for-a-more-advanced-dynamic-thresholds-metric-alert"></a>Szablonu dla bardziej zaawansowanych dynamicznymi progami alertu dotyczącego metryki

Następujący szablon służy do tworzenia bardziej zaawansowanych dynamicznymi progami alertu dotyczącego metryki na metryki jednowymiarowe. Wiele kryteriów nie są obecnie obsługiwane.

Dynamicznymi progami, który może utworzyć regułę alertów dostosować progi setek metryki serii (nawet różne typy) w czasie, co skutkuje mniejszej liczby reguł alertów do zarządzania.

Zapisz poniższe dane json jako advanceddynamicmetricalert.json na potrzeby tego przewodnika.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
        "criterion":{
            "type": "object",
            "metadata": {
                "description": "Criterion includes metric name, dimension values, threshold and an operator."
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
        "criteria": "[array(parameters('criterion'))]"
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
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
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

Zapisz i zmodyfikować poniższe dane json jako advanceddynamicmetricalert.parameters.json na potrzeby tego przewodnika.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "New Multi-dimensional Metric Alert with Dynamic Thresholds (Replace with your alert name)"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert with Dynamic Thresholds created via template (Replace with your alert description)"
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
                    "criterionType": "DynamicThresholdCriterion",
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
                    "operator": "GreaterOrLessThan",
                    "alertSensitivity": "Medium",
                    "failingPeriods": {
                        "numberOfEvaluationPeriods": "4",
                        "minFailingPeriodsToAlert": "3"
                    },
                    "timeAggregation": "Total"
                }
        }
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-actiongroup-name"
        }
    }
}
```


Można utworzyć alertu metryki za pomocą pliku szablon i parametry przy użyciu programu PowerShell lub wiersza polecenia platformy Azure z Twojego bieżącego katalogu roboczego

Korzystanie z programu Azure PowerShell
```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile advanceddynamicmetricalert.json -TemplateParameterFile advanceddynamicmetricalert.parameters.json
```



Korzystanie z interfejsu wiersza polecenia platformy Azure
```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file advanceddynamicmetricalert.json \
    --parameters @advanceddynamicmetricalert.parameters.json
```

>[!NOTE]
>
> Chociaż może zostać utworzony alert dotyczący metryki w innej grupie zasobów do zasobu docelowego, zaleca się przy użyciu tej samej grupie zasobów jako zasobu docelowego.

## <a name="template-for-metric-alert-that-monitors-multiple-resources"></a>Szablon dla alertu metryki, które monitoruje wiele zasobów

Przedstawione w poprzednich sekcjach opisano przykładowe szablony usługi Azure Resource Manager w celu tworzenia alertów dotyczących metryk, które monitorują pojedynczy zasób. Usługa Azure Monitor teraz obsługuje monitorowania wielu zasobów przy użyciu jednej reguły alertów metryk. Tę funkcję wersji zapoznawczej jest obecnie dostępna wyłącznie za pośrednictwem usługi Azure Resource Manager i interfejsu API REST i jest obsługiwany tylko w przypadku maszyn wirtualnych.

Dynamiczna reguła alertów progi może również pomóc utworzyć dostosowane progi setek metryki serii (nawet różne typy) w czasie, co skutkuje mniejszej liczby reguł alertów do zarządzania.

W tej sekcji przedstawiono szablony usługi Azure Resource Manager dla trzech scenariuszy do monitorowania wielu zasobów przy użyciu jednej reguły.

- Monitorowanie wszystkich maszyn wirtualnych (w jednym regionie platformy Azure) w co najmniej jedną grupę zasobów.
- Monitorowanie wszystkich maszyn wirtualnych (w jednym regionie platformy Azure) w ramach subskrypcji
- Odnajdywanie i monitorowanie listę maszyn wirtualnych (w jednym regionie platformy Azure) w ramach subskrypcji.

### <a name="static-threshold-alert-on-all-virtual-machines-in-one-or-more-resource-groups"></a>Statyczny próg alertu na wszystkich maszynach wirtualnych w co najmniej jedną grupę zasobów

Ten szablon utworzy statyczny próg metryki reguły alertu monitorującej procentowe użycie procesora CPU dla wszystkich maszyn wirtualnych (w jednym regionie platformy Azure) w co najmniej jedną grupę zasobów.

Zapisz poniższe dane json jako all-vms-in-resource-group-static.json na potrzeby tego przewodnika.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
        "targetResourceGroup":{
            "type": "array",
            "minLength": 1,
            "metadata": {
                "description": "Full path of the resource group(s) where target resources to be monitored are in. For example - /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName"
            }
        },
        "targetResourceRegion":{
            "type": "string",
            "allowedValues": [
                "EastUS",
                "EastUS2",
                "CentralUS",
                "NorthCentralUS",
                "SouthCentralUS",
                "WestCentralUS",
                "WestUS",
                "WestUS2",
                "CanadaEast",
                "CanadaCentral",
                "BrazilSouth",
                "NorthEurope",
                "WestEurope",
                "FranceCentral",
                "FranceSouth",
                "UKWest",
                "UKSouth",
                "GermanyCentral",
                "GermanyNortheast",
                "GermanyNorth",
                "GermanyWestCentral",
                "SwitzerlandNorth",
                "SwitzerlandWest",
                "NorwayEast",
                "NorwayWest",
                "SoutheastAsia",
                "EastAsia",
                "AustraliaEast",
                "AustraliaSoutheast",
                "AustraliaCentral",
                "AustraliaCentral2",
                "ChinaEast",
                "ChinaNorth",
                "ChinaEast2",
                "ChinaNorth2",
                "CentralIndia",
                "WestIndia",
                "SouthIndia",
                "JapanEast",
                "JapanWest",
                "KoreaCentral",
                "KoreaSouth",
                "SouthAfricaWest",
                "SouthAfricaNorth",
                "UAECentral",
                "UAENorth"
            ],
            "metadata": {
                "description": "Azure region in which target resources to be monitored are in (without spaces). For example: EastUS"
            }
        },
        "targetResourceType": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Resource type of target resources to be monitored. Currently only supported resource type is Microsoft.Compute/virtualMachines"
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
                "Total",
                "Count"
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
                "scopes": "[parameters('targetResourceGroup')]",
                "targetResourceType": "[parameters('targetResourceType')]",
                "targetResourceRegion": "[parameters('targetResourceRegion')]",
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
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

Używając powyższego szablonu przy użyciu pliku parametrów poniżej.
Zapisz i zmodyfikować poniższe dane json jako all-vms-in-resource-group-static.parameters.json na potrzeby tego przewodnika.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "Multi-resource metric alert via Azure Resource Manager template"
        },
        "alertDescription": {
            "value": "New Multi-resource metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "targetResourceGroup":{
            "value": [
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name1",
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name2"
            ]
        },
        "targetResourceRegion":{
            "value": "SouthCentralUS"
        },
        "targetResourceType":{
            "value": "Microsoft.Compute/virtualMachines"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterThan"
        },
        "threshold": {
            "value": "0"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group-name"
        }
    }
}
```

Możesz utworzyć statyczny alertu metryki za pomocą pliku szablon i parametry przy użyciu programu PowerShell lub wiersza polecenia platformy Azure z Twojego bieżącego katalogu roboczego.

Korzystanie z programu Azure PowerShell

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name MultiResourceAlertDeployment -ResourceGroupName ResourceGroupWhereRuleShouldbeSaved `
  -TemplateFile all-vms-in-resource-group-static.json -TemplateParameterFile all-vms-in-resource-group-static.parameters.json
```

Korzystanie z interfejsu wiersza polecenia platformy Azure

```azurecli
az login

az group deployment create \
    --name MultiResourceAlertDeployment \
    --resource-group ResourceGroupWhereRuleShouldbeSaved \
    --template-file all-vms-in-resource-group-static.json \
    --parameters @all-vms-in-resource-group-static.parameters.json
```

### <a name="dynamic-thresholds-alert-on-all-virtual-machines-in-one-or-more-resource-groups"></a>Dynamiczne alert progi na wszystkich maszynach wirtualnych w co najmniej jedną grupę zasobów

Ten szablon utworzy dynamicznymi progami metryki reguły alertu monitorującej procentowe użycie procesora CPU dla wszystkich maszyn wirtualnych (w jednym regionie platformy Azure) w co najmniej jedną grupę zasobów.

Zapisz poniższe dane json jako all-vms-in-resource-group-dynamic.json na potrzeby tego przewodnika.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
        "targetResourceGroup":{
            "type": "array",
            "minLength": 1,
            "metadata": {
                "description": "Full path of the resource group(s) where target resources to be monitored are in. For example - /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName"
            }
        },
        "targetResourceRegion":{
            "type": "string",
            "allowedValues": [
                "EastUS",
                "EastUS2",
                "CentralUS",
                "NorthCentralUS",
                "SouthCentralUS",
                "WestCentralUS",
                "WestUS",
                "WestUS2",
                "CanadaEast",
                "CanadaCentral",
                "BrazilSouth",
                "NorthEurope",
                "WestEurope",
                "FranceCentral",
                "FranceSouth",
                "UKWest",
                "UKSouth",
                "GermanyCentral",
                "GermanyNortheast",
                "GermanyNorth",
                "GermanyWestCentral",
                "SwitzerlandNorth",
                "SwitzerlandWest",
                "NorwayEast",
                "NorwayWest",
                "SoutheastAsia",
                "EastAsia",
                "AustraliaEast",
                "AustraliaSoutheast",
                "AustraliaCentral",
                "AustraliaCentral2",
                "ChinaEast",
                "ChinaNorth",
                "ChinaEast2",
                "ChinaNorth2",
                "CentralIndia",
                "WestIndia",
                "SouthIndia",
                "JapanEast",
                "JapanWest",
                "KoreaCentral",
                "KoreaSouth",
                "SouthAfricaWest",
                "SouthAfricaNorth",
                "UAECentral",
                "UAENorth"
            ],
            "metadata": {
                "description": "Azure region in which target resources to be monitored are in (without spaces). For example: EastUS"
            }
        },
        "targetResourceType": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Resource type of target resources to be monitored. Currently only supported resource type is Microsoft.Compute/virtualMachines"
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
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
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
                "scopes": "[parameters('targetResourceGroup')]",
                "targetResourceType": "[parameters('targetResourceType')]",
                "targetResourceRegion": "[parameters('targetResourceRegion')]",
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
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

Używając powyższego szablonu przy użyciu pliku parametrów poniżej.
Zapisz i zmodyfikować poniższe dane json jako all-vms-in-resource-group-dynamic.parameters.json na potrzeby tego przewodnika.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "Multi-resource metric alert with Dynamic Thresholds via Azure Resource Manager template"
        },
        "alertDescription": {
            "value": "New Multi-resource metric alert with Dynamic Thresholds created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "targetResourceGroup":{
            "value": [
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name1",
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name2"
            ]
        },
        "targetResourceRegion":{
            "value": "SouthCentralUS"
        },
        "targetResourceType":{
            "value": "Microsoft.Compute/virtualMachines"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterOrLessThan"
        },
        "alertSensitivity": {
            "value": "Medium"
        },
        "numberOfEvaluationPeriods": {
            "value": "4"
        },
        "minFailingPeriodsToAlert": {
            "value": "3"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group-name"
        }
    }
}
```

Można utworzyć alertu metryki za pomocą pliku szablon i parametry przy użyciu programu PowerShell lub wiersza polecenia platformy Azure z Twojego bieżącego katalogu roboczego.

Korzystanie z programu Azure PowerShell

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name MultiResourceAlertDeployment -ResourceGroupName ResourceGroupWhereRuleShouldbeSaved `
  -TemplateFile all-vms-in-resource-group-dynamic.json -TemplateParameterFile all-vms-in-resource-group-dynamic.parameters.json
```

Korzystanie z interfejsu wiersza polecenia platformy Azure

```azurecli
az login

az group deployment create \
    --name MultiResourceAlertDeployment \
    --resource-group ResourceGroupWhereRuleShouldbeSaved \
    --template-file all-vms-in-resource-group-dynamic.json \
    --parameters @all-vms-in-resource-group-dynamic.parameters.json
```

### <a name="static-threshold-alert-on-all-virtual-machines-in-a-subscription"></a>Statyczny próg alertu na wszystkich maszynach wirtualnych w ramach subskrypcji

Ten szablon utworzy statyczny próg metryki reguły alertu monitorującej procentowe użycie procesora CPU dla wszystkich maszyn wirtualnych (w jednym regionie platformy Azure) w ramach subskrypcji.

Zapisz poniższe dane json jako all — maszyny wirtualne w subskrypcji — static.json na potrzeby tego przewodnika.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
        "targetSubscription":{
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Azure Resource Manager path up to subscription ID. For example - /subscriptions/00000000-0000-0000-0000-0000-00000000"
            }
        },
        "targetResourceRegion":{
            "type": "string",
            "allowedValues": [
                "EastUS",
                "EastUS2",
                "CentralUS",
                "NorthCentralUS",
                "SouthCentralUS",
                "WestCentralUS",
                "WestUS",
                "WestUS2",
                "CanadaEast",
                "CanadaCentral",
                "BrazilSouth",
                "NorthEurope",
                "WestEurope",
                "FranceCentral",
                "FranceSouth",
                "UKWest",
                "UKSouth",
                "GermanyCentral",
                "GermanyNortheast",
                "GermanyNorth",
                "GermanyWestCentral",
                "SwitzerlandNorth",
                "SwitzerlandWest",
                "NorwayEast",
                "NorwayWest",
                "SoutheastAsia",
                "EastAsia",
                "AustraliaEast",
                "AustraliaSoutheast",
                "AustraliaCentral",
                "AustraliaCentral2",
                "ChinaEast",
                "ChinaNorth",
                "ChinaEast2",
                "ChinaNorth2",
                "CentralIndia",
                "WestIndia",
                "SouthIndia",
                "JapanEast",
                "JapanWest",
                "KoreaCentral",
                "KoreaSouth",
                "SouthAfricaWest",
                "SouthAfricaNorth",
                "UAECentral",
                "UAENorth"
            ],
            "metadata": {
                "description": "Azure region in which target resources to be monitored are in (without spaces). For example: EastUS"
            }
        },
        "targetResourceType": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Resource type of target resources to be monitored. Currently only supported resource type is Microsoft.Compute/virtualMachines"
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
                "Total",
                "Count"
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
                "scopes": ["[parameters('targetSubscription')]"],
                "targetResourceType": "[parameters('targetResourceType')]",
                "targetResourceRegion": "[parameters('targetResourceRegion')]",
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
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

Używając powyższego szablonu przy użyciu pliku parametrów poniżej.
Zapisz i zmodyfikować poniższe dane json, wszystkich — maszyny wirtualne w subskrypcji — static.parameters.json na potrzeby tego przewodnika.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "Multi-resource sub level metric alert via Azure Resource Manager template"
        },
        "alertDescription": {
            "value": "New Multi-resource sub level metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "targetSubscription":{
            "value": "/subscriptions/replace-with-subscription-id"
        },
        "targetResourceRegion":{
            "value": "SouthCentralUS"
        },
        "targetResourceType":{
            "value": "Microsoft.Compute/virtualMachines"
        },        
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterThan"
        },
        "threshold": {
            "value": "0"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group-name"
        }
    }
}
```

Można utworzyć alertu metryki za pomocą pliku szablon i parametry przy użyciu programu PowerShell lub wiersza polecenia platformy Azure z Twojego bieżącego katalogu roboczego.

Korzystanie z programu Azure PowerShell

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name MultiResourceAlertDeployment -ResourceGroupName ResourceGroupWhereRuleShouldbeSaved `
  -TemplateFile all-vms-in-subscription-static.json -TemplateParameterFile all-vms-in-subscription-static.parameters.json
```

Korzystanie z interfejsu wiersza polecenia platformy Azure

```azurecli
az login

az group deployment create \
    --name MultiResourceAlertDeployment \
    --resource-group ResourceGroupWhereRuleShouldbeSaved \
    --template-file all-vms-in-subscription-static.json \
    --parameters @all-vms-in-subscription.parameters-static.json
```

### <a name="dynamic-thresholds-alert-on-all-virtual-machines-in-a-subscription"></a>Dynamiczne alert progi dla wszystkich maszyn wirtualnych w ramach subskrypcji

Ten szablon utworzy dynamicznymi progami metryki reguły alertu monitorującej procentowe użycie procesora CPU dla wszystkich maszyn wirtualnych (w jednym regionie platformy Azure) w ramach subskrypcji.

Zapisz poniższe dane json jako all — maszyny wirtualne w subskrypcji — dynamic.json na potrzeby tego przewodnika.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
        "targetSubscription":{
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Azure Resource Manager path up to subscription ID. For example - /subscriptions/00000000-0000-0000-0000-0000-00000000"
            }
        },
        "targetResourceRegion":{
            "type": "string",
            "allowedValues": [
                "EastUS",
                "EastUS2",
                "CentralUS",
                "NorthCentralUS",
                "SouthCentralUS",
                "WestCentralUS",
                "WestUS",
                "WestUS2",
                "CanadaEast",
                "CanadaCentral",
                "BrazilSouth",
                "NorthEurope",
                "WestEurope",
                "FranceCentral",
                "FranceSouth",
                "UKWest",
                "UKSouth",
                "GermanyCentral",
                "GermanyNortheast",
                "GermanyNorth",
                "GermanyWestCentral",
                "SwitzerlandNorth",
                "SwitzerlandWest",
                "NorwayEast",
                "NorwayWest",
                "SoutheastAsia",
                "EastAsia",
                "AustraliaEast",
                "AustraliaSoutheast",
                "AustraliaCentral",
                "AustraliaCentral2",
                "ChinaEast",
                "ChinaNorth",
                "ChinaEast2",
                "ChinaNorth2",
                "CentralIndia",
                "WestIndia",
                "SouthIndia",
                "JapanEast",
                "JapanWest",
                "KoreaCentral",
                "KoreaSouth",
                "SouthAfricaWest",
                "SouthAfricaNorth",
                "UAECentral",
                "UAENorth"
            ],
            "metadata": {
                "description": "Azure region in which target resources to be monitored are in (without spaces). For example: EastUS"
            }
        },
        "targetResourceType": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Resource type of target resources to be monitored. Currently only supported resource type is Microsoft.Compute/virtualMachines"
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
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
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
                "scopes": ["[parameters('targetSubscription')]"],
                "targetResourceType": "[parameters('targetResourceType')]",
                "targetResourceRegion": "[parameters('targetResourceRegion')]",
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
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

Używając powyższego szablonu przy użyciu pliku parametrów poniżej.
Zapisz i zmodyfikować poniższe dane json, wszystkich — maszyny wirtualne w subskrypcji — dynamic.parameters.json na potrzeby tego przewodnika.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "Multi-resource sub level metric alert with Dynamic Thresholds via Azure Resource Manager template"
        },
        "alertDescription": {
            "value": "New Multi-resource sub level metric alert with Dynamic Thresholds created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "targetSubscription":{
            "value": "/subscriptions/replace-with-subscription-id"
        },
        "targetResourceRegion":{
            "value": "SouthCentralUS"
        },
        "targetResourceType":{
            "value": "Microsoft.Compute/virtualMachines"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterOrLessThan"
        },
        "alertSensitivity": {
            "value": "Medium"
        },
        "numberOfEvaluationPeriods": {
            "value": "4"
        },
        "minFailingPeriodsToAlert": {
            "value": "3"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group-name"
        }
    }
}
```

Można utworzyć alertu metryki za pomocą pliku szablon i parametry przy użyciu programu PowerShell lub wiersza polecenia platformy Azure z Twojego bieżącego katalogu roboczego.

Korzystanie z programu Azure PowerShell

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name MultiResourceAlertDeployment -ResourceGroupName ResourceGroupWhereRuleShouldbeSaved `
  -TemplateFile all-vms-in-subscription-dynamic.json -TemplateParameterFile all-vms-in-subscription-dynamic.parameters.json
```

Korzystanie z interfejsu wiersza polecenia platformy Azure

```azurecli
az login

az group deployment create \
    --name MultiResourceAlertDeployment \
    --resource-group ResourceGroupWhereRuleShouldbeSaved \
    --template-file all-vms-in-subscription-dynamic.json \
    --parameters @all-vms-in-subscription-dynamic.parameter-dynamics.json
```

### <a name="static-threshold-alert-on-a-list-of-virtual-machines"></a>Statyczny próg alertu na liście maszyn wirtualnych

Ten szablon utworzy statyczny próg metryki reguły alertu monitorującej procentowe użycie procesora CPU dla listy maszyn wirtualnych (w jednym regionie platformy Azure) w ramach subskrypcji.

Zapisz poniższe dane json jako listy z vms-static.json na potrzeby tego przewodnika.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
        "targetResourceId":{
            "type": "array",
            "minLength": 1,
            "metadata": {
                "description": "array of Azure resource Ids. For example - /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroup/resource-group-name/Microsoft.compute/virtualMachines/vm-name"
            }
        },
        "targetResourceRegion":{
            "type": "string",
            "allowedValues": [
                "EastUS",
                "EastUS2",
                "CentralUS",
                "NorthCentralUS",
                "SouthCentralUS",
                "WestCentralUS",
                "WestUS",
                "WestUS2",
                "CanadaEast",
                "CanadaCentral",
                "BrazilSouth",
                "NorthEurope",
                "WestEurope",
                "FranceCentral",
                "FranceSouth",
                "UKWest",
                "UKSouth",
                "GermanyCentral",
                "GermanyNortheast",
                "GermanyNorth",
                "GermanyWestCentral",
                "SwitzerlandNorth",
                "SwitzerlandWest",
                "NorwayEast",
                "NorwayWest",
                "SoutheastAsia",
                "EastAsia",
                "AustraliaEast",
                "AustraliaSoutheast",
                "AustraliaCentral",
                "AustraliaCentral2",
                "ChinaEast",
                "ChinaNorth",
                "ChinaEast2",
                "ChinaNorth2",
                "CentralIndia",
                "WestIndia",
                "SouthIndia",
                "JapanEast",
                "JapanWest",
                "KoreaCentral",
                "KoreaSouth",
                "SouthAfricaWest",
                "SouthAfricaNorth",
                "UAECentral",
                "UAENorth"
            ],
            "metadata": {
                "description": "Azure region in which target resources to be monitored are in (without spaces). For example: EastUS"
            }
        },
        "targetResourceType": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Resource type of target resources to be monitored. Currently only supported resource type is Microsoft.Compute/virtualMachines"
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
                "Total",
                "Count"
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
                "scopes": "[parameters('targetResourceId')]",
                "targetResourceType": "[parameters('targetResourceType')]",
                "targetResourceRegion": "[parameters('targetResourceRegion')]",
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
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

Używając powyższego szablonu przy użyciu pliku parametrów poniżej.
Zapisz i zmodyfikować poniższe dane json jako listy z — maszyny wirtualne static.parameters.json na potrzeby tego przewodnika.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "Multi-resource metric alert by list via Azure Resource Manager template"
        },
        "alertDescription": {
            "value": "New Multi-resource metric alert by list created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "targetResourceId":{
            "value": [
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name1/Microsoft.Compute/virtualMachines/replace-with-vm-name1",
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name2/Microsoft.Compute/virtualMachines/replace-with-vm-name2"
            ]
        },
        "targetResourceRegion":{
            "value": "SouthCentralUS"
        },
        "targetResourceType":{
            "value": "Microsoft.Compute/virtualMachines"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterThan"
        },
        "threshold": {
            "value": "0"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group-name"
        }
    }
}
```

Można utworzyć alertu metryki za pomocą pliku szablon i parametry przy użyciu programu PowerShell lub wiersza polecenia platformy Azure z Twojego bieżącego katalogu roboczego.

Korzystanie z programu Azure PowerShell

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name MultiResourceAlertDeployment -ResourceGroupName ResourceGroupWhereRuleShouldbeSaved `
  -TemplateFile list-of-vms-static.json -TemplateParameterFile list-of-vms-static.parameters.json
```

Korzystanie z interfejsu wiersza polecenia platformy Azure

```azurecli
az login

az group deployment create \
    --name MultiResourceAlertDeployment \
    --resource-group ResourceGroupWhereRuleShouldbeSaved \
    --template-file list-of-vms-static.json \
    --parameters @list-of-vms-static.parameters.json
```

### <a name="dynamic-thresholds-alert-on-a-list-of-virtual-machines"></a>Dynamiczne alert progi dla listy maszyn wirtualnych

Ten szablon utworzy dynamicznymi progami metryki reguły alertu monitorującej procentowe użycie procesora CPU dla listy maszyn wirtualnych (w jednym regionie platformy Azure) w ramach subskrypcji.

Zapisz poniższe dane json jako listy z vms-dynamic.json na potrzeby tego przewodnika.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
        "targetResourceId":{
            "type": "array",
            "minLength": 1,
            "metadata": {
                "description": "array of Azure resource Ids. For example - /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroup/resource-group-name/Microsoft.compute/virtualMachines/vm-name"
            }
        },
        "targetResourceRegion":{
            "type": "string",
            "allowedValues": [
                "EastUS",
                "EastUS2",
                "CentralUS",
                "NorthCentralUS",
                "SouthCentralUS",
                "WestCentralUS",
                "WestUS",
                "WestUS2",
                "CanadaEast",
                "CanadaCentral",
                "BrazilSouth",
                "NorthEurope",
                "WestEurope",
                "FranceCentral",
                "FranceSouth",
                "UKWest",
                "UKSouth",
                "GermanyCentral",
                "GermanyNortheast",
                "GermanyNorth",
                "GermanyWestCentral",
                "SwitzerlandNorth",
                "SwitzerlandWest",
                "NorwayEast",
                "NorwayWest",
                "SoutheastAsia",
                "EastAsia",
                "AustraliaEast",
                "AustraliaSoutheast",
                "AustraliaCentral",
                "AustraliaCentral2",
                "ChinaEast",
                "ChinaNorth",
                "ChinaEast2",
                "ChinaNorth2",
                "CentralIndia",
                "WestIndia",
                "SouthIndia",
                "JapanEast",
                "JapanWest",
                "KoreaCentral",
                "KoreaSouth",
                "SouthAfricaWest",
                "SouthAfricaNorth",
                "UAECentral",
                "UAENorth"
            ],
            "metadata": {
                "description": "Azure region in which target resources to be monitored are in (without spaces). For example: EastUS"
            }
        },
        "targetResourceType": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Resource type of target resources to be monitored. Currently only supported resource type is Microsoft.Compute/virtualMachines"
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
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
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
                "scopes": "[parameters('targetResourceId')]",
                "targetResourceType": "[parameters('targetResourceType')]",
                "targetResourceRegion": "[parameters('targetResourceRegion')]",
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
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

Używając powyższego szablonu przy użyciu pliku parametrów poniżej.
Zapisz i zmodyfikować poniższe dane json jako listy z — maszyny wirtualne dynamic.parameters.json na potrzeby tego przewodnika.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "Multi-resource metric alert with Dynamic Thresholds by list via Azure Resource Manager template"
        },
        "alertDescription": {
            "value": "New Multi-resource metric alert with Dynamic Thresholds by list created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "targetResourceId":{
            "value": [
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name1/Microsoft.Compute/virtualMachines/replace-with-vm-name1",
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name2/Microsoft.Compute/virtualMachines/replace-with-vm-name2"
            ]
        },
        "targetResourceRegion":{
            "value": "SouthCentralUS"
        },
        "targetResourceType":{
            "value": "Microsoft.Compute/virtualMachines"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterOrLessThan"
        },
        "alertSensitivity": {
            "value": "Medium"
        },
        "numberOfEvaluationPeriods": {
            "value": "4"
        },
        "minFailingPeriodsToAlert": {
            "value": "3"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group-name"
        }
    }
}
```

Można utworzyć alertu metryki za pomocą pliku szablon i parametry przy użyciu programu PowerShell lub wiersza polecenia platformy Azure z Twojego bieżącego katalogu roboczego.

Korzystanie z programu Azure PowerShell

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name MultiResourceAlertDeployment -ResourceGroupName ResourceGroupWhereRuleShouldbeSaved `
  -TemplateFile list-of-vms-dynamic.json -TemplateParameterFile list-of-vms-dynamic.parameters.json
```

Korzystanie z interfejsu wiersza polecenia platformy Azure

```azurecli
az login

az group deployment create \
    --name MultiResourceAlertDeployment \
    --resource-group ResourceGroupWhereRuleShouldbeSaved \
    --template-file list-of-vms-dynamic.json \
    --parameters @list-of-vms-dynamic.parameters.json
```

## <a name="next-steps"></a>Kolejne kroki
* Przeczytaj więcej na temat [alertów na platformie Azure](alerts-overview.md)
* Dowiedz się, jak [Tworzenie grupy akcji za pomocą szablonów usługi Resource Manager](action-groups-create-resource-manager-template.md)
* Składnię JSON i właściwości, zobacz [Microsoft.Insights/metricAlerts](/azure/templates/microsoft.insights/metricalerts) odwołanie do szablonu.

