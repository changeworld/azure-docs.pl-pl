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
ms.openlocfilehash: 4e01dbb0036761215a9a05c464b20ead340a2e3d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423765"
---
### <a name="authenticationresult-properties-in-msalnet"></a>Właściwości AuthenticationResult w MSAL.NET

Metody uzyskiwania tokenów zwracają `AuthenticationResult` (lub dla metod asynchronicznych, `Task<AuthenticationResult>`.

W MSAL.NET `AuthenticationResult` uwidacznia:

- `AccessToken`, aby uzyskać dostęp do zasobów za pomocą interfejsu API sieci Web. Ten parametr jest ciągiem, zazwyczaj szyfrowanym przy użyciu algorytmu Base64, ale nie powinien być nigdy widoczny w tokenie dostępu. Format nie jest gwarantowany, aby pozostały stabilny i można go było zaszyfrować dla zasobu. Osoby piszące kod w zależności od zawartości tokenu dostępu na kliencie są jednym z największych źródeł błędów i podziałów logiki klienta. Zobacz też [tokeny dostępu](../articles/active-directory/develop/access-tokens.md)
- `IdToken` dla użytkownika (ten parametr jest zakodowaną JWT). Zobacz [tokeny identyfikatorów](../articles/active-directory/develop/id-tokens.md)
- `ExpiresOn` informuje datę i godzinę wygaśnięcia tokenu
- `TenantId` zawiera dzierżawcę, w którym znaleziono użytkownika. Dla użytkowników-Gości (scenariusze B2B usługi Azure AD) identyfikator dzierżawy jest dzierżawcą gościa, a nie z unikatowym dzierżawcą.
Po dostarczeniu tokenu dla użytkownika `AuthenticationResult` zawiera również informacje o tym użytkowniku. W przypadku poufnych przepływów klientów, w których tokeny są żądane bez użytkownika (dla aplikacji), informacje o użytkowniku mają wartość null.
- `Scopes`, dla którego został wystawiony token.
- Unikatowy identyfikator użytkownika.

### <a name="iaccount"></a>IAccount

MSAL.NET definiuje pojęcie konta (za pomocą interfejsu `IAccount`). Ta przerwana zmiana zapewnia właściwą semantykę: fakt, że ten sam użytkownik może mieć kilka kont w różnych katalogach usługi Azure AD. Ponadto MSAL.NET zapewnia lepsze informacje w przypadku scenariuszy gościa, ponieważ są udostępniane informacje o koncie głównym.
Na poniższym diagramie przedstawiono strukturę interfejsu `IAccount`:

![image](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

Klasa `AccountId` identyfikuje konto w określonej dzierżawie. Ma następujące właściwości:

| Właściwość | Opis |
|----------|-------------|
| `TenantId` | Ciąg reprezentujący identyfikator GUID, który jest IDENTYFIKATORem dzierżawy, w której znajduje się konto. |
| `ObjectId` | Ciąg reprezentujący identyfikator GUID, który jest IDENTYFIKATORem użytkownika, który jest właścicielem konta w dzierżawie. |
| `Identifier` | Unikatowy identyfikator konta. `Identifier` jest połączeniem `ObjectId` i `TenantId` rozdzielonych przecinkami i nie są zakodowane w formacie base64. |

Interfejs `IAccount` reprezentuje informacje o pojedynczym koncie. Ten sam użytkownik może być obecny w różnych dzierżawach, co oznacza, że użytkownik może mieć wiele kont. Jego członkowie to:

| Właściwość | Opis |
|----------|-------------|
| `Username` | Ciąg zawierający wartości, które można wyświetlić w formacie UserPrincipalName (UPN), na przykład john.doe@contoso.com. Ten ciąg może mieć wartość null, podczas gdy wartości HomeAccountId i HomeAccountId. Identifier nie będą mieć wartości null. Ta właściwość zastępuje właściwość `DisplayableId` `IUser` we wcześniejszych wersjach MSAL.NET. |
| `Environment` | Ciąg zawierający dostawcę tożsamości dla tego konta, na przykład `login.microsoftonline.com`. Ta właściwość zastępuje `IdentityProvider` Właściwość `IUser`, z tą różnicą, że `IdentityProvider` również zawierały informacje o dzierżawie (oprócz środowiska chmury), a w tym miejscu wartość jest tylko hostem. |
| `HomeAccountId` | Identyfikator AccountId dla użytkownika. Ta właściwość jednoznacznie identyfikuje użytkownika w dzierżawach usługi Azure AD. |

### <a name="using-the-token-to-call-a-protected-api"></a>Używanie tokenu do wywoływania chronionego interfejsu API

Gdy `AuthenticationResult` został zwrócony przez MSAL (w `result`), należy dodać go do nagłówka autoryzacji HTTP przed wywołaniem dostępu do chronionego internetowego interfejsu API.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```