---
title: Rejestrowanie internetowego interfejsu API, który wywołuje internetowe interfejsy API — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć internetowy interfejs API, który wywołuje podrzędne interfejsy API sieci Web (rejestracja aplikacji).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 048f7d41bd9d106121859e6b1fc013258067af9c
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885127"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Internetowy interfejs API, który wywołuje internetowe interfejsy API: rejestracja aplikacji

Internetowy interfejs API, który wywołuje podrzędne interfejsy API sieci web ma taką samą rejestrację jak chroniony interfejs API sieci web. W związku z tym należy postępować zgodnie z instrukcjami w [chronionym interfejsie API sieci web: Rejestracja aplikacji](scenario-protected-web-api-app-registration.md).

Ponieważ aplikacja sieci web wywołuje teraz internetowe interfejsy API, staje się poufną aplikacją kliencką. Dlatego wymagane są dodatkowe informacje rejestracyjne: aplikacja musi udostępniać wpisy tajne (poświadczenia klienta) platformie tożsamości firmy Microsoft.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Uprawnienia aplikacji

Aplikacje sieci Web wywołują interfejsy API w imieniu użytkowników, dla których odebrano token nośnika. Aplikacje sieci web muszą żądać uprawnień delegowanych. Aby uzyskać więcej informacji, zobacz [Dodawanie uprawnień dostępu do interfejsów API sieci Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Internetowy interfejs API, który wywołuje internetowe interfejsy API: konfiguracja kodu](scenario-web-api-call-api-app-configuration.md)
