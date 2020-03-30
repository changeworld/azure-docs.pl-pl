---
title: Tworzenie bramy sieci wirtualnej nadmiarowej strefy w strefach dostępności platformy Azure
description: Wdrażanie bram sieci VPN i bram usługi ExpressRoute w strefach dostępności
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/10/2020
ms.author: cherylmc
ms.openlocfilehash: d8c6b68a38d4b60cf7a3194e6a5ded8804cc416f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77150190"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>Tworzenie bramy sieci wirtualnej nadmiarowej strefy w strefach dostępności platformy Azure

Bramy sieci VPN i usługi ExpressRoute można wdrożyć w strefach dostępności platformy Azure. Zapewni to elastyczność, skalowalność i większą dostępność bram sieci wirtualnej. Wdrażanie bram w strefach dostępności platformy Azure fizycznie i logicznie dzieli bramy w danym regionie, chroniąc jednocześnie lokalną łączność sieci z platformą Azure przed błędami na poziomie strefy. Aby uzyskać więcej informacji, zobacz [Informacje o bramy sieci wirtualnej nadmiarowe strefy](about-zone-redundant-vnet-gateways.md) i o [strefach dostępności platformy Azure](../availability-zones/az-overview.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="1-declare-your-variables"></a><a name="variables"></a>1. Zadeklaruj swoje zmienne

Zadeklaruj zmienne, których chcesz użyć. Użyj poniższego przykładu, podstawiając własne wartości tam, gdzie to konieczne. Jeśli zamkniesz sesję programu PowerShell/Cloud Shell w dowolnym momencie podczas ćwiczenia, po prostu skopiuj i wklej wartości ponownie, aby ponownie zadeklarować zmienne. Podczas określania lokalizacji sprawdź, czy określony region jest obsługiwany. Aby uzyskać więcej informacji, zobacz często zadawane [pytania](#faq).

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

## <a name="2-create-the-virtual-network"></a><a name="configure"></a>2. Tworzenie sieci wirtualnej

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

## <a name="3-add-the-gateway-subnet"></a><a name="gwsub"></a>3. Dodaj podsieć bramy

Podsieć bramy zawiera zastrzeżone adresy IP używane przez usługi bramy sieci wirtualnej. Aby dodać i ustawić podsieć bramy, należy dodać i ustawić następujące przykłady:

Dodaj podsieć bramy.

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Ustawianie konfiguracji podsieci bramy dla sieci wirtualnej.

```azurepowershell-interactive
$getvnet | Set-AzVirtualNetwork
```
## <a name="4-request-a-public-ip-address"></a><a name="publicip"></a>4. Poproś o publiczny adres IP
 
W tym kroku wybierz instrukcje, które mają zastosowanie do bramy, którą chcesz utworzyć. Wybór stref do wdrażania bram zależy od stref określonych dla publicznego adresu IP.

### <a name="for-zone-redundant-gateways"></a><a name="ipzoneredundant"></a>Dla bram nadmiarowych stref

Zażądaj publicznego adresu IP ze **standardową** jednostką SKU publicIpaddress i nie określaj żadnej strefy. W takim przypadku utworzony standardowy publiczny adres IP będzie publicznym ipem nadmiarowym strefy.   

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="for-zonal-gateways"></a><a name="ipzonalgw"></a>Dla bram strefowych

Poproś o publiczny adres IP z **jednostką SKU Standard** PublicIpaddress. Określ strefę (1, 2 lub 3). Wszystkie wystąpienia bramy zostaną wdrożone w tej strefie.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="for-regional-gateways"></a><a name="ipregionalgw"></a>Dla bram regionalnych

Poproś o publiczny adres IP z **podstawową** jednostką SKU PublicIpaddress. W takim przypadku brama jest wdrażana jako brama regionalna i nie ma żadnej nadmiarowości strefy wbudowanej w bramę. Wystąpienia bramy są tworzone odpowiednio w dowolnej strefie.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="5-create-the-ip-configuration"></a><a name="gwipconfig"></a>5. Tworzenie konfiguracji IP

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="6-create-the-gateway"></a><a name="gwconfig"></a>6. Tworzenie bramy

Utwórz bramę sieci wirtualnej.

### <a name="for-expressroute"></a>Dla usługi ExpressRoute

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute -GatewaySku ErGw1AZ
```

### <a name="for-vpn-gateway"></a>Dla bramy sieci VPN

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1AZ
```

## <a name="faq"></a><a name="faq"></a>Najczęściej zadawane pytania

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Co się zmieni po wdrożeniu tych nowych jednostek SKU?

Z twojej perspektywy można wdrażać bramy z nadmiarowością stref. Oznacza to, że wszystkie wystąpienia bram zostaną wdrożone w strefach dostępności platformy Azure, a każda strefa dostępności jest inną domeną błędów i aktualizacji. Dzięki temu bramy są bardziej niezawodne, dostępne i odporne na awarie stref.

### <a name="can-i-use-the-azure-portal"></a>Czy mogę korzystać z witryny Azure portal?

Tak, można użyć witryny Azure Portal do wdrożenia nowych jednostek SKU. Jednak te nowe jednostki SKU będą widoczne tylko w tych regionach platformy Azure, które mają strefy dostępności platformy Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Jakie regiony są dostępne dla mnie do korzystania z nowych jednostek SKU?

Zobacz [Strefy dostępności,](../availability-zones/az-overview.md#services-support-by-region) aby uzyskać najnowszą listę dostępnych regionów.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Czy mogę zmienić/przeprowadzić migrację/uaktualnić istniejące bramy sieci wirtualnej do bram strefowych lub strefowych?

Migracja istniejących bram sieci wirtualnej do bram strefowych lub strefowych nie jest obecnie obsługiwana. Można jednak usunąć istniejącą bramę i ponownie utworzyć bramę strefową lub strefową.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Czy mogę wdrożyć bramy sieci VPN i express route w tej samej sieci wirtualnej?

Współistnienie bram sieci VPN i express route w tej samej sieci wirtualnej jest obsługiwane. Należy jednak zarezerwować zakres adresów IP /27 dla podsieci bramy.
