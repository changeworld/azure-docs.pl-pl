---
title: Omówienie usług sieciowych platformy Azure
description: Dowiedz się więcej o usługach sieciowych na platformie Azure i ich możliwościach — usługach łączności, usługach ochrony aplikacji, usługach dostarczania aplikacji i monitorowaniu sieci.
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: kumud
ms.openlocfilehash: 90eddea839d2f6ae5235ac6a391b40dd667ab401
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257839"
---
# <a name="azure-networking-services-overview"></a>Omówienie usług sieciowych platformy Azure

Usługi sieciowe na platformie Azure zapewniają wiele funkcji sieciowych, które mogą być używane razem lub oddzielnie. Kliknij dowolną z następujących kluczowych funkcji, aby dowiedzieć się więcej o nich:
- [**Usługi łączności:**](#connect)Łączenie zasobów platformy Azure i zasobów lokalnych przy użyciu dowolnej lub kombinacji tych usług sieciowych na platformie Azure — sieć wirtualna (VNet), wirtualna sieć WAN, usługa ExpressRoute, brama sieci VPN, brama NAT sieci wirtualnej, usługa Azure DNS, usługa komunikacji równorzędnej i usługa Azure Bastion.
- [**Usługi ochrony aplikacji**](#protect) Chroń swoje aplikacje przy użyciu dowolnej lub kombinacji tych usług sieciowych na platformie Azure — łącze prywatne, ochrona przed atakami DDoS, zapora sieciowa, sieciowe grupy zabezpieczeń, zapora aplikacji sieci Web i punkty końcowe sieci wirtualnej.
- [**Usługi dostarczania aplikacji**](#deliver) Dostarczaj aplikacje w sieci platformy Azure przy użyciu dowolnej lub kombinacji tych usług sieciowych na platformie Azure — usługa CDN, usługa Azure Front Door Service, Traffic Manager, brama aplikacji, analizator internetowy i moduł równoważenia obciążenia.
- [**Monitorowanie sieci**](#monitor) — monitorowanie zasobów sieciowych przy użyciu dowolnej lub kombinacji tych usług sieciowych na platformie Azure — Kontrola sieci, monitora usługi ExpressRoute, usługi Azure Monitor lub punktu dostępu terminala sieci wirtualnej (TAP).

## <a name="connectivity-services"></a><a name="connect"></a>Usługi łączności
 
W tej sekcji opisano usługi, które zapewniają łączność między zasobami platformy Azure, łączność z sieci lokalnej do zasobów platformy Azure i połączenia między gałęziami na platformie Azure — sieć wirtualna (sieć wirtualna), wirtualna sieć WAN, usługa ExpressRoute, brama sieci VPN, Brama NAT sieci wirtualnej, usługa Azure DNS, usługa równorzędna platformy Azure i usługa Azure Bastion.

|Usługa|Dlaczego?|Scenariusze|
|---|---|---|
|[Sieć wirtualna](#vnet)|Umożliwia zasoby platformy Azure, aby bezpiecznie komunikować się ze sobą, Internet i sieci lokalne.| <p>[Filtrowanie ruchu sieciowego](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[Routing ruchu sieciowego](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[Ograniczenie dostępu przez sieć do zasobów](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[Łączenie sieci wirtualnych](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[Expressroute](#expressroute)|Rozszerza sieci lokalne do chmury firmy Microsoft za pośrednictwem połączenia prywatnego ułatwione przez dostawcę łączności.|<p>[Tworzenie i modyfikowanie obwodu usługi ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[Tworzenie i modyfikowanie komunikacji równorzędnej dla obwodu usługi ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[Link a VNet to an ExpressRoute circuit (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute)](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[Konfigurowanie filtrów tras dla obwodów usługi ExpressRoute i zarządzanie nimi](../expressroute/how-to-routefilter-portal.md)</p>|
|[Brama sieci VPN](#vpngateway)|Wysyła zaszyfrowany ruch między siecią wirtualną platformy Azure a lokalizacją lokalną za pośrednictwem publicznego Internetu.|<p>[Połączenia lokacja-lokacja](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[Połączenia między sieciami wirtualnymi](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[Połączenia typu "punkt-lokacja"](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[Wirtualna sieć WAN](#virtualwan)|Optymalizuje i automatyzuje łączność z gałęzią do platformy Azure i za pośrednictwem platformy Azure. Regiony platformy Azure służą jako koncentratory, z którymi można połączyć gałęzie.|<p>[Połączenia lokacja-lokacja](../virtual-wan/virtual-wan-site-to-site-portal.md), [połączenia usługi ExpressRoute](../virtual-wan/virtual-wan-expressroute-portal.md)</p>|
|[Azure DNS](#dns)|Hostuje domeny DNS, które zapewniają rozpoznawanie nazw przy użyciu infrastruktury platformy Microsoft Azure.|<p>[Hostowanie własnej domeny w usłudze Azure DNS](../dns/dns-delegate-domain-azure-dns.md)</p><p>[Tworzenie rekordów DNS dla aplikacji sieci Web](../dns/dns-web-sites-custom-domain.md)</p> <p>[Tworzenie rekordu aliasu dla menedżera ruchu](../dns/tutorial-alias-tm.md)</p> <p>[Tworzenie rekordu aliasu dla publicznego adresu IP](../dns/tutorial-alias-pip.md)</p> <p>[Tworzenie rekordu aliasu dla rekordu zasobu strefy](../dns/tutorial-alias-rr.md)</p>|
|[Azure Bastion](#bastion)|Skonfiguruj bezpieczne i bezproblemowe połączenie RDP/SSH z maszynami wirtualnymi bezpośrednio w witrynie Azure Portal za pośrednictwem protokołu SSL. Po nawiązaniu połączenia za pośrednictwem usługi Azure Bastion maszyny wirtualne nie potrzebują publicznego adresu IP|<p>[Tworzenie hosta bastionu platformy Azure](../bastion/bastion-create-host-portal.md)</p><p>[Łączenie się przy użyciu funkcji SSH z maszyną wirtualną z systemem Linux](../bastion/bastion-connect-vm-ssh.md)</p><p>[Łączenie się przy użyciu protokołu RDP z maszyną wirtualną systemu Windows](../bastion/bastion-connect-vm-rdp.md)</p>|
|[Brama NAT sieci wirtualnej](#nat)|Utwórz bramę NAT, aby zapewnić łączność wychodzącą dla maszyny wirtualnej.|<p>[Tworzenie bramy NAT](../virtual-network/quickstart-create-nat-gateway-portal.md)</p>|
|[Usługa równorzędnej platformy Azure (wersja zapoznawcza)](#azurepeeringservice)|Współpracuj z dostawcami usług w celu uzyskania optymalnego i niezawodnego routingu do chmury firmy Microsoft za pośrednictwem sieci publicznej.|<p>[Rejestrowanie usługi równorzędnej platformy Azure](../peering-service/azure-portal.md)</p>|
||||


### <a name="virtual-network"></a><a name="vnet"></a>Sieć wirtualna

Sieć wirtualna platformy Azure (VNet) jest podstawowym elementem konstrukcyjnym sieci prywatnej na platformie Azure. Za pomocą sieci wirtualnych można:
- **Komunikowanie się między zasobami platformy Azure:** Można wdrożyć maszyny wirtualne i kilka innych typów zasobów platformy Azure w sieci wirtualnej, takich jak środowiska usługi Azure App Service, usługa Azure Kubernetes Service (AKS) i zestawy skalowania maszyny wirtualnej platformy Azure. Aby wyświetlić kompletną listę zasobów platformy Azure, które można wdrożyć w sieci wirtualnej, zobacz [Integracja sieci wirtualnej z usługą](../virtual-network/virtual-network-for-azure-services.md).
- **Komunikują się między sobą:** Można połączyć sieci wirtualne ze sobą, umożliwiając zasoby w każdej sieci wirtualnej do komunikowania się ze sobą, za pomocą komunikacji równorzędnej sieci wirtualnej. Łączone sieci wirtualne mogą znajdować się w tym samym regionie lub w różnych regionach świadczenia usługi Azure. Aby uzyskać więcej informacji, zobacz [Komunikacja równorzędna sieci wirtualnej](../virtual-network/virtual-network-peering-overview.md).
- **Komunikowanie się z Internetem:** Wszystkie zasoby w sieci wirtualnej mogą domyślnie komunikować się wychodzące z Internetem. Z zasobem w ruchu przychodzącym możesz komunikować się przez przypisanie publicznego adresu IP lub publicznego modułu równoważenia obciążenia. Do zarządzania połączeniami wychodzącymi można również używać [publicznych adresów IP](../virtual-network/virtual-network-public-ip-address.md) lub publicznego [modułu równoważenia obciążenia.](../load-balancer/load-balancer-overview.md)
- **Komunikowanie się z sieciami lokalnymi:** Komputery i sieci lokalne można połączyć z siecią wirtualną za pomocą [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) lub usługi [ExpressRoute.](../expressroute/expressroute-introduction.md)

Aby uzyskać więcej informacji, zobacz [Co to jest usługa Azure Virtual Network?](../virtual-network/virtual-networks-overview.md).

### <a name="expressroute"></a><a name="expressroute"></a>Expressroute
Usługa ExpressRoute umożliwia rozszerzenie sieci lokalnych do chmury firmy Microsoft za pośrednictwem połączenia prywatnego ułatwione przez dostawcę łączności. To połączenie jest prywatne. Ruch nie przechodzi przez Internet. Dzięki usłudze ExpressRoute można ustanowić połączenia z usługami Microsoft w chmurze, np. Microsoft Azure, Office 365 i Dynamics 365.  Aby uzyskać więcej informacji, zobacz [Co to jest usługa ExpressRoute?](../expressroute/expressroute-introduction.md).

![Azure ExpressRoute](./media/networking-overview/expressroute-connection-overview.png)

### <a name="vpn-gateway"></a><a name="vpngateway"></a>Brama sieci VPN
Brama sieci VPN ułatwia tworzenie szyfrowanych połączeń międzylokacowych z siecią wirtualną z lokalizacji lokalnych lub tworzenie szyfrowanych połączeń między sieciami wirtualnymi. Istnieją różne konfiguracje dostępne dla połączeń bramy sieci VPN, takie jak lokacja lokacja, punkt-lokacja lub sieć wirtualna do sieci wirtualnej.
Na poniższym diagramie przedstawiono wiele połączeń sieci VPN lokacja-lokacja z tą samą siecią wirtualną.

![Połączenia bramy sieci VPN platformy lokacja-lokacja](./media/networking-overview/vpngateway-multisite-connection-diagram.png)

Aby uzyskać więcej informacji na temat różnych typów połączeń sieci VPN, zobacz [Brama sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="virtual-wan"></a><a name="virtualwan"></a>Wirtualna sieć WAN
Wirtualna sieć WAN platformy Azure to usługa sieciowa, która zapewnia zoptymalizowaną i zautomatyzowaną łączność z platformą Azure i za pośrednictwem tej platformy. Regiony platformy Azure służą jako koncentratory, z którymi można połączyć gałęzie. Można korzystać z szkieletu platformy Azure, aby również połączyć gałęzie i cieszyć się łącznością między gałęziami a siecią wirtualną. Wirtualna sieć WAN platformy Azure łączy wiele usług łączności w chmurze platformy Azure, takich jak sieć VPN typu lokacja-lokacja, usługa ExpressRoute, sieć VPN użytkowników typu "punkt-lokacja" w jeden interfejs operacyjny. Łączność z sieciami wirtualnymi platformy Azure jest nawiązywała przy użyciu połączeń sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Co to jest wirtualna sieć WAN platformy Azure?](../virtual-wan/virtual-wan-about.md).

![Diagram usługi Virtual WAN](./media/networking-overview/virtualwan1.png)

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
Azure DNS to usługa hostingowa przeznaczona dla domen DNS, która umożliwia rozpoznawanie nazw przy użyciu infrastruktury platformy Microsoft Azure. Dzięki hostowaniu swoich domen na platformie Azure możesz zarządzać rekordami DNS z zastosowaniem tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co w przypadku innych usług platformy Azure. Aby uzyskać więcej informacji, zobacz [Co to jest usługa Azure DNS?](../dns/dns-overview.md).

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
Usługa Azure Bastion to nowa w pełni zarządzana przez platformę usługa PaaS, którą aprowizujesz w sieci wirtualnej. Zapewnia bezpieczną i bezproblemową łączność RDP/SSH z maszynami wirtualnymi bezpośrednio w witrynie Azure portal za pomocą protokołu SSL. Gdy łączysz się za pomocą usługi Azure Bastion, maszyny wirtualne nie muszą mieć publicznego adresu IP. Aby uzyskać więcej informacji, zobacz [Co to jest Bastion platformy Azure?](../bastion/bastion-overview.md).

![Architektura Bastionu platformy Azure](./media/networking-overview/architecture.png)

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>Brama NAT sieci wirtualnej
Virtual Network NAT (translacja adresów sieciowych) upraszcza połączenie internetowe tylko wychodzące dla sieci wirtualnych. Po skonfigurowaniu w podsieci wszystkie połączenia wychodzące używają określonych statycznych publicznych adresów IP. Łączność wychodząca jest możliwa bez modułu równoważenia obciążenia lub publicznych adresów IP bezpośrednio podłączonych do maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz [Co to jest brama NAT sieci wirtualnej?](../virtual-network/nat-overview.md) 

![Brama NAT sieci wirtualnej](./media/networking-overview/flow-map.png)

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a>Usługa równorzędnej platformy Azure
Usługa Azure Peering zwiększa łączność z klientami z usługami w chmurze firmy Microsoft, takimi jak Office 365, Dynamics 365, usługi oprogramowania jako usługi (SaaS), platforma Azure lub dowolne usługi firmy Microsoft dostępne za pośrednictwem publicznego Internetu. Aby uzyskać więcej informacji, zobacz [Co to jest usługa równorzędnej platformy Azure?](../peering-service/about.md).

## <a name="application-protection-services"></a><a name="protect"></a>Usługi ochrony aplikacji

W tej sekcji opisano usługi sieciowe na platformie Azure, które pomagają chronić zasoby sieciowe — ochrona aplikacji przy użyciu dowolnej lub kombinacji tych usług sieciowych na platformie Azure — łącze prywatne, ochrona przed atakami DDoS, zapora sieciowa, grupy zabezpieczeń sieci, sieć Web Zapora aplikacji i punkty końcowe sieci wirtualnej.

|Usługa|Dlaczego?|Scenariusz|
|---|---|---|
|[Ochrona przed atakami DDOS](#ddosprotection) |Wysoka dostępność aplikacji z ochroną przed nadmiernymi opłatami za ruch IP|[Zarządzanie ochroną DDoS platformy Azure](../virtual-network/manage-ddos-protection.md)|
|[Zapora aplikacji sieci Web](#waf)|<p>[Usługa Azure WAF z bramą aplikacji](../web-application-firewall/ag/ag-overview.md) zapewnia regionalną ochronę jednostkom w przestrzeni adresowej publicznej i prywatnej</p><p>[Usługa Azure WAF z drzwiami frontowymi](../web-application-firewall/afds/afds-overview.md) zapewnia ochronę na krawędzi sieci do publicznych punktów końcowych.</p>|<p>[Konfigurowanie reguł ochrony botów](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[Konfigurowanie niestandardowego kodu odpowiedzi](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[Konfigurowanie reguł ograniczeń ip](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[Konfigurowanie reguły limitu szybkości](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Azure Firewall](#firewall)|Azure Firewall to zarządzana, sieciowa usługa zabezpieczeń oparta na chmurze, która zabezpiecza zasoby usługi Azure Virtual Network. Jest to w pełni stanowa zapora jako usługa z wbudowaną wysoką dostępnością i możliwością nieograniczonego skalowania w chmurze.|<p>[Wdrażanie zapory platformy Azure w sieci wirtualnej](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[- Wdrażanie zapory platformy Azure w sieci hybrydowej](../firewall/tutorial-hybrid-ps.md)</p> <p>[Filtrowanie ruchu przychodzącego za pomocą usługi Azure Firewall DNAT](../firewall/tutorial-firewall-dnat.md)</p>|
|[Grupy zabezpieczeń sieci](#nsg)|Pełna szczegółowa rozproszona kontrola węzła końcowego na maszynie Wirtualnej/podsieci dla wszystkich przepływów ruchu sieciowego|[Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)|
|[Punkty końcowe usługi dla sieci wirtualnej](#serviceendpoints)|Umożliwia ograniczenie dostępu sieciowego do niektórych zasobów usługi platformy Azure do podsieci sieci wirtualnej|[Ograniczanie dostępu sieciowego do zasobów PaaS](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md)|
[Link prywatny](#privatelink)|Umożliwia dostęp do usług PaaS platformy Azure (na przykład usługi Azure Storage i bazy danych SQL) oraz usługi azure hostowane przez klienta/partnera za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej.|<p>[Tworzenie prywatnego punktu końcowego](../private-link/create-private-endpoint-portal.md)</p><p>[Tworzenie usługi łącza prywatnego](../private-link/create-private-link-service-portal.md)</p>|
|||
### <a name="ddos-protection"></a><a name="ddosprotection"></a>Ochrona przed atakami DDoS 
[Usługa Azure DDoS Protection](../virtual-network/manage-ddos-protection.md) zapewnia środki zaradcze przeciwko najbardziej zaawansowanym zagrożeniom DDoS. Usługa zapewnia rozszerzone możliwości ograniczania ddos dla aplikacji i zasobów wdrożonych w sieciach wirtualnych. Ponadto klienci korzystający z usługi Azure DDoS Protection mają dostęp do pomocy technicznej ddos rapid response, aby zaangażować ekspertów DDoS podczas aktywnego ataku.

![DDoS Protection](./media/networking-overview/ddos-protection.png)

### <a name="web-application-firewall"></a><a name="waf"></a>Zapora aplikacji sieci Web

[Zapora aplikacji sieci Web platformy Azure](../web-application-firewall/overview.md) (WAF) zapewnia ochronę aplikacji sieci web przed typowymi exploitami sieci Web i lukami w zabezpieczeniach, takimi jak iniekcja SQL i skrypty między witrynami. Usługa Azure WAF zapewnia ochronę po wyjęciu z pudełka przed lukami w zabezpieczeniach owasp top 10 za pośrednictwem reguł zarządzanych. Ponadto klienci mogą również skonfigurować reguły niestandardowe, które są regułami zarządzanymi przez klienta, aby zapewnić dodatkową ochronę na podstawie źródłowego zakresu IP i żądać atrybutów, takich jak nagłówki, pliki cookie, pola danych formularza lub parametry ciągu zapytania.

Klienci mogą wdrożyć [usługę Azure WAF za pomocą bramy aplikacji,](../application-gateway/waf-overview.md) która zapewnia regionalną ochronę jednostkom w przestrzeni adresowej publicznej i prywatnej. Klienci mogą również wdrożyć [usługę Azure WAF za pomocą drzwi ekwiomuj,](../frontdoor/waf-overview.md) który zapewnia ochronę w sieci edge do publicznych punktów końcowych.

![Zapora aplikacji internetowej](./media/networking-overview/waf-overview.png)


### <a name="azure-firewall"></a><a name="firewall"></a>Azure Firewall
Azure Firewall to zarządzana, sieciowa usługa zabezpieczeń oparta na chmurze, która zabezpiecza zasoby usługi Azure Virtual Network. Korzystając z Zapory platformy Azure, można centralnie tworzyć, wymuszać i rejestrować zasady łączności aplikacji i sieci w ramach subskrypcji i sieci wirtualnych. Usługa Azure Firewall korzysta ze statycznego publicznego adresu IP dla zasobów sieci wirtualnej, co umożliwia zewnętrznym zaporom identyfikowanie ruchu pochodzącego z sieci wirtualnej. 

Aby uzyskać więcej informacji na temat Zapory platformy Azure, zobacz [dokumentację Zapory platformy Azure](../firewall/overview.md).

![Omówienie zapory](./media/networking-overview/firewall-threat.png)

### <a name="network-security-groups"></a><a name="nsg"></a>Grupy zabezpieczeń sieci
Ruch sieciowy przychodzący do zasobów platformy Azure i wychodzący z nich w sieci wirtualnej platformy Azure można filtrować za pomocą grupy zabezpieczeń sieci. Aby uzyskać więcej informacji, zobacz [Omówienie zabezpieczeń](../virtual-network/security-overview.md).

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>Punkty końcowe usługi
Punkty końcowe usługi sieci wirtualnej rozszerzają prywatną przestrzeń adresową i tożsamość sieci wirtualnej do usług platformy Azure za pośrednictwem bezpośredniego połączenia. Punkty końcowe umożliwiają zabezpieczanie krytycznych zasobów usługi platformy Azure tylko do sieci wirtualnych. Ruch z sieci wirtualnej do usługi platformy Azure zawsze pozostaje w sieci szkieletowej platformy Microsoft Azure. Aby uzyskać więcej informacji, zobacz [Punkty końcowe usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md).

![Punkty końcowe usługi dla sieci wirtualnej](./media/networking-overview/vnet-service-endpoints-overview.png)

### <a name="azure-private-link"></a><a name="privatelink"></a>Link prywatny platformy Azure
[Usługa Azure Private Link](../private-link/private-link-overview.md) umożliwia dostęp do usług PaaS platformy Azure (na przykład usługi Azure Storage i bazy danych SQL) oraz usługi azure hostowane przez klienta/partnera za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej.
Ruch między siecią wirtualną a usługą odbywa się w sieci szkieletowej firmy Microsoft. Udostępnianie usługi w publicznym internecie nie jest już konieczne. Możesz utworzyć własną usługę linków prywatnych w sieci wirtualnej i dostarczyć ją klientom.

![Omówienie prywatnego punktu końcowego](./media/networking-overview/private-endpoint.png)


## <a name="application-delivery-services"></a><a name="deliver"></a>Usługi dostarczania aplikacji

W tej sekcji opisano usługi sieciowe na platformie Azure, które pomagają w dostarczaniu aplikacji — Kontrola sieci, monitor usługi ExpressRoute, monitor platformy Azure lub punkt dostępu terminala sieci wirtualnej (TAP).

|Usługa|Dlaczego?|Scenariusz|
|---|---|---|
|[Content Delivery Network](#cdn)|Dostarcza użytkownikom treści o dużej przepustowości. Sieci CDN przechowują zawartość w pamięci podręcznej na serwerach brzegowych w lokalizacjach punktu obecności (POP), które znajdują się w pobliżu użytkowników końcowych, aby zminimalizować opóźnienia|<p>[Dodawanie sieci CDN do aplikacji sieci Web](../cdn/cdn-add-to-web-app.md)</p> <p>[- Dostęp do obiektów blob magazynu przy użyciu domeny niestandardowej usługi Azure CDN za pośrednictwem protokołu HTTPS](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[Dodawanie domeny niestandardowej do punktu końcowego usługi Azure CDN](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[konfigurowanie protokołu HTTPS w domenie niestandardowej usługi Azure CDN](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate)</p>|
|[Azure Front Door Service](#frontdoor)|Umożliwia definiowanie, zarządzanie i monitorowanie routingu globalnego dla ruchu internetowego przez optymalizację pod kątem najlepszej wydajności i natychmiastowej globalnej pracy awaryjnej w celu zapewnienia wysokiej dostępności.|<p>[Dodawanie domeny niestandardowej do usługi Azure Front Door Service](../frontdoor/front-door-custom-domain.md)</p> <p>[konfigurowanie protokołu HTTPS w niestandardowej domenie usługi Front Door](../frontdoor/front-door-custom-domain-https.md)</p><p>[Konfigurowanie zasad Zapory aplikacji z filtrem geograficznym](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[Menedżer ruchu](#trafficmanager)|Dystrybuuje ruch oparty na systemie DNS do usług w globalnych regionach platformy Azure, zapewniając jednocześnie wysoką dostępność i szybkość reakcji|<p> [Kierowanie ruchem w celu uzyskania niskich opóźnień](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[Kierowanie ruchu do priorytetowego punktu końcowego](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [Kontrolowanie ruchu za pomocą punktów końcowych z wagami](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[Ruch trasy na podstawie położenia geograficznego punktu końcowego](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [Kierowanie ruchem na podstawie podsieci użytkownika](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[Moduł równoważenia obciążenia](#loadbalancer)|Zapewnia regionalne równoważenie obciążenia przez routing ruchu między strefami dostępności i do sieci wirtualnych. Zapewnia wewnętrzne równoważenie obciążenia przez routing ruchu między zasobami i między zasobami do tworzenia aplikacji regionalnej.|<p> [Równoważenie obciążenia ruchem internetowym przez skierowanie go do maszyn wirtualnych](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[Ruch równoważenia obciążenia na maszynach wirtualnych w sieci wirtualnej](../load-balancer/tutorial-load-balancer-basic-internal-portal.md)<p>[Port forward traffic do określonego portu na określonych maszynach wirtualnych](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [Konfigurowanie reguł równoważenia obciążenia i ruchu wychodzącego](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[Brama aplikacji](#applicationgateway)|Azure Application Gateway to moduł równoważenia obciążenia ruchu internetowego, który umożliwia zarządzanie ruchem kierowanym do aplikacji internetowych.|<p>[Bezpośredni ruch internetowy w usłudze Azure Application Gateway](../application-gateway/quick-create-portal.md)</p><p>[Konfigurowanie bramy aplikacji z kończeniem żądań protokołu](../application-gateway/create-ssl-portal.md)</p><p>[Tworzenie bramy aplikacji z przekierowywaniem na podstawie ścieżki URL](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="content-delivery-network"></a><a name="cdn"></a>Content Delivery Network
Usługa Azure Content Delivery Network (CDN) oferuje deweloperom globalne rozwiązanie umożliwiające szybkie dostarczanie użytkownikom zawartości wymagającej wysokiej przepustowości przez zapisywanie zawartości w pamięci podręcznej w węzłach fizycznych strategicznie rozmieszczonych na całym świecie. Aby uzyskać więcej informacji na temat usługi Azure CDN, zobacz [Sieć dostarczania zawartości platformy Azure](../cdn/cdn-overview.md)

![Usługa Azure CDN](./media/networking-overview/cdn-overview.png)

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Usługa Azure Front Door
Usługa Azure Front Door Service umożliwia definiowanie i monitorowanie globalnego routingu ruchu internetowego oraz zarządzanie nim przez optymalizowanie pod kątem uzyskiwania najlepszej wydajności i natychmiastowego przechodzenia w tryb failover w celu zapewnienia wysokiej dostępności. Dzięki usłudze Front Door można przekształcić globalne usługi dla konsumentów (wiele regionów) i aplikacji w niezawodne, spersonalizowane, nowoczesne, wysokowydajne aplikacje, interfejsy API i zawartość, które dotrą do odbiorców globalnych za pomocą platformy Azure. Aby uzyskać więcej informacji, zobacz [Drzwi frontowe platformy Azure](../frontdoor/front-door-overview.md).


### <a name="traffic-manager"></a><a name="trafficmanager"></a>Menedżer ruchu

Usługa Azure Traffic Manager to oparty na systemie DNS moduł równoważenia obciążenia ruchu, który umożliwia optymalną dystrybucję ruchu do usług w wielu regionach platformy Azure na świecie, przy jednoczesnym zapewnieniu wysokiej dostępności i krótkiego czasu odpowiedzi. Usługa Traffic Manager udostępnia szereg metod routingu ruchu w celu dystrybucji ruchu, takich jak priorytet, ważona, wydajność, geograficzna, wielowartościowa lub podsieć. Aby uzyskać więcej informacji na temat metod routingu ruchu, zobacz [Metody routingu usługi Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

Na poniższym diagramie przedstawiono routing oparty na priorytecie punktu końcowego z usługą Traffic Manager:

![Metoda routingu ruchu usługi Azure Traffic Manager](./media/networking-overview/priority.png)

Aby uzyskać więcej informacji na temat usługi Traffic Manager, zobacz [Co to jest usługa Azure Traffic Manager?](../traffic-manager/traffic-manager-overview.md)

### <a name="load-balancer"></a><a name="loadbalancer"></a>Moduł równoważenia obciążenia
Moduł równoważenia obciążenia usługi Azure zapewnia równoważenie obciążenia warstwy 4 o wysokiej wydajności i małych opóźnieniach dla wszystkich protokołów UDP i TCP. Zarządza połączeniami przychodzącymi i wychodzącymi. Można skonfigurować publiczne i wewnętrzne punkty końcowe z równoważeniem obciążenia. Reguły mapowania połączeń przychodzących do miejsc docelowych puli zaplecza można zdefiniować za pomocą opcji sondowania kondycji protokołu TCP i HTTP w celu zarządzania dostępnością usług. Aby dowiedzieć się więcej o modułze równoważenia obciążenia, przeczytaj [artykuł omówienie modułu równoważenia obciążenia.](../load-balancer/load-balancer-overview.md)

Na poniższej ilustracji przedstawiono wielowarstwową aplikację z dostępem do Internetu, która wykorzystuje zarówno zewnętrzne, jak i wewnętrzne moduły równoważenia obciążenia:

![Przykład równoważenia obciążenia platformy Azure](./media/networking-overview/load-balancer.png)


### <a name="application-gateway"></a><a name="applicationgateway"></a>Brama aplikacji
Azure Application Gateway to moduł równoważenia obciążenia ruchu internetowego, który umożliwia zarządzanie ruchem kierowanym do aplikacji internetowych. Jest to kontroler dostarczania aplikacji (ADC) jako usługa, oferująca różne możliwości równoważenia obciążenia warstwy 7 dla aplikacji. Aby uzyskać więcej informacji, zobacz [Co to jest brama aplikacji platformy Azure?](../application-gateway/overview.md).

Na poniższym diagramie przedstawiono routing oparty na ścieżce adresów URL z bramą aplikacji.

![Przykład bramy aplikacji](./media/networking-overview/figure1-720.png)

## <a name="network-monitoring-services"></a><a name="monitor"></a>Usługi monitorowania sieci
W tej sekcji opisano usługi sieciowe na platformie Azure, które pomagają monitorować zasoby sieciowe — Obserwator sieci, Monitor usługi ExpressRoute, Monitor Platformy Azure i TAP sieci wirtualnej.

|Usługa|Dlaczego?|Scenariusz|
|---|---|---|
|[Network Watcher](#networkwatcher)|Pomaga monitorować i rozwiązywać problemy z łącznością, pomaga diagnozować problemy z siecią VPN, sieciową siecią ręczną i routingiem, przechwytywać pakiety na maszynie Wirtualnej, automatyzować wyzwalanie narzędzi diagnostycznych przy użyciu funkcji Azure Functions i aplikacji logiki|<p>[Diagnozowanie problemu z filtrowaniem ruchu na maszynie wirtualnej](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[Diagnozowanie problemu z routingiem maszyny wirtualnej](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[Monitorowanie komunikacji między maszynami wirtualnymi](../network-watcher/connection-monitor.md)</p><p>[Diagnozowanie problemów z komunikacją między sieciami](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[Rejestrowanie ruchu sieciowego do i z maszyny wirtualnej](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[ExpressRoute Monitor](#expressroutemonitor)|Zapewnia monitorowanie wydajności, dostępności i wykorzystania sieci w czasie rzeczywistym, pomaga w automatycznym odnajdowaniu topologii sieci, zapewnia szybszą izolację błędów, wykrywa przejściowe problemy z siecią, pomaga analizować historyczną wydajność sieci charakterystyki, obsługuje wieloase abonamen|<p>[Konfigurowanie monitora wydajności sieci dla usługi ExpressRoute](../expressroute/how-to-npm.md)</p><p>[Monitorowanie, metryki i alerty usługi ExpressRoute](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Azure Monitor](#azuremonitor)|Pomaga zrozumieć, jak aplikacje działają i proaktywnie identyfikuje problemy wpływające na nie i zasoby, od których zależą.|<p>[Dane i alerty menedżera ruchu](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[Diagnostyka monitora platformy Azure dla standardowego modułu równoważenia obciążenia](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[Monitorowanie dzienników i metryk usługi Azure Firewall](../firewall/tutorial-diagnostics.md)</p><p>[Monitorowanie i rejestrowanie zapory aplikacji internetowej platformy Azure](../frontdoor/waf-front-door-monitor.md)</p>|
|[Tap sieci wirtualnej](#vnettap)|Zapewnia ciągłe przesyłanie strumieniowe ruchu sieciowego maszyny wirtualnej do modułu zbierającego pakiety, umożliwia rozwiązania do zarządzania wydajnością sieci i aplikacji oraz narzędzia do analizy zabezpieczeń|[Tworzenie zasobu TAP sieci wirtualnej](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="network-watcher"></a><a name="networkwatcher"></a>Network Watcher
Azure Network Watcher to usługa, która udostępnia narzędzia umożliwiające monitorowanie, diagnozowanie, wyświetlanie metryk i włączanie lub wyłączanie dzienników zasobów w sieci wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [Co to jest Obserwator sieci?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>ExpressRoute Monitor
Aby dowiedzieć się, jak wyświetlać metryki obwodów usługi ExpressRoute, dzienniki diagnostyczne i alerty, zobacz [Monitorowanie usługi ExpressRoute, metryki i alerty](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Monitor
Usługa Azure Monitor maksymalizuje dostępność i wydajność aplikacji zapewniając kompleksowe rozwiązanie do zbierania i analizowania danych telemetrycznych ze środowisk chmurowych i lokalnych oraz podejmowania działań w oparciu o nie. Pomaga interpretować działanie aplikacji i proaktywnie identyfikuje problemy dotyczące aplikacji i zasobów, od których zależą. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="virtual-network-tap"></a><a name="vnettap"></a>Tap sieci wirtualnej
Usługa Azure virtual network TAP (Terminal Access Point) umożliwia ciągłe przesyłanie strumieniowe ruchu sieciowego maszyny wirtualnej do modułu zbierającego pakiety sieciowego lub narzędzia analitycznego. Narzędzie do tworzenia lub analizy jest dostarczane przez partnera [urządzenia wirtualnego sieci.](https://azure.microsoft.com/solutions/network-appliances/) 

Na poniższej ilustracji przedstawiono działanie tap sieci wirtualnej. 

![Jak działa tap sieci wirtualnej](./media/networking-overview/virtual-network-tap-architecture.png)

Aby uzyskać więcej informacji, zobacz [Co to jest TAP sieci wirtualnej](../virtual-network/virtual-network-tap-overview.md).

## <a name="next-steps"></a>Następne kroki

- Utwórz pierwszą sieć wirtualną i połącz z nią kilka maszyn wirtualnych, wykonując kroki opisane w artykule [Tworzenie pierwszej sieci wirtualnej.](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
- Podłącz komputer do sieci wirtualnej, wykonując czynności opisane w [artykule Konfigurowanie połączenia typu "punkt-lokacja".](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
- Równoważenie obciążenia ruchu internetowego do serwerów publicznych, wykonując kroki opisane w artykule [Tworzenie modułu równoważenia obciążenia skierowanego do Internetu.](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
 
 
   
