---
title: Aplikacja klasyczna, która wywołuje interfejsy API sieci Web (omówienie) — platforma tożsamości firmy Microsoft
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
ms.openlocfilehash: 5288fe57b2f83522b140f65fa62e08f9c63a7af5
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852681"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Scenariusz: Aplikacja klasyczna wywołująca internetowe interfejsy API

Dowiedz się wszystkiego, co jest potrzebne do utworzenia aplikacji klasycznej, która wywołuje interfejsy API sieci Web

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Wprowadzenie

Jeśli jeszcze tego nie zrobiono, Utwórz swoją pierwszą aplikację, postępując zgodnie z przewodnikiem Szybki Start dla programu .NET Desktop lub platformy UWP szybki start:

> [!div class="nextstepaction"]
> [Szybki start: Uzyskiwanie tokenu i wywoływanie Microsoft Graph interfejsu API z aplikacji klasycznej systemu Windows](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Szybki start: Uzyskiwanie tokenu i wywoływanie Microsoft Graph interfejsu API z poziomu aplikacji platformy UWP](./quickstart-v2-uwp.md)

## <a name="overview"></a>Omówienie

Napisz aplikację klasyczną i chcesz zalogować użytkowników do aplikacji i wywoływać interfejsy API sieci Web, takie jak Microsoft Graph, inne interfejsy API firmy Microsoft lub własny internetowy interfejs API. Masz kilka możliwości:

- Możesz użyć pozyskiwania tokenów interaktywnych:

  - Jeśli aplikacja klasyczna obsługuje kontrolki graficzne, na przykład jeśli jest to aplikacja Windows. form lub aplikacja WPF.
  - Jeśli jest to aplikacja platformy .NET Core i wyrażasz zgodę na interakcję uwierzytelniania z usługą Azure AD w przeglądarce systemowej

- W przypadku aplikacji hostowanych w systemie Windows istnieje również możliwość, że aplikacje działające na komputerach przyłączonych do domeny systemu Windows lub usługi AAD są dołączone do dyskretnego uzyskiwania tokenu przy użyciu zintegrowanego uwierzytelniania systemu Windows.
- Na koniec, chociaż nie jest to zalecane, możesz użyć nazwy użytkownika/hasła w publicznych aplikacjach klienckich. Nadal jest to konieczne w niektórych scenariuszach (takich jak DevOps), ale uważaj, że użycie go spowoduje nałożenie ograniczeń dotyczących aplikacji. Na przykład nie może zalogować użytkownika, który musi przeprowadzić uwierzytelnianie wieloskładnikowe (dostęp warunkowy). Ponadto aplikacja nie będzie korzystać z logowania jednokrotnego (SSO).

  Jest to również sprzeczne z zasadami nowoczesnego uwierzytelniania i są dostępne tylko dla starszych przyczyn.

  ![Aplikacja klasyczna](media/scenarios/desktop-app.svg)

- W przypadku pisania przenośnego narzędzia wiersza polecenia — prawdopodobnie aplikacji platformy .NET Core działającej w systemie Linux lub Mac — w przypadku zaakceptowania, że uwierzytelnianie zostanie delegowane do przeglądarki systemowej, będzie można korzystać z uwierzytelniania interakcyjnego. (Platforma .NET Core nie udostępnia jeszcze [przeglądarki sieci Web](https://aka.ms/msal-net-uses-web-browser) , dlatego uwierzytelnianie odbywa się w przeglądarce systemowej), w przeciwnym razie najlepszą opcją w tym przypadku jest użycie przepływu kodu urządzenia. Ten przepływ jest również używany w przypadku aplikacji bez przeglądarki, takich jak aplikacje IoT

  ![Aplikacja bezprzeglądarki](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Szczegółowych informacji

Aplikacje klasyczne mają różne wartości, które są zależne od tego, czy aplikacja korzysta z uwierzytelniania interaktywnego, czy nie.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Aplikacja klasyczna — Rejestracja aplikacji](scenario-desktop-app-registration.md)
