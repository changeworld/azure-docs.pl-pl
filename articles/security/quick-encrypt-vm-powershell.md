---
title: Szybki start — szyfrowanie maszyny wirtualnej IaaS z systemem Windows za pomocą programu Azure PowerShell | Microsoft Docs
description: Z tego przewodnika Szybki start dowiesz się, jak zaszyfrować maszynę wirtualną IaaS z systemem Windows na platformie Azure.
services: security
documentationcenter: na
author: msmbaldwin
manager: MBaldwin
ms.assetid: c8abd340-5ed4-42ec-b83f-4d679b61494d
ms.service: security
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/14/2019
ms.author: mbaldwin
ms.custom: seodec18
ms.openlocfilehash: c36f9506665f573a2e7990102af81a9de088e95b
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67501646"
---
# <a name="quickstart-encrypt-a-windows-iaas-vm-with-azure-powershell"></a>Szybki start: Szyfrowanie maszyny wirtualnej IaaS z systemem Windows za pomocą programu Azure PowerShell

Usługa Azure Disk Encryption umożliwia szyfrowanie dysków maszyn wirtualnych IaaS z systemami Windows i Linux. Rozwiązanie jest zintegrowane z usługą Azure Key Vault, aby pomóc w sterowaniu i zarządzaniu wpisami tajnymi i kluczami szyfrowania dysków. Usługa Azure Disk Encryption pozwala zagwarantować, że maszyny wirtualne są zabezpieczone w spoczynku za pomocą standardowych w branży technologii. W tym przewodniku Szybki start utworzysz maszynę wirtualną z systemem Windows Server 2016 i zaszyfrujesz dysk systemu operacyjnego.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- Windows PowerShell ISE
- Instalowanie lub aktualizowanie do [najnowszą wersję modułu Azure PowerShell](/powershell/azure/install-az-ps)
    - Wersja modułu Az musi być 1.0.0 lub nowszym. Użyj `Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path` Aby sprawdzić wersję.
- Kopia [skryptu wymagań wstępnych usługi Azure Disk Encryption](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1)
    - Jeśli masz już ten skrypt, pobierz nową kopię, ponieważ została ona ostatnio zmieniona. 
    - Użyj klawiszy **CTRL-A**, aby zaznaczyć cały tekst, a następnie użyj klawiszy **CTRL-C**, aby skopiować cały tekst do Notatnika.
    - Zapisz plik jako **ADEPrereqScript.ps1**.


## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

1. Kliknij prawym przyciskiem myszy pozycję **Windows PowerShell ISE** i kliknij pozycję **Uruchom jako administrator**.
1. W oknie **Administrator: Windows PowerShell ISE** kliknij opcję **View** (Widok), a następnie kliknij pozycję **Show Script Pane** (Pokaż okienko skryptu).
1. W okienku skryptu wpisz następujące polecenie cmdlet: 

     ```azurepowershell
      Connect-AzAccount
     ```

1. Kliknij zieloną strzałkę **Run Script** (Uruchom skrypt) lub użyj klawisza F5. 
2. Użyj logowania interakcyjnego, aby zakończyć proces łączenia z kontem platformy Azure.
3. Skopiuj **identyfikator subskrypcji** zwrócony do użycia przy uruchamianiu następnego skryptu programu PowerShell. 

## <a name="bkmk_PrereqScript"></a>Uruchamianie skryptu wymagań wstępnych usługi Azure Disk Encryption
 Plik **ADEPrereqScript.ps1** tworzy grupę zasobów i magazyn kluczy oraz ustawi zasady dostępu magazynu kluczy. Ponadto skrypt tworzy blokadę zasobów w magazynie kluczy, aby chronić je przed przypadkowym usunięciem.  

1. W oknie **Administrator: Windows PowerShell ISE** kliknij opcję **File** (Plik), a następnie kliknij pozycję **Open** (Otwórz). Przejdź do pliku **ADEPrereqScript.ps1** i kliknij go dwukrotnie. Skrypt zostanie otwarty w okienku skryptu.
2. Kliknij zieloną strzałkę **Run Script** (Uruchom skrypt) lub użyj klawisza F5, aby uruchomić skrypt. 
3. Wpisz nazwę nowej **grupy zasobów** i nazwę nowego **magazynu kluczy**. Nie używaj istniejącej grupy zasobów ani istniejącego magazynu kluczy na potrzeby tego przewodnika Szybki Start, ponieważ grupa zasobów zostanie później usunięta. 
4. Wpisz lokalizację, w której chcesz utworzyć zasoby, taką jak **EastUS**. Pobierz listę lokalizacji za pomocą polecenia `Get-AzLocation`.
5. Skopiuj **identyfikator subskrypcji**. Możesz pobrać identyfikator subskrypcji za pomocą polecenia `Get-AzSubscription`.  
6. Kliknij zieloną strzałkę **Run Script** (Uruchom skrypt). 
7. Skopiuj zwracane wartości **DiskEncryptionKeyVaultUrl** i **DiskEncryptionKeyVaultId** do późniejszego użycia.

