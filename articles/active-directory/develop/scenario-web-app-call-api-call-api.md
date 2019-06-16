---
title: Aplikacja sieci Web, że wywołania internetowych interfejsów API (wywoływanie interfejsu web API) — Platforma tożsamości firmy Microsoft
description: Informacje o sposobie tworzenia aplikacji sieci Web, która wywołuje internetowych interfejsów API (wywoływanie interfejsu web API)
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
ms.openlocfilehash: dd44dda06b2f6fc48538f2fb74c0bf8e04d0362b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074638"
---
# <a name="web-app-that-calls-web-apis---call-a-web-api"></a>Aplikacja sieci Web, która wywołuje interfejsy API — w sieci web wywołania interfejsu API sieci web

Teraz, gdy masz token, można wywoływać chroniony internetowy interfejs API.

## <a name="aspnet-core"></a>ASP.NET Core

Oto uproszczony kod akcji `HomeController`. Ten kod pobiera tokenu służącego do wywoływania programu Microsoft Graph. Tego kodu w czasie został dodany, przedstawiający sposób wywoływania programu Microsoft Graph jako interfejs API REST.

```CSharp
public async Task<IActionResult> Profile()
{
 var application = BuildConfidentialClientApplication(HttpContext, HttpContext.User);
 string accountIdentifier = claimsPrincipal.GetMsalAccountId();
 string loginHint = claimsPrincipal.GetLoginHint();

 // Get the account
 IAccount account = await application.GetAccountAsync(accountIdentifier);

 // Special case for guest users as the Guest iod / tenant id are not surfaced.
 if (account == null)
 {
  var accounts = await application.GetAccountsAsync();
  account = accounts.FirstOrDefault(a => a.Username == loginHint);
 }

 AuthenticationResult result;
 result = await application.AcquireTokenSilent(new []{"user.read"}, account)
                            .ExecuteAsync();
 var accessToken = result.AccessToken;

 // Calls the web API (here the graph)
 HttpClient httpClient = new HttpClient();
 httpClient.DefaultRequestHeaders.Authorization =
     new AuthenticationHeaderValue(Constants.BearerAuthorizationScheme,accessToken);
 var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

 if (response.StatusCode == HttpStatusCode.OK)
 {
  var content = await response.Content.ReadAsStringAsync();

  dynamic me = JsonConvert.DeserializeObject(content);
  return me;
 }

 ViewData["Me"] = me;
 return View();
}
```

> [!NOTE]
> Ta sama zasada służy do wywołania dowolnego interfejsu API sieci web.
>
> Najbardziej w usłudze Azure web API udostępniają zestaw SDK, który upraszcza wywoływaniu tego elementu. Jest to również w przypadku programu Microsoft Graph. W następnym artykule dowiesz się, gdzie można znaleźć samouczek pokazujący tych aspektów.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przenoszenie do środowiska produkcyjnego](scenario-web-app-call-api-production.md)
