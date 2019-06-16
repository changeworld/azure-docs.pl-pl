---
title: Wysyłanie powiadomień wypychanych bezpiecznego przy użyciu usługi Azure Notification Hubs
description: Dowiedz się, jak wysyłać powiadomienia wypychane bezpiecznych aplikacji systemu Android na platformie Azure. Przykłady kodu napisane w języku Java i C#.
documentationcenter: android
keywords: powiadomienie wypychane, powiadomienia wypychane, wypychanie wiadomości, powiadomienia wypychane w systemie android
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
ms.assetid: daf3de1c-f6a9-43c4-8165-a76bfaa70893
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 27536b0a3d7e0858a5660b4c7b33cb6679b5fbf1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60874564"
---
# <a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Wysyłanie powiadomień wypychanych bezpiecznego przy użyciu usługi Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Aplikacje uniwersalne systemu Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Omówienie

> [!IMPORTANT]
> Do wykonania kroków tego samouczka potrzebne jest aktywne konto platformy Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać więcej informacji, zobacz [bezpłatnej wersji próbnej Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Obsługę powiadomień wypychanych w systemie Microsoft Azure pozwala na dostęp, łatwy w użyciu, wieloplatformową infrastruktury komunikatów powiadomień wypychanych skalowanych w poziomie, co znacznie upraszcza implementację powiadomienia wypychane dla aplikacji firmowych i komercyjnych dla platformy urządzeń przenośnych.

Ze względu na przepisami ograniczenia zabezpieczeń, czasami aplikacja może mają zostać uwzględnione coś powiadomienia, które nie mogą być przekazywane za pośrednictwem infrastruktury powiadomień wypychanych standardowych. W tym samouczku opisano sposób osiągnięcia tego samego środowiska poprzez wysłanie informacji poufnych przez bezpieczne, uwierzytelnione połączenie między urządzeniu z systemem Android klienta i zaplecza aplikacji.

Na wysokim poziomie przepływ jest następująca:

1. Zaplecze aplikacji:
   * Ładunek bezpiecznego magazynów w wewnętrznej bazie danych.
   * Wysyła identyfikator tego powiadomienia na urządzeniu z systemem Android (nie informacji poufnych są wysyłane).
2. Aplikacja na urządzeniu, podczas odbierania powiadomienia:
   * Urządzenie z systemem Android kontaktuje się z zaplecza bezpiecznego ładunku żądania.
   * Aplikację można wyświetlić ładunku powiadomienie na urządzenie.

Należy zauważyć, że w poprzednim przepływu (i w ramach tego samouczka) zakłada się, że urządzenie przechowuje token uwierzytelniania w magazynie lokalnym, po użytkownik loguje. Ta metoda gwarantuje nie zakłóca pracy jako urządzenia mogą pobierać ładunku bezpieczne powiadomień, używające tego tokenu. Jeśli aplikacja nie przechowuje tokeny uwierzytelniania na urządzeniu lub tokeny te mogą wygasnąć, aplikacji urządzenia, po otrzymaniu powiadomienia wypychanego powinien być wyświetlany ogólny powiadomienie monitowania użytkownika do uruchomienia aplikacji. Aplikacja następnie uwierzytelnia użytkownika i zawiera ładunek powiadomienia.

W tym samouczku pokazano, jak wysyłać powiadomienia wypychane bezpieczne. Opiera się na [powiadamianie użytkowników](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) samouczek, więc należy wykonać kroki opisane w tym samouczku najpierw Jeśli jeszcze go.

> [!NOTE]
> W tym samouczku przyjęto założenie, że utworzone i skonfigurowane Centrum powiadomień, zgodnie z opisem w [wprowadzenie do usługi Notification Hubs (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Modyfikowanie projektu systemu Android

Teraz, gdy został zmodyfikowany z poziomu aplikacji zaplecza do wysyłania tylko *identyfikator* powiadomienia wypychane, trzeba zmienić aplikacji systemu Android do obsługi powiadomienia i wywołania zwrotnego zaplecza do pobrania zabezpieczoną wiadomość, która ma być wyświetlany.
Aby osiągnąć ten cel, upewnij się, że aplikacja dla systemu Android wie, jak można uwierzytelniać za pomocą usługi zaplecza, po odebraniu powiadomienia wypychane.

Teraz, zmodyfikować *logowania* przepływu, aby zapisać wartości nagłówka uwierzytelniania w udostępnionych preferencji aplikacji. Mechanizmy analogiczne może służyć do przechowywania dowolnego token uwierzytelniania (na przykład tokenów protokołu OAuth), która aplikacja ma korzystać bez poświadczeń użytkownika.

1. W projekcie aplikacji dla systemu Android, Dodaj następujące stałe w górnej części `MainActivity` klasy:

    ```java
    public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
    public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
    ```
2. Nadal w `MainActivity` klasy, zaktualizuj `getAuthorizationHeader()` metoda zawiera następujący kod:

    ```java
    private String getAuthorizationHeader() throws UnsupportedEncodingException {
        EditText username = (EditText) findViewById(R.id.usernameText);
        EditText password = (EditText) findViewById(R.id.passwordText);
        String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
        basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

        SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

        return basicAuthHeader;
    }
    ```
3. Dodaj następujący kod `import` instrukcji na górze `MainActivity` pliku:

    ```java
    import android.content.SharedPreferences;
    ```

Teraz zmienić program obsługi, który jest wywoływane w przypadku otrzymania powiadomienia.

1. W `MyHandler` klasy zmiany `OnReceive()` metodę, aby zawierać:

    ```java
    public void onReceive(Context context, Bundle bundle) {
        ctx = context;
        String secureMessageId = bundle.getString("secureId");
        retrieveNotification(secureMessageId);
    }
    ```
2. Następnie dodaj `retrieveNotification()` metody, zastępując symbol zastępczy `{back-end endpoint}` z punktem końcowym zaplecza uzyskanego podczas wdrażania usługi zaplecza:

    ```java
    private void retrieveNotification(final String secureMessageId) {
        SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);

        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {
                    HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                    request.addHeader("Authorization", "Basic "+authorizationHeader);
                    HttpResponse response = new DefaultHttpClient().execute(request);
                    if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                        Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                        throw new RuntimeException("Error retrieving secure notification");
                    }
                    String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                    JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                    sendNotification(secureNotification.getString("Payload"));
                } catch (Exception e) {
                    Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                    return e;
                }
                return null;
            }
        }.execute(null, null, null);
    }
    ```

Ta metoda wywołuje Twojej aplikacji zaplecza do pobierania zawartości powiadomień przy użyciu poświadczeń przechowywanych w udostępnionych preferencji i wyświetla je jako normalny powiadomień. Wygląda powiadomienie dla użytkownika aplikacji, tak samo jak inne powiadomienia wypychanego.

Zaleca się do obsługi przypadków brakuje właściwości nagłówka uwierzytelniania lub odrzucenie przez serwer zaplecza. Określone obsługi tych przypadkach zależy przede wszystkim środowiska użytkownika docelowego. Jedną z opcji jest powiadomienia wraz z monitem o ogólnych w celu uwierzytelnienia użytkownika można pobrać rzeczywiste powiadomień.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aby uruchomić aplikację, wykonaj następujące czynności:

1. Upewnij się, że **AppBackend** jest wdrażana na platformie Azure. Jeśli używasz programu Visual Studio, uruchom **AppBackend** aplikacji internetowego interfejsu API. Zostanie wyświetlona strona sieci web platformy ASP.NET.
2. W środowisku Eclipse Uruchom aplikację na urządzenie fizyczne systemu Android lub w emulatorze.
3. W aplikacji systemu Android interfejsu użytkownika wprowadź nazwę użytkownika i hasło. Mogą to być dowolny ciąg, ale muszą one mieć taką samą wartość.
4. W aplikacji systemu Android interfejsu użytkownika, kliknij przycisk **Zaloguj**. Następnie kliknij przycisk **wysyłania wypychanych**.
