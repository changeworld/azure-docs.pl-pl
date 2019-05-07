---
title: Demon aplikacji wywoływania interfejsów API sieci web (wywoływania interfejsów API sieci web) — Platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację demona wywołuje interfejsy API (wywoływania interfejsów API sieci web) sieci web
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: aff375f996126d9e8b64361fc0e5673c25d30c19
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076273"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Demon aplikacji, połączeń internetowych interfejsów API — wywołania interfejsu API sieci web z aplikacji

Aplikacji demona można wywoływać internetowy interfejs API z poziomu aplikacji demona .NET lub wywołać kilka wstępnie zatwierdzonych interfejsów API sieci web.

## <a name="calling-a-web-api-from-a-net-daemon-application"></a>Wywoływanie interfejsu web API z aplikacji demona platformy .NET

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-several-apis"></a>Wywoływanie kilka interfejsów API

Aplikacje demona internetowych interfejsów API, który można wywoływać na potrzeby wstępnego zatwierdzenia. Nie będzie żadnych przyrostowe zgodę za pomocą aplikacji demona (zachodzi żadna interakcja użytkownika). Administrator dzierżawy musi na wstępne wyrażanie zgody aplikacji i wszystkich uprawnień interfejsu API. Jeśli chcesz się połączyć kilka interfejsów API, należy uzyskać token dla każdego zasobu każdego wywołania czasu `AcquireTokenForClient`. Biblioteki MSAL użyje pamięci podręcznej tokenu aplikacji, aby uniknąć niepotrzebnych usługa wywołania.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Demon aplikacji — przenoszenie do środowiska produkcyjnego](./scenario-daemon-production.md)