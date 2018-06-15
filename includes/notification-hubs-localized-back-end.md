---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/02/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 752feca30fdca663aaf8bd88e6686781b9065682
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33836685"
---
W przypadku wysyłania powiadomień szablonu wystarczy stanowią zbiór właściwości. W tym scenariuszu zestaw właściwości zawiera zlokalizowanej wersji bieżącej wiadomości.

    {
        "News_English": "World News in English!",
        "News_French": "World News in French!",
        "News_Mandarin": "World News in Mandarin!"
    }



### <a name="send-notifications-using-a-c-console-app"></a>Wysyłanie powiadomień za pomocą aplikacji konsolowej C#
W tej sekcji przedstawiono sposób wysyłania powiadomień za pomocą aplikacji konsoli. Kod wysyła powiadomienia do urządzeń zarówno Sklepu Windows i iOS. Modyfikowanie `SendTemplateNotificationAsync` metody w aplikacji konsoli wcześniej utworzony z następującym kodem:

```csharp
private static async void SendTemplateNotificationAsync()
{
    // Define the notification hub.
    NotificationHubClient hub = 
        NotificationHubClient.CreateClientFromConnectionString(
            "<connection string with full access>", "<hub name>");

    // Sending the notification as a template notification. All template registrations that contain 
    // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications. 
    // This includes APNS, GCM, WNS, and MPNS template registrations.
    Dictionary<string, string> templateParams = new Dictionary<string, string>();

    // Create an array of breaking news categories.
    var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
    var locales = new string[] { "English", "French", "Mandarin" };

    foreach (var category in categories)
    {
        templateParams["messageParam"] = "Breaking " + category + " News!";

        // Sending localized News for each tag too...
        foreach( var locale in locales)
        {
            string key = "News_" + locale;

            // Your real localized news content would go here.
            templateParams[key] = "Breaking " + category + " News in " + locale + "!";
        }

        await hub.SendTemplateNotificationAsync(templateParams, category);
    }
}
```

Metoda SendTemplateNotificationAsync zapewnia zlokalizowanych część wiadomości, aby **wszystkie** urządzenia, niezależnie od platformy. Centrum powiadomień kompilacji, a następnie dostarcza poprawne ładunku natywnego na wszystkich urządzeniach, które subskrybuje z konkretnym elementem tag.

### <a name="sending-notification-with-mobile-services"></a>Wysyłanie powiadomień z usług Mobile Services
W harmonogramie Twojej usługi Mobile Services użyj następującego skryptu:

```csharp
var azure = require('azure');
var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
var notification = {
        "News_English": "World News in English!",
        "News_French": "World News in French!",
        "News_Mandarin", "World News in Mandarin!"
}
notificationHubService.send('World', notification, function(error) {
    if (!error) {
        console.warn("Notification successful");
    }
});
```

