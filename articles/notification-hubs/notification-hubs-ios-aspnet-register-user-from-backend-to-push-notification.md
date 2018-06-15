---
title: Zarejestruj bieżącego użytkownika dla powiadomień wypychanych przy użyciu interfejsu API sieci Web | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć żądanie rejestracji powiadomień wypychanych w aplikacji systemu iOS za pomocą usługi Azure Notification Hubs, gdy rejestracja odbywa się za pośrednictwem interfejsu API sieci Web platformy ASP.NET.
services: notification-hubs
documentationcenter: ios
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: c43c15131afb5fbf346b0137dac566f5331c65a2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776376"
---
# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Zarejestruj bieżącego użytkownika dla powiadomień wypychanych przy użyciu platformy ASP.NET
> [!div class="op_single_selector"]
> * [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
> 
> 

## <a name="overview"></a>Przegląd
W tym temacie przedstawiono sposób żądania rejestracji powiadomień wypychanych przy użyciu usługi Azure Notification Hubs, gdy rejestracja odbywa się za pośrednictwem interfejsu API sieci Web platformy ASP.NET. W tym temacie rozszerza samouczka [powiadomić użytkowników z usługą Notification Hubs]. Należy zostały już wykonane kroki wymagane w tym samouczku można utworzyć uwierzytelnionego usługi mobilnej. Aby uzyskać więcej informacji od scenariusza, Powiadom użytkowników, zobacz [powiadomić użytkowników z usługą Notification Hubs].

## <a name="update-your-app"></a>Aktualizowanie aplikacji
1. W Twojej MainStoryboard_iPhone.storyboard Dodaj poniższe składniki z biblioteki obiektów:
   
   * **Etykieta**: "Wypychane do użytkowników z usługą Notification Hubs"
   * **Etykieta**: "Identyfikator InstallationId"
   * **Etykieta**: "Użytkownika"
   * **Pole tekstowe**: "Użytkownika"
   * **Etykieta**: "Password"
   * **Pole tekstowe**: "Password"
   * **Przycisk**: "Logowanie"
     
     W tym momencie z scenorysu wygląda następująco:
     
      ![][0]
2. W edytorze Asystenta tworzenia gniazda dla wszystkich kontrolek wyłączone i połączeń telefonicznych z nimi, pola tekstowego Uzyskuj dostęp do kontrolera widoku (delegat) i utworzyć **akcji** dla **logowania** przycisku.
   
       ![][1]
   
       Your BreakingNewsViewController.h file should now contain the following code:
   
        @property (weak, nonatomic) IBOutlet UILabel *installationId;
        @property (weak, nonatomic) IBOutlet UITextField *User;
        @property (weak, nonatomic) IBOutlet UITextField *Password;
   
        - (IBAction)login:(id)sender;
3. Utwórz klasę o nazwie **DeviceInfo**i skopiuj następujący kod do sekcji interfejsu pliku DeviceInfo.h:
   
        @property (readonly, nonatomic) NSString* installationId;
        @property (nonatomic) NSData* deviceToken;
4. Skopiuj następujący kod w sekcji implementacji pliku DeviceInfo.m:
   
            @synthesize installationId = _installationId;
   
            - (id)init {
                if (!(self = [super init]))
                    return nil;
   
                NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
                _installationId = [defaults stringForKey:@"PushToUserInstallationId"];
                if(!_installationId) {
                    CFUUIDRef newUUID = CFUUIDCreate(kCFAllocatorDefault);
                    _installationId = (__bridge_transfer NSString *)CFUUIDCreateString(kCFAllocatorDefault, newUUID);
                    CFRelease(newUUID);
   
                    //store the install ID so we don't generate a new one next time
                    [defaults setObject:_installationId forKey:@"PushToUserInstallationId"];
                    [defaults synchronize];
                }
   
                return self;
            }
   
            - (NSString*)getDeviceTokenInHex {
                const unsigned *tokenBytes = [[self deviceToken] bytes];
                NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
                                      ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                                      ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                                      ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
                return hexToken;
            }
5. W PushToUserAppDelegate.h Dodaj singleton następujące właściwości:
   
        @property (strong, nonatomic) DeviceInfo* deviceInfo;
6. W **didFinishLaunchingWithOptions** metody w PushToUserAppDelegate.m, Dodaj następujący kod:
   
        self.deviceInfo = [[DeviceInfo alloc] init];
   
        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
   
    Pierwszy wiersz inicjuje **DeviceInfo** singleton. Drugi wiersz rozpoczyna się rejestracja powiadomień wypychanych, który jest już wyświetlany, jeśli zostały już wykonane [Rozpoczynanie pracy z usługą Notification Hubs] samouczka.
7. W PushToUserAppDelegate.m, zaimplementuj metodę **didRegisterForRemoteNotificationsWithDeviceToken** w Twojej AppDelegate i Dodaj następujący kod:
   
        self.deviceInfo.deviceToken = deviceToken;
   
    To ustawienie token urządzenia dla żądania.
   
   > [!NOTE]
   > W tym momencie nie powinien zawierać dowolny kod w ramach tej metody. Jeśli masz już wywołanie **registerNativeWithDeviceToken** metody, które zostały dodane po wykonaniu [Rozpoczynanie pracy z usługą Notification Hubs](/manage/services/notification-hubs/get-started-notification-hubs-ios/) samouczka należy komentarz lub usunąć tego wywołania.
   > 
   > 
8. W pliku PushToUserAppDelegate.m Dodaj następującą metodę obsługi:
   
   * informacje o użytkowniku aplikacji (void):(UIApplication *) aplikacji didReceiveRemoteNotification:(NSDictionary *) {NSLog (@"% @", informacje o użytkowniku);   UIAlertView * alert = [[UIAlertView alokacji] initWithTitle:@"Notification" wiadomości: [objectForKey informacje o użytkowniku:@"inAppMessage"] cancelButtonTitle delegata: nil: @"OK" otherButtonTitles:nil, nil];   [Pokaż alert] }
   
   Ta metoda wyświetla alert w interfejsie użytkownika, gdy aplikacja odbiera powiadomienia jest uruchomiona.
9. Otwórz plik PushToUserViewController.m i zwracać klawiatury w implementacji następujących:
   
        - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
            if (theTextField == self.User || theTextField == self.Password) {
                [theTextField resignFirstResponder];
            }
            return YES;
        }
