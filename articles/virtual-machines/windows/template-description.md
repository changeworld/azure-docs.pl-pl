---
title: Maszyny wirtualne w szablonie usługi Azure Resource Manager | Microsoft Azure
description: Dowiedz się więcej na temat sposobu zasobu maszyny wirtualnej jest zdefiniowany w szablonie usługi Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f63ab5cc-45b8-43aa-a4e7-69dc42adbb99
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: cynthn
ms.openlocfilehash: 50fb5eeea5e3d6a2f991e92ae9c188822ddb40b0
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Maszyny wirtualne w szablonie usługi Azure Resource Manager

W tym artykule opisano aspekty szablonu usługi Azure Resource Manager, które są stosowane do maszyn wirtualnych. W tym artykule nie opisano pełną szablonu do utworzenia maszyny wirtualnej; w tym należy definicji zasobu dla kont magazynu, interfejsy sieciowe publicznych adresów IP i sieci wirtualnych. Aby uzyskać więcej informacji o sposobie tych zasobów można definiować razem, zobacz [Przewodnik po szablonie usługi Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Istnieje wiele [szablony w galerii](https://azure.microsoft.com/documentation/templates/?term=VM) zawierające zasobu maszyny Wirtualnej. Nie wszystkie elementy, które można uwzględnić w szablonie są opisane poniżej.

W tym przykładzie pokazano sekcję typowe zasobu szablon umożliwiający tworzenie określonej liczby maszyn wirtualnych:

```json
"resources": [
  { 
    "apiVersion": "2016-04-30-preview", 
    "type": "Microsoft.Compute/virtualMachines", 
    "name": "[concat('myVM', copyindex())]", 
    "location": "[resourceGroup().location]",
    "copy": {
      "name": "virtualMachineLoop", 
      "count": "[parameters('numberOfInstances')]"
    },
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/myNIC', copyindex())]" 
    ], 
    "properties": { 
      "hardwareProfile": { 
        "vmSize": "Standard_DS1" 
      }, 
      "osProfile": { 
        "computername": "[concat('myVM', copyindex())]", 
        "adminUsername": "[parameters('adminUsername')]", 
        "adminPassword": "[parameters('adminPassword')]" 
      }, 
      "storageProfile": { 
        "imageReference": { 
          "publisher": "MicrosoftWindowsServer", 
          "offer": "WindowsServer", 
          "sku": "2012-R2-Datacenter", 
          "version": "latest" 
        }, 
        "osDisk": { 
          "name": "[concat('myOSDisk', copyindex())]",
          "caching": "ReadWrite", 
          "createOption": "FromImage" 
        },
        "dataDisks": [
          {
            "name": "[concat('myDataDisk', copyindex())]",
            "diskSizeGB": "100",
            "lun": 0,
            "createOption": "Empty"
          }
        ] 
      }, 
      "networkProfile": { 
        "networkInterfaces": [ 
          { 
            "id": "[resourceId('Microsoft.Network/networkInterfaces',
              concat('myNIC', copyindex()))]" 
          } 
        ] 
      },
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('https://', variables('storageName'), '.blob.core.windows.net')]"
        }
      } 
    },
    "resources": [ 
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
      {
        "name": "MyCustomScriptExtension",
        "type": "extensions",
        "apiVersion": "2016-03-30",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
        ],
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.7",
          "autoUpgradeMinorVersion": true,
          "settings": {
            "fileUris": [
              "[concat('https://', variables('storageName'),
                '.blob.core.windows.net/customscripts/start.ps1')]" 
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
          }
        }
      } 
    ]
  } 
]
``` 

> [!NOTE] 
>W tym przykładzie opiera się na konto magazynu, który został wcześniej utworzony. Konta magazynu można utworzyć przez wdrożenie jej z szablonu. Przykład również zależy od interfejsu sieciowego i jego zasoby zależne, zdefiniowane w szablonie. Te zasoby nie są wyświetlane w przykładzie.
>
>

## <a name="api-version"></a>Wersja interfejsu API

Podczas wdrażania zasobów przy użyciu szablonu, należy określić wersję interfejsu API do użycia. W przykładzie pokazano zasobu maszyny wirtualnej za pomocą tego elementu apiVersion:

```
"apiVersion": "2016-04-30-preview",
```

Wersja interfejsu API, określ w szablonie dotyczy właściwości, które można zdefiniować w szablonie. Ogólnie rzecz biorąc należy zaznaczyć najnowszą wersję interfejsu API, podczas tworzenia szablonów. Istniejących szablonów można zdecydować, czy chcesz nadal używać starszej wersji interfejsu API, lub zaktualizować szablon do najnowszej wersji móc korzystać z nowych funkcji.

Użyj tych możliwości pobierania najnowszej wersji interfejsu API:

- Interfejs API REST - [listy wszystkich dostawców zasobów](https://docs.microsoft.com/rest/api/resources/providers#Providers_List)
- PowerShell - [Get-AzureRmResourceProvider](/powershell/module/azurerm.resources/get-azurermresourceprovider)
- Azure CLI 2.0 — [az Pokaż dostawcy](https://docs.microsoft.com/cli/azure/provider#az_provider_show)

## <a name="parameters-and-variables"></a>Parametry i zmienne

[Parametry](../../resource-group-authoring-templates.md) ułatwiają określenie wartości dla szablonu po jego uruchomieniu. W tej sekcji parametrów jest używana w tym przykładzie:

```        
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

Podczas wdrażania szablonu przykład wprowadzenie wartości dla nazwy i hasła konta administratora na każdej maszyny Wirtualnej i liczbę maszyn wirtualnych, aby utworzyć. Istnieje możliwość określenia wartości parametrów w osobnym pliku, który jest zarządzany przy użyciu szablonu lub podanie wartości po wyświetleniu monitu.

[Zmienne](../../resource-group-authoring-templates.md) ułatwiają konfigurowanie wartości w szablonie często używane w całej go lub które mogą ulec zmianie. W tej sekcji zmiennych jest używana w tym przykładzie:

```
"variables": { 
  "storageName": "mystore1",
  "accountid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name,
  '/providers/','Microsoft.Storage/storageAccounts/', variables('storageName'))]", 
  "wadlogs": "<WadCfg> 
    <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> 
      <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> 
      <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > 
        <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" />
      </WindowsEventLog>", 
  "wadperfcounters": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\">
      <PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\">
        <annotation displayName=\"Threads\" locale=\"en-us\"/>
      </PerformanceCounterConfiguration>
    </PerformanceCounters>", 
  "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters'), 
    '<Metrics resourceId=\"')]", 
  "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name , 
    '/providers/', 'Microsoft.Compute/virtualMachines/')]", 
  "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/>
    <MetricAggregation scheduledTransferPeriod=\"PT1M\"/>
    </Metrics></DiagnosticMonitorConfiguration>
    </WadCfg>"
}, 
```

Podczas wdrażania szablonu przykładowe wartości zmiennych są używane nazwy i identyfikatora wcześniej utworzone konto magazynu. Zmienne są również używane w celu zapewnienia ustawień diagnostycznych rozszerzenia. Użyj [najlepszych rozwiązań dotyczących tworzenia szablonów usługi Azure Resource Manager](../../resource-manager-template-best-practices.md) Aby określić sposób strukturę parametrów i zmiennych w szablonie.

## <a name="resource-loops"></a>Pętle zasobów

Jeśli potrzebujesz więcej niż jednej maszyny wirtualnej dla aplikacji, można użyć elementu kopiowania w szablonie. Ten opcjonalny element w pętli tworzenie liczby maszyn wirtualnych, które określono jako parametr:

```
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

