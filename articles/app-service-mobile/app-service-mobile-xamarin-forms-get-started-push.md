---
title: Dodawanie powiadomień wypychanych do aplikacji platformy Xamarin.Forms | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wysyłać powiadomienia wypychane obejmującego wiele platform do aplikacji platformy Xamarin.Forms przy użyciu usług Azure.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: 0bea00d411205541684e807182abd3236c09bd9d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Dodawanie powiadomień wypychanych do aplikacji platformy Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Przegląd
W tym samouczku, dodawanie powiadomień wypychanych do projektów, które powstały w wyniku [szybki start platformy Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Oznacza to, że powiadomienie wypychane zostanie wysłane do wszystkich klientów i platform, za każdym razem, gdy wstawieniu rekordu.

Jeśli nie używasz Projekt serwera pobrany szybki start, konieczne będzie pakiet rozszerzenia powiadomień wypychanych. Aby uzyskać więcej informacji, zobacz [pracować z serwera wewnętrznej bazy danych .NET SDK usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne
Dla systemu iOS, konieczne będzie [członkostwo w programie dla deweloperów firmy Apple](https://developer.apple.com/programs/ios/) i urządzenie fizyczne z systemem iOS. [Symulatora systemu iOS nie obsługuje powiadomień wypychanych](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>Konfigurowanie Centrum powiadomień
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Aktualizowanie projektów serwera do wysyłania powiadomień wypychanych
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Konfigurowanie i uruchamianie projektu systemu Android (opcjonalnie)
Wykonaj tę sekcję, aby włączyć powiadomień wypychanych dla projektu platformy Xamarin.Forms Droid dla systemu Android.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Włącz Firebase Cloud Messaging (FCM)
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>Konfigurowanie zaplecza aplikacji mobilnej do wysyłania żądań wypychanych przy użyciu FCM
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Dodawanie powiadomień wypychanych do projektu systemu Android
Na zapleczu skonfigurowano FCM można dodać składniki i kody klienta do rejestracji w usłudze FCM. Można również zarejestrować dla powiadomień wypychanych przy użyciu usługi Azure Notification Hubs przy użyciu zaplecza aplikacji mobilnych i otrzymywać powiadomienia.

1. W **Droid** projektu, kliknij prawym przyciskiem myszy **odwołania > Zarządzaj pakietami NuGet...** .
1. W oknie Menedżera pakietów NuGet, wyszukaj **Xamarin.Firebase.Messaging** pakiet, a następnie dodaj go do projektu.
1. W properies projektu dla **Droid** projektu, Ustaw aplikację, aby skompilować przy użyciu systemu Android w wersji 7.0 lub nowszej.
1. Dodaj **google services.json** plik pobrany z poziomu konsoli Firebase do katalogu głównego **Droid** projektu i ustawić jej akcji kompilacji **GoogleServicesJson**. Aby uzyskać więcej informacji, zobacz [Dodaj plik JSON usługi Google](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File).

#### <a name="registering-with-firebase-cloud-messaging"></a>Rejestrowanie w Firebase Cloud Messaging

1. Otwórz **AndroidManifest.xml** pliku i Wstaw następujący `<receiver>` elementy do `<application>` elementu:

        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
          <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
          </intent-filter>
        </receiver>

#### <a name="implementing-the-firebase-instance-id-service"></a>Wdrażanie usługi Identyfikatora wystąpienia Firebase

1. Dodaj nową klasę do **Droid** projektu o nazwie `FirebaseRegistrationService`i upewnij się, że następujące `using` instrukcje znajdują się w górnej części pliku:

        using System.Threading.Tasks;
        using Android.App;
        using Android.Util;
        using Firebase.Iid;
        using Microsoft.WindowsAzure.MobileServices;

1. Zamień wszystkie puste `FirebaseRegistrationService` klasy następującym kodem:

        [Service]
        [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
        public class FirebaseRegistrationService : FirebaseInstanceIdService
        {
            const string TAG = "FirebaseRegistrationService";

            public override void OnTokenRefresh()
            {
                var refreshedToken = FirebaseInstanceId.Instance.Token;
                Log.Debug(TAG, "Refreshed token: " + refreshedToken);
                SendRegistrationTokenToAzureNotificationHub(refreshedToken);
            }

            void SendRegistrationTokenToAzureNotificationHub(string token)
            {
                // Update notification hub registration
                Task.Run(async () =>
                {
                    await AzureNotificationHubService.RegisterAsync(TodoItemManager.DefaultManager.CurrentClient.GetPush(), token);
                });
            }
        }

    `FirebaseRegistrationService` Klasy jest odpowiedzialny za wygenerowanie tokenów zabezpieczających, które zezwolić aplikacji na dostęp do FCM. `OnTokenRefresh` Metoda jest wywoływana, gdy aplikacja odbiera token rejestracji z FCM. Metoda pobiera tokenu z `FirebaseInstanceId.Instance.Token` właściwość, która asynchronicznie jest aktualizowana przez FCM. `OnTokenRefresh` Wywoływana jest metoda rzadko, ponieważ token jest aktualizowana tylko, gdy jest zainstalowany lub odinstalowany, gdy użytkownik usuwa dane aplikacji, gdy aplikacja Usuwa identyfikator wystąpienia aplikacji, lub gdy został zabezpieczeń tokenu naruszenie bezpieczeństwa. Ponadto usługa FCM identyfikator wystąpienia żądania, czy aplikacja odświeża token jego okresowo, zazwyczaj co 6 miesięcy.

    `OnTokenRefresh` Również wywołuje metodę `SendRegistrationTokenToAzureNotificationHub` metodę, która jest używana do skojarzenia z Centrum powiadomień Azure tokenu rejestracji użytkownika.

#### <a name="registering-with-the-azure-notification-hub"></a>Rejestrowanie w Centrum powiadomień Azure

1. Dodaj nową klasę do **Droid** projektu o nazwie `AzureNotificationHubService`i upewnij się, że następujące `using` instrukcje znajdują się w górnej części pliku:

        using System;
        using System.Threading.Tasks;
        using Android.Util;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;

1. Zamień wszystkie puste `AzureNotificationHubService` klasy następującym kodem:

        public class AzureNotificationHubService
        {
            const string TAG = "AzureNotificationHubService";

            public static async Task RegisterAsync(Push push, string token)
            {
                try
                {
                    const string templateBody = "{\"data\":{\"message\":\"$(messageParam)\"}}";
                    JObject templates = new JObject();
                    templates["genericMessage"] = new JObject
                    {
                        {"body", templateBody}
                    };

                    await push.RegisterAsync(token, templates);
                    Log.Info("Push Installation Id: ", push.InstallationId.ToString());
                }
                catch (Exception ex)
                {
                    Log.Error(TAG, "Could not register with Notification Hub: " + ex.Message);
                }
            }
        }

    `RegisterAsync` — Metoda tworzy szablon wiadomości prostego powiadomienia jako JSON i rejestruje odbierać powiadomienia szablonu z Centrum powiadomień przy użyciu tokenu rejestracji Firebase. Dzięki temu, że powiadomienia wysyłane z Centrum powiadomień Azure będzie obowiązywać reprezentowany przez tokenu rejestracji urządzenia.

#### <a name="displaying-the-contents-of-a-push-notification"></a>Wyświetlanie zawartości powiadomień wypychanych

1. Dodaj nową klasę do **Droid** projektu o nazwie `FirebaseNotificationService`i upewnij się, że następujące `using` instrukcje znajdują się w górnej części pliku:

        using Android.App;
        using Android.Content;
        using Android.Media;
        using Android.Support.V7.App;
        using Android.Util;
        using Firebase.Messaging;

1. Zamień wszystkie puste `FirebaseNotificationService` klasy następującym kodem:

        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class FirebaseNotificationService : FirebaseMessagingService
        {
            const string TAG = "FirebaseNotificationService";

            public override void OnMessageReceived(RemoteMessage message)
            {
                Log.Debug(TAG, "From: " + message.From);

                // Pull message body out of the template
                var messageBody = message.Data["message"];
                if (string.IsNullOrWhiteSpace(messageBody))
                    return;

                Log.Debug(TAG, "Notification message body: " + messageBody);
                SendNotification(messageBody);
            }

            void SendNotification(string messageBody)
            {
                var intent = new Intent(this, typeof(MainActivity));
                intent.AddFlags(ActivityFlags.ClearTop);
                var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

                var notificationBuilder = new NotificationCompat.Builder(this)
                    .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                    .SetContentTitle("New Todo Item")
                    .SetContentText(messageBody)
                    .SetContentIntent(pendingIntent)
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))
                    .SetAutoCancel(true);

                var notificationManager = NotificationManager.FromContext(this);
                notificationManager.Notify(0, notificationBuilder.Build());
            }
        }

    `OnMessageReceived` Metodę, która jest wywoływana, gdy aplikacja odbiera powiadomienie z FCM, wyodrębnia zawartość wiadomości i wywołuje `SendNotification` metody. Ta metoda Konwertuje zawartość komunikatu lokalnego powiadomienia, który jest uruchamiany, gdy aplikacja jest uruchomiona z powiadomień w obszarze powiadomień.

Teraz wszystko jest gotowe testowych powiadomień wypychanych w aplikacji uruchomionych na urządzeniu z systemem Android lub emulator.

### <a name="test-push-notifications-in-your-android-app"></a>Testowych powiadomień wypychanych w aplikacji systemu Android
Pierwsze dwa kroki są wymagane tylko wtedy, gdy w przypadku testowania emulatora.

1. Upewnij się, że wdrażania lub debugowania na urządzeniu lub emulatorze, który jest skonfigurowany z usług Google Play. Można to zweryfikować, sprawdzania **odtwarzanie** aplikacje są instalowane na urządzeniu lub emulatorze.
2. Dodaj konto Google na urządzeniu z systemem Android, klikając **aplikacje** > **ustawienia** > **Dodaj konto**. Następnie postępuj zgodnie z monitami dodanie istniejącego konta Google na urządzeniu lub Utwórz nową.
3. W programie Visual Studio lub Xamarin Studio kliknij prawym przyciskiem myszy **Droid** projekt i kliknij przycisk **Ustaw jako projekt startowy**.
4. Kliknij przycisk **Uruchom** Aby skompilować projekt i uruchomić aplikację na urządzeniu z systemem Android lub w emulatorze.
5. W aplikacji wpisz zadania, a następnie kliknij przycisk plus (**+**) ikona.
6. Sprawdź, czy otrzyma powiadomienie po dodaniu elementu.

## <a name="configure-and-run-the-ios-project-optional"></a>Konfigurowanie i uruchamianie projektu iOS (opcjonalnie)
Ta sekcja dotyczy uruchamiania projektu Xamarin iOS dla urządzeń z systemem iOS. Jeśli nie pracujesz z urządzeniami z systemem iOS, możesz pominąć tę sekcję.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Konfigurowanie Centrum powiadomień dla usługi APNS
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Następnie skonfiguruj ustawienia projektu dla systemu iOS w programie Xamarin Studio lub Visual Studio.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Dodawanie powiadomień wypychanych do aplikacji systemu iOS
1. W **iOS** projektu, otwórz AppDelegate.cs i dodaj następującą instrukcję na początku pliku kodu.

        using Newtonsoft.Json.Linq;
2. W **AppDelegate** klasy, Dodaj zastąpienie **RegisteredForRemoteNotifications** zdarzenia do zarejestrowania dla powiadomień:

        public override void RegisteredForRemoteNotifications(UIApplication application,
            NSData deviceToken)
        {
            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }
3. W **AppDelegate**, także dodać następujące zastąpienie dla **DidReceiveRemoteNotification** obsługi zdarzeń:

        public override void DidReceiveRemoteNotification(UIApplication application,
            NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps[new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

    Ta metoda obsługuje przychodzące powiadomienia, gdy aplikacja jest uruchomiona.
4. W **AppDelegate** klasy, Dodaj następujący kod, aby **FinishedLaunching** metody:

        // Register for push notifications.
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

    Umożliwia to obsługę zdalnego powiadomienia i żądania push rejestracji.

Aplikacja jest teraz zaktualizowana do obsługi powiadomień wypychanych.

#### <a name="test-push-notifications-in-your-ios-app"></a>Testowych powiadomień wypychanych w aplikacji systemu iOS
1. Kliknij prawym przyciskiem myszy projekt iOS, a następnie kliknij przycisk **Ustaw jako projekt startowy**.
2. Naciśnij klawisz **Uruchom** przycisk lub **F5** w Visual Studio, aby skompilować projekt i uruchomić aplikację w urządzeniu z systemem iOS. Następnie kliknij przycisk **OK** do akceptowania powiadomień wypychanych.

   > [!NOTE]
   > Należy jawnie zaakceptować powiadomień wypychanych z aplikacji. To żądanie występuje tylko aplikacja jest uruchamiana po raz pierwszy.
   >
   >
3. W aplikacji wpisz zadania, a następnie kliknij przycisk plus (**+**) ikona.
4. Sprawdź, czy otrzyma powiadomienie, a następnie kliknij **OK** na odrzucenie powiadomienia.

## <a name="configure-and-run-windows-projects-optional"></a>Konfigurowanie i uruchamianie projekty systemu Windows (opcjonalnie)
Ta sekcja dotyczy uruchamiania aplikacji platformy Xamarin.Forms i WinPhone81 projektów dla urządzeń z systemem Windows. Te kroki również obsługiwać projekty systemu Windows platformy Uniwersalnej. Jeśli nie pracujesz z urządzeniami z systemem Windows, możesz pominąć tę sekcję.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Rejestrowanie aplikacji systemu Windows dla powiadomień wypychanych z powiadomienia usługi WNS (Windows)
[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurowanie Centrum powiadomień w przypadku usługi WNS
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Dodawanie powiadomień wypychanych do aplikacji systemu Windows
1. W programie Visual Studio Otwórz **App.xaml.cs** w systemie Windows projektu i dodaj następujące instrukcje.

        using Newtonsoft.Json.Linq;
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
        using <your_TodoItemManager_portable_class_namespace>;

    Zastąp `<your_TodoItemManager_portable_class_namespace>` z przestrzenią nazw przenośne projektu zawierającego `TodoItemManager` klasy.
2. W pliku App.xaml.cs Dodaj następujące **InitNotificationsAsync** metody:

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS =
                "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyWNS},
                {"headers", headers} // Needed for WNS.
            };

            await TodoItemManager.DefaultManager.CurrentClient.GetPush()
                .RegisterAsync(channel.Uri, templates);
        }

    Ta metoda pobiera kanału powiadomień wypychanych i rejestruje szablonu, aby otrzymywać powiadomienia szablonu z Centrum powiadomień. Powiadomienie szablonu, który obsługuje *messageParam* będą dostarczane do tego klienta.
3. W pliku App.xaml.cs aktualizacji **OnLaunched** definicję metody obsługi zdarzeń, dodając `async` modyfikator. Następnie dodaj następujący wiersz kodu na końcu metody:

        await InitNotificationsAsync();

    Dzięki temu, że rejestracji powiadomień wypychanych jest tworzony lub odświeżyć za każdym razem, gdy aplikacja jest uruchamiana. Należy to zrobić, aby zagwarantować kanału wypychanych usługi WNS jest zawsze aktywna.  
4. Otwórz w Eksploratorze rozwiązań programu Visual Studio, **Package.appxmanifest** pliku, a następnie ustaw **wyskakujące funkcją** do **tak** w obszarze **powiadomienia**.
5. Tworzenie aplikacji i sprawdź, czy użytkownik nie ma błędów. Twoja aplikacja kliencka powinna teraz rejestrować się dla szablonu powiadomień z zaplecza aplikacji mobilnej. W tej sekcji należy powtórzyć dla każdego projektu systemu Windows w rozwiązaniu.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testowych powiadomień wypychanych w aplikacji systemu Windows
1. W programie Visual Studio, kliknij prawym przyciskiem myszy projekt dla systemu Windows, a następnie kliknij przycisk **Ustaw jako projekt startowy**.
2. Kliknij przycisk **Uruchom**, aby skompilować projekt i uruchomić aplikację.
3. W aplikacji wpisz nazwę nowego zadania do wykonania, a następnie kliknij przycisk plus (**+**) ikonę, aby dodać go.
4. Sprawdź, czy otrzyma powiadomienie po dodaniu elementu.

## <a name="next-steps"></a>Kolejne kroki
Dodatkowe informacje na temat powiadomień wypychanych:

* [Wysyłanie powiadomień wypychanych z usługi Azure Mobile Apps](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Zdalnego powiadomienia z Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [Diagnozowanie problemów powiadomień wypychanych](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Istnieją różne przyczyny, dlaczego powiadomienia mogą pobrać usunięte lub nie kończą na urządzeniach. W tym temacie przedstawiono sposób analizowania i ustalić główną przyczynę niepowodzenia powiadomień wypychanych.

Możesz również na jedno z następujących samouczków:

* [Dodawanie uwierzytelniania do aplikacji](app-service-mobile-xamarin-forms-get-started-users.md)  
  Dowiedz się, jak uwierzytelniać użytkowników aplikacji przy użyciu dostawcy tożsamości.
* [Włączanie synchronizacji w trybie offline dla aplikacji](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Dowiedz się, jak dodać obsługę aplikacji w trybie offline przy użyciu zaplecza funkcji Mobile Apps. Z synchronizacji w trybie offline, użytkownicy mogą korzystać z aplikacji mobilnej&mdash;przeglądanie, dodawanie lub modyfikowanie danych&mdash;nawet wtedy, gdy istnieje połączenie sieciowe.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
