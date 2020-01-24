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
ms.openlocfilehash: c7c54c7c4718cea479a812574e961ef2338a6be6
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701760"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Internetowy interfejs API, który wywołuje interfejsy API sieci Web: wywoływanie interfejsu API

Po uzyskaniu tokenu można wywołać chroniony internetowy interfejs API. Można to zrobić na kontrolerze internetowego interfejsu API usługi ASP.NET lub ASP.NET Core.

## <a name="controller-code"></a>Kod kontrolera

Poniższy kod kontynuuje przykładowy kod, który jest wyświetlany w [interfejsie API sieci Web, który wywołuje interfejsy API sieci Web: uzyskuje token dla aplikacji](scenario-web-api-call-api-acquire-token.md). Kod jest wywoływany w akcjach kontrolerów interfejsu API. Wywołuje podrzędny interfejs API o nazwie *todolist*.

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

// After the token has been returned by Microsoft Authentication Library (MSAL), add it to the HTTP authorization header before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Internetowy interfejs API, który wywołuje interfejsy API sieci Web: Przenieś do środowiska produkcyjnego](scenario-web-api-call-api-production.md)
