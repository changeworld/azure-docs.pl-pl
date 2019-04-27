---
title: Łączenie sieci lokalnej z platformą Azure | Dokumentacja firmy Microsoft
description: Zawiera objaśnienie i porównuje różne dostępne metody łączenia usług chmurowych firmy Microsoft, takich jak Azure, Office 365 i Dynamics CRM Online.
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
ms.openlocfilehash: 9361c0b2ee84d7459bd432f5a8ce13fc5fd85ef8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583540"
---
# <a name="connecting-your-on-premises-network-to-azure"></a>Łączenie sieci lokalnej z platformą Azure
Firma Microsoft udostępnia kilka typów usług w chmurze. Natomiast można połączyć do wszystkich usług, za pośrednictwem publicznej sieci Internet, można także nawiązać niektóre z tych usług za pomocą tunelu wirtualnej sieci prywatnej (VPN), za pośrednictwem Internetu lub bezpośredniego połączenia z firmą Microsoft. W tym artykule zawarto informacje ułatwiające określenie opcji łączności, które najlepiej spełniającą Twoje potrzeby, na podstawie typów usług chmurowych firmy Microsoft, które zostaną zużyte. Większość organizacji korzystanie z wielu typów połączeń, które opisano poniżej.

## <a name="connecting-over-the-public-internet"></a>Łączenie za pośrednictwem publicznej sieci Internet
Ten typ połączenia umożliwia dostęp do usług chmurowych firmy Microsoft bezpośrednio przez Internet, jak pokazano poniżej.

![Internet](./media/guidance-connecting-your-on-premises-network-to-azure/internet.png "Internet")

To połączenie jest zwykle pierwszy typ używany do nawiązywania połączenia z usługami chmurowymi firmy Microsoft. W poniższej tabeli wymieniono zalety i wady tego typu połączenia.

| **Korzyści** | **Zagadnienia do rozważenia** |
| --- | --- |
| Wymaga żadnych modyfikacji w sieci lokalnej, tak długo, jak wszystkie urządzenia klienta mają nieograniczony dostęp do wszystkich adresów IP i portów w Internecie. |Chociaż często ruch jest szyfrowany, przy użyciu protokołu HTTPS, mogą zostać przechwycone w drodze od momentu jego przesyłane za pośrednictwem publicznej sieci Internet. |
| Można nawiązać połączenia z usług chmurowych firmy Microsoft dostępne do publicznego Internetu. |Nieprzewidywalne opóźnienia, ponieważ połączenie jest przesyłane przez Internet. |
| Wykorzystuje istniejące połączenie internetowe. | |
| Nie wymaga zarządzania wszystkie urządzenia łączności. | |

To połączenie nie ma łączności lub koszty przepustowości, ponieważ używasz istniejące połączenie internetowe.

## <a name="connecting-with-a-point-to-site-connection"></a>Łączenie za pomocą połączenia typu punkt lokacja
Ten typ połączenia umożliwia dostęp do niektórych usług chmurowych firmy Microsoft za pośrednictwem tunelu Secure Socket Tunneling Protocol (SSTP) przez Internet, jak pokazano poniżej.

![P2S](./media/guidance-connecting-your-on-premises-network-to-azure/p2s.png "punkt lokacja połączenie")

Połączenie jest wykonywane istniejące połączenie internetowe, ale wymagane jest użycie usługi Azure VPN Gateway. W poniższej tabeli wymieniono zalety i wady tego typu połączenia.

| **Korzyści** | **Zagadnienia do rozważenia** |
| --- | --- |
| Wymaga żadnych modyfikacji w sieci lokalnej, tak długo, jak wszystkie urządzenia klienta mają nieograniczony dostęp do wszystkich adresów IP i portów w Internecie. |Jeśli ruch jest szyfrowany za pomocą protokołu IPSec, mogą zostać przechwycone w drodze od momentu jego przesyłane za pośrednictwem publicznej sieci Internet. |
| Wykorzystuje istniejące połączenie internetowe. |Nieprzewidywalne opóźnienia, ponieważ połączenie jest przesyłane przez Internet. |
| Przepustowość do 200 Mb/s na bramę. |Wymaga, tworzenie i zarządzanie oddzielnych połączeń między poszczególne urządzenia w sieci lokalnej i każdej bramy, każde urządzenie musi połączyć się z nimi. |
| Można połączyć się z usług platformy Azure, które mogą być połączone z sieciami wirtualnymi platformy Azure (VNet), takich jak Azure Virtual Machines i usług Azure Cloud Services. |Wymaga minimalnego administracją bramy sieci VPN platformy Azure. |
| Nie można połączyć się z Microsoft Office 365 lub Dynamics CRM Online. | |
| Nie można nawiązać połączenia z usług platformy Azure, które nie mogą być połączone z siecią wirtualną. | |

Dowiedz się więcej o [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) usługi, jego [ceny](https://azure.microsoft.com/pricing/details/vpn-gateway)i transfer danych wychodzących [ceny](https://azure.microsoft.com/pricing/details/data-transfers).

## <a name="connecting-with-a-site-to-site-connection"></a>Łączenie za pomocą połączenia typu lokacja lokacja
Ten typ połączenia umożliwia dostęp do niektórych usług chmurowych firmy Microsoft za pośrednictwem tunelu IPSec przez Internet, jak pokazano poniżej.

![S2S](./media/guidance-connecting-your-on-premises-network-to-azure/s2s.png "połączenia lokacja lokacja")

Połączenie jest wykonywane istniejące połączenie internetowe, ale wymagane jest użycie usługi Azure VPN Gateway za pomocą jego skojarzonego ceny i ceny za transfer danych wychodzących. W poniższej tabeli wymieniono zalety i wady tego typu połączenia.

| **Korzyści** | **Zagadnienia do rozważenia** |
| --- | --- |
| Wszystkie urządzenia w sieci lokalnej może komunikować się z usługami platformy Azure, połączone z siecią wirtualną, dzięki czemu nie ma potrzeby konfigurowania poszczególnych połączeń dla każdego urządzenia. |Jeśli ruch jest szyfrowany za pomocą protokołu IPSec, mogą zostać przechwycone w drodze od momentu jego przesyłane za pośrednictwem publicznej sieci Internet. |
| Wykorzystuje istniejące połączenie internetowe. |Nieprzewidywalne opóźnienia, ponieważ połączenie jest przesyłane przez Internet. |
| Można połączyć się z usług platformy Azure, które mogą być połączone z siecią wirtualną, takie jak maszyny wirtualne i usługi w chmurze. |Należy skonfigurować i zarządzać zweryfikowanych sieci VPN urządzenia * w środowisku lokalnym. |
| Przepustowość do 200 Mb/s na bramę. |Wymaga minimalnego administracją bramy sieci VPN platformy Azure. |
| Można wymusić ruch wychodzący inicjowane z chmury maszyn wirtualnych za pośrednictwem sieci lokalnej inspekcji i rejestrowania przy użyciu tras zdefiniowanych przez użytkownika lub Border Gateway Protocol (BGP) **. |Nie można połączyć się z Microsoft Office 365 lub Dynamics CRM Online. |
| Nie można nawiązać połączenia z usług platformy Azure, które nie mogą być połączone z siecią wirtualną. | |
| Jeśli korzystasz z usług, które inicjują połączenia do lokalnych urządzeń i zasady zabezpieczeń dla tego wymagają, może być konieczne z zaporą między siecią lokalną a platformą Azure. | |

* * Wyświetl listę [zweryfikowanych urządzeń sieci VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable).
* ** Dowiedz się więcej o korzystaniu z [trasy zdefiniowane przez użytkownika](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) lub [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) wymusić routing z sieci wirtualnych platformy Azure do lokalnego urządzenia.

## <a name="connecting-with-a-dedicated-private-connection"></a>Łączenie z dedykowanego połączenia prywatnego
Ten typ połączenia zapewnia dostęp do wszystkich usług w chmurze firmy Microsoft za pośrednictwem specjalnego połączenia prywatnego, do firmy Microsoft, który nie przechodzi przez Internet, jak pokazano poniżej.

![ER](./media/guidance-connecting-your-on-premises-network-to-azure/er.png "połączenia usługi ExpressRoute")

Połączenie wymaga korzystania z usługi ExpressRoute i połączeń z dostawcą łączności. W poniższej tabeli wymieniono zalety i wady tego typu połączenia.

| **Korzyści** | **Zagadnienia do rozważenia** |
| --- | --- |
| Nie można przechwytywać ruch przesyłane za pośrednictwem publicznej sieci Internet, ponieważ dedykowanego połączenia za pośrednictwem dostawcy usług jest używany. |Wymaga lokalnego routera zarządzania. |
| Przepustowość do 10 Gb/s dla obwodu usługi ExpressRoute i przepływności do 2 Gb/s do każdej bramy. |Wymaga dedykowanego połączenia z dostawcą łączności. |
| Przewidywalne opóźnienie jest dedykowane połączenie do firmy Microsoft, które nie przechodzi przez Internet. |Może wymagać minimalnego administracją jeden lub więcej bram sieci VPN platformy Azure (Jeśli obwód nawiązywania połączenia z sieciami wirtualnymi). |
| Nie wymaga komunikacji szyfrowanej, chociaż można szyfrować ruchu sieciowego, w razie potrzeby. |Jeśli używasz usług w chmurze, które inicjują połączenia do lokalnych urządzeń może być konieczne z zaporą między siecią lokalną a platformą Azure. |
| Mogą łączyć się bezpośrednio wszystkich usług chmurowych firmy Microsoft, z kilkoma wyjątkami *. |Wymaga translatora adresów sieciowych (NAT), wprowadzając centrami danych firmy Microsoft dla usług, których nie można połączyć VNet.* * adresów IP w środowisku lokalnym |
| Można wymusić ruch wychodzący inicjowane z chmury maszyn wirtualnych za pośrednictwem sieci lokalnej inspekcji i rejestrowania przy użyciu protokołu BGP. | |

* * Wyświetl [listę usług](../expressroute/expressroute-faqs.md#supported-services) nie można użyć z usługą ExpressRoute. Twoja subskrypcja platformy Azure muszą zostać zatwierdzone do łączenia z usługą Office 365.  Zobacz [usługi Azure ExpressRoute dla usługi Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd?ui=en-US&rs=en-US&ad=US&fromAR=1) artykuł, aby uzyskać szczegółowe informacje.
* ** Więcej informacji na temat usługi ExpressRoute [translatora adresów Sieciowych](../expressroute/expressroute-nat.md) wymagania.

Dowiedz się więcej o [ExpressRoute](../expressroute/expressroute-introduction.md), związanych z nią [ceny](https://azure.microsoft.com/pricing/details/expressroute), i [dostawców łączności](../expressroute/expressroute-locations.md#locations).

## <a name="additional-considerations"></a>Dodatkowe zagadnienia
* Kilka z powyższych opcji mają różne maksymalnych limitów, które obsługują połączenia sieci wirtualnej, połączeń bramy i innych kryteriów. Zalecane jest przejrzenie Azure [limity dotyczące sieci](../azure-subscription-service-limits.md#networking-limits) do zrozumienia, jeśli któryś z nich mieć wpływ na typy połączeń zdecydujesz się używać.
* Jeśli planujesz podłączać bramy za pośrednictwem połączenia sieci VPN typu lokacja lokacja do tej samej sieci wirtualnej jako brama usługi ExpressRoute, należy się zapoznać z ważne ograniczenia najpierw. Zobacz [Konfigurowanie połączeń usługi ExpressRoute i lokacja-lokacja współistniejących](../expressroute/expressroute-howto-coexist-resource-manager.md#limits-and-limitations) artykuł, aby uzyskać więcej informacji.

## <a name="next-steps"></a>Kolejne kroki
Poniższe zasoby wyjaśnić sposób zaimplementowania typów połączeń, omówione w tym artykule.

* [Implementowanie połączenia punkt lokacja](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Implementowanie połączenia lokacja lokacja](guidance-hybrid-network-vpn.md)
* [Zaimplementuj dedykowane połączenie prywatne](guidance-hybrid-network-expressroute.md)
* [Zaimplementuj dedykowane połączenie prywatne za pomocą połączenia typu lokacja lokacja w celu zapewnienia wysokiej dostępności](guidance-hybrid-network-expressroute-vpn-failover.md)
