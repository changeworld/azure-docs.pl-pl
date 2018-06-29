---
title: 'Azure AD Connect: Uwierzytelniania przekazywanego — jak działa | Dokumentacja firmy Microsoft'
description: W tym artykule opisano, jak działa uwierzytelniania przekazywanego Azure Active Directory
services: active-directory
keywords: Azure AD Connect przekazywanego uwierzytelniania, instalacji usługi Active Directory, wymaganych składników dla usługi Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 5fde0ea00aacbb791836fc1076b88dafd3728454
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063480"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Azure przekazywanego uwierzytelnianie usługi Active Directory: Techniczne nowości
W tym artykule przedstawiono sposób działania przekazywanego uwierzytelniania w usłudze Azure Active directory (Azure AD). Bezpośrednie technicznych i informacji o zabezpieczeniach, zobacz [nowości zabezpieczeń](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) artykułu.

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Jak działa uwierzytelniania przekazywanego Azure Active Directory?

Gdy użytkownik próbuje zalogować się do aplikacji zabezpieczonej przez usługi Azure AD i przekazywanego uwierzytelniania jest włączona w ramach dzierżawy, zostaną wykonane następujące kroki:

1. Użytkownik próbuje uzyskać dostęp do aplikacji, na przykład [aplikacji Outlook Web App](https://outlook.office365.com/owa/).
2. Jeśli użytkownik nie jest już zalogowany, zostanie przekierowany użytkownik w usłudze Azure AD **logowania użytkownika** strony.
3. Użytkownik wprowadza swoją nazwę użytkownika do strony logowania usługi Azure AD, a następnie wybiera **dalej** przycisku.
4. Użytkownik wprowadza swoje hasło do strona logowania usługi Azure AD, a następnie wybiera **Zaloguj** przycisku.
5. Azure AD, po otrzymaniu żądania do logowania, umieszcza nazwy użytkownika i hasła (zaszyfrowane przy użyciu klucza publicznego agentów uwierzytelniania) w kolejce.
6. Agent uwierzytelniania lokalnego pobiera nazwę użytkownika i hasło zaszyfrowanych z kolejki. Należy pamiętać, że Agent nie często sondowania żądań z kolejki, ale pobiera żądania wstępnie ustanowić trwałe połączenie.
7. Agent odszyfrowuje hasło przy użyciu jego klucz prywatny.
8. Agent weryfikuje nazwy użytkownika i hasła w usłudze Active Directory przy użyciu standardowych interfejsów API systemu Windows, który jest podobny mechanizm jakie Active Directory Federation Services (AD FS) używa. Nazwa użytkownika może być albo lokalnymi domyślna nazwa użytkownika, zazwyczaj `userPrincipalName`, lub inny atrybut skonfigurowane w programie Azure AD Connect (nazywane `Alternate ID`).
9. Kontroler domeny usługi Active Directory lokalnymi (DC) ocenia żądanie i zwraca właściwą odpowiedź (Powodzenie, Niepowodzenie, hasło wygasło lub użytkownika zablokowane) do agenta.
10. Agent uwierzytelniania z kolei, zwraca ten odpowiedź z powrotem do usługi Azure AD.
11. Usługi Azure AD ocenia odpowiedzi i odpowiada użytkowników zależnie od potrzeb. Na przykład usługi Azure AD loguje się użytkownik natychmiast lub żądań do usługi Azure Multi-Factor Authentication.
12. Jeśli logowanie użytkownika zakończy się pomyślnie, można uzyskać dostępu do aplikacji.

Na poniższym diagramie przedstawiono wszystkie składniki i kroki do wykonania:

![Uwierzytelnianie przekazywane](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>Kolejne kroki
- [Bieżące ograniczenia](active-directory-aadconnect-pass-through-authentication-current-limitations.md): Dowiedz się, jakie scenariusze są obsługiwane i zostały.
- [Szybki Start](active-directory-aadconnect-pass-through-authentication-quick-start.md): rozpocząć pracę na Azure AD przekazywanego uwierzytelniania.
- [Inteligentne blokady](../authentication/howto-password-smart-lockout.md): Konfigurowanie funkcji blokady inteligentnej na swojej dzierżawy, aby chronić kont użytkowników.
- [Często zadawane pytania](active-directory-aadconnect-pass-through-authentication-faq.md): odpowiedzi na często zadawane pytania.
- [Rozwiązywanie problemów z](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Dowiedz się, jak rozwiązać typowe problemy z funkcją uwierzytelniania przekazywanego.
- [Nowości zabezpieczeń](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): uzyskiwanie informacji głębokość technicznych dotyczących funkcji uwierzytelniania przekazywanego.
- [Azure AD SSO bezproblemowe](active-directory-aadconnect-sso.md): Dowiedz się więcej o tej funkcji uzupełniające.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): plik nowego żądania funkcji za pomocą na Forum usługi Azure Active Directory.

