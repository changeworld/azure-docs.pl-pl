---
title: Aplikacja sieci Web, która wywołuje interfejsy API sieci Web (Konfiguracja kodu) — Microsoft Identity platform
description: Dowiedz się, jak utworzyć aplikację sieci Web, która wywołuje interfejsy API sieci Web (Konfiguracja kodu aplikacji)
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15c12aebccf34957db8442034ebbcd6ac7c107e1
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276732"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Aplikacja sieci Web, która wywołuje interfejsy API sieci Web — konfiguracja kodu

Jak widać w scenariuszu logowania [aplikacji sieci Web](scenario-web-app-sign-user-overview.md), jeśli użytkownik zalogowany jest delegowany do oprogramowania pośredniczącego Open ID Connect (OIDC), chcesz podpiąć w procesie OIDC. Sposób, w jaki można to zrobić, zależy od używanej platformy (w tym miejscu ASP.NET i ASP.NET Core), ale na końcu zasubskrybujesz zdarzenia pośredniczące OIDC. Zasada polega na tym, że:

- ASP.NET lub ASP.NET podstawowe żądanie kodu autoryzacji. Dzięki temu ASP.NET/ASP.NET rdzeń zezwoli użytkownikowi na logowanie i zgodę,
- Zasubskrybujesz odbieranie kodu autoryzacji przez aplikację internetową.
- Po odebraniu kodu uwierzytelniania należy użyć bibliotek MSAL do zrealizowania kodu i uzyskanych tokenów dostępu i odświeżenia magazynu tokenów w pamięci podręcznej tokenów. Z tego miejsca pamięć podręczna może być używana w innych częściach aplikacji, aby uzyskać inne tokeny dyskretnie.

## <a name="libraries-supporting-web-app-scenarios"></a>Biblioteki obsługujące scenariusze aplikacji sieci Web

Biblioteki obsługujące przepływ kodu autoryzacji dla Web Apps są następujące:

| Biblioteka MSAL | Opis |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Obsługiwane platformy to .NET Framework i platformy .NET Core (nie platformy UWP, Xamarin. iOS i Xamarin. Android, ponieważ te platformy są używane do tworzenia publicznych aplikacji klienckich). |
| ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL. Python | Opracowywanie w toku — w publicznej wersji zapoznawczej |
| ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Opracowywanie w toku — w publicznej wersji zapoznawczej |

## <a name="aspnet-core-configuration"></a>Konfiguracja ASP.NET Core

W ASP.NET Core w `Startup.cs` pliku występują problemy. Chcesz subskrybować `OnAuthorizationCodeReceived` zdarzenie połączenia Open ID i z tego zdarzenia, wywołaj MSAL. Metoda `AcquireTokenFromAuthorizationCode` sieci, która ma wpływ na przechowywanie w pamięci podręcznej tokenów, token dostępu dla żądanych zakresów i token odświeżania, który zostanie użyty do odświeżenia tokenu dostępu, gdy jest bliski wygaśnięcia lub aby uzyskać token w imieniu tego samego użytkownika , ale dla innego zasobu.

Komentarze w kodzie poniżej będą pomocne w zrozumieniu niektórych z lew MSAL.NET i ASP.NET Core. Pełne szczegóły znajdują się w [samouczku przyrostowym aplikacji sieci Web ASP.NET Core, rozdział 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)

```CSharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Response type. We ask ASP.NET to request an Auth Code, and an IDToken
   options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

   // This "offline_access" scope is needed to get a refresh token when users sign in with
   // their Microsoft personal accounts
   // (it's required by MSAL.NET and automatically provided by Azure AD when users
   // sign in with work or school accounts, but not with their Microsoft personal accounts)
   options.Scope.Add(OidcConstants.ScopeOfflineAccess);
   options.Scope.Add("user.read"); // for instance

   // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
   // where it will be usable from Controllers later (through the TokenAcquisition service)
   var handler = options.Events.OnAuthorizationCodeReceived;
   options.Events.OnAuthorizationCodeReceived = async context =>
   {
    // As AcquireTokenByAuthorizationCode is asynchronous we want to tell ASP.NET core
    // that we are handing the code even if it's not done yet, so that it does 
    // not concurrently call the Token endpoint.
    context.HandleCodeRedemption();

    // Call MSAL.NET AcquireTokenByAuthorizationCode
    var application = BuildConfidentialClientApplication(context.HttpContext,
                                                         context.Principal);
    var result = await application.AcquireTokenByAuthorizationCode(scopes.Except(scopesRequestedByMsalNet), context.ProtocolMessage.Code)
                                  .ExecuteAsync();

    // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it
    // and will not send the OAuth 2.0 request in case a further call to
    // AcquireTokenByAuthorizationCodeAsync in the future for incremental consent 
    // (getting a code requesting more scopes)
    // Share the ID Token so that the identity of the user is known in the application (in 
    // HttpContext.User)
    context.HandleCodeRedemption(null, result.IdToken);

    // Call the previous handler if any
    await handler(context);
   };
```

W ASP.NET Core tworzenia poufnej aplikacji klienckiej używa informacji znajdujących się w elemencie HttpContext. Ten element HttpContext wie o adresie URL aplikacji sieci Web oraz zalogowanym użytkowniku (w a `ClaimsPrincipal`). Używa również konfiguracji ASP.NET Core, która zawiera sekcję "AzureAD" i która jest powiązana ze `_applicationOptions` strukturą danych. Na koniec aplikacja musi obsługiwać pamięć podręczną tokenów.

```CSharp
/// <summary>
/// Creates an MSAL Confidential client application
/// </summary>
/// <param name="httpContext">HttpContext associated with the OIDC response</param>
/// <param name="claimsPrincipal">Identity for the signed-in user</param>
/// <returns></returns>
private IConfidentialClientApplication BuildConfidentialClientApplication(HttpContext httpContext, ClaimsPrincipal claimsPrincipal)
{
 var request = httpContext.Request;

 // Find the URI of the application)
 string currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, azureAdOptions.CallbackPath ?? string.Empty);

 // Updates the authority from the instance (including national clouds) and the tenant
 string authority = $"{azureAdOptions.Instance}{azureAdOptions.TenantId}/";

 // Instantiates the application based on the application options (including the client secret)
 var app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
               .WithRedirectUri(currentUri)
               .WithAuthority(authority)
               .Build();

 // Initialize token cache providers. In the case of Web applications, there must be one
 // token cache per user (here the key of the token cache is in the claimsPrincipal which
 // contains the identity of the signed-in user)
 if (this.UserTokenCacheProvider != null)
 {
  this.UserTokenCacheProvider.Initialize(app.UserTokenCache, httpContext, claimsPrincipal);
 }
 if (this.AppTokenCacheProvider != null)
 {
  this.AppTokenCacheProvider.Initialize(app.AppTokenCache, httpContext);
 }
 return app;
}
```

`AcquireTokenByAuthorizationCode`naprawdę realizuje kod autoryzacji wymagany przez ASP.NET i pobiera tokeny dodawane do pamięci podręcznej tokenów użytkownika MSAL.NET. Następnie są one używane w kontrolerach ASP.NET Core.

## <a name="aspnet-configuration"></a>Konfiguracja ASP.NET

Sposób, w jaki ASP.NET obsługuje rzeczy, jest podobny, z tą różnicą, że konfiguracja OpenIdConnect `OnAuthorizationCodeReceived` i subskrypcja zdarzenia wystąpi `App_Start\Startup.Auth.cs` w pliku. Podobne koncepcje znajdziesz w tym miejscu, z wyjątkiem sytuacji, w których należy określić RedirectUri w pliku konfiguracji, co jest nieco mniej niezawodne:

