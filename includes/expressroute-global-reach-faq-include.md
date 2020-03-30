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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67182967"
---
### <a name="what-is-expressroute-global-reach"></a>Co to jest globalny zasięg usługi ExpressRoute?

Usługa ExpressRoute Global Reach to usługa platformy Azure, która łączy sieci lokalne za pośrednictwem usługi ExpressRoute za pośrednictwem globalnej sieci firmy Microsoft. Jeśli na przykład prywatne centrum danych w Kalifornii jest połączone z usługą ExpressRoute w Dolinie Krzemowej i innym prywatnym centrum danych w Teksasie połączonym z usługą ExpressRoute w Dallas, z usługą ExpressRoute Global Reach, możesz połączyć swoje prywatne centra danych za pośrednictwem dwóch połączeń usługi ExpressRoute, a ruch między centrami danych będzie przechodził przez szkielet sieci firmy Microsoft.

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>Jak włączyć lub wyłączyć globalny zasięg usługi ExpressRoute?

Włącz globalny zasięg usługi ExpressRoute, łącząc ze sobą obwody usługi ExpressRoute. Wyłącz tę funkcję, odłączając obwody. Zobacz [konfigurację](../articles/expressroute/expressroute-howto-set-global-reach.md).

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>Czy potrzebuję usługi ExpressRoute Premium dla usługi ExpressRoute Global Reach?

Jeśli obwody usługi ExpressRoute znajdują się w tym samym regionie geopolitycznym, nie potrzebujesz usługi ExpressRoute Premium, aby połączyć je ze sobą. Jeśli dwa obwody usługi ExpressRoute znajdują się w różnych regionach geopolitycznych, potrzebujesz usługi ExpressRoute Premium dla obu obwodów, aby umożliwić łączność między nimi. 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>Jak naliczona zostanie opłata za globalny zasięg usługi ExpressRoute?

Usługa ExpressRoute umożliwia łączność z sieci lokalnej z usługami w chmurze firmy Microsoft. Usługa ExpressRoute Global Reach umożliwia łączność między własnymi sieciami lokalnymi za pośrednictwem istniejących obwodów usługi ExpressRoute, wykorzystując globalną sieć firmy Microsoft. Usługa ExpressRoute Global Reach jest rozliczana oddzielnie od istniejącej usługi Usługi ExpressRoute. Za włączenie tej funkcji w każdym obwodzie usługi ExpressRoute pobierana jest dodatkowa opłata. Ruch między sieciami lokalnymi włączonymi przez globalny zasięg usługi ExpressRoute będzie rozliczany za szybkość ruchu wychodzącego u źródła i za szybkość transferu danych przychodzących w miejscu docelowym. Ceny są oparte na strefie, w której znajdują się obwody.

### <a name="where-is-expressroute-global-reach-supported"></a>Gdzie jest obsługiwany globalny zasięg usługi ExpressRoute?

Globalny zasięg usługi ExpressRoute jest obsługiwany w [wybranych krajach/regionach lub miejscach.](../articles/expressroute/expressroute-global-reach.md) Obwody usługi ExpressRoute muszą być tworzone w lokalizacjach komunikacji równorzędnej w tych krajach/regionach lub miejscach.

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>Mam więcej niż dwie sieci lokalne, z których każda jest podłączona do obwodu usługi ExpressRoute. Czy mogę włączyć usługę ExpressRoute Global Reach, aby połączyć ze sobą wszystkie moje sieci lokalne?

Tak, możesz, o ile obwody znajdują się w obsługiwanych krajach/regionach. Należy podłączyć dwa obwody usługi ExpressRoute naraz. Aby utworzyć sieć w pełni oczkach, należy wyliczyć wszystkie pary obwodów i powtórzyć konfigurację. 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>Czy mogę włączyć globalny zasięg usługi ExpressRoute między dwoma obwodami usługi ExpressRoute w tej samej lokalizacji komunikacji równorzędnej?

Nie. Dwa obwody muszą pochodzić z różnych lokalizacji komunikacji równorzędnej. Jeśli metro w obsługiwanym kraju/regionie ma więcej niż jedną lokalizację komunikacji równorzędnej usługi ExpressRoute, można połączyć obwody usługi ExpressRoute utworzone w różnych lokalizacjach komunikacji równorzędnej w tym metrze. 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>Jeśli usługa ExpressRoute Global Reach jest włączona między obwodami X i obwodami Y oraz między obwodami Y a obwodem Z, czy moje sieci lokalne podłączone do obwodu X i obwodu Z będą ze sobą rozmawiać za pośrednictwem sieci firmy Microsoft?

Nie. Aby włączyć łączność między dowolnymi dwiema sieciami lokalnymi, należy jawnie połączyć odpowiednie obwody usługi ExpressRoute. W powyższym przykładzie należy podłączyć obwód X i obwód Z. 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>Jakiej przepustowości sieci mogę oczekiwać między sieciami lokalnymi po włączeniu globalnego zasięgu usługi ExpressRoute?

Przepustowość sieci między sieciami lokalnymi, włączona przez globalny zasięg usługi ExpressRoute, jest ograniczona przez mniejsze z dwóch obwodów usługi ExpressRoute. Ruch między obiektami platformy Azure i ruch między lokalami lokalnymi współużytkują ten sam obwód i podlegają temu samejej limitowi przepustowości. 

### <a name="with-expressroute-global-reach-what-are-the-limits-on-the-number-of-routes-i-can-advertise-and-the-number-of-routes-i-will-receive"></a>Jakie są limity liczby tras, które mogę reklamować w programie ExpressRoute Global Reach i liczby tras, które otrzymuję?

Liczba tras, które można anonsować firmie Microsoft na prywatnej komunikacji równorzędnej platformy Azure, pozostaje na poziomie 4000 w obwodzie standardowym lub 10000 na obwodzie Premium. Liczba tras, które otrzymasz od firmy Microsoft w prywatnej komunikacji równorzędnej platformy Azure, będzie sumą tras sieci wirtualnych platformy Azure i tras z innych sieci lokalnych połączonych za pośrednictwem usługi ExpressRoute Global Reach. Upewnij się, że ustawiono odpowiedni maksymalny limit prefiksów na routerze lokalnym. 

### <a name="what-is-the-sla-for-expressroute-global-reach"></a>Co to jest usługa SLA dla globalnego zasięgu usługi ExpressRoute?

Usługa ExpressRoute Global Reach zapewnia taką samą [usługę SLA dostępności,](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/) jak zwykła usługa usługi Usługi ExpressRoute.
