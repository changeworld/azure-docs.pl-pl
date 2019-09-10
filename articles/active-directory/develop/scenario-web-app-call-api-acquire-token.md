---
title: Aplikacja sieci Web, która wywołuje interfejsy API sieci Web (Uzyskiwanie tokenu dla aplikacji) — Microsoft Identity platform
description: Dowiedz się, jak utworzyć aplikację sieci Web, która wywołuje interfejsy API sieci Web (Uzyskiwanie tokenu dla aplikacji)
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
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3aa144c76fb0a8e479658efdb5d43361fbbc085c
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860628"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Aplikacja sieci Web, która wywołuje interfejsy API sieci Web — uzyskuje token dla aplikacji

Teraz, gdy masz już obiekt aplikacji klienta, użyjesz go, aby uzyskać token do wywoływania internetowego interfejsu API. W ASP.NET lub ASP.NET Core wywołanie internetowego interfejsu API jest następnie wykonywane na kontrolerze. To wszystko:

- Uzyskiwanie tokenu dla internetowego interfejsu API przy użyciu pamięci podręcznej tokenów. Aby uzyskać ten token, należy wywołać `AcquireTokenSilent`.
- Wywoływanie chronionego interfejsu API z tokenem dostępu.

## <a name="aspnet-core"></a>ASP.NET Core

Metody kontrolera są chronione przez `[Authorize]` atrybut, który wymusza uwierzytelnienie użytkowników w celu korzystania z aplikacji sieci Web. Oto kod, który wywołuje Microsoft Graph.

```CSharp
[Authorize]
public class HomeController : Controller
{
 ...
}
```

Poniżej przedstawiono uproszczony kod akcji HomeController, który pobiera token wywołujący Microsoft Graph.

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
 ...
 // use the access token to call a web API
}
```

Aby dokładniej zrozumieć kod wymagany dla tego scenariusza, zobacz krok 2 ([2-1 — wywołania aplikacji sieci Web Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) w samouczku [MS-Identity-aspnetcore-webapp-samouczka](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) .

Istnieje wiele dodatkowych złożoności, takich jak:

- Implementowanie pamięci podręcznej tokenów dla aplikacji sieci Web (samouczek zawiera kilka implementacji)
- Usuwanie konta z pamięci podręcznej po wylogowaniu się użytkownika
- Wywoływanie kilku interfejsów API, w tym dla posiadania przyrostowej zgody

## <a name="aspnet"></a>ASP.NET

Elementy są podobne do ASP.NET:

- Akcja kontrolera chroniona przez atrybut [autoryzuje] wyodrębnia identyfikator dzierżawy i identyfikator `ClaimsPrincipal` użytkownika elementu członkowskiego kontrolera. (ASP.NET używa `HttpContext.User`.)
- Z tego miejsca tworzy MSAL.NET `IConfidentialClientApplication`.
- Na koniec wywołuje `AcquireTokenSilent` metodę poufnej aplikacji klienckiej.

Kod jest podobny do kodu pokazanego dla ASP.NET Core.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wywoływanie interfejsu API sieci Web](scenario-web-app-call-api-call-api.md)
