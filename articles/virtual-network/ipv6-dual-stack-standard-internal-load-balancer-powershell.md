---
title: Wdrażanie aplikacji ipv6 z dwoma stosami przy użyciu standardowego wewnętrznego modułu równoważenia obciążenia na platformie Azure — PowerShell
titlesuffix: Azure Virtual Network
description: W tym artykule pokazano, jak wdrożyć aplikację podwójnego stosu IPv6 ze standardowym wewnętrznym modułem równoważenia obciążenia w sieci wirtualnej platformy Azure przy użyciu programu Azure Powershell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2019
ms.author: kumud
ms.openlocfilehash: fdf726fd31e8b92a04a1c136eb5cd7110e0c6d5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72333367"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-standard-internal-load-balancer-in-azure---powershell-preview"></a>Wdrażanie aplikacji podwójnego stosu IPv6 przy użyciu standardowego wewnętrznego modułu równoważenia obciążenia na platformie Azure — program PowerShell (wersja zapoznawcza)

W tym artykule pokazano, jak wdrożyć aplikację podwójnego stosu (IPv4 + IPv6) na platformie Azure, która zawiera sieć wirtualną i podsieć z dwoma stosami, standardowy wewnętrzny moduł równoważenia obciążenia z dwoma konfiguracjami frontu (IPv4 + IPv6), maszyny wirtualne z kartami sieciowymi z dwoma kartami IP, które mają podwójny adres IP konfiguracji, sieciowej grupy zabezpieczeń i publicznych usług IP.

> [!Important]
> Obsługa IPv6 dla usługi Azure Virtual Network jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Procedura tworzenia modułu wyważającego obciążenia wewnętrznego obsługującego iPv6 jest prawie identyczna z procesem tworzenia internetowego modułu równoważenia obciążenia IPv6 opisanego [tutaj](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md). Jedyne różnice przy tworzeniu wewnętrznego modułu równoważenia obciążenia znajdują się w konfiguracji frontonalnej, jak pokazano w poniższym przykładzie programu PowerShell:

```azurepowershell
 $frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
 -Name "dsLbFrontEnd_v6" `
 -PrivateIpAddress "ace:cab:deca:deed::100" `
 -PrivateIpAddressVersion "IPv6" `
 -Subnet $DsSubnet
```

Zmiany, które sprawiają, że powyższe wewnętrzne moduły równoważenia obciążenia konfiguracji front-end są:
- Jest `PrivateIpAddressVersion` określony jako "IPv6"
- Argument `-PublicIpAddress` został pominięty lub zastąpiony `-PrivateIpAddress`. Należy zauważyć, że adres prywatny musi znajdować się w zakresie przestrzeni IP podsieci, w którym zostanie wdrożony wewnętrzny moduł równoważenia obciążenia. Jeśli zostanie `-PrivateIpAddress` pominięty statyczny, następny wolny adres IPv6 zostanie wybrany z podsieci, w której wdrożony jest wewnętrzny moduł równoważenia obciążenia.
- Podsieć podwójnego stosu, w którym zostanie wdrożony wewnętrzny `-Subnet` moduł `-SubnetId` równoważenia obciążenia, jest określona za pomocą argumentu lub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać programu PowerShell lokalnie, ten artykuł wymaga modułu programu Azure PowerShell w wersji 6.9.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="prerequisites"></a>Wymagania wstępne
Przed wdrożeniem aplikacji z dwoma stosami na platformie Azure należy skonfigurować subskrypcję dla tej funkcji w wersji zapoznawczej przy użyciu następującej usługi Azure PowerShell:

Zarejestruj się w następujący sposób:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Rejestracja funkcji trwa do 30 minut. Możesz sprawdzić stan rejestracji, uruchamiając następujące polecenie programu Azure PowerShell: Sprawdź rejestrację w następujący sposób:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Po zakończeniu rejestracji uruchom następujące polecenie:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem sieci wirtualnej z dwoma stosami należy utworzyć grupę zasobów za pomocą [programu New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład tworzy grupę zasobów o nazwie *dsStd_ILB_RG* w lokalizacji *we wschodniej* części usa:

```azurepowershell
$rg = New-AzResourceGroup `
  -ResourceGroupName "dsStd_ILB_RG"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>Tworzenie publicznych adresów IP i IPv6
Aby uzyskać dostęp do maszyn wirtualnych z Internetu, potrzebujesz publicznych adresów IP IPv4 i IPv6 dla maszyn wirtualnych. Tworzenie publicznych adresów IP za pomocą [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Poniższy przykład tworzy publiczny adres IP IPv4 i IPv6 o nazwie *RdpPublicIP_1* i *RdpPublicIP_2* w *dsStd_ILB_RG* grupie zasobów:

```azurepowershell
$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
  
