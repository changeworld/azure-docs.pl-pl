---
title: Tworzenie jednostronicowej aplikacji wywołującej internetowy interfejs API — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację jednostronicową, która wywołuje interfejs API sieci Web
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: ccece6c840033913ec6d96b446dcb98c4befb32f
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160002"
---
# <a name="single-page-application-call-a-web-api"></a>Aplikacja jednostronicowa: wywoływanie interfejsu API sieci Web

Zalecamy wywołanie metody `acquireTokenSilent`, aby uzyskać lub odnowić token dostępu przed wywołaniem internetowego interfejsu API. Po uzyskaniu tokenu można wywołać chroniony internetowy interfejs API.

## <a name="call-a-web-api"></a>Wywoływanie interfejsu API sieci Web

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Użyj tokenu uzyskanego dostępu jako okaziciela w żądaniu HTTP w celu wywołania dowolnego internetowego interfejsu API, takiego jak Microsoft Graph API. Na przykład:

```javascript
    var headers = new Headers();
    var bearer = "Bearer " + access_token;
    headers.append("Authorization", bearer);
    var options = {
         method: "GET",
         headers: headers
    };
    var graphEndpoint = "https://graph.microsoft.com/v1.0/me";

    fetch(graphEndpoint, options)
        .then(function (response) {
             //do something with response
        }
```

# <a name="angulartabangular"></a>[Angular](#tab/angular)

Otoka kątowa MSAL wykorzystuje Interceptor HTTP, aby automatycznie uzyskiwać tokeny dostępu w trybie dyskretnym i dołączać je do żądań HTTP do interfejsów API. Aby uzyskać więcej informacji, zobacz [pozyskiwanie tokenu do wywoływania interfejsu API](scenario-spa-acquire-token.md).

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przenieś do środowiska produkcyjnego](scenario-spa-production.md)
