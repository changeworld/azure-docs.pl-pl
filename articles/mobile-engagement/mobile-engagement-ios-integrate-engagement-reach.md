---
title: Azure Mobile Engagement iOS SDK Integration osiągnąć | Dokumentacja firmy Microsoft
description: Najnowsze aktualizacje i procedury dla systemu iOS SDK dla usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 1f5f5857-867c-40c5-9d76-675a343a0296
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 12/13/2016
ms.author: piyushjo
ms.openlocfilehash: 8d531f5850e8f7f352774f5894285402bd4cc53e
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-engagement-reach-on-ios"></a>Jak zintegrować Reach usługi Engagement w systemie iOS
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Należy wykonać procedury integracji opisane w sekcji [jak zintegrować zaangażowania w dokumencie iOS](mobile-engagement-ios-integrate-engagement.md) przed wykonaniem tego przewodnika.

Ta dokumentacja wymaga XCode 8. Jeśli naprawdę są zależne od XCode 7, możesz użyć [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh). Jest znaną usterką w poprzedniej podczas uruchamiania na urządzeniach z systemem iOS 10: powiadomień systemowych nie są których wykonano akcje. Rozwiązać problem, należy zaimplementować przestarzałe interfejsu API `application:didReceiveRemoteNotification:` w aplikacji delegowanie w następujący sposób:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> **Nie zaleca się rozwiązanie** jak to zachowanie można zmienić w jakiegokolwiek uaktualnienia wersji nadchodzących iOS (nawet pomocnicze), ponieważ ten iOS interfejsu API jest przestarzały. Powinna przebiegać XCode 8 tak szybko, jak to możliwe.
>
>

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Umożliwianie aplikacji otrzymywania cichych powiadomień wypychanych
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

## <a name="integration-steps"></a>Kroki integracji
### <a name="embed-the-engagement-reach-sdk-into-your-ios-project"></a>Osadź SDK Reach usługi Engagement do projektu systemu iOS
* Dodawanie zestawu sdk modułu Reach do projektu Xcode. W środowisku Xcode przejdź do **projektu \> Dodaj do projektu** i wybierz polecenie `EngagementReach` folderu.

### <a name="modify-your-application-delegate"></a>Modyfikowanie delegata aplikacji
* W górnej części pliku implementacji Zaimportuj moduł Reach usługi Engagement:

      [...]
      #import "AEReachModule.h"
* Wewnątrz metody `applicationDidFinishLaunching:` lub `application:didFinishLaunchingWithOptions:`, Utwórz moduł reach i przekaż go do Twojego istniejącego wiersza inicjowania usługi Engagement:

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
        [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
        [...]

        return YES;
      }
* Modyfikowanie **"icon.png"** ciąg zawierający nazwę obrazu jako ikonę powiadomienia.
* Jeśli chcesz użyć opcji *wartość wskaźnika aktualizacji* kampanie Zasięgowe lub jeśli chcesz użyć natywnych powiadomień wypychanych \<SaaS/Reach interfejsu API/kampanii format/Native Push\> kampanie, należy umożliwić moduł Reach Zarządzanie wskaźnika Ikona sam (zostanie automatycznie usunięty wskaźnika aplikacji i również zresetować do wartości przechowywanej przez zaangażowania za każdym razem, gdy aplikacja jest uruchomiona lub foregrounded). Można to zrobić, dodając następujący wiersz po zainicjowaniu modułu Reach:

      [reach setAutoBadgeEnabled:YES];
* Jeśli chcesz obsługiwać Reach wypychanie danych, należy przeprowadzić delegata aplikacji jest zgodny ze `AEReachDataPushDelegate` protokołu. Dodaj następujący wiersz po zainicjowaniu modułu Reach:

      [reach setDataPushDelegate:self];
* Następnie można zaimplementować metody `onDataPushStringReceived:` i `onDataPushBase64ReceivedWithDecodedBody:andEncodedBody:` w delegata aplikacji:

      -(BOOL)didReceiveStringDataPushWithCategory:(NSString*)category body:(NSString*)body
      {
         NSLog(@"String data push message with category <%@> received: %@", category, body);
         return YES;
      }

      -(BOOL)didReceiveBase64DataPushWithCategory:(NSString*)category decodedBody:(NSData *)decodedBody encodedBody:(NSString *)encodedBody
      {
         NSLog(@"Base64 data push message with category <%@> received: %@", category, encodedBody);
         // Do something useful with decodedBody like updating an image view
         return YES;
      }

