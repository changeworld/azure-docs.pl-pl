---
title: Konfigurowanie aplikacji sieci Web, która wywołuje interfejsy API sieci Web — Microsoft Identity platform | Azure
description: Dowiedz się, jak skonfigurować kod aplikacji sieci Web, która wywołuje interfejsy API sieci Web
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7bf5256390961f4d469aa70be4b0b0c1069db4cf
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701692"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Aplikacja sieci Web, która wywołuje interfejsy API sieci Web — konfiguracja kodu

Jak widać w [scenariuszu logowania w aplikacji sieci Web](scenario-web-app-sign-user-overview.md), aplikacja sieci Web używa [przepływu kodu autoryzacji](v2-oauth2-auth-code-flow.md) protokołu OAuth 2.0, aby zalogować użytkownika. Ten przepływ znajduje się w dwóch częściach:

1. Zażądaj kodu autoryzacji. Ta część deleguje do programu Microsoft Identity platform prywatny dialog z użytkownikiem. Użytkownik loguje się i wyraża zgodę na korzystanie z interfejsów API sieci Web. Po pomyślnym zakończeniu tego prywatnego okna dialogowego aplikacja odbiera kod autoryzacji w identyfikatorze URI przekierowania.
1. Zażądaj tokenu dostępu dla interfejsu API przez zrealizowanie kodu autoryzacji.

[Scenariusz logowania aplikacji sieci Web](scenario-web-app-sign-user-overview.md) dotyczy tylko pierwszego etapu. Dowiedz się tutaj, jak zmodyfikować internetowy interfejs API, który loguje użytkowników, aby teraz wywołuje interfejsy API sieci Web.

## <a name="libraries-supporting-web-app-scenarios"></a>Biblioteki obsługujące scenariusze aplikacji sieci Web

Biblioteki obsługujące przepływ kodu autoryzacji dla usługi Web Apps to:

| Biblioteka MSAL | Opis |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Obsługiwane platformy to .NET Framework i platformy .NET Core (nie platformy UWP, Xamarin. iOS i Xamarin. Android, ponieważ te platformy są używane do tworzenia publicznych aplikacji klienckich). |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Obsługa aplikacji sieci Web w języku Python |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Obsługa aplikacji sieci Web w języku Java |

Wybierz kartę odpowiadającą używanej platformie:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

W przypadku, gdy logowanie użytkownika zostanie delegowane do oprogramowania pośredniczącego Open ID Connect (OIDC), chcesz utworzyć punkt zaczepienia w procesie OIDC. Metoda ta jest inna w zależności od używanej platformy.
W przypadku ASP.NET Core zasubskrybujesz zdarzenia OIDC oprogramowania pośredniczącego. Zasada polega na tym, że:

- Umożliwisz ASP.NET podstawowe żądanie kodu autoryzacji za pomocą oprogramowania pośredniczącego Open ID Connect. Dzięki temu ASP.NET/ASP.NET rdzeń zezwoli użytkownikowi na logowanie i zgodę,
- Zasubskrybujesz odbieranie kodu autoryzacji przez aplikację internetową. Ta subskrypcja jest wykonywana przez C# delegata.
- Po odebraniu kodu uwierzytelniania należy użyć bibliotek MSAL do zrealizowania kodu, a uzyskane tokeny dostępu i tokeny odświeżania są przechowywane w pamięci podręcznej tokenów. Z tego miejsca można korzystać z pamięci podręcznej w innych częściach aplikacji, na przykład w kontrolerach, aby uzyskać inne tokeny w trybie dyskretnym.

Fragmenty kodu w tym artykule i następujące zostały wyodrębnione z [samouczka ASP.NET Core aplikacji sieci Web, rozdział 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph). Warto zapoznać się z tym samouczkiem, aby uzyskać szczegółowe informacje dotyczące implementacji.

