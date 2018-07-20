---
title: 'Usługi Azure AD Connect: Uwierzytelnianie przekazywane — jak działa | Dokumentacja firmy Microsoft'
description: W tym artykule opisano, jak działa uwierzytelnianie przekazywane usługi Azure Active Directory
services: active-directory
keywords: Azure AD Connect uwierzytelniania przekazywanego, instalacji usługi Active Directory, wymaganych składników dla usługi Azure AD, logowania jednokrotnego, logowanie jednokrotne
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 371f3ea3b764eecbb621fc06ec483de4778e7db2
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159495"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Azure Active Directory uwierzytelnianie przekazywane: Techniczne
W tym artykule przedstawiono omówienie, jak działa uwierzytelniania przekazywanego usługi Azure Active directory (Azure AD). Głębokie techniczne i informacje o zabezpieczeniach, zobacz [zagłębia zabezpieczeń](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) artykułu.

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Jak działa uwierzytelnianie przekazywane usługi Azure Active Directory?

[!NOTE]
Wymaganie wstępne uwierzytelnianie przekazywane do pracy użytkownicy muszą być obsługiwana w usłudze Azure AD z Active Directory w środowisku lokalnym za pomocą usługi Azure AD Connect. Uwierzytelnianie przekazywane nie ma zastosowania do użytkowników tylko w chmurze.

Gdy użytkownik próbuje zalogować się do aplikacji zabezpieczonej przez usługi Azure AD, a uwierzytelnianie przekazywane jest włączona w dzierżawie, wykonywane są następujące kroki:

1. Użytkownik próbuje uzyskać dostęp do aplikacji, na przykład [aplikacji Outlook Web App](https://outlook.office365.com/owa/).
2. Jeśli użytkownik nie jest już zalogowany, użytkownik jest przekierowywany do usługi Azure AD **logowania użytkownika** strony.
3. Użytkownik wprowadza swoją nazwę użytkownika do strony logowania usługi Azure AD, a następnie wybiera **dalej** przycisku.
4. Użytkownik wprowadza swoje hasło na stronie logowania usługi Azure AD, a następnie wybiera **Zaloguj** przycisku.
5. Usługi Azure AD, po odebraniu żądania do logowania, umieszcza nazwy użytkownika i hasła (zaszyfrowane przy użyciu klucza publicznego agentów uwierzytelniania) w kolejce.
6. Agent uwierzytelniania lokalnego pobiera nazwę użytkownika i zaszyfrowane hasło z kolejki. Należy zauważyć, że Agent często nie sondowania żądań z kolejki, ale pobiera żądania za pośrednictwem wstępnie ustanowionego połączenia trwałego.
7. Agent odszyfrowuje hasło za pomocą jego klucza prywatnego.
8. Agent sprawdza poprawność nazwy użytkownika i hasła w usłudze Active Directory przy użyciu standardowych interfejsów API Windows, który jest podobny mechanizm do jakich usług federacyjnych Active Directory (AD FS) używa. Nazwa użytkownika może być albo w środowisku lokalnym domyślna nazwa użytkownika, zazwyczaj `userPrincipalName`, lub inny atrybut skonfigurowanego w Azure AD Connect (nazywane `Alternate ID`).
9. Lokalny kontroler domeny usługi Active Directory (DC) ocenia żądanie i zwraca odpowiednią odpowiedź (Powodzenie, Niepowodzenie, hasło wygasło lub użytkownika zablokowane) do agenta.
10. Agent uwierzytelniania zwraca z kolei ta odpowiedź z powrotem do usługi Azure AD.
11. Usługa Azure AD ocenia odpowiedzi i reaguje na użytkownika, zgodnie z potrzebami. Na przykład usługi Azure AD loguje się użytkownik natychmiast albo żądań dla usługi Azure Multi-Factor Authentication.
12. Jeśli logowanie użytkownika zakończy się pomyślnie, użytkownikowi dostęp do aplikacji.

Na poniższym diagramie przedstawiono wszystkie składniki i kroki do wykonania:

![Uwierzytelnianie przekazywane](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>Kolejne kroki
- [Bieżące ograniczenia](active-directory-aadconnect-pass-through-authentication-current-limitations.md): Dowiedz się, jakie scenariusze są obsługiwane i te, które nie są.
- [Przewodnik Szybki Start](active-directory-aadconnect-pass-through-authentication-quick-start.md): Rozpocznij pracę na uwierzytelnianie przekazywane usługi AD platformy Azure.
- [Blokada Smart](../authentication/howto-password-smart-lockout.md): Konfigurowanie funkcji inteligentnej blokady na swoją dzierżawę, aby chronić kont użytkowników.
- [Często zadawane pytania](active-directory-aadconnect-pass-through-authentication-faq.md): odpowiedzi na często zadawane pytania.
- [Rozwiązywanie problemów z](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Dowiedz się, jak rozwiązać typowe problemy przy użyciu funkcji uwierzytelniania przekazywanego.
- [Szczegółowe omówienie zabezpieczeń](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): Uzyskaj informacje szczegółowe techniczne na temat funkcji uwierzytelniania przekazywanego.
- [Usługa Azure bezproblemowe logowanie Jednokrotne AD](active-directory-aadconnect-sso.md): więcej informacji na temat tej dodatkowej funkcji.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Użyj Forum usługi Azure Active Directory do pliku sugestie dotyczące nowych funkcji.

