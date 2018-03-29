---
title: Automatycznie włączaj ustawień diagnostycznych przy użyciu szablonu usługi Resource Manager | Dokumentacja firmy Microsoft
description: Dowiedz się, jak szablon Menedżera zasobów do tworzenia ustawień diagnostycznych, które umożliwi strumienia dzienników diagnostycznych do usługi Event Hubs lub przechowywać je na koncie magazynu.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a8a88a8c-4a48-4df6-8f7e-d90634d39c57
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2018
ms.author: johnkem
ms.openlocfilehash: 80f427d5acb884be1752f470e2a9d9d04eee5518
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Automatycznie włączaj ustawień diagnostycznych na tworzenie zasobów przy użyciu szablonu usługi Resource Manager
W tym artykule zostanie przedstawiony sposób korzystania [szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) do konfigurowania ustawień diagnostycznych zasobu podczas jego tworzenia. Umożliwia to automatyczne uruchamianie przesyłanych strumieniowo z dzienników diagnostycznych i metryk do usługi Event Hubs, archiwizacji je na koncie magazynu lub wysyłania ich do analizy dzienników po utworzeniu zasobu.

Metoda Włączanie dzienników diagnostycznych przy użyciu szablonu usługi Resource Manager zależy od typu zasobu.

* **Inne niż obliczeń** używany przez zasoby (na przykład automatyzacji grup zabezpieczeń sieci, Logic Apps) [ustawień diagnostycznych opisane w tym artykule](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings).
* **Obliczenia bazy danych** zasobów (WAD/LAD w oparciu o), użyj [WAD/LAD pliku konfiguracji opisanych w tym artykule](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

W tym artykule możemy opisano sposób konfigurowania diagnostyki za pomocą jednej z metod.

Podstawowe kroki są następujące:

1. Tworzenie szablonu w formacie JSON, który opisuje sposób utworzenia zasobu i Włącz diagnostykę.
2. [Wdrażanie szablonu przy użyciu dowolnej metody wdrażania](../azure-resource-manager/resource-group-template-deploy.md).

Poniżej możemy podać przykład pliku JSON szablonu, który chcesz wygenerować z systemem innym niż obliczeniowych i zasobów obliczeniowych.

## <a name="non-compute-resource-template"></a>Zasób obliczeniowy bez szablonu
Dla zasobów obliczeniowych nie należy wykonać dwie czynności:

1. Dodawanie parametrów do obiektu blob parametry dla nazwy konta magazynu, identyfikator reguły autoryzacji Centrum zdarzeń i/lub identyfikator obszaru roboczego analizy dzienników OMS (Włączanie archiwizacji dzienników diagnostycznych na konto magazynu, przesyłania strumieniowego dzienników do usługi Event Hubs i/lub wysyłania dzienników do dziennika Analytics).
   
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
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
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
        "description": "Azure Resource ID of the Log Analytics workspace for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. W tablicy zasobów zasobu, dla którego chcesz włączyć dzienniki diagnostyczne, Dodaj zasób typu `[resource namespace]/providers/diagnosticSettings`.
   
    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "Microsoft.Insights/[parameters('settingName')]",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
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

Właściwości obiektu blob dla ustawienie diagnostyczne następuje [opisany w tym artykule](https://docs.microsoft.com/rest/api/monitor/ServiceDiagnosticSettings/CreateOrUpdate). Dodawanie `metrics` właściwości umożliwi to również wysłać metryki zasobów do tych samym dane wyjściowe, pod warunkiem, że [zasób obsługuje metryki Azure Monitor](monitoring-supported-metrics.md).

W tym miejscu jest pełny przykład, która tworzy aplikację logiki i włącza opcję przesyłania strumieniowego centra zdarzeń i magazynu w ramach konta magazynu.

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
      "defaultValue": "http://azure.microsoft.com/en-us/status/feed/"
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
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
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
          "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
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
          "name": "Microsoft.Insights/[parameters('settingName')]",
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

## <a name="compute-resource-template"></a>Obliczenia bazy danych zasobów szablonu
Aby włączyć diagnostykę na zasobów obliczeniowych, na przykład klaster maszyny wirtualnej lub usługi Service Fabric należy:

1. Dodaj rozszerzenie diagnostyki Azure w definicji zasobu maszyny Wirtualnej.
2. Określ konta i/lub zdarzenia koncentratora magazynu jako parametr.
3. Dodaj zawartość pliku WADCfg XML do właściwości XMLCfg, prawidłowo anulowanie wszystkich znaków XML.

> [!WARNING]
> Ten ostatni krok mogą być nieco kłopotliwe prawo. [Znajduje się w artykule](../virtual-machines/windows/extensions-diagnostics-template.md#diagnostics-configuration-variables) przykład dzielącą schemat konfiguracji diagnostyki na zmiennych, które są anulowane i prawidłowo sformatowane.
> 
> 

Cały proces, w tym przykłady, jest opisany [w tym dokumencie](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Następne kroki
* [Więcej informacji na temat dzienników diagnostycznych platformy Azure](monitoring-overview-of-diagnostic-logs.md)
* [Strumienia Azure dzienników diagnostycznych do usługi Event Hubs](monitoring-stream-diagnostic-logs-to-event-hubs.md)

