---
title: Środowisko użytkownika za pomocą usługi Azure AD Identity Protection
description: Środowisko użytkownika usługi Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc10fb4f9894a355c9eed024ae9f87747214999b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72887002"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>Środowisko użytkownika za pomocą usługi Azure AD Identity Protection

Za pomocą usługi Azure Active Directory Identity Protection można:

* Wymagaj od użytkowników rejestrowania się w celu uwierzytelniania wieloskładnikowego usługi Azure
* Automatyzacja korygowania ryzykownych logów i użytkowników, których bezpieczeństwo zostało naruszone

Wszystkie zasady ochrony tożsamości mają wpływ na środowisko logowania dla użytkowników. Umożliwienie użytkownikom rejestrowania się i używania narzędzi, takich jak usługa Azure MFA i samoobsługowe resetowanie hasła, może zmniejszyć wpływ. Te narzędzia wraz z odpowiednimi opcjami zasad daje użytkownikom opcję samozaradania, gdy jej potrzebują.

## <a name="multi-factor-authentication-registration"></a>Rejestracja uwierzytelniania wieloskładnikowego

Włączenie zasad ochrony tożsamości wymagających rejestracji uwierzytelniania wieloskładnikowego i kierowania na wszystkich użytkowników, upewnij się, że mają możliwość korzystania z usługi Azure MFA do samodzielnego korygowania w przyszłości. Skonfigurowanie tej zasady daje użytkownikom 14-dniowy okres, w którym mogą wybrać rejestrację, a na końcu są zmuszeni do rejestracji. Środowisko dla użytkowników jest opisane poniżej. Więcej informacji można znaleźć w dokumentacji użytkownika końcowego w artykule, [Omówienie weryfikacji dwuskładnikowej oraz konta służbowego lub szkolnego.](../user-help/user-help-two-step-verification-overview.md)

### <a name="registration-interrupt"></a>Przerwanie rejestracji

1. Podczas logowania do dowolnej aplikacji zintegrowanej z usługą Azure AD użytkownik otrzymuje powiadomienie o wymaganiu skonfigurowania konta do uwierzytelniania wieloskładnikowego. Ta zasada jest również wyzwalana w systemie Windows 10 Out of Box Experience dla nowych użytkowników z nowym urządzeniem.
   
    ![Więcej informacji wymaganych](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. Wykonaj kroki z przewodnikiem, aby zarejestrować się w usłudze Azure Multi-Factor Authentication i zakończyć logowanie.

## <a name="risky-sign-in-remediation"></a>Ryzykowne korygowanie logowania

Gdy administrator skonfigurował zasady dla ryzyka logowania, użytkownicy, których dotyczy problem, są powiadamiani, gdy próbują się zalogować i wyzwolić poziom ryzyka zasad. 

### <a name="risky-sign-in-self-remediation"></a>Ryzykowne samorekultywowanie logowania

1. Użytkownik jest informowany, że wykryto coś niezwykłego o ich logowania, takich jak logowanie się z nowej lokalizacji, urządzenia lub aplikacji.
   
    ![Coś niezwykłego monitu](./media/concept-identity-protection-user-experience/120.png)

1. Użytkownik jest zobowiązany do udowodnienia swojej tożsamości, wypełniając usługi Azure MFA za pomocą jednej z wcześniej zarejestrowanych metod. 

### <a name="risky-sign-in-administrator-unblock"></a>Ryzykowne odblokowanie administratora logowania

Administratorzy mogą blokować użytkowników po zalogowaniu się w zależności od poziomu ryzyka. Aby odblokować, użytkownicy końcowi muszą skontaktować się ze swoim personelem IT lub mogą spróbować zalogować się ze znanej lokalizacji lub urządzenia. Samoremediacja przez wykonywanie uwierzytelniania wieloskładnikowego nie jest opcją w tym przypadku.

![Zablokowane przez zasady ryzyka logowania](./media/concept-identity-protection-user-experience/200.png)

Pracownicy IT mogą postępować zgodnie z instrukcjami w sekcji [Odblokowywanie użytkowników,](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk) aby umożliwić użytkownikom zalogowanie się z powrotem.

## <a name="risky-user-remediation"></a>Ryzykowne korygowanie przez użytkownika

Po skonfigurowaniu zasad ryzyka użytkownika użytkownicy, którzy spełniają prawdopodobieństwo naruszenia poziomu ryzyka użytkownika, muszą przejść przez przepływ odzyskiwania naruszenia zabezpieczeń użytkownika, zanim będą mogli się zalogować. 

### <a name="risky-user-self-remediation"></a>Ryzykowne samorekultywacja użytkownika

1. Użytkownik jest informowany, że ich bezpieczeństwo konta jest zagrożone z powodu podejrzanej aktywności lub wycieku poświadczeń.
   
    ![Korekty](./media/concept-identity-protection-user-experience/101.png)

1. Użytkownik jest zobowiązany do udowodnienia swojej tożsamości, wypełniając usługi Azure MFA za pomocą jednej z wcześniej zarejestrowanych metod. 
1. Wreszcie użytkownik jest zmuszony do zmiany hasła przy użyciu samoobsługowego resetowania hasła, ponieważ ktoś inny mógł mieć dostęp do swojego konta.

## <a name="risky-sign-in-administrator-unblock"></a>Ryzykowne odblokowanie administratora logowania

Administratorzy mogą blokować użytkowników po zalogowaniu się w zależności od poziomu ryzyka. Aby odblokować, użytkownicy końcowi muszą skontaktować się ze swoim personelem IT. Samozaradianie przez wykonywanie uwierzytelniania wieloskładnikowego i samoobsługowego resetowania hasła nie jest w tym przypadku opcją.

![Zablokowane przez zasady dotyczące ryzyka użytkownika](./media/concept-identity-protection-user-experience/104.png)

Pracownicy IT mogą postępować zgodnie z instrukcjami w sekcji [Odblokowywanie użytkowników,](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk) aby umożliwić użytkownikom zalogowanie się z powrotem.

## <a name="see-also"></a>Zobacz też

- [Korygowanie ryzyka i odblokowywanie użytkowników](howto-identity-protection-remediate-unblock.md)

- [Ochrona tożsamości w usłudze Azure Active Directory](../active-directory-identityprotection.md) 
