---
title: Jak to działa usługa Azure MFA — usługa Azure Active Directory
description: Uwierzytelnianie wieloskładnikowe platformy Azure pomaga chronić dostęp do danych i aplikacji, spełniając jednocześnie zapotrzebowanie użytkowników na prosty proces logowania.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39948214f5bd080be417ed515bea6bff87d3b303
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484064"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Jak to działa: usługa Azure Multi-Factor Authentication

Bezpieczeństwo weryfikacji dwuetapowej leży w jej podejściu warstwowym. Naruszenie wielu czynników uwierzytelniania stanowi poważne wyzwanie dla atakujących. Nawet jeśli osobie atakującej uda się nauczyć hasła użytkownika, jest bezużyteczne bez konieczności posiadania dodatkowej metody uwierzytelniania. Działa poprzez wymaganie dwóch lub więcej z następujących metod uwierzytelniania:

* Coś, co znasz (zazwyczaj hasło)
* Coś, co masz (zaufane urządzenie, które nie jest łatwo powielane, jak telefon)
* Coś, czym jesteś (biometria)

<center>

![Obraz metod uwierzytelniania koncepcyjnego](./media/concept-mfa-howitworks/methods.png)</center>

Uwierzytelnianie wieloskładnikowe usługi Azure (MFA) pomaga chronić dostęp do danych i aplikacji przy zachowaniu prostoty dla użytkowników. Zapewnia dodatkowe bezpieczeństwo, wymagając drugiej formy uwierzytelniania i zapewnia silne uwierzytelnianie za pomocą szeregu łatwych w użyciu [metod uwierzytelniania.](concept-authentication-methods.md) Użytkownicy mogą lub nie mogą być kwestionowane dla usługi MFA na podstawie decyzji konfiguracji, które administrator sprawia, że.

## <a name="how-to-get-multi-factor-authentication"></a>Jak uzyskać usługę Multi-Factor Authentication?

Usługa Multi-Factor Authentication jest częścią następujących ofert:

* **Usługa Azure Active Directory Premium** lub **Microsoft 365 Business** — w pełni funkcjonalne korzystanie z uwierzytelniania wieloskładnikowego platformy Azure przy użyciu zasad dostępu warunkowego wymaga uwierzytelniania wieloskładnikowego.

* **Licencje usługi Azure AD Free** lub standalone Office **365** — wymagaj uwierzytelniania wieloskładnikowego dla użytkowników i administratorów za pomocą [ustawień domyślnych zabezpieczeń.](../fundamentals/concept-fundamentals-security-defaults.md)

* **Administratorzy globalni usługi Azure Active Directory** — konta administratorów globalnych mogą być chronione za pomocą podzbioru funkcji usługi Azure Multi-Factor Authentication.

> [!NOTE]
> Nowi klienci nie mogą już kupować uwierzytelniania wieloskładnikowego platformy Azure jako samodzielnej oferty od 1 września 2018 r. Uwierzytelnianie wieloskładnikowe będzie nadal dostępne w licencjach usługi Azure AD Premium.

## <a name="supportability"></a>Możliwości obsługi

Ponieważ większość użytkowników jest przyzwyczajona do używania tylko haseł do uwierzytelniania, ważne jest, aby organizacja komunikowała się ze wszystkimi użytkownikami w związku z tym procesem. Świadomość może zmniejszyć prawdopodobieństwo, że użytkownicy zadzwonią do działu pomocy technicznej w przypadku drobnych problemów związanych z uwierzytelnianiem wieloskładnikowym. Istnieją jednak pewne scenariusze, w których tymczasowe wyłączenie usługi MFA jest konieczne. Aby zrozumieć, jak obsługiwać te scenariusze, należy użyć następujących wskazówek:

* Szkolenie personelu pomocy technicznej do obsługi scenariuszy, w których użytkownik nie może się zalogować, ponieważ nie mają dostępu do swoich metod uwierzytelniania lub nie działają poprawnie.
   * Za pomocą zasad dostępu warunkowego dla usługi Azure MFA, pracownicy pomocy technicznej można dodać użytkownika do grupy, która jest wykluczona z zasad wymagających usługi MFA.
* Rozważ użycie funkcji Dostęp warunkowy o nazwie lokalizacje jako sposobu zminimalizowania monitów weryfikacji dwuetapowej. Dzięki tej funkcji administratorzy mogą pominąć weryfikację dwuetapową dla użytkowników logujących się z bezpiecznej zaufanej lokalizacji sieciowej, takiej jak segment sieciowy używany do dołączania nowych użytkowników.
* Wdrażanie [usługi Azure AD Identity Protection](../active-directory-identityprotection.md) i wyzwalanie weryfikacji dwuetapowej na podstawie wykrywania ryzyka.

## <a name="next-steps"></a>Następne kroki

- [Wdrażanie uwierzytelniania wieloskładnikowego platformy Azure krok po kroku](howto-mfa-getstarted.md)
