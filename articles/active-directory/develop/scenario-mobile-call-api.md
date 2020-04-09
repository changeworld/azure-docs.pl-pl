---
title: Wywoływanie internetowego interfejsu API z aplikacji mobilnej | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć aplikację mobilną, która wywołuje internetowe interfejsy API. (Wywołanie internetowego interfejsu API).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 28f57c5657ce2f8537a654a7f67ed4481fab2c91
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882696"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>Wywoływanie internetowego interfejsu API z aplikacji mobilnej

Po aplikacji loguje się do użytkownika i odbiera tokeny, Biblioteka uwierzytelniania firmy Microsoft (MSAL) udostępnia informacje o użytkowniku, środowisku użytkownika i wystawionych tokenów. Aplikacja może używać tych wartości do wywoływania internetowego interfejsu API lub wyświetlania wiadomości powitalnej dla użytkownika.

W tym artykule przyjrzymy się najpierw wynikowi MSAL. Następnie przyjrzymy się, jak używać `AuthenticationResult` `result` tokenu dostępu z lub wywołać chroniony interfejs API sieci web.

## <a name="msal-result"></a>Wynik MSAL
MSAL zawiera następujące wartości: 

- `AccessToken`wywołuje chronione interfejsy API sieci web w żądaniu okaziciela HTTP.
- `IdToken`zawiera przydatne informacje o zalogowanych użytkownikach. Informacje te obejmują nazwę użytkownika, dzierżawy domowej i unikatowy identyfikator magazynu.
- `ExpiresOn`to czas wygaśnięcia tokenu. MSAL obsługuje automatyczne odświeżanie aplikacji.
- `TenantId`jest identyfikatorem dzierżawy, w którym użytkownik się zalogował. W przypadku użytkowników-gości w usłudze Azure Active Directory (Azure AD) B2B ta wartość identyfikuje dzierżawę, w której użytkownik się zalogował. Wartość nie identyfikuje dzierżawy domowej użytkownika.  
- `Scopes`wskazuje zakresy, które zostały przyznane za pomocą tokenu. Przyznane zakresy mogą być podzbiorem żądanych zakresów.

MSAL zapewnia również abstrakcję dla `Account` wartości. Wartość `Account` reprezentuje konto zalogowane bieżącego użytkownika:

- `HomeAccountIdentifier`identyfikuje dzierżawę domową użytkownika.
- `UserName`jest preferowaną nazwą użytkownika. Ta wartość może być pusta dla użytkowników usługi Azure AD B2C.
- `AccountIdentifier`identyfikuje zalogowany użytkownik. W większości przypadków ta wartość jest `HomeAccountIdentifier` taka sama jak wartość, chyba że użytkownik jest gościem w innej dzierżawie.

## <a name="call-an-api"></a>Wywoływanie interfejsu API

Po tokenie dostępu można wywołać internetowy interfejs API. Aplikacja użyje tokenu do utworzenia żądania HTTP, a następnie uruchom żądanie.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="msal-for-ios-and-macos"></a>Biblioteka MSAL dla systemów iOS i macOS

Metody uzyskiwania tokenów `MSALResult` zwracają obiekt. `MSALResult`udostępnia właściwość. `accessToken` Można użyć `accessToken` do wywołania internetowego interfejsu API. Dodaj tę właściwość do nagłówka autoryzacji HTTP przed wywołaniem dostępu do chronionego interfejsu API sieci web.

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };
        
NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="make-several-api-requests"></a>Składanie kilku żądań interfejsu API

Jeśli chcesz wywołać ten sam interfejs API kilka razy lub jeśli chcesz wywołać wiele interfejsów API, podczas tworzenia aplikacji należy wziąć pod uwagę następujące tematy:

- **Zgoda przyrostowa:** Platforma tożsamości firmy Microsoft umożliwia aplikacjom uzyskanie zgody użytkownika, gdy wymagane są uprawnienia, a nie wszystkie na początku. Za każdym razem, gdy aplikacja jest gotowa do wywołania interfejsu API, powinna zażądać tylko zakresów, których potrzebuje.

- **Dostęp warunkowy:** Podczas tworzenia kilku żądań interfejsu API, w niektórych scenariuszach może być konieczne spełnienie dodatkowych wymagań dotyczących dostępu warunkowego. Wymagania mogą wzrosnąć w ten sposób, jeśli pierwsze żądanie nie ma zasad dostępu warunkowego, a aplikacja próbuje dyskretnie uzyskać dostęp do nowego interfejsu API, który wymaga dostępu warunkowego. Aby poradzić sobie z tym problemem, należy złapać błędy z cichych żądań i być przygotowanym do złożenia interaktywnego żądania.  Aby uzyskać więcej informacji, zobacz [Wskazówki dotyczące dostępu warunkowego](../azuread-dev/conditional-access-dev-guide.md).

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>Wywoływanie kilku interfejsów API przy użyciu zgody przyrostowej i dostępu warunkowego

Jeśli musisz wywołać kilka interfejsów API dla tego samego użytkownika, po pozyskaniu tokenu dla użytkownika, można `AcquireTokenSilent` uniknąć wielokrotnego proszenia użytkownika o poświadczenia, następnie wywołując token:

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Interakcja jest wymagana, gdy:

- Użytkownik wyraził zgodę na pierwszy interfejs API, ale teraz musi wyrazić zgodę na więcej zakresów. W takim przypadku użytkownik korzysta z przyrostowej zgody.
- Pierwszy interfejs API nie wymaga uwierzytelniania wieloskładnikowego, ale następny interfejs API nie.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przenoszenie do środowiska produkcyjnego](scenario-mobile-production.md)
