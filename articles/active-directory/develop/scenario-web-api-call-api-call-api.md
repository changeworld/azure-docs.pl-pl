---
title: Internetowy interfejs API, który wywołuje interfejsy API (wywołanie interfejsów API) — Platforma tożsamości usługi Microsoft w sieci web
description: Dowiedz się, jak utworzyć internetowy interfejs API tego wywołania podrzędne interfejsów API sieci web (wywoływanie interfejsu web API).
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
ms.openlocfilehash: 1cd093cc68a21558dc326221eeaa8c034c24f1c2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074728"
---
# <a name="web-api-that-calls-web-apis---call-an-api"></a>Internetowy interfejs API, który wywołuje internetowych interfejsów API — wywołania interfejsu API

Po utworzeniu tokenu, można wywoływać chroniony internetowy interfejs API. Można to zrobić w kontrolerze interfejsu API sieci Web ASP.NET/ASP.NET Core.

## <a name="controller-code"></a>Kod kontrolera

W tym miejscu jest kontynuacja kod przykładu przedstawiony w [wywołań interfejsu API sieci web chronionych internetowych interfejsów API — uzyskiwanie tokenu](scenario-web-api-call-api-acquire-token.md), co jest nazywane w akcjach kontrolerów interfejsów API, wywoływania podrzędnego interfejsu API, (o nazwie todolist).

Po nabył token służyć jako token elementu nośnego do wywołania interfejsu API niższego rzędu.

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

// Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przenoszenie do środowiska produkcyjnego](scenario-web-api-call-api-production.md)
