---
title: Aplikacja klasyczna, która wywołuje interfejsy API (pobierania tokenu dla aplikacji) — w sieci web platforma tożsamości usługi Microsoft
description: Dowiedz się, jak utworzyć aplikację, która wywołuje interfejsy API sieci web (Uzyskiwanie tokenu dla aplikacji |)
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d84801d6368bcc29f08145f190c2a07c64050ced
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795101"
---
# <a name="desktop-app-that-calls-web-apis---acquire-a-token"></a>Aplikacja klasyczna, która wywołuje interfejsy API — w sieci web uzyskać token

Po wbudowaniu możesz `IPublicClientApplication`, zostanie on użyty do uzyskania tokenu, który będzie następnie umożliwia wywoływanie internetowego interfejsu API.

## <a name="recommended-pattern"></a>Zalecany wzorzec

Interfejs API sieci web jest definiowany przez jego `scopes`. Niezależnie od komputera, należy podać w aplikacji jest wzorzec, który będziesz chciał użyć:

- Systematyczne próby pobrania tokenu z pamięci podręcznej tokenu przez wywołanie metody `AcquireTokenSilent`
- Jeśli to wywołanie nie powiedzie się, użyj `AcquireToken` przepływ, który chcesz użyć (w tym miejscu są reprezentowane przez `AcquireTokenXX`)

```CSharp
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

Oto szczegóły różne sposoby uzyskania tokenów w aplikacji klasycznej

## <a name="acquiring-a-token-interactively"></a>Uzyskiwanie tokenu interaktywnie

Poniższy przykład pokazuje minimalnej ilości kodu w celu pobrania tokenu interaktywnie odczytywanie profilu użytkownika przy użyciu programu Microsoft Graph.

```CSharp
string[] scopes = new string["user.read"];
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

### <a name="mandatory-parameters"></a>Obowiązkowe parametry

