---
title: Jak skonfigurować zasady rejestracji uwierzytelniania wieloskładnikowego w programie Azure Active Directory Identity Protection | Microsoft Docs
description: Dowiedz się, jak skonfigurować Azure AD Identity Protection zasady rejestracji w usłudze uwierzytelniania wieloskładnikowego.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/01/2019
author: MicrosoftGuyJFlo
manager: daveba
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27ff7512bb3f9422ed4c8edd7ab50fce23f0ed07
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499559"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Instrukcje: Konfigurowanie zasad rejestracji w usłudze Azure MFA

Azure AD Identity Protection ułatwia zarządzanie przeprowadzeniem rejestracji usługi uwierzytelniania wieloskładnikowego (MFA) przez skonfigurowanie zasad dostępu warunkowego, aby wymagać rejestracji MFA niezależnie od tego, która aplikacja uwierzytelniania jest zapisywana. W tym artykule wyjaśniono, jak można używać zasad i jak je skonfigurować.



## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Co to są zasady rejestracji w usłudze Azure MFA

Usługa Azure MFA Authentication umożliwia sprawdzenie, kto jest używany więcej niż nazwa użytkownika i hasło. Zapewnia drugą warstwę zabezpieczeń do logowania użytkowników. Aby użytkownicy mogli odpowiadać na monity usługi MFA, muszą najpierw zarejestrować się na potrzeby uwierzytelniania wieloskładnikowego Azure.

Zalecamy wymaganie uwierzytelniania wieloskładnikowego w usłudze Azure dla użytkowników, ponieważ:

- Zapewnia silne uwierzytelnianie dzięki szerokiemu zakresowi opcji łatwej weryfikacji
- Odgrywa kluczową rolę w przygotowaniu organizacji do ochrony i odzyskiwania po zdarzeniach ryzyka w ramach usługi Identity Protection

Aby uzyskać więcej informacji na temat usługi MFA, zobacz [co to jest uwierzytelnianie wieloskładnikowe w systemie Azure?](../authentication/howto-mfa-getstarted.md)

## <a name="how-do-i-access-the-registration-policy"></a>Jak mogę uzyskać dostępu do zasad rejestracji?

Zasady rejestracji MFA znajduje się w sekcji **Konfigurowanie** na [stronie Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).

![Zasady MFA](./media/howto-mfa-policy/1014.png)

## <a name="policy-settings"></a>Ustawienia zasad

Konfigurując zasady rejestracji MFA, należy wprowadzić następujące zmiany w konfiguracji:

- Użytkownicy i grupy, których dotyczą zasady. Pamiętaj, aby wykluczyć [konta dostępu awaryjnego](../users-groups-roles/directory-emergency-access.md)w organizacji.

    ![Użytkownicy i grupy](./media/howto-mfa-policy/11.png)

- Kontrolka, którą chcesz wymusić — **Wymagaj rejestracji w usłudze Azure MFA**

    ![Access](./media/howto-mfa-policy/12.png)

- Dla zasad Wymuś należy ustawić wartość **włączone**.

    ![Wymuś zasady](./media/howto-mfa-policy/14.png)

- **Zapisywanie** zasad

## <a name="user-experience"></a>Środowisko użytkownika

Azure Active Directory Identity Protection będzie monitować użytkowników o zarejestrowanie przy następnym logowaniu się w sposób interaktywny.

Aby zapoznać się z omówieniem środowiska użytkownika powiązanego, zobacz:

- [Przepływ rejestracji usługi uwierzytelniania](flows.md#multi-factor-authentication-registration)wieloskładnikowego.  
- [Środowisko logowania przy użyciu Azure AD Identity Protection](flows.md).  

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem Azure AD Identity Protection, zobacz [omówienie Azure AD Identity Protection](overview.md).
