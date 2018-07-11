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
ms.date: 03/22/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 34b83c54e31ed73af3f776a6add8f218dda35cf7
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2018
ms.locfileid: "37918924"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory uwierzytelnianie przekazywane: Bieżące ograniczenia

>[!IMPORTANT]
>Uwierzytelnianie przekazywane usługi Azure Active Directory (Azure AD) jest bezpłatną funkcją, które nie potrzebują żadnych płatnej wersji usługi Azure AD z niego korzystać. Uwierzytelnianie przekazywane jest dostępne tylko w wystąpieniu na całym świecie usługi Azure AD, a nie na [chmury Microsoft Azure (Niemcy)](http://www.microsoft.de/cloud-deutschland) lub [chmury Microsoft Azure dla instytucji rządowych](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

W pełni obsługiwane są następujące scenariusze:

- Logowania użytkowników do wszystkich aplikacji opartych na przeglądarce sieci web.
- Logowania użytkowników do aplikacji pakietu Office, które obsługują [nowoczesnego uwierzytelniania](https://aka.ms/modernauthga): pakiety Office 2016 lub Office 2013 _z_ nowoczesnego uwierzytelniania.
- Logowania użytkowników do klientów programu Outlook przy użyciu starszych protokołów, takich jak program Exchange ActiveSync, SMTP, POP i IMAP.
- Logowania użytkowników do usługi Skype dla firm obsługujących nowoczesne uwierzytelnianie, w tym w trybie online i hybrydowych topologii. Dowiedz się więcej na temat obsługiwanych topologii [tutaj](https://technet.microsoft.com/library/mt803262.aspx).
- Domena usługi Azure AD łączy dla urządzeń z systemem Windows 10.
- Hasła aplikacji dla usługi Multi-Factor Authentication.

## <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

Poniższe scenariusze są _nie_ obsługiwane:

- Logowania użytkowników do starszych aplikacji klienckich pakietu Office, z wyjątkiem programu Outlook (zobacz **obsługiwane scenariusze** powyżej): pakiet Office 2010 i Office 2013 _bez_ nowoczesnego uwierzytelniania. Aby przełączyć się do nowoczesnego uwierzytelniania, jeśli jest to możliwe, zaleca się organizacji. Nowoczesne uwierzytelnianie umożliwia obsługę uwierzytelniania przekazywanego. Pomaga również Zabezpieczanie kont użytkowników przy użyciu [dostępu warunkowego](../active-directory-conditional-access-azure-portal.md) funkcje, takie jak uwierzytelnianie wieloskładnikowe systemu Azure.
- Dostęp do udostępnianie kalendarza i bezpłatne zajęty informacji w programie Exchange środowisk o hybrydowych w pakiecie Office 2010 tylko.
- Logowania użytkowników do usługi Skype dla firm, aplikacje klienckie _bez_ nowoczesnego uwierzytelniania.
- Użytkownik logowania do programu PowerShell w wersji 1.0. Firma Microsoft zaleca się używanie programu PowerShell w wersji 2.0.
- Wykrywanie użytkowników za pomocą [wyciek poświadczeń](../active-directory-reporting-risk-events.md#leaked-credentials).
- Usługi domenowe Azure AD musi być włączona w dzierżawie synchronizacji skrótów haseł. W związku z tym dzierżaw, które korzystają z uwierzytelniania przekazywanego _tylko_ nie działają w scenariuszach wymagających usług domenowych Azure AD.
- Uwierzytelnianie przekazywane nie jest zintegrowany z [programu Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md).
- Programu Apple Device Enrollment Program (DEP firmy Apple) przy użyciu Asystenta ustawień systemu iOS nie obsługują nowoczesnego uwierzytelniania. To nie będzie można zarejestrować urządzeń objętych programem DEP firmy Apple w usłudze Intune dla domeny zarządzanej przy użyciu przekazywanego uwierzytelniania. Należy rozważyć użycie [aplikacji Portal firmy](https://blogs.technet.microsoft.com/intunesupport/2018/02/08/support-for-multi-token-dep-and-authentication-with-company-portal/) alternatywą.

>[!IMPORTANT]
>Jako obejście dla nieobsługiwanych scenariuszy _tylko_, włączanie synchronizacji skrótów haseł na [funkcje opcjonalne](active-directory-aadconnect-get-started-custom.md#optional-features) strony kreatora Azure AD Connect. Gdy użytkownik zaloguje się do aplikacji na liście w "nieobsługiwane scenariusze" sekcji, te określone żądania logowania są _nie_ obsługiwany przez agentów uwierzytelniania przekazywanego i dlatego nie będą rejestrowane w [ Uwierzytelnianie przekazywane dzienniki](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs).

>[!NOTE]
Włączanie synchronizacji skrótów haseł z opcją uwierzytelnianie trybu failover w przypadku infrastruktury lokalnej jest zakłócona. Tego rodzaju tryb failover z uwierzytelniania przekazywanego do synchronizacji skrótów haseł usługi Active Directory nie jest automatyczna. Musisz przełączyć metodę logowania ręcznie za pomocą usługi Azure AD Connect. Jeśli serwer z programem Azure AD Connect ulegnie awarii, potrzebujesz pomocy z Microsoft Support wyłączyć uwierzytelnianie przekazywane.

## <a name="next-steps"></a>Kolejne kroki
- [Szybki start](active-directory-aadconnect-pass-through-authentication-quick-start.md): Szybkie rozpoczynanie pracy przy użyciu uwierzytelniania przekazywanego usługi AD platformy Azure.
- [Blokada Smart](../authentication/howto-password-smart-lockout.md): informacje dotyczące konfigurowania funkcji inteligentnej blokady na swoją dzierżawę, aby chronić kont użytkowników.
- [Rozbudowana technicznie](active-directory-aadconnect-pass-through-authentication-how-it-works.md): zrozumienie sposobu działania funkcji uwierzytelniania przekazywanego.
- [Często zadawane pytania dotyczące](active-directory-aadconnect-pass-through-authentication-faq.md): odpowiedzi na często zadawane pytania dotyczące funkcji uwierzytelniania przekazywanego.
- [Rozwiązywanie problemów z](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Dowiedz się, jak rozwiązać typowe problemy przy użyciu funkcji uwierzytelniania przekazywanego.
- [Szczegółowe omówienie zabezpieczeń](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): Uzyskaj informacje szczegółowe techniczne na temat funkcji uwierzytelniania przekazywanego.
- [Usługa Azure bezproblemowe logowanie Jednokrotne AD](active-directory-aadconnect-sso.md): więcej informacji na temat tej dodatkowej funkcji.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Użyj Forum usługi Azure Active Directory do pliku sugestie dotyczące nowych funkcji.
