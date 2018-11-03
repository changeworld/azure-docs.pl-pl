---
title: Uwierzytelnianie, zarejestruj się, resetowania haseł w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Jak utworzyć aplikację sieci web, która zawiera konta-dokonywania/Zaloguj się i edytowania profilu, resetowania haseł za pomocą usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1db805efe7eaec77fcafeb169b3d99098b57f582
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978982"
---
# <a name="create-an-aspnet-web-app-with-azure-active-directory-b2c-sign-up-sign-in-profile-edit-and-password-reset"></a>Tworzenie aplikacji sieci web platformy ASP.NET za pomocą usługi Azure Active Directory B2C profilu rejestracji, logowania, edycji i resetowania haseł

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Dodaj funkcje obsługi tożsamości usługi Azure AD B2C w aplikacji sieci web
> * Zarejestruj swoją aplikację sieci web w katalogu usługi Azure AD B2C
> * Tworzenie użytkownika konta-dokonywania/logowania, edytowania profilów i zasady resetowania hasła dla aplikacji sieci web

## <a name="prerequisites"></a>Wymagania wstępne

- Dzierżawy usługi B2C należy połączyć z kontem platformy Azure. Można utworzyć bezpłatne konto platformy Azure [tutaj](https://azure.microsoft.com/).
- Potrzebujesz [programu Microsoft Visual Studio](https://www.visualstudio.com/) lub podobny do wyświetlanie i modyfikowanie przykładowego kodu.

## <a name="create-an-azure-ad-b2c-tenant"></a>Tworzenie dzierżawy usługi Azure AD B2C

Zanim użyjesz usługi Azure AD B2C, trzeba utworzyć dzierżawę. Dzierżawa jest kontenerem dla wszystkich użytkowników, aplikacji, grup i. Jeśli nie masz jeszcze, należy utworzyć dzierżawę B2C przed rozpoczęciem pracy w tym przewodniku.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

> [!NOTE]
> 
> Należy połączyć z dzierżawy usługi Azure AD B2C z subskrypcją platformy Azure. Po wybraniu **Utwórz**, wybierz opcję **dzierżawy łączy istniejące usługi Azure AD B2C do mojej subskrypcji platformy Azure** opcji, a następnie w **dzierżawy usługi Azure AD B2C** listę rozwijaną, wybierz opcję dzierżawy, który chcesz skojarzyć.

## <a name="create-and-register-an-application"></a>Tworzenie i rejestrowanie aplikacji

Następnie należy utworzyć i zarejestrować aplikację w dzierżawie usługi Azure AD B2C. Dzięki temu informacje wymagające bezpiecznego komunikowania się z aplikacją usługi Azure AD B2C. 

Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**. Teraz powinien być używany dzierżawy, która została wcześniej utworzona.

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

Gdy wszystko będzie gotowe, konieczne będzie interfejsu API i aplikacji macierzystej w ustawieniach aplikacji.

## <a name="create-policies-on-your-b2c-tenant"></a>Tworzenie zasad na swoją dzierżawę B2C

W usłudze Azure AD B2C każde działanie użytkownika jest definiowane przy użyciu [zasad](active-directory-b2c-reference-policies.md). Ten przykładowy kod obejmuje trzy środowiska tożsamości: **rejestracji i logowania**, **profilu edycji**, i **resetowania hasła**.  Dla każdego typu należy utworzyć jeden zbiór zasad zgodnie z opisem w [artykule o zasadach](active-directory-b2c-reference-policies.md). Dla każdej zasady upewnij się, aby wybrać atrybut nazwy wyświetlania lub oświadczeń i skopiuj nazwę swoich zasad w celu późniejszego użycia.

### <a name="add-your-identity-providers"></a>Dodaj dostawców tożsamości

Wybierz z ustawień, **dostawców tożsamości** i wybierz polecenie rejestrowania nazwy użytkownika lub rejestracja E-mail.

### <a name="create-a-sign-up-and-sign-in-policy"></a>Tworzenie zasad rejestracji i logowania

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

### <a name="create-a-profile-editing-policy"></a>Tworzenie zasad edytowania profilów

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

### <a name="create-a-password-reset-policy"></a>Tworzenie zasad resetowania haseł

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

Po utworzeniu zasad możesz rozpocząć tworzenie aplikacji.

## <a name="download-the-sample-code"></a>Pobierz przykładowy kod

Kod używany w tym samouczku [jest przechowywany w serwisie GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Przykład można sklonować, uruchamiając polecenie:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Po pobraniu przykładu kodu otwórz plik SLN programu Visual Studio, aby rozpocząć. Plik rozwiązania zawiera dwa projekty: `TaskWebApp` i `TaskService`. `TaskWebApp` to aplikacja sieci web MVC, który użytkownik wchodzi w interakcję z. `TaskService` to interfejs API zaplecza aplikacji, który przechowuje listy zadań do wykonania poszczególnych użytkowników. Ten artykuł zawiera jedynie omówienie aplikacji `TaskWebApp`. Aby dowiedzieć się, jak tworzyć `TaskService` za pomocą usługi Azure AD B2C, zobacz [Nasz samouczek dotyczący interfejsu api sieci web platformy .NET](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="update-code-to-use-your-tenant-and-policies"></a>Zaktualizuj kod w celu użycia zasad i dzierżawy

Nasz przykład został skonfigurowany do używania zasad i identyfikatora klienta dzierżawy pokazowej. Aby połączyć go z własną dzierżawę, należy otworzyć `web.config` w `TaskWebApp` projektu i Zastąp następujące wartości:

* `ida:Tenant` nazwą dzierżawy
* `ida:ClientId` identyfikatorem aplikacji internetowej
* `ida:ClientSecret` kluczem tajnym aplikacji internetowej
* `ida:SignUpSignInPolicyId` nazwą zasady tworzenia konta/logowania
* `ida:EditProfilePolicyId` nazwą zasady edycji profilu
* `ida:ResetPasswordPolicyId` nazwą zasady resetowania hasła

## <a name="launch-the-app"></a>Uruchom aplikację
Z poziomu programu Visual Studio, uruchom aplikację. Przejdź do karty listy zadań do wykonania i zwróć uwagę, adres URl: https://*YourTenantName*.b2clogin.com/*YourTenantName*/oauth2/v2.0/authorize?p=*YourSignUpPolicyName* & client_id =*YourclientID*...

Zarejestruj się w aplikacji przy użyciu nazwy użytkownika lub adres e-mail. Wyloguj się, a następnie zaloguj się ponownie i dokonać edycji profilu lub zresetować hasło. Wyloguj się i zaloguj się jako inny użytkownik. 

## <a name="add-social-idps"></a>Dodaj dostawców tożsamości społecznościowych

Obecnie aplikacja obsługuje tylko użytkownika, zarejestruj się i zaloguj się przy użyciu **kont lokalnych**; kont przechowywane w katalogu usługi B2C, używające nazwy użytkownika i hasła. Za pomocą usługi Azure AD B2C, możesz dodać obsługę innych **dostawców tożsamości** (IDPs) bez konieczności zmieniania żadnego kodu.

Aby dodać społecznościowych dostawców tożsamości do aplikacji, należy rozpocząć zgodnie z instrukcjami szczegółowe w następujących artykułach. Dla każdego dostawcy tożsamości, które mają być obsługiwane należy zarejestrować aplikację w tym systemie i uzyskać identyfikator klienta.

* [Konfigurowanie usługi Facebook jako dostawcy tożsamości](active-directory-b2c-setup-fb-app.md)
* [Konfigurowanie Google jako dostawcy tożsamości](active-directory-b2c-setup-goog-app.md)
* [Konfigurowanie Amazon jako dostawcy tożsamości](active-directory-b2c-setup-amzn-app.md)
* [Konfigurowanie usługi LinkedIn jako dostawcy tożsamości](active-directory-b2c-setup-li-app.md)

Po dodaniu dostawcy tożsamości do katalogu usługi B2C edycję każdego z trzech zbiorów zasad w celu uwzględnienia nowych dostawców tożsamości, zgodnie z opisem w [artykule dotyczącym struktury zasad](active-directory-b2c-reference-policies.md). Po zapisaniu zasad, należy ponownie uruchomić tę aplikację.  Powinien zostać wyświetlony nowy dostawców tożsamości, dodany jako logowania i środowisk opcji rejestracji w każdym z Twoją tożsamość.

Możesz eksperymentować z zasadami dotyczącymi zasobów i obserwować wpływ na przykładowej aplikacji. Dodaj lub usuń dostawców tożsamości, manipulowania oświadczeń aplikacji lub zmień atrybuty tworzenia konta. Wypróbuj, aż zobaczysz, jak zasady, żądania uwierzytelniania i OWIN powiązać ze sobą.

## <a name="sample-code-walkthrough"></a>Przewodnik po przykładzie kodu
W poniższych sekcjach opisano sposób skonfigurowania przykładowego kodu aplikacji. Udziału można używać jako przewodnika podczas programowania aplikacji w przyszłości.

### <a name="add-authentication-support"></a>Dodaj obsługę uwierzytelniania

Teraz można skonfigurować aplikację do używania usługi Azure AD B2C. Aplikacja komunikuje się z usługą Azure AD B2C, wysyłając żądania uwierzytelniania OpenID Connect. Żądania dyktowanie środowisko użytkownika, który chce wykonać, określając zasady aplikacji. Przy użyciu biblioteki OWIN firmy Microsoft, do wysyłania tych żądań, wykonanie zasad, zarządzanie sesje użytkowników oraz inne funkcje.

#### <a name="install-owin"></a>Instalowanie interfejsu OWIN

Aby rozpocząć, Dodaj pakiety NuGet oprogramowanie pośredniczące OWIN do projektu przy użyciu konsoli Menedżera pakietów Visual Studio.

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

Otwórz plik `App_Start\Startup.Auth.cs` i zaimplementuj metodę `ConfigureAuth(...)`. Parametry podane `OpenIdConnectAuthenticationOptions` służyć jako współrzędnych dla aplikacji do komunikowania się z usługą Azure AD B2C. Jeśli niektóre parametry nie jest określony, użyje wartości domyślnej. Na przykład firma Microsoft nie określaj `ResponseType` w przykładzie, więc wartość domyślna `code id_token` zostaną użyte na każde wychodzące żądanie do usługi Azure AD B2C.

Należy również skonfigurować uwierzytelniania plików cookie. Oprogramowania pośredniczącego OpenID Connect używa plików cookie w celu zachowania sesji użytkownika, między innymi.

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

                // Specify the scope by appending all of the scopes requested into one string (separated by a blank space)
                Scope = $"openid profile offline_access {ReadTasksScope} {WriteTasksScope}"
            }
        );
    }

    // Implement the "Notification" methods...
}
```

W `OpenIdConnectAuthenticationOptions` powyżej, możemy zdefiniować zestaw funkcji wywołania zwrotnego dla określonego powiadomienia, które są odbierane przez oprogramowanie pośredniczące uwierzytelniania OpenID Connect. Te zachowania są definiowane przy użyciu `OpenIdConnectAuthenticationNotifications` obiektu i przechowywane w `Notifications` zmiennej. W naszym przykładzie definiujemy trzech różnych wywołań zwrotnych w zależności od zdarzenia.

### <a name="using-different-policies"></a>Przy użyciu różnych zasad

`RedirectToIdentityProvider` Przy każdym wysłaniu żądania do usługi Azure AD B2C jest wyzwalane powiadomienie. W funkcji wywołania zwrotnego `OnRedirectToIdentityProvider`, możemy sprawdzić, w wywołaniu wychodzących, jeśli chcemy użyć różnych zasad. Aby można było wykonać Resetowanie hasła lub edytowania profilu, należy użyć odpowiednich zasad, takich jak zasady, zamiast domyślnych zasad "Zarejestruj się lub zaloguj się" resetowania hasła.

W naszym przykładzie gdy użytkownik chce, aby zresetować hasło lub dokonać edycji profilu, dodamy zasad które firma Microsoft nie chce używać w kontekście OWIN. Można to zrobić, wykonując następujące czynności:

```CSharp
    // Let the middleware know you are trying to use the edit profile policy
    HttpContext.GetOwinContext().Set("Policy", EditProfilePolicyId);
```

I można zaimplementować funkcję wywołania zwrotnego `OnRedirectToIdentityProvider` , wykonując następujące czynności:

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

`AuthorizationCodeReceived` Jest wyzwalane powiadomienie, po odebraniu kodu autoryzacji. Oprogramowania pośredniczącego OpenID Connect nie obsługuje wymianę kodów dla tokenów dostępu. Można ręcznie wymienić kod tokenu w funkcji wywołania zwrotnego. Aby uzyskać więcej informacji można znaleźć w [dokumentacji](active-directory-b2c-devquickstarts-web-api-dotnet.md) objaśniający sposób.

### <a name="handling-errors"></a>Obsługa błędów

`AuthenticationFailed` Jest wyzwalane powiadomienie, gdy uwierzytelnianie nie powiedzie się. W jego metody wywołania zwrotnego może obsługiwać błędy, jak chcesz. Należy jednak dodać Wyszukaj kod błędu: `AADB2C90118`. W czasie wykonywania zasad "Rejestracji lub logowania" użytkownik musi wybrać **nie pamiętasz hasła?** łącza. W takiej sytuacji usługi Azure AD B2C wysyła aplikacji, ten kod błędu wskazujący, że aplikacji należy upewnić żądanie zamiast przy użyciu zasad resetowania hasła.

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

### <a name="send-authentication-requests-to-azure-ad"></a>Wysyłanie żądania uwierzytelnienia do usługi Azure AD

Aplikacji jest prawidłowo skonfigurowany do komunikowania się z usługą Azure AD B2C przy użyciu protokołu uwierzytelniania OpenID Connect. OWIN zarządza szczegóły obsługuje tworzenie komunikatów uwierzytelniania, sprawdzanie poprawności tokenów z usługi Azure AD B2C i podtrzymywanie sesji użytkowników. Pozostaje tylko do inicjowania przepływu każdego użytkownika.

Gdy użytkownik wybierze **Sign up/logowanie w**, **Edytuj profil**, lub **Resetuj hasło** w aplikacji sieci web skojarzone z akcją jest wywoływana w `Controllers\AccountController.cs`:

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

Korzystanie z OWIN, można również wylogowania użytkownika z aplikacji. W `Controllers\AccountController.cs` mamy:

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

Oprócz jawne wywołanie zasad, można użyć `[Authorize]` tag w kontrolerach, który wykonuje zasady, jeśli użytkownik nie jest zalogowany. Otwórz `Controllers\HomeController.cs` i Dodaj `[Authorize]` tag, aby kontroler oświadczeń.  Wybiera OWIN, ostatnie zasady skonfigurowane, kiedy `[Authorize]` tag tych limitów zostanie osiągnięty.

```CSharp
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

### <a name="display-user-information"></a>Wyświetlanie informacji o użytkowniku

Podczas uwierzytelniania użytkowników za pomocą protokołu OpenID Connect, usługi Azure AD B2C zwraca tokenu Identyfikacyjnego aplikacja, która zawiera **oświadczeń**. Są to asercje o użytkowniku. Korzystanie z oświadczeń, aby spersonalizować Twojej aplikacji.

Otwórz plik `Controllers\HomeController.cs`. Dostęp do oświadczenia użytkownika w kontrolerach za pośrednictwem `ClaimsPrincipal.Current` podmiotu zabezpieczeń.

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

Wszelkie roszczenia, które aplikacja otrzyma w taki sam sposób możesz uzyskać dostęp.  Lista wszystkich oświadczeń odbiera aplikacja jest dostępna dla Ciebie na **oświadczeń** strony.
