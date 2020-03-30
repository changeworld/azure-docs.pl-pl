---
title: Jak skonfigurować klientów OpenVPN dla usługi Azure VPN Gateway| Dokumenty firmy Microsoft
description: Kroki konfigurowania klientów OpenVPN dla bramy sieci VPN platformy Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 09ff3ccebad0baa4148e68995254c818a29d7bd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066051"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Konfigurowanie klientów OpenVPN dla bramy sieci VPN platformy Azure

Ten artykuł pomaga skonfigurować klientów **protokołu OpenVPN. &reg; **

## <a name="before-you-begin"></a>Przed rozpoczęciem

Sprawdź, czy wykonano kroki konfigurowania OpenVPN dla bramy sieci VPN. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie usługi OpenVPN dla usługi Azure VPN Gateway](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz, aby klienci sieci VPN mogli uzyskiwać dostęp do zasobów w innej sieci wirtualnej, postępuj zgodnie z instrukcjami w artykule [sieć wirtualna do sieci wirtualnej,](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) aby skonfigurować połączenie vnet-do sieci wirtualnej. Pamiętaj, aby włączyć BGP na bramy i połączenia, w przeciwnym razie ruch nie będzie przepływać.

**"OpenVPN" jest znakiem towarowym firmy OpenVPN Inc.**
