---
title: Ustawienia bramy sieci VPN dla połączenia platformy Azure między środowiskami lokalnymi | Dokumentacja firmy Microsoft
description: Więcej informacji na temat ustawień bramy sieci VPN dla bramy sieci wirtualnej platformy Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: cherylmc
ms.openlocfilehash: 4030c196d6a4de721b640f5da0b692f4d8157d12
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59049867"
---
# <a name="about-vpn-gateway-configuration-settings"></a>Informacje o ustawieniach konfiguracji bramy sieci VPN

Tworzenie bramy sieci VPN jest typem bramy sieci wirtualnej, który wysyła zaszyfrowany ruch sieciowy między siecią wirtualną i Twojej lokalizacji lokalnej przez połączenie publiczne. Tworzenie bramy sieci VPN umożliwia również wysyłanie ruchu między sieciami wirtualnymi w sieci szkieletowej platformy Azure.

Połączenie bramy sieci VPN zależy od konfiguracji wielu zasobów, z których każdy zawiera konfigurowalnych ustawień. Sekcje w tym artykule omówiono, zasoby i ustawienia, które odnoszą się do bramy sieci VPN dla sieci wirtualnej utworzonej w modelu wdrażania usługi Resource Manager. Możesz znaleźć opisy i diagramy topologii dla każdego rozwiązania połączenia w [VPN Gateway — informacje](vpn-gateway-about-vpngateways.md) artykułu.

Wartości w tym artykule mają zastosowanie bram sieci VPN (bramy sieci wirtualnej, które używają zmienna-GatewayType Vpn). Ten artykuł nie obejmuje wszystkie typy bram lub bram strefowo nadmiarowe.

* Aby uzyskać wartości, które są stosowane do klasy-GatewayType "ExpressRoute", zobacz [bramy sieci wirtualnej dla usługi ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).

* Strefowo nadmiarowe bram, zobacz [o bramach strefowo nadmiarowe](about-zone-redundant-vnet-gateways.md).

