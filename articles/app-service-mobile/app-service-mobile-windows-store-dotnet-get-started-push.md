---
title: Dodawanie powiadomień wypychanych do aplikacji platformy uniwersalnej systemuśpiłan
description: Dowiedz się, jak używać aplikacji mobilnych usługi Azure App Service i centrów powiadomień platformy Azure do wysyłania powiadomień wypychanych do aplikacji platformy uniwersalnej systemu Windows (UWP).
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 6f200e9649a00bfe890d46f86e62404f1a7e844f
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366288"
---
# <a name="add-push-notifications-to-your-windows-app"></a>Dodawanie powiadomień wypychanych do aplikacji systemu Windows

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Omówienie

W tym samouczku dodasz powiadomienia wypychane do projektu [szybkiego startu systemu Windows,](app-service-mobile-windows-store-dotnet-get-started.md) aby powiadomienie wypychane było wysyłane do urządzenia za każdym razem, gdy rekord jest wstawiany.

Jeśli nie korzystasz z pobranego projektu serwera szybkiego startu, potrzebny będzie pakiet rozszerzenia powiadomień wypychanych. Aby uzyskać więcej informacji, zobacz [Praca z zestawem SDK serwera wewnętrznej bazy danych .NET dla aplikacji mobilnych platformy Azure.](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

## <a name="configure-a-notification-hub"></a><a name="configure-hub"></a>Konfigurowanie Centrum powiadomień

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Rejestrowanie aplikacji dla usługi powiadomień wypychanych

Musisz przesłać aplikację do sklepu Microsoft Store, a następnie skonfigurować projekt serwera do integracji z [usługami powiadomień wypychanych systemu Windows (WNS)](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) w celu wysłania wypychania.

1. W Eksploratorze rozwiązań programu Visual Studio kliknij prawym przyciskiem myszy projekt aplikacji platformy uniwersalnej systemu Windows, kliknij polecenie **Skojarzenie sklepu** > **z magazynem...**.

    ![Kojarzenie aplikacji ze sklepem Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)

2. W kreatorze kliknij przycisk **Dalej**, zaloguj się za pomocą konta Microsoft, wpisz nazwę aplikacji w **pozycji Rezerwuj nową nazwę aplikacji,** a następnie kliknij pozycję **Zarezerwuj**.
3. Po pomyślnym utworzeniu rejestracji aplikacji wybierz nową nazwę aplikacji, kliknij przycisk **Dalej**, a następnie kliknij przycisk **Skojarz**. Spowoduje to dodanie wymaganych informacji rejestracyjnych sklepu Microsoft Store do manifestu aplikacji.
4. Przejdź do [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/) i zaloguj się za pomocą konta Microsoft. Kliknij aplikację ze Sklepu Windows skojarzoną w poprzednim kroku.
5. Na stronie rejestracji zanotuj wartość w obszarze **Wpisy tajne aplikacji** i **identyfikator SID pakietu,** który będzie następnie używany do konfigurowania zaplecza aplikacji mobilnej.

    ![Kojarzenie aplikacji ze sklepem Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > Klucz tajny klienta i identyfikator SID pakietu są ważnymi poświadczeniami zabezpieczeń. Nie udostępniaj nikomu tych wartości ani nie rozpowszechniaj ich razem z aplikacją. **Identyfikator aplikacji** jest używany z kluczem tajnym do konfigurowania uwierzytelniania konta Microsoft.

[Usługa App Center](https://docs.microsoft.com/appcenter/sdk/push/uwp#prerequisite---register-your-app-for-windows-notification-services-wns) zawiera również instrukcje konfigurowania aplikacji platformy uniwersalnej systemu Windows dla powiadomień wypychanych.

## <a name="configure-the-backend-to-send-push-notifications"></a>Konfigurowanie wewnętrznej bazy danych do wysyłania powiadomień wypychanych

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a name="update-the-server-to-send-push-notifications"></a><a id="update-service"></a>Aktualizowanie serwera w celu wysyłania powiadomień wypychanych

Użyj poniższej procedury, która&mdash;odpowiada typowi projektu wewnętrznej bazy danych [.NET](#dotnet) lub [wewnętrznej bazy danych Node.js](#nodejs).

### <a name="net-backend-project"></a><a name="dotnet"></a>Projekt zaplecza .NET

1. W programie Visual Studio kliknij prawym przyciskiem myszy projekt serwera i kliknij polecenie **Zarządzaj pakietami NuGet**, wyszukaj witrynę Microsoft.Azure.NotificationHubs, a następnie kliknij polecenie **Zainstaluj**. Spowoduje to zainstalowanie biblioteki klienta centrum powiadomień.
2. Rozwiń **kontrolery**, otwórz TodoItemController.cs i dodaj następujące instrukcje za pomocą instrukcji:

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

    // Get the Notification Hubs credentials for the Mobile App.
    string notificationHubName = settings.NotificationHubName;
    string notificationHubConnection = settings
        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

    // Create the notification hub client.
    NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

    // Define a WNS payload
    var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
                            + item.Text + @"</text></binding></visual></toast>";
    try
    {
        // Send the push notification.
        var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

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

    Ten kod informuje centrum powiadomień o wysłaniu powiadomienia wypychanego po wstawieniu nowego elementu.

4. Ponownie opublikuj projekt serwera.

### <a name="nodejs-backend-project"></a><a name="nodejs"></a>Projekt wewnętrznej bazy danych Node.js
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

    // Define the WNS payload that contains the new item Text.
    var payload = "<toast><visual><binding template=\ToastText01\><text id=\"1\">"
                                + context.item.text + "</text></binding></visual></toast>";

    // Execute the insert.  The insert returns the results as a Promise,
    // Do the push as a post-execute action within the promise flow.
    return context.execute()
        .then(function (results) {
            // Only do the push if configured
            if (context.push) {
                // Send a WNS native toast notification.
                context.push.wns.sendToast(null, payload, function (error) {
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

    Spowoduje to wysłanie wyskakującego powiadomienia WNS zawierającego plik item.text po wstawieniu nowego elementu todo.

3. Podczas edytowania pliku na komputerze lokalnym ponownie opublikuj projekt serwera.

## <a name="add-push-notifications-to-your-app"></a><a id="update-app"></a>Dodawanie powiadomień wypychanych do aplikacji
Następnie aplikacja musi zarejestrować się w celu otrzymywanania powiadomień wypychanych podczas uruchamiania. Po włączeniu uwierzytelniania upewnij się, że użytkownik loguje się przed próbą zarejestrowania powiadomień wypychanych.

1. Otwórz **plik projektu App.xaml.cs** i dodaj `using` następujące instrukcje:

    ```csharp
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    ```

2. W tym samym pliku dodaj następującą definicję metody **InitNotificationsAsync** do klasy **aplikacji:**

    ```csharp
    private async Task InitNotificationsAsync()
    {
        // Get a channel URI from WNS.
        var channel = await PushNotificationChannelManager
            .CreatePushNotificationChannelForApplicationAsync();

        // Register the channel URI with Notification Hubs.
        await App.MobileService.GetPush().RegisterAsync(channel.Uri);
    }
    ```

    Ten kod pobiera ChannelURI dla aplikacji z usługi WNS, a następnie rejestruje ten ChannelURI z aplikacji mobilnej usługi App Service.

3. U góry **OnLaunched** obsługi zdarzeń w **App.xaml.cs**, dodać modyfikator **asynchronicznych** do definicji metody i dodać następujące wywołanie do nowej metody **InitNotificationsAsync,** jak w poniższym przykładzie:

    ```csharp
    protected async override void OnLaunched(LaunchActivatedEventArgs e)
    {
        await InitNotificationsAsync();

        // ...
    }
    ```

    Gwarantuje to, że krótkotrwałe ChannelURI jest rejestrowany za każdym razem, gdy aplikacja jest uruchamiana.

4. Odbuduj projekt aplikacji platformy uniwersalnej systemuśpiłnie. Aplikacja jest teraz gotowa do odbierania wyskakujących powiadomień.

## <a name="test-push-notifications-in-your-app"></a><a id="test"></a>Testowanie powiadomień wypychanych w aplikacji

[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a name="next-steps"></a><a id="more"></a>Następne kroki

Dowiedz się więcej o powiadomieniach wypychanych:

* [Jak korzystać z zarządzanego klienta dla aplikacji mobilnych platformy Azure](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications) Szablony zapewniają elastyczność wysyłania wieloplatformowych wypycheń i zlokalizowanych wypycheń. Dowiedz się, jak zarejestrować szablony.
* [Diagnozowanie problemów z powiadomieniami wypychanych](../notification-hubs/notification-hubs-push-notification-fixer.md) Istnieją różne powody, dla których powiadomienia mogą zostać usunięte lub nie trafiają do urządzeń. W tym temacie pokazano, jak analizować i dowiedzieć się główną przyczynę błędów powiadomień wypychanych.

Rozważ kontynuowanie jednego z następujących samouczków:

* [Dodawanie uwierzytelniania do aplikacji](app-service-mobile-windows-store-dotnet-get-started-users.md) Dowiedz się, jak uwierzytelniać użytkowników aplikacji przy użyciu dostawcy tożsamości.
* [Włączanie synchronizacji w trybie offline dla aplikacji](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) Dowiedz się, jak dodać obsługę aplikacji w trybie offline przy użyciu zaplecza aplikacji mobilnej. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacją mobilną &mdash; przeglądanie, dodawanie lub modyfikowanie danych &mdash; nawet w przypadku braku połączenia sieciowego.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
