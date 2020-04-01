---
title: 'Szybki start: tworzenie bramy TRANSLATORA — usługa Azure PowerShell'
titlesuffix: Azure Virtual Network NAT
description: Ten przewodnik Szybki start pokazuje, jak utworzyć bramę TRANSLATORA przy użyciu programu Azure PowerShell
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 1d5f8d6e0b2499bbecd32e7cb3fda2cd2cad4d19
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202242"
---
# <a name="quickstart-create-a-nat-gateway-using-azure-powershell"></a>Szybki start: tworzenie bramy translatora i sieci na wirtualnej sieci podczas korzystania z programu Azure PowerShell

Ten przewodnik Szybki start pokazuje, jak korzystać z usługi Azure Virtual Network NAT. Utworzysz bramę NAT, aby zapewnić łączność wychodzącą dla maszyny wirtualnej na platformie Azure. 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ten samouczek można wykonać przy użyciu usługi Azure Cloud Shell lub uruchomić polecenia lokalnie.  Jeśli usługa Azure Cloud Shell nie została użyta, [zaloguj się teraz.](https://shell.azure.com)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=latest). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o nazwie **myResourceGroupNAT** w lokalizacji **eastus2:**

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
  
New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>Tworzenie bramy NAT

Publiczne opcje ip bramy NAT to:

* **Publiczne adresy IP**
* **Publiczne prefiksy IP**

Oba mogą być używane z bramą NAT.

Dodamy publiczny adres IP i publiczny prefiks IP do tego scenariusza, aby zademonstrować.

### <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Aby uzyskać dostęp do Internetu, potrzebujesz co najmniej jednego publicznego adresu IP bramy TRANSLATORA. Użyj [New-AzPublicIpAddress,](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) aby utworzyć publiczny zasób adresu IP o nazwie **myPublicIP** w **myResourceGroupNAT**. Wynik tego polecenia będą przechowywane w zmiennej **$publicIP** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pbnm = 'myPublicIP'
  
$publicIP = 
New-AzPublicIpAddress -Name $pbnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Tworzenie publicznego prefiksu IP

Użyj [new-AzPublicIpPrefix,](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest) aby utworzyć publiczny zasób prefiksu IP o nazwie **myPublicIPprefix** w **myResourceGroupNAT**.  Wynik tego polecenia będą przechowywane w zmiennej o nazwie **$publicIPPrefix** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pxnm = 'myPublicIPprefix'

$publicIPPrefix = 
New-AzPublicIpPrefix -Name $pxnm -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>Tworzenie zasobu bramy TRANSLATORA

W tej sekcji opisano, jak można tworzyć i konfigurować następujące składniki usługi NAT przy użyciu zasobu bramy NAT:
  - Publiczna pula adresów IP i publiczny prefiks IP do użycia dla przepływów wychodzących przetłumaczonych przez zasób bramy NAT.
  - Zmień limit czasu bezczynności z domyślnego 4 minut na 10 minut.

Utwórz globalną bramę nat platformy Azure z [New-AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway). Wynikiem tego polecenia utworzy się zasób bramy o nazwie **myNATgateway,** który używa publicznego adresu IP **myPublicIP** i publicznego prefiksu IP **myPublicIPprefix**. Limit czasu bezczynnego jest ustawiony na 10 minut.  Wynik tego polecenia będą przechowywane w zmiennej o nazwie **$natGateway** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$gnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $gnm -ResourceGroupName $rsg -PublicIpAddress $publicIP -PublicIpPrefix $publicIPPrefix -Location $loc -Sku $sku -IdleTimeoutInMinutes 10
  ```

W tym momencie brama NAT jest funkcjonalna i brakuje tylko konfigurowania podsieci sieci wirtualnej.

## <a name="configure-virtual-network"></a>Konfigurowanie sieci wirtualnej

Utwórz sieć wirtualną i skojarz podsieć z bramą.

Utwórz sieć wirtualną o nazwie **myVnet** z podsiecią o nazwie **mySubnet** przy użyciu [New-AzVirtualNetWorkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) w **myResourceGroup** przy użyciu [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). Przestrzeń adresowa IP dla sieci wirtualnej to **192.168.0.0/16**. Podsieć w sieci wirtualnej jest **192.168.0.0/24**.  Wynik poleceń będą przechowywane w zmiennych o nazwie **$subnet** i **$vnet** do późniejszego użycia.

```azurepowershell-interactive
$sbnm = 'mySubnet'
$vnnm = 'myVnet'
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pfxsub = '192.168.0.0/24'
$pfxvn = '192.168.0.0/16'

$subnet = 
New-AzVirtualNetworkSubnetConfig -Name $sbnm -AddressPrefix $pfxsub -NatGateway $natGateway

$vnet = 
New-AzVirtualNetwork -Name $vnnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $pfxvn -Subnet $subnet
```

Cały ruch wychodzący do miejsc docelowych w Internecie jest teraz korzystający z usługi NAT.  Nie jest konieczne skonfigurowanie UDR.

## <a name="create-a-vm-to-use-the-nat-service"></a>Tworzenie maszyny Wirtualnej w celu korzystania z usługi NAT

Teraz utworzymy maszynę wirtualną do korzystania z usługi NAT.  Ta maszyna wirtualna ma publiczny adres IP do użycia jako publiczny adres IP na poziomie wystąpienia, aby umożliwić dostęp do maszyny Wirtualnej.  Usługa NAT jest świadoma kierunku przepływu i zastąpi domyślne miejsce docelowe Internetu w podsieci. Publiczny adres IP maszyny Wirtualnej nie będzie używany dla połączeń wychodzących.

### <a name="create-public-ip-for-source-vm"></a>Tworzenie publicznego adresu IP dla źródłowego wirtualnego

Tworzymy publiczny adres IP, który ma być używany do uzyskiwania dostępu do maszyny Wirtualnej.  Użyj [New-AzPublicIpAddress,](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) aby utworzyć publiczny zasób adresu IP o nazwie **myPublicIPVM** w **myResourceGroupNAT**.  Wynik tego polecenia będą przechowywane w zmiennej o nazwie **$publicIpVM** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$ipnm = 'myPublicIPVM'
$sku = 'Standard'

$publicIpVM = 
New-AzPublicIpAddress -Name $ipnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Tworzenie sieciowej sieciowej sieciowej i uwidacznianie punktu końcowego SSH dla maszyny Wirtualnej

Standardowe publiczne adresy IP są "bezpieczne domyślnie", musimy utworzyć nsg, aby umożliwić dostęp przychodzący dla ssh. Użyj [New-AzNetworkSecurityGroup,](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) aby utworzyć zasób sieciowej grupy zabezpieczeń o nazwie **myNSG**. Użyj [New-AzNetworkSecurityRuleConfig,](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) aby utworzyć regułę sieciowej grupy zabezpieczeń dla dostępu SSH o nazwie **ssh** w **myResourceGroupNAT**.  Wynik tego polecenia będą przechowywane w zmiennej o nazwie **$nsg** do późniejszego użycia.

```azurepowershell-interactive
$rnm = 'ssh'
$rdesc = 'SSH access'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '100'
$prt = '22'
$rsg = 'myResourceGroupNAT'
$rnm = 'myNSG'
$loc = 'eastus2'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdesc -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange $prt

$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $rnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-vm"></a>Tworzenie karty sieciowej dla maszyny Wirtualnej

Utwórz interfejs sieciowy z [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) o nazwie **myNic**. To polecenie kojarzy publiczny adres IP i sieciową grupę zabezpieczeń. Wynik tego polecenia będą przechowywane w zmiennej o nazwie **$nic** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myNic'
$loc = 'eastus2'

$nic = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nmn -NetworkSecurityGroupID $nsg.Id -PublicIPAddressID $publicIPVM.Id -SubnetID $vnet.Subnets[0].Id -Location $loc
```

### <a name="create-vm"></a>Tworzenie maszyny wirtualnej

#### <a name="create-ssh-key-pair"></a>Tworzenie pary kluczy SSH

Do wykonania kroków tego przewodnika Szybki start konieczne jest posiadanie pary kluczy SSH. Jeśli masz już parę kluczy SSH, możesz pominąć ten krok.

Użyj ssh-keygen, aby utworzyć parę kluczy SSH.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Aby uzyskać bardziej szczegółowe informacje na temat tworzenia par kluczy SSH, łącznie z użyciem programu PuTTy, zobacz [Jak używać kluczy SSH w systemie Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

Jeśli utworzysz parę kluczy SSH przy użyciu powłoki chmury, para kluczy jest przechowywana w obrazie kontenera. To [konto magazynu jest tworzone automatycznie](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). Nie usuwaj konta magazynu ani udziału plików w obrębie, dopóki nie odzyskasz kluczy.

#### <a name="create-vm-configuration"></a>Tworzenie konfiguracji maszyny Wirtualnej

Aby utworzyć maszynę wirtualną w programie PowerShell, należy utworzyć konfigurację, która ma ustawienia dla obrazu do użycia, rozmiar i opcje uwierzytelniania. Konfiguracja jest używana do tworzenia maszyny Wirtualnej.

Zdefiniuj poświadczenia protokołu SSH, informacje o systemie operacyjnym i rozmiar maszyny wirtualnej. W tym przykładzie klucz SSH jest przechowywany w ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
#Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force

$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vnm = 'myVM'
$vsz = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$sku = '18.04-LTS'
$ver = 'latest'

$vmConfig = 
New-AzVMConfig -VMName $vnm -VMSize $vsz

Set-AzVMOperatingSystem -VM $vmConfig -Linux -ComputerName $vnm -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfig -PublisherName $pub -Offer $off -Skus $sku -Version $ver

Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Połącz definicje konfiguracji, aby utworzyć maszynę wirtualną o nazwie **myVM** z [new-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) w **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmconfig
```

Poczekaj, aż maszyna wirtualna przygotuje się do wdrożenia, a następnie kontynuuj pozostałe kroki.

## <a name="discover-the-ip-address-of-the-vm"></a>Odnajduj adres IP maszyny Wirtualnej

Najpierw musimy odnajdować adres IP utworzonej maszyny Wirtualnej. Aby uzyskać publiczny adres IP maszyny Wirtualnej, użyj [adresu Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myPublicIPVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $nmn | select IpAddress
``` 

>[!IMPORTANT]
>Skopiuj publiczny adres IP, a następnie wklej go do notatnika, aby można było go użyć do uzyskania dostępu do maszyny Wirtualnej.

### <a name="sign-in-to-vm"></a>Logowanie się do maszyny Wirtualnej

Poświadczenia SSH powinny być przechowywane w aplikacji Cloud Shell z poprzedniej operacji.  Otwórz usługę [Azure Cloud Shell](https://shell.azure.com) w przeglądarce. Użyj adresu IP pobranego w poprzednim kroku do protokołu SSH na maszynie wirtualnej.

```bash
ssh azureuser@<ip-address-destination>
```

Teraz możesz przystąpić do korzystania z usługi NAT.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, można użyć [polecenia Usuń-AzResourceGroup,](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) aby usunąć grupę zasobów i wszystkie zasoby zawarte w.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono bramę NAT i maszynę wirtualną, aby z niej korzystać. 

Przejrzyj metryki w usłudze Azure Monitor, aby wyświetlić działanie usługi NAT. Diagnozowanie problemów, takich jak wyczerpanie zasobów dostępnych portów SNAT.  Wyczerpanie zasobów portów SNAT jest rozwiązywane przez dodanie dodatkowych zasobów publicznego adresu IP lub publicznych zasobów prefiksu IP lub obu tych zasobów.


- Dowiedz się więcej o [translatoru adresów sieci wirtualnej platformy Azure](./nat-overview.md)
- Dowiedz się więcej o [zasobie bramy NAT](./nat-gateway-resource.md).
- Szybki start do wdrażania [zasobu bramy NAT przy użyciu interfejsu wiersza polecenia platformy Azure](./quickstart-create-nat-gateway-cli.md).
- Szybki start do wdrażania [zasobu bramy NAT przy użyciu programu Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Szybki start do wdrażania [zasobu bramy NAT przy użyciu portalu Azure](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]


