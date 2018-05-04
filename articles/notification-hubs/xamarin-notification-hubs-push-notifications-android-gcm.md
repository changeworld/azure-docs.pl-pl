---
title: Wprowadzenie do usługi Notification Hubs dla aplikacji platformy Xamarin.Android | Microsoft Docs
description: Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia wypychane do aplikacji platformy Xamarin Android przy użyciu usługi Azure Notification Hubs.
author: jwhitedev
manager: kpiteira
editor: ''
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 7fee7813bbdcf902d5f5ae2d0af7540c8899ad25
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-notification-hubs-for-xamarinandroid-apps"></a>Wprowadzenie do usługi Notification Hubs dla aplikacji platformy Xamarin.Android
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Omówienie
Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia wypychane do aplikacji platformy Xamarin.Android przy użyciu usługi Azure Notification Hubs. Utworzysz pustą aplikację dla platformy Xamarin.Android służącą do odbierania powiadomień wypychanych przy użyciu usługi Firebase Cloud Messaging (FCM). Po zakończeniu będzie można za pomocą centrum powiadomień wysyłać powiadomienia wypychane do wszystkich urządzeń z tą aplikacją. Gotowy kod jest dostępny w przykładowej aplikacji [NotificationHubs][GitHub].

W tym samouczku został omówiony prosty scenariusz wysyłania przy użyciu usługi Notification Hubs.

## <a name="before-you-begin"></a>Przed rozpoczęciem
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Kompletny kod dla tego samouczka można znaleźć w witrynie GitHub [tutaj][GitHub].

## <a name="prerequisites"></a>Wymagania wstępne
Dla tego samouczka wymagane są następujące elementy:

* [Program Visual Studio z platformą Xamarin] w systemie Windows lub [program Visual Studio dla komputerów Mac] w systemie OS X.
* Aktywne konto Google

Wykonanie czynności opisanych w tym samouczku jest wymaganiem wstępnym dla wszystkich innych samouczków usługi Notification Hubs dotyczących aplikacji platformy Xamarin.Android.

> [!IMPORTANT]
> Do wykonania kroków tego samouczka potrzebne jest aktywne konto platformy Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).
> 
> 

## <a name="enable-firebase-cloud-messaging"></a>Włączanie usługi Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-your-notification-hub"></a>Konfigurowanie centrum powiadomień
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li><p>Wybierz kartę <b>Konfiguracja</b> w górnej części ekranu, wprowadź wartość <b>Klucz interfejsu API</b> uzyskaną w poprzedniej sekcji, a następnie wybierz pozycję <b>Zapisz</b>.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Centrum powiadomień jest skonfigurowane do pracy z usługą FCM i uzyskano parametry połączenia do rejestrowania aplikacji zarówno w celu odbierania, jak i wysyłania powiadomień wypychanych.

## <a name="connect-your-app-to-the-notification-hub"></a>Łączenie aplikacji z centrum powiadomień
Najpierw utwórz nowy projekt. 

1. W programie Visual Studio wybierz pozycje **New Solution** (Nowe rozwiązanie) > **Android App** (Aplikacja systemu Android), a następnie kliknij przycisk **Next** (Dalej).
   
      ![Visual Studio — tworzenie nowego projektu dla systemu Android][22]

2. Wprowadź nazwę aplikacji w polu **App Name** i identyfikator w polu **Identifier**. W obszarze **Target Platforms** (Platformy docelowe) wybierz platformy, które mają być obsługiwane, a następnie wybierz kolejno pozycje **Next** (Dalej) i **Create** (Utwórz).
   
      ![Visual Studio — konfiguracja aplikacji dla systemu Android][23]

    Spowoduje to utworzenie nowego projektu dla systemu Android.

3. Otwórz właściwości projektu, klikając utworzony projekt prawym przyciskiem myszy w widoku Solution (Rozwiązanie) i wybierając pozycję **Options** (Opcje). Wybierz pozycję **Android Application** (Aplikacja dla systemu Android) w sekcji **Build** (Kompilacja).
   
    Upewnij się, że pierwsza litera w polu **Package name** (Nazwa pakietu) jest mała.
   
   > [!IMPORTANT]
   > Nazwa pakietu musi zaczynać się od małej litery. W przeciwnym razie podczas rejestrowania aplikacji dla powiadomień wypychanych w kolejnym etapie zostaną zwrócone błędy manifestu aplikacji.
   > 
   > 
   
      ![Visual Studio — opcje projektu dla systemu Android][24]
4. Opcjonalnie możesz ustawić wartość w polu **Minimum Android version** (Minimalna wersja systemu Android) na inny poziom interfejsu API.
5. Opcjonalnie możesz ustawić wartość w polu **Target Android version** (Docelowa wersja systemu Android) na inną docelową wersję interfejsu API (wymagany poziom 8 lub wyższy).
6. Wybierz przycisk **OK** i zamknij okno dialogowe Project Options (Opcje projektu).

