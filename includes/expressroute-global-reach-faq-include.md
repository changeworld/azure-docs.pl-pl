---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/19/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 317a480c13c5c6e00653fd61878a379df3f65ac4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182967"
---
### <a name="what-is-expressroute-global-reach"></a>Co to jest usługa ExpressRoute zasięgu globalnym?

Zasięgu globalnym usługi ExpressRoute jest usługą platformy Azure, który nawiązuje połączenie z sieciami lokalnymi za pośrednictwem usługi ExpressRoute za pośrednictwem globalnej sieci firmy Microsoft. Na przykład jeśli masz centrum danych prywatnych w Kalifornii nawiązanie połączenia usługi ExpressRoute w Dolinie Krzemowej i innego centrum danych prywatnych w Teksasie połączona z usługą ExpressRoute w Dallas, przy użyciu usługi ExpressRoute zasięgu globalnym, możesz połączyć swoje centra danych prywatnych razem za pomocą dwóch połączeń usługi ExpressRoute i sieci dla wielu ruch związany z centrum danych będzie przechodzić za pośrednictwem sieci firmy Microsoft w sieci szkieletowej.

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>Jak włączyć lub wyłączyć zasięgu globalnym usługi ExpressRoute?

Włącz zasięgu globalnym usługi ExpressRoute, łącząc ze sobą obwodów usługi ExpressRoute. Tę funkcję można wyłączyć przez odłączenie obwody. Zobacz [konfiguracji](../articles/expressroute/expressroute-howto-set-global-reach.md).

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>Czy muszę mieć ExpressRoute Premium dla usługi ExpressRoute zasięgu globalnym?

Jeśli obwodów usługi ExpressRoute znajdują się w tym samym regionie geograficznymi, nie potrzebujesz ExpressRoute Premium, aby połączyć je ze sobą. Jeśli dwa obwody usługi ExpressRoute znajdują się w różnych regionów geopolitycznych, niezbędna ExpressRoute Premium dla obu obwodów, aby włączyć łączność między nimi. 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>Jak mogę opłata zasięgu globalnym usługi ExpressRoute?

Usługa ExpressRoute zapewnia łączność z sieci lokalnych usług chmurowych firmy Microsoft. Zasięgu globalnym usługi ExpressRoute zapewnia łączność między własne sieciami lokalnymi za pośrednictwem istniejących obwodów usługi ExpressRoute, korzystając z globalnej sieci firmy Microsoft. Zasięgu globalnym usługi ExpressRoute jest rozliczana osobno z istniejącej usługi ExpressRoute. Istnieje dodatkowa opłata włączenie tej funkcji na każdy obwód usługi ExpressRoute. Ruch między sieci lokalnej włączane przez zasięgu globalnym usługi ExpressRoute jest naliczana dla szybkość ruchu wychodzącego w miejscu źródłowym i szybkość transferu danych przychodzących w miejscu docelowym. Kursy są oparte na strefy, w którym znajdują się obwody.

### <a name="where-is-expressroute-global-reach-supported"></a>Gdzie jest zasięgu globalnym ExpressRoute obsługiwane?

Zasięgu globalnym usługi ExpressRoute jest obsługiwana w [Wybierz kraje/regiony lub miejsca](../articles/expressroute/expressroute-global-reach.md). Obwody usługi ExpressRoute musi zostać utworzony w lokalizacji komunikacji równorzędnej w tych krajach/regionach lub miejsca.

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>Czy mogę mieć więcej niż dwie sieci lokalne, każda jest połączona z obwodem usługi ExpressRoute. Można włączyć usługi ExpressRoute zasięgu globalnym do łączenia wszystkich moich sieci w środowisku lokalnym?

Tak, można tak długo, jak obwodów są obsługiwane kraje/regiony. Należy się połączyć dwa obwody usługi ExpressRoute w danym momencie. Do utworzenia w pełni stopniu sieci, należy wyliczyć wszystkie parach obwodów i bezpiecznego powtórzenia konfiguracji. 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>Można włączyć zasięgu globalnym usługi ExpressRoute między dwa obwody usługi ExpressRoute w tej samej lokalizacji komunikacji równorzędnej?

Nie. Dwa obwody muszą pochodzić z różnych lokalizacji komunikacji równorzędnej. Jeśli metro w obsługiwanych kraju/regionu ma więcej niż jednej lokalizacji komunikacji równorzędnej usługi ExpressRoute, możesz połączyć ze sobą obwodów usługi ExpressRoute tworzone w różnych lokalizacjach komunikacji równorzędnej w tym metro. 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>Włączenie zasięgu globalnym usługi ExpressRoute między obwodu X i Y obwodu oraz między obwodu Y i obwodu Z będzie Moje sieciami lokalnymi połączone z obwodem X i obwodu Z komunikować się ze sobą za pośrednictwem sieci firmy Microsoft?

Nie. Aby włączyć łączność między dowolnymi dwiema sieci lokalnej, należy połączyć jawnie odpowiedniego obwodów usługi ExpressRoute. W powyższym przykładzie należy połączyć obwodu X i obwodu Z. 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>Co to jest przepływność sieci, oczekiwanych między Moje sieci w środowisku lokalnym, po włączeniu zasięgu globalnym usługi ExpressRoute?

Przepustowość sieci między sieci w środowisku lokalnym, korzystając z usługi ExpressRoute zasięgu globalnym, jest ograniczone przez mniejszą z dwóch obwodów usługi ExpressRoute. Ruchu lokalnego na platformę Azure ruchu lokalnego do lokalnego współużytkować ten sam obwód i podlegają tym samym ograniczenie przepustowości. 

### <a name="with-expressroute-global-reach-what-are-the-limits-on-the-number-of-routes-i-can-advertise-and-the-number-of-routes-i-will-receive"></a>Za pomocą usługi ExpressRoute zasięgu globalnym jakie są ograniczenia liczby tras, które można było ogłaszać dłuższe i liczby tras, które będę otrzymywać?

Liczba tras, które można anonsować do firmy Microsoft na prywatną komunikację równorzędną Azure pozostaje na poziomie 4000 na obwód standardowy lub 10000 w obwodzie Premium. Liczba tras, które otrzymuje od firmy Microsoft na prywatną komunikację równorzędną Azure będzie sumą tras sieci wirtualnych platformy Azure i tras z sieci lokalnej połączonych za pośrednictwem usługi ExpressRoute zasięgu globalnym. Upewnij się, że Ustaw limit maksymalny prefiksu na routerze w środowisku lokalnym. 

### <a name="what-is-the-sla-for-expressroute-global-reach"></a>Co to jest umowa SLA dla usługi ExpressRoute zasięgu globalnym?

Zasięgu globalnym usługi ExpressRoute zapewnia takie same [umowa SLA gwarantująca dostępność](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/) jako regularne usługi ExpressRoute.
