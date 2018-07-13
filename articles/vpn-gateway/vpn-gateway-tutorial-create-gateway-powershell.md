---
title: Tworzenie bramy sieci VPN na platformie Azure i zarządzanie nią przy użyciu programu PowerShell | Microsoft Docs
description: Samouczek — tworzenie bramy sieci VPN i zarządzanie nią przy użyciu modułu Azure PowerShell
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 05/14/2018
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: 0f10384e7e21d65b3a16869a10f8294b9643c74c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38705687"
---
# <a name="create-and-manage-vpn-gateway-with-the-azure-powershell-module"></a>Tworzenie bramy sieci VPN i zarządzanie nią przy użyciu modułu Azure PowerShell

Bramy sieci VPN na platformie Azure zapewniają łączność między lokalizacjami klienta a platformą Azure. Ten samouczek obejmuje podstawowe etapy wdrażania bramy sieci VPN, takie jak tworzenie bramy i zarządzanie nią. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie bramy sieci VPN
> * Zmiana rozmiaru bramy sieci VPN
> * Resetowanie bramy VPN Gateway

Poniższy diagram przedstawia sieć wirtualną oraz bramę sieci VPN utworzoną w ramach tego samouczka.

![Sieć wirtualna i brama sieci VPN](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)

### <a name="azure-cloud-shell-and-azure-powershell"></a>Usługa Azure Cloud Shell i program Azure PowerShell

