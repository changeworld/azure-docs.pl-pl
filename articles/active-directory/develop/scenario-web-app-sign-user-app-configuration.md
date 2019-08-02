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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a96d17ae7fbe94877032e7b4b2aacb63f6e070ca
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562260"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Aplikacja internetowa, która loguje się do użytkowników — konfiguracja kodu

Dowiedz się, jak skonfigurować kod dla aplikacji sieci Web, które logują się do użytkowników.

## <a name="libraries-used-to-protect-web-apps"></a>Biblioteki używane do ochrony Web Apps

<!-- This section can be in an include for Web App and Web APIs -->
Biblioteki używane do ochrony aplikacji sieci Web (i internetowego interfejsu API) są następujące:

| Platforma | Biblioteka | Opis |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Rozszerzenia modelu tożsamości dla platformy .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Używane bezpośrednio przez ASP.NET i ASP.NET Core, rozszerzenia tożsamości Microsoft dla platformy .NET proponują zestaw bibliotek DLL działających zarówno na platformie .NET Framework, jak i .NET Core. Z poziomu aplikacji sieci Web ASP.NET/ASP.NET Core można kontrolować sprawdzanie poprawności tokenu przy użyciu klasy **TokenValidationParameters** (w szczególności w niektórych scenariuszach niezależnych dostawców oprogramowania) |

## <a name="aspnet-core-configuration"></a>Konfiguracja ASP.NET Core

Fragmenty kodu w tym artykule i następujące zostały wyodrębnione z samouczka [ASP.NET Core aplikacji sieci Web, rozdział 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg). Warto zapoznać się z tym samouczkiem, aby uzyskać szczegółowe informacje dotyczące implementacji.

### <a name="application-configuration-files"></a>Pliki konfiguracji aplikacji

W ASP.NET Core użytkownicy logujący aplikacje sieci Web z platformą tożsamości firmy Microsoft są konfigurowani za pomocą `appsettings.json` pliku. Ustawienia, które należy wypełnić:

- Chmura `Instance` , jeśli chcesz, aby aplikacja działała w chmurach narodowych
- odbiorcy w`tenantId`
- `clientId` dla aplikacji, tak jak skopiowane z Azure Portal.

```JSon
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

W ASP.NET Core istnieje inny plik, który zawiera adres URL (`applicationUrl`) i Port SSL (`sslPort`) dla aplikacji, a także różne profile.

```JSon
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

W Azure Portal identyfikatory URI odpowiedzi, które należy zarejestrować na stronie **uwierzytelniania** dla aplikacji, muszą być zgodne z tymi adresami URL; oznacza to, że w przypadku dwóch powyższych plików konfiguracji byłyby `https://localhost:44321/signin-oidc` one `http://localhost:3110` applicationUrl, `sslPort` ale `CallbackPath` jest określony (44321) i jest `/signin-oidc` tak, jak zdefiniowano w `appsettings.json`.
  
W ten sam sposób identyfikator URI wylogowania zostanie ustawiony na `https://localhost:44321/signout-callback-oidc`wartość.

### <a name="initialization-code"></a>Kod inicjalizacji

W ASP.NET Core Web Apps (i interfejsy API sieci Web) kod inicjujący aplikację znajduje się w `Startup.cs` pliku, a aby dodać uwierzytelnianie za pomocą platformy tożsamości firmy Microsoft (dawniej usługi Azure AD v 2.0), należy dodać następujący kod. Komentarze w kodzie nie powinny mieć wyjaśnień.

  > [!NOTE]
  > Po rozpoczęciu projektu przy użyciu domyślnego projektu sieci Web ASP.NET Core w programie Visual `dotnet new mvc` Studio lub `AddAzureAD` użycia metody jest domyślnie dostępne, ponieważ pakiety powiązane są ładowane automatycznie. Jeśli jednak tworzysz projekt od podstaw i próbujesz użyć poniższego kodu, sugerujemy dodanie pakietu NuGet **"Microsoft. AspNetCore. Authentication. AzureAD. UI"** do projektu, aby udostępnić `AddAzureAD` metodę.
  
```CSharp
 services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
         .AddAzureAD(options => configuration.Bind("AzureAd", options));

 services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
 {
  // The ASP.NET core templates are currently using Azure AD v1.0, and compute
  // the authority (as {Instance}/{TenantID}). We want to use the Microsoft identity platform endpoint
  options.Authority = options.Authority + "/v2.0/";

  // If you want to restrict the users that can sign-in to specific organizations
  // Set the tenant value in the appsettings.json file to 'organizations', and add the
  // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection.
  // Otherwise validate the issuer
  options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ForAadInstance(options.Authority).ValidateAadIssuer;

  // Set the nameClaimType to be preferred_username.
  // This change is needed because certain token claims from Azure AD v1.0 endpoint
  // (on which the original .NET core template is based) are different in Microsoft identity platform endpoint.
  // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
  // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
  options.TokenValidationParameters.NameClaimType = "preferred_username";
  ...
```

## <a name="aspnet-configuration"></a>Konfiguracja ASP.NET

W programie ASP.NET aplikacja jest konfigurowana za pomocą `Web.Config` pliku

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

Kod związany z uwierzytelnianiem w programie ASP.NET Web App/interfejsy API sieci Web znajduje `App_Start/Startup.Auth.cs` się w pliku.

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

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Logowanie i wylogowywanie](scenario-web-app-sign-user-sign-in.md)
