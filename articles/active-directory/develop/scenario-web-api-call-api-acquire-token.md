---
title: Internetowy interfejs API, wywołuje inne interfejsy API sieci web (Uzyskiwanie tokenu dla aplikacji) — Platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć internetowy interfejs API, że wywołania innych internetowych interfejsów API (pobierania tokenu dla aplikacji).
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
ms.openlocfilehash: 986e2e0f8a481d61dc870af2548290658b44d2d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65231097"
---
# <a name="web-api-that-calls-web-apis---acquire-a-token-for-the-app"></a>Internetowy interfejs API, która wywołuje interfejsy API — w sieci web uzyskać token dla aplikacji

Po dołączeniu aplikacji klienckiej obiektu, można uzyskać tokenu, który służy do wywoływania interfejsu API sieci web.

## <a name="code-in-the-controller"></a>Możesz pisać kod w kontrolerze

Poniżej przedstawiono przykładowy kod, który zostanie wywołany w akcjach kontrolerów interfejsów API, wywoływanie podrzędnego interfejsu API (o nazwie todolist).

```CSharp
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

`BuildConfidentialClient()` jest podobny do opisane w artykule [interfejsu API sieci Web, która wywołuje interfejsy API — Konfiguracja aplikacji w sieci web](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()` tworzy wystąpienie `IConfidentialClientApplication` z pamięcią podręczną, która zawiera tylko informacje dotyczące jednego konta. Konto jest udostępniane przez `GetAccountIdentifier` metody.

`GetAccountIdentifier` Metoda używa oświadczenia skojarzone z tożsamością użytkownika, dla którego internetowy interfejs API Odebrano tokenu JWT:

```CSharp
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

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wywoływanie interfejsu web API](scenario-web-api-call-api-call-api.md)
