---
title: Aplikacja internetowa wywołuje interfejsy API (kod Konfiguracja) - sieci web platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację sieci Web wywołuje interfejsy API (konfiguracji kodu aplikacji) sieci web
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
ms.openlocfilehash: bd7f393f889facf147cf25625d5c3b20f886ddf5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65784936"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Aplikacja sieci Web, że wywołania sieci web interfejsy API — Konfiguracja kodu

Jak widać na [scenariusz logowania użytkowników w aplikacji sieci Web](scenario-web-app-sign-user-overview.md), biorąc pod uwagę, że logowanie użytkownika jest delegowane do Open ID connect (OIDC) w oprogramowaniu pośredniczącym, aby punktu zaczepienia w górę w procesie OIDC. Sposobem wykonania tego zadania różni się zależnie od używanego (tutaj ASP.NET i ASP.NET Core), ale w końcu zostanie subskrybowanie zdarzeń OIDC oprogramowania pośredniczącego. Zasada jest to, że:

- Będziesz umożliwiają ASP.NET lub ASP.NET core żądania kodu autoryzacji. Wykonując spowoduje core ASP.NET/ASP.NET umożliwiają użytkownikowi, zaloguj się i wyrazić zgodę,
- Będziesz subskrybować odbiór kod autoryzacji przez aplikację internetową.
- Po odebraniu kodu uwierzytelniania użyjesz biblioteki MSAL do realizacji kodu i wynikowy tokenów dostępu, a następnie Odśwież magazyn tokenów w pamięci podręcznej tokenu. Z tego miejsca pamięci podręcznej może służyć w innych częściach aplikacji można uzyskać innych tokenów w trybie dyskretnym.

## <a name="libraries-supporting-web-app-scenarios"></a>Biblioteki wspiera scenariusze aplikacji sieci Web

Dostępne są następujące biblioteki obsługi przepływ kodu autoryzacji dla aplikacji sieci Web:

| Biblioteka MSAL | Opis |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Obsługiwane platformy to platformy .NET Framework i .NET Core (nie platformy uniwersalnej systemu Windows, platformy Xamarin.iOS i Xamarin.Android jako tych platform są używane do tworzenia aplikacji klienckich publiczny) |
| ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Programowanie w toku — w publicznej wersji zapoznawczej |
| ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Programowanie w toku — w publicznej wersji zapoznawczej |

## <a name="aspnet-core-configuration"></a>Konfiguracja platformy ASP.NET Core

W programie ASP.NET Core dziać rzeczy `Startup.cs` pliku. Można subskrybować `OnAuthorizationCodeReceived` otworzyć o identyfikatorze zdarzenia połączenia i z tego wydarzenia, wywołaj MSAL. Metoda firmy NET `AcquireTokenFromAuthorizationCode` została przechowywania w pamięci podręcznej tokenu, token dostępu dla żądanego zakresów i token odświeżania, który będzie używany do odświeżenia tokenu dostępu, gdy jest bliski wygaśnięcia, lub można pobrać tokenu w imieniu tego samego użytkownika , ale do innego zasobu.

Komentarze w kodzie poniżej pomoże poznać niektóre aspekty skomplikowanych tkania platformy MSAL.NET i ASP.NET Core

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

W programie ASP.NET Core tworzenie aplikacji poufne klienta używa informacji w obiekcie HttpContext. Ten element HttpContext zna adres URL dla aplikacji sieci Web i zalogowanego użytkownika (w `ClaimsPrincipal`). Używa również konfiguracja platformy ASP.NET Core, która zawiera sekcja "AzureAD" i która jest powiązana `_applicationOptions` strukturę danych. Na koniec aplikacja musi do obsługi pamięci podręcznej tokenu.

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

`AcquireTokenByAuthorizationCode` tak naprawdę umarza kod autoryzacji wymagane przez platformę ASP.NET i pobiera tokenów, które są dodawane do platformy MSAL.NET pamięci podręcznej tokenu użytkownika. W tym miejscu, są one następnie używane w kontrolerów platformy ASP.NET Core.

## <a name="aspnet-configuration"></a>Konfiguracja platformy ASP.NET

Sposób ASP.NET obsługuje rzeczy jest podobny, z tą różnicą, że konfiguracja OpenIdConnect i subskrypcję, której chcesz `OnAuthorizationCodeReceived` zdarzeń odbywa się w `App_Start\Startup.Auth.cs` pliku. Można znaleźć podobne pojęć związanych z tą różnicą, że w tym miejscu należy określić parametr RedirectUri w pliku konfiguracji, który jest nieco mniej niezawodne:

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
  // We'll inject our own issuer validation logic below.
  ValidateIssuer = false,
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

### <a name="msalnet-token-cache-for-a-aspnet-core-web-app"></a>Bufor platformy MSAL.NET tokenu dla aplikacji sieci Web platformy ASP.NET (rdzenie)

W aplikacji sieci web (lub internetowych interfejsów API jako środek fakcie) implementacja pamięci podręcznej tokenu różni się od implementacji pamięci podręcznej tokenu aplikacje klasyczne (które są często [plikowym](scenario-desktop-acquire-token.md#file-based-token-cache). Może używać sesji ASP.NET/ASP.NET Core lub w pamięci podręcznej Redis, lub bazy danych lub nawet usługi Azure Blob storage. W kodzie fragment kodu powyżej to jest obiekt `EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);` wywołania metody, powiązanej usługi cache service. Szczegółowych informacji o tym, co się dzieje, w tym miejscu wykracza poza zakres tego podręcznika scenariuszem, ale linki zostały podane poniżej.

> [!IMPORTANT]
> Bardzo ważne jest, aby weź pod uwagę to, że dla aplikacji sieci web i interfejsów API sieci web, powinien istnieć jeden pamięci podręcznej tokenu dla użytkownika (na konto). Należy serializować pamięci podręcznej tokenu dla każdego konta.

Przykłady dotyczące używania pamięci podręczne tokenu dla aplikacji sieci Web i interfejsy API sieci web są dostępne w [samouczek dotyczący aplikacji sieci Web platformy ASP.NET Core](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) w fazie [pamięci podręcznej tokenu 2-2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). W celu implementacji mają przyjrzeć się następującym folderze [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) w [microsoft uwierzytelniania rozszerzenia do dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) biblioteki (w [ Microsoft.Identity.Client.Extensions.Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) folderu.

## <a name="next-steps"></a>Kolejne kroki

W tym momencie po zalogowaniu użytkownika tokenu jest przechowywany w pamięci podręcznej tokenu. Zobaczmy, jak są one następnie używane w innych częściach aplikacji sieci Web.

> [!div class="nextstepaction"]
> [Zaloguj się do aplikacji sieci Web](scenario-web-app-call-api-sign-in.md)
