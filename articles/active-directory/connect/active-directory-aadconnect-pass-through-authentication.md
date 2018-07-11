---
title: 'Azure AD Connect: Uwierzytelnianie przekazywane | Dokumentacja firmy Microsoft'
description: W tym artykule opisano uwierzytelnianie przekazywane usługi Azure Active Directory (Azure AD) i jak pozwala logowania usługi Azure AD, sprawdzając poprawność haseł użytkowników w usłudze Active Directory w środowisku lokalnym.
services: active-directory
keywords: Co to jest usługa Azure AD Connect uwierzytelniania przekazywanego, instalowanie usługi Active Directory, wymaganych składników dla usługi Azure AD, logowania jednokrotnego, logowanie jednokrotne
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2d88bf5d20beb9de9bf4a0cdcb43548d0d582779
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917282"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Logowanie użytkownika przy użyciu uwierzytelniania przekazywanego usługi Azure Active Directory

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Co to jest uwierzytelnianie przekazywane usługi Azure Active Directory?

Uwierzytelnianie przekazywane usługi Azure Active Directory (Azure AD) umożliwia użytkownikom logować się do zarówno lokalnych, jak i aplikacji działających w chmurze przy użyciu tego samego hasła. Ta funkcja zapewnia użytkownikom lepsze środowisko — jedno mniej hasło do zapamiętania i zmniejsza koszty pomocy technicznej IT, ponieważ użytkownicy są mniej prawdopodobne, należy zapominać, jak zalogować. Gdy użytkownicy logują się przy użyciu usługi Azure AD, ta funkcja sprawdza poprawność haseł użytkowników bezpośrednio w odniesieniu do usługi Active Directory w środowisku lokalnym.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Ta funkcja stanowi alternatywę [synchronizacji skrótów haseł usługi Azure AD](active-directory-aadconnectsync-implement-password-hash-synchronization.md), który zapewnia takie same korzyści uwierzytelniania w chmurze dla organizacji. Jednak zasady zabezpieczeń i zgodności w niektórych organizacjach nie zezwalają na występowanie takich organizacjach do wysłania haseł użytkowników, nawet w formie skrótu, poza jego granicami wewnętrznego. Uwierzytelnianie przekazywane jest właściwe rozwiązanie dla tych organizacji.

