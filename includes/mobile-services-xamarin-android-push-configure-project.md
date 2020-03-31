---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 69dc0e1c14bc88cdbf0aa48700f95058ba759cc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183737"
---
1. W widoku Rozwiązanie (lub **Eksplorator rozwiązania** w programie Visual Studio) kliknij prawym przyciskiem myszy folder **Składniki,** kliknij polecenie **Uzyskaj więcej składników...**, wyszukaj składnik Klienta usługi Google Cloud **Messaging** i dodaj go do projektu.
2. Otwórz plik projektu ToDoActivity.cs i dodaj do klasy następującą instrukcję za pomocą instrukcji:

    ```csharp
    using Gcm.Client;
    ```

3. W klasie **ToDoActivity** dodaj następujący nowy kod: 

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

    Dzięki temu można uzyskać dostęp do wystąpienia klienta mobilnego z procesu obsługi wypychania.
4. Dodaj następujący kod do **OnCreate** metody, po **MobileServiceClient** jest tworzony:

    ```csharp
    // Set the current instance of TodoActivity.
    instance = this;

    // Make sure the GCM client is set up correctly.
    GcmClient.CheckDevice(this);
    GcmClient.CheckManifest(this);

    // Register the app for push notifications.
    GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);
    ```

Twoja **ToDoActivity** jest teraz przygotowana do dodawania powiadomień wypychanych.
