---
title: Uzyskaj token dyskretnie (Biblioteka uwierzytelniania firmy Microsoft dla platformy .NET) | Azure
description: Dowiedz się, jak uzyskać token dostępu dyskretnie (z pamięci podręcznej tokenów) przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
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
ms.date: 07/16/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 309e912f2adf5249770b40a631ed62f7cb3113e5
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277943"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Pobieranie tokenu z pamięci podręcznej tokenów przy użyciu MSAL.NET

Gdy uzyskujesz token dostępu przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET), token jest buforowany. Gdy aplikacja wymaga tokenu, powinna najpierw wywołać `AcquireTokenSilent` metodę, aby sprawdzić, czy akceptowalny token znajduje się w pamięci podręcznej. W wielu przypadkach możliwe jest uzyskanie innego tokenu z większą liczbą zakresów na podstawie tokenu w pamięci podręcznej. Istnieje również możliwość odświeżenia tokenu, gdy zbliża się do wygaśnięcia (ponieważ pamięć podręczna tokenów zawiera również token odświeżania).

Zalecany wzorzec polega na wywołaniu metody jako `AcquireTokenSilent` pierwszej.  Jeśli `AcquireTokenSilent` to się nie powiedzie, uzyskaj token przy użyciu innych metod.

W poniższym przykładzie aplikacja najpierw próbuje uzyskać token z pamięci podręcznej tokenów.  `MsalUiRequiredException` Jeśli wystąpi wyjątek, aplikacja uzyskuje token interaktywnie. 

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