$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
```
## <a name="create-the-virtual-network-and-the-subnet"></a>Tworzenie sieci wirtualnej i podsieci

Utwórz sieć wirtualną za pomocą [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) z podwójnym stosem konfiguracji podsieci przy użyciu [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Poniższy przykład tworzy sieć wirtualną o nazwie *dsVnet* z *dsSubnet*.

```azurepowershell
# Create dual stack subnet config
$DsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name "dsSubnet" `
  -AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $DsSubnet

#Refresh the fully populated subnet for use in load balancer frontend configuration
$DsSubnet = get-AzVirtualNetworkSubnetconfig -name dsSubnet -VirtualNetwork $vnet
```
## <a name="create-standard-load-balancer"></a>Tworzenie usługi Load Balancer w warstwie Standardowa

W tej sekcji można skonfigurować adres IP z dwiema frontendami (IPv4 i IPv6) oraz pulę adresów zaplecza dla modułu równoważenia obciążenia, a następnie utworzyć standardowy moduł równoważenia obciążenia.

### <a name="create-front-end-ip"></a>Tworzenie adresu IP frontonu

Utwórz adres IP frontonu przy użyciu polecenia [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). Poniższy przykład tworzy konfiguracje IP iPv4 i IPv6 o nazwie *dsLbFrontEnd_v4* i *dsLbFrontEnd_v6:*

```azurepowershell
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PrivateIpAddress "10.0.0.100"  `
  -PrivateIpAddressVersion "IPv4"   `
  -Subnet $DsSubnet

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PrivateIpAddress "ace:cab:deca:deed::100"  `
  -PrivateIpAddressVersion "IPv6"   `
  -Subnet $DsSubnet

```

### <a name="configure-back-end-address-pool"></a>Konfigurowanie puli adresów zaplecza

Utwórz pulę adresów zaplecza z [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). W kolejnych krokach maszyny wirtualne zostaną dołączone do tej puli zaplecza. W poniższym przykładzie utworzono pule adresów zaplecza o nazwie *dsLbBackEndPool_v4* i *dsLbBackEndPool_v6* w celu uwzględnienia maszyn wirtualnych z konfiguracjami kart SIECIOWYCH IPV4 i IPv6:

```azurepowershell
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. Zdefiniuj konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Aby upewnić się, że tylko w dobrej kondycji maszyny wirtualne odbierają ruch, można opcjonalnie zdefiniować sondę kondycji. Podstawowy moduł równoważenia obciążenia używa sondy IPv4 do oceny kondycji zarówno punktów końcowych IPv4, jak i IPv6 na maszynach wirtualnych. Standardowy moduł równoważenia obciążenia zawiera obsługę jawnie sond kondycji IPv6.

Utwórz regułę modułu równoważenia obciążenia przy użyciu polecenia [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). W poniższym przykładzie utworzono reguły równoważenia obciążenia o nazwach *dsLBrule_v4* i *dsLBrule_v6* i równoważą ruch na porcie *TCP* *80* do konfiguracji IP frontendu IPv4 i IPv6:

```azurepowershell
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

### <a name="create-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

Utwórz standardowy moduł równoważenia obciążenia za pomocą [new-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). Poniższy przykład tworzy publiczny standardowy moduł równoważenia obciążenia o nazwie *myInternalLoadBalancer* przy użyciu konfiguracji IP frontendu IPv4 i IPv6, pul zaplecza i reguł równoważenia obciążenia utworzonych w poprzednich krokach:

```azurepowershell
$lb = New-AzLoadBalancer  `
  -ResourceGroupName $rg.ResourceGroupName  `
  -Location $rg.Location  `
  -Name  "MyInternalLoadBalancer"  `
  -Sku "Standard"  `
  -FrontendIpConfiguration  $frontendIPv4,$frontendIPv6  `
  -BackendAddressPool  $backendPoolv4,$backendPoolv6  `
  -LoadBalancingRule  $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych
Przed wdrożeniem niektórych maszyn wirtualnych i przetestowaniu modułu balansera należy utworzyć pomocnicze zasoby sieciowe — zestaw dostępności, sieciową grupę zabezpieczeń i wirtualne karty sieciowe. 

### <a name="create-an-availability-set"></a>Tworzenie zestawu dostępności
Aby zwiększyć wysoką dostępność aplikacji, umieść maszyny wirtualne w zestawie dostępności.

