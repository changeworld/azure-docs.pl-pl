---
title: Użyj szablonów Azure Resource Manager, aby utworzyć i skonfigurować obszar roboczy Log Analytics | Microsoft Docs
description: Za pomocą szablonów Azure Resource Manager można tworzyć i konfigurować Log Analytics obszary robocze.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/09/2020
ms.openlocfilehash: 1b084b8cbf87817a4ff12fdb56f44b740a6d6a12
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373327"
---
# <a name="manage-log-analytics-workspace-using-azure-resource-manager-templates"></a>Zarządzanie obszarem roboczym Log Analytics przy użyciu szablonów Azure Resource Manager

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Za pomocą [szablonów Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) można tworzyć i konfigurować log Analytics obszary robocze w Azure monitor. Przykłady zadań, które można wykonywać za pomocą szablonów, to m.in.:

* Tworzenie obszaru roboczego, w tym Ustawianie warstwy cenowej i rezerwacji pojemności
* Dodawanie rozwiązania
* Utwórz zapisane wyszukiwania
* Utwórz grupę komputerów
* Włącz zbieranie dzienników usług IIS na komputerach z zainstalowanym agentem Windows
* Liczniki wydajności są zbierane z komputerów z systemami Linux i Windows
* Zbieraj zdarzenia z dziennika systemu na komputerach z systemem Linux 
* Zbieranie zdarzeń z dzienników zdarzeń Windows
* Zbieranie niestandardowych dzienników z komputera z systemem Windows
* Dodaj agenta usługi log analytics na maszynie wirtualnej platformy Azure
* Skonfiguruj usługę log analytics do indeksowania danych zebranych za pomocą diagnostyki Azure

W tym artykule przedstawiono przykłady szablonów, które ilustrują część konfiguracji, którą można wykonać za pomocą szablonów.

## <a name="api-versions"></a>Wersje interfejsu API

W poniższej tabeli wymieniono wersje interfejsu API dla zasobów używanych w tym przykładzie.

| Zasób | Typ zasobu | Wersja interfejsu API |
|:---|:---|:---|
| Obszar roboczy   | obszary robocze    | 2017-03-15 — wersja zapoznawcza |
| Wyszukiwanie      | savedSearches | 2015-03-20 |
| Źródło danych | źródła danych   | 2015-11-01 — wersja zapoznawcza |
| Rozwiązanie    | rozwiązania     | 2015-11-01 — wersja zapoznawcza |

## <a name="create-a-log-analytics-workspace"></a>Tworzenie obszaru roboczego Log Analytics

