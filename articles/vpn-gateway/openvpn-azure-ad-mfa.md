---
title: 'Włączanie uwierzytelniania wieloskładnikowego dla użytkowników sieci VPN: uwierzytelnianie usługi Azure AD | Microsoft Docs'
description: Włączanie uwierzytelniania wieloskładnikowego dla użytkowników sieci VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: alzam
ms.openlocfilehash: 1b506f8439deabd110daaefab36442140e20d0d4
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185367"
---
# <a name="enable-multi-factor-authentication-mfa-for-vpn-users"></a>Włącz Multi-Factor Authentication (MFA) dla użytkowników sieci VPN

Możesz włączyć usługę MFA dla użytkowników w dzierżawie usługi Azure AD, aby użytkownicy otrzymali monit o uwierzytelnienie drugiego czynnika przed udzieleniem dostępu.

> [!NOTE]
> Wymaganie wstępne: skonfigurowano dzierżawę usługi Azure AD zgodnie z opisem w dokumencie "Konfigurowanie dzierżawy".
>

### <a name="tenant"></a>1. Zaloguj się do Azure Portal i przejdź do **Azure Active Directory** , **Wszyscy użytkownicy** i kliknij **Multi-Factor Authentication**


   ![Nowa dzierżawa usługi Azure AD](./media/openvpn-azure-ad-mfa/mfa1.jpg)

### <a name="users"></a>2. Wybierz użytkowników, dla których chcesz włączyć usługę MFA, a następnie kliknij pozycję **Włącz** .

   ![Nowa dzierżawa usługi Azure AD](./media/openvpn-azure-ad-mfa/mfa2.jpg)

### <a name="enable-authentication"></a>3. Przejdź do **Azure Active Directory** , **aplikacje dla przedsiębiorstw**, **wszystkie aplikacje** i kliknij pozycję **Azure VPN**


   ![Identyfikator katalogu](./media/openvpn-azure-ad-mfa/user1.jpg)

### <a name="users"></a>4. Upewnij się, że **Użytkownicy mogą się logować?** ma ustawioną wartość tak. Jeśli chcesz, aby tylko użytkownicy, którzy mają uprawnienia do sieci VPN platformy Azure, mogli się zalogować, a następnie ustawić **wymagane przypisanie użytkownika?** do tak, jak również wszyscy użytkownicy w dzierżawie usługi AD będą mogli pomyślnie nawiązywać połączenia z siecią VPN.

   ![Uprawnienia](./media/openvpn-azure-ad-mfa/user2.jpg)


## <a name="next-steps"></a>Kolejne kroki

Aby można było połączyć się z siecią wirtualną, należy utworzyć i skonfigurować profil klienta sieci VPN. Zobacz [Konfigurowanie klienta VPN dla połączeń sieci VPN P2S](openvpn-azure-ad-client.md).
