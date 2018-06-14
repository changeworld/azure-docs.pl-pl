---
title: Dodawanie powiadomień wypychanych do aplikacji platformy Xamarin.iOS za pomocą usługi Azure App Service
description: Dowiedz się, jak używać usługi Azure App Service do wysyłania powiadomień wypychanych do aplikacji platformy Xamarin.iOS
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
ms.openlocfilehash: b8d5a8d8725e2e9412cef7c377b17a77f34be27d
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2018
ms.locfileid: "27592511"
---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Dodawanie powiadomień wypychanych do aplikacji platformy Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Przegląd
W tym samouczku, dodawanie powiadomień wypychanych do [Xamarin.iOS szybki start](app-service-mobile-xamarin-ios-get-started.md) projektu, aby powiadomienia wypychane są wysyłane do urządzenia, za każdym razem, gdy wstawieniu rekordu.

Jeśli nie używasz Projekt serwera pobrany szybki start, konieczne będzie pakiet rozszerzenia powiadomień wypychanych. Zobacz [pracować z serwera wewnętrznej bazy danych .NET SDK usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) Aby uzyskać więcej informacji.

## <a name="prerequisites"></a>Wymagania wstępne
* Zakończenie [szybkiego startu Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md) samouczka.
* Urządzenie fizyczne z systemem iOS. Powiadomienia wypychane nie są obsługiwane za pomocą symulatora systemu iOS.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Rejestrowanie aplikacji dla powiadomień wypychanych w portalu dla deweloperów firmy Apple
[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>Konfigurowanie aplikacji mobilnej do wysyłania powiadomień wypychanych
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Aktualizowanie projektów serwera do wysyłania powiadomień wypychanych
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>Konfigurowanie projektu platformy Xamarin.iOS
[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>Dodawanie powiadomień wypychanych do aplikacji
1. W **QSTodoService**, dodaj następującą właściwość, aby **AppDelegate** można uzyskać klientów urządzeń przenośnych:
   
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
2. Dodaj następujące `using` instrukcji na początku **AppDelegate.cs** pliku.
   
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;
3. W **AppDelegate**, Zastąp **FinishedLaunching** zdarzeń:
   
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
4. W tym samym pliku, Zastąp **RegisteredForRemoteNotifications** zdarzeń. W tym kodzie rejestrowania dla powiadomień prostego szablonu, który zostanie wysłany na wszystkich platformach obsługiwanych przez serwer.
   
    Aby uzyskać więcej informacji dotyczących szablonów z usługą Notification Hubs, zobacz [szablony](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

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


1. Następnie zastąp **DidReceivedRemoteNotification** zdarzeń:
   
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

Aplikacja jest teraz zaktualizowana do obsługi powiadomień wypychanych.

## <a name="test"></a>Testowych powiadomień wypychanych w aplikacji
1. Naciśnij klawisz **Uruchom** przycisk, aby skompilować projekt i uruchomić aplikację w stanie urządzenia z systemem iOS, a następnie kliknij przycisk **OK** do akceptowania powiadomień wypychanych.
   
   > [!NOTE]
   > Należy jawnie zaakceptować powiadomień wypychanych z aplikacji. To żądanie występuje tylko aplikacja jest uruchamiana po raz pierwszy.
   > 
   > 
2. W aplikacji wpisz zadania, a następnie kliknij przycisk plus (**+**) ikona.
3. Sprawdź, czy powiadomienie o odebraniu, a następnie kliknij przycisk **OK** na odrzucenie powiadomienia.
4. Powtórz krok 2 natychmiast zamknij aplikację, a następnie sprawdź, czy powiadomienie jest widoczne.

W tym samouczku została pomyślnie ukończona.

<!-- Images. -->

<!-- URLs. -->



