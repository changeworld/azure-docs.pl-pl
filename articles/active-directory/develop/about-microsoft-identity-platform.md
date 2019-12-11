---
title: Ewolucja platformy tożsamości firmy Microsoft — Azure
description: Dowiedz się więcej o platformie tożsamości firmy Microsoft, ewolucji usługi tożsamości Azure Active Directory (Azure AD) i platformy deweloperskiej.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 12/09/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e5faf9db9a6ba97f353b54a4fb8123fde072574
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74996990"
---
# <a name="evolution-of-microsoft-identity-platform"></a>Ewolucja platformy tożsamości firmy Microsoft

Platforma tożsamości firmy Microsoft jest ewolucją platformy deweloperskiej Azure Active Directory (Azure AD). Dzięki temu deweloperzy mogą tworzyć aplikacje, które logują użytkowników, uzyskują tokeny do wywoływania interfejsów API, takich jak Microsoft Graph lub interfejsy API zaprojektowane przez deweloperów. Obejmuje ona usługę uwierzytelniania, biblioteki typu open-source, rejestrację aplikacji i konfigurację (za pomocą portalu dla deweloperów i interfejsu API aplikacji), pełną dokumentację dla deweloperów, przykłady przewodnika Szybki Start, przykłady kodu, samouczki, przewodniki i inna zawartość dla deweloperów. Platforma tożsamości firmy Microsoft obsługuje standardowe protokoły branżowe, takie jak OAuth 2.0 i OpenID Connect.

Do tej pory większość deweloperów pracowała z platformą Azure AD v 1.0 do uwierzytelniania kont służbowych (zainicjowanych przez usługę Azure AD) przez żądanie tokenów z punktu końcowego usługi Azure AD v 1.0, przy użyciu biblioteki Azure AD Authentication Library (ADAL), Azure Portal dla programu Rejestracja i konfiguracja aplikacji oraz interfejs API programu Graph usługi Azure AD na potrzeby konfiguracji aplikacji programistycznych.

Za pomocą ujednoliconej platformy tożsamości firmy Microsoft (v 2.0) można napisać kod jednokrotnie i uwierzytelnić tożsamość firmy Microsoft w swojej aplikacji. W przypadku kilku platform zaleca się używanie w pełni obsługiwanej biblioteki uwierzytelniania Microsoft Open Source (MSAL) w odniesieniu do punktów końcowych platformy tożsamości. Usługa MSAL jest prosta do użycia, zapewnia użytkownikom doskonałe funkcje logowania jednokrotnego (SSO, Single Sign-on), pomaga osiągnąć wysoką niezawodność i wydajność oraz jest opracowywana przy użyciu usługi Microsoft Secure Development (SDL). Podczas wywoływania interfejsów API można skonfigurować aplikację tak, aby korzystała z powiększej zgody, która pozwala opóźnić żądanie zgody na bardziej inwazyjne zakresy, dopóki użycie aplikacji zagwarantuje to w czasie wykonywania.  Program MSAL obsługuje również Azure Active Directory B2C, więc klienci używają własnych tożsamości dla konta społecznościowego, przedsiębiorstwa lub lokalnego do uzyskiwania dostępu jednokrotnego do aplikacji i interfejsów API.

Korzystając z platformy tożsamości firmy Microsoft, Rozszerz swój dostęp do tego rodzaju użytkowników:

- Konta służbowe (konta aprowizacji usługi Azure AD)
- Konta osobiste (takie jak Outlook.com lub Hotmail.com)
- Klienci, którzy korzystają z własnych wiadomości e-mail lub tożsamości społecznościowej (takich jak LinkedIn, Facebook, Google) za pośrednictwem MSAL i Azure AD B2C

Za pomocą Azure Portal można zarejestrować i skonfigurować aplikację oraz użyć interfejsu API Microsoft Graph do konfiguracji aplikacji programistycznych.