![Uwierzytelnianie przekazywane usługi Azure AD](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

Można połączyć uwierzytelniania przekazywanego z [bezproblemowego logowania jednokrotnego](active-directory-aadconnect-sso.md) funkcji. Dzięki temu, gdy użytkownicy uzyskują dostęp do aplikacji na swoich komputerach firmowych, w sieci firmowej nie muszą wpisać swoje hasła do logowania.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Najważniejsze zalety stosowania uwierzytelniania przekazywanego usługi AD platformy Azure

- *Doskonałe środowisko użytkownika*
  - Użytkownicy używać tego samego hasła do logowania się do zarówno lokalnych, jak i aplikacji działających w chmurze.
  - Użytkownicy poświęcają mniej czasu, rozmawiając z działu pomocy technicznej rozwiązywanie związane z hasłami problemów informatycznych.
  - Użytkownicy mogą wykonać [Samoobsługowe zarządzanie hasłami](../authentication/active-directory-passwords-overview.md) zadania w chmurze.
- *Ułatwia wdrażanie i administrowanie*
  - Nie ma potrzeby konfiguracji sieci lub złożonych lokalnych wdrożeń.
  - Należy po prostu uproszczone agenta być zainstalowane w środowisku lokalnym.
  - Nie narzutu związanego z zarządzaniem. Agent automatycznie otrzymuje ulepszeń i poprawek błędów.
- *Bezpieczeństwo*
  - Hasłami lokalnymi nigdy nie są przechowywane w chmurze w dowolnej postaci.
  - Agent wykonuje tylko połączenia wychodzące z sieci. Dlatego jest wymagany do zainstalowania agenta w sieci obwodowej, znanej także jako strefa DMZ.
  - Chroni Twoje konta użytkownika poprzez bezproblemowe współdziałanie z [zasady dostępu warunkowego usługi Azure AD](../active-directory-conditional-access-azure-portal.md), w tym uwierzytelnianie wieloskładnikowe (MFA) i przez [odfiltrowanie siłowego złamania hasła](../authentication/howto-password-smart-lockout.md).
- *O wysokiej dostępności*
  - Dodatkowych agentów można zainstalować na wielu serwerach w środowisku lokalnym, aby zapewnić wysoką dostępność żądań logowania.

## <a name="feature-highlights"></a>Funkcja wyróżnia

- Obsługuje logowanie użytkownika do wszystkich aplikacji opartych na przeglądarce sieci web i aplikacji klienckich Microsoft Office, które używają [nowoczesnego uwierzytelniania](https://aka.ms/modernauthga).
- Nazwy logowania użytkowników może być albo w środowisku lokalnym domyślna nazwa użytkownika (`userPrincipalName`) lub inny atrybut skonfigurowanego w Azure AD Connect (nazywane `Alternate ID`).
- Funkcja bezproblemowo współdziała z [dostępu warunkowego](../active-directory-conditional-access-azure-portal.md) funkcje takie jak usługi Multi-Factor Authentication (MFA) do zabezpieczania użytkowników.
- Zintegrowana z usługą chmurze [Samoobsługowe zarządzanie hasłami](../authentication/active-directory-passwords-overview.md), łącznie z zapisywaniem zwrotnym haseł do lokalnej usługi Active Directory i zapewniają ochronę haseł przez blokowanie najczęściej używanych haseł.
- Obsługiwane są środowisk wielu lasów, jeśli istnieją relacje zaufania lasu między lasami usługi AD i routing sufiksów nazw jest poprawnie skonfigurowana.
- Jest bezpłatną funkcją, które nie potrzebują żadnych płatnej wersji usługi Azure AD z niego korzystać.
- Można ją włączyć za pomocą [program Azure AD Connect](active-directory-aadconnect.md).
- Korzysta ona z agentem uproszczone w środowisku lokalnym, który odbiera i odpowiada na żądania weryfikacji hasła.
- Instalowanie wielu agentów zapewnia wysoką dostępność żądań logowania.
- Jego [chroni](../authentication/howto-password-smart-lockout.md) kont lokalnych przeciwko słownikowym wymusić złamania hasła w chmurze.

## <a name="next-steps"></a>Kolejne kroki

- [**Przewodnik Szybki Start** ](active-directory-aadconnect-pass-through-authentication-quick-start.md) — Rozpocznij pracę, a systemem uwierzytelniania przekazywanego usługi AD platformy Azure.
- [**Blokada Smart** ](../authentication/howto-password-smart-lockout.md) — możliwości konfigurowania inteligentnej blokady w dzierżawie usługi ochrony kont użytkowników.
- [**Bieżące ograniczenia** ](active-directory-aadconnect-pass-through-authentication-current-limitations.md) — Dowiedz się, jakie scenariusze są obsługiwane i te, które nie są.
- [**Rozbudowana technicznie** ](active-directory-aadconnect-pass-through-authentication-how-it-works.md) -zrozumienie sposobu działania tej funkcji.
- [**Często zadawane pytania** ](active-directory-aadconnect-pass-through-authentication-faq.md) — odpowiedzi na często zadawane pytania.
- [**Rozwiązywanie problemów z** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) — Dowiedz się, jak rozwiązać typowe problemy z funkcją.
- [**Security Deep Dive** ](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) — dodatkowe informacje szczegółowe techniczne na temat funkcji.
- [**Usługa Azure bezproblemowe logowanie Jednokrotne AD** ](active-directory-aadconnect-sso.md) — Dowiedz się więcej na temat tej dodatkowej funkcji.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) — w przypadku zgłaszania sugestie dotyczące nowych funkcji.
