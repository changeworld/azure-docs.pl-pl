---
title: Wysyłanie zlokalizowanych powiadomień wypychanych do systemu iOS przy użyciu usługi Azure Notification Hubs | Dokumenty firmy Microsoft
description: Dowiedz się, jak używać powiadomień zlokalizowanymi wypychaniem na urządzeniach z systemem iOS przy użyciu usługi Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: a8614156be5d516d16aff698b604cf0e661d7311
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72385656"
---
# <a name="tutorial-send-localized-push-notifications-to-ios-using-azure-notification-hubs"></a>Samouczek: Wysyłanie zlokalizowanych powiadomień wypychanych do systemu iOS przy użyciu usługi Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Sklep Windows C #](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

W tym samouczku pokazano, jak używać funkcji [szablonów](notification-hubs-templates-cross-platform-push-messages.md) usługi Azure Notification Hubs do emisji powiadomień o aktualnościach, które zostały zlokalizowane według języka i urządzenia. W tym samouczku możesz zacząć od aplikacji na iOS utworzonej w [Centrum powiadomień do wysyłania najświeższych wiadomości.] Po zakończeniu możesz zarejestrować się w kategoriach, które Cię interesują, określić język, w którym mają być wyświetlane powiadomienia, i otrzymywać tylko powiadomienia wypychane dla wybranych kategorii w tym języku.

Istnieją dwie części do tego scenariusza:

* Aplikacja na iOS umożliwia urządzeniom klienckim określenie języka i subskrybowanie różnych kategorii wiadomości;
* Zaplecze emituje powiadomienia, przy użyciu **tagu** **i** szablonu funkcji usługi Azure Notification Hubs.

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Aktualizowanie interfejsu użytkownika aplikacji
> * Tworzenie aplikacji na iOS
> * Wysyłanie zlokalizowanych powiadomień o szablonach z aplikacji konsoli .NET
> * Wysyłanie zlokalizowanych powiadomień o szablonach z urządzenia

## <a name="overview"></a>Omówienie

W [centrum powiadomień do wysyłania najświeższych wiadomości]powstała aplikacja, która używała **tagów** do subskrybowania powiadomień dla różnych kategorii wiadomości. Wiele aplikacji jest jednak kierowanych na wiele rynków i wymaga lokalizacji. Oznacza to, że treść samych powiadomień musi być zlokalizowana i dostarczona do właściwego zestawu urządzeń. W tym samouczku pokazano, jak używać funkcji **szablonu** centrum powiadomień do łatwego dostarczania zlokalizowanych powiadomień o nowościach.

> [!NOTE]
> Jednym ze sposobów wysyłania zlokalizowanych powiadomień jest utworzenie wielu wersji każdego tagu. Na przykład, aby wspierać angielski, francuski i mandaryński, potrzebujesz trzech różnych tagów dla wiadomości ze świata: "world_en", "world_fr" i "world_ch". Następnie musisz wysłać zlokalizowaną wersję wiadomości ze świata do każdego z tych tagów. W tym temacie należy użyć szablonów, aby uniknąć rozprzestrzeniania tagów i wymagania dotyczące wysyłania wielu wiadomości.

Szablony to sposób na określenie, w jaki sposób określone urządzenie powinno otrzymywać powiadomienie. Szablon określa dokładny format ładunku, odwołując się do właściwości stanowiących część komunikatu wysyłanego przez zaplecze aplikacji. W twoim przypadku wysyłasz wiadomość niezależną od ustawień regionalnych zawierającą wszystkie obsługiwane języki:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Następnie upewnij się, że urządzenia rejestrują się przy szablonie, który odwołuje się do poprawnej właściwości. Na przykład aplikacja dla systemu iOS, która chce zarejestrować się dla francuskich rejestrów wiadomości przy użyciu następującej składni:

```json
{
    aps: {
        alert: "$(News_French)"
    }
}
```

