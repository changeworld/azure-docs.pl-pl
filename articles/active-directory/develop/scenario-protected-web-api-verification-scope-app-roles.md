---
title: Chroniony internetowy interfejs API — konfiguracji kodu aplikacji | Usługa Azure Active Directory
description: Dowiedz się, jak utworzyć chroniony internetowy interfejs API i konfigurowanie kodu aplikacji.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23ff03316a1f9409d4d6e4b7ddf52d0c8cc7a909
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551537"
---
# <a name="protected-web-api-adding-authorization-to-your-api"></a>Chroniony internetowy interfejs API: Dodawanie autoryzacji do interfejsu API

W tym artykule opisano, jak dodać autoryzacji do interfejsu API sieci web. Ta ochrona gwarantuje, że interfejs API jest wywoływana tylko przez:

- Aplikacje w imieniu użytkowników, którzy mają odpowiednie zakresy.
- Aplikacje demona mające role odpowiednie aplikacji.

Aby chronić internetowego interfejsu API ASP.NET/ASP.NET Core, musisz dodać `[Authorize]` atrybutu na jeden z nich:

- Kontroler, jeśli chcesz, aby wszystkie akcje kontrolera, do którego ma być chroniony
- Akcja poszczególnych kontrolera interfejsu API

```CSharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Jednak ta ochrona nie jest wystarczająca. Gwarantuje on tylko, że ASP.NET/ASP.NET Core zostanie przeprowadzona Weryfikacja tokenu. Interfejs API musi sprawdzić, czy token używany do wywoływania usługi sieci web interfejsu API została zażądana przy użyciu oświadczeń oczekuje, że, w szczególności:

- *Zakresy*, jeśli interfejs API jest wywoływana w imieniu użytkownika.
- *Role aplikacji*, jeśli interfejs API może zostać wywołana z aplikacją demon.

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>Weryfikowanie zakresów w interfejsach API o nazwie w imieniu użytkowników

Jeśli Twój interfejs API jest wywoływany przez aplikację kliencką w imieniu użytkownika, musi ona żądania tokenu elementu nośnego, który ma określone zakresy dla interfejsu API. (Zobacz [Konfiguracja kodu | Token elementu nośnego](scenario-protected-web-api-app-configuration.md#bearer-token).)

```CSharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result.
        ...
    }
...
}
```

`VerifyUserHasAnyAcceptedScope` Metody jak podobną do następującej:

- Sprawdź, czy oświadczenie o nazwie `http://schemas.microsoft.com/identity/claims/scope` lub `scp`.
- Sprawdź, czy oświadczenie ma wartość, która zawiera zakres, oczekiwane przez interfejs API.

```CSharp
    /// <summary>
    /// When applied to a <see cref="HttpContext"/>, verifies that the user authenticated in the 
    /// web API has any of the accepted scopes.
    /// If the authenticated user doesn't have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized error with a message noting which scopes are expected in the token.
    /// </summary>
    /// <param name="acceptedScopes">Scopes accepted by this API</param>
    /// <exception cref="HttpRequestException"/> with a <see cref="HttpResponse.StatusCode"/> set to 
    /// <see cref="HttpStatusCode.Unauthorized"/>
    public static void VerifyUserHasAnyAcceptedScope(this HttpContext context,
                                                     params string[] acceptedScopes)
    {
        if (acceptedScopes == null)
        {
            throw new ArgumentNullException(nameof(acceptedScopes));
        }
        Claim scopeClaim = HttpContext?.User
                                      ?.FindFirst("http://schemas.microsoft.com/identity/claims/scope");
        if (scopeClaim == null || !scopeClaim.Value.Split(' ').Intersect(acceptedScopes).Any())
        {
            context.Response.StatusCode = (int)HttpStatusCode.Unauthorized;
            string message = $"The 'scope' claim does not contain scopes '{string.Join(",", acceptedScopes)}' or was not found";
            throw new HttpRequestException(message);
        }
    }
```

Ten przykładowy kod jest dla platformy ASP.NET Core. W technologii ASP.NET, wystarczy zastąpić `HttpContext.User` z `ClaimsPrincipal.Current`i Zastąp typ oświadczenia `"http://schemas.microsoft.com/identity/claims/scope"` z `"scp"`. (Zobacz też fragment kodu w dalszej części tego artykułu).

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>Weryfikowanie role aplikacji w interfejsach API wywoływany przez aplikacje demona

Jeśli internetowy interfejs API jest wywoływany przez [aplikacji demona](scenario-daemon-overview.md), ta aplikacja powinna wymagać aplikacji uprawnień do interfejsu API sieci web. Widzieliśmy w [ujawnienia uprawnień aplikacji (aplikacji role)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles) , interfejs API udostępnia takie uprawnienia (na przykład `access_as_application` roli aplikacji).
Teraz musisz sprawdzić, czy token, który otrzymał zawiera interfejsy API `roles` oświadczeń i że to oświadczenie z wartością oczekuje. Kod, wykonując Ta weryfikacja jest podobny do kodu, który sprawdza uprawnienia delegowane, z wyjątkiem tego, zamiast testowanie pod kątem `scopes`, przetestujesz akcji kontrolera dla `roles`:

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

`ValidateAppRole()` Metoda może być podobny do poniższego:

```CSharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage 
        { StatusCode = HttpStatusCode.Unauthorized, 
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found" 
        });
    }
}
}
```

Ten przykładowy kod jest dla platformy ASP.NET. Dla platformy ASP.NET Core, wystarczy zastąpić `ClaimsPrincipal.Current` z `HttpContext.User`i Zastąp `"roles"` oświadczenia nazwy `"http://schemas.microsoft.com/identity/claims/roles"`. (Zobacz też fragment kodu we wcześniejszej części tego artykułu).

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Akceptować tokeny tylko do aplikacji, jeśli interfejs API sieci web powinna być wywoływana tylko przez aplikacje demona

`roles` Oświadczeń jest również używany dla użytkowników we wzorcach przypisanie użytkownika. (Zobacz [jak: Dodawanie ról aplikacji w aplikacji i odbierać je w tokenie](howto-add-app-roles-in-azure-ad-apps.md).) Sprawdzanie, więc tylko role będzie zezwalać aplikacjom Zaloguj się jako użytkownicy i odwrotnie, jeśli role można przypisać do obu. Firma Microsoft zaleca zadeklarujesz przypisane różne role dla użytkowników i aplikacji uniknąć nieporozumień tego.

Jeśli chcesz zezwolić tylko na aplikacje demona do wywołania interfejsu API sieci web, Dodaj warunek, podczas sprawdzania poprawności roli aplikacji, czy token jest tokenem tylko do aplikacji:

```CSharp
string oid = ClaimsPrincipal.Current.FindFirst("oid");
string sub = ClaimsPrincipal.Current.FindFirst("sub");
bool isAppOnlyToken = oid == sub;
```

Sprawdzanie warunku odwrotność pozwoli tylko te aplikacje, które logują się użytkownika do wywołania interfejsu API.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przenoszenie do środowiska produkcyjnego](scenario-protected-web-api-production.md)
