---
title: Rejestrowanie aplikacji sieci Web, która wywołuje internetowe interfejsy API — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak zarejestrować aplikację internetową, która wywołuje internetowe interfejsy API
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
ms.openlocfilehash: 8cb7d86bd419563363779c499962c81f0c59e3b6
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881880"
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
