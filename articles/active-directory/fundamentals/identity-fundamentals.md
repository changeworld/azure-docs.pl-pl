---
title: Jakie są podstawowe informacje dotyczące zarządzania tożsamościami i dostępem z platformą Azure? — Usługa azure Active Directory | Dokumentacja firmy Microsoft
description: Więcej informacji na temat możliwości zaawansowaną ochronę i dodatkowe narzędzia, które są dostępne w wersji usługi Azure Active Directory — wersja Premium.
services: active-directory
author: eross-msft
manager: mtillman
ms.author: lizross
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/13/2018
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: f7baa29c77ae4af9813bfc755a39cc07288a3ad2
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45734679"
---
# <a name="what-are-the-fundamentals-of-azure-identity-and-access-management"></a>Jakie są podstawowe informacje dotyczące zarządzania tożsamościami i dostępem z platformą Azure?
Usługa Azure AD Premium jest oparte na chmurze tożsamościami i dostępem rozwiązaniem do zarządzania, z możliwościami zaawansowaną ochronę. Te zaawansowane funkcje pomagają bezpieczna tożsamość dla wszystkich aplikacji, ochrony tożsamości (rozszerzony za pomocą [programu Microsoft graph security intelligence](https://www.microsoft.com/security/intelligence)), i [Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md). Usługa Azure AD pomaga w zabezpieczeniu tożsamości użytkowników w czasie rzeczywistym, ułatwiając tworzenie opartych na ryzykach zasady dostępu adaptacyjne wokół danych Twojej organizacji.

Obejrzyj ten krótki film wideo, aby zapoznać się z omówieniem zarządzania tożsamościami i ochrony tożsamości w usłudze Azure AD:
>[!VIDEO https://www.youtube.com/embed/9LGIJ2-FKIM]

Usługa Azure AD udostępnia również zestaw narzędzi, które mogą pomóc w bezpieczny, automatyzacji i zarządzania środowiskiem, w tym resetowaniem haseł, zarządzanie użytkownikami i grupami i żądania aplikacji. Usługa Azure AD może również ułatwić zarządzanie urządzeń należących do użytkowników i dostępu i kontroli nad oprogramowania jako usługi (SaaS).

Aby uzyskać więcej informacji o kosztach wersje usługi Azure Active Directory Premium systemu Azure i powiązane z nim narzędzia, zobacz [cennik usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>Łączenie lokalnej usługi Active Directory z usługą Azure AD oraz Office 365
Rozszerzanie wdrożenia usługi Active Directory środowiska lokalnego do chmury przez integrowanie katalogów lokalnych z usługą Azure AD za pomocą [hybrydowe Zarządzanie tożsamościami](https://aka.ms/aadframework). [Program Azure AD Connect](../connect/active-directory-aadconnect.md) zapewnia tej integracji przyznawanie użytkownikom jednej tożsamości i logowania jednokrotnego (SSO), dostęp do zasobów lokalnych i usług w chmurze, takich jak Office 365.

Program Azure AD Connect zastępuje starsze wersje narzędzi do integracji tożsamości, takie jak DirSync i Azure AD Sync, pomagając zgodnie z potrzebami synchronizacji tożsamości między lokalną i usługą Azure AD. Synchronizacja usługi Azure AD Connect jest włączana za pomocą:

- **Synchronizacja.** Odpowiedzialnych za tworzenie użytkowników, grup i innych obiektów. Jest również odpowiedzialny za upewnienie się, że informacje o tożsamości użytkowników w środowisku lokalnym odpowiada elementom w usłudze Azure AD. Włączanie zapisywania zwrotnego haseł pomaga również synchronizowanie katalogów lokalnych użytkowników zaktualizowania hasła w usłudze Azure AD.

- uwierzytelnianie. Ważne jest wybranie metody uwierzytelniania w prawo, podczas konfigurowania Twojego rozwiązania tożsamości hybrydowej usługi Azure AD. Można wybrać uwierzytelnianie w chmurze (synchronizacji skrótów haseł / uwierzytelnianie przekazywane) lub uwierzytelnianie federacyjne (AD FS) w swojej organizacji. Aby uzyskać więcej informacji na temat dostępnych opcji, zobacz [wybierz metodę uwierzytelniania odpowiednie dla Twojego rozwiązania tożsamości hybrydowej usługi Azure Active Directory](https://aka.ms/auth-options).

- **Monitorowanie kondycji.** Program Azure AD Connect Health oferuje funkcje monitorowania i centralnej lokalizacji w witrynie Azure portal, aby wyświetlić to działanie. Aby uzyskać więcej informacji, zobacz [Monitorowanie lokalnej infrastruktury do obsługi tożsamości i usług synchronizacji w chmurze](../connect-health/active-directory-aadconnect-health.md).

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>Zwiększanie produktywności i obniżanie kosztów pomocy technicznej dzięki funkcjom samoobsługi i logowania jednokrotnego
Użytkownicy zaoszczędzić czas, w których użytkownicy muszą jednej nazwy użytkownika i hasło, wraz ze spójnym środowiskiem na każdym urządzeniu. Użytkownicy oszczędzają czas, wykonując samoobsługi zadania, takie jak[zresetować zapomniane hasło](../user-help/active-directory-passwords-update-your-own-password.md) lub zażądać dostępu do aplikacji bez konieczności oczekiwania na pomocy od pomocy technicznej.

Wspieranie logowania jednokrotnego i spójne środowisko, usługi Azure AD [rozszerza usługi Active Directory w środowisku lokalnym](../connect/active-directory-aadconnect.md) do chmury, dzięki czemu Twoi użytkownicy mogli używać swoich podstawowego konta organizacyjnego dla swoich urządzeń przyłączonych do domeny firmy zasobów, i sieci web i aplikacji SaaS muszą korzystać wykonywane zadania. 

Ponadto dostęp do aplikacji można automatycznie aprowizowana (lub cofanie aprowizacji) na podstawie członkostwa w grupach i stanu użytkownika, ułatwiając kontrola dostępu do aplikacji z galerii lub własnych aplikacji w środowisku lokalnym, które zostały opracowane i opublikowane za pośrednictwem [serwera Proxy aplikacji usługi Azure AD](../manage-apps/application-proxy.md).

## <a name="manage-and-control-access-to-your-organizational-resources"></a>Zarządzanie i kontrola dostępu do zasobów organizacji
Rozwiązania zarządzania tożsamościami i dostępem firmy Microsoft pomagają chronić dostęp do aplikacji i zasobów w centrum danych Twojej organizacji i w chmurze. To zarządzanie dostępem pomaga zapewnić dodatkowe poziomy sprawdzania poprawności, takich jak [uwierzytelnianie wieloskładnikowe](../authentication/concept-mfa-howitworks.md) i [zasady dostępu warunkowego](../conditional-access/overview.md). Monitorowania podejrzanych działań przez zaawansowane raportowanie zabezpieczeń, inspekcji, a alerty mogą także ułatwiają eliminowanie potencjalnych problemów z zabezpieczeniami.

Za pomocą zasad dostępu warunkowego w usłudze Azure AD — wersja Premium umożliwia tworzenie reguł na podstawie zasad dostępu dla dowolnej aplikacji platformy Azure podłączonej do usługi AD, takich jak aplikacje SaaS, niestandardowe aplikacje działające w chmurze lub lokalnie lub aplikacji sieci web). Usługa Azure AD ocenia reguły w czasie rzeczywistym, wymuszając ich zastosowanie zawsze, gdy użytkownik próbuje uzyskać dostęp do aplikacji. Zasady ochrony usługi Azure identity pozwalają automatycznie podejmuje działania (przez blokowanie dostępu, wymuszanie uwierzytelniania wieloskładnikowego lub resetowanie haseł użytkowników) Jeśli został odnaleziony podejrzanych działań.

## <a name="azure-active-directory-privileged-identity-management"></a>Azure Active Directory Privileged Identity Management
[Privileged Identity Management (PIM)](../privileged-identity-management/pim-getting-started.md), dołączone do wersji usługi Azure Active Directory Premium 2, ułatwia odnajdowanie, ograniczanie i monitorowanie konta z uprawnieniami administracyjnymi oraz ich dostępu do zasobów w usłudze Azure Active Directory i innych Usługi online firmy Microsoft. Usługa PIM pomaga również administrowanie dostępu administracyjnego na żądanie dla dokładnego okres czasu, czego potrzebujesz, co oznacza, umożliwia administratorom żądania uwierzytelnienia Multi-Factor Authentication, tymczasowe podniesienia swoich uprawnień przez wstępnie skonfigurowany czas przed ich kont powrócić do stanu zwykłego użytkownika.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat architektury usługi Azure AD, zobacz [co to jest architektura usługi Azure AD?](active-directory-architecture.md).
