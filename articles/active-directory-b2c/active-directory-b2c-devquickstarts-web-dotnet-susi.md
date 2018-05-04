---
title: Resetowania hasła konta, uwierzytelnianie, ASP.NET Azure Active Directory B2C
description: Jak utworzyć aplikację sieci web, która ma konta-konta/logowania, edycji profilu i hasło resetowania przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: .net
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/17/2017
ms.author: davidmu
ms.openlocfilehash: 052d81f8170273bb0e40572322349550e7219363
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="create-an-aspnet-web-app-with-azure-active-directory-b2c-sign-up-sign-in-profile-edit-and-password-reset"></a>Tworzenie aplikacji sieci web platformy ASP.NET z usługi Azure Active Directory B2C profilu rejestracji, logowania, edycji i resetowania hasła

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Dodawanie funkcji tożsamości usługi Azure AD B2C do aplikacji sieci web
> * Rejestrowanie aplikacji sieci web w katalogu usługi Azure AD B2C
> * Tworzenie użytkownika konta-konta/logowania, edycji profilu i zasady resetowania hasła dla aplikacji sieci web

## <a name="prerequisites"></a>Wymagania wstępne

- Dzierżawy usługi B2C należy połączyć z kontem platformy Azure. Możesz utworzyć bezpłatne konto platformy Azure [tutaj](https://azure.microsoft.com/).
- Należy [programu Microsoft Visual Studio](https://www.visualstudio.com/) lub program podobne do wyświetlania i modyfikowania przykładowy kod.

## <a name="create-an-azure-ad-b2c-directory"></a>Tworzenie katalogu usługi Azure AD B2C

Przed rozpoczęciem korzystania z usługi Azure AD B2C należy utworzyć katalog lub dzierżawę. Katalog jest kontenerem dla wszystkich użytkowników, aplikacji, grup i inne. Jeśli nie masz już, tworzenia katalogu B2C, przed kontynuowaniem w tym przewodniku.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

> [!NOTE]
> 
> Musisz połączyć dzierżawy B2C do subskrypcji platformy Azure. Po wybraniu **Utwórz**, wybierz pozycję **dzierżawy łącze istniejącej usługi Azure AD B2C do subskrypcji platformy Azure** opcji, a następnie w **dzierżawy usługi Azure AD B2C** listy rozwijanej, wybierz dzierżawcy do skojarzenia.

## <a name="create-and-register-an-application"></a>Tworzenie i rejestrowania aplikacji

Następnie należy utworzyć i zarejestrować aplikację w katalogu usługi B2C. Zapewnia informacje wymagające usługi Azure AD B2C do bezpiecznego komunikowania się z aplikacją. 

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

Gdy wszystko będzie gotowe, będzie mieć zarówno interfejs API i aplikacji natywnej w ustawienia aplikacji.

## <a name="create-policies-on-your-b2c-tenant"></a>Tworzenie zasad na dzierżawę B2C

W usłudze Azure AD B2C każde działanie użytkownika jest definiowane przy użyciu [zasad](active-directory-b2c-reference-policies.md). Ten przykładowy kod obejmuje trzy środowiska tożsamości: **rejestracji i logowania**, **edycji profilu**, i **resetowania hasła**.  Dla każdego typu należy utworzyć jeden zbiór zasad zgodnie z opisem w [artykule o zasadach](active-directory-b2c-reference-policies.md). Dla każdej zasady upewnij się, aby wybrać atrybut nazwy wyświetlania lub oświadczeń i skopiuj nazwę tej zasady do późniejszego użycia.

### <a name="add-your-identity-providers"></a>Dodaj użytkownika dostawców tożsamości

Wybierz z ustawień **dostawców tożsamości** i wybierz polecenie zapisywania nazwy użytkownika lub rejestracji poczty E-mail.

### <a name="create-a-sign-up-and-sign-in-policy"></a>Tworzenie zasad rejestracji i logowania

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

### <a name="create-a-profile-editing-policy"></a>Tworzenie zasad edytowania profilów

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

### <a name="create-a-password-reset-policy"></a>Tworzenie zasad resetowania haseł

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

Po utworzeniu zasad, możesz przystąpić do tworzenia aplikacji.

## <a name="download-the-sample-code"></a>Pobierz przykładowy kod

Kod używany w tym samouczku [jest przechowywany w serwisie GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Przykład można sklonować, uruchamiając polecenie:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Po pobraniu przykładu kodu otwórz plik SLN programu Visual Studio, aby rozpocząć. Plik rozwiązania zawiera dwa projekty: `TaskWebApp` i `TaskService`. `TaskWebApp` to aplikacja sieci web MVC, którą użytkownik wchodzi w interakcję z. `TaskService` to interfejs API zaplecza aplikacji, który przechowuje listy zadań do wykonania poszczególnych użytkowników. Ten artykuł zawiera jedynie omówienie aplikacji `TaskWebApp`. Aby dowiedzieć się, jak tworzyć `TaskService` za pomocą usługi Azure AD B2C, zobacz [naszym samouczkiem interfejsu api sieci web .NET](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="update-code-to-use-your-tenant-and-policies"></a>Zaktualizuj kod, aby użyć zasad i dzierżawy

Nasz przykład został skonfigurowany do używania zasad i identyfikatora klienta dzierżawy pokazowej. Aby połączyć go z własną dzierżawę, należy otworzyć `web.config` w `TaskWebApp` projektu i Zastąp następujące wartości:

* `ida:Tenant` nazwą dzierżawy
* `ida:ClientId` identyfikatorem aplikacji sieci Web
* `ida:ClientSecret` kluczem tajnym aplikacji sieci Web
* `ida:SignUpSignInPolicyId` nazwą zasady tworzenia konta/logowania
* `ida:EditProfilePolicyId` nazwą zasady edycji profilu
* `ida:ResetPasswordPolicyId` nazwą zasady resetowania hasła

## <a name="launch-the-app"></a>Uruchom aplikację
Z poziomu programu Visual Studio, uruchom aplikację. Przejdź do karty listy zadań do wykonania i zanotuj adres URl jest: https://login.microsoftonline.com/ *YourTenantName*/oauth2/v2.0/authorize?p=*YourSignUpPolicyName*& client_id =*YourclientID*.....

Utwórz aplikację za pomocą poczty e-mail nazwy adres lub użytkownika. Wyloguj, a następnie zaloguj się ponownie i edytowanie profilu lub zresetować hasło. Wyloguj się i zaloguj się jako inny użytkownik. 

## <a name="add-social-idps"></a>Dodaj IDPs społecznościowych

Obecnie aplikacja obsługuje tylko użytkownik, zapisywania i zaloguj się przy użyciu **kont lokalnych**; kontom przechowywane w katalogu B2C, użyj nazwy użytkownika i hasła. Za pomocą usługi Azure AD B2C, można dodać obsługę innych **dostawców tożsamości** (IDPs) bez zmieniania żadnego kodu.

Aby dodać społecznościowych IDPs do aplikacji, Rozpocznij zgodnie z instrukcjami szczegółowe w tych artykułach. Dla każdego dostawców tożsamości, które mają być obsługiwane musisz zarejestrować aplikację w tym systemie i Uzyskaj identyfikator klienta.

* [Konfigurowanie usługi Facebook jako dostawca tożsamości](active-directory-b2c-setup-fb-app.md)
* [Konfigurowanie usługi Google jako dostawca tożsamości](active-directory-b2c-setup-goog-app.md)
* [Konfigurowanie usługi Amazon jako dostawca tożsamości](active-directory-b2c-setup-amzn-app.md)
* [Konfigurowanie LinkedIn jako dostawca tożsamości](active-directory-b2c-setup-li-app.md)

Po dodaniu dostawców tożsamości do katalogu usługi B2C edycję każdego z trzech zbiorów zasad uwzględnienie nowych IDPs, zgodnie z opisem w [artykule o zasadach](active-directory-b2c-reference-policies.md). Po zapisaniu zasad, uruchom ponownie aplikację.  Powinien zostać wyświetlony nowy IDPs, dodawane jako logowania oraz wrażenia opcji zapisywania w każdym z Twoją tożsamość.

Możesz eksperymentować z zasadami i obserwować wpływ na przykładowej aplikacji. Dodaj lub usuń IDPs, manipulować oświadczenia aplikacji lub zmień atrybuty rejestracji. Eksperymentu, aż zostanie wyświetlony, jak zasady, żądania uwierzytelniania i OWIN powiązać razem.

## <a name="sample-code-walkthrough"></a>Przykładowy kod wskazówki
Poniższe sekcje pokazują, jak przykładowy kod aplikacji jest skonfigurowana. Mogą wykorzystać te informacje jako przewodnika przy projektowaniu aplikacji w przyszłości.

### <a name="add-authentication-support"></a>Dodaj obsługę uwierzytelniania

Można teraz skonfigurować aplikację do używania usługi Azure AD B2C. Aplikacja komunikuje się z usługi Azure AD B2C, wysyłając żądania uwierzytelniania OpenID Connect. Żądania jest wymagane przez środowisko użytkownika, które chce wykonać określając zasady aplikacji. Biblioteka OWIN firmy Microsoft umożliwia wysyłać te żądania, wykonania zasady, zarządzania sesjami użytkownika i inne.

#### <a name="install-owin"></a>Instalowanie interfejsu OWIN

Aby rozpocząć, Dodaj pakiety NuGet oprogramowanie pośredniczące OWIN do projektu przy użyciu konsoli Menedżera pakietów programu Visual Studio.

```Console
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

#### <a name="add-an-owin-startup-class"></a>Dodawanie klasy początkowej OWIN

Dodaj klasę początkową OWIN do interfejsu API o nazwie `Startup.cs`.  Kliknij projekt prawym przyciskiem myszy, wybierz kolejno pozycje **Dodaj** i **Nowy element**, a następnie wyszukaj interfejs OWIN. Oprogramowanie pośredniczące OWIN wywoła metodę `Configuration(…)` podczas uruchamiania aplikacji.

W naszym przykładzie zmieniliśmy deklarację klasy na `public partial class Startup` i wdrożyliśmy drugą część klasy w `App_Start\Startup.Auth.cs`. Wewnątrz metody `Configuration` dodaliśmy wywołanie `ConfigureAuth`, które jest zdefiniowane w `Startup.Auth.cs`. Po modyfikacji `Startup.cs` wygląda podobnie do następujących:

```CSharp
// Startup.cs

public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        // ConfigureAuth defined in other part of the class
        ConfigureAuth(app);
    }
}
```

#### <a name="configure-the-authentication-middleware"></a>Konfigurowanie oprogramowania pośredniczącego uwierzytelniania

Otwórz plik `App_Start\Startup.Auth.cs` i wdrożenie `ConfigureAuth(...)` metody. Parametry podane `OpenIdConnectAuthenticationOptions` służyć jako współrzędnych dla aplikacji w celu komunikowania się z usługą Azure AD B2C. Jeśli nie określono niektórych parametrów, użyje wartości domyślnej. Na przykład firma Microsoft nie określaj `ResponseType` w próbce, dlatego wartość domyślna `code id_token` będzie służyć do wszystkich żądań wychodzących do usługi Azure AD B2C.

Należy również skonfigurować plik cookie uwierzytelniania. Oprogramowanie pośredniczące OpenID Connect używa plików cookie, aby zachować sesje użytkowników, między innymi.

```CSharp
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // Initialize variables ...

    // Configure the OWIN middleware
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseCookieAuthentication(new CookieAuthenticationOptions());
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseOpenIdConnectAuthentication(
            new OpenIdConnectAuthenticationOptions
            {
                // Generate the metadata address using the tenant and policy information
                MetadataAddress = String.Format(AadInstance, Tenant, DefaultPolicy),

                // These are standard OpenID Connect parameters, with values pulled from web.config
                ClientId = ClientId,
                RedirectUri = RedirectUri,
                PostLogoutRedirectUri = RedirectUri,

                // Specify the callbacks for each type of notifications
                Notifications = new OpenIdConnectAuthenticationNotifications
                {
                    RedirectToIdentityProvider = OnRedirectToIdentityProvider,
                    AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    AuthenticationFailed = OnAuthenticationFailed,
                },

                // Specify the claims to validate
                TokenValidationParameters = new TokenValidationParameters
                {
                    NameClaimType = "name"
                },

                // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
                Scope = $"openid profile offline_access {ReadTasksScope} {WriteTasksScope}"
            }
        );
    }

    // Implement the "Notification" methods...
}
```

W `OpenIdConnectAuthenticationOptions` powyżej, definiujemy zestaw funkcji wywołania zwrotnego dla określonych powiadomień, które są odbierane przez oprogramowanie pośredniczące OpenID Connect. Te zachowania są definiowane przy użyciu `OpenIdConnectAuthenticationNotifications` obiektu i przechowywane w `Notifications` zmiennej. W naszym przykładzie definiujemy trzech różnych wywołań zwrotnych w zależności od zdarzenia.

### <a name="using-different-policies"></a>Przy użyciu różnych zasad

`RedirectToIdentityProvider` Powiadomień jest zawsze wyzwalane, gdy żądań do usługi Azure AD B2C. W funkcji wywołania zwrotnego `OnRedirectToIdentityProvider`, sprawdzamy w wywołaniu wychodzących, jeśli chcemy użyć różnych zasad. Aby nie resetowania haseł lub edytowanie profilu, trzeba użyć odpowiednich zasad, takich jak zasady, zamiast domyślnej zasady "Rejestracji i logowania" resetowania hasła.

W naszym przykładzie gdy użytkownik chce, aby zresetować hasło lub edytowanie profilu, dodamy zasady, które firma Microsoft nie chce używać w kontekście OWIN. Który może odbywać się w następujący sposób:

```CSharp
    // Let the middleware know you are trying to use the edit profile policy
    HttpContext.GetOwinContext().Set("Policy", EditProfilePolicyId);
