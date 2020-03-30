---
title: 'Usługa Azure AD Connect: bezproblemowe logowanie jednokrotne | Dokumenty firmy Microsoft'
description: W tym temacie opisano bezproblemowe logowanie jednokrotne usługi Azure Active Directory (Azure AD) i sposób, w jaki umożliwia zapewnienie prawdziwego logowania jednokrotnego dla użytkowników komputerów stacjonarnych w sieci firmowej.
services: active-directory
keywords: co to jest usługa Azure AD Connect, instalowanie usługi Active Directory, wymagane składniki dla usługi Azure AD, logowania jednokrotnego, logowania jednokrotnego
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1b7e4716e731e6b73e3ac60b64baa71043906fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77483758"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Bezproblemowe logowanie usługi Azure Active Directory

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Co to jest bezproblemowe logowanie jednokrotne usługi Azure Active Directory?

Bezproblemowe logowanie jednokrotne w usłudze Azure Active Directory zapewnia automatyczne logowanie użytkowników, gdy ich urządzenia są połączone z siecią firmową. Po włączeniu użytkownicy nie muszą wpisywać swoich haseł, aby zalogować się do usługi Azure AD, a zwykle nawet wpisywać swoje nazwy użytkowników. Ta funkcja zapewnia użytkownikom łatwy dostęp do aplikacji w chmurze bez konieczności używania dodatkowych składników lokalnych.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Bezproblemowe logowanie jednokrotne można łączyć z [metodami logowania hash hasła](how-to-connect-password-hash-synchronization.md) lub [uwierzytelniania przekazywania.](how-to-connect-pta.md) Bezproblemowe logowanie jednokrotne _nie_ ma zastosowania do usług federacyjnych Active Directory (ADFS).

![Bezproblemowe logowanie jednokrotne](./media/how-to-connect-sso/sso1.png)

>[!IMPORTANT]
>Bezproblemowe logowanie jednokrotne wymaga, aby urządzenie użytkownika było tylko **przyłączone** do domeny, ale nie jest używane na urządzeniach [przyłączonych do usługi Azure AD](../devices/concept-azure-ad-join.md) lub hybrydowych urządzeniach [przyłączonych do usługi Azure AD.](../devices/concept-azure-ad-join-hybrid.md) Przyłączone do usługi Azure AD i hybrydowa usługa Azure AD dołączyły do pracy na podstawie [podstawowego tokenu odświeżania.](../devices/concept-primary-refresh-token.md)

## <a name="key-benefits"></a>Najważniejsze korzyści

- *Fantastyczne środowisko pracy użytkownika*
  - Użytkownicy są automatycznie logowane do aplikacji lokalnych i opartych na chmurze.
  - Użytkownicy nie muszą wielokrotnie wprowadzać swoich haseł.
