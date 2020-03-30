---
title: Plik dyrektywy include
titleSuffix: Azure
description: Plik dyrektywy include
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 86d768db7a31c634bdaca6c93f633c7bbaf10a65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774291"
---
Polecenie cmdlet programu PowerShell **Get-AzPeeringLocation** zwraca listę lokalizacji `Kind`komunikacji równorzędnej z parametrem obowiązkowym, który będzie używany w późniejszych krokach:

```powershell
Get-AzPeeringLocation -Kind Direct
```

Bezpośrednie lokalizacje komunikacji równorzędnej zawierają następujące pola:
* Lokalizacja komunikacji równorzędnej 
* Kraj
* Element PeeringDBFacilityId
* PeeringDBFacilityLink
* TransferOffers

Sprawdź, czy jesteś obecny w żądanej funkcji komunikacji równorzędnej, odwołując się do [peeringdb](https://wwww.peeringdb.com).

Poniżej znajduje się przykład, który pokazuje, jak używać Seattle jako lokalizacji komunikacji równorzędnej do tworzenia direct peering:

```powershell
$peeringLocations = Get-AzPeeringLocation -Kind Direct
$peeringLocation = $peeringLocations | where {$_.PeeringLocation -contains "Seattle"}
$peeringLocation

PeeringLocation       : Seattle
Address               : 2001 Sixth Avenue
Country               : US
PeeringDBFacilityId   : 71
PeeringDBFacilityLink : https://www.peeringdb.com/fac/71
BandwidthOffers       : {10Gbps, 100Gbps}
```