Zauważ również, w tym przykładzie użytą indeks pętli, określając niektóre wartości dla zasobu. Na przykład jeśli wprowadzono liczby wystąpień trzy nazwy dysków systemu operacyjnego są myOSDisk1, myOSDisk2 i myOSDisk3:

```
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
}
```

> [!NOTE] 
>W tym przykładzie użyto zarządzanych dyski dla maszyn wirtualnych.
>
>

Należy pamiętać, że tworzenie pętli dla jednego zasobu w szablonie może wymagać używania pętli podczas tworzenia lub uzyskiwania dostępu do innych zasobów. Na przykład wiele maszyn wirtualnych nie mogą używać tego samego interfejsu sieciowego, dlatego jeśli szablonu w pętli tworzenia trzech maszyn wirtualnych należy również pętli przez proces tworzenia trzech interfejsów sieciowych. Podczas przypisywania do interfejsu sieciowego z maszyną wirtualną, indeks pętli jest używany do identyfikowania go:

```
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Zależności

Najwięcej zasobów są zależne od innych zasobów potrzebnych do prawidłowego działania. Maszyny wirtualne muszą być skojarzone z siecią wirtualną i zrobić, że wymaga ona interfejs sieciowy. [DependsOn](../../resource-group-define-dependencies.md) element służy do upewnij się, że interfejs sieciowy jest gotowa do użycia, przed utworzeniem maszyn wirtualnych:

```
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

