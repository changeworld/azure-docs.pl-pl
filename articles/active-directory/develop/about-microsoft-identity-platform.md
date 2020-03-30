---
title: Ewolucja platformy tożsamości firmy Microsoft — Platforma Azure
description: Dowiedz się więcej o platformie tożsamości firmy Microsoft, ewolucji usługi tożsamości usługi Azure Active Directory (Azure AD) i platformy deweloperskiej.
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
ms.openlocfilehash: 8714b7a96197cb4a59b29bada31b5559961bf8e3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78300217"
---
# <a name="evolution-of-microsoft-identity-platform"></a>Ewolucja platformy tożsamości firmy Microsoft

Platforma tożsamości firmy Microsoft jest ewolucją platformy deweloperskiej Azure Active Directory (Azure AD). Umożliwia deweloperom tworzenie aplikacji, które logują się do użytkowników, pobierz tokeny do wywoływania interfejsów API, takich jak Microsoft Graph lub interfejsów API, które deweloperzy stworzyli. Składa się z usługi uwierzytelniania, bibliotek open source, rejestracji aplikacji i konfiguracji (za pośrednictwem portalu dla deweloperów i interfejsu API aplikacji), pełnej dokumentacji dewelopera, przykładów szybkiego startu, przykładów kodu, samouczków, poradniki i innych treści programistów. Platforma tożsamości firmy Microsoft obsługuje standardowe protokoły branżowe, takie jak OAuth 2.0 i OpenID Connect.

Do tej pory większość deweloperów współpracowała z platformą Azure AD AD w wersji 1.0 w celu uwierzytelniania kont służbowych i szkolnych (aprowizowanych przez usługę Azure AD), żądając tokenów z punktu końcowego usługi Azure AD AD w wersji 1.0 przy użyciu biblioteki uwierzytelniania usługi Azure AD (ADAL), witryny Azure portal dla rejestracji i konfiguracji aplikacji oraz interfejsu API programu Microsoft Graph dla konfiguracji aplikacji programowych.

Dzięki ujednoliconej platformie tożsamości firmy Microsoft (w wersji 2.0) można raz napisać kod i uwierzytelnić dowolną tożsamość firmy Microsoft w aplikacji. W przypadku kilku platform w pełni obsługiwana biblioteka uwierzytelniania firmy Microsoft (MSAL) typu open source jest zalecana do użycia w punktach końcowych platformy tożsamości. Msal jest prosty w obsłudze, zapewnia doskonałe środowisko logowania jednokrotnego (SSO) dla użytkowników, pomaga osiągnąć wysoką niezawodność i wydajność i został opracowany przy użyciu programu Microsoft Secure Development Lifecycle (SDL). Podczas wywoływania interfejsów API, można skonfigurować aplikację, aby korzystać z przyrostowej zgody, co pozwala opóźnić żądanie zgody na bardziej inwazyjne zakresy, dopóki użycie aplikacji gwarantuje to w czasie wykonywania.  Usługa MSAL obsługuje również usługę Azure Active Directory B2C, dzięki czemu klienci korzystają z preferowanych tożsamości kont społecznościowych, korporacyjnych lub lokalnych, aby uzyskać dostęp do logowania jednokrotnego aplikacji i interfejsów API.

Dzięki platformie tożsamości firmy Microsoft rozszerzysz zasięg na tego rodzaju użytkowników:

- Konta służbowe i szkolne (konta aprowizacji usługi Azure AD)
- Konta osobiste (takie jak Outlook.com lub Hotmail.com)
- Twoi klienci, którzy przynoszą własną pocztę e-mail lub tożsamość społecznościową (na przykład LinkedIn, Facebook, Google) za pośrednictwem msal i usługi Azure AD B2C

Za pomocą portalu Azure można zarejestrować i skonfigurować aplikację oraz użyć interfejsu API programu Microsoft Graph.

Zaktualizuj aplikację we własnym tempie. Aplikacje utworzone za pomocą bibliotek ADAL są nadal obsługiwane. Obsługiwane są również mieszane portfele aplikacji, które składają się z aplikacji utworzonych za pomocą bibliotek ADAL i aplikacji utworzonych za pomocą bibliotek MSAL. Oznacza to, że aplikacje korzystające z najnowszej biblioteki ADAL i najnowszego narzędzia MSAL będą dostarczać identyfikatory SSO w całym portfolio, dostarczane przez współużytkowaną pamięć podręczną tokenów między tymi bibliotekami. Aplikacje zaktualizowane z usługi ADAL do MSAL będą utrzymywać stan logowania użytkownika po uaktualnieniu.

## <a name="microsoft-identity-platform-experience"></a>Środowisko platformy tożsamości firmy Microsoft

Na poniższym diagramie przedstawiono ogólnie środowisko obsługi tożsamości firmy Microsoft, obejmujące środowisko rejestracji aplikacji, zestawy SDK, punkty końcowe i obsługiwane tożsamości.

![Platforma tożsamości firmy Microsoft obecnie](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>Rejestracja aplikacji

Środowisko **[rejestracji aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908)** portalu Azure portal jest jednym środowisko portalu do zarządzania wszystkimi aplikacjami, które zostały zintegrowane z platformą tożsamości firmy Microsoft. Jeśli korzystasz z portalu rejestracji aplikacji, zacznij używać środowiska rejestracji aplikacji portalu Azure portal.

Aby uzyskać integrację z usługą Azure AD B2C (podczas uwierzytelniania tożsamości społecznościowych lub lokalnych), musisz zarejestrować aplikację w dzierżawie usługi Azure AD B2C. To środowisko jest również częścią witryny Azure portal.

Użyj [interfejsu API aplikacji,](https://docs.microsoft.com/graph/api/resources/application?view=graph-rest-1.0) aby programowo skonfigurować aplikacje zintegrowane z platformą tożsamości firmy Microsoft w celu uwierzytelnienia dowolnej tożsamości firmy Microsoft.

### <a name="msal-libraries"></a>Biblioteki MSAL

Biblioteka MSAL służy do tworzenia aplikacji, które uwierzytelniają wszystkie tożsamości firmy Microsoft. Biblioteki MSAL w programach .NET i JavaScript są ogólnie dostępne. Biblioteki MSAL dla systemów iOS i Android są w wersji zapoznawczej i nadają się do użycia w środowisku produkcyjnym. Zapewniamy taką samą obsługę poziomu produkcji dla bibliotek MSAL w wersji zapoznawczej, jak w przypadku wersji MSAL i ADAL, które są ogólnie dostępne.

Można również użyć bibliotek MSAL do integracji aplikacji z usługą Azure AD B2C.

Biblioteki po stronie serwera do tworzenia aplikacji sieci Web i internetowych interfejsów API są ogólnie dostępne: [ASP.NET](https://docs.microsoft.com/aspnet/overview) i [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2)

### <a name="microsoft-identity-platform-endpoint"></a>Punkt końcowy platformy tożsamości firmy Microsoft

Punkt końcowy platformy tożsamości firmy Microsoft (w wersji 2.0) jest teraz certyfikowany przez oidc. Współpracuje z bibliotekami uwierzytelniania firmy Microsoft (MSAL) lub dowolną inną biblioteką ze standardami. Wdraża zakresy czytelne dla człowieka, zgodnie ze standardami branżowymi.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat wersji 1.0 i 2.0.

* [Omówienie platformy tożsamości firmy Microsoft (w wersji 2.0)](v2-overview.md)
* [Omówienie usługi Azure Active Directory dla deweloperów (wersja 1.0)](../azuread-dev/v1-overview.md)
