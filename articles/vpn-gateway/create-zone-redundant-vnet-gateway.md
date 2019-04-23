---
title: Tworzenie bramy sieci wirtualnej strefowo nadmiarowy w strefy dostępności platformy Azure | Dokumentacja firmy Microsoft
description: Wdrażanie bramy VPN Gateway i ExpressRoute w strefach dostępności
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: cherylmc
ms.openlocfilehash: f2b459ccfd7e3f513b9b6526864321ce247ae7aa
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004520"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>Tworzenie bramy sieci wirtualnej strefowo nadmiarowy w strefy dostępności platformy Azure

Można wdrożyć bramy sieci VPN i ExpressRoute w strefach dostępności platformy Azure. To zapewniają elastyczność, skalowalność i większą dostępność bramy sieci wirtualnej. Wdrażanie bramy w strefach dostępności Azure fizycznie i logicznie dzieli bramy w danym regionie, przy jednoczesnej ochronie łączności sieci środowiska lokalnego do platformy Azure przed awariami poziomu strefy. Aby uzyskać informacje, zobacz [o bramach sieci wirtualnej strefowo nadmiarowe](about-zone-redundant-vnet-gateways.md) i [strefy dostępności platformy Azure o](../availability-zones/az-overview.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Możesz użyć programu PowerShell zainstalowane lokalnie na komputerze lub w usłudze Azure Cloud Shell. Jeśli zdecydujesz się zainstalować i korzystać z programu PowerShell lokalnie, ta funkcja wymaga najnowszej wersji modułu programu PowerShell.

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>Przy użyciu programu PowerShell lokalnie

Jeśli używasz programu PowerShell lokalnie na komputerze, a nie przy użyciu usługi Cloud Shell, musisz zainstalować moduł PowerShell 1.0.0 lub nowszym. Aby sprawdzić wersję programu PowerShell, który został zainstalowany, użyj następującego polecenia:

```azurepowershell
Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
```

Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="variables"></a>1. Zadeklarowanie zmiennych

Poniżej przedstawiono wartości używanych na potrzeby procedury w przykładzie. Ponadto niektóre przykłady używać zmiennych zadeklarowanych w ramach kroków. Jeśli używane są następujące kroki we własnym środowisku, koniecznie Zastąp te wartości własnymi. Podczas określania lokalizacji, należy sprawdzić, czy jest obsługiwany region, który określisz. Aby uzyskać więcej informacji, zobacz [— często zadawane pytania](#faq).

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="configure"></a>2. Tworzenie sieci wirtualnej

Utwórz grupę zasobów.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

Utwórz sieć wirtualną.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="gwsub"></a>3. Dodaj podsieć bramy

Podsieć bramy zawiera zastrzeżone adresy IP, z których korzystają usługi bramy sieci wirtualnej. Aby dodać i ustawić podsieć bramy można użyć następujących przykładów:

Dodaj podsieć bramy.

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Ustaw konfigurację podsieci bramy dla sieci wirtualnej.

```azurepowershell-interactive
$getvnet | Set-AzVirtualNetwork
```
## <a name="publicip"></a>4. Przesłanie żądania dotyczącego publicznego adresu IP
 
W tym kroku wybierz instrukcje, które są stosowane do bramy, która ma zostać utworzona. Wybór strefy do wdrażania bramy zależy od strefy, określony dla publicznego adresu IP.

### <a name="ipzoneredundant"></a>Strefowo nadmiarowe bram

Żądanie publicznego adresu IP za pomocą **standardowa** jednostki SKU publicznego adresu IP, a nie określać żadnej strefy. W tym przypadku standardowego publicznego adresu IP utworzonego będzie strefowo nadmiarowy publiczny adres IP.   

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="ipzonalgw"></a>Strefowy bram

Żądanie publicznego adresu IP za pomocą **standardowa** jednostki SKU publicznego adresu IP. Określenie strefy (1, 2 lub 3). Wszystkie wystąpienia bramy zostanie wdrożony w tej strefie.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>Regionalne bram

Żądanie publicznego adresu IP za pomocą **podstawowe** jednostki SKU publicznego adresu IP. W takich przypadkach brama jest wdrażana jako brama regionalnych i nie ma żadnych strefy nadmiarowości wbudowanej w bramie. Bramy są tworzone wystąpienia w żadnych stref odpowiednio.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>5. Utwórz konfigurację adresów IP

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>6. Tworzenie bramy

Tworzenie bramy sieci wirtualnej.

### <a name="for-expressroute"></a>For ExpressRoute

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>For VPN Gateway

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="faq"></a>Często zadawane pytania

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Co się zmieni podczas wdrażania tych nowych jednostek SKU?

Z perspektywy można wdrożyć bram dzięki nadmiarowości strefy. Oznacza to, że wszystkie wystąpienia bramy zostanie wdrożone w różnych strefach dostępności platformy Azure, a każda strefa dostępności w innej domenie błędów i aktualizacji. Dzięki temu bram niezawodne, dostępna i odporna na awarie strefy.

### <a name="can-i-use-the-azure-portal"></a>Czy można użyć witryny Azure portal?

Tak, można użyć witryny Azure portal do wdrażania nowych jednostek SKU. Jednak zobaczą te nowe jednostki SKU tylko w tych regionach platformy Azure, które mają strefy dostępności platformy Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Jakie regiony są dostępne w użyciu nowych jednostek SKU?

Zobacz [strefy dostępności](../availability-zones/az-overview.md#services-support-by-region) najbardziej aktualną listę dostępnych regionów.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Czy mogę zmiany/migracja/uaktualnić Moje istniejącej bramy sieci wirtualnej do bram strefowo nadmiarowego lub strefowych?

Migrowanie istniejących bram sieci wirtualnej do bram strefowo nadmiarowego lub strefowych nie jest obecnie obsługiwane. Można jednak usunąć istniejącą bramę i ponownie utworzyć bramę strefowo nadmiarowego lub strefowych.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Czy można wdrożyć bramy sieci VPN i Expressroute w tej samej sieci wirtualnej?

Współistnienie bram sieci VPN i Expressroute w tej samej sieci wirtualnej jest obsługiwana. Jednak należy zarezerwować/27 zakres adresów IP podsieci bramy.
