---
title: Wdróż aplikację podwójnego stosu protokołu IPv6 w sieci wirtualnej platformy Azure — PowerShell
titlesuffix: Azure Virtual Network
description: W tym artykule przedstawiono sposób wdrażania aplikacji podwójnego stosu protokołu IPv6 w sieci wirtualnej platformy Azure przy użyciu programu Azure Powershell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: a9f8066896134072665c3f5b325e033b638bf094
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001010"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---powershell-preview"></a>Wdróż aplikację podwójnego stosu protokołu IPv6 w systemie Azure - PowerShell (wersja zapoznawcza)

W tym artykule pokazano, jak wdrożyć aplikację podwójny stos (IPv4 + IPv6) na platformie Azure, zawierającą podwójnego stosu sieci wirtualnej i podsieci, moduł równoważenia obciążenia z konfiguracji frontonu podwójnego (IPv4 + IPv6), maszyn wirtualnych przy użyciu karty sieciowe, które ma podwójną konfiguracji adresu IP, sieci grupy zabezpieczeń i publicznych adresów IP.

> [!Important]
> Obsługa protokołu IPv6 dla usługi Azure Virtual Network jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli zdecydujesz się zainstalować program PowerShell i używać lokalnie, ten artykuł wymaga programu Azure PowerShell w wersji modułu 6.9.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="prerequisites"></a>Wymagania wstępne
Przed wdrożeniem aplikacji podwójnego stosu na platformie Azure, należy skonfigurować subskrypcję dla tej funkcji w wersji zapoznawczej, za pomocą następujących programu Azure PowerShell:

