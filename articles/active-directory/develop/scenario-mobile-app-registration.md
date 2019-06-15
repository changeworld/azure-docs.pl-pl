---
title: Aplikacja mobilna, że interfejsy API — konfiguracji kodu aplikacji sieci web wywołuje | Platforma tożsamości firmy Microsoft
description: Informacje o sposobie tworzenia aplikacji mobilnych, połączeń internetowych interfejsów API (konfiguracji kodu aplikacji)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
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
ms.openlocfilehash: 2b6ebab0eeca6895e1c7a0f6008972030d81da42
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65962407"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>Aplikacja mobilna, że wywołania sieci web interfejsy API — rejestrowanie aplikacji

Ten artykuł zawiera instrukcje rejestracji aplikacji do tworzenia aplikacji mobilnych.

## <a name="supported-account-types"></a>Obsługiwane typy konta

Typy kont, obsługiwane w aplikacjach mobilnych zależą od tego, środowisko, które chcesz włączyć i użytkowników, dla których jest przeznaczona aplikacja.

## <a name="platform-configuration-and-redirect-uris"></a>Konfiguracja platformy i identyfikatory URI przekierowania  

Podczas kompilowania aplikacji mobilnej, najbardziej krytycznym kroku rejestracji jest identyfikator URI przekierowania. Można to skonfigurować za pomocą [konfigurację platformy w bloku uwierzytelniania](https://aka.ms/MobileAppReg).

Środowisko to umożliwi aplikację, aby uzyskać logowanie jednokrotne (SSO) przy użyciu Microsoft Authenticator (i Portal firmy usługi Intune w systemie Android) oraz pomocy technicznej zasady zarządzania urządzeniami.

Jeśli chcesz ręcznie skonfigurować identyfikator URI przekierowania, możesz to zrobić za pośrednictwem manifestu aplikacji. Zalecany format jest następujący:

- ***iOS***: `msauth.<BUNDLE_ID>://auth`
- ***Android***: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Wyznaczania wartości skrótu podpisu dla systemu Android mogą być generowane przy użyciu kluczy wydania lub debugowania za pomocą polecenia KeyTool.

## <a name="api-permissions"></a>Uprawnienia do interfejsu API

Aplikacje mobilne wywoływania interfejsów API w imieniu zalogowanego użytkownika. Twoja aplikacja wymaga zażądać uprawnień delegowanych, nazywana także zakresów. W zależności od założenie można to zrobić statycznie za pośrednictwem witryny Azure portal lub dynamicznie w czasie wykonywania. Statycznie zarejestrowanie uprawnień umożliwia administratorom łatwe zatwierdzić aplikację i jest zalecane.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu](scenario-mobile-acquire-token.md)
