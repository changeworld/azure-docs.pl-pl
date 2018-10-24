---
title: 'Współdziałanie usługi ExpressRoute, sieci VPN typu lokacja lokacja i komunikacja równorzędna sieci wirtualnych — analiza płaszczyzna kontroli: łączności z zapleczem platformy Azure, funkcje współpracy | Dokumentacja firmy Microsoft'
description: Ta strona zawiera analizy płaszczyzna kontroli konfiguracji testu, utworzone w celu analizowania interoperacyjności funkcje usługi ExpressRoute, sieci VPN typu lokacja lokacja i komunikacja równorzędna sieci wirtualnych.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: ee887da18b5666e61bc25365791b2e7dffb925e0
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947275"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---control-plane-analysis"></a>Współdziałanie usługi ExpressRoute, Site-to-site VPN i sieci wirtualnej komunikacji równorzędnej — analiza płaszczyzna kontroli

W tym artykule Omówmy analizy płaszczyzna kontroli konfiguracji testu. Jeśli chcesz zapoznać się z konfiguracji testu, zobacz [konfiguracji testu][Setup]. Aby przejrzeć szczegóły konfiguracji konfiguracji testu, zobacz [konfiguracji ustawień testu][Configuration].

Analiza płaszczyzna kontroli zasadniczo sprawdza, czy trasy wymieniane między sieci w obrębie topologii. Kontrolowanie pomocy analizy płaszczyzny jak różne wyświetlanie topologii sieci.

##<a name="hub-and-spoke-vnet-perspective"></a>Perspektywa Centrum i sieci typu gwiazda

Na poniższym diagramie przedstawiono sieć z piastą i perspektywy sieci typu gwiazda, (wyróżnione na niebiesko). Diagram pokazuje również autonomicznego numer systemu (ASN) inną sieć i tras wymieniane między różnymi sieciami. 

[![1]][1]

Zauważ, że brama usługi ExpressRoute ASN sieć różni się od numeru ASN programu Microsoft routerami granicznymi Enterprise (Msee). Brama usługi ExpressRoute używa prywatnego numeru ASN (65515) i Msee globalnie Użyj publicznego numeru ASN (12076). Podczas konfigurowania komunikacji równorzędnej usługi ExpressRoute, ponieważ MSEE jest element równorzędny, należy użyć 12076 jako element równorzędny numer ASN. Na stronie platformy Azure rozwiązania MSEE ustanawia eBGP komunikacji równorzędnej z bramy usługi ExpressRoute. Podwójna eBGP komunikacji równorzędnej, MSEE ustanawia dla każdego komunikacji równorzędnej usługi ExpressRoute na poziomie warstwy kontroli jest przezroczysty. W związku z tym gdy jest wyświetlany w tabeli tras usługi ExpressRoute, zostanie wyświetlony numer ASN bramy usługi ExpressRoute sieci wirtualnej dla sieci wirtualnej prefiksów. Poniżej przedstawiono zrzut ekranu tabeli tras usługi ExpressRoute próbki: 

[![5]][5]

W ramach platformy Azure numer ASN jest istotne z punktu widzenia komunikacji równorzędnej. Domyślny numer ASN zarówno bramy usługi ExpressRoute, jak i bramy sieci VPN jest 65515.

##<a name="on-premises-location-1-and-remote-vnet-perspective-via-expressroute-1"></a>Perspektywa lokalnej lokalizacji-1 i zdalna sieć wirtualna za pośrednictwem usługi ExpressRoute-1

Lokalnej lokalizacji-1 i zdalna sieć wirtualna są podłączone do sieci wirtualnej koncentratora, za pośrednictwem usługi ExpressRoute 1 i w związku z tym mają ten sam punktu widzenia topologii, jak pokazano na poniższym diagramie.

[![2]][2]

##<a name="on-premises-location-1-and-branch-vnet-perspective-via-site-to-site-vpn"></a>Perspektywa lokalnej lokalizacji-1 i gałęzi w sieci wirtualnej za pośrednictwem sieci VPN typu lokacja-lokacja

W środowisku lokalnym lokalizacji-1 i gałęzi w sieci wirtualnej są podłączone do sieci Centrum bramy sieci VPN za pośrednictwem połączeń sieci VPN typu lokacja-lokacja i w związku z tym mają ten sam punktu widzenia topologii, jak pokazano na poniższym diagramie.

[![3]][3]

##<a name="on-premises-location-2-perspective"></a>Perspektywa lokalnej lokalizacji-2

W środowisku lokalnym lokalizacji-2 jest połączony piastą za pośrednictwem prywatnej komunikacji równorzędnej usługi ExpressRoute 2. 

[![4]][4]

## <a name="further-reading"></a>Dalsze informacje

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Za pomocą połączenia sieci VPN usługi ExpressRoute i lokacja-lokacja w tandem

####  <a name="site-to-site-vpn-over-expressroute"></a>Site-to-Site VPN za pośrednictwem usługi ExpressRoute

Sieci VPN typu lokacja-lokacja można skonfigurować za pośrednictwem komunikacji równorzędnej Microsoft usługi ExpressRoute do prywatnie wymiany danych między siecią lokalną i sieciami wirtualnymi platformy Azure z poufności, powtarzaniu, autentyczności i integralności. Aby uzyskać więcej informacji na temat konfigurowania lokacja-lokacja protokołu IPSec sieci VPN w trybie tunelowania za pośrednictwem komunikacji równorzędnej firmy Microsoft usługi ExpressRoute, zobacz [Site-to-site VPN za pośrednictwem usługi ExpressRoute — komunikacja równorzędna firmy Microsoft][S2S-Over-ExR]. 

