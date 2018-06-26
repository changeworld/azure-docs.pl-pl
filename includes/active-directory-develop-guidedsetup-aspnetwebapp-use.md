---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 98bb86be1e1d0dccb5a76b91489e664ee4a30765
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36943593"
---
## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Dodawanie kontrolera do obsługi żądań logowania i wylogowywania

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
    
6. Dodaj te dwie metody poniżej, aby obsłużyć logowania i wylogowywania do kontrolera, inicjując żądanie uwierzytelnienia za pomocą kodu:
    
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

## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Tworzenie strony głównej aplikacji do logowania użytkowników przy użyciu przycisku logowania

W programie Visual Studio Utwórz nowy widok, aby dodać przycisk Zarejestruj i wyświetlić informacje o użytkowniku po uwierzytelnieniu:

1.  Kliknij prawym przyciskiem myszy `Views\Home` i wybierz polecenie `Add View`
2.  Nadaj jej nazwę `Index`.
3.  Dodaj poniższy kod HTML, który zawiera przycisk Zarejestruj, do pliku:

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
> Ta strona dodaje przycisk Zarejestruj się w formacie SVG z czarnym tle:<br/>![Zaloguj się przy użyciu firmy Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Więcej logowania przycisków, przejdź do [tej strony](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "znakowanie wytyczne").
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Dodawanie kontrolera w celu wyświetlenia oświadczeń użytkownika
Ten kontroler pokazuje zastosowań `[Authorize]` atrybut do ochrony kontrolera. Ten atrybut ogranicza dostęp do kontrolera, zezwalając tylko użytkownicy uwierzytelnieni. Poniższy kod sprawia, że użycie atrybutu do wyświetlenia w ramach logowania w oświadczeń użytkowników, które zostały pobrane.

1.  Kliknij prawym przyciskiem myszy `Controllers` folderu: `Add` > `Controller`
2.  Wybierz pozycję `MVC {version} Controller – Empty`.
3.  Kliknij przycisk *Dodaj*
4.  Nadaj jej nazwę `ClaimsController`
5.  Zamień na kod klasy kontrolera kod poniżej — spowoduje to dodanie `[Authorize]` do klasy atrybutu:

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
> Z powodu użycia `[Authorize]` atrybutu, wszystkie metody tego kontrolera można wykonać tylko, jeśli użytkownik jest uwierzytelniony. Jeśli użytkownik nie jest uwierzytelniony i próbuje uzyskać dostęp z kontrolerem, OWIN inicjują żądania uwierzytelnienia i zmusza użytkownika do uwierzytelniania. Powyższy kod analizuje lista oświadczeń dla określonego użytkownika atrybuty uwzględnione w tokenie identyfikator użytkownika. Te atrybuty obejmują pełną nazwę użytkownika i nazwę użytkownika, a także temat identyfikator użytkownika globalne. Zawiera także *identyfikator dzierżawcy*, który reprezentuje identyfikator organizacji użytkownika. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Tworzenie widoku do wyświetlenia oświadczenia użytkownika

W programie Visual Studio Utwórz nowy widok, aby wyświetlić oświadczeń użytkownika na stronie sieci web:

1.  Kliknij prawym przyciskiem myszy `Views\Claims` folderu oraz: `Add View`
2.  Nadaj jej nazwę `Index`.
3.  Dodaj poniższy kod HTML do pliku:

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
