---
title: Aplikacja mobilna, która wywołuje internetowych interfejsów API — Przegląd | Platforma tożsamości firmy Microsoft
description: Informacje o sposobie tworzenia aplikacji mobilnych, połączeń internetowych interfejsów API (omówienie)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb0acd1534bab11eb57a7aa0e695f192b5999ed2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076498"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scenariusz: Wywołuje interfejsy API sieci web aplikacji mobilnej

Dowiedz się, wszystko, czego potrzebujesz do tworzenia aplikacji mobilnej, która wywołuje interfejsy API sieci web.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Wprowadzenie

Utwórz swoją pierwszą aplikację mobilną i wypróbuj Szybki Start!

> [!div class="nextstepaction"]
> [Szybki start: Uzyskiwanie tokenu i wywołania interfejsu API Microsoft Graph w aplikacji systemu Android](./quickstart-v2-android.md)
>
> [Szybki start: Uzyskiwanie tokenu i wywołania interfejsu API Microsoft Graph w aplikacji systemu iOS](./quickstart-v2-ios.md)
>
> [Szybki start: Uzyskiwanie tokenu i wywołania interfejsu API Microsoft Graph z platformy Xamarin iOS & aplikacji dla systemu Android](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Omówienie

Podczas kompilowania aplikacji mobilnej, spersonalizowane, bezproblemowe użytkownika końcowego jest niezbędne.  Platforma tożsamości firmy Microsoft umożliwia deweloperów aplikacji mobilnych, w tym celu dokładnie dla systemów iOS i Android użytkowników. Zarejestrować aplikację w usłudze Azure AD, osobiste konto Microsoft i użytkowników usługi Azure AD B2C i uzyskać tokeny do wywołania internetowego interfejsu API w ich imieniu. Aby zaimplementować te przepływy, użyjemy Microsoft Authentication Library (MSAL), który implementuje będące standardami branżowymi [przepływ kodu autoryzacji OAuth2.0](v2-oauth2-auth-code-flow.md).

![Aplikacje demona](./media/scenarios/mobile-app.svg)

Zagadnienia dotyczące aplikacji mobilnej:

- ***Środowisko użytkownika jest klucz***: Użytkownicy mogą zobaczyć wartość aplikacji przed monitem logowania i żądać tylko wymagane uprawnienia.
- ***Obsługuje wszystkie konfiguracje użytkownika***: Wielu użytkowników biznesowych przenośnych znajdują się pod dostępu warunkowego i zasad zgodności urządzeń. Pamiętaj obsługiwać te kluczowych scenariuszy.
- ***Implementowanie logowania jednokrotnego (SSO)***: Biblioteka MSAL i platformą Microsoft identity się włączanie logowania jednokrotnego łatwo kupić za pośrednictwem przeglądarki na urządzeniu lub Microsoft Authenticator (i Portal firmy usługi Intune w systemie Android).

## <a name="specifics"></a>Szczegółowe informacje

Podczas kompilowania aplikacji mobilnej na platformie tożsamości firmy Microsoft, środowisko end-to-end ma kilka istotnych kwestii:

- W zależności od platformy logowanie bez interakcji może nie być możliwe podczas pierwszego logowania. systemy iOS, na przykład wymaga aplikacji, aby pokazać interakcji użytkownika podczas pobierania czas pierwszego logowania jednokrotnego przy użyciu Microsoft Authenticator (i Portal firmy usługi Intune w systemie Android).
- W systemach iOS i Android MSAL może używać zewnętrznej przeglądarki, (które mogą być wyświetlane na podstawie aplikacji) w celu logowania użytkowników. To można dostosować, aby zamiast tego użyj elementów Webview w aplikacji.
- Nigdy nie używać hasła w aplikacji mobilnej, będzie on dostępny dla wszystkich użytkowników.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](scenario-mobile-app-registration.md)
