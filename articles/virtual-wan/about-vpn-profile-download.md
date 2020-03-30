---
title: 'Wirtualna sieć WAN platformy Azure: profile klientów sieci VPN użytkowników'
description: Pomaga to w pracy z plikiem profilu klienta
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: b88be5cccdaeb452288a2cb2f1dd4b4690a8b274
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066209"
---
# <a name="working-with-user-vpn-client-profiles"></a>Praca z profilami klientów sieci VPN użytkowników

Pobrany plik profilu zawiera informacje niezbędne do skonfigurowania połączenia sieci VPN. Ten artykuł pomaga uzyskać i zrozumieć informacje niezbędne dla profilu klienta sieci VPN użytkownika.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* Folder **OpenVPN** zawiera profil *ovpn,* który musi zostać zmodyfikowany w celu uwzględnienia klucza i certyfikatu. Aby uzyskać więcej informacji, zobacz [Konfigurowanie klientów OpenVPN](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat sieci VPN użytkownika [wirtualnej](virtual-wan-point-to-site-portal.md)sieci WAN, zobacz Tworzenie połączenia sieci VPN użytkownika .
