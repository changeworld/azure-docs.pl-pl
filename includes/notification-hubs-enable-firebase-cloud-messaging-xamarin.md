---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/01/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 45bdd569741dc13181bcaf9e8587a02b3d02c621
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68728859"
---
1. Zaloguj się do [konsoli Firebase](https://firebase.google.com/console/). Utwórz nowy projekt Firebase, jeśli jeszcze go nie masz.
2. Po utworzeniu projektu wybierz pozycję **Dodaj Firebase do swojej aplikacji dla systemu Android**. 

    ![Dodawanie usługi Firebase do aplikacji systemu Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. Na stronie **Dodaj firebase do aplikacji na Androida,** podejmij następujące kroki: 
    1. W przypadku **nazwy pakietu android**wprowadź nazwę pakietu. Na przykład: `tutorials.tutoria1.xamarinfcmapp`. 

        ![Określ nazwę pakietu](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Wybierz **pozycję Zarejestruj aplikację**.  
    1. Wybierz **pobierz google-services.json**. Następnie zapisz plik w folderze projektu i wybierz przycisk **Dalej**. Jeśli projekt programu Visual Studio nie został jeszcze utworzony, można wykonać ten krok po utworzeniu projektu. 

        ![Pobierz google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
    6. Wybierz **pozycję Dalej**. 
    7. Wybierz **pozycję Pomiń ten krok**. 

        ![Pomiń ostatni krok](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. W konsoli usługi Firebase wybierz koło zębate dla projektu. Następnie wybierz pozycję **Ustawienia projektu**.

    ![Wybieranie ustawień projektu](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Jeśli plik **google-services.json** nie został pobrany, możesz go pobrać na tej stronie. 

    ![Pobierz plik google-services.json z zakładki Ogólne](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-services-json-general-page.png)
1. Przełącz się na kartę **Wiadomości w chmurze** u góry. Skopiuj i zapisz **klucz serwera** do późniejszego użycia. Ta wartość służy do konfigurowania centrum powiadomień.

    ![Kopiowanie klucza serwera](./media/notification-hubs-enable-firebase-cloud-messaging/server-key.png)
