---
title: 'Azure VPN Gateway: łączenie bram z wieloma lokalnymi urządzeniami sieci VPN opartymi na zasadach'
description: Skonfiguruj bramę sieci VPN opartą na trasach platformy Azure dla wielu urządzeń sieci VPN opartych na zasadach przy użyciu Azure Resource Manager i programu PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: yushwang
ms.openlocfilehash: dec088ed751856957735867f740dc951c3b8f2ac
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162076"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Łączenie bram sieci VPN platformy Azure z wieloma lokalnymi urządzeniami sieci VPN opartymi na zasadach przy użyciu programu PowerShell

Ten artykuł ułatwia skonfigurowanie bramy sieci VPN opartej na trasach platformy Azure w celu połączenia z wieloma lokalnymi urządzeniami sieci VPN opartymi na zasadach wykorzystującymi niestandardowe zasady protokołu IPsec/IKE dla połączeń sieci VPN S2S.



## <a name="about"></a>Informacje o bramach sieci VPN opartych na zasadach i trasach

Zasady — *a* urządzenia sieci VPN oparte na trasach różnią się w zależności od tego, jak selektory ruchu IPSec są ustawiane dla połączenia:

* **Oparte na zasadach** Urządzenia sieci VPN używają kombinacji prefiksów z obu sieci do definiowania sposobu szyfrowania i odszyfrowywania ruchu za pośrednictwem tuneli IPsec. Jest zazwyczaj oparty na urządzeniach zapory, które wykonują filtrowanie pakietów. Szyfrowanie i odszyfrowywanie tunelu IPsec są dodawane do aparatu filtrowania pakietów i przetwarzania.
* **Oparta na trasach** Urządzenia sieci VPN używają selektorów ruchu dowolnego do dowolnego (symbol wieloznaczny) i umożliwiają kierowanie tabel routingu/przekazywania do różnych tuneli IPsec. Jest zazwyczaj oparty na platformach routera, w których każdy tunel IPsec jest modelowany jako interfejs sieciowy lub VTI (interfejs tunelu wirtualnego).

Na poniższych diagramach są wyróżniane dwa modele:

