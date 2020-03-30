---
title: Starsze jednostki SKU bramy sieci vpn sieci wirtualnej sieci wirtualnej platformy Azure
description: Jak pracować ze starymi jednostkami SKU bramy sieci wirtualnej; Podstawowa, Standardowa i WysokaWydajność.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/15/2019
ms.author: cherylmc
ms.openlocfilehash: 9c5e6d5aca51bd560a46837ba47de86362665773
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279392"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Praca z jednostkami SKU bramy sieci wirtualnej (starsze jednostki SKU)

Ten artykuł zawiera informacje o starszych (starych) jednostkach SKU bramy sieci wirtualnej. Starsze jednostki SKU nadal działają w obu modelach wdrażania bram sieci VPN, które zostały już utworzone. Klasyczne bramy sieci VPN nadal używają starszych jednostek SKU, zarówno dla istniejących bram, jak i dla nowych bram. Podczas tworzenia nowych bram sieci VPN Menedżera zasobów należy używać nowych jednostek SKU bramy. Aby uzyskać informacje o nowych jednostkach SKU, zobacz [Informacje o bramie sieci VPN](vpn-gateway-about-vpngateways.md).

## <a name="gateway-skus"></a><a name="gwsku"></a>Jednostki SKU bramy

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

Ceny starszych bram można wyświetlić w sekcji **Bramy sieci wirtualnej,** która znajduje się na [stronie cennika usługi ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute).

## <a name="estimated-aggregate-throughput-by-sku"></a><a name="agg"></a>Szacowana agregowana przepływność według jednostki SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="supported-configurations-by-sku-and-vpn-type"></a><a name="config"></a>Obsługiwane konfiguracje według jednostki SKU i typu SIECI VPN

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize-a-gateway"></a><a name="resize"></a>Zmienianie rozmiaru bramy

Można zmienić rozmiar bramy do jednostki SKU bramy w tej samej rodzinie jednostek SKU. Na przykład jeśli masz standardową jednostkę SKU, można zmienić rozmiar do jednostki SKU HighPerformance. Nie można jednak zmienić rozmiaru bramy sieci VPN między starymi jednostkami SKU a nowymi rodzinami jednostek SKU. Na przykład nie można przejść ze standardowej jednostki SKU do jednostki SKU VpnGw2 lub podstawowej jednostki SKU do VpnGw1.

### <a name="resource-manager"></a>Resource Manager

Aby zmienić rozmiar bramy modelu wdrażania Menedżera zasobów przy użyciu programu PowerShell, należy użyć następującego polecenia:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

Można również zmienić rozmiar bramy w witrynie Azure portal.

### <a name="classic"></a><a name="classicresize"></a>Wdrożenie klasyczne

Aby zmienić rozmiar bramy dla klasycznego modelu wdrażania, należy użyć poleceń cmdlet programu PowerShell zarządzania usługami. Użyj następującego polecenia:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="change-to-the-new-gateway-skus"></a><a name="change"></a>Przejście na nowe jednostki SKU bramy

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat nowych jednostek SKU bramy, zobacz [Jednostki SKU bramy](vpn-gateway-about-vpngateways.md#gwsku).

Aby uzyskać więcej informacji o ustawieniach konfiguracji, zobacz [Informacje o ustawieniach konfiguracji bramy sieci VPN](vpn-gateway-about-vpn-gateway-settings.md).
