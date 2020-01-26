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
ms.openlocfilehash: 374b215a737efbe3d421b6dc49af01303ec54473
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759164"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Aplikacja sieci Web, która wywołuje interfejsy API sieci Web: Konfiguracja kodu

Jak pokazano w [aplikacji sieci Web, która loguje się w](scenario-web-app-sign-user-overview.md) scenariuszu użytkowników, aplikacja sieci Web używa [przepływu kodu autoryzacji OAuth 2,0](v2-oauth2-auth-code-flow.md) do podpisania użytkownika w programie. Ten przepływ ma dwie czynności:

1. Zażądaj kodu autoryzacji. Ta część deleguje prywatny dialog z użytkownikiem do platformy tożsamości firmy Microsoft. Podczas tego dialogu użytkownik loguje się i wyraża zgodę na korzystanie z interfejsów API sieci Web. Po pomyślnym zakończeniu dialogu prywatnego aplikacja internetowa otrzymuje kod autoryzacji w identyfikatorze URI przekierowania.
1. Zażądaj tokenu dostępu dla interfejsu API przez zrealizowanie kodu autoryzacji.

[Aplikacja sieci Web, która loguje się do scenariuszy użytkowników,](scenario-web-app-sign-user-overview.md) dotyczyła tylko pierwszego kroku. Tutaj dowiesz się, jak zmodyfikować aplikację sieci Web tak, aby nie tylko oznakować użytkowników w programie, ale również teraz wywołuje interfejsy API sieci Web.

## <a name="libraries-that-support-web-app-scenarios"></a>Biblioteki obsługujące scenariusze aplikacji sieci Web

Następujące biblioteki w bibliotece Microsoft Authentication Library (MSAL) obsługują przepływ kodu autoryzacji dla aplikacji sieci Web:

| Biblioteka MSAL | Opis |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Obsługa platform .NET Framework i .NET Core. Nieobsługiwane są platforma uniwersalna systemu Windows (platformy UWP), Xamarin. iOS i Xamarin. Android, ponieważ te platformy są używane do tworzenia publicznych aplikacji klienckich. |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> Biblioteka MSAL dla języka Python | Obsługa aplikacji sieci Web w języku Python. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> Biblioteka MSAL dla języka Java | Obsługa aplikacji sieci Web w języku Java. |

Wybierz kartę dla interesującej platformy:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Ponieważ logowanie użytkownika jest delegowane do oprogramowania pośredniczącego Open ID Connect (OIDC), należy skorzystać z procesu OIDC. Sposób działania zależy od używanej platformy.

W przypadku ASP.NET Core zasubskrybujesz zdarzenia OIDC oprogramowania pośredniczącego:

- Umożliwimy ASP.NET Core żądanie kodu autoryzacji za pomocą programu Open ID Connect. ASP.NET lub ASP.NET Core umożliwi użytkownikowi logowanie się i zgodę.
- Zasubskrybujesz aplikację sieci Web, aby otrzymać kod autoryzacji. Ta subskrypcja jest wykonywana za pomocą C# delegata.
- Po odebraniu kodu autoryzacji będziesz używać bibliotek MSAL do ich zrealizowania. Tokeny dostępu i tokeny odświeżania są przechowywane w pamięci podręcznej tokenów. Pamięć podręczna może być używana w innych częściach aplikacji, takich jak kontrolery, aby uzyskać inne tokeny w trybie dyskretnym.

Przykłady kodu w tym artykule i następujące zostały wyodrębnione z [wielostopniowego samouczka aplikacji internetowej ASP.NET Core, rozdział 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph). Warto zapoznać się z tym samouczkiem, aby uzyskać szczegółowe informacje dotyczące implementacji.

