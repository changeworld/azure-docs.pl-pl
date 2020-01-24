---
title: Rejestrowanie internetowego interfejsu API, który wywołuje interfejsy API sieci Web — Microsoft Identity platform | Azure
description: Dowiedz się, jak utworzyć internetowy interfejs API, który wywołuje podrzędne interfejsy API sieci Web (Rejestracja aplikacji).
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
ms.openlocfilehash: bafd71f34602535bb6193a8d8114a1182e4e8f40
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701794"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Internetowy interfejs API, który wywołuje interfejsy API sieci Web: Rejestracja aplikacji

Internetowy interfejs API, który wywołuje podrzędne interfejsy API sieci Web, ma taką samą rejestrację jak chroniony internetowy interfejs API. W związku z tym należy postępować zgodnie z instrukcjami w temacie [Protected Web API: Rejestracja aplikacji](scenario-protected-web-api-app-registration.md).

Ponieważ aplikacja sieci Web wywołuje teraz interfejsy API sieci Web, jest to poufna aplikacja kliencka. Dlatego wymagane są dodatkowe informacje o rejestracji: aplikacja musi udostępniać wpisy tajne (poświadczenia klienta) za pomocą platformy tożsamości firmy Microsoft.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Uprawnienia do interfejsu API

Usługi Web Apps wywołania interfejsów API w imieniu użytkowników, dla których odebrano token okaziciela. Aplikacje sieci Web muszą zażądać uprawnień delegowanych. Aby uzyskać więcej informacji, zobacz [Dodawanie uprawnień dostępu do interfejsów API sieci Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Internetowy interfejs API, który wywołuje interfejsy API sieci Web: Konfiguracja kodu](scenario-web-api-call-api-app-configuration.md)
