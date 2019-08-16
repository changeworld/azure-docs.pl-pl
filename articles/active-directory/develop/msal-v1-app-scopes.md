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
ms.date: 04/23/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0590614e1c1bc7331246e76fa26a6567a05324e6
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532339"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Zakresy dla internetowego interfejsu API akceptujące tokeny v 1.0

Uprawnienia OAuth2 to zakresy uprawnień, które aplikacja sieci Web usługi Azure AD dla deweloperów (w wersji 1.0) uwidacznia dla aplikacji klienckich. Te zakresy uprawnień mogą być udzielane aplikacjom klienckim podczas wyrażania zgody. Zapoznaj się z `oauth2Permissions` sekcją w temacie Informacje dotyczące [manifestu aplikacji Azure Active Directory](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Zakresy żądania dostępu do określonych uprawnień OAuth2 aplikacji v 1.0
Jeśli chcesz uzyskać tokeny dla określonych zakresów aplikacji v 1.0 (na przykład Azure AD Graph, czyli https:\//Graph.Windows.NET), musisz utworzyć zakresy, łącząc żądany identyfikator zasobu z żądanym uprawnieniem OAuth2 dla tego zasobu.

Na przykład, aby uzyskać dostęp w imieniu użytkownika do interfejsu API sieci Web w wersji 1.0, gdzie identyfikator `ResourceId`URI aplikacji jest:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Jeśli chcesz odczytywać i zapisywać MSAL.NET Azure Active Directory przy użyciu interfejsu API programu Azure AD Graph (https:\//Graph.Windows.NET/), należy utworzyć listę zakresów w następujący sposób:

```csharp
string ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.windows.net/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Jeśli chcesz napisać zakres odpowiadający interfejsowi API Azure Resource Manager (https:\//Management.Core.Windows.NET/), musisz zażądać następującego zakresu (należy zauważyć dwa ukośniki):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Należy użyć dwóch ukośników, ponieważ Azure Resource Manager interfejs API oczekuje ukośnika w swoim zasobie (AUD), a następnie jest ukośnik, aby oddzielić nazwę interfejsu API od zakresu.

Logika używana przez usługę Azure AD jest następująca:

- Dla punktu końcowego ADAL (v 1.0) z tokenem dostępu w wersji 1.0 (możliwe), AUD = zasób
- W przypadku usługi MSAL (punkt końcowy Microsoft Identity platform (v 2.0)) żądanie tokenu dostępu dla zasobu akceptującego tokeny v 2.0, AUD = Resource. AppId
- W przypadku usługi MSAL (punkt końcowy v 2.0) z prośbą o token dostępu dla zasobu akceptującego token dostępu w wersji 1.0 (w tym przypadku powyżej) usługa Azure AD analizuje żądanych odbiorców od żądanego zakresu, pobierając wszystko przed ostatnim ukośnikiem i używając go jako identyfikatora zasobu. W związku z tym\/jeśli https:/Database.Windows.NET oczekuje odbiorców protokołu https:\//Database.Windows.NET/, należy zażądać zakresu "https:\//Database.Windows.NET//.default". Zobacz również artykuł dotyczący problemów [z usługą GitHub #747: Końcowy ukośnik adresu URL zasobu został pominięty, co spowodowało błąd](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)uwierzytelniania SQL.

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

## <a name="scopes-to-request-for-client-credential-flow--daemon-app"></a>Zakresy żądania dla aplikacji Flow/demon poświadczeń klienta
W przypadku przepływu poświadczeń klienta należy również `/.default`przekazać zakres. Ta informacja dotyczy usługi Azure AD: "wszystkie uprawnienia na poziomie aplikacji, które administrator wyraził zgodę na rejestrację aplikacji.
