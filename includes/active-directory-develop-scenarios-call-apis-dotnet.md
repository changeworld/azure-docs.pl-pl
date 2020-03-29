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
ms.openlocfilehash: 3d4e45d1bf53bab4d1f9c45367f9d051f1668e2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76309036"
---
### <a name="authenticationresult-properties-in-msalnet"></a>Właściwości AuthenticationResult w MSAL.NET

Metody pozyskiwania tokenów `AuthenticationResult`zwracają . W przypadku metod `Task<AuthenticationResult>` asynchronizacyjnych zwraca.

W `AuthenticationResult` MSAL.NET, ujawnia:

- `AccessToken`dla internetowego interfejsu API, aby uzyskać dostęp do zasobów. Ten parametr jest ciągiem, zwykle JWT zakodowanym w bazie podstawowej 64. Klient nigdy nie powinien zaglądać do tokenu dostępu. Format nie jest gwarantowane, aby pozostać stabilny i może być zaszyfrowany dla zasobu. Pisanie kodu, który zależy od zawartości tokenu dostępu na kliencie jest jednym z największych źródeł błędów i podziałów logiki klienta. Aby uzyskać więcej informacji, zobacz [Tokeny programu Access](../articles/active-directory/develop/access-tokens.md).
- `IdToken`dla użytkownika. Ten parametr jest zakodowanym JWT. Aby uzyskać więcej informacji, zobacz [tokeny identyfikatorów](../articles/active-directory/develop/id-tokens.md).
- `ExpiresOn`informuje o dacie i godzinie wygaśnięcia tokenu.
- `TenantId`zawiera dzierżawę, w której znaleziono użytkownika. Dla użytkowników-gości w scenariuszach B2B usługi Azure Active Directory (Azure AD) identyfikator dzierżawy jest dzierżawą gościa, a nie unikatową dzierżawą.
Gdy token jest dostarczany dla `AuthenticationResult` użytkownika, zawiera również informacje o tym użytkowniku. W przypadku poufnych przepływów klienta, w których tokeny są wymagane bez użytkownika dla aplikacji, te informacje o użytkowniku mają wartość null.
- Dla `Scopes` którego token został wystawiony.
- Unikatowy identyfikator użytkownika.

### <a name="iaccount"></a>Konto IAccount

MSAL.NET definiuje pojęcie konta za pośrednictwem `IAccount` interfejsu. Ta zmiana podziału zapewnia prawo semantyki. Ten sam użytkownik może mieć kilka kont w różnych katalogach usługi Azure AD. Ponadto MSAL.NET zapewnia lepsze informacje w przypadku scenariuszy gościa, ponieważ informacje o koncie domowym są dostarczane.
Na poniższym diagramie `IAccount` przedstawiono strukturę interfejsu.

![Struktura interfejsu IAccount](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

Klasa `AccountId` identyfikuje konto w określonej dzierżawie z właściwościami pokazanymi w poniższej tabeli.

| Właściwość | Opis |
|----------|-------------|
| `TenantId` | Reprezentacja ciągu dla identyfikatora GUID, który jest identyfikatorem dzierżawy, w którym znajduje się konto. |
| `ObjectId` | Reprezentacja ciągu dla identyfikatora GUID, który jest identyfikatorem użytkownika, który jest właścicielem konta w dzierżawie. |
| `Identifier` | Unikatowy identyfikator konta. `Identifier`jest łączeniem `ObjectId` przecinka i `TenantId` oddzielaniem ich przecinkami. Nie są one zakodowane w bazie 64. |

Interfejs `IAccount` reprezentuje informacje o jednym koncie. Ten sam użytkownik może być obecny w różnych dzierżaw, co oznacza, że użytkownik może mieć wiele kont. Jego członkowie są pokazane w poniższej tabeli.

| Właściwość | Opis |
|----------|-------------|
| `Username` | Ciąg zawierający wyświetlaną wartość w formacie UserPrincipalName (UPN) na przykład john.doe@contoso.com. Ten ciąg może mieć wartość null, w przeciwieństwie do HomeAccountId i HomeAccountId.Identifier, które nie będą zerowe. Ta właściwość `DisplayableId` zastępuje `IUser` właściwość w poprzednich wersjach MSAL.NET. |
| `Environment` | Ciąg, który zawiera dostawcę tożsamości dla `login.microsoftonline.com`tego konta, na przykład . Ta właściwość `IdentityProvider` zastępuje `IUser`właściwość `IdentityProvider` , z tą różnicą, że również miał informacje o dzierżawie, oprócz środowiska chmury. W tym miejscu wartość jest tylko hosta. |
| `HomeAccountId` | Identyfikator konta użytkownika. Ta właściwość jednoznacznie identyfikuje użytkownika w dzierżawach usługi Azure AD. |

### <a name="use-the-token-to-call-a-protected-api"></a>Użyj tokenu, aby wywołać chroniony interfejs API

Po `AuthenticationResult` są zwracane `result`przez MSAL w , dodać go do nagłówka autoryzacji HTTP przed dokonaniem połączenia, aby uzyskać dostęp do chronionego interfejsu API sieci web.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```