* Dla wirtualnej sieci WAN, zobacz [o wirtualne sieci WAN](../virtual-wan/virtual-wan-about.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="gwtype"></a>Typy bram

Każda sieć wirtualna może mieć tylko jedną bramę sieci wirtualnej dla każdego typu. Podczas tworzenia bramy sieci wirtualnej, upewnij się, że typ bramy jest prawidłowy dla danej konfiguracji.

Dostępne wartości - GatewayType są następujące:

* Vpn
* ExpressRoute

Brama sieci VPN wymaga `-GatewayType` *Vpn*.

Przykład:

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gwsku"></a>Jednostki SKU bramy

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-a-gateway-sku"></a>Konfigurowanie bramy jednostki SKU

#### <a name="azure-portal"></a>Azure Portal

Jeśli używasz witryny Azure portal do tworzenia bramy sieci wirtualnej usługi Resource Manager za pomocą listy rozwijanej można wybrać jednostkę SKU bramy. Opcje, które są prezentowane za pomocą odpowiadają typu bramy i wybranego typu sieci VPN.

#### <a name="powershell"></a>PowerShell

W poniższym przykładzie programu PowerShell `-GatewaySku` jako VpnGw1. Podczas tworzenia bramy przy użyciu programu PowerShell, musisz najpierw Utwórz konfigurację adresów IP, a następnie użyć zmiennej do odwoływania się do niego. W tym przykładzie zmienna konfiguracyjna jest $gwipconfig.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'US East' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWPIP --resource-group TestRG1 --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait
```

###  <a name="resizechange"></a>Zmiana rozmiaru lub zmiana jednostki SKU

Jeśli masz bramy sieci VPN, którego chcesz użyć innej jednostki SKU bramy opcje są albo Zmień rozmiar jednostki SKU bramy lub zmienić na inną jednostkę SKU. Po zmianie na inną jednostkę SKU bramy, całkowicie Usuń istniejącą bramę i tworzyć nowe. Tworzenie bramy może zająć do 45 minut kompilacji. W odróżnieniu od podczas zmiany rozmiaru bramy jednostki SKU i nie ma długi Przestój, ponieważ nie trzeba usunąć i ponownie utworzyć bramę. Jeśli masz opcję, aby zmienić rozmiar jednostki SKU bramy, zamiast go zmienić, należy to zrobić. Jednak istnieją reguły dotyczące rozmiaru:

1. Można zmienić rozmiar jednostek SKU, wybierając z opcji VpnGw1, VpnGw2 i VpnGw3.
2. Podczas pracy ze starymi jednostkami SKU bramy można zmienić rozmiar, wybierając z opcji Basic, Standard i HighPerformance.
3. **Nie można** zmienić rozmiaru z opcji Basic/Standard/HighPerformance na nowe opcje VpnGw1/VpnGw2/VpnGw3. Zamiast tego należy [zmienić](#change) do nowych jednostek SKU.

#### <a name="resizegwsku"></a>Aby zmienić rozmiar bramy

[!INCLUDE [Resize a SKU](../../includes/vpn-gateway-gwsku-resize-include.md)]

####  <a name="change"></a>Aby zmienić ze starej (starsze) do nowej jednostki SKU

[!INCLUDE [Change a SKU](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="connectiontype"></a>Typy połączeń

W modelu wdrażania usługi Resource Manager każda konfiguracja wymaga typu połączenia bramy określonej sieci wirtualnej. Dostępne wartości opcji `-ConnectionType` w programie PowerShell w usłudze Resource Manager to:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

W poniższym przykładzie programu PowerShell, możemy utworzyć połączenie S2S, które wymaga typu połączenia *IPsec*.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpntype"></a>Typy sieci VPN

Podczas tworzenia bramy sieci wirtualnej dla konfiguracji bramy sieci VPN, należy określić typ sieci VPN. Typ sieci VPN, który wybierzesz, zależy od topologii połączenia, który chcesz utworzyć. Na przykład połączenia P2S wymaga typu sieci VPN typu RouteBased. Typ sieci VPN może także zależeć od sprzętu, którego używasz. Konfiguracje S2S wymagają urządzenia sieci VPN. Niektóre urządzenia sieci VPN obsługują tylko określonego typu sieci VPN.

Typ sieci VPN, którą wybierzesz musi spełniać wszystkie połączenia wymagania dotyczące rozwiązania, którą chcesz utworzyć. Na przykład, jeśli chcesz utworzyć połączenie bramy sieci VPN S2S i połączenia bramy sieci VPN P2S dla tej samej sieci wirtualnej, można użyć typu sieci VPN *RouteBased* ponieważ P2S wymagają typu sieci VPN typu RouteBased. Czy trzeba będzie również sprawdzić, czy urządzenie sieci VPN obsługiwana połączenia sieci VPN typu RouteBased. 

Po utworzeniu bramy sieci wirtualnej nie można zmienić typu sieci VPN. Musisz usunąć bramy sieci wirtualnej i Utwórz nowe. Istnieją dwa typy sieci VPN:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

W poniższym przykładzie programu PowerShell `-VpnType` jako *RouteBased*. Podczas tworzenia bramy musisz upewnić się, że typ -VpnType jest prawidłowy dla danej konfiguracji.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="requirements"></a>Wymagania dotyczące bramy

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gwsub"></a>Podsieć bramy

Przed utworzeniem bramy sieci VPN, należy utworzyć podsieć bramy. Podsieć bramy zawiera adresy IP, które używają bramy sieci wirtualnej maszyny wirtualne i usługi. Podczas tworzenia bramy sieci wirtualnej maszyny wirtualne bramy są wdrażane w podsieci bramy i skonfigurować wymagane ustawienia bramy sieci VPN. Nigdy nie należy wdrażać dowolne inne (na przykład dodatkowych maszyn wirtualnych) w podsieci bramy. Podsieć bramy musi mieć nazwę "GatewaySubnet" działało poprawnie. Nadanie podsieci bramy nazwy "GatewaySubnet" umożliwia wiedzieć, że jest to podsieć do wdrożenia bramy sieci wirtualnej maszyn wirtualnych i usług platformy Azure.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Podczas tworzenia podsieci bramy należy określić liczbę zawartych w niej adresów IP. Adresy IP w podsieci bramy są przydzielane do usług bramy i maszyny wirtualne bramy. Niektóre konfiguracje wymagają większej liczby adresów IP niż inne. Przyjrzyj się instrukcje dotyczące konfiguracji, który chcesz utworzyć i sprawdź, czy podsieci bramy, w której chcesz utworzyć spełnia te wymagania. Ponadto można upewnij się, że podsieć bramy zawiera wystarczającą liczbę adresów IP, aby uwzględnić możliwe przyszłe konfiguracje dodatkowe. Chociaż można utworzyć podsieci bramy tak małej jak/29, zaleca się utworzenie podsieci bramy/28 i większych (/ 28, wartość/27, / 26 itp.). W ten sposób w przypadku dodania funkcji w przyszłości, nie musisz usunąć bramy, a następnie usunięcie i ponowne utworzenie podsieci bramy, aby umożliwić większej liczby adresów IP.

W poniższym przykładzie programu PowerShell usługi Resource Manager zawiera podsieć bramy o nazwie GatewaySubnet. Widać, że notacja CIDR Określa rozmiar/27, który daje wystarczającą liczbę adresów IP w przypadku większości konfiguracji, które obecnie istnieją.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="lng"></a>Bramy sieci lokalnej

 Brama sieci lokalnej różni się od bramą sieci wirtualnej. Podczas tworzenia konfiguracji bramy sieci VPN, Brama sieci lokalnej zazwyczaj reprezentuje Twojej lokalizacji lokalnej. W klasycznym modelu wdrażania brama sieci była określana jako lokacja lokalna.

Nazwij bramy sieci lokalnej, publiczny adres IP lokalnego urządzenia sieci VPN i określić prefiksy adresów, które znajdują się w lokalizacji lokalnej. Azure porównaniem z prefiksy adresów docelowych dla ruchu sieciowego, konsultacje dotyczące konfiguracji, określony dla bramy sieci lokalnej i w związku z tym kieruje pakiety. Należy również określić bramy sieci lokalnej w przypadku konfiguracji sieci wirtualnej między sieciami wirtualnymi, korzystających z połączenia bramy sieci VPN.

W poniższym przykładzie programu PowerShell tworzy nową bramę sieci lokalnej:

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Czasami zachodzi potrzeba modyfikowanie ustawień bramy sieci lokalnej. Na przykład podczas dodawania lub modyfikowania zakres adresów, lub jeśli zmieni się adres IP urządzenia sieci VPN. Zobacz [modyfikowanie ustawień lokalnej bramy sieci przy użyciu programu PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>REST API, w przypadku poleceń cmdlet programu PowerShell i interfejs wiersza polecenia

Aby uzyskać dodatkowe zasoby techniczne i wymagań określonej składni, korzystając z interfejsów API REST, poleceń cmdlet programu PowerShell lub wiersza polecenia platformy Azure w przypadku konfiguracji bramy sieci VPN zobacz następujące strony:

| **Wdrożenie klasyczne** | **Resource Manager** |
| --- | --- |
| [Program PowerShell](/powershell/module/az.network/#networking) |[Program PowerShell](/powershell/module/az.network#vpn) |
| [Interfejs API REST](https://msdn.microsoft.com/library/jj154113) |[Interfejs API REST](/rest/api/network/virtualnetworkgateways) |
| Nieobsługiwane | [Interfejs wiersza polecenia platformy Azure](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o konfiguracjach dostępnych połączeń, zobacz [VPN Gateway — informacje](vpn-gateway-about-vpngateways.md).
