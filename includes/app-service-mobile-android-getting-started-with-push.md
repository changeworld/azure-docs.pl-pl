---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: e66e7d4646b650d2d811d3807db04605dfddeded
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183745"
---
1. W projekcie **aplikacji** otwórz `AndroidManifest.xml`plik . Dodaj następujący kod `application` po tagu otwierającym:

    ```xml
    <service android:name=".ToDoMessagingService">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT"/>
        </intent-filter>
    </service>
    <service android:name=".ToDoInstanceIdService">
        <intent-filter>
            <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
        </intent-filter>
    </service>
    ```

2. Otwórz plik `ToDoActivity.java`i włóż następujące zmiany:

    - Dodaj instrukcję importu:

        ```java
        import com.google.firebase.iid.FirebaseInstanceId;
        ```

    - Zmień definicję `MobileServiceClient` ze **prywatnego** na **prywatny statyczny**, więc teraz wygląda tak:

        ```java
        private static MobileServiceClient mClient;
        ```

    - Dodaj `registerPush` metodę:

        ```java
        public static void registerPush() {
            final String token = FirebaseInstanceId.getInstance().getToken();
            if (token != null) {
                new AsyncTask<Void, Void, Void>() {
                    protected Void doInBackground(Void... params) {
                        mClient.getPush().register(token);
                        return null;
                    }
                }.execute();
            }
        }
        ```

    - Zaktualizuj **onCreate** metody `ToDoActivity` klasy. Upewnij się, aby dodać `MobileServiceClient` ten kod po wystąpieniu.

        ```java
        registerPush();
        ```

3. Dodaj nową klasę do obsługi powiadomień. W Eksploratorze projektu otwórz **aplikację** > **Java** > **węzły obszaru nazw projektu** i kliknij prawym przyciskiem myszy węzeł nazwy pakietu. Kliknij **pozycję Nowy**, a następnie kliknij pozycję Klasa Języka **Java**. W kategorii `ToDoMessagingService`Nazwa wpisz , a następnie kliknij przycisk OK. Następnie należy zastąpić deklarację klasy:

    ```java
    import android.app.Notification;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;

    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;

    public class ToDoMessagingService extends FirebaseMessagingService {

        private static final int NOTIFICATION_ID = 1;

        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            String message = remoteMessage.getData().get("message");
            if (message != null) {
                sendNotification("Notification Hub Demo", message);
            }
        }

        private void sendNotification(String title, String messageBody) {
            PendingIntent contentIntent = PendingIntent.getActivity(this, 0, new Intent(this, ToDoActivity.class), 0);
            Notification.Builder notificationBuilder = new Notification.Builder(this)
                    .setSmallIcon(R.drawable.ic_launcher)
                    .setContentTitle(title)
                    .setContentText(messageBody)
                    .setContentIntent(contentIntent);
            NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
            if (notificationManager != null) {
                notificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
            }
        }
    }
    ```

4. Dodaj inną klasę do obsługi aktualizacji tokenu. Utwórz `ToDoInstanceIdService` klasę java i zastąp deklarację klasy:

    ```java
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class ToDoInstanceIdService extends FirebaseInstanceIdService {

        @Override
        public void onTokenRefresh() {
            ToDoActivity.registerPush();
        }
    }
    ```

Aplikacja jest teraz aktualizowana w celu obsługi powiadomień wypychanych.
