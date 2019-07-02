---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7e047ba57a61d2f327544ec795f640f5066962f6
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509892"
---
1. Zaloguj się do [konsoli Firebase](https://firebase.google.com/console/). Utwórz nowy projekt Firebase, jeśli jeszcze go nie masz.
2. Po utworzeniu projektu wybierz pozycję **Dodaj Firebase do swojej aplikacji dla systemu Android**. 

    ![Dodawanie usługi Firebase do aplikacji systemu Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. Na **Dodaj Firebase do aplikacji systemu Android** strony, wykonaj następujące czynności: 
1. 
    1. Aby uzyskać **nazwa pakietu systemu Android**, wprowadź nazwę pakietu. Na przykład: `tutorials.tutoria1.xamarinfcmapp`. 

        ![Określ nazwę pakietu](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)

2. Wybierz **Rejestracja aplikacji**. 
1. 
1. Wybierz **pobierania pliku google-services.json**. Następnie zapisz plik do **aplikacji** folderu projektu i wybierz **dalej**. 

    ![Pobieranie pliku google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)

6. Wybierz opcję **Dalej**. 
7. Wybierz **Pomiń ten krok**. 

    ![Pomiń ostatni krok](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)

8. W konsoli usługi Firebase wybierz koło zębate dla projektu. Następnie wybierz pozycję **Ustawienia projektu**.

    ![Wybieranie ustawień projektu](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)

4. Jeśli nie zostały pobrane **google-services.json** pliku, należy pobrać go na tej stronie. 

1. Przełącz się do **Cloud Messaging** kartę u góry. 

1. Skopiuj i Zapisz **klucz starsza wersja serwera** do późniejszego użycia. Ta wartość umożliwia konfigurowanie Centrum powiadomień.