![Skrypt wymagań wstępnych usługi Azure Disk Encryption uruchomiony w programie PowerShell ISE](media/azure-security-disk-encryption/ade-prereq-script.PNG)


## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej 
Teraz musisz utworzyć maszynę wirtualną, aby móc zaszyfrować jej dysk. Skrypt, którego użyjesz, tworzy maszynę wirtualną systemu Windows Server 2016 z 8 GB pamięci RAM i dyskiem systemu operacyjnego o rozmiarze 30 GB. 

1. Skopiuj skrypt do okienka skryptu w oknie **Administrator: Windows PowerShell ISE** i zmodyfikuj trzy pierwsze zmienne. Grupa zasobów i lokalizacja muszą być takie same jak te, których użyto dla [skryptu wymagań wstępnych](#bkmk_PrereqScript).  

   ```azurepowershell
    # Variables for common values
    $resourceGroup = "MySecureRG"
    $location = "EastUS"
    $vmName = "MySecureVM"
    
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."
    
    # Create a resource group
    New-AzResourceGroup -Name $resourceGroup -Location $location
    
    # Create a subnet configuration
    $subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
    
    # Create a virtual network
    $vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
      -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
    
    # Create a public IP address and specify a DNS name
    $pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
      -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
    
    # Create an inbound network security group rule for port 3389
    $nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
      -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
      -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
    
    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
      -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    
    # Create a virtual machine configuration
    $vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D2_v3 | `
    Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter-smalldisk -Version latest | `
    Add-AzVMNetworkInterface -Id $nic.Id
    
    # Create a virtual machine
    New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
   ```

2. Kliknij zieloną strzałkę **Run Script** (Uruchom skrypt), aby skompilować maszynę wirtualną.  


## <a name="encrypt-the-disk-of-the-vm"></a>Szyfrowanie dysku maszyny wirtualnej
Teraz, po utworzeniu i skonfigurowaniu magazynu kluczy i maszyny Wirtualnej, można zaszyfrować dysk z **AzVmDiskEncryptionExtension zestaw** polecenia cmdlet. 
 
1. Uruchom następujące polecenie cmdlet, aby zaszyfrować dysk maszyny wirtualnej:

    ```azurepowershell
     Set-AzVmDiskEncryptionExtension -ResourceGroupName "MySecureRG" -VMName "MySecureVM" `
     -DiskEncryptionKeyVaultId "<Returned by the prerequisites script>" -DiskEncryptionKeyVaultUrl "<Returned by the prerequisites script>"
     ```


1. Po zakończeniu szyfrowania możesz sprawdzić, czy dysk jest zaszyfrowany, za pomocą następującego polecenia cmdlet: 

     ```azurepowershell
     Get-AzVmDiskEncryptionStatus -ResourceGroupName "MySecureRG" -VMName "MySecureVM"
     ```
    ![Get-AzVmDiskEncryptionStatus output](media/azure-security-disk-encryption/ade-get-encryption-status.PNG)
    
## <a name="clean-up-resources"></a>Oczyszczanie zasobów
 Plik **ADEPrereqScript.ps1** tworzy blokadę zasobów w magazynie kluczy. Aby oczyścić zasoby z tego przewodnika Szybki Start, należy najpierw usunąć blokadę zasobów, a następnie usunąć grupę zasobów. 

1. Usuń blokadę zasobów z magazynu kluczy.

     ```azurepowershell
     $LockId =(Get-AzResourceLock -ResourceGroupName "MySecureRG" -ResourceName "MySecureVault" -ResourceType "Microsoft.KeyVault/vaults").LockID 
     Remove-AzResourceLock -LockID $LockId
      ```
    
2. Usuń grupę zasobów. Spowoduje to również usunięcie wszystkich zasobów w grupie. 
     ```azurepowershell
      Remove-AzResourceGroup -Name "MySecureRG"
      ```

## <a name="next-steps"></a>Kolejne kroki
Przejdź do następnego artykułu, aby dowiedzieć się więcej na temat wymagań wstępnych usługi Azure Disk Encryption dotyczących maszyn wirtualnych IaaS.

> [!div class="nextstepaction"]
> [Wymagania wstępne dotyczące usługi Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md)
