---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 05/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9f7f9b1504533e0ec503e97c086c380da71671a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66299706"
---
| Resource | Domyślne/maksymalny limit |
| --- | --- |
| Obwody usługi ExpressRoute na subskrypcję |10 |
| Obwody usługi ExpressRoute na region na subskrypcję, za pomocą usługi Azure Resource Manager |10 |
| Maksymalna liczba tras anonsowanych do prywatnej komunikacji równorzędnej Azure ze standardem usługi ExpressRoute |4,000 |
| Maksymalna liczba tras anonsowanych do prywatnej komunikacji równorzędnej Azure dzięki dodatkowi ExpressRoute Premium |10 000 |
| Maksymalna liczba tras anonsowanych z prywatnej komunikacji równorzędnej Azure z przestrzeni adresowej sieci wirtualnej dla połączenia usługi ExpressRoute |200 |
| Maksymalna liczba tras anonsowanych do firmy Microsoft, komunikację równorzędną za pomocą usługi ExpressRoute Standard |200 |
| Maksymalna liczba tras anonsowanych do firmy Microsoft, komunikacja równorzędna dzięki dodatkowi ExpressRoute Premium |200 |
| Maksymalna liczba obwodów usługi ExpressRoute, połączone z tej samej sieci wirtualnej w tej samej lokalizacji komunikacji równorzędnej |4 |
| Maksymalna liczba obwodów usługi ExpressRoute, połączone z tej samej sieci wirtualnej w innej lokalizacji komunikacji równorzędnej |4 |
| Liczba łączy sieci wirtualnej może istnieć obwód usługi ExpressRoute |Zobacz [liczbę sieci wirtualnych na obwód usługi ExpressRoute](#vnetpercircuit) tabeli.  |

#### <a name="vnetpercircuit"></a> Liczba sieci wirtualnych na obwód usługi ExpressRoute
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
| 40 GB/s * |10 |100 |
| 100 GB/s * |10 |100 |

\* Tylko bezpośrednio usługi ExpressRoute
