---
title: Wyślij metryki systemu operacyjnego gościa, aby metryki usługi Azure Monitor przechowywania, przy użyciu szablonu usługi Resource Manager dla maszyny wirtualnej Windows
description: Wyślij metryki systemu operacyjnego gościa, aby metryki usługi Azure Monitor przechowywania, przy użyciu szablonu usługi Resource Manager dla maszyny wirtualnej Windows
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: a0a9af2098c4b45b8988e190a3984724cfce46ac
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67143687"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Wyślij metryki systemu operacyjnego gościa, aby metryki usługi Azure Monitor przechowywania, przy użyciu szablonu usługi Resource Manager dla maszyny wirtualnej Windows

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Za pomocą usługi Azure Monitor [rozszerzenie diagnostyki](diagnostics-extension-overview.md), można zbierać metryki i dzienniki z systemu operacyjnego gościa (systemu operacyjnego gościa), który działa jako część maszyn wirtualnych, usługi w chmurze lub klaster usługi Service Fabric. Rozszerzenie może wysyłać telemetrię do [wielu różnych lokalizacjach.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

W tym artykule opisano proces wysyłania metryki wydajności systemu operacyjnego gościa na maszynie wirtualnej Windows do magazynu danych usługi Azure Monitor. Począwszy od wersji 1.11 diagnostyki, można napisać metryki bezpośrednio do usługi Azure Monitor przechowywać metryki, którym już zbieranymi metrykami standardowa platforma.

Przechowywanie ich w tej lokalizacji umożliwia dostęp do tych samych czynności w przypadku metryk platformy. Akcje obejmują niemal w czasie rzeczywistym alertów, wykresy, routing i dostęp z interfejsu API REST i inne. W przeszłości rozszerzenie diagnostyki zapisano do usługi Azure Storage, ale nie do magazynu danych usługi Azure Monitor.

Jeśli dopiero zaczynasz pracę z szablonami usługi Resource Manager, Dowiedz się więcej o [wdrożeń szablonu](../../azure-resource-manager/resource-group-overview.md) oraz struktury i składni.

## <a name="prerequisites"></a>Wymagania wstępne

- Twoja subskrypcja musi być zarejestrowana w [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services).

- Musisz mieć [programu Azure PowerShell](/powershell/azure) lub [usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) zainstalowane.


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Konfigurowanie usługi Azure Monitor jako ujście danych
Rozszerzenie diagnostyki platformy Azure używa funkcji o nazwie "danych wychwytywanie" trasy metryk i dzienników do innej lokalizacji. Poniższe kroki pokazują sposób używania szablonu usługi Resource Manager i programu PowerShell, aby wdrożyć Maszynę wirtualną przy użyciu nowego ujście danych "Azure Monitor".

## <a name="author-resource-manager-template"></a>Szablon usługi Resource Manager dla autora
Na przykład można użyć publicznie dostępnych przykładowy szablon. Począwszy od szablony są pod https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows.

- **Azuredeploy.JSON** jest wstępnie skonfigurowany szablon usługi Resource Manager dla wdrożenia maszyny wirtualnej.

- **Azuredeploy.parameters.JSON** jest plik parametrów, która przechowuje informacje, takie jak jakie nazwy użytkownika i hasła chcesz ustawić dla maszyny Wirtualnej. Podczas wdrażania szablonu usługi Resource Manager używa parametrów, które są ustawione w tym pliku.

Pobierz i Zapisz oba pliki lokalnie.

### <a name="modify-azuredeployparametersjson"></a>Modify azuredeploy.parameters.json
Otwórz *azuredeploy.parameters.json* pliku

1. Wprowadź wartości w polach **adminUsername** i **adminPassword** dla maszyny Wirtualnej. Te parametry są używane dla dostępu zdalnego z maszyną wirtualną. Aby uniknąć przejęty maszyny Wirtualnej, nie należy użyć wartości w tym szablonie. Boty skanowania z Internetem, aby nazwy użytkownika i hasła w publicznych repozytoriach GitHub. Mogą one mieć testowania maszyn wirtualnych przy użyciu tych ustawień domyślnych.

1. Utwórz unikatową dnsname dla maszyny Wirtualnej.

### <a name="modify-azuredeployjson"></a>Modify azuredeploy.json

Otwórz *azuredeploy.json* pliku

Identyfikator konta magazynu, aby dodać **zmienne** części szablonu po wejściu do **storageAccountName.**

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

Dodaj to rozszerzenie tożsamości usługi zarządzanej (MSI) do szablonu w górnej części **zasobów** sekcji. Rozszerzenie zapewnia, że usługa Azure Monitor akceptuje metryki, które są emitowane.

```json
//Find this code.
"resources": [
// Add this code directly below.
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'), '/', 'WADExtensionSetup')]",
        "apiVersion": "2017-12-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    },
```

Dodaj **tożsamości** konfiguracji do zasobu maszyny Wirtualnej, aby upewnić się, że platforma Azure przypisuje tożsamość systemu z rozszerzeniem MSI. Ten krok zapewnia, że maszyna wirtualna może emitować metryki gościa o sobie samym do usługi Azure Monitor.

```json
// Find this section
                "subnet": {
            "id": "[variables('subnetRef')]"
            }
        }
        }
    ]
    }
},
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    // add these 3 lines below
    "identity": {
    "type": "SystemAssigned"
    },
    //end of added lines
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
    "hardwareProfile": {
    ...
```

Dodaj następującą konfigurację, aby włączyć rozszerzenie diagnostyki na maszynie wirtualnej Windows. Dla prostych opartych na usłudze Resource Manager maszyny wirtualnej możemy dodać konfiguracji rozszerzenia do tablicy zasobów dla maszyny wirtualnej. Wiersz "sink"&mdash; "AzMonSink" i odpowiednie "SinksConfig" w dalszej części sekcji&mdash;włączyć rozszerzenie do emitowania metryki bezpośrednio do usługi Azure Monitor. Możesz dodać lub usunąć liczniki wydajności, zgodnie z potrzebami.


```json
        "networkProfile": {
            "networkInterfaces": [
            {
                "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
            ]
        },
"diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob]"
    }
}
},
//Start of section to add
"resources": [
{
            "type": "extensions",
            "name": "Microsoft.Insights.VMDiagnosticsSettings",
            "apiVersion": "2017-12-01",
            "location": "[resourceGroup().location]",
            "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.12",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "WadCfg": {
                "DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
        },
                    "Directories": {
                    "scheduledTransferPeriod": "PT1M",
    "IISLogs": {
                        "containerName": "wad-iis-logfiles"
                    },
                    "FailedRequestLogs": {
                        "containerName": "wad-failedrequestlogs"
                    }
                    },
                    "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "sinks": "AzMonSink",
                    "PerformanceCounterConfiguration": [
                        {
                        "counterSpecifier": "\\Memory\\Available Bytes",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\Committed Bytes",
                        "sampleRate": "PT15S"
                        }
                    ]
                    },
                    "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                        {
                        "name": "Application!*"
                        }
                    ]
                    },
                    "Logs": {
                    "scheduledTransferPeriod": "PT1M",
                    "scheduledTransferLogLevelFilter": "Error"
                    }
                },
                "SinksConfig": {
                    "Sink": [
                    {
                        "name" : "AzMonSink",
                        "AzureMonitor" : {}
                    }
                    ]
                }
                },
                "StorageAccount": "[variables('storageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[variables('storageAccountName')]",
                "storageAccountKey": "[listKeys(variables('accountid'),'2015-06-15').key1]",
                "storageAccountEndPoint": "https://core.windows.net/"
            }
            }
        }
        ]
//End of section to add
```


Zapisz i zamknij oba pliki.


## <a name="deploy-the-resource-manager-template"></a>Wdrażanie szablonu usługi Resource Manager

> [!NOTE]
> Musi być uruchomiona wersja rozszerzenia diagnostyki platformy Azure w wersji 1.5 lub nowszej i mieć **autoUpgradeMinorVersion**: właściwości ustawiono wartość "true" w szablonie usługi Resource Manager. Po uruchomieniu maszyny Wirtualnej platformy Azure następnie ładuje właściwe rozszerzenie. Jeśli nie masz tych ustawień w szablonie, należy je zmienić i ponownego wdrażania szablonu.


Aby wdrożyć szablon usługi Resource Manager, będziemy korzystać z programu Azure PowerShell.

1. Uruchom program PowerShell.
1. Zaloguj się do platformy Azure za pomocą `Login-AzAccount`.
1. Pobierz listę subskrypcji przy użyciu `Get-AzSubscription`.
1. Ustaw subskrypcję, której używasz do utworzenia/zaktualizowania maszynę wirtualną w:

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. Aby utworzyć nową grupę zasobów dla maszyny Wirtualnej, który jest wdrażany, uruchom następujące polecenie:

   ```powershell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > Pamiętaj, aby [region platformy Azure, która jest włączona na użytek metryki niestandardowe](metrics-custom-overview.md).

1. Uruchom następujące polecenia, aby wdrożyć maszynę Wirtualną przy użyciu szablonu usługi Resource Manager.
   > [!NOTE]
   > Jeśli chcesz zaktualizować istniejącą maszynę Wirtualną, wystarczy dodać *— tryb Przyrostowy* -to-end następującego polecenia.

   ```powershell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. Po pomyślnym wdrożeniu maszyny Wirtualnej należy w witrynie Azure portal, emitowanie metryk do usługi Azure Monitor.

   > [!NOTE]
   > Możesz napotkać błędy wokół wybranego vmSkuSize. Jeśli tak się stanie, wróć do pliku azuredeploy.json i zaktualizuj wartość domyślna parametru vmSkuSize. W tym przypadku zaleca się podjęcie próby "Standard_DS1_v2").

## <a name="chart-your-metrics"></a>Wykres metryk

1. Zaloguj się w witrynie Azure Portal.

2. W menu po lewej stronie wybierz **Monitor**.

3. Na stronie monitorowanie wybierz **metryki**.

   ![Strona metryki](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. Okres agregacji można zmienić **ciągu ostatnich 30 minut**.

5. W menu rozwijanym zasobów wybierz utworzoną maszynę Wirtualną. Jeśli nie zmienisz nazwy szablonu, należy go *SimpleWinVM2*.

6. Wybierz z menu rozwijanego przestrzenie nazw **azure.vm.windows.guest**

7. Z menu rozwijanego metryk wybierz **pamięci\%przydzielone bajty w użyciu**.


## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [metryki niestandardowe](metrics-custom-overview.md).

