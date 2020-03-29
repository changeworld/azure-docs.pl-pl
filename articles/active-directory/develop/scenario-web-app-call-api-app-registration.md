---
title: Rejestrowanie aplikacji sieci Web, która wywołuje internetowe interfejsy API — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak zarejestrować aplikację internetową, która wywołuje internetowe interfejsy API
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
ms.openlocfilehash: 5a57fcef3569734964bf6e8a41faa49800798f9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759061"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>Aplikacja internetowa, która wywołuje internetowe interfejsy API: rejestracja aplikacji

Aplikacja internetowa, która wywołuje interfejsy API sieci Web ma taką samą rejestrację jak aplikacja sieci web, która loguje użytkowników. Postępuj zgodnie z instrukcjami w [aplikacji internetowej, która loguje się do użytkowników: Rejestracja aplikacji](scenario-web-app-sign-user-app-registration.md).

Jednak ponieważ aplikacja sieci web teraz również wywołuje interfejsy API sieci web, staje się poufne aplikacji klienckiej. Dlatego wymagana jest dodatkowa rejestracja. Aplikacja musi udostępniać poświadczenia klienta lub *wpisy tajne*z platformą tożsamości firmy Microsoft.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Uprawnienia aplikacji

Aplikacje sieci Web wywołują interfejsy API w imieniu zalogowanego użytkownika. Aby to zrobić, muszą zażądać *delegowanych uprawnień*. Aby uzyskać szczegółowe informacje, zobacz [Dodawanie uprawnień dostępu do internetowych interfejsów API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Aplikacja internetowa, która wywołuje internetowe interfejsy API: konfiguracja kodu](scenario-web-app-call-api-app-configuration.md)
