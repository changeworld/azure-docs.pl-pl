---
title: Protected Web API — omówienie
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć chroniony internetowy interfejs API (omówienie).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 9017fbde1a44bcdf39fa8730bed25141da19fd56
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882407"
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
> [Rejestracja aplikacji](scenario-protected-web-api-app-registration.md)
