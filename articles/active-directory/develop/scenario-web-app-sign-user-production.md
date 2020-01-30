---
title: Przenoszenie aplikacji sieci Web, która loguje użytkowników do produkcji — platforma tożsamości firmy Microsoft | Azure
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
ms.openlocfilehash: 6c486e59f32afd09a9934ae2298172ccb4ee2414
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768102"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Aplikacja internetowa, która loguje użytkowników: Przenieś do środowiska produkcyjnego

Teraz, gdy wiesz już, jak uzyskać token do wywoływania interfejsów API sieci Web, Dowiedz się, jak przenieść go do środowiska produkcyjnego.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Następne kroki

### <a name="same-site"></a>Ta sama witryna

Upewnij się, że rozumiesz potencjalne problemy z nowymi wersjami przeglądarki Chrome

> [!div class="nextstepaction"]
> [Jak obsłużyć zmiany plików cookie SameSite w przeglądarce Chrome](howto-handle-samesite-cookie-changes-chrome-browser.md)

### <a name="scenario-for-calling-web-apis"></a>Scenariusz wywoływania interfejsów API sieci Web

Gdy aplikacja sieci Web loguje się do użytkowników, może wywoływać interfejsy API sieci Web w imieniu zalogowanych użytkowników. Wywoływanie interfejsów API sieci Web z aplikacji sieci Web jest obiektem następującego scenariusza:

> [!div class="nextstepaction"]
> [Aplikacja internetowa wywołująca internetowe interfejsy API](scenario-web-app-call-api-overview.md)

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Głębokie szczegółowe: samouczek aplikacji sieci Web ASP.NET Core

Dowiedz się więcej na temat innych sposobów logowania użytkowników przy użyciu tego samouczka ASP.NET Core: 

> [!div class="nextstepaction"]
> [Zezwól aplikacjom sieci Web na logowanie użytkowników i wywoływanie interfejsów API za pomocą platformy tożsamości firmy Microsoft dla deweloperów](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

Ten samouczek progresywny zawiera kod gotowy do użycia w środowisku produkcyjnym dla aplikacji sieci Web, w tym sposób dodawania logowania przy użyciu kont w programie:

- Twoja organizacja
- Wiele organizacji
- Konta służbowe lub osobiste konta Microsoft
- [Azure AD B2C](https://aka.ms/aadb2c)
- Chmury narodowe

## <a name="sample-code-java-web-app"></a>Przykładowy kod: aplikacja internetowa Java

Dowiedz się więcej o aplikacji sieci Web Java z tego przykładu w witrynie GitHub: 

> [!div class="nextstepaction"]
> [Aplikacja sieci Web w języku Java, która loguje użytkowników z platformą tożsamości firmy Microsoft i wywołuje Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
