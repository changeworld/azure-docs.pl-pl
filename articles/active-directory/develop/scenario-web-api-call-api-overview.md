---
title: Tworzenie interfejsu API sieci Web, który wywołuje interfejsy API sieci Web — Microsoft Identity platform | Azure
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
ms.openlocfilehash: b67507daf8005f3f9a299b778f1fba4ce67d46d4
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044148"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Scenariusz: internetowy interfejs API, który wywołuje interfejsy API sieci Web

Dowiedz się, co musisz wiedzieć, aby utworzyć interfejs API sieci Web, który wywołuje interfejsy API sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne

W tym scenariuszu, w którym chroniony interfejs API sieci Web wywołuje interfejsy API sieci Web, kompiluje się w scenariuszu "Ochrona internetowego interfejsu API". Aby dowiedzieć się więcej na temat tego scenariusza, zobacz temat [Scenariusz: chroniony internetowy interfejs API](scenario-protected-web-api-overview.md).

## <a name="overview"></a>Przegląd

- Klient sieci Web, pulpitu, aplikacji mobilnej lub jednostronicowej (niereprezentowanej na towarzyszącym diagramie) wywołuje chroniony internetowy interfejs API i udostępnia token okaziciela sieci Web w formacie JSON (JWT) w nagłówku HTTP "Authorization".
- Chroniony internetowy interfejs API sprawdza token i używa metody `AcquireTokenOnBehalfOf` Microsoft Authentication Library (MSAL), aby zażądać innego tokenu od Azure Active Directory (Azure AD), dzięki czemu chroniony internetowy interfejs API może wywołać drugi internetowy interfejs API lub podrzędny interfejs API sieci Web w imieniu użytkownika.
- Chroniony internetowy interfejs API może również wywołać `AcquireTokenSilent`później, aby żądać tokenów innych podrzędnych interfejsów API w imieniu tego samego użytkownika. `AcquireTokenSilent` odświeża token w razie konieczności.

![Diagram interfejsu API sieci Web wywołującego interfejs API sieci Web](media/scenarios/web-api.svg)

## <a name="specifics"></a>Szczegółowych informacji

Część rejestracji aplikacji, która jest powiązana z uprawnieniami interfejsu API, jest klasyczna. Konfiguracja aplikacji obejmuje używanie przepływu OAuth 2,0 w imieniu użytkownika do wymiany tokenu okaziciela JWT z tokenem dla podrzędnego interfejsu API. Token ten jest dodawany do pamięci podręcznej tokenów, gdzie jest dostępny w kontrolerach internetowego interfejsu API, a następnie może uzyskać token dyskretnie wywołujący podrzędne interfejsy API.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](scenario-web-api-call-api-app-registration.md)
