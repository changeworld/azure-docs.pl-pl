---
title: Często zadawane pytania — Azure ExpressRoute | Dokumenty firmy Microsoft
description: Często zadawane pytania dotyczące usługi ExpressRoute zawierają informacje o obsługiwanych usługach platformy Azure, kosztach, danych i połączeniach, umowy SLA, dostawcach i lokalizacjach, przepustowości i dodatkowych szczegółach technicznych.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: jaredro
ms.openlocfilehash: 845c53ec970777901ae8d1c0abf5032ac705d3e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264923"
---
# <a name="expressroute-faq"></a>Usługa ExpressRoute — często zadawane pytania

## <a name="what-is-expressroute"></a>Co to jest ExpressRoute?

Usługa ExpressRoute to usługa platformy Azure, która umożliwia tworzenie prywatnych połączeń między centrami danych firmy Microsoft a infrastrukturą, która znajduje się w twoim lokalinie lub w zakładzie kolokacji. Połączenia usługi ExpressRoute nie przechodzą przez publiczny Internet i oferują większe bezpieczeństwo, niezawodność i szybkość przy niższych opóźnieniach niż typowe połączenia przez Internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Jakie są korzyści z korzystania z usługi ExpressRoute i połączeń sieci prywatnej?

Połączenia ExpressRoute nie odbywają się za pośrednictwem publicznego Internetu. Oferują one większe bezpieczeństwo, niezawodność i szybkość, z niższymi i stałymi opóźnieniami niż typowe połączenia przez Internet. W niektórych przypadkach przy użyciu połączeń usługi ExpressRoute do przesyłania danych między urządzeniami lokalnymi i platformy Azure może przynieść znaczne korzyści kosztowe.

### <a name="where-is-the-service-available"></a>Gdzie jest dostępna usługa?

