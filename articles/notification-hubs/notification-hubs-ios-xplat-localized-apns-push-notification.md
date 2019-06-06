---
title: Zlokalizowanych powiadomień wypychanych do urządzeń z systemem iOS przy użyciu usługi Azure Notification Hubs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać powiadomień wypychanych, zlokalizowane do urządzeń z systemem iOS przy użyciu usługi Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: a293f0b656c075ae3b21ccf98e602e43ed761958
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428461"
---
# <a name="tutorial-push-localized-notifications-to-ios-devices-using-azure-notification-hubs"></a>Samouczek: Zlokalizowanych powiadomień wypychanych do urządzeń z systemem iOS przy użyciu usługi Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Sklep Windows — C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

W tym samouczku dowiesz się, jak używać [szablony](notification-hubs-templates-cross-platform-push-messages.md) funkcji usługi Azure Notification Hubs wysyłać powiadomienia o najważniejszych informacjach, które zostały zlokalizowane według języka i urządzenia. W tym samouczku zaczniesz od aplikacji dla systemu iOS utworzone w [Wysyłanie najważniejszych wiadomości przy użyciu usługi Notification Hubs]. Po zakończeniu rejestrowania dla kategorii, które interesują Cię, określ język, w której chcesz otrzymywać powiadomienia i otrzymywać tylko powiadomienia wypychane dla wybranych kategorii, w tym języku.

Istnieją dwie części do tego scenariusza:

* Aplikacja systemu iOS umożliwia klienta urządzenia, aby określić język, a do subskrybowania podziału różnych kategorii wiadomości;
* Serwer zaplecza emituje powiadomienia, za pomocą **tag** i **szablonu** funkcje usługi Azure Notification Hubs.

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Aktualizowanie interfejsu użytkownika aplikacji
> * Tworzenie aplikacji dla systemu iOS
> * Wysyłanie powiadomień zlokalizowanych szablonu z aplikacji konsoli .NET
> * Wysyłanie powiadomień zlokalizowanych szablonu z urządzenia

## <a name="overview"></a>Omówienie

W [Wysyłanie najważniejszych wiadomości przy użyciu usługi Notification Hubs], utworzoną aplikację, która jest używana **tagi** do subskrybowania powiadomień dla różnych grup dyskusyjnych kategorii. Wiele aplikacji, jednak docelowe różne rynki i wymagają lokalizacji. Oznacza to, że zawartość powiadomień samodzielnie muszą być zlokalizowane i dostarczane do odpowiednich zestawów urządzeń. W tym samouczku dowiesz się, jak używać **szablonu** funkcji usługi Notification hubs pozwalające łatwo dostarczać powiadomienia o najważniejszych zlokalizowane.

> [!NOTE]
> Jednym ze sposobów na wysyłanie powiadomień zlokalizowanych jest utworzenie wielu wersji każdego znacznika. Na przykład, aby zapewnić obsługę angielski, francuski i mandaryński, będziesz potrzebować trzech różnych znaczników dla wiadomości ze świata: "world_en", "world_fr" i "world_ch". Następnie trzeba wysłać zlokalizowaną wersję wiadomości ze świata do każdego z tych znaczników. W tym temacie umożliwia szablony uniknąć rozprzestrzenianie tagów i konieczności wysyłania wielu wiadomości.

Szablony są sposób określania, jak określonego urządzenia powinien otrzymywać powiadomienie o. Szablon określa dokładny format ładunku, odwołując się do właściwości stanowiących część komunikatu wysyłanego przez zaplecze aplikacji. W Twoim przypadku należy wysłać wiadomość niezależne od ustawień regionalnych, zawierającą wszystkie obsługiwane języki:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Następnie upewnij się, że urządzenia zarejestrować się przy użyciu szablonu, który odwołuje się do odpowiedniej właściwości. Na przykład aplikację dla systemu iOS, które chcą zarejestrować francuska wiadomości rejestruje przy użyciu następującej składni:

```json
{
    aps: {
        alert: "$(News_French)"
    }
}
```

Aby uzyskać więcej informacji dotyczących szablonów, zobacz [szablony](notification-hubs-templates-cross-platform-push-messages.md) artykułu.

## <a name="prerequisites"></a>Wymagania wstępne

