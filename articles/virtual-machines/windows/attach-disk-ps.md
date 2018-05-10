---
title: Dołączenie dysku danych do maszyny Wirtualnej systemu Windows na platformie Azure przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Jak można dołączyć danych do nowego lub istniejącego dysku do maszyny Wirtualnej systemu Windows przy użyciu programu PowerShell z modelu wdrażania usługi Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: cynthn
ms.openlocfilehash: 384203134d1588053f91b66d32e9b0bf1ec69306
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="attach-a-data-disk-to-a-windows-vm-using-powershell"></a>Dołączenie dysku danych do maszyny Wirtualnej systemu Windows przy użyciu programu PowerShell

W tym artykule pokazano, jak dołączyć istniejących i nowych dysków do maszyny wirtualnej systemu Windows przy użyciu programu PowerShell. 

Zanim to zrobisz, przejrzyj następujące wskazówki:
* Rozmiar maszyny wirtualnej Określa, ile można dołączać dysków z danymi. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Aby użyć magazyn w warstwie Premium, będziesz potrzebować magazyn w warstwie Premium włączone rozmiar maszyny Wirtualnej, takie jak serii DS lub GS-series maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [magazyn w warstwie Premium: magazyn o wysokiej wydajności dla obciążeń maszyny wirtualnej Azure](premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli wybierzesz do zainstalowania i używania programu PowerShell lokalnie, ten samouczek wymaga programu Azure PowerShell modułu w wersji 6.0.0 lub nowszej. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.


## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>Dodaj dysk danych puste do maszyny wirtualnej

Ten przykład przedstawia sposób dodawania dysku pusty danych do istniejącej maszyny wirtualnej.

### <a name="using-managed-disks"></a>Za pomocą dysków zarządzanych

```azurepowershell-interactive
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US' 
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzureRmDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-managed-disks-in-an-availability-zone"></a>Za pomocą dysków zarządzanych w strefie dostępności
Aby utworzyć dysk w strefie dostępności, użyj [AzureRmDiskConfig nowy](/powershell/module/azurerm.compute/new-azurermdiskconfig) z `-Zone` parametru. W poniższym przykładzie tworzony jest dysk w strefie *1*.


```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US 2' 
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzureRmDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1
$dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```


### <a name="initialize-the-disk"></a>Zainicjuj dysk

Po dodaniu pusty dysk, musisz go zainicjować. Aby zainicjalizować dysk, możesz zalogować się do maszyny Wirtualnej i użyj przystawki Zarządzanie dyskami. Jeśli usługi WinRM i certyfikatu na maszynie Wirtualnej jest włączona podczas jego tworzenia, można użyć zdalnego programu PowerShell, aby zainicjalizować dysk. Można także użyć niestandardowego rozszerzenia skryptu: 

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```
        
Plik skryptu może zawierać przypominać ten kod, aby zainicjować dyski:

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

Jako dysk danych, można dołączyć istniejącego dysku zarządzanego z maszyną wirtualną. 

```azurepowershell-interactive
$rgName = "myResourceGroup"
$vmName = "myVM"
$location = "East US" 
$dataDiskName = "myDisk"
$disk = Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzureRmVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>Kolejne kroki

Utwórz [migawki](snapshot-copy-managed-disk.md).
