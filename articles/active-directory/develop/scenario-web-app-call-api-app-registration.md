---
title: Aplikacja internetowa wywołuje interfejsy API (Rejestrowanie aplikacji) — sieci web platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację sieci web wywołuje interfejsy API (Rejestrowanie aplikacji) sieci web
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
ms.openlocfilehash: 5becdc287f7cad28aa6c7c07ebc107586e9a2b2a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075193"
---
# <a name="web-app-that-calls-web-apis---app-registration"></a>Aplikacja sieci Web, że wywołania sieci web interfejsy API — rejestrowanie aplikacji

Web app wywoływania interfejsów API sieci web ma ten sam rejestracji jako aplikację sieci Web logowania użytkowników. W związku z tym musisz wykonaj instrukcje z sekcji [aplikacja sieci Web przez logowania użytkowników — rejestrowanie aplikacji](scenario-web-app-sign-user-app-registration.md)

Jednak ponieważ aplikacja sieci Web teraz połączeń internetowych interfejsów API, staje się aplikacji poufne klienta. Dlatego jest nieco dodatkowej wymagana rejestracja: należy ją udostępnić klucze tajne (poświadczenia klienta) z platformą Microsoft identity.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Uprawnienia do interfejsu API

Aplikacje sieci Web wywoływania interfejsów API w imieniu zalogowanego użytkownika. Muszą zażądać uprawnień delegowanych. Aby uzyskać szczegółowe informacje, zobacz [Dodawanie uprawnień dostępu do interfejsów API sieci web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Konfiguracja kodu aplikacji](scenario-web-app-call-api-app-configuration.md)
