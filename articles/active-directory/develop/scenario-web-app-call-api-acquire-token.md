---
title: Aplikacja sieci Web, która wywołuje interfejsy API (Uzyskiwanie tokenu dla aplikacji) — w sieci web platforma tożsamości usługi Microsoft
description: Dowiedz się, jak utworzyć aplikację sieci Web, która wywołuje interfejsy API (pobierania tokenu dla aplikacji) w sieci web
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
ms.openlocfilehash: 653db995000308bb3ef78a9183696cd9d8ed1056
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074803"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Aplikacja sieci Web, która wywołuje interfejsy API — w sieci web uzyskać token dla aplikacji

Teraz został wcześniej utworzony zostanie obiekt aplikacji klienta, użyjesz go do uzyskania tokenu, który następnie użyjemy do wywoływania interfejsów API sieci web. W programie ASP.NET lub ASP.NET Core wywoływanie dany internetowy interfejs API następnie odbywa się w kontrolerze. Chodzi o:

- Aby uzyskać token internetowy interfejs API przy użyciu pamięci podręcznej tokenu. W tym celu należy wywołać `AcquireTokenSilent`
- Podczas wywoływania chronionego interfejsu API przy użyciu tokenu dostępu

## <a name="aspnet-core"></a>ASP.NET Core

Metody kontrolera są chronione przez `[Authorize]` atrybut, który wymuszającej uwierzytelnianie w celu korzystania z aplikacji sieci Web. Poniżej przedstawiono kod, który wywołuje program Microsoft Graph

```CSharp
[Authorize]
public class HomeController : Controller
{
 ...
}
```

Oto uproszczony kod akcji HomeController, które pobiera tokenu służącego do wywoływania programu Microsoft Graph.

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

Jeśli chcesz poznać szczegóły łącznie kodu jest wymagane dla tego scenariusza, zobacz Faza 2 [2-1-Web App wywołania programu Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph) kroku [ms-identity-aspnetcore-aplikacji sieci Web tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) samouczek

Istnieje wiele dodatkowej złożoności takich jak:

- Implementowanie pamięci podręcznej tokenu dla aplikacji sieci Web (samouczek przedstawia kilka implementacji)
- Usuwanie konta z pamięci podręcznej po użytkownik podpisuje skalowalnego w poziomie
- Wywoływanie kilka interfejsów API, obejmujące przyrostowe zgody

## <a name="aspnet"></a>ASP.NET

Czynności są podobne w programie ASP.NET:

- Akcja kontrolera, chronione przez atrybut [Authorize] wyodrębni dzierżawy, identyfikator i nazwę użytkownika, `ClaimsPrincipal` elementu członkowskiego kontrolera (aplikacja ASP.NET używa `HttpContext.User`)
- w tym miejscu zbudował platformy MSAL.NET `IConfidentialClientApplication`
- Wywołanie IT `AcquireTokenSilent` metody stosowania poufne klienta 

Kod jest podobny do kodu pokazanego dla platformy ASP.NET Core

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wywoływanie interfejsu API sieci Web](scenario-web-app-call-api-call-api.md)
