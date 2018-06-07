---
title: Dodawanie powiadomień wypychanych do aplikacji systemu Windows platformy Uniwersalnej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wysyłać powiadomienia wypychane do aplikacji systemu Windows platformy Uniwersalnej za pomocą usługi Azure App Service Mobile Apps i usługi Azure Notification Hubs.
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
ms.openlocfilehash: 9e3ed6d19b0f830923745ad0263c5c4f920c0f51
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597862"
---
# <a name="add-push-notifications-to-your-windows-app"></a>Dodawanie powiadomień wypychanych do aplikacji systemu Windows
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Przegląd
W tym samouczku, dodawanie powiadomień wypychanych do [szybki start systemu Windows](app-service-mobile-windows-store-dotnet-get-started.md) projektu, aby powiadomienia wypychane są wysyłane do urządzenia, za każdym razem, gdy wstawieniu rekordu.

Jeśli nie używasz Projekt serwera pobrany szybki start, konieczne będzie pakiet rozszerzenia powiadomień wypychanych. Zobacz [pracować z serwera wewnętrznej bazy danych .NET SDK usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) Aby uzyskać więcej informacji.

## <a name="configure-hub"></a>Konfigurowanie Centrum powiadomień
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Rejestrowanie aplikacji dla usługi powiadomień wypychanych
Należy przesłać aplikacji Microsoft Store, a następnie skonfigurować Projekt serwera do integracji z usługi WNS (Windows Notification) do wysyłania wypychania.

1. W Eksploratorze rozwiązań programu Visual Studio, kliknij prawym przyciskiem myszy projekt aplikacji platformy uniwersalnej systemu Windows, kliknij przycisk **magazynu** > **Skojarz aplikację ze sklepem...** .

    ![Skojarz aplikację ze sklepu firmy Microsoft](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)
