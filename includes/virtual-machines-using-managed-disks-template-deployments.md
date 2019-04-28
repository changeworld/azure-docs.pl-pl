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
ms.openlocfilehash: 6085eb2b520217c4e678a75032e8a1cb4b9343ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60581094"
---
# <a name="using-managed-disks-in-azure-resource-manager-templates"></a>Za pomocą usługi Managed Disks w szablonach usługi Azure Resource Manager

W tym dokumencie przedstawiono różnice między zarządzanymi i niezarządzanymi dyskami aprowizację maszyn wirtualnych za pomocą szablonów usługi Azure Resource Manager. Przykłady ułatwiają aktualizowanie istniejących szablonów, które korzystają z dysków niezarządzanych do dysków zarządzanych. Odwołanie, używamy [101-maszyny wirtualnej — prosty — windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) szablonu jako wskazówki. Możesz zobaczyć szablon przy użyciu zarówno [usługi managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) i poprzedniej wersji przy użyciu [niezarządzane dyski](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) Jeśli chcesz je bezpośrednio porównywać.

## <a name="unmanaged-disks-template-formatting"></a>Formatowanie szablonu dysków niezarządzanych

Aby rozpocząć, możemy take wygląd, w jaki sposób usługa unmanaged disks są wdrażane. Podczas tworzenia dysków niezarządzanych, potrzebne jest konto magazynu do przechowywania plików wirtualnego dysku twardego. Można utworzyć nowe konto magazynu lub użyj przepływu, który już istnieje. W tym artykule pokazano, jak utworzyć nowe konto magazynu. Utwórz zasób konta magazynu w bloku zasobów, jak pokazano poniżej.

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

W ramach obiektu maszyny wirtualnej należy dodać zależność od konta magazynu, aby upewnić się, czy został utworzony przed maszyny wirtualnej. W ramach `storageProfile` sekcji podaj pełny identyfikator URI lokalizacja wirtualnego dysku twardego, który odwołuje się do konta magazynu i nie jest wymagana dla dysku systemu operacyjnego i dysków z danymi.

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

## <a name="managed-disks-template-formatting"></a>Zarządzane dyski szablonu formatowania

Za pomocą usługi Azure Managed Disks dysk stanie się zasobem najwyższego poziomu i nie wymaga już konto magazynu ma zostać utworzony przez użytkownika. Dyski zarządzane najpierw zostały ujawnione w `2016-04-30-preview` wersji interfejsu API są dostępne we wszystkich kolejnych wersjach interfejsu API i są teraz domyślny typ dysku. W poniższych sekcjach opisano domyślne ustawienia i szczegółowo opisują jak dalej dostosowywać dysków.

> [!NOTE]
> Zalecane jest użycie wersji interfejsu API nowsze niż `2016-04-30-preview` jako wprowadzono istotne zmiany między `2016-04-30-preview` i `2017-03-30`.
>
>

### <a name="default-managed-disk-settings"></a>Domyślne ustawienia dysku zarządzanego

Aby utworzyć Maszynę wirtualną z dyskami zarządzanymi, nie potrzebujesz już do utworzenia magazynu konta zasobu, a następnie można zaktualizować zasobu maszyny wirtualnej w następujący sposób. W szczególności należy pamiętać, że `apiVersion` odzwierciedla `2017-03-30` i `osDisk` i `dataDisks` nie będzie odwoływać się do określonego identyfikatora URI dysku VHD. W przypadku wdrażania bez określenia dodatkowych właściwości, dysk użyje typu magazynu, w zależności od rozmiaru maszyny Wirtualnej. Na przykład jeśli używasz Premium stanie rozmiar maszyny Wirtualnej (rozmiary za pomocą "s" w ich imieniu, takie jak Standard_D2s_v3) system użyje Premium_LRS magazynu. Użyj ustawienia jednostki sku dysku, aby określić typ magazynu. Jeśli nazwa nie zostanie określona, zajmuje format `<VMName>_OsDisk_1_<randomstring>` dla dysku systemu operacyjnego i `<VMName>_disk<#>_<randomstring>` dla każdego dysku danych. Domyślnie usługa Azure disk encryption jest wyłączony; buforowanie jest odczytu/zapisu dla dysku systemu operacyjnego i brak w przypadku dysków z danymi. W poniższym przykładzie może Zauważ, że nadal ma zależy od konta magazynu, mimo że to jest tylko do przechowywania diagnostyki i nie jest wymagany dla magazynu danych na dysku.

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

