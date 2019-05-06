---
title: Demon aplikacji wywołania sieci web interfejsy API (Uzyskiwanie tokenów dla aplikacji) — Platforma tożsamości usługi Microsoft
description: Dowiedz się, jak utworzyć aplikację demona wywołuje interfejsy API, (uzyskiwania tokenów) sieci web
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
ms.openlocfilehash: aa4f5dc7a5aceaf81f71eacd36d131471a57e5c0
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075373"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Demon aplikacji, która wywołuje interfejsy API — w sieci web uzyskać token

Gdy jest tworzona aplikacja kliencka poufne, można uzyskać token dla aplikacji, wywołując ``AcquireTokenForClient``, przekazując zakresu i wymuszenie lub nie odświeżenia tokenu.

## <a name="scopes-to-request"></a>Zakres żądania

Zakres żądania przepływ poświadczeń klienta jest nazwa zasobu, następuje `/.default`. Ten zapis informuje usługi Azure AD do użycia **uprawnień na poziomie aplikacji** zadeklarowanych statycznie podczas rejestracji aplikacji. Ponadto, jak pokazano wcześniej, te interfejsu API muszą być przyznane uprawnienia przez administratora dzierżawy

### <a name="net"></a>.NET

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="python"></a>Python

W MSAL. Python i plik konfiguracji będzie wyglądać poniższy fragment kodu:

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

Zakres używany dla poświadczeń klienta powinna zawsze być resourceId + "/ .default"

### <a name="case-of-v10-resources"></a>Przypadek zasobów w wersji 1.0

> [!IMPORTANT]
> Biblioteka MSAL (punktu końcowego v2.0) pytaniem tokenu dostępu dla zasobu akceptowania tokenu dostępu w wersji 1.0 usługi Azure AD zostanie przetworzony przez pobranie wszystkiego wcześniej niż ostatni ukośnika i używać go jako identyfikator zasobu odbiorców w żądanym zakresie.
> W związku z tym if, takich jak Azure SQL (**https://database.windows.net**) zasobu oczekuje odbiorcami kończącego się ukośnikiem (dla usługi Azure SQL: `https://database.windows.net/`), należy zażądać zakresu `https://database.windows.net//.default` (Uwaga podwójny ukośnik). Zobacz też platformy MSAL.NET problem [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Ukośnika url zasobu zostanie pominięty, który spowodował niepowodzenie uwierzytelniania sql.

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

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

#### <a name="application-token-cache"></a>Pamięć podręczną tokenu aplikacji

W przypadku platformy MSAL.NET `AcquireTokenForClient` używa **pamięci podręcznej tokenu aplikacji** (wszystkie inne metody AcquireTokenXX Użyj pamięci podręcznej tokenu użytkownika) Nie wywołuj `AcquireTokenSilent` przed wywołaniem `AcquireTokenForClient` jako `AcquireTokenSilent` używa **użytkownika** tokenu z pamięci podręcznej. `AcquireTokenForClient` sprawdza, czy **aplikacji** token w pamięci podręcznej sam i aktualizuje go.

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

### <a name="protocol"></a>Protokół

Jeśli nie masz jeszcze bibliotekę wybranym języku, możesz chcieć użyć protokołu bezpośrednio:

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>Pierwszy przypadek: Żądanie tokenu dostępu za pomocą wspólny klucz tajny

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>Drugi przypadek: Żądanie tokenu dostępu przy użyciu certyfikatu

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

### <a name="learn-more-about-the-protocol"></a>Dowiedz się więcej na temat protokołu

Aby uzyskać więcej informacji zobacz dokumentację protokołu: [Klienta OAuth 2.0 i usługi Azure Active Directory w wersji 2.0 credentials flow](v2-oauth2-client-creds-grant-flow.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="did-you-use-the-resourcedefault-scope"></a>Czy użyto zakres zasobów/.default?

Jeśli otrzymasz komunikat o błędzie informujący, że użyto nieprawidłowego zakresu, prawdopodobnie został użyty przez Ciebie `resource/.default` zakresu.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Użytkownik zapomniał podać zgody administratora? Daemon apps potrzebny!

Jeśli wystąpi błąd podczas wywoływania interfejsu API **uprawnienia niewystarczające do ukończenia operacji**, administrator dzierżawy musi udzielić uprawnień do aplikacji. Zobacz krok 6 rejestru powyżej aplikacji klienckiej.
Zazwyczaj zobaczysz i błędów, takich jak poniższy opis błędu:

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

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Demon aplikacji — wywoływanie interfejsu web API](scenario-daemon-call-api.md)