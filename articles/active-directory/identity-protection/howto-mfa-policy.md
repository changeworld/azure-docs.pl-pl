---
title: Jak skonfigurować zasady rejestracji uwierzytelniania wieloskładnikowego w usłudze Azure Active Directory Identity Protection | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować zasady rejestracji uwierzytelniania wieloskładnikowego Azure AD Identity Protection.
services: active-directory
keywords: Usługa Azure active directory identity protection odnajdywania aplikacji w chmurze, zarządzanie aplikacji, zabezpieczenia, ryzyka, poziom ryzyka, luk w zabezpieczeniach, zasady zabezpieczeń
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: abd1049551d7dbc4823636dfdc00f64afab72cdf
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60008600"
---
# <a name="how-to-configure-the-multi-factor-authentication-registration-policy"></a>Instrukcje: Skonfiguruj zasady rejestracji uwierzytelniania wieloskładnikowego

Usługa Azure AD Identity Protection pomaga w zarządzaniu wdrożenie rejestracji uwierzytelniania wieloskładnikowego (MFA), konfigurując zasady, które wymagają rejestracji usługi MFA. W tym artykule opisano, jakie zasady mogą służyć do i sposobu ich konfigurowania.

## <a name="what-is-the-multi-factor-authentication-registration-policy"></a>Co to są zasady rejestracji uwierzytelniania wieloskładnikowego?

Usługa Azure Multi-Factor authentication jest metodą weryfikacji tożsamości, która wymaga użycia więcej niż tylko nazwy użytkownika i hasła. Zapewnia drugą warstwę zabezpieczeń do logowania użytkowników i transakcji. Aby użytkownicy mogli odpowiadanie na monity funkcji uwierzytelniania Wieloskładnikowego muszą najpierw zarejestrować usługi MFA. 

Firma Microsoft zaleca, wymagają usługi Azure Multi-Factor authentication do logowania użytkownika, ponieważ jego:

- Zapewnia silne uwierzytelnianie z szerokim zakresem prostych opcji weryfikacji

- Odgrywa kluczową rolę w przygotowywanie organizacji do ochrony i odzyskiwania z naruszeń konta


Aby uzyskać szczegółowe informacje na temat uwierzytelniania Wieloskładnikowego, zobacz [co to jest uwierzytelnianie wieloskładnikowe systemu Azure?](../authentication/multi-factor-authentication.md)


## <a name="how-do-i-access-the-mfa-registration-policy"></a>Jak uzyskać dostęp do zasad rejestracji usługi MFA?
   
Zasady rejestracji usługi MFA znajduje się w **Konfiguruj** sekcji na [strony usługi Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Zasady MFA](./media/howto-mfa-policy/1014.png)




## <a name="policy-settings"></a>Ustawienia zasad

Po skonfigurowaniu zasad rejestracji MFA, musisz ustawić:

- Użytkownicy i grupy, które zostaną zastosowane zasady:

    ![Użytkownicy i grupy](./media/howto-mfa-policy/11.png)

- Formant, który chcesz wymusić wymaganie rejestracji usługi MFA:  

    ![Dostęp](./media/howto-mfa-policy/12.png)

- Stan zasad:

    ![Wymuszanie zasad](./media/howto-mfa-policy/14.png)


W oknie dialogowym konfiguracji zasad zapewnia możliwość szacunkowa ocena wpływu konfigurację.

![Szacowany wpływ](./media/howto-mfa-policy/15.png)




## <a name="user-experience"></a>Środowisko użytkownika


Omówienie środowiska użytkownika Zobacz:

* [Uwierzytelnianie wieloskładnikowe rejestracji w usłudze flow](flows.md#multi-factor-authentication-registration).  
* [Logowania środowisk przy użyciu usługi Azure AD Identity Protection](flows.md).  



## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać omówienie programu Azure AD Identity Protection, zobacz [Omówienie usługi Azure AD Identity Protection](overview.md).
