---
title: Wysyłanie powiadomień push do aplikacji dla systemu iOS przy użyciu usługi Azure Notification Hubs | Microsoft Docs
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
ms.date: 05/21/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 0335f5c71f99e6c7a90ce920c25e6bb7e9b4a08f
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211948"
---
# <a name="tutorial-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Samouczek: wysyłanie powiadomień push do aplikacji dla systemu iOS przy użyciu usługi Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)


Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia push do aplikacji dla systemu iOS przy użyciu usługi Azure Notification Hubs. Przy użyciu usługi [Apple Push Notification Service (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1) utworzysz pustą aplikację dla systemu iOS służącą do odbierania powiadomień push.

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Generowanie pliku żądania podpisania certyfikatu
> * Rejestrowanie aplikacji dla usługi powiadomień wypychanych
> * Tworzenie profilu inicjowania obsługi dla aplikacji
> * Konfigurowanie centrum powiadomień dla powiadomień push systemu iOS
> * Łączenie aplikacji dla systemu iOS z usługą Notification Hubs
> * Wysyłanie testowych powiadomień wypychanych
> * Sprawdzanie, czy aplikacja odbiera powiadomienia

Kompletny kod dla tego samouczka można znaleźć [w witrynie GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/master/Samples). 

## <a name="prerequisites"></a>Wymagania wstępne

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz [utworzyć bezpłatne konto platformy Azure](https://azure.microsoft.com/free) w zaledwie kilka minut.
* [Struktura Windows Azure Messaging]
* Najnowsza wersja środowiska [Xcode]
* Urządzenie zgodne z systemem iOS 10 (lub nowszą wersją)
* Członkostwo w [programie dla deweloperów firmy Apple](https://developer.apple.com/programs/).
  
  > [!NOTE]
  > Ze względu na wymagania dotyczące konfiguracji powiadomień wypychanych należy wdrożyć i przetestować powiadomienia wypychane na fizycznym urządzeniu z systemem iOS (telefonie iPhone lub tablecie iPad), a nie w symulatorze systemu iOS.
  
Wykonanie czynności opisanych w tym samouczku jest wymaganiem wstępnym dla wszystkich innych samouczków usługi Notification Hubs dotyczących aplikacji dla systemu iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-notification-hubs"></a>Łączenie aplikacji dla systemu iOS z usługą Notification Hubs

1. W środowisku Xcode utwórz nowy projekt dla systemu iOS i wybierz szablon **Single View Application** (Aplikacja z jednym widokiem).

    ![Xcode — aplikacja z jednym widokiem][8]

2. Podczas określania opcji dla nowego projektu upewnij się, że użyto tych samych wartości **Product Name** (Nazwa produktu) i **Organization Identifier** (Identyfikator organizacji), które zostały użyte podczas ustawiania identyfikatora pakietu w portalu dla deweloperów firmy Apple.

    ![Xcode — opcje projektu][11]

3. W obszarze Project Navigator (Nawigator projektów) kliknij nazwę swojego projektu, kliknij kartę **General** (Ogólne) i wyszukaj pozycję **Signing** (Podpisywanie). Upewnij się, że wybrano odpowiedni zespół dla konta dewelopera firmy Apple. Środowisko Xcode powinno automatycznie ściągnąć utworzony wcześniej profil aprowizowania na podstawie identyfikatora pakietu.

    Jeśli nowy profil aprowizowania utworzony w programie Xcode nie jest widoczny, odśwież profile dla Twojej tożsamości podpisywania. Kliknij pozycję **Xcode** na pasku menu, kliknij pozycję **Preferences** (Preferencje), kliknij kartę **Account** (Konto), kliknij przycisk **View Details** (Pokaż szczegóły), kliknij tożsamość podpisywania, a następnie kliknij przycisk odświeżania w prawym dolnym rogu.

    ![Xcode — profil aprowizowania][9]

4. Wybierz kartę **Capabilities** (Możliwości) i upewnij się, że powiadomienia push są włączone.

    ![Xcode — możliwości push][12]

5. Dodaj moduły zestawu Azure Notification Hubs SDK.

   Zestaw SDK usługi Azure Notification Hubs można zintegrować z aplikacją za pomocą [Cocoapods](https://cocoapods.org) lub ręcznie dodając pliki binarne do projektu.

   - Integracja za pośrednictwem Cocoapods

     Dodaj następujące zależności do `podfile` usługi, aby dołączyć do aplikacji usługę Azure Notification Hubs SDK.

     ```ruby
     pod 'AzureNotificationHubs-iOS'
     ```

     Uruchom `pod install` , aby zainstalować nowo zdefiniowany pod i `.xcworkspace`otworzyć.

     > [!NOTE]
     > ```[!] Unable to find a specification for `AzureNotificationHubs-iOS` ``` Jeśli zobaczysz błąd podobny do uruchomionego `pod install`, uruchom `pod repo update` polecenie, aby uzyskać najnowsze Zasobniki z repozytorium Cocoapods, a następnie `pod install`Uruchom polecenie.

   - Integracja za pośrednictwem Carthage

     Dodaj następujące zależności do `Cartfile` usługi, aby dołączyć do aplikacji usługę Azure Notification Hubs SDK.

     ```ruby
     github "Azure/azure-notificationhubs-ios"
     ```

     Zależności Next, Update i Build:

     ```shell
     $ carthage update
     ```

     Aby uzyskać więcej informacji na temat korzystania z programu Carthage, zobacz [repozytorium GitHub Carthage](https://github.com/Carthage/Carthage).

   - Integracja przez kopiowanie plików binarnych do projektu

     1. Pobierz platformę [zestawu SDK usługi Azure Notification Hubs](https://github.com/Azure/azure-notificationhubs-ios/releases) w postaci pliku zip i rozpakuj ją.

     2. W programie Xcode kliknij prawym przyciskiem myszy projekt i kliknij opcję **Add Files to** (Dodaj pliki do), aby dodać folder **WindowsAzureMessaging.framework** do projektu Xcode. Wybierz pozycję **Options** (Opcje), upewnij się, że pozycja **Copy items if needed** (Skopiuj elementy w razie potrzeby) jest zaznaczona, a następnie kliknij pozycję **Add** (Dodaj).

        ![Rozpakowywanie zestawu SDK platformy Azure][10]

6. Dodaj nowy plik nagłówkowy do projektu o nazwie `HubInfo.h`. Ten plik zawiera stałe używane przez centrum powiadomień. Dodaj następujące definicje i zastąp symbole zastępcze literału ciągu przy użyciu *nazwy centrum* i zanotowanej wcześniej wartości *DefaultListenSharedAccessSignature*.

    ```objc
    #ifndef HubInfo_h
    #define HubInfo_h

        #define HUBNAME @"<Enter the name of your hub>"
        #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"

    #endif /* HubInfo_h */
    ```

7. Otwórz plik `AppDelegate.h` i dodaj następujące dyrektywy importu:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h>
    #import "HubInfo.h"
    ```
8. W pliku `AppDelegate.m` dodaj następujący kod w metodzie `didFinishLaunchingWithOptions` zależnie od wersji systemu iOS. Ten kod rejestruje dojście do urządzenia w usłudze APNs:

    ```objc
    UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
        UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

    [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    [[UIApplication sharedApplication] registerForRemoteNotifications];
    ```

9. W tym samym pliku dodaj następujące metody:

    ```objc
        - (void) application:(UIApplication *) application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
        SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                    notificationHubPath:HUBNAME];

        [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
            else {
                [self MessageBox:@"Registration Status" message:@"Registered"];
            }
        }];
        }

    -(void)MessageBox:(NSString *) title message:(NSString *)messageText
    {
        UIAlertController *alert = [UIAlertController alertControllerWithTitle:title message:messageText preferredStyle:UIAlertControllerStyleAlert];
        UIAlertAction *okAction = [UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:nil];
        [alert addAction:okAction];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:alert animated:YES completion:nil];
    }
    ```

    Ten kod łączy się z centrum powiadomień przy użyciu informacji o połączeniu określonych w pliku HubInfo.h. Następnie przekazuje token urządzenia do centrum powiadomień, aby umożliwić wysyłanie powiadomień.

10. W tym samym pliku dodaj następującą metodę służącą do wyświetlania elementu **UIAlert** w przypadku otrzymania powiadomienia, gdy aplikacja jest aktywna:

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

11. Skompiluj i uruchom aplikację na urządzeniu, aby upewnić się, że nie występują żadne błędy.

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
>[Wysyłanie powiadomień push do konkretnych urządzeń](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png
[12]: ./media/notification-hubs-ios-get-started/notification-hubs-enable-push.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
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
[Azure Portal]: https://portal.azure.com
