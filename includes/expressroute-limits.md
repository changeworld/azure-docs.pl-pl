---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 06/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d067f1af3d5479aef28ddf2290cebe3fe45726b0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64733794"
---
| Resource | Domyślne/maksymalny limit |
| --- | --- |
| Obwody usługi ExpressRoute na subskrypcję |10 |
| Obwody usługi ExpressRoute na region na subskrypcję, za pomocą usługi Azure Resource Manager |10 |
| Maksymalna liczba tras do prywatnej komunikacji równorzędnej Azure ze standardem usługi ExpressRoute |4,000 |
| Maksymalna liczba tras do prywatnej komunikacji równorzędnej Azure dzięki dodatkowi ExpressRoute Premium |10 000 |
| Maksymalna liczba tras do prywatnej komunikacji równorzędnej Azure z przestrzeni adresowej sieci wirtualnej dla połączenia usługi ExpressRoute |200 | 
| Maksymalna liczba tras do komunikacji równorzędnej, Microsoft Azure ze standardem usługi ExpressRoute |200 |
| Maksymalna liczba tras do komunikacji równorzędnej Microsoft Azure, dzięki dodatkowi ExpressRoute Premium |200 |
| Maksymalna liczba obwodów usługi ExpressRoute, połączone z tej samej sieci wirtualnej w tej samej lokalizacji komunikacji równorzędnej |4 |
| Maksymalna liczba obwodów usługi ExpressRoute, połączone z tej samej sieci wirtualnej w innej lokalizacji komunikacji równorzędnej |> 4, zależy od rozmiaru GatewaySubnet|
| Liczba łączy sieci wirtualnej może istnieć obwód usługi ExpressRoute |Zobacz poniższą tabelę. |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a>Liczba sieci wirtualnych na obwód usługi ExpressRoute
| **Rozmiar obwodu** | **Liczba łączy sieci wirtualnej dla warstwy standardowa** | **Liczba łączy sieci wirtualnej z dodatkiem Premium** |
| --- | --- | --- |
| 50 Mb/s |10 |20 |
| 100 Mb/s |10 |25 |
| 200 Mb/s |10 |25 |
| 500 Mb/s |10 |40 |
| 1 Gb/s |10 |50 |
| 2 Gb/s |10 |60 |
| 5 Gb/s |10 |75 |
| 10 Gb/s |10 |100 |