### <a name="category"></a>Kategoria
Parametr kategorii jest opcjonalny, podczas tworzenia kampanii wypychania danych i umożliwia Wypychanie do filtrowania danych. Jest to przydatne, jeśli chcesz dystrybuować różnych rodzajów z `Base64` danych, aby zidentyfikować ich typu przed ich analizowania.

**Aplikacja jest teraz gotowa do odbierania i wyświetlić zawartość reach!**

## <a name="how-to-receive-announcements-and-polls-at-any-time"></a>Jak odbierać anonsów i sond w dowolnym momencie
Zaangażowania można wysłać powiadomienia o zasięgu użytkownikom końcowym w dowolnej chwili za pomocą usługi Apple Push Notification Service.

Aby włączyć tę funkcję, musisz przygotować aplikacji dla powiadomień wypychanych firmy Apple i modyfikowanie delegata aplikacji.

### <a name="prepare-your-application-for-apple-push-notifications"></a>Przygotowanie aplikacji dla powiadomień wypychanych firmy Apple
Wykonaj przewodnik: [sposobu przygotowania aplikacji do powiadomień wypychanych firmy Apple](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)

### <a name="add-the-necessary-client-code"></a>Dodaj kod niezbędne klienta
*W tym momencie aplikacji powinien mieć zarejestrowany certyfikat usługi Apple push frontonu zaangażowania.*

Jeśli nie jest jeszcze zrobione, należy zarejestrować aplikację w taki sposób, aby odbierać powiadomienia wypychane.

* Importuj `User Notification` framework:

        #import <UserNotifications/UserNotifications.h>
* Dodaj następujący wiersz po uruchomieniu aplikacji (zwykle w `application:didFinishLaunchingWithOptions:`):

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

