---
title: Przenoszenie internetowego interfejsu API wywoływanie interfejsów API sieci Web do środowiska produkcyjnego Microsoft Identity platform | Azure
description: Dowiedz się, jak przenieść internetowy interfejs API, który wywołuje interfejsy API sieci Web w środowisku produkcyjnym.
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
ms.openlocfilehash: 0d59a5b2a74c10e36103713725113cbe8c9cc412
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965173"
---
# <a name="web-api-that-calls-web-apis---move-to-production"></a>Internetowy interfejs API, który wywołuje interfejsy API sieci Web — Przenieś do środowiska produkcyjnego

Po uzyskaniu tokenu do wywoływania interfejsów API sieci Web można przenieść aplikację do środowiska produkcyjnego.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Dowiedz się więcej

Teraz, gdy znasz podstawowe informacje o sposobie wywoływania interfejsów API sieci Web z własnego internetowego interfejsu API, być może zainteresuje Cię ten samouczek, w którym opisano kod używany do tworzenia chronionego interfejsu API sieci Web wywołującego interfejsy API sieci Web.

| Przykład | Platforma | Opis |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | Interfejs API sieci Web ASP.NET Core 2,2, Desktop (WPF) | Interfejs API sieci Web ASP.NET Core 2,2 wywoływanie Microsoft Graph, który jest wywoływany z aplikacji WPF przy użyciu platformy tożsamości firmy Microsoft (v 2.0) |
