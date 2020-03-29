---
title: 'Usługa Azure AD Connect: uwierzytelnianie przekazywane — jak to działa | Dokumenty firmy Microsoft'
description: W tym artykule opisano, jak działa uwierzytelnianie przekazywane usługi Azure Active Directory
services: active-directory
keywords: Uwierzytelnianie przekazywanie usługi Azure AD Connect, instalowanie usługi Active Directory, wymagane składniki dla usługi Azure AD, logowania jednokrotnego, logowania jednokrotnego
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59cd52dbdf6c13900cde592aeb52d8bf9abf850f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60347796"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Uwierzytelnianie przekazywania usługi Azure Active Directory: głębokie nurkowanie techniczne
Ten artykuł zawiera omówienie działania uwierzytelniania przekazywanego usługi Azure Active Directory (Azure AD). Aby uzyskać szczegółowe informacje techniczne i dotyczące zabezpieczeń, zobacz artykuł [głębokie nurkowanie zabezpieczeń.](how-to-connect-pta-security-deep-dive.md)

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Jak działa uwierzytelnianie przekazywane usługi Azure Active Directory?

>[!NOTE]
>Jako warunek wstępny uwierzytelniania przekazywania do pracy, użytkownicy muszą być aprowizacji do usługi Azure AD z lokalnej usługi Active Directory przy użyciu usługi Azure AD Connect. Uwierzytelnianie przekazywane nie ma zastosowania do użytkowników tylko w chmurze.

Gdy użytkownik próbuje zalogować się do aplikacji zabezpieczonej przez usługę Azure AD, a jeśli uwierzytelnianie przekazywania jest włączone w dzierżawie, występują następujące kroki:

1. Użytkownik próbuje uzyskać dostęp do aplikacji, na przykład [aplikacji Outlook Web App](https://outlook.office365.com/owa/).
2. Jeśli użytkownik nie jest jeszcze zalogowany, użytkownik jest przekierowywane do strony **logowania użytkownika** usługi Azure AD.
3. Użytkownik wprowadza swoją nazwę użytkownika na stronie logowania usługi Azure AD, a następnie wybiera przycisk **Dalej.**
4. Użytkownik wprowadza swoje hasło do strony logowania usługi Azure AD, a następnie wybiera przycisk **Zaloguj.**
5. Usługa Azure AD, po otrzymaniu żądania logowania, umieszcza nazwę użytkownika i hasło (szyfrowane przy użyciu klucza publicznego agentów uwierzytelniania) w kolejce.
6. Lokalny agent uwierzytelniania pobiera nazwę użytkownika i zaszyfrowane hasło z kolejki. Należy zauważyć, że agent nie często sondowania żądań z kolejki, ale pobiera żądania za sprawą wstępnie ustanowionego połączenia trwałego.
7. Agent odszyfrowuje hasło przy użyciu klucza prywatnego.
8. Agent sprawdza poprawność nazwy użytkownika i hasła w usłudze Active Directory przy użyciu standardowych interfejsów API systemu Windows, który jest mechanizmem podobnym do używanego przez usługi federacyjne Active Directory (AD FS). Nazwa użytkownika może być domyślną nazwą użytkownika `userPrincipalName`lokalnego, zwykle lub innym atrybutem skonfigurowanym w usłudze Azure AD Connect (znanym jako `Alternate ID`).
9. Lokalny kontroler domeny usługi Active Directory (DC) ocenia żądanie i zwraca agentowi odpowiednią odpowiedź (powodzenie, niepowodzenie, wygaśnięcie hasła lub zablokowanie użytkownika).
10. Agent uwierzytelniania z kolei zwraca tę odpowiedź z powrotem do usługi Azure AD.
11. Usługa Azure AD ocenia odpowiedź i odpowiada użytkownikowi odpowiednio. Na przykład usługa Azure AD podpisuje użytkownika natychmiast lub żąda uwierzytelniania wieloskładnikowego platformy Azure.
12. Jeśli logowanie użytkownika zakończy się pomyślnie, użytkownik może uzyskać dostęp do aplikacji.

Na poniższym diagramie przedstawiono wszystkie składniki i kroki:

![Uwierzytelnianie przekazywane](./media/how-to-connect-pta-how-it-works/pta2.png)

## <a name="next-steps"></a>Następne kroki
- [Bieżące ograniczenia](how-to-connect-pta-current-limitations.md): Dowiedz się, które scenariusze są obsługiwane, a które nie.
- [Szybki start:](how-to-connect-pta-quick-start.md)Wystartuj i uruchom uwierzytelnianie przekazywane usługi Azure AD.
- [Migracja z usług AD FS do uwierzytelniania przekazywanego](https://aka.ms/adfstoPTADP) — szczegółowy przewodnik dotyczący migracji z usług AD FS (lub innych technologii federacyjnej) do uwierzytelniania przekazywanego.
- [Inteligentna blokada:](../authentication/howto-password-smart-lockout.md)Skonfiguruj funkcję smart lockout w dzierżawie, aby chronić konta użytkowników.
- [Często zadawane pytania:](how-to-connect-pta-faq.md)znajdź odpowiedzi na często zadawane pytania.
- [Rozwiązywanie problemów](tshoot-connect-pass-through-authentication.md): Dowiedz się, jak rozwiązać typowe problemy z funkcją uwierzytelniania przekazywanego.
- [Głębokie nurkowanie](how-to-connect-pta-security-deep-dive.md)zabezpieczeń: uzyskaj głębokie informacje techniczne na temat funkcji uwierzytelniania przekazywanego.
- [Bezproblemowe logowanie jednokrotne usługi Azure AD:](how-to-connect-sso.md)Dowiedz się więcej o tej uzupełniającej funkcji.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Użyj forum usługi Azure Active Directory, aby złożyć nowe żądania funkcji.

