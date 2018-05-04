---
title: 'Połączenie bramy sieci VPN platformy Azure do wielu urządzeń lokalnych, na podstawie zasad sieci VPN: usługi Azure Resource Manager: programu PowerShell | Dokumentacja firmy Microsoft'
description: Konfigurowanie usługi Azure opartej na trasach bramy sieci VPN do wielu opartych na zasadach urządzenia sieci VPN za pomocą usługi Azure Resource Manager i programu PowerShell.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: dc2dc660262cec892270f8d6e70691fdd169a5c4
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Połączenie bramy sieci VPN platformy Azure do wielu urządzeń lokalnych, na podstawie zasad sieci VPN przy użyciu programu PowerShell

W tym artykule opisano, jak skonfigurować opartej na trasach Brama sieci VPN do nawiązania połączenia wielu urządzeń lokalnych, na podstawie zasad sieci VPN korzystania z niestandardowych zasad IPsec i IKE połączeń sieci VPN S2S.

## <a name="about-policy-based-and-route-based-vpn-gateways"></a>Temat bram sieci VPN opartych na zasadach i opartej na trasach

Zasady - *a* opartej na trasach urządzenia sieci VPN różnią się w konfiguracji selektorów ruchu protokołu IPsec dla połączenia:

* **Oparte na zasadach** urządzenia sieci VPN przy użyciu kombinacji prefiksów z obu sieci, aby zdefiniować sposób ruch jest szyfrowany/odszyfrowywany przez tunel protokołu IPsec. Zazwyczaj jest tworzony na urządzeniach zapory, które wykonują filtrowanie pakietów. IPsec tunelu szyfrowania i odszyfrowywania są dodawane do filtrowania i aparat przetwarzania pakietu.
* **Oparte na trasach** urządzenia sieci VPN przy użyciu dowolnego z każdym (symbol wieloznaczny) ruchu selektorów, a let routingu/przekazywania ruchu bezpośredniego tabel do różnych tuneli protokołu IPsec. Zazwyczaj jest tworzony na platformach routera, gdzie każdy tunelu IPsec ma formę interfejsu sieciowego lub VTI (interfejsu tunelu wirtualnej).

Poniższych diagramach wyróżnić dwa modele:

### <a name="policy-based-vpn-example"></a>Przykład sieci VPN opartych na zasadach
![oparte na zasadach](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Przykład sieci VPN opartej na trasach
![oparte na trasach](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Pomocy technicznej platformy Azure dla sieci VPN opartych na zasadach
Obecnie usługa Azure obsługuje oba tryby bramy sieci VPN: opartej na trasach bramy sieci VPN i bramy sieci VPN opartych na zasadach. Zostały one utworzone na różnych platformach wewnętrznego, które powodują różne specyfikacje:

|                          | **PolicyBased VPN Gateway** | **Brama sieci VPN z siecią typu RouteBased**               |
| ---                      | ---                         | ---                                      |
| **Jednostka SKU bramy Azure**    | Podstawowa                       | Basic, Standard, wysokowydajnej, VpnGw1, VpnGw2, VpnGw3 |
| **Wersja IKE**          | IKEv1                       | IKEv2                                    |
| **Maks. Połączeń S2S** | **1**                       | Basic/Standard: 10<br> Wysokowydajnej: 30 |
|                          |                             |                                          |

Niestandardowe zasady IPsec i IKE, można teraz skonfigurować Azure bramy sieci VPN opartej na trasach do ruchu na podstawie prefiksu selektorów za pomocą opcji "**PolicyBasedTrafficSelectors**", aby nawiązać połączenie urządzenia sieci VPN na podstawie zasad lokalnych. Ta funkcja umożliwia nawiązanie połączenia z sieci wirtualnej platformy Azure i Brama sieci VPN z wieloma lokalnie na podstawie zasad urządzenia sieci VPN/zapory usuwanie limit pojedynczego połączenia z bieżącej platformy Azure na podstawie zasad bramy sieci VPN.

> [!IMPORTANT]
> 1. Aby włączyć to połączenie, musi obsługiwać lokalnego urządzenia sieci VPN opartych na zasadach **IKEv2** do nawiązania połączenia platformy Azure bramy sieci VPN opartej na trasach. Sprawdź specyfikację urządzenia sieci VPN.
> 2. Sieciach lokalnych, łączących się za pośrednictwem urządzenia sieci VPN opartych na zasadach z tym mechanizm mogą łączyć się tylko z sieci wirtualnej platformy Azure; **nie można ich przesyłania do innych sieci lokalnej lub sieci wirtualnych za pomocą tej samej bramy sieci VPN platformy Azure**.
> 3. Opcja konfiguracji jest częścią niestandardowe zasady IPsec i IKE połączenia. Po włączeniu opcji selektor ruchu na podstawie zasad, należy określić pełną zasad (algorytmów szyfrowania i integralności IPsec i IKE, silnych kluczy i okresy istnienia SA).

Na poniższym diagramie przedstawiono Dlaczego routing tranzytowy za pośrednictwem bramy sieci VPN platformy Azure nie działa z opcją opartych na zasadach:

![przesyłania opartych na zasadach](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Jak pokazano na rysunku, Brama sieci VPN platformy Azure ma selektorów ruch z sieci wirtualnej wszystkie prefiksy sieci lokalnej, ale nie prefiksy połączenia między. Na przykład lokalnej lokacji 2, lokacja 3 i 4 lokacji można każdego komunikują się VNet1 odpowiednio, ale nie można połączyć za pośrednictwem bramy sieci VPN platformy Azure do siebie. Na diagramie przedstawiono selektorów ruchu cross-connect, które nie są dostępne w bramie sieci VPN platformy Azure zgodnie z tą konfiguracją.

## <a name="configure-policy-based-traffic-selectors-on-a-connection"></a>Konfigurowanie połączenia selektorów ruchu na podstawie zasad

Instrukcje w tym artykule wykonaj tym samym przykładzie, zgodnie z opisem w [zasady IPsec/IKE skonfigurować dla połączeń S2S lub do wirtualnymi](vpn-gateway-ipsecikepolicy-rm-powershell.md) do nawiązywania połączenia S2S VPN. Jest to przedstawione na poniższym diagramie:

![zasady s2s](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

Przepływ pracy łączność ta:
1. Tworzenie sieci wirtualnej, Brama sieci VPN i bramy sieci lokalnej dla połączenia między lokalizacjami
2. Tworzenie zasad IPsec i IKE
3. Stosowanie zasad podczas tworzenia połączenia S2S lub do wirtualnymi i **włączyć selektorów ruchu na podstawie zasad** połączenia
4. Jeśli połączenie jest już utworzony, można zastosować lub zaktualizuj zasady do istniejącego połączenia

## <a name="enable-policy-based-traffic-selectors-on-a-connection"></a>Włącz selektorów opartych na zasadach ruchu dla połączenia

Upewnij się, że zostały wykonane [część 3 artykułu zasad IPsec/IKE skonfigurować](vpn-gateway-ipsecikepolicy-rm-powershell.md) dla tej sekcji. W poniższym przykładzie użyto te same parametry i kroki:

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Krok 1 — Tworzenie sieci wirtualnej, Brama sieci VPN i bramy sieci lokalnej

#### <a name="1-declare-your-variables--connect-to-your-subscription"></a>1. Deklarowanie zmiennych & nawiązać połączenia z subskrypcją
W tym ćwiczeniu Rozpoczniemy przez zadeklarowanie naszych zmiennych. Podczas konfigurowania produktu należy pamiętać o zastąpieniu ich odpowiednimi wartościami.

```powershell
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
Aby używać poleceń cmdlet Menedżera zasobów, upewnij się, że można przełączyć do trybu programu PowerShell. Więcej informacji znajduje się w temacie [Using Windows PowerShell with Resource Manager](../powershell-azure-resource-manager.md) (Używanie programu Windows PowerShell z usługą Resource Manager).

Otwórz konsolę programu PowerShell i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="2-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>2. Tworzenie sieci wirtualnej, Brama sieci VPN i bramy sieci lokalnej
Poniższy przykład tworzy sieć wirtualną, TestVNet1 z trzech podsieci oraz bramy sieci VPN. Zastępowanie wartości, jest ważne, aby zawsze nazwę podsieci bramy w szczególności "GatewaySubnet". W przypadku nadania jej innej nazwy proces tworzenia bramy zakończy się niepowodzeniem.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a>Krok 2 — Tworzenie połączenia sieci VPN S2S przy użyciu zasad IPsec i IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Tworzenie zasad IPsec i IKE

> [!IMPORTANT]
> Należy utworzyć zasady IPsec i IKE, aby włączyć opcję "UsePolicyBasedTrafficSelectors" w połączeniu.

Poniższy przykład tworzy zasady IPsec i IKE z te algorytmy i parametry:
* IKEv2: AES256, SHA384, DHGroup24
* Protokół IPsec: AES256, SHA256, PFS24, skojarzenia zabezpieczeń okres istnienia 3600 s & 2048KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup PFS24 -SALifeTimeSeconds 3600 -SADataSizeKilobytes 2048
```

#### <a name="2-create-the-s2s-vpn-connection-with-policy-based-traffic-selectors-and-ipsecike-policy"></a>2. Utworzyć połączenie sieci VPN S2S za pomocą zasad IPsec i IKE i selektorów ruchu na podstawie zasad
Tworzenie połączenia sieci VPN S2S i stosowanie zasad IPsec i IKE utworzony w poprzednim kroku. Należy pamiętać o dodatkowy parametr "-UsePolicyBasedTrafficSelectors $True" co pozwala selektorów ruchu na podstawie zasad połączenia.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Po wykonaniu tych kroków, połączenia sieci VPN S2S spowoduje za pomocą zasad IPsec i IKE zdefiniowany i włączenie selektorów ruchu na podstawie zasad połączenia. Można powtórzyć te same kroki, aby dodać więcej połączeń do urządzenia sieci VPN opartych na zasadach dodatkowych lokalnych z tej samej bramy sieci VPN platformy Azure.

## <a name="update-policy-based-traffic-selectors-for-a-connection"></a>Zaktualizuj selektorów opartych na zasadach ruchu dla połączenia
W ostatniej sekcji widoczne, jak zaktualizować opcję selektorów ruchu na podstawie zasad dla istniejącego połączenia sieci VPN S2S.

### <a name="1-get-the-connection"></a>1. Pobierz dane o połączeniu
Pobierz zasób połączenia.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
```

### <a name="2-check-the-policy-based-traffic-selectors-option"></a>2. Zaznacz opcję selektorów ruchu na podstawie zasad
Następujący wiersz pokazuje, czy selektorów opartych na zasadach ruchu są używane do połączenia:

```powershell
$connection6.UsePolicyBasedTrafficSelectors
```

Jeśli wiersz zwraca "**True**", następnie selektorów ruchu na podstawie zasad są skonfigurowane w ramach połączenia; w przeciwnym razie zwraca wartość "**False**."

### <a name="3-update-the-policy-based-traffic-selectors-on-a-connection"></a>3. Zaktualizuj selektorów opartych na zasadach ruchu dla połączenia
Po uzyskaniu zasobu połączenia, można włączyć lub wyłączyć opcję.

#### <a name="disable-usepolicybasedtrafficselectors"></a>Wyłącz UsePolicyBasedTrafficSelectors
Poniższy przykład wyłącza opcję selektorów ruchu na podstawie zasad, ale pozostawia zasad IPsec i IKE bez zmian:

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
```

#### <a name="enable-usepolicybasedtrafficselectors"></a>Włącz UsePolicyBasedTrafficSelectors
Poniższy przykład włącza opcję selektorów ruchu na podstawie zasad, ale pozostawia zasad IPsec i IKE bez zmian:

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
```

## <a name="next-steps"></a>Kolejne kroki
Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Kroki opisano w sekcji [Tworzenie maszyny wirtualnej](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Sprawdź również [zasady IPsec/IKE skonfigurować dla połączeń sieci VPN S2S lub wirtualnymi do](vpn-gateway-ipsecikepolicy-rm-powershell.md) uzyskać więcej informacji dotyczących zasad protokołu IPsec/IKE niestandardowych.
