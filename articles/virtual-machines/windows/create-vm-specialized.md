---
title: Tworzenie maszyny Wirtualnej systemu Windows z specjalne wirtualnego dysku twardego na platformie Azure | Dokumentacja firmy Microsoft
description: Utwórz nową maszynę Wirtualną systemu Windows przez dołączenie specjalne dysków zarządzanych jako dysk systemu operacyjnego przy użyciu modelu wdrażania Menedżera zasobów.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: cynthn
ms.openlocfilehash: 7b1a145040297debe2c348d61f204fc82d2e7d4d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="create-a-windows-vm-from-a-specialized-disk-using-powershell"></a>Tworzenie maszyny Wirtualnej systemu Windows z dysku specjalne przy użyciu programu PowerShell

Tworzenie nowej maszyny Wirtualnej przez dołączenie specjalne dysków zarządzanych jako dysk systemu operacyjnego. Specjalne dysku jest kopią wirtualnego dysku twardego (VHD) z istniejącej maszyny Wirtualnej, który przechowuje konta użytkowników, aplikacji i innych danych o stanie z oryginalnego maszyny Wirtualnej. 

Specjalne wirtualnego dysku twardego jest używany do utworzenia nowej maszyny Wirtualnej, nazwy komputera oryginalna maszyna wirtualna zachowuje nowej maszyny Wirtualnej. Inne informacje specyficzne dla komputera jest również przechowywać, a w niektórych przypadkach te informacje zduplikowane może spowodować problemy. Należy pamiętać o jakie rodzaje informacji specyficznych dla komputera aplikacje zależne od podczas kopiowania maszyny Wirtualnej.

