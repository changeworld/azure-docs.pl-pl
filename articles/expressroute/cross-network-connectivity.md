---
title: Łączność między sieci platformy Azure | Dokumentacja firmy Microsoft
description: Ta strona zawiera opis scenariusza aplikacji dla wielu połączeń sieciowych i rozwiązanie oparte na funkcji sieciowych platformy Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 4923c7f2048b7368af6314d5e2288216115bc3bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60368880"
---
# <a name="cross-network-connectivity"></a>Łączność między sieciami

Firma Fabrikam, Inc. ma duże obecności fizycznej i wdrażania platformy Azure w regionie wschodnie stany USA. Firma Fabrikam ma zaplecza łączność między jej w środowisku lokalnym i wdrożeń platformy Azure za pośrednictwem usługi ExpressRoute. Podobnie Contoso Ltd. ma obecności i wdrażania platformy Azure w regionie zachodnie stany USA. Firma Contoso ma wewnętrznej łączności między jej w środowisku lokalnym i wdrożeń platformy Azure za pośrednictwem usługi ExpressRoute.  

Fabrikam Inc. acquires Contoso Ltd. Po połączeniu firma Fabrikam chce łączenia sieci. Na poniższym rysunku przedstawiono scenariusz:

 [![1]][1]

Kreskowane strzałki w środku rysunku powyżej wskazują połączeń wybraną sieć. Istnieją trzy typy żądanego połączenia krzyżowego: Łączenie z firmy Fabrikam i sieci firmy Contoso między 1), 2) między regionalnych magazynami lokalnymi i sieciami wirtualnymi dla wielu łączy (który, łączenie z firmy Fabrikam w sieci lokalnej z siecią wirtualną firmy Contoso i łączenie Contoso między siecią lokalną z siecią wirtualną firmy Fabrikam) i (3) firmy Fabrikam, jak i firmy Contoso Połącz z siecią lokalną między. 

W poniższej tabeli przedstawiono prywatną komunikację równorzędną ExpressRoute Contoso Ltd. przed połączeniem tabeli tras.

[![2]][2]

W poniższej tabeli przedstawiono obowiązujące trasy maszyny Wirtualnej w ramach subskrypcji firmy Contoso, przed połączeniem. Na tabelę maszyn wirtualnych w sieci wirtualnej ma informacje o przestrzeni adresowej sieci wirtualnej i sieci lokalnej firmy Contoso, oprócz programów domyślnych. 

[![4]][4]

W poniższej tabeli przedstawiono prywatną komunikację równorzędną ExpressRoute Fabrikam Inc. przed połączeniem tabeli tras.

[![3]][3]

W poniższej tabeli przedstawiono obowiązujące trasy maszyny Wirtualnej w ramach subskrypcji firmy Fabrikam, przed połączeniem. Dla tabeli maszyn wirtualnych w sieci wirtualnej ma informacje o przestrzeni adresowej sieci wirtualnej i sieci lokalnej firmy Fabrikam, oprócz programów domyślnych.

[![5]][5]

W tym artykule Omówmy krok po kroku i omawiać sposób osiągnięcia żądanego połączenia krzyżowego za pomocą następujących funkcji sieci platformy Azure:

* [Wirtualne sieci równorzędne][Virtual network peering] 
* [Połączenia ExpressRoute w sieci wirtualnej][connection]
* [Global Reach][Global Reach] 

## <a name="cross-connecting-vnets"></a>Krzyżowe, łączenia sieci wirtualnych

Komunikacja równorzędna sieci wirtualnych (komunikacja równorzędna sieci wirtualnych) zapewnia najbardziej optymalną i najwyższą wydajność sieci podczas łączenia dwóch sieci wirtualnych. Komunikacja równorzędna sieci wirtualnych obsługuje komunikacji równorzędnej dwie sieci wirtualne w obrębie tego samego regionu platformy Azure (często nazywany komunikacja równorzędna sieci wirtualnych) i w dwóch różnych regionach platformy Azure (często nazywany globalnych wirtualnych sieci równorzędnych). 

