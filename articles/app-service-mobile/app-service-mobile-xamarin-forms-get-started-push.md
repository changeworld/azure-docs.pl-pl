---
title: Dodawanie powiadomień wypychanych do aplikacji Xamarin.Forms
description: Dowiedz się, jak używać usług platformy Azure do wysyłania powiadomień wypychanych wieloplatformowych do aplikacji platformy Xamarin.Forms.
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: f23ac2d693492695c398893c103d5a77a0e93129
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461474"
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Dodawanie powiadomień wypychanych do aplikacji Xamarin.Forms

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Omówienie

W tym samouczku dodasz powiadomienia wypychane do wszystkich projektów, które powstały w wyniku [szybkiego uruchamiania platformy Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Oznacza to, że powiadomienie wypychane jest wysyłane do wszystkich klientów między platformami za każdym razem, gdy rekord jest wstawiany.

Jeśli nie korzystasz z pobranego projektu serwera szybkiego startu, potrzebny będzie pakiet rozszerzenia powiadomień wypychanych. Aby uzyskać więcej informacji, zobacz [Praca z zestawem SDK serwera wewnętrznej bazy danych .NET dla aplikacji mobilnych platformy Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne

W przypadku systemu iOS potrzebne będzie członkostwo w [programie deweloperskim Firmy Apple](https://developer.apple.com/programs/ios/) i fizyczne urządzenie z systemem iOS. [Symulator systemu iOS nie obsługuje powiadomień wypychanych](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-a-notification-hub"></a><a name="configure-hub"></a>Konfigurowanie centrum powiadomień

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Aktualizowanie projektu serwera w celu wysyłania powiadomień wypychanych

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Konfigurowanie i uruchamianie projektu systemu Android (opcjonalnie)

Wypełnij tę sekcję, aby włączyć powiadomienia wypychane dla projektu Xamarin.Forms Droid dla systemu Android.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Włącz firebase cloud messaging (FCM)

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>Konfigurowanie zaplecza aplikacji mobilnych do wysyłania żądań wypychania przy użyciu usługi FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Dodawanie powiadomień wypychanych do projektu systemu Android

Po skonfigurowaniu zaplecza z fcm można dodawać składniki i kody do klienta, aby zarejestrować się w FCM. Możesz również zarejestrować powiadomienia wypychane za pomocą usługi Azure Notification Hubs za pośrednictwem zaplecza aplikacji mobilnych i otrzymywać powiadomienia.

1. W projekcie **Droid** kliknij prawym przyciskiem myszy **odwołania > zarządzanie pakietami NuGet ...**.
1. W oknie Menedżera pakietów NuGet wyszukaj pakiet **Xamarin.Firebase.Messaging** i dodaj go do projektu.
1. We właściwościach projektu **Droid** ustaw aplikację do kompilacji przy użyciu systemu Android w wersji 7.0 lub nowszej.
1. Dodaj plik **google-services.json,** pobrany z konsoli Firebase, do katalogu głównego projektu **Droid** i ustaw jego akcję kompilacji na **GoogleServicesJson**. Aby uzyskać więcej informacji, zobacz [Dodawanie pliku JSON usług Google](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File).

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

#### <a name="implementing-the-firebase-instance-id-service"></a>Implementowanie usługi identyfikatora wystąpienia firebase

1. Dodaj nową klasę do projektu `FirebaseRegistrationService` **Droid** o nazwie `using` i upewnij się, że w górnej części pliku znajdują się następujące instrukcje:

    ```csharp
    using System.Threading.Tasks;
    using Android.App;
    using Android.Util;
    using Firebase.Iid;
    using Microsoft.WindowsAzure.MobileServices;
    ```

1. Zastąp pustą `FirebaseRegistrationService` klasę następującym kodem:

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

    Klasa `FirebaseRegistrationService` jest odpowiedzialna za generowanie tokenów zabezpieczających, które autoryzują aplikację do dostępu do fcm. Metoda `OnTokenRefresh` jest wywoływana, gdy aplikacja odbiera token rejestracji z FCM. Metoda pobiera token z `FirebaseInstanceId.Instance.Token` właściwości, która jest asynchronicznie aktualizowana przez FCM. Metoda `OnTokenRefresh` jest rzadko wywoływana, ponieważ token jest aktualizowany tylko wtedy, gdy aplikacja jest zainstalowana lub odinstalowana, gdy użytkownik usuwa dane aplikacji, gdy aplikacja usuwa identyfikator wystąpienia lub gdy bezpieczeństwo tokenu zostało naruszone. Ponadto usługa identyfikator wystąpienia FCM zażąda, aby aplikacja okresowo odświeża swój token, zazwyczaj co 6 miesięcy.

    Metoda `OnTokenRefresh` wywołuje również `SendRegistrationTokenToAzureNotificationHub` metodę, która jest używana do skojarzenia tokenu rejestracji użytkownika z Centrum powiadomień platformy Azure.

#### <a name="registering-with-the-azure-notification-hub"></a>Rejestrowanie się w Centrum powiadomień platformy Azure

1. Dodaj nową klasę do projektu `AzureNotificationHubService` **Droid** o nazwie `using` i upewnij się, że w górnej części pliku znajdują się następujące instrukcje:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Android.Util;
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

1. Zastąp pustą `AzureNotificationHubService` klasę następującym kodem:

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

    Metoda `RegisterAsync` tworzy prosty szablon wiadomości powiadomień jako JSON i rejestruje się, aby otrzymywać powiadomienia szablonu z centrum powiadomień, przy użyciu tokenu rejestracji Firebase. Gwarantuje to, że wszystkie powiadomienia wysyłane z usługi Azure Notification Hub będą kierowane na urządzenie reprezentowane przez token rejestracji.

#### <a name="displaying-the-contents-of-a-push-notification"></a>Wyświetlanie zawartości powiadomienia wypychanego

1. Dodaj nową klasę do projektu `FirebaseNotificationService` **Droid** o nazwie `using` i upewnij się, że w górnej części pliku znajdują się następujące instrukcje:

    ```csharp
    using Android.App;
    using Android.Content;
    using Android.Media;
    using Android.Support.V7.App;
    using Android.Util;
    using Firebase.Messaging;
    ```

1. Zastąp pustą `FirebaseNotificationService` klasę następującym kodem:

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
            //Unique request code to avoid PendingIntent collision.
            var requestCode = new Random().Next();
            var pendingIntent = PendingIntent.GetActivity(this, requestCode, intent, PendingIntentFlags.OneShot);
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

    Metoda, `OnMessageReceived` która jest wywoływana, gdy aplikacja odbiera powiadomienie z FCM, wyodrębnia zawartość wiadomości i wywołuje `SendNotification` metodę. Ta metoda konwertuje zawartość wiadomości na lokalne powiadomienie, które jest uruchamiane, gdy aplikacja jest uruchomiona, z powiadomieniem wyświetlanym w obszarze powiadomień.

Teraz możesz przystąpić do testowania powiadomień push w aplikacji uruchomionej na urządzeniu z systemem Android lub emulatorze.

### <a name="test-push-notifications-in-your-android-app"></a>Testowanie powiadomień wypychanych w aplikacji na Androida

Pierwsze dwa kroki są wymagane tylko podczas testowania na emulatorze.

1. Upewnij się, że wdrażasz lub debugujesz na urządzeniu lub emulatorze skonfigurowanym w usługach Google Play. Można to zweryfikować, sprawdzając, czy aplikacje **Play** są zainstalowane na urządzeniu lub emulatorze.
2. Dodaj konto Google do urządzenia z Androidem, klikając**pozycję Ustawienia** >  **aplikacji** > **Dodaj konto**. Następnie postępuj zgodnie z instrukcjami, aby dodać istniejące konto Google do urządzenia lub utworzyć nowe.
3. W programie Visual Studio lub Xamarin Studio kliknij prawym przyciskiem myszy projekt **Droid** i kliknij polecenie **Ustaw jako projekt startowy**.
4. Kliknij **przycisk Uruchom,** aby utworzyć projekt i uruchomić aplikację na urządzeniu z androidem lub emulatorze.
5. W aplikacji wpisz zadanie, a następnie kliknij**+** ikonę plus ( ).
6. Sprawdź, czy powiadomienie jest odbierane po dodaniu elementu.

## <a name="configure-and-run-the-ios-project-optional"></a>Konfigurowanie i uruchamianie projektu systemu iOS (opcjonalnie)

Ta sekcja dotyczy uruchamiania projektu Xamarin iOS dla urządzeń z systemem iOS. Jeśli nie pracujesz z urządzeniami z systemem iOS, możesz pominąć tę sekcję.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Konfigurowanie centrum powiadomień dla usługi APNS

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Następnie skonfigurujesz ustawienie projektu systemu iOS w programie Xamarin Studio lub Visual Studio.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Dodawanie powiadomień wypychanych do aplikacji na iOS

1. W projekcie **systemu iOS** otwórz AppDelegate.cs i dodaj następującą instrukcję do górnej części pliku kodu.

    ```csharp
    using Newtonsoft.Json.Linq;
    ```

2. W **AppDelegate** klasy, dodać zastąpienie dla **RegisteredForRemoteNotifications** zdarzenia, aby zarejestrować się do powiadomień:

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

3. W **AppDelegate**, również dodać następujące zastąpienie **didReceiveRemoteNotification** obsługi zdarzenia:

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

    Ta metoda obsługuje powiadomienia przychodzące, gdy aplikacja jest uruchomiona.

4. W **AppDelegate** klasy, dodaj następujący kod do **FinishedLaunching** metody:

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

    Umożliwia to obsługę zdalnych powiadomień i żądań rejestracji wypychania.

Aplikacja jest teraz aktualizowana w celu obsługi powiadomień wypychanych.

#### <a name="test-push-notifications-in-your-ios-app"></a>Testowanie powiadomień wypychanych w aplikacji na iOS

1. Kliknij prawym przyciskiem myszy projekt systemu iOS, a następnie kliknij polecenie **Ustaw jako projekt startowy**.
2. Naciśnij przycisk **Uruchom** lub **F5** w programie Visual Studio, aby utworzyć projekt i uruchomić aplikację na urządzeniu z systemem iOS. Następnie kliknij przycisk **OK,** aby zaakceptować powiadomienia wypychane.

   > [!NOTE]
   > Musisz jawnie akceptować powiadomienia wypychane z aplikacji. To żądanie występuje tylko przy pierwszym uruchomieniu aplikacji.

3. W aplikacji wpisz zadanie, a następnie kliknij**+** ikonę plus ( ).
4. Sprawdź, czy powiadomienie zostało odebrane, a następnie kliknij przycisk **OK,** aby odrzucić powiadomienie.

## <a name="configure-and-run-windows-projects-optional"></a>Konfigurowanie i uruchamianie projektów systemu Windows (opcjonalnie)

Ta sekcja służy do uruchamiania projektów Xamarin.Forms WinApp i WinPhone81 dla urządzeń z systemem Windows. Te kroki obsługują również projekty platformy uniwersalnej systemu Windows (UWP). Jeśli nie pracujesz z urządzeniami z systemem Windows, możesz pominąć tę sekcję.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Rejestrowanie aplikacji systemu Windows w celu otrzymywani powiadomień wypychanych w usłudze powiadomień systemu Windows (WNS)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurowanie centrum powiadomień dla usługi WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Dodawanie powiadomień wypychanych do aplikacji systemu Windows

1. W programie Visual Studio otwórz **App.xaml.cs** w projekcie systemu Windows i dodaj następujące instrukcje.

    ```csharp
    using Newtonsoft.Json.Linq;
    using Microsoft.WindowsAzure.MobileServices;
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    using <your_TodoItemManager_portable_class_namespace>;
    ```

    Zamień `<your_TodoItemManager_portable_class_namespace>` obszar nazw projektu przenośnego, który zawiera `TodoItemManager` klasę.

2. W App.xaml.cs dodaj następującą metodę **InitNotificationsAsync:**

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

    Ta metoda pobiera kanał powiadomień wypychanych i rejestruje szablon do odbierania powiadomień szablonu z Centrum powiadomień. Powiadomienie szablonu, który obsługuje *messageParam* zostaną dostarczone do tego klienta.

3. W App.xaml.cs zaktualizuj definicję metody obsługi zdarzeń **OnLaunched,** dodając `async` modyfikator. Następnie dodaj następujący wiersz kodu na końcu metody:

    ```csharp
    await InitNotificationsAsync();
    ```

    Dzięki temu rejestracja powiadomień wypychanych jest tworzona lub odświeżana za każdym razem, gdy aplikacja jest uruchamiana. Należy to zrobić, aby zagwarantować, że kanał push WNS jest zawsze aktywny.  

4. W Eksploratorze rozwiązań dla programu Visual Studio otwórz plik **Package.appxmanifest** i ustaw **opcję Wyskakuj na** **Tak** w obszarze **Powiadomienia**.
5. Skompiluj aplikację i sprawdź, czy nie ma żadnych błędów. Aplikacja kliencka powinna teraz zarejestrować się w celu otrzymywannia powiadomień o szablonie z zaplecza aplikacji mobilnych. Powtórz tę sekcję dla każdego projektu systemu Windows w rozwiązaniu.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testowanie powiadomień wypychanych w aplikacji systemu Windows

1. W programie Visual Studio kliknij prawym przyciskiem myszy projekt systemu Windows, a następnie kliknij polecenie **Ustaw jako projekt startowy**.
2. Kliknij przycisk **Uruchom**, aby skompilować projekt i uruchomić aplikację.
3. W aplikacji wpisz nazwę nowego todoitem, a następnie kliknij**+** ikonę plus ( ), aby ją dodać.
4. Sprawdź, czy powiadomienie jest odbierane po dodaniu elementu.

## <a name="next-steps"></a>Następne kroki

Więcej informacji o powiadomieniach wypychanych:

* [Wysyłanie powiadomień wypychanych z aplikacji mobilnych platformy Azure](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Zdalne powiadomienia z firebase cloud messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [Diagnozowanie problemów z powiadomieniami wypychanych](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Istnieją różne powody, dla których powiadomienia mogą zostać usunięte lub nie trafiają do urządzeń. W tym temacie pokazano, jak analizować i dowiedzieć się główną przyczynę błędów powiadomień wypychanych.

Można również przejść do jednego z następujących samouczków:

* [Dodawanie uwierzytelniania do aplikacji](app-service-mobile-xamarin-forms-get-started-users.md)  
  Dowiedz się, jak uwierzytelniać użytkowników aplikacji przy użyciu dostawcy tożsamości.
* [Włączanie synchronizacji w trybie offline dla aplikacji](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Dowiedz się, jak dodać obsługę aplikacji w trybie offline przy użyciu zaplecza funkcji Mobile Apps. Dzięki synchronizacji w trybie offline&mdash;użytkownicy mogą wchodzić&mdash;w interakcje z przeglądaniem, dodawaniem lub modyfikowaniem danych aplikacji mobilnej, nawet jeśli nie ma połączenia sieciowego.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: https://go.microsoft.com/fwlink/p/?LinkId=272333
