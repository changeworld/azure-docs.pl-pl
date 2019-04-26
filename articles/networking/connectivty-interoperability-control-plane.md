---
title: 'Współdziałanie w funkcjach łączność z zaplecza platformy Azure: Kontrolowanie analizy płaszczyzny | Dokumentacja firmy Microsoft'
description: Ten artykuł zawiera analizy płaszczyzna kontroli konfiguracji testu, które służy do analizowania współdziałanie usługi ExpressRoute, sieci VPN typu lokacja lokacja i wirtualnych sieci równorzędnych na platformie Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 28ce4cfd0c62586510a6f7dfdeca8b552fe9638e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60425642"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-control-plane-analysis"></a>Współdziałanie w funkcjach łączność z zaplecza platformy Azure: Analiza płaszczyzna kontroli

W tym artykule opisano analizę płaszczyzna kontroli [Testuj ustawienia][Setup]. Możesz również przejrzeć [w konfiguracji testu] [ Configuration] i [analizy na płaszczyźnie danych] [ Data-Analysis] ustawień testu.

Analiza płaszczyzna kontroli sprawdza zasadniczo tras, które są wymieniane między sieci w obrębie topologii. Analiza płaszczyzna kontroli mogą ułatwić zrozumienie sposobu różnych sieciach wyświetlić topologię.

## <a name="hub-and-spoke-vnet-perspective"></a>Gwiazda Perspektywa sieci wirtualnej

Na poniższym rysunku przedstawiono sieć z punktu widzenia centralnej sieci wirtualnej (VNet) i szprychy sieci wirtualnej (wyróżnione na niebiesko). Na rysunku przedstawiono również numer systemu autonomicznego (ASN) w różnych sieciach i tras, które są wymieniane między różnymi sieciami: 

[![1]][1]

Numer ASN bramy usługi Azure ExpressRoute w sieci wirtualnej różni się od numeru ASN programu Microsoft routerami granicznymi Enterprise (Msee). Brama usługi ExpressRoute używa prywatnego numeru ASN (wartość **65515**) i Msee Użyj publicznego numeru ASN (wartość **12076**) globalnie. Po skonfigurowaniu komunikacji równorzędnej usługi ExpressRoute, ponieważ MSEE równorzędnej, możesz użyć **12076** jako element równorzędny numer ASN. Na stronie platformy Azure rozwiązania MSEE ustanawia eBGP komunikacji równorzędnej z bramą ExpressRoute. Podwójna eBGP komunikacji równorzędnej, MSEE ustanawia dla każdego komunikacji równorzędnej usługi ExpressRoute na poziomie płaszczyzna kontroli jest przezroczysty. Podczas przeglądania tabeli tras usługi ExpressRoute widać bramę usługi ExpressRoute w sieci wirtualnej ASN dla sieci wirtualnej prefiksów. 

Na poniższej ilustracji przedstawiono przykładową tabelę tras usługi ExpressRoute: 

[![5]][5]

W ramach platformy Azure numer ASN jest istotne tylko z punktu widzenia komunikacji równorzędnej. Domyślny numer ASN zarówno bramę usługi ExpressRoute, jak i bramy sieci VPN w usłudze Azure VPN Gateway to **65515**.

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>W środowisku lokalnym 1 lokalizacji do zdalnego punktu widzenia sieci wirtualnej za pośrednictwem usługi ExpressRoute 1

1 lokalizacji lokalnej i zdalnej sieci wirtualnej są podłączone do koncentratora, sieci wirtualnych przy użyciu usługi ExpressRoute 1. Mają tego samego punktu widzenia topologii, jak pokazano na poniższym diagramie:

