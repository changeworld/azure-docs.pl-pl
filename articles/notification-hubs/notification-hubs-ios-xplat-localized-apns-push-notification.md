---
title: Zlokalizowane powiadomień wypychanych do urządzeń z systemem iOS przy użyciu usługi Azure Notification Hubs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać powiadomień wypychanych zlokalizowane do urządzeń z systemem iOS przy użyciu usługi Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: b3d74086ee233da50138aff00d8da78aa0243a75
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-push-localized-notifications-to-ios-devices-using-azure-notification-hubs"></a>Samouczek: Zlokalizowanych powiadomień wypychanych do urządzeń z systemem iOS przy użyciu usługi Azure Notification Hubs 
> [!div class="op_single_selector"]
> * [Sklep Windows C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)
> 

Ten samouczek przedstawia sposób użycia [szablony](notification-hubs-templates-cross-platform-push-messages.md) funkcji usługi Azure Notification Hubs wysyłać powiadomienia o najważniejszych wiadomościach które zostały zlokalizowane według języka i urządzenia. W tym samouczku początkowo utworzony w aplikacji systemu iOS [użyciu usługi Notification Hubs wysyłanie najważniejszych wiadomości]. Po zakończeniu kategorie, które chcesz zarejestrować, określ język, w których chcesz otrzymywać powiadomienia i otrzymywać tylko powiadomień wypychanych do wybranych kategorii w tym języku.

Istnieją dwie części tego scenariusza:

* Aplikacja systemu iOS umożliwia klienta urządzeń do określania języka i subskrybować podziału różne kategorie nowości;
* Zaplecza emituje powiadomień, przy użyciu **tag** i **szablonu** funkcje usługi Azure Notification Hubs.

W tym samouczku można wykonać następujące czynności:

> [!div class="checklist"]
> * Zaktualizuj interfejs użytkownika aplikacji
> * Tworzenie aplikacji dla systemu iOS
> * Wysyłanie powiadomień zlokalizowanego szablonu z aplikacji konsoli .NET
> * Wysyłanie powiadomień zlokalizowanego szablonu z urządzenia


## <a name="overview"></a>Przegląd
W [użyciu usługi Notification Hubs wysyłanie najważniejszych wiadomości], utworzono aplikację, która jest używana **tagi** do subskrybowania powiadomień dla wiadomości różnych kategorii. Wiele aplikacji, jednak docelowy kilku rynkach i wymagają lokalizacji. Oznacza, że zawartość powiadomień się muszą być lokalizowany i dostarczane do odpowiednich zestawów urządzeń. Ten samouczek przedstawia sposób użycia **szablonu** funkcji usługi Notification Hubs łatwe udostępnianie powiadomienia o zlokalizowanych najważniejszych wiadomościach.

> [!NOTE]
> Jedną z metod wysyłania powiadomień zlokalizowanych jest tworzenie wielu wersji każdego znacznika. Na przykład do obsługi w języku angielskim, francuskim i mandaryński, będzie potrzebny trzy różne znaczniki dla wiadomości world: "world_en", "world_fr" i "world_ch". Następnie trzeba wysłać zlokalizowanej wersji wiadomości world do każdego z tych tagów. W tym temacie umożliwiają szablony uniknąć mnożenie tagów i wymaganie wysyła wiele komunikatów.

Na wysokim poziomie szablony są sposób, aby określić, jak powinno zostać odebrane powiadomienie określonego urządzenia. Szablon określa format ładunku dokładne odwołując się do właściwości, które są częścią komunikat wysłany przez zaplecze Twojej aplikacji. W Twoim przypadku należy wysłać komunikat niezależny od ustawień regionalnych zawierający wszystkie obsługiwane języki:

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Upewnij się, zarejestruj szablon, który odwołuje się do właściwości poprawne przez urządzenia. Na przykład aplikacja systemu iOS, która potrzebuje zarejestrować dla wiadomości francuskim rejestruje przy użyciu następującej składni:

    {
        aps:{
            alert: "$(News_French)"
        }
    }

Aby uzyskać więcej informacji dotyczących szablonów, zobacz [szablony](notification-hubs-templates-cross-platform-push-messages.md) artykułu.

## <a name="prerequisites"></a>Wymagania wstępne

- Zakończenie [powiadomień wypychanych do urządzeń z systemem iOS określonych](notification-hubs-ios-xplat-segmented-apns-push-notification.md) samouczka i mieć kod dostępne, ponieważ w tym samouczku bezpośrednio oparta na kod.
- Visual Studio 2012 lub nowszy jest opcjonalna.

## <a name="update-the-app-user-interface"></a>Zaktualizuj interfejs użytkownika aplikacji
W tej sekcji możesz zmodyfikować aplikację fundamentalne wiadomości, utworzonego w temacie [użyciu usługi Notification Hubs wysyłanie najważniejszych wiadomości] wysłać zlokalizowane fundamentalne wiadomości za pomocą szablonów.

W Twojej MainStoryboard_iPhone.storyboard dodać kontrolkę segmentu z trzech językach: angielski, francuski i mandaryński.

![][13]

Następnie upewnij się dodać IBOutlet w Twojej ViewController.h, jak pokazano na poniższej ilustracji:

![][14]

## <a name="build-the-ios-app"></a>Tworzenie aplikacji dla systemu iOS
1. W Twojej Notification.h dodać *retrieveLocale* metody i zmodyfikować magazynu oraz subskrybowania metody, jak pokazano w poniższym kodzie:
   
    ```obj-c
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;
   
        - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;
   
        - (NSSet*) retrieveCategories;
   
        - (int) retrieveLocale;
   
    ```
    W Twojej Notification.m zmodyfikować *storeCategoriesAndSubscribe* metody, dodając parametr ustawień regionalnych i przechowywanie ich w domyślnych ustawień użytkownika:
   
    ```obj-c
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
            [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
            [defaults synchronize];
   
            [self subscribeWithLocale: locale categories:categories completion:completion];
        }
    ````
    Następnie zmodyfikuj *subskrypcji* metodę w celu uwzględnienia ustawień regionalnych:
   
    ```obj-c
        - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];
   
            NSString* localeString;
            switch (locale) {
                case 0:
                    localeString = @"English";
                    break;
                case 1:
                    localeString = @"French";
                    break;
                case 2:
                    localeString = @"Mandarin";
                    break;
            }
   
            NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];
   
            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
        }
       ```
    You use the method *registerTemplateWithDeviceToken*, instead of *registerNativeWithDeviceToken*. When you register for a template, you have to provide the json template and also a name for the template (as the app might want to register different templates). Make sure to register your categories as tags, as you want to make sure to receive the notifciations for those news.
   
    Add a method to retrieve the locale from the user default settings:
   
    ```obj-c
        - (int) retrieveLocale {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
   
            int locale = [defaults integerForKey:@"BreakingNewsLocale"];
   
            return locale < 0?0:locale;
        }
    ```
2. Teraz, gdy zmodyfikowane klasy powiadomień, masz upewnij się, że ViewController korzysta z nowych UISegmentControl. Dodaj następujący wiersz w *viewDidLoad* metody, aby upewnić się wyświetlić aktualnie wybranego ustawienia regionalne:
   
    ```obj-c
        self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
     ```  
    Następnie w Twojej *subskrypcji* metody, zmień wywołania do *storeCategoriesAndSubscribe* poniższy kod:
   
    ```obj-c
        [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
            if (!error) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      @"Subscribed!" delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];
    ```
3. Na koniec należy zaktualizować *didRegisterForRemoteNotificationsWithDeviceToken* metody w Twojej AppDelegate.m, dzięki czemu można poprawnie odświeżyć rejestracji podczas uruchamiania aplikacji. Zmień wywołania do *subskrypcji* metody powiadomienia z następującym kodem:
   
    ```obj-c
        NSSet* categories = [self.notifications retrieveCategories];
        int locale = [self.notifications retrieveLocale];
        [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>(opcjonalnie) Wysyłanie powiadomień zlokalizowanego szablonu z aplikacji konsoli .NET
[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>(opcjonalnie) Wysyłanie powiadomień zlokalizowanego szablonu z urządzenia
Jeśli nie masz dostępu do programu Visual Studio lub po prostu chcesz przetestować wysyłanie powiadomień zlokalizowanego szablonu bezpośrednio z aplikacji na urządzeniu. Można dodać parametry szablonu zlokalizowanych `SendNotificationRESTAPI` metody zdefiniowanej w poprzednich instrukcji.

    ```obj-c
        - (void)SendNotificationRESTAPI:(NSString*)categoryTag
        {
            NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                     defaultSessionConfiguration] delegate:nil delegateQueue:nil];

            NSString *json;

            // Construct the messages REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                               HUBNAME, API_VERSION]];

            // Generated the token to be used in the authorization header.
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the template notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];

            // Add the category as a tag
            [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

            // Template notification
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
                    \"News_English\":\"Breaking %@ News in English : %@\","
                    \"News_French\":\"Breaking %@ News in French : %@\","
                    \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
                    categoryTag, self.notificationMessage.text,
                    categoryTag, self.notificationMessage.text,  // insert English localized news here
                    categoryTag, self.notificationMessage.text,  // insert French localized news here
                    categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

            // Signify template notification format
            [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            // JSON Content-Type
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                       completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
               {
               NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                   if (error || httpResponse.statusCode != 200)
                   {
                       NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                   }
                   if (data != NULL)
                   {
                       //xmlParser = [[NSXMLParser alloc] initWithData:data];
                       //[xmlParser setDelegate:self];
                       //[xmlParser parse];
                   }
               }];

            [dataTask resume];
        }
    ```


## <a name="next-steps"></a>Kolejne kroki
W tym samouczku powiadomienia zlokalizowanych jest wysyłane do urządzenia z systemem iOS. Aby dowiedzieć się, jak powiadomienia wypychane do określonych użytkowników aplikacji dla systemu iOS, przejdź do samouczka następujące: 

> [!div class="nextstepaction"]
>[Powiadomienia wypychane do określonych użytkowników](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)

<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[użyciu usługi Notification Hubs wysyłanie najważniejszych wiadomości]: /manage/services/notification-hubs/breaking-news-ios
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notify users with Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md

[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
