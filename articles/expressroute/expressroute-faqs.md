---
title: Usługa Azure ExpressRoute — często zadawane pytania | Dokumentacja firmy Microsoft
description: Często zadawane pytania usługi ExpressRoute zawiera informacje o obsługiwanych usług Azure, kosztów, danych i połączeń, umowy SLA, dostawców i lokalizacje, przepustowości i dodatkowe szczegóły techniczne.
documentationcenter: na
services: expressroute
author: cherylmc
manager: jeconnoc
editor: ''
ms.assetid: 09b17bc4-d0b3-4ab0-8c14-eed730e1446e
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/14/2018
ms.author: cherylmc
ms.openlocfilehash: f68a4d699d1ad6592fe5481cd69795b06e7d8fca
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34160873"
---
# <a name="expressroute-faq"></a>Usługa ExpressRoute — często zadawane pytania

## <a name="what-is-expressroute"></a>Co to jest ExpressRoute?

ExpressRoute jest usługa Azure, która umożliwia tworzenie prywatnych połączeń między centrach danych firmy Microsoft i infrastrukturę, która jest lokalnie lub w zakładzie wspólnej lokalizacji. Połączenia ExpressRoute nie go za pośrednictwem publicznej sieci Internet i oferują wyższej bezpieczeństwa, niezawodności i szybkości z opóźnieniami niższe niż typowe połączenia za pośrednictwem Internetu.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Jakie są zalety programu ExpressRoute, jak i połączeń sieci prywatnej?

Połączenia ExpressRoute nie odbywają się za pośrednictwem publicznego Internetu. Oferują one wyższej bezpieczeństwa, niezawodności i szybkości pracy, dolnej i spójny opóźnienia niż typowe połączenia za pośrednictwem Internetu. W niektórych przypadkach przy użyciu połączenia ExpressRoute na przesyłanie danych między lokalnymi urządzeniami i Azure może spowodować znaczne oszczędności.

### <a name="where-is-the-service-available"></a>Gdy usługa jest dostępna

