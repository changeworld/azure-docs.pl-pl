---
title: Globalny zasięg usługi Azure ExpressRoute | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże Ci połączyć obwodów usługi ExpressRoute razem w celu zapewnienia prywatnych sieci między sieci lokalnych i włączanie zasięgu globalnym.
documentationcenter: na
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 90f34da10e0193e31a0f70187463799013f52be8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966819"
---
# <a name="link-expressroute-circuits-to-enable-expressroute-global-reach-preview"></a>Obwody usługi ExpressRoute łącze umożliwiające ExpressRoute zasięgu globalnym (wersja zapoznawcza)

Usługa ExpressRoute jest prywatny i odporne na błędy sposób łączenia sieci lokalnej Microsoft Cloud. Wiele usług w chmurze firmy Microsoft, takich jak Azure, Office 365 i Dynamics 365 dostęp z Twojego Centrum danych prywatnych lub sieci firmowej. Na przykład masz oddziału w San Francisco z obwodem usługi ExpressRoute w Dolinie Krzemowej i innego oddziału firmy w Baltimore z obwodem usługi ExpressRoute w Waszyngtonie. 

Obu biur oddziałów mogą mieć łączność o dużej szybkości do zasobów platformy Azure w regionie wschodnie stany USA i zachodnie stany USA. Jednak oddziałów nie wymiany danych bezpośrednio ze sobą. Innymi słowy 10.0.1.0/24 mogą wymieniać dane z 10.0.3.0/24 i 10.0.4.0/24, ale nie z 10.0.2.0/24.

![Bez][1]

Za pomocą **zasięgu globalnym ExpressRoute**, możesz połączyć obwodów usługi ExpressRoute, dzięki czemu sieć danych prywatnych między sieci lokalnej. W powyższym przykładzie, dodając ExpressRoute zasięgu globalnym San Francisco pakietu office (10.0.1.0/24) mogą bezpośrednio wymiany danych z biura Baltimore (10.0.2.0/24) za pośrednictwem istniejących obwodów usługi ExpressRoute i za pośrednictwem globalnej sieci firmy Microsoft. 

![za pomocą][2]

Zasięgu globalnym usługi ExpressRoute jest obecnie obsługiwane w następujących krajach:

* Stany Zjednoczone
* Wielka Brytania 
* Japonia

Utworzono obwodów usługi ExpressRoute [lokalizacje komunikacji równorzędnej usługi ExpressRoute](expressroute-locations.md) w tych krajach. Aby włączyć usługi ExpressRoute zasięgu globalnym między [różnych regionów geopolitycznych](expressroute-locations.md), obwodów usługi musi być jednostki SKU Premium.


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
Aby umożliwić zasięgu globalnym usługi ExpressRoute między wszystkie dwa obwody usługi ExpressRoute tak długo, jak znajdują się one w obsługiwane kraje i są tworzone w różnych lokalizacjach komunikacji równorzędnej. Jeśli Twoja subskrypcja jest właścicielem zarówno obwody, można wybrać obu obwodu, Uruchom konfigurację w poniższych sekcjach. Jeśli dwa obwody należą do różnych subskrypcji platformy Azure, należy autoryzacji z jedną subskrypcją platformy Azure, a przekazać klucza autoryzacji podczas uruchamiania polecenia konfiguracji w ramach subskrypcji platformy Azure.

## <a name="to-enable-connectivity-between-your-on-premises-networks"></a>Aby włączyć łączność między sieci lokalnej

Użyj następujących poleceń, aby uzyskać obwodu 1 i 2 obwodu. Są dwa obwody w tej samej subskrypcji.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

Uruchom następujące polecenie przed 1 obwodu i przekaż w identyfikatora obwodu 2 prywatną komunikację równorzędną firmy.

Należy zwrócić uwagę na to, że prywatnej komunikacji równorzędnej firmy identyfikator wygląda następująco:

`/subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering`


```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

*- AddressPrefix* musi mieć wartość/29 IPv4 podsieci, na przykład "10.0.0.0/29". Firma Microsoft użyje adresów IP w tej podsieci można ustanowić łączności między dwa obwody usługi ExpressRoute. Nie należy używać adresów w tej podsieci, w Twoich sieciach wirtualnych platformy Azure lub w sieci lokalnej. 


Zapisz konfigurację w obwodzie 1.

```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Po zakończeniu poprzedniej operacji powinny mieć łączność między sieci lokalnej po obu stronach za pośrednictwem dwóch obwodów usługi ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Obwody usługi ExpressRoute w różnych subskrypcjach platformy Azure

Jeśli dwa obwody nie znajdują się w tej samej subskrypcji platformy Azure, konieczne będzie autoryzacji. W następującej konfiguracji autoryzacji jest generowany w ramach subskrypcji z obwodem 2 i klucza autoryzacji jest przekazywana do obwodu 1.

Generowanie klucza autoryzacji. 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
Zanotuj identyfikator obwodu 2 prywatną komunikację równorzędną firmy, a także klucza autoryzacji.

Uruchom następujące polecenie przed 1 obwodu. Przekaż 2 prywatnej komunikacji równorzędnej obwodu przez identyfikator i klucz autoryzacji

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

Zapisz konfigurację w obwodzie 1.

```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Po zakończeniu poprzedniej operacji powinny mieć łączność między sieci lokalnej po obu stronach za pośrednictwem dwóch obwodów usługi ExpressRoute.

## <a name="to-get-and-verify-the-configuration"></a>Aby pobrać i zweryfikować konfigurację

Użyj następującego polecenia aby zweryfikować konfigurację w ramach obwodu, w którym dokonano konfiguracji. W tym przykładzie użyto obwodu 1 z poprzedniego przykładu.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Jeśli po prostu uruchomić *$ckt1* w programie PowerShell, zobaczysz *CircuitConnectionStatus* w danych wyjściowych. Jego temu wiadomo, czy połączenie zostanie nawiązane, "Połączono", czy nie, "Odłączone". 

## <a name="to-disable-connectivity-between-your-on-premises-networks"></a>Aby wyłączyć łączność między sieci lokalnej

Aby wyłączyć, Uruchom polecenia względem obwodu, w którym dokonano konfiguracji. W tym przykładzie użyto obwodu 1 z poprzednich przykładów.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Można uruchomić operację pobierania, aby sprawdzić stan. 

Po wykonaniu powyższych operacji już nie będą mieć łączność między sieci lokalnej za pomocą obwodów usługi ExpressRoute. 

## <a name="next-steps"></a>Kolejne kroki
1. [Dowiedz się więcej o zasięgu globalnym usługi ExpressRoute](expressroute-faqs.md#globalreach)
2. [Sprawdź łączność usługi ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Połącz obwód usługi ExpressRoute z sieci wirtualnej platformy Azure](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "Diagram bez zasięgu globalnym"
[2]: ./media/expressroute-global-reach/2.png "Diagram o zasięgu globalnym"