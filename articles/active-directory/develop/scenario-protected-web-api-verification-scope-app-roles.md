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
ms.openlocfilehash: b700825be9a7fe23fe4b50a2d69d4de71f7dc038
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116456"
---
# <a name="protected-web-api---adding-authorization-to-your-api"></a>Chroniony internetowy interfejs API — Dodawanie autoryzacji do interfejsu API

W tym artykule opisano, jak dodać autoryzacji do internetowego interfejsu API. Ta ochrona gwarantuje, że jest on wywoływany tylko:

- aplikacje w imieniu użytkowników mających odpowiednie zakresy 
- lub przez aplikacje demona z rolami prawo aplikacji.

W technologii ASP.NET / platformy ASP.NET Core interfejsu API sieci Web ma być chroniony, należy dodać `[Authorize]` atrybutu na:

- Jeśli chcesz, aby wszystkie akcje kontrolera, do którego ma być chroniony kontroler
- lub akcji poszczególnych kontrolera interfejsu API.

```CSharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Jednak ta ochrona nie jest wystarczająca. Gwarantuje transmitować tylko tego ASP.NET / platformy ASP.NET Core zostanie przeprowadzona Weryfikacja tokenu. Interfejs API musi sprawdzić, czy token używany do wywoływania interfejsu API sieci Web została zażądana przy użyciu oświadczeń, który oczekuje w szczególności:

- **zakresy** Jeśli interfejs API jest wywoływana w imieniu użytkownika
- **role aplikacji** Jeśli interfejs API może zostać wywołana z aplikacją demon.

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>Weryfikowanie zakresów w interfejsach API o nazwie w imieniu użytkowników

Jeśli Twój interfejs API jest wywoływany przez aplikację kliencką w imieniu użytkownika, a następnie należy uzyskać token elementu nośnego z zakresów określonych w przypadku interfejsu API (zobacz [Konfiguracja kodu | Token elementu nośnego](scenario-protected-web-api-app-configuration.md#bearer-token))

```CSharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The Web API will only accept tokens 1) for users, 2) having the `access_as_user` scope for
    /// this API
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result
        ...
    }
...
}
```

`VerifyUserHasAnyAcceptedScope` Metody jak podobną do następującej:

- Sprawdź, czy jest oświadczeń o nazwie `http://schemas.microsoft.com/identity/claims/scope` lub `scp`
- Sprawdź, czy oświadczenie ma wartość zawierającą zakres oczekiwane przez interfejs API.

```CSharp
    /// <summary>
    /// When applied to an <see cref="HttpContext"/>, verifies that the user authenticated in the 
    /// Web API has any of the accepted scopes.
    /// If the authenticated user does not have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized with the message telling which scopes are expected in the token
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

Ten przykładowy kod jest dla platformy ASP.NET Core. Dla programu ASP.NET, po prostu Zamień `HttpContext.User` przez `ClaimsPrincipal.Current`oraz typ oświadczenia `"http://schemas.microsoft.com/identity/claims/scope"` przez `"scp"` (patrz poniższy fragment kodu)

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>Weryfikowanie role aplikacji w interfejsach API wywoływany przez aplikacje demona

Jeśli internetowy interfejs API jest wywoływany przez [aplikacji demona](scenario-daemon-overview.md), a następnie ta aplikacja powinna wymagać aplikacji uprawnień do internetowego interfejsu API. Widzieliśmy w [scenario-protected-web-api-app-registration.md#how-to-expose-application-permissions--app-roles-], interfejs API udostępnia takie uprawnienia (na przykład jako `access_as_application` roli aplikacji).
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
    // In this case we look for a `role` value of `access_as_application`
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

Ten przykładowy kod jest dla platformy ASP.NET. Dla platformy ASP.NET Core, wystarczy zastąpić `ClaimsPrincipal.Current` przez `HttpContext.User` i `"roles"` oświadczenia nazwy `"http://schemas.microsoft.com/identity/claims/roles"` (patrz także: powyższym fragmencie kodu)

### <a name="accepting-app-only-tokens-if-the-web-api-should-only-be-called-by-daemon-apps"></a>Akceptuje tylko tokenów aplikacji, jeśli interfejs API sieci Web powinna być wywoływana tylko przez aplikacje demona

`roles` Oświadczeń jest również używany dla użytkowników we wzorcach przypisania użytkownika (zobacz [jak: Dodawanie ról aplikacji w aplikacji i odbierać je w tokenie](howto-add-app-roles-in-azure-ad-apps.md)). Sprawdzanie, więc tylko role będzie zezwalać aplikacjom Zaloguj się jako użytkownicy i odwrotnie, jeśli role można przypisać do obu. Firma Microsoft zaleca mające różne role, zadeklarowany dla użytkowników i aplikacji uniknąć nieporozumień tego.

Jeśli chcesz zezwalać tylko na aplikacje demona do wywołania interfejsu API sieci Web, należy dodać warunek, podczas sprawdzania poprawności roli aplikacji, czy token jest tokenem tylko do aplikacji:

```CSharp
string oid = ClaimsPrincipal.Current.FindFirst("oid");
string sub = ClaimsPrincipal.Current.FindFirst("sub");
bool isAppOnlyToken = oid == sub;
```

Sprawdzanie warunku odwrotność pozwoli tylko te aplikacje, które logują użytkowników do wywołania interfejsu API.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przenoszenie do środowiska produkcyjnego](scenario-protected-web-api-production.md)
