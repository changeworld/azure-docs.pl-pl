---
title: Temat usługi ExpressRoute bezpośrednio — Azure | Dokumentacja firmy Microsoft
description: Ta strona zawiera omówienie usługi ExpressRoute Direct
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: a294c444f10719f69716b25b97cd137874a3e0be
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954148"
---
# <a name="about-expressroute-direct"></a>Informacje o usłudze ExpressRoute Direct

Bezpośrednio z usługi ExpressRoute zapewnia możliwość łączenia bezpośrednio do globalnej sieci firmy Microsoft w lokalizacji komunikacji równorzędnej, strategicznie rozproszonych na całym świecie. Funkcja ExpressRoute Direct oferuje dwie połączenia o szybkości 100 GB/s lub 10 GB/s, które obsługują łączność aktywną/aktywną w dużej skali.

Najważniejsze funkcje, które zapewnia usługi ExpressRoute bezpośrednio obejmują, ale nie są ograniczone do:

* Masywne pozyskiwanie danych do usług, takich jak Storage i Cosmos DB
* Izolacja fizyczna dla branż, które są regulowane i wymagają dedykowanych i izolowanych połączeń, takich jak: usług bankowych, rządowych i handlowych
* Pełna kontrola nad rozmieszczeniem obwodów na podstawie jednostek biznesowych

## <a name="onboard-to-expressroute-direct"></a>Dołączanie do ExpressRoute Direct

Przed użyciem usługi ExpressRoute Direct należy najpierw zarejestrować swoją subskrypcję. Aby zarejestrować urządzenie, Wyślij wiadomość E-mail do <ExpressRouteDirect@microsoft.com> identyfikatorem subskrypcji, łącznie z następującymi szczegółami:

* Scenariusze, które chcesz wykonać przy użyciu **bezpośrednio z usługi ExpressRoute**
* Zobacz Preferencje lokalizacji — [partnerzy i lokalizacje komunikacji równorzędnej](expressroute-locations-providers.md) pełną listę wszystkich lokalizacji
* Oś czasu dla wdrożenia
* Inne pytania

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>Usługi ExpressRoute za pomocą dostawcy usług i bezpośrednio z usługi ExpressRoute

| **Przy użyciu dostawcy usługi ExpressRoute** | **Bezpośrednie usługi ExpressRoute** | 
| --- | --- |
| Korzysta z dostawców usługi, aby włączyć szybkie dołączania i połączeń z istniejącą infrastrukturą | Wymaga infrastruktury 100 GB/s/10 GB/s i pełnego zarządzania wszystkimi warstwami
| Integruje się z setkami dostawców sieci Ethernet i MPLS | Bezpośrednie/dedykowana pojemność branżach regulowanych prawnie i pozyskiwania duża ilość danych |
| Jednostki SKU obwodów z 50 MB/s do 10 GB/s | Klient może wybrać kombinację następujących jednostek SKU obwodu na 100 GB/s ExpressRoute bezpośrednio: <ul><li>5 Gb/s</li><li>10 Gb/s</li><li>40 GB/s</li><li>100 GB/s</li></ul> Klient może wybrać kombinację następujących jednostek SKU obwodu na 10 GB/s ExpressRoute bezpośrednio:<ul><li>1 Gb/s</li><li>2 Gb/s</li><li>5 Gb/s</li><li>10 Gb/s</li></ul>
| Zoptymalizowane pod kątem pojedynczej dzierżawy | Optymalizacja pod kątem pojedynczej dzierżawy z wieloma jednostkami biznesowymi i wieloma środowiskami roboczymi

## <a name="expressroute-direct-circuits"></a>Obwody usługi ExpressRoute bezpośrednio

Usługa Microsoft Azure ExpressRoute umożliwia rozszerzanie sieci lokalnych na chmurę Microsoft za pośrednictwem połączenia prywatnego obsługiwanego przez dostawcę połączenia. Dzięki usłudze ExpressRoute można ustanowić połączenia z usługami Microsoft w chmurze, np. Microsoft Azure, Office 365 i Dynamics 365.  

Każdej lokalizacji komunikacji równorzędnej ma dostęp do globalnej sieci firmy Microsoft i mogą uzyskiwać dostęp do dowolnego regionu w strefie geopolitycznej domyślnie i mogą uzyskiwać dostęp do wszystkich regionach na świecie przy użyciu obwodu premium.  

Funkcje, które w większości przypadków jest odpowiednikiem obwodów, które korzystają z dostawcy usługi ExpressRoute do działania. Aby zapewnić obsługę dalsze szczegółowość oraz nowych możliwości oferowanych przy użyciu usługi ExpressRoute bezpośrednio, ma niektórych kluczowych funkcji, które istnieją na bezpośrednie obwodów usługi ExpressRoute.

## <a name="circuit-skus"></a>Jednostki SKU obwodu

Bezpośrednie ExpressRoute obsługuje scenariuszy pozyskiwania duża ilość danych do usługi Azure storage i innych usług danych big data. Obwody usługi ExpressRoute na 100 GB/s ExpressRoute bezpośrednio obsługują także **40 GB/s** i 100 jednostki SKU obwodów **GB** /s. Fizyczne pary portów to **100 lub 10 GB/s** i mogą mieć wiele obwodów wirtualnych. Rozmiary obwodu:

| **100 GB/s ExpressRoute Direct** | **10 GB/s ExpressRoute Direct** | 
| --- | --- |
| **Zasubskrybowana przepustowość**: 200 GB/s | **Zasubskrybowana przepustowość**: 20 GB/s |
| <ul><li>5 Gb/s</li><li>10 Gb/s</li><li>40 GB/s</li><li>100 GB/s</li></ul> | <ul><li>1 Gb/s</li><li>2 Gb/s</li><li>5 Gb/s</li><li>10 Gb/s</li></ul>

## <a name="technical-requirements"></a>Wymagania techniczne

* Interfejsy Microsoft Enterprise Edge router (MSEE):
    * Dwa lub 100 Gigabit Ethernet Ports tylko dla pary routerów
    * LR łączności Fiber z pojedynczym trybem
    * IPv4 i IPv6
    * IP MTU 1500 bajtów

* Połączenia warstwy 2/warstwy 3/routera:
    * Musi obsługiwać 1 tag 802.1 Q (Dot1Q) lub dwa Tagi 802.1 Q (QinQ) tag
    * Etertype = 0x8100
    * Należy dodać zewnętrzny tag sieci VLAN (STAG) na podstawie identyfikatora sieci VLAN określonego przez firmę Microsoft — *dotyczy tylko QinQ*
    * Musi obsługiwać wiele sesji protokołu BGP (VLAN) na port i urządzenie
    * Łączność z protokołami IPv4 i IPv6. *W przypadku protokołu IPv6 nie zostanie utworzony dodatkowy interfejs podrzędny. Adres IPv6 zostanie dodany do istniejącego interfejsu*podrzędnego. 
    * Opcjonalnie: Obsługa [wykrywania dwukierunkowego przekazywania (BFD)](https://docs.microsoft.com/azure/expressroute/expressroute-bfd) , która jest konfigurowana domyślnie na wszystkich prywatnych komunikacji równorzędnej w obwodach usługi ExpressRoute

## <a name="vlan-tagging"></a>Znakowanie sieci VLAN

Usługa ExpressRoute bezpośrednio obsługuje QinQ i Dot1Q znakowanie sieci VLAN.

* **Znakowanie sieci VLAN QinQ** umożliwia izolowanych domen routingu na podstawie obwodu usługi ExpressRoute. Platforma Azure dynamicznie przydziela Tag S, podczas tworzenia obwodu i nie można jej zmienić. Każdego wystąpienia komunikacji równorzędnej na obwód (prywatnej i Microsoft) będą korzystać z unikatowych tagów języka C jako sieci VLAN. Tag języka C nie musi być unikatowa w obrębie obwodów na portach bezpośrednio z usługi ExpressRoute.

* **Znakowanie sieci VLAN Dot1Q** umożliwia pojedynczego otagowane sieci VLAN na poszczególnych para portów bezpośrednio z usługi ExpressRoute. C — Tag, używany w komunikacji równorzędnej musi być unikatowa we wszystkich obwodów i komunikacji równorzędnej w parę portu bezpośrednio z usługi ExpressRoute.

## <a name="workflow"></a>Przepływ pracy

[![utworzonego](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>Umowa SLA

Bezpośrednie ExpressRoute zapewnia taką samą umową SLA klasy przedsiębiorstwa aktywny/aktywny nadmiarowych połączeń do globalnej sieci firmy Microsoft. Infrastruktura usługi ExpressRoute jest nadmiarowy i łączności do globalnej sieci firmy Microsoft jest nadmiarowy i zróżnicowanych i skaluje się w związku z tym wymagania klientów. 

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie usługi ExpressRoute bezpośrednio](expressroute-howto-erdirect.md)
