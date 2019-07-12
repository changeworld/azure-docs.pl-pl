---
title: Temat usługi ExpressRoute bezpośrednio — Azure | Dokumentacja firmy Microsoft
description: Ta strona zawiera omówienie usługi ExpressRoute bezpośrednio
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: e598cc03a1b7b4999719152540866c7168130e03
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807478"
---
# <a name="about-expressroute-direct"></a>Informacje o usłudze ExpressRoute Direct

Bezpośrednio z usługi ExpressRoute zapewnia możliwość łączenia bezpośrednio do globalnej sieci firmy Microsoft w lokalizacji komunikacji równorzędnej, strategicznie rozproszonych na całym świecie. Bezpośrednie usługi ExpressRoute zapewnia podwójne 100 GB/s i 10 GB/s łączności, obsługująca łączność aktywny/aktywny na dużą skalę.

Najważniejsze funkcje, które zapewnia usługi ExpressRoute bezpośrednio obejmują, ale nie są ograniczone do:

* Masywne pozyskiwanie danych do usług, takich jak Storage i Cosmos DB
* Fizyczne odizolowanie dla przedsiębiorstw, które są regulowane i wymagają dedykowanej i odizolowanej łączności, takich jak: usług bankowych, rządowych i handlowych
* Pełna kontrola nad rozmieszczeniem obwodów na podstawie jednostek biznesowych

## <a name="onboard-to-expressroute-direct"></a>Dołączanie do usługi ExpressRoute bezpośrednio

Przed rozpoczęciem korzystania z usługi ExpressRoute bezpośrednio, musisz zarejestrować swoją subskrypcję. Aby zarejestrować urządzenie, Wyślij wiadomość E-mail do <ExpressRouteDirect@microsoft.com> identyfikatorem subskrypcji, łącznie z następującymi szczegółami:

* Scenariusze, które chcesz wykonać przy użyciu **bezpośrednio z usługi ExpressRoute**
* Zobacz Preferencje lokalizacji — [partnerzy i lokalizacje komunikacji równorzędnej](expressroute-locations-providers.md) pełną listę wszystkich lokalizacji
* Oś czasu dla wdrożenia
* Inne pytania

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>Usługi ExpressRoute za pomocą dostawcy usług i bezpośrednio z usługi ExpressRoute

| **Przy użyciu dostawcy usługi ExpressRoute** | **Bezpośrednie usługi ExpressRoute** | 
| --- | --- |
| Korzysta z dostawców usługi, aby włączyć szybkie dołączania i połączeń z istniejącą infrastrukturą | Wymaga 100 GB/s/10 GB/s, infrastruktury i pełnego zarządzania wszystkie warstwy
| Integruje się z setkami dostawców sieci Ethernet i MPLS | Bezpośrednie/dedykowana pojemność branżach regulowanych prawnie i pozyskiwania duża ilość danych |
| Jednostki SKU obwodów z 50 MB/s do 10 GB/s | Odbiorcy mogą wybrać kombinację obwodu następujących jednostek SKU na 100 GB/s usługi ExpressRoute bezpośrednie: <ul><li>5 Gb/s</li><li>10 Gb/s</li><li>40 GB/s</li><li>100 GB/s</li></ul> Odbiorcy mogą wybrać kombinację obwodu następujących jednostek SKU w ramach 10 GB/s usługi ExpressRoute bezpośrednie:<ul><li>1 Gb/s</li><li>2 Gb/s</li><li>5 Gb/s</li><li>10 Gb/s</li></ul>
| Zoptymalizowane pod kątem pojedynczej dzierżawy | Zoptymalizowane pod kątem dla dostawców usług w jednej dzierżawy/w chmurze / wielu jednostkach biznesowych

## <a name="expressroute-direct-circuits"></a>Obwody usługi ExpressRoute bezpośrednio

Usługa Microsoft Azure ExpressRoute umożliwia rozszerzanie sieci lokalnych na chmurę Microsoft za pośrednictwem połączenia prywatnego obsługiwanego przez dostawcę połączenia. Dzięki usłudze ExpressRoute można ustanowić połączenia z usługami Microsoft w chmurze, np. Microsoft Azure, Office 365 i Dynamics 365.  

Każdej lokalizacji komunikacji równorzędnej ma dostęp do globalnej sieci firmy Microsoft i mogą uzyskiwać dostęp do dowolnego regionu w strefie geopolitycznej domyślnie i mogą uzyskiwać dostęp do wszystkich regionach na świecie przy użyciu obwodu premium.  

Funkcje, które w większości przypadków jest odpowiednikiem obwodów, które korzystają z dostawcy usługi ExpressRoute do działania. Aby zapewnić obsługę dalsze szczegółowość oraz nowych możliwości oferowanych przy użyciu usługi ExpressRoute bezpośrednio, ma niektórych kluczowych funkcji, które istnieją na bezpośrednie obwodów usługi ExpressRoute.

## <a name="circuit-skus"></a>Jednostki SKU obwodu

Bezpośrednie ExpressRoute obsługuje scenariuszy pozyskiwania duża ilość danych do usługi Azure storage i innych usług danych big data. Obwody usługi ExpressRoute na 100 GB/s ExpressRoute bezpośrednie teraz obsługują także **40 GB/s** i **100 GB/s** circuit jednostki SKU. Pary fizycznego portu są **100 lub 10 GB/s** tylko i może mieć wiele obwodów wirtualnych. Rozmiary obwodu:

| **100 Gbps ExpressRoute Direct** | **10 Gbps ExpressRoute Direct** | 
| --- | --- |
| **Zasubskrybowano przepustowości**: 200 GB/s | **Zasubskrybowano przepustowości**: 20 GB/s |
| <ul><li>5 Gb/s</li><li>10 Gb/s</li><li>40 GB/s</li><li>100 GB/s</li></ul> | <ul><li>1 Gb/s</li><li>2 Gb/s</li><li>5 Gb/s</li><li>10 Gb/s</li></ul>

## <a name="technical-requirements"></a>Wymagania techniczne

* Interfejsy routera (MSEE) krawędź firmy Microsoft dla przedsiębiorstw:
    * Podwójnych portów sieci Gigabit Ethernet 10 lub 100 przez parę routera
    * Pojedynczy Fiber LR tryb łączności
    * IPv4 i IPv6
    * IP rozmiar jednostki MTU 1500 bajtów

* Przełącznik/routerem łączność 2/warstwy 3 warstwy:
    * Musi obsługiwać 1 802.1Q (Dot1Q) tagu lub tagu dwóch (QinQ) 802.1Q tag hermetyzacji
    * Ethertype = 0x8100
    * Należy dodać zewnętrzne tagu sieci VLAN (STAG) na podstawie Identyfikatora sieci VLAN, określone przez firmę Microsoft - *dotyczy tylko QinQ*
    * Musi obsługiwać wiele sesji protokołu BGP (VLAN) na port i urządzenia
    * Łączność z protokołem IPv4 i IPv6. *W przypadku protokołu IPv6 bez dodatkowych podrzędnych interfejsu zostanie utworzony. Adres IPv6, które zostaną dodane do istniejącego interfejsu podrzędnych*. 
    * Opcjonalnie: [Dwukierunkowe przesyłanie dalej wykrywania (BFD)](https://docs.microsoft.com/azure/expressroute/expressroute-bfd) pomocy technicznej, które jest skonfigurowane domyślnie na wszystkich prywatnej komunikacji równorzędnej dla obwodów usługi ExpressRoute

## <a name="vlan-tagging"></a>Znakowanie sieci VLAN

Usługa ExpressRoute bezpośrednio obsługuje QinQ i Dot1Q znakowanie sieci VLAN.

* **Znakowanie sieci VLAN QinQ** umożliwia izolowanych domen routingu na podstawie obwodu usługi ExpressRoute. Platforma Azure dynamicznie przydziela Tag S, podczas tworzenia obwodu i nie można jej zmienić. Każdego wystąpienia komunikacji równorzędnej na obwód (prywatnej i Microsoft) będą korzystać z unikatowych tagów języka C jako sieci VLAN. Tag języka C nie musi być unikatowa w obrębie obwodów na portach bezpośrednio z usługi ExpressRoute.

* **Znakowanie sieci VLAN Dot1Q** umożliwia pojedynczego otagowane sieci VLAN na poszczególnych para portów bezpośrednio z usługi ExpressRoute. C — Tag, używany w komunikacji równorzędnej musi być unikatowa we wszystkich obwodów i komunikacji równorzędnej w parę portu bezpośrednio z usługi ExpressRoute.

## <a name="workflow"></a>Przepływ pracy

[![Przepływ pracy](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>Umowa SLA

Bezpośrednie ExpressRoute zapewnia taką samą umową SLA klasy przedsiębiorstwa aktywny/aktywny nadmiarowych połączeń do globalnej sieci firmy Microsoft. Infrastruktura usługi ExpressRoute jest nadmiarowy i łączności do globalnej sieci firmy Microsoft jest nadmiarowy i zróżnicowanych i skaluje się w związku z tym wymagania klientów. 

## <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie usługi ExpressRoute bezpośrednio](expressroute-howto-erdirect.md)
