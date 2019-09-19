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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ed61a09ffc76b4813dcb97330d3a1a436aa16eb
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086459"
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

### <a name="application-registration"></a>Rejestracja aplikacji

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Podczas rejestracji aplikacji zarejestrowano **Identyfikator URI po wylogowaniu**. `https://localhost:44321/signout-oidc` W naszym samouczku zarejestrowano się w polu **adres URL wylogowania** w sekcji **Ustawienia zaawansowane** na stronie **uwierzytelnianie** . Aby uzyskać szczegółowe informacje, zobacz [Rejestrowanie aplikacji webApp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Podczas rejestracji aplikacji zarejestrowano **Identyfikator URI po wylogowaniu**. `https://localhost:44308/Account/EndSession` W naszym samouczku zarejestrowano się w polu **adres URL wylogowania** w sekcji **Ustawienia zaawansowane** na stronie **uwierzytelnianie** . Aby uzyskać szczegółowe informacje, zobacz [Rejestrowanie aplikacji webApp](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet)

# <a name="javatabjava"></a>[Java](#tab/java)

Podczas rejestracji aplikacji należy zarejestrować **Identyfikator URI po wylogowaniu**. `http://localhost:8080/msal4jsample/` W naszym samouczku zarejestrowano się w polu **adres URL wylogowania** w sekcji **Ustawienia zaawansowane** na stronie **uwierzytelnianie** .

# <a name="pythontabpython"></a>[Python](#tab/python)

Podczas rejestracji aplikacji nie trzeba rejestrować adresu URL wylogowywania. Przykład nie implementuje globalnego wylogowania

---

### <a name="sign-out-button"></a>Przycisk Wyloguj

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

W ASP.NET Core przycisk Wyloguj jest uwidaczniany w `Views\Shared\_LoginPartial.cshtml` usłudze i wyświetlany tylko wtedy, gdy istnieje konto uwierzytelnione (to oznacza, że użytkownik zalogował się wcześniej).

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

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

W ASP.NET MVC przycisk Wyloguj jest uwidaczniany w `Views\Shared\_LoginPartial.cshtml` i wyświetlany tylko wtedy, gdy istnieje konto uwierzytelnione (to jest logowanie użytkownika wcześniej).

```html
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

W naszym przewodniku szybki start dla języka Java przycisk Wyloguj znajduje się w pliku main/resources/templates/auth_page.html

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="pythontabpython"></a>[Python](#tab/python)

W przewodniku szybki start w języku Python przycisk Wyloguj znajduje się w pliku [templates/Display.html](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e1199b4c3cdcb637cf0d8306832efbd85492e123/templates/display.html#L18-L20)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Acquire Token Result </title>
</head>
<body>
    {% if cond  %}
        <p1><b>Your information</b> </p1>
        <table>
        {% for key, value in auth_result.items() %}
           <tr>
                <th> {{ key }} </th>
                <td> {{ value }} </td>
           </tr>
        {% endfor %}
        </table>
        <form action="/logout" >
            <input type="submit" value=" Logout"/>
        </form>
    {% else %}
        <p1><b> {{auth_result}} </b> </p1>
        <form action="/authenticate" >
            <input type="submit" value=" Sign-in"/>
        </form>
    {% endif %}
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`Signout()`Akcja kontrolera

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

W ASP.NET naciśnięcie przycisku **Wyloguj** w aplikacji sieci Web wyzwala `SignOut` akcję na `AccountController` kontrolerze. W poprzednich wersjach szablonów `Account` ASP.NET Core kontroler został osadzony w aplikacji sieci Web, ale nie jest już tak samo, jak teraz jest częścią samej struktury ASP.NET Core.

Kod `AccountController` jest dostępny w repozytorium ASP.NET Core w lokalizacji z [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Kontrola konta:

- Ustawia identyfikator URI przekierowania OpenID Connect `/Account/SignedOut` na tak, aby kontroler został wywołany, gdy usługa Azure AD wykona wylogowanie
- Wywołania `Signout()`, które umożliwiają oprogramowanie pośredniczące OpenIdConnect kontaktują się z punktem `logout` końcowym platformy tożsamości firmy Microsoft, który:

  - Czyści plik cookie sesji z przeglądarki i
  - na koniec wywołuje **adres URL wylogowania**, który domyślnie wyświetla stronę ze stroną widoku wylogowanego [SignedOut. html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) , która jest dostępna jako część ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

W ASP.NET wylogowanie jest wyzwalane z `SignOut()` metody na kontrolerze (na przykład elementu AccountController). Ta metoda nie jest częścią ASP.NET Framework (w przeciwieństwie do co dzieje się w ASP.NET Core). Nie używa `async`i dlatego:

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
 Response.Redirect("/");
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

W języku Java wylogowanie jest obsługiwane przez wywołanie punktu końcowego wylogowania platformy tożsamości firmy Microsoft bezpośrednio i udostępnienie post_logout_redirect_uri.

```Java
@RequestMapping("/msal4jsample/sign_out")
    public void signOut(HttpServletRequest httpRequest, HttpServletResponse response) throws IOException {

        httpRequest.getSession().invalidate();

        String endSessionEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/logout";

        String redirectUrl = "http://localhost:8080/msal4jsample/";
        response.sendRedirect(endSessionEndpoint + "?post_logout_redirect_uri=" +
                URLEncoder.encode(redirectUrl, "UTF-8"));
    }
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
@app.route("/logout")
def logout():
    return flask.redirect(flask.url_for('index'))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Przechwytywanie wywołania do `logout` punktu końcowego

Identyfikator URI po wylogowaniu umożliwia aplikacjom uczestnictwo w globalnych wylogowaniach.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core pośredniczące umożliwia aplikacji przechwycenie wywołania punktu końcowego platformy `logout` tożsamości firmy Microsoft przez podanie zdarzenia OpenIdConnect o nazwie. `OnRedirectToIdentityProviderForSignOut` Zobacz [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156) , aby zapoznać się z przykładem subskrybowania tego zdarzenia (w celu wyczyszczenia pamięci podręcznej tokenów)

```CSharp
               // Handling the global sign-out
                options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
                {
                    // Forget about the signed-in user
                };
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

W ASP.NET, który jest delegowany do oprogramowania pośredniczącego w celu wykonania wylogowania, wyczyszczenie pliku cookie sesji:

```CSharp
public class AccountController : Controller
{
 ...
 public void EndSession()
 {
  Request.GetOwinContext().Authentication.SignOut();
  Request.GetOwinContext().Authentication.SignOut(Microsoft.AspNet.Identity.DefaultAuthenticationTypes.ApplicationCookie);
  this.HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
 }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

W naszym przewodniku szybki start dla języka Java, identyfikator URI przekierowania po wylogowaniu po prostu wyświetla stronę index. html 

# <a name="pythontabpython"></a>[Python](#tab/python)

W przewodniku szybki start w języku Python identyfikator URI przekierowania po wylogowaniu po prostu wyświetla stronę index. html.

---

## <a name="protocol"></a>Protocol

Jeśli chcesz dowiedzieć się więcej o wylogowaniu, zapoznaj się z dokumentacją protokołu, która jest dostępna w programie [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przenieś do środowiska produkcyjnego](scenario-web-app-sign-user-production.md)
