---
title: Uzyskiwanie tokenu do wywoływania internetowego interfejsu API (aplikacji klasycznej) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć aplikację klasyczną, która wywołuje internetowe interfejsy API w celu uzyskania tokenu dla aplikacji
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: a5942a9d614bbb06fadb1d4b16d4c68c007434c7
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885322"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>Aplikacja klasyczna, która wywołuje internetowe interfejsy API: uzyskiwanie tokenu

Po skompilowaniu wystąpienia publicznej aplikacji klienckiej użyjesz go do uzyskania tokenu, który następnie użyjesz do wywołania internetowego interfejsu API.

## <a name="recommended-pattern"></a>Zalecany wzór

Internetowy interfejs API jest `scopes`definiowany przez jego . Niezależnie od doświadczenia, które można podać w aplikacji, wzorzec do użycia jest:

- Systematycznie próbuj uzyskać token z pamięci `AcquireTokenSilent`podręcznej tokenu, wywołując .
- Jeśli to wywołanie `AcquireToken` nie powiedzie się, użyj przepływu, `AcquireTokenXX`którego chcesz użyć, który jest reprezentowany w tym miejscu przez program .

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>w MSAL.NET

```csharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account  
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```

# <a name="java"></a>[Java](#tab/java)

```java

Set<IAccount> accountsInCache = pca.getAccounts().join();
// Take first account in the cache. In a production application, you would filter
// accountsInCache to get the right account for the user authenticating.
IAccount account = accountsInCache.iterator().next();

IAuthenticationResult result;
try {
    SilentParameters silentParameters =
            SilentParameters
                    .builder(SCOPE, account)
                    .build();

    // try to acquire token silently. This call will fail since the token cache
    // does not have any data for the user you are trying to acquire a token for
    result = pca.acquireTokenSilently(silentParameters).join();
} catch (Exception ex) {
    if (ex.getCause() instanceof MsalException) {

        InteractiveRequestParameters parameters = InteractiveRequestParameters
                .builder(new URI("http://localhost"))
                .scopes(SCOPE)
                .build();

        // Try to acquire a token interactively with system browser. If successful, you should see
        // the token and account information printed out to console
        result = pca.acquireToken(parameters).join();
    } else {
        // Handle other exceptions accordingly
        throw ex;
    }
}
return result;

```

# <a name="python"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```

# <a name="macos"></a>[Macos](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>W systemie MSAL dla systemu iOS i macOS

Cel C:

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```
Swift:

```swift
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
}
```
---

Oto różne sposoby uzyskiwania tokenów w aplikacji klasycznej.

## <a name="acquire-a-token-interactively"></a>Interaktywne pozyskiwanie tokenu

W poniższym przykładzie pokazano minimalny kod, aby uzyskać token interaktywnie do odczytu profilu użytkownika za pomocą programu Microsoft Graph.

# <a name="net"></a>[.NET](#tab/dotnet)
### <a name="in-msalnet"></a>w MSAL.NET

```csharp
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

`AcquireTokenInteractive`ma tylko jeden parametr ``scopes``obowiązkowy, który zawiera wyliczenie ciągów, które definiują zakresy, dla których wymagany jest token. Jeśli token jest dla programu Microsoft Graph, wymagane zakresy można znaleźć w odwołaniu do interfejsu API każdego interfejsu API programu Microsoft Graph w sekcji o nazwie "Uprawnienia". Na przykład, aby [wyświetlić listę kontaktów użytkownika,](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)należy użyć zakresu "User.Read", "Contacts.Read". Aby uzyskać więcej informacji, zobacz [Odwołanie do uprawnień programu Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

W systemie Android należy również określić `.WithParentActivityOrWindow`działanie nadrzędne przy użyciu , jak pokazano, tak aby token wraca do tego działania nadrzędnego po interakcji. Jeśli go nie określisz, podczas wywoływania `.ExecuteAsync()`zostanie zgłoszony wyjątek.

### <a name="specific-optional-parameters-in-msalnet"></a>Specyficzne parametry opcjonalne w MSAL.NET

#### <a name="withparentactivityorwindow"></a>ZParentActivityOrWindow

Interfejs użytkownika jest ważne, ponieważ jest interaktywny. `AcquireTokenInteractive`ma jeden określony parametr opcjonalny, który można określić, dla platform, które go obsługują, nadrzędnego interfejsu użytkownika. Gdy jest używany w `.WithParentActivityOrWindow` aplikacji klasycznej, ma inny typ, który zależy od platformy.

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Uwagi:

- W systemie .NET `object` Standard `Activity` oczekiwana `UIViewController` wartość jest `NSWindow` obowiązuje `IWin32Window` w `IntPr` systemie Android, iOS, mac i windows.
- W systemie Windows `AcquireTokenInteractive` należy wywołać z wątku interfejsu użytkownika, tak aby osadzona przeglądarka pobiera odpowiedni kontekst synchronizacji interfejsu użytkownika. Nie wywołanie z wątku interfejsu użytkownika może spowodować komunikaty nie pompować poprawnie i zakleszczenie scenariuszy z interfejsu użytkownika. Jednym ze sposobów wywoływania bibliotek uwierzytelniania firmy Microsoft (MSALs) z wątku interfejsu użytkownika, `Dispatcher` jeśli nie jesteś już w wątku interfejsu użytkownika jest użycie na WPF.
- Jeśli używasz WPF, aby uzyskać okno z kontrolki WPF, można użyć `WindowInteropHelper.Handle` klasy. Następnie wywołanie jest z kontroli`this`WPF ( ):

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>ZPrompt

`WithPrompt()`służy do kontrolowania interaktywności z użytkownikiem, określając monit.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Klasa definiuje następujące stałe:

- ``SelectAccount``wymusza, aby sts przedstawiał okno dialogowe wyboru konta, które zawiera konta, dla których użytkownik ma sesję. Ta opcja jest przydatna, gdy deweloperzy aplikacji chcą pozwolić użytkownikom wybierać spośród różnych tożsamości. Ta opcja umożliwia msal do wysyłania ``prompt=select_account`` do dostawcy tożsamości. Ta opcja jest domyślnie zaznaczona. Wykonuje dobrą robotę, zapewniając najlepsze możliwe środowisko na podstawie dostępnych informacji, takich jak konto i obecność sesji dla użytkownika. Nie zmieniaj go, chyba że masz ku temu dobry powód.
- ``Consent``umożliwia deweloperowi aplikacji, aby wymusić użytkownika, aby zostać poproszony o zgodę, nawet jeśli zgoda została udzielona wcześniej. W takim przypadku MSAL wysyła `prompt=consent` do dostawcy tożsamości. Ta opcja może być używana w niektórych aplikacjach z uwzględnieniem zabezpieczeń, w których zarządzanie organizacją wymaga, aby użytkownik był przedstawiany z okna dialogowego zgody za każdym razem, gdy aplikacja jest używana.
- ``ForceLogin``umożliwia deweloperowi aplikacji monitowanie użytkownika o poświadczenia przez usługę, nawet jeśli ten monit użytkownika może nie być potrzebny. Ta opcja może być przydatne, aby umożliwić użytkownikowi zalogować się ponownie, jeśli pobieranie tokenu nie powiedzie się. W takim przypadku MSAL wysyła `prompt=login` do dostawcy tożsamości. Czasami jest używany w aplikacjach bezpieczeństwa, gdzie zarządzanie organizacją wymaga, aby użytkownik ponownie loguje się za każdym razem, gdy uzyskują dostęp do określonych części aplikacji.
- ``Never``(tylko dla platformy .NET 4.5 i WinRT) nie poprosi użytkownika, ale zamiast tego spróbuje użyć pliku cookie przechowywanego w ukrytym osadzonym widoku sieci Web. Aby uzyskać więcej informacji, zobacz widoki sieci Web w MSAL.NET. Użycie tej opcji może zakończyć się niepowodzeniem. W takim `AcquireTokenInteractive` przypadku zgłasza wyjątek, aby powiadomić, że interakcja interfejsu użytkownika jest potrzebna. Musisz użyć innego `Prompt` parametru.
- ``NoPrompt``nie wyśle żadnego monitu do dostawcy tożsamości. Ta opcja jest przydatna tylko w przypadku zasad profilu edycji usługi Azure Active Directory (Azure AD) B2C. Aby uzyskać więcej informacji, zobacz [szczegóły usługi Azure AD B2C](https://aka.ms/msal-net-b2c-specificities).

#### <a name="withextrascopetoconsent"></a>ZExtraScopeToConsent

Ten modyfikator jest używany w zaawansowanym scenariuszu, w którym użytkownik ma wstępnie wyrazić zgodę na kilka zasobów z góry i nie chcesz używać przyrostowej zgody, która jest zwykle używana z MSAL.NET/the platformie tożsamości firmy Microsoft. Aby uzyskać więcej informacji, zobacz [Wyrażanie zgody użytkownika z góry na kilka zasobów.](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>ZCustomWebUi

Interfejs użytkownika sieci web to mechanizm do wywoływania przeglądarki. Ten mechanizm może być dedykowany ui WebBrowser kontroli lub sposób delegowania otwierania przeglądarki.
MSAL udostępnia implementacje interfejsu użytkownika sieci Web dla większości platform, ale istnieją przypadki, w których można samodzielnie hostować przeglądarkę:

- Platformy, które nie są jawnie objęte MSAL, na przykład Blazor, Unity i Mono na komputerach stacjonarnych.
- Chcesz interfejsu użytkownika przetestować aplikację i użyć zautomatyzowanej przeglądarki, która może być używana z Selenem.
- Przeglądarka i aplikacja, które uruchamiają MSAL są w oddzielnych procesach.

##### <a name="at-a-glance"></a>W skrócie

Aby to osiągnąć, dajesz `start Url`MSAL , które muszą być wyświetlane w wybranej przeglądarce, aby użytkownik końcowy mógł wprowadzać elementy, takie jak ich nazwa użytkownika.
Po zakończeniu uwierzytelniania aplikacja musi przejść z `end Url`powrotem do msal , który zawiera kod dostarczony przez usługę Azure AD.
Gospodarz jest `end Url` zawsze `redirectUri`. Aby `end Url`przechwycić, wykonaj jedną z następujących czynności:

- Monitoruj przekierowania przeglądarki, aż `redirect Url` zostanie trafione.
- Przekieruj przeglądarkę do adresu URL, który monitorujesz.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi jest punktem rozszerzalności

`WithCustomWebUi`jest punktem rozszerzalności, którego można użyć do zapewnienia własnego interfejsu użytkownika w publicznych aplikacjach klienckich. Można również zezwolić użytkownikowi przejść przez /Authorize punktu końcowego dostawcy tożsamości i pozwolić mu zalogować się i wyrazić zgodę. MSAL.NET można następnie zrealizować kod uwierzytelniania i uzyskać token. Na przykład jest używany w programie Visual Studio, aby aplikacje elektronów (na przykład Visual Studio Feedback) zapewnić interakcję sieci web, ale pozostawić do MSAL.NET, aby wykonać większość pracy. Można go również użyć, jeśli chcesz zapewnić automatyzację interfejsu użytkownika. W publicznych aplikacjach klienckich MSAL.NET używa standardu Klucza próbny dla wymiany kodu (PKCE), aby zapewnić przestrzeganie zabezpieczeń. Tylko MSAL.NET może zrealizować kod. Aby uzyskać więcej informacji, zobacz [RFC 7636 - Klucz dowodu wymiany kodu przez klientów publicznych OAuth](https://tools.ietf.org/html/rfc7636).

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>Użyj zCustomWebUi

Aby `.WithCustomWebUI`użyć , wykonaj następujące kroki.

  1. Zaimplementuj interfejs `ICustomWebUi`. Aby uzyskać więcej informacji, zobacz [tę stronę internetową](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). Zaimplementuj jedną `AcquireAuthorizationCodeAsync`metodę i zaakceptuj adres URL kodu autoryzacji obliczony przez MSAL.NET. Następnie pozwól użytkownikowi przejść przez interakcję z dostawcą tożsamości i zwrócić adres URL, za pomocą którego dostawca tożsamości nazwałby implementację z powrotem wraz z kodem autoryzacji. Jeśli masz problemy, implementacja `MsalExtensionException` powinna zgłosić wyjątek, aby ładnie współpracować z MSAL.
  2. W `AcquireTokenInteractive` wywołaniu użyj `.WithCustomUI()` modyfikatora przekazując wystąpienie niestandardowego interfejsu użytkownika sieci Web.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>Przykłady implementacji ICustomWebUi w automatyzacji testów: SeleniumWebUI

Zespół MSAL.NET przepisał testy interfejsu użytkownika, aby użyć tego mechanizmu rozszerzalności. Jeśli jesteś zainteresowany, spójrz na [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) klasy w kodzie źródłowym MSAL.NET.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>Zapewnij doskonałe wrażenia z systemwebviewoptions

Z MSAL.NET 4.1 [`SystemWebViewOptions`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet)można określić:

- Identyfikator URI, aby`BrowserRedirectError`przejść do ( )`HtmlMessageError`lub fragment HTML do wyświetlenia ( ) w przypadku błędów logowania lub zgody w systemowej przeglądarce internetowej.
- Identyfikator URI, aby`BrowserRedirectSuccess`przejść do ( )`HtmlMessageSuccess`lub fragment HTML do wyświetlenia ( ) w przypadku pomyślnego logowania lub zgody.
- Akcja do uruchomienia w celu uruchomienia przeglądarki systemowej. Można podać własną implementację, `OpenBrowserAsync` ustawiając pełnomocnika. Klasa zapewnia również domyślną implementację `OpenWithEdgeBrowserAsync` dla `OpenWithChromeEdgeBrowserAsync` dwóch przeglądarek: i microsoft edge i [Microsoft Edge na Chromium](https://www.windowscentral.com/faq-edge-chromium), odpowiednio.

Aby użyć tej struktury, napisz coś w następującym przykładzie:

```csharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>Inne parametry opcjonalne

Aby dowiedzieć się więcej o `AcquireTokenInteractive`wszystkich innych parametrach opcjonalnych dla , zobacz [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods).

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

MSAL Python nie zapewnia metody tokenu nabycia interaktywnego bezpośrednio. Zamiast tego wymaga aplikacji do wysyłania żądania autoryzacji w jego realizacji przepływu interakcji użytkownika, aby uzyskać kod autoryzacji. Ten kod można następnie `acquire_token_by_authorization_code` przekazać do metody, aby uzyskać token.

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_authorization_code(
         request.args['code'],
         scopes=config["scope"])    

```

# <a name="macos"></a>[Macos](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>W systemie MSAL dla systemu iOS i macOS

Cel C:

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
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

Swift:

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```
---

## <a name="integrated-windows-authentication"></a>Zintegrowane uwierzytelnianie systemu Windows

Aby zalogować się do użytkownika domeny na komputerze przyłączanym do usługi Azure AD, użyj zintegrowanego uwierzytelniania systemu Windows (IWA).

### <a name="constraints"></a>Ograniczenia

- Zintegrowane uwierzytelnianie systemu Windows jest użyteczne tylko dla użytkowników *federacyjnej+,* czyli użytkowników utworzonych w usłudze Active Directory i wspieranych przez usługę Azure AD. Użytkownicy utworzyli bezpośrednio w usłudze Azure AD bez kopii zapasowej usługi Active Directory, znanej jako użytkownicy *zarządzani,* nie mogą używać tego przepływu uwierzytelniania. To ograniczenie nie ma wpływu na przepływ nazwy użytkownika i hasła.
- IWA jest przeznaczony dla aplikacji napisanych dla platform .NET Framework, .NET Core i Universal Windows Platform (UWP).
- IWA nie pomija uwierzytelniania wieloskładnikowego (MFA). Jeśli usługa MFA jest skonfigurowana, IWA może zakończyć się niepowodzeniem, jeśli wymagane jest wyzwanie usługi MFA, ponieważ usługa MFA wymaga interakcji z użytkownikiem.
  > [!NOTE]
  > Ten jest trudny. IWA nie jest interakcyjny, ale usługa MFA wymaga interaktywności użytkownika. Nie masz kontroli, gdy dostawca tożsamości żąda wykonania usługi MFA, administrator dzierżawy. Z naszych obserwacji wynika, że usługa MFA jest wymagana, gdy logujesz się z innego kraju, gdy nie masz połączenia za pośrednictwem sieci VPN z siecią firmową, a czasami nawet po podłączeniu przez VPN. Nie oczekuj deterministycznego zestawu reguł. Usługa Azure AD używa sztucznej inteligencji do ciągłego uczenia się, czy usługa MFA jest wymagana. Powrót do monitu użytkownika, takiego jak uwierzytelnianie interaktywne lub przepływ kodu urządzenia, jeśli IWA nie powiedzie się.

- Organ uchwalony `PublicClientApplicationBuilder` musi być:
  - Dzierżawiony formularza `https://login.microsoftonline.com/{tenant}/`, `tenant` gdzie jest identyfikator GUID, który reprezentuje identyfikator dzierżawy lub domeny skojarzonej z dzierżawy.
  - Dla każdej pracy i `https://login.microsoftonline.com/organizations/`kont szkolnych: .
  - Konta osobiste firmy Microsoft nie są obsługiwane. Nie można używać /common lub /consumers dzierżawców.

- Ponieważ zintegrowane uwierzytelnianie systemu Windows jest przepływem cichym:
  - Użytkownik aplikacji musi wcześniej wyrazić zgodę na korzystanie z aplikacji.
  - Lub administrator dzierżawy musi wcześniej wyrazić zgodę na wszystkich użytkowników w dzierżawie do korzystania z aplikacji.
  - Innymi słowy:
    - Jako deweloper wybrałeś przycisk **Grant** w witrynie Azure portal dla siebie.
    - Lub administrator dzierżawy wybrał **grant/revoke zgody administratora dla {domeny dzierżawy}** przycisk na karcie **uprawnienia interfejsu API** rejestracji dla aplikacji. Aby uzyskać więcej informacji, zobacz [Dodawanie uprawnień dostępu do interfejsów API sieci Web](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis).
    - Możesz też udostępnić użytkownikom możliwość wyrażenia zgody na aplikację. Aby uzyskać więcej informacji, zobacz [Żądanie zgody użytkownika](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent).
    - Lub podano sposób dla administratora dzierżawy, aby wyrazić zgodę na aplikację. Aby uzyskać więcej informacji, zobacz [Zgoda administratora](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant).

- Ten przepływ jest włączony dla aplikacji .NET desktop, .NET Core i platformy uniwersalnej systemu Windows.

Aby uzyskać więcej informacji na temat zgody, zobacz [Uprawnienia i zgoda platformy tożsamości firmy Microsoft](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent).

### <a name="learn-how-to-use-it"></a>Dowiedz się, jak z niego korzystać

# <a name="net"></a>[.NET](#tab/dotnet)

W MSAL.NET musisz użyć:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

Zwykle potrzebujesz tylko jednego`scopes`parametru ( ). W zależności od sposobu konfigurowania zasad przez administratora systemu Windows aplikacje na komputerze z systemem Windows mogą nie być dozwolone wyszukywać zalogowanych użytkowników. W takim przypadku użyj drugiej `.WithUsername()`metody i przekaż nazwę użytkownika zalogowanego jako format UPN, `joe@contoso.com`na przykład . W programie .NET Core dostępna jest tylko przeciążenie przyjmujące nazwę użytkownika, ponieważ platforma .NET Core nie może poprosić o nazwę użytkownika systemu operacyjnego.

Poniższy przykład przedstawia najbardziej aktualny przypadek, z objaśnieniami rodzaju wyjątków, które można uzyskać i ich środków zaradczych.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

Aby zapoznać się z listą możliwych modyfikatorów w acquireTokenByIntegratedWindowsAuthentication, zobacz [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods).

# <a name="java"></a>[Java](#tab/java)

Ten wyciąg pochodzi z [przykładów deweloperów MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Ten przepływ nie jest jeszcze obsługiwany w języku MSAL Python.

# <a name="macos"></a>[Macos](#tab/macOS)

Ten przepływ nie dotyczy systemu MacOS.

---

## <a name="username-and-password"></a>Nazwa użytkownika i hasło

Można również uzyskać token, podając nazwę użytkownika i hasło. Przepływ ten jest ograniczony i nie jest zalecane, ale nadal istnieją przypadki użycia, gdzie jest to konieczne.

### <a name="this-flow-isnt-recommended"></a>Ten przepływ nie jest zalecany

Ten przepływ nie jest *zalecane,* ponieważ o aplikacji poprosić użytkownika o ich hasło nie jest bezpieczny. Aby uzyskać więcej informacji, zobacz [Co jest rozwiązaniem narastającego problemu haseł?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). Preferowanym przepływem do pochyłego uzyskiwania tokenu na komputerach przyłączonych do domeny systemu Windows jest [zintegrowane uwierzytelnianie systemu Windows.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication) Można również użyć [przepływu kodu urządzenia](https://aka.ms/msal-net-device-code-flow).

> [!NOTE]
> Przy użyciu nazwy użytkownika i hasła jest przydatne w niektórych przypadkach, takich jak scenariusze DevOps. Ale jeśli chcesz użyć nazwy użytkownika i hasła w interaktywnych scenariuszach, w których udostępniasz własny interfejs użytkownika, zastanów się, jak odejść od niego. Używając nazwy użytkownika i hasła, rezygnujesz z wielu rzeczy:
>
> - Podstawowe założenia współczesnej tożsamości. Hasło może zostać wygłumione i odtwonione, ponieważ udostępniony klucz tajny może zostać przechwycony. Jest niezgodny z bez hasła.
> - Użytkownicy, którzy muszą wykonać usługi MFA, nie mogą się zalogować, ponieważ nie ma interakcji.
> - Użytkownicy nie mogą jednokrotnie logować się (logowanie jednokrotne).

### <a name="constraints"></a>Ograniczenia

Obowiązują również następujące ograniczenia:

- Przepływ nazwy użytkownika i hasła nie jest zgodny z dostępem warunkowym i uwierzytelnianiem wieloskładnikowym. W związku z tym jeśli aplikacja jest uruchamiana w dzierżawie usługi Azure AD, w której administrator dzierżawy wymaga uwierzytelniania wieloskładnikowego, nie można użyć tego przepływu. Wiele organizacji to robi.
- Działa tylko na kontach służbowych i szkolnych (nie MSA).
- Przepływ jest dostępny na pulpicie platformy .NET i .NET Core, ale nie na platformie uniwersalnej systemu Windows.

### <a name="b2c-specifics"></a>Specyfika B2C

Aby uzyskać więcej informacji, zobacz [Poświadczenia hasła właściciela zasobu (ROPC) z B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="use-it"></a>Użyj go

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`zawiera metodę `AcquireTokenByUsernamePassword`.

Poniższa próbka przedstawia uproszczony przypadek.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

Poniższy przykład przedstawia najbardziej aktualny przypadek, z objaśnieniami rodzaju wyjątków, które można uzyskać i ich środków zaradczych.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See https://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

Aby uzyskać więcej informacji na temat wszystkich `AcquireTokenByUsernamePassword`modyfikatorów, które można zastosować do , zobacz [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods).

# <a name="java"></a>[Java](#tab/java)

Poniższy wyciąg pochodzi z [przykładów deweloperów MSAL Java.](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)

```Java
private static IAuthenticationResult acquireTokenUsernamePassword() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();
        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            UserNamePasswordParameters parameters =
                    UserNamePasswordParameters
                            .builder(SCOPE, USER_NAME, USER_PASSWORD.toCharArray())
                            .build();
            // Try to acquire a token via username/password. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Ten wyciąg pochodzi z [przykładów deweloperów języka MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    # See this page for constraints of Username Password Flow.
    # https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Username-Password-Authentication
    result = app.acquire_token_by_username_password(
        config["username"], config["password"], scopes=config["scope"])
```

# <a name="macos"></a>[Macos](#tab/macOS)

Ten przepływ nie jest obsługiwany w systemie MSAL dla systemu macOS.

---

## <a name="command-line-tool-without-a-web-browser"></a>Narzędzie wiersza polecenia bez przeglądarki internetowej

### <a name="device-code-flow"></a>Przepływ kodu urządzenia

Jeśli piszesz narzędzie wiersza polecenia, które nie ma formantów sieci Web i nie możesz lub nie chcesz używać poprzednich przepływów, musisz użyć przepływu kodu urządzenia.

Uwierzytelnianie interaktywne za pomocą usługi Azure AD wymaga przeglądarki sieci Web. Aby uzyskać więcej informacji, zobacz [Korzystanie z przeglądarek internetowych](https://aka.ms/msal-net-uses-web-browser). Aby uwierzytelnić użytkowników na urządzeniach lub systemach operacyjnych, które nie udostępniają przeglądarki internetowej, przepływ kodu urządzenia umożliwia użytkownikowi korzystanie z innego urządzenia, takiego jak komputer lub telefon komórkowy, aby zalogować się interaktywnie. Za pomocą przepływu kodu urządzenia, aplikacja uzyskuje tokeny za pośrednictwem dwuetapowego procesu, który jest przeznaczony dla tych urządzeń lub systemu operacyjnego. Przykładami takich aplikacji są aplikacje uruchamiane na narzędziach iOT lub wiersza polecenia (CLI). Chodzi o to, że:

1. Zawsze, gdy wymagane jest uwierzytelnianie użytkownika, aplikacja udostępnia kod dla użytkownika. Użytkownik jest proszony o użycie innego urządzenia, takiego jak smartfon podłączony do Internetu, aby przejść do adresu URL, na przykład. `https://microsoft.com/devicelogin` Następnie użytkownik jest monitowany o wprowadzenie kodu. To zrobić, strona internetowa prowadzi użytkownika przez normalne środowisko uwierzytelniania, który obejmuje monity zgody i uwierzytelniania wieloskładnikowego, jeśli to konieczne.

2. Po pomyślnym uwierzytelnieniu aplikacja wiersza polecenia odbiera wymagane tokeny za pośrednictwem kanału wstecz i używa ich do wykonywania wywołań interfejsu API sieci web, których potrzebuje.

### <a name="use-it"></a>Użyj go

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`zawiera metodę `AcquireTokenWithDeviceCode`o nazwie .

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Ta metoda przyjmuje jako parametry:

- Aby `scopes` zażądać tokenu dostępu dla.
- Wywołanie zwrotne, `DeviceCodeResult`które odbiera .

  ![Właściwości DeviceCodeResult](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

Poniższy przykładowy kod przedstawia najbardziej aktualny przypadek, z objaśnieniami rodzaju wyjątków, które można uzyskać i ich łagodzenia.

```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] Scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(Scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }         
}

private async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }
    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```
# <a name="java"></a>[Java](#tab/java)

Ten wyciąg pochodzi z [przykładów deweloperów MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```java
private static IAuthenticationResult acquireTokenDeviceCode() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) ->
                    System.out.println(deviceCode.message());

            DeviceCodeFlowParameters parameters =
                    DeviceCodeFlowParameters
                            .builder(SCOPE, deviceCodeConsumer)
                            .build();

            // Try to acquire a token via device code flow. If successful, you should see
            // the token and account information printed out to console, and the sample_cache.json
            // file should have been updated with the latest tokens.
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Ten wyciąg pochodzi z [przykładów deweloperów języka MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Note: If your device-flow app does not have any interactive ability, you can
#   completely skip the following cache part. But here we demonstrate it anyway.
# We now check the cache to see if we have some end users signed in before.
accounts = app.get_accounts()
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    print("Pick the account you want to use to proceed:")
    for a in accounts:
        print(a["username"])
    # Assuming the end user chose this one
    chosen = accounts[0]
    # Now let's try to find a token in cache for this account
    result = app.acquire_token_silent(config["scope"], account=chosen)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")

    flow = app.initiate_device_flow(scopes=config["scope"])
    if "user_code" not in flow:
        raise ValueError(
            "Fail to create device flow. Err: %s" % json.dumps(flow, indent=4))

    print(flow["message"])
    sys.stdout.flush()  # Some terminal needs this to ensure the message is shown

    # Ideally you should wait here, in order to save some unnecessary polling
    # input("Press Enter after signing in from another device to proceed, CTRL+C to abort.")

    result = app.acquire_token_by_device_flow(flow)  # By default it will block
        # You can follow this instruction to shorten the block time
        #    https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow
        # or you may even turn off the blocking behavior,
        # and then keep calling acquire_token_by_device_flow(flow) in your own customized loop
```

# <a name="macos"></a>[Macos](#tab/macOS)

Ten przepływ nie dotyczy systemu MacOS.

---

## <a name="file-based-token-cache"></a>Pamięć podręczna tokenów opartych na plikach

W MSAL.NET domyślnie udostępniana jest pamięć podręczna tokenów w pamięci.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>Serializacja jest konfigurowalna w aplikacjach klasycznych i aplikacjach internetowych systemu Windows lub interfejsach API sieci Web

W przypadku programu .NET Framework i .NET Core, jeśli nie zrobisz nic dodatkowego, pamięć podręczna tokenów w pamięci trwa przez cały czas trwania aplikacji. Aby zrozumieć, dlaczego serializacja nie jest dostarczana po wyjęciu z pudełka, należy pamiętać, że aplikacje msal .NET desktop lub .NET Core mogą być aplikacjami konsoli lub systemu Windows (które miałyby dostęp do systemu plików), *ale także* aplikacjami internetowymi lub internetowymi interfejsami API. Te aplikacje sieci web i interfejsy API sieci Web mogą używać niektórych określonych mechanizmów pamięci podręcznej, takich jak bazy danych, rozproszone pamięci podręczne i pamięci podręczne Redis. Aby mieć trwałą aplikację pamięci podręcznej tokenu na pulpicie platformy .NET lub .NET Core, należy dostosować serializację.

Klasy i interfejsy zaangażowane w serializacji pamięci podręcznej tokenów są następujące typy:

- ``ITokenCache``, który definiuje zdarzenia do subskrybowania żądań serializacji pamięci podręcznej tokenów oraz metody serializacji lub deserializacji pamięci podręcznej w różnych formatach (ADAL v3.0, MSAL 2.x i MSAL 3.x = ADAL v5.0).
- ``TokenCacheCallback``jest wywołaniem zwrotnym przekazanym do zdarzeń, dzięki czemu można obsługiwać serializacji. Będą one wywoływane z ``TokenCacheNotificationArgs``argumentami typu .
- ``TokenCacheNotificationArgs``tylko zawiera ``ClientId`` aplikację i odwołanie do użytkownika, dla którego token jest dostępny.

  ![Diagram serializacji pamięci podręcznej tokenu](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET tworzy pamięć podręczną tokenu dla Ciebie `IToken` i zapewnia pamięć podręczną podczas wywoływania aplikacji `UserTokenCache` i `AppTokenCache` właściwości. Nie powinieneś samodzielnie implementować interfejsu. Odpowiedzialność, podczas implementowania serializacji niestandardowej pamięci podręcznej tokenu, jest:
>
> - Reaguj na `BeforeAccess` zdarzenia `AfterAccess` lub ich odpowiednik *Asynna.* Pełnomocnik`BeforeAccess` jest odpowiedzialny za deserializacji pamięci podręcznej. Pełnomocnik `AfterAccess` jest odpowiedzialny za serializacji pamięci podręcznej.
> - Zrozumieć, że część tych zdarzeń przechowywania lub ładowania obiektów blob, które są przekazywane przez argument zdarzenia do dowolnego magazynu, który chcesz.

Strategie różnią się w zależności od tego, czy piszesz serializacji pamięci podręcznej tokenu dla publicznej aplikacji klienckiej, takich jak pulpit lub poufne aplikacji klienckiej, takich jak aplikacja sieci web lub interfejs API sieci Web lub aplikacji demona.

Od msal v2.x, masz kilka opcji. Wybór zależy od tego, czy chcesz serializować pamięć podręczną tylko do formatu MSAL.NET, który jest ujednoliconym formatem pamięci podręcznej, która jest wspólna dla MSAL, ale także na różnych platformach. Lub można również obsługiwać [serializacji](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) pamięci podręcznej starszych tokenów ADAL v3.

Dostosowywanie serializacji pamięci podręcznej tokenów w celu współużytkowania stanu SSO między ADAL.NET 3.x, ADAL.NET 5.x i MSAL.NET jest wyjaśnione w części przykładowego [typu active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

### <a name="simple-token-cache-serialization-msal-only"></a>Prosta serializacja pamięci podręcznej tokenów (tylko MSAL)

Poniższy przykład jest naiwną implementacją niestandardowej serializacji pamięci podręcznej tokenu dla aplikacji klasycznych. W tym miejscu pamięć podręczna tokenu użytkownika znajduje się w pliku w tym samym folderze co aplikacja.

Po utworzeniu aplikacji można włączyć serializację, wywołując ``TokenCacheHelper.EnableSerialization()`` `UserTokenCache`i przekazując aplikację .

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Ta klasa pomocnika wygląda jak fragment kodu:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Podgląd modułu szeregowego opartego na pamięci podręcznej tokenu jakości produktu dla publicznych aplikacji klienckich dla aplikacji klasycznych działających w systemach Windows, Mac i Linux jest dostępny w bibliotece open source [microsoft.Identity.Client.Extensions.Msal.](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) Można go dołączyć do aplikacji z następującego pakietu NuGet: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Zastrzeżenie: Biblioteka Microsoft.Identity.Client.Extensions.Msal jest rozszerzeniem o MSAL.NET. Klasy w tych bibliotekach może ich drogę do MSAL.NET w przyszłości, jak jest lub z przełomowych zmian.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Serializacja pamięci podręcznej dwóch tokenów (ujednolicona pamięć podręczna MSAL + ADAL v3)

Można zaimplementować serializację pamięci podręcznej tokenów w formacie Ujednoliconej pamięci podręcznej. Ten format jest wspólny dla ADAL.NET 4.x i MSAL.NET 2.x oraz innych listów MSALs tej samej generacji lub starszych na tej samej platformie. Zainspiruj się następującym kodem:

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Tym razem klasa pomocnika wygląda następująco:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   cache.SetBeforeAccess(BeforeAccessNotification);
   cache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wywoływanie internetowego interfejsu API z aplikacji klasycznej](scenario-desktop-call-api.md)
