---
title: Obwody usługi ExpressRoute platformy Azure i domeny routingu | Dokumentacja firmy Microsoft
description: Ta strona zawiera omówienie obwodów usługi ExpressRoute i domeny routingu.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: cherylmc
ms.openlocfilehash: c1fbc99702e077e600f01959224f3321e7543e59
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2018
ms.locfileid: "48044403"
---
# <a name="expressroute-circuits-and-routing-domains"></a>Obwody usługi ExpressRoute i domeny routingu
 Należy zamówić *obwód usługi ExpressRoute* do podłączenia infrastruktury lokalnej do firmy Microsoft za pośrednictwem dostawcy łączności. Na poniższej ilustracji przedstawiono logiczną reprezentację łączność sieci WAN, a firmy Microsoft.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

## <a name="expressroute-circuits"></a>Obwody usługi ExpressRoute
*Obwód usługi ExpressRoute* reprezentuje połączenie logiczne między lokalną infrastrukturą i usługami chmurowymi firmy Microsoft za pośrednictwem dostawcy łączności. Może zamówić łączność obejmującą wiele obwodów usługi ExpressRoute. Każdy obwód może znajdować się w tej samej lub różnych regionach i mogą być połączone z lokalnym za pośrednictwem połączenia różnych dostawców. 

Obwody usługi ExpressRoute nie są mapowane do żadnych jednostek fizycznych. Obwód jest unikatowo identyfikowana przez standardowy identyfikator GUID wywołać jako klucz usługi (s-key). Klucz usługi jest tylko część informacji wymienianych między firmą Microsoft, dostawca połączenia, a. Klawisz s nie jest wpis tajny ze względów bezpieczeństwa. Istnieje mapowanie 1:1 między obwodu usługi ExpressRoute i klawisz s.

Obwód usługi ExpressRoute może mieć maksymalnie trzech niezależnych komunikacji równorzędnej: Azure prywatnej publicznych, platformy Azure i firmy Microsoft. Każdego wystąpienia komunikacji równorzędnej to para BGP niezależnie od sesji z nich nadmiarowo skonfigurowany w celu zapewnienia wysokiej dostępności. Brak 1: n (1 < = N < = 3) mapowanie między obwodu usługi ExpressRoute i domeny routingu. Obwód usługi ExpressRoute może mieć jeden, dwa lub wszystkie trzy komunikacje równorzędne włączone na obwód usługi ExpressRoute.

Każdy obwód o stałej szerokości pasma (50 MB/s, 100 MB/s, 200 MB/s, 500 MB/s, 1 GB/s, 10 GB/s) i jest mapowany do dostawcy łączności i lokalizacji komunikacji równorzędnej. Przepustowość, którą wybierzesz jest współużytkowany przez wszystkie komunikacje równorzędne dla obwodu. 

### <a name="quotas-limits-and-limitations"></a>Przydziały, limity i ograniczenia
Domyślne limity przydziału i limity dotyczą każdego obwód usługi ExpressRoute. Zapoznaj się [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../azure-subscription-service-limits.md) aktualne informacje dotyczące limitów przydziału.

## <a name="expressroute-routing-domains"></a>Domeny routingu usługi ExpressRoute
Obwód usługi ExpressRoute ma wiele domen routingu skojarzone z nią: Azure prywatnej publicznych, platformy Azure i firmy Microsoft. Każda z domen routingu jest skonfigurowane tak samo w pary routerów (aktywny / aktywny lub udostępnianie obciążenia konfiguracji) wysokiej dostępności. Usługi platformy Azure są klasyfikowane jako *publicznej platformy Azure* i *Azure prywatne* do reprezentowania schematów adresowania IP.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="azure-private-peering"></a>Prywatna komunikacja równorzędna Azure
Usługi Azure compute usług, a mianowicie maszyn wirtualnych (IaaS) i usług w chmurze (PaaS), które są wdrażane w ramach sieci wirtualnej mogą być połączone za pośrednictwem prywatnej komunikacji równorzędnej domeny. Prywatnej komunikacji równorzędnej domeny jest uważana za zaufane rozszerzenie sieci podstawowej w Microsoft Azure. Można skonfigurować dwukierunkowej łączności między sieciami wirtualnymi platformy Azure (Vnet) i sieci podstawowej. Tę komunikację równorzędną pozwala nawiązać połączenie z maszynami wirtualnymi i usługami bezpośrednio na ich prywatnych adresów IP w chmurze.  

