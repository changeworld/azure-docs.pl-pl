---
title: Konfigurowanie zawsze włączonego tunelu użytkownika sieci VPN
titleSuffix: Azure VPN Gateway
description: W tym artykule opisano sposób konfigurowania tunelu użytkownika zawsze włączonej sieci VPN dla bramy sieci VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 56934dd13661d8f623e673e2817e87618675c7ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502261"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Konfigurowanie zawsze włączonego tunelu VPN użytkownika

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Konfigurowanie bramy

 Użyj instrukcji zawartych w [artykule Konfigurowanie połączenia sieci VPN typu punkt-lokacja,](vpn-gateway-howto-point-to-site-resource-manager-portal.md) aby skonfigurować bramę sieci VPN do używania protokołu IKEv2 i uwierzytelniania opartego na certyfikatach.

## <a name="configure-a-user-tunnel"></a>Konfigurowanie tunelu użytkownika

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Aby usunąć profil

Aby usunąć profil, należy wykonać następujące czynności:

1. Uruchom następujące polecenie:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Odłącz połączenie i wyczyść pole wyboru **Automatycznie połącz.**

   ![Czyszczenie](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Następne kroki

Aby rozwiązać wszelkie problemy z połączeniem, które mogą wystąpić, zobacz [Problemy z połączeniem typu punkt-lokacja platformy Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