Istnieje kilka opcji:
* [Użyj istniejącego dysku zarządzanego](#option-1-use-an-existing-disk). Jest to przydatne, jeśli dla maszyny Wirtualnej, który nie działa prawidłowo. Możesz usunąć ponownemu maszyny Wirtualnej zarządzanej dysku do utworzenia nowej maszyny Wirtualnej. 
* [Przekazywanie wirtualnego dysku twardego](#option-2-upload-a-specialized-vhd) 
* [Skopiuj istniejącej maszyny Wirtualnej platformy Azure przy użyciu migawek](#option-3-copy-an-existing-azure-vm)

Można także użyć portalu Azure, aby [Tworzenie nowej maszyny Wirtualnej z dysku VHD specjalne](create-vm-specialized-portal.md).

W tym temacie przedstawiono sposób użycia dysków zarządzanych. Jeśli masz wdrożenie starszych wymagający przy użyciu konta magazynu, zobacz [utworzyć Maszynę wirtualną z wirtualnego dysku twardego specjalne konta magazynu](sa-create-vm-specialized.md)

## <a name="before-you-begin"></a>Przed rozpoczęciem
Jeśli używasz programu PowerShell, upewnij się, że masz najnowszą wersję modułu programu AzureRM.Compute PowerShell. 

```powershell
Install-Module AzureRM -RequiredVersion 6.0.0
```
Aby uzyskać więcej informacji, zobacz [przechowywanie wersji programu Azure PowerShell](/powershell/azure/overview).

## <a name="option-1-use-an-existing-disk"></a>Opcja 1: Użyj istniejącego dysku

Jeśli masz maszynę Wirtualną, która została usunięta i chcesz użyć ponownie dysk systemu operacyjnego, aby utworzyć nową maszynę Wirtualną, użyj [Get-AzureRmDisk](/azure/powershell/get-azurermdisk).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzureRmDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Teraz możesz dołączyć ten dysk jako dysk systemu operacyjnego do [nowej maszyny Wirtualnej](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>Opcja 2: Przekazanie specjalne dysku VHD

Możesz przekazać wirtualnego dysku twardego z specjalne utworzone za pomocą wirtualizacji narzędzia lokalnych, takich jak funkcja Hyper-V lub maszyny Wirtualnej wyeksportowane z innej maszyny Wirtualnej.

### <a name="prepare-the-vm"></a>Przygotowywanie maszyny wirtualnej
Jeśli zamierzasz używać wirtualnego dysku twardego jako — jest, aby utworzyć nową maszynę Wirtualną, upewnij się, wykonywane są następujące czynności. 
  
  * [Przygotowywanie dysku VHD systemu Windows, aby przekazać do usługi Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Nie** generalize maszyny Wirtualnej za pomocą programu Sysprep.
  * Usuń wszystkie narzędzia wirtualizacji gościa i agentów, które są zainstalowane na maszynie Wirtualnej (np. narzędzi VMware).
  * Upewnij się, że maszyna wirtualna jest skonfigurowana do pobierania jego adres IP i ustawienia DNS za pośrednictwem protokołu DHCP. Daje to pewność, że serwer uzyskuje adres IP w sieci wirtualnej podczas uruchamiania. 


### <a name="get-the-storage-account"></a>Uzyskaj konto magazynu
Potrzebujesz konta magazynu na platformie Azure do przechowywania przekazany wirtualny dysk twardy. Możesz użyć istniejącego konta magazynu lub Utwórz nową. 

Aby wyświetlić konta dostępny magazyn, wpisz:

```powershell
Get-AzureRmStorageAccount
```

Jeśli chcesz użyć istniejącego konta magazynu, przejdź do [przekazanie dysku VHD](#upload-the-vhd-to-your-storage-account) sekcji.

Jeśli musisz utworzyć konto magazynu, wykonaj następujące kroki:

1. Potrzebna jest nazwa grupy zasobów, w którym ma zostać utworzony na koncie magazynu. Aby dowiedzieć się, wszystkie grupy zasobów, które są w ramach subskrypcji, wpisz:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Aby utworzyć grupę zasobów o nazwie *myResourceGroup* w *zachodnie stany USA* regionu, wpisz:

    ```powershell
    New-AzureRmResourceGroup `
       -Name myResourceGroup `
       -Location "West US"
    ```

2. Utwórz konto magazynu o nazwie *mojekontomagazynu* w tej grupie zasobów za pomocą [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) polecenia cmdlet:
   
    ```powershell
    New-AzureRmStorageAccount `
       -ResourceGroupName myResourceGroup `
       -Name mystorageaccount `
       -Location "West US" `
       -SkuName "Standard_LRS" `
       -Kind "Storage"
    ```

### <a name="upload-the-vhd-to-your-storage-account"></a>Przekazanie dysku VHD do konta magazynu 
Użyj [AzureRmVhd Dodaj](/powershell/module/azurerm.compute/add-azurermvhd) polecenia cmdlet w celu przekazania dysku VHD do kontenera na koncie magazynu. W tym przykładzie powoduje przekazanie pliku *myVHD.vhd* z `"C:\Users\Public\Documents\Virtual hard disks\"` na konto magazynu o nazwie *mojekontomagazynu* w *myResourceGroup* grupy zasobów. Plik jest przechowywany w kontenerze o nazwie *mojkontener* i Nowa nazwa pliku będzie *myUploadedVHD.vhd*.

```powershell
$resourceGroupName = "myResourceGroup"
$urlOfUploadedVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $resourceGroupName `
   -Destination $urlOfUploadedVhd `
   -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


W przypadku powodzenia można uzyskać odpowiedzi, która wygląda podobnie do poniższego:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

W zależności od połączenia sieciowego i rozmiar pliku VHD to polecenie może zająć trochę czasu, aby zakończyć

### <a name="create-a-managed-disk-from-the-vhd"></a>Tworzenie dysku zarządzanego z dysku VHD

Tworzenie dysku zarządzanego z specjalne dysku VHD za pomocą konta magazynu [AzureRMDisk nowy](/powershell/module/azurerm.compute/new-azurermdisk). W tym przykładzie użyto **myOSDisk1** dla nazwy dysku umieszcza dysku *Standard_LRS* magazynu i używa *https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd* jako identyfikator URI dysku VHD źródła.

Utwórz nową grupę zasobów dla nowej maszyny Wirtualnej.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Utwórz nowy dysk systemu operacyjnego na podstawie przekazanego wirtualnego dysku twardego. 

```powershell
$sourceUri = (https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd)
$osDiskName = 'myOsDisk'
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig -AccountType Standard_LRS  `
    -Location $location -CreateOption Import `
    -SourceUri $sourceUri) `
    -ResourceGroupName $destinationResourceGroup
```

## <a name="option-3-copy-an-existing-azure-vm"></a>Opcja 3: Kopiowanie istniejącej maszyny Wirtualnej Azure

Można utworzyć kopii maszyny wirtualnej używa dyskach zarządzanych przez tworzenie migawki maszyny wirtualnej, a następnie za pomocą tej migawki, aby utworzyć nową zarządzane dysku i nowej maszyny Wirtualnej.


### <a name="take-a-snapshot-of-the-os-disk"></a>Utwórz migawkę dysk systemu operacyjnego

Można wykonać migawki elementu i całej maszyny Wirtualnej (w tym wszystkie dyski) lub tylko jednego dysku. Poniższe kroki opisano sposób migawki tylko dysk systemu operacyjnego z sieci maszyny Wirtualnej przy użyciu [AzureRmSnapshot nowy](/powershell/module/azurerm.compute/new-azurermsnapshot) polecenia cmdlet. 

Ustaw niektórych parametrów. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Pobierz obiekt VM.

```powershell
$vm = Get-AzureRmVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Pobierz nazwę dysku systemu operacyjnego.

 ```powershell
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Utworzenie migawki konfiguracji. 

 ```powershell
$snapshotConfig =  New-AzureRmSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

Migawki.

```powershell
$snapShot = New-AzureRmSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Jeśli planujesz używać migawki, aby utworzyć maszynę Wirtualną, która musi być wydajnych, użyj parametru `-AccountType Premium_LRS` przy użyciu polecenia New-AzureRmSnapshot. Parametr tworzy migawkę tak, aby była przechowywana jako dysk zarządzane Premium. Dysków zarządzanych w warstwie Premium są droższe niż standardowe. Dlatego upewnij się, że naprawdę potrzebny Premium przed rozpoczęciem korzystania z parametru.

### <a name="create-a-new-disk-from-the-snapshot"></a>Utwórz nowy dysk z migawki

Tworzenie dysku zarządzanego z migawki przy użyciu [AzureRMDisk nowy](/powershell/module/azurerm.compute/new-azurermdisk). W tym przykładzie użyto *myOSDisk* dla nazwy dysku.

Utwórz nową grupę zasobów dla nowej maszyny Wirtualnej.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Ustaw nazwę dysku systemu operacyjnego. 

```powershell
$osDiskName = 'myOsDisk'
```

Tworzenie dysku zarządzanego.

```powershell
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Tworzenie nowej maszyny Wirtualnej 

Tworzenie sieci i innych zasobów maszyny Wirtualnej do użycia przez nowej maszyny Wirtualnej.

### <a name="create-the-subnet-and-vnet"></a>Utwórz podsieć i sieć wirtualna

Utwórz sieć wirtualną i podsieć [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md).

Utwórz podsieć. W tym przykładzie tworzy podsieć o nazwie **mySubNet**, w grupie zasobów **myDestinationResourceGroup**i ustawia prefiks adresu podsieci **10.0.0.0/24**.
   
```powershell
$subnetName = 'mySubNet'
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig `
   -Name $subnetName `
   -AddressPrefix 10.0.0.0/24
```

Tworzenie sieci wirtualnej. W tym przykładzie nazwa sieci wirtualnej, który ma zostać **myVnetName**, lokalizacja **zachodnie stany USA**i prefiksu adresu dla sieci wirtualnej do **10.0.0.0/16**. 
   
```powershell
$vnetName = "myVnetName"
$vnet = New-AzureRmVirtualNetwork `
   -Name $vnetName -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $singleSubnet
```    


### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Tworzenie grupy zabezpieczeń sieci i reguły protokołu RDP
Aby móc zalogować się do maszyny Wirtualnej za pomocą protokołu RDP, musisz mieć regułę zabezpieczeń, która udziela dostępu RDP do portu 3389. Ponieważ wirtualny dysk twardy dla nowej maszyny Wirtualnej został utworzony na podstawie istniejącej maszyny Wirtualnej specjalne, można użyć konta ze źródłowej maszyny wirtualnej dla protokołu RDP.

W tym przykładzie nazwa grupy NSG **myNsg** i nazwa reguły protokołu RDP do **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

Aby uzyskać więcej informacji na temat punktów końcowych i reguły NSG, zobacz [Otwieranie portów dla maszyny Wirtualnej na platformie Azure przy użyciu programu PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Tworzenie publicznego adresu IP i karty Sieciowej
Aby umożliwić komunikację z maszyną wirtualną w sieci wirtualnej, potrzebujesz [publicznego adresu IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) i interfejsu sieciowego.

Tworzenie publicznego adresu IP. W tym przykładzie ustawiono nazwa publicznego adresu IP **myIP**.
   
```powershell
$ipName = "myIP"
$pip = New-AzureRmPublicIpAddress `
   -Name $ipName -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -AllocationMethod Dynamic
```       

Utwórz kartę sieciową. W tym przykładzie nazwa karty Sieciowej jest ustawiona **myNicName**.
   
```powershell
$nicName = "myNicName"
$nic = New-AzureRmNetworkInterface -Name $nicName `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location -SubnetId $vnet.Subnets[0].Id `
   -PublicIpAddressId $pip.Id `
   -NetworkSecurityGroupId $nsg.Id
```



### <a name="set-the-vm-name-and-size"></a>Ustaw nazwę maszyny Wirtualnej i rozmiar

W tym przykładzie nazwa maszyny Wirtualnej *myVM* i rozmiaru maszyny Wirtualnej *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Dodaj kartę Sieciową
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Dodawanie dysku systemu operacyjnego 

Dodawanie dysku systemu operacyjnego do konfiguracji przy użyciu [AzureRmVMOSDisk zestawu](/powershell/module/azurerm.compute/set-azurermvmosdisk). W tym przykładzie rozmiar dysku do *128 GB* i dołącza dysków zarządzanych jako *Windows* dysku systemu operacyjnego.
 
```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Zakończenie maszyny Wirtualnej 

Utwórz maszynę Wirtualną przy użyciu [AzureRMVM nowy](/powershell/module/azurerm.compute/new-azurermvm)konfiguracje, które właśnie utworzyliśmy.

```powershell
New-AzureRmVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Jeśli to polecenie zakończyło się pomyślnie, zostanie wyświetlone dane wyjściowe w następujący sposób:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Sprawdź, czy maszyna wirtualna została utworzona
Nowo utworzona maszyna wirtualna powinna być widoczna albo w [portalu Azure](https://portal.azure.com)w obszarze **Przeglądaj** > **maszyn wirtualnych**, lub za pomocą następujących poleceń programu PowerShell:

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Kolejne kroki
Aby zalogować się do nowej maszyny wirtualnej, przejdź do maszyny Wirtualnej w [portal](https://portal.azure.com), kliknij przycisk **Connect**i Otwórz plik RDP pulpitu zdalnego. Korzystać z poświadczeń konta oryginalnego maszyny wirtualnej, aby zalogować się do nowej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [jak połączenia i zaloguj się do maszyny wirtualnej platformy Azure systemem Windows](connect-logon.md).

