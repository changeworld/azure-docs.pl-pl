---
title: Aplikacja mobilna, która wywołuje interfejsy API sieci Web — uzyskiwanie tokenu dla aplikacji | Platforma tożsamości firmy Microsoft
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
ms.openlocfilehash: d49717355cab5441d26608fa12333bd1b8b73d44
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68413541"
---
# <a name="mobile-app-that-calls-web-apis---get-a-token"></a>Aplikacja mobilna, która wywołuje interfejsy API sieci Web — uzyskiwanie tokenu

Aby można było rozpocząć wywoływanie chronionych interfejsów API sieci Web, aplikacja będzie potrzebować tokenu dostępu. W tym artykule omówiono proces uzyskiwania tokenu przy użyciu biblioteki uwierzytelniania firmy Microsoft (MSAL).

## <a name="scopes-to-request"></a>Zakresy do żądania

Podczas żądania tokenu należy zdefiniować zakres. Zakres określa, jakie dane może uzyskać dostęp do aplikacji.  

Najprostszym podejściem jest łączenie żądanych interfejsów API `App ID URI` sieci Web z zakresem. `.default` W ten sposób mówimy platformę tożsamości firmy Microsoft, że aplikacja wymaga wszystkich zakresów ustawionych w portalu.

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

## <a name="get-tokens"></a>Pobierz tokeny

### <a name="via-msal"></a>Via MSAL

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

### <a name="mandatory-parameters"></a>Parametry obowiązkowe

`AcquireTokenInteractive`ma tylko jeden obowiązkowy parametr ``scopes``, który zawiera Wyliczenie ciągów definiujących zakresy, dla których wymagany jest token. Jeśli token jest przeznaczony dla Microsoft Graph, wymagane zakresy można znaleźć w dokumentacji interfejsu API dla każdego interfejsu API programu Microsoft Graph w sekcji o nazwie "uprawnienia". Na przykład aby [wyświetlić listę kontaktów użytkownika](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), należy użyć zakresu "User. Read", "Contacts. Read". Zobacz również [Microsoft Graph informacje](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)o uprawnieniach.

Jeśli nie określono go podczas kompilowania aplikacji, w systemie Android należy również określić działanie nadrzędne (przy użyciu `.WithParentActivityOrWindow`, patrz poniżej), aby token odwracał do tego działania nadrzędnego po interakcji. Jeśli nie zostanie on określony, zostanie zgłoszony wyjątek podczas wywoływania `.ExecuteAsync()`.

### <a name="specific-optional-parameters"></a>Określone parametry opcjonalne

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()`służy do kontrolowania interakcji z użytkownikiem przez określenie monitu

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Klasa definiuje następujące stałe:

- ``SelectAccount``: wymusi, aby usługa STS zaprezentować okno dialogowe wyboru konta zawierające konta, dla których użytkownik ma sesję. Ta opcja jest przydatna, gdy deweloperzy aplikacji chcą zezwolić użytkownikom na wybór różnych tożsamości. Ta opcja umożliwia MSAL wysyłanie ``prompt=select_account`` do dostawcy tożsamości. Ta opcja jest domyślna i jest dobrym zadaniem do zapewnienia najlepszego możliwego środowiska na podstawie dostępnych informacji (konta, obecności sesji użytkownika itd.). ...). Nie zmieniaj go, chyba że masz dobry powód do tego celu.
- ``Consent``: umożliwia deweloperowi aplikacji wymuszenie monitowania użytkownika o zgodę, nawet jeśli zgoda została udzielona wcześniej. W takim przypadku MSAL wysyła `prompt=consent` do dostawcy tożsamości. Ta opcja może być używana w niektórych aplikacjach ukierunkowanych na zabezpieczenia, w których organizacja organizacji wymaga, aby użytkownik przedstawił okno dialogowe zgody za każdym razem, gdy aplikacja jest używana.
- ``ForceLogin``: umożliwia deweloperowi aplikacji wyświetlenie monitu o podanie poświadczeń przez usługę, nawet jeśli ten monit nie jest wymagany. Ta opcja może być przydatna, jeśli uzyskanie tokenu kończy się niepowodzeniem, aby pozwolić użytkownikowi na ponowne logowanie. W takim przypadku MSAL wysyła `prompt=login` do dostawcy tożsamości. Z tego względu zaobserwowano, że został on użyty w niektórych aplikacjach ukierunkowanych na zabezpieczenia, w których administrator organizacji wymaga, aby użytkownik zalogował się ponownie w każdym momencie, gdy uzyskuje dostęp do określonych części aplikacji.
- ``Never``(tylko w przypadku programu .NET 4,5 i WinRT) nie monituje użytkownika, ale zamiast tego spróbuje użyć pliku cookie przechowywanego w ukrytym osadzonym widoku sieci Web (patrz poniżej: Widoki sieci Web w programie MSAL.NET). Użycie tej opcji może zakończyć się niepowodzeniem, a `AcquireTokenInteractive` w takim przypadku zostanie zgłoszony wyjątek w celu powiadomienia o konieczności interakcji z interfejsem użytkownika i należy użyć innego `Prompt` parametru.
- ``NoPrompt``: Nie wyśle żadnego monitu do dostawcy tożsamości. Ta opcja jest przydatna tylko w przypadku Azure AD B2C edytowania zasad profilu (zobacz [szczegóły B2C](https://aka.ms/msal-net-b2c-specificities)).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Ten modyfikator jest używany w zaawansowanym scenariuszu, w którym użytkownik powinien wstępnie wyrazić zgodę na kilka zasobów z góry (i nie chce używać powyższej zgody, która zwykle jest używana z MSAL.NET/Microsoft Identity platform v 2.0). Aby uzyskać szczegółowe informacje [, zobacz How to: czy użytkownik ma zgodę na dostęp do kilku zasobów](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources).

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="other-optional-parameters"></a>Inne parametry opcjonalne

Dowiedz się więcej o wszystkich innych opcjonalnych `AcquireTokenInteractive` parametrach z dokumentacji referencyjnej dla [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)

### <a name="via-the-protocol"></a>Za pośrednictwem protokołu

Nie zalecamy użycia protokołu bezpośrednio. Jeśli to zrobisz, aplikacja nie będzie obsługiwać niektórych scenariuszy logowania jednokrotnego (SSO), zarządzania urządzeniami i dostępu warunkowego.

W przypadku korzystania z protokołu do uzyskiwania tokenów dla aplikacji mobilnych należy wykonać dwa żądania: uzyskaj kod autoryzacji i wymieniaj go z tokenem.

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

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wywoływanie interfejsu API sieci Web](scenario-mobile-call-api.md)
