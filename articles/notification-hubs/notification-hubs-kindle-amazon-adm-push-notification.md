---
title: Wysyłanie zlokalizowanych powiadomień do aplikacji dla urządzeń Kindle przy użyciu usługi Azure Notification Hubs | Microsoft Docs
description: Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia wypychane do aplikacji dla urządzeń Kindle przy użyciu usługi Azure Notification Hubs.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 346fc8e5-294b-4e4f-9f27-7a82d9626e93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-kindle
ms.devlang: Java
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 7dc969fdcf8b819f5223bdbff3cc1b9d4439c370
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60873593"
---
# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>Rozpoczynanie pracy z usługą Notification Hubs dla aplikacji dla urządzeń Kindle

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia wypychane do aplikacji dla urządzeń Kindle przy użyciu usługi Azure Notification Hubs. Utworzysz pustą aplikację dla urządzeń Kindle służącą do odbierania powiadomień push przy użyciu usługi Amazon Device Messaging (ADM).

W tym samouczku utworzysz/zaktualizujesz kod, aby wykonać następujące zadania:

> [!div class="checklist"]
> * Dodawanie nowej aplikacji do portalu dla deweloperów
> * Tworzenie klucza interfejsu API
> * Dodawanie poświadczeń do centrum
> * Konfigurowanie aplikacji
> * Tworzenie programu obsługi usługi ADM
> * Dodawanie klucza interfejsu API do aplikacji
> * Uruchamianie aplikacji
> * Wysyłanie powiadomienia testowego

## <a name="prerequisites"></a>Wymagania wstępne

