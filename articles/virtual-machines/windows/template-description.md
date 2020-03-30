---
title: Maszyny wirtualne w szablonie usługi Azure Resource Manager | Microsoft Azure
description: Dowiedz się więcej o definiowaniu zasobu maszyny wirtualnej w szablonie usługi Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: f63ab5cc-45b8-43aa-a4e7-69dc42adbb99
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/03/2019
ms.author: cynthn
ms.openlocfilehash: c9bf1cf0564655c932e066e5b74225382375e9c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235417"
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Maszyny wirtualne w szablonie usługi Azure Resource Manager

W tym artykule opisano aspekty szablonu usługi Azure Resource Manager, które mają zastosowanie do maszyn wirtualnych. W tym artykule nie opisano pełnego szablonu do tworzenia maszyny wirtualnej; do tego potrzebne są definicje zasobów dla kont magazynu, interfejsów sieciowych, publicznych adresów IP i sieci wirtualnych. Aby uzyskać więcej informacji na temat sposobu definiowania tych zasobów razem, zobacz przewodnik [po szablonie Menedżera zasobów](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Istnieje wiele [szablonów w galerii,](https://azure.microsoft.com/documentation/templates/?term=VM) które zawierają zasób maszyny Wirtualnej. Nie wszystkie elementy, które mogą być zawarte w szablonie są opisane w tym miejscu.

 

W tym przykładzie pokazano typową sekcję zasobów szablonu do tworzenia określonej liczby maszyn wirtualnych:

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
>W tym przykładzie opiera się na koncie magazynu, który został wcześniej utworzony. Konto magazynu można utworzyć, wdrażając je z szablonu. W przykładzie opiera się również na interfejsie sieciowym i jego zasobach zależnych, które zostaną zdefiniowane w szablonie. Te zasoby nie są wyświetlane w przykładzie.
>
>

## <a name="api-version"></a>Wersja interfejsu API

Podczas wdrażania zasobów przy użyciu szablonu, należy określić wersję interfejsu API do użycia. W przykładzie pokazano zasób maszyny wirtualnej przy użyciu tego elementu apiVersion:

```json
"apiVersion": "2016-04-30-preview",
```

Wersja interfejsu API określona w szablonie ma wpływ na właściwości, które można zdefiniować w szablonie. Ogólnie rzecz biorąc należy wybrać najnowszą wersję interfejsu API podczas tworzenia szablonów. W przypadku istniejących szablonów można zdecydować, czy chcesz kontynuować korzystanie z wcześniejszej wersji interfejsu API, czy zaktualizować szablon dla najnowszej wersji, aby korzystać z nowych funkcji.

Skorzystaj z tych możliwości, aby uzyskać najnowsze wersje interfejsu API:

- INTERFEJS API REST — [lista wszystkich dostawców zasobów](https://docs.microsoft.com/rest/api/resources/providers)
- PowerShell - [Get-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/get-azresourceprovider)
- Azure CLI — [pokaz dostawcy az](https://docs.microsoft.com/cli/azure/provider)


## <a name="parameters-and-variables"></a>Parametry i zmienne

[Parametry](../../resource-group-authoring-templates.md) ułatwiają określenie wartości szablonu po jego uruchomieniu. Ta sekcja parametrów jest używana w przykładzie:

```json
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

Podczas wdrażania przykładowego szablonu należy wprowadzić wartości nazwy i hasła konta administratora na każdej maszynie wirtualnej i liczby maszyn wirtualnych do utworzenia. Można określić wartości parametrów w oddzielnym pliku, który jest zarządzany za pomocą szablonu lub podać wartości po wyświetleniu monitu.

[Zmienne](../../resource-group-authoring-templates.md) ułatwiają konfigurowanie wartości w szablonie, które są wielokrotnie używane w całym nim lub które mogą się zmieniać w czasie. Ta sekcja zmiennych jest używana w przykładzie:

```json
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

Podczas wdrażania przykładowego szablonu wartości zmiennych są używane dla nazwy i identyfikatora wcześniej utworzonego konta magazynu. Zmienne są również używane do zapewnienia ustawień rozszerzenia diagnostycznego. Skorzystaj z [najlepszych rozwiązań dotyczących tworzenia szablonów usługi Azure Resource Manager,](../../resource-manager-template-best-practices.md) aby ułatwić podjęcie decyzji o sposobie struktury parametrów i zmiennych w szablonie.

## <a name="resource-loops"></a>Pętle zasobów

Jeśli potrzebujesz więcej niż jednej maszyny wirtualnej dla aplikacji, można użyć elementu kopiowania w szablonie. Ten opcjonalny element pętli poprzez tworzenie liczby maszyn wirtualnych, które zostały określone jako parametr:

```json
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

Ponadto należy zauważyć w przykładzie, że indeks pętli jest używany podczas określania niektórych wartości dla zasobu. Na przykład, jeśli wprowadzono liczbę wystąpień trzech, nazwy dysków systemu operacyjnego to myOSDisk1, myOSDisk2 i myOSDisk3:

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
}
```

> [!NOTE] 
>W tym przykładzie użyto dysków zarządzanych dla maszyn wirtualnych.
>
>

Należy pamiętać, że tworzenie pętli dla jednego zasobu w szablonie może wymagać użycia pętli podczas tworzenia lub uzyskiwania dostępu do innych zasobów. Na przykład wiele maszyn wirtualnych nie można użyć tego samego interfejsu sieciowego, więc jeśli szablon pętli poprzez tworzenie trzech maszyn wirtualnych musi również pętli poprzez tworzenie trzech interfejsów sieciowych. Podczas przypisywania interfejsu sieciowego do maszyny Wirtualnej indeks pętli służy do jej identyfikowania:

```json
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Zależności

Większość zasobów zależy od innych zasobów do poprawnego działania. Maszyny wirtualne muszą być skojarzone z siecią wirtualną i aby to zrobić, potrzebuje interfejsu sieciowego. Element [dependsOn](../../resource-group-define-dependencies.md) jest używany, aby upewnić się, że interfejs sieciowy jest gotowy do użycia przed utworzeniem maszyn wirtualnych:

```json
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

Menedżer zasobów wdraża równolegle wszystkie zasoby, które nie są zależne od innego zasobu wdrażane. Należy zachować ostrożność podczas ustawiania zależności, ponieważ można przypadkowo spowolnić wdrożenie, określając niepotrzebne zależności. Zależności można łańcuch za pośrednictwem wielu zasobów. Na przykład interfejs sieciowy zależy od publicznego adresu IP i zasobów sieci wirtualnej.

Skąd wiesz, czy wymagana jest zależność? Spójrz na wartości ustawione w szablonie. Jeśli element w definicji zasobów maszyny wirtualnej wskazuje na inny zasób, który jest wdrażany w tym samym szablonie, potrzebujesz zależności. Na przykład przykładowa maszyna wirtualna definiuje profil sieciowy:

```json
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

Aby ustawić tę właściwość, interfejs sieciowy musi istnieć. W związku z tym należy zależności. Należy również ustawić zależność, gdy jeden zasób (element podrzędny) jest zdefiniowany w innym zasobie (element nadrzędny). Na przykład ustawienia diagnostyczne i rozszerzenia skryptów niestandardowych są zdefiniowane jako zasoby podrzędne maszyny wirtualnej. Nie można ich utworzyć, dopóki nie istnieje maszyna wirtualna. W związku z tym oba zasoby są oznaczone jako zależne od maszyny wirtualnej.

## <a name="profiles"></a>Profile

Podczas definiowania zasobu maszyny wirtualnej jest używanych kilka elementów profilu. Niektóre są wymagane, a niektóre są opcjonalne. Na przykład hardwareProfile, osProfile, storageProfile i networkProfile elementy są wymagane, ale diagnostykaProfile jest opcjonalne. Profile te definiują ustawienia, takie jak:
   
- [Rozmiar](sizes.md)
- [nazwa](/azure/architecture/best-practices/resource-naming) i poświadczenia
- ustawienia dysku i [systemu operacyjnego](cli-ps-findimage.md)
- [interfejs sieciowy](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md) 
- diagnostyka rozruchu

## <a name="disks-and-images"></a>Dyski i obrazy
   
Na platformie Azure pliki vhd mogą reprezentować [dyski lub obrazy](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Gdy system operacyjny w pliku vhd jest wyspecjalizowany jako określona maszyna wirtualna, jest określana jako dysk. Gdy system operacyjny w pliku vhd jest uogólniony do tworzenia wielu maszyn wirtualnych, jest określany jako obraz.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Tworzenie nowych maszyn wirtualnych i nowych dysków na podstawie obrazu platformy

Podczas tworzenia maszyny Wirtualnej, należy zdecydować, jaki system operacyjny do użycia. ImageReference element służy do definiowania systemu operacyjnego nowej maszyny Wirtualnej. W przykładzie przedstawiono definicję systemu operacyjnego Windows Server:

```json
"imageReference": { 
  "publisher": "MicrosoftWindowsServer", 
  "offer": "WindowsServer", 
  "sku": "2012-R2-Datacenter", 
  "version": "latest" 
},
```

Jeśli chcesz utworzyć system operacyjny Linux, możesz użyć tej definicji:

```json
"imageReference": {
  "publisher": "Canonical",
  "offer": "UbuntuServer",
  "sku": "14.04.2-LTS",
  "version": "latest"
},
```

Ustawienia konfiguracji dysku systemu operacyjnego są przypisywane z elementem osDisk. W przykładzie zdefiniowano nowy dysk zarządzany z trybem buforowania ustawionym na **ReadWrite** i że dysk jest tworzony na podstawie [obrazu platformy:](cli-ps-findimage.md)

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>Tworzenie nowych maszyn wirtualnych z istniejących dysków zarządzanych

Jeśli chcesz utworzyć maszyny wirtualne z istniejących dysków, usuń imageReference i osProfile elementów i zdefiniuj następujące ustawienia dysku:

```json
"osDisk": { 
  "osType": "Windows",
  "managedDisk": { 
    "id": "[resourceId('Microsoft.Compute/disks', [concat('myOSDisk', copyindex())])]" 
  }, 
  "caching": "ReadWrite",
  "createOption": "Attach" 
},
```

### <a name="create-new-virtual-machines-from-a-managed-image"></a>Tworzenie nowych maszyn wirtualnych na podstawie zarządzanego obrazu

Jeśli chcesz utworzyć maszynę wirtualną z obrazu zarządzanego, zmień element imageReference i zdefiniuj następujące ustawienia dysku:

```json
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

### <a name="attach-data-disks"></a>Dołączanie dysków z danymi

Opcjonalnie można dodać dyski danych do maszyn wirtualnych. [Liczba dysków](sizes.md) zależy od rozmiaru używanego dysku systemu operacyjnego. Przy rozmiarze maszyn wirtualnych ustawionym na Standard_DS1_v2 maksymalna liczba dysków danych, które mogą zostać dodane do nich, wynosi dwa. W przykładzie jeden zarządzany dysk danych jest dodawany do każdej maszyny Wirtualnej:

```json
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

Mimo że [rozszerzenia](extensions-features.md) są oddzielnym zasobem, są one ściśle powiązane z maszynami wirtualnymi. Rozszerzenia mogą być dodawane jako zasób podrzędny maszyny Wirtualnej lub jako oddzielny zasób. W przykładzie pokazano [rozszerzenie diagnostyki](extensions-diagnostics-template.md) dodawane do maszyn wirtualnych:

```json
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

Ten zasób rozszerzenia używa zmiennej storageName i zmiennych diagnostycznych w celu zapewnienia wartości. Jeśli chcesz zmienić dane, które są zbierane przez to rozszerzenie, można dodać więcej liczników wydajności do zmiennej wadperfcounters. Można również umieścić dane diagnostyczne na innym koncie magazynu niż gdzie dyski maszyn wirtualnych są przechowywane.

Istnieje wiele rozszerzeń, które można zainstalować na maszynie wirtualnej, ale najbardziej przydatne jest prawdopodobnie [niestandardowe rozszerzenie skryptu](extensions-customscript.md). W tym przykładzie skrypt programu PowerShell o nazwie start.ps1 jest uruchamiany na każdej maszynie wirtualnej po pierwszym uruchomieniu:

```json
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

Skrypt start.ps1 może wykonywać wiele zadań konfiguracyjnych. Na przykład dyski danych, które są dodawane do maszyn wirtualnych w przykładzie nie są inicjowane; można użyć skryptu niestandardowego, aby je zainicjować. Jeśli masz wiele zadań uruchamiania do wykonania, można użyć pliku start.ps1 do wywołania innych skryptów programu PowerShell w magazynie platformy Azure. W przykładzie użyto programu PowerShell, ale można użyć dowolnej metody skryptów, która jest dostępna w systemie operacyjnym, którego używasz.

Stan zainstalowanych rozszerzeń można sprawdzić w ustawieniach rozszerzeń w portalu:

![Uzyskaj stan rozszerzenia](./media/template-description/virtual-machines-show-extensions.png)

Informacje o rozszerzeniu można również uzyskać za pomocą polecenia **Get-AzVMExtension** PowerShell, **rozszerzenia maszyny wirtualnej uzyskać** polecenie interfejsu wiersza polecenia platformy Azure lub interfejsu API REST informacji o **rozszerzeniu.**

## <a name="deployments"></a>Wdrożenia

Podczas wdrażania szablonu platforma Azure śledzi zasoby wdrożone jako grupa i automatycznie przypisuje nazwę do tej wdrożonej grupy. Nazwa wdrożenia jest taka sama jak nazwa szablonu.

Jeśli jesteś ciekaw stanu zasobów we wdrożeniu, wyświetl grupę zasobów w witrynie Azure portal:

![Uzyskaj informacje o wdrażaniu](./media/template-description/virtual-machines-deployment-info.png)
    
Nie jest problemem użycie tego samego szablonu do tworzenia zasobów lub aktualizowania istniejących zasobów. Podczas wdrażania szablonów za pomocą poleceń można powiedzieć, którego [trybu](../../resource-group-template-deploy.md) chcesz użyć. Tryb można ustawić na **Ukończony** lub **Przyrostowy**. Domyślnie jest do aktualizacji przyrostowych. Należy zachować ostrożność podczas korzystania z trybu **Complete,** ponieważ możesz przypadkowo usunąć zasoby. Po ustawieniu trybu **na Zakończ**Menedżer zasobów usuwa wszystkie zasoby w grupie zasobów, których nie ma w szablonie.

## <a name="next-steps"></a>Następne kroki

- Utwórz własny szablon przy użyciu [szablonów usługi Azure Resource Manager.](../../resource-group-authoring-templates.md)
- Wdrażanie szablonu utworzonego przy użyciu [funkcji Utwórz maszynę wirtualną systemu Windows z szablonem Menedżera zasobów](ps-template.md).
- Dowiedz się, jak zarządzać maszynami wirtualnymi utworzonymi przez przeglądanie [maszyn wirtualnych systemu Windows i zarządzanie nimi za pomocą modułu Programu Azure PowerShell.](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
- Aby zapoznać się ze składnią JSON i właściwościami typów zasobów w szablonach, zobacz [Odwołanie do szablonu usługi Azure Resource Manager](/azure/templates/).