### <a name="using-a-top-level-managed-disk-resource"></a>Za pomocą zasobem najwyższego poziomu dysku zarządzanego

Jako alternatywę do określania konfiguracji dysku w obiekcie maszyny wirtualnej można utworzyć zasób dyskowy najwyższego poziomu i dołączyć go jako część tworzenia maszyn wirtualnych. Na przykład można utworzyć zasób dysku w następujący sposób, aby użyć jako dysku danych.

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

W ramach obiektu maszyny Wirtualnej należy odwołać się do obiektu dysku do podłączenia. Określ identyfikator ID zasobu dysku zarządzanego, utworzone w `managedDisk` właściwość umożliwia dołączanie dysku podczas tworzenia maszyny Wirtualnej. `apiVersion` Dla maszyny Wirtualnej jest równa zasobów `2017-03-30`. Zależność od zasobu dyskowego jest dodawany do upewnij się, że jego pomyślnym utworzeniu przed utworzeniem maszyny Wirtualnej. 

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

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Tworzenie zarządzanych zestawów dostępności z maszynami wirtualnymi przy użyciu dysków zarządzanych

Do utworzenia zarządzanej zestawy dostępności mające maszyny wirtualne korzystają z dysków zarządzanych, należy dodać `sku` obiektu zestawie dostępności zasobów i ustawić `name` właściwość `Aligned`. Ta właściwość gwarantuje, że dyski dla każdej maszyny Wirtualnej są wystarczająco odizolowane od siebie, aby uniknąć pojedynczych punktów awarii. Należy również zauważyć, że `apiVersion` dla zestawu dostępności zasobów ustawiono `2017-03-30`.

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

### <a name="standard-ssd-disks"></a>Dyski SSD w warstwie standardowa

Poniżej przedstawiono parametry potrzebne w szablonie usługi Resource Manager do utworzenia dysków SSD w warstwie standardowa:

* *wersja interfejsu API* dla dostawcy Microsoft.Compute musi być ustawiona jako `2018-04-01` (lub nowszy)
* Określ *managedDisk.storageAccountType* jako `StandardSSD_LRS`

W poniższym przykładzie przedstawiono *properties.storageProfile.osDisk* dotyczącej maszyny Wirtualnej, która korzysta z dysków SSD w warstwie standardowa:

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

Aby uzyskać kompletny szablon przykład sposobu tworzenia dysku SSD w warstwie standardowa przy użyciu szablonu, zobacz [Utwórz Maszynę wirtualną z obrazu Windows przy użyciu standardowych dysków z danymi SSD](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

### <a name="additional-scenarios-and-customizations"></a>Dodatkowe scenariusze i dostosowania

Aby uzyskać pełne informacje na temat specyfikacji interfejsu API REST, przejrzyj [Tworzenie dysku zarządzanego dokumentację interfejsu API REST](/rest/api/manageddisks/disks/disks-create-or-update). Znajdziesz w dodatkowych scenariuszach, a także domyślne i dopuszczalne wartości, które mogą być przesyłane do interfejsu API za pomocą wdrożeń szablonu. 

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać pełne szablony, które korzystają z dysków zarządzanych skorzystaj z następujących linków repozytorium Szybki Start platformy Azure.
    * [Maszyna wirtualna Windows w przypadku dysków zarządzanych](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Maszyny Wirtualnej systemu Linux przy użyciu dysku zarządzanego](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
    * [Pełną listę szablonów dysku zarządzanego](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* Odwiedź stronę [Omówienie usługi Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md) dokumentu, aby dowiedzieć się więcej na temat dysków zarządzanych.
* Przejrzyj dokumentację referencyjną szablonu zasobów maszyny wirtualnej, odwiedzając [odwołanie do szablonu Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines) dokumentu.
* Przejrzyj dokumentację referencyjną szablonu zasobów dyskowych, odwiedzając [odwołanie do szablonu Microsoft.Compute/disks](/azure/templates/microsoft.compute/disks) dokumentu.
* Aby uzyskać informacje na temat sposobu używania dysków zarządzanych w zestawach skalowania maszyn wirtualnych platformy Azure, odwiedź stronę [korzystanie z dysków danych za pomocą zestawów skalowania](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks) dokumentu.
