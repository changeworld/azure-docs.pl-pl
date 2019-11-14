---
title: 'ExpressRoute: Połącz sieć wirtualną z obwodem: Azure PowerShell'
description: Ten dokument zawiera omówienie sposobu łączenia sieci wirtualnych (Vnet) do obwodów usługi ExpressRoute za pomocą modelu wdrażania usługi Resource Manager i programu PowerShell.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 05/20/2018
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 22e235b16f834198f5edc2f9365d2b13e1e9c49f
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74031729"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Łączenie sieci wirtualnej z obwodem usługi ExpressRoute
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-linkvnet-cli.md)
> * [Wideo — witryna Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-linkvnet-classic.md)
>

Ten artykuł ułatwia łączenia sieci wirtualnych (Vnet) obwodami usługi ExpressRoute platformy Azure przy użyciu modelu wdrażania usługi Resource Manager i programu PowerShell. Sieci wirtualne albo mogą być w tej samej subskrypcji lub w ramach innej subskrypcji. W tym artykule także dowiesz się, jak zaktualizować łącze sieci wirtualnej.

* Maksymalnie 10 sieciami wirtualnymi można połączyć standardowy obwód usługi ExpressRoute. Wszystkie sieci wirtualne muszą znajdować się w tym samym regionie geograficznymi, korzystając z standardowy obwód usługi ExpressRoute. 

* Pojedynczej sieci wirtualnej można połączyć maksymalnie cztery obwodów usługi ExpressRoute. Wykonaj kroki w tym artykule, aby utworzyć nowy obiekt połączenia dla każdy obwód usługi ExpressRoute, z którą jest nawiązywane. Obwody usługi ExpressRoute może być w tej samej subskrypcji, w różnych subskrypcjach lub kombinacji obu.

* Można połączyć sieci wirtualne poza region geopolityczny obwodu usługi ExpressRoute lub połączenia większej liczby sieci wirtualnych z obwód usługi ExpressRoute, jeśli został włączony dodatek premium usługi ExpressRoute. Sprawdź [— często zadawane pytania](expressroute-faqs.md) więcej informacji na temat używania dodatku premium.


## <a name="before-you-begin"></a>Przed rozpoczęciem

* Przegląd [wymagania wstępne](expressroute-prerequisites.md), [wymagania dotyczące routingu](expressroute-routing.md), i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfiguracji.

