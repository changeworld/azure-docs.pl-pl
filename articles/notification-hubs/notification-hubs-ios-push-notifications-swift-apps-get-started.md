---
title: Wysyłanie powiadomień wypychanych do aplikacji Swift na iOS korzystających z centrów powiadomień platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak wypychać powiadomienia do aplikacji Swift na iOS korzystających z usługi Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: femila
editor: jwargo
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: a721c519c7a836e20455c6f1887bcfa7b52951f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336638"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-api"></a>Samouczek: Wysyłanie powiadomień wypychanych do aplikacji Swift na iOS przy użyciu interfejsu API REST centrum powiadomień

> [!div class="op_single_selector"]
> * [Obiektowy C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

W tym samouczku usługi Azure Notification Hubs służy do wypychania powiadomień do aplikacji systemu iOS opartej na platformie Swift przy użyciu [interfejsu API REST](/rest/api/notificationhubs/). Można również utworzyć pustą aplikację na iOS, która odbiera powiadomienia wypychane przy użyciu [usługi Apple Push Notification (APN).](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)

W tym samouczku przedstawiono następujące kroki:

> [!div class="checklist"]
> * Wygeneruj plik żądania podpisywania certyfikatów.
> * Poproś aplikację o powiadomienia wypychane.
> * Utwórz profil inicjowania obsługi administracyjnej dla aplikacji.
> * Tworzenie centrum powiadomień.
> * Skonfiguruj centrum powiadomień za pomocą informacji o apn.
> * Połącz aplikację z systemem iOS z centrum powiadomień.
> * Przetestuj roztwór.

Pełny kod dla tego samouczka można znaleźć [na GitHub](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_rest).

## <a name="prerequisites"></a>Wymagania wstępne

Aby podążać za nimi, potrzebujesz:

- Aby przejść przez [usługi Azure Notification Hubs omówienie,](notification-hubs-push-notification-overview.md) jeśli nie są zaznajomieni z usługą.
- Aby dowiedzieć się więcej o [rejestracjach i instalacji](notification-hubs-push-notification-registration-management.md).
- Aktywne [konto dewelopera Apple](https://developer.apple.com).
- Komputer Mac z systemem Xcode wraz z prawidłowym certyfikatem dewelopera zainstalowanym w pęku kluczy.
- Fizyczne urządzenie iPhone, które można uruchomić i debugować z, jak nie można przetestować powiadomienia push z symulatorem.
- Fizyczne urządzenie na iPhone'a zarejestrowane w [portalu Apple Portal](https://developer.apple.com) i skojarzone z certyfikatem.
- [Subskrypcja platformy Azure,](https://portal.azure.com) w której można tworzyć zasoby i zarządzać nimi.

Nawet jeśli nie masz wcześniejszego doświadczenia z programem iOS, powinieneś być w stanie wykonać kroki tworzenia tego przykładu pierwszych zasad. Jednak skorzystasz ze znajomości następujących pojęć:

- Tworzenie aplikacji na iOS za pomocą Xcode i Swift.
- Konfigurowanie [centrum powiadomień platformy Azure](notification-hubs-ios-apple-push-notification-apns-get-started.md) dla systemu iOS.
- [Portal deweloperów Firmy Apple](https://developer.apple.com) i portal [Azure](https://portal.azure.com).

> [!NOTE]
> Centrum powiadomień zostanie skonfigurowane do używania tylko trybu uwierzytelniania **piaskownicy.** Nie należy używać tego trybu uwierzytelniania dla obciążeń produkcyjnych.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>Łączenie aplikacji na iOS z centrum powiadomień

W tej sekcji skompilujesz aplikację dla systemu iOS, która połączy się z Centrum powiadomień.  

### <a name="create-an-ios-project"></a>Tworzenie projektu systemu iOS

1. W środowisku Xcode utwórz nowy projekt dla systemu iOS i wybierz szablon **Single View Application** (Aplikacja z jednym widokiem).

1. Podczas ustawiania opcji dla nowego projektu:

   1. Określ **nazwę produktu** (PushDemo) i identyfikator **organizacji** (`com.<organization>`) używane podczas **ustawiania identyfikatora pakietu** w portalu Apple Developer Portal.

   1. Wybierz **zespół,** dla których skonfigurowano **identyfikator aplikacji.**

   1. Ustaw **język** na **Swift**.

   1. Wybierz **pozycję Dalej**.

1. Utwórz nowy folder o nazwie **SupportingFiles**.

1. Utwórz nowy plik listy p o nazwie **devsettings.plist** w folderze **SupportingFiles.** Pamiętaj, aby dodać ten folder do pliku **gitignore,** aby nie został popełniony podczas pracy z repozytorium git. W aplikacji produkcyjnej prawdopodobnie warunkowo ustawienie tych wpisów tajnych w ramach procesu kompilacji zautomatyzowanej. Takie ustawienia nie są omówione w tym instruktażu.

1. Zaktualizuj **devsettings.plist,** aby uwzględnić następujące wpisy konfiguracji przy użyciu własnych wartości z aprowizacji centrum powiadomień:

   | Klucz                            | Typ                     | Wartość                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | Ciąg                   | \<> hubKey                  |
   | notificationHubKeyName         | Ciąg                   | \<> hubKeyName              |
   | notificationHubName            | Ciąg                   | \<> hubName                 |
   | powiadomienieNameks       | Ciąg                   | \<> hubNamespace            |

   Wymagane wartości można znaleźć, przechodząc do zasobu centrum powiadomień w witrynie Azure portal. W szczególności **notificationHubName** i **notificationHubNamespace** wartości znajdują się w prawym górnym rogu podsumowanie **Essentials** w **ramach przeglądu** strony.

   ![Podsumowanie programu Notification Hubs Essentials](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Można również znaleźć **notificationHubKeyName** i **notificationHubKey** wartości, przechodząc do **zasad dostępu** i wybierając odpowiednie zasady **dostępu,** takie jak `DefaultFullSharedAccessSignature`. Następnie skopiuj z **podstawowego ciągu** `SharedAccessKeyName=` połączenia `notificationHubKeyName` wartość poprzedzony `SharedAccessKey=` i `notificationHubKey`wartość poprzedzony dla .

   Parametry połączenia powinny być w następującym formacie:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Aby było proste, `DefaultFullSharedAccessSignature` należy określić, aby można było użyć tokenu do wysyłania powiadomień. W praktyce `DefaultListenSharedAccessSignature` byłoby to lepszym wyborem w sytuacjach, w których chcesz otrzymywać tylko powiadomienia.

1. W **obszarze Nawigator projektu**wybierz nazwę **projektu,** a następnie wybierz kartę **Ogólne.**

1. Znajdź **tożsamość,** a następnie ustaw wartość `com.<organization>.PushDemo` **identyfikatora pakietu,** tak aby była zgodna , która jest wartością używaną dla **identyfikatora aplikacji** z poprzedniego kroku.

1. Znajdź **możliwości podpisywania &**, a następnie wybierz odpowiedni **zespół** dla swojego **konta programisty Apple**. Wartość **Team** powinna być zgodna z wartością, w ramach której utworzono certyfikaty i profile.

1. Xcode powinien automatycznie ściągnąć w dół odpowiednią wartość **profilu inicjowania obsługi administracyjnej** na podstawie **identyfikatora pakietu**. Jeśli nie widzisz nowej wartości **profilu inicjowania obsługi administracyjnej,** spróbuj odświeżyć profile **tożsamości podpisywania,** wybierając**konto** **preferencji** >  **Xcode,** > a następnie wybierz przycisk **Pobierz profile ręczne,** aby pobrać profile.

1. Nadal na karcie **Podpisywanie & możliwości** kliknij przycisk **+ Możliwości** i naciśnij dwukrotnie **powiadomienia wypychane** z listy, aby upewnić się, że **powiadomienia wypychane** są włączone.

1. Otwórz plik **AppDelegate.swift,** aby zaimplementować protokół **UNUserNotificationCenterDelegate** i dodaj następujący kod do górnej części klasy:

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
        
        ...
    }
    ```

    Użyjesz tych członków później. W szczególności **użyjesz** elementu członkowskiego tagów w ramach rejestracji przy użyciu **szablonu niestandardowego.** Aby uzyskać więcej informacji na temat tagów, zobacz [Tagi rejestracji](notification-hubs-tags-segment-push-message.md) i [rejestracji szablonów](notification-hubs-templates-cross-platform-push-messages.md).

1. W tym samym pliku dodaj następujący kod do funkcji **didFinishLaunchingWithOptions:**

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

    Ten kod pobiera wartości ustawień z **devsettings.plist**, ustawia **AppDelegate** klasy jako **delegata UNUserNotificationCenter,** żąda autoryzacji powiadomień wypychanych, a następnie wywołuje **registerForRemoteNotifications**.

    Aby zachować prostotę, kod obsługuje *tylko system iOS 10 i nowsze*. Można dodać obsługę poprzednich wersji systemu operacyjnego, warunkowo przy użyciu odpowiednich interfejsów API i metod, jak zwykle.

1. W tym samym pliku dodaj następujące funkcje:

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        let keyWindow = UIApplication.shared.windows.filter {$0.isKeyWindow}.first
        keyWindow?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    Kod używa **installationId** i **pushChannel** wartości do rejestracji w centrum powiadomień. W takim przypadku używasz **UIDevice.current.identifierForVendor,** aby zapewnić unikatową wartość do identyfikowania urządzenia, a następnie formatowanie **deviceToken,** aby zapewnić żądaną wartość **pushChannel.** Funkcja **showAlert** istnieje po prostu po to, aby wyświetlić tekst wiadomości w celach demonstracyjnych.

1. Nadal w pliku **AppDelegate.swift,** dodaj **willPresent** i **didReceive** funkcje **UNUserNotificationCenterDelegate**. Te funkcje wyświetlają alert, gdy zostaną powiadomieni, że aplikacja działa odpowiednio na pierwszym planie lub w tle.

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

1. Dodaj instrukcje drukowania na dole **funkcji didRegisterForRemoteNotificationsWithDeviceToken,** aby sprawdzić, czy **installationId** i **pushChannel** są przypisane wartości.

    ```swift
    print(installationId)
    print(pushChannel)
    ```

1. Utwórz **foldery Modele,** **Usługi**i **Narzędzia** dla podstawowych składników, które zostaną dodane do projektu później.

1. Sprawdź, czy projekt tworzy i działa na urządzeniu fizycznym. Powiadomień wypychanych nie można przetestować przy użyciu symulatora.

### <a name="create-models"></a>Tworzenie modeli

W tym kroku utworzysz zestaw modeli do [reprezentowania ładunków interfejsu API REST centrum powiadomień](/rest/api/notificationhubs/) i przechowywania danych tokenu wymaganego podpisu dostępu udostępnionego (SAS).

1. Dodaj nowy plik Swift o nazwie **PushTemplate.swift** do folderu **Modele.** Ten model zapewnia strukturę reprezentującą **treść** pojedynczego szablonu jako część ładunku **DeviceInstallation.**

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Dodaj nowy plik Swift o nazwie **DeviceInstallation.swift** do folderu **Modele.** Ten plik definiuje strukturę reprezentującą ładunek do tworzenia lub aktualizowania **instalacji urządzenia**. Dodaj następujący kod do pliku:

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

1. Dodaj nowy plik Swift o nazwie **TokenData.swift** do folderu **Modele.** Ten model będzie używany do przechowywania tokenu Sygnatury dostępu Współdzielonego wraz z jego wygaśnięciem.

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

### <a name="generate-a-sas-token"></a>Generowanie tokenu SAS

Centra powiadomień korzystają z tej samej infrastruktury zabezpieczeń co usługa Azure Service Bus. Aby wywołać interfejs API REST, musisz [programowo wygenerować token sygnatury dostępu Współdzielonego,](/rest/api/eventhub/generate-sas-token) który może być używany w nagłówku **autoryzacji** żądania.  

Wynikowy token będzie w następującym formacie:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Sam proces obejmuje te same sześć kluczowych kroków:  

1. Obliczanie wygaśnięcia w formacie [czasu UNIX Epoch,](https://en.wikipedia.org/wiki/Unix_time) co oznacza liczbę sekund, które upłynęło od północy Skoordynowany czas uniwersalny, 1 stycznia 1970.
1. Formatowanie **ResourceUrl,** który reprezentuje zasób, do którego próbujesz uzyskać dostęp, więc jest zakodowany w procentach i małe litery. **ResourceUrl** ma formularz `'https://<namespace>.servicebus.windows.net/<hubName>'`.
1. Przygotowanie **StringToSign**, który jest `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`sformatowany jako .
1. Przetwarzanie i kodowanie **sygnatury** przez base64 przy użyciu skrótu HMAC-SHA256 wartości **StringToSign.** Wartość mieszania jest używana z **kluczową** częścią **ciągu połączenia** dla odpowiedniej **reguły autoryzacji**.
1. Formatowanie podpisu zakodowanego **Signature** w formacie Base64, tak aby był zakodowany procent.
1. Konstruowanie tokenu w oczekiwanym formacie przy użyciu **urlencodedsignature**, **ExpiryEpoch**, **KeyName**i **UrlEncodedResourceUrl** wartości.

Zobacz [dokumentację usługi Azure Service Bus, aby](../service-bus-messaging/service-bus-sas.md) uzyskać dokładniejsze omówienie podpisu dostępu udostępnionego i sposobu korzystania z niej usługi Azure Service Bus i Notification Hubs.

Na potrzeby tego przykładu Swift, masz zamiar użyć biblioteki Apple open-source **CommonCrypto,** aby pomóc w mieszaniu podpisu. Ponieważ jest to biblioteka C, nie jest dostępna w Swift po wyjęciu z pudełka. Bibliotekę można udostępnić za pomocą nagłówka pomostowego.

Aby dodać i skonfigurować nagłówek mostkowania:

1. W polu Xcode wybierz pozycję **Plik** > **nowy** > **File** > **plik nagłówka pliku**. Nazwij plik **nagłówkowy BridgingHeader.h**.

1. Edytuj plik, aby zaimportować **CommonHMAC.h**:

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Zaktualizuj **ustawienia kompilacji** obiektu docelowego, aby odwoływać się do nagłówka pomostowego:

   1. Dotknij projektu **PushDemo** i przewiń w dół do sekcji **Swift Compiler.**

   1. Upewnij się, że opcja **Zainstaluj nagłówek zgodności języka C** jest ustawiona na **Tak**.

   1. Wprowadź ścieżkę `'<ProjectName>/BridgingHeader.h'` pliku do opcji **Nagłówek pomostowy Objective-C.** Jest to ścieżka pliku do naszego nagłówka pomostowego.

   Jeśli nie możesz znaleźć tych opcji, upewnij się, że masz wybrany widok **Wszystkie,** a nie **podstawowy** lub **dostosowany**.

   Dostępnych jest wiele bibliotek otoki open source innych firm, które mogą nieco ułatwić korzystanie z **CommonCrypto.** Jednak omówienie takich bibliotek wykracza poza zakres tego artykułu.

1. Dodaj nowy plik Swift o nazwie **TokenUtility.swift** w folderze **Narzędzia** i dodaj następujący kod:

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

   To narzędzie hermetyzuje logikę odpowiedzialną za generowanie tokenu sygnatury dostępu Współdzielonego.

   Jak opisano **wcześniej, getSasToken** funkcja organizuje kroki wysokiego poziomu wymagane do przygotowania tokenu. Funkcja zostanie wywołana przez usługę instalacyjną w dalszej części tego samouczka.

   Pozostałe dwie funkcje są wywoływane przez funkcję **getSasToken:** **sha256HMac** do obliczania podpisu i **urlEncodedString** do kodowania skojarzonego ciągu adresu URL. Funkcja **urlEncodedString** jest wymagana, ponieważ nie jest możliwe osiągnięcie wymaganego danych wyjściowych przy użyciu wbudowanej funkcji **addingPercentEncoding.**

   Zestaw [SDK systemu iOS usługi Azure Storage jest](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) doskonałym przykładem podejścia do tych operacji w języku Objective-C. Więcej informacji na temat tokenów sas usługi Azure Service Bus można znaleźć w [dokumentacji usługi Azure Service Bus.](../service-bus-messaging/service-bus-sas.md)

1. W **AppDelegate.swift**dodaj następujący kod do funkcji *didRegisterForRemoteNotificationsWithDeviceToken,* aby sprawdzić, czy **token.getSasToken** generuje prawidłowy token
    
    ```swift
    let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

    let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                                withKeyName: self.notificationHubKeyName!,
                                                andKey: self.notificationHubKey!)
    
    print(tokenData.token)
    ```

    Pamiętaj, aby zastąpić wartości zastępcze w **ciągu baseAddress** własnymi

### <a name="verify-the-sas-token"></a>Weryfikacja tokenu sygnatury dostępu Współdzielonego

Przed zaimplementowanie usługi instalacji w kliencie, sprawdź, czy nasza aplikacja jest poprawnie generowania tokenu sygnatury dostępu Współdzielonego przy użyciu narzędzia HTTP wyboru. Na potrzeby tego samouczka naszym narzędziem z wyboru będzie **Listonosz.**

Zanotuj **identyfikator instalacji** i wartości **tokenów** generowanych przez aplikację.

Wykonaj następujące kroki, aby wywołać interfejs API **instalacji:**

1. W **obszarze Listonosz**otwórz nową kartę.

1. Ustaw żądanie na **GET** i określ następujący adres:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. Skonfiguruj nagłówki żądań w następujący sposób:

   | Klucz           | Wartość            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Autoryzacja | \<> sasToken       |
   | wersja x-ms  | 2015-01          |

1. Wybierz przycisk **Kod,** który pojawi się w prawym górnym rogu pod przyciskiem **Zapisz.** Żądanie powinno wyglądać podobnie do następującego przykładu:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. Kliknij przycisk **Wyślij**.

Nie istnieje rejestracja dla określonej **installationId** w tym momencie. Weryfikacja powinna skutkować odpowiedzią "404 Nie znaleziono", a nie odpowiedzią "401 Nieautoryzowane". Ten wynik powinien potwierdzić, że token sygnatury dostępu Współdzielonego został zaakceptowany.

### <a name="implement-the-installation-service-class"></a>Zaimplementuj klasę usługi instalacji

Następnie zaimplementujesz nasze podstawowe otoki wokół [interfejsu API Instalacje REST](/rest/api/notificationhubs/create-overwrite-installation).  

Dodaj nowy plik Swift o nazwie **NotificationRegistrationService.swift** w folderze **Usługi,** a następnie dodaj do tego pliku następujący kod:

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
    private var tokenExpiryDate : Date? = nil
    
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
                        completion(err == nil && 
                        (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }
    
    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }
    
    private func getSasToken() -> String {
        if (tokenData == nil ||
            tokenExpiryDate == nil ||
            Date() >= tokenExpiryDate!) {
            
            self.tokenData = TokenUtility.getSasToken(
                forResourceUrl: getBaseAddress(),
                withKeyName: self.keyName,
                andKey: self.key)
            
            self.tokenExpiryDate = Date(timeIntervalSinceNow: -(5 * 60))
                .addingTimeInterval(TimeInterval(tokenData!.expiration))
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

Wymagane szczegóły są dostarczane w ramach inicjowania. Znaczniki i szablony są opcjonalnie przekazywane do funkcji **rejestru,** aby stanowić część ładunku JSON **instalacji urządzenia.**  

Funkcja **rejestru** wywołuje inne funkcje prywatne, aby przygotować żądanie. Po odebraniu odpowiedzi zostanie wywołana zakończenie i wskazuje, czy rejestracja zakończyła się pomyślnie.  

Punkt końcowy żądania jest konstruowany przez funkcję **getBaseAddress.** Konstrukcja używa *obszaru nazw* parametrów centrum powiadomień i *nazwy,* które zostały podane podczas inicjowania.  

Funkcja **getSasToken** sprawdza, czy aktualnie przechowywany token jest prawidłowy. Jeśli token nie jest prawidłowy, funkcja wywołuje **TokenUtility** do generowania nowego tokenu, a następnie przechowuje go przed zwróceniem wartości.

Na koniec **encodeToJson** konwertuje odpowiednie obiekty modelu do JSON do użycia jako część treści żądania.

### <a name="invoke-the-notification-hubs-rest-api"></a>Wywoływanie interfejsu API REST centrów powiadomień

Ostatnim krokiem jest aktualizowanie **AppDelegate** do korzystania **z NotificationRegistrationService** zarejestrować się w naszym **NotificationHub**.

1. Otwórz **appdelegate.swift** i dodaj zmienne na poziomie klasy, aby zapisać odwołanie do **Usługi Rejestracji Noficiation** i ogólnego **przycisku PushTemplate:**

    ```swift
    var registrationService : NotificationRegistrationService?
    let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
    ```

1. W tym samym pliku zaktualizuj **didRegisterForRemoteNotificationsWithDeviceToken,** aby zainicjować **NotificationRegistrationService** z wymaganymi parametrami, a następnie wywołać funkcję **rejestru.**

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

    Aby było to proste, zamierzasz użyć kilku instrukcji drukowania, aby zaktualizować okno danych wyjściowych w wyniku operacji **rejestru.**

1. Teraz skompiluj i uruchom aplikację na urządzeniu fizycznym. Powinien zostać wyświetlony "Zarejestrowany" w oknie danych wyjściowych.

## <a name="test-the-solution"></a>Przetestuj roztwór

Nasza aplikacja na tym etapie jest zarejestrowana w **NotificationHub** i może otrzymywać powiadomienia push. W xcode zatrzymaj debuger i zamknij aplikację, jeśli jest aktualnie uruchomiona. Następnie sprawdź, czy szczegóły **instalacji urządzenia** są zgodnie z oczekiwaniami i czy nasza aplikacja może teraz otrzymywać powiadomienia wypychane.  

### <a name="verify-the-device-installation"></a>Sprawdź instalację urządzenia

Teraz można złożyć to samo żądanie, co poprzednio, używając **listonosza** do [weryfikacji tokenu sygnatury dostępu Współdzielonego.](#verify-the-sas-token) Zakładając, że token sygnatury dostępu Współdzielonego nie wygasł, odpowiedź powinna teraz zawierać podane szczegóły instalacji, takie jak szablony i tagi.

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

Jeśli poprzedni **token sygnatury dostępu Współdzielonego** wygasł, można dodać punkt **przerwania** do **wiersza 24** klasy **TokenUtility,** aby uzyskać nowy **token sygnatury dostępu Współdzielonego** i zaktualizować nagłówek **autoryzacji** o tę nową wartość.

### <a name="send-a-test-notification-azure-portal"></a>Wysyłanie powiadomienia testowego (Azure portal)

Najszybszym sposobem przetestowania, że można teraz otrzymywać powiadomienia, jest przejście do centrum powiadomień w witrynie Azure portal:

1. W witrynie Azure portal przejdź do karty **Przegląd** w centrum powiadomień.

1. Wybierz **opcję Wyślij test**, który znajduje się powyżej podsumowania **Essentials** w lewym górnym rogu okna portalu:

    ![Przycisk Wyślij test podsumowanie powiadomień Hubs Essentials](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. Wybierz **pozycję Szablon niestandardowy** z listy **Platformy.**

1. Wprowadź **12345** dla wyrażenia **Wyślij do znacznika**. Wcześniej określono ten tag w naszej instalacji.

1. Opcjonalnie edytuj **komunikat** w ładunku JSON:

    ![Wysyłanie testowe centrów powiadomień](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Wybierz pozycję **Wyślij**. Portal powinien wskazać, czy powiadomienie zostało pomyślnie wysłane do urządzenia:

    ![Wyniki testu testów centrów powiadomień](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Zakładając, że aplikacja nie jest uruchomiona na pierwszym planie, powinien zostać wyświetlony powiadomienie w **Centrum powiadomień** na urządzeniu. Dotknięcie powiadomienia powinno otworzyć aplikację i wyświetlić alert.

    ![Przykład odebranego powiadomienia](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Wyślij powiadomienie testowe (operator poczty)

Możesz wysyłać powiadomienia za pośrednictwem [interfejsu API REST](/rest/api/notificationhubs/) za pomocą **postmana,** co może być wygodniejszym sposobem testowania.

1. Otwórz nową kartę w obszarze **Listonosz**.

1. Ustaw żądanie na **POST**i wprowadź następujący adres:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. Skonfiguruj nagłówki żądań w następujący sposób:

   | Klucz                            | Wartość                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | aplikacja/json;charset=utf-8 |
   | Autoryzacja                  | \<> sasToken                     |
   | ServiceBusSformowanienotyfikacji  | szablon                       |
   | Tagi                           | "12345"                        |

1. Skonfiguruj **treść** żądania do **używania pliku RAW — JSON (application.json)** z następującym ładunkiem JSON:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Wybierz przycisk **Kod,** który znajduje się pod przyciskiem **Zapisz** w prawym górnym rogu okna. Żądanie powinno wyglądać podobnie do następującego przykładu:

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

1. Kliknij przycisk **Wyślij**.

Powinieneś otrzymać kod stanu utworzonego sukcesu **201** i otrzymać powiadomienie na urządzeniu klienckim..

## <a name="next-steps"></a>Następne kroki
Masz teraz podstawową aplikację iOS Swift połączoną z centrum powiadomień za pośrednictwem [interfejsu API REST](/rest/api/notificationhubs/) i możesz wysyłać i odbierać powiadomienia. Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Usługi Azure Notification Hubs — omówienie](notification-hubs-push-notification-overview.md)
- [Centra powiadomień REST API](/rest/api/notificationhubs/)
- [SDK koncentratorów powiadomień dla operacji zaplecza](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [SDK centrów powiadomień w usłudze GitHub](https://github.com/Azure/azure-notificationhubs)
- [Zarejestruj się za pomocą zaplecza aplikacji](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Zarządzanie rejestracją](notification-hubs-push-notification-registration-management.md)
- [Praca z tagami](notification-hubs-tags-segment-push-message.md) 
- [Praca z szablonami niestandardowymi](notification-hubs-templates-cross-platform-push-messages.md)
- [Kontrola dostępu usługi Service Bus z podpisami dostępu współdzielonego](../service-bus-messaging/service-bus-sas.md)
- [Programowo generowanie tokenów sygnatury dostępu Współdzielonego](/rest/api/eventhub/generate-sas-token)
- [Bezpieczeństwo Apple: wspólne krypto](https://developer.apple.com/security/)
- [Czas unix epoki](https://en.wikipedia.org/wiki/Unix_time)
- [Hmac](https://en.wikipedia.org/wiki/HMAC)
