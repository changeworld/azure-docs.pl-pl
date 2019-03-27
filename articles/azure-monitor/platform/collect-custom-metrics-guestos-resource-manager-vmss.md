---
title: Wysyłanie metryk przez system operacyjny gościa do usługi Azure Monitor metryki magazynu przy użyciu szablonu usługi Azure Resource Manager dla zestawu skalowania maszyn wirtualnych Windows
description: Wysyłanie metryk przez system operacyjny gościa do usługi Azure Monitor metryki magazynu przy użyciu szablonu usługi Resource Manager dla zestawu skalowania maszyn wirtualnych Windows
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 573c205cd2e208a1cb2b526d96fb08ca21331c80
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58481327"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Wysyłanie metryk przez system operacyjny gościa do usługi Azure Monitor metryki magazynu przy użyciu szablonu usługi Azure Resource Manager dla zestawu skalowania maszyn wirtualnych Windows

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Za pomocą usługi Azure Monitor [rozszerzenia diagnostyki Azure Windows (WAD)](diagnostics-extension-overview.md), można zbierać metryki i dzienniki z systemu operacyjnego gościa (systemu operacyjnego gościa), które zostaną uruchomione w ramach maszyny wirtualnej, usługa w chmurze lub klastra usługi Azure Service Fabric. Rozszerzenie może wysyłać telemetrię do wielu różnych lokalizacjach, wymienione w artykule wcześniej połączona.  

W tym artykule opisano proces metryki wydajności systemu operacyjnego gościa wysyłania zestawu w magazynie danych usługi Azure Monitor skalowania maszyn wirtualnych Windows. Począwszy od programu Windows Azure Diagnostics wersji 1.11, można napisać przechowywać metryki bezpośrednio do metryk usługi Azure Monitor, gdy już zbieranymi metrykami standardowa platforma. Dzięki przechowywaniu ich w tej lokalizacji, możesz uzyskać dostęp, te same akcje, które są dostępne dla platform metryk. Akcje obejmują prawie alerty w czasie rzeczywistym, wykresy, routingu, dostęp z interfejsu API REST i nie tylko. W przeszłości przez rozszerzenie Diagnostyka Azure Windows powstała z jednego do usługi Azure Storage, ale nie do magazynu danych usługi Azure Monitor.  

Jeśli dopiero zaczynasz pracę z szablonami usługi Resource Manager, Dowiedz się więcej o [wdrożeń szablonu](../../azure-resource-manager/resource-group-overview.md) oraz struktury i składni.  

## <a name="prerequisites"></a>Wymagania wstępne

