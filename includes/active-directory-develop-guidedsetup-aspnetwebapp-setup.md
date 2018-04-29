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
ms.openlocfilehash: abb118610afa55834a3a6792c0a5503a1abfd09e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
## <a name="set-up-your-project"></a>Konfigurowanie projektu

W tej sekcji przedstawiono kroki, aby zainstalować i skonfigurować potoku uwierzytelniania za pomocą oprogramowania pośredniczącego OWIN w projekcie ASP.NET przy użyciu protokołu OpenID Connect. 

> Preferowane jest zamiast tego Pobierz ten przykładowy projekt programu Visual Studio? [Pobieranie projektu](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) i przejść [kroku konfiguracji](#register-your-application) skonfigurować przykładowy kod przed wykonaniem.

### <a name="create-your-aspnet-project"></a>Tworzenie projektu platformy ASP.NET

1. W programie Visual Studio: `File` > `New` > `Project`<br/>
2. W obszarze *Visual C# \Web*, wybierz pozycję `ASP.NET Web Application (.NET Framework)`.
3. Nazwa aplikacji, a następnie kliknij przycisk *OK*
4. Wybierz `Empty` i zaznacz pole wyboru, aby dodać `MVC` odwołań

## <a name="add-authentication-components"></a>Dodaj składniki uwierzytelniania

1. W programie Visual Studio: `Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Dodaj *pakietów NuGet oprogramowanie pośredniczące OWIN* wpisując następujące polecenie w oknie konsoli Menedżera pakietów:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```
    
<!--start-collapse-->
> ### <a name="about-these-libraries"></a>Te biblioteki — informacje
>Powyżej biblioteki Włącz logowanie jednokrotne (SSO) przy użyciu protokołu OpenID Connect przy użyciu uwierzytelniania opartego na pliku cookie. Po zakończeniu uwierzytelniania i token reprezentujący użytkownika jest wysyłany do aplikacji, oprogramowanie pośredniczące OWIN tworzy plik cookie sesji. Dlatego użytkownik nie musi ponownie wpisać hasło i nie jest wymagane nie dodatkowe weryfikacji przeglądarki następnie używa tego pliku cookie dla kolejnych żądań.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Konfigurowanie procesu uwierzytelniania
Poniższe kroki są używane do tworzenia oprogramowania pośredniczącego OWIN klasy początkowej można skonfigurować uwierzytelniania OpenID Connect. Ta klasa zostanie uruchomiony automatycznie po rozpoczęciu procesu programu IIS.

> [!TIP]
> Jeśli projekt nie ma `Startup.cs` pliku w folderze głównym:<br/>
> 1. Kliknij prawym przyciskiem myszy w folderze głównym projektu: >    `Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Nadaj jej nazwę `Startup.cs`<br/>
>
>> Upewnij się, że jest wybrana klasa, klasę początkową OWIN i nie C# klasa standardowa. To potwierdzić, sprawdzając występowanie `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` powyżej przestrzeni nazw.

1. Dodaj *OWIN* i *Microsoft.IdentityModel* odwołuje się do `Startup.cs`:

    ```csharp
    using Microsoft.Owin;
    using Owin;
    using Microsoft.IdentityModel.Protocols;
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    using Microsoft.Owin.Security.Notifications;
    ```

2. Zastąp klasy początkowej przy użyciu poniższego kodu:

    ```csharp
    public class Startup
    {        
        // The Client ID is used by the application to uniquely identify itself to Azure AD.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];
    
        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];
    
        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];
    
        // Authority is the URL for authority, composed by Azure Active Directory v2 endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
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
                    Scope = OpenIdConnectScopes.OpenIdProfile,
                    // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                    ResponseType = OpenIdConnectResponseTypes.IdToken,
                    // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                    // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                    // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter 
                    TokenValidationParameters = new System.IdentityModel.Tokens.TokenValidationParameters() { ValidateIssuer = false },
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


<!--start-collapse-->
> ### <a name="more-information"></a>Więcej informacji
> Parametry podane *OpenIDConnectAuthenticationOptions* służyć jako współrzędnych dla aplikacji do komunikowania się z usługą Azure AD. Ponieważ oprogramowania pośredniczącego protokołu OpenID Connect używa plików cookie w tle, należy skonfigurować uwierzytelniania plików cookie jako kod powyżej przedstawiono. *ValidateIssuer* wartość informuje OpenIdConnect nie ograniczyć dostęp do jednej z określonych organizacji.
<!--end-collapse-->

