---
title: Dodawanie powiadomień wypychanych do aplikacji uniwersalnych platformy Windows (UWP) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure App Service Mobile Apps i usługi Azure Notification Hubs wysyłać powiadomienia wypychane do aplikacji platformy uniwersalnej Windows (UWP).
services: app-service\mobile,notification-hubs
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: 7efd853e7b66933cac811625d7510139864f41f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128036"
---
# <a name="add-push-notifications-to-your-windows-app"></a>Dodawanie powiadomień wypychanych do aplikacji Windows

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Omówienie

W ramach tego samouczka, możesz dodać powiadomienia wypychane do [Windows — szybki start](app-service-mobile-windows-store-dotnet-get-started.md) projektu, dzięki czemu jest wysyłane powiadomienie push do urządzenia, za każdym razem, gdy rekord zostanie wstawiona.

Jeśli nie używasz projektu serwera pobranego — szybki start, konieczne będzie pakiet rozszerzenia powiadomień wypychanych. Zobacz [pracy z zestawem SDK serwera zaplecza platformy .NET dla usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) Aby uzyskać więcej informacji.

## <a name="configure-hub"></a>Konfigurowanie Centrum powiadomień

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Rejestrowanie aplikacji dla usługi powiadomień wypychanych

Musisz przesłać aplikacji Microsoft Store, a następnie skonfigurować Projekt serwera do integracji z [Windows usługi powiadomień WNS](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) do wysyłania wypychanych.

1. W Eksploratorze rozwiązań w usłudze Visual Studio, kliknij prawym przyciskiem myszy projekt aplikacji platformy uniwersalnej systemu Windows, kliknij przycisk **Store** > **Skojarz aplikację ze Store...** .

    ![Skojarz aplikację z Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)

