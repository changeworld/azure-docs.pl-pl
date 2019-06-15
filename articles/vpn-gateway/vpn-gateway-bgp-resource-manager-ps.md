---
title: 'Skonfiguruj protokół BGP dla bram Azure VPN Gateway: Menedżer zasobów: Program PowerShell | Dokumentacja firmy Microsoft'
description: W tym artykule przedstawiono konfigurowanie protokołu BGP z bramami sieci VPN Azure przy użyciu usługi Azure Resource Manager i programu PowerShell.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 905b11a7-1333-482c-820b-0fd0f44238e5
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: yushwang
ms.openlocfilehash: c65ea038fc39702affae93cb68b8cf644393c62e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66150218"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-powershell"></a>Jak skonfigurować protokół BGP w bramach sieci VPN Azure przy użyciu programu PowerShell
W tym artykule przedstawiono kroki, aby włączyć protokół BGP dla połączenia sieci VPN typu lokacja-lokacja (S2S) między środowiskami lokalnymi i połączenia sieć wirtualna-sieć wirtualna za pomocą modelu wdrażania usługi Resource Manager i programu PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about-bgp"></a>BGP — informacje
BGP to standardowy protokół routingu używany często w Internecie do wymiany informacji o routingu i osiągalności między dwiema lub wieloma sieciami. Protokół BGP umożliwia bramom sieci VPN na platformie Azure i lokalnym urządzeniom sieci VPN (nazywanym elementami równorzędnymi lub sąsiednimi BGP), przeprowadzaną za pośrednictwem bram lub routerów wymianę „tras” zawierających przeznaczone dla obu bram informacje na temat dostępności i osiągalności tych prefiksów. Protokół BGP umożliwia również włączenie routingu tranzytowego między wieloma sieciami poprzez propagowanie tras, których brama BGP uczy się od jednego elementu równorzędnego BGP, we wszystkich innych elementach równorzędnych BGP.

Zobacz [Omówienie protokołu BGP z bramami sieci VPN Azure](vpn-gateway-bgp-overview.md) do więcej dyskusji na temat korzyści użycia protokołu BGP i zrozumienie wymagań technicznych oraz kwestii związanych z przy użyciu protokołu BGP.

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Wprowadzenie do protokołu BGP w bramach sieci VPN platformy Azure

Ten artykuł przeprowadzi Cię przez kroki, aby wykonać następujące zadania:

