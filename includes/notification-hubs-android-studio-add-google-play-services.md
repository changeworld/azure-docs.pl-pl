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
ms.openlocfilehash: cd202c98ed605209f5600965ecdb6c0b4c03c17e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67509094"
---
1. W programie Android Studio wybierz pozycję **Narzędzia** w menu, a następnie wybierz pozycję **Menedżer zestawu SDK**. 
2. Wybierz docelową wersję sdk systemu Android, który jest używany w projekcie. Następnie wybierz pozycję **Pokaż szczegóły pakietu**. 

    ![Menedżer zestawów Android SDK — wybieranie wersji docelowej](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Wybierz **interfejsy API Google**, jeśli nie są jeszcze zainstalowane.

    ![Menedżer zestawów Android SDK — wybrane interfejsy API firmy Google](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Przełącz się na kartę **Narzędzia zestawu SDK.** Jeśli usługi Google Play nie zostały jeszcze zainstalowane, wybierz pozycję **Usługi Google Play,** jak pokazano na poniższej ilustracji. Następnie wybierz pozycję **Zastosuj** do zainstalowania. Zanotuj ścieżkę zestawu SDK, która będzie potrzebna w kolejnym kroku.

    ![Menedżer zestawów Android SDK — wybrane usługi Google Play](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Jeśli zobaczysz okno dialogowe **Potwierdzanie zmiany**, wybierz przycisk **OK**. Instalator składników zainstaluje żądane składniki. Wybierz przycisk **Finish** (Zakończ) po zainstalowaniu składników.
4. Wybierz przycisk **OK**, aby zamknąć okno dialogowe **Settings for New Projects** (Ustawienia nowego projektu).  
5. Otwórz plik build.gradle w katalogu **aplikacji,** a następnie `dependencies`dodaj następujący wiersz w obszarze . 

    ```gradle
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. Wybierz ikonę **Synchronizuj teraz** na pasku narzędzi.

    ![Synchronizacja za pomocą narzędzia Gradle](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. Otwórz plik AndroidManifest.xml, a następnie dodaj następujący tag do tagu *aplikacji.*

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
