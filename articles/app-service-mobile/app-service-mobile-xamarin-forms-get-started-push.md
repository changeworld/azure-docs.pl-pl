---
title: Dodawanie powiadomień wypychanych do aplikacji platformy Xamarin. Forms | Microsoft Docs
description: Dowiedz się, jak wysyłać wieloplatformowe powiadomienia wypychane do aplikacji platformy Xamarin. Forms przy użyciu usług Azure.
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
ms.openlocfilehash: 8ce307df954575b3204f7a4b3f46af1f4a9c3089
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027448"
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Dodawanie powiadomień wypychanych do aplikacji platformy Xamarin. Forms

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center obsługuje kompleksowe i zintegrowane usługi centralne do tworzenia aplikacji mobilnych. Deweloperzy mogą używać usług **kompilowania**, **testowania** i **dystrybucji** , aby skonfigurować ciągłą integrację i potok dostarczania. Po wdrożeniu aplikacji deweloperzy mogą monitorować stan i użycie swojej aplikacji przy użyciu usług **analizy** i **diagnostyki** oraz angażować się z użytkownikami za pomocą usługi **wypychania** . Deweloperzy mogą również korzystać z **uwierzytelniania** w celu uwierzytelniania użytkowników i usługi **danych** w celu utrwalania i synchronizowania danych aplikacji w chmurze.
> Jeśli chcesz zintegrować usługi w chmurze w swojej aplikacji mobilnej, zarejestruj się w usłudze App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) już dziś.

## <a name="overview"></a>Przegląd

W tym samouczku dowiesz się, jak dodać powiadomienia wypychane do wszystkich projektów, które powstały w ramach [szybkiego startu Xamarin. Forms](app-service-mobile-xamarin-forms-get-started.md). Oznacza to, że powiadomienie wypychane jest wysyłane do wszystkich klientów na wielu platformach za każdym razem, gdy rekord zostanie wstawiony.

