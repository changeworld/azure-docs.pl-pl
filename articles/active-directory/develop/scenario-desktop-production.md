---
title: Aplikacja klasyczna, że wywołuje interfejsy API sieci web (przenoszenie do środowiska produkcyjnego) — Platforma tożsamości usługi Microsoft
description: Naucz się tworzyć aplikację klasyczną wywołuje interfejsy API (przenoszenie do środowiska produkcyjnego) sieci web
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
ms.date: 04/18/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2343a416bd810792e7267b94395f953aa4f880a1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111199"
---
# <a name="desktop-app-that-calls-web-apis---move-to-production"></a>Aplikacja klasyczna, która wywołuje sieci web interfejsy API — przenoszenie do środowiska produkcyjnego

Ten artykuł zawiera szczegóły, aby ulepszać aplikację dalej i przenieś ją do środowiska produkcyjnego.

## <a name="handling-errors-in-desktop-applications"></a>Obsługa błędów w aplikacjach klasycznych

W różnych przepływów wyjaśniono sposób obsługi błędów dla przepływów dyskretnej (jak pokazano w fragmenty kodu). Również w tym samouczku, istnieją przypadki, w którym interakcja jest wymagane (przyrostowe wyrażania zgody i dostępu warunkowego).

## <a name="how-to-have--the-user-consent-upfront-for-several-resources"></a>Jak zgody użytkownika z wyprzedzeniem dla kilku zasobów

> [!NOTE]
> Aby uzyskać zgodę kilka działa zasobów dla platformy tożsamości firmy Microsoft, ale nie dla usługi Azure Active Directory (Azure AD) B2C. Usługa Azure AD B2C obsługuje tylko zgody administratora, nie zgody użytkownika.

Punkt końcowy platformy (w wersji 2.0) tożsamości firmy Microsoft nie zezwala na uzyskać token dla kilku zasobów jednocześnie. W związku z tym `scopes` parametru może zawierać tylko zakresy do jednego zasobu. Należy zapewnić, że użytkownik wstępnie wyraża zgodę na kilka zasobów za pomocą `extraScopesToConsent` parametru.

Na przykład jeśli masz dwa zasoby, które mają dwa zakresy każdego:

- `https://mytenant.onmicrosoft.com/customerapi` -z 2-zakresami `customer.read` i `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi` -z 2-zakresami `vendor.read` i `vendor.write`

Należy używać `.WithAdditionalPromptToConsent` modyfikator, który ma `extraScopesToConsent` parametru.

Na wystąpienie:

```CSharp
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

To wywołanie Uzyskaj token dostępu dla pierwszego interfejsu API sieci web.

Gdy potrzebne do wywoływania drugiego interfejsu API sieci web, można wywołać:

```CSharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsenting-each-time-the-app-is-run"></a>Konta osobiste Microsoft wymaga reconsenting każdorazowo, gdy aplikacja jest uruchomiona

Dla użytkowników konta osobiste Microsoft reprompting o zgodę na każde wywołanie klienta natywnego (desktop/mobile app), do autoryzacji jest to oczekiwane zachowanie. Natywny klient tożsamości jest z założenia niezabezpieczone (sprzecznie poufne klienta aplikacji, która wpisu tajnego z platformą Microsoft Identity w celu potwierdzenia tożsamości). Platforma Microsoft identity wybrała zmniejszyć to braku bezpieczeństwa dla konsumentów, wykonując monitowania użytkownika o zgodę, każdym razem, gdy aplikacja jest autoryzowany.

## <a name="next-steps"></a>Kolejne kroki

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
