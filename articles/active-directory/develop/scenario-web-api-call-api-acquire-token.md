---
title: Pobierz token dla internetowego interfejsu API, który wywołuje internetowe interfejsy API | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć internetowy interfejs API, który wywołuje internetowe interfejsy API, które wymagają nabycia tokenu dla aplikacji.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 79f8eb9e804502a7c0e61c18e4998fa05db10278
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885144"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Internetowy interfejs API, który wywołuje internetowe interfejsy API: uzyskiwanie tokenu dla aplikacji

Po zbudowaniu obiektu aplikacji klienckiej użyj go do uzyskania tokenu, którego można użyć do wywołania internetowego interfejsu API.

## <a name="code-in-the-controller"></a>Kod w sterowniku

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Oto przykład kodu, który jest wywoływany w działaniach kontrolerów interfejsu API. Wywołuje podrzędny interfejs API o nazwie *todolist*.

```csharp
private async Task GetTodoList(bool isAppStarting)
{
 ...
 //
 // Get an access token to call the To Do service.
 //
 AuthenticationResult result = null;
 try
 {
  app = BuildConfidentialClient(HttpContext, HttpContext.User);
  result = await app.AcquireTokenSilent(Scopes, account)
                     .ExecuteAsync()
                     .ConfigureAwait(false);
 }
...
}
```

`BuildConfidentialClient()`jest podobny do scenariusza w [interfejsie API sieci Web, który wywołuje interfejsy API sieci Web: konfiguracja aplikacji](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()`wystąpienia `IConfidentialClientApplication` z pamięcią podręczną zawierającą informacje tylko dla jednego konta. Konto jest dostarczane `GetAccountIdentifier` metodą.

Metoda `GetAccountIdentifier` używa oświadczeń, które są skojarzone z tożsamością użytkownika, dla którego internetowy interfejs API odebrał token JSON Web Token (JWT):

```csharp
public static string GetMsalAccountId(this ClaimsPrincipal claimsPrincipal)
{
 string userObjectId = GetObjectId(claimsPrincipal);
 string tenantId = GetTenantId(claimsPrincipal);

 if (    !string.IsNullOrWhiteSpace(userObjectId)
      && !string.IsNullOrWhiteSpace(tenantId))
 {
  return $"{userObjectId}.{tenantId}";
 }

 return null;
}
```

# <a name="java"></a>[Java](#tab/java)
Oto przykład kodu, który jest wywoływany w działaniach kontrolerów interfejsu API. Wywołuje interfejs API podrzędny — Microsoft Graph.

```java
@RestController
public class ApiController {

    @Autowired
    MsalAuthHelper msalAuthHelper;

    @RequestMapping("/graphMeApi")
    public String graphMeApi() throws MalformedURLException {

        String oboAccessToken = msalAuthHelper.getOboToken("https://graph.microsoft.com/.default");

        return callMicrosoftGraphMeEndpoint(oboAccessToken);
    }

}
```

# <a name="python"></a>[Python](#tab/python)

Interfejs API sieci Web języka Python będzie musiał użyć oprogramowania pośredniczącego, aby sprawdzić poprawność tokenu nośnika odebranego od klienta. Internetowy interfejs API można następnie uzyskać token dostępu dla interfejsu API [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) podrzędnego przy użyciu biblioteki MSAL Python, wywołując metodę. Przykład demonstruujący ten przepływ za pomocą biuletynu MSAL Python nie jest jeszcze dostępny.

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Internetowy interfejs API wywołujący internetowe interfejsy API: wywoływanie interfejsu API](scenario-web-api-call-api-call-api.md)
