---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 2b9d696ca896d0c8f0801f055000b9763d65d7ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60298200"
---
## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>dodawanie kontrolera do obsługi żądań logowania i wylogowania

W tym kroku przedstawiono sposób tworzenia nowego kontrolera do udostępnienia metody logowania i wylogowywania.

1.  Kliknij prawym przyciskiem myszy `Controllers` i wybierz polecenie `Add` > `Controller`
2.  Wybierz pozycję `MVC (.NET version) Controller – Empty`.
3.  Kliknij przycisk *Dodaj*
4.  Nadaj mu nazwę `HomeController` i kliknij przycisk *Dodaj*
5.  Dodaj *OWIN* odwołania do klasy:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```
    
6. Dodaj te dwie metody poniżej, aby obsłużyć logowania i wylogowania do kontrolera, inicjując wezwanie do uwierzytelnienia za pomocą kodu:
    
    ```csharp
    /// <summary>
    /// Send an OpenID Connect sign-in request.
    /// Alternatively, you can just decorate the SignIn method with the [Authorize] attribute
    /// </summary>
    public void SignIn()
    {
        if (!Request.IsAuthenticated)
        {
            HttpContext.GetOwinContext().Authentication.Challenge(
                new AuthenticationProperties{ RedirectUri = "/" },
                OpenIdConnectAuthenticationDefaults.AuthenticationType);
        }
    }
    
    /// <summary>
    /// Send an OpenID Connect sign-out request.
    /// </summary>
    public void SignOut()
    {
        HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
    }
    ```

## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>tworzenie strony głównej aplikacji do logowania użytkowników za pomocą przycisku logowania

W programie Visual Studio utwórz nowy widok, aby dodać przycisk logowania i wyświetlić informacje o użytkowniku po uwierzytelnieniu:

1.  Kliknij prawym przyciskiem myszy `Views\Home` i wybierz polecenie `Add View`
2.  Nadaj jej nazwę `Index`.
3.  Dodaj do pliku następujący kod HTML, który zawiera przycisk logowania:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Guide</title>
    </head>
    <body>
    @if (!Request.IsAuthenticated)
    {
        <!-- If the user is not authenticated, display the sign-in button -->
        <a href="@Url.Action("SignIn", "Home")" style="text-decoration: none;">
            <svg xmlns="http://www.w3.org/2000/svg" xml:space="preserve" width="300px" height="50px" viewBox="0 0 3278 522" class="SignInButton">
            <style type="text/css">.fil0:hover {fill: #4B4B4B;} .fnt0 {font-size: 260px;font-family: 'Segoe UI Semibold', 'Segoe UI'; text-decoration: none;}</style>
            <rect class="fil0" x="2" y="2" width="3174" height="517" fill="black" />
            <rect x="150" y="129" width="122" height="122" fill="#F35325" />
            <rect x="284" y="129" width="122" height="122" fill="#81BC06" />
            <rect x="150" y="263" width="122" height="122" fill="#05A6F0" />
            <rect x="284" y="263" width="122" height="122" fill="#FFBA08" />
            <text x="470" y="357" fill="white" class="fnt0">Sign in with Microsoft</text>
            </svg>
        </a>
    }
    else
    {
        <span><br/>Hello @System.Security.Claims.ClaimsPrincipal.Current.FindFirst("name").Value;</span>
        <br /><br />
        @Html.ActionLink("See Your Claims", "Index", "Claims")
        <br /><br />
        @Html.ActionLink("Sign out", "SignOut", "Home")
    }
    @if (!string.IsNullOrWhiteSpace(Request.QueryString["errormessage"]))
    {
        <div style="background-color:red;color:white;font-weight: bold;">Error: @Request.QueryString["errormessage"]</div>
    }
    </body>
    </html>
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>Więcej informacji
> Ta strona dodaje przycisk logowania w formacie SVG z czarnym tle:<br/>![Zaloguj się przy użyciu konta Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Więcej logowania przyciski, przejdź do [na tej stronie](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "wytyczne dotyczące oznaczania marką").
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Dodaj kontroler na wyświetlanie oświadczeń użytkownika
Ten kontroler pokazuje wykorzystanie atrybutu `[Authorize]` do ochrony kontrolera. Ten atrybut ogranicza dostęp do kontrolera, zezwalając na dostęp tylko uwierzytelnionym użytkownikom. Poniższy kod wykorzystuje atrybutu do wyświetlenia oświadczeń użytkowników, które zostały pobrane w ramach logowania.

1.  Kliknij prawym przyciskiem myszy `Controllers` folderu: `Add` > `Controller`
2.  Wybierz pozycję `MVC {version} Controller – Empty`.
3.  Kliknij przycisk *Dodaj*
4.  Nadaj jej nazwę `ClaimsController`
5.  Zastąp kod klasy kontrolera kodem poniżej — spowoduje to dodanie `[Authorize]` do klasy atrybutu:

    ```csharp
    [Authorize]
    public class ClaimsController : Controller
    {
        /// <summary>
        /// Add user's claims to viewbag
        /// </summary>
        /// <returns></returns>
        public ActionResult Index()
        {
            var userClaims = User.Identity as System.Security.Claims.ClaimsIdentity;
    
            //You get the user’s first and last name below:
            ViewBag.Name = userClaims?.FindFirst("name")?.Value;
    
            // The 'preferred_username' claim can be used for showing the username
            ViewBag.Username = userClaims?.FindFirst("preferred_username")?.Value;
    
            // The subject/ NameIdentifier claim can be used to uniquely identify the user across the web
            ViewBag.Subject = userClaims?.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier)?.Value;
    
            // TenantId is the unique Tenant Id - which represents an organization in Azure AD
            ViewBag.TenantId = userClaims?.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid")?.Value;
    
            return View();
        }
    }
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>Więcej informacji
> Z powodu użycia atrybutu `[Authorize]` wszystkie metody tego kontrolera można wykonać tylko wtedy, gdy użytkownik jest uwierzytelniony. Jeśli użytkownik nie jest uwierzytelniony, próbuje uzyskać dostęp z kontrolerem OWIN inicjują wezwanie do uwierzytelnienia i zmusza użytkownika do uwierzytelniania. Powyższy kod sprawdza lista oświadczeń dla określonego użytkownika atrybutów zawarte w tokenie identyfikatora użytkownika. Te atrybuty obejmują imię i nazwisko użytkownika oraz nazwę użytkownika, a także podmiot globalnego identyfikatora. Zawiera on także *identyfikator dzierżawy*, który reprezentuje identyfikator organizacji użytkownika. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>tworzenie widoku w celu wyświetlenia oświadczeń użytkownika

