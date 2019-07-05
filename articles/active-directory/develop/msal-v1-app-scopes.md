---
title: Zakresy dla aplikacji w wersji 1.0 (Microsoft Authentication Library) | Azure
description: Dowiedz się więcej na temat zakresów dla aplikacji w wersji 1.0 przy użyciu Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e43bc245a5908ba1bf91e7b4bee6df2f5cfc618
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514369"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Zakresy interfejsu API sieci Web akceptuje tokenów w wersji 1.0

Uprawnienia OAuth2 są zakresy uprawnień, które usługa Azure AD dla aplikacji interfejsu API (zasobu) w sieci web deweloperów (1.0) uwidacznia aplikacjom klienckim. Te zakresy uprawnień, może przyznawać aplikacjom klienckim podczas wyrażania zgody. Zobacz sekcję `oauth2Permissions` w [odwołanie do manifestu aplikacji usługi Azure Active Directory](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Zakresy, aby zawnioskować o dostęp do określonych uprawnień OAuth2 aplikacji w wersji 1.0
Jeśli chcesz uzyskać tokeny dla określonych zakresów aplikacji w wersji 1.0 (na przykład wykres usługi Azure AD, która jest typu https:\//graph.windows.net), należy utworzyć zakresy przez złączenie identyfikatorem żądanego zasobu z uprawnieniem żądaną OAuth2 dla tego zasobu.

Na przykład, aby dostępu w imieniu użytkownika v1.0 internetowego interfejsu API w przypadku aplikacji, identyfikator URI `ResourceId`:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Jeśli chcesz odczytywać i zapisywać przy użyciu platformy MSAL.NET usługi Azure Active Directory przy użyciu interfejsu API programu graph usługi Azure AD (https:\//graph.windows.net/), należy utworzyć listę zakresów, co przedstawiono poniżej:

```csharp
string ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.windows.net/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Jeśli chcesz zapisać zakres odpowiadający interfejsu API usługi Azure Resource Manager (https:\//management.core.windows.net/), musisz zażądać następującym zakresem (Uwaga dwa ukośniki):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Należy użyć dwa ukośniki, ponieważ interfejs API usługi Azure Resource Manager oczekuje ukośnika w jego oświadczenia odbiorców (aud), a następnie jest ukośnikiem, aby oddzielić nazwę interfejsu API z zakresu.

Przez logikę używaną przez usługę Azure AD jest następująca:

- Dla punktu końcowego biblioteki ADAL (1.0), przy użyciu tokenu dostępu w wersji 1.0 (tylko to możliwe), aud = zasobów
- Dla biblioteki MSAL (punkt końcowy platformy (w wersji 2.0) programu Microsoft identity) pytaniem tokenu dostępu dla zasobu, akceptując tokenów w wersji 2.0, aud = zasobów. Identyfikator aplikacji
- Biblioteka MSAL (punktu końcowego v2.0) pytaniem tokenu dostępu dla zasobu akceptowania tokenu dostępu w wersji 1.0 (czyli tak powyżej) usługi Azure AD zostanie przetworzony przez pobranie wszystkiego wcześniej niż ostatni ukośnika i używać go jako identyfikator zasobu odbiorców w żądanym zakresie. W związku z tym jeśli https:\//database.windows.net oczekuje odbiorcami "https:\//database.windows.net/", należy do zakresu żądania "https:\//database.windows.net//.default". Zobacz też GitHub problem [#747: Ukośnika url zasobu jest pominięty, który spowodował niepowodzenie uwierzytelniania sql](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Zakresy, aby zawnioskować o dostęp do uprawnień aplikacji w wersji 1.0
Jeśli chcesz uzyskać token dla wszystkich zakresów statycznej aplikacji w wersji 1.0, Dołącz ".default", aby identyfikator URI interfejsu API:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-client-credential-flow--daemon-app"></a>Zakresy, aby poprosić o dodanie do klienta poświadczeń przepływu / aplikacji demona
W przypadku przepływ poświadczeń klienta, również będą zakres, aby przekazać `/.default`. Oznacza to, do usługi Azure AD: "wszystkie poziomie aplikacji uprawnienia, które administrator wyraził zgody na rejestracji aplikacji.