* [Część 1. Włączanie protokołu BGP dla bramy sieci VPN platformy Azure](#enablebgp)
* Część 2 — ustanowić połączenia między środowiskami lokalnymi za pomocą protokołu BGP
* [Część 3 — ustanowienia połączenia sieć wirtualna-sieć wirtualna za pomocą protokołu BGP](#v2vbgp)

Każda część instrukcji stanowi podstawowy blok konstrukcyjny włączania protokołu BGP w łączność sieciową. Jeśli wykonasz wszystkich trzech części, tworzysz topologii, jak pokazano na poniższym diagramie:

![Topologii protokołu BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Można połączyć części ze sobą, aby tworzyć bardziej złożone, z wieloma przeskokami tranzytowej sieci, który odpowiada Twoim potrzebom.

## <a name ="enablebgp"></a>Część 1 — Konfigurowanie protokołu BGP bramy sieci VPN platformy Azure
Kroki konfiguracji Skonfiguruj parametry protokołu BGP bramy sieci VPN platformy Azure, jak pokazano na poniższym diagramie:

![BGP Gateway](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Przed rozpoczęciem
* Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
* Zainstaluj polecenia cmdlet programu Azure PowerShell Resource Manager. Aby uzyskać więcej informacji na temat instalowania poleceń cmdlet programu PowerShell, zobacz artykuł [How to install and configure Azure PowerShell](/powershell/azure/overview) (Instalowanie i konfigurowanie programu Azure PowerShell). 

### <a name="step-1---create-and-configure-vnet1"></a>Krok 1 — Tworzenie i konfigurowanie sieci VNet1
#### <a name="1-declare-your-variables"></a>1. Zadeklarowanie zmiennych
Na potrzeby tego ćwiczenia Rozpoczniemy od zadeklarowania zmiennych. Poniższy przykład deklaruje zmienne przy użyciu wartości na potrzeby tego ćwiczenia. Podczas konfigurowania produktu należy pamiętać o zastąpieniu ich odpowiednimi wartościami. Tych zmiennych można użyć, aby wykonać opisane kroki w celu zapoznania się z tego typu konfiguracją. Zmodyfikuj zmienne, a następnie skopiuj je i wklej do konsoli programu PowerShell.

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

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Połączyć subskrypcję i utworzyć nową grupę zasobów
Za pomocą poleceń cmdlet usługi Resource Manager, upewnij się, że przełączanie do trybu programu PowerShell. Więcej informacji znajduje się w temacie [Using Windows PowerShell with Resource Manager](../powershell-azure-resource-manager.md) (Używanie programu Windows PowerShell z usługą Resource Manager).

Otwórz konsolę programu PowerShell i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Utworzenie sieci TestVNet1
Poniższy przykład tworzy sieć wirtualną o nazwie sieci TestVNet1 oraz trzy podsieci, nazwy GatewaySubnet, jeden frontonu o nazwie i jeden o nazwie wewnętrznej bazy danych. Podczas zastępowania wartości ważne jest, aby podsieć bramy zawsze nosiła nazwę GatewaySubnet. W przypadku nadania jej innej nazwy proces tworzenia bramy zakończy się niepowodzeniem.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Krok 2 — Tworzenie bramy sieci VPN dla sieci TestVNet1 z parametrami protokołu BGP
#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. Utwórz konfiguracje adresów IP i podsieci
Następnie zostaje przesłane żądanie przydzielenia publicznego adresu IP do bramy, która zostanie utworzona dla sieci wirtualnej. Należy również zdefiniować wymagana podsieć i konfiguracje adresów IP.

```powershell
$gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Tworzenie bramy sieci VPN z numerem AS
Utwórz bramę sieci wirtualnej dla sieci TestVNet1. Protokół BGP wymaga bramy sieci VPN opartej na trasach, a także dodawanie parametru, - Asn, aby ustawić numer ASN (numer AS) dla sieci TestVNet1. Jeśli parametr numer ASN nie jest ustawiony, zostanie przypisany numer ASN 65515. Tworzenie bramy może potrwać co najmniej 30 minut.

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Uzyskaj adres IP elementu równorzędnego protokołu BGP platformy Azure
Po utworzeniu bramy, należy uzyskać adres IP elementu równorzędnego protokołu BGP bramy sieci VPN platformy Azure. Ten adres jest wymagane do skonfigurowania bramy Azure VPN Gateway, jako element równorzędny BGP dla lokalnych urządzeń sieci VPN.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet1gw.BgpSettingsText
```

Ostatnie polecenie pokazuje odpowiednich konfiguracji protokołu BGP bramy sieci VPN platformy Azure; na przykład:

```powershell
$vnet1gw.BgpSettingsText
{
    "Asn": 65010,
    "BgpPeeringAddress": "10.12.255.30",
    "PeerWeight": 0
}
```

Po utworzeniu bramy można użyć tej bramy można ustanowić połączenia między środowiskami lokalnymi lub połączenia sieć wirtualna-sieć wirtualna za pomocą protokołu BGP. Poniższe sekcje opisano kroki, aby ukończyć wykonywania.

## <a name ="crossprembbgp"></a>Część 2 — ustanowić połączenia między środowiskami lokalnymi za pomocą protokołu BGP

Aby nawiązać połączenie między środowiskami lokalnymi, musisz utworzyć bramy sieci lokalnej do reprezentowania swoje lokalne urządzenie sieci VPN i połączenia do łączenia bramy sieci VPN z bramy sieci lokalnej. Gdy istnieją artykułów, które przeprowadzą Cię przez następujące kroki, ten artykuł zawiera dodatkowe właściwości, które są wymagane, aby określić parametry konfiguracji protokołu BGP.

![Protokół BGP dla między środowiskami lokalnymi](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Przed kontynuowaniem upewnij się, że zostały wykonane [część 1](#enablebgp) tego ćwiczenia.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Krok 1 — Tworzenie i konfigurowanie bramy sieci lokalnej

#### <a name="1-declare-your-variables"></a>1. Zadeklarowanie zmiennych

To ćwiczenie w dalszym ciągu kompilacji konfiguracji przedstawionej na diagramie. Należy pamiętać o zastąpieniu przykładowych wartości tymi, które mają zostać użyte w danej konfiguracji.

```powershell
$RG5 = "TestBGPRG5"
$Location5 = "East US 2"
$LNGName5 = "Site5"
$LNGPrefix50 = "10.52.255.254/32"
$LNGIP5 = "Your_VPN_Device_IP"
$LNGASN5 = 65050
$BGPPeerIP5 = "10.52.255.254"
```

Kilka rzeczy, aby informacje dotyczące parametrów bramy sieci lokalnej:

* Brama sieci lokalnej może być w tej samej lub innej lokalizacji i grupie zasobów co Brama sieci VPN. Ten przykład przedstawia je w różnych grupach zasobów w różnych lokalizacjach.
* Prefiks, czego potrzebujesz do deklarowania bramy sieci lokalnej jest adres hosta, adresu IP elementu równorzędnego protokołu BGP na twoim urządzeniu sieci VPN. W tym przypadku jest/32 prefiksu "10.52.255.254/32".
* Przypominamy należy użyć innego ASN protokołu BGP między sieciami lokalnymi a siecią wirtualną platformy Azure. Jeśli są one takie same, należy zmienić numer ASN Twojej sieci wirtualnej, jeśli Twoje lokalne urządzenie sieci VPN używa już numer ASN nawiązać komunikację równorzędną z innymi sąsiadów protokołu BGP.

Przed kontynuowaniem upewnij się, że nadal masz połączenie z subskrypcją 1.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Utwórz bramę sieci lokalnej dla Site5

Pamiętaj utworzyć grupę zasobów, jeśli nie został utworzony, przed utworzeniem bramy sieci lokalnej. Zwróć uwagę, dwa dodatkowe parametry dla bramy sieci lokalnej: Numer ASN i BgpPeerAddress.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Krok 2 — połączenie bramy sieci wirtualnej i bramy sieci lokalnej

#### <a name="1-get-the-two-gateways"></a>1. Pobierz dwóch bram

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Utwórz sieć TestVNet1 Site5 połączenia

W tym kroku zostanie utworzone połączenie z sieci wirtualnej TestVNet1 do Site5. Należy określić "-EnableBGP $True" można włączyć protokołu BGP dla tego połączenia. Jak już wspomniano, istnieje możliwość połączenia protokołu BGP i protokołów innych niż BGP dla tej samej bramy sieci VPN platformy Azure. Chyba, że Protokół BGP jest włączony we właściwości połączenia, Azure nie włączyć protokół BGP dla tego połączenia, nawet jeśli Protokół BGP parametry zostały już skonfigurowane na obu bram.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

Poniższy przykład wyświetla parametry, które możesz wprowadzić do sekcji konfiguracji protokołu BGP na twoim urządzeniu sieci VPN w środowisku lokalnym na potrzeby tego ćwiczenia:

```

- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP    : 10.12.255.30
- Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Po kilku minutach zostanie nawiązane połączenie, i sesji komunikacji równorzędnej protokołu BGP raz rozpoczyna protokołu IPsec, połączenie zostanie nawiązane.

## <a name ="v2vbgp"></a>Część 3 — ustanowienia połączenia sieć wirtualna-sieć wirtualna za pomocą protokołu BGP

W tej sekcji dodaje połączenia sieć wirtualna-sieć wirtualna za pomocą protokołu BGP, jak pokazano na poniższym diagramie:

![Protokół BGP dla sieci wirtualnej między sieciami wirtualnymi](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Poniższe instrukcje są kontynuacją z poprzednich kroków. Należy wykonać [części I](#enablebgp) tworzenie i konfigurowanie sieci TestVNet1 i bramę VPN z protokołem BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Krok 1 — Tworzenie TestVNet2 i bramy sieci VPN

Należy się upewnić, że przestrzeń adresów IP nowej sieci wirtualnej TestVNet2, nie nakłada się z żadnym z zakresów w Twojej sieci wirtualnej.

W tym przykładzie sieci wirtualne należą do tej samej subskrypcji. Można skonfigurować połączenia sieć wirtualna-sieć wirtualna między różnymi subskrypcjami. Aby uzyskać więcej informacji, zobacz [Konfigurowanie połączenia sieć wirtualna-sieć wirtualna](vpn-gateway-vnet-vnet-rm-ps.md). Upewnij się, możesz dodać "-EnableBgp $True" podczas tworzenia połączeń, aby włączyć protokół BGP.

#### <a name="1-declare-your-variables"></a>1. Zadeklarowanie zmiennych

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

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. Tworzenie bramy sieci VPN dla TestVNet2 z parametrami protokołu BGP

Żądanie publicznego adresu IP do przydzielenia do bramy, należy utworzyć dla sieci wirtualnej i definiowania wymagane podsieci i konfiguracje adresów IP.

```powershell
$gwpip2    = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2     = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2
```

Tworzenie bramy sieci VPN, z numerem AS. Konieczne jest przesłonięcie domyślny numer ASN na bram sieci VPN platformy Azure. Numery ASN dla połączonych sieci wirtualnych muszą być różne, aby włączyć protokół BGP i routing tranzytowy.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard -Asn $VNet2ASN
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

W tym kroku zostanie utworzone połączenie z sieci wirtualnej TestVNet1 TestVNet2 i połączenie z TestVNet2 do sieci TestVNet1.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Pamiętaj włączyć protokół BGP dla obu połączeń.
> 
> 

Po wykonaniu tych kroków, połączenie zostanie nawiązane po kilku minutach. Sesja komunikacji równorzędnej protokołu BGP działa po ukończeniu połączenia sieć wirtualna-sieć wirtualna.

Jeśli ukończono wszystkie trzy części tego ćwiczenia upewnieniu się, następująca topologia sieci:

![Protokół BGP dla sieci wirtualnej między sieciami wirtualnymi](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Kolejne kroki

Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Kroki opisano w sekcji [Tworzenie maszyny wirtualnej](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
