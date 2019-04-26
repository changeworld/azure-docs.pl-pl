---
title: Tworzenie obrazu niezarządzanego uogólnionej maszyny wirtualnej na platformie Azure | Dokumentacja firmy Microsoft
description: Utwórz obraz unmanged uogólnionej maszyny Wirtualnej Windows służące do tworzenia wielu kopii maszyny Wirtualnej na platformie Azure.
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
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: e1ed419892412c1fb9334fed74b82c53154723ed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60252415"
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Sposób tworzenia obrazu maszyny Wirtualnej niezarządzanej maszyny wirtualnej platformy Azure

W tym artykule opisano użycie kont magazynu. Firma Microsoft zaleca używanie dysków zarządzanych i obrazy zarządzanych zamiast konta magazynu. Aby uzyskać więcej informacji, zobacz [przechwytywania obrazu zarządzanego uogólnionej maszyny Wirtualnej na platformie Azure](capture-image-resource.md).

W tym artykule pokazano, jak używać programu Azure PowerShell do tworzenia obrazu uogólnionej maszyny Wirtualnej platformy Azure przy użyciu konta magazynu. Obraz, który umożliwia następnie utworzyć inną maszynę Wirtualną. Obraz zawiera dysk systemu operacyjnego i dysków z danymi, które są dołączone do maszyny wirtualnej. Obraz, który nie zawiera zasoby sieci wirtualnej, dlatego należy skonfigurować te zasoby podczas tworzenia nowej maszyny Wirtualnej. 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="generalize-the-vm"></a>Uogólnianie maszyny Wirtualnej 
W tej sekcji pokazano, jak uogólnić maszyny wirtualnej Windows do użycia jako obraz. Uogólnianie maszyny Wirtualnej powoduje usunięcie wszystkich danych konta osobistego, między innymi i przygotowuje maszynę do użycia jako obraz. Aby uzyskać więcej informacji na temat narzędzia Sysprep, zobacz [Używanie narzędzia Sysprep: wprowadzenie](https://technet.microsoft.com/library/bb457073.aspx).

Upewnij się, że role serwera uruchomionego na maszynie są obsługiwane przez program Sysprep. Aby uzyskać więcej informacji, zobacz [Obsługa narzędzia Sysprep dla ról serwera](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Podczas przekazywania wirtualnego dysku twardego na platformie Azure po raz pierwszy, upewnij się, że masz [przygotować maszyny Wirtualnej](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) przed uruchomieniem programu Sysprep. 
> 
> 

Możesz również uogólnić maszyny Wirtualnej systemu Linux przy użyciu `sudo waagent -deprovision+user` i przechwytywanie maszyny Wirtualnej za pomocą programu PowerShell. Aby dowiedzieć się, jak przechwycić Maszynę wirtualną przy użyciu interfejsu wiersza polecenia, zobacz [jak Uogólnij i Przechwyć maszynę wirtualną systemu Linux przy użyciu wiersza polecenia platformy Azure](../linux/capture-image.md).


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

## <a name="log-in-to-azure-powershell"></a>Logowanie do programu Azure PowerShell
1. Otwórz program PowerShell platformy Azure i zaloguj się do konta platformy Azure.
   
    ```powershell
    Connect-AzAccount
    ```
   
    Wprowadzenie poświadczeń konta platformy Azure zostanie otwarte okno podręczne.
2. Pobierz identyfikatory subskrypcji dla dostępnych subskrypcji.
   
    ```powershell
    Get-AzSubscription
    ```
3. Ustaw poprawną subskrypcję za pomocą identyfikatora subskrypcji.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Cofanie przydziału maszyny Wirtualnej i ustawić stan uogólniony

> [!IMPORTANT] 
> Nie można dodać, edytować ani usuwać znaczników z maszyny Wirtualnej z systemem, gdy jest ona oznaczona jako uogólniona. Jeśli chcesz dodać tag do maszyny Wirtualnej, upewnij się, że możesz dodawać znaczników, zanim oznaczysz je jako uogólniona.
> 

1. Cofnij przydział zasobów maszyny Wirtualnej.
   
    ```powershell
    Stop-AzVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    *Stan* dla maszyny Wirtualnej w witrynie Azure portal zmieni się z **zatrzymane** do **zatrzymane (cofnięty przydział)**.
2. Ustaw stan maszyny wirtualnej do **Uogólniono**. 
   
    ```powershell
    Set-AzVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Sprawdź stan maszyny Wirtualnej. **OSState/uogólniony** sekcji dla maszyny Wirtualnej powinien mieć **DisplayStatus** równa **uogólniona maszyna wirtualna**.  
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Tworzenie obrazu

Tworzenie obrazu niezarządzanego maszyny wirtualnej w docelowy kontener magazynu za pomocą tego polecenia. Obraz, który jest tworzony w tym samym koncie magazynu, ponieważ oryginalna maszyna wirtualna. `-Path` Parametr zapisuje kopię szablonu JSON dla źródłowej maszyny Wirtualnej na komputerze lokalnym. `-DestinationContainerName` Parametr jest nazwą kontenera, w którym chcesz przechowywać swoje obrazy. Jeśli kontener nie istnieje, zostanie utworzony automatycznie.
   
```powershell
Save-AzVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
Adres URL obrazu można uzyskać za pomocą szablonu pliku JSON. Przejdź do **zasobów** > **storageProfile** > **osDisk** > **obraz**  >  **uri** sekcji pełną ścieżkę obrazu. Adres URL obrazu wygląda następująco: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
   
Można również sprawdzić identyfikatora URI w portalu. Obraz, który jest kopiowany do kontenera o nazwie **systemu** na koncie magazynu. 

## <a name="create-a-vm-from-the-image"></a>Utwórz maszynę wirtualną na podstawie obrazu

Teraz możesz utworzyć co najmniej jedną maszynę wirtualną z obrazu niezarządzanego.

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
    $location = "West US"
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
Następujące polecenie programu PowerShell ukończenia konfiguracji maszyny wirtualnej i używa obrazu niezarządzanego jako źródło dla nowej instalacji.

</br>

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

### <a name="verify-that-the-vm-was-created"></a>Sprawdź, czy maszyna wirtualna została utworzona
Po zakończeniu powinien zostać wyświetlony nowo utworzonej maszyny Wirtualnej w [witryny Azure portal](https://portal.azure.com) w obszarze **Przeglądaj** > **maszyn wirtualnych**, lub używając następujące polecenie programu PowerShell polecenia:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Kolejne kroki
Aby zarządzać swoją nową maszyną wirtualną przy użyciu programu Azure PowerShell, zobacz [Zarządzanie maszynami wirtualnymi przy użyciu usługi Azure Resource Manager i programu PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


