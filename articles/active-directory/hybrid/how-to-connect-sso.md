---
title: 'Program Azure AD Connect: Bezproblemowe logowanie jednokrotne | Dokumentacja firmy Microsoft'
description: W tym temacie opisano usługi Azure Active Directory (Azure AD) bezproblemowego logowania jednokrotnego i jak pozwala musisz podać wartość true, logowania jednokrotnego dla użytkowników firmowych komputerów stacjonarnych w sieci firmowej.
services: active-directory
keywords: Co to jest program Azure AD Connect, instalowanie usługi Active Directory, wymaganych składników dla usługi Azure AD, logowania jednokrotnego, logowanie jednokrotne
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c34d8de3dfd06540dd50542ab19da0c1d9b1567
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60242238"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Usługa Azure Active Directory bezproblemowego logowania jednokrotnego

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Co to jest Azure Active Directory bezproblemowego logowania jednokrotnego?

Bezproblemowe logowanie jednokrotne w usłudze Azure Active Directory zapewnia automatyczne logowanie użytkowników, gdy ich urządzenia są połączone z siecią firmową. Po włączeniu użytkownicy nie muszą wpisać hasła, aby zalogować się do usługi Azure AD, a nawet są zazwyczaj wpisz w ich nazw użytkowników. Ta funkcja zapewnia użytkownikom łatwy dostęp do aplikacji w chmurze bez konieczności używania dodatkowych składników lokalnych.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Bezproblemowe logowanie Jednokrotne może być łączone z albo [synchronizacji skrótów haseł](how-to-connect-password-hash-synchronization.md) lub [uwierzytelniania przekazywanego](how-to-connect-pta.md) metod logowania. Bezproblemowe logowanie Jednokrotne jest _nie_ mające zastosowanie do Active Directory Federation Services (ADFS).

![Bezproblemowe logowanie jednokrotne](./media/how-to-connect-sso/sso1.png)

>[!IMPORTANT]
>Bezproblemowe logowanie Jednokrotne wymaga urządzenia użytkownika, aby był **przyłączone do domeny**, ale nie musi dla urządzenia, aby był [usługi Azure AD dołączono](../active-directory-azureadjoin-overview.md).

## <a name="key-benefits"></a>Najważniejsze korzyści

- *Fantastyczne środowisko pracy użytkownika*
  - Użytkownicy zostaną automatycznie wylogowani zarówno lokalnych, jak i aplikacji działających w chmurze.
  - Użytkownicy nie musieli wprowadzać haseł wielokrotnie.
