---
title: Microsoft Identity platform (v 2.0) — Omówienie — Azure
description: Dowiedz się więcej o punkcie końcowym i platformie Microsoft Identity platform (v 2.0).
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbbcd854434bcc085f1b63fb864755dd0e928fc9
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852166"
---
# <a name="microsoft-identity-platform-v20-overview"></a>Microsoft Identity platform (v 2.0) — Omówienie

Platforma tożsamości firmy Microsoft jest ewolucją platformy deweloperskiej Azure Active Directory (Azure AD). Dzięki temu deweloperzy mogą tworzyć aplikacje, które logują się do wszystkich tożsamości firmy Microsoft i uzyskują tokeny do wywoływania interfejsów API firmy Microsoft, takich jak Microsoft Graph, lub interfejsów API, które zostały skompilowane przez deweloperów. Platforma tożsamości firmy Microsoft składa się z:

- **Uwierzytelnianie OAuth 2,0 i OpenID Connect Connect Standard-zgodna z usługą** , która umożliwia deweloperom uwierzytelnianie dowolnej tożsamości firmy Microsoft, w tym:
  - Konta służbowe (obsługiwane za pomocą usługi Azure AD)
  - Osobiste konta Microsoft (np. Skype, Xbox i Outlook.com)
  - Konta społecznościowe lub lokalne (za pośrednictwem Azure AD B2C)
- **Biblioteki Open Source**: Biblioteki uwierzytelniania firmy Microsoft (MSAL) i obsługa innych bibliotek zgodnych ze standardami
- **Portal zarządzania aplikacjami**: Środowisko rejestracji i konfiguracji wbudowane w Azure Portal wraz z innymi funkcjami zarządzania platformy Azure.
- **Interfejs API konfiguracji aplikacji i program PowerShell**: umożliwiające programistyczną konfigurację aplikacji za poorednictwem interfejsu API REST (Microsoft Graph i Azure Active Directory Graph 1,6) i programu PowerShell, dzięki czemu można zautomatyzować zadania DevOps.
- **Zawartość dla deweloperów**: Dokumentacja dotycząca pojęć i referencyjnych, przykłady przewodnika Szybki Start, przykłady kodu, samouczki i przewodniki dotyczące wykonywania instrukcji.

W przypadku deweloperów platforma tożsamości firmy Microsoft oferuje bezproblemową integrację innowacji w zakresie tożsamości i miejsca zabezpieczeń, takich jak uwierzytelnianie bez hasła, uwierzytelnianie krok po kroku i dostęp warunkowy.  Nie musisz wprowadzać takich funkcji samodzielnie: Aplikacje zintegrowane z platformą tożsamości firmy Microsoft w sposób natywny wykorzystują takie innowacje.

Korzystając z platformy tożsamości firmy Microsoft, możesz napisać kod jeden raz i skontaktować się z dowolnym użytkownikiem. Aplikację można skompilować raz i korzystać z niej na wielu platformach lub utworzyć aplikację, która działa jako klient, a także aplikację zasobów (API).

## <a name="getting-started"></a>Wprowadzenie

Praca z tożsamościami nie musi być trudna. Wybierz [scenariusz](authentication-flows-app-scenarios.md) , który ma zastosowanie do Ciebie — każda ścieżka scenariusza zawiera Przewodnik Szybki Start i stronę z omówieniem, aby rozpocząć działanie w ciągu kilku minut:

- [Tworzenie aplikacji jednostronicowej](scenario-spa-overview.md)
- [Tworzenie aplikacji sieci Web, która umożliwia użytkownikom logowanie się](scenario-web-app-sign-user-overview.md)
- [Tworzenie aplikacji sieci Web, która wywołuje interfejsy API sieci Web](scenario-web-app-call-api-overview.md)
- [Tworzenie chronionego internetowego interfejsu API](scenario-protected-web-api-overview.md)
- [Tworzenie interfejsu API sieci Web, który wywołuje interfejsy API sieci Web](scenario-web-api-call-api-overview.md)
- [Tworzenie aplikacji klasycznej](scenario-desktop-overview.md)
- [Tworzenie aplikacji demona](scenario-daemon-overview.md)
- [Tworzenie aplikacji mobilnej](scenario-mobile-overview.md)

Poniższy wykres przedstawia typowe scenariusze aplikacji uwierzytelniania — Użyj go jako odniesienia podczas integracji platformy tożsamości firmy Microsoft z Twoją aplikacją.

[![Scenariusze aplikacji na platformie tożsamości firmy Microsoft](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz dowiedzieć się więcej o podstawowych pojęciach dotyczących uwierzytelniania, zalecamy rozpoczęcie pracy z następującymi tematami:

- [Przepływy uwierzytelniania i scenariusze aplikacji](authentication-flows-app-scenarios.md)
- [Informacje podstawowe o uwierzytelnianiu](authentication-scenarios.md)
- [Nazwy główne aplikacji i usługi](app-objects-and-service-principals.md)
- [Odbiorców](v2-supported-account-types.md)
- [Uprawnienia i zgoda](v2-permissions-and-consent.md)
- [Tokeny identyfikatorów](id-tokens.md) i [tokeny dostępu](access-tokens.md)

Tworzenie aplikacji bogatej w dane, która wywołuje [Microsoft Graph](https://docs.microsoft.com/graph/overview).

Gdy wszystko będzie gotowe do uruchomienia aplikacji w **środowisku produkcyjnym**, należy zapoznać się z następującymi najlepszymi rozwiązaniami:

- [Włącz rejestrowanie](msal-logging.md) w aplikacji.
- Włącz telemetrię w aplikacji.
- Włącz [serwery proxy i Dostosuj klientów http](msal-net-provide-httpclient.md).
- Przetestuj integrację, postępując zgodnie z [listą kontrolną integracji z platformą Microsoft Identity](identity-platform-integration-checklist.md).

## <a name="learn-more"></a>Dowiedz się więcej

Jeśli planujesz utworzenie aplikacji przeznaczonej dla klienta, która jest używana w tożsamościach społecznościowych i lokalnych, zobacz [omówienie Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-add-identity-providers).
