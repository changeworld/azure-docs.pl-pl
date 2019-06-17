---
title: 'Współdziałanie w funkcjach łączność z zaplecza platformy Azure: Testuj ustawienia | Dokumentacja firmy Microsoft'
description: W tym artykule opisano ustawienia testu, służących do analizowania współdziałanie usługi ExpressRoute, sieci VPN typu lokacja lokacja i wirtualnych sieci równorzędnych na platformie Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 8be546c5dba4c6c694c8cef03a4bdd6005d68189
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60811109"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-setup"></a>Współdziałanie w funkcjach łączność z zaplecza platformy Azure: Ustawienia testu

W tym artykule opisano ustawienia testu, służących do analizowania jak usługi platformy Azure networking współdziałać na poziomie poziom płaszczyzna kontroli i płaszczyzny danych. Przyjrzyjmy się chwilę składników sieci platformy Azure:

-   **Azure ExpressRoute**: Użyj prywatnej komunikacji równorzędnej w usługi Azure ExpressRoute, aby bezpośrednio łączyć się wdrożenia usługi Azure Virtual Network z prywatnych przestrzeni adresów IP w sieci lokalnej. Usługa ExpressRoute może pomóc Ci osiągnąć wyższą przepustowość i połączenie prywatne. Wielu partnerów ekonomicznym usługi ExpressRoute oferuje połączenia usługi ExpressRoute z umowami SLA. Aby dowiedzieć się więcej na temat usługi ExpressRoute i Dowiedz się, jak skonfigurować usługi ExpressRoute, zobacz [wprowadzenie do usługi ExpressRoute][ExpressRoute].
-   **Site-to-site VPN**: Bramy sieci VPN platformy Azure jako sieci VPN lokacja lokacja służy do bezpiecznego połączenia między siecią lokalną na platformę Azure za pośrednictwem Internetu lub przy użyciu usługi ExpressRoute. Aby dowiedzieć się, jak skonfigurować sieć VPN lokacja lokacja, do połączenia z platformą Azure, zobacz [Konfigurowanie bramy sieci VPN][VPN].
-   **Komunikacja równorzędna sieci wirtualnych**: Użyj komunikacji równorzędnej sieci wirtualnych (VNet), aby poprawnie ustanowić połączenia między sieciami wirtualnymi w sieci wirtualnej platformy Azure. Aby dowiedzieć się więcej na temat komunikacji równorzędnej sieci wirtualnych, zobacz [samouczek dotyczący wirtualnych sieci równorzędnych][VNet].

## <a name="test-setup"></a>Ustawienia testu

Na poniższym rysunku przedstawiono konfiguracji testu:

[![1]][1]

Centralnym punktem ustawień testu jest piastą 1 Region platformy Azure. Piastą jest podłączony do różnych sieci w następujący sposób:

-   Piastą połączyć sieć wirtualną będącą szprychą za pomocą komunikacji równorzędnej sieci wirtualnych. Sieć wirtualną będącą szprychą ma zdalny dostęp do obu bram w sieci wirtualnej serwera centralnego.
-   Piastą jest podłączony do gałęzi sieci wirtualnej przy użyciu sieci VPN typu lokacja lokacja. Połączenie używa eBGP w celu wymiany tras.
-   Piastą jest podłączony do sieci 1 lokalizacji lokalnej za pomocą prywatnej komunikacji równorzędnej usługi ExpressRoute jako ścieżki podstawowej. Korzysta ona połączenie sieci VPN typu lokacja lokacja jako ścieżki kopii zapasowej. W dalszej części tego artykułu nazywamy tego obwodu usługi ExpressRoute 1 usługi ExpressRoute. Domyślnie obwody usługi ExpressRoute zapewniają łączność nadmiarowa w celu zapewnienia wysokiej dostępności. ExpressRoute 1 identyfikator interfejsu routera brzegowego (CE) drugiego klienta, skierowana dodatkowego Microsoft Enterprise krawędzi routera (rozwiązania MSEE) jest wyłączona. Czerwona linia za pośrednictwem strzałki podwójnej linii na poprzednim rysunku reprezentuje wyłączenia interfejsu routera podrzędnego CE.
-   Piastą jest podłączony do sieci lokalnej w lokalizacji 2 przy użyciu innej prywatnej komunikacji równorzędnej usługi ExpressRoute. W dalszej części tego artykułu nazywamy tego drugiego obwodu usługi ExpressRoute 2 usługi ExpressRoute.
-   ExpressRoute 1 również zarówno w sieci wirtualnej serwera centralnego, jak i w sieci lokalnej w lokalizacji 1 łączy się z zdalna sieć wirtualna 2 Region platformy Azure.

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

Dowiedz się więcej o [szczegóły konfiguracji] [ Configuration] topologia testów.

Dowiedz się więcej o [analizy płaszczyznę sterowania] [ Control-Analysis] konfiguracji testu i widoki różnych sieci wirtualnych lub sieci VLAN w topologii.

Dowiedz się więcej o [analizy na płaszczyźnie danych] [ Data-Analysis] konfiguracji testu i widokami funkcji monitorowania sieci platformy Azure.

Zobacz [ExpressRoute — często zadawane pytania] [ ExR-FAQ] do:
-   Dowiedz się, jak wiele obwodów usługi ExpressRoute, możesz nawiązać połączenie bramy usługi ExpressRoute.
-   Dowiedz się, ile bram usługi ExpressRoute, można połączyć z obwodem usługi ExpressRoute.
-   Poznaj inne limity skalowania usługi expressroute.


<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "diagram topologii testu"

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


