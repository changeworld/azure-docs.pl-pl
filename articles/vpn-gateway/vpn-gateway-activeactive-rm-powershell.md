---
title: 'Skonfigurować połączenia sieci VPN S2S aktywny aktywny dla bram sieci VPN: usługi Azure Resource Manager: programu PowerShell | Dokumentacja firmy Microsoft'
description: W tym artykule przedstawiono konfigurowanie aktywny aktywny połączeń przy użyciu bramy sieci VPN platformy Azure przy użyciu usługi Azure Resource Manager i programu PowerShell.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: yushwang
ms.openlocfilehash: c09abe97d34b7220d76481a403165f1b7e07fcaa
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31602114"
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Skonfigurować połączenia sieci VPN S2S aktywny aktywny z bramy sieci VPN platformy Azure

W tym artykule przedstawiono kroki, aby utworzyć między lokalizacjami aktywny aktywny i wirtualnymi do połączeń przy użyciu modelu wdrażania usługi Resource Manager i programu PowerShell.

## <a name="about-highly-available-cross-premises-connections"></a>O wysokiej dostępności między lokalizacjami połączeń
Do uzyskania wysokiej dostępności między lokalizacjami i łączności do wirtualnymi, należy wdrożyć wiele bram sieci VPN i ustanowienia wielu równoległych połączeń między sieciami i Azure. Zobacz [wysokiej dostępności między lokalizacjami i łączności do wirtualnymi](vpn-gateway-highlyavailable.md) Omówienie opcji łączności i topologii.

Ten artykuł zawiera instrukcje dotyczące konfiguracji połączenia sieci VPN między lokalizacjami aktywny aktywny oraz aktywny aktywny połączenie między dwiema sieciami wirtualnymi.

