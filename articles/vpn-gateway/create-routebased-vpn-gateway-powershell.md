---
title: 'Utwórz bramę sieci VPN platformy Azure opartej na trasach: programu PowerShell | Dokumentacja firmy Microsoft'
description: Szybko utworzyć bramę sieci VPN opartej na trasach przy użyciu programu PowerShell
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2018
ms.author: cherylmc
ms.openlocfilehash: 57da0d1f6878ce31f0e47680a4750a90115c93f6
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="create-a-route-based-vpn-gateway-using-powershell"></a>Utwórz bramę sieci VPN opartej na trasach przy użyciu programu PowerShell

Ten artykuł pomaga szybko utworzyć bramę sieci VPN platformy Azure opartej na trasach przy użyciu programu PowerShell. Brama sieci VPN jest używany podczas tworzenia połączenia sieci VPN do sieci lokalnej. Aby połączyć sieci wirtualnych umożliwia także bramy sieci VPN.

Kroki opisane w tym artykule spowoduje utworzenie sieci wirtualnej, podsieci, podsieć bramy i bramy sieci VPN opartej na trasach (bramy sieci wirtualnej). Po zakończeniu tworzenia bramy następnie można utworzyć połączenia. Kroki te wymagają subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga modułu Azure PowerShell w wersji 5.3.0 lub nowszej. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

```azurepowershell-interactive
New-AzureRmResourceGroup -Name TestRG1 -Location EastUS
```

## <a name="vnet"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną przy użyciu polecenia [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Poniższy przykład tworzy sieć wirtualną o nazwie **VNet1** w **EastUS** lokalizacji:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName TestRG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
```

Tworzenie konfiguracji podsieci przy użyciu [AzureRmVirtualNetworkSubnetConfig nowy](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) polecenia cmdlet.

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Ustaw konfigurację podsieci dla sieci wirtualnej za pomocą [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork) polecenia cmdlet.


```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="gwsubnet"></a>Dodaj podsieć bramy

Podsieć bramy zawiera zastrzeżone adresy IP, które używają usługi bramy sieci wirtualnej. Aby dodać podsieci bramy, należy użyć poniższych przykładach:

Ustaw zmienną sieci wirtualnej.

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
```

Tworzenie przy użyciu podsieci bramy [AzureRmVirtualNetworkSubnetConfig Dodaj](/powershell/module/azurerm.network/Add-AzureRmVirtualNetworkSubnetConfig) polecenia cmdlet.

```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
```

Ustaw konfigurację podsieci dla sieci wirtualnej za pomocą [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork) polecenia cmdlet.

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="PublicIP"></a>Żądaj publicznego adresu IP

Brama sieci VPN musi mieć dynamicznie przydzielonego publicznego adresu IP. Podczas tworzenia połączenia z bramą sieci VPN jest to adres IP, który określisz. Skorzystaj z następującego przykładu, aby zażądać publiczny adres IP:

```azurepowershell-interactive
$gwpip= New-AzureRmPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="GatewayIPConfig"></a>Tworzenie konfiguracji adresu IP bramy

W ramach konfiguracji bramy zostaje zdefiniowana podsieć i publiczny adres IP do użycia. Poniższy przykład umożliwia utworzenie własnej konfiguracji bramy:

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```
## <a name="CreateGateway"></a>Tworzenie bramy sieci VPN

Brama sieci VPN może zająć 45 minut lub dłużej, aby utworzyć. Po zakończeniu bramy można utworzyć połączenie między sieci wirtualnej i innej sieci wirtualnej. Lub Utwórz połączenie między sieci wirtualnej i lokalizacji lokalnej. Tworzenie bramy sieci VPN przy użyciu [AzureRmVirtualNetworkGateway nowy](/powershell/module/azurerm.network/New-AzureRmVirtualNetworkGateway) polecenia cmdlet.

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

## <a name="viewgw"></a>Widok bramy sieci VPN

Można wyświetlić przy użyciu bramy sieci VPN [Get-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Get-AzureRmVirtualNetworkGateway) polecenia cmdlet.

```azurepowershell-interactive
Get-AzureRmVirtualNetworkGateway -Name Vnet1GW -ResourceGroup TestRG1
```

Dane wyjściowe będą podobne w tym przykładzie:

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

## <a name="viewgwpip"></a>Widok publiczny adres IP

Aby wyświetlić publicznego adresu IP dla bramy sieci VPN, należy użyć [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/Get-AzureRmPublicIpAddress) polecenia cmdlet.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1
```

W odpowiedzi na przykład wartość adres IP jest publiczny adres IP.

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

Gdy nie są już potrzebne zasoby utworzone, użyj [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) polecenie, aby usunąć grupę zasobów. Spowoduje to usunięcie grupy zasobów i wszystkie zasoby, które zawiera.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name TestRG1
```

## <a name="next-steps"></a>Kolejne kroki

Po zakończeniu bramy podczas tworzenia, można utworzyć połączenie między sieci wirtualnej i innej sieci wirtualnej. Lub Utwórz połączenie między sieci wirtualnej i lokalizacji lokalnej.

> [!div class="nextstepaction"]
> [Utwórz połączenie lokacja lokacja](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Utwórz połączenie punkt lokacja](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Utwórz połączenie do innej sieci wirtualnej](vpn-gateway-vnet-vnet-rm-ps.md)