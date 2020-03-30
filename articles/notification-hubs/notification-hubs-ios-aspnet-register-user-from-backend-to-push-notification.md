---
title: Zarejestruj bieżącego użytkownika do powiadomień wypychanych za pomocą interfejsu API sieci Web | Dokumenty firmy Microsoft
description: Dowiedz się, jak żądać rejestracji powiadomień wypychanych w aplikacji systemu iOS za pomocą usługi Azure Notification Hubs podczas rejestracji wykonywanej przez ASP.NET interfejsu API sieci Web.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 3fec04a1a45f8b154e27a1e5303e44111f4cb421
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71211876"
---
# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Zarejestruj bieżącego użytkownika do powiadomień wypychanych za pomocą ASP.NET

> [!div class="op_single_selector"]
> * [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)

## <a name="overview"></a>Omówienie

W tym temacie pokazano, jak zażądać rejestracji powiadomień wypychanych w usłudze Azure Notification Hubs, gdy rejestracja jest wykonywana przez ASP.NET interfejsu API sieci Web. W tym temacie rozszerza samouczek [Powiadom użytkowników o Centra powiadomień]. Aby utworzyć uwierzytelnioną usługę mobilną, musisz już wykonać wymagane kroki w tym samouczku. Aby uzyskać więcej informacji na temat scenariusza powiadamiania użytkowników, zobacz [Powiadamianie użytkowników za pomocą Centrów powiadomień].

## <a name="update-your-app"></a>Aktualizowanie aplikacji

1. W MainStoryboard_iPhone.storyboard dodaj następujące składniki z biblioteki obiektów:

   * **Etykieta**: "Wypychanie do użytkownika za pomocą centrów powiadomień"
   * **Etykieta**: "InstallationId"
   * **Etykieta**: "Użytkownik"
   * **Pole tekstowe:**"Użytkownik"
   * **Etykieta**: "Hasło"
   * **Pole tekstowe:**"Hasło"
   * **Przycisk**: "Zaloguj się"

     W tym momencie scenorys wygląda następująco:

     ![][0]

2. W edytorze asystenta utwórz gniazda dla wszystkich przełączonych formantów i zadzwoń do nich, połącz pola tekstowe z kontrolerem widoku (pełnomocnik) i utwórz **akcję** dla przycisku **logowania.**

    ![][1]

    Plik BreakingNewsViewController.h powinien teraz zawierać następujący kod:

    ```objc
    @property (weak, nonatomic) IBOutlet UILabel *installationId;
    @property (weak, nonatomic) IBOutlet UITextField *User;
    @property (weak, nonatomic) IBOutlet UITextField *Password;

    - (IBAction)login:(id)sender;
    ```
3. Utwórz klasę `DeviceInfo`o nazwie i skopiuj następujący kod do sekcji interfejsu pliku DeviceInfo.h:

    ```objc
    @property (readonly, nonatomic) NSString* installationId;
    @property (nonatomic) NSData* deviceToken;
    ```
4. Skopiuj następujący kod w sekcji implementacji pliku DeviceInfo.m:

    ```objc
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
    ```

5. W pliku PushToUserAppDelegate.h dodaj następującą właściwość singleton:

    ```objc
    @property (strong, nonatomic) DeviceInfo* deviceInfo;
    ```
6. W `didFinishLaunchingWithOptions` metodzie w PushToUserAppDelegate.m dodaj następujący kod:

    ```objc
    self.deviceInfo = [[DeviceInfo alloc] init];

    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
    ```

    Pierwszy wiersz inicjuje `DeviceInfo` singleton. Drugi wiersz rozpoczyna rejestrację powiadomień wypychanych, która jest już obecna, jeśli zostało już ukończone samouczek [Wprowadzenie do powiadomień z centrum powiadomień.]
7. W aplikacji PushToUserAppDelegate.m `didRegisterForRemoteNotificationsWithDeviceToken` zaimplementuj metodę w aplikacji AppDelegate i dodaj następujący kod:

    ```objc
    self.deviceInfo.deviceToken = deviceToken;
    ```

    Spowoduje to ustawienie tokenu urządzenia dla żądania.

   > [!NOTE]
   > W tym momencie nie powinno być żadnego innego kodu w tej metodzie. Jeśli masz już wywołanie `registerNativeWithDeviceToken` metody, która została dodana po zakończeniu samouczek [Wprowadzenie do centrów powiadomień,](notification-hubs-ios-apple-push-notification-apns-get-started.md) musisz skomentować lub usunąć to wywołanie.

8. W `PushToUserAppDelegate.m` pliku dodaj następującą metodę obsługi:

    ```objc
    * (void) application:(UIApplication *) application didReceiveRemoteNotification:(NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                             [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
                             @"OK" otherButtonTitles:nil, nil];
       [alert show];
    }
    ```

    Ta metoda wyświetla alert w interfejsie użytkownika, gdy aplikacja odbiera powiadomienia, gdy jest uruchomiona.

9. Otwórz `PushToUserViewController.m` plik i zwróć klawiaturę w następującej implementacji:

    ```objc
    - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
        if (theTextField == self.User || theTextField == self.Password) {
            [theTextField resignFirstResponder];
        }
        return YES;
    }
    ```

10. W `viewDidLoad` metodzie `PushToUserViewController.m` w pliku zaiwówniaj etykietę w `installationId` następujący sposób:

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
    Self.installationId.text = deviceInfo.installationId;
    ```

11. Dodaj następujące właściwości w `PushToUserViewController.m`interfejsie w:

    ```objc
    @property (readonly) NSOperationQueue* downloadQueue;
    - (NSString*)base64forData:(NSData*)theData;
    ```

12. Następnie dodaj następującą implementację:

    ```objc
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
    ```

13. Skopiuj `login` następujący kod do metody obsługi utworzonej przez XCode:

    ```objc
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
    ```

    Ta metoda pobiera identyfikator instalacji i kanał powiadomień wypychanych i wysyła go, wraz z typem urządzenia, do uwierzytelnionej metody interfejsu API sieci Web, która tworzy rejestrację w Centrach powiadomień. Ten internetowy interfejs API został zdefiniowany w [obszarze Powiadamianie użytkowników za pomocą centrów powiadomień].

Teraz, gdy aplikacja kliencka została zaktualizowana, wróć do [powiadamiania użytkowników za pomocą centrów powiadomień] i zaktualizuj usługę mobilną, aby wysyłać powiadomienia za pomocą Centrów powiadomień.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[Powiadamianie użytkowników za pomocą Centrów powiadomień]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Wprowadzenie do centrów powiadomień]: notification-hubs-ios-apple-push-notification-apns-get-started.md
