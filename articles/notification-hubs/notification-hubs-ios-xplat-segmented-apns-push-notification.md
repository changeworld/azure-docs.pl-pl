---
title: Powiadomienia wypychane określonych urządzeń z systemem iOS przy użyciu usługi Azure Notification Hubs | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak wysyłać powiadomienia wypychane do urządzeń z systemem iOS w określonym za pomocą usługi Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 6ead4169-deff-4947-858c-8c6cf03cc3b2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 6db7862a115179552a2dd57c07af66b3b5aa10e3
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2018
ms.locfileid: "42055403"
---
# <a name="tutorial-push-notifications-to-specific-ios-devices-using-azure-notification-hubs"></a>Samouczek: Wypychanie powiadomień określonych urządzeń z systemem iOS przy użyciu usługi Azure Notification Hubs
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Przegląd
Ten samouczek pokazuje, jak rozgłaszać powiadomienia o najważniejszych informacjach do aplikacji iOS przy użyciu usługi Azure Notification Hubs. Po zakończeniu będą mogli zarejestrować informacje na temat przełomowych kategorii wiadomości, który Cię interesuje i otrzymywać tylko powiadomienia wypychane dla tych kategorii. Ten scenariusz jest typowym wzorcem dla wielu aplikacji wymagających wysyłania powiadomień do grup użytkowników, które wcześniej zadeklarowały zainteresowanie nimi, na przykład czytników danych RSS, aplikacji dla fanów muzyki itp.

Scenariusze rozgłaszania są włączone w przypadku co najmniej jednego *tagu* podczas tworzenia rejestracji w centrum powiadomień. Gdy powiadomienia są wysyłane do znacznika, urządzenia, które zostały zarejestrowane dla tagu otrzymać powiadomienia. Tagi to po prostu ciągi, dlatego nie muszą być aprowizowane z wyprzedzeniem. Aby uzyskać więcej informacji na temat tagów, zobacz [Notification Hubs Routing and Tag Expressions (Wyrażenia routingu i tagów w usłudze Notification Hubs)](notification-hubs-tags-segment-push-message.md).

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Dodaj wybór kategorii do aplikacji
> * Wysyłanie powiadomień z tagami
> * Wysyłanie powiadomień z urządzenia
> * Uruchamianie aplikacji i generowanie powiadomień

## <a name="prerequisites"></a>Wymagania wstępne
Ten temat opiera się na aplikacji utworzonej w [samouczek: powiadomienia wypychane do aplikacji dla systemu iOS przy użyciu usługi Azure Notification Hubs][get-started]. Przed rozpoczęciem tego samouczka należy zostały już wykonane [samouczek: powiadomienia wypychane do aplikacji dla systemu iOS przy użyciu usługi Azure Notification Hubs][get-started].

## <a name="add-category-selection-to-the-app"></a>Dodawanie wyboru kategorii do aplikacji
Pierwszym krokiem jest, aby dodać elementy interfejsu użytkownika do Twojego istniejącego scenorysu, który umożliwia użytkownikowi wybranie kategorii, aby zarejestrować. Kategorie wybrane przez użytkownika są przechowywane na urządzeniu. Po uruchomieniu aplikacji w centrum powiadomień zostanie utworzona rejestracja urządzenia z wybranymi kategoriami w formie tagów.

1. W swojej MainStoryboard_iPhone.storyboard Dodaj następujące składniki z biblioteki obiektów:
   
   * Etykieta z tekstem "Istotne wiadomości"
   * Etykiety z teksty kategorii "World", "Polityka", "Business", "Technologia", "Nauka", "Sport"
   * Sześć przełączników, po jednym dla każdej kategorii, ustaw każdy przełącznik **stanu** jako **poza** domyślnie.
   * Przycisk jedną etykietę "Subscribe"
     
     Scenorysu powinien wyglądać w następujący sposób:
     
     ![][3]
2. W edytorze Asystenta ustawień, należy utworzyć gniazda dla wszystkich przełączników i ich wywoływania "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"
3. Utwórz akcję dla przycisku o nazwie **subskrybowania**. Twoje ViewController.h powinien zawierać następujący kod:
   
    ```obj-c
        @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;
   
        - (IBAction)subscribe:(id)sender;
    ```
4. Utwórz nową **Cocoa Touch klasy** o nazwie `Notifications`. Skopiuj poniższy kod w sekcji interfejs pliku Notifications.h:
   
    ```obj-c
        @property NSData* deviceToken;
   
        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;
   
        - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                    completion:(void (^)(NSError* error))completion;
   
        - (NSSet*)retrieveCategories;
   
        - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
    ```
5. Dodaj następujące dyrektywy importu do Notifications.m:
   
    ```obj-c
        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    ```
