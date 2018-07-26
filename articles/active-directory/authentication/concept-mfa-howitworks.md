---
title: Uwierzytelnianie wieloskładnikowe systemu Azure — jak to działa
description: Usługa Azure Multi-Factor Authentication zabezpiecza dostęp do danych i aplikacji, a jednocześnie spełnia wymagania użytkowników dotyczące prostoty procesu logowania.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 25af61ce51828691d08210f4089afa0cf56b9846
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257855"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Jak to działa: Usługa Azure Multi-Factor Authentication

Zabezpieczenia weryfikacji dwuetapowej znajduje się w jego warstwowego podejścia. Naruszenie wiele składników uwierzytelniania przedstawia znaczące wyzwane dla osób atakujących. Nawet wtedy, gdy osoba atakująca zarządza się hasło użytkownika, jest bezcelowe również bez posiadania dodatkową metodę uwierzytelniania. To działa, wymagając dwie lub więcej z następujących metod uwierzytelniania:

* Coś, co wiesz (zwykle hasła)
* Coś, co masz (zaufane urządzenie, którego nie można łatwo zduplikować, takie jak telefon)
* Coś, co Ciebie cechuje (biometria)

<center>![Obraz metod uwierzytelniania koncepcyjne](./media/concept-mfa-howitworks/methods.png)</center>

Usługa Azure Multi-Factor Authentication (MFA) zabezpiecza dostęp do danych i aplikacji przy zachowaniu uproszczenia dla użytkowników. Zapewnia dodatkowe zabezpieczenia, wymagając od drugiej formy uwierzytelniania i zapewnia silne uwierzytelnianie za pomocą szeregu łatwy w użyciu [metod uwierzytelniania](concept-authentication-methods.md).

## <a name="how-to-get-multi-factor-authentication"></a>Jak uzyskać uwierzytelnianie wieloskładnikowe?

Uwierzytelnianie Multi-Factor Authentication jest dostępna w ramach następujących ofert:

* **Licencje usługi Azure Active Directory Premium** — w pełni funkcjonalne korzystania z usługi Azure Multi-Factor Authentication (chmura) lub serwer usługi Azure Multi-Factor Authentication (lokalnie).
   * **Usługa Azure MFA (chmura)** - **ta opcja jest zalecana ścieżki o nowych wdrożeniach**. Usługa Azure MFA w chmurze wymaga nie infrastruktury lokalnej i mogą być używane z użytkowników federacyjnych lub tylko w chmurze.
   * **Serwer usługi Azure MFA** — Jeśli Twoja organizacja chce, aby zarządzać elementami powiązaną infrastrukturą i wdrożeniu usług AD FS w danym środowisku lokalnej w ten sposób może być opcją.
* **Uwierzytelnianie wieloskładnikowe dla usługi Office 365** — niektóre funkcje usługi Azure Multi-Factor Authentication są dostępne jako część Twojej subskrypcji. Aby uzyskać więcej informacji na temat usługi MFA dla usługi Office 365, zobacz artykuł [planowanie uwierzytelniania wieloskładnikowego dla pakietu Office 365 wdrożeń](https://support.office.com/article/plan-for-multi-factor-authentication-for-office-365-deployments-043807b2-21db-4d5c-b430-c8a6dee0e6ba).
* **Azure Active Directory administratorzy globalni** — niektóre funkcje usługi Azure Multi-Factor Authentication są dostępne jako sposób na ochronę kont administratora globalnego.

### <a name="auth-provider-or-mfa-license"></a>Licencja dostawcy uwierzytelniania lub uwierzytelnianie wieloskładnikowe

Jeśli masz usługi Azure AD Premium lub pakiet licencji, która obejmuje usługi Azure AD Premium, masz już usługę Azure MFA. Twoja organizacja nie musi nic więcej, aby rozszerzyć możliwości weryfikacji dwuetapowej dla wszystkich użytkowników. Musisz przypisać licencję do użytkownika, a następnie włączyć uwierzytelnianie wieloskładnikowe.

Jeśli nie masz licencji, które obejmują usługi Azure MFA lub nie masz wystarczającą liczbę licencji, aby obejmowały wszystkich użytkowników, możesz utworzyć [dostawcy usługi MFA](concept-mfa-authprovider.md) rozszerzenie pełnego zestawu funkcji uwierzytelniania Wieloskładnikowego dla użytkowników, którzy ich potrzebują. 

> [!IMPORTANT]
> Jeśli nie masz wystarczającą liczbę licencji dla wszystkich użytkowników, można utworzyć dostawcy usługi Multi-Factor Authentication na użytkownika, aby pokrywał pozostałej części organizacji. Nie należy tworzyć dostawcy usługi Multi-Factor Authentication na uwierzytelnienie. Jeśli to zrobisz, może wystąpić płacić za weryfikację żądań od użytkowników, które już masz licencje.

## <a name="supportability"></a>Możliwości obsługi

Ponieważ większość użytkowników są przyzwyczajeni do korzystania z tylko hasła do uwierzytelniania, ważne jest, że Twoja organizacja komunikuje się do wszystkich użytkowników dotyczące tego procesu. Rozpoznawanie może zmniejszyć prawdopodobieństwo, że użytkownicy wywołania pomocy technicznej dla drobne problemy związane z usługi MFA. Jednak istnieją pewne scenariusze, w których tymczasowo wyłączyć uwierzytelnianie wieloskładnikowe jest konieczne. Skorzystaj z poniższych wskazówek, aby zrozumieć sposób obsługi tych scenariuszy:

* Szkolenie z personelem pomocy technicznej do obsługi scenariuszy, w których użytkownik nie może zalogować, ponieważ nie mają dostępu do metody ich uwierzytelniania lub nie działają prawidłowo.
   * Za pomocą zasad dostępu warunkowego dla usługi MFA Azure z personelem pomocy technicznej można dodać użytkownika do grupy, który jest wykluczony z zasad, wymaganie usługi MFA.
   * Działu pomocy technicznej można włączyć tymczasowe jednorazowe obejście dla użytkowników serwera usługi Azure MFA umożliwia użytkownikowi uwierzytelnienie bez weryfikacji dwuetapowej. Obejście jest tymczasowe i wygasa po określonej liczbie sekund.
   * Za pomocą zasad dostępu warunkowego dla usługi MFA Azure z personelem pomocy technicznej można dodać użytkownika do grupy, który jest wykluczony z zasad, wymaganie usługi MFA.
* Należy rozważyć użycie zaufanych adresów IP lub nazwane lokalizacje jako sposób, aby zminimalizować monity weryfikacji dwuetapowej. Dzięki tej funkcji Administratorzy dzierżawy zarządzanych lub federacyjnego można pominąć weryfikacji dwuetapowej dla użytkowników, którzy są logowanie z zaufanej lokalizacji sieciowej takich jak intranet w organizacji.
* Wdrażanie [usługi Azure AD Identity Protection](../active-directory-identityprotection.md) i wyzwolić weryfikację dwuetapową, w oparciu o zdarzenia o podwyższonym ryzyku.

## <a name="next-steps"></a>Kolejne kroki

- Uzyskaj instrukcje krok po kroku MFA [plan wdrożenia](https://aka.ms/MFADeploymentPlan)

- Informacje o [licencji użytkowników](concept-mfa-licensing.md)

- Uzyskiwanie szczegółowych informacji [wersji do wdrożenia](concept-mfa-whichversion.md)

- Znajdź odpowiedzi na [— często zadawane pytania](multi-factor-authentication-faq.md)
