---
title: Powiadomienia wypychane do określonych użytkowników przy użyciu usługi Azure Notification Hubs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wysyłać powiadomienia push do konkretnych użytkowników przy użyciu usługi Azure Notification Hubs.
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
ms.assetid: 1f7d1410-ef93-4c4b-813b-f075eed20082
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 9b6c0715cb85e245aba94adfb8b33d0d07ece9a9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60880471"
---
# <a name="tutorial-push-notifications-to-specific-users-using-azure-notification-hubs"></a>Samouczek: Powiadomienia wypychane do określonych użytkowników przy użyciu usługi Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

W tym samouczku pokazano, jak wysyłać powiadomienia push do użytkownika konkretnej aplikacji na konkretnym urządzeniu za pomocą usługi Azure Notification Hubs. Zaplecza ASP.NET WebAPI jest używany do uwierzytelniania klientów i generowania powiadomień, jak pokazano w temacie wskazówki [rejestrowania z zaplecza aplikacji](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend).

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Tworzenie projektu interfejsu WebAPI
> * Uwierzytelnianie klientów w zapleczu interfejsu WebAPI
> * Rejestrowanie na potrzeby powiadomień za pomocą zaplecza interfejsu WebAPI
> * Wysyłanie powiadomień z zaplecza interfejsu WebAPI
> * Publikowanie nowego zaplecza interfejsu WebAPI
> * Modyfikowanie aplikacji systemu iOS
> * Testowanie aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że utworzone i skonfigurowane Centrum powiadomień, zgodnie z opisem w [wprowadzenie do usługi Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md). W tym samouczku jest również wstępnie wymagana do [Secure Push (iOS)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) samouczka.
Jeśli chcesz używać jako usługi zaplecza usługi Mobile Apps, zobacz [Mobile Apps wprowadzenie do wypychania](../app-service-mobile/app-service-mobile-ios-get-started-push.md).

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Modyfikowanie aplikacji systemu iOS

1. Otwórz aplikację widoku jednostronicowej utworzonej w [wprowadzenie do usługi Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) samouczka.

   > [!NOTE]
   > W tej sekcji założono, projektu skonfigurowano nazwę organizacji puste. Jeśli nie, należy poprzedzić nazwa organizacji do wszystkich nazw klas.

2. W `Main.storyboard` plików, dodawanie składników pokazano na zrzucie ekranu z biblioteki obiektów.

    ![Edytuj scenorysu w programie Xcode interface builder][1]

   * **Nazwa użytkownika**: A UITextField z tekstem zastępczym *wprowadź nazwę użytkownika*tuż poniżej Wyślij wyniki etykiety i ograniczone do lewego i prawego marginesu oraz pod etykieta wyników wysyłania.
   * **Hasło**: A UITextField z tekstem zastępczym *wprowadź hasło*, tuż poniżej nazwę użytkownika tekst pola i ograniczone do lewego i prawego marginesu oraz poniżej pola tekstowego nazwy użytkownika. Sprawdź **Secure wprowadzania tekstu** w obszarze opcji w Inspektorze atrybut *klucz zwracają*.
   * **Zaloguj się**: Obiektu klasy UIButton etykietą bezpośrednio pod polem tekstowym hasła i usuń zaznaczenie pola wyboru **włączone** opcji Inspector atrybutów, w obszarze *zawartość kontrolki*
   * **USŁUGI WNS**: Etykiety i Przełącz, aby włączyć wysyłanie powiadomień Windows Notification Service, jeśli jest została skonfigurowana w Centrum. Zobacz [wprowadzenie Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) samouczka.
   * **GCM**: Etykiety i Przełącz, aby włączyć wysyłanie powiadomienia do Google Cloud Messaging, jeśli jest została skonfigurowana w Centrum. Zobacz [wprowadzenie dla systemu Android](notification-hubs-android-push-notification-google-gcm-get-started.md) samouczka.
   * **APNS**: Etykieta i przełącznik, aby włączyć wysyłanie powiadomienia do usług powiadomień platformy firmy Apple.
   * **Odbiorcy Username:A** UITextField z tekstem zastępczym *tagiem username odbiorcy*bezpośrednio pod GCM etykiety i ograniczone do lewego i prawego marginesu i umieszczone pod wpisami etykieta usługi GCM.

     Niektóre składniki zostały dodane w [wprowadzenie do usługi Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) samouczka.

3. **CTRL** przeciągnij ze składników w widoku, aby `ViewController.h` i dodać tych nowych punktów.

    ```objc
    @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
    @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
    @property (weak, nonatomic) IBOutlet UITextField *RecipientField;
    @property (weak, nonatomic) IBOutlet UITextField *NotificationField;

    // Used to enable the buttons on the UI
    @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
    @property (weak, nonatomic) IBOutlet UIButton *SendNotificationButton;

    // Used to enabled sending notifications across platforms
    @property (weak, nonatomic) IBOutlet UISwitch *WNSSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *GCMSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *APNSSwitch;

    - (IBAction)LogInAction:(id)sender;
    ```

4. W `ViewController.h`, Dodaj następujący kod `#define` po Twoich instrukcjach importu. Zastąp `<Enter Your Backend Endpoint>` zastępczego docelowy adres URL umożliwia wdrażanie zaplecza aplikacji w poprzedniej sekcji. Na przykład `http://your_backend.azurewebsites.net`.

    ```objc
    #define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"
    ```

5. W projekcie, Utwórz nową klasę Cocoa Touch o nazwie `RegisterClient` do interfejsu z zaplecza programu ASP.NET został utworzony. Tworzenie klasy dziedziczącej z `NSObject`. Następnie dodaj następujący kod w `RegisterClient.h`.

    ```objc
    @interface RegisterClient : NSObject

    @property (strong, nonatomic) NSString* authenticationHeader;

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
        andCompletion:(void(^)(NSError*))completion;

    -(instancetype) initWithEndpoint:(NSString*)Endpoint;

    @end
    ```

6. W `RegisterClient.m`, zaktualizuj `@interface` sekcji:

    ```objc
    @interface RegisterClient ()

    @property (strong, nonatomic) NSURLSession* session;
    @property (strong, nonatomic) NSURLSession* endpoint;

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion;
    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion;
    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSString*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion;

    @end
    ```

7. Zastąp `@implementation` sekcji RegisterClient.m następującym kodem:

    ```objc
    @implementation RegisterClient

    // Globals used by RegisterClient
    NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";

    -(instancetype) initWithEndpoint:(NSString*)Endpoint
    {
        self = [super init];
        if (self) {
            NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
            _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
            _endpoint = Endpoint;
        }
        return self;
    }

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
                andCompletion:(void(^)(NSError*))completion
    {
        [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
    }

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion
    {
        NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

        NSString *deviceTokenString = [[token description]
            stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
        deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""]
                                uppercaseString];

        [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString
            completion:^(NSString* registrationId, NSError *error) {
            NSLog(@"regId: %@", registrationId);
            if (error) {
                completion(error);
                return;
            }

            [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString
                tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
                if (error) {
                    completion(error);
                    return;
                }

                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                if (httpResponse.statusCode == 200) {
                    completion(nil);
                } else if (httpResponse.statusCode == 410 && retry) {
                    [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
                } else {
                    NSLog(@"Registration error with response status: %ld", (long)httpResponse.statusCode);

                    completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }

            }];
        }];
    }

    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSData*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion
    {
        NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token,
                                                @"Tags": [tags allObjects]};
        NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration
                            options:NSJSONWritingPrettyPrinted error:nil];

        NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData
                                            encoding:NSUTF8StringEncoding]);

        NSString* endpoint = [NSString stringWithFormat:@"%@/api/register/%@", _endpoint,
                                registrationId];
        NSURL* requestURL = [NSURL URLWithString:endpoint];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"PUT"];
        [request setHTTPBody:jsonData];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
        [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            if (!error)
            {
                completion(response, error);
            }
            else
            {
                NSLog(@"Error request: %@", error);
                completion(nil, error);
            }
        }];
        [dataTask resume];
    }

    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion
    {
        NSString* registrationId = [[NSUserDefaults standardUserDefaults]
                                    objectForKey:RegistrationIdLocalStorageKey];

        if (registrationId)
        {
            completion(registrationId, nil);
            return;
        }

        // request new one & save
        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/api/register?handle=%@",
                                _endpoint, token]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200)
            {
                NSString* registrationId = [[NSString alloc] initWithData:data
                    encoding:NSUTF8StringEncoding];

                // remove quotes
                registrationId = [registrationId substringWithRange:NSMakeRange(1,
                                    [registrationId length]-2)];

                [[NSUserDefaults standardUserDefaults] setObject:registrationId
                    forKey:RegistrationIdLocalStorageKey];
                [[NSUserDefaults standardUserDefaults] synchronize];

                completion(registrationId, nil);
            }
            else
            {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(nil, error);
                else {
                    completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }

    @end
    ```

    Ten kod implementuje logikę szczegółowo opisane w artykule wskazówki [rejestrowania z zaplecza aplikacji](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) przy użyciu sesji NSURLSession do wykonania POZOSTAŁEJ wywołania do zaplecza aplikacji i NSUserDefaults lokalnie przechowywać identyfikator, zwrócone przez Centrum powiadomień.

    Ta klasa wymaga jego właściwość `authorizationHeader` należy ustawić, aby zapewnić prawidłowe działanie. Ta właściwość jest ustawiana przez `ViewController` klasy po logowaniu.

