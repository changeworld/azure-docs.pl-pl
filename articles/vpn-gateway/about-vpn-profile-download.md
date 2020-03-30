---
title: 'Brama sieci VPN platformy Azure: informacje o profilach klientów sieci VPN p2S'
description: Pomaga to w pracy z plikiem profilu klienta
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/17/2020
ms.author: cherylmc
ms.openlocfilehash: b78d32dbb7b4e0d9a3b13ff741e6e38c12be0e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528510"
---
# <a name="about-p2s-vpn-client-profiles"></a>Profile klientów sieci VPN p2S — informacje

Pobrany plik profilu zawiera informacje niezbędne do skonfigurowania połączenia sieci VPN. Ten artykuł pomoże Ci uzyskać i zrozumieć informacje niezbędne dla profilu klienta sieci VPN.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* Folder **OpenVPN** zawiera profil *ovpn,* który musi zostać zmodyfikowany w celu uwzględnienia klucza i certyfikatu. Aby uzyskać więcej informacji, zobacz [Konfigurowanie klientów OpenVPN dla usługi Azure VPN Gateway](vpn-gateway-howto-openvpn-clients.md#windows). Ten folder nie będzie obecny w pliku zip, jeśli uwierzytelnianie usługi Azure AD jest zaznaczone na bramie sieci VPN. Zamiast tego azurevpnconfig.xml będzie w folderze AzureVPN.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat punktu do witryny, zobacz [Informacje o punkcie lokacji](point-to-site-about.md).