[!INCLUDE [working with cloudshell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

Jeśli chcesz zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga modułu Azure PowerShell w wersji 5.3 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure. 

## <a name="common-network-parameter-values"></a>Częste wartości parametrów sieciowych

Zmień poniższe wartości na podstawie konfiguracji Twojego środowiska i sieci.

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$VNet1ASN    = 65010
$DNS1        = "8.8.8.8"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów na pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Najpierw należy utworzyć grupę zasobów. W poniższym przykładzie grupa zasobów o nazwie *TestRG1* została utworzona w regionie *East US*:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Brama sieci VPN na platformie Azure zapewnia łączność między lokalizacjami oraz funkcjonalność serwera VPN P2S w sieci wirtualnej. Dodaj bramę sieci VPN do istniejącej sieci wirtualnej lub utwórz nową sieć wirtualną i bramę. W tym przykładzie utworzono nową sieć wirtualną z trzema podsieciami: frontonu, zaplecza i bramy za pomocą poleceń [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) oraz [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork):

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubnet1 -AddressPrefix $GwPrefix1
$vnet   = New-AzureRmVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>Żądanie publicznego adresu IP dla bramy sieci VPN

Bramy sieci VPN na platformie Azure komunikują się z lokalnymi urządzeniami sieci VPN przez Internet, by przeprowadzić negocjacje IKE (Internet Key Exchange) oraz utworzyć tunele protokołu IPsec. Utwórz publiczny adres IP i przypisz go do bramy sieci VPN za pomocą poleceń [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) oraz [New-AzureRmVirtualNetworkGatewayIpConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayipconfig), jak pokazano w poniższym przykładzie:

> [!IMPORTANT]
> Obecnie bramy mogą korzystać tylko z dynamicznych publicznych adresów IP. Statyczne adresy IP nie są obsługiwane przez bramy sieci VPN na platformie Azure.

```azurepowershell-interactive
$gwpip    = New-AzureRmPublicIpAddress -Name $GwIP1 -ResourceGroupName $RG1 `
              -Location $Location1 -AllocationMethod Dynamic
$subnet   = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' `
              -VirtualNetwork $vnet
$gwipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 `
              -Subnet $subnet -PublicIpAddress $gwpip
```

## <a name="create-vpn-gateway"></a>Tworzenie bramy sieci VPN

Tworzenie bramy sieci VPN może potrwać 45 minut lub dłużej. Po zakończeniu tworzenia bramy możesz utworzyć połączenie między swoją siecią wirtualną a inną siecią wirtualną albo lokalizacją lokalną. Utwórz bramę sieci VPN za pomocą polecenia cmdlet [New-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/New-AzureRmVirtualNetworkGateway).

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

Wartości parametrów klucza:
* GatewayType: użyj **Vpn** w przypadku połączeń między lokacjami oraz między sieciami wirtualnymi
* VpnType: użyj **RouteBased**, by umożliwić interakcję z większą liczbą urządzeń VPN oraz korzystanie z większej liczby funkcji routingu
* GatewaySku: domyślna wartość to **VpnGw1**. Zmień ją na VpnGw2 lub VpnGw3, jeśli potrzebujesz wyższej przepływności albo większej liczby połączeń. Aby uzyskać więcej informacji, zobacz [Gateway SKUs](vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Jednostki SKU bramy).

Po zakończeniu tworzenia bramy możesz utworzyć połączenie między swoją siecią wirtualną a inną siecią wirtualną albo lokalizacją lokalną. Możesz również skonfigurować połączenie P2S komputera klienckiego ze swoją siecią lokalną.

## <a name="resize-vpn-gateway"></a>Zmiana rozmiaru bramy sieci VPN

Możesz zmienić jednostkę SKU po utworzeniu bramy sieci VPN. Różne jednostki SKU obsługują różne specyfikacje, takie jak przepływność, liczba połączeń itd. W poniższym przykładzie zmieniono rozmiar bramy z VpnGw1 na VpnGw2 za pomocą polecenia [Resize-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Resize-AzureRmVirtualNetworkGateway). Aby uzyskać więcej informacji, zobacz [Gateway SKUs](vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Jednostki SKU bramy).

```azurepowershell-interactive
$gw = Get-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzureRmVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

Zmiana rozmiaru bramy sieci VPN również trwa około 30–45 minut, jednak ta operacja **nie** przerywa ani nie usuwa istniejących połączeń i konfiguracji.

## <a name="reset-vpn-gateway"></a>Resetowanie bramy sieci VPN

W ramach rozwiązywania problemów możesz zresetować bramę sieci VPN na platformie Azure, by wymusić ponowne uruchomienie konfiguracji tuneli protokołu IPsec/IKE. Aby zresetować bramę, użyj polecenia [Reset-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Reset-AzureRmVirtualNetworkGateway).

```azurepowershell-interactive
$gw = Get-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

Aby uzyskać więcej informacji, zobacz temat [Resetowanie bramy VPN Gateway](vpn-gateway-resetgw-classic.md).

## <a name="get-the-gateway-public-ip-address"></a>Pobieranie publicznego adresu IP bramy

Jeśli znasz nazwę publicznego adresu IP, za pomocą polecenia [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/Reset-AzureRmPublicIpAddress) możesz wyświetlić publiczny adres IP przypisany do bramy.

```azurepowershell-interactive
$myGwIp = Get-AzureRmPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="delete-vpn-gateway"></a>Usuwanie bramy sieci VPN

Pełna konfiguracja łączności między lokalizacjami oraz między sieciami wirtualnymi wymaga wielu typów zasobów oprócz bramy sieci VPN. Zanim usuniesz bramę sieci VPN, usuń powiązane z nią połączenia. Po usunięciu bramy możesz usunąć publiczny adres (lub adresy) IP bramy. Szczegółowe instrukcje zostały podane w artykule [Usuwanie bramy VPN Gateway](vpn-gateway-delete-vnet-gateway-powershell.md).

Jeśli brama jest częścią wdrożenia prototypowego lub służącego do weryfikacji koncepcji, możesz usunąć grupę zasobów, bramę sieci VPN i wszystkie powiązane zasoby za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje dotyczące podstawowych zadań tworzenia bramy sieci VPN i zarządzania nią, takie jak:

> [!div class="checklist"]
> * Tworzenie bramy sieci VPN
> * Zmiana rozmiaru bramy sieci VPN
> * Resetowanie bramy VPN Gateway

Przejdź do następujących samouczków, aby dowiedzieć się więcej na temat połączeń S2S, między sieciami wirtualnymi i P2S.

> [!div class="nextstepaction"]
> * [Tworzenie połączeń S2S](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [Tworzenie połączeń między sieciami wirtualnymi](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Tworzenie połączeń P2S](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