* [Część 1 — Tworzenie i konfigurowanie bramy sieci VPN platformy Azure w trybie aktywny / aktywny](#aagateway)
* [Część 2 - zestawiania połączeń między różnymi lokalizacjami aktywny aktywny](#aacrossprem)
* [Część 3 — ustanowienie połączenia do wirtualnymi aktywny aktywny](#aav2v)

Jeśli masz już bramy sieci VPN, można:
* [Zaktualizuj istniejącą bramę sieci VPN z aktywnego gotowości do aktywny aktywny lub na odwrót](#aaupdate)

Można łączyć ze sobą do tworzenia topologii sieci bardziej złożonych, wysokiej dostępności, która odpowiada Twoim potrzebom.

> [!IMPORTANT]
> Tryb aktywny aktywny używa tylko następujące wersje produktu: 
  * VpnGw1, VpnGw2, VpnGw3
  * Wysokowydajnej (dla starego starszej wersji jednostki SKU)
> 
> 

## <a name ="aagateway"></a>Część 1 — Tworzenie i konfigurowanie aktywny aktywny bramy sieci VPN
Poniższe kroki skonfiguruje bramy sieci VPN platformy Azure w trybach aktywny aktywny. Podstawowe różnice między bramami aktywny aktywny i aktywnego gotowości:

* Należy utworzyć dwie konfiguracje IP bramy z dwóch publicznych adresów IP
* Należy ustawić flagę EnableActiveActiveFeature
* Jednostka SKU bramy musi być VpnGw1, VpnGw2, VpnGw3 lub wysokowydajnej (starszej wersji jednostki SKU).

Inne właściwości są takie same jak bramy z systemem innym niż — aktywny aktywny. 

### <a name="before-you-begin"></a>Przed rozpoczęciem
* Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
* Niezbędne jest zainstalowanie poleceń cmdlet programu PowerShell usługi Azure Resource Manager. Zobacz [Omówienie programu Azure PowerShell](/powershell/azure/overview) Aby uzyskać więcej informacji na temat instalacji poleceń cmdlet programu PowerShell.

### <a name="step-1---create-and-configure-vnet1"></a>Krok 1 — Tworzenie i konfigurowanie VNet1
#### <a name="1-declare-your-variables"></a>1. Zadeklarowanie zmiennych
To ćwiczenie rozpoczniemy od zadeklarowania zmiennych. W poniższym przykładzie deklarujemy zmienne przy użyciu wartości podanych dla tego ćwiczenia. Podczas konfigurowania produktu należy pamiętać o zastąpieniu ich odpowiednimi wartościami. Tych zmiennych można użyć, aby wykonać opisane kroki w celu zapoznania się z tego typu konfiguracją. Zmodyfikuj zmienne, a następnie skopiuj je i wklej do konsoli programu PowerShell.

```powershell
$Sub1 = "Ross"
$RG1 = "TestAARG1"
$Location1 = "West US"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$VNet1ASN = 65010
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPName2 = "VNet1GWIP2"
$GW1IPconf1 = "gw1ipconf1"
$GW1IPconf2 = "gw1ipconf2"
$Connection12 = "VNet1toVNet2"
$Connection151 = "VNet1toSite5_1"
$Connection152 = "VNet1toSite5_2"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Połączyć subskrypcję i Utwórz nową grupę zasobów
Upewnij się, że program PowerShell został przełączony do trybu umożliwiającego korzystanie z poleceń cmdlet usługi Resource Manager. Więcej informacji znajduje się w temacie [Using Windows PowerShell with Resource Manager](../powershell-azure-resource-manager.md) (Używanie programu Windows PowerShell z usługą Resource Manager).

Otwórz konsolę programu PowerShell i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Utworzenie sieci TestVNet1
Poniższy przykład pozwala utworzyć sieć wirtualną o nazwie TestVNet1 oraz trzy podsieci noszące kolejno nazwy GatewaySubnet, FrontEnd i BackEnd. Podczas zastępowania wartości ważne jest, aby podsieć bramy zawsze nosiła nazwę GatewaySubnet. W przypadku nadania jej innej nazwy proces tworzenia bramy zakończy się niepowodzeniem.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Krok 2 — Tworzenie bramy sieci VPN dla TestVNet1 w trybie aktywny / aktywny
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. Tworzenie publicznych adresów IP i bramy konfiguracje adresów IP
Żądanie dwie publiczne adresy IP do przydzielenia do bramy, który spowoduje utworzenie sieci wirtualnej. Zostanie również definiować podsieci i wymagane konfiguracje adresów IP.

```powershell
$gw1pip1 = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. Tworzenie bramy sieci VPN z konfiguracją aktywny aktywny
Utwórz bramę sieci wirtualnej dla sieci TestVNet1. Zauważ, że istnieją dwa wpisy GatewayIpConfig, i Flaga EnableActiveActiveFeature jest ustawiona. Tworzenie bramy może potrwać co najmniej 45 minut.

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. Uzyskaj bramy publicznych adresów IP i adres IP elementu równorzędnego protokołu BGP
Po utworzeniu bramy, należy uzyskać adres IP elementu równorzędnego protokołu BGP dla bramy sieci VPN platformy Azure. Ten adres jest potrzebne do skonfigurowania bramy sieci VPN platformy Azure jako element równorzędny BGP dla lokalnego urządzenia sieci VPN.

```powershell
$gw1pip1 = Get-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

Pokaż dwie publiczne adresy IP przydzielone dla bramy sieci VPN, a ich odpowiednie adresy IP elementu równorzędnego protokołu BGP dla każdego wystąpienia bramy przy użyciu następujących poleceń cmdlet:

```powershell
PS D:\> $gw1pip1.IpAddress
40.112.190.5

PS D:\> $gw1pip2.IpAddress
138.91.156.129

PS D:\> $vnet1gw.BgpSettingsText
{
  "Asn": 65010,
  "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
  "PeerWeight": 0
}
```

Kolejność publicznego adresu IP adresów dla wystąpień bramy i odpowiednie adresy komunikacji równorzędnej BGP są takie same. W tym przykładzie bramy maszyny Wirtualnej z publicznego adresu IP z 40.112.190.5 użyje 10.12.255.4 jako adres komunikacji równorzędnej BGP i bramy z 138.91.156.129 użyje 10.12.255.5. Te informacje są potrzebne podczas konfigurowania na urządzeniach w sieci VPN lokalne nawiązywania połączenia z bramą aktywny aktywny. Brama jest wyświetlane na diagramie poniżej przy użyciu wszystkich adresów:

![aktywny aktywny bramy](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Po utworzeniu bramy można użyć ta brama między lokalizacjami aktywny aktywny lub wirtualnymi do połączenia. Poniższe sekcje przeprowadzenie kroki, aby ukończyć wykonywania.

## <a name ="aacrossprem"></a>Część 2 - nawiązywania połączenia między lokalizacjami aktywny aktywny
Nawiązanie połączenia między różnymi lokalizacjami, musisz utworzyć bramy sieci lokalnej do reprezentowania lokalnego urządzenia sieci VPN i połączenia bramy sieci VPN platformy Azure z bramy sieci lokalnej. W tym przykładzie Brama sieci VPN platformy Azure jest w trybie aktywny / aktywny. W związku z tym nawet, jeśli istnieje tylko jeden lokalnego urządzenia sieci VPN (bramy sieci lokalnej) i jednego połączenia zasobów, zarówno wystąpień bramy sieci VPN platformy Azure zostanie ustanowiona tunel S2S VPN z lokalnego urządzenia.

Przed kontynuowaniem upewnij się, zostały ukończone [część 1](#aagateway) tego ćwiczenia.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Krok 1 — Tworzenie i konfigurowanie bramy sieci lokalnej
#### <a name="1-declare-your-variables"></a>1. Zadeklarowanie zmiennych
Tego ćwiczenia jest kontynuowana konfiguracji przedstawione na diagramie. Należy pamiętać o zastąpieniu przykładowych wartości tymi, które mają zostać użyte w danej konfiguracji.

```powershell
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

Kilka rzeczy do uwzględnienia dotyczącymi parametrów bramy sieci lokalnej:

* Brama sieci lokalnej może być w tej samej lub innej lokalizacji i grupy zasobów jako brama sieci VPN. Ten przykład przedstawia je w różnych grupach zasobów, ale w tej samej lokalizacji platformy Azure.
* Jeśli istnieje tylko jeden lokalnego urządzenia sieci VPN zgodnie z powyższym, połączenia aktywny aktywny pracować z lub bez protokołu BGP. W tym przykładzie użyto protokołu BGP dla połączenia między lokalizacjami.
* Jeśli protokół BGP jest włączony, prefiksu, musisz zadeklarować dla bramy sieci lokalnej jest adres hosta, adresu IP elementu równorzędnego protokołu BGP na urządzeniu sieci VPN. W takim przypadku jest /32 prefiksu "10.52.255.253/32".
* Przypomnienia należy użyć innego protokołu BGP numerów ASN między lokalnymi sieciami i sieci wirtualnej platformy Azure. Jeśli są one takie same, musisz zmienić numer ASN Twojej sieci wirtualnej, jeśli lokalnego urządzenia sieci VPN używa już właściwość ASN do elementu równorzędnego z innych sąsiadów BGP.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Tworzenie bramy sieci lokalnej dla Site5
Przed kontynuowaniem upewnij się, że nadal masz połączenie z subskrypcją 1. Utwórz grupę zasobów, jeśli nie został jeszcze utworzony.

```powershell
New-AzureRmResourceGroup -Name $RG5 -Location $Location5
New-AzureRmLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Krok 2 — połączenie bramy sieci wirtualnej i Brama sieci lokalnej
#### <a name="1-get-the-two-gateways"></a>1. Pobierz dwóch bram

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzureRmLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Utwórz TestVNet1 Site5 połączenia
W tym kroku należy utworzyć połączenie z TestVNet1 do Site5_1 z "Wartość", wartość $True.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. Parametry sieci VPN i protokołu BGP dla lokalnego urządzenia sieci VPN
W poniższym przykładzie przedstawiono parametry, które wejdzie w sekcji konfiguracji protokołu BGP na urządzeniu lokalnym sieci VPN w tym ćwiczeniu:

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.253
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Można nawiązać połączenia, po upływie kilku minut, a sesja komunikacji równorzędnej BGP rozpocznie się po nawiązaniu połączenia protokołu IPsec. W tym przykładzie został skonfigurowany do tej pory tylko jeden lokalnego urządzenia sieci VPN, co na diagramie pokazano poniżej:

![active-active-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Krok 3 — Połącz dwa lokalnego urządzenia sieci VPN dla bramy sieci VPN aktywny aktywny
Mając dwa urządzenia sieci VPN w tej samej sieci lokalnej, można osiągnąć dwie nadmiarowość, nawiązując połączenie bramy sieci VPN platformy Azure, do drugiego urządzenia sieci VPN.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. Utwórz drugi bramę sieci lokalnej dla Site5
Adres IP bramy, prefiks adresu i adres komunikacji równorzędnej BGP dla drugiego bramy sieci lokalnej musi nie nakładają się na poprzedniej bramy sieci lokalnej w tej samej sieci lokalnej.

```powershell
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```powershell
New-AzureRmLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Połączenie bramy sieci wirtualnej, a druga Brama sieci lokalnej
Utwórz połączenie z TestVNet1 do Site5_2 z "Wartość", wartość $True

```powershell
$lng5gw2 = Get-AzureRmLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. Parametry sieci VPN i protokołu BGP dla drugiego lokalnego urządzenia sieci VPN
Podobnie poniżej listy parametrów wprowadzany do drugiego urządzenia sieci VPN:

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Gdy jest nawiązane połączenie (tunele), konieczne będzie podwójne nadmiarowe urządzenia sieci VPN i tunele, połączenie sieci lokalnej i Azure:

![podwójny nadmiarowość crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name ="aav2v"></a>Część 3 — ustanowienie połączenia do wirtualnymi aktywny aktywny
W tej sekcji tworzy aktywny aktywny wirtualnymi do połączenia z protokołem BGP. 

Poniższe instrukcje stanowią ciąg dalszy poprzedzających je kroków wymienionych powyżej. Należy wykonać [część 1](#aagateway) do tworzenia i konfigurowania TestVNet1 i bramy sieci VPN z protokołem BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Krok 1 — Tworzenie TestVNet2 i bramy sieci VPN
Należy się upewnić, że przestrzeń adresową nową sieć wirtualną TestVNet2, nie mogą się pokrywać ze wszystkimi zakresy sieci wirtualnej.

W tym przykładzie sieci wirtualne należą do tej samej subskrypcji. Można skonfigurować do wirtualnymi połączeń między różnych subskrypcji; Zapoznaj się z [skonfigurować połączenia do wirtualnymi](vpn-gateway-vnet-vnet-rm-ps.md) Aby dowiedzieć się więcej szczegółów. Upewnij się, że możesz dodać "-wartość $True" podczas tworzenia połączenia, aby włączyć protokół BGP.

#### <a name="1-declare-your-variables"></a>1. Zadeklarowanie zmiennych
Należy pamiętać o zastąpieniu przykładowych wartości tymi, które mają zostać użyte w danej konfiguracji.

```powershell
$RG2 = "TestAARG2"
$Location2 = "East US"
$VNetName2 = "TestVNet2"
$FESubName2 = "FrontEnd"
$BESubName2 = "Backend"
$GWSubName2 = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$VNet2ASN = 65020
$DNS2 = "8.8.8.8"
$GWName2 = "VNet2GW"
$GW2IPName1 = "VNet2GWIP1"
$GW2IPconf1 = "gw2ipconf1"
$GW2IPName2 = "VNet2GWIP2"
$GW2IPconf2 = "gw2ipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Utwórz TestVNet2 w nowej grupy zasobów

```powershell
New-AzureRmResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. Tworzenie bramy sieci VPN aktywny aktywny dla TestVNet2
Żądanie dwie publiczne adresy IP do przydzielenia do bramy, który spowoduje utworzenie sieci wirtualnej. Zostanie również definiować podsieci i wymagane konfiguracje adresów IP.

```powershell
$gw2pip1 = New-AzureRmPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzureRmPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

Utwórz bramę sieci VPN z liczbą AS a flagi "EnableActiveActiveFeature". Należy pamiętać, że na Twojej bramy sieci VPN platformy Azure, należy zastąpić domyślny numer ASN. Numery ASN dla połączonych sieci wirtualnych musi być inna, aby włączyć protokół BGP i routing tranzytowy.

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Krok 2 — Połącz bram TestVNet1 i TestVNet2
W tym przykładzie zarówno bramy znajdują się w tej samej subskrypcji. Ten krok można wykonać w tej samej sesji programu PowerShell.

#### <a name="1-get-both-gateways"></a>1. Pobierz obu bram
Pamiętaj o zalogowaniu się i połączeniu z subskrypcją 1.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. Utwórz zarówno połączenia
W tym kroku utworzysz połączenie z TestVNet1 TestVNet2 i połączenie z TestVNet2 do TestVNet1.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Pamiętaj włączyć protokół BGP dla obu połączeń.
> 
> 

Po wykonaniu tych kroków, połączenie zostanie nawiązane w kilka minut, a Protokół BGP sesji połączenia równorzędnego będzie się po zakończeniu połączenia do wirtualnymi z nadmiarowością podwójne:

![active-active-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>Zaktualizuj istniejącą bramę sieci VPN

Ta sekcja pomoże Ci Zmień istniejącą bramę sieci VPN platformy Azure ze stanu wstrzymania aktywny na aktywny aktywny tryb lub na odwrót.

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>Zmień bramy aktywnego gotowości do bramy aktywny aktywny

Poniższy przykład konwertuje bramy aktywnego gotowości do bramy aktywny aktywny. Po zmianie aktywnego gotowości uzyskanie aktywny aktywny, można utworzyć innym publicznym adresem IP, a następnie dodać drugi konfigurację IP bramy.

#### <a name="1-declare-your-variables"></a>1. Zadeklarowanie zmiennych

Zastąp następujące parametry używany w przykładach przy użyciu ustawień, które wymagają konfiguracji własnych następnie Zadeklaruj te zmienne.

```powershell
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

Po deklarowania zmiennych, można skopiować i wkleić w tym przykładzie do konsoli programu PowerShell.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. Utwórz publiczny adres IP, a następnie dodać drugi konfigurację IP bramy

```powershell
$gwpip2 = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzureRmVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. Włącz tryb aktywny aktywny i zaktualizować bramę

W tym kroku Włącz tryb aktywny aktywny i zaktualizować bramę. W tym przykładzie bramy sieci VPN jest aktualnie używa starszej wersji Standard. Jednak aktywny aktywny nie obsługuje wersji Standard. Aby zmienić rozmiar starszej wersji jednostki SKU na taki, który jest obsługiwany (w tym przypadku wysokowydajnej), po prostu określić obsługiwanych starszych wersji produktu, którego chcesz używać.

* Nie można zmienić starszej wersji jednostki SKU na jedną z nowych wersji produktu za pomocą tego kroku. Można zmienić tylko starszej wersji jednostki SKU do innego obsługiwanych starszej wersji jednostki SKU. Na przykład nie można zmienić jednostki SKU zgodne ze standardem do VpnGw1 (nawet jeśli VpnGw1 jest obsługiwana w przypadku aktywny aktywny) ponieważ Standard jest starszej wersji jednostki SKU i VpnGw1 bieżącej wersji produktu. Aby uzyskać więcej informacji na temat zmiany rozmiaru i Migrowanie jednostki SKU, zobacz [jednostki SKU bramy](vpn-gateway-about-vpngateways.md#gwsku).

* Jeśli chcesz zmienić rozmiar bieżącego SKU, na przykład VpnGw1 do VpnGw3, możesz to zrobić przy użyciu tego kroku, ponieważ jednostki SKU znajdują się w tej samej rodziny SKU. Aby to zrobić, należy użyć wartości: ```-GatewaySku VpnGw3```

Gdy używana jest to w danym środowisku, jeśli nie trzeba zmieniać rozmiar bramy, nie trzeba określać GatewaySku —. Zwróć uwagę, że w tym kroku należy ustawić obiektu bramy w programie PowerShell, aby wyzwolić rzeczywistej aktualizacji. Ta aktualizacja może potrwać 30 do 45 minut, nawet jeśli nie są zmiany rozmiaru bramy.

```powershell
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>Zmień bramy aktywny aktywny do aktywnego gotowości bramy
#### <a name="1-declare-your-variables"></a>1. Zadeklarowanie zmiennych

Zastąp następujące parametry używany w przykładach przy użyciu ustawień, które wymagają konfiguracji własnych następnie Zadeklaruj te zmienne.

```powershell
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

Po deklarowania zmiennych, uzyskać nazwy konfiguracji adresu IP, który chcesz usunąć.

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. Usuń konfigurację IP bramy i Wyłącz tryb aktywny aktywny

W tym przykładzie umożliwiają Usuń konfigurację IP bramy i Wyłącz tryb aktywny aktywny. Zwróć uwagę, że należy ustawić obiektu bramy w programie PowerShell, aby wyzwolić rzeczywistej aktualizacji.

```powershell
Remove-AzureRmVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

Ta aktualizacja może potrwać do 30 do 45 minut.

## <a name="next-steps"></a>Kolejne kroki
Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Kroki opisano w sekcji [Tworzenie maszyny wirtualnej](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
