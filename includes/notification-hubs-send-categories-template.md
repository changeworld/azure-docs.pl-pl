---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/30/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: f0ff729084d194ff2e05e89eadc45782f775b1c5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66156747"
---
W tej sekcji wyślesz najważniejsze wiadomości w formie oznaczonych tagami powiadomień szablonu z aplikacji konsoli .NET. 

1. W programie Visual Studio, tworzenie nowej wizualizacji C# konsoli aplikacji:. W menu, wybierz **pliku** > **New** > **projektu**.
    b. Rozwiń **Visual C#** i wybierz **pulpitu Windows**. 
    c. Wybierz **Aplikacja konsoli (.NET Framework)** na liście szablonów. 
    d. Wprowadź **nazwa** dla aplikacji. 
    e. Wybierz **folderu** dla aplikacji.
    f. Wybierz **OK** do tworzenia projektu. 
2. W menu głównym programu Visual Studio wybierz **narzędzia** > **Menedżera pakietów NuGet** > **Konsola Menedżera pakietów** a następnie w oknie konsoli Wprowadź następujący ciąg:
   
    ```
    Install-Package Microsoft.Azure.NotificationHubs
    ```
   
3. Kliknij przycisk **Wprowadź**.  
    Ta akcja spowoduje dodanie odwołania do zestawu SDK usługi Azure Notification Hubs z użyciem [Pakiet NuGet Microsoft.Azure.Notification Hubs].

4. Otwórz plik Program.cs i dodaj następującą instrukcję `using`:
   
    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

5. W klasie `Program` dodaj następującą metodę lub zastąp ją, jeśli już istnieje:
   
    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```   
   
    Ten kod wysyła powiadomienie szablonu dla każdego z sześciu tagów w tablicy ciągów. Użycie tagów pozwala zagwarantować, że urządzenia otrzymają powiadomienia tylko dla zarejestrowanych kategorii.

5. W poprzednim kodzie zastąp symbole zastępcze `<hub name>` i `<connection string with full access>` wartościami: nazwą centrum powiadomień i parametrami połączenia *DefaultFullSharedAccessSignature* z pulpitu nawigacyjnego centrum powiadomień.

6. W metodzie **Main** dodaj następujące wiersze:
   
    ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

7. Skompiluj aplikację konsoli.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: https://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Pakiet NuGet Microsoft.Azure.Notification Hubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
