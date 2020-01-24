---
title: Zarejestruj aplikację sieci Web, która wywołuje interfejsy API sieci Web — Microsoft Identity platform | Azure
description: Dowiedz się, jak zarejestrować aplikację sieci Web, która wywołuje interfejsy API sieci Web
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
ms.openlocfilehash: bcb4fa5eb5889698174b65eb576c9caf0c82f857
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701675"
---
# <a name="web-app-that-calls-web-apis---app-registration"></a>Aplikacja sieci Web, która wywołuje interfejsy API sieci Web — Rejestracja aplikacji

Aplikacje sieci Web wywołujące interfejsy API sieci Web mają taką samą rejestrację jak użytkownicy korzystający z aplikacji sieci Web. W związku z tym musisz postępować zgodnie z instrukcjami w [aplikacji sieci Web, które logują się do użytkowników — Rejestracja aplikacji](scenario-web-app-sign-user-app-registration.md)

Jednak aplikacja sieci Web wywołuje teraz interfejsy API sieci Web, a to poufna aplikacja kliencka. Dlatego istnieje niezbędna dodatkowa Rejestracja: musi ona udostępniać wpisy tajne (poświadczenia klienta) za pomocą platformy tożsamości firmy Microsoft.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Uprawnienia do interfejsu API

Aplikacje sieci Web wywołania interfejsów API w imieniu zalogowanego użytkownika. Muszą oni zażądać uprawnień delegowanych. Aby uzyskać szczegółowe informacje [, zobacz Dodawanie uprawnień dostępu do interfejsów API sieci Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfiguracja kodu aplikacji](scenario-web-app-call-api-app-configuration.md)
