---
title: Tworzenie internetowego interfejsu API, który wywołuje internetowe interfejsy API — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć internetowy interfejs API, który wywołuje podrzędne interfejsy API sieci Web (omówienie).
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
ms.openlocfilehash: 467ff2f789cc83bc5651d831838da0b5c922c839
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701743"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Scenariusz: internetowy interfejs API, który wywołuje internetowe interfejsy API

Dowiedz się, co musisz wiedzieć, aby utworzyć internetowy interfejs API, który wywołuje internetowe interfejsy API.

## <a name="prerequisites"></a>Wymagania wstępne

W tym scenariuszu, w którym chroniony interfejs API sieci web wywołuje interfejsy API sieci web, opiera się na scenariuszu "Chroń internetowy interfejs API". Aby dowiedzieć się więcej o tym podstawowym scenariuszu, zobacz [Scenariusz: Chroniony internetowy interfejs API](scenario-protected-web-api-overview.md).

## <a name="overview"></a>Omówienie

- Klient aplikacji sieci Web, pulpitu, urządzenia przenośnego lub jednostronicowego (nieprezentowany na towarzyszącym diagramie) wywołuje chroniony interfejs API sieci Web i udostępnia token nośny tokenu JSON Web Token (JWT) w nagłówku HTTP "Autoryzacja".
- Chroniony interfejs API sieci Web sprawdza poprawność tokenu i `AcquireTokenOnBehalfOf` używa metody Biblioteki uwierzytelniania firmy Microsoft (MSAL) do żądania innego tokenu z usługi Azure Active Directory (Azure AD), dzięki czemu chroniony internetowy interfejs API może wywołać drugi internetowy interfejs API lub podrzędny interfejs API sieci web w imieniu użytkownika.
- Chroniony interfejs API sieci `AcquireTokenSilent`web można również wywołać później, aby zażądać tokenów dla innych interfejsów API podrzędnego w imieniu tego samego użytkownika. `AcquireTokenSilent`odświeża token w razie potrzeby.

![Diagram internetowego interfejsu API wywołującego internetowy interfejs API](media/scenarios/web-api.svg)

## <a name="specifics"></a>Specyfiki

Część rejestracji aplikacji, która jest związana z uprawnieniami interfejsu API jest klasyczna. Konfiguracja aplikacji obejmuje przy użyciu przepływu W imieniu klienta OAuth 2.0 do wymiany tokenu nośnika JWT względem tokenu dla interfejsu API podrzędnego. Ten token jest dodawany do pamięci podręcznej tokenu, gdzie jest dostępny w kontrolerach interfejsu API sieci web, a następnie można uzyskać token dyskretnie do wywołania interfejsów API niższego rzędu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](scenario-web-api-call-api-app-registration.md)