Zobacz tę stronę, aby dostrzec lokalizację i dostępność usługi: [Partnerzy i lokalizacje usługi ExpressRoute](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Jak korzystać z usługi ExpressRoute, aby połączyć się z firmą Microsoft, jeśli nie mam partnerstwa z jednym z partnerów operatora usługi ExpressRoute?

Możesz wybrać regionalne połączenia przewoźnika i lądowe połączenia Ethernet do jednej z obsługiwanych lokalizacji dostawcy wymiany. Następnie można równorzędnie z firmą Microsoft w lokalizacji dostawcy. Sprawdź ostatnią sekcję [partnerów i lokalizacji usługi ExpressRoute,](expressroute-locations.md) aby sprawdzić, czy usługodawca jest obecny w dowolnej z lokalizacji wymiany. Następnie można zamówić obwód usługi ExpressRoute za pośrednictwem dostawcy usług, aby połączyć się z platformą Azure.

### <a name="how-much-does-expressroute-cost"></a>Ile kosztuje usługa ExpressRoute?

Sprawdź [szczegóły cen, aby](https://azure.microsoft.com/pricing/details/expressroute/) uzyskać informacje o cenach.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Jeśli płacę za obwód usługi ExpressRoute o określonej przepustowości, czy zakupione przeze mnie połączenie VPN od dostawcy usług sieciowych musi mieć taką samą prędkość?

Nie. Możesz kupić połączenie VPN o dowolnej prędkości od usługodawcy. Jednak połączenie z platformą Azure jest ograniczone do przepustowości obwodu usługi ExpressRoute, który kupujesz.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Jeśli płacę za obwód usługi ExpressRoute o danej przepustowości, czy w razie potrzeby mogę osiągać wyższe prędkości?

Tak. Obwody usługi ExpressRoute są skonfigurowane tak, aby umożliwić rozerwanie do dwukrotności limitu przepustowości zamówionego bez dodatkowych kosztów. Skontaktuj się z usługodawcą, aby sprawdzić, czy obsługuje tę funkcję. Nie jest to przez dłuższy czas i nie jest gwarantowane.  Jeśli ruch przepływa przez bramę usługi ExpressRoute, przepustowość dla sku jest stała i nie można go rozerwać.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Czy można jednocześnie używać tego samego połączenia sieci prywatnej z siecią wirtualną i innymi usługami platformy Azure?

Tak. Po skonfigurowaniu obwodu usługi ExpressRoute umożliwia jednoczesny dostęp do usług w sieci wirtualnej i innych usługach platformy Azure. Łączysz się z sieciami wirtualnymi za pośrednictwem prywatnej ścieżki komunikacji równorzędnej oraz z innymi usługami za pośrednictwem ścieżki komunikacji równorzędnej firmy Microsoft.

### <a name="how-are-vnets-advertised-on-expressroute-private-peering"></a>Jak sieci wirtualne są reklamowane w prywatnej komunikacji równorzędnej usługi ExpressRoute?

Brama usługi ExpressRoute anonsuje *przestrzenie adresowe* sieci wirtualnej platformy Azure, nie można dołączyć/wykluczyć na poziomie podsieci. Jest to zawsze przestrzeń adresowa sieci wirtualnej, która jest anonsowana. Ponadto jeśli używana jest komunikacja równorzędna sieci wirtualnej, a sieć wirtualna w sieci wirtualnej ma włączoną funkcję "Użyj bramy zdalnej", anonsowana zostanie również przestrzeń adresowa równorzędnej sieci wirtualnej.

### <a name="how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering"></a>Ile prefiksów można anonsować z sieci wirtualnej do lokalnej w prywatnej komunikacji równorzędnej usługi ExpressRoute?

Istnieje maksymalnie 200 prefiksów anonsowanych w jednym połączeniu usługi ExpressRoute lub za pośrednictwem komunikacji równorzędnej sieci wirtualnej przy użyciu przesyłania bramy. Na przykład jeśli masz 199 przestrzeni adresowych w jednej sieci wirtualnej podłączony do obwodu usługi ExpressRoute, wszystkie 199 z tych prefiksów będą anonsowane lokalnie. Alternatywnie, jeśli masz włączonąnetę wirtualną, aby zezwolić na przesyłanie bramy z 1 przestrzenią adresową i 150 szprychami sieci wirtualnych włączonych przy użyciu opcji "Zezwalaj na bramę zdalną", sieci wirtualnej wdrożonej z bramą anonsuje 151 prefiksów do lokalnego.

### <a name="what-happens-if-i-exceed-the-prefix-limit-on-an-expressroute-connection"></a>Co się stanie, jeśli przekroczę limit prefiksu połączenia usługi ExpressRoute?

Połączenie między obwodem usługi ExpressRoute a bramą (i równorzędne sieci wirtualne przy użyciu przesyłania bramy, jeśli dotyczy) zostanie szepne. Zostanie ponownie ustanowiony, gdy limit prefiksu nie zostanie już przekroczony.  

### <a name="can-i-filter-routes-coming-from-my-on-premises-network"></a>Czy mogę filtrować trasy pochodzące z mojej sieci lokalnej?

Jedynym sposobem filtrowania/dołączania tras jest lokalny router brzegowy. Trasy zdefiniowane przez użytkownika można dodać w sieci wirtualnej, aby wpłynąć na określone routingu, ale będzie to statyczne i nie jest częścią reklamy BGP.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>Czy usługa ExpressRoute oferuje umowę dotyczącą poziomu usług (SLA)?

Aby uzyskać więcej informacji, zobacz stronę [umowy SLA usługi ExpressRoute.](https://azure.microsoft.com/support/legal/sla/)

## <a name="supported-services"></a>Obsługiwane usługi

Usługa ExpressRoute obsługuje [trzy domeny routingu](expressroute-circuit-peerings.md) dla różnych typów usług: prywatna komunikacja równorzędna, komunikacja równorzędna firmy Microsoft i komunikacja równorzędna publiczna (przestarzała).

### <a name="private-peering"></a>Prywatna komunikacja równorzędna

**Obsługiwane:**

* Sieci wirtualne, w tym wszystkie maszyny wirtualne i usługi w chmurze

### <a name="microsoft-peering"></a>Komunikacja równorzędna firmy Microsoft

Jeśli twój obwód usługi ExpressRoute jest włączony dla komunikacji równorzędnej platformy Microsoft platformy Azure, można uzyskać dostęp do [publicznych zakresów adresów IP używanych](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) na platformie Azure za pomocą obwodu. Komunikacja równorzędna platformy Azure Microsoft zapewni dostęp do usług aktualnie hostowanych na platformie Azure (z ograniczeniami geograficznymi w zależności od jednostki SKU obwodu). Aby sprawdzić dostępność dla określonej usługi, można sprawdzić dokumentację dla tej usługi, aby sprawdzić, czy istnieje zarezerwowany zakres opublikowany dla tej usługi. Następnie wyszukuj zakresy adresów IP usługi docelowej i porównaj je z zakresami wymienionymi w [pliku XML zakresów adresów IP platformy Azure i tagów usług — Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519). Alternatywnie można otworzyć bilet pomocy technicznej dla danej usługi w celu wyjaśnienia.

**Obsługiwane:**

* [Office 365](https://aka.ms/ExpressRouteOffice365)
* Usługa Power BI — dostępne za pośrednictwem społeczności regionalnej platformy Azure, zobacz [tutaj,](https://docs.microsoft.com/power-bi/service-admin-where-is-my-tenant-located) aby dowiedzieć się, jak znaleźć region dzierżawy usługi Power BI.
* Usługa Azure Active Directory
* [Windows Virtual Desktop](https://azure.microsoft.com/services/virtual-desktop/)
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (społeczność usług globalnych platformy Azure)
* Publiczne adresy IP platformy Azure dla usługi IaaS (maszyny wirtualne, bramy sieci wirtualnej, moduły równoważenia obciążenia itp.)  
* Większość innych usług platformy Azure są również obsługiwane. Skontaktuj się bezpośrednio z usługą, której chcesz użyć do weryfikacji pomocy technicznej.

**Nie jest obsługiwane:**

* CDN
* Azure Front Door
* Serwer uwierzytelniania wieloskładnikowego (starsza wersja)
* Traffic Manager

### <a name="public-peering"></a>Publiczna komunikacja równorzędna

Publiczne komunikacji równorzędnej został wyłączony na nowych obwodów usługi ExpressRoute. Usługi platformy Azure są teraz dostępne w komunikacji równorzędnej firmy Microsoft. Jeśli obwód, który został utworzony przed publiczne komunikacji równorzędnej jest przestarzałe, można użyć komunikacji równorzędnej firmy Microsoft lub komunikacji równorzędnej publicznych, w zależności od usług, które chcesz.

Aby uzyskać więcej informacji i kroki konfiguracji dla komunikacji równorzędnej publicznej, zobacz [Komunikacja publiczna usługi ExpressRoute](about-public-peering.md).

### <a name="why-i-see-advertised-public-prefixes-status-as-validation-needed-while-configuring-microsoft-peering"></a>Dlaczego podczas konfigurowania komunikacji równorzędnej firmy Microsoft jest "anonsowane prefiksy publiczne" jako "Sprawdzanie poprawności potrzebne"?

Firma Microsoft sprawdza, czy określone "Anonsowane prefiksy publiczne" i "Peer ASN" (lub "Asn klienta") są przypisane do Ciebie w rejestrze routingu internetowego. Jeśli otrzymujesz prefiksy publiczne z innej jednostki i jeśli przypisanie nie jest rejestrowane w rejestrze routingu, automatyczne sprawdzanie poprawności nie zostanie zakończone i będzie wymagać ręcznego sprawdzania poprawności. Jeśli automatyczne sprawdzanie poprawności nie powiedzie się, zostanie wyświetlony komunikat "Sprawdzanie poprawności potrzebne".

Jeśli zostanie wyświetlony komunikat "Sprawdzanie poprawności potrzebne", zebrać dokumenty, które pokazują prefiksy publiczne są przypisane do organizacji przez jednostkę, która jest wymieniona jako właściciel prefiksów w rejestrze routingu i przesłać te dokumenty do ręcznego sprawdzania poprawności przez otwierając bilet wsparcia, jak pokazano poniżej.

![](./media/expressroute-faqs/ticket-portal-msftpeering-prefix-validation.png)

### <a name="is-dynamics-365-supported-on-expressroute"></a>Czy dynamics 365 jest obsługiwany w utrysk utryskowych w ucie?

Środowiska Dynamics 365 i Common Data Service (CDS) są hostowane na platformie Azure i dlatego klienci korzystają z podstawowej obsługi usługi ExpressRoute dla zasobów platformy Azure. Można połączyć się z jego punktów końcowych usługi, jeśli filtr routera zawiera regiony platformy Azure środowiska Dynamics 365/CDS są hostowane w.

> [!NOTE]
> [Usługa ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium) **nie** jest wymagana dla łączności Dynamics 365 za pośrednictwem usługi Azure ExpressRoute.

## <a name="data-and-connections"></a>Dane i połączenia

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Czy istnieją ograniczenia dotyczące ilości danych, które można przesyłać za pomocą usługi ExpressRoute?

Nie ustalamy limitu ilości transferu danych. Informacje na temat przepustowości można znaleźć w [informacjach o](https://azure.microsoft.com/pricing/details/expressroute/) cenach.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Jakie szybkości połączeń są obsługiwane przez program ExpressRoute?

Obsługiwane oferty przepustowości:

50 Mb/s, 100 Mb/s, 200 Mb/s, 500 Mb/s, 1 Gb/s, 2 Gb/s, 5 Gb/s, 10 Gb/s

### <a name="which-service-providers-are-available"></a>Którzy dostawcy usług są dostępni?

Zobacz [Partnerów i lokalizacje usługi ExpressRoute,](expressroute-locations.md) aby uzyskać listę dostawców usług i lokalizacji.

## <a name="technical-details"></a>Szczegóły techniczne

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Jakie są wymagania techniczne dotyczące łączenia mojej lokalizacji lokalnej z platformą Azure?

Aby uzyskać wymagania [dotyczące usług ExpressRoute, zobacz stronę wymagań wstępnych usługi ExpressRoute.](expressroute-prerequisites.md)

### <a name="are-connections-to-expressroute-redundant"></a>Czy połączenia z usługą ExpressRoute są zbędne?

Tak. Każdy obwód usługi ExpressRoute ma nadmiarową parę połączeń krzyżowych skonfigurowanych w celu zapewnienia wysokiej dostępności.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Czy jeśli jedno z moich łączy usługi ExpressRoute uchyli się, utracę łączność?

Nie utracisz łączności, jeśli jedno z połączeń krzyżowych ulegnie awarii. Dostępne jest nadmiarowe połączenie obsługujące obciążenie sieci i zapewniające wysoką dostępność obwodu usługi ExpressRoute. Ponadto można utworzyć obwód w innej lokalizacji komunikacji równorzędnej, aby osiągnąć odporność na poziomie obwodu.

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>Jak wdrożyć nadmiarowość w prywatnej komunikacji równorzędnej?

Wiele obwodów usługi ExpressRoute z różnych lokalizacji komunikacji równorzędnej można podłączyć do tej samej sieci wirtualnej, aby zapewnić wysoką dostępność w przypadku, gdy pojedynczy obwód staje się niedostępny. Następnie można [przypisać wyższe wagi](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection) do połączenia lokalnego, aby preferować określony obwód. Zdecydowanie zaleca się, aby klienci konfigurują co najmniej dwa obwody usługi ExpressRoute, aby uniknąć pojedynczych punktów awarii. 

Zobacz [tutaj,](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) aby zaprojektować dla wysokiej dostępności i [tutaj](https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering) do projektowania dla odzyskiwania po awarii.  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Jak zaimplementować nadmiarowość w komunikacji równorzędnej firmy Microsoft?

Jest wysoce zalecane, gdy klienci korzystają z komunikacji równorzędnej firmy Microsoft, aby uzyskać dostęp do usług publicznych platformy Azure, takich jak Usługa Azure Storage lub Azure SQL, a także klientów korzystających z komunikacji równorzędnej firmy Microsoft dla usługi Office 365, którzy implementują wiele obwodów w różnych komunikacji równorzędnej lokalizacji, aby uniknąć pojedynczych punktów awarii. Klienci mogą anonsować ten sam prefiks w obu obwodach i używać [as path prepending](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending) lub anonsować różne prefiksy do określenia ścieżki z lokalnego.

Zobacz [tutaj,](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) aby projektować dla wysokiej dostępności.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Jak zapewnić wysoką dostępność w sieci wirtualnej połączonej z usługą ExpressRoute?

Wysoką dostępność można osiągnąć, łącząc obwody usługi ExpressRoute w różnych lokalizacjach komunikacji równorzędnej (na przykład w Singapurze, Singapurze2) z siecią wirtualną. Jeśli jeden obwód usługi ExpressRoute uginie się, łączność zostanie przejęta awaryjnie z innym obwodem usługi ExpressRoute. Domyślnie ruch opuszczający sieć wirtualną jest kierowany na podstawie routingu wieloskładniczego o jednakowym koszcie (ECMP). Za pomocą funkcji Connection Weight można preferować jeden obwód do drugiego. Aby uzyskać więcej informacji, zobacz [Optymalizacja routingu usługi ExpressRoute](expressroute-optimize-routing.md).

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-peering-or-public-peering-is-preferred-on-the-expressroute-path"></a>Jak upewnić się, że mój ruch przeznaczony dla usług publicznych platformy Azure, takich jak Usługa Azure Storage i Azure SQL w komunikacji równorzędnej firmy Microsoft lub komunikacji równorzędnej publicznej, jest preferowany na ścieżce usługi ExpressRoute?

Należy zaimplementować atrybut *Preferencji lokalnych* na routerach, aby upewnić się, że ścieżka z lokalnego do platformy Azure jest zawsze preferowana w obwodach usługi ExpressRoute.

Zobacz dodatkowe szczegóły [tutaj](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#path-selection-on-microsoft-and-public-peerings) na wybór ścieżki BGP i typowe konfiguracje routera. 

### <a name="if-im-not-co-located-at-a-cloud-exchange-and-my-service-provider-offers-point-to-point-connection-do-i-need-to-order-two-physical-connections-between-my-on-premises-network-and-microsoft"></a><a name="onep2plink"></a>Jeśli nie jestem współlokacyjnie w wymianie w chmurze, a mój dostawca usług oferuje połączenie typu punkt-punkt, czy muszę zamówić dwa fizyczne połączenia między moją siecią lokalną a firmą Microsoft?

Jeśli usługodawca może ustanowić dwa obwody wirtualne Ethernet za pośrednictwem połączenia fizycznego, potrzebujesz tylko jednego połączenia fizycznego. Połączenie fizyczne (na przykład światłowód) jest zakończone na urządzeniu warstwy 1 (L1) (patrz obraz). Dwa obwody wirtualne Ethernet są oznaczone różnymi identyfikatorami sieci VLAN, jednym dla obwodu podstawowego i jednym dla pomocniczego. Te identyfikatory VLAN znajdują się w zewnętrznym nagłówku Ethernet 802.1Q. Wewnętrzny nagłówek Ethernet 802.1Q (nie pokazany) jest mapowany na określoną [domenę routingu Usługi ExpressRoute.](expressroute-circuit-peerings.md)

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Czy można rozszerzyć jeden z moich sieci VLAN na platformę Azure przy użyciu usługi ExpressRoute?

Nie. Nie obsługujemy rozszerzeń łączności warstwy 2 na platformie Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Czy mogę mieć w ramach subskrypcji więcej niż jeden obwód usługi ExpressRoute?

Tak. W subskrypcji może być więcej niż jeden obwód usługi ExpressRoute. Domyślny limit jest ustawiony na 10. W razie potrzeby można skontaktować się z pomocą techniczną firmy Microsoft, aby zwiększyć limit.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Czy mogę mieć obwody usługi ExpressRoute od różnych dostawców usług?

Tak. Obwody usługi ExpressRoute mogą być obsługiwane przez wielu usługodawców. Każdy obwód usługi ExpressRoute jest skojarzony tylko z jednym dostawcą usług. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Widzę dwie lokalizacje komunikacji równorzędnej Usługi ExpressRoute w tym samym metrze, na przykład w Singapurze i Singapurze2. Którą lokalizację komunikacji równorzędnej wybrać do utworzenia obwodu usługi ExpressRoute?
Jeśli usługodawca oferuje usługę ExpressRoute w obu lokacjach, możesz współpracować z dostawcą i wybrać jedną z witryn, aby skonfigurować usługę ExpressRoute. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>Czy mogę mieć wiele obwodów usługi ExpressRoute w tym samym metrze? Czy mogę połączyć je z tą samą siecią wirtualną?

Tak. Możesz mieć wiele obwodów usługi ExpressRoute z tymi samymi lub różnymi dostawcami usług. Jeśli metro ma wiele lokalizacji komunikacji równorzędnej usługi ExpressRoute, a obwody są tworzone w różnych lokalizacjach komunikacji równorzędnej, można połączyć je z tą samą siecią wirtualną. Jeśli obwody są tworzone w tej samej lokalizacji komunikacji równorzędnej, można połączyć do 4 obwodów z tą samą siecią wirtualną.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Jak połączyć sieci wirtualne z obwodem usługi ExpressRoute

Podstawowe kroki to:

* Ustal obwód usługi ExpressRoute i włącz go dostawca usług.
* Użytkownik lub dostawca należy skonfigurować komunikację równorzędnych BGP.
* Połącz sieć wirtualną z obwodem usługi ExpressRoute.

Aby uzyskać więcej informacji, zobacz [Przepływy pracy usługi ExpressRoute dotyczące inicjowania obsługi administracyjnej obwodów i stanów obwodów](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Czy istnieją granice łączności dla mojego obwodu usługi ExpressRoute?

Tak. Artykuł [Partnerów i lokalizacji usługi ExpressRoute](expressroute-locations.md) zawiera omówienie granic łączności dla obwodu usługi ExpressRoute. Łączność dla obwodu usługi ExpressRoute jest ograniczona do jednego regionu geopolitycznego. Łączność można rozszerzyć, aby przejść przez regiony geopolityczne, włączając funkcję premium usługi ExpressRoute.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Czy mogę połączyć się z więcej niż jedną siecią wirtualną z obwodem usługi ExpressRoute?

Tak. W standardowym obwodzie usługi ExpressRoute można mieć maksymalnie 10 połączeń z sieciami wirtualnymi i do 100 w [uziemianym obwodzie usługi ExpressRoute.](#expressroute-premium) 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Mam wiele subskrypcji platformy Azure, które zawierają sieci wirtualne. Czy mogę połączyć sieci wirtualne, które są w oddzielnych subskrypcjach, z jednym obwodem usługi ExpressRoute?

Tak. Można połączyć do 10 sieci wirtualnych w tej samej subskrypcji co obwód lub różne subskrypcje przy użyciu jednego obwodu usługi ExpressRoute. Ten limit można zwiększyć, włączając funkcję premium usługi ExpressRoute.

Aby uzyskać więcej informacji, zobacz [Udostępnianie obwodu usługi ExpressRoute w wielu subskrypcjach.](expressroute-howto-linkvnet-arm.md)

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Mam wiele subskrypcji platformy Azure skojarzonych z różnymi dzierżawami usługi Azure Active Directory lub rejestracjami w ramach umowy Enterprise Agreement. Czy można połączyć sieci wirtualne, które znajdują się w oddzielnych dzierżawców i rejestracji do jednego obwodu usługi ExpressRoute nie w tej samej dzierżawy lub rejestracji?

Tak. Autoryzacje usługi ExpressRoute mogą obejmować granice subskrypcji, dzierżawy i rejestracji bez konieczności dodatkowej konfiguracji. 

Aby uzyskać więcej informacji, zobacz [Udostępnianie obwodu usługi ExpressRoute w wielu subskrypcjach.](expressroute-howto-linkvnet-arm.md)

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Czy sieci wirtualne są połączone z tym samym obwodem odizolowanym od siebie?

Nie. Z punktu widzenia routingu wszystkie sieci wirtualne połączone z tym samym obwodem usługi ExpressRoute są częścią tej samej domeny routingu i nie są odizolowane od siebie. Jeśli potrzebujesz izolacji trasy, musisz utworzyć oddzielny obwód usługi ExpressRoute.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Czy mogę mieć jedną sieć wirtualną połączoną z więcej niż jednym obwodem usługi ExpressRoute?

Tak. Jedną sieć wirtualną można połączyć z maksymalnie czterema obwodami usługi ExpressRoute w tych samych lub różnych lokalizacjach komunikacji równorzędnej. 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Czy mogę uzyskać dostęp do Internetu z moich sieci wirtualnych połączonych z obwodami usługi ExpressRoute?

Tak. Jeśli nie anonsowane są trasy domyślne (0.0.0.0/0) lub prefiksy trasy internetowej za pośrednictwem sesji BGP, można połączyć się z Internetem z sieci wirtualnej połączonej z obwodem usługi ExpressRoute.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Czy mogę zablokować połączenie internetowe z sieciami wirtualnymi połączonymi z obwodami usługi ExpressRoute?

Tak. Można anonsować trasy domyślne (0.0.0.0/0), aby zablokować całą łączność z Internetem z maszynami wirtualnymi wdrożonymi w sieci wirtualnej i kierować cały ruch przez obwód usługi ExpressRoute.

Jeśli anonsujesz trasy domyślne, firma Microsoft wymusi ruch do usług oferowanych za pośrednictwem komunikacji równorzędnej firmy Microsoft (takich jak magazyn platformy Azure i bazy danych SQL) z powrotem do twojej siedziby. Należy skonfigurować routery, aby przywrócić ruch na platformie Azure za pośrednictwem ścieżki komunikacji równorzędnej firmy Microsoft lub przez Internet. Jeśli włączono punkt końcowy usługi dla usługi, ruch do usługi nie jest zmuszony do pomieszczeń. Ruch pozostaje w sieci szkieletowej platformy Azure. Aby dowiedzieć się więcej o punktach końcowych usługi, zobacz [Punkty końcowe usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Czy sieci wirtualne połączone z tym samym obwodem usługi ExpressRoute mogą ze sobą rozmawiać?

Tak. Maszyny wirtualne wdrożone w sieciach wirtualnych połączonych z tym samym obwodem usługi ExpressRoute mogą komunikować się ze sobą.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Czy mogę używać łączności lokacja-lokacja dla sieci wirtualnych w połączeniu z usługą ExpressRoute?

Tak. Usługa ExpressRoute może współistnieć z sieciami VPN typu lokacja lokacja. Zobacz [Konfigurowanie połączeń współistniejących w układzie ekspresowym i lokacja.](expressroute-howto-coexist-resource-manager.md)

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Dlaczego z bramą usługi ExpressRoute w sieci wirtualnej jest skojarzony publiczny adres IP?

Publiczny adres IP jest używany tylko do zarządzania wewnętrznego i nie stanowi zagrożenia bezpieczeństwa sieci wirtualnej.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Czy istnieją ograniczenia dotyczące liczby tras, które mogę reklamować?

Tak. Akceptujemy do 4000 prefiksów trasy dla prywatnej komunikacji równorzędnej i 200 dla komunikacji równorzędnej firmy Microsoft. Możesz zwiększyć tę liczbę do 10 000 tras komunikacji prywatnej, jeśli włączysz funkcję Premium usługi ExpressRoute.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Czy istnieją ograniczenia dotyczące zakresów adresów IP, które mogę reklamować podczas sesji BGP?

Nie akceptujemy prefiksów prywatnych (RFC1918) dla sesji BGP komunikacji równorzędnej firmy Microsoft. Akceptujemy dowolny rozmiar prefiksu (do /32) zarówno w firmie Microsoft, jak i w prywatnej komunikacji równorzędnej.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>Co się stanie, jeśli przekroczę limity BGP?

Sesje BGP zostaną przerwane. Zostaną one zresetowane, gdy liczba prefiksów spadnie poniżej limitu.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Jaki jest czas wstrzymania usługi BGP usługi ExpressRoute? Czy można go regulować?

Czas wstrzymania wynosi 180. Wiadomości keep-alive są wysyłane co 60 sekund. Są to stałe ustawienia po stronie firmy Microsoft, których nie można zmienić. Możliwe jest skonfigurowanie różnych czasomierzy, a parametry sesji BGP zostaną odpowiednio wynegocjowane.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Czy mogę zmienić przepustowość obwodu usługi ExpressRoute?

Tak, można podjąć próbę zwiększenia przepustowości obwodu usługi ExpressRoute w witrynie Azure portal lub przy użyciu programu PowerShell. Jeśli na porcie fizycznym, na którym utworzono obwód, dostępna jest pojemność, zmiana powiedzie się. 

Jeśli zmiana nie powiedzie się, oznacza to, że na bieżącym porcie nie ma wystarczającej pojemności i trzeba utworzyć nowy obwód usługi ExpressRoute o wyższej przepustowości lub że w tej lokalizacji nie ma dodatkowej pojemności, w którym to przypadku nie będzie można zwiększyć Przepustowości. 

Należy również wykonać z dostawcą łączności, aby upewnić się, że aktualizują ograniczenia w swoich sieciach do obsługi zwiększenia przepustowości. Nie można jednak zmniejszyć przepustowości obwodu usługi ExpressRoute. Musisz utworzyć nowy obwód usługi ExpressRoute o niższej przepustowości i usunąć stary obwód.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Jak zmienić przepustowość obwodu usługi ExpressRoute?

Przepustowość obwodu usługi ExpressRoute można zaktualizować za pomocą interfejsu API REST lub polecenia cmdlet programu PowerShell.

## <a name="expressroute-premium"></a>Usługa ExpressRoute premium

### <a name="what-is-expressroute-premium"></a>Co to jest usługa ExpressRoute premium?

Usługa ExpressRoute premium to zbiór następujących funkcji:

* Zwiększono limit tabeli routingu z 4000 tras do 10 000 tras dla komunikacji prywatnej.
* Zwiększona liczba sieci wirtualnych i połączeń Globalny zasięg usługi ExpressRoute, które można włączyć w obwodzie usługi ExpressRoute (wartość domyślna to 10). Aby uzyskać więcej informacji, zobacz tabelę [Limity usługi ExpressRoute.](#limits)
* Łączność z pakietem Office 365
* Globalna łączność za pośrednictwem sieci bazowej firmy Microsoft. Teraz można połączyć sieci wirtualnej w jednym regionie geopolitycznym z obwodem usługi ExpressRoute w innym regionie.<br>
    **Przykłady:**

    *  Sieci wirtualnej utworzonej w Europie Zachodniej można połączyć z obwodem usługi ExpressRoute utworzonym w Dolinie Krzemowej. 
    *  W komunikacji równorzędnej firmy Microsoft prefiksy z innych regionów geopolitycznych są anonsowane w taki sposób, że można połączyć się z, na przykład, SQL Azure w Europie Zachodniej z obwodu w Dolinie Krzemowej.


### <a name="how-many-vnets-and-expressroute-global-reach-connections-can-i-enable-on-an-expressroute-circuit-if-i-enabled-expressroute-premium"></a><a name="limits"></a>Ile sieci wirtualnych i połączeń Globalny zasięg usługi ExpressRoute można włączyć na obwodzie usługi ExpressRoute, jeśli włączyłem usługę ExpressRoute premium?

W poniższych tabelach przedstawiono limity usługi ExpressRoute oraz liczbę połączeń wirtualnych i globalnych połączeń zasięgu usługi ExpressRoute na obwód usługi ExpressRoute:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Jak włączyć usługę ExpressRoute premium?

Funkcje premium usługi ExpressRoute można włączyć, gdy funkcja jest włączona, i można ją zamknąć, aktualizując stan obwodu. Można włączyć expressroute premium w czasie tworzenia obwodu lub można wywołać polecenie cmdlet INTERFEJSU REST / PowerShell.

### <a name="how-do-i-disable-expressroute-premium"></a>Jak wyłączyć usługę ExpressRoute premium?

Można wyłączyć premię Usługi ExpressRoute, wywołując interfejs API REST lub polecenie cmdlet programu PowerShell. Przed wyłączeniem usługi ExpressRoute premium należy upewnić się, że łączność została przeskalowana zgodnie z domyślnymi limitami. Jeśli wykorzystanie skaluje się poza domyślne limity, żądanie wyłączenia premii Usługi ExpressRoute nie powiedzie się.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Czy mogę wybrać funkcje, które chcę z zestawu funkcji premium?

Nie. Nie można wybrać funkcji. Włączamy wszystkie funkcje po włączeniu usługi ExpressRoute premium.

### <a name="how-much-does-expressroute-premium-cost"></a>Ile kosztuje usługa ExpressRoute premium?

Zapoznaj się [ze szczegółami cen](https://azure.microsoft.com/pricing/details/expressroute/) dla kosztów.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Czy oprócz standardowych opłat za korzystanie z usługi ExpressRoute płacę za premię za usługi ExpressRoute?

Tak. Opłaty premium usługi ExpressRoute obowiązują oprócz opłat i opłat za połączenia usługi ExpressRoute wymaganych przez dostawcę łączności.

## <a name="expressroute-local"></a>ExpressRoute Local
### <a name="what-is-expressroute-local"></a>Co to jest usługa ExpressRoute Local?
Usługa ExpressRoute Local jest jednostką SKU obwodu usługi ExpressRoute, oprócz standardowej jednostki SKU i jednostki SKU Premium. Kluczową cechą local jest to, że lokalny obwód w lokalizacji komunikacji równorzędnej usługi ExpressRoute zapewnia dostęp tylko do jednego lub dwóch regionów platformy Azure w tym samym metrze lub w jej pobliżu. Z kolei obwód standardowy zapewnia dostęp do wszystkich regionów platformy Azure w obszarze geopolitycznym i obwodu Premium do wszystkich regionów platformy Azure na całym świecie. 

### <a name="what-are-the-benefits-of-expressroute-local"></a>Jakie są korzyści z usługi ExpressRoute Local?
Podczas gdy musisz płacić za transfer danych wychodzących dla obwodu Standardowego lub Premium ExpressRoute, nie płacisz transferu danych wychodzących oddzielnie dla obwodu lokalnego usługi ExpressRoute. Innymi słowy, cena usługi ExpressRoute Local obejmuje opłaty za transfer danych. Usługa ExpressRoute Local jest bardziej ekonomicznym rozwiązaniem, jeśli masz ogromną ilość danych do przesłania i możesz przenieść dane za pomocą połączenia prywatnego do lokalizacji komunikacji równorzędnej usługi ExpressRoute w pobliżu żądanych regionów platformy Azure. 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>Jakie funkcje są dostępne, a co nie w umiejscowie w umiejscowie w umiejscowie w umiejscowie?
W porównaniu do standardowego obwodu usługi ExpressRoute, obwód lokalny ma ten sam zestaw funkcji, z wyjątkiem:
* Zakres dostępu do regionów platformy Azure w sposób opisany powyżej
* Globalny zasięg usługi ExpressRoute nie jest dostępny w serwisie lokalnym

Usługa ExpressRoute Local ma również takie same limity zasobów (np. liczba sieci wirtualnych na obwód) jak standard. 

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>Gdzie jest dostępna usługa ExpressRoute Local i które regiony platformy Azure są mapowaną dla każdej lokalizacji komunikacji równorzędnej?
Usługa ExpressRoute Local jest dostępna w lokalizacjach komunikacji równorzędnej, w których jeden lub dwa regiony platformy Azure znajdują się w pobliżu. Nie jest dostępna w lokalizacji komunikacji równorzędnej, w której nie ma żadnego regionu platformy Azure w tym stanie lub prowincji lub kraju. Zobacz dokładne mapowania [na stronie Lokalizacje](expressroute-locations-providers.md).  

## <a name="expressroute-for-office-365"></a>Usługa ExpressRoute dla usługi Office 365

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services"></a>Jak utworzyć obwód usługi ExpressRoute, aby połączyć się z usługami Office 365?

1. Przejrzyj [stronę wymagań wstępnych usługi ExpressRoute,](expressroute-prerequisites.md) aby upewnić się, że spełniasz wymagania.
2. Aby upewnić się, że twoje potrzeby w zakresie łączności są spełnione, przejrzyj listę dostawców usług i lokalizacji w artykule [Partnerów i lokalizacji usługi ExpressRoute.](expressroute-locations.md)
3. Zaplanuj swoje wymagania dotyczące pojemności, przeglądając [planowanie sieci i dostrajanie wydajności dla usługi Office 365](https://aka.ms/tune/).
4. Wykonaj kroki wymienione w przepływach pracy, aby skonfigurować przepływy pracy usługi [ExpressRoute dla inicjowania obsługi administracyjnej obwodów i stanów obwodów](expressroute-workflows.md).

> [!IMPORTANT]
> Upewnij się, że podczas konfigurowania łączności z usługami Office 365 włączono dodatek Premium usługi ExpressRoute premium.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services"></a>Czy istniejące obwody usługi ExpressRoute mogą obsługiwać łączność z usługami Office 365?

Tak. Istniejący obwód usługi ExpressRoute można skonfigurować do obsługi łączności z usługami Office 365. Upewnij się, że masz wystarczającą pojemność, aby połączyć się z usługami Office 365 i że włączono dodatek premium. [Planowanie sieci i dostrajanie wydajności usługi Office 365](https://aka.ms/tune/) ułatwia planowanie potrzeb w zakresie łączności. Zobacz [też: Tworzenie i modyfikowanie obwodu usługi ExpressRoute](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>Do jakich usług Office 365 można uzyskać dostęp za połączenie usługi ExpressRoute?

Aby uzyskać aktualną listę usług obsługiwanych za pomocą usługi ExpressRoute, można znaleźć na stronie [Adresów URL i zakresów adresów IP usługi Office 365.](https://aka.ms/o365endpoints)

### <a name="how-much-does-expressroute-for-office-365-services-cost"></a>Ile kosztuje usługa ExpressRoute dla usługi Office 365?

Usługi Office 365 wymagają włączenia dodatku premium. Zobacz [stronę szczegółów cenowania](https://azure.microsoft.com/pricing/details/expressroute/) kosztów.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>W jakich regionach jest obsługiwana usługa ExpressRoute dla usługi Office 365?

Aby uzyskać informacje, [zobacz Partnerzy i lokalizacje usługi ExpressRoute.](expressroute-locations.md)

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Czy mogę uzyskać dostęp do usługi Office 365 przez Internet, nawet jeśli usługa ExpressRoute została skonfigurowana dla mojej organizacji?

Tak. Punkty końcowe usługi Office 365 są dostępne za pośrednictwem Internetu, mimo że usługa ExpressRoute została skonfigurowana dla twojej sieci. Skontaktuj się z zespołem sieciowym organizacji, jeśli sieć w Twojej lokalizacji jest skonfigurowana do łączenia się z usługami Office 365 za pośrednictwem usługi ExpressRoute.

### <a name="how-can-i-plan-for-high-availability-for-office-365-network-traffic-on-azure-expressroute"></a>Jak zaplanować wysoką dostępność ruchu sieciowego usługi Office 365 na platformie Azure ExpressRoute?
Zobacz zalecenie [dotyczące wysokiej dostępności i pracy awaryjnej za pomocą usługi Azure ExpressRoute](https://aka.ms/erhighavailability)

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Czy mogę uzyskać dostęp do usług Office 365 US Government Community (GCC) za pomocą obwodu usługi Azure US US UŁ.

Tak. Punkty końcowe usługi Office 365 GCC są dostępne za pośrednictwem usługi Azure US US Us ExpressRoute. Jednak najpierw należy otworzyć bilet pomocy technicznej w witrynie Azure portal, aby zapewnić prefiksy, które zamierzasz anonsować firmie Microsoft. Łączność z usługami GCC usługi Office 365 zostanie nawiązana po rozwiązaniu biletu pomocy technicznej. 

## <a name="route-filters-for-microsoft-peering"></a>Filtry tras dla komunikacji równorzędnej firmy Microsoft

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Włączam usługę Microsoft w komunikacji równorzędnej po raz pierwszy, jakie trasy zobaczę?

Nie zobaczysz żadnych tras. Musisz dołączyć filtr trasy do obwodu, aby rozpocząć reklamy prefiksów. Aby uzyskać instrukcje, zobacz [Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Włączyłem usługę Komunikacji równorzędnej firmy Microsoft i teraz próbuję wybrać usługę Exchange Online, ale daje mi to błąd, że nie jestem do tego upoważniony.

Podczas korzystania z filtrów tras każdy klient może włączyć komunikację równorzędną firmy Microsoft. Jednak w przypadku korzystania z usług Office 365 nadal musisz uzyskać autoryzację przez office 365.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>Włączono komunikację równorzędną firmy Microsoft przed 1 sierpnia 2017 r., jak mogę korzystać z filtrów tras?

Istniejący obwód będzie nadal reklamował prefiksy usługi Office 365. Jeśli chcesz dodać reklamy prefiksów publicznych platformy Azure za pomocą tej samej komunikacji równorzędnej firmy Microsoft, możesz utworzyć filtr marszruty, wybrać usługi, które są potrzebne (w tym usługi office 365, których potrzebujesz), i dołączyć filtr do komunikacji równorzędnej firmy Microsoft. Aby uzyskać instrukcje, zobacz [Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Mam Microsoft peering w jednej lokalizacji, teraz staram się włączyć go w innej lokalizacji i nie widzę żadnych prefiksów.

* Komunikacja równorzędna obwodów usługi ExpressRoute skonfigurowana przed 1 sierpnia 2017 r. będzie miała wszystkie prefiksy usługi anonsowane za pośrednictwem komunikacji równorzędnej firmy Microsoft, nawet jeśli filtry tras nie są zdefiniowane.

* Komunikacja równorzędna obwodów usługi ExpressRoute skonfigurowana 1 sierpnia 2017 r. lub później nie będzie miała żadnych prefiksów anonsowanych, dopóki filtr trasy nie zostanie dołączony do obwodu. Domyślnie nie zobaczysz żadnych prefiksów.

## <a name="expressroute-direct"></a><a name="expressRouteDirect"></a>Usługa ExpressRoute Direct

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="global-reach"></a><a name="globalreach"></a>Globalny zasięg

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]
