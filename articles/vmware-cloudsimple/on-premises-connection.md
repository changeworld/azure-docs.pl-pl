---
title: Rozwiązanie VMware platformy Azure przez CloudSimple — połączenie lokalne przy użyciu ExpressRoute
description: Opisuje sposób żądania połączenia lokalnego przy użyciu usługi ExpressRoute z sieci regionu CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c06cd53e408ebcae24de487fe1d4d25e14aae11b
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240709"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>Nawiązywanie połączenia z poziomu lokalnego z CloudSimple za pomocą ExpressRoute

Jeśli masz już połączenie z usługą Azure ExpressRoute z lokalizacji zewnętrznej (na przykład lokalnie) na platformie Azure, możesz połączyć ją ze środowiskiem CloudSimple. Można to zrobić za pomocą funkcji platformy Azure, która umożliwia łączenie się ze sobą przy użyciu dwóch obwodów usługi ExpressRoute. Ta metoda służy do ustanawiania bezpiecznego, prywatnego, wysokiej przepustowości i małych opóźnień między dwoma środowiskami.

[![Połączenie lokalne ExpressRoute — Global Reach](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do ustanowienia połączenia Global Reach z lokalnego programu jest wymagany blok adresów sieciowych ( **/29** ).  Przestrzeń adresowa/29 jest używana dla sieci tranzytowej między obwodami usługi ExpressRoute.  Sieć tranzytowa nie powinna nakładać się na żadne sieci wirtualne platformy Azure, sieci lokalne ani CloudSimple sieci chmur prywatnych.

## <a name="prerequisites"></a>Wymagania wstępne

* Obwód usługi Azure ExpressRoute jest wymagany, aby można było nawiązać połączenie między obwodem a sieciami chmur prywatnych CloudSimple.
* Użytkownik musi mieć uprawnienia, aby utworzyć klucze autoryzacji w obwodzie ExpressRoute.

## <a name="scenarios"></a>Scenariusze

Połączenie sieci lokalnej z siecią prywatną w chmurze umożliwia korzystanie z chmury prywatnej na różne sposoby, w tym następujące:

* Uzyskaj dostęp do sieci prywatnej chmury bez tworzenia połączenia sieci VPN typu lokacja-lokacja.
* Użyj lokalnego Active Directory jako źródła tożsamości w chmurze prywatnej.
* Migrowanie maszyn wirtualnych działających lokalnie do chmury prywatnej.
* Korzystaj z chmury prywatnej w ramach rozwiązania do odzyskiwania po awarii.
* Korzystaj z zasobów lokalnych na maszynach wirtualnych obciążeń chmury prywatnej.

## <a name="connecting-expressroute-circuits"></a>Łączenie obwodów usługi ExpressRoute

Aby nawiązać połączenie z usługą ExpressRoute, należy utworzyć autoryzację w obwodzie usługi ExpressRoute i podać informacje o autoryzacji do CloudSimple.

### <a name="create-expressroute-authorization"></a>Utwórz autoryzację ExpressRoute

1. Zaloguj się do Portalu Azure.

2. Na górnym pasku wyszukiwania Wyszukaj pozycję **obwód usługi ExpressRoute** , a następnie kliknij pozycję **ExpressRoute obwody** w obszarze **usługi**.
    [![Obwody usługi ExpressRoute](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Wybierz obwód ExpressRoute, który ma zostać połączony z siecią CloudSimple.

4. Na stronie ExpressRoute kliknij pozycję **autoryzacje**, wprowadź nazwę autoryzacji, a następnie kliknij przycisk **Zapisz**.
    [![Autoryzacja obwodu ExpressRoute](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Skopiuj identyfikator zasobu i klucz autoryzacji, klikając ikonę kopiowania. Wklej identyfikator i klucz do pliku tekstowego.
    [![Kopia autoryzacji obwodu ExpressRoute](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **Identyfikator zasobu** musi być kopiowany z interfejsu użytkownika i powinien być w formacie ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` , gdy podajesz go do obsługi.

6. Zastąp bilet z <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">obsługą</a> połączenia, które ma zostać utworzone.
    * Typ problemu: **Naukow**
    * Subskrypcja: **Subskrypcja, w której wdrożono usługę CloudSimple**
    * Usługa: **Rozwiązanie VMware według CloudSimple**
    * Typ problemu: **Żądanie obsługi**
    * Podtyp problemu: **Utwórz połączenie ExpressRoute z lokalną usługą**
    * Podaj identyfikator zasobu i klucz autoryzacji, które zostały skopiowane i zapisane w okienku szczegółów.
    * Podaj/29 przestrzeń adresową sieci dla sieci tranzytowej.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o usłudze Azure Network Connections](cloudsimple-azure-network-connection.md)  
