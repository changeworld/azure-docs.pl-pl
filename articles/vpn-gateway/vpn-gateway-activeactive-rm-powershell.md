---
title: 'Konfigurowanie połączeń sieci VPN S2S aktywne aktywne dla bram sieci VPN: Azure Resource Manager: Program PowerShell | Dokumentacja firmy Microsoft'
description: W tym artykule przedstawiono konfigurowanie połączeń typu aktywne aktywne przy użyciu bram Azure VPN Gateway przy użyciu usługi Azure Resource Manager i programu PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/24/2018
ms.author: yushwang, cherylmc
ms.openlocfilehash: 7ba4fb32ddfb8b3eb88d2dbfce265b070d521414
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66119407"
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Konfigurowanie połączenia sieci VPN S2S aktywne aktywne przy użyciu bram Azure VPN Gateway

W tym artykule przedstawiono kroki umożliwiające utworzenie aktywne aktywne, między środowiskami lokalnymi i połączenia sieć wirtualna-sieć wirtualna za pomocą modelu wdrażania usługi Resource Manager i programu PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about-highly-available-cross-premises-connections"></a>Informacje o połączeniach o wysokiej dostępności między środowiskami lokalnymi
Uzyskanie wysokiej dostępności obejmujących wiele lokalizacji i połączenia sieć wirtualna-sieć wirtualna, należy wdrożyć wiele bram sieci VPN i utworzyć wiele równoległych połączeń między sieciami i platformy Azure. Zobacz [o wysokiej dostępności obejmujące i połączenia sieć wirtualna-sieć wirtualna](vpn-gateway-highlyavailable.md) omówienie topologii i opcje łączności.

Ten artykuł zawiera instrukcje konfigurowania połączenia sieci VPN typu aktywne aktywne między środowiskami lokalnymi i aktywne aktywne połączenie między dwiema sieciami wirtualnymi.

* [Część 1 — Tworzenie i konfigurowanie bramy sieci VPN platformy Azure w tryb aktywny aktywny](#aagateway)
* [Część 2 — nawiązywać połączenia z lokalizacjami aktywny aktywny](#aacrossprem)
* [Część 3 — nawiązywać połączenia z sieciami wirtualnymi aktywny aktywny](#aav2v)

Jeśli masz już bramę sieci VPN, możesz to zrobić:
* [Aktualizowanie istniejącej bramy sieci VPN z aktywne-w gotowości do aktywny aktywny lub na odwrót](#aaupdate)

Można łączyć je ze sobą, aby tworzyć topologii bardziej złożonych sieci o wysokiej dostępności, który odpowiada Twoim potrzebom.

> [!IMPORTANT]
> Tryb aktywny aktywny używa tylko następujących jednostek SKU: 
>   * VpnGw1, VpnGw2, VpnGw3
>   * HighPerformance (dla starego starszych jednostek SKU)

## <a name ="aagateway"></a>Część 1 — Tworzenie i konfigurowanie bramy sieci VPN typu aktywne aktywne
Poniższe kroki skonfiguruje bramy sieci VPN platformy Azure w trybie aktywny aktywny. Podstawowe różnice między bramami aktywne aktywne i aktywne-w gotowości:

* Musisz utworzyć dwie konfiguracje protokołu IP bramy przy użyciu dwóch publicznych adresów IP
* Należy ustawić flagę EnableActiveActiveFeature
* Jednostka SKU bramy musi być VpnGw1, VpnGw2, VpnGw3 lub HighPerformance (starszej wersji jednostki SKU).

Inne właściwości są takie same jak bram innych — aktywny aktywny. 

### <a name="before-you-begin"></a>Przed rozpoczęciem
* Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
* Niezbędne jest zainstalowanie poleceń cmdlet programu PowerShell usługi Azure Resource Manager. Zobacz [Omówienie programu Azure PowerShell](/powershell/azure/overview) Aby uzyskać więcej informacji na temat instalowania poleceń cmdlet programu PowerShell.

### <a name="step-1---create-and-configure-vnet1"></a>Krok 1 — Tworzenie i konfigurowanie sieci VNet1
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

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Połączyć subskrypcję i utworzyć nową grupę zasobów
Upewnij się, że program PowerShell został przełączony do trybu umożliwiającego korzystanie z poleceń cmdlet usługi Resource Manager. Więcej informacji znajduje się w temacie [Using Windows PowerShell with Resource Manager](../powershell-azure-resource-manager.md) (Używanie programu Windows PowerShell z usługą Resource Manager).

Otwórz konsolę programu PowerShell i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Utworzenie sieci TestVNet1
Poniższy przykład pozwala utworzyć sieć wirtualną o nazwie TestVNet1 oraz trzy podsieci noszące kolejno nazwy GatewaySubnet, FrontEnd i BackEnd. Podczas zastępowania wartości ważne jest, aby podsieć bramy zawsze nosiła nazwę GatewaySubnet. W przypadku nadania jej innej nazwy proces tworzenia bramy zakończy się niepowodzeniem.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Krok 2 — Tworzenie bramy sieci VPN dla sieci TestVNet1 w trybie aktywny aktywny
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. Utwórz publiczne adresy IP i bramę konfiguracje adresów IP
Żądanie dwa publiczne adresy IP do przydzielenia do bramy, która zostanie utworzona dla sieci wirtualnej. Zostanie również definiować podsieci i konfiguracje adresów IP wymagana.

```powershell
$gw1pip1 = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. Tworzenie bramy sieci VPN w konfiguracji aktywny aktywny
Utwórz bramę sieci wirtualnej dla sieci TestVNet1. Pamiętaj, że istnieją dwa wpisy GatewayIpConfig EnableActiveActiveFeature flaga jest ustawiona. Tworzenie bramy może potrwać co najmniej 45 minut.

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. Uzyskanie publicznych adresów IP bramy i adres IP elementu równorzędnego protokołu BGP
Po utworzeniu bramy, należy uzyskać adres IP elementu równorzędnego protokołu BGP bramy sieci VPN platformy Azure. Ten adres jest wymagane do skonfigurowania bramy Azure VPN Gateway, jako element równorzędny BGP dla lokalnych urządzeń sieci VPN.

```powershell
$gw1pip1 = Get-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

Aby wyświetlić dwa publiczne adresy IP przydzielone dla bramy sieci VPN i ich odpowiednie adresy IP elementu równorzędnego protokołu BGP dla poszczególnych wystąpień bramy, należy użyć następujących poleceń cmdlet:

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

Kolejność publiczny adres IP adresów dla wystąpienia bramy i odpowiednie adresy komunikacji równorzędnej protokołu BGP są takie same. W tym przykładzie brama maszyna wirtualna z publicznym adresem IP 40.112.190.5 użyje 10.12.255.4 jako jego adres komunikacji równorzędnej BGP i bramy z 138.91.156.129 użyje 10.12.255.5. Te informacje są potrzebne podczas konfigurowania w lokalnych urządzeń sieci VPN nawiązywania połączenia z bramą aktywny aktywny. Brama jest wyświetlane na diagramie poniżej przy użyciu wszystkich adresów:

![aktywne aktywne bramy](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Po utworzeniu bramy można użyć tej bramy można ustanowić między środowiskami lokalnymi aktywny aktywny lub połączenia sieć wirtualna-sieć wirtualna. Poniższe sekcje opisano kroki, aby ukończyć wykonywania.

## <a name ="aacrossprem"></a>Część 2 — nawiązywania połączenia między środowiskami lokalnymi aktywny aktywny
Nawiązanie połączenia między środowiskami lokalnymi, musisz utworzyć bramy sieci lokalnej do reprezentowania swoje lokalne urządzenie sieci VPN i połączenia bramy sieci VPN platformy Azure za pomocą bramy sieci lokalnej. W tym przykładzie brama Azure VPN gateway jest w trybie aktywny aktywny. Co w efekcie nawet, jeśli istnieje tylko jeden lokalnego urządzenia sieci VPN (Brama sieci lokalnej) i jedno połączenie zasobów, oba wystąpienia bramy sieci VPN platformy Azure ustanowią tunele S2S VPN za pomocą lokalnego urządzenia.

Przed kontynuowaniem upewnij się, że zostały wykonane [część 1](#aagateway) tego ćwiczenia.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Krok 1 — Tworzenie i konfigurowanie bramy sieci lokalnej
#### <a name="1-declare-your-variables"></a>1. Zadeklarowanie zmiennych
To ćwiczenie będą w dalszym ciągu kompilacji konfiguracji przedstawionej na diagramie. Należy pamiętać o zastąpieniu przykładowych wartości tymi, które mają zostać użyte w danej konfiguracji.

```powershell
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

Kilka rzeczy, aby informacje dotyczące parametrów bramy sieci lokalnej:

* Brama sieci lokalnej może być w tej samej lub innej lokalizacji i grupie zasobów co Brama sieci VPN. Ten przykład przedstawia je w różnych grupach zasobów, ale w tej samej lokalizacji platformy Azure.
* Jeśli istnieje tylko jeden lokalnego urządzenia sieci VPN, jak pokazano powyżej, połączenia aktywne aktywne można pracować z lub bez protokołu BGP. W tym przykładzie użyto protokołu BGP dla połączenia między środowiskami lokalnymi.
* Jeśli protokół BGP jest włączony, prefiksu, czego potrzebujesz do deklarowania bramy sieci lokalnej jest adres hosta, adresu IP elementu równorzędnego protokołu BGP na twoim urządzeniu sieci VPN. W tym przypadku jest/32 prefiksu "10.52.255.253/32".
* Przypominamy należy użyć innego ASN protokołu BGP między sieciami lokalnymi a siecią wirtualną platformy Azure. Jeśli są one takie same, należy zmienić numer ASN Twojej sieci wirtualnej, jeśli Twoje lokalne urządzenie sieci VPN używa już numer ASN nawiązać komunikację równorzędną z innymi sąsiadów protokołu BGP.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Utwórz bramę sieci lokalnej dla Site5
Przed kontynuowaniem upewnij się, że nadal masz połączenie z subskrypcją 1. Utwórz grupę zasobów, jeśli nie została jeszcze utworzona.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5
New-AzLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Krok 2 — połączenie bramy sieci wirtualnej i bramy sieci lokalnej
#### <a name="1-get-the-two-gateways"></a>1. Pobierz dwóch bram

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Utwórz sieć TestVNet1 Site5 połączenia
W tym kroku zostanie utworzone połączenie z sieci wirtualnej TestVNet1 do Site5_1 za pomocą "Wartość", wartość $True.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. Sieci VPN i BGP parametrów dla Twojego lokalnego urządzenia sieci VPN
W poniższym przykładzie przedstawiono parametry, które wejdzie w sekcji konfiguracji protokołu BGP na twoim urządzeniu sieci VPN w środowisku lokalnym na potrzeby tego ćwiczenia:

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

Można nawiązać połączenia, po upływie kilku minut, a sesja komunikacji równorzędnej protokołu BGP zostanie uruchomiony, gdy zostanie nawiązane połączenie protokołu IPsec. Do tej pory w tym przykładzie ma skonfigurowany tylko jeden lokalnego urządzenia sieci VPN, skutkuje na diagramie poniżej:

![active-active-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Krok 3 — łączenie dwóch lokalnych urządzeń sieci VPN bramy sieci VPN typu aktywne aktywne
Jeśli masz dwa urządzenia sieci VPN w tej samej sieci lokalnej, można osiągnąć podwójna nadmiarowość, nawiązując połączenie bramy sieci VPN platformy Azure do drugiego urządzenia sieci VPN.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. Tworzenie druga Brama sieci lokalnej dla Site5
Adres IP bramy, prefiks adresu i adres komunikacji równorzędnej protokołu BGP dla druga Brama sieci lokalnej nie może pokrywać z poprzednią wersją bramy sieci lokalnej dla tej samej sieci lokalnej.

```powershell
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```powershell
New-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Łączenie bramy sieci wirtualnej i druga Brama sieci lokalnej
Utwórz połączenie z sieci wirtualnej TestVNet1 do Site5_2 za pomocą "Wartość", wartość $True

```powershell
$lng5gw2 = Get-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. Sieci VPN i BGP parametrów dla drugiego lokalnego urządzenia sieci VPN
Podobnie poniżej listy parametrów zawrzesz drugiego urządzenia sieci VPN:

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

Po ustanowiono połączenia (tunele), będziesz mieć dwa nadmiarowe urządzenia sieci VPN i tunele, połączenie z siecią lokalną i platformą Azure:

![dual-redundancy-crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name ="aav2v"></a>Część 3 — nawiązania połączenia sieć wirtualna-sieć wirtualna aktywny aktywny
W tej sekcji tworzy aktywne aktywne połączenie sieć wirtualna-sieć wirtualna za pomocą protokołu BGP. 

Poniższe instrukcje stanowią ciąg dalszy poprzedzających je kroków wymienionych powyżej. Należy wykonać [część 1](#aagateway) tworzenie i konfigurowanie sieci TestVNet1 i bramę VPN z protokołem BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Krok 1 — Tworzenie TestVNet2 i bramy sieci VPN
Należy się upewnić, że przestrzeń adresów IP nowej sieci wirtualnej TestVNet2, nie nakłada się z żadnym z zakresów w Twojej sieci wirtualnej.

W tym przykładzie sieci wirtualne należą do tej samej subskrypcji. Można skonfigurować połączenia sieć wirtualna-sieć wirtualna między różnymi subskrypcjami; Zapoznaj się [Konfigurowanie połączenia sieć wirtualna-sieć wirtualna](vpn-gateway-vnet-vnet-rm-ps.md) Aby uzyskać więcej informacji. Upewnij się, możesz dodać "-EnableBgp $True" podczas tworzenia połączeń, aby włączyć protokół BGP.

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

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Utwórz TestVNet2 w nowej grupie zasobów

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. Tworzenie bramy sieci VPN typu aktywne aktywne dla TestVNet2
Żądanie dwa publiczne adresy IP do przydzielenia do bramy, która zostanie utworzona dla sieci wirtualnej. Zostanie również definiować podsieci i konfiguracje adresów IP wymagana.

```powershell
$gw2pip1 = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

Tworzenie bramy sieci VPN z numer AS oraz flagę "EnableActiveActiveFeature". Należy pamiętać, że musisz przesłonić domyślny numer ASN na bram sieci VPN platformy Azure. Numery ASN dla połączonych sieci wirtualnych muszą być różne, aby włączyć protokół BGP i routing tranzytowy.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Krok 2 — połączenie bram sieci wirtualnej TestVNet1 i TestVNet2
W tym przykładzie obie bramy znajdują się w tej samej subskrypcji. Po ukończeniu tego kroku, w tej samej sesji programu PowerShell.

#### <a name="1-get-both-gateways"></a>1. Pobierz obu bram
Pamiętaj o zalogowaniu się i połączeniu z subskrypcją 1.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. Tworzenie zarówno połączeń
W tym kroku utworzysz połączenie z sieci wirtualnej TestVNet1 TestVNet2 i połączenie z TestVNet2 do sieci TestVNet1.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Pamiętaj włączyć protokół BGP dla obu połączeń.
> 
> 

Po wykonaniu tych kroków, połączenie ustanowią można w ciągu kilku minut i protokołu BGP sesji połączenia równorzędnego będą się po zakończeniu połączenia sieć wirtualna-sieć wirtualna za pomocą podwójna nadmiarowość:

![active-active-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>Aktualizowanie istniejącej bramy sieci VPN

Ten rozdział pomaga zmienić istniejącą bramę sieci VPN platformy Azure z aktywne-w gotowości, aby tryb aktywny aktywny lub na odwrót.

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>Zmień bramę aktywne-w gotowości do bramy aktywny aktywny

Poniższy przykład konwertuje bramę aktywne-w gotowości na bramę aktywny aktywny. Po zmianie bramy aktywne-w gotowości do aktywne aktywne, możesz utworzyć inny publiczny adres IP, a następnie dodać drugi konfigurację IP bramy.

#### <a name="1-declare-your-variables"></a>1. Zadeklarowanie zmiennych

Zastąp następujące parametry używane dla przykładów przy użyciu ustawień, które wymagają własnych konfiguracji następnie Zadeklaruj te zmienne.

```powershell
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

Po deklarowanie zmiennych, można skopiować i wkleić w tym przykładzie do konsoli programu PowerShell.

```powershell
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. Tworzenie publicznego adresu IP, a następnie dodaj druga Konfiguracja protokołu IP bramy

```powershell
$gwpip2 = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. Włącz tryb aktywny aktywny i zaktualizować bramę

W tym kroku możesz włączyć tryb aktywny aktywny i zaktualizować bramę. W tym przykładzie bramy sieci VPN aktualnie używa starszej wersji standardowej jednostki SKU. Aktywne aktywne nie obsługuje jednak standardowej jednostki SKU. Aby zmienić rozmiar starszej wersji jednostki SKU na taki, który jest obsługiwany (w tym przypadku HighPerformance), wystarczy określić obsługiwanych starszych jednostek SKU, którego chcesz używać.

* Nie można zmienić starszych jednostek SKU, do jednego z nowych jednostek SKU, przy użyciu tego kroku. Można tylko zmienić rozmiar starszej wersji jednostki SKU do innego obsługiwanych starszej wersji jednostki SKU. Na przykład nie można zmienić jednostkę SKU od planu Standard do VpnGw1 (mimo że VpnGw1 jest obsługiwane na aktywny / aktywny) ponieważ warstwa standardowa to starszej wersji jednostki SKU i VpnGw1 jest bieżąca jednostka SKU. Aby uzyskać więcej informacji na temat zmiany rozmiaru i Migrowanie jednostek SKU, zobacz [jednostki SKU bramy](vpn-gateway-about-vpngateways.md#gwsku).

* Jeśli chcesz zmienić rozmiar bieżąca jednostka SKU na przykład VpnGw1 do VpnGw3, możesz to zrobić przy użyciu tego kroku, ponieważ jednostki SKU znajdują się w tej samej rodziny jednostki SKU. Aby to zrobić, należy użyć wartości: ```-GatewaySku VpnGw3```

Gdy używana jest to w danym środowisku, jeśli nie potrzebujesz zmienić rozmiar bramy, nie należy określić parametr GatewaySku —. Należy zauważyć, że w tym kroku należy ustawić obiektu bramy w programie PowerShell, aby wyzwolić rzeczywistej aktualizacji. Ta aktualizacja może potrwać od 30 do 45 minut, nawet wtedy, gdy nie są rozmiaru bramy.

```powershell
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>Zmień bramę aktywny / aktywny z bramą aktywne-w gotowości
#### <a name="1-declare-your-variables"></a>1. Zadeklarowanie zmiennych

Zastąp następujące parametry używane dla przykładów przy użyciu ustawień, które wymagają własnych konfiguracji następnie Zadeklaruj te zmienne.

```powershell
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

Po deklarowanie zmiennych, należy uzyskać nazwę konfiguracji adresu IP, który chcesz usunąć.

```powershell
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. Usuń konfigurację protokołu IP bramy i Wyłącz tryb aktywny aktywny

Ten przykład umożliwia Usuń konfigurację protokołu IP bramy i Wyłącz tryb aktywny aktywny. Należy zauważyć, że należy ustawić obiektu bramy w programie PowerShell w celu wyzwolenia aktualizacji rzeczywiste.

```powershell
Remove-AzVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

Ta aktualizacja może potrwać do 30 do 45 minut.

## <a name="next-steps"></a>Kolejne kroki
Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Kroki opisano w sekcji [Tworzenie maszyny wirtualnej](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
