---
title: Dodawanie powiadomień wypychanych do aplikacji platforma uniwersalna systemu Windows (platformy UWP) | Microsoft Docs
description: Dowiedz się, jak używać Mobile Apps Azure App Service i usługi Azure Notification Hubs do wysyłania powiadomień wypychanych do aplikacji platforma uniwersalna systemu Windows (platformy UWP).
services: app-service\mobile,notification-hubs
documentationcenter: windows
author: elamalani
manager: crdun
editor: ''
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: e3e82c971fee7f7dd95e6f9ef72631e8e82ebe7f
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025264"
---
# <a name="add-push-notifications-to-your-windows-app"></a>Dodawanie powiadomień wypychanych do aplikacji systemu Windows

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center obsługuje kompleksowe i zintegrowane usługi centralne do tworzenia aplikacji mobilnych. Deweloperzy mogą używać usług **kompilowania**, **testowania** i **dystrybucji** , aby skonfigurować ciągłą integrację i potok dostarczania. Po wdrożeniu aplikacji deweloperzy mogą monitorować stan i użycie swojej aplikacji przy użyciu usług **analizy** i **diagnostyki** oraz angażować się z użytkownikami za pomocą usługi **wypychania** . Deweloperzy mogą również korzystać z **uwierzytelniania** w celu uwierzytelniania użytkowników i usługi **danych** w celu utrwalania i synchronizowania danych aplikacji w chmurze.
> Jeśli chcesz zintegrować usługi w chmurze w swojej aplikacji mobilnej, zarejestruj się w usłudze App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) już dziś.

## <a name="overview"></a>Przegląd

W tym samouczku dowiesz się, jak dodać powiadomienia wypychane do projektu [szybkiego startu systemu Windows](app-service-mobile-windows-store-dotnet-get-started.md) , aby Powiadomienie wypychane było wysyłane do urządzenia za każdym razem, gdy rekord zostanie wstawiony.

