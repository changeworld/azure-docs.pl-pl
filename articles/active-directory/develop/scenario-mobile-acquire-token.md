---
title: Aplikacja mobilna, która wywołuje interfejsy API sieci Web — uzyskiwanie tokenu dla aplikacji
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć aplikację mobilną, która wywołuje interfejsy API sieci Web (Uzyskiwanie tokenu dla aplikacji)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b1d6a0e4bfffaaf4ed0fa836bcf62a7f1104e6a
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803767"
---
# <a name="mobile-app-that-calls-web-apis---get-a-token"></a>Aplikacja mobilna, która wywołuje interfejsy API sieci Web — uzyskiwanie tokenu

Aby można było rozpocząć wywoływanie chronionych interfejsów API sieci Web, aplikacja będzie potrzebować tokenu dostępu. W tym artykule omówiono proces uzyskiwania tokenu przy użyciu biblioteki uwierzytelniania firmy Microsoft (MSAL).

## <a name="scopes-to-request"></a>Zakresy do żądania

Podczas żądania tokenu należy zdefiniować zakres. Zakres określa, jakie dane może uzyskać dostęp do aplikacji.  

Najprostszym podejściem jest łączenie `App ID URI` żądanych interfejsów API sieci Web z zakresem `.default`. W ten sposób mówimy platformę tożsamości firmy Microsoft, że aplikacja wymaga wszystkich zakresów ustawionych w portalu.

#### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

#### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

#### <a name="xamarin"></a>Xamarin
```CSharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Pobierz tokeny

### <a name="acquire-tokens-via-msal"></a>Uzyskiwanie tokenów za pośrednictwem MSAL

MSAL umożliwia aplikacjom uzyskiwanie tokenów dyskretnie i interaktywnie. Po prostu wywołaj te metody, a MSAL zwraca token dostępu dla żądanych zakresów. Prawidłowym wzorcem jest wykonanie żądania dyskretnego i powrót do interakcyjnego żądania.

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

**Najpierw spróbuj uzyskać token w trybie dyskretnym:**

Cel-C:

```objc

NSArray *scopes = @[@"https://graph.microsoft.com/.default"];
NSString *accountIdentifier = @"my.account.id";
    
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];
    
MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {
        
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;
            
        // Access token to call the Web API
        NSString *accessToken = result.accessToken;
    }
        
    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
        return;
    }
}];
```
 
Adres

```swift

let scopes = ["https://graph.microsoft.com/.default"]
let accountIdentifier = "my.account.id"
        
guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in
            
    guard let authResult = result, error == nil else {
                
    let nsError = error! as NSError
                
    if (nsError.domain == MSALErrorDomain &&
        nsError.code == MSALError.interactionRequired.rawValue) {
                    
            // Interactive auth will be required, call acquireToken()
            return
         }
         return
     }
            
    // You'll want to get the account identifier to retrieve and reuse the account
    // for later acquireToken calls
    let accountIdentifier = authResult.account.identifier
            
    // Access token to call the Web API
    let accessToken = authResult.accessToken
}
```

**Następnie Jeśli MSAL zwraca `MSALErrorInteractionRequired`, spróbuj uzyskać tokeny interaktywnie:**

Cel-C:

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithParentViewController:viewController];
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:webParameters];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error) 
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;
            
        NSString *accessToken = result.accessToken;
    }
}];
```

Adres

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(parentViewController: viewController)
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: webviewParameters)
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in
                
    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }
                
    // Get access token from result
    let accessToken = authResult.accessToken
})
```

MSAL dla systemów iOS i macOS obsługuje różne Modyfikatory podczas interakcyjnego lub dyskretnego uzyskiwania tokenu.
* [Typowe parametry podczas uzyskiwania tokenu](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Parametry dotyczące pozyskiwania tokenów interaktywnych](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Parametry pozyskiwania tokenów dyskretnych](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

Poniższy przykład pokazuje minimalny kod, aby interaktywnie uzyskać token do odczytywania profilu użytkownika przy użyciu Microsoft Graph.

```CSharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

#### <a name="mandatory-parameters-in-msalnet"></a>Obowiązkowe parametry w MSAL.NET

