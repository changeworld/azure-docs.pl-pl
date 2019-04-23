---
title: Przekaż generalize wirtualnego dysku twardego do tworzenia wielu maszyn wirtualnych na platformie Azure | Dokumentacja firmy Microsoft
description: Przekazywanie uogólnionego wirtualnego dysku twardego do konta usługi Azure storage, aby utworzyć maszyny Wirtualnej z systemem Windows za pomocą modelu wdrażania usługi Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: cd81eb3d62332bba67f0056a6f7b49279bc50c4f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59788816"
---
# <a name="upload-a-generalized-vhd-to-azure-to-create-a-new-vm"></a>Przekazywanie uogólnionego wirtualnego dysku twardego do systemu Azure do tworzenia nowej maszyny Wirtualnej

W tym temacie omówiono przekazywanie uogólnionego dysku niezarządzanego do konta magazynu, a następnie utworzenie nowej maszyny Wirtualnej przy użyciu przekazanych dysku. Uogólnionego obrazu dysku VHD miał wszystkie informacje osobiste Konto usunięte za pomocą programu Sysprep. 

Jeśli chcesz utworzyć maszynę Wirtualną z wyspecjalizowanego wirtualnego dysku twardego w ramach konta magazynu, zobacz [tworzenie maszyny Wirtualnej na podstawie wyspecjalizowanego wirtualnego dysku twardego](sa-create-vm-specialized.md).

W tym temacie omówiono używanie kont magazynu, ale zalecamy klientom przenoszenie zamiast Managed Disks. Aby uzyskać pełne omówienie sposobu przygotowania, przekazywanie i tworzenie nowej maszyny Wirtualnej przy użyciu dysków zarządzanych, zobacz [Utwórz nową maszynę Wirtualną z uogólnionego wirtualnego dysku twardego przekazany na platformę Azure przy użyciu dysków Managed Disks](upload-generalized-managed.md).

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="prepare-the-vm"></a>Przygotowywanie maszyny wirtualnej

Uogólnionego wirtualnego dysku twardego miał wszystkie informacje osobiste Konto usunięte za pomocą programu Sysprep. Jeśli zamierzasz używać wirtualnego dysku twardego jako obraz, aby tworzyć nowe maszyny wirtualne z, należy:
  
  * [Przygotowywanie wirtualnego dysku twardego Windows do przekazania na platformę Azure](prepare-for-upload-vhd-image.md). 
  * Uogólnianie maszyny wirtualnej za pomocą programu Sysprep

