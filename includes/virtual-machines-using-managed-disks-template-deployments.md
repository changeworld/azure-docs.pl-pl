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
ms.openlocfilehash: f363c52093e0ae8314e07e750eb4362f71e9a4b1
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34826518"
---
# <a name="using-managed-disks-in-azure-resource-manager-templates"></a>Za pomocą Managed dysków w szablonach usługi Azure Resource Manager

Ten dokument przeprowadzi Cię przez różnice między zarządzanymi i niezarządzanymi dysków przy użyciu szablonów usługi Azure Resource Manager na umieszczanie maszyn wirtualnych. Przykłady ułatwić aktualizowanie istniejących szablonów, które korzystają z dysków niezarządzanych do zarządzanych dysków. Odwołania, jest używany [101 maszyny wirtualnej — prosty — windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) szablonu jako przewodnika. Widać szablon przy użyciu zarówno [dyskach zarządzanych](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) i przy użyciu poprzedniej wersji [niezarządzanych dysków](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) Jeśli chcesz bezpośrednio porównywać.

## <a name="unmanaged-disks-template-formatting"></a>Niezarządzane formatowania szablonu dysków

Aby rozpocząć, umożliwia podjęcia wyglądu w sposób niezarządzany dyski są wdrażane. Podczas tworzenia dysków niezarządzane, potrzebujesz konta magazynu do przechowywania plików wirtualnego dysku twardego. Możesz utworzyć nowe konto magazynu lub użyć już istniejącego. W tym artykule przedstawiono sposób tworzenia nowego konta magazynu. Utwórz zasób konta magazynu w bloku zasobów, jak pokazano poniżej.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2016-01-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

W obiekcie maszyny wirtualnej należy dodać zależność na koncie magazynu, aby upewnić się, że został utworzony przed maszyny wirtualnej. W ramach `storageProfile` sekcji, określ pełny identyfikator URI lokalizacji wirtualnego dysku twardego, który odwołuje się do konta magazynu i jest wymagany dla dysku systemu operacyjnego i dysków z danymi.

```json
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines",
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

## <a name="managed-disks-template-formatting"></a>Zarządzane formatowania szablonu dysków

W przypadku dysków zarządzanych Azure dysk staje się zasobem najwyższego poziomu i nie wymaga już konto magazynu ma zostać utworzony przez użytkownika. Dysków zarządzanych zostały najpierw udostępnione w `2016-04-30-preview` wersja interfejsu API są dostępne w wszystkie kolejne wersje interfejsu API i są teraz domyślny typ dysku. Poniższe sekcje przeprowadzenie ustawienia domyślne i szczegółów jak dostosować dysków.

> [!NOTE]
> Zalecane jest użycie wersji interfejsu API później niż `2016-04-30-preview` jako wystąpiły zmiany podziału między `2016-04-30-preview` i `2017-03-30`.
>
>

### <a name="default-managed-disk-settings"></a>Domyślne ustawienia dysków zarządzanych

Aby utworzyć Maszynę wirtualną z dyskami zarządzanych, już nie musisz utworzyć magazyn kont zasobów i można zaktualizować zasobu maszyny wirtualnej w następujący sposób. W szczególności należy pamiętać, że `apiVersion` odzwierciedla `2017-03-30` i `osDisk` i `dataDisks` nie odnoszą się do określonego identyfikatora URI dysku VHD. W przypadku wdrażania bez określenia dodatkowych właściwości, dysk będzie używać [magazynu Standard-LRS](../articles/storage/common/storage-redundancy.md). Jeśli nazwa nie zostanie określona, zajmuje format `<VMName>_OsDisk_1_<randomstring>` dla dysku systemu operacyjnego i `<VMName>_disk<#>_<randomstring>` dla każdego dysku danych. Domyślnie szyfrowania dysków Azure jest wyłączony; buforowanie jest odczytu/zapisu dla dysku systemu operacyjnego i brak w przypadku dysków z danymi. W poniższym przykładzie mogą pojawić się, że istnieje zależność konta magazynu, mimo że to jest tylko do przechowywania diagnostyki i nie jest wymagany dla magazynu danych na dysku.