W programie Visual Studio utwórz nowy widok w celu wyświetlenia oświadczeń użytkownika na stronie internetowej:

1.  Kliknij prawym przyciskiem myszy `Views\Claims` folderu oraz: `Add View`
2.  Nadaj jej nazwę `Index`.
3.  Dodaj następujący kod HTML do pliku:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Sample</title>
        <link href="@Url.Content("~/Content/bootstrap.min.css")" rel="stylesheet" type="text/css" />
    </head>
    <body style="padding:50px">
        <h3>Main Claims:</h3>
        <table class="table table-striped table-bordered table-hover">
            <tr><td>Name</td><td>@ViewBag.Name</td></tr>
            <tr><td>Username</td><td>@ViewBag.Username</td></tr>
            <tr><td>Subject</td><td>@ViewBag.Subject</td></tr>
            <tr><td>TenantId</td><td>@ViewBag.TenantId</td></tr>
        </table>
        <br />
        <h3>All Claims:</h3>
        <table class="table table-striped table-bordered table-hover table-condensed">
        @foreach (var claim in System.Security.Claims.ClaimsPrincipal.Current.Claims)
        {
            <tr><td>@claim.Type</td><td>@claim.Value</td></tr>
        }
        </table>
        <br />
        <br />
        @Html.ActionLink("Sign out", "SignOut", "Home", null, new { @class = "btn btn-primary" })
    </body>
    </html>
    ```
