---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 01/04/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: f00ca7ddf44a9d5b850cd47520970a0396a0c1b5
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54453100"
---
1. Otwórz narzędzie Android SDK Manager, klikając ikonę na pasku narzędzi programu Android Studio lub klikając kolejno opcje **Tools** (Narzędzia) > **Android** > **SDK Manager** w menu. Znajdź wersję docelową zestawu SDK systemu Android używaną w projekcie, otwórz ją, klikając opcję **Show Package Details** (Pokaż szczegóły pakietu), po czym wybierz opcję **Google APIs** (Interfejsy API Google), jeśli nie została ona jeszcze zainstalowana.
2. Kliknij kartę **SDK Tools** (Narzędzia zestawu SDK). Jeśli usługa Google Play nie została jeszcze zainstalowana, kliknij pozycję **Google Play Services** (Usługi Google Play), jak przedstawiono poniżej. Następnie kliknij opcję **Apply** (Zastosuj), aby zainstalować usługę. Zanotuj ścieżkę zestawu SDK, która będzie potrzebna w kolejnym kroku.

    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Otwórz plik `build.gradle` w katalogu aplikacji.

    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)
4. Dodaj następujący wiersz w obszarze `dependencies`:

    ```text
    compile 'com.google.android.gms:play-services-gcm:12.0.0'
    ```
5. Kliknij ikonę **Sync Project with Gradle Files** (Synchronizuj projekt z plikami narzędzia Gradle) na pasku narzędzi.
6. Otwórz plik **AndroidManifest.xml** i dodaj ten tag do tagu *aplikacji*.

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
