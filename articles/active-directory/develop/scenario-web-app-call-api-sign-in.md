---
title: Usuwanie kont z pamięci podręcznej tokenu na wylogowaniu — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak usunąć konto z pamięci podręcznej tokeny przy wylogowaniu
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
ms.openlocfilehash: a77bb59afa753fa9d1655e787d4f7a18715ed2ca
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701590"
---
# <a name="remove-accounts-from-the-cache-on-global-sign-out"></a>Usuwanie kont z pamięci podręcznej na globalne wylogowywanie

Już wiesz, jak dodać logowanie do aplikacji sieci Web. Dowiesz się, że w [aplikacji internetowej, która loguje się do użytkowników, Dodaj logowanie](scenario-web-app-sign-user-sign-in.md).

Co więcej, jest to, że jeśli użytkownik wyloguje się z tej aplikacji lub z dowolnej aplikacji, należy usunąć tokeny skojarzone z tym użytkownikiem.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>Przechwytywanie wywołania zwrotnego po wylogowaniu — Rejestracja jednokrotna

Aplikacja może przechwycić zdarzenie po `logout`, aby na przykład wyczyścić wpis pamięci podręcznej tokenów skojarzonej z kontem, które zostało wylogowane. Aplikacja sieci Web będzie przechowywać tokeny dostępu dla użytkownika w pamięci podręcznej. Przechwycenie po `logout` wywołanie zwrotne umożliwia aplikacji sieci Web usunięcie użytkownika z pamięci podręcznej tokenów.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Ten mechanizm jest przedstawiony w `AddMsal()` metodzie [WebAppServiceCollectionExtensions. cs # L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157)

**Adres URL wylogowania** , który został zarejestrowany dla aplikacji, umożliwia wdrożenie rejestracji jednokrotnej. Punkt końcowy `logout` platformy tożsamości firmy Microsoft będzie wywoływał **adres URL wylogowania** zarejestrowany w aplikacji. To wywołanie występuje, jeśli wylogowanie zostało zainicjowane z aplikacji sieci Web lub z innej aplikacji sieci Web lub przeglądarki. Aby uzyskać więcej informacji, zobacz [Logowanie](v2-protocols-oidc.md#single-sign-out)jednokrotne.

```csharp
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

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu dla aplikacji sieci Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu dla aplikacji sieci Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu dla aplikacji sieci Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu dla aplikacji sieci Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
