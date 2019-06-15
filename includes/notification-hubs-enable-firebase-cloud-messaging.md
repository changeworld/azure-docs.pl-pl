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
ms.openlocfilehash: 5f919a04b47aa6fdef9500f3d7e6bef4ddaa239e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66140265"
---
1. Zaloguj się do [konsoli Firebase](https://firebase.google.com/console/). Utwórz nowy projekt Firebase, jeśli jeszcze go nie masz.
2. Po utworzeniu projektu wybierz pozycję **Dodaj Firebase do swojej aplikacji dla systemu Android**. 

    ![Dodawanie usługi Firebase do aplikacji systemu Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Na **Dodaj Firebase do aplikacji systemu Android** wykonaj następujące czynności: 
    1. Dla **nazwa pakietu systemu Android**, skopiuj wartość Twojej **applicationId** w Twojej aplikacji **build.gradle** pliku. W tym przykładzie przedstawiono w nim `com.fabrikam.fcmtutorial1app`. 

        ![Określ nazwę pakietu](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Wybierz **Rejestracja aplikacji**. 
4. Wybierz **pobierania pliku google-services.json**, Zapisz plik do **aplikacji** folderu projektu, a następnie wybierz **dalej**. 

    ![Pobieranie pliku google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Wprowadź następujące **zmiany konfiguracji** do projektu w programie Android Studio. 
    1.  W swojej **na poziomie projektu build.gradle** pliku (&lt;projektu&gt;/build.gradle), dodaj następującą instrukcję, aby **zależności** sekcji. 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. W swojej **build.gradle na poziomie aplikacji** pliku (&lt;projektu&gt;/&lt;modułu aplikacji&gt;/build.gradle), dodaj następującą instrukcję, aby  **zależności** sekcji. 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.1'
        ```

    3. Dodaj następujący wiersz na końcu **build.gradle na poziomie aplikacji** plik po sekcji dependencies. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. Wybierz **Synchronizuj teraz*** na pasku narzędzi. 
 
        ![zmiany konfiguracji Build.gradle](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Wybierz **dalej** na stronie. 
7. Wybierz **Pomiń ten krok** na stronie. 

    ![Pomiń ostatni krok](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)1. 
8. W konsoli usługi Firebase wybierz koło zębate dla projektu. Następnie wybierz pozycję **Ustawienia projektu**.

    ![Wybieranie ustawień projektu](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Jeśli nie zostały pobrane **google-services.json** mezzanine do **aplikacji** folderu projektu w programie Android Studio, możesz zrobić tak na tej stronie. 
5. Przełącz się do **Cloud Messaging** kartę u góry. 
6. Skopiuj i Zapisz **klucz serwera** do późniejszego użycia. Ta wartość umożliwia konfigurowanie Centrum powiadomień.