10. W **viewDidLoad** metody w pliku PushToUserViewController.m zainicjować etykiety installationId w następujący sposób:
    
         DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
         Self.installationId.text = deviceInfo.installationId;
11. W interfejsie w PushToUserViewController.m, należy dodać następujące właściwości:
    
        @property (readonly) NSOperationQueue* downloadQueue;
        - (NSString*)base64forData:(NSData*)theData;
12. Następnie należy dodać następujące wdrożenia:
    
            - (NSOperationQueue *)downloadQueue {
                if (!_downloadQueue) {
                    _downloadQueue = [[NSOperationQueue alloc] init];
                    _downloadQueue.name = @"Download Queue";
                    _downloadQueue.maxConcurrentOperationCount = 1;
                }
                return _downloadQueue;
            }
    
            // base64 encoding
            - (NSString*)base64forData:(NSData*)theData
            {
                const uint8_t* input = (const uint8_t*)[theData bytes];
                NSInteger length = [theData length];
    
                static char table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";
    
                NSMutableData* data = [NSMutableData dataWithLength:((length + 2) / 3) * 4];
                uint8_t* output = (uint8_t*)data.mutableBytes;
    
                NSInteger i;
                for (i=0; i < length; i += 3) {
                    NSInteger value = 0;
                    NSInteger j;
                    for (j = i; j < (i + 3); j++) {
                        value <<= 8;
    
                        if (j < length) {
                            value |= (0xFF & input[j]);
                        }
                    }
    
                    NSInteger theIndex = (i / 3) * 4;
                    output[theIndex + 0] =                    table[(value >> 18) & 0x3F];
                    output[theIndex + 1] =                    table[(value >> 12) & 0x3F];
                    output[theIndex + 2] = (i + 1) < length ? table[(value >> 6)  & 0x3F] : '=';
                    output[theIndex + 3] = (i + 2) < length ? table[(value >> 0)  & 0x3F] : '=';
                }
    
                return [[NSString alloc] initWithData:data encoding:NSASCIIStringEncoding];
            }
13. Skopiuj następujący kod do **logowania** metoda obsługi utworzone przez XCode:
    
            DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
    
            // build JSON
            NSString* json = [NSString stringWithFormat:@"{\"platform\":\"ios\", \"instId\":\"%@\", \"deviceToken\":\"%@\"}", deviceInfo.installationId, [deviceInfo getDeviceTokenInHex]];
    
            // build auth string
            NSString* authString = [NSString stringWithFormat:@"%@:%@", self.User.text, self.Password.text];
    
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"http://nhnotifyuser.azurewebsites.net/api/register"]];
            [request setHTTPMethod:@"POST"];
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
            [request addValue:[@([json lengthOfBytesUsingEncoding:NSUTF8StringEncoding]) description] forHTTPHeaderField:@"Content-Length"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request addValue:[NSString stringWithFormat:@"Basic %@",[self base64forData:[authString dataUsingEncoding:NSUTF8StringEncoding]]] forHTTPHeaderField:@"Authorization"];
    
            // connect with POST
            [NSURLConnection sendAsynchronousRequest:request queue:[self downloadQueue] completionHandler:^(NSURLResponse* response, NSData* data, NSError* error) {
                // add UIAlert depending on response.
                if (error != nil) {
                    NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                    if ([httpResponse statusCode] == 200) {
                        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
                        [alert show];
                    } else {
                        NSLog(@"status: %ld", (long)[httpResponse statusCode]);
                    }
                } else {
                    NSLog(@"error: %@", error);
                }
            }];
    
    Ta metoda pobiera zarówno identyfikator instalacji, jak i kanału dla powiadomień wypychanych i wysyła, wraz z typem urządzenia do uwierzytelnionego metody interfejsu API sieci Web, która tworzy rejestracji w usłudze Notification Hubs. Ten interfejs API sieci Web został zdefiniowany w [powiadomić użytkowników z usługą Notification Hubs].

Teraz, gdy aplikacja klienta została zaktualizowana, wróć do [powiadomić użytkowników z usługą Notification Hubs] i zaktualizuj usługi mobilnej w celu wysyłania powiadomień przy użyciu usługi Notification Hubs.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[powiadomić użytkowników z usługą Notification Hubs]: /manage/services/notification-hubs/notify-users-aspnet

[Rozpoczynanie pracy z usługą Notification Hubs]: /manage/services/notification-hubs/get-started-notification-hubs-ios
