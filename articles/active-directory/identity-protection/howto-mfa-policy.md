---
title: Jak skonfigurować zasady rejestracji uwierzytelniania wieloskładnikowego w usłudze Azure Active Directory Identity Protection | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować zasady rejestracji uwierzytelniania wieloskładnikowego Azure AD Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.date: 05/01/2019
author: MicrosoftGuyJFlo
manager: daveba
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 434d07163713a139b42a5dbe1664f81dafc2a1ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108937"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Instrukcje: Skonfiguruj zasady rejestracji usługi Azure Multi-Factor Authentication

Usługa Azure AD Identity Protection pomaga w zarządzaniu wdrożenie rejestracji uwierzytelniania wieloskładnikowego (MFA), konfigurując zasady dostępu warunkowego, które wymagają rejestracji usługi MFA, niezależnie od tego, jakie aplikacji logujesz się. W tym artykule opisano, jakie zasady mogą służyć do i sposobu ich konfigurowania.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Co to są zasady rejestracji usługi Azure Multi-Factor Authentication?

Usługa Azure Multi-Factor Authentication udostępnia środki do weryfikacji swojej tożsamości w przypadku korzystania z więcej niż tylko nazwy użytkownika i hasła. Zapewnia drugą warstwę zabezpieczeń do logowania użytkowników. Aby użytkownicy mogli odpowiadanie na monity funkcji uwierzytelniania Wieloskładnikowego muszą najpierw zarejestrować dla usługi Azure Multi-Factor Authentication.

Firma Microsoft zaleca, wymagają usługi Azure Multi-Factor Authentication do logowania użytkownika, ponieważ jego:

- Zapewnia silne uwierzytelnianie z szerokim zakresem prostych opcji weryfikacji
- Odgrywa kluczową rolę w przygotowywanie organizacji do ochrony i odzyskiwania z zdarzeń o podwyższonym ryzyku w Identity Protection

Aby uzyskać szczegółowe informacje na temat uwierzytelniania Wieloskładnikowego, zobacz [co to jest uwierzytelnianie wieloskładnikowe systemu Azure?](../authentication/howto-mfa-getstarted.md)

## <a name="how-do-i-access-the-registration-policy"></a>Jak uzyskać dostęp do zasad rejestracji?

Zasady rejestracji usługi MFA znajduje się w **Konfiguruj** sekcji na [strony usługi Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).

![Zasady MFA](./media/howto-mfa-policy/1014.png)

## <a name="policy-settings"></a>Ustawienia zasad

Po skonfigurowaniu zasad rejestracji MFA, musisz wprowadzić następujące zmiany w konfiguracji:

- Użytkownicy i grupy, które zostaną zastosowane zasady. Pamiętaj, aby wykluczyć organizacji [kont dostępu awaryjnego](../users-groups-roles/directory-emergency-access.md).

    ![Użytkownicy i grupy](./media/howto-mfa-policy/11.png)

- Formant chcesz wymusić - **rejestracji wymagają usługi Azure MFA**

    ![Access](./media/howto-mfa-policy/12.png)

- Wymuszanie zasad powinien być ustawiony na **na**.

    ![Wymuszanie zasad](./media/howto-mfa-policy/14.png)

- **Zapisz** zasad

## <a name="user-experience"></a>Środowisko użytkownika

Omówienie środowiska użytkownika Zobacz:

- [Uwierzytelnianie wieloskładnikowe rejestracji w usłudze flow](flows.md#multi-factor-authentication-registration).  
- [Logowania środowisk przy użyciu usługi Azure AD Identity Protection](flows.md).  

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać omówienie programu Azure AD Identity Protection, zobacz [Omówienie usługi Azure AD Identity Protection](overview.md).