* Wykonaj [powiadomienia wypychane do urządzeń z systemem iOS w określonych](notification-hubs-ios-xplat-segmented-apns-push-notification.md) samouczek i mieć kod jest dostępny, ponieważ ten samouczek opiera się bezpośrednio na kod.
* Visual Studio 2019 r jest opcjonalne.

## <a name="update-the-app-user-interface"></a>Aktualizowanie interfejsu użytkownika aplikacji

W tej sekcji zmodyfikujesz aplikacji istotne wiadomości, który został utworzony w temacie [Wysyłanie najważniejszych wiadomości przy użyciu usługi Notification Hubs] wysyłać zlokalizowany przy użyciu szablonów wiadomości z ostatniej chwili.

W swojej `MainStoryboard_iPhone.storyboard`, dodawanie kontrolki Segmentowane z trzech językach: Angielski, francuski i mandaryński.

![Tworzenie scenorysu interfejsu użytkownika dla systemu iOS][13]

Następnie upewnij się dodać IBOutlet w swojej ViewController.h, jak pokazano na poniższej ilustracji:

![Tworzenie gniazda dla przełączników][14]

## <a name="build-the-ios-app"></a>Tworzenie aplikacji dla systemu iOS

1. W swojej `Notification.h`, Dodaj `retrieveLocale` metody i modyfikowanie magazynu i subskrybowanie metody, jak pokazano w poniższym kodzie:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

    - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

    - (NSSet*) retrieveCategories;

    - (int) retrieveLocale;
    ```
    W swojej `Notification.m`, zmodyfikuj `storeCategoriesAndSubscribe` metody, dodając `locale` parametru i przechowywaniu jej w ustawieniach domyślnych użytkownika:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
        [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
        [defaults synchronize];

        [self subscribeWithLocale: locale categories:categories completion:completion];
    }
    ```

    Następnie zmodyfikuj *subskrybowania* metodę, aby uwzględnić ustawienia regionalne:

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

    Używana jest metoda `registerTemplateWithDeviceToken`, zamiast `registerNativeWithDeviceToken`. Podczas rejestrowania dla szablonu, należy podać szablon json, a także nazwę dla szablonu (jak aplikacja może być do rejestrowania różnych szablonów). Pamiętaj zarejestrować kategorie jako tagi, jak chcesz upewnić się otrzymywać powiadomienia o tych wiadomości.

    Dodaj metodę, aby pobrać ustawienia regionalne w ustawieniach domyślnych użytkownika:

    ```objc
    - (int) retrieveLocale {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        int locale = [defaults integerForKey:@"BreakingNewsLocale"];

        return locale < 0?0:locale;
    }
    ```

2. Teraz, gdy zmodyfikowano `Notifications` klasy, należy upewnić się, że `ViewController` sprawia, że użycie nowej `UISegmentControl`. Dodaj następujący wiersz w `viewDidLoad` metody, aby upewnić się wyświetlić ustawienia regionalne, który jest aktualnie wybrany:

    ```objc
    self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
    ```

    Następnie w swojej `subscribe` metodę, zmień wywołania do `storeCategoriesAndSubscribe` z następującym kodem:

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

3. Na koniec należy zaktualizować `didRegisterForRemoteNotificationsWithDeviceToken` metody w swojej AppDelegate.m tak, aby poprawnie można odświeżyć rejestrację, podczas uruchamiania aplikacji. Zmień wywołania do `subscribe` metodę powiadomienia z następującym kodem:

    ```obj-c
    NSSet* categories = [self.notifications retrieveCategories];
    int locale = [self.notifications retrieveLocale];
    [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>(opcjonalnie) Wysyłanie powiadomień zlokalizowanych szablonu z aplikacji konsoli .NET

[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>(opcjonalnie) Wysyłanie powiadomień zlokalizowanych szablonu z urządzenia

Jeśli nie ma dostępu do programu Visual Studio lub po prostu chcesz przetestować, wysyłanie powiadomień zlokalizowanych szablonu bezpośrednio z aplikacji na urządzeniu. Można dodać parametry szablonu zlokalizowane `SendNotificationRESTAPI` metody zdefiniowanej w poprzednim samouczku.

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

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku powiadomień zlokalizowanych jest wysyłane do urządzenia z systemem iOS. Aby dowiedzieć się, jak wysyłać powiadomienia wypychane do określonych użytkowników aplikacji dla systemu iOS, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Wysyłanie powiadomień push do konkretnych użytkowników](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)

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