Skonfigurujmy globalne wirtualne sieci równorzędne sieci wirtualne w subskrypcjach Contoso i Fabrikam Azure. Jak utworzyć sieć wirtualną, komunikacja równorzędna między dwiema sieciami wirtualnymi, zobacz [Tworzenie wirtualnych sieci równorzędnych] [ Configure VNet peering] artykułu.

Na poniższej ilustracji przedstawiono architekturę sieci po skonfigurowaniu globalnych wirtualnych sieci równorzędnych.

[![6]][6]

W poniższej tabeli przedstawiono trasy do subskrypcji Contoso maszyny Wirtualnej. Należy zwrócić uwagę na ostatni wpis w tabeli. Ten wpis jest wynikiem wielu łączenie sieci wirtualnych.

[![7]][7]

W poniższej tabeli przedstawiono trasy do subskrypcji firmy Fabrikam maszyny Wirtualnej. Należy zwrócić uwagę na ostatni wpis w tabeli. Ten wpis jest wynikiem wielu łączenie sieci wirtualnych.

[![8]][8]

Bezpośrednio wirtualne sieci równorzędne łączą dwie sieci wirtualne (patrz nie ma żadnych następny przeskok dla *VNetGlobalPeering* wpisu w powyższych dwóch tabel)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Między sieciami wirtualnymi nawiązywania połączenia z sieciami lokalnymi

Można nawiązać połączenia obwodu usługi ExpressRoute wielu sieci wirtualnych. Zobacz [limity usług i subskrypcji] [ Subscription limits] maksymalnej liczby sieci wirtualnych, które mogą być połączone z obwodem usługi ExpressRoute. 

Połącz obwodu usługi ExpressRoute firma Fabrikam Contoso subskrypcji sieci wirtualnej i podobnie obwód usługi ExpressRoute firma Contoso z firmy Fabrikam subskrypcji sieci wirtualnej, aby włączyć łączność między między sieciami wirtualnymi i sieciami lokalnymi. Aby połączyć sieć wirtualną z obwodem usługi ExpressRoute w innej subskrypcji, musimy tworzenie i używanie autoryzacji.  Zobacz artykuł: [Łączenie sieci wirtualnej z obwodem usługi ExpressRoute][Connect-ER-VNet].

Na poniższej ilustracji przedstawiono architekturę sieci po skonfigurowaniu usługi ExpressRoute między łączności z sieciami wirtualnymi.

[![9]][9]

W poniższej tabeli przedstawiono tabeli tras prywatnej komunikacji równorzędnej z usługi ExpressRoute firma Contoso Ltd. po między sieciami wirtualnymi nawiązywania połączenia z sieciami lokalnymi za pośrednictwem usługi ExpressRoute. Przekonać się, że tabela tras zawiera trasy, należący do sieci wirtualnych.

[![10]][10]

W poniższej tabeli przedstawiono tabeli tras prywatnej komunikacji równorzędnej usługi ExpressRoute firma Fabrikam Inc. po między sieciami wirtualnymi nawiązywania połączenia z sieciami lokalnymi za pośrednictwem usługi ExpressRoute. Przekonać się, że tabela tras zawiera trasy, należący do sieci wirtualnych.

[![11]][11]

W poniższej tabeli przedstawiono trasy do subskrypcji Contoso maszyny Wirtualnej. Należy zwrócić uwagę na *bramy sieci wirtualnej* wpisów w tabeli. Maszyna wirtualna będzie widział tras dla sieci lokalnych.

[![12]][12]

W poniższej tabeli przedstawiono trasy do subskrypcji firmy Fabrikam maszyny Wirtualnej. Należy zwrócić uwagę na *bramy sieci wirtualnej* wpisów w tabeli. Maszyna wirtualna będzie widział tras dla sieci lokalnych.

[![13]][13]

