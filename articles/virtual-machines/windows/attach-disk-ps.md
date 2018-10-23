---
title: Dołączanie dysku danych do maszyny Wirtualnej z systemem Windows na platformie Azure przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Jak dołączyć dysk danych do nowego lub istniejącego do maszyny Wirtualnej Windows przy użyciu programu PowerShell przy użyciu modelu wdrażania usługi Resource Manager.
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
ms.date: 10/16/2018
ms.author: cynthn
ms.openlocfilehash: cd11bb8ae8f22705feb7eebeafde385fcf11fdcd
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2018
ms.locfileid: "49637089"
---
# <a name="attach-a-data-disk-to-a-windows-vm-with-powershell"></a>Dołączanie dysku danych do maszyny Wirtualnej z systemem Windows przy użyciu programu PowerShell

W tym artykule pokazano, jak dołączyć istniejących i nowych dysków na maszynę wirtualną Windows przy użyciu programu PowerShell. 

Po pierwsze Przejrzyj poniższe wskazówki:
* Liczba dysków z danymi można dołączać jest kontrolowana przez rozmiar maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Używanie usługi Premium storage, należy włączyć usługę Premium Storage typu maszyny Wirtualnej serii DS lub maszyny wirtualnej serii GS. Aby uzyskać więcej informacji, zobacz [usługi Premium Storage: magazyn o wysokiej wydajności dla obciążeń maszyn wirtualnych platformy Azure](premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Aby zainstalować i korzystać z programu PowerShell lokalnie, ten samouczek wymaga programu Azure PowerShell module w wersji 6.0.0 lub nowszej. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, należy także uruchomić `Connect-AzureRmAccount` do utworzenia połączenia z platformą Azure.


## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>Dodawanie pustego dysku danych do maszyny wirtualnej

Ten przykład przedstawia sposób dodawania pustego dysku danych do istniejącej maszyny wirtualnej.

### <a name="using-managed-disks"></a>Używanie dysków zarządzanych

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

### <a name="using-managed-disks-in-an-availability-zone"></a>Używanie dysków zarządzanych w strefie dostępności
Aby utworzyć dysk w strefie dostępności, należy użyć [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig) z `-Zone` parametru. Poniższy przykład tworzy dysk w strefie *1*.


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

Po dodaniu pusty dysk, musisz go zainicjować. Aby zainicjować dysk, możesz zalogować się do maszyny Wirtualnej i użyj przystawki Zarządzanie dyskami. Jeśli włączono [WinRM](https://docs.microsoft.com/windows/desktop/WinRM/portal) i certyfikatu na maszynie Wirtualnej, podczas jego tworzenia, można użyć zdalnego programu PowerShell do Zainicjuj dysk. Można również użyć rozszerzenia niestandardowego skryptu: 

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```
        
Plik skryptu może zawierać kod, aby zainicjować dyski, na przykład:

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


## <a name="attach-an-existing-data-disk-to-a-vm"></a>Dołącz istniejący dysk danych do maszyny Wirtualnej

Można dołączyć istniejącego dysku zarządzanego do maszyny Wirtualnej jako dysk z danymi. 

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

Tworzenie [migawki](snapshot-copy-managed-disk.md).
