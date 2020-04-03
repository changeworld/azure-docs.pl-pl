---
title: 'Usługa ExpressRoute: łączenie sieci wirtualnej z obwódem: Program Azure PowerShell'
description: Ten dokument zawiera omówienie sposobu łączenia sieci wirtualnych (sieci wirtualnych) z obwodami usługi ExpressRoute przy użyciu modelu wdrażania Menedżera zasobów i programu PowerShell.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 05/20/2018
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 755b1898ee4cbc32de3a65a6bbc368ecf3eb3acf
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616380"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Łączenie sieci wirtualnej z obwodem usługi ExpressRoute
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-linkvnet-cli.md)
> * [Klip wideo — portal Platformy Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-linkvnet-classic.md)
>

Ten artykuł ułatwia łączenie sieci wirtualnych (sieci wirtualnych) z obwodami usługi Azure ExpressRoute przy użyciu modelu wdrażania usługi Resource Manager i programu PowerShell. Sieci wirtualne mogą być w tej samej subskrypcji lub części innej subskrypcji. W tym artykule pokazano również, jak zaktualizować łącze sieci wirtualnej.

* Do standardowego obwodu usługi ExpressRoute można połączyć do 10 sieci wirtualnych. Wszystkie sieci wirtualne muszą znajdować się w tym samym regionie geopolitycznym podczas korzystania ze standardowego obwodu usługi ExpressRoute. 

* Pojedyncza sieci wirtualnej mogą być połączone z maksymalnie czterech obwodów usługi ExpressRoute. Czynności opisane w tym artykule służą do tworzenia nowego obiektu połączenia dla każdego obwodu usługi ExpressRoute, z który się łączysz. Obwody usługi ExpressRoute mogą być w tej samej subskrypcji, różnych subskrypcji lub kombinacji obu.

* Można połączyć sieci wirtualne poza region geopolityczny obwodu usługi ExpressRoute lub podłączyć większą liczbę sieci wirtualnych do obwodu usługi ExpressRoute, jeśli włączono dodatek Usługi ExpressRoute premium. Więcej informacji na temat dodatku premium można znaleźć w [często zadawanych](expressroute-faqs.md) pytaniach.


## <a name="before-you-begin"></a>Przed rozpoczęciem

* Przed rozpoczęciem konfiguracji należy zapoznać się [z wymaganiami wstępnymi,](expressroute-prerequisites.md) [wymaganiami dotyczącymi routingu](expressroute-routing.md)i [przepływami pracy.](expressroute-workflows.md)

