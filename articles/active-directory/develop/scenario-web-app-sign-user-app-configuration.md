---
title: Konfigurowanie aplikacji sieci Web, która loguje się do użytkowników — platforma tożsamości firmy Microsoft | Azure
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
ms.openlocfilehash: d54103cad8a3550bdc300cba2308397dd1ce3d6c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262440"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>Aplikacja internetowa, która loguje użytkowników: Konfiguracja kodu

Dowiedz się, jak skonfigurować kod dla aplikacji sieci Web, która loguje się w użytkownikach.

## <a name="libraries-for-protecting-web-apps"></a>Biblioteki do ochrony aplikacji sieci Web

<!-- This section can be in an include for Web App and Web APIs -->
Biblioteki używane do ochrony aplikacji sieci Web (i internetowego interfejsu API) są następujące:

| Platforma | Biblioteka | Opis |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Rozszerzenia modelu tożsamości dla platformy .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Używane bezpośrednio przez ASP.NET i ASP.NET Core, rozszerzenia modelu tożsamości Microsoft dla platformy .NET proponują zestaw bibliotek DLL działających na .NET Framework i .NET Core. Z aplikacji sieci Web ASP.NET lub ASP.NET Core można kontrolować sprawdzanie poprawności tokenu przy użyciu klasy **TokenValidationParameters** (w szczególności w niektórych scenariuszach partnerskich). |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Obsługa aplikacji sieci Web w języku Java |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | Obsługa aplikacji sieci Web w języku Python |

Wybierz kartę odpowiadającą wybranej platformie:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Fragmenty kodu w tym artykule i następujące zostały wyodrębnione z [samouczka ASP.NET Core aplikacji sieci Web, rozdział 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

Warto zapoznać się z tym samouczkiem, aby uzyskać szczegółowe informacje dotyczące implementacji.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Fragmenty kodu w tym artykule i poniższe elementy są wyodrębniane z [przykładowej aplikacji sieci web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect).

Warto zapoznać się z tym przykładem, aby uzyskać szczegółowe informacje dotyczące implementacji.

# <a name="java"></a>[Java](#tab/java)

Fragmenty kodu w tym artykule i poniższe zostały wyodrębnione z [aplikacji sieci Web Java wywołującej przykład Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp) w języku Java MSAL.

Warto zapoznać się z tym przykładem, aby uzyskać szczegółowe informacje dotyczące implementacji.

# <a name="python"></a>[Python](#tab/python)

Fragmenty kodu w tym artykule i następujące elementy zostały wyodrębnione z [aplikacji sieci Web w języku Python wywołującej przykład programu Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) w języku Python MSAL.

Warto zapoznać się z tym przykładem, aby uzyskać szczegółowe informacje dotyczące implementacji.

---

## <a name="configuration-files"></a>Pliki konfiguracji

Aplikacje sieci Web, które logują użytkowników przy użyciu platformy tożsamości firmy Microsoft, są zwykle konfigurowane za pośrednictwem plików konfiguracji. Ustawienia, które należy wypełnić:

- Wystąpienie w chmurze (`Instance`), jeśli chcesz, aby aplikacja działała w chmurach narodowych, na przykład
- Odbiorcy w IDENTYFIKATORze dzierżawy (`TenantId`)
- Identyfikator klienta (`ClientId`) dla aplikacji, skopiowany z Azure Portal

Czasami aplikacje mogą być parametryczne przez `Authority`, które są połączeniem `Instance` i `TenantId`.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

W ASP.NET Core te ustawienia znajdują się w pliku [appSettings. JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) , w sekcji "AzureAd".

```Json
{
  "AzureAd": {
    // Azure cloud instance among:
    // - "https://login.microsoftonline.com/" for Azure public cloud
    // - "https://login.microsoftonline.us/" for Azure US government
    // - "https://login.microsoftonline.de/" for Azure AD Germany
    // - "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD audience among:
    // - "TenantId" as a GUID obtained from the Azure portal to sign in users in your organization
    // - "organizations" to sign in users in any work or school account
    // - "common" to sign in users with any work or school account or Microsoft personal account
    // - "consumers" to sign in users with a Microsoft personal account only
    "TenantId": "[Enter the tenantId here]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

W ASP.NET Core inny plik ([properties\launchSettings.JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)) zawiera adres URL (`applicationUrl`) i Port SSL (`sslPort`) dla aplikacji i różnych profilów.

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

W Azure Portal identyfikatory URI odpowiedzi, które należy zarejestrować na stronie **uwierzytelniania** dla aplikacji, muszą być zgodne z tymi adresami URL. W przypadku dwóch wcześniejszych plików konfiguracji będą one `https://localhost:44321/signin-oidc`. Przyczyną jest to, że `applicationUrl` jest `http://localhost:3110`, ale `sslPort` jest określony (44321). `CallbackPath` jest `/signin-oidc`, zgodnie z definicją w `appsettings.json`.

W ten sam sposób identyfikator URI wylogowania zostanie ustawiony na `https://localhost:44321/signout-callback-oidc`.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

W programie ASP.NET aplikacja jest konfigurowana za pomocą pliku [Web. config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) w wierszach od 12 do 15.

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, visit
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

W Azure Portal identyfikatory URI odpowiedzi, które należy zarejestrować na stronie **uwierzytelniania** dla aplikacji, muszą być zgodne z tymi adresami URL. Oznacza to, że powinny być `https://localhost:44326/`.

# <a name="java"></a>[Java](#tab/java)

W języku Java konfiguracja znajduje się w pliku [Application. Properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) znajdującym się w obszarze `src/main/resources`.

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

W Azure Portal identyfikatory URI odpowiedzi, które należy zarejestrować na stronie **uwierzytelniania** dla aplikacji, muszą być zgodne z wystąpieniami `redirectUri` zdefiniowanymi przez aplikację. Oznacza to, że powinny być `http://localhost:8080/msal4jsample/secure/aad` i `http://localhost:8080/msal4jsample/graph/me`.

# <a name="python"></a>[Python](#tab/python)

Oto plik konfiguracyjny języka Python w [app_config. PR](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py):

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> Ten przewodnik Szybki Start proponuje przechowywanie klucza tajnego klienta w pliku konfiguracyjnym dla uproszczenia. W aplikacji produkcyjnej warto używać innych metod przechowywania danych tajnych, takich jak Magazyn kluczy lub zmienna środowiskowa, zgodnie z opisem w [dokumentacji kolby](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables).
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Kod inicjalizacji

Kod inicjalizacji różni się w zależności od platformy. W przypadku ASP.NET Core i ASP.NET Logowanie użytkowników jest delegowane do oprogramowania pośredniczącego OpenID Connect Connect. Szablon ASP.NET lub ASP.NET Core generuje aplikacje sieci Web dla punktu końcowego Azure Active Directory (Azure AD) v 1.0. Aby dostosować je do punktu końcowego Microsoft Identity platform (v 2.0), wymagana jest pewna konfiguracja. W przypadku środowiska Java jest ono obsługiwane przez źródło współpracy aplikacji.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

W ASP.NET Core aplikacje sieci Web (i interfejsy API sieci Web) aplikacja jest chroniona, ponieważ na kontrolerach lub akcjach kontrolera istnieje atrybut `[Authorize]`. Ten atrybut sprawdza, czy użytkownik jest uwierzytelniony. Kod inicjujący aplikację znajduje się w pliku Startup.cs.

Aby dodać uwierzytelnianie za pomocą platformy tożsamości firmy Microsoft (dawniej usługi Azure AD v 2.0), należy dodać następujący kod. Komentarze w kodzie nie powinny mieć wyjaśnień.

> [!NOTE]
> Jeśli rozpoczniesz projekt przy użyciu domyślnego ASP.NET Core projektu sieci Web w programie Visual Studio lub za pomocą `dotnet new mvc`, Metoda `AddAzureAD` jest domyślnie dostępna. Wynika to z faktu, że pakiety powiązane są ładowane automatycznie.
>
> Jeśli kompilujesz projekt od podstaw i próbujesz użyć poniższego kodu, zalecamy dodanie pakietu NuGet **Microsoft. AspNetCore. Authentication. AzureAD. UI** do projektu w celu udostępnienia metody `AddAzureAD`.

Poniższy kod jest dostępny w programie [Startup. cs # L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34).

```csharp
public class Startup
{
 ...

  // This method is called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign in users with the Microsoft identity platform
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

Metoda rozszerzenia `AddMicrosoftIdentityPlatformAuthentication` jest zdefiniowana w [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23). Go

- Dodaje usługę uwierzytelniania.
- Konfiguruje opcje odczytu pliku konfiguracji.
- Konfiguruje opcje połączenia OpenID Connect, tak aby używany urząd był platformą tożsamości firmy Microsoft (dawniej usługi Azure AD v 2.0).
- Sprawdza poprawność wystawcy tokenu.
- Zapewnia, że oświadczenia odpowiadające nazwie są mapowane z oświadczenia `preferred_username` w tokenie ID.

Oprócz konfiguracji można określić nazwę sekcji konfiguracji podczas wywoływania `AddMicrosoftIdentityPlatformAuthentication`. Domyślnie jest `AzureAd`.

Śledzenie zdarzeń OpenID Connect łączenie oprogramowania pośredniczącego może pomóc w rozwiązywaniu problemów z aplikacją sieci Web, jeśli uwierzytelnianie nie działa. Ustawienie `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` na `true` pokaże sposób, w jaki informacje są opracowywane przez zestaw ASP.NET Core oprogramowania pośredniczącego w miarę postępu z odpowiedzi HTTP na tożsamość użytkownika w `HttpContext.User`.

```csharp
/// <summary>
/// Add authentication with the Microsoft identity platform.
/// This method expects the configuration file to have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenID Connect events.
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
      // Per the following code, this application signs in users in any work or school
      // accounts and any Microsoft personal accounts.
      // If you want to direct Azure AD to restrict the users who can sign in, change
      // the tenant value of the appsettings.json file in the following way:
      // - Only work or school accounts => 'organizations'
      // - Only Microsoft personal accounts => 'consumers'
      // - Work or school and personal accounts => 'common'
      // If you want to restrict the users who can sign in to only one tenant,
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization.
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users who can sign in to several organizations,
      // set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to the options.TokenValidationParameters.ValidIssuers collection.
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set nameClaimType to be preferred_username.
      // This change is needed because certain token claims from the Azure AD v1 endpoint
      // (on which the original .NET Core template is based) are different from the Microsoft identity platform endpoint.
      // For more details, see [ID tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).
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

Klasa `AadIssuerValidator` umożliwia sprawdzenie poprawności wystawcy tokenu w wielu przypadkach. Ta klasa działa z tokenem w wersji 1.0 lub 2.0, z jedną dzierżawą lub aplikacją wielodostępną albo aplikacją, która loguje się do użytkowników przy użyciu osobistych kont Microsoft w chmurze publicznej platformy Azure lub w chmurach krajowych. Jest on dostępny w [witrynie Microsoft. Identity. Web/Resource/AadIssuerValidator. cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Kod związany z uwierzytelnianiem w aplikacji sieci Web ASP.NET i interfejsy API sieci Web znajdują się w pliku [App_Start/Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) .

```csharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0.
     // `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/.
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="java"></a>[Java](#tab/java)

Przykład Java używa struktury sprężynowej. Aplikacja jest chroniona, ponieważ implementuje się filtr, który przechwytuje poszczególne odpowiedzi HTTP. W przypadku aplikacji sieci Web szybkiego startu dla języka Java ten filtr jest `AuthFilter` w `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`.

Filtr przetwarza przepływ kodu autoryzacji OAuth 2,0 i sprawdza, czy użytkownik jest uwierzytelniony (Metoda`isAuthenticated()`). Jeśli użytkownik nie jest uwierzytelniony, oblicza adres URL punktów końcowych autoryzacji usługi Azure AD i przekierowuje przeglądarkę do tego identyfikatora URI.

Po nadejściu odpowiedzi, zawierający kod autoryzacji, uzyskuje token przy użyciu języka Java MSAL. Gdy ostatecznie otrzyma token z punktu końcowego tokenu (w identyfikatorze URI przekierowania), użytkownik jest zalogowany.

Aby uzyskać szczegółowe informacje, zobacz metodę `doFilter()` w [AuthFilter. Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java).

> [!NOTE]
> Kod `doFilter()` jest zapisywana w nieco innym porządku, ale przepływ jest tym opisany.

Aby uzyskać szczegółowe informacje na temat przepływu kodu autoryzacji, który jest wyzwalany przez tę metodę, zobacz [przepływ kodu autoryzacji Microsoft Identity platform i OAuth 2,0](v2-oauth2-auth-code-flow.md).

# <a name="python"></a>[Python](#tab/python)

Przykład języka Python używa kolby. Inicjalizacja kolb i MSAL Python jest wykonywana w [App. PR # L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

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

W następnym artykule dowiesz się, jak wyzwolić logowanie i wylogować.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Logowanie i wylogowywanie](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Logowanie i wylogowywanie](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Logowanie i wylogowywanie](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Logowanie i wylogowywanie](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
