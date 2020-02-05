---
title: Azure VMware Solutions (Automatyczna synchronizacja) — połączenie lokalne przy użyciu ExpressRoute
description: Opisuje, jak zażądać połączenia lokalnego przy użyciu usługi ExpressRoute z sieci regionu z możliwością automatycznej synchronizacji
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 10a21faf2790b4c7a26d80e46bf44c8bffabf27f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019625"
---
# <a name="connect-from-on-premises-to-avs-using-expressroute"></a>Łączenie się z lokalnego programu w celu automatycznej synchronizacji przy użyciu ExpressRoute

Jeśli masz już połączenie z usługą Azure ExpressRoute z lokalizacji zewnętrznej (na przykład lokalnie) na platformie Azure, możesz połączyć ją ze środowiskiem automatycznej synchronizacji. Można to zrobić za pomocą funkcji platformy Azure, która umożliwia łączenie się ze sobą przy użyciu dwóch obwodów usługi ExpressRoute. Ta metoda służy do ustanawiania bezpiecznego, prywatnego, wysokiej przepustowości i małych opóźnień między dwoma środowiskami.

[![lokalne połączenie ExpressRoute — Global Reach](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do ustanowienia połączenia Global Reach z lokalnego programu jest wymagany blok adresów sieciowych ( **/29** ). Przestrzeń adresowa/29 jest używana dla sieci tranzytowej między obwodami usługi ExpressRoute. Sieć tranzytowa nie powinna nakładać się na żadne sieci wirtualne platformy Azure, sieci lokalne ani automatyczna synchronizacja sieci chmur prywatnych.

## <a name="prerequisites"></a>Wymagania wstępne

* Obwód usługi Azure ExpressRoute jest wymagany przed nawiązaniem połączenia między sieciami obwodu i chmurą prywatną w chmurze.
* Użytkownik musi mieć uprawnienia, aby utworzyć klucze autoryzacji w obwodzie ExpressRoute.

## <a name="scenarios"></a>Scenariusze

Połączenie sieci lokalnej z Twoją siecią prywatną w chmurze umożliwia korzystanie z chmury prywatnej do automatycznej synchronizacji na różne sposoby, w tym w następujących scenariuszach:

* Uzyskaj dostęp do swojej sieci prywatnej chmury do automatycznej synchronizacji bez tworzenia połączenia sieci VPN typu lokacja-lokacja.
* Użyj lokalnego Active Directory jako źródła tożsamości w chmurze prywatnej automatycznej synchronizacji.
* Migrowanie maszyn wirtualnych działających lokalnie do chmury prywatnej automatycznej synchronizacji.
* Korzystaj z chmury prywatnej do automatycznej synchronizacji w ramach rozwiązania do odzyskiwania po awarii.
* Korzystaj z zasobów lokalnych na maszynach wirtualnych obciążeń związanych z chmurą prywatną w chmurze.

## <a name="connecting-expressroute-circuits"></a>Łączenie obwodów usługi ExpressRoute

Aby nawiązać połączenie z usługą ExpressRoute, należy utworzyć autoryzację w obwodzie usługi ExpressRoute i podać informacje o autoryzacji do automatycznej synchronizacji.


### <a name="create-expressroute-authorization"></a>Utwórz autoryzację ExpressRoute

1. Zaloguj się do Portalu Azure.

2. Na górnym pasku wyszukiwania Wyszukaj pozycję **obwód usługi ExpressRoute** , a następnie kliknij pozycję **ExpressRoute obwody** w obszarze **usługi**.
    [![obwody usługi ExpressRoute](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Wybierz obwód ExpressRoute, który ma zostać połączony z siecią automatycznej synchronizacji.

4. Na stronie ExpressRoute kliknij pozycję **autoryzacje**, wprowadź nazwę autoryzacji, a następnie kliknij przycisk **Zapisz**.
    [![autoryzacja obwodu ExpressRoute](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Skopiuj identyfikator zasobu i klucz autoryzacji, klikając ikonę kopiowania. Wklej identyfikator i klucz do pliku tekstowego.
    [![kopia autoryzacji obwodu ExpressRoute](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **Identyfikator zasobu** musi być kopiowany z interfejsu użytkownika i powinien mieć format ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>```, gdy podajesz go do obsługi.

6. Zastąp bilet z <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">obsługą</a> połączenia, które ma zostać utworzone.
    * Typ problemu: **techniczne**
    * Subskrypcja: **subskrypcja, w której jest wdrażana usługa automatycznej synchronizacji**
    * Usługa: **rozwiązania VMware (Automatyczna synchronizacja)**
    * Typ problemu: **żądanie obsługi**
    * Podtyp problemu: **Utwórz połączenie ExpressRoute do lokalnego**
    * Podaj identyfikator zasobu i klucz autoryzacji, które zostały skopiowane i zapisane w okienku szczegółów.
    * Podaj/29 przestrzeń adresową sieci dla sieci tranzytowej.
    * Czy wysyłasz domyślną trasę za poorednictwem ExpressRoute?
    * Czy ruch w chmurze do automatycznej synchronizacji używa trasy domyślnej wysyłanej za pomocą ExpressRoute?

    > [!IMPORTANT]
    > Wysyłanie trasy domyślnej pozwala wysyłać cały ruch internetowy z chmury prywatnej do automatycznej synchronizacji przy użyciu lokalnego połączenia internetowego. Aby wyłączyć domyślną trasę skonfigurowaną w chmurze prywatnej automatycznej synchronizacji i korzystać z domyślnej trasy połączenia lokalnego, podaj szczegóły w bilet pomocy technicznej.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o usłudze Azure Network Connections](cloudsimple-azure-network-connection.md)  
