---
title: Wywołaj zabezpieczonych ASP.NET web api w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Jak utworzyć aplikację sieci Web .NET i wywoływanie sieci web interfejsu api przy użyciu tokenów dostępu do usługi Azure Active Directory B2C i OAuth 2.0.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 03/17/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 39603cf103a8ff2656c76843aeae36b17936d13a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712406"
---
# <a name="azure-ad-b2c-call-a-net-web-api-from-a-net-web-app"></a>Usługa Azure AD B2C: Wywołanie interfejsu API sieci web .NET z aplikacji sieci web .NET

Za pomocą usługi Azure AD B2C, można dodawać tożsamości zaawansowanych funkcji zarządzania do aplikacji sieci web i interfejsów API sieci web. W tym artykule omówiono sposób żądań tokenów dostępu i Utwórz wywołania z aplikacji sieci web .NET "Lista zadań do wykonania".NET interfejs api sieci web.

W tym artykule nie opisano sposobu wdrażania logowania, rejestracji i zarządzania profilami w usłudze Azure AD B2C. Uwzględniono w szczególności wywoływania interfejsów API sieci web po użytkownik jest już uwierzytelniony. Jeśli nie jest jeszcze, wykonaj następujące czynności:

* Rozpoczynanie pracy z [aplikacji sieci web .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
* Rozpoczynanie pracy z [interfejs api sieci web .NET](active-directory-b2c-devquickstarts-api-dotnet.md)

## <a name="prerequisite"></a>Wymagania wstępne

Aby utworzyć aplikację sieci web, która wywołuje sieci web interfejsu api, musisz:

1. [Tworzenie dzierżawy usługi Azure AD B2C](active-directory-b2c-get-started.md).
2. [Zarejestruj sieci web interfejsu api](active-directory-b2c-app-registration.md#register-a-web-api).
3. [Rejestrowanie aplikacji sieci web](active-directory-b2c-app-registration.md#register-a-web-app).
4. [Ustawianie zasad](active-directory-b2c-reference-policies.md).
5. [Przyznać uprawnienia aplikacji sieci web w sieci web interfejsu api](active-directory-b2c-access-tokens.md#publishing-permissions).

> [!IMPORTANT]
> Aplikacja kliencka i interfejs API sieci Web muszą korzystać z tego samego katalogu usługi Azure AD B2C.
>

## <a name="download-the-code"></a>Pobieranie kodu

Kod używany w tym samouczku [jest przechowywany w serwisie GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Przykład można sklonować, uruchamiając polecenie:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Po pobraniu przykładu kodu otwórz plik SLN programu Visual Studio, aby rozpocząć. Plik rozwiązania zawiera dwa projekty: `TaskWebApp` i `TaskService`. `TaskWebApp` to aplikacja sieci web MVC, którą użytkownik wchodzi w interakcję z. `TaskService` to interfejs API zaplecza aplikacji, który przechowuje listy zadań do wykonania poszczególnych użytkowników. W tym artykule opisano tworzenie `TaskWebApp` aplikacji sieci web lub `TaskService` interfejs api sieci web. Aby dowiedzieć się, jak utworzyć aplikację sieci web platformy .NET przy użyciu usługi Azure AD B2C, zobacz nasze [samouczek aplikacji sieci web .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md). Aby dowiedzieć się, jak tworzyć chronione przy użyciu usługi Azure AD B2C interfejsu API sieci web .NET, zobacz nasze [interfejsu API sieci web platformy .NET — samouczek](active-directory-b2c-devquickstarts-api-dotnet.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Aktualizowanie konfiguracji usługi Azure AD B2C

Nasz przykład został skonfigurowany do używania zasad i identyfikatora klienta dzierżawy pokazowej. Jeśli chcesz korzystać z własnych dzierżawy:

1. Otwórz plik `web.config` w projekcie `TaskService` i zastąp wartości

    * `ida:Tenant` nazwą dzierżawy
    * `ida:ClientId` za pomocą Identyfikatora aplikacji interfejsu api sieci web
    * `ida:SignUpSignInPolicyId` nazwą zasady tworzenia konta/logowania

2. Otwórz plik `web.config` w projekcie `TaskWebApp` i zastąp wartości

    * `ida:Tenant` nazwą dzierżawy
    * `ida:ClientId` identyfikatorem aplikacji sieci Web
    * `ida:ClientSecret` kluczem tajnym aplikacji sieci Web
    * `ida:SignUpSignInPolicyId` nazwą zasady tworzenia konta/logowania
    * `ida:EditProfilePolicyId` nazwą zasady edycji profilu
    * `ida:ResetPasswordPolicyId` nazwą zasady resetowania hasła



## <a name="requesting-and-saving-an-access-token"></a>Żądania i zapisywanie token dostępu

### <a name="specify-the-permissions"></a>Określ uprawnienia

Aby wprowadzić wywołanie interfejsu API sieci web, należy uwierzytelnić użytkownika (przy użyciu Twojego konta-konta/zasad logowania) i [odbierania token dostępu](active-directory-b2c-access-tokens.md) z usługi Azure AD B2C. Aby otrzymywać tokenu dostępu, należy najpierw określić chcesz tokenu dostępu, aby udzielić uprawnień. Uprawnienia są określone w `scope` parametru po wprowadzeniu żądania `/authorize` punktu końcowego. Na przykład, aby uzyskać token dostępu z uprawnieniami "do odczytu", do aplikacji zasobów, która zawiera identyfikator URI aplikacji z `https://contoso.onmicrosoft.com/tasks`, zakres będzie `https://contoso.onmicrosoft.com/tasks/read`.

Aby określić zakres w naszym przykładzie, otwórz plik `App_Start\Startup.Auth.cs` i zdefiniuj `Scope` zmiennej w OpenIdConnectAuthenticationOptions.

```CSharp
// App_Start\Startup.Auth.cs

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ...

            // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
            Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
        }
    );
}
```

### <a name="exchange-the-authorization-code-for-an-access-token"></a>Kod autoryzacji dla tokenu dostępu programu Exchange

Po użytkownik kończy proces rejestracji i logowania, aplikacja zostanie wyświetlony kod autoryzacji z usługi Azure AD B2C. Oprogramowanie pośredniczące OWIN OpenID Connect zapisze kod, ale nie będzie exchange dla tokenu dostępu. Można użyć [biblioteki MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) dokonanie programu exchange. W naszym przykładzie został skonfigurowany wywołanie zwrotne powiadomienia w oprogramowaniu pośredniczącym protokołu OpenID Connect przy każdym odebraniu kod autoryzacji. Podczas wywołania zwrotnego używamy MSAL wymiany kod tokenu i zapisać token do pamięci podręcznej.

```CSharp
/*
* Callback function when an authorization code is received
*/
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
    // Extract the code from the response notification
    var code = notification.Code;

    var userObjectId = notification.AuthenticationTicket.Identity.FindFirst(ObjectIdElement).Value;
    var authority = String.Format(AadInstance, Tenant, DefaultPolicy);
    var httpContext = notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase;

    // Exchange the code for a token. Make sure to specify the necessary scopes
    ClientCredential cred = new ClientCredential(ClientSecret);
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                            RedirectUri, cred, new NaiveSessionCache(userObjectId, httpContext));
    var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { ReadTasksScope, WriteTasksScope }, code, DefaultPolicy);
}
```

## <a name="calling-the-web-api"></a>Wywołanie interfejsu API sieci web

W tej sekcji omówiono sposób użycia tokenu otrzymał podczas tworzenia-konta/logowania w usłudze Azure AD B2C, aby uzyskać dostęp do interfejsu API sieci web.

### <a name="retrieve-the-saved-token-in-the-controllers"></a>Pobierz token zapisany w kontrolerów

`TasksController` Jest odpowiedzialny za komunikację z interfejsu API sieci web i do wysyłania żądań HTTP do interfejsu API mogą odczytywać, tworzyć i usuwać zadania. Ponieważ interfejsu API jest zabezpieczony przez usługę Azure AD B2C, należy najpierw pobrać token, który został zapisany w kroku powyżej.

```CSharp
// Controllers\TasksController.cs

/*
* Uses MSAL to retrieve the token from the cache
*/
private async void acquireToken(String[] scope)
{
    string userObjectID = ClaimsPrincipal.Current.FindFirst(Startup.ObjectIdElement).Value;
    string authority = String.Format(Startup.AadInstance, Startup.Tenant, Startup.DefaultPolicy);

    ClientCredential credential = new ClientCredential(Startup.ClientSecret);

    // Retrieve the token using the provided scopes
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                        Startup.RedirectUri, credential,
                                        new NaiveSessionCache(userObjectID, this.HttpContext));
    AuthenticationResult result = await app.AcquireTokenSilentAsync(scope);

    accessToken = result.Token;
}
```

### <a name="read-tasks-from-the-web-api"></a>Odczytywanie zadań z interfejsu API sieci web

Jeśli masz token może dołączyć ją do HTTP `GET` zażądać `Authorization` nagłówek, aby bezpiecznie wywoływać `TaskService`:

```CSharp
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try {

        // Retrieve the token with the specified scopes
        acquireToken(new string[] { Startup.ReadTasksScope });

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, apiEndpoint);

        // Add token to the Authorization header and make the request
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
        HttpResponseMessage response = await client.SendAsync(request);

        // Handle response ...
}

```

### <a name="create-and-delete-tasks-on-the-web-api"></a>Tworzenie i usuwanie zadań w interfejsie API sieci web

Wykonują te same czynności, po wysłaniu `POST` i `DELETE` żądania sieci Web interfejsu API, przy użyciu MSAL można pobrać token dostępu z pamięci podręcznej.

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

Na koniec Skompiluj i uruchom obie aplikacje. Zarejestruj się i zaloguj się i Utwórz zadania dla zalogowanego użytkownika. Wyloguj się i zaloguj się jako inny użytkownik. Utwórz zadania dla tego użytkownika. Zwróć uwagę, jak zadania są przechowywane dla poszczególnych użytkowników w interfejsie API, ponieważ wyodrębnia on tożsamość użytkownika z tokenu, który odbiera. Spróbuj również gry z zakresów. Usuń uprawnienie do "write", a następnie spróbuj dodać zadania. Po prostu upewnij się, że Wyloguj każdej zmianie zakresu.