```

I funkcja wywołania zwrotnego można zaimplementować `OnRedirectToIdentityProvider` przez wykonanie następujących czynności:

```CSharp
/*
*  On each call to Azure AD B2C, check if a policy (e.g. the profile edit or password reset policy) has been specified in the OWIN context.
*  If so, use that policy when making the call. Also, don't request a code (since it won't be needed).
*/
private Task OnRedirectToIdentityProvider(RedirectToIdentityProviderNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    var policy = notification.OwinContext.Get<string>("Policy");

    if (!string.IsNullOrEmpty(policy) && !policy.Equals(DefaultPolicy))
    {
        notification.ProtocolMessage.Scope = OpenIdConnectScopes.OpenId;
        notification.ProtocolMessage.ResponseType = OpenIdConnectResponseTypes.IdToken;
        notification.ProtocolMessage.IssuerAddress = notification.ProtocolMessage.IssuerAddress.Replace(DefaultPolicy, policy);
    }

    return Task.FromResult(0);
}
```

### <a name="handling-authorization-codes"></a>Obsługa kodów autoryzacji

`AuthorizationCodeReceived` Powiadomień jest wyzwalane po odebraniu kod autoryzacji. Oprogramowanie pośredniczące OpenID Connect nie obsługuje kodów wymianę tokenów dostępu. Należy ręcznie wymienić kod tokenu w funkcji wywołania zwrotnego. Aby uzyskać więcej informacji, zapoznaj się [dokumentacji](active-directory-b2c-devquickstarts-web-api-dotnet.md) objaśniający sposób.

### <a name="handling-errors"></a>Obsługa błędów

`AuthenticationFailed` Powiadomień jest wyzwalane, gdy uwierzytelnianie nie powiedzie się. Metody wywołania zwrotnego może obsługiwać błędy, zgodnie z wymaganiami. Należy jednak dodać wyboru dla kodu błędu `AADB2C90118`. Podczas wykonywania zasady "Rejestracji i logowania", użytkownik ma możliwość wybierz **nie pamiętasz hasła?** łącza. W takim przypadku usługi Azure AD B2C wysyła aplikacji, ten kod błędu wskazujący, czy aplikacji należy wysłać żądanie, zamiast zasady resetowania hasła.

```CSharp
/*
* Catch any failures received by the authentication middleware and handle appropriately
*/
private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    notification.HandleResponse();

    // Handle the error code that Azure AD B2C throws when trying to reset a password from the login page
    // because password reset is not supported by a "sign-up or sign-in policy"
    if (notification.ProtocolMessage.ErrorDescription != null && notification.ProtocolMessage.ErrorDescription.Contains("AADB2C90118"))
    {
        // If the user clicked the reset password link, redirect to the reset password route
        notification.Response.Redirect("/Account/ResetPassword");
    }
    else if (notification.Exception.Message == "access_denied")
    {
        notification.Response.Redirect("/");
    }
    else
    {
        notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
    }

    return Task.FromResult(0);
}
```

### <a name="send-authentication-requests-to-azure-ad"></a>Wysyłanie żądania uwierzytelniania do usługi Azure AD

Aplikacji jest teraz prawidłowo skonfigurowana do komunikowania się z usługą Azure AD B2C przy użyciu protokołu uwierzytelniania OpenID Connect. OWIN zarządza szczegółowe informacje, obsługuje tworzenie komunikatów uwierzytelniania, sprawdzanie poprawności tokenów z usługi Azure AD B2C i podtrzymywanie sesji użytkowników. Wszystkie opcje, które pozostaje ma inicjować przepływ każdego użytkownika.

Gdy użytkownik wybierze **up logowania/logowania**, **edytowanie profilu**, lub **resetowania hasła** w aplikacji sieci web skojarzonej akcji jest wywoływana w `Controllers\AccountController.cs`:

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign up or sign in
*/
public void SignUpSignIn()
{
    // Use the default policy to process the sign up / sign in flow
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge();
        return;
    }

    Response.Redirect("/");
}

/*
*  Called when requesting to edit a profile
*/
public void EditProfile()
{
    if (Request.IsAuthenticated)
    {
        // Let the middleware know you are trying to use the edit profile policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
        HttpContext.GetOwinContext().Set("Policy", Startup.EditProfilePolicyId);

        // Set the page to redirect to after editing the profile
        var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
        HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

        return;
    }

    Response.Redirect("/");

}

/*
*  Called when requesting to reset a password
*/
public void ResetPassword()
{
    // Let the middleware know you are trying to use the reset password policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
    HttpContext.GetOwinContext().Set("Policy", Startup.ResetPasswordPolicyId);

    // Set the page to redirect to after changing passwords
    var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
    HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

    return;
}
```

