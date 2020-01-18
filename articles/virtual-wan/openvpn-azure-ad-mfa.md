---
title: 'Włączanie uwierzytelniania wieloskładnikowego dla użytkowników sieci VPN: uwierzytelnianie usługi Azure AD'
description: Włączanie uwierzytelniania wieloskładnikowego dla użytkowników sieci VPN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: alzam
ms.openlocfilehash: fcb60e80189da89b3f634c14582be606307536e6
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166680"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Włącz usługę Azure Multi-Factor Authentication (MFA) dla użytkowników sieci VPN

Jeśli chcesz, aby użytkownicy otrzymali monit o drugi czynnik uwierzytelniania przed udzieleniem dostępu, możesz skonfigurować usługę Azure Multi-Factor Authentication (MFA) dla dzierżawy usługi Azure AD. Kroki opisane w tym artykule ułatwiają włączenie wymagania dotyczącego weryfikacji dwuetapowej.

## <a name="prereq"></a>Wymagań wstępnych

Wymaganie wstępne dla tej konfiguracji jest skonfigurowanym dzierżawcą usługi Azure AD przy użyciu kroków opisanych w temacie [Konfigurowanie dzierżawy](openvpn-azure-ad-tenant.md).

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="enablesign"></a>Konfigurowanie ustawień logowania

Na stronie **właściwości sieci VPN platformy Azure** Skonfiguruj ustawienia logowania.

1. Ustawienie **włączone dla użytkowników do logowania?** na **tak**. Dzięki temu wszyscy użytkownicy w dzierżawie usługi AD mogą pomyślnie łączyć się z siecią VPN.
2. Czy należy ustawić **wymagane przypisanie użytkownika?** na **wartość tak** , jeśli chcesz ograniczyć logowanie tylko do użytkowników, którzy mają uprawnienia do sieci VPN platformy Azure.
3. Zapisz zmiany.

   ![Uprawnienia](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>Następne kroki

Aby nawiązać połączenie z siecią wirtualną, należy utworzyć i skonfigurować profil klienta sieci VPN. Zobacz [Konfigurowanie uwierzytelniania usługi Azure AD na potrzeby połączenia punkt-lokacja z platformą Azure](virtual-wan-point-to-site-azure-ad.md).
