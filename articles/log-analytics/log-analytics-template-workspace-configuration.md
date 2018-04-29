---
title: Za pomocą szablonów usługi Azure Resource Manager do tworzenia i konfigurowania obszaru roboczego analizy dzienników | Dokumentacja firmy Microsoft
description: Szablony usługi Azure Resource Manager umożliwia tworzenie i konfigurowanie analizy dzienników obszarów roboczych.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''
ms.assetid: d21ca1b0-847d-4716-bb30-2a8c02a606aa
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: json
ms.topic: article
ms.date: 04/25/2018
ms.author: richrund
ms.openlocfilehash: 297f15430c64e5de3c10e6f38855664a50d11a8d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="manage-log-analytics-using-azure-resource-manager-templates"></a>Zarządzanie za pomocą szablonów usługi Azure Resource Manager analizy dzienników
Można użyć [szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) do tworzenia i konfigurowania analizy dzienników obszarów roboczych. Zadania, które można wykonywać za pomocą szablonów należą:

* Tworzenie obszaru roboczego, włączając ustawienie warstwy cenowej 
* Dodawanie rozwiązania
* Tworzenie zapisanych wyszukiwań
* Tworzenie grupy komputerów
* Włącz zbieranie dzienników usług IIS z komputerów z zainstalowanym agentem systemu Windows
* Zebrać liczników wydajności z komputerów z systemami Linux i Windows
* Zbierać zdarzenia z dziennika systemowego na komputerach z systemem Linux 
* Zbieranie zdarzeń z dzienników zdarzeń systemu Windows
* Dodaj agenta analizy dziennika do maszyny wirtualnej platformy Azure
* Konfigurowanie analizy dzienników do indeksowania danych zbieranych za pomocą diagnostyki Azure

W tym artykule przedstawiono przykłady szablonów, które zilustrować niektóre konfiguracji, które można wykonywać za pomocą szablonów.

## <a name="create-a-log-analytics-workspace"></a>Tworzenie obszaru roboczego analizy dzienników
W poniższym przykładzie jest tworzona przy użyciu szablonu z komputera lokalnego obszaru roboczego. Szablon JSON skonfigurowano tylko wyświetlenie monitu o nazwę obszaru roboczego i określenie wartości domyślnej dla innych parametrów, które prawdopodobnie będzie służyć jako standardowej konfiguracji w danym środowisku.  

Następujące parametry ustaw wartość domyślną:

* Lokalizacja — wartość domyślna to wschodnie stany USA
* Jednostka SKU - domyślnie nowe warstwy cenowej na GB wydane w ramach 2018 kwietnia model cenowy

>[!WARNING]
>W przypadku tworzenia lub konfigurowania obszaru roboczego analizy dzienników w ramach subskrypcji, który dokonał do nowego 2018 kwietnia model cenowy, jest jedyną prawidłową analizy dzienników warstwy cenowej **PerGB2018**. 
>

### <a name="create-and-deploy-template"></a>Tworzenie i wdrażanie szablonu

1. Skopiuj i wklej następującą składnię JSON do pliku:

    ```json
    {
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          },
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```
2. Edytuj szablon zgodnie z wymaganiami.  Przegląd [szablonu Microsoft.OperationalInsights/workspaces](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) odwołania, aby dowiedzieć się, jakie właściwości i wartości są obsługiwane. 
3. Zapisz ten plik jako **deploylaworkspacetemplate.json** do folderu lokalnego.
4. Wszystko jest teraz gotowe do wdrożenia tego szablonu. Możesz użyć programu PowerShell lub wiersza polecenia w celu cretae obszaru roboczego.

   * Dla środowiska PowerShell Użyj następujących poleceń z folderu zawierającego szablon:
   
        ```powershell
        New-AzureRmResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json
        ```

   * W wierszu polecenia użyj następujących poleceń z folderu zawierającego szablon:

        ```cmd
        azure config mode arm
        azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile deploylaworkspacetemplate.json
        ```

Wdrożenie może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat o podobne do poniższych opcji obejmujących wynik:<br><br> ![Przykład wynik po zakończeniu wdrożenia](./media/log-analytics-template-workspace-configuration/template-output-01.png)

## <a name="configure-a-log-analytics-workspace"></a>Konfigurowanie obszaru roboczego analizy dzienników
Poniższy przykład szablonu ilustruje sposób:

