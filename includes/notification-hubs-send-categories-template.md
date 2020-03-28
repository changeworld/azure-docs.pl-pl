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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74228180"
---
W tej sekcji wyślesz najważniejsze wiadomości w formie oznaczonych tagami powiadomień szablonu z aplikacji konsoli .NET.

1. W programie Visual Studio utwórz nową aplikację konsoli języka Visual C#:
    1. W menu wybierz polecenie **Plik** > **nowego** > **projektu**.
    1. W **obszarze Tworzenie nowego projektu**wybierz pozycję Aplikacja konsoli **(.NET Framework)** dla języka C# na liście szablonów i wybierz pozycję **Dalej**.
    1. wprowadź nazwę aplikacji.
    1. W obszarze **Rozwiązanie**wybierz pozycję **Dodaj do rozwiązania**i wybierz pozycję **Utwórz,** aby utworzyć projekt.

1. Wybierz **pozycję Narzędzia** > **NuGet Package Manager** > **Package Manager Console,** a następnie w oknie konsoli uruchom następujące polecenie:

   ```powershell
   Install-Package Microsoft.Azure.NotificationHubs
   ```

   Ta akcja dodaje odwołanie do zestawu SDK usługi Azure Notification Hubs przy użyciu pakietu [Microsoft.Azure.NotificationHubs.]

1. Otwórz plik *Program.cs* i dodaj następującą `using` instrukcję:

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

1. W `Main()` metodzie dodaj następujące wiersze:

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
[Witryna Microsoft.Azure.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
