---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/16/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8c82e3d2c978a7980d02bc686346acdcb21dd591
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "66425765"
---
|  | **Punkt-lokacja** | **Lokacja na miejscu** | **Expressroute** |
| --- | --- | --- | --- |
| **Usługi obsługiwane przez platformę Azure** |Usługi Cloud Services i Virtual Machines |Usługi Cloud Services i Virtual Machines |[Lista usług](../articles/expressroute/expressroute-faqs.md#supported-services) |
| **Typowe przepustowości** |Oparte na jednostce SKU bramy |Zwykle < 1 Gb/s łącznie |50 Mb/s, 100 Mb/s, 200 Mb/s, 500 Mb/s, 1 Gb/s, 2 Gb/s, 5 Gb/s, 10 Gb/s |
| **Obsługiwane protokoły** |Protokół SSTP (Secure Sockets Tunneling Protocol), OpenVPN i IPsec |IPsec |Bezpośrednie połączenie za pośrednictwem sieci VLAN, technologie VPN NSP (MPLS, VPLS itp.) |
| **Routing** |RouteBased (dynamiczny) |Firma Microsoft obsługuje routing typu PolicyBased (statyczny) i RouteBased (dynamiczny VPN) |BGP |
| **Odporność połączenia** |aktywne/pasywne |aktywne/pasywne lub aktywne/aktywne |aktywne/aktywne |
| **Typowy przypadek użycia** |Tworzenie prototypów, scenariusze laboratorium do tworzenia i testowania dla usług w chmurze i maszyn wirtualnych |Scenariusze laboratorium do tworzenia i testowania oraz obciążenia produkcyjne o małej skali dla usług w chmurze i maszyn wirtualnych |Dostęp do wszystkich usług Azure (zatwierdzona lista), obciążenia o znaczeniu krytycznym oraz klasy korporacyjnej, Backup, dane big data, platforma Azure jako lokacja DR |
| **Umowa SLA** |[Umowa SLA](https://azure.microsoft.com/support/legal/sla/) |[Umowa SLA](https://azure.microsoft.com/support/legal/sla/) |[Umowa SLA](https://azure.microsoft.com/support/legal/sla/) |
| **Cennik** |[Cennik](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Cennik](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Cennik](https://azure.microsoft.com/pricing/details/expressroute/) |
| **Dokumentacja techniczna** |[Dokumentacja bramy sieci VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Dokumentacja bramy sieci VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Dokumentacja usługi ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) |
| **Najczęściej zadawane pytania** |[Brama sieci VPN — często zadawane pytania](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Brama sieci VPN — często zadawane pytania](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Usługa ExpressRoute — często zadawane pytania](../articles/expressroute/expressroute-faqs.md) |
