---
title: Aplikacja internetowa, która loguje użytkowników (Konfiguracja kodu) — platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację internetową, która loguje się do użytkowników (Konfiguracja kodu)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: f52fc70b54c27362575bef00c39a93d13e77cc2e
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175349"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Aplikacja internetowa, która loguje się do użytkowników — konfiguracja kodu

Dowiedz się, jak skonfigurować kod dla aplikacji sieci Web, które logują się do użytkowników.

## <a name="libraries-used-to-protect-web-apps"></a>Biblioteki używane do ochrony Web Apps

<!-- This section can be in an include for Web App and Web APIs -->
Biblioteki używane do ochrony aplikacji sieci Web (i internetowego interfejsu API) są następujące:

| Platforma | Biblioteka | Opis |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Rozszerzenia modelu tożsamości dla platformy .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Używane bezpośrednio przez ASP.NET i ASP.NET Core, rozszerzenia tożsamości Microsoft dla platformy .NET proponują zestaw bibliotek DLL działających zarówno na platformie .NET Framework, jak i .NET Core. Z poziomu aplikacji sieci Web ASP.NET/ASP.NET Core można kontrolować sprawdzanie poprawności tokenu przy użyciu klasy **TokenValidationParameters** (w szczególności w niektórych scenariuszach niezależnych dostawców oprogramowania) |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | MSAL for Java — obecnie w publicznej wersji zapoznawczej |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | MSAL for Python — obecnie w publicznej wersji zapoznawczej |

