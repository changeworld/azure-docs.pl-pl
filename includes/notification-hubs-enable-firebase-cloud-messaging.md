---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 09/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ccb5e40738680181e7339b8652d029597c7d0bd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70935097"
---
1. Zaloguj się do [konsoli Firebase](https://firebase.google.com/console/). Utwórz nowy projekt Firebase, jeśli jeszcze go nie masz.
2. Po utworzeniu projektu wybierz pozycję **Dodaj Firebase do swojej aplikacji dla systemu Android**. 

    ![Dodawanie usługi Firebase do aplikacji systemu Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Na stronie **Dodaj firebase do aplikacji na Androida,** podejmij następujące kroki: 
    1. Dla **nazwy pakietu systemu Android,** skopiować wartość **applicationId** w pliku build.gradle aplikacji. W tym przykładzie `com.fabrikam.fcmtutorial1app`jest to . 

        ![Określ nazwę pakietu](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Wybierz **pozycję Zarejestruj aplikację**. 
4. Wybierz **pozycję Pobierz plik google-services.json**, zapisz plik w folderze **aplikacji** projektu, a następnie wybierz pozycję **Dalej**. 

    ![Pobierz google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Wykonuj następujące **zmiany konfiguracji** projektu w Android Studio. 
    1.  W pliku build.gradle na&lt;poziomie&gt;projektu (project /build.gradle) dodaj następującą instrukcję do sekcji **zależności.** 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. W pliku build.gradle na&lt;poziomie&gt;/&lt;aplikacji&gt;(project app-module /build.gradle) dodaj następujące instrukcje do sekcji **zależności.** 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.8'
        implementation 'com.google.firebase:firebase-messaging:17.3.4'
        ```

    3. Dodaj następujący wiersz na końcu pliku build.gradle na poziomie aplikacji po sekcji zależności. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. Wybierz **pozycję Synchronizuj teraz** na pasku narzędzi. 
 
        ![zmiany konfiguracji build.gradle](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Wybierz **pozycję Dalej**. 
7. Wybierz **pozycję Pomiń ten krok**. 

    ![Pomiń ostatni krok](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. W konsoli usługi Firebase wybierz koło zębate dla projektu. Następnie wybierz pozycję **Ustawienia projektu**.

    ![Wybieranie ustawień projektu](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Jeśli plik google-services.json nie został pobrany do folderu **aplikacji** projektu Android Studio, możesz to zrobić na tej stronie. 
5. Przełącz się na kartę **Wiadomości w chmurze** u góry. 
6. Skopiuj i zapisz **klucz serwera** do późniejszego użycia. Ta wartość służy do konfigurowania koncentratora.
