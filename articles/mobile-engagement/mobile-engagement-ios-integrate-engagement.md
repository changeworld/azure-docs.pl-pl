---
title: Azure Mobile Engagement iOS SDK Integration | Dokumentacja firmy Microsoft
description: Najnowsze aktualizacje i procedury dla systemu iOS SDK dla usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 947ea44b-00c1-450f-9a3b-74437954dc56
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: f0d650205ca86205ad857003f892c1a60b3831bf
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-engagement-on-ios"></a>Jak zintegrować zaangażowania w systemie iOS
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

> [!div class="op_single_selector"]
> * [Aplikacje uniwersalne systemu Windows](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
>
>

W tej procedurze opisano Najprostszym sposobem, aby aktywować analizy i funkcji w aplikacji systemu iOS monitorowania usługi Engagement.

Zestaw SDK usługi Engagement wymaga systemu iOS7 + i Xcode 8 +: cel wdrożenia aplikacji musi wynosić co najmniej system iOS 7.

> [!NOTE]
> Jeśli naprawdę są zależne od XCode 7, możesz użyć [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh). Brak znaną usterką w moduł Reach tej poprzedniej wersji podczas uruchamiania na Zobacz urządzeń z systemem iOS 10 [integracji modułu reach](mobile-engagement-ios-integrate-engagement-reach.md) więcej szczegółów. Jeśli zdecydujesz się użyć v3.2.4 zestawu SDK, a następnie pominąć `UserNotifications.framework` zaimportować w następnym kroku.
>
>

Poniższe kroki są wystarczająco aktywować raport dzienniki wymagane do obliczenia wszystkich statystyki dotyczące użytkowników, sesji, działania, awarii (Crash) i Technicals. Raport dzienniki wymagane do obliczenia innych danych statystycznych, takich jak zdarzenia i błędy zadań musi zostać wykonane ręcznie przy użyciu interfejsu API usługi Engagement (zobacz [sposobu korzystania z zaawansowanych Mobile Engagement znakowanie interfejsu API w aplikacji systemu iOS](mobile-engagement-ios-use-engagement-api.md) ponieważ te statystyki są aplikacji zależnej.

## <a name="embed-the-engagement-sdk-into-your-ios-project"></a>Osadź Engagement SDK do projektu systemu iOS
* Pobierz zestaw SDK systemu iOS z [tutaj](http://aka.ms/qk2rnj).
* Dodaj zestaw SDK usługi Engagement do projektu systemu iOS: w środowisku Xcode, kliknij prawym przyciskiem myszy projekt i wybierz **"Dodaj pliki do..."** i wybierz polecenie `EngagementSDK` folderu.
* Engagement wymaga dodatkowych platform pracę: w Eksploratorze projektu otwórz okienko z projektu i wybierz poprawny docelowy. Następnie otwórz **"Fazy kompilacji"** kartę i **"Binarny z bibliotekami"** menu Dodaj następujące struktury:

  * `UserNotifications.framework` -łącze jako `Optional`
  * `AdSupport.framework` -łącze jako `Optional`
  * `SystemConfiguration.framework`
  * `CoreTelephony.framework`
  * `CFNetwork.framework`
  * `CoreLocation.framework`
  * `libxml2.dylib`

> [!NOTE]
> W ramach AdSupport mogą zostać usunięte. Zaangażowania musi platforma służąca do gromadzenia identyfikator IDFA. Jednak można wyłączyć kolekcji identyfikator IDFA \<ios-sdk zaangażowania — identyfikator idfa\> do wykonania nowe zasady Apple dotyczące tego identyfikatora.
>
>

## <a name="initialize-the-engagement-sdk"></a>Inicjowanie usługi Engagement SDK
Należy zmodyfikować delegata aplikacji:

* U góry pliku implementacji należy zaimportować agenta usługi Engagement:

      [...]
      #import "EngagementAgent.h"
* Inicjowanie usługi Engagement wewnątrz metody "**applicationDidFinishLaunching:**"lub"**aplikacji: didFinishLaunchingWithOptions:**":

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
      {
        [...]
        [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
        [...]
      }

## <a name="basic-reporting"></a>Podstawowe raportowanie
### <a name="recommended-method-overload-your-uiviewcontroller-classes"></a>Zalecana metoda: przeciążenia sieci `UIViewController` klas
Aby aktywować raportu wszystkie dzienniki wymagane przez zaangażowania można obliczyć użytkowników, sesji, działania, awarii (Crash) i statystyki technicznych, możesz po prostu wprowadzić wszystkie Twoje `UIViewController` klasy podrzędne dziedziczą `EngagementViewController` klasy (takie same reguły dla `UITableViewController`  - \> `EngagementTableViewController`).

**Bez usługi Engagement:**

    #import <UIKit/UIKit.h>

    @interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**Z usługi Engagement:**

    #import <UIKit/UIKit.h>
    #import "EngagementViewController.h"

    @interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### <a name="alternate-method-call-startactivity-manually"></a>Alternatywna metoda: wywołanie `startActivity()` ręcznie
Jeśli nie możesz lub nie było przeciążyć Twojej `UIViewController` klasy, zamiast tego można uruchomić działań przez wywołanie metody `EngagementAgent`przez metody bezpośrednio.

> [!IMPORTANT]
> IOS SDK automatycznie wywołuje `endActivity()` metody, gdy aplikacja zostanie zamknięta. W związku z tym jest *wysokiej* zaleca, aby wywołać `startActivity` metody zmianie aktywności użytkownika i *nigdy* wywołać `endActivity` metody, ponieważ wywołanie tej metody wymusza bieżącej sesji, aby zostać zakończona.
>
>

## <a name="location-reporting"></a>Raportowanie lokalizacji
Apple warunków użytkowania usługi nie umożliwiają aplikacjom Użyj śledzenia w celu statystyki tylko lokalizacji. W związku z tym zalecane jest włączanie lokalizacji raportów tylko wtedy, gdy aplikacja jest również używać śledzenia z innego powodu lokalizacji.

Począwszy od systemu iOS 8, należy podać opis sposobu Twoja aplikacja korzysta z usług lokalizacji przez ustawienie ciąg dla klucza [NSLocationWhenInUseUsageDescription] lub [NSLocationAlwaysUsageDescription] w pliku Info.plist aplikacji. Jeśli chcesz lokalizacji raportu w tle z zaangażowania Dodaj klucz NSLocationAlwaysUsageDescription. We wszystkich innych przypadkach Dodaj klucz NSLocationWhenInUseUsageDescription. Należy pamiętać, że trzeba zarówno NSLocationAlwaysAndWhenInUseUsageDescription i NSLocationWhenInUseUsageDescription lokalizacji tło raportu w systemie iOS 11.

### <a name="lazy-area-location-reporting"></a>Raportowanie lokalizacji obszaru z opóźnieniem
Umożliwia raportowanie lokalizacji obszaru z opóźnieniem zgłoszenia kraj, region i lokalizację skojarzone z urządzeniami. Raportowanie lokalizacji tego typu używa tylko lokalizacje sieciowe (na podstawie Identyfikatora komórki lub Wi-Fi). Obszar urządzenia są zgłaszane co najwyżej raz na sesję. GPS nigdy nie jest używana, a w związku z tym ten typ lokalizacji raportu ma bardzo nieliczne (nie musi wypowiedzieć nie) wpływ na baterii.

Obszary zgłoszone są używane do obliczeniowe geograficzne statystyki dotyczące użytkowników, sesji, zdarzenia i błędy. Mogą one również używane jako kryterium w kampanie Zasięgowe. Ostatni znany obszaru zgłoszonych dla urządzenia mogą być pobierane podziękowania dla [interfejsu API urządzenia].

Aby włączyć raportowanie lokalizacji obszaru z opóźnieniem, Dodaj następujący wiersz po inicjowanie agenta usługi Engagement:

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
      [...]
      [[EngagementAgent shared] setLazyAreaLocationReport:YES];
      [...]
    }

### <a name="real-time-location-reporting"></a>Raportowanie lokalizacji w czasie rzeczywistym
Raportowanie lokalizacji w czasie rzeczywistym umożliwia zgłoszenia współrzędne geograficzne skojarzonego z urządzeń. Domyślnie raportowanie lokalizacji tego typu używa tylko lokalizacje sieciowe (na podstawie Identyfikatora komórki lub Wi-Fi) i raportowania jest aktywne po uruchomieniu aplikacji na pierwszym planie (np. podczas sesji).

Lokalizacje w czasie rzeczywistym *nie* używany do obliczania statystyk. Ich jedynym celem jest, aby zezwolić na użycie grodzenia czasu rzeczywistego \<Reach-odbiorców geofencing\> kryterium w kampanie Zasięgowe.

Aby włączyć raportowanie lokalizacji w czasie rzeczywistym, Dodaj następujący wiersz po inicjowanie agenta usługi Engagement:

    [[EngagementAgent shared] setRealtimeLocationReport:YES];

#### <a name="gps-based-reporting"></a>GPS na podstawie raportowania
Domyślnie raportowanie lokalizacji w czasie rzeczywistym używa tylko lokalizacje sieciowe. Aby włączyć korzystanie z GPS na podstawie lokalizacji (które są znacznie bardziej precyzyjne), dodać:

    [[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### <a name="background-reporting"></a>Raportowanie w tle
Domyślnie raportowanie lokalizacji w czasie rzeczywistym jest aktywne po uruchomieniu aplikacji na pierwszym planie (np. podczas sesji). Aby włączyć raportowanie również, w tle, należy dodać:

    [[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [!NOTE]
> Gdy aplikacja zostanie uruchomiona w tle, tylko do sieci na podstawie lokalizacji są raportowane, nawet jeśli włączono GPS.
>
>

Implementacja tej funkcji będzie wywoływać [startMonitoringSignificantLocationChanges] gdy aplikacja przechodzi w tle. Należy pamiętać, że go zostanie automatycznie uruchom ponownie aplikację w tle Jeśli jest to nowe zdarzenie lokalizacji.

## <a name="advanced-reporting"></a>Zaawansowane raportowanie
Opcjonalnie, jeśli chcesz zgłosić aplikacji określonych zdarzeń, błędów i zadań, należy użyć interfejsu API usługi Engagement za pomocą metody `EngagementAgent` klasy. Obiekt tej klasy można pobrać przez wywołanie metody `[EngagementAgent shared]` metody statycznej.

Interfejsu API usługi Engagement pozwala korzystać ze wszystkich zaawansowanych możliwości usługi Engagement i została szczegółowo opisana w sposób używania interfejsu API programu zaangażowania w systemie iOS (jak również w dokumentacji technicznej `EngagementAgent` klasy).

## <a name="disable-idfa-collection"></a>Wyłącz funkcję zbierania identyfikator IDFA
Domyślnie użyje Engagement [identyfikator IDFA] do unikatowego identyfikowania użytkownika. Ale jeśli nie używasz reklamy w innym miejscu w aplikacji, mogą zostać odrzucone przez proces przeglądu sklepu z aplikacjami. Identyfikator IDFA kolekcji można wyłączyć, dodając makro preprocesora `ENGAGEMENT_DISABLE_IDFA` w pliku pch (lub w `Build Settings` aplikacji). To zagwarantować, że jest żadnych odwołań do `ASIdentifierManager`, `advertisingIdentifier` lub `isAdvertisingTrackingEnabled` w kompilacji aplikacji.

Integracja w **prefix.pch** pliku:

    #define ENGAGEMENT_DISABLE_IDFA
    ...

Można Sprawdź, czy kolekcja identyfikator IDFA prawidłowo jest wyłączone w aplikacji w sprawdzając dzienników testu zaangażowania. Zobacz testowego integracji\<ios-sdk-engagement testu — identyfikator idfa\> dokumentacji, aby uzyskać więcej informacji.

## <a name="disable-log-reporting"></a>Wyłączanie dziennika raportowania
### <a name="using-a-method-call"></a>Przy użyciu wywołania metody
Jeśli chcesz zaangażowania, aby zatrzymać wysyłanie dzienników można wywołać:

    [[EngagementAgent shared] setEnabled:NO];

To wywołanie jest trwały: używa `NSUserDefaults` do przechowywania informacji.

Możesz włączyć ponownie raportowania przez wywołanie tej samej funkcji z dziennika `YES`.

### <a name="integration-in-your-settings-bundle"></a>Integracja z ustawień pakietu
Zamiast wywoływania tej funkcji, to ustawienie można również zintegrować bezpośrednio w istniejącą `Settings.bundle` pliku. Ciąg `engagement_agent_enabled` muszą być używane jako identyfikator preferencji, a musi być skojarzony z przełącznikiem przełączania (`PSToggleSwitchSpecifier`).

Poniższy przykład przedstawia `Settings.bundle` pokazano, jak ją wdrożyć:

    <dict>
        <key>PreferenceSpecifiers</key>
        <array>
            <dict>
                <key>DefaultValue</key>
                <true/>
                <key>Key</key>
                <string>engagement_agent_enabled</string>
                <key>Title</key>
                <string>Log reporting enabled</string>
                <key>Type</key>
                <string>PSToggleSwitchSpecifier</string>
            </dict>
        </array>
        <key>StringsTable</key>
        <string>Root</string>
    </dict>

<!-- URLs. -->
[interfejsu API urządzenia]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]:http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[identyfikator IDFA]:https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier
