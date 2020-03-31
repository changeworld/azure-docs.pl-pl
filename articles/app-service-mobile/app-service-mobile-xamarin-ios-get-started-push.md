---
title: Dodawanie powiadomień wypychanych do aplikacji Xamarin.iOS
description: Dowiedz się, jak używać usługi Azure App Service do wysyłania powiadomień wypychanych do aplikacji Xamarin.iOS.
ms.assetid: 2921214a-49f8-45e1-a306-a85ce21defca
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: f9c70491d06f61931ebabda859ff3a86ed035b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249284"
---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Dodawanie powiadomień wypychanych do aplikacji Xamarin.iOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Omówienie

W tym samouczku dodasz powiadomienia wypychane do projektu [szybkiego startu platformy Xamarin.iOS,](app-service-mobile-xamarin-ios-get-started.md) aby powiadomienie wypychane było wysyłane do urządzenia za każdym razem, gdy zostanie wstawiony rekord.

Jeśli nie korzystasz z pobranego projektu serwera szybkiego startu, potrzebny będzie pakiet rozszerzenia powiadomień wypychanych. Aby uzyskać więcej informacji, zobacz [Praca z zestawem SDK serwera wewnętrznej bazy danych .NET dla aplikacji mobilnych platformy Azure.](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończ samouczek [szybki start platformy Xamarin.iOS.](app-service-mobile-xamarin-ios-get-started.md)
* Fizyczne urządzenie z systemem iOS. Powiadomienia wypychane nie są obsługiwane przez symulator systemu iOS.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Zarejestruj aplikację do otrzymywani powiadomień push w portalu deweloperskim firmy Apple

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>Konfigurowanie aplikacji mobilnej do wysyłania powiadomień wypychanych

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Aktualizowanie projektu serwera w celu wysyłania powiadomień wypychanych

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>Konfigurowanie projektu platformy Xamarin.iOS

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>Dodawanie powiadomień wypychanych do aplikacji

1. W **QSTodoService**dodaj następującą właściwość, aby **aplikacja AppDelegate** mogła nabyć klienta mobilnego:

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

2. Dodaj następującą `using` instrukcję do górnej części pliku **AppDelegate.cs.**

    ```csharp
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

3. W **aplikacji AppDelegate**, zastąpić **FinishedLaunching** zdarzenia:

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

4. W tym samym pliku zastąp `RegisteredForRemoteNotifications` zdarzenie. W tym kodzie rejestrujesz się w celu uzyskania prostego szablonu powiadomienia, które zostanie wysłane przez serwer na wszystkich obsługiwanych platformach.

    Aby uzyskać więcej informacji na temat szablonów za pomocą Centrów powiadomień, zobacz [Szablony](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

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

5. Następnie należy zastąpić **zdarzenie DidReceivedRemoteNotification:**

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

Aplikacja jest teraz aktualizowana w celu obsługi powiadomień wypychanych.

## <a name="test-push-notifications-in-your-app"></a><a name="test"></a>Testowanie powiadomień wypychanych w aplikacji

1. Naciśnij przycisk **Uruchom,** aby utworzyć projekt i uruchomić aplikację na urządzeniu obsługującym iOS, a następnie kliknij przycisk **OK,** aby zaakceptować powiadomienia wypychane.

   > [!NOTE]
   > Musisz jawnie akceptować powiadomienia wypychane z aplikacji. To żądanie występuje tylko przy pierwszym uruchomieniu aplikacji.

2. W aplikacji wpisz zadanie, a następnie kliknij**+** ikonę plus ( ).
3. Sprawdź, czy powiadomienie zostało odebrane, a następnie kliknij przycisk **OK,** aby odrzucić powiadomienie.
4. Powtórz krok 2 i natychmiast zamknij aplikację, a następnie sprawdź, czy jest wyświetlane powiadomienie.

Pomyślnie ukończono ten samouczek.