>[!NOTE]
>W jednej subskrypcji firmy Fabrikam i/lub Contoso, które mogą też istnieć sieci wirtualne będące szprychami odpowiednich piastą (projekt gwiazdy nie zilustrowano diagramów architektury, w tym artykule). Między połączenia między bramami sieci wirtualnej koncentratora do usługi ExpressRoute będzie również umożliwiać komunikację między koncentratory Wschód i zachód i szprychy.
>

## <a name="cross-connecting-on-premises-networks"></a>Krzyżowe łączenia sieci lokalnej

Zasięgu globalnym usługi ExpressRoute zapewnia łączność między sieciami lokalnymi, które są podłączone do różnych obwodów usługi ExpressRoute. Skonfigurujmy zasięgu globalnym między obwodów Contoso i Fabrikam usługi ExpressRoute. Ponieważ obwodów usługi ExpressRoute znajdują się w różnych subskrypcjach, musimy utworzyć i używać autoryzacji. Zobacz [Konfigurowanie usługi ExpressRoute zasięgu globalnym] [ Configure Global Reach] artykuł, aby uzyskać wskazówki krok po kroku.

Na poniższej ilustracji przedstawiono architekturę sieci po skonfigurowaniu zasięgu globalnym.

[![14]][14]

W poniższej tabeli przedstawiono tabeli tras prywatnej komunikacji równorzędnej z usługi ExpressRoute firma Contoso Ltd. po skonfigurowaniu zasięgu globalnym. Zobaczyć, że tabela tras ma trasy należących do sieci lokalnej. 

[![15]][15]

W poniższej tabeli przedstawiono tabeli tras prywatnej komunikacji równorzędnej usługi ExpressRoute firma Fabrikam Inc., po skonfigurowaniu zasięgu globalnym. Zobaczyć, że tabela tras ma trasy należących do sieci lokalnej.

[![16]][16]

## <a name="next-steps"></a>Kolejne kroki

Zobacz [sieć wirtualna — często zadawane pytania][VNet-FAQ], w przypadku dalszych pytań dotyczących sieci wirtualnej i komunikacja równorzędna sieci wirtualnych. Zobacz [ExpressRoute — często zadawane pytania] [ ER-FAQ] dla połączenia sieciowego dalsze pytania w ramach usługi ExpressRoute i wirtualnych.

Globalny zasięg, jest wdrażana na podstawie kraju, kraju. Aby zobaczyć, czy zasięgu globalnym jest dostępna w krajach, które mają, zobacz [zasięgu globalnym ExpressRoute][Global Reach].

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "scenariusza aplikacji"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "tabeli tras usługi ExpressRoute firma Contoso przed połączeniem"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "tabeli tras usługi ExpressRoute firma Fabrikam przed połączeniem"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "maszyn wirtualnych firmy Contoso kieruje przed połączeniem"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "maszyn wirtualnych firmy Fabrikam kieruje przed połączeniem"
[6]: ./media/cross-network-connectivity/vnet-peering.png "architektury po komunikacja równorzędna sieci wirtualnych"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "maszyn wirtualnych firmy Contoso kieruje po komunikacja równorzędna sieci wirtualnych"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "maszyn wirtualnych firmy Fabrikam kieruje po komunikacja równorzędna sieci wirtualnych"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "architektury po ExpressRoutes obejmujące wiele połączeń"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "tabeli tras usługi ExpressRoute firma Contoso po krzyżowe łączące ExR i sieciami wirtualnymi"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "tabeli tras usługi ExpressRoute firma Fabrikam po wielu łączące ExR i sieciami wirtualnymi"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "trasy maszyn wirtualnych firmy Contoso po wielu łączące ExR i sieciami wirtualnymi"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "trasy maszyn wirtualnych firmy Fabrikam po wielu łączące ExR i sieciami wirtualnymi"
[14]: ./media/cross-network-connectivity/globalreach.png "architektury po skonfigurowaniu zasięgu globalnym"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "tabeli tras usługi ExpressRoute firma Contoso po zasięgu globalnym"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "tabeli tras usługi ExpressRoute firma Fabrikam po zasięgu globalnym"

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq