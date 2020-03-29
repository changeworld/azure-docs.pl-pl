---
title: Konwertowanie magazynu dysków zarządzanych między dyskami SSD klasy podstawowej i premium
description: Jak konwertować dyski zarządzane platformy Azure ze standardu na premium lub premium na standard przy użyciu programu Azure PowerShell.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e339f0c7ca0807eec3e160eeb3464044c2ef29ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720949"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Aktualizowanie typu magazynu dysku zarządzanego

Istnieją cztery typy dysków zarządzanych platformy Azure: dyski Azure ultra SSD (wersja zapoznawcza), dysk SSD w wersji premium, standardowy dysk SSD i standardowy dysk twardy. Możesz przełączać się między trzema typami dysków GA (premium SSD, standard SSD i standard HDD) w zależności od potrzeb dotyczących wydajności. Nie można jeszcze przełączyć się z lub na ultra SSD, musisz wdrożyć nowy.

Ta funkcja nie jest obsługiwana dla dysków niezarządzanych. Można jednak łatwo [przekonwertować dysk niezarządzany na dysk zarządzany,](convert-unmanaged-to-managed-disks.md) aby móc przełączać się między typami dysków.

 

## <a name="prerequisites"></a>Wymagania wstępne

* Ponieważ konwersja wymaga ponownego uruchomienia maszyny wirtualnej (VM), należy zaplanować migrację magazynu dysku podczas istniejącego okna konserwacji.
* Jeśli dysk jest niezarządzany, należy najpierw [przekonwertować go na dysk zarządzany, aby](convert-unmanaged-to-managed-disks.md) przełączać się między opcjami magazynu.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Przełączanie wszystkich dysków zarządzanych maszyny Wirtualnej między usługą Premium a Standard

W tym przykładzie pokazano, jak przekonwertować wszystkie dyski maszyny Wirtualnej z magazynu standardowego na magazyn w wersji Premium lub z magazynu Premium na Standardowy. Aby korzystać z dysków zarządzanych w układce, maszyna wirtualna musi używać [rozmiaru maszyny Wirtualnej,](sizes.md) który obsługuje magazyn w klasie Premium. W tym przykładzie przełącza się również do rozmiaru, który obsługuje magazynu w wersji premium:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Przełączanie poszczególnych dysków zarządzanych między standardem a usługą Premium

W przypadku obciążenia deweloper/test można chcieć, aby połączenie dysków standard i premium zmniejszyło koszty. Można uaktualnić tylko te dyski, które wymagają lepszej wydajności. W tym przykładzie pokazano, jak przekonwertować jeden dysk maszyny Wirtualnej z magazynu standardowego na magazyn w wersji Premium lub z magazynu Premium na Standardowy. Aby korzystać z dysków zarządzanych w układce, maszyna wirtualna musi używać [rozmiaru maszyny Wirtualnej,](sizes.md) który obsługuje magazyn w klasie Premium. W tym przykładzie pokazano również, jak przełączyć się do rozmiaru, który obsługuje magazynu w wersji Premium:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Konwertowanie dysków zarządzanych ze standardu na premium w witrynie Azure portal

Wykonaj następujące kroki:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz maszynę wirtualną z listy **maszyn wirtualnych** w portalu.
3. Jeśli maszyna wirtualna nie jest zatrzymana, wybierz **pozycję Zatrzymaj** w górnej części **okienka Przegląd maszyny** Wirtualnej i poczekaj na zatrzymanie maszyny Wirtualnej.
3. W okienku maszyny Wirtualnej wybierz pozycję **Dyski** z menu.
4. Wybierz dysk, który chcesz przekonwertować.
5. Z menu **wybierz opcję Konfiguracja.**
6. Zmień **typ konta** ze **standardowego dysku twardego** na dysk **SSD premium**.
7. Kliknij **pozycję Zapisz**i zamknij okienko dysku.

Konwersja typu dysku jest natychmiastowa. Możesz uruchomić maszynę wirtualną po konwersji.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Przełączanie dysków zarządzanych między standardowym dyskiem twardym a standardowym dyskiem SSD 

W tym przykładzie pokazano, jak przekonwertować pojedynczy dysk maszyn wirtualnych ze standardowego dysku twardego na standardowy dysk SSD lub ze standardowego dysku SSD na standardowy dysk twardy:

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
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Następne kroki

Tworzenie kopii maszyny wirtualnej tylko do odczytu przy użyciu [migawki](snapshot-copy-managed-disk.md).