Poniższy przykład tworzy obszar roboczy przy użyciu szablonu z komputera lokalnego. Szablon JSON jest skonfigurowany tak, aby wymagał tylko nazwy i lokalizacji nowego obszaru roboczego. Używa wartości określonych dla innych parametrów obszaru roboczego, takich jak [Tryb kontroli dostępu](design-logs-deployment.md#access-control-mode), warstwa cenowa, przechowywanie i poziom rezerwacji pojemności.

W przypadku rezerwacji pojemności należy określić wybraną rezerwację pojemności do pozyskiwania danych, określając `CapacityReservation` jednostki SKU i wartość w GB dla właściwości `capacityReservationLevel`. Poniższa lista zawiera szczegółowe informacje o obsługiwanych wartościach i zachowaniach podczas ich konfigurowania.

- Po ustawieniu limitu rezerwacji nie można zmienić innej jednostki SKU w ciągu 31 dni.

- Po ustawieniu wartości rezerwacji można zwiększyć ją tylko w ciągu 31 dni.

- Wartość `capacityReservationLevel` można ustawić tylko w wielokrotnośćch 100, a maksymalna wartość to 50000.

- W przypadku zwiększenia poziomu rezerwacji czasomierz zostanie zresetowany i nie będzie można go zmienić na inny 31 dni od tej aktualizacji.  

- Jeśli zmodyfikujesz każdą inną właściwość obszaru roboczego, ale zachowasz limit rezerwacji na tym samym poziomie, czasomierz nie zostanie zresetowany. 

### <a name="create-and-deploy-template"></a>Tworzenie i wdrażanie szablonu

1. Skopiuj i wklej następującą składnię JSON do pliku:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
      "pricingTier": {
        "type": "string",
        "allowedValues": [
          "pergb2018",
          "Free",
          "Standalone",
          "PerNode",
          "Standard",
          "Premium"
          ],
        "defaultValue": "pergb2018",
        "metadata": {
        "description": "Pricing tier: PerGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
        }
      },
      "location": {
        "type": "String",
        "allowedValues": [
        "australiacentral", 
        "australiaeast", 
        "australiasoutheast", 
        "brazilsouth",
        "canadacentral", 
        "centralindia", 
        "centralus", 
        "eastasia", 
        "eastus", 
        "eastus2", 
        "francecentral", 
        "japaneast", 
        "koreacentral", 
        "northcentralus", 
        "northeurope", 
        "southafricanorth", 
        "southcentralus", 
        "southeastasia", 
        "uksouth", 
        "ukwest", 
        "westcentralus", 
        "westeurope", 
        "westus", 
        "westus2" 
        ],
      "metadata": {
        "description": "Specifies the location in which to create the workspace."
        }
      }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
          "name": "[parameters('pricingTier')]"
                },
                "retentionInDays": 120,
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            }
          }
       ]
    }
    ```

> [Informacje] dla ustawień rezerwacji pojemności Użyj tych właściwości w obszarze "jednostka SKU":

>   "name": "CapacityReservation",

>   "capacityReservationLevel": 100


2. Edytuj szablon do własnych wymagań. Zapoznaj się z tematem dokumentacja [szablonu Microsoft. OperationalInsights/Workspaces](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) , aby dowiedzieć się, jakie właściwości i wartości są obsługiwane. 
3. Zapisz ten plik jako **deploylaworkspacetemplate. JSON** w folderze lokalnym.
4. Wszystko jest teraz gotowe do wdrożenia tego szablonu. Za pomocą programu PowerShell lub wiersza polecenia można utworzyć obszar roboczy, określając nazwę i lokalizację obszaru roboczego w ramach polecenia. Nazwa obszaru roboczego musi być globalnie unikatowa w ramach wszystkich subskrypcji platformy Azure.

   * W przypadku programu PowerShell Użyj następujących poleceń z folderu zawierającego szablon:
   
        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json -workspaceName <workspace-name> -location <location>
        ```

   * W wierszu polecenia Użyj następujących poleceń z folderu zawierającego szablon:

        ```cmd
        azure config mode arm
        azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile deploylaworkspacetemplate.json --workspaceName <workspace-name> --location <location>
        ```

Wdrożenie może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat podobny do poniższego, który zawiera wynik:<br><br> ![Przykład wyniku, gdy wdrożenie jest ukończone](./media/template-workspace-configuration/template-output-01.png)

## <a name="configure-a-log-analytics-workspace"></a>Konfigurowanie obszaru roboczego Log Analytics

Poniższy przykładowy szablon ilustruje sposób wykonywania następujących czynności:

