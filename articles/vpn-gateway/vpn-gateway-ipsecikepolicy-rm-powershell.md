---
title: Zasady protokołu IPsec/IKE dla sieci VPN S2S & połączenia sieci wirtualnej z siecią wirtualną
titleSuffix: Azure VPN Gateway
description: Konfigurowanie zasad Protokołu IPsec/IKE dla połączeń S2S lub sieci wirtualnej z siecią wirtualną z bramami sieci VPN platformy Azure przy użyciu usługi Azure Resource Manager i PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: eaca48fc354f1cf37635e9729b04eaaaa882ba1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161906"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>Konfigurowanie zasad protokołu IPsec/IKE dla połączeń międzylokacyjnej sieci VPN lub połączeń typu sieć wirtualna-sieć wirtualna

W tym artykule oszukuje się, jak skonfigurować zasady protokołu IPsec/IKE dla sieci VPN między lokacjami lub połączeniami sieci wirtualnej z siecią wirtualną przy użyciu modelu wdrażania Menedżera zasobów i programu PowerShell.



## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a><a name="about"></a>Parametry zasad IPsec i IKE dla bram sieci VPN platformy Azure — informacje
Standard protokołu IPsec i IKE obsługuje szeroką gamę algorytmów kryptograficznych w różnych kombinacjach. Zapoznaj się [z wymaganiami kryptograficznymi i bramami sieci VPN platformy Azure,](vpn-gateway-about-compliance-crypto.md) aby dowiedzieć się, w jaki sposób może to pomóc w zapewnieniu zgodności między lokalizacjami i łączności sieci wirtualnej z siecią wirtualną spełniają wymagania dotyczące zgodności lub zabezpieczeń.

Ten artykuł zawiera instrukcje tworzenia i konfigurowania zasad IPsec/IKE i zastosowania do nowego lub istniejącego połączenia:

* [Część 1 - Przepływ pracy do tworzenia i ustawiania zasad IPsec/IKE](#workflow)
* [Część 2 - Obsługiwane algorytmy kryptograficzne i kluczowe mocne strony](#params)
* [Część 3 - Tworzenie nowego połączenia sieci VPN S2S z zasadami IPsec/IKE](#crossprem)
* [Część 4 — tworzenie nowego połączenia sieci wirtualnej z siecią wirtualną z zasadami protokołu IPsec/IKE](#vnet2vnet)
* [Część 5 - Zarządzanie (tworzenie, dodawanie, usuwanie) zasad IPsec/IKE dla połączenia](#managepolicy)

> [!IMPORTANT]
> 1. Należy zauważyć, że zasady protokołu IPsec/IKE działają tylko w następujących jednostkach SKU bramy:
>    * ***VpnGw1, VpnGw2, VpnGw3*** (oparte na trasie)
>    * ***Standardowa*** i ***wysokawydajność*** (oparta na trasie)
> 2. Można określić tylko ***jedną*** kombinację zasad dla danego połączenia.
> 3. Należy określić wszystkie algorytmy i parametry zarówno dla IKE (Tryb główny) i IPsec (tryb szybki). Określenie zasad częściowych nie jest dozwolone.
> 4. Skonsultuj się ze specyfikacjami dostawcy urządzenia sieci VPN, aby upewnić się, że zasady są obsługiwane na lokalnych urządzeniach sieci VPN. Połączenia S2S lub sieci wirtualnej do sieci wirtualnej nie można ustalić, jeśli zasady są niezgodne.

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a><a name ="workflow"></a>Część 1 - Przepływ pracy do tworzenia i ustawiania zasad IPsec/IKE
W tej sekcji opisano przepływ pracy w celu utworzenia i zaktualizowania zasad IPsec/IKE w sieci VPN S2S lub połączeniu sieci wirtualnej z siecią wirtualną:
1. Tworzenie sieci wirtualnej i bramy VPN Gateway
2. Tworzenie bramy sieci lokalnej dla połączenia międzylokacowego lub innej sieci wirtualnej i bramy dla połączenia z siecią wirtualną z siecią wirtualną
3. Tworzenie zasad IPsec/IKE z wybranymi algorytmami i parametrami
4. Tworzenie połączenia (IPsec lub VNet2VNet) z zasadami IPsec/IKE
5. Dodawanie/aktualizowanie/usuwanie zasad IPsec/IKE dla istniejącego połączenia

Instrukcje zawarte w tym artykule pomagają skonfigurować i skonfigurować zasady protokołu IPsec/IKE, jak pokazano na diagramie:

![ipsec-ike-policy ipsec-ike-policy ipsec-ike-policy ipsec](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name="part-2---supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>Część 2 - Obsługiwane algorytmy kryptograficzne & kluczowe mocne strony

W poniższej tabeli wymieniono obsługiwane algorytmy kryptograficzne i mocne strony kluczy konfigurowane przez klientów:

| **IPsec/IKEv2**  | **Opcje**    |
| ---  | --- 
| Szyfrowanie IKEv2 | AES256, AES192, AES128, DES3, DES  
| Integralność IKEv2  | SHA384, SHA256, SHA1, MD5  |
| Grupa DH         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, Brak |
| Szyfrowanie IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Brak    |
| Integralność IPsec  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| Grupa PFS        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Brak 
| Okres istnienia skojarzeń zabezpieczeń QM   | **(Opcjonalnie**: wartości domyślne są używane, jeśli nie są określone)<br>Sekundy (liczba całkowita; **min. 300**/wartość domyślna 27 000 sekund)<br>KB (liczba całkowita; **min. 1024**/wartość domyślna to 102 400 000 KB)   |
| Selektor ruchu | UsePolicyBasedTrafficSelectors** ($True/$False; **Opcjonalne**, domyślne $False jeśli nie określono)    |
|  |  |

> [!IMPORTANT]
> 1. **Konfiguracja lokalnego urządzenia sieci VPN musi być zgodna z następującymi algorytmami (lub je zawierać) oraz z następującymi parametrami określonymi w zasadach protokołu IPsec/IKE platformy Azure (lub je zawierać):**
>    * Algorytm szyfrowania IKE (tryb główny / faza 1)
>    * Algorytm integralności IKE (tryb główny / faza 1)
>    * Grupa DH (tryb główny / faza 1)
>    * Algorytm szyfrowania IPsec (tryb szybki / faza 2)
>    * Algorytm integralności IPsec (tryb szybki / faza 2)
>    * Grupa PFS (tryb szybki / faza 2)
>    * Selektor ruchu (jeśli używane są selektory ruchuPolicyBasedTraffic)
>    * Okresy istnienia skojarzenia zabezpieczeń są tylko specyfikacjami lokalnymi i nie muszą być zgodne.
>
> 2. **Jeśli GCMAES jest używany jako dla algorytmu szyfrowania IPsec, należy wybrać ten sam algorytm GCMAES i długość klucza dla integralności IPsec; na przykład, używając GCMAES128 zarówno dla**
> 3. W powyższej tabeli:
>    * IKEv2 odpowiada trybowi głównemu lub fazie 1
>    * Protokół IPsec odpowiada trybowi szybkiemu lub fazie 2
>    * Grupa DH określa grupę Diffie-Hellmen używaną w trybie głównym lub w fazie 1
>    * Grupa PFS określiła grupę Diffie-Hellmen używaną w trybie szybkim lub w fazie 2
> 4. Okres istnienia skojarzenia zabezpieczeń trybu głównego protokołu IKEv2 jest ustalony na 28 800 sekund na bramach sieci VPN platformy Azure
> 5. Ustawienie "UsePolicyBasedTrafficSelectors" na $True na połączenie spowoduje skonfigurowanie bramy sieci VPN platformy Azure do łączenia się z zaporą sieci VPN opartą na zasadach w środowisku lokalnym. Jeśli włączysz policybasedTrafficSelectors, należy upewnić się, że urządzenie sieci VPN ma pasujące selektory ruchu zdefiniowane ze wszystkimi kombinacjami prefiksów sieci lokalnej (bramy sieci lokalnej) do/z prefiksów sieci wirtualnej platformy Azure, a nie do dowolnego. Na przykład jeśli prefiksy sieci lokalnej to 10.1.0.0/16 i 10.2.0.0/16, a prefiksy sieci wirtualnej to 192.168.0.0/16 i 172.16.0.0/16, trzeba określić następujące selektory ruchu:
>    * 10.1.0.0/16 <====> 192.168.0.0/16
>    * 10.1.0.0/16 <====> 172.16.0.0/16
>    * 10.2.0.0/16 <====> 192.168.0.0/16
>    * 10.2.0.0/16 <====> 172.16.0.0/16

Aby uzyskać więcej informacji dotyczących selektorów ruchu [opartych na zasadach, zobacz Łączenie wielu lokalnych urządzeń sieci VPN opartych na zasadach](vpn-gateway-connect-multiple-policybased-rm-ps.md).

W poniższej tabeli wymieniono odpowiednie grupy Diffie-Hellman obsługiwane przez zasady niestandardowe:

| **Grupa Diffie’ego-Hellmana**  | **DHGroup**              | **PFSGroup** | **Długość klucza** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | MODP, 768-bitowy   |
| 2                         | DHGroup2                 | PFS2         | MODP, 1024-bitowy  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | MODP, 2048-bitowy  |
| 19                        | ECP256                   | ECP256       | ECP, 256-bitowy    |
| 20                        | ECP384                   | ECP384       | ECP, 384-bitowy    |
| 24                        | DHGroup24                | PFS24        | MODP, 2048-bitowy  |

Więcej informacji można znaleźć w artykułach [RFC3526](https://tools.ietf.org/html/rfc3526) i [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name="part-3---create-a-new-s2s-vpn-connection-with-ipsecike-policy"></a><a name ="crossprem"></a>Część 3 - Tworzenie nowego połączenia sieci VPN S2S z zasadami IPsec/IKE

W tej sekcji znajdziesz kroki tworzenia połączenia sieci VPN S2S z zasadami IPsec/IKE. Następujące kroki tworzą połączenie, jak pokazano na diagramie:

![s2s-polityka](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

Aby uzyskać bardziej szczegółowe instrukcje dotyczące tworzenia połączenia sieci VPN s2S, zobacz [Tworzenie połączenia sieci VPN S2S.](vpn-gateway-create-site-to-site-rm-powershell.md)

### <a name="before-you-begin"></a><a name="before"></a>Przed rozpoczęciem

* Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
* Zainstaluj polecenia cmdlet programu Azure Resource Manager programu PowerShell. Zobacz [Omówienie programu Azure PowerShell,](/powershell/azure/overview) aby uzyskać więcej informacji na temat instalowania poleceń cmdlet programu PowerShell.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>Krok 1 - Tworzenie sieci wirtualnej, bramy sieci VPN i bramy sieci lokalnej

#### <a name="1-declare-your-variables"></a>1. Zadeklaruj swoje zmienne

W tym ćwiczeniu zaczynamy od zadeklarowania naszych zmiennych. Podczas konfigurowania produktu należy pamiętać o zastąpieniu ich odpowiednimi wartościami.

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

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Połącz się z subskrypcją i utwórz nową grupę zasobów

Upewnij się, że program PowerShell został przełączony do trybu umożliwiającego korzystanie z poleceń cmdlet usługi Resource Manager. Więcej informacji znajduje się w temacie [Using Windows PowerShell with Resource Manager](../powershell-azure-resource-manager.md) (Używanie programu Windows PowerShell z usługą Resource Manager).

Otwórz konsolę programu PowerShell i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Tworzenie sieci wirtualnej, bramy sieci VPN i bramy sieci lokalnej

Poniższy przykład tworzy sieć wirtualną TestVNet1 z trzema podsieciami i bramą sieci VPN. Podczas zastępowania wartości ważne jest, aby podsieć bramy zawsze nosiła nazwę GatewaySubnet. W przypadku nadania jej innej nazwy proces tworzenia bramy zakończy się niepowodzeniem.

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

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a><a name="s2sconnection"></a>Krok 2 — tworzenie połączenia sieci VPN S2S z zasadami IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Tworzenie zasad IPsec/IKE

Poniższy przykładowy skrypt tworzy zasady IPsec/IKE z następującymi algorytmami i parametrami:

* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS Brak, Okres istnienia SA 14400 sekund & 102400000KB

```powershell
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Jeśli używasz GCMAES dla protokołu IPsec, należy użyć tego samego algorytmu GCMAES i długości klucza zarówno dla szyfrowania IPsec i integralności. Na przykład powyżej odpowiednie parametry będą "-IpsecEncryption GCMAES256 -IpsecIntegrity GCMAES256" podczas korzystania z GCMAES256.

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2. Tworzenie połączenia sieci VPN S2S z zasadami IPsec/IKE

Utwórz połączenie sieci VPN S2S i zastosuj wcześniej utworzone zasady IPsec/IKE.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Opcjonalnie można dodać "-UsePolicyBasedTrafficSelectors $True" do polecenia cmdlet tworzenia połączenia, aby umożliwić bramie sieci VPN platformy Azure łączenie się z urządzeniami sieci VPN opartymi na zasadach lokalnymi, jak opisano powyżej.

> [!IMPORTANT]
> Po określeniu zasad IPsec/IKE w połączeniu brama sieci VPN platformy Azure będzie wysyłać lub akceptować tylko propozycję protokołu IPsec/IKE z określonymi algorytmami kryptograficznymi i mocnymi eniami kluczy w tym konkretnym połączeniu. Upewnij się, że lokalne urządzenie sieci VPN dla połączenia używa lub akceptuje dokładną kombinację zasad, w przeciwnym razie tunel sieci VPN S2S nie ustanowi.


## <a name="part-4---create-a-new-vnet-to-vnet-connection-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>Część 4 — tworzenie nowego połączenia sieci wirtualnej z siecią wirtualną z zasadami protokołu IPsec/IKE

Kroki tworzenia połączenia sieci wirtualnej z siecią wirtualną z zasadami Protokołu IPsec/IKE są podobne do tych, które występują w połączeniu sieci VPN S2S. Następujące przykładowe skrypty tworzą połączenie, jak pokazano na diagramie:

![zasady v2v](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

Zobacz [Tworzenie połączenia sieci wirtualnej z siecią wirtualną, aby](vpn-gateway-vnet-vnet-rm-ps.md) uzyskać bardziej szczegółowe kroki tworzenia połączenia sieci wirtualnej z siecią wirtualną. Aby utworzyć i skonfigurować testvnet1 i bramę sieci VPN, należy ukończyć [część 3.](#crossprem)

### <a name="step-1---create-the-second-virtual-network-and-vpn-gateway"></a><a name="createvnet2"></a>Krok 1 - Tworzenie drugiej sieci wirtualnej i bramy sieci VPN

#### <a name="1-declare-your-variables"></a>1. Zadeklaruj swoje zmienne

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

#### <a name="2-create-the-second-virtual-network-and-vpn-gateway-in-the-new-resource-group"></a>2. Tworzenie drugiej sieci wirtualnej i bramy sieci VPN w nowej grupie zasobów

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

### <a name="step-2---create-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>Krok 2 — tworzenie połączenia VNet-toVNet z zasadami IPsec/IKE

Podobnie jak połączenie sieci VPN S2S, utwórz zasady IPsec/IKE, a następnie zastosuj do zasad do nowego połączenia.

#### <a name="1-create-an-ipsecike-policy"></a>1. Tworzenie zasad IPsec/IKE

Poniższy przykładowy skrypt tworzy inne zasady IPsec/IKE z następującymi algorytmami i parametrami:
* IKEv2: AES128, SHA1, DHGroup14
* IPsec: GCMAES128, GCMAES128, PFS14, SA Żywotność 14400 sekund & 102400000KB

```powershell
$ipsecpolicy2 = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2. Tworzenie połączeń sieci wirtualnej z siecią wirtualną za pomocą zasad IPsec/IKE

Utwórz połączenie sieci wirtualnej z siecią wirtualną i zastosuj utworzone zasady IPsec/IKE. W tym przykładzie obie bramy są w tej samej subskrypcji. Dlatego można utworzyć i skonfigurować oba połączenia z tymi samymi zasadami IPsec/IKE w tej samej sesji programu PowerShell.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> Po określeniu zasad IPsec/IKE w połączeniu brama sieci VPN platformy Azure będzie wysyłać lub akceptować tylko propozycję protokołu IPsec/IKE z określonymi algorytmami kryptograficznymi i mocnymi eniami kluczy w tym konkretnym połączeniu. Upewnij się, że zasady protokołu IPsec dla obu połączeń są takie same, w przeciwnym razie połączenie sieci wirtualnej do sieci wirtualnej nie zostanie ustanowione.

Po wykonaniu tych kroków połączenie zostanie nawiązane w ciągu kilku minut, a na początku zostanie wyświetlona następująca topologia sieci:

![ipsec-ike-policy ipsec-ike-policy ipsec-ike-policy ipsec](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name="part-5---update-ipsecike-policy-for-a-connection"></a><a name ="managepolicy"></a>Część 5 — aktualizowanie zasad IPsec/IKE dla połączenia

W ostatniej sekcji pokazano, jak zarządzać zasadami protokołu IPsec/IKE dla istniejącego połączenia S2S lub sieci wirtualnej do sieci wirtualnej. Poniższe ćwiczenie przeprowadzi Cię przez następujące operacje połączenia:

1. Pokaż zasady IPsec/IKE połączenia
2. Dodawanie lub aktualizowanie zasad IPsec/IKE do połączenia
3. Usuwanie zasad IPsec/IKE z połączenia

Te same kroki dotyczą zarówno połączeń S2S i sieci wirtualnej do sieci wirtualnej.

> [!IMPORTANT]
> Zasady protokołu IPsec/IKE są obsługiwane tylko w bramy sieci VPN oparte na trasach *standard* *i highPerformance.* Nie działa na jednostce SKU bramy podstawowej ani w bramie sieci VPN opartej na zasadach.

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Pokaż zasady IPsec/IKE połączenia

W poniższym przykładzie pokazano, jak skonfigurować zasady protokołu IPsec/IKE w połączeniu. Skrypty również kontynuować z ćwiczeń powyżej.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Ostatnie polecenie wyświetla bieżącą zasadę IPsec/IKE skonfigurowana w połączeniu, jeśli istnieje. Poniżej przedstawiono przykładowe dane wyjściowe dla połączenia:

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

Jeśli nie skonfigurowano żadnych zasad protokołu IPsec/IKE, polecenie (PS> $connection6. IpsecPolicies) otrzymuje pusty zwrot. Nie oznacza to, że protokół IPsec/IKE nie jest skonfigurowany w połączeniu, ale nie ma niestandardowych zasad IPsec/IKE. Rzeczywiste połączenie używa domyślnej zasady wynegocjowanej między lokalnym urządzeniem sieci VPN a bramą sieci VPN platformy Azure.

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Dodawanie lub aktualizowanie zasad IPsec/IKE dla połączenia

Kroki, aby dodać nową zasadę lub zaktualizować istniejące zasady w połączeniu są takie same: utwórz nową zasadę, a następnie zastosuj nowe zasady do połączenia.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Aby włączyć parametr "UsePolicyBasedTrafficSelectors" podczas łączenia się z lokalnym urządzeniem sieci VPN opartym na zasadach, dodaj parametr "-UsePolicyBaseTrafficSelectors" do polecenia cmdlet lub ustaw go tak, aby $False, aby wyłączyć opcję:

```powershell
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

Możesz ponownie uzyskać połączenie, aby sprawdzić, czy zasady są zaktualizowane.

```powershell
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Powinny być widoczne dane wyjściowe z ostatniego wiersza, jak pokazano w poniższym przykładzie:

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

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Usuwanie zasad IPsec/IKE z połączenia

Po usunięciu zasad niestandardowych z połączenia brama sieci VPN platformy Azure powraca do [domyślnej listy propozycji protokołu IPsec/IKE](vpn-gateway-about-vpn-devices.md) i renegocjuje ponownie z lokalnym urządzeniem sieci VPN.

```powershell
$RG1           = "TestPolicyRG1"
$Connection16  = "VNet1toSite6"
$connection6   = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Za pomocą tego samego skryptu można sprawdzić, czy zasady zostały usunięte z połączenia.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat selektorów ruchu [opartych na zasadach, zobacz Łączenie wielu lokalnych urządzeń sieci VPN opartych na zasadach.](vpn-gateway-connect-multiple-policybased-rm-ps.md)

Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Kroki opisano w sekcji [Tworzenie maszyny wirtualnej](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
