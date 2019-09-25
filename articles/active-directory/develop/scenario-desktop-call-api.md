---
title: Aplikacja klasyczna, która wywołuje interfejsy API sieci Web (wywołując internetowy interfejs API) — Microsoft Identity platform
description: Dowiedz się, jak utworzyć aplikację klasyczną wywołującą interfejsy API sieci Web (wywołując internetowy interfejs API)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56d3d01e39adfeb6bf2ef5e7e7d595f49c90f5a5
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268280"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>Aplikacja klasyczna, która wywołuje interfejsy API sieci Web — wywołuje internetowy interfejs API

Teraz, gdy masz token, możesz wywołać chroniony internetowy interfejs API.

## <a name="calling-a-web-api-from-net"></a>Wywoływanie internetowego interfejsu API z platformy .NET

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-a-web-api-in-msal-for-ios-and-macos"></a>Wywoływanie interfejsu API sieci Web w programie MSAL dla systemów iOS i macOS

Metody uzyskiwania tokenów zwracają `MSALResult` obiekt. `MSALResult``accessToken` uwidacznia właściwość, która może służyć do wywoływania internetowego interfejsu API. Token dostępu należy dodać do nagłówka autoryzacji HTTP przed wywołaniem dostępu do chronionego internetowego interfejsu API.

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

Jeśli konieczne jest wywołanie kilku interfejsów API dla tego samego użytkownika, po uzyskaniu tokenu dla pierwszego interfejsu API można wywołać metodę `AcquireTokenSilent`i uzyskać token dla innych interfejsów API dyskretnie większości czasu.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Sytuacje, w których wymagane jest interakcja, to:

- Użytkownik wyraził zgodę na pierwszy interfejs API, ale teraz musi powyrazić zgody na więcej zakresów (powiększanie zgody)
- Pierwszy interfejs API nie wymagał uwierzytelniania wieloskładnikowego, ale Następna z nich robi.

```CSharp
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
> [Przenieś do środowiska produkcyjnego](scenario-desktop-production.md)
