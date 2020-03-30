---
title: 'Usługa Azure ExpressRoute: wymagania dotyczące usługi QoS'
description: Ta strona zawiera szczegółowe wymagania dotyczące konfigurowania qoS i zarządzania nim. Omówione są usługi programu Skype dla firm/usługi głosowe.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: cherylmc
ms.openlocfilehash: debc5d91478d0a5c3cc16c7b09f5713ba09b467e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74080096"
---
# <a name="expressroute-qos-requirements"></a>Wymagania dotyczące technologii QoS w usłudze ExpressRoute
Program Skype dla firm obejmuje różne obciążenia, które wymagają zróżnicowanej obsługi w technologii QoS. Jeśli planujesz korzystać z usług głosowych za pośrednictwem usługi ExpressRoute, musisz spełnić opisane poniżej wymagania.

![](./media/expressroute-qos/expressroute-qos.png)

> [!NOTE]
> Wymagania dotyczące technologii QoS dotyczą tylko komunikacji równorzędnej firmy Microsoft. Wartości DSCP w ruchu sieciowym otrzymane w publicznej komunikacji równorzędnej Azure i prywatnej komunikacji równorzędnej Azure zostaną zresetowane do wartości 0. 
> 
> 

Poniższa tabela zawiera listę oznaczeń DSCP używanych przez usługi Microsoft Teams i skype dla firm. Więcej informacji znajduje się w artykule [Managing QoS for Skype for Business](https://docs.microsoft.com/SkypeForBusiness/manage/network-management/qos/managing-quality-of-service-QoS) (Zarządzanie technologią QoS na potrzeby programu Skype dla firm).

| **Klasa ruchu** | **Obsługa (oznaczanie DSCP)** | **Microsoft Teams i skype dla firm obciążenia** |
| --- | --- | --- |
| **Połączenia głosowe** |EF (46) |Skype / Microsoft Teams / Lync głos |
| **Interaktywne** |AF41 (34) |Wideo, VBSS |
| |AF21 (18) |Współdzielenie aplikacji | 
| **Domyślny** |AF11 (10) |Transfer plików |
| |CS0 (0) |Inne |

* Sklasyfikuj obciążenia i zaznacz odpowiednie wartości DSCP. Postępuj zgodnie ze wskazówkami podanymi [tutaj](https://docs.microsoft.com/SkypeForBusiness/manage/network-management/qos/configuring-port-ranges-for-your-skype-clients#configure-quality-of-service-policies-for-clients-running-on-windows-10) dotyczącymi ustawiania oznaczeń DSCP w sieci.
* Skonfiguruj i obsługuj wiele kolejek w technologii QoS w sieci. Voice musi być klasą samodzielną i otrzymać leczenie EF określone w [RFC 3246](https://www.ietf.org/rfc/rfc3246.txt). 
* Możesz wybrać mechanizm kolejkowania, zasady wykrywania przeciążenia oraz alokację przepustowości na klasę ruchu. Oznaczanie DSCP dla obciążeń programu Skype dla firm musi jednak zostać zachowane. Jeśli używasz oznaczenia DSCP niewymienionego powyżej, np. AF31 (26), musisz zmodyfikować tę wartość DSCP do 0 przed wysłaniem pakietu do firmy Microsoft. Firma Microsoft wysyła tylko pakiety oznaczone wartościami DSCP pokazanymi w powyższej tabeli. 

## <a name="next-steps"></a>Następne kroki
* Zapoznaj się z wymaganiami dotyczącymi [routingu](expressroute-routing.md) i [translatora adresów sieciowych](expressroute-nat.md).
* Użyj następujących linków przydatnych podczas konfigurowania połączenia za pomocą usługi ExpressRoute.
  
  * [Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)](expressroute-howto-circuit-classic.md)
  * [Configure routing (Konfigurowanie routingu)](expressroute-howto-routing-classic.md)
  * [Link a VNet to an ExpressRoute circuit (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute)](expressroute-howto-linkvnet-classic.md)

