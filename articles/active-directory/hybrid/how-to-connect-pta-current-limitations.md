---
title: 'Usługi Azure AD Connect: Uwierzytelnianie przekazywane - bieżące ograniczenia | Dokumentacja firmy Microsoft'
description: W tym artykule opisano bieżące ograniczenia uwierzytelniania przekazywanego usługi Azure Active Directory (Azure AD)
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
ms.date: 09/04/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: fabefcffdd7ee9a23d8989f897f30cbf027f42af
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426421"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory uwierzytelnianie przekazywane: Bieżące ograniczenia

>[!IMPORTANT]
>Uwierzytelnianie przekazywane usługi Azure Active Directory (Azure AD) jest bezpłatną funkcją, które nie potrzebują żadnych płatnej wersji usługi Azure AD z niego korzystać. Uwierzytelnianie przekazywane jest dostępne tylko w wystąpieniu na całym świecie usługi Azure AD, a nie na [chmury Microsoft Azure (Niemcy)](https://www.microsoft.de/cloud-deutschland) lub [chmury Microsoft Azure dla instytucji rządowych](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Obsługiwane są następujące scenariusze:

- Logowania użytkowników do aplikacji opartej na przeglądarce sieci web.
- Logowania użytkowników do klientów programu Outlook przy użyciu starszych protokołów, takich jak program Exchange ActiveSync, EAS, SMTP, POP i IMAP.
- Logowania użytkowników starsze aplikacje klienckie pakietu Office i aplikacji pakietu Office, które obsługują [nowoczesnego uwierzytelniania](https://aka.ms/modernauthga): wersje pakietu Office 2010, 2013 i 2016.
- Logowania użytkowników do starszej wersji protokołu aplikacji, takich jak program PowerShell w wersji 1.0 i innym osobom.
- Usługa Azure AD łączy dla urządzeń z systemem Windows 10.
- Hasła aplikacji dla usługi Multi-Factor Authentication.

## <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

Poniższe scenariusze są _nie_ obsługiwane:

- Wykrywanie użytkowników za pomocą [wyciek poświadczeń](../reports-monitoring/concept-risk-events.md#leaked-credentials).
- Usługi domenowe Azure AD musi być włączona w dzierżawie synchronizacji skrótów haseł. W związku z tym dzierżaw, które korzystają z uwierzytelniania przekazywanego _tylko_ nie działają w scenariuszach wymagających usług domenowych Azure AD.
- Uwierzytelnianie przekazywane nie jest zintegrowany z [programu Azure AD Connect Health](whatis-hybrid-identity-health.md).

>[!IMPORTANT]
>Jako obejście dla nieobsługiwanych scenariuszy _tylko_ (z wyjątkiem integracji usługi Azure AD Connect Health) Włączanie synchronizacji skrótów haseł na [funkcje opcjonalne](how-to-connect-install-custom.md#optional-features) strony kreatora Azure AD Connect.

>[!NOTE]
Włączanie synchronizacji skrótów haseł z opcją uwierzytelnianie trybu failover w przypadku infrastruktury lokalnej jest zakłócona. Tego rodzaju tryb failover z uwierzytelniania przekazywanego do synchronizacji skrótów haseł nie jest automatyczna. Musisz przełączyć metodę logowania ręcznie za pomocą usługi Azure AD Connect. Jeśli serwer z programem Azure AD Connect ulegnie awarii, potrzebujesz pomocy z Microsoft Support wyłączyć uwierzytelnianie przekazywane.

## <a name="next-steps"></a>Kolejne kroki
- [Szybki start](how-to-connect-pta-quick-start.md): Szybkie rozpoczynanie pracy przy użyciu uwierzytelniania przekazywanego usługi AD platformy Azure.
- [Migrowanie z usług AD FS do uwierzytelniania przekazywanego](https://aka.ms/ADFSTOPTADPDownload) — szczegółowy przewodnik dotyczący migracji z usług AD FS (lub inne technologie federacyjnych) do uwierzytelniania przekazywanego.
- [Blokada Smart](../authentication/howto-password-smart-lockout.md): informacje dotyczące konfigurowania funkcji inteligentnej blokady na swoją dzierżawę, aby chronić kont użytkowników.
- [Rozbudowana technicznie](how-to-connect-pta-how-it-works.md): zrozumienie sposobu działania funkcji uwierzytelniania przekazywanego.
- [Często zadawane pytania dotyczące](how-to-connect-pta-faq.md): odpowiedzi na często zadawane pytania dotyczące funkcji uwierzytelniania przekazywanego.
- [Rozwiązywanie problemów z](tshoot-connect-pass-through-authentication.md): Dowiedz się, jak rozwiązać typowe problemy przy użyciu funkcji uwierzytelniania przekazywanego.
- [Szczegółowe omówienie zabezpieczeń](how-to-connect-pta-security-deep-dive.md): Uzyskaj informacje szczegółowe techniczne na temat funkcji uwierzytelniania przekazywanego.
- [Usługa Azure bezproblemowe logowanie Jednokrotne AD](how-to-connect-sso.md): więcej informacji na temat tej dodatkowej funkcji.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Użyj Forum usługi Azure Active Directory do pliku sugestie dotyczące nowych funkcji.
