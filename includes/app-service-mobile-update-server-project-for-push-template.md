---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: c664b089f316255fabc4c8dc36b291d7d63e6280
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59804791"
---
W tej sekcji należy zaktualizować kod w istniejącym projekcie zaplecza funkcji Mobile Apps do wysyłania powiadomień wypychanych za każdym razem, gdy zostanie dodany nowy element. Ten proces jest obsługiwana przez [szablonu](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) wypycha funkcji usługi Azure Notification Hubs, która umożliwia dla wielu platform. Różnych klientów są zarejestrowane dla powiadomień wypychanych przy użyciu szablonów i uzyskać jednej, uniwersalnej wypychania dla wszystkich platform klienta.

Wybierz jedną z poniższych procedur, które jest zgodny z typem projektu zaplecza&mdash;albo [zaplecze .NET](#dotnet) lub [zaplecza Node.js](#nodejs).

### <a name="dotnet"></a>Projekt zaplecza .NET

1. W programie Visual Studio kliknij prawym przyciskiem myszy projekt serwera. Następnie wybierz pozycję **Zarządzaj pakietami NuGet**. Wyszukaj `Microsoft.Azure.NotificationHubs`, a następnie wybierz pozycję **zainstalować**. Ten proces instaluje biblioteki usługi Notification Hubs do wysyłania powiadomień z zaplecza.
2. W projekcie serwera Otwórz **kontrolerów** > **TodoItemController.cs**. Następnie dodaj następujące instrukcje using:

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

3. W **PostTodoItem** metody, Dodaj następujący kod po wywołaniu **InsertAsync**:  

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

    Ten proces wysyła powiadomienia szablonu, który zawiera element. Tekst, gdy znajduje się nowy element.

4. Ponownie opublikować projekt serwera.

### <a name="nodejs"></a>Projekt zaplecza node.js

1. Jeśli użytkownik jeszcze tego nie zrobiono, [pobrać projekt zaplecza szybkiego startu](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart), lub użyj innego [edytora online w witrynie Azure portal](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. Zastąp istniejący kod w todoitem.js następującym kodem:

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

    Ten proces wysyła powiadomienie szablon zawierający item.text, gdy znajduje się nowy element.

3. Podczas edytowania pliku na komputerze lokalnym, należy ponownie opublikować projekt serwera.
