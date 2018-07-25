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
ms.openlocfilehash: fad9b990b6ff1021efdaf8aadeb1e19d8a55871d
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39137988"
---
|**SKU**   | **Połączenia typu lokacja-lokacja/Połączenia między sieciami wirtualnymi<br>Tunele** | **Połączenia<br>punkt-lokacja** | **Test porównawczy<br>agregowanej przepływności** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| Maksymalnie z 30*                         | Maksymalnie z 128**             | 650 Mb/s                    |
|**VpnGw2**| Maksymalnie z 30*                         | Maksymalnie z 128**             | 1 Gb/s                      |
|**VpnGw3**| Maksymalnie z 30*                         | Maksymalnie z 128**             | 1,25 Gb/s                   |
|**Podstawowa** | Maksymalnie z 10                         | Maksymalnie z 128               | 100 Mb/s                    | 

* (*) Jeśli potrzebujesz więcej niż 30 tuneli sieci VPN S2S, skorzystaj z usługi [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md).

* (**) Skontaktuj się z pomocą techniczną, jeśli są potrzebne dodatkowe połączenia

* Test porównawczy agregowanej przepływności opiera się na pomiarach wielu tuneli zagregowanych za pośrednictwem jednej bramy. Ze względu na warunki ruchu internetowego i zachowania aplikacji nie jest to przepływność gwarantowana.

* Informacje o cenach znajdują się na stronie [Cennik](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Informacje na temat umowy SLA (Service Level Agreement) można znaleźć na stronie [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* Jednostki SKU VpnGw1, VpnGw2 i VpnGw3 są obsługiwane w przypadku bram sieci VPN tylko za pomocą modelu wdrażania przy użyciu usługi Resource Manager.