Menedżer zasobów wdraża równolegle wszystkie zasoby, które nie są zależne od innego zasobu wdrażany. Należy zachować ostrożność podczas ustawiania zależności, ponieważ przypadkowo spowalniają wdrożenia przez określenie niepotrzebnych zależności. Zależności mogą być powiązane za pomocą wielu zasobów. Na przykład interfejsu sieciowego jest zależna od publicznego adresu IP i zasoby sieci wirtualnej.

Jak sprawdzić, czy zależność jest wymagane? Sprawdź wartości ustawionej w szablonie. Element punktów definicji zasobu maszyny wirtualnej do innego zasobu, które zostało wdrożone w tym samym szablonie, należy najpierw zależności. Na przykład na komputerze wirtualnym przykład definiuje profilu sieciowego:

```
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

Aby ustawić tę właściwość, musi istnieć interfejsu sieciowego. W związku z tym należy zależności. Należy również ustawić zależność, gdy jeden zasób (podrzędny) zdefiniowano w ramach innego zasobu (nadrzędnego). Na przykład ustawienia diagnostyki i rozszerzenia niestandardowego skryptu są oba zdefiniowane jako zasoby podrzędne maszyny wirtualnej. Nie można utworzyć dopóki maszyna wirtualna istnieje. W związku z tym oba zasoby są oznaczone jako zależne od maszyny wirtualnej.

## <a name="profiles"></a>Profile

Niektóre elementy profilu są używane podczas definiowania zasobu maszyny wirtualnej. Niektóre są wymagane, a inne opcjonalne. Na przykład hardwareProfile, osProfile storageProfile i networkProfile elementy są wymagane, ale diagnosticsProfile jest opcjonalna. Te profile zdefiniować ustawienia, takie jak:
   
- [Rozmiar](sizes.md)
- [Nazwa](/architecture/best-practices/naming-conventions) i poświadczenia
- dysk i [ustawień systemu operacyjnego](cli-ps-findimage.md)
- [Interfejs sieciowy](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md) 
- Diagnostyki rozruchu

## <a name="disks-and-images"></a>Dyski i obrazów
   
Na platformie Azure, pliki wirtualnego dysku twardego może reprezentować [dysków lub obrazów](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). W przypadku systemu operacyjnego w pliku vhd jest przeznaczone do można określonej maszyny Wirtualnej, jest określana na jako dysk. Gdy systemu operacyjnego w pliku vhd jest uogólnione w celu można utworzyć wiele maszyn wirtualnych, jego jest określany jako obraz.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Tworzenie nowych maszyn wirtualnych i nowych dysków z obrazu platformy

Podczas tworzenia maszyny Wirtualnej należy zdecydować, jakiego systemu operacyjnego do użycia. Element elementu imageReference służy do definiowania nowej maszyny Wirtualnej systemu operacyjnego. W przykładzie pokazano definicję dla systemu operacyjnego Windows Server:

```
"imageReference": { 
  "publisher": "MicrosoftWindowsServer", 
  "offer": "WindowsServer", 
  "sku": "2012-R2-Datacenter", 
  "version": "latest" 
},
```

Może użyć tej definicji, jeśli chcesz utworzyć systemu operacyjnego Linux:

```
"imageReference": {
  "publisher": "Canonical",
  "offer": "UbuntuServer",
  "sku": "14.04.2-LTS",
  "version": "latest"
},
```

Ustawienia konfiguracji dla dysku systemu operacyjnego są przypisywane z elementem osDisk. W przykładzie zdefiniowano nowych dysków zarządzanych w trybie buforowania, wartość **ReadWrite** i że dysk jest tworzony z [obrazu platformy](cli-ps-findimage.md):

```
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>Tworzenie nowych maszyn wirtualnych z istniejących dysków zarządzanych

