---
title: Powiadomienia wypychane do aplikacji Swift iOS, które używają usługi Azure Notification Hubs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak powiadomienia wypychane do aplikacji Swift iOS, które używają usługi Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: patniko
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.openlocfilehash: c35044918876b2c7710e26f6b868bc1096c2f538
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340401"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-that-use-the-notification-hubs-rest-api"></a>Samouczek: Powiadomienia wypychane do aplikacji Swift iOS, korzystających z interfejsu API REST dla centrów powiadomień

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

W tym samouczku używasz usługi Azure Notification Hubs wysyłać powiadomienia wypychane do aplikacji systemu iOS opartych na języku Swift przy użyciu [interfejsu API REST](/rest/api/notificationhubs/). Możesz również utworzyć pusta aplikacja systemu iOS odbierającą powiadomienia wypychane przy użyciu [Apple Push Notification service (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

Ten samouczek przeprowadzi Cię przez następujące kroki:

> [!div class="checklist"]
> * Wygeneruj plik żądania podpisania certyfikatu.
> * Żądanie aplikacji dla powiadomień wypychanych.
> * Utwórz profil inicjowania obsługi administracyjnej dla aplikacji.
> * Tworzenie centrum powiadomień.
> * Konfigurowanie Centrum powiadomień przy użyciu informacji z usługi APNs.
> * Połącz swoją aplikację dla systemu iOS do danego centrum powiadomień.
> * Testowanie rozwiązania.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku potrzebne są:

- Aby wykonać kroki [Omówienie usługi Azure Notification Hubs](notification-hubs-push-notification-overview.md) Jeśli nie znasz usługi.
- Aby dowiedzieć się więcej na temat [rejestracji i instalacji](notification-hubs-push-notification-registration-management.md).
- Aktywne [konto programu Apple Developer](https://developer.apple.com).
- Komputer Mac korzystania z narzędzia Xcode oraz zainstalowanego w łańcuchu kluczy certyfikatu prawidłowy dla deweloperów.
- Urządzenie fizyczne urządzenia iPhone, można uruchomić i debugować, ponieważ nie można przetestować powiadomienia wypychane z symulatorem.
- Urządzenie fizyczne urządzenia iPhone zarejestrowane w [portalu Apple](https://developer.apple.com) i skojarzonych z certyfikatu.
- [Subskrypcji platformy Azure](https://portal.azure.com) gdzie można tworzyć i zarządzać zasobami.

Nawet jeśli masz już pewne doświadczenie w za pomocą programowania w systemie iOS, należy skorzystać z procedury służące do tworzenia w tym przykładzie pierwsze zasady. Jednak warto skorzystać z znajomość następujących pojęć:

- Tworzenie aplikacji dla systemu iOS za pomocą środowiska Xcode i Swift.
- Konfigurowanie [Centrum powiadomień Azure](notification-hubs-ios-apple-push-notification-apns-get-started.md) dla systemu iOS.
- [Portalu Apple Developer Portal](https://developer.apple.com) i [witryny Azure portal](https://portal.azure.com).

> [!NOTE]
> Centrum powiadomień zostanie skonfigurowana w celu użycia **piaskownicy** tylko w trybie uwierzytelniania. Ten tryb uwierzytelniania nie należy używać w przypadku obciążeń produkcyjnych.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>Łączenie aplikacji dla systemu iOS z Centrum powiadomień

W tej sekcji utworzysz aplikację dla systemu iOS, która połączy się z Centrum powiadomień.  

### <a name="create-an-ios-project"></a>Tworzenie projektu systemu iOS

1. W środowisku Xcode utwórz nowy projekt dla systemu iOS i wybierz szablon **Single View Application** (Aplikacja z jednym widokiem).

1. Podczas określania opcji dla nowego projektu:

   1. Określ **nazwa produktu** (PushDemo) i **identyfikator organizacji** (`com.<organization>`) używanej podczas ustawiania **identyfikatora pakietu** w portalu dla deweloperów firmy Apple.

   1. Wybierz **zespołu** , **Identyfikatora aplikacji** zostało skonfigurowane pod kątem.

   1. Ustaw **języka** do **Swift**.

   1. Wybierz opcję **Dalej**.

1. Utwórz nowy folder o nazwie **SupportingFiles**.

1. Utwórz nowy plik p-list, nazywane **devsettings.plist** w **SupportingFiles** folderu. Pamiętaj dodać ten folder do Twojej **gitignore** pliku, dzięki czemu nie jest zatwierdzona podczas pracy z repozytorium git. W aplikacji produkcyjnej użytkownik będzie prawdopodobnie można warunkowo ustawienie tych kluczy tajnych w ramach zautomatyzowanego procesu kompilacji. Takie ustawienia nie są uwzględnione w tym przewodniku.

1. Aktualizacja **devsettings.plist** do obejmują następujące pozycje konfiguracji przy użyciu wartości z Centrum powiadomień, aprowizowana przez Ciebie:

   | Klucz                            | Type                     | Value                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | String                   | \<hubKey>                  |
   | notificationHubKeyName         | String                   | \<hubKeyName>              |
   | notificationHubName            | String                   | \<hubName>                 |
   | notificationHubNamespace       | String                   | \<hubNamespace>            |

   Wymagane wartości można znaleźć, przechodząc do zasób Centrum powiadomień w witrynie Azure portal. W szczególności **notificationHubName** i **notificationHubNamespace** wartości znajdują się w prawym górnym rogu **Essentials** podsumowania w ramach **Przegląd** strony.

   ![Podsumowanie Essentials centra powiadomień](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Możesz również znaleźć **notificationHubKeyName** i **notificationHubKey** wartości, przechodząc do **zasady dostępu** i wybierając odpowiednie  **Zasady dostępu**, takich jak `DefaultFullSharedAccessSignature`. Następnie należy skopiować z **podstawowe parametry połączenia** wartość prefiks `SharedAccessKeyName=` dla `notificationHubKeyName` i wartość prefiks `SharedAccessKey=` dla `notificationHubKey`.

   Parametry połączenia powinny być w następującym formacie:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Aby zachować prostotę, należy określić `DefaultFullSharedAccessSignature` aby można było używać tokenu do wysyłania powiadomień. W praktyce `DefaultListenSharedAccessSignature` będzie lepszym rozwiązaniem dla sytuacji, gdy tylko do odbierania powiadomień.

1. W obszarze **Nawigatora projektu**, wybierz opcję **Nazwa projektu** , a następnie wybierz **ogólne** kartę.

1. Znajdź **tożsamości** , a następnie ustaw **identyfikatora pakietu** wartości, tak aby był zgodny `com.<organization>.PushDemo`, wartość używaną dla **Identyfikatora aplikacji** w poprzednim kroku.

1. Znajdź **podpisywanie**, a następnie wybierz odpowiednią **zespołu** dla Twojego **konto programu Apple Developer**. **Zespołu** wartość powinna odpowiadać co w ramach którego są tworzone certyfikaty i profile.

1. Środowisko Xcode powinno automatycznie ściągnąć odpowiednie **profilu aprowizacji** na podstawie wartości **identyfikatora pakietu**. Jeśli nie widzisz nowy **profilu aprowizacji** wartość, Odśwież profile dla **tożsamość podpisywania** , wybierając **Xcode**  >  **Preferencje** > **konta** > **wyświetlić szczegóły**. Wybierz **tożsamość podpisywania**, a następnie wybierz pozycję **Odśwież** przycisk w prawym dolnym rogu, aby pobrać profili.

1. Wybierz **możliwości** kartę i upewnij się, że **powiadomienia wypychane** są włączone.

1. Otwórz swoje **AppDelegate.swift** plik, aby zaimplementować **UNUserNotificationCenterDelegate** protokołu i Dodaj następujący kod na początku klasy:

    ```swift
    @UIApplicationMain
    class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {

        ...

        var configValues: NSDictionary?
        var notificationHubNamespace : String?
        var notificationHubName : String?
        var notificationHubKeyName : String?
        var notificationHubKey : String?
        let tags = ["12345"]
        let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")

        ...
    }
    ```

    Później użyjesz tych elementów członkowskich. W szczególności użyjesz **tagi** i **genericTemplate** członków w ramach rejestracji. Aby uzyskać więcej informacji na temat tagów, zobacz [tagi do rejestracji](notification-hubs-tags-segment-push-message.md) i [Rejestracja szablonu](notification-hubs-templates-cross-platform-push-messages.md).

1. W tym samym pliku Dodaj następujący kod do **didFinishLaunchingWithOptions** funkcji:

    ```swift
    if let path = Bundle.main.path(forResource: "devsettings", ofType: "plist") {
        if let configValues = NSDictionary(contentsOfFile: path) {
            self.notificationHubNamespace = configValues["notificationHubNamespace"] as? String
            self.notificationHubName = configValues["notificationHubName"] as? String
            self.notificationHubKeyName = configValues["notificationHubKeyName"] as? String
            self.notificationHubKey = configValues["notificationHubKey"] as? String
        }
    }

    if #available(iOS 10.0, *){
        UNUserNotificationCenter.current().delegate = self
        UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
            (granted, error) in

            if (granted)
            {
                DispatchQueue.main.async {
                    application.registerForRemoteNotifications()
                }
            }
        }
    }

    return true
    ```

    Ten kod pobiera wartości ustawienie z **devsettings.plist**, ustawia **elemencie AppDelegate** klasy **UNUserNotificationCenter** delegować żądania autoryzacji powiadomienia wypychane, a następnie wywołania **registerForRemoteNotifications**.

    Aby zachować prostotę, obsługuje kod *system iOS 10 lub nowsza*. Aby dodać obsługę poprzednich wersji systemu operacyjnego, należy warunkowo przy użyciu odpowiedniej metody i interfejsy API, jak zwykle.

1. W tym samym pliku Dodaj następujące funkcje:

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        self.window?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    Kod używa **installationId** i **pushChannel** wartości do rejestracji w usłudze notification hubs. W tym przypadku używasz **UIDevice.current.identifierForVendor** zapewnienie unikatową wartość, aby zidentyfikować urządzenia, a następnie formatowanie **deviceToken** zapewnienie żądaną  **pushChannel** wartość. **ShowAlert** istnieje funkcja po prostu, aby wyświetlić część tekstu komunikatu w celach demonstracyjnych.

1. Nadal w **AppDelegate.swift** Dodaj **willPresent** i **didReceive** funkcje **UNUserNotificationCenterDelegate**. Te funkcje wyświetlić alert, gdy są one wyświetlane powiadomienie, aplikacja jest uruchomiona w pierwszym planie lub w tle odpowiednio.

    ```swift
    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        willPresent notification: UNNotification, 
        withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        showAlert(withText: notification.request.content.body)
    }

    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        didReceive response: UNNotificationResponse, 
        withCompletionHandler completionHandler: @escaping () -> Void) {
        showAlert(withText: response.notification.request.content.body)
    }
    ```

1. Dodawanie instrukcji wydruku do dolnej części **didRegisterForRemoteNotificationsWithDeviceToken** funkcję, aby sprawdzić, czy **installationId** i **pushChannel** są przypisane wartości.

1. Tworzenie **modeli**, **usług**, i **narzędzia** folderów dla podstawowych składników można dodawać do projektu później.

1. Upewnij się, że projekt kompiluje i uruchamia na urządzeniu fizycznym. Nie można przetestować powiadomienia wypychane przy użyciu symulatora.

### <a name="create-models"></a>Tworzenie modeli

W tym kroku utworzysz zestaw modeli do reprezentowania [interfejsu API REST centrów powiadomień](/rest/api/notificationhubs/) ładunków i do przechowywania wymaganych dostępu współdzielonego podpisu (SAS) token danych.

1. Dodaj nowy plik Swift o nazwie **PushTemplate.swift** do **modeli** folderu. Ten model zawiera struktura przedstawiający **treści** poszczególnych szablonu w ramach **DeviceInstallation** ładunku.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Dodaj nowy plik Swift o nazwie **DeviceInstallation.swift** do **modeli** folderu. Ten plik definiuje strukturę reprezentujący ładunku do tworzenia lub aktualizowania **Instalacja urządzenia**. Dodaj następujący kod do pliku:

    ```swift
    import Foundation

    struct DeviceInstallation : Codable {
        let installationId : String
        let pushChannel : String
        let platform : String = "apns"
        var tags : [String]
        var templates : Dictionary<String, PushTemplate>

        init(withInstallationId installationId : String, andPushChannel pushChannel : String) {
            self.installationId = installationId
            self.pushChannel = pushChannel
            self.tags = [String]()
            self.templates = Dictionary<String, PushTemplate>()
        }
    }
    ```

1. Dodaj nowy plik Swift o nazwie **TokenData.swift** do **modeli** folderu. W tym modelu będzie służyć do przechowywania tokenu sygnatury dostępu Współdzielonego, wraz z jego wygaśnięcia.

    ```swift
    import Foundation

    struct TokenData {

        let token : String
        let expiration : Int

        init(withToken token : String, andTokenExpiration expiration : Int) {
            self.token = token
            self.expiration = expiration
        }
    }
    ```

### <a name="generate-a-sas-token"></a>Wygeneruj token sygnatury dostępu Współdzielonego

Usługa Notification Hubs korzystają z tej samej infrastruktury zabezpieczeń jako usługi Azure Service Bus. Aby wywołać interfejs API REST, konieczne będzie [programistycznego generowania tokenu sygnatury dostępu Współdzielonego](/rest/api/eventhub/generate-sas-token) mogą być używane w **autoryzacji** nagłówku żądania.  

Wynikowy token będzie miała następujący format:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Sam proces obejmuje te same kroki sześć klucza:  

1. Przetwarzanie wygaśnięcia w [czasie Uniksowym UNIX](https://en.wikipedia.org/wiki/Unix_time) format, co oznacza liczbę sekund, które upłynęły od północy uniwersalny czas koordynowany 1 stycznia 1970.
1. Formatowanie **ResourceUrl** reprezentujący zasób próbujesz uzyskać dostęp, więc jest zakodowany w formacie procent i małe litery. **ResourceUrl** ma postać `'https://<namespace>.servicebus.windows.net/<hubName>'`.
1. Trwa przygotowywanie **StringToSign**, który jest w formacie `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`.
1. Obliczeń i kodowanie Base64 **podpisu** przy użyciu skrótu HMAC SHA256 **StringToSign** wartości. Wartość skrótu jest używana z **klucz** wchodzi w skład **parametry połączenia** dla odpowiednich **reguły autoryzacji**.
1. Formatowanie zakodowane w formacie Base64 **podpisu** więc jest zakodowany w procentach.
1. Konstruowanie token w oczekiwanym formacie za pomocą **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**, i **UrlEncodedResourceUrl** wartości.

Zobacz [dokumentacji usługi Azure Service Bus](../service-bus-messaging/service-bus-sas.md) bardziej szczegółowe omówienie sygnatury dostępu współdzielonego i jak usługi Azure Service Bus i Notification Hubs go używać.

Na potrzeby tego przykładu Swift zamierzasz używać typu open source firmy Apple **CommonCrypto** biblioteki, aby pomóc w wyznaczania wartości skrótu podpisu. Po wykonaniu biblioteki C, nie jest dostępna w języku Swift gotowe. Biblioteki można udostępnić przy użyciu mostkowania nagłówka.

Aby dodać i skonfigurować mostkowanie nagłówka:

1. W programie Xcode, wybierz **pliku** > **New** > **pliku** > **plik nagłówkowy**. Nazwa pliku nagłówkowego **BridgingHeader.h**.

1. Edytuj plik do zaimportowania **CommonHMAC.h**:

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Aktualizowanie elementu docelowego **ustawieniach kompilacji** k odkazu nagłówka mostkowania:

   1. Otwórz **ustawienia tworzenia** karcie, a następnie przewiń w dół do **kompilator języka Swift** sekcji.

   1. Upewnij się, że **zainstalować nagłówka zgodność języka Objective-C** ustawiono opcję **tak**.

   1. Wprowadź ścieżkę do pliku `'<ProjectName>/BridgingHeader.h'` do **języka Objective-C, nagłówek mostkowania** opcji. Jest to ścieżka pliku do naszych nagłówka mostkowania.

   Jeśli nie możesz znaleźć tych opcji, upewnij się, że **wszystkich** widok wybrany zamiast **podstawowe** lub **dostosowany**.

   Brak dostępnych wiele bibliotek otoki typu open-source innych firm, które może spowodować, że za pomocą **CommonCrypto** nieco prostsze. Omówienie biblioteki jest jednak poza zakres tego artykułu.

1. Dodaj nowy plik Swift o nazwie **TokenUtility.swift** w ramach **narzędzia** folderze i Dodaj następujący kod:

   ```swift
   import Foundation

   struct TokenUtility {    
        typealias Context = UnsafeMutablePointer<CCHmacContext>

        static func getSasToken(forResourceUrl resourceUrl : String, withKeyName keyName : String, andKey key : String, andExpiryInSeconds expiryInSeconds : Int = 3600) -> TokenData {
            let expiry = (Int(NSDate().timeIntervalSince1970) + expiryInSeconds).description
            let encodedUrl = urlEncodedString(withString: resourceUrl)
            let stringToSign = "\(encodedUrl)\n\(expiry)"
            let hashValue = sha256HMac(withData: stringToSign.data(using: .utf8)!, andKey: key.data(using: .utf8)!)
            let signature = hashValue.base64EncodedString(options: .init(rawValue: 0))
            let encodedSignature = urlEncodedString(withString: signature)
            let sasToken = "SharedAccessSignature sr=\(encodedUrl)&sig=\(encodedSignature)&se=\(expiry)&skn=\(keyName)"
            let tokenData = TokenData(withToken: sasToken, andTokenExpiration: expiryInSeconds)

            return tokenData
        }

        private static func sha256HMac(withData data : Data, andKey key : Data) -> Data {
            let context = Context.allocate(capacity: 1)
            CCHmacInit(context, CCHmacAlgorithm(kCCHmacAlgSHA256), (key as NSData).bytes, size_t((key as NSData).length))
            CCHmacUpdate(context, (data as NSData).bytes, (data as NSData).length)
            var hmac = Array<UInt8>(repeating: 0, count: Int(CC_SHA256_DIGEST_LENGTH))
            CCHmacFinal(context, &hmac)

            let result = NSData(bytes: hmac, length: hmac.count)
            context.deallocate()

            return result as Data
        }

        private static func urlEncodedString(withString stringToConvert : String) -> String {
            var encodedString = ""
            let sourceUtf8 = (stringToConvert as NSString).utf8String
            let length = strlen(sourceUtf8)

            let charArray: [Character] = [ ".", "-", "_", "~", "a", "z", "A", "Z", "0", "9"]
            let asUInt8Array = String(charArray).utf8.map{ Int8($0) }

            for i in 0..<length {
                let currentChar = sourceUtf8![i]

                if (currentChar == asUInt8Array[0] || currentChar == asUInt8Array[1] || currentChar == asUInt8Array[2] || currentChar == asUInt8Array[3] ||
                    (currentChar >= asUInt8Array[4] && currentChar <= asUInt8Array[5]) ||
                    (currentChar >= asUInt8Array[6] && currentChar <= asUInt8Array[7]) ||
                    (currentChar >= asUInt8Array[8] && currentChar <= asUInt8Array[9])) {
                    encodedString += String(format:"%c", currentChar)
                }
                else {
                    encodedString += String(format:"%%%02x", currentChar)
                }
            }

            return encodedString
        }
    }
   ```

   To narzędzie hermetyzuje logikę odpowiedzialnego za wygenerowanie tokenu sygnatury dostępu Współdzielonego.

   Jak wskazano wcześniej **getSasToken** funkcja organizuje ogólne kroki wymagane w celu przygotowania do tokenu. Funkcja zostanie wywołana przez usługę instalacji w dalszej części tego samouczka.

   Te dwie funkcje są wywoływane przez **getSasToken** funkcja: **sha256HMac** dotyczących przeprowadzania obliczeń podpisu i **urlEncodedString** kodowania powiązanego adresu URL ciąg. **UrlEncodedString** funkcja jest wymagana, ponieważ nie jest możliwe uzyskanie wymaganych danych wyjściowych za pomocą wbudowanych **addingPercentEncoding** funkcji.

   [Usługi Azure Storage z systemem iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) jest przykładem doskonały sposób podejścia te operacje w Objective-C. Więcej informacji na temat tokenów SAS magistrali usług platformy Azure można znaleźć w [dokumentacji usługi Azure Service Bus](../service-bus-messaging/service-bus-sas.md).

### <a name="verify-the-sas-token"></a>Weryfikuj token sygnatury dostępu Współdzielonego

Przed zaimplementowaniem usługi instalacji klienta należy sprawdzić, że nasza aplikacja poprawnie generuje token sygnatury dostępu Współdzielonego za pomocą narzędzia usługi HTTP wybranego. Na potrzeby tego samouczka będzie nasze narzędzie wyboru **Postman**.

Należy pamiętać, za pomocą odpowiednio rozmieszczonych instrukcji drukowania lub punkt przerwania **installationId** i **tokenu** wartości generowanych przez aplikację.

Wykonaj następujące kroki, aby wywołać **instalacje** interfejsu API:

1. W **Postman**, otwórz nową kartę.

1. Ustawiono żądaniu **UZYSKAĆ** i podaj następujący adres:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. Skonfiguruj nagłówki żądania w następujący sposób:

   | Klucz           | Value            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Autoryzacja | \<sasToken>       |
   | x-ms-version  | 2015-01          |

1. Wybierz **kodu** przycisk, który pojawia się w prawym górnym rogu w obszarze **Zapisz** przycisku. Żądanie powinno wyglądać podobnie do poniższego przykładu:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. Wybierz **wysyłania** przycisku.

Rejestracja nie istnieje w określonym **installationId** w tym momencie. Weryfikacja powinno spowodować odpowiedź "404 Nie znaleziono", a nie na odpowiedź "401 Brak autoryzacji". Ten wynik należy upewnić się o przyjęciu tokenu sygnatury dostępu Współdzielonego.

### <a name="implement-the-installation-service-class"></a>Implementowanie klasy usługi instalacji

Następnie będzie implementowana naszych podstawowych otokę [interfejsu API REST instalacje](/rest/api/notificationhubs/create-overwrite-installation).  

Dodaj nowy plik Swift o nazwie **NotificationRegistrationService.swift** w obszarze **usług** folder, a następnie dodaj następujący kod do tego pliku:

```swift
import Foundation

class NotificationRegistrationService {
    private let tokenizedBaseAddress: String = "https://%@.servicebus.windows.net/%@"
    private let tokenizedCreateOrUpdateInstallationRequest = "/installations/%@?api-version=%@"
    private let session = URLSession(configuration: URLSessionConfiguration.default)
    private let apiVersion = "2015-01"
    private let jsonEncoder = JSONEncoder()
    private let defaultHeaders: [String : String]
    private let installationId : String
    private let pushChannel : String
    private let hubNamespace : String
    private let hubName : String
    private let keyName : String
    private let key : String
    private var tokenData : TokenData? = nil

    init(withInstallationId installationId : String,
            andPushChannel pushChannel : String,
            andHubNamespace hubNamespace : String,
            andHubName hubName : String,
            andKeyName keyName : String,
            andKey key: String) {
        self.installationId = installationId
        self.pushChannel = pushChannel
        self.hubNamespace = hubNamespace
        self.hubName = hubName
        self.keyName = keyName
        self.key = key
        self.defaultHeaders = ["Content-Type": "application/json", "x-ms-version": apiVersion]
    }

    func register(
        withTags tags : [String]? = nil,
        andTemplates templates : Dictionary<String, PushTemplate>? = nil,
        completeWith completion: ((_ result: Bool) -> ())? = nil) {

        var deviceInstallation = DeviceInstallation(withInstallationId: installationId, andPushChannel: pushChannel)

        if let tags = tags {
            deviceInstallation.tags = tags
        }

        if let templates = templates {
            deviceInstallation.templates = templates
        }

        if let deviceInstallationJson = encodeToJson(deviceInstallation) {
            let sasToken = getSasToken()
            let requestUrl = String.init(format: tokenizedCreateOrUpdateInstallationRequest, installationId, apiVersion)
            let apiEndpoint = "\(getBaseAddress())\(requestUrl)"

            var request = URLRequest(url: URL(string: apiEndpoint)!)
            request.httpMethod = "PUT"

            for (key,value) in self.defaultHeaders {
                request.addValue(value, forHTTPHeaderField: key)
            }

            request.addValue(sasToken, forHTTPHeaderField: "Authorization")
            request.httpBody = Data(deviceInstallationJson.utf8)

            (self.session.dataTask(with: request) { dat, res, err in
                if let completion = completion {
                        completion(err == nil && (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }

    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }

    private func getSasToken() -> String {
        if (tokenData == nil ||
            Date(timeIntervalSince1970: Double((tokenData?.expiration)!)) < Date(timeIntervalSinceNow: -(5 * 60))) {
            self.tokenData = TokenUtility.getSasToken(forResourceUrl: getBaseAddress(), withKeyName: self.keyName, andKey: self.key)
        }

        return (tokenData?.token)!
    }

    private func encodeToJson<T : Encodable>(_ object: T) -> String? {
        do {
            let jsonData = try jsonEncoder.encode(object)
            if let jsonString = String(data: jsonData, encoding: .utf8) {
                return jsonString
            } else {
                return nil
            }
        }
        catch {
            return nil
        }
    }
}
```

Wymagane szczegóły znajdują się w ramach inicjowania. Tagi i szablony opcjonalnie są przekazywane do **zarejestrować** funkcja część **Instalacja urządzenia** ładunek w formacie JSON.  

**Zarejestrować** funkcja wywołuje prywatne funkcje przygotować żądania. Po otrzymaniu odpowiedzi zakończenia nosi nazwę i wskazuje, czy rejestracja zakończyła się powodzeniem.  

Punkt końcowy żądania jest tworzony przez **getbaseaddress —** funkcji. Budowa używa parametrów Centrum powiadomień *przestrzeni nazw* i *nazwa* który dostarczono podczas inicjowania.  

**GetSasToken** funkcja sprawdza, czy aktualnie przechowywana token jest prawidłowy. Jeśli token jest nieprawidłowy, funkcja wywołuje **TokenUtility** można wygenerować nowy token, a następnie przechowuje przed zwróceniem wartości.

Na koniec **encodeToJson** konwertuje odpowiedni model obiektów JSON jako część treści żądania.

### <a name="invoke-the-notification-hubs-rest-api"></a>Wywoływanie interfejsu API REST usługi Notification Hubs

Ostatnim krokiem jest aktualizowany **elemencie AppDelegate** używać **NotificationRegistrationService** zarejestrować się w naszym **Centrum NotificationHub**.

1. Otwórz **AppDelegate.swift** i Dodaj zmienną poziomie klasy do przechowywania odwołań do **NotificationRegistrationService**:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

1. W tym samym pliku, należy zaktualizować **didRegisterForRemoteNotificationsWithDeviceToken** funkcji, aby zainicjować **NotificationRegistrationService** z wymaganymi parametrami, a następnie wywołania **zarejestrować** funkcji.

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})

        // Initialize the Notification Registration Service
        self.registrationService = NotificationRegistrationService(
            withInstallationId: installationId,
            andPushChannel: pushChannel,
            andHubNamespace: notificationHubNamespace!,
            andHubName: notificationHubName!,
            andKeyName: notificationHubKeyName!,
            andKey: notificationHubKey!)

        // Call register, passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    W celu uproszczenia go zamierzasz używać na kilka instrukcji wydruku do aktualizacji w oknie danych wyjściowych z wynikiem **zarejestrować** operacji.

1. Teraz tworzenie i uruchamianie aplikacji na urządzeniu fizycznym. Powinien zostać wyświetlony "Zarejestrowane" w oknie danych wyjściowych.

## <a name="test-the-solution"></a>Testowanie rozwiązania

Nasza aplikacja na tym etapie jest zarejestrowane w usłudze **Centrum NotificationHub** i może odbierać powiadomienia wypychane. W programie Xcode zatrzymaniu debugera i zamknij aplikację, jeśli jest ono aktualnie uruchomione. Następnie sprawdź, czy **Instalacja urządzenia** szczegóły są zgodne z oczekiwaniami i naszej aplikacji mogą teraz odbierać powiadomienia wypychane.  

### <a name="verify-the-device-installation"></a>Weryfikacja instalacji urządzenia

Można teraz wprowadzić z tego samego żądania, tak jak wcześniej przy użyciu **Postman** dla [weryfikacji tokenu sygnatury dostępu Współdzielonego](#verify-the-sas-token). Przy założeniu, że token sygnatury dostępu Współdzielonego nie upłynął, odpowiedź powinna teraz zawierać szczegółowe informacje dotyczące instalacji, które podałeś, takimi jak tagi i szablony.

```json
{
    "installationId": "<installationId>",
    "pushChannel": "<pushChannel>",
    "pushChannelExpired": false,
    "platform": "apns",
    "expirationTime": "9999-12-31T23:59:59.9999999Z",
    "tags": [
        "12345"
    ],
    "templates": {
        "genericTemplate": {
            "body": "{\"aps\":{\"alert\":\"$(message)\"}}",
            "tags": [
                "genericTemplate"
            ]
        }
    }
}
```

### <a name="send-a-test-notification-azure-portal"></a>Wyślij testowe powiadomienie e-mail (witryna Azure portal)

Najszybszym sposobem na test, mogą teraz otrzymać powiadomienia jest przejdź do Centrum powiadomień w witrynie Azure portal:

1. W witrynie Azure portal przejdź do **Przegląd** karty w Centrum powiadomień.

1. Wybierz **wysyłanie testowe**, czyli powyżej **Essentials** podsumowania w lewej górnej części okna portalu:

    ![Notification Hubs Essentials przycisk Wyślij podsumowanie testu](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. Wybierz **szablonu niestandardowego** z **platform** listy.

1. Wprowadź **12345** dla **Wyślij do wyrażenia tagu**. Ten tag miał wcześniej określoną w naszej instalacji.

1. Opcjonalnie Edytuj **komunikat** w ładunku JSON:

    ![Wysyłanie testowe centra powiadomień](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Wybierz pozycję **Wyślij**. Portal powinny wskazywać, czy powiadomienie zostało pomyślnie wysłane do urządzenia:

    ![Wyniki wysyłanie testowe centra powiadomień](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Przy założeniu, że aplikacja nie jest uruchomiona na pierwszym planie, powinno również zostać wyświetlone powiadomienie **Centrum powiadomień** na urządzeniu. Dotknięcie powiadomienia powinno powodować otwarcie aplikacji i wyświetlić alert.

    ![Otrzymano powiadomienie o przykładzie](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Wyślij testowe powiadomienie e-mail (poczty operatora)

Możesz wysłać powiadomienia za pośrednictwem [interfejsu API REST](/rest/api/notificationhubs/) przy użyciu **Postman**, które mogą być bardziej wygodne do testowania.

1. Otwórz nową kartę w **Postman**.

1. Ustawiono żądaniu **WPIS**, a następnie wprowadź następujący adres:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. Skonfiguruj nagłówki żądania w następujący sposób:

   | Klucz                            | Value                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | application/json;charset=utf-8 |
   | Autoryzacja                  | \<sasToken>                     |
   | ServiceBusNotification-Format  | Szablon                       |
   | `Tags`                           | "12345"                        |

1. Skonfiguruj żądanie **treści** używać **RAW - JSON (application.json)** za pomocą następujących ładunek w formacie JSON:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Wybierz **kodu** przycisku, który znajduje się w obszarze **Zapisz** przycisk w prawym górnym rogu okna. Żądanie powinno wyglądać podobnie do poniższego przykładu:

    ```html
    POST /<hubName>/messages/?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json;charset=utf-8.
    ServiceBusNotification-Format: template
    Tags: "12345"
    Authorization: <sasToken>
    Cache-Control: no-cache
    Postman-Token: <postmanToken>

    {
        "message" : "Hello from Postman!"
    }
    ```

1. Wybierz **wysyłania** przycisku.

Należy uzyskać pomyślny kod statusu i otrzymywać powiadomienia na urządzeniu klienckim.

## <a name="next-steps"></a>Kolejne kroki
Masz teraz podstawowe dla systemu iOS app Swift, podłączone do danego centrum powiadomień za pośrednictwem [interfejsu API REST](/rest/api/notificationhubs/) oraz wysyłać i odbierać powiadomienia. Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Omówienie usługi Azure Notification Hubs](notification-hubs-push-notification-overview.md)
- [Usługa Notification Hubs interfejsów API REST](/rest/api/notificationhubs/)
- [Zestaw SDK usługi Notification Hubs dla operacji zaplecza](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Usługa Notification Hubs zestawu SDK w witrynie GitHub](https://github.com/Azure/azure-notificationhubs)
- [Zarejestrowanie aplikacji zaplecza.](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Zarządzanie rejestracją](notification-hubs-push-notification-registration-management.md)
- [Praca z tagami](notification-hubs-tags-segment-push-message.md) 
- [Praca z szablonów niestandardowych](notification-hubs-templates-cross-platform-push-messages.md)
- [Kontrola dostępu usługi Service Bus przy użyciu sygnatury dostępu współdzielonego](../service-bus-messaging/service-bus-sas.md)
- [Programowe Generowanie tokenów sygnatur dostępu Współdzielonego](/rest/api/eventhub/generate-sas-token)
- [Zabezpieczeń firmy Apple: typowe kryptograficznego](https://developer.apple.com/security/)
- [Czas Uniksowy systemu UNIX](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
