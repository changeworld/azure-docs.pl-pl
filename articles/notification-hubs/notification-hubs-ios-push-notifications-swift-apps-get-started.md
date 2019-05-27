---
title: Powiadomienia wypychane do aplikacji Swift iOS przy użyciu usługi Azure Notification Hubs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wypychać powiadomienia do aplikacji Swift iOS przy użyciu usługi Azure Notification Hubs.
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
ms.openlocfilehash: 64a05c6cbd412953ae10b31efc1d141c9cefd062
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65994769"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-using-the-notification-hubs-rest-api"></a>Samouczek: Powiadomienia wypychane do aplikacji Swift iOS przy użyciu interfejsu API REST dla centrów powiadomień

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

W tym samouczku używasz usługi Azure Notification Hubs wysyłać powiadomienia wypychane do aplikacji systemu iOS opartych na języku Swift za pomocą [interfejsu API REST](/rest/api/notificationhubs/). Przy użyciu usługi [Apple Push Notification Service (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1) utworzysz pustą aplikację dla systemu iOS służącą do odbierania powiadomień push.

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Generowanie pliku żądania podpisania certyfikatu
> * Rejestrowanie aplikacji dla usługi powiadomień wypychanych
> * Tworzenie profilu inicjowania obsługi dla aplikacji
> * Tworzenie centrum powiadomień
> * Konfigurowanie Centrum powiadomień przy użyciu informacji z usługi APNS
> * Łączenie aplikacji dla systemu iOS z usługą Notification Hubs
> * Testowanie rozwiązania

## <a name="prerequisites"></a>Wymagania wstępne
W tym samouczku potrzebne są:

- Zapoznaj się z artykułem [Omówienie usługi Azure Notification Hubs](notification-hubs-push-notification-overview.md) Jeśli nie znasz usługi. 
- Więcej informacji na temat rejestracji oraz instalacji: [Zarządzanie rejestracją](notification-hubs-push-notification-registration-management.md)
- Aktywne [konta dewelopera firmy Apple](https://developer.apple.com) 
- Mac za pomocą edytora Xcode wraz z zainstalowanego w łańcuchu kluczy certyfikatu prawidłowy dla deweloperów
- Urządzenie fizyczne urządzenia iPhone możesz uruchamiać i debugować za pomocą (nie można przetestować powiadomienia wypychane z symulatorem)
- Urządzenie fizyczne urządzenia iPhone zarejestrowane w [portalu Apple](https://developer.apple.com) i skojarzonych z Twojego certyfikatu
- [Subskrypcji platformy Azure](https://portal.azure.com) gdzie tworzenie i zarządzanie zasobami

Należy umożliwić z niego skorzystać, aby utworzyć w tym przykładzie pierwsze zasad, bez wcześniejszego doświadczenia. Jednak, znajomość następujących pojęć jest korzystne:

- Tworzenie aplikacji dla systemu iOS za pomocą środowiska Xcode i Swift
- Konfigurowanie [Azure Notification Hub](notification-hubs-ios-apple-push-notification-apns-get-started.md) dla systemu iOS
- Znajomość [portalu Apple Developer Portal](https://developer.apple.com) i [witryny Azure portal](https://portal.azure.com)

> [!NOTE]
> Centrum powiadomień zostanie skonfigurowana w celu użycia *piaskownicy* tylko w trybie uwierzytelniania. Ten tryb uwierzytelniania nie należy używać w przypadku obciążeń produkcyjnych.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-notification-hubs"></a>Łączenie aplikacji dla systemu iOS z usługą Notification Hubs
W tej sekcji utworzysz aplikację dla systemu iOS, która połączy się z Centrum powiadomień.  

### <a name="create-an-ios-project"></a>Tworzenie projektu systemu iOS
1. W **Xcode**, Utwórz nowy projekt dla systemu iOS i wybierz **aplikacja pojedynczego widoku** szablonu.
2. Podczas określania opcji dla nowego projektu, wykonaj następujące kroki:
    1. Użyto tych samych **nazwa produktu** (oznacza to, **PushDemo**) i **identyfikator organizacji** (czyli `com.<organization>`) używane podczas **pakietu Identyfikator** została ustawiona w **portalu Apple Developer Portal**. 
    2. Wybierz **zespołu** , **Identyfikatora aplikacji** zostało skonfigurowane pod kątem.
    3. Ustaw **języka** do **Swift**.
    4. Kliknij przycisk **Dalej**
3. Utwórz nowy folder o nazwie **SupportingFiles**.
4. Utwórz nową **plist** pliku o nazwie **devsettings.plist** w obszarze **SupportingFiles** folderu. Pamiętaj dodać ten folder do Twojej **gitignore** pliku, dzięki czemu nie jest zatwierdzona podczas pracy z **repozytorium git**. W aplikacji produkcyjnej użytkownik będzie prawdopodobnie można warunkowo ustawienie tych kluczy tajnych w ramach zautomatyzowanego procesu kompilacji. Jednak to nie obejmuje jako część tego przewodnika.
5. Aktualizacja **devsettings.plist** do obejmują następujące pozycje konfiguracji (przy użyciu wartości z **Centrum powiadomień** można zainicjować obsługi administracyjnej):

   | Klucz                            | Type                     | Wartość                     |               
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | String                   | \<hubKey\>                |
   | notificationHubKeyName         | String                   | \<hubKeyName\>            |
   | notificationHubName            | String                   | \<hubName\>               |
   | notificationHubNamespace       | String                   | \<hubNamespace\>          |
    
   Wymagane wartości można znaleźć, przechodząc do **Centrum powiadomień** zasobu w **witryny Azure portal**. Możesz znaleźć **notificationHubName** i **notificationHubNamespace** wartości w prawym górnym rogu **Essentials** podsumowania w ramach  **Omówienie** strony.

   ![Podsumowanie Essentials centra powiadomień](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Możesz znaleźć **notificationHubKeyName** i **notificationHubKey** wartości, przechodząc do **zasady dostępu**, klikając na odpowiednie **dostępu Zasady**. Na przykład `DefaultFullSharedAccessSignature`. Następnie skopiuj z **podstawowe parametry połączenia** wartość prefiks `SharedAccessKeyName=` dla `notificationHubKeyName` i wartość prefiks `SharedAccessKey=` dla `notificationHubKey`. Parametry połączenia powinny być w następującym formacie:
    
   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Aby zachować prostotę, użyjesz *DefaultFullSharedAccessSignature* aby można było używać tokenu do wysyłania powiadomień, jak również. Jednak w praktyce `DefaultListenSharedAccessSignature` będzie lepszym rozwiązaniem dla sytuacji, gdy tylko do odbierania powiadomień.       
6. W obszarze **Nawigatora projektu**, kliknij przycisk **Nazwa projektu**, następnie kliknij przycisk **ogólne** kartę
7. Znajdź **tożsamości**, a następnie ustaw **identyfikatora pakietu** wartość tak, aby odpowiadała używaną dla **Identyfikatora aplikacji** (w poprzednim kroku) oznacza to, `'com.<organization>.PushDemo'`
8. Znajdź **podpisywanie**, upewnij się, możesz wybrać odpowiednie **zespołu** dla Twojego **konto programu Apple Developer** (znajdujący się w ramach której została utworzona z certyfikatów i profilów wcześniej).  **Środowisko Xcode** powinno automatycznie ściągnąć odpowiednie **profilu aprowizacji** na podstawie **identyfikatora pakietu**. Jeśli nie widzisz nowy **profilu aprowizacji**, Odśwież profile dla **tożsamość podpisywania** (*Xcode > Preferencje > konta > Wyświetl szczegóły*). Kliknięcie **tożsamość podpisywania**, klikając **Odśwież** przycisk w prawym dolnym rogu powinien pobierać profile.
9. Wybierz **możliwości** kartę i upewnij się, że **powiadomienia wypychane** są włączone.
10. Otwórz swoje **AppDelegate.swift** plik, aby zaimplementować *UNUserNotificationCenterDelegate* protokołu i Dodaj następujący kod na początku klasy:
    
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

    Później użyjesz tych elementów członkowskich. *Tagi* i *genericTemplate* będą używane w ramach rejestracji. Aby uzyskać więcej informacji na temat tagów, zobacz [tagi do rejestracji](notification-hubs-tags-segment-push-message.md) i [Rejestracja szablonu](notification-hubs-templates-cross-platform-push-messages.md).
 
11. W tym samym pliku Dodaj następujący kod w *didFinishLaunchingWithOptions* funkcji:

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

    Ten kod pobiera wartości ustawienie z **devsettings.plist**, ustawia **elemencie AppDelegate** klasy *UNUserNotificationCenter* delegować żądania autoryzacji powiadomienia wypychane, a następnie wywołania *registerForRemoteNotifications*.
    
    Aby zachować prostotę, obsługuje kod **system iOS 10 lub powyżej tylko**. Aby dodać obsługę starszych wersji systemu operacyjnego, należy warunkowo przy użyciu odpowiedniej metody i interfejsy API, jak zwykle.

12. W tym samym pliku Dodaj następujące funkcje:

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

    Kod używa *installationId* i *pushChannel* wartości, aby zarejestrować za pomocą **Centrum powiadomień**. W tym przypadku używasz *UIDevice.current.identifierForVendor* udostępnienia unikatową wartość, aby zidentyfikować urządzenia, a następnie go sformatuj *deviceToken* do przekazywania nam żądaną *pushChannel* wartości. *ShowAlert* funkcji jest po prostu wyświetlanie część tekstu komunikatu w celach demonstracyjnych.

13. Nadal w **AppDelegate.swift**, Dodaj *willPresent* i *didReceive* **UNUserNotificationCenterDelegate** funkcje Wyświetl alert, gdy dotarła powiadomienie, gdy aplikacja jest uruchomiona w pierwszego planu i tła odpowiednio
    
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

14. Dodawanie instrukcji wydruku do dolnej części *didRegisterForRemoteNotificationsWithDeviceToken* funkcję, aby sprawdzić, czy *installationId* i *pushChannel* są przypisane wartości
15. Tworzenie folderów (**modeli**, **usług**, i **narzędzia**) dla podstawowych składników można dodawać do projektu później
16. Sprawdź, czy projekt kompiluje i uruchamia na urządzeniu fizycznym (push, których nie można przetestować powiadomienia za pomocą symulatora)

### <a name="create-models"></a>Tworzenie modeli
W tym kroku utworzysz zestaw modeli do reprezentowania [interfejsu API REST centrów powiadomień](/rest/api/notificationhubs/) ładunków i do przechowywania wymaganych **tokenu sygnatury dostępu Współdzielonego** danych.


1.  Dodaj nowy plik swift, aby **modeli** o nazwie **PushTemplate.swift**. Ten model zawiera struktura przedstawiający **treści** poszczególnych szablonu w ramach **DeviceInstallation** ładunek:
    
    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String
    
        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2. Dodaj nowy plik swift, aby **modeli** folder o nazwie **DeviceInstallation.swift**. Ten plik definiuje strukturę reprezentujący ładunku do tworzenia lub aktualizowania **Instalacja urządzenia**. Dodaj następujący kod do pliku:
    
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

3.  Dodaj nowy plik swift w obszarze **modeli** o nazwie **TokenData.swift**. W tym modelu będzie służyć do przechowywania **tokenu sygnatury dostępu Współdzielonego** wraz z jego wygaśnięcia

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
**Usługa Notification Hubs** korzystają z tej samej infrastruktury zabezpieczeń jako **usługi Azure Service Bus**. Aby wywołać interfejs API REST, konieczne będzie [programistycznego generowania tokenu sygnatury dostępu Współdzielonego](/rest/api/eventhub/generate-sas-token) mogą być używane w **autoryzacji** nagłówku żądania.  

Wynikowy token będzie miała następujący format: 

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Sam proces obejmuje te same kroki sześć klucza:  

1.  Przetwarzanie wygaśnięcia w [czasie Uniksowym UNIX](https://en.wikipedia.org/wiki/Unix_time) formatu (w sekundach, jaki upłynął od 00:00:00 UTC 1 stycznia 1970 r.)
2.  Formatowanie **ResourceUrl** (reprezentujące zasób próbujesz uzyskać dostęp, czyli `'https://\<namespace\>.servicebus.windows.net/\<hubName\>'`), dzięki czemu jest zakodowany w formacie procent i małe litery
3.  Trwa przygotowywanie **StringToSign**, który składa się z `'\<**UrlEncodedResourceUrl**\>\n\<**ExpiryEpoch**\>'`
4.  Przetwarzanie (i podstawowej kodowanie 64) **podpisu** przy użyciu **HMAC SHA256** z **StringToSign** wartością **klucz** wchodzi w skład **Parametry połączenia** (dla odpowiednich **reguły autoryzacji**)
5.  Formatowanie base 64 zakodowane **podpisu** więc procent, kodowane
6.  Konstruowanie **tokenu** w oczekiwanym formacie za pomocą **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**i **UrlEncodedResourceUrl** wartości

Zobacz [dokumentacji usługi Azure Service Bus](../service-bus-messaging/service-bus-sas.md) bardziej szczegółowe omówienie **sygnatura dostępu współdzielonego** i sposobie ich użycia przez **usługi Azure Service Bus** i  **Usługa Notification Hubs**.

Na potrzeby tego przykładu Swift zamierzasz używać typu open source firmy Apple **CommonCrypto** biblioteki, aby pomóc w wyznaczania wartości skrótu podpisu. Po wykonaniu biblioteki C, nie jest dostępna w Swift out-of--box. Jednakże możesz udostępnić to przy użyciu nagłówka mostkowania. Aby dodać i skonfigurować mostkowanie nagłówka:

1. W **Xcode**, przejdź do **pliku**, następnie **New**, następnie **pliku** i wybierz **plik nagłówkowy** nadawania mu nazwy *"BridgingHeader.h"*
2. Edytuj plik do zaimportowania **CommonHMAC**

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

3. Aktualizowanie elementu docelowego **ustawieniach kompilacji** k odkazu mostkowania nagłówka. Otwórz **ustawienia tworzenia** karcie, a następnie przewiń w dół do **kompilator języka Swift** sekcji. Upewnij się, że **zainstalować nagłówka zgodność języka Objective-C** opcja równa **tak** i wprowadź ścieżkę pliku do naszych nagłówka mostkowania w **mostkowanie nagłówka Objective-C**   opcji, czyli `'\<ProjectName\>/BridgingHeader.h'`. Jeśli nie możesz znaleźć tych opcji, upewnij się, że **wszystkich** wybrany widok (zamiast **podstawowe** lub **dostosowany**).
    
   Są dostępne, wiele bibliotek otoki typu open-source innych firm, które może spowodować, że za pomocą **CommonCrypto** nieco prostsze. Jest on poza zakres tego artykułu.

4. Dodaj nowy plik Swift w obszarze **narzędzia** folder o nazwie **TokenUtility.swift** i Dodaj następujący kod:

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
    
    To narzędzie hermetyzuje logikę odpowiedzialnego za wygenerowanie **tokenu sygnatury dostępu Współdzielonego**. *GetSasToken* funkcji organizuje ogólne kroki wymagane w celu przygotowania tokenu, co zostało opisane wcześniej i zostanie wywołana przez usługę instalacji w krokach w dalszej części tego samouczka. Te dwie funkcje są wywoływane przez *funkcja getSasToken*; *sha256HMac* dotyczących przeprowadzania obliczeń podpisu i *urlEncodedString* kodowania odpowiednich ciągu adresu Url. *UrlEncodedString* funkcja była wymagana, ponieważ to nie było możliwe uzyskanie wymaganych danych wyjściowych za pomocą wbudowanych *addingPercentEncoding* funkcji. [Usługi Azure Storage z systemem iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) obsługiwane na przykład doskonały sposób podejścia te operacje, chociaż w Objective-C. Więcej informacji na **tokenów SAS magistrali usług Azure** znajdują się w [dokumentacji usługi Azure Service Bus](../service-bus-messaging/service-bus-sas.md). 

### <a name="verify-the-sas-token"></a>Weryfikuj token sygnatury dostępu Współdzielonego
Przed zaimplementowaniem usługi instalacji klienta można sprawdzić poprawnie generuje naszą aplikację **tokenu sygnatury dostępu Współdzielonego** za pomocą narzędzia usługi http wyboru. Na potrzeby tego wpisu będzie nasze narzędzie wyboru **Postman**.

Zwróć uwagę na *installationId* i *tokenu* wartości generowanych przez aplikację za pomocą odpowiednio rozmieszczonych drukowanie instrukcji lub punkt przerwania. 

Wykonaj następujące kroki, aby wywołać *instalacje* interfejsu API:

1. W **Postman**, otwórz nową kartę
2. Ustawiono żądaniu **UZYSKAĆ** i następującego adresu:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

3. Skonfiguruj nagłówki żądania w następujący sposób:
    
   | Klucz           | Wartość            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Autoryzacja | \<sasToken\>     |
   | x-ms-version  | 2015-01          |

4. Kliknij pozycję **kodu** przycisku (prawym górnym rogu w obszarze **Zapisz** przycisk). Żądanie powinno wyglądać podobnie do poniższego przykładu:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

5. Kliknij przycisk **wysyłania** przycisku

Rejestracja nie istnieje w określonym *installationId* w tym momencie, ale powinna być rozwiązywana we **404 Nie znaleziono** odpowiedzi zamiast **401 nieautoryzowane**. Ten wynik powinien upewnij się, że **tokenu sygnatury dostępu Współdzielonego** zostało zaakceptowane.

### <a name="implement-the-installation-service-class"></a>Implementowanie klasy usługi instalacji
Następnie będzie implementowana naszych podstawowych otokę [interfejsu API REST instalacje](/rest/api/notificationhubs/create-overwrite-installation).  

Dodaj nowy plik Swift w obszarze **usług** folder o nazwie **NotificationRegistrationService.swift**, następnie dodaj następujący kod do tego pliku:

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
 
Wymagane szczegóły znajdują się w ramach inicjowania. Tagi i szablony opcjonalnie są przekazywane do *zarejestrować* funkcja część **Instalacja urządzenia** ładunek w formacie JSON.  

*Zarejestrować* funkcja wywołuje prywatne funkcje przygotować żądania. Po otrzymaniu odpowiedzi zakończenia nosi nazwę wskazującą, czy rejestracja zakończyła się powodzeniem.  

Punkt końcowy żądania jest tworzony przez *getbaseaddress —* funkcję za pomocą **Centrum powiadomień** parametry **przestrzeni nazw** i **nazwa**świadczona inicjacji.  

*GetSasToken* funkcja sprawdzi, czy aktualnie przechowywana token jest prawidłowy, w przeciwnym razie go będzie wyróżnienia do **TokenUtility** można wygenerować nowy i umieszczenie go przed zwróceniem wartości. 

Na koniec *encodeToJson* przekonwertuje odpowiedni model obiektów do postaci JSON, jako część treści żądania.

### <a name="invoke-the-notification-hubs-rest-api"></a>Wywoływanie interfejsu API REST usługi Notification Hubs
Ostatnim krokiem jest zaktualizowanie **elemencie AppDelegate** używać **NotifiationRegistrationService** zarejestrować się w naszym **Centrum NotificationHub**. 

1. Otwórz **AppDelegate.swift** i Dodaj zmienną poziomie klasy do przechowywania odwołań do **NoficiationRegistrationService**:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

2. W tym samym pliku, należy zaktualizować *didRegisterForRemoteNotificationsWithDeviceToken* funkcji, aby zainicjować **NotificationRegistrationService** z wymaganymi parametrami, a następnie wywołać *zarejestrować* funkcji.

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
    
        // Call register passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    W celu uproszczenia go zamierzasz używać na kilka instrukcji wydruku do aktualizacji w oknie danych wyjściowych z wynikiem *zarejestrować* operacji. 

3. Teraz tworzenie i uruchamianie aplikacji (na urządzeniu fizycznym). Powinien zostać wyświetlony **"Zarejestrowano"** w oknie danych wyjściowych.

## <a name="test-the-solution"></a>Testowanie rozwiązania
Na tym etapie nasza aplikacja jest zarejestrowana w **Centrum NotificationHub** i może odbierać powiadomienia wypychane. W **Xcode**, zatrzymaniu debugera i zamknij aplikację (jeśli jest ono aktualnie uruchomione). Następnie sprawdzisz, **Instalacja urządzenia** szczegóły są zgodne z oczekiwaniami oraz że nasza aplikacja może odbierać rzeczywiście obecnie powiadomienia wypychane.  

### <a name="verify-the-device-installation"></a>Weryfikacja instalacji urządzenia
Można teraz wprowadzić z tego samego żądania, tak jak wcześniej przy użyciu **Postman** dla [weryfikacji tokenu sygnatury dostępu Współdzielonego](#verify-the-sas-token). Przy założeniu, że **tokenu sygnatury dostępu Współdzielonego** nie uznawane za wygasłe, odpowiedź powinna teraz zawierać szczegółowe informacje dotyczące instalacji, podać takie jak szablony i tagów.  

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
Najszybszym sposobem testu, mogą teraz otrzymać powiadomienia jest można przejść do **Centrum powiadomień** w **witryny Azure portal**.

1. W **witryny Azure portal**, przejdź do **Przegląd** karcie swoje **Centrum powiadomień**
2. Kliknij przycisk **wysyłanie testowe** (lewy górny) powyżej **Essentials** podsumowania

    ![Notification Hubs Essentials przycisk Wyślij podsumowanie testu](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)
3. Wybierz **szablonu niestandardowego** z listy **platformy**
4. Wprowadź **12345** dla **Wyślij do wyrażenia tagu** (w naszym instalacji Gdybyśmy ustawili ten tag)
5. Opcjonalnie można edytować **komunikat** w ładunku JSON
    
    ![Wysyłanie testowe centra powiadomień](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)
6. Kliknij przycisk **wysyłania** i portalu powinny wskazywać, czy powiadomienie zostało pomyślnie wysłane do urządzenia

    ![Wyniki wysyłanie testowe centra powiadomień](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Powinno również zostać wyświetlone powiadomienie **Centrum powiadomień** na urządzeniu (przy założeniu, że aplikacja nie jest uruchomiona na pierwszym planie). Naciskając powiadomienia powinno powodować otwarcie aplikacji i wyświetlić alert.

    ![Otrzymano powiadomienie o przykładzie](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-postman"></a>Wyślij testowe powiadomienie e-mail (Postman)
Możesz wysłać powiadomienia za pośrednictwem odpowiednich [interfejsu API REST](/rest/api/notificationhubs/) za pośrednictwem **Postman** jak dobrze i może być bardziej wygodne do testowania. 

1. W **Postman**, otwórz nową kartę
2. Ustawiono żądaniu **WPIS** i wprowadź następujący adres:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

3. Skonfiguruj nagłówki żądania w następujący sposób:
    
   | Klucz                            | Wartość                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | application/json;charset=utf-8 |
   | Autoryzacja                  | \<sasToken\>                   |
   | ServiceBusNotification-Format  | szablon                       |
   | `Tags`                           | "12345"                        |

4. Skonfiguruj żądanie **treści** używać **RAW - JSON (application.json)** za pomocą następujących ładunek w formacie JSON:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

5. Kliknij pozycję **kodu** przycisku (prawym górnym rogu w obszarze **Zapisz** przycisk). Żądanie powinno wyglądać podobnie do poniższego przykładu:

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

5. Kliknij przycisk **wysyłania** przycisku

Należy uzyskać pomyślny kod statusu i otrzymywać powiadomienia na urządzeniu klienckim.

## <a name="next-steps"></a>Kolejne kroki
Masz teraz aplikację Swift podstawowe dla systemu iOS połączone **Centrum powiadomień** za pośrednictwem [interfejsu API REST](/rest/api/notificationhubs/) oraz wysyłać i odbierać powiadomienia. Aby uzyskać więcej informacji zobacz następujące artykuły: 

- [Omówienie usługi Azure Notification Hubs](notification-hubs-push-notification-overview.md)
- [Usługa Notification Hubs interfejsów API REST](/rest/api/notificationhubs/)
- [Zestaw SDK usługi Notification Hubs dla operacji wewnętrznej bazy danych](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Usługa Notification Hubs zestawu SDK w witrynie GitHub](https://github.com/Azure/azure-notificationhubs)
- [Zarejestruj za pomocą zaplecza aplikacji](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Zarządzanie rejestracją](notification-hubs-push-notification-registration-management.md)
- [Praca z tagami](notification-hubs-tags-segment-push-message.md) 
- [Praca z szablonów niestandardowych](notification-hubs-templates-cross-platform-push-messages.md)
- [Kontrola dostępu usługi Service Bus przy użyciu sygnatury dostępu współdzielonego](../service-bus-messaging/service-bus-sas.md)
- [Programowe Generowanie tokenów sygnatur dostępu Współdzielonego](/rest/api/eventhub/generate-sas-token)
- [Zabezpieczeń firmy Apple: typowe kryptograficznego](https://developer.apple.com/security/)
- [Czas Uniksowy systemu UNIX](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