Jeśli nie używasz pobranego projektu szybkiego startu serwera, będzie potrzebny pakiet rozszerzenia powiadomień wypychanych. Aby uzyskać więcej informacji, zobacz temat [współpraca z zestawem SDK serwera zaplecza platformy .NET dla platformy Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

## <a name="configure-hub"></a>Konfigurowanie centrum powiadomień

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Rejestrowanie aplikacji dla usługi powiadomień wypychanych

Musisz przesłać aplikację do Microsoft Store, a następnie skonfigurować projekt serwera do integracji z [usługami powiadomień systemu Windows (WNS)](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) w celu wysyłania wypychania.

1. W programie Visual Studio Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt aplikacji platformy UWP, kliknij pozycję **przechowuj** > **Skojarz aplikację ze sklepem...** .

    ![Skojarz aplikację z Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)

2. W Kreatorze kliknij przycisk **dalej**, zaloguj się przy użyciu konto Microsoft, wpisz nazwę aplikacji w polu **Zarezerwuj nową nazwę aplikacji**, a następnie kliknij pozycję **Zarezerwuj**.
3. Po pomyślnym utworzeniu rejestracji aplikacji wybierz nową nazwę aplikacji, kliknij przycisk **dalej**, a następnie kliknij pozycję **Skojarz**. Spowoduje to dodanie wymaganych informacji dotyczących rejestracji Microsoft Store do manifestu aplikacji.
4. Przejdź do [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/) i zaloguj się przy użyciu konto Microsoft. Kliknij aplikację ze sklepu Windows skojarzoną w poprzednim kroku.
5. Na stronie Rejestracja Zanotuj wartość w obszarze wpisy **tajne aplikacji** i **Identyfikator SID pakietu**, który będzie dalej używany do konfigurowania zaplecza aplikacji mobilnej.

    ![Skojarz aplikację z Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > Klucz tajny klienta i identyfikator SID pakietu są ważnymi poświadczeniami zabezpieczeń. Nie udostępniaj nikomu tych wartości ani nie rozpowszechniaj ich razem z aplikacją. **Identyfikator aplikacji** jest używany z wpisem tajnym w celu skonfigurowania uwierzytelniania konta Microsoft.

[App Center](https://docs.microsoft.com/appcenter/sdk/push/uwp#prerequisite---register-your-app-for-windows-notification-services-wns) również zawiera instrukcje dotyczące KONFIGUROWANIA aplikacji platformy UWP na potrzeby powiadomień wypychanych.

## <a name="configure-the-backend-to-send-push-notifications"></a>Konfigurowanie zaplecza do wysyłania powiadomień wypychanych

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a id="update-service"></a>Aktualizowanie serwera w celu wysyłania powiadomień wypychanych

Wykonaj poniższą procedurę, która pasuje do typu projektu zaplecza @ no__t-0either [.NET](#dotnet) lub zaplecza [Node. js](#nodejs).

### <a name="dotnet"></a>Projekt zaplecza .NET

1. W programie Visual Studio kliknij prawym przyciskiem myszy projekt serwera, a następnie kliknij pozycję **Zarządzaj pakietami NuGet**, wyszukaj ciąg Microsoft. Azure. NotificationHubs, a następnie kliknij przycisk **Instaluj**. Spowoduje to zainstalowanie biblioteki klienta Notification Hubs.
2. Rozwiń węzeł **Kontrolery**, Otwórz TodoItemController.cs i Dodaj następujące instrukcje using:

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

3. W metodzie **PostTodoItem** Dodaj następujący kod po wywołaniu **InsertAsync**:

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

    Ten kod nakazuje centrum powiadomień wysyłanie powiadomień wypychanych po wstawieniu nowego elementu.

4. Opublikuj ponownie projekt serwera.

### <a name="nodejs"></a>Projekt zaplecza Node. js
1. Skonfiguruj projekt zaplecza.
2. Zastąp istniejący kod w pliku TodoItem. js następującym:

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

    Spowoduje to wysłanie wyskakującego powiadomienia WNS zawierającego element. Text po wstawieniu nowego elementu do wykonania.

3. Podczas edytowania pliku na komputerze lokalnym należy ponownie opublikować projekt serwera.

## <a id="update-app"></a>Dodawanie powiadomień wypychanych do aplikacji
Następnie aplikacja musi zarejestrować się w celu otrzymywania powiadomień wypychanych po rozpoczęciu. Po włączeniu uwierzytelniania upewnij się, że użytkownik loguje się przed próbą zarejestrowania się na potrzeby powiadomień wypychanych.

1. Otwórz plik projektu **App.XAML.cs** i Dodaj następujące instrukcje `using`:

    ```csharp
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    ```

2. W tym samym pliku Dodaj następującą definicję metody **następującą initnotificationsasync** do klasy **App** :

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

    Ten kod pobiera identyfikator channeluri dla aplikacji z WNS, a następnie rejestruje ten identyfikator channeluri za pomocą aplikacji mobilnej App Service.

3. W górnej części procedury obsługi zdarzeń **OnStarted** w programie **App.XAML.cs**Dodaj modyfikator **Async** do definicji metody i Dodaj następujące wywołanie do nowej metody **następującą initnotificationsasync** , jak w poniższym przykładzie:

    ```csharp
    protected async override void OnLaunched(LaunchActivatedEventArgs e)
    {
        await InitNotificationsAsync();

        // ...
    }
    ```

    Gwarantuje to, że krótkotrwałe identyfikator channeluri jest rejestrowane za każdym razem, gdy aplikacja zostanie uruchomiona.

4. Skompiluj ponownie projekt aplikacji platformy UWP. Aplikacja jest teraz gotowa do odbierania wyskakujących powiadomień.

## <a id="test"></a>Testowanie powiadomień wypychanych w aplikacji

[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a id="more"></a>Następne kroki

Dowiedz się więcej o powiadomieniach wypychanych:

* [Jak używać zarządzanego klienta usługi Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications) Szablony zapewniają elastyczność wysyłania wypchnięciów międzyplatformowych i zlokalizowanych powiadomień wypychanych. Dowiedz się, jak zarejestrować szablony.
* [Diagnozuj problemy z powiadomieniem wypychanym](../notification-hubs/notification-hubs-push-notification-fixer.md) Istnieją różne przyczyny, dla których powiadomienia mogą zostać porzucone lub nie kończyć się na urządzeniach. W tym temacie pokazano, jak analizować i ustalić główną przyczynę niepowodzeń powiadomień wypychanych.

Rozważ przejście do jednego z następujących samouczków:

* [Dodawanie uwierzytelniania do aplikacji](app-service-mobile-windows-store-dotnet-get-started-users.md) Dowiedz się, jak uwierzytelniać użytkowników aplikacji przy użyciu dostawcy tożsamości.
* [Włączanie synchronizacji w trybie offline dla aplikacji](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) Dowiedz się, jak dodać obsługę aplikacji w trybie offline przy użyciu zaplecza aplikacji mobilnej. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacją mobilną &mdash; przeglądanie, dodawanie lub modyfikowanie danych &mdash; nawet w przypadku braku połączenia sieciowego.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