6. Skopiuj poniższy kod w sekcji implementacji Notifications.m pliku.
   
    ```obj-c
        SBNotificationHub* hub;
   
        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{
   
            hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
                                        notificationHubPath:hubName];
   
            return self;
        }
   
        - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
   
            [self subscribeWithCategories:categories completion:completion];
        }

        - (NSSet*)retrieveCategories {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

            NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

            if (!categories) return [[NSSet alloc] init];
            return [[NSSet alloc] initWithArray:categories];
        }

        - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
        {
           //[hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];

            NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
                jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
        }
    ```

    Ta klasa używa lokalnego magazynu do przechowywania i pobierania kategorii wiadomości, który odbiera to urządzenie. Ponadto zawiera metodę, aby zarejestrować się w tych kategoriach, za pomocą [szablonu](notification-hubs-templates-cross-platform-push-messages.md) rejestracji.

1. W pliku AppDelegate.h Dodaj instrukcję import Notifications.h i Dodaj właściwość do wystąpienia klasy powiadomienia:
   
    ```obj-c
        #import "Notifications.h"
   
        @property (nonatomic) Notifications* notifications;
    ```
2. W **didFinishLaunchingWithOptions** metody w AppDelegate.m, Dodaj kod do inicjowania wystąpienia powiadomienia na początku metody.  
   
    `HUBNAME` i `HUBLISTENACCESS` (zdefiniowane w pliku hubinfo.h) powinno mieć już `<hub name>` i `<connection string with listen access>` symbole zastępcze zastąpić nazwę Centrum powiadomień i parametry połączenia dla *DefaultListenSharedAccessSignature* uzyskany wcześniej
   
    ```obj-c
        self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
    ```
   
   > [!NOTE]
   > Ponieważ poświadczenia dystrybuowane przy użyciu aplikacji klienckiej nie są zazwyczaj bezpieczne, należy przy użyciu aplikacji klienckiej dystrybuować wyłącznie klucz dostępu do nasłuchiwania. Dostęp do nasłuchiwania umożliwia aplikacji rejestrowanie powiadomień, ale nie może ona modyfikować istniejących rejestracji ani wysyłać powiadomień. Klucz pełnego dostępu jest używany w zabezpieczonej usłudze zaplecza do wysyłania powiadomień oraz zmiany istniejących rejestracji.
   > 
   > 
3. W **didRegisterForRemoteNotificationsWithDeviceToken** metody w AppDelegate.m, Zastąp kod w metodzie następujący kod, aby przekazać token urządzenia do klasy powiadomienia. Klasa powiadomień wykonuje rejestrowanie na potrzeby powiadomień za pomocą kategorii. Jeśli użytkownik zmieni wybór kategorii, należy wywołać `subscribeWithCategories` metody w odpowiedzi na **subskrybowania** przycisk, aby je zaktualizować.
   
   > [!NOTE]
   > Ponieważ token urządzenia przypisane przez firmy Apple Push Notification Service (APNS) szansy można w dowolnym momencie, należy zarejestrować się często uniknąć niepowodzeń powiadomień powiadomienia. Poniższy przykład przeprowadza rejestrację w celu otrzymywania powiadomień za każdym razem, gdy aplikacja jest uruchamiana. W przypadku często uruchamianych aplikacji — więcej niż raz dziennie — prawdopodobnie możesz pominąć rejestrację, aby zachować przepustowość, jeśli od poprzedniej rejestracji upłynął czas krótszy niż jeden dzień.
   > 
   > 
   
    ```obj-c
        self.notifications.deviceToken = deviceToken;
   
        // Retrieves the categories from local storage and requests a registration for these categories
        // each time the app starts and performs a registration.
   
        NSSet* categories = [self.notifications retrieveCategories];
        [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];
    ```

    W tym momencie powinna być nie innym kodem **didRegisterForRemoteNotificationsWithDeviceToken** metody.

1. Następujące metody powinna już istnieć w AppDelegate.m ukończenie [Rozpoczynanie pracy z usługą Notification Hubs] [ get-started] samouczka. Jeśli nie, należy je dodać.
   
    ```obj-c    
    -(void)MessageBox:(NSString *)title message:(NSString *)messageText
    {
   
        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
        [alert show];
    }
   
   * (void)application:(UIApplication *)application didReceiveRemoteNotification:
       (NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
     }
    ```
   
   Ta metoda obsługuje powiadomienia otrzymane, gdy aplikacja jest uruchomiona, wyświetlając prostą **UIAlert**.
2. W ViewController.m, Dodaj instrukcję import AppDelegate.h i skopiuj następujący kod do wygenerowanych XCode **subskrybowania** metody. Ten kod aktualizuje rejestracji powiadomień do użycia nowe tagi kategorii, którą użytkownik wybrał w interfejsie użytkownika.
   
    ```obj-c
       #import "Notifications.h"
   
       NSMutableArray* categories = [[NSMutableArray alloc] init];
   
       if (self.WorldSwitch.isOn) [categories addObject:@"World"];
       if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
       if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
       if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
       if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
       if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];
   
       Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];
   
       [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
           if (!error) {
               [(AppDelegate*)[[UIApplication sharedApplication]delegate] MessageBox:@"Notification" message:@"Subscribed!"];
           } else {
               NSLog(@"Error subscribing: %@", error);
           }
       }];
    ```

   Ta metoda tworzy **NSMutableArray** kategorii i używa **powiadomienia** klasy do przechowywania listy w Magazyn lokalny i rejestry, odpowiednie tagi w Centrum powiadomień. Jeśli kategorie zostaną zmienione, rejestracja zostanie ponownie utworzona przy użyciu nowych kategorii.
