---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 02/05/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: fb27386881e89cd9056d0efccb7d3c301867bd83
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183481"
---
1. W programie **Android Studio**, wybierz z menu pozycję **Tools** (Narzędzia) i wybierz pozycję **SDK Manager** (Menedżer zestawów SDK). 
2. Wybierz wersję docelową zestawu Android SDK, który jest używany w projekcie, a następnie wybierz pozycję **Show Package Details** (Pokaż szczegóły pakietu). 

    ![Menedżer zestawów Android SDK — wybieranie wersji docelowej](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Wybierz pozycję **Google APIs** (Interfejsy API firmy Google), jeśli jeszcze nie zostały one zainstalowane.

    ![Menedżer zestawów Android SDK — wybrane interfejsy API firmy Google](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Przełącz się na kartę **SDK Tools** (Narzędzia zestawu SDK). Jeśli usługa Google Play nie została jeszcze zainstalowana, wybierz pozycję **Google Play Services** (Usługi Google Play), jak pokazano na następującej ilustracji. Następnie kliknij opcję **Apply** (Zastosuj), aby zainstalować usługę. Zanotuj ścieżkę zestawu SDK, która będzie potrzebna w kolejnym kroku.

    ![Menedżer zestawów Android SDK — wybrane usługi Google Play](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Jeśli zobaczysz okno dialogowe **Potwierdzanie zmiany**, wybierz przycisk **OK**. Instalator składników zainstaluje żądane składniki. Wybierz przycisk **Finish** (Zakończ) po zainstalowaniu składników.
4. Wybierz przycisk **OK**, aby zamknąć okno dialogowe **Settings for New Projects** (Ustawienia nowego projektu).  
5. Otwórz plik `build.gradle` w katalogu **app** i dodaj następujący wiersz w sekcji `dependencies`. 

    ```gradle
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. Wybierz ikonę **Sync Now** (Synchronizuj teraz) na pasku narzędzi.

    ![Synchronizacja za pomocą narzędzia Gradle](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. Otwórz plik **AndroidManifest.xml** i dodaj ten tag do tagu *aplikacji*.

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