8. W `ViewController.h`, Dodaj `#import` poufności informacji dotyczące `RegisterClient.h`. Następnie dodaj deklarację dla tokenu urządzenia i odwołanie do `RegisterClient` wystąpienia w `@interface` sekcji:

    ```objc
    #import "RegisterClient.h"

    @property (strong, nonatomic) NSData* deviceToken;
    @property (strong, nonatomic) RegisterClient* registerClient;
    ```

9. W ViewController.m, Dodaj deklarację metody prywatnej w `@interface` sekcji:

    ```objc
    @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>

    // create the Authorization header to perform Basic authentication with your app back-end
    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;

    @end
    ```

    > [!NOTE]
    > Poniższy fragment kodu nie jest bezpieczne uwierzytelnianie, należy zastąpić implementację `createAndSetAuthenticationHeaderWithUsername:AndPassword:` przy użyciu mechanizmu uwierzytelniania określonych która generuje token uwierzytelniania do użycia przez rejestr klasy klienta, np. Protokołu OAuth usługi Active Directory.

10. Następnie w `@implementation` części `ViewController.m`, Dodaj następujący kod, który dodaje implementacji do ustawiania nagłówka tokenu i uwierzytelniania urządzenia.

    ```objc
    -(void) setDeviceToken: (NSData*) deviceToken
    {
        _deviceToken = deviceToken;
        self.LogInButton.enabled = YES;
    }

    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;
    {
        NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

        NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

        self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData
                                                    encoding:NSUTF8StringEncoding];
    }

    -(BOOL)textFieldShouldReturn:(UITextField *)textField
    {
        [textField resignFirstResponder];
        return YES;
    }
    ```

    Zwróć uwagę, jak przycisk Zaloguj umożliwia ustawienie tokenu urządzenia. Jest on, ponieważ w ramach działania logowania, kontroler widoku rejestruje dla powiadomień wypychanych przy użyciu zaplecza aplikacji. Z tego powodu nie chcesz, dziennika akcji była dostępna, aż token urządzenia został prawidłowo skonfigurowany. Logowanie z rejestracji wypychanych można oddzielić tak długo, jak pierwsza ma miejsce, przed jego.

11. W ViewController.m, użyj poniższe fragmenty kodu, aby zaimplementować metodę akcji dla Twojego **logowanie** przycisk i metodę, aby wysłać komunikatu powiadomienia przy użyciu zaplecza ASP.NET.

    ```objc
    - (IBAction)LogInAction:(id)sender {
        // create authentication header and set it in register client
        NSString* username = self.UsernameField.text;
        NSString* password = self.PasswordField.text;

        [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

        __weak ViewController* selfie = self;
        [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil
            andCompletion:^(NSError* error) {
            if (!error) {
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    selfie.SendNotificationButton.enabled = YES;
                    [self MessageBox:@"Success" message:@"Registered successfully!"];
                });
            }
        }];
    }

    - (void)SendNotificationASPNETBackend:(NSString*)pns UsernameTag:(NSString*)usernameTag
                Message:(NSString*)message
    {
        NSURLSession* session = [NSURLSession
            sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:nil
            delegateQueue:nil];

        // Pass the pns and username tag as parameters with the REST URL to the ASP.NET backend
        NSURL* requestURL = [NSURL URLWithString:[NSString
            stringWithFormat:@"%@/api/notifications?pns=%@&to_tag=%@", BACKEND_ENDPOINT, pns,
            usernameTag]];

        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];

        // Get the mock authenticationheader from the register client
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
            self.registerClient.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        //Add the notification message body
        [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
        [request setHTTPBody:[message dataUsingEncoding:NSUTF8StringEncoding]];

        // Execute the send notification REST API on the ASP.NET Backend
        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (error || httpResponse.statusCode != 200)
            {
                NSString* status = [NSString stringWithFormat:@"Error Status for %@: %d\nError: %@\n",
                                    pns, httpResponse.statusCode, error];
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    // Append text because all 3 PNS calls may also have information to view
                    [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];
                });
                NSLog(status);
            }

            if (data != NULL)
            {
                xmlParser = [[NSXMLParser alloc] initWithData:data];
                [xmlParser setDelegate:self];
                [xmlParser parse];
            }
        }];
        [dataTask resume];
    }
    ```

