---
title: Zmiana zestawu dostępności maszyn wirtualnych
description: Dowiedz się, jak zmienić zestaw dostępności dla maszyn wirtualnych przy użyciu Azure PowerShell i Menedżer zasobów model wdrażania.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/12/2019
ms.author: cynthn
ms.openlocfilehash: 7d03d684edfded1450043b943fc188c7aa07dc16
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039570"
---
# <a name="change-the-availability-set-for-a-windows-vm"></a>Zmiana zestawu dostępności dla maszyny wirtualnej z systemem Windows
W poniższych krokach opisano, jak zmienić zestaw dostępności maszyny wirtualnej przy użyciu Azure PowerShell. Maszynę wirtualną można dodać do zestawu dostępności tylko podczas jego tworzenia. Aby zmienić zestaw dostępności, należy usunąć i ponownie utworzyć maszynę wirtualną. 

Ten artykuł został ostatnio przetestowany w dniu 2/12/2019 przy użyciu [Azure Cloud Shell](https://shell.azure.com/powershell) i [AZ PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps) Version 1.2.0.

 

## <a name="change-the-availability-set"></a>Zmień zestaw dostępności 

Poniższy skrypt zawiera przykład zbierania wymaganych informacji, usuwania oryginalnej maszyny wirtualnej, a następnie tworzenia jej ponownie w nowym zestawie dostępności.

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

## <a name="next-steps"></a>Następne kroki

Dodaj dodatkowy magazyn do maszyny wirtualnej, dodając dodatkowy [dysk danych](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

