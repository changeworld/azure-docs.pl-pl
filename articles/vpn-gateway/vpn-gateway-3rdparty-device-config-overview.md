---
title: Partner konfiguracji urządzeń sieci VPN do łączenia się z bramami Azure VPN Gateway | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie konfiguracji urządzeń sieci VPN partnera do łączenia się z bramami Azure VPN Gateway.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: ''
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.openlocfilehash: 7d3a32b5f2b2742a36716bac9747f20c47c98858
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66150181"
---
# <a name="overview-of-partner-vpn-device-configurations"></a>Omówienie konfiguracji urządzeń sieci VPN partnera
Ten artykuł zawiera omówienie konfigurowania lokalnego urządzenia sieci VPN do łączenia się z bramami Azure VPN Gateway. A przykładowy sieci wirtualnej platformy Azure i konfigurowania bramy sieci VPN jest używana do pokazują, jak połączyć się z konfiguracji urządzenia sieci VPN różnych lokalnych przy użyciu tych samych parametrów.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="device-requirements"></a>Wymagania dotyczące urządzeń
Bramy sieci VPN platformy Azure na użytek standardowych mechanizmów protokołu IPsec/IKE sieci VPN typu lokacja lokacja (S2S) tuneli. Aby uzyskać listę parametrów protokołu IPsec/IKE i algorytmów kryptograficznych dla bram Azure VPN Gateway, zobacz [urządzenia sieci VPN o](vpn-gateway-about-vpn-devices.md). Można również określić dokładny algorytmów i sił klucza dla określonego połączenia zgodnie z opisem w [informacje o wymaganiach kryptograficznych](vpn-gateway-about-compliance-crypto.md).

## <a name ="singletunnel"></a>Jednego tunelu sieci VPN
Pierwsza konfiguracja w próbce składa się z jednego tunelu sieci VPN S2S między bramą sieci VPN platformy Azure i lokalnym urządzeniem sieci VPN. Opcjonalnie można skonfigurować [protokołu BGP (Border Gateway) przez tunel VPN](#bgp).

![Diagram przedstawiający jednego tunelu sieci VPN S2S](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Aby uzyskać instrukcje krok po kroku skonfigurować jednego tunelu sieci VPN, zobacz [Konfigurowanie połączenia typu lokacja lokacja](vpn-gateway-howto-site-to-site-resource-manager-portal.md). W poniższych sekcjach Określ parametry połączenia dla przykładowej konfiguracji i podaj skrypt programu PowerShell, aby pomóc Ci rozpocząć pracę.

### <a name="connection-parameters"></a>Parametry połączenia
Ta sekcja zawiera listę parametrów dla przykładów, które są opisane w poprzednich sekcjach.

| **Parametr**                | **Wartość**                    |
| ---                          | ---                          |
| Prefiksy adresów sieci wirtualnej        | 10.11.0.0/16<br>10.12.0.0/16 |
| Adres IP bramy sieci VPN platformy Azure         | Azure VPN Gateway IP         |
| Prefiksy adresów lokalnych | 10.51.0.0/16<br>10.52.0.0/16 |
| Adres IP urządzenia sieci VPN w środowisku lokalnym    | Adres IP urządzenia sieci VPN w środowisku lokalnym    |
| * Virtual network numer ASN protokołu BGP                | 65010                        |
| * Azure IP elementu równorzędnego protokołu BGP           | 10.12.255.30                 |
| * Numer ASN protokołu BGP w środowisku lokalnym         | 65050                        |
| * Adresu IP elementu równorzędnego protokołu BGP w środowisku lokalnym     | 10.52.255.254                |

\* Opcjonalny parametr dla protokołu BGP tylko.

### <a name="sample-powershell-script"></a>Przykładowy skrypt programu PowerShell
Ta sekcja zawiera przykładowy skrypt, aby rozpocząć pracę. Aby uzyskać szczegółowe instrukcje, zobacz [utworzyć połączenie sieci VPN S2S przy użyciu programu PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md).

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subscription_Name"
$RG1           = "TestRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$VNet1ASN      = 65010
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GWIPName1     = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection15  = "VNet1toSite5"
$LNGName5      = "Site5"
$LNGPrefix50   = "10.52.255.254/32"
$LNGPrefix51   = "10.51.0.0/16"
$LNGPrefix52   = "10.52.0.0/16"
$LNGIP5        = "Your_VPN_Device_IP"
$LNGASN5       = 65050
$BGPPeerIP5    = "10.52.255.254"

# Connect to your subscription and create a new resource group

Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name ="policybased"></a>(Opcjonalnie) Niestandardowe zasady protokołu IPsec/IKE za pomocą UsePolicyBasedTrafficSelectors
Jeśli Twoje urządzenia sieci VPN nie obsługują selektorów dowolna dowolna ruchu, takich jak konfiguracje trasach ani VTI, należy utworzyć niestandardowe zasady protokołu IPsec/IKE za pomocą [UsePolicyBasedTrafficSelectors](vpn-gateway-connect-multiple-policybased-rm-ps.md) opcji.

> [!IMPORTANT]
> Należy utworzyć zasady protokołu IPsec/IKE, aby umożliwić **UsePolicyBasedTrafficSelectors** opcji w połączeniu.


Przykładowy skrypt tworzy zasady protokołu IPsec/IKE za pomocą następujących algorytmów i parametrów:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: Algorytm SHA1 AES256, PFS24, sekund 7200 okres istnienia skojarzeń zabezpieczeń i 20,480,000 KB (20 GB)

Skrypt stosuje zasady protokołu IPsec/IKE oraz umożliwia **UsePolicyBasedTrafficSelectors** opcji w połączeniu.

```powershell
$ipsecpolicy5 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name ="bgp"></a>(Opcjonalnie) Użyj protokołu BGP dla połączenia sieci VPN S2S
Podczas tworzenia połączenia sieci VPN S2S, opcjonalnie możesz skorzystać z [protokołu BGP dla bramy sieci VPN](vpn-gateway-bgp-resource-manager-ps.md). Takie podejście ma dwie różnice:

* Prefiksy adresów lokalnych może być adresem jednego hosta. Adres IP elementu równorzędnego protokołu BGP w środowisku lokalnym jest określony w następujący sposób:

    ```powershell
    New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* Podczas tworzenia połączenia należy ustawić **- EnableBGP** możliwość $True:

    ```powershell
    New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać instrukcje krok po kroku konfigurowania bramy sieci VPN typu aktywne aktywne, zobacz [konfigurowanie bram sieci VPN typu aktywne aktywne dla połączeń sieci wirtualnej między sieciami wirtualnymi i między środowiskami lokalnymi](vpn-gateway-activeactive-rm-powershell.md).

