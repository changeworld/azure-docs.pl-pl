---
title: Przenoszenie aplikacji sieci Web, która loguje się do użytkowników do produkcji - Platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację internetową, która loguje się do użytkowników (przejście do produkcji)
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768102"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Aplikacja sieci Web, która loguje się do użytkowników: Przejście do produkcji

Teraz, gdy wiesz, jak uzyskać token do wywoływania internetowych interfejsów API, dowiedz się, jak przenieść go do produkcji.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Następne kroki

### <a name="same-site"></a>Ta sama strona

Upewnij się, że rozumiesz możliwe problemy z nowymi wersjami przeglądarki Chrome

> [!div class="nextstepaction"]
> [Jak radzić sobie ze zmianami plików cookie SameSite w przeglądarce Chrome](howto-handle-samesite-cookie-changes-chrome-browser.md)

### <a name="scenario-for-calling-web-apis"></a>Scenariusz wywoływania interfejsów API sieci Web

Po logowaniu się aplikacji sieci web w użytkownikach może wywoływać interfejsy API sieci Web w imieniu zalogowanych użytkowników. Wywoływanie interfejsów API sieci web z aplikacji sieci web jest obiektem następującego scenariusza:

> [!div class="nextstepaction"]
> [Aplikacja internetowa wywołująca internetowe interfejsy API](scenario-web-app-call-api-overview.md)

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Głębokie nurkowanie: ASP.NET samouczek aplikacji sieci Web Core

Dowiedz się więcej o innych sposobach logowania użytkowników za pomocą tego samouczka ASP.NET Core: 

> [!div class="nextstepaction"]
> [Włączanie aplikacji sieci Web do logowania użytkowników i wywoływania interfejsów API za pomocą platformy tożsamości firmy Microsoft dla deweloperów](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

Ten samouczek progresywny zawiera kod gotowy do produkcji dla aplikacji sieci web, w tym sposób dodawania logowania za pomocą kont w:

- Twoja organizacja
- Wiele organizacji
- Konta służbowe lub konta firmowe lub osobiste konta Microsoft
- [Funkcje B2C platformy Azure](https://aka.ms/aadb2c)
- Chmury narodowe

## <a name="sample-code-java-web-app"></a>Przykładowy kod: aplikacja internetowa Java

Dowiedz się więcej o aplikacji internetowej Java z tego przykładu w usłudze GitHub: 

> [!div class="nextstepaction"]
> [Aplikacja java web, która loguje się do użytkowników za pomocą platformy tożsamości firmy Microsoft i wywołuje program Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
