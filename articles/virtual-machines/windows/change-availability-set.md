---
title: Zmień zestaw dostępności maszyn wirtualnych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zmienić zestawu dostępności dla maszyn wirtualnych przy użyciu programu Azure PowerShell i modelu wdrażania usługi Resource Manager.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: 8d11f81d5fea47ccef8689a84c06768c4ca36012
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162040"
---
# <a name="change-the-availability-set-for-a-windows-vm"></a>Zmienianie zestawu dostępności dla maszyny Wirtualnej z systemem Windows
Poniżej opisano sposób zmiany zestawu dostępności maszyny wirtualnej przy użyciu programu Azure PowerShell. Maszynę wirtualną można dodać tylko zestaw dostępności podczas jego tworzenia. Aby zmienić dostępności należy ustawić, należy usunąć i ponownie utworzyć maszynę wirtualną. 

## <a name="change-the-availability-set"></a>Zmień zestaw dostępności 

Poniższy skrypt zawiera przykład zbieranie wymaganych informacji, usuwanie oryginalnej maszyny Wirtualnej i odtworzenia go w nowy zestaw dostępności.

```powershell
# Set variables
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    $newAvailSetName = "myAvailabilitySet"

# Get the details of the VM to be moved to the Availablity Set
    $originalVM = Get-AzureRmVM `
       -ResourceGroupName $resourceGroup `
       -Name $vmName

# Remove the original VM
    Remove-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName

# Create new availability set if it does not exist
    $availSet = Get-AzureRmAvailabilitySet `
       -ResourceGroupName $resourceGroup `
       -Name $newAvailSetName `
       -ErrorAction Ignore
    if (-Not $availSet) {
    $availSet = New-AzureRmAvailabilitySet `
       -Location $originalVM.Location `
       -Name $newAvailSetName `
       -ResourceGroupName $resourceGroup `
       -PlatformFaultDomainCount 2 `
       -PlatformUpdateDomainCount 2 `
       -Sku Aligned
    }

# Create the basic configuration for the replacement VM
    $newVM = New-AzureRmVMConfig `
       -VMName $originalVM.Name `
       -VMSize $originalVM.HardwareProfile.VmSize `
       -AvailabilitySetId $availSet.Id
  
    Set-AzureRmVMOSDisk `
       -VM $newVM -CreateOption Attach `
       -ManagedDiskId $originalVM.StorageProfile.OsDisk.ManagedDisk.Id `
       -Name $originalVM.StorageProfile.OsDisk.Name `
       -Windows

# Add Data Disks
    foreach ($disk in $originalVM.StorageProfile.DataDisks) { 
    Add-AzureRmVMDataDisk -VM $newVM `
       -Name $disk.Name `
       -ManagedDiskId $disk.ManagedDisk.Id `
       -Caching $disk.Caching `
       -Lun $disk.Lun `
       -DiskSizeInGB $disk.DiskSizeGB `
       -CreateOption Attach
    }
    
# Add NIC(s)
    foreach ($nic in $originalVM.NetworkProfile.NetworkInterfaces) {
        Add-AzureRmVMNetworkInterface `
           -VM $newVM `
           -Id $nic.Id
    }

# Recreate the VM
    New-AzureRmVM `
       -ResourceGroupName $resourceGroup `
       -Location $originalVM.Location `
       -VM $newVM `
       -DisableBginfoExtension
```

## <a name="next-steps"></a>Kolejne kroki

Dodaj dodatkowy magazyn do maszyny Wirtualnej przez dodanie dodatkowego [dysk z danymi](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

