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
ms.openlocfilehash: c9457e51858d4a073d8baffdd435c8100d95d566
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
|**SKU**   | **Połączenia typu lokacja-lokacja/Połączenia między sieciami wirtualnymi<br>Tunele** | **Połączenia<br>punkt-lokacja** | **Test porównawczy<br>agregowanej przepływności** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| Maksymalnie z 30                         | Maksymalnie z 128               | 650 Mb/s                    |
|**VpnGw2**| Maksymalnie z 30                         | Maksymalnie z 128               | 1 Gb/s                      |
|**VpnGw3**| Maksymalnie z 30                         | Maksymalnie z 128               | 1,25 Gb/s                   |
|**Podstawowa** | Maksymalnie z 10                         | Maksymalnie z 128               | 100 Mb/s                    | 
|          |                                 |                        |                             | 

- Test porównawczy agregowanej przepływności opiera się na pomiarach wielu tuneli zagregowanych za pośrednictwem jednej bramy. Ze względu na warunki ruchu internetowego i zachowania aplikacji nie jest to przepływność gwarantowana.

- Informacje o cenach znajdują się na stronie [Cennik](https://azure.microsoft.com/pricing/details/vpn-gateway).

- Informacje na temat umowy SLA (Service Level Agreement) można znaleźć na stronie [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

- VpnGw1, VpnGw2 i VpnGw3 są obsługiwane dla bram sieci VPN przy użyciu tylko modelu wdrażania Menedżera zasobów.