---
title: Wysyłanie powiadomień push do aplikacji platformy Xamarin.Android przy użyciu usługi Azure Notification Hubs | Microsoft Docs
description: Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia wypychane do aplikacji platformy Xamarin Android przy użyciu usługi Azure Notification Hubs.
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 94e8e813b537d304e62854b81979d433d0645115
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2018
ms.locfileid: "41919445"
---
# <a name="tutorial-push-notifications-to-xamarinandroid-apps-using-azure-notification-hubs"></a>Samouczek: wysyłanie powiadomień push do aplikacji platformy Xamarin.Android przy użyciu usługi Azure Notification Hubs
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Omówienie
Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia wypychane do aplikacji platformy Xamarin.Android przy użyciu usługi Azure Notification Hubs. Utworzysz pustą aplikację dla platformy Xamarin.Android służącą do odbierania powiadomień push przy użyciu usługi Firebase Cloud Messaging (FCM). Za pomocą centrum powiadomień będzie można wysyłać powiadomienia push do wszystkich urządzeń, na których działa ta aplikacja. Gotowy kod jest dostępny w przykładowej aplikacji [NotificationHubs][GitHub].

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Tworzenie projektu Firebase i włączanie usługi Firebase Cloud Messaging
> * Tworzenie centrum powiadomień
> * Tworzenie aplikacji platformy Xamarin.Android i łączenie jej z centrum powiadomień
> * Wysyłanie powiadomień testowych z witryny Azure Portal

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Program Visual Studio z platformą Xamarin] w systemie Windows lub [program Visual Studio dla komputerów Mac] w systemie OS X.
- Aktywne konto Google

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Tworzenie projektu Firebase i włączanie usługi Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="create-a-notification-hub"></a>Tworzenie centrum powiadomień
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcm-settings-for-the-notification-hub"></a>Konfigurowanie ustawień usługi GCM dla centrum powiadomień

