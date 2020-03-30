---
title: Zasady usługi Azure AD Identity Protection
description: Identyfikowanie trzech zasad, które są włączone za pomocą usługi Ochrona tożsamości
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
ms.openlocfilehash: 4a44e32efa3e38cf9c4df7dc00e3189c129db418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72887418"
---
# <a name="identity-protection-policies"></a>Zasady ochrony tożsamości

Usługa Azure Active Directory Identity Protection zawiera trzy domyślne zasady, które administratorzy mogą włączyć. Te zasady obejmują ograniczone dostosowywanie, ale mają zastosowanie do większości organizacji. Wszystkie zasady pozwalają na wykluczenie użytkowników, takich jak [dostęp awaryjny lub break-glass konta administratora](../users-groups-roles/directory-emergency-access.md).

![Zasady ochrony tożsamości](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Zasady rejestracji usługi Azure MFA

Ochrona tożsamości może pomóc organizacjom wdrożyć usługę Azure Multi-Factor Authentication (MFA) przy użyciu zasad dostępu warunkowego wymagających rejestracji podczas logowania. Włączenie tych zasad to świetny sposób na zapewnienie, że nowi użytkownicy w organizacji zarejestrowali się w u administratorów usługi MFA pierwszego dnia. Uwierzytelnianie wieloskładnikowe jest jedną z metod samoradowania zdarzeń ryzyka w ramach ochrony tożsamości. Samozakadowanie umożliwia użytkownikom samodzielne podejmowanie działań w celu zmniejszenia liczby połączeń w pomocy technicznej.

Więcej informacji na temat uwierzytelniania wieloskładnikowego platformy Azure można znaleźć w artykule [Jak to działa: Uwierzytelnianie wieloskładnikowe platformy Azure](../authentication/concept-mfa-howitworks.md).

## <a name="sign-in-risk-policy"></a>Zasady dotyczące ryzyka logowania

Usługa Identity Protection analizuje sygnały z każdego logowania, zarówno w czasie rzeczywistym, jak i w trybie offline, i oblicza ocenę ryzyka na podstawie prawdopodobieństwa, że logowanie nie zostało wykonane przez użytkownika. Administratorzy mogą podejmować decyzje na podstawie tego sygnału oceny ryzyka, aby wymusić wymagania organizacyjne. Administratorzy mogą blokować dostęp, zezwalać na dostęp lub zezwalać na dostęp, ale wymagają uwierzytelniania wieloskładnikowego.

Jeśli zostanie wykryte ryzyko, użytkownicy mogą wykonywać uwierzytelnianie wieloskładnikowe w celu samodzielnego korygowania i zamykania ryzykownego zdarzenia logowania, aby zapobiec niepotrzebnemu hałasowi dla administratorów.

> [!NOTE] 
> Użytkownicy muszą wcześniej zarejestrować się dla usługi Azure Uwierzytelnianie wieloskładnikowe przed wyzwoleniem zasad ryzyka logowania.

### <a name="custom-conditional-access-policy"></a>Niestandardowe zasady dostępu warunkowego

Administratorzy mogą również utworzyć niestandardowe zasady dostępu warunkowego, w tym ryzyko logowania jako warunek przypisania. Więcej informacji na temat dostępu warunkowego można znaleźć w artykule [Co to jest dostęp warunkowy?](../conditional-access/overview.md)

![Zasady ryzyka logowania niestandardowego dostępu warunkowego](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Zasady dotyczące ryzyka użytkownika

Ochrona tożsamości można obliczyć, co uważa za normalne dla zachowania użytkownika i używać go do oparcie decyzji dla ich ryzyka. Ryzyko użytkownika jest obliczenie prawdopodobieństwa, że tożsamość została naruszona. Administratorzy mogą podejmować decyzje na podstawie tego sygnału oceny ryzyka, aby wymusić wymagania organizacyjne. Administratorzy mogą zablokować dostęp, zezwolić na dostęp lub zezwolić na dostęp, ale wymagają zmiany hasła przy użyciu [samoobsługowego resetowania hasła usługi Azure AD.](../authentication/howto-sspr-deployment.md)

Jeśli zostanie wykryte ryzyko, użytkownicy mogą wykonać samoobsługowe resetowanie hasła w celu samodzielnego korygowania i zamknięcia zdarzenia ryzyka użytkownika, aby zapobiec niepotrzebnemu hałasowi dla administratorów.

> [!NOTE] 
> Użytkownicy muszą wcześniej zarejestrować się do samodzielnego resetowania hasła przed wyzwoleniem zasad ryzyka użytkownika.

## <a name="next-steps"></a>Następne kroki

- [Włączanie samoobsługowego resetowania hasła usługi Azure AD](../authentication/howto-sspr-deployment.md)

- [Włączanie usługi Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

- [Włączanie zasad rejestracji uwierzytelniania wieloskładnikowego platformy Azure](howto-identity-protection-configure-mfa-policy.md)

- [Włączanie zasad logowania i ryzyka użytkownika](howto-identity-protection-configure-risk-policies.md)
