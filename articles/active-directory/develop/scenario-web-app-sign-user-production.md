---
title: Aplikacja sieci Web przez logowania użytkowników (przenoszenie do środowiska produkcyjnego) — Platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację sieci web, logujący się użytkownicy (przenoszenie do środowiska produkcyjnego)
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
ms.openlocfilehash: d41ad2518f885bbaa02dda3b01f0c02e9fc1d217
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074713"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>Aplikacja sieci Web, który loguje się użytkownicy — przenoszenie do środowiska produkcyjnego

Teraz gdy wiesz, jak można uzyskać tokenu służącego do wywoływania interfejsów API sieci web, Dowiedz się, jak przenieść je do środowiska produkcyjnego.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Kolejne kroki

### <a name="calling-web-apis-scenario"></a>Scenariusz interfejsów API sieci web wywołania

Jeden raz w sieci web logowania użytkownikom aplikacji, może wywołać interfejsy API sieci web w imieniu zalogowanych użytkowników. Wywołania interfejsów API sieci web z aplikacji sieci web jest obiektem następujący scenariusz:

> [!div class="nextstepaction"]
> [Aplikacja internetowa wywołuje interfejsy API sieci web](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---web-app-tutorial"></a>Szczegółowe informacje — samouczek aplikacji sieci web

Dowiedz się więcej o inne sposoby logowania użytkowników, samouczek platformy ASP.NET Core: [ms-identity-aspnetcore-aplikacji sieci Web tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial). To jest stopniowe samouczek kodem gotowe produkcji dla aplikacji sieci web, w tym jak dodać logowanie.

<!--- Removed the diagram as it's already shown in the above link to GitHub

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