Aby się wylogować użytkownika z poziomu aplikacji umożliwia także OWIN. W `Controllers\AccountController.cs` mamy:

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign out
*/
public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request.
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

Oprócz jawnie wywoływanie zasad, można użyć `[Authorize]` tag w kontrolerach, który wykonuje zasadę, jeśli użytkownik nie jest zalogowany. Otwórz `Controllers\HomeController.cs` i Dodaj `[Authorize]` tag do kontrolera oświadczeń.  Wybiera OWIN ostatnio zasady skonfigurowane podczas obliczania `[Authorize]` tag zostaje trafiony.

```CSharp
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

### <a name="display-user-information"></a>Wyświetl informacje o użytkowniku

Podczas uwierzytelniania użytkowników za pomocą protokołu OpenID Connect, usługi Azure AD B2C zwraca token identyfikator aplikacji, która zawiera **oświadczeń**. Te są potwierdzeniami dotyczącymi użytkownika. Aby spersonalizować aplikacji może użyć oświadczeń.

Otwórz plik `Controllers\HomeController.cs`. Oświadczenia użytkowników są dostępne w kontrolerach za pośrednictwem `ClaimsPrincipal.Current` obiekt główny zabezpieczeń.

```CSharp
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

Możesz uzyskać dostęp, że aplikacja odbiera w taki sam sposób.  Lista wszystkich oświadczeń aplikacja odbiera jest dostępne na **oświadczeń** strony.
