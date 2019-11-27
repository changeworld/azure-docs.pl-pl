---
title: Zakresy dla aplikacji w wersji 1.0 (Biblioteka uwierzytelniania firmy Microsoft) | Azure
description: Dowiedz się więcej o zakresach dla aplikacji w wersji 1.0 przy użyciu biblioteki uwierzytelniania firmy Microsoft (MSAL).
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
ms.date: 11/25/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70a8a5859c7f1e2353b53d01a25a0ca39e0b04dd
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74532977"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Zakresy dla internetowego interfejsu API akceptujące tokeny v 1.0

Uprawnienia OAuth2 to zakresy uprawnień, Azure Active Directory które aplikacja sieci Web (zasób) dla deweloperów () (Azure AD) dla programistów (w wersji 1.0) jest uwidaczniana aplikacjom klienckim. Te zakresy uprawnień mogą być udzielane aplikacjom klienckim podczas wyrażania zgody. Zapoznaj się z sekcją `oauth2Permissions` w [Kompendium manifestu aplikacji Azure Active Directory](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Zakresy żądania dostępu do określonych uprawnień OAuth2 aplikacji v 1.0

Aby uzyskać tokeny dla określonych zakresów aplikacji w wersji 1.0 (na przykład Graf usługi Azure AD, czyli https:\//graph.windows.net), należy utworzyć zakresy przez połączenie żądanego identyfikatora zasobu z żądanym uprawnieniem OAuth2 dla tego zasobu.

Na przykład w celu uzyskania dostępu w imieniu użytkownika do interfejsu API sieci Web w wersji 1.0, w którym jest `ResourceId`identyfikator URI aplikacji:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Aby odczytywać i zapisywać dane z usługi MSAL.NET Azure AD za pomocą usługi Azure AD interfejs API programu Graph (https:\//graph.windows.net/), należy utworzyć listę zakresów, jak pokazano w następujących przykładach:

```csharp
string ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.windows.net/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Aby napisać zakres odpowiadający interfejsowi API Azure Resource Manager (https:\//management.core.windows.net/), musisz zażądać następującego zakresu (należy zauważyć dwa ukośniki):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Należy użyć dwóch ukośników, ponieważ Azure Resource Manager interfejs API oczekuje ukośnika w swoim zasobie (AUD), a następnie jest ukośnik, aby oddzielić nazwę interfejsu API od zakresu.

Logika używana przez usługę Azure AD jest następująca:

- Dla punktu końcowego biblioteki ADAL (Azure AD v 1.0) z tokenem dostępu w wersji 1.0 (możliwe), AUD = zasób
- Punkt końcowy usługi MSAL (Microsoft Identity platform (v 2.0)) z prośbą o token dostępu dla zasobu akceptującego tokeny v 2.0, `aud=resource.AppId`
- W przypadku usługi MSAL (punkt końcowy v 2.0) z prośbą o token dostępu dla zasobu, który akceptuje token dostępu w wersji 1.0 (w przypadku powyższego przypadku), usługa Azure AD analizuje żądanych odbiorców od żądanego zakresu, pobierając wszystko przed ostatnim ukośnikiem i używając go jako identyfikatora zasobu. W związku z tym, jeśli https:\//database.windows.net oczekuje odbiorców "https:\//database.windows.net/", należy zażądać zakresu "https:\//database.windows.net//.default". Zobacz również problem z usługą GitHub [#747: zostanie pominięty końcowy ukośnik adresu URL zasobu, co spowodowało błąd uwierzytelniania SQL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Zakresy żądania dostępu do wszystkich uprawnień aplikacji w wersji 1.0

Jeśli chcesz uzyskać token dla wszystkich zakresów statycznych aplikacji v 1.0, Dołącz wartość ". default" do identyfikatora URI aplikacji interfejsu API:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>Zakresy do żądania dla aplikacji przepływu/demona poświadczeń klienta

W przypadku przepływu poświadczeń klienta należy również `/.default`zakres do przekazania. Ta informacja dotyczy usługi Azure AD: "wszystkie uprawnienia na poziomie aplikacji, które administrator wyraził zgodę na rejestrację aplikacji.
