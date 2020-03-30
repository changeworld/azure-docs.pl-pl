---
title: Dołączanie dysku danych do maszyny Wirtualnej systemu Windows na platformie Azure przy użyciu programu PowerShell
description: Jak dołączyć nowy lub istniejący dysk danych do maszyny Wirtualnej systemu Windows przy użyciu programu PowerShell z modelem wdrażania Menedżera zasobów.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: ce995a84d2290845e83416caf9c8b0004242eed4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267757"
---
# <a name="attach-a-data-disk-to-a-windows-vm-with-powershell"></a>Dołączanie dysku z danymi do maszyny Wirtualnej systemu Windows za pomocą programu PowerShell

W tym artykule pokazano, jak dołączyć zarówno nowe, jak i istniejące dyski do maszyny wirtualnej systemu Windows przy użyciu programu PowerShell. 

Najpierw zapoznaj się z tymi wskazówkami:

* Rozmiar maszyny wirtualnej określa, ile dysków danych można dołączyć. Aby uzyskać więcej informacji, zobacz [Rozmiary maszyn wirtualnych](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Aby korzystać z dysków SSD klasy premium, potrzebny jest [typ maszyny wirtualnej z obsługą magazynu w jakości premium,](sizes-memory.md)taki jak maszyna wirtualna z serii DS lub GS.

W tym artykule używa programu PowerShell w ramach [usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), który jest stale aktualizowany do najnowszej wersji. Aby otworzyć powłokę chmury, wybierz pozycję **Wypróbuj ją** u góry dowolnego bloku kodu.

## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>Dodawanie pustego dysku danych do maszyny wirtualnej

W tym przykładzie pokazano, jak dodać pusty dysk danych do istniejącej maszyny wirtualnej.

### <a name="using-managed-disks"></a>Korzystanie z dysków zarządzanych

```azurepowershell-interactive
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US' 
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-managed-disks-in-an-availability-zone"></a>Korzystanie z dysków zarządzanych w strefie dostępności

Aby utworzyć dysk w strefie dostępności, należy użyć [new-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig) z parametrem. `-Zone` Poniższy przykład tworzy dysk w strefie *1*.

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US 2'
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="initialize-the-disk"></a>Inicjowanie dysku

Po dodaniu pustego dysku należy go zainicjować. Aby zainicjować dysk, można zalogować się do maszyny Wirtualnej i użyć zarządzania dyskami. Jeśli podczas tworzenia usługi [WinRM](https://docs.microsoft.com/windows/desktop/WinRM/portal) i certyfikatu na maszynie wirtualnej została włączona, można użyć zdalnego programu PowerShell do zainicjowania dysku. Można również użyć niestandardowego rozszerzenia skryptu:

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```

Plik skryptu może zawierać kod do inicjowania dysków, na przykład:

```azurepowershell-interactive
    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
    $count++
    }
```

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Dołączanie istniejącego dysku danych do maszyny Wirtualnej

Istniejący dysk zarządzany można dołączyć do maszyny Wirtualnej jako dysk danych.

```azurepowershell-interactive
$rgName = "myResourceGroup"
$vmName = "myVM"
$location = "East US" 
$dataDiskName = "myDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>Następne kroki

Można również wdrożyć dyski zarządzane przy użyciu szablonów. Aby uzyskać więcej informacji, zobacz [Korzystanie z dysków zarządzanych w szablonach usługi Azure Resource Manager](using-managed-disks-template-deployments.md) lub [szablonu szybkiego startu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-data-disk) do wdrażania wielu dysków z danymi.