Aby uzyskać więcej informacji na temat szablonów, zobacz [artykuł Szablony.](notification-hubs-templates-cross-platform-push-messages.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Wypełnij [powiadomienia wypychanych do określonych urządzeń z systemem iOS](notification-hubs-ios-xplat-segmented-apns-push-notification.md) samouczek i mieć kod dostępny, ponieważ ten samouczek tworzy bezpośrednio na tym kodzie.
* Visual Studio 2019 jest opcjonalne.

## <a name="update-the-app-user-interface"></a>Aktualizowanie interfejsu użytkownika aplikacji

W tej sekcji zmodyfikujesz aplikację Breaking News, która została utworzona w temacie [Użyj centrum powiadomień do wysyłania wiadomości o aktualnościach] w celu wysyłania zlokalizowanych wiadomości o aktualnościach przy użyciu szablonów.

W `MainStoryboard_iPhone.storyboard`programach dodano formę podzieloną na segmenty w trzech językach: angielskim, francuskim i mandaryńskim.

![Tworzenie scenorysu interfejsu użytkownika systemu iOS][13]

Następnie należy dodać IBOutlet w ViewController.h, jak pokazano na poniższej ilustracji:

![Tworzenie gniazd dla przełączników][14]

## <a name="build-the-ios-app"></a>Tworzenie aplikacji na iOS

1. W `Notification.h`swoim , `retrieveLocale` dodaj metodę i zmodyfikuj metody sklepu i subskrybowania, jak pokazano w poniższym kodzie:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

    - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

    - (NSSet*) retrieveCategories;

    - (int) retrieveLocale;
    ```
    W `Notification.m`, `storeCategoriesAndSubscribe` zmodyfikuj `locale` metodę, dodając parametr i przechowując go w ustawieniach domyślnych użytkownika:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
        [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
        [defaults synchronize];

        [self subscribeWithLocale: locale categories:categories completion:completion];
    }
    ```

    Następnie zmodyfikuj metodę *subskrybowania,* aby uwzględnić ustawienia regionalne:

    ```objc
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

    Metoda jest `registerTemplateWithDeviceToken`używana zamiast `registerNativeWithDeviceToken`. Podczas rejestracji szablonu, należy podać szablon json, a także nazwę szablonu (jak aplikacja może chcieć zarejestrować różne szablony). Pamiętaj, aby zarejestrować kategorie jako tagi, ponieważ chcesz otrzymywać powiadomienia o tych wiadomościach.

    Dodaj metodę pobierania ustawień regionalnych z ustawień domyślnych użytkownika:

    ```objc
    - (int) retrieveLocale {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        int locale = [defaults integerForKey:@"BreakingNewsLocale"];

        return locale < 0?0:locale;
    }
    ```

2. Teraz, gdy zmodyfikowano `Notifications` klasę, musisz upewnić `ViewController` się, że `UISegmentControl`korzysta z nowego . Dodaj następujący wiersz `viewDidLoad` w metodzie, aby upewnić się, że wyświetlane są aktualnie wybrane ustawienia regionalne:

    ```objc
    self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
    ```

    Następnie w `subscribe` metodzie zmień wywołanie `storeCategoriesAndSubscribe` do następującego kodu:

    ```objc
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

3. Na koniec należy zaktualizować `didRegisterForRemoteNotificationsWithDeviceToken` metodę w AppDelegate.m, dzięki czemu można poprawnie odświeżyć rejestrację po uruchomieniu aplikacji. Zmień wywołanie `subscribe` metody powiadomień za pomocą następującego kodu:

    ```obj-c
    NSSet* categories = [self.notifications retrieveCategories];
    int locale = [self.notifications retrieveLocale];
    [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>(opcjonalnie) Wysyłanie zlokalizowanych powiadomień o szablonach z aplikacji konsoli .NET

[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>(opcjonalnie) Wysyłanie zlokalizowanych powiadomień o szablonach z urządzenia

Jeśli nie masz dostępu do programu Visual Studio lub chcesz po prostu przetestować wysyłanie zlokalizowanych powiadomień szablonu bezpośrednio z aplikacji na urządzeniu. Można dodać zlokalizowane parametry szablonu do metody zdefiniowanej `SendNotificationRESTAPI` w poprzednim samouczku.

```objc
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

## <a name="next-steps"></a>Następne kroki

W tym samouczku wysłano zlokalizowane powiadomienia do urządzeń z systemem iOS. Aby dowiedzieć się, jak wypychać powiadomienia do określonych użytkowników aplikacji na iOS, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Wypychanie powiadomień do konkretnych użytkowników](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)

<!-- Images. -->
[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: https://msdn.microsoft.com/library/jj927168.aspx
[Wysyłanie najważniejszych wiadomości przy użyciu usługi Notification Hubs]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notify users with Notification Hubs: Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md
[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: https://msdn.microsoft.com/library/jj927168.aspx
