---
title: Tworzenie obrazu niezarządzane uogólniony maszyny wirtualnej na platformie Azure | Dokumentacja firmy Microsoft
description: Utwórz obraz unmanged uogólniony maszyny Wirtualnej systemu Windows na potrzeby tworzenia wielu kopii maszyny wirtualnej na platformie Azure.
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
ms.openlocfilehash: b416acd9a2a3b03502b7eca11eade9dbd56f3afe
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Jak utworzyć niezarządzane obrazu maszyny Wirtualnej z maszyny Wirtualnej platformy Azure

W tym artykule omówiono używanie kont magazynu. Zalecane jest użycie dysków zarządzanych i zarządzanych obrazów zamiast konta magazynu. Aby uzyskać więcej informacji, zobacz [Przechwyć obraz zarządzanych uogólniony maszyny wirtualnej na platformie Azure](capture-image-resource.md).

W tym artykule przedstawiono sposób tworzenia obrazu uogólniony maszyny Wirtualnej platformy Azure przy użyciu programu Azure PowerShell przy użyciu konta magazynu. Obraz można następnie używać do tworzenia inną maszynę Wirtualną. Obraz zawiera dysk systemu operacyjnego i dysków z danymi, które są dołączone do maszyny wirtualnej. Obraz nie zawiera zasobów sieci wirtualnej, więc należy skonfigurować te zasoby, podczas tworzenia nowej maszyny Wirtualnej. 

## <a name="prerequisites"></a>Wymagania wstępne
Musisz mieć wersję programu Azure PowerShell 1.0.x lub nowszej. Jeśli jeszcze nie zainstalowano programu PowerShell, przeczytaj [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview) kroki instalacji.

## <a name="generalize-the-vm"></a>Generalize maszyny Wirtualnej 
W tej sekcji przedstawiono sposób generalize maszyny wirtualnej systemu Windows do użycia jako obraz. Uogólnianie maszyny Wirtualnej powoduje usunięcie wszystkich informacji osobowych konta, między innymi i przygotowuje komputer do użycia jako obraz. Aby uzyskać więcej informacji o narzędziu Sysprep, zobacz [sposobu użycia programu Sysprep: wprowadzenie](http://technet.microsoft.com/library/bb457073.aspx).

Upewnij się, że ról serwera uruchomionych na komputerze są obsługiwane przez program Sysprep. Aby uzyskać więcej informacji, zobacz [Obsługa programu Sysprep dla ról serwera](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Podczas przekazywania dysk VHD do platformy Azure po raz pierwszy, upewnij się, że masz [przygotować maszyny Wirtualnej](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) przed uruchomieniem programu Sysprep. 
> 
> 

Można również generalize maszyny Wirtualnej systemu Linux przy użyciu `sudo waagent -deprovision+user` i przechwytywanie maszyny Wirtualnej za pomocą programu PowerShell. Aby dowiedzieć się, jak przechwycić Maszynę wirtualną za pomocą interfejsu wiersza polecenia, zobacz [generalize i przechwytywanie maszyny wirtualnej systemu Linux przy użyciu interfejsu wiersza polecenia Azure ](../linux/capture-image.md).


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

## <a name="log-in-to-azure-powershell"></a>Logowanie do programu Azure PowerShell
1. Otwórz program Azure PowerShell i zaloguj się do konta platformy Azure.
   
    ```powershell
    Connect-AzureRmAccount
    ```
   
    Otwiera okno podręczne wprowadzenie poświadczeń konta platformy Azure.
2. Pobierz identyfikatory subskrypcji dla dostępnych subskrypcji.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Ustaw poprawną subskrypcję za pomocą identyfikatora subskrypcji.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Cofnięcie przydziału maszyny Wirtualnej i ustawić stan na uogólniony

> [!IMPORTANT] 
> Nie można dodać, edytować lub usuwanie tagów z maszyny Wirtualnej, gdy jest oznaczony jako uogólniony. Jeśli chcesz dodać tag do maszyny Wirtualnej, upewnij się, że Dodaj tagi przed oznaczając je jako uogólniony.
> 

1. Cofnięcie przydziału zasobów maszyny Wirtualnej.
   
    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    *Stan* dla maszyny Wirtualnej na platformie Azure portal zmieni się z **zatrzymane** do **zatrzymane (cofnięciu przydziału)**.
2. Ustaw stan maszyny wirtualnej na **Uogólniono**. 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Sprawdź stan maszyny Wirtualnej. **OSState/uogólniony** sekcji dla maszyny Wirtualnej powinny mieć **DisplayStatus** ustawioną **uogólniona maszyna wirtualna**.  
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Tworzenie obrazu

Tworzenie obrazu niezarządzane maszyny wirtualnej w docelowy kontener magazynu przy użyciu tego polecenia. Obraz jest tworzony w tym samym kontem magazynu oryginalna maszyna wirtualna. `-Path` Parametr zapisuje kopię szablonu JSON dla źródłowej maszyny Wirtualnej na komputerze lokalnym. `-DestinationContainerName` Parametru jest nazwa kontenera, w którym chcesz przechowywać obrazów. Jeśli kontener nie istnieje, jest tworzony automatycznie.
   
```powershell
Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
Możesz uzyskać adres URL obrazu z szablonu pliku JSON. Przejdź do **zasobów** > **storageProfile** > **osDisk** > **obrazu**  >  **uri** sekcji pełną ścieżkę obrazu. Adres URL obrazu, wygląda następująco: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
   
Można również sprawdzić identyfikatora URI w portalu. Obraz jest kopiowany do kontenera o nazwie **systemu** na koncie magazynu. 

## <a name="create-a-vm-from-the-image"></a>Utwórz maszynę Wirtualną z obrazu

Teraz możesz utworzyć przynajmniej jednej maszyny wirtualnej z obrazu niezarządzane.

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
Następujące środowiska PowerShell kończy konfiguracji maszyn wirtualnych i używa niezarządzanej obrazu jako źródło dla nowej instalacji.

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

### <a name="verify-that-the-vm-was-created"></a>Sprawdź, czy maszyna wirtualna została utworzona
Po zakończeniu powinien zostać wyświetlony nowo utworzony maszyny Wirtualnej w ramach [portalu Azure](https://portal.azure.com) w obszarze **Przeglądaj** > **maszyn wirtualnych**, lub za pomocą następujących poleceń programu PowerShell:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Kolejne kroki
Aby zarządzać nową maszynę wirtualną w taki sposób, z programu Azure PowerShell, zobacz [zarządzania maszynami wirtualnymi przy użyciu usługi Azure Resource Manager i programu PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


