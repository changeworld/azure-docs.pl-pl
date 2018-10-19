---
title: Dotyczących platformy Azure bezpośrednio z usługi ExpressRoute | Dokumentacja firmy Microsoft
description: Ta strona zawiera omówienie usługi ExpressRoute bezpośrednich (wersja zapoznawcza)
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: ce4bc88f6b599bb3f5f760b2ba83c81f2b6acc5f
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2018
ms.locfileid: "49405182"
---
# <a name="about-expressroute-direct-preview"></a>Temat usługi ExpressRoute bezpośrednie (wersja zapoznawcza)

Usługa ExpressRoute Direct daje klientom możliwość łączenia się bezpośrednio z globalną siecią firmy Microsoft w skomunikowanych równorzędnie lokalizacjach strategicznie rozmieszczonych na całym świecie. Bezpośrednie ExpressRoute zapewnia podwójne 100 GB/s łączności obsługuje połączenia aktywny/aktywny na dużą skalę. 

Najważniejsze funkcje, które zapewnia usługi ExpressRoute bezpośrednio obejmują, ale nie są ograniczone do:

* Masywne pozyskiwanie danych do usług, takich jak Storage i Cosmos DB 
* Fizyczne oddzielenie w przypadku tych branż, które są regulowane i w których łączność musi być dedykowana i oddzielona, na przykład w przypadku usług bankowych, rządowych i handlowych 
* Pełna kontrola nad rozmieszczeniem obwodów na podstawie jednostek biznesowych

> [!IMPORTANT]
> Bezpośrednie usługi ExpressRoute jest obecnie dostępna w wersji zapoznawczej.
>
> Publiczna wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie należy korzystać z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enroll-in-the-preview"></a>Rejestracja w wersji zapoznawczej

Przed rozpoczęciem korzystania z usługi ExpressRoute bezpośrednio, musisz zarejestrować swoją subskrypcję w wersji zapoznawczej. Aby przeprowadzić rejestrację, wyślij wiadomość e-mail na adres <ExpressRouteDirect@microsoft.com>, podając identyfikator subskrypcji. Bezpośrednie usługi ExpressRoute jest funkcją przeznaczonych dla przedsiębiorstw. Podaj dodatkowe szczegóły:

* Scenariusze, które chcesz wykonać przy użyciu **bezpośrednio z usługi ExpressRoute**
* Zobacz Preferencje lokalizacji — [partnerzy i lokalizacje komunikacji równorzędnej](expressroute-locations-providers.md) pełną listę wszystkich lokalizacji
* Oś czasu dla wdrożenia
* Inne pytania

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>Usługi ExpressRoute za pomocą dostawcy usług i bezpośrednio z usługi ExpressRoute

| **Przy użyciu dostawcy usługi ExpressRoute** | **Bezpośrednie usługi ExpressRoute** | 
| --- | --- | 
| Korzysta z dostawcą usługi, aby włączyć szybkiego dołączania i połączeń z istniejącą infrastrukturą | Wymaga 100 GB/s, infrastruktury i pełnego zarządzania wszystkie warstwy
| Integruje się z setkami dostawców sieci Ethernet i MPLS | Bezpośrednie/dedykowana pojemność branżach regulowanych prawnie i pozyskiwania duża ilość danych | 
| SKU obwodów z dnia 50 MB/s do 10 GB/s | Jednostki SKU obwodów od 5 GB/s do 100 GB/s (5 GB/s, 10 GB/s, 40 GB/s, 100 GB/s)
| Zoptymalizowane pod kątem pojedynczej dzierżawy | Zoptymalizowane pod kątem dla dostawców usług w jednej dzierżawy/w chmurze / wielu jednostkach biznesowych

## <a name="expressroute-direct-circuits"></a>Obwody usługi ExpressRoute bezpośrednio

Usługa Microsoft Azure ExpressRoute umożliwia rozszerzanie sieci lokalnych na chmurę Microsoft za pośrednictwem połączenia prywatnego obsługiwanego przez dostawcę połączenia. Dzięki usłudze ExpressRoute można ustanowić połączenia z usługami Microsoft w chmurze, np. Microsoft Azure, Office 365 i Dynamics 365.  

Każdej lokalizacji komunikacji równorzędnej ma dostęp do globalnej sieci firmy Microsoft i mogą uzyskiwać dostęp do dowolnego regionu w strefie geopolitycznej domyślnie i mogą uzyskiwać dostęp do wszystkich regionach na świecie przy użyciu obwodu premium.  

Funkcje, które w większości przypadków jest odpowiednikiem obwodów, które korzystają z dostawcy usługi ExpressRoute do działania. Aby zapewnić obsługę dalsze szczegółowość oraz nowych możliwości oferowanych przy użyciu usługi ExpressRoute bezpośrednio, ma niektórych kluczowych funkcji, które istnieją na bezpośrednie obwodów usługi ExpressRoute.

## <a name="circuit-skus"></a>Jednostki SKU obwodu

Bezpośrednie ExpressRoute obsługuje scenariuszy pozyskiwania duża ilość danych do usługi Azure storage i innych usług danych big data. Usługi ExpressRoute circuits na bezpośrednio z usługi ExpressRoute teraz również obsługę **40 GB/s** i **100 GB/s** circuit jednostki SKU. 

## <a name="vlan-tagging"></a>Znakowanie sieci VLAN

Usługa ExpressRoute bezpośrednio obsługuje QinQ i Dot1Q znakowanie sieci VLAN.

* **Znakowanie sieci VLAN QinQ** umożliwia izolowanych domen routingu na podstawie obwodu usługi ExpressRoute. Platforma Azure dynamicznie przydziela Tag S, podczas tworzenia obwodu i nie można jej zmienić. Każdego wystąpienia komunikacji równorzędnej na obwód (prywatnej i Microsoft) będą korzystać z unikatowych tagów języka C jako sieci VLAN. Tag języka C nie musi być unikatowa w obrębie obwodów na portach bezpośrednio z usługi ExpressRoute. 

* **Znakowanie sieci VLAN Dot1Q** umożliwia pojedynczego otagowane sieci VLAN na poszczególnych para portów bezpośrednio z usługi ExpressRoute. C — Tag, używany w komunikacji równorzędnej musi być unikatowa we wszystkich obwodów i komunikacji równorzędnej w parę portu bezpośrednio z usługi ExpressRoute.

## <a name="workflow"></a>Przepływ pracy

![przepływ pracy](./media/expressroute-erdirect-about/workflow1.png)

## <a name="sla"></a>Umowa SLA

Bezpośrednie ExpressRoute zapewnia taką samą umową SLA klasy przedsiębiorstwa aktywny/aktywny nadmiarowych połączeń do globalnej sieci firmy Microsoft. Infrastruktura usługi ExpressRoute jest nadmiarowy i łączności do globalnej sieci firmy Microsoft jest nadmiarowy i zróżnicowanych i skaluje się w związku z tym wymagania klientów. W trakcie okresu zapoznawczego, będzie objęta umową SLA i mają być uwzględniane tylko w przypadku obciążeń nieprodukcyjnych.

## <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie usługi ExpressRoute bezpośrednio](expressroute-howto-erdirect.md)