Zarejestruj w następujący sposób:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
```
Trwa rejestracja funkcji ukończyć do 30 minut. Twój status rejestracji można sprawdzić przez uruchomienie następującego polecenia programu Azure PowerShell: Sprawdzanie rejestracji w następujący sposób:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
```
Po zakończeniu rejestracji, uruchom następujące polecenie:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem sieci wirtualnej obsługującej podwójny stos, należy utworzyć grupę zasobów za pomocą [New AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład tworzy grupę zasobów o nazwie *myRGDualStack* w *wschodnie stany USA* lokalizacji:

```azurepowershell-interactive
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>Utwórz publiczne adresy IP protokołu IPv4 i IPv6
Dostęp do maszyn wirtualnych z Internetu, potrzebujesz protokołów IPv4 i IPv6 publiczne adresy IP dla modułu równoważenia obciążenia. Utwórz publiczne adresy IP z [New AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Poniższy przykład tworzy protokołów IPv4 i IPv6 publiczny adres IP o nazwie *dsPublicIP_v4* i *dsPublicIP_v6* w *dsRG1* grupy zasobów:

```azurepowershell-interactive
$PublicIP_v4 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v4" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv6
```
Aby uzyskać dostęp do maszyn wirtualnych przy użyciu połączenia RDP, należy utworzyć publiczne adresy IP protokołu IPV4 dla maszyn wirtualnych z [New AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

```azurepowershell-interactive
  $RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
  $RdpPublicIP_2 = New-AzPublicIpAddress `
   -Name "RdpPublicIP_2" `
   -ResourceGroupName $rg.ResourceGroupName `
   -Location $rg.Location  `
   -AllocationMethod Dynamic `
   -IpAddressVersion IPv4
```

## <a name="create-basic-load-balancer"></a>Tworzenie podstawowego modułu równoważenia obciążenia

W tej sekcji konfigurowania adresu IP frontonu podwójny (IPv4 i IPv6) i puli adresów zaplecza modułu równoważenia obciążenia, a następnie Tworzenie podstawowego modułu równoważenia obciążenia.

### <a name="create-front-end-ip"></a>Tworzenie adresu IP frontonu

Utwórz adres IP frontonu przy użyciu polecenia [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). Poniższy przykład tworzy frontonu IPv4 i IPv6 w konfiguracji adresu IP o nazwie *dsLbFrontEnd_v4* i *dsLbFrontEnd_v6*:

```azurepowershell-interactive
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

```

### <a name="configure-back-end-address-pool"></a>Konfigurowanie puli adresów zaplecza

Utwórz pulę adresów zaplecza przy użyciu polecenia [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). W kolejnych krokach maszyny wirtualne zostaną dołączone do tej puli zaplecza. Poniższy przykład tworzy pule adresów zaplecza o nazwie *dsLbBackEndPool_v4* i *dsLbBackEndPool_v6* obejmujący maszynami wirtualnymi z konfiguracjami IPV4 i IPv6 karty Sieciowej:

```azurepowershell-interactive
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. Zdefiniuj konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Aby upewnić się, że tylko dobrej kondycji maszyn wirtualnych odbierania ruchu, opcjonalnie możesz zdefiniować sondy kondycji. Podstawowego modułu równoważenia obciążenia używa sondy protokołu IPv4 w celu oceny kondycji dla IPv4 i IPv6 punktów końcowych na maszynach wirtualnych. Standardowego modułu równoważenia obciążenia obsługuje jawnie IPv6 sondy kondycji.

Utwórz regułę modułu równoważenia obciążenia przy użyciu polecenia [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). Poniższy przykład obejmuje tworzenie reguły modułu równoważenia obciążenia o nazwie *dsLBrule_v4* i *dsLBrule_v6* i równoważy ruch na *TCP* portu *80* do IPv4 i IPv6 konfiguracji adresów IP frontonu:

```azurepowershell-interactive
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

Utwórz podstawowy moduł równoważenia obciążenia przy użyciu polecenia [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). Poniższy przykład obejmuje tworzenie publicznego podstawowego modułu równoważenia obciążenia o nazwie *myLoadBalancer* za pomocą protokołów IPv4 i IPv6 adresu IP frontonu konfiguracje, pule zaplecza i reguł równoważenia obciążenia, które zostały utworzone w poprzednich krokach:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "MyLoadBalancer" `
-Sku "Basic" `
-FrontendIpConfiguration $frontendIPv4,$frontendIPv6 `
-BackendAddressPool $backendPoolv4,$backendPoolv6 `
-LoadBalancingRule $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych
Przed wdrożeniem maszyn wirtualnych i przetestowanie usługi Równoważenie, należy utworzyć pomocnicze zasoby sieci — zestaw dostępności, sieciowej grupy zabezpieczeń, sieci wirtualnej i wirtualne karty sieciowe. 
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

Utwórz sieciową grupę zabezpieczeń dla reguły rządzące ruchu przychodzącego i wychodzącego komunikacji w sieci Wirtualnej.

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

Tworzenie reguły grupy zabezpieczeń sieci, aby zezwolić na połączenia z Internetem za pośrednictwem portu 80 z [New AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

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

Utwórz sieć wirtualną przy użyciu polecenia [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Poniższy przykład obejmuje tworzenie sieci wirtualnej o nazwie *myVnet* z podsiecią *mySubnet*:

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

Tworzenie wirtualnych kart sieciowych z [New AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Poniższy przykład tworzy dwie wirtualne karty sieciowe zarówno w przypadku konfiguracji protokołów IPv4 i IPv6. (Po jednej karcie na każdą maszynę wirtualną, która zostanie utworzona na potrzeby aplikacji w kolejnych krokach).

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
$imageSKU = "2016-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null 
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1 

$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null 
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```

## <a name="determine-ip-addresses-of-the-ipv4-and-ipv6-endpoints"></a>Określić adresy IP punktów końcowych protokołów IPv4 i IPv6
Pobierz wszystkie obiekty interfejsu sieciowego w grupie zasobów, aby podsumować adresy IP używane w tym wdrożeniu przy użyciu `get-AzNetworkInterface`. Ponadto Uzyskaj adresy frontonu modułu równoważenia obciążenia punkty końcowe protokołów IPv4 i IPv6 z `get-AzpublicIpAddress`.

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
Na poniższej ilustracji przedstawiono przykładowe dane wyjściowe, która zawiera prywatne adresy IPv4 i IPv6 dwie maszyny wirtualne i adresy IPv4 i IPv6 frontonu modułu równoważenia obciążenia.

![Podsumowanie IP podwójny stos (IPv4/IPv6) wdrażanie aplikacji na platformie Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-application-summary.png)

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Wyświetl sieć wirtualna podwójnego stosu protokołu IPv6 w witrynie Azure portal
Sieć wirtualna podwójnego stosu protokołu IPv6 można wyświetlić w witrynie Azure portal w następujący sposób:
1. Na pasku wyszukiwania portalu wpisz *dsVnet*.
2. Gdy pozycja **myVirtualNetwork** pojawi się w wynikach wyszukiwania, wybierz ją. Spowoduje to uruchomienie **Przegląd** strony podwójnego stosu sieci wirtualnej o nazwie *dsVnet*. Podwójny stos sieci wirtualnej zawiera dwie karty sieciowe IPv4 i IPv6 konfiguracji znajduje się w podsieci podwójnego stosu, o nazwie *dsSubnet*.

  ![Podwójnego stosu protokołu IPv6 sieci wirtualnej na platformie Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> Protokół IPv6 dla sieci wirtualnej platformy Azure jest dostępna w witrynie Azure portal w trybie tylko do odczytu dla tej wersji zapoznawczej.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name dsRG1
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule utworzono podstawowy moduł równoważenia obciążenia za pomocą podwójnego konfigurację adresu IP frontonu (IPv4 i IPv6). Utworzono również dwie maszyny wirtualne, które karty sieciowe dołączone do dwóch konfiguracji adresu IP (IPV4 + IPv6), które zostały dodane do puli zaplecza modułu równoważenia obciążenia. Aby dowiedzieć się więcej na temat obsługi protokołu IPv6 w sieciach wirtualnych platformy Azure, zobacz [co to jest protokół IPv6 dla usługi Azure Virtual Network?](ipv6-overview.md)