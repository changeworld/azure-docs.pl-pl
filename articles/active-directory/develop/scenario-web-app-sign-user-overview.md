---
title: Aplikacja sieci Web, logujący się użytkownicy (omówienie) — Platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację sieci web, logujący się użytkownicy (omówienie)
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
ms.openlocfilehash: 6ce534c6eeecba220fd829be829caa679df52055
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65833101"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Scenariusz: Aplikacja internetowa z możliwością logowania użytkowników

Dowiedz się, wszystko, czego potrzebujesz do tworzenia aplikacji sieci web przez użytkowników logowania z platformą Microsoft identity.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Wprowadzenie

Jeśli chcesz utworzyć pierwszy przenośnego (platformy ASP.NET Core) aplikacji sieci web, które logują użytkowników tego przewodnika Szybki Start:

> [!div class="nextstepaction"]
> [Szybki start: Aplikacja internetowa platformy ASP.NET Core, przez logowania użytkowników](quickstart-v2-aspnet-core-webapp.md)

Jeśli chcesz pozostać przy użyciu platformy ASP.NET, wypróbuj następującego samouczka:

> [!div class="nextstepaction"]
> [Szybki start: Aplikacja sieci web platformy ASP.NET przez logowania użytkowników](quickstart-v2-aspnet-webapp.md)

## <a name="overview"></a>Omówienie

Możesz dodać do uwierzytelniania aplikacji sieci web, dzięki czemu można logowania użytkowników. Dodawanie uwierzytelniania umożliwia aplikacji dostęp do informacji o profilu ograniczona, a, na przykład Dostosowywanie doświadczenia z oferowanych swoim użytkownikom w sieci web. Aplikacje sieci Web uwierzytelnienia użytkownika w przeglądarce sieci web. W tym scenariuszu aplikacji sieci web poleca przeglądarkę użytkownika do logowania do usługi Azure AD. Usługa Azure AD, zwraca odpowiedź logowania za pośrednictwem przeglądarki przez użytkownika, który zawiera oświadczenia dotyczące użytkownika w tokenie zabezpieczającym. Korzystać z logowania użytkowników [Open ID Connect](./v2-protocols-oidc.md) standardowego protokołu sam upraszczane przez użycie oprogramowania pośredniczącego [biblioteki](scenario-web-app-sign-user-app-configuration.md#libraries-used-to-protect-web-apps).

![Aplikacja sieci Web logowania użytkowników](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Jako drugą fazę można również włączyć aplikację, aby wywoływać interfejsy API sieci Web w imieniu zalogowanego użytkownika. Ta kolejną fazą jest inny scenariusz znajdziesz w [aplikacji sieci Web wywołuje interfejsy API sieci Web](scenario-web-app-call-api-overview.md)

> [!NOTE]
> Dodawanie logowania do sieci web aplikacja jest o ochronie aplikacji sieci web i sprawdzanie poprawności tokenu użytkownika, to znaczy, elementy **oprogramowania pośredniczącego** czy biblioteki. Ten scenariusz nie wymaga jeszcze Microsoft uwierzytelniania biblioteki (MSAL), które dotyczą uzyskiwania tokenu służącego do wywoływania chronionego interfejsów API. Biblioteki uwierzytelniania tylko zostaną wprowadzone w tym scenariuszu monitowania, gdy aplikacja sieci web musi wywoływać interfejsy API sieci web.

## <a name="specifics"></a>Szczegółowe informacje

- Podczas rejestracji aplikacji, należy podać jedną kilka (Jeśli aplikacja jest wdrażana na kilku lokalizacjach) odpowiedzi oraz identyfikatorów URI. W niektórych przypadkach (rdzenie ASP.NET/ASP.NET) należy włączyć IDToken. Na koniec należy skonfigurować wylogowania identyfikatora URI, aby aplikacja reaguje na użytkowników logowania na poziomie.
- W kodzie aplikacji musisz podać urząd, do której sieci web logowania delegatów aplikacji. Możesz chcieć dostosować walidacji tokenów (w szczególności w scenariuszach niezależnego dostawcy oprogramowania).
- Aplikacje sieci Web obsługuje wszystkie typy kont. Aby uzyskać więcej informacji, zobacz [obsługiwane typy kont](v2-supported-account-types.md).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](scenario-web-app-sign-user-app-registration.md)
