---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 0196d39f5b131bc54e00412beb7fdf10b7352336
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121856"
---
### <a name="authenticationresult-properties-in-msalnet"></a>Właściwości AuthenticationResult platformy MSAL.NET

Metody uzyskiwania tokenów zwracają `AuthenticationResult` (lub do metod asynchronicznych `Task<AuthenticationResult>`.

W przypadku platformy MSAL.NET `AuthenticationResult` przedstawia:

- `AccessToken` interfejsu API sieci Web dostęp do zasobów. Ten parametr jest ciągiem, zwykle JWT zakodowany w formacie base64, ale klient nigdy nie powinien wyglądać w tokenie dostępu. Format nie jest gwarantowana pozostaną niezmienione i mogą być szyfrowane dla zasobu. Osoby pisanie kodu w zależności od zawartości tokenu dostępu na komputerze klienckim jest jednym z największych źródeł błędów i podziałów logiki klienta. Zobacz też [tokeny dostępu](../articles/active-directory/develop/access-tokens.md)
- `IdToken` dla użytkownika (ten parametr jest zakodowany token JWT). Zobacz [tokeny Identyfikatora](../articles/active-directory/develop/id-tokens.md)
- `ExpiresOn` informuje Data/godzina wygaśnięcia tokenu
- `TenantId` zawiera dzierżawy, w którym użytkownik został znaleziony. Dla użytkowników-gości (scenariusze B2B usługi Azure AD) identyfikator dzierżawy jest dzierżawy gościa, a nie unikatowy dzierżawy.
Gdy token jest dostarczany dla użytkownika, `AuthenticationResult` zawiera także informacje o tym użytkownika. Poufne klienta przepływów, w których tokeny są żądane bez użytkownika (w przypadku aplikacji) te informacje użytkownika ma wartość null.
- `Scopes` Dla został wystawiony token.
- Unikatowy identyfikator dla użytkownika.

### <a name="iaccount"></a>IAccount

Platformy MSAL.NET definiuje pojęcie konta (za pośrednictwem `IAccount` interfejsu). Tej istotnej zmiany zapewnia semantykę, prawy: fakt, że ten sam użytkownik może mieć kilka kont na innej platformie Azure AD katalogów. Platformy MSAL.NET także lepsze informacje w przypadku scenariuszy gościa, ponieważ informacje o koncie głównego.
Na poniższym diagramie przedstawiono strukturę `IAccount` interfejsu:

![image](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

`AccountId` Klasy zidentyfikuje konto w określonej dzierżawie. Ma następujące właściwości:

| Właściwość | Opis |
|----------|-------------|
| `TenantId` | Reprezentacja ciągu identyfikatora GUID jest identyfikator dzierżawy, w którym znajduje się konto. |
| `ObjectId` | Reprezentacja ciągu identyfikatora GUID jest identyfikator użytkownika będącego właścicielem konta w dzierżawie. |
| `Identifier` | Unikatowy identyfikator dla konta. `Identifier` jest łączenie `ObjectId` i `TenantId` oddzielone przecinkami i czy nie zakodowane w formacie base64. |

`IAccount` Interfejs reprezentuje informacje dotyczące jednego konta. Ten sam użytkownik może znajdować się w różnych dzierżawach, oznacza to, że użytkownik może mieć wiele kont. Jego członkowie są:

| Właściwość | Opis |
|----------|-------------|
| `Username` | Ciąg zawierający zawiera wartość w formacie UserPrincipalName (UPN), na przykład john.doe@contoso.com. Ten ciąg może być null, HomeAccountId i HomeAccountId.Identifier nie będzie mieć wartość null. Ta właściwość zastępuje `DisplayableId` właściwość `IUser` w poprzednich wersjach platformy MSAL.NET. |
| `Environment` | Ciąg zawierający dostawcy tożsamości dla tego konta, na przykład `login.microsoftonline.com`. Ta właściwość zastępuje `IdentityProvider` właściwość `IUser`, chyba że `IdentityProvider` również istniała informacji o dzierżawie (oprócz w środowisku chmury), w tym miejscu wartość jest tylko hosta. |
| `HomeAccountId` | AccountId głównego konta dla użytkownika. Ta właściwość jednoznacznie identyfikuje użytkownika dla dzierżaw usługi Azure AD. |

### <a name="using-the-token-to-call-a-protected-api"></a>Przy użyciu tokenu do wywoływania chronionego interfejsu API

Gdy `AuthenticationResult` został zwrócony przez biblioteki MSAL (w `result`), należy dodać go do nagłówka autoryzacji HTTP, przed wykonaniem wywołania do dostępu do chronionego internetowego interfejsu API.

```CSharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```