Wybierz kartę odpowiadającą używanej platformie:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Fragmenty kodu w tym artykule i następujące zostały wyodrębnione z [samouczka ASP.NET Core aplikacji sieci Web, rozdział 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

Warto zapoznać się z tym samouczkiem, aby uzyskać szczegółowe informacje dotyczące implementacji.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Fragmenty kodu w tym artykule i następujące są wyodrębniane z [przykładowej aplikacji ASP.NET Web](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)

Warto zapoznać się z tym przykładem, aby uzyskać szczegółowe informacje dotyczące implementacji.

# <a name="javatabjava"></a>[Java](#tab/java)

Fragmenty kodu w tym artykule i następujące elementy zostały wyodrębnione z [aplikacji sieci Web Java wywołującej](https://github.com/Azure-Samples/ms-identity-java-webapp) przykład aplikacji sieci Web programu Microsoft Graph MSAL Java

Warto zapoznać się z tym przykładem, aby uzyskać szczegółowe informacje dotyczące implementacji.

# <a name="pythontabpython"></a>[Python](#tab/python)

Fragmenty kodu w tym artykule i poniższe zostały wyodrębnione z [aplikacji sieci Web w języku Python wywołującej program Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) MSAL. Przykład aplikacji sieci Web w języku Python

Warto zapoznać się z tym przykładem, aby uzyskać szczegółowe informacje dotyczące implementacji.

---

## <a name="configuration-files"></a>Pliki konfiguracji

Aplikacje sieci Web, które logują użytkowników przy użyciu platformy tożsamości firmy Microsoft, są zwykle konfigurowane za pomocą plików konfiguracji. Ustawienia, które należy wypełnić:

- `Instance` w chmurze, jeśli chcesz, aby aplikacja działała (na przykład w chmurach narodowych)
- odbiorcy w `tenantId`
- `clientId` dla aplikacji, jak skopiowano z Azure Portal.

Czasami aplikacje mogą być parametryczne przez `authority`, które są połączeniem `instance` i `tenantId`

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

W ASP.NET Core te ustawienia znajdują się w pliku [appSettings. JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) , w sekcji "AzureAD".

```Json
{
  "AzureAd": {
    // Azure Cloud instance among:
    // "https://login.microsoftonline.com/" for Azure Public cloud.
    // "https://login.microsoftonline.us/" for Azure US government.
    // "https://login.microsoftonline.de/" for Azure AD Germany
    // "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD Audience among:
    // - the tenant Id as a GUID obtained from the azure portal to sign-in users in your organization
    // - "organizations" to sign-in users in any work or school accounts
    // - "common" to sign-in users with any work and school account or Microsoft personal account
    // - "consumers" to sign-in users with Microsoft personal account only
    "TenantId": "[Enter the tenantId here]]",

    // Client Id (Application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

W ASP.NET Core istnieje inny plik [properties\launchSettings.JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7) , który zawiera adres URL (`applicationUrl`) i Port SSL (`sslPort`) dla aplikacji i różnych profilów.

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

W Azure Portal identyfikatory URI odpowiedzi, które należy zarejestrować na stronie **uwierzytelniania** dla aplikacji, muszą być zgodne z tymi adresami URL; oznacza to, że w przypadku dwóch powyższych plików konfiguracji byłyby one `https://localhost:44321/signin-oidc`, ponieważ applicationUrl jest `http://localhost:3110`, ale `sslPort` jest określony (44321), a `CallbackPath` jest `/signin-oidc` zgodnie z definicją w `appsettings.json`.

W ten sam sposób identyfikator URI wylogowania zostanie ustawiony na `https://localhost:44321/signout-callback-oidc`.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

W programie ASP.NET aplikacja jest konfigurowana za pomocą wierszy w pliku [Web. Config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) 12-15

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, please visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

W Azure Portal identyfikatory URI odpowiedzi, które należy zarejestrować na stronie **uwierzytelniania** dla aplikacji, muszą być zgodne z tymi adresami URL; `https://localhost:44326/`.

# <a name="javatabjava"></a>[Java](#tab/java)

W języku Java konfiguracja znajduje się w pliku [Application. Properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) znajdującym się w obszarze `src/main/resources`

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

W Azure Portal identyfikatory URI odpowiedzi, które należy zarejestrować na stronie **uwierzytelniania** dla aplikacji, muszą być zgodne z redirectUrisą zdefiniowaną przez aplikację, która jest `http://localhost:8080/msal4jsample/secure/aad` i `http://localhost:8080/msal4jsample/graph/me`

# <a name="pythontabpython"></a>[Python](#tab/python)

Oto plik konfiguracji języka Python w [app_config. PR](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py)

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So token cache will be stored in server-side session
```

> [!NOTE]
> Ten przewodnik Szybki Start proponuje przechowywanie klucza tajnego klienta w pliku konfiguracyjnym dla uproszczenia.
> W aplikacji produkcyjnej warto używać innych metod przechowywania wpisów tajnych, takich jak Magazyn kluczy lub zmienna środowiskowa, zgodnie z opisem w dokumentacji kolby: https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Kod inicjalizacji

Kod inicjalizacji różni się w zależności od platformy. W przypadku ASP.NET Core i ASP.NET Logowanie użytkowników jest delegowane do oprogramowania pośredniczącego OpenIDConnect. Dzisiaj szablon ASP.NET/ASP.NET Core generuje aplikacje sieci Web dla punktu końcowego usługi Azure AD v 1.0. W związku z tym, aby dostosować je do punktu końcowego Microsoft Identity platform (v 2.0), wymagana jest konfiguracja. W przypadku środowiska Java jest ono obsługiwane przez źródło współpracy aplikacji.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

W ASP.NET Core Web Apps (i interfejsy API sieci Web) aplikacja jest chroniona, ponieważ masz atrybut `[Authorize]` na kontrolerach lub akcjach kontrolera. Ten atrybut sprawdza, czy użytkownik jest uwierzytelniony. Kod inicjujący inicjowanie aplikacji znajduje się w pliku `Startup.cs`, a w celu dodania uwierzytelniania przy użyciu platformy tożsamości firmy Microsoft (dawniej usługa Azure AD v 2.0) należy dodać poniższy kod. Komentarze w kodzie nie powinny mieć wyjaśnień.

  > [!NOTE]
  > Jeśli rozpoczniesz projekt przy użyciu domyślnego projektu sieci Web ASP.NET Core w programie Visual Studio lub przy użyciu `dotnet new mvc` Metoda `AddAzureAD` jest dostępna domyślnie, ponieważ pakiety powiązane są ładowane automatycznie.
  > Jeśli jednak tworzysz projekt od podstaw i próbujesz użyć poniższego kodu, zalecamy dodanie pakietu NuGet **"Microsoft. AspNetCore. Authentication. AzureAD. UI"** do projektu w celu udostępnienia metody `AddAzureAD`.

Poniższy kod jest dostępny w programie [Startup. cs # L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34)

```CSharp
public class Startup
{
 ...

  // This method gets called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign-in users with the Microsoft identity platform
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration);

      services.AddMvc(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
            })
        .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
    }
