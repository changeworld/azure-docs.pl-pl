---
title: Włączanie Multi-Factor Authentication dla Twojej organizacji — Azure Active Directory
description: Włącz usługę Azure MFA dla Twojej organizacji w oparciu o licencję
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84da00b7d6ef49fcdcc91bf6374d8c8f48a034da
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848701"
---
# <a name="enable-multi-factor-authentication-for-your-organization"></a>Włączanie Multi-Factor Authentication dla Twojej organizacji

Istnieje wiele sposobów włączania usługi Azure Multi-Factor Authentication (MFA) dla użytkowników Azure Active Directory (AD) na podstawie licencji należących do organizacji. 

![Zbadaj sygnały i wymuś uwierzytelnianie MFA w razie konieczności](./media/concept-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

W oparciu o nasze studia Twoje konto ma więcej niż 99,9% mniej trudniejsze do naruszenia w przypadku korzystania z usługi MFA.

Aby zapewnić, że Twoja organizacja domyślnie włączy uwierzytelnianie wieloskładnikowe, zanim staną się statystyką?

## <a name="free-option"></a>Opcja bezpłatna

Klienci korzystający z bezpłatnych korzyści płynących z usługi Azure AD mogą używać [ustawień domyślnych zabezpieczeń](../conditional-access/concept-conditional-access-security-defaults.md) w celu włączenia uwierzytelniania wieloskładnikowego w swoich środowiskach.

## <a name="office-365"></a>Office 365

W przypadku klientów z pakietem Office 365 dostępne są dwie opcje:

- [Ustawienia domyślne zabezpieczeń](../conditional-access/concept-conditional-access-security-defaults.md) można włączyć za pomocą usługi Azure AD w celu ochrony wszystkich użytkowników za pomocą usługi Azure Multi-Factor Authentication.
- Jeśli Twoja organizacja wymaga większego stopnia szczegółowości w dostarczaniu uwierzytelniania wieloskładnikowego, licencje pakietu Office obejmują funkcje [MFA dla poszczególnych użytkowników](../authentication/howto-mfa-userstates.md) . Uwierzytelnianie wieloskładnikowe dla użytkowników jest włączane i wymuszane dla każdego użytkownika indywidualnie przez administratorów.

## <a name="azure-ad-premium-p1"></a>Usługa Azure AD — warstwa Premium P1

W przypadku klientów mających Azure AD — wersja Premium P1 lub podobne licencje zawierające te funkcje, takie jak Enterprise Mobility + Security E3, Microsoft 365 F1 lub Microsoft 365 E3: 

Zalecane jest używanie [zasad dostępu warunkowego](../conditional-access/concept-conditional-access-policy-common.md) w celu zapewnienia najlepszego środowiska użytkownika.

## <a name="azure-ad-premium-p2"></a>Usługa Azure AD — warstwa Premium P2

W przypadku klientów mających Azure AD — wersja Premium P2 lub podobne licencje zawierające te funkcje, takie jak Enterprise Mobility + Security E5 lub Microsoft 365 E5: 

Zalecenie polega na użyciu [zasad dostępu warunkowego](../conditional-access/concept-conditional-access-policy-common.md) oraz zasad dotyczących zagrożeń związanych z [ochroną tożsamości](../identity-protection/overview-v2.md) w celu uzyskania najlepszego środowiska użytkownika i elastyczność wymuszania.

## <a name="authentication-methods"></a>Metody uwierzytelniania

|   | Domyślne ustawienia zabezpieczeń | Wszystkie inne metody |
| --- | --- | --- |
| Powiadomienie przez aplikację mobilną | X | X |
| Kod weryfikacyjny z aplikacji mobilnej lub tokenu sprzętowego |   | X |
| Wiadomość SMS na telefon |   | X |
| Połączenie z telefonem |   | X |
| Hasła aplikacji |   | X * * |

\* * Hasła aplikacji są dostępne tylko w ramach usługi MFA dla poszczególnych użytkowników ze starszymi scenariuszami uwierzytelniania tylko wtedy, gdy są włączone przez administratorów.

## <a name="next-steps"></a>Następne kroki

[Strona cennika usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
