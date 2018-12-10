---
title: 'Wymagania dotyczące technologii QoS - usługi ExpressRoute: Azure | Dokumentacja firmy Microsoft'
description: Ta strona zawiera szczegółowe wymagania dotyczące konfigurowania i zarządzania nimi QoS. Omówiono Skype dla firm/rejestr usług.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: cb9acb5434fad6cd7898be14404c5ed4125ab96a
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141173"
---
# <a name="expressroute-qos-requirements"></a>Wymagania dotyczące technologii QoS w usłudze ExpressRoute
Program Skype dla firm obejmuje różne obciążenia, które wymagają zróżnicowanej obsługi w technologii QoS. Jeśli planujesz korzystać z usług głosowych za pośrednictwem usługi ExpressRoute, musisz spełnić opisane poniżej wymagania.

![](./media/expressroute-qos/expressroute-qos.png)

> [!NOTE]
> Wymagania dotyczące technologii QoS dotyczą tylko komunikacji równorzędnej firmy Microsoft. Wartości DSCP w ruchu sieciowym otrzymane w publicznej komunikacji równorzędnej Azure i prywatnej komunikacji równorzędnej Azure zostaną zresetowane do wartości 0. 
> 
> 

Poniższa tabela zawiera listę oznaczeń DSCP używanych przez program Skype dla firm. Więcej informacji znajduje się w artykule [Managing QoS for Skype for Business](https://technet.microsoft.com/library/gg405409.aspx) (Zarządzanie technologią QoS na potrzeby programu Skype dla firm).

| **Klasa ruchu** | **Obsługa (oznaczanie DSCP)** | **Obciążenia programu Skype dla firm** |
| --- | --- | --- |
| **Połączenia głosowe** |EF (46) |Połączenia głosowe Skype/Lync |
| **Interaktywne** |AF41 (34) |Wideo, VBSS |
| AF21 (18) |Współdzielenie aplikacji | |
| **Domyślne** |AF11 (10) |Transfer plików |
| CS0 (0) |Inne | |

* Sklasyfikuj obciążenia i zaznacz odpowiednie wartości DSCP. Postępuj zgodnie ze wskazówkami podanymi [tutaj](https://technet.microsoft.com/library/gg405409.aspx) dotyczącymi ustawiania oznaczeń DSCP w sieci.
* Skonfiguruj i obsługuj wiele kolejek w technologii QoS w sieci. Połączenia głosowe muszą być klasą autonomiczną i być obsługiwane w sposób określony w RFC 3246. 
* Możesz wybrać mechanizm kolejkowania, zasady wykrywania przeciążenia oraz alokację przepustowości na klasę ruchu. Oznaczanie DSCP dla obciążeń programu Skype dla firm musi jednak zostać zachowane. Jeśli używasz oznaczenia DSCP niewymienionego powyżej, np. AF31 (26), musisz zmodyfikować tę wartość DSCP do 0 przed wysłaniem pakietu do firmy Microsoft. Firma Microsoft wysyła tylko pakiety oznaczone wartościami DSCP pokazanymi w powyższej tabeli. 

## <a name="next-steps"></a>Kolejne kroki
* Zapoznaj się z wymaganiami dotyczącymi [routingu](expressroute-routing.md) i [translatora adresów sieciowych](expressroute-nat.md).
* Użyj następujących linków przydatnych podczas konfigurowania połączenia za pomocą usługi ExpressRoute.
  
  * [Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)](expressroute-howto-circuit-classic.md)
  * [Configure routing (Konfigurowanie routingu)](expressroute-howto-routing-classic.md)
  * [Link a VNet to an ExpressRoute circuit (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute)](expressroute-howto-linkvnet-classic.md)

