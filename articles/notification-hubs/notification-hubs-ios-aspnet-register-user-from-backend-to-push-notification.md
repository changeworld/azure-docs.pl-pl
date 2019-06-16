---
title: Rejestrowanie bieżącego użytkownika dla powiadomień wypychanych przy użyciu interfejsu API sieci Web | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć żądanie rejestracji powiadomień wypychanych w aplikacji systemu iOS z usługą Azure Notification Hubs, gdy rejestracja odbywa się przez interfejs API sieci Web platformy ASP.NET.
services: notification-hubs
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: ff77a955c34941d87a1f653726ab3f19e84aa440
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61458352"
---
# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Rejestrowanie bieżącego użytkownika dla powiadomień wypychanych za pomocą programu ASP.NET

> [!div class="op_single_selector"]
> * [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)

## <a name="overview"></a>Omówienie

W tym temacie dowiesz się, jak utworzyć żądanie rejestracji powiadomień wypychanych przy użyciu usługi Azure Notification Hubs, gdy rejestracja odbywa się przez interfejs API sieci Web platformy ASP.NET. Ten temat rozszerza samouczka [powiadamianie użytkowników za pomocą usługi Notification Hubs]. Należy zostały już wykonane kroki wymagane w tym samouczku, aby utworzyć usługę mobilną uwierzytelniony. Aby uzyskać więcej informacji od scenariusza, Powiadom użytkowników, zobacz [powiadamianie użytkowników za pomocą usługi Notification Hubs].

## <a name="update-your-app"></a>Zaktualizuj aplikację

1. W swojej MainStoryboard_iPhone.storyboard Dodaj następujące składniki z biblioteki obiektów:

   * **Etykieta**: "Push do użytkowników z usługą Notification Hubs"
   * **Etykieta**: "InstallationId"
   * **Etykieta**: "Użytkownik"
   * **Pole tekstowe**: "Użytkownik"
   * **Etykieta**: "Password"
   * **Pole tekstowe**: "Password"
   * **Przycisk**: "Logowanie"

     W tym momencie scenorysu wygląda podobnie do poniższego:

     ![][0]

2. W edytorze Asystenta Tworzenie gniazda dla wszystkich kontrolek przełączono stan i ich wywoływania, Łączenie pól tekstowych przy użyciu kontrolera widoku (delegat) i utworzyć **akcji** dla **logowania** przycisku.

    ![][1]

    Plik BreakingNewsViewController.h teraz powinien zawierać następujący kod:

    ```objc
    @property (weak, nonatomic) IBOutlet UILabel *installationId;
    @property (weak, nonatomic) IBOutlet UITextField *User;
    @property (weak, nonatomic) IBOutlet UITextField *Password;

    - (IBAction)login:(id)sender;
    ```
3. Utwórz klasę o nazwie `DeviceInfo`i skopiuj następujący kod do sekcji interfejs pliku DeviceInfo.h:

    ```objc
    @property (readonly, nonatomic) NSString* installationId;
    @property (nonatomic) NSData* deviceToken;
    ```
4. Skopiuj poniższy kod w sekcji implementacji pliku DeviceInfo.m:

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

5. W PushToUserAppDelegate.h Dodaj następujące właściwości pojedyncze wystąpienie:

    ```objc
    @property (strong, nonatomic) DeviceInfo* deviceInfo;
    ```
6. W `didFinishLaunchingWithOptions` metody w PushToUserAppDelegate.m, Dodaj następujący kod:

    ```objc
    self.deviceInfo = [[DeviceInfo alloc] init];

    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
    ```

    Inicjuje pierwszy wiersz `DeviceInfo` pojedynczego wystąpienia. Drugi wiersz rozpoczyna się rejestracja powiadomień wypychanych, który już jest obecny, jeśli zostały już wykonane [Rozpoczynanie pracy z usługą Notification Hubs] samouczka.
7. W pliku PushToUserAppDelegate.m, zaimplementuj metodę `didRegisterForRemoteNotificationsWithDeviceToken` w elemencie AppDelegate i Dodaj następujący kod:

    ```objc
    self.deviceInfo.deviceToken = deviceToken;
    ```

    Spowoduje to ustawienie tokenu urządzenia dla żądania.

   > [!NOTE]
   > W tym momencie nie powinno być innymi kodami w przypadku tej metody. Jeśli masz już wywołanie `registerNativeWithDeviceToken` metodę, która została dodana po zakończeniu [Rozpoczynanie pracy z usługą Notification Hubs](notification-hubs-ios-apple-push-notification-apns-get-started.md) samouczek, musisz komentarz lub usuń to wywołanie.

8. W `PushToUserAppDelegate.m` plików, dodaj następującą metodę programu obsługi:

    ```objc
    * (void) application:(UIApplication *) application didReceiveRemoteNotification:(NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                             [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
                             @"OK" otherButtonTitles:nil, nil];
       [alert show];
    }
    ```

    Ta metoda wyświetla alert w interfejsie użytkownika, gdy aplikacja otrzymuje powiadomienia, jest uruchomiona.

9. Otwórz `PushToUserViewController.m` pliku, a następnie wróć klawiatury w poniższej implementacji:

    ```objc
    - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
        if (theTextField == self.User || theTextField == self.Password) {
            [theTextField resignFirstResponder];
        }
        return YES;
    }
    ```

10. W `viewDidLoad` method in Class metoda `PushToUserViewController.m` plików, inicjowanie `installationId` etykietę w następujący sposób:

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
    Self.installationId.text = deviceInfo.installationId;
    ```

11. Dodaj następujące właściwości w interfejsie w `PushToUserViewController.m`:

    ```objc
    @property (readonly) NSOperationQueue* downloadQueue;
    - (NSString*)base64forData:(NSData*)theData;
    ```

12. Następnie należy dodać następującą implementacją:

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

13. Skopiuj następujący kod do `login` metody obsługi utworzone przez program XCode:

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

    Ta metoda pobiera zarówno identyfikator instalacji, jak i kanał dla powiadomień wypychanych i wysyła, oraz typ urządzenia do uwierzytelnionego metody interfejsu API sieci Web, która tworzy rejestracji w usłudze Notification Hubs. Ten interfejs API sieci Web został zdefiniowany w [powiadamianie użytkowników za pomocą usługi Notification Hubs].

Teraz, gdy aplikacja klienta została zaktualizowana, wróć do [powiadamianie użytkowników za pomocą usługi Notification Hubs] i zaktualizuj usługę mobilną do wysyłania powiadomień przy użyciu usługi Notification Hubs.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[Powiadamianie użytkowników za pomocą usługi Notification Hubs]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Rozpoczynanie pracy z usługą Notification Hubs]: notification-hubs-ios-apple-push-notification-apns-get-started.md
