---
title: Aplikacja klasyczna wywołuje interfejsy API (omówienie) — Platforma tożsamości usługi Microsoft sieci web
description: Naucz się tworzyć aplikację klasyczną wywołuje interfejsy API (omówienie) sieci web
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
ms.openlocfilehash: 44d31011ca70bbebaf994b5fb80a45eee8dbde40
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076948"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Scenariusz: Aplikacja klasyczna wywołuje interfejsy API sieci web

Dowiedz się, wszystko, czego potrzebujesz do tworzenia aplikacji pulpitu, która wywołuje interfejsy API sieci web

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Wprowadzenie

Jeśli jeszcze nie, należy utworzyć swoją pierwszą aplikację, postępując zgodnie z przewodnika Szybki Start klasycznej platformy .NET lub szybkiego startu platformy uniwersalnej systemu Windows:

> [!div class="nextstepaction"]
> [Szybki start: Uzyskiwanie tokenu i wywołania interfejsu API Microsoft Graph z aplikacji klasycznej Windows](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Szybki start: Uzyskiwanie tokenu i wywołania interfejsu API Microsoft Graph z poziomu aplikacji platformy uniwersalnej systemu Windows](./quickstart-v2-uwp.md)

## <a name="overview"></a>Omówienie

Pisanie aplikacji pulpitu, a chcesz logowania użytkowników do aplikacji i wywołań interfejsów API, takich jak Microsoft Graph, innych APIs firmy Microsoft lub własnych internetowy interfejs API sieci web. Istnieje kilka możliwości:

- Jeśli swojej aplikacji klasycznej obsługuje formanty graficzny, na przykład jeśli jest to aplikacja Windows.Form lub aplikacji WPF, możesz użyć interaktywne uzyskanie tokenu.
- Dla aplikacji Windows, hostowane użytkownik może również dla aplikacji uruchamianych na komputerach dołączonych do domeny Windows lub usługi AAD przyłączone do uzyskać token w trybie dyskretnym przy użyciu zintegrowanego uwierzytelniania Windows.
- Na koniec chociaż nie jest to zalecane, można użyć nazwy użytkownika i hasła w aplikacjach klienckich publicznych. Nadal jest konieczne w niektórych scenariuszach (np. DevOps), ale należy pamiętać, że korzystania z niego będzie nakładają ograniczenia dla aplikacji. Na przykład nie można zarejestrować użytkownika, która musi wykonywać uwierzytelnianie wieloskładnikowe (dostęp warunkowy). Ponadto aplikacji nie będzie korzystać z logowania jednokrotnego (SSO).

  Jest również względem zasad nowoczesnego uwierzytelniania i jest dostępna wyłącznie dla starszych powodów.

  ![Aplikacji klasycznej](media/scenarios/desktop-app.svg)

- Jeśli piszesz przenośne narzędzie wiersza polecenia — prawdopodobnie aplikacji .NET Core w systemie Linux lub Mac — nie można używać ani przeprowadzić uwierzytelnianie interakcyjne (zgodnie z platformy .NET Core nie zapewnia [przeglądarki sieci Web](https://aka.ms/msal-net-uses-web-browser)), ani zintegrowany Uwierzytelnianie Windows. Najlepszym rozwiązaniem w takiej sytuacji jest użycie przepływu kodu urządzenia. Ten przepływ jest również używany do aplikacji bez przeglądarki, takich jak aplikacje iOT

  ![Browserless aplikacji](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Szczegółowe informacje

Aplikacje klasyczne mają wiele specyfikę, zależy głównie od tego, czy Twoja aplikacja używa uwierzytelnianie interakcyjne, czy nie.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Aplikacja klasyczna — rejestrowanie aplikacji](scenario-desktop-app-registration.md)
