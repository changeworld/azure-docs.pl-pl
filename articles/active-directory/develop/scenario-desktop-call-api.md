---
title: Wywołaj interfejsy API sieci Web z aplikacji klasycznej — Microsoft Identity platform | Azure
description: Dowiedz się, jak utworzyć aplikację klasyczną wywołującą interfejsy API sieci Web
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75bb919b73791b78084e82351d7d6b7d93edc322
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423865"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>Aplikacja klasyczna, która wywołuje interfejsy API sieci Web — wywołuje internetowy interfejs API

Teraz, gdy masz token, możesz wywołać chroniony internetowy interfejs API.

## <a name="calling-a-web-api"></a>Wywoływanie internetowego interfejsu API

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->
# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="javatabjava"></a>[Java](#tab/java)

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

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

## <a name="calling-a-web-api-in-msal-for-ios-and-macos"></a>Wywoływanie interfejsu API sieci Web w programie MSAL dla systemów iOS i macOS

Metody uzyskiwania tokenów zwracają `MSALResult` obiektu. `MSALResult` uwidacznia Właściwość `accessToken`, która może służyć do wywoływania internetowego interfejsu API. Token dostępu należy dodać do nagłówka autoryzacji HTTP przed wywołaniem dostępu do chronionego internetowego interfejsu API.

Cel-C:

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

Adres

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]

let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

## <a name="calling-several-apis---incremental-consent-and-conditional-access"></a>Wywoływanie kilku interfejsów API — wyrażanie zgody i dostępu warunkowego

Jeśli konieczne jest wywołanie kilku interfejsów API dla tego samego użytkownika, po uzyskaniu tokenu dla pierwszego interfejsu API można tylko wywołać `AcquireTokenSilent`i uzyskać token dla innych interfejsów API w większości czasu.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Sytuacje, w których wymagane jest interakcja, to:

- Użytkownik wyraził zgodę na pierwszy interfejs API, ale teraz musi powyrazić zgody na więcej zakresów (powiększanie zgody)
- Pierwszy interfejs API nie wymagał uwierzytelniania wieloskładnikowego, ale Następna z nich robi.

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
> [Przenieś do środowiska produkcyjnego](scenario-desktop-production.md)
