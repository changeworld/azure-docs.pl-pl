---
title: Uwierzytelnianie wieloskładnikowe systemu Azure — jak działa - usługi Azure Active Directory
description: Usługa Azure Multi-Factor Authentication zabezpiecza dostęp do danych i aplikacji, a jednocześnie spełnia wymagania użytkowników dotyczące prostoty procesu logowania.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7328fb958774b5e17511d046e914cc5612e8a96d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60415836"
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

* **Licencje usługi Azure Active Directory Premium** — w pełni funkcjonalne korzystania z usługi Azure Multi-Factor Authentication (chmura) lub serwer usługi Azure Multi-Factor Authentication (lokalnie).
   * **Usługa Azure MFA (chmura)** - **ta opcja jest zalecana ścieżki o nowych wdrożeniach**. Usługa Azure MFA w chmurze wymaga nie infrastruktury lokalnej i mogą być używane z użytkowników federacyjnych lub tylko w chmurze.
   * **Serwer usługi Azure MFA** — Jeśli Twoja organizacja chce, aby zarządzać elementami powiązaną infrastrukturą i wdrożeniu usług AD FS w środowisku lokalnych w ten sposób może być opcją.
* **Uwierzytelnianie wieloskładnikowe dla usługi Office 365** — niektóre funkcje usługi Azure Multi-Factor Authentication są dostępne jako część Twojej subskrypcji. Aby uzyskać więcej informacji na temat usługi MFA dla usługi Office 365, zobacz artykuł [planowanie uwierzytelniania wieloskładnikowego dla pakietu Office 365 wdrożeń](https://support.office.com/article/plan-for-multi-factor-authentication-for-office-365-deployments-043807b2-21db-4d5c-b430-c8a6dee0e6ba).
* **Azure Active Directory administratorzy globalni** — niektóre funkcje usługi Azure Multi-Factor Authentication są dostępne jako sposób na ochronę kont administratora globalnego.

> [!NOTE]
> Nowi klienci nie mogą kupować usługi Azure Multi-Factor Authentication jako autonomiczny oferty od 1 września 2018 r. Uwierzytelnianie wieloskładnikowe będzie dostępna funkcja licencje usługi Azure AD Premium w.

## <a name="supportability"></a>Możliwości obsługi

Ponieważ większość użytkowników są przyzwyczajeni do korzystania z tylko hasła do uwierzytelniania, ważne jest, że Twoja organizacja komunikuje się do wszystkich użytkowników dotyczące tego procesu. Rozpoznawanie może zmniejszyć prawdopodobieństwo, że użytkownicy wywołania pomocy technicznej dla drobne problemy związane z usługi MFA. Jednak istnieją pewne scenariusze, w których tymczasowo wyłączyć uwierzytelnianie wieloskładnikowe jest konieczne. Skorzystaj z poniższych wskazówek, aby zrozumieć sposób obsługi tych scenariuszy:

* Szkolenie z personelem pomocy technicznej do obsługi scenariuszy, w których użytkownik nie może zalogować, ponieważ nie mają dostępu do metody ich uwierzytelniania lub nie działają prawidłowo.
   * Za pomocą zasad dostępu warunkowego dla usługi MFA Azure z personelem pomocy technicznej można dodać użytkownika do grupy, który jest wykluczony z zasad, wymaganie usługi MFA.
   * Działu pomocy technicznej można włączyć tymczasowe jednorazowe obejście dla użytkowników serwera usługi Azure MFA umożliwia użytkownikowi uwierzytelnienie bez weryfikacji dwuetapowej. Obejście jest tymczasowe i wygasa po określonej liczbie sekund.   
* Należy rozważyć użycie zaufanych adresów IP lub nazwane lokalizacje jako sposób, aby zminimalizować monity weryfikacji dwuetapowej. Dzięki tej funkcji Administratorzy dzierżawy zarządzanych lub federacyjnego można pominąć weryfikacji dwuetapowej dla użytkowników, którzy są logowanie z zaufanej lokalizacji sieciowej takich jak intranet w organizacji.
* Wdrażanie [usługi Azure AD Identity Protection](../active-directory-identityprotection.md) i wyzwolić weryfikację dwuetapową, w oparciu o zdarzenia o podwyższonym ryzyku.

## <a name="next-steps"></a>Kolejne kroki

- Uzyskaj instrukcje krok po kroku MFA [plan wdrożenia](https://aka.ms/MFADeploymentPlan)

- Uzyskanie szczegółowych informacji o [licencjonowaniu użytkowników](concept-mfa-licensing.md)

- Uzyskanie szczegółowych informacji o [wersji do wdrożenia](concept-mfa-whichversion.md)

- Uzyskanie odpowiedzi na [często zadawane pytania](multi-factor-authentication-faq.md)