```json
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
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

### <a name="using-a-top-level-managed-disk-resource"></a>Przy użyciu zasobów dysków zarządzanych w najwyższego poziomu

Zamiast określać konfigurację dysków w obiekcie maszyny wirtualnej można tworzenia zasobu dysku najwyższego poziomu i dołącz je jako część tworzenie maszyny wirtualnej. Na przykład można utworzyć zasobu dysku w następujący sposób, aby użyć jako dysku danych.

```json
{
    "type": "Microsoft.Compute/disks",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "apiVersion": "2017-03-30",
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

W ramach obiektu maszyny Wirtualnej odwołuje się do obiektu dysku jest dołączony. Określ identyfikator ID zasobu dysku zarządzanego utworzone w `managedDisk` właściwość umożliwia dołączanie dysku tworzenia maszyny Wirtualnej. `apiVersion` Dla maszyny Wirtualnej zasobów ma ustawioną wartość `2017-03-30`. Zależność od zasobu dyskowego jest dodawany do upewnij się, że pomyślnie utworzono przed utworzeniem maszyny Wirtualnej. 

```json
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
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

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Tworzenie zestawów dostępności zarządzanych maszyn wirtualnych za pomocą dysków zarządzanych

Do tworzenia zarządzanego zestawy dostępności z maszynami wirtualnymi przy użyciu dysków zarządzanych, Dodaj `sku` obiektu dostępności ustawić zasobów i ustawić `name` właściwości `Aligned`. Ta właściwość zapewnia, że dyski dla każdej maszyny Wirtualnej są wystarczająco odizolowane od siebie, aby uniknąć pojedynczych punktów awarii. Należy również zauważyć, że `apiVersion` dla zestawu dostępności zasobów ma ustawioną wartość `2017-03-30`.

```json
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/availabilitySets",
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

### <a name="standard-ssd-disks"></a>Dyski SSD standardowe

Poniżej przedstawiono parametry wymagane w szablonie usługi Resource Manager do tworzenia standardowych dysków SSD:

* *apiVersion* dla Microsoft.Compute musi być ustawiona jako `2018-04-01` (lub nowsza)
* Określ *managedDisk.storageAccountType* jako `StandardSSD_LRS`

W poniższym przykładzie przedstawiono *properties.storageProfile.osDisk* sekcji dla maszyny Wirtualnej, która używa standardowych dysków SSD:

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

Na przykład Pełna szablonu tworzenia dysku standardowych dysków SSD przy użyciu szablonu, zobacz [utworzyć Maszynę wirtualną z obrazu systemu Windows w przypadku standardowych dysków danych SSD](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

### <a name="additional-scenarios-and-customizations"></a>Dodatkowe scenariusze i dostosowania

Aby uzyskać pełne informacje dotyczące specyfikacji interfejsu API REST, zapoznaj się z tematem [tworzenie dysków zarządzanych w dokumentacji interfejsu API REST](/rest/api/manageddisks/disks/disks-create-or-update). Dostępne są dodatkowe scenariusze, a także domyślne i dopuszczalne wartości, które można przesłać do interfejsu API za pomocą szablonu wdrożenia. 

## <a name="next-steps"></a>Kolejne kroki

* Pełna szablonów, które zarządzanych dysków można znaleźć w następujących łączy repozytorium Szybki Start Azure.
    * [Maszyny Wirtualnej systemu Windows z dyskiem zarządzanym](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Maszyny Wirtualnej systemu Linux z dyskiem zarządzanym](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
    * [Pełną listę szablonów zarządzanych dysku](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* Odwiedź stronę [omówienie dysków zarządzanych Azure](../articles/virtual-machines/windows/managed-disks-overview.md) dokumentu, aby dowiedzieć się więcej o dyskach zarządzanych.
* Zapoznaj się z dokumentacją odwołanie szablonu, zasobów maszyny wirtualnej po przejściu na stronę [odwołania do szablonu Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines) dokumentu.
* Zapoznaj się z dokumentacją odwołanie szablonu, zasoby dyskowe odwiedzając [odwołania do szablonu Microsoft.Compute/disks](/azure/templates/microsoft.compute/disks) dokumentu.
* Aby uzyskać informacje dotyczące sposobu używania dysków zarządzanych w zestawy skalowania maszyny wirtualnej platformy Azure, odwiedź stronę [dysków danych za pomocą zestawów skali](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md) dokumentu.
