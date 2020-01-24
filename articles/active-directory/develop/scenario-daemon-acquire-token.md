---
title: Uzyskiwanie tokenów do wywoływania internetowego interfejsu API (aplikacja demona) — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację demona, która wywołuje interfejsy API sieci Web (Uzyskiwanie tokenów)
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 38df99f0a4932f477e900382c7ff1ae7b50febe9
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702474"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Aplikacja demona, która wywołuje interfejsy API sieci Web — pozyskiwanie tokenu

Po skonstruowaniu poufnej aplikacji klienckiej można uzyskać token dla aplikacji, wywołując ``AcquireTokenForClient``, przekazując zakres i wymuszanie lub nie odświeżanie tokenu.

## <a name="scopes-to-request"></a>Zakresy do żądania

Zakresem żądania dla przepływu poświadczeń klienta jest nazwa zasobu, po którym następuje `/.default`. Ta notacja nakazuje usłudze Azure AD używanie **uprawnień na poziomie aplikacji** , które są zadeklarowane statycznie podczas rejestrowania aplikacji. Podobnie jak wcześniej, te uprawnienia interfejsu API muszą zostać przyznane przez administratora dzierżawy

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

W MSAL Python plik konfiguracji będzie wyglądać podobnie do następującego fragmentu kodu:

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="case-of-azure-ad-v10-resources"></a>Przypadek zasobów usługi Azure AD (v 1.0)

Zakres używany na potrzeby poświadczeń klienta zawsze powinien być identyfikatorem resourceId + "/.default"

> [!IMPORTANT]
> W przypadku MSAL z prośbą o token dostępu dla zasobu akceptującego token dostępu w wersji 1.0 usługa Azure AD analizuje żądanych odbiorców od żądanego zakresu, pobierając wszystko przed ostatnim ukośnikiem i używając go jako identyfikatora zasobu.
> W związku z tym, jeśli tak, jak Azure SQL ( **https://database.windows.net** ), zasób oczekuje odbiorców kończących się ukośnikiem (w przypadku usługi Azure sql: `https://database.windows.net/` ), należy zażądać zakresu `https://database.windows.net//.default` (należy pamiętać o podwójnym ukośniku). Zobacz również [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)problemu MSAL.NET: zostanie pominięty końcowy ukośnik adresu URL zasobu, co spowodowało błąd uwierzytelniania SQL.

## <a name="acquiretokenforclient-api"></a>Interfejs API AcquireTokenForClient

Aby uzyskać token dla aplikacji, użyj `AcquireTokenForClient` lub odpowiednika w zależności od platform.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```csharp
using Microsoft.Identity.Client;

// With client credentials flows the scopes is ALWAYS of the shape "resource/.default", as the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application does not have sufficient permissions
    // - did you declare enough app permissions in during the app creation?
    // - did the tenant admin needs to grant permissions to the application.
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be of the form "https://resourceurl/.default"
    // Mitigation: change the scope to be as expected !
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# Firstly, looks up a token from cache
# Since we are looking for token for the current app, NOT for an end user,
# notice we give account parameter as None.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You may need this when reporting a bug
```

# <a name="javatabjava"></a>[Java](#tab/java)

Jest to wyciąg z [przykładów deweloperskich MSAL języka Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/).

```Java
ClientCredentialParameters clientCredentialParam = ClientCredentialParameters.builder(
        Collections.singleton(GRAPH_DEFAULT_SCOPE))
        .build();

CompletableFuture<IAuthenticationResult> future = app.acquireToken(clientCredentialParam);

BiConsumer<IAuthenticationResult, Throwable> processAuthResult = (res, ex) -> {
    if (ex != null) {
        System.out.println("Oops! We have an exception - " + ex.getMessage());
    }
    System.out.println("Returned ok - " + res);
    System.out.println("ID Token - " + res.idToken());

    /* call a protected API with res.accessToken() */
};

future.whenCompleteAsync(processAuthResult);
future.join();
```

---

### <a name="protocol"></a>Protocol (Protokół)

Jeśli nie masz jeszcze biblioteki dla wybranego języka, możesz chcieć użyć protokołu bezpośrednio:

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>Pierwszy przypadek: żądanie tokenu dostępu przy użyciu wspólnego klucza tajnego

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>Drugi przypadek: żądanie tokenu dostępu z certyfikatem

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

Aby uzyskać więcej informacji, zobacz dokumentację protokołu: [Microsoft Identity platform oraz przepływ poświadczeń klienta OAuth 2,0](v2-oauth2-client-creds-grant-flow.md).

## <a name="application-token-cache"></a>Pamięć podręczna tokenów aplikacji

W MSAL.NET `AcquireTokenForClient` korzysta z **pamięci podręcznej tokenów aplikacji** (wszystkie inne metody AcquireTokenXX używają pamięci podręcznej tokenów użytkowników) nie wywołuj `AcquireTokenSilent` przed wywołaniem `AcquireTokenForClient` jako `AcquireTokenSilent` korzysta z pamięci podręcznej tokenów **użytkownika** . `AcquireTokenForClient` sprawdza sam pamięć podręczną tokenów **aplikacji** i aktualizuje go.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="did-you-use-the-resourcedefault-scope"></a>Czy używasz tego zakresu zasobu/. default?

Jeśli zostanie wyświetlony komunikat o błędzie z informacją, że użyto nieprawidłowego zakresu, prawdopodobnie nie użyto zakresu `resource/.default`.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Czy pamiętasz o podaniu zgody administratora? Aplikacje demonów wymagają!

Jeśli wystąpi błąd podczas wywoływania interfejsu API **niewystarczające uprawnienia do ukończenia tej operacji**, Administrator dzierżawy musi udzielić uprawnień do aplikacji. Zobacz Krok 6 rejestracji aplikacji klienckiej powyżej.
Zwykle zobaczysz i wystąpi błąd podobny do następującego:

```JSon
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

## <a name="next-steps"></a>Następne kroki

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Aplikacja demona — wywoływanie internetowego interfejsu API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Aplikacja demona — wywoływanie internetowego interfejsu API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Aplikacja demona — wywoływanie internetowego interfejsu API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=java)

---
