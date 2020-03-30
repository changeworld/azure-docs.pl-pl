---
title: Łączność między sieciami azureowymi
description: Na tej stronie opisano scenariusz aplikacji dla łączności między sieciowej i rozwiązania opartego na funkcjach sieci platformy Azure.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 48ec26cc98310dfeb61aa17018c940b431cfbcee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644260"
---
# <a name="cross-network-connectivity"></a>Łączność między sieciami

Firma Fabrikam Inc. ma dużą obecność fizyczną i wdrożenie platformy Azure we wschodnich stanach USA. Firma Fabrikam ma łączność zaplecza między wdrożeniami lokalnymi i wdrożeniami platformy Azure za pośrednictwem usługi ExpressRoute. Podobnie Contoso Ltd. ma obecność i wdrożenie platformy Azure w zachodnie stany USA. Contoso ma łączność zaplecza między jego lokalnych i wdrożenia platformy Azure za pośrednictwem usługi ExpressRoute.  

Fabrikam Inc. przejmuje Firmę Contoso Ltd. Po fuzji Firma Fabrikam chce połączyć sieci. Poniższy rysunek ilustruje scenariusz:

 [![1]][1]

Strzałki przerywane na środku powyższego rysunku wskazują żądane połączenia międzysystemowe sieci. W szczególności istnieją trzy typy połączeń krzyżowych: 1) Fabrikam i Contoso VNets cross connect, 2) Cross regional on-premises and VNets cross connects (czyli łączenie sieci lokalnej Fabrikam z siecią wirtualną Contoso i łączenie contoso sieci lokalnej do sieci wirtualnej Fabrikam vnet) i 3) połączenia krzyżowego sieciowej firmy Fabrikam i Contoso. 

W poniższej tabeli przedstawiono tabelę tras prywatnej komunikacji równorzędnej usługi ExpressRoute firmy Contoso Ltd. przed połączeniem.

[![2]][2]

W poniższej tabeli przedstawiono efektywne trasy maszyny Wirtualnej w subskrypcji Contoso przed fuzją. W tabeli maszyna wirtualna w sieci wirtualnej jest świadoma przestrzeni adresowej sieci wirtualnej i sieci lokalnej Contoso, z wyjątkiem sieci domyślnej. 

[![4]][4]

W poniższej tabeli przedstawiono tabelę tras prywatnej komunikacji równorzędnej usługi ExpressRoute firmy Fabrikam Inc. przed połączeniem.

[![3]][3]

W poniższej tabeli przedstawiono efektywne trasy maszyny Wirtualnej w subskrypcji fabrikam przed fuzją. W tabeli maszyna wirtualna na sieci wirtualnej jest świadoma przestrzeni adresowej sieci wirtualnej i sieci lokalnej firmy Fabrikam, z wyjątkiem sieci domyślnej.

[![5]][5]

W tym artykule przejdźmy krok po kroku i omówić, jak osiągnąć żądane połączenia krzyżowe przy użyciu następujących funkcji sieci platformy Azure:

* [Komunikacja równorzędna sieci wirtualnej][Virtual network peering] 
* [Połączenie usługi ExpressRoute w sieci wirtualnej][connection]
* [Globalny zasięg][Global Reach] 

## <a name="cross-connecting-vnets"></a>Sieci wirtualne łączące poprzecznego

Komunikacja równorzędna sieci wirtualnej (komunikacja równorzędna sieci wirtualnej) zapewnia najbardziej optymalną i najlepszą wydajność sieci podczas łączenia dwóch sieci wirtualnych. Komunikacja równorzędna sieci wirtualnej obsługuje komunikację równorzędną dwóch sieci wirtualnych zarówno w tym samym regionie platformy Azure (powszechnie nazywany komunikacja równorzędna sieci wirtualnej), jak i w dwóch różnych regionach platformy Azure (powszechnie nazywanych komunikacją równorzędną sieci wirtualnej globalnej). 

