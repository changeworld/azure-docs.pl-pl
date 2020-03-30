---
title: Usługi Azure Notification Hubs Secure Push dla systemu iOS
description: Dowiedz się, jak wysyłać bezpieczne powiadomienia wypychane do aplikacji na iOS z platformy Azure. Przykłady kodu napisane w językach Objective-C i C#.
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: 17d42b0a-2c80-4e35-a1ed-ed510d19f4b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 96d1dd514f6fb9c11d7194714337583d6b4387cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530752"
---
# <a name="azure-notification-hubs-secure-push"></a>Bezpieczne wypychanie w centrach powiadomień platformy Azure

> [!div class="op_single_selector"]
> * [Aplikacje uniwersalne systemu Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Omówienie

Obsługa powiadomień wypychanych na platformie Microsoft Azure umożliwia dostęp do łatwej w użyciu, wieloplatformowej, skalowej w poziomie infrastruktury push, co znacznie upraszcza wdrażanie powiadomień wypychanych zarówno dla aplikacji konsumenckich, jak i korporacyjnych dla urządzeń przenośnych Platformy.

Ze względu na ograniczenia regulacyjne lub zabezpieczeń czasami aplikacja może chcieć dołączyć coś w powiadomieniu, które nie mogą być przesyłane za pośrednictwem standardowej infrastruktury powiadomień wypychanych. W tym samouczku opisano sposób osiągnięcia tego samego środowiska, wysyłając poufne informacje za pośrednictwem bezpiecznego, uwierzytelnionego połączenia między urządzeniem klienckim a zapleczem aplikacji.

Na wysokim poziomie przepływ jest następujący:

1. Back-end aplikacji:
   * Przechowuje bezpieczne ładunku w bazie danych zaplecza.
   * Wysyła identyfikator tego powiadomienia do urządzenia (nie są wysyłane żadne bezpieczne informacje).
2. Aplikacja na urządzeniu, po otrzymaniu powiadomienia:
   * Urządzenie styka się z zapleczem, żądając bezpiecznego ładunku.
   * Aplikacja może wyświetlić ładunek jako powiadomienie na urządzeniu.

Należy pamiętać, że w poprzednim przepływie (i w tym samouczku) zakładamy, że urządzenie przechowuje token uwierzytelniania w magazynie lokalnym, po zalogowaniu się użytkownika. Gwarantuje to bezproblemowe środowisko, ponieważ urządzenie może pobrać bezpieczny ładunek powiadomienia przy użyciu tego tokenu. Jeśli aplikacja nie przechowuje tokenów uwierzytelniania na urządzeniu lub jeśli te tokeny mogą wygasnąć, aplikacja urządzenia po otrzymaniu powiadomienia powinna wyświetlić ogólne powiadomienie z monitem o uruchomienie aplikacji. Następnie aplikacja uwierzytelnia użytkownika i wyświetla ładunek powiadomień.

Ten samouczek Bezpiecznego wypychania pokazuje, jak bezpiecznie wysłać powiadomienie wypychanego. Samouczek opiera się na podziale [Powiadom użytkowników](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) samouczka, więc należy wykonać kroki w tym samouczku pierwszy.

> [!NOTE]
> W tym samouczku przyjęto założenie, że centrum powiadomień zostało utworzone i skonfigurowane zgodnie z opisem w [artykule Wprowadzenie do centrów powiadomień (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Modyfikowanie projektu systemu iOS

Teraz, gdy zmodyfikowano zaplecza aplikacji, aby wysłać tylko *identyfikator* powiadomienia, należy zmienić aplikację systemu iOS do obsługi tego powiadomienia i oddzwonić zaplecza, aby pobrać bezpieczną wiadomość, która ma być wyświetlana.

Aby osiągnąć ten cel, musimy napisać logikę, aby pobrać bezpieczną zawartość z zaplecza aplikacji.

1. W `AppDelegate.m`obszarze , upewnij się, że aplikacja rejestruje się dla powiadomień dyskretnych, aby przetwarzała identyfikator powiadomień wysłany z wewnętrznej bazy danych. Dodaj `UIRemoteNotificationTypeNewsstandContentAvailability` opcję w didFinishLaunchingWithOptions:

    ```objc
    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    ```
2. W `AppDelegate.m` sekcji dodaj implementacji u góry z następującą deklaracją:

    ```objc
    @interface AppDelegate ()
    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    @end
    ```
3. Następnie dodaj w sekcji implementacji następujący kod, `{back-end endpoint}` zastępując symbol zastępczy punktem końcowym dla zaplecza uzyskanego wcześniej:

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

    Ta metoda wywołuje zaplecze aplikacji, aby pobrać zawartość powiadomień przy użyciu poświadczeń przechowywanych w preferencjach udostępnionych.

4. Teraz musimy obsłużyć przychodzące powiadomienie i użyć powyższej metody, aby pobrać zawartość do wyświetlenia. Najpierw musimy włączyć aplikację na iOS, aby działać w tle podczas odbierania powiadomienia wypychanego. W **programie XCode**wybierz projekt aplikacji w lewym panelu, a następnie kliknij główny obiekt docelowy aplikacji w sekcji **Obiekty docelowe** z centralnego okienka.
5. Następnie kliknij kartę **Możliwości** u góry centralnego okienka i zaznacz pole wyboru **Powiadomienia zdalne.**

    ![][IOS1]

6. W `AppDelegate.m` dodaj następującą metodę obsługi powiadomień wypychanych:

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

    Należy zauważyć, że zaleca się obsługiwanie przypadków braku właściwości nagłówka uwierzytelniania lub odrzucenia przez zaplecze. Specyficzne postępowanie w tych przypadkach zależy głównie od środowiska użytkownika docelowego. Jedną z opcji jest wyświetlenie powiadomienia z ogólnym monitem dla użytkownika do uwierzytelniania, aby pobrać rzeczywiste powiadomienie.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aby uruchomić aplikację, wykonaj następujące czynności:

1. W xcode uruchom aplikację na fizycznym urządzeniu z systemem iOS (powiadomienia wypychania nie będą działać w symulatorze).
2. W interfejsie użytkownika aplikacji dla systemu iOS wprowadź nazwę użytkownika i hasło. Mogą to być dowolny ciąg, ale muszą być tej samej wartości.
3. W interfejsie użytkownika aplikacji dla systemu iOS kliknij pozycję **Zaloguj się**. Następnie kliknij przycisk **Wyślij wypychanie**. Bezpieczne powiadomienie powinno być wyświetlane w Centrum powiadomień.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
