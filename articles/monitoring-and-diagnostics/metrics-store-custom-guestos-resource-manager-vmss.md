---
title: Wyślij metryki systemu operacyjnego gościa, aby metryki usługi Azure Monitor przechowywania dla zestawu skalowania maszyn wirtualnych Windows przy użyciu szablonu usługi Resource Manager
description: Wyślij metryki systemu operacyjnego gościa, aby metryki usługi Azure Monitor przechowywania dla zestawu skalowania maszyn wirtualnych Windows przy użyciu szablonu usługi Resource Manager
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: d896cb01c7dc2cd4ed028db418f838809c7ce25c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987003"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Wyślij metryki systemu operacyjnego gościa, aby metryki usługi Azure Monitor przechowywania dla zestawu skalowania maszyn wirtualnych Windows przy użyciu szablonu usługi Resource Manager

Usługi Azure Monitor [rozszerzenia diagnostyki Azure Windows](azure-diagnostics.md) (WAD) umożliwia zbieranie metryk i dzienników uruchamiania systemu operacyjnego gościa (systemu operacyjnego gościa) jako część klastra maszyny wirtualnej, usługa w chmurze lub usługi Service Fabric.  Rozszerzenie może wysyłać telemetrię do wielu różnych lokalizacjach, wymienione w artykule wcześniej połączona.  

W tym artykule opisano proces metryki wydajności systemu operacyjnego gościa wysyłania zestawu w magazynie danych usługi Azure Monitor skalowania maszyn wirtualnych Windows. Począwszy od wersji 1.11 WAD, można napisać metryki bezpośrednio do sklepu metryk usługi Azure Monitor, gdy już zbieranymi metrykami standardowa platforma. Przechowywania ich w tej lokalizacji umożliwia dostęp do tego samego Akcje dostępne dla platform metryk.  Akcje obejmują niemal w czasie rzeczywistym alertów, wykresy, routingu, dostęp z interfejsu API REST i nie tylko.  W przeszłości rozszerzenia WAD powstała z jednego do usługi Azure Storage, ale nie do magazynu danych usługi Azure Monitor.  

Jeśli jesteś nowym użytkownikiem szablonów usługi Resource Manager, Dowiedz się więcej o [wdrożeń szablonu](../azure-resource-manager/resource-group-overview.md)oraz struktury i składni.  

## <a name="pre-requisites"></a>Wymagania wstępne

- Twoja subskrypcja musi być zarejestrowana w [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- Musisz mieć [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) zainstalowany, możesz też [usługi Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Konfigurowanie usługi Azure Monitor jako ujście danych 
Rozszerzenie diagnostyki platformy Azure używa funkcji o nazwie "danych wychwytywanie" trasy metryk i dzienników do innej lokalizacji.  Poniższe kroki pokazują, jak wdrożyć Maszynę wirtualną przy użyciu nowego ujście danych "Azure Monitor" za pomocą szablonu usługi Resource Manager i programu PowerShell. 

## <a name="author-resource-manager-template"></a>Szablon usługi Resource Manager dla autora 
Na przykład można użyć publicznie dostępnych przykładowy szablon. Począwszy od szablony są pod https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale  

- **Azuredeploy.JSON** jest wstępnie skonfigurowany szablon usługi Resource Manager do wdrożenia zestawu skalowania maszyn wirtualnych

- **Azuredeploy.parameters.JSON** jest plik parametrów, która przechowuje informacje, takie jak jakie nazwy użytkownika i hasła chcesz ustawić dla maszyny Wirtualnej. Podczas wdrażania szablonu usługi Resource Manager używa parametrów ustawione w tym pliku. 

Pobierz i Zapisz oba pliki lokalnie. 

###  <a name="modify-azuredeployparametersjson"></a>Modyfikowanie azuredeploy.parameters.json
Otwórz *azuredeploy.parameters.json* pliku 

- Podaj **vmSKU** chcesz wdrożyć (Firma Microsoft zaleca, aby maszyna wirtualna Standard_D2_v3) 
- Określ **windowsOSVersion** chcesz dla zestawu skalowania maszyn wirtualnych (Firma Microsoft zaleca 2016 Datacenter) 
- Nazwa zasobu, aby wdrożyć za pomocą zestawu skalowania maszyn wirtualnych **vmssName** właściwości. Na przykład *VMSS WAD TEST*.    
- Określ liczbę maszyn wirtualnych, które chcesz być uruchomione na maszynie wirtualnej zestawu skalowania przy użyciu **instanceCount** właściwości
- Wprowadź wartości w polach **adminUsername** i **adminPassword** skalowania maszyn wirtualnych można ustawić. Te parametry są używane dla dostępu zdalnego do maszyn wirtualnych w zestawie skalowania. Te parametry są używane dla dostępu zdalnego z maszyną wirtualną. NIE należy używać w tym szablonie pozwala uniknąć przejęty maszyny Wirtualnej. Boty skanowania z Internetem, aby nazwy użytkowników i hasła w publicznych repozytoriach Github. Mogą one mieć testowania maszyn wirtualnych przy użyciu tych ustawień domyślnych. 


###  <a name="modify-azuredeployjson"></a>Modyfikowanie azuredeploy.json
Otwórz *azuredeploy.json* pliku 

Dodaj zmienną do przechowywania informacji o koncie magazynu w szablonie usługi Resource Manager. Nadal należy podać konto magazynu w ramach instalacji rozszerzenia diagnostyki. Dzienniki i/lub liczniki wydajności w pliku konfiguracyjnym diagnostyki są zapisywane na koncie magazynu określonym oprócz są wysyłane do magazynu metryk usługi Azure Monitor. 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
 ```
 
Znaleźć definicji zestawu skalowania maszyn wirtualnych w sekcji zasobów i Dodaj sekcję "tożsamość" w konfiguracji. Gwarantuje to, że platforma Azure przypisuje tożsamość systemu. Ten krok zapewnia, że maszyny wirtualne w zestawie skalowania może emitować metryki gościa o sobie do usługi Azure Monitor.  

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


W **extensionProfile**, Dodaj nowe rozszerzenie do szablonu, jak to przedstawiono w **sekcji VMSS WAD rozszerzeń**.  Ta sekcja jest rozszerzenie tożsamości usługi zarządzanej (MSI), który gwarantuje, że metryki jest emitowane są akceptowane przez usługi Azure Monitor. **Nazwa** pole może zawierać dowolną nazwę. 

Poniższy kod poniżej rozszerzenia MSI dodaje także rozszerzenie diagnostyki i konfiguracji jako rozszerzenie zasobu do zasobu zestawu skalowania maszyny wirtualnej. Możesz dodawać i usuwać liczniki wydajności, zgodnie z potrzebami. 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed service identity   
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


Dodaj dependsOn dla konta magazynu upewnić się, że jest tworzony w odpowiedniej kolejności. 
```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
 ```

Tworzenie konta magazynu, jeśli jeden nie jest już utworzony w szablonie.  
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
> Musi być uruchomiona diagnostyki Azure rozszerzenia w wersji 1.5 lub nowszej i ma "autoUpgradeMinorVersion": właściwość wartość *true* w szablonie usługi Resource Manager.  Po uruchomieniu maszyny Wirtualnej platformy Azure następnie ładuje właściwe rozszerzenie. Jeśli nie masz tych ustawień w szablonie, należy je zmienić i ponownego wdrażania szablonu. 


Aby wdrożyć szablon usługi Resource Manager, będziemy korzystać programu Azure PowerShell.  

1. Uruchamianie programu PowerShell 
1. Zaloguj się do platformy Azure za pomocą `Login-AzureRmAccount`
1. Pobierz listę subskrypcji przy użyciu `Get-AzureRmSubscription`
1. Ustaw subskrypcję, której można będzie można tworzenia/aktualizacji maszyny wirtualnej w 

   ```PowerShell
   Select-AzureRmSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Utwórz nową grupę zasobów dla maszyny Wirtualnej wdrożone, uruchom poniższe polecenie 

   ```PowerShell
    New-AzureRmResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Pamiętaj, aby korzystać z regionu platformy Azure, który jest włączony dla metryki niestandardowe. Zobacz 
 
1. Wykonaj następujące polecenia, aby wdrożyć maszynę Wirtualną za pomocą  
   > [!NOTE] 
   > Jeśli chcesz zaktualizować istniejącego zestawu skalowania, po prostu Dodaj *— tryb Przyrostowy* -to-end następującego polecenia. 
 
   ```PowerShell
   New-AzureRmResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. Po pomyślnym wdrożeniu można znaleźć maszyny wirtualnej zestawu skalowania w witrynie Azure portal, a powinien być emitowania metryk do usługi Azure Monitor. 

   > [!NOTE] 
   > Mogą występować błędy wokół wybranego vmSkuSize. Jeśli tak się stanie, wróć do pliku azuredeploy.json i zaktualizuj wartość domyślna parametru vmSkuSize. W tym przypadku zaleca się podjęcie próby "Standard_DS1_v2"). 


## <a name="chart-your-metrics"></a>Wykres metryk 

1. Logowanie się do witryny Azure Portal 

1. W menu po lewej stronie kliknij **monitora** 

1. Na stronie monitora kliknij **metryki**. 

   ![Strona metryki](./media/metrics-store-custom-rest-api/metrics.png) 

1. Okres agregacji można zmienić **ciągu ostatnich 30 minut**.  

1. W zasobie listy rozwijanej wybierz zestaw skalowania maszyn wirtualnych, który został utworzony.  

1. W przestrzeniach nazw listy rozwijanej wybierz **azure.vm.windows.guest** 

1. W metryki listę rozwijaną, wybierz **pamięci\%przydzielone bajty w użyciu**.  

Następnie możesz również na korzystanie z wymiarów na tę metrykę do wykresu tej metryki dla konkretnej maszyny Wirtualnej w zestawie skalowania, lub do wykreślenia w każdej maszynie Wirtualnej w zestawie skalowania. 



## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [metryki niestandardowe](metrics-custom-overview.md).

