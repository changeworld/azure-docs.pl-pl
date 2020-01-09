---
title: Przenoszenie aplikacji klasycznych interfejsy API sieci Web do produkcji — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak przenieść aplikację klasyczną, która wywołuje interfejsy API sieci Web w środowisku produkcyjnym
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe727afcfdec204c92c82c3e695961707af90e65
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423809"
---
# <a name="desktop-app-that-calls-web-apis---move-to-production"></a>Aplikacja klasyczna, która wywołuje interfejsy API sieci Web — Przenieś do środowiska produkcyjnego

Ten artykuł zawiera szczegółowe informacje dotyczące przenoszenia aplikacji klasycznej, która wywołuje interfejsy API sieci Web w środowisku produkcyjnym.

## <a name="handling-errors-in-desktop-applications"></a>Obsługa błędów w aplikacjach klasycznych

W różnych przepływach wiesz, jak obsłużyć błędy dla przepływów dyskretnych (jak pokazano w fragmentach kodu). Pojawiły się także sytuacje, w których jest wymagana interakcja (przyrostowa zgoda i dostęp warunkowy).

## <a name="how-to-have--the-user-consent-upfront-for-several-resources"></a>Jak uzyskać zgodę użytkownika na dostęp do kilku zasobów

> [!NOTE]
> Uzyskanie zgody na kilka zasobów działa w przypadku platformy tożsamości firmy Microsoft, ale nie dla Azure Active Directory (Azure AD) B2C. Azure AD B2C obsługuje tylko zgodę z uprawnieniami administratora, a nie za zgodą użytkownika.

Punkt końcowy Microsoft Identity platform (v 2.0) nie zezwala na uzyskanie tokenu dla kilku zasobów jednocześnie. W związku z tym, parametr `scopes` może zawierać tylko zakresy dla pojedynczego zasobu. Można upewnić się, że użytkownik wstępnie wysłał do kilku zasobów przy użyciu `extraScopesToConsent` parametru.

Na przykład jeśli masz dwa zasoby, które mają dwa zakresy:

- `https://mytenant.onmicrosoft.com/customerapi` — z 2 zakresami `customer.read` i `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi` — z 2 zakresami `vendor.read` i `vendor.write`

Należy użyć modyfikatora `.WithAdditionalPromptToConsent`, który ma parametr `extraScopesToConsent`.

Na wystąpienie:

### <a name="in-msalnet"></a>W MSAL.NET

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

### <a name="in-msal-for-ios-and-macos"></a>W MSAL dla systemów iOS i macOS

Cel-C:

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];

NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

Adres

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

To wywołanie spowoduje uzyskanie tokenu dostępu dla pierwszego internetowego interfejsu API.

Gdy musisz wywołać drugi internetowy interfejs API, możesz wywołać `AcquireTokenSilent` API:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsenting-each-time-the-app-is-run"></a>Konto osobiste firmy Microsoft wymaga zgody przy każdym uruchomieniu aplikacji

W przypadku użytkowników z kontami osobistymi firmy Microsoft monit o zgodę na każde wywołanie natywnego klienta (aplikacji klasycznej/mobilnej) do autoryzacji jest zamierzonym zachowaniem. Natywna tożsamość klienta jest z natury niezabezpieczona (w przeciwieństwie do poufnej aplikacji klienckiej, która zamieni wpis tajny na platformę tożsamości firmy Microsoft w celu potwierdzenia ich tożsamości). Platforma tożsamości firmy Microsoft zdecydowała się wyeliminować to niebezpieczeństwo dla usług konsumenckich, monitując użytkownika o zgodę, za każdym razem, gdy aplikacja jest autoryzowana.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
