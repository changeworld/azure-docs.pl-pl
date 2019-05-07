---
title: Aplikacja jednostronicowa (wywołania internetowego interfejsu API) — Platforma tożsamości firmy Microsoft
description: Informacje o sposobie tworzenia aplikacji jednostronicowych (wywołania internetowego interfejsu API)
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01f141a5374c0e794b264f6e0135ca3e15ff8359
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074848"
---
# <a name="single-page-application---call-a-web-api"></a>Aplikacja jednostronicowa - wywołania w interfejsie web API

Firma Microsoft zaleca wywołanie `acquireTokenSilent` metodę, aby uzyskać lub odnowić token dostępu przed wywołaniem interfejsu API sieci web. Po utworzeniu tokenu, można wywoływać chroniony internetowy interfejs API.

## <a name="call-a-web-api"></a>Wywoływanie interfejsu API sieci Web

### <a name="javascript"></a>JavaScript

Wywoływanie wszelkie internetowego interfejsu API, np. interfejsu API Microsoft Graph przy użyciu tokenu uzyskano dostęp jako okaziciela w żądaniu HTTP. Na przykład:

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

Jak wspomniano w [pobieranie sekcji tokeny](scenario-spa-acquire-token.md), korzysta z biblioteki MSAL Angular otoki interceptor HTTP, aby automatycznie uzyskania tokenów dostępu w trybie dyskretnym i dołącz je do żądania HTTP do interfejsów API.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przenoszenie do środowiska produkcyjnego](scenario-spa-production.md)
