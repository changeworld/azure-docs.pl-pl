---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c06e69dd9d1997500589659e936dc25ee01ed145
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
ms.locfileid: "30196819"
---
Dla bieżącego jednostki SKU (VpnGw1 VpnGw2 i VPNGW3) Aby zmienić rozmiar aktualizacji do bardziej zaawansowanych, jednostka SKU bramy można użyć `Resize-AzureRmVirtualNetworkGateway` polecenia cmdlet programu PowerShell. Możesz również obniżyć bramy, rozmiar jednostki SKU za pomocą tego polecenia cmdlet. Jeśli używasz bramy podstawowa jednostka SKU, [zamiast tego użyj tych instrukcji](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) zmiany rozmiaru bramy.

W poniższym przykładzie programu PowerShell pokazano zmieniany na VpnGw2 jednostki SKU bramy.

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Można również zmienić rozmiar bramy w portalu Azure, przechodząc do **konfiguracji** strony dla bramy sieci wirtualnej i wybierając różne jednostki SKU z listy rozwijanej.