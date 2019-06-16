---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 46cfb27b8bde95990d13ec4bca4e96f25cfe9dc5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66141250"
---
Ze względu na trwające prace programistyczne wersji zestawu SDK systemu Android zainstalowane w programie Android Studio może być niezgodna z wersją w kodzie. Zestaw SDK systemu Android, do którego odwołuje się ten samouczek jest wersja 26, najnowsze informacje w czasie pisania. Numer wersji może zwiększyć, są wyświetlane nowe wersje zestawu SDK, a firma Microsoft zaleca używanie najnowsza dostępna wersja.

Są dwa objawy niezgodność wersji:

- Podczas tworzenia lub ponownie skompilować projekt, może wystąpić komunikaty o błędach Gradle, takich jak `Gradle sync failed: Failed to find target with hash string 'android-XX'`.
- Na podstawie standardowych obiektów dla systemu Android w kodzie, który powinna być rozpoznawana `import` instrukcji może generować komunikatów o błędach.

Jeśli któraś z tych pojawi się wersja zestawu SDK systemu Android zainstalowane w programie Android Studio mogą być niezgodne docelowy zestaw SDK z pobranego projektu. Aby sprawdzić wersję, wprowadź następujące zmiany:

1. W programie Android Studio, kliknij przycisk **narzędzia** > **Android** > **menedżera zestawów SDK**. Jeśli nie zainstalowano najnowszej wersji platformy zestawu SDK, następnie kliknij, aby go zainstalować. Zanotuj numer wersji.

2. Na **Eksplorator projektów** , w obszarze **skryptów narzędzia Gradle**, otwórz plik **build.gradle (moduł: aplikacja)** . Upewnij się, że **compileSdkVersion** i **targetSdkVersion** są ustawione na zainstalowaną najnowszą wersję zestawu SDK. `build.gradle` Może wyglądać następująco:

    ```gradle
    android {
        compileSdkVersion 26
        defaultConfig {
            targetSdkVersion 26
        }
    }
    ```
