---
title: Tworzenie połączeń S2S sieci VPN na platformie Azure i zarządzanie nimi przy użyciu programu PowerShell | Microsoft Docs
description: Samouczek — tworzenie połączeń S2S sieci VPN i zarządzanie nimi za pomocą modułu Azure PowerShell
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 05/08/2018
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: 0c71062bded65f8aa7c259c0678ee6675e2dab38
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55509477"
---
# <a name="create-and-manage-s2s-vpn-connections-with-the-azure-powershell-module"></a>Tworzenie połączeń S2S sieci VPN i zarządzanie nimi za pomocą modułu Azure PowerShell

Połączenia S2S sieci VPN na platformie Azure zapewniają bezpieczną łączność między lokalizacjami klienta a platformą Azure. Ten samouczek przedstawia cykle życia połączeń IPsec S2S sieci VPN, takie jak tworzenie połączenia S2S sieci VPN i zarządzanie nim. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie połączenia S2S sieci VPN
> * Aktualizowanie właściwości połączenia: klucza wstępnego, protokołu BGP, zasad IPsec/IKE
> * Dodawanie większej liczby połączeń sieci VPN
> * Usuwanie połączenia sieci VPN

Na poniższym diagramie przedstawiono topologię wykorzystywaną w tym samouczku:

![Diagram połączenia sieci VPN typu lokacja-lokacja](./media/vpn-gateway-tutorial-vpnconnection-powershell/site-to-site-diagram.png)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli chcesz zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga modułu Azure PowerShell w wersji 5.3 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="requirements"></a>Wymagania

Ukończ pierwszy samouczek: „[Tworzenie bramy sieci VPN przy użyciu programu Azure PowerShell](vpn-gateway-tutorial-create-gateway-powershell.md)”, aby utworzyć następujące zasoby:

1. Grupa zasobów (TestRG1), sieć wirtualna (VNet1) i podsieć GatewaySubnet
2. Brama sieci VPN (VNet1GW)

Poniżej przedstawiono wartości parametrów sieci wirtualnej. Zwróć uwagę na dodatkowe wartości bramy sieci lokalnej, aby reprezentowały Twoją sieć lokalną. Zmień te wartości na podstawie konfiguracji Twojego środowiska i sieci.

```azurepowershell-interactive
# Virtual network
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$VNet1Prefix = "10.1.0.0/16"
$VNet1ASN    = 65010
$Gw1         = "VNet1GW"

# On-premises network
$LNG1        = "VPNsite1"
$LNGprefix1  = "10.101.0.0/24"
$LNGprefix2  = "10.101.1.0/24"
$LNGIP1      = "YourDevicePublicIP"

# Optional - on-premises BGP properties
$LNGASN1     = 65011
$BGPPeerIP1  = "10.101.1.254"

# Connection
$Connection1 = "VNet1ToSite1"
```

Przepływ pracy prowadzący do utworzenia połączenia S2S sieci VPN jest prosty:

1. Utwórz bramę sieci lokalnej do reprezentowania sieci lokalnej.
2. Utwórz połączenie między bramą sieci VPN platformy Azure i bramą sieci lokalnej.

## <a name="create-a-local-network-gateway"></a>Tworzenie bramy sieci lokalnej

Brama sieci lokalnej reprezentuje Twoją sieć lokalną. W bramie sieci lokalnej możesz określić właściwości Twojej sieci lokalnej, w tym:

* Publiczny adres IP urządzenia sieci VPN
* Przestrzeń adresów lokalnych
* (Opcjonalnie) Atrybuty protokołu BGP (adres IP elementu równorzędnego protokołu BGP oraz numer AS)

Utwórz bramę sieci lokalnej za pomocą polecenia [New-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-6.8.1).

```azurepowershell-interactive
New-AzureRmLocalNetworkGateway -Name $LNG1 -ResourceGroupName $RG1 `
  -Location 'East US' -GatewayIpAddress $LNGIP1 -AddressPrefix $LNGprefix1,$LNGprefix2
```

## <a name="create-a-s2s-vpn-connection"></a>Tworzenie połączenia S2S sieci VPN

Następnie należy utworzyć połączenie sieci VPN typu lokacja-lokacja między bramą sieci wirtualnej i urządzeniem sieci VPN za pomocą polecenia [New-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-6.8.1). Należy pamiętać, że dla połączenia sieci VPN typu lokacja-lokacja wartość parametru „-ConnectionType” to *IPsec*.

```azurepowershell-interactive
$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng1 = Get-AzureRmLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng1 `
  -ConnectionType IPsec -SharedKey "Azure@!b2C3"
```

Dodaj opcjonalną właściwość „**-EnableBGP $True**”, aby włączyć protokół BGP dla połączenia, jeśli używasz protokołu BGP. To ustawienie jest domyślnie wyłączone.

## <a name="update-the-vpn-connection-pre-shared-key-bgp-and-ipsecike-policy"></a>Aktualizowanie klucza wstępnego, protokołu BGP i zasad IPsec/IKE połączenia VPN

### <a name="view-and-update-your-pre-shared-key"></a>Wyświetlanie i aktualizowanie klucza wstępnego

Połączenie S2S sieci VPN na platformie Azure używa klucza wstępnego (klucza tajnego) do uwierzytelniania między lokalnym urządzeniem sieci VPN i bramą sieci VPN platformy Azure. Klucz wstępny połączenia można wyświetlić i zaktualizować za pomocą poleceń [Get-AzureRmVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkgatewayconnectionsharedkey?view=azurermps-6.8.1) i [Set-AzureRmVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnectionsharedkey?view=azurermps-6.8.1).

> [!IMPORTANT]
> Klucz wstępny jest ciągiem **drukowalnych znaków ASCII**. Jego długość nie może przekraczać 128 znaków.

To polecenie wyświetla klucz wstępny połączenia:

```azurepowershell-interactive
Get-AzureRmVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1
```

W danych wyjściowych po przedstawionym powyżej kodzie zostanie wyświetlony ciąg „**Azure@!b2C3**”. Użyj poniższego polecenia, aby zmienić wartość klucza wstępnego na „**Azure@!_b2=C3**”:

```azurepowershell-interactive
Set-AzureRmVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1 `
  -Value "Azure@!_b2=C3"
```

### <a name="enable-bgp-on-vpn-connection"></a>Włączanie protokołu BGP dla połączenia sieci VPN

Brama sieci VPN platformy Azure obsługuje protokół dynamicznego routingu BGP. Protokół BGP można włączyć dla poszczególnych połączeń w zależności od tego, czy używasz protokołu BGP w lokalnych sieciach i urządzeniach. Przed włączeniem protokołu BGP dla połączenia należy określić następujące właściwości protokołu BGP:

* Numer ASN (Autonomous System Number) sieci VPN platformy Azure
* Lokalny numer ASN bramy sieci lokalnej
* Lokalny adres IP elementu równorzędnego protokołu BGP bramy sieci lokalnej

Jeśli nie skonfigurowano właściwości protokołu BGP, użyj następujących poleceń, aby dodać te właściwości do bramy sieci VPN i bramy sieci lokalnej: [Set-AzureRmVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-6.8.1) i [Set-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-6.8.1).

```azurepowershell-interactive
$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $vng1 -Asn $VNet1ASN

$lng1 = Get-AzureRmLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $lng1 `
  -Asn $LNGASN1 -BgpPeeringAddress $BGPPeerIP1
```

Włącz protokół BGP za pomocą polecenia [Set-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-6.8.1).

```azurepowershell-interactive
$connection = Get-AzureRmVirtualNetworkGatewayConnection `
  -Name $Connection1 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -EnableBGP $True
```

Protokół BGP można wyłączyć, zmieniając wartość właściwości „-EnableBGP” na **$False**. Zapoznaj się z artykułem [BGP on Azure VPN gateways](vpn-gateway-bgp-resource-manager-ps.md) (Protokół BGP w bramach sieci VPN platformy Azure), aby uzyskać bardziej szczegółowe wyjaśnienia dotyczące protokołu BGP w bramach sieci VPN platformy Azure.

### <a name="apply-a-custom-ipsecike-policy-on-the-connection"></a>Stosowanie niestandardowych zasad IPsec/IKE do połączenia

Aby określić dokładną kombinację algorytmów kryptograficznych i siłę kluczy IPsec/IKE połączenia, zamiast [domyślnych propozycji](vpn-gateway-about-vpn-devices.md#ipsec) można zastosować opcjonalne zasady IPsec/IKE. Poniższy przykładowy skrypt tworzy inne zasady IPsec/IKE z następującymi algorytmami i parametrami:

* IKEv2: AES256, SHA256, DHGroup14
* IPsec: AES128, SHA1, PFS14, okres istnienia skojarzeń zabezpieczeń 14 400 sekund, pojemność 102 400 000 KB

```azurepowershell-interactive
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 `
                -ResourceGroupName $RG1
$newpolicy  = New-AzureRmIpsecPolicy `
                -IkeEncryption AES256 -IkeIntegrity SHA256 -DhGroup DHGroup14 `
                -IpsecEncryption AES128 -IpsecIntegrity SHA1 -PfsGroup PFS2048 `
                -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -IpsecPolicies $newpolicy
```

Aby zapoznać się z pełną listą algorytmów i instrukcji, zobacz [IPsec/IKE policy for S2S or VNet-to-VNet connections](vpn-gateway-ipsecikepolicy-rm-powershell.md) (Zasady IPsec/IKE dla połączeń S2S lub połączeń między sieciami wirtualnymi).

## <a name="add-another-s2s-vpn-connection"></a>Dodawanie następnego połączenia S2S sieci VPN

Aby dodać inne połączenie S2S sieci VPN do tej samej bramy sieci VPN, utwórz następną bramę sieci lokalnej i utwórz nowe połączenie między nową bramą sieci lokalnej i bramą sieci VPN. Poniżej zamieszczono kod zgodny z przykładem z tego artykułu.

```azurepowershell-interactive
# On-premises network
$LNG2        = "VPNsite2"
$Location2   = "West US"
$LNGprefix21 = "10.102.0.0/24"
$LNGprefix22 = "10.102.1.0/24"
$LNGIP2      = "YourDevicePublicIP"
$Connection2 = "VNet1ToSite2"

New-AzureRmLocalNetworkGateway -Name $LNG2 -ResourceGroupName $RG1 `
  -Location $Location2 -GatewayIpAddress $LNGIP2 -AddressPrefix $LNGprefix21,$LNGprefix22

$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng2 = Get-AzureRmLocalNetworkGateway   -Name $LNG2 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng2 `
  -ConnectionType IPsec -SharedKey "AzureA1%b2_C3+"
```

Teraz istnieją dwa połączenia S2S sieci VPN do bramy sieci VPN platformy Azure.

![Połączenia sieci VPN z obsługą wielu lokacji](./media/vpn-gateway-tutorial-vpnconnection-powershell/multisite-connections.png)

## <a name="delete-a-s2s-vpn-connection"></a>Usuwanie połączenia S2S sieci VPN

Użyj polecenia [Remove-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermvirtualnetworkgatewayconnection?view=azurermps-6.8.1), aby usunąć połączenie S2S sieci VPN.

```azurepowershell-interactive
Remove-AzureRmVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1
```

Usuń bramę sieci lokalnej, jeśli nie jest już potrzebna. Nie można usunąć bramy sieci lokalnej, jeśli skojarzono z nią inne połączenia.

```azurepowershell-interactive
Remove-AzureRmVirtualNetworkGatewayConnection -Name $LNG2 -ResourceGroupName $RG1
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia połączeń S2S sieci VPN i zarządzania nimi. Opisano m.in. następujące czynności:

> [!div class="checklist"]
> * Tworzenie połączenia S2S sieci VPN
> * Aktualizowanie właściwości połączenia: klucza wstępnego, protokołu BGP, zasad IPsec/IKE
> * Dodawanie większej liczby połączeń sieci VPN
> * Usuwanie połączenia sieci VPN

Przejdź do następujących samouczków, aby dowiedzieć się więcej na temat połączeń S2S, między sieciami wirtualnymi i P2S.

> [!div class="nextstepaction"]
> * [Tworzenie połączeń między sieciami wirtualnymi](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Tworzenie połączeń P2S](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
