---
title: 'Konfigurowanie globalny zasięg — usługi ExpressRoute: Azure | Microsoft Docs'
description: Ten artykuł pomoże Ci połączyć obwodów usługi ExpressRoute razem w celu zapewnienia prywatnych sieci między sieci lokalnych i włączanie zasięgu globalnym.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: de9cbd9cfac766e2a67274684d3fb6b447e45200
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64572756"
---
# <a name="configure-expressroute-global-reach"></a>Konfigurowanie usługi ExpressRoute Global Reach

Ten artykuł ułatwia konfigurowanie usługi ExpressRoute zasięgu globalnym przy użyciu programu PowerShell. Aby uzyskać więcej informacji, zobacz [zasięgu globalnym ExpressRouteRoute](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji upewnij się, że:

* Rozumiesz aprowizacji obwodu usługi ExpressRoute [przepływy pracy](expressroute-workflows.md).
* Obwodów usługi ExpressRoute znajdują się w stanie elastycznie.
* Prywatnej komunikacji równorzędnej Azure jest konfigurowany na obwodów usługi ExpressRoute.
* Jeśli chcesz uruchomić program PowerShell lokalnie, upewnij się, że najnowszą wersję programu Azure PowerShell jest zainstalowany na tym komputerze.

### <a name="working-with-azure-powershell"></a>Praca z programem Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>Identyfikowanie obwodów

1. Aby uruchomić konfigurację, zaloguj się do konta platformy Azure i wybierz subskrypcję, której chcesz użyć.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Zidentyfikuj obwodów usługi ExpressRoute, którego chcesz używać. Aby umożliwić zasięgu globalnym usługi ExpressRoute między wszystkie dwa obwody usługi ExpressRoute tak długo, jak one znajdują się w obsługiwane kraje/regiony i zostały utworzone w różnych lokalizacjach komunikacji równorzędnej. 

   * Jeśli Twoja subskrypcja jest właścicielem zarówno obwody, można wybrać albo obwodu, Uruchom konfigurację w poniższych sekcjach.
   * Jeśli dwa obwody znajdują się w różnych subskrypcjach platformy Azure, konieczne będzie autoryzacji z jedną subskrypcją platformy Azure. Następnie są przekazywane w klucza autoryzacji podczas uruchamiania polecenia konfiguracji w ramach subskrypcji platformy Azure.

## <a name="enable-connectivity"></a>Włącz połączenia

Włącz łączność między sieci lokalnej. Istnieją osobne zestawy instrukcji dla obwodów, które znajdują się w tej samej subskrypcji platformy Azure i obwody, które są w różnych subskrypcjach.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>Obwody usługi ExpressRoute w tej samej subskrypcji platformy Azure

1. Użyj następujących poleceń, aby uzyskać obwodu 1 i 2 obwodu. Są dwa obwody w tej samej subskrypcji.

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. Uruchom następujące polecenie przed 1 obwodu i przekazać Identyfikatora prywatnej komunikacji równorzędnej obwodu 2. Przy uruchamianiu polecenia, pamiętaj o następujących kwestiach:

   * Identyfikator komunikacji równorzędnej prywatnej wygląda podobnie do poniższego przykładu: 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *-AddressPrefix* musi mieć wartość/29 IPv4 podsieci, na przykład "10.0.0.0/29". Używamy adresów IP w tej podsieci, można ustanowić łączności między dwa obwody usługi ExpressRoute. Nie można używać adresów w tej podsieci, w sieciach wirtualnych platformy Azure lub w sieci lokalnej.

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. Zapisz konfigurację w obwodzie 1 w następujący sposób:

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Po zakończeniu poprzedniej operacji, będą mieć łączność między sieci lokalnej po obu stronach za pośrednictwem dwóch obwodów usługi ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Obwody usługi ExpressRoute w różnych subskrypcjach platformy Azure

Jeśli dwa obwody nie znajdują się w tej samej subskrypcji platformy Azure, konieczne będzie autoryzacji. W następującej konfiguracji autoryzacji jest generowany w subskrypcji obwodu 2 i klucza autoryzacji jest przekazywana do obwodu 1.

1. Generowanie klucza autoryzacji.

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   Zanotuj Identyfikatora prywatnej komunikacji równorzędnej obwodu 2, a także klucza autoryzacji.
2. Uruchom następujące polecenie przed 1 obwodu. Przekaż Identyfikatora prywatnej komunikacji równorzędnej obwodu 2 i klucza autoryzacji.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. Zapisz konfigurację w obwodzie 1.

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Po zakończeniu poprzedniej operacji, będą mieć łączność między sieci lokalnej po obu stronach za pośrednictwem dwóch obwodów usługi ExpressRoute.

## <a name="verify-the-configuration"></a>Sprawdzanie konfiguracji

Użyj następującego polecenia można sprawdzić konfigurację w ramach obwodu, w którym dokonano konfiguracji (na przykład obwodu 1 w poprzednim przykładzie).
```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Jeśli po prostu uruchomić *$ckt1* w programie PowerShell, zobacz *CircuitConnectionStatus* w danych wyjściowych. Określa czy "Odłączone", "Połączono" lub nawiązać połączenie. 

## <a name="disable-connectivity"></a>Wyłącz łączności

Aby wyłączyć połączenie między siecią lokalną siecią, uruchamiać polecenia obwodu którym dokonano konfiguracji (na przykład obwód 1 w poprzednim przykładzie).

```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Można uruchomić operację pobierania, aby sprawdzić stan.

Po wykonaniu poprzedniej operacji nie masz już połączenie między sieci lokalnej za pomocą obwodów usługi ExpressRoute.

## <a name="next-steps"></a>Kolejne kroki
1. [Dowiedz się więcej o zasięgu globalnym usługi ExpressRoute](expressroute-global-reach.md)
2. [Sprawdź łączność usługi ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Połącz obwodu usługi ExpressRoute z siecią wirtualną platformy Azure](expressroute-howto-linkvnet-arm.md)