Zobacz tę stronę w celu lokalizacji usługi i dostępność: [ExpressRoute partnerów i lokalizacje](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Jak używać usługi ExpressRoute połączenia z firmą Microsoft, jeśli nie mam powiązania z jednym z partnerów ExpressRoute — operator

Możesz wybrać operator regionalnych i trafić połączenia Ethernet do jednego z programu exchange obsługiwanych lokalizacji dostawcy. Następnie można elementu równorzędnego z firmą Microsoft w lokalizacji dostawcy. Sprawdź w ostatniej sekcji [ExpressRoute partnerów i lokalizacje](expressroute-locations.md) aby zobaczyć, czy usługodawca znajduje się w tych lokalizacjach programu exchange. Następnie można zamówić obwodu usługi ExpressRoute za pośrednictwem dostawcy usługi z usługą Azure.

### <a name="how-much-does-expressroute-cost"></a>Jaki jest koszt usługi ExpressRoute?

Sprawdź [szczegóły cennika](https://azure.microsoft.com/pricing/details/expressroute/) uzyskać informacje o cenach.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Jeśli opłacać obwodu usługi ExpressRoute przepustowości danego połączenia sieci VPN, które można kupić z usługodawcą sieci ma być tej samej szybkości?

Nie. Połączenia sieci VPN, o dowolnym szybkości możesz kupić od dostawcy usług. Jednak połączenie Azure jest ograniczona do przepustowości obwodu ExpressRoute zakupu.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Jeśli opłacać obwodu usługi ExpressRoute danego przepustowości I masz możliwość serii do większych szybkościach w razie potrzeby?

Tak. Obwody usługi ExpressRoute są skonfigurowane do umożliwiają serii maksymalnie dwa razy przepustowości limit kupowane dla bez dodatkowych kosztów. Skontaktuj się z dostawcą usług, aby zobaczyć, czy obsługują one tej możliwości.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Czy można używać tego samego połączenia sieci prywatnej z sieci wirtualnej i innych usług Azure równocześnie?

Tak. Obwodu usługi ExpressRoute, gdy zostanie ustawiona, umożliwia dostęp do usług sieci wirtualnej i innych usług Azure jednocześnie. Połączenie z siecią wirtualną za pośrednictwem ścieżki prywatnej komunikacji równorzędnej i innych usług za pośrednictwem publicznej komunikacji równorzędnej ścieżki.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>ExpressRoute oferuje Umowa dotycząca poziomu usług (SLA)?

Aby uzyskać informacje, zobacz [ExpressRoute SLA](https://azure.microsoft.com/support/legal/sla/) strony.

## <a name="supported-services"></a>Obsługiwane usługi

Obsługuje ExpressRoute [trzy domeny routingu](expressroute-circuit-peerings.md) dla różnych typów usług.

### <a name="private-peering"></a>Prywatna komunikacja równorzędna

* Sieci wirtualne, w tym wszystkie maszyny wirtualne i usługi w chmurze

### <a name="public-peering"></a>Publiczna komunikacja równorzędna

>[!NOTE]
>Komunikacji równorzędnej firmy Microsoft jest to preferowany sposób dostępu do wszystkich usług hostowanych na platformie Azure.
>

* Power BI
* Dynamics 365 Finanse i operacji (wcześniej znane jako Dynamics AX Online)
* Większość usług Azure są obsługiwane. Sprawdź, czy bezpośrednio z usługą, który chcesz użyć do sprawdzenia pomocy technicznej.<br>
  Następujące usługi nie są obsługiwane:
    * CDN
    * Visual Studio Team Services testów obciążenia
    * Multi-Factor Authentication
    * Traffic Manager

### <a name="microsoft-peering"></a>Komunikacja równorzędna firmy Microsoft

* [Office 365](http://aka.ms/ExpressRouteOffice365)
* Dynamics 365 zaangażowanie klientów aplikacji (wcześniej znane jako CRM Online)
  * Dynamics 365 sprzedaży
  * Dynamics 365 dla klientów usługi
  * Dynamics 365 usługi pola
  * Dynamics 365 usługi projektu
* Przy użyciu [filtry tras](#route-filters-for-microsoft-peering), możesz uzyskać dostęp do tej samej usługi publiczne z komunikacji równorzędnej firmy Microsoft:
  * Power BI
  * Dynamics 365 Finanse i operacje
  * Większość usług Azure są obsługiwane. Sprawdź, czy bezpośrednio z usługą, który chcesz użyć do sprawdzenia pomocy technicznej.<br>
  Następujące usługi nie są obsługiwane:
    * CDN
    * Visual Studio Team Services testów obciążenia
    * Multi-Factor Authentication
    * Traffic Manager

## <a name="data-and-connections"></a>Dane i połączeń

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Czy istnieją ograniczenia dotyczące ilości danych, który można przenieść za pomocą usługi ExpressRoute?

Firma Microsoft nie ustawić limit wielkości transferu danych. Zapoznaj się [szczegóły cennika](https://azure.microsoft.com/pricing/details/expressroute/) informacji o szybkości przepustowości.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Szybkość połączenia, które są obsługiwane przez usługi ExpressRoute?

Obsługiwane oferty przepustowości:

50 Mb/s, 100 Mb/s, 200 Mb/s, 500 Mb/s, 1 Gb/s, 2 Gb/s, 5 Gb/s, 10 Gb/s

### <a name="which-service-providers-are-available"></a>Dostawcy usług, które są dostępne?

Zobacz [ExpressRoute partnerów i lokalizacje](expressroute-locations.md) listy usługodawców i lokalizacji.

## <a name="technical-details"></a>Szczegóły techniczne

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Jakie są wymagania techniczne dotyczące łączenia mojej lokalizacji lokalnej na platformie Azure?

Zobacz [strony wymagania wstępne usługi ExpressRoute](expressroute-prerequisites.md) wymagania.

### <a name="are-connections-to-expressroute-redundant"></a>Czy nadmiarowych połączeń usługi expressroute?

Tak. Każdy obwód usługi ExpressRoute ma parze nadmiarowych cross połączeń skonfigurowanych w celu zapewnienia wysokiej dostępności.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Łączność zostaną utracone w przypadku awarii jednego łącza ExpressRoute?

Nie nastąpi utrata połączenia, jeśli jedno z połączeń między zakończy się niepowodzeniem. Nadmiarowe połączenia jest dostępna do obsługi obciążenia sieci i zapewnić wysoką dostępność obwodu usługi ExpressRoute. Ponadto można tworzyć obwód w innej lokalizacji komunikacji równorzędnej do osiągnięcia obwodu poziom odporności.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Jak zapewnić wysoką dostępność w sieci wirtualnej podłączone do usługi ExpressRoute?

Wysoką dostępność można osiągnąć łącząc obwody usługi ExpressRoute w różnych lokalizacjach komunikacji równorzędnej (np. Singapuru, Singapore2) do sieci wirtualnej. Jeśli jeden obwód usługi ExpressRoute ulegnie awarii, połączenia zostaną przełączone awaryjnie do innego obwodu usługi expressroute. Domyślnie ruchu wychodzącego sieci wirtualnej jest kierowany oparte na taki sam koszt wielościeżkowe routingu ECMP (). Waga połączenia służy do woli jednego obwodu. Zobacz [optymalizacji routingu usługi ExpressRoute](expressroute-optimize-routing.md) dodatkowe szczegóły dotyczące wagi połączenia.

### <a name="onep2plink"></a>Jeśli nie mam umieszczonych w chmurze programu exchange i usługodawcą oferuje połączeniem, należy kolejność dwóch fizycznego połączenia między Moja sieć lokalną i firmy Microsoft?

Jeśli usługodawca może nawiązać dwie wirtualne obwody Ethernet za pośrednictwem połączenia fizycznego, wystarczy tylko jedno połączenie fizyczne. Połączenie fizyczne (na przykład, światłowód) zostało zakończone w warstwie 1 urządzenia (L1) (zobacz obraz). Dwie wirtualne obwody Ethernet oznakowane z różnych identyfikatorów sieci VLAN, jeden dla podstawowego obwodu i drugi dla pomocniczej. Te identyfikatory sieci VLAN znajdują się w zewnętrznym 802.1Q nagłówka Ethernet. Wewnętrzny 802.1Q nagłówka Ethernet (tego nie pokazano) jest mapowany na określony [domeny routingu usługi ExpressRoute](expressroute-circuit-peerings.md).

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Czy mogę rozszerzyć jedną z mojej sieci VLAN na platformie Azure przy użyciu usługi ExpressRoute?

Nie. Warstwy 2 łączności rozszerzenia nie jest obsługiwana na platformie Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Czy można mieć więcej niż jeden obwód usługi ExpressRoute w mojej subskrypcji?

Tak. Może mieć więcej niż jeden obwód usługi ExpressRoute w ramach subskrypcji. Domyślny limit wynosi 10. Możesz skontaktować się Microsoft Support Aby zwiększyć limit, jeśli to konieczne.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Czy można mieć obwody usługi ExpressRoute z innego usługodawcy.

Tak. Może mieć obwody usługi ExpressRoute z wielu usługodawców. Każdy obwód usługi ExpressRoute, jest skojarzona z tylko jedną usługę dostawcy. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-eg-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Dwa ExpressRoute komunikacji równorzędnej lokalizacji w tej samej metro, np. Singapuru i Singapore2 są widoczne. Które lokalizacja komunikacji równorzędnej wybrać do utworzenia obwód usługi ExpressRoute
Jeśli usługodawca oferuje usługi ExpressRoute w obu lokacjach, możesz pracować z dostawcą i pobranie jednej lokacji do skonfigurowania usługi ExpressRoute. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>Czy można mieć wiele obwody usługi ExpressRoute w tym samym metro Czy można połączyć je z tej samej sieci wirtualnej?

Tak. Może mieć wiele obwody usługi ExpressRoute z tego samego lub innego usługodawcy. Jeśli metro ma wiele lokalizacji komunikacji równorzędnej ExpressRoute i obwodów są tworzone w różnych lokalizacjach komunikacji równorzędnej, można je połączyć do tej samej sieci wirtualnej. Jeśli obwodów są tworzone w tej samej lokalizacji komunikacji równorzędnej, nie możesz połączyć je z tej samej sieci wirtualnej. Każda nazwa lokalizacji w portalu Azure lub interfejsu API programu PowerShell/CLI reprezentuje jedną lokalizację komunikacji równorzędnej. Można na przykład wybierz lokalizacje komunikacji równorzędnej "Singapuru" i "Singapore2" i Połącz z każdej z tą samą siecią virtuan obwodów. 

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Jak połączyć mojej sieci wirtualnych do obwodu usługi ExpressRoute

Przedstawiono podstawowe czynności:

* Ustanów obwodu usługi ExpressRoute i dostawcy usług ją włączyć.
* Użytkownik lub dostawcę, należy skonfigurować komunikacji równorzędnej BGP (s).
* Połącz sieci wirtualnej z obwodem usługi ExpressRoute.

Aby uzyskać więcej informacji, zobacz [przepływy pracy usługi ExpressRoute dla aprowizacji obwodów i stanów obwodów](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Czy istnieją granice łączności dla obwód usługi ExpressRoute?

Tak. [ExpressRoute partnerów i lokalizacje](expressroute-locations.md) artykuł zawiera omówienie granice łączności dla obwodu usługi ExpressRoute. Łączność dla obwodu usługi ExpressRoute jest ograniczone do jednego regionu geograficznymi. Można skalować łączności między regionami geograficznymi przez włączenie funkcji premium usługi ExpressRoute.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Czy można połączyć z więcej niż jedną sieć wirtualną z obwodem usługi ExpressRoute?

Tak. Może mieć maksymalnie 10 połączeń sieci wirtualnych na standardowe obwodu ExpressRoute i maksymalnie 100 [obwodu ExpressRoute premium](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Masz wiele subskrypcji Azure, zawierające sieci wirtualnych. Czy można połączyć sieci wirtualnych, które znajdują się w oddzielnych subskrypcje pojedynczego obwodu ExpressRoute?

Tak. Można autoryzować maksymalnie 10 innych subskrypcji platformy Azure można użyć pojedynczego obwodu usługi expressroute. Można zwiększyć ten limit, należy włączyć funkcję premium usługi ExpressRoute.

Aby uzyskać więcej informacji, zobacz [udostępnianie obwodu usługi ExpressRoute między wieloma subskrypcjami](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Masz wiele subskrypcji Azure, powiązanych z różnych dzierżaw usługi Azure Active Directory lub rejestracji Enterprise Agreement. Czy można połączyć sieci wirtualnych, które znajdują się w oddzielnych dzierżawców i rejestracji do pojedynczego obwodu usługi expressroute nie znajduje się w tej samej dzierżawy lub rejestracji?

Tak. Autoryzacje ExpressRoute mogą znajdować się na granice subskrypcji dzierżawcy i rejestracji z konieczności dodatkowej konfiguracji. 

Aby uzyskać więcej informacji, zobacz [udostępnianie obwodu usługi ExpressRoute między wieloma subskrypcjami](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Sieci wirtualne są podłączone do tego samego obwodu odizolowane od siebie?

Nie. Z punktu widzenia routingu wszystkie sieci wirtualne, połączone z tym samym obwodu ExpressRoute są częścią tej samej domenie routingu i nie są od siebie odizolowane. Izolacja trasy, należy należy utworzyć oddzielny obwód usługi expressroute.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Czy można mieć jedną sieć wirtualną podłączony do więcej niż jeden obwodu ExpressRoute?

Tak. Możesz połączyć pojedynczy sieci wirtualnej z maksymalnie cztery obwody usługi ExpressRoute. Musi zostać określona przez cztery różne [lokalizacje ExpressRoute](expressroute-locations.md).

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Z mojej sieci wirtualne podłączone do obwody usługi ExpressRoute uzyskanie dostępu do Internetu?

Tak. Jeśli ma nie anonsowane trasy domyślne (0.0.0.0/0) lub Internet trasy prefiksy za pomocą sesji protokołu BGP, możesz połączyć się z Internetem z sieci wirtualnej związane z obwodem usługi ExpressRoute.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Czy można zablokować łączności z Internetem do sieci wirtualnych połączony obwody usługi ExpressRoute?

Tak. Możesz anonsują tras domyślnych (0.0.0.0/0), aby zablokować wszystkie łączności z Internetem do maszyn wirtualnych wdrożonych w ramach sieci wirtualnej i rozsyłać cały ruch się za pośrednictwem obwodu usługi expressroute.

Jeśli anonsują tras domyślnych, możemy wymusić ruchu usługi oferowane za pośrednictwem publicznej komunikacji równorzędnej (takie jak usługa Azure storage i bazy danych SQL) Powrót do lokalnej. Konieczne będzie skonfiguruj routery tak, aby przywrócić ruchu Azure za pośrednictwem publicznej komunikacji równorzędnej ścieżki lub za pośrednictwem Internetu. Jeśli włączono punkt końcowy usługi (wersja zapoznawcza) dla usługi, ruch z usługą nie wymusza na lokalnej. Pozostaje ruchu w sieci Azure sieci szkieletowej. Aby dowiedzieć się więcej na temat punktów końcowych usług, zobacz [punktów końcowych usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Sieci wirtualne, połączone z tym samym obwodu ExpressRoute może komunikować między sobą?

Tak. Maszyn wirtualnych wdrożonych w sieci wirtualne podłączone do tego samego obwodu ExpressRoute mogą komunikować się ze sobą.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Dla sieci wirtualnych w połączeniu z ExpressRoute można używać połączenia lokacja lokacja?

Tak. ExpressRoute może współistnieć z sieci VPN typu lokacja lokacja.

### <a name="can-i-move-a-virtual-network-from-site-to-site--point-to-site-configuration-to-use-expressroute"></a>Można przenieść sieć wirtualną z konfigurację site-to-site / punkt lokacja, aby używać usługi ExpressRoute?

Tak. Należy utworzyć bramę usługi ExpressRoute w ramach sieci wirtualnej. Brak małych przestoju, skojarzonych z procesem.

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Dlaczego jest publiczny adres IP skojarzone z bramą usługi ExpressRoute w sieci wirtualnej

Publiczny adres IP jest używana do zarządzania wewnętrzny tylko i nie stanowi zagrożenie bezpieczeństwa, sieci wirtualnych.

### <a name="what-do-i-need-to-connect-to-azure-storage-over-expressroute"></a>Co potrzebne do połączenia z magazynem Azure za pośrednictwem usługi ExpressRoute?

Należy ustanowić obwodu usługi ExpressRoute i skonfigurować tras dla publicznej komunikacji równorzędnej.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Czy istnieją limity liczby I może anonsować tras?

Tak. Możemy zaakceptować maksymalnie 4000 prefiksy tras dla prywatnej komunikacji równorzędnej oraz 200 dla publicznej komunikacji równorzędnej i komunikacji równorzędnej firmy Microsoft. Może to zwiększyć do 10 000 tras dla prywatnej komunikacji równorzędnej po włączeniu funkcji premium usługi ExpressRoute.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Czy istnieją ograniczenia dotyczące zakresów adresów IP I może anonsować sesji protokołu BGP?

Firma Microsoft nie akceptują prefiksy prywatne (RFC1918) w sieci publicznej i Microsoft komunikacji równorzędnej BGP sesji.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>Co się stanie w przypadku I przekracza BGP ogranicza?

Sesje BGP zostanie usunięty. One zostaną zresetowane, gdy liczba prefiks przechodzi poniżej limitu.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Co to jest podczas wstrzymywania BGP usługi ExpressRoute? Czy można je dostosować?

Czas wstrzymania wynosi 180. Komunikaty utrzymywania aktywności są wysyłane co 60 sekund. Te określone ustawienia są po stronie firmy Microsoft, nie można jej zmienić. Istnieje możliwość skonfigurowania czasomierze różnych i będzie odpowiednio negocjowane parametry sesji BGP.

### <a name="after-i-advertise-the-default-route-00000-to-my-virtual-networks-i-cant-activate-windows-running-on-my-azure-vms-how-to-i-fix-this"></a>Po do mojej sieci wirtualnych I anonsowanie trasy domyślnej (0.0.0.0/0), nie można aktywować systemu Windows na moich maszynach wirtualnych platformy Azure. Jak można rozwiązać ten problem?

Poniższe kroki pomocy Azure rozpoznaje żądanie aktywacji:

1. Ustanów publicznej komunikacji równorzędnej dla obwodu usługi ExpressRoute.
2. Wyszukiwania DNS i znaleźć adres IP **kms.core.windows.net**
3. Usługa zarządzania kluczami musi rozpoznać, czy żądanie aktywacji pochodzi z platformy Azure i honoruj żądania. Wykonaj jeden z trzech następujących zadań:

   * W sieci lokalnej należy przekierować ruch kierowany do adresu IP, które zostały uzyskane w kroku 2 do platformy Azure za pośrednictwem publicznej komunikacji równorzędnej.
   * Ma NSP dostawcy włosów — numer pin ruchu do platformy Azure za pośrednictwem publicznej komunikacji równorzędnej.
   * Tworzenie punktów adres IP z Internetu, jako następnego przeskoku trasy zdefiniowane przez użytkownika i zastosować je do adresy podsieci używane, w którym znajdują się te maszyny wirtualne.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Czy można zmienić przepustowość obwodu usługi ExpressRoute?

Tak, możesz spróbować zwiększyć przepustowość obwodu usługi ExpressRoute w portalu Azure lub za pomocą programu PowerShell. Brak dostępnej pojemności na fizyczny port, na którym utworzono obwodu, zmiany zakończy się pomyślnie. 

Jeśli ta zmiana nie powiedzie się, oznacza to, albo nie jest dostępna wystarczająca pojemność na bieżącego portu i należy utworzyć nowy obwód usługi ExpressRoute z większej przepustowości lub że nie ma żadnych dodatkowej pojemności w tej lokalizacji, w którym to przypadku nie będzie można zwiększyć przepustowość. 

Należy również wykonać kolejne czynności z dostawcą połączenia, aby upewnić się, aby zaktualizować limity w ramach ich sieci w celu obsługi zwiększonej przepustowości. Nie można jednak zmniejszyć przepustowość obwodu usługi ExpressRoute. Należy utworzyć nowy obwód usługi ExpressRoute z mniejszej przepustowości sieci i Usuń stare obwodu.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Jak zmienić przepustowość obwodu usługi ExpressRoute?

Można aktualizować przepustowości obwodu ExpressRoute, przy użyciu interfejsu API REST lub polecenia cmdlet programu PowerShell.

## <a name="expressroute-premium"></a>ExpressRoute — wersja premium

### <a name="what-is-expressroute-premium"></a>Co to jest w wersji premium ExpressRoute?

ExpressRoute — wersja premium jest kolekcją następujące funkcje:

* Zwiększenie routingu limit tabeli z 4000 trasy do 10 000 tras dla prywatnej komunikacji równorzędnej.
* Większa liczba sieci wirtualnych, które mogą być połączone z obwodem usługi ExpressRoute (wartość domyślna to 10). Aby uzyskać więcej informacji, zobacz [limity ExpressRoute](#limits) tabeli.
* Łączność z usługą Office 365 i Dynamics 365.
* Globalne łączność za pośrednictwem sieci podstawowej firmy Microsoft. Teraz możesz połączyć sieć wirtualną w jednym regionie geograficznymi z obwodu usługi ExpressRoute w innym regionie.<br>
    **Przykłady:**

    *  Możesz połączyć utworzony w Europie zachodnie z obwodem usługi ExpressRoute utworzone w Dolinie Krzemowej sieci wirtualnej. 
    *  W ramach publicznej komunikacji równorzędnej, prefiksy z innych regionów geograficznymi są rozgłaszane w taki sposób, że można nawiązać, na przykład SQL Azure w Europie zachodnie z obwodem w Dolinie Krzemowej.


### <a name="limits"></a>Jak wiele sieci wirtualnych można połączyć z obwodem usługi ExpressRoute włączenie premium usługi ExpressRoute?

W poniższej tabeli przedstawiono limity ExpressRoute i liczba sieci wirtualnych na obwodu ExpressRoute:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Jak włączyć ExpressRoute premium?

Funkcje premium ExpressRoute można włączyć, gdy ta funkcja jest włączona i może zostać wyłączony przez zaktualizowanie stanu obwodu. Możesz włączyć w czasie tworzenia obwodu ExpressRoute — wersja premium lub można wywołać interfejsu API REST / polecenia cmdlet programu PowerShell.

### <a name="how-do-i-disable-expressroute-premium"></a>Jak wyłączyć ExpressRoute premium?

Możesz wyłączyć ExpressRoute premium przez wywołanie interfejsu API REST lub polecenia cmdlet programu PowerShell. Należy się upewnić, że ma skalowane potrzeb łączności, aby spełnić domyślne limity przed wyłączeniem premium usługi ExpressRoute. Jeśli Twoje użycie skaluje innych niż domyślne limity, żądanie o wyłączenie premium usługi ExpressRoute nie powiedzie się.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Można można wybrać funkcje interesujące z zestawu funkcji premium?

Nie. Nie można wybrać funkcje. Po włączeniu premium usługi ExpressRoute włączyć wszystkie funkcje.

### <a name="how-much-does-expressroute-premium-cost"></a>Jaki jest koszt premium ExpressRoute?

Zapoznaj się [szczegóły cennika](https://azure.microsoft.com/pricing/details/expressroute/) kosztów.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Zwrócić ExpressRoute — wersja Premium, oprócz standardowe opłaty ExpressRoute?

Tak. ExpressRoute premium opłaty na górze obciążenia obwodu ExpressRoute i obciążeń, wymagane przez dostawcę łączności.

## <a name="expressroute-for-office-365-and-dynamics-365"></a>Usługi dla usługi Office 365 i Dynamics 365

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services-and-dynamics-365"></a>Jak utworzyć obwodu usługi ExpressRoute łączenie się z usługami Office 365 i Dynamics 365?

1. Przegląd [strony wymagania wstępne usługi ExpressRoute](expressroute-prerequisites.md) się upewnić, że spełniono wymagania.
2. Aby upewnić się, że spełnione są Twoje potrzeby w zakresie łączności, zapoznaj się z listą dostawców usług i lokalizacje w [ExpressRoute partnerów i lokalizacje](expressroute-locations.md) artykułu.
3. Planowanie wymagań dotyczących pojemności, przeglądając [Planowanie sieci i dostrajania wydajności dla usługi Office 365](http://aka.ms/tune/).
4. Wykonaj wszystkie czynności opisane w przepływach pracy do skonfigurowania łączności [przepływy pracy usługi ExpressRoute dla aprowizacji obwodów i stanów obwodów](expressroute-workflows.md).

> [!IMPORTANT]
> Upewnij się, czy włączono dodatek premium ExpressRoute podczas konfigurowania łączność z usługami Office 365 i Dynamics 365.
> 
> 

### <a name="do-i-need-to-enable-azure-public-peering-to-connect-to-office-365-services-and-dynamics-365"></a>Należy włączyć publicznej komunikacji równorzędnej platformy Azure do nawiązania połączenia usługi Office 365 i Dynamics 365?

Nie, tylko należy włączyć Peering firmy Microsoft. Uwierzytelnianie ruchu do usługi Azure AD jest przesyłany przez Peering firmy Microsoft. 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services-and-dynamics-365"></a>Mój istniejący obwody usługi ExpressRoute, może obsługiwać łączność z usługami Office 365 i Dynamics 365?

Tak. Istniejącym obwodem usługi expressroute, można skonfigurować do obsługi łączności z usługami Office 365. Upewnij się, że masz wystarczająco duże, aby połączyć się z usługami Office 365 i włączono dodatek w warstwie premium. [Planowanie sieci i dostrajania wydajności dla usługi Office 365](http://aka.ms/tune/) musi pomaga zaplanować łączność. Zobacz też [tworzenia i modyfikowania obwodu usługi expressroute](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>Jakie usługi Office 365, usług jest dostępna za pośrednictwem połączenia ExpressRoute?

Zapoznaj się [zakresów adresów IP i URL usługi Office 365](http://aka.ms/o365endpoints) strony aktualną listę usług obsługiwanych przez usługi ExpressRoute.

### <a name="how-much-does-expressroute-for-office-365-services-and-dynamics-365-cost"></a>Ile kosztuje ExpressRoute dla usług Office 365 i Dynamics 365 kosztów?

Usługi Office 365 i Dynamics 365 wymagają włączenia dodatek w warstwie premium. Zobacz [cennikiem szczegóły](https://azure.microsoft.com/pricing/details/expressroute/) kosztów.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>W jakich regionach jest obsługiwana ExpressRoute dla usługi Office 365?

Zobacz [ExpressRoute partnerów i lokalizacje](expressroute-locations.md) informacji.

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Można uzyskać dostęp do usługi Office 365 przez Internet, nawet jeśli ExpressRoute został skonfigurowany dla mojej organizacji?

Tak. Punkty końcowe usługi Office 365 są dostępne za pośrednictwem Internetu, nawet jeśli skonfigurowano usługi ExpressRoute w sieci. Sprawdź, czy z zespołem sieci organizacji skonfigurowanie sieci w danej lokalizacji do nawiązania połączenia usługi Office 365 za pomocą usługi ExpressRoute.

### <a name="how-can-i-plan-for-high-availability-for-office-365-network-traffic-on-azure-expressroute"></a>Można planie wysokiej dostępności dla ruchu sieciowego usługi Office 365 na platformie Azure ExpressRoute?
Zobacz zalecenia dotyczące [wysokiej dostępności i pracy awaryjnej z Azure ExpressRoute](https://aka.ms/erhighavailability)

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Można uzyskać dostęp do usług Office 365 instytucji rządowych Stanów Zjednoczonych społeczności (GCC) za pośrednictwem usługi Azure instytucji rządowych USA obwodu usługi ExpressRoute?

Tak. Punkty końcowe usługi GCC usługi Office 365 są dostępne za pośrednictwem usługi Azure instytucji rządowych USA usługi ExpressRoute. Jednak należy najpierw otworzyć bilet pomocy technicznej w portalu Azure w celu zapewnienia prefiksów, które mają zostać anonsowane do firmy Microsoft. Łączność usługi Office 365 GCC zostanie nawiązane, po usunięciu biletu pomocy technicznej. 

### <a name="can-dynamics-365-for-operations-formerly-known-as-dynamics-ax-online-be-accessed-over-an-expressroute-connection"></a>365 Dynamics dla operacji (wcześniej znane jako Dynamics AX Online) jest dostępna za pośrednictwem połączenia ExpressRoute?

Tak. [Dynamics 365 operacjach](https://www.microsoft.com/dynamics365/operations) jest hostowana na platformie Azure. Można włączyć publicznej komunikacji równorzędnej platformy Azure obwodu ExpressRoute, do nawiązania połączenia.

## <a name="route-filters-for-microsoft-peering"></a>Filtry trasy dla komunikacji równorzędnej firmy Microsoft

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>I am włączenie komunikacji równorzędnej firmy Microsoft po raz pierwszy, jakie trasy zobaczą?

Nie będą widzieć żadnych trasy. Należy dołączyć filtr trasy do obwodu zacząć prefiks anonsów. Aby uzyskać instrukcje, zobacz [Konfigurowanie filtrów trasy dla komunikacji równorzędnej firmy Microsoft](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Po włączeniu komunikacji równorzędnej firmy Microsoft i teraz próbuję wybierz usługi Exchange Online, ale jego nadanie mnie błąd I nie jestem osobą uprawnioną Aby wykonać to zadanie.

Podczas korzystania z filtrów tras, każdy klient można włączyć w komunikacji równorzędnej firmy Microsoft. Jednak służący do konsumowania usługi Office 365, nadal należy pobrać uprawnień przez usługę Office 365.

### <a name="do-i-need-to-get-authorization-for-turning-on-dynamics-365-over-microsoft-peering"></a>Należy uzyskać autoryzacji włączania Dynamics 365 za pośrednictwem komunikacji równorzędnej firmy Microsoft?

Nie, nie trzeba autoryzacji dla usługi Dynamics 365. Można utworzyć regułę, a następnie wybierz społeczności Dynamics 365 bez autoryzacji.

### <a name="i-enabled-microsoft-peering-prior-to-august-1st-2017-how-can-i-take-advantage-of-route-filters"></a>Microsoft równorzędna przed 1 sierpnia 2017 jak możliwość korzystania z filtrów tras I włączone?

Z istniejącym obwodem będzie anonsowania prefiksów dla usługi Office 365 i Dynamics 365. Jeśli chcesz dodać anonsów Azure prefiksów publicznych za pośrednictwem tego samego komunikacji równorzędnej firmy Microsoft, można utworzyć filtr trasy, wybierz usługi, które są potrzebne anonsowane (w tym usług Office 365, które należy i Dynamics 365) i dołączyć filtr do programu Microsoft komunikacji równorzędnej. Aby uzyskać instrukcje, zobacz [Konfigurowanie filtrów trasy dla komunikacji równorzędnej firmy Microsoft](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Mam Microsoft równorzędna w jednej lokalizacji, a teraz próbuję włączyć ją w innej lokalizacji i nie widzę żadnych prefiksów.

* Z obwody usługi ExpressRoute, które zostały skonfigurowane przed 1 sierpnia 2017 komunikacji równorzędnej firmy Microsoft ma wszystkie prefiksy usługi anonsowane przez firmę Microsoft, zaglądanie, nawet jeśli nie zdefiniowano filtrów trasy.

* Z obwody usługi ExpressRoute, które są skonfigurowane na lub po 1 sierpnia 2017 komunikacji równorzędnej firmy Microsoft nie będzie miał wszystkie prefiksy anonsowane do momentu filtr tras jest dołączony do obwodu. Prefiksy nie będą widzieć domyślnie.
