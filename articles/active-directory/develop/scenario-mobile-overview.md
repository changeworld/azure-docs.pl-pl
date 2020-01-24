---
title: Tworzenie aplikacji mobilnej, która wywołuje interfejsy API sieci Web | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć aplikację mobilną wywołującą interfejsy API sieci Web (omówienie)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0a33b5bb4fc7220a9cf66f40e9805d3fde9fccef
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702049"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scenariusz: aplikacja mobilna, która wywołuje interfejsy API sieci Web

Dowiedz się, jak utworzyć aplikację mobilną, która wywołuje interfejsy API sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Wprowadzenie

Utwórz pierwszą aplikację mobilną i wypróbuj Przewodnik Szybki Start.

> [!div class="nextstepaction"]
> [Szybki Start: uzyskiwanie tokenu i wywoływanie Microsoft Graph interfejsu API z poziomu aplikacji systemu Android](./quickstart-v2-android.md)
>
> [Szybki Start: uzyskiwanie tokenu i wywoływanie Microsoft Graph interfejsu API z poziomu aplikacji systemu iOS](./quickstart-v2-ios.md)
>
> [Szybki Start: uzyskiwanie tokenu i wywoływanie Microsoft Graph interfejsu API z aplikacji platformy Xamarin dla systemu iOS &](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Przegląd

Spersonalizowane, bezproblemowe środowisko użytkownika jest niezbędne dla aplikacji mobilnych.  Platforma tożsamości firmy Microsoft umożliwia deweloperom mobilnym tworzenie tego środowiska dla użytkowników systemów iOS i Android. Twoja aplikacja może logować się Azure Active Directory użytkowników (usługi Azure AD), użytkowników prywatnych konto Microsoft i Azure AD B2C użytkowników oraz uzyskiwać tokeny do wywoływania interfejsu API sieci Web w ich imieniu. Aby zaimplementować te przepływy, będziemy używać biblioteki Microsoft Authentication Library (MSAL), która implementuje [przepływ kodu autoryzacji standardu OAuth 2.0](v2-oauth2-auth-code-flow.md).

![Aplikacje demona](./media/scenarios/mobile-app.svg)

Zagadnienia dotyczące aplikacji mobilnych:

- **Środowisko użytkownika jest kluczem**: zezwól użytkownikom na wyświetlanie wartości aplikacji przed pytaniem o logowanie i Żądaj tylko wymaganych uprawnień.
- **Obsługa wszystkich konfiguracji użytkowników**: wielu użytkowników pracujących na urządzeniach przenośnych jest objętych zasadami dostępu warunkowego i zasad zgodności urządzeń. Upewnij się, że te kluczowe scenariusze są obsługiwane.
- Zaimplementuj Logowanie jednokrotne **(SSO)** : MSAL i Microsoft Identity platform umożliwiają łatwe logowanie jednokrotne za pomocą przeglądarki urządzenia lub Microsoft Authenticator (i Intune — portal firmy w systemie Android).

## <a name="specifics"></a>Szczegółowych informacji

Należy pamiętać o następujących kwestiach podczas tworzenia aplikacji mobilnej na platformie tożsamości firmy Microsoft:

- W zależności od platformy niektóre Interakcje użytkownika mogą być wymagane podczas pierwszego logowania użytkowników. Na przykład system iOS wymaga, aby aplikacje pokazywały Interakcje użytkownika podczas pierwszego korzystania z logowania jednokrotnego za pośrednictwem Microsoft Authenticator (i Intune — Portal firmy w systemie Android).
- W systemach iOS i Android MSAL może używać zewnętrznej przeglądarki (która może pojawić się na wierzchu aplikacji) do logowania użytkowników. Możesz dostosować konfigurację tak, aby korzystała z widoków WebView w aplikacji.
- Nigdy nie używaj wpisu tajnego w aplikacji mobilnej. Będzie ona dostępna dla wszystkich użytkowników.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](scenario-mobile-app-registration.md)
