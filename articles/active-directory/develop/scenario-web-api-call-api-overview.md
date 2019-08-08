---
title: Internetowy interfejs API, który wywołuje podrzędne interfejsy API sieci Web (omówienie) — Microsoft Identity platform
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ef9fc121b16d81eed932d1ab55ca38d2a2f1057
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852500"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>Scenariusz: Internetowy interfejs API wywołujący internetowe interfejsy API

Dowiedz się, co należy zrobić, aby utworzyć interfejs API sieci Web, który wywołuje interfejsy API sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne

Ten scenariusz, chroniony internetowy interfejs API, który wywołuje interfejsy API sieci Web, jest oparty na scenariuszu "Ochrona internetowego interfejsu API". Aby dowiedzieć się więcej o tym scenariuszu podstaw, zobacz artykuł [Protected Web API — scenariusz](scenario-protected-web-api-overview.md) pierwszy.

## <a name="overview"></a>Przegląd

- Klient (aplikacja sieci Web, komputer stacjonarny, aplikacje mobilne lub jednostronicowe) — nie został przedstawiony na poniższym diagramie — wywołuje chroniony internetowy interfejs API i udostępnia token okaziciela JWT w nagłówku HTTP "Authorization".
- Chroniony internetowy interfejs API sprawdza token i używa metody MSAL `AcquireTokenOnBehalfOf` , aby zażądać (z usługi Azure AD) innego tokenu, tak aby mógł sam, wywołać drugi internetowy interfejs API (o nazwie podrzędny interfejs API sieci Web) w imieniu użytkownika.
- Chroniony internetowy interfejs API używa tego tokenu do wywoływania podrzędnego interfejsu API. Może również wywołać `AcquireTokenSilent`później, aby zażądać tokenów dla innych podrzędnych interfejsów API (ale nadal w imieniu tego samego użytkownika). `AcquireTokenSilent`Odświeża token w razie konieczności.

![Interfejs API sieci Web wywołujący interfejs API sieci Web](media/scenarios/web-api.svg)

## <a name="specifics"></a>Szczegółowych informacji

Częścią rejestracji aplikacji powiązanej z uprawnieniami interfejsu API jest klasyczny. Konfiguracja aplikacji obejmuje używanie przepływu OAuth 2,0 w imieniu użytkownika do wymiany tokenu okaziciela JWT z tokenem dla podrzędnego interfejsu API. Token ten jest dodawany do pamięci podręcznej tokenów, gdzie jest dostępny w kontrolerach internetowego interfejsu API i może uzyskać token dyskretnie wywołujący podrzędne interfejsy API.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](scenario-web-api-call-api-app-registration.md)
