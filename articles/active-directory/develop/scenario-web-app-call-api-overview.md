---
title: Tworzenie aplikacji sieci Web, która wywołuje interfejsy API sieci Web — Microsoft Identity platform | Azure
description: Dowiedz się, jak utworzyć aplikację sieci Web, która wywołuje interfejsy API sieci Web (omówienie)
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
ms.openlocfilehash: 9e123195205bb0eb88f0edd4e2dff2e0da9d84ce
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701658"
---
# <a name="scenario-web-app-that-calls-web-apis"></a>Scenariusz: aplikacja sieci Web, która wywołuje interfejsy API sieci Web

Dowiedz się, jak utworzyć aplikację internetową, która loguje użytkowników na platformie tożsamości firmy Microsoft, a następnie wywołuje interfejsy API sieci Web w imieniu zalogowanego użytkownika.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

W tym scenariuszu Przypuśćmy, że przeszedł następujący scenariusz:

> [!div class="nextstepaction"]
> [Aplikacja internetowa, która loguje użytkowników](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Przegląd

Należy dodać uwierzytelnianie do aplikacji sieci Web, co może spowodować zalogowanie użytkowników i wywołanie internetowego interfejsu API w imieniu zalogowanego użytkownika.

![Aplikacja internetowa wywołująca internetowe interfejsy API](./media/scenario-webapp/web-app.svg)

Web Apps, które wywołują interfejsy API sieci Web:

- są poufnymi aplikacjami klienckimi.
- Dlatego zarejestrowali wpis tajny (hasło aplikacji lub certyfikat) w usłudze Azure AD. Ten wpis tajny jest przesyłany w trakcie wywołania usługi Azure AD w celu uzyskania tokenu

## <a name="specifics"></a>Szczegółowych informacji

> [!NOTE]
> Dodawanie logowania do aplikacji sieci Web nie korzysta z bibliotek MSAL w taki sposób, aby chronić aplikację sieci Web. Ochrona bibliotek jest realizowana przez biblioteki o nazwie oprogramowanie pośredniczące. Był to obiekt w poprzednim scenariuszu użytkowników logujących się [do aplikacji sieci Web](scenario-web-app-sign-user-overview.md)
>
> Podczas wywoływania interfejsów API sieci Web z aplikacji sieci Web należy uzyskać tokeny dostępu dla tych interfejsów API sieci Web. Aby uzyskać te tokeny, można użyć bibliotek MSAL.

Kompleksowe środowisko deweloperów w tym scenariuszu ma następujące aspekty:

- Podczas [rejestracji aplikacji](scenario-web-app-call-api-app-registration.md)należy podać jedną lub kilka (Jeśli aplikacja jest wdrażana w kilku lokalizacjach), identyfikatory URI odpowiedzi, wpisy tajne lub certyfikaty muszą być udostępniane w usłudze Azure AD.
- [Konfiguracja aplikacji](scenario-web-app-call-api-app-configuration.md) wymaga podania poświadczeń klienta jako współużytkowanych z usługą Azure AD podczas rejestracji aplikacji

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](scenario-web-app-call-api-app-registration.md)
