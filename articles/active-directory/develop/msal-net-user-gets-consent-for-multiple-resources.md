---
title: Uzyskaj zgodę na kilka zasobów (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak użytkownik może uzyskać wstępną zgodę na kilka zasobów przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 3bd23a1f14d5e3cbf9fc41ade47571c6689f3468
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695028"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>Użytkownik otrzymuje zgodę na kilka zasobów przy użyciu MSAL.NET
Punkt końcowy platformy tożsamości firmy Microsoft nie pozwala na uzyskanie tokenu dla kilku zasobów jednocześnie. W przypadku korzystania z biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET) parametr zakresy w metodzie pozyskiwania tokenów powinien zawierać tylko zakresy dla pojedynczego zasobu. Można jednak wstępnie wyrazić zgodę na kilka zasobów, określając dodatkowe zakresy przy użyciu metody `.WithExtraScopeToConsent` Builder.

> [!NOTE]
> Uzyskanie zgody na kilka zasobów działa w przypadku platformy tożsamości firmy Microsoft, ale nie dla Azure AD B2C. Azure AD B2C obsługuje tylko zgodę z uprawnieniami administratora, a nie za zgodą użytkownika.

Na przykład jeśli masz dwa zasoby, które mają 2 zakresy każdy:

- https:\//mytenant.onmicrosoft.com/customerapi (z 2 zakresami `customer.read` i `customer.write`)
- https:\//mytenant.onmicrosoft.com/vendorapi (z 2 zakresami `vendor.read` i `vendor.write`)

Należy użyć modyfikatora `.WithExtraScopeToConsent`, który ma parametr *extraScopesToConsent* , jak pokazano w następującym przykładzie:

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

Spowoduje to uzyskanie tokenu dostępu dla pierwszego internetowego interfejsu API. Następnie, gdy musisz uzyskać dostęp do drugiego internetowego interfejsu API, możesz w trybie dyskretnym uzyskać token z pamięci podręcznej tokenów:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
