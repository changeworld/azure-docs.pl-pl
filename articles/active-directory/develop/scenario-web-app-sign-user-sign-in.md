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
ms.openlocfilehash: 09b39cb9db2450b7d200ec725396141f72f1b2f1
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310031"
---
# <a name="web-app-that-signs-in-users---sign-in-and-sign-out"></a>Aplikacja internetowa, która loguje się do użytkowników — Zaloguj się i wyloguj

Dowiedz się, jak dodać logowanie do kodu aplikacji sieci Web, które logują się do użytkowników, a następnie, jak umożliwić im wylogowywanie

## <a name="sign-in"></a>Logowanie

Logowanie jest wprowadzane przez dwie części:

- przycisk logowania na stronie HTML
- Akcja logowania w kodzie znajdującym się w kontrolerze

### <a name="sign-in-button"></a>Przycisk logowania

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

W ASP.NET Core przycisk logowania jest uwidaczniany w `Views\Shared\_LoginPartial.cshtml` usłudze i wyświetlany tylko wtedy, gdy nie ma konta uwierzytelnionego (to oznacza, że użytkownik nie został jeszcze zalogowany lub wyloguje się).

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
 // Code omitted code for clarity
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
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

W naszym przewodniku szybki start dla języka Java przycisk Wyloguj znajduje się w pliku [Main/Resources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html)

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>HomePage</title>
</head>
<body>
<h3>Home Page</h3>

<form action="/msal4jsample/secure/aad">
    <input type="submit" value="Login">
</form>

</body>
</html>
```

# <a name="pythontabpython"></a>[Python](#tab/python)

W przewodniku szybki start dla języka Python nie ma przycisku logowania. Użytkownik jest automatycznie monitowany o zalogowanie się za pomocą kodu w momencie osiągnięcia katalogu głównego aplikacji sieci Web. Zobacz [App. PR # L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18)

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="login-action-of-the-controller"></a>`login()`Akcja kontrolera

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

W programie ASP.NET naciśnięcie przycisku **logowania** w aplikacji sieci Web wyzwala `SignIn` akcję na `AccountController` kontrolerze. W poprzednich wersjach szablonów `Account` ASP.NET Core kontroler został osadzony w aplikacji sieci Web, ale nie jest już tak samo, jak teraz jest częścią samej struktury ASP.NET Core.

Kod `AccountController` jest dostępny w repozytorium ASP.NET Core z [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Kontrola konta wzywa użytkownika przez przekierowanie do punktu końcowego platformy tożsamości firmy Microsoft. Aby uzyskać szczegółowe informacje, zobacz metodę [logowania](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31) podaną jako część ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

W ASP.NET wylogowanie jest wyzwalane z `SignOut()` metody na kontrolerze (na przykład [elementu AccountController. cs # L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Ta metoda nie jest częścią ASP.NET Framework (w przeciwieństwie do co dzieje się w ASP.NET Core). Go

- wysyła wyzwanie logowania OpenID Connect po zaproponowaniu identyfikatora URI przekierowania

```CSharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

W języku Java wylogowanie jest obsługiwane przez wywołanie punktu końcowego wylogowania platformy tożsamości firmy Microsoft bezpośrednio i udostępnienie post_logout_redirect_uri. Aby uzyskać szczegółowe informacje, zobacz [AuthPageController. Java # L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48)

```Java
@Controller
public class AuthPageController {

    @Autowired
    AuthHelper authHelper;

    @RequestMapping("/msal4jsample")
    public String homepage(){
        return "index";
    }

    @RequestMapping("/msal4jsample/secure/aad")
    public ModelAndView securePage(HttpServletRequest httpRequest) throws ParseException {
        ModelAndView mav = new ModelAndView("auth_page");

        setAccountInfo(mav, httpRequest);

        return mav;
    }

    // More code omitted for simplicity
```

# <a name="pythontabpython"></a>[Python](#tab/python)

W przeciwieństwie do innych platform, MSAL. W języku Python należy zadbać o to, aby użytkownik zalogować się na stronie logowania. Zobacz [App. PR # L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28)

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use empty list [] to just sign in,
                           # here we choose to also collect end user consent upfront
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

za pomocą metody _build_msal_app () zdefiniowanej w [App. PR # L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) w następujący sposób:

```Python
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

def _get_token_from_cache(scope=None):
    cache = _load_cache()  # This web app maintains one cache per session
    cca = _build_msal_app(cache)
    accounts = cca.get_accounts()
    if accounts:  # So all account(s) belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

Gdy użytkownik zaloguje się do aplikacji, prawdopodobnie trzeba będzie ją wylogować.

## <a name="sign-out"></a>Wyloguj

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

Podczas rejestracji aplikacji nie trzeba rejestrować dodatkowego adresu URL wylogowywania. Aplikacja zostanie wywołana z powrotem na swoim głównym adresie URL

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

W przewodniku szybki start w języku Python przycisk Wyloguj znajduje się w pliku [templates/index. html # L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python Web App</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`Signout()`Akcja kontrolera

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

W ASP.NET naciśnięcie przycisku **Wyloguj** w aplikacji sieci Web wyzwala `SignOut` akcję na `AccountController` kontrolerze. W poprzednich wersjach szablonów `Account` ASP.NET Core kontroler został osadzony w aplikacji sieci Web, ale nie jest już tak samo, jak teraz jest częścią samej struktury ASP.NET Core.

Kod `AccountController` jest dostępny w repozytorium ASP.NET Core w lokalizacji z [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Kontrola konta:

- Ustawia identyfikator URI przekierowania OpenID Connect `/Account/SignedOut` na tak, aby kontroler został wywołany, gdy usługa Azure AD zakończy wylogowywanie
- Wywołania `Signout()`, które umożliwiają oprogramowanie pośredniczące OpenIdConnect kontaktują się z punktem `logout` końcowym platformy tożsamości firmy Microsoft, który:

  - Czyści plik cookie sesji z przeglądarki i
  - na koniec wywołuje **adres URL wylogowania**, który domyślnie wyświetla stronę ze stroną widoku wylogowanego [SignedOut. html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) , która jest dostępna jako część ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

W ASP.NET wylogowanie jest wyzwalane z `SignOut()` metody na kontrolerze (na przykład [elementu AccountController. cs # L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Ta metoda nie jest częścią ASP.NET Framework (w przeciwieństwie do co dzieje się w ASP.NET Core). Go

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

W języku Java wylogowanie jest obsługiwane przez wywołanie punktu końcowego wylogowania platformy tożsamości firmy Microsoft bezpośrednio i udostępnienie post_logout_redirect_uri. Aby uzyskać szczegółowe informacje, zobacz [AuthPageController. Java # L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60)

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

Kod, który wyloguje użytkownika, znajduje się w [aplikacji App. PR # L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L46-L52) 

```Python
@app.route("/logout")
def logout():
    session["user"] = None  # Log out from this app from its session
    # session.clear()  # If you prefer, this would nuke the user's token cache too
    return redirect(  # Also need to logout from Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
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