3. W ViewController.m, Dodaj następujący kod w **viewDidLoad** metodę, aby ustawić interfejsu użytkownika na podstawie wcześniej zapisany kategorii.

    ```obj-c    
        // This updates the UI on startup based on the status of previously saved categories.

        Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        NSSet* categories = [notifications retrieveCategories];

        if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
        if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
        if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
        if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
        if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
        if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;
    ```


Można teraz sklepu zestaw kategorii w magazynie lokalnym urządzenia używane do rejestrowania w Centrum powiadomień przy każdym uruchomieniu aplikacji. Użytkownik może zmienić wybór kategorii na środowisko uruchomieniowe i kliknij przycisk **subskrybowania** metodę, aby zaktualizować rejestracji dla urządzenia. Następnie należy zaktualizować aplikację, aby wysłać powiadomienia o najważniejszych bezpośrednio w samej aplikacji.

## <a name="optional-send-tagged-notifications"></a>(opcjonalnie) Wysyłanie powiadomień oznakowane
Jeśli nie masz dostępu do programu Visual Studio, możesz od razu przejść do następnej sekcji i wysyłania powiadomień z poziomu samej aplikacji. Możesz również wysłać powiadomienie prawidłowego szablonu z [Azure Portal] za pomocą karty debugowanie w Centrum powiadomień. 

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>(opcjonalnie) Wysyłanie powiadomień z urządzenia
Zwykle powiadomienia będą wysyłane za pomocą usługi zaplecza, ale możesz wysłać powiadomienia o najważniejszych informacjach bezpośrednio z aplikacji. Aby to zrobić, należy zaktualizować `SendNotificationRESTAPI` metody, które zostały zdefiniowane w [Rozpoczynanie pracy z usługą Notification Hubs] [ get-started] samouczka.

1. W `ViewController.m`, zaktualizuj `SendNotificationRESTAPI` metodę jako zgodna, który akceptuje parametr dla tagu kategorii i wysyła prawidłowe [szablonu](notification-hubs-templates-cross-platform-push-messages.md) powiadomień.
   
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
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\"}",
                    categoryTag, self.notificationMessage.text];
   
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
2. W `ViewController.m`, zaktualizuj **Wyślij powiadomienie E-mail** akcji, jak pokazano w kodzie, który następuje po. Który wysyła powiadomienia przy użyciu każdego znacznika indywidualnie i wysyła na wiele platform.

    ```obj-c
        - (IBAction)SendNotificationMessage:(id)sender
        {
            self.sendResults.text = @"";

            NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                    @"Technology", @"Science", @"Sports", nil];

            // Lets send the message as breaking news for each category to WNS, GCM, and APNS
            // using a template.
            for(NSString* category in categories)
            {
                [self SendNotificationRESTAPI:category];
            }
        }
    ```


1. Ponownie skompiluj projekt i upewnij się, że żadne błędy kompilacji.

## <a name="run-the-app-and-generate-notifications"></a>Uruchamianie aplikacji i generowanie powiadomień
1. Naciśnij przycisk Uruchom, aby skompilować projekt i uruchomić aplikację. Wybierz niektóre opcje wiadomości podziału, aby zasubskrybować, a następnie naciśnij klawisz **Subskrybuj** przycisku. Powinieneś zobaczyć okno dialogowe wskazujący, że powiadomienia zostały subskrybowany.
   
    ![][1]
   
    Po wybraniu **Subskrybuj**, konwertuje wybranych kategorii do tagów i żąda nowej rejestracji urządzenia wybranych tagów z Centrum powiadomień aplikacji.
2. Wprowadź komunikat do wysłania jako wiadomości naciśnij **Wyślij powiadomienie E-mail** przycisku. Alternatywnie można uruchomić aplikacji konsoli .NET w celu generowania powiadomień.
   
    ![][2]
3. Każde urządzenie, które subskrybuje wiadomości z ostatniej chwili otrzymuje powiadomienia o najważniejszych wysłane.

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku wysłano wyemitowane powiadomienia do określonych urządzeń z systemem iOS zostały zarejestrowane dla kategorii. Aby dowiedzieć się, jak i zlokalizowanych powiadomień wypychanych, przejdź do następującego samouczka: 

> [!div class="nextstepaction"]
>[Wypychanie powiadomień zlokalizowanych](notification-hubs-ios-xplat-localized-apns-push-notification.md)


<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png








<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[get-started]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Azure Portal]: https://portal.azure.com