Skonfigurujmy globalną sieć wirtualną komunikacji równorzędnej między sieciami wirtualnymi w subskrypcjach platformy Contoso i Fabrikam azure. Aby dowiedzieć się, jak utworzyć komunikację równorzędną sieci wirtualnej między dwiema sieciami wirtualnymi, zobacz Tworzenie artykułu [komunikacji równorzędnej sieci wirtualnej.][Configure VNet peering]

Na poniższej ilustracji przedstawiono architekturę sieci po skonfigurowaniu komunikacji równorzędnej sieci wirtualnej globalnej.

[![6]][6]

W poniższej tabeli przedstawiono trasy znane maszynie wirtualnej subskrypcji Contoso. Zwróć uwagę na ostatni wpis tabeli. Ten wpis jest wynikiem krzyżowego łączenia sieci wirtualnych.

[![7]][7]

W poniższej tabeli przedstawiono trasy znane maszynie wirtualnej subskrypcji fabrikam. Zwróć uwagę na ostatni wpis tabeli. Ten wpis jest wynikiem krzyżowego łączenia sieci wirtualnych.

[![8]][8]

Komunikacja równorzędna sieci wirtualnej bezpośrednio łączy dwie sieci wirtualne (zobacz nie ma następnego przeskoku dla wpisu *VNetGlobalPeering* w powyższych dwóch tabelach)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Łączenie sieci wirtualnych z sieciami lokalnymi

Możemy podłączyć obwód usługi ExpressRoute do wielu sieci wirtualnych. Zobacz [Limity subskrypcji i usług][Subscription limits] dla maksymalnej liczby sieci wirtualnych, które mogą być podłączone do obwodu usługi ExpressRoute. 

Połączmy obwód Fabrikam ExpressRoute z siecią wirtualną subskrypcji Contoso i podobnie obwód Contoso ExpressRoute z siecią wirtualną subskrypcji firmy Fabrikam, aby umożliwić łączność krzyżową między sieciami wirtualnymi a sieciami lokalnymi. Aby połączyć sieć wirtualną z obwodem usługi ExpressRoute w innej subskrypcji, musimy utworzyć i użyć autoryzacji.  Zobacz artykuł: [Podłączanie sieci wirtualnej do obwodu usługi ExpressRoute][Connect-ER-VNet].

Na poniższej ilustracji przedstawiono architekturę sieci po skonfigurowaniu połączenia krzyżowego usługi ExpressRoute z sieciami wirtualnymi.

[![9]][9]

W poniższej tabeli przedstawiono tabelę tras prywatnej komunikacji równorzędnej usługi ExpressRoute firmy Contoso Ltd., po połączeniu sieci wirtualnych z sieciami lokalnymi za pośrednictwem usługi ExpressRoute. Zobacz, że tabela tras ma trasy należące do obu sieci wirtualnych.

[![10]][10]

W poniższej tabeli przedstawiono tabelę tras prywatnej komunikacji równorzędnej usługi ExpressRoute firmy Fabrikam Inc., po połączeniu sieci wirtualnych z sieciami lokalnymi za pośrednictwem usługi ExpressRoute. Zobacz, że tabela tras ma trasy należące do obu sieci wirtualnych.

[![11]][11]

W poniższej tabeli przedstawiono trasy znane maszynie wirtualnej subskrypcji Contoso. Zwróć uwagę na wpisy *bramy sieci wirtualnej* tabeli. Maszyna wirtualna widzi trasy dla obu sieci lokalnych.

[![12]][12]

W poniższej tabeli przedstawiono trasy znane maszynie wirtualnej subskrypcji fabrikam. Zwróć uwagę na wpisy *bramy sieci wirtualnej* tabeli. Maszyna wirtualna widzi trasy dla obu sieci lokalnych.

[![13]][13]

