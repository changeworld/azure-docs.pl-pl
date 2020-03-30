---
title: Rozwiązanie azure VMware by CloudSimple — połączenie lokalne przy użyciu usługi ExpressRoute
description: W tym artykule opisano sposób żądania połączenia lokalnego przy użyciu usługi ExpressRoute z sieci regionu CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0dd5ede110255b6e53bbc397e683e66b3beffc65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019625"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>Łączenie się z lokalnego do CloudSimple przy użyciu usługi ExpressRoute

Jeśli masz już połączenie Usługi Azure ExpressRoute z lokalizacji zewnętrznej (takiej jak lokalna) do platformy Azure, możesz połączyć je ze środowiskiem CloudSimple. Można to zrobić za pomocą funkcji platformy Azure, która umożliwia dwa obwody usługi ExpressRoute połączyć się ze sobą. Ta metoda ustanawia bezpieczne, prywatne, wysokiej przepustowości, małe opóźnienia połączenia między dwoma środowiskami.

[![Lokalne połączenie usługi ExpressRoute — globalny zasięg](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do nawiązywania połączenia globalnego zasięgu z lokalnego wymagane jest utworzenie bloku adresów sieciowych **/29.**  Przestrzeń adresowa /29 jest używana do przesyłania sieci między obwodami usługi ExpressRoute.  Sieć tranzytowa nie powinna pokrywać się z żadną siecią wirtualną platformy Azure, sieciami lokalnymi ani sieciami cloudsimple private cloud.

## <a name="prerequisites"></a>Wymagania wstępne

* Obwód usługi Azure ExpressRoute jest wymagany przed nawiązaniem połączenia między obwodem a sieciami cloudsimple private cloud.
* Użytkownik jest wymagany z uprawnieniami do tworzenia kluczy autoryzacji na obwodzie usługi ExpressRoute.

## <a name="scenarios"></a>Scenariusze

Połączenie sieci lokalnej z siecią Private Cloud umożliwia korzystanie z chmury prywatnej na różne sposoby, w tym w następujących scenariuszach:

* Uzyskaj dostęp do sieci private cloud bez tworzenia połączenia sieci VPN między lokacjami.
* Użyj lokalnej usługi Active Directory jako źródła tożsamości w chmurze prywatnej.
* Migrowanie maszyn wirtualnych z systemem lokalnym do chmury prywatnej.
* Korzystaj z chmury prywatnej jako części rozwiązania do odzyskiwania po awarii.
* Korzystaj z zasobów lokalnych na maszynach wirtualnych obciążenia chmury prywatnej.

## <a name="connecting-expressroute-circuits"></a>Podłączanie obwodów usługi ExpressRoute

Aby ustanowić połączenie Usługi ExpressRoute, należy utworzyć autoryzację na obwodzie usługi ExpressRoute i podać informacje o autoryzacji cloudsimple.


### <a name="create-expressroute-authorization"></a>Tworzenie autoryzacji usługi ExpressRoute

1. Zaloguj się do Portalu Azure.

2. Na górnym pasku wyszukiwania wyszukaj **obwód usługi ExpressRoute** i kliknij pozycję **Obwód usługi ExpressRoute** w obszarze **Usługi**.
    [![Obwody usługi ExpressRoute](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Wybierz obwód usługi ExpressRoute, który ma zostać łączony z siecią CloudSimple.

4. Na stronie ExpressRoute kliknij pozycję **Autoryzacje**, wprowadź nazwę autoryzacji i kliknij przycisk **Zapisz**.
    [![Autoryzacja obwodu usługi ExpressRoute](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Skopiuj identyfikator zasobu i klucz autoryzacji, klikając ikonę kopiowania. Wklej identyfikator i wpisanie klucza do pliku tekstowego.
    [![Kopia autoryzacji obwodu usługi ExpressRoute](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **Identyfikator zasobu** musi być skopiowany z interfejsu użytkownika ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` i powinien być w formacie po podaniu go do obsługi.

6. Złóż bilet z <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">obsługą</a> połączenia, które ma zostać utworzone.
    * Typ problemu: **Techniczne**
    * Subskrypcja: **subskrypcja, w której wdrożono usługę CloudSimple**
    * Usługa: **Rozwiązanie VMware by CloudSimple**
    * Typ problemu: **Żądanie usługi**
    * Podtyp problemu: **Tworzenie połączenia usługi ExpressRoute z lokalnymi**
    * Podaj identyfikator zasobu i klucz autoryzacji skopiowany i zapisany w okienku szczegółów.
    * Zapewnij /29 przestrzeń adresową sieci dla sieci tranzytowej.
    * Czy wysyłasz trasę domyślną za pośrednictwem usługi ExpressRoute?
    * Czy ruch w chmurze prywatnej powinien używać domyślnej trasy wysyłanej za pośrednictwem usługi ExpressRoute?

    > [!IMPORTANT]
    > Wysyłanie trasy domyślnej umożliwia wysyłanie całego ruchu internetowego z chmury prywatnej przy użyciu lokalnego połączenia internetowego.  Aby wyłączyć trasę domyślną skonfigurowaną w chmurze prywatnej i użyć domyślnej trasy połączenia lokalnego, podaj szczegóły w bilecie pomocy technicznej.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o połączeniach sieciowych platformy Azure](cloudsimple-azure-network-connection.md)  
