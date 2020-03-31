---
title: Tworzenie alertów metryk dla dzienników w usłudze Azure Monitor
description: Samouczek dotyczący tworzenia alertów metryk w czasie zbliżonym w czasie rzeczywistym na temat popularnych danych analizy dzienników.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/17/2018
ms.subservice: alerts
ms.openlocfilehash: 6b21f228858954292e7a3bc5561d5e86fcfaaf41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055180"
---
# <a name="create-metric-alerts-for-logs-in-azure-monitor"></a>Tworzenie alertów metryk dla dzienników w usłudze Azure Monitor

## <a name="overview"></a>Omówienie

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Usługa Azure Monitor obsługuje [typ alertu metryki,](../../azure-monitor/platform/alerts-metric-near-real-time.md) który ma korzyści w stosunku do [alertów klasycznych.](../../azure-monitor/platform/alerts-classic-portal.md) Metryki są dostępne dla [dużej listy usług platformy Azure.](../../azure-monitor/platform/metrics-supported.md) W tym artykule opisano użycie podzbioru `Microsoft.OperationalInsights/workspaces`(czyli) dla zasobu - .

Alerty metryk można używać w popularnych dziennikach usługi Log Analytics wyodrębnionych jako metryki w ramach metryk z dzienników, w tym zasobów na platformie Azure lub lokalnie. Obsługiwane rozwiązania usługi Log Analytics są wymienione poniżej:

- [Liczniki wydajności](../../azure-monitor/platform/data-sources-performance-counters.md) dla komputerów z systemem Windows & Linux
- [Rekordy pulsu dla kondycji agenta](../../azure-monitor/insights/solution-agenthealth.md)
- [Aktualizowanie](../../automation/automation-update-management.md) rekordów zarządzania
- Dzienniki [danych zdarzeń](../../azure-monitor/platform/data-sources-windows-events.md)

Istnieje wiele korzyści dla korzystania z **alertów metryk dla dzienników** za pośrednictwem [alertów dziennika opartych](../../azure-monitor/platform/alerts-log.md) na kwerendach na platformie Azure; niektóre z nich są wymienione poniżej:

- Alerty metryki oferują niemal w czasie rzeczywistym możliwości monitorowania i alerty metryki dla dzienników rozwidli dane ze źródła dziennika, aby zapewnić to samo.
- Alerty metryki są stanowe — powiadamianie tylko raz, gdy alert jest uruchamiany i raz, gdy alert zostanie rozwiązany; w przeciwieństwie do alertów dziennika, które są bezstanowe i strzelają w każdym interwale, jeśli warunek alertu jest spełniony.
- Alerty metryki dla dziennika zapewniają wiele wymiarów, dzięki czemu filtrowanie określonych wartości, takich jak komputery, typ systemu operacyjnego itp., jest prostsze; bez konieczności penning zapytania w analizie.

> [!NOTE]
> Określona metryka i/lub wymiar będą wyświetlane tylko wtedy, gdy dane dla niej istnieją w wybranym okresie. Te metryki są dostępne dla klientów z obszarami roboczymi usługi Azure Log Analytics.

## <a name="metrics-and-dimensions-supported-for-logs"></a>Metryki i wymiary obsługiwane dla dzienników

 Alerty metryki obsługują alerty dotyczące metryk korzystających z wymiarów. Za pomocą wymiarów można filtrować dane do odpowiedniego poziomu. Pełna lista metryk obsługiwanych dla dzienników z [obszarów roboczych usługi Log Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces) jest na liście; obsługiwanych rozwiązań.

> [!NOTE]
> Aby wyświetlić obsługiwane metryki wyodrębnione z obszaru roboczego usługi Log Analytics za pośrednictwem [usługi Azure Monitor — metryki,](../../azure-monitor/platform/metrics-charts.md)alert metryki dla dziennika musi zostać utworzony na tej konkretnej metryki. Wymiary wybrane w alert metryki dla dzienników — będą wyświetlane tylko do eksploracji za pośrednictwem usługi Azure Monitor — metryki.

## <a name="creating-metric-alert-for-log-analytics"></a>Tworzenie alertu metryki dla usługi Log Analytics

Dane metryki z popularnych dzienników są potokami przed przetworzeniem w usłudze Log Analytics w usłudze Azure Monitor — metryki. Dzięki temu użytkownicy mogą korzystać z możliwości platformy Metryki, a także alert metric — w tym o alerty z częstotliwością tak niskie, jak 1 minuta.
Poniżej wymieniono sposób tworzenia alertu metrycznego dla dzienników.

