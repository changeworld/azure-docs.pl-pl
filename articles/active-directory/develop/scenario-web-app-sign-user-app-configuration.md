---
title: Aplikacja sieci Web, logujący się użytkownicy (kod Konfiguracja) - Platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację sieci web, logujący się użytkownicy (Konfiguracja kodu)
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
ms.openlocfilehash: 82e6cbcd01c87ddffb7eac8d0ea0faef85f41a13
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66254008"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Aplikacja sieci Web przez logowania użytkowników — Konfiguracja kodu

Dowiedz się, jak skonfigurować kod dla aplikacji sieci Web logowania przez użytkowników.

## <a name="libraries-used-to-protect-web-apps"></a>Biblioteki używane do ochrony aplikacji sieci Web

<!-- This section can be in an include for Web App and Web APIs -->
Dostępne są następujące biblioteki, umożliwia ochronę aplikacji sieci Web (i interfejs API sieci Web):

| Platforma | Biblioteka | Opis |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Tożsamość rozszerzeń modelu dla platformy .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Oprogramowanie Microsoft Identity Extensions dla platformy .NET jest używana bezpośrednio przez platformę ASP.NET i ASP.NET Core, i proponuje zbiór bibliotek DLL, uruchamiania na .NET Framework i .NET Core. Z aplikacji sieci Web Core ASP.NET/ASP.NET, można kontrolować za pomocą tokenu weryfikacji **TokenValidationParameters** klasy (w szczególności w niektórych przypadkach niezależny dostawca oprogramowania) |

## <a name="aspnet-core-configuration"></a>Konfiguracja platformy ASP.NET Core

### <a name="application-configuration-files"></a>Pliki konfiguracyjne aplikacji

W programie ASP.NET Core Web logowanie użytkowników aplikacji z platformą tożsamości Microsoft są konfigurowane za pomocą `appsettings.json` pliku. Ustawienia, które należy wypełnić są następujące:

- Chmura `Instance` Jeśli chcesz, aby aplikacja ma działać w chmurach krajowych
- grupy odbiorców w `tenantId`
- `clientId` dla aplikacji, jak skopiować z witryny Azure portal.

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
    // - the tenant Id as a a GUID obtained from the azure portal to sign-in users in your organization
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

W programie ASP.NET Core jest inny plik, który zawiera adres URL (`applicationUrl`) i SSL Port (`sslPort`) dla aplikacji, a także różne profile.

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

W witrynie Azure portal, Odpowiedz identyfikatory URI, które należy zarejestrować w **uwierzytelniania** strony aplikacji musi być zgodna te adresy URL; oznacza to, powyższych plików dwóch konfiguracji mogą być `https://localhost:44321/signin-oidc` jako applicationUrl jest `http://localhost:3110` ale `sslPort` jest określony (44321), a `CallbackPath` jest `/signin-oidc` zgodnie z definicją w `appsettings.json`.
  
W ten sam sposób, będzie miał ustawienie Wyloguj URI `https://localhost:44321/signout-callback-oidc`.

### <a name="initialization-code"></a>Kod inicjowania

W aplikacji sieci Web programu ASP.NET Core i interfejsów API sieci Web, kod, wykonując Inicjowanie aplikacji znajduje się w `Startup.cs` pliku, a, aby dodać uwierzytelnianie przy użyciu programu Microsoft Identity platformy (dawniej Azure AD) w wersji 2.0, należy dodać następujący kod. Komentarze w kodzie powinno być oczywista.

  > [!NOTE]
  > Po uruchomieniu projektu z projektu sieci web domyślnego platformy ASP.NET core w programie Visual studio lub za pomocą `dotnet new mvc` metoda `AddAzureAD` jest domyślnie dostępna, ponieważ powiązane pakiety są ładowane automatycznie. Jednak jeśli kompilacja projektu od podstaw i próbujesz użyć poniższego kodu, zalecamy, aby dodać pakiet NuGet **"Microsoft.AspNetCore.Authentication.AzureAD.UI"** do projektu, aby `AddAzureAD` dostępnej metody.
  
```CSharp
 services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
         .AddAzureAD(options => configuration.Bind("AzureAd", options));

 services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
 {
  // The ASP.NET core templates are currently using Azure AD v1.0, and compute
  // the authority (as {Instance}/{TenantID}). We want to use the Microsoft Identity Platform v2.0 endpoint
  options.Authority = options.Authority + "/v2.0/";

  // If you want to restrict the users that can sign-in to specific organizations
  // Set the tenant value in the appsettings.json file to 'organizations', and add the
  // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection.
  // Otherwise validate the issuer
  options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ForAadInstance(options.Authority).ValidateAadIssuer;

  // Set the nameClaimType to be preferred_username.
  // This change is needed because certain token claims from Azure AD v1.0 endpoint
  // (on which the original .NET core template is based) are different in Azure AD v2.0 endpoint.
  // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
  // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
  options.TokenValidationParameters.NameClaimType = "preferred_username";
  ...
```

## <a name="aspnet-configuration"></a>Konfiguracja platformy ASP.NET

W programie ASP.NET: aplikacja jest skonfigurowana za pośrednictwem `Web.Config` pliku

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

Kod związane z uwierzytelnianiem w aplikacji sieci Web platformy ASP.NET / interfejsów API sieci Web znajduje się w `App_Start/Startup.Auth.cs` pliku.

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
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

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zaloguj się i wylogowywania](scenario-web-app-sign-user-sign-in.md)
