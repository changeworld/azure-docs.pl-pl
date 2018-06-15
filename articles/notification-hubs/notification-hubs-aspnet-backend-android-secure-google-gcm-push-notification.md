---
title: Wysyłanie powiadomień wypychanych bezpiecznego przy użyciu usługi Azure Notification Hubs
description: Dowiedz się, jak wysyłać powiadomienia wypychane bezpieczny do aplikacji systemu Android na platformie Azure. Przykłady kodu napisane w języku Java i C#.
documentationcenter: android
keywords: Wypychanie powiadomień, powiadomienia wypychane push wiadomości, powiadomień wypychanych systemu android
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
ms.assetid: daf3de1c-f6a9-43c4-8165-a76bfaa70893
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 04/25/2018
ms.author: dimazaid
ms.openlocfilehash: 58f6967c59a5060baa10ff83752b9c6ed08226cb
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776764"
---
# <a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Wysyłanie powiadomień wypychanych bezpiecznego przy użyciu usługi Azure Notification Hubs
> [!div class="op_single_selector"]
> * [Aplikacje uniwersalne systemu Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)
> 
> 

## <a name="overview"></a>Przegląd
> [!IMPORTANT]
> Do wykonania kroków tego samouczka potrzebne jest aktywne konto platformy Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).
> 
> 

Obsługa powiadomień wypychanych w Microsoft Azure pozwala uzyskać dostęp, łatwy w użyciu multiplatform, infrastruktury wiadomości wypychanych skalowanej, co znacznie upraszcza implementację powiadomień wypychanych zarówno konsumenckie i korporacyjne aplikacji dla systemu platformy urządzeń przenośnych.

Z powodu przepisami ograniczeń dotyczących zabezpieczeń, czasami aplikacji może mają zostać uwzględnione coś w powiadomienie, które nie są przesyłane za pośrednictwem infrastruktury powiadomień wypychanych standardowa. Ten przewodnik opisuje sposób do osiągnięcia w tym samym środowisku, wysyłając informacje poufne za pośrednictwem uwierzytelnionego bezpiecznego połączenia między urządzeniem z systemem Android klienta i zaplecza aplikacji.

Na wysokim poziomie przepływ wygląda następująco:

1. Zaplecza aplikacji:
   * Magazyny bezpiecznego ładunku w wewnętrznej bazie danych.
   * Wysyła identyfikator tego powiadomienia na urządzeniu z systemem Android (nie informacji o są wysyłane).
2. Aplikacją na urządzeniu, podczas odbierania powiadomienia:
   * Urządzenie z systemem Android kontaktuje się z zaplecza żąda bezpiecznego ładunku.
   * Aplikację można wyświetlić ładunku jako powiadomienie na urządzeniu.

Należy pamiętać, że w poprzednim przepływu (i w tym samouczku), zakłada się, że urządzenia są przechowywane token uwierzytelniania w magazynie lokalnym, po zalogowaniu się użytkownika. Takie podejście gwarantuje bezproblemowe, jak urządzenia mogą pobierać ładunku bezpiecznego powiadomienia za pomocą tego tokenu. Jeśli aplikacja nie przechowuje tokeny uwierzytelniania na urządzeniu lub tokeny te mogą wygasnąć, aplikacji urządzenia, po otrzymaniu powiadomienia wypychane powinien być wyświetlany ogólny powiadomienie monitowania użytkownika do uruchomienia aplikacji. Następnie aplikacja uwierzytelnia użytkownika i zawiera ładunek powiadomienia.

Ten samouczek pokazuje, jak wysyłać powiadomienia wypychane bezpieczne. Opiera się na [Powiadom użytkowników](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) samouczku, dlatego należy wykonać kroki opisane w tym samouczku najpierw Jeśli jeszcze.

> [!NOTE]
> Ten samouczek zakłada, że utworzony i skonfigurowany Centrum powiadomień, zgodnie z opisem w [wprowadzenie do korzystania z usługi Notification Hubs (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Modyfikowanie projektu dla systemu Android
Teraz, aby modyfikować Twojej aplikacji zaplecza wysłać tylko *identyfikator* powiadomienia wypychane, należy zmienić aplikacji systemu Android w celu obsługi tego powiadomienia i wywołania zwrotnego z zaplecza można pobrać zabezpieczoną wiadomość, który będzie wyświetlany.
Na osiągnięcie tego celu, należy upewnić się, że aplikacji systemu Android potrafi do samodzielnego uwierzytelnienia z poziomu zaplecza, gdy odbiera powiadomienia wypychane.

Teraz, zmodyfikować *logowania* przepływu, aby zapisać wartości nagłówka uwierzytelniania w udostępnionych preferencji aplikacji. Mechanizmy analogiczne może służyć do przechowywania wszelkich token uwierzytelniania (na przykład tokenów OAuth), która aplikacja ma używać bez konieczności wprowadzania poświadczeń użytkownika.

1. W projekcie aplikacji systemu Android, Dodaj następujące ograniczenia w górnej części **MainActivity** klasy:
   
        public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
        public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
2. Nadal **MainActivity** klasy, zaktualizuj `getAuthorizationHeader()` metoda zawiera następujący kod:
   
        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
   
            SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();
   
            return basicAuthHeader;
        }
3. Dodaj następujące `import` instrukcje w górnej części **MainActivity** pliku:
   
        import android.content.SharedPreferences;

Teraz Zmień obsługi, które jest wywoływane w przypadku otrzymania powiadomienia.

1. W **MyHandler** klasy zmiany `OnReceive()` metoda zawiera:
   
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String secureMessageId = bundle.getString("secureId");
            retrieveNotification(secureMessageId);
        }
2. Następnie dodaj `retrieveNotification()` metoda zastępuje symbol zastępczy `{back-end endpoint}` z punktem końcowym zaplecza uzyskane podczas wdrażania sieci wewnętrznej:
   
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

Ta metoda wywołuje Twojej aplikacji zaplecza do pobierania zawartości powiadomienia przy użyciu poświadczeń przechowywanych w udostępnionych preferencji i wyświetla je jako normalne powiadomienie. Wygląda powiadomienie dla użytkownika aplikacji, dokładnie tak samo jak inne powiadomień wypychanych.

Zaleca się do obsługi przypadków brakujących właściwości nagłówka uwierzytelniania lub odrzucenia przez zaplecza. Obsługę określonych przypadkach zależy przede wszystkim użytkowników docelowych. Jedną z opcji jest powiadomienia z monitem ogólnego dla użytkownika do uwierzytelniania można pobrać rzeczywiste powiadomienia mają być wyświetlane.

## <a name="run-the-application"></a>Uruchamianie aplikacji
Aby uruchomić aplikację, wykonaj następujące czynności:

1. Upewnij się, że **AppBackend** jest wdrożona na platformie Azure. Jeśli używasz programu Visual Studio, uruchom **AppBackend** aplikacji interfejsu API sieci Web. Zostanie wyświetlona strona sieci web ASP.NET.
2. W środowisku Eclipse Uruchom aplikację na fizyczne urządzenie z systemem Android lub emulator.
3. W aplikacji systemu Android interfejsu użytkownika wprowadź nazwę użytkownika i hasło. Mogą to być dowolny ciąg, ale muszą one mieć taką samą wartość.
4. W aplikacji systemu Android interfejsu użytkownika, kliknij przycisk **Zaloguj**. Następnie kliknij przycisk **wysyłania wypychania**.

