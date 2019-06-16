---
title: Wyczyść pamięć podręczną tokenu przy użyciu Biblioteka Microsoft Authentication Library for .NET — Azure
description: Dowiedz się, jak wyczyścić pamięć podręczną tokenu przy użyciu Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6763c6b2b1f9b4de7d8669a50a4979a7aac00c7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544121"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Wyczyść pamięć podręczną tokenu przy użyciu platformy MSAL.NET

Gdy użytkownik [uzyskiwanie tokenu dostępu](msal-acquire-cache-tokens.md) przy użyciu Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET), token jest buforowany. Gdy aplikacja wymaga tokenu, najpierw należy wywołać `AcquireTokenSilent` metodę, aby sprawdzić, czy token dopuszczalne w pamięci podręcznej. 

Wyczyszczenie pamięci podręcznej jest osiągane poprzez usunięcie konta z pamięci podręcznej. Plik cookie sesji, który znajduje się w przeglądarce, jednak nie powoduje usunięcia.  Poniższy przykład tworzy wystąpienie aplikacji publicznych klienta, pobiera kont dla aplikacji i usuwa konta.

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

Aby dowiedzieć się więcej na temat uzyskiwania i buforowanie tokenów, przeczytaj [uzyskiwanie tokenu dostępu](msal-acquire-cache-tokens.md).
