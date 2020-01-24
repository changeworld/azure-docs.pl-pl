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
ms.openlocfilehash: 1c3d6f8d47152d70bdeaabbbc6d7b81187291857
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701811"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Internetowy interfejs API, który wywołuje interfejsy API sieci Web: uzyskiwanie tokenu dla aplikacji

Po skompilowaniu obiektu aplikacji klienckiej Użyj go, aby uzyskać token, którego można użyć do wywołania interfejsu API sieci Web.

## <a name="code-in-the-controller"></a>Kod w kontrolerze

Oto przykład kodu, który jest wywoływany w akcjach kontrolerów interfejsu API. Wywołuje podrzędny interfejs API o nazwie *todolist*.

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

`BuildConfidentialClient()` przypomina scenariusz w [interfejsie API sieci Web, który wywołuje interfejsy API sieci Web: Konfiguracja aplikacji](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()` tworzy wystąpienia `IConfidentialClientApplication` z pamięcią podręczną zawierającą informacje tylko dla jednego konta. Konto jest dostarczane przez metodę `GetAccountIdentifier`.

Metoda `GetAccountIdentifier` używa oświadczeń, które są skojarzone z tożsamością użytkownika, dla którego internetowy interfejs API odebrał token sieci Web JSON (JWT):

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
> [Internetowy interfejs API, który wywołuje interfejsy API sieci Web: wywoływanie interfejsu API](scenario-web-api-call-api-call-api.md)
