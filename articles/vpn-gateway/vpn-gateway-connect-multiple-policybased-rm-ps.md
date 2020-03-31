---
title: 'Brama sieci VPN platformy Azure: łączenie bram z wieloma lokalnymi urządzeniami sieci VPN opartymi na zasadach'
description: Skonfiguruj bramę sieci VPN opartą na trasach platformy Azure na wielu urządzeniach sieci VPN opartych na zasadach przy użyciu usługi Azure Resource Manager i powershell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: yushwang
ms.openlocfilehash: 687c33e50a986cf8af08d0201fe0159a79cf02a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123321"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Łączenie bram sieci VPN platformy Azure z wieloma lokalnymi urządzeniami sieci VPN opartymi na zasadach przy użyciu programu PowerShell

Ten artykuł ułatwia konfigurowanie bramy sieci VPN opartej na marszrutach platformy Azure do łączenia się z wieloma lokalnymi urządzeniami sieci VPN opartymi na zasadach, korzystając z niestandardowych zasad protokołu IPsec/IKE w połączeniach sieci VPN S2S.

## <a name="about-policy-based-and-route-based-vpn-gateways"></a><a name="about"></a>Bramy sieci VPN oparte na zasadach i trasach — informacje

Urządzenia sieci VPN oparte na *zasadach i* trasach różnią się w sposobie ustawiania selektorów ruchu IPsec w połączeniu:

* **Oparte na zasadach** Urządzenia sieci VPN używają kombinacji prefiksów z obu sieci, aby zdefiniować sposób szyfrowania/odszyfrowywania ruchu przez tunele IPsec. Zazwyczaj jest on zbudowany na urządzeniach zapory, które wykonują filtrowanie pakietów. Szyfrowanie i odszyfrowywanie tunelu IPsec jest dodawane do aparatu filtrowania i przetwarzania pakietów.
* **Oparte na marszrutach** Urządzenia sieci VPN używają selektorów ruchu dowolnego do dowolnego (symboli wieloznacznych) i umożliwiają tabelom routingu/przekazywania kierować ruch do różnych tuneli IPsec. Zazwyczaj jest zbudowany na platformach routera, gdzie każdy tunel IPsec jest modelowany jako interfejs sieciowy lub VTI (wirtualny interfejs tunelu).

Na poniższych diagramach wyróżniono dwa modele:

### <a name="policy-based-vpn-example"></a>Przykład sieci VPN opartej na zasadach
![oparte na polityce](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Przykład sieci VPN opartej na marszrutach
![oparte na trasie](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Obsługa platformy Azure dla sieci VPN opartej na zasadach
Obecnie platforma Azure obsługuje oba tryby bram sieci VPN: bramy sieci VPN oparte na trasach i bramy sieci VPN oparte na zasadach. Są one zbudowane na różnych platformach wewnętrznych, co skutkuje różnymi specyfikacjami:

|                          | **Brama sieci VPN oparta na zasadach** | **Brama sieci VPN oparta na routinie**       |**Brama sieci VPN oparta na routinie**                          |
| ---                      | ---                         | ---                              |---                                                 |
| **Azure Gateway SKU**    | Podstawowa (Basic)                       | Podstawowa (Basic)                            | VpnGw1, VpnGw2, VpnGw3, VpnGw4, VpnGw5  |
| **Wersja IKE**          | IKEv1                       | IKEv2                            | IKEv1 i IKEv2                         |
| **Max. Połączenia S2S** | **1**                       | 10                               | 30                     |
|                          |                             |                                  |                                                    |

Za pomocą niestandardowych zasad IPsec/IKE można teraz skonfigurować bramy sieci VPN oparte na trasach platformy Azure do używania selektorów ruchu opartych na prefiksach z opcją **"PolicyBasedTrafficSelectors**", aby połączyć się z lokalnymi urządzeniami sieci VPN opartymi na zasadach. Ta funkcja umożliwia łączenie się z sieci wirtualnej platformy Azure i bramy sieci VPN z wieloma lokalnymi urządzeniami sieci VPN/zapory opartymi na zasadach, usuwając limit pojedynczego połączenia z bieżących bram sieci VPN opartych na zasadach platformy Azure.

> [!IMPORTANT]
> 1. Aby włączyć tę łączność, lokalne urządzenia sieci VPN oparte na zasadach muszą obsługiwać **usługę IKEv2,** aby połączyć się z bramami sieci VPN opartymi na trasach platformy Azure. Sprawdź specyfikacje urządzenia VPN.
> 2. Sieci lokalne łączące się za pośrednictwem opartych na zasadach urządzeń sieci VPN z tym mechanizmem mogą łączyć się tylko z siecią wirtualną platformy Azure; **nie mogą przesyłać do innych sieci lokalnych ani sieci wirtualnych za pośrednictwem tej samej bramy sieci VPN platformy Azure.**
> 3. Opcja konfiguracji jest częścią niestandardowych zasad połączenia IPsec/IKE. Jeśli włączysz opcję selektora ruchu opartego na zasadach, należy określić pełną zasadę (algorytmy szyfrowania i integralności IPsec/IKE, mocne strony kluczy i okresy istnienia sa).

Na poniższym diagramie przedstawiono, dlaczego routing tranzytowy za pośrednictwem bramy sieci VPN platformy Azure nie działa z opcją opartą na zasadach:

![tranzyt oparty na polityce](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Jak pokazano na diagramie bramy sieci VPN platformy Azure ma selektory ruchu z sieci wirtualnej do każdego z prefiksów sieci lokalnej, ale nie prefiksy połączenia krzyżowego. Na przykład lokacja lokalna 2, lokacja 3 i lokacja 4 mogą komunikować się odpowiednio z siecią wirtualną1, ale nie mogą łączyć się ze sobą za pośrednictwem bramy sieci VPN platformy Azure. Diagram przedstawia selektory ruchu międzyłączami, które nie są dostępne w bramie sieci VPN platformy Azure w tej konfiguracji.

## <a name="workflow"></a><a name="workflow"></a>Przepływ pracy

Instrukcje opisane w tym artykule są zgodne z tym samym przykładem, co opisane w temacie [Konfigurowanie zasad protokołu IPsec/IKE dla połączeń S2S lub sieć wirtualna do sieci wirtualnej](vpn-gateway-ipsecikepolicy-rm-powershell.md) w celu ustanowienia połączenia sieci VPN S2S. Jest to pokazane na poniższym diagramie:

![s2s-polityka](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

Przepływ pracy, aby włączyć tę łączność:
1. Utwórz sieć wirtualną, bramę sieci VPN i bramę sieci lokalnej dla połączenia międzylokacowego.
2. Utwórz zasady protokołu IPsec/IKE.
3. Zastosuj zasady podczas tworzenia połączenia S2S lub sieci wirtualnej do sieci wirtualnej i **włączyć selektory ruchu oparte** na zasadach na połączenie.
4. Jeśli połączenie jest już utworzone, można zastosować lub zaktualizować zasady do istniejącego połączenia.

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial).

* [!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enable-policy-based-traffic-selectors"></a><a name="enablepolicybased"></a>Włączanie selektorów ruchu opartych na zasadach

W tej sekcji pokazano, jak włączyć selektory ruchu oparte na zasadach w połączeniu. Upewnij się, że [część 3 artykułu zasad Konfigurowanie protokołu IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md)została ukończona . Kroki opisane w tym artykule używają tych samych parametrów.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Krok 1 - Tworzenie sieci wirtualnej, bramy sieci VPN i bramy sieci lokalnej

#### <a name="connect-to-your-subscription-and-declare-your-variables"></a>Połącz się z subskrypcją i zadeklaruj zmienne

1. Jeśli program PowerShell jest używany lokalnie na komputerze, zaloguj się przy użyciu polecenia cmdlet *Connect-AzAccount.* Zamiast tego użyj usługi Azure Cloud Shell w przeglądarce.

2. Zadeklaruj swoje zmienne. W tym ćwiczeniu używamy następujących zmiennych:

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

#### <a name="create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Tworzenie sieci wirtualnej, bramy sieci VPN i bramy sieci lokalnej

1. Utwórz grupę zasobów.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG1 -Location $Location1
   ```
2. Poniższy przykład służy do tworzenia sieci wirtualnej TestVNet1 z trzema podsieciami i bramy sieci VPN. Jeśli chcesz zastąpić wartości, ważne jest, aby zawsze nadawać nazwę podsieci bramy w szczególności "GatewaySubnet". W przypadku nadania jej innej nazwy proces tworzenia bramy zakończy się niepowodzeniem.

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

### <a name="step-2---create-an-s2s-vpn-connection-with-an-ipsecike-policy"></a>Krok 2 — tworzenie połączenia sieci VPN S2S z zasadami IPsec/IKE

1. Utwórz zasady protokołu IPsec/IKE.

   > [!IMPORTANT]
   > Należy utworzyć zasady IPsec/IKE, aby włączyć opcję "UsePolicyBasedTrafficSelectors" na połączeniu.

   Poniższy przykład tworzy zasady IPsec/IKE z tymi algorytmami i parametrami:
    * IKEv2: AES256, SHA384, DHGroup24
    * IPsec: AES256, SHA256, PFS Brak, Okres istnienia SA 14400 sekund & 102400000KB

   ```azurepowershell-interactive
   $ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
   ```
1. Utwórz połączenie sieci VPN S2S za pomocą selektorów ruchu opartych na zasadach oraz zasad IPsec/IKE i zastosuj zasady IPsec/IKE utworzone w poprzednim kroku. Należy pamiętać o dodatkowym parametrze "-UsePolicyBasedTrafficSelectors $True", który umożliwia selektory ruchu oparte na zasadach na połączenie.

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
   $lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

   New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
   ```
1. Po wykonaniu tych czynności połączenie sieci VPN S2S będzie używać zdefiniowanych zasad IPsec/IKE i włączać selektory ruchu oparte na zasadach w połączeniu. Można powtórzyć te same kroki, aby dodać więcej połączeń do dodatkowych lokalnych urządzeń sieci VPN opartych na zasadach z tej samej bramy sieci VPN platformy Azure.

## <a name="to-update-policy-based-traffic-selectors"></a><a name="update"></a>Aby zaktualizować selektory ruchu oparte na zasadach
W tej sekcji pokazano, jak zaktualizować opcję selektorów ruchu opartego na zasadach dla istniejącego połączenia sieci VPN S2S.

1. Pobierz zasób połączenia.

   ```azurepowershell-interactive
   $RG1          = "TestPolicyRG1"
   $Connection16 = "VNet1toSite6"
   $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
   ```
1. Wyświetl opcję selektorów ruchu opartego na zasadach.
Następujący wiersz pokazuje, czy selektory ruchu oparte na zasadach są używane dla połączenia:

   ```azurepowershell-interactive
   $connection6.UsePolicyBasedTrafficSelectors
   ```

   Jeśli wiersz zwraca **"True",** selektory ruchu oparte na zasadach są konfigurowane w połączeniu; w przeciwnym razie zwraca **"False**"".
1. Po uzyskaniu zasobu połączenia można włączyć lub wyłączyć selektory ruchu oparte na zasadach w połączeniu.

   - Aby włączyć

      Poniższy przykład włącza opcję selektorów ruchu opartych na zasadach, ale pozostawia zasady IPsec/IKE bez zmian:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
      ```

   - Aby wyłączyć

      Poniższy przykład wyłącza opcję selektorów ruchu opartych na zasadach, ale pozostawia zasady IPsec/IKE bez zmian:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
      ```

## <a name="next-steps"></a>Następne kroki
Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Kroki opisano w sekcji [Tworzenie maszyny wirtualnej](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Zapoznaj się również [z zasadami Konfigurowanie protokołu IPsec/IKE dla połączeń sieci VPN lub sieci wirtualnej z siecią wirtualną S2S, aby](vpn-gateway-ipsecikepolicy-rm-powershell.md) uzyskać więcej informacji na temat niestandardowych zasad protokołu IPsec/IKE.
