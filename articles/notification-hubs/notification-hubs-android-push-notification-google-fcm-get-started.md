---
title: Wysyłanie powiadomień wypychanych do systemu Android przy użyciu usługi Azure Notification Hubs i Firebase | Dokumenty firmy Microsoft
description: Korzystając z tego samouczka, dowiesz się, jak za pomocą usług Azure Notification Hubs i Google Firebase Cloud Messaging wysyłać powiadomienia push do urządzeń z systemem Android.
services: notification-hubs
documentationcenter: android
keywords: powiadomienia wypychane, powiadomienie wypychane, powiadomienia wypychane w systemie android, firebase cloud messaging
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 02298560-da61-4bbb-b07c-e79bd520e420
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/11/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 09/11/2019
ms.openlocfilehash: e0682a33540cafd8ebab1bb248d462b30ee4dc2a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80127534"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-firebase"></a>Samouczek: Wysyłanie powiadomień push do urządzeń z Androidem za pomocą Firebase

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia push do aplikacji dla systemu Android przy użyciu usług Azure Notification Hubs i Firebase Cloud Messaging (FCM). W tym samouczku tworzysz pustą aplikację dla systemu Android służącą do odbierania powiadomień push przy użyciu usługi Firebase Cloud Messaging (FCM).

Wypełniony kod dla tego samouczka można pobrać [z GitHub](https://github.com/Azure/azure-notificationhubs-android/tree/master/samples/FCMTutorialApp).

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Tworzenie projektu programu Android Studio.
> * Tworzenie projektu Firebase obsługującego usługę Firebase Cloud Messaging.
> * Utwórz koncentrator.
> * Połącz aplikację z centrum.
> * Testowanie aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka potrzebne jest aktywne konto platformy Azure. Jeśli nie masz konta, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/free/). 

Potrzebne są również następujące elementy: 

* Najnowsza wersja [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797)
* Android 2.3 lub wyższy dla Firebase Cloud Messaging
* Google Repozytorium wersja 27 lub nowsza dla Firebase Cloud Messaging
* Usługi Google Play 9.0.2 lub nowsze dla firebase cloud messaging

Ukończenie tego samouczka jest warunkiem wstępnym do wykonywania wszystkich innych samouczków Centrum powiadomień dla aplikacji na Androida.

## <a name="create-an-android-studio-project"></a>Tworzenie projektu programu Android Studio

