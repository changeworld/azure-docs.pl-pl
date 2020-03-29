---
title: 'Współdziałanie w funkcjach łączności zaplecza platformy Azure: Konfiguracja testowa | Dokumenty firmy Microsoft'
description: W tym artykule opisano konfigurację testu, której można użyć do analizowania współdziałania między usługą ExpressRoute, sieci VPN typu lokacja-lokacja i komunikacji równorzędnej w sieci wirtualnej na platformie Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 0cbd4b620a03ed26e95679cf7cb1abef277a9471
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873799"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-setup"></a>Współdziałanie w funkcjach łączności zaplecza platformy Azure: konfiguracja testowa

W tym artykule opisano konfigurację testu, której można użyć do analizy sposobu współdziałania usług sieciowych platformy Azure na poziomie płaszczyzny sterowania i płaszczyzny danych. Przyjrzyjmy się pokrótce składnikom sieci platformy Azure:

-   **Azure ExpressRoute:** Użyj prywatnej komunikacji równorzędnej w usłudze Azure ExpressRoute, aby bezpośrednio połączyć prywatne przestrzenie IP w sieci lokalnej z wdrożeniami sieci wirtualnej platformy Azure. Usługa ExpressRoute może pomóc w osiągnięciu wyższej przepustowości i połączenia prywatnego. Wielu partnerów programu ExpressRoute eco oferuje łączność usługi ExpressRoute z łatWami. Aby dowiedzieć się więcej o udziale usługi ExpressRoute i dowiedzieć się, jak skonfigurować program ExpressRoute, zobacz [Wprowadzenie do usługi ExpressRoute][ExpressRoute].
-   **Sieć VPN typu lokacja lokacja:** usługa Azure VPN Gateway umożliwia bezpieczne łączenie sieci lokalnej z platformą Azure za pośrednictwem Internetu lub przy użyciu usługi ExpressRoute. Aby dowiedzieć się, jak skonfigurować sieć VPN typu lokacja lokacja do łączenia się z platformą Azure, zobacz [Konfigurowanie bramy sieci VPN][VPN].
-   **Komunikacja równorzędna sieci wirtualnej:** Użyj komunikacji równorzędnej sieci wirtualnej (VNet), aby ustanowić łączność między sieciami wirtualnymi w sieci wirtualnej platformy Azure. Aby dowiedzieć się więcej na temat komunikacji równorzędnej sieci wirtualnej, zobacz [samouczek dotyczący komunikacji równorzędnej sieci wirtualnej][VNet].

## <a name="test-setup"></a>Konfiguracja testu

Poniższy rysunek ilustruje konfigurację testu:

![1][1]

Centralnym elementem konfiguracji testu jest hub vnet w regionie Azure 1. Hub VNet jest podłączony do różnych sieci w następujący sposób:

-   Hub VNet jest podłączony do sieci wirtualnej szprychy przy użyciu komunikacji równorzędnej sieci wirtualnej. Szprychowa sieci wirtualnej ma zdalny dostęp do obu bram w sieci wirtualnej centrum.
-   Hub VNet jest połączony z siecią wirtualną oddziału przy użyciu sieci VPN lokacji lokacji. Łączność używa eBGP do wymiany tras.
-   Hub VNet jest połączony z lokalną siecią lokalizacji 1 przy użyciu usługi ExpressRoute private peering jako ścieżki podstawowej. Używa łączności sieci VPN między lokacjami jako ścieżki tworzenia kopii zapasowej. Poniżej dalsza część artykułu Odnosimy się do tego obwodu usługi ExpressRoute jako usługi ExpressRoute 1. Domyślnie obwody usługi ExpressRoute zapewniają nadmiarową łączność w celu zapewnienia wysokiej dostępności. W usłudze ExpressRoute 1 podinterface routera pomocniczego klienta (CE), który stoi w obliczu pomocniczego routera Microsoft Enterprise Edge Router (MSEE), jest wyłączony. Czerwona linia nad strzałką dwuwierszową na poprzednim rysunku reprezentuje wyłączony podprzemchowy routera CE.
-   Hub VNet jest połączony z lokalną siecią lokalizacji 2 przy użyciu innej prywatnej komunikacji równorzędnej usługi ExpressRoute. Poniżej dalsza część artykułu Odnosimy się do tego drugiego obwodu usługi ExpressRoute jako usługi ExpressRoute 2.
-   Usługa ExpressRoute 1 łączy również sieć wirtualną centrum i lokalną sieć lokalizacji 1 ze zdalną siecią wirtualną w regionie Azure Region 2.

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

Dowiedz się więcej o [szczegółach konfiguracji][Configuration] topologii testu.

Dowiedz się więcej o [analizie płaszczyzny sterowania][Control-Analysis] konfiguracji testu i widokach różnych sieci wirtualnych lub sieci VLAN w topologii.

Dowiedz się więcej o [analizie płaszczyzny danych][Data-Analysis] podczas instalacji testów i widoków funkcji monitorowania sieci platformy Azure.

Zobacz często zadawane [pytania dotyczące usługi ExpressRoute,][ExR-FAQ] aby:
-   Dowiedz się, ile obwodów usługi ExpressRoute można połączyć z bramą usługi ExpressRoute.
-   Dowiedz się, ile bram usługi ExpressRoute można połączyć z obwódem usługi ExpressRoute.
-   Dowiedz się więcej o innych ograniczeniach skali usługi ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "Diagram topologii testowej"

<!--Link References-->
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: connectivty-interoperability-configuration.md
[Control-Analysis]: connectivty-interoperability-control-plane.md
[Data-Analysis]: connectivty-interoperability-data-plane.md
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha


