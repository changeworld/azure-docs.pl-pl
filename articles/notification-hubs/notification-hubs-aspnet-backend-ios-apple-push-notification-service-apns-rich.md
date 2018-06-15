---
title: Wypychania sformatowanego centra powiadomień Azure
description: Dowiedz się, jak wysyłać powiadomienia wypychane sformatowanego do aplikacji systemu iOS z platformy Azure. Przykłady kodu napisane w języku Objective C i C#.
documentationcenter: ios
services: notification-hubs
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 590304df-c0a4-46c5-8ef5-6a6486bb3340
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/25/2018
ms.author: dimazaid
ms.openlocfilehash: 21ed7dd1120958576651703283a0732e3843546d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777461"
---
# <a name="azure-notification-hubs-rich-push"></a>Wypychania sformatowanego centra powiadomień Azure
## <a name="overview"></a>Przegląd
Aby angażowania użytkowników błyskawicznych sformatowanego zawartość, aplikacja może być push poza zwykły tekst. Te powiadomienia wspierania interakcji użytkowników i obecny zawartości, takie jak adresy URL, dźwięki, obrazy/bony i. W tym samouczku opiera się na [Powiadom użytkowników](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) tematu i pokazuje, jak wysyłać powiadomienia wypychane, zawierające ładunków (na przykład obraz).

W tym samouczku jest zgodny z systemem iOS 7 i 8.

  ![][IOS1]

Na wysokim poziomie:

1. Zaplecze aplikacji:
   * Przechowuje sformatowanego ładunku (w tym przypadku obrazu) w magazynie bazy danych i lokalnym wewnętrznej bazy danych
   * Wysyła identyfikator sformatowanego powiadomienia do urządzenia
2. Aplikacją na urządzeniu:
   * Kontakty zaplecza sformatowanego ładunku o identyfikatorze odbiera żądanie
   * Wysyła powiadomienia użytkowników na urządzeniu, po zakończeniu pobierania danych i pokazuje ładunku natychmiast, gdy użytkownik naciśnie polecenie, aby dowiedzieć się więcej

## <a name="webapi-project"></a>WebAPI projektu
1. W programie Visual Studio Otwórz **AppBackend** projektu, który został utworzony w [Powiadom użytkowników](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) samouczka.
2. Uzyskaj obraz chcesz powiadamiać użytkowników i umieszcza je **img** folderu w katalogu projektu.
3. Kliknij przycisk **Pokaż wszystkie pliki** w Eksploratorze rozwiązań i kliknij prawym przyciskiem myszy folder, z którego **załącz do projektu**.
4. Zaznacz obraz, zmienić jego akcji kompilacji w oknie właściwości, aby **osadzonego zasobu**.
   
    ![][IOS2]
5. W **Notifications.cs**, dodaj następującą instrukcję using:
   
        using System.Reflection;
