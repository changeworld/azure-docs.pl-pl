---
title: Wysyłanie powiadomień wypychanych do określonych urządzeń z systemem iOS przy użyciu usługi Azure Notification Hubs | Dokumenty firmy Microsoft
description: W tym samouczku dowiesz się, jak używać usługi Azure Notification Hubs do wysyłania powiadomień wypychanych do określonych urządzeń z systemem iOS.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6ead4169-deff-4947-858c-8c6cf03cc3b2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 11/07/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: a775963f1b0fa19cd687c839f527f4a078c76864
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80126990"
---
# <a name="tutorial-send-push-notifications-to-specific-ios-devices-using-azure-notification-hubs"></a>Samouczek: Wysyłanie powiadomień wypychanych do określonych urządzeń z systemem iOS przy użyciu usługi Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Omówienie

W tym samouczku pokazano, jak używać usługi Azure Notification Hubs do emisji powiadomień o nowościach do aplikacji dla systemu iOS. Po zakończeniu możesz zarejestrować się w kategoriach wiadomości, które Cię interesują, i otrzymywać tylko powiadomienia push dla tych kategorii. Ten scenariusz jest typowym wzorcem dla wielu aplikacji wymagających wysyłania powiadomień do grup użytkowników, które wcześniej zadeklarowały zainteresowanie nimi, na przykład czytników danych RSS, aplikacji dla fanów muzyki itp.

Scenariusze rozgłaszania są włączone w przypadku co najmniej jednego *tagu* podczas tworzenia rejestracji w centrum powiadomień. Gdy powiadomienia są wysyłane do tagu, urządzenia, które zostały zarejestrowane dla tagu, otrzymają powiadomienie. Tagi to po prostu ciągi, dlatego nie muszą być aprowizowane z wyprzedzeniem. Aby uzyskać więcej informacji na temat tagów, zobacz [Notification Hubs Routing and Tag Expressions (Wyrażenia routingu i tagów w usłudze Notification Hubs)](notification-hubs-tags-segment-push-message.md).

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Dodawanie zaznaczenia kategorii do aplikacji
> * Wysyłanie powiadomień z tagami
> * Wysyłanie powiadomień z urządzenia
> * Uruchamianie aplikacji i generowanie powiadomień

## <a name="prerequisites"></a>Wymagania wstępne

Ten temat opiera się na aplikacji utworzonej w [samouczku: Powiadomienia wypychania do aplikacji systemu iOS przy użyciu usługi Azure Notification Hubs][get-started]. Przed rozpoczęciem tego samouczka musisz już ukończyć [samouczek: Powiadomienia wypychania do aplikacji systemu iOS przy użyciu usługi Azure Notification Hubs][get-started].

## <a name="add-category-selection-to-the-app"></a>Dodawanie wyboru kategorii do aplikacji

Pierwszym krokiem jest dodanie elementów interfejsu użytkownika do istniejącego scenorysu, które umożliwiają użytkownikowi wybranie kategorii do zarejestrowania. Kategorie wybrane przez użytkownika są przechowywane na urządzeniu. Po uruchomieniu aplikacji w centrum powiadomień zostanie utworzona rejestracja urządzenia z wybranymi kategoriami w formie tagów.

1. W **MainStoryboard_iPhone.storyboard** dodaj następujące składniki z biblioteki obiektów:

   * Etykieta z tekstem "Breaking News",
   * Etykiety z tekstami kategorii "Świat", "Polityka", "Biznes", "Technologia", "Nauka", "Sport",
   * Sześć przełączników, po jednym na kategorię, domyślnie ustawia każde **stan** przełącznika **jako wyłączone.**
   * Jeden przycisk z etykietą "Subskrybuj"

     Scenorys powinien wyglądać następująco:

     ![Konstruktor interfejsów Xcode][3]

2. W edytorze asystenta, tworzyć gniazda dla wszystkich przełączników i nazwać je "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"

3. Utwórz akcję dla `subscribe`przycisku o nazwie ; twój `ViewController.h` powinien zawierać następujący kod:

    ```objc
    @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

    - (IBAction)subscribe:(id)sender;
    ```

4. Utwórz nową **klasę Cocoa Touch** o nazwie `Notifications`. Skopiuj następujący kod w sekcji interfejsu pliku Notifications.h:

    ```objc
    @property NSData* deviceToken;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

    - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                completion:(void (^)(NSError* error))completion;

    - (NSSet*)retrieveCategories;

    - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
    ```

