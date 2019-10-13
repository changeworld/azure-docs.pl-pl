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
ms.openlocfilehash: 299f92484000cb5c59291a5af87f24d89a771fee
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72296746"
---
W tej sekcji wyślesz najważniejsze wiadomości w formie oznaczonych tagami powiadomień szablonu z aplikacji konsoli .NET.

1. W programie Visual Studio utwórz nową aplikację konsoli języka Visual C#:
    1. W menu wybierz kolejno pozycje **plik** > **Nowy** > **projekt**.
    1. W obszarze **Utwórz nowy projekt**wybierz pozycję **aplikacja konsoli (.NET Framework)** dla C# programu na liście szablonów, a następnie wybierz pozycję **dalej**.
    1. Wprowadź nazwę aplikacji.
    1. W polu **rozwiązanie**wybierz opcję **Dodaj do rozwiązania**, a następnie wybierz pozycję **Utwórz** , aby utworzyć projekt.

1. Wybierz pozycję **narzędzia** > **menedżer pakietów NuGet** > **konsola Menedżera pakietów** , a następnie w oknie konsoli Uruchom następujące polecenie:

   ```powershell
   Install-Package Microsoft.Azure.NotificationHubs
   ```

   Ta akcja dodaje odwołanie do zestawu Azure Notification Hubs SDK przy użyciu pakietu [Microsoft. Azure. NotificationHubs] .

1. Otwórz plik *program.cs* i Dodaj następującą instrukcję `using`:

   ```csharp
   using Microsoft.Azure.NotificationHubs;
   ```

1. W klasie `Program` dodaj następującą metodę lub zastąp ją, jeśli już istnieje:

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

1. W poprzednim kodzie zastąp symbole zastępcze `<hub name>` i `<connection string with full access>` wartościami: nazwą centrum powiadomień i parametrami połączenia *DefaultFullSharedAccessSignature* z pulpitu nawigacyjnego centrum powiadomień.

1. W metodzie `Main()` Dodaj następujące wiersze:

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
[Microsoft. Azure. NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
