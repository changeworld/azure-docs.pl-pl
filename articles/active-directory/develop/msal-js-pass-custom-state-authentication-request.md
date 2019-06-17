---
title: Przekazywanie stanów niestandardowych w żądaniach uwierzytelniania (Biblioteka Microsoft Authentication Library dla języka JavaScript) | Azure
description: Dowiedz się, jak przekazać żądanie uwierzytelnienia za pomocą Biblioteka Microsoft Authentication Library dla języka JavaScript (MSAL.js) wartość parametru stanów niestandardowych.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f29d84838ddb11ac359d7a04dbce8e39dd05ac01
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66420501"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Przekazywanie stanów niestandardowych w żądaniach uwierzytelniania przy użyciu MSAL.js
*Stanu* parametru, zgodnie z definicją protokołu OAuth 2.0 jest dołączana do żądań uwierzytelnienia i jest także zwracany w odpowiedzi tokenu, aby zapobiec atakom fałszowanie żądań między witrynami. Domyślnie biblioteka Microsoft Authentication Library dla języka JavaScript (MSAL.js) przekazuje losowo generowany unikatowy *stanu* wartość parametru w żądaniach uwierzytelniania.

Parametr state można również kodowania informacji stanu aplikacji przed przekierowania. Można przekazać stanu użytkownika w aplikacji, takich jak strony lub widok, w jakim były, jako dane wejściowe dla tego parametru. Biblioteka MSAL.js umożliwia przekazywanie Twojego stanów niestandardowych jako parametr stanu w `Request` obiektu:

```javascript
// Request type
export type AuthenticationParameters = {
    scopes?: Array<string>;
    extraScopesToConsent?: Array<string>;
    prompt?: string;
    extraQueryParameters?: QPDict;
    claimsRequest?: string;
    authority?: string;
    state?: string;
    correlationId?: string;
    account?: Account;
    sid?: string;
    loginHint?: string;
};
```

Na przykład:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: “page_url”
}

myMSALObj.loginPopup(loginRequest);
```

Przekazana stanu jest dołączany do Unikatowy identyfikator GUID, ustaw przez MSAL.js podczas wysyłania żądania. Gdy zostanie zwrócona odpowiedź, MSAL.js wyszukuje zgodny stan, a następnie zwraca niestandardowe przekazywane w stanie `Response` obiektu jako `accountState`.

```javascript
export type AuthResponse = {
    uniqueId: string;
    tenantId: string;
    tokenType: string;
    idToken: IdToken;
    accessToken: string;
    scopes: Array<string>;
    expiresOn: Date;
    account: Account;
    accountState: string;
};
```

Aby dowiedzieć się więcej, przeczytaj temat [Kompilowanie aplikacji jednostronicowej (SPA)](scenario-spa-overview.md) przy użyciu MSAL.js.