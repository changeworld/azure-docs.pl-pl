---
title: Dodawanie powiadomień wypychanych do aplikacji platformy Xamarin.iOS przy użyciu usługi Azure App Service
description: Dowiedz się, jak wysyłać powiadomienia wypychane do aplikacji platformy Xamarin.iOS przy użyciu usługi Azure App Service
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 2921214a-49f8-45e1-a306-a85ce21defca
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: de24b64ecd95eec79d7508f978acda9f0ae5a8d6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62097542"
---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Dodawanie powiadomień wypychanych do aplikacji platformy Xamarin.iOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Omówienie

W ramach tego samouczka, możesz dodać powiadomienia wypychane do [szybki start platformy Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md) projektu, dzięki czemu jest wysyłane powiadomienie push do urządzenia, za każdym razem, gdy rekord zostanie wstawiona.

Jeśli nie używasz projektu serwera pobranego — szybki start, konieczne będzie pakiet rozszerzenia powiadomień wypychanych. Zobacz [pracy z zestawem SDK serwera zaplecza platformy .NET dla usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) Aby uzyskać więcej informacji.

## <a name="prerequisites"></a>Wymagania wstępne

* Wykonaj [Szybki Start platformy Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md) samouczka.
* Urządzenie fizyczne z systemem iOS. Powiadomienia wypychane nie są obsługiwane przez symulator systemu iOS.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Zarejestrować aplikację na potrzeby powiadomień wypychanych w portalu dla deweloperów firmy Apple

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>Konfigurowanie aplikacji mobilnej do wysyłania powiadomień wypychanych

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Aktualizuj projekt serwera do wysyłania powiadomień wypychanych

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>Konfigurowanie projektu Xamarin.iOS

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>Dodawanie powiadomień wypychanych do aplikacji

1. W **QSTodoService**, dodaj następującą właściwość, aby **elemencie AppDelegate** mogą nabyć klientów urządzeń przenośnych:

    ```csharp
    public MobileServiceClient GetClient {
        get
        {
            return client;
        }
        private set
        {
            client = value;
        }
    }
    ```

2. Dodaj następujący kod `using` instrukcji na górze **AppDelegate.cs** pliku.

    ```csharp
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

3. W **elemencie AppDelegate**, Zastąp **FinishedLaunching** zdarzeń:

   ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // registers for push for iOS8
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

        return true;
    }
    ```

4. W tym samym pliku, należy zastąpić `RegisteredForRemoteNotifications` zdarzeń. W tym kodzie w przypadku rejestracji powiadomienia prostego szablonu, który zostanie wysłany na wszystkich platformach obsługiwanych przez serwer.

    Aby uzyskać więcej informacji na temat szablonów przy użyciu usługi Notification Hubs, zobacz [szablony](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
    {
        MobileServiceClient client = QSTodoService.DefaultService.GetClient;

        const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
        {
            {"body", templateBodyAPNS}
        };

        // Register for push with your mobile app
        var push = client.GetPush();
        push.RegisterAsync(deviceToken, templates);
    }
    ```

5. Następnie zastąp **DidReceivedRemoteNotification** zdarzeń:

   ```csharp
    public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
    {
        NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

        string alert = string.Empty;
        if (aps.ContainsKey(new NSString("alert")))
            alert = (aps [new NSString("alert")] as NSString).ToString();

        //show alert
        if (!string.IsNullOrEmpty(alert))
        {
            UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
            avAlert.Show();
        }
    }
    ```

Aplikacji został zaktualizowany do obsługi powiadomień wypychanych.

## <a name="test"></a>Testowych powiadomień push w aplikacji

1. Naciśnij klawisz **Uruchom** przycisk, aby skompilować projekt i uruchomić aplikację w stanie urządzenia z systemem iOS, a następnie kliknij przycisk **OK** do akceptowania powiadomień wypychanych.

   > [!NOTE]
   > Należy jawnie zaakceptować powiadomień wypychanych z poziomu aplikacji. To żądanie występuje tylko przy pierwszym uruchomieniu aplikacji.

2. W aplikacji wpisz zadania, a następnie kliknij znak plus ( **+** ) ikona.
3. Sprawdź, czy powiadomienie zostanie odebrana, a następnie kliknij przycisk **OK** aby odrzucić powiadomienie.
4. Powtórz krok 2 natychmiast zamknąć aplikację, a następnie sprawdź, czy zostaje wyświetlone powiadomienie.

W tym samouczku została ukończona pomyślnie.
