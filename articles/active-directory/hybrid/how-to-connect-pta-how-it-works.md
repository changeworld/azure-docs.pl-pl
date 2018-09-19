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
ms.openlocfilehash: b8a758923f0e3e287da4a377d140f1f659cef46f
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46312697"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Azure Active Directory uwierzytelnianie przekazywane: Techniczne
W tym artykule przedstawiono omówienie, jak działa uwierzytelniania przekazywanego usługi Azure Active directory (Azure AD). Głębokie techniczne i informacje o zabezpieczeniach, zobacz [zagłębia zabezpieczeń](how-to-connect-pta-security-deep-dive.md) artykułu.

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Jak działa uwierzytelnianie przekazywane usługi Azure Active Directory?

>[!NOTE]
>Wymaganie wstępne uwierzytelnianie przekazywane do pracy użytkownicy muszą być obsługiwana w usłudze Azure AD z Active Directory w środowisku lokalnym za pomocą usługi Azure AD Connect. Uwierzytelnianie przekazywane nie ma zastosowania do użytkowników tylko w chmurze.

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

![Uwierzytelnianie przekazywane](./media/how-to-connect-pta-how-it-works/pta2.png)

## <a name="next-steps"></a>Kolejne kroki
- [Bieżące ograniczenia](how-to-connect-pta-current-limitations.md): Dowiedz się, jakie scenariusze są obsługiwane i te, które nie są.
- [Przewodnik Szybki Start](how-to-connect-pta-quick-start.md): Rozpocznij pracę na uwierzytelnianie przekazywane usługi AD platformy Azure.
- [Migrowanie z usług AD FS do uwierzytelniania przekazywanego](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx) — szczegółowy przewodnik dotyczący migracji z usług AD FS (lub inne technologie federacyjnych) do uwierzytelniania przekazywanego.
- [Blokada Smart](../authentication/howto-password-smart-lockout.md): Konfigurowanie funkcji inteligentnej blokady na swoją dzierżawę, aby chronić kont użytkowników.
- [Często zadawane pytania](how-to-connect-pta-faq.md): odpowiedzi na często zadawane pytania.
- [Rozwiązywanie problemów z](tshoot-connect-pass-through-authentication.md): Dowiedz się, jak rozwiązać typowe problemy przy użyciu funkcji uwierzytelniania przekazywanego.
- [Szczegółowe omówienie zabezpieczeń](how-to-connect-pta-security-deep-dive.md): Uzyskaj informacje szczegółowe techniczne na temat funkcji uwierzytelniania przekazywanego.
- [Usługa Azure bezproblemowe logowanie Jednokrotne AD](how-to-connect-sso.md): więcej informacji na temat tej dodatkowej funkcji.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Użyj Forum usługi Azure Active Directory do pliku sugestie dotyczące nowych funkcji.

