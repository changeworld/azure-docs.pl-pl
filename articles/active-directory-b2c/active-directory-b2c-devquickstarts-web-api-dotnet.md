---
title: Wywoływanie interfejsu web API platformy .NET z aplikacji sieci web platformy .NET w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Jak utworzyć aplikację sieci Web platformy .NET i wywołania internetowego interfejsu api przy użyciu tokenów dostępu usługi Azure Active Directory B2C i OAuth 2.0.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7296954a17b21183eb8be2744b42289522cf7f57
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2018
ms.locfileid: "51012500"
---
# <a name="call-a-net-web-api-from-a-net-web-app-in-azure-active-directory-b2c"></a>Wywoływanie interfejsu web API platformy .NET z aplikacji sieci web platformy .NET w usłudze Azure Active Directory B2C

Za pomocą usługi Azure AD B2C, można dodać zaawansowane funkcje obsługi tożsamości zarządzania do aplikacji sieci web i interfejsów API sieci web. W tym artykule omówiono sposób żądań tokenów dostępu i marka wywołania z aplikacji sieci web platformy .NET "Lista zadań do wykonania".NET interfejs api sieci web.

Ten artykuł nie obejmuje, jak zaimplementować logowania, rejestracji i zarządzania profilami za pomocą usługi Azure AD B2C. Uwzględniono w szczególności wywoływania interfejsów API sieci web po użytkownik jest już uwierzytelniony. Jeśli jeszcze nie, należy:

* Rozpoczynanie pracy z usługą [aplikacji internetowej platformy .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
* Rozpoczynanie pracy z usługą [interfejs api sieci web platformy .NET](active-directory-b2c-devquickstarts-api-dotnet.md)

## <a name="prerequisite"></a>Wymagania wstępne

Aby utworzyć aplikację sieci web, która wywołuje internetowy interfejs api, musisz:

1. [Tworzenie dzierżawy usługi Azure AD B2C](active-directory-b2c-get-started.md).
2. [Rejestrowanie internetowego interfejsu api](active-directory-b2c-app-registration.md).
3. [Rejestrowanie aplikacji internetowej](active-directory-b2c-app-registration.md).
4. [Konfigurowanie zasad](active-directory-b2c-reference-policies.md).
5. [Udzielanie uprawnień aplikacji do użycia w sieci web sieci web interfejsu api](active-directory-b2c-access-tokens.md).

> [!IMPORTANT]
> Aplikacja kliencka i interfejs API sieci Web muszą korzystać z tego samego katalogu usługi Azure AD B2C.
>

## <a name="download-the-code"></a>Pobieranie kodu

Kod używany w tym samouczku [jest przechowywany w serwisie GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Przykład można sklonować, uruchamiając polecenie:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Po pobraniu przykładu kodu otwórz plik SLN programu Visual Studio, aby rozpocząć. Plik rozwiązania zawiera dwa projekty: `TaskWebApp` i `TaskService`. `TaskWebApp` jest aplikacją sieci web MVC, który użytkownik wchodzi w interakcję z. `TaskService` to interfejs API zaplecza aplikacji, który przechowuje listy zadań do wykonania poszczególnych użytkowników. W tym artykule nie omówiono tworzenia `TaskWebApp` aplikacji sieci web lub `TaskService` interfejs api sieci web. Aby dowiedzieć się, jak utworzyć aplikację sieci web platformy .NET przy użyciu usługi Azure AD B2C, zobacz nasze [samouczek aplikacji sieci web platformy .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md). Aby dowiedzieć się, jak utworzyć interfejs API zabezpieczony za pomocą usługi Azure AD B2C sieci web platformy .NET, zobacz nasze [samouczek dotyczący interfejsu API sieci web platformy .NET](active-directory-b2c-devquickstarts-api-dotnet.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Aktualizowanie konfiguracji usługi Azure AD B2C

Nasz przykład został skonfigurowany do używania zasad i identyfikatora klienta dzierżawy pokazowej. Jeśli chcesz użyć własnej dzierżawy:

1. Otwórz plik `web.config` w projekcie `TaskService` i zastąp wartości

    * `ida:Tenant` nazwą dzierżawy
    * `ida:ClientId` za pomocą Identyfikatora aplikacji interfejsu api sieci web
    * `ida:SignUpSignInPolicyId` nazwą zasady tworzenia konta/logowania

2. Otwórz plik `web.config` w projekcie `TaskWebApp` i zastąp wartości

    * `ida:Tenant` nazwą dzierżawy
    * `ida:ClientId` identyfikatorem aplikacji internetowej
    * `ida:ClientSecret` kluczem tajnym aplikacji internetowej
    * `ida:SignUpSignInPolicyId` nazwą zasady tworzenia konta/logowania
    * `ida:EditProfilePolicyId` nazwą zasady edycji profilu
    * `ida:ResetPasswordPolicyId` nazwą zasady resetowania hasła



## <a name="requesting-and-saving-an-access-token"></a>Żądanie certyfikatu oraz zapisywanie tokenu dostępu

### <a name="specify-the-permissions"></a>Określ uprawnienia

W celu wywoływania interfejsu API sieci web, należy uwierzytelnić użytkownika (przy użyciu Twojego konta-dokonywania/zasad logowania) i [odbierania token dostępu](active-directory-b2c-access-tokens.md) z usługi Azure AD B2C. Aby otrzymać token dostępu, najpierw należy określić uprawnienia chcesz tokenu dostępu, aby udzielić. Uprawnienia są określone w `scope` parametru po wprowadzeniu żądanie `/authorize` punktu końcowego. Na przykład w celu uzyskania tokenu dostępu z uprawnieniami "Odczyt" aplikacji zasobów, która ma identyfikator URI Identyfikatora aplikacji z `https://contoso.onmicrosoft.com/tasks`, byłoby zakres `https://contoso.onmicrosoft.com/tasks/read`.

Aby określić zakres w naszym przykładzie, otwórz plik `App_Start\Startup.Auth.cs` i zdefiniuj `Scope` OpenIdConnectAuthenticationOptions zmienną.

```CSharp
// App_Start\Startup.Auth.cs

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ...

            // Specify the scope by appending all of the scopes requested into one string (separated by a blank space)
            Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
        }
    );
}
```

### <a name="exchange-the-authorization-code-for-an-access-token"></a>Kod autoryzacji dla tokenu dostępu programu Exchange

Po użytkownik kończy proces rejestracji lub logowania, aplikacja otrzyma kod autoryzacji z usługi Azure AD B2C. Oprogramowanie pośredniczące OWIN OpenID Connect będą przechowywane w kodzie, ale nie będzie wymiany dla tokenu dostępu. Możesz użyć [biblioteki MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) zapewnienie programu exchange. W naszym przykładzie skonfigurowaliśmy wywołania zwrotnego do oprogramowania pośredniczącego OpenID Connect po każdym odebraniu kodu autoryzacji. Wywołanie zwrotne firma Microsoft użycia biblioteki MSAL do programu exchange kodu dla tokenu, a następnie zapisz token w pamięci podręcznej.

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

## <a name="calling-the-web-api"></a>Wywoływanie interfejsu API sieci web

W tej sekcji omówiono sposób użycia tokenu odebranych w trakcie konta-dokonywania/Zaloguj się przy użyciu usługi Azure AD B2C w celu uzyskania dostępu do interfejsu API sieci web.

### <a name="retrieve-the-saved-token-in-the-controllers"></a>Pobieranie tokenu zapisanych w kontrolerów

`TasksController` Jest odpowiedzialny za komunikację za pomocą interfejsu API sieci web i wysyłania żądań HTTP do interfejsu API do odczytu, tworzenia i usuwania zadań. Ponieważ interfejs API jest zabezpieczony przez usługę Azure AD B2C, musisz najpierw pobrać token, który został zapisany w kroku powyżej.

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

### <a name="read-tasks-from-the-web-api"></a>Odczyt zadań z interfejsu API sieci web

Jeśli masz już token może dołączyć ją do HTTP `GET` zażądać `Authorization` nagłówek, aby bezpiecznie wywoływać `TaskService`:

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

### <a name="create-and-delete-tasks-on-the-web-api"></a>Tworzenie i usuwanie zadań na interfejs API sieci web

Postępuj zgodnie z tego samego wzorca, gdy wysyłasz `POST` i `DELETE` żądań do internetowego interfejsu API, z zastosowaniem biblioteki MSAL do pobrania tokenu dostępu z pamięci podręcznej.

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

Na koniec Skompiluj i uruchom obie aplikacje. Zarejestruj się i zaloguj się i Utwórz zadania dla zalogowanego użytkownika. Wyloguj się i zaloguj się jako inny użytkownik. Utwórz zadania dla tego użytkownika. Zwróć uwagę, jak zadania są przechowywane dla poszczególnych użytkowników w interfejsie API, ponieważ wyodrębnia tożsamość użytkownika z tokenu odbiera. Spróbuj również gry z zakresów. Usuń uprawnienia do "write", a następnie spróbuj dodać zadania. Po prostu upewnij się wylogować się po każdej zmianie zakresu.

