---
title: Przekaż generalize wirtualnego dysku twardego, aby utworzyć wiele maszyn wirtualnych na platformie Azure | Dokumentacja firmy Microsoft
description: Przekaż uogólniony wirtualny dysk twardy do konta magazynu Azure, aby utworzyć Maszynę wirtualną systemu Windows do użycia z modelu wdrażania usługi Resource Manager.
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
ms.openlocfilehash: be2ec6df33f5756dc080195bfad32e0c9079453c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31603204"
---
# <a name="upload-a-generalized-vhd-to-azure-to-create-a-new-vm"></a>Przekaż uogólniony wirtualny dysk twardy do platformy Azure do utworzenia nowej maszyny Wirtualnej

W tym temacie omówiono przekazywania uogólniony dysk niezarządzanych do konta magazynu, a następnie utworzenie nowej maszyny Wirtualnej przy użyciu przekazywanego dysku. Obraz uogólniony wirtualny dysk twardy ma wszystkie informacje osobiste Konto usunięte za pomocą programu Sysprep. 

Jeśli chcesz utworzyć Maszynę wirtualną z wirtualnego dysku twardego specjalne na koncie magazynu, zobacz [tworzenie maszyny Wirtualnej z dysku VHD specjalne](sa-create-vm-specialized.md).

W tym temacie omówiono używanie kont magazynu, ale zalecamy przeniesienie klientów do zamiast dysków zarządzanych. Aby uzyskać pełne omówienie sposobu przygotowania, przekazywanie i utworzyć nową maszynę Wirtualną za pomocą dysków zarządzanych, zobacz [Utwórz nową maszynę Wirtualną z wirtualnego dysku twardego uogólnionego przekazane do platformy Azure przy użyciu dysków zarządzanych](upload-generalized-managed.md).



## <a name="prepare-the-vm"></a>Przygotowywanie maszyny wirtualnej

Uogólniony wirtualny dysk twardy ma wszystkie informacje osobiste Konto usunięte za pomocą programu Sysprep. Jeśli planujesz używać wirtualnego dysku twardego jako obraz do tworzenia nowych maszyn wirtualnych z, wykonaj następujące czynności:
  
  * [Przygotowywanie dysku VHD systemu Windows, aby przekazać do usługi Azure](prepare-for-upload-vhd-image.md). 
  * Maszyny wirtualnej za pomocą programu Sysprep do uogólnienia

