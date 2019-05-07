---
title: Aplikacja jednostronicowa (przenoszenie do środowiska produkcyjnego) — Platforma tożsamości firmy Microsoft
description: Informacje o sposobie tworzenia aplikacji jednostronicowych (przenoszenie do środowiska produkcyjnego)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6a04f5d62ec750cfbe44765e833552bd694654d
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075013"
---
# <a name="single-page-application---move-to-production"></a>Aplikacja jednostronicowa — przenoszenie do środowiska produkcyjnego

Teraz gdy wiesz, jak można uzyskać tokenu służącego do wywoływania interfejsów API sieci Web, Dowiedz się, jak przenieść do środowiska produkcyjnego.

## <a name="improve-your-app"></a>Ulepszenie aplikacji

Wykonaj kroki wymagane do przygotowania aplikacji w środowisku produkcyjnym.

- [Włącz rejestrowanie](msal-logging.md) w aplikacji.

## <a name="test-your-integration"></a>Testowanie integracji usługi

- Testowanie integracji usługi wykonując [Microsoft tożsamości platformy integracji z listy kontrolnej](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Kolejne kroki

Poniżej przedstawiono kilka przykładów/samouczków:

- Aby uzyskać szczegółowe informacje z tego przewodnika Szybki Start przykładowa, co wyjaśnia kod jak logować użytkowników i uzyskania tokenu dostępu do wywołania interfejsu API programu Graph MS przy użyciu MSAL.js

    > [!div class="nextstepaction"]
    > [Samouczek JavaScript SPA](./tutorial-v2-javascript-spa.md)

- Przykład przedstawiający sposób uzyskiwania tokenów dla własnego zaplecza internetowego interfejsu API przy użyciu MSAL.js

     > [!div class="nextstepaction"]
     > [SPA z zaplecza programu ASP.NET](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

- Przykład pokazują, jak używać MSAL.js do logowania użytkowników w aplikacji zarejestrowanych w usłudze Azure AD B2C

    > [!div class="nextstepaction"]
    > [SPA za pomocą usługi Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
