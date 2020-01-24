---
title: Przenoszenie aplikacji jednostronicowej do środowiska produkcyjnego — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację jednostronicową (Przenieś do środowiska produkcyjnego)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 01b923e0d013fab1815456e55eac6036ded772bb
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701845"
---
# <a name="single-page-application-move-to-production"></a>Aplikacja jednostronicowa: Przenieś do środowiska produkcyjnego

Teraz, gdy wiesz już, jak uzyskać token do wywoływania interfejsów API sieci Web, Dowiedz się, jak przejść do środowiska produkcyjnego.

## <a name="improve-your-app"></a>Ulepszanie aplikacji

[Włącz rejestrowanie](msal-logging.md) , aby przygotować aplikację do produkcji.

## <a name="test-your-integration"></a>Testowanie integracji

Przetestuj integrację, postępując zgodnie z [listą kontrolną integracji z platformą Microsoft Identity](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Następne kroki

Głębokie szczegółowe przykładu szybkiego startu, w którym wyjaśniono kod sposobu logowania użytkowników i uzyskiwania tokenu dostępu do wywoływania interfejsu API Microsoft Graph przy użyciu MSAL. js:

> [!div class="nextstepaction"]
> [Samouczek dotyczący SPA JavaScript](./tutorial-v2-javascript-spa.md)

Przykład demonstrujący, jak uzyskać tokeny dla własnego interfejsu API sieci Web zaplecza przy użyciu MSAL. js:

> [!div class="nextstepaction"]
> [SPA z zapleczem ASP.NET](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

Przykład, który pokazuje, jak używać MSAL. js do logowania użytkowników w aplikacji, która jest zarejestrowana w Azure Active Directory B2C (Azure AD B2C):

> [!div class="nextstepaction"]
> [SPA z Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