- Twoja subskrypcja musi być zarejestrowana w [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Musisz mieć [programu Azure PowerShell](/powershell/azure) zainstalowany, możesz też [usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Konfigurowanie usługi Azure Monitor jako ujście danych 
Rozszerzenie diagnostyki platformy Azure używa funkcji o nazwie **ujść danych** trasy metryk i dzienników do innej lokalizacji. Poniższe kroki pokazują, jak wdrożyć Maszynę wirtualną przy użyciu nowej usługi Azure Monitor ujście danych za pomocą szablonu usługi Resource Manager i programu PowerShell. 

## <a name="author-a-resource-manager-template"></a>Tworzenie szablonu usługi Resource Manager 
Na przykład można użyć publicznie dostępnych [przykładowy szablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale):  

- **Azuredeploy.JSON** jest wstępnie skonfigurowany szablon usługi Resource Manager do wdrożenia zestawu skalowania maszyn wirtualnych.

- **Azuredeploy.parameters.JSON** jest plik parametrów, która przechowuje informacje, takie jak jakie nazwę użytkownika i hasło, którego chcesz ustawić dla maszyny Wirtualnej. Podczas wdrażania szablonu usługi Resource Manager używa parametrów ustawione w tym pliku. 

Pobierz i Zapisz oba pliki lokalnie. 

###  <a name="modify-azuredeployparametersjson"></a>Modify azuredeploy.parameters.json
Otwórz **azuredeploy.parameters.json** pliku:  
 
- Podaj **vmSKU** mają zostać wdrożone. Zalecamy, aby maszyna wirtualna Standard_D2_v3. 
- Określ **windowsOSVersion** ma dla zestawu skalowania maszyn wirtualnych. Firma Microsoft zaleca 2016 Datacenter. 
- Nazwa zasobu, aby można wdrożyć za pomocą zestawu skalowania maszyn wirtualnych **vmssName** właściwości. Na przykład **VMSS WAD TEST**.    
- Określ liczbę maszyn wirtualnych, które chcesz uruchomić w zestawie przy użyciu skalowania maszyn wirtualnych **instanceCount** właściwości.
- Wprowadź wartości w polach **adminUsername** i **adminPassword** skalowania maszyn wirtualnych można ustawić. Te parametry są używane dla dostępu zdalnego do maszyn wirtualnych w zestawie skalowania. Aby uniknąć przejęty, maszyna wirtualna **nie** użyć w tym szablonie. Boty skanowania z Internetem, aby nazwy użytkowników i hasła w publicznych repozytoriach GitHub. Prawdopodobnie będą do testowania maszyny wirtualne przy użyciu tych ustawień domyślnych. 


###  <a name="modify-azuredeployjson"></a>Modify azuredeploy.json
Otwórz **azuredeploy.json** pliku. 

Dodaj zmienną do przechowywania informacji o koncie magazynu w szablonie usługi Resource Manager. Wszystkie dzienniki lub liczniki wydajności w pliku konfiguracyjnym diagnostyki są zapisywane do magazynu metryk usługi Azure Monitor oraz konto magazynu określone w tym miejscu: 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
Znajdź definicję zestawu skalowania maszyny wirtualnej w sekcji zasobów i Dodaj **tożsamości** sekcji konfiguracji. To dodawanie gwarantuje, że platforma Azure przypisze on tożsamość systemu. Ten krok zapewnia również, że maszyny wirtualne w zestawie skalowania może emitować metryki gościa o sobie do usługi Azure Monitor:  

```json
    { 
      "type": "Microsoft.Compute/virtualMachineScaleSets", 
      "name": "[variables('namingInfix')]", 
      "location": "[resourceGroup().location]", 
      "apiVersion": "2017-03-30", 
      //add these lines below
      "identity": { 
           "type": "systemAssigned" 
       }, 
       //end of lines to add
```

Na maszynie wirtualnej zasobów zestawu skalowania, Znajdź **virtualMachineProfile** sekcji. Dodaj nowy profil o nazwie **extensionsProfile** do zarządzania rozszerzeniami.  


W **extensionProfile**, Dodaj nowe rozszerzenie do szablonu, jak pokazano na **VMSS WAD rozszerzeń** sekcji.  Ta sekcja dotyczy zarządzanych tożsamości dla rozszerzenia zasobów platformy Azure, która gwarantuje, że metryki jest emitowane są akceptowane przez usługi Azure Monitor. **Nazwa** pole może zawierać dowolną nazwę. 

Poniższy kod z rozszerzeniem MSI dodaje także rozszerzenie diagnostyki i konfiguracji jako rozszerzenie zasobu do zasobu zestawu skalowania maszyny wirtualnej. Możesz dodać lub usunąć liczniki wydajności, zgodnie z potrzebami: 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed identities for Azure resources   
                { 
                 "name": "VMSS-WAD-extension", 
                 "properties": { 
                       "publisher": "Microsoft.ManagedIdentity", 
                       "type": "ManagedIdentityExtensionForWindows", 
                       "typeHandlerVersion": "1.0", 
                       "autoUpgradeMinorVersion": true, 
                       "settings": { 
                             "port": 50342 
                           }, 
                       "protectedSettings": {} 
                     } 
                                
            }, 
            // add diagnostic extension. (Remove this comment after pasting.)
            { 
              "name": "[concat('VMDiagnosticsVmExt','_vmNodeType0Name')]", 
              "properties": { 
                   "type": "IaaSDiagnostics", 
                   "autoUpgradeMinorVersion": true, 
                   "protectedSettings": { 
                        "storageAccountName": "[variables('storageAccountName')]", 
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')),'2015-05-01-preview').key1]", 
                        "storageAccountEndPoint": "https://core.windows.net/" 
                   }, 
                   "publisher": "Microsoft.Azure.Diagnostics", 
                   "settings": { 
                        "WadCfg": { 
                              "DiagnosticMonitorConfiguration": { 
                                   "overallQuotaInMB": "50000", 
                                   "PerformanceCounters": { 
                                       "scheduledTransferPeriod": "PT1M", 
                                       "sinks": "AzMonSink", 
                                       "PerformanceCounterConfiguration": [ 
                                          { 
              "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                                              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Available Bytes", 
              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Committed Bytes", 
              "sampleRate": "PT15S" 
           } 
                                       ] 
                                 }, 
                                 "EtwProviders": { 
                                       "EtwEventSourceProviderConfiguration": [ 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Actors", 
                                              "scheduledTransferKeywordFilter": "1", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                              "eventDestination": "ServiceFabricReliableActorEventTable" 
                                           } 
                                           }, 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Services", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricReliableServiceEventTable" 
                                              } 
                                           } 
                                     ], 
                                     "EtwManifestProviderConfiguration": [ 
                                           { 
                                              "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8", 
                                              "scheduledTransferLogLevelFilter": "Information", 
                                              "scheduledTransferKeywordFilter": "4611686018427387904", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricSystemEventTable" 
                                              } 
                                          } 
                                     ] 
                               } 
                               }, 
                               "SinksConfig": { 
                                     "Sink": [ 
                                          { 
                                              "name": "AzMonSink", 
                                              "AzureMonitor": {} 
                                          } 
                                      ] 
                               } 
                         }, 
                         "StorageAccount": "[variables('storageAccountName')]" 
                         }, 
                        "typeHandlerVersion": "1.11" 
                  } 
           } 
            ] 
          }
          }
      }
    },
    //end of added code plus a few brackets. Be sure that the number and type of brackets match properly when done. 
    {
      "type": "Microsoft.Insights/autoscaleSettings",
...
```


Dodaj **dependsOn** dla konta magazynu upewnić się, jest tworzony w odpowiedniej kolejności: 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

Tworzenie konta magazynu, jeśli jedna z nich nie jest już utworzona w szablonie: 

```json
"resources": [
// add this code    
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
    "accountType": "Standard_LRS"
    }
},
// end added code
{ 
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
```

Zapisz i zamknij oba pliki. 

## <a name="deploy-the-resource-manager-template"></a>Wdrażanie szablonu usługi Resource Manager 

> [!NOTE]  
> Użytkownik musi działać wersja rozszerzenia diagnostyki Azure wersji 1.5 lub nowszej **i** mają **autoUpgradeMinorVersion:** właściwością **true** w Menedżerze zasobów szablon. Po uruchomieniu maszyny Wirtualnej platformy Azure następnie ładuje właściwe rozszerzenie. Jeśli nie masz tych ustawień w szablonie, należy je zmienić i ponownego wdrażania szablonu. 


Aby wdrożyć szablon usługi Resource Manager, należy użyć programu Azure PowerShell:  

1. Uruchom program PowerShell. 
1. Zaloguj się do platformy Azure za pomocą `Login-AzAccount`.
1. Pobierz listę subskrypcji przy użyciu `Get-AzSubscription`.
1. Ustaw subskrypcję będzie utworzyć lub zaktualizować maszyny wirtualnej: 

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Utwórz nową grupę zasobów dla wdrożenia maszyny Wirtualnej. Uruchom następujące polecenie: 

   ```powershell
    New-AzResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Pamiętaj, aby korzystać z regionu platformy Azure, który jest włączony dla metryki niestandardowe. Pamiętaj, aby używać [region platformy Azure, w którym włączono obsługę metryki niestandardowe](https://github.com/MicrosoftDocs/azure-docs-pr/pull/metrics-custom-overview.md#supported-regions).
 
1. Uruchom następujące polecenia, aby wdrożyć maszynę Wirtualną:  

   > [!NOTE]  
   > Jeśli chcesz zaktualizować istniejącego zestawu skalowania, należy dodać **— tryb Przyrostowy** na końcu polecenia. 
 
   ```powershell
   New-AzResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. Po pomyślnym wdrożeniu, należy odnaleźć maszyny wirtualnej zestawu skalowania w witrynie Azure portal. Powinien on emituje metryk do usługi Azure Monitor. 

   > [!NOTE]  
   > Możesz napotkać błędy wokół wybranego **vmSkuSize**. W takim przypadku należy wrócić do swojej **azuredeploy.json** plików i zaktualizuj wartość domyślną **vmSkuSize** parametru. Zaleca się, że próbujesz **standardowa_ds1_v2**. 


## <a name="chart-your-metrics"></a>Wykres metryk 

1. Zaloguj się do Portalu Azure. 

1. W menu po lewej stronie wybierz **Monitor**. 

1. Na **Monitor** wybierz opcję **metryki**. 

   ![Monitorowanie — metryki strony](media/collect-custom-metrics-guestos-resource-manager-vmss/metrics.png) 

1. Okres agregacji można zmienić **ciągu ostatnich 30 minut**.  

1. W menu rozwijanym zasobów wybierz zestaw skalowania maszyn wirtualnych, który został utworzony.  

1. Wybierz z menu rozwijanego przestrzenie nazw **azure.vm.windows.guest**. 

1. Wybierz z menu rozwijanego metryki **pamięci\%przydzielone bajty w użyciu**.  

Następnie możesz również użycie wymiarów na tę metrykę do wykresu go dla konkretnej maszyny Wirtualnej lub wykresu poszczególnych maszyn wirtualnych w zestawie skalowania. 



## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [metryki niestandardowe](metrics-custom-overview.md).


