---
title: Uzyskaj zgodę na kilka zasobów (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak użytkownik może uzyskać wstępną zgodę na kilka zasobów przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 94c9a2b6a46262ad293da9ca3ba493d6f898c870
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085842"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>Użytkownik otrzymuje zgodę na kilka zasobów przy użyciu MSAL.NET
Punkt końcowy platformy tożsamości firmy Microsoft nie pozwala na uzyskanie tokenu dla kilku zasobów naraz. W przypadku korzystania z biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET) parametr zakresów w metodzie tokenu nabycia powinien zawierać tylko zakresy dla pojedynczego zasobu. Można jednak wstępnie wyrazić zgodę na kilka zasobów z `.WithExtraScopeToConsent` góry, określając dodatkowe zakresy przy użyciu metody konstruktora.

> [!NOTE]
> Uzyskiwanie zgody na kilka zasobów działa dla platformy tożsamości firmy Microsoft, ale nie dla usługi Azure AD B2C. Usługa Azure AD B2C obsługuje tylko zgodę administratora, a nie zgodę użytkownika.

Na przykład jeśli masz dwa zasoby, które mają po 2 zakresy:

- https:\//mytenant.onmicrosoft.com/customerapi (z 2 `customer.read` zakresami i `customer.write`)
- https:\//mytenant.onmicrosoft.com/vendorapi (z 2 `vendor.read` zakresami i `vendor.write`)

Należy użyć `.WithExtraScopeToConsent` modyfikatora, który ma *parametr extraScopesToConsent,* jak pokazano w poniższym przykładzie:

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

Spowoduje to uzyskanie tokenu dostępu dla pierwszego interfejsu API sieci web. Następnie, gdy trzeba uzyskać dostęp do drugiego interfejsu API sieci web można dyskretnie uzyskać token z pamięci podręcznej tokenu:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
