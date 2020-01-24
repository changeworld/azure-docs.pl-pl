---
title: Uzyskaj token w aplikacjach sieci Web, które wywołują interfejsy API sieci Web — Microsoft Identity platform | Azure
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: a2bbe6e60aeb25d3c159c87228350065649c89cb
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701709"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Aplikacja sieci Web, która wywołuje interfejsy API sieci Web — uzyskuje token dla aplikacji

Teraz, gdy masz już obiekt aplikacji klienta, użyjesz go, aby uzyskać token do wywoływania internetowego interfejsu API. W ASP.NET lub ASP.NET Core wywołanie internetowego interfejsu API jest następnie wykonywane na kontrolerze. To wszystko:

- Uzyskiwanie tokenu dla internetowego interfejsu API przy użyciu pamięci podręcznej tokenów. Aby uzyskać ten token, należy wywołać `AcquireTokenSilent`.
- Wywoływanie chronionego interfejsu API z tokenem dostępu.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Metody kontrolera są chronione przez atrybut `[Authorize]`, który wymusza uwierzytelnienie użytkowników w celu korzystania z aplikacji sieci Web. Oto kod, który wywołuje Microsoft Graph.

```csharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions(see code below)

}
```

Usługa `ITokenAcquisition` jest wstrzykiwana przez ASP.NET za pomocą iniekcji zależności.


Poniżej przedstawiono uproszczony kod akcji HomeController, który pobiera token wywołujący Microsoft Graph.

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

 // use the access token to call a protected web API
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

Aby dokładniej zrozumieć kod wymagany dla tego scenariusza, zobacz krok 2 ([2-1 — wywołania aplikacji sieci Web Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) w samouczku [MS-Identity-aspnetcore-webapp-samouczka](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) .

Istnieje wiele dodatkowych złożoności, takich jak:

- Wywoływanie kilku interfejsów API,
- przetwarzanie przyrostowej zgody i dostępu warunkowego.

Te zaawansowane kroki są przetwarzane w rozdziale 3 samouczka [3-webapp-wiele interfejsów API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Elementy są podobne do ASP.NET:

- Akcja kontrolera chroniona przez atrybut [autoryzuje] wyodrębnia identyfikator dzierżawy i identyfikator użytkownika `ClaimsPrincipal` elementu członkowskiego kontrolera. (ASP.NET używa `HttpContext.User`.)
- Z tego miejsca kompiluje `IConfidentialClientApplication`MSAL.NET.
- Na koniec wywołuje metodę `AcquireTokenSilent` poufnej aplikacji klienckiej.

Kod jest podobny do kodu pokazanego dla ASP.NET Core.

# <a name="javatabjava"></a>[Java](#tab/java)

W przykładowym kodzie Java kod, który wywołuje interfejs API, znajduje się w metodzie getUsersFromGraph [AuthPageController. Java # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

Próbuje wywołać `getAuthResultBySilentFlow`. Jeśli użytkownik musi wyrazić zgodę na więcej zakresów, kod przetwarza `MsalInteractionRequiredException`, aby zakwestionować użytkownika.

```java
@RequestMapping("/msal4jsample/graph/me")
public ModelAndView getUserFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
        throws Throwable {

    IAuthenticationResult result;
    ModelAndView mav;
    try {
        result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
    } catch (ExecutionException e) {
        if (e.getCause() instanceof MsalInteractionRequiredException) {

            // If silent call returns MsalInteractionRequired, then redirect to Authorization endpoint
            // so user can consent to new scopes
            String state = UUID.randomUUID().toString();
            String nonce = UUID.randomUUID().toString();

            SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

            String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                    httpRequest.getParameter("claims"),
                    "User.Read",
                    authHelper.getRedirectUriGraph(),
                    state,
                    nonce);

            return new ModelAndView("redirect:" + authorizationCodeUrl);
        } else {

            mav = new ModelAndView("error");
            mav.addObject("error", e);
            return mav;
        }
    }

    if (result == null) {
        mav = new ModelAndView("error");
        mav.addObject("error", new Exception("AuthenticationResult not found in session."));
    } else {
        mav = new ModelAndView("auth_page");
        setAccountInfo(mav, httpRequest);

        try {
            mav.addObject("userInfo", getUserInfoFromGraph(result.accessToken()));

            return mav;
        } catch (Exception e) {
            mav = new ModelAndView("error");
            mav.addObject("error", e);
        }
    }
    return mav;
}
// Code omitted here.
```

# <a name="pythontabpython"></a>[Python](#tab/python)

W przykładowym języku Python kod wywołujący program Microsoft Graph jest w [App. PR # L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

Próbuje uzyskać token z pamięci podręcznej tokenów, a następnie wywołuje internetowy interfejs API po ustawieniu nagłówka autoryzacji. Jeśli nie jest to możliwe, ponowne znakowanie użytkownika.

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wywoływanie interfejsu API sieci Web](scenario-web-app-call-api-call-api.md)
