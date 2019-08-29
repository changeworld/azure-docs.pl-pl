---
title: Multi-Factor Authentication platformy Azure — jak działa — Azure Active Directory
description: Usługa Azure Multi-Factor Authentication zabezpiecza dostęp do danych i aplikacji, a jednocześnie spełnia wymagania użytkowników dotyczące prostoty procesu logowania.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57b441803a066b794d17b46afec4d930b94ebdea
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125135"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Jak to działa: Azure Multi-Factor Authentication

Bezpieczeństwo dwuetapowej weryfikacji polega na podejściu warstwowym. Naruszenie wielu czynników uwierzytelniania stanowi znaczące wyzwanie dla osób atakujących. Nawet jeśli osoba atakująca zarządza, aby poznać hasło użytkownika, jest bezużyteczne bez również posiadania dodatkowej metody uwierzytelniania. Działa przez wymaganie co najmniej dwóch następujących metod uwierzytelniania:

* Coś, co wiesz (zazwyczaj hasło)
* Coś, co masz (zaufane urządzenie, które nie jest łatwo duplikowane, takie jak telefon)
* Coś, co masz (biometria)

<center>

![Obraz metod uwierzytelniania koncepcyjnego](./media/concept-mfa-howitworks/methods.png)</center>

Usługa Azure Multi-Factor Authentication (MFA) pomaga w zabezpieczeniu dostępu do danych i aplikacji przy jednoczesnym zachowaniu prostoty dla użytkowników. Zapewnia dodatkowe zabezpieczenia, wymagając drugiej formy uwierzytelniania i zapewnia silne uwierzytelnianie za pośrednictwem różnych [metod uwierzytelniania](concept-authentication-methods.md). Użytkownicy mogą lub nie mogą zakwestionować usługi MFA w oparciu o decyzje konfiguracyjne wykonywane przez administratora.

## <a name="how-to-get-multi-factor-authentication"></a>Jak uzyskać Multi-Factor Authentication?

Multi-Factor Authentication jest częścią następujących ofert:

* **Azure Active Directory — wersja Premium** lub **Microsoft 365 Business** — w pełni funkcjonalne korzystanie z platformy Azure Multi-Factor Authentication przy użyciu zasad dostępu warunkowego, aby wymagać uwierzytelniania wieloskładnikowego.

* **Azure AD — wersja bezpłatna** lub autonomiczne licencje **pakietu Office 365** — Użyj wstępnie utworzonych [zasad ochrony punktu odniesienia dostępu warunkowego](../conditional-access/concept-baseline-protection.md) , aby wymagać uwierzytelniania wieloskładnikowego dla użytkowników i administratorów.

* **Azure Active Directory Administratorzy globalni** — podzbiór funkcji Multi-Factor Authentication platformy Azure jest dostępny jako środek do ochrony kont administratorów globalnych.

> [!NOTE]
> Nowi klienci nie mogą już kupować Multi-Factor Authentication platformy Azure jako autonomicznej oferty obowiązującej 1 września, 2018. Uwierzytelnianie wieloskładnikowe będzie nadal mieć dostępną funkcję w Azure AD — wersja Premium licencji.

## <a name="supportability"></a>Możliwości obsługi

Ponieważ większość użytkowników jest przyzwyczajonych do korzystania tylko z haseł do uwierzytelniania, ważne jest, aby Twoja organizacja komunikuje się ze wszystkimi użytkownikami dotyczącymi tego procesu. Świadomość może zmniejszyć prawdopodobieństwo, że użytkownicy wywołują pomoc techniczną w przypadku drobnych problemów związanych z usługą MFA. Istnieją jednak sytuacje, w których konieczne jest tymczasowe wyłączenie usługi MFA. Skorzystaj z poniższych wskazówek, aby zrozumieć, jak obsługiwać te scenariusze:

* Przeszkol personel pomocy technicznej, aby obsługiwał scenariusze, w których użytkownik nie może się zalogować, ponieważ nie ma dostępu do ich metod uwierzytelniania lub nie działa prawidłowo.
   * Korzystając z zasad dostępu warunkowego dla usługi Azure MFA, pracownicy pomocy technicznej mogą dodać użytkownika do grupy, która jest wykluczona z zasad wymagających uwierzytelniania wieloskładnikowego.
* Rozważ użycie dostępu warunkowego o nazwie Locations jako metody minimalizowania dwuetapowych wierszy weryfikacyjnych. Dzięki tej funkcji Administratorzy mogą ominąć weryfikację dwuetapową dla użytkowników, którzy logują się z bezpiecznej zaufanej lokalizacji sieciowej, takiej jak segment sieci używany na potrzeby dołączania do nowego użytkownika.
* Wdróż [Azure AD Identity Protection](../active-directory-identityprotection.md) i Wyzwól weryfikację dwuetapową opartą na wykryciu ryzyka.

## <a name="next-steps"></a>Następne kroki

- [Wdrożenie krok po kroku na platformie Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
