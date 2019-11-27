---
title: 'Włączanie uwierzytelniania wieloskładnikowego dla użytkowników sieci VPN: uwierzytelnianie usługi Azure AD'
description: Włączanie uwierzytelniania wieloskładnikowego dla użytkowników sieci VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 7f05b850a0d886ac0df5c542de647f91fe62eb05
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382210"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Włącz usługę Azure Multi-Factor Authentication (MFA) dla użytkowników sieci VPN

Jeśli chcesz, aby użytkownicy otrzymali monit o drugi czynnik uwierzytelniania przed udzieleniem dostępu, możesz skonfigurować usługę Azure Multi-Factor Authentication (MFA) dla dzierżawy usługi Azure AD. Kroki opisane w tym artykule ułatwiają włączenie wymagania dotyczącego weryfikacji dwuetapowej.

## <a name="prereq"></a>Wymagań wstępnych

Wymaganie wstępne dla tej konfiguracji jest skonfigurowanym dzierżawcą usługi Azure AD przy użyciu kroków opisanych w temacie [Konfigurowanie dzierżawy](openvpn-azure-ad-tenant.md).

## <a name="mfa"></a>Otwieranie strony MFA

1. Zaloguj się do Portalu Azure.
2. Przejdź do **Azure Active Directory — > wszystkich użytkowników**.
3. Wybierz pozycję **Multi-Factor Authentication** , aby otworzyć stronę uwierzytelnianie wieloskładnikowe.

   ![Logowanie](./media/openvpn-azure-ad-mfa/mfa1.jpg)

## <a name="users"></a>Wybieranie użytkowników

1. Na stronie **uwierzytelnianie wieloskładnikowe** wybierz użytkowników, dla których chcesz włączyć usługę MFA.
2. Wybierz pozycję **Włącz**.

   ![Wybierz pozycję](./media/openvpn-azure-ad-mfa/mfa2.jpg)

## <a name="enableauth"></a>Włącz uwierzytelnianie

1. Przejdź do **aplikacji Azure Active Directory-> Enterprise — > wszystkich aplikacji**.
2. Na stronie **aplikacje dla przedsiębiorstw — wszystkie aplikacje** wybierz pozycję **Sieć VPN platformy Azure**.

   ![Identyfikator katalogu](./media/openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a>Konfigurowanie ustawień logowania

Na stronie **właściwości sieci VPN platformy Azure** Skonfiguruj ustawienia logowania.

1. Ustawienie **włączone dla użytkowników do logowania?** na **tak**. Dzięki temu wszyscy użytkownicy w dzierżawie usługi AD mogą pomyślnie łączyć się z siecią VPN.
2. Czy należy ustawić **wymagane przypisanie użytkownika?** na **wartość tak** , jeśli chcesz ograniczyć logowanie tylko do użytkowników, którzy mają uprawnienia do sieci VPN platformy Azure.
3. Zapisz zmiany.

   ![Uprawnienia](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>Następne kroki

Aby nawiązać połączenie z siecią wirtualną, należy utworzyć i skonfigurować profil klienta sieci VPN. Zobacz [Konfigurowanie klienta VPN dla połączeń sieci VPN P2S](openvpn-azure-ad-client.md).
