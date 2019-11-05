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
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495719"
---
Możesz użyć polecenia cmdlet programu `Resize-AzVirtualNetworkGateway` PowerShell, aby uaktualnić lub obniżyć wersję SKU Generation1 lub Generation2 (wszystkie jednostki SKU VpnGw można zmienić z wyjątkiem podstawowych jednostek SKU). Jeśli używasz podstawowej jednostki SKU bramy, [zamiast tego użyj tych instrukcji](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) , aby zmienić rozmiar bramy.

Poniższy przykład programu PowerShell pokazuje jednostkę SKU bramy, która jest zmieniana na VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Możesz również zmienić rozmiar bramy w Azure Portal, przechodząc do strony **konfiguracji** bramy sieci wirtualnej i wybierając inną jednostkę SKU z listy rozwijanej.
