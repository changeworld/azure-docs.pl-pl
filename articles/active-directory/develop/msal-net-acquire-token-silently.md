---
title: Uzyskaj token dyskretnie (Biblioteka uwierzytelniania firmy Microsoft dla platformy .NET)
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak uzyskać token dostępu dyskretnie (z pamięci podręcznej tokenów) przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
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
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1dc573c37aab95006f647509080fb9883d8650c7
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802942"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Pobieranie tokenu z pamięci podręcznej tokenów przy użyciu MSAL.NET

Gdy uzyskujesz token dostępu przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET), token jest buforowany. Gdy aplikacja wymaga tokenu, należy najpierw wywołać metodę `AcquireTokenSilent`, aby sprawdzić, czy akceptowalny token znajduje się w pamięci podręcznej. W wielu przypadkach możliwe jest uzyskanie innego tokenu z większą liczbą zakresów na podstawie tokenu w pamięci podręcznej. Istnieje również możliwość odświeżenia tokenu, gdy zbliża się do wygaśnięcia (ponieważ pamięć podręczna tokenów zawiera również token odświeżania).

Zalecany wzorzec polega na wywołaniu metody `AcquireTokenSilent`.  Jeśli `AcquireTokenSilent` nie powiedzie się, uzyskaj token przy użyciu innych metod.

W poniższym przykładzie aplikacja najpierw próbuje uzyskać token z pamięci podręcznej tokenów.  Jeśli zostanie zgłoszony wyjątek `MsalUiRequiredException`, aplikacja uzyskuje token interaktywnie. 

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
 // A MsalUiRequiredException happened on AcquireTokenSilent.
 // This indicates you need to call AcquireTokenInteractive to acquire a token
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