2. W kreatorze kliknij **dalej**, zaloguj się przy użyciu konta Microsoft, wpisz nazwę aplikacji w **Zarezerwuj nazwę nowej aplikacji**, następnie kliknij przycisk **rezerwy**.
3. Po pomyślnym utworzeniu rejestracji aplikacji, wybierz nową nazwę aplikacji, kliknij przycisk **dalej**, a następnie kliknij przycisk **skojarzyć**. Spowoduje to dodanie wymaganych informacji o rejestracji Microsoft Store manifest aplikacji.
4. Przejdź do [Centrum deweloperów systemu Windows](https://dev.windows.com/en-us/overview), logowanie przy użyciu konta Microsoft, kliknij przycisk nowej rejestracji aplikacji w **Moje aplikacje**, następnie rozwiń węzeł **usług**  >   **Powiadomienia wypychane**.
5. W **powiadomienia wypychane** kliknij przycisk **witryna usług Live** w obszarze **Microsoft Azure Mobile Services**.
6. Na stronie rejestracji Zanotuj wartość w kluczu **klucze tajne aplikacji** i **identyfikatora SID pakietu**, który następnie będzie używane do konfigurowania zaplecza aplikacji mobilnej.

    ![Skojarz aplikację ze sklepu firmy Microsoft](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > Klucz tajny klienta i identyfikator SID pakietu są ważnymi poświadczeniami zabezpieczeń. Nie udostępniaj nikomu tych wartości ani nie rozpowszechniaj ich razem z aplikacją. **Identyfikator aplikacji** jest używany z klucza tajnego do konfigurowania uwierzytelniania Account firmy Microsoft.
   >
   >

## <a name="configure-the-backend-to-send-push-notifications"></a>Konfigurowanie zaplecza do wysyłania powiadomień wypychanych
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a id="update-service"></a>Zaktualizuj serwer do wysyłania powiadomień wypychanych
Poniższa procedura odpowiadający danemu typowi projektu zaplecza&mdash;albo [zaplecza .NET](#dotnet) lub [zaplecza Node.js](#nodejs).

### <a name="dotnet"></a>Projektu zaplecza .NET
1. W programie Visual Studio, kliknij prawym przyciskiem myszy projekt serwera, a następnie kliknij przycisk **Zarządzaj pakietami NuGet**, wyszukaj Microsoft.Azure.NotificationHubs, a następnie kliknij przycisk **zainstalować**. Spowoduje to zainstalowanie biblioteki klienta usługi Notification Hubs.
2. Rozwiń węzeł **kontrolerów**, otwórz TodoItemController.cs i dodaj następujące instrukcje using:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;
3. W **PostTodoItem** metody, Dodaj następujący kod po wywołaniu **InsertAsync**:

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

    Ten kod informuje Centrum powiadomień do wysyłania powiadomień wypychanych po Wstawianie nowego elementu.
4. Ponownie opublikować projekt serwera.

### <a name="nodejs"></a>Projektu zaplecza node.js
1. Jeśli jeszcze tego nie zrobiono, [pobieranie projektu Szybki Start](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) lub użyj innego [edytora online w portalu Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. Zastąp istniejący kod w pliku todoitem.js następujące czynności:

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');

        var table = azureMobileApps.table();

        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK,
        // see http://aka.ms/nodejshubs
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

    To wysyła wyskakujących powiadomień WNS zawierający item.text po wstawieniu nowe zadanie do wykonania.
3. Podczas edytowania pliku na komputerze lokalnym, należy ponownie opublikować projekt serwera.

## <a id="update-app"></a>Dodawanie powiadomień wypychanych do aplikacji
Następnie zarejestrować dla powiadomień wypychanych przy uruchamianiu aplikacji. Jeśli już zostało włączone uwierzytelnianie, upewnij się, że zalogowaniu się użytkownika przed podjęciem próby rejestracji powiadomień wypychanych.

1. Otwórz **App.xaml.cs** pliku projektu i dodaj następującą `using` instrukcji:

        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
2. W tym samym pliku Dodaj następujące **InitNotificationsAsync** definicję metody do **aplikacji** klasy:

        private async Task InitNotificationsAsync()
        {
            // Get a channel URI from WNS.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register the channel URI with Notification Hubs.
            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }

    Ten kod pobiera ChannelURI dla aplikacji z usługi WNS, a następnie rejestruje ten ChannelURI w aplikacji mobilnej usługi aplikacji.
3. W górnej części **OnLaunched** obsługi zdarzeń w **App.xaml.cs**, Dodaj **async** modyfikator do definicji — metoda i dodaj następujące wywołanie do nowego  **InitNotificationsAsync** metody, jak w poniższym przykładzie:

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

            // ...
        }

    Gwarantuje to, czy krótkim okresie ChannelURI jest zarejestrowana w każdym razem, gdy aplikacja jest uruchamiana.
4. Ponownie skompiluj projekt aplikacji platformy uniwersalnej systemu Windows. Aplikacja jest teraz gotowa do odbierania wyskakujących powiadomień.

## <a id="test"></a>Testowych powiadomień wypychanych w aplikacji
[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a id="more"></a>Następne kroki
Dowiedz się więcej na temat powiadomień wypychanych:

* [Jak używać zarządzanego klienta usługi Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications) Szablony zapewniają elastyczność i możliwość wysyłania wypchnięć i platform i wypchnięć zlokalizowanego. Dowiedz się, jak zarejestrować szablonów.
* [Diagnozowanie problemów powiadomień wypychanych](../notification-hubs/notification-hubs-push-notification-fixer.md) różnych powodów, dlaczego powiadomienia mogą pobrać usunięte lub nie kończą na urządzeniach. W tym temacie przedstawiono sposób analizowania i ustalić główną przyczynę niepowodzenia powiadomień wypychanych.

Należy rozważyć kontynuowanie na jedno z następujących samouczków:

* [Dodawanie uwierzytelniania do aplikacji](app-service-mobile-windows-store-dotnet-get-started-users.md) Dowiedz się, jak uwierzytelniać użytkowników aplikacji przy użyciu dostawcy tożsamości.
* [Włączanie synchronizacji w trybie offline dla aplikacji](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) Dowiedz się, jak dodać obsługę w trybie offline aplikacji przy użyciu zaplecza aplikacji mobilnej. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacją mobilną &mdash; przeglądanie, dodawanie lub modyfikowanie danych &mdash; nawet w przypadku braku połączenia sieciowego.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
