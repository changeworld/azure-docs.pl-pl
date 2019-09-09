---
title: Aplikacja internetowa, która loguje użytkowników (Zaloguj się) — platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację internetową, która loguje się do użytkowników (Zaloguj się)
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
ms.openlocfilehash: 9766b530b4d795d0f35f097de20155cdd17687ca
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70812393"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>Aplikacja sieci Web, która loguje się do użytkowników

Dowiedz się, jak dodać logowanie do kodu aplikacji sieci Web, które logują się do użytkowników.

## <a name="sign-in"></a>Logowanie

Kod, który został sprawdzony w [konfiguracji kodu aplikacji](scenario-web-app-sign-user-app-configuration.md) w poprzedniej artykule, jest wymagany do zaimplementowania logowania.
Gdy użytkownik zaloguje się do aplikacji, prawdopodobnie trzeba będzie ją wylogować. ASP.NET podstawowe uchwyty do wylogowania.

## <a name="what-sign-out-involves"></a>Co obejmuje wylogowanie

Wylogowanie z aplikacji sieci Web ma więcej niż usuwanie informacji o koncie zalogowanym ze stanu aplikacji sieci Web.
Aplikacja sieci Web musi również przekierować użytkownika do punktu końcowego platformy `logout` tożsamości firmy Microsoft, aby się wylogować. Gdy aplikacja internetowa przekierowuje użytkownika do `logout` punktu końcowego, ten punkt końcowy czyści sesję użytkownika z przeglądarki. Jeśli Twoja aplikacja nie przejdzie do `logout` punktu końcowego, użytkownik będzie ponownie uwierzytelniać się w aplikacji bez konieczności ponownego wprowadzania poświadczeń, ponieważ będzie mieć prawidłową sesję logowania jednokrotnego z punktem końcowym platformy tożsamości firmy Microsoft.

Aby dowiedzieć się więcej, zobacz sekcję [Wysyłanie żądania wylogowania](v2-protocols-oidc.md#send-a-sign-out-request) z poziomu [platformy tożsamości firmy Microsoft i](v2-protocols-oidc.md) dokumentacji dotyczącej pojęć dotyczących protokołu OpenID Connect Connect.

## <a name="application-registration"></a>Rejestracja aplikacji

Podczas rejestracji aplikacji zarejestrowano **Identyfikator URI po wylogowaniu**. `https://localhost:44321/signout-oidc` W naszym samouczku zarejestrowano się w polu **adres URL wylogowania** w sekcji **Ustawienia zaawansowane** na stronie **uwierzytelnianie** . Aby uzyskać szczegółowe informacje, zobacz [Rejestrowanie aplikacji webApp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

## <a name="aspnet-core-code"></a>Kod ASP.NET Core

### <a name="signout-button"></a>Przycisk wylogowaniu

Przycisk Wyloguj jest udostępniany w `Views\Shared\_LoginPartial.cshtml` usłudze i wyświetlany tylko wtedy, gdy istnieje konto uwierzytelnione (to oznacza, że użytkownik zalogował się wcześniej).

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.GetDisplayName()!</li>
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignOut">Sign out</a></li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

### <a name="signout-action-of-the-accountcontroller"></a>`Signout()`Akcja`AccountController`

Naciśnięcie przycisku **Wyloguj** w aplikacji sieci Web wyzwala `SignOut` akcję na `Account` kontrolerze. W poprzednich wersjach szablonów `Account` ASP.NET Core kontroler został osadzony w aplikacji sieci Web, ale nie jest to już teraz częścią samej struktury ASP.NET Core. 

Kod `AccountController` jest dostępny w repozytorium ASP.NET Core w lokalizacji z [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Kontrola konta:

- Ustawia identyfikator URI przekierowania OpenID Connect `/Account/SignedOut` na tak, aby kontroler został wywołany, gdy usługa Azure AD wykona wylogowywanie
- Wywołania `Signout()`, które umożliwiają oprogramowanie pośredniczące OpenIdConnect kontaktują się z punktem `logout` końcowym platformy tożsamości firmy Microsoft, który:

  - Czyści plik cookie sesji z przeglądarki i
  - Wywołania finally wywołują **adres URL wylogowania**, który domyślnie wyświetla wylogowaną stronę widoku [SignedOut. html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) udostępnianą jako część ASP.NET Core.

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Przechwytywanie wywołania do `logout` punktu końcowego

ASP.NET Core pośredniczące umożliwia aplikacji przechwycenie wywołania punktu końcowego platformy `logout` tożsamości firmy Microsoft przez podanie zdarzenia OpenIdConnect o nazwie. `OnRedirectToIdentityProviderForSignOut` Aplikacja sieci Web używa jej do próby uniknięcia wylogowania użytkownika w oknie dialogowym Wybieranie konta. Ta przechwycenie jest wykonywane w `AddAzureAdV2Authentication` `Microsoft.Identity.Web` bibliotece wielokrotnego użytku. Zobacz [StartupHelpers.cs L58-L66](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L58-L66)

```CSharp
public static IServiceCollection AddAzureAdV2Authentication(this IServiceCollection services,
                                                            IConfiguration configuration)
{
    ...
    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
        ...
        options.Authority = options.Authority + "/v2.0/";
        ...
        // Attempt to avoid displaying the select account dialog when signing out
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            var user = context.HttpContext.User;
            context.ProtocolMessage.LoginHint = user.GetLoginHint();
            context.ProtocolMessage.DomainHint = user.GetDomainHint();
            await Task.FromResult(0);
        };
    }
}
```

## <a name="aspnet-code"></a>Kod ASP.NET

W ASP.NET wylogowanie jest wyzwalane z metody wylogowaniu () na kontrolerze (na przykład elementu AccountController). Ta metoda nie jest częścią ASP.NET Framework (w przeciwieństwie do co dzieje się w ASP.NET Core) i nie używa Async i dlatego:

- wysyła wyzwanie wylogowania OpenID Connect
- czyści pamięć podręczną
- przekierowuje do strony, którą chce

```CSharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 MsalAppBuilder.ClearUserTokenCache();
 Response.Redirect("/");
}
```

## <a name="protocol"></a>Protocol

Jeśli nie chcesz używać ASP.NET Core lub ASP.NET, możesz zapoznać się z dokumentacją protokołu, która jest dostępna w ramach programu [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przenieś do środowiska produkcyjnego](scenario-web-app-sign-user-production.md)