`AcquireTokenInteractive` ma tylko jeden parametr obowiązkowy ``scopes``, który zawiera wyliczenie ciągów, które definiują zakresów, dla których wymagany jest token. Jeśli token jest dla programu Microsoft Graph, wymaganych zakresów można znaleźć w dokumentacja interfejsu api z każdej Microsoft interfejsu API programu graph w sekcji o nazwie "Uprawnienia". Na przykład w celu [listy kontaktów użytkownika](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), będzie trzeba było używać zakresu "User.Read", "Contacts.Read". Zobacz też [odwołanie do uprawnień programu Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

W systemie Android, należy także określić działania nadrzędnego (przy użyciu `.WithParentActivityOrWindow`, patrz poniżej), aby token otrzymuje dla danego działania nadrzędnego po interakcji. Jeśli nie określisz, pojawi się wyjątek podczas wywoływania `.ExecuteAsync()`.

### <a name="specific-optional-parameters"></a>Określone parametry opcjonalne

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

Trwa interaktywny, interfejs użytkownika jest ważne. `AcquireTokenInteractive` ma jeden parametr opcjonalny określonych włączanie do określenia dla platform, obsługi interfejsu użytkownika obiektu nadrzędnego. Gdy są używane w przypadku aplikacji komputerowych `.WithParentActivityOrWindow` ma inny typ w zależności od platformy:

```CSharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Uwagi:

- W .NET Standard, oczekiwany `object` jest `Activity` w systemie Android `UIViewController` w systemach iOS, `NSWindow` na komputerze MAC, a `IWin32Window` lub `IntPr` na Windows.
- W Windows, należy wywołać `AcquireTokenInteractive` w interfejsie użytkownika wątku, aby osadzonej przeglądarce pobiera odpowiedniego kontekstu synchronizacji interfejsu użytkownika.  Nie wywołuje metody z wątku interfejsu użytkownika może spowodować komunikaty, aby nie pompy prawidłowo i/lub zakleszczenie przy użyciu interfejsu użytkownika. Jeden ze sposobów wywoływania biblioteki MSAL z wątku interfejsu użytkownika, jeśli nie jesteś w wątku interfejsu użytkownika już jest użycie `Dispatcher` na WPF.
- Jeśli używasz środowisku WPF można pobrać okna z formantu WPF, można użyć `WindowInteropHelper.Handle` klasy. Wywołanie jest następnie z formantu WPF (`this`):
  
  ```CSharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` Służy do sterowania interakcję z użytkownikiem, określając w wierszu

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Klasa definiuje następujące stałe:

- ``SelectAccount``: spowoduje to wymuszenie usługi STS do wyświetlenia okna dialogowego wyboru konta, które zostały zawierającej konta, dla których użytkownik ma sesję. Ta opcja jest przydatna, gdy deweloperzy aplikacji mają mieć możliwość wyboru różnych tożsamości użytkownika. Ta opcja dyski biblioteki MSAL do wysyłania ``prompt=select_account`` do dostawcy tożsamości. Ta opcja jest domyślnie i jest dobrym zadań związanych z udostępnianiem najlepsze możliwe wrażenia, na podstawie informacji dostępnych (konto, obecności sesję użytkownika i tak dalej. ...). Nie należy zmieniać jego chyba że masz powód, aby to zrobić.
- ``Consent``: umożliwia aplikacji dla deweloperów, aby wymusić użytkownika można monitów o wyrażenie zgody, nawet wtedy, gdy przed przyznaniu zgody. W tym przypadku wysyła MSAL `prompt=consent` do dostawcy tożsamości. Ta opcja może służyć w niektórych aplikacjach zabezpieczeń skupia się gdzie nadzoru organizacji wymaga, że użytkownik zobaczy okno dialogowe ze zgodą każdorazowo, gdy aplikacja jest używana.
- ``ForceLogin``: włącza deweloperów aplikacji do użytkownika zostanie wyświetlony monit o poświadczenia w usłudze, nawet jeśli nie ma być wymagane tym monitem użytkownika. Ta opcja może być przydatne w przypadku pobierania tokenu nie powiedzie się, aby umożliwić użytkownikowi ponowne-konta logowania. W tym przypadku wysyła MSAL `prompt=login` do dostawcy tożsamości. Ponownie zobaczyliśmy, używana w niektórych aplikacjach zabezpieczeń skupia się, gdzie nadzoru organizacji żądań, użytkownik relogs — w każdym uzyskaniu dostępu do określonych części aplikacji.
- ``Never`` (dla platformy .NET 4.5 i tylko WinRT), nie będzie monitował użytkownika, ale zamiast tego podejmie próbę użycia plików cookie przechowywane w widoku osadzone ukryte w sieci web (zobacz poniżej: Widoki sieci Web w platformy MSAL.NET). Przy użyciu tej opcji może zakończyć się niepowodzeniem, a w takim przypadku `AcquireTokenInteractive` spowoduje zgłoszenie wyjątku powiadamiania, wymagane jest interakcja interfejsu użytkownika i należy użyć innej `Prompt` parametru.
- ``NoPrompt``: Nie będzie wysyłać dowolnego wiersza do dostawcy tożsamości. Ta opcja jest tylko przydatne w przypadku zasad profilu edycji usługi Azure AD B2C (zobacz [specyfiki B2C](https://aka.ms/msal-net-b2c-specificities)).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Ten modyfikator jest używany w ramach zaawansowanego scenariusza, w której chcesz użytkowników do wstępnie wyrażenia zgody na kilka zasobów z wyprzedzeniem (i nie chcesz używać przyrostowe zgody, który jest zwykle używany przy użyciu platformy MSAL.NET / v2.0 platforma tożsamości firmy Microsoft). Aby uzyskać szczegółowe informacje, zobacz [porad: użytkownik z wyprzedzeniem zgody dla kilku zasobów](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources).

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi jest punktem rozszerzalności

`WithCustomWebUi` jest punktem rozszerzalności, który pozwala zapewnić własnego interfejsu użytkownika w aplikacjach klienckich publicznego, a aby umożliwić użytkownikowi przechodzą przez punkt końcowy/Authorize dostawcy tożsamości, dzięki czemu ich Zaloguj się i wyrazić zgodę. Platformy MSAL.NET można następnie wykorzystaj kod uwierzytelniania i uzyskać token. Na przykład jest używany w programie Visual Studio w zapewnienie elektronów aplikacji (na przykład opinii programu VS) zapewniają interakcji w sieci web, ale pozostawić ją do platformy MSAL.NET wykonywanie większości zadań. Umożliwia także go, jeśli chcesz udostępnić automatyzacji interfejsu użytkownika. W aplikacjach klienckich publicznych, platformy MSAL.NET używa standardu PKCE ([7636 RFC — dowód klucz wymiany kodu przez publiczny klientów uwierzytelniania OAuth](https://tools.ietf.org/html/rfc7636)) w celu zapewnienia, że bezpieczeństwo to: Tylko platformy MSAL.NET można zrealizować kod.

  ```CSharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="how-to-use-withcustomwebui"></a>Jak używać WithCustomWebUi

Aby można było używać `.WithCustomWebUI`, musisz:
  
  1. Implementowanie `ICustomWebUi` interfejsu (zobacz [tutaj](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). Zasadniczo musisz zaimplementować jedną metodę `AcquireAuthorizationCodeAsync` przyjmuje adres URL kod autoryzacji (obliczonej przez platformy MSAL.NET), przez użytkownika, przejdź do interakcji z dostawcy tożsamości, a następnie powrotu kopii adresu URL, za pomocą którego będzie dostawcy tożsamości wywołaniu implementacji Wstecz (w tym kod autoryzacji). Jeśli masz problemy, Twoja implementacja powinno zgłosić `MsalExtensionException` wyjątek dobrze współpracować z biblioteki MSAL.
  2. W swojej `AcquireTokenInteractive` wywołania, można użyć `.WithCustomUI()` modyfikator przekazywanie wystąpienia niestandardowego interfejsu użytkownika sieci web

     ```CSharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation---seleniumwebui"></a>Przykłady implementacji ICustomWebUi w automatyzacji testów - SeleniumWebUI

Zespół platformy MSAL.NET mają przepisany Nasze testy interfejsu użytkownika, aby korzystać z tego mechanizmu rozszerzalności. W przypadku, gdy chcesz wziąć może mieć przyjrzeć się [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) klasy w kodzie źródłowym platformy MSAL.NET

#### <a name="other-optional-parameters"></a>Inne parametry opcjonalne

Dowiedz się więcej o wszystkie inne opcjonalne parametry `AcquireTokenInteractive` z dokumentację referencyjną [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="integrated-windows-authentication"></a>Zintegrowane uwierzytelnianie Windows

Jeśli chcesz się zalogować użytkownika domeny na domeny lub usługi Azure AD przyłączone do komputera, należy użyć:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

### <a name="constraints"></a>Ograniczenia

- AcquireTokenByIntegratedWindowsAuth (IWA) jest tylko dla **Sfederowane** tylko użytkowników, którzy są, użytkownicy utworzone w usłudze Active Directory i wspierana przez usługę Azure Active Directory. Użytkownicy utworzeni bezpośrednio w usłudze AAD, bez zapasowego AD - **zarządzane** użytkownicy — nie można użyć tego przepływu uwierzytelniania. To ograniczenie nie ma wpływu na przepływ nazwy użytkownika i hasła.
- IWA jest dla aplikacji napisanych dla .NET Framework, .NET Core i platformy UWP
- IWA nie obejście uwierzytelniania Wieloskładnikowego (uwierzytelniania współczynnik multi). Skonfigurowanie uwierzytelniania Wieloskładnikowego IWA może zakończyć się niepowodzeniem, jeśli żądanie usługi MFA jest wymagana, ponieważ uwierzytelnianie wieloskładnikowe wymaga interakcji z użytkownikiem.
  > [!NOTE]
  > To zadanie jest trudne. Zintegrowane nie jest interakcyjny, ale 2FA wymaga interakcji ze strony użytkownika. Kontroluje, gdy dostawca tożsamości żąda uwierzytelniania 2fa przed uzyskaniem można wykonać, jest administratorem dzierżawy. Z naszej uwagi funkcji 2FA jest wymagana podczas logowania z innego kraju, gdy nie jest połączone za pośrednictwem sieci VPN z siecią firmową, a czasami nawet w przypadku połączenia za pośrednictwem sieci VPN. Nie będziesz już deterministyczne zestaw reguł, usługi Azure Active Directory używa sztucznej Inteligencji, aby stale Dowiedz się, czy konieczne jest 2FA. Należy rezerwowej w celu monitowania użytkownika (interakcyjnego uwierzytelniania lub urządzenie przepływ kodu), jeśli IWA zakończy się niepowodzeniem.

- Przekazano urzędowi `PublicClientApplicationBuilder` musi być:
  - ed dzierżawy (w postaci `https://login.microsoftonline.com/{tenant}/` gdzie `tenant` jest albo identyfikator guid reprezentujący identyfikator dzierżawy lub domeny skojarzone z dzierżawcą.
  - dla każdego roboczych i kont służbowych (`https://login.microsoftonline.com/organizations/`)

  > Nie są obsługiwane osobistych kont Microsoft (nie można użyć/Common lub /consumers dzierżaw)

- Ponieważ zintegrowane uwierzytelnianie Windows dyskretnej przepływu:
  - użytkownik aplikacji musi wcześniej wyrażono zgodę na używanie aplikacji
  - lub Administrator dzierżawy musi wcześniej wyrażono zgodę na wszystkich użytkowników w dzierżawie korzystanie z aplikacji.
  - Innymi słowy:
    - albo jako deweloper naciśniętych **Grant** przycisku w witrynie Azure portal dla siebie
    - lub administratora dzierżawy nacisnął **Grant/revoke zgody administratora {domenie dzierżawy}** znajdujący się w **uprawnienia do interfejsu API** kartę rejestracji aplikacji (zobacz [Dodaj uprawnienia do dostęp do interfejsów API sieci web](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis))
    - lub został podany sposób użytkownikom na wyrażanie zgody aplikacji (zobacz [żądanie zgody użytkownika](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent))
    - lub został podany sposób administrator dzierżawy wyrazić zgodę dla aplikacji (zobacz [zgody administratora](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant))

- Ten przepływ jest włączona dla klasycznych na platformie .net, .net core i Windows Uniwersalnej aplikacji. Na platformie .NET core tylko przeciążenie nazwa użytkownika jest dostępna, ponieważ platformy .NET Core nie może zapytać nazwa użytkownika do systemu operacyjnego.
  
Aby uzyskać więcej informacji na temat zgody, zobacz [v2.0 uprawnienia i zgody](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

### <a name="how-to-use-it"></a>Jak z niej korzystać

Zazwyczaj potrzebne tylko jeden parametr (`scopes`). Jednak zależnie od tego, sposób Windows administrator ma ustawienia zasad, może być możliwe, aplikacje na komputerze z systemem windows nie mogą wyszukać zalogowanego użytkownika. W takim przypadku należy użyć drugiej metody `.WithUsername()` i przekazać nazwę użytkownika zalogowanego użytkownika w formacie UPN - `joe@contoso.com`.

Poniższy przykład przedstawia informacje o najnowszych przypadek, wraz z wyjaśnieniem rodzaju wyjątki, które mogą uzyskać i ich ograniczenia

```CSharp
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

Aby uzyskać listę możliwych modyfikatorów AcquireTokenByIntegratedWindowsAuthentication zobacz [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="username--password"></a>Nazwa użytkownika / hasło

Można również uzyskać token, podając nazwę użytkownika i hasło. Ten przepływ jest ograniczone i nie jest zalecane, ale są nadal używać przypadki, gdzie jest to konieczne.

### <a name="this-flow-isnt-recommended"></a>Ten przepływ nie jest zalecane.

Ten przepływ jest **niezalecane** ponieważ monitowania użytkownika o ich hasła aplikacji nie jest bezpieczne. Aby uzyskać więcej informacji na temat tego problemu, zobacz [w tym artykule](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). Preferowany przepływ w celu uzyskania tokenu w trybie dyskretnym na komputerach przyłączonych do domeny Windows jest [zintegrowane uwierzytelnianie Windows](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). W przeciwnym razie możesz również użyć [przepływ kodu urządzenia](https://aka.ms/msal-net-device-code-flow)

> [!NOTE] 
> Mimo że jest to przydatne w niektórych przypadkach (scenariuszy DevOps), jeśli chcesz użyć nazwy użytkownika i hasła w interakcyjnych scenariuszy, w którym udostępniać swoje onw interfejsu użytkownika, należy tak naprawdę rozważać jak przenieść poza. Przy użyciu nazwy użytkownika i hasła możesz są zapewniając telefoniczny kilka rzeczy:
>
> - podstawowe dzierżaw nowoczesnych tożsamości: hasło pobiera połowy, odtworzyć. Ponieważ mamy tę koncepcję udziału klucz tajny, który może zostać przechwycona.
> Jest to niezgodne z bez hasła.
> - Użytkownicy, którzy muszą przeprowadzić uwierzytelnianie wieloskładnikowe nie będzie mógł zalogować (zgodnie z nie zachodzi żadna interakcja)
> - Użytkownicy nie będą mieć możliwość logowania jednokrotnego

### <a name="constraints"></a>Ograniczenia

Ponadto obowiązują następujące ograniczenia:

- Przepływ nazwy użytkownika/hasła nie jest zgodny z dostępu warunkowego i uwierzytelniania wieloskładnikowego: W rezultacie jeśli aplikacja działa w dzierżawie usługi Azure AD, w którym administrator dzierżawy wymaga uwierzytelniania wieloskładnikowego, nie możesz użyć tego przepływu. Wiele organizacji to zrobić.
- Działa tylko w przypadku pracy i konta służbowe (nie MSA)
- Przepływ jest dostępne w klasycznych na platformie .net i .net core, ale nie w platformy uniwersalnej systemu Windows

### <a name="b2c-specifics"></a>Charakterystyka B2C

[Więcej informacji o użyciu ROPC z usługą B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="how-to-use-it"></a>Jak z niej korzystać?

`IPublicClientApplication`zawiera metodę `AcquireTokenByUsernamePassword`

Poniższy przykład przedstawia uproszczoną przypadek

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
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

Poniższy przykład przedstawia informacje o najnowszych przypadek, wraz z wyjaśnieniem rodzaju wyjątki, które mogą uzyskać i ich ograniczenia

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
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

Aby uzyskać szczegółowe informacje na temat modyfikatorami, które mogą być stosowane do `AcquireTokenByUsernamePassword`, zobacz [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="command-line-tool-without-web-browser"></a>Narzędzie wiersza polecenia (bez przeglądarki sieci web)

### <a name="device-code-flow-why-and-how"></a>Kod urządzenia przepływu Dlaczego? i jak?

Jeśli piszesz narzędzie wiersza polecenia (nie ma formantów sieci Web), a nie może lub nie chcesz używać poprzedniej przepływów, będziesz musiał użyć `AcquireTokenWithDeviceCode`.

Uwierzytelnianie interakcyjne z usługą Azure AD wymaga przeglądarki sieci web (szczegółowe informacje można znaleźć [użycia przeglądarki sieci web](https://aka.ms/msal-net-uses-web-browser)). Jednak do uwierzytelniania użytkowników na urządzeniach lub systemy operacyjne, które nie udostępniają przeglądarki sieci Web, przepływ kodu urządzenia umożliwia użytkownikowi używanie innego urządzenia (na przykład innego komputera lub telefon komórkowy) do podpisywania interaktywnie. Za pomocą przepływu kodu urządzenia, aplikacji uzyskuje tokenów przejdziesz do dwuetapowego procesu, szczególnie zaprojektowana z myślą o tych urządzeń/OS. Przykładami takich aplikacji są aplikacje uruchomione na iOT lub narzędzia wiersza polecenia (CLI). Chodzi o to, że:

1. Zawsze wtedy, gdy wymagane jest uwierzytelnianie użytkownika, aplikacja zawiera kod i prosi użytkownika o za pomocą innego urządzenia (np. smartfony podłączonej do Internetu) przejdź do adresu URL (na przykład `https://microsoft.com/devicelogin`), gdzie użytkownicy będą monitowani o podanie kodu. Czy to zrobione, strony sieci web przeprowadzi użytkownika przez środowisko normalnego uwierzytelniania, w tym monitów o wyrażenie zgody i uwierzytelniania wieloskładnikowego, jeśli to konieczne.

2. Po pomyślnym uwierzytelnieniu czyli aplikację wiersza polecenia otrzyma wymagane tokenów za pośrednictwem kanału zwrotnego i użyje do wykonywania wywołań interfejsu API sieci web, których potrzebuje.

### <a name="code"></a>Kod

`IPublicClientApplication`zawiera metodę o nazwie `AcquireTokenWithDeviceCode`

```CSharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Ta metoda przyjmuje jako parametry:

- `scopes` Do żądania tokenu dostępu dla
- Wywołanie zwrotne, które będą otrzymywać `DeviceCodeResult`

  ![image](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

Następujący przykładowy kod przedstawia informacje o najnowszych przypadek, wraz z wyjaśnieniem rodzaju wyjątki, które mogą uzyskać i ich ograniczenia.

```CSharp
static async Task<AuthenticationResult> GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication pca = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 AuthenticationResult result = null;
 var accounts = await app.GetAccountsAsync();

 // All AcquireToken* methods store the tokens in the cache, so check the cache first
 try
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
       .ExecuteAsync();
 }
 catch (MsalUiRequiredException ex)
 {
  // A MsalUiRequiredException happened on AcquireTokenSilent.
  // This indicates you need to call AcquireTokenInteractive to acquire a token
  System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
 }

 try
 {
  result = await app.AcquireTokenWithDeviceCode(scopes,
      deviceCodeCallback =>
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
  // If you use a CancellationToken, and call the Cancel() method on it, then this may be triggered
  // to indicate that the operation was cancelled.
  // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
  // for more detailed information on how C# supports cancellation in managed threads.
 }
 catch (MsalClientException ex)
 {
  // Verification code expired before contacting the server
  // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
  // call to `AcquireTokenWithDeviceCode` is not cancelled in between
 }
}
```

## <a name="file-based-token-cache"></a>Pamięć podręczną tokenu opartą na plikach

W platformy MSAL.NET domyślnie znajduje się w pamięci podręcznej tokenu.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-appsweb-apis"></a>Serializacja jest możliwe do dostosowania w Windows aplikacji klasycznych i sieci web apps/interfejsów API sieci web

W przypadku platformy .NET Framework i .NET core Jeśli nie ma niczego, ważny w pamięci podręcznej tokenu na czas trwania aplikacji. Zrozumienie, dlaczego serializacji nie podano gotowych, należy pamiętać MSAL .NET desktop/podstawowych aplikacji może być konsoli lub aplikacji Windows, (które będzie miała dostępu do systemu plików), **, ale także** aplikacji sieci Web lub interfejs API sieci web. Te interfejsy API sieci web i aplikacji sieci Web może używać niektórych mechanizmów określonych pamięci podręcznej, takich jak bazy danych, rozproszonej pamięci podręcznej, pamięci podręczne redis Cache i tak dalej. Aby mieć gotową aplikację do trwałego pamięci podręcznej tokenu w klasycznych na platformie .NET lub Core, należy dostosować serializacji.

Klasy i interfejsy, zaangażowany w pamięci podręcznej tokenu serializacji są następujące typy:

- ``ITokenCache``, który definiuje zdarzenia do subskrybowania żądania serializacji w pamięci podręcznej tokenu, a także metody, aby serializować lub deserializować pamięci podręcznej w różnych formatach (v3.0 biblioteki ADAL, biblioteka MSAL 2.x i Biblioteka MSAL 3.x = v5.0 biblioteki ADAL)
- ``TokenCacheCallback`` Wywołanie zwrotne przechodzi do zdarzenia, aby można było obsłużyć serializacji. będzie można wywołać z argumentami typu ``TokenCacheNotificationArgs``.
- ``TokenCacheNotificationArgs`` zapewnia tylko ``ClientId`` aplikacji i odwołania do użytkownika, dla którego token jest dostępna

  ![image](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> Platformy MSAL.NET tworzy token pamięci podręczne i zapewnia `IToken` pamięci podręcznej podczas wywoływania aplikacji `GetUserTokenCache` i `GetAppTokenCache` metody. Nie powinien implementować interfejs samodzielnie. Odpowiedzialność, podczas implementowania serializacji niestandardowej pamięci podręcznej tokenu, jest:
>
> - Reagowanie na `BeforeAccess` i `AfterAccess` "miara events" (lub ich *Async* odpowiednika). `BeforeAccess` Delegat jest odpowiedzialna deserializować pamięci podręcznej, natomiast `AfterAccess` jeden jest odpowiedzialny za serializacji w pamięci podręcznej.
> - Części te zdarzenia magazynu lub obiektów blob, które są przekazywane za pośrednictwem argumentu zdarzenia można niezależnie od magazynu, które chcesz załadować.

Strategie różnią się w zależności od Jeśli piszesz serializacji pamięci podręcznej tokenu dla aplikacji klienckiej publiczny (wersja klasyczna) lub aplikacji poufne klienta (sieci web/sieci web aplikacji interfejsu API, aplikacji demona).

Ponieważ biblioteki MSAL V2.x masz kilka opcji, aby serializować pamięci podręcznej tylko do formatu platformy MSAL.NET (jednolitym formacie pamięci podręcznej, które często za pomocą biblioteki MSAL, ale także na platformach), czy też mają być obsługiwane w zależności od [starszych](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) Serializacja pamięć podręczną tokenu biblioteki ADAL V3.

Dostosowanie serializacji pamięci podręcznej tokenu, aby udostępnianie stanu logowania jednokrotnego między ADAL.NET 3.x, ADAL.NET 5.x i platformy MSAL.NET zostało wyjaśnione w części z poniższego przykładu: [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)

### <a name="simple-token-cache-serialization-msal-only"></a>Serializacja prostą pamięci podręcznej tokenu (tylko MSAL)

Poniżej przedstawiono przykład z implementacją naiwni niestandardowej serializacji pamięci podręcznej tokenu dla aplikacji klasycznych. Oto pamięci podręcznej tokenu użytkownika w pliku w tym samym folderze co aplikacja.

Po skompilowaniu aplikacji włączyć serializacji przez wywołanie ``TokenCacheHelper.EnableSerialization()`` przekazanie aplikacji `UserTokenCache`

```CSharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Ta klasa pomocy będzie wyglądać jak poniższy fragment kodu:

```CSharp
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

Podgląd produktu jakości tokenu pamięć podręczną opartą na plikach serializatora dla aplikacji klienckich publicznej (dla aplikacji klasycznych, systemem Windows, Mac i linux) jest dostępny z [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) Biblioteka typu open source. Można dołączyć go w swoich aplikacjach z następujący pakiet nuget: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Wykluczenie. Biblioteka Microsoft.Identity.Client.Extensions.Msal jest rozszerzeniem za pośrednictwem platformy MSAL.NET. Klasy w tych bibliotek mogą przedostają się do platformy MSAL.NET w przyszłości, bez zmian lub z ostatniej zmiany.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Podwójna pamięci podręcznej tokenu serializacji (pamięć podręczną biblioteki MSAL unified + biblioteki ADAL V3)

Jeśli chcesz zaimplementować serializacji pamięci podręcznej tokenu zarówno za pomocą ujednoliconej w pamięci podręcznej formatu (często ADAL.NET 4.x i platformy MSAL.NET 2.x oraz z innych MSALs generacji odpowiadającej generacji lub starsze, na jednej platformie), użytkownik może daj się zainspirować następujący kod :

```CSharp
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

Tym razem klasy pomocnika wygląda podobnie do poniższego kodu:

```CSharp
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
   usertokenCache = cache;
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   usertokenCache.SetBeforeAccess(BeforeAccessNotification);
   usertokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Token cache
  /// </summary>
  static ITokenCache usertokenCache;

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
> [Wywoływanie interfejsu web API z aplikacji klasycznej](scenario-desktop-call-api.md)
