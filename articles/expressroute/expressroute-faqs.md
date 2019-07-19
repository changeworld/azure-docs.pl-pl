---
title: Często zadawane pytania — usługa Azure ExpressRoute | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące usługi ExpressRoute zawiera informacje o obsługiwanych usług platformy Azure, kosztów, danych i połączeń, umowy SLA, dostawcy i lokalizacje, przepustowości i dodatkowe szczegóły techniczne.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: e5674ffb4325eb27af8d0673b2d6ad5ba3a6195e
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854339"
---
# <a name="expressroute-faq"></a>Usługa ExpressRoute — często zadawane pytania

## <a name="what-is-expressroute"></a>Co to jest ExpressRoute?

ExpressRoute to usługa, która umożliwia tworzenie prywatnych połączeń między centrami danych firmy Microsoft i infrastrukturą lokalną lub w funkcji wspólnej lokalizacji. Połączenia ExpressRoute nie omijają publiczny Internet i oferują wyższe bezpieczeństwa, niezawodności i szybkości pracy krótsze opóźnienia niż typowe połączenia przez Internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Jakie są korzyści z używania usługi ExpressRoute i prywatne połączenia sieciowe?

Połączenia ExpressRoute nie odbywają się za pośrednictwem publicznego Internetu. Oferują one wyższe bezpieczeństwa, niezawodności i szybkości, z niższym i spójny opóźnienia niż typowe połączenia przez Internet. W niektórych przypadkach używanie połączeń ExpressRoute do przesyłania danych między lokalnymi urządzeniami i platformy Azure może przynieść znaczną oszczędność kosztów.

### <a name="where-is-the-service-available"></a>Gdy usługa jest dostępna

