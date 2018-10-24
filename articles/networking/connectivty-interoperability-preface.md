---
title: 'Współdziałanie usługi ExpressRoute, sieci VPN typu lokacja lokacja i komunikacja równorzędna sieci wirtualnych — Testuj ustawienia: łączności z zapleczem platformy Azure, funkcje współpracy | Dokumentacja firmy Microsoft'
description: Ta strona zawiera konfiguracji testu, który służy do analizowania współdziałanie funkcje usługi ExpressRoute, sieci VPN typu lokacja lokacja i komunikacja równorzędna sieci wirtualnych.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: e859a0a3ac35a9d9f2dab579b7609192e599f90f
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947271"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---test-setup"></a>Współdziałanie usługi ExpressRoute, Site-to-site VPN i sieci wirtualnej komunikacji równorzędnej - konfiguracji testu
W tym artykule możemy zidentyfikować konfiguracji testu, który możemy użyć do analizowania, jak różne funkcje transfer działają ze sobą zarówno na poziomie warstwy kontroli i płaszczyzny danych. Przed omówieniem konfiguracji testu, krótko sprawdźmy, co oznaczają te różne funkcje sieciowe platformy Azure.

ExpressRoute: Za pomocą usługi ExpressRoute prywatnej komunikacji równorzędnej można mogą łączyć się bezpośrednio prywatnych przestrzeni adresów IP w sieci lokalnej do wdrożenia sieci wirtualnej platformy Azure.  Przy użyciu usługi ExpressRoute można osiągnąć wyższą przepustowość i połączenie prywatne. Ma wielu partnerów ekonomicznym usługi ExpressRoute, którzy oferują łączność usługi ExpressRoute w ramach umowy SLA. Aby dowiedzieć się więcej na temat usługi ExpressRoute i sposobu ich konfigurowania, zobacz [wprowadzenie do usługi ExpressRoute][ExpressRoute]

Sieć VPN lokacja-lokacja: Bezpiecznie łączyć z siecią lokalną na platformę Azure za pośrednictwem Internetu lub za pośrednictwem usługi ExpressRoute, sieci VPN typu lokacja-lokacja (S2S) opcja jest dostępna. Aby dowiedzieć się więcej o sposobie konfigurowania sieci VPN S2S dla łączenie z platformą Azure, zobacz [konfigurowania bramy sieci VPN][VPN]

Komunikacja równorzędna sieci wirtualnej: Komunikacja równorzędna sieci wirtualnych jest dostępna do ustanowienia połączenia między sieciami wirtualnymi (Vnet). Aby dowiedzieć się więcej na temat komunikacji równorzędnej sieci wirtualnych, zobacz [samouczek dotyczący wirtualnych sieci równorzędnych][VNet].

##<a name="test-setup"></a>Ustawienia testu

Poniższy diagram przedstawia konfiguracji testu.

[![1]][1]

Element center konfiguracji testu jest piastą 1 Region platformy Azure. Centrum sieci wirtualnej jest podłączony do różnych sieci:

1.  Do typu gwiazda sieci wirtualnych przy użyciu komunikacji równorzędnej sieci wirtualnych. Sieć wirtualną będącą szprychą ma zdalny dostęp do obu bram w sieci wirtualnej koncentratora.
2.  Do sieci wirtualnej gałęzi za pośrednictwem sieci VPN typu lokacja lokacja. Połączenie używa eBGP w celu wymiany tras.
3.  Do 1 lokalizację sieci lokalnej za pośrednictwem prywatnej komunikacji równorzędnej usługi ExpressRoute jako ścieżki podstawowej i połączenia sieci VPN typu lokacja-lokacja jako ścieżki kopii zapasowej. W pozostałej części tego dokumentu będziemy nazywać ten obwód usługi ExpressRoute jako ExpressRoute1. Domyślnie obwody usługi ExpressRoute zapewniają łączność nadmiarowa w celu zapewnienia wysokiej dostępności. W ExpressRoute1 interfejsu routera CE dodatkowej podrzędnego połączonego z dodatkowego rozwiązania MSEE jest wyłączone. To jest sygnalizowany czerwona linia za pośrednictwem strzałki podwójnej linii na powyższym diagramie.
4.  Do 2 lokalizację sieci lokalnej za pośrednictwem innego prywatnej komunikacji równorzędnej usługi ExpressRoute. W pozostałej części tego dokumentu będziemy nazywać ten drugi obwód usługi ExpressRoute jako ExpressRoute2.
5.  ExpressRoute1 łączy się również z piastą i lokalizacja 1 w środowisku lokalnym do zdalnej sieci wirtualnej w wersji 2 Region platformy Azure.

## <a name="further-reading"></a>Dalsze informacje

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Za pomocą połączenia sieci VPN usługi ExpressRoute i lokacja-lokacja w tandem

#### <a name="site-to-site-vpn-over-expressroute"></a>Site-to-Site VPN za pośrednictwem usługi ExpressRoute 

