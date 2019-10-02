---
title: Aplikacja sieci Web, która wywołuje interfejsy API sieci Web (logowanie) — Microsoft Identity platform
description: Dowiedz się, jak utworzyć aplikację sieci Web, która wywołuje interfejsy API sieci Web (logowanie)
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
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3036f8cb72f2a07673743a77e8be37614002563f
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720194"
---
# <a name="web-app-that-calls-web-apis---sign-in"></a>Aplikacja sieci Web, która wywołuje interfejsy API sieci Web — logowanie

Już wiesz, jak dodać logowanie do aplikacji sieci Web. Dowiesz się, że w [aplikacji internetowej, która loguje się do użytkowników, Dodaj logowanie](scenario-web-app-sign-user-sign-in.md).

Co więcej, jest to, że jeśli użytkownik wyloguje się z tej aplikacji lub z dowolnej aplikacji, należy usunąć tokeny skojarzone z tym użytkownikiem.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>Przechwytywanie wywołania zwrotnego po wylogowaniu — Rejestracja jednokrotna

Aplikacja może przechwycić zdarzenie po `logout`, aby na przykład wyczyścić wpis pamięci podręcznej tokenów skojarzonej z kontem, które wylogowano. Aplikacja sieci Web będzie przechowywać tokeny dostępu dla użytkownika w pamięci podręcznej. Przechwycenie wywołania zwrotnego po `logout` umożliwia aplikacji sieci Web usunięcie użytkownika z pamięci podręcznej tokenów.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Ten mechanizm jest zilustrowany w metodzie `AddMsal()` [WebAppServiceCollectionExtensions. cs # L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157)

**Adres URL wylogowania** , który został zarejestrowany dla aplikacji, umożliwia wdrożenie rejestracji jednokrotnej. Punkt końcowy na platformie tożsamości firmy Microsoft `logout` wywoła **adres URL wylogowania** zarejestrowany w aplikacji. To wywołanie występuje, jeśli wylogowanie zostało zainicjowane z aplikacji sieci Web lub z innej aplikacji sieci Web lub przeglądarki. Aby uzyskać więcej informacji, zobacz [Logowanie](v2-protocols-oidc.md#single-sign-out)jednokrotne.

```CSharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: removing the account from MSAL.NET cache
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

Kod dla RemoveAccountAsync jest dostępny w [witrynie Microsoft. Identity. Web/TokenAcquisition. cs # L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Przykład ASP.NET nie usuwa kont z pamięci podręcznej po wylogowaniu globalnym

# <a name="javatabjava"></a>[Java](#tab/java)

Przykład Java nie usuwa kont z pamięci podręcznej na globalnym wylogowaniu

# <a name="pythontabpython"></a>[Python](#tab/python)

Przykład języka Python nie usuwa kont z pamięci podręcznej na globalnym wylogowaniu

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu dla aplikacji sieci Web](scenario-web-app-call-api-acquire-token.md)
