---
title: Utwórz bramę sieci wirtualnej strefowo nadmiarowy w strefach dostępności Azure - Preview | Dokumentacja firmy Microsoft
description: Wdrażanie bramy sieci VPN i ExpressRoute bram w strefach dostępności - Preview.
services: vpn-gateway
documentationcenter: na
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/28/2018
ms.author: cherylmc
ms.openlocfilehash: c484358bf98f0121cfc3ce270b162b01c75b5b09
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096237"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones---preview"></a>Utwórz bramę sieci wirtualnej strefowo nadmiarowy w strefach dostępności Azure - Preview

Można wdrożyć bram sieci VPN i ExpressRoute w [stref dostępności Azure](../availability-zones/az-overview.md). Wybranie tej opcji powoduje odporność, skalowalność i większą dostępność do bramy sieci wirtualnej. Wdrażanie bramy w strefach dostępności Azure fizycznie i logicznie oddziela bram w obrębie regionu, chroniąc łączność sieci lokalnej na platformie Azure z poziomu strefy błędów.

Bramy zonal i strefowo nadmiarowy mają podstawowych wydajności za pośrednictwem bramy sieci wirtualnej regularne. Ponadto Tworzenie bramy sieci wirtualnej strefowo nadmiarowy lub zonal jest szybsze od tworzenia innych bram. Zamiast 45 minut utworzyć razy trwają około 15 minut przez bramę usługi ExpressRoute i 19 minut dla bramy sieci VPN.

### <a name="zrgw"></a>Bram strefowo nadmiarowy

Aby automatycznie wdrożyć z bram sieci wirtualnej w różnych strefach dostępności, używając bram sieci wirtualnej strefowo nadmiarowy. Z bramami strefowo nadmiarowy można wykorzystać czas działania 99,99% umowy SLA w GA dostępu do sieci krytycznym, skalowalnych usług Azure.

<br>
<br>

![Grafika bram redunant strefy](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>Zonal bram

Aby wdrożyć bram w określonej strefy, należy użyć zonal bram. Podczas wdrażania bramy zonal oba wystąpienia bramy są wdrażane w tej samej strefie dostępności.

<br>
<br>

![Grafika zonal bram](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>Jednostki SKU bramy

Brama strefowo nadmiarowy i zonal muszą korzystać nowej jednostki SKU bramy. Gdy [samodzielnie zarejestrować się w podglądzie](#enroll), zostanie wyświetlony nowy bramy sieci wirtualnej jednostki SKU we wszystkich regionach Azure AZ. Te jednostki SKU są podobne do odpowiedniej jednostki SKU dla ExpressRoute, jak i bramy sieci VPN, z wyjątkiem tego, że są one zależne strefowo nadmiarowy i zonal bram.

Nową bramę jednostki SKU są:

### <a name="vpn-gateway"></a>VPN Gateway

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>Jednostki SKU publicznego adresu IP

Strefowo nadmiarowy bram i bram zonal zależne od zasobów platformy Azure publicznego adresu IP *standardowe* jednostki SKU. Konfiguracja zasobu publicznego adresu IP platformy Azure Określa, czy bramy, który można wdrożyć jest strefowo nadmiarowy, lub zonal. Jeśli utworzono zasób publicznego adresu IP z *podstawowe* jednostka SKU bramy nie będą miały żadnych nadmiarowość strefy i zasobów bramy będzie regionalne.

### <a name="pipzrg"></a>Bram strefowo nadmiarowy

Po utworzeniu publiczny adres IP, za pomocą **standardowe** SKU publicznego adresu IP bez określania strefy, to zachowanie różni się w zależności od tego, czy brama jest bramy sieci VPN lub brama usługi ExpressRoute. 

* Dla bramy sieci VPN wystąpieniach bramy dwóch zostanie wdrożona w dowolnym 2 poza te trzy strefy w celu zapewnienia nadmiarowości strefy. 
* Dla bramy ExpressRoute ponieważ może istnieć więcej niż dwa wystąpienia bramy mogą znajdować się we wszystkich trzech stref.

### <a name="pipzg"></a>Zonal bram

Po utworzeniu publiczny adres IP, za pomocą **standardowe** publicznego adresu IP jednostki SKU i określenie strefy (1, 2 lub 3), wszystkich wystąpień bramy zostanie wdrożony w tej samej strefie.

### <a name="piprg"></a>Regionalne bram

Po utworzeniu publiczny adres IP, za pomocą **podstawowe** SKU publicznego adresu IP, brama jest wdrożona jako regionalne bramy, a nie ma żadnych strefy nadmiarowość wbudowanych w bramie.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Możesz użyć programu PowerShell, albo zainstalowane lokalnie na komputerze lub powłoki chmury Azure. Jeśli wybierzesz do zainstalowania i używania programu PowerShell lokalnie, ta funkcja wymaga najnowszej wersji modułu programu PowerShell.

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>Lokalnie przy użyciu programu PowerShell

Jeśli używasz programu PowerShell lokalnie na komputerze, a nie za pomocą powłoki chmury, musisz zainstalować moduł PowerShell 6.1.1 lub nowszej. Aby sprawdzić wersję programu PowerShell, który został zainstalowany, użyj następującego polecenia:

```azurepowershell
Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path
```

Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="enroll"></a>1. Zarejestruj się w wersji zapoznawczej

Przed skonfigurowaniem bramy strefowo nadmiarowy lub zonal, należy najpierw samodzielnie zarejestrować subskrypcji w wersji zapoznawczej. Po zainicjowano subskrypcji, rozpocznie wyświetlić nową bramę jednostki SKU we wszystkich regionach Azure AZ. 

Upewnij się, że zalogowano się na konto platformy Azure i korzystają z subskrypcji, które mają być dozwolone dla tej wersji zapoznawczej. Skorzystaj z następującego przykładu, aby zarejestrować:

```azurepowershell-interactive
Register-AzureRmProviderFeature -FeatureName AllowVMSSVirtualNetworkGateway -ProviderNamespace Microsoft.Network
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

Aby sprawdzić, czy funkcja "AllowVMSSVirtualNetworkGateway" jest zarejestrowana w ramach subskrypcji, użyj następującego polecenia:

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network
```

Wynik będzie wyglądać podobnie jak w tym przykładzie:

![Zainicjowano obsługę administracyjną](./media/create-zone-redundant-vnet-gateway/verifypreview.png)

## <a name="variables"></a>2. Zadeklarowanie zmiennych

Wartości używanych na przykład kroki są wymienione poniżej. Ponadto niektóre przykłady używać zmiennych zadeklarowanych w ramach czynności. Jeśli używane są następujące kroki we własnym środowisku, należy zastąpić te wartości własnymi. Podczas określania lokalizacji, upewnij się, że region, które określisz jest obsługiwana. Aby uzyskać więcej informacji, zobacz [— często zadawane pytania](#faq).

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="configure"></a>3. Utwórz sieć wirtualną

Utwórz grupę zasobów.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

Utwórz sieć wirtualną.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzureRmVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="gwsub"></a>4. Dodaj podsieć bramy

Podsieć bramy zawiera zastrzeżone adresy IP, które używają usługi bramy sieci wirtualnej. Użyj poniższych przykładach, aby dodać i ustawić podsieć bramy:

Dodaj podsieć bramy.

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Ustaw konfigurację podsieci bramy sieci wirtualnej.

```azurepowershell-interactive
$getvnet | Set-AzureRmVirtualNetwork
```
## <a name="publicip"></a>5. Przesłanie żądania dotyczącego publicznego adresu IP
 
W tym kroku wybierz instrukcje dotyczące bramy, która ma zostać utworzona. Wybór strefy wdrażania bramy zależy od stref dla publicznego adresu IP.

### <a name="ipzoneredundant"></a>Dla bram strefowo nadmiarowy

Żądaj publicznego adresu IP z **standardowe** SKU publicznego adresu IP i nie określaj żadnej strefy. W tym przypadku standardowe publiczny adres IP utworzona będzie strefowo nadmiarowy publicznego adresu IP.   

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="ipzonalgw"></a>Zonal bram

Żądaj publicznego adresu IP z **standardowe** SKU publicznego adresu IP. Określ strefę (1, 2 lub 3). Wszystkie wystąpienia bramy zostanie wdrożony w tej strefie.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>Regionalne bram

Żądaj publicznego adresu IP z **podstawowe** SKU publicznego adresu IP. W takim przypadku bramy jest wdrożona jako regionalne bramy i nie ma żadnych strefy nadmiarowość wbudowanych w bramie. Wystąpienia bramy są tworzone w dowolnej strefie odpowiednio.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>6. Tworzenie konfiguracji IP

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>7. Tworzenie bramy

Utwórz bramę sieci wirtualnej.

>[!NOTE]
>W tej chwili nie można określić jednostka SKU bramy. Jednostka SKU zostanie automatycznie domyślnie ErGw1AZ ExpressRoute i VpnGw1AZ dla bramy sieci VPN.
>

### <a name="for-expressroute"></a>W przypadku połączeń ExpressRoute

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>Dla bramy sieci VPN

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="feedback"></a>Jak przesłać opinię

Prosimy o wyrażenie opinii. Wyślij wiadomość e-mail do aznetworkgateways@microsoft.com do Zgłoś wszelkie problemy lub Prześlij opinię (dodatnie lub ujemne) dla strefy nadmiarowego i zonal bram sieci VPN i Expressroute. Zawierają nazwę firmy w "[]" w wierszu tematu. Również obejmować identyfikator subskrypcji raportowania problemu.

## <a name="faq"></a>Często zadawane pytania

### <a name="how-do-i-sign-up-for-the-preview"></a>Jak tworzyć konta dla wersji zapoznawczej?

Możesz [samodzielnie zarejestrować](#enroll) za pomocą poleceń programu PowerShell w tym artykule.

### <a name="what-will-change-when-i-enroll"></a>Co zostanie zmieniona, gdy zarejestruję?

Z perspektywy administratora podczas udostępniania wersji zapoznawczej, można wdrożyć z bramy z nadmiarowością strefy. To oznacza, że wszystkie wystąpienia bramy zostanie wdrożone w różnych strefach dostępności Azure i każdej strefy dostępności jest w innej domenie awarii i aktualizacji. Bramy sieci dzięki temu bardziej niezawodne, dostępność i odporność na awarie strefy.

### <a name="what-regions-are-available-for-the-preview"></a>Jakie regiony są dostępne w wersji zapoznawczej?

Nadmiarowe strefy i zonal bramy są dostępne w produkcji/regiony platformy Azure publicznego.

### <a name="will-i-be-billed-for-participating-in-this-preview"></a>Zostanie I opłaty będą naliczane za uczestniczących w tej wersji zapoznawczej?

Nie będą naliczane sieci bramy, jeśli podczas udostępniania wersji zapoznawczej. Ponieważ nie ma żadnych SLA podłączonych do wdrożenia. Firma Microsoft interesuje bardzo wysłuchaniu opinii.

> [!NOTE]
> Dla bram usługi ExpressRoute brama nie jest rozliczany obciążona. Jednak zostanie rozliczony obwodu się (nie bramy).

### <a name="what-regions-are-available-for-me-to-try-this-in"></a>Jakie regiony są dostępne dla mnie próby to w?

Publicznej wersji zapoznawczej jest dostępna w regionach środkowe stany USA i Francja centralnej (regiony platformy Azure mających stref dostępności ogólnie dostępna). Idąc dalej, firma Microsoft udostępni Strefowo nadmiarowy bramy do Ciebie w różnych regionach w publicznej Azure.

### <a name="can-i-change-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Czy można zmienić mojej istniejącej bramy sieci wirtualnej do bram strefowo nadmiarowy lub zonal?

Migrowanie z istniejącej bramy sieci wirtualnej do bram strefowo nadmiarowy lub zonal nie jest obecnie obsługiwane. Można jednak usunąć istniejące bramy i ponownie utwórz strefowo nadmiarowy lub zonal bramę.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Czy mogę wdrożyć zarówno sieci VPN i Expressroute bram w tej samej sieci wirtualnej

Współistnienie zarówno sieci VPN i Expressroute bram w tej samej sieci wirtualnej jest obsługiwany w publicznej wersji zapoznawczej. Jednak należy pamiętać o następujących wymaganiach i ograniczeniach:

* Rezerwacja a /27 zakres adresów IP dla podsieci bramy.
* Strefy nadmiarowe/zonal bram Express Route tylko mogą współistnieć z strefy nadmiarowe/zonal bramy sieci VPN.
* Wdrażanie strefy nadmiarowe/zonal bramy Expressroute przed wdrożeniem strefy nadmiarowe/zonal bramy sieci VPN.
* Strefy nadmiarowe/zonal bramy Expressroute można podłączyć do maksymalnie 4 obwodów.

## <a name="next-steps"></a>Kolejne kroki

Prosimy o wyrażenie opinii. Wyślij wiadomość e-mail do aznetworkgateways@microsoft.com do Zgłoś wszelkie problemy lub Prześlij opinię (dodatnie lub ujemne) dla strefy nadmiarowego i zonal bram sieci VPN i Expressroute. Zawierają nazwę firmy w "[]" w wierszu tematu. Również obejmować identyfikator subskrypcji raportowania problemu.