6. Aktualizacji całym **powiadomienia** klasy następującym kodem. Pamiętaj zastąpić symbole zastępcze poświadczenia Centrum powiadomień i nazwę pliku obrazu.
   
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
   
   > [!NOTE]
   > (opcjonalnie) Zapoznaj się [jak osadzić i uzyskiwać dostęp do zasobów przy użyciu programu Visual C#](http://support.microsoft.com/kb/319292) Aby uzyskać więcej informacji na temat sposobu dodawania i uzyskiwanie zasobów projektu.
   > 
   > 
7. W **NotificationsController.cs**, Zmień definicję **NotificationsController** z poniższe fragmenty kodu. Wysyła identyfikator początkowej dyskretnej sformatowanego powiadomień do urządzeń i umożliwia pobranie obrazu po stronie klienta:
   
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
8. Teraz ponownie wdrożymy tę aplikację do witryny sieci Web platformy Azure, aby udostępnić go ze wszystkich urządzeń. Kliknij prawym przyciskiem myszy projekt **AppBackend** i wybierz polecenie **Publikuj**.
9. Wybierz urządzenie docelowe publikowania witryny sieci Web platformy Azure. Zaloguj się przy użyciu konta platformy Azure i wybierz istniejącą lub nową witrynę sieci Web i zanotuj **docelowy adres URL** właściwości w **połączenia** kartę. W dalszej części tego samouczka będziemy nazywać ten adres URL *punktem końcowym zaplecza*. Kliknij przycisk **Opublikuj**.

## <a name="modify-the-ios-project"></a>Zmodyfikuj projekt dla systemu iOS
Teraz, gdy zmieniono zapleczu swojej aplikacji, aby wysłać tylko *identyfikator* powiadomienia, spowoduje zmianę aplikacji systemu iOS do obsługi tego identyfikatora i pobierania sformatowany komunikat z zaplecza.

1. Otwórz projekt dla systemu iOS i włączyć zdalne powiadomienia, przechodząc do urządzenie docelowe głównej aplikacji w **cele** sekcji.
2. Polecenie **możliwości**, Włącz **tryby tła**i sprawdź **zdalnego powiadomienia** wyboru.
   
    ![][IOS3]
3. Przejdź do **Main.storyboard**i upewnij się, że masz kontroler widoku (określonej jako Home kontrolera widoku w tym samouczku) z [powiadamia użytkownika](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) samouczka.
4. Dodaj **kontrolera nawigacji** do scenorysu i kontrolki przeciągania Home kontrolera widoku umożliwia **Widok główny** nawigacji. Upewnij się, że **jest początkowej kontrolera widoku** w atrybutach inspektora wybrano tylko kontrolera nawigacji.
5. Dodaj **kontrolera widoku** scenorysu, a następnie dodaj **widoku obrazu**. Jest to strona, którą użytkownicy zobaczą, gdy wybiera on dowiedzieć się więcej, klikając notifiication. Twoje scenorysu powinna wyglądać następująco:
   
    ![][IOS4]
6. Polecenie **Home kontrolera widoku** scenorysu, i upewnij się, że ma ona **homeViewController** jako jego **klasy niestandardowej** i **scenorysu identyfikator**w obszarze inspektora tożsamości.
7. Wykonaj te same czynności dla obrazu kontroler widoku jako **imageViewController**.
8. Następnie utwórz nową klasę kontrolera widoku zatytułowany **imageViewController** do obsługi interfejsu użytkownika został utworzony.
9. W **imageViewController.h**, Dodaj następujący kod do kontrolera deklaracji interfejsu. Upewnij się, że formant podczas przeciągania z widoku obrazu scenorysu te właściwości, aby połączyć dwóch:
   
        @property (weak, nonatomic) IBOutlet UIImageView *myImage;
        @property (strong) UIImage* imagePayload;
10. W **imageViewController.m**, Dodaj następujący kod na końcu **viewDidload**:
    
        // Display the UI Image in UI Image View
        [self.myImage setImage:self.imagePayload];
11. W **AppDelegate.m**, zaimportuj kontrolera obraz został utworzony:
    
        #import "imageViewController.h"
12. Dodaj sekcję interfejsu z deklaracją następujące:
    
        @interface AppDelegate ()
    
        @property UIImage* imagePayload;
        @property NSDictionary* userInfo;
        @property BOOL iOS8;
    
        // Obtain content from backend with notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;
    
        // Redirect to Image View Controller after notification interaction
        - (void)redirectToImageViewWithImage: (UIImage *)img;
    
        @end
13. W **AppDelegate**, upewnij się, że aplikacja rejestruje dyskretnej powiadomień w **aplikacji: didFinishLaunchingWithOptions**:
    
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

1. Subsitute w następujących implementacji dla **aplikacji: didRegisterForRemoteNotificationsWithDeviceToken** do wykonania dla scenorysu zmiany interfejsu użytkownika do konta:
   
       // Access navigation controller which is at the root of window
       UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
       // Get home view controller from stack on navigation controller
       homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
       hvc.deviceToken = deviceToken;
2. Następnie należy dodać następujące metody umożliwiające **AppDelegate.m** można pobrać obrazu z punktu końcowego i wysyłania powiadomień lokalnego, po ukończeniu pobierania. Zastąp symbol zastępczy `{backend endpoint}` z wewnętrznej bazy danych punktu końcowego:
   
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
3. Obsługę lokalnego powiadomienia powyżej otwierając kontroler widoku obrazu w **AppDelegate.m** z następujących metod:
   
       // Helper: redirect users to image view controller
       - (void)redirectToImageViewWithImage: (UIImage *)img {
           UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
           UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main"
                                                                    bundle: nil];
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

## <a name="run-the-application"></a>Uruchamianie aplikacji
1. W programie XCode Uruchom aplikację na urządzenie fizyczne z systemem iOS (wypychanie powiadomień nie będzie działać w symulatorze).
2. W aplikacji systemu iOS interfejsu użytkownika, wprowadź nazwę użytkownika i hasła tę samą wartość do uwierzytelniania i kliknięcie **dziennika w**.
3. Kliknij przycisk **wysyłania wypychania** i powinien zostać wyświetlony alert w aplikacji. Po kliknięciu **więcej**, zostanie przeniesiony do obrazu chcesz uwzględnić w zapleczu swojej aplikacji.
4. Możesz również kliknąć **wysyłania wypychania** i natychmiast naciśnij przycisk macierzystego urządzenia. Za chwilę otrzymasz powiadomienie wypychane. Wybierz na niej, lub kliknij przycisk więcej, zostanie wyświetlona do aplikacji i sformatowanego obrazu.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
