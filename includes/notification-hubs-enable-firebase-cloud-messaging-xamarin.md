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
ms.openlocfilehash: 90ffebf5f3375e94545f82a95f5c240ad845bd94
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2019
ms.locfileid: "57908290"
---
1. Zaloguj się do [konsoli Firebase](https://firebase.google.com/console/). Utwórz nowy projekt Firebase, jeśli jeszcze go nie masz.
2. Po utworzeniu projektu wybierz pozycję **Dodaj Firebase do swojej aplikacji dla systemu Android**. 

    ![Dodawanie usługi Firebase do aplikacji systemu Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Na **Dodaj Firebase do aplikacji systemu Android** wykonaj następujące czynności: 
    1. Aby uzyskać **nazwa pakietu systemu Android**, wprowadź nazwę pakietu. Na przykład: `tutorials.tutoria1.xamarinfcmapp`. 

        ![Określ nazwę pakietu](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Wybierz **Rejestracja aplikacji**. 
4. Wybierz **pobierania pliku google-services.json**, Zapisz plik do **aplikacji** folderu projektu, a następnie wybierz **dalej**. 

    ![Pobieranie pliku google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
6. Wybierz **dalej** na stronie. 
7. Wybierz **Pomiń ten krok** na stronie. 

    ![Pomiń ostatni krok](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. W konsoli usługi Firebase wybierz koło zębate dla projektu. Następnie wybierz pozycję **Ustawienia projektu**.

    ![Wybieranie ustawień projektu](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Jeśli nie zostały pobrane **google-services.json** pliku, możesz zrobić tak na tej stronie. 
5. Przełącz się do **Cloud Messaging** kartę u góry. 
6. Skopiuj i Zapisz **klucz serwera** do późniejszego użycia. Ta wartość umożliwia konfigurowanie Centrum powiadomień.
