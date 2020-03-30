---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: jboeshart
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: jaboes
ms.custom: include file
ms.openlocfilehash: 126b488d2bb59e2904bee646301240efe6fe71a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76037999"
---
Ten dokument przechodzi przez różnice między dyskami zarządzanymi i niezarządzanymi podczas korzystania z szablonów usługi Azure Resource Manager do inicjowania obsługi administracyjnej maszyn wirtualnych. Przykłady pomagają zaktualizować istniejące szablony, które używają dysków niezarządzanych do dysków zarządzanych. Dla porównania, używamy szablonu [101-vm-simple-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) jako przewodnika. Możesz zobaczyć szablon przy użyciu zarówno [dysków zarządzanych,](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) jak i poprzedniej wersji przy użyciu [dysków niezarządzanych,](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) jeśli chcesz je bezpośrednio porównać.

## <a name="unmanaged-disks-template-formatting"></a>Formatowanie szablonu dysków niezarządzanych

Aby rozpocząć, przyjrzyjmy się, jak dyski niezarządzane są wdrażane. Podczas tworzenia dysków niezarządzanych, potrzebne jest konto magazynu do przechowywania plików VHD. Można utworzyć nowe konto magazynu lub użyć konta, które już istnieje. Z tego artykułu dowiesz się, jak utworzyć nowe konto magazynu. Utwórz zasób konta magazynu w bloku zasobów, jak pokazano poniżej.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[variables('storageAccountName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

W obiekcie maszyny wirtualnej dodaj zależność od konta magazynu, aby upewnić się, że jest ona tworzona przed maszyną wirtualną. W `storageProfile` sekcji określ pełny identyfikator URI lokalizacji VHD, który odwołuje się do konta magazynu i jest potrzebny dla dysku systemu operacyjnego i wszystkich dysków z danymi.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>Formatowanie szablonu dysków zarządzanych

Dzięki dyskom zarządzanym platformy Azure dysk staje się zasobem najwyższego poziomu i nie wymaga już konta magazynu, które ma zostać utworzone przez użytkownika. Dyski zarządzane zostały najpierw `2016-04-30-preview` ujawnione w wersji interfejsu API, są dostępne we wszystkich kolejnych wersjach interfejsu API i są teraz domyślnym typem dysku. W poniższych sekcjach przedstawiono ustawienia domyślne i szczegółowo opisano, jak jeszcze bardziej dostosować dyski.

> [!NOTE]
> Zaleca się użycie wersji interfejsu `2016-04-30-preview` API później niż `2016-04-30-preview` w `2017-03-30`przypadku przełomowych zmian między i .
>
>

### <a name="default-managed-disk-settings"></a>Domyślne ustawienia dysku zarządzanego

Aby utworzyć maszynę wirtualną z dyskami zarządzanymi, nie trzeba już tworzyć zasobu konta magazynu. Odwołując się do poniższego przykładu szablonu, istnieją pewne różnice w stosunku do poprzednich przykładów dysków niezarządzanych do zanotowania:

- Jest `apiVersion` to wersja, która obsługuje dyski zarządzane.
- `osDisk`i `dataDisks` nie odnoszą się już do określonego identyfikatora URI dla VHD.
- Podczas wdrażania bez określania dodatkowych właściwości, dysk użyje typu magazynu na podstawie rozmiaru maszyny Wirtualnej. Na przykład jeśli używasz rozmiaru maszyny Wirtualnej, który obsługuje magazyn w wersji premium (rozmiary z "s" w ich nazwie, takie jak Standard_D2s_v3), dyski w wersji premium zostaną skonfigurowane domyślnie. Można to zmienić za pomocą ustawienia sku dysku, aby określić typ magazynu.
- Jeśli nazwa dysku nie jest określona, `<VMName>_OsDisk_1_<randomstring>` przyjmuje format dysku `<VMName>_disk<#>_<randomstring>` systemu operacyjnego i dla każdego dysku danych.
  - Jeśli maszyna wirtualna jest tworzona na podstawie obrazu niestandardowego, domyślne ustawienia typu konta magazynu i nazwy dysku są pobierane z właściwości dysku zdefiniowanych w zasobie obrazu niestandardowego. Można je zastąpić, określając wartości dla nich w szablonie.
- Domyślnie szyfrowanie dysków platformy Azure jest wyłączone.
- Domyślnie buforowanie dysku to Odczyt/zapis dla dysku systemu operacyjnego i Brak dla dysków z danymi.
- W poniższym przykładzie nadal istnieje zależność konta magazynu, chociaż jest to tylko do przechowywania diagnostyki i nie jest potrzebne do przechowywania dysku.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="using-a-top-level-managed-disk-resource"></a>Korzystanie z zasobu dysku zarządzanego najwyższego poziomu

Jako alternatywę dla określenia konfiguracji dysku w obiekcie maszyny wirtualnej można utworzyć zasób dysku najwyższego poziomu i dołączyć go jako część tworzenia maszyny wirtualnej. Na przykład można utworzyć zasób dysku w następujący sposób, aby użyć go jako dysku danych.

```json
{
    "type": "Microsoft.Compute/disks",
    "apiVersion": "2018-06-01",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

W obiekcie maszyny Wirtualnej odwołaj się do obiektu dysku, który ma zostać dołączony. Określenie identyfikatora zasobu dysku zarządzanego `managedDisk` utworzonego we właściwości umożliwia przywiązanie dysku podczas tworzenia maszyny wirtualnej. Zasób `apiVersion` maszyny Wirtualnej `2017-03-30`jest ustawiony na . Zależność od zasobu dysku jest dodawany, aby upewnić się, że został pomyślnie utworzony przed utworzeniem maszyny Wirtualnej. 

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Tworzenie zarządzanych zestawów dostępności przy użyciu maszyn wirtualnych przy użyciu dysków zarządzanych

Aby utworzyć zarządzane zestawy dostępności z maszynami wirtualnymi `sku` przy użyciu dysków zarządzanych, `Aligned`dodaj obiekt do zasobu zestawu dostępności i ustaw właściwość na `name` . Ta właściwość zapewnia, że dyski dla każdej maszyny Wirtualnej są wystarczająco odizolowane od siebie, aby uniknąć pojedynczych punktów awarii. Należy również `apiVersion` zauważyć, że dla zasobu `2018-10-01`zestawu dostępności jest ustawiona na .

```json
{
    "type": "Microsoft.Compute/availabilitySets",
    "apiVersion": "2018-10-01",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 3,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

### <a name="standard-ssd-disks"></a>Standardowe dyski SSD

Poniżej znajdują się parametry potrzebne w szablonie Menedżera zasobów do tworzenia standardowych dysków SSD:

* *apiVersion* for Microsoft.Compute musi `2018-04-01` być ustawiony jako (lub nowszy)
* Określ *typ konta managedDisk.storageAccounttype* jako`StandardSSD_LRS`

W poniższym przykładzie przedstawiono sekcję *properties.storageProfile.osDisk* dla maszyny Wirtualnej korzystającej ze standardowych dysków SSD:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Aby uzyskać pełny przykład szablonu tworzenia standardowego dysku SSD z szablonem, zobacz [Tworzenie maszyny wirtualnej z obrazu systemu Windows ze standardowymi dyskami danych SSD](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

### <a name="additional-scenarios-and-customizations"></a>Dodatkowe scenariusze i dostosowania

Aby znaleźć pełne informacje na temat specyfikacji interfejsu API REST, zapoznaj się z [dokumentacją interfejsu API interfejsu REST na dysku zarządzanym.](/rest/api/manageddisks/disks/disks-create-or-update) Znajdziesz dodatkowe scenariusze, a także domyślne i dopuszczalne wartości, które można przesłać do interfejsu API za pośrednictwem wdrożeń szablonów. 

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać pełne szablony korzystające z dysków zarządzanych, odwiedź następujące łącza usługi Szybki start usługi Szybki start.
    * [Maszyna wirtualna systemu Windows z dyskiem zarządzanym](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Maszyna wirtualna systemu Linux z dyskiem zarządzanym](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* Odwiedź dokument [Omówienie dysków zarządzanych platformy Azure,](../articles/virtual-machines/windows/managed-disks-overview.md) aby dowiedzieć się więcej o dyskach zarządzanych.
* Przejrzyj dokumentację referencyjną szablonu dla zasobów maszyny wirtualnej, odwiedzając dokument [referencyjny szablonu Microsoft.Compute/virtualMachines.](/azure/templates/microsoft.compute/virtualmachines)
* Przejrzyj dokumentację referencyjną szablonu dla zasobów dyskowych, odwiedzając dokument [referencyjny szablonu Microsoft.Compute/disks.](/azure/templates/microsoft.compute/disks)
* Aby uzyskać informacje na temat używania dysków zarządzanych w zestawach skalowania maszyny wirtualnej platformy Azure, odwiedź dokument [Użyj dysków danych z zestawami skalowania.](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks)
