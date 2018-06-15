---
title: Starszych sieci wirtualnej platformy Azure jednostki SKU bramy sieci VPN | Dokumentacja firmy Microsoft
description: Jak pracować z starego bramy sieci wirtualnej jednostki SKU; Basic, Standard i wysokowydajnej.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2018
ms.author: cherylmc
ms.openlocfilehash: 4feecb9c1e91e1bc6c66a610c092e7bf894886e5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30190232"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Praca z bramy sieci wirtualnej jednostki SKU (starszej wersji jednostki SKU)

Ten artykuł zawiera informacje dotyczące starszych bramy sieci wirtualnej (stare) jednostki SKU. Starszego jednostki SKU nadal działa w obu modelach wdrażania dla bram sieci VPN, które zostały już utworzone. Klasycznego bramy sieci VPN w dalszym ciągu używać starszej wersji jednostki SKU, zarówno dla bramy istniejących i nowych bram. Podczas tworzenia nowego Menedżera zasobów sieci VPN bramy, użyj nowej jednostki SKU bramy. Informacje o nowej wersji produktu, zobacz [o bramy sieci VPN](vpn-gateway-about-vpngateways.md).

## <a name="gwsku"></a>Jednostki SKU bramy

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

## <a name="agg"></a>Szacowany łącznej przepływności przez jednostki SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>Obsługiwane konfiguracje według typu jednostki SKU i sieci VPN

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>Zmień rozmiar bramy

Można zmienić rozmiar jednostka SKU bramy w ramach tej samej rodziny SKU bramy. Na przykład jeśli masz wersji Standard, możesz zmienić rozmiar do SKU wysokowydajnej. Jednak nie można zmienić rozmiaru bramy sieci VPN między starym jednostki SKU i nowe rodziny SKU. Nie można na przykład przejść z wersji Standard, VpnGw2 SKU lub podstawowa jednostka SKU VpnGw1.

Aby zmienić rozmiar bramy dla klasycznym modelu wdrożenia, użyj następującego polecenia:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

Aby zmienić rozmiar bramy dla modelu wdrażania usługi Resource Manager przy użyciu programu PowerShell, użyj następującego polecenia:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```
Można również zmienić rozmiar bramy w portalu Azure.

## <a name="change"></a>Zmień na nowej jednostki SKU bramy

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o nowej jednostki SKU bramy, zobacz [jednostki SKU bramy](vpn-gateway-about-vpngateways.md#gwsku).

Aby uzyskać więcej informacji na temat ustawień konfiguracji, zobacz [o bramy sieci VPN, ustawienia konfiguracji](vpn-gateway-about-vpn-gateway-settings.md).