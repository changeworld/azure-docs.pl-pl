---
title: Wdrażanie aplikacji IPv6 z dwoma stosami — standardowy moduł równoważenia obciążenia — PowerShell
titlesuffix: Azure Virtual Network
description: W tym artykule pokazano, jak wdrożyć aplikację podwójnego stosu IPv6 ze standardowym modułem równoważenia obciążenia w sieci wirtualnej platformy Azure przy użyciu programu Azure Powershell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2020
ms.author: kumud
ms.openlocfilehash: d6b61e27324220fc78ace3e964aed98f9ba114d3
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420931"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---powershell"></a>Wdrażanie aplikacji podwójnego stosu IPv6 na platformie Azure — program PowerShell

W tym artykule pokazano, jak wdrożyć aplikację podwójnego stosu (IPv4 + IPv6) przy użyciu standardowego modułu równoważenia obciążenia na platformie Azure, która zawiera sieć wirtualną i podsieć z dwoma stosami, standardowy moduł równoważenia obciążenia z dwoma (IPv4 + IPv6) konfiguracje frontona, maszyny wirtualne z kartami sieciowymi, które mają konfigurację dwóch adresów IP, grupę zabezpieczeń sieci i publiczne adresy IP.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać programu PowerShell lokalnie, ten artykuł wymaga modułu programu Azure PowerShell w wersji 6.9.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem sieci wirtualnej z dwoma stosami należy utworzyć grupę zasobów za pomocą [programu New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład tworzy grupę zasobów o nazwie *myRGDualStack* w lokalizacji *we wschodniej części usa:*

```azurepowershell-interactive
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>Tworzenie publicznych adresów IP i IPv6
Aby uzyskać dostęp do maszyn wirtualnych z Internetu, potrzebujesz publicznych adresów IP IPv4 i IPv6 dla modułu równoważenia obciążenia. Tworzenie publicznych adresów IP za pomocą [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Poniższy przykład tworzy publiczny adres IP IPv4 i IPv6 o nazwie *dsPublicIP_v4* i *dsPublicIP_v6* w grupie zasobów *dsRG1:*

```azurepowershell-interactive
$PublicIP_v4 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v4" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4 `
  -Sku Standard
  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6 `
  -Sku Standard
```
Aby uzyskać dostęp do maszyn wirtualnych przy użyciu połączenia RDP, należy utworzyć publiczne adresy IPV4 dla maszyn wirtualnych za pomocą [usługi New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

```azurepowershell-interactive
  $RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -Sku Standard `
  -IpAddressVersion IPv4
  
  $RdpPublicIP_2 = New-AzPublicIpAddress `
   -Name "RdpPublicIP_2" `
   -ResourceGroupName $rg.ResourceGroupName `
   -Location $rg.Location  `
   -AllocationMethod Static `
   -Sku Standard `
   -IpAddressVersion IPv4
```

## <a name="create-standard-load-balancer"></a>Tworzenie usługi Load Balancer w warstwie Standardowa

W tej sekcji można skonfigurować adres IP z dwiema frontendami (IPv4 i IPv6) oraz pulę adresów zaplecza dla modułu równoważenia obciążenia, a następnie utworzyć standardowy moduł równoważenia obciążenia.

### <a name="create-front-end-ip"></a>Tworzenie adresu IP frontonu

Utwórz adres IP frontonu przy użyciu polecenia [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). Poniższy przykład tworzy konfiguracje IP iPv4 i IPv6 o nazwie *dsLbFrontEnd_v4* i *dsLbFrontEnd_v6:*

```azurepowershell-interactive
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

```

### <a name="configure-back-end-address-pool"></a>Konfigurowanie puli adresów zaplecza

Utwórz pulę adresów zaplecza z [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). W kolejnych krokach maszyny wirtualne zostaną dołączone do tej puli zaplecza. W poniższym przykładzie utworzono pule adresów zaplecza o nazwie *dsLbBackEndPool_v4* i *dsLbBackEndPool_v6* w celu uwzględnienia maszyn wirtualnych z konfiguracjami kart SIECIOWYCH IPV4 i IPv6:

```azurepowershell-interactive
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v6"
```
### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji
Użyj [Add-AzLoadBalancerProbeConfig,](/powershell/module/az.network/add-azloadbalancerprobeconfig) aby utworzyć sondę kondycji do monitorowania kondycji maszyn wirtualnych.
```azurepowershell
$probe = New-AzLoadBalancerProbeConfig -Name MyProbe -Protocol tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
```
### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. Zdefiniuj konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Aby upewnić się, że tylko w dobrej kondycji maszyny wirtualne odbierają ruch, można opcjonalnie zdefiniować sondę kondycji. Podstawowy moduł równoważenia obciążenia używa sondy IPv4 do oceny kondycji zarówno punktów końcowych IPv4, jak i IPv6 na maszynach wirtualnych. Standardowy moduł równoważenia obciążenia zawiera obsługę jawnie sond kondycji IPv6.

Utwórz regułę modułu równoważenia obciążenia przy użyciu polecenia [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). W poniższym przykładzie utworzono reguły równoważenia obciążenia o nazwach *dsLBrule_v4* i *dsLBrule_v6* i równoważą ruch na porcie *TCP* *80* do konfiguracji IP frontendu IPv4 i IPv6:

```azurepowershell-interactive
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
   -probe $probe

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
   -probe $probe
```

### <a name="create-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

Utwórz standardowy moduł równoważenia obciążenia za pomocą [new-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). Poniższy przykład tworzy publiczny standardowy moduł równoważenia obciążenia o nazwie *myLoadBalancer* przy użyciu konfiguracji IP frontendu IPv4 i IPv6, pul zaplecza i reguł równoważenia obciążenia utworzonych w poprzednich krokach:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "MyLoadBalancer" `
-Sku "Standard" `
-FrontendIpConfiguration $frontendIPv4,$frontendIPv6 `
-BackendAddressPool $backendPoolv4,$backendPoolv6 `
-LoadBalancingRule $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych
Przed wdrożeniem niektórych maszyn wirtualnych i przetestowaniu modułu balansera należy utworzyć pomocnicze zasoby sieciowe — zestaw dostępności, grupę zabezpieczeń sieci, sieć wirtualną i wirtualne karty sieciowe. 
### <a name="create-an-availability-set"></a>Tworzenie zestawu dostępności
Aby poprawić wysoką dostępność aplikacji, umieść maszyny wirtualne w zestawie dostępności.

Aby utworzyć zestaw dostępności, użyj polecenia [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). W poniższym przykładzie zostanie utworzony zestaw dostępności o nazwie *myAvailabilitySet*:

```azurepowershell-interactive
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

```azurepowershell-interactive
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

```azurepowershell-interactive
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

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsNSG1"  `
-SecurityRules $rule1,$rule2
```
### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną przy użyciu polecenia [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Poniższy przykład tworzy sieć wirtualną o nazwie *dsVnet* z *mySubnet:*

```azurepowershell-interactive
# Create dual stack subnet
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "dsSubnet" `
-AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $subnet
```

### <a name="create-nics"></a>Tworzenie kart sieciowych

Tworzenie wirtualnych kart sieciowych za pomocą [new-aznetworkinterface](/powershell/module/az.network/new-aznetworkinterface). Poniższy przykład tworzy dwie wirtualne karty sieciowe zarówno w konfiguracjach IPv4 i IPv6. (Po jednej karcie na każdą maszynę wirtualną, która zostanie utworzona na potrzeby aplikacji w kolejnych krokach).

```azurepowershell-interactive
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_1
      
  $Ip6Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp6Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv6 `
    -LoadBalancerBackendAddressPool $backendPoolv6
    
  $NIC_1 = New-AzNetworkInterface `
    -Name "dsNIC1" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 
    
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_2  

  $NIC_2 = New-AzNetworkInterface `
    -Name "dsNIC2" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 

```

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Ustaw nazwę użytkownika i hasło administratora maszyn wirtualnych przy użyciu polecenia [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VMs."
```

Następnie utwórz maszyny wirtualne za pomocą polecenia [New-AzVM](/powershell/module/az.compute/new-azvm). Poniższy przykład obejmuje tworzenie dwóch maszyn wirtualnych oraz wymaganych składników sieci wirtualnej, jeśli jeszcze nie istnieją. 

```azurepowershell-interactive
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

## <a name="determine-ip-addresses-of-the-ipv4-and-ipv6-endpoints"></a>Określanie adresów IP punktów końcowych IPv4 i IPv6
Pobierz wszystkie obiekty interfejsu sieciowego w grupie zasobów, aby podsumować adres IP używany w tym wdrożeniu za pomocą programu `get-AzNetworkInterface`. Ponadto, pobierz adresy frontendu modułu równoważenia obciążenia punktów końcowych IPv4 i IPv6 za pomocą `get-AzpublicIpAddress`.

```azurepowershell-interactive
$rgName= "dsRG1"
$NICsInRG= get-AzNetworkInterface -resourceGroupName $rgName 
write-host `nSummary of IPs in this Deployment: 
write-host ******************************************
foreach ($NIC in $NICsInRG) {
 
    $VMid= $NIC.virtualmachine.id 
    $VMnamebits= $VMid.split("/") 
    $VMname= $VMnamebits[($VMnamebits.count-1)] 
    write-host `nPrivate IP addresses for $VMname 
    $IPconfigsInNIC= $NIC.IPconfigurations 
    foreach ($IPconfig in $IPconfigsInNIC) {
 
        $IPaddress= $IPconfig.privateipaddress 
        write-host "    "$IPaddress 
        IF ($IPconfig.PublicIpAddress.ID) {
 
            $IDbits= ($IPconfig.PublicIpAddress.ID).split("/")
            $PipName= $IDbits[($IDbits.count-1)]
            $PipObject= get-azPublicIpAddress -name $PipName -resourceGroup $rgName
            write-host "    "RDP address:  $PipObject.IpAddress
                 }
         }
 }
 
 
 
  write-host `nPublic IP addresses on Load Balancer:
 
  (get-AzpublicIpAddress -resourcegroupname $rgName | where { $_.name -notlike "RdpPublicIP*" }).IpAddress
```
Na poniższej ilustracji przedstawiono przykładowe dane wyjściowe, które zawierają listę prywatnych adresów IPv4 i IPv6 dwóch maszyn wirtualnych oraz adresów IP iPv4 fronta i IPv6 modułu równoważenia obciążenia.

![Podsumowanie protokołu IP wdrożenia aplikacji z dwoma stosami (IPv4/IPv6) na platformie Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-application-summary.png)

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Wyświetlanie sieci wirtualnej z dwoma stosami IPv6 w witrynie Azure portal
Sieć wirtualną z dwoma stosami IPv6 można wyświetlić w witrynie Azure Portal w następujący sposób:
1. Na pasku wyszukiwania portalu wprowadź *dsVnet*.
2. Gdy w wynikach wyszukiwania pojawi się **dsVnet,** wybierz ją. Spowoduje to uruchomienie strony **Przegląd** sieci wirtualnej z dwoma stosami o nazwie *dsVnet*. Sieć wirtualna z dwoma stosami pokazuje dwie karty sieciowe z konfiguracjami IPv4 i IPv6 znajdującymi się w podsieci podwójnego stosu o nazwie *dsSubnet*.

  ![Sieć wirtualna z dwoma stosami IPv6 na platformie Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name dsRG1
```

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono standardowy moduł równoważenia obciążenia z konfiguracją IP z podwójnym frontendem (IPv4 i IPv6). Utworzono również dwie maszyny wirtualne, które zawierały karty sieciowe z dwiema konfiguracjami IP (IPV4 + IPv6), które zostały dodane do puli zaplecza modułu równoważenia obciążenia. Aby dowiedzieć się więcej o obsłudze IPv6 w sieciach wirtualnych platformy Azure, zobacz [Co to jest IPv6 dla usługi Azure Virtual Network?](ipv6-overview.md)
