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
ms.date: 01/03/2019
ms.author: cynthn
ms.openlocfilehash: f4a69fd57bf2e9535fdb1664365b074927e27585
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64707254"
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Maszyny wirtualne w szablonie usługi Azure Resource Manager

W tym artykule opisano aspekty szablonu usługi Azure Resource Manager, które są stosowane do maszyn wirtualnych. W tym artykule nie opisano kompletny szablon, podczas tworzenia maszyny wirtualnej; w tym należy definicji zasobu dla konta magazynu, interfejsy sieciowe, publiczne adresy IP i sieci wirtualnych. Aby uzyskać więcej informacji na temat sposobu te zasoby można zdefiniować ze sobą, zobacz [Przewodnik po szablonie usługi Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Istnieje wiele [szablonów w galerii](https://azure.microsoft.com/documentation/templates/?term=VM) zawierające zasób maszyny Wirtualnej. Nie wszystkie elementy, które mogły zostać uwzględnione w szablonie są opisane poniżej.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

W tym przykładzie pokazano sekcję typowe zasobu szablon służący do tworzenia ciągu określonej liczby maszyn wirtualnych:

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
>W tym przykładzie opiera się na konto magazynu, który został wcześniej utworzony. Konto magazynu można utworzyć przez wdrożenie jej z szablonu. Przykład korzysta również interfejs sieciowy i jego zasobów zależnych, zdefiniowane w szablonie. Te zasoby nie są wyświetlane w przykładzie.
>
>

## <a name="api-version"></a>Wersja interfejsu API

Podczas wdrażania zasobów przy użyciu szablonu, należy określić wersję interfejsu API do użycia. W przykładzie pokazano zasobu maszyny wirtualnej przy użyciu tego elementu apiVersion:

```
"apiVersion": "2016-04-30-preview",
```

Wersja interfejsu API, określ w szablonie ma wpływ na właściwości, które można zdefiniować w szablonie. Ogólnie rzecz biorąc należy wybrać najbardziej aktualną wersję interfejsu API podczas tworzenia szablonów. Dla istniejących szablonów można zdecydować, czy chcesz nadal używać starszej wersji interfejsu API lub zaktualizować szablon do najnowszej wersji, aby móc korzystać z nowych funkcji.

Użyj tych możliwości w celu uzyskania najnowszej wersji interfejsu API:

- Interfejs API REST — [listy wszystkich dostawców zasobów](https://docs.microsoft.com/rest/api/resources/providers)
- PowerShell — [Get AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/get-azresourceprovider)
- Interfejs wiersza polecenia Azure - [az provider show](https://docs.microsoft.com/cli/azure/provider)


## <a name="parameters-and-variables"></a>Parametry i zmienne

[Parametry](../../resource-group-authoring-templates.md) ułatwiają określenie wartości dla szablonu, po uruchomieniu go. W tej sekcji parametrów jest używany w przykładzie:

```        
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

Gdy wdrożysz przykładowy szablon, można wprowadzić wartości nazwy i hasła konta administratora na każdej maszynie Wirtualnej i liczbę maszyn wirtualnych, aby utworzyć. Masz możliwość określania wartości parametrów w oddzielnym pliku, który jest zarządzany przy użyciu szablonu lub podając wartości po wyświetleniu monitu.

[Zmienne](../../resource-group-authoring-templates.md) ułatwiają konfigurowanie wartości w szablonie, które są używane wielokrotnie w całym go lub które mogą ulec zmianie. Ta sekcja zmiennych jest używana w przykładzie:

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

Podczas wdrażania przykładowy szablon wartości zmiennych są używane dla nazwy i identyfikatora poprzednio utworzonego konta magazynu. Zmienne są również używane, aby określić ustawienia dla rozszerzenia diagnostycznego. Użyj [najlepsze rozwiązania dotyczące tworzenia szablonów usługi Azure Resource Manager](../../resource-manager-template-best-practices.md) ułatwią Ci zdecydować, jak struktury, parametry i zmienne w szablonie.

## <a name="resource-loops"></a>Pętle zasobów

Jeśli potrzebujesz więcej niż jednej maszyny wirtualnej dla aplikacji, można użyć elementu kopii w szablonie. Ten opcjonalny element pętli tworzenia wielu maszyn wirtualnych, które określony jako parametr:

```
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

Zauważ również, w przykładzie, indeks pętli jest używana podczas określania niektóre wartości dla zasobu. Na przykład jeśli wprowadzono liczbę wystąpień jako trzy nazwy dysków systemu operacyjnego są myOSDisk1 myOSDisk2 i myOSDisk3:

```
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
}
```

> [!NOTE] 
>W tym przykładzie korzysta z dysków zarządzanych dla maszyn wirtualnych.
>
>

Należy pamiętać, że tworzenie pętli dla jednego zasobu w szablonie mogą wymagać używania pętli, podczas tworzenia lub uzyskiwania dostępu do innych zasobów. Na przykład wiele maszyn wirtualnych nie można użyć tego samego interfejsu sieciowego, więc jeśli szablon pętli tworzenie trzech maszyn wirtualnych należy również pętli proces tworzenia trzy interfejsy sieciowe. Podczas przypisywania interfejsu sieciowego do maszyny Wirtualnej, indeks pętli jest używany do identyfikowania:

```
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Zależności

Większość zasobów są zależne od innych zasobów, aby działać poprawnie. Maszyny wirtualne muszą być skojarzone z siecią wirtualną i celu, że wymaga ona interfejsu sieciowego. [DependsOn](../../resource-group-define-dependencies.md) element jest używany, aby upewnić się, że interfejs sieciowy jest gotowa do użycia, przed utworzeniem maszyn wirtualnych:

```
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

Resource Manager wdrożyła je w sposób równoległy wszystkie zasoby, które nie są zależne od innego zasobu wdrożenia. Należy zachować ostrożność podczas ustawiania zależności, ponieważ może przypadkowo spowalniać wdrożenia, określając niepotrzebne zależności. Zależności można połączyć w łańcuch za pomocą wielu zasobów. Na przykład interfejs sieciowy jest zależny od publicznego adresu IP i zasoby sieci wirtualnej.

Skąd wiadomo, czy zależność jest wymagana? Przyjrzyj się wartości, które są zdefiniowane w szablonie. Jeśli element w punktach definicji zasobu maszyny wirtualnej do innego zasobu, który jest wdrożony w tym samym szablonie, konieczne będzie zależności. Na przykład maszyny wirtualnej przykład definiuje profilu sieciowego:

```
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

Aby ustawić tę właściwość, musi istnieć interfejs sieciowy. Dlatego konieczne jest zależność. Należy również ustawić zależność, gdy jeden zasób (element podrzędny) zdefiniowano w ramach innego zasobu (nadrzędnego). Na przykład ustawienia diagnostyczne i rozszerzenia niestandardowego skryptu są definiowane jako zasoby podrzędne maszyny wirtualnej. Nie można utworzyć do momentu maszyna wirtualna istnieje. W związku z tym oba zasoby są oznaczone jako zależny od maszyny wirtualnej.

## <a name="profiles"></a>Profile

Niektóre elementy profilu są używane podczas definiowania zasobu maszyny wirtualnej. Niektóre są wymagane, a niektóre są opcjonalne. Na przykład — obiekt hardwareProfile osProfile, storageProfile oraz elementy networkProfile elementy są wymagane, ale diagnosticsProfile jest opcjonalne. Te profile definiują ustawienia, takie jak:
   
- [Rozmiar](sizes.md)
- [Nazwa](/azure/architecture/best-practices/naming-conventions) i poświadczeń
- dysk i [ustawień systemu operacyjnego](cli-ps-findimage.md)
- [Interfejs sieciowy](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md) 
- Diagnostyka rozruchu

## <a name="disks-and-images"></a>Dyski i obrazy
   
Na platformie Azure, pliki wirtualnego dysku twardego może reprezentować [dysków lub obrazów](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). System operacyjny w pliku wirtualnego dysku twardego jest przeznaczone do określonej maszyny Wirtualnej, jest określany na dysk. Plik wirtualnego dysku twardego systemu operacyjnego jest uogólniony, aby służyć do tworzenia wielu maszyn wirtualnych, jest określany na jako obraz.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Tworzenie nowych maszyn wirtualnych i nowych dysków na podstawie obrazu platformy

Podczas tworzenia maszyny Wirtualnej, należy zdecydować, jakiego systemu operacyjnego do użycia. Elementu imageReference służy do definiowania systemu operacyjnego nowej maszyny Wirtualnej. W przykładzie pokazano definicję dla systemu operacyjnego Windows Server:

```
"imageReference": { 
  "publisher": "MicrosoftWindowsServer", 
  "offer": "WindowsServer", 
  "sku": "2012-R2-Datacenter", 
  "version": "latest" 
},
```

Jeśli chcesz utworzyć systemu operacyjnego Linux, możesz użyć tej definicji:

```
"imageReference": {
  "publisher": "Canonical",
  "offer": "UbuntuServer",
  "sku": "14.04.2-LTS",
  "version": "latest"
},
```

Ustawienia konfiguracji dla dysku systemu operacyjnego są przypisywane przy użyciu elementu osDisk. W przykładzie zdefiniowano nowego dysku zarządzanego w trybie buforowania równa **ReadWrite** oraz że dysk jest tworzony z [obrazu platformy](cli-ps-findimage.md):

```
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>Tworzenie nowych maszyn wirtualnych na podstawie istniejących dysków zarządzanych

Jeśli chcesz utworzyć maszyny wirtualne z istniejących dysków, Usuń imageReference i elementy typu osProfile, a Definiuj następujące ustawienia dysku:

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

### <a name="create-new-virtual-machines-from-a-managed-image"></a>Tworzenie nowych maszyn wirtualnych na podstawie obrazu zarządzanego

Jeśli chcesz utworzyć maszynę wirtualną z obrazu zarządzanego Zmienianie elementu imageReference i Definiuj następujące ustawienia dysku:

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

### <a name="attach-data-disks"></a>Dołączanie dysków danych

Możesz opcjonalnie dodać dyski z danymi do maszyn wirtualnych. [Liczbę dysków](sizes.md) zależy od rozmiaru dysku systemu operacyjnego, którego używasz. Maksymalna liczba dysków z danymi, które można dodać do nich jest rozmiar maszyn wirtualnych, ustaw na maszynie standardowa_ds1_v2 dwa. W tym przykładzie jednego zarządzanego dysku danych jest dodawany do każdej maszyny Wirtualnej:

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

Mimo że [rozszerzenia](extensions-features.md) są osobnego zasobu, są ściśle powiązane maszyn wirtualnych. Rozszerzenia można dodać jako zasób podrzędny maszyny wirtualnej lub jako osobny zasób. W przykładzie pokazano [rozszerzenie diagnostyki](extensions-diagnostics-template.md) dodawany do maszyn wirtualnych:

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

Ten zasób rozszerzenia używa zmiennej storageName i diagnostycznych zmiennych w celu zapewnienia wartości. Jeśli chcesz zmienić dane gromadzone przez to rozszerzenie, należy dodać większej liczby liczników wydajności do zmiennej wadperfcounters. Można także umieścić dane diagnostyczne do konta magazynu innego niż przechowywania dysków maszyny Wirtualnej.

Istnieje wiele rozszerzeń, które można zainstalować na maszynie Wirtualnej, ale najbardziej przydatne prawdopodobnie [rozszerzenia niestandardowego skryptu](extensions-customscript.md). W tym przykładzie skrypt programu PowerShell o nazwie start.ps1 działa na każdej maszynie Wirtualnej podczas pierwszego uruchomienia:

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

Skrypt start.ps1 można wykonywać wiele zadań konfiguracyjnych. Na przykład dysków z danymi, które są dodawane do maszyn wirtualnych w tym przykładzie nie są inicjowane; można użyć niestandardowego skryptu do ich inicjowania. Jeśli masz wiele zadań uruchamiania, aby zrobić, można użyć pliku start.ps1 do wywoływania innych skryptów programu PowerShell w usłudze Azure storage. W przykładzie użyto programu PowerShell, ale można użyć dowolnej metody obsługi skryptów, która jest dostępna w systemie operacyjnym, którego używasz.

Można wyświetlić stan zainstalowanych rozszerzeń z ustawień rozszerzenia w portalu:

![Pobierz stan rozszerzenia](./media/template-description/virtual-machines-show-extensions.png)

Można również uzyskać informacji o rozszerzeniu za pomocą **Get AzVMExtension** polecenia programu PowerShell **get rozszerzenia maszyny wirtualnej** polecenia wiersza polecenia platformy Azure lub **uzyskiwanie informacji o rozszerzeniu**Interfejsu API REST.

## <a name="deployments"></a>Wdrożenia

Podczas wdrażania szablonu usługi Azure śledzi zasoby wdrożone w grupie, a następnie automatycznie przypisuje nazwę do wdrożonej grupy. Nazwa wdrożenia jest taka sama jak nazwa szablonu.

Jeśli zastanawiasz się, stan zasobów w ramach wdrożenia, należy wyświetlić grupę zasobów w witrynie Azure portal:

![Uzyskaj informacje na temat wdrażania](./media/template-description/virtual-machines-deployment-info.png)
    
Nie jest problemem, aby użyć tego samego szablonu do tworzenia zasobów lub zaktualizować istniejące zasoby. Korzystając z poleceń wdrażania szablonów, masz możliwość powiedzieć, który [tryb](../../resource-group-template-deploy.md) chcesz użyć. Tryb może być ustawiony na **Complete** lub **przyrostowe**. Wartość domyślna to w celu aktualizacji przyrostowych. Należy zachować ostrożność przy użyciu **Complete** tryb ponieważ przypadkowo usuniesz zasobów. Po ustawieniu trybu na **Complete**, Menedżer zasobów usuwa wszystkie zasoby w grupie zasobów, które nie są w szablonie.

## <a name="next-steps"></a>Następne kroki

- Utwórz własny szablon przy użyciu [tworzenia usługi Azure Resource Manager](../../resource-group-authoring-templates.md).
- Wdrażanie szablonu, które zostały utworzone za pomocą [Utwórz maszynę wirtualną Windows przy użyciu szablonu usługi Resource Manager](ps-template.md).
- Dowiedz się, jak zarządzanie maszynami wirtualnymi, które zostały utworzone, przeglądając [Utwórz Windows maszyn wirtualnych i zarządzanie przy użyciu modułu Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Właściwości typów zasobów w szablonach i składnię JSON, zobacz [odwołanie do szablonu usługi Azure Resource Manager](/azure/templates/).