Aby utworzyć zestaw dostępności, użyj polecenia [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). Poniższy przykład tworzy zestaw dostępności o nazwie *dsAVset:*

```azurepowershell
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz grupę zabezpieczeń sieci dla reguł, które będą regulować komunikację przychodzącą i wychodzącą w sieci wirtualnej.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Tworzenie reguły sieciowej grupy zabezpieczeń dla portu 3389

Utwórz regułę sieciowej grupy zabezpieczeń, aby zezwolić na połączenia protokołu RDP za pośrednictwem portu 3389 przy użyciu polecenia [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleRDP' `
  -Description 'Allow RDP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389
```
#### <a name="create-a-network-security-group-rule-for-port-80"></a>Tworzenie reguły sieciowej grupy zabezpieczeń dla portu 80

Utwórz regułę sieciowej grupy zabezpieczeń, aby zezwolić na połączenia internetowe za pośrednictwem portu 80 za pomocą [pliku New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange 80 `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz sieciową grupę zabezpieczeń przy użyciu polecenia [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsNSG1"  `
  -SecurityRules $rule1,$rule2
```
### <a name="create-nics"></a>Tworzenie kart sieciowych

Tworzenie wirtualnych kart sieciowych za pomocą [new-aznetworkinterface](/powershell/module/az.network/new-aznetworkinterface). Poniższy przykład tworzy dwie wirtualne karty sieciowe zarówno w konfiguracjach IPv4 i IPv6. (Po jednej karcie na każdą maszynę wirtualną, która zostanie utworzona na potrzeby aplikacji w kolejnych krokach).

```azurepowershell

# Create the IPv4 configuration for NIC 1
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_1

# Create the IPv6 configuration
$Ip6Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp6Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv6 `
  -LoadBalancerBackendAddressPool $backendPoolv6

# Create NIC 1
$NIC_1 = New-AzNetworkInterface `
  -Name "dsNIC1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

# Create the IPv4 configuration for NIC 2
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_2

# Create NIC 2 reusing the IPv6 configuration from NIC 1
$NIC_2 = New-AzNetworkInterface `
  -Name "dsNIC2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

```

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Ustaw nazwę użytkownika i hasło administratora maszyn wirtualnych przy użyciu polecenia [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VM's"
```

Następnie utwórz maszyny wirtualne za pomocą polecenia [New-AzVM](/powershell/module/az.compute/new-azvm). Poniższy przykład obejmuje tworzenie dwóch maszyn wirtualnych oraz wymaganych składników sieci wirtualnej, jeśli jeszcze nie istnieją.

```azurepowershell
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2019-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1


$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```
## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Wyświetlanie sieci wirtualnej z dwoma stosami IPv6 w witrynie Azure portal
Sieć wirtualną z dwoma stosami IPv6 można wyświetlić w witrynie Azure Portal w następujący sposób:
1. Na pasku wyszukiwania portalu wprowadź *dsVnet*.
2. Gdy w wynikach wyszukiwania pojawi się **dsVnet,** wybierz ją. Spowoduje to uruchomienie strony **Przegląd** sieci wirtualnej z dwoma stosami o nazwie *dsVnet*. Sieć wirtualna z dwoma stosami pokazuje dwie karty sieciowe z konfiguracjami IPv4 i IPv6 znajdującymi się w podsieci podwójnego stosu o nazwie *dsSubnet*.

![Sieć wirtualna IPv6 Dual Stack ze standardowym wewnętrznym modułem równoważenia obciążenia](./media/ipv6-dual-stack-standard-internal-load-balancer-powershell/ipv6-dual-stack-virtual-network.png)


> [!NOTE]
> Sieć wirtualna IPv6 dla platformy Azure jest dostępna w witrynie Azure portal tylko do odczytu w tej wersji w wersji zapoznawczej.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell
Remove-AzResourceGroup -Name dsStd_ILB_RG
```

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono standardowy moduł równoważenia obciążenia z konfiguracją IP z podwójnym frontendem (IPv4 i IPv6). Utworzono również dwie maszyny wirtualne, które zawierały karty sieciowe z dwiema konfiguracjami IP (IPV4 + IPv6), które zostały dodane do puli zaplecza modułu równoważenia obciążenia. Aby dowiedzieć się więcej o obsłudze IPv6 w sieciach wirtualnych platformy Azure, zobacz [Co to jest IPv6 dla usługi Azure Virtual Network?](ipv6-overview.md)