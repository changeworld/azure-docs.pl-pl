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
ms.openlocfilehash: 86e1a9cce1864ce259fe07b6949be2e32be242a8
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774421"
---
Następująca operacja modyfikacji jest obsługiwana w przypadku komunikacji równorzędnej programu Exchange
1. Dodawanie połączeń komunikacji równorzędnej programu Exchange
1. Usuń połączenia komunikacji równorzędnej programu Exchange
1. Dodaj sesję IPv4/IPv6 dla aktywnych połączeń.
1. Usuń sesję IPv4/IPv6 dla aktywnych połączeń.


### <a name="add-exchange-peering-connections"></a>Dodawanie połączeń komunikacji równorzędnej programu Exchange

W poniższym przykładzie opisano sposób dodawania połączeń do istniejącej komunikacji równorzędnej programu Exchange

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringExchangeConnectionObject `
    -PeeringDBFacilityId $exchangeLocation[1].PeeringDBFacilityId `
    -PeerSessionIPv4Address 198.32.134.22 `
    -PeerSessionIPv6Address  2001:504:12::22 `
    -MaxPrefixesAdvertisedIPv4 2000 `
    -MaxPrefixesAdvertisedIPv6 2000 `

$exchangePeering.Connections.Add($connection)

$exchangePeering | Update-AzPeering

```

### <a name="remove-exchange-peering-connections"></a>Usuń połączenia komunikacji równorzędnej programu Exchange

W poniższym przykładzie opisano sposób usuwania połączeń do istniejącej komunikacji równorzędnej programu Exchange

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

```

Wyświetl wszystkie połączenia i wybierz połączenie, które chcesz usunąć. 

```powershell

$exchangePeering

Name              : SeattleExchangePeering
Sku.Name          : Basic_Exchange_Free
Kind              : Exchange
Connections       : {11}
PeerAsn.Id        : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{peerAsnName}
PeeringLocation   : Seattle
ProvisioningState : Succeeded
Location          : West US 2
Id                : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleExchangePeering
Type              : Microsoft.Peering/peerings
Tags              : {}

```

W poniższym poleceniu zamiast wartości 0 wprowadź numer indeksu dla połączenia, które chcesz usunąć.

```powershell

$connection = $exchangePeering.Connections[0]

$exchangePeering.Connections.Remove($connection)

$exchangePeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Dodaj sesję IPv4/IPv6 dla aktywnych połączeń

W poniższym przykładzie opisano sposób dodawania sesji IPv6 do istniejącego połączenia z programem Exchange.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Usuń sesję protokołu IPv4/IPv6 dla aktywnych połączeń

Usuwanie sesji IPv4/IPv6 z istniejącego połączenia nie jest obecnie obsługiwane w programie PowerShell. Skontaktuj się z pomocą techniczną [firmy Microsoft](mailto:peeringexperience@microsoft.com).