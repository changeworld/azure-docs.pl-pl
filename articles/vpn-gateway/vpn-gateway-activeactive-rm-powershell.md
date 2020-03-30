---
title: Konfigurowanie aktywnych i aktywnych połączeń bramy sieci VPN usługi S2S
description: W tym artykule okonywanie połączeń aktywnych aktywnych z bramami sieci VPN platformy Azure przy użyciu usługi Azure Resource Manager i programu PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/24/2018
ms.author: yushwang
ms.reviewer: cherylmc
ms.openlocfilehash: ec3697208434eb971e47136416f2c2cc541b5cea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244643"
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Konfigurowanie aktywnych aktywnych połączeń sieci VPN s2s za pomocą bram sieci VPN platformy Azure

W tym artykule otwór o krokach tworzenia aktywnych i aktywnych połączeń międzylokalnościami i sieci wirtualnej do sieci wirtualnej przy użyciu modelu wdrażania Menedżera zasobów i programu PowerShell.



## <a name="about-highly-available-cross-premises-connections"></a>Informacje o wysoce dostępnych połączeniach międzylokajnych
Aby osiągnąć wysoką dostępność dla łączności między lokalizacjami i siecią wirtualną z siecią wirtualną, należy wdrożyć wiele bram sieci VPN i ustanowić wiele połączeń równoległych między sieciami a platformą Azure. Zobacz [wysoce dostępne między premises i łączności sieci wirtualnej do sieci wirtualnej, aby](vpn-gateway-highlyavailable.md) uzyskać omówienie opcji łączności i topologii.

Ten artykuł zawiera instrukcje konfigurowania aktywnego połączenia sieci VPN między lokalami oraz aktywne połączenie między dwiema sieciami wirtualnymi.

* [Część 1 — tworzenie i konfigurowanie bramy sieci VPN platformy Azure w trybie aktywnym i aktywnym](#aagateway)
* [Część 2 - Ustanawianie aktywnych i aktywnych połączeń międzylokacowych](#aacrossprem)
* [Część 3 - Ustanawianie aktywnych połączeń sieci wirtualnej z siecią wirtualną](#aav2v)

Jeśli masz już bramę sieci VPN, możesz:
* [Aktualizowanie istniejącej bramy sieci VPN z aktywnego stanu wstrzymania do aktywnej aktywności lub odwrotnie](#aaupdate)

Można je połączyć, aby utworzyć bardziej złożoną, wysoce dostępną topologię sieci, która spełnia Twoje potrzeby.

> [!IMPORTANT]
> Tryb aktywny-aktywny używa tylko następujących jednostek SKU: 
>   * VpnGw1, VpnGw2, VpnGw3
>   * HighPerformance (dla starych starszych jednostek SKU)

## <a name="part-1---create-and-configure-active-active-vpn-gateways"></a><a name ="aagateway"></a>Część 1 - Tworzenie i konfigurowanie aktywnych i aktywnych bram sieci VPN
Poniższe kroki skonfigurują bramę sieci VPN platformy Azure w trybach aktywnych i aktywnych. Kluczowe różnice między bramami aktywnymi i aktywnymi w trybie gotowości:

* Należy utworzyć dwie konfiguracje IP bramy z dwoma publicznymi adresami IP
* Należy ustawić flagę EnableActiveActiveFeature
* Jednostka SKU bramy musi być VpnGw1, VpnGw2, VpnGw3 lub HighPerformance (starsza jednostka SKU).

Inne właściwości są takie same jak bramy nieaktywne aktywne. 

### <a name="before-you-begin"></a>Przed rozpoczęciem
* Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
* Niezbędne jest zainstalowanie poleceń cmdlet programu PowerShell usługi Azure Resource Manager. Zobacz [Omówienie programu Azure PowerShell,](/powershell/azure/overview) aby uzyskać więcej informacji na temat instalowania poleceń cmdlet programu PowerShell.

### <a name="step-1---create-and-configure-vnet1"></a>Krok 1 - Tworzenie i konfigurowanie sieci wirtualnej1
#### <a name="1-declare-your-variables"></a>1. Zadeklaruj swoje zmienne
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

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Połącz się z subskrypcją i utwórz nową grupę zasobów
Upewnij się, że program PowerShell został przełączony do trybu umożliwiającego korzystanie z poleceń cmdlet usługi Resource Manager. Więcej informacji znajduje się w temacie [Using Windows PowerShell with Resource Manager](../powershell-azure-resource-manager.md) (Używanie programu Windows PowerShell z usługą Resource Manager).

Otwórz konsolę programu PowerShell i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Utwórz TestVNet1
Poniższy przykład pozwala utworzyć sieć wirtualną o nazwie TestVNet1 oraz trzy podsieci noszące kolejno nazwy GatewaySubnet, FrontEnd i BackEnd. Podczas zastępowania wartości ważne jest, aby podsieć bramy zawsze nosiła nazwę GatewaySubnet. W przypadku nadania jej innej nazwy proces tworzenia bramy zakończy się niepowodzeniem.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Krok 2 - Tworzenie bramy sieci VPN dla TestVNet1 w trybie aktywnym i aktywnym
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. Tworzenie publicznych adresów IP i konfiguracji adresów IP bramy
Zażądaj dwóch publicznych adresów IP, które mają być przydzielone do bramy, którą utworzysz dla sieci wirtualnej. Należy również zdefiniować wymagane konfiguracje podsieci i adresu IP.

```powershell
$gw1pip1 = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. Tworzenie bramy sieci VPN z konfiguracją aktywny-aktywny
Utwórz bramę sieci wirtualnej dla sieci TestVNet1. Należy zauważyć, że istnieją dwa wpisy GatewayIpConfig i EnableActiveActiveFeature flagi jest ustawiona. Tworzenie bramy może potrwać co najmniej 45 minut.

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. Uzyskiwanie publicznych adresów IP bramy i równorzędnego adresu IP BGP
Po utworzeniu bramy należy uzyskać adres IP elementu równorzędnego BGP w usłudze Azure VPN Gateway. Ten adres jest potrzebny do skonfigurowania bramy sieci VPN platformy Azure jako elementu równorzędnego BGP dla lokalnych urządzeń sieci VPN.

```powershell
$gw1pip1 = Get-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

Użyj następujących poleceń cmdlet, aby wyświetlić dwa publiczne adresy IP przydzielone dla bramy sieci VPN i odpowiadające im adresy IP elementu równorzędnego BGP dla każdego wystąpienia bramy:

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

Kolejność publicznych adresów IP dla wystąpień bramy i odpowiadających im adresów równorzędnych BGP są takie same. W tym przykładzie maszyna wirtualna bramy z publicznym adresem IP 40.112.190.5 użyje 10.12.255.4 jako adresu równorzędnego BGP, a brama z 138.91.156.129 użyje 10.12.255.5. Te informacje są potrzebne podczas konfigurowania lokalnych urządzeń sieci VPN łączących się z bramą aktywny-aktywny. Brama jest pokazana na poniższym diagramie ze wszystkimi adresami:

![brama aktywny-aktywny](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Po utworzeniu bramy można użyć tej bramy do ustanowienia aktywnej sieci międzylokacjowej lub połączenia sieci wirtualnej z siecią wirtualną. W poniższych sekcjach przedstawiono kroki, aby ukończyć ćwiczenie.

## <a name="part-2---establish-an-active-active-cross-premises-connection"></a><a name ="aacrossprem"></a>Część 2 - Ustanowienie aktywnego połączenia między lokalami
Aby ustanowić połączenie międzylokacje, należy utworzyć bramę sieci lokalnej do reprezentowania lokalnego urządzenia sieci VPN i połączenie, aby połączyć bramę sieci VPN platformy Azure z bramą sieci lokalnej. W tym przykładzie brama sieci VPN platformy Azure jest w trybie aktywnym i aktywnym. W rezultacie, mimo że istnieje tylko jedno lokalne urządzenie sieci VPN (brama sieci lokalnej) i jeden zasób połączenia, oba wystąpienia bramy sieci VPN platformy Azure ustanowią tunele sieci VPN S2S z urządzeniem lokalnym.

Przed kontynuowaniem upewnij się, że ukończyłeś [część 1](#aagateway) tego ćwiczenia.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Krok 1 - Tworzenie i konfigurowanie bramy sieci lokalnej
#### <a name="1-declare-your-variables"></a>1. Zadeklaruj swoje zmienne
To ćwiczenie będzie nadal tworzyć konfigurację pokazaną na diagramie. Należy pamiętać o zastąpieniu przykładowych wartości tymi, które mają zostać użyte w danej konfiguracji.

```powershell
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

Kilka rzeczy, na które należy zwrócić uwagę w odniesieniu do parametrów bramy sieci lokalnej:

* Brama sieci lokalnej może znajdować się w tej samej lub innej lokalizacji i grupie zasobów co brama sieci VPN. W tym przykładzie przedstawiono je w różnych grupach zasobów, ale w tej samej lokalizacji platformy Azure.
* Jeśli istnieje tylko jedno lokalne urządzenie sieci VPN, jak pokazano powyżej, aktywne połączenie może pracować z protokołem BGP lub bez niego. W tym przykładzie użyto protokołu BGP dla połączenia między lokalami.
* Jeśli protokół BGP jest włączony, prefiks, który należy zadeklarować dla bramy sieci lokalnej, jest adresem hosta adresu IP elementu równorzędnego BGP na urządzeniu sieci VPN. W tym przypadku jest to /32 prefiks "10.52.255.253/32".
* Przypominamy, że należy użyć różnych sieci ASN BGP między sieciami lokalnymi a siecią wirtualną platformy Azure. Jeśli są takie same, należy zmienić sieć wirtualną ASN, jeśli lokalne urządzenie sieci VPN już używa asn do obsługi równorzędnej z innymi sąsiadami protokołu BGP.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Tworzenie bramy sieci lokalnej dla lokacji5
Przed kontynuowaniem upewnij się, że nadal masz połączenie z subskrypcją 1. Utwórz grupę zasobów, jeśli nie została jeszcze utworzona.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5
New-AzLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Krok 2 - Podłącz bramę sieci wirtualnej i bramę sieci lokalnej
#### <a name="1-get-the-two-gateways"></a>1. Pobierz dwie bramy

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Utwórz połączenie TestVNet1 z witryną5
W tym kroku należy utworzyć połączenie z TestVNet1 do Site5_1 z "EnableBGP" ustawiona na $True.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. Parametry VPN i BGP dla lokalnego urządzenia VPN
W poniższym przykładzie wymieniono parametry wprowadzone w sekcji konfiguracji BGP na lokalnym urządzeniu sieci VPN dla tego ćwiczenia:

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

Połączenie powinno zostać nawiązane po kilku minutach, a sesja równorzędna BGP rozpocznie się po nawiązaniu połączenia IPsec. W tym przykładzie do tej pory skonfigurowano tylko jedno lokalne urządzenie sieci VPN, co powoduje, że diagram pokazano poniżej:

![aktywny-aktywny-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Krok 3 - Podłącz dwa lokalne urządzenia sieci VPN do aktywnej bramy sieci VPN
Jeśli masz dwa urządzenia sieci VPN w tej samej sieci lokalnej, możesz osiągnąć podwójną nadmiarowość, łącząc bramę sieci VPN platformy Azure z drugim urządzeniem sieci VPN.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. Tworzenie drugiej bramy sieci lokalnej dla lokacji5
Adres IP bramy, prefiks adresu i adres równorzędny protokołu BGP dla drugiej bramy sieci lokalnej nie mogą pokrywać się z poprzednią bramą sieci lokalnej dla tej samej sieci lokalnej.

```powershell
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```powershell
New-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Podłącz bramę sieci wirtualnej i drugą bramę sieci lokalnej
Utwórz połączenie z TestVNet1 do Site5_2 z "EnableBGP" ustawioną na $True

```powershell
$lng5gw2 = Get-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. Parametry VPN i BGP dla drugiego lokalnego urządzenia VPN
Podobnie, poniżej wymieniono parametry, które wprowadzisz w drugim urządzeniu VPN:

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

Po nawiązaniu połączenia (tunele) będą dostępne dwa nadmiarowe urządzenia sieci VPN i tunele łączące sieć lokalną i platformę Azure:

![podwójna redundancja-crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name="part-3---establish-an-active-active-vnet-to-vnet-connection"></a><a name ="aav2v"></a>Część 3 - Ustanawianie aktywnego połączenia sieci wirtualnej z siecią wirtualną
W tej sekcji utworzy się aktywne połączenie sieci wirtualnej z siecią wirtualną z usługą BGP. 

Poniższe instrukcje stanowią ciąg dalszy poprzedzających je kroków wymienionych powyżej. Aby utworzyć i skonfigurować testvnet1 i bramę sieci VPN z usługą BGP, należy ukończyć [część 1.](#aagateway) 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Krok 1 - Tworzenie TestVNet2 i bramy sieci VPN
Ważne jest, aby upewnić się, że przestrzeń adresowa IP nowej sieci wirtualnej, TestVNet2, nie pokrywa się z żadnym z zakresów sieci wirtualnej.

W tym przykładzie sieci wirtualne należą do tej samej subskrypcji. Można skonfigurować połączenia sieci wirtualnej z siecią wirtualną między różnymi subskrypcjami; aby dowiedzieć się więcej, zapoznaj się [z informacjami dotyczącymi konfigurowania połączenia sieci wirtualnej z siecią wirtualną.](vpn-gateway-vnet-vnet-rm-ps.md) Upewnij się, że podczas tworzenia połączeń dodano "-EnableBgp $True" w celu włączenia protokołu BGP.

#### <a name="1-declare-your-variables"></a>1. Zadeklaruj swoje zmienne
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

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Utwórz TestVNet2 w nowej grupie zasobów

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. Tworzenie aktywnej bramy sieci VPN dla TestVNet2
Zażądaj dwóch publicznych adresów IP, które mają być przydzielone do bramy, którą utworzysz dla sieci wirtualnej. Należy również zdefiniować wymagane konfiguracje podsieci i adresu IP.

```powershell
$gw2pip1 = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

Utwórz bramę sieci VPN z numerem AS i flagą "EnableActiveActiveFeature". Należy zauważyć, że należy zastąpić domyślną sieć ASN w swoich bramach sieci VPN platformy Azure. Sieci ASN dla połączonych sieci wirtualnych muszą być różne, aby włączyć BGP i routingu tranzytowego.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Krok 2 - Podłączanie bram TestVNet1 i TestVNet2
W tym przykładzie obie bramy są w tej samej subskrypcji. Ten krok można wykonać w tej samej sesji programu PowerShell.

#### <a name="1-get-both-gateways"></a>1. Pobierz obie bramy
Pamiętaj o zalogowaniu się i połączeniu z subskrypcją 1.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. Tworzenie obu połączeń
W tym kroku utworzysz połączenie z TestVNet1 do TestVNet2 i połączenie z TestVNet2 do TestVNet1.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Pamiętaj, aby włączyć BGP dla obu połączeń.
> 
> 

Po wykonaniu tych kroków połączenie zostanie nawiązane w ciągu kilku minut, a sesja równorzędna BGP będzie się po zakończeniu połączenia sieci wirtualnej z siecią wirtualną z podwójną nadmiarowością:

![active-active-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name="update-an-existing-vpn-gateway"></a><a name ="aaupdate"></a>Aktualizowanie istniejącej bramy sieci VPN

Ta sekcja ułatwia zmianę istniejącej bramy sieci VPN platformy Azure z trybu aktywnego i aktywnego w trybie aktywnym lub na odwrót.

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>Zmienianie bramy aktywnej w trybie gotowości na bramę aktywną i aktywną

Poniższy przykład konwertuje bramę aktywnego wstrzymania na bramę aktywny-aktywny. Po zmianie bramy aktywnej w trybie gotowości na aktywny-aktywny, należy utworzyć inny publiczny adres IP, a następnie dodać drugą konfigurację IP bramy.

#### <a name="1-declare-your-variables"></a>1. Zadeklaruj swoje zmienne

Zastąp następujące parametry używane w przykładach ustawieniami wymaganymi dla własnej konfiguracji, a następnie zadeklaruj te zmienne.

```powershell
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

Po zadeklarowaniu zmiennych można skopiować i wkleić ten przykład do konsoli programu PowerShell.

```powershell
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. Utwórz publiczny adres IP, a następnie dodaj drugą konfigurację IP bramy

```powershell
$gwpip2 = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. Włącz tryb aktywny-aktywny i zaktualizuj bramę

W tym kroku włączysz tryb aktywny-aktywny i zaktualizujesz bramę. W tym przykładzie brama sieci VPN korzysta obecnie ze starszej standardowej jednostki SKU. Jednak active-active nie obsługuje standardowej jednostki SKU. Aby zmienić rozmiar starszej jednostki SKU do tej, która jest obsługiwana (w tym przypadku HighPerformance), wystarczy określić obsługiwane starsze jednostki SKU, które mają być używane.

* Nie można zmienić starszej jednostki SKU na jedną z nowych jednostek SKU przy użyciu tego kroku. Rozmiar starszej jednostki SKU można zmienić tylko na inną obsługiwaną starszą jednostkę SKU. Na przykład nie można zmienić jednostki SKU ze standardowej na VpnGw1 (nawet jeśli VpnGw1 jest obsługiwany dla aktywnych aktywnych), ponieważ Standard jest starszą jednostką SKU, a VpnGw1 jest bieżącą jednostką SKU. Aby uzyskać więcej informacji na temat zmiany rozmiaru i migracji jednostek SKU, zobacz [Jednostki SKU bramy](vpn-gateway-about-vpngateways.md#gwsku).

* Jeśli chcesz zmienić rozmiar bieżącej jednostki SKU, na przykład VpnGw1 do VpnGw3, możesz to zrobić za pomocą tego kroku, ponieważ jednostki SKU znajdują się w tej samej rodzinie jednostek SKU. Aby to zrobić, należy użyć wartości:```-GatewaySku VpnGw3```

Gdy używasz tego w swoim środowisku, jeśli nie trzeba zmienić rozmiaru bramy, nie trzeba określić -GatewaySku. Należy zauważyć, że w tym kroku należy ustawić obiekt bramy w programie PowerShell, aby wyzwolić rzeczywistą aktualizację. Ta aktualizacja może potrwać od 30 do 45 minut, nawet jeśli nie zmiana rozmiaru bramy.

```powershell
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>Zmienianie bramy aktywnej i aktywnej na bramę aktywną w trybie gotowości
#### <a name="1-declare-your-variables"></a>1. Zadeklaruj swoje zmienne

Zastąp następujące parametry używane w przykładach ustawieniami wymaganymi dla własnej konfiguracji, a następnie zadeklaruj te zmienne.

```powershell
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

Po zadeklarowaniu zmiennych pobierz nazwę konfiguracji IP, którą chcesz usunąć.

```powershell
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. Usuń konfigurację IP bramy i wyłącz tryb aktywny-aktywny

W tym przykładzie można usunąć konfigurację ip bramy i wyłączyć tryb aktywny-aktywny. Należy zauważyć, że należy ustawić obiekt bramy w programie PowerShell, aby wyzwolić rzeczywistą aktualizację.

```powershell
Remove-AzVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

Ta aktualizacja może potrwać do 30 do 45 minut.

## <a name="next-steps"></a>Następne kroki
Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Kroki opisano w sekcji [Tworzenie maszyny wirtualnej](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
