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
ms.date: 02/05/2019
ms.author: jowargo
ms.openlocfilehash: 2fe448f3ed91f2c6dd242c24aa378c3541eceecc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60876801"
---
# <a name="tutorial-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-firebase-cloud-messaging"></a>Samouczek: wysyłanie powiadomień push do urządzeń z systemem Android przy użyciu usług Azure Notification Hubs i Google Firebase Cloud Messaging

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia push do aplikacji dla systemu Android przy użyciu usług Azure Notification Hubs i Firebase Cloud Messaging (FCM). W tym samouczku tworzysz pustą aplikację dla systemu Android służącą do odbierania powiadomień push przy użyciu usługi Firebase Cloud Messaging (FCM).

Kompletny kod dla tego samouczka można pobrać z witryny GitHub [tutaj](https://github.com/Azure/azure-notificationhubs-android/tree/master/samples/FCMTutorialApp).

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Tworzenie projektu programu Android Studio.
> * Tworzenie projektu Firebase obsługującego usługę Firebase Cloud Messaging.
> * Tworzenie centrum powiadomień.
> * Łączenie aplikacji z centrum powiadomień.
> * Testowanie aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka potrzebne jest aktywne konto platformy Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/free/).

* Oprócz aktywnego konta platformy Azure wspomnianego powyżej w tym samouczku wymagana jest najnowsza wersja programu [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797).
* System Android 2.3 lub nowszy dla usługi Firebase Cloud Messaging.
* Usługa Google Repository w wersji 27 lub nowszej jest wymagana dla usługi Firebase Cloud Messaging.
* Usługi Google Play Services 9.0.2 lub nowsze dla usługi Firebase Cloud Messaging.
* Wykonanie czynności opisanych w tym samouczku jest wymaganiem wstępnym dla wszystkich innych samouczków usługi Notification Hubs dotyczących aplikacji dla systemu Android.

## <a name="create-an-android-studio-project"></a>Tworzenie projektu programu Android Studio

