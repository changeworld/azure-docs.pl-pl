---
title: Aplikacja demona wywołująca interfejsy API sieci Web (Uzyskiwanie tokenów dla aplikacji) — Microsoft Identity platform
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a5f15aa5264c0abf87cb15f0468e8a3a924e0b5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562352"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Aplikacja demona, która wywołuje interfejsy API sieci Web — pozyskiwanie tokenu

Po skonstruowaniu poufnej aplikacji klienckiej można uzyskać token dla aplikacji przez wywołanie ``AcquireTokenForClient``, przekazanie zakresu i wymuszenie lub nie odświeżenie tokenu.

## <a name="scopes-to-request"></a>Zakresy do żądania

Zakresem żądania dla przepływu poświadczeń klienta jest nazwa zasobu, po którym następuje `/.default`. Ta notacja nakazuje usłudze Azure AD używanie **uprawnień na poziomie aplikacji** , które są zadeklarowane statycznie podczas rejestrowania aplikacji. Podobnie jak wcześniej, te uprawnienia interfejsu API muszą zostać przyznane przez administratora dzierżawy

### <a name="net"></a>.NET

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="python"></a>Python

W MSAL. W języku Python plik konfiguracji będzie wyglądać podobnie do następującego fragmentu kodu:

```Python
{
    "authority": "https://login.microsoftonline.com/organizations",
    "client_id": "your_client_id",
    "secret": "This is a sample only. You better NOT persist your password."
    "scope": ["https://graph.microsoft.com/.default"]
}
```

### <a name="java"></a>Java

```Java
public final static String KEYVAULT_DEFAULT_SCOPE = "https://vault.azure.net/.default";
```

### <a name="all"></a>Wszyscy

Zakres używany na potrzeby poświadczeń klienta zawsze powinien być identyfikatorem resourceId + "/.default"

### <a name="case-of-azure-ad-v10-resources"></a>Przypadek zasobów usługi Azure AD (v 1.0)

> [!IMPORTANT]
> W przypadku usługi MSAL (Microsoft Identity platform Endpoint) z prośbą o token dostępu dla zasobu akceptującego token dostępu w wersji 1.0 usługa Azure AD analizuje żądanych odbiorców od żądanego zakresu, pobierając wszystko przed ostatnim ukośnikiem i używając go jako identyfikatora zasobu.
> W związku z tym, jeśli tak **https://database.windows.net** , jak Azure SQL (), zasób oczekuje odbiorców kończących się ukośnikiem ( `https://database.windows.net/` w przypadku usługi Azure SQL:), należy zażądać zakresu `https://database.windows.net//.default` (należy pamiętać o podwójnym ukośniku). Zobacz również [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)problemu MSAL.NET: Końcowy ukośnik adresu URL zasobu został pominięty, co spowodowało błąd uwierzytelniania SQL.

## <a name="acquiretokenforclient-api"></a>Interfejs API AcquireTokenForClient

### <a name="net"></a>.NET

```CSharp
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

#### <a name="application-token-cache"></a>Pamięć podręczna tokenów aplikacji

W programie MSAL.NET `AcquireTokenForClient` korzysta z **pamięci podręcznej tokenów aplikacji** (wszystkie inne metody AcquireTokenXX używają pamięci podręcznej `AcquireTokenSilent` tokenów `AcquireTokenForClient` użytkowników `AcquireTokenSilent` ), które nie są wywoływane przed wywołaniem jako używa pamięci podręcznej tokenów **użytkownika** `AcquireTokenForClient`sprawdza sam pamięć podręczną tokenu **aplikacji** i aktualizuje go.

### <a name="python"></a>Python

```Python
# Firstly, looks up a token from cache
# Since we are looking for token for the current app, NOT for an end user,
# notice we give account parameter as None.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

### <a name="java"></a>Java

```Java
ClientCredentialParameters parameters = ClientCredentialParameters
        .builder(Collections.singleton(KEYVAULT_DEFAULT_SCOPE))
        .build();

CompletableFuture<AuthenticationResult> future = cca.acquireToken(parameters);

// You can complete the future in many different ways. Here we use .get() for simplicity
AuthenticationResult result = future.get();
```

### <a name="protocol"></a>Protocol

Jeśli nie masz jeszcze biblioteki dla wybranego języka, możesz chcieć użyć protokołu bezpośrednio:

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>Pierwszy przypadek: Żądanie tokenu dostępu z wspólnym kluczem tajnym

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>Drugi przypadek: Żądanie tokenu dostępu z certyfikatem

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

### <a name="learn-more-about-the-protocol"></a>Dowiedz się więcej o protokole

Aby uzyskać więcej informacji, zobacz dokumentację protokołu: [Program Microsoft Identity platform i przepływ poświadczeń klienta OAuth 2,0](v2-oauth2-client-creds-grant-flow.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="did-you-use-the-resourcedefault-scope"></a>Czy używasz tego zakresu zasobu/. default?

Jeśli zostanie wyświetlony komunikat o błędzie z informacją, że użyto nieprawidłowego zakresu, prawdopodobnie nie był `resource/.default` używany zakres.

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

> [!div class="nextstepaction"]
> [Aplikacja demona — wywoływanie internetowego interfejsu API](scenario-daemon-call-api.md)