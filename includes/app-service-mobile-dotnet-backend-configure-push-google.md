---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 3c6147cc24f285fc32dce145e594f743c9633c20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68857459"
---
Użyj procedury zgodnej z typem&mdash;projektu zaplecza .NET lub [zaplecza](#dotnet) [Node.js](#nodejs).

### <a name="net-back-end-project"></a><a name="dotnet"></a>Projekt zaplecza platformy .NET

1. W programie Visual Studio kliknij prawym przyciskiem myszy projekt serwera, a następnie kliknij polecenie **Zarządzaj pakietami NuGet**. Wyszukaj `Microsoft.Azure.NotificationHubs`, a następnie kliknij przycisk **Zainstaluj**. Spowoduje to zainstalowanie biblioteki klienta centrum powiadomień.
2. W folderze Kontrolery otwórz TodoItemController.cs i dodaj `using` następujące instrukcje:

    ```csharp
    using Microsoft.Azure.Mobile.Server.Config;
    using Microsoft.Azure.NotificationHubs;
    ```

3. Zastąp metodę `PostTodoItem` poniższym kodem:  

    ```csharp
    public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
    {
        TodoItem current = await InsertAsync(item);
        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;

        MobileAppSettingsDictionary settings =
            this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Android payload
        var androidNotificationPayload = "{ \"data\" : {\"message\":\"" + item.Text + "\"}}";

        try
        {
            // Send the push notification and log the results.
            var result = await hub.SendGcmNativeNotificationAsync(androidNotificationPayload);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }
        return CreatedAtRoute("Tables", new { id = current.Id }, current);
    }
    ```

4. Ponownie opublikuj projekt serwera.

### <a name="nodejs-back-end-project"></a><a name="nodejs"></a>Projekt zaplecza node.js

1. Skonfiguruj projekt wewnętrznej bazy danych.
2. Zastąp istniejący kod w pliku todoitem.js na następujące:

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
    promises = require('azure-mobile-apps/src/utilities/promises'),
    logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    table.insert(function (context) {
    // For more information about the Notification Hubs JavaScript SDK,
    // see https://aka.ms/nodejshubs
    logger.info('Running TodoItem.insert');

    // Define the GCM payload.
    var payload = {
        "data": {
            "message": context.item.text
        }
    };

    // Execute the insert.  The insert returns the results as a Promise,
    // Do the push as a post-execute action within the promise flow.
    return context.execute()
        .then(function (results) {
            // Only do the push if configured
            if (context.push) {
                // Send a GCM native notification.
                context.push.gcm.send(null, payload, function (error) {
                    if (error) {
                        logger.error('Error while sending push notification: ', error);
                    } else {
                        logger.info('Push notification sent successfully!');
                    }
                });
            }
            // Don't forget to return the results from the context.execute()
            return results;
        })
        .catch(function (error) {
            logger.error('Error while running context.execute: ', error);
        });
    });

    module.exports = table;
    ```

    Spowoduje to wysłanie powiadomienia GCM, który zawiera item.text po wstawieniu nowego elementu todo.

3. Podczas edytowania pliku na komputerze lokalnym ponownie opublikuj projekt serwera.
