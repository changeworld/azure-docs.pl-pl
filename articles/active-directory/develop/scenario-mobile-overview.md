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
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: df5b53ccdcd33e2542834769baff5b759ff7b170
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962365"
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

## <a name="overview"></a>Przegląd

Środowisko spersonalizowane bezproblemowy interfejs użytkownika ma zasadnicze znaczenie dla aplikacji mobilnych.  Platforma tożsamości firmy Microsoft umożliwia deweloperów aplikacji mobilnych utworzyć środowiska dla systemów iOS i Android użytkowników. Aplikacji do logowania użytkowników usługi Azure Active Directory (Azure AD), osobiste użytkowników kont Microsoft i użytkowników usługi Azure AD B2C i uzyskać tokeny do wywołania internetowego interfejsu API w ich imieniu. Aby zaimplementować te przepływy, użyjemy Microsoft Authentication Library (MSAL), która implementuje będące standardami branżowymi [przepływ kodu autoryzacji OAuth2.0](v2-oauth2-auth-code-flow.md).

![Aplikacje demona](./media/scenarios/mobile-app.svg)

Zagadnienia dotyczące aplikacji mobilnych:

- **Środowisko użytkownika jest klucz**: Użytkownicy mogą zobaczyć wartość aplikacji przed monitem logowania i zażądać wymaganych uprawnień.
- **Obsługuje wszystkie konfiguracje użytkownika**: Wielu użytkowników biznesowych przenośnych znajdują się pod dostępu warunkowego i zasad zgodności urządzeń. Pamiętaj obsługiwać te kluczowych scenariuszy.
- **Implementowanie logowania jednokrotnego (SSO)**: Biblioteka MSAL i platformą Microsoft identity się włączanie logowania jednokrotnego łatwo kupić za pośrednictwem przeglądarki na urządzeniu lub Microsoft Authenticator (i Portal firmy usługi Intune w systemie Android).

## <a name="specifics"></a>Szczegółowe informacje

Podczas kompilowania aplikacji mobilnej na platformie tożsamości firmy Microsoft, należy pamiętać o tych zagadnień:

- W zależności od platformy interakcji z użytkownikiem może być wymagane, gdy użytkownicy logują się po raz pierwszy. Na przykład dla systemu iOS wymaga od aplikacji, aby wyświetlać interakcji z użytkownikiem, gdy przy użyciu logowania jednokrotnego po raz pierwszy przy użyciu Microsoft Authenticator (i Portal firmy usługi Intune w systemie Android).
- W systemach iOS i Android MSAL użyć zewnętrznej przeglądarce (która może pojawić się na podstawie aplikacji) do logowania użytkowników. Można dostosować konfigurację, aby zamiast tego użyj elementów Webview w aplikacji.
- Nigdy nie używać hasła w aplikacji mobilnej. Będzie ona dostępna dla wszystkich użytkowników.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](scenario-mobile-app-registration.md)
