---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 675ad278cb8bdc0ced4eff3bd77572f44c9808fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68857457"
---
W tej sekcji zaktualizujesz kod w istniejącym projekcie zaplecza aplikacji mobilnych, aby wysyłać powiadomienie wypychanego za każdym razem, gdy nowy element zostanie dodany. Ten proces jest obsługiwany przez funkcję [szablonu](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) usługi Azure Notification Hubs, która umożliwia wypychanie między platformami. Różnych klientów są rejestrowane dla powiadomień wypychanych przy użyciu szablonów i jeden uniwersalny wypychanie można uzyskać do wszystkich platform klienta.

Wybierz jedną z poniższych procedur, która&mdash;pasuje do typu projektu zaplecza [.NET](#dotnet) lub [zaplecza Node.js](#nodejs).

### <a name="net-back-end-project"></a><a name="dotnet"></a>Projekt zaplecza platformy .NET

1. W programie Visual Studio kliknij prawym przyciskiem myszy projekt serwera. Następnie wybierz pozycję **Zarządzaj pakietami NuGet**. Wyszukaj `Microsoft.Azure.NotificationHubs`, a następnie wybierz pozycję **Zainstaluj**. Ten proces instaluje bibliotekę Centrum powiadomień do wysyłania powiadomień z zaplecza.
2. W projekcie serwera otwórz **TodoItemController.cs kontrolery** > **.** Następnie dodaj następujące instrukcje za pomocą:

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

3. W **PostTodoItem** metody, dodać następujący kod po wywołaniu **InsertAsync:**  

    ```csharp
    // Get the settings for the server project.
    HttpConfiguration config = this.Configuration;
    MobileAppSettingsDictionary settings =
        this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

    // Get the Notification Hubs credentials for the mobile app.
    string notificationHubName = settings.NotificationHubName;
    string notificationHubConnection = settings
        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

    // Create a new Notification Hub client.
    NotificationHubClient hub = NotificationHubClient
    .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

    // Send the message so that all template registrations that contain "messageParam"
    // receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
    Dictionary<string,string> templateParams = new Dictionary<string,string>();
    templateParams["messageParam"] = item.Text + " was added to the list.";

    try
    {
        // Send the push notification and log the results.
        var result = await hub.SendTemplateNotificationAsync(templateParams);

        // Write the success result to the logs.
        config.Services.GetTraceWriter().Info(result.State.ToString());
    }
    catch (System.Exception ex)
    {
        // Write the failure result to the logs.
        config.Services.GetTraceWriter()
            .Error(ex.Message, null, "Push.SendAsync Error");
    }
    ```

    Ten proces wysyła powiadomienie szablonu, który zawiera element. Tekst po wstawieniu nowego elementu.

4. Ponownie opublikuj projekt serwera.

### <a name="nodejs-back-end-project"></a><a name="nodejs"></a>Projekt zaplecza node.js

1. Skonfiguruj projekt wewnętrznej bazy danych.
2. Zastąp istniejący kod w pliku todoitem.js następującym kodem:

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
    promises = require('azure-mobile-apps/src/utilities/promises'),
    logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    table.insert(function (context) {
    // For more information about the Notification Hubs JavaScript SDK,
    // see https://aka.ms/nodejshubs.
    logger.info('Running TodoItem.insert');

    // Define the template payload.
    var payload = '{"messageParam": "' + context.item.text + '" }';  

    // Execute the insert. The insert returns the results as a promise.
    // Do the push as a post-execute action within the promise flow.
    return context.execute()
        .then(function (results) {
            // Only do the push if configured.
            if (context.push) {
                // Send a template notification.
                context.push.send(null, payload, function (error) {
                    if (error) {
                        logger.error('Error while sending push notification: ', error);
                    } else {
                        logger.info('Push notification sent successfully!');
                    }
                });
            }
            // Don't forget to return the results from the context.execute().
            return results;
        })
        .catch(function (error) {
            logger.error('Error while running context.execute: ', error);
        });
    });

    module.exports = table;  
    ```

    Ten proces wysyła powiadomienie szablonu, który zawiera item.text po wstawieniu nowego elementu.

3. Podczas edytowania pliku na komputerze lokalnym ponownie opublikuj projekt serwera.
