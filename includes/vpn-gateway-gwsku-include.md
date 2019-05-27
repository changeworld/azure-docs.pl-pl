---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/20/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b1a9d93d9fccf02ba1517e429625150736e539e9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159333"
---
Podczas tworzenia bramy sieci wirtualnej musisz wybrać jednostkę SKU bramy do użycia. Wybierz jednostkę SKU spełniającą Twoje wymagania na podstawie typów obciążeń, przepustowości, funkcji i umów SLA. Dla bramy sieci wirtualnej jednostek SKU w języku strefy dostępności platformy Azure, zobacz [SKU bramy stref dostępności platformy Azure](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md).

###  <a name="benchmark"></a>Jednostki SKU bramy według tunelowania, połączenia i przepływności

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

###  <a name="feature"></a>Jednostki SKU bramy według zestawu funkcji

Nowa brama sieci VPN jednostki SKU usprawnić zestawy funkcji oferowane w bramach:

| **SKU**| **Funkcje**|
| ---    | ---         |
|**Podstawowe** (*)   | **Sieć VPN oparta na trasy**: 10 tunele S2S/połączeń; nie uwierzytelniania RADIUS na potrzeby P2S; bez protokołu IKEv2 dla P2S<br>**Sieć VPN oparta na zasadach**: (IKEv1): 1 tunelu S2S/połączenia; bez P2S|
| **VpnGw1, VpnGw2 i VpnGw3** | **Sieć VPN oparta na trasy**: maksymalnie 30 tuneli (*) P2S, BGP, aktywne aktywne, niestandardowe protokołu IPsec/IKE zasady, współistnienie ExpressRoute/VPN |
|        |             |

( * ) Można skonfigurować „PolicyBasedTrafficSelectors” do łączenia bramy sieci VPN opartej na trasach (VpnGw1, VpnGw2, VpnGw3) z wieloma lokalnymi urządzeniami zapory opartymi na zasadach. Aby zapoznać się ze szczegółami, zobacz artykuł [Connect VPN gateways to multiple on-premises policy-based VPN devices using PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) (Połączenie bram sieci VPN z wieloma lokalnymi urządzeniami sieci VPN opartymi na zasadach przy użyciu programu PowerShell).

(\*\*) Podstawowa jednostka SKU jest uznawany za starszej wersji jednostki SKU. Podstawowa jednostka SKU ma pewne ograniczenia w zakresie funkcji. Rozmiaru bramy, która korzysta z podstawowej jednostki SKU, nie można zmienić na jedną z nowych jednostek SKU bramy. Zamiast tego należy przejść na nową jednostkę SKU, co wiąże się z koniecznością usunięcia i ponownego utworzenia bramy sieci VPN.

###  <a name="workloads"></a>Jednostki SKU bramy — vs w środowisku produkcyjnym. w środowisku tworzenia i testowania

Z powodu różnic w umowach SLA i zestawach funkcji zaleca się następujące jednostki SKU dla produkcji i testowania aplikacji:

| **Obciążenie**                       | **Jednostki SKU**               |
| ---                                | ---                    |
| **Tryb produkcyjny, obciążenia krytyczne** | VpnGw1, VpnGw2, VpnGw3 |
| **Środowisko tworzenia i testowania lub weryfikacja koncepcji**   | Basic (*)                 |
|                                    |                        |

(\*\*) Podstawowa jednostka SKU jest uznawany za starszej wersji jednostki SKU i ma ograniczenia funkcji. Zanim użyjesz podstawowej jednostki SKU sprawdź, czy potrzebna Ci funkcja jest obsługiwana.

Jeśli używasz starych jednostek SKU (starsza wersja) produkcyjnym zalecane są jednostki SKU Standard i HighPerformance. Aby uzyskać informacje i instrukcje dla starych jednostek SKU, zobacz [jednostki SKU bramy (starsze)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
