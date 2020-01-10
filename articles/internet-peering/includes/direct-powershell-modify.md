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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774265"
---
W tej sekcji opisano sposób wykonywania następujących operacji modyfikacji dla bezpośredniej komunikacji równorzędnej:

* Dodaj bezpośrednie połączenia komunikacji równorzędnej
* Usuń bezpośrednie połączenia komunikacji równorzędnej
* Uaktualnij lub obniżaj poziom przepustowości dla aktywnych połączeń.
* Dodaj sesję IPv4/IPv6 dla aktywnych połączeń.
* Usuń sesję IPv4/IPv6 dla aktywnych połączeń.

### <a name="add-direct-peering-connections"></a>Dodaj bezpośrednie połączenia komunikacji równorzędnej

W poniższym przykładzie opisano sposób dodawania połączeń do istniejącej bezpośredniej komunikacji równorzędnej

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

### <a name="remove-direct-peering-connections"></a>Usuń bezpośrednie połączenia komunikacji równorzędnej

Usuwanie połączenia nie jest obecnie obsługiwane w programie PowerShell. Skontaktuj się z pomocą techniczną [firmy Microsoft](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Przepustowość uaktualnienia lub obniżenia poziomu aktywnych połączeń

W poniższym przykładzie opisano sposób dodawania 10Gbps do istniejącego połączenia bezpośredniego.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Dodaj sesję IPv4/IPv6 dla aktywnych połączeń.

W poniższym przykładzie opisano sposób dodawania sesji IPv6 na istniejącym połączeniu bezpośrednim z tylko sesją IPv4. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Usuń sesję IPv4/IPv6 dla aktywnych połączeń.

Usuwanie sesji IPv4/IPv6 z istniejącego połączenia nie jest obecnie obsługiwane w programie PowerShell. Skontaktuj się z pomocą techniczną [firmy Microsoft](mailto:peeringexperience@microsoft.com).