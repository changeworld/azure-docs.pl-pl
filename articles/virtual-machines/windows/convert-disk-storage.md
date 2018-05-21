---
title: Konwertuj Azure zarządzanego dyski magazynu ze standardu do premium i na odwrót | Dokumentacja firmy Microsoft
description: Jak przekonwertować Azure zarządzane dysków ze standardu do premium i odwrotnie, przy użyciu programu Azure PowerShell.
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
ms.openlocfilehash: 19979240e13ac822921b7f43a158d171aeea0123
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Konwertuj Azure zarządzanego dyski magazynu ze standardu do premium i na odwrót

Zarządzane dysków oferuje dwie opcje magazynu: [Premium](premium-storage.md) (oparte na dysk SSD) i [standardowe](standard-storage.md) (oparte na dysk twardy). Umożliwia ona łatwo przełączać się między dwie opcje z minimalnym czasem przestojów na podstawie Twoich potrzeb wydajności. Ta funkcja nie jest dostępna dla niezarządzanego dysków. Ale możesz z łatwością [konwertować na zarządzane dyski](convert-unmanaged-to-managed-disks.md) można łatwo przełączać się między dwie opcje.

W tym artykule przedstawiono sposób konwertowania dysków zarządzanych ze standardu do premium i na odwrót przy użyciu programu Azure PowerShell. Jeśli musisz zainstalować lub uaktualnić go, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-azurerm-ps.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Konwersja wymaga ponownego uruchomienia maszyny wirtualnej, więc planowanie migracji magazynu dysków w istniejącym oknie obsługi. 
* Jeśli używane są niezarządzane dysków, najpierw [konwertować na zarządzane dyski](convert-unmanaged-to-managed-disks.md) do korzystania z tego artykułu w celu przełączania się między dwie opcje magazynu. 
* W tym artykule wymaga programu Azure PowerShell modułu w wersji 6.0.0 lub nowszej. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Należy również wykonać `Connect-AzureRmAccount` można utworzyć połączenia z platformą Azure.


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Konwertuj wszystkie zarządzane dyski maszyny wirtualnej ze standardu na — wersja premium i na odwrót

W poniższym przykładzie zostanie przedstawiony sposób Przełącz wszystkie dyski maszyny Wirtualnej ze standardu, aby Magazyn w warstwie premium. Aby użyć dysków zarządzanych w warstwie premium, musisz użyć maszyny Wirtualnej [rozmiar maszyny Wirtualnej](sizes.md) obsługującym magazyn w warstwie premium. W tym przykładzie również zmienia rozmiar, który obsługuje magazyn w warstwie premium.

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'PremiumLRS'

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
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Konwertuj dysk zarządzanych ze standardu na — wersja premium i na odwrót

Dla obciążenia i testowania możesz mieć kombinację dysków warstwy standardowa i premium, aby zmniejszyć koszty. Można wykonać je przez uaktualnienie do magazyn w warstwie premium, tylko dyski, które wymagają lepszą wydajność. W poniższym przykładzie zostanie przedstawiony sposób przełącznika jednego dysku maszyny Wirtualnej ze standardu, aby Magazyn w warstwie premium i na odwrót. Aby użyć dysków zarządzanych w warstwie premium, musisz użyć maszyny Wirtualnej [rozmiar maszyny Wirtualnej](sizes.md) obsługującym magazyn w warstwie premium. W tym przykładzie również zmienia rozmiar, który obsługuje magazyn w warstwie premium.

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'PremiumLRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get the ARM resource to get name and resource group of the VM
$vmResource = Get-AzureRmResource -ResourceId $disk.diskId

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name -Force

$vm = Get-AzureRmVM $vmResource.ResourceGroupName -Name $vmResource.ResourceName 

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

## <a name="next-steps"></a>Kolejne kroki

Podjąć tylko do odczytu kopię maszyny Wirtualnej za pomocą [migawki](snapshot-copy-managed-disk.md).

