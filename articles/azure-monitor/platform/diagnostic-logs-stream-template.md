---
title: Automatyczne włączanie ustawień diagnostycznych przy użyciu szablonu usługi Resource Manager
description: Dowiedz się, jak używać szablonu usługi Resource Manager do tworzenia ustawień diagnostycznych, umożliwiające przesyłanie strumieniowe dzienników diagnostycznych do usługi Event Hubs lub przechowywać je w ramach konta magazynu.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 3/26/2018
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: 7edce5175a1dda66abf3316cb8f0eb33e9f64ef7
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371473"
---
# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Automatyczne włączanie ustawień diagnostycznych podczas tworzenia zasobów przy użyciu szablonu usługi Resource Manager
W tym artykule pokazano, jak można użyć [szablonu usługi Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) do konfigurowania ustawień diagnostycznych dla zasobu, podczas jego tworzenia. Dzięki temu można automatycznie rozpocząć przesyłanie strumieniowe swoje dzienniki diagnostyczne i metryki usługi Event hubs, ich archiwizowanie na koncie magazynu lub wysyłając je do obszaru roboczego usługi Log Analytics, po utworzeniu zasobu.

> [!WARNING]
> Od 1 listopada 2018 r. format danych dzienników na koncie magazynu zmieni się na JSON Lines. [W tym artykule znajdziesz opis skutków tej zmiany oraz instrukcje aktualizacji narzędzi w celu zapewnienia obsługi nowego formatu.](./../../azure-monitor/platform/diagnostic-logs-append-blobs.md) 
>
> 

Metody włączania dzienników diagnostycznych przy użyciu szablonu usługi Resource Manager, zależy od typu zasobu.

* **Non-Compute** używany przez zasoby (na przykład automatyzacji sieciowych grup zabezpieczeń, Logic Apps) [ustawień diagnostycznych opisanych w tym artykule](../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings).
* **Obliczenia** używany przez zasoby (WAD/LAD oparte) [WAD/LAD pliku konfiguracji opisanych w tym artykule](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

W tym artykule opisano sposób konfigurowania diagnostyki za pomocą jednej z metod.

Podstawowe kroki są następujące:

1. Utwórz szablon jako plik JSON, który zawiera opis sposobu tworzenia zasobu i Włącz diagnostykę.
2. [Wdrażanie szablonu przy użyciu dowolnej metody wdrażania](../../azure-resource-manager/resource-group-template-deploy.md).

Poniżej podajemy przykładowy plik JSON szablonu, czego potrzebujesz do generowania dla innych wystąpień obliczeniowych i zasobów obliczeniowych.

## <a name="non-compute-resource-template"></a>Zasób obliczeniowy inne niż szablonu
W przypadku zasobów-Compute należy wykonać dwie czynności:

1. Dodawanie parametrów do obiektu blob parametry dla nazwy konta magazynu, identyfikator reguły autoryzacji Centrum zdarzeń i/lub identyfikator obszaru roboczego usługi Log Analytics (Włączanie archiwizowanie dzienników diagnostycznych na koncie magazynu, przesyłanie strumieniowe dzienników do usługi Event Hubs i/lub wysyłania dzienników do usługi Azure Monitor).
   
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
        "name": "[concat('Microsoft.Insights/', parameters('settingName'))]",
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

Następujące właściwości obiektu blob na potrzeby ustawienia diagnostyczne [formacie opisanym w tym artykule](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate). Dodawanie `metrics` właściwość umożliwia również wysyłanie metryk zasobów do tych tych samych danych wyjściowych, pod warunkiem, że [zasób obsługuje metryk usługi Azure Monitor](../../azure-monitor/platform/metrics-supported.md).

Oto pełny przykład, który służy do tworzenia aplikacji logiki i włącza funkcję przesyłania strumieniowego do usługi Event Hubs i magazynu na koncie magazynu.

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

## <a name="compute-resource-template"></a>Szablon zasobów obliczeniowych
Aby włączyć diagnostykę na zasób obliczeniowy, na przykład klaster maszyny wirtualnej lub usługi Service Fabric, musisz:

1. Dodaj rozszerzenie diagnostyki platformy Azure w definicji zasobu maszyny Wirtualnej.
2. Określ Centrum konta i/lub zdarzenia magazynu jako parametr.
3. Dodaj zawartość pliku WADCfg XML do właściwości XMLCfg prawidłowo anulowania zapewnianego element wszystkie znaki XML.

> [!WARNING]
> Ten ostatni krok może być trudne uzyskać odpowiednie. [Ten artykuł](../../virtual-machines/extensions/diagnostics-template.md#diagnostics-configuration-variables) przykład dzielącą schemat konfiguracji diagnostyki na zmienne, które są poprzedzone znakiem zmiany znaczenia i poprawnie sformatowany.
> 
> 

Opisano cały proces, w tym przykłady, [w tym dokumencie](../../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Kolejne kroki
* [Dowiedz się więcej o dziennikach diagnostycznych platformy Azure](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [Stream dzienniki diagnostyczne platformy Azure do usługi Event Hubs](../../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md)