Sieci VPN typu lokacja-lokacja można skonfigurować za pośrednictwem komunikacji równorzędnej Microsoft usługi ExpressRoute do prywatnie wymiany danych między siecią lokalną i sieciami wirtualnymi platformy Azure z poufności, powtarzaniu, autentyczności i integralności. Aby uzyskać więcej informacji na temat sposobu konfigurowania lokacja-lokacja protokołu IPSec sieci VPN w trybie tunelowania za pośrednictwem komunikacji równorzędnej firmy Microsoft usługi ExpressRoute, zobacz [Site-to-site VPN za pośrednictwem usługi ExpressRoute — komunikacja równorzędna firmy Microsoft][S2S-Over-ExR]. 

Główne ograniczenie konfigurowania sieci VPN S2S za pośrednictwem komunikacji równorzędnej firmy Microsoft jest przepływność. Przepływność za pośrednictwem tunelu IPSec jest ograniczona przez pojemność bramy sieci VPN. Przepływność bramy sieci VPN jest mniejsza w porównaniu do przepustowości usługi ExpressRoute. W takich scenariuszach duże zwiększenie ruchu w bezpiecznym i prywatnej komunikacji równorzędnej dla wszystkich innych ruchu przy użyciu tunelu IPSec może pomóc zoptymalizować wykorzystanie przepustowości usługi ExpressRoute.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Site-to-Site VPN jako bezpieczna ścieżka trybu failover dla usługi ExpressRoute
Usługa ExpressRoute jest oferowana jako para nadmiarowe obwodu, aby zapewnić wysoką dostępność. Magazynu geograficznie nadmiarowego połączenia usługi ExpressRoute można skonfigurować w różnych regionach platformy Azure. Również jako gotowe w naszej konfiguracji testu w danym regionie platformy Azure, aby ścieżka trybu failover dla Twojego połączenia usługi ExpressRoute można zrobić za pomocą Site-to-Site VPN. Gdy te same prefiksy są anonsowane za pośrednictwem usługi ExpressRoute oraz sieci VPN S2S, Azure preferuje usługę ExpressRoute sieci VPN S2S. Aby uniknąć asymetryczne routingu usługi ExpressRoute i połączeń VPN S2S lokalne konfiguracji sieci należy również gospodarzami preferowanie usługi ExpressRoute za pośrednictwem połączenia sieci VPN S2S.

Aby uzyskać więcej informacji na temat sposobu konfigurowania współistniejących połączeń usługi ExpressRoute i sieci VPN typu lokacja-lokacja, zobacz [usługi ExpressRoute i współistnienia lokacja-lokacja][ExR-S2S-CoEx].

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>Rozszerzanie łączność sieci wirtualne będące szprychami wewnętrznej bazy danych i biur

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>Łączność z siecią wirtualną za pomocą komunikacji równorzędnej sieci wirtualnych szprych

Gwiazda sieć wirtualna architektura jest powszechnie używana. Centrum jest używana sieć wirtualna (VNet) na platformie Azure, która działa jako centralny punkt łączności między Twoje sieci wirtualne będące szprychami i siecią lokalną. Szprychy są sieciami wirtualnymi równorzędnymi z piastą i mogą być używane do izolowania obciążeń. Ruch przepływa między lokalnym centrum danych a piastą za pośrednictwem połączenia usługi ExpressRoute lub sieci VPN. Aby uzyskać więcej informacji na temat architektury, zobacz [Gwiazda — architektura][Hub-n-Spoke]

Wirtualne sieci równorzędne w obrębie regionu umożliwia szprychy sieciach wirtualnych do komunikowania się z sieciami zdalnymi za pomocą bram sieci wirtualnej koncentratora (bramy sieci VPN i ExpressRoute).

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Gałąź połączenie między sieciami przy użyciu sieci VPN typu lokacja-lokacja

Jeśli chcesz, aby gałąź sieciami wirtualnymi (w różnych regionach) i sieciami lokalnymi komunikują się ze sobą za pośrednictwem sieci wirtualnej koncentratora, natywne rozwiązanie na platformie Azure jest połączenie z siecią VPN lokacja lokacja przy użyciu sieci VPN. Jest dostępna Alternatywna opcja na potrzeby routingu w piaście urządzenia WUS.

Do konfigurowania bramy sieci VPN, zobacz [konfigurowania bramy sieci VPN][VPN]. Do wdrażania urządzeń WUS wysokiej dostępności, zobacz [wdrożenia o wysokiej dostępności urządzeń WUS][Deploy-NVA].

## <a name="next-steps"></a>Kolejne kroki

Szczegółowe informacje dotyczące konfiguracji topologii testów, zobacz [szczegóły konfiguracji][Configuration].

Do analizy płaszczyzna kontroli ustawień testu i zrozumieć widoków innej sieci wirtualnej/sieć VLAN topologii, zobacz [analizy płaszczyznę kontroli][Control-Analysis].

Analiza płaszczyzny danych konfiguracji testu i widokami funkcji monitorowania sieci platformy Azure, zobacz [płaszczyzny danych analizy][Data-Analysis].

Aby dowiedzieć się, jak wiele obwodów usługi ExpressRoute, możesz nawiązać połączenie bramy usługi ExpressRoute lub ile bram usługi ExpressRoute można nawiązać połączenie obwodu usługi ExpressRoute lub Dowiedz się, inne limity skalowania usługi expressroute, zobacz [ExpressRoute — często zadawane pytania][ExR-FAQ]



<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "topologii testu"

<!--Link References-->
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha




