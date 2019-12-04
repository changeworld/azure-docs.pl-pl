---
title: Aplikacja jednostronicowa (wywoływanie internetowego interfejsu API) — platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację jednostronicową (wywołać interfejs API sieci Web)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4170a6642d35802581b5d1ff28eb802a6eb3482b
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766161"
---
# <a name="single-page-application-call-a-web-api"></a>Aplikacja jednostronicowa: wywoływanie interfejsu API sieci Web

Zalecamy wywołanie metody `acquireTokenSilent`, aby uzyskać lub odnowić token dostępu przed wywołaniem internetowego interfejsu API. Po uzyskaniu tokenu można wywołać chroniony internetowy interfejs API.

## <a name="call-a-web-api"></a>Wywoływanie interfejsu API sieci Web

### <a name="javascript"></a>JavaScript

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

### <a name="angular"></a>Angular

Otoka kątowa MSAL wykorzystuje Interceptor HTTP, aby automatycznie uzyskiwać tokeny dostępu w trybie dyskretnym i dołączać je do żądań HTTP do interfejsów API. Aby uzyskać więcej informacji, zobacz [pozyskiwanie tokenu do wywoływania interfejsu API](scenario-spa-acquire-token.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przenieś do środowiska produkcyjnego](scenario-spa-production.md)
