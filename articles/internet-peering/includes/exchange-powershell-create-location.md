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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774395"
---
Polecenie cmdlet **Get-AzPeeringLocation** programu PowerShell zwraca listę lokalizacji komunikacji równorzędnej z obowiązkowym parametrem `Kind`, który będzie używany w dalszych krokach:

```powershell
Get-AzPeeringLocation -Kind "Exchange"
```

Lokalizacje komunikacji równorzędnej programu Exchange zawierają następujące pola:
* Program exchangename
* PeeringLocation
* Kraj
* PeeringDBFacilityId
* PeeringDBFacilityLink
* MicrosoftIPv4Address
* MicrosoftIPv6Address

Sprawdź, czy istnieje w wymaganej funkcji komunikacji równorzędnej, odwołując się do [PeeringDB](https://w www.peeringdb.com).

Poniżej znajduje się przykład, w którym pokazano, jak używać Seattle jako lokalizacji komunikacji równorzędnej do tworzenia komunikacji równorzędnej:

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