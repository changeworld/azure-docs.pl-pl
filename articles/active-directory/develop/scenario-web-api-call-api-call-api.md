---
title: Interfejs API sieci Web, który wywołuje interfejsy API sieci Web — Microsoft Identity platform | Azure
description: Dowiedz się, jak utworzyć internetowy interfejs API, który wywołuje interfejsy API sieci Web.
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
ms.openlocfilehash: 5829ca41aaa4bd61f8878657e5eedbf6351b5df4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423578"
---
# <a name="web-api-that-calls-web-apis---call-an-api"></a>Internetowy interfejs API, który wywołuje interfejsy API sieci Web — wywołuje interfejs API

Po uzyskaniu tokenu można wywołać chroniony internetowy interfejs API. Jest to realizowane z kontrolera interfejsu API sieci Web ASP.NET/ASP.NET Core.

## <a name="controller-code"></a>Kod kontrolera

Oto kontynuacja przykładowego kodu podanego w obszarze [chronione interfejsy API sieci Web wywołania interfejsów API sieci Web — uzyskiwanie tokenu](scenario-web-api-call-api-acquire-token.md), który jest wywoływany w akcjach kontrolerów interfejsu użytkownika, wywoływanie podrzędnego interfejsu API (o nazwie todolist).

Po uzyskaniu tokenu Użyj go jako tokenu okaziciela, aby wywołać podrzędny interfejs API.

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

// Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przenieś do środowiska produkcyjnego](scenario-web-api-call-api-production.md)
