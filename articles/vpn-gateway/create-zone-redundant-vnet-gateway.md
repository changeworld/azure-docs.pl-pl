---
title: Tworzenie strefowo nadmiarowej bramy sieci wirtualnej w Strefy dostępności platformy Azure
description: Wdróż VPN Gateway i bram ExpressRoute w Strefy dostępności
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/10/2020
ms.author: cherylmc
ms.openlocfilehash: d8c6b68a38d4b60cf7a3194e6a5ded8804cc416f
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77150190"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>Tworzenie strefowo nadmiarowej bramy sieci wirtualnej w Strefy dostępności platformy Azure

Bramy sieci VPN i ExpressRoute można wdrożyć w Strefy dostępności platformy Azure. Zapewnia to elastyczność, skalowalność i wyższą dostępność dla bram sieci wirtualnej. Wdrażanie bram w Strefy dostępności platformy Azure fizycznie i logicznie oddziela bramy w danym regionie, chroniąc połączenie sieci lokalnej na platformie Azure przed awariami na poziomie strefy. Aby uzyskać więcej informacji, zobacz [about Zone-nadmiarowe bramy sieci wirtualnej](about-zone-redundant-vnet-gateways.md) i [Informacje o strefy dostępności platformy Azure](../availability-zones/az-overview.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="variables"></a>1. Zadeklaruj zmienne

Zadeklaruj zmienne, których chcesz użyć. Użyj poniższego przykładu, podstawiając własne wartości tam, gdzie to konieczne. Jeśli zamkniesz sesję programu PowerShell/Cloud Shell w dowolnym momencie podczas wykonywania, po prostu skopiuj i wklej ponownie wartości, aby ponownie zadeklarować zmienne. Podczas określania lokalizacji Sprawdź, czy określony region jest obsługiwany. Aby uzyskać więcej informacji, zobacz [często zadawane pytania](#faq).

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

## <a name="configure"></a>2. Utwórz sieć wirtualną

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

Podsieć bramy zawiera zastrzeżone adresy IP, z których korzystają usługi bramy sieci wirtualnej. Aby dodać i ustawić podsieć bramy, użyj następujących przykładów:

Dodaj podsieć bramy.

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Ustaw konfigurację podsieci bramy dla sieci wirtualnej.

```azurepowershell-interactive
$getvnet | Set-AzVirtualNetwork
```
## <a name="publicip"></a>4. Zażądaj publicznego adresu IP
 
W tym kroku Wybierz instrukcje, które mają zastosowanie do bramy, którą chcesz utworzyć. Wybór stref do wdrożenia bram zależy od stref określonych dla publicznego adresu IP.

### <a name="ipzoneredundant"></a>W przypadku bram strefowo nadmiarowych

Zażądaj publicznego adresu IP przy użyciu **standardowej** jednostki SKU PublicIpaddress i nie określaj żadnej strefy. W takim przypadku standardowy publiczny adres IP, który został utworzony, będzie strefowo nadmiarowym publicznym adresem IP.   

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="ipzonalgw"></a>Dla bram strefowych

Zażądaj publicznego adresu IP przy użyciu **standardowej** jednostki SKU PublicIpaddress. Określ strefę (1, 2 lub 3). Wszystkie wystąpienia bramy zostaną wdrożone w tej strefie.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>Dla bram regionalnych

Zażądaj publicznego adresu IP z **podstawową** jednostką SKU PublicIpaddress. W takim przypadku Brama jest wdrażana jako brama regionalna i nie ma żadnej wbudowanej w nią nadmiarowości strefy. Wystąpienia bramy są tworzone odpowiednio w dowolnych strefach.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>5. Utwórz konfigurację adresu IP

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>6. Tworzenie bramy

Utwórz bramę sieci wirtualnej.

### <a name="for-expressroute"></a>Dla ExpressRoute

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute -GatewaySku ErGw1AZ
```

### <a name="for-vpn-gateway"></a>Dla VPN Gateway

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1AZ
```

## <a name="faq"></a>Często zadawane pytania

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Co zmienią się, gdy wdrażam te nowe jednostki SKU?

Z perspektywy można wdrożyć bramy z nadmiarowością strefy. Oznacza to, że wszystkie wystąpienia bram zostaną wdrożone między Strefy dostępności platformy Azure, a każda strefa dostępności jest inną domeną błędów i aktualizacji. Dzięki temu bramy są bardziej niezawodne, dostępne i odporne na awarie stref.

### <a name="can-i-use-the-azure-portal"></a>Czy mogę użyć Azure Portal?

Tak, możesz użyć Azure Portal do wdrożenia nowych jednostek SKU. Te nowe jednostki SKU będą jednak widoczne tylko w tych regionach świadczenia usługi Azure, w których Strefy dostępności platformy Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Jakie regiony są dostępne dla mnie, aby można było korzystać z nowych jednostek SKU?

Zobacz [strefy dostępności](../availability-zones/az-overview.md#services-support-by-region) , aby uzyskać najnowszą listę dostępnych regionów.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Czy mogę zmienić/migrować/uaktualnić istniejące bramy sieci wirtualnej do bram nadmiarowych lub stref stref?

Migrowanie istniejących bram sieci wirtualnej do strefowo nadmiarowe lub bramy strefowe nie jest obecnie obsługiwane. Można jednak usunąć istniejącą bramę i ponownie utworzyć bramę nadmiarową lub strefowo.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Czy mogę wdrożyć bramy sieci VPN i usługi Express Route w tej samej sieci wirtualnej?

Obsługiwane są oba bramy sieci VPN i Express Route w tej samej sieci wirtualnej. Należy jednak zarezerwować zakres adresów IP/27 dla podsieci bramy.
