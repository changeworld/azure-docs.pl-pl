---
title: 'Azure ExpressRoute: Konfigurowanie globalnego zasięgu'
description: Ten artykuł ułatwia łączenie obwodów usługi ExpressRoute w celu tworzenia sieci prywatnej między sieciami lokalnymi i włączania globalnego zasięgu.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.openlocfilehash: 76de7a8854a58deb924cbbe3177ad5a7b5fd57a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083465"
---
# <a name="configure-expressroute-global-reach"></a>Konfigurowanie usługi ExpressRoute Global Reach

Ten artykuł ułatwia konfigurowanie globalnego zasięgu usługi ExpressRoute przy użyciu programu PowerShell. Aby uzyskać więcej informacji, zobacz [Usługa ExpressRouteRoute Global Reach](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji upewnij się, że:

* Rozumiesz [przepływy pracy](expressroute-workflows.md)inicjowania obsługi administracyjnej obwodów usługi ExpressRoute .
* Obwody usługi ExpressRoute są w stanie aprowidywizacji.
* Prywatna komunikacja równorzędna platformy Azure jest skonfigurowana w obwodach usługi ExpressRoute.
* Jeśli chcesz uruchomić program PowerShell lokalnie, sprawdź, czy na komputerze jest zainstalowana najnowsza wersja programu Azure PowerShell.

### <a name="working-with-azure-powershell"></a>Praca z programem Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>Identyfikowanie obwodów

1. Aby rozpocząć konfigurację, zaloguj się do konta platformy Azure i wybierz subskrypcję, której chcesz użyć.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Zidentyfikuj obwody usługi ExpressRoute, których chcesz użyć. Globalny zasięg usługi ExpressRoute można włączyć między dwoma obwodami usługi ExpressRoute, o ile znajdują się one w obsługiwanych krajach/regionach i zostały utworzone w różnych lokalizacjach komunikacji równorzędnej. 

   * Jeśli subskrypcja jest właścicielem obu obwodów, można wybrać jeden z obwodów, aby uruchomić konfigurację w poniższych sekcjach.
   * Jeśli dwa obwody są w różnych subskrypcji platformy Azure, należy autoryzacji z jednej subskrypcji platformy Azure. Następnie należy przekazać klucz autoryzacji po uruchomieniu polecenia konfiguracji w innej subskrypcji platformy Azure.

## <a name="enable-connectivity"></a>Włącz łączność

Włącz łączność między sieciami lokalnymi. Istnieją oddzielne zestawy instrukcji dla obwodów, które są w tej samej subskrypcji platformy Azure i obwodów, które są różne subskrypcje.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>Obwody usługi ExpressRoute w tej samej subskrypcji platformy Azure

1. Użyj następujących poleceń, aby uzyskać obwód 1 i obwód 2. Dwa obwody są w tej samej subskrypcji.

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. Uruchom następujące polecenie względem obwodu 1 i przekaż prywatny identyfikator komunikacji równorzędnej obwodu 2. Podczas uruchamiania polecenia należy zwrócić uwagę na następujące kwestie:

   * Prywatny identyfikator komunikacji równorzędnej wygląda podobnie do następującego przykładu: 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *-AddressPrefix* musi być podsiecią IPv4 /29, na przykład "10.0.0.0/29". Używamy adresów IP w tej podsieci do ustanawiania łączności między dwoma obwodami usługi ExpressRoute. Nie należy używać adresów w tej podsieci w sieci wirtualnej platformy Azure lub w sieci lokalnej.

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. Zapisz konfigurację w obwodzie 1 w następujący sposób:

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Po zakończeniu poprzedniej operacji będziesz mieć łączność między sieciami lokalnymi po obu stronach za pośrednictwem dwóch obwodów usługi ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Obwody usługi ExpressRoute w różnych subskrypcjach platformy Azure

Jeśli dwa obwody nie są w tej samej subskrypcji platformy Azure, potrzebujesz autoryzacji. W następującej konfiguracji autoryzacja jest generowana w subskrypcji obwodu 2, a klucz autoryzacji jest przekazywany do obwodu 1.

1. Generowanie klucza autoryzacji.

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   Zanotuj prywatny identyfikator komunikacji równorzędnej obwodu 2, a także klucz autoryzacji.
2. Uruchom następujące polecenie względem obwodu 1. Przekaż prywatny identyfikator komunikacji równorzędnej obwodu 2 i klucz autoryzacji.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. Zapisz konfigurację w obwodzie 1.

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Po zakończeniu poprzedniej operacji będziesz mieć łączność między sieciami lokalnymi po obu stronach za pośrednictwem dwóch obwodów usługi ExpressRoute.

## <a name="verify-the-configuration"></a>Sprawdzanie konfiguracji

Użyj następującego polecenia, aby sprawdzić konfigurację obwodu, w którym została wykonana konfiguracja (na przykład obwód 1 w poprzednim przykładzie).
```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Jeśli po prostu uruchomić *$ckt1* w programie PowerShell, zobaczysz *CircuitConnectionStatus* w danych wyjściowych. Informuje, czy łączność jest ustanowiona, "Połączone" lub "Rozłączone". 

## <a name="disable-connectivity"></a>Wyłącz łączność

Aby wyłączyć łączność między sieciami lokalnymi, uruchom polecenia względem obwodu, w którym została wykonana konfiguracja (na przykład obwód 1 w poprzednim przykładzie).

```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Można uruchomić Get operacji, aby zweryfikować stan.

Po zakończeniu poprzedniej operacji nie masz już łączności między siecią lokalną za pośrednictwem obwodów usługi ExpressRoute.

## <a name="next-steps"></a>Następne kroki
1. [Dowiedz się więcej o globalnym zasięgu usługi ExpressRoute](expressroute-global-reach.md)
2. [Weryfikowanie łączności usługi ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Łączenie obwodu usługi ExpressRoute z siecią wirtualną platformy Azure](expressroute-howto-linkvnet-arm.md)
