---
title: Uzyskać token w trybie dyskretnym (Biblioteka Microsoft Authentication Library dla platformy .NET) | Azure
description: Dowiedz się, jak można uzyskać tokenu dostępu w trybie dyskretnym (z pamięci podręcznej tokenu) przy użyciu Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET).
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
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6331407067a39550d866d7c293a92fac9184b54e
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544239"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Pobrania tokenu z pamięci podręcznej tokenu przy użyciu platformy MSAL.NET

Po uzyskaniu tokenu dostępu przy użyciu Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET), jest buforowany token. Gdy aplikacja wymaga tokenu, najpierw należy wywołać `AcquireTokenSilent` metodę, aby sprawdzić, czy token dopuszczalne w pamięci podręcznej. W wielu przypadkach jest to możliwe uzyskać inny token za pomocą więcej zakresów, na podstawie tokenów w pamięci podręcznej. Istnieje również możliwość odświeżenia tokenu, gdy będzie niedługo wygaśnie (zgodnie z pamięci podręcznej tokenu zawiera także tokenu odświeżania).

Zalecany wzorzec polega na wywołaniu `AcquireTokenSilent` metoda pierwszy.  Jeśli `AcquireTokenSilent` zakończy się niepowodzeniem, następnie uzyskać token przy użyciu innych metod.

W poniższym przykładzie najpierw próbuje uzyskać tokenu z pamięci podręcznej tokenu.  Jeśli `MsalUiRequiredException` jest zgłaszany wyjątek, aplikacja uzyskuje token interaktywnie. 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilentAsync.
 // This indicates you need to call AcquireTokenAsync to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```