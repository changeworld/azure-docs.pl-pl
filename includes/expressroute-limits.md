---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d1402bcc0c46003429e1809e8d09e9662218cd82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334770"
---
| Zasób | Limit |
| --- | --- |
| Obwody usługi ExpressRoute na subskrypcję |10 |
| Obwody usługi ExpressRoute na region na subskrypcję z usługą Azure Resource Manager |10 |
| Maksymalna liczba tras anonsowanych do prywatnej komunikacji równorzędnej platformy Azure w usłudze ExpressRoute Standard |4000 |
| Maksymalna liczba tras anonsowanych do prywatnej komunikacji równorzędnej platformy Azure za pomocą dodatku Usługi ExpressRoute Premium |10 000 |
| Maksymalna liczba tras anonsowanych z prywatnej komunikacji równorzędnej platformy Azure z przestrzeni adresowej sieci wirtualnej dla połączenia usługi ExpressRoute |200 |
| Maksymalna liczba tras anonsowanych do komunikacji równorzędnej firmy Microsoft w programie ExpressRoute Standard |200 |
| Maksymalna liczba tras anonsowanych do komunikacji równorzędnej firmy Microsoft za pomocą dodatku ExpressRoute Premium |200 |
| Maksymalna liczba obwodów usługi ExpressRoute połączonych z tą samą siecią wirtualną w tej samej lokalizacji komunikacji równorzędnej |4 |
| Maksymalna liczba obwodów usługi ExpressRoute połączonych z tą samą siecią wirtualną w różnych lokalizacjach komunikacji równorzędnej |4 |
| Liczba dozwolonych łączy sieci wirtualnej na obwód usługi ExpressRoute |Zobacz liczbę sieci wirtualnych na tabelę [obwodów usługi ExpressRoute.](#vnetpercircuit)  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a>Liczba sieci wirtualnych na obwód usługi ExpressRoute
| **Rozmiar obwodu** | **Liczba łączy sieci wirtualnej dla standardu** | **Liczba łączy sieci wirtualnej z dodatkiem Premium** |
| --- | --- | --- |
| 50 Mb/s |10 |20 |
| 100 Mb/s |10 |25 |
| 200 Mb/s |10 |25 |
| 500 Mb/s |10 |40 |
| 1 Gb/s |10 |50 |
| 2 Gb/s |10 |60 |
| 5 Gb/s |10 |75 |
| 10 Gb/s |10 |100 |
| 40 Gb/s* |10 |100 |
| 100 Gb/s* |10 |100 |

**Tylko bezpośrednie przypisanie usługi ExpressRoute 100 Gb/s*

> [!NOTE]
> Połączenia globalnego zasięgu są wliczane do limitu połączeń sieci wirtualnej na obwód usługi ExpressRoute. Na przykład obwód premium o prędkości 10 Gb/s umożliwiałby 5 połączeń globalnego zasięgu i 95 połączeń z bramami usługi ExpressRoute lub 95 połączeniami globalnego zasięgu oraz 5 połączeń z bramami usługi ExpressRoute lub dowolną inną kombinacją do limitu 100 połączeń obwodu.
