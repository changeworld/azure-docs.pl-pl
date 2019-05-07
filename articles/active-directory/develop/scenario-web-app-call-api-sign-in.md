---
title: Aplikacja internetowa wywołuje interfejsy API (logowanie) — Platforma tożsamości usługi Microsoft sieci web
description: Dowiedz się, jak utworzyć aplikację sieci Web, która wywołuje internetowych interfejsów API (Zaloguj się)
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
ms.openlocfilehash: 663cea72eb620217ad5fa8925d3bb00eedbf890c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074563"
---
# <a name="web-app-that-calls-web-apis---sign-in"></a>Aplikacja sieci Web, która wywołuje interfejsy API sieci web — logowanie

Już wiesz, jak dodawać logowania do aplikacji sieci web. Dowiesz się, że w [aplikacji sieci Web, czy użytkownicy objawy — Dodawanie logowania](scenario-web-app-sign-user-sign-in.md).

Czym różni się w tym miejscu, jest po użytkownik zalogował się w poziomie, z tej aplikacji lub z poziomu dowolnej aplikacji usunąć z pamięci podręcznej tokenu, tokeny skojarzonych z użytkownikiem.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>Przechwytuje wywołania zwrotnego po wylogowaniu - pojedynczego Wyloguj

Aplikację można przechwycić po `logout` zdarzenie, na przykład wyczyść wpis pamięci podręcznej tokenu skojarzone z kontem Uruchom jako wylogowany. Zobaczymy w drugiej części tego samouczka (dotyczące funkcji Web app wywoływanie interfejsu API sieci Web), aplikacji sieci web będą przechowywane tokeny dostępu dla użytkownika w pamięci podręcznej. Przechwytuje po `logout` wywołanie zwrotne umożliwia aplikacji sieci web usunąć użytkownika z pamięci podręcznej tokenu. Ten mechanizm zilustrowano `AddMsal()` metody [143 StartupHelper.cs L137](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L137-L143)

**Adres Url wylogowania** zarejestrowaniu dla aplikacji umożliwia implementowania logowania jednokrotnego w poziomie. Platforma Microsoft identity `logout` punkt końcowy będzie wywoływać **adres URL wylogowania** zarejestrowane z aplikacją. To wywołanie się dzieje w przypadku wylogowywania zainicjowano z aplikacji sieci web lub innej aplikacji sieci web lub w przeglądarce. Aby uzyskać więcej informacji, zobacz [wylogowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc#single-sign-out) w dokumentacji koncepcyjnego.

```CSharp
public static IServiceCollection AddMsal(this IServiceCollection services, IEnumerable<string> initialScopes)
{
    services.AddTokenAcquisition();

    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
     ...
        // Handling the sign-out: removing the account from MSAL.NET cache
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            // Remove the account from MSAL.NET token cache
            var _tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
            await _tokenAcquisition.RemoveAccount(context);
        };
    });
    return services;
}
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu dla aplikacji sieci web](scenario-web-app-call-api-acquire-token.md)