Jeśli chcesz tworzyć maszyny wirtualne z istniejącej dysków, Usuń elementu imageReference i elementy osProfile i Definiuj następujące ustawienia dysku:

```
"osDisk": { 
  "osType": "Windows",
  "managedDisk": { 
    "id": "[resourceId('Microsoft.Compute/disks', [concat('myOSDisk', copyindex())])]" 
  }, 
  "caching": "ReadWrite",
  "createOption": "Attach" 
},
```

### <a name="create-new-virtual-machines-from-a-managed-image"></a>Tworzenie nowych maszyn wirtualnych z zarządzanego obrazu

Jeśli chcesz utworzyć maszynę wirtualną z do zarządzanego obrazu, zmień element elementu imageReference i Definiuj następujące ustawienia dysku:

```
"storageProfile": { 
  "imageReference": {
    "id": "[resourceId('Microsoft.Compute/images', 'myImage')]"
  },
  "osDisk": { 
    "name": "[concat('myOSDisk', copyindex())]",
    "osType": "Windows",
    "caching": "ReadWrite", 
    "createOption": "FromImage" 
  }
},
```

### <a name="attach-data-disks"></a>Dołącz dysków z danymi

Można opcjonalnie dodawać dysków z danymi z maszynami wirtualnymi. [Liczba dysków](sizes.md) zależy od rozmiaru dysku systemu operacyjnego, którego używasz. Maksymalna liczba dysków danych, które można dodać do nich o rozmiarze maszyn wirtualnych ustawioną Standard_DS1_v2 wynosi dwa. W tym przykładzie jeden dysk danych zarządzanych jest dodawany do każdej maszyny Wirtualnej:

```
"dataDisks": [
  {
    "name": "[concat('myDataDisk', copyindex())]",
    "diskSizeGB": "100",
    "lun": 0, 
    "caching": "ReadWrite",
    "createOption": "Empty"
  }
],
```

## <a name="extensions"></a>Rozszerzenia

Mimo że [rozszerzenia](extensions-features.md) są osobne zasobu, są ściśle powiązane maszyn wirtualnych. Rozszerzenia mogą być dodawane jako zasób podrzędnych maszyny wirtualnej lub jako osobne zasób. W przykładzie [rozszerzenia diagnostyki](extensions-diagnostics-template.md) dodawany do maszyn wirtualnych:

```
{ 
  "name": "Microsoft.Insights.VMDiagnosticsSettings", 
  "type": "extensions", 
  "location": "[resourceGroup().location]", 
  "apiVersion": "2016-03-30", 
  "dependsOn": [ 
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
  ], 
  "properties": { 
    "publisher": "Microsoft.Azure.Diagnostics", 
    "type": "IaaSDiagnostics", 
    "typeHandlerVersion": "1.5", 
    "autoUpgradeMinorVersion": true, 
    "settings": { 
      "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
      variables('wadmetricsresourceid'), 
      concat('myVM', copyindex()),
      variables('wadcfgxend')))]", 
      "storageAccount": "[variables('storageName')]" 
    }, 
    "protectedSettings": { 
      "storageAccountName": "[variables('storageName')]", 
      "storageAccountKey": "[listkeys(variables('accountid'), 
        '2015-06-15').key1]", 
      "storageAccountEndPoint": "https://core.windows.net" 
    } 
  } 
},
```

