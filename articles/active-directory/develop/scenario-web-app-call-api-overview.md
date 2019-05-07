---
title: Aplikacja internetowa wywołuje interfejsy API (omówienie) — Platforma tożsamości usługi Microsoft sieci web
description: Dowiedz się, jak utworzyć aplikację sieci web wywołuje interfejsy API (omówienie) sieci web
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce45f11a697b72ebdd0fe01166a70e7b47aa8e9f
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076303"
---
# <a name="scenario-web-app-that-calls-web-apis"></a>Scenariusz: Aplikacja internetowa wywołuje interfejsy API sieci web

Dowiedz się, jak utworzyć aplikację sieci web użytkownikom logowanie się na platformie tożsamości firmy Microsoft i która wywołuje interfejsy API sieci web w imieniu zalogowanego użytkownika.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

W tym scenariuszu supposes już przeszli następujący scenariusz:

> [!div class="nextstepaction"]
> [Aplikacja sieci Web przez użytkowników logowania](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Omówienie

Możesz dodać uwierzytelnianie do aplikacji sieci Web, w związku z tym można logowania użytkowników, która wywołuje internetowy interfejs API w imieniu zalogowanego użytkownika.

![Aplikacja internetowa wywołuje interfejsy API sieci web](./media/scenario-webapp/web-app.svg)

Aplikacje sieci Web, która wywołuje interfejsy API sieci web:

- są poufne klienta aplikacji.
- Dlatego zarejestrowanego wpis tajny (hasło aplikacji lub certyfikat) z usługą Azure AD. Ten klucz tajny jest przekazywany w podczas wywołania do usługi Azure AD w celu pobrania tokenu

## <a name="specifics"></a>Szczegółowe informacje

> [!NOTE]
> Dodawanie logowania do aplikacji sieci Web nie używa biblioteki MSAL jest o ochronie aplikacji sieci Web. Ochrona biblioteki odbywa się przez biblioteki o nazwie oprogramowania pośredniczącego. Jest to obiekt w poprzednim scenariuszu [logowania użytkowników do aplikacji sieci Web](scenario-web-app-sign-user-overview.md)
>
> Podczas wywoływania interfejsów API sieci web z aplikacji sieci Web, konieczne będzie uzyskiwanie tokenów dostępu do tych interfejsów API sieci web. Za pomocą biblioteki MSAL do uzyskania tych tokenów.

W związku z tym, środowisko typu end to end deweloperów, w tym scenariuszu ma konkretnych aspektów jako:

- Podczas [Rejestracja aplikacji](scenario-web-app-call-api-app-registration.md), należy podać jedną, kilka (Jeśli aplikacja jest wdrażana na kilku lokalizacjach) Odpowiedz identyfikatory URI, wpisów tajnych oraz ich lub certyfikaty muszą być udostępniane w usłudze Azure AD.
- [Konfiguracji aplikacji](scenario-web-app-call-api-app-configuration.md) wymaga podania poświadczeń klienta jako udostępniony z usługą Azure AD podczas rejestracji aplikacji

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](scenario-web-app-call-api-app-registration.md)