> [!NOTE]
> Aby w pełni zrozumieć przykłady kodu, należy zapoznać się z tematami [ASP.NET Core Fundamentals](https://docs.microsoft.com/aspnet/core/fundamentals), a zwłaszcza z [iniekcją](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) i [opcjami](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)zależności.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Ponieważ logowanie użytkownika jest delegowane do oprogramowania pośredniczącego Open ID Connect (OIDC), należy skorzystać z procesu OIDC. Sposób działania zależy od używanej platformy.

W przypadku usługi ASP.NET zasubskrybujesz zdarzenia oprogramowania OIDC pośredniczące:

- Umożliwimy ASP.NET Core żądanie kodu autoryzacji za pomocą programu Open ID Connect. ASP.NET lub ASP.NET Core umożliwi użytkownikowi logowanie się i zgodę.
- Zasubskrybujesz aplikację sieci Web, aby otrzymać kod autoryzacji. Ta subskrypcja jest wykonywana za pomocą C# delegata.
- Po odebraniu kodu autoryzacji będziesz używać bibliotek MSAL do ich zrealizowania. Tokeny dostępu i tokeny odświeżania są przechowywane w pamięci podręcznej tokenów. Pamięć podręczna może być używana w innych częściach aplikacji, takich jak kontrolery, aby uzyskać inne tokeny w trybie dyskretnym.

Przykłady kodu w tym artykule i poniższe zostały wyodrębnione z [przykładowej aplikacji internetowej ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Warto zapoznać się z tym przykładem, aby uzyskać szczegółowe informacje dotyczące implementacji.

# <a name="javatabjava"></a>[Java](#tab/java)

Przykłady kodu w tym artykule i następujące zostały wyodrębnione z [aplikacji sieci Web Java, która wywołuje Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp), przykład aplikacji sieci Web, która używa MSAL for Java.
Przykład umożliwia obecnie MSAL for Java utworzenie adresu URL kodu autoryzacji i obsługę nawigacji do punktu końcowego autoryzacji platformy tożsamości firmy Microsoft. Istnieje również możliwość użycia zabezpieczeń przebiegu w celu podpisania użytkownika w programie. Warto zapoznać się z przykładem, aby uzyskać pełne szczegóły implementacji.

# <a name="pythontabpython"></a>[Python](#tab/python)

Przykłady kodu w tym artykule i następujące zostały wyodrębnione z [aplikacji sieci Web](https://github.com/Azure-Samples/ms-identity-python-webapp)w języku Python, która wywołuje Microsoft Graph, przykład aplikacji internetowej korzystającej z MSAL. Python.
Przykład umożliwia obecnie MSAL. Język Python tworzy adres URL kodu autoryzacji i obsługuje nawigację do punktu końcowego autoryzacji platformy tożsamości firmy Microsoft. Warto zapoznać się z przykładem, aby uzyskać pełne szczegóły implementacji.

---

## <a name="code-that-redeems-the-authorization-code"></a>Kod, który zrealizuje kod autoryzacji

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

W ASP.NET Core w pliku `Startup.cs` subskrybujesz zdarzenie `OnAuthorizationCodeReceived` OpenID Connect Connect. Z tego zdarzenia Wywołaj metodę `AcquireTokenFromAuthorizationCode` MSAL.NET. Ta metoda przechowuje następujące tokeny w pamięci podręcznej tokenów:

- *Token dostępu* dla żądanego `scopes`.
- *Token odświeżania*. Ten token zostanie użyty do odświeżenia tokenu dostępu, gdy jest bliski wygaśnięcia lub aby uzyskać inny token w imieniu tego samego użytkownika, ale dla innego zasobu.

[Samouczek aplikacji sieci web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) zawiera kod wielokrotnego użytku dla aplikacji sieci Web.

Poniżej znajduje się kod z [uruchamiania. cs # L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42). Funkcja IT wywołuje następujące wywołania:

- Metoda `AddMicrosoftIdentityPlatformAuthentication`, która dodaje uwierzytelnianie do aplikacji sieci Web.
- Metoda `AddMsal`, która dodaje możliwość wywoływania interfejsów API sieci Web.
- Metoda `AddInMemoryTokenCaches`, która polega na wyborze implementacji pamięci podręcznej tokenów.

```csharp
public class Startup
{
  // Code not shown here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token-cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not shown here
}
```

`Constants.ScopeUserRead` jest zdefiniowany w [stałych. cs # P5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5):

```csharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

Przebadano już zawartość `AddMicrosoftIdentityPlatformAuthentication` w [aplikacji sieci Web, która oznacza, że użytkownicy rejestrują konfigurację kodu](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code).

### <a name="the-addmsal-method"></a>Metoda AddMsal

Kod dla `AddMsal` znajduje się w [witrynie Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159).

```csharp

/// <summary>
/// Extensions for IServiceCollection for startup initialization
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the web app or web API.
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection.
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign in by using their personal Microsoft accounts.
          // (It's required by MSAL.NET and automatically provided when users sign in by using work or school accounts.)
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

          // Handle the auth redemption by MSAL.NET so that a token is available in the token cache,
          // where it will be usable from controllers later (by means of the TokenAcquisition service).
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

- Aplikacja sieci Web ASP.NET Core żąda zarówno tokenu identyfikatora dla użytkownika, jak i kodu uwierzytelniania (`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`).
- Zostanie dodany zakres `offline_access`. Ten zakres uzyskuje zgodę użytkownika na uzyskanie tokenu odświeżania przez aplikację.
- Aplikacja subskrybuje zdarzenie `OnAuthorizationCodeReceived` OIDC i realizuje wywołanie przy użyciu MSAL.NET, które jest w tym miejscu umieszczane w składniku wielokrotnego użytku implementującym `ITokenAcquisition`.

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>TokenAcquisition. AddAccountToCacheFromAuthorizationCodeAsync — Metoda

Metoda `TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` znajduje się w [witrynie Microsoft. Identity. Web/TokenAcquisition. cs # L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145). Gwarantuje to:

- ASP.NET nie próbuje zrealizować kodu uwierzytelniania równolegle z MSAL.NET (`context.HandleCodeRedemption();`).
- Oświadczenia w tokenie identyfikatora są dostępne dla MSAL, aby obliczyć klucz pamięci podręcznej tokenu dla konta użytkownika.
- Wystąpienie aplikacji MSAL.NET jest tworzone w razie potrzeby.
- Kod jest zrealizowany przez aplikację MSAL.NET.
- Nowy token identyfikatora jest udostępniany ASP.NET Core podczas wywołania `context.HandleCodeRedemption(null, result.IdToken);`. Token dostępu nie jest udostępniany ASP.NET Core. Pozostaje w pamięci podręcznej tokenów MSAL.NET skojarzonych z użytkownikiem, w której jest gotowy do użycia w kontrolerach ASP.NET Core.

Oto odpowiedni kod dla `TokenAcquisition`:

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
      // Because AcquireTokenByAuthorizationCodeAsync is asynchronous, we tell ASP.NET core that we're handing the code
      // even if it's not done yet, so that it doesn't concurrently call the token endpoint. Otherwise, there will be a
      // race condition that causes an Azure AD error message ("code already redeemed").
      context.HandleCodeRedemption();

      // The cache needs the claims from the ID token.
      // If they're not yet in the HttpContext.User's claims, add them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Don't share the access token with ASP.NET Core. If we share it, ASP.NET will cache it and won't send the OAuth 2.0 request if
      // a further call to AcquireTokenByAuthorizationCodeAsync is required later for incremental consent (getting a code requesting more scopes).
      // Do share the ID token, however.
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

W ASP.NET Core tworzenia poufnej aplikacji klienckiej korzystają z informacji znajdujących się w `HttpContext`. Dostęp do `HttpContext` skojarzonych z żądaniem uzyskuje się za pomocą właściwości `CurrentHttpContext`. `HttpContext` zawiera informacje o adresie URL aplikacji sieci Web oraz o zalogowanym użytkowniku (w `ClaimsPrincipal`). 

Metoda `BuildConfidentialClientApplication` używa również konfiguracji ASP.NET Core. Konfiguracja zawiera sekcję "AzureAD", a także jest powiązana z obu następujących elementów:

- Struktura danych `_applicationOptions` typu [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet).
- Wystąpienie `azureAdOptions` typu [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs)zdefiniowane w `Authentication.AzureAD.UI`ASP.NET Core.

Na koniec aplikacja musi obsługiwać pamięć podręczną tokenów. Dowiesz się więcej o tym, jak to opisano w następnej sekcji.

Kod dla metody `GetOrBuildConfidentialClientApplication()` ma wartość [Microsoft. Identity. Web/TokenAcquisition. cs # L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). Używa elementów członkowskich, które zostały wstrzykiwane przez iniekcję zależności (w konstruktorze `TokenAcquisition` w [Microsoft. Identity. Web/TokenAcquisition. cs # L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59)).

Oto kod `GetOrBuildConfidentialClientApplication`:

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor.
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL confidential client application, if needed.
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

`AcquireTokenByAuthorizationCode` jest w rzeczywistości metodą, która realizuje kod autoryzacji, który ASP.NET żądań i pobiera tokeny dodawane do pamięci podręcznej MSAL.NET user-token. Z pamięci podręcznej tokeny są następnie używane na kontrolerach ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET obsługuje elementy podobnie jak ASP.NET Core, z tą różnicą, że konfiguracja OpenID Connect Connect i subskrypcję `OnAuthorizationCodeReceived` się w pliku [App_Start \Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) . Pojęcia są również podobne do tych w ASP.NET Core, z wyjątkiem tego, że w ASP.NET należy określić `RedirectUri` w [Web. config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Ta konfiguracja jest nieco mniej niezawodna niż ta w ASP.NET Core, ponieważ należy ją zmienić podczas wdrażania aplikacji.

Oto kod dla Startup.Auth.cs:

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the query string (&code=<code>).
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
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0.
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // A basic set of permissions for user sign-in and profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application, you would use IssuerValidator for additional checks, such as making sure the user's organization has signed up for your app.
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
      // Upon successful sign-in, get the access token and cache it by using MSAL.
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

Zobacz temat [aplikacja sieci Web, która loguje się w użytkownikach: Konfiguracja kodu](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) , aby zrozumieć, jak przykład Java Pobiera kod autoryzacji. Gdy aplikacja otrzyma kod, [AuthFilter. Java # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Deleguje do metody `AuthHelper.processAuthenticationCodeRedirect` w [AuthHelper. Java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
1. Wywołania `getAuthResultByAuthCode`.

```Java
class AuthHelper {
  // Code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // Code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // Code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// Code omitted
  }
}
```

Metoda `getAuthResultByAuthCode` jest definiowana w [AuthHelper. Java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Tworzy MSAL `ConfidentialClientApplication`, a następnie wywołuje `acquireToken()` z `AuthorizationCodeParameters` utworzonego na podstawie kodu autoryzacji.

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

Zażądano przepływu kodu autoryzacji, jak pokazano w [aplikacji sieci Web, która loguje się do użytkowników: Konfiguracja kodu](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). Następnie kod zostaje odebrany w funkcji `authorized`, która zawiera kolby z `/getAToken` adresem URL. Zobacz [App. PR # L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) , aby uzyskać pełny kontekst tego kodu:

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD.
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here.
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
Użycie potwierdzeń klientów jest zaawansowanym scenariuszem, szczegółowym w przypadku [potwierdzeń klientów](msal-net-client-assertions.md).

## <a name="token-cache"></a>Pamięć podręczna tokenów

> [!IMPORTANT]
> Implementacja pamięci podręcznej tokenów dla aplikacji sieci Web lub interfejsów API sieci Web różni się od implementacji aplikacji klasycznych, która często jest [oparta na plikach](scenario-desktop-acquire-token.md#file-based-token-cache).
> Ze względu na bezpieczeństwo i wydajność warto upewnić się, że w przypadku aplikacji sieci Web i interfejsów API sieci Web istnieje jedna pamięć podręczna tokenów na konto użytkownika. Należy serializować pamięć podręczną tokenów dla każdego konta.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Samouczek ASP.NET Core używa iniekcji zależności, aby umożliwić określenie implementacji pamięci podręcznej tokenów w pliku Startup.cs dla aplikacji. Microsoft. Identity. Web zawiera wstępnie skompilowane serializatory pamięci podręcznej tokenów opisane w [serializacji buforu tokenów](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization). Interesującą możliwością jest wybranie ASP.NET Core pamięci [podręcznej pamięci rozproszonej](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache):

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// Then, choose your implementation.

// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache()

// Or a Redis cache:
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache:
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Aby uzyskać szczegółowe informacje o dostawcach pamięci podręcznej tokenów, zobacz również [samouczki aplikacji sieci Web ASP.NET Core | Faza pamięci podręcznej tokenów](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) samouczka.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Implementacja pamięci podręcznej tokenów dla aplikacji sieci Web lub interfejsów API sieci Web różni się od implementacji aplikacji klasycznych, która często jest [oparta na plikach](scenario-desktop-acquire-token.md#file-based-token-cache).

Implementacja aplikacji sieci Web może korzystać z sesji ASP.NET lub pamięci serwera. Na przykład Zobacz, jak implementacja pamięci podręcznej jest podłączana po utworzeniu aplikacji MSAL.NET w [MsalAppBuilder. cs # L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51):

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

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation.
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java udostępnia metody serializacji i deserializacji pamięci podręcznej tokenów. Przykład Java obsługuje serializację z sesji, jak pokazano w metodzie `getAuthResultBySilentFlow` w [AuthHelper. Java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122):

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

  // Update session with latest token cache.
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

Szczegóły klasy `SessionManagementHelper` są dostępne w [przykładzie MSAL dla języka Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java).

# <a name="pythontabpython"></a>[Python](#tab/python)

W przykładzie języka Python jedna pamięć podręczna na konto jest zapewniana przez ponowne utworzenie poufnej aplikacji klienckiej dla każdego żądania, a następnie Serializacja jej w pamięci podręcznej sesji kolb:

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

W tym momencie po zalogowaniu się użytkownika token jest przechowywany w pamięci podręcznej tokenów. Zobaczmy, w jaki sposób jest używany w innych częściach aplikacji sieci Web.

> [!div class="nextstepaction"]
> [Aplikacja sieci Web, która wywołuje interfejsy API sieci Web: usuwanie kont z pamięci podręcznej na globalne wylogowywanie](scenario-web-app-call-api-sign-in.md)