[![2]][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>W środowisku lokalnym Lokalizacja 1 i gałąź Perspektywa sieci wirtualnej za pośrednictwem sieci VPN lokacja lokacja

Zarówno 1 lokalizację lokalną, jak i gałąź, dla sieci wirtualnej są podłączone do bramy sieci VPN sieci wirtualnej koncentratora, za pośrednictwem połączenia sieci VPN typu lokacja lokacja. Mają tego samego punktu widzenia topologii, jak pokazano na poniższym diagramie:

[![3]][3]

## <a name="on-premises-location-2-perspective"></a>W środowisku lokalnym perspektywy lokalizacji 2

Lokalne lokalizacji 2 jest podłączony do sieci wirtualnej serwera centralnego za pośrednictwem prywatnej komunikacji równorzędnej usługi ExpressRoute 2: 

[![4]][4]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Usługi ExpressRoute i lokacja lokacja połączenia sieci VPN w tandem

###  <a name="site-to-site-vpn-over-expressroute"></a>Site-to-site VPN za pośrednictwem usługi ExpressRoute

Sieć VPN lokacja lokacja można skonfigurować przy użyciu usługi ExpressRoute komunikacji równorzędnej firmy Microsoft do prywatnie wymiany danych między siecią lokalną i sieciami wirtualnymi platformy Azure. W przypadku tej konfiguracji można wymiany danych z poufność, autentyczności i integralności. Wymiana danych jest także powtarzaniu. Aby uzyskać więcej informacji na temat konfigurowania sieci VPN lokacja lokacja protokołu IPsec w trybie tunelowania za pomocą komunikacji równorzędnej firmy Microsoft usługi ExpressRoute, zobacz [Site-to-site VPN za pośrednictwem komunikacji równorzędnej firmy Microsoft usługi ExpressRoute][S2S-Over-ExR]. 

Głównym ograniczeniem konfigurowania sieci VPN lokacja lokacja, który korzysta z komunikacji równorzędnej firmy Microsoft jest przepływność. Przepływność za pośrednictwem tunelu IPsec jest ograniczona przez pojemność bramy sieci VPN. Przepływność bramy sieci VPN jest mniejszy niż przepływności usługi ExpressRoute. W tym scenariuszu przy użyciu tunelu IPsec, wysoce bezpieczna ruchu i za pomocą prywatnej komunikacji równorzędnej dla innego ruchu pomaga zoptymalizować wykorzystanie przepustowości usługi ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Site-to-site VPN jako bezpieczna ścieżka trybu failover dla usługi ExpressRoute

Usługa ExpressRoute służy jako parę nadmiarowych obwodu, aby zapewnić wysoką dostępność. Magazynu geograficznie nadmiarowego połączenia usługi ExpressRoute można skonfigurować w różnych regionach platformy Azure. Również jak pokazano w naszej konfiguracji testu, w obrębie regionu Azure, można użyć sieci VPN lokacja lokacja można utworzyć ścieżki pracy awaryjnej dla usługi połączenia usługi ExpressRoute. Gdy te same prefiksy są anonsowane za pośrednictwem usługi ExpressRoute oraz sieci VPN lokacja lokacja, Azure priorytetem usługi ExpressRoute. Aby uniknąć asymetryczne routingu usługi ExpressRoute i sieci VPN typu lokacja lokacja, lokalne konfiguracji sieci, należy również gospodarzami przy użyciu połączenia usługi ExpressRoute używa połączenie sieci VPN typu lokacja lokacja.

Aby uzyskać więcej informacji o sposobie konfigurowania współistniejących połączeń usługi ExpressRoute i sieci VPN lokacja lokacja, zobacz [usługi ExpressRoute i współistnienia lokacja lokacja][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Rozszerzanie zaplecza łączność sieci wirtualne będące szprychami i biur

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Typu gwiazda połączenie między sieciami przy użyciu komunikacji równorzędnej sieci wirtualnych

Gwiazda architektury sieci wirtualnej jest powszechnie używana. Piasta to sieć wirtualną na platformie Azure, która działa jako centralny punkt łączności między Twoje sieci wirtualne będące szprychami i siecią lokalną. Szprychy są sieciami wirtualnymi równorzędnymi z piastą, oraz tych, które można użyć do izolowania obciążeń. Ruch przepływa między lokalnym centrum danych a piastą za pośrednictwem połączenia usługi ExpressRoute lub sieci VPN. Aby uzyskać więcej informacji na temat architektury, zobacz [zaimplementować topologii sieci piasty i szprych na platformie Azure][Hub-n-Spoke].

W sieci wirtualnej komunikacji równorzędnej w regionie sieci wirtualne będące szprychami można użyć bramy sieci wirtualnej koncentratora (bramy sieci VPN i ExpressRoute) do komunikowania się z sieciami zdalnymi.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Połączenie między sieciami gałęzi przy użyciu sieci VPN typu lokacja lokacja

Możesz chcieć gałęzi sieci wirtualne, które znajdują się w różnych regionach i sieciami lokalnymi do komunikowania się ze sobą za pośrednictwem sieci wirtualnej serwera centralnego. Natywne rozwiązanie platformy Azure dla tej konfiguracji jest połączenie sieci VPN typu lokacja lokacja przy użyciu sieci VPN. Alternatywą jest na potrzeby routingu w piaście wirtualnego urządzenia sieciowego (WUS).

Aby uzyskać więcej informacji, zobacz [co to jest usługa VPN Gateway?] [ VPN] i [wdrożenia o wysokiej dostępności urządzeń WUS][Deploy-NVA].

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [analizy na płaszczyźnie danych] [ Data-Analysis] konfiguracji testu i widokami funkcji monitorowania sieci platformy Azure.

Zobacz [ExpressRoute — często zadawane pytania] [ ExR-FAQ] do:
-   Dowiedz się, jak wiele obwodów usługi ExpressRoute, możesz nawiązać połączenie bramy usługi ExpressRoute.
-   Dowiedz się, ile bram usługi ExpressRoute, można połączyć z obwodem usługi ExpressRoute.
-   Poznaj inne limity skalowania usługi expressroute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "gwiazdy perspektywy topologii sieci wirtualnej"
[2]: ./media/backend-interoperability/Loc1ExRView.png "1 lokalizacji i zdalnej sieci wirtualnej perspektywy topologii za pośrednictwem usługi ExpressRoute 1"
[3]: ./media/backend-interoperability/Loc1VPNView.png "Lokalizacja 1 i gałąź perspektywy topologii za pośrednictwem sieci VPN lokacja lokacja sieci wirtualnej"
[4]: ./media/backend-interoperability/Loc2View.png "perspektywy lokalizacji 2 topologii"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "tabeli tras usługi ExpressRoute 1"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
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


