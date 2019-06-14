---
title: 'Konfigurowanie zasad protokołu IPsec/IKE dla połączeń S2S VPN lub sieć wirtualna-sieć wirtualna: Azure Resource Manager: Program PowerShell | Dokumentacja firmy Microsoft'
description: Konfigurowanie zasad IPsec/IKE dla połączeń S2S lub sieć wirtualna-sieć wirtualna z bramami sieci VPN Azure przy użyciu usługi Azure Resource Manager i programu PowerShell.
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
ms.openlocfilehash: d04d62d66b4ba22437e6d854566f8bbf5536a6fc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66121123"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>Konfigurowanie zasad protokołu IPsec/IKE dla połączeń S2S VPN lub sieć wirtualna-sieć wirtualna

W tym artykule przedstawiono kroki, aby skonfigurować zasady protokołu IPsec/IKE dla połączeń sieci VPN typu lokacja-lokacja lub sieć wirtualna-sieć wirtualna za pomocą modelu wdrażania usługi Resource Manager i programu PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about"></a>Parametry zasad IPsec i IKE bram Azure VPN Gateway — informacje
Standardowego protokołu IPsec i IKE obsługuje szeroką gamę algorytmów kryptograficznych w różnych kombinacjach. Zapoznaj się [o wymaganiach kryptograficznych oraz bram Azure VPN Gateway](vpn-gateway-about-compliance-crypto.md) aby zobaczyć, jak może to ułatwić, zapewniając obejmujących wiele lokalizacji i połączenia sieć wirtualna-sieć wirtualna spełniają wymagania zgodności i zabezpieczeń.

Ten artykuł zawiera instrukcje dotyczące tworzenia i konfigurowania zasad protokołu IPsec/IKE oraz dotyczą nowe lub istniejące połączenie:

* [Część 1 — przepływ pracy, aby utworzyć i ustawić zasady protokołu IPsec/IKE](#workflow)
* [Część 2. obsługiwane algorytmy kryptograficzne i siły klucza](#params)
* [Część 3 — Tworzenie nowego połączenia sieci VPN S2S za pomocą zasad IPsec/IKE](#crossprem)
* [Część 4 — Tworzenie nowego połączenia sieć wirtualna-sieć wirtualna za pomocą zasad IPsec/IKE](#vnet2vnet)
* [Część 5 — Zarządzanie (Tworzenie, dodawanie, usuwanie) zasad IPsec/IKE dla połączeń](#managepolicy)

> [!IMPORTANT]
> 1. Należy zwrócić uwagę na to, że zasady protokołu IPsec/IKE działa tylko w następujących jednostkach SKU bramy:
>    * ***VpnGw1, VpnGw2, VpnGw3*** (oparte na trasach)
>    * ***Standardowa*** i ***HighPerformance*** (oparte na trasach)
> 2. Można określić tylko ***jedną*** kombinację zasad dla danego połączenia.
> 3. Należy określić wszystkie algorytmy i parametry zarówno dla protokołu IKE (tryb główny) i IPsec (tryb szybki). Określenie zasad częściowych nie jest dozwolone.
> 4. Zapoznaj się z specyfikacji dostawcy urządzeń sieci VPN, tak aby upewnić się, że zasady jest obsługiwane na urządzeniach sieci VPN w środowisku lokalnym. S2S lub połączenia sieć wirtualna-sieć wirtualna nie może ustanowić, jeśli zasady są niezgodne.

## <a name ="workflow"></a>Część 1 — przepływ pracy, aby utworzyć i ustawić zasady protokołu IPsec/IKE
W tej sekcji opisano przepływ pracy do tworzenia i aktualizowania zasad IPsec/IKE dla połączenia sieci VPN S2S lub sieć wirtualna-sieć wirtualna:
1. Tworzenie sieci wirtualnej i bramy VPN Gateway
2. Tworzenie bramy sieci lokalnej dla wielu połączeń lokalnych lub innej sieci wirtualnej i bramy połączenia sieć wirtualna-sieć wirtualna
3. Tworzenie zasad protokołu IPsec/IKE za pomocą wybranego algorytmów i parametrów
4. Tworzenie połączenia (IPsec lub VNet2VNet) za pomocą zasad IPsec/IKE
5. Dodawanie/aktualizowanie/usuwanie zasad protokołu IPsec/IKE dla istniejącego połączenia

Instrukcje w tym artykule umożliwia instalowanie i konfigurowanie zasad IPsec/IKE, jak pokazano na diagramie:

![ipsec-ike-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name ="params"></a>Część 2. obsługiwane algorytmy kryptograficzne i siły klucza

W poniższej tabeli wymieniono obsługiwane algorytmy kryptograficzne i siły klucza konfigurowalne przez klientów:

| **IPsec/IKEv2**  | **Opcje**    |
| ---  | --- 
| Szyfrowanie IKEv2 | AES256, AES192, AES128, DES3, DES  
| Integralność IKEv2  | SHA384, SHA256, SHA1, MD5  |
| Grupa DH         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, None |
| Szyfrowanie IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Brak    |
| Integralność IPsec  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| Grupa PFS        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Brak 
| Okres istnienia skojarzeń zabezpieczeń QM   | (**Opcjonalnie**: wartości domyślne są stosowane, jeśli nie określono)<br>Sekundy (liczba całkowita; **min. 300**/wartość domyślna 27 000 sekund)<br>KB (liczba całkowita; **min. 1024**/wartość domyślna to 102 400 000 KB)   |
| Selektor ruchu | UsePolicyBasedTrafficSelectors ** ($True/$False; **Opcjonalnie**, domyślna $False, jeśli nie określono)    |
|  |  |

> [!IMPORTANT]
> 1. **Konfiguracji urządzenia sieci VPN w środowisku lokalnym musi odpowiadać lub zawierają następujące algorytmów i parametrów, które określają zasady protokołu IPsec/IKE platformy Azure:**
>    * Algorytm szyfrowania IKE (tryb główny / fazy 1)
>    * Algorytm integralności IKE (tryb główny / fazy 1)
>    * Grupa DH (trybu głównego / etap 1)
>    * Algorytm szyfrowania IPsec (tryb szybki / Phase 2)
>    * Algorytm integralności IPsec (tryb szybki / Phase 2)
>    * Grupa PFS (tryb szybki / fazy 2)
>    * Selektor ruchu, (jeśli jest używana UsePolicyBasedTrafficSelectors)
>    * Okresy istnienia skojarzenia zabezpieczeń są tylko specyfikacjami lokalnymi i nie muszą być zgodne.
>
> 2. **Jeśli GCMAES jest używany jak w przypadku algorytm szyfrowania IPsec, musisz wybrać ten sam algorytm GCMAES i długość klucza dla integralność IPsec; na przykład za pomocą GCMAES128 dla obu**
> 3. W powyższej tabeli:
>    * Protokół IKEv2 odnosi się do trybu głównego lub fazy 1
>    * Protokół IPsec odnosi się do trybu szybkiego lub fazy 2
>    * Grupa DH określa grupę Diffie-Hellmen używana w trybie główne fazy 1
>    * Określona Grupa PFS, grupy Diffie'ego-Hellmen wykorzystane w trybie szybkim lub fazy 2
> 4. Okres istnienia skojarzenia zabezpieczeń trybu głównego protokołu IKEv2 jest ustalony na 28 800 sekund na bramach sieci VPN platformy Azure
> 5. Ustawienie "UsePolicyBasedTrafficSelectors" $true połączenia skonfiguruje bramy sieci VPN Azure, aby nawiązać połączenie oparte na zasadach zapory sieci VPN w środowisku lokalnym. Jeśli włączysz PolicyBasedTrafficSelectors, musisz upewnij się, że pasujące selektorów ruchu zdefiniowane przy użyciu wszystkich kombinacjach lokalnej sieci prefiksy (Brama sieci lokalnej) z prefiksy sieci wirtualnej platformy Azure, a nie urządzenia sieci VPN Dowolna dowolna. Na przykład jeśli prefiksy sieci lokalnej to 10.1.0.0/16 i 10.2.0.0/16, a prefiksy sieci wirtualnej to 192.168.0.0/16 i 172.16.0.0/16, trzeba określić następujące selektory ruchu:
>    * 10.1.0.0/16 <====> 192.168.0.0/16
>    * 10.1.0.0/16 <====> 172.16.0.0/16
>    * 10.2.0.0/16 <====> 192.168.0.0/16
>    * 10.2.0.0/16 <====> 172.16.0.0/16

Aby uzyskać więcej informacji na temat opartych na zasadach selektorów ruchu, zobacz [łączenie wielu lokalnych na podstawie zasad urządzeń sieci VPN](vpn-gateway-connect-multiple-policybased-rm-ps.md).

W poniższej tabeli wymieniono odpowiadających im grup Diffie'ego-Hellmana obsługiwane przez zasady niestandardowe:

| **Grupa Diffie’ego-Hellmana**  | **DHGroup**              | **PFSGroup** | **Długość klucza** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | MODP, 768-bitowy   |
| 2                         | DHGroup2                 | PFS2         | MODP, 1024-bitowy  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | MODP, 2048-bitowy  |
| 19                        | ECP256                   | ECP256       | ECP, 256-bitowy    |
| 20                        | ECP384                   | ECP284       | ECP, 384-bitowy    |
| 24                        | DHGroup24                | PFS24        | MODP, 2048-bitowy  |

Więcej informacji można znaleźć w artykułach [RFC3526](https://tools.ietf.org/html/rfc3526) i [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name ="crossprem"></a>Część 3 — Tworzenie nowego połączenia sieci VPN S2S za pomocą zasad IPsec/IKE

Ta sekcja przeprowadzi Cię przez kroki tworzenia połączenia sieci VPN S2S przy użyciu zasad protokołu IPsec/IKE. Poniższe kroki umożliwiają utworzenie połączenia jak pokazano na diagramie:

![zasady s2s](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

Zobacz [Tworzenie połączenia sieci VPN S2S](vpn-gateway-create-site-to-site-rm-powershell.md) Aby uzyskać szczegółowe instrukcje krok po kroku dotyczące tworzenia połączenia sieci VPN S2S.

### <a name="before"></a>Przed rozpoczęciem

* Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
* Zainstaluj polecenia cmdlet programu Azure PowerShell Resource Manager. Zobacz [Omówienie programu Azure PowerShell](/powershell/azure/overview) Aby uzyskać więcej informacji na temat instalowania poleceń cmdlet programu PowerShell.

### <a name="createvnet1"></a>Krok 1 — Tworzenie sieci wirtualnej, bramy sieci VPN i bramy sieci lokalnej

#### <a name="1-declare-your-variables"></a>1. Zadeklarowanie zmiennych

Na potrzeby tego ćwiczenia Rozpoczniemy od zadeklarowania zmiennych. Podczas konfigurowania produktu należy pamiętać o zastąpieniu ich odpowiednimi wartościami.

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

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Połączyć subskrypcję i utworzyć nową grupę zasobów

Upewnij się, że program PowerShell został przełączony do trybu umożliwiającego korzystanie z poleceń cmdlet usługi Resource Manager. Więcej informacji znajduje się w temacie [Using Windows PowerShell with Resource Manager](../powershell-azure-resource-manager.md) (Używanie programu Windows PowerShell z usługą Resource Manager).

Otwórz konsolę programu PowerShell i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Tworzenie sieci wirtualnej, bramy sieci VPN i bramy sieci lokalnej

Poniższy przykład tworzy sieć wirtualną, z trzema podsieciami sieci wirtualnej TestVNet1 i bramę sieci VPN. Podczas zastępowania wartości ważne jest, aby podsieć bramy zawsze nosiła nazwę GatewaySubnet. W przypadku nadania jej innej nazwy proces tworzenia bramy zakończy się niepowodzeniem.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1

New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="s2sconnection"></a>Krok 2 — Tworzenie połączenia sieci VPN S2S przy użyciu zasad protokołu IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Tworzenie zasad protokołu IPsec/IKE

Poniższy przykładowy skrypt tworzy zasady protokołu IPsec/IKE za pomocą następujących algorytmów i parametrów:

* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS None, sekund 14400 okres istnienia skojarzeń zabezpieczeń i 102400000KB

```powershell
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Jeśli używasz GCMAES dla protokołu IPsec, należy użyć ten sam algorytm GCMAES i długość klucza dla integralności i szyfrowania IPsec. Na przykład powyżej, będą odpowiednich parametrów "-IpsecEncryption GCMAES256 - IpsecIntegrity GCMAES256" przy użyciu GCMAES256.

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2. Tworzenie połączenia sieci VPN S2S za pomocą zasad IPsec/IKE

Tworzenie połączenia sieci VPN S2S i stosowanie zasad IPsec/IKE, utworzony wcześniej.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Możesz opcjonalnie dodać "-UsePolicyBasedTrafficSelectors $True" do tworzenia połączenia polecenie cmdlet do włączania bramy Azure VPN gateway nawiązać połączenie na podstawie zasad urządzenia sieci VPN w środowisku lokalnym, zgodnie z powyższym opisem.

> [!IMPORTANT]
> Po określeniu zasad protokołu IPsec/IKE dla połączenia, bramy sieci VPN platformy Azure tylko wysyłanie lub zaakceptuj propozycji protokołu IPsec/IKE za pomocą określonego algorytmy kryptograficzne i siły klucza dla tego konkretnego połączenia. Upewnij się urządzeniem sieci VPN w środowisku lokalnym dla połączenia używa lub akceptuje kombinację dokładnie zasad, w przeciwnym razie nie zostanie ustanowione tunel S2S VPN.


## <a name ="vnet2vnet"></a>Część 4 — Tworzenie nowego połączenia sieć wirtualna-sieć wirtualna za pomocą zasad IPsec/IKE

Kroki tworzenia połączenia sieć wirtualna-sieć wirtualna za pomocą zasad IPsec/IKE są podobne do tego połączenia sieci VPN S2S. Następujące przykładowe skrypty Utwórz połączenie, jak pokazano na diagramie:

![v2v zasad](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

Zobacz [tworzenia połączenia sieć wirtualna-sieć wirtualna](vpn-gateway-vnet-vnet-rm-ps.md) bardziej szczegółowe kroki tworzenia połączenia sieć wirtualna-sieć wirtualna. Należy wykonać [część 3](#crossprem) tworzenie i konfigurowanie sieci TestVNet1 i bramę VPN.

### <a name="createvnet2"></a>Krok 1 — Tworzenie drugiej sieci wirtualnej i bramy sieci VPN

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

#### <a name="2-create-the-second-virtual-network-and-vpn-gateway-in-the-new-resource-group"></a>2. Tworzenie drugiej sieci wirtualnej i bramy sieci VPN w nowej grupy zasobów

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

$gw2pip1    = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$vnet2      = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1

New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
```

### <a name="step-2---create-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>Krok 2 — Tworzenie połączeń sieci wirtualnej toVNet za pomocą zasad IPsec/IKE

Podobnie jak połączenia sieci VPN S2S Tworzenie zasad protokołu IPsec/IKE, a następnie mają zastosowanie do zasad nowego połączenia.

#### <a name="1-create-an-ipsecike-policy"></a>1. Tworzenie zasad protokołu IPsec/IKE

Poniższy przykładowy skrypt tworzy inne zasady IPsec/IKE z następującymi algorytmami i parametrami:
* IKEv2: AES128, SHA1, DHGroup14
* IPsec: GCMAES128, GCMAES128, PFS14, 14400 okres istnienia skojarzeń zabezpieczeń s & 102400000KB

```powershell
$ipsecpolicy2 = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2. Tworzenie połączenia sieć wirtualna-sieć wirtualna za pomocą zasad IPsec/IKE

Tworzenie połączenia sieć wirtualna-sieć wirtualna i stosowanie zasad IPsec/IKE, który został utworzony. W tym przykładzie obie bramy znajdują się w tej samej subskrypcji. Dlatego jest to możliwe utworzyć i skonfigurować zarówno połączenia przy użyciu tych samych zasad protokołu IPsec/IKE, w tej samej sesji programu PowerShell.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> Po określeniu zasad protokołu IPsec/IKE dla połączenia, bramy sieci VPN platformy Azure tylko wysyłanie lub zaakceptuj propozycji protokołu IPsec/IKE za pomocą określonego algorytmy kryptograficzne i siły klucza dla tego konkretnego połączenia. Upewnij się, że zasady protokołu IPsec dla obu połączeń są takie same, w przeciwnym razie nie zostanie ustanowione połączenie sieć wirtualna-sieć wirtualna.

Po wykonaniu tych kroków, połączenie zostanie nawiązane za kilka minut i będzie mieć następujące topologią i konfiguracją sieci, jak pokazano na początku:

![ipsec-ike-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name ="managepolicy"></a>Część 5 — zasady aktualizacji protokołu IPsec/IKE dla połączeń

Ostatnia sekcja pokazuje, jak zarządzać zasadami IPsec/IKE dla istniejącego połączenia S2S lub sieć wirtualna-sieć wirtualna. Ćwiczenie poniżej przeprowadzi Cię przez następujące operacje dla połączenia:

1. Pokaż zasad IPsec/IKE połączenia
2. Dodania lub zaktualizowania zasad protokołu IPsec/IKE do połączenia
3. Usuń zasady protokołu IPsec/IKE, połączenie

Te same kroki dotyczą połączeń S2S i sieć wirtualna-sieć wirtualna.

> [!IMPORTANT]
> Zasady protokołu IPsec/IKE są obsługiwane na *standardowa* i *HighPerformance* oparte na trasach bram sieci VPN tylko. Nie działa na podstawowej jednostki SKU bramy lub bramy sieci VPN oparte na zasadach.

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Pokaż zasad IPsec/IKE połączenia

Poniższy przykład pokazuje, jak można pobrać zasad protokołu IPsec/IKE, skonfigurowane dla połączenia. Skrypty nadal z ćwiczeń opisanych powyżej.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Ostatnie polecenie wyświetla bieżące zasady protokołu IPsec/IKE, skonfigurowany w ramach połączenia, jeśli istnieje. Poniżej przedstawiono przykładowe dane wyjściowe dla połączenia:

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

W przypadku skonfigurowane żadne zasady protokołu IPsec/IKE, polecenie (PS > $connection6.policy) pobiera pustego zwracane. Nie oznacza protokołu IPsec/IKE nie jest skonfigurowany na połączenie, ale to nie niestandardowe zasady protokołu IPsec/IKE. Rzeczywiste połączenie korzysta z domyślnych zasad negocjowane między swoje lokalne urządzenie sieci VPN i bramą sieci VPN platformy Azure.

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Dodawanie lub aktualizowanie zasad protokołu IPsec/IKE dla połączeń

Kroki, aby dodać nowe zasady lub zaktualizować istniejące zasady połączenia są takie same: Utwórz nowe zasady, a następnie zastosować nowe zasady dla połączenia.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Aby włączyć "UsePolicyBasedTrafficSelectors" podczas nawiązywania połączenia urządzenia sieci VPN oparte na zasadach w środowisku lokalnym, należy dodać "-UsePolicyBaseTrafficSelectors" parametru do polecenia cmdlet lub ustaw go na $False, aby wyłączyć opcję:

```powershell
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

Można uzyskać połączenia ponownie, aby sprawdzić, jeśli zasady są aktualizowane.

```powershell
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Powinny zostać wyświetlone dane wyjściowe z ostatnim wierszu, jak pokazano w poniższym przykładzie:

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

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Usuwanie zasad protokołu IPsec/IKE, połączenie

Po usunięciu zasad niestandardowych z połączenia brama Azure VPN gateway jest przywracana do [domyślnej listy propozycji protokołu IPsec/IKE](vpn-gateway-about-vpn-devices.md) i podejmuje próbę negocjacje za pomocą urządzenia sieci VPN w środowisku lokalnym.

```powershell
$RG1           = "TestPolicyRG1"
$Connection16  = "VNet1toSite6"
$connection6   = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Ten sam skrypt służy do sprawdzania, jeśli zasady zostały usunięte z połączenia.

## <a name="next-steps"></a>Kolejne kroki

Zobacz [łączenie wielu lokalnych na podstawie zasad urządzeń sieci VPN](vpn-gateway-connect-multiple-policybased-rm-ps.md) Aby uzyskać więcej informacji dotyczących opartych na zasadach selektorów ruchu.

Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Kroki opisano w sekcji [Tworzenie maszyny wirtualnej](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
