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
ms.date: 04/29/2019
ms.author: jowargo
ms.openlocfilehash: abc77ad4d06dc719ee1a89cd8fcf29d42d96b483
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147652"
---
# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>Rozpoczynanie pracy z usługą Notification Hubs dla aplikacji dla urządzeń Kindle

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia wypychane do aplikacji dla urządzeń Kindle przy użyciu usługi Azure Notification Hubs. Utworzysz pustą aplikację dla urządzeń Kindle służącą do odbierania powiadomień push przy użyciu usługi Amazon Device Messaging (ADM).

W tym samouczku utworzysz/zaktualizujesz kod, aby wykonać następujące zadania:

> [!div class="checklist"]
> * Dodawanie nowej aplikacji do portalu dla deweloperów
> * Tworzenie klucza interfejsu API
> * Tworzenie i konfigurowanie Centrum powiadomień
> * Konfigurowanie aplikacji
> * Tworzenie programu obsługi usługi ADM
> * Dodawanie klucza interfejsu API do aplikacji
> * Uruchamianie aplikacji
> * Wysyłanie powiadomienia testowego

## <a name="prerequisites"></a>Wymagania wstępne

- [Android Studio](https://developer.android.com/studio/?gclid=CjwKCAjwwZrmBRA7EiwA4iMzBPZ9YYmR0pbb5LtjnWhWCxe8PWrmjmeaR6ad5ksCr_j2mmkVj_-o6hoCAqwQAvD_BwE).
- Postępuj zgodnie z instrukcjami w temacie [Setting Up Your Development Environment](https://developer.amazon.com/docs/fire-tablets/ft-set-up-your-development-environment.html) (Konfigurowanie środowiska projektowego), aby skonfigurować środowisko projektowe dla urządzeń Kindle.

## <a name="add-a-new-app-to-the-developer-portal"></a>Dodawanie nowej aplikacji do portalu dla deweloperów

1. Zaloguj się do [portalu dla deweloperów firmy Amazon](https://developer.amazon.com/apps-and-games/console/apps/list.html).
2. Wybierz **Dodaj nową aplikację**, a następnie wybierz pozycję **Android**.  

    ![Przycisk Dodaj nowe aplikacji](./media/notification-hubs-kindle-get-started/add-new-app-button.png)
1. Na **nowość aplikacji** strony, wykonaj następujące kroki, aby uzyskać **klucz aplikacji**:
    1. Wprowadź nazwę dla **Tytuł aplikacji**.
    2. Wybierz dowolne **kategorii** (na przykład: edukacji)
    4. Wprowadź adres e-mail **adres e-mail pomocy technicznej klienta** pola. 
    5. Wybierz pozycję **Zapisz**.

        ![Nowa strona przesyłania aplikacji](./media/notification-hubs-kindle-get-started/new-app-submission-page.png) 
2.  U góry strony, przełącz się do **App Services** kartę.

    ![Karta usługi aplikacji](./media/notification-hubs-kindle-get-started/app-services-tab.png)
1. Na **App Services** karty, przewiń w dół i wybierz **wyświetlanie reklam mobilnych** w **Mobile Ads** sekcji. Zostanie wyświetlony **Mobile Ads** strony na nowej karcie w przeglądarce sieci web. 

    ![Sekcja Mobile Ads - link widoku Mobile Ads](./media/notification-hubs-kindle-get-started/view-mobile-ads-link.png)
1. Na **Mobile Ads** wykonaj następujące czynności: 
    1. Określ, czy aplikacja jest skierowany głównie dzieci w obszarze 13. Na potrzeby tego samouczka wybierz **nie**.
    1. Wybierz **przesłać**. 

        ![Strona Mobile Ads](./media/notification-hubs-kindle-get-started/mobile-ads-page.png)
    3. Kopiuj **klucz aplikacji** z **Mobile Ads** strony. 

        ![Klucz aplikacji](./media/notification-hubs-kindle-get-started/application-key.png)
3.  Teraz przejdź do karty przeglądarki sieci web, która ma **App Services** Otwórz, a następnie wykonaj następujące czynności:
    1. Przewiń do **Device Messaging** sekcji.     
    1. Rozwiń **wybierz istniejący profil zabezpieczeń lub Utwórz nową**, a następnie wybierz pozycję **Utwórz profil zabezpieczeń**. 

        ![Tworzenie przycisku profil zabezpieczeń](./media/notification-hubs-kindle-get-started/create-security-profile-button.png)
    1. Wprowadź **nazwa** dla swojego profilu zabezpieczeń. 
    2. Wprowadź **opis** dla swojego profilu zabezpieczeń. 
    3. Wybierz pozycję **Zapisz**. 

        ![Zapisz profil zabezpieczeń](./media/notification-hubs-kindle-get-started/save-security-profile.png)
    1. Wybierz **Włącz Device Messaging** do włączenia obsługi wiadomości na ten profil zabezpieczeń urządzenia. 

        ![Umożliwianie przesyłania wiadomości urządzenia](./media/notification-hubs-kindle-get-started/enable-device-messaging.png)
    1. Następnie wybierz **Wyświetl profil zabezpieczeń** na stronie wyników. 
1. Teraz na **profil zabezpieczeń** wykonaj następujące czynności: 
    1. Przełącz się do **ustawień sieci Web** , a następnie skopiuj **identyfikator klienta** i **klucz tajny klienta** wartości do późniejszego użycia. 

        ![Pobierz klucz tajny i identyfikator klienta](./media/notification-hubs-kindle-get-started/client-id-secret.png) 
    2. Przełącz się do **ustawienia systemu Android/Kindle** strony, a nie zamykaj strony. Wprowadzisz te wartości w następnej sekcji. 

## <a name="create-an-api-key"></a>Tworzenie klucza interfejsu API
1. Otwórz wiersz polecenia z uprawnieniami administratora.
2. Przejdź do folderu zestawu SDK systemu Android.
3. Wprowadź następujące polecenie:

    ```shell
    keytool -list -v -alias androiddebugkey -keystore ./debug.keystore
    ```
4. Jako hasło **keystore** wpisz wartość **android**.
5. Kopiuj **MD5** i **SHA256** odcisków palców. 
6. Tworzenie kopii w portalu dla deweloperów na **ustawienia systemu Android/Kindle** karcie, wykonaj następujące czynności: 
    1. Wprowadź **nazwę klucza interfejsu API**. 
    2. Wprowadź **nazwa pakietu** dla aplikacji (na przykład **com.fabrikam.mykindleapp**) i **MD5** wartość.
        
        >[!IMPORTANT]
        > Podczas tworzenia aplikacji w programie Android Studio, w tym miejscu użyć podanej nazwy pakietu. 
    1. Wklej **sygnaturę MD5** wcześniej zostały skopiowane. 
    2. Wklej **podpisu SHA256** wcześniej zostały skopiowane.  
    3. Wybierz **Generowanie nowego klucza**.

        ![Ustawienia systemu android/Kindle — generowanie klucza](./media/notification-hubs-kindle-get-started/android-kindle-settings.png)
    4. Teraz wybierz **Pokaż** na liście, aby wyświetlić klucz interfejsu API. 

        ![Ustawienia systemu android/Kindle — Pokaż klucz interfejsu API](./media/notification-hubs-kindle-get-started/show-api-key-button.png) 
    5. W **szczegółów klucza interfejsu API** okna, skopiuj klucz interfejsu API i zapisać go w innym miejscu. Następnie wybierz **X** w prawym górnym rogu, aby zamknąć okno. 


## <a name="create-and-configure-a-notification-hub"></a>Tworzenie i konfigurowanie Centrum powiadomień

1. Wykonaj czynności opisane w [Tworzenie Centrum powiadomień platformy Azure w witrynie Azure portal](create-notification-hub-portal.md) artykuł, aby utworzyć Centrum powiadomień. 
2. Wybierz **Amazon (ADM)** w obszarze **ustawienia** menu.
3. Wklej **identyfikator klienta** i **klucz tajny klienta** został wcześniej zapisany. 
4. Wybierz pozycję **Zapisz** na pasku narzędzi. 

    ![Konfigurowanie ustawień usługi ADM dla Centrum powiadomień](./media/notification-hubs-kindle-get-started/configure-notification-hub.png)
5. Wybierz **zasady dostępu** w menu po lewej stronie, a następnie wybierz pozycję **kopiowania** przycisk, aby parametry połączenia dla **DefaultListenSharedAccessSignature** zasad. Zapisz go gdzieś. Zostanie on użyty później w kodzie źródłowym. 

    ![Centrum powiadomień — parametry połączenia nasłuchiwania](./media/notification-hubs-kindle-get-started/event-hub-listen-connection-string.png)    

## <a name="set-up-your-application"></a>Konfigurowanie aplikacji

1. Uruchom program Android Studio. 
2. Wybierz **pliku**, wskaż polecenie **New**, a następnie wybierz pozycję **nowy projekt**. 
3. W **wybierz projekt** okna na **telefony i tablety** zaznacz **puste działanie**i wybierz **dalej**. 
4. W **Skonfiguruj projekt** okna, wykonaj następujące czynności:
    1. Wprowadź **Nazwa aplikacji**. Można zapewnić zgodność z nazwą aplikacji, utworzonej w portalu dla deweloperów firmy Amazon. 
    2. Wprowadź **nazwa pakietu**. 
        
        >[!IMPORTANT]
        >Nazwa pakietu musi odpowiadać nazwie pakietu, które określiłeś w portalu dla deweloperów firmy Amazon.
    3. Przejrzyj i zaktualizuj pozostałe wartości zgodnie z potrzebami. 
    4. Wybierz pozycję **Finish** (Zakończ). 

        ![Konfigurowanie projektu dla systemu Android](./media/notification-hubs-kindle-get-started/new-android-studio-project.png)
5. Pobierz [Amazon SDK dla deweloperów dla systemu Android](https://developer.amazon.com/sdk-download) biblioteki na dysku twardym. Wyodrębnij plik zip zestawu SDK.
6. W programie Android Studio, należy zmienić strukturę folderów z **Android** do **projektu** Jeśli już nie jest równa **projektu**. 

    ![Android Studio — Przełącz do struktury projektu](./media/notification-hubs-kindle-get-started/android-studio-project-view.png)
7. Rozwiń **aplikacji** się **libs** folder w widoku drzewa.     
8. W oknie Eksploratora plików przejdź do folderu, w której pobrano zestawy Android SDK Amazon.
9. Naciśnij klawisz **CTRL** i przeciągnij i upuść **amazon — urządzenia — Obsługa wiadomości — 1.0.1.jar** plik **lib** węzeł w widoku drzewa. 

    ![Android Studio — Dodaj urządzenie Amazon komunikatów JAR](./media/notification-hubs-kindle-get-started/drag-drop-amazon-device-messaging-jar.png)
9. W **kopiowania** wybierz **OK**. Jeśli widzisz **przenieść** okna zamiast **kopiowania** oknie, zamknij je i spróbuj wykonać operację przeciągania i upuszczania za pomocą **CTRL** wciśnięcie przycisku. 

    ![Android Studio — kopia pliku JAR](./media/notification-hubs-kindle-get-started/copy-jar-window.png)
10. Dodaj następującą instrukcję, aby **build.gradle aplikacji** w pliku **zależności** sekcji: `implementation files('libs/amazon-device-messaging-1.0.1.jar')`. 

    ![Android Studio — Dodawanie usługi ADM do build.gradle aplikacji](./media/notification-hubs-kindle-get-started/adm-build-gradle.png)
11. W `Build.Gradle` plik **aplikacji**, Dodaj następujące wiersze w **zależności** sekcji: 

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```
12. Dodaj następujące repozytorium **po** **zależności** sekcji:

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```
13. W edytorze dla **build.gradle** plik **aplikacji**, wybierz opcję **Synchronizuj teraz** na pasku narzędzi. 

    ![Android Studio — build.gradle synchronizacji aplikacji](./media/notification-hubs-kindle-get-started/gradle-sync-now.png)
14. Przejdź z powrotem do struktury systemu Android w widoku drzewa.  Dodaj przestrzeń nazw Amazon do głównego elementu manifestu:

    ```xml
    xmlns:amazon="http://schemas.amazon.com/apk/res/android"
    ```
   
    ![Przestrzeń nazw Amazon w manifeście](./media/notification-hubs-kindle-get-started/amazon-namespace-manifest.png)
2. Dodaj uprawnienia jako pierwszy element w elemencie manifestu. Zastąp **[YOUR PACKAGE NAME]** przy użyciu pakietu, który został użyty do utworzenia aplikacji.

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
3. Wstaw następujący element jako pierwszy element podrzędny elementu aplikacji. Zastąp **[YOUR PACKAGE NAME]** nazwą pakietu za pomocą którego użyto do utworzenia aplikacji. Instrukcje dotyczące tworzenia klasy MyADMMessageHandler w następnym kroku. 

    ```xml
        <amazon:enable-feature
            android:name="com.amazon.device.messaging"
            android:required="true"/>
        <service
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler"
            android:exported="false" />
        <receiver
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler$Receiver"
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

1. Dodaj nową klasę do `com.fabrikam.mykindleapp` pakiet w projekcie, który dziedziczy z `com.amazon.device.messaging.ADMMessageHandlerBase` i nadaj mu nazwę `MyADMMessageHandler`, jak pokazano na poniższej ilustracji:

    ![Utwórz klasę MyADMMessageHandler](./media/notification-hubs-kindle-get-started/create-adm-message-handler.png)

2. Dodaj następujący kod `import` instrukcje `MyADMMessageHandler` klasy:

    ```java
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.support.v4.app.NotificationCompat;
    import android.util.Log;
    import com.amazon.device.messaging.ADMMessageReceiver;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    ```

3. Dodaj następujący kod w utworzonej klasie. Należy pamiętać, `[HUB NAME]` i `[LISTEN CONNECTION STRING]` o nazwie parametry połączenia Centrum i nasłuchiwał powiadomień: 

    ```java
    public static final int NOTIFICATION_ID = 1;
    private NotificationManager mNotificationManager;
    NotificationCompat.Builder builder;
    private static NotificationHub hub;

    public static NotificationHub getNotificationHub(Context context) {
        Log.v("com.wa.hellokindlefire", "getNotificationHub");
        if (hub == null) {
            hub = new NotificationHub("[HUB NAME]", "[HUB NAMESPACE CONNECTION STRING]", context);
        }
        return hub;
    }

    public MyADMMessageHandler() {
        super("MyADMMessageHandler");
    }

    @Override
    protected void onMessage(Intent intent) {
        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);
    }

    @Override
    protected void onRegistrationError(String s) {

    }

    @Override
    protected void onRegistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).register(s);
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
        }
    }

    @Override
    protected void onUnregistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).unregister();
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
        }
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
## <a name="create-an-adm-object"></a>Utwórz obiekt usługi ADM
1 w `MainActivity.java` Dodaj następujące instrukcje importu:

    ```java
    import android.os.AsyncTask;
    import android.util.Log;
    import com.amazon.device.messaging.ADM;
    ```
