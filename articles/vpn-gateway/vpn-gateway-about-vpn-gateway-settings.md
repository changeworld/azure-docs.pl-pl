---
title: 'Brama sieci VPN platformy Azure: ustawienia konfiguracji'
description: Dowiedz się więcej o ustawieniach bramy sieci VPN dla bram sieci wirtualnej platformy Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: d7a2040748d170b4e536df59947ea811f149d931
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244864"
---
# <a name="about-vpn-gateway-configuration-settings"></a>Ustawienia konfiguracji bramy sieci VPN — informacje

Brama sieci VPN to typ bramy sieci wirtualnej, która wysyła zaszyfrowany ruch między siecią wirtualną a lokalizacją lokalną za pośrednictwem połączenia publicznego. Można również użyć bramy sieci VPN do wysyłania ruchu między sieciami wirtualnymi w obszarze szkieletu platformy Azure.

Połączenie bramy sieci VPN zależy od konfiguracji wielu zasobów, z których każdy zawiera konfigurowalne ustawienia. W sekcjach w tym artykule omówiono zasoby i ustawienia, które odnoszą się do bramy sieci VPN dla sieci wirtualnej utworzonej w modelu wdrażania Menedżera zasobów. Opisy i diagramy topologii dla każdego rozwiązania połączenia można znaleźć w artykule [Informacje o bramie sieci VPN.](vpn-gateway-about-vpngateways.md)

Wartości podane w tym artykule dotyczą bram sieci VPN (bram sieci wirtualnej korzystających z sieci VPN typu GatewayType). Ten artykuł nie obejmuje wszystkich typów bram ani bram nadmiarowych stref.

* Aby uzyskać wartości, które mają zastosowanie do -GatewayType "ExpressRoute", zobacz [Bramy sieci wirtualnej dla usługi ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).

* W przypadku bram nadmiarowych stref zobacz [Informacje o bramy nadmiarowe strefy](about-zone-redundant-vnet-gateways.md).

* Aby uzyskać informacje virtual wan, zobacz [Informacje o wirtualnej sieci WAN](../virtual-wan/virtual-wan-about.md).



## <a name="gateway-types"></a><a name="gwtype"></a>Typy bram

Każda sieć wirtualna może mieć tylko jedną bramę sieci wirtualnej każdego typu. Podczas tworzenia bramy sieci wirtualnej należy upewnić się, że typ bramy jest poprawny dla twojej konfiguracji.

Dostępne wartości dla -GatewayType to:

* Vpn
* ExpressRoute

Brama sieci `-GatewayType` VPN wymaga *sieci Vpn*.

Przykład:

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gateway-skus"></a><a name="gwsku"></a>Jednostki SKU bramy

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-a-gateway-sku"></a>Konfigurowanie jednostki SKU bramy

#### <a name="azure-portal"></a>Portal Azure

Jeśli używasz witryny Azure portal do tworzenia bramy sieci wirtualnej Usługi Resource Manager, można wybrać jednostkę SKU bramy przy użyciu listy rozwijanej. Opcje, z którymi są prezentowane, odpowiadają wybranemu typowi bramy i sieci VPN.

#### <a name="powershell"></a>PowerShell

