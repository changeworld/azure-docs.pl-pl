---
title: Konwertuj Azure zarządzane dyski magazynu od planu Standard do Premium lub Premium na standardową | Dokumentacja firmy Microsoft
description: Sposób konwertowania Azure dysków zarządzanych warstwy standardowej na Premium lub Premium na standardową przy użyciu programu Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 5687e6d0094083a9ee58455cc72b0b2e4da32d65
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417153"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Aktualizuj typ magazynu dysków zarządzanych

Istnieją cztery dyski zarządzane typy dysków platformy Azure: Dyski SSD ultra platformy Azure (wersja zapoznawcza) i premium SSD, SSD w warstwie standardowa i standardowych dysków Twardych. Możesz przełączać się między trzy typy dysków GA (premium SSD, SSD w warstwie standardowa i standardowych dysków Twardych) oparte na potrzeby związane z wydajnością. Nie jesteś jeszcze możliwość przełączania się z lub do najwyższej dyski SSD, Wdróż nową.

Ta funkcja nie jest obsługiwana w przypadku dysków niezarządzanych. Ale możesz z łatwością [przekonwertować dysku niezarządzanego dysku zarządzanego](convert-unmanaged-to-managed-disks.md) mieć możliwość przełączania się między typy dysków.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Ponieważ konwersja wymaga ponownego uruchomienia maszyny wirtualnej (VM), należy zaplanować migrację magazynu na dysku podczas istniejącego okna obsługi.
* Jeśli dysk jest niezarządzany, najpierw [przekonwertować go na dysk zarządzany](convert-unmanaged-to-managed-disks.md) , dzięki czemu można przełączać się między opcjami magazynu.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Przełącz wszystkie zarządzane dyski maszyny Wirtualnej między Premium i standardowa

W tym przykładzie pokazano, jak można przekonwertować wszystkie dyski maszyny Wirtualnej z warstwy standardowa do magazynu w warstwie Premium lub wersji Premium do magazynu w warstwie standardowa. Aby korzystać z dysków zarządzanych w warstwie Premium, musisz użyć maszyny Wirtualnej [rozmiar maszyny Wirtualnej](sizes.md) , która obsługuje usługę Premium storage. W tym przykładzie zmienia także rozmiar, który obsługuje usługę premium storage:

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
        $diskUpdateConfig = New-AzDiskUpdateConfig –AccountType $storageType
        Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Przełącz poszczególne dysków zarządzanych warstw standardowa i Premium

Do tworzenia i testowania obciążenia możesz zechcieć kombinację dysków Standard i Premium, aby zmniejszyć koszty. Można uaktualnić tylko te dyski, wymagających lepszą wydajność. Ten przykład przedstawia sposób konwertowania z jednego dysku maszyny Wirtualnej z warstwy standardowa do magazynu w warstwie Premium lub wersji Premium do magazynu w warstwie standardowa. Aby korzystać z dysków zarządzanych w warstwie Premium, musisz użyć maszyny Wirtualnej [rozmiar maszyny Wirtualnej](sizes.md) , która obsługuje usługę Premium storage. W tym przykładzie przedstawiono również jak przełączyć się do rozmiaru, który obsługuje usługę Premium storage:

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
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Konwertuj dysków zarządzanych warstwy standardowej na Premium w witrynie Azure portal

Wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz maszynę Wirtualną z listy **maszyn wirtualnych** w portalu.
3. Jeśli maszyna wirtualna nie jest zatrzymana, wybierz opcję **zatrzymać** w górnej części maszyn wirtualnych **Przegląd** okienko i zaczekaj, aż maszyny Wirtualnej zatrzymać.
3. W okienku dla maszyny Wirtualnej wybierz **dysków** z menu.
4. Wybierz dysk, który ma zostać przekonwertowany.
5. Wybierz **konfiguracji** z menu.
6. Zmiana **typ konta** z **standardowych dysków Twardych** do **dysku Premium SSD**.
7. Kliknij przycisk **Zapisz**i zamknąć okienko dysku.

Konwersja typu dysku jest natychmiastowe. Możesz ponownie uruchomić maszynę Wirtualną po konwersji.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Przełącz dysków zarządzanych między standardowych dysków Twardych i SSD w warstwie standardowa 

Ten przykład pokazuje sposób konwertowania pojedynczego dysku maszyny Wirtualnej, z standardowych dysków Twardych do SSD w warstwie standardowa lub SSD w warstwie standardowa do standardowych dysków Twardych:

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
