---
title: Zaawansowane wypychanie w centrach powiadomień platformy Azure
description: Dowiedz się, jak wysyłać zaawansowane powiadomienia wypychane do aplikacji na iOS z platformy Azure. Przykłady kodu napisane w językach Objective-C i C#.
documentationcenter: ios
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 590304df-c0a4-46c5-8ef5-6a6486bb3340
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 9da629929ca88f406dc503710477104be94c47e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212191"
---
# <a name="azure-notification-hubs-rich-push"></a>Zaawansowane wypychanie w centrach powiadomień platformy Azure

## <a name="overview"></a>Omówienie

Aby przyciągnąć użytkowników do natychmiastowej zawartości rozszerzonej, aplikacja może chcieć wypchnąć poza zwykły tekst. Powiadomienia te promują interakcje z użytkownikami i prezentują treści, takie jak adresy URL, dźwięki, obrazy/ kupony i inne. Ten samouczek opiera się na [powiadom użytkowników](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) tematu i pokazuje, jak wysyłać powiadomienia wypychań, które zawierają ładunki (na przykład obraz).

Ten samouczek jest zgodny z systemem iOS 7 & 8.

  ![][IOS1]

Na wysokim poziomie:

1. Zaplecze aplikacji:
   * Przechowuje bogaty ładunek (w tym przypadku obraz) w bazie danych wewnętrznej bazy danych/magazynu lokalnego
   * Wysyła identyfikator tego bogatego powiadomienia do urządzenia
2. Aplikacja na urządzeniu:
   * Kontaktuje się z zapleczem żądającym ładunku z rozszerzonym identyfikatorem, który otrzymuje
   * Wysyła powiadomienia użytkowników na urządzeniu po zakończeniu pobierania danych i pokazuje ładunek natychmiast po dotknięciu przez użytkowników, aby dowiedzieć się więcej

## <a name="webapi-project"></a>Projekt WebAPI

1. W programie Visual Studio otwórz projekt **AppBackend** utworzony w samouczku [Powiadom użytkowników.](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)
2. Uzyskaj obraz, z którego chcesz powiadomić użytkowników, i umieść go w folderze **img** w katalogu projektu.
3. Kliknij **pozycję Pokaż wszystkie pliki** w Eksploratorze rozwiązań, a następnie kliknij prawym przyciskiem myszy folder do uwzględnienia w programie **Project**.
4. Po wybraniu obrazu zmień okno Akcja kompilacji we właściwościach na **Osadzony zasób**.

    ![][IOS2]
5. W pliku `Notifications.cs` dodaj następującą instrukcję using:

    ```csharp
    using System.Reflection;
    ```