Tego rozszerzenia zasobu używa zmiennej storageName i zmienne diagnostycznych, aby zapewnić wartości. Jeśli chcesz zmienić dane, które są zbierane przez to rozszerzenie, możesz dodać więcej liczników wydajności do zmiennej wadperfcounters. Można również wybrać umieszczanie danych diagnostycznych do konta magazynu innego niż przechowywania dysków maszyny Wirtualnej.

Istnieje wiele rozszerzeń, które można zainstalować na maszynie Wirtualnej, ale najbardziej przydatne jest prawdopodobnie [niestandardowe rozszerzenie skryptu](extensions-customscript.md). W tym przykładzie skrypt programu PowerShell o nazwie start.ps1 działa na każdej maszynie Wirtualnej po pierwszym uruchomieniu:

```
{
  "name": "MyCustomScriptExtension",
  "type": "extensions",
  "apiVersion": "2016-03-30",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
  ],
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "[concat('https://', variables('storageName'),
          '.blob.core.windows.net/customscripts/start.ps1')]" 
      ],
      "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
    }
  }
}
```

Skrypt start.ps1 można wykonywać wiele zadań konfiguracyjnych. Na przykład dysków z danymi, które są dodawane do maszyn wirtualnych w tym przykładzie nie jest inicjowany; niestandardowego skryptu służy do ich inicjowania. Jeśli masz wiele zadań uruchamiania zrobić, można użyć pliku start.ps1 do wywołania inne skrypty programu PowerShell w magazynie Azure. W przykładzie użyto programu PowerShell, ale można użyć dowolnej metody skryptów, która jest dostępna w systemie operacyjnym, którego używasz.

Można wyświetlić stan zainstalowanych rozszerzeń z ustawień rozszerzenia w portalu:

![Pobierz stan rozszerzenia](./media/template-description/virtual-machines-show-extensions.png)

Można także uzyskać informacji o rozszerzeniu przy użyciu **Get-AzureRmVMExtension** polecenia programu PowerShell **get rozszerzenia maszyny wirtualnej** polecenia Azure CLI 2.0 lub **uzyskiwanie informacji o rozszerzeniu** INTERFEJS API REST.

## <a name="deployments"></a>Wdrożenia

Podczas wdrażania szablonu Azure śledzi zasoby wdrożone w grupie, a następnie automatycznie przypisuje nazwę do wdrożonej grupy. Nazwa wdrożenia jest taka sama jak nazwa szablonu.

Jeśli zastanawiasz się, jak stan zasobów we wdrożeniu, można użyć bloku grupy zasobów w portalu Azure:

![Uzyskaj informacje na temat wdrażania](./media/template-description/virtual-machines-deployment-info.png)
    
Nie jest problem do używania tego samego szablonu, aby utworzyć zasobów lub zaktualizować istniejące zasoby. Korzystając z polecenia do wdrażania szablonów, masz możliwość powiedzieć, które [tryb](../../resource-group-template-deploy.md) chcesz użyć. Tryb może być ustawiona jako **Complete** lub **przyrostowe**. Wartość domyślna to robić aktualizacji przyrostowych. Należy zachować ostrożność przy użyciu **Complete** tryb ponieważ przypadkowo usuniesz zasobami. Po ustawieniu trybu **Complete**, Menedżer zasobów usuwa wszystkie zasoby w grupie zasobów, które nie znajdują się w szablonie.

## <a name="next-steps"></a>Następne kroki

- Tworzenie własnych przy użyciu szablonu [szablonów Authoring Azure Resource Manager](../../resource-group-authoring-templates.md).
- Wdrażanie szablonu, który został utworzony przy użyciu [Utwórz maszynę wirtualną z systemem Windows przy użyciu szablonu usługi Resource Manager](ps-template.md).
- Dowiedz się, jak zarządzać maszynami wirtualnymi, które zostały utworzone, przeglądając [tworzenia i zarządzania maszynami wirtualnymi systemu Windows za pomocą modułu Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
