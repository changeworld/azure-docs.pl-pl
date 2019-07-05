---
title: Dodawanie powiadomień wypychanych do aplikacji platformy Xamarin.Forms | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wysyłać powiadomienia wypychane dla wielu platform do aplikacji platformy Xamarin.Forms przy użyciu usług platformy Azure.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: b7e2ff63211ec5891a48a585e4f69e18116cdeb3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446582"
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Dodawanie powiadomień wypychanych do aplikacji platformy Xamarin.Forms

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center jest inwestujemy w nowe i zintegrowane usługi decydujące znaczenie dla aplikacji mobilnych. Deweloperzy mogą używać **kompilacji**, **testu** i **dystrybucji** usług do konfigurowania potoku ciągłej integracji i ciągłego dostarczania. Gdy aplikacja jest wdrażana, deweloperzy mogą monitorować stan i użycie ich przy użyciu aplikacji **Analytics** i **diagnostyki** usług i angażuj użytkowników za pomocą **wypychania** Usługa. Deweloperzy mogą również wykorzystać **uwierzytelniania** do uwierzytelniania użytkowników i **danych** usługę, aby utrwalić i synchronizowanie danych aplikacji w chmurze. Zapoznaj się z [platformy App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-forms-get-started-push) już dziś.
>

## <a name="overview"></a>Przegląd

W tym samouczku, jak dodawać powiadomienia wypychane do wszystkich projektów, które powstały w wyniku [Xamarin.Forms — szybki start](app-service-mobile-xamarin-forms-get-started.md). Oznacza to, że powiadomienia wypychane są wysyłane do wszystkich klientów dla wielu platform, za każdym razem, gdy rekord zostanie wstawiona.

