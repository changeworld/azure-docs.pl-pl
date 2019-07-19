---
title: Aplikacja mobilna, która wywołuje interfejsy API sieci Web — konfiguracja kodu aplikacji | Platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację mobilną wywołującą interfejsy API sieci Web (konfigurację kodu aplikacji)
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
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f43ae9da51f68c9765a36d27c993d1c9935d61fa
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326122"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>Aplikacja mobilna, która wywołuje interfejsy API sieci Web — Rejestracja aplikacji

Ten artykuł zawiera instrukcje dotyczące rejestracji aplikacji na potrzeby tworzenia aplikacji mobilnej.

## <a name="supported-account-types"></a>Obsługiwane typy konta

Typy kont obsługiwane w aplikacjach mobilnych zależą od środowiska, które chcesz włączyć, i użytkowników, dla których aplikacja jest ukierunkowana.

## <a name="platform-configuration-and-redirect-uris"></a>Konfiguracja platformy i identyfikatory URI przekierowania  

Podczas kompilowania aplikacji mobilnej najważniejszym krokiem rejestracji jest identyfikator URI przekierowania. Tę wartość można ustawić za pomocą [konfiguracji platformy w bloku uwierzytelnianie](https://aka.ms/MobileAppReg).

To środowisko umożliwi aplikacji uzyskanie rejestracji jednokrotnej (SSO) za pomocą Microsoft Authenticator (i Intune — Portal firmy w systemie Android), a także obsługę zasad zarządzania urządzeniami.

Jeśli wolisz ręcznie skonfigurować identyfikator URI przekierowania, możesz to zrobić za pomocą manifestu aplikacji. Zalecany format jest następujący:

- ***iOS***: `msauth.<BUNDLE_ID>://auth`
- System ***Android***:`msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Skrót sygnatury systemu Android można wygenerować przy użyciu klawiszy Zwolnij lub Debuguj za pomocą polecenia narzędzia.

## <a name="api-permissions"></a>Uprawnienia interfejsu API

Interfejsy API wywołania aplikacji mobilnych w imieniu zalogowanego użytkownika. Aplikacja musi zażądać delegowania uprawnień, nazywanych również zakresami. W zależności od wymaganego środowiska można to zrobić statycznie przez Azure Portal lub dynamicznie w czasie wykonywania. Statyczne rejestrowanie uprawnień pozwala administratorom łatwo zatwierdzać aplikację i jest zalecane.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu](scenario-mobile-acquire-token.md)
