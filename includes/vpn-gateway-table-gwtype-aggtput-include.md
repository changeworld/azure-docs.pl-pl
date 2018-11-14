---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b698dd03473dd3cb708c47c6554869eebba48bf9
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2018
ms.locfileid: "51597637"
---
|**SKU**   | **Połączenia typu lokacja-lokacja/Połączenia między sieciami wirtualnymi<br>Tunele** | **Połączenia typu punkt-lokacja<br> Połączenia SSTP** | **Połączenia typu punkt-lokacja<br> Połączenia IKEv2** | **Test porównawczy<br>agregowanej przepływności** | **BGP** |
|---       | ---        | ---       | ---            | ---       | --- |
|**Podstawowa** | Maksymalnie z 10    | Maksymalnie z 128  | Nieobsługiwane  | 100 Mb/s  | Nieobsługiwane|
|**VpnGw1**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 250       | 650 Mb/s  | Obsługiwane |
|**VpnGw2**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 500       | 1 Gb/s    | Obsługiwane |
|**VpnGw3**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 1000      | 1,25 Gb/s | Obsługiwane |


(*) Jeśli potrzebujesz więcej niż 30 tuneli sieci VPN S2S, skorzystaj z usługi [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md).

* Test porównawczy agregowanej przepływności dla bramy sieci VPN bazuje na sumie wartości dla połączeń typu lokacja-lokacja i punkt-lokacja. **Posiadanie dużej liczby połączeń typu punkt-lokacja może negatywnie wpływać na połączenie typu lokacja-lokacja z powodu ograniczeń przepustowości.** Test porównawczy agregowanej przepływności opiera się na pomiarach wielu tuneli zagregowanych za pośrednictwem jednej bramy. Ze względu na warunki ruchu internetowego i zachowania aplikacji nie jest to przepływność gwarantowana.

* Te limity połączeń są niezależne. Przykładowo dla jednostki SKU VpnGw1 można mieć 128 połączeń SSTP, a oprócz tego 250 połączeń IKEv2.

* Informacje o cenach znajdują się na stronie [Cennik](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Informacje na temat umowy SLA (Service Level Agreement) można znaleźć na stronie [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* Jednostki SKU VpnGw1, VpnGw2 i VpnGw3 są obsługiwane w przypadku bram sieci VPN tylko za pomocą modelu wdrażania przy użyciu usługi Resource Manager.