Możesz połączyć więcej niż jednej sieci wirtualnej do prywatnej komunikacji równorzędnej domeny. Przegląd [z często Zadawanymi pytaniami](expressroute-faqs.md) informacji na temat limity i ograniczenia. Możesz odwiedzić stronę [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../azure-subscription-service-limits.md) aktualne informacje dotyczące limitów.  Zapoznaj się [Routing](expressroute-routing.md) strony, aby uzyskać szczegółowe informacje na temat konfiguracji routingu.

### <a name="microsoft-peering"></a>Komunikacja równorzędna firmy Microsoft

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Łączność z usługami online firmy Microsoft (usługi Office 365, Dynamics 365 i PaaS platformy Azure) jest za pośrednictwem komunikacji równorzędnej firmy Microsoft. Włączyliśmy dwukierunkowej łączności między sieci WAN, a Microsoft usługi w chmurze za pośrednictwem domeny routingu komunikacji równorzędnej firmy Microsoft. Musisz połączyć się z usługami chmurowymi firmy Microsoft tylko za pośrednictwem publiczne adresy IP, które są własnością użytkownik lub jego dostawca łączności i muszą być zgodne ze wszystkimi zdefiniowanymi regułami. Aby uzyskać więcej informacji, zobacz [wymagania wstępne usługi ExpressRoute](expressroute-prerequisites.md) strony.

Zobacz [z często Zadawanymi pytaniami](expressroute-faqs.md) Aby uzyskać więcej informacji na temat obsługiwanych usług, koszty i szczegółów konfiguracji. Zobacz [lokalizacji usługi ExpressRoute](expressroute-locations.md) informacje dotyczące listy dostawców łączności oferty pomocy technicznej komunikacji równorzędnej firmy Microsoft.

### <a name="azure-public-peering"></a>Usługa Azure publicznej komunikacji równorzędnej (przestarzałe obwodów)

> [!IMPORTANT]
> Nie jest dostępna dla obwodów publicznej komunikacji równorzędnej Azure.  
>   

Usług, takich jak Azure Storage, bazy danych SQL i witryn sieci Web są oferowane na publicznych adresów IP. Prywatnie można połączyć do usług hostowanych na publicznych adresów IP, w tym VIP usługi w chmurze za pośrednictwem publicznej komunikacji równorzędnej domeny routingu. Można publicznej komunikacji równorzędnej domeny nawiązać połączenie z sieci Obwodowej i połączyć się z wszystkich usług platformy Azure dla swoich publicznych adresów IP z poziomu sieci WAN bez konieczności nawiązywania połączenia przez internet. 

Połączenie jest zawsze inicjowane z sieci WAN z usługami Microsoft Azure. Usługi Microsoft Azure nie będą mogły inicjować połączeń do sieci za pomocą tej domeny routingu. Po włączeniu publicznej komunikacji równorzędnej, możesz połączyć się do wszystkich usług platformy Azure. Firma Microsoft nie umożliwiają selektywne wybierz usługi, dla których firma Microsoft anonsować trasy do.

W ramach sieci, aby używać tylko trasy, czego potrzebujesz, możesz zdefiniować filtry tras niestandardowych. Zapoznaj się [Routing](expressroute-routing.md) strony, aby uzyskać szczegółowe informacje na temat konfiguracji routingu. 

Aby uzyskać więcej informacji na temat usług obsługiwanych za pośrednictwem publicznej komunikacji równorzędnej domeny routingu, zobacz [— często zadawane pytania](expressroute-faqs.md).

## <a name="routing-domain-comparison"></a>Routing porównania domeny
W poniższej tabeli porównano trzy domeny routingu:

