---
title: Aplikacja sieci Web użytkownikom zalogowanie się (logowanie) — Platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację sieci web przez logowania użytkowników (Zaloguj się)
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
ms.openlocfilehash: 3fb7fbba7ec48da580d2a630ae51aa20b3307848
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074623"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>Aplikacja logujący się użytkownicy sieci Web — zaloguj się

Dowiedz się, jak dodać logowanie do kodu aplikacji sieci web logowania przez użytkowników.

## <a name="sign-in"></a>Zaloguj się

Kodu zostały wykonane następujące kroki w poprzednim artykule [konfiguracji kodu aplikacji](scenario-web-app-sign-user-app-configuration.md) to wszystko, czego potrzebujesz do zaimplementowania wylogowania. Po użytkownik logował się do aplikacji, prawdopodobnie chcesz włączyć je się wylogować. Platforma ASP.NET core obsługuje wylogowywanie w protokole dla Ciebie.

## <a name="what-sign-out-involves"></a>Wyloguj się co pociąga za sobą

Wylogowywanie z aplikacji sieci web jest więcej niż usunięcia informacji o koncie logował się ze stanu aplikacji sieci web.
Aplikacja sieci web musi również przekierować użytkownika do v2.0 platforma tożsamości firmy Microsoft `logout` punktu końcowego, aby się wylogować. Gdy Twoja aplikacja sieci web przekierowuje użytkownika do `logout` punktu końcowego, ten punkt końcowy czyści sesji użytkownika w przeglądarce. Jeśli aplikacja nie przejdź do `logout` punktu końcowego, użytkownik będzie ponownego uwierzytelnienia do aplikacji bez podawania ich poświadczenia ponownie, ponieważ mają oni prawidłowe jednej sesji logowania z punktem końcowym v2.0 platformy Microsoft Identity.

Aby dowiedzieć się więcej, zobacz [wysyłać żądania wyrejestrowywania](v2-protocols-oidc.md#send-a-sign-out-request) sekcji [Microsoft Identity platformy w wersji 2.0 i protokołu OpenID Connect](v2-protocols-oidc.md) dokumentacji koncepcyjnego.

## <a name="application-registration"></a>Rejestracja aplikacji

Podczas rejestracji aplikacji zostanie zarejestrowany **wpis w Wyloguj URI**. W naszym samouczku zarejestrowano `https://localhost:44321/signout-oidc` w **adres URL wylogowania** pole **Zaawansowane ustawienia** sekcji **uwierzytelniania** strony. Aby uzyskać szczegółowe informacje, zobacz [ rejestrowanie aplikacji w aplikacji sieci Web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

## <a name="aspnet-core-code"></a>Kod platformy ASP.NET Core

### <a name="signout-button"></a>Przycisk Wyloguj się

Wyloguj przycisk jest widoczna w `Views\Shared\_LoginPartial.cshtml` i wyświetlane tylko wtedy, gdy istnieje uwierzytelnionego konta (po użytkownik wcześniej logował się).

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

### <a name="signout-action-of-the-accountcontroller"></a>`Signout()` Akcja `AccountController`

Naciśnięcie klawisza **Wyloguj** przycisk na wyzwalacze aplikacji sieci web `SignOut` akcji `Account` kontrolera. W poprzednich wersjach szablonów platformy ASP.NET core `Account` kontroler został osadzony w aplikacji sieci web, ale nie jest to już tak jak działa teraz w samej strukturze ASP.NET Core. 

Kod `AccountController` jest dostępna z platformy ASP.NET core repozytorium na z [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Kontrola konta:

- Identyfikator URI do przekierowania zestawy wiadomość OpenID `/Account/SignedOut` tak, aby kontroler jest wywoływana po usłudze Azure AD przeprowadzono logowanie się
- Wywołania `Signout()`, co pozwoli OpenIdConnect oprogramowania pośredniczącego, skontaktuj się z platformą Microsoft identity `logout` punktu końcowego który:

  - Usuwa plik cookie sesji z przeglądarki, a
  - Wywołania na koniec wywołuje **adres URL wylogowania**, który) domyślnie wyświetla podpisanej stronę widoku [SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) również udostępniane w ramach platformy ASP.NET Core.

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Przechwytuje wywołania `logout` punktu końcowego

Oprogramowanie pośredniczące programu ASP.NET Core OpenIdConnect umożliwia aplikacji przechwytuje wywołania z platformą tożsamości Microsoft `logout` punktu końcowego, zapewniając OpenIdConnect zdarzenie o nazwie `OnRedirectToIdentityProviderForSignOut`. Aplikacja internetowa używa go próbę uniknąć oknie dialogowym Wybierz konto, aby przedstawić użytkownikowi, podczas logowania. Przejmowanie to odbywa się w `AddAzureAdV2Authentication` z `Microsoft.Identity.Web` biblioteki wielokrotnego użytku. Zobacz [StartupHelpers.cs L58-L66](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L58-L66)

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

## <a name="aspnet-code"></a>ASP.NET Code

W programie ASP.NET: Wylogowywanie zostanie wywołany z metody SignOut() na kontrolerze (dla wystąpienia elementu AccountController). Ta metoda nie jest częścią struktury ASP.NET (sprzecznie co się stanie w programie ASP.NET Core), a nie korzysta z async i dlatego jego:

- wysyła żądanie wylogowania OpenId
- Czyści pamięć podręczną
- Przekierowuje do strony, którą chce

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

## <a name="protocol"></a>Protokół

Jeśli nie chcesz używać platformy ASP.NET Core i ASP.NET, można przyjrzeć się w dokumentacji protokołu, który jest dostępny z [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przenoszenie do środowiska produkcyjnego](scenario-web-app-sign-user-production.md)