Jeśli nie używasz projektu serwera pobranego — szybki start, konieczne będzie pakiet rozszerzenia powiadomień wypychanych. Aby uzyskać więcej informacji, zobacz [pracy z zestawem SDK serwera zaplecza platformy .NET dla usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne

Dla systemów iOS, konieczne będzie [członkostwa w programie dla deweloperów firmy Apple](https://developer.apple.com/programs/ios/) oraz urządzenie fizyczne z systemem iOS. [Symulatora systemu iOS nie obsługuje powiadomienia wypychane](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>Konfigurowanie Centrum powiadomień

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Aktualizuj projekt serwera do wysyłania powiadomień wypychanych

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Konfigurowanie i uruchamianie projektu dla systemu Android (opcjonalnie)

Wypełnij tę sekcję, aby włączyć powiadomienia wypychane dla projektu Xamarin.Forms (droid) dla systemu Android.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Włączanie usługi Firebase Cloud Messaging (FCM)

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>Skonfiguruj zaplecze funkcji Mobile Apps do wysyłania żądań push przy użyciu usługi FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Dodawanie powiadomień wypychanych do projektu dla systemu Android

Za pomocą zaplecza skonfigurowane przy użyciu usługi FCM można dodać składniki i kody klienta można zarejestrować za pomocą usługi FCM. Możesz także zarejestrować odbieranie powiadomień wypychanych przy użyciu usługi Azure Notification Hubs za pomocą zaplecze funkcji Mobile Apps i otrzymywać powiadomienia.

1. W **Droid** projektu, kliknij prawym przyciskiem myszy **odwołania > Zarządzaj pakietami NuGet...** .
1. W oknie Menedżera pakietów NuGet, wyszukaj **Xamarin.Firebase.Messaging** pakietu i dodaj go do projektu.
1. We właściwościach projektu **Droid** projektu, ustaw ją skompilować przy użyciu systemu Android w wersji 7.0 lub nowszej.
1. Dodaj **google-services.json** pliku, został pobrany z konsoli Firebase do katalogu głównego **Droid** projektu i ustaw jego akcję kompilacji na **GoogleServicesJson**. Aby uzyskać więcej informacji, zobacz [Dodaj plik JSON usługi Google](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File).

#### <a name="registering-with-firebase-cloud-messaging"></a>Rejestrowanie w usłudze Firebase Cloud Messaging

1. Otwórz plik **AndroidManifest.xml** i wstaw następujące elementy `<receiver>` do elementu `<application>`:

    ```xml
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```

#### <a name="implementing-the-firebase-instance-id-service"></a>Wdrażanie usługi Identyfikatora wystąpienia usługi Firebase

1. Dodaj nową klasę do **Droid** projektu o nazwie `FirebaseRegistrationService`i upewnij się, że następujące `using` instrukcje są obecne w górnej części pliku:

    ```csharp
    using System.Threading.Tasks;
    using Android.App;
    using Android.Util;
    using Firebase.Iid;
    using Microsoft.WindowsAzure.MobileServices;
    ```

1. Zastąp pustą `FirebaseRegistrationService` klasy z następującym kodem:

    ```csharp
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
    ```

    `FirebaseRegistrationService` Klasa jest odpowiedzialny za Generowanie tokenów zabezpieczających, które Zezwól aplikacji na dostęp do usługi FCM. `OnTokenRefresh` Metoda jest wywoływana, gdy aplikacja odbiera token rejestracji z usługi FCM. Metoda pobiera token z `FirebaseInstanceId.Instance.Token` właściwość, która asynchronicznie jest aktualizowany za pomocą usługi FCM. `OnTokenRefresh` Wywoływana jest metoda rzadko, ponieważ token są aktualizowane tylko po zainstalowaniu lub odinstalowaniu, gdy użytkownik usuwa dane aplikacji, gdy aplikacja Usuwa identyfikator wystąpienia aplikacji lub gdy zabezpieczeń tokenu naruszenie bezpieczeństwa. Ponadto usługi Identyfikatora wystąpienia usługi FCM zażąda, że aplikacja odświeża jej token okresowo zazwyczaj co 6 miesięcy.

    `OnTokenRefresh` Wywołuje również metodę `SendRegistrationTokenToAzureNotificationHub` metody, która służy do kojarzenia tokenu rejestracji w usłudze Azure Notification Hubs.

#### <a name="registering-with-the-azure-notification-hub"></a>Rejestrowanie w usłudze Azure Notification Hubs

1. Dodaj nową klasę do **Droid** projektu o nazwie `AzureNotificationHubService`i upewnij się, że następujące `using` instrukcje są obecne w górnej części pliku:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Android.Util;
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

1. Zastąp pustą `AzureNotificationHubService` klasy z następującym kodem:

    ```csharp
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
    ```

    `RegisterAsync` Metoda tworzy szablon wiadomości z powiadomieniem proste jak JSON i rejestry, aby otrzymywać powiadomienia o szablonach z Centrum powiadomień przy użyciu tokenu rejestracji usługi Firebase. Daje to gwarancję, że wszelkie powiadomienia wysyłane z Centrum powiadomień platformy Azure będą ukierunkowane na urządzenie, reprezentowane przez tokenu rejestracji.

#### <a name="displaying-the-contents-of-a-push-notification"></a>Wyświetlanie zawartości powiadomienia Wypychanego

1. Dodaj nową klasę do **Droid** projektu o nazwie `FirebaseNotificationService`i upewnij się, że następujące `using` instrukcje są obecne w górnej części pliku:

    ```csharp
    using Android.App;
    using Android.Content;
    using Android.Media;
    using Android.Support.V7.App;
    using Android.Util;
    using Firebase.Messaging;
    ```

1. Zastąp pustą `FirebaseNotificationService` klasy z następującym kodem:

    ```csharp
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
    ```

    `OnMessageReceived` Metody, która jest wywoływana, gdy aplikacja odbiera powiadomienia z usługi FCM, wyodrębnianie treści komunikatu i wywołuje `SendNotification` metody. Ta metoda Konwertuje zawartość komunikatu do lokalnego powiadomienia, który jest uruchamiany, gdy aplikacja jest uruchomiona z powiadomieniem znajdujących się w obszarze powiadomień.

Teraz jesteś gotowy testowych powiadomień push w aplikacji uruchomionej na urządzeniu z systemem Android lub emulator.

### <a name="test-push-notifications-in-your-android-app"></a>Testowych powiadomień push w aplikacji systemu Android

Pierwsze dwa kroki są wymagane tylko wtedy, gdy testujesz w emulatorze.

1. Upewnij się, że wdrożenie lub debugowania na urządzeniu lub w emulatorze, który jest skonfigurowany przy użyciu usługi Google Play. Można to sprawdzić przez sprawdzanie **Odtwórz** aplikacje są instalowane na urządzeniu lub w emulatorze.
2. Dodaj konto Google na urządzeniu z systemem Android, klikając **aplikacje** > **ustawienia** > **Dodaj konto**. Następnie postępuj zgodnie z instrukcjami, dodanie istniejącego konta Google na urządzeniu lub Utwórz nową.
3. W programie Visual Studio lub Xamarin Studio kliknij prawym przyciskiem myszy **Droid** projektu, a następnie kliknij przycisk **Ustaw jako projekt startowy**.
4. Kliknij przycisk **Uruchom** Aby skompilować projekt i uruchomić aplikację na urządzeniu z systemem Android lub w emulatorze.
5. W aplikacji wpisz zadania, a następnie kliknij znak plus ( **+** ) ikona.
6. Sprawdź, czy otrzyma powiadomienie po dodaniu elementu.

## <a name="configure-and-run-the-ios-project-optional"></a>Konfigurowanie i uruchamianie projektu systemu iOS (opcjonalnie)

Ta sekcja dotyczy uruchamiania projektu Xamarin iOS dla urządzeń z systemem iOS. Jeśli nie pracujesz z urządzeniami z systemem iOS, możesz pominąć tę sekcję.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Konfigurowanie Centrum powiadomień dla usługi APNS

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Następnie skonfiguruj ustawienia projektu dla systemu iOS w programie Xamarin Studio lub Visual Studio.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Dodawanie powiadomień wypychanych do aplikacji systemu iOS

1. W **iOS** projektu, otwórz AppDelegate.cs i dodaj następującą instrukcję na górze pliku kodu.

    ```csharp
    using Newtonsoft.Json.Linq;
    ```

2. W **elemencie AppDelegate** klasy, Dodaj zastąpienie **RegisteredForRemoteNotifications** zdarzenie, aby zarejestrować odbieranie powiadomień:

    ```csharp
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
    ```

3. W **elemencie AppDelegate**, również dodać następujące zastąpienie **DidReceiveRemoteNotification** programu obsługi zdarzeń:

    ```csharp
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
    ```

    Ta metoda obsługuje przychodzące powiadomienia, gdy aplikacja jest uruchomiona.

4. W **elemencie AppDelegate** klasy, Dodaj następujący kod do **FinishedLaunching** metody:

    ```csharp
    // Register for push notifications.
    var settings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert
        | UIUserNotificationType.Badge
        | UIUserNotificationType.Sound,
        new NSSet());

    UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications();
    ```

    Umożliwia to obsługę zdalne powiadomienia i rejestracja wypychanych żądań.

Aplikacji został zaktualizowany do obsługi powiadomień wypychanych.

#### <a name="test-push-notifications-in-your-ios-app"></a>Testowych powiadomień push w aplikacji systemu iOS

1. Kliknij prawym przyciskiem myszy projekt iOS, a następnie kliknij przycisk **Ustaw jako projekt startowy**.
2. Naciśnij klawisz **Uruchom** przycisk lub **F5** w programie Visual Studio, aby skompilować projekt i uruchomić aplikację w urządzeniu z systemem iOS. Następnie kliknij przycisk **OK** do akceptowania powiadomień wypychanych.

   > [!NOTE]
   > Należy jawnie zaakceptować powiadomień wypychanych z poziomu aplikacji. To żądanie występuje tylko przy pierwszym uruchomieniu aplikacji.

3. W aplikacji wpisz zadania, a następnie kliknij znak plus ( **+** ) ikona.
4. Sprawdź, czy Odebrano powiadomienie, a następnie kliknij **OK** aby odrzucić powiadomienie.

## <a name="configure-and-run-windows-projects-optional"></a>Konfigurowanie i uruchamianie projektów Windows (opcjonalnie)

Ta sekcja dotyczy uruchamiania aplikacji platformy Xamarin.Forms i WinPhone81 projektów dla urządzeń Windows. Te kroki obsługują także projekty platformy uniwersalnej Windows (UWP). Jeśli nie pracujesz z urządzeniami z systemem Windows, możesz pominąć tę sekcję.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Rejestrowanie aplikacji Windows dla usługi powiadomień wypychanych usługi powiadomień Windows (WNS)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurowanie Centrum powiadomień w przypadku usługi WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Dodawanie powiadomień wypychanych do aplikacji Windows

1. W programie Visual Studio, otwórz **App.xaml.cs** w Windows projektu i dodaj następujące instrukcje.

    ```csharp
    using Newtonsoft.Json.Linq;
    using Microsoft.WindowsAzure.MobileServices;
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    using <your_TodoItemManager_portable_class_namespace>;
    ```

    Zastąp `<your_TodoItemManager_portable_class_namespace>` się w przestrzeni nazw projektu przenośnych, które zawiera `TodoItemManager` klasy.

2. W pliku App.xaml.cs Dodaj następującą **InitNotificationsAsync** metody:

    ```csharp
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
    ```

    Ta metoda pobiera kanał powiadomień wypychanych i rejestruje szablon, aby otrzymywać powiadomienia o szablonach Centrum powiadomień. Powiadomienia szablonu, który obsługuje *messageParam* zostaną dostarczone do tego klienta.

3. W pliku App.xaml.cs aktualizacji **OnLaunched** definicję metody obsługi zdarzeń, dodając `async` modyfikator. Następnie dodaj następujący wiersz kodu na końcu metody:

    ```csharp
    await InitNotificationsAsync();
    ```

    Daje to gwarancję, że rejestracji powiadomień wypychanych jest utworzony lub odświeżane za każdym razem, gdy aplikacja jest uruchamiana. Należy to zrobić, aby zagwarantować kanału wypychania usługi WNS jest zawsze aktywny.  

4. W Eksploratorze rozwiązań dla programu Visual Studio, otwórz **Package.appxmanifest** pliku, a następnie ustaw **wyskakujące możliwością** do **tak** w obszarze **powiadomienia**.
5. Tworzenie aplikacji i upewnij się, że użytkownik nie ma błędów. Aplikację kliencką teraz należy zarejestrować odbieranie powiadomień szablonu z zaplecze funkcji Mobile Apps. W tej sekcji należy powtórzyć dla każdego projektu Windows w rozwiązaniu.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testowych powiadomień push w aplikacji Windows

1. W programie Visual Studio kliknij prawym przyciskiem myszy projekt Windows, a następnie kliknij przycisk **Ustaw jako projekt startowy**.
2. Kliknij przycisk **Uruchom**, aby skompilować projekt i uruchomić aplikację.
3. W aplikacji wpisz nazwę nowego czynność do wykonania, a następnie kliknij znak plus ( **+** ) ikonę, aby ją dodać.
4. Sprawdź, czy otrzyma powiadomienie po dodaniu elementu.

## <a name="next-steps"></a>Kolejne kroki

Możesz dowiedzieć się więcej na temat powiadomień wypychanych:

* [Wysyłanie powiadomień wypychanych z usługi Azure Mobile Apps](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Usługa Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Zdalne powiadomienia przy użyciu usługi Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [Diagnozowanie problemów z powiadomień wypychanych](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Istnieją różne powody, dlaczego powiadomienia porzucane może lub nie znajdą się na urządzeniach. W tym temacie dowiesz się, jak analizować i ustalić główną przyczynę niepowodzenia powiadomień wypychanych.

Można również przejść do jednej z następujących samouczków:

* [Dodawanie uwierzytelniania do aplikacji](app-service-mobile-xamarin-forms-get-started-users.md)  
  Dowiedz się, jak uwierzytelniać użytkowników aplikacji przy użyciu dostawcy tożsamości.
* [Włączanie synchronizacji w trybie offline dla aplikacji](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Dowiedz się, jak dodać obsługę aplikacji w trybie offline przy użyciu zaplecza funkcji Mobile Apps. Synchronizacja w trybie offline, użytkownicy mogą wchodzić w interakcje z aplikacją mobilną&mdash;wyświetlanie, dodawanie lub modyfikowanie danych&mdash;nawet w przypadku braku połączenia sieciowego.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: https://go.microsoft.com/fwlink/p/?LinkId=272333
