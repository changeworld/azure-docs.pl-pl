---
title: Ewolucja platformą Microsoft identity - Azure
description: Zapoznaj się z platformą Microsoft identity, unowocześnienia funkcji usługi Azure Active Directory (Azure AD) identity usługi i dla deweloperów platformy.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/03/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ead81dfec5c98e810abfe4b88accb9aa9092fc90
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66472717"
---
# <a name="evolution-of-microsoft-identity-platform"></a>Ewolucja platformy tożsamości firmy Microsoft

Platforma tożsamości firmy Microsoft jest ewolucją platformy deweloperskiej Azure Active Directory (Azure AD). Dzięki niej deweloperzy mogą tworzyć aplikacje, które logowania użytkowników, uzyskiwanie tokenów do wywoływania interfejsów API, takich jak program Microsoft Graph lub interfejsów API, które programiści. Składa się z usługą uwierzytelniania, bibliotek typu open source, rejestrowanie aplikacji i konfiguracji (za pośrednictwem portalu dla deweloperów i aplikacji interfejsu API), dokumentację dla deweloperów pełną, quickstart — przykłady, przykłady kodu, samouczki i przewodniki z instrukcjami i innej zawartości dla deweloperów. Platforma tożsamości firmy Microsoft obsługuje standardowe protokoły branżowe, takie jak OAuth 2.0 i OpenID Connect.

Aż do chwili Większość programistów mający doświadczenie z platformy w wersji 1.0 usługi Azure AD do uwierzytelniania pracy i służbowego konta (zaprowizowane przez usługę Azure AD), żądając tokenów z punktu końcowego usługi Azure AD w wersji 1.0, za pomocą usługi Azure AD Authentication Library (ADAL) witrynie Azure portal. Rejestrowanie aplikacji i konfiguracji i interfejsu API programu Graph usługi Azure AD, dla konfiguracji aplikacji programowych.

Dzięki platformie tożsamości firmy Microsoft (w wersji 2.0) należy zwiększyć zasięg tego rodzaju użytkowników:

- Oraz do kont służbowych (konta usługi Azure AD aprowizacji)
- Konta osobiste (takich jak Outlook.com lub Hotmail.com)
- Twoje klientów, którzy przynoszą własne wiadomości e-mail lub tożsamości społecznościowych (takich jak LinkedIn, Facebook, Google) za pośrednictwem oferty usługi Azure AD B2C

Ujednolicona platforma tożsamości firmy Microsoft możesz napisać kod raz i uwierzytelniania tożsamości firmy Microsoft w swojej aplikacji. Dla różnych platform jest w pełni obsługiwana biblioteki typu open source o nazwie Microsoft Authentication Library (MSAL). Biblioteka MSAL jest łatwa w użyciu, zapewnia doskonałą logowania jednokrotnego (SSO) środowiska dla użytkowników, ułatwia osiągnięcie wysokiej niezawodności i wydajności oraz przy użyciu Microsoft Secure Development Lifecycle (SDL). Podczas wywoływania interfejsów API, można skonfigurować aplikację, aby korzystać z zalet przyrostowe zgody, co pozwala na opóźnienia żądania o zgodę na więcej zakresów inwazyjne do momentu użycia aplikacji gwarantuje to w czasie wykonywania.

Możesz użyć witryny Azure portal do rejestracji i konfiguracji aplikacji i użyć interfejsu API programu Microsoft Graph w konfiguracji aplikacji programowych.

Aktualizowanie aplikacji w swoim własnym tempie. Aplikacje utworzone przy użyciu biblioteki ADAL w dalszym ciągu być obsługiwane. Mieszane aplikacji portfolio, składające się z aplikacji utworzonych za pomocą biblioteki ADAL i aplikacji utworzonych za pomocą biblioteki MSAL są również obsługiwane. Oznacza to, aplikacji przy użyciu najnowszej biblioteki ADAL oraz najnowsze biblioteki MSAL będzie dostarczać logowania jednokrotnego w portfolio, dostarczone przez udostępnionej pamięci podręcznej tokenu od tych bibliotek. Aplikacje zaktualizowany z biblioteki ADAL do biblioteki MSAL zachowają logowania stanu użytkownika podczas uaktualniania.

## <a name="microsoft-identity-platform-experience"></a>Środowisko platformy tożsamości firmy Microsoft

Na poniższym diagramie przedstawiono ogólnie środowisko obsługi tożsamości firmy Microsoft, obejmujące środowisko rejestracji aplikacji, zestawy SDK, punkty końcowe i obsługiwane tożsamości.

![Platforma tożsamości firmy Microsoft obecnie](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>Proces rejestracji aplikacji

Witryna Azure portal **[rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908)** środowisko jest w jednym środowisku portalu do zarządzania wszystkich aplikacji, które zostały zintegrowane z platformą Microsoft identity. Jeśli masz doświadczenie z portalu rejestracji aplikacji, uruchamianie, zamiast proces rejestracji aplikacji portalu platformy Azure.

Integracja z usługą Azure AD B2C (podczas uwierzytelniania tożsamości społecznościowych lub lokalne) należy zarejestrować aplikację w dzierżawie B2C. To środowisko jest również częścią witryny Azure portal.

**Aplikacji interfejsu API w programie Microsoft Graph** jest obecnie dostępna w wersji zapoznawczej. Ten interfejs API umożliwia programowe Konfigurowanie aplikacji zintegrowanych z platformą tożsamości Microsoft do uwierzytelniania tożsamości firmy Microsoft. Jednak dopóki ten interfejs API staje się ogólnie dostępna, należy użyć interfejsu API usługi Azure AD Graph w wersji 1.6 i manifest aplikacji.

### <a name="msal-libraries"></a>Biblioteki MSAL

Biblioteka MSAL służy do tworzenia aplikacji, które przeprowadzają uwierzytelnianie wszystkich tożsamości firmy Microsoft. Biblioteki MSAL na platformie .NET są ogólnie dostępne. Biblioteki MSAL dla języka JavaScript, iOS i Android są w wersji zapoznawczej i jest odpowiedni do użytku w środowisku produkcyjnym. Firma Microsoft zapewnia ten sam poziom obsługi produkcji dla biblioteki MSAL w wersji zapoznawczej, tak jak w przypadku wersji biblioteki MSAL i biblioteki ADAL, które są ogólnie dostępne.

Biblioteki MSAL umożliwia również zintegrować aplikację z usługi Azure AD B2C.

Po stronie serwera biblioteki do tworzenia interfejsów API sieci web i aplikacji sieci web są ogólnie dostępne: [ASP.NET](https://docs.microsoft.com/aspnet/overview) i [platformy ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2)

### <a name="microsoft-identity-platform-endpoint"></a>Punkt końcowy platforma tożsamości firmy Microsoft

Punkt końcowy platformy (w wersji 2.0) tożsamości firmy Microsoft jest teraz certyfikowane OIDC. Działa z bibliotek uwierzytelniania firmy Microsoft (MSAL) lub inne biblioteki zgodnych ze standardami. Implementuje ludzi można odczytać zakresów, zgodnie ze standardami branżowymi.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat wersji 1.0 i 2.0.

* [Przegląd usługi Microsoft identity platformy (w wersji 2.0)](v2-overview.md)
* [Usługa Azure Active Directory dla deweloperów (1.0) — omówienie](v1-overview.md)
