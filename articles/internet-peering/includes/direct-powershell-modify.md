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
ms.openlocfilehash: 03c67ccf88a8c73fe04f062c6af9520115c185a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774265"
---
W tej sekcji opisano sposób wykonywania następujących operacji modyfikacji dla komunikacji bezpośredniej komunikacji równorzędnej:

* Dodawanie bezpośrednich połączeń komunikacji równorzędnej
* Usuwanie bezpośrednich połączeń komunikacji równorzędnej
* Uaktualnianie lub obniżanie przepustowości w połączeniach aktywnych.
* Dodaj sesję IPv4/IPv6 w aktywnych połączeniach.
* Usuń sesję IPv4/IPv6 w aktywnych połączeniach.

### <a name="add-direct-peering-connections"></a>Dodawanie bezpośrednich połączeń komunikacji równorzędnej

W poniższym przykładzie opisano sposób dodawania połączeń do istniejącej komunikacji bezpośredniej komunikacji równorzędnej

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringDirectConnection `
    -PeeringDBFacilityId $peeringLocation.PeeringDBFacilityId `
    -SessionPrefixV4 "10.22.31.0/31" `
    -SessionPrefixV6 "fe02::3e:0/127" `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000

$directPeering.Connections.Add($connection)

$directPeering | Update-AzPeering
```

### <a name="remove-direct-peering-connections"></a>Usuwanie bezpośrednich połączeń komunikacji równorzędnej

Usuwanie połączenia nie jest obecnie obsługiwane w programie PowerShell. Skontaktuj się z [programem Microsoft w komunikacji równorzędnej](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Uaktualnianie lub obniżanie przepustowości połączeń aktywnych

W poniższym przykładzie opisano sposób dodawania 10 Gb/s do istniejącego połączenia bezpośredniego.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Dodaj sesję IPv4/IPv6 w aktywnych połączeniach.

W poniższym przykładzie opisano sposób dodawania sesji IPv6 do istniejącego połączenia bezpośredniego tylko z sesją IPv4. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Usuń sesję IPv4/IPv6 w aktywnych połączeniach.

Usuwanie sesji IPv4/IPv6 z istniejącego połączenia nie jest obecnie obsługiwane w programie PowerShell. Skontaktuj się z [programem Microsoft w komunikacji równorzędnej](mailto:peeringexperience@microsoft.com).