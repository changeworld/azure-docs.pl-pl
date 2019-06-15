---
title: 'Połączenie bramy sieci VPN platformy Azure do wielu lokalnych na podstawie zasad urządzeń sieci VPN: Azure Resource Manager: Program PowerShell | Dokumentacja firmy Microsoft'
description: Konfigurowanie sieci VPN bramy oparte na trasach platformy Azure do wielu oparte na zasadach urządzeń sieci VPN przy użyciu usługi Azure Resource Manager i programu PowerShell.
services: vpn-gateway
documentationcenter: na
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: yushwang
ms.openlocfilehash: 9085d5ee21b1e955b7d9416a379ee730ba26ad3e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66150084"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Łączenie bramy sieci VPN platformy Azure do wielu lokalnych na podstawie zasad urządzeń sieci VPN przy użyciu programu PowerShell

Ten artykuł ułatwia konfigurowanie sieci VPN bramy oparte na trasach Azure połączyć się z wielu lokalnych na podstawie zasad urządzeń sieci VPN korzystanie z niestandardowych zasad protokołu IPsec/IKE dla połączeń sieci VPN S2S.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about"></a>Temat bram sieci VPN oparte na zasadach i na trasach

Zasady — *a* urządzenia sieci VPN opartej na trasach różnią się w konfiguracji protokołu IPsec selektorów ruchu dla połączenia:

* **Oparte na zasadach** urządzenia sieci VPN przy użyciu kombinacji prefiksów w obu sieciach do zdefiniowania, jak ruch jest szyfrowany/odszyfrować za pośrednictwem tuneli IPsec. Zazwyczaj jest on oparty na zaporach, wykonujących filtrowanie pakietów. Szyfrowanie tunelu IPsec i odszyfrowywania są dodawane do pakietu, filtrowanie i aparat przetwarzania.
* **Oparta na trasach** urządzenia sieci VPN za pomocą selektorów ruchu dowolna dowolna (symbol wieloznaczny), a także umożliwić routing/przekazywania tabel bezpośrednie kierowanie ruchu do różnych tuneli protokołu IPsec. Zazwyczaj jest on oparty na platformach routera, gdzie każdego tunelu IPsec formę interfejsu sieciowego lub VTI (interfejsu tunelu wirtualnej).

Następujące diagramy zaznacz dwa modele:

### <a name="policy-based-vpn-example"></a>Przykład sieci VPN oparte na zasadach
![oparte na zasadach](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Przykład sieci VPN opartej na trasach
![oparta na trasach](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Pomoc techniczna platformy Azure dla sieci VPN opartej na zasadach
Obecnie platforma Azure obsługuje oba tryby bram sieci VPN: oparta na trasach bram sieci VPN i bramami sieci VPN oparte na zasadach. Zostały one utworzone na różnych platformach wewnętrznych, które powodują różne specyfikacje:

|                          | **PolicyBased VPN Gateway** | **RouteBased VPN Gateway**               |
| ---                      | ---                         | ---                                      |
| **Jednostka SKU bramy platformy Azure**    | Podstawowa                       | Basic, Standard, HighPerformance, VpnGw1, VpnGw2 i VpnGw3 |
| **Wersja IKE**          | IKEv1                       | IKEv2                                    |
| **Maksymalna liczba. Połączeń S2S** | **1**                       | Podstawowa/standardowa: 10<br> HighPerformance: 30 |
|                          |                             |                                          |

Niestandardowe zasady protokołu IPsec/IKE, możesz teraz skonfigurować Azure bram sieci VPN opartej na trasach selektorów ruchu na podstawie prefiksu za pomocą opcji "**PolicyBasedTrafficSelectors**", aby nawiązać połączenie z urządzeń sieci VPN oparte na zasadach w środowisku lokalnym. Ta funkcja pozwala nawiązać połączenie z siecią wirtualną platformy Azure i bramy sieci VPN z wieloma lokalnymi oparte na zasadach sieci VPN/Zapora urządzeniami, usuwając limit jednego połączenia z bieżącej platformy Azure na podstawie zasad bramy VPN Gateway.

> [!IMPORTANT]
> 1. Aby włączyć to połączenie, musi obsługiwać lokalnych urządzeń sieci VPN oparte na zasadach **IKEv2** nawiązać połączenia z programem Azure bram sieci VPN opartej na trasach. Sprawdź specyfikację urządzenia sieci VPN.
> 2. Sieciami lokalnymi, łącząc się za pośrednictwem urządzenia sieci VPN oparte na zasadach przy użyciu tego mechanizmu można połączyć tylko z sieci wirtualnej platformy Azure; **one nie można przesłać do innych sieci lokalnej lub sieci wirtualnych za pomocą tej samej bramy sieci VPN platformy Azure**.
> 3. Opcja konfiguracji jest częścią zasady niestandardowe połączenia protokołu IPsec/IKE. Jeśli opcja selektor ruchu na podstawie zasad jest włączona, należy określić pełną zasad (algorytmy integralności i szyfrowania IPsec/IKE, siły klucza i okresy istnienia skojarzeń zabezpieczeń).

Na poniższym diagramie przedstawiono Dlaczego routing tranzytowy za pośrednictwem bramy sieci VPN platformy Azure nie działa z opcją oparte na zasadach:

![tranzyt oparte na zasadach](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Jak pokazano na diagramie, bramy sieci VPN platformy Azure ma selektorów ruchu z sieci wirtualnej do każdego z prefiksy sieci lokalnej, ale nie prefiksy obejmującej wiele połączeń. Na przykład lokacji lokalnej 2, witryna 3 i 4 lokacji może każdego komunikować się do sieci VNet1 odpowiednio, ale nie można połączyć za pośrednictwem bramy sieci VPN platformy Azure ze sobą. Na diagramie przedstawiono cross-connect selektorów ruchu, które nie są dostępne w przypadku bramy sieci VPN platformy Azure w ramach tej konfiguracji.

## <a name="configurepolicybased"></a>Konfigurowanie opartych na zasadach selektorów ruchu dla połączenia

Instrukcje w tym artykule postępuj zgodnie z tym samym przykładzie zgodnie z opisem w [zasady konfigurowania protokołu IPsec/IKE dla połączeń S2S lub sieć wirtualna-sieć wirtualna](vpn-gateway-ipsecikepolicy-rm-powershell.md) do nawiązania połączenia sieci VPN S2S. Jest to pokazane na poniższym diagramie:

![zasady s2s](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

Przepływ pracy, aby włączyć łączność ta:
1. Tworzenie sieci wirtualnej, bramy sieci VPN i bramy sieci lokalnej w celu nawiązania połączenia między środowiskami lokalnymi
2. Tworzenie zasad protokołu IPsec/IKE
3. Zastosuj zasady, podczas tworzenia połączenia S2S lub sieć wirtualna-sieć wirtualna i **Włącz selektorów ruchu na podstawie zasad** połączenia
4. Jeśli połączenie jest już utworzony, można zastosować lub zaktualizować zasady do istniejącego połączenia

## <a name="before-you-begin"></a>Przed rozpoczęciem

Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enablepolicybased"></a>Włącz ruch oparte na zasadach selektorów połączenia

Upewnij się, że zostały wykonane [część 3 artykułu zasad protokołu IPsec/IKE skonfigurować](vpn-gateway-ipsecikepolicy-rm-powershell.md) dla tej sekcji. W poniższym przykładzie użyto tych samych parametrów i kroki:

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Krok 1 — Tworzenie sieci wirtualnej, bramy sieci VPN i bramy sieci lokalnej

#### <a name="1-connect-to-your-subscription-and-declare-your-variables"></a>1. Połącz z subskrypcją i Zadeklaruj swoje zmienne

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

Zadeklaruj swoje zmienne. Na potrzeby tego ćwiczenia Stosujemy następujące zmienne:

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

#### <a name="2-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>2. Tworzenie sieci wirtualnej, bramy sieci VPN i bramy sieci lokalnej

Utwórz grupę zasobów.

```azurepowershell-interactive
New-AzResourceGroup -Name $RG1 -Location $Location1
```

Poniższy przykład umożliwia utworzenie sieci wirtualnej TestVNet1 z trzech podsieci i bramę VPN. Zastąp wartości jest ważne, że należy zawsze nazywać podsieć bramy specjalnie "GatewaySubnet". W przypadku nadania jej innej nazwy proces tworzenia bramy zakończy się niepowodzeniem.

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

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a>Krok 2 — Tworzenie połączenia sieci VPN S2S przy użyciu zasad protokołu IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Tworzenie zasad protokołu IPsec/IKE

> [!IMPORTANT]
> Należy utworzyć zasad protokołu IPsec/IKE, aby włączyć opcję "UsePolicyBasedTrafficSelectors" w połączeniu.

Poniższy przykład tworzy zasady protokołu IPsec/IKE za pomocą tych algorytmów i parametrów:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS None, sekund 14400 okres istnienia skojarzeń zabezpieczeń i 102400000KB

```azurepowershell-interactive
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-the-s2s-vpn-connection-with-policy-based-traffic-selectors-and-ipsecike-policy"></a>2. Utwórz połączenie sieci VPN S2S z opartych na zasadach selektorów ruchu i zasady protokołu IPsec/IKE
Tworzenie połączenia sieci VPN S2S i stosowanie zasad IPsec/IKE, utworzony w poprzednim kroku. Należy pamiętać o dodatkowy parametr "-UsePolicyBasedTrafficSelectors $True" umożliwiająca selektorów oparte na zasadach ruchu dla połączenia.

```azurepowershell-interactive
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Po wykonaniu tych kroków, połączenia sieci VPN S2S będzie zasad protokołu IPsec/IKE, zdefiniowanych oraz obsługę ruchu oparte na zasadach selektorów w połączeniu. Możesz powtórzyć te same kroki, aby dodać więcej połączeń do urządzenia sieci VPN oparte na zasadach dodatkowe środowiska lokalnego z tą samą bramą sieci VPN platformy Azure.

## <a name="update-policy-based-traffic-selectors-for-a-connection"></a>Aktualizuj selektorów ruchu na podstawie zasad dla połączenia
Ostatnia sekcja pokazuje, jak zaktualizować opcji selektory ruchu na podstawie zasad dla istniejącego połączenia sieci VPN S2S.

### <a name="1-get-the-connection"></a>1. Uzyskaj połączenie
Pobierz zasób połączenia.

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
```

### <a name="2-check-the-policy-based-traffic-selectors-option"></a>2. Zaznacz opcję selektory ruchu na podstawie zasad
Następujący wiersz pokazuje, czy selektorów ruchu oparte na zasadach są używane do połączenia:

```azurepowershell-interactive
$connection6.UsePolicyBasedTrafficSelectors
```

Jeśli wiersz zwraca "**True**", następnie opartych na zasadach selektorów ruchu są skonfigurowane w ramach połączenia; w przeciwnym razie zwraca "**False**."

### <a name="3-enabledisable-the-policy-based-traffic-selectors-on-a-connection"></a>3. Włącz/Wyłącz selektory ruchu na podstawie zasad połączenia
Po uzyskaniu zasobu połączenia, można włączyć lub wyłączyć opcję.

#### <a name="to-enable-usepolicybasedtrafficselectors"></a>To Enable UsePolicyBasedTrafficSelectors
Poniższy przykład włącza opcję selektory ruchu na podstawie zasad, ale pozostawia zasad protokołu IPsec/IKE, bez zmian:

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
```

#### <a name="to-disable-usepolicybasedtrafficselectors"></a>To Disable UsePolicyBasedTrafficSelectors
Poniższy przykład wyłącza opcję selektory ruchu na podstawie zasad, ale pozostawia zasad protokołu IPsec/IKE, bez zmian:

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
```

## <a name="next-steps"></a>Kolejne kroki
Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Kroki opisano w sekcji [Tworzenie maszyny wirtualnej](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Sprawdź również [zasady konfigurowania protokołu IPsec/IKE dla połączeń S2S VPN lub sieć wirtualna-sieć wirtualna](vpn-gateway-ipsecikepolicy-rm-powershell.md) dodatkowe szczegóły dotyczące niestandardowych zasad protokołu IPsec/IKE.
