---
title: Wysyłanie powiadomień push do aplikacji systemu Android przy użyciu usług Azure Notification Hubs i Firebase Cloud Messaging | Microsoft Docs
description: Korzystając z tego samouczka, dowiesz się, jak za pomocą usług Azure Notification Hubs i Google Firebase Cloud Messaging wysyłać powiadomienia push do urządzeń z systemem Android.
services: notification-hubs
documentationcenter: android
keywords: powiadomienia wypychane, powiadomienie wypychane, powiadomienia wypychane w systemie android, firebase cloud messaging
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 02298560-da61-4bbb-b07c-e79bd520e420
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/30/2019
ms.author: jowargo
ms.openlocfilehash: c21b1e38077575fc49221150a61693a23aa408a3
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509130"
---
# <a name="tutorial-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-firebase-cloud-messaging"></a>Samouczek: wysyłanie powiadomień push do urządzeń z systemem Android przy użyciu usług Azure Notification Hubs i Google Firebase Cloud Messaging

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia push do aplikacji dla systemu Android przy użyciu usług Azure Notification Hubs i Firebase Cloud Messaging (FCM). W tym samouczku tworzysz pustą aplikację dla systemu Android służącą do odbierania powiadomień push przy użyciu usługi Firebase Cloud Messaging (FCM).

Kompletny kod dla tego samouczka można pobrać [z serwisu GitHub](https://github.com/Azure/azure-notificationhubs-android/tree/master/samples/FCMTutorialApp).

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Tworzenie projektu programu Android Studio.
> * Tworzenie projektu Firebase obsługującego usługę Firebase Cloud Messaging.
> * Utwórz koncentrator.
> * Łączenie aplikacji z Centrum.
> * Testowanie aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka potrzebne jest aktywne konto platformy Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać więcej informacji, zobacz [bezpłatnej wersji próbnej Azure](https://azure.microsoft.com/free/). 

Wymagane są również następujące elementy: 

* Najnowszą wersję [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797)
* System android 2.3 lub nowszy dla usługi Firebase Cloud Messaging
* Google Repository w wersji 27 lub nowszej dla usługi Firebase Cloud Messaging
* Usługi Google Play Services 9.0.2 lub nowsze dla usługi Firebase Cloud Messaging

Wykonanie kroków tego samouczka jest wymagane do wykonywania wszystkich innych samouczków usługi Notification Hubs dla aplikacji systemu Android.

## <a name="create-an-android-studio-project"></a>Tworzenie projektu programu Android Studio

1. Uruchom program Android Studio.
2. Wybierz **pliku**, wskaż polecenie **New**, a następnie wybierz pozycję **nowy projekt**. 
2. Na **wybierz projekt** wybierz opcję **puste działanie**, a następnie wybierz pozycję **dalej**. 
3. Na **Skonfiguruj projekt** strony, wykonaj następujące czynności: 
    1. Wprowadź nazwę aplikacji.
    2. Określ lokalizację, w której chcesz zapisać pliki projektu. 
    3. Wybierz pozycję **Finish** (Zakończ). 

        ![Konfigurowanie projektu](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>Tworzenie projektu Firebase obsługującego usługę FCM

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-hub"></a>Konfigurowanie Centrum

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Konfigurowanie ustawień usługi Firebase Cloud Messaging na potrzeby centrum

1. W okienku po lewej stronie w obszarze **ustawień** wybierz **Google (GCM/FCM)** . 
2. Wprowadź **klucz serwera** w projekcie usługi FCM, który został wcześniej zapisany. 
3. Na pasku narzędzi wybierz **Zapisz**. 

    ![Centrum powiadomień platformy Azure — Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Witryna Azure portal wyświetla komunikat w alertach pomyślnie zaktualizowano Centrum. Przycisk **Save** (Zapisz) będzie wyłączony. 

Twoje Centrum jest teraz skonfigurowane do pracy przy użyciu usługi Firebase Cloud Messaging. Masz również parametry połączenia, które są niezbędne do wysyłania powiadomień na urządzeniu i Zarejestruj aplikację, aby otrzymywać powiadomienia.

## <a id="connecting-app"></a>Łączenie aplikacji z centrum powiadomień

### <a name="add-google-play-services-to-the-project"></a>Dodawanie usług Google Play do projektu

[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="add-azure-notification-hubs-libraries"></a>Dodawanie bibliotek usługi Azure Notification Hubs

1. W plik Build.Gradle dla aplikacji Dodaj następujące wiersze w sekcji dependencies.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

2. Dodaj następujące repozytorium po sekcji dependencies.

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="add-google-firebase-support"></a>Dodawanie obsługi usługi Google Firebase

1. W pliku Build.Gradle dla aplikacji, Dodaj następujące wiersze w **zależności** sekcji, jeśli jeszcze nie istnieją. 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:17.3.4'
    ```

2. Dodaj następująca wtyczka na końcu pliku, jeśli nie jest już istnieje. 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```
3. Wybierz pozycję **Sync Now** (Synchronizuj teraz) na pasku narzędzi.

### <a name="update-the-androidmanifestxml-file"></a>Aktualizowanie pliku AndroidManifest.xml

1. Po otrzymaniu tokenu rejestracji usługi FCM, możesz użyć go do [rejestrowanie w usłudze Azure Notification Hubs](notification-hubs-push-notification-registration-management.md). Obsługę rejestracji w tle za pomocą `IntentService` o nazwie `RegistrationIntentService`. Ta usługa również odświeża tokenu rejestracji usługi FCM.

    Dodaj następującą definicję usługi do pliku AndroidManifest.xml wewnątrz tagu `<application>`.

    ```xml
    <service
        android:name=".RegistrationIntentService"
        android:exported="false">
    </service>
    ```

2. Musisz również zdefiniować odbiornik, aby otrzymywać powiadomienia. Dodaj następującą definicję odbiornika do pliku AndroidManifest.xml wewnątrz tagu `<application>`. 

    ```xml
    <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <category android:name="<your package name>" />
        </intent-filter>
    </receiver>
    ```

    > [!IMPORTANT]
    > Zastąp `<your package NAME>` zastępczego rzeczywistą nazwą pakietu, który jest wyświetlany w górnej części pliku AndroidManifest.xml.
3. Dodaj następujące wymagane powiązane z usługą FCM poniżej uprawnienia `</application>` tagu.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="add-code"></a>Dodaj kod

1. W widoku projektu rozwiń węzeł **app** > **src** > **main** > **java**. Kliknij prawym przyciskiem myszy folder pakietu w obszarze **java**, wybierz opcję **New**, a następnie wybierz pozycję **klasy Java**. Wprowadź **NotificationSettings** dla nazwy, a następnie wybierz **OK**.

    Zaktualizuj te trzy symbole zastępcze w poniższym kodzie dla klasy `NotificationSettings`:

   * **HubListenConnectionString**: parametry połączenia **DefaultListenAccessSignature** dla centrum. Możesz skopiować te parametry połączenia, klikając **zasady dostępu** w Centrum w [Azure Portal].
   * **HubName**: Użyj nazwy Centrum jest wyświetlana na stronie Centrum w [Azure Portal].

     `NotificationSettings` — kod:

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

     > [!IMPORTANT]
     > Wprowadź **nazwa** i **DefaultListenSharedAccessSignature** Centrum przed kontynuacją. 

3. Dodaj kolejną nową klasę o nazwie `RegistrationIntentService` do projektu. Ta klasa implementuje `IntentService` interfejsu. Umożliwia on również obsługę [odświeżanie tokenu usługi FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) i [rejestrowanie w usłudze notification hubs](notification-hubs-push-notification-registration-management.md).

    Użyj następującego kodu dla tej klasy.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;
    import com.google.android.gms.tasks.OnSuccessListener;
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.google.firebase.iid.InstanceIdResult;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    import java.util.concurrent.TimeUnit;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";
        String FCM_token = null;

        private NotificationHub hub;

        public RegistrationIntentService() {
            super(TAG);
        }

        @Override
        protected void onHandleIntent(Intent intent) {

            SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
            String resultString = null;
            String regID = null;
            String storedToken = null;

            try {
                FirebaseInstanceId.getInstance().getInstanceId().addOnSuccessListener(new OnSuccessListener<InstanceIdResult>() { 
                    @Override 
                    public void onSuccess(InstanceIdResult instanceIdResult) { 
                        FCM_token = instanceIdResult.getToken(); 
                        Log.d(TAG, "FCM Registration Token: " + FCM_token); 
                    } 
                }); 
                TimeUnit.SECONDS.sleep(1);

                // Storing the registration ID that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server.
                // Otherwise, your server should have already received the token.
                if (((regID=sharedPreferences.getString("registrationID", null)) == null)){

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                // Check to see if the token has been compromised and needs refreshing.
                else if ((storedToken=sharedPreferences.getString("FCMtoken", "")) != FCM_token) {

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                else {
                    resultString = "Previously Registered Successfully - RegId : " + regID;
                }
            } catch (Exception e) {
                Log.e(TAG, resultString="Failed to complete registration", e);
                // If an exception happens while fetching the new token or updating registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

4. W `MainActivity` klasy, Dodaj następujący kod `import` instrukcji nad deklaracją klasy.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import com.microsoft.windowsazure.notifications.NotificationsManager;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    ```

5. Dodaj następujące składowe u góry klasy. Pola służą do [sprawdzania dostępności usług Google Play zgodnie z zaleceniami firmy Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private static final String TAG = "MainActivity";
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

6. W `MainActivity` klasy, dodaj następującą metodę do sprawdzania dostępności usług Google Play.

    ```java
    /**
    * Check the device to make sure it has the Google Play Services APK. If
    * it doesn't, display a dialog box that enables  users to download the APK from
    * the Google Play Store or enable it in the device's system settings.
    */

    private boolean checkPlayServices() {
        GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
        int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.SUCCESS) {
            if (apiAvailability.isUserResolvableError(resultCode)) {
                apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                        .show();
            } else {
                Log.i(TAG, "This device is not supported by Google Play Services.");
                ToastNotify("This device is not supported by Google Play Services.");
                finish();
            }
            return false;
        }
        return true;
    }
    ```

7. W `MainActivity` klasy, Dodaj następujący kod, który sprawdza dostępność usług Google Play przed wywołaniem `IntentService` do pobrania tokenu rejestracji FCM i rejestracji w Centrum:

    ```java
    public void registerWithNotificationHubs()
    {
        if (checkPlayServices()) {
            // Start IntentService to register this application with FCM.
            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    }
    ```

8. W `OnCreate` metody `MainActivity` klasy, Dodaj następujący kod, aby rozpocząć proces rejestracji, po utworzeniu działania:

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        registerWithNotificationHubs();
        MyHandler.createChannelAndHandleNotifications(getApplicationContext());
    }
    ```

9. Aby sprawdzić stan i zgłosić stan aplikacji w swojej aplikacji, Dodaj te dodatkowe metody do `MainActivity`:

    ```java
    @Override
    protected void onStart() {
        super.onStart();
        isVisible = true;
    }

    @Override
    protected void onPause() {
        super.onPause();
        isVisible = false;
    }

    @Override
    protected void onResume() {
        super.onResume();
        isVisible = true;
    }

    @Override
    protected void onStop() {
        super.onStop();
        isVisible = false;
    }

    public void ToastNotify(final String notificationMessage) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                TextView helloText = (TextView) findViewById(R.id.text_hello);
                helloText.setText(notificationMessage);
            }
        });
    }
    ```

10. Metoda `ToastNotify` używa kontrolki *"Hello World"* `TextView` do trwałego zgłaszania stanu i powiadomień w aplikacji. W układzie **res** > **layout** > **activity_main.xml** dodaj następujący identyfikator dla tej kontrolki.

    ```java
    android:id="@+id/text_hello"
    ```

11. Następnie możesz dodać podklasę dla odbiornika zdefiniowanego w pliku AndroidManifest.xml. Dodaj kolejną nową klasę o nazwie `MyHandler` do projektu.

12. Dodaj następujące instrukcje import u góry pliku `MyHandler.java`:

    ```java
    import android.app.NotificationChannel;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.media.RingtoneManager;
    import android.net.Uri;
    import android.os.Build;
    import android.os.Bundle;
    import android.support.v4.app.NotificationCompat;
    import com.microsoft.windowsazure.notifications.NotificationsHandler;    
    import com.microsoft.windowsazure.notifications.NotificationsManager;
    ```

13. Dodaj następujący kod do `MyHandler` klasy, dzięki czemu podklasą `com.microsoft.windowsazure.notifications.NotificationsHandler`.

    Ten kod zastępuje metodę `OnReceive`, dlatego program obsługi zgłasza odbierane powiadomienia. Program obsługi wysyła również powiadomienie wypychane do menedżera powiadomień systemu Android za pomocą metody `sendNotification()`. Wywołaj `sendNotification()` metody, gdy aplikacja nie jest uruchomiona i Otrzymano powiadomienie.

    ```java
    public class MyHandler extends NotificationsHandler {
        public static final String NOTIFICATION_CHANNEL_ID = "nh-demo-channel-id";
        public static final String NOTIFICATION_CHANNEL_NAME = "Notification Hubs Demo Channel";
        public static final String NOTIFICATION_CHANNEL_DESCRIPTION = "Notification Hubs Demo Channel";
    
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;
    
        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("message");
            sendNotification(nhMessage);
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
        }
    
        private void sendNotification(String msg) {
    
            Intent intent = new Intent(ctx, MainActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    
            mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);
    
            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                    intent, PendingIntent.FLAG_ONE_SHOT);
    
            Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
            NotificationCompat.Builder notificationBuilder = new NotificationCompat.Builder(
                    ctx,
                    NOTIFICATION_CHANNEL_ID)
                    .setContentText(msg)
                    .setPriority(NotificationCompat.PRIORITY_HIGH)
                    .setSmallIcon(android.R.drawable.ic_popup_reminder)
                    .setBadgeIconType(NotificationCompat.BADGE_ICON_SMALL);
    
            notificationBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
        }
    
        public static void createChannelAndHandleNotifications(Context context) {
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                NotificationChannel channel = new NotificationChannel(
                        NOTIFICATION_CHANNEL_ID,
                        NOTIFICATION_CHANNEL_NAME,
                        NotificationManager.IMPORTANCE_HIGH);
                channel.setDescription(NOTIFICATION_CHANNEL_DESCRIPTION);
                channel.setShowBadge(true);
    
                NotificationManager notificationManager = context.getSystemService(NotificationManager.class);
                notificationManager.createNotificationChannel(channel);
                NotificationsManager.handleNotifications(context, "", MyHandler.class);
            }
        }
    }
    ```

14. W programie Android Studio, na pasku menu wybierz **kompilacji** > **ponownie skompilować projekt** aby upewnić się, że nie ma żadnych błędów w kodzie. Jeśli otrzymasz komunikat o błędzie o `ic_launcher` ikonę Usuń następującą instrukcję w pliku AndroidManifest.xml: 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
15. Uruchom aplikację na twoim urządzeniu i sprawdź, czy pomyślnie rejestruje się ona koncentratora.

    > [!NOTE]
    > Rejestracja może zakończyć się niepowodzeniem podczas początkowego uruchamiania do momentu `onTokenRefresh()` zostanie wywołana metoda usługi Identyfikatora wystąpienia. Odświeżenie powinno zainicjować pomyślną rejestrację w centrum powiadomień.

    ![Rejestrowanie urządzenia powiodło się.](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>Wysyłanie testowego powiadomienia z centrum powiadomień

Można wysyłać powiadomienia wypychane z [Azure Portal] , wykonując następujące czynności:

1. W witrynie Azure portal na stronie Centrum powiadomień dla Twojego Centrum, wybierz **wysyłanie testowe** w **Rozwiązywanie problemów** sekcji.
3. W pozycji **Platformy** wybierz opcję **Android**.
4. Wybierz pozycję **Wyślij**.  Nie zobaczysz powiadomienie na urządzenie z systemem Android jeszcze ponieważ aplikacji mobilnej nie zostały uruchomione na nim. Po uruchomieniu aplikacji mobilnej, wybierz **wysyłania** przycisk ponownie, aby zobaczyć komunikat powiadomienia.
5. Zobacz wyniki operacji na liście u dołu.

    ![Azure Notification Hubs — Wysyłanie testowe](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. Zobaczysz powiadomienie na swoim urządzeniu. 

    ![Komunikat z powiadomieniem na urządzeniu](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>Uruchamianie aplikacji mobilnej na emulatorze
Przed testowaniem powiadomienia wypychane w emulatorze, upewnij się, że obraz emulatora obsługuje poziom interfejsu API Google, który został wybrany dla aplikacji. Jeśli obraz nie obsługuje natywnych interfejsów API Google, możesz otrzymać **usługi\_nie\_dostępne** wyjątku.

Ponadto upewnij się, że dodano konto Google do uruchomionego emulatora w obszarze **ustawienia** > **kont**. W przeciwnym razie próby rejestracji z usługą FCM może spowodować **uwierzytelniania\_niepowodzenie** wyjątku.

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku użyto usługi Firebase Cloud Messaging wysyłać powiadomienia do wszystkich urządzeń z systemem Android, które zostały zarejestrowane w usłudze. Aby dowiedzieć się, jak wysyłać powiadomienia push do konkretnych urządzeń, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Samouczek: Powiadomienia wypychane do określonych urządzeń z systemem Android](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Azure Portal]: https://portal.azure.com
