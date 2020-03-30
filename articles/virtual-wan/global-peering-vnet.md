---
title: Konfigurowanie komunikacji równorzędnej globalnej sieci wirtualnej dla wirtualnej sieci WAN platformy Azure | Dokumenty firmy Microsoft
description: Podłącz sieć wirtualną w innym regionie do centrum wirtualnej sieci WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 340472f84d2dd2c4f46d180992745a57e8ad1884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73588228"
---
# <a name="configure-global-vnet-peering-cross-region-vnet-for-virtual-wan"></a>Konfigurowanie globalnej sieci równorzędnej sieci wirtualnej (międzyregionowej sieci wirtualnej) dla wirtualnej sieci WAN

Sieć wirtualną w innym regionie można połączyć z koncentratorem wirtualnej sieci WAN.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Sprawdź, czy spełniasz następujące kryteria:

* Sieć wirtualna między regionami (szprycha) nie jest połączona z innym koncentratorem wirtualnej sieci WAN. Szprycha może być podłączona tylko do jednego koncentratora wirtualnego.
* Sieć wirtualna (szprycha) nie zawiera bramy sieci wirtualnej (na przykład bramy sieci wirtualnej usługi Azure VPN gateway lub bramy sieci wirtualnej usługi ExpressRoute). Jeśli sieć wirtualna zawiera bramę sieci wirtualnej, należy usunąć bramę przed nawiązaniem połączenia sieci wirtualnej szprychy z koncentratorem.

## <a name="register-this-feature"></a><a name="register"></a>Rejestrowanie tej funkcji

Tę funkcję można zarejestrować za pomocą programu PowerShell. Jeśli wybierzesz "Wypróbuj" w poniższym przykładzie, usługa Azure Cloud-Shell zostanie otwarta i nie trzeba będzie instalować poleceń cmdlet programu PowerShell lokalnie na komputerze. W razie potrzeby można zmienić subskrypcje za pomocą polecenia cmdlet 'Select-AzSubscription -SubscriptionId'. <subid>

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Network'
```

## <a name="verify-registration"></a><a name="verify"></a>Weryfikowanie rejestracji

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
```

## <a name="connect-a-vnet-to-the-hub"></a><a name="hub"></a>Podłączanie sieci wirtualnej do koncentratora

W tym kroku utworzysz połączenie komunikacji równorzędnej między koncentratorem a siecią wirtualną między regionami. Powtórz te czynności dla każdej sieci wirtualnej, z którą chcesz się połączyć.

1. Na stronie sieci wirtualnej WAN kliknij pozycję **Połączenia sieci wirtualnych**.
2. Na stronie połączenia sieci wirtualnej kliknij polecenie **+ Dodaj połączenie**.
3. Na stronie **Dodaj połączenie** wypełnij następujące pola:

    * **Nazwa połączenia** — nazwij połączenie.
    * **Koncentratory** — wybierz koncentrator, z którym chcesz skojarzyć to połączenie.
    * **Subskrypcja** — sprawdź, czy wybrano właściwą subskrypcję.
    * **Sieć wirtualna** — wybierz sieć wirtualną, którą chcesz połączyć z tym koncentratorem. Sieć wirtualna nie może mieć istniejącej bramy sieci wirtualnej.
4. Kliknij przycisk **OK**, aby utworzyć połączenie równorzędne.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o wirtualnej sieci WAN, zobacz [Omówienie wirtualnej sieci WAN](virtual-wan-about.md).