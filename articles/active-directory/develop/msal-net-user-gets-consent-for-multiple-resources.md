---
title: Uzyskaj zgodę na kilka zasobów (Biblioteka uwierzytelniania firmy Microsoft dla platformy .NET)
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak użytkownik może uzyskać wstępną zgodę na kilka zasobów przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
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
ms.date: 04/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3a3d50e94f76341dc349eda997d6e25ca96dec0
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802693"
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
