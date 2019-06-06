---
title: Uwierzytelnianie wieloskładnikowe systemu Azure — jak działa - usługi Azure Active Directory
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
ms.openlocfilehash: fa25e8a965b89c4e97263e3767a9400079fcad7a
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496802"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Jak to działa: Azure Multi-Factor Authentication

Zabezpieczenia weryfikacji dwuetapowej znajduje się w jego warstwowego podejścia. Naruszenie wiele składników uwierzytelniania przedstawia znaczące wyzwane dla osób atakujących. Nawet wtedy, gdy osoba atakująca zarządza się hasło użytkownika, jest bezcelowe również bez posiadania dodatkową metodę uwierzytelniania. To działa, wymagając dwie lub więcej z następujących metod uwierzytelniania:

* Coś, co wiesz (zwykle hasła)
* Coś, co masz (zaufane urządzenie, którego nie można łatwo zduplikować, takie jak telefon)
* Coś, co Ciebie cechuje (biometria)

<center>

![Obraz metod uwierzytelniania koncepcyjne](./media/concept-mfa-howitworks/methods.png)</center>

Usługa Azure Multi-Factor Authentication (MFA) zabezpiecza dostęp do danych i aplikacji przy zachowaniu uproszczenia dla użytkowników. Zapewnia dodatkowe zabezpieczenia, wymagając od drugiej formy uwierzytelniania i zapewnia silne uwierzytelnianie za pomocą szeregu łatwy w użyciu [metod uwierzytelniania](concept-authentication-methods.md). Użytkownicy mogą lub nie mogą być kwestionowane usługi MFA, w oparciu o decyzji związanych z konfiguracją, wprowadzane przez administratora.

## <a name="how-to-get-multi-factor-authentication"></a>Jak uzyskać uwierzytelnianie wieloskładnikowe?

Uwierzytelnianie Multi-Factor Authentication jest dostępna w ramach następujących ofert:

* **Usługa Azure Active Directory — wersja Premium** lub **Microsoft 365 Business** — w pełni funkcjonalne korzystanie z usługi Azure Multi-Factor Authentication za pomocą zasad dostępu warunkowego, aby wymagać uwierzytelniania wieloskładnikowego.

* **Azure AD bezpłatna**, **usługi Azure AD podstawowa**, lub autonomicznych **usługi Office 365** licencje — Użyj wstępnie utworzone [zasady ochrony linii bazowej dostępu warunkowego](../conditional-access/concept-baseline-protection.md) będą musieli uwierzytelnianie wieloskładnikowe dla użytkowników i administratorów.

* **Azure Active Directory administratorzy globalni** — niektóre funkcje usługi Azure Multi-Factor Authentication są dostępne jako sposób na ochronę kont administratora globalnego.

> [!NOTE]
> Nowi klienci nie mogą kupować usługi Azure Multi-Factor Authentication jako autonomiczny oferty od 1 września 2018 r. Uwierzytelnianie wieloskładnikowe będzie dostępna funkcja licencje usługi Azure AD Premium w.

## <a name="supportability"></a>Możliwości obsługi

Ponieważ większość użytkowników są przyzwyczajeni do korzystania z tylko hasła do uwierzytelniania, ważne jest, że Twoja organizacja komunikuje się do wszystkich użytkowników dotyczące tego procesu. Rozpoznawanie może zmniejszyć prawdopodobieństwo, że użytkownicy wywołania pomocy technicznej dla drobne problemy związane z usługi MFA. Jednak istnieją pewne scenariusze, w których tymczasowo wyłączyć uwierzytelnianie wieloskładnikowe jest konieczne. Skorzystaj z poniższych wskazówek, aby zrozumieć sposób obsługi tych scenariuszy:

* Szkolenie z personelem pomocy technicznej do obsługi scenariuszy, w których użytkownik nie może zalogować, ponieważ nie mają dostępu do metody ich uwierzytelniania lub nie działają prawidłowo.
   * Za pomocą zasad dostępu warunkowego dla usługi MFA Azure z personelem pomocy technicznej można dodać użytkownika do grupy, który jest wykluczony z zasad, wymaganie usługi MFA.
* Należy wziąć pod uwagę przy użyciu dostępu warunkowego, lokalizacje z nazwą jak sposób, aby zminimalizować weryfikacji dwuetapowej monity. Dzięki tej funkcji, Administratorzy mogą pominięciu weryfikacji dwuetapowej dla użytkowników, którzy logują z lokalizacji bezpiecznej, zaufanej sieci, takiej jak sieć segmentu umożliwiający dołączanie nowych użytkowników.
* Wdrażanie [usługi Azure AD Identity Protection](../active-directory-identityprotection.md) i wyzwolić weryfikację dwuetapową, w oparciu o zdarzenia o podwyższonym ryzyku.

## <a name="next-steps"></a>Kolejne kroki

- [Instrukcje krok po kroku wdrożenia usługi Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
