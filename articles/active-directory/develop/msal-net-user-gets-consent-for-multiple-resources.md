---
title: Uzyskaj zgodę dla kilku zasobów (Biblioteka Microsoft Authentication Library dla platformy .NET) | Azure
description: Dowiedz się, jak użytkownik może uzyskać zgodę przed dla kilku zasobów przy użyciu Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET).
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
ms.openlocfilehash: e8bd9a86d5ec0d39a7f1c26adac52f41e6420283
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121986"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>Użytkownik otrzyma wyrażania zgody dla kilku zasobów przy użyciu platformy MSAL.NET
Microsoft platformy tożsamości z punktu końcowego nie pozwala uzyskać token dla kilku zasobów jednocześnie. Korzystając z Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET), parametr zakresów w przypadku metody tokenu nabywania powinien zawierać tylko zakresy do jednego zasobu. Jednak można wstępnie zgadzasz się na kilka zasobów z wyprzedzeniem, określając dodatkowe zakresy, za pomocą `.WithExtraScopeToConsent` metody konstruktora.

> [!NOTE]
> Aby uzyskać zgodę kilka działa zasobów dla platformy tożsamości firmy Microsoft, ale nie dla usługi Azure AD B2C. Usługa Azure AD B2C obsługuje tylko zgody administratora, nie zgody użytkownika.

Na przykład jeśli masz dwa zasoby, które mają 2 zakresów każdy:

- protokół https:\//mytenant.onmicrosoft.com/customerapi (z 2-zakresami `customer.read` i `customer.write`)
- protokół https:\//mytenant.onmicrosoft.com/vendorapi (z 2-zakresami `vendor.read` i `vendor.write`)

Należy używać `.WithExtraScopeToConsent` modyfikator, która ma *extraScopesToConsent* parametru, jak pokazano w poniższym przykładzie:

```csharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.onmicrosoft.com/customerapi/customer.read",
  "https://mytenant.onmicrosoft.com/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

Instalacja obejmuje programy tokenu dostępu dla pierwszego interfejsu API sieci web. Następnie Jeśli potrzebujesz dostępu do drugiego interfejsu API sieci web dyskretnie pobieranie tokenu z pamięci podręcznej tokenu:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
