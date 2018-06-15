---
title: Łączenie sieci lokalnej na platformie Azure | Dokumentacja firmy Microsoft
description: Zawiera opis oraz porównanie różnych metod do połączenia usługi w chmurze Microsoft Azure, Office 365 i Dynamics CRM Online.
services: ''
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: ''
ms.assetid: 294d39ad-40e0-476a-a362-57911b1172b7
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2016
ms.author: cherylmc
ms.openlocfilehash: 37d83d3b6dea1763d85f2411816ba2fee4279100
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2018
ms.locfileid: "23851015"
---
# <a name="connecting-your-on-premises-network-to-azure"></a>Łączenie sieci lokalnej na platformie Azure
Firma Microsoft udostępnia kilka typów usług w chmurze. Podczas wszystkich usług można nawiązać za pośrednictwem publicznej sieci Internet, można również nawiązać niektórych usług przy użyciu tunelu wirtualnej sieci prywatnej (VPN) przez Internet lub za pośrednictwem bezpośredniego połączenia z firmą Microsoft. Ten artykuł pomaga określić opcji łączności będzie najlepiej odpowiadać potrzebom użytkownika na podstawie typów usług chmurowych firmy Microsoft, które zostaną zużyte. Większość organizacji korzystanie z wielu typów połączeń opisane poniżej.

## <a name="connecting-over-the-public-internet"></a>Połączenie za pośrednictwem publicznego Internetu
Ten typ połączenia zapewnia dostęp do usług chmurowych firmy Microsoft bezpośrednio przez Internet, jak pokazano poniżej.

![Internet](./media/guidance-connecting-your-on-premises-network-to-azure/internet.png "Internet")

To połączenie jest zwykle pierwszy typ służące do połączenia usługi w chmurze firmy Microsoft. W poniższej tabeli przedstawiono zalet i wad tego typu połączenia.

| **Korzyści** | **Zagadnienia do rozważenia** |
| --- | --- |
| Wymaga żadnych modyfikacji do sieci lokalnej, dopóki wszystkie urządzenia klienta mają nieograniczony dostęp do wszystkich adresów IP i portów w Internecie. |Jeśli ruch jest często szyfrowany przy użyciu protokołu HTTPS, mogą zostać przechwycone w drodze od momentu jego są przesyłane za pośrednictwem publicznej sieci Internet. |
| Może nawiązać połączenie z Internetem publicznego wszystkich usług chmurowych firmy Microsoft. |Nieoczekiwane opóźnienia, ponieważ połączenie jest przesyłany przez Internet. |
| Korzysta z istniejącego połączenia internetowego. | |
| Nie wymaga zarządzania urządzeniami łączności. | |

To połączenie nie ma łączności ani kosztów przepustowości, ponieważ używasz istniejącego połączenia internetowego.

## <a name="connecting-with-a-point-to-site-connection"></a>Łączenie z połączenie punkt lokacja
Ten typ połączenia zapewnia dostęp do niektórych usług chmurowych firmy Microsoft za pośrednictwem tunelu Secure Socket Tunneling Protocol (SSTP) za pośrednictwem Internetu, jak pokazano poniżej.

![P2S](./media/guidance-connecting-your-on-premises-network-to-azure/p2s.png "połączeniapunkt lokacja")

Połączenie jest nawiązywane za pośrednictwem istniejącego połączenia internetowego, ale wymagane jest użycie bramy sieci VPN platformy Azure. W poniższej tabeli przedstawiono zalet i wad tego typu połączenia.

| **Korzyści** | **Zagadnienia do rozważenia** |
| --- | --- |
| Wymaga żadnych modyfikacji do sieci lokalnej, dopóki wszystkie urządzenia klienta mają nieograniczony dostęp do wszystkich adresów IP i portów w Internecie. |Jeśli ruch jest szyfrowany za pomocą protokołu IPSec, mogą zostać przechwycone w drodze od momentu jego są przesyłane za pośrednictwem publicznej sieci Internet. |
| Korzysta z istniejącego połączenia internetowego. |Nieoczekiwane opóźnienia, ponieważ połączenie jest przesyłany przez Internet. |
| Przepływność maksymalnie 200 Mb/s dla każdej bramy. |Wymaga tworzenie i zarządzanie osobne połączenia między urządzeniami w sieci lokalnej i każdej bramy, każde urządzenie musi połączyć się z nimi. |
| Można nawiązać połączenia z usługami Azure, których można podłączyć do sieci wirtualnych Azure (VNet) takie jak maszyny wirtualne platformy Azure i usługi w chmurze Azure. |Wymaga minimalnej administracji trwającą bramy sieci VPN platformy Azure. |
| Nie można nawiązać połączenia z usługi Microsoft Office 365 lub usługi Dynamics CRM Online. | |
| Nie można nawiązać połączenia z usługami Azure, których nie można podłączyć do sieci wirtualnej. | |