1. Uruchom program Android Studio.
2. Wybierz z menu pozycję **File** (Plik), wskaż pozycję **New** (Nowy) i wybierz pozycję **New Project** (Nowy projekt). 
2. Na stronie **Choose your project** (Wybieranie projektu) wybierz pozycję **Empty Activity** (Puste działanie) i wybierz pozycję **Next** (Dalej). 
3. Na stronie **Configure your project** (Konfigurowanie projektu) wykonaj następujące kroki: 
    1. Wprowadź **nazwę** aplikacji.
    2. Określ lokalizację, w której mają być zapisywane pliki projektu. 
    3. Wybierz pozycję **Finish** (Zakończ). 

        ![Konfigurowanie projektu](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>Tworzenie projektu Firebase obsługującego usługę FCM

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Konfigurowanie centrum powiadomień

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Konfigurowanie ustawień usługi Firebase Cloud Messaging na potrzeby centrum

1. Wybierz pozycję **Google (GCM/FCM)** w obszarze **Settings** (Ustawienia) w menu po lewej. 
2. Wklej **klucz serwera** dla projektu usługi FCM, który został zapisany wcześniej. 
3. Wybierz pozycję **Zapisz** na pasku narzędzi. 

    ![Azure Notification Hubs — Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Wśród alertów pojawi się komunikat o pomyślnym zaktualizowaniu centrum powiadomień. Przycisk **Save** (Zapisz) będzie wyłączony. 

Twoje centrum powiadomień jest teraz skonfigurowane do pracy z usługą Firebase Cloud Messaging i uzyskano parametry połączenia do rejestrowania aplikacji w celu odbierania i wysyłania powiadomień push.

## <a id="connecting-app"></a>Łączenie aplikacji z centrum powiadomień

### <a name="add-google-play-services-to-the-project"></a>Dodawanie usług Google Play do projektu

[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="adding-azure-notification-hubs-libraries"></a>Dodawanie bibliotek usługi Azure Notification Hubs

1. W pliku `Build.Gradle` dla **aplikacji** dodaj następujące wiersze w sekcji **dependencies**.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

2. Dodaj następujące repozytorium po sekcji **dependencies**.

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="add-google-firebase-support"></a>Dodawanie obsługi usługi Google Firebase

1. W pliku `Build.Gradle` dla **aplikacji** dodaj następujące wiersze w sekcji **dependencies**, jeśli jeszcze nie istnieją. 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.7'
    ```

2. Na końcu pliku dodaj następującą wtyczkę, jeśli jeszcze nie istnieje. 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```

### <a name="updating-the-androidmanifestxml"></a>Aktualizowanie pliku AndroidManifest.xml

1. W celu zapewnienia obsługi usługi FCM musisz zaimplementować w naszym kodzie wystąpienie usługi odbiornika interfejsu Instance ID, który służy do [uzyskiwania tokenów rejestracji](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register) przy użyciu [interfejsu API FirebaseInstanceId firmy Google](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId). W tym samouczku nazwa klasy to `MyInstanceIDService`.

    Dodaj następującą definicję usługi do pliku AndroidManifest.xml wewnątrz tagu `<application>`.

    ```xml
    <service android:name=".MyInstanceIDService">
        <intent-filter>
            <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
        </intent-filter>
    </service>
    ```

2. Po uzyskaniu tokenu rejestracji usługi FCM z interfejsu API FirebaseInstanceId używa się go do [rejestracji w usłudze Azure Notification Hubs](notification-hubs-push-notification-registration-management.md). Zapewniasz obsługę rejestracji w tle przy użyciu klasy `IntentService` o nazwie `RegistrationIntentService`. Ta usługa odpowiada również za odświeżanie Twojego tokenu rejestracji usługi FCM.

    Dodaj następującą definicję usługi do pliku AndroidManifest.xml wewnątrz tagu `<application>`.

    ```xml
    <service
        android:name=".RegistrationIntentService"
        android:exported="false">
    </service>
    ```

3. Aby otrzymywać powiadomienia, musisz również zdefiniować odbiornik. Dodaj następującą definicję odbiornika do pliku AndroidManifest.xml wewnątrz tagu `<application>`. 

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
    > Zastąp symbol zastępczy `<your package NAME>` rzeczywistą nazwą pakietu wyświetlaną u góry pliku `AndroidManifest.xml`.
4. Wybierz pozycję **Sync Now** (Synchronizuj teraz) na pasku narzędzi.
5. Dodaj następujące wymagane uprawnienia powiązane z usługą FCM **poniżej** tagu `</application>`.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="adding-code"></a>Dodawanie kodu

1. W widoku projektu rozwiń węzeł **app** > **src** > **main** > **java**. Kliknij prawym przyciskiem folder pakietu w węźle **java**, kliknij pozycję **New** (Nowy), a następnie kliknij pozycję **Java Class** (Klasa Java). Wprowadź parametr `NotificationSettings` dla nazwy, a następnie wybierz pozycję **OK**.

    Zaktualizuj te trzy symbole zastępcze w poniższym kodzie dla klasy `NotificationSettings`:

   * **HubListenConnectionString**: parametry połączenia **DefaultListenAccessSignature** dla centrum. Możesz skopiować te parametry połączenia, klikając pozycję **Zasady dostępu** w centrum w witrynie [Azure Portal].
   * **HubName**: użyj nazwy centrum powiadomień wyświetlanej na stronie centrum w witrynie [Azure Portal].

     `NotificationSettings` — kod:

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

     > [!IMPORTANT]
     > Przed przejściem dalej wprowadź parametry **name** i **DefaultListenSharedAccessSignature** dla centrum powiadomień. 
2. Dodaj kolejną klasę o nazwie `MyInstanceIDService`. Ta klasa to Twoja implementacja usługi odbiornika interfejsu Instance ID.

    Kod dla tej klasy będzie wywoływać klasę `IntentService` w celu [odświeżenia tokenu usługi FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) w tle.

    ```java
    import android.content.Intent;
    import android.util.Log;
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class MyInstanceIDService extends FirebaseInstanceIdService {

        private static final String TAG = "MyInstanceIDService";

        @Override
        public void onTokenRefresh() {

            Log.d(TAG, "Refreshing FCM Registration Token");

            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    };
    ```

3. Dodaj kolejną nową klasę o nazwie `RegistrationIntentService` do projektu. Ta klasa implementuje interfejs `IntentService` oraz obsługuje [odświeżanie tokenu usługi FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) i [rejestrowanie w centrum powiadomień](notification-hubs-push-notification-registration-management.md).

    Użyj następującego kodu dla tej klasy.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.microsoft.windowsazure.messaging.NotificationHub;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";

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
                String FCM_token = FirebaseInstanceId.getInstance().getToken();
                Log.d(TAG, "FCM Registration Token: " + FCM_token);

                // Storing the registration ID that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server,
                // otherwise your server should have already received the token.
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

                // Check if the token may have been compromised and needs refreshing.
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
                // If an exception happens while fetching the new token or updating our registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

4. W klasie `MainActivity` dodaj następujące instrukcje `import` nad deklaracją klasy.

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

6. W klasie `MainActivity` dodaj następującą metodę do sprawdzania dostępności usług Google Play.

    ```java
    /**
    * Check the device to make sure it has the Google Play Services APK. If
    * it doesn't, display a dialog that allows users to download the APK from
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

7. W klasie `MainActivity` dodaj następujący kod, który sprawdza dostępność usług Google Play przed wywołaniem klasy `IntentService` w celu pobrania tokenu rejestracji FCM i rejestracji w centrum powiadomień.

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

8. W metodzie `OnCreate` klasy `MainActivity` dodaj następujący kod w celu uruchomienia procesu rejestracji po utworzeniu działania.

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

9. Aby zweryfikować stan aplikacji i zgłosić stan w aplikacji, dodaj te dodatkowe metody do klasy `MainActivity`.

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

10. Metoda `ToastNotify` używa kontrolki *"Hello World"* `TextView` do trwałego zgłaszania stanu i powiadomień w aplikacji. W układzie **res** -> **layout** -> **activity_main.xml** dodaj następujący identyfikator dla tej kontrolki.

    ```java
    android:id="@+id/text_hello"
    ```

11. Następnie dodaj podklasę dla odbiornika zdefiniowanego w pliku AndroidManifest.xml. Dodaj kolejną nową klasę o nazwie `MyHandler` do projektu.

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

13. Dodaj następujący kod do klasy `MyHandler`, aby stała się podklasą klasy `com.microsoft.windowsazure.notifications.NotificationsHandler`.

    Ten kod zastępuje metodę `OnReceive`, dlatego program obsługi zgłasza odbierane powiadomienia. Program obsługi wysyła również powiadomienie wypychane do menedżera powiadomień systemu Android za pomocą metody `sendNotification()`. Metoda `sendNotification()` powinna być wykonywana, gdy aplikacja nie jest uruchomiona i otrzymano powiadomienie.

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

14. W programie Android Studio na pasku menu kliknij pozycję **Build** > **Rebuild Project** (Kompiluj — Kompiluj ponownie projekt), aby upewnić się, że w kodzie nie występują żadne błędy. Jeśli zobaczysz błąd dotyczący ikony `ic_launcher`, usuń następującą instrukcję z pliku AndroidManifest.xml. 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
15. Uruchom aplikację na urządzeniu i sprawdź, czy pomyślnie rejestruje się ona w centrum powiadomień.

    > [!NOTE]
    > Rejestracja podczas początkowego uruchamiania może kończyć się niepowodzeniem, dopóki nie zostanie wywołana metoda `onTokenRefresh()` usługi identyfikatora wystąpienia. Odświeżenie powinno zainicjować pomyślną rejestrację w centrum powiadomień.

    ![Rejestrowanie urządzenia powiodło się.](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>Wysyłanie testowego powiadomienia z centrum powiadomień

Powiadomienia push możesz wysłać z witryny [Azure Portal], wykonując następujące czynności:

1. W witrynie Azure Portal na stronie **Centrum powiadomień** Twojego centrum powiadomień wybierz pozycję **Wysyłanie testowe** w sekcji **Rozwiązywanie problemów**.
3. W pozycji **Platformy** wybierz opcję **Android**.
4. Wybierz pozycję **Wyślij**.  Nie zobaczysz powiadomienia na urządzeniu z systemem Android, ponieważ jeszcze nie uruchomiono na nim aplikacji mobilnej. Po uruchomieniu aplikacji mobilnej wybierz przycisk **Wyślij** ponownie, aby zobaczyć komunikat powiadomienia.
5. Zobacz **wynik** operacji na liście u dołu.

    ![Azure Notification Hubs — Wysyłanie testowe](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. Zobaczysz powiadomienie na swoim urządzeniu. 

    ![Komunikat z powiadomieniem na urządzeniu](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>Uruchamianie aplikacji mobilnej na emulatorze
Aby przetestować powiadomienia wypychane w emulatorze, upewnij się, że obraz emulatora obsługuje poziom interfejsu API Google wybrany dla aplikacji. Jeśli obraz nie obsługuje natywnych interfejsów API Google, może wystąpić wyjątek **SERVICE\_NOT\_AVAILABLE** (usługa niedostępna).

Oprócz tego upewnij się, że dodano konto Google do uruchomionego emulatora w obszarze **Settings**  >  **Accounts** (Ustawienia > Konta). W przeciwnym razie próby rejestracji w usłudze GCM mogą spowodować wystąpienie wyjątku **AUTHENTICATION\_FAILED** (uwierzytelnianie nie powiodło się).

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku za pomocą usługi Firebase Cloud Messaging wysłano powiadomienia push do urządzeń z systemem Android. Aby dowiedzieć się, jak wypychać powiadomienia przy użyciu usługi Google Cloud Messaging, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Wysyłanie powiadomień push do urządzeń z systemem Android przy użyciu usługi Google Cloud Messaging](notification-hubs-android-push-notification-google-gcm-get-started.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Azure Portal]: https://portal.azure.com
