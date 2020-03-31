---
title: Zbieranie metryk maszyn wirtualnych systemu Windows w monitorze platformy Azure za pomocą szablonu
description: Wysyłanie metryk systemu operacyjnego gościa do magazynu metryk Usługi Azure Monitor przy użyciu szablonu Menedżera zasobów dla maszyny wirtualnej systemu Windows
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: e747ca89912c36538bfb9d02986629fe57c5adcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657371"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Wysyłanie metryk systemu operacyjnego gościa do magazynu metryk Usługi Azure Monitor przy użyciu szablonu Menedżera zasobów dla maszyny wirtualnej systemu Windows

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Za pomocą [rozszerzenia diagnostyki](diagnostics-extension-overview.md)usługi Azure Monitor można zbierać metryki i dzienniki z systemu operacyjnego gościa (system operacyjny gościa), który jest uruchomiony jako część maszyny wirtualnej, usługi w chmurze lub klastra sieci szkieletowej usług. Rozszerzenie może wysyłać dane telemetryczne do [wielu różnych lokalizacji.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

W tym artykule opisano proces wysyłania metryk wydajności systemu operacyjnego gościa dla maszyny wirtualnej systemu Windows do magazynu danych usługi Azure Monitor. Począwszy od diagnostyki w wersji 1.11, można zapisywać metryki bezpośrednio w magazynie metryk usługi Azure Monitor, gdzie standardowe metryki platformy są już zbierane.

Przechowywanie ich w tej lokalizacji umożliwia dostęp do tych samych akcji dla metryk platformy. Akcje obejmują alerty w czasie zbliżonym do rzeczywistego, wykresy, routing i dostęp z interfejsu API REST i innych. W przeszłości rozszerzenie diagnostyki napisał do usługi Azure Storage, ale nie do magazynu danych usługi Azure Monitor.

Jeśli jesteś nowym użytkownikiem szablonów Menedżera zasobów, dowiedz się więcej o [wdrożeniach szablonów](../../azure-resource-manager/management/overview.md) oraz ich strukturze i składni.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja musi być zarejestrowana w [witrynie Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services).

- Musisz mieć zainstalowany [program Azure PowerShell](/powershell/azure) lub [usługa Azure Cloud Shell.](https://docs.microsoft.com/azure/cloud-shell/overview)

- Zasób maszyny Wirtualnej musi znajdować się w [regionie obsługującym metryki niestandardowe](metrics-custom-overview.md#supported-regions). 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Konfigurowanie usługi Azure Monitor jako ujścia danych
Rozszerzenie diagnostyki platformy Azure używa funkcji o nazwie "pochłaniacze danych" do kierowania metryk i dzienników do różnych lokalizacji. Poniższe kroki pokazują, jak używać szablonu Menedżera zasobów i programu PowerShell do wdrażania maszyny wirtualnej przy użyciu nowego ujścia danych "Azure Monitor".

## <a name="author-resource-manager-template"></a>Szablon Menedżera zasobów autora
W tym przykładzie można użyć publicznie dostępnego przykładowego szablonu. Początkowe szablony https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windowsznajdują się w punkcie .

- **Azuredeploy.json** jest wstępnie skonfigurowanym szablonem Menedżera zasobów do wdrażania maszyny wirtualnej.

- **Azuredeploy.parameters.json** to plik parametrów, który przechowuje informacje, takie jak nazwa użytkownika i hasło, które chcesz ustawić dla maszyny Wirtualnej. Podczas wdrażania szablon Menedżera zasobów używa parametrów ustawionych w tym pliku.

Pobierz i zapisz oba pliki lokalnie.

### <a name="modify-azuredeployparametersjson"></a>Modyfikowanie pliku azuredeploy.parameters.json
Otwórz plik *azuredeploy.parameters.json*

1. Wprowadź wartości dla **adminUsername** i **adminPassword** dla maszyny Wirtualnej. Parametry te są używane do zdalnego dostępu do maszyny Wirtualnej. Aby uniknąć porwania maszyny Wirtualnej, NIE należy używać wartości w tym szablonie. Boty skanują internet w poszukiwaniu nazw użytkowników i haseł w publicznych repozytoriach GitHub. Prawdopodobnie będą testować maszyny wirtualne z tymi wartościami domyślnymi.

1. Utwórz unikatową nazwa dns dla maszyny Wirtualnej.

### <a name="modify-azuredeployjson"></a>Modyfikowanie pliku azuredeploy.json

Otwórz plik *azuredeploy.json*

Dodaj identyfikator konta magazynu do sekcji **zmiennych** szablonu po wpisie **dla storageAccountName.**

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

Dodaj to rozszerzenie tożsamości usługi zarządzanej (MSI) do szablonu u góry sekcji **zasobów.** Rozszerzenie zapewnia, że usługa Azure Monitor akceptuje metryki, które są emitowane.

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

Dodaj konfigurację **tożsamości** do zasobu maszyny Wirtualnej, aby upewnić się, że platforma Azure przypisuje tożsamość systemu do rozszerzenia MSI. Ten krok gwarantuje, że maszyna wirtualna może emitować metryki gościa o sobie do usługi Azure Monitor.

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

Dodaj następującą konfigurację, aby włączyć rozszerzenie diagnostyki na maszynie wirtualnej systemu Windows. W przypadku prostej maszyny wirtualnej opartej na Menedżerze zasobów możemy dodać konfigurację rozszerzenia do tablicy zasobów dla maszyny wirtualnej. Wiersz "pochłania"&mdash; "AzMonSink" i odpowiadające mu "SinksConfig" w dalszej części sekcji&mdash;umożliwiają rozszerzenie do emitowania metryk bezpośrednio do usługi Azure Monitor. W razie potrzeby możesz dodać lub usunąć liczniki wydajności.


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
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'), '/', 'Microsoft.Insights.VMDiagnosticsSettings')]",
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


## <a name="deploy-the-resource-manager-template"></a>Wdrażanie szablonu Menedżera zasobów

> [!NOTE]
> Musisz być uruchomiony rozszerzenie diagnostyki platformy Azure w wersji 1.5 lub nowszej i mieć **autoUpgradeMinorVersion:** właściwość ustawiona na "true" w szablonie Menedżera zasobów. Platforma Azure następnie ładuje odpowiednie rozszerzenie po uruchomieniu maszyny Wirtualnej. Jeśli nie masz tych ustawień w szablonie, zmień je i ponownie wdrożyj szablon.


Aby wdrożyć szablon Usługi Resource Manager, korzystamy z programu Azure PowerShell.

1. Uruchom program PowerShell.
1. Zaloguj się na `Login-AzAccount`platformie Azure przy użyciu programu .
1. Pobierz listę subskrypcji za `Get-AzSubscription`pomocą programu .
1. Ustaw subskrypcję używasz do tworzenia/aktualizowania maszyny wirtualnej w:

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. Aby utworzyć nową grupę zasobów dla wdrażanych maszyn wirtualnych, uruchom następujące polecenie:

   ```powershell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > Pamiętaj, aby [użyć regionu platformy Azure, który jest włączony dla metryk niestandardowych](metrics-custom-overview.md).

1. Uruchom następujące polecenia, aby wdrożyć maszynę wirtualną przy użyciu szablonu Menedżera zasobów.
   > [!NOTE]
   > Jeśli chcesz zaktualizować istniejącą maszynę wirtualną, po prostu dodaj *-Mode Przyrostowe* na końcu następującego polecenia.

   ```powershell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. Po pomyślnym wdrożeniu maszyna wirtualna powinna znajdować się w witrynie Azure portal, emitując metryki do usługi Azure Monitor.

   > [!NOTE]
   > Możesz napotkać błędy wokół wybranego vmSkuSize. W takim przypadku wróć do pliku azuredeploy.json i zaktualizuj domyślną wartość parametru vmSkuSize. W takim przypadku zalecamy wypróbowanie "Standard_DS1_v2").

## <a name="chart-your-metrics"></a>Wykres metryki

1. Zaloguj się w witrynie Azure Portal.

2. W menu po lewej stronie wybierz pozycję **Monitor**.

3. Na stronie Monitor wybierz pozycję **Metryki**.

   ![Strona Metryki](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. Zmień okres agregacji na **Ostatnie 30 minut**.

5. W menu rozwijanym zasobu wybierz utworzoną maszynę wirtualną. Jeśli nazwa szablonu nie została zmieniona, powinna ona być *simplewinvm2*.

6. W menu rozwijanym obszary nazw wybierz pozycję **azure.vm.windows.guest**

7. W menu rozwijanym metryki wybierz polecenie **Bajty\%zatwierdzone w pamięci w użyciu**.


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [metrykach niestandardowych](metrics-custom-overview.md).

