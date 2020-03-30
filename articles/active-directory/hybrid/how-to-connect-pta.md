---
title: 'Usługa Azure AD Connect: uwierzytelnianie przekazywane | Dokumenty firmy Microsoft'
description: W tym artykule opisano uwierzytelnianie przekazywane usługi Azure Active Directory (Azure AD) i sposób, w jaki zezwala na logowanie usługi Azure AD, sprawdzając hasła użytkowników względem lokalnej usługi Active Directory.
services: active-directory
keywords: co to jest uwierzytelnianie przekazywanie usługi Azure AD Connect, instalowanie usługi Active Directory, wymagane składniki dla usługi Azure AD, logowania jednokrotnego, logowania jednokrotnego
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4b52a3025bfb15e2679709353cebf28254a75c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185499"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Logowanie użytkownika przy użyciu uwierzytelniania przekazywanego usługi Azure Active Directory

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Co to jest uwierzytelnianie przekazywania usługi Azure Active Directory?

Uwierzytelnianie przekazywane usługi Azure Active Directory (Azure AD) umożliwia użytkownikom logowanie się do aplikacji lokalnych i opartych na chmurze przy użyciu tych samych haseł. Ta funkcja zapewnia lepszą obsługę użytkowników (jedno hasło mniej do zapamiętania) i obniża koszty pomocy technicznej IT, zmniejszając prawdopodobieństwo tego, że użytkownik zapomni sposób logowania się. Gdy użytkownicy logują się przy użyciu usługi Azure AD, ta funkcja sprawdza poprawność haseł użytkowników bezpośrednio w lokalnej usłudze Active Directory.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Ta funkcja jest alternatywą dla [synchronizacji skrótów haseł usługi Azure AD,](how-to-connect-password-hash-synchronization.md)która zapewnia taką samą korzyść uwierzytelniania w chmurze dla organizacji. Jednak niektóre organizacje, które chcą wymusić lokalne zasady zabezpieczeń i haseł usługi Active Directory, mogą zamiast tego użyć uwierzytelniania przekazywanego. Zapoznaj się z [tym przewodnikiem,](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) aby porównać różne metody logowania usługi Azure AD i jak wybrać odpowiednią metodę logowania dla twojej organizacji.

![Uwierzytelnianie przekazywane usługi Azure AD](./media/how-to-connect-pta/pta1.png)

Uwierzytelnianie przekazywane można łączyć z funkcją [bezproblemowego logowania jednokrotnego.](how-to-connect-sso.md) W ten sposób, gdy użytkownicy uzyskują dostęp do aplikacji na swoich komputerach firmowych w sieci firmowej, nie muszą wpisywać swoich haseł, aby się zalogować.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Najważniejsze korzyści wynikające z korzystania z uwierzytelniania przekazywania usługi Azure AD

- *Fantastyczne środowisko pracy użytkownika*
  - Użytkownicy używają tych samych haseł do logowania się do aplikacji lokalnych i opartych na chmurze.
  - Użytkownicy spędzają mniej czasu na rozmowach z działem pomocy technicznej IT w rozwiązywaniu problemów związanych z hasłami.
  - Użytkownicy mogą wykonywać [samoobsługowe zadania zarządzania hasłami](../authentication/active-directory-passwords-overview.md) w chmurze.
- *Łatwe wdrażanie & administrowanie*
  - Nie ma potrzeby stosowania złożonych wdrożeń lokalnych ani konfiguracji sieci.
  - Potrzebuje tylko lekkiego agenta do zainstalowania lokalnie.
  - Brak kosztów zarządzania. Agent automatycznie otrzymuje ulepszenia i poprawki błędów.
- *Bezpieczeństwo*
  - Hasła lokalne nigdy nie są przechowywane w chmurze w żadnej formie.
  - Chroni konta użytkowników, bezproblemowo współpracując z [zasadami dostępu warunkowego usługi Azure AD,](../active-directory-conditional-access-azure-portal.md)w tym uwierzytelnianiem wieloskładnikowym (MFA), [blokowaniem starszego uwierzytelniania](../conditional-access/concept-conditional-access-conditions.md) i [filtrowaniem ataków na hasła.](../authentication/howto-password-smart-lockout.md)
  - Agent wykonuje tylko połączenia wychodzące z sieci. W związku z tym nie ma wymogu, aby zainstalować agenta w sieci obwodowej, znany również jako DMZ.
  - Komunikacja między agentem a usługą Azure AD jest zabezpieczona przy użyciu uwierzytelniania opartego na certyfikatach. Certyfikaty te są automatycznie odnawiane co kilka miesięcy przez usługę Azure AD.
- *Wysoka dostępność*
  - Dodatkowe agentów można zainstalować na wielu serwerach lokalnych, aby zapewnić wysoką dostępność żądań logowania.

## <a name="feature-highlights"></a>Najważniejsze informacje o funkcjach

- Obsługuje logowanie użytkowników do wszystkich aplikacji opartych na przeglądarce internetowej oraz do aplikacji klienckich pakietu Microsoft Office, które używają [nowoczesnego uwierzytelniania](https://aka.ms/modernauthga).
- Nazwy użytkowników logowania mogą być domyślną nazwą użytkownika`userPrincipalName`lokalnego ( ) lub innym atrybutem `Alternate ID`skonfigurowanym w usłudze Azure AD Connect (znanym jako ).
- Ta funkcja bezproblemowo współpracuje z funkcjami [dostępu warunkowego,](../active-directory-conditional-access-azure-portal.md) takimi jak uwierzytelnianie wieloskładnikowe (MFA), aby ułatwić bezpieczeństwo użytkowników.
- Zintegrowane z [samoobsługowym zarządzaniem hasłami](../authentication/active-directory-passwords-overview.md)w chmurze, w tym zapisywaniem haseł w lokalnej usłudze Active Directory i ochroną hasłem, poprzez zablokowanie powszechnie używanych haseł.
- Środowiska wielu lasów są obsługiwane, jeśli między lasami usługi AD istnieją relacje zaufania lasu i jeśli routing sufiksu nazw jest poprawnie skonfigurowany.
- Jest to bezpłatna funkcja i nie potrzebujesz żadnych płatnych wersji usługi Azure AD, aby z niej korzystać.
- Można go włączyć za pośrednictwem [usługi Azure AD Connect](whatis-hybrid-identity.md).
- Używa lekkiego agenta lokalnego, który nasłuchuje żądań sprawdzania poprawności hasła i odpowiada na nie.
- Instalowanie wielu agentów zapewnia wysoką dostępność żądań logowania.
- [Chroni](../authentication/howto-password-smart-lockout.md) konta lokalne przed atakami z użyciem haseł w chmurze.

## <a name="next-steps"></a>Następne kroki

- [Szybki start](how-to-connect-pta-quick-start.md) — rozpoczynanie pracy z uwierzytelnianiem przekazu usługi Azure AD.
- [Migracja z usług AD FS do uwierzytelniania przekazywanego](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) — szczegółowy przewodnik dotyczący migracji z usług AD FS (lub innych technologii federacyjnej) do uwierzytelniania przekazywanego.
- [Inteligentna blokada](../authentication/howto-password-smart-lockout.md) — konfigurowanie funkcji inteligentnej blokady w dzierżawie w celu ochrony kont użytkowników.
- [Bieżące ograniczenia](how-to-connect-pta-current-limitations.md) — dowiedz się, które scenariusze są obsługiwane, a które nie.
- [Głębokie nurkowanie techniczne](how-to-connect-pta-how-it-works.md) — dowiedz się, jak działa ta funkcja.
- [Najczęściej zadawane pytania](how-to-connect-pta-faq.md) — odpowiedzi na często zadawane pytania.
- [Rozwiązywanie problemów](tshoot-connect-pass-through-authentication.md) — dowiedz się, jak rozwiązać typowe problemy z tą funkcją.
- [Security Deep Dive](how-to-connect-pta-security-deep-dive.md) - Dodatkowe głębokie informacje techniczne na temat tej funkcji.
- [Bezproblemowe logowanie jednokrotne usługi Azure AD](how-to-connect-sso.md) — dowiedz się więcej o tej uzupełniającej funkcji.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) — do składania nowych żądań funkcji.
