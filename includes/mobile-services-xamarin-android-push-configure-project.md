---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 69dc0e1c14bc88cdbf0aa48700f95058ba759cc0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183737"
---
1. W widoku Solution (lub **Eksploratora rozwiązań** w programie Visual Studio), kliknij prawym przyciskiem myszy **składniki** folderu, kliknij przycisk **Uzyskaj więcej składników...** , wyszukaj **Google Cloud Messaging Client** składników i dodaj go do projektu.
2. Otwórz plik projektu o nazwie ToDoActivity.cs i dodaj następujące za pomocą instrukcji do klasy:

    ```csharp
    using Gcm.Client;
    ```

3. W **ToDoActivity** klasy, Dodaj następujący kod, nowe: 

    ```csharp
    // Create a new instance field for this activity.
    static ToDoActivity instance = new ToDoActivity();

    // Return the current activity instance.
    public static ToDoActivity CurrentActivity
    {
        get
        {
            return instance;
        }
    }
    // Return the Mobile Services client.
    public MobileServiceClient CurrentClient
    {
        get
        {
            return client;
        }
    }
    ```

    Dzięki temu można uzyskać dostęp do wystąpienia klienta mobilnego z procesu wypychania obsługi usługi.
4. Dodaj następujący kod do **OnCreate** metoda po **MobileServiceClient** zostanie utworzony:

    ```csharp
    // Set the current instance of TodoActivity.
    instance = this;

    // Make sure the GCM client is set up correctly.
    GcmClient.CheckDevice(this);
    GcmClient.CheckManifest(this);

    // Register the app for push notifications.
    GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);
    ```

Twoje **ToDoActivity** jest teraz gotowy do dodawania powiadomień wypychanych.
