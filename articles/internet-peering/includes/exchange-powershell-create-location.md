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
ms.openlocfilehash: 6f5d2dc30ac0f6316587fa0836b87cbd4efc0a8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774395"
---
Polecenie cmdlet programu PowerShell **Get-AzPeeringLocation** zwraca listę lokalizacji `Kind`komunikacji równorzędnej z parametrem obowiązkowym, który będzie używany w późniejszych krokach:

```powershell
Get-AzPeeringLocation -Kind "Exchange"
```

Lokalizacje komunikacji równorzędnej programu Exchange zawierają następujące pola:
* Nazwa wymiany
* Lokalizacja komunikacji równorzędnej
* Kraj
* Element PeeringDBFacilityId
* PeeringDBFacilityLink
* MicrosoftIPv4Address
* MicrosoftIPv6Address

Sprawdź, czy jesteś obecny w żądanej funkcji komunikacji równorzędnej, odwołując się do [peeringdb](https://wwww.peeringdb.com).

Poniżej znajduje się przykład, który pokazuje, jak używać Seattle jako lokalizacji komunikacji równorzędnej do tworzenia komunikacji równorzędnej:

```powershell
$exchangeLocations = Get-AzPeeringLocation -Kind Exchange
$exchangeLocation = $exchangeLocations | where {$_.PeeringLocation -eq "Seattle"}

#check the location metadata
$exchangeLocation

ExchangeName          : Columbia IX
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 99999
PeeringDBFacilityLink : https://www.peeringdb.com/ix/99999
MicrosoftIPv4Address  : 10.12.97.129
MicrosoftIPv6Address  :

ExchangeName          : Equinix Seattle
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 11
PeeringDBFacilityLink : https://www.peeringdb.com/ix/11
MicrosoftIPv4Address  : 198.32.134.152
MicrosoftIPv6Address  : 2001:504:12::15

...

```