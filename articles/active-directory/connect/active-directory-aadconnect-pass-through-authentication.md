---
title: 'Azure AD Connect: Uwierzytelniania przekazywanego | Dokumentacja firmy Microsoft'
description: W tym artykule opisano uwierzytelniania przekazywanego usługi Azure Active Directory (Azure AD) i jak umożliwia logowania usługi Azure AD przez sprawdzanie poprawności hasła użytkowników z lokalnej usługi Active Directory.
services: active-directory
keywords: Co to jest Azure AD Connect przekazywanego uwierzytelniania, należy zainstalować usługi Active Directory, wymaganych składników dla usługi Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: billmath
ms.openlocfilehash: 377a8255961a4c7fa55f8bbc5cb98ae4deff3205
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Logowanie użytkownika do uwierzytelniania przekazywanego Azure Active Directory

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Co to jest Azure uwierzytelniania przekazywanego usługi Active Directory?

Azure Active Directory (Azure AD) przekazywanego uwierzytelniania umożliwia użytkownikom logowanie się zarówno lokalnie, jak i aplikacji działających w chmurze przy użyciu takich samych haseł w. Ta funkcja zapewnia użytkownikom lepsze środowisko — jedno hasło mniej do zapamiętania i ogranicza koszty pomocy technicznej IT, ponieważ są rzadziej zapomnieć, jak zalogować się użytkownicy. Podczas logowania przy użyciu usługi Azure AD, ta funkcja sprawdza poprawność haseł użytkowników bezpośrednio z lokalnej usługi Active Directory.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Ta funkcja stanowi alternatywę dla [synchronizacji skrótu hasła programu Azure AD](active-directory-aadconnectsync-implement-password-hash-synchronization.md), którym znajdują się takie same korzyści uwierzytelniania w chmurze dla organizacji. Jednak zasady zabezpieczeń i zgodności w niektórych organizacjach nie zezwolić na te organizacje do wysłania hasła użytkowników, nawet w formie skrótu, poza ich granice wewnętrznego. Uwierzytelniania przekazywanego to odpowiednie rozwiązanie dla tych organizacji.

![Azure AD przekazywanego uwierzytelniania](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

Możesz łączyć uwierzytelniania przekazywanego z [bezproblemowe logowanie jednokrotne](active-directory-aadconnect-sso.md) funkcji. Dzięki temu, gdy użytkownicy uzyskują dostęp do aplikacji na komputerach firmowych w sieci firmowej nie muszą o wpisanie ich haseł do logowania.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Najważniejsze zalety stosowania uwierzytelniania usługi Azure AD przekazywania

- *Środowisko użytkowników*
  - Użytkownicy używać tego samego hasła do logowania się do zarówno lokalnie, jak i aplikacje oparte na chmurze.
  - Użytkownicy poświęcają mniej czasu rozmowie z pomocy technicznej rozpoznawania skojarzone z hasłem problemów IT.
  - Użytkownicy mogą wykonać [zarządzania hasłami samoobsługi](../authentication/active-directory-passwords-overview.md) zadania w chmurze.
- *Łatwe wdrażanie i administrowanie*
  - Nie jest konieczne złożone lokalnego wdrożenia i konfiguracji sieci.
  - Należy po prostu lekkie agenta być zainstalowana na lokalnym.
  - Nie koszty zarządzania. Agent automatycznie otrzymuje ulepszeń i poprawek usterek.
- *Bezpieczeństwo*
  - Lokalne hasła nigdy nie są przechowywane w chmurze w jakimkolwiek formularzu.
  - Agent tylko sprawia, że połączenia wychodzące z sieci. W związku z tym jest wymagany do zainstalowania agenta w sieci obwodowej, znanej także jako strefa DMZ.
  - Chroni kont użytkowników przy stosowaniu bezproblemowo [zasady dostępu warunkowego AD Azure](../active-directory-conditional-access-azure-portal.md), łącznie z usługi Multi-Factor Authentication (MFA) i przez [filtrowanie ataków siłowych hasła](active-directory-aadconnect-pass-through-authentication-smart-lockout.md).
- *Wysokiej dostępności*
  - Dodatkowe agentów można zainstalować na wielu serwerach lokalnych, aby zapewnić wysoką dostępność żądań logowania.

## <a name="feature-highlights"></a>Zawiera opis funkcji

- Obsługuje logowanie użytkownika do wszystkich aplikacji opartej na przeglądarce sieci web i aplikacje klienckie Microsoft Office, które używają [nowoczesnego uwierzytelniania](https://aka.ms/modernauthga).
- Nazwy logowania użytkowników może być albo lokalnymi domyślna nazwa użytkownika (`userPrincipalName`) lub inny atrybut skonfigurowane w programie Azure AD Connect (nazywane `Alternate ID`).
- Funkcja bezproblemowo współdziała z [dostępu warunkowego](../active-directory-conditional-access-azure-portal.md) funkcje takie jak usługi Multi-Factor Authentication (MFA) do zabezpieczania użytkowników.
- Zintegrowana z opartej na chmurze [zarządzania hasłami samoobsługi](../authentication/active-directory-passwords-overview.md), włączając funkcję zapisywania zwrotnego haseł do lokalnej usługi Active Directory i ochrona za pomocą hasła przy zakaz najczęściej używanych haseł.
- Środowiska z wieloma lasami są obsługiwane, jeśli istnieją relacje zaufania lasu między z lasów usługi AD i Jeśli routing sufiksów nazw jest poprawnie skonfigurowany.
- Jest bezpłatna funkcji i nie wymagają żadnych wersji płatnej usługi Azure AD z niego korzystać.
- Można ją włączyć za pomocą [Azure AD Connect](active-directory-aadconnect.md).
- Używa agenta lekkie lokalnymi, który odbiera i odpowiada na żądania sprawdzania poprawności hasła.
- Instalowanie wielu agentów zapewnia wysoką dostępność żądań logowania.
- On [chroni](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) złamania hasła w chmurze force konta lokalnego przed metodą siłową.

## <a name="next-steps"></a>Kolejne kroki

- [**Szybki Start** ](active-directory-aadconnect-pass-through-authentication-quick-start.md) — Uzyskaj i systemem Azure AD przekazywanego uwierzytelniania.
- [**Inteligentne blokady** ](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) — funkcja Konfigurowanie blokady inteligentnej na dzierżawy do ochrony kont użytkowników.
- [**Bieżące ograniczenia** ](active-directory-aadconnect-pass-through-authentication-current-limitations.md) — Dowiedz się, jakie scenariusze są obsługiwane i zostały.
- [**Nowości techniczne** ](active-directory-aadconnect-pass-through-authentication-how-it-works.md) -zrozumieć sposób działania tej funkcji.
- [**Często zadawane pytania** ](active-directory-aadconnect-pass-through-authentication-faq.md) — odpowiedzi na często zadawane pytania.
- [**Rozwiązywanie problemów z** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) — Dowiedz się, jak rozwiązać typowe problemy z funkcją.
- [**Nowości zabezpieczeń** ](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) -uzyskać dodatkowe informacje techniczne głębokość tę funkcję.
- [**Azure AD SSO bezproblemowe** ](active-directory-aadconnect-sso.md) — Dowiedz się więcej o tej funkcji uzupełniające.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) — w przypadku zgłoszenia żądania nowych funkcji.
