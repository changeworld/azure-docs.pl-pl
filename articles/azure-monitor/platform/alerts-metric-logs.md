---
title: Tworzenie alertów dotyczących metryk dla dzienników w usłudze Azure Monitor
description: Samouczek dotyczący tworzenia alertów dotyczących metryk niemal w czasie rzeczywistym w dane analizy dziennika popularne.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 1c744e0063d5c56b2ca17f2b6c6fa694ad13a26c
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64872580"
---
# <a name="create-metric-alerts-for-logs-in-azure-monitor"></a>Tworzenie alertów metryk dla dzienników w usłudze Azure Monitor

## <a name="overview"></a>Omówienie

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Platforma Azure obsługuje monitorowanie [typu alertu Metryka](../../azure-monitor/platform/alerts-metric-near-real-time.md) mającego korzyści za pośrednictwem [alertów klasycznych](../../azure-monitor/platform/alerts-classic-portal.md). Metryki są dostępne dla [obszerne listy usług systemu Azure](../../azure-monitor/platform/metrics-supported.md). W tym artykule opisano korzystanie z podzestawu (czyli) dla zasobu - `Microsoft.OperationalInsights/workspaces`.

Popularne dzienników usługi Log Analytics, wyodrębnić jako metryki jako część metryk z dzienników w tym zasobów na platformie Azure lub lokalnie, można użyć alertów dotyczących metryk. Poniżej wymieniono obsługiwane rozwiązania usługi Log Analytics:

- [Liczniki wydajności](../../azure-monitor/platform/data-sources-performance-counters.md) maszyn Windows i Linux
- [Rekordy pulsu dla kondycji agenta](../../azure-monitor/insights/solution-agenthealth.md)
- [Zarządzanie aktualizacjami](../../automation/automation-update-management.md) rekordów
- [Dane zdarzenia](../../azure-monitor/platform/data-sources-windows-events.md) dzienników

Istnieje wiele korzyści dla przy użyciu **alerty metryki dla dzienników** za pośrednictwem na podstawie zapytania [alertów dzienników](../../azure-monitor/platform/alerts-log.md) na platformie Azure; niektóre z nich są wymienione poniżej:

- Alerty metryki oferują niemal w czasie rzeczywistym monitorowania możliwości i alerty metryki dla dzienników rozwidlenia danych ze źródła dziennika, aby upewnić się, takie same.
- Alerty metryki są stanowe — tylko powiadomienia po po wyzwoleniu alertu, a po kiedy alert został rozwiązany; w przeciwieństwie do alertów dziennika, które są bezstanowe i zachować wyzwalania w odstępach co, jeśli jest spełniony warunek alertu.
- Alerty metryki dla dziennika zapewniają wiele wymiarów, umożliwiając filtrowanie do konkretnych wartości, takich jak komputery, typ systemu operacyjnego, prostsze; itp. bez konieczności penning zapytania w usłudze analytics.

> [!NOTE]
> Określone metryki i/lub wymiaru będą wyświetlane tylko jeśli dane dla niego istnieje w wybranym okresie. Te metryki są dostępne dla klientów z obszarami roboczymi w usłudze Azure Log Analytics.

## <a name="metrics-and-dimensions-supported-for-logs"></a>Metryki i wymiary obsługiwane dla dzienników

 alerty metryki obsługuje alerty dotyczące metryk, używanego przez wymiary. Wymiarów można użyć do filtrowania swoje metryki na odpowiedni poziom. Pełną listę metryk obsługiwane w przypadku dzienników z [obszarów roboczych usługi Log Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces) znajduje się na liście; w obsługiwana rozwiązania.

> [!NOTE]
> Aby wyświetlić obsługiwane metryki dla wyodrębniania z obszaru roboczego usługi Log Analytics, za pośrednictwem [Azure monitorowanie — metryki](../../azure-monitor/platform/metrics-charts.md); metryki alertów dla dziennika musi zostać utworzony dla wymienionych metryki. Wymiary wybrany w alertu metryki dla dzienników — będą wyświetlane tylko w zakresie badań za pomocą usługi Azure Monitor — metryki.

## <a name="creating-metric-alert-for-log-analytics"></a>Tworzenie alertu metryki dla usługi Log Analytics

Dane metryk z popularnych dzienników jest przekazywany w potoku przed jego przetworzeniem w usłudze Log Analytics w usłudze Azure Monitor — metryki. Dzięki temu użytkownicy mogą korzystać z możliwości platformy metryki, a także alertu metryki — w tym o alerty z częstotliwością wynoszącą nawet minutę.
Poniżej przedstawiono sposób tworzenia alertu metryki dla dzienników.

