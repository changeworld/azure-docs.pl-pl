---
title: Internetowy interfejs API, wywołuje interfejsy API sieci web (przenoszenie do środowiska produkcyjnego) — Platforma tożsamości usługi Microsoft
description: Dowiedz się, jak utworzyć internetowy interfejs API podrzędne wywołuje interfejsy API (przenoszenie do środowiska produkcyjnego) sieci web.
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
ms.openlocfilehash: 7de892a0851e556c5046fe4b3691f39b42a9d237
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074758"
---
# <a name="web-api-that-calls-web-apis---move-to-production"></a>Internetowy interfejs API, która wywołuje interfejsy API — w sieci web, przejść do środowiska produkcyjnego

Po nabyciu tokenu służącego do wywoływania interfejsów API sieci web, możesz przenieść aplikację do środowiska produkcyjnego.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Dowiedz się więcej

Teraz, kiedy znasz podstawy jak wywołać interfejsy API sieci web z własnego interfejsu API sieci web, być może zainteresuje tego samouczka opisano kod, który został użyty do tworzenia chroniony internetowy interfejs API wywołuje interfejsy API sieci web.

| Sample | Platforma | Opis |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | Platforma ASP.NET Core 2.2 interfejsu API sieci Web, pulpitu (WPF) | Wywołanie programu Microsoft Graph API sieci Web platformy ASP.NET Core 2.2, sama wywołać z aplikacji WPF przy użyciu platformy tożsamości firmy Microsoft (w wersji 2.0) |
