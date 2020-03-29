---
title: Wyczyść pamięć podręczną tokenów (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak wyczyścić pamięć podręczną tokenów przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: a10efb5ff0a2c6a3ced3631dfe82c86e3e8a72fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084764"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Wyczyść pamięć podręczną tokenów przy użyciu MSAL.NET

Po [nabyciu tokenu dostępu](msal-acquire-cache-tokens.md) przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET) token jest buforowany. Gdy aplikacja potrzebuje tokenu, należy `AcquireTokenSilent` najpierw wywołać metodę, aby sprawdzić, czy dopuszczalne token jest w pamięci podręcznej. 

Wyczyszczenie pamięci podręcznej odbywa się przez usunięcie kont z pamięci podręcznej. Nie powoduje to jednak usunięcia pliku cookie sesji, który znajduje się w przeglądarce.  Poniższy przykład powoduje wystąpienia publicznej aplikacji klienckiej, pobiera konta dla aplikacji i usuwa konta.

```csharp
private readonly IPublicClientApplication _app;
private static readonly string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
private static readonly string Authority = string.Format(CultureInfo.InvariantCulture, AadInstance, Tenant);

_app = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .Build();

var accounts = (await _app.GetAccountsAsync()).ToList();

// clear the cache
while (accounts.Any())
{
   await _app.RemoveAsync(accounts.First());
   accounts = (await _app.GetAccountsAsync()).ToList();
}

```

Aby dowiedzieć się więcej o pozyskiwaniu i buforowaniu tokenów, przeczytaj [o nabyciu tokenu dostępu.](msal-acquire-cache-tokens.md)