- *Łatwe wdrażanie & administrowanie*
  - Aby to działało, nie są potrzebne żadne dodatkowe składniki lokalnie.
  - Działa z dowolną metodą uwierzytelniania w chmurze — [synchronizacją skrótów haseł](how-to-connect-password-hash-synchronization.md) lub [uwierzytelnianiem przekazy.](how-to-connect-pta.md)
  - Można wdrożyć dla niektórych lub wszystkich użytkowników przy użyciu zasad grupy.
  - Rejestrowanie urządzeń innych niż Windows 10 w usłudze Azure AD bez konieczności korzystania z infrastruktury usług AD FS. Ta funkcja wymaga użycia wersji 2.1 lub nowszej [klienta dołączania do miejsca pracy.](https://www.microsoft.com/download/details.aspx?id=53554)

## <a name="feature-highlights"></a>Najważniejsze informacje o funkcjach

- Nazwa użytkownika logowania może być domyślną nazwą użytkownika`userPrincipalName`lokalnego ( ) lub innym`Alternate ID`atrybutem skonfigurowanym w usłudze Azure AD Connect ( ). Oba przypadki użycia działają, ponieważ bezproblemowe logowanie jednokrotne używa `securityIdentifier` oświadczenia w bilecie Kerberos, aby wyszukać odpowiedni obiekt użytkownika w usłudze Azure AD.
- Bezszwowe logowanie jednokrotne jest funkcją oportunistyczną. Jeśli z jakiegoś powodu nie powiedzie się, środowisko logowania użytkownika powraca do swojego regularnego zachowania — to znaczy, że użytkownik musi wprowadzić swoje hasło na stronie logowania.
- Jeśli aplikacja (na `https://myapps.microsoft.com/contoso.com`przykład ) `domain_hint` przekazuje (OpenID `whr` Connect) lub (SAML) parametr `login_hint` - identyfikowanie dzierżawy lub parametru - identyfikując użytkownika, w żądaniu logowania usługi Azure AD, użytkownicy są automatycznie zalogowany bez wprowadzania nazw użytkowników lub haseł.
- Użytkownicy `https://contoso.sharepoint.com`otrzymują również ciche środowisko logowania, `https://login.microsoftonline.com/contoso.com/<..>` jeśli aplikacja (na przykład) wysyła żądania logowania do punktów końcowych usługi `https://login.microsoftonline.com/<tenant_ID>/<..>` Azure AD skonfigurowanych jako dzierżawcy — czyli `https://login.microsoftonline.com/common/<...>`lub — zamiast wspólnego punktu końcowego usługi Azure AD — czyli .
- Wyloguj się jest obsługiwany. Dzięki temu użytkownicy mogą wybrać inne konto usługi Azure AD, aby się zalogować, zamiast automatycznie logować się przy użyciu bezproblemowego logowania jednokrotnego.
- Klienci usługi Office 365 Win32 (Outlook, Word, Excel i inni) w wersji 16.0.8730.xxxx i nowszej są obsługiwani przy użyciu przepływu nieinteraktywnego. W usłudze OneDrive należy aktywować [funkcję cichej konfiguracji usługi OneDrive,](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) aby uzyskać funkcję cichego logowania.
- Można go włączyć za pośrednictwem usługi Azure AD Connect.
- Jest to bezpłatna funkcja i nie potrzebujesz żadnych płatnych wersji usługi Azure AD, aby z niej korzystać.
- Jest on obsługiwany na klientach opartych na przeglądarce internetowej i klientach pakietu Office, które obsługują [nowoczesne uwierzytelnianie](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) na platformach i przeglądarkach obsługujących uwierzytelnianie Kerberos:

| System operacyjny\Przeglądarka |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Tak\*|Tak|Tak|Tak\*\*\*|Nie dotyczy
|Windows 8.1|Tak\*|Nie dotyczy|Tak|Tak\*\*\*|Nie dotyczy
|Windows 8|Tak\*|Nie dotyczy|Tak|Tak\*\*\*|Nie dotyczy
|Windows 7|Tak\*|Nie dotyczy|Tak|Tak\*\*\*|Nie dotyczy
|Windows Server 2012 R2 lub wyższy|Tak\*\*|Nie dotyczy|Tak|Tak\*\*\*|Nie dotyczy
|Mac OS X|Nie dotyczy|Nie dotyczy|Tak\*\*\*|Tak\*\*\*|Tak\*\*\*


\*Wymaga programu Internet Explorer w wersji 10 lub nowszej

\*\*Wymaga programu Internet Explorer w wersji 10 lub nowszej. Wyłącz rozszerzony tryb chroniony

\*\*\*Wymaga [dodatkowej konfiguracji](how-to-connect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>W systemie Windows 10 zalecenie jest użycie [usługi Azure AD Join](../devices/concept-azure-ad-join.md) dla optymalnego środowiska logowania jednokrotnego z usługą Azure AD.

## <a name="next-steps"></a>Następne kroki

- [**Szybki start**](how-to-connect-sso-quick-start.md) — wystartuj i uruchamiaj bezproblemowe logowanie jednokrotne usługi Azure AD.
- [**Plan wdrażania**](https://aka.ms/deploymentplans/sso) — plan wdrażania krok po kroku.
- [**Głębokie nurkowanie techniczne**](how-to-connect-sso-how-it-works.md) — dowiedz się, jak działa ta funkcja.
- [**Najczęściej zadawane pytania**](how-to-connect-sso-faq.md) — odpowiedzi na często zadawane pytania.
- [**Rozwiązywanie problemów**](tshoot-connect-sso.md) — dowiedz się, jak rozwiązać typowe problemy z tą funkcją.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) — do składania nowych żądań funkcji.

