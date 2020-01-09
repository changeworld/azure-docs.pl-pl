---
title: Uzyskaj token dla internetowego interfejsu API, który wywołuje interfejsy API sieci Web | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć interfejs API sieci Web, który wywołuje interfejsy API sieci Web, które wymagają uzyskiwania tokenu dla aplikacji.
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
ms.openlocfilehash: cc92fb7bc5ddf451279e6c157f9e93aa7fe9a12a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423619"
---
# <a name="web-api-that-calls-web-apis---acquire-a-token-for-the-app"></a>Internetowy interfejs API wywołujący interfejsy API sieci Web — uzyskiwanie tokenu dla aplikacji

Po skompilowaniu obiektu aplikacji klienckiej Użyj go, aby uzyskać token, którego można użyć do wywołania interfejsu API sieci Web.

## <a name="code-in-the-controller"></a>Kod w kontrolerze

Oto przykład kodu, który zostanie wywołany w akcjach kontrolerów interfejsu API, wywołujący podrzędny interfejs API (o nazwie todolist).

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

`BuildConfidentialClient()` jest podobny do przedstawionego w artykule [internetowy interfejs API, który wywołuje interfejsy API sieci Web — konfiguracja aplikacji](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()` tworzy wystąpienia `IConfidentialClientApplication` z pamięcią podręczną, która zawiera tylko informacje dla jednego konta. Konto jest dostarczane przez metodę `GetAccountIdentifier`.

Metoda `GetAccountIdentifier` używa oświadczeń skojarzonych z tożsamością użytkownika, dla którego internetowy interfejs API odebrał token JWT:

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

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wywoływanie interfejsu API sieci Web](scenario-web-api-call-api-call-api.md)