Poniższy przykład programu PowerShell określa `-GatewaySku` jako VpnGw1. Podczas tworzenia bramy za pomocą programu PowerShell należy najpierw utworzyć konfigurację IP, a następnie użyć zmiennej, aby się do niej odwołać. W tym przykładzie zmienna konfiguracji jest $gwipconfig.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'US East' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWPIP --resource-group TestRG1 --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait
```

###  <a name="resizing-or-changing-a-sku"></a><a name="resizechange"></a>Zmienianie rozmiaru lub zmienianie jednostki SKU

Jeśli masz bramę sieci VPN i chcesz użyć innej jednostki SKU bramy, dostępne są opcje zmiany rozmiaru jednostki SKU bramy lub zmiany na inną jednostkę SKU. Po zmianie na inną jednostkę SKU bramy, należy całkowicie usunąć istniejącą bramę i utworzyć nową. Tworzenie bramy może potrwać do 45 minut. Dla porównania podczas ponownego rozmiaru jednostki SKU bramy nie ma zbyt wiele przestojów, ponieważ nie trzeba usuwać i odbudowywać bramy. Jeśli masz możliwość zmiany rozmiaru jednostki SKU bramy, a nie jej zmiany, należy to zrobić. Istnieją jednak zasady dotyczące zmiany rozmiaru:

1. Z wyjątkiem podstawowej jednostki SKU można zmienić rozmiar jednostki SKU bramy sieci VPN do innej jednostki SKU bramy sieci VPN w ramach tej samej generacji (generacji 1 lub generacji2). Na przykład VpnGw1 generacji1 można zwymiarować na VpnGw2 generacji1, ale nie na VpnGw2 generacji2.
2. Podczas pracy ze starymi jednostkami SKU bramy można zmienić rozmiar, wybierając z opcji Basic, Standard i HighPerformance.
3. Nie **można** zmienić rozmiaru jednostek SKU Basic/Standard/HighPerformance na jednostki SKU VpnGw. Zamiast tego należy [zmienić](#change) na nowe jednostki SKU.

#### <a name="to-resize-a-gateway"></a><a name="resizegwsku"></a>Aby zmienić rozmiar bramy

[!INCLUDE [Resize a SKU](../../includes/vpn-gateway-gwsku-resize-include.md)]

####  <a name="to-change-from-an-old-legacy-sku-to-a-new-sku"></a><a name="change"></a>Aby zmienić starą (starszą) jednostkę SKU na nową jednostkę SKU

[!INCLUDE [Change a SKU](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="connection-types"></a><a name="connectiontype"></a>Typy połączeń

W modelu wdrażania Menedżera zasobów każda konfiguracja wymaga określonego typu połączenia bramy sieci wirtualnej. Dostępne wartości opcji `-ConnectionType` w programie PowerShell w usłudze Resource Manager to:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

W poniższym przykładzie programu PowerShell tworzymy połączenie S2S, które wymaga typu połączenia *IPsec*.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpn-types"></a><a name="vpntype"></a>Typy sieci VPN

Podczas tworzenia bramy sieci wirtualnej dla konfiguracji bramy sieci VPN należy określić typ sieci VPN. Typ sieci VPN, który wybierzesz, zależy od topologii połączeń, którą chcesz utworzyć. Na przykład połączenie P2S wymaga typu sieci VPN opartej na routicie. Typ sieci VPN może również zależeć od używanego sprzętu. Konfiguracje S2S wymagają urządzenia sieci VPN. Niektóre urządzenia SIECI VPN obsługują tylko określony typ sieci VPN.

Wybrany typ sieci VPN musi spełniać wszystkie wymagania dotyczące połączenia dla rozwiązania, które chcesz utworzyć. Na przykład jeśli chcesz utworzyć połączenie bramy sieci VPN S2S i połączenie bramy sieci VPN P2S dla tej samej sieci wirtualnej, należy użyć typu VPN *RouteBased,* ponieważ P2S wymaga typu Sieci VPN opartej na routinie. Należy również sprawdzić, czy urządzenie sieci VPN obsługuje połączenie sieci VPN na podstawie trasy. 

Po utworzeniu bramy sieci wirtualnej nie można zmienić typu sieci VPN. Musisz usunąć bramę sieci wirtualnej i utworzyć nową. Istnieją dwa typy sieci VPN:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Poniższy przykład programu PowerShell określa `-VpnType` jako *RouteBased*. Podczas tworzenia bramy musisz upewnić się, że typ -VpnType jest prawidłowy dla danej konfiguracji.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="gateway-requirements"></a><a name="requirements"></a>Wymagania dotyczące bramy

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gateway-subnet"></a><a name="gwsub"></a>Podsieć bramy

Przed utworzeniem bramy sieci VPN należy utworzyć podsieć bramy. Podsieć bramy zawiera adresy IP używane przez maszyny wirtualne i usługi bramy sieci wirtualnej. Podczas tworzenia bramy sieci wirtualnej maszyny wirtualne bramy są wdrażane w podsieci bramy i konfigurowane przy konieczności tworzenia ustawień bramy sieci VPN. Nigdy nie wdrażaj niczego innego (na przykład dodatkowych maszyn wirtualnych) w podsieci bramy. Podsieć bramy musi mieć nazwę "GatewaySubnet", aby działała poprawnie. Nazwanie podsieci bramy "GatewaySubnet" informuje platformę Azure, że jest to podsieć do wdrożenia maszyn wirtualnych i usług bramy sieci wirtualnej.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Podczas tworzenia podsieci bramy należy określić liczbę zawartych w niej adresów IP. Adresy IP w podsieci bramy są przydzielane do maszyn wirtualnych bramy i usług bramy. Niektóre konfiguracje wymagają większej liczby adresów IP niż inne. 

Podczas planowania rozmiaru podsieci bramy zapoznaj się z dokumentacją konfiguracji, którą planujesz utworzyć. Na przykład konfiguracja współistnienia bramy usługi ExpressRoute/VPN wymaga większej podsieci bramy niż większość innych konfiguracji. Ponadto można upewnić się, że podsieć bramy zawiera wystarczającą liczbę adresów IP, aby pomieścić możliwe przyszłe dodatkowe konfiguracje. Chociaż można utworzyć podsieć bramy tak małe, jak /29, zaleca się utworzenie podsieci bramy /27 lub większej (/27, /26 itp.), jeśli masz do dyspozycji przestrzeń adresową. Będzie to pomieścić większość konfiguracji.

W poniższym przykładzie programu PowerShell menedżera zasobów przedstawiono podsieć bramy o nazwie GatewaySubnet. Można zobaczyć notacji CIDR określa /27, który pozwala na wystarczającą liczbę adresów IP dla większości konfiguracji, które obecnie istnieją.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="local-network-gateways"></a><a name="lng"></a>Bramy sieci lokalnej

 Brama sieci lokalnej różni się od bramy sieci wirtualnej. Podczas tworzenia konfiguracji bramy sieci VPN brama sieci lokalnej zwykle reprezentuje lokalizację lokalną. W klasycznym modelu wdrażania brama sieci była określana jako lokacja lokalna.

Nadaj bramie sieci lokalnej nazwę, publiczny adres IP lokalnego urządzenia sieci VPN i określisz prefiksy adresów, które znajdują się w lokalizacji lokalnej. Platforma Azure analizuje prefiksy adresu docelowego dla ruchu sieciowego, sprawdza konfigurację określoną dla bramy sieci lokalnej i odpowiednio kieruje pakiety. Można również określić bramy sieci lokalnej dla konfiguracji sieci wirtualnej do sieci wirtualnej, które używają połączenia bramy sieci VPN.

W poniższym przykładzie programu PowerShell utworzy nową bramę sieci lokalnej:

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Czasami trzeba zmodyfikować ustawienia bramy sieci lokalnej. Na przykład podczas dodawania lub modyfikowania zakresu adresów lub zmiany adresu IP urządzenia sieci VPN. Zobacz [Modyfikowanie ustawień bramy sieci lokalnej przy użyciu programu PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="rest-apis-powershell-cmdlets-and-cli"></a><a name="resources"></a>Interfejsy API REST, polecenia cmdlet programu PowerShell i interfejs wiersza polecenia

Aby uzyskać dodatkowe zasoby techniczne i określone wymagania składni podczas korzystania z interfejsów API REST, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia platformy Azure dla konfiguracji bramy sieci VPN, zobacz następujące strony:

| **Wdrożenie klasyczne** | **Menedżer zasobów** |
| --- | --- |
| [Powershell](/powershell/module/az.network/#networking) |[Powershell](/powershell/module/az.network#vpn) |
| [INTERFEJS API ODPOCZYNKU](https://msdn.microsoft.com/library/jj154113) |[INTERFEJS API ODPOCZYNKU](/rest/api/network/virtualnetworkgateways) |
| Nieobsługiwane | [Interfejs wiersza polecenia platformy Azure](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat dostępnych konfiguracji połączeń, zobacz [Informacje o bramie sieci VPN](vpn-gateway-about-vpngateways.md).
