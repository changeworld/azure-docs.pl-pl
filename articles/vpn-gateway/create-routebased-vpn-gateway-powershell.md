---
title: 'Brama sieci VPN platformy Azure: tworzenie bramy opartej na marszrutach: Program PowerShell'
description: Szybkie tworzenie bramy sieci VPN opartej na trasach przy użyciu programu PowerShell
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/10/2020
ms.author: cherylmc
ms.openlocfilehash: 8a4bb9d2ac7b8124fa9b1e00f3ecceda4f4a4cdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152962"
---
# <a name="create-a-route-based-vpn-gateway-using-powershell"></a>Tworzenie bramy sieci VPN opartej na marszrutach przy użyciu programu PowerShell

Ten artykuł ułatwia szybkie tworzenie bramy sieci VPN opartej na marszrutach przy użyciu programu PowerShell. Brama sieci VPN jest używana podczas tworzenia połączenia sieci VPN z siecią lokalną. Można również użyć bramy sieci VPN do łączenia sieci wirtualnych.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Kroki opisane w tym artykule utworzy sieć wirtualną, podsieć, podsieć bramy i bramę sieci VPN opartą na trasie (brama sieci wirtualnej). Po zakończeniu tworzenia bramy można następnie utworzyć połączenia. Te kroki wymagają subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

### <a name="working-with-azure-powershell"></a>Praca z programem Azure PowerShell

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów. Jeśli używasz programu PowerShell lokalnie, otwórz konsolę programu PowerShell z `Connect-AzAccount` podwyższonymi uprawnieniami i połącz się z platformą Azure za pomocą polecenia.

