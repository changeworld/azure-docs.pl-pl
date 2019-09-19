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
ms.openlocfilehash: 45d34297bf37a6e46bc57e95ff49def49051e32e
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "67622114"
---
| Resource | Domyślny/maksymalny limit |
| --- | --- |
| Obwody usługi ExpressRoute na subskrypcję |10 |
| Obwody usługi ExpressRoute na region na subskrypcję z Azure Resource Manager |10 |
| Maksymalna liczba tras anonsowanych do prywatnej komunikacji równorzędnej Azure przy użyciu standardu ExpressRoute |4,000 |
| Maksymalna liczba tras anonsowanych do prywatnej komunikacji równorzędnej Azure przy użyciu dodatku ExpressRoute Premium |10 000 |
| Maksymalna liczba tras anonsowanych z prywatnej komunikacji równorzędnej Azure z przestrzeni adresowej sieci wirtualnej dla połączenia ExpressRoute |200 |
| Maksymalna liczba tras anonsowanych do komunikacji równorzędnej firmy Microsoft przy użyciu standardu ExpressRoute |200 |
| Maksymalna liczba tras anonsowanych w komunikacji równorzędnej firmy Microsoft z dodatkiem ExpressRoute Premium |200 |
| Maksymalna liczba obwodów usługi ExpressRoute połączonych z tą samą siecią wirtualną w tej samej lokalizacji komunikacji równorzędnej |4 |
| Maksymalna liczba obwodów usługi ExpressRoute połączonych z tą samą siecią wirtualną w różnych lokalizacjach komunikacji równorzędnej |4 |
| Liczba dozwolonych linków sieci wirtualnej na obwód ExpressRoute |Zobacz [liczbę sieci wirtualnych na tabelę obwodu ExpressRoute](#vnetpercircuit) .  |

#### <a name="vnetpercircuit"></a>Liczba sieci wirtualnych na obwód ExpressRoute
| **Rozmiar obwodu** | **Liczba linków sieci wirtualnej dla warstwy Standardowa** | **Liczba linków sieci wirtualnej z dodatkiem Premium** |
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

**100 GB/s ExpressRoute tylko bezpośrednie*
