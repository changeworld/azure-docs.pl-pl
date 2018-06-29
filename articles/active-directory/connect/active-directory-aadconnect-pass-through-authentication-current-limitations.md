---
title: 'Azure AD Connect: Przekazywanego uwierzytelniania - bieżące ograniczenia | Dokumentacja firmy Microsoft'
description: W tym artykule opisano bieżące ograniczenia uwierzytelniania przekazywanego usługi Azure Active Directory (Azure AD)
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
ms.date: 03/22/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 8bdf1e3a46b6b03a7472bd2330b0a97429917122
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37028918"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Usługi Azure Active Directory przekazywanego uwierzytelniania: Bieżące ograniczenia

>[!IMPORTANT]
>Azure Active Directory (Azure AD) przekazywanego uwierzytelniania jest funkcją wolne, i nie wymagają żadnych wersji płatnej usługi Azure AD z niego korzystać. Przekazywanego uwierzytelniania jest dostępna w wystąpieniu na całym świecie usługi Azure AD, a nie na tylko [chmury Microsoft Azure Niemcy](http://www.microsoft.de/cloud-deutschland) lub [chmury Microsoft Azure dla instytucji rządowych](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Pełni są obsługiwane następujące scenariusze:

- Użytkownik logowania do wszystkich aplikacji opartej na przeglądarce sieci web.
- Logowania użytkownika do aplikacji pakietu Office, które obsługują [nowoczesnego uwierzytelniania](https://aka.ms/modernauthga): pakiet Office 2016 i Office 2013 _z_ nowoczesnego uwierzytelniania.
- Logowania użytkowników do klientów programu Outlook przy użyciu starszej wersji protokołów, takich jak program Exchange ActiveSync, SMTP, POP i IMAP.
- Logowania użytkownika do usługi Skype dla firm obsługujące nowoczesnego uwierzytelniania, w tym w trybie online i hybrydowych topologii. Dowiedz się więcej na temat obsługiwanych topologii [tutaj](https://technet.microsoft.com/library/mt803262.aspx).
- Łączy domenowych Azure AD dla urządzeń z systemem Windows 10.
- Hasła aplikacji w usłudze Multi-Factor Authentication.

## <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

Poniższe scenariusze są _nie_ obsługiwane:

- Logowania użytkownika do starszych aplikacjach klienckich pakietu Office, z wyjątkiem programu Outlook (zobacz **obsługiwane scenariusze** powyżej): pakiet Office 2010 i Office 2013 _bez_ nowoczesnego uwierzytelniania. Organizacje zaleca się przełączyć do nowoczesnego uwierzytelniania, jeśli to możliwe. Umożliwia obsługę uwierzytelniania przekazywanego nowoczesnego uwierzytelniania. Pomaga również Zabezpieczanie kont użytkowników przy użyciu [dostępu warunkowego](../active-directory-conditional-access-azure-portal.md) funkcje, takie jak uwierzytelnianie wieloskładnikowe Azure.
- Dostęp do kalendarza udostępniania i informacje wolny/zajęty w programie Exchange środowisk hybrydowych obejmujących na pakiet Office 2010 tylko.
- Użytkownik logowania do usługi Skype dla firm aplikacje klienckie _bez_ nowoczesnego uwierzytelniania.
- Użytkownik logowania do programu PowerShell w wersji 1.0. Firma Microsoft zaleca użycie programu PowerShell w wersji 2.0.
- Wykrywanie użytkownikom [ujawnione poświadczenia](../active-directory-reporting-risk-events.md#leaked-credentials).
- Usługi domenowe Azure AD wymaga synchronizacji skrótu hasła w dzierżawie. W związku z tym dzierżawcami, które korzystają z uwierzytelniania przekazywanego _tylko_ nie działa w przypadku scenariuszy, które wymagają usług domenowych Azure AD.
- Przekazywanego uwierzytelniania nie jest zintegrowany z [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md).
- Program rejestracji urządzeń firmy Apple (DEP firmy Apple) przy użyciu Asystenta ustawień systemu iOS nie obsługuje nowoczesnego uwierzytelniania. To nie będzie można zarejestrować urządzeń DEP firmy Apple do usługi Intune dla domen zarządzanych przy użyciu przekazywanego uwierzytelniania. Należy rozważyć użycie [aplikacji Portal firmy](https://blogs.technet.microsoft.com/intunesupport/2018/02/08/support-for-multi-token-dep-and-authentication-with-company-portal/) jako alternatywę.

>[!IMPORTANT]
>Jako obejścia nieobsługiwane scenariusze _tylko_, włączanie synchronizacji skrótów haseł na [funkcje opcjonalne](active-directory-aadconnect-get-started-custom.md#optional-features) strony kreatora programu Azure AD Connect. Podczas logowania użytkowników do aplikacji na liście w "nieobsługiwanych scenariuszy" sekcji, te określone żądania logowania są _nie_ obsługiwany przez agentów uwierzytelniania przekazywanego i dlatego nie będą rejestrowane w [ Rejestruje uwierzytelniania przekazywanego](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs).

>[!NOTE]
Włączanie synchronizacji skrótów haseł umożliwia uwierzytelnianie trybu failover, jeśli jest zakłócona infrastruktury lokalnej. Ten tryb failover z uwierzytelniania przekazywanego synchronizacji skrótu hasła usługi Active Directory nie jest automatyczna. Musisz przełączyć metoda logowania ręcznie za pomocą usługi Azure AD Connect. Jeśli serwer z systemem Azure AD Connect ulegnie awarii, potrzebna pomoc od firmy Microsoft Support wyłączyć uwierzytelniania przekazywanego.

## <a name="next-steps"></a>Kolejne kroki
- [Szybki start](active-directory-aadconnect-pass-through-authentication-quick-start.md): szybkim rozpoczynaniu pracy związanej z usługi Azure AD przekazywanego uwierzytelniania.
- [Inteligentne blokady](../authentication/howto-password-smart-lockout.md): informacje o sposobie konfigurowania funkcji blokady inteligentnej na swojej dzierżawy, aby chronić kont użytkowników.
- [Nowości techniczne](active-directory-aadconnect-pass-through-authentication-how-it-works.md): zrozumieć sposób działania funkcji uwierzytelniania przekazywanego.
- [Często zadawane pytania](active-directory-aadconnect-pass-through-authentication-faq.md): odpowiedzi na często zadawane pytania dotyczące funkcji uwierzytelniania przekazywanego.
- [Rozwiązywanie problemów z](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Dowiedz się, jak rozwiązać typowe problemy z funkcją uwierzytelniania przekazywanego.
- [Nowości zabezpieczeń](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): uzyskiwanie informacji głębokość technicznych dotyczących funkcji uwierzytelniania przekazywanego.
- [Azure AD SSO bezproblemowe](active-directory-aadconnect-sso.md): Dowiedz się więcej o tej funkcji uzupełniające.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): plik nowego żądania funkcji za pomocą na Forum usługi Azure Active Directory.
