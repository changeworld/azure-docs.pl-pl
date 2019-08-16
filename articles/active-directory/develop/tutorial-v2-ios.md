---
title: Wprowadzenie do systemu iOS — platforma tożsamości firmy Microsoft | Azure
description: Jak aplikacje dla systemu iOS (SWIFT) mogą wywołać interfejs API, który wymaga tokenów dostępu przy użyciu platformy tożsamości firmy Microsoft
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/14/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c9afa63a26b7ac990867517f44825054b4c5436
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69512392"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-app"></a>Logowanie użytkowników i wywoływanie Microsoft Graph z aplikacji dla systemu iOS

W tym samouczku dowiesz się, jak zintegrować aplikację systemu iOS z platformą tożsamości firmy Microsoft. Aplikacja zarejestruje użytkownika, uzyska token dostępu do wywołania interfejsu API Microsoft Graph i wyśle żądanie do interfejsu API Microsoft Graph.  

Po ukończeniu tego przewodnika aplikacja będzie akceptować logowania do osobistych kont Microsoft (w tym outlook.com, live.com i innych) oraz kont służbowych z dowolnej firmy lub organizacji korzystającej z Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Jak działa ten samouczek

![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten samouczek](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Aplikacja w tym samouczku zaloguje użytkowników i pobierze dane w ich imieniu.  Dostęp do tych danych będzie uzyskiwany za pośrednictwem chronionego interfejsu API (w tym przypadku Microsoft Graph API), który wymaga autoryzacji i jest chroniony przez platformę tożsamości firmy Microsoft.

Więcej szczegółów:

* Twoja aplikacja zaloguje użytkownika za pomocą przeglądarki lub Microsoft Authenticator.
* Użytkownik końcowy zaakceptuje uprawnienia wymagane przez aplikację.
* Aplikacja zostanie wystawiona token dostępu dla interfejsu API Microsoft Graph.
* Token dostępu zostanie uwzględniony w żądaniu HTTP do internetowego interfejsu API.
* Przetwórz odpowiedź Microsoft Graph.

Ten przykład używa biblioteki uwierzytelniania firmy Microsoft (MSAL) w celu zaimplementowania uwierzytelniania. Usługa MSAL automatycznie odnawia tokeny, dostarcza Logowanie jednokrotne między innymi aplikacjami na urządzeniu i zarządza kontami.

## <a name="prerequisites"></a>Wymagania wstępne

- XCode wersja 10. x jest wymagana do skompilowania aplikacji w tym przewodniku. Możesz pobrać XCode z(https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "adresu URL pobierania Xcode")w [witrynie iTunes].
- Biblioteka uwierzytelniania firmy Microsoft ([MSAL. Framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Można użyć Menedżera zależności lub ręcznie dodać bibliotekę. W poniższych instrukcjach pokazano, jak to zrobić.

Ten samouczek spowoduje utworzenie nowego projektu. Jeśli chcesz zamiast tego pobrać ukończony samouczek, [Pobierz kod](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip).

## <a name="create-a-new-project"></a>Tworzenie nowego projektu

1. Otwórz Xcode i wybierz pozycję **Utwórz nowy projekt Xcode**.
2. Wybierz pozycję**aplikacja z pojedynczym widokiem** **systemu iOS** > i wybierz pozycję **dalej**.
3. Podaj nazwę produktu.
4. Ustaw **Język** na **SWIFT** i wybierz pozycję **dalej**.
5. Wybierz folder, w którym chcesz utworzyć aplikację, a następnie kliknij przycisk **Utwórz**.

## <a name="register-your-application"></a>Zarejestruj swoją aplikację

1. Przejdź do witryny [Azure Portal](https://aka.ms/MobileAppReg).
2. Otwórz [blok rejestracje aplikacji](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) i kliknij pozycję **+ Nowa rejestracja**.
3. Wprowadź **nazwę** aplikacji, a następnie, bez ustawienia identyfikatora URI przekierowania, kliknij pozycję **zarejestruj**.
4. W sekcji **Zarządzanie** w wyświetlonym okienku wybierz pozycję **uwierzytelnianie**.
5. Kliknij pozycję **Wypróbuj nowe środowisko** w górnej części ekranu, aby otworzyć nowe środowisko rejestracji aplikacji, a następnie kliknij pozycję **+ Nowa rejestracja** >  **+ Dodaj platformę** > **iOS**.
    - Wprowadź identyfikator pakietu projektu. Jeśli pobrano kod, jest `com.microsoft.identitysample.MSALiOS`to. Jeśli tworzysz własny projekt, wybierz projekt w Xcode i Otwórz kartę **Ogólne** . Identyfikator pakietu zostanie wyświetlony w sekcji **tożsamość** .
6. Kliknij `Configure` i Zapisz **konfigurację MSAL** , która zostanie wyświetlona na stronie **Konfiguracja systemu iOS** , tak aby można ją było wprowadzić podczas późniejszej konfiguracji aplikacji.  Kliknij przycisk **Gotowe**.

## <a name="add-msal"></a>Dodaj MSAL

Wybierz jedną z następujących metod instalacji biblioteki MSAL w aplikacji:

### <a name="cocoapods"></a>CocoaPods

1. Jeśli używasz [CocoaPods](https://cocoapods.org/), zainstaluj `MSAL` program, tworząc najpierw pusty plik o nazwie `podfile` w tym samym folderze, w `.xcodeproj` którym znajduje się plik projektu. Dodaj następujące elementy `podfile`:

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL', '~> 0.4.0'
   end
   ```

2. Zamień `<your-target-here>` na nazwę projektu.
3. W oknie terminalu przejdź do folderu, który zawiera `podfile` utworzone i uruchomione `pod install` polecenie, aby zainstalować bibliotekę MSAL.
4. Zamknij Xcode i Otwórz `<your project name>.xcworkspace` , aby ponownie załadować projekt w Xcode.

### <a name="carthage"></a>Carthage

Jeśli używasz [Carthage](https://github.com/Carthage/Carthage), zainstaluj `MSAL` go, `Cartfile`dodając go do:

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

W oknie terminalu w tym samym katalogu, który został zaktualizowany `Cartfile`, uruchom następujące polecenie, aby Carthage zaktualizować zależności w projekcie:

```bash
carthage update --platform iOS
```

### <a name="manually"></a>Ręcznie

Możesz również użyć modułu podrzędnego Git lub zapoznaj się z najnowszą wersją, aby użyć jej jako platformy w aplikacji.

## <a name="add-your-app-registration"></a>Dodawanie rejestracji aplikacji

Następnie dodamy Twoją rejestrację aplikacji do Twojego kodu. 

Najpierw Dodaj następującą instrukcję importu na początku `ViewController.swift` plików i: `AppDelegate.swift`

```swift
import MSAL
```

Następnie Dodaj następujący kod do `ViewController.swift` programu `viewDidLoad()`przed:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
let kAuthority = "https://login.microsoftonline.com/common"
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
```

Zmodyfikuj wartość przypisaną do `kClientID`swojego identyfikatora aplikacji. Ta wartość jest częścią danych konfiguracji MSAL, które zostały zapisane w ramach kroku na początku tego samouczka, aby zarejestrować aplikację w Azure Portal.

## <a name="configure-url-schemes"></a>Konfigurowanie schematów adresów URL

W tym kroku zostanie zarejestrowane `CFBundleURLSchemes` , aby użytkownik mógł zostać przekierowany z powrotem do aplikacji po zalogowaniu się. Dzięki temu `LSApplicationQueriesSchemes` aplikacja może również używać Microsoft Authenticator.

W Xcode Otwórz `Info.plist` jako plik kodu źródłowego i Dodaj następujący `<dict>` element w sekcji. Zamień `[BUNDLE_ID]` na wartość użytą w Azure Portal, w przypadku której pobrano kod, to `com.microsoft.identitysample.MSALiOS`. Jeśli tworzysz własny projekt, wybierz projekt w Xcode i Otwórz kartę **Ogólne** . Identyfikator pakietu zostanie wyświetlony w sekcji **tożsamość** .

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauth</string>
    <string>msauthv2</string>
</array>
```

## <a name="create-your-apps-ui"></a>Tworzenie interfejsu użytkownika aplikacji

Teraz Utwórz interfejs użytkownika, który zawiera przycisk do wywoływania interfejsu API Microsoft Graph, drugi do wylogowania, i widok tekstu, aby wyświetlić niektóre dane wyjściowe, dodając następujący kod do `ViewController`klasy:

```swift
var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = UIButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
        callGraphButton.setTitleColor(.blue, for: .normal)
        callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
        callGraphButton.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
        callGraphButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
        // Add sign out button
        signOutButton = UIButton()
        signOutButton.translatesAutoresizingMaskIntoConstraints = false
        signOutButton.setTitle("Sign Out", for: .normal)
        signOutButton.setTitleColor(.blue, for: .normal)
        signOutButton.setTitleColor(.gray, for: .disabled)
        signOutButton.addTarget(self, action: #selector(signOut(_:)), for: .touchUpInside)
        self.view.addSubview(signOutButton)
        
        signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
        signOutButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
        signOutButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = UITextView()
        loggingText.isUserInteractionEnabled = false
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: 10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
    }
```

Następnie w `viewDidLoad()` klasie należy zamienić metodę na: `ViewController`

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        do {
            try self.initMSAL()
        } catch let error {
            self.loggingText.text = "Unable to create Application Context \(error)"
        }
    }
```

## <a name="use-msal"></a>Użyj MSAL

### <a name="initialize-msal"></a>Zainicjuj MSAL

Dodaj następującą `InitMSAL` metodę `ViewController` do klasy:

```swift
    func initMSAL() throws {
        
        guard let authorityURL = URL(string: kAuthority) else {
            self.loggingText.text = "Unable to create authority URL"
            return
        }
        
        let authority = try MSALAADAuthority(url: authorityURL)
        
        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
    }
```

### <a name="handle-the-sign-in-callback"></a>Obsługuj wywołanie zwrotne logowania

Otwórz plik `AppDelegate.swift`. Aby obsłużyć wywołanie zwrotne po zalogowaniu `MSALPublicClientApplication.handleMSALResponse` , Dodaj `appDelegate` do klasy podobnej do tej:

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

#### <a name="acquire-tokens"></a>Uzyskaj tokeny

Teraz możemy zaimplementować logikę przetwarzania interfejsu użytkownika aplikacji oraz interaktywnie uzyskać tokeny za pomocą MSAL.

MSAL udostępnia dwie podstawowe metody uzyskiwania tokenów: `acquireTokenSilently()` i `acquireTokenInteractively()`: 

- `acquireTokenSilently()`próbuje zalogować użytkownika i uzyskać tokeny bez interakcji użytkownika, o ile konto jest obecne.

- `acquireTokenInteractively()`zawsze pokazuje interfejs użytkownika podczas próby zalogowania użytkownika. Może ona używać plików cookie sesji w przeglądarce lub konta w usłudze Microsoft Authenticator w celu zapewnienia interaktywnego logowania jednokrotnego.

Dodaj następujący kod do `ViewController` klasy:

```swift
    @objc func callGraphAPI(_ sender: UIButton) {
        
        guard let currentAccount = self.currentAccount() else {
            // We check to see if we have a current logged in account.
            // If we don't, then we need to sign someone in.
            acquireTokenInteractively()
            return
        }
        
        acquireTokenSilently(currentAccount)
    }

    func currentAccount() -> MSALAccount? {
        
        guard let applicationContext = self.applicationContext else { return nil }
        
        // We retrieve our current account by getting the first account from cache
        // In multi-account applications, account should be retrieved by home account identifier or username instead
        
        do {
            let cachedAccounts = try applicationContext.allAccounts()
            if !cachedAccounts.isEmpty {
                return cachedAccounts.first
            }
        } catch let error as NSError {
            self.updateLogging(text: "Didn't find any accounts in cache: \(error)")
        }
        
        return nil
    }
```

#### <a name="get-a-token-interactively"></a>Interaktywnie Uzyskaj token

Poniższy kod pobiera token po raz pierwszy przez utworzenie `MSALInteractiveTokenParameters` obiektu i wywołanie. `acquireToken` Następnie dodasz kod, który:

1. Tworzy `MSALInteractiveTokenParameters` z zakresami.
2. Wywołania `acquireToken()` z utworzonymi parametrami.
3. Obsługuje błędy. Więcej szczegółów można znaleźć w przewodniku [obsługi błędów systemu iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Error-Handling).
4. Obsługuje pomyślne przypadki.

Dodaj następujący kod do `ViewController` klasy.

```swift
    func acquireTokenInteractively() {
   
        guard let applicationContext = self.applicationContext else { return }
     // #1    
        let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
     // #2        
        applicationContext.acquireToken(with: parameters) { (result, error) in
     // #3            
            if let error = error {
                self.updateLogging(text: "Could not acquire token: \(error)")
                return
            }
            guard let result = result else {   
                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }
     // #4            
            self.accessToken = result.accessToken
            self.updateLogging(text: "Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```



#### <a name="get-a-token-silently"></a>Uzyskaj token dyskretnie

Aby uzyskać zaktualizowany token w trybie dyskretnym, Dodaj następujący kod do `ViewController` klasy. Tworzy `MSALSilentTokenParameters` obiekt i wywołuje `acquireTokenSilent()`:

```swift
    
    func acquireTokenSilently(_ account : MSALAccount!) {
        guard let applicationContext = self.applicationContext else { return }
        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
        
        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in    
            if let error = error {
                let nsError = error as NSError
                if (nsError.domain == MSALErrorDomain) {
                    if (nsError.code == MSALError.interactionRequired.rawValue) {
                        DispatchQueue.main.async {
                            self.acquireTokenInteractively()
                        }
                        return
                    }
                }
                self.updateLogging(text: "Could not acquire token silently: \(error)")
                return
            }
            
            guard let result = result else {
                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }
            
            self.accessToken = result.accessToken
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```

### <a name="call-the-microsoft-graph-api"></a>Wywoływanie interfejsu API Microsoft Graph 

Gdy masz token, aplikacja może używać go w nagłówku HTTP w celu uzyskania autoryzowanego żądania do Microsoft Graph:

| klucz nagłówka    | value                 |
| ------------- | --------------------- |
| Authorization | > Tokenu \<dostępu okaziciela |

Dodaj następujący kod do `ViewController` klasy:

```swift
    func getContentWithToken() {        
        // Specify the Graph API endpoint
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)
        
        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
               
        URLSession.shared.dataTask(with: request) { data, response, error in
               
        if let error = error {
            self.updateLogging(text: "Couldn't get graph result: \(error)")
            return
        }
               
        guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
               
        self.updateLogging(text: "Couldn't deserialize result JSON")
            return
        }
               
        self.updateLogging(text: "Result from Graph: \(result))")
        
        }.resume()
    }
```

Aby dowiedzieć się więcej o interfejsie API Microsoft Graph, zobacz [Microsoft Graph interfejsu API](https://graph.microsoft.com) .

### <a name="use-msal-for-sign-out"></a>Użyj MSAL do wylogowania

Następnie Dodaj obsługę wylogowywania.

> [!Important]
> Wylogowanie za pomocą usługi MSAL usuwa wszystkie znane informacje o użytkowniku z aplikacji, ale użytkownik będzie nadal mieć aktywną sesję na swoim urządzeniu. Jeśli użytkownik spróbuje się zalogować ponownie, może wyświetlić interfejs użytkownika logowania, ale może nie być konieczne ponowne wprowadzenie poświadczeń, ponieważ sesja urządzenia jest nadal aktywna.

Aby dodać możliwość wylogowania, Dodaj następujący kod do `ViewController` klasy. Ta metoda umożliwia przechodzenie między wszystkimi kontami i usuwanie ich:

```swift 
    @objc func signOut(_ sender: UIButton) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache */
            
            try applicationContext.remove(account)
            self.loggingText.text = ""
            self.signOutButton.isEnabled = false
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>Włącz buforowanie tokenów

Domyślnie program MSAL przechowuje w pamięci podręcznej tokeny aplikacji w łańcuchu kluczy systemu iOS. 

Aby włączyć buforowanie tokenów:
1. Przejdź do pozycji Ustawienia projektu Xcode > **karcie** > możliwości**Włącz udostępnianie łańcucha kluczy**
2. Kliknij **+** i wprowadź `com.microsoft.adalcache` wpis **grup łańcucha kluczy** .

### <a name="add-helper-methods"></a>Dodawanie metod pomocnika

Dodaj następujące metody pomocnika do klasy `ViewController` , aby ukończyć próbkę:

``` swift
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.text = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.text = text
            }
        }
    }
    
    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
```

### <a name="multi-account-applications"></a>Aplikacje wielokonta

Ta aplikacja została skompilowana dla scenariusza jednego konta. MSAL obsługuje również scenariusze obejmujące wiele kont, ale wymagają pewnej dodatkowej pracy z poziomu aplikacji. Należy utworzyć interfejs użytkownika, aby ułatwić użytkownikowi wybranie konta, które ma być używane dla każdej akcji wymagającej tokenów. Alternatywnie aplikacja może zaimplementować heurystykę, aby wybrać konto, które ma być używane przez `getAccounts()` metodę.

## <a name="test-your-app"></a>Testowanie aplikacji

### <a name="run-locally"></a>Uruchamianie lokalnie

Kompiluj i Wdróż aplikację na urządzeniu testowym lub w emulatorze. Powinno być możliwe zalogowanie się i uzyskanie tokenów dla usługi Azure AD lub osobistych kont Microsoft.

Gdy użytkownik loguje się po raz pierwszy, zostanie poproszony o tożsamość firmy Microsoft, aby wyrazić zgodę na wymagane uprawnienia.  Chociaż większość użytkowników ma możliwość wyrażania zgody, niektórzy dzierżawy usługi Azure AD mają wyłączoną zgodę użytkownika, która wymaga od administratorów zgody w imieniu wszystkich użytkowników. Aby obsłużyć ten scenariusz, należy zarejestrować zakresy aplikacji w Azure Portal.

Po zalogowaniu aplikacja będzie wyświetlać dane zwrócone z punktu końcowego Microsoft Graph `/me` .

## <a name="get-help"></a>Uzyskiwanie pomocy

Odwiedź [Pomoc i pomoc techniczną,](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) Jeśli masz problemy z tym samouczkiem lub platformą tożsamości firmy Microsoft.

Pomóż nam ulepszyć platformę tożsamości firmy Microsoft. Powiedz nam, co myślisz, wykonując krótką ankietę z dwoma pytaniami.

> [!div class="nextstepaction"]
> [Microsoft Identity platform — ankieta](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)