6. Zaktualizuj całą `Notifications` klasę za pomocą następującego kodu. Pamiętaj, aby zastąpić symbole zastępcze poświadczeniami centrum powiadomień i nazwą pliku obrazu.

    ```csharp
    public class Notification {
        public int Id { get; set; }
        // Initial notification message to display to users
        public string Message { get; set; }
        // Type of rich payload (developer-defined)
        public string RichType { get; set; }
        public string Payload { get; set; }
        public bool Read { get; set; }
    }

    public class Notifications {
        public static Notifications Instance = new Notifications();

        private List<Notification> notifications = new List<Notification>();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            // Placeholders: replace with the connection string (with full access) for your notification hub and the hub name from the Azure Classics Portal
            Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",  "{hub name}");
        }

        public Notification CreateNotification(string message, string richType, string payload) {
            var notification = new Notification() {
                Id = notifications.Count,
                Message = message,
                RichType = richType,
                Payload = payload,
                Read = false
            };

            notifications.Add(notification);

            return notification;
        }

        public Stream ReadImage(int id) {
            var assembly = Assembly.GetExecutingAssembly();
            // Placeholder: image file name (for example, logo.png).
            return assembly.GetManifestResourceStream("AppBackend.img.{logo.png}");
        }
    }
    ```

   > [!NOTE]
   > (opcjonalnie) Zobacz [Jak osadzać i uzyskiwać dostęp do zasobów przy użyciu języka Visual C#,](https://support.microsoft.com/kb/319292) aby uzyskać więcej informacji na temat dodawania i uzyskiwania zasobów projektu.

7. W `NotificationsController.cs`, redefine 'NotificationsController z następującymi fragmentami kodu. Spowoduje to wysłanie początkowego cichego, bogatego identyfikatora powiadomień do urządzenia i umożliwia pobieranie obrazu po stronie klienta:

    ```csharp
    // Return http response with image binary
    public HttpResponseMessage Get(int id) {
        var stream = Notifications.Instance.ReadImage(id);

        var result = new HttpResponseMessage(HttpStatusCode.OK);
        result.Content = new StreamContent(stream);
        // Switch in your image extension for "png"
        result.Content.Headers.ContentType = new System.Net.Http.Headers.MediaTypeHeaderValue("image/{png}");

        return result;
    }

    // Create rich notification and send initial silent notification (containing id) to client
    public async Task<HttpResponseMessage> Post() {
        // Replace the placeholder with image file name
        var richNotificationInTheBackend = Notifications.Instance.CreateNotification("Check this image out!", "img",  "{logo.png}");

        var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

        // Silent notification with content available
        var aboutUser = "{\"aps\": {\"content-available\": 1, \"sound\":\"\"}, \"richId\": \"" + richNotificationInTheBackend.Id.ToString() + "\",  \"richMessage\": \"" + richNotificationInTheBackend.Message + "\", \"richType\": \"" + richNotificationInTheBackend.RichType + "\"}";

        // Send notification to apns
        await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(aboutUser, usernameTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```
8. Teraz ponownie wdrożymy tę aplikację w witrynie sieci Web platformy Azure, aby była dostępna ze wszystkich urządzeń. Kliknij prawym przyciskiem myszy projekt **AppBackend** i wybierz polecenie **Publikuj**.
9. Wybierz witrynę sieci Web platformy Azure jako miejsce docelowe publikowania. Zaloguj się za pomocą konta platformy Azure i wybierz istniejącą lub nową witrynę sieci Web i zanotuj **właściwość docelowego adresu URL** na karcie **Połączenie.** Będziemy odnosić się do tego adresu URL jako *punktu końcowego wewnętrznej bazy danych* w dalszej części tego samouczka. Kliknij przycisk **Opublikuj**.

## <a name="modify-the-ios-project"></a>Modyfikowanie projektu systemu iOS

Teraz, gdy zmodyfikowano wewnętrznej bazy danych aplikacji, aby wysłać tylko *identyfikator* powiadomienia, można zmienić aplikację systemu iOS do obsługi tego identyfikatora i pobrać bogatą wiadomość z wewnętrznej bazy danych.

1. Otwórz projekt systemu iOS i włącz powiadomienia zdalne, przechodząc do głównego celu aplikacji w sekcji **Obiekty docelowe.**
2. Kliknij opcję **Możliwości**, włącz **tryby w tle**i zaznacz pole wyboru Powiadomienia **zdalne.**

    ![][IOS3]
3. Otwórz `Main.storyboard`, i upewnij się, że masz kontroler widoku (dalej home view kontrolera w tym samouczku) z [pouczek powiadom użytkownika.](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)
4. Dodaj **kontroler nawigacji** do scenorysu i przeciągnij sterowanie do kontrolera widoku głównego, aby uczynić go **głównym widokiem** nawigacji. Upewnij się, że **kontroler widoku początkowego** w inspektorze atrybutów jest wybrany tylko dla kontrolera nawigacji.
5. Dodawanie **kontrolera widoku** do scenorysu i dodawanie **widoku obrazu**. Jest to strona, którą użytkownicy zobaczą, gdy zdecydują się dowiedzieć więcej, klikając powiadomienie. Scenorys powinien wyglądać następująco:

    ![][IOS4]
6. Kliknij **kontroler widoku głównego** w scenorysie i upewnij się, że ma **homeViewController** jako jego **klasa niestandardowa** i **identyfikator scenorysu** w inspektorze tożsamości.
7. Zrób to samo dla kontrolera widoku obrazu jako **imageViewController**.
8. Następnie należy utworzyć nową klasę kontrolera widoku o nazwie **imageViewController** do obsługi interfejsu użytkownika, który właśnie utworzono.
9. W **imageViewController.h**, dodać następujące do deklaracji interfejsu kontrolera. Upewnij się, że przeciągnij z widoku obrazu scenorysu do tych właściwości, aby połączyć te dwa:

    ```objc
    @property (weak, nonatomic) IBOutlet UIImageView *myImage;
    @property (strong) UIImage* imagePayload;
    ```
10. W `imageViewController.m`, dodać następujące na `viewDidload`końcu :

    ```objc
    // Display the UI Image in UI Image View
    [self.myImage setImage:self.imagePayload];
    ```
11. W `AppDelegate.m`programze importuj utworzony kontroler obrazu:

    ```objc
    #import "imageViewController.h"
    ```
12. Dodaj sekcję interfejsu z następującą deklaracją:

    ```objc
    @interface AppDelegate ()

    @property UIImage* imagePayload;
    @property NSDictionary* userInfo;
    @property BOOL iOS8;

    // Obtain content from backend with notification id
    - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;

    // Redirect to Image View Controller after notification interaction
    - (void)redirectToImageViewWithImage: (UIImage *)img;

    @end
    ```
13. W `AppDelegate`obszarze , upewnij się, że `application: didFinishLaunchingWithOptions`aplikacja rejestruje się w celu otrzymywanych powiadomień w trybie :

    ```objc
    // Software version
    self.iOS8 = [[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)];

    // Register for remote notifications for iOS8 and previous versions
    if (self.iOS8) {
        NSLog(@"This device is running with iOS8.");

        // Action
        UIMutableUserNotificationAction *richPushAction = [[UIMutableUserNotificationAction alloc] init];
        richPushAction.identifier = @"richPushMore";
        richPushAction.activationMode = UIUserNotificationActivationModeForeground;
        richPushAction.authenticationRequired = NO;
        richPushAction.title = @"More";

        // Notification category
        UIMutableUserNotificationCategory* richPushCategory = [[UIMutableUserNotificationCategory alloc] init];
        richPushCategory.identifier = @"richPush";
        [richPushCategory setActions:@[richPushAction] forContext:UIUserNotificationActionContextDefault];

        // Notification categories
        NSSet* richPushCategories = [NSSet setWithObjects:richPushCategory, nil];

        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                UIUserNotificationTypeAlert |
                                                UIUserNotificationTypeBadge
                                                                                    categories:richPushCategories];

        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];

    }
    else {
        // Previous iOS versions
        NSLog(@"This device is running with iOS7 or earlier versions.");

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    }

    return YES;
    ```

14. Zastąp w `application:didRegisterForRemoteNotificationsWithDeviceToken` następującej implementacji, aby uwzględnić zmiany interfejsu użytkownika scenorysu:

    ```objc
    // Access navigation controller which is at the root of window
    UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
    // Get home view controller from stack on navigation controller
    homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
    hvc.deviceToken = deviceToken;
    ```
15. Następnie dodaj następujące metody, aby `AppDelegate.m` pobrać obraz z punktu końcowego i wysłać powiadomienie lokalne po zakończeniu pobierania. Pamiętaj, aby zastąpić `{backend endpoint}` symbol zastępczy punktem końcowym wewnętrznej bazy danych:

    ```objc
    NSString *const GetNotificationEndpoint = @"{backend endpoint}/api/notifications";

    // Helper: retrieve notification content from backend with rich notification id
    - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion {
        UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
        homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
        NSString* authenticationHeader = hvc.registerClient.authenticationHeader;
        // Check if authenticated
        if (!authenticationHeader) return;

        NSURLSession* session = [NSURLSession
                                sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                delegate:nil
                                delegateQueue:nil];

        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, richId]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"GET"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {

            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200) {
                // From NSData to UIImage
                self.imagePayload = [UIImage imageWithData:data];

                completion(nil);
            }
            else {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(error);
                else {
                    completion([NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }

    // Handle silent push notifications when id is sent from backend
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler {
        self.userInfo = userInfo;
        int richId = [[self.userInfo objectForKey:@"richId"] intValue];
        NSString* richType = [self.userInfo objectForKey:@"richType"];

        // Retrieve image data
        if ([richType isEqualToString:@"img"]) {  
            [self retrieveRichImageWithId:richId completion:^(NSError* error) {
                if (!error){
                    // Send local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];

                    // "5" is arbitrary here to give you enough time to quit out of the app and receive push notifications
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:5];
                    localNotification.userInfo = self.userInfo;
                    localNotification.alertBody = [self.userInfo objectForKey:@"richMessage"];
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];

                    // iOS8 categories
                    if (self.iOS8) {
                        localNotification.category = @"richPush";
                    }

                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                    handler(UIBackgroundFetchResultNewData);
                }
                else{
                    handler(UIBackgroundFetchResultFailed);
                }
            }];
        }
        // Add "else if" here to handle more types of rich content such as url, sound files, etc.
    }
    ```
16. Obsłużyć powiadomienie lokalne powyżej, `AppDelegate.m` otwierając kontroler widoku obrazu w następujących metod:

    ```objc
    // Helper: redirect users to image view controller
    - (void)redirectToImageViewWithImage: (UIImage *)img {
        UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
        UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main" bundle: nil];
        imageViewController *imgViewController = [mainStoryboard instantiateViewControllerWithIdentifier: @"imageViewController"];
        // Pass data/image to image view controller
        imgViewController.imagePayload = img;

        // Redirect
        [navigationController pushViewController:imgViewController animated:YES];
    }

    // Handle local notification sent above in didReceiveRemoteNotification
    - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
        if (application.applicationState == UIApplicationStateActive) {
            // Show in-app alert with an extra "more" button
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:notification.alertBody delegate:self cancelButtonTitle:@"OK" otherButtonTitles:@"More", nil];
            [alert show];
        }
        // App becomes active from user's tap on notification
        else {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
    }

    // Handle buttons in in-app alerts and redirect with data/image
    - (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex {
        // Handle "more" button
        if (buttonIndex == 1)
        {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
        // Add "else if" here to handle more buttons
    }

    // Handle notification setting actions in iOS8
    - (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forLocalNotification:(UILocalNotification *)notification completionHandler:(void (^)())completionHandler {
        // Handle richPush related buttons
        if ([identifier isEqualToString:@"richPushMore"]) {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
        completionHandler();
    }
    ```

## <a name="run-the-application"></a>Uruchamianie aplikacji

1. W xcode uruchom aplikację na fizycznym urządzeniu z systemem iOS (powiadomienia wypychania nie będą działać w symulatorze).
2. W interfejsie użytkownika aplikacji dla systemu iOS wprowadź nazwę użytkownika i hasło o tej samej wartości do uwierzytelniania i kliknij przycisk **Zaloguj**się .
3. Kliknij **przycisk Wyślij wypychanie,** a powinien zostać wyświetlony alert w aplikacji. Jeśli klikniesz na **Więcej**, zostaniesz przeniesiony do obrazu, który wybrałeś do uwzględnienia w wewnętrznej cemie aplikacji.
4. Możesz również kliknąć przycisk **Wyślij** i natychmiast nacisnąć przycisk początek urządzenia. Za kilka chwil otrzymasz powiadomienie push. Jeśli dotkniesz go lub klikniesz więcej, zostaniesz przeniesiony do aplikacji i zawartości bogatego obrazu.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