* Musisz mieć aktywny obwód usługi ExpressRoute. 
  * Postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md) i włączyć obwód przez dostawcę łączności. 
  * Upewnij się, że masz skonfigurowany private peering platformy Azure dla obwodu. Zapoznaj się z artykułem [konfigurowania routingu,](expressroute-howto-routing-arm.md) aby uzyskać instrukcje dotyczące routingu. 
  * Upewnij się, że prywatna komunikacja równorzędna platformy Azure jest skonfigurowana, a komunikacja równorzędna BGP między siecią a firmą Microsoft jest w górę, dzięki czemu można włączyć łączność end-to-end.
  * Upewnij się, że masz sieć wirtualną i bramę sieci wirtualnej utworzone i w pełni aprowizowana. Postępuj zgodnie z instrukcjami, aby [utworzyć bramę sieci wirtualnej dla usługi ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Brama sieci wirtualnej dla usługi ExpressRoute używa gatewayType "ExpressRoute", a nie SIECI VPN.

### <a name="working-with-azure-powershell"></a>Praca z programem Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Łączenie sieci wirtualnej w tej samej subskrypcji z obwodem
Bramę sieci wirtualnej można połączyć z obwodem usługi ExpressRoute przy użyciu następującego polecenia cmdlet. Przed uruchomieniem polecenia cmdlet upewnij się, że brama sieci wirtualnej jest tworzona i jest gotowa do łączenia:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Połączenie sieci wirtualnej w innej subskrypcji z obwodem
Obwód usługi ExpressRoute można udostępniać w wielu subskrypcjach. Na poniższej ilustracji przedstawiono prosty schemat działania udostępniania dla obwodów usługi ExpressRoute w wielu subskrypcjach.

Każda z mniejszych chmur w dużej chmurze jest używana do reprezentowania subskrypcji należących do różnych działów w organizacji. Każdy z działów w organizacji może używać własnej subskrypcji do wdrażania swoich usług — ale może udostępniać jeden obwód usługi ExpressRoute, aby połączyć się z powrotem do sieci lokalnej. Jeden dział (w tym przykładzie: IT) może być właścicielem obwodu usługi ExpressRoute. Inne subskrypcje w organizacji mogą korzystać z obwodu usługi ExpressRoute.

> [!NOTE]
> Opłaty za łączność i przepustowość dla obwodu usługi ExpressRoute zostaną zastosowane do właściciela subskrypcji. Wszystkie sieci wirtualne mają tę samą przepustowość.
> 
> 

![Łączność między subskrypcjami](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)


### <a name="administration---circuit-owners-and-circuit-users"></a>Administracja - właściciele obwodów i użytkownicy obwodów

"Właściciel obwodu" jest autoryzowanym użytkownikiem zasilania zasobu obwodu usługi ExpressRoute. Właściciel obwodu może tworzyć autoryzacje, które mogą być realizowane przez "użytkowników obwodu". Użytkownicy obwodów są właścicielami bram sieci wirtualnej, które nie są w ramach tej samej subskrypcji co obwód usługi ExpressRoute. Użytkownicy obwodów mogą realizować autoryzacje (jedna autoryzacja na sieć wirtualną).

Właściciel obwodu ma prawo do modyfikowania i cofania autoryzacji w dowolnym momencie. Cofnięcie autoryzacji powoduje usunięcie wszystkich połączeń łączy z subskrypcji, której dostęp został odwołany.

### <a name="circuit-owner-operations"></a>Operacje właściciela obwodu

**Aby utworzyć autoryzację**

Właściciel obwodu tworzy autoryzację. Powoduje to utworzenie klucza autoryzacji, który może być używany przez użytkownika obwodu do łączenia swoich bram sieci wirtualnej z obwodem usługi ExpressRoute. Autoryzacja jest ważna tylko dla jednego połączenia.

Poniższy fragment polecenia cmdlet pokazuje, jak utworzyć autoryzację:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```


Odpowiedź na to będzie zawierać klucz autoryzacji i stan:

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded



**Aby przejrzeć autoryzacje**

Właściciel obwodu może przejrzeć wszystkie uprawnienia wydane na określonym obwodzie, uruchamiając następujące polecenie cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Aby dodać uprawnienia**

Właściciel obwodu może dodawać autoryzacje przy użyciu następującego polecenia cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Aby usunąć autoryzacje**

Właściciel obwodu może odwołać/usunąć autoryzacje do użytkownika, uruchamiając następujące polecenie cmdlet:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Operacje użytkownika obwodu

Użytkownik obwodu potrzebuje identyfikatora równorzędnego i klucza autoryzacji od właściciela obwodu. Klucz autoryzacji jest identyfikatorem GUID.

Identyfikator elementu równorzędnego można sprawdzić za pomocą następującego polecenia:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Aby zrealizować autoryzację połączenia**

Użytkownik obwodu może uruchomić następujące polecenie cmdlet, aby zrealizować autoryzację łącza:

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Aby zwolnić autoryzację połączenia**

Autoryzację można zwolnić, usuwając połączenie łączące obwód usługi ExpressRoute z siecią wirtualną.

## <a name="modify-a-virtual-network-connection"></a>Modyfikowanie połączenia sieci wirtualnej
Można zaktualizować niektóre właściwości połączenia z siecią wirtualną. 

**Aby zaktualizować masę połączenia**

Sieć wirtualna może być podłączona do wielu obwodów usługi ExpressRoute. Ten sam prefiks może zostać wyświetlony z więcej niż jednego obwodu usługi ExpressRoute. Aby wybrać połączenie, które ma być wysyłane dla tego prefiksu, można zmienić *szerokość trasy* połączenia. Ruch zostanie wysłany na połączenie o najwyższej *szerokości trasy*.

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

Zakres *routinguwaga* jest 0 do 32000. Wartość domyślna to 0.

## <a name="configure-expressroute-fastpath"></a>Konfigurowanie programu ExpressRoute FastPath 
Program [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) można włączyć, jeśli brama sieci wirtualnej to Ultra Performance lub ErGw3AZ. FastPath zwiększa wydajność ścieżki danych, takich jak pakiety na sekundę i połączenia na sekundę między siecią lokalną a siecią wirtualną. 

**Konfigurowanie programu FastPath przy nowym połączeniu**

```azurepowershell-interactive 
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG" 
$gw = Get-AzVirtualNetworkGateway -Name "MyGateway" -ResourceGroupName "MyRG" 
$connection = New-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" -ExpressRouteGatewayBypass -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute -Location "MyLocation" 
``` 

**Aktualizowanie istniejącego połączenia w celu włączenia programu FastPath**

```azurepowershell-interactive 
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" 
$connection.ExpressRouteGatewayBypass = $True
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
``` 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat usługi ExpressRoute, zobacz [często zadawane pytania dotyczące usługi ExpressRoute](expressroute-faqs.md).
