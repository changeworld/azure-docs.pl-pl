---
title: Tworzenie aplikacji klasycznej, która wywołuje interfejsy API sieci Web — Microsoft Identity platform | Azure
description: Dowiedz się, jak utworzyć aplikację klasyczną wywołującą interfejsy API sieci Web (omówienie)
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
ms.openlocfilehash: 545012629686e1fe3ece8a48ed852542e09e54fe
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965521"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Scenariusz: aplikacja klasyczna, która wywołuje interfejsy API sieci Web

Dowiedz się wszystkiego, co jest potrzebne do utworzenia aplikacji klasycznej, która wywołuje interfejsy API sieci Web

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Wprowadzenie

Jeśli jeszcze tego nie zrobiono, Utwórz swoją pierwszą aplikację, postępując zgodnie z przewodnikiem Szybki Start dla programu .NET Desktop, platformy UWP przewodniku szybki start lub aplikacji natywnej macOS:

> [!div class="nextstepaction"]
> [Szybki Start: uzyskiwanie tokenu i wywoływanie Microsoft Graph interfejsu API z aplikacji klasycznej systemu Windows](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Szybki Start: uzyskiwanie tokenu i wywoływanie Microsoft Graph interfejsu API z poziomu aplikacji platformy UWP](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [Szybki Start: uzyskiwanie tokenu i wywoływanie Microsoft Graph interfejsu API z natywnej aplikacji macOS](./quickstart-v2-ios.md)

## <a name="overview"></a>Przegląd

Napisz aplikację klasyczną i chcesz zalogować użytkowników do aplikacji i wywoływać interfejsy API sieci Web, takie jak Microsoft Graph, inne interfejsy API firmy Microsoft lub własny internetowy interfejs API. Masz kilka możliwości:

- Możesz użyć pozyskiwania tokenów interaktywnych:

  - Jeśli aplikacja klasyczna obsługuje kontrolki graficzne, na przykład jeśli jest to aplikacja typu Windows. form, aplikacja WPF lub aplikacja natywna macOS.
  - Jeśli jest to aplikacja platformy .NET Core i wyrażasz zgodę na interakcję uwierzytelniania z usługą Azure AD w przeglądarce systemowej

- W przypadku aplikacji hostowanych w systemie Windows istnieje również możliwość, że aplikacje działające na komputerach przyłączonych do domeny systemu Windows lub usługi AAD są dołączone do dyskretnego uzyskiwania tokenu przy użyciu zintegrowanego uwierzytelniania systemu Windows.
- Na koniec, chociaż nie jest to zalecane, możesz użyć nazwy użytkownika/hasła w publicznych aplikacjach klienckich. Nadal jest to konieczne w niektórych scenariuszach (takich jak DevOps), ale uważaj, że użycie go spowoduje nałożenie ograniczeń dotyczących aplikacji. Na przykład nie może zalogować użytkownika, który musi przeprowadzić uwierzytelnianie wieloskładnikowe (dostęp warunkowy). Ponadto aplikacja nie będzie korzystać z logowania jednokrotnego (SSO).

  Jest to również sprzeczne z zasadami nowoczesnego uwierzytelniania i są dostępne tylko dla starszych przyczyn.

  ![Aplikacja klasyczna](media/scenarios/desktop-app.svg)

- W przypadku pisania przenośnego narzędzia wiersza polecenia — prawdopodobnie aplikacji platformy .NET Core działającej w systemie Linux lub Mac — w przypadku zaakceptowania, że uwierzytelnianie zostanie delegowane do przeglądarki systemowej, będzie można korzystać z uwierzytelniania interakcyjnego. (Platforma .NET Core nie udostępnia jeszcze [przeglądarki sieci Web](https://aka.ms/msal-net-uses-web-browser) , dlatego uwierzytelnianie odbywa się w przeglądarce systemowej), w przeciwnym razie najlepszą opcją w tym przypadku jest użycie przepływu kodu urządzenia. Ten przepływ jest również używany w przypadku aplikacji bez przeglądarki, takich jak aplikacje IoT

  ![Aplikacja bezprzeglądarki](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Szczegółowych informacji

Aplikacje klasyczne mają różne wartości, które są zależne od tego, czy aplikacja korzysta z uwierzytelniania interaktywnego, czy nie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Aplikacja klasyczna — Rejestracja aplikacji](scenario-desktop-app-registration.md)