Następnie musisz podać do zaangażowania token urządzenia zwrócony przez serwery firmy Apple. Jest to wykonywane w metodę o nazwie `application:didRegisterForRemoteNotificationsWithDeviceToken:` w delegata aplikacji:

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
        [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

Na koniec należy poinformować Engagement SDK, gdy aplikacja otrzyma powiadomienie zdalnego. Aby to zrobić, należy wywołać metodę `applicationDidReceiveRemoteNotification:fetchCompletionHandler:` w delegata aplikacji:

    - (void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

> [!IMPORTANT]
> Domyślnie Reach usługi Engagement steruje completionHandler. Jeśli chcesz ręcznie odpowiadanie na `handler` bloków w kodzie, można przekazać nil `handler` argumentu i kontroli zakończenia zablokować samodzielnie. Zobacz `UIBackgroundFetchResult` typu listę możliwych wartości.
>
>

### <a name="full-example"></a>Pełny przykład
Oto pełny przykład integracji:

    #pragma mark -
    #pragma mark Application lifecycle

    - (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
    {
      /* Reach module */
      AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
      [reach setAutoBadgeEnabled:YES];

      /* Engagement initialization */
      [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
      [[EngagementAgent shared] setPushDelegate:self];

      /* Views */
      [window addSubview:[tabBarController view]];
      [window makeKeyAndVisible];

      [application registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert|UIRemoteNotificationTypeBadge|UIRemoteNotificationTypeSound];
      return YES;
    }

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
      [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
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

## <a name="how-to-customize-campaigns"></a>Dostosowywanie kampanii
### <a name="notifications"></a>Powiadomienia
Istnieją dwa typy powiadomień: powiadomień systemowych i w aplikacji.

Powiadomienia systemowe są obsługiwane przez system iOS i nie można dostosować.

W aplikacji zawiadomienia widoku, który jest dynamicznie dodawane do bieżącego okna aplikacji. Jest to nakładce powiadomienia. Nakładki powiadomienia są bardzo szybkiego integracji, ponieważ wymagają one, nie można zmodyfikować w dowolnym widoku w aplikacji.

#### <a name="layout"></a>Układ
Aby zmodyfikować wygląd powiadomienia w aplikacji, wystarczy zmodyfikować plik `AENotificationView.xib` do własnych potrzeb, tak długo, jak zabezpieczyć wartości tagów i typy widoków istniejących podrzędnych.

Domyślnie powiadomienia w aplikacji są prezentowane w dolnej części ekranu. Jeśli chcesz je wyświetlić w górnej części ekranu, Edytuj dostarczonych `AENotificationView.xib` i zmienić `AutoSizing` właściwości widoku głównego, mogą być przechowywane w górnej części jej superview.

#### <a name="categories"></a>Kategorie
Po zmodyfikowaniu układu, możesz zmodyfikować wygląd wszystkich powiadomień. Kategorie umożliwiają definiowanie różnych wygląda docelowej (prawdopodobnie zachowania) dla powiadomień. Po utworzeniu kampanii Reach można określić kategorię. Należy pamiętać, że kategorie pozwalają również dostosować anonsów i sond, opisane w dalszej części tego dokumentu.

Aby zarejestrować kategorii Obsługa powiadomienia, należy dodać wywołanie po zainicjowaniu moduł reach.

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:@"my_category"];
    ...

`myNotifier` musi być wystąpieniem obiektu, który jest zgodny z protokołem `AENotifier`.

Można zaimplementować metody protokołu przez siebie lub mogą być reimplement istniejącej klasy `AEDefaultNotifier` który już wykonuje większość pracy.

Na przykład jeśli chcesz zmienić definicję widoku powiadomień dla określonej kategorii, należy wykonać w tym przykładzie:

    #import "AEDefaultNotifier.h"
    #import "AENotificationView.h"
    @interface MyNotifier : AEDefaultNotifier
    @end

    @implementation MyNotifier

    -(NSString*)nibNameForCategory:(NSString*)category
    {
      return "MyNotificationView";
    }

    @end

Ten prosty przykład kategorii założono, że istnieje plik o nazwie `MyNotificationView.xib` w Twojego pakietu aplikacji głównej. Jeśli metoda nie jest w stanie znaleźć odpowiadającego `.xib`, nie będą wyświetlane powiadomienia i zaangażowania dane wyjściowe obejmują komunikat w konsoli.

Nib podanego pliku powinna przestrzegać następujących reguł:

* Powinna ona zawierać tylko jeden widok.
* Widoków podrzędnych powinny być tego samego typu, jak te w nib podany plik o nazwie `AENotificationView.xib`
* Widoków podrzędnych powinny mieć te same znaczniki, jak te w dostarczonych nib plik o nazwie `AENotificationView.xib`

> [!TIP]
> Po prostu skopiuj plik nib udostępnionego o nazwie `AENotificationView.xib`i rozpocząć pracę z tego miejsca. Jednak należy zachować ostrożność, widok wewnątrz tego pliku nib jest skojarzony z klasy `AENotificationView`. Ta klasa ponownie definiować metodę `layoutSubViews` na przenoszenie i zmienianie rozmiaru jego widoków podrzędnych zgodnie z kontekstu. Chcesz go zastąpić `UIView` lub klasy widok niestandardowy.
>
>

Jeśli potrzebujesz bardziej dostosowywania powiadomienia (Jeśli na przykład chcesz załadować widoku bezpośrednio z kodu), zaleca się Spójrz na źródła podany kod i klasy w dokumentacji `Protocol ReferencesDefaultNotifier` i `AENotifier`.

Pamiętaj, że można używać tego samego zgłaszający dla wielu kategorii.

Można również ponownie zdefiniować zgłaszający domyślne następująco:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:kAEReachDefaultCategory];

##### <a name="notification-handling"></a>Obsługa powiadomień
Podczas korzystania z domyślnej kategorii, w przypadku niektórych metod cyklu życia są nazywane na `AEReachContent` obiektu do raportu statystyk i zaktualizować stan kampanii:

* Gdy zostanie wyświetlone powiadomienie w aplikacji, `displayNotification` metoda jest wywoływana (która statystyki) przez `AEReachModule` Jeśli `handleNotification:` zwraca `YES`.
* Jeśli powiadomienie jest odrzucane, `exitNotification` metoda jest wywoływana, statystyka jest zgłaszany i dalej kampanii teraz mogą być przetwarzane.
* Po kliknięciu powiadomienia `actionNotification` jest wywoływana, statystyka zostaje zgłoszone i skojarzona akcja jest wykonywana.

Jeśli implementacji `AENotifier` pomija domyślne zachowanie, należy wywołać metody te cyklu życia samodzielnie. Poniższe przykłady przedstawiają w niektórych przypadkach, gdy pomijane jest domyślne zachowanie:

* Nie można rozszerzyć `AEDefaultNotifier`, np. zaimplementowano obsługi kategorii od początku.
* Możesz overrode `prepareNotificationView:forContent:`, pamiętaj zamapować co najmniej `onNotificationActioned` lub `onNotificationExited` do jednego z U.I kontrolki.

> [!WARNING]
> Jeśli `handleNotification:` zgłasza wyjątek, zawartość zostanie usunięta i `drop` jest wywoływana, jest to raportowane w statystykach i dalej kampanii teraz mogą być przetwarzane.
>
>

#### <a name="include-notification-as-part-of-an-existing-view"></a>Powiadomienia jako część istniejącego widoku
Nakładki są bardzo szybkiego integracji, ale może nie może być czasami wygodne lub może mieć niepożądane skutki uboczne.

Jeśli użytkownik nie ma spełnia Twoje wymagania systemu nakładki w niektórych widoków, można dostosować go do tych widoków.

Można zdecydować objąć naszych układu powiadomień istniejących widoków. Aby to zrobić, brak dwa style implementacji:

1. Dodaj widok powiadomienia za pomocą konstruktora interfejsu

   * Otwórz *interfejsu konstruktora*
   * Umieść 320 x 60 (lub jeśli iPad 768 x 60) `UIView` miejscu powiadomienia
   * Ustaw wartość tagu dla tego widoku do: **36822491**
2. Dodaj widok powiadomienia programowo. Po zainicjowaniu widoku, po prostu dodaj następujący kod:

       UIView* notificationView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 320, 60)]; //Replace x and y coordinate values to your needs.
       notificationView.tag = NOTIFICATION_AREA_VIEW_TAG;
       [self.view addSubview:notificationView];

`NOTIFICATION_AREA_VIEW_TAG` Makro można znaleźć w `AEDefaultNotifier.h`.

> [!NOTE]
> Zgłaszający domyślne automatycznie wykrywa, czy układ powiadomień znajduje się w tym widoku i nie dodają nakładki dla niego.
>
>

### <a name="announcements-and-polls"></a>Anonsów i sond
#### <a name="layouts"></a>Układy
Można modyfikować pliki `AEDefaultAnnouncementView.xib` i `AEDefaultPollView.xib` tak długo, jak zabezpieczyć wartości tagów i typy widoków istniejących podrzędnych.

#### <a name="categories"></a>Kategorie
##### <a name="alternate-layouts"></a>Układy alternatywnej
Takich jak powiadomienia Kategoria kampanii można mieć alternatywne układy anonsów i sond.

Aby utworzyć kategorii anonsu, należy rozszerzyć **AEAnnouncementViewController** i zarejestruj go po zainicjowaniu moduł reach:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:@"my_category"];

