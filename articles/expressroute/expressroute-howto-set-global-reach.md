---
title: 'Konfigurowanie globalny zasięg — usługi ExpressRoute: Azure | Microsoft Docs'
description: Ten artykuł pomoże Ci połączyć obwodów usługi ExpressRoute razem w celu zapewnienia prywatnych sieci między sieci lokalnych i włączanie zasięgu globalnym.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: ab1098ca65ad92cffdbe1dfb24fd43fcc8f10eae
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54431682"
---
# <a name="configure-expressroute-global-reach-preview"></a>Konfigurowanie usługi ExpressRoute zasięgu globalnym (wersja zapoznawcza)
Ten artykuł ułatwia konfigurowanie usługi ExpressRoute zasięgu globalnym przy użyciu programu PowerShell. Aby uzyskać więcej informacji, zobacz [zasięgu globalnym ExpressRouteRoute](expressroute-global-reach.md).
 
## <a name="before-you-begin"></a>Przed rozpoczęciem
> [!IMPORTANT]
> Tej publicznej wersji zapoznawczej jest oferowana bez umowy dotyczącej poziomu usług i nie powinna być używana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 


Przed rozpoczęciem konfiguracji upewnij się, że:

* Czy po zainstalowaniu najnowszej wersji programu Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
* Zrozumienie aprowizacji obwodu usługi ExpressRoute [przepływy pracy](expressroute-workflows.md).
* Czy obwodów usługi ExpressRoute znajdują się w stanie elastycznie.
* Czy prywatnej komunikacji równorzędnej Azure jest konfigurowany na obwodów usługi ExpressRoute.  

### <a name="sign-in-to-your-azure-account"></a>Zaloguj się do swojego konta platformy Azure
Aby uruchomić konfigurację, zaloguj się do konta platformy Azure. 

Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień, a następnie nawiąż połączenie z kontem. Polecenie wyświetli monit o podanie poświadczeń logowania dla konta platformy Azure.  

```powershell
Connect-AzureRmAccount
```

Jeśli masz wiele subskrypcji platformy Azure, wyświetl subskrypcje dla konta.

```powershell
Get-AzureRmSubscription
```

Wskaż subskrypcję, której chcesz użyć.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identyfikowanie obwodów usługi ExpressRoute dla konfiguracji
Aby umożliwić zasięgu globalnym usługi ExpressRoute między wszystkie dwa obwody usługi ExpressRoute tak długo, jak znajdują się w obsługiwane kraje i zostały utworzone w różnych lokalizacjach komunikacji równorzędnej. Jeśli Twoja subskrypcja jest właścicielem zarówno obwody, można wybrać albo obwodu, Uruchom konfigurację w poniższych sekcjach. 

Jeśli dwa obwody znajdują się w różnych subskrypcjach platformy Azure, konieczne będzie autoryzacji z jedną subskrypcją platformy Azure. Następnie są przekazywane w klucza autoryzacji podczas uruchamiania polecenia konfiguracji w ramach subskrypcji platformy Azure.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Włącz łączność między sieci lokalnej

Użyj następujących poleceń, aby uzyskać obwodu 1 i 2 obwodu. Są dwa obwody w tej samej subskrypcji.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

Uruchom następujące polecenie przed 1 obwodu i przekazać Identyfikatora prywatnej komunikacji równorzędnej obwodu 2. Przy uruchamianiu polecenia, pamiętaj o następujących kwestiach:

* Identyfikator komunikacji równorzędnej prywatnej wygląda podobnie do poniższego przykładu: 

  ```
  /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
  ```
* *-AddressPrefix* musi mieć wartość/29 IPv4 podsieci, na przykład "10.0.0.0/29". Używamy adresów IP w tej podsieci, można ustanowić łączności między dwa obwody usługi ExpressRoute. Nie można używać adresów w tej podsieci, w sieciach wirtualnych platformy Azure lub w sieci lokalnej.

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

Zapisz konfigurację w obwodzie 1 w następujący sposób:
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Po zakończeniu poprzedniej operacji powinny mieć łączność między sieci lokalnej po obu stronach za pośrednictwem dwóch obwodów usługi ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Obwody usługi ExpressRoute w różnych subskrypcjach platformy Azure

Jeśli dwa obwody nie znajdują się w tej samej subskrypcji platformy Azure, konieczne będzie autoryzacji. W następującej konfiguracji autoryzacji jest generowany w subskrypcji obwodu 2 i klucza autoryzacji jest przekazywana do obwodu 1.

Generowanie klucza autoryzacji. 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
Zanotuj Identyfikatora prywatnej komunikacji równorzędnej obwodu 2, a także klucza autoryzacji.

Uruchom następujące polecenie przed 1 obwodu. Przekaż Identyfikatora prywatnej komunikacji równorzędnej obwodu 2 i klucza autoryzacji.
```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

Zapisz konfigurację w obwodzie 1.
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Po zakończeniu poprzedniej operacji powinny mieć łączność między sieci lokalnej po obu stronach za pośrednictwem dwóch obwodów usługi ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Pobierz i Zweryfikuj konfigurację

Użyj następującego polecenia można sprawdzić konfigurację w ramach obwodu, w którym dokonano konfiguracji (na przykład obwodu 1 w poprzednim przykładzie).

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Jeśli po prostu uruchomić *$ckt1* w programie PowerShell, zobacz *CircuitConnectionStatus* w danych wyjściowych. Określa czy "Odłączone", "Połączono" lub nawiązać połączenie. 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Wyłącz łączność między sieci lokalnej

Aby wyłączyć połączenie, Uruchom polecenia względem obwodu, w którym dokonano konfiguracji (na przykład obwodu 1 w poprzednim przykładzie).

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Można uruchomić operację pobierania, aby sprawdzić stan. 

Po wykonaniu poprzedniej operacji nie masz już połączenie między sieci lokalnej za pomocą obwodów usługi ExpressRoute. 


## <a name="next-steps"></a>Kolejne kroki
1. [Dowiedz się więcej o zasięgu globalnym usługi ExpressRoute](expressroute-global-reach.md)
2. [Sprawdź łączność usługi ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Połącz obwodu usługi ExpressRoute z siecią wirtualną platformy Azure](expressroute-howto-linkvnet-arm.md)


