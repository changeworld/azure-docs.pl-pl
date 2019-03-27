---
title: Tworzenie maszyny Wirtualnej z systemem Windows na podstawie wyspecjalizowanego wirtualnego dysku twardego na platformie Azure | Dokumentacja firmy Microsoft
description: Utwórz nową maszynę Wirtualną Windows, dołączając wyspecjalizowanego dysku zarządzanego jako dysk systemu operacyjnego za pomocą modelu wdrażania usługi Resource Manager.
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
ms.date: 10/10/2018
ms.author: cynthn
ms.openlocfilehash: a5e891d334bc15e0b03facb1f1f5ed8a511cda55
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58443885"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>Tworzenie maszyny Wirtualnej z systemem Windows na podstawie wyspecjalizowanego dysku za pomocą programu PowerShell

Tworzenie nowej maszyny Wirtualnej przez dołączenie wyspecjalizowanego dysku zarządzanego jako dysk systemu operacyjnego. Wyspecjalizowanego dysku jest kopii wirtualnego dysku twardego (VHD) z istniejącej maszyny Wirtualnej, która zawiera konta użytkowników, aplikacji i innych danych o stanie z oryginalną maszynę Wirtualną. 

Korzystając z wyspecjalizowanego wirtualnego dysku twardego do tworzenia nowej maszyny Wirtualnej, nowa maszyna wirtualna zachowuje nazwę komputera w oryginalnej maszyny Wirtualnej. Inne informacje specyficzne dla komputera są także przechowywane i w niektórych przypadkach te informacje zduplikowane może to spowodować problemy. Podczas kopiowania maszyny Wirtualnej, należy pamiętać o jakie rodzaje informacji specyficznych dla komputera zależą swoje aplikacje.

