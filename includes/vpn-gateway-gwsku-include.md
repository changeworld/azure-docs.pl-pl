---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8087025810214f3edbb74e628698eb69558f3500
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74085263"
---
Podczas tworzenia bramy sieci wirtualnej musisz wybrać jednostkę SKU bramy do użycia. Wybierz jednostkę SKU spełniającą Twoje wymagania na podstawie typów obciążeń, przepustowości, funkcji i umów SLA. W przypadku jednostek SKU bramy sieci wirtualnej w strefach dostępności platformy Azure zobacz [Jednostki SKU bramy dostępności platformy Azure](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md).

###  <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>Jednostki SKU bramy według tunelowania, połączenia i przepływności

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

> [!NOTE]
> SKU VPNGw (VpnGw1, VpnGw1AZ, VpnGw2, VpnGw2AZ, VpnGw3, VpnGw3AZ, VpnGw4, VpnGw4AZ, VpnGw5 i VpnGw5AZ) są obsługiwane tylko dla modelu wdrażania Menedżera zasobów. Klasyczne sieci wirtualne powinny nadal używać starych (starszych) jednostek SKU.
>  * Aby uzyskać informacje dotyczące pracy ze starszymi jednostkami SKU bramy (Basic, Standard i HighPerformance), zobacz [Praca z jednostkami SKU bramy sieci VPN (starsze jednostki SKU).](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md)
>  * W przypadku jednostek SKU bramy usługi ExpressRoute zobacz [Bramy sieci wirtualnej dla usługi ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).
>

###  <a name="gateway-skus-by-feature-set"></a><a name="feature"></a>Jednostki SKU bramy według zestawu funkcji

Nowe jednostki SKU bramy sieci VPN usprawniają zestawy funkcji oferowane na bramkach:

| **Numer jednostki magazynowej**| **Funkcje**|
| ---    | ---         |
|**Podstawowe** (**)   | **VPN oparty na trasach:** 10 tuneli dla S2S/połączeń; brak uwierzytelniania RADIUS dla P2S; brak IKEv2 dla P2S<br>**VPN oparty na zasadach:**(IKEv1): 1 tunel S2S/connection; brak P2S|
| **Wszystkie jednostki SKU generacji1 i generacji2 z wyjątkiem podstawowych** | **VPN oparty na trasach:** do 30 tuneli (*), P2S, BGP, aktywne aktywne, niestandardowe zasady IPsec/IKE, współistnienie usługi ExpressRoute/VPN |
|        |             |

(*) Można skonfigurować "PolicyBasedTrafficSelectors", aby połączyć bramę sieci VPN opartą na trasie z wieloma lokalnymi urządzeniami zapór opartych na zasadach. Aby zapoznać się ze szczegółami, zobacz artykuł [Connect VPN gateways to multiple on-premises policy-based VPN devices using PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) (Połączenie bram sieci VPN z wieloma lokalnymi urządzeniami sieci VPN opartymi na zasadach przy użyciu programu PowerShell).

(\*\*) Podstawowa jednostka SKU jest uważana za starszą jednostkę SKU. Podstawowa jednostka SKU ma pewne ograniczenia w zakresie funkcji. Rozmiaru bramy, która korzysta z podstawowej jednostki SKU, nie można zmienić na jedną z nowych jednostek SKU bramy. Zamiast tego należy przejść na nową jednostkę SKU, co wiąże się z koniecznością usunięcia i ponownego utworzenia bramy sieci VPN.

###  <a name="gateway-skus---production-vs-dev-test-workloads"></a><a name="workloads"></a>Jednostki SKU bramy — procesory produkcyjne a obciążenia dewelopersko-testowe

Z powodu różnic w umowach SLA i zestawach funkcji zaleca się następujące jednostki SKU dla trybu produkcyjnego i w środowisku tworzenia i testowania:

| **Obciążenie**                       | **Jednostki SKU**               |
| ---                                | ---                    |
| **Tryb produkcyjny, obciążenia krytyczne** | Wszystkie jednostki SKU generacji1 i generacji2 z wyjątkiem podstawowych |
| **Środowisko tworzenia i testowania lub weryfikacja koncepcji**   | Podstawowe (**)                 |
|                                    |                        |

(\*\*) Podstawowa jednostka SKU jest uważana za starszą jednostkę SKU i ma ograniczenia funkcji. Zanim użyjesz podstawowej jednostki SKU sprawdź, czy potrzebna Ci funkcja jest obsługiwana.

Jeśli używasz starych jednostek SKU (legacy), zalecenia sku produkcji są standardowe i highperformance. Aby uzyskać informacje i instrukcje dotyczące starych jednostek SKU, zobacz [Jednostki SKU bramy (starsze)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
