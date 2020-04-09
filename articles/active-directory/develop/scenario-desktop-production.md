---
title: Przenoszenie interfejsów API wywoływania aplikacji komputerowych do środowiska produkcyjnego — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak przenieść aplikację klasyczną, która wywołuje internetowe interfejsy API do środowiska produkcyjnego
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: ea564eb69f102d8e548bf8ae9a626598fa264cd4
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882883"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>Aplikacja klasyczna, która wywołuje internetowe interfejsy API: przejście do środowiska produkcyjnego

W tym artykule dowiesz się, jak przenieść aplikację klasyczną, która wywołuje internetowe interfejsy API do środowiska produkcyjnego.

## <a name="handle-errors-in-desktop-applications"></a>Obsługa błędów w aplikacjach klasycznych

W różnych przepływów, zostały dowiesz się, jak obsługiwać błędy przepływów dyskretnych, jak pokazano w fragmentach kodu. Zaobserwowano również, że istnieją przypadki, w których interakcja jest potrzebna, jak w przyrostowej zgody i dostępu warunkowego.

## <a name="have-the-user-consent-upfront-for-several-resources"></a>Wyrażanie zgody użytkownika z góry na kilka zasobów

> [!NOTE]
> Uzyskiwanie zgody na kilka zasobów działa na platformie tożsamości firmy Microsoft, ale nie dla usługi Azure Active Directory (Azure AD) B2C. Usługa Azure AD B2C obsługuje tylko zgodę administratora, a nie zgodę użytkownika.

Nie można uzyskać tokenu dla kilku zasobów jednocześnie z punktem końcowym platformy tożsamości firmy Microsoft (wersja 2.0). Parametr `scopes` może zawierać zakresy tylko dla jednego zasobu. Można upewnić się, że użytkownik wstępnie wyraża `extraScopesToConsent` zgodę na kilka zasobów przy użyciu parametru.

Na przykład może mieć dwa zasoby, które mają dwa zakresy każdy:

- `https://mytenant.onmicrosoft.com/customerapi`z zakresami `customer.read` i`customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi`z zakresami `vendor.read` i`vendor.write`

W tym przykładzie `.WithAdditionalPromptToConsent` należy użyć `extraScopesToConsent` modyfikatora, który ma parametr.

Przykład:

### <a name="in-msalnet"></a>w MSAL.NET

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

### <a name="in-msal-for-ios-and-macos"></a>W systemie MSAL dla systemu iOS i macOS

Cel C:

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];

NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

Swift:

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

To wywołanie pobiera token dostępu dla pierwszego interfejsu API sieci web.

Jeśli musisz wywołać drugi internetowy interfejs `AcquireTokenSilent` API, zadzwoń do interfejsu API.

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>Konto osobiste Microsoft wymaga ponownej śwegosci przy każdym uruchomieniu aplikacji

W przypadku użytkowników kont osobistych firmy Microsoft zalecane przetwarzanie zgody na każde wywołanie klienta macierzystego (komputera lub aplikacji mobilnej) w celu autoryzacji jest zamierzonym zachowaniem. Tożsamość klienta macierzystego jest z natury niebezpieczna, co jest sprzeczne z tożsamością aplikacji klienta poufnego. Poufne aplikacje klienckie wymieniają klucz tajny z platformą Microsoft Identity, aby udowodnić swoją tożsamość. Platforma tożsamości firmy Microsoft postanowiła ograniczyć tę niepewność dla usług konsumenckich, monitując użytkownika o zgodę za każdym razem, gdy aplikacja jest autoryzowana.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