1. Dodawanie rozwiązania do obszaru roboczego
2. Tworzenie zapisanych wyszukiwań
3. Tworzenie grupy komputerów
4. Włącz zbieranie dzienników usług IIS z komputerów z zainstalowanym agentem systemu Windows
5. Zbierania z komputerów z systemem Linux liczników wydajności dysku logicznego (% użytych węzłów i; Wolne megabajty; % Używane miejsce; Transfery dyskowe/s; Odczyty dysku/s; Zapisy dysku/s)
6. Zbieraj zdarzenia dziennika systemowego z komputerów z systemem Linux
7. Zbieranie zdarzeń błędu i ostrzeżenia w dzienniku zdarzeń aplikacji z komputerów z systemem Windows
8. Zbieraj dane licznika wydajności dostępna pamięć (MB) z komputerów z systemem Windows
9. Zbieranie dzienników usług IIS i dzienniki zdarzeń systemu Windows zapisywane przez diagnostycznych platformy Azure na konto magazynu

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "workspaceName"
      }
    },
    "serviceTier": {
      "type": "string",
      "allowedValues": [
        "Free",
        "Standalone",
        "PerNode",
        "PerGB2018"
      ],
      "metadata": {
        "description": "Service Tier: Free, Standalone, PerNode, or PerGB2018"
    }
      },
    "dataRetention": {
      "type": "int",
      "defaultValue": 30,
      "minValue": 7,
      "maxValue": 730,
      "metadata": {
        "description": "Number of days of retention. Free plans can only have 7 days, Standalone and OMS plans include 30 days for free"
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "East US",
        "West Europe",
        "Southeast Asia",
        "Australia Southeast"
      ]
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
        "sku": {
          "Name": "[parameters('serviceTier')]"
        },
    "retentionInDays": "[parameters('dataRetention')]"
      },
      "resources": [
        {
          "apiVersion": "2017-03-15-preview",
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
          "apiVersion": "2015-11-01-preview",
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
    "portalUrl": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').portalUrl]"
    }
  }
}

```
### <a name="deploying-the-sample-template"></a>Wdrażanie przykładowy szablon
Aby wdrożyć przykładowy szablon:

1. Na przykład zapisać przykładowe dołączone w pliku, `azuredeploy.json` 
2. Edytuj szablon do konfiguracji, który ma
3. Użyj programu PowerShell lub wiersza polecenia do wdrożenia szablonu

#### <a name="powershell"></a>PowerShell
```powershell
New-AzureRmResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile azuredeploy.json
```

#### <a name="command-line"></a>Wiersz polecenia
```cmd
azure config mode arm
azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile azuredeploy.json
```

## <a name="example-resource-manager-templates"></a>Szablony przykład Resource Manager
Galerii szablonów Szybki Start Azure zawiera kilka szablonów dla analizy dziennika, w tym:

* [Wdrażanie maszyny wirtualnej z systemem Windows z rozszerzeniem wirtualna analizy dzienników](https://azure.microsoft.com/documentation/templates/201-oms-extension-windows-vm/)
* [Wdróż maszynę wirtualną z systemem Linux z rozszerzeniem wirtualna analizy dzienników](https://azure.microsoft.com/documentation/templates/201-oms-extension-ubuntu-vm/)
* [Monitor usługi Azure Site Recovery przy użyciu istniejący obszar roboczy analizy dzienników](https://azure.microsoft.com/documentation/templates/asr-oms-monitoring/)
* [Monitorowanie aplikacji sieci Web platformy Azure przy użyciu istniejący obszar roboczy analizy dzienników](https://azure.microsoft.com/documentation/templates/101-webappazure-oms-monitoring/)
* [Dodaj istniejące konto magazynu z usługą OMS](https://azure.microsoft.com/resources/templates/oms-existing-storage-account/)

## <a name="next-steps"></a>Kolejne kroki
* [Wdrażanie agenta systemu Windows na maszynach wirtualnych platformy Azure przy użyciu szablonu usługi Resource Manager](../virtual-machines/windows/extensions-oms.md).
* [Wdrażanie agenta systemu Linux na maszynach wirtualnych platformy Azure przy użyciu szablonu usługi Resource Manager](../virtual-machines/linux/extensions-oms.md).