## <a name="prerequisites-for-metric-alert-for-logs"></a>Wymagania wstępne dotyczące alertu metryki dla dzienników

Metryki dla dzienników zebranych dotyczące działania danych usługi Log Analytics, że należy ustawić włączone i dostępne:

1. **Obszar roboczy usługi Analytics aktywnego dziennika**: Prawidłowego i aktywnego obszaru roboczego usługi Log Analytics musi być obecny. Aby uzyskać więcej informacji, zobacz [Utwórz obszar roboczy usługi Log Analytics w witrynie Azure portal](../../azure-monitor/learn/quick-create-workspace.md).
2. **Agent jest skonfigurowany dla obszaru roboczego usługi Log Analytics**: Agent musi być skonfigurowana dla maszyn wirtualnych platformy Azure (i/lub) lokalnych maszyn wirtualnych do wysyłania danych do obszaru roboczego analizy dzienników, używane w poprzednim kroku. Aby uzyskać więcej informacji, zobacz [Log Analytics — omówienie agenta](../../azure-monitor/platform/agents-overview.md).
3. **Zainstalowano obsługiwanych rozwiązań do analizy dzienników**: Rozwiązanie do analizy dziennika powinny być skonfigurowane i wysyłania danych do obszaru roboczego usługi Log Analytics — obsługiwane są rozwiązania [liczniki wydajności dla Windows i Linux](../../azure-monitor/platform/data-sources-performance-counters.md), [rekordy pulsu agenta kondycji](../../azure-monitor/insights/solution-agenthealth.md) , [Rozwiązanie update management](../../automation/automation-update-management.md), i [dane zdarzenia](../../azure-monitor/platform/data-sources-windows-events.md).
4. **Zaloguj się skonfigurować do wysyłania dzienników rozwiązań do analizy**: Rozwiązanie do analizy dziennika powinien mieć wymagane dzienniki/dane odpowiadające [obsługiwane w przypadku obszarów roboczych usługi Log Analytics metryki](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces) włączone. Na przykład w przypadku *% dostępnej pamięci* licznika jego muszą być skonfigurowane w [liczniki wydajności](../../azure-monitor/platform/data-sources-performance-counters.md) rozwiązania pierwszy.

## <a name="configuring-metric-alert-for-logs"></a>Konfigurowanie alertu metryki dla dzienników

 alerty metryk mogą być tworzone i zarządzane przy użyciu witryny Azure portal, szablonów usługi Resource Manager, interfejs API REST, programu PowerShell i wiersza polecenia platformy Azure. Ponieważ alerty metryki dla dzienników po wariant alertów dotyczących metryk — wymagania wstępne są wykonywane, można utworzyć alertu metryki dla dzienników dla określonego obszaru roboczego usługi Log Analytics. Wszystkie właściwości i funkcje związane z [alertów dotyczących metryk](../../azure-monitor/platform/alerts-metric-near-real-time.md) będą one dotyczyć alertów metryk, dzienników, a także, w tym schemat ładunek, limity przydziału stosowane i rozliczane ceny.

