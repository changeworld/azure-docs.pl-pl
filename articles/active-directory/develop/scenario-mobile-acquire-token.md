---
title: Aplikacja mobilna wywołuje interfejsy API — w uzyskanie tokenu dla aplikacji sieci web | Platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację mobilną, która wywołuje internetowych interfejsów API (uzyskanie tokenu dla aplikacji)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88c9215ed221e24099eeb219a4db599a1955920a
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550344"
---
# <a name="mobile-app-that-calls-web-apis---get-a-token"></a>Aplikacja mobilna, która wywołuje interfejsy API — w sieci web uzyskać token

Przed rozpoczęciem chronionego podczas wywoływania interfejsów API, aplikacji sieci web należy tokenu dostępu. W tym artykule przedstawiono proces pobierania tokenu przy użyciu Microsoft Authentication Library (MSAL).

## <a name="scopes-to-request"></a>Zakres żądania

W przypadku żądania tokenu, należy zdefiniować zakres. Zakres Określa, jakie dane mogą uzyskiwać dostęp do aplikacji.  

Jest to najłatwiejsza metoda, połączyć żądaną internetowego interfejsu API `App ID URI` z zakresem `.default`. Ten sposób nakazuje platformie tożsamości firmy Microsoft, wymaganych przez aplikację, że wszystkie zakresy są ustawione w portalu.

#### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

#### <a name="ios"></a>iOS
```swift
let scopes: [String] = ["https://graph.microsoft.com/.default"]
```

#### <a name="xamarin"></a>Xamarin
```CSharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Uzyskiwanie tokenów

### <a name="via-msal"></a>Via MSAL

Biblioteka MSAL umożliwia aplikacjom uzyskiwanie tokenów dyskretnie interaktywnie. Po prostu wywoływanie tych metod i Biblioteka MSAL zwraca token dostępu dla żądanego zakresów. Prawidłowy wzorzec jest wykonywania żądaniem dyskretnej i wrócić do interaktywnego żądania.

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});    

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed request.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

```swift
// Initialize the app.
guard let authorityURL = URL(string: kAuthority) else {
    self.loggingText.text = "Unable to create authority URL"
    return
}
let authority = try MSALAADAuthority(url: authorityURL)
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)

// Get tokens.
let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
applicationContext.acquireTokenSilent(with: parameters) { (result, error) in
    if let error = error {
        let nsError = error as NSError

        // interactionRequired means you need to ask the user to sign in. This usually happens
        // when the user's refresh token is expired or when the user has changed the password,
        // among other possible reasons.
        if (nsError.domain == MSALErrorDomain) {
            if (nsError.code == MSALError.interactionRequired.rawValue) {    
                DispatchQueue.main.async {
                    guard let applicationContext = self.applicationContext else { return }
                    let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
                    applicationContext.acquireToken(with: parameters) { (result, error) in
                        if let error = error {
                            self.updateLogging(text: "Could not acquire token: \(error)")
                            return
                        }

                        guard let result = result else {
                            self.updateLogging(text: "Could not acquire token: No result returned")
                            return
                        }
                        
                        // Token is ready via interaction!
                        self.accessToken = result.accessToken
                    }
                }
                return
            }
        }

        self.updateLogging(text: "Could not acquire token silently: \(error)")
        return
    }
    guard let result = result else {
        self.updateLogging(text: "Could not acquire token: No result returned")
        return
    }

    // Token is ready via silent acquisition.
    self.accessToken = result.accessToken
}
```

#### <a name="xamarin"></a>Xamarin

```CSharp
string[] scopes = new string["https://graph.microsoft.com/.default"];
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException e)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="via-the-protocol"></a>Za pośrednictwem protokołu

Nie zaleca się bezpośrednio przy użyciu protokołu. Jeśli to zrobisz, aplikacja nie obsługuje niektórych logowania jednokrotnego (SSO), zarządzanie urządzeniami i scenariuszy dostępu warunkowego.

Gdy używasz protokołu uzyskiwanie tokenów dla aplikacji mobilnych, należy wprowadzić dwa żądania: uzyskanie kodu autoryzacji i jego wymiany dla tokenu.

#### <a name="get-authorization-code"></a>Uzyskaj kod autoryzacji

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-token"></a>Uzyskiwanie tokenu dostępu i odświeżanie

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wywoływanie interfejsu web API](scenario-mobile-call-api.md)
