---
title: Utwórz maszynę wirtualną systemu Linux przy użyciu programu PowerShell w stosie Azure | Dokumentacja firmy Microsoft
description: Utwórz maszynę wirtualną systemu Linux przy użyciu programu PowerShell w stosie Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 03EE5929-4F05-47D7-B246-EA93D6FC47CD
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 1e2dbc6020dd317e96c4116811f8e3bf87680bfb
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="quickstart-create-a-linux-server-virtual-machine-by-using-powershell-in-azure-stack"></a>Szybki Start: tworzenie maszyny wirtualnej systemu Linux serwera przy użyciu programu PowerShell w stosie Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Można utworzyć maszyny wirtualnej systemu Ubuntu Server 16.04 LTS przy użyciu programu PowerShell usługi Azure stosu. Wykonaj kroki opisane w tym artykule do utworzenia i użycia maszyny wirtualnej.  Ten artykuł zawiera także zapoznać się z procedurą:

* Połączenie z maszyną wirtualną za pomocą zdalnego klienta.
* Zainstaluj serwer sieci web NGINX i wyświetlić domyślną stronę główną.
* Wyczyścić zasoby nieużywane.

## <a name="prerequisites"></a>Wymagania wstępne

* **Obraz systemu Linux w stosie Azure marketplace**

   Domyślnie, stos Azure marketplace nie zawiera obrazu systemu Linux. Pobierz operatora stosu Azure, aby zapewnić **Ubuntu Server 16.04 LTS** obrazu należy. Operator można użyć procedury opisanej w [pobieranie elementów marketplace z platformy Azure do stosu Azure](../azure-stack-download-azure-marketplace-item.md) artykułu.

* Stos Azure wymaga określonej wersji programu Azure PowerShell do tworzenia i zarządzania zasobami. Jeśli nie jest skonfigurowany do stosu Azure PowerShell, zaloguj się do [zestaw deweloperski](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), lub z systemem Windows klient zewnętrzny w przypadku [połączone za pośrednictwem sieci VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) i wykonaj kroki, aby [ Zainstaluj](azure-stack-powershell-install.md) i [skonfigurować](azure-stack-powershell-configure-user.md) środowiska PowerShell.

* Klucz publiczny SSH z id_rsa.pub nazwa zapisany w katalogu .ssh profilu użytkownika systemu Windows. Aby uzyskać szczegółowe informacje na temat tworzenia kluczy SSH, zobacz [klucze tworzenie SSH w systemie Windows](../../virtual-machines/linux/ssh-from-windows.md).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów jest kontenerem logicznym, w której można wdrożyć aplikację i zarządzania zasobami Azure stosu. W zestawie rozwoju lub system Azure stosu zintegrowane Uruchom następujący blok kodu, aby utworzyć grupę zasobów. Wartości są przypisywane do wszystkich zmiennych w tym dokumencie, można użyć tych wartości lub przypisać nowe wartości.

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>Utwórz zasoby magazynu

Utwórz konto magazynu, a następnie utworzyć kontenera magazynu, Ubuntu Server 16.04 LTS obrazu.

```powershell
# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create a new storage account
$StorageAccount = New-AzureRMStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzureRmCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

# Create a storage container to store the virtual machine image
$containerName = 'osdisks'
$container = New-AzureStorageContainer `
  -Name $containerName `
  -Permission Blob
```

## <a name="create-networking-resources"></a>Tworzenie zasobów sieciowych

Utwórz sieć wirtualną, podsieć i publiczny adres IP. Te zasoby są używane do zapewniania łączności sieciowej do maszyny wirtualnej.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"

```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Tworzenie sieciowej grupy zabezpieczeń i reguły sieciowej grupy zabezpieczeń

Grupy zabezpieczeń sieci zabezpiecza maszyny wirtualnej za pomocą reguł ruchu przychodzącego i wychodzącego. Utwórz regułę ruchu przychodzącego dla portu 3389 przychodzących połączeń usług pulpitu zdalnego i regułę ruchu przychodzącego dla portu 80, aby zezwolić na ruch przychodzący sieci web.

```powershell
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleSSH  -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location local `
-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>Tworzenie karty sieciowej dla maszyny wirtualnej

Karta sieciowa łączy maszynę wirtualną z podsiecią, sieciową grupą zabezpieczeń i publicznym adresem IP.

```powershell
# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz konfigurację maszyny wirtualnej. Ta konfiguracja zawiera ustawienia używane podczas wdrażania maszyny wirtualnej. Na przykład: poświadczenia użytkownika, rozmiar i obraz maszyny wirtualnej.

```powershell
# Define a credential object.
$UserName='demouser'
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ($UserName, $securePassword)

# Create the virtual machine configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_D1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "16.04-LTS" `
  -Version "latest"

$osDiskName = "OsDisk"
$osDiskUri = '{0}vhds/{1}-{2}.vhd' -f `
  $StorageAccount.PrimaryEndpoints.Blob.ToString(),`
  $vmName.ToLower(), `
  $osDiskName

# Sets the operating system disk properties on a virtual machine.
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -Name $osDiskName `
  -VhdUri $OsDiskUri `
  -CreateOption FromImage | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"

# Adds the SSH Key to the virtual machine
Add-AzureRmVMSshPublicKey -VM $VirtualMachine `
 -KeyData $sshPublicKey `
 -Path "/home/azureuser/.ssh/authorized_keys"

# Create the virtual machine.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
 -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Po wdrożeniu maszyny wirtualnej, skonfiguruj połączenie SSH dla maszyny wirtualnej. Wróć do publicznego adresu IP maszyny wirtualnej za pomocą polecenia [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress?view=azurermps-4.3.1).

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Z systemu klienta przy użyciu protokołu SSH, zainstalowane Użyj następującego polecenia, aby nawiązać połączenie z maszyną wirtualną. Jeśli pracujesz w systemie Windows, możesz użyć [Putty](http://www.putty.org/) do utworzenia połączenia.

```
ssh <Public IP Address>
```

Po wyświetleniu monitu wprowadź azureuser jako użytkownik logowania. Jeśli hasło użyte podczas tworzenia kluczy SSH, należy podać hasło.

## <a name="install-the-nginx-web-server"></a>Zainstaluj serwer sieci web NGINX

Aby zaktualizować zasoby pakietu i zainstalować najnowszy pakiet NGINX, uruchom następujący skrypt:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Wyświetlanie strony powitalnej serwera NGINX

Z NGINX zainstalowane, a port 80 jest otwarty na maszynie wirtualnej można uzyskać dostępu do serwera sieci web przy użyciu publicznego adresu IP maszyny wirtualnej. Otwórz przeglądarkę sieci web i przejdź do ```http://<public IP address>```.

![Strona powitalna serwera sieci web NGINX](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Oczyszczanie zasobów, które nie jest już konieczne. Można użyć [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup?view=azurermps-4.3.1) polecenie, aby usunąć te zasoby. Aby usunąć grupę zasobów i wszystkie jego zasoby, uruchom następujące polecenie:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

Ta opcja szybkiego startu wdrożono podstawowej maszyny wirtualnej systemu Linux serwera. Aby dowiedzieć się więcej o maszynach wirtualnych Azure stosu, przejdź do [zagadnienia dotyczące maszyn wirtualnych w stosie Azure](azure-stack-vm-considerations.md).
