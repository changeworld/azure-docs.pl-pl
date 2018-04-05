---
title: Wprowadzenie do usługi Azure Mobile Engagement dla systemu iOS w języku Objective C | Microsoft Docs
description: Dowiedz się, jak używać usługi Azure Mobile Engagement razem z funkcją analizy i powiadomieniami wypychanymi na potrzeby aplikacji systemu iOS.
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 117b5742-522b-41de-98c5-f432da2d98f8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 7cf493e3a4878c64b0ea6a74b6e16329ce3d27b5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-objective-c"></a>Wprowadzenie do usługi Azure Mobile Engagement dla aplikacji systemu iOS w języku Objective C
> [!IMPORTANT]
> Usługa Azure Mobile Engagement zostanie wycofana w dniu 31-03-2018. Ta strona zostanie usunięta wkrótce potem.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

W tym temacie pokazano, jak za pomocą usługi Azure Mobile Engagement określać użycie aplikacji i wysyłać powiadomienia wypychane do danego segmentu użytkowników aplikacji systemu iOS.
W tym samouczku zostanie utworzona pusta aplikacja systemu iOS służąca do zbierania podstawowych danych i odbierania powiadomień wypychanych przy użyciu systemu Apple Push Notification System (APNS).

Dla tego samouczka wymagane są następujące elementy:

* Edytor XCode 8, który można zainstalować ze sklepu Mac App Store
* Zestaw [Mobile Engagement iOS SDK]

Wykonanie czynności opisanych w tym samouczku jest wymaganiem wstępnym dla wszystkich innych samouczków usługi Mobile Engagement dla aplikacji systemu iOS.

> [!NOTE]
> Do wykonania kroków tego samouczka potrzebne jest aktywne konto platformy Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-get-started).
>
>

## <a id="setup-azme"></a>Konfigurowanie usługi Mobile Engagement dla aplikacji systemu iOS
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Łączenie aplikacji z zapleczem usługi Mobile Engagement
Ten samouczek przedstawia „podstawową integrację”, tj. minimalny zestaw wymagany do zbierania danych i wysyłania powiadomień wypychanych. Kompletna dokumentacja integracji znajduje się w sekcji [Mobile Engagement iOS SDK integration](mobile-engagement-ios-sdk-overview.md) (Integracja zestawu Mobile Engagement iOS SDK).

Aby zademonstrować integrację, utworzymy podstawową aplikację za pomocą edytora XCode.

### <a name="create-a-new-ios-project"></a>Tworzenie nowego projektu systemu iOS
[!INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

### <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Łączenie aplikacji z zapleczem usługi Mobile Engagement
1. Pobierz zestaw [Mobile Engagement iOS SDK].
2. Wyodrębnij plik tar.gz do folderu na swoim komputerze.
3. Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Add files to** (Dodaj pliki do).

    ![][1]

4. Przejdź do folderu, w którym został wyodrębniony zestaw SDK, wybierz folder `EngagementSDK` i kliknij pozycję **Opcje** w lewym dolnym rogu. Następnie upewnij się, że pole wyboru **Copy items if needed** (Kopiuj elementy w razie potrzeby) i pole wyboru Twojego miejsca docelowego są zaznaczone, po czym naciśnij przycisk **OK**.

    ![][2]

5. Otwórz kartę **Build Phases** (Fazy kompilacji) i w menu **Link Binary With Libraries** (Połącz plik binarny z bibliotekami) dodaj następujące platformy (patrz ilustracja):

    ![][3]

6. Wróć do witryny Azure Portal na stronie **Informacje o połączeniu** w swojej aplikacji i skopiuj parametry połączenia.

    ![](../../includes/media/mobile-engagement-create-app-in-portal-new/app-connection-info.png)
7. Dodaj następujący wiersz kodu w pliku **AppDelegate.m**.

    ```obj-c
    #import "EngagementAgent.h"
    ```
8. Wklej parametry połączenia w delegacie `didFinishLaunchingWithOptions`.

    ```obj-c
    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
            [...]   
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
            [...]
    }
    ```

9. Instrukcja `setTestLogEnabled` jest instrukcją opcjonalną, która umożliwia włączenie dzienników zestawu SDK w celu identyfikowania problemów.

## <a id="monitor"></a>Włączanie monitorowania w czasie rzeczywistym
Aby rozpocząć wysyłanie danych i upewnić się, że użytkownicy są aktywni, konieczne jest wysłanie co najmniej jednego ekranu (Działanie) do zaplecza usługi Mobile Engagement.

1. Otwórz plik **ViewController.h** i zaimportuj element **EngagementViewController.h**:

    ```obj-c
    #import "EngagementViewController.h"
    ```

2. Następnie zastąp superklasę interfejsu **ViewController** elementem `EngagementViewController`:
 
    ```obj-c
   @interface ViewController : EngagementViewController
   ```

## <a id="monitor"></a>Łączenie aplikacji z funkcją monitorowania w czasie rzeczywistym
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Włączanie powiadomień wypychanych i funkcji komunikatów w aplikacji
Usługa Mobile Engagement umożliwia wchodzenie w interakcję z użytkownikami i modułem REACH przy użyciu powiadomień wypychanych i komunikatów w aplikacji w kontekście kampanii. Ten moduł w portalu Mobile Engagement ma nazwę REACH.
W poniższych sekcjach aplikacja zostanie skonfigurowana do ich odbierania.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Umożliwianie aplikacji otrzymywania cichych powiadomień wypychanych
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>Dodawanie biblioteki Reach do projektu
1. Kliknij prawym przyciskiem myszy projekt.
2. Wybierz pozycję **Add file to** (Dodaj plik do).
3. Przejdź do folderu, w którym został wyodrębniony zestaw SDK.
4. Wybierz folder `EngagementReach`.
5. Kliknij pozycję **Add** (Dodaj).

### <a name="modify-your-application-delegate"></a>Modyfikowanie delegata aplikacji
1. Wróć do pliku **AppDelegate.m** i zaimportuj moduł Reach usługi Engagement.

    ```obj-c
    #import "AEReachModule.h"
    #import <UserNotifications/UserNotifications.h>
    ```

2. Wewnątrz metody `application:didFinishLaunchingWithOptions` utwórz moduł Reach i uwzględnij go w istniejącym wierszu inicjowania usługi Engagement:

    ```obj-c
    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
        [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
        [...]
        return YES;
    }
    ```

### <a name="enable-your-app-to-receive-apns-push-notifications"></a>Umożliwianie aplikacji otrzymywania powiadomień wypychanych usługi APNS
1. Dodaj następujący wiersz do metody `application:didFinishLaunchingWithOptions`:

    ```obj-c
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
    ```
2. Dodaj metodę `application:didRegisterForRemoteNotificationsWithDeviceToken` w następujący sposób:

    ```obj-c
    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
            [[EngagementAgent shared] registerDeviceToken:deviceToken];
        NSLog(@"Registered Token: %@", deviceToken);
    }
    ```

3. Dodaj metodę `didFailToRegisterForRemoteNotificationsWithError` w następujący sposób:

    ```obj-c
    - (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error
    {
        NSLog(@"Failed to get token, error: %@", error);
    }
    ```

4. Dodaj metodę `didReceiveRemoteNotification:fetchCompletionHandler` w następujący sposób:

    ```obj-c
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }
    ```
    
[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
