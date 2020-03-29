---
title: Protected Web API — omówienie
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć chroniony internetowy interfejs API (omówienie).
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
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: abe4ecf77e7a65251830ff822b15f79291471202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773334"
---
# <a name="scenario-protected-web-api"></a>Scenariusz: Chroniony internetowy interfejs API

W tym scenariuszu dowiesz się, jak udostępnić internetowy interfejs API. Dowiesz się również, jak chronić internetowy interfejs API, aby dostęp do niego tylko uwierzytelnieni użytkownicy mogli uzyskać do niego dostęp.

Aby korzystać z internetowego interfejsu API, należy włączyć uwierzytelnionych użytkowników z kontami służbowymi i szkolnymi lub włączyć konta osobiste firmy Microsoft.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Specyfiki

Oto konkretne informacje, które musisz znać, aby chronić internetowe interfejsy API:

- Rejestracja aplikacji musi ujawnić co najmniej jeden zakres. Wersja tokenu zaakceptowana przez internetowy interfejs API zależy od grupy odbiorców logowania.
- Konfiguracja kodu dla internetowego interfejsu API musi sprawdzić poprawność tokenu używanego podczas wywoływania internetowego interfejsu API.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](scenario-protected-web-api-app-registration.md)