Istnieje kilka opcji:
* [Użyj istniejącego dysku zarządzanego](#option-1-use-an-existing-disk). Ta opcja jest przydatna, jeśli masz maszynę Wirtualną, która nie działa prawidłowo. Można usunąć maszynę Wirtualną, a następnie ponowne użycie dysku zarządzanego do tworzenia nowej maszyny Wirtualnej. 
* [Przekazywanie wirtualnego dysku twardego](#option-2-upload-a-specialized-vhd) 
* [Kopiowanie istniejących maszyn wirtualnych platformy Azure przy użyciu migawek](#option-3-copy-an-existing-azure-vm)

Można również użyć witryny Azure portal do [Tworzenie nowej maszyny Wirtualnej na podstawie wyspecjalizowanego wirtualnego dysku twardego](create-vm-specialized-portal.md).

W tym artykule przedstawiono sposób używania dysków zarządzanych. Jeśli masz wdrożenie starszego wymagającym, przy użyciu konta magazynu, zobacz [tworzenie maszyny Wirtualnej na podstawie wyspecjalizowanego wirtualnego dysku twardego w ramach konta magazynu](sa-create-vm-specialized.md).

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="option-1-use-an-existing-disk"></a>Opcja 1: Użyj istniejącego dysku

Jeśli masz maszyny Wirtualnej, który został usunięty i chcesz ponownie użyć dysku systemu operacyjnego, aby utworzyć nową maszynę Wirtualną, użyj [Get AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Można teraz dołączać ten dysk jako dysk systemu operacyjnego [nowej maszyny Wirtualnej](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>Opcja 2: Przekazywanie wyspecjalizowanego wirtualnego dysku twardego

Możesz przekazać wirtualny dysk twardy z wyspecjalizowanej maszyny Wirtualnej utworzone za pomocą wirtualizacji narzędzia lokalnych, takich jak funkcji Hyper-V lub maszyny Wirtualnej wyeksportowane z innej chmury.

### <a name="prepare-the-vm"></a>Przygotowywanie maszyny wirtualnej
Użyj dysku VHD jako — jest utworzenie nowej maszyny Wirtualnej. 
  
  * [Przygotowywanie wirtualnego dysku twardego Windows do przekazania na platformę Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Nie** uogólnianie maszyny Wirtualnej przy użyciu narzędzia Sysprep.
  * Usuń wszelkie gościa wirtualizacji narzędzi i agentów, które są zainstalowane na maszynie Wirtualnej (np. narzędzi VMware).
  * Upewnij się, że maszyna wirtualna jest skonfigurowana, aby uzyskać adres IP i ustawienia serwera DNS z serwera DHCP. Daje to gwarancję, że serwer uzyskuje adres IP w sieci wirtualnej podczas uruchamiania. 


### <a name="get-the-storage-account"></a>Pobierz konto magazynu
Musisz mieć konto magazynu, na platformie Azure do przechowywania przekazanego wirtualnego dysku twardego. Możesz użyć istniejącego konta magazynu lub Utwórz nową. 

Pokaż konta dostępnego magazynu.

```powershell
Get-AzStorageAccount
```

Aby użyć istniejącego konta magazynu, przejdź do [przekazanie dysku VHD](#upload-the-vhd-to-your-storage-account) sekcji.

Tworzenie konta magazynu

1. Będzie potrzebna jest nazwa grupy zasobów, w którym zostanie utworzone konto magazynu. Użyj Get-AzResourceGroup Zobacz wszystkie grupy zasobów, które znajdują się w Twojej subskrypcji.
   
    ```powershell
    Get-AzResourceGroup
    ```

    Utwórz grupę zasobów o nazwie *myResourceGroup* w *zachodnie stany USA* regionu.

    ```powershell
    New-AzResourceGroup `
       -Name myResourceGroup `
       -Location "West US"
    ```

2. Utwórz konto magazynu o nazwie *mystorageaccount* w grupie zasobów przy użyciu [New AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) polecenia cmdlet.
   
    ```powershell
    New-AzStorageAccount `
       -ResourceGroupName myResourceGroup `
       -Name mystorageaccount `
       -Location "West US" `
       -SkuName "Standard_LRS" `
       -Kind "Storage"
    ```

### <a name="upload-the-vhd-to-your-storage-account"></a>Przekazanie dysku VHD do konta magazynu 
Użyj [AzVhd Dodaj](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) polecenia cmdlet do przekazania dysku VHD do kontenera na koncie magazynu. Ten przykładowy przekazuje plik *myVHD.vhd* z "dyski twarde C:\Users\Public\Documents\Virtual\" na konto magazynu o nazwie *mystorageaccount* w  *myResourceGroup* grupy zasobów. Plik jest przechowywany w kontenerze o nazwie *mycontainer* nową nazwę pliku. zostanie ona *myUploadedVHD.vhd*.

```powershell
$resourceGroupName = "myResourceGroup"
$urlOfUploadedVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $resourceGroupName `
   -Destination $urlOfUploadedVhd `
   -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


W przypadku pomyślnego polecenia otrzymasz odpowiedź, która wygląda podobnie do następującej:

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

To polecenie może zająć trochę czasu w zależności od połączenia sieciowego i rozmiar pliku wirtualnego dysku twardego.

### <a name="create-a-managed-disk-from-the-vhd"></a>Tworzenie dysku zarządzanego na podstawie wirtualnego dysku twardego

Tworzenie dysku zarządzanego na podstawie wyspecjalizowanego wirtualnego dysku twardego w ramach konta magazynu przy użyciu [New AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk). W tym przykładzie użyto *myOSDisk1* nazwę dysku, umieszcza dysku w *Standard_LRS* magazynu i używa *https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd* jako identyfikator URI dysku VHD źródła.

Utwórz nową grupę zasobów dla nowej maszyny Wirtualnej.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Utwórz nowy dysk systemu operacyjnego z przekazanego wirtualnego dysku twardego. 

```powershell
$sourceUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd'
$osDiskName = 'myOsDisk'
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig -AccountType Standard_LRS  `
    -Location $location -CreateOption Import `
    -SourceUri $sourceUri) `
    -ResourceGroupName $destinationResourceGroup
```

## <a name="option-3-copy-an-existing-azure-vm"></a>Opcja 3: Kopiowanie istniejących maszyn wirtualnych platformy Azure

Można utworzyć kopii maszyn wirtualnych, które korzysta z dysków zarządzanych przez wykonanie migawki maszyny wirtualnej, a następnie za pomocą tej migawki do utworzenia nowego zarządzanego dysku i nową maszynę Wirtualną.


### <a name="take-a-snapshot-of-the-os-disk"></a>Tworzenie migawki dysku systemu operacyjnego

Można utworzyć migawkę całej maszyny Wirtualnej (w tym wszystkie dyski) lub tylko jednego dysku. Poniższe kroki pokazują jak utworzyć migawkę po prostu dysku systemu operacyjnego maszyny wirtualnej za pomocą [New AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) polecenia cmdlet. 

Najpierw należy ustawić niektóre parametry. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Pobierz obiekt maszyny Wirtualnej.

```powershell
$vm = Get-AzVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Pobierz nazwę dysku systemu operacyjnego.

 ```powershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Utwórz konfigurację migawki. 

 ```powershell
$snapshotConfig =  New-AzSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

Migawki.

```powershell
$snapShot = New-AzSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Aby użyć tej migawki do utworzenia maszyny Wirtualnej, który ma zostać o wysokiej wydajności, Dodaj parametr `-AccountType Premium_LRS` do polecenia New-AzSnapshotConfig. Ten parametr tworzy migawkę, aby umożliwić są przechowywane jako dysku zarządzanego w warstwie Premium. Premium Managed Disks to bardziej kosztowne niż standardowe, dlatego upewnij się, że musisz mieć Premium przed rozpoczęciem korzystania z tego parametru.

### <a name="create-a-new-disk-from-the-snapshot"></a>Tworzenie nowego dysku z migawki

Tworzenie dysku zarządzanego na podstawie migawki przy użyciu [New AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk). W tym przykładzie użyto *myOSDisk* dla nazwy dysku.

Utwórz nową grupę zasobów dla nowej maszyny Wirtualnej.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Ustaw nazwę dysku systemu operacyjnego. 

```powershell
$osDiskName = 'myOsDisk'
```

Tworzenie dysku zarządzanego.

```powershell
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Utwórz nową maszynę Wirtualną 

Tworzenie sieci i innych zasobów maszyny Wirtualnej, który będzie używany przez nową maszynę Wirtualną.

### <a name="create-the-subnet-and-virtual-network"></a>Utwórz podsieć i sieć wirtualną

Tworzenie [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md) i podsieć dla maszyny Wirtualnej.

1. Utwórz podsieć. W tym przykładzie tworzy podsieć o nazwie *mySubNet*, w grupie zasobów *myDestinationResourceGroup*i ustawia prefiks adresu podsieci *10.0.0.0/24*.
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. Utwórz sieć wirtualną. W tym przykładzie nazwa sieci wirtualnej w *myVnetName*, lokalizację *zachodnie stany USA*i prefiksu adresu dla sieci wirtualnej do *10.0.0.0/16*. 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Tworzenie sieciowej grupy zabezpieczeń i regułę protokołu RDP
Aby można było zalogować się do maszyny Wirtualnej przy użyciu protokołu remote desktop protocol (RDP), musisz mieć reguły zabezpieczeń, która umożliwia dostęp do portu 3389 protokołu RDP. W tym przykładzie dysk VHD dla nowej maszyny Wirtualnej został utworzony z istniejącej maszyny Wirtualnej specjalistyczne, aby można było używać konta, które istniały na źródłowej maszynie wirtualnej, dla protokołu RDP.

W tym przykładzie nazwa Sieciowej grupy zabezpieczeń sieci w *myNsg* i nazwa reguły protokołu RDP do *myRdpRule*.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

Aby uzyskać więcej informacji na temat punktów końcowych i reguł sieciowej grupy zabezpieczeń, zobacz [Otwieranie portów dla maszyny Wirtualnej na platformie Azure przy użyciu programu PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Tworzenie publicznego adresu IP i karty Sieciowej
Aby umożliwić komunikację z maszyną wirtualną w sieci wirtualnej, musisz mieć [publiczny adres IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) i interfejsu sieciowego.

1. Tworzenie publicznego adresu IP. W tym przykładzie nazwa publicznego adresu IP jest ustawiona na *myIP*.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. Utwórz kartę sieciową. W tym przykładzie nazwa karty interfejsu Sieciowego jest ustawiona na *myNicName*.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>Ustaw nazwę maszyny Wirtualnej i rozmiar

W tym przykładzie nazwa maszyny Wirtualnej w *myVM* i rozmiar maszyny Wirtualnej do *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Dodawanie karty Sieciowej
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Dodaj dysk systemu operacyjnego 

Dodaj dysk systemu operacyjnego do konfiguracji za pomocą [AzVMOSDisk zestaw](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk). W tym przykładzie rozmiar dysku, który ma *128 GB* i dołączenie dysku zarządzanego jako *Windows* dysku systemu operacyjnego.
 
```powershell
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Wykonaj maszyny Wirtualnej 

Tworzenie maszyny Wirtualnej przy użyciu [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) z konfiguracjami, które właśnie utworzyliśmy.

```powershell
New-AzVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Jeśli polecenie zakończy się pomyślnie, zostanie wyświetlone następujące dane wyjściowe:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Sprawdź, czy maszyna wirtualna została utworzona
Powinien zostać wyświetlony nowo utworzonej maszyny Wirtualnej albo w [witryny Azure portal](https://portal.azure.com) w obszarze **Przeglądaj** > **maszyn wirtualnych**, lub za pomocą następujących poleceń programu PowerShell.

```powershell
$vmList = Get-AzVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Kolejne kroki
Zaloguj się do swojej nowej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [jak połączyć i zaloguj się na maszynie wirtualnej platformy Azure, systemem Windows](connect-logon.md).

