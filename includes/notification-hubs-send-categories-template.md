---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 11/07/2019
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 48907713082ebb1008ad963121671b36af7f2731
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228180"
---
W tej sekcji wyślesz najważniejsze wiadomości w formie oznaczonych tagami powiadomień szablonu z aplikacji konsoli .NET.

1. W programie Visual Studio utwórz nową aplikację konsoli języka Visual C#:
    1. On the menu, select **File** > **New** > **Project**.
    1. In **Create a new project**, select **Console App (.NET Framework)** for C# in the list of templates, and select **Next**.
    1. Enter a name for the app.
    1. For **Solution**, choose **Add to solution**, and select **Create** to create the project.

1. Select **Tools** > **NuGet Package Manager** > **Package Manager Console** and then, in the console window, run the following command:

   ```powershell
   Install-Package Microsoft.Azure.NotificationHubs
   ```

   This action adds a reference to the Azure Notification Hubs SDK by using the [Microsoft.Azure.NotificationHubs] package.

1. Open the *Program.cs* file, and add the following `using` statement:

   ```csharp
   using Microsoft.Azure.NotificationHubs;
   ```

1. W klasie `Program` dodaj następującą metodę lub zastąp ją, jeśli już istnieje:

    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Apple requires the apns-push-type header for all requests
        var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM/FCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```

   Ten kod wysyła powiadomienie szablonu dla każdego z sześciu tagów w tablicy ciągów. Użycie tagów pozwala zagwarantować, że urządzenia otrzymają powiadomienia tylko dla zarejestrowanych kategorii.

1. W poprzednim kodzie zastąp symbole zastępcze `<hub name>` i `<connection string with full access>` wartościami: nazwą centrum powiadomień i parametrami połączenia *DefaultFullSharedAccessSignature* z pulpitu nawigacyjnego centrum powiadomień.

1. In the `Main()` method, add the following lines:

   ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

1. Skompiluj aplikację konsoli.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: https://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft.Azure.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