> [!NOTE]
> Każdym razem, użytkownik będzie kliknij powiadomienie anonsu z kategorią "Mój\_kategorii", kontroler widoku zarejestrowane (w takim przypadku `MyCustomAnnouncementViewController`) zostanie zainicjowana przez wywołanie metody `initWithAnnouncement:` i widoku zostaną dodane do bieżące okno aplikacji.
>
>

W implementacji `AEAnnouncementViewController` klasy trzeba będzie można odczytać właściwości `announcement` zainicjować z widoków podrzędnych. Należy wziąć pod uwagę w poniższym przykładzie, gdy dwie etykiety są inicjowane przy użyciu `title` i `body` właściwości `AEReachAnnouncement` klasy:

    -(void)loadView
    {
        [super loadView];

        UILabel* titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        titleLabel.font = [UIFont systemFontOfSize:32.0];
        titleLabel.text = self.announcement.title;

        UILabel* bodyLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        bodyLabel.font = [UIFont systemFontOfSize:24.0];
        bodyLabel.text = self.announcement.body;

        [self.view addSubview:titleLabel];
        [self.view addSubview:bodyLabel];
    }

Jeśli nie chcesz załadować widoków samodzielnie, ale po prostu chcesz ponownie użyć domyślnego układu widoku anonsu, po prostu ułatwia kontroler niestandardowy widok rozszerza klasę podana `AEDefaultAnnouncementViewController`. W takim przypadku zduplikowany plik nib `AEDefaultAnnouncementView.xib` i zmień jego nazwę, dlatego może być załadowany przez kontroler niestandardowy widok (dla kontrolera o nazwie `CustomAnnouncementViewController`, należy wywołać pliku nib `CustomAnnouncementView.xib`).

Aby zastąpić domyślne kategorię anonsów, po prostu zarejestrować Kontroler Widok niestandardowy kategorii zdefiniowanej w `kAEReachDefaultCategory`:

    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:kAEReachDefaultCategory];

Ankiety można dostosować ten sam sposób:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerPollController:[MyCustomPollViewController class] forCategory:@"my_category"];

Teraz, dostępnego `MyCustomPollViewController` muszą być rozszerzane `AEPollViewController`. Można rozszerzyć z domyślnego kontrolera: `AEDefaultPollViewController`.

> [!IMPORTANT]
> Nie zapomnij wywołań albo `action` (`submitAnswers:` kontrolerów widok niestandardowy sondowania) lub `exit` metoda przed kontroler widoku jest odrzucane. W przeciwnym razie statystyki nie zostaną wysłane (tzn. nie analytics kampanii) i więcej kampanii istotne dalej nie zostaną powiadomieni do ponownego uruchomienia procesu aplikacji.
>
>

##### <a name="implementation-example"></a>Przykład wdrożenia
W tej implementacji widok niestandardowy anonsu została załadowana z pliku zewnętrznego xib.

Podobnie jak dostosowywania powiadomień zaawansowane zalecane jest aby przyjrzeć się kodu źródłowego standardowej implementacji.

`CustomAnnouncementViewController.h`

    //Interface
    @interface CustomAnnouncementViewController : AEAnnouncementViewController {
      UILabel* titleLabel;
      UITextView* descTextView;
      UIWebView* htmlWebView;
      UIButton* okButton;
      UIButton* cancelButton;
    }

    @property (nonatomic, retain) IBOutlet UILabel* titleLabel;
    @property (nonatomic, retain) IBOutlet UITextView* descTextView;
    @property (nonatomic, retain) IBOutlet UIWebView* htmlWebView;
    @property (nonatomic, retain) IBOutlet UIButton* okButton;
    @property (nonatomic, retain) IBOutlet UIButton* cancelButton;

    -(IBAction)okButtonClicked:(id)sender;
    -(IBAction)cancelButtonClicked:(id)sender;

`CustomAnnouncementViewController.m`

    //Implementation
    @implementation CustomAnnouncementViewController
    @synthesize titleLabel;
    @synthesize descTextView;
    @synthesize htmlWebView;
    @synthesize okButton;
    @synthesize cancelButton;

    -(id)initWithAnnouncement:(AEReachAnnouncement*)anAnnouncement
    {
      self = [super initWithNibName:@"CustomAnnouncementViewController" bundle:nil];
      if (self != nil) {
        self.announcement = anAnnouncement;
      }
      return self;
    }

    - (void) dealloc
    {
      [titleLabel release];
      [descTextView release];
      [htmlWebView release];
      [okButton release];
      [cancelButton release];
      [super dealloc];
    }

    - (void)viewDidLoad {
      [super viewDidLoad];

      /* Init announcement title */
      titleLabel.text = self.announcement.title;

      /* Init announcement body */
      if(self.announcement.type == AEAnnouncementTypeHtml)
      {
        titleLabel.hidden = YES;
        htmlWebView.hidden = NO;
        [htmlWebView loadHTMLString:self.announcement.body baseURL:[NSURL URLWithString:@"http://localhost/"]];
      }
      else
      {
        titleLabel.hidden = NO;
        htmlWebView.hidden = YES;
        descTextView.text = self.announcement.body;
      }

      /* Set action button label */
      if([self.announcement.actionLabel length] > 0)
        [okButton setTitle:self.announcement.actionLabel forState:UIControlStateNormal];

      /* Set exit button label */
      if([self.announcement.exitLabel length] > 0)
        [cancelButton setTitle:self.announcement.exitLabel forState:UIControlStateNormal];
    }

    #pragma mark Actions

    -(IBAction)okButtonClicked:(id)sender
    {
        [self action];
    }

    -(IBAction)cancelButtonClicked:(id)sender
    {
        [self exit];
    }

    @end
