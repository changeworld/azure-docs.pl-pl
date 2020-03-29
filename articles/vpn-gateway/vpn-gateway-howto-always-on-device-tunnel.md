---
title: Konfigurowanie zawsze włączonego tunelu sieci VPN
titleSuffix: Azure VPN Gateway
description: Kroki konfigurowania tunelu zawsze włączonej sieci VPN dla bramy sieci VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 9036992a354b41cd75735e5ccd895d7287a00e91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371765"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Konfigurowanie tunelu zawsze włączonego urządzenia sieci VPN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Konfigurowanie bramy

Skonfiguruj bramę sieci VPN do używania protokołu IKEv2 i uwierzytelniania opartego na certyfikatach przy użyciu artykułu [Konfigurowanie połączenia sieci VPN typu punkt-lokacja.](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

## <a name="configure-the-device-tunnel"></a>Konfigurowanie tunelu urządzenia

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Aby usunąć profil

Aby usunąć profil, uruchom następujące polecenie:

![Czyszczenie](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Następne kroki

Aby rozwiązać problem, zobacz [Problemy z połączeniem typu punkt-lokacja platformy Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