```CSharp
private void ConfigureAuth(IAppBuilder app)
{
 app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

 app.UseCookieAuthentication(new CookieAuthenticationOptions { });

 app.UseOpenIdConnectAuthentication(
 new OpenIdConnectAuthenticationOptions
 {
  Authority = Globals.Authority,
  ClientId = Globals.ClientId,
  RedirectUri = Globals.RedirectUri,
  PostLogoutRedirectUri = Globals.RedirectUri,
  Scope = Globals.BasicSignInScopes, // a basic set of permissions for user sign in & profile access
  TokenValidationParameters = new TokenValidationParameters
  {
   NameClaimType = "name",
  },
  Notifications = new OpenIdConnectAuthenticationNotifications()
  {
   AuthorizationCodeReceived = OnAuthorizationCodeReceived,
  }
 });
}
```

```CSharp
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
{
 // Upon successful sign in, get & cache a token using MSAL
 string userId = context.AuthenticationTicket.Identity.FindFirst(ClaimTypes.NameIdentifier).Value;
 IConfidentialClientApplication clientapp;
 clientApp = ConfidentialClientApplicationBuilder.Create(Globals.ClientId)
                  .WithClientSecret(Globals.ClientSecret)
                  .WithRedirectUri(Globals.RedirectUri)
                  .WithAuthority(new Uri(Globals.Authority))
                  .Build();

  EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);

 AuthenticationResult result = await clientapp.AcquireTokenByAuthorizationCode(scopes, context.Code)
                                              .ExecuteAsync();
}
```

Na koniec zamiast wpisu tajnego klienta poufna aplikacja kliencka może również potwierdzić swoją tożsamość przy użyciu certyfikatu klienta lub potwierdzenia klienta.
Korzystanie z zatwierdzeń klientów jest zaawansowanym scenariuszem, szczegółowym w przypadku potwierdzeń [klienta](msal-net-client-assertions.md)

### <a name="msalnet-token-cache-for-a-aspnet-core-web-app"></a>Pamięć podręczna tokenów MSAL.NET dla aplikacji sieci Web ASP.NET (Core)

W przypadku aplikacji sieci Web (lub interfejsów API sieci Web w rzeczywistości) implementacja pamięci podręcznej tokenów różni się od implementacji pamięci podręcznej tokenów aplikacji klasycznych (często [opartych na plikach](scenario-desktop-acquire-token.md#file-based-token-cache)). Może korzystać z sesji ASP.NET/ASP.NET Core lub pamięci podręcznej Redis lub bazy danych, a nawet magazynu obiektów blob platformy Azure. W powyższym fragmencie kodu jest to obiekt `EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);` wywołania metody, które wiąże usługę pamięci podręcznej. Szczegółowe informacje o tym, co się dzieje, znajdują się poza zakresem tego przewodnika scenariusza, ale linki są podane poniżej.

> [!IMPORTANT]
> Istotną kwestią jest to, że w przypadku aplikacji sieci Web i interfejsów API sieci Web powinna istnieć jedna pamięć podręczna tokenów dla każdego użytkownika (na konto). Należy serializować pamięć podręczną tokenów dla każdego konta.

Przykłady użycia pamięci podręcznej tokenów dla aplikacji sieci Web i interfejsów API sieci Web są dostępne w [samouczku ASP.NET Core aplikacji sieci](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) Web w fazie [2-2 tokena w pamięci](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)podręcznej. W przypadku implementacji zapoznaj się z następującym folderem [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) w bibliotece [Microsoft-Authentication-Extensions-for-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) (w folderze [Microsoft. Identity. Client. Extensions. Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) ).

## <a name="next-steps"></a>Następne kroki

W tym momencie podczas logowania użytkownika w pamięci podręcznej tokeny jest przechowywany token. Zobaczmy, w jaki sposób jest używany w innych częściach aplikacji sieci Web.

> [!div class="nextstepaction"]
> [Zaloguj się do aplikacji sieci Web](scenario-web-app-call-api-sign-in.md)