* Pobierz zestaw Android SDK (założono, że używane jest środowisko Eclipse) z [portalu deweloperów systemu Android](https://go.microsoft.com/fwlink/?LinkId=389797).
* Postępuj zgodnie z instrukcjami w temacie [Setting Up Your Development Environment](https://developer.amazon.com/docs/fire-tablets/ft-set-up-your-development-environment.html) (Konfigurowanie środowiska projektowego), aby skonfigurować środowisko projektowe dla urządzeń Kindle.

## <a name="add-a-new-app-to-the-developer-portal"></a>Dodawanie nowej aplikacji do portalu dla deweloperów

1. Najpierw utwórz aplikację w [portalu dla deweloperów firmy Amazon].

    ![][0]

2. Skopiuj wartość **Application Key** (Klucz aplikacji).

    ![][1]

3. W portalu kliknij nazwę aplikacji, a następnie kliknij kartę **Device Messaging**.

    ![][2]

4. Kliknij pozycję **Create a New Security Profile** (Utwórz nowy profil zabezpieczeń), a następnie utwórz nowy profil zabezpieczeń (na przykład **profil zabezpieczeń TestAdm**). Następnie kliknij przycisk **Save** (Zapisz).

    ![][3]

5. Kliknij pozycję **Security Profiles** (Profile zabezpieczeń), aby wyświetlić utworzony profil zabezpieczeń. Skopiuj wartości **Client ID** (Identyfikator klienta) i **Client Secret** (Klucz tajny klienta) do późniejszego użycia.

    ![][4]

## <a name="create-an-api-key"></a>Tworzenie klucza interfejsu API

1. Otwórz wiersz polecenia z uprawnieniami administratora.
2. Przejdź do folderu zestawu SDK systemu Android.
3. Wprowadź następujące polecenie:

    ```shell
    keytool -list -v -alias androiddebugkey -keystore ./debug.keystore
    ```

    ![][5]

4. Jako hasło **keystore** wpisz wartość **android**.
5. Skopiuj odcisk palca **MD5**.
6. W portalu dla deweloperów na karcie **Messaging** (Komunikaty) kliknij pozycję **Android/Kindle** i wpisz nazwę pakietu aplikacji (na przykład **com.sample.notificationhubtest**) oraz wartość **MD5**, a następnie kliknij pozycję **Generate API Key** (Generuj klucz API).

## <a name="add-credentials-to-the-hub"></a>Dodawanie poświadczeń do centrum

W portalu dodaj klucz tajny klienta i identyfikator klienta na karcie **Konfiguracja** w centrum powiadomień.

## <a name="set-up-your-application"></a>Konfigurowanie aplikacji

> [!NOTE]
> Podczas tworzenia aplikacji użyj co najmniej poziomu 17 interfejsu API.

Dodaj biblioteki usługi ADM do projektu Eclipse:

1. Aby uzyskać bibliotekę usługi ADM [pobierz zestaw SDK]. Wyodrębnij plik zip zestawu SDK.
2. W programie Eclipse kliknij prawym przyciskiem myszy projekt, a następnie kliknij pozycję **Properties** (Właściwości). Wybierz pozycję **Java Build Path** (Ścieżka kompilacji języka Java) po lewej stronie, a następnie wybierz kartę **Libraries** (Biblioteki) u góry. Kliknij pozycję **Add External Jar** (Dodaj zewnętrzny plik jar) i wybierz plik `\SDK\Android\DeviceMessaging\lib\amazon-device-messaging-*.jar` z katalogu, do którego wyodrębniono zestaw Amazon SDK.
3. Pobierz zestaw SDK usługi Notification Hubs dla systemu Android (link).
4. Rozpakuj pakiet, a następnie przeciągnij plik `notification-hubs-sdk.jar` do folderu `libs` w programie Eclipse.

Edytuj manifest aplikacji w celu zapewnienia obsługi usługi ADM:

1. Dodaj przestrzeń nazw Amazon do głównego elementu manifestu:

    ```xml
    xmlns:amazon="http://schemas.amazon.com/apk/res/android"
    ```

2. Dodaj uprawnienia jako pierwszy element w elemencie manifestu. Zastąp wartość **[YOUR PACKAGE NAME]** nazwą pakietu użytego do utworzenia aplikacji.

    ```xml
    <permission
        android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
        android:protectionLevel="signature" />

    <uses-permission android:name="android.permission.INTERNET"/>

    <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

    <!-- This permission allows your app access to receive push notifications
    from ADM. -->
    <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

    <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    ```
3. Wstaw następujący element jako pierwszy element podrzędny elementu aplikacji. Pamiętaj, aby zastąpić wartość **[YOUR SERVICE NAME]** nazwą programu obsługi komunikatów ADM tworzonego w następnej sekcji (w tym pakietu) oraz wartość **[YOUR PACKAGE NAME]** nazwą pakietu, którego użyto do utworzenia aplikacji.

    ```xml
    <amazon:enable-feature
        android:name="com.amazon.device.messaging"
        android:required="true"/>
    <service
        android:name="[YOUR SERVICE NAME]"
        android:exported="false" />

    <receiver
        android:name="[YOUR SERVICE NAME]$Receiver" />

        <!-- This permission ensures that only ADM can send your app registration broadcasts. -->
        android:permission="com.amazon.device.messaging.permission.SEND" >

        <!-- To interact with ADM, your app must listen for the following intents. -->
        <intent-filter>
        <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
        <action android:name="com.amazon.device.messaging.intent.RECEIVE" />

        <!-- Replace the name in the category tag with your app's package name. -->
        <category android:name="[YOUR PACKAGE NAME]" />
        </intent-filter>
    </receiver>
    ```

## <a name="create-your-adm-message-handler"></a>Tworzenie programu obsługi usługi ADM

1. Utwórz nową klasę dziedziczącą z klasy `com.amazon.device.messaging.ADMMessageHandlerBase` i nadaj jej nazwę `MyADMMessageHandler`, jak przedstawiono na następującej ilustracji:

    ![][6]

2. Dodaj następujące instrukcje `import`:

    ```java
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.support.v4.app.NotificationCompat;
    import com.amazon.device.messaging.ADMMessageReceiver;
    import com.microsoft.windowsazure.messaging.NotificationHub
    ```

3. Dodaj następujący kod w utworzonej klasie. Pamiętaj, aby zastąpić nazwę centrum i parametry połączenia (nasłuchiwanie):

    ```java
    public static final int NOTIFICATION_ID = 1;
    private NotificationManager mNotificationManager;
    NotificationCompat.Builder builder;
        private static NotificationHub hub;
    public static NotificationHub getNotificationHub(Context context) {
        Log.v("com.wa.hellokindlefire", "getNotificationHub");
        if (hub == null) {
            hub = new NotificationHub("[hub name]", "[listen connection string]", context);
        }
        return hub;
    }

    public MyADMMessageHandler() {
            super("MyADMMessageHandler");
        }

        public static class Receiver extends ADMMessageReceiver
        {
            public Receiver()
            {
                super(MyADMMessageHandler.class);
            }
        }

        private void sendNotification(String msg) {
            Context ctx = getApplicationContext();

            mNotificationManager = (NotificationManager)
                ctx.getSystemService(Context.NOTIFICATION_SERVICE);

        PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);

        NotificationCompat.Builder mBuilder =
                new NotificationCompat.Builder(ctx)
                .setSmallIcon(R.mipmap.ic_launcher)
                .setContentTitle("Notification Hub Demo")
                .setStyle(new NotificationCompat.BigTextStyle()
                        .bigText(msg))
                .setContentText(msg);

            mBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
    }
    ```
4. Dodaj następujący kod do metody `OnMessage()`:

    ```java
    String nhMessage = intent.getExtras().getString("msg");
    sendNotification(nhMessage);
    ```
5. Dodaj następujący kod do metody `OnRegistered`:

    ```java
    try {
        getNotificationHub(getApplicationContext()).register(registrationId);
    } catch (Exception e) {
        Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
    }
    ```
6. Dodaj następujący kod do metody `OnUnregistered`:

    ```java
    try {
        getNotificationHub(getApplicationContext()).unregister();
    } catch (Exception e) {
        Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
    }
    ```
7. W metodzie `MainActivity` dodaj następującą instrukcję import:

    ```java
    import com.amazon.device.messaging.ADM;
    ```
8. Dodaj następujący kod na końcu metody `OnCreate`:

    ```java
    final ADM adm = new ADM(this);
    if (adm.getRegistrationId() == null)
    {
        adm.startRegister();
    } else {
        new AsyncTask() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        MyADMMessageHandler.getNotificationHub(getApplicationContext()).register(adm.getRegistrationId());
                    } catch (Exception e) {
                        Log.e("com.wa.hellokindlefire", "Failed registration with hub", e);
                        return e;
                    }
                    return null;
                }
            }.execute(null, null, null);
    }
    ```

## <a name="add-your-api-key-to-your-app"></a>Dodawanie klucza interfejsu API do aplikacji

1. W programie Eclipse utwórz nowy plik o nazwie `api_key.txt` w katalogu zasobów projektu.
2. Otwórz plik i skopiuj klucz interfejsu API wygenerowany w portalu dla deweloperów firmy Amazon.

## <a name="run-the-app"></a>Uruchamianie aplikacji

1. Uruchom emulator.
2. W emulatorze szybko przesuń od góry i kliknij pozycję **Settings** (Ustawienia), a następnie kliknij pozycję **My account** (Moje konto) i zarejestruj prawidłowe konto Amazon.
3. Uruchom aplikację w programie Eclipse.

> [!NOTE]
> W przypadku wystąpienia problemu sprawdź czas emulatora (lub urządzenia). Wartość czasu musi być dokładna. Aby zmienić czas emulatora urządzenia Kindle, możesz uruchomić następujące polecenie w katalogu narzędzi platformy zestawu SDK dla systemu Android:

```shell
adb shell  date -s "yyyymmdd.hhmmss"
```

## <a name="send-a-notification-message"></a>Wysyłanie komunikatu powiadomienia

Aby wysłać komunikat przy użyciu programu .NET:

```csharp
static void Main(string[] args)
{
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

    hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
}
```

![][7]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku wysłano rozgłaszane powiadomienia do wszystkich urządzeń Kindle zarejestrowanych w zapleczu. Aby dowiedzieć się, jak wysyłać powiadomienia push do konkretnych urządzeń Kindle, przejdź do następującego samouczka:  Następujący samouczek przedstawia sposób wypychania powiadomień do konkretnych urządzeń z systemem Android, ale możesz użyć tej samej logiki do wypychania powiadomień do konkretnych urządzeń Kindle.

> [!div class="nextstepaction"]
>[Wysyłanie powiadomień push do konkretnych urządzeń](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- URLs. -->
[portalu dla deweloperów firmy Amazon]: https://developer.amazon.com/home.html
[pobierz zestaw SDK]: https://developer.amazon.com/public/resources/development-tools/sdk
[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