12. Aktualizowanie akcji dla **Wyślij powiadomienie E-mail** przycisk, aby korzystać z zaplecza programu ASP.NET i wysyłać do dowolnego systemu powiadomień platformy obsługiwane przez przełącznik.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        //[self SendNotificationRESTAPI];
        [self SendToEnabledPlatforms];
    }

    -(void)SendToEnabledPlatforms
    {
        NSString* json = [NSString stringWithFormat:@"\"%@\"",self.notificationMessage.text];

        [self.sendResults setText:@""];

        if ([self.WNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"wns" UsernameTag:self.RecipientField.text Message:json];

        if ([self.GCMSwitch isOn])
            [self SendNotificationASPNETBackend:@"gcm" UsernameTag:self.RecipientField.text Message:json];

        if ([self.APNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"apns" UsernameTag:self.RecipientField.text Message:json];
    }
    ```

13. W `ViewDidLoad` funkcji, Dodaj następujący kod do utworzenia wystąpienia `RegisterClient` wystąpienia i ustawić delegatów dla pól tekstu.

    ```objc
    self.UsernameField.delegate = self;
    self.PasswordField.delegate = self;
    self.RecipientField.delegate = self;
    self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```

14. Teraz w `AppDelegate.m`, Usuń całą zawartość metody `application:didRegisterForPushNotificationWithDeviceToken:` i zastąp go poniższym (tak, aby upewnić się, że kontroler widoku zawiera najnowszy token urządzenia pobierane z usługi APNs):

    ```objc
    // Add import to the top of the file
    #import "ViewController.h"

    - (void)application:(UIApplication *)application
                didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
        ViewController* rvc = (ViewController*) self.window.rootViewController;
        rvc.deviceToken = deviceToken;
    }
    ```

15. Na koniec w `AppDelegate.m`, upewnij się, że zainstalowano następującą metodę:

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

## <a name="test-the-application"></a>Testowanie aplikacji

1. W programie XCode Uruchom aplikację na urządzenie fizyczne z systemem iOS (wypychane, powiadomienia nie działają w symulatorze).
2. W aplikacji dla systemu iOS interfejsu użytkownika wprowadź tę samą wartość dla nazwy użytkownika i hasło. Następnie kliknij przycisk **logowanie**.

    ![iOS testowanie aplikacji][2]

3. Powinieneś zobaczyć okno podręczne informujące o powodzeniu rejestracji. Kliknij przycisk **OK**.

    ![iOS test wyświetlonego][3]

4. W **tagiem username odbiorcy* tekstu wprowadź tag nazwy użytkownika, które są używane z rejestracji za pomocą innego urządzenia.
5. Wprowadź komunikat powiadomienia, a następnie kliknij przycisk **Wyślij powiadomienie E-mail**. Tylko urządzenia, które ma rejestracji o tag nazwy użytkownika odbiorcy komunikat powiadomienia. Jest wysyłane tylko do tych użytkowników.

    ![oznakowane powiadomienie testowe dla systemu iOS][4]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób wysyłania powiadomień push do konkretnych użytkowników, którzy mają tagi skojarzone ze swoimi rejestracjami. Aby dowiedzieć się, jak wypychać powiadomienia oparte na lokalizacji, przejdź do następującego samouczka: 

> [!div class="nextstepaction"]
>[Wypychanie powiadomień na podstawie lokalizacji](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
