---
title: 'Azure AD Connect: Bezproblemowe logowanie jednokrotne | Dokumentacja firmy Microsoft'
description: W tym temacie opisano usługi Azure Active Directory (Azure AD) bezproblemowe logowanie jednokrotne i jak umożliwia podania true logowania jednokrotnego do firmowych pulpitów użytkowników w sieci firmowej.
services: active-directory
keywords: Co to jest usługa Azure AD Connect, zainstaluj usługę Active Directory, wymaganych składników dla usługi Azure AD, SSO, Single Sign-on
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
ms.author: billmath
ms.openlocfilehash: 6a106ba59f09ffc20f8461c5267ae06695df4cd0
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Usługa Azure Active Directory bezproblemowe logowanie jednokrotne

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Co to jest Azure Active Directory bezproblemowe logowanie jednokrotne?

Azure Active Directory bezproblemowe logowanie jednokrotne (Azure AD bezproblemowe logowanie Jednokrotne) automatycznie podpisuje użytkowników, gdy są w ich urządzenia podłączone do sieci firmowej. Po włączeniu użytkownicy nie muszą o wpisanie hasła, aby zalogować się do usługi Azure AD i zazwyczaj nawet wpisz w ich nazwami użytkowników. Ta funkcja zapewnia użytkownikom łatwy dostęp do aplikacji opartej na chmurze bez konieczności żadnych składników dodatkowych lokalnych.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Bezproblemowe logowanie Jednokrotne można łączyć z jedną [synchronizacji skrótu hasła](active-directory-aadconnectsync-implement-password-hash-synchronization.md) lub [uwierzytelniania przekazywanego](active-directory-aadconnect-pass-through-authentication.md) metody logowania.

![Bezproblemowe logowanie jednokrotne](./media/active-directory-aadconnect-sso/sso1.png)

>[!IMPORTANT]
>Bezproblemowe rejestracji Jednokrotnej jest _nie_ mające zastosowanie do Active Directory Federation Services (ADFS).

## <a name="key-benefits"></a>Najważniejsze korzyści

- *Środowisko użytkowników*
  - Użytkownicy zostaną automatycznie wylogowani do zarówno lokalnie, jak i aplikacje oparte na chmurze.
  - Użytkownicy nie muszą wprowadzać haseł wielokrotnie.
- *Łatwe wdrażanie i administrowanie*
  - Żadne dodatkowe składniki niezbędne dane lokalne na działało.
  - Działa przy użyciu dowolnej metody uwierzytelniania w chmurze — [synchronizacji skrótu hasła](active-directory-aadconnectsync-implement-password-hash-synchronization.md) lub [uwierzytelniania przekazywanego](active-directory-aadconnect-pass-through-authentication.md).
  - Mogą być wprowadzanie do niektórych lub wszystkich użytkowników za pomocą zasad grupy.
  - Rejestrowanie urządzeń z systemem innym niż Windows 10 z usługą Azure AD bez konieczności dla dowolnej infrastruktury usług AD FS. Ta funkcja wymaga użycia wersji 2.1 lub nowszej [Dołącz do miejsca pracy klienta](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Zawiera opis funkcji

- Nazwa logowania użytkownika może być albo lokalnymi domyślna nazwa użytkownika (`userPrincipalName`) lub inny atrybut skonfigurowane w programie Azure AD Connect (`Alternate ID`). Użyć obu przypadkach pracy, ponieważ używa bezproblemowe logowania jednokrotnego `securityIdentifier` oświadczeń w bilecie protokołu Kerberos w celu wyszukania odpowiedniego obiektu użytkownika w usłudze Azure AD.
- Bezproblemowe rejestracji Jednokrotnej jest funkcją oportunistyczne. Jeśli go nie powiedzie się z jakiegokolwiek powodu, środowisko logowania użytkownika Przechodzi wstecz na nim - tj, użytkownik musi wprowadzić swoje hasło na stronie logowania.
- Jeśli aplikacja (na przykład https://myapps.microsoft.com/contoso.com) przekazuje `domain_hint` (OpenID Connect) lub `whr` parametr (SAML) - identyfikowania Twojej dzierżawy lub `login_hint` parametru - identyfikacji użytkownika, w jego usługi Azure AD żądania logowania, użytkownicy są zostanie automatycznie zalogowany bez ich wprowadzania nazwy użytkownika i hasła.
- Użytkownicy także uzyskać dyskretnej środowisko logowania, jeśli aplikacja (na przykład https://contoso.sharepoint.com) oznacza to, wysyła żądań logowania do usługi Azure AD dzierżawcza punkty końcowe — https://login.microsoftonline.com/contoso.com/<..> lub https://login.microsoftonline.com/<tenant_ID>/<..> — zamiast usługi Azure AD wspólnego punktu końcowego — to znaczy https://login.microsoftonline.com/common/<...>.
- Wyloguj jest obsługiwana. Dzięki temu użytkownicy mogą wybrać inne konto usługi Azure AD do zalogowania, zamiast automatycznie podpisywana przy użyciu logowania jednokrotnego bezproblemowe automatycznie.
- Klienci Office 365 (16.0.8730.xxxx i nowsze) są obsługiwane przy użyciu podejścia nieinterakcyjnego przepływu.
- Można ją włączyć za pomocą usługi Azure AD Connect.
- Jest bezpłatna funkcji i nie wymagają żadnych wersji płatnej usługi Azure AD z niego korzystać.
- Jest obsługiwana na klientach przeglądarki sieci web i klientom pakietu Office, które obsługują [nowoczesnego uwierzytelniania](https://aka.ms/modernauthga) na różnych platformach i przeglądarki umożliwia uwierzytelnianie Kerberos:

| OS\Browser |Internet Explorer|Brzeg|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Yes|Nie|Yes|Tak\*|ND
|Windows 8.1|Yes|ND|Yes|Tak\*|ND
|Windows 8|Yes|ND|Yes|Tak\*|ND
|Windows 7|Yes|ND|Yes|Tak\*|ND
|Mac OS X|ND|ND|Tak\*|Tak\*|Tak\*

\*Wymaga [dodatkowej konfiguracji](active-directory-aadconnect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>W systemie Windows 10, zalecane jest użycie [Azure AD Join](../active-directory-azureadjoin-overview.md) w celu uzyskania optymalnej pojedynczego logowania jednokrotnego wyników z usługą Azure AD.

## <a name="next-steps"></a>Kolejne kroki

- [**Szybki Start** ](active-directory-aadconnect-sso-quick-start.md) — Uzyskaj i systemem Azure AD bezproblemowe Usługa rejestracji Jednokrotnej.
- [**Nowości techniczne** ](active-directory-aadconnect-sso-how-it-works.md) -zrozumieć sposób działania tej funkcji.
- [**Często zadawane pytania** ](active-directory-aadconnect-sso-faq.md) — odpowiedzi na często zadawane pytania.
- [**Rozwiązywanie problemów z** ](active-directory-aadconnect-troubleshoot-sso.md) — Dowiedz się, jak rozwiązać typowe problemy z funkcją.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) — w przypadku zgłoszenia żądania nowych funkcji.
