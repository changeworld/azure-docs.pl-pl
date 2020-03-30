---
title: Wysyłanie powiadomień wypychanych do aplikacji systemu iOS przy użyciu usługi Azure Notification Hubs | Dokumenty firmy Microsoft
description: Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia wypychane do aplikacji dla systemu iOS przy użyciu usługi Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
keywords: powiadomienie wypychane, powiadomienia wypychane, powiadomienia wypychane w systemie ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/21/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 032ca8d4ecbcf1fc7f3c22cbe5a0ee934fc5e17c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74407087"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Samouczek: Wysyłanie powiadomień wypychanych do aplikacji systemu iOS przy użyciu usługi Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Cel C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

W tym samouczku używasz usługi Azure Notification Hubs do wysyłania powiadomień wypychanych do aplikacji systemu iOS. Przy użyciu usługi [Apple Push Notification Service (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1) utworzysz pustą aplikację dla systemu iOS służącą do odbierania powiadomień push.

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Generowanie pliku żądania podpisania certyfikatu
> * Rejestrowanie aplikacji dla usługi powiadomień wypychanych
> * Tworzenie profilu inicjowania obsługi dla aplikacji
> * Konfigurowanie centrum powiadomień dla powiadomień push systemu iOS
> * Łączenie aplikacji dla systemu iOS z usługą Notification Hubs
> * Wysyłanie testowych powiadomień wypychanych
> * Sprawdzanie, czy aplikacja odbiera powiadomienia

Pełny kod dla tego samouczka można znaleźć [na GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/master/Samples).

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

* Aktywne konto platformy Azure. Jeśli nie masz konta, możesz [utworzyć bezpłatne konto platformy Azure](https://azure.microsoft.com/free).
* [Struktura Windows Azure Messaging]
* Najnowsza wersja środowiska [Xcode]
* Urządzenie z systemem iOS w wersji 10 (lub nowszej)
* Członkostwo w [programie dla deweloperów firmy Apple](https://developer.apple.com/programs/).
  
  > [!NOTE]
  > Ze względu na wymagania dotyczące konfiguracji powiadomień wypychanych należy wdrożyć i przetestować powiadomienia wypychane na fizycznym urządzeniu z systemem iOS (telefonie iPhone lub tablecie iPad), a nie w symulatorze systemu iOS.
  
Wykonanie czynności opisanych w tym samouczku jest wymaganiem wstępnym dla wszystkich innych samouczków usługi Notification Hubs dotyczących aplikacji dla systemu iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-notification-hubs"></a>Łączenie aplikacji dla systemu iOS z usługą Notification Hubs

1. W środowisku Xcode utwórz nowy projekt dla systemu iOS i wybierz szablon **Single View Application** (Aplikacja z jednym widokiem).

    ![Xcode — aplikacja z jednym widokiem][8]

2. Podczas określania opcji dla nowego projektu upewnij się, że użyto tych samych wartości **Product Name** (Nazwa produktu) i **Organization Identifier** (Identyfikator organizacji), które zostały użyte podczas ustawiania identyfikatora pakietu w portalu dla deweloperów firmy Apple.

3. W obszarze Project Navigator wybierz nazwę projektu w obszarze **Obiekty docelowe**, a następnie **Team** wybierz kartę **Podpisywanie & Możliwości.** Środowisko Xcode powinno automatycznie ściągnąć utworzony wcześniej profil aprowizowania na podstawie identyfikatora pakietu.

    Jeśli nowy profil aprowizowania utworzony w programie Xcode nie jest widoczny, odśwież profile dla Twojej tożsamości podpisywania. Kliknij pozycję **Xcode** na pasku menu, kliknij pozycję **Preferences** (Preferencje), kliknij kartę **Account** (Konto), kliknij przycisk **View Details** (Pokaż szczegóły), kliknij tożsamość podpisywania, a następnie kliknij przycisk odświeżania w prawym dolnym rogu.

    ![Xcode — profil aprowizowania][9]

4. Na karcie **Możliwości & podpisywania** wybierz pozycję **+ Możliwość**.  Kliknij dwukrotnie **powiadomienia wypychane,** aby ją włączyć.

    ![Xcode — możliwości push][12]

5. Dodaj moduły SDK usługi Azure Notification Hubs.

   Zestaw SDK usługi Azure Notification Hubs można zintegrować z aplikacją przy użyciu [cocoapods](https://cocoapods.org) lub ręcznie dodając pliki binarne do projektu.

   - Integracja poprzez cocoapods

     Dodaj następujące zależności do `podfile` swojej aplikacji, aby uwzględnić zestaw SDK usługi Azure Notification Hubs do aplikacji.

     ```ruby
     pod 'AzureNotificationHubs-iOS'
     ```

     Uruchom, `pod install` aby zainstalować nowo zdefiniowany zasobnik i otworzyć plik `.xcworkspace`.

     > [!NOTE]
     > Jeśli widzisz błąd, taki jak **[!] Nie można znaleźć specyfikacji dla platformy AzureNotificationHubs-iOS** podczas uruchamiania, `pod install`uruchom, `pod repo update` aby uzyskać najnowsze zasobniki `pod install`z repozytorium Cocoapods, a następnie uruchom .

   - Integracja poprzez Kartadę

     Dodaj następujące zależności do `Cartfile` swojej aplikacji, aby uwzględnić zestaw SDK usługi Azure Notification Hubs do aplikacji.

     ```ruby
     github "Azure/azure-notificationhubs-ios"
     ```

     Następnie zaktualizuj i stwórz zależności:

     ```shell
     $ carthage update
     ```

     Aby uzyskać więcej informacji na temat korzystania z Kartathage, zobacz [repozytorium Kartuskiej GitHub](https://github.com/Carthage/Carthage).

   - Integracja przez skopiowanie plików binarnych do projektu

     1. Pobierz platformę [SDK usługi Azure Notification Hubs](https://github.com/Azure/azure-notificationhubs-ios/releases) dostarczoną jako plik zip i rozpakuj ją.

     2. W programie Xcode kliknij prawym przyciskiem myszy projekt i kliknij opcję **Add Files to** (Dodaj pliki do), aby dodać folder **WindowsAzureMessaging.framework** do projektu Xcode. Wybierz pozycję **Options** (Opcje), upewnij się, że pozycja **Copy items if needed** (Skopiuj elementy w razie potrzeby) jest zaznaczona, a następnie kliknij pozycję **Add** (Dodaj).

        ![Rozpakowywanie zestawu SDK platformy Azure][10]

6. Dodaj nowy plik nagłówka do projektu o nazwie **Constants.h**. Aby to zrobić, kliknij prawym przyciskiem myszy nazwę projektu i wybierz polecenie **Nowy plik...**. Następnie wybierz **pozycję Plik nagłówka**. Ten plik zawiera stałe używane przez centrum powiadomień. Następnie wybierz **przycisk Dalej**. Nazwij plik **Constants.h**.

7. Dodaj następujący kod do pliku Constants.h:

    ```objc
    #ifndef Constants_h
    #define Constants_h

    extern NSString* const NHInfoConnectionString;
    extern NSString* const NHInfoHubName;
    extern NSString* const NHUserDefaultTags;

    #endif /* Constants_h */
    ```

8. Dodaj plik implementacji dla Constants.h. Aby to zrobić, kliknij prawym przyciskiem myszy nazwę projektu i wybierz polecenie **Nowy plik...**. Wybierz **opcję Plik Objective-C**, a następnie wybierz pozycję **Dalej**. Nazwij plik **Constants.m**.

    ![Dodaj plik .m](media/notification-hubs-ios-get-started/new-file-objc.png)

9. Otwórz plik **Constants.m** i zastąp jego zawartość następującym kodem. Zastąp symbole `NotificationHubConnectionString` zastępcze literału ciągu i `NotificationHubConnectionString` nazwę koncentratora oraz podpis **DefaultListenSharedAccess,** zgodnie z wcześniejszymi danymi uzyskanymi z portalu:

    ```objc
    #import <Foundation/Foundation.h>
    #import "Constants.h"

    NSString* const NHInfoConnectionString = @"NotificationHubConnectionString";
    NSString* const NHInfoHubName = @"NotificationHubName";
    NSString* const NHUserDefaultTags = @"notification_tags";
    ```

10. Otwórz plik **AppDelegate.h** projektu i zastąp jego zawartość następującym kodem:

    ```objc
    #import <UIKit/UIKit.h>
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h> 

    @interface AppDelegate : UIResponder <UIApplicationDelegate,UNUserNotificationCenterDelegate>

    @property (strong, nonatomic) UIWindow *window;

    - (void)handleRegister;
    - (void)handleUnregister;

    @end

    ```

11. W pliku **AppDelegate.m** projektu dodaj następujące `import` instrukcje:

    ```objc
    #import "Constants.h"
    #import "NotificationDetailViewController.h"
    ```

12. Również w pliku **AppDelegate.m** dodaj następujący wiersz `didFinishLaunchingWithOptions` kodu w metodzie opartej na wersji systemu iOS. Ten kod rejestruje dojście do urządzenia w usłudze APNs:

    ```objc
    [[UNUserNotificationCenter currentNotificationCenter] setDelegate:self];
    ```

13. W tym samym pliku **AppDelegate.m** zastąp cały kod następującym `didFinishLaunchingWithOptions` kodem:

    ```objc
    // Tells the app that a remote notification arrived that indicates there is data to be fetched.

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))completionHandler {
        NSLog(@"Received remote (silent) notification");
        [self logNotificationDetails:userInfo];

        //
        // Let the system know the silent notification has been processed.
        //
        completionHandler(UIBackgroundFetchResultNoData);
    }

    // Tells the delegate that the app successfully registered with Apple Push Notification service (APNs).

    - (void) application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
        NSMutableSet *tags = [[NSMutableSet alloc] init];

        // Load and parse stored tags
        NSString *unparsedTags = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
        if (unparsedTags.length > 0) {
            NSArray *tagsArray = [unparsedTags componentsSeparatedByString: @","];
            [tags addObjectsFromArray:tagsArray];
        }

        // Register the device with the Notification Hub.
        // If the device has not already been registered, this will create the registration.
        // If the device has already been registered, this will update the existing registration.
        //
        SBNotificationHub* hub = [self getNotificationHub];
        [hub registerNativeWithDeviceToken:deviceToken tags:tags completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            } else {
                [self showAlert:@"Registered" withTitle:@"Registration Status"];
            }
        }];
    }

    // UNUserNotificationCenterDelegate methods
    //
    // Asks the delegate how to handle a notification that arrived while the app was running in the  foreground.

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler {
        NSLog(@"Received notification while the application is in the foreground");

        // The system calls this delegate method when the app is in the foreground. This allows the app to handle the notification
        // itself (and potentially modify the default system behavior).

        // Handle the notification by displaying custom UI.
        //
        [self showNotification:notification.request.content.userInfo];

        // Use 'options' to specify which default behaviors to enable.
        // - UNAuthorizationOptionBadge: Apply the notification's badge value to the app’s icon.
        // - UNAuthorizationOptionSound: Play the sound associated with the notification.
        // - UNAuthorizationOptionAlert: Display the alert using the content provided by the notification.
        //
        // In this case, do not pass UNAuthorizationOptionAlert because the notification was handled by the app.
        //
        completionHandler(UNAuthorizationOptionBadge | UNAuthorizationOptionSound);
    }

    // Asks the delegate to process the user's response to a delivered notification.
    //

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)(void))completionHandler {
        NSLog(@"Received notification while the application is in the background");

        // The system calls this delegate method when the user taps or responds to the system notification.

        // Handle the notification response by displaying custom UI
        //
        [self showNotification:response.notification.request.content.userInfo];

        // Let the system know the response has been processed.
        //
        completionHandler();
    }

    // App logic and helpers

    - (SBNotificationHub *)getNotificationHub {
        NSString *hubName = [[NSBundle mainBundle] objectForInfoDictionaryKey:NHInfoHubName];
        NSString *connectionString = [[NSBundle mainBundle] objectForInfoDictionaryKey:NHInfoConnectionString];

        return [[SBNotificationHub alloc] initWithConnectionString:connectionString notificationHubPath:hubName];
    }

    - (void)handleRegister {
        UNUserNotificationCenter *center = [UNUserNotificationCenter currentNotificationCenter];

        UNAuthorizationOptions options =  UNAuthorizationOptionAlert | UNAuthorizationOptionSound | UNAuthorizationOptionBadge;
        [center requestAuthorizationWithOptions:(options) completionHandler:^(BOOL granted, NSError * _Nullable error) {
            if (error != nil) {
                NSLog(@"Error requesting for authorization: %@", error);
            }
        }];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
    }

    - (void)handleUnregister {
        //
        // Unregister the device with the Notification Hub.
        //
        SBNotificationHub *hub = [self getNotificationHub];
        [hub unregisterNativeWithCompletion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error unregistering for push: %@", error);
            } else {
                [self showAlert:@"Unregistered" withTitle:@"Registration Status"];
            }
        }];
    }

    - (void)logNotificationDetails:(NSDictionary *)userInfo {
        if (userInfo != nil) {
            UIApplicationState state = [UIApplication sharedApplication].applicationState;
            BOOL background = state != UIApplicationStateActive;
            NSLog(@"Received %@notification: \n%@", background ? @"(background) " : @"", userInfo);
        }
    }

    - (void)showAlert:(NSString *)message withTitle:(NSString *)title {
        if (title == nil) {
            title = @"Alert";
        }
        UIAlertController *alert = [UIAlertController alertControllerWithTitle:title message:message preferredStyle:UIAlertControllerStyleAlert];
        [alert addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:nil]];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:alert animated:YES completion:nil];
    }

    - (void)showNotification:(NSDictionary *)userInfo {
        [self logNotificationDetails:userInfo];

        NotificationDetailViewController *notificationDetail = [[NotificationDetailViewController alloc] initWithUserInfo:userInfo];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:notificationDetail animated:YES completion:nil];
    }

    @end
    ```

    Ten kod łączy się z centrum powiadomień przy użyciu informacji o połączeniu określonych w **pliku Constants.h**. Następnie przekazuje token urządzenia do centrum powiadomień, aby umożliwić wysyłanie powiadomień.

### <a name="notificationdetailviewcontroller"></a>Kontroler powiadomieniaDetailViewController

1. Podobnie jak w poprzednich instrukcjach, dodaj kolejny plik nagłówka o nazwie **NotificationDetailViewController.h**. Zastąp zawartość nowego pliku nagłówka następującym kodem:

    ```objc
    #import <UIKit/UIKit.h>

    NS_ASSUME_NONNULL_BEGIN

    @interface NotificationDetailViewController : UIViewController

    @property (strong, nonatomic) IBOutlet UILabel *titleLabel;
    @property (strong, nonatomic) IBOutlet UILabel *bodyLabel;
    @property (strong, nonatomic) IBOutlet UIButton *dismissButton;

    @property (strong, nonatomic) NSDictionary *userInfo;

    - (id)initWithUserInfo:(NSDictionary *)userInfo;

    @end

    NS_ASSUME_NONNULL_END
    ```

2. Dodaj plik implementacji **NotificationDetailViewController.m**. Zastąp zawartość pliku następującym kodem, `UIViewController` który implementuje metody:

    ```objc
    #import "NotificationDetailViewController.h"

    @interface NotificationDetailViewController ()

    @end

    @implementation NotificationDetailViewController

    - (id)initWithUserInfo:(NSDictionary *)userInfo {
        self = [super initWithNibName:@"NotificationDetail" bundle:nil];
        if (self) {
            _userInfo = userInfo;
        }
        return self;
    }

    - (void)viewDidLayoutSubviews {
        [self.titleLabel sizeToFit];
        [self.bodyLabel sizeToFit];
    }

    - (void)viewDidLoad {
        [super viewDidLoad];

        NSString *title = nil;
        NSString *body = nil;

        NSDictionary *aps = [_userInfo valueForKey:@"aps"];
        NSObject *alertObject = [aps valueForKey:@"alert"];
        if (alertObject != nil) {
            if ([alertObject isKindOfClass:[NSDictionary class]]) {
                NSDictionary *alertDict = (NSDictionary *)alertObject;
                title = [alertDict valueForKey:@"title"];
                body = [alertObject valueForKey:@"body"];
            } else if ([alertObject isKindOfClass:[NSString class]]) {
                body = (NSString *)alertObject;
            } else {
                NSLog(@"Unable to parse notification content. Unexpected format: %@", alertObject);
            }
        }

        if (title == nil) {
            title = @"<unset>";
        }

        if (body == nil) {
            body = @"<unset>";
        }

        self.titleLabel.text = title;
        self.bodyLabel.text = body;
    }

    - (IBAction)handleDismiss:(id)sender {
        [self dismissViewControllerAnimated:YES completion:nil];
    }

    @end
    ```

### <a name="viewcontroller"></a>ViewController (Kontroler widoku)

1. W pliku **ViewController.h** projektu dodaj następujące `import` instrukcje:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h>
    ```

2. Również w **ViewController.h**, dodać następujące `@interface` deklaracje właściwości po deklaracji:

    ```objc
    @property (strong, nonatomic) IBOutlet UITextField *tagsTextField;
    @property (strong, nonatomic) IBOutlet UIButton *registerButton;
    @property (strong, nonatomic) IBOutlet UIButton *unregisterButton;
    ```

3. W pliku implementacji **ViewController.m** projektu zastąp zawartość pliku następującym kodem:

    ```objc
    #import "ViewController.h"
    #import "Constants.h"
    #import "AppDelegate.h"

    @interface ViewController ()

    @end

    @implementation ViewController

    // UIViewController methods

    - (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
        // Simple method to dismiss keyboard when user taps outside of the UITextField.
        [self.view endEditing:YES];
    }

    - (void)viewDidLoad {
        [super viewDidLoad];

        // Load raw tags text from storage and initialize the text field
        self.tagsTextField.text = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
    }

    - (IBAction)handleRegister:(id)sender {
        // Save raw tags text in storage
        [[NSUserDefaults standardUserDefaults] setValue:self.tagsTextField.text forKey:NHUserDefaultTags];

    // Delegate processing the register action to the app delegate.
    [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleRegister)];
    }

    - (IBAction)handleUnregister:(id)sender {
        [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleUnregister)];
    }

    @end
    ```

4. Skompiluj i uruchom aplikację na urządzeniu, aby upewnić się, że nie występują żadne błędy.

## <a name="send-test-push-notifications"></a>Wysyłanie testowych powiadomień wypychanych

Odbieranie powiadomień w aplikacji możesz przetestować za pomocą opcji *Wysyłanie testowe* w witrynie [Azure Portal]. Powoduje to wysłanie testowego powiadomienia push na urządzenie.

![Witryna Azure Portal — wysyłanie testowe][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="verify-that-your-app-receives-push-notifications"></a>Sprawdzanie, czy aplikacja odbiera powiadomienia push

Aby przetestować powiadomienia wypychane w systemie iOS, należy wdrożyć aplikację na fizycznym urządzeniu z systemem iOS. Nie można wysłać powiadomień wypychanych firmy Apple przy użyciu symulatora systemu iOS.

1. Uruchom aplikację i upewnij się, że rejestracja zakończyła się powodzeniem, a następnie naciśnij przycisk **OK**.

    ![Test rejestracji powiadomień wypychanych aplikacji dla systemu iOS][33]

2. Następnie wyślij z witryny [Azure Portal] testowe powiadomienie push, jak opisano powyżej.

3. Powiadomienie wypychane zostanie wysłane do wszystkich urządzeń zarejestrowanych do odbierania powiadomień od określonego centrum powiadomień.

    ![Test odbierania powiadomienia wypychanego z aplikacji dla systemu iOS][35]

## <a name="next-steps"></a>Następne kroki

W tym prostym przykładzie wysłano powiadomienia wypychane do wszystkich zarejestrowanych urządzeń z systemem iOS. Aby dowiedzieć się, jak wysyłać powiadomienia push do konkretnych urządzeń z systemem iOS, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Wypychanie powiadomień do konkretnych urządzeń](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png
[12]: ./media/notification-hubs-ios-get-started/notification-hubs-enable-push.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png

<!-- URLs. -->
[Struktura Windows Azure Messaging]: https://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure Notification Hubs Notify Users for iOS with .NET backend]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Portal Azure]: https://portal.azure.com