## <a name="prerequisites-for-metric-alert-for-logs"></a>Wymagania wstępne dotyczące alertu metryki dla dzienników

Zanim metryka dla dzienników zebranych w danych usługi Log Analytics zadziała, należy skonfigurować i udostępnić następujące dane:

1. **Aktywny obszar roboczy analizy dzienników:** musi istnieć prawidłowy i aktywny obszar roboczy usługi Log Analytics. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego analizy dzienników w witrynie Azure portal](../../azure-monitor/learn/quick-create-workspace.md).
2. **Agent jest skonfigurowany dla obszaru roboczego usługi Log Analytics:** Agent musi być skonfigurowany dla maszyn wirtualnych platformy Azure (i/lub) lokalnych maszyn wirtualnych do wysyłania danych do obszaru roboczego usługi Log Analytics używanego we wcześniejszym kroku. Aby uzyskać więcej informacji, zobacz [Log Analytics — Przegląd agenta](../../azure-monitor/platform/agents-overview.md).
3. **Zainstalowane są obsługiwane rozwiązania do analizy dzienników:** rozwiązanie usługi Log Analytics powinno być skonfigurowane i wysyłanie danych do obszaru roboczego usługi Log Analytics — obsługiwane rozwiązania to [liczniki wydajności dla systemu Windows & Linux,](../../azure-monitor/platform/data-sources-performance-counters.md) [rekordy pulsu dla kondycji agenta,](../../azure-monitor/insights/solution-agenthealth.md) [zarządzanie aktualizacjami](../../automation/automation-update-management.md)i [dane zdarzeń](../../azure-monitor/platform/data-sources-windows-events.md).
4. **Rozwiązania usługi Log Analytics skonfigurowane do wysyłania dzienników:** rozwiązanie usługi Log Analytics powinno mieć wymagane dzienniki/dane [odpowiadające metrycznym obsługiwanym dla włączonych obszarów roboczych usługi Log Analytics.](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces) Na przykład dla *% dostępnej pamięci* licznik musi być skonfigurowany w [rozwiązaniu liczniki wydajności](../../azure-monitor/platform/data-sources-performance-counters.md) pierwszy.

## <a name="configuring-metric-alert-for-logs"></a>Konfigurowanie alertu metryki dla dzienników

 Alerty metryki można tworzyć i zarządzać za pomocą witryny Azure portal, szablony menedżera zasobów, interfejs API REST, program PowerShell i interfejs wiersza polecenia platformy Azure. Ponieważ alerty metryki dla dzienników, jest wariantem alertów metryki — po zakończeniu wymagań wstępnych alert metryki dla dzienników można utworzyć dla określonego obszaru roboczego usługi Log Analytics. Wszystkie cechy i funkcje [alertów metryk](../../azure-monitor/platform/alerts-metric-near-real-time.md) będą miały zastosowanie do alertów metryk dla dzienników, jak również; w tym schemat ładunku, obowiązujące limity przydziałów i cena naliczona.

