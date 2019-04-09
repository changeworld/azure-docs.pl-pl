---
title: Utwórz maszynę wirtualną Windows przy użyciu programu PowerShell w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Utwórz maszynę wirtualną Windows przy użyciu programu PowerShell w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/14/2019
ms.author: mabrigg
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: feab6bcaf23852ae00a1cd09f9ad30cd6397bb99
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59276205"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-powershell-in-azure-stack"></a>Szybki Start: tworzenie maszyny wirtualnej systemu Windows Server przy użyciu programu PowerShell w usłudze Azure Stack

*Dotyczy Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Można utworzyć maszynę wirtualną systemu Windows Server 2016 za pomocą usługi Azure Stack PowerShell. Wykonaj kroki opisane w tym artykule, aby utworzyć maszynę wirtualną. Ten artykuł zawiera również kroki, aby:

* Łączenie z maszyną wirtualną za pomocą zdalnego klienta.
* Zainstaluj serwer sieci web usług IIS i wyświetlić domyślną stronę główną.
* Czyszczenie zasobów.

> [!NOTE]
>  Można wykonać kroki opisane w tym artykule, Azure Stack Development Kit lub z klienta zewnętrznego z systemem Windows, jeśli są połączone za pośrednictwem sieci VPN.

## <a name="prerequisites"></a>Wymagania wstępne

* Upewnij się, że operator usługi Azure Stack został dodany **systemu Windows Server 2016** obrazu portalu Marketplace usługi Azure Stack.

* Usługa Azure Stack wymaga określonej wersji programu Azure PowerShell do tworzenia i zarządzania zasobami. Jeśli nie masz skonfigurowany dla usługi Azure Stack PowerShell, wykonaj kroki, aby [zainstalować](azure-stack-powershell-install.md) programu PowerShell.

* W programie Azure PowerShell stosu skonfigurować należy połączyć się z środowiskiem usługi Azure Stack. Aby uzyskać instrukcje, zobacz [nawiązywanie połączenia z usługi Azure Stack przy użyciu programu PowerShell jako użytkownik](azure-stack-powershell-configure-user.md).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener, do której usługa Azure Stack zasoby są wdrażane i zarządzane. Z Twojego zestawu SDK lub system zintegrowany z usługi Azure Stack Uruchom następujący blok kodu, aby utworzyć grupę zasobów. Wartości są przypisywane do wszystkich zmiennych w tym dokumencie, można użyć tych wartości lub przypisać nowe wartości.

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>Tworzenie zasobów magazynu

Utwórz konto magazynu i kontener magazynu do przechowywania obrazu systemu Windows Server 2016.

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

Sieciowa grupa zabezpieczeń chroni maszynę wirtualną za pomocą reguł ruchu przychodzącego i wychodzącego. Pozwala utworzyć regułę ruchu przychodzącego dla portu 3389, aby zezwolić na połączenia przychodzące pulpitu zdalnego i regułę ruchu przychodzącego dla portu 80, aby zezwolić na przychodzący ruch sieci web.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleRDP `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleWWW `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP,$nsgRuleWeb
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

Utwórz konfigurację maszyny wirtualnej. Ta konfiguracja zawiera ustawienia używane podczas wdrażania maszyny wirtualnej. Na przykład: poświadczenia, rozmiaru i obrazu maszyny wirtualnej.

```powershell
# Define a credential object to store the username and password for the virtual machine
$UserName='demouser'
$Password='Password@123'| ConvertTo-SecureString -Force -AsPlainText
$Credential=New-Object PSCredential($UserName,$Password)

# Create the virtual machine configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_A1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Windows `
  -ComputerName "MainComputer" `
  -Credential $Credential

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "MicrosoftWindowsServer" `
  -Offer "WindowsServer" `
  -Skus "2016-Datacenter" `
  -Version "latest"

# Sets the operating system disk properties on a virtual machine.
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -CreateOption FromImage | `
  Set-AzureRmVMBootDiagnostics -ResourceGroupName $ResourceGroupName `
  -StorageAccountName $StorageAccountName -Enable |`
  Add-AzureRmVMNetworkInterface -Id $nic.Id


# Create the virtual machine.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Zdalny do maszyny wirtualnej, który został utworzony w poprzednim kroku potrzebujesz publicznego adresu IP. Uruchom następujące polecenie, aby pobrać publiczny adres IP maszyny wirtualnej:

```powershell
Get-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName | Select IpAddress
```

Użyj następującego polecenia, aby utworzyć sesję usług pulpitu zdalnego z maszyną wirtualną. Zamień adres IP na publiczny adres IP Twojej maszyny wirtualnej. Po wyświetleniu monitu wprowadź nazwę użytkownika i hasło, których użyto podczas tworzenia maszyny wirtualnej.

```powershell
mstsc /v <publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>Instalowanie usług IIS za pośrednictwem programu PowerShell

Teraz po zalogowaniu do maszyny wirtualnej platformy Azure możesz użyć jednego wiersza w programie PowerShell, aby zainstalować usługi IIS i włączyć lokalną regułę zapory, która zezwala na ruch w sieci Web. Otwórz wiersz polecenia programu PowerShell i uruchom następujące polecenie:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Wyświetlanie strony powitalnej usług IIS

Z zainstalowanymi usługami IIS oraz port 80 jest otwarty na maszynie Wirtualnej można użyć wybranej przeglądarki sieci web, aby wyświetlić domyślną stronę powitalną usług IIS. Użyj *publicznego adresu IP* opisane w poprzedniej sekcji w celu odwiedzenia strony domyślnej.

![Domyślna witryna usług IIS](./media/azure-stack-quick-create-vm-windows-powershell/default-iis-website.png)

## <a name="delete-the-virtual-machine"></a>Usuń maszynę wirtualną

Gdy nie będą już potrzebne, użyj następującego polecenia, aby usunąć grupę zasobów, która zawiera maszynę wirtualną i powiązane zasoby:

```powershell
Remove-AzureRmResourceGroup `
  -Name $ResourceGroupName
```

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start wdrożono prostą maszynę wirtualną Windows. Aby dowiedzieć się więcej o maszynach wirtualnych usługi Azure Stack, w dalszym ciągu [uwagi dotyczące maszyn wirtualnych w usłudze Azure Stack](azure-stack-vm-considerations.md).