### <a name="add-the-required-packages-to-your-project"></a>Dodawanie wymaganych pakietów do projektu

1. Kliknij projekt prawym przyciskiem myszy i wybierz polecenie **Dodaj** > **Dodaj pakiety NuGet**.
2. Wyszukaj pakiet **Xamarin.Azure.NotificationHubs.Android** i dodaj go do projektu.
3. Wyszukaj pakiet **Xamarin.Firebase.Messaging** i dodaj go do projektu.

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
   
   * **Listen connection string (Parametry połączenia nasłuchiwania)**: na pulpicie nawigacyjnym w [witrynie Azure Portal] kliknij pozycję **Wyświetl parametry połączeń**. Skopiuj parametr połączenia *DefaultListenSharedAccessSignature* dla tej wartości.
   * **Hub name**: nazwa centrum wprowadzona w [witrynie Azure Portal]. Na przykład *moje_centrum_powiadomien_2*.
     
2. Utwórz klasę **Constants.cs** w projekcie Xamarin i zdefiniuj w tej klasie następujące wartości stałych. Zastąp tekst zastępczy własnymi wartościami.
    
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

6. Kliknij prawym przyciskiem myszy projekt i dodaj plik `google-services.json` pobrany wcześniej z projektu Firebase. Kliknij prawym przyciskiem myszy dodany plik i ustaw akcję kompilacji na `GoogleServicesJson`

    ![Visual Studio — konfigurowanie pliku google-services.json][25]

7. Utwórz nową klasę **MyFirebaseIIDService**.

8. W pliku **MyFirebaseIIDService.cs** dodaj następujące instrukcje using:
   
    ```csharp
        using System;
        using Android.App;
        using Firebase.Iid;
        using Android.Util;
        using WindowsAzure.Messaging;
        using System.Collections.Generic;
    ```

9. W pliku **MyFirebaseIIDService.cs** dodaj następujący kod powyżej deklaracji **klasy**, która musi dziedziczyć z klasy **FirebaseInstanceIdService**:
   
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
        using System;
        using System.Linq;
        using Android;
        using Android.App;
        using Android.Content;
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

15. Uruchom aplikację na urządzeniu lub załadowanym emulatorze.

## <a name="send-notifications-from-the-portal"></a>Wysyłanie powiadomień z portalu
Odbieranie powiadomień w aplikacji możesz przetestować za pomocą opcji *Wysyłanie testowe* w witrynie [witrynie Azure Portal]. Powoduje to wysłanie testowego powiadomienia push na urządzenie.

![Witryna Azure Portal — wysyłanie testowe][30]

Powiadomienia wypychane są zwykle wysyłane w usłudze zaplecza, takiej jak Mobile Services czy ASP.NET, za pośrednictwem zgodnej biblioteki. Jeśli biblioteka nie jest dostępna w danym zapleczu, powiadomienia można również wysyłać bezpośrednio za pomocą interfejsu API REST.

Poniższa lista zawiera kilka innych samouczków, z którymi warto się zapoznać, planując wysyłanie powiadomień:

* ASP.NET: zobacz [Wysyłanie powiadomień wypychanych do użytkowników przy użyciu usługi Notification Hubs].
* Zestaw SDK Java usługi Azure Notification Hubs: zobacz [Jak używać usługi Notification Hubs za pomocą języka Java](notification-hubs-java-push-notification-tutorial.md), aby zapoznać się ze sposobem wysyłania powiadomień za pomocą języka Java. To rozwiązanie przetestowano w programie Eclipse do tworzenia aplikacji dla systemu Android.
* PHP: zobacz [Jak używać usługi Notification Hubs za pomocą języka PHP](notification-hubs-php-push-notification-tutorial.md).

## <a name="next-steps"></a>Następne kroki
W tym prostym przykładzie wysłano powiadomienia do wszystkich urządzeń z systemem Android. Aby skierować je do określonych użytkowników, zapoznaj się z samouczkiem [Wysyłanie powiadomień wypychanych do użytkowników przy użyciu usługi Notification Hubs]. Jeśli chcesz podzielić użytkowników na grupy zainteresowań, zobacz [Wysyłanie najważniejszych wiadomości przy użyciu usługi Notification Hubs]. Aby uzyskać więcej informacji na temat korzystania z usługi Notification Hubs, zobacz [Wskazówki dotyczące usługi Notification Hubs] oraz [Poradnik dotyczący usługi Notification Hubs dla systemu Android].

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

[witrynie Azure Portal]: https://portal.azure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Wskazówki dotyczące usługi Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Poradnik dotyczący usługi Notification Hubs dla systemu Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Wysyłanie powiadomień wypychanych do użytkowników przy użyciu usługi Notification Hubs]: /manage/services/notification-hubs/notify-users-aspnet
[Wysyłanie najważniejszych wiadomości przy użyciu usługi Notification Hubs]: /manage/services/notification-hubs/breaking-news-dotnet
[GitHub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid
