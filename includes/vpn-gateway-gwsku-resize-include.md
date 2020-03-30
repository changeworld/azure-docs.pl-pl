---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4842c55b2b1fd23f4d6b7996ccf02e7141504836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73495719"
---
Polecenia cmdlet `Resize-AzVirtualNetworkGateway` programu PowerShell można użyć do uaktualnienia lub obniżenia wersji SKU generacji1 lub Generacji2 (wszystkie jednostki SKU VpnGw można zmienić z wyjątkiem podstawowych jednostek SKU). Jeśli używasz jednostki SKU bramy podstawowej, [użyj tych instrukcji,](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) aby zmienić rozmiar bramy.

W poniższym przykładzie programu PowerShell przedstawiono jednostkę SKU bramy, która została przesiąknięta do sieci VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Można również zmienić rozmiar bramy w witrynie Azure portal, przechodząc do **strony konfiguracji** bramy sieci wirtualnej i wybierając inną jednostkę SKU z listy rozwijanej.