1. Dodawanie rozwiązań do obszaru roboczego
2. Utwórz zapisane wyszukiwania
3. Utwórz grupę komputerów
4. Włącz zbieranie dzienników usług IIS na komputerach z zainstalowanym agentem Windows
5. Zbieranie liczników wydajności dysku logicznego z komputerów z systemem Linux (% użytych węzłów i; Wolne megabajty; Używany obszar; % Transfery dyskowe/s; Odczyty dysku/s; Zapisy dysku/s)
6. Zbieranie zdarzeń dziennika systemu z komputerów z systemem Linux
7. Zbieranie zdarzeń błędu i ostrzeżenia w dzienniku zdarzeń aplikacji z komputerów Windows
8. Zbieraj dane licznika wydajności dostępna pamięć (MB) z komputerów Windows
9. Zbieranie dzienników usług IIS i dzienników zdarzeń systemu Windows, które zostały zapisane przez diagnostykę platformy Azure na koncie magazynu
10. Zbieranie niestandardowych dzienników z komputera z systemem Windows

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Workspace name"
      }
    },
    "pricingTier": {
      "type": "string",
      "allowedValues": [
        "PerGB2018",
        "Free",
        "Standalone",
        "PerNode",
        "Standard",
        "Premium"
      ],
      "defaultValue": "pergb2018",
      "metadata": {
        "description": "Pricing tier: pergb2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
      }
    },
    "dataRetention": {
      "type": "int",
      "defaultValue": 30,
      "minValue": 7,
      "maxValue": 730,
      "metadata": {
        "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
      }
    },
    "immediatePurgeDataOn30Days": {
      "type": "bool",
      "defaultValue": "[bool('false')]",
      "metadata": {
        "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "australiacentral", 
        "australiaeast", 
        "australiasoutheast", 
        "brazilsouth",
        "canadacentral", 
        "centralindia", 
        "centralus", 
        "eastasia", 
        "eastus", 
        "eastus2", 
        "francecentral", 
        "japaneast", 
        "koreacentral", 
        "northcentralus", 
        "northeurope", 
        "southafricanorth", 
        "southcentralus", 
        "southeastasia", 
        "uksouth", 
        "ukwest", 
        "westcentralus", 
        "westeurope", 
        "westus", 
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location in which to create the workspace."
      }
    },
    "applicationDiagnosticsStorageAccountName": {
        "type": "string",
        "metadata": {
          "description": "Name of the storage account with Azure diagnostics output"
        }
    },
    "applicationDiagnosticsStorageAccountResourceGroup": {
        "type": "string",
        "metadata": {
          "description": "The resource group name containing the storage account with Azure diagnostics output"
        }
    },
    "customLogName": {
    "type": "string",
    "metadata": {
      "description": "The custom log name"
      }
     }
    },
    "variables": {
      "Updates": {
        "Name": "[Concat('Updates', '(', parameters('workspaceName'), ')')]",
        "GalleryName": "Updates"
      },
      "AntiMalware": {
        "Name": "[concat('AntiMalware', '(', parameters('workspaceName'), ')')]",
        "GalleryName": "AntiMalware"
      },
      "SQLAssessment": {
        "Name": "[Concat('SQLAssessment', '(', parameters('workspaceName'), ')')]",
        "GalleryName": "SQLAssessment"
      },
      "diagnosticsStorageAccount": "[resourceId(parameters('applicationDiagnosticsStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName'))]"
  },
  "resources": [
    {
      "apiVersion": "2017-03-15-preview",
      "type": "Microsoft.OperationalInsights/workspaces",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "properties": {
        "retentionInDays": "[parameters('dataRetention')]",
        "features": {
          "immediatePurgeDataOn30Days": "[parameters('immediatePurgeDataOn30Days')]"
        },
        "sku": {
          "name": "[parameters('pricingTier')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-03-20",
          "name": "VMSS Queries2",
          "type": "savedSearches",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "Category": "VMSS",
            "ETag": "*",
            "DisplayName": "VMSS Instance Count",
            "Query": "Event | where Source == \"ServiceFabricNodeBootstrapAgent\" | summarize AggregatedValue = count() by Computer",
            "Version": 1
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsEvent1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsEvent",
          "properties": {
            "eventLogName": "Application",
            "eventTypes": [
              {
                "eventType": "Error"
              },
              {
                "eventType": "Warning"
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsPerfCounter1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsPerformanceCounter",
          "properties": {
            "objectName": "Memory",
            "instanceName": "*",
            "intervalSeconds": 10,
            "counterName": "Available MBytes"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleIISLog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "IISLogs",
          "properties": {
            "state": "OnPremiseEnabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxSyslog",
          "properties": {
            "syslogName": "kern",
            "syslogSeverities": [
              {
                "severity": "emerg"
              },
              {
                "severity": "alert"
              },
              {
                "severity": "crit"
              },
              {
                "severity": "err"
              },
              {
                "severity": "warning"
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslogCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxSyslogCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerf1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceObject",
          "properties": {
            "performanceCounters": [
              {
                "counterName": "% Used Inodes"
              },
              {
                "counterName": "Free Megabytes"
              },
              {
                "counterName": "% Used Space"
              },
              {
                "counterName": "Disk Transfers/sec"
              },
              {
                "counterName": "Disk Reads/sec"
              },
              {
                "counterName": "Disk Writes/sec"
              }
            ],
            "objectName": "Logical Disk",
            "instanceName": "*",
            "intervalSeconds": 10
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "dataSources",
          "name": "[concat(parameters('workspaceName'), parameters('customLogName'))]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', '/', parameters('workspaceName'))]"
          ],
          "kind": "CustomLog",
          "properties": {
            "customLogName": "[parameters('customLogName')]",
            "description": "this is a description",
            "extractions": [
              {
                "extractionName": "TimeGenerated",
                "extractionProperties": {
                  "dateTimeExtraction": {
                    "regex": [
                      {
                        "matchIndex": 0,
                        "numberdGroup": null,
                        "pattern": "((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]"
                      }
                    ]
                  }
                },
                "extractionType": "DateTime"
              }
            ],
            "inputs": [
              {
                "location": {
                  "fileSystemLocations": {
                    "linuxFileTypeLogPaths": null,
                    "windowsFileTypeLogPaths": [
                      "[concat('c:\\Windows\\Logs\\',parameters('customLogName'))]"
                    ]
                  }
                },
                "recordDelimiter": {
                  "regexDelimiter": {
                    "matchIndex": 0,
                    "numberdGroup": null,
                    "pattern": "(^.*((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9].*$)"
                  }
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerfCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-03-20",
          "name": "[concat(parameters('applicationDiagnosticsStorageAccountName'),parameters('workspaceName'))]",
          "type": "storageinsightconfigs",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "containers": [ 
              "wad-iis-logfiles" 
            ],
            "tables": [
              "WADWindowsEventLogsTable"
            ],
            "storageAccount": {
              "id": "[variables('diagnosticsStorageAccount')]",
              "key": "[listKeys(variables('diagnosticsStorageAccount'),'2015-06-15').key1]"
            }
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('Updates').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('Updates').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('Updates').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('AntiMalware').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('AntiMalware').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('AntiMalware').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('AntiMalware').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('SQLAssessment').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('SQLAssessment').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('SQLAssessment').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('SQLAssessment').GalleryName)]",
            "promotionCode": ""
          }
        }
      ]
    }
  ],
  "outputs": {
    "workspaceName": {
      "type": "string",
      "value": "[parameters('workspaceName')]"
    },
    "provisioningState": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').provisioningState]"
    },
    "source": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').source]"
    },
    "customerId": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').customerId]"
    },
    "pricingTier": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').sku.name]"
    },
    "retentionInDays": {
      "type": "int",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').retentionInDays]"
    },
    "immediatePurgeDataOn30Days": {  
      "type": "bool",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').features.immediatePurgeDataOn30Days]"
    },
    "portalUrl": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').portalUrl]"
    }
  }
}
```

### <a name="deploying-the-sample-template"></a>Wdrażanie przykładowego szablonu

Aby wdrożyć przykładowy szablon:

1. Zapisz dołączony przykład w pliku, na przykład `azuredeploy.json` 
2. Edytuj szablon, aby skonfigurować żądaną konfigurację
3. Wdrażanie szablonu przy użyciu programu PowerShell lub wiersza polecenia

#### <a name="powershell"></a>Program PowerShell

```powershell
New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile azuredeploy.json
```

#### <a name="command-line"></a>Wiersz polecenia

```cmd
azure config mode arm
azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile azuredeploy.json
```

## <a name="example-resource-manager-templates"></a>Przykładowe szablony Menedżer zasobów

Galeria szablonów szybkiego startu platformy Azure zawiera kilka szablonów dla Log Analytics, w tym:

* [Wdróż maszynę wirtualną z systemem Windows przy użyciu rozszerzenia maszyny wirtualnej Log Analytics](https://azure.microsoft.com/documentation/templates/201-oms-extension-windows-vm/)
* [Wdróż maszynę wirtualną z systemem Linux przy użyciu rozszerzenia maszyny wirtualnej Log Analytics](https://azure.microsoft.com/documentation/templates/201-oms-extension-ubuntu-vm/)
* [Monitorowanie Azure Site Recovery przy użyciu istniejącego obszaru roboczego Log Analytics](https://azure.microsoft.com/documentation/templates/asr-oms-monitoring/)
* [Monitorowanie Web Apps platformy Azure przy użyciu istniejącego obszaru roboczego Log Analytics](https://azure.microsoft.com/documentation/templates/101-webappazure-oms-monitoring/)
* [Dodaj istniejące konto magazynu do Log Analytics](https://azure.microsoft.com/resources/templates/oms-existing-storage-account/)

## <a name="next-steps"></a>Następne kroki

* [Wdróż agenta systemu Windows na maszynach wirtualnych platformy Azure przy użyciu szablonu Menedżer zasobów](../../virtual-machines/extensions/oms-windows.md).

* [Wdróż agenta systemu Linux na maszynach wirtualnych platformy Azure przy użyciu szablonu Menedżer zasobów](../../virtual-machines/extensions/oms-linux.md).
