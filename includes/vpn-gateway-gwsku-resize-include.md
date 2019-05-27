---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f6fd4039614dbd7c1a2b2c6ba8403502a6420fe3
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159380"
---
Dla bieżącej jednostki SKU (VpnGw1, VpnGw2 i VPNGW3), którą chcesz zmienić rozmiar bramy jednostki SKU, aby uaktualnić bardziej wydajne niż ten, który można użyć `Resize-AzVirtualNetworkGateway` polecenia cmdlet programu PowerShell. Możesz również obniżyć wersję bramy rozmiar jednostki SKU, za pomocą tego polecenia cmdlet. Jeśli używasz podstawowa brama jednostki SKU i [zamiast tego użyj tych instrukcji](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) Aby zmienić rozmiar bramy.

W poniższym przykładzie programu PowerShell pokazuje zmiany rozmiaru do VpnGw2 jednostkę SKU bramy.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Można również zmienić rozmiar bramy w witrynie Azure portal, przechodząc do **konfiguracji** strony dla bramy sieci wirtualnej, a następnie wybierając różne jednostki SKU z listy rozwijanej.