### <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Maszyny wirtualnej systemu Windows za pomocą programu Sysprep do uogólnienia
W tej sekcji przedstawiono sposób generalize maszyny wirtualnej systemu Windows do użycia jako obraz. Program Sysprep usuwa wszystkie informacje osobiste konto, między innymi i przygotowuje komputer do użycia jako obraz. Aby uzyskać więcej informacji o narzędziu Sysprep, zobacz [sposobu użycia programu Sysprep: wprowadzenie](http://technet.microsoft.com/library/bb457073.aspx).

Upewnij się, że ról serwera uruchomionych na komputerze są obsługiwane przez program Sysprep. Aby uzyskać więcej informacji, zobacz [Obsługa programu Sysprep dla ról serwera](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Jeśli korzystasz z programu Sysprep przed przekazaniem dysk VHD do platformy Azure po raz pierwszy, upewnij się, masz [przygotować maszyny Wirtualnej](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) przed uruchomieniem programu Sysprep. 
> 
> 

1. Zaloguj się do maszyny wirtualnej systemu Windows.
2. Otwórz okno Wiersz polecenia jako administrator. Zmień katalog na **%windir%\system32\sysprep**, a następnie uruchom `sysprep.exe`.
3. W **narzędzie przygotowania systemu** okno dialogowe, wybierz opcję **wprowadź systemu Out-of-Box Experience (OOBE)** i upewnij się, że **Generalize** pole wyboru jest zaznaczone.
4. W **opcje zamykania**, wybierz pozycję **zamknięcia**.
5. Kliknij przycisk **OK**.
   
    ![Uruchom program Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Po zakończeniu działania programu Sysprep, zamyka maszyny wirtualnej. 

> [!IMPORTANT]
> Nie uruchamiaj ponownie maszynę Wirtualną, aż wszystko będzie gotowe, przekazywanie wirtualnego dysku twardego na platformie Azure lub Tworzenie obrazu z maszyny Wirtualnej. Gdy maszyna wirtualna przypadkowo pobiera ponownie uruchomione, uruchom program Sysprep do uogólnienia go ponownie.
> 
> 


## <a name="upload-the-vhd"></a>Przekazywanie wirtualnego dysku twardego

Przekazanie dysku VHD do konta magazynu platformy Azure.

### <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.
Jeśli nie masz jeszcze programu PowerShell w wersji 1.4 lub nowszy zainstalowany, przeczytaj [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

1. Otwórz program Azure PowerShell i zaloguj się do konta platformy Azure. Otwiera okno podręczne wprowadzenie poświadczeń konta platformy Azure.
   
    ```powershell
    Connect-AzureRmAccount
    ```
2. Pobierz identyfikatory subskrypcji dla dostępnych subskrypcji.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Ustaw poprawną subskrypcję za pomocą identyfikatora subskrypcji. Zastąp `<subscriptionID>` o identyfikatorze poprawną subskrypcję.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

### <a name="get-the-storage-account"></a>Uzyskaj konto magazynu
Potrzebujesz konta magazynu na platformie Azure do przechowywania załadowanego obrazu maszyny Wirtualnej. Możesz użyć istniejącego konta magazynu lub Utwórz nową. 

Aby wyświetlić konta dostępny magazyn, wpisz:

```powershell
Get-AzureRmStorageAccount
```

Jeśli chcesz użyć istniejącego konta magazynu, przejdź do [przekazać obraz maszyny Wirtualnej](#upload-the-vm-vhd-to-your-storage-account) sekcji.

Jeśli musisz utworzyć konto magazynu, wykonaj następujące kroki:

1. Potrzebna jest nazwa grupy zasobów, w którym ma zostać utworzony na koncie magazynu. Aby dowiedzieć się, wszystkie grupy zasobów, które są w ramach subskrypcji, wpisz:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Aby utworzyć grupę zasobów o nazwie **myResourceGroup** w **zachodnie stany USA** regionu, wpisz:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Utwórz konto magazynu o nazwie **mojekontomagazynu** w tej grupie zasobów za pomocą [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) polecenia cmdlet:
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
 
### <a name="start-the-upload"></a>Uruchom przekazywania 

Użyj [AzureRmVhd Dodaj](/powershell/module/azurerm.compute/add-azurermvhd) polecenia cmdlet, aby przekazać obraz do kontenera na koncie magazynu. W tym przykładzie powoduje przekazanie pliku **myVHD.vhd** z `"C:\Users\Public\Documents\Virtual hard disks\"` na konto magazynu o nazwie **mojekontomagazynu** w **myResourceGroup** grupy zasobów. Plik zostaną umieszczone w kontenerze o nazwie **mojkontener** i Nowa nazwa pliku będzie **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
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

W zależności od połączenia sieciowego i rozmiar pliku VHD to polecenie może zająć trochę czasu, aby zakończyć.


## <a name="create-a-new-vm"></a>Tworzenie nowej maszyny Wirtualnej 

Przekazany wirtualny dysk twardy można teraz używać do tworzenia nowej maszyny Wirtualnej. 

### <a name="set-the-uri-of-the-vhd"></a>Ustaw identyfikator URI dysku VHD

Identyfikator URI dysku VHD do użycia ma format: https://**mojekontomagazynu**.blob.core.windows.net/**mojkontener**/**MyVhdName**VHD. W tym przykładzie wirtualnego dysku twardego o nazwie **myVHD** jest na koncie magazynu **mojekontomagazynu** w kontenerze **mojkontener**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Utwórz sieć wirtualną i podsieć [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md).

1. Utwórz podsieć. W poniższym przykładzie tworzone podsieci o nazwie **mySubnet** w grupie zasobów **myResourceGroup** z prefiksem adresu o **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Utwórz sieć wirtualną. Poniższy przykład tworzy sieć wirtualną o nazwie **myVnet** w **zachodnie stany USA** lokalizacji z prefiksem adresu o **10.0.0.0/16**.  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Tworzenie publicznego adresu IP adres i interfejsu sieciowego
Aby umożliwić komunikację z maszyną wirtualną w sieci wirtualnej, potrzebujesz [publicznego adresu IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) i interfejsu sieciowego.

1. Utwórz publiczny adres IP. W tym przykładzie jest tworzony publiczny adres IP o nazwie **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Utwórz kartę sieciową. W tym przykładzie jest tworzony karty Sieciowej o nazwie **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Tworzenie grupy zabezpieczeń sieci i reguły protokołu RDP
Aby móc zalogować się do maszyny Wirtualnej za pomocą protokołu RDP, musisz mieć regułę zabezpieczeń, która udziela dostępu RDP do portu 3389. 

W tym przykładzie tworzy grupy NSG o nazwie **myNsg** zawierający regułę o nazwie **myRdpRule** za pośrednictwem portu 3389 która zezwala na ruch RDP. Aby uzyskać więcej informacji na temat grup NSG, zobacz [Otwieranie portów dla maszyny Wirtualnej na platformie Azure przy użyciu programu PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Utwórz zmienną dla sieci wirtualnej
Utwórz zmienną dla ukończonych sieci wirtualnej. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej
Poniższy skrypt programu PowerShell przedstawia sposób ustawiania konfiguracji maszyny wirtualnej i Użyj załadowanego obrazu maszyny Wirtualnej jako źródło dla nowej instalacji.



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
    $storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>Sprawdź, czy maszyna wirtualna została utworzona
Po zakończeniu powinien zostać wyświetlony nowo utworzony maszyny Wirtualnej w ramach [portalu Azure](https://portal.azure.com) w obszarze **Przeglądaj** > **maszyn wirtualnych**, lub za pomocą następujących poleceń programu PowerShell:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Kolejne kroki
Aby zarządzać nową maszynę wirtualną w taki sposób, z programu Azure PowerShell, zobacz [zarządzania maszynami wirtualnymi przy użyciu usługi Azure Resource Manager i programu PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