Dowiedz się więcej o [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) usługi, jego [cennik](https://azure.microsoft.com/pricing/details/vpn-gateway), a transfer danych wychodzących [cennik](https://azure.microsoft.com/pricing/details/data-transfers).

## <a name="connecting-with-a-site-to-site-connection"></a>Łączenie z połączenia lokacja lokacja
Ten typ połączenia zapewnia dostęp do niektórych usług chmurowych firmy Microsoft za pośrednictwem tunelu IPSec za pośrednictwem Internetu, jak pokazano poniżej.

![S2S](./media/guidance-connecting-your-on-premises-network-to-azure/s2s.png "połączenie lokacja lokacja")

Połączenie jest nawiązywane za pośrednictwem istniejącego połączenia internetowego, ale wymagane jest użycie bramy sieci VPN platformy Azure skojarzone cennik i transfer danych wychodzących cennik. W poniższej tabeli przedstawiono zalet i wad tego typu połączenia.

| **Korzyści** | **Zagadnienia do rozważenia** |
| --- | --- |
| Wszystkie urządzenia w sieci lokalnej mogą komunikować się z usługami Azure podłączone do sieci wirtualnej, a więc nie ma potrzeby konfigurowania poszczególnych połączeń dla każdego urządzenia. |Jeśli ruch jest szyfrowany za pomocą protokołu IPSec, mogą zostać przechwycone w drodze od momentu jego są przesyłane za pośrednictwem publicznej sieci Internet. |
| Korzysta z istniejącego połączenia internetowego. |Nieoczekiwane opóźnienia, ponieważ połączenie jest przesyłany przez Internet. |
| Może służyć do nawiązania połączenia usług platformy Azure, które mogą być podłączone do sieci wirtualnej, takich jak maszyny wirtualne i usługi w chmurze. |Należy skonfigurować i zarządzać zweryfikowanych sieci VPN urządzenia * lokalnymi. |
| Przepływność maksymalnie 200 Mb/s dla każdej bramy. |Wymaga minimalnej administracji trwającą bramy sieci VPN platformy Azure. |
| Można wymusić na ruch wychodzący inicjowane z chmury maszyn wirtualnych za pośrednictwem sieci lokalnej inspekcji i rejestrowanie przy użyciu trasy zdefiniowane przez użytkownika lub protokołu BGP (Border Gateway) **. |Nie można nawiązać połączenia z usługi Microsoft Office 365 lub usługi Dynamics CRM Online. |
| Nie można nawiązać połączenia z usługami Azure, których nie można podłączyć do sieci wirtualnej. | |
| Jeśli korzystasz z usług, które inicjują połączenia do urządzeń lokalnych i wymagają zasady zabezpieczeń, może być konieczne zapory między siecią lokalną a platformą Azure. | |

* * Wyświetlanie listy [zweryfikowane urządzenia sieci VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable).
* ** Dowiedz się więcej o korzystaniu z [trasy zdefiniowane przez użytkownika](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) lub [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) wymusić routingu z sieciami wirtualnymi platformy Azure do lokalnego urządzenia.

## <a name="connecting-with-a-dedicated-private-connection"></a>Łączenie za pomocą dedykowanego połączenia prywatne
Ten typ połączenia zapewnia dostęp do wszystkich usług chmurowych firmy Microsoft za pośrednictwem dedykowanego połączenia prywatne, do firmy Microsoft, które nie przechodzą przez Internet, jak pokazano poniżej.

![ER](./media/guidance-connecting-your-on-premises-network-to-azure/er.png "połączenia ExpressRoute")

Połączenie wymaga korzystania z usługi ExpressRoute i połączenie z dostawcą połączenia. W poniższej tabeli przedstawiono zalet i wad tego typu połączenia.

| **Korzyści** | **Zagadnienia do rozważenia** |
| --- | --- |
| Ruch nie przechwycone podczas przesyłania za pośrednictwem publicznej sieci Internet, ponieważ jest używane dedykowane połączenie za pośrednictwem dostawcy usługi. |Wymaga lokalnego routera zarządzania. |
| Przepustowość do 10 Gb/s dla obwodu usługi expressroute i przepływność maksymalnie 2 Gb/s do każdej bramy. |Wymaga dedykowanego połączenia z dostawcą połączenia. |
| Przewidywalna opóźnienia, ponieważ jest dedykowanego połączenia do firmy Microsoft, które nie przechodzą przez Internet. |Może wymagać minimalnego administracją z co najmniej jedna brama sieci VPN platformy Azure (jeśli obwodu nawiązywania połączenia z sieciami wirtualnymi). |
| Nie wymaga szyfrowaną komunikację, chociaż można zaszyfrować dane, w razie potrzeby. |Jeśli używasz usługi w chmurze, które inicjują połączenia do urządzeń lokalnych, może być konieczne zapory między siecią lokalną a platformą Azure. |
| Mogą nawiązać bezpośrednie wszystkich usług chmurowych firmy Microsoft, z kilkoma wyjątkami *. |Wymaga lokalnego adresów IP wprowadzania centrach danych firmy Microsoft dla usług, które nie może być połączona z VNet.* * translatora adresów sieciowych (NAT) |
| Można wymusić ruch wychodzący inicjowane z chmury maszyn wirtualnych za pośrednictwem sieci lokalnej inspekcji i rejestrowanie przy użyciu protokołu BGP. | |

* * Wyświetlanie [listę usług](../expressroute/expressroute-faqs.md#supported-services) nie można użyć z usługi ExpressRoute. Twoja subskrypcja platformy Azure musi być zatwierdzony do nawiązania połączenia usługi Office 365.  Zobacz [Azure ExpressRoute dla usługi Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd?ui=en-US&rs=en-US&ad=US&fromAR=1) artykułu, aby uzyskać szczegółowe informacje.
* ** Dowiedz się więcej o ExpressRoute [NAT](../expressroute/expressroute-nat.md) wymagania.

Dowiedz się więcej o [ExpressRoute](../expressroute/expressroute-introduction.md), jego skojarzony [cennik](https://azure.microsoft.com/pricing/details/expressroute), i [dostawców łączności](../expressroute/expressroute-locations.md#locations).

## <a name="additional-considerations"></a>Dodatkowe zagadnienia
* Niektóre z powyższych opcji mają różne ogranicza maksymalną można obsługę połączeń, połączenia bramy sieci wirtualnej i innych kryteriów. Zalecane jest przejrzenie Azure [sieci limity](../azure-subscription-service-limits.md#networking-limits) zrozumienie, jeśli ich wpływ na typy łączności chcesz użyć.
* Jeśli planujesz nawiązywania połączenia bramy z połączenia sieci VPN typu lokacja lokacja z tej samej sieci wirtualnej jako bramę usługi ExpressRoute, należy się zapoznać z ważne ograniczenia najpierw. Zobacz [połączeń ExpressRoute skonfigurować i lokacja-lokacja ważnych](../expressroute/expressroute-howto-coexist-resource-manager.md#limits-and-limitations) artykułu, aby uzyskać więcej informacji.

## <a name="next-steps"></a>Kolejne kroki
Zasoby przedstawione poniżej wyjaśnić sposób zaimplementowania typy połączeń omówione w tym artykule.

* [Implementuje połączenie punkt lokacja](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Implementuje połączenie lokacja lokacja](guidance-hybrid-network-vpn.md)
* [Implementowanie dedykowane połączenie prywatne](guidance-hybrid-network-expressroute.md)
* [Implementowanie dedykowanego połączenia prywatnego za pomocą połączenia lokacja lokacja wysokiej dostępności](guidance-hybrid-network-expressroute-vpn-failover.md)