Zobacz Tę stronę, aby poznać lokalizację i dostępność usługi: [ExpressRoute partnerzy i lokalizacje](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Jak używać usługi ExpressRoute do łączenia się z firmą Microsoft, jeśli nie mam partnerstwa z jednym z partnerów operatora usługi ExpressRoute?

Można wybrać regionalnych operatora i przejście połączenia Ethernet do jednego z programu exchange obsługiwanych lokalizacji dostawcy. Możesz następnie komunikacji równorzędnej z firmą Microsoft w lokalizacji dostawcy. Sprawdź ostatnią sekcję [ExpressRoute partnerzy i lokalizacje](expressroute-locations.md) aby zobaczyć, czy dostawcą usług znajduje się w tych lokalizacjach programu exchange. Następnie można zamówić obwodu usługi ExpressRoute za pośrednictwem dostawcy usług w celu połączenia z platformą Azure.

### <a name="how-much-does-expressroute-cost"></a>Jaki jest koszt usługi ExpressRoute?

Sprawdź [cennik](https://azure.microsoft.com/pricing/details/expressroute/) Aby uzyskać informacje o cenach.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Jeśli płacisz obwód usługi ExpressRoute w danej przepustowości połączenia sieci VPN, który można zakupić od dostawcy usług w mojej sieci ma być tej samej szybkości?

Nie. Połączenie sieci VPN, o dowolnym szybkości można kupić od dostawcy usług. Jednak Twoje połączenie z platformą Azure jest ograniczona do przepustowości obwodu usługi ExpressRoute, która zakupu.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Jeśli płacisz obwód usługi ExpressRoute w danej przepustowości I masz opcję przejścia do większe szybkości, jeśli to konieczne?

Tak. Obwody usługi ExpressRoute są skonfigurowane do umożliwiają serii maksymalnie dwa razy limit przepustowości, który został uzyskany bez dodatkowych kosztów. Skontaktuj się z dostawcą usługi, aby sprawdzić, czy obsługują one tę możliwość. Nie jest to przez dłuższy czas i nie jest gwarantowane. 

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Czy mogę za pomocą tego samego połączenia sieci prywatnej sieci wirtualnej i innych usług platformy Azure jednocześnie?

Tak. Obwód usługi ExpressRoute, po skonfigurowaniu pozwala jednocześnie dostęp do usług w ramach sieci wirtualnej i innych usług platformy Azure. Możesz łączyć z sieciami wirtualnymi w ścieżce prywatnej komunikacji równorzędnej, a także do innych usług za pośrednictwem ścieżki komunikacji równorzędnej firmy Microsoft.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>Usługa ExpressRoute oferuje umowy poziomu usług (SLA)?

Aby uzyskać informacje, zobacz [umowa SLA usługi ExpressRoute](https://azure.microsoft.com/support/legal/sla/) strony.

## <a name="supported-services"></a>Obsługiwane usługi

Usługa ExpressRoute obsługuje [trzy domeny routingu](expressroute-circuit-peerings.md) dla różnych typów usług.

### <a name="private-peering"></a>Prywatna komunikacja równorzędna

* Sieci wirtualne, w tym wszystkich maszyn wirtualnych i usług w chmurze

### <a name="public-peering"></a>Publiczna komunikacja równorzędna

>[!NOTE]
>Publiczna komunikacja równorzędna została wyłączona na obwodów usługi ExpressRoute. Usługi platformy Azure są dostępne w komunikacji równorzędnej firmy Microsoft.
>

* Power BI
* Dynamics 365 for Finance and Operations (wcześniej znane jako Dynamics AX Online)
* Większość usług platformy Azure są obsługiwane. Sprawdź, czy bezpośrednio z usługą, który chcesz użyć, aby sprawdzić, pomocy technicznej.<br><br>
  **Następujące usługi nie są obsługiwane**:
    * CDN
    * Moje drzwi platformy Azure
    * Multi-Factor Authentication
    * Traffic Manager

### <a name="microsoft-peering"></a>Komunikacja równorzędna firmy Microsoft

* [Office 365](https://aka.ms/ExpressRouteOffice365)
* Dynamics 365 
* Power BI — dostępne za pośrednictwem społeczności regionalnej platformy Azure, zobacz [tutaj](https://docs.microsoft.com/power-bi/service-admin-where-is-my-tenant-located) , jak sprawdzić region dzierżawy Power BI. 
* Usługa Azure Active Directory
* [Usługa Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (społeczność globalne usługi platformy Azure)
* Większość usług platformy Azure są obsługiwane. Sprawdź, czy bezpośrednio z usługą, który chcesz użyć, aby sprawdzić, pomocy technicznej.<br><br>**Następujące usługi nie są obsługiwane**:
    * CDN
    * Moje drzwi platformy Azure
    * Multi-Factor Authentication
    * Traffic Manager

## <a name="data-and-connections"></a>Połączeń i danych

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Czy istnieją ograniczenia dotyczące ilości danych, które mogą przesyłać przy użyciu usługi ExpressRoute?

Firma Microsoft nie Ustaw limit ilości przesyłanych danych. Zapoznaj się [cennik](https://azure.microsoft.com/pricing/details/expressroute/) uzyskać informacji na temat stawki za przepustowość.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Szybkość połączenia, które są obsługiwane przez usługę ExpressRoute?

Obsługiwane oferty przepustowości:

50 Mb/s, 100 Mb/s, 200 Mb/s, 500 Mb/s, 1 Gb/s, 2 Gb/s, 5 Gb/s, 10 Gb/s

### <a name="which-service-providers-are-available"></a>Dostawcy usług, które są dostępne?

Zobacz [ExpressRoute partnerzy i lokalizacje](expressroute-locations.md) listy usługodawców i lokalizacji.

## <a name="technical-details"></a>Szczegóły techniczne

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Jakie są wymagania techniczne dotyczące łączenia z moją lokalizacją lokalną na platformę Azure?

Zobacz [strony wymagania wstępne usługi ExpressRoute](expressroute-prerequisites.md) wymagania.

### <a name="are-connections-to-expressroute-redundant"></a>Czy nadmiarowych połączeń usługi expressroute?

Tak. Każdy obwód usługi ExpressRoute ma nadmiarowej parze wielu połączeń skonfigurowanych w celu zapewnienia wysokiej dostępności.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Łączność zostaną utracone w przypadku awarii jednej z moich łączy usługi ExpressRoute?

Nie nastąpi utrata połączenia w przypadku awarii jednego z wielu połączeń. Nadmiarowe połączenie jest dostępne do obsługi obciążenia sieci i zapewnić wysoką dostępność obwód usługi ExpressRoute. Ponadto można utworzyć obwodu, w innej lokalizacji komunikacji równorzędnej do osiągnięcia obwodu na poziomie odporności na błędy.

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>Jak mogę zaimplementować nadmiarowości prywatnej komunikacji równorzędnej?

Wiele obwodów usługi ExpressRoute z różnych lokalizacji komunikacji równorzędnej można podłączyć do tej samej sieci wirtualnej, aby zapewnić wysoką dostępność w przypadku, gdy jeden obwód stanie się niedostępny. Następnie można [przypisać wyższe wagi](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection) do połączenia lokalnego, aby preferować preferowany obwód. Zdecydowanie zalecamy, aby klienci mogli skonfigurować co najmniej dwa obwody usługi ExpressRoute, aby uniknąć pojedynczych punktów awarii. 

Zobacz [tutaj](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) , aby zapoznać się z projektowaniem pod kątem wysokiej dostępności i w [tym miejscu](https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering) na potrzeby projektowania odzyskiwania po awarii.  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Jak wdrożyć nadmiarowość w komunikacji równorzędnej firmy Microsoft?

Zdecydowanie zaleca się, aby klienci używali komunikacji równorzędnej firmy Microsoft w celu uzyskania dostępu do usług publicznych platformy Azure, takich jak Azure Storage lub Azure SQL, a także klientów korzystających z komunikacji równorzędnej firmy Microsoft dla pakietu Office 365, które implementują wiele obwodów w innej komunikacji równorzędnej lokalizacje, aby uniknąć pojedynczych punktów awarii. Klienci mogą anonsować ten sam prefiks na obu obwodach i używać [jako ścieżki oczekujących](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending) lub anonsować różne prefiksy w celu określenia ścieżki ze środowisk lokalnych.

Zobacz [tutaj](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) , aby zapoznać się z projektowaniem pod kątem wysokiej dostępności.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Jak zapewnić wysoką dostępność w sieci wirtualnej, która jest połączona z usługą ExpressRoute?

Wysoka dostępność można osiągnąć przez nawiązanie połączenia z siecią wirtualną obwodów usługi ExpressRoute w innej lokalizacji komunikacji równorzędnej (na przykład, Singapur, singapur2). Jeśli jeden obwód usługi ExpressRoute ulegnie awarii, połączenia zakończy się niepowodzeniem za pośrednictwem innego obwodu usługi ExpressRoute. Domyślnie ruchu wychodzącego z sieci wirtualnej odbywa się na podstawie na równy koszt wiele ścieżek routingu (ECMP). Waga połączenia służy do woli jednym obwodem łączącym. Aby uzyskać więcej informacji, zobacz [Optymalizacja routingu usługi ExpressRoute](expressroute-optimize-routing.md).

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-or-public-peering-is-preferred-on-the-expressroute-path"></a>Jak mogę upewnij się, że mój ruch przeznaczony dla usług publicznych platformy Azure, takich jak Azure Storage i Azure SQL w firmie Microsoft lub publiczna Komunikacja równorzędna, jest preferowany na ścieżce ExpressRoute?

Musisz zaimplementować lokalny atrybut *preferencji* na routerach, aby upewnić się, że ścieżka z lokalnego do platformy Azure jest zawsze preferowana w obwodach usługi ExpressRoute.

Więcej [szczegółowych informacji](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#path-selection-of-microsoft-and-public-peerings) znajduje się w sekcji Wybór ścieżki BGP i typowe konfiguracje routera. 

### <a name="onep2plink"></a>Jeśli nie mam wspólnie w ramach wymiany w chmurze i usługodawcą oferuje połączenia punkt-punkt, czy muszę zamówić łączność obejmującą dwóch fizycznych połączeń między Moje między siecią lokalną a firmą Microsoft?

Jeśli usługodawca może ustanowić dwóch obwodów sieci Ethernet w wirtualnej za pośrednictwem połączenia fizycznego, wystarczy jedno fizyczne połączenie. Fizyczne połączenie (na przykład, światłowód) zostanie zakończone w warstwie 1 urządzenie (L1) (patrz obrazu). Dwie wirtualne obwodów sieci Ethernet są oznaczane za pomocą różnych identyfikatorach sieci VLAN, jeden dla obwód podstawowy i jeden dla pomocniczej. Te identyfikatory sieci VLAN znajdują się w zewnętrznym 802.1Q nagłówek Ethernet. Wewnętrzny 802.1Q nagłówek Ethernet (niewyświetlany) jest mapowany na konkretnym [domeny routingu usługi ExpressRoute](expressroute-circuit-peerings.md).

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Czy mogę przedłużyć jednej z moich sieci VLAN na platformie Azure przy użyciu usługi ExpressRoute?

Nie. Warstwa 2 łączności rozszerzenia nie jest obsługiwana na platformie Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>W mojej subskrypcji można mieć więcej niż jeden obwód usługi ExpressRoute?

Tak. Może mieć więcej niż jeden obwód usługi ExpressRoute w ramach subskrypcji. Domyślny limit jest ustawiony na 10. Możesz skontaktować się Support firmy Microsoft, aby zwiększyć limit, jeśli to konieczne.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Czy można mieć obwodów usługi ExpressRoute z różnych dostawców usług?

Tak. Obwody usługi ExpressRoute może mieć z wielu dostawców usług. Każdy obwód usługi ExpressRoute jest skojarzony z tylko jedną usługę Dostawca. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Czy mogę zobaczyć dwa lokalizacji usługi ExpressRoute komunikacji równorzędnej w tym samym metro, na przykład, Singapur i singapur2. Lokalizacji, do której peering wybrać do utworzenia Mój obwód ExpressRoute
Jeśli usługodawca oferuje usługi ExpressRoute w obu lokacjach, możesz współpracować z dostawcą i pobranie jednej lokacji do skonfigurowania usługi ExpressRoute. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>W tym samym metro można mieć wiele obwodów usługi ExpressRoute? Czy można połączyć je z tej samej sieci wirtualnej?

Tak. Masz wiele obwodów usługi ExpressRoute za pomocą tego samego lub innego usługodawcy. Jeżeli metro ma wiele lokalizacji komunikacji równorzędnej usługi ExpressRoute i obwodów tworzonych w różnych lokalizacjach komunikacji równorzędnej, możesz je połączyć do tej samej sieci wirtualnej. Jeśli obwody są tworzone w tej samej lokalizacji komunikacji równorzędnej, można połączyć maksymalnie 4 obwodów z tą samą siecią wirtualną.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Jak połączyć mojej sieci wirtualne z obwodem usługi ExpressRoute

Przedstawiono podstawowe kroki:

* Ustanów obwodu usługi ExpressRoute i mieć dostawcę usług ją włączyć.
* Użytkownik lub dostawcę, należy skonfigurować komunikacji równorzędnej protokołu BGP (s).
* Łączenie sieci wirtualnej z obwodem usługi ExpressRoute.

Aby uzyskać więcej informacji, zobacz [przepływy pracy ExpressRoute dla aprowizacji obwodu i stanów obwodów](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Czy istnieją połączenia granice Mój obwód ExpressRoute?

Tak. [ExpressRoute partnerzy i lokalizacje](expressroute-locations.md) artykuł zawiera omówienie granic łączności dla obwodu usługi ExpressRoute. Łączność dla obwodu usługi ExpressRoute jest ograniczona w pojedynczym regionie geopolitycznym. Połączenia można rozszerzyć, aby obejmujące wiele regionów geopolitycznych, włączając funkcję premium usługi ExpressRoute.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Czy można połączyć z więcej niż jednej sieci wirtualnej z obwodem usługi ExpressRoute?

Tak. Może mieć maksymalnie 10 połączeń sieci wirtualnych na standardowy obwód usługi ExpressRoute i maksymalnie 100 [obwód usługi ExpressRoute premium](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Masz wiele subskrypcji platformy Azure, które zawierają sieci wirtualne. Czy można połączyć sieci wirtualne, które znajdują się w osobnych subskrypcji do jednego obwodu usługi ExpressRoute?

Tak. Możesz połączyć maksymalnie 10 sieci wirtualnych w tej samej subskrypcji co obwód lub różne subskrypcje, korzystając z pojedynczego obwodu usługi ExpressRoute. Ten limit można zwiększyć przez włączenie funkcji premium usługi ExpressRoute.

Aby uzyskać więcej informacji, zobacz [udostępnianie obwodu usługi ExpressRoute w ramach wielu subskrypcji](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Masz wiele subskrypcji platformy Azure skojarzone z różnych dzierżaw usługi Azure Active Directory lub rejestracji umowy Enterprise Agreement. Czy można połączyć sieci wirtualne, które znajdują się w oddzielnych dzierżaw i rejestracji do pojedynczego obwodu usługi ExpressRoute nie znajduje się w tej samej dzierżawy lub rejestracji?

Tak. Autoryzacji ExpressRoute mogą znajdować się na granice subskrypcji, dzierżawcy i rejestracji bez konieczności dodatkowej konfiguracji. 

Aby uzyskać więcej informacji, zobacz [udostępnianie obwodu usługi ExpressRoute w ramach wielu subskrypcji](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Sieci wirtualne są połączone ten sam obwód odizolowane od siebie?

Nie. Z punktu widzenia routingu wszystkie sieci wirtualne połączone z tym samym obwodem usługi ExpressRoute są częścią tej samej domenie routingu i nie są od siebie odizolowane. Jeśli potrzebujesz izolacji trasy, musisz utworzyć oddzielne obwód usługi ExpressRoute.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Czy można mieć jedną sieć wirtualną podłączony do więcej niż jeden obwód usługi ExpressRoute?

Tak. Można połączyć pojedynczą sieć wirtualną z maksymalnie czterema obwodami ExpressRoute w jednej lub innej lokalizacji komunikacji równorzędnej. 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Z mojej sieci wirtualnych, które zostały podłączone do obwodów usługi ExpressRoute ma dostęp do Internetu?

Tak. Jeśli nie anonsujesz trasy domyślnej (0.0.0.0/0) lub prefiksy trasy Internet za pośrednictwem sesji protokołu BGP, możesz połączyć się z Internetem z sieci wirtualnej połączonej z obwodem usługi ExpressRoute.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Czy można zablokować połączenia internetowego z sieci wirtualne podłączone do obwodów usługi ExpressRoute?

Tak. Można anonsować trasy domyślnej (0.0.0.0/0) do Blokuj wszystkie połączenia internetowego z maszyn wirtualnych wdrożonych w ramach sieci wirtualnej, a następnie kierować cały ruch się za pośrednictwem obwodu usługi ExpressRoute.

Jeśli możesz anonsować trasy domyślne, firma Microsoft wymuszają ruch usługi oferowane za pośrednictwem komunikacji równorzędnej (np. usługi Azure storage i bazą danych SQL) firmy Microsoft do środowisku lokalnym. Należy skonfigurować routery do zwrócenia ruchu na platformie Azure za pośrednictwem ścieżki komunikacji równorzędnej firmy Microsoft lub za pośrednictwem Internetu. Po włączeniu punktu końcowego usługi dla usługi, ruch do usługi nie jest zmuszony do środowisku lokalnym. Ruch pozostaje w sieci szkieletowej platformy Azure. Aby dowiedzieć się więcej na temat punktów końcowych usługi, zobacz [punkty końcowe usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Sieci wirtualne połączone z tym samym obwodem usługi ExpressRoute skontaktować się ze sobą?

Tak. Maszyny wirtualne wdrożone w sieciach wirtualnych połączonych z tym samym obwodem usługi ExpressRoute może komunikować się ze sobą.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Czy można używać połączenia lokacja lokacja dla sieci wirtualnych w połączeniu z usługą ExpressRoute?

Tak. Usługa ExpressRoute może współistnieć z sieci VPN typu lokacja lokacja. Zobacz [Konfigurowanie usługi ExpressRoute i współistniejących połączeń lokacja lokacja](expressroute-howto-coexist-resource-manager.md).

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Dlaczego jest publiczny adres IP skojarzony z bramą ExpressRoute w sieci wirtualnej

Publiczny adres IP jest używany do zarządzania wewnętrznych, tylko, a nie stanowi zagrożenie bezpieczeństwa w sieci wirtualnej.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Czy istnieją ograniczenia liczby tras, które można było ogłaszać dłuższe?

Tak. Akceptujemy do 4000 prefiksów tras do prywatnej komunikacji równorzędnej i 200 dla komunikacji równorzędnej firmy Microsoft. Może to zwiększyć do 10 000 tras do prywatnej komunikacji równorzędnej, po włączeniu funkcji premium usługi ExpressRoute.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Czy istnieją ograniczenia dotyczące zakresów adresów IP I można anonsować za pośrednictwem sesji protokołu BGP?

Prefiksy prywatne (RFC1918) nie są akceptowane dla sesji protokołu BGP z komunikacji równorzędnej firmy Microsoft. Akceptujemy każdy rozmiar prefiksu (do/32) zarówno przez firmę Microsoft, jak i prywatną komunikację równorzędną.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>Co się stanie w przypadku przekroczenia Protokół BGP ogranicza?

Sesje protokołu BGP zostanie porzucony. One zostaną zresetowane, gdy liczba prefiks spadnie poniżej limitu.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Co to jest czas wstrzymania BGP usługi ExpressRoute? Czy może być regulowany?

Czas wstrzymania to 180. Komunikaty utrzymywania aktywności są wysyłane co 60 sekund. Te ustawienia są stałych po stronie firmy Microsoft, nie można jej zmienić. Istnieje możliwość skonfigurowania różnych czasomierze i będzie negocjowane odpowiednio parametry sesji protokołu BGP.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Czy można zmienić przepustowość obwodu usługi ExpressRoute?

Tak, można spróbować zwiększyć przepustowość obwodu usługi ExpressRoute w witrynie Azure portal lub przy użyciu programu PowerShell. Brak dostępnej pojemności na port fizyczny, w której utworzono obwodu, zmiany zakończy się pomyślnie. 

Jeśli zmiany nie powiedzie się, oznacza to, albo nie ma za małą pojemność na bieżącego portu i należy utworzyć nowy obwód usługi ExpressRoute z wyższej przepustowości lub, w tej lokalizacji nie ma żadnych dodatkowych możliwości, w którym to przypadku nie można zwiększyć przepustowość. 

Należy również wykonać kolejne czynności z dostawcą połączenia, aby upewnić się, aby zaktualizować ograniczenia w obrębie ich sieci w celu obsługi zwiększonej przepustowości. Nie można jednak zmniejszyć przepustowość obwodu usługi ExpressRoute. Musisz utworzyć nowy obwód usługi ExpressRoute za pomocą mniejszej przepustowości i Usuń stare obwodu.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Jak zmienić przepustowość obwodu usługi ExpressRoute?

Możesz zaktualizować przepustowość obwodu usługi ExpressRoute za pomocą interfejsu API REST lub polecenia cmdlet programu PowerShell.

## <a name="expressroute-premium"></a>ExpressRoute premium

### <a name="what-is-expressroute-premium"></a>Co to jest ExpressRoute w wersji premium?

ExpressRoute premium jest kolekcją następujące funkcje:

* Zwiększono routingu limit tabeli z tras 4000 do 10 000 tras do prywatnej komunikacji równorzędnej.
* Zwiększenie liczby połączeń sieci wirtualne i usługi ExpressRoute zasięgu globalnym, które można włączyć dla obwodu usługi ExpressRoute (wartość domyślna to 10). Aby uzyskać więcej informacji, zobacz [limity usługi ExpressRoute](#limits) tabeli.
* Łączność z usługi Office 365 i Dynamics 365.
* Globalna łączność między za pośrednictwem sieci podstawowej firmy Microsoft. Teraz można połączyć sieć wirtualną w jednym regionie geopolitycznym obwodu usługi ExpressRoute w innym regionie.<br>
    **Przykłady:**

    *  Możliwe jest łączenie sieci wirtualnej utworzonej w regionie Europa Zachodnia z obwodem usługi ExpressRoute utworzony w Dolinie Krzemowej. 
    *  W komunikacji równorzędnej firmy Microsoft prefiksów z innych regionów geopolitycznych są anonsowane w taki sposób, że można nawiązać połączenie, na przykład SQL Azure w regionie Europa Zachodnia z obwodem w Dolinie Krzemowej.


### <a name="limits"></a>Liczba połączeń sieci wirtualne i usługi ExpressRoute zasięgu globalnym można włączyć na obwód usługi ExpressRoute włączenie ExpressRoute w wersji premium?

W poniższej tabeli przedstawiono limity usługi ExpressRoute i połączeń sieci wirtualne i usługi ExpressRoute zasięgu globalnym na obwód usługi ExpressRoute:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Jak włączyć ExpressRoute w wersji premium?

Funkcje premium usługi ExpressRoute można włączyć, jeśli funkcja jest włączona i może zostać wyłączony, aktualizując Stan obwodu. Możesz włączyć w czasie tworzenia obwodu ExpressRoute w wersji premium lub może wywołać interfejs API REST / polecenia cmdlet programu PowerShell.

### <a name="how-do-i-disable-expressroute-premium"></a>Jak wyłączyć ExpressRoute w wersji premium?

ExpressRoute premium można wyłączyć przez wywołanie interfejsu API REST lub polecenia cmdlet programu PowerShell. Upewnij się, że była skalowana potrzeb łączności, aby spełnić limity domyślne przed wyłączeniem ExpressRoute w wersji premium. Skaluje swoje użycie przekroczą ustalone limity domyślne, żądanie wyłączenia ExpressRoute premium nie powiedzie się.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Można można wybrać funkcje, których chcę z zestawu funkcji premium?

Nie. Nie można wybrać funkcje. Włączyliśmy wszystkich funkcji, po włączeniu ExpressRoute w wersji premium.

### <a name="how-much-does-expressroute-premium-cost"></a>Jaki jest koszt premium usługi ExpressRoute?

Zapoznaj się [cennik](https://azure.microsoft.com/pricing/details/expressroute/) kosztów.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Czy opłaty za usługi ExpressRoute premium oprócz standardowe opłaty za usługę ExpressRoute?

Tak. ExpressRoute premium opłaty na podstawie obwodu usługi ExpressRoute opłaty za magazyn i wymagane przez dostawcę połączenia.

## <a name="expressroute-local"></a>ExpressRoute lokalny
### <a name="what-is-expressroute-local"></a>Co to jest ExpressRoute Local?
ExpressRoute Local to jednostka SKU obwodu ExpressRoute dostępnego na [ExpressRoute Direct](expressroute-erdirect-about.md). Kluczową funkcją lokalną jest to, że lokalny obwód w lokalizacji komunikacji równorzędnej ExpressRoute zapewnia dostęp tylko do jednego lub dwóch regionów świadczenia usługi Azure w lub w prawie jednej linii metra. Natomiast standardowy obwód zapewnia dostęp do wszystkich regionów świadczenia usługi Azure w obszarze geopolitycznym oraz obwodu Premium do wszystkich regionów świadczenia usługi Azure globalnie. 

### <a name="what-are-the-benefits-of-expressroute-local"></a>Jakie korzyści wynikają z ExpressRoute lokalnego?
Gdy musisz zapłacać transfer danych wychodzących dla obwodu usługi ExpressRoute w warstwie Standardowa lub Premium, nie płacisz transferów danych wychodzących oddzielnie dla lokalnego obwodu usługi ExpressRoute. Innymi słowy, Cena ExpressRoute lokalnego obejmuje opłaty za transfer danych. ExpressRoute Local to bardziej ekonomiczne rozwiązanie, jeśli masz ogromne ilości danych do przetransferowania, a dane można przenieść za pośrednictwem połączenia prywatnego do lokalizacji komunikacji równorzędnej ExpressRoute znajdującej się blisko żądanych regionów świadczenia usługi Azure. 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>Jakie funkcje są dostępne i co nie jest ExpressRoute lokalne?
W porównaniu do standardowego obwodu ExpressRoute, obwód lokalny ma ten sam zestaw funkcji, z wyjątkiem:
* Zakres dostępu do regionów platformy Azure zgodnie z powyższym opisem
* ExpressRoute Global Reach nie jest dostępna w lokalnym

ExpressRoute lokalna ma również te same limity dotyczące zasobów (np. liczbę sieci wirtualnych na obwód) jako standard. 

### <a name="how-to-configure-expressroute-local"></a>Jak skonfigurować lokalne ExpressRoute? 
ExpressRoute Local jest dostępny tylko w ExpressRoute bezpośredniej. Najpierw należy skonfigurować port Direct ExpressRoute. Po utworzeniu portu bezpośredniego można utworzyć obwód lokalny zgodnie z instrukcjami znajdującymi się [tutaj](expressroute-howto-erdirect.md).

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>Gdzie jest dostępna usługa ExpressRoute lokalna i które regiony platformy Azure są mapowane na poszczególne lokalizacje komunikacji równorzędnej?
ExpressRoute Local jest dostępny w lokalizacjach komunikacji równorzędnej, w których jeden lub dwa regiony platformy Azure są zamknięte. Nie jest on dostępny w lokalizacji komunikacji równorzędnej, w której nie ma regionu świadczenia usługi Azure w tym stanie lub województwie lub kraju. Sprawdź dokładne mapowania na [stronie Lokalizacje](expressroute-locations-providers.md).  

## <a name="expressroute-for-office-365"></a>Usługa ExpressRoute dla usługi Office 365

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services"></a>Jak utworzyć obwód usługi ExpressRoute, aby połączyć się z usługami Office 365?

1. Przegląd [strony wymagania wstępne usługi ExpressRoute](expressroute-prerequisites.md) się upewnić, że spełnione są wymagania.
2. Aby upewnić się, że spełnione są Twoje potrzeby w zakresie łączności, zapoznaj się z listy dostawców usług i lokalizacje w [ExpressRoute partnerzy i lokalizacje](expressroute-locations.md) artykułu.
3. Planowanie wymagań dotyczących pojemności, przeglądając [Planowanie sieci i dostrajanie wydajności dla usługi Office 365](https://aka.ms/tune/).
4. Wykonaj następujące kroki w przepływach pracy, aby skonfigurować łączność [przepływy pracy ExpressRoute dla aprowizacji obwodu i stanów obwodów](expressroute-workflows.md).

> [!IMPORTANT]
> Upewnij się, że włączono dodatek ExpressRoute premium podczas konfigurowania łączności z usługami Office 365.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services-and-dynamics-365"></a>Czy moje istniejące obwodów usługi ExpressRoute może obsługiwać łączność z usługami Office 365 i Dynamics 365?

Tak. Istniejący obwód usługi ExpressRoute można skonfigurować do obsługi łączności z usługami Office 365. Upewnij się, że masz wystarczająco duże, aby połączyć się z usługami Office 365 i włączeniu dodatku premium. [Planowanie sieci i dostrajanie wydajności dla usługi Office 365](https://aka.ms/tune/) musi pomaga zaplanować łączności. Zobacz też [tworzenie i modyfikowanie obwodu ExpressRoute](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>Jakie usługi Office 365 services jest dostępna za pośrednictwem połączenia usługi ExpressRoute?

Zapoznaj się [URL usługi Office 365 i zakresy adresów IP](https://aka.ms/o365endpoints) strony, aby uzyskać aktualną listę usług obsługiwanych za pośrednictwem usługi ExpressRoute.

### <a name="how-much-does-expressroute-for-office-365-services-cost"></a>Ile kosztuje usługa ExpressRoute dla kosztu usługi Office 365?

Usługi Office 365 wymaga dodatku premium, aby włączyć. Zobacz [stronę szczegółów cennika](https://azure.microsoft.com/pricing/details/expressroute/) kosztów.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>W jakich regionach jest obsługiwane usługi ExpressRoute dla usługi Office 365?

Zobacz [ExpressRoute partnerzy i lokalizacje](expressroute-locations.md) informacji.

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Można uzyskać dostęp do usługi Office 365 za pośrednictwem Internetu, nawet jeśli usługa ExpressRoute została skonfigurowana dla mojej organizacji?

Tak. Punkty końcowe usługi Office 365 są dostępne za pośrednictwem Internetu, nawet jeśli usługa ExpressRoute został skonfigurowany w sieci. Sprawdź sieci zespołowi w organizacji, jeśli sieć lokalizacji jest skonfigurowany nawiązać połączenia z usługami Office 365 za pośrednictwem usługi ExpressRoute.

### <a name="how-can-i-plan-for-high-availability-for-office-365-network-traffic-on-azure-expressroute"></a>Jak można zaplanować wysokiej dostępności dla usługi Office 365, ruch sieciowy w ramach usługi Azure ExpressRoute?
Zobacz zalecenie dla [wysokiej dostępności i pracy awaryjnej przy użyciu usługi Azure ExpressRoute](https://aka.ms/erhighavailability)

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Można uzyskać dostęp do usług Office 365 US Government Community (GCC) za pośrednictwem obwodu usługi ExpressRoute systemu Azure US Government?

Tak. Punkty końcowe usługi GCC usługi Office 365 są dostępne za pośrednictwem usługi Azure US Government usługi ExpressRoute. Jednakże należy najpierw otworzyć bilet pomocy technicznej w witrynie Azure portal, aby zapewnić prefiksy, które mają być anonsowane do firmy Microsoft. Łączność z usługami Office 365 GCC zostanie nawiązane, po usunięciu bilet pomocy technicznej. 

## <a name="route-filters-for-microsoft-peering"></a>Filtrów tras dla komunikacji równorzędnej firmy Microsoft

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Czy mogę jestem włączenie komunikacji równorzędnej firmy Microsoft po raz pierwszy, jakie trasy zostanie wyświetlony?

Nie będą widzieć wszystkie trasy. Musisz dołączyć filtru tras do obwodu można uruchomić anonse prefiks. Aby uzyskać instrukcje, zobacz [Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Po włączeniu komunikacji równorzędnej firmy Microsoft i teraz próbuję wybierz usługi Exchange Online, ale jej zapewniając mnie błąd I nie mam uprawnień to zrobić.

Korzystając z filtrów tras, każdy klient można włączyć komunikację równorzędną firmy Microsoft. Jednak co umożliwia korzystanie z usługi Office 365, nadal musisz uzyskać autoryzowane przez usługę Office 365.

### <a name="do-i-need-to-get-authorization-for-turning-on-dynamics-365-over-microsoft-peering"></a>Należy uzyskać pozwolenie na włączenie Dynamics 365 za pośrednictwem komunikacji równorzędnej firmy Microsoft?

Nie, nie trzeba autoryzacji dla Dynamics 365. Można utworzyć regułę, a następnie wybierz społeczności Dynamics 365, bez autoryzacji.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>Czy włączono komunikacji równorzędnej przed 1 sierpnia 2017 r., jak skorzystać z filtrów tras firmy Microsoft?

Twój istniejący obwód będzie nadal anonsowania prefiksów dla usługi Office 365 i Dynamics 365. Jeśli chcesz dodać anonse prefiksy publiczne platformy Azure za pośrednictwem tego samego komunikacji równorzędnej firmy Microsoft, można utworzyć filtru tras, wybierz usługi, których potrzebujesz anonsowane (w tym usługi Office 365, czego potrzebujesz i Dynamics 365) i dołączyć filtr do firmy Microsoft komunikacji równorzędnej. Aby uzyskać instrukcje, zobacz [Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Mam komunikacji równorzędnej w jednej lokalizacji firmy Microsoft, a teraz próbuję włączyć ją w innej lokalizacji i nie widzę żadnych prefiksów.

* Obwodów usługi ExpressRoute, które zostały skonfigurowane przed 1 sierpnia 2017 r. komunikacji równorzędnej firmy Microsoft będzie miał wszystkie prefiksy usługi anonsowanego za pośrednictwem komunikacji równorzędnej firmy Microsoft, nawet jeśli nie zdefiniowano filtry tras.

* Komunikacja równorzędna firmy Microsoft obwodów usługi ExpressRoute, skonfigurowanych po 1 sierpnia 2017 r. nie będzie miał wszelkie prefiksy anonsowane do czasu podłączenia filtru tras do obwodu. Domyślnie, zostanie wyświetlony bez prefiksów.

## <a name="expressRouteDirect"></a>ExpressRoute bezpośrednie

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="globalreach"></a>Global Reach

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]
