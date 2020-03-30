---
title: Używanie usługi Azure ExpressRoute do obsługi użytkowników zdalnych
description: Na tej stronie opisano, jak można korzystać z usługi Azure ExpressRoute, aby umożliwić pracę zdalną ze względu na pandemię COVID-19.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/22/2020
ms.author: ajitbhu
ms.openlocfilehash: 2fe990fd9c5922dd2e059dbb212cd8bb1cd3726e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336649"
---
# <a name="using-azure-expressroute-to-create-hybrid-connectivity-to-support-remote-users"></a>Tworzenie łączności hybrydowej przy użyciu usługi Azure ExpressRoute w celu obsługi użytkowników zdalnych

W tym artykule opisano, jak można korzystać z usługi ExpressRoute, platformy Azure, sieci firmy Microsoft i ekosystemu partnerów platformy Azure do pracy zdalnej.

## <a name="connecting-to-azure-services-with-expressroute"></a>Łączenie się z usługami platformy Azure za pomocą usługi ExpressRoute

>[!NOTE]
>W tym artykule opisano, jak można wykorzystać usługi ExpressRoute, platformy Azure, sieci firmy Microsoft i ekosystemu partnerów platformy Azure do pracy zdalnej i łagodzenia problemów sieciowych, które występują z powodu kryzysu COVID-19.
>

[Usługa ExpressRoute](expressroute-introduction.md) to usługa platformy Azure, która umożliwia prywatną łączność między centrami danych firmy Microsoft a infrastrukturą, która znajduje się w twoim lokalinie lub w zakładzie kolokacji. Połączenia ExpressRoute nie odbywają się za pośrednictwem publicznego Internetu. Oferują one bezpieczną łączność, niezawodność i szybkość, z niższymi i stałymi opóźnieniami niż typowe połączenia przez Internet.

## <a name="useful-links"></a>Przydatne łącza

* [Jak zwiększyć przepustowość dla istniejącego obwodu usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [Monitorowanie, metryki i alerty usługi ExpressRoute](expressroute-monitoring-metrics-alerts.md#expressroute-gateway-connections-in-bitsseconds)
* [Optymalizacja trasy za 100](expressroute-optimize-routing.md)
* [Usługa Azure ExpressRoute dla usługi O365](https://docs.microsoft.com/office365/enterprise/azure-expressroute?redirectSourcePath=%252farticle%252f6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Zagadnienia dotyczące routingu asymetrycznego](expressroute-asymmetric-routing.md)
* [Jak otworzyć żądanie pomocy technicznej w witrynie Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="troubleshoot"></a>Rozwiązywanie problemów

* [Verify ExpressRoute Connectivity (Weryfikowanie połączenia usługi ExpressRoute)](expressroute-troubleshooting-expressroute-overview.md)
* Uzyskiwanie tabeli ARP w [Menedżerze zasobów](expressroute-troubleshooting-arp-resource-manager.md) i [klasycznym](expressroute-troubleshooting-arp-classic.md)
* [Resetowanie nieudanego obwodu](reset-circuit.md)
* [Rozwiązywanie problemów z wydajnością sieci](expressroute-troubleshooting-network-performance.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o modelach łączności usługi ExpressRoute](expressroute-connectivity-models.md)
* Więcej informacji na temat połączeń ExpressRoute i domen routingu. Zobacz [obwody i domeny routingu usługi ExpressRoute](expressroute-circuit-peerings.md)
* Znajdź dostawcę usługi. Zobacz [partnerów usługi ExpressRoute i lokalizacje komunikacji równorzędnej](expressroute-locations.md)
* Upewnij się, że zostały spełnione wszystkie wymagania wstępne. Zobacz artykuł [ExpressRoute prerequisites](expressroute-prerequisites.md) (Wymagania wstępne usługi ExpressRoute).
* [Tworzenie i modyfikowanie obwodu usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
* [Tworzenie i modyfikowanie komunikacji równorzędnej dla obwodu usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Łączenie sieci wirtualnej z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
