---
title: Informacje o usłudze Azure ExpressRoute Direct
description: Ta strona zawiera przegląd usługi Direct usługi ExpressRoute
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jaredro
ms.openlocfilehash: f26fdd75e0a6c6228d329a8b9be4743072ae54c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083602"
---
# <a name="about-expressroute-direct"></a>Informacje o usłudze ExpressRoute Direct

Usługa ExpressRoute Direct umożliwia bezpośrednie łączenie się bezpośrednio z globalną siecią firmy Microsoft w lokalizacjach komunikacji równorzędnej strategicznie rozproszonych na całym świecie. Usługa ExpressRoute Direct zapewnia podwójną łączność 100 Gb/s lub 10 Gb/s, która obsługuje łączność Active/Active na dużą skalę.

Najważniejsze funkcje, które zapewnia program ExpressRoute Direct obejmują między innymi:

* Masywne pozyskiwanie danych do usług, takich jak Storage i Cosmos DB
* Izolacja fizyczna dla branż, które są regulowane i wymagają dedykowanej i odosobnionej łączności, takiej jak: bankowość, rząd i handel detaliczny
* Pełna kontrola nad rozmieszczeniem obwodów na podstawie jednostek biznesowych

## <a name="onboard-to-expressroute-direct"></a>Wbudowany do usługi ExpressRoute Direct

Przed użyciem usługi ExpressRoute Direct należy najpierw zarejestrować subskrypcję. Aby się zarejestrować, <ExpressRouteDirect@microsoft.com> wyślij wiadomość e-mail z identyfikatorem subskrypcji, podając następujące szczegóły:

* Scenariusze, które chcesz zrealizować za pomocą usługi **ExpressRoute Direct**
* Preferencje lokalizacji — zobacz [Partnerzy i lokalizacje komunikacji równorzędnej,](expressroute-locations-providers.md) aby uzyskać pełną listę wszystkich lokalizacji
* Oś czasu dla wdrożenia
* Wszelkie inne pytania

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>Usługa ExpressRoute przy użyciu dostawcy usług i usługi ExpressRoute Direct

| **Usługa ExpressRoute przy użyciu dostawcy usług** | **Usługa ExpressRoute Direct** | 
| --- | --- |
| Wykorzystuje usługodawców, aby umożliwić szybkie dołączanie i łączność z istniejącą infrastrukturą | Wymaga infrastruktury 100 Gb/s/10 Gb/s i pełnego zarządzania wszystkimi warstwami
| Integruje się z setkami dostawców, w tym Ethernet i MPLS | Bezpośrednie/dedykowane zdolności produkcyjne dla branż regulowanych i masowego pozyskiwania danych |
| Układy SKU od 50 Mb/s do 10 Gb/s | Klient może wybrać kombinację następujących jednostek SKU obwodu w udziale usługi ExpressRoute Direct 100 Gb/s: <ul><li>5 Gb/s</li><li>10 Gb/s</li><li>40 Gb/s</li><li>100 Gb/s</li></ul> Klient może wybrać kombinację następujących jednostek SKU obwodu w udziale usługi ExpressRoute Direct 10 Gb/s:<ul><li>1 Gb/s</li><li>2 Gb/s</li><li>5 Gb/s</li><li>10 Gb/s</li></ul>
| Zoptymalizowane pod kątem pojedynczej dzierżawy | Zoptymalizowany pod kątem pojedynczego dzierżawcy z wieloma jednostkami biznesowymi i wieloma środowiskami pracy

## <a name="expressroute-direct-circuits"></a>Obwody direct usługi ExpressRoute

Usługa Microsoft Azure ExpressRoute umożliwia rozszerzanie sieci lokalnych na chmurę Microsoft za pośrednictwem połączenia prywatnego obsługiwanego przez dostawcę połączenia. Za pomocą usługi ExpressRoute można nawiązywać połączenia z usługami w chmurze firmy Microsoft, takimi jak Microsoft Azure i Office 365.

Każda lokalizacja komunikacji równorzędnej ma dostęp do globalnej sieci firmy Microsoft i domyślnie może uzyskać dostęp do dowolnego regionu w strefie geopolitycznej i uzyskać dostęp do wszystkich regionów globalnych za pomocą obwodu premium.  

Funkcjonalność w większości scenariuszy jest odpowiednikiem obwodów, które korzystają z usługi usługi Usługi ExpressRoute do działania. Aby obsługiwać dalszą szczegółowość i nowe możliwości oferowane przy użyciu usługi ExpressRoute Direct, istnieją pewne kluczowe funkcje, które istnieją w obwodach bezpośrednich usługi ExpressRoute.

## <a name="circuit-skus"></a>Jednostki SKU obwodu

Usługa ExpressRoute Direct obsługuje scenariusze masowego pozyskiwania danych w magazynie platformy Azure i innych usługach dużych zbiorów danych. Obwody usługi ExpressRoute w uiszczanych po 100 Gb/s kursach Direct obsługują teraz również jednostki SKU **obwodu 40 Gb/s** i **100 Gb/s.** Pary portów fizycznych mają tylko **100 lub 10 Gb/s** i mogą mieć wiele obwodów wirtualnych. Rozmiary obwodów:

| **100 Gb/s bezpośrednio kursów ekspresowych** | **10 Gb/s Bezpośrednio usługi ExpressRoute** | 
| --- | --- |
| **Subskrybowana przepustowość:** 200 Gb/s | **Subskrybowana przepustowość:** 20 Gb/s |
| <ul><li>5 Gb/s</li><li>10 Gb/s</li><li>40 Gb/s</li><li>100 Gb/s</li></ul> | <ul><li>1 Gb/s</li><li>2 Gb/s</li><li>5 Gb/s</li><li>10 Gb/s</li></ul>

## <a name="technical-requirements"></a>Wymagania techniczne

* Interfejsy routera Microsoft Enterprise Edge Router (MSEE):
    * Dwa porty 10 lub 100 Gigabit Ethernet tylko w parach routerów
    * Łączność światłowodowa LR w trybie jednomodowym
    * IPv4 i IPv6
    * Ip MTU 1500 bajtów

* Łączność z warstwą przełącznik/router 2/warstwa 3:
    * Musi obsługiwać tag 1 802.1Q (Dot1Q) lub dwie hermetyzację tagu Tag 802.1Q (QinQ)
    * Ethertype = 0x8100
    * Należy dodać zewnętrzny znacznik VLAN (STAG) na podstawie identyfikatora VLAN określonego przez firmę Microsoft — *ma zastosowanie tylko w przypadku QinQ*
    * Musi obsługiwać wiele sesji BGP (VLAN) na port i urządzenie
    * Łączność IPv4 i IPv6. *W przypadku IPv6 nie zostanie utworzony dodatkowy podnajem. Adres IPv6 zostanie dodany do istniejącego podkonisuj*. 
    * Opcjonalnie: obsługa [dwukierunkowego wykrywania przekazywania dalej (BFD),](https://docs.microsoft.com/azure/expressroute/expressroute-bfd) która jest domyślnie skonfigurowana we wszystkich prywatnych układach równorzędnych na obwodach usługi ExpressRoute

## <a name="vlan-tagging"></a>Tagowanie sieci VLAN

Usługa ExpressRoute Direct obsługuje tagowanie funkcji QinQ i Dot1Q VLAN.

* **QinQ VLAN Tagging** umożliwia izolowane domeny routingu na podstawie obwodu usługi ExpressRoute. Platforma Azure dynamicznie przydziela znacznik S podczas tworzenia obwodu i nie można jej zmienić. Każda komunikacja równorzędna na obwodzie (private i Microsoft) będzie korzystać z unikatowego tagu C jako sieci VLAN. Tag C nie musi być unikatowy dla obwodów na portach Direct usługi ExpressRoute.

* **Tagowanie sieci VLAN w programie Dot1Q** umożliwia pojedyncze oznakowanie sieci VLAN na podstawie pary portów Direct usługi ExpressRoute. Znacznik C używany w komunikacji równorzędnej musi być unikatowy we wszystkich obwodach i komunikacji równorzędnej na parze portów Direct usługi ExpressRoute.

## <a name="workflow"></a>Przepływ pracy

[![Przepływu pracy](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>Umowa SLA

Usługa ExpressRoute Direct zapewnia tę samą usługę SLA klasy korporacyjnej z aktywnymi/aktywnymi nadmiarowymi połączeniami z siecią Microsoft Global Network. Infrastruktura usługi ExpressRoute jest nadmiarowa, a łączność z siecią Microsoft Global Network jest nadmiarowa i zróżnicowana oraz odpowiednio skaluje się zgodnie z wymaganiami klientów. 

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie usługi ExpressRoute Direct](expressroute-howto-erdirect.md)
