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
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: dcfc341b89a3cfebcb5538f88481fd2fbb2936a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60298193"
---
## <a name="set-up-your-project"></a>konfigurowanie projektu

W tej sekcji przedstawiono kroki, aby zainstalować i skonfigurować potoku uwierzytelniania za pomocą oprogramowania pośredniczącego OWIN w projekcie ASP.NET przy użyciu protokołu OpenID Connect.

> Chcesz zamiast tego Pobierz ten przykładowy projekt programu Visual Studio? [Pobieranie projektu](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) , a następnie przejdź do [kroku konfiguracji](#register-your-application) do skonfigurowania przykładowego kodu przed wykonaniem.

### <a name="create-your-aspnet-project"></a>tworzenie projektu ASP.NET

1. W programie Visual Studio: `File` > `New` > `Project`
2. W obszarze *Visual C# \Web*, wybierz opcję `ASP.NET Web Application (.NET Framework)`.
3. Nadaj nazwę aplikacji, a następnie kliknij przycisk *OK*
4. Wybierz `Empty` i zaznacz pole wyboru, aby dodać `MVC` odwołania

## <a name="add-authentication-components"></a>dodawanie składników uwierzytelniania

1. W programie Visual Studio: `Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Dodaj *pakiety NuGet oprogramowania pośredniczącego OWIN*, wpisując następujące polecenie w oknie Konsola menedżera pakietów:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>Te biblioteki — informacje
> Powyższe biblioteki włączają logowanie jednokrotne (SSO) przy użyciu protokołu OpenID Connect za pośrednictwem uwierzytelniania w oparciu o pliki cookie. Po zakończeniu uwierzytelniania i wysłaniu tokenu reprezentującego użytkownika do aplikacji oprogramowanie pośredniczące OWIN tworzy plik cookie sesji. Przeglądarki następnie używa ten plik cookie dla kolejnych żądań, dzięki czemu użytkownik nie musiał ponownie wpisz hasło, a nie dodatkowa weryfikacja jest wymagana.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>konfigurowanie potoku uwierzytelniania

Poniższe kroki są używane do tworzenia oprogramowania pośredniczącego OWIN Klasa początkowa, aby skonfigurować uwierzytelnianie OpenID Connect. Ta klasa zostanie wykonana automatycznie, gdy rozpoczyna się proces usług IIS.

> [!TIP]
> Jeśli projekt nie ma pliku `Startup.cs` w folderze głównym:
> 1. Kliknij prawym przyciskiem myszy w folderze głównym projektu: > `Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Nadaj jej nazwę `Startup.cs`
>
>> Upewnij się, że wybrana klasa to klasa początkowa OWIN, a nie standardowa klasa C#. Potwierdź to, sprawdzając obecność ciągu `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` powyżej przestrzeni nazw.

1. Dodaj *OWIN* i *Microsoft.IdentityModel* odwołuje się do `Startup.cs`:

    ```csharp
    using Microsoft.Owin;
    using Owin;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.IdentityModel.Tokens;
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    using Microsoft.Owin.Security.Notifications;
    ```

2. Klasa początkowa Zastąp poniższy kod:

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Azure AD.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Azure Active Directory v2.0 endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
        string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, System.Configuration.ConfigurationManager.AppSettings["Authority"], tenant);

        /// <summary>
        /// Configure OWIN to use OpenIdConnect 
        /// </summary>
        /// <param name="app"></param>
        public void Configuration(IAppBuilder app)
        {
            app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

            app.UseCookieAuthentication(new CookieAuthenticationOptions());
            app.UseOpenIdConnectAuthentication(
                new OpenIdConnectAuthenticationOptions
                {
                    // Sets the ClientId, authority, RedirectUri as obtained from web.config
                    ClientId = clientId,
                    Authority = authority,
                    RedirectUri = redirectUri,
                    // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
                    PostLogoutRedirectUri = redirectUri,
                    Scope = OpenIdConnectScope.OpenIdProfile,
                    // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                    ResponseType = OpenIdConnectResponseType.IdToken,
                    // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                    // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                    // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter
                    TokenValidationParameters = new TokenValidationParameters()
                    {
                        ValidateIssuer = false // This is a simplification
                    },
                    // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed
                    }
                }
            );
        }

        /// <summary>
        /// Handle failed authentication requests by redirecting the user to the home page with an error in the query string
        /// </summary>
        /// <param name="context"></param>
        /// <returns></returns>
        private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> context)
        {
            context.HandleResponse();
            context.Response.Redirect("/?errormessage=" + context.Exception.Message);
            return Task.FromResult(0);
        }
    }
    ```

> [!NOTE]
> Ustawienie `ValidateIssuer = false` jest uproszczenia w tym przewodniku Szybki Start. W rzeczywistym aplikacji należy dokonać weryfikacji wystawcy, zobacz przykłady, aby dowiedzieć się, jak to zrobić.

<!--start-collapse-->
> ### <a name="more-information"></a>Więcej informacji
> Parametry podane w pozycji *OpenIDConnectAuthenticationOptions* służą jako współrzędne dla aplikacji do komunikowania się z usługą Azure AD. Ponieważ oprogramowania pośredniczącego OpenID Connect używa plików cookie w tle, należy skonfigurować uwierzytelniania plików cookie, jak kod powyżej przedstawiono. Wartość *ValidateIssuer* informuje oprogramowanie OpenIdConnect, aby nie ograniczać dostępu do jednej konkretnej organizacji.
<!--end-collapse-->
