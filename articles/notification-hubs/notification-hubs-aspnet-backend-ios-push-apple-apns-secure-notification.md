---
title: Azure Notification Hubs bezpieczne wypychanie
description: Dowiedz się, jak wysyłać powiadomienia wypychane bezpiecznych aplikacji systemu iOS na platformie Azure. Przykłady kodu napisane w językach Objective-C i C#.
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
ms.assetid: 17d42b0a-2c80-4e35-a1ed-ed510d19f4b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: d88bdb1eaeb95413df84bf69ed4fc763b6d4901f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61458510"
---
# <a name="azure-notification-hubs-secure-push"></a>Azure Notification Hubs bezpieczne wypychanie

> [!div class="op_single_selector"]
> * [Aplikacje uniwersalne systemu Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Omówienie

Obsługę powiadomień wypychanych w systemie Microsoft Azure umożliwia dostęp do infrastruktury wypychania łatwy w użyciu, wieloplatformową i skalowanych w poziomie, który znacznie upraszcza implementację powiadomienia wypychane dla aplikacji firmowych i komercyjnych dla urządzeń przenośnych platform.

Ze względu na przepisami ograniczenia zabezpieczeń, czasami aplikacja może mają zostać uwzględnione coś powiadomienia, które nie mogą być przekazywane za pośrednictwem infrastruktury powiadomień wypychanych standardowych. W tym samouczku opisano sposób osiągnięcia tego samego środowiska poprzez wysłanie informacji poufnych przez bezpieczne, uwierzytelnione połączenie między urządzeniem klienckim i zapleczem aplikacji.

Na wysokim poziomie przepływ jest następująca:

1. Zaplecze aplikacji:
   * Ładunek bezpiecznego magazynów w wewnętrznej bazie danych.
   * Wysyła identyfikator to powiadomienie do urządzenia (wysyłane nie informacje zabezpieczeń).
2. Aplikacja na urządzeniu, podczas odbierania powiadomienia:
   * Urządzenie kontaktuje się z zaplecza bezpiecznego ładunku żądania.
   * Aplikację można wyświetlić ładunku powiadomienie na urządzenie.

Należy zauważyć, że w poprzedniej przepływu (i w ramach tego samouczka) przyjęto założenie, że urządzenia są przechowywane token uwierzytelniania w magazynie lokalnym, po użytkownik loguje. Gwarantuje to nie zakłóca pracy, jak urządzenia mogą pobierać ładunku bezpieczne powiadomień, używające tego tokenu. Jeśli aplikacja nie przechowuje tokeny uwierzytelniania na urządzeniu lub tokeny te mogą wygasnąć, aplikacji urządzenia, po otrzymaniu zawiadomienia powinien być wyświetlany ogólny powiadomienie monitowania użytkownika do uruchomienia aplikacji. Aplikacja następnie uwierzytelnia użytkownika i zawiera ładunek powiadomienia.

W tym samouczku Secure wypychania pokazuje, jak bezpiecznie wysyłać powiadomienia wypychane. Samouczek opiera się na [powiadamianie użytkowników](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) samouczek, dlatego należy wykonać kroki opisane w tym samouczku najpierw.

> [!NOTE]
> W tym samouczku przyjęto założenie, że utworzone i skonfigurowane Centrum powiadomień, zgodnie z opisem w [wprowadzenie do usługi Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Modyfikowanie projektu systemu iOS

Teraz, gdy został zmodyfikowany z poziomu aplikacji zaplecza do wysyłania tylko *identyfikator* powiadomienia, należy zmienić aplikację systemu iOS do obsługi powiadomienia i wywołania zwrotnego zaplecza do pobrania zabezpieczoną wiadomość, która ma być wyświetlany.

Aby osiągnąć ten cel, mamy pisanie logiki można pobrać zawartości bezpiecznej z zaplecza aplikacji.

1. W `AppDelegate.m`, upewnij się, rejestruje aplikacji dyskretnej powiadomienia, więc są przetwarzane identyfikator powiadomienia wysyłane z wewnętrznej bazy danych. Dodaj `UIRemoteNotificationTypeNewsstandContentAvailability` opcji didFinishLaunchingWithOptions:

    ```objc
    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    ```
2. W swojej `AppDelegate.m` Dodaj sekcji implementacji u góry z następującą deklarację:

    ```objc
    @interface AppDelegate ()
    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    @end
    ```
3. Następnie dodaj w sekcji implementacji następującym kodem, zastępując symbol zastępczy `{back-end endpoint}` z punktem końcowym dla zaplecza uzyskany wcześniej:

    ```objc
    NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    {
        // check if authenticated
        ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
        NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
        if (!authenticationHeader) return;

        NSURLSession* session = [NSURLSession
                                    sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                    delegate:nil
                                    delegateQueue:nil];

        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"GET"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200)
            {
                NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                completion([json objectForKey:@"Payload"], nil);
            }
            else
            {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(nil, error);
                else {
                    completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }
    ```

    Ta metoda wywołuje Twojej aplikacji zaplecza do pobierania zawartości powiadomień przy użyciu poświadczeń przechowywanych w udostępnionych preferencji.

4. Teraz mamy do obsługi przychodzących powiadomień i użyj powyższej metody, aby pobrać zawartość do wyświetlenia. Najpierw musimy włączyć aplikacji systemu iOS do uruchamiania w tle podczas odbierania powiadomień wypychanych. W **XCode**, wybierz swój projekt aplikacji w lewym panelu, a następnie kliknij urządzenie docelowe głównej aplikacji w **cele** sekcji w okienku centralnym.
5. Następnie kliknij przycisk z **możliwości** u góry okienka centralną, a następnie sprawdź **zdalne powiadomienia** pola wyboru.

    ![][IOS1]

6. W `AppDelegate.m` Dodaj następującą metodę do obsługi powiadomień wypychanych:

    ```objc
    -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
    {
        NSLog(@"%@", userInfo);

        [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
            if (!error) {
                // show local notification
                UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                localNotification.alertBody = payload;
                localNotification.timeZone = [NSTimeZone defaultTimeZone];
                [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                completionHandler(UIBackgroundFetchResultNewData);
            } else {
                completionHandler(UIBackgroundFetchResultFailed);
            }
        }];

    }
    ```

    Należy pamiętać, że korzystniejsze sposób obsługiwać przypadki, które brakuje właściwości nagłówka uwierzytelniania lub odrzucenie przez serwer zaplecza. Określone obsługi tych przypadkach zależy od przede wszystkim środowiska użytkownika docelowego. Jedną z opcji jest powiadomienia wraz z monitem o ogólnych w celu uwierzytelnienia użytkownika można pobrać rzeczywiste powiadomień.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aby uruchomić aplikację, wykonaj następujące czynności:

1. W programie XCode Uruchom aplikację na urządzenie fizyczne z systemem iOS (wypychane, powiadomienia nie będzie działać w symulatorze).
2. W aplikacji dla systemu iOS interfejsu użytkownika wprowadź nazwę użytkownika i hasło. Mogą to być dowolny ciąg, ale muszą one mieć taką samą wartość.
3. W aplikacji dla systemu iOS interfejsu użytkownika, kliknij przycisk **Zaloguj**. Następnie kliknij przycisk **wysyłania wypychanych**. Powinien zostać wyświetlony bezpiecznego powiadomienia są wyświetlane w Centrum powiadomień.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
