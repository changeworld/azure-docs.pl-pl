---
title: Chroniony internetowy interfejs API — Omówienie
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć chroniony internetowy interfejs API (omówienie).
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
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f96393adf0eaed8a28bc1a2ec0def6b0386bd7ac
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701981"
---
# <a name="scenario-protected-web-api"></a>Scenariusz: chroniony internetowy interfejs API

W tym scenariuszu pokazano, jak można uwidocznić internetowy interfejs API i jak można go chronić, tak aby tylko uwierzytelnieni użytkownicy mieli dostęp do interfejsu API. Aby korzystać z internetowego interfejsu API, należy włączyć uwierzytelnionym użytkownikom zarówno konta służbowego, jak i osobistego konta Microsoft.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Szczegółowych informacji

Oto kilka szczegółowych informacji, które należy znać, aby chronić interfejsy API sieci Web:

- Rejestracja aplikacji musi uwidaczniać co najmniej jeden zakres. Wersja tokenu zaakceptowana przez internetowy interfejs API zależy od odbiorców logowania.
- Konfiguracja kodu dla internetowego interfejsu API musi weryfikować token używany podczas wywoływania interfejsu API sieci Web.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](scenario-protected-web-api-app-registration.md)
