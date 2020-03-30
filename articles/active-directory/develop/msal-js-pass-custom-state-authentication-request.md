---
title: Przekazywanie stanu niestandardowego w żądaniach uwierzytelniania (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak przekazać niestandardową wartość parametru stanu w żądaniu uwierzytelniania przy użyciu biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/16/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1c05956f83ad3a6491627be8916fac2c8be2b7ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084934"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Przekazywanie stanu niestandardowego w żądaniach uwierzytelniania przy użyciu pliku MSAL.js

Parametr *state,* zgodnie z definicją OAuth 2.0, jest uwzględniony w żądaniu uwierzytelniania i jest również zwracany w odpowiedzi tokenu, aby zapobiec atakom fałszerstwa żądań między lokacjami. Domyślnie biblioteka uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL.js) przekazuje losowo wygenerowaną unikatową wartość parametru *stanu* w żądaniach uwierzytelniania.

Parametr stanu może również służyć do kodowania informacji o stanie aplikacji przed przekierowaniem. Stan użytkownika w aplikacji, na przykład strona lub widok, na której się znajdował, można przekazać jako dane wejściowe do tego parametru. Biblioteka MSAL.js umożliwia przekazywanie stanu niestandardowego `Request` jako parametru stanu w obiekcie:

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
    forceRefresh?: boolean;
};
```

> [!Note]
> Jeśli chcesz pominąć token w pamięci podręcznej i przejść do `forceRefresh` serwera, przekaż w wartości logicznej do AuthenticationParameters obiektu używanego do żądania logowania/tokenu.
> `forceRefresh`nie powinny być używane domyślnie, ze względu na wpływ na wydajność aplikacji.
> Poleganie na pamięci podręcznej zapewni użytkownikom lepsze środowisko.
> Pomijanie pamięci podręcznej powinny być używane tylko w scenariuszach, w których wiesz, że aktualnie buforowane dane nie mają aktualnych informacji.
> Takie jak narzędzie administrator, który dodaje role do użytkownika, który musi uzyskać nowy token ze zaktualizowanymi rolami.

Przykład:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

Przekazany w stanie jest dołączany do unikatowego identyfikatora GUID ustawionego przez msal.js podczas wysyłania żądania. Po zwróceniu odpowiedzi msal.js sprawdza dopasowanie stanu, a następnie zwraca `Response` niestandardowe przekazywane w stanie w obiekcie jako `accountState`.

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

Aby dowiedzieć się więcej, przeczytaj o [tworzeniu aplikacji jednostronicowej (SPA)](scenario-spa-overview.md) przy użyciu pliku MSAL.js.