Szczegółowe informacje krok po kroku i przykłady — zobacz [creating and managing alertów dotyczących metryk i](https://aka.ms/createmetricalert). W szczególności dla alertów metryk, dzienników — postępuj zgodnie z instrukcjami dotyczącymi zarządzania alertów dotyczących metryk i upewnić się, że:

- Docelowe dla alertu metryki jest prawidłowym *obszaru roboczego usługi Log Analytics*
- Sygnał, wybrany dla alertu metryki dla wybranych *obszaru roboczego usługi Log Analytics* typu **metryki**
- Filtr dla określonych warunków lub zasobów przy użyciu filtrów wymiaru; metryki dla dzienników są wielowymiarowych
- Podczas konfigurowania *logiki sygnału*, pojedynczy alert można utworzyć na wiele wartości wymiaru (np. komputer)
- Jeśli **nie** przy użyciu witryny Azure portal do tworzenia alertu metryki dla wybranych *obszaru roboczego usługi Log Analytics*; a następnie użytkownik będzie musiał ręcznie najpierw utworzyć regułę jawnej konwersji danych dzienników na metrykę przy użyciu [Usługa azure Monitor — reguły zaplanowanego zapytania](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules).

> [!NOTE]
> Podczas tworzenia alertu metryki dla obszaru roboczego usługi Log Analytics w witrynie Azure portal — odpowiadający reguły do konwertowania danych dziennika do metryk za pośrednictwem [usługi Azure Monitor — zaplanowane reguł zapytań](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) jest tworzony automatycznie w tle,  *bez potrzeby interwencji użytkownika lub akcji*. Aby alert dotyczący metryki na potrzeby tworzenia dzienników przy użyciu środków innych niż witryna Azure portal, zobacz [szablonu zasobów dla alertów metryk, dzienników](#resource-template-for-metric-alerts-for-logs) sekcji, na przykład sposobu tworzenia dziennika ScheduledQueryRule na podstawie reguły konwersji metryki przed alertu metryki Tworzenie — jeszcze nie będzie żadnych danych dla alertu metryki na dzienniki utworzone.

## <a name="resource-template-for-metric-alerts-for-logs"></a>Zasób szablonu dla alertów dotyczących metryk dla dzienników

Jak wspomniano wcześniej, proces tworzenia alertów dotyczących metryk z dzienników jest dwa filtrowania:

1. Utwórz regułę ekstrakcji metryk z obsługiwanych dzienników przy użyciu interfejsu API scheduledQueryRule
2. Tworzenie alertu metryki dla metryki wyodrębnione z dziennika (w step1) i w obszarze roboczym usługi Log Analytics, co zasób docelowy

### <a name="metric-alerts-for-logs-with-static-threshold"></a>W dziennikach statyczny próg alertów dotyczących metryk

Aby osiągnąć takie same, jeden można korzystać z przykładu szablonu Azure Resource Manager poniżej — gdy tworzenia statyczny próg alertu metryki zależy pomyślne utworzenie reguły ekstrakcji metryk z dzienników za pośrednictwem scheduledQueryRule.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
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
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
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

Załóżmy, że powyższy kod JSON jest zapisywany jako metricfromLogsAlertStatic.json — a następnie można połączyć z plikiem JSON parametrów do utworzenia na podstawie szablonu usługi Resource. Poniżej przedstawiono przykładowy plik JSON parametru:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule" 
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterThan"
        },
        "threshold":{
            "value": "1"
        },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Zakładając, że powyższe pliku parametrów jest zapisywany jako metricfromLogsAlertStatic.parameters.json; następnie można utworzyć alertu metryki za pomocą dzienników [zasobu szablon do tworzenia w witrynie Azure portal](../../azure-resource-manager/resource-group-template-deploy-portal.md).

Jeden alternatywnie także użyć poniższego polecenia programu Azure Powershell:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertStatic.json TemplateParameterFile metricfromLogsAlertStatic.parameters.json
```

Lub użyj wdrażanie szablonu zasobów przy użyciu wiersza polecenia platformy Azure:

```CLI
az group deployment create --resource-group myRG --template-file metricfromLogsAlertStatic.json --parameters @metricfromLogsAlertStatic.parameters.json
```

### <a name="metric-alerts-for-logs-with-dynamic-thresholds"></a>Alerty metryki dla dzienników z dynamicznymi progami

Aby osiągnąć takie same, jeden można korzystać z przykładu szablonu Azure Resource Manager poniżej — gdy Tworzenie alertu metryki dynamicznymi progami zależy pomyślne utworzenie reguły ekstrakcji metryk z dzienników za pośrednictwem scheduledQueryRule.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
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
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
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

Załóżmy, że powyższy kod JSON jest zapisywany jako metricfromLogsAlertDynamic.json — a następnie można połączyć z plikiem JSON parametrów do utworzenia na podstawie szablonu usługi Resource. Poniżej przedstawiono przykładowy plik JSON parametru:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule"
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
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
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Zakładając, że powyższe pliku parametrów jest zapisywany jako metricfromLogsAlertDynamic.parameters.json; następnie można utworzyć alertu metryki za pomocą dzienników [zasobu szablon do tworzenia w witrynie Azure portal](../../azure-resource-manager/resource-group-template-deploy-portal.md).

Jeden alternatywnie także użyć poniższego polecenia programu Azure Powershell:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertDynamic.json TemplateParameterFile metricfromLogsAlertDynamic.parameters.json
```

Lub użyj wdrażanie szablonu zasobów przy użyciu wiersza polecenia platformy Azure:

```CLI
az group deployment create --resource-group myRG --template-file metricfromLogsAlertDynamic.json --parameters @metricfromLogsAlertDynamic.parameters.json
```

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [alertów dotyczących metryk](alerts-metric.md).
- Dowiedz się więcej o [alerty dzienników w usłudze Azure](../../azure-monitor/platform/alerts-unified-log.md).
- Dowiedz się więcej o [alertów na platformie Azure](alerts-overview.md).