```azurepowershell-interactive
New-AzResourceGroup -Name TestRG1 -Location EastUS
```

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną przy użyciu polecenia [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Poniższy przykład tworzy sieć wirtualną o nazwie **VNet1** w lokalizacji **EastUS:**

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName TestRG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
```

Utwórz konfigurację podsieci przy użyciu polecenia cmdlet [New-AzVirtualNetworkSubnetConfig.](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Ustaw konfigurację podsieci dla sieci wirtualnej za pomocą polecenia cmdlet [Set-AzVirtualNetwork.](/powershell/module/az.network/Set-azVirtualNetwork)


```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="add-a-gateway-subnet"></a><a name="gwsubnet"></a>Dodawanie podsieci bramy

Podsieć bramy zawiera zastrzeżone adresy IP używane przez usługi bramy sieci wirtualnej. Aby dodać podsieć bramy, należy użyć następujących przykładów:

Ustaw zmienną dla sieci wirtualnej.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
```

Utwórz podsieć bramy przy użyciu polecenia cmdlet [Add-AzVirtualNetworkSubnetConfig.](/powershell/module/az.network/Add-azVirtualNetworkSubnetConfig)

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
```

Ustaw konfigurację podsieci dla sieci wirtualnej za pomocą polecenia cmdlet [Set-AzVirtualNetwork.](/powershell/module/az.network/Set-azVirtualNetwork)

```azurepowershell-interactive
$vnet | Set-AzVirtualNetwork
```

## <a name="request-a-public-ip-address"></a><a name="PublicIP"></a>Przesłanie żądania dotyczącego publicznego adresu IP

Brama sieci VPN musi mieć dynamicznie przydzielony publiczny adres IP. Podczas tworzenia połączenia z bramą sieci VPN jest to adres IP określony przez użytkownika. Użyj następującego przykładu, aby zażądać publicznego adresu IP:

```azurepowershell-interactive
$gwpip= New-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="create-the-gateway-ip-address-configuration"></a><a name="GatewayIPConfig"></a>Tworzenie konfiguracji adresu IP bramy

W ramach konfiguracji bramy zostaje zdefiniowana podsieć i publiczny adres IP do użycia. Poniższy przykład umożliwia utworzenie własnej konfiguracji bramy:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```
## <a name="create-the-vpn-gateway"></a><a name="CreateGateway"></a>Tworzenie bramy sieci VPN

Tworzenie bramy sieci VPN może potrwać 45 minut lub dłużej. Po zakończeniu bramy można utworzyć połączenie między siecią wirtualną a inną siecią wirtualną. Możesz też utworzyć połączenie między siecią wirtualną a lokalizacją lokalną. Utwórz bramę sieci VPN za pomocą polecenia cmdlet [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway).

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>Wyświetlanie bramy sieci VPN

Bramę sieci VPN można wyświetlić za pomocą polecenia cmdlet [Get-AzVirtualNetworkGateway.](/powershell/module/az.network/Get-azVirtualNetworkGateway)

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -Name Vnet1GW -ResourceGroup TestRG1
```

Dane wyjściowe będą wyglądać podobnie do tego przykładu:

```
Name                   : VNet1GW
ResourceGroupName      : TestRG1
Location               : eastus
Id                     : /subscriptions/<subscription ID>/resourceGroups/TestRG1/provide
                         rs/Microsoft.Network/virtualNetworkGateways/VNet1GW
Etag                   : W/"0952d-9da8-4d7d-a8ed-28c8ca0413"
ResourceGuid           : dc6ce1de-2c4494-9d0b-20b03ac595
ProvisioningState      : Succeeded
Tags                   :
IpConfigurations       : [
                           {
                             "PrivateIpAllocationMethod": "Dynamic",
                             "Subnet": {
                               "Id": "/subscriptions/<subscription ID>/resourceGroups/Te
                         stRG1/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet"
                             },
                             "PublicIpAddress": {
                               "Id": "/subscriptions/<subscription ID>/resourceGroups/Te
                         stRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP"
                             },
                             "Name": "default",
                             "Etag": "W/\"0952d-9da8-4d7d-a8ed-28c8ca0413\"",
                             "Id": "/subscriptions/<subscription ID>/resourceGroups/Test
                         RG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/de
                         fault"
                           }
                         ]
GatewayType            : Vpn
VpnType                : RouteBased
EnableBgp              : False
ActiveActive           : False
GatewayDefaultSite     : null
Sku                    : {
                           "Capacity": 2,
                           "Name": "VpnGw1",
                           "Tier": "VpnGw1"
                         }
VpnClientConfiguration : null
BgpSettings            : {
     
```

## <a name="view-the-public-ip-address"></a><a name="viewgwpip"></a>Wyświetlanie publicznego adresu IP

Aby wyświetlić publiczny adres IP bramy sieci VPN, użyj polecenia cmdlet [Get-AzPublicIpAddress.](/powershell/module/az.network/Get-azPublicIpAddress)

```azurepowershell-interactive
Get-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1
```

W odpowiedzi przykładowej wartość IpAddress jest publicznym adresem IP.

```
Name                     : VNet1GWIP
ResourceGroupName        : TestRG1
Location                 : eastus
Id                       : /subscriptions/<subscription ID>/resourceGroups/TestRG1/provi
                           ders/Microsoft.Network/publicIPAddresses/VNet1GWIP
Etag                     : W/"5001666a-bc2a-484b-bcf5-ad488dabd8ca"
ResourceGuid             : 3c7c481e-9828-4dae-abdc-f95b383
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Dynamic
IpAddress                : 13.90.153.3
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                             "Id": "/subscriptions/<subscription ID>/resourceGroups/Test
                           RG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/
                           default"
                           }
DnsSettings              : null
Zones                    : {}
Sku                      : {
                             "Name": "Basic"
                           }
IpTags                   : {}
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy utworzone zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Spowoduje to usunięcie grupy zasobów i wszystkich znajdujących się w niej zasobów.

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
```

## <a name="next-steps"></a>Następne kroki

Po zakończeniu tworzenia bramy można utworzyć połączenie między siecią wirtualną a inną siecią wirtualną. Możesz też utworzyć połączenie między siecią wirtualną a lokalizacją lokalną.

> [!div class="nextstepaction"]
> [Tworzenie połączenia typu lokacja-lokacja](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Tworzenie połączenia typu punkt-lokacja](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Tworzenie połączenia z inną siecią wirtualną](vpn-gateway-vnet-vnet-rm-ps.md)
