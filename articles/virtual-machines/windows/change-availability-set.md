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
ms.date: 02/12/2019
ms.author: cynthn
ms.openlocfilehash: 59e28591bda0bada24929fe95fba248d333833e6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64723362"
---
# <a name="change-the-availability-set-for-a-windows-vm"></a>Zmienianie zestawu dostępności dla maszyny Wirtualnej z systemem Windows
Poniżej opisano sposób zmiany zestawu dostępności maszyny wirtualnej przy użyciu programu Azure PowerShell. Maszynę wirtualną można dodać tylko zestaw dostępności podczas jego tworzenia. Aby zmienić dostępności należy ustawić, należy usunąć i ponownie utworzyć maszynę wirtualną. 

W tym artykule ostatniego testowania przy użyciu 2/12/2019 [usługi Azure Cloud Shell](https://shell.azure.com/powershell) i [modułu Az PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) wersji 1.2.0 lub nowszej.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="change-the-availability-set"></a>Zmień zestaw dostępności 

Poniższy skrypt zawiera przykład zbieranie wymaganych informacji, usuwanie oryginalnej maszyny Wirtualnej i odtworzenia go w nowy zestaw dostępności.

```powershell
# Set variables
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    $newAvailSetName = "myAvailabilitySet"

# Get the details of the VM to be moved to the Availability Set
    $originalVM = Get-AzVM `
       -ResourceGroupName $resourceGroup `
       -Name $vmName

# Create new availability set if it does not exist
    $availSet = Get-AzAvailabilitySet `
       -ResourceGroupName $resourceGroup `
       -Name $newAvailSetName `
       -ErrorAction Ignore
    if (-Not $availSet) {
    $availSet = New-AzAvailabilitySet `
       -Location $originalVM.Location `
       -Name $newAvailSetName `
       -ResourceGroupName $resourceGroup `
       -PlatformFaultDomainCount 2 `
       -PlatformUpdateDomainCount 2 `
       -Sku Aligned
    }
    
# Remove the original VM
    Remove-AzVM -ResourceGroupName $resourceGroup -Name $vmName    

# Create the basic configuration for the replacement VM
    $newVM = New-AzVMConfig `
       -VMName $originalVM.Name `
       -VMSize $originalVM.HardwareProfile.VmSize `
       -AvailabilitySetId $availSet.Id
  
    Set-AzVMOSDisk `
       -VM $newVM -CreateOption Attach `
       -ManagedDiskId $originalVM.StorageProfile.OsDisk.ManagedDisk.Id `
       -Name $originalVM.StorageProfile.OsDisk.Name `
       -Windows

# Add Data Disks
    foreach ($disk in $originalVM.StorageProfile.DataDisks) { 
    Add-AzVMDataDisk -VM $newVM `
       -Name $disk.Name `
       -ManagedDiskId $disk.ManagedDisk.Id `
       -Caching $disk.Caching `
       -Lun $disk.Lun `
       -DiskSizeInGB $disk.DiskSizeGB `
       -CreateOption Attach
    }
    
# Add NIC(s) and keep the same NIC as primary
    foreach ($nic in $originalVM.NetworkProfile.NetworkInterfaces) {    
    if ($nic.Primary -eq "True")
        {
            Add-AzVMNetworkInterface `
            -VM $newVM `
            -Id $nic.Id -Primary
            }
        else
            {
              Add-AzVMNetworkInterface `
              -VM $newVM `
              -Id $nic.Id 
                }
    }

# Recreate the VM
    New-AzVM `
       -ResourceGroupName $resourceGroup `
       -Location $originalVM.Location `
       -VM $newVM `
       -DisableBginfoExtension
```

## <a name="next-steps"></a>Kolejne kroki

Dodaj dodatkowy magazyn do maszyny Wirtualnej przez dodanie dodatkowego [dysk z danymi](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