- *Ułatwia wdrażanie i administrowanie*
  - Nie dodatkowe składniki wymagane w środowisku lokalnym, aby umożliwić.
  - Działa przy użyciu dowolnej metody uwierzytelniania w chmurze — [synchronizacji skrótów haseł](how-to-connect-password-hash-synchronization.md) lub [uwierzytelniania przekazywanego](how-to-connect-pta.md).
  - Mogą być wprowadzane do niektórych lub wszystkich użytkowników za pomocą zasad grupy.
  - Rejestrowanie urządzeń do systemu Windows 10 z usługą Azure AD bez konieczności żadnej infrastruktury usług AD FS. Ta funkcja wymaga, aby użyć wersji 2.1 lub nowszej [dołączenie do miejsca pracy klienta](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Funkcja wyróżnia

- Nazwa logowania użytkownika mogą być albo w środowisku lokalnym domyślna nazwa użytkownika (`userPrincipalName`) lub inny atrybut skonfigurowanego w Azure AD Connect (`Alternate ID`). Użyć obu przypadkach pracy, ponieważ używa bezproblemowe logowanie Jednokrotne `securityIdentifier` oświadczenia w bilecie protokołu Kerberos, aby wyszukać odpowiedni obiekt użytkownika w usłudze Azure AD.
- Bezproblemowe logowanie Jednokrotne jest funkcją oportunistyczne. Jeśli jej nie powiedzie się z jakiegokolwiek powodu, środowisko logowania użytkownika powraca do regularnych zachowań — tzn, użytkownik musi wprowadzić swoje hasło na stronie logowania.
- Jeśli aplikacja (na przykład `https://myapps.microsoft.com/contoso.com`) przekazuje `domain_hint` (OpenID Connect) lub `whr` (SAML) parametr - identyfikowania dzierżawy lub `login_hint` parametr - identyfikowania użytkowników, w jego usługi Azure AD żądanie logowania, użytkownicy są zostanie automatycznie zalogowany bez ich wprowadzania nazwy użytkownika i hasła.
- Użytkownicy uzyskują również dyskretnej środowisko logowania jednokrotnego w przypadku aplikacji (na przykład `https://contoso.sharepoint.com`) wysyła żądania rejestrowania do punktów końcowych usługi Azure AD jako dzierżawy — oznacza to, `https://login.microsoftonline.com/contoso.com/<..>` lub `https://login.microsoftonline.com/<tenant_ID>/<..>` — zamiast usługi Azure AD wspólnego punktu końcowego — czyli `https://login.microsoftonline.com/common/<...>` .
- Wyloguj się jest obsługiwane. Dzięki temu użytkownicy mogą wybrać inne konto usługi Azure AD do zalogowania, a nie są automatycznie zalogować się przy użyciu bezproblemowe logowanie Jednokrotne automatycznie.
- Klienci usługi Office 365 Win32 (Outlook, Word, Excel i innych) z wersji 16.0.8730.xxxx lub nowszym są obsługiwane przy użyciu nieinterakcyjnych usługi flow. Do usługi OneDrive, musisz aktywować [OneDrive dyskretnej konfiguracji funkcji](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) dyskretnej środowisko logowania jednokrotnego.
- Można ją włączyć za pomocą usługi Azure AD Connect.
- Jest bezpłatną funkcją, które nie potrzebują żadnych płatnej wersji usługi Azure AD z niego korzystać.
- Jest on obsługiwany w klientów przeglądarki sieci web oraz klientów pakietu Office, które obsługują [nowoczesnego uwierzytelniania](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) na platformach i w przeglądarkach zdolne do uwierzytelniania Kerberos:

| OS\Browser |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Tak\*|Nie|Tak|Tak\*\*\*|ND
|Windows 8.1|Tak\*|ND|Tak|Tak\*\*\*|ND
|Windows 8|Tak\*|ND|Yes|Tak\*\*\*|ND
|Windows 7|Tak\*|ND|Tak|Tak\*\*\*|ND
|Windows Server 2012 R2 lub nowszy|Tak\*\*|ND|Tak|Tak\*\*\*|ND
|Mac OS X|ND|ND|Tak\*\*\*|Tak\*\*\*|Tak\*\*\*


\*Wymaga programu Internet Explorer w wersji 10 lub nowszy

\*\*Wymaga programu Internet Explorer w wersji 10 lub nowszej. Wyłącz rozszerzony tryb chroniony

\*\*\*Wymaga [dodatkowej konfiguracji](how-to-connect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>Dla systemu Windows 10, zaleca się użycie [Azure AD Join](../active-directory-azureadjoin-overview.md) dla optymalnej logowanie jednokrotne z usługą Azure AD.

## <a name="next-steps"></a>Kolejne kroki

- [**Przewodnik Szybki Start** ](how-to-connect-sso-quick-start.md) — Rozpocznij pracę, a systemem Azure bezproblemowe logowanie Jednokrotne usługi AD.
- [**Plan wdrożenia** ](https://aka.ms/AuthenticationDeploymentPlan) — plan wdrożenie krok po kroku.
- [**Rozbudowana technicznie** ](how-to-connect-sso-how-it-works.md) -zrozumienie sposobu działania tej funkcji.
- [**Często zadawane pytania** ](how-to-connect-sso-faq.md) — odpowiedzi na często zadawane pytania.
- [**Rozwiązywanie problemów z** ](tshoot-connect-sso.md) — Dowiedz się, jak rozwiązać typowe problemy z funkcją.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) — w przypadku zgłaszania sugestie dotyczące nowych funkcji.

