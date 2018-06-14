---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6efec75884857d93f2e128104136bf59a1114594
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197182"
---
W poniższej tabeli przedstawiono typy bram i szacowaną agregowaną przepływność według jednostek SKU bramy. Ta tabela ma zastosowanie do usługi Resource Manager i klasycznych modeli wdrażania. 

Ceny różnych jednostek SKU bramy są różne. Aby uzyskać więcej informacji, zobacz temat [VPN Gateway — cennik](https://azure.microsoft.com/pricing/details/vpn-gateway).

Zwróć uwagę, że w tej tabeli nie przedstawiono jednostki SKU bramy UltraPerformance. Informacje o jednostce SKU UltraPerformance można znaleźć w dokumentacji dotyczącej usługi [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).

|  | **Przepływność usługi VPN Gateway (1)** | **Maksymalna liczba tuneli protokołu IPsec usługi VPN Gateway (2)** | **Przepływność bramy ExpressRoute** | **Usługi VPN Gateway i ExpressRoute współistnieją** |
| --- | --- | --- | --- | --- |
| **Podstawowa jednostka SKU (3)(5)(6)** |100 Mb/s |10 |500 Mb/s (6) |Nie |
| **Standardowa jednostka SKU (4)(5)** |100 Mb/s |10 |1000 Mb/s |Yes |
| **Jednostka SKU wysokiej wydajności (4)** |200 Mb/s |30 |2000 Mb/s |Yes |


(1) Przepływność sieci VPN została wstępnie oszacowana na podstawie pomiarów między sieciami wirtualnymi w ramach tego samego regionu systemu Azure. Nie jest to gwarantowana przepływność dla połączeń obejmujących wiele lokalizacji za pośrednictwem sieci Internet. Jest to maksymalna możliwa przepustowość.

(2) Liczba tuneli dotyczy sieci VPN opartych na trasie. Sieć VPN oparta na zasadach może obsługiwać tylko jeden tunel VPN typu lokacja-lokacja.

(3) Protokół BGP nie jest obsługiwany w ramach podstawowej jednostki SKU.

(4) Sieci VPN oparte na zasadach nie są obsługiwane w ramach tej jednostki SKU. Są one obsługiwane tylko w ramach podstawowej jednostki SKU.

(5) Połączenia usługi VPN Gateway typu lokacja do lokacji (S2S) aktywne-aktywne nie są obsługiwane w ramach tej jednostki SKU. Połączenie typu aktywne-aktywne jest obsługiwane tylko dla jednostki SKU o wysokiej wydajności.

(6) podstawowy SKU jest przestarzały do użycia z usługi ExpressRoute.
