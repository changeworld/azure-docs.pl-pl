---
title: Azure Mobile Engagement iOS SDK omówienie | Dokumentacja firmy Microsoft
description: Najnowsze aktualizacje i procedury dla systemu iOS SDK dla usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 3a03bbd6-bcf8-436c-9775-5a8188629252
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 6c9d2ddeacd1d05142019f22eedf4eb3bb4f06cd
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="ios-sdk-for-azure-mobile-engagement"></a>Zestaw iOS SDK dla usługi Azure Mobile Engagement
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Zacznij tutaj uzyskać szczegółowe informacje na temat integracji Azure Mobile Engagement w aplikacji systemu iOS. Jeśli chcesz go wypróbować najpierw, upewnij się, jak naszym [samouczek 15 minut](mobile-engagement-ios-get-started.md).

Kliknij, aby wyświetlić [zawartość zestawu SDK](mobile-engagement-ios-sdk-content.md)

## <a name="integration-procedures"></a>Procedury integracji
1. Zacznij tutaj: [jak zintegrowana usługa Mobile Engagement w aplikacji systemu iOS](mobile-engagement-ios-integrate-engagement.md)
2. Dla powiadomień: [integrowanie Reach (powiadomienia) w aplikacji systemu iOS](mobile-engagement-ios-integrate-engagement-reach.md)
3. Tag plan implementacji: [sposobu korzystania z zaawansowanych Mobile Engagement znakowanie interfejsu API w aplikacji systemu iOS](mobile-engagement-ios-use-engagement-api.md)

## <a name="release-notes"></a>Informacje o wersji
### <a name="410-07172017"></a>4.1.0 (07/17/2017)
* Identyfikatory stałych wyczyszczone w tle.
* Stałe ostrzeżenia na 9 XCode o interfejsach API nie jest wywoływana w kolejki głównej.
* Stała sond Reach przeciek pamięci.
* Obsługę systemu iOS usunąć 6.X. Począwszy od tej wersji cel wdrożenia aplikacji musi mieć co najmniej system iOS 7.

Dla starszych wersji zobacz [ukończyć informacje o wersji](mobile-engagement-ios-release-notes.md)

## <a name="upgrade-procedures"></a>Procedury uaktualniania
Jeśli już jest zintegrowany starszą wersję programu zaangażowania do aplikacji, należy wziąć pod uwagę następujące kwestie, podczas uaktualniania zestawu SDK.

Może być konieczne wykonaj kilka procedur, jeśli pominięte kilka wersji można znaleźć zestawu SDK pełną [procedur uaktualniania](mobile-engagement-ios-upgrade-procedure.md).

Dla każdej nowej wersji zestawu SDK najpierw należy zastąpić (Usuń i ponownie zaimportować w programie xcode) EngagementSDK i EngagementReach folderów.

### <a name="from-300-to-400"></a>Z 3.0.0 do 4.0.0
### <a name="xcode-8"></a>XCode 8
XCode 8 jest obowiązkowy, począwszy od wersji 4.0.0 zestawu SDK.

> [!NOTE]
> Jeśli naprawdę są zależne od XCode 7, możesz użyć [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh). Jest znaną usterką w moduł reach tej poprzedniej wersji podczas uruchamiania na urządzeniach z systemem iOS 10: powiadomień systemowych nie są których wykonano akcje. Rozwiązać problem, należy zaimplementować przestarzałe interfejsu API `application:didReceiveRemoteNotification:` w aplikacji delegowanie w następujący sposób:
>
>

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> **Nie zaleca się rozwiązanie** jak to zachowanie można zmienić w jakiegokolwiek uaktualnienia wersji nadchodzących iOS (nawet pomocnicze), ponieważ ten iOS interfejsu API jest przestarzały. Powinna przebiegać XCode 8 tak szybko, jak to możliwe.
>
>

#### <a name="usernotifications-framework"></a>UserNotifications framework
Konieczne jest dodanie `UserNotifications` framework w poszczególnych faz kompilacji.

w obszarze Eksplorator projektów Otwórz okienko z projektu i wybierz docelowy o poprawnej. Następnie otwórz **"Fazy kompilacji"** kartę i w **"Binarny z bibliotekami"** menu Dodaj framework `UserNotifications.framework` -łącze jako `Optional`

#### <a name="application-push-capability"></a>Możliwości wypychania aplikacji
XCode 8 może zresetować aplikacji push możliwości, dokładnie Sprawdź ten `capability` kartę wybranych docelowych.

#### <a name="add-the-new-ios-10-notification-registration-code"></a>Dodaj nowy kod rejestracji powiadamiania iOS 10
Starsze fragmentu kodu do rejestrowania aplikacji powiadomień nadal działa, ale podczas uruchamiania w systemie iOS 10 przy użyciu interfejsów API przestarzałe.

Importuj `User Notification` framework:

        #import <UserNotifications/UserNotifications.h>

W delegata aplikacji `application:didFinishLaunchingWithOptions` Zastąp metodę:

        if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
            [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
            [application registerForRemoteNotifications];
        }
        else {

            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

przez:

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

#### <a name="resolve-unusernotificationcenter-delegate-conflicts"></a>Rozwiązywanie konfliktów delegata UNUserNotificationCenter

*Jeśli implementuje jednej z bibliotek innych firm ani aplikacji `UNUserNotificationCenterDelegate` , a następnie można pominąć tej części.*

A `UNUserNotificationCenter` delegata jest używana przez zestaw SDK do monitorowania cyklu życia zaangażowania powiadomienia na urządzeniach z systemem iOS, 10 lub nowszy. Zestaw SDK ma własną implementację `UNUserNotificationCenterDelegate` protokołu, ale może istnieć tylko jedna `UNUserNotificationCenter` delegować na aplikację. Inne delegata dodane do `UNUserNotificationCenter` obiektu może powodować konflikt z zaangażowania, jeden. Jeśli zestaw SDK wykryje delegata użytkownika lub dowolnej innej strony trzeciej firmy nie będzie używać własną implementację daje możliwość Rozwiąż konflikty. Należy dodać logikę zaangażowania do własnych delegata, aby rozwiązać konflikty.

Istnieją dwa sposoby można to osiągnąć.

Wniosek 1, po prostu przekazywania pełnomocnika wywołania do zestawu SDK:

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

Lub propozycji 2, dziedziczenie z `AEUserNotificationHandler` — klasa

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [!NOTE]
> Można określić, czy powiadomienie jest dostarczany z zaangażowania lub nie przez przekazanie jej `userInfo` słownika do agenta `isEngagementPushPayload:` metoda klasy.

Upewnij się, że `UNUserNotificationCenter` obiektu delegowanego ustawiono pełnomocnika albo `application:willFinishLaunchingWithOptions:` lub `application:didFinishLaunchingWithOptions:` metody delegata aplikacji.
Na przykład jeśli zaimplementowano wniosek powyżej 1:

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        // Any other code

        [UNUserNotificationCenter currentNotificationCenter].delegate = self;
        return YES;
      }
