---
title: 'Włączanie uwierzytelniania wieloskładnikowego dla użytkowników sieci VPN: uwierzytelnianie usługi Azure AD'
description: Włączanie uwierzytelniania wieloskładnikowego dla użytkowników sieci VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 771dea2d9ae2979bc71880368ed3a9a538e8a803
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964734"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Włącz usługę Azure Multi-Factor Authentication (MFA) dla użytkowników sieci VPN

Jeśli chcesz, aby użytkownicy otrzymali monit o drugi czynnik uwierzytelniania przed udzieleniem dostępu, możesz skonfigurować usługę Azure Multi-Factor Authentication (MFA) dla poszczególnych użytkowników lub wykorzystać Multi-Factor Authentication (MFA) za pośrednictwem [dostępu warunkowego](../active-directory/conditional-access/overview.md) , aby uzyskać bardziej szczegółowy formant. Konfigurowanie Multi-Factor Authentication na użytkownika można włączyć bez dodatkowych kosztów, jednak w przypadku włączania usługi MFA na użytkownika zostanie wyświetlony monit o uwierzytelnienie drugiego czynnika dla wszystkich aplikacji powiązanych z dzierżawą usługi Azure AD. Dostęp warunkowy umożliwia dokładniejszą kontrolę nad sposobem promowania drugiego czynnika i umożliwia przypisanie uwierzytelniania MFA tylko do sieci VPN, a nie innych aplikacji powiązanych z dzierżawą usługi Azure AD.

## <a name="enableauth"></a>Włącz uwierzytelnianie

1. Przejdź do **aplikacji Azure Active Directory-> Enterprise — > wszystkich aplikacji**.
2. Na stronie **aplikacje dla przedsiębiorstw — wszystkie aplikacje** wybierz pozycję **Sieć VPN platformy Azure**.

   ![Identyfikator katalogu](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a>Konfigurowanie ustawień logowania

Na stronie **właściwości sieci VPN platformy Azure** Skonfiguruj ustawienia logowania.

1. Ustawienie **włączone dla użytkowników do logowania?** na **tak**. Dzięki temu wszyscy użytkownicy w dzierżawie usługi AD mogą pomyślnie łączyć się z siecią VPN.
2. Czy należy ustawić **wymagane przypisanie użytkownika?** na **wartość tak** , jeśli chcesz ograniczyć logowanie tylko do użytkowników, którzy mają uprawnienia do sieci VPN platformy Azure.
3. Zapisz zmiany.

   ![Uprawnienia](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="option-1---enable-multi-factor-authentication-mfa-via-conditional-access"></a>Opcja 1 — Włączanie Multi-Factor Authentication (MFA) za pośrednictwem dostępu warunkowego

Dostęp warunkowy umożliwia uzyskanie szczegółowej kontroli dostępu dla poszczególnych aplikacji.  Należy pamiętać, że aby korzystać z dostępu warunkowego, należy mieć Azure AD — wersja Premium 1 lub większą licencję dla użytkowników, którzy będą objęci regułami dostępu warunkowego.

1. Na stronie **aplikacje dla przedsiębiorstw — wszystkie aplikacje** wybierz pozycję **Sieć VPN platformy Azure**, wybierz pozycję **dostęp warunkowy**, a następnie kliknij pozycję **nowe zasady**.
2. W obszarze Użytkownicy i grupy na karcie *Dołącz* **Wybierz pozycję Użytkownicy i grupy**, sprawdź **użytkowników i grupy**, a następnie wybierz grupę lub zestaw użytkowników, którzy mają podlegać usłudze MFA.  Kliknij przycisk **Gotowe**.
![Przypisania](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. W obszarze **Grant**(Udziel) zaznacz pole wyboru **Udziel dostępu**, zaznacz pole wyboru **Wymagaj uwierzytelniania wieloskładnikowego**, zaznacz pole wyboru **Wymagaj wszystkich zaznaczonych kontrolek**, a następnie kliknij przycisk **Wybierz** .
![udzielić dostępu-MFA](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. Sprawdź **w** obszarze **Włączanie zasad** i kliknij przycisk **Utwórz** .
![włączyć](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png) zasad

## <a name="option-2---enable-multi-factor-authentication-mfa-per-user"></a>Opcja 2 — Włączanie Multi-Factor Authentication (MFA) na użytkownika

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="next-steps"></a>Następne kroki

Aby nawiązać połączenie z siecią wirtualną, należy utworzyć i skonfigurować profil klienta sieci VPN. Zobacz [Konfigurowanie klienta VPN dla połączeń sieci VPN P2S](openvpn-azure-ad-client.md).
