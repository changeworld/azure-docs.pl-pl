---
title: 'Jak skonfigurować OpenVPN w usłudze Azure VPN Gateway: PowerShell| Microsoft Docs'
description: Kroki, aby skonfigurować OpenVPN dla bramy sieci VPN platformy Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: 609c2ef91fafe0ae955252a594292d861e772f87
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002960"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Konfigurowanie OpenVPN Azure point-to-site VPN Gateway

Ten artykuł pomoże Ci skonfigurować **OpenVPN® protokołu** w usłudze Azure VPN Gateway. Tego artykułu przyjęto założenie, iż już środowisku point-to-site pracy. Jeśli tego nie zrobisz, użyj instrukcji w kroku 1, aby utworzyć sieci VPN punkt lokacja.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="vnet"></a>1. Tworzenie sieci VPN punkt lokacja

Jeśli nie masz już działający środowiska point-to-site, postępuj zgodnie z instrukcjami Aby utworzyć jeden. Zobacz [tworzenie sieci VPN punkt lokacja](vpn-gateway-howto-point-to-site-resource-manager-portal.md) do tworzenia i konfigurowania bramy sieci VPN typu punkt lokacja przy użyciu uwierzytelniania certyfikatu platformy Azure natywnych. 

> [!IMPORTANT]
> Podstawowa jednostka SKU nie jest obsługiwana dla OpenVPN.

## <a name="enable"></a>2. Włącz OpenVPN bramy

Włącz OpenVPN na bramie. Upewnij się, że brama została już skonfigurowana do punkt lokacja (protokół IKEv2 lub SSTP) przed uruchomieniem poniższych poleceń:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Kolejne kroki

Aby skonfigurować klientów OpenVPN, zobacz [klientom Konfigurowanie OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" jest zastrzeżonym znakiem towarowym firmy OpenVPN Inc.**