Aby uzyskać szczegółowe informacje krok po kroku i przykłady — zobacz [tworzenie alertów metryk i zarządzanie nimi.](https://aka.ms/createmetricalert) W szczególności w przypadku alertów metryk dla dzienników — postępuj zgodnie z instrukcjami dotyczącymi zarządzania alertami metryk i upewnij się, że:

- Target dla alertu metryki jest prawidłowym *obszarem roboczym usługi Log Analytics*
- Sygnał wybrany dla alertu metryki dla wybranego *obszaru roboczego usługi Log Analytics* jest typu **Metryka**
- Filtrowanie dla określonych warunków lub zasobów przy użyciu filtrów wymiarów; metryki dla dzienników są wielowymiarowe
- Podczas konfigurowania *logiki sygnału*można utworzyć pojedynczy alert obejmujący wiele wartości wymiarów (np.
- Jeśli **nie** używasz witryny Azure portal do tworzenia alertu metryki dla wybranego *obszaru roboczego usługi Log Analytics;* następnie użytkownik musi ręcznie utworzyć jawną regułę konwersji danych dziennika na metrykę przy użyciu [usługi Azure Monitor — zaplanowane reguły kwerendy](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules).

> [!NOTE]
> Podczas tworzenia alertu metryki dla obszaru roboczego usługi Log Analytics za pośrednictwem witryny Azure portal — odpowiednia reguła do konwertowania danych dziennika na metrykę za pośrednictwem [usługi Azure Monitor — zaplanowane reguły zapytań](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) są tworzone automatycznie w tle, bez konieczności interwencji użytkownika lub *akcji.* Aby uzyskać alert metryki dla tworzenia dzienników przy użyciu środków innych niż Azure portal, zobacz [Szablon zasobu dla alertów metryk dla dzienników](#resource-template-for-metric-alerts-for-logs) sekcji na przykładzie środków tworzenia scheduledQueryRule reguły konwersji opartej na metryki przed utworzeniem alertu metryki — w przeciwnym razie nie będzie żadnych danych dla alertu metryki w dziennikach utworzonych.

## <a name="resource-template-for-metric-alerts-for-logs"></a>Szablon zasobu dla alertów metryk dla dzienników

Jak wspomniano wcześniej, proces tworzenia alertów metryk z dzienników jest dwa bolące:

1. Tworzenie reguły wyodrębniania metryk z obsługiwanych dzienników przy użyciu interfejsu API scheduledQueryRule
2. Tworzenie alertu metryki dla metryki wyodrębnionych z dziennika (w kroku 1) i obszaru roboczego usługi Log Analytics jako zasobu docelowego

### <a name="metric-alerts-for-logs-with-static-threshold"></a>Alerty metryczne dla dzienników z progiem statycznym

Aby osiągnąć to samo, można użyć przykładowego szablonu usługi Azure Resource Manager poniżej — gdzie utworzenie alertu metryki progu statycznego zależy od pomyślnego utworzenia reguły wyodrębniania metryk z dzienników za pośrednictwem scheduledQueryRule.

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

Powiedzmy, że powyższy JSON jest zapisywany jako metricfromLogsAlertStatic.json - wtedy może być połączony z parametrem pliku JSON dla tworzenia opartego na szablonie zasobów. Przykładowy parametr pliku JSON znajduje się poniżej:

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

Przy założeniu, że powyższy plik parametru jest zapisywany jako metricfromLogsAlertStatic.parameters.json; następnie można utworzyć alert metryki dla dzienników przy użyciu [szablonu zasobów do tworzenia w witrynie Azure portal](../../azure-resource-manager/templates/deploy-portal.md).

Alternatywnie można użyć polecenia programu Azure Powershell poniżej, jak również:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertStatic.json TemplateParameterFile metricfromLogsAlertStatic.parameters.json
```

Możesz też użyć wdrożenia szablonu zasobu przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
az group deployment create --resource-group myRG --template-file metricfromLogsAlertStatic.json --parameters @metricfromLogsAlertStatic.parameters.json
```

### <a name="metric-alerts-for-logs-with-dynamic-thresholds"></a>Alerty metryczne dla dzienników z dynamicznymi progami

Aby osiągnąć to samo, można użyć przykładowego szablonu usługi Azure Resource Manager poniżej — gdzie utworzenie alertu metryki progów dynamicznych zależy od pomyślnego utworzenia reguły wyodrębniania metryk z dzienników za pośrednictwem scheduledQueryRule.

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

Powiedzmy, że powyższy JSON jest zapisywany jako metricfromLogsAlertDynamic.json - wtedy może być połączony z parametrem pliku JSON dla tworzenia opartego na szablonie zasobów. Przykładowy parametr pliku JSON znajduje się poniżej:

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

Przy założeniu, że powyższy plik parametru jest zapisywany jako metricfromLogsAlertDynamic.parameters.json; następnie można utworzyć alert metryki dla dzienników przy użyciu [szablonu zasobów do tworzenia w witrynie Azure portal](../../azure-resource-manager/templates/deploy-portal.md).

Alternatywnie można użyć polecenia programu Azure Powershell poniżej, jak również:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertDynamic.json TemplateParameterFile metricfromLogsAlertDynamic.parameters.json
```

Możesz też użyć wdrożenia szablonu zasobu przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
az group deployment create --resource-group myRG --template-file metricfromLogsAlertDynamic.json --parameters @metricfromLogsAlertDynamic.parameters.json
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [alertach metrycznych](alerts-metric.md).
- Dowiedz się więcej o [alertach dziennika na platformie Azure](../../azure-monitor/platform/alerts-unified-log.md).
- Dowiedz się więcej o [alertach na platformie Azure](alerts-overview.md).
