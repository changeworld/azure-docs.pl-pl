---
title: Wprowadzenie do systemu iOS — platforma tożsamości firmy Microsoft | Azure
description: Jak aplikacje systemu iOS (Swift) można wywołać interfejsu API, wymaga tokenów dostępu przy użyciu platformy tożsamości firmy Microsoft
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 780da9359aaf645abc9b685fa9d90bbea9199759
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962167"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-app"></a>Logowania użytkowników i wywoływania usługi Microsoft Graph w aplikacji systemu iOS

W tym samouczku dowiesz się, jak utworzyć aplikację dla systemu iOS i zintegrować ją z platformą Microsoft identity. Ściślej mówiąc ta aplikacja będzie logowania użytkownika, uzyskania tokenu dostępu do wywołania interfejsu API programu Microsoft Graph i dokonać podstawowego żądania interfejsu API programu Microsoft Graph.  

Po zakończeniu przewodnika, aplikacja będzie akceptować logowania osobistych kont Microsoft (w tym outlook.com, live.com i inne) i służbowego konta z firmy lub organizacji, która używa usługi Azure Active Directory.

## <a name="how-this-guide-works"></a>Jak działa ten przewodnik

![Pokazuje, jak działa przykładowej aplikacji wygenerowanych w ramach tego samouczka](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Aplikacja w tym przykładzie będzie logować użytkowników i Pobierz dane w ich imieniu.  Te dane będą uzyskiwać dostęp za pośrednictwem chronionego interfejsu API (Microsoft interfejsu API programu Graph w tym przypadku) wymaga autoryzacji, która jest również chroniony przez platforma tożsamości firmy Microsoft.

Więcej szczegółów:

* Aplikacja będzie Zaloguj użytkownika za pośrednictwem przeglądarki lub Microsoft Authenticator.
* Użytkownik końcowy będzie akceptować uprawnienia, które zażądał aplikacji. 
* Aplikacja zostanie wystawiony token dostępu do interfejsu API programu Microsoft Graph.
* Token dostępu zostaną uwzględnione w żądaniu HTTP do interfejsu API sieci web.
* Przetworzenie odpowiedzi programu Microsoft Graph.

W tym przykładzie używa Microsoft Authentication library (MSAL) do implementacji uwierzytelniania Biblioteka MSAL będzie automatycznie odnowić tokenów, dostarczanie SSO między innymi aplikacjami na urządzeniu i zarządzanie konta.

## <a name="prerequisites"></a>Wymagania wstępne

- XCode w wersji 10.x jest wymagany dla przykładu, który jest tworzony w tym przewodniku. Możesz pobrać środowisko XCode ze [witryny sieci Web w programie iTunes](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "adres URL pobierania w środowisku XCode").
- Biblioteka Microsoft Authentication Library ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Można użyć Menedżera zależności lub ręcznie Dodaj. Brak sekcji poniżej z [informacji](#add-msal). 

## <a name="set-up-your-project"></a>konfigurowanie projektu

W tym samouczku spowoduje utworzenie nowego projektu. Jeśli chcesz zamiast tego Pobierz samouczek ukończone [pobrać kod](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip).

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

1. Otwórz program Xcode i wybierz **Utwórz nowy projekt Xcode**.
2. Wybierz **dla systemu iOS > pojedynczy widok aplikacji** i wybierz **dalej**.
3. Nadaj nazwę produktu, a następnie wybierz pozycję **dalej**.
4. Wybierz folder, aby utworzyć aplikację, a następnie kliknij przycisk *Utwórz*.

## <a name="register-your-application"></a>Zarejestruj swoją aplikację 

Aplikację można zarejestrować na dwa sposoby, zgodnie z opisem w dwóch następnych sekcjach.

### <a name="register-your-app"></a>Rejestrowanie aplikacji

1. Przejdź do [witryny Azure portal](https://aka.ms/MobileAppReg) > Wybierz `New registration`. 
2. Wprowadź **nazwa** aplikacji > `Register`. **Identyfikator URI przekierowania nie jest ustawiona na tym etapie**. 
3. W `Manage` sekcję i przejdź do `Authentication` > `Add a platform` > `iOS`
    - Wprowadź identyfikator projektu pakietu. Jeśli pobrano kod to `com.microsoft.identitysample.MSALiOS`.
4. Trafienia `Configure` i przechowywać `MSAL Configuration` na później. 

## <a name="add-msal"></a>Dodawanie biblioteki MSAL

### <a name="get-msal"></a>Get MSAL

#### <a name="cocoapods"></a>CocoaPods

Możesz użyć [CocoaPods](https://cocoapods.org/) zainstalował `MSAL` przez dodanie jej do swojej `Podfile` poniżej wartości docelowej:

```
use_frameworks!

target '<your-target-here>' do
   pod 'MSAL', '~> 0.4.0'
end
```

#### <a name="carthage"></a>Carthage

Możesz użyć [Carthage](https://github.com/Carthage/Carthage) zainstalował `MSAL` przez dodanie jej do swojej `Cartfile`: 

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

#### <a name="manually"></a>Ręcznie

Możesz również użyć narzędzia Git w module podrzędnym lub zapoznaj się z najnowszą wersją i używać jako platformę w aplikacji.

### <a name="add-your-app-registration"></a>Dodawanie rejestracji aplikacji

Następnie dodaj rejestrację aplikacji w kodzie. Dodaj swoje ***klienta / identyfikator aplikacji*** do `ViewController.swift`:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
let kAuthority = "https://login.microsoftonline.com/common"
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
```

### <a name="configure-url-schemes"></a>Konfigurowanie Schematy adresów URL

Zarejestruj `CFBundleURLSchemes` umożliwia wywołanie zwrotne, dzięki czemu użytkownik może być przekierowany z powrotem do aplikacji po zalogowaniu.

`LSApplicationQueriesSchemes` Umożliwia, za pomocą aplikacji Microsoft Authenticator używać, jeśli jest dostępny. 

Aby to zrobić, otwórz `Info.plist` jako źródło kodu i Dodaj następujący kod, zastępując ***BUNDLE_ID*** z skonfigurowany w witrynie Azure portal.

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

### <a name="import-msal"></a>Importuj biblioteki MSAL

Importuj biblioteki MSAL struktura w systemie `ViewController.swift` i `AppDelegate.swift` plików:

```swift
import MSAL
```

## <a name="create-your-apps-ui"></a>Tworzenie aplikacji interfejsu użytkownika

W tym samouczku musisz utworzyć:

- Wywołanie interfejsu API programu Graph przycisku
- Wyloguj się przycisk
- Element textview rejestrowania

W `ViewController.swift`, zdefiniuj właściwości i `initUI()` w następujący sposób:

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

Następnie dodaj do `viewDidLoad()` z ViewController.swift:

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

## <a name="use-msal"></a>Użycia biblioteki MSAL

### <a name="initialize-msal"></a>Inicjowanie biblioteki MSAL

Najpierw musisz utworzyć `MSALPublicClientApplication` z wystąpieniem `MSALPublicClientConfiguration` aplikacji:

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

### <a name="handle-the-callback"></a>Dojście do wywołania zwrotnego

Aby obsłużyć wywołania zwrotnego po zalogowaniu, należy dodać `MSALPublicClientApplication.handleMSALResponse` w `appDelegate`:

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

#### <a name="acquire-tokens"></a>Uzyskiwanie tokenów

Teraz można zaimplementować interfejsu użytkownika aplikacji, przetwarzanie logiki i uzyskiwania tokenów interaktywnie przy użyciu biblioteki MSAL. 

Biblioteka MSAL udostępnia dwie podstawowe metody uzyskiwania tokenów: `acquireTokenSilently` i `acquireTokenInteractively`.  

`acquireTokenSilently()` próbuje zalogować użytkownika i uzyskiwanie tokenów, bez żadnej interakcji użytkownika, jeśli konto jest obecny.

`acquireTokenInteractively` zawsze wyświetli interfejsu użytkownika podczas próby zalogowania się użytkownika i uzyskiwanie tokenów. jednak może ona używać plików cookie sesji w przeglądarce lub konta w aplikacji Microsoft authenticator zapewnienie środowiska interaktywnego logowania jednokrotnego. 

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

#### <a name="get-a-token-interactively"></a>Pobierz token interaktywnie

Aby uzyskać token po raz pierwszy, musisz utworzyć `MSALInteractiveTokenParameters` i wywołać `acquireToken`.

1. Utwórz `MSALInteractiveTokenParameters` z zakresami.
2. Wywołaj `acquireToken` z parametrami utworzone.
3. Odpowiednio je obsłużyć błąd. Aby uzyskać więcej szczegółów, zobacz [Przewodnik obsługi błędów dla systemu iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Error-Handling).
4. Obsługę w przypadku pomyślnego. 

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



#### <a name="get-a-token-silently"></a>Uzyskać token w trybie dyskretnym

Aby nabyć zaktualizowanego tokenu w trybie dyskretnym, należy utworzyć `MSALSilentTokenParameters` i wywołać `acquireTokenSilent`:

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

### <a name="call-the-microsoft-graph-api"></a>Wywołanie interfejsu API programu Microsoft Graph 

Po utworzeniu tokenu za pośrednictwem `self.accessToken`, aplikacja tej wartości można użyć w nagłówku HTTP się z żądaniem autoryzowanego do programu Microsoft Graph:

| Klucz nagłówka    | value                 |
| ------------- | --------------------- |
| Autoryzacja | Elementu nośnego < token dostępu > |

Dodaj następujące polecenie, aby `ViewController.swift`:

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

Dowiedz się więcej o [interfejsu API Microsoft Graph](https://graph.microsoft.com)

### <a name="use-msal-for-sign-out"></a>Użycia biblioteki MSAL do wylogowania

Teraz, up, dodamy obsługę wylogowania do naszej aplikacji. 

Należy pamiętać, wylogowania z biblioteki MSAL usuwa wszelkie informacje o użytkowniku z tej aplikacji, ale użytkownik nadal będzie miał aktywną sesję na swoim urządzeniu. Jeśli użytkownik spróbuje się zalogować ponownie ich może zostać wyświetlony interakcji, ale nie może być konieczne ponowne wprowadzenie poświadczeń z powodu sesji urządzenia są aktywne. 

Aby dodać wylogowania, skopiuj następującą metodę do swojej `ViewController.swift`:

```swift 
    @objc func signOut(_ sender: UIButton) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
            try applicationContext.remove(account)
            self.loggingText.text = ""
            self.signOutButton.isEnabled = false
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>Włącz buforowanie tokenów

Domyślnie biblioteka MSAL buforuje tokenów aplikacji w pęku kluczy systemu iOS. 

Aby włączyć buforowanie tokenów, przejdź do ustawień projektu Xcode > `Capabilities tab`  >  `Enable Keychain Sharing` > kliknij `Plus` > wprowadź **com.microsoft.adalcache**.

### <a name="add-helper-methods"></a>Dodaj metody pomocnika

Dodaj te metody pomocnika do ukończenia przykładu:

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

### <a name="multi-account-applications"></a>Aplikacje dla wielu kont

Ta aplikacja została stworzona w scenariuszu jednego konta. Biblioteka MSAL obsługuje również scenariusze wielu konta, ale wymaga wykonania dodatkowych czynności z aplikacji. Należy utworzyć interfejs użytkownika, aby pomóc użytkownika wybierz konto, które mają być użyte dla każdej akcji, które wymagają tokenów. Alternatywnie aplikację można wdrożyć heurystyki o wybranie konta do użycia za pomocą `getAllAccounts(...)` metody.

## <a name="test-your-app"></a>Testowanie aplikacji

### <a name="run-locally"></a>Uruchamianie lokalnie

Jeśli wykonano powyższe kodu, spróbuj do tworzenia i wdrażania aplikacji na urządzeniu testowym lub w emulatorze. Powinna być może logować się i uzyskiwać tokeny dla usługi Azure AD lub osobistych kont Microsoft! Po zalogowaniu użytkownika tej aplikacji będą wyświetlane dane zwrócone przez program Microsoft Graph `/me` punktu końcowego. 

Jeśli masz problemy, możesz otworzyć zgłoszenie tego dokumentu lub biblioteka MSAL i Daj nam znać. 

### <a name="consent-to-your-app"></a>Zgoda na aplikację

Dowolny użytkownik zaloguje się do aplikacji, po raz pierwszy zostanie wyświetlony monit, tożsamości firmy Microsoft do wyrażenia zgody na uprawnienia wymagane.  Mimo że większość użytkowników stanie wyrażanie zgody, niektóre dzierżaw usługi Azure AD zostało wyłączone przez zgody użytkownika - wymagające zgody w imieniu wszystkich użytkowników, administratorów.  Aby zapewnić obsługę tego scenariusza, należy zarejestrować zakresy Twojej aplikacji w witrynie Azure portal.

## <a name="help-and-support"></a>Pomoc i obsługa techniczna

Wystąpił problem z dowolnym z tego samouczka lub z platformą tożsamości Microsoft? Zobacz [Pomoc i obsługa techniczna](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)

