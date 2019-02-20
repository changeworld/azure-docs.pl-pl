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
ms.openlocfilehash: 081352a23e6a0d8f9e2daa77eca1f8ac85172ff6
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418232"
---
Dla bieżącej jednostki SKU (VpnGw1, VpnGw2 i VPNGW3), którą chcesz zmienić rozmiar bramy jednostki SKU, aby uaktualnić bardziej wydajne niż ten, który można użyć `Resize-AzVirtualNetworkGateway` polecenia cmdlet programu PowerShell. Możesz również obniżyć wersję bramy rozmiar jednostki SKU, za pomocą tego polecenia cmdlet. Jeśli używasz podstawowa brama jednostki SKU i [zamiast tego użyj tych instrukcji](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) Aby zmienić rozmiar bramy.

W poniższym przykładzie programu PowerShell pokazuje zmiany rozmiaru do VpnGw2 jednostkę SKU bramy.

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Można również zmienić rozmiar bramy w witrynie Azure portal, przechodząc do **konfiguracji** strony dla bramy sieci wirtualnej, a następnie wybierając różne jednostki SKU z listy rozwijanej.