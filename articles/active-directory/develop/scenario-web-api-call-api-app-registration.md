---
title: Internetowy interfejs API tego wywołania podrzędne interfejsów API sieci web (rejestracji aplikacji) — Platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć internetowy interfejs API tego wywołania podrzędne interfejsów API sieci web (rejestracji aplikacji)
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
ms.openlocfilehash: fb03869cdea2150b6e922e2d6d81e577c3be02da
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075388"
---
# <a name="web-api-that-calls-web-apis---app-registration"></a>Czy interfejsy API — rejestrowanie aplikacji sieci web wywołań interfejsu API sieci Web

Podrzędne interfejsów API sieci web wywołuje internetowy interfejs API ma tę samą rejestrację jako chroniony internetowy interfejs API. W związku z tym, musisz wykonaj instrukcje z sekcji [chroniony internetowy interfejs API — rejestrowanie aplikacji](scenario-protected-web-api-app-registration.md).

Ponieważ aplikacja sieci web teraz połączeń internetowych interfejsów API, staje się aplikacja poufne klienta. Dlatego ma informacje o dodatkowych rejestracji, które są wymagane: aplikacja musi udostępnić klucze tajne (poświadczenia klienta) z platformą Microsoft identity.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Uprawnienia do interfejsu API

Aplikacje sieci Web wywoływania interfejsów API w imieniu użytkownika, dla którego został odebrany token elementu nośnego. Muszą zażądać uprawnień delegowanych. Aby uzyskać więcej informacji, zobacz [Dodawanie uprawnień dostępu do interfejsów API sieci web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Konfiguracja kodu aplikacji](scenario-web-api-call-api-app-configuration.md)
