---
title: Azure Mobile Engagement iOS SDK procedury uaktualniania | Dokumentacja firmy Microsoft
description: Najnowsze aktualizacje i procedury dla systemu iOS SDK dla usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 72a9e493-3f14-4e52-b6e2-0490fd04b184
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 12/13/2016
ms.author: piyushjo
ms.openlocfilehash: ac03593cf0c983297e583985a3e68ea6c17e39de
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="upgrade-procedures"></a>Procedury uaktualniania
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Jeśli już jest zintegrowany starszą wersję programu zaangażowania do aplikacji, należy wziąć pod uwagę następujące kwestie, podczas uaktualniania zestawu SDK.

Dla każdej nowej wersji zestawu SDK najpierw należy zastąpić (Usuń i ponownie zaimportować w programie xcode) EngagementSDK i EngagementReach folderów.

## <a name="from-300-to-400"></a>Z 3.0.0 do 4.0.0
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

### <a name="usernotifications-framework"></a>UserNotifications framework
Konieczne jest dodanie `UserNotifications` framework w poszczególnych faz kompilacji.

w obszarze Eksplorator projektów Otwórz okienko z projektu i wybierz docelowy o poprawnej. Następnie otwórz **"Fazy kompilacji"** kartę i w **"Binarny z bibliotekami"** menu Dodaj framework `UserNotifications.framework` -łącze jako `Optional`

### <a name="application-push-capability"></a>Możliwości wypychania aplikacji
XCode 8 może zresetować aplikacji push możliwości, dokładnie Sprawdź ten `capability` kartę wybranych docelowych.

### <a name="add-the-new-ios-10-notification-registration-code"></a>Dodaj nowy kod rejestracji powiadamiania iOS 10
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

### <a name="resolve-unusernotificationcenter-delegate-conflicts"></a>Rozwiązywanie konfliktów delegata UNUserNotificationCenter

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

## <a name="from-200-to-300"></a>Z 2.0.0 do 3.0.0
Obsługę systemu iOS usunąć 4.X. Począwszy od tej wersji cel wdrożenia aplikacji musi mieć co najmniej iOS 6.

Jeśli używasz Reach w aplikacji, należy dodać `remote-notification` do wartości `UIBackgroundModes` tablicy w pliku Info.plist, aby otrzymywać powiadomienia zdalnego.

Metoda `application:didReceiveRemoteNotification:` musi zostać zastąpiona `application:didReceiveRemoteNotification:fetchCompletionHandler:` w delegata aplikacji.

"AEPushDelegate.h" jest przestarzała i interfejsu należy usunąć wszystkie odwołania. Dotyczy to również usunięcie `[[EngagementAgent shared] setPushDelegate:self]` i metody obiektu delegowanego z delegata aplikacji:

    -(void)willRetrieveLaunchMessage;
    -(void)didFailToRetrieveLaunchMessage;
    -(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

## <a name="from-1160-to-200"></a>Z 1.16.0 do 2.0.0
Poniżej opisano sposób migracji integracji zestawu SDK usługi Capptain oferowane przez Capptain SAS w aplikacji obsługiwane przez usługę Azure Mobile Engagement.
W przypadku migracji z wcześniejszej wersji, przejrzyj witrynę sieci web Capptain migracji 1.16 najpierw, a następnie zastosować następującą procedurę.

> [!IMPORTANT]
> Capptain i usługi Mobile Engagement nie są takie same usługi i procedury przedstawionej poniżej tylko prezentuje sposób migracji aplikacji klienckiej. Migrowanie zestawu SDK w aplikacji wiadomości nie będzie migrację danych z serwerów Capptain do serwerów usługi Mobile Engagement
> 
> 

### <a name="agent"></a>Agent
Metoda `registerApp:` został zastąpiony nową metodą `init:`. Delegata aplikacji należy zaktualizować odpowiednio i użyj parametrów połączenia:

            - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
            {
              [...]
              [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
              [...]
            }

Śledzenie SmartAd został usunięty z zestawu SDK, należy usunąć wszystkie wystąpienia `AETrackModule` — klasa

### <a name="class-name-changes"></a>Zmiany nazwy klasy
W ramach rebranding istnieje kilka nazw klasy i plików, które muszą zostać zmienione.

Wszystkie klasy i jest poprzedzony prefiksem "CP" zostały zmienione z prefiksem "AE".

Przykład:

* `CPModule.h` została zmieniona na `AEModule.h`.

Wszystkie klasy i jest poprzedzony prefiksem "Capptain" zostały zmienione z prefiksem "Zaangażowania".

Przykłady:

* Klasa `CapptainAgent` została zmieniona na `EngagementAgent`.
* Klasa `CapptainTableViewController` została zmieniona na `EngagementTableViewController`.
* Klasa `CapptainUtils` została zmieniona na `EngagementUtils`.
* Klasa `CapptainViewController` została zmieniona na `EngagementViewController`.

