---
title: Wyczyść pamięć podręczną tokenów przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak wyczyścić pamięć podręczną tokenów przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bd48078ec87b085a71ee23006d18917881894bf
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802915"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Wyczyść pamięć podręczną tokenów przy użyciu MSAL.NET

Gdy [uzyskujesz token dostępu](msal-acquire-cache-tokens.md) przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .net (MSAL.NET), token jest buforowany. Gdy aplikacja wymaga tokenu, należy najpierw wywołać metodę `AcquireTokenSilent`, aby sprawdzić, czy akceptowalny token znajduje się w pamięci podręcznej. 

Czyszczenie pamięci podręcznej jest realizowane przez usunięcie kont z pamięci podręcznej. Nie spowoduje to usunięcia pliku cookie sesji, który znajduje się w przeglądarce.  Poniższy przykład tworzy wystąpienie publicznej aplikacji klienckiej, pobiera konta aplikacji i usuwa konta.

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

Aby dowiedzieć się więcej na temat uzyskiwania i buforowania tokenów, przeczytaj artykuł [pozyskiwanie tokenu dostępu](msal-acquire-cache-tokens.md).
