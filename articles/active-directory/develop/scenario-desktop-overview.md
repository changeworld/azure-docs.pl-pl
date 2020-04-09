---
title: Tworzenie aplikacji klasycznej, która wywołuje internetowe interfejsy API — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację klasyczną, która wywołuje internetowe interfejsy API (omówienie)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 09cc43dec2eff48754f5a6e693badd6bb1907cce
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883005"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Scenariusz: aplikacja komputerowa, która wywołuje internetowe interfejsy API

Dowiedz się wszystkiego, czego potrzebujesz, aby utworzyć aplikację klasyczną, która wywołuje internetowe interfejsy API.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="get-started"></a>Rozpoczęcie pracy

Jeśli jeszcze tego nie zrobiłeś, utwórz pierwszą aplikację, wykonując szybki start pulpitu platformy .NET, szybki szybki start platformy uniwersalnej systemu Windows (UWP) lub szybki start aplikacji natywnej dla systemu macOS:

> [!div class="nextstepaction"]
> [Szybki start: uzyskiwanie tokenu i wywoływanie interfejsu API programu Microsoft Graph z poziomu aplikacji klasycznej w systemie Windows](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Szybki start: uzyskiwanie tokenu i wywoływanie interfejsu API programu Microsoft Graph z aplikacji platformy uniwersalnej systemu](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [Szybki start: uzyskiwanie tokenu i wywoływanie interfejsu API programu Microsoft Graph z natywnej aplikacji systemu macOS](./quickstart-v2-ios.md)

## <a name="overview"></a>Omówienie

Piszesz aplikację klasyczną i chcesz zalogować użytkowników do aplikacji i wywołać interfejsy API sieci web, takie jak Microsoft Graph, inne interfejsy API firmy Microsoft lub własny internetowy interfejs API. Masz kilka możliwości:

- Można użyć interaktywnego pozyskiwania tokenów:

  - Jeśli aplikacja klasyczna obsługuje formanty graficzne, na przykład, jeśli jest to aplikacja Windows.Form, aplikacja WPF lub natywna aplikacja macOS.
  - Lub jeśli jest to aplikacja .NET Core i zgadzasz się na interakcję uwierzytelniania z usługą Azure Active Directory (Azure AD) w przeglądarce systemu.

- W przypadku aplikacji hostowanych systemu Windows jest również możliwe dla aplikacji uruchomionych na komputerach przyłączonych do domeny systemu Windows lub usługi Azure AD przyłączonych do uzyskania tokenu po cichu przy użyciu zintegrowanego uwierzytelniania systemu Windows.
- Na koniec i chociaż nie jest to zalecane, można użyć nazwy użytkownika i hasła w publicznych aplikacjach klienckich. Jest to nadal potrzebne w niektórych scenariuszach, takich jak DevOps. Korzystanie z niego nakłada ograniczenia na aplikację. Na przykład nie można zalogować użytkownika, który musi wykonać uwierzytelnianie wieloskładnikowe (dostęp warunkowy). Ponadto aplikacja nie będzie korzystać z logowania jednokrotnego (Logowanie jednokrotne).

  Jest to również sprzeczne z zasadami nowoczesnego uwierzytelniania i jest dostarczane tylko ze względu na dziedzictwo.

  ![Aplikacja komputerowa](media/scenarios/desktop-app.svg)

- Jeśli piszesz przenośne narzędzie wiersza polecenia, prawdopodobnie aplikację .NET Core, która działa na systemie Linux lub Mac, a jeśli zaakceptujesz, że uwierzytelnianie zostanie delegowane do przeglądarki systemowej, możesz użyć uwierzytelniania interaktywnego. Program .NET Core nie udostępnia [przeglądarki internetowej,](https://aka.ms/msal-net-uses-web-browser)więc uwierzytelnianie odbywa się w przeglądarce systemowej. W przeciwnym razie najlepszym rozwiązaniem w takim przypadku jest użycie przepływu kodu urządzenia. Przepływ ten jest również używany dla aplikacji bez przeglądarki, takich jak aplikacje IoT.

  ![Aplikacja bez przeglądarki](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Specyfiki

Aplikacje klasyczne mają wiele specyfiki. Zależą one głównie od tego, czy aplikacja używa uwierzytelniania interaktywnego, czy nie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Aplikacja komputerowa: rejestracja aplikacji](scenario-desktop-app-registration.md)