1. Wybierz pozycję **Google (GCM)** w sekcji **USTAWIENIA POWIADOMIEŃ**. 
2. Wprowadź **starszy klucz serwera** zanotowany z konsoli Firebase Google Console. 
3. Wybierz pozycję **Zapisz** na pasku narzędzi. 

    ![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Centrum powiadomień jest skonfigurowane do pracy z usługą FCM i uzyskano parametry połączenia do rejestrowania aplikacji zarówno w celu odbierania, jak i wysyłania powiadomień wypychanych.

## <a name="create-xamarinandroid-app-and-connect-it-to-notification-hub"></a>Tworzenie aplikacji platformy Xamarin.Android i łączenie jej z centrum powiadomień

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Tworzenie projektu programu Visual Studio i dodawanie pakietów NuGet
1. W programie Visual Studio wskaż pozycję **Plik**, wybierz pozycję **Nowy**, a następnie wybierz pozycję **Projekt**. 
   
      ![Visual Studio — tworzenie nowego projektu dla systemu Android](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog.png)
2. W oknie **Eksploratora rozwiązań** rozwiń węzeł **Właściwości** i kliknij pozycję **AndroidManifest.xml**. Zaktualizuj nazwę pakietu tak, aby pasowała do nazwy pakietu wprowadzonej podczas dodawania usługi Firebase Cloud Messaging do projektu w konsoli Google Firebase Console. 

      ![Nazwa pakietu w usłudze GCM](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
3. Kliknij projekt prawym przyciskiem myszy i wybierz pozycję **Zarządzaj pakietami NuGet**. 
4. Wybierz kartę **Przeglądaj**. Wyszukaj pozycję **Xamarin.GooglePlayServices.Base**. Na liście wyników wybierz pozycję **Xamarin.GooglePlayServices.Base**. Następnie wybierz pozycję **Zainstaluj**. 

      ![Pakiet NuGet usług Google Play](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
5. W oknie **Menedżera pakietów NuGet** wyszukaj pozycję **Xamarin.Firebase.Messaging**. Na liście wyników wybierz pozycję **Xamarin.Firebase.Messaging**. Następnie wybierz pozycję **Zainstaluj**. 
6. Teraz wyszukaj pozycję **Xamarin.Azure.NotificationHubs.Android**. Na liście wyników wybierz pozycję **Xamarin.Azure.NotificationHubs.Android**. Następnie wybierz pozycję **Zainstaluj**. 

### <a name="add-the-google-services-json-file"></a>Dodawanie pliku JSON usług Google

1. Skopiuj plik **google-services.json** pobrany z konsoli Google Firebase Console do folderu projektu.
2. Dodaj plik **google-services.json** do projektu.
3. Wybierz plik **google-services.json** w oknie **Eksploratora rozwiązań**.
4. W okienku **Właściwości** ustaw **GoogleServicesJson** jako akcję kompilacji. Jeśli nie widzisz opcji **GoogleServicesJson**, zamknij program Visual Studio, uruchom go ponownie, otwórz projekt i spróbuj ponownie. 

      ![Akcja kompilacji GoogleServicesJson](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Konfigurowanie centrów powiadomień w projekcie

#### <a name="registering-with-firebase-cloud-messaging"></a>Rejestrowanie w usłudze Firebase Cloud Messaging

Otwórz plik **AndroidManifest.xml** i wstaw następujące elementy `<receiver>` do elementu `<application>`:

        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
          <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
          </intent-filter>
        </receiver>

1. Zbierz następujące informacje dotyczące aplikacji dla systemu Android i centrum powiadomień:
   
   * **Listen connection string (Parametry połączenia nasłuchiwania)**: na pulpicie nawigacyjnym w witrynie [Azure Portal] kliknij pozycję **Wyświetl parametry połączeń**. Skopiuj parametr połączenia *DefaultListenSharedAccessSignature* dla tej wartości.
   * **Nazwa centrum**: nazwa centrum z witryny [Azure Portal]. Na przykład *moje_centrum_powiadomien_2*.
     
2. Kliknij prawym przyciskiem myszy **projekt** w oknie **Eksploratora rozwiązań**, wskaż pozycję **Dodaj** i wybierz pozycję **Klasa**. 
4. Utwórz klasę **Constants.cs** w projekcie Xamarin i zdefiniuj w tej klasie następujące wartości stałych. Zastąp tekst zastępczy własnymi wartościami.
    
    ```csharp
        public static class Constants
        {
           public const string ListenConnectionString = "<Listen connection string>";
           public const string NotificationHubName = "<hub name>";
        }
    ```
3. W pliku **MainActivity.cs** dodaj następujące instrukcje using:
   
    ```csharp
        using Android.Util;
    ```
4. Dodaj do pliku **MainActivity.cs** zmienną instance w celu wyświetlania okna dialogowego alertu podczas działania aplikacji:
   
    ```csharp
        public const string TAG = "MainActivity";
    ```
5. Dodaj następujący kod do metody `OnCreate` w pliku **MainActivity.cs** po elemencie `base.OnCreate(savedInstanceState)`:

    ```csharp   
        if (Intent.Extras != null)
        {
            foreach (var key in Intent.Extras.KeySet())
            {
                if(key!=null)
                {
                    var value = Intent.Extras.GetString(key);
                    Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
                }
            }
        }
    ```
7. Utwórz nową klasę **MyFirebaseIIDService** podobnie jak klasę **Stałe**. 
8. W pliku **MyFirebaseIIDService.cs** dodaj następujące instrukcje using:
   
    ```csharp
    using Android.App;
    using Android.Util;
    using WindowsAzure.Messaging;
    using Firebase.Iid;
    ```

9. W pliku **MyFirebaseIIDService.cs** dodaj następującą deklarację **klasy**, która musi dziedziczyć z klasy **FirebaseInstanceIdService**:
   
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
        public class MyFirebaseIIDService : FirebaseInstanceIdService
    ```
10. W pliku **MyFirebaseIIDService.cs** dodaj następujący kod:
   
    ```csharp
        const string TAG = "MyFirebaseIIDService";
        NotificationHub hub;

        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "FCM token: " + refreshedToken);
            SendRegistrationToServer(refreshedToken);
        }

        void SendRegistrationToServer(string token)
        {
            // Register with Notification Hubs
            hub = new NotificationHub(Constants.NotificationHubName,
                                      Constants.ListenConnectionString, this);

            var tags = new List<string>() { };
            var regID = hub.Register(token, tags.ToArray()).RegistrationId;

            Log.Debug(TAG, $"Successful registration of ID {regID}");
        }
    ```
11. Utwórz nową klasę dla projektu, nadaj jej nazwę **MyFirebaseMessagingService**.
12. W pliku **MyFirebaseMessagingService.cs** dodaj następujące instrukcje using.
    
    ```csharp
        using Android.App;
        using Android.Util;
        using Firebase.Messaging;
    ```
13. Dodaj następujący kod nad deklaracją klasy, która musi dziedziczyć z klasy **FirebaseMessagingService**:
    
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class MyFirebaseMessagingService : FirebaseMessagingService
    ```    
14. W pliku **MyFirebaseMessagingService.cs** dodaj następujący kod:
    
    ```csharp
        const string TAG = "MyFirebaseMsgService";
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            if(message.GetNotification()!= null)
            {
                //These is how most messages will be received
                Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
                SendNotification(message.GetNotification().Body);
            }
            else 
            {
                //Only used for debugging payloads sent from the Azure portal
                SendNotification(message.Data.Values.First());

            }

        }

        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new Notification.Builder(this)
                        .SetContentTitle("FCM Message")
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(messageBody)
                        .SetAutoCancel(true)
                        .SetContentIntent(pendingIntent);

            var notificationManager = NotificationManager.FromContext(this);

            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```
15. **Skompiluj** projekt. 
16. **Uruchom** aplikację na urządzeniu lub załadowanym emulatorze.

## <a name="send-test-notification-from-the-azure-portal"></a>Wysyłanie powiadomienia testowego z witryny Azure Portal
Odbieranie powiadomień w aplikacji możesz przetestować za pomocą opcji *Wysyłanie testowe* w witrynie [Azure Portal]. Powoduje to wysłanie testowego powiadomienia push na urządzenie.

![Witryna Azure Portal — wysyłanie testowe](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

Powiadomienia wypychane są zwykle wysyłane w usłudze zaplecza, takiej jak Mobile Services czy ASP.NET, za pośrednictwem zgodnej biblioteki. Jeśli biblioteka nie jest dostępna w danym zapleczu, powiadomienia można również wysyłać bezpośrednio za pomocą interfejsu API REST.

## <a name="next-steps"></a>Następne kroki
W tym samouczku wysłano wyemitowane powiadomienia do wszystkich urządzeń z systemem Android zarejestrowanych w zapleczu. Aby dowiedzieć się, jak wysyłać powiadomienia push do konkretnych urządzeń z systemem Android, przejdź do następującego samouczka: 

> [!div class="nextstepaction"]
>[Wysyłanie powiadomień push do konkretnych urządzeń](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)


<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Program Visual Studio z platformą Xamarin]: https://docs.microsoft.com/visualstudio/install/install-visual-studio
[program Visual Studio dla komputerów Mac]: https://www.visualstudio.com/vs/visual-studio-mac/

[Azure Portal]: https://portal.azure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[GitHub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid
