---
title: Konfigurowanie usługi Azure ExpressRoute, zasięgu globalnym | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże Ci połączyć obwodów usługi ExpressRoute razem w celu zapewnienia prywatnych sieci między sieci lokalnych i włączanie zasięgu globalnym.
documentationcenter: na
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: cherylmc
ms.openlocfilehash: 4006626f9768289e75ccd61a1ef0bad5389f0a7a
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49071233"
---
# <a name="configure-expressroute-global-reach-preview"></a>Konfigurowanie usługi ExpressRoute o globalnym zasięgu (wersja zapoznawcza)
Ten artykuł ułatwia konfigurowanie usługi ExpressRoute zasięgu globalnym przy użyciu programu PowerShell. Aby uzyskać więcej informacji, zobacz [zasięgu globalnym ExpressRouteRoute](expressroute-global-reach.md).
 
## <a name="before-you-begin"></a>Przed rozpoczęciem
> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 


Przed rozpoczęciem konfiguracji należy sprawdzić następujące wymagania.

* Zainstaluj najnowszą wersję programu Azure PowerShell. Zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-azurerm-ps).
* Zrozumienie aprowizacji obwodu usługi ExpressRoute [przepływy pracy](expressroute-workflows.md).
* Upewnij się, że obwodów usługi ExpressRoute znajdują się w stanie Aprowizowana.
* Upewnij się, że prywatnej komunikacji równorzędnej Azure jest skonfigurowany na obwodów usługi ExpressRoute.  

### <a name="log-into-your-azure-account"></a>Zaloguj się do konta platformy Azure
Aby uruchomić konfigurację, należy zalogować się do konta platformy Azure. 

Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i połącz się ze swoim kontem. Polecenie wyświetli monit o poświadczenia logowania dla konta platformy Azure.  

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
Aby umożliwić zasięgu globalnym usługi ExpressRoute między wszystkie dwa obwody usługi ExpressRoute tak długo, jak znajdują się one w obsługiwane kraje i są tworzone w różnych lokalizacjach komunikacji równorzędnej. Jeśli Twoja subskrypcja jest właścicielem zarówno obwodów można albo obwodu, Uruchom konfigurację w poniższych sekcjach. Jeśli dwa obwody należą do różnych subskrypcji platformy Azure, należy autoryzacji z jedną subskrypcją platformy Azure, a przekazać klucza autoryzacji podczas uruchamiania polecenia konfiguracji w ramach subskrypcji platformy Azure.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Włącz łączność między sieci lokalnej

Użyj następujących poleceń, aby uzyskać obwodu 1 i 2 obwodu. Są dwa obwody w tej samej subskrypcji.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

Uruchom następujące polecenie przed 1 obwodu i przekaż w identyfikatora obwodu 2 prywatną komunikację równorzędną firmy.

> [!NOTE]
> Prywatna komunikacja równorzędna firmy identyfikator wygląda */subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering*
> 
>

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

> [!IMPORTANT]
> *-AddressPrefix* musi mieć wartość/29 IPv4 podsieci, np. "10.0.0.0/29". Firma Microsoft użyje adresów IP w tej podsieci można ustanowić łączności między dwa obwody usługi ExpressRoute. Nie można używać adresów w tej podsieci w sieci wirtualne platformy Azure lub w sieci lokalnej.
> 



Zapisz konfigurację w obwodzie 1
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Po ukończeniu powyższych operacji powinny mieć łączność między sieci lokalnej po obu stronach za pośrednictwem dwóch obwodów usługi ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Obwody usługi ExpressRoute w różnych subskrypcjach platformy Azure

Jeśli dwa obwody nie znajdują się w tej samej subskrypcji platformy Azure, konieczne będzie autoryzacji. W następującej konfiguracji autoryzacji jest generowany w ramach subskrypcji z obwodem 2 i klucza autoryzacji jest przekazywana do obwodu 1.

Generowanie klucza autoryzacji. 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
Zanotuj obwodu 2 identyfikator prywatną komunikację równorzędną firmy, a także klucza autoryzacji.

Uruchom następujące polecenie przed 1 obwodu. Przekaż 2 prywatnej komunikacji równorzędnej obwodu przez identyfikator i klucz autoryzacji 
```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

Zapisz konfigurację w obwodzie 1
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Po ukończeniu powyższych operacji powinny mieć łączność między sieci lokalnej po obu stronach za pośrednictwem dwóch obwodów usługi ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Pobierz i Zweryfikuj konfigurację

Użyj następującego polecenia aby zweryfikować konfigurację w ramach obwodu, w którym konfiguracji została wprowadzona, czyli obwodu 1 w powyższym przykładzie.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Jeśli po prostu uruchomić *$ckt1* w programie PowerShell zostanie wyświetlony *CircuitConnectionStatus* w danych wyjściowych. Jego temu wiadomo, czy połączenie zostanie nawiązane, "Połączono", czy nie, "Odłączone". 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Wyłącz łączność między sieci lokalnej

Aby je wyłączyć, uruchamiać polecenia obwodu gdzie konfiguracja została wprowadzona, czyli obwodu 1 w powyższym przykładzie.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Można uruchomić operację pobierania, aby sprawdzić stan. 

Po wykonaniu powyższych operacji już nie będą mieć łączność między sieci lokalnej za pomocą obwodów usługi ExpressRoute. 


## <a name="next-steps"></a>Następne kroki
1. [Dowiedz się więcej o zasięgu globalnym usługi ExpressRoute](expressroute-global-reach.md)
2. [Sprawdź łączność usługi ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Połącz obwód usługi ExpressRoute z sieci wirtualnej platformy Azure](expressroute-howto-linkvnet-arm.md)


