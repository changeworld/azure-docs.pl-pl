---
title: Internetowy interfejs API, który wywołuje podrzędnego internetowych interfejsów API (omówienie) — Platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć internetowy interfejs API tego wywołania podrzędne interfejsów API sieci web (omówienie).
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
ms.openlocfilehash: 497134b7f3cc535f7b3f180db13cd04ef56787db
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075403"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>Scenariusz: Internetowy interfejs API wywołujący internetowe interfejsy API

Dowiedz się, że wszystko, czego potrzebujesz do tworzenia interfejsu API sieci web, które wywołuje interfejsy API sieci web.

## <a name="prerequisites"></a>Wymagania wstępne

Ten scenariusz chroniony internetowy interfejs API, że wywołania internetowych interfejsów API, opiera się na podstawie scenariusza "Chronienia internetowego interfejsu API". Aby dowiedzieć się więcej na temat tego scenariusza podstawowe, zobacz [chroniony internetowy interfejs API — scenariusz](scenario-protected-web-api-overview.md) pierwszy.

## <a name="overview"></a>Omówienie

- Klient (sieci web, pulpitu, aplikacji mobilnej lub jednostronicowych) — nie reprezentowane na diagramie poniżej - wywołuje chroniony internetowy interfejs API i zawiera token elementu nośnego tokenu JWT w nagłówku Http "Autoryzacja".
- Chroniony internetowy interfejs API sprawdza poprawność tokenu i używa biblioteki MSAL `AcquireTokenOnBehalfOf` metodę, aby zażądać (z usługi Azure AD) inny token, więc, że jest to możliwe, wywołaj drugi interfejs API sieci web (o nazwie transmisji internetowego interfejsu API) w imieniu użytkownika.
- Chroniony internetowy interfejs API używa tego tokenu do wywoływania podrzędnego interfejsu API. Można również wywołać `AcquireTokenSilent`nowszej, aby żądania tokenów dla innych interfejsów API, podrzędne (ale nadal w imieniu tego samego użytkownika). `AcquireTokenSilent` Odświeża token, gdy potrzebne.

![Interfejs API wywołuje internetowy interfejs API sieci Web](media/scenarios/web-api.svg)

## <a name="specifics"></a>Szczegółowe informacje

Część rejestracji aplikacji powiązanych z uprawnieniami interfejsu API jest klasycznego. Konfiguracja aplikacji obejmuje przy użyciu protokołu OAuth 2.0 w imieniu użytkownika z usługi flow do wymiany tokenu elementu nośnego tokenu JWT dla tokenu dla podrzędnego interfejsu API. Token ten jest dodawana do pamięci podręcznej tokenu, gdzie jest dostępna w kontrolerach internetowego interfejsu API i można uzyskać token dyskretnie do wywoływania interfejsów API niższego rzędu.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](scenario-web-api-call-api-app-registration.md)