```

`AddMicrosoftIdentityPlatformAuthentication` to metoda rozszerzająca zdefiniowana w [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23). Go

- dodaje usługę uwierzytelniania
- Skonfiguruj opcje odczytu pliku konfiguracji
- konfiguruje opcje połączenia OpenID Connect, tak aby używany urząd był platformą tożsamości firmy Microsoft (dawniej usługi Azure AD v 2.0)
- Wystawca tokenu został sprawdzony
- oświadczenia odpowiadające nazwie są mapowane z oświadczenia "preferred_username" w tokenie identyfikatora

Oprócz konfiguracji można określić, podczas wywoływania `AddMicrosoftIdentityPlatformAuthentication`:

- Nazwa sekcji konfiguracji (domyślnie AzureAD)
- Jeśli chcesz śledzić zdarzenia oprogramowania pośredniczącego OpenIdConnect, które mogą pomóc w rozwiązywaniu problemów z aplikacją sieci Web, jeśli uwierzytelnianie nie działa: ustawienie `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` na `true` pokaże sposób, w jaki informacje są rozbudowane przez zestaw ASP.NET Coreego oprogramowania pośredniczącego. postępuje z odpowiedzi HTTP na tożsamość użytkownika w `HttpContext.User`.

```CSharp
/// <summary>
/// Add authentication with Microsoft identity platform.
/// This method expects the configuration file will have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenIdConnect events.
/// </param>
/// <returns></returns>
public static IServiceCollection AddMicrosoftIdentityPlatformAuthentication(
  this IServiceCollection services,
  IConfiguration configuration,
  string configSectionName = "AzureAd",
  bool subscribeToOpenIdConnectMiddlewareDiagnosticsEvents = false)
{
  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
      .AddAzureAD(options => configuration.Bind(configSectionName, options));
  services.Configure<AzureADOptions>(options => configuration.Bind(configSectionName, options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
      // Per the code below, this application signs in users in any Work and School
      // accounts and any Microsoft Personal Accounts.
      // If you want to direct Azure AD to restrict the users that can sign-in, change
      // the tenant value of the appsettings.json file in the following way:
      // - only Work and School accounts => 'organizations'
      // - only Microsoft Personal accounts => 'consumers'
      // - Work and School and Personal accounts => 'common'
      // If you want to restrict the users that can sign-in to only one tenant
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users that can sign-in to several organizations
      // Set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set the nameClaimType to be preferred_username.
      // This change is needed because certain token claims from Azure AD V1 endpoint
      // (on which the original .NET core template is based) are different than Microsoft identity platform endpoint.
      // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
      options.TokenValidationParameters.NameClaimType = "preferred_username";

      // ...

      if (subscribeToOpenIdConnectMiddlewareDiagnosticsEvents)
      {
          OpenIdConnectMiddlewareDiagnostics.Subscribe(options.Events);
      }
  });
  return services;
}
  ...
```

Klasa `AadIssuerValidator` umożliwia sprawdzenie poprawności wystawcy tokenu w wielu przypadkach (tokeny w wersji 1.0 lub 2.0, pojedynczej dzierżawie lub aplikacji z wieloma dzierżawcami, które logują się do użytkowników przy użyciu osobistych kont Microsoft, w chmurze publicznej platformy Azure lub w krajowym chmury). Jest on dostępny w [witrynie Microsoft. Identity. Web/Resource/AadIssuerValidator. cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Kod związany z uwierzytelnianiem w programie ASP.NET Web App/interfejsy API sieci Web znajduje się w pliku [App_Start/Startup. auth. cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) .

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0
     // The `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="javatabjava"></a>[Java](#tab/java)

Przykład Java używa struktury sprężynowej. Aplikacja jest chroniona, ponieważ implementuje `Filter`, która przechwytuje każdą odpowiedź HTTP. W aplikacji sieci Web Java — Szybki Start, ten filtr jest `AuthFilter` w `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`. Filtr przetwarza przepływ kodu autoryzacji OAuth 2,0 i w związku z tym:

- sprawdza, czy użytkownik jest uwierzytelniony (Metoda `isAuthenticated()`)
- Jeśli użytkownik nie jest uwierzytelniony, oblicza adres URL autoryzowanych punktów końcowych usługi Azure AD i przekierowuje przeglądarkę do tego identyfikatora URI
- po nadejściu odpowiedzi zawierającej kod uwierzytelniania uzyskuje token przy użyciu języka Java MSAL.
- gdy ostatecznie otrzyma token z punktu końcowego tokenu (w identyfikatorze URI przekierowania), użytkownik jest zalogowany.

Aby uzyskać szczegółowe informacje, zobacz metodę `doFilter()` w [AuthFilter. Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java)

> [!NOTE]
> Kod `doFilter()` jest zapisywana w nieco innym porządku, ale przepływ jest tym opisany.

Aby uzyskać szczegółowe informacje na temat przepływu kodu autoryzacji wyzwalanego przez tę metodę, zobacz artykuł [Microsoft Identity platform i kod autoryzacji OAuth 2,0](v2-oauth2-auth-code-flow.md) .

# <a name="pythontabpython"></a>[Python](#tab/python)

Przykład języka Python używa kolby. Inicjowanie kolby i MSAL. Język Python jest wykonywany w [aplikacji App. PR # L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28)

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>Następne kroki

W następnym artykule dowiesz się, jak wyzwolić logowanie i wylogować się.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Logowanie i wylogowywanie](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Logowanie i wylogowywanie](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Logowanie i wylogowywanie](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Logowanie i wylogowywanie](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
