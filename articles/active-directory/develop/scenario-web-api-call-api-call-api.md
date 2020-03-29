---
title: Internetowy interfejs API, który wywołuje internetowe interfejsy API — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć internetowy interfejs API, który wywołuje internetowe interfejsy API.
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
ms.openlocfilehash: d66a08d4e84a3771d6c3fa46b96c975869435452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76833383"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Internetowy interfejs API wywołujący internetowe interfejsy API: wywoływanie interfejsu API

Po tokenie można wywołać chroniony interfejs API sieci web. Można to zrobić z kontrolera interfejsu API sieci web.

## <a name="controller-code"></a>Kod kontrolera

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Poniższy kod kontynuuje przykładowy kod, który jest wyświetlany w [interfejsie API sieci web, który wywołuje interfejsy API sieci web: Uzyskaj token dla aplikacji](scenario-web-api-call-api-acquire-token.md). Kod jest wywoływany w akcjach kontrolerów interfejsu API. Wywołuje podrzędny interfejs API o nazwie *todolist*.

Po nabyciu tokenu użyj go jako tokenu nośnika do wywołania interfejsu API podrzędnego.

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

# <a name="java"></a>[Java](#tab/java)

Poniższy kod kontynuuje przykładowy kod, który jest wyświetlany w [interfejsie API sieci web, który wywołuje interfejsy API sieci web: Uzyskaj token dla aplikacji](scenario-web-api-call-api-acquire-token.md). Kod jest wywoływany w akcjach kontrolerów interfejsu API. Wywołuje podrzędny interfejs API MS Graph.

Po nabyciu tokenu użyj go jako tokenu nośnika do wywołania interfejsu API podrzędnego.

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
Przykład demonstruujący ten przepływ za pomocą biuletynu MSAL Python nie jest jeszcze dostępny.

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Internetowy interfejs API, który wywołuje internetowe interfejsy API: przejście do produkcji](scenario-web-api-call-api-production.md)
