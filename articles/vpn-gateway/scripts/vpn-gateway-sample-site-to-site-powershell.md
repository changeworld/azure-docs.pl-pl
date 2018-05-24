---
title: Przykładowy skrypt programu Azure PowerShell — konfigurowanie sieci VPN typu lokacja-lokacja | Microsoft Docs
description: Konfigurowanie sieci VPN typu lokacja-lokacja.
services: vpn-gateway
documentationcenter: vpn-gateway
author: cherylmc
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/30/2018
ms.author: anzaman
ms.openlocfilehash: 62381c08892017512dcf2bd5d9c7f8690bedfb6c
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
---
# <a name="create-a-vpn-gateway-and-add-a-site-to-site-connection-using-powershell"></a>Tworzenie usługi VPN Gateway i dodawanie połączenia lokacja-lokacja za pomocą programu PowerShell

Ten skrypt tworzy bramę sieci VPN opartą na trasie i dodaje konfigurację typu lokacja-lokacja. Aby utworzyć połączenie, należy również skonfigurować urządzenie sieci VPN. Aby uzyskać więcej informacji, zobacz [Informacje na temat urządzeń sieci VPN i parametrów protokołu IPsec/IKE dla połączeń bramy VPN typu lokacja-lokacja](../vpn-gateway-about-vpn-devices.md).


```azurepowershell-interactive
# Declare variables
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "10.0.0.0/16"
  $FESubPrefix = "10.1.0.0/24"
  $BESubPrefix = "10.1.1.0/24"
  $GWSubPrefix = "10.1.255.0/27"
  $VPNClientAddressPool = "192.168.0.0/24"
  $RG = "TestRG1"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWIP"
  $GWIPconfName = "gwipconf"
# Create a resource group
New-AzureRmResourceGroup -Name TestRG1 -Location EastUS
# Create a virtual network
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName TestRG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
# Create a subnet configuration
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork
# Set the subnet configuration for the virtual network
$virtualNetwork | Set-AzureRmVirtualNetwork
# Add a gateway subnet
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
# Set the subnet configuration for the virtual network
$vnet | Set-AzureRmVirtualNetwork
# Request a public IP address
$gwpip= New-AzureRmPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1 -Location 'East US' `
 -AllocationMethod Dynamic
# Create the gateway IP address configuration
$vnet = Get-AzureRmVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
# Create the VPN gateway
New-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
 -Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
 -VpnType RouteBased -GatewaySku VpnGw1
# Create the local network gateway
New-AzureRmLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
 -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
# Configure your on-premises VPN device
# Create the VPN connection
$gateway1 = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
$local = Get-AzureRmLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
New-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1 `
 -Location 'East US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
 -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy utworzone zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Spowoduje to usunięcie grupy zasobów i wszystkich znajdujących się w niej zasobów.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name TestRG1
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu utworzenia wdrożenia. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig) | Dodaje konfigurację podsieci. Ta konfiguracja jest używana podczas procesu tworzenia sieci wirtualnej. |
| [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork) | Pobiera szczegóły sieci wirtualnej. |
| [Get-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/get-azurermvirtualnetworkgateway) | Pobiera szczegóły bramy sieci wirtualnej. |
| [Get-AzureRmLocalNetworkGateway](/powershell/module/azurerm.network/get-azurermvirtualnetworkgateway) | Pobiera szczegóły bramy sieci lokalnej. |
| [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig) | Pobiera szczegóły konfiguracji podsieci sieci wirtualnej. |
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Tworzy konfigurację podsieci. Ta konfiguracja jest używana podczas procesu tworzenia sieci wirtualnej. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Tworzy sieć wirtualną. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Tworzy publiczny adres IP. |
| [New-AzureRmVirtualNetworkGatewayIpConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayipconfig) | Tworzy nową konfigurację adresu IP bramy. |
| [New-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.resources/new-azurermvirtualnetworkgateway) | Tworzy bramę sieci VPN. |
| [New-AzureRmLocalNetworkGateway](/powershell/module/azurerm.resources/new-azurermlocalnetworkgateway) | Tworzy bramę sieci lokalnej. |
| [New-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/ new-azurermvirtualnetworkgatewayconnection) | Tworzy połączenie lokacja-lokacja. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Usuwa grupę zasobów i wszystkie zasoby w niej zawarte. |
| [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) | Ustawia konfigurację podsieci dla sieci wirtualnej. |
| [Set-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway) | Ustawia konfigurację dla bramy sieci VPN. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).
