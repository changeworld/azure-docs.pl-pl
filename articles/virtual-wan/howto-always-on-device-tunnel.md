---
title: Konfigurowanie zawsze włączonego tunelu sieci VPN
titleSuffix: Azure Virtual WAN
description: Kroki konfigurowania tunelu urządzenia Always On VPN dla wirtualnej sieci WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: e3eea639eaa52c07e877476e9215144e916618d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502856"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Konfigurowanie tunelu urządzenia zawsze włączonego sieci VPN dla wirtualnej sieci WAN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Należy utworzyć konfigurację punktu do lokacji i edytować przypisanie koncentratora wirtualnego. Instrukcje dotyczące następujących sekcji można znaleźć w następujących sekcjach:

* [Tworzenie konfiguracji P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Edytowanie przypisania centrum](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-the-device-tunnel"></a>Konfigurowanie tunelu urządzenia

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Aby usunąć profil

Aby usunąć profil, uruchom następujące polecenie:

![Czyszczenie](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz często zadawane [pytania](virtual-wan-faq.md).