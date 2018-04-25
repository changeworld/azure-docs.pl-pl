---
title: "Wprowadzenie logowania aplikacji sieci web usługi Azure AD 2.0 .NET | Dokumentacja firmy Microsoft"
description: "Sposób tworzenia aplikacji sieci Web .NET MVC logujący się użytkowników przy użyciu obu Account firmy Microsoft i konta służbowego."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mtillman
editor: 
ms.assetid: c8b97ac6-0a06-4367-81b6-7d1d98152b14
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: a23b3b1084cf6776cee8583891ae3d879183d072
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2018
---
# <a name="add-sign-in-to-an-net-mvc-web-app"></a>Dodaj logowanie do aplikacji sieci web platformy .NET MVC
Z punktem końcowym v2.0 można szybko dodać konta służbowego i uwierzytelniania do aplikacji sieci web z obsługą oba osobistego konta Microsoft.  W aplikacji sieci web ASP.NET można to zrobić za pomocą oprogramowania pośredniczącego OWIN firmy Microsoft włączone w programie .NET Framework 4.5.

> [!NOTE]
> Nie wszystkie usługi Azure Active Directory scenariuszy i funkcji obsługiwanych przez punktu końcowego v2.0.  Aby ustalić, czy należy używać punktu końcowego v2.0, przeczytaj o [ograniczenia v2.0](active-directory-v2-limitations.md).
>
>

 W tym miejscu będzie budujemy aplikacji sieci web, która używa OWIN zalogować użytkownika, wyświetlane informacje o użytkowniku i zalogować użytkownika poza aplikacją.

## <a name="download"></a>Do pobrania
Kod używany w tym samouczku jest przechowywany [ w serwisie GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet).  Aby z niego skorzystać, można [pobrać szkielet aplikacji w formie .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) lub sklonować szkielet:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

Ukończonej aplikacji znajduje się na końcu również w tym samouczku.

## <a name="register-an-app"></a>Rejestracja aplikacji
Utwórz nową aplikację na [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), lub wykonaj następujące [szczegółowe kroki](active-directory-v2-app-registration.md).  Upewnij się, że:

* Skopiuj **identyfikator aplikacji** przypisany do aplikacji, będzie on potrzebny wkrótce.
* Dodaj **Web** platformy aplikacji.
* Wprowadź poprawny **identyfikator URI przekierowania**. Wskazuje identyfikator uri przekierowania do usługi Azure AD, w którym powinny być kierowane odpowiedzi uwierzytelniania — wartością domyślną tego samouczka jest `https://localhost:44326/`.

## <a name="install--configure-owin-authentication"></a>Instalowanie i konfigurowanie uwierzytelniania OWIN
W tym miejscu skonfigurujemy oprogramowania pośredniczącego OWIN do zastosowania protokołu uwierzytelniania OpenID Connect.  OWIN będzie służyć do wysyłania żądań zalogowania się i wylogowania, zarządzania sesji użytkownika i uzyskać informacje o użytkowniku, między innymi.

1. Aby rozpocząć, otwórz `web.config` plików w folderze głównym projektu, a następnie wprowadź wartości konfiguracji aplikacji w `<appSettings>` sekcji.

  * `ida:ClientId` Jest **identyfikator aplikacji** przypisany do aplikacji w portalu rejestracji.
  * `ida:RedirectUri` Jest **identyfikator Uri przekierowania** wprowadzony w portalu.

2. Następnie dodaj pakiety NuGet oprogramowanie pośredniczące OWIN do projektu przy użyciu konsoli Menedżera pakietów.

        ```
        PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
        PM> Install-Package Microsoft.Owin.Security.Cookies
        PM> Install-Package Microsoft.Owin.Host.SystemWeb
        ```  

3. Dodaj "Klasę początkową OWIN" do projektu o nazwie `Startup.cs` --> kliknij projekt prawym **Dodaj** --> **nowy element** --> Wyszukaj "OWIN".  Oprogramowanie pośredniczące OWIN wywoła metodę `Configuration(...)` podczas uruchamiania aplikacji.
4. Deklaracja klasy, aby zmienić `public partial class Startup` — już zaimplementowano część tej klasy dla Ciebie w innym pliku.  W `Configuration(...)` metody, utworzyć ConfigureAuth(...) wywołana w celu ustawienia uwierzytelniania dla aplikacji sieci web  

        ```csharp
        [assembly: OwinStartup(typeof(Startup))]
        
        namespace TodoList_WebApp
        {
            public partial class Startup
            {
                public void Configuration(IAppBuilder app)
                {
                    ConfigureAuth(app);
                }
            }
        }
        ```

5. Otwórz plik `App_Start\Startup.Auth.cs` i wdrożenie `ConfigureAuth(...)` metody.  Parametry podane `OpenIdConnectAuthenticationOptions` będzie służyć jako współrzędnych dla aplikacji w celu komunikowania się z usługą Azure AD.  Należy również skonfigurować uwierzytelniania plików Cookie — oprogramowanie pośredniczące OpenID Connect używa plików cookie poniżej obejmuje.

        ```csharp
        public void ConfigureAuth(IAppBuilder app)
                     {
                             app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);
        
                             app.UseCookieAuthentication(new CookieAuthenticationOptions());
        
                             app.UseOpenIdConnectAuthentication(
                                     new OpenIdConnectAuthenticationOptions
                                     {
                                             // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
                                             // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
                                             // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.
        
                                             ClientId = clientId,
                                             Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
                                             RedirectUri = redirectUri,
                                             Scope = "openid email profile",
                                             ResponseType = "id_token",
                                             PostLogoutRedirectUri = redirectUri,
                                             TokenValidationParameters = new TokenValidationParameters
                                             {
                                                     ValidateIssuer = false,
                                             },
                                             Notifications = new OpenIdConnectAuthenticationNotifications
                                             {
                                                     AuthenticationFailed = OnAuthenticationFailed,
                                             }
                                     });
                     }
        ```

## <a name="send-authentication-requests"></a>Wysyłanie żądania uwierzytelniania
Aplikacji jest teraz prawidłowo skonfigurowana do komunikowania się z punktem końcowym v2.0 za pomocą protokołu uwierzytelniania OpenID Connect.  Wszystkie szczegóły ugly obsługuje tworzenie komunikatów uwierzytelniania, sprawdzanie poprawności tokenów z usługi Azure AD i podtrzymywanie sesji użytkowników ma podjąć obsługę OWIN.  Wszystko, co jest pozostaje, aby zapewnić użytkownikom możliwość zalogować się i wylogowywania.

- Można użyć znaczników w kontrolerach wymaganie, że użytkownik loguje się przed uzyskaniem dostępu do konkretnej strony autoryzacji.  Otwórz `Controllers\HomeController.cs`i Dodaj `[Authorize]` tag do kontrolera informacje.
        
        ```csharp
        [Authorize]
        public ActionResult About()
        {
          ...
        ```

- OWIN umożliwia również wystawić żądania uwierzytelniania od bezpośrednio w kodzie.  Otwórz `Controllers\AccountController.cs`.  W działaniach SignIn() i SignOut() wystawić odpowiednio OpenID Connect żądania i wylogowania żądania.

        ```csharp
        public void SignIn()
        {
            // Send an OpenID Connect sign-in request.
            if (!Request.IsAuthenticated)
            {
                HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
            }
        }
        
        // BUGBUG: Ending a session with the v2.0 endpoint is not yet supported.  Here, we just end the session with the web app.  
        public void SignOut()
        {
            // Send an OpenID Connect sign-out request.
            HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
            Response.Redirect("/");
        }
        ```

- Teraz Otwórz `Views\Shared\_LoginPartial.cshtml`.  Jest to, której będzie wyświetlany, gdy użytkownik łączy logowania i wylogowywania aplikacji, a następnie wydrukuj nazwy użytkownika w widoku.

        ```HTML
        @if (Request.IsAuthenticated)
        {
            <text>
                <ul class="nav navbar-nav navbar-right">
                    <li class="navbar-text">
        
                        @*The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves.*@
        
                        Hello, @(System.Security.Claims.ClaimsPrincipal.Current.FindFirst("preferred_username").Value)!
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

## <a name="display-user-information"></a>Wyświetl informacje o użytkowniku
Podczas uwierzytelniania użytkowników z OpenID Connect, punktu końcowego v2.0 zwraca żądaniu aplikacja, która zawiera oświadczenia lub potwierdzeniami dotyczącymi użytkownika.  Może użyć tych oświadczeń spersonalizować aplikacji:

- Otwórz plik `Controllers\HomeController.cs`.  Dostęp do oświadczeń użytkownika w kontrolerach za pośrednictwem `ClaimsPrincipal.Current` obiekt główny zabezpieczeń.

        ```csharp
        [Authorize]
        public ActionResult About()
        {
            ViewBag.Name = ClaimsPrincipal.Current.FindFirst("name").Value;
        
            // The object ID claim will only be emitted for work or school accounts at this time.
            Claim oid = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier");
            ViewBag.ObjectId = oid == null ? string.Empty : oid.Value;
        
            // The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves
            ViewBag.Username = ClaimsPrincipal.Current.FindFirst("preferred_username").Value;
        
            // The subject or nameidentifier claim can be used to uniquely identify the user
            ViewBag.Subject = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        
            return View();
        }
        ```

## <a name="run"></a>Uruchom polecenie
Na koniec Skompiluj i uruchom aplikację!   Zaloguj się przy użyciu Account firmy Microsoft lub konta służbowego i zwróć uwagę, jak tożsamość użytkowników jest widoczny w górnym pasku nawigacyjnym.  Masz teraz zabezpieczone przy użyciu standardowych protokołach branżowych, uwierzytelniających użytkowników przy użyciu ich osobistych i pracy/służbowego konta aplikacji sieci web.

Próbka ukończone (bez wartości konfiguracji) [jest dostarczane jako zip w tym miejscu](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip), lub można ją sklonować z serwisu GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

## <a name="next-steps"></a>Kolejne kroki
Możesz teraz przejść do bardziej zaawansowanych tematów.  Można spróbować:

[Zabezpieczanie interfejsu API sieci Web, z punktem końcowym v2.0 >>](active-directory-devquickstarts-webapi-dotnet.md)

Aby uzyskać dodatkowe zasoby Zobacz:

* [Przewodnik dewelopera v2.0 >>](active-directory-appmodel-v2-overview.md)
* [Tag StackOverflow "azure-active-directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Pobierz aktualizacje zabezpieczeń naszych produktów
Firma Microsoft zachęca do przekazywania powiadomień o występujących incydentach zabezpieczeń poprzez wizytę na [tej stronie](https://technet.microsoft.com/security/dd252948) i subskrybowanie Doradczych alertów zabezpieczeń.
