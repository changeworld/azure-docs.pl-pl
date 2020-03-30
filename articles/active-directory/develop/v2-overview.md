---
title: Omówienie platformy tożsamości firmy Microsoft (w wersji 2.0) — platforma Azure
description: Dowiedz się więcej o punkcie końcowym i platformie platformy tożsamości firmy Microsoft (w wersji 2.0).
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
ms.date: 05/08/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f6953fb5d016c802db260c55bc4970c3f029ab1a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240828"
---
# <a name="microsoft-identity-platform-v20-overview"></a>Omówienie platformy tożsamości firmy Microsoft (w wersji 2.0)

Platforma tożsamości firmy Microsoft jest ewolucją platformy deweloperskiej Azure Active Directory (Azure AD). Umożliwia deweloperom tworzenie aplikacji, które logują się we wszystkich tożsamościach firmy Microsoft i otrzymują tokeny do wywoływania interfejsów API firmy Microsoft, takich jak Microsoft Graph lub interfejsów API, które stworzyli deweloperzy. Platforma tożsamości firmy Microsoft składa się z:

- **Usługa uwierzytelniania zgodnego ze standardem OAuth 2.0 i OpenID Connect,** która umożliwia deweloperom uwierzytelnianie dowolnej tożsamości firmy Microsoft, w tym:
  - Konta służbowe (aprowizowane za pośrednictwem usługi Azure AD)
  - Osobiste konta Microsoft (takie jak Skype, Xbox i Outlook.com)
  - Konta społecznościowe lub lokalne (za pośrednictwem usługi Azure AD B2C)
- **Biblioteki open source**: Biblioteki uwierzytelniania firmy Microsoft (MSAL) i obsługa innych bibliotek zgodnych ze standardami
- **Portal zarządzania aplikacjami:** środowisko rejestracji i konfiguracji wbudowane w witrynę Azure portal, wraz ze wszystkimi innymi możliwościami zarządzania platformy Azure.
- **Interfejs API konfiguracji aplikacji i program PowerShell**: który umożliwia programową konfigurację aplikacji za pośrednictwem interfejsu API programu Microsoft Graph i programu PowerShell, dzięki czemu można zautomatyzować zadania DevOps.
- **Zawartość dewelopera:** dokumentacja koncepcyjna i referencyjna, przykłady szybkiego startu, przykłady kodu, samouczki i instrukcje.

Dla deweloperów platforma tożsamości firmy Microsoft oferuje bezproblemową integrację z innowacjami w przestrzeni tożsamości i zabezpieczeń, takimi jak uwierzytelnianie bez hasła, uwierzytelnianie step-up i dostęp warunkowy.  Nie musisz samodzielnie implementować takich funkcji: aplikacje zintegrowane z platformą tożsamości firmy Microsoft natywnie wykorzystują takie innowacje.

Za pomocą platformy tożsamości firmy Microsoft można napisać kod raz i dotrzeć do dowolnego użytkownika. Możesz utworzyć aplikację raz i mieć go działać na wielu platformach lub utworzyć aplikację, która działa jako klient, a także aplikacji zasobów (API).

## <a name="getting-started"></a>Wprowadzenie

Praca z tożsamościami nie musi być trudna. 

Obejrzyj [film wideo z platformy tożsamości firmy Microsoft,](identity-videos.md) aby poznać podstawy. 

Wybierz [scenariusz,](authentication-flows-app-scenarios.md) który dotyczy Ciebie — każda ścieżka scenariusza ma szybki start i stronę przeglądu, aby rozpocząć i uruchomić w ciągu kilku minut:

- [Tworzenie aplikacji jednostronicowej](scenario-spa-overview.md)
- [Tworzenie aplikacji internetowej z możliwością logowania użytkowników](scenario-web-app-sign-user-overview.md)
- [Tworzenie aplikacji internetowej wywołującej internetowe interfejsy API](scenario-web-app-call-api-overview.md)
- [Tworzenie chronionego internetowego interfejsu API](scenario-protected-web-api-overview.md)
- [Tworzenie internetowego interfejsu API, który wywołuje internetowe interfejsy API](scenario-web-api-call-api-overview.md)
- [Tworzenie aplikacji klasycznej](scenario-desktop-overview.md)
- [Tworzenie aplikacji demona](scenario-daemon-overview.md)
- [Tworzenie aplikacji mobilnej](scenario-mobile-overview.md)

Na poniższym wykresie przedstawiono typowe scenariusze aplikacji uwierzytelniania — użyj go jako odwołanie podczas integrowania platformy tożsamości firmy Microsoft z aplikacją.

[![Scenariusze aplikacji na platformie tożsamości firmy Microsoft](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz dowiedzieć się więcej o podstawowych pojęciach dotyczących uwierzytelniania, zalecamy rozpoczęcie od następujących tematów:

- [Przepływy uwierzytelniania i scenariusze aplikacji](authentication-flows-app-scenarios.md)
- [Podstawowe informacje o uwierzytelnianiu](authentication-scenarios.md)
- [Podmioty zleceniodawcy aplikacji i usług](app-objects-and-service-principals.md)
- [Odbiorców](v2-supported-account-types.md)
- [Uprawnienia i zgoda](v2-permissions-and-consent.md)
- [Tokeny identyfikatorów](id-tokens.md) i [tokeny dostępu](access-tokens.md)

Tworzenie aplikacji bogatej w dane, która wywołuje [program Microsoft Graph](https://docs.microsoft.com/graph/overview).

Gdy wszystko będzie gotowe do uruchomienia aplikacji w **środowisku produkcyjnym,** zapoznaj się z tymi sprawdzonymi rozwiązaniami:

- [Włącz rejestrowanie](msal-logging.md) w aplikacji.
- Włącz dane telemetryczne w aplikacji.
- Włącz [serwery proxy i dostosuj klientów HTTP](msal-net-provide-httpclient.md).
- Przetestuj integrację, postępając zgodnie z [listą kontrolną integracji platformy tożsamości firmy Microsoft.](identity-platform-integration-checklist.md)

## <a name="learn-more"></a>Dowiedz się więcej

Jeśli planujesz utworzyć aplikację skierowaną do klienta, która loguje się w tożsamościach społecznościowych i lokalnych, zobacz [omówienie usługi Azure AD B2C.](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-add-identity-providers)