> [!NOTE]
> Aby zrozumieć w pełni Poniższe fragmenty kodu, należy zapoznać się z tematami [ASP.NET Core Fundamentals](https://docs.microsoft.com/aspnet/core/fundamentals), a w szczególności [iniekcją zależności](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) i [opcjami](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

W przypadku, gdy logowanie użytkownika zostanie delegowane do oprogramowania pośredniczącego Open ID Connect (OIDC), chcesz utworzyć punkt zaczepienia w procesie OIDC. Metoda ta jest inna w zależności od używanej platformy.
W przypadku ASP.NET zasubskrybujesz zdarzenia pośredniczące OIDC. Zasada polega na tym, że:

- Umożliwisz ASP.NET podstawowe żądanie kodu autoryzacji za pomocą oprogramowania pośredniczącego Open ID Connect. Dzięki temu ASP.NET/ASP.NET rdzeń zezwoli użytkownikowi na logowanie i zgodę,
- Zasubskrybujesz odbieranie kodu autoryzacji przez aplikację internetową. To jest C# delegat.
- Po odebraniu kodu uwierzytelniania będziesz używać bibliotek MSAL do zrealizowania kodu. Tokeny dostępu i tokeny odświeżania są przechowywane w pamięci podręcznej tokenów. Z tego miejsca można korzystać z pamięci podręcznej w innych częściach aplikacji, na przykład w kontrolerach, aby uzyskać inne tokeny w trybie dyskretnym.

Fragmenty kodu w tym artykule i poniższe elementy są wyodrębniane z [przykładowej aplikacji sieci Web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Warto zapoznać się z tym przykładem, aby uzyskać szczegółowe informacje dotyczące implementacji.

# <a name="javatabjava"></a>[Java](#tab/java)

Fragmenty kodu w tym artykule i następujące zostały wyodrębnione z [aplikacji sieci Web Java wywołującej](https://github.com/Azure-Samples/ms-identity-java-webapp) przykład aplikacji sieci Web programu Microsoft Graph MSAL Java.
Przykład umożliwia obecnie MSAL Java Tworzenie adresu URL kodu autoryzacji i obsługę nawigacji do platformy tożsamości firmy Microsoft autoryzuje punkt końcowy. Istnieje również możliwość użycia zabezpieczeń przebiegu w celu zalogowania użytkownika. Warto zapoznać się z tym przykładem, aby uzyskać szczegółowe informacje dotyczące implementacji.

# <a name="pythontabpython"></a>[Python](#tab/python)

Fragmenty kodu w tym artykule i poniższe zostały wyodrębnione z [aplikacji sieci Web w języku Python wywołującej program Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) MSAL. Przykład aplikacji sieci Web w języku Python.
Przykład umożliwia obecnie MSAL. Język Python tworzy adres URL kodu autoryzacji i obsługuje nawigację do punktu końcowego autoryzacji platformy tożsamości firmy Microsoft. Warto zapoznać się z tym przykładem, aby uzyskać szczegółowe informacje dotyczące implementacji.

---

## <a name="code-that-redeems-the-authorization-code"></a>Kod, który zrealizuje kod autoryzacji

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

W ASP.NET Core zasada jest w pliku `Startup.cs`. Chcesz subskrybować zdarzenie `OnAuthorizationCodeReceived` Open ID Connect i z tego zdarzenia Wywołaj MSAL. Metoda sieci `AcquireTokenFromAuthorizationCode`, która ma wpływ na przechowywanie w pamięci podręcznej tokenów, token dostępu dla żądanych `scopes`i token odświeżania, który zostanie użyty do odświeżenia tokenu dostępu, gdy jest bliski wygaśnięcia lub aby uzyskać token w imieniu tego samego użytkownika, ale dla innego zasobu.

W ramach tego [samouczka ASP.NET Core aplikacji sieci Web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) próbuje udostępnić Ci kod wielokrotnego użytku dla aplikacji sieci Web.

Oto kod [Startup. cs # L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42) , który zawiera wywołanie metody `AddMicrosoftIdentityPlatformAuthentication`, która dodaje uwierzytelnianie do aplikacji sieci Web, a `AddMsal`, która dodaje możliwość wywoływania interfejsów API sieci Web. Wywołanie `AddInMemoryTokenCaches` polega na wyborze implementacji pamięci podręcznej tokenów wśród tych, które są możliwe:

```csharp
public class Startup
{
  // Code not show here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not show here
}
```

`Constants.ScopeUserRead` jest zdefiniowany w [stałych. cs # P5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5)

```csharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

Przebadano już zawartość `AddMicrosoftIdentityPlatformAuthentication` w [aplikacji sieci Web, która umożliwia logowanie użytkowników — konfiguracja kodu](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code)

### <a name="the-addmsal-method"></a>Metoda AddMsal

Kod dla `AddMsal` znajduje się w [witrynie Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159).

```csharp

/// <summary>
/// Extensions for IServiceCollection for startup initialization.
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the Web App or Web API
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign-in with their Microsoft personal accounts
          // (it's required by MSAL.NET and automatically provided when users sign-in with work or school accounts)
          options.Scope.Add("offline_access");
          if (initialScopes != null)
          {
              foreach (string scope in initialScopes)
              {
                  if (!options.Scope.Contains(scope))
                  {
                      options.Scope.Add(scope);
                  }
              }
          }

          // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
          // where it will be usable from Controllers later (through the TokenAcquisition service)
          var handler = options.Events.OnAuthorizationCodeReceived;
          options.Events.OnAuthorizationCodeReceived = async context =>
          {
              var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
              await tokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync(context, options.Scope).ConfigureAwait(false);
              await handler(context).ConfigureAwait(false);
          };
      });
      return services;
  }
}
```

Metoda `AddMsal` zapewnia, że:

- Aplikacja sieci Web ASP.NET Core żąda dla użytkownika zarówno IDToken, jak i kodu uwierzytelniania (`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`)
- zostanie dodany zakres `offline_access`. Jest to konieczne, aby użytkownik wyraził zgodę na uzyskanie tokenu odświeżania.
- Aplikacja subskrybuje zdarzenie `OnAuthorizationCodeReceived` OIDC i realizuje wywołanie przy użyciu MSAL.NET, które jest tutaj hermetyzowane do składnika wielokrotnego użytku implementującego `ITokenAcquisition`.

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>TokenAcquisition. AddAccountToCacheFromAuthorizationCodeAsync — Metoda

Metoda `TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` znajduje się w [witrynie Microsoft. Identity. Web/TokenAcquisition. cs # L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145). Gwarantuje to:

- ASP.NET nie próbuje zrealizować kodu uwierzytelniania równolegle z MSAL.NET (`context.HandleCodeRedemption();`)
- Oświadczenia w IDToken są dostępne dla MSAL, aby obliczyć klucz pamięci podręcznej tokenu dla konta użytkownika
- w razie konieczności zostanie utworzone wystąpienie aplikacji MSAL.NET
- kod jest zrealizowany przez aplikację MSAL.NET
- Nowy token identyfikatora jest współużytkowany z ASP.NET Core (podczas wywołania do `context.HandleCodeRedemption(null, result.IdToken);`). Token dostępu nie jest udostępniany ASP.NET Core. Pozostaje w pamięci podręcznej tokenów MSAL.NET skojarzonych z użytkownikiem, w której jest gotowy do użycia w kontrolerach ASP.NET Core.

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  string[] scopesRequestedByMsalNet = new string[]{ "openid", "profile", "offline_access" };

  // Code omitted here for clarity


  public async Task AddAccountToCacheFromAuthorizationCodeAsync(AuthorizationCodeReceivedContext context, IEnumerable<string> scopes)
  {
   // Code omitted here for clarity

    try
    {
      // As AcquireTokenByAuthorizationCodeAsync is asynchronous we want to tell ASP.NET core that we are handing the code
      // even if it's not done yet, so that it does not concurrently call the Token endpoint. (otherwise there will be a
      // race condition ending-up in an error from Azure AD telling "code already redeemed")
      context.HandleCodeRedemption();

      // The cache will need the claims from the ID token.
      // If they are not yet in the HttpContext.User's claims, so adding them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it and will not send the OAuth 2.0 request in
      // case a further call to AcquireTokenByAuthorizationCodeAsync in the future is required for incremental consent (getting a code requesting more scopes)
      // Share the ID Token though
      var result = await application
          .AcquireTokenByAuthorizationCode(scopes.Except(_scopesRequestedByMsalNet), context.ProtocolMessage.Code)
          .ExecuteAsync()
          .ConfigureAwait(false);

      context.HandleCodeRedemption(null, result.IdToken);
  }
  catch (MsalException ex)
  {
      Debug.WriteLine(ex.Message);
      throw;
  }
 }
```

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>TokenAcquisition. BuildConfidentialClientApplication — Metoda

W ASP.NET Core tworzenia poufnej aplikacji klienckiej używa informacji znajdujących się w elemencie HttpContext. Dostęp za pomocą właściwości `CurrentHttpContext`, elementu HttpContext skojarzonego z żądaniem, wie o adresie URL aplikacji sieci Web oraz zalogowanego użytkownika (w `ClaimsPrincipal`). `BuildConfidentialClientApplication` używa również konfiguracji ASP.NET Core, która zawiera sekcję "AzureAD", która jest powiązana z:

- Struktura danych `_applicationOptions` typu [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet)
- wystąpienie `azureAdOptions` typu [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs) zdefiniowane w ASP.NET Core `Authentication.AzureAD.UI`. Na koniec aplikacja musi obsługiwać pamięć podręczną tokenów. Dowiesz się więcej na ten temat w następnej sekcji.

Kod dla metody `GetOrBuildConfidentialClientApplication()` ma wartość [Microsoft. Identity. Web/TokenAcquisition. cs # L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). Używa elementów członkowskich, które zostały wprowadzone przez wstrzyknięcie zależności (przekazano w konstruktorze TokenAcquisition w [Microsoft. Identity. Web/TokenAcquisition. cs # L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59))

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL Confidential client application if needed
  /// </summary>
  private IConfidentialClientApplication GetOrBuildConfidentialClientApplication()
  {
    if (application == null)
    {
        application = BuildConfidentialClientApplication();
    }
    return application;
  }

  /// <summary>
  /// Creates an MSAL Confidential client application
  /// </summary>
  /// <param name="claimsPrincipal"></param>
  /// <returns></returns>
  private IConfidentialClientApplication BuildConfidentialClientApplication()
  {
    var request = CurrentHttpContext.Request;
    var azureAdOptions = _azureAdOptions;
    var applicationOptions = _applicationOptions;
    string currentUri = UriHelper.BuildAbsolute(
        request.Scheme,
        request.Host,
        request.PathBase,
        azureAdOptions.CallbackPath ?? string.Empty);

    string authority = $"{applicationOptions.Instance}{applicationOptions.TenantId}/";

    var app = ConfidentialClientApplicationBuilder
        .CreateWithApplicationOptions(applicationOptions)
        .WithRedirectUri(currentUri)
        .WithAuthority(authority)
        .Build();

    // Initialize token cache providers
    _appTokenCacheProvider?.InitializeAsync(app.AppTokenCache);
    _userTokenCacheProvider?.InitializeAsync(app.UserTokenCache);

    return app;
  }

```

### <a name="summary"></a>Podsumowanie

W celu podsumowania `AcquireTokenByAuthorizationCode` naprawdę realizuje kod autoryzacji żądany przez ASP.NET i pobiera tokeny dodawane do pamięci podręcznej tokenów użytkownika MSAL.NET. Następnie są one używane w kontrolerach ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Sposób, w jaki ASP.NET obsługuje elementy, jest podobny do ASP.NET Core, z tą różnicą, że konfiguracja OpenIdConnect i subskrypcja zdarzenia `OnAuthorizationCodeReceived` odbywa się w pliku [App_Start \Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) . Podobne koncepcje znajdziesz w ASP.NET Core, z wyjątkiem tego, że w ASP.NET należy określić RedirectUri w [pliku Web. config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Ta konfiguracja jest nieco mniej niezawodna niż to, co jest wykonywane w ASP.NET Core, ponieważ należy ją zmienić podczas wdrażania aplikacji.

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the querystring (&code=<code>).
    app.UseOAuth2CodeRedeemer(
        new OAuth2CodeRedeemerOptions
        {
            ClientId = AuthenticationConfig.ClientId,
            ClientSecret = AuthenticationConfig.ClientSecret,
            RedirectUri = AuthenticationConfig.RedirectUri
        }
      );

  app.UseOpenIdConnectAuthentication(
      new OpenIdConnectAuthenticationOptions
      {
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // a basic set of permissions for user sign in & profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application you would use IssuerValidator for additional checks, like making sure the user's organization has signed up for your app.
            //     IssuerValidator = (issuer, token, tvp) =>
            //     {
            //        //if(MyCustomTenantValidation(issuer))
            //        return issuer;
            //        //else
            //        //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
            //    },
            //NameClaimType = "name",
        },
        Notifications = new OpenIdConnectAuthenticationNotifications()
        {
            AuthorizationCodeReceived = OnAuthorizationCodeReceived,
            AuthenticationFailed = OnAuthenticationFailed,
        }
      });
  }

  private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
  {
      // Upon successful sign in, get the access token & cache it using MSAL
      IConfidentialClientApplication clientApp = MsalAppBuilder.BuildConfidentialClientApplication(new ClaimsPrincipal(context.AuthenticationTicket.Identity));
      AuthenticationResult result = await clientApp.AcquireTokenByAuthorizationCode(new[] { "Mail.Read" }, context.Code).ExecuteAsync();
  }

  private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
  {
      notification.HandleResponse();
      notification.Response.Redirect("/Error?message=" + notification.Exception.Message);
      return Task.FromResult(0);
  }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Zobacz sekcję w [aplikacji sieci Web, która umożliwia użytkownikom](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) logowanie się — konfiguracja kodu, aby zrozumieć, jak przykład Java Pobiera kod autoryzacji. Po odebraniu przez aplikację Aplikacja [AuthFilter. Java # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56) deleguje do metody `AuthHelper.processAuthenticationCodeRedirect` w [AuthHelper. Java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97), a następnie wywołuje `getAuthResultByAuthCode`:

```Java
class AuthHelper {
  // code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// code omitted
  }
}
```

Metoda `getAuthResultByAuthCode` jest definiowana w [AuthHelper. Java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Tworzy MSAL `ConfidentialClientApplication` i wywołuje `acquireToken()` za pomocą `AuthorizationCodeParameters` utworzonych na podstawie kodu autoryzacji.

```Java
   private IAuthenticationResult getAuthResultByAuthCode(
            HttpServletRequest httpServletRequest,
            AuthorizationCode authorizationCode,
            String currentUri) throws Throwable {

        IAuthenticationResult result;
        ConfidentialClientApplication app;
        try {
            app = createClientApplication();

            String authCode = authorizationCode.getValue();
            AuthorizationCodeParameters parameters = AuthorizationCodeParameters.builder(
                    authCode,
                    new URI(currentUri)).
                    build();

            Future<IAuthenticationResult> future = app.acquireToken(parameters);

            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        }

        if (result == null) {
            throw new ServiceUnavailableException("authentication result was null");
        }

        SessionManagementHelper.storeTokenCacheInSession(httpServletRequest, app.tokenCache().serialize());

        return result;
    }

    private ConfidentialClientApplication createClientApplication() throws MalformedURLException {
        return ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(clientSecret)).
                authority(authority).
                build();
    }
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Po zażądaniu przepływu kodu autoryzacji, który jest widoczny w [aplikacji sieci Web, która loguje](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code)się przez użytkowników, kod autoryzacji jest odbierany przez funkcję `authorized`, która zawiera więcej niż jeden z adresów URL/getAToken. Zobacz [App. PR # L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44)

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

Zamiast wpisu tajnego klienta poufna aplikacja kliencka może również udowodnić swoją tożsamość przy użyciu certyfikatu klienta lub potwierdzenia klienta.
Korzystanie z zatwierdzeń klientów jest zaawansowanym scenariuszem, szczegółowym w przypadku [potwierdzeń klienta](msal-net-client-assertions.md)

## <a name="token-cache"></a>Pamięć podręczna tokenów

> [!IMPORTANT]
> W przypadku aplikacji sieci Web (lub interfejsów API sieci Web w rzeczywistości) implementacja pamięci podręcznej tokenów różni się od implementacji pamięci podręcznej tokenów aplikacji klasycznych (często [opartych na plikach](scenario-desktop-acquire-token.md#file-based-token-cache)).
> Ważne jest, ze względu na bezpieczeństwo i wydajność, aby zapewnić, że aplikacje sieci Web i interfejsy API sieci Web powinny mieć jedną pamięć podręczną tokenów dla każdego użytkownika (na konto). Należy serializować pamięć podręczną tokenów dla każdego konta.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Samouczek ASP.NET Core używa iniekcji zależności, aby umożliwić określenie implementacji pamięci podręcznej tokenów w pliku Startup.cs dla aplikacji. Microsoft. Identity. Web zawiera wiele wbudowanych serializatorów pamięci podręcznej tokenów opisanych w [serializacji pamięci podręcznej tokenu](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization). Interesującą możliwością jest wybranie ASP.NET Core pamięci [podręcznej pamięci rozproszonej](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache):

```csharp
// or use a distributed Token Cache by adding
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// and then choose your implementation

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache()

// Or a Redis cache
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Aby uzyskać szczegółowe informacje o dostawcach pamięci podręcznej tokenów, zobacz również [samouczki aplikacji sieci Web ASP.NET Core | Faza pamięci podręcznej tokenów](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) samouczka

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

W przypadku aplikacji sieci Web (lub interfejsów API sieci Web w rzeczywistości) implementacja pamięci podręcznej tokenów różni się od implementacji pamięci podręcznej tokenów aplikacji klasycznych (często [opartych na plikach](scenario-desktop-acquire-token.md#file-based-token-cache)). Może korzystać z sesji ASP.NET lub pamięci serwera. Zobacz, jak to zrobić implementacja pamięci podręcznej po utworzeniu aplikacji MSAL.NET w [MsalAppBuilder. cs # L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51)

```csharp
public static class MsalAppBuilder
{
 // Omitted code
    public static IConfidentialClientApplication BuildConfidentialClientApplication(ClaimsPrincipal currentUser)
    {
      IConfidentialClientApplication clientapp = ConfidentialClientApplicationBuilder.Create(AuthenticationConfig.ClientId)
            .WithClientSecret(AuthenticationConfig.ClientSecret)
            .WithRedirectUri(AuthenticationConfig.RedirectUri)
            .WithAuthority(new Uri(AuthenticationConfig.Authority))
            .Build();

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java udostępnia metody serializacji i deserializacji pamięci podręcznej tokenów. Przykład Java obsługuje serializację z sesji, jak pokazano w metodzie `getAuthResultBySilentFlow` w [AuthHelper. Java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122)

```Java
IAuthenticationResult getAuthResultBySilentFlow(HttpServletRequest httpRequest, HttpServletResponse httpResponse)
      throws Throwable {

  IAuthenticationResult result =  SessionManagementHelper.getAuthSessionObject(httpRequest);

  IConfidentialClientApplication app = createClientApplication();

  Object tokenCache = httpRequest.getSession().getAttribute("token_cache");
  if (tokenCache != null) {
      app.tokenCache().deserialize(tokenCache.toString());
  }

  SilentParameters parameters = SilentParameters.builder(
          Collections.singleton("User.Read"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  //update session with latest token cache
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

Szczegóły klasy `SessionManagementHelper` są podane w[](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)

# <a name="pythontabpython"></a>[Python](#tab/python)

W przykładzie języka Python w pamięci podręcznej według konta jest zapewniona przez ponowne utworzenie poufnej aplikacji klienckiej dla każdego żądania i Serializacja jej w pamięci podręcznej sesji kolb:

```python
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)

# Code omitted here for simplicity

def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)
```

---

## <a name="next-steps"></a>Następne kroki

W tym momencie podczas logowania użytkownika w pamięci podręcznej tokeny jest przechowywany token. Zobaczmy, w jaki sposób jest używany w innych częściach aplikacji sieci Web.

> [!div class="nextstepaction"]
> [Zaloguj się do aplikacji sieci Web](scenario-web-app-call-api-sign-in.md)