>[!NOTE]
>W subskrypcjach fabrikam i/lub Contoso można również szprychy sieci wirtualnych do odpowiedniej sieci wirtualnej centrum (projekt koncentratora i szprychy nie jest zilustrowany na diagramach architektury w tym artykule). Połączenia krzyżowe między bramami sieci wirtualnej koncentratora z usługą ExpressRoute umożliwią również komunikację między centrami wschód-zachód i szprychami.
>

## <a name="cross-connecting-on-premises-networks"></a>Łączenie krzyżowe w sieciach lokalnych

Usługa ExpressRoute Global Reach zapewnia łączność między sieciami lokalnymi połączonymi z różnymi obwodami usługi ExpressRoute. Skonfigurujmy globalny zasięg między obwodami Contoso i Fabrikam ExpressRoute. Ponieważ obwody usługi ExpressRoute są w różnych subskrypcji, musimy utworzyć i używać autoryzacji. Aby uzyskać wskazówki dotyczące krok po kroku, zobacz Konfigurowanie globalnego [zasięgu usługi ExpressRoute.][Configure Global Reach]

Na poniższej ilustracji przedstawiono architekturę sieci po skonfigurowaniu globalnego zasięgu.

[![14]][14]

W poniższej tabeli przedstawiono tabelę tras prywatnej komunikacji równorzędnej usługi ExpressRoute firmy Contoso Ltd., po skonfigurowaniu globalnego zasięgu. Zobacz, że tabela tras ma trasy należące do obu sieci lokalnych. 

[![15]][15]

W poniższej tabeli przedstawiono tabelę tras prywatnej komunikacji równorzędnej usługi ExpressRoute firmy Fabrikam Inc., po skonfigurowaniu globalnego zasięgu. Zobacz, że tabela tras ma trasy należące do obu sieci lokalnych.

[![16]][16]

## <a name="next-steps"></a>Następne kroki

Zobacz [często zadawane pytania dotyczące sieci wirtualnej][VNet-FAQ], aby uzyskać więcej pytań dotyczących sieci wirtualnej i komunikacji równorzędnej sieci wirtualnej. Zobacz [często zadawane pytania dotyczące usługi ExpressRoute, aby][ER-FAQ] uzyskać więcej pytań dotyczących usługi ExpressRoute i łączności z siecią wirtualną.

Globalny zasięg jest wdrażany w zależności od kraju/regionu. Aby sprawdzić, czy globalny zasięg jest dostępny w krajach/regionach, które chcesz, zobacz [Globalny zasięg usługi ExpressRoute][Global Reach].

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "Scenariusz aplikacji"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "Tabela tras Contoso ExpressRoute przed połączeniem"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "Tabela tras Fabrikam ExpressRoute przed połączeniem"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "Trasy maszyn wirtualnych Contoso przed połączeniem"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "tras Fabrikam VM przed fuzją"
[6]: ./media/cross-network-connectivity/vnet-peering.png "Architektura po komunikacji równorzędnej sieci wirtualnej"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "Trasy maszyn wirtualnych Contoso po komunikacji równorzędnej sieci wirtualnej"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "tras fabrikam VM po komunikacji równorzędnej sieci wirtualnej"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "Architektura po połączeniu krzyżowym ExpressRoutes"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "Tabela tras Contoso ExpressRoute po połączeniu krzyżowym Sieci ExR i VNets"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "Tabela tras Fabrikam ExpressRoute po połączeniu krzyżowym Sieci ExR i VNets"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "Trasy contoso VM po połączeniu krzyżowym ExR i sieci wirtualnych"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "tras Fabrikam VM po połączeniu krzyżowym ExR i VNets"
[14]: ./media/cross-network-connectivity/globalreach.png "Architektura po skonfigurowaniu globalnego zasięgu"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "Tabela tras contoso expressroute po osiągnięciu globalnym"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "Tabela tras Fabrikam ExpressRoute po globalnym zasięgu"

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq