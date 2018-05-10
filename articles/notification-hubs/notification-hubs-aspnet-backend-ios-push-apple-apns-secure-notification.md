---
title: Azure Notification Hubs bezpiecznego Push
description: Dowiedz się, jak wysyłać powiadomienia wypychane bezpieczny do aplikacji systemu iOS z platformy Azure. Przykłady kodu napisane w języku Objective C i C#.
documentationcenter: ios
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
ms.assetid: 17d42b0a-2c80-4e35-a1ed-ed510d19f4b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/25/2018
ms.author: dimazaid
ms.openlocfilehash: d3ba967a164a35af5bf66f7e74d5f95b5dc2a37f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="azure-notification-hubs-secure-push"></a>Azure Notification Hubs bezpiecznego Push
> [!div class="op_single_selector"]
> * [Aplikacje uniwersalne systemu Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)
> 
> 

## <a name="overview"></a>Przegląd
Obsługa powiadomień wypychanych w Microsoft Azure pozwala uzyskiwać dostęp do infrastruktury wypychania łatwy w użyciu, wieloplatformową skalowalnych w poziomie, co znacznie upraszcza implementacji powiadomienia wypychane dla aplikacji zarówno konsumenckie i korporacyjne dla platform urządzeń przenośnych.

Z powodu przepisami ograniczeń dotyczących zabezpieczeń, czasami aplikacji może mają zostać uwzględnione coś w powiadomienie, które nie są przesyłane za pośrednictwem infrastruktury powiadomień wypychanych standardowa. Ten przewodnik opisuje sposób do osiągnięcia w tym samym środowisku, wysyłając informacje poufne za pośrednictwem bezpiecznego uwierzytelnionego połączenia od urządzeń klienckich i zaplecza aplikacji.

Na wysokim poziomie przepływ wygląda następująco:

1. Zaplecza aplikacji:
   * Magazyny bezpiecznego ładunku w wewnętrznej bazie danych.
   * Wysyła identyfikator tego powiadomienia do urządzenia (nie informacji o są wysyłane).
2. Aplikacją na urządzeniu, podczas odbierania powiadomienia:
   * Urządzenie kontaktuje się z zaplecza żąda bezpiecznego ładunku.
   * Aplikację można wyświetlić ładunku jako powiadomienie na urządzeniu.

Należy pamiętać, że w poprzednim przepływu (i w tym samouczku) przyjęto założenie, że urządzenia są przechowywane token uwierzytelniania w magazynie lokalnym, po zalogowaniu się użytkownika. Gwarantuje to bezproblemowe, jak urządzenia mogą pobierać ładunku bezpiecznego powiadomienia za pomocą tego tokenu. Jeśli aplikacja nie przechowuje tokeny uwierzytelniania na urządzeniu lub tokeny te mogą wygasnąć, aplikacji urządzenia, po otrzymaniu powiadomienia powinien być wyświetlany ogólny powiadomienie monitowania użytkownika do uruchomienia aplikacji. Następnie aplikacja uwierzytelnia użytkownika i zawiera ładunek powiadomienia.

W tym samouczku Secure wypychania pokazano, jak bezpiecznie wysyłać powiadomienia wypychane. Samouczek opiera się na [Powiadom użytkowników](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) samouczku, dlatego należy wykonać kroki tego samouczka najpierw.

> [!NOTE]
> Ten samouczek zakłada, że utworzony i skonfigurowany Centrum powiadomień, zgodnie z opisem w [wprowadzenie do korzystania z usługi Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Zmodyfikuj projekt dla systemu iOS
Teraz, aby modyfikować Twojej aplikacji zaplecza wysłać tylko *identyfikator* powiadomienia, należy zmienić aplikacji systemu iOS w celu obsługi tego powiadomienia i wywołania zwrotnego z zaplecza można pobrać zabezpieczoną wiadomość, który będzie wyświetlany.

Na osiągnięcie tego celu, musimy pisanie logiki można pobrać zawartości bezpiecznej z zaplecza aplikacji.

1. W **AppDelegate.m**, upewnij się, że rejestrów aplikacji dyskretnej powiadomień, przetwarza identyfikator powiadomień wysyłanych z wewnętrznej bazy danych. Dodaj **UIRemoteNotificationTypeNewsstandContentAvailability** opcji w didFinishLaunchingWithOptions:
   
        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
2. W Twojej **AppDelegate.m** Dodaj sekcji implementacji u góry z deklaracją następujące:
   
        @interface AppDelegate ()
        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        @end
3. Następnie dodaj w sekcji implementacji następujący kod, zastępując symbol zastępczy `{back-end endpoint}` z poziomu zaplecza uzyskany wcześniej punktu końcowego:

```
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

    This method calls your app back-end to retrieve the notification content using the credentials stored in the shared preferences.

1. Teraz musimy obsługi przychodzących powiadomień i pobrać zawartość do wyświetlenia przy użyciu metody powyżej. Najpierw musimy włączyć uruchomione w tle podczas odbierania powiadomień wypychanych w aplikacji systemu iOS. W **XCode**, wybierz projekt aplikacji w lewym panelu, a następnie kliknij urządzenie docelowe głównej aplikacji w **cele** sekcji w okienku centralnym.
2. Następnie kliknij przycisk z **możliwości** w górnej części okienka centralnej i sprawdź **zdalnego powiadomienia** wyboru.
   
    ![][IOS1]
3. W **AppDelegate.m** Dodaj następującą metodę do obsługi powiadomień wypychanych:
   
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
   
    Należy pamiętać, że preferowane do obsługi przypadków brakujących właściwości nagłówka uwierzytelniania lub odrzucenia przez zaplecza. Obsługę określonych przypadkach zależy od większości użytkowników docelowych. Jedną z opcji jest powiadomienia z monitem ogólnego dla użytkownika do uwierzytelniania można pobrać rzeczywiste powiadomienia mają być wyświetlane.

## <a name="run-the-application"></a>Uruchamianie aplikacji
Aby uruchomić aplikację, wykonaj następujące czynności:

1. W programie XCode Uruchom aplikację na urządzenie fizyczne z systemem iOS (wypychanie powiadomień nie będzie działać w symulatorze).
2. W aplikacji systemu iOS interfejsu użytkownika wprowadź nazwę użytkownika i hasło. Mogą to być dowolny ciąg, ale muszą one mieć taką samą wartość.
3. W aplikacji systemu iOS interfejsu użytkownika, kliknij przycisk **Zaloguj**. Następnie kliknij przycisk **wysyłania wypychania**. Powinny pojawić się bezpiecznego powiadomienia są wyświetlane w Centrum powiadomień.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
