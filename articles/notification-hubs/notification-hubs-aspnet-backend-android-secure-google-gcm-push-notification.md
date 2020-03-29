---
title: Wysyłanie powiadomień o bezpiecznych wypychach za pomocą usługi Azure Notification Hubs
description: Dowiedz się, jak wysyłać bezpieczne powiadomienia wypychane do aplikacji dla systemu Android z platformy Azure. Przykłady kodu napisane w języku Java i C#.
documentationcenter: android
keywords: powiadomienie push, powiadomienia push, wiadomości push, powiadomienia push android
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: daf3de1c-f6a9-43c4-8165-a76bfaa70893
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 419a9f9b5ce698c7516edd55856cbea9891ba029
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212187"
---
# <a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Wysyłanie powiadomień o bezpiecznych wypychach za pomocą usługi Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Aplikacje uniwersalne systemu Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Omówienie

> [!IMPORTANT]
> Do wykonania kroków tego samouczka potrzebne jest aktywne konto platformy Azure. Jeśli nie masz konta, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Obsługa powiadomień wypychanych na platformie Microsoft Azure umożliwia dostęp do łatwej w użyciu, wieloplatformowej, skalowej w poziomie infrastruktury komunikatów wypychanych, co znacznie upraszcza wdrażanie powiadomień wypychanych zarówno dla aplikacji konsumenckich, jak i korporacyjnych platform mobilnych.

Ze względu na ograniczenia regulacyjne lub zabezpieczeń czasami aplikacja może chcieć dołączyć coś w powiadomieniu, które nie mogą być przesyłane za pośrednictwem standardowej infrastruktury powiadomień wypychanych. W tym samouczku opisano, jak osiągnąć to samo środowisko, wysyłając poufne informacje za pośrednictwem bezpiecznego, uwierzytelnionego połączenia między urządzeniem klienckim z systemem Android a zapleczem aplikacji.

Na wysokim poziomie przepływ jest następujący:

1. Back-end aplikacji:
   * Przechowuje bezpieczne ładunku w bazie danych zaplecza.
   * Wysyła identyfikator tego powiadomienia do urządzenia z systemem Android (nie są wysyłane żadne bezpieczne informacje).
2. Aplikacja na urządzeniu, po otrzymaniu powiadomienia:
   * Urządzenie z systemem Android kontaktuje się z zapleczem żądającym bezpiecznego ładunku.
   * Aplikacja może wyświetlić ładunek jako powiadomienie na urządzeniu.

Należy pamiętać, że w poprzednim przepływie (i w tym samouczku) zakłada się, że urządzenie przechowuje token uwierzytelniania w magazynie lokalnym, po zalogowaniu się użytkownika. Takie podejście gwarantuje bezproblemowe środowisko, ponieważ urządzenie może pobrać bezpieczne ładunku powiadomienia przy użyciu tego tokenu. Jeśli aplikacja nie przechowuje tokenów uwierzytelniania na urządzeniu lub jeśli te tokeny mogą wygasnąć, aplikacja urządzenia po otrzymaniu powiadomienia wypychanego powinna wyświetlić ogólne powiadomienie z monitem o uruchomienie aplikacji. Następnie aplikacja uwierzytelnia użytkownika i wyświetla ładunek powiadomień.

W tym samouczku pokazano, jak wysyłać bezpieczne powiadomienia wypychane. Opiera się na podziale na [użytkowników](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) samouczka, więc należy wykonać kroki w tym samouczku, jeśli jeszcze tego nie zrobiłeś.

> [!NOTE]
> W tym samouczku założono, że utworzono i skonfigurowano centrum powiadomień zgodnie z opisem w [artykule Wprowadzenie do centrów powiadomień (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Modyfikowanie projektu systemu Android

Teraz, gdy zmodyfikowano zaplecza aplikacji, aby wysłać tylko *identyfikator* powiadomienia wypychanego, musisz zmienić aplikację systemu Android do obsługi tego powiadomienia i oddzwonić do zaplecza, aby pobrać bezpieczną wiadomość, która ma być wyświetlana.
Aby osiągnąć ten cel, należy upewnić się, że aplikacja na Androida wie, jak uwierzytelnić się za pomocą zaplecza po otrzymaniu powiadomień wypychanych.

Teraz zmodyfikuj przepływ *logowania,* aby zapisać wartość nagłówka uwierzytelniania w udostępnionych preferencjach aplikacji. Analogiczne mechanizmy mogą służyć do przechowywania dowolnego tokenu uwierzytelniania (na przykład tokeny OAuth), który aplikacja ma do użycia bez konieczności poświadczeń użytkownika.

1. W projekcie aplikacji na Androida dodaj następujące stałe `MainActivity` u góry klasy:

    ```java
    public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
    public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
    ```
2. Nadal w `MainActivity` klasie, `getAuthorizationHeader()` zaktualizować metodę, aby zawierać następujący kod:

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
3. W górnej `import` części pliku dodaj `MainActivity` następujące instrukcje:

    ```java
    import android.content.SharedPreferences;
    ```

Teraz zmień program obsługi, który jest wywoływany po odebraniu powiadomienia.

1. W `MyHandler` klasie zmień `OnReceive()` metodę, która ma zawierać:

    ```java
    public void onReceive(Context context, Bundle bundle) {
        ctx = context;
        String secureMessageId = bundle.getString("secureId");
        retrieveNotification(secureMessageId);
    }
    ```
2. Następnie dodaj `retrieveNotification()` metodę, zastępując `{back-end endpoint}` symbol zastępczy punktem końcowym zaplecza uzyskanym podczas wdrażania zaplecza:

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

Ta metoda wywołuje zaplecze aplikacji, aby pobrać zawartość powiadomień przy użyciu poświadczeń przechowywanych w preferencjach udostępnionych i wyświetla je jako normalne powiadomienie. Powiadomienie wygląda na użytkownika aplikacji dokładnie tak, jak każde inne powiadomienie wypychowe.

Zaleca się obsługiwanie przypadków braku właściwości nagłówka uwierzytelniania lub odrzucenia przez zaplecze. Specyficzne postępowanie tych przypadków zależy głównie od środowiska użytkownika docelowego. Jedną z opcji jest wyświetlenie powiadomienia z ogólnym monitem dla użytkownika do uwierzytelniania, aby pobrać rzeczywiste powiadomienie.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aby uruchomić aplikację, wykonaj następujące czynności:

1. Upewnij **się, że aplikacja AppBackend** została wdrożona na platformie Azure. Jeśli używasz programu Visual Studio, uruchom aplikację **AppBackend** Web API. Zostanie wyświetlona ASP.NET strona internetowa.
2. W aplikacji Eclipse uruchom aplikację na fizycznym urządzeniu z systemem Android lub emulatorze.
3. W interfejsie użytkownika aplikacji systemu Android wprowadź nazwę użytkownika i hasło. Mogą to być dowolny ciąg, ale muszą być tej samej wartości.
4. W interfejsie użytkownika aplikacji na androida kliknij pozycję **Zaloguj się**. Następnie kliknij przycisk **Wyślij wypychanie**.