2. Dodaj następujący kod na końcu metody `OnCreate`:

    ```java
    final ADM adm = new ADM(this);
    if (adm.getRegistrationId() == null)
    {
        adm.startRegister();
    } else {
        new AsyncTask() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {                         MyADMMessageHandler.getNotificationHub(getApplicationContext()).register(adm.getRegistrationId());
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
1. Wykonaj następujące kroki, aby dodać folder zasobów do projektu. 
    1. Przełącz się do **projektu** widoku. 
    2. Kliknij prawym przyciskiem myszy **aplikacji**.
    3. Wybierz pozycję **Nowy**.
    4. Wybierz **folderu**. 
    5. Następnie wybierz **folderu zasobów**. 

        ![Dodawanie menu folder zasobów](./media/notification-hubs-kindle-get-started/add-assets-folder-menu.png)    
    6. Na **konfigurowania składnika** wykonaj następujące czynności:
        1. Wybierz **zmienić lokalizację folderu**
        2. Upewnij się, że folder jest ustawiony na: `src/main/assets`.
        3. Wybierz pozycję **Finish** (Zakończ). 
        
            ![Skonfiguruj folder zasobów](./media/notification-hubs-kindle-get-started/configure-asset-folder.png)
2. Dodaj plik o nazwie **api_key.txt** do **zasoby** folderu. W widoku drzewa rozwiń **aplikacji**, rozwiń węzeł **src**, rozwiń węzeł **głównego**i kliknij prawym przyciskiem myszy **zasoby**, wskaż polecenie **nowy**, a następnie wybierz pozycję **pliku**. Wprowadź **api_key.txt** dla nazwy pliku. 3. 
5. Kopiowanie klucza interfejsu API, który został wygenerowany w portalu dla deweloperów firmy Amazon do pliku api_key.txt. 
6. Skompiluj projekt. 

## <a name="run-the-app"></a>Uruchamianie aplikacji
1. Na urządzenie Kindle, szybko przesuń od góry, a następnie kliknij przycisk **ustawienia**, a następnie kliknij przycisk **Moje konto** i zarejestrowanie prawidłowe konto Amazon.
2. Uruchom aplikację na urządzenie Kindle z systemem Android Studio. 

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

Przykładowy kod, zobacz [w tym przykładzie w serwisie GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/blob/master/Samples/SendPushSample/SendPushSample/Program.cs).

![][7]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku wysłano rozgłaszane powiadomienia do wszystkich urządzeń Kindle zarejestrowanych w zapleczu. Aby dowiedzieć się, jak wysyłać powiadomienia push do konkretnych urządzeń Kindle, przejdź do następującego samouczka:  Następujący samouczek przedstawia sposób wypychania powiadomień do konkretnych urządzeń z systemem Android, ale możesz użyć tej samej logiki do wypychania powiadomień do konkretnych urządzeń Kindle.

> [!div class="nextstepaction"]
>[Wysyłanie powiadomień push do konkretnych urządzeń](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- URLs. -->
[Amazon developer portal]: https://developer.amazon.com/home.html
[download the SDK]: https://developer.amazon.com/public/resources/development-tools/sdk
[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
