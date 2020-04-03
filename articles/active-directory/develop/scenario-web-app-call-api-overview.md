---
title: Tworzenie aplikacji sieci Web, która wywołuje internetowe interfejsy API — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację sieci Web, która wywołuje internetowe interfejsy API (omówienie)
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
ms.openlocfilehash: 14f513306b3f0bc0c06a4143e5174c3ecddaef62
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617161"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>Scenariusz: aplikacja internetowa, która wywołuje internetowe interfejsy API

Dowiedz się, jak utworzyć aplikację sieci web, która loguje użytkowników do platformy tożsamości firmy Microsoft, a następnie wywołuje internetowe interfejsy API w imieniu zalogowanego użytkownika.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

W tym scenariuszu przyjęto założenie, że przeszedłeś już następujący scenariusz:

> [!div class="nextstepaction"]
> [Aplikacja internetowa z możliwością logowania użytkowników](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Omówienie

Możesz dodać uwierzytelnianie do aplikacji sieci web, aby mogła zalogować się do użytkownika i wywołać internetowy interfejs API w imieniu zalogowanego użytkownika.

![Aplikacja internetowa wywołująca internetowe interfejsy API](./media/scenario-webapp/web-app.svg)

Aplikacje sieci Web, które wywołują internetowe interfejsy API są poufne aplikacje klienckie.
Dlatego rejestrują klucz tajny (hasło lub certyfikat aplikacji) w usłudze Azure Active Directory (Azure AD). Ten klucz tajny jest przekazywany podczas wywołania usługi Azure AD, aby uzyskać token.

## <a name="specifics"></a>Specyfiki

> [!NOTE]
> Dodawanie logowania do aplikacji sieci web polega na ochronie samej aplikacji sieci web. Tę ochronę uzyskuje się przy użyciu bibliotek *oprogramowania pośredniczącego,* a nie biblioteki uwierzytelniania firmy Microsoft (MSAL). W poprzednim [scenariuszu, aplikacja sieci Web, która loguje się w użytkowników,](scenario-web-app-sign-user-overview.md)omówione tego tematu.
>
> W tym scenariuszu opisano sposób wywoływania interfejsów API sieci web z aplikacji sieci web. Musisz uzyskać tokeny dostępu dla tych interfejsów API sieci web. Biblioteki MSAL służy do uzyskania tych tokenów.

Program w tym scenariuszu obejmuje te konkretne zadania:

- Podczas [rejestracji aplikacji](scenario-web-app-call-api-app-registration.md)należy podać identyfikator URI odpowiedzi, klucz tajny lub certyfikat, który ma zostać udostępniony usłudze Azure AD. Jeśli wdrożysz aplikację w kilku lokalizacjach, podasz te informacje dla każdej lokalizacji.
- [Konfiguracja aplikacji](scenario-web-app-call-api-app-configuration.md) musi zawierać poświadczenia klienta, które zostały udostępnione za pomocą usługi Azure AD podczas rejestracji aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Aplikacja internetowa, która wywołuje internetowe interfejsy API: rejestracja aplikacji](scenario-web-app-call-api-app-registration.md)
