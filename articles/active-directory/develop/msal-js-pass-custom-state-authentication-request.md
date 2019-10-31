---
title: Przekaż stan niestandardowy w żądaniach uwierzytelniania (Biblioteka uwierzytelniania firmy Microsoft dla języka JavaScript)
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak przekazać niestandardową wartość parametru stanu w żądaniu uwierzytelniania przy użyciu biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL. js).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 527c1937da8634f7448c82ca8c0331fb5cfb85e3
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150604"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Przekazywanie stanu niestandardowego w żądaniach uwierzytelniania przy użyciu MSAL. js
Parametr *State* , zdefiniowany przez OAuth 2,0, jest uwzględniony w żądaniu uwierzytelniania i jest również zwracany w odpowiedzi tokenu, aby zapobiec atakom na fałszerstwo żądań między lokacjami. Domyślnie Biblioteka uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL. js) przekazuje losowo wygenerowane unikatowe wartości parametru *stanu* w żądaniach uwierzytelniania.

Parametru stanu można także użyć do kodowania informacji o stanie aplikacji przed przekierowaniem. Można przekazać stan użytkownika w aplikacji, na przykład stronę lub widok, jako dane wejściowe tego parametru. Biblioteka MSAL. js umożliwia przekazanie stanu niestandardowego jako parametru stanu w obiekcie `Request`:

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
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

Przesłany stan jest dołączany do unikatowego identyfikatora GUID ustawionego przez MSAL. js podczas wysyłania żądania. Gdy odpowiedź zostanie zwrócona, MSAL. js sprawdza zgodność stanu, a następnie zwraca niestandardową przekazana stan w obiekcie `Response` jako `accountState`.

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

Aby dowiedzieć się więcej, Przeczytaj o [kompilowaniu aplikacji jednostronicowej (Spa)](scenario-spa-overview.md) przy użyciu MSAL. js.