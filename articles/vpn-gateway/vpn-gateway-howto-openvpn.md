---
title: 'Jak skonfigurować openvpn na usługi Azure VPN Gateway: PowerShell'
description: Kroki konfigurowania usługi OpenVPN for Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: 7505420cc31fe751ecc0c114a89fea0734cbc6cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162411"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Konfigurowanie usługi OpenVPN dla bramy sieci VPN typu punkt-lokacja platformy Azure

Ten artykuł ułatwia konfigurowanie **protokołu OpenVPN®** na bramie sieci VPN platformy Azure. W tym artykule przyjęto założenie, że masz już środowisko robocze punkt do lokacji. Jeśli tego nie zrobisz, użyj instrukcji w kroku 1, aby utworzyć sieć VPN typu punkt-lokacja.



## <a name="1-create-a-point-to-site-vpn"></a><a name="vnet"></a>1. Tworzenie sieci VPN typu punkt-lokacja

Jeśli nie masz jeszcze działającego środowiska typu punkt-lokacja, postępuj zgodnie z instrukcją, aby je utworzyć. Zobacz [Tworzenie sieci VPN typu lokacja w celu](vpn-gateway-howto-point-to-site-resource-manager-portal.md) utworzenia i skonfigurowania bramy sieci VPN typu punkt-lokacja z natywnym uwierzytelnianiem certyfikatów platformy Azure. 

> [!IMPORTANT]
> Podstawowa jednostka SKU nie jest obsługiwana dla openvpn.

## <a name="2-enable-openvpn-on-the-gateway"></a><a name="enable"></a>2. Włącz OpenVPN na bramie

Włącz OpenVPN na swojej bramie. Przed uruchomieniem następujących poleceń upewnij się, że brama jest już skonfigurowana dla lokalizacji typu "w punkcie lokacji" (IKEv2 lub SSTP):

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować klientów dla OpenVPN, zobacz [Konfigurowanie klientów OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" jest znakiem towarowym firmy OpenVPN Inc.**
