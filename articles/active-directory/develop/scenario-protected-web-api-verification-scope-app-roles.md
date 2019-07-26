---
title: Chroniony internetowy interfejs API — konfiguracja kodu aplikacji | Azure Active Directory
description: Dowiedz się, jak utworzyć chroniony internetowy interfejs API i skonfigurować kod aplikacji.
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
ms.openlocfilehash: b249b99faa62e73b9aa3247f71f88767fca96f01
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488850"
---
# <a name="protected-web-api-adding-authorization-to-your-api"></a>Chroniony internetowy interfejs API: Dodawanie autoryzacji do interfejsu API

W tym artykule opisano sposób dodawania autoryzacji do internetowego interfejsu API. Ta ochrona zapewnia, że interfejs API jest wywoływany tylko przez:

- Aplikacje w imieniu użytkowników, którzy mają odpowiednie zakresy.
- Aplikacje demonów, które mają odpowiednie role aplikacji.

> [!NOTE]
> Fragmenty kodu z tego artykułu są wyodrębniane z poniższych przykładów, które są w pełni funkcjonalne
>
> - [Samouczek przyrostowy interfejsu Web API ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) w witrynie GitHub
> - [Przykład interfejsu API sieci Web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

Aby chronić ASP.NET/ASP.NET podstawowy interfejs API sieci Web, należy dodać `[Authorize]` atrybut na jednym z następujących elementów:

- Kontroler, jeśli chcesz, aby wszystkie akcje kontrolera były chronione
- Akcja pojedynczego kontrolera dla interfejsu API

```CSharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Jednak ta ochrona jest za mała. Gwarantuje tylko, że rdzeń ASP.NET/ASP.NET sprawdzi token. Interfejs API musi sprawdzić, czy token używany do wywoływania internetowego interfejsu API został zażądany przy użyciu oczekiwanych oświadczeń, w szczególności:

- *Zakresy*, jeśli interfejs API jest wywoływany w imieniu użytkownika.
- *Role aplikacji*, jeśli interfejs API można wywołać z poziomu aplikacji demona.

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>Weryfikowanie zakresów w interfejsach API wywoływanych w imieniu użytkowników

Jeśli interfejs API jest wywoływany przez aplikację kliencką w imieniu użytkownika, musi zażądać tokenu okaziciela z określonymi zakresami dla interfejsu API. (Zobacz [Konfiguracja kodu | Token okaziciela](scenario-protected-web-api-app-configuration.md#bearer-token)).

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

`VerifyUserHasAnyAcceptedScope` Metoda będzie wyglądać następująco:

- Sprawdź, czy istnieje zgłoszenie o nazwie `http://schemas.microsoft.com/identity/claims/scope` lub `scp`.
- Sprawdź, czy w ramach tego żądania znajduje się wartość zawierająca zakres oczekiwany przez interfejs API.

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

Ten [przykładowy kod](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) jest przeznaczony dla ASP.NET Core. W przypadku ASP.NET, po `HttpContext.User` prostu `ClaimsPrincipal.Current`Zastąp wartość i Zastąp `"http://schemas.microsoft.com/identity/claims/scope"` typ `"scp"`"z. (Zobacz również fragment kodu w dalszej części tego artykułu).

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>Weryfikowanie ról aplikacji w interfejsach API wywoływanych przez aplikacje demona

Jeśli internetowy interfejs API jest wywoływany przez [aplikację demona](scenario-daemon-overview.md), aplikacja powinna wymagać uprawnień aplikacji do internetowego interfejsu API. Zaobserwowano [udostępnianie uprawnień aplikacji (ról aplikacji)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles) , które udostępnia interfejs API (na przykład `access_as_application` rolę aplikacji).
Teraz musisz mieć swoje interfejsy API, aby sprawdzić, czy otrzymany token zawiera `roles` zastrzeżenie i czy to zgłoszenie ma oczekiwaną wartość. Kod wykonujący tę weryfikację przypomina kod, który weryfikuje uprawnienia delegowane, z tą różnicą, że zamiast testowania `scopes`dla, Akcja kontrolera zostanie przetestowana dla: `roles`

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

`ValidateAppRole()` Metoda może wyglądać następująco:

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

Tym razem fragment kodu jest przeznaczony dla ASP.NET. W przypadku ASP.NET Core po prostu `ClaimsPrincipal.Current` Zastąp ciąg opcją `HttpContext.User`i `"roles"` `"http://schemas.microsoft.com/identity/claims/roles"`Zastąp ciąg nazwą usługi. (Zobacz również fragment kodu we wcześniejszej części tego artykułu).

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Akceptowanie tokenów tylko do aplikacji, jeśli internetowy interfejs API powinien być wywoływany tylko przez aplikacje demona

`roles` To zgłoszenie jest również używane dla użytkowników w wzorcach przypisywania użytkowników. (Zobacz [How to: Dodaj role aplikacji w swojej aplikacji i Odbierz je w tokenie](howto-add-app-roles-in-azure-ad-apps.md). Dlatego po prostu sprawdzanie ról będzie zezwalać aplikacjom na logowanie się jako użytkownicy i inne sposoby, jeśli role można przypisać do obu tych funkcji. Zalecamy zadeklarować różne role dla użytkowników i aplikacji, aby zapobiec tym nieporozumieniu.

Jeśli chcesz zezwolić na wywoływanie internetowego interfejsu API przez aplikacje demona, Dodaj warunek, gdy zostanie zweryfikowana rola aplikacji, że token jest tokenem tylko do aplikacji:

```CSharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Sprawdzanie warunku odwrotnego zezwala na dostęp do interfejsu API tylko aplikacjom, które logują się do użytkownika.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przenieś do środowiska produkcyjnego](scenario-protected-web-api-production.md)
