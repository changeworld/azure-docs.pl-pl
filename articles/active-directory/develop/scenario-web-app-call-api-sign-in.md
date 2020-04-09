---
title: Usuwanie kont z pamięci podręcznej tokenów przy wylogowywania się — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak usunąć konto z pamięci podręcznej tokenu podczas wylogowywania się
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 934b756329065c466f21fca1480247065bdea28b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881616"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Aplikacja internetowa, która wywołuje internetowe interfejsy API: usuwanie kont z pamięci podręcznej tokenu podczas globalnego wylogowania

Dowiedziasz się, jak dodać logowanie do aplikacji sieci Web w [aplikacji sieci Web, która loguje się do użytkowników: Logowanie i wylogowywanie](scenario-web-app-sign-user-sign-in.md)się .

Wyloguj się jest inny dla aplikacji internetowej, która wywołuje web apis. Gdy użytkownik wyloguje się z aplikacji lub z dowolnej aplikacji, należy usunąć tokeny skojarzone z tym użytkownikiem z pamięci podręcznej tokenu.

## <a name="intercept-the-callback-after-single-sign-out"></a>Przechwytywanie wywołania zwrotnego po wylogowywania się po wylogowywaniem się

Aby wyczyścić wpis pamięci podręcznej tokenu skojarzony z kontem, które się wylogował, aplikacja może przechwycić zdarzenie po. `logout` Tokeny dostępu do sklepu Web Apps store dla każdego użytkownika w pamięci podręcznej tokenu. Przechwytując po `logout` wywołaniu zwrotnym, aplikacja sieci web można usunąć użytkownika z pamięci podręcznej.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Dla ASP.NET Core mechanizm przechwytywania jest zilustrowany `AddMsal()` metodą [WebAppServiceCollectionExtensions.cs#L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157).

Adres URL wylogowania, który został wcześniej zarejestrowany dla aplikacji umożliwia zaimplementowanie pojedynczego wylogowania. Punkt końcowy `logout` platformy tożsamości firmy Microsoft wywołuje adres URL wylogowania. To wywołanie ma miejsce, jeśli wylogowanie rozpoczęło się z aplikacji sieci web lub z innej aplikacji internetowej lub przeglądarki. Aby uzyskać więcej informacji, zobacz [Wylogowanie jednokrotne](v2-protocols-oidc.md#single-sign-out).

```csharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: Remove the account from MSAL.NET cache.
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache.
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

Kod jest `RemoveAccountAsync` dostępny w witrynie [Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Przykład ASP.NET nie usuwa kont z pamięci podręcznej podczas globalnego wylogowania.

# <a name="java"></a>[Java](#tab/java)

Przykład języka Java nie usuwa kont z pamięci podręcznej podczas globalnego wylogowania.

# <a name="python"></a>[Python](#tab/python)

Przykład języka Python nie usuwa kont z pamięci podręcznej podczas globalnego wylogowania.

---

## <a name="next-steps"></a>Następne kroki

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu dla aplikacji sieci web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu dla aplikacji sieci web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu dla aplikacji sieci web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu dla aplikacji sieci web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
