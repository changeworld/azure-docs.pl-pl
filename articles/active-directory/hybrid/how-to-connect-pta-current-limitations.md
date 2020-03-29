---
title: 'Usługa Azure AD Connect: uwierzytelnianie przekazywane — bieżące ograniczenia | Dokumenty firmy Microsoft'
description: W tym artykule opisano bieżące ograniczenia uwierzytelniania przekazywania usługi Azure Active Directory (Azure AD)
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
ms.date: 09/04/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97dc67d46b08bf5765c59806b45edd82f38720cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60347751"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Uwierzytelnianie przekazywane usługi Azure Active Directory: bieżące ograniczenia

>[!IMPORTANT]
>Uwierzytelnianie przekazywania usługi Azure Active Directory (Azure AD) jest bezpłatną funkcją i nie potrzebujesz żadnych płatnych wersji usługi Azure AD, aby z niej korzystać. Uwierzytelnianie przekazywane jest dostępne tylko w ogólnoświatowym wystąpieniu usługi Azure AD, a nie w [chmurze Microsoft Azure Germany](https://www.microsoft.de/cloud-deutschland) ani [w chmurze Microsoft Azure Dla Instytucji Rządowych.](https://azure.microsoft.com/features/gov/)

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Obsługiwane są następujące scenariusze:

- Logowania użytkowników do aplikacji opartych na przeglądarce internetowej.
- Logowania użytkowników do klientów programu Outlook przy użyciu starszych protokołów, takich jak Exchange ActiveSync, EAS, SMTP, POP i IMAP.
- Logowania użytkowników do starszych aplikacji klienckich pakietu Office i aplikacji pakietu Office [obsługujących nowoczesne uwierzytelnianie:](https://www.microsoft.com/en-us/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview)wersje pakietu Office 2013 i 2016.
- Logowania użytkowników do starszych aplikacji protokołu, takich jak PowerShell w wersji 1.0 i innych.
- Dołączanie usługi Azure AD dla urządzeń z systemem Windows 10.
- Hasła aplikacji do uwierzytelniania wieloskładnikowego.

## <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

Następujące scenariusze _nie_ są obsługiwane:

- Wykrywanie użytkowników z [wyciekiem poświadczeń](../reports-monitoring/concept-risk-events.md#leaked-credentials).
- Usługi domenowe usługi Azure AD muszą mieć włączoną synchronizację skrótów haseł w dzierżawie. W związku z tym dzierżawców, które używają uwierzytelniania przekazywania _tylko_ nie działają w scenariuszach, które wymagają usług domenowych usługi Azure AD.
- Uwierzytelnianie przekazywane nie jest zintegrowane z [usługą Azure AD Connect Health.](whatis-hybrid-identity-health.md)

> [!IMPORTANT]
> Aby obejść _tylko_ nieobsługiwały scenariusze (z wyjątkiem integracji usługi Azure AD Connect Health), włącz synchronizację skrótów haseł na stronie [Funkcje opcjonalne](how-to-connect-install-custom.md#optional-features) w kreatorze usługi Azure AD Connect.
> 
> [!NOTE]
> Włączenie synchronizacji skrótów haseł umożliwia uwierzytelnianie trybu failover w przypadku zakłócenia infrastruktury lokalnej. Ta funkcja failover z uwierzytelniania przekazywania do synchronizacji skrótów haseł nie jest automatyczna. Musisz ręcznie przełączyć metodę logowania przy użyciu usługi Azure AD Connect. Jeśli serwer z uruchomiona usługą Azure AD Connect ustępuje, aby wyłączyć uwierzytelnianie przekazywane, wymagana jest pomoc pomocy technicznej firmy Microsoft.

## <a name="next-steps"></a>Następne kroki
- [Szybki start:](how-to-connect-pta-quick-start.md)Wystartuj i uruchom za pomocą uwierzytelniania przekazywania usługi Azure AD.
- [Migracja z usług AD FS do uwierzytelniania przekazywanego](https://aka.ms/ADFSTOPTADPDownload) — szczegółowy przewodnik dotyczący migracji z usług AD FS (lub innych technologii federacyjnej) do uwierzytelniania przekazywanego.
- [Inteligentna blokada:](../authentication/howto-password-smart-lockout.md)Dowiedz się, jak skonfigurować funkcję smart lockout w dzierżawie w celu ochrony kont użytkowników.
- [Głębokie głębokie nurkowanie techniczne:](how-to-connect-pta-how-it-works.md)Dowiedz się, jak działa funkcja uwierzytelniania przekazywanego.
- [Często zadawane pytania:](how-to-connect-pta-faq.md)znajdź odpowiedzi na często zadawane pytania dotyczące funkcji uwierzytelniania przekazywanego.
- [Rozwiązywanie problemów](tshoot-connect-pass-through-authentication.md): Dowiedz się, jak rozwiązać typowe problemy z funkcją uwierzytelniania przekazywanego.
- [Głębokie skanowanie zabezpieczeń:](how-to-connect-pta-security-deep-dive.md)uzyskaj głębokie informacje techniczne na temat funkcji uwierzytelniania przekazywanego.
- [Bezproblemowe logowanie jednokrotne usługi Azure AD:](how-to-connect-sso.md)Dowiedz się więcej o tej uzupełniającej funkcji.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Użyj forum usługi Azure Active Directory, aby złożyć nowe żądania funkcji.
