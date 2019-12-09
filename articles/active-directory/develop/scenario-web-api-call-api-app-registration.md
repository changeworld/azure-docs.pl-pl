---
title: Rejestrowanie internetowego interfejsu API, który wywołuje interfejsy API sieci Web — Microsoft Identity platform | Azure
description: Dowiedz się, jak utworzyć internetowy interfejs API, który wywołuje podrzędne interfejsy API sieci Web (Rejestracja aplikacji)
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
ms.openlocfilehash: cb5f4763e13935b99564bfcb6d8b6e7f463ed59e
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919804"
---
# <a name="web-api-that-calls-web-apis---app-registration"></a>Interfejs API sieci Web, który wywołuje interfejsy API sieci Web — Rejestracja aplikacji

Internetowy interfejs API, który wywołuje podrzędne interfejsy API sieci Web, ma taką samą rejestrację jak chroniony internetowy interfejs API. W związku z tym musisz postępować zgodnie z instrukcjami w temacie [Protected Web API — Rejestracja aplikacji](scenario-protected-web-api-app-registration.md).

Ponieważ jednak aplikacja sieci Web wywołuje teraz interfejsy API sieci Web, jest to poufna aplikacja kliencka. Dlatego są wymagane dodatkowe informacje rejestracyjne: aplikacja musi udostępniać wpisy tajne (poświadczenia klienta) za pomocą platformy tożsamości firmy Microsoft.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Uprawnienia do interfejsu API

Aplikacje sieci Web wywołują interfejsy API w imieniu użytkownika, dla którego otrzymano token okaziciela. Muszą oni zażądać uprawnień delegowanych. Aby uzyskać szczegółowe informacje, zobacz [Dodawanie uprawnień dostępu do interfejsów API sieci Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfiguracja kodu aplikacji](scenario-web-api-call-api-app-configuration.md)