Jeśli nie używasz pobranego projektu szybkiego startu serwera, będzie potrzebny pakiet rozszerzenia powiadomień wypychanych. Aby uzyskać więcej informacji, zobacz temat [współpraca z zestawem SDK serwera zaplecza platformy .NET dla platformy Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne

W przypadku systemu iOS wymagane jest [członkostwo w programie Apple Developer](https://developer.apple.com/programs/ios/) i fizyczne urządzenie z systemem iOS. [Symulator systemu iOS nie obsługuje powiadomień wypychanych](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>Konfigurowanie centrum powiadomień

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Aktualizowanie projektu serwera w celu wysyłania powiadomień wypychanych

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Konfigurowanie i uruchamianie projektu systemu Android (opcjonalnie)

Wykonaj tę sekcję, aby włączyć powiadomienia wypychane dla projektu Xamarin. Forms Droid dla systemu Android.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Włącz funkcję Firebase Cloud Messaging (FCM)

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>Konfigurowanie zaplecza Mobile Apps w celu wysyłania żądań push przy użyciu FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Dodawanie powiadomień wypychanych do projektu systemu Android

Zaplecze skonfigurowanym za pomocą FCM można dodać składniki i kody do klienta w celu zarejestrowania się w FCM. Możesz również rejestrować powiadomienia wypychane za pomocą usługi Azure Notification Hubs za pomocą zaplecza Mobile Apps i odbierać powiadomienia.

1. W projekcie **Droid** kliknij prawym przyciskiem myszy pozycję **odwołania > Zarządzaj pakietami NuGet.** ...
1. W oknie Menedżer pakietów NuGet Wyszukaj pakiet **Xamarin. Firebase. Messaging** i dodaj go do projektu.
1. We właściwościach projektu projektu **Droid** Ustaw aplikację na kompilację przy użyciu systemu Android w wersji 7,0 lub nowszej.
1. Dodaj plik **Google-Services. JSON** pobrany z konsoli Firebase do katalogu głównego projektu **Droid** i ustaw jego akcję kompilacji na **GoogleServicesJson**. Aby uzyskać więcej informacji, zobacz [Dodawanie pliku JSON usług Google Services](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File).

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

#### <a name="implementing-the-firebase-instance-id-service"></a>Implementowanie usługi identyfikatora wystąpienia Firebase

1. Dodaj nową klasę do projektu **Droid** o nazwie `FirebaseRegistrationService` i upewnij się, że na początku pliku znajdują się następujące instrukcje `using`:

    ```csharp
    using System.Threading.Tasks;
    using Android.App;
    using Android.Util;
    using Firebase.Iid;
    using Microsoft.WindowsAzure.MobileServices;
    ```

1. Zastąp pustą klasę `FirebaseRegistrationService` następującym kodem:

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

    Klasa `FirebaseRegistrationService` jest odpowiedzialna za generowanie tokenów zabezpieczających, które autoryzują aplikację w celu uzyskania dostępu do FCM. Metoda `OnTokenRefresh` jest wywoływana, gdy aplikacja otrzymuje token rejestracji z FCM. Metoda pobiera token z właściwości `FirebaseInstanceId.Instance.Token`, która jest asynchronicznie aktualizowana przez FCM. Metoda `OnTokenRefresh` jest rzadko wywoływana, ponieważ token jest aktualizowany tylko wtedy, gdy aplikacja jest zainstalowana lub odinstalowana, gdy użytkownik usuwa dane aplikacji, gdy aplikacja wymazuje identyfikator wystąpienia lub gdy zabezpieczenia tokenu zostały naruszone. Ponadto usługa identyfikatora wystąpienia FCM będzie żądać, aby aplikacja odświeża swój token okresowo, zazwyczaj co 6 miesięcy.

    Metoda `OnTokenRefresh` wywołuje również metodę `SendRegistrationTokenToAzureNotificationHub`, która jest używana do kojarzenia tokena rejestracji użytkownika z centrum powiadomień platformy Azure.

#### <a name="registering-with-the-azure-notification-hub"></a>Rejestrowanie w usłudze Azure Notification Hub

1. Dodaj nową klasę do projektu **Droid** o nazwie `AzureNotificationHubService` i upewnij się, że na początku pliku znajdują się następujące instrukcje `using`:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Android.Util;
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

1. Zastąp pustą klasę `AzureNotificationHubService` następującym kodem:

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

    Metoda `RegisterAsync` tworzy prosty szablon komunikatu z powiadomieniem jako kod JSON i rejestruje otrzymywanie powiadomień o szablonach z centrum powiadomień przy użyciu tokenu rejestracji Firebase. Dzięki temu wszystkie powiadomienia wysyłane z centrum powiadomień platformy Azure będą ukierunkowane na urządzenie reprezentowane przez token rejestracji.

#### <a name="displaying-the-contents-of-a-push-notification"></a>Wyświetlanie zawartości powiadomienia wypychanego

1. Dodaj nową klasę do projektu **Droid** o nazwie `FirebaseNotificationService` i upewnij się, że na początku pliku znajdują się następujące instrukcje `using`:

    ```csharp
    using Android.App;
    using Android.Content;
    using Android.Media;
    using Android.Support.V7.App;
    using Android.Util;
    using Firebase.Messaging;
    ```

1. Zastąp pustą klasę `FirebaseNotificationService` następującym kodem:

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

    Metoda `OnMessageReceived` wywoływana, gdy aplikacja odbiera powiadomienie z FCM, wyodrębnia zawartość komunikatu i wywołuje metodę `SendNotification`. Ta metoda umożliwia konwersję zawartości komunikatu do lokalnego powiadomienia, które zostało uruchomione, gdy aplikacja jest uruchomiona, z powiadomieniem wyświetlanym w obszarze powiadomień.

Teraz możesz zacząć testować powiadomienia wypychane w aplikacji uruchomionej na urządzeniu z systemem Android lub w emulatorze.

### <a name="test-push-notifications-in-your-android-app"></a>Testowanie powiadomień wypychanych w aplikacji systemu Android

Pierwsze dwa kroki są wymagane tylko w przypadku testowania na emulatorze.

1. Upewnij się, że wdrażasz program lub debugujesz go na urządzeniu lub w emulatorze skonfigurowanym za pomocą Usługi Google Play. Można to sprawdzić, sprawdzając, czy aplikacje **odtwarzania** są zainstalowane na urządzeniu lub w emulatorze.
2. Dodaj konto Google do urządzenia z systemem Android, klikając pozycję **aplikacje** > **Ustawienia** > **Dodaj konto**. Następnie postępuj zgodnie z monitami, aby dodać istniejące konto Google do urządzenia lub utworzyć nowe.
3. W programie Visual Studio lub Xamarin Studio kliknij prawym przyciskiem myszy projekt **Droid** , a następnie kliknij pozycję **Ustaw jako projekt startowy**.
4. Kliknij przycisk **Uruchom** , aby skompilować projekt i uruchomić aplikację na urządzeniu z systemem Android lub w emulatorze.
5. W aplikacji wpisz zadanie, a następnie kliknij ikonę plus ( **+** ).
6. Sprawdź, czy odebrano powiadomienie po dodaniu elementu.

## <a name="configure-and-run-the-ios-project-optional"></a>Konfigurowanie i uruchamianie projektu dla systemu iOS (opcjonalnie)

Ta sekcja dotyczy uruchamiania projektu Xamarin iOS dla urządzeń z systemem iOS. Jeśli nie pracujesz z urządzeniami z systemem iOS, możesz pominąć tę sekcję.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Konfigurowanie centrum powiadomień dla usługi APNS

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Następnie skonfigurujesz ustawienie projektu dla systemu iOS w Xamarin Studio lub Visual Studio.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Dodawanie powiadomień wypychanych do aplikacji systemu iOS

1. W projekcie **systemu iOS** Otwórz AppDelegate.cs i Dodaj następującą instrukcję na początku pliku kodu.

    ```csharp
    using Newtonsoft.Json.Linq;
    ```

2. W klasie **AppDelegate** Dodaj zastąpienie dla zdarzenia **RegisteredForRemoteNotifications** , aby zarejestrować się w celu otrzymywania powiadomień:

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

3. W programie **AppDelegate**Dodaj również następujące zastąpienie dla programu obsługi zdarzeń **DidReceiveRemoteNotification** :

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

4. W klasie **AppDelegate** Dodaj następujący kod do metody **FinishedLaunching** :

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

    Umożliwia to obsługę powiadomień zdalnych i żądań rejestracji wypychanej.

Twoja aplikacja została zaktualizowana w celu obsługi powiadomień wypychanych.

#### <a name="test-push-notifications-in-your-ios-app"></a>Testowanie powiadomień wypychanych w aplikacji systemu iOS

1. Kliknij prawym przyciskiem myszy projekt iOS, a następnie kliknij pozycję **Ustaw jako projekt startowy**.
2. Naciśnij przycisk **Uruchom** lub **F5** w programie Visual Studio, aby skompilować projekt i uruchomić aplikację na urządzeniu z systemem iOS. Następnie kliknij przycisk **OK** , aby zaakceptować powiadomienia wypychane.

   > [!NOTE]
   > Musisz jawnie zaakceptować powiadomienia wypychane z aplikacji. To żądanie występuje tylko podczas pierwszego uruchomienia aplikacji.

3. W aplikacji wpisz zadanie, a następnie kliknij ikonę plus ( **+** ).
4. Sprawdź, czy odebrano powiadomienie, a następnie kliknij przycisk **OK** , aby odrzucić powiadomienie.

## <a name="configure-and-run-windows-projects-optional"></a>Konfigurowanie i uruchamianie projektów systemu Windows (opcjonalnie)

Ta sekcja służy do uruchamiania projektów Xamarin. Forms WinApp i WinPhone81 dla urządzeń z systemem Windows. Te kroki obsługują również projekty platforma uniwersalna systemu Windows (platformy UWP). Jeśli nie pracujesz z urządzeniami z systemem Windows, możesz pominąć tę sekcję.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Rejestrowanie aplikacji systemu Windows na potrzeby powiadomień wypychanych za pomocą usługi powiadomień systemu Windows (WNS)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurowanie centrum powiadomień dla WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Dodawanie powiadomień wypychanych do aplikacji systemu Windows

1. W programie Visual Studio Otwórz **App.XAML.cs** w projekcie systemu Windows i Dodaj następujące instrukcje.

    ```csharp
    using Newtonsoft.Json.Linq;
    using Microsoft.WindowsAzure.MobileServices;
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    using <your_TodoItemManager_portable_class_namespace>;
    ```

    Zastąp `<your_TodoItemManager_portable_class_namespace>` z przestrzenią nazw projektu przenośnego, który zawiera klasę `TodoItemManager`.

2. W App.xaml.cs Dodaj następującą metodę **następującą initnotificationsasync** :

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

    Ta metoda pobiera kanał powiadomień wypychanych i rejestruje szablon w celu otrzymywania powiadomień o szablonach z centrum powiadomień. Powiadomienie o szablonie, które obsługuje *messageParam* , zostanie dostarczone do tego klienta.

3. W App.xaml.cs zaktualizuj definicję metody obsługi zdarzeń **OnStarted** , dodając modyfikator `async`. Następnie Dodaj następujący wiersz kodu na końcu metody:

    ```csharp
    await InitNotificationsAsync();
    ```

    Dzięki temu Rejestracja powiadomień wypychanych jest tworzona lub odświeżana za każdym razem, gdy aplikacja zostanie uruchomiona. Należy to zrobić w celu zagwarantowania, że kanał push WNS jest zawsze aktywny.  

4. W Eksplorator rozwiązań dla programu Visual Studio Otwórz plik **Package. appxmanifest** i ustaw opcję **wyskakujące** z opcją **tak** w obszarze **powiadomienia**.
5. Skompiluj aplikację i sprawdź, czy nie wystąpiły żadne błędy. Twoja aplikacja kliencka powinna teraz rejestrować się w celu otrzymywania powiadomień dotyczących szablonów z zaplecza Mobile Apps. Powtórz tę sekcję dla każdego projektu systemu Windows w rozwiązaniu.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testowanie powiadomień wypychanych w aplikacji systemu Windows

1. W programie Visual Studio kliknij prawym przyciskiem myszy projekt systemu Windows, a następnie kliknij pozycję **Ustaw jako projekt startowy**.
2. Kliknij przycisk **Uruchom**, aby skompilować projekt i uruchomić aplikację.
3. W aplikacji wpisz nazwę nowego TodoItem, a następnie kliknij ikonę plus ( **+** ), aby ją dodać.
4. Sprawdź, czy odebrano powiadomienie po dodaniu elementu.

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat powiadomień wypychanych można znaleźć w tematach:

* [Wysyłanie powiadomień wypychanych z usługi Azure Mobile Apps](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Obsługa komunikatów w chmurze Firebase](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Powiadomienia zdalne z obsługą komunikatów w chmurze Firebase](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [Diagnozuj problemy z powiadomieniem wypychanym](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Istnieją różne przyczyny, dla których powiadomienia mogą zostać porzucone lub nie kończyć się na urządzeniach. W tym temacie pokazano, jak analizować i ustalić główną przyczynę niepowodzeń powiadomień wypychanych.

Możesz również przejść do jednego z następujących samouczków:

* [Dodawanie uwierzytelniania do aplikacji](app-service-mobile-xamarin-forms-get-started-users.md)  
  Dowiedz się, jak uwierzytelniać użytkowników aplikacji przy użyciu dostawcy tożsamości.
* [Włączanie synchronizacji w trybie offline dla aplikacji](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Dowiedz się, jak dodać obsługę aplikacji w trybie offline przy użyciu zaplecza funkcji Mobile Apps. Dzięki synchronizacji w trybie offline użytkownicy mogą korzystać z aplikacji mobilnej @ no__t-0viewing, dodając lub modyfikując dane @ no__t-1even, gdy nie ma połączenia sieciowego.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: https://go.microsoft.com/fwlink/p/?LinkId=272333
