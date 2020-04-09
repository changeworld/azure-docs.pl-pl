---
title: Tworzenie aplikacji jednostronicowej wywołującej internetowy interfejs API — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć jednostronicową aplikację, która wywołuje internetowy interfejs API
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: 5b70b109f43e80fc3ec68f52aef2dba6823033bb
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882135"
---
# <a name="single-page-application-call-a-web-api"></a>Aplikacja jednostronicowa: wywoływanie internetowego interfejsu API

Zaleca się wywołanie `acquireTokenSilent` metody nabycia lub odnowienia tokenu dostępu przed wywołaniem interfejsu API sieci web. Po tokenie można wywołać chroniony interfejs API sieci web.

## <a name="call-a-web-api"></a>Wywoływanie interfejsu API sieci Web

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Użyj tokenu nabytego dostępu jako nośnika w żądaniu HTTP, aby wywołać dowolny internetowy interfejs API, taki jak microsoft graph API. Przykład:

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

# <a name="angular"></a>[Angular](#tab/angular)

Otoka kątowa MSAL korzysta z interceptora HTTP, aby automatycznie pozyskiwać tokeny dostępu w trybie dyskretnym i dołączać je do żądań HTTP do interfejsów API. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie tokenu do wywoływania interfejsu API](scenario-spa-acquire-token.md).

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przenoszenie do środowiska produkcyjnego](scenario-spa-production.md)