Zaktualizuj swoją aplikację w swoim własnym tempie. Aplikacje skompilowane przy użyciu bibliotek ADAL są nadal obsługiwane. Obsługiwane są również mieszane portfolio aplikacji, które obejmują aplikacje skompilowane przy użyciu biblioteki ADAL i aplikacje skompilowane przy użyciu bibliotek MSAL. Oznacza to, że aplikacje korzystające z najnowszej biblioteki ADAL i najnowszej MSAL będą dostarczać Logowanie jednokrotne w portfolio, zapewniane przez pamięć podręczną tokenów udostępnionych między tymi bibliotekami. Aplikacje zaktualizowane z biblioteki ADAL do MSAL będą obsługiwać stan logowania użytkownika po uaktualnieniu.

## <a name="microsoft-identity-platform-experience"></a>Środowisko platformy tożsamości firmy Microsoft

Na poniższym diagramie przedstawiono ogólnie środowisko obsługi tożsamości firmy Microsoft, obejmujące środowisko rejestracji aplikacji, zestawy SDK, punkty końcowe i obsługiwane tożsamości.

![Platforma tożsamości firmy Microsoft obecnie](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>Środowisko rejestracji aplikacji

Środowisko **[Rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908)** Azure Portal to jedno środowisko portalu do zarządzania wszystkimi aplikacjami, które zostały zintegrowane z platformą tożsamości firmy Microsoft. Jeśli korzystasz z portalu rejestracji aplikacji, zamiast tego zacznij korzystać z Azure Portal rejestracji aplikacji.

Aby przeprowadzić integrację z usługą Azure AD B2C (podczas uwierzytelniania tożsamości społecznościowych lub lokalnych), należy zarejestrować aplikację w dzierżawie Azure AD B2C. To środowisko jest również częścią Azure Portal.

**Interfejs API aplikacji w Microsoft Graph** jest obecnie w wersji zapoznawczej. Ten interfejs API umożliwia programowe Konfigurowanie aplikacji zintegrowanych z platformą tożsamości firmy Microsoft na potrzeby uwierzytelniania dowolnej tożsamości firmy Microsoft. Jednak do momentu osiągnięcia ogólnej dostępności tego interfejsu API należy użyć interfejsu API programu Azure AD Graph 1,6 i manifestu aplikacji.

### <a name="msal-libraries"></a>Biblioteki MSAL

Biblioteka MSAL może służyć do kompilowania aplikacji, które uwierzytelniają wszystkie tożsamości firmy Microsoft. Biblioteki MSAL w programie .NET i JavaScript są ogólnie dostępne. Biblioteki MSAL dla systemów iOS i Android są w wersji zapoznawczej i są odpowiednie do użycia w środowisku produkcyjnym. Zapewniamy tę samą obsługę produkcyjną dla bibliotek MSAL w wersji zapoznawczej, tak jak w przypadku systemów MSAL i ADAL, które są ogólnie dostępne.

Za pomocą bibliotek MSAL można także zintegrować swoją aplikację z Azure AD B2C.

Biblioteki po stronie serwera do tworzenia aplikacji sieci Web i interfejsów API sieci Web są ogólnie dostępne: [ASP.NET](https://docs.microsoft.com/aspnet/overview) i [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2)

### <a name="microsoft-identity-platform-endpoint"></a>Punkt końcowy platformy tożsamości firmy Microsoft

Punkt końcowy Microsoft Identity platform (v 2.0) jest teraz certyfikowany OIDC. Współpracuje z bibliotekami uwierzytelniania firmy Microsoft (MSAL) lub dowolną inną biblioteką zgodną ze standardami. Służy do implementowania zakresów ludzkich, zgodnie ze standardami branżowymi.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat wersji 1.0 i 2.0.

* [Microsoft Identity platform (v 2.0) — Omówienie](v2-overview.md)
* [Azure Active Directory dla deweloperów (v 1.0) — Omówienie](v1-overview.md)
