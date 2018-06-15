---
title: 'Konfigurowanie zasad IPsec i IKE dla połączeń sieci VPN S2S lub do wirtualnymi: usługi Azure Resource Manager: programu PowerShell | Dokumentacja firmy Microsoft'
description: Skonfiguruj zasady IPsec i IKE dla połączeń S2S lub do wirtualnymi z bramy sieci VPN platformy Azure przy użyciu usługi Azure Resource Manager i programu PowerShell.
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
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: fa1aed76f63e500a6c2849fb9b62a918e85c9fb0
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
ms.locfileid: "31601155"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>Konfigurowanie zasad IPsec i IKE dla połączeń S2S sieci VPN lub sieć wirtualną do sieci wirtualnej

W tym artykule przedstawiono kroki, aby skonfigurować zasady IPsec/IKE dla połączeń VPN lokacja-lokacja lub do wirtualnymi przy użyciu modelu wdrażania usługi Resource Manager i programu PowerShell.

## <a name="about"></a>Parametry zasad IPsec i IKE dla bram sieci VPN platformy Azure — informacje
Standardowego protokołu IPsec i IKE obsługuje szeroką gamę algorytmów kryptograficznych w różnych kombinacjach. Zapoznaj się [temat wymagań usług kryptograficznych i bram sieci VPN platformy Azure](vpn-gateway-about-compliance-crypto.md) aby zobaczyć, jak może to pomóc w sprawdzeniu między lokalizacjami i łączności do wirtualnymi spełniają wymagania zgodności i zabezpieczeń.

Ten artykuł zawiera instrukcje dotyczące tworzenia i konfigurowania zasad IPsec i IKE i zastosować do nowego lub istniejącego połączenia:

* [Część 1 - przepływu pracy, aby utworzyć i ustawić zasady IPsec/IKE](#workflow)
* [Część 2 - obsługiwanych algorytmów kryptograficznych i kluczy sile](#params)
* [Część 3 — Utwórz nowe połączenie sieci VPN S2S z zasadami IPsec i IKE](#crossprem)
* [Część 4 — Utwórz nowe połączenie do wirtualnymi z zasadami IPsec i IKE](#vnet2vnet)
* [Część 5 - zarządzania (Tworzenie, Dodaj i Usuń) zasady IPsec i IKE połączenia](#managepolicy)

> [!IMPORTANT]
> 1. Należy pamiętać, że zasady IPsec/IKE działa tylko na następujące jednostki SKU bramy:
>    * ***VpnGw3 VpnGw1, VpnGw2,*** (opartej na trasach)
>    * ***Standardowe*** i ***wysokowydajnej*** (opartej na trasach)
> 2. Można określić tylko ***jedną*** kombinację zasad dla danego połączenia.
> 3. Należy określić wszystkie algorytmy i parametry IKE (tryb główny) i IPsec (trybu szybkiego). Określenie zasad częściowych nie jest dozwolone.
> 4. Zapoznaj się ze specyfikacjami dostawcy urządzenia sieci VPN, tak aby upewnić się, że zasady jest obsługiwana na urządzeniach lokalnych sieci VPN. S2S lub połączeń do wirtualnymi nie można ustanowić, jeśli zasady są niezgodne.

## <a name ="workflow"></a>Część 1 - przepływu pracy, aby utworzyć i ustawić zasady IPsec/IKE
W tej sekcji przedstawiono przepływ pracy do tworzenia i aktualizowania zasad IPsec i IKE połączenia sieci VPN S2S lub wirtualnymi do:
1. Tworzenie sieci wirtualnej i bramy VPN Gateway
2. Tworzenie bramy sieci lokalnej dla krzyżyk lokalne połączenie lub innej sieci wirtualnej i brama dla połączenia do wirtualnymi
3. Utwórz zasady IPsec i IKE z wybranych algorytmów i parametry
4. Utworzyć połączenie (protokołu IPsec lub VNet2VNet) za pomocą zasad IPsec i IKE
5. Dodawania/aktualizacji/usuwania zasad IPsec i IKE dla istniejącego połączenia

Instrukcje w tym artykule pomaga utworzyć i skonfigurować zasady IPsec i IKE, jak pokazano na diagramie:

![ipsec-ike-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name ="params"></a>Część 2 - obsługiwane algorytmy kryptograficzne & sile klucza

W poniższej tabeli wymieniono obsługiwane algorytmów kryptograficznych i kluczy sile można skonfigurować przez klientów:

| **IPsec/IKEv2**  | **Opcje**    |
| ---  | --- 
| Szyfrowanie IKEv2 | AES256, AES192, AES128, DES3, DES  
| Integralność IKEv2  | SHA384, SHA256, SHA1, MD5  |
| Grupa DH         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, None |
| Szyfrowanie IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Brak    |
| Integralność IPsec  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| Grupa PFS        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Brak 
| Okres istnienia skojarzeń zabezpieczeń QM   | (**Opcjonalnie**: wartości domyślne są używane jeśli nie została określona)<br>Sekundy (liczba całkowita; **min. 300**/wartość domyślna 27 000 sekund)<br>KB (liczba całkowita; **min. 1024**/wartość domyślna to 102 400 000 KB)   |
| Selektor ruchu | UsePolicyBasedTrafficSelectors ** ($True/$False; **Opcjonalnie**, domyślna $False Jeśli nie została określona)    |
|  |  |

> [!IMPORTANT]
> 1. **Konfiguracji urządzenia sieci VPN między lokalnymi muszą być zgodne i zawierają następujące algorytmów i parametrów określających zasady Azure IPsec/IKE:**
>    * Algorytm szyfrowania IKE (trybu głównego / fazy 1)
>    * Algorytm integralności IKE (trybu głównego / fazy 1)
>    * Grupa DH (trybu głównego / faza 1)
>    * Algorytm szyfrowania IPsec (trybu szybkiego / fazy 2)
>    * Algorytm integralności protokołu IPsec (trybu szybkiego / fazy 2)
>    * Grupy doskonałego utajnienia przekazywania (trybu szybkiego / fazy 2)
>    * Ruch selektora (jeśli jest używany UsePolicyBasedTrafficSelectors)
>    * Okresy istnienia skojarzenia zabezpieczeń są tylko specyfikacjami lokalnymi i nie muszą być zgodne.
>
> 2. **Jeśli GCMAES jest używany jak w przypadku algorytmu szyfrowania IPsec, musisz wybrać samego algorytmu GCMAES i długości kluczy dla protokołu IPsec integralności; na przykład za pomocą GCMAES128 zarówno dla**
> 3. W powyższej tabeli:
>    * Protokół IKEv2 odpowiada trybu głównego lub fazy 1
>    * Protokół IPsec odpowiada trybu szybkiego lub faza 2
>    * Grupa DH określa grupę Diffie-Hellmen używane w trybie głównym lub fazy 1
>    * Określona Grupa PFS grupy Diffie'ego-Hellmen używane w trybie szybkim lub fazy 2
> 4. Okres istnienia skojarzenia zabezpieczeń trybu głównego protokołu IKEv2 jest ustalony na 28 800 sekund na bramach sieci VPN platformy Azure
> 5. Ustawienie "UsePolicyBasedTrafficSelectors" $true połączenia skonfiguruje bramy sieci VPN platformy Azure do nawiązania połączenia na podstawie zasad zapory VPN lokalnie. Po włączeniu PolicyBasedTrafficSelectors należy upewnić się, że urządzenie sieci VPN zawiera pasującego selektorów ruchu zdefiniowane z wszystkich kombinacji prefiksów (bramy sieci lokalnej) sieci lokalnej, tak z prefiksami sieci wirtualnej platformy Azure, zamiast dowolny z każdym. Na przykład jeśli prefiksy sieci lokalnej to 10.1.0.0/16 i 10.2.0.0/16, a prefiksy sieci wirtualnej to 192.168.0.0/16 i 172.16.0.0/16, trzeba określić następujące selektory ruchu:
>    * 10.1.0.0/16 <====> 192.168.0.0/16
>    * 10.1.0.0/16 <====> 172.16.0.0/16
>    * 10.2.0.0/16 <====> 192.168.0.0/16
>    * 10.2.0.0/16 <====> 172.16.0.0/16

Aby uzyskać więcej informacji dotyczących selektorów ruchu na podstawie zasad, zobacz [połączyć wiele urządzeń lokalnych, na podstawie zasad sieci VPN](vpn-gateway-connect-multiple-policybased-rm-ps.md).

W poniższej tabeli przedstawiono odpowiednie grupy Diffie-Hellmana obsługiwanych przez zasady niestandardowe:

| **Grupa Diffie’ego-Hellmana**  | **DHGroup**              | **PFSGroup** | **Długość klucza** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | MODP, 768-bitowy   |
| 2                         | DHGroup2                 | PFS2         | MODP, 1024-bitowy  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | MODP, 2048-bitowy  |
| 19                        | ECP256                   | ECP256       | ECP, 256-bitowy    |
| 20                        | ECP384                   | ECP284       | ECP, 384-bitowy    |
| 24                        | DHGroup24                | PFS24        | MODP, 2048-bitowy  |

Więcej informacji można znaleźć w artykułach [RFC3526](https://tools.ietf.org/html/rfc3526) i [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name ="crossprem"></a>Część 3 — Utwórz nowe połączenie sieci VPN S2S z zasadami IPsec i IKE

Ta sekcja przeprowadzi Cię przez kroki tworzenia połączenia sieci VPN S2S przy użyciu zasad IPsec i IKE. Poniższe kroki utworzenia połączenia, jak pokazano na diagramie:

![zasady s2s](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

Zobacz [Tworzenie połączenia sieci VPN S2S](vpn-gateway-create-site-to-site-rm-powershell.md) Aby uzyskać szczegółowe instrukcje krok po kroku dotyczące tworzenia połączenia sieci VPN S2S.

### <a name="before"></a>Przed rozpoczęciem

* Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
* Zainstaluj polecenia cmdlet programu PowerShell usługi Azure Resource Manager. Zobacz [Omówienie programu Azure PowerShell](/powershell/azure/overview) Aby uzyskać więcej informacji na temat instalacji poleceń cmdlet programu PowerShell.

### <a name="createvnet1"></a>Krok 1 — Tworzenie sieci wirtualnej, Brama sieci VPN i bramy sieci lokalnej

#### <a name="1-declare-your-variables"></a>1. Zadeklarowanie zmiennych

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

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Połączyć subskrypcję i Utwórz nową grupę zasobów

Upewnij się, że program PowerShell został przełączony do trybu umożliwiającego korzystanie z poleceń cmdlet usługi Resource Manager. Więcej informacji znajduje się w temacie [Using Windows PowerShell with Resource Manager](../powershell-azure-resource-manager.md) (Używanie programu Windows PowerShell z usługą Resource Manager).

Otwórz konsolę programu PowerShell i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Tworzenie sieci wirtualnej, Brama sieci VPN i bramy sieci lokalnej

W poniższym przykładzie tworzone sieci wirtualnej i TestVNet1 z trzech podsieci bramy sieci VPN. Podczas zastępowania wartości ważne jest, aby podsieć bramy zawsze nosiła nazwę GatewaySubnet. W przypadku nadania jej innej nazwy proces tworzenia bramy zakończy się niepowodzeniem.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="s2sconnection"></a>Krok 2 — Tworzenie połączenia sieci VPN S2S przy użyciu zasad IPsec i IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Tworzenie zasad IPsec i IKE

Poniższy przykładowy skrypt tworzy zasady IPsec i IKE o parametrach i następujących algorytmów:

* IKEv2: AES256, SHA384, DHGroup24
* Protokół IPsec: AES256, SHA256, PFS None, sekund 14400 okres istnienia SA & 102400000KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Jeśli używasz GCMAES dla protokołu IPsec, możesz korzystać samego algorytmu GCMAES i długości kluczy do szyfrowania IPsec i integralności. Na przykład powyżej, będzie odpowiednich parametrów "-IpsecEncryption GCMAES256 - IpsecIntegrity GCMAES256" przy użyciu GCMAES256.

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2. Utworzyć połączenie sieci VPN S2S za pomocą zasad IPsec i IKE

Tworzenie połączenia sieci VPN S2S i stosowanie zasad IPsec i IKE utworzony wcześniej.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Opcjonalnie można dodać "-UsePolicyBasedTrafficSelectors $True" do tworzenia połączenia polecenie cmdlet, aby włączyć bramy sieci VPN platformy Azure do nawiązania połączenia na podstawie zasad urządzenia sieci VPN lokalnie, jak opisano powyżej.

> [!IMPORTANT]
> Gdy zasady IPsec i IKE jest określony dla połączenia, bramy sieci VPN platformy Azure tylko wysłać lub Zaakceptuj propozycję IPsec i IKE z określonym algorytmów kryptograficznych i kluczy sile dla tego konkretnego połączenia. Upewnij się, lokalnego urządzenia sieci VPN dla połączenia używa lub akceptuje kombinacji dokładne zasady, w przeciwnym razie tunel S2S VPN nie zostanie nawiązane.


## <a name ="vnet2vnet"></a>Część 4 — Utwórz nowe połączenie do wirtualnymi z zasadami IPsec i IKE

Kroki tworzenia połączenia do wirtualnymi przy użyciu zasad IPsec i IKE są podobne do tego połączenia sieci VPN S2S. Poniższe przykładowe skrypty utworzenia połączenia, jak pokazano na diagramie:

![v2v zasad](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

Zobacz [utworzyć połączenie do wirtualnymi](vpn-gateway-vnet-vnet-rm-ps.md) bardziej szczegółowe kroki tworzenia połączenia do wirtualnymi. Należy wykonać [część 3](#crossprem) do tworzenia i konfigurowania TestVNet1 i bramy sieci VPN.

### <a name="createvnet2"></a>Krok 1 — Tworzenie drugiej sieci wirtualnej i Brama sieci VPN

#### <a name="1-declare-your-variables"></a>1. Zadeklarowanie zmiennych

Należy pamiętać o zastąpieniu przykładowych wartości tymi, które mają zostać użyte w danej konfiguracji.

```powershell
$RG2          = "TestPolicyRG2"
$Location2    = "East US 2"
$VNetName2    = "TestVNet2"
$FESubName2   = "FrontEnd"
$BESubName2   = "Backend"
$GWSubName2   = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$DNS2         = "8.8.8.8"
$GWName2      = "VNet2GW"
$GW2IPName1   = "VNet2GWIP1"
$GW2IPconf1   = "gw2ipconf1"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-the-second-virtual-network-and-vpn-gateway-in-the-new-resource-group"></a>2. Tworzenie drugiej sieci wirtualnej i Brama sieci VPN w nowej grupy zasobów

```powershell
New-AzureRmResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

$gw2pip1    = New-AzureRmPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$vnet2      = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1

New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
```

### <a name="step-2---create-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>Krok 2 — Tworzenie połączenia sieci wirtualnej toVNet z zasadami IPsec i IKE

Podobny do połączenia sieci VPN S2S Tworzenie zasad IPsec i IKE, a następnie zastosować zasady do nowego połączenia.

#### <a name="1-create-an-ipsecike-policy"></a>1. Tworzenie zasad IPsec i IKE

Poniższy przykładowy skrypt tworzy różne zasady IPsec i IKE z następujących algorytmów i parametry:
* IKEv2: AES128, SHA1, DHGroup14
* Protokół IPsec: GCMAES128 GCMAES128, PFS14, skojarzenia zabezpieczeń okres istnienia 14400 s & 102400000KB

```powershell
$ipsecpolicy2 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2. Utwórz wirtualnymi do połączenia z zasadami IPsec i IKE

Utwórz połączenie do wirtualnymi i stosowanie zasad IPsec i IKE, utworzony. W tym przykładzie zarówno bramy znajdują się w tej samej subskrypcji. Jest to możliwe utworzyć i skonfigurować zarówno połączeń z tych samych zasad IPsec i IKE w tej samej sesji programu PowerShell.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> Gdy zasady IPsec i IKE jest określony dla połączenia, bramy sieci VPN platformy Azure tylko wysłać lub Zaakceptuj propozycję IPsec i IKE z określonym algorytmów kryptograficznych i kluczy sile dla tego konkretnego połączenia. Upewnij się, że zasady protokołu IPsec dla obu połączeń są takie same, w przeciwnym razie nie zostanie nawiązane połączenie do wirtualnymi.

Po wykonaniu tych kroków, połączenie zostanie nawiązane za kilka minut i będzie miał następujących topologii sieci, jak pokazano na początku:

![ipsec-ike-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name ="managepolicy"></a>Część 5 - zasad IPsec/IKE aktualizacji dla połączenia

Ostatniej sekcji przedstawiono sposób zarządzać zasadami IPsec i IKE dla istniejącego połączenia S2S lub do wirtualnymi. Ćwiczenie poniżej przedstawiono następujące operacje dla połączenia:

1. Pokaż zasady IPsec i IKE połączenia
2. Dodania lub zaktualizowania zasad IPsec i IKE do połączenia
3. Usuń zasady IPsec i IKE z połączeniem

Te same kroki dotyczą zarówno S2S, jak i wirtualnymi do połączenia.

> [!IMPORTANT]
> Zasady protokołu IPsec/IKE jest obsługiwana w *standardowe* i *wysokowydajnej* opartej na trasach bramy sieci VPN tylko. Nie działa na podstawowa jednostka SKU bramy lub bramy sieci VPN opartych na zasadach.

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Pokaż zasady IPsec i IKE połączenia

Poniższy przykład pokazuje, jak można pobrać zasad IPsec i IKE skonfigurowane dla połączenia. Skrypty również kontynuować pracę z ćwiczeń opisanych powyżej.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Ostatnie polecenie wyświetla bieżące zasady IPsec i IKE skonfigurowana w połączeniu, jeśli istnieje. Oto przykładowe dane wyjściowe dla połączenia:

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES256
IkeIntegrity        : SHA384
DhGroup             : DHGroup24
PfsGroup            : PFS24
```

Jeśli jest skonfigurowane żadnych zasad IPsec i IKE, polecenie (PS > $connection6.policy) pobiera zwracany pusta. Nie oznacza IPsec i IKE nie jest skonfigurowana w połączeniu, ale czy nie ma niestandardowych zasad IPsec i IKE. Rzeczywiste połączenie korzysta z domyślnych zasad negocjowanych lokalnego urządzenia sieci VPN i bramy sieci VPN platformy Azure.

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Dodania lub zaktualizowania zasad IPsec i IKE połączenia

Kroki, aby dodać nową zasadę lub zaktualizować istniejące zasady połączenia są takie same: Utwórz nowe zasady, a następnie zastosować nowe zasady do połączenia.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Aby włączyć "UsePolicyBasedTrafficSelectors" podczas nawiązywania połączenia urządzenia sieci VPN na podstawie zasad lokalnych, Dodaj "-UsePolicyBaseTrafficSelectors" parametru do polecenia cmdlet, lub ustaw ją na $False wyłączyć opcję:

```powershell
Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

Można uzyskać połączenia ponownie, aby sprawdzić, czy zasady zostaną zmienione.

```powershell
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Powinny pojawić się dane wyjściowe z ostatniego wiersza, jak pokazano w poniższym przykładzie:

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES128
IkeIntegrity        : SHA1
DhGroup             : DHGroup14
PfsGroup            : None
```

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Usuń zasady IPsec i IKE z połączeniem

Po usunięciu zasady niestandardowe połączenie bramy sieci VPN platformy Azure powróci do [domyślnej listy propozycje IPsec i IKE](vpn-gateway-about-vpn-devices.md) i podejmuje próbę negocjacje do lokalnego urządzenia sieci VPN.

```powershell
$RG1           = "TestPolicyRG1"
$Connection16  = "VNet1toSite6"
$connection6   = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Sam skrypt służy do sprawdzania, jeśli zasady zostały usunięte z połączenia.

## <a name="next-steps"></a>Kolejne kroki

Zobacz [połączyć wiele urządzeń lokalnych, na podstawie zasad sieci VPN](vpn-gateway-connect-multiple-policybased-rm-ps.md) więcej szczegółów dotyczących ruchu na podstawie zasad selektorów.

Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Kroki opisano w sekcji [Tworzenie maszyny wirtualnej](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
