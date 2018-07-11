---
title: 'Azure AD Connect: Bezproblemowe logowanie jednokrotne | Dokumentacja firmy Microsoft'
description: W tym temacie opisano usługi Azure Active Directory (Azure AD) bezproblemowego logowania jednokrotnego i jak pozwala musisz podać wartość true, logowania jednokrotnego dla użytkowników firmowych komputerów stacjonarnych w sieci firmowej.
services: active-directory
keywords: Co to jest program Azure AD Connect, instalowanie usługi Active Directory, wymaganych składników dla usługi Azure AD, logowania jednokrotnego, logowanie jednokrotne
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
ms.openlocfilehash: ad7c412ee92db53dd797e38df2fc6db0a762fe78
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916170"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Usługa Azure Active Directory bezproblemowego logowania jednokrotnego

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Co to jest Azure Active Directory bezproblemowego logowania jednokrotnego?

Azure Active Directory bezproblemowe logowanie jednokrotne (Azure AD bezproblemowe logowanie Jednokrotne) automatycznie zaloguje się użytkowników, gdy są one na urządzeniach firmowych połączonych z siecią firmową. Po włączeniu użytkownicy nie muszą wpisać hasła, aby zalogować się do usługi Azure AD, a nawet są zazwyczaj wpisz w ich nazw użytkowników. Ta funkcja zapewnia użytkownikom łatwy dostęp do aplikacji w chmurze bez żadnych dodatkowych lokalnych składników.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Bezproblemowe logowanie Jednokrotne może być łączone z albo [synchronizacji skrótów haseł](active-directory-aadconnectsync-implement-password-hash-synchronization.md) lub [uwierzytelniania przekazywanego](active-directory-aadconnect-pass-through-authentication.md) metod logowania.

![Bezproblemowe logowanie jednokrotne](./media/active-directory-aadconnect-sso/sso1.png)

>[!IMPORTANT]
>Bezproblemowe logowanie Jednokrotne jest _nie_ mające zastosowanie do Active Directory Federation Services (ADFS).

## <a name="key-benefits"></a>Najważniejsze korzyści

- *Doskonałe środowisko użytkownika*
  - Użytkownicy zostaną automatycznie wylogowani zarówno lokalnych, jak i aplikacji działających w chmurze.
  - Użytkownicy nie musieli wprowadzać haseł wielokrotnie.
- *Ułatwia wdrażanie i administrowanie*
  - Nie dodatkowe składniki wymagane w środowisku lokalnym, aby umożliwić.
  - Działa przy użyciu dowolnej metody uwierzytelniania w chmurze — [synchronizacji skrótów haseł](active-directory-aadconnectsync-implement-password-hash-synchronization.md) lub [uwierzytelniania przekazywanego](active-directory-aadconnect-pass-through-authentication.md).
  - Mogą być wprowadzane do niektórych lub wszystkich użytkowników za pomocą zasad grupy.
  - Rejestrowanie urządzeń do systemu Windows 10 z usługą Azure AD bez konieczności żadnej infrastruktury usług AD FS. Ta funkcja wymaga, aby użyć wersji 2.1 lub nowszej [dołączenie do miejsca pracy klienta](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Funkcja wyróżnia

- Nazwa logowania użytkownika mogą być albo w środowisku lokalnym domyślna nazwa użytkownika (`userPrincipalName`) lub inny atrybut skonfigurowanego w Azure AD Connect (`Alternate ID`). Użyć obu przypadkach pracy, ponieważ używa bezproblemowe logowanie Jednokrotne `securityIdentifier` oświadczenia w bilecie protokołu Kerberos, aby wyszukać odpowiedni obiekt użytkownika w usłudze Azure AD.
- Bezproblemowe logowanie Jednokrotne jest funkcją oportunistyczne. Jeśli jej nie powiedzie się z jakiegokolwiek powodu, środowisko logowania użytkownika powraca do regularnych zachowań — tzn, użytkownik musi wprowadzić swoje hasło na stronie logowania.
- Jeśli aplikacja (na przykład https://myapps.microsoft.com/contoso.com) przekazuje `domain_hint` (OpenID Connect) lub `whr` (SAML) parametr - identyfikowania dzierżawy lub `login_hint` parametr - identyfikowania użytkowników, w jego usługi Azure AD żądanie logowania, użytkownicy są zostanie automatycznie zalogowany bez ich wprowadzania nazwy użytkownika i hasła.
- Użytkownicy uzyskują również dyskretnej środowisko logowania jednokrotnego w przypadku aplikacji (na przykład https://contoso.sharepoint.com) wysyła żądania rejestrowania do gośćmi punkty końcowe usługi Azure AD — oznacza to, https://login.microsoftonline.com/contoso.com/<..> lub https://login.microsoftonline.com/<tenant_ID>/<..> — zamiast usługi Azure AD wspólnego punktu końcowego — czyli https://login.microsoftonline.com/common/<...>.
- Wyloguj się jest obsługiwane. Dzięki temu użytkownicy mogą wybrać inne konto usługi Azure AD do zalogowania, a nie są automatycznie zalogować się przy użyciu bezproblemowe logowanie Jednokrotne automatycznie.
- Klienci usługi Office 365 (16.0.8730.xxxx i nowsze wersje) są obsługiwane przy użyciu nieinterakcyjnych usługi flow.
- Można ją włączyć za pomocą usługi Azure AD Connect.
- Jest bezpłatną funkcją, które nie potrzebują żadnych płatnej wersji usługi Azure AD z niego korzystać.
- Jest on obsługiwany w klientów przeglądarki sieci web oraz klientów pakietu Office, które obsługują [nowoczesnego uwierzytelniania](https://aka.ms/modernauthga) na platformach i w przeglądarkach zdolne do uwierzytelniania Kerberos:

| OS\Browser |Internet Explorer|Brzeg|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Yes|Nie|Yes|Tak\*|ND
|Windows 8.1|Yes|ND|Yes|Tak\*|ND
|Windows 8|Yes|ND|Yes|Tak\*|ND
|Windows 7|Yes|ND|Yes|Tak\*|ND
|Mac OS X|ND|ND|Tak\*|Tak\*|Tak\*

\*Wymaga [dodatkowej konfiguracji](active-directory-aadconnect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>Dla systemu Windows 10, zaleca się użycie [Azure AD Join](../active-directory-azureadjoin-overview.md) dla optymalnej logowanie jednokrotne z usługą Azure AD.

## <a name="next-steps"></a>Kolejne kroki

- [**Przewodnik Szybki Start** ](active-directory-aadconnect-sso-quick-start.md) — Rozpocznij pracę, a systemem Azure bezproblemowe logowanie Jednokrotne usługi AD.
- [**Rozbudowana technicznie** ](active-directory-aadconnect-sso-how-it-works.md) -zrozumienie sposobu działania tej funkcji.
- [**Często zadawane pytania** ](active-directory-aadconnect-sso-faq.md) — odpowiedzi na często zadawane pytania.
- [**Rozwiązywanie problemów z** ](active-directory-aadconnect-troubleshoot-sso.md) — Dowiedz się, jak rozwiązać typowe problemy z funkcją.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) — w przypadku zgłaszania sugestie dotyczące nowych funkcji.