`AcquireTokenInteractive` ma tylko jeden obowiązkowy ``scopes``parametrów, który zawiera Wyliczenie ciągów definiujących zakresy, dla których wymagany jest token. Jeśli token jest przeznaczony dla Microsoft Graph, wymagane zakresy można znaleźć w dokumentacji interfejsu API dla każdego interfejsu API programu Microsoft Graph w sekcji o nazwie "uprawnienia". Na przykład aby [wyświetlić listę kontaktów użytkownika](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), należy użyć zakresu "User. Read", "Contacts. Read". Zobacz również [Microsoft Graph informacje o uprawnieniach](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

Jeśli nie określono go podczas kompilowania aplikacji, w systemie Android należy również określić działanie nadrzędne (przy użyciu `.WithParentActivityOrWindow`, zobacz poniżej), aby token odwracał do tego działania nadrzędnego po interakcji. Jeśli nie zostanie on określony, zostanie zgłoszony wyjątek podczas wywoływania `.ExecuteAsync()`.

#### <a name="specific-optional-parameters-in-msalnet"></a>Określone parametry opcjonalne w MSAL.NET

##### <a name="withprompt"></a>WithPrompt

`WithPrompt()` służy do kontrolowania interakcji z użytkownikiem przez określenie monitu

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Klasa definiuje następujące stałe:

- ``SelectAccount``: spowoduje wymuszenie prezentowania przez usługę STS okna dialogowego wyboru konta zawierającego konta, dla których użytkownik ma sesję. Ta opcja jest przydatna, gdy deweloperzy aplikacji chcą zezwolić użytkownikom na wybór różnych tożsamości. Ta opcja umożliwia MSAL wysyłanie ``prompt=select_account`` do dostawcy tożsamości. Ta opcja jest domyślna i jest dobrym zadaniem do zapewnienia najlepszego możliwego środowiska na podstawie dostępnych informacji (konta, obecności sesji użytkownika itd.). ...). Nie zmieniaj go, chyba że masz dobry powód do tego celu.
- ``Consent``: umożliwia deweloperowi aplikacji wymuszenie monitowania użytkownika o zgodę, nawet jeśli zgoda została udzielona wcześniej. W takim przypadku MSAL wysyła `prompt=consent` do dostawcy tożsamości. Ta opcja może być używana w niektórych aplikacjach ukierunkowanych na zabezpieczenia, w których organizacja organizacji wymaga, aby użytkownik przedstawił okno dialogowe zgody za każdym razem, gdy aplikacja jest używana.
- ``ForceLogin``: umożliwia deweloperowi aplikacji wyświetlenie monitu o podanie poświadczeń przez usługę, nawet jeśli ten monit nie jest wymagany. Ta opcja może być przydatna, jeśli uzyskanie tokenu kończy się niepowodzeniem, aby pozwolić użytkownikowi na ponowne logowanie. W takim przypadku MSAL wysyła `prompt=login` do dostawcy tożsamości. Z tego względu zaobserwowano, że został on użyty w niektórych aplikacjach ukierunkowanych na zabezpieczenia, w których administrator organizacji wymaga, aby użytkownik zalogował się ponownie w każdym momencie, gdy uzyskuje dostęp do określonych części aplikacji.
- ``Never`` (tylko dla programu .NET 4,5 i WinRT) nie monituje użytkownika, ale zamiast tego spróbuje użyć pliku cookie przechowywanego w ukrytym osadzonym widoku sieci Web (patrz poniżej: widoki sieci Web w MSAL.NET). Użycie tej opcji może zakończyć się niepowodzeniem, a w takim przypadku `AcquireTokenInteractive` zgłosi wyjątek w celu powiadomienia o konieczności interakcji z interfejsem użytkownika i należy użyć innego parametru `Prompt`.
- ``NoPrompt``: nie wyśle żadnego monitu do dostawcy tożsamości. Ta opcja jest przydatna tylko w przypadku Azure AD B2C edytowania zasad profilu (zobacz [szczegóły B2C](https://aka.ms/msal-net-b2c-specificities)).

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Ten modyfikator jest używany w zaawansowanym scenariuszu, w którym użytkownik powinien wstępnie wyrazić zgodę na kilka zasobów z góry (i nie chce używać powyższej zgody, która zwykle jest używana z MSAL.NET/Microsoft Identity platform v 2.0). Aby uzyskać szczegółowe informacje [, zobacz How to: czy użytkownik ma zgodę na dostęp do kilku zasobów](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources).

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Inne parametry opcjonalne

Dowiedz się więcej na temat wszystkich innych parametrów opcjonalnych dla `AcquireTokenInteractive` z dokumentacji referencyjnej dla [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)

### <a name="acquire-tokens-via-the-protocol"></a>Uzyskiwanie tokenów za pośrednictwem protokołu

Nie zalecamy użycia protokołu bezpośrednio. Jeśli to zrobisz, aplikacja nie będzie obsługiwać niektórych scenariuszy logowania jednokrotnego (SSO), zarządzania urządzeniami i dostępu warunkowego.

W przypadku korzystania z protokołu do uzyskiwania tokenów dla aplikacji mobilnych należy wykonać dwa żądania: uzyskaj kod autoryzacji i wymieniaj go z tokenem.

#### <a name="get-authorization-code"></a>Pobierz kod autoryzacji

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-token"></a>Uzyskaj dostęp i token odświeżania

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

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wywoływanie interfejsu API sieci Web](scenario-mobile-call-api.md)