Główne ograniczenie konfigurowania sieci VPN S2S za pośrednictwem komunikacji równorzędnej firmy Microsoft jest przepływność. Przepływność za pośrednictwem tunelu IPSec jest ograniczona przez pojemność bramy sieci VPN. Przepływność bramy sieci VPN jest mniejsza w porównaniu do przepustowości usługi ExpressRoute. W takich scenariuszach duże zwiększenie ruchu w bezpiecznym i prywatnej komunikacji równorzędnej dla wszystkich innych ruchu przy użyciu tunelu IPSec może pomóc zoptymalizować wykorzystanie przepustowości usługi ExpressRoute.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Site-to-Site VPN jako bezpieczna ścieżka trybu failover dla usługi ExpressRoute
Usługa ExpressRoute jest oferowana jako para nadmiarowe obwodu, aby zapewnić wysoką dostępność. Magazynu geograficznie nadmiarowego połączenia usługi ExpressRoute można skonfigurować w różnych regionach platformy Azure. Również jako gotowe w naszej konfiguracji testu w danym regionie platformy Azure, aby ścieżka trybu failover dla Twojego połączenia usługi ExpressRoute można zrobić za pomocą Site-to-Site VPN. Gdy te same prefiksy są anonsowane za pośrednictwem usługi ExpressRoute oraz sieci VPN S2S, Azure preferuje usługę ExpressRoute sieci VPN S2S. Aby uniknąć asymetryczne routingu usługi ExpressRoute i połączeń VPN S2S lokalne konfiguracji sieci należy również gospodarzami preferowanie usługi ExpressRoute za pośrednictwem połączenia sieci VPN S2S.

Aby uzyskać więcej informacji na temat konfigurowania współistniejących połączeń usługi ExpressRoute i sieci VPN typu lokacja-lokacja, zobacz [usługi ExpressRoute i współistnienia lokacja-lokacja][ExR-S2S-CoEx].

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>Rozszerzanie łączność sieci wirtualne będące szprychami wewnętrznej bazy danych i biur

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>Łączność z siecią wirtualną za pomocą komunikacji równorzędnej sieci wirtualnych szprych

Gwiazda sieć wirtualna architektura jest powszechnie używana. Centrum jest używana sieć wirtualna (VNet) na platformie Azure, która działa jako centralny punkt łączności między Twoje sieci wirtualne będące szprychami i siecią lokalną. Szprychy są sieciami wirtualnymi równorzędnymi z piastą i mogą być używane do izolowania obciążeń. Ruch przepływa między lokalnym centrum danych a piastą za pośrednictwem połączenia usługi ExpressRoute lub sieci VPN. Aby uzyskać szczegółowe informacje o architekturze, zobacz [Gwiazda — architektura][Hub-n-Spoke]

Wirtualne sieci równorzędne w obrębie regionu umożliwia szprychy sieciach wirtualnych do komunikowania się z sieciami zdalnymi za pomocą bram sieci wirtualnej koncentratora (bramy sieci VPN i ExpressRoute).

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Gałąź połączenie między sieciami przy użyciu sieci VPN typu lokacja-lokacja

Jeśli chcesz, aby gałąź sieciami wirtualnymi (w różnych regionach) i sieciami lokalnymi komunikują się ze sobą za pośrednictwem sieci wirtualnej koncentratora, natywne rozwiązanie na platformie Azure jest połączenie z siecią VPN lokacja lokacja przy użyciu sieci VPN. Jest dostępna Alternatywna opcja na potrzeby routingu w piaście urządzenia WUS.

Do konfigurowania bramy sieci VPN, zobacz [konfigurowania bramy sieci VPN][VPN]. Do wdrażania urządzeń WUS wysokiej dostępności, zobacz [wdrożenia o wysokiej dostępności urządzeń WUS][Deploy-NVA].

## <a name="next-steps"></a>Kolejne kroki

Analiza płaszczyzny danych konfiguracji testu i widokami funkcji monitorowania sieci platformy Azure, zobacz [płaszczyzny danych analizy][Data-Analysis].

Aby dowiedzieć się, jak wiele obwodów usługi ExpressRoute, możesz nawiązać połączenie bramy usługi ExpressRoute lub ile bram usługi ExpressRoute można nawiązać połączenie obwodu usługi ExpressRoute lub Dowiedz się, inne limity skalowania usługi expressroute, zobacz [ExpressRoute — często zadawane pytania][ExR-FAQ]


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "Centrum i szprychy sieci wirtualnej z punktu widzenia topologii"
[2]: ./media/backend-interoperability/Loc1ExRView.png "lokalizacji-1 i zdalnego Perspektywa sieci wirtualnej za pośrednictwem usługi ExpressRoute 1 topologii"
[3]: ./media/backend-interoperability/Loc1VPNView.png "lokalizacji-1 i gałęzi Perspektywa sieci wirtualnej za pośrednictwem S2S VPN topologii"
[4]: ./media/backend-interoperability/Loc2View.png "perspektywy lokalizacji 2 topologii"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "tras usługi ExpressRoute 1"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-config
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering




