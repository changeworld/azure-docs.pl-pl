---
title: Tworzenie bramy sieci wirtualnej strefowo nadmiarowy w strefy dostępności platformy Azure — wersja zapoznawcza | Dokumentacja firmy Microsoft
description: Wdrażanie bramy VPN Gateway i ExpressRoute w strefach dostępności — w wersji zapoznawczej.
services: vpn-gateway
documentationcenter: na
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/09/2018
ms.author: cherylmc
ms.openlocfilehash: fa349555a5effd41ca519cbd5a29005203d79543
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952559"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones---preview"></a>Tworzenie bramy sieci wirtualnej strefowo nadmiarowy w strefy dostępności platformy Azure — wersja zapoznawcza

Można wdrożyć bramy sieci VPN i ExpressRoute w [strefy dostępności platformy Azure](../availability-zones/az-overview.md). To zapewniają elastyczność, skalowalność i większą dostępność bramy sieci wirtualnej. Wdrażanie bramy w strefach dostępności Azure fizycznie i logicznie dzieli bramy w danym regionie, przy jednoczesnej ochronie łączności sieci środowiska lokalnego do platformy Azure przed awariami poziomu strefy.

Strefowy oraz strefowo nadmiarowych bramy mają ulepszeń wydajności podstawowych za pośrednictwem bramy sieci wirtualnej regularne. Ponadto Tworzenie bramy sieci wirtualnej strefowo nadmiarowego lub strefowych jest szybsze niż tworzenie innych bram. Zamiast biorąc 45 minut, utworzyć razy trwa około 15 minut przez bramę usługi ExpressRoute i 19 minut dla bramy sieci VPN.

### <a name="zrgw"></a>Strefowo nadmiarowe bram

Aby automatycznie wdrażać bram sieci wirtualnej w różnych strefach dostępności, można użyć bramy sieci wirtualnej strefowo nadmiarowe. Strefowo nadmiarowe bram mogą wykorzystać umowa SLA gwarantująca działanie przez 99,99% czasu w wersji ogólnie dostępnej na dostęp do usługi o kluczowym znaczeniu, skalowalnych usług na platformie Azure.

<br>
<br>

![Grafika bram redunant strefy](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>Strefowy bram

Aby wdrożyć bram w określonej strefy, należy użyć strefowych bram. Podczas wdrażania bramy strefowych oba wystąpienia bramy są wdrażane w tej samej strefie dostępności.

<br>
<br>

![Grafika strefowych bram](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>Jednostki SKU bramy

Strefowo nadmiarowy i strefowych bram należy użyć nowej jednostki SKU bramy. Po [samodzielnie zarejestrować się w wersji zapoznawczej](#enroll), zobaczysz nowe jednostki SKU we wszystkich regionach platformy Azure AZ bramy sieci wirtualnej. Te jednostki SKU są podobne do odpowiedniej jednostki SKU dla usługi ExpressRoute i bramy sieci VPN, z tą różnicą, że są one zależne strefowo nadmiarowy i strefowych bram.

Nowa brama jednostki SKU to:

### <a name="vpn-gateway"></a>VPN Gateway

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>Jednostki SKU publicznego adresu IP

Strefowo nadmiarowe bramy i bramy strefowych zależą od platformy Azure publiczny zasób adresu IP *standardowa* jednostki SKU. Konfigurację zasobów publicznych adresów IP platformy Azure Określa, czy bramy, który można wdrożyć strefowo nadmiarowe, lub strefowych. Jeśli utworzysz publiczny zasób adresu IP mający *podstawowe* jednostki SKU, brama nie będzie żadnych nadmiarowości strefy i zasoby bramy będzie regionalne.

### <a name="pipzrg"></a>Strefowo nadmiarowe bram

Po utworzeniu publicznego adresu IP, za pomocą **standardowa** publicznego adresu IP jednostki SKU bez określania strefy i zachowanie różni się w zależności od tego, czy brama jest brama sieci VPN lub brama usługi ExpressRoute. 

* Dla bramy sieci VPN wystąpienia dwóch bramy zostanie wdrożony w dowolnym 2 poza te trzy strefy, w celu zapewnienia nadmiarowości strefy. 
* W przypadku bramy usługi ExpressRoute ponieważ może istnieć więcej niż dwóch wystąpień, bramy mogą rozciągać się na trzy strefy.

### <a name="pipzg"></a>Strefowy bram

Po utworzeniu publicznego adresu IP, za pomocą **standardowa** publicznego adresu IP jednostki SKU i określenie strefy (1, 2 lub 3), wszystkie wystąpienia bramy zostanie wdrożony w tej samej strefie.

### <a name="piprg"></a>Regionalne bram

Po utworzeniu publicznego adresu IP, za pomocą **podstawowe** publicznego adresu IP jednostki SKU i bramy jest wdrażany jako regionalne bramy i nie ma żadnych strefy nadmiarowości wbudowanej w bramie.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Możesz użyć programu PowerShell zainstalowane lokalnie na komputerze lub w usłudze Azure Cloud Shell. Jeśli zdecydujesz się zainstalować i korzystać z programu PowerShell lokalnie, ta funkcja wymaga najnowszej wersji modułu programu PowerShell.

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>Przy użyciu programu PowerShell lokalnie

Jeśli używasz programu PowerShell lokalnie na komputerze, a nie przy użyciu usługi Cloud Shell, musisz zainstalować moduł PowerShell 6.1.1 lub nowszej. Aby sprawdzić wersję programu PowerShell, który został zainstalowany, użyj następującego polecenia:

```azurepowershell
Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path
```

Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="enroll"></a>1. Rejestrowanie w wersji zapoznawczej

Przed skonfigurowaniem bramy strefowo nadmiarowego lub strefowych, musisz najpierw samodzielnie zarejestrować subskrypcji w wersji zapoznawczej. Po zainicjowaniu obsługi subskrypcji rozpocznie się nowej bramy jednostki SKU we wszystkich regionach AZ platformy Azure. 

Upewnij się, że zalogowano się do konta platformy Azure i korzystania z subskrypcji, którą chcesz umieścić na liście dozwolonych dla tej wersji zapoznawczej. Skorzystaj z następującego przykładu, aby zarejestrować:

```azurepowershell-interactive
Register-AzureRmProviderFeature -FeatureName AllowVMSSVirtualNetworkGateway -ProviderNamespace Microsoft.Network
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

Aby sprawdzić, czy funkcja "AllowVMSSVirtualNetworkGateway" jest zarejestrowana w ramach subskrypcji, użyj następującego polecenia:

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network
```

Wynik będzie wyglądał podobnie do tego przykładu:

![Zainicjowano obsługę administracyjną](./media/create-zone-redundant-vnet-gateway/verifypreview.png)

## <a name="variables"></a>2. Zadeklarowanie zmiennych

Poniżej przedstawiono wartości używanych na potrzeby procedury w przykładzie. Ponadto niektóre przykłady używać zmiennych zadeklarowanych w ramach kroków. Jeśli używane są następujące kroki we własnym środowisku, koniecznie Zastąp te wartości własnymi. Podczas określania lokalizacji, należy sprawdzić, czy jest obsługiwany region, który określisz. Aby uzyskać więcej informacji, zobacz [— często zadawane pytania](#faq).

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

## <a name="configure"></a>3. Tworzenie sieci wirtualnej

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

Podsieć bramy zawiera zastrzeżone adresy IP, z których korzystają usługi bramy sieci wirtualnej. Aby dodać i ustawić podsieć bramy można użyć następujących przykładów:

Dodaj podsieć bramy.

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Ustaw konfigurację podsieci bramy dla sieci wirtualnej.

```azurepowershell-interactive
$getvnet | Set-AzureRmVirtualNetwork
```
## <a name="publicip"></a>5. Przesłanie żądania dotyczącego publicznego adresu IP
 
W tym kroku wybierz instrukcje, które są stosowane do bramy, która ma zostać utworzona. Wybór strefy do wdrażania bramy zależy od strefy, określony dla publicznego adresu IP.

### <a name="ipzoneredundant"></a>Strefowo nadmiarowe bram

Żądanie publicznego adresu IP za pomocą **standardowa** jednostki SKU publicznego adresu IP, a nie określać żadnej strefy. W tym przypadku standardowego publicznego adresu IP utworzonego będzie strefowo nadmiarowy publiczny adres IP.   

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="ipzonalgw"></a>Strefowy bram

Żądanie publicznego adresu IP za pomocą **standardowa** jednostki SKU publicznego adresu IP. Określenie strefy (1, 2 lub 3). Wszystkie wystąpienia bramy zostanie wdrożony w tej strefie.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>Regionalne bram

Żądanie publicznego adresu IP za pomocą **podstawowe** jednostki SKU publicznego adresu IP. W takich przypadkach brama jest wdrażana jako brama regionalnych i nie ma żadnych strefy nadmiarowości wbudowanej w bramie. Bramy są tworzone wystąpienia w żadnych stref odpowiednio.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>6. Utwórz konfigurację adresów IP

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>7. Tworzenie bramy

Tworzenie bramy sieci wirtualnej.

### <a name="for-expressroute"></a>Dla usługi ExpressRoute

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>Dla bramy sieci VPN

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="feedback"></a>Jak przekazać swoją opinię

Będziemy wdzięczni za Twoją opinię. Wyślij wiadomość e-mail do aznetworkgateways@microsoft.com Zgłoś wszelkie problemy lub opinię (dodatnia lub ujemna) strefowo nadmiarowy i strefowych bram sieci VPN i Expressroute. Obejmują nazwę swojej firmy w "[]" w wierszu tematu. Jeśli zgłaszasz problem, również obejmować identyfikator subskrypcji.

## <a name="faq"></a>Często zadawane pytania

### <a name="how-do-i-sign-up-for-the-preview"></a>Jak utworzyć konto usługi do korzystania z wersji zapoznawczej?

Możesz [samodzielnie zarejestrować](#enroll) za pomocą poleceń programu PowerShell, w tym artykule.

### <a name="what-will-change-when-i-enroll"></a>Co zostanie zmieniona, gdy zarejestruję?

Z perspektywy w trakcie okresu Zapoznawczego możesz wdrożyć bram dzięki nadmiarowości strefy. Oznacza to, że wszystkie wystąpienia bramy zostanie wdrożone w różnych strefach dostępności platformy Azure, a każda strefa dostępności w innej domenie błędów i aktualizacji. Dzięki temu bram niezawodne, dostępna i odporna na awarie strefy.

### <a name="can-i-use-the-azure-portal"></a>Czy można użyć witryny Azure portal?

Tak, można użyć witryny Azure portal w wersji zapoznawczej. Jednak nadal musisz zarejestrować przy użyciu programu PowerShell lub nie będzie można korzystać z portalu w wersji zapoznawczej.

### <a name="what-regions-are-available-for-the-preview"></a>Jakie regiony są dostępne w wersji zapoznawczej?

Strefowo nadmiarowy i strefowych bram są dostępne w regionach świadczenia publicznej środowisko produkcyjne i Azure.

### <a name="will-i-be-billed-for-participating-in-this-preview"></a>Jest rozliczana za udział w tej wersji zapoznawczej?

Nie będą naliczane bram, jeśli w trakcie okresu Zapoznawczego. Jednak nie ma umowy SLA załączone wraz z wdrożenia. Jesteśmy bardzo zainteresowani wysłuchaniu opinii.

> [!NOTE]
> Dla bramy usługi ExpressRoute brama nie jest naliczana opłata. Jednak obwodu, sama (nie brama) będzie rozliczane.

### <a name="what-regions-are-available-for-me-to-try-this-in"></a>Jakie regiony są dostępne dla mnie do wypróbowania w?

Publiczna wersja zapoznawcza jest dostępna w regionach środkowe stany USA i Francja Środkowa (regionach platformy Azure, które mają strefy dostępności jest ogólnie dostępna). Idąc dalej, firma Microsoft będzie udostępniać strefowo nadmiarowe bramy do Ciebie w innych regionach publicznych platformy Azure.

### <a name="can-i-change-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Czy mogę zmienić mój istniejącej bramy sieci wirtualnej do bram strefowo nadmiarowego lub strefowych

Migrowanie istniejących bram sieci wirtualnej do bram strefowo nadmiarowego lub strefowych nie jest obecnie obsługiwane. Można jednak usunąć istniejącą bramę i ponownie utworzyć bramę strefowo nadmiarowego lub strefowych.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Czy można wdrożyć bramy sieci VPN i Expressroute w tej samej sieci wirtualnej?

Współistnienie bram sieci VPN i Expressroute w tej samej sieci wirtualnej jest obsługiwana w publicznej wersji zapoznawczej. Jednak należy pamiętać o następujących wymaganiach i ograniczeniach:

* Rezerwa na wartość/27 zakres adresów IP podsieci bramy.
* Strefa nadmiarowego/strefowych bram Expressroute tylko mogą współistnieć z funkcją strefy nadmiarowego/strefowych bram sieci VPN.
* Przed wdrożeniem strefy nadmiarowego/strefowych bramy sieci VPN, należy wdrożyć strefę nadmiarowego/strefowych bramy Express Route.
* Strefa nadmiarowego/strefowych bramy Expressroute można podłączyć do maksymalnie 4 obwodów.

## <a name="next-steps"></a>Kolejne kroki

Będziemy wdzięczni za Twoją opinię. Wyślij wiadomość e-mail do aznetworkgateways@microsoft.com Zgłoś wszelkie problemy lub opinię (dodatnia lub ujemna) strefowo nadmiarowy i strefowych bram sieci VPN i Expressroute. Obejmują nazwę swojej firmy w "[]" w wierszu tematu. Jeśli zgłaszasz problem, również obejmować identyfikator subskrypcji.
