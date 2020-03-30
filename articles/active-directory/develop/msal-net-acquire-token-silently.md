---
title: Uzyskaj token z pamięci podręcznej (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak po cichu uzyskać token dostępu (z pamięci podręcznej tokenów) przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 90189a1d7fd6421b7a24940e8c6ed615fa0df6d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084839"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Pobierz token z pamięci podręcznej tokenu przy użyciu MSAL.NET

Po nabyciu tokenu dostępu przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET) token jest buforowany. Gdy aplikacja potrzebuje tokenu, należy `AcquireTokenSilent` najpierw wywołać metodę, aby sprawdzić, czy dopuszczalne token jest w pamięci podręcznej. W wielu przypadkach jest możliwe uzyskanie innego tokenu z więcej zakresów na podstawie tokenu w pamięci podręcznej. Istnieje również możliwość odświeżenia tokenu, gdy zbliża się do wygaśnięcia (ponieważ pamięć podręczna tokenu zawiera również token odświeżania).

Zalecany wzorzec `AcquireTokenSilent` jest najpierw wywołać metodę.  Jeśli `AcquireTokenSilent` nie powiedzie się, a następnie uzyskać token przy użyciu innych metod.

W poniższym przykładzie aplikacja najpierw próbuje uzyskać token z pamięci podręcznej tokenu.  Jeśli `MsalUiRequiredException` wyjątek, aplikacja uzyskuje token interaktywnie. 

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
