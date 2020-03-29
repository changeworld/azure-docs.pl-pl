---
title: 'Współdziałanie w funkcjach łączności zaplecza platformy Azure: analiza płaszczyzny sterowania | Dokumenty firmy Microsoft'
description: Ten artykuł zawiera analizę płaszczyzny sterowania konfiguracji testu, której można użyć do analizowania współdziałania między usługą ExpressRoute, sieci VPN typu lokacja-lokacja i komunikacji równorzędnej w sieci wirtualnej na platformie Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 4921e4c4fc0da95250a0171c66d6a69093b10687
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873849"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-control-plane-analysis"></a>Współdziałanie w funkcjach łączności zaplecza platformy Azure: analiza płaszczyzny sterowania

W tym artykule opisano analizę płaszczyzny sterowania [konfiguracji testu][Setup]. Można również przejrzeć [konfigurację konfiguracji testu][Configuration] i [analizę płaszczyzny danych][Data-Analysis] konfiguracji testu.

Analiza płaszczyzny sterowania zasadniczo sprawdza trasy, które są wymieniane między sieciami w ramach topologii. Analiza płaszczyzny sterowania może pomóc w zrozumieniu, jak różne sieci wyświetlą topologię.

## <a name="hub-and-spoke-vnet-perspective"></a>Perspektywa koncentratora i sieci wirtualnej

Poniższy rysunek ilustruje sieć z perspektywy sieci wirtualnej koncentratora (VNet) i sieci wirtualnej szprychy (wyróżnionej na niebiesko). Rysunek przedstawia również numer systemu autonomicznego (ASN) różnych sieci i tras, które są wymieniane między różnymi sieciami: 

![1][1]

Numer ASN bramy azure expressroute sieci wirtualnej różni się od asn routerów microsoft enterprise edge (MSEE). Brama usługi ExpressRoute używa prywatnego asn (wartość **65515),** a MSEE używają publicznej sieci ASN (wartość **12076)** globalnie. Podczas konfigurowania komunikacji równorzędnej usługi ExpressRoute, ponieważ MSEE jest elementem równorzędnym, używasz **12076** jako równorzędnego ASN. Po stronie platformy Azure msee ustanawia komunikacji równorzędnej eBGP z bramą usługi ExpressRoute. Podwójna komunikacja równorzędna eBGP ustanowiona przez MSEE dla każdego elementu równorzędnego usługi ExpressRoute jest przezroczysta na poziomie płaszczyzny sterowania. W związku z tym podczas wyświetlania tabeli trasy usługi ExpressRoute, widzisz sieci Wirtualnej usługi Asn bramy usługi ExpressRoute dla prefiksów sieci wirtualnej. 

Na poniższej ilustracji przedstawiono przykładową tabelę tras usługi ExpressRoute: 

![5][5]

W ramach platformy Azure asn jest istotne tylko z punktu widzenia komunikacji równorzędnej. Domyślnie asn zarówno bramy usługi ExpressRoute, jak i bramy sieci VPN w usłudze Azure VPN Gateway to **65515**.

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>Lokalna lokalizacja 1 i zdalna perspektywa sieci wirtualnej za pośrednictwem usługi ExpressRoute 1

Zarówno lokalna lokalizacja 1, jak i zdalna sieci wirtualne są połączone z hubową siecią wirtualną za pośrednictwem usługi ExpressRoute 1. Mają tę samą perspektywę topologii, jak pokazano na poniższym diagramie:

