---
title: Żądanie oświadczeń niestandardowych (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak żądać oświadczeń niestandardowych.
services: active-directory
documentationcenter: ''
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 44158296faaf238fd72f2360149d3d93f68c5ba0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085609"
---
# <a name="how-to-request-custom-claims-using-msal-for-ios-and-macos"></a>Jak: Żądanie oświadczeń niestandardowych przy użyciu usługi MSAL dla systemu iOS i macOS

OpenID Connect umożliwia opcjonalnie żądanie zwrotu poszczególnych oświadczeń z punktu końcowego UserInfo i/lub tokenu identyfikatora. Żądanie oświadczeń jest reprezentowane jako obiekt JSON, który zawiera listę żądanych oświadczeń. Zobacz [OpenID Connect Core 1.0,](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter) aby uzyskać więcej informacji.

Biblioteka uwierzytelniania firmy Microsoft (MSAL) dla systemu iOS i macOS umożliwia żądanie określonych oświadczeń zarówno w scenariuszach nabycia tokenów interaktywnych, jak i cichych. Robi to za `claimsRequest` pośrednictwem parametru.

Istnieje wiele scenariuszy, w których jest to potrzebne. Przykład:

- Żądanie oświadczeń poza standardowym zestawem dla aplikacji.
- Żądanie określonych kombinacji standardowych oświadczeń, których nie można określić przy użyciu zakresów dla aplikacji. Na przykład jeśli token dostępu zostanie odrzucony z powodu brakujących oświadczeń, aplikacja może zażądać brakujących oświadczeń przy użyciu usługi MSAL.

> [!NOTE]
> MSAL pomija pamięć podręczną tokenu dostępu, gdy zostanie określone żądanie oświadczeń. Ważne jest, aby `claimsRequest` podać tylko parametr, gdy potrzebne są dodatkowe `claimsRequest` oświadczenia (w przeciwieństwie do zawsze zapewniając ten sam parametr w każdym wywołaniu interfejsu API MSAL).

`claimsRequest`można określić `MSALSilentTokenParameters` `MSALInteractiveTokenParameters`w i:

```objc
/*!
 MSALTokenParameters is the base abstract class for all types of token parameters (silent and interactive).
 */
@interface MSALTokenParameters : NSObject

/*!
 The claims parameter that needs to be sent to authorization or token endpoint.
 If claims parameter is passed in silent flow, access token will be skipped and refresh token will be tried.
 */
@property (nonatomic, nullable) MSALClaimsRequest *claimsRequest;

@end
```
`MSALClaimsRequest`można skonstruować z NSString reprezentacji żądań oświadczeń JSON. 

Cel C:

```objc
NSError *claimsError = nil;
MSALClaimsRequest *request = [[MSALClaimsRequest alloc] initWithJsonString:@"{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}" error:&claimsError];
```

Swift:

```swift
var requestError: NSError? = nil
let request = MSALClaimsRequest(jsonString: "{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}",
                                        error: &requestError)
```



Można go również zmodyfikować, żądając dodatkowych konkretnych oświadczeń:

Cel C:

```objc
MSALIndividualClaimRequest *individualClaimRequest = [[MSALIndividualClaimRequest alloc] initWithName:@"custom_claim"];
individualClaimRequest.additionalInfo = [MSALIndividualClaimRequestAdditionalInfo new];
individualClaimRequest.additionalInfo.essential = @1;
individualClaimRequest.additionalInfo.value = @"myvalue";
[request requestClaim:individualClaimRequest forTarget:MSALClaimsRequestTargetIdToken error:&claimsError];
```

Swift:

```swift
let individualClaimRequest = MSALIndividualClaimRequest(name: "custom-claim")
let additionalInfo = MSALIndividualClaimRequestAdditionalInfo()
additionalInfo.essential = 1
additionalInfo.value = "myvalue"
individualClaimRequest.additionalInfo = additionalInfo
do {
  try request.requestClaim(individualClaimRequest, for: .idToken)
} catch let error as NSError {
  // handle error here  
}
        
```



`MSALClaimsRequest`następnie należy ustawić w parametrach tokenu i dostarczyć do jednego z interfejsów API pozyskiwania tokenów MSAL:

Cel C:

```objc
MSALPublicClientApplication *application = ...;
MSALWebviewParameters *webParameters = ...;

MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"user.read"]
                                                                                  webviewParameters:webParameters];
parameters.claimsRequest = request;
    
[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let webParameters: MSALWebviewParameters!
        
let parameters = MSALInteractiveTokenParameters(scopes: ["user.read"], webviewParameters: webParameters)
parameters.claimsRequest = request
        
application.acquireToken(with: parameters) { (result: MSALResult?, error: Error?) in            ...

```



## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [przepływach uwierzytelniania i scenariuszach aplikacji](authentication-flows-app-scenarios.md)