|  | **Prywatna komunikacja równorzędna** | **Komunikacja równorzędna firmy Microsoft** |  **Publicznej komunikacji równorzędnej** (przestarzałe dla obwodów) |
| --- | --- | --- | --- |
| **Maksymalna liczba. prefiksy # obsługiwane na połączenie komunikacji równorzędnej** |4000 domyślnie 10 000 operacji przy użyciu usługi ExpressRoute Premium |200 |200 |
| **Obsługiwane zakresów adresów IP** |Dowolny prawidłowy adres IP w sieci WAN. |Publiczne adresy IP posiadane przez Ciebie lub dostawcą łączności. |Publiczne adresy IP posiadane przez Ciebie lub dostawcą łączności. |
| **JAKO liczba wymagań** |Prywatne i publiczne numery AS. Musi być właścicielem publicznie jako liczbę, jeśli zdecydujesz się go użyć. |Prywatne i publiczne numery AS. Jednak musisz udowodnić, że własność publicznych adresów IP. |Prywatne i publiczne numery AS. Jednak musisz udowodnić, że własność publicznych adresów IP. |
| **Obsługiwane protokoły IP**| Protokół IPv4 |  IPv4, IPv6 | Protokół IPv4 |
| **Routing adresów IP interfejsu** |RFC1918 i publiczne adresy IP |Publiczne adresy IP zarejestrowane na Ciebie w rejestrów routingu. |Publiczne adresy IP zarejestrowane na Ciebie w rejestrów routingu. |
| **Skrót MD5 pomocy technicznej** |Yes |Yes |Yes |

Można włączyć co najmniej jedną z domen routingu w ramach obwodu usługi ExpressRoute. Można mieć wszystkich domen routingu umieszczanie na tej samej sieci VPN, aby połączyć je w pojedynczej domeny routingu. Można również wprowadzić je w różnych domen routingu, podobne do diagramu. Zalecana konfiguracja to, że prywatnej komunikacji równorzędnej jest podłączony bezpośrednio do sieci podstawowej, oraz publicznej komunikacji równorzędnej oraz komunikacji równorzędnej firmy Microsoft, które są podłączone do sieci Obwodowej.

Jeśli chcesz mieć wszystkie trzy sesje komunikacji równorzędnej, musisz mieć trzy pary sesji protokołu BGP (jedną parę dla każdego typu komunikacji równorzędnej). Pary sesji protokołu BGP, podaj link o wysokiej dostępności. Jeśli łączysz się za pośrednictwem dostawców łączności 2 warstwy, ponosisz odpowiedzialność za Konfigurowanie routingu oraz zarządzanie nim. Dowiedz się więcej, przeglądając [przepływy pracy](expressroute-workflows.md) dotyczące konfigurowania usługi ExpressRoute.

## <a name="expressroute-health"></a>Kondycja usługi ExpressRoute
Obwody usługi ExpressRoute mogą być monitorowane, aby zapewnić dostępność, łączność z sieciami wirtualnymi i przy użyciu wykorzystanie przepustowości [rozwiązania Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM).

NPM monitoruje kondycję prywatnej komunikacji równorzędnej Azure i komunikacji równorzędnej firmy Microsoft.  Zapoznaj się z naszym [wpis](https://azure.microsoft.com/en-in/blog/monitoring-of-azure-expressroute-in-preview/) Aby uzyskać więcej informacji.

## <a name="next-steps"></a>Kolejne kroki
* Znajdź dostawcę usługi. Zobacz [dostawcy i lokalizacje usługi ExpressRoute](expressroute-locations.md).
* Upewnij się, że zostały spełnione wszystkie wymagania wstępne. Zobacz artykuł [ExpressRoute prerequisites](expressroute-prerequisites.md) (Wymagania wstępne usługi ExpressRoute).
* Skonfiguruj połączenie usługi ExpressRoute.
  * [Tworzenie obwodów usługi ExpressRoute i zarządzanie nimi](expressroute-howto-circuit-portal-resource-manager.md)
  * [Konfigurowanie routingu (komunikacji równorzędnej) dla obwodów usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)

