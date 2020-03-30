---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 46cfb27b8bde95990d13ec4bca4e96f25cfe9dc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183746"
---
Ze względu na ciągłe opracowywanie wersja SDK systemu Android zainstalowana w Android Studio może nie być zgodna z wersją w kodzie. Zestawu SDK systemu Android, do którego odwołuje się ten samouczek, jest wersja 26, najpóźniej w momencie pisania. Numer wersji może wzrosnąć w miarę pojawiania się nowych wersji zestawu SDK i zalecamy użycie najnowszej dostępnej wersji.

Dwa objawy niezgodności wersji to:

- Podczas tworzenia lub przebudowy projektu mogą pojawić się `Gradle sync failed: Failed to find target with hash string 'android-XX'`komunikaty o błędach Gradle, takie jak .
- Standardowe obiekty systemu Android w `import` kodzie, który powinien zostać rozpoznany na podstawie instrukcji, mogą generować komunikaty o błędach.

Jeśli którykolwiek z nich pojawi się, wersja sdk systemu Android zainstalowany w programie Android Studio może nie odpowiadać celowi SDK pobranego projektu. Aby zweryfikować wersję, należy wprowadzić następujące zmiany:

1. W Android Studio kliknij pozycję **Narzędzia** > **Android** > **SDK Manager**. Jeśli nie zainstalowano najnowszej wersji platformy SDK, kliknij, aby ją zainstalować. Zanotuj numer wersji.

2. Na karcie **Eksplorator projektu** w obszarze **Skrypty gradle**otwórz plik **build.gradle (moduł: aplikacja)**. Upewnij się, że **compileSdkVersion** i **targetSdkVersion** są ustawione na zainstalowaną najnowszą wersję zestawu SDK. Może `build.gradle` wyglądać następująco:

    ```gradle
    android {
        compileSdkVersion 26
        defaultConfig {
            targetSdkVersion 26
        }
    }
    ```
