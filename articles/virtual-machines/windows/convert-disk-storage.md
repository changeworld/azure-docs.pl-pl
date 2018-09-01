---
title: Konwertuj Azure zarządzane dyski magazynu od planu standard do warstwy premium i odwrotnie | Dokumentacja firmy Microsoft
description: Sposób konwertowania Azure zarządzane dyski od planu standard do premium i odwrotnie, przy użyciu programu Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: ramankum
manager: kavithag
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: 7e4a37e318bd45c5cc2dca4e085b2a112db65efe
ms.sourcegitcommit: e45b2aa85063d33853560ec4bc867f230c1c18ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43371359"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Konwertuj Azure zarządzane dyski magazynu od planu standard do warstwy premium i odwrotnie

Managed Disks oferuje trzy opcje magazynu: [dysku Premium SSD](../windows/premium-storage.md), standardowe SSD(Preview) i [standardowych dysków Twardych](../windows/standard-storage.md). Dzięki temu można łatwo przełączać się między opcjami przy minimalnych przestojach w oparciu o potrzeby związane z wydajnością. To nie jest obsługiwana w przypadku dysków niezarządzanych. Ale możesz z łatwością [konwersji do usługi managed disks](convert-unmanaged-to-managed-disks.md) można łatwo przełączać się między typy dysków.

W tym artykule pokazano, jak konwertować dysków zarządzanych warstwy standardowej na premium i odwrotnie przy użyciu programu Azure PowerShell. Jeśli musisz zainstalować lub uaktualnić go, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.8.1).

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Konwersja wymaga ponownego uruchomienia maszyny wirtualnej, więc należy zaplanować migrację magazynu dysków podczas istniejącego okna obsługi. 
* Jeśli używasz dysków niezarządzanych, najpierw [konwersji do usługi managed disks](convert-unmanaged-to-managed-disks.md) na potrzeby przełączania się między opcjami magazynu w tym artykule. 
* Ten artykuł wymaga programu Azure PowerShell module w wersji 6.0.0 lub nowszej. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Należy również uruchomić polecenie `Connect-AzureRmAccount` w celu nawiązania połączenia z platformą Azure.


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Konwertuj zarządzane dyski maszyny Wirtualnej od planu standard do warstwy premium i odwrotnie

Poniższy przykład pokazuje, jak przełączyć wszystkie dyski maszyny wirtualnej z warstwy standardowa do usługi premium storage. Aby korzystać z dysków zarządzanych w warstwie premium, musisz użyć maszyny Wirtualnej [rozmiar maszyny Wirtualnej](sizes.md) , która obsługuje usługę premium storage. W tym przykładzie zmienia także rozmiar, który obsługuje usługę premium storage.

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
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzureRmVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType $storageType
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Konwertuj dysk zarządzany od planu standard do warstwy premium i odwrotnie

Tworzenie i testowanie obciążenia możesz mieć kombinację dysków standardowa i premium, aby zmniejszyć koszt. Można wykonać je przez uaktualnienie do usługi premium storage, dyski, które wymagają lepszą wydajność. Poniższy przykład pokazuje, jak przełączyć jednego dysku maszyny wirtualnej od planu standard do premium storage i na odwrót. Aby korzystać z dysków zarządzanych w warstwie premium, musisz użyć maszyny Wirtualnej [rozmiar maszyny Wirtualnej](sizes.md) , która obsługuje usługę premium storage. W tym przykładzie zmienia także rozmiar, który obsługuje usługę premium storage.

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd-and-vice-versa"></a>Konwertuj dysk zarządzany ze standardowych dysków Twardych na SSD w warstwie standardowa i na odwrót

Poniższy przykład pokazuje, jak przełączyć jednego dysku maszyny wirtualnej ze standardowych dysków Twardych na SSD w warstwie standardowa i na odwrót.

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Kolejne kroki

Wykonaj kopię tylko do odczytu maszyny wirtualnej przy użyciu [migawek](snapshot-copy-managed-disk.md).

