---
title: Wyślij metryki systemu operacyjnego gościa, aby metryki usługi Azure Monitor przechowywania, przy użyciu szablonu usługi Resource Manager dla Windows maszyny wirtualnej
description: Wyślij metryki systemu operacyjnego gościa, aby metryki usługi Azure Monitor przechowywania, przy użyciu szablonu usługi Resource Manager dla Windows maszyny wirtualnej
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: f3076054eb6e18eb5143a34ba558c1f9e43ea4a5
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49345190"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Wyślij metryki systemu operacyjnego gościa, aby metryki usługi Azure Monitor przechowywania, przy użyciu szablonu usługi Resource Manager dla Windows maszyny wirtualnej

Usługi Azure Monitor [rozszerzenia diagnostyki Azure Windows](azure-diagnostics.md) (WAD) umożliwia zbieranie metryk i dzienników uruchamiania systemu operacyjnego gościa (systemu operacyjnego gościa) jako część klastra maszyny wirtualnej, usługa w chmurze lub usługi Service Fabric.  Rozszerzenie może wysyłać telemetrię do wielu różnych lokalizacjach, wymienione w artykule wcześniej połączona.  

W tym artykule opisano proces metryki wydajności systemu operacyjnego gościa wysyłania dla Windows maszyny wirtualnej do magazynu danych usługi Azure Monitor. Począwszy od wersji 1.11 WAD, można napisać metryki bezpośrednio do sklepu metryk usługi Azure Monitor, gdy już zbieranymi metrykami standardowa platforma. Przechowywania ich w tej lokalizacji umożliwia dostęp do tego samego Akcje dostępne dla platform metryk.  Akcje obejmują niemal w czasie rzeczywistym alertów, wykresy, routingu, dostęp z interfejsu API REST i nie tylko.  W przeszłości rozszerzenia WAD powstała z jednego do usługi Azure Storage, ale nie do magazynu danych usługi Azure Monitor.   

Jeśli dopiero zaczynasz pracę z szablonami usługi Resource Manager, Dowiedz się więcej o [wdrożeń szablonu](../azure-resource-manager/resource-group-overview.md)oraz struktury i składni.  

## <a name="pre-requisites"></a>Wymagania wstępne

