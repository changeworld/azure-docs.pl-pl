---
title: Konwertuj Azure zarządzane dyski magazynu od planu standard do warstwy premium i odwrotnie | Dokumentacja firmy Microsoft
description: Sposób konwertowania Azure zarządzane dyski od planu standard do premium i odwrotnie, przy użyciu programu Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: ramankumarlive
manager: kavithag
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/04/2018
ms.author: ramankum
ms.subservice: disks
ms.openlocfilehash: 676adb05178097c1802c0ff5b3a5da1bbb7b188c
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978378"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Aktualizuj typ magazynu dysków zarządzanych

Usługa Azure Managed Disks oferuje trzy opcje typu magazynu: [Premium SSD](../windows/premium-storage.md), [SSD w warstwie standardowa](../windows/disks-standard-ssd.md), i [standardowych dysków Twardych](../windows/standard-storage.md). Można przełączać się między typami magazynu z minimalnym czasem przestojów, w oparciu o potrzeby związane z wydajnością dysku zarządzanego. Przełączanie między typami magazynu nie jest obsługiwane dla dysku niezarządzanego; można jednak łatwo [przekonwertować dysku niezarządzanego dysku zarządzanego](convert-unmanaged-to-managed-disks.md).

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Ponieważ konwersja wymaga ponownego uruchomienia maszyny wirtualnej (VM), należy zaplanować migrację magazynu dysków podczas istniejącego okna obsługi. 
* Jeśli używasz dysku niezarządzanego, najpierw [przekonwertować go na dysk zarządzany](convert-unmanaged-to-managed-disks.md) aby możliwe było przełączać typów magazynów. 
* Przykłady w niniejszym artykule wymagają programu Azure PowerShell w module w wersji 6.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/azurerm/install-az-ps). Uruchom [Connect AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) do utworzenia połączenia z platformą Azure.


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium"></a>Konwertuj dyski zarządzane maszyny wirtualnej z warstwy standardowa do warstwy premium

Poniższy przykład pokazuje, jak przełączyć wszystkie dyski maszyny wirtualnej z warstwy standardowa do usługi premium storage. Aby korzystać z dysków zarządzanych w warstwie premium, musisz użyć maszyny Wirtualnej [rozmiar maszyny Wirtualnej](sizes.md) , która obsługuje usługę premium storage. W tym przykładzie zmienia także rozmiar, który obsługuje usługę premium storage:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from standard to premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzDiskUpdateConfig –AccountType $storageType
        Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="convert-a-managed-disk-from-standard-to-premium"></a>Konwertuj dysk zarządzany od planu standard do premium

Do tworzenia i testowania obciążenia możesz chcieć kombinację dysków standardowa i premium, aby zmniejszyć koszt. Aby to zrobić, uaktualnienie do usługi premium storage tylko dyski, które wymagają lepszą wydajność. Poniższy przykład pokazuje, jak przełączyć jednego dysku maszyny wirtualnej od planu standard do premium storage i na odwrót. Aby korzystać z dysków zarządzanych w warstwie premium, musisz użyć maszyny Wirtualnej [rozmiar maszyny Wirtualnej](sizes.md) , która obsługuje usługę premium storage. W tym przykładzie przedstawiono również jak przełączyć się do rozmiaru, który obsługuje usługę premium storage:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd"></a>Konwertuj dysk zarządzany ze standardowych dysków Twardych na SSD w warstwie standardowa

Poniższy przykład pokazuje, jak przełączyć jednego dysku maszyny wirtualnej ze standardowych dysków Twardych na SSD w warstwie standardowa i na odwrót:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Kolejne kroki

Utwórz kopię tylko do odczytu, maszyny wirtualnej przy użyciu [migawki](snapshot-copy-managed-disk.md).

