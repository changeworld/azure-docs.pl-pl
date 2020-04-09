---
title: Wywoływanie internetowych interfejsów API z aplikacji klasycznej — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację klasyczną, która wywołuje internetowe interfejsy API
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 753892790a6f6b898b48d955e6806837967f3e92
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882968"
---
# <a name="desktop-app-that-calls-web-apis-call-a-web-api"></a>Aplikacja klasyczna wywołująca internetowe interfejsy API: wywoływanie internetowego interfejsu API

Teraz, gdy masz token, można wywołać chroniony interfejs API sieci web.

## <a name="call-a-web-api"></a>Wywoływanie interfejsu API sieci Web

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->
# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="java"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

# <a name="macos"></a>[Macos](#tab/macOS)

## <a name="call-a-web-api-in-msal-for-ios-and-macos"></a>Wywoływanie internetowego interfejsu API w systemie MSAL dla systemu iOS i macOS

Metody uzyskiwania tokenów `MSALResult` zwracają obiekt. `MSALResult`udostępnia `accessToken` właściwość, która może służyć do wywoływania interfejsu API sieci web. Dodaj token dostępu do nagłówka autoryzacji HTTP przed wywołaniem dostępu do chronionego interfejsu API sieci web.

Cel C:

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

Swift:

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]

let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

## <a name="call-several-apis-incremental-consent-and-conditional-access"></a>Wywoływanie kilku interfejsów API: przyrostowa zgoda i dostęp warunkowy

Aby wywołać kilka interfejsów API dla tego samego użytkownika, po `AcquireTokenSilent`dokonaniu tokenu dla pierwszego interfejsu API, wywołaj . Otrzymasz token dla innych interfejsów API po cichu przez większość czasu.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Interakcja jest wymagana, gdy:

- Użytkownik wyraził zgodę na pierwszy interfejs API, ale teraz musi wyrazić zgodę na więcej zakresów. Ten rodzaj zgody jest znany jako zgoda przyrostowa.
- Pierwszy interfejs API nie wymaga uwierzytelniania wieloskładnikowego, ale następny nie.

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
---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przenoszenie do środowiska produkcyjnego](scenario-desktop-production.md)