5. Dodaj następującą dyrektywę importu do notifications.m:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    ```

6. Skopiuj poniższy kod w sekcji implementacji pliku Notifications.m.

    ```objc
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

    Ta klasa używa magazynu lokalnego do przechowywania i pobierania kategorii wiadomości, które odbiera to urządzenie. Ponadto zawiera metodę rejestrowania dla tych kategorii przy użyciu rejestracji [szablonu.](notification-hubs-templates-cross-platform-push-messages.md)

7. W `AppDelegate.h` pliku dodaj instrukcję `Notifications.h` importu i dodaj właściwość `Notifications` dla wystąpienia klasy:

    ```objc
    #import "Notifications.h"

    @property (nonatomic) Notifications* notifications;
    ```

8. W `didFinishLaunchingWithOptions` metodzie `AppDelegate.m`w , dodaj kod, aby zainicjować wystąpienie powiadomień na początku metody.  
    `HUBNAME`i `HUBLISTENACCESS` `hubinfo.h`(zdefiniowane w) `<hub name>` powinny `<connection string with listen access>` mieć już i symbole zastępcze zastąpione nazwą centrum powiadomień i parametry połączenia *defaultlistenSharedAccessSignature,* które zostały uzyskane wcześniej

    ```objc
    self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
    ```

    > [!NOTE]
    > Ponieważ poświadczenia dystrybuowane przy użyciu aplikacji klienckiej nie są zazwyczaj bezpieczne, należy przy użyciu aplikacji klienckiej dystrybuować wyłącznie klucz dostępu do nasłuchiwania. Dostęp do nasłuchiwania umożliwia aplikacji rejestrowanie powiadomień, ale nie może ona modyfikować istniejących rejestracji ani wysyłać powiadomień. Klucz pełnego dostępu jest używany w zabezpieczonej usłudze zaplecza do wysyłania powiadomień oraz zmiany istniejących rejestracji.

9. W `didRegisterForRemoteNotificationsWithDeviceToken` metodzie `AppDelegate.m`w , zastąp kod w metodzie następującym kodem, aby przekazać token urządzenia do `notifications` klasy. Klasa `notifications` wykonuje rejestrowanie powiadomień z kategoriami. Jeśli użytkownik zmieni wybór kategorii, `subscribeWithCategories` wywołanie metody w odpowiedzi na przycisk **subskrybuj,** aby je zaktualizować.

    > [!NOTE]
    > Ponieważ token urządzenia przypisany przez usługę powiadomień wypychanych Apple (APNS) może ulec zmianie w dowolnym momencie, należy często rejestrować się w celu otrzymywania powiadomień, aby uniknąć błędów powiadomień. Poniższy przykład przeprowadza rejestrację w celu otrzymywania powiadomień za każdym razem, gdy aplikacja jest uruchamiana. W przypadku często uruchamianych aplikacji — więcej niż raz dziennie — prawdopodobnie możesz pominąć rejestrację, aby zachować przepustowość, jeśli od poprzedniej rejestracji upłynął czas krótszy niż jeden dzień.

    ```objc
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

    W tym momencie nie powinno być `didRegisterForRemoteNotificationsWithDeviceToken` żadnego innego kodu w metodzie.

10. Następujące metody powinny być `AppDelegate.m` już obecne w od ukończenia [wprowadzenie do powiadomień hubs][get-started] samouczek. Jeśli nie, dodaj je.

    ```objc
    - (void)MessageBox:(NSString *)title message:(NSString *)messageText
    {

        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
        [alert show];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:
       (NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
     }
    ```

    Ta metoda obsługuje powiadomienia odebrane, gdy aplikacja jest uruchomiona, wyświetlając prosty **UIAlert**.

11. W `ViewController.m`, `import` dodaj `AppDelegate.h` instrukcję dla i skopiuj następujący kod do metody generowanej przez `subscribe` XCode. Ten kod aktualizuje rejestrację powiadomień, aby używać nowych tagów kategorii wybranych przez użytkownika w interfejsie użytkownika.

    ```objc
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
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:"Notification" message:"Subscribed" delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

    Ta metoda `NSMutableArray` tworzy jedną z kategorii `Notifications` i używa klasy do przechowywania listy w magazynie lokalnym i rejestruje odpowiednie tagi z centrum powiadomień. Jeśli kategorie zostaną zmienione, rejestracja zostanie ponownie utworzona przy użyciu nowych kategorii.

12. W `ViewController.m`programie dodaj następujący `viewDidLoad` kod w metodzie, aby ustawić interfejs użytkownika na podstawie wcześniej zapisanych kategorii.

    ```objc
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

Aplikacja może teraz przechowywać zestaw kategorii w lokalnej pamięci masowej urządzenia używanej do rejestrowania się w centrum powiadomień przy każdym uruchomieniu aplikacji. Użytkownik może zmienić wybór kategorii w czasie `subscribe` wykonywania i kliknij metodę, aby zaktualizować rejestrację urządzenia. Następnie zaktualizuj aplikację, aby wysyłać powiadomienia o nowościach bezpośrednio w samej aplikacji.

## <a name="optional-send-tagged-notifications"></a>(opcjonalnie) Wysyłanie oznakowanych powiadomień

Jeśli nie masz dostępu do programu Visual Studio, możesz przejść do następnej sekcji i wysyłać powiadomienia z samej aplikacji. Można również wysłać powiadomienie o szablonie z [witryny Azure portal] przy użyciu karty debugowania dla centrum powiadomień.

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>(opcjonalnie) Wysyłanie powiadomień z urządzenia

Zwykle powiadomienia będą wysyłane przez usługę zaplecza, ale możesz wysyłać powiadomienia o najświeższych wiadomościach bezpośrednio z aplikacji. Aby to zrobić, `SendNotificationRESTAPI` należy zaktualizować metodę zdefiniowaną w [samouczku Wprowadzenie do powiadomień z centrami powiadomień.][get-started]

1. W `ViewController.m`, `SendNotificationRESTAPI` zaktualizuj metodę w następujący sposób, aby zaakceptować parametr tagu kategorii i wysłać odpowiednie powiadomienie [szablonu.](notification-hubs-templates-cross-platform-push-messages.md)

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

2. W `ViewController.m`, `Send Notification` zaktualizuj akcję, jak pokazano w poniższym kodzie. Tak, aby wysyła powiadomienia przy użyciu każdego tagu indywidualnie i wysyła do wielu platform.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        self.sendResults.text = @"";

        NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                @"Technology", @"Science", @"Sports", nil];

        // Lets send the message as breaking news for each category to WNS, FCM, and APNS
        // using a template.
        for(NSString* category in categories)
        {
            [self SendNotificationRESTAPI:category];
        }
    }
    ```

3. Odbuduj projekt i upewnij się, że nie ma błędów kompilacji.

## <a name="run-the-app-and-generate-notifications"></a>Uruchamianie aplikacji i generowanie powiadomień

1. Kliknij przycisk Uruchom, aby skompilować projekt i uruchomić aplikację. Wybierz kilka najświeższych opcji wiadomości, aby subskrybować, a następnie naciśnij przycisk **Subskrybuj.** Powinno zostać wyświetlone okno dialogowe wskazujące, że powiadomienia zostały subskrybowane.

    ![Przykładowe powiadomienie w iOS][1]

    Po wybraniu opcji **Subskrybuj**aplikacja konwertuje wybrane kategorie na znaczniki i żąda nowej rejestracji urządzenia dla wybranych tagów z Centrum powiadomień.

2. Wprowadź wiadomość, która ma zostać wysłana jako przełomowa wiadomość, a następnie naciśnij przycisk **Wyślij powiadomienie.** Alternatywnie uruchom aplikację konsoli .NET, aby wygenerować powiadomienia.

    ![Zmienianie preferencji powiadomień w systemach iOS][2]

3. Każde urządzenie subskrybowane na najświeższe wiadomości otrzymuje powiadomienia o nowościach, które właśnie wysłałeś.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wysłano powiadomienia o emisji do określonych urządzeń z systemem iOS, które zostały zarejestrowane dla kategorii. Aby dowiedzieć się, jak wypychać zlokalizowane powiadomienia, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Wypychanie powiadomień zlokalizowanych](notification-hubs-ios-xplat-localized-apns-push-notification.md)

<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: https://msdn.microsoft.com/library/jj927168.aspx
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: https://msdn.microsoft.com/library/jj927168.aspx
[get-started]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Portal Azure]: https://portal.azure.com