![2][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>Lokalna lokalizacja 1 i perspektywa sieci wirtualnej oddziału za pośrednictwem sieci VPN lokacji lokacji

Zarówno lokalna lokalizacja 1, jak i sieć wirtualna oddziału są połączone z bramą sieci VPN sieci wirtualnej koncentratora za pośrednictwem połączenia sieci VPN lokacja lokacja lokacja. Mają tę samą perspektywę topologii, jak pokazano na poniższym diagramie:

![3][3]

## <a name="on-premises-location-2-perspective"></a>Lokalna perspektywa lokalizacji 2

Lokalna lokalizacja 2 jest połączona z hubową siecią wirtualną za pośrednictwem prywatnej komunikacji równorzędnej usługi ExpressRoute 2: 

![4][4]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Łączność sieci VPN typu usługi ExpressRoute i lokacja lokacja w tandemie

###  <a name="site-to-site-vpn-over-expressroute"></a>Sieć VPN typu lokacja za pośrednictwem usługi ExpressRoute

Sieć VPN typu lokacja-lokacja umożliwia korzystanie z usługi ExpressRoute Microsoft w celu prywatnej wymiany danych między siecią lokalną a sieciami wirtualnymi platformy Azure. Dzięki tej konfiguracji można wymieniać dane z poufnością, autentycznością i integralnością. Wymiana danych jest również anty-powtórka. Aby uzyskać więcej informacji na temat konfigurowania sieci VPN IPsec między lokacjami w trybie tunelowania przy użyciu komunikacji równorzędnej firmy ExpressRoute Microsoft, zobacz [Sieć VPN typu lokacja-lokacja za pośrednictwem usługi ExpressRoute Microsoft peering][S2S-Over-ExR]. 

Podstawowym ograniczeniem konfigurowania sieci VPN typu lokacja lokacja, która korzysta z komunikacji równorzędnej firmy Microsoft, jest przepływność. Przepływność w tunelu IPsec jest ograniczona przez pojemność bramy sieci VPN. Przepływność bramy sieci VPN jest niższa niż przepływność usługi ExpressRoute. W tym scenariuszu przy użyciu tunelu IPsec dla wysoce bezpiecznego ruchu i przy użyciu prywatnej komunikacji równorzędnej dla wszystkich innych ruchu pomaga zoptymalizować wykorzystanie przepustowości usługi ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Sieć VPN typu lokacja-lokacja jako bezpieczna ścieżka trybu failover dla usługi ExpressRoute

Usługa ExpressRoute służy jako nadmiarowa para obwodów, aby zapewnić wysoką dostępność. Łączność geograficznie nadmiarowa usługi ExpressRoute można skonfigurować w różnych regionach platformy Azure. Ponadto, jak pokazano w naszej konfiguracji testów, w regionie platformy Azure można użyć sieci VPN lokacji do lokacji, aby utworzyć ścieżkę trybu failover dla łączności usługi ExpressRoute. Gdy te same prefiksy są anonsowane zarówno za pośrednictwem usługi ExpressRoute, jak i sieci VPN typu lokacja lokacja, platforma Azure nadaje priorytety usługi ExpressRoute. Aby uniknąć asymetrycznego routingu między usługą ExpressRoute a siecią VPN typu lokacja lokacja, konfiguracja sieci lokalnej powinna również odwzajemnić się przy użyciu łączności usługi ExpressRoute, zanim będzie używana łączność sieci VPN między lokacjami.

Aby uzyskać więcej informacji na temat konfigurowania współistniejących połączeń dla usługi ExpressRoute i sieci VPN typu lokacja-lokacja, zobacz [Współistnienie usługi ExpressRoute i współistnienie lokacji.][ExR-S2S-CoEx]

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Rozszerzanie łączności zaplecza do sieci wirtualnych i oddziałów szprychowych

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Szprychowa łączność sieci wirtualnej przy użyciu komunikacji równorzędnej sieci wirtualnej

Architektura koncentratora i sieci wirtualnej jest szeroko stosowana. Centrum jest siecią wirtualną na platformie Azure, która działa jako centralny punkt łączności między sieciami wirtualnymi szprychy i siecią lokalną. Szprychy są sieci wirtualne, które równorzędne z koncentratora i które można użyć do izolowania obciążeń. Przepływy ruchu między lokalnym centrum danych a koncentratorem za pośrednictwem połączenia usługi ExpressRoute lub SIECI VPN. Aby uzyskać więcej informacji na temat architektury, zobacz [Implementowanie topologii sieci z szprychą koncentratora na platformie Azure.][Hub-n-Spoke]

W sieci wirtualnej komunikacji równorzędnej w regionie, szprychowe sieci wirtualne mogą używać bram sieci wirtualnej centrum (zarówno sieci VPN, jak i bram usługi ExpressRoute) do komunikowania się z sieciami zdalnymi.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Łączność sieci wirtualnej oddziału przy użyciu sieci VPN lokacja lokacja

Sieci wirtualne oddziałów, które znajdują się w różnych regionach, oraz sieci lokalne, mogą komunikować się ze sobą za pośrednictwem sieci wirtualnej koncentratora. Natywne rozwiązanie platformy Azure dla tej konfiguracji to łączność sieci VPN między lokacjami przy użyciu sieci VPN. Alternatywą jest użycie sieciowego urządzenia wirtualnego (NVA) do routingu w koncentratorze.

Aby uzyskać więcej informacji, zobacz [Deploy a highly available NVA][Deploy-NVA] [Co to jest brama sieci VPN?][VPN]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [analizie płaszczyzny danych][Data-Analysis] podczas instalacji testów i widoków funkcji monitorowania sieci platformy Azure.

Zobacz często zadawane [pytania dotyczące usługi ExpressRoute,][ExR-FAQ] aby:
-   Dowiedz się, ile obwodów usługi ExpressRoute można połączyć z bramą usługi ExpressRoute.
-   Dowiedz się, ile bram usługi ExpressRoute można połączyć z obwódem usługi ExpressRoute.
-   Dowiedz się więcej o innych ograniczeniach skali usługi ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "Hub i szprycha VNet perspektywy topologii"
[2]: ./media/backend-interoperability/Loc1ExRView.png "Lokalizacja 1 i zdalna perspektywa sieci wirtualnej topologii za pośrednictwem usługi ExpressRoute 1"
[3]: ./media/backend-interoperability/Loc1VPNView.png "Lokalizacja 1 i perspektywa sieci wirtualnej oddziału topologii za pośrednictwem sieci VPN lokacja-lokacja"
[4]: ./media/backend-interoperability/Loc2View.png "Lokalizacja 2 perspektywa topologii"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "Tabela trasy usługi ExpressRoute 1"

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


