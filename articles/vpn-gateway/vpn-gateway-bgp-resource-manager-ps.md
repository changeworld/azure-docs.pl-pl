---
title: 'Brama sieci VPN platformy Azure: konfigurowanie protokołu BGP: programu PowerShell'
description: W tym artykule okonywanie konfiguracji protokołu BGP przy użyciu usługi Azure Resource Manager i powershell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 04/12/2017
ms.author: yushwang
ms.openlocfilehash: 78147a96d6d9e92c2602b6a83cbed743cf2abf37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152044"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-powershell"></a>Jak skonfigurować usługę BGP w bramach sieci VPN platformy Azure przy użyciu programu PowerShell
W tym artykule otrzymasz od ciebie kroków, aby włączyć usługę BGP na międzylokacyjnym połączeniu sieci VPN lokacja lokacja (S2S) i połączeniu sieci wirtualnej z siecią wirtualną przy użyciu modelu wdrażania Menedżera zasobów i programu PowerShell.



## <a name="about-bgp"></a>BGP — informacje
BGP to standardowy protokół routingu używany często w Internecie do wymiany informacji o routingu i osiągalności między dwiema lub wieloma sieciami. Protokół BGP umożliwia bramom sieci VPN na platformie Azure i lokalnym urządzeniom sieci VPN (nazywanym elementami równorzędnymi lub sąsiednimi BGP), przeprowadzaną za pośrednictwem bram lub routerów wymianę „tras” zawierających przeznaczone dla obu bram informacje na temat dostępności i osiągalności tych prefiksów. Protokół BGP umożliwia również włączenie routingu tranzytowego między wieloma sieciami poprzez propagowanie tras, których brama BGP uczy się od jednego elementu równorzędnego BGP, we wszystkich innych elementach równorzędnych BGP.

Zobacz [Omówienie protokołu BGP z bramami sieci VPN platformy Azure,](vpn-gateway-bgp-overview.md) aby uzyskać więcej dyskusji na temat korzyści związanych z usługą BGP i zrozumieć wymagania techniczne i zagadnienia związane z korzystaniem z protokołu BGP.

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Wprowadzenie do protokołu BGP na portalach sieci VPN platformy Azure

W tym artykule przedstawiono kroki, aby wykonać następujące zadania:

* [Część 1 — włączanie protokołu BGP w bramie sieci VPN platformy Azure](#enablebgp)
* Część 2 - Ustanowienie połączenia międzylokacowego z BGP
* [Część 3 - Ustanawianie połączenia sieci wirtualnej z siecią wirtualną z usługą BGP](#v2vbgp)

Każda część instrukcji stanowi podstawowy blok konstrukcyjny włączania protokołu BGP w łączności sieciowej. Jeśli ukończysz wszystkie trzy części, tworzysz topologię, jak pokazano na poniższym diagramie:

![Topologia BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Można połączyć części, aby zbudować bardziej złożoną sieć tranzytową o wielu przeskoku, która spełni Twoje potrzeby.

## <a name="part-1---configure-bgp-on-the-azure-vpn-gateway"></a><a name ="enablebgp"></a>Część 1 — konfigurowanie protokołu BGP w bramie sieci VPN platformy Azure
Kroki konfiguracji skonfigurować parametry BGP bramy sieci VPN platformy Azure, jak pokazano na poniższym diagramie:

![Brama BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Przed rozpoczęciem
* Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
* Zainstaluj polecenia cmdlet programu Azure Resource Manager programu PowerShell. Aby uzyskać więcej informacji na temat instalowania poleceń cmdlet programu PowerShell, zobacz artykuł [How to install and configure Azure PowerShell](/powershell/azure/overview) (Instalowanie i konfigurowanie programu Azure PowerShell). 

### <a name="step-1---create-and-configure-vnet1"></a>Krok 1 - Tworzenie i konfigurowanie sieci wirtualnej1
#### <a name="1-declare-your-variables"></a>1. Zadeklaruj swoje zmienne
W tym ćwiczeniu zaczynamy od zadeklarowania naszych zmiennych. Poniższy przykład deklaruje zmienne przy użyciu wartości dla tego ćwiczenia. Podczas konfigurowania produktu należy pamiętać o zastąpieniu ich odpowiednimi wartościami. Tych zmiennych można użyć, aby wykonać opisane kroki w celu zapoznania się z tego typu konfiguracją. Zmodyfikuj zmienne, a następnie skopiuj je i wklej do konsoli programu PowerShell.

```powershell
$Sub1 = "Replace_With_Your_Subscription_Name"
$RG1 = "TestBGPRG1"
$Location1 = "East US"
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
$GWIPName1 = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection12 = "VNet1toVNet2"
$Connection15 = "VNet1toSite5"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Połącz się z subskrypcją i utwórz nową grupę zasobów
Aby użyć poleceń cmdlet Menedżera zasobów, upewnij się, że przełączysz się do trybu programu PowerShell. Więcej informacji znajduje się w temacie [Using Windows PowerShell with Resource Manager](../powershell-azure-resource-manager.md) (Używanie programu Windows PowerShell z usługą Resource Manager).

Otwórz konsolę programu PowerShell i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Utwórz TestVNet1
Poniższy przykład tworzy sieć wirtualną o nazwie TestVNet1 i trzy podsieci, jeden o nazwie GatewaySubnet, jeden o nazwie FrontEnd i jeden o nazwie Wewnętrznej bazy danych. Podczas zastępowania wartości ważne jest, aby podsieć bramy zawsze nosiła nazwę GatewaySubnet. W przypadku nadania jej innej nazwy proces tworzenia bramy zakończy się niepowodzeniem.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Krok 2 - Tworzenie bramy sieci VPN dla TestVNet1 z parametrami BGP
#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. Tworzenie konfiguracji IP i podsieci
Następnie zostaje przesłane żądanie przydzielenia publicznego adresu IP do bramy, która zostanie utworzona dla sieci wirtualnej. Zdefiniujesz również wymagane konfiguracje podsieci i ip.

```powershell
$gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Utwórz bramę sieci VPN z numerem AS
Utwórz bramę sieci wirtualnej dla sieci TestVNet1. BGP wymaga bramy sieci VPN opartej na marszrutach, a także parametru dodawania -Asn, aby ustawić numer ASN (AS Number) dla TestVNet1. Jeśli nie ustawisz parametru ASN, przypisany jest ASN 65515. Tworzenie bramy może potrwać co najmniej 30 minut.

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Uzyskiwanie adresu IP elementu równorzędnego usługi Azure BGP
Po utworzeniu bramy należy uzyskać adres IP elementu równorzędnego BGP w usłudze Azure VPN Gateway. Ten adres jest potrzebny do skonfigurowania bramy sieci VPN platformy Azure jako elementu równorzędnego BGP dla lokalnych urządzeń sieci VPN.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet1gw.BgpSettingsText
```

Ostatnie polecenie pokazuje odpowiednie konfiguracje protokołu BGP w usłudze Azure VPN Gateway; na przykład:

```powershell
$vnet1gw.BgpSettingsText
{
    "Asn": 65010,
    "BgpPeeringAddress": "10.12.255.30",
    "PeerWeight": 0
}
```

Po utworzeniu bramy można użyć tej bramy do ustanowienia połączenia międzylokalnościowego lub połączenia sieci wirtualnej z siecią wirtualną z usługą BGP. W poniższych sekcjach przedstawiono kroki, aby ukończyć ćwiczenie.

## <a name="part-2---establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembbgp"></a>Część 2 - Ustanowienie połączenia międzylokacowego z BGP

Aby ustanowić połączenie międzylokacje, należy utworzyć bramę sieci lokalnej reprezentującą lokalne urządzenie sieci VPN oraz połączenie, aby połączyć bramę sieci VPN z bramą sieciowej. Chociaż istnieją artykuły, które prowadzą przez te kroki, ten artykuł zawiera dodatkowe właściwości wymagane do określenia parametrów konfiguracji BGP.

![BGP dla międzypomieszczeń](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Przed kontynuowaniem upewnij się, że ukończyłeś [część 1](#enablebgp) tego ćwiczenia.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Krok 1 - Tworzenie i konfigurowanie bramy sieci lokalnej

#### <a name="1-declare-your-variables"></a>1. Zadeklaruj swoje zmienne

To ćwiczenie kontynuuje tworzenie konfiguracji pokazanej na diagramie. Należy pamiętać o zastąpieniu przykładowych wartości tymi, które mają zostać użyte w danej konfiguracji.

```powershell
$RG5 = "TestBGPRG5"
$Location5 = "East US 2"
$LNGName5 = "Site5"
$LNGPrefix50 = "10.52.255.254/32"
$LNGIP5 = "Your_VPN_Device_IP"
$LNGASN5 = 65050
$BGPPeerIP5 = "10.52.255.254"
```

Kilka rzeczy, na które należy zwrócić uwagę w odniesieniu do parametrów bramy sieci lokalnej:

* Brama sieci lokalnej może znajdować się w tej samej lub innej lokalizacji i grupie zasobów co brama sieci VPN. W tym przykładzie przedstawiono je w różnych grupach zasobów w różnych lokalizacjach.
* Prefiks, który należy zadeklarować dla bramy sieci lokalnej, jest adresem hosta adresu IP elementu równorzędnego BGP na urządzeniu sieci VPN. W tym przypadku jest to /32 prefiks "10.52.255.254/32".
* Przypominamy, że należy użyć różnych sieci ASN BGP między sieciami lokalnymi a siecią wirtualną platformy Azure. Jeśli są takie same, należy zmienić sieć wirtualną ASN, jeśli lokalne urządzenie sieci VPN już używa asn do obsługi równorzędnej z innymi sąsiadami protokołu BGP.

Przed kontynuowaniem upewnij się, że nadal masz połączenie z subskrypcją 1.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Tworzenie bramy sieci lokalnej dla lokacji5

Przed utworzeniem bramy sieci lokalnej należy utworzyć grupę zasobów, jeśli nie została utworzona. Zwróć uwagę na dwa dodatkowe parametry bramy sieci lokalnej: Asn i BgpPeerAddress.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Krok 2 - Podłącz bramę sieci wirtualnej i bramę sieci lokalnej

#### <a name="1-get-the-two-gateways"></a>1. Pobierz dwie bramy

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Utwórz połączenie TestVNet1 z witryną5

W tym kroku utworzysz połączenie z TestVNet1 do Site5. Aby włączyć usługę BGP dla tego połączenia, należy określić "-EnableBGP $True". Jak wspomniano wcześniej, możliwe jest, aby mieć połączenia BGP i innych niż BGP dla tej samej bramy sieci VPN platformy Azure. Jeśli protokół BGP nie jest włączony we właściwości połączenia, platforma Azure nie włączy protokołu BGP dla tego połączenia, nawet jeśli parametry protokołu BGP są już skonfigurowane w obu bramkach.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

W poniższym przykładzie wymieniono parametry wprowadzone w sekcji konfiguracji Protokołu BGP na lokalnym urządzeniu sieci VPN dla tego ćwiczenia:

```

- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP    : 10.12.255.30
- Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Połączenie jest ustanawiane po kilku minutach, a sesja równorzędna BGP rozpoczyna się po nawiązaniu połączenia IPsec.

## <a name="part-3---establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>Część 3 - Ustanawianie połączenia sieci wirtualnej z siecią wirtualną z usługą BGP

W tej sekcji dodano połączenie sieci wirtualnej z siecią wirtualną z usługą BGP, jak pokazano na poniższym diagramie:

![BGP dla sieci wirtualnej do sieci wirtualnej](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Poniższe instrukcje są kontynuowane w poprzednich krokach. Musisz ukończyć [część I,](#enablebgp) aby utworzyć i skonfigurować TestVNet1 i bramę sieci VPN z BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Krok 1 - Tworzenie TestVNet2 i bramy sieci VPN

Ważne jest, aby upewnić się, że przestrzeń adresowa IP nowej sieci wirtualnej, TestVNet2, nie pokrywa się z żadnym z zakresów sieci wirtualnej.

W tym przykładzie sieci wirtualne należą do tej samej subskrypcji. Można skonfigurować połączenia sieci wirtualnej z siecią wirtualną między różnymi subskrypcjami. Aby uzyskać więcej informacji, zobacz [Konfigurowanie połączenia sieci wirtualnej z siecią wirtualną](vpn-gateway-vnet-vnet-rm-ps.md). Upewnij się, że podczas tworzenia połączeń dodano "-EnableBgp $True" w celu włączenia protokołu BGP.

#### <a name="1-declare-your-variables"></a>1. Zadeklaruj swoje zmienne

Należy pamiętać o zastąpieniu przykładowych wartości tymi, które mają zostać użyte w danej konfiguracji.

```powershell
$RG2 = "TestBGPRG2"
$Location2 = "West US"
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
$GWIPName2 = "VNet2GWIP"
$GWIPconfName2 = "gwipconf2"
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

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. Tworzenie bramy sieci VPN dla TestVNet2 z parametrami BGP

Zażądaj publicznego adresu IP, który zostanie przydzielony do bramy utworzonej dla sieci wirtualnej i zdefiniuj wymagane konfiguracje podsieci i adresów IP.

```powershell
$gwpip2    = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2     = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2
```

Utwórz bramę sieci VPN z numerem AS. Należy zastąpić domyślną sieć ASN w swoich bramach sieci VPN platformy Azure. Sieci ASN dla połączonych sieci wirtualnych muszą być różne, aby włączyć BGP i routingu tranzytowego.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN
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

W tym kroku należy utworzyć połączenie z TestVNet1 do TestVNet2 i połączenie z TestVNet2 do TestVNet1.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Pamiętaj, aby włączyć BGP dla obu połączeń.
> 
> 

Po wykonaniu tych kroków połączenie jest ustanawiane po kilku minutach. Sesja komunikacji równorzędnej protokołu BGP jest w górę po zakończeniu połączenia sieci wirtualnej do sieci wirtualnej.

Po wykonaniu wszystkich trzech części tego ćwiczenia ustanowiono następującą topologię sieci:

![BGP dla sieci wirtualnej do sieci wirtualnej](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Następne kroki

Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Kroki opisano w sekcji [Tworzenie maszyny wirtualnej](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
