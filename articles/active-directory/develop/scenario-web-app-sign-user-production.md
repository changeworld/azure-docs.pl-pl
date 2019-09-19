---
title: Aplikacja internetowa, która umożliwia użytkownikom logowanie się (przejście do produkcji) — platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację internetową, która loguje użytkowników (Przenieś do środowiska produkcyjnego)
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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd6717c132d32d54c16e7678695a09e35181a057
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086544"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>Aplikacja internetowa, która loguje użytkowników — przenosi do produkcji

Teraz, gdy wiesz już, jak uzyskać token do wywoływania interfejsów API sieci Web, Dowiedz się, jak przenieść go do środowiska produkcyjnego.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Następne kroki

### <a name="calling-web-apis-scenario"></a>Scenariusz wywoływania interfejsów API sieci Web

Gdy aplikacja sieci Web loguje się, może wywoływać interfejsy API sieci Web w imieniu zalogowanych użytkowników. Wywoływanie interfejsów API sieci Web z aplikacji sieci Web jest obiektem następującego scenariusza:

> [!div class="nextstepaction"]
> [Aplikacja internetowa wywołująca internetowe interfejsy API](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---aspnet-core-web-app-tutorial"></a>Głębokie szczegółowe — samouczek aplikacji sieci Web ASP.NET Core

Dowiedz się więcej na temat innych sposobów logowania użytkowników przy użyciu samouczka ASP.NET Core: [MS-Identity-aspnetcore-webapp-samouczek](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial). Ten przykład to progresywny samouczek z kodem gotowym do produkcji dla aplikacji sieci Web, w tym jak dodać Logowanie przy użyciu kont w programie:

- Twoja organizacja,
- wiele organizacji,
- konta służbowe lub osobiste konto Microsoft,
- za pomocą [Azure AD B2C](https://aka.ms/aadb2c),
- lub w chmurach krajowych.

### <a name="sample-code---java-web-app"></a>Przykładowy kod — aplikacja internetowa Java

Dowiedz się więcej o aplikacji sieci Web Java z przykładu w witrynie GitHub: [Aplikacja sieci Web w języku Java, która loguje użytkowników z platformą tożsamości firmy Microsoft i wywołuje Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
