---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/22/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8fb9e9ea0e126509697b4874bf1e5e0b6a380e7f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66425761"
---
|**SKU**   | **Połączenia typu lokacja-lokacja/Połączenia między sieciami wirtualnymi<br>Tunele** | **Połączenia typu punkt-lokacja<br> Połączenia SSTP** | **P2S<br> IKEv2/OpenVPN Connections** | **Test porównawczy<br>agregowanej przepływności** | **BGP** | **Strefowo nadmiarowe** |
|---       | ---        | ---       | ---            | ---       | --- | --- |
|**Podstawowa** | Maksymalnie z 10    | Maksymalnie z 128  | Nieobsługiwane  | 100 Mb/s  | Nieobsługiwane| Nie |
|**VpnGw1**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 250       | 650 Mb/s  | Obsługiwane | Nie |
|**VpnGw2**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 500       | 1 Gb/s    | Obsługiwane | Nie |
|**VpnGw3**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 1000      | 1,25 Gb/s | Obsługiwane | Nie |
|**VpnGw1AZ**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 250       | 650 Mb/s  | Obsługiwane | Tak |
|**VpnGw2AZ**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 500       | 1 Gb/s    | Obsługiwane | Tak |
|**VpnGw3AZ**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 1000      | 1,25 Gb/s | Obsługiwane | Yes |


(*) Jeśli potrzebujesz więcej niż 30 tuneli sieci VPN S2S, skorzystaj z usługi [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md).

* Test porównawczy agregowanej przepływności opiera się na pomiarach wielu tuneli zagregowanych za pośrednictwem jednej bramy. Test porównawczy agregowanej przepływności dla bramy sieci VPN bazuje na sumie wartości dla połączeń typu lokacja-lokacja i punkt-lokacja. **Posiadanie dużej liczby połączeń typu punkt-lokacja może negatywnie wpływać na połączenie typu lokacja-lokacja z powodu ograniczeń przepustowości.** Ze względu na warunki ruchu internetowego i zachowania aplikacji test porównawczy agregowanej przepływności nie pokazuje przepływności gwarantowanej.

* Te limity połączeń są niezależne. Przykładowo dla jednostki SKU VpnGw1 można mieć 128 połączeń SSTP, a oprócz tego 250 połączeń IKEv2.

* Informacje o cenach znajdują się na stronie [Cennik](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Informacje na temat umowy SLA (Service Level Agreement) można znaleźć na stronie [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* Jednostki SKU VpnGw1, VpnGw2 i VpnGw3 są obsługiwane w przypadku bram sieci VPN tylko za pomocą modelu wdrażania przy użyciu usługi Resource Manager.

* Podstawowa jednostka SKU jest traktowana jako starsza jednostka SKU. Podstawowa jednostka SKU ma pewne ograniczenia w zakresie funkcji. Rozmiaru bramy, która korzysta z podstawowej jednostki SKU, nie można zmienić na jedną z nowych jednostek SKU bramy. Zamiast tego należy przejść na nową jednostkę SKU, co wiąże się z koniecznością usunięcia i ponownego utworzenia bramy sieci VPN. Zanim użyjesz podstawowej jednostki SKU sprawdź, czy potrzebna Ci funkcja jest obsługiwana.
