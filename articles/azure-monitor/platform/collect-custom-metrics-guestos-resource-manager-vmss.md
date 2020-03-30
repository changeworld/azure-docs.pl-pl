---
title: Zbieranie metryk zestawu skalowania systemu Windows w usłudze Azure Monitor z szablonem
description: Wysyłanie metryk systemu operacyjnego gościa do magazynu metryk Usługi Azure Monitor przy użyciu szablonu Menedżera zasobów dla zestawu skalowania maszyny wirtualnej systemu Windows
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 24f83e4f6285d045e67bdaef431ebcff2345ef84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663900"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Wysyłanie metryk systemu operacyjnego gościa do magazynu metryk Usługi Azure Monitor przy użyciu szablonu usługi Azure Resource Manager dla zestawu skalowania maszyny wirtualnej systemu Windows

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Za pomocą [rozszerzenia diagnostyki systemu Azure Azure Azure (WAD)](diagnostics-extension-overview.md)usługi Azure , można zbierać metryki i dzienniki z systemu operacyjnego gościa (system operacyjny gościa), który działa jako część maszyny wirtualnej, usługi w chmurze lub klastra sieci szkieletowej usługi Azure. Rozszerzenie może wysyłać dane telemetryczne do wielu różnych lokalizacji wymienionych w wcześniej połączonym artykule.  

W tym artykule opisano proces wysyłania metryk wydajności systemu operacyjnego gościa dla skalowania maszyny wirtualnej systemu Windows ustawionej na magazyn danych usługi Azure Monitor. Począwszy od diagnostyki systemu Windows Azure w wersji 1.11, można zapisywać metryki bezpośrednio w magazynie metryk usługi Azure Monitor, gdzie standardowe metryki platformy są już zbierane. Przechowując je w tej lokalizacji, można uzyskać dostęp do tych samych akcji, które są dostępne dla metryk platformy. Akcje obejmują niemal alerty w czasie rzeczywistym, wykresy, routing, dostęp z interfejsu API REST i inne. W przeszłości rozszerzenie diagnostyki systemu Windows Azure pisał do usługi Azure Storage, ale nie do magazynu danych usługi Azure Monitor.  

Jeśli jesteś nowym użytkownikiem szablonów Menedżera zasobów, dowiedz się więcej o [wdrożeniach szablonów](../../azure-resource-manager/management/overview.md) oraz ich strukturze i składni.  

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja musi być zarejestrowana w [witrynie Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Musisz mieć zainstalowany [program Azure PowerShell](/powershell/azure) lub użyć [usługi Azure Cloud Shell.](https://docs.microsoft.com/azure/cloud-shell/overview) 

- Zasób maszyny Wirtualnej musi znajdować się w [regionie obsługującym metryki niestandardowe](metrics-custom-overview.md#supported-regions).

## <a name="set-up-azure-monitor-as-a-data-sink"></a>Konfigurowanie usługi Azure Monitor jako ujścia danych 
Rozszerzenie diagnostyki platformy Azure używa funkcji o nazwie **pochłaniacze danych** do kierowania metryk i dzienników do różnych lokalizacji. Poniższe kroki pokazują, jak używać szablonu Menedżera zasobów i programu PowerShell do wdrażania maszyny wirtualnej przy użyciu nowego ujścia danych usługi Azure Monitor. 

## <a name="author-a-resource-manager-template"></a>Tworzenie szablonu Menedżera zasobów 
W tym przykładzie można użyć publicznie dostępnego [przykładowego szablonu:](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale)  

- **Azuredeploy.json** to wstępnie skonfigurowany szablon Menedżera zasobów do wdrażania zestawu skalowania maszyny wirtualnej.

- **Azuredeploy.parameters.json** to plik parametrów, który przechowuje informacje, takie jak nazwa użytkownika i hasło, które chcesz ustawić dla maszyny Wirtualnej. Podczas wdrażania szablon Menedżera zasobów używa parametrów określonych w tym pliku. 

Pobierz i zapisz oba pliki lokalnie. 

###  <a name="modify-azuredeployparametersjson"></a>Modyfikowanie pliku azuredeploy.parameters.json
Otwórz plik **azuredeploy.parameters.json:**  
 
- Podaj **vmSKU,** którą chcesz wdrożyć. Polecamy Standard_D2_v3. 
- Określ **windowsOSVersion,** który ma dla zestawu skalowania maszyny wirtualnej. Zalecamy 2016-Datacenter. 
- Nazwij zasób zestawu skalowania maszyny wirtualnej, który ma zostać wdrożony przy użyciu właściwości **vmssName.** Przykładem jest **VMSS-WAD-TEST**.    
- Określ liczbę maszyn wirtualnych, które mają być uruchamiane w skali maszyny wirtualnej ustawionej przy użyciu właściwości **instanceCount.**
- Wprowadź wartości dla **adminUsername** i **adminPassword** dla zestawu skalowania maszyny wirtualnej. Parametry te są używane do zdalnego dostępu do maszyn wirtualnych w zestawie skalowania. Aby uniknąć porwania maszyny Wirtualnej, **nie** należy używać tych w tym szablonie. Boty skanują internet w poszukiwaniu nazw użytkowników i haseł w publicznych repozytoriach GitHub. Prawdopodobnie będą testować maszyny wirtualne z tymi wartościami domyślnymi. 


###  <a name="modify-azuredeployjson"></a>Modyfikowanie pliku azuredeploy.json
Otwórz plik **azuredeploy.json.** 

Dodaj zmienną, aby przechowywać informacje o koncie magazynu w szablonie Menedżera zasobów. Wszystkie dzienniki lub liczniki wydajności określone w pliku konfiguracji diagnostyki są zapisywane zarówno w magazynie metryk Usługi Azure Monitor, jak i na koncie magazynu, które określisz w tym miejscu: 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
Znajdź definicję zestawu skalowania maszyny wirtualnej w sekcji zasobów i dodaj sekcję **tożsamości** do konfiguracji. To dodanie gwarantuje, że platforma Azure przypisuje mu tożsamość systemu. Ten krok zapewnia również, że maszyny wirtualne w zestawie skalowania mogą emitować metryki gościa o sobie do usługi Azure Monitor:  

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

W zasobie zestawu skalowania maszyny wirtualnej znajdź sekcję **virtualMachineProfile.** Dodaj nowy profil o nazwie **rozszerzeniaProfile** do zarządzania rozszerzeniami.  


W **rozszerzeńProfile**dodaj nowe rozszerzenie do szablonu, jak pokazano w sekcji **VMSS-WAD-extension.**  Ta sekcja jest tożsamościami zarządzanymi dla rozszerzenia zasobów platformy Azure, który zapewnia, że emitowane metryki są akceptowane przez usługę Azure Monitor. Pole **nazwy** może zawierać dowolną nazwę. 

Poniższy kod z rozszerzenia MSI dodaje również rozszerzenie diagnostyki i konfiguracji jako zasób rozszerzenia do zasobu zestawu skalowania maszyny wirtualnej. W razie potrzeby możesz dodać lub usunąć liczniki wydajności: 

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


Dodaj **dependsOn** dla konta magazynu, aby upewnić się, że jest tworzony w odpowiedniej kolejności: 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

Utwórz konto magazynu, jeśli nie jest jeszcze utworzone w szablonie: 

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

## <a name="deploy-the-resource-manager-template"></a>Wdrażanie szablonu Menedżera zasobów 

> [!NOTE]  
> Musisz być uruchomiony rozszerzenie diagnostyki platformy Azure w wersji 1.5 lub nowszej **i** mieć **autoUpgradeMinorVersion:** właściwość ustawiona na **true** w szablonie Menedżera zasobów. Platforma Azure następnie ładuje odpowiednie rozszerzenie po uruchomieniu maszyny Wirtualnej. Jeśli nie masz tych ustawień w szablonie, zmień je i ponownie wdrożyj szablon. 


Aby wdrożyć szablon Usługi Resource Manager, użyj programu Azure PowerShell:  

1. Uruchom program PowerShell. 
1. Zaloguj się na `Login-AzAccount`platformie Azure przy użyciu programu .
1. Pobierz listę subskrypcji za `Get-AzSubscription`pomocą programu .
1. Ustaw subskrypcję, którą utworzysz, lub zaktualizuj maszynę wirtualną: 

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Utwórz nową grupę zasobów dla wdrażanych maszyn wirtualnych. Uruchom następujące polecenie: 

   ```powershell
    New-AzResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Pamiętaj, aby użyć regionu platformy Azure, który jest włączony dla metryk niestandardowych. Pamiętaj, aby użyć [regionu platformy Azure, który jest włączony dla niestandardowych metryk.](https://github.com/MicrosoftDocs/azure-docs-pr/pull/metrics-custom-overview.md#supported-regions)
 
1. Uruchom następujące polecenia, aby wdrożyć maszynę wirtualną:  

   > [!NOTE]  
   > Jeśli chcesz zaktualizować istniejący zestaw skalowania, dodaj **-Mode Przyrostowe** na końcu polecenia. 
 
   ```powershell
   New-AzResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. Po pomyślnym wdrożeniu należy znaleźć skalę maszyny wirtualnej ustawioną w witrynie Azure portal. Powinien emitować metryki do usługi Azure Monitor. 

   > [!NOTE]  
   > Możesz napotkać błędy wokół wybranego **vmSkuSize**. W takim przypadku wróć do pliku **azuredeploy.json** i zaktualizuj domyślną wartość parametru **vmSkuSize.** Zalecamy wypróbowanie **Standard_DS1_v2**. 


## <a name="chart-your-metrics"></a>Wykres metryki 

1. Zaloguj się do Portalu Azure. 

1. W menu po lewej stronie wybierz pozycję **Monitor**. 

1. Na stronie **Monitor** wybierz pozycję **Metryki**. 

   ![Monitor - strona Metryki](media/collect-custom-metrics-guestos-resource-manager-vmss/metrics.png) 

1. Zmień okres agregacji na **Ostatnie 30 minut**.  

1. W menu rozwijanym zasobu wybierz utworzony zestaw skalowania maszyny wirtualnej.  

1. W menu rozwijanym obszary nazw wybierz pozycję **azure.vm.windows.guest**. 

1. W menu rozwijanym metryki wybierz polecenie **Bajty popełnione w pamięci\%w użyciu**.  

Następnie można również użyć wymiarów na tej metryki, aby wykres go dla określonej maszyny Wirtualnej lub wykreślić każdą maszynę wirtualną w zestawie skalowania. 



## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [metrykach niestandardowych](metrics-custom-overview.md).


