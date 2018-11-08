---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 11/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fab00e5281bb91bce10228b3bc2e9cfd503d5d5b
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219821"
---
Należy utworzyć sieć wirtualną i podsieć bramy, przed rozpoczęciem pracy na następujące zadania.

> [!NOTE]
> Te przykłady nie dotyczą S2S/ExpressRoute współistnieją konfiguracje.
> Aby uzyskać więcej informacji na temat pracy z bramami w konfiguracji coexist zobacz [skonfigurować współistniejące połączenia](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>Dodawanie bramy

Użyj poniższego polecenia, aby utworzyć bramę. Pamiętaj zastąpić wszelkie wartości swoimi własnymi.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Sprawdź, czy brama została utworzona

Użyj poniższego polecenia, aby sprawdzić, czy brama została utworzona. To polecenie również pobiera identyfikator bramy, które są wymagane dla innych operacji.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Zmień rozmiar bramy

Istnieje szereg [jednostki SKU bramy](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Aby zmienić jednostkę SKU bramy w dowolnym momencie, można użyć następującego polecenia.

> [!IMPORTANT]
> To polecenie nie działa dla bramy UltraPerformance. Aby zmienić brama do bramy UltraPerformance, najpierw usuń istniejącą bramą ExpressRoute, a następnie utwórz nowe bramy UltraPerformance. W przypadku obniżania bramy z bramy UltraPerformance, najpierw usuń bramy UltraPerformance, a następnie utwórz nową bramę. 
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Usuwanie bramy

Użyj poniższego polecenia, aby usunąć bramę

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```