* Musisz mieć aktywny obwód usługi ExpressRoute. 
  * Postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md) i który powinien zostać włączony przez dostawcę połączenia. 
  * Upewnij się, że prywatnej komunikacji równorzędnej Azure skonfigurowany dla obwodu. Zobacz [Konfigurowanie routingu](expressroute-howto-routing-arm.md) artykuł, aby uzyskać instrukcje routingu. 
  * Upewnij się, że skonfigurowano prywatnej komunikacji równorzędnej Azure i komunikację równorzędną BGP między siecią a Microsoft działa tak, aby umożliwić łączność end-to-end.
  * Upewnij się, że masz sieci wirtualnej i bramy sieci wirtualnej, utworzona i w pełni zaaprowizowanym. Postępuj zgodnie z instrukcjami, aby [Tworzenie bramy sieci wirtualnej dla usługi ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Brama sieci wirtualnej dla usługi ExpressRoute używa GatewayType "ExpressRoute", nie sieci VPN.

### <a name="working-with-azure-powershell"></a>Praca z programem Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Łączenie sieci wirtualnej w tej samej subskrypcji z obwodem
Możesz połączyć bramę sieci wirtualnej z obwodem usługi ExpressRoute za pomocą następującego polecenia cmdlet. Upewnij się, że brama sieci wirtualnej jest tworzony i jest gotowy do konsolidacji przed uruchomieniem polecenia cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Połączenie sieci wirtualnej w innej subskrypcji z obwodem
Obwód usługi ExpressRoute można udostępniać w wielu subskrypcjach. Na poniższej ilustracji pokazano prosty schemat działa jak udostępniania dla obwodów usługi ExpressRoute w wielu subskrypcjach.

Każdy z mniejszych chmury w chmurze dużych jest używana do reprezentowania subskrypcje, które należą do różnych działów w organizacji. Każdego z działów w organizacji można użyć własnej subskrypcji do wdrażania usług —, ale mogą udostępniać pojedynczy obwód usługi ExpressRoute do łączenia z powrotem do sieci lokalnej. Jednego działu (w tym przykładzie: IT) mogą być właścicielami obwód usługi ExpressRoute. Inne subskrypcje w ramach organizacji, można użyć z obwodem usługi ExpressRoute.

> [!NOTE]
> Połączeniami i przepustowością opłat za obwód usługi ExpressRoute będą stosowane do właściciela subskrypcji. Wszystkie sieci wirtualne współdzielą ten sam przepustowość.
> 
> 

![Łączność między subskrypcjami](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)


### <a name="administration---circuit-owners-and-circuit-users"></a>Administrowanie — obwodu właścicieli i użytkowników obwodu

Właściciela obwodu jest autoryzowanym użytkownikiem Power zasobu obwodu usługi ExpressRoute. Właściciela obwodu tworzyć autoryzacje do zrealizowania przez "Użytkownicy obwodu". Użytkownicy obwodu są właścicielami bram sieci wirtualnych, które nie znajdują się w innej subskrypcji niż obwód usługi ExpressRoute. Użytkownicy obwodu można realizować autoryzacje (jeden autoryzację dla pojedynczej sieci wirtualnej).

Właściciela obwodu ma uprawnienia do modyfikowania i odwołać autoryzacji w dowolnym momencie. Cofanie autoryzacji skutkuje wszystkich połączeń usuwana z subskrypcji, do których dostęp został odwołany.

### <a name="circuit-owner-operations"></a>Operacje właściciela obwodu

**Aby utworzyć autoryzacji**

Właściciela obwodu tworzy autoryzacji. Powoduje to utworzenie klucza autoryzacji, który może służyć przez użytkowników obwodu połączyć ich bramy sieci wirtualnej z obwodem usługi ExpressRoute. Autoryzacja jest prawidłowe dla tylko jedno połączenie.

Poniższy fragment kodu polecenie cmdlet przedstawia sposób tworzenia autoryzacji:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```


Odpowiedzi na to będzie zawierać klucza autoryzacji i stanu:

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded



**Aby zapoznać się z autoryzacjami**

Właściciela obwodu, można przejrzeć wszystkie autoryzacje są wydawane w ramach określonego obwodu, uruchamiając następujące polecenie cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Aby dodać autoryzacji**

Właściciela obwodu, można dodać autoryzacji za pomocą następującego polecenia cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Aby usunąć autoryzacji**

Właściciela obwodu można odwołać/usuwanie autoryzacji dla użytkownika, uruchamiając następujące polecenie cmdlet:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Operacje użytkownika obwodu

Obwód trzeba podać identyfikator elementu równorzędnego i klucza autoryzacji z właściciela obwodu. Klucz autoryzacji jest identyfikatorem GUID.

Identyfikator elementu równorzędnego może zostać sprawdzone z następującego polecenia:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Aby zrealizować autoryzacji połączenia**

Użytkowników obwodu, można uruchomić następujące polecenie cmdlet, aby Zrealizuj autoryzację łącza:

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Aby zwolnić autoryzacji połączenia**

Autoryzacji można zwolnić, usuwając połączenia prowadzący obwód usługi ExpressRoute w sieci wirtualnej.

## <a name="modify-a-virtual-network-connection"></a>Modyfikowanie połączenia sieci wirtualnej
Możesz zaktualizować niektóre właściwości połączenia sieci wirtualnej. 

**Aby zaktualizować wagi połączenia**

Sieci wirtualne mogą być połączone wieloma obwodami usługi ExpressRoute. Ten sam prefiks może pojawić się z więcej niż jeden obwód usługi ExpressRoute. Aby wybór połączenia do wysyłania ruchu przeznaczonego dla tego prefiksu, możesz zmienić *RoutingWeight* połączenia. Ruch będzie wysyłany w połączeniu z najwyższą *RoutingWeight*.

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

Zakres *RoutingWeight* 0 do 32 000. Wartość domyślna to 0.

## <a name="configure-expressroute-fastpath"></a>Konfigurowanie ExpressRoute FastPath 
Możesz włączyć [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) , jeśli obwód ExpressRoute jest na [ExpressRoute Direct](expressroute-erdirect-about.md) , a Brama sieci wirtualnej to Ultra Performance lub ErGw3AZ. FastPath poprawia wydajność ścieżki danych, na przykład pakiety na sekundę i połączenia na sekundę między siecią lokalną i siecią wirtualną. 

> [!NOTE] 
> Jeśli masz już połączenie z siecią wirtualną, ale nie włączono FastPath, musisz usunąć połączenie z siecią wirtualną i utworzyć nowe. 
> 
>  

```azurepowershell-interactive 
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG" 
$gw = Get-AzVirtualNetworkGateway -Name "MyGateway" -ResourceGroupName "MyRG" 
$connection = New-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" -ExpressRouteGatewayBypass -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute -Location "MyLocation" 
``` 

## <a name="next-steps"></a>Następne kroki
Więcej informacji na temat usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).
