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
ms.openlocfilehash: 4c13708ad785a2291da3db61d739f604a2c3bb88
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55475894"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Aktualizuj typ magazynu dysków zarządzanych

Usługa Azure Managed Disks oferuje trzy opcje typu magazynu: [Premium SSD](../windows/premium-storage.md), [SSD w warstwie standardowa](../windows/disks-standard-ssd.md), i [standardowych dysków Twardych](../windows/standard-storage.md). Można przełączać się między typami magazynu z minimalnym czasem przestojów, w oparciu o potrzeby związane z wydajnością dysku zarządzanego. Przełączanie między typami magazynu nie jest obsługiwane dla dysku niezarządzanego; można jednak łatwo [przekonwertować dysku niezarządzanego dysku zarządzanego](convert-unmanaged-to-managed-disks.md).

W tym artykule przedstawiono sposób konwertowania dysku zarządzanego od planu standard do premium i odwrotnie, przy użyciu programu Azure PowerShell. Jeśli musisz zainstalować lub uaktualnić programu PowerShell, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.8.1).

## <a name="prerequisites"></a>Wymagania wstępne

* Ponieważ konwersja wymaga ponownego uruchomienia maszyny wirtualnej (VM), należy zaplanować migrację magazynu dysków podczas istniejącego okna obsługi. 
* Jeśli używasz dysku niezarządzanego, najpierw [przekonwertować go na dysk zarządzany](convert-unmanaged-to-managed-disks.md) aby możliwe było przełączać typów magazynów. 
* Przykłady w niniejszym artykule wymagają programu Azure PowerShell w module w wersji 6.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Uruchom [Connect-AzureRmAccount](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount) do utworzenia połączenia z platformą Azure.


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

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

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

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd"></a>Konwertuj dysk zarządzany ze standardowych dysków Twardych na SSD w warstwie standardowa

Poniższy przykład pokazuje, jak przełączyć jednego dysku maszyny wirtualnej ze standardowych dysków Twardych na SSD w warstwie standardowa i na odwrót:

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

Utwórz kopię tylko do odczytu, maszyny wirtualnej przy użyciu [migawki](snapshot-copy-managed-disk.md).