### <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Uogólnij maszynę wirtualną Windows, za pomocą programu Sysprep
W tej sekcji pokazano, jak uogólnić maszyny wirtualnej Windows do użycia jako obraz. Narzędzie Sysprep między innymi usuwa wszystkie informacje osobiste związane z kontem i przygotowuje maszynę do używania jako obraz. Aby uzyskać więcej informacji na temat narzędzia Sysprep, zobacz [Używanie narzędzia Sysprep: wprowadzenie](https://technet.microsoft.com/library/bb457073.aspx).

Upewnij się, że role serwera uruchomionego na maszynie są obsługiwane przez program Sysprep. Aby uzyskać więcej informacji, zobacz [Obsługa narzędzia Sysprep dla ról serwera](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Jeśli używasz narzędzia Sysprep przed przekazaniem wirtualnego dysku twardego na platformie Azure po raz pierwszy, upewnij się, że masz [przygotować maszyny Wirtualnej](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) przed uruchomieniem programu Sysprep. 
> 
> 

1. Zaloguj się do maszyny wirtualnej Windows.
2. Otwórz okno wiersza polecenia jako administrator. Zmień katalog na **%windir%\system32\sysprep**, a następnie uruchom plik `sysprep.exe`.
3. W oknie dialogowym **Narzędzie przygotowywania systemu** wybierz pozycję **Włącz systemowy tryb OOBE** i upewnij się, że pole wyboru **Uogólnij** jest zaznaczone.
4. W **opcje zamykania**, wybierz opcję **zamknięcia**.
5. Kliknij przycisk **OK**.
   
    ![Uruchom program Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Gdy narzędzie Sysprep zakończy działanie, maszyna wirtualna zostanie wyłączona. 

> [!IMPORTANT]
> Nie uruchamiaj ponownie maszynę Wirtualną, aż wszystko będzie gotowe przekazanie dysku VHD na platformie Azure lub utworzenie obrazu z maszyny Wirtualnej. Jeśli maszyna wirtualna przypadkowo zostanie ponownie uruchomiony, uruchom program Sysprep do uogólnienia go ponownie.
> 
> 


## <a name="upload-the-vhd"></a>Przekazywanie wirtualnego dysku twardego

Przekazanie dysku VHD do konta usługi Azure storage.

### <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.
Jeśli nie masz jeszcze programu PowerShell w wersji 1.4 lub nowszej zainstalowany, przeczytaj [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).

1. Otwórz program PowerShell platformy Azure i zaloguj się do konta platformy Azure. Wprowadzenie poświadczeń konta platformy Azure zostanie otwarte okno podręczne.
   
    ```powershell
    Connect-AzAccount
    ```
2. Pobierz identyfikatory subskrypcji dla dostępnych subskrypcji.
   
    ```powershell
    Get-AzSubscription
    ```
3. Ustaw poprawną subskrypcję za pomocą identyfikatora subskrypcji. Zastąp `<subscriptionID>` o identyfikatorze poprawną subskrypcję.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

### <a name="get-the-storage-account"></a>Pobierz konto magazynu
Wymagane jest konto magazynu na platformie Azure do przechowywania przekazanego obrazu maszyny Wirtualnej. Możesz użyć istniejącego konta magazynu lub Utwórz nową. 

Aby wyświetlić konta dostępnego magazynu, wpisz:

```powershell
Get-AzStorageAccount
```

Jeśli chcesz użyć istniejącego konta magazynu, przejdź do przekazywania sekcję obrazu maszyny Wirtualnej.

Jeśli musisz utworzyć konto magazynu, wykonaj następujące czynności:

1. Potrzebna jest nazwa grupy zasobów, tworzona na koncie magazynu. Aby dowiedzieć się, wszystkie grupy zasobów, które znajdują się w Twojej subskrypcji, wpisz:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Aby utworzyć grupę zasobów o nazwie **myResourceGroup** w **zachodnie stany USA** regionu, wpisz:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Utwórz konto magazynu o nazwie **mystorageaccount** w tej grupie zasobów przy użyciu [New AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) polecenia cmdlet:
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
 
### <a name="start-the-upload"></a>Rozpocznij przekazywanie 

Użyj [AzVhd Dodaj](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) polecenia cmdlet w celu przekazania obrazu do kontenera na koncie magazynu. Ten przykładowy przekazuje plik **myVHD.vhd** z `"C:\Users\Public\Documents\Virtual hard disks\"` na konto magazynu o nazwie **mystorageaccount** w **myResourceGroup** grupy zasobów. Plik zostanie umieszczony w kontenerze o nazwie **mycontainer** nową nazwę pliku. zostanie ona **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Jeśli to się powiedzie, otrzymasz odpowiedź, która wygląda podobnie do następującej:

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

W zależności od połączenia sieciowego i rozmiar pliku wirtualnego dysku twardego tego polecenia może potrwać trochę czasu.


## <a name="create-a-new-vm"></a>Utwórz nową maszynę Wirtualną 

Przekazany wirtualny dysk twardy można teraz używać do tworzenia nowej maszyny Wirtualnej. 

### <a name="set-the-uri-of-the-vhd"></a>Ustaw identyfikator URI wirtualnego dysku twardego

Identyfikator URI wirtualnego dysku twardego do użycia jest w formacie: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**VHD. W tym przykładzie wirtualnego dysku twardego o nazwie **myVHD** jest na koncie magazynu **mystorageaccount** w kontenerze **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Tworzenie sieci wirtualnej i podsieci [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md).

1. Utwórz podsieć. Poniższy przykład tworzy podsieć o nazwie **mySubnet** w grupie zasobów **myResourceGroup** z prefiksem adresu **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Utwórz sieć wirtualną. Poniższy przykład tworzy sieć wirtualną o nazwie **myVnet** w **zachodnie stany USA** lokalizacji z prefiksem adresu **10.0.0.0/16**.  
   
    ```powershell
    $location = "WestUS"
    $vnetName = "myVnet"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Tworzenie publicznego adresu IP adres i interfejsu sieciowego
Aby umożliwić komunikację z maszyną wirtualną w sieci wirtualnej, potrzebujesz [publicznego adresu IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) i interfejsu sieciowego.

1. Tworzenie publicznego adresu IP. W tym przykładzie tworzy publiczny adres IP o nazwie **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Utwórz kartę sieciową. W tym przykładzie tworzy kartę Sieciową o nazwie **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Tworzenie sieciowej grupy zabezpieczeń i regułę protokołu RDP
Aby można było zalogować się do maszyny Wirtualnej przy użyciu protokołu RDP, musisz mieć reguły zabezpieczeń, która umożliwia dostęp do portu 3389 protokołu RDP. 

Ten przykład tworzy sieciową grupę zabezpieczeń o nazwie **myNsg** zawierający regułę o nazwie **myRdpRule** która zezwala na ruch RDP przez port 3389. Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [Otwieranie portów dla maszyny Wirtualnej na platformie Azure przy użyciu programu PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Utwórz zmienną dla sieci wirtualnej
Utwórz zmienną dla ukończonych sieci wirtualnej. 

```powershell
$vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej
Poniższy skrypt programu PowerShell przedstawia sposób ustawiania konfiguracji maszyny wirtualnej i używanie przekazanego obrazu maszyny Wirtualnej jako źródło dla nowej instalacji.



```powershell
# Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential

    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"

    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"

    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"

    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"

    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"

    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant
    # storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage,
    # Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"

    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>Sprawdź, czy maszyna wirtualna została utworzona
Po zakończeniu powinien zostać wyświetlony nowo utworzonej maszyny Wirtualnej w [witryny Azure portal](https://portal.azure.com) w obszarze **Przeglądaj** > **maszyn wirtualnych**, lub używając następujące polecenie programu PowerShell polecenia:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Kolejne kroki
Aby zarządzać swoją nową maszyną wirtualną przy użyciu programu Azure PowerShell, zobacz [Zarządzanie maszynami wirtualnymi przy użyciu usługi Azure Resource Manager i programu PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


