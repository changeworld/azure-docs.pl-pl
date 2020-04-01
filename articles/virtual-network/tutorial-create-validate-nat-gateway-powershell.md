---
title: 'Samouczek: Tworzenie i testowanie bramy TRANSLATORA — Azure PowerShell'
titlesuffix: Azure Virtual Network NAT
description: W tym samouczku pokazano, jak utworzyć bramę NAT przy użyciu programu Azure PowerShell i przetestować usługę NAT
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 61cda5e61d14c4eeaf2d88483603707598b1c911
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202239"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell-and-test-the-nat-service"></a>Samouczek: Tworzenie bramy NAT przy użyciu programu Azure PowerShell i testowanie usługi NAT

W tym samouczku utworzysz bramę NAT, aby zapewnić łączność wychodzącą dla maszyn wirtualnych na platformie Azure. Aby przetestować bramę NAT, należy wdrożyć maszynę wirtualną źródłową i docelową. Przetestujesz bramę NAT, nawiązując połączenia wychodzące z publicznym adresem IP. Te połączenia będą pochodzić ze źródła do docelowej maszyny wirtualnej. Ten samouczek wdraża źródło i miejsce docelowe w dwóch różnych sieciach wirtualnych w tej samej grupie zasobów dla uproszczenia.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ten samouczek można wykonać przy użyciu usługi Azure Cloud Shell lub uruchomić odpowiednie polecenia lokalnie.  Jeśli nigdy nie używałeś usługi Azure Cloud Shell, należy [zalogować się teraz.](https://shell.azure.com)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](https://docs.microsoft.com/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o nazwie **myResourceGroupNAT** w lokalizacji **eastus2:**


```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>Tworzenie bramy NAT

### <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Aby uzyskać dostęp do Internetu, potrzebujesz co najmniej jednego publicznego adresu IP bramy TRANSLATORA. Użyj [New-AzPublicIpAddress,](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) aby utworzyć publiczny zasób adresu IP o nazwie **myPublicIPsource** w **myResourceGroupNAT**. Wynik tego polecenia będą przechowywane w zmiennej o nazwie **$publicIPsource** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pips = 'myPublicIPsource'
$alm = 'Static'
$sku = 'Standard'

$publicIPsource = 
New-AzPublicIpAddress -Name $pips -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Tworzenie publicznego prefiksu IP

 Użyj [new-AzPublicIpPrefix,](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest) aby utworzyć publiczny zasób prefiksu IP o nazwie **myPublicIPprefixsource** w **myResourceGroupNAT**.  Wynik tego polecenia będą przechowywane w zmiennej o nazwie **$publicIPPrefixsource** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$prips = 'mypublicIPprefixsource'

$publicIPPrefixsource = 
New-AzPublicIpPrefix -Name $prips -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>Tworzenie zasobu bramy TRANSLATORA

W tej sekcji opisano, jak można tworzyć i konfigurować następujące składniki usługi NAT przy użyciu zasobu bramy NAT:
  - Publiczna pula adresów IP i publiczny prefiks IP do użycia dla przepływów wychodzących przetłumaczonych przez zasób bramy NAT.
  - Zmień limit czasu bezczynności z domyślnego 4 minut na 10 minut.

Utwórz globalną bramę nat platformy Azure z [New-AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway). Wynikiem tego polecenia utworzy się zasób bramy o nazwie **myNATgateway,** który używa publicznego adresu IP **myPublicIPsource** i publicznego prefiksu IP **myPublicIPprefixsource**. Limit czasu bezczynnego jest ustawiony na 10 minut.  Wynik tego polecenia będą przechowywane w zmiennej o nazwie **$natGateway** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$nnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $nnm -ResourceGroupName $rsg -PublicIpAddress $publicIPsource -PublicIpPrefix $publicIPPrefixsource -Location $loc -Sku $sku -IdleTimeoutInMinutes 10      
  ```

W tym momencie brama NAT jest funkcjonalna i brakuje tylko konfigurowania podsieci sieci wirtualnej.

## <a name="prepare-the-source-for-outbound-traffic"></a>Przygotowanie źródła dla ruchu wychodzącego

Poprowadzimy Cię przez konfigurację pełnego środowiska testowego. Zestawisz test przy użyciu narzędzi typu open source do weryfikacji bramy NAT. Zaczniemy od źródła, które będzie korzystać z bramy NAT, którą utworzyliśmy wcześniej.

### <a name="configure-virtual-network-for-source"></a>Konfigurowanie sieci wirtualnej dla źródła

Utwórz sieć wirtualną i skojarz podsieć z bramą.

Utwórz sieć wirtualną o nazwie **myVnetsource** z podsiecią o nazwie **mySubnetsource** przy użyciu [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) w **myResourceGroupNAT** przy użyciu [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). Przestrzeń adresowa IP dla sieci wirtualnej to **192.168.0.0/16**. Podsieć w sieci wirtualnej jest **192.168.0.0/24**.  Wynik poleceń będą przechowywane w zmiennych o nazwie **$subnetsource** i **$vnetsource** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$sbn = 'mySubnetsource'
$spfx = '192.168.0.0/24'
$vnm = 'myVnetsource'
$vpfx = '192.168.0.0/16'
$loc = 'eastus2'

$subnetsource = 
New-AzVirtualNetworkSubnetConfig -Name $sbn -AddressPrefix $spfx -NatGateway $natGateway

$vnetsource = 
New-AzVirtualNetwork -Name $vnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $vpfx -Subnet $subnet
```

Cały ruch wychodzący do miejsc docelowych w Internecie jest teraz korzystający z usługi NAT.  Nie jest konieczne skonfigurowanie UDR.

Zanim będziemy mogli przetestować bramę NAT, musimy utworzyć źródłową maszynę wirtualną.  Firma Wezwie publiczny zasób adresu IP jako publiczny adres IP na poziomie wystąpienia, aby uzyskać dostęp do tej maszyny wirtualnej z zewnątrz. Ten adres jest używany tylko do uzyskania do niego dostęp do testu.  Zademonstrujemy, jak usługa NAT ma pierwszeństwo przed innymi opcjami wychodzącymi.

Można również utworzyć tę maszynę wirtualną bez publicznego adresu IP i utworzyć inną maszynę wirtualną do użycia jako jumpbox bez publicznego adresu IP jako ćwiczenia.

### <a name="create-public-ip-for-source-vm"></a>Tworzenie publicznego adresu IP dla źródłowego wirtualnego

Tworzymy publiczny adres IP, który ma być używany do uzyskiwania dostępu do maszyny Wirtualnej.  Użyj [New-AzPublicIpAddress,](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) aby utworzyć publiczny zasób adresu IP o nazwie **myPublicIPVM** w **myResourceGroupNAT**.  Wynik tego polecenia będą przechowywane w zmiennej o nazwie **$publicIpsourceVM** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pipvm = 'myPublicIpsourceVM'
$alm = 'Static'

$publicIpsourceVM = 
New-AzPublicIpAddress -Name $pipvm -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Tworzenie sieciowej sieciowej sieciowej i uwidacznianie punktu końcowego SSH dla maszyny Wirtualnej

Ponieważ standardowe publiczne adresy IP są domyślnie "bezpieczne", tworzymy sieć sieciową, aby umożliwić dostęp przychodzący dla ssh. Usługa NAT jest świadomy kierunku przepływu. Ta grupa sieciowa sieciowej sieciowej nie będzie używana do ruchu wychodzącego po skonfigurowaniu bramy NAT w tej samej podsieci. Użyj [New-AzNetworkSecurityGroup,](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) aby utworzyć zasób sieciowej grupy zabezpieczeń o nazwie **myNSGsource**. Użyj [New-AzNetworkSecurityRuleConfig,](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) aby utworzyć regułę sieciowej grupy zabezpieczeń dla dostępu SSH o nazwie **ssh** w **myResourceGroupNAT**. Wynik tego polecenia będą przechowywane w zmiennej o nazwie **$nsgsource** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$rnm = 'ssh'
$rdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$nsnm = 'myNSGsource'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$nsgsource = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $nsnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-source-vm"></a>Tworzenie karty sieciowej dla źródłowej maszyny Wirtualnej

Utwórz interfejs sieciowy z [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) o nazwie **myNicsource**. To polecenie skojarzy publiczny adres IP i sieciową grupę zabezpieczeń. Wynik tego polecenia będą przechowywane w zmiennej o nazwie **$nicsource** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nin = 'myNicsource'

$nicsource = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nin -NetworkSecurityGroupID $nsgsource.Id -PublicIPAddressID $publicIPVMsource.Id -SubnetID $vnetsource.Subnets[0].Id -Location $loc
```

### <a name="create-a-source-vm"></a>Tworzenie źródłowej maszyny Wirtualnej

#### <a name="create-ssh-key-pair"></a>Tworzenie pary kluczy SSH

Do wykonania kroków tego przewodnika Szybki start konieczne jest posiadanie pary kluczy SSH. Jeśli masz już parę kluczy SSH, możesz pominąć ten krok.

Użyj ssh-keygen, aby utworzyć parę kluczy SSH.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Aby uzyskać bardziej szczegółowe informacje na temat tworzenia par kluczy SSH, łącznie z użyciem programu PuTTy, zobacz [Jak używać kluczy SSH w systemie Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

Jeśli utworzysz parę kluczy SSH przy użyciu powłoki chmury, para kluczy jest przechowywana w obrazie kontenera. To [konto magazynu jest tworzone automatycznie](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). Nie usuwaj konta magazynu ani udziału plików w obrębie, dopóki nie odzyskasz kluczy.

#### <a name="create-vm-configuration"></a>Tworzenie konfiguracji maszyny Wirtualnej

Aby utworzyć maszynę wirtualną w programie PowerShell, należy utworzyć konfigurację obejmującą ustawienia, takie jak obraz do użycia, rozmiar i opcje uwierzytelniania. Następnie konfiguracja jest używana do utworzenia maszyny wirtualnej.

Zdefiniuj poświadczenia protokołu SSH, informacje o systemie operacyjnym i rozmiar maszyny wirtualnej. W tym przykładzie klucz SSH jest przechowywany w ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmn = 'myVMsource'
$vms = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigsource = 
New-AzVMConfig -VMName $vmn -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigsource -Linux -ComputerName $vmn -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigsource -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigsource -Id $nicsource.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigsource -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Połącz definicje konfiguracji, aby utworzyć maszynę wirtualną o nazwie **myVMsource** z [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) w **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigsource
```

Gdy polecenie powróci natychmiast, może upłynąć kilka minut, aby maszyna wirtualna została wdrożona.

## <a name="prepare-destination-for-outbound-traffic"></a>Przygotowanie miejsca docelowego dla ruchu wychodzącego

Teraz utworzymy miejsce docelowe dla ruchu wychodzącego przetłumaczonego przez usługę NAT, aby umożliwić jego przetestowanie.

### <a name="configure-virtual-network-for-destination"></a>Konfigurowanie sieci wirtualnej dla miejsca docelowego

Musimy utworzyć sieć wirtualną, w której będzie docelowa maszyna wirtualna.  Te polecenia są takie same kroki jak dla źródłowej maszyny Wirtualnej. Dodano niewielkie zmiany, aby udostępnić docelowy punkt końcowy.

Utwórz sieć wirtualną o nazwie **myVnetdestination** z podsiecią o nazwie **mySubnetdestination** przy użyciu [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) w **myResourceGroupNAT** przy użyciu [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). Przestrzeń adresowa IP dla sieci wirtualnej to **192.168.0.0/16**. Podsieć w sieci wirtualnej jest **192.168.0.0/24**.  Wynik poleceń będą przechowywane w zmiennych o nazwie **$subnetdestination** i **$vnetdestination** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sbdn = 'mySubnetdestination'
$spfx = '192.168.0.0/24'
$vdn = 'myVnetdestination'
$vpfx = '192.168.0.0/16'

$subnetdestination = 
New-AzVirtualNetworkSubnetConfig -Name $sbdn -AddressPrefix $spfx

$vnetdestination = 
New-AzVirtualNetwork -Name $vdn -ResourceGroupName $rsg -Location $loc -AddressPrefix $vpfx -Subnet $subnetdestination
```

### <a name="create-public-ip-for-destination-vm"></a>Tworzenie publicznego adresu IP dla docelowej maszyny Wirtualnej

Tworzymy publiczny adres IP, który ma być używany do uzyskiwania dostępu do docelowej maszyny Wirtualnej.  Użyj [New-AzPublicIpAddress,](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) aby utworzyć publiczny zasób adresu IP o nazwie **myPublicIPdestinationVM** w **myResourceGroupNAT**.  Wynik tego polecenia będą przechowywane w zmiennej o nazwie **$publicIpdestinationVM** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$all = 'Static'
$pipd = 'myPublicIPdestinationVM'

$publicIpdestinationVM = 
New-AzPublicIpAddress -Name $pipd -ResourceGroupName $rsg -AllocationMethod $all -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-and-http-endpoint-for-vm"></a>Tworzenie sieciowej sieciowej sieciowej i uwidacznianie punktu końcowego SSH i HTTP dla maszyny Wirtualnej

Standardowe publiczne adresy IP są "bezpieczne domyślnie", tworzymy nsg, aby umożliwić dostęp przychodzący dla ssh. Użyj [New-AzNetworkSecurityGroup,](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) aby utworzyć zasób sieciowej grupy zabezpieczeń o nazwie **myNSGdestination**. Użyj [New-AzNetworkSecurityRuleConfig,](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) aby utworzyć regułę sieciowej sieciowej dla dostępu SSH o nazwie **ssh**.  Użyj [New-AzNetworkSecurityRuleConfig,](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) aby utworzyć regułę sieciowej sieciowej sieciowej dla dostępu HTTP o nazwie **http**. Obie reguły zostaną utworzone w **myResourceGroupNAT**. Wynik tego polecenia będą przechowywane w zmiennej o nazwie **$nsgdestination** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$snm = 'ssh'
$sdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$hnm = 'http'
$hdsc = 'HTTP access'
$nsnm = 'myNSGdestination'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $snm -Description $sdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$httprule = 
New-AzNetworkSecurityRuleConfig -Name $hnm -Description $hdsc -Access $acc -Protocol $prt -Direction $dir -Priority 101 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80

$nsgdestination = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $nsnm -Location $loc -SecurityRules $sshrule,$httprule
```

### <a name="create-nic-for-destination-vm"></a>Tworzenie karty sieciowej dla docelowej maszyny Wirtualnej

Utwórz interfejs sieciowy z [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) o nazwie **myNicdestination**. To polecenie zostanie skojarzone z publicznym adresem IP i sieciową grupą zabezpieczeń. Wynik tego polecenia będą przechowywane w zmiennej o nazwie **$nicdestination** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nnm = 'myNicdestination'

$nicdestination = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nnm -NetworkSecurityGroupID $nsgdestination.Id -PublicIPAddressID $publicIPdestinationVM.Id -SubnetID $vnetdestination.Subnets[0].Id -Location $loc
```

### <a name="create-a-destination-vm"></a>Tworzenie docelowej maszyny Wirtualnej

#### <a name="create-vm-configuration"></a>Tworzenie konfiguracji maszyny Wirtualnej

Aby utworzyć maszynę wirtualną w programie PowerShell, należy utworzyć konfigurację obejmującą ustawienia, takie jak obraz do użycia, rozmiar i opcje uwierzytelniania. Następnie konfiguracja jest używana do utworzenia maszyny wirtualnej.

Zdefiniuj poświadczenia protokołu SSH, informacje o systemie operacyjnym i rozmiar maszyny wirtualnej. W tym przykładzie klucz SSH jest przechowywany w ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$vmd = 'myVMdestination'
$vms = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigdestination = New-AzVMConfig -VMName $vmd -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigdestination -Linux -ComputerName $vmd -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigdestination -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigdestination -Id $nicdestination.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigdestination -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Połącz definicje konfiguracji, aby utworzyć maszynę wirtualną o nazwie **myVMdestination** z [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) w **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigdestination
```

Gdy polecenie powróci natychmiast, może upłynąć kilka minut, aby maszyna wirtualna została wdrożona.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Przygotowanie serwera sieci web i przetestowanie ładunku na docelowej maszynie wirtualnej

Najpierw musimy odnajdować adres IP docelowej maszyny Wirtualnej.  Aby uzyskać publiczny adres IP maszyny Wirtualnej, użyj [adresu Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPdestinationVM'
  
Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>Skopiuj publiczny adres IP, a następnie wklej go do notatnika, aby można było go użyć w kolejnych krokach. Wskazać, że jest to docelowa maszyna wirtualna.

### <a name="sign-in-to-destination-vm"></a>Logowanie się do docelowej maszyny Wirtualnej

Poświadczenia SSH powinny być przechowywane w aplikacji Cloud Shell z poprzedniej operacji.  Otwórz usługę [Azure Cloud Shell](https://shell.azure.com) w przeglądarce. Użyj adresu IP pobranego w poprzednim kroku do protokołu SSH na maszynie wirtualnej. 

```bash
ssh azureuser@<ip-address-destination>
```

Po zalogowaniu się skopiuj i wklej następujące polecenia.  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

Te polecenia zaktualizują maszynę wirtualną, zainstalują nginx i utworzą plik o rozmiarze 100 KB. Ten plik zostanie pobrany ze źródłowej maszyny Wirtualnej przy użyciu usługi NAT.

Zamknij sesję SSH z docelową maszyną wirtualną.

## <a name="prepare-test-on-source-vm"></a>Przygotowanie testu na źródłowej maszynie wirtualnej

Najpierw musimy odnajdować adres IP źródłowej maszyny Wirtualnej.  Aby uzyskać publiczny adres IP maszyny Wirtualnej, użyj [adresu Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPsourceVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>Skopiuj publiczny adres IP, a następnie wklej go do notatnika, aby można było go użyć w kolejnych krokach. Wskazać, że jest to źródło maszyny wirtualnej.

### <a name="log-into-source-vm"></a>Zaloguj się do źródłowego vm

Ponownie poświadczenia SSH są przechowywane w usłudze Cloud Shell. Otwórz nową kartę usługi [Azure Cloud Shell](https://shell.azure.com) w przeglądarce.  Użyj adresu IP pobranego w poprzednim kroku do protokołu SSH na maszynie wirtualnej. 

```bash
ssh azureuser@<ip-address-source>
```

Skopiuj i wklej następujące polecenia, aby przygotować się do testowania usługi NAT.

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Te polecenia zaktualizują maszynę wirtualną, zainstalują go, [zainstalują hej](https://github.com/rakyll/hey) z GitHub i zaktualizują środowisko powłoki.

Teraz możesz przetestować usługę NAT.

## <a name="validate-nat-service"></a>Sprawdzanie poprawności usługi NAT

Po zalogowaniu się do źródłowej maszyny Wirtualnej można użyć **curl** i hej do **generowania** żądań do docelowego adresu IP.

Użyj curl, aby pobrać plik 100-KB.  Zastąp ** \<>docelowy adresu IP** w poniższym przykładzie docelowym adresem IP skopiowanym wcześniej.  **Parametr --output** wskazuje, że pobrany plik zostanie odrzucony.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Można również wygenerować serię żądań przy użyciu **hej**. Ponownie zastąp ** \<>docelowy adresu IP** na wcześniej skopiowany adres IP.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

To polecenie wygeneruje 100 żądań, 10 jednocześnie, z limitem czasu 30 sekund. Połączenie TCP nie zostanie ponownie użyczone.  Każde żądanie zostanie pobrane 100 Kbyt.  Pod koniec biegu **hej** zgłosi niektóre statystyki dotyczące tego, jak dobrze zrobiła to usługa NAT.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, można użyć [polecenia Usuń-AzResourceGroup,](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) aby usunąć grupę zasobów i wszystkie zasoby zawarte w.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzono bramę NAT, utworzono maszynę wirtualną źródłową i docelową, a następnie przetestowano bramę NAT.

Przejrzyj metryki w usłudze Azure Monitor, aby wyświetlić działanie usługi NAT. Diagnozowanie problemów, takich jak wyczerpanie zasobów dostępnych portów SNAT.  Wyczerpanie zasobów portów SNAT można łatwo rozwiązać, dodając dodatkowe zasoby publicznego adresu IP lub publiczne zasoby prefiksów IP lub oba te zasoby.

- Dowiedz się więcej o [nat sieci wirtualnej](./nat-overview.md)
- Dowiedz się więcej o [zasobie bramy NAT](./nat-gateway-resource.md).
- Szybki start do wdrażania [zasobu bramy NAT przy użyciu interfejsu wiersza polecenia platformy Azure](./quickstart-create-nat-gateway-cli.md).
- Szybki start do wdrażania [zasobu bramy NAT przy użyciu programu Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Szybki start do wdrażania [zasobu bramy NAT przy użyciu portalu Azure](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]

