---
title: Aplikacja klasyczna wywołuje interfejsy API, (wywoływanie interfejsu web API) — sieci web platforma tożsamości firmy Microsoft
description: Naucz się tworzyć aplikację pulpitu, która wywołuje internetowych interfejsów API (wywoływanie interfejsu web API)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fb240b54c3d698ead9d3427f603acca2b53745a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075928"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>Aplikacja klasyczna, która wywołuje interfejsy API — w sieci web wywołania interfejsu API sieci web

Teraz, gdy masz token, można wywoływać chroniony internetowy interfejs API.

## <a name="calling-a-web-api-from-net"></a>Wywoływanie interfejsu web API z platformy .NET

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-several-apis---incremental-consent-and-conditional-access"></a>Wywoływanie kilka interfejsów API — przyrostowe wyrażania zgody i dostępu warunkowego

Jeśli zachodzi potrzeba wywołania kilka interfejsów API dla tego samego użytkownika, gdy masz token dla pierwszego interfejsu API, możesz po prostu wywołać `AcquireTokenSilent`, a uzyskasz tokenu dla innych interfejsów API dyskretnie większość czasu.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Przypadki, w których wymagane jest interakcja jest, gdy:

- Użytkownik wyraził zgodę dla pierwszego interfejsu API, ale teraz trzeba zgoda na więcej zakresów (zgody przyrostowych)
- Pierwszego interfejsu API nie wymagają uwierzytelniania wieloskładnikowego wiele, ale nie kolejny.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przenoszenie do środowiska produkcyjnego](scenario-desktop-production.md)