1. Uruchom program Android Studio.
2. Wybierz **pozycję Plik**, wskaż pozycję **Nowy**, a następnie wybierz pozycję Nowy **projekt**. 
2. Na stronie **Wybierz projekt** wybierz pozycję **Puste działanie**, a następnie wybierz pozycję **Dalej**. 
3. Na stronie **Konfigurowanie projektu** należy wykonać następujące czynności: 
    1. Wprowadź nazwę aplikacji.
    2. Określ lokalizację, w której mają być zapisywane pliki projektu. 
    3. Wybierz **pozycję Zakończ**. 

        ![Konfigurowanie projektu](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>Tworzenie projektu Firebase obsługującego usługę FCM

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-hub"></a>Konfigurowanie koncentratora

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Konfigurowanie ustawień usługi Firebase Cloud Messaging na potrzeby centrum

1. W lewym okienku w obszarze **Ustawienia** wybierz pozycję **Google (GCM/FCM).** 
2. Wprowadź **klucz serwera** dla projektu FCM, który został zapisany wcześniej. 
3. Na pasku narzędzi wybierz pozycję **Zapisz**. 

    ![Usługa Azure Notification Hub — Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Portal Platformy Azure wyświetla komunikat w alertach, że centrum zostało pomyślnie zaktualizowane. Przycisk **Save** (Zapisz) będzie wyłączony. 

Twoje centrum jest teraz skonfigurowane do pracy z firebase cloud messaging. Masz również parametry połączenia, które są niezbędne do wysyłania powiadomień do urządzenia i zarejestrowania aplikacji do odbierania powiadomień.

## <a name="connect-your-app-to-the-notification-hub"></a><a id="connecting-app"></a>Łączenie aplikacji z centrum powiadomień

### <a name="add-google-play-services-to-the-project"></a>Dodawanie usług Google Play do projektu

1. W programie Android Studio wybierz pozycję **Narzędzia** w menu, a następnie wybierz pozycję **Menedżer zestawu SDK**. 
2. Wybierz docelową wersję sdk systemu Android, który jest używany w projekcie. Następnie wybierz pozycję **Pokaż szczegóły pakietu**. 

    ![Menedżer zestawów Android SDK — wybieranie wersji docelowej](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Wybierz **interfejsy API Google**, jeśli nie są jeszcze zainstalowane.

    ![Menedżer zestawów Android SDK — wybrane interfejsy API firmy Google](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Przełącz się na kartę **Narzędzia zestawu SDK.** Jeśli usługi Google Play nie zostały jeszcze zainstalowane, wybierz pozycję **Usługi Google Play,** jak pokazano na poniższej ilustracji. Następnie wybierz pozycję **Zastosuj** do zainstalowania. Zanotuj ścieżkę zestawu SDK, która będzie potrzebna w kolejnym kroku.

    ![Menedżer zestawów Android SDK — wybrane usługi Google Play](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Jeśli zobaczysz okno dialogowe **Potwierdzanie zmiany**, wybierz przycisk **OK**. Instalator składników zainstaluje żądane składniki. Wybierz przycisk **Finish** (Zakończ) po zainstalowaniu składników.
4. Wybierz przycisk **OK**, aby zamknąć okno dialogowe **Settings for New Projects** (Ustawienia nowego projektu).  
1. Otwórz plik AndroidManifest.xml, a następnie dodaj następujący tag do tagu *aplikacji.*

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```


### <a name="add-azure-notification-hubs-libraries"></a>Dodawanie bibliotek centrów powiadomień platformy Azure

1. W pliku Build.Gradle dla aplikacji dodaj następujące wiersze w sekcji zależności.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
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

1. W pliku Build.Gradle dla aplikacji dodaj następujące wiersze w sekcji **zależności,** jeśli jeszcze nie istnieją. 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:17.3.4'
    ```

2. Dodaj następującą wtyczkę na końcu pliku, jeśli jeszcze go tam nie ma. 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```
3. Wybierz pozycję **Sync Now** (Synchronizuj teraz) na pasku narzędzi.

### <a name="update-the-androidmanifestxml-file"></a>Aktualizowanie pliku AndroidManifest.xml

1. Po otrzymaniu tokenu rejestracji FCM można go używać do [rejestrowania się w usłudze Azure Notification Hubs.](notification-hubs-push-notification-registration-management.md) Ta rejestracja jest obsługiwana `IntentService` w `RegistrationIntentService`tle przy użyciu nazwanego pliku . Ta usługa odświeża również token rejestracji FCM. Należy również utworzyć `FirebaseService` klasę o nazwie `FirebaseMessagingService` jako podklasę i zastąpić `onMessageReceived` metodę odbierania i obsługi powiadomień. 

    Dodaj następującą definicję usługi do pliku AndroidManifest.xml wewnątrz tagu `<application>`.

    ```xml
    <service
        android:name=".RegistrationIntentService"
        android:exported="false">
    </service>
    <service
        android:name=".FirebaseService"
        android:exported="false">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT" />
        </intent-filter>
    </service>
    ```
3. Dodaj następujące niezbędne uprawnienia związane z `</application>` FCM pod tagiem.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="add-code"></a>Dodaj kod

1. W widoku projektu rozwiń **aplikację** > **src** > **main** > **java**. Kliknij prawym przyciskiem myszy folder pakietu w obszarze **java**, wybierz pozycję **Nowy**, a następnie wybierz pozycję **Klasa Java**. Wprowadź **ustawienia powiadomień** dla nazwy, a następnie wybierz przycisk **OK**.

    Zaktualizuj te trzy symbole zastępcze w poniższym kodzie dla klasy `NotificationSettings`:

   * **HubListenConnectionString**: domyślne parametry połączenia **DefaultListenAccessSignature** dla centrum. Ciąg połączenia można skopiować, klikając pozycję **Zasady dostępu** w centrum w [witrynie Azure Portal].
   * **Nazwa centrum:** Użyj nazwy centrum, która pojawia się na stronie centrum w [witrynie Azure portal].

     `NotificationSettings` — kod:

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

     > [!IMPORTANT]
     > Wprowadź **nazwę** i **defaultlistenSharedAccessSignature** centrum przed kontynuowaniem. 

2. Dodaj kolejną nową klasę o nazwie `RegistrationIntentService` do projektu. Ta klasa implementuje `IntentService` interfejs. Obsługuje również [odświeżanie tokenu FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) i [rejestrowanie się w centrum powiadomień.](notification-hubs-push-notification-registration-management.md)

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

3. W `MainActivity` klasie dodaj następujące `import` instrukcje powyżej deklaracji klasy.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    ```

4. Dodaj następujące składowe u góry klasy. Pola służą do [sprawdzania dostępności usług Google Play zgodnie z zaleceniami firmy Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private static final String TAG = "MainActivity";
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

5. W `MainActivity` klasie dodaj następującą metodę, aby sprawdzić dostępność usług Google Play.

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

6. W `MainActivity` klasie dodaj następujący kod, który sprawdza usługi `IntentService` Google Play przed wywołaniem tokenu rejestracji FCM i zarejestruj się w centrum:

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

7. W `OnCreate` metodzie `MainActivity` klasy dodaj następujący kod, aby rozpocząć proces rejestracji podczas tworzenia działania:

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        registerWithNotificationHubs();
        FirebaseService.createChannelAndHandleNotifications(getApplicationContext());
    }
    ```

8. Aby zweryfikować stan aplikacji i stan raportu w `MainActivity`aplikacji, dodaj te dodatkowe metody do:

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

9. Metoda `ToastNotify` używa kontrolki *"Hello World"* `TextView` do trwałego zgłaszania stanu i powiadomień w aplikacji. W**układzie** >  **res** > **activity_main.xml** układu dodaj następujący identyfikator dla tego formantu.

    ```java
    android:id="@+id/text_hello"
    ```

    ![Azure Notification Hubs — Wysyłanie testowe](./media/notification-hubs-android-push-notification-google-fcm-get-started/activity-main-xml.png)

10. Następnie należy dodać podklasę dla odbiornika zdefiniowanego w systemie AndroidManifest.xml. Dodaj kolejną nową klasę o nazwie `FirebaseService` do projektu.

11. Dodaj następujące instrukcje import u góry pliku `FirebaseService.java`:

    ```java
    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;
    import android.util.Log;
    import android.app.NotificationChannel;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.media.RingtoneManager;
    import android.net.Uri;
    import android.os.Build;
    import android.os.Bundle;
    import androidx.core.app.NotificationCompat;
    ```

12. Dodaj następujący kod `FirebaseService` dla klasy, czyniąc ją `FirebaseMessagingService`podklasą .

    Ten kod zastępuje `onMessageReceived` metodę i raportuje powiadomienia, które są odbierane. wysyła również powiadomienie wypychane do menedżera `sendNotification()` powiadomień systemu Android przy użyciu metody. Wywołanie `sendNotification()` metody, gdy aplikacja nie jest uruchomiona i zostanie odebrane powiadomienie.

    ```java
    public class FirebaseService extends FirebaseMessagingService
    {
        private String TAG = "FirebaseService";
    
        public static final String NOTIFICATION_CHANNEL_ID = "nh-demo-channel-id";
        public static final String NOTIFICATION_CHANNEL_NAME = "Notification Hubs Demo Channel";
        public static final String NOTIFICATION_CHANNEL_DESCRIPTION = "Notification Hubs Demo Channel";
    
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        static Context ctx;
    
        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            // ...
    
            // TODO(developer): Handle FCM messages here.
            // Not getting messages here? See why this may be: https://goo.gl/39bRNJ
            Log.d(TAG, "From: " + remoteMessage.getFrom());
    
            String nhMessage;
            // Check if message contains a notification payload.
            if (remoteMessage.getNotification() != null) {
                Log.d(TAG, "Message Notification Body: " + remoteMessage.getNotification().getBody());
    
                nhMessage = remoteMessage.getNotification().getBody();
            }
            else {
                nhMessage = remoteMessage.getData().values().iterator().next();
            }
    
            // Also if you intend on generating your own notifications as a result of a received FCM
            // message, here is where that should be initiated. See sendNotification method below.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
            sendNotification(nhMessage);
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
            ctx = context;
    
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                NotificationChannel channel = new NotificationChannel(
                        NOTIFICATION_CHANNEL_ID,
                        NOTIFICATION_CHANNEL_NAME,
                        NotificationManager.IMPORTANCE_HIGH);
                channel.setDescription(NOTIFICATION_CHANNEL_DESCRIPTION);
                channel.setShowBadge(true);
    
                NotificationManager notificationManager = context.getSystemService(NotificationManager.class);
                notificationManager.createNotificationChannel(channel);
             }
        }
    }
    ```

13. W programie Android Studio na pasku menu wybierz pozycję >  **Buduj****projekt przebudowy,** aby upewnić się, że w kodzie nie ma żadnych błędów. Jeśli zostanie wyświetlony błąd `ic_launcher` dotyczący ikony, usuń następującą instrukcję z pliku AndroidManifest.xml: 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
14. Upewnij się, że masz urządzenie wirtualne do uruchamiania aplikacji. Jeśli go nie masz, dodaj go w następujący sposób:
    1. ![Otwórz menedżera urządzeń](./media/notification-hubs-android-push-notification-google-fcm-get-started/open-device-manager.png)
    2. ![Tworzenie urządzenia wirtualnego](./media/notification-hubs-android-push-notification-google-fcm-get-started/your-virtual-devices.PNG)

15. Uruchom aplikację na wybranym urządzeniu i sprawdź, czy rejestruje się pomyślnie w centrum.

    > [!NOTE]
    > Rejestracja może zakończyć się `onTokenRefresh()` niepowodzeniem podczas początkowego uruchamiania, dopóki nie zostanie wywołana metoda usługi identyfikatora wystąpienia. Odświeżenie powinno zainicjować pomyślną rejestrację w centrum powiadomień.

    ![Rejestrowanie urządzenia powiodło się.](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>Wysyłanie testowego powiadomienia z centrum powiadomień

Powiadomienia wypychane można wysyłać z [witryny Azure Portal,] wykonując następujące kroki:

1. W witrynie Azure portal na stronie Centrum powiadomień dla twojego centrum wybierz pozycję **Testuj wysyłanie** w sekcji **Rozwiązywanie problemów.**
3. W pozycji **Platformy** wybierz opcję **Android**.
4. Wybierz pozycję **Wyślij**.  Nie zobaczysz jeszcze powiadomienia na urządzeniu z Androidem, ponieważ nie uruchomisz na nim aplikacji mobilnej. Po uruchomieniu aplikacji mobilnej ponownie wybierz przycisk **Wyślij,** aby wyświetlić komunikat z powiadomieniem.
5. Zobacz wynik operacji na liście u dołu.

    ![Azure Notification Hubs — Wysyłanie testowe](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. Zostanie wyświetlony komunikat z powiadomieniem na urządzeniu. 

    ![Komunikat z powiadomieniem na urządzeniu](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>Uruchamianie aplikacji mobilnej na emulatorze
Przed przetestowaniem powiadomień wypychanych wewnątrz emulatora upewnij się, że obraz emulatora obsługuje poziom interfejsu API Google wybrany dla aplikacji. Jeśli obraz nie obsługuje natywnych interfejsów API Google, możesz uzyskać wyjątek **USŁUGA\_NIEDOSTĘPNA.\_**

Upewnij się również, że konto Google zostało dodane do emulatora działa w obszarze**Konta** **ustawień** > . W przeciwnym razie próby zarejestrowania się w FCM może spowodować **uwierzytelnianie\_nie powiodło się** wyjątek.

## <a name="next-steps"></a>Następne kroki
W tym samouczku użyto Firebase Cloud Messaging do emisji powiadomień do wszystkich urządzeń z systemem Android, które zostały zarejestrowane w usłudze. Aby dowiedzieć się, jak wysyłać powiadomienia push do konkretnych urządzeń, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Samouczek: Wypychanie powiadomień do określonych urządzeń z Androidem](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Azure Portal]: https://portal.azure.com
