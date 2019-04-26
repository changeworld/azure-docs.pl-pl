---
title: 'Program Azure AD Connect: Uwierzytelnianie przekazywane — bieżące ograniczenia | Dokumentacja firmy Microsoft'
description: W tym artykule opisano bieżące ograniczenia uwierzytelniania przekazywanego usługi Azure Active Directory (Azure AD)
services: active-directory
keywords: Azure AD Connect uwierzytelniania przekazywanego, instalacji usługi Active Directory, wymaganych składników dla usługi Azure AD, logowania jednokrotnego, logowanie jednokrotne
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/04/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97dc67d46b08bf5765c59806b45edd82f38720cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60347751"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Uwierzytelnianie przekazywane usługi Azure Active Directory: Bieżące ograniczenia

>[!IMPORTANT]
>Uwierzytelnianie przekazywane usługi Azure Active Directory (Azure AD) jest bezpłatną funkcją, które nie potrzebują żadnych płatnej wersji usługi Azure AD z niego korzystać. Uwierzytelnianie przekazywane jest dostępne tylko w wystąpieniu na całym świecie usługi Azure AD, a nie na [chmury Microsoft Azure (Niemcy)](https://www.microsoft.de/cloud-deutschland) lub [chmury Microsoft Azure dla instytucji rządowych](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Obsługiwane są następujące scenariusze:

- Logowania użytkowników do aplikacji opartej na przeglądarce sieci web.
- Logowania użytkowników do klientów programu Outlook przy użyciu starszych protokołów, takich jak program Exchange ActiveSync, EAS, SMTP, POP i IMAP.
- Logowania użytkowników starsze aplikacje klienckie pakietu Office i aplikacji pakietu Office, które obsługują [nowoczesnego uwierzytelniania](https://www.microsoft.com/en-us/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview): Pakiet Office 2013 i 2016 wersji.
- Logowania użytkowników do starszej wersji protokołu aplikacji, takich jak program PowerShell w wersji 1.0 i innym osobom.
- Usługa Azure AD łączy dla urządzeń z systemem Windows 10.
- Hasła aplikacji dla usługi Multi-Factor Authentication.

## <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

Poniższe scenariusze są _nie_ obsługiwane:

- Wykrywanie użytkowników za pomocą [wyciek poświadczeń](../reports-monitoring/concept-risk-events.md#leaked-credentials).
- Usługi domenowe Azure AD musi być włączona w dzierżawie synchronizacji skrótów haseł. W związku z tym dzierżaw, które korzystają z uwierzytelniania przekazywanego _tylko_ nie działają w scenariuszach wymagających usług domenowych Azure AD.
- Uwierzytelnianie przekazywane nie jest zintegrowany z [programu Azure AD Connect Health](whatis-hybrid-identity-health.md).

> [!IMPORTANT]
> Jako obejście dla nieobsługiwanych scenariuszy _tylko_ (z wyjątkiem integracji usługi Azure AD Connect Health) Włączanie synchronizacji skrótów haseł na [funkcje opcjonalne](how-to-connect-install-custom.md#optional-features) strony kreatora Azure AD Connect.
> 
> [!NOTE]
> Włączanie synchronizacji skrótów haseł z opcją uwierzytelnianie trybu failover w przypadku infrastruktury lokalnej jest zakłócona. Tego rodzaju tryb failover z uwierzytelniania przekazywanego do synchronizacji skrótów haseł nie jest automatyczna. Musisz przełączyć metodę logowania ręcznie za pomocą usługi Azure AD Connect. Jeśli serwer z programem Azure AD Connect ulegnie awarii, potrzebujesz pomocy z Microsoft Support wyłączyć uwierzytelnianie przekazywane.

## <a name="next-steps"></a>Kolejne kroki
- [Szybki start](how-to-connect-pta-quick-start.md): Rozpocznij pracę przy użyciu uwierzytelniania przekazywanego usługi AD platformy Azure.
- [Migrowanie z usług AD FS do uwierzytelniania przekazywanego](https://aka.ms/ADFSTOPTADPDownload) — szczegółowy przewodnik dotyczący migracji z usług AD FS (lub inne technologie federacyjnych) do uwierzytelniania przekazywanego.
- [Blokada Smart](../authentication/howto-password-smart-lockout.md): Dowiedz się, jak skonfigurować funkcji inteligentnej blokady na swoją dzierżawę, aby chronić kont użytkowników.
- [Rozbudowana technicznie](how-to-connect-pta-how-it-works.md): Dowiedz się, jak działa funkcja uwierzytelniania przekazywanego.
- [Często zadawane pytania dotyczące](how-to-connect-pta-faq.md): Znajdź odpowiedzi na często zadawane pytania dotyczące funkcji uwierzytelniania przekazywanego.
- [Rozwiązywanie problemów z](tshoot-connect-pass-through-authentication.md): Dowiedz się, jak rozwiązać typowe problemy przy użyciu funkcji uwierzytelniania przekazywanego.
- [Szczegółowe omówienie zabezpieczeń](how-to-connect-pta-security-deep-dive.md): Uzyskaj informacje szczegółowe techniczne na temat funkcji uwierzytelniania przekazywanego.
- [Bezproblemowe logowanie Jednokrotne usługi Azure AD](how-to-connect-sso.md): Dowiedz się więcej na temat tej dodatkowej funkcji.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Forum usługi Azure Active Directory umożliwia pliku sugestie dotyczące nowych funkcji.