2. W kreatorze kliknij pozycję **dalej**, zaloguj się przy użyciu swojego konta Microsoft, wpisz nazwę swojej aplikacji w **Zarezerwuj nazwę nowej aplikacji**, następnie kliknij przycisk **rezerwy**.
3. Po pomyślnym utworzeniu rejestracji aplikacji, wybierz nazwę nowej aplikacji, kliknij przycisk **dalej**, a następnie kliknij przycisk **skojarzyć**. Spowoduje to dodanie wymaganych informacji dotyczących rejestracji Microsoft Store do manifestu aplikacji.
4. Przejdź do [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/) i zaloguj się przy użyciu konta Microsoft. Kliknij aplikację Windows Store, którą skojarzone w poprzednim kroku.
5. Na stronie rejestracji, zanotuj wartość w obszarze **wpisów tajnych aplikacji** i **identyfikator SID pakietu**, który następnie będzie używany do konfigurowania zaplecza aplikacji mobilnej.

    ![Skojarz aplikację z Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > Klucz tajny klienta i identyfikator SID pakietu są ważnymi poświadczeniami zabezpieczeń. Nie udostępniaj nikomu tych wartości ani nie rozpowszechniaj ich razem z aplikacją. **Identyfikator aplikacji** jest używany przy użyciu klucza tajnego do konfigurowania uwierzytelniania Account firmy Microsoft.

[App Center](https://docs.microsoft.com/appcenter/sdk/push/uwp#prerequisite---register-your-app-for-windows-notification-services-wns) zawiera również instrukcje dotyczące konfigurowania aplikacji platformy UWP dla powiadomień wypychanych.

## <a name="configure-the-backend-to-send-push-notifications"></a>Konfigurowanie zaplecza służącego do wysyłania powiadomień wypychanych

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a id="update-service"></a>Zaktualizuj serwer do wysyłania powiadomień wypychanych

Użyj poniższej procedury, który pasuje do typu projektu wewnętrznej bazy danych&mdash;albo [zaplecza platformy .NET](#dotnet) lub [zaplecze środowiska Node.js](#nodejs).

### <a name="dotnet"></a>Projekt zaplecza platformy .NET

1. W programie Visual Studio, kliknij prawym przyciskiem myszy projekt serwera, a następnie kliknij przycisk **Zarządzaj pakietami NuGet**, wyszukaj Microsoft.Azure.NotificationHubs, a następnie kliknij przycisk **zainstalować**. Spowoduje to zainstalowanie biblioteki klienta usługi Notification Hubs.
2. Rozwiń **kontrolerów**, otwórz TodoItemController.cs i dodaj następujące za pomocą instrukcji:

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

    Ten kod zawiera informacje dla Centrum powiadomień wysyłać powiadomienia wypychane w przypadku, gdy nowy element jest wstawiania.

4. Ponownie opublikować projekt serwera.

### <a name="nodejs"></a>Projekt zaplecza node.js
1. Jeśli użytkownik jeszcze tego nie zrobiono, [pobieranie projektu Szybki Start](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) lub użyj innego [edytora online w witrynie Azure portal](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. Zastąp istniejący kod w pliku todoitem.js następujących czynności:

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

    Spowoduje to wysłanie WNS wyskakujące powiadomienie zawiera item.text, gdy znajduje się nowy element zadania.

3. Podczas edytowania pliku na komputerze lokalnym, należy ponownie opublikować projekt serwera.

## <a id="update-app"></a>Dodawanie powiadomień wypychanych do aplikacji
Następnie aplikacja musi zarejestrować na potrzeby powiadomień wypychanych przy uruchamianiu. Gdy już po włączeniu uwierzytelniania, upewnij się, że zalogowaniu się użytkownika przed podjęciem próby rejestracji powiadomień wypychanych.

1. Otwórz **App.xaml.cs** pliku projektu i dodaj następującą `using` instrukcji:

    ```csharp
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    ```

2. W tym samym pliku Dodaj następujący kod **InitNotificationsAsync** definicji metody, aby **aplikacji** klasy:

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

    Ten kod pobiera identyfikator ChannelURI w aplikacji z usługi WNS, a następnie rejestruje ten identyfikator ChannelURI w usłudze App Service Mobile App.

3. W górnej części **OnLaunched** programu obsługi zdarzeń w **App.xaml.cs**, Dodaj **async** modyfikatora definicję metody i dodaj następujące wywołanie do nowej  **InitNotificationsAsync** metody, jak w poniższym przykładzie:

    ```csharp
    protected async override void OnLaunched(LaunchActivatedEventArgs e)
    {
        await InitNotificationsAsync();

        // ...
    }
    ```

    Gwarantuje to, że krótkotrwałe identyfikator ChannelURI jest zarejestrowany w każdym razem, gdy aplikacja zostanie uruchomiona.

4. Ponownie skompiluj projekt aplikacji platformy uniwersalnej systemu Windows. Aplikacja jest teraz gotowa do odbierania wyskakujących powiadomień.

## <a id="test"></a>Testowych powiadomień push w aplikacji

[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a id="more"></a>Następne kroki

Dowiedz się więcej na temat powiadomień wypychanych:

* [Jak używać zarządzanego klienta usługi Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications) Szablony zapewniają elastyczność i możliwość wysyłania wypchnięć dla wielu platform i zlokalizowane wypchnięć. Dowiedz się, jak zarejestrować szablony.
* [Diagnozowanie problemów z powiadomień wypychanych](../notification-hubs/notification-hubs-push-notification-fixer.md) istnieją różne powody, dlaczego powiadomienia porzucane może lub nie znajdą się na urządzeniach. W tym temacie dowiesz się, jak analizować i ustalić główną przyczynę niepowodzenia powiadomień wypychanych.

Należy wziąć pod uwagę przejściem do jednej z następujących samouczków:

* [Dodawanie uwierzytelniania do aplikacji](app-service-mobile-windows-store-dotnet-get-started-users.md) Dowiedz się, jak uwierzytelniać użytkowników aplikacji przy użyciu dostawcy tożsamości.
* [Włączanie synchronizacji offline dla aplikacji](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) Dowiedz się, jak dodać obsługę trybu offline aplikacji przy użyciu zaplecza aplikacji mobilnej. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacją mobilną &mdash; przeglądanie, dodawanie lub modyfikowanie danych &mdash; nawet w przypadku braku połączenia sieciowego.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