- Twoja subskrypcja musi być zarejestrowana w [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- Musisz mieć [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) zainstalowany, możesz też [usługi Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 

 
## <a name="set-up-azure-monitor-as-a-data-sink"></a>Konfigurowanie usługi Azure Monitor jako ujście danych 
Rozszerzenie diagnostyki platformy Azure używa funkcji o nazwie "danych wychwytywanie" trasy metryk i dzienników do innej lokalizacji.  Poniższe kroki pokazują, jak wdrożyć Maszynę wirtualną przy użyciu nowego ujście danych "Azure Monitor" za pomocą szablonu usługi Resource Manager i programu PowerShell. 

## <a name="author-resource-manager-template"></a>Szablon usługi Resource Manager dla autora 
Na przykład można użyć publicznie dostępnych przykładowy szablon. Począwszy od szablony są pod https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows 

- **Azuredeploy.JSON** jest wstępnie skonfigurowany szablon usługi Resource Manager dla wdrożenia maszyny wirtualnej. 

- **Azuredeploy.parameters.JSON** jest plik parametrów, która przechowuje informacje, takie jak jakie nazwy użytkownika i hasła chcesz ustawić dla maszyny Wirtualnej. Podczas wdrażania szablonu usługi Resource Manager używa parametrów ustawione w tym pliku. 

Pobierz i Zapisz oba pliki lokalnie. 

###  <a name="modify-azuredeployparametersjson"></a>Modyfikowanie azuredeploy.parameters.json
Otwórz *azuredeploy.parameters.json* pliku 

1. Wprowadź wartości w polach *adminUsername* i *adminPassword* dla maszyny Wirtualnej. Te parametry są używane dla dostępu zdalnego z maszyną wirtualną. NIE należy używać w tym szablonie pozwala uniknąć przejęty maszyny Wirtualnej. Boty skanowania z Internetem, aby nazwy użytkowników i hasła w publicznych repozytoriach Github. Mogą one mieć testowania maszyn wirtualnych przy użyciu tych ustawień domyślnych.  

1. Utwórz unikatową dnsname dla maszyny Wirtualnej.  

### <a name="modify-azuredeployjson"></a>Modyfikowanie azuredeploy.json

Otwórz *azuredeploy.json* pliku 

Identyfikator konta magazynu, aby dodać **zmienne** części szablonu po wejściu do **storageAccountName**.  

```json
// Find these lines 
"variables": { 
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]", 

// Add this line directly below.  
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]", 
```

Dodaj ten zarządzanych tożsamości dla rozszerzenia zasobów platformy Azure do szablonu w górnej części sekcję "zasoby".  Rozszerzenie zapewnia, że usługa Azure Monitor akceptuje metryk jest emitowane.  

```json
//Find this code 
"resources": [
// Add this code directly below
    { 
        "type": "Microsoft.Compute/virtualMachines/extensions", 
        "name": "WADExtensionSetup", 
        "apiVersion": "2015-05-01-preview", 
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

Dodaj konfigurację "tożsamość" do zasobu maszyny Wirtualnej, aby upewnić się, przypisuje platformy Azure z rozszerzeniem MSI tożsamość systemu. Ten krok zapewnia, że maszyna wirtualna może emitować metryki gościa o sobie samym do usługi Azure Monitor 

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

Dodaj następującą konfigurację, aby włączyć rozszerzenie diagnostyki na maszynie wirtualnej Windows.  Dla prostych opartych na usłudze Resource Manager maszyny wirtualnej możemy dodać konfiguracji rozszerzenia do tablicy zasobów dla maszyny wirtualnej. Wiersz "sink": "AzMonSink" i odpowiednie "SinksConfig" w dalszej części sekcji Włączanie rozszerzenia emitują metryki bezpośrednio do usługi Azure Monitor. Możesz dodawać i usuwać liczniki wydajności, zgodnie z potrzebami.  


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
            "apiVersion": "2015-05-01-preview", 
            "location": "[resourceGroup().location]", 
            "dependsOn": [ 
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" 
            ], 
            "properties": { 
            "publisher": "Microsoft.Azure.Diagnostics", 
            "type": "IaaSDiagnostics", 
            "typeHandlerVersion": "1.4", 
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


Zapisz i zamknij oba pliki 
 

## <a name="deploy-the-resource-manager-template"></a>Wdrażanie szablonu usługi Resource Manager 

> [!NOTE]
> Musi być uruchomiona diagnostyki Azure rozszerzenia w wersji 1.5 lub nowszej i ma "autoUpgradeMinorVersion": właściwości ustawiono wartość "true" w szablonie usługi Resource Manager.  Po uruchomieniu maszyny Wirtualnej platformy Azure następnie ładuje właściwe rozszerzenie. Jeśli nie masz tych ustawień w szablonie, należy je zmienić i ponownego wdrażania szablonu. 


Aby wdrożyć szablon usługi Resource Manager będziemy korzystać programu Azure PowerShell.  

1. Uruchamianie programu PowerShell 
1. Zaloguj się do platformy Azure za pomocą `Login-AzureRmAccount`
1. Pobierz listę subskrypcji przy użyciu `Get-AzureRmSubscription`
1. Ustaw subskrypcję, której można będzie można tworzenia/aktualizacji maszyny wirtualnej w 

   ```PowerShell
   Select-AzureRmSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Utwórz nową grupę zasobów dla maszyny Wirtualnej wdrożone, uruchom poniższe polecenie 

   ```PowerShell
    New-AzureRmResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>" 
   ```
   > [!NOTE] 
   > Pamiętaj, aby [region platformy Azure, która jest włączona na użytek metryki niestandardowe](metrics-custom-overview.md). 
 
1. Wykonaj następujące polecenia, aby wdrożyć maszynę Wirtualną za pomocą  
   > [!NOTE] 
   > Jeśli chcesz zaktualizować istniejącą maszynę Wirtualną, wystarczy dodać *— tryb Przyrostowy* -to-end następującego polecenia. 
 
   ```PowerShell
   New-AzureRmResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>" 
   ```
  
1. Po pomyślnym wdrożeniu można znaleźć maszyny Wirtualnej w witrynie Azure Portal, a powinien być emitowania metryk do usługi Azure Monitor. 

   > [!NOTE] 
   > Mogą występować błędy wokół wybranego vmSkuSize. Jeśli tak się stanie, wróć do pliku azuredeploy.json i zaktualizuj wartość domyślna parametru vmSkuSize. W tym przypadku zaleca się podjęcie próby "Standard_DS1_v2"). 

## <a name="chart-your-metrics"></a>Wykres metryk 

1. Zaloguj się do witryny Azure Portal 

1. W menu po lewej stronie kliknij **monitora** 

1. Na stronie monitora kliknij **metryki**. 

   ![Strona metryki](./media/metrics-store-custom-rest-api/metrics.png) 

1. Okres agregacji można zmienić **ciągu ostatnich 30 minut**.  

1. W zasobie listy rozwijanej wybierz właśnie utworzony maszyny Wirtualnej. Jeśli nie zmienisz nazwy szablonu, należy go *SimpleWinVM2*.  

1. W przestrzeniach nazw listy rozwijanej wybierz **azure.vm.windows.guest** 

1. W metryki listę rozwijaną, wybierz **pamięci\%przydzielone bajty w użyciu**.  
 

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [metryki niestandardowe](metrics-custom-overview.md).