---
title: Konfigurowania połączeń sieci VPN lokacja lokacja w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zasad IPsec/IKE dla połączeń sieci VPN lub sieć wirtualna-sieć wirtualna lokacja lokacja w usłudze Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2019
ms.author: sethm
ms.lastreviewed: 01/19/2019
ms.openlocfilehash: 07e598d6fd4ed2937d86f31593a220c0c28ba328
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58074662"
---
# <a name="configure-ipsecike-policy-for-site-to-site-vpn-connections"></a>Konfigurowanie zasad protokołu IPsec/IKE dla połączeń VPN typu lokacja lokacja

W tym artykule opisano kroki konfigurowania zasad protokołu IPsec/IKE dla sieci VPN typu lokacja lokacja (S2S) połączenia w usłudze Azure Stack.

>[!NOTE]
> Musi mieć uruchomioną kompilację usługi Azure Stack **1809** lub później, aby użyć tej funkcji.  Jeśli obecnie używasz kompilacji przed 1809, zaktualizuj system usługi Azure Stack do najnowszej kompilacji przed podjęciem próby korzystać z tej funkcji, lub wykonaj kroki opisane w tym artykule.

## <a name="ipsec-and-ike-policy-parameters-for-vpn-gateways"></a>Parametry zasad IPsec i IKE bramy sieci VPN

Standardowego protokołu IPsec i IKE obsługuje szeroką gamę algorytmów kryptograficznych w różnych kombinacjach. Aby zobaczyć, których parametry są obsługiwane w usłudze Azure Stack, zobacz [parametrów protokołu IPsec/IKE](azure-stack-vpn-gateway-settings.md#ipsecike-parameters), która może ułatwić spełniają wymagania zgodności i zabezpieczeń.

Ten artykuł zawiera instrukcje dotyczące sposobu tworzenia i konfigurowania zasad protokołu IPsec/IKE oraz dotyczą nowe lub istniejące połączenie.

## <a name="considerations"></a>Zagadnienia do rozważenia

Korzystając z tych zasad, należy zwrócić uwagę następujące ważne kwestie:

- Zasady protokołu IPsec/IKE działa tylko na *standardowa* i *HighPerformance* (oparte na trasach) jednostki SKU bramy.

- Można określić tylko **jeden** kombinację zasad dla danego połączenia.

- Należy określić wszystkie algorytmy i parametry zarówno dla protokołu IKE (tryb główny) i IPsec (tryb szybki). Określenie zasad częściowych nie jest dozwolone.

- Zapoznaj się z specyfikacji dostawcy urządzeń sieci VPN, tak aby upewnić się, że zasady jest obsługiwane na urządzeniach sieci VPN w środowisku lokalnym. Nie można nawiązać połączenia lokacja lokacja, jeśli zasady są niezgodne.

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a>Część 1 — przepływ pracy, aby utworzyć i ustawić zasady protokołu IPsec/IKE

W tej sekcji opisano przepływ pracy wymagane do tworzenia i aktualizowania zasad protokołu IPsec/IKE dla połączenia sieci VPN lokacja lokacja:

1. Tworzenie sieci wirtualnej i bramy sieci VPN.

2. Utwórz bramę sieci lokalnej dla połączenia między środowiskami lokalnymi.

3. Tworzenie zasad protokołu IPsec/IKE za pomocą wybranego algorytmów i parametrów.

4. Utwórz połączenie IPSec za pomocą zasad IPsec/IKE.

5. Dodawanie/aktualizowanie/usuwanie zasad protokołu IPsec/IKE dla istniejącego połączenia.

Instrukcje w tym artykule pomocy można Instalowanie i konfigurowanie zasad protokołu IPsec/IKE, jak pokazano na poniższej ilustracji:

![Instalowanie i konfigurowanie zasad IPsec/IKE](media/azure-stack-vpn-s2s/site-to-site.png)

## <a name="part-2---supported-cryptographic-algorithms-and-key-strengths"></a>Część 2. obsługiwane algorytmy kryptograficzne i siły klucza

W poniższej tabeli wymieniono obsługiwane algorytmy kryptograficzne i siły klucza konfigurowalne przez klientów usługi Azure Stack:

| IPsec/IKEv2                                          | Opcje                                                                  |
|------------------------------------------------------|--------------------------------------------------------------------------|
| Szyfrowanie IKEv2                                     | AES256, AES192, AES128, DES3, DES                                        |
| Integralność IKEv2                                      | SHA384, SHA256, SHA1, MD5                                                |
| Grupa DH                                             | ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, None         |
| Szyfrowanie IPsec                                     | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Brak |
| Integralność IPsec                                      | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                       |
| Grupa PFS                                            | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Brak                         |
| Okres istnienia skojarzeń zabezpieczeń QM                                       | (Opcjonalne: wartości domyślne są stosowane, jeśli nie określono)<br />                         Sekundy (liczba całkowita; min. 300/domyślne 27 000 sekund)<br />                         KB (liczba całkowita; min. 1024/domyślne 102400000 KB) |
| Selektor ruchu                                     | Selektory ruchu na podstawie zasad nie są obsługiwane w usłudze Azure Stack.         |

- Konfiguracja lokalnego urządzenia sieci VPN musi być zgodna z następującymi algorytmami (lub je zawierać) oraz z następującymi parametrami określonymi w zasadach protokołu IPsec/IKE platformy Azure (lub je zawierać):

  - Algorytm szyfrowania IKE (tryb główny / fazy 1)
  - Algorytm integralności IKE (tryb główny / fazy 1)
  - Grupa DH (trybu głównego / etap 1)
  - Algorytm szyfrowania IPsec (tryb szybki / Phase 2)
  - Algorytm integralności IPsec (tryb szybki / Phase 2)
  - Grupa PFS (tryb szybki / fazy 2)
  - Okresy istnienia skojarzenia zabezpieczeń są tylko specyfikacjami lokalnymi i nie muszą być zgodne.

- Jeśli GCMAES jest używany jak w przypadku algorytm szyfrowania IPsec, musisz wybrać ten sam algorytm GCMAES i długość klucza dla integralność IPsec; na przykład za pomocą GCMAES128 dla obu.

- W powyższej tabeli:

  - Protokół IKEv2 odnosi się do trybu głównego lub fazy 1
  - Protokół IPsec odnosi się do trybu szybkiego lub fazy 2
  - Grupa DH określa grupę Diffie-Hellmen używana w trybie główne fazy 1
  - Określona Grupa PFS, grupy Diffie'ego-Hellmen wykorzystane w trybie szybkim lub fazy 2

- Okres istnienia skojarzenia zabezpieczeń trybu głównego protokołu IKEv2 jest ustalony na 28 800 sekund na bramach sieci VPN platformy Azure Stack.

W poniższej tabeli wymieniono odpowiadających im grup Diffie'ego-Hellmana obsługiwane przez zasady niestandardowe:

| Grupa Diffie’ego-Hellmana | DHGroup   | PFSGroup      | Długość klucza    |
|----------------------|-----------|---------------|---------------|
| 1                    | DHGroup1  | PFS1          | MODP, 768-bitowy  |
| 2                    | DHGroup2  | PFS2          | MODP, 1024-bitowy |
| 14                   | DHGroup14<br/>DHGroup2048 | PFS2048       | MODP, 2048-bitowy |
| 19                   | ECP256    | ECP256        | ECP, 256-bitowy   |
| 20                   | ECP384    | ECP384        | ECP, 384-bitowy   |
| 24                   | DHGroup24 | PFS24         | MODP, 2048-bitowy |

Aby uzyskać więcej informacji, zobacz [RFC3526](https://tools.ietf.org/html/rfc3526) i [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name="part-3---create-a-new-site-to-site-vpn-connection-with-ipsecike-policy"></a>Część 3 — Tworzenie nowego połączenia sieci VPN typu lokacja lokacja przy użyciu zasad IPsec/IKE

Ta sekcja przeprowadzi Cię przez kroki umożliwiające utworzenie połączenia sieci VPN typu lokacja lokacja przy użyciu zasad protokołu IPsec/IKE. Poniższe kroki umożliwiają utworzenie połączenia, jak pokazano na poniższej ilustracji:

![lokacja do lokacji zasad](media/azure-stack-vpn-s2s/site-to-site.png)

Aby uzyskać szczegółowe instrukcje krok po kroku dotyczące tworzenia połączenia sieci VPN typu lokacja lokacja, zobacz [tworzenia połączenia sieci VPN lokacja lokacja](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

### <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że następujące wymagania wstępne:

- Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować swoje [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), lub zarejestrować się w celu [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

- W przypadku poleceń cmdlet programu Azure PowerShell Resource Manager. Zobacz [Instalowanie programu PowerShell dla usługi Azure Stack](../azure-stack-powershell-install.md) Aby uzyskać więcej informacji na temat instalowania poleceń cmdlet programu PowerShell.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Krok 1 — Tworzenie sieci wirtualnej, bramy sieci VPN i bramy sieci lokalnej

#### <a name="1-declare-variables"></a>1. Zadeklaruj zmienne

Na potrzeby tego ćwiczenia Rozpocznij od zadeklarowania poniższe zmienne. Należy koniecznie Zastąp symbole zastępcze własnymi wartościami, podczas konfigurowania w środowisku produkcyjnym:

```powershell
$Sub1 = "\<YourSubscriptionName\>"
$RG1 = "TestPolicyRG1"
$Location1 = "East US 2"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPconf1 = "gw1ipconf1"
$Connection16 = "VNet1toSite6"
$LNGName6 = "Site6"
$LNGPrefix61 = "10.61.0.0/16"
$LNGPrefix62 = "10.62.0.0/16"
$LNGIP6 = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Połączyć subskrypcję i utworzyć nową grupę zasobów

Upewnij się, że program PowerShell został przełączony do trybu umożliwiającego korzystanie z poleceń cmdlet usługi Resource Manager. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z usługi Azure Stack przy użyciu programu PowerShell jako użytkownik](azure-stack-powershell-configure-user.md).

Otwórz konsolę programu PowerShell i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Tworzenie sieci wirtualnej, bramy sieci VPN i bramy sieci lokalnej

Poniższy przykład tworzy sieć wirtualną, **TestVNet1**oraz trzy podsieci i bramę sieci VPN. Podczas zastępowania wartości ważne, że należy zawsze nazywać podsieć bramy specjalnie jest **GatewaySubnet**. W przypadku nadania jej innej nazwy proces tworzenia bramy zakończy się niepowodzeniem.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1 = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1

$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
-VirtualNetwork $vnet1

$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1
-Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
-Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn
-VpnType RouteBased -GatewaySku VpnGw1

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1
-Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix
$LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-site-to-site-vpn-connection-with-an-ipsecike-policy"></a>Krok 2 — Tworzenie połączenia sieci VPN typu lokacja lokacja przy użyciu zasad protokołu IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Tworzenie zasad protokołu IPsec/IKE

Ten przykładowy skrypt tworzy zasady protokołu IPsec/IKE za pomocą następujących algorytmów i parametrów:

- IKEv2: AES128, SHA1, DHGroup14
- IPsec: AES256, SHA256, none, sekund 14400 okres istnienia skojarzeń zabezpieczeń i 102400000KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup none -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Jeśli używasz GCMAES dla protokołu IPsec, należy użyć ten sam algorytm GCMAES i długość klucza dla integralności i szyfrowania IPsec.

#### <a name="2-create-the-site-to-site-vpn-connection-with-the-ipsecike-policy"></a>2. Tworzenie połączenia sieci VPN lokacja lokacja za pomocą zasad IPsec/IKE

Tworzenie połączenia sieci VPN typu lokacja lokacja i stosowanie zasad IPsec/IKE, która została utworzona wcześniej.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'Azs123'
```

> [!IMPORTANT]
> Po określeniu zasad protokołu IPsec/IKE dla połączenia, bramy sieci VPN platformy Azure tylko wysyłanie lub zaakceptuj propozycji protokołu IPsec/IKE za pomocą określonego algorytmy kryptograficzne i siły klucza dla tego konkretnego połączenia. Upewnij się, że urządzenie sieci VPN w środowisku lokalnym dla połączenia używa lub przyjmuje dokładnie kombinację zasad, w przeciwnym razie tunelu sieci VPN typu lokacja lokacja nie zostanie nawiązane.

## <a name="part-4---update-ipsecike-policy-for-a-connection"></a>Część 4 — zasady aktualizacji protokołu IPsec/IKE dla połączeń

Poprzedniej sekcji pokazano, jak zarządzać zasadami IPsec/IKE dla istniejącego połączenia lokacja lokacja. Poniższej sekcji przedstawiono następujące operacje dla połączenia:

1. Pokaż zasad IPsec/IKE połączenia
2. Dodania lub zaktualizowania zasad protokołu IPsec/IKE do połączenia
3. Usuń zasady protokołu IPsec/IKE, połączenie

> [!NOTE]
> Zasady protokołu IPsec/IKE są obsługiwane na *standardowa* i *HighPerformance* oparte na trasach bram sieci VPN tylko. Nie działa na *podstawowe* jednostki SKU bramy.

### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Pokaż zasad IPsec/IKE połączenia

Poniższy przykład pokazuje, jak można pobrać zasad protokołu IPsec/IKE, skonfigurowane dla połączenia. Skrypty są także nadal z poprzedniego Ćwiczenia:

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Ostatnie polecenie wyświetla bieżące zasady protokołu IPsec/IKE, skonfigurowany w ramach połączenia, jeśli istnieje. Poniższy przykład przedstawia przykładowe dane wyjściowe dla połączenia:

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

W przypadku skonfigurowane żadne zasady protokołu IPsec/IKE, polecenie `$connection6.policy` pobiera pustego zwracane. Nie oznacza to, że protokół IPsec/IKE nie jest skonfigurowany w ramach połączenia; oznacza, że nie niestandardowe zasady protokołu IPsec/IKE. Rzeczywiste połączenie korzysta z domyślnych zasad negocjowane między swoje lokalne urządzenie sieci VPN i bramą sieci VPN platformy Azure.

### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Dodawanie lub aktualizowanie zasad protokołu IPsec/IKE dla połączeń

Kroki, aby dodać nowe zasady lub zaktualizować istniejące zasady połączenia są takie same: Utwórz nowe zasady, a następnie zastosować nowe zasady dla połączenia.

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1

$newpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

$connection6.SharedKey = "AzS123"

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Można uzyskać połączenia ponownie, aby sprawdzić, jeśli zasady są aktualizowane:

```powershell
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Powinny zostać wyświetlone dane wyjściowe z ostatnim wierszu, jak pokazano w poniższym przykładzie:

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Usuwanie zasad protokołu IPsec/IKE, połączenie

Po usunięciu zasad niestandardowych z połączenia Brama sieci VPN platformy Azure, wraca do [propozycji protokołu IPsec/IKE domyślne](azure-stack-vpn-gateway-settings.md#ipsecike-parameters)i podejmuje negocjacje za pomocą urządzenia sieci VPN w środowisku lokalnym.

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.SharedKey = “AzS123”
$currentpolicy = $connection6.IpsecPolicies\[0\]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Ten sam skrypt służy do sprawdzania, jeśli zasady zostały usunięte z połączenia.

## <a name="next-steps"></a>Kolejne kroki

- [Ustawienia konfiguracji bramy sieci VPN dla usługi Azure Stack](azure-stack-vpn-gateway-settings.md)