### <a name="policy-based-vpn-example"></a>Przykład sieci VPN opartej na zasadach
![oparte na zasadach](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Przykład sieci VPN opartej na trasach
![oparta na trasach](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Pomoc techniczna platformy Azure dla sieci VPN opartej na zasadach
Obecnie platforma Azure obsługuje oba tryby bram sieci VPN: bramy sieci VPN oparte na trasach i bramy sieci VPN oparte na zasadach. Są one oparte na różnych platformach wewnętrznych, co skutkuje różnymi specyfikacjami:

|                          | **PolicyBased VPN Gateway** | **RouteBased VPN Gateway**       |**RouteBased VPN Gateway**                          |
| ---                      | ---                         | ---                              |---                                                 |
| **Jednostka SKU bramy platformy Azure**    | Podstawowa                       | Podstawowa                            | Standard, HighPerformance, VpnGw1, VpnGw2, VpnGw3  |
| **Wersja IKE**          | IKEv1                       | IKEv2                            | Protokoły IKEv1 i IKEv2                                    |
| **Maksymalny. Połączenia S2S** | **1**                       | 10                               |Standardowa: 10<br> Inne jednostki SKU: 30                     |
|                          |                             |                                  |                                                    |

Za pomocą niestandardowych zasad protokołu IPsec/IKE można teraz skonfigurować bramy sieci VPN oparte na trasach na podstawie prefiksów z opcją "**PolicyBasedTrafficSelectors**", aby połączyć się z lokalnymi urządzeniami sieci VPN opartymi na zasadach. Ta funkcja umożliwia łączenie z sieci wirtualnej platformy Azure i bramy sieci VPN z wieloma lokalnymi urządzeniami sieci VPN/zapory opartymi na zasadach, usuwając limit pojedynczego połączenia z bieżących bram sieci VPN opartych na zasadach platformy Azure.

> [!IMPORTANT]
> 1. Aby włączyć tę łączność, lokalne urządzenia sieci VPN oparte na zasadach muszą obsługiwać protokół **IKEv2** do łączenia się z bramami sieci VPN opartymi na trasach platformy Azure. Sprawdź specyfikacje urządzeń sieci VPN.
> 2. Sieci lokalne łączące się za pośrednictwem urządzeń sieci VPN opartych na zasadach z tym mechanizmem mogą łączyć się tylko z siecią wirtualną platformy Azure. **nie mogą oni przetransferować do innych sieci lokalnych lub sieci wirtualnych za pośrednictwem tej samej bramy sieci VPN platformy Azure**.
> 3. Opcja konfiguracji jest częścią niestandardowych zasad połączenia protokołu IPsec/IKE. W przypadku włączenia opcji selektora ruchu opartego na zasadach należy określić kompletną zasadę (algorytmy szyfrowania IPsec/IKE i integralności, mocne klucze i okresy istnienia skojarzeń zabezpieczeń).

Na poniższym diagramie przedstawiono, dlaczego Routing tranzytowy za pośrednictwem usługi Azure VPN Gateway nie działa z opcją opartą na zasadach:

![tranzyt oparty na zasadach](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Jak pokazano na diagramie, Brama sieci VPN platformy Azure ma selektory ruchu z sieci wirtualnej do każdego z prefiksów sieci lokalnych, ale nie prefiksów między połączeniami. Na przykład lokacja lokalna 2, lokacja 3 i witryna 4 mogą komunikować się odpowiednio VNet1, ale nie może łączyć się za pośrednictwem bramy sieci VPN platformy Azure ze sobą. Na diagramie przedstawiono selektory ruchu między połączeniami, które nie są dostępne w bramie sieci VPN platformy Azure w ramach tej konfiguracji.

## <a name="configurepolicybased"></a>Konfigurowanie selektorów ruchu opartych na zasadach dla połączenia

Instrukcje zawarte w tym artykule są zgodne z tym samym przykładem, zgodnie z opisem w artykule [Konfigurowanie zasad protokołu IPSec/IKE dla połączeń S2S lub Sieć wirtualna-sieć](vpn-gateway-ipsecikepolicy-rm-powershell.md) wirtualna w celu nawiązania połączenia sieci VPN S2S. Jest to pokazane na poniższym diagramie:

![S2S — zasady](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

Przepływ pracy umożliwiający włączenie tej łączności:
1. Utwórz sieć wirtualną, bramę sieci VPN i bramę sieci lokalnej dla połączenia obejmującego wiele lokalizacji
2. Tworzenie zasad protokołu IPsec/IKE
3. Zastosowanie zasad podczas tworzenia połączenia S2S lub komunikacji między sieciami wirtualnymi i **Włączanie selektorów ruchu opartych na zasadach** dla połączenia
4. Jeśli połączenie zostało już utworzone, możesz zastosować lub zaktualizować zasady do istniejącego połączenia

## <a name="before-you-begin"></a>Przed rozpoczęciem

Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enablepolicybased"></a>Włączanie selektorów ruchu opartych na zasadach dla połączenia

Upewnij się, że zakończono [część 3 artykułu Konfigurowanie zasad protokołu IPSec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) dla tej sekcji. W poniższym przykładzie zastosowano te same parametry i kroki:

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Krok 1. Tworzenie sieci wirtualnej, bramy sieci VPN i bramy sieci lokalnej

#### <a name="1-connect-to-your-subscription-and-declare-your-variables"></a>1. Połącz się z subskrypcją i Zadeklaruj zmienne

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

Zadeklaruj swoje zmienne. W tym ćwiczeniu używane są następujące zmienne:

```azurepowershell-interactive
$Sub1          = "<YourSubscriptionName>"
$RG1           = "TestPolicyRG1"
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
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GW1IPName1    = "VNet1GWIP1"
$GW1IPconf1    = "gw1ipconf1"
$Connection16  = "VNet1toSite6"

$LNGName6      = "Site6"
$LNGPrefix61   = "10.61.0.0/16"
$LNGPrefix62   = "10.62.0.0/16"
$LNGIP6        = "131.107.72.22"
```

#### <a name="2-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>2. Utwórz sieć wirtualną, bramę sieci VPN i bramę sieci lokalnej.

Utwórz grupę zasobów.

```azurepowershell-interactive
New-AzResourceGroup -Name $RG1 -Location $Location1
```

Użyj poniższego przykładu, aby utworzyć sieć wirtualną sieci testvnet1 z trzema podsieciami i bramą sieci VPN. Jeśli chcesz zastąpić wartości, pamiętaj, że zawsze należy nazwać podsieć bramy, szczególnie "GatewaySubnet". W przypadku nadania jej innej nazwy proces tworzenia bramy zakończy się niepowodzeniem.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance

New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a>Krok 2. Tworzenie połączenia sieci VPN S2S przy użyciu zasad protokołu IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Tworzenie zasad protokołu IPsec/IKE

> [!IMPORTANT]
> Aby włączyć opcję "UsePolicyBasedTrafficSelectors" dla połączenia, należy utworzyć zasady protokołu IPsec/IKE.

Poniższy przykład tworzy zasady protokołu IPsec/IKE z tymi algorytmami i parametrami:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS None, okres istnienia skojarzenia zabezpieczeń 14400 s & 102400000KB

```azurepowershell-interactive
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-the-s2s-vpn-connection-with-policy-based-traffic-selectors-and-ipsecike-policy"></a>2. Utwórz połączenie sieci VPN S2S przy użyciu selektorów ruchu opartego na zasadach i zasad protokołu IPsec/IKE
Utwórz połączenie sieci VPN S2S i Zastosuj zasady protokołu IPsec/IKE utworzone w poprzednim kroku. Należy pamiętać o dodatkowym parametrze "-UsePolicyBasedTrafficSelectors $True", który umożliwia wybór ruchu oparty na zasadach w połączeniu.

```azurepowershell-interactive
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Po wykonaniu tych kroków połączenie sieci VPN S2S będzie używać zdefiniowanych zasad protokołu IPsec/IKE i włącza selektory ruchu opartego na zasadach w połączeniu. Możesz powtórzyć te same czynności, aby dodać więcej połączeń do dodatkowych lokalnych urządzeń sieci VPN opartych na zasadach z tej samej bramy sieci VPN platformy Azure.

## <a name="update-policy-based-traffic-selectors-for-a-connection"></a>Aktualizowanie selektorów ruchu opartych na zasadach dla połączenia
W ostatniej sekcji pokazano, jak zaktualizować opcję selektorów ruchu opartych na zasadach dla istniejącego połączenia sieci VPN S2S.

### <a name="1-get-the-connection"></a>1. Pobierz połączenie
Pobierz zasób połączenia.

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
```

### <a name="2-check-the-policy-based-traffic-selectors-option"></a>2. Sprawdź opcję selektorów ruchu opartych na zasadach
W poniższym wierszu przedstawiono, czy dla połączenia są używane selektory ruchu opartego na zasadach:

```azurepowershell-interactive
$connection6.UsePolicyBasedTrafficSelectors
```

Jeśli wiersz zwraca wartość "**true**", to w ramach połączenia są konfigurowane selektory ruchu opartego na zasadach. w przeciwnym razie zwraca wartość "**false**".

### <a name="3-enabledisable-the-policy-based-traffic-selectors-on-a-connection"></a>3. Włącz/Wyłącz selektory ruchu opartego na zasadach dla połączenia
Po uzyskaniu zasobu połączenia można włączyć lub wyłączyć opcję.

#### <a name="to-enable-usepolicybasedtrafficselectors"></a>Aby włączyć UsePolicyBasedTrafficSelectors
Poniższy przykład włącza opcję selektorów ruchu opartych na zasadach, ale pozostawia zasady protokołu IPsec/IKE bez zmian:

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
```

#### <a name="to-disable-usepolicybasedtrafficselectors"></a>Aby wyłączyć UsePolicyBasedTrafficSelectors
Poniższy przykład wyłącza opcję selektorów ruchu opartych na zasadach, ale pozostawia zasady protokołu IPsec/IKE bez zmian:

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
```

## <a name="next-steps"></a>Następne kroki
Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Kroki opisano w sekcji [Tworzenie maszyny wirtualnej](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Zapoznaj się również z tematem [Konfigurowanie zasad protokołu IPSec/IKE dla połączeń sieci VPN S2S lub połączenia Sieć wirtualna-sieć wirtualna,](vpn-gateway-ipsecikepolicy-rm-powershell.md) Aby uzyskać szczegółowe informacje na temat niestandardowych zasad protokołu IPSec/IKE.
