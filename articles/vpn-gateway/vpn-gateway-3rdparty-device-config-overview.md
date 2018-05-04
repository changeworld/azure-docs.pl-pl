---
title: Partnera konfiguracji urządzenia sieci VPN do połączenia bramy sieci VPN platformy Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie konfiguracji urządzenia sieci VPN partnera do połączenia bramy sieci VPN platformy Azure.
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
ms.openlocfilehash: dd9ca3937d688170798c42fce45dbcd7711773d1
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="overview-of-partner-vpn-device-configurations"></a>Przegląd konfiguracji urządzenia sieci VPN partnera
Ten artykuł zawiera omówienie konfigurowania lokalnego urządzenia sieci VPN do połączenia bramy sieci VPN platformy Azure. A przykładowe sieci wirtualnej platformy Azure i instalacji bramy sieci VPN jest używany jak nawiązać połączenia z konfiguracji urządzenia sieci VPN innej lokalnymi przy użyciu tych samych parametrach.

## <a name="device-requirements"></a>Wymagania dotyczące urządzeń
Bramy sieci VPN platformy Azure, użyj standardowych pakietów protokołu IPsec i IKE dla sieci VPN typu lokacja lokacja (S2S) tuneli. Aby uzyskać listę parametrów IPsec i IKE i algorytmów kryptograficznych dla bram sieci VPN platformy Azure, zobacz [urządzenia sieci VPN o](vpn-gateway-about-vpn-devices.md). Można również określić dokładną algorytmów i silnych kluczy dla określonego połączenia zgodnie z opisem w [o wymaganiach dotyczących kryptograficznych](vpn-gateway-about-compliance-crypto.md).

## <a name ="singletunnel"></a>Jeden tunel VPN
Pierwszy konfiguracji w próbce składa się z jednego tunelu S2S VPN między bramą sieci VPN platformy Azure i lokalnego urządzenia sieci VPN. Opcjonalnie można skonfigurować [protokołu BGP (Border Gateway) przez tunel VPN](#bgp).

![Diagram jeden tunel S2S VPN](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Aby uzyskać instrukcje skonfigurować jeden tunel VPN, zobacz [konfigurowania połączenia lokacja lokacja](vpn-gateway-howto-site-to-site-resource-manager-portal.md). W poniższych sekcjach Określ parametry połączenia dla Przykładowa konfiguracja i podaj skrypt programu PowerShell, aby pomóc Ci rozpocząć.

### <a name="connection-parameters"></a>Parametry połączenia
W tej sekcji opisano parametry przykłady, które zostały opisane w poprzednich sekcjach.

| **Parametr**                | **Wartość**                    |
| ---                          | ---                          |
| Prefiksy adresów sieci wirtualnej        | 10.11.0.0/16<br>10.12.0.0/16 |
| Adres IP bramy sieci VPN platformy Azure         | Brama sieci VPN platformy Azure IP         |
| Prefiksy adresów lokalnych | 10.51.0.0/16<br>10.52.0.0/16 |
| Lokalny adres IP urządzenia sieci VPN    | Lokalny adres IP urządzenia sieci VPN    |
| * Wirtualnej sieci BGP ASN                | 65010                        |
| * Azure IP elementu równorzędnego protokołu BGP           | 10.12.255.30                 |
| * Lokalnymi BGP ASN         | 65050                        |
| * IP elementu równorzędnego BGP lokalnej     | 10.52.255.254                |

\* Opcjonalny parametr dla protokołu BGP tylko.

### <a name="sample-powershell-script"></a>Przykładowy skrypt programu PowerShell
Ta sekcja zawiera przykładowy skrypt ułatwiających rozpoczęcie pracy. Aby uzyskać szczegółowe instrukcje, zobacz [utworzyć połączenie sieci VPN S2S przy użyciu programu PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md).

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subcription_Name"
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

Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name ="policybased"></a>(Opcjonalnie) Niestandardowe zasady IPsec i IKE za pomocą UsePolicyBasedTrafficSelectors
Jeśli urządzenia sieci VPN nie obsługują selektorów dowolny z każdym ruchu, takie jak konfiguracje oparte na trasach lub na podstawie VTI, Utwórz niestandardowe zasady IPsec i IKE z [UsePolicyBasedTrafficSelectors](vpn-gateway-connect-multiple-policybased-rm-ps.md) opcji.

> [!IMPORTANT]
> Należy utworzyć zasady IPsec i IKE, aby włączyć **UsePolicyBasedTrafficSelectors** opcję połączenia.


Przykładowy skrypt tworzy zasady IPsec i IKE o parametrach i następujących algorytmów:
* IKEv2: AES256, SHA384, DHGroup24
* Protokół IPsec: AES256, SHA1, PFS24, skojarzenia zabezpieczeń okres istnienia 7200 sekund i 20,480,000 KB (20 GB)

Skrypt stosuje zasady IPsec i IKE i umożliwia **UsePolicyBasedTrafficSelectors** opcję połączenia.

```powershell
$ipsecpolicy5 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name ="bgp"></a>(Opcjonalnie) Użyj protokołu BGP dla połączenia sieci VPN S2S
Podczas tworzenia połączenia sieci VPN S2S, można opcjonalnie użyć [BGP dla bramy sieci VPN](vpn-gateway-bgp-resource-manager-ps.md). Takie podejście charakteryzuje się dwoma różnice:

* Prefiksy adresów lokalnych może być adresem jednego hosta. Adres IP elementu równorzędnego protokołu BGP lokalnymi wygląda następująco:

    ```powershell
    New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* Podczas tworzenia połączenia należy ustawić **- EnableBGP** opcji $true:

    ```powershell
    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać instrukcje krok po kroku do skonfigurowania bramy sieci VPN aktywny / aktywny, zobacz [Konfigurowanie aktywny aktywny bram sieci VPN między lokalizacjami i połączeń między wirtualnymi do](vpn-gateway-activeactive-rm-powershell.md).

