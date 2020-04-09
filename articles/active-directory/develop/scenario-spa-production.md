---
title: Przenoszenie aplikacji jednostronicowej do produkcji — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację jednostronicową (przejście do produkcji)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 04a900c98311f4e7dcccbfca93ea41e212308759
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882101"
---
# <a name="single-page-application-move-to-production"></a>Aplikacja jednostronicowa: przejście do produkcji

Teraz, gdy wiesz, jak uzyskać token do wywoływania internetowych interfejsów API, dowiedz się, jak przejść do produkcji.

## <a name="improve-your-app"></a>Ulepszanie aplikacji

[Włącz rejestrowanie,](msal-logging.md) aby przygotować produkcję aplikacji.

## <a name="test-your-integration"></a>Przetestuj swoją integrację

Przetestuj integrację, postępając zgodnie z [listą kontrolną integracji platformy tożsamości firmy Microsoft.](identity-platform-integration-checklist.md)

## <a name="next-steps"></a>Następne kroki

Głębokie zagłębienie przykładu przewodnika Szybki start, w którym wyjaśniono kod logowania użytkowników i uzyskiwania tokenu dostępu do wywoływania interfejsu API programu Microsoft Graph przy użyciu pliku MSAL.js:

> [!div class="nextstepaction"]
> [JavaScript SPA samouczek](./tutorial-v2-javascript-spa.md)

Przykład, który pokazuje, jak pobrać tokeny dla własnego interfejsu API sieci web zaplecza przy użyciu msal.js:

> [!div class="nextstepaction"]
> [SPA z ASP.NET back-endem](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

Przykład, który pokazuje, jak używać msal.js do logowania użytkowników w aplikacji, która jest zarejestrowana w usłudze Azure Active Directory B2C (Azure AD B2C):

> [!div class="nextstepaction"]
> [SPA z usługą Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
