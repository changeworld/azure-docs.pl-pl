---
title: Wprowadzenie do systemu iOS — platforma tożsamości firmy Microsoft | Azure
description: Jak aplikacje systemu iOS (Swift) można wywołać interfejsu API, wymaga tokenów dostępu przy użyciu platformy tożsamości firmy Microsoft
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.topic: tutorial
ms.workload: identity
ms.date: 07/15/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 910069ab89cef18794e637b6bfbbc57fb732871c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67872091"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-app"></a>Logowania użytkowników i wywoływania usługi Microsoft Graph w aplikacji systemu iOS

W tym samouczku dowiesz się, jak zintegrować aplikację dla systemu iOS z platformą Microsoft identity. Aplikacja do logowania użytkownika, Uzyskaj token dostępu do wywołania interfejsu API programu Microsoft Graph i wysłać żądanie do interfejsu API programu Microsoft Graph.  

Po zakończeniu przewodnika, aplikacja będzie akceptować logowania osobistych kont Microsoft (w tym outlook.com, live.com i inne) i służbowego konta z firmy lub organizacji, która używa usługi Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Jak działa w tym samouczku

![Pokazuje, jak działa przykładowej aplikacji wygenerowanych w ramach tego samouczka](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Aplikacja w tym samouczku zostanie logowania użytkowników i Pobierz dane w ich imieniu.  Te dane będą uzyskiwać dostęp za pośrednictwem chronionego interfejsu API (Microsoft interfejsu API programu Graph w tym przypadku) wymaga autoryzacji, która jest chroniona przez platforma tożsamości firmy Microsoft.

Więcej szczegółów:

* Aplikacja będzie Zaloguj użytkownika za pośrednictwem przeglądarki lub Microsoft Authenticator.
* Użytkownik końcowy będzie akceptować uprawnienia, które zażądał aplikacji.
* Aplikacja zostanie wystawiony token dostępu do interfejsu API programu Microsoft Graph.
* Token dostępu zostaną uwzględnione w żądaniu HTTP do interfejsu API sieci web.
* Przetworzenie odpowiedzi programu Microsoft Graph.

W tym przykładzie używa Microsoft Authentication library (MSAL) do implementacji uwierzytelniania. Biblioteka MSAL zostanie automatycznie odnowić tokenów, dostarczanie logowania jednokrotnego (SSO) między innymi aplikacjami na urządzeniu oraz zarządzanie konta.

## <a name="prerequisites"></a>Wymagania wstępne

- XCode w wersji 10.x jest wymagany do kompilowania aplikacji, w tym przewodniku. Możesz pobrać środowisko XCode ze [witryny sieci Web w programie iTunes](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "adres URL pobierania w środowisku XCode").
- Biblioteka Microsoft Authentication Library ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Można użyć Menedżera zależności lub ręcznie dodaj bibliotekę. W poniższych instrukcjach pokazano, jak.

W tym samouczku spowoduje utworzenie nowego projektu. Jeśli chcesz zamiast tego Pobierz samouczek ukończone [pobrać kod](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip).

## <a name="create-a-new-project"></a>Tworzenie nowego projektu

1. Otwórz program Xcode i wybierz **Utwórz nowy projekt Xcode**.
2. Wybierz **iOS** > **aplikacji z pojedynczym widokiem** i wybierz **dalej**.
3. Podaj nazwę produktu.
4. Ustaw **języka** do **Swift** i wybierz **dalej**.
5. Wybierz folder, aby utworzyć aplikację, a następnie kliknij przycisk **Utwórz**.

## <a name="register-your-application"></a>Rejestrowanie aplikacji

1. Przejdź do witryny [Azure Portal](https://aka.ms/MobileAppReg).
2. Otwórz [bloku rejestracje aplikacji](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) i kliknij przycisk **+ rejestrowanie nowej**.
3. Wprowadź **nazwa** dla aplikacji, a następnie bez ustawienia identyfikatora URI przekierowania, kliknij przycisk **zarejestrować**.
4. W **Zarządzaj** sekcji okienka, zostanie wyświetlone, wybierz **uwierzytelniania**.
5. Kliknij przycisk **wypróbować nowe środowisko** w górnej części ekranu, aby otworzyć nowe środowisko rejestracji aplikacji, a następnie kliknij przycisk **+ rejestrowanie nowej** >  **+ Dodaj platformę**  >  **iOS**.
    - Wprowadź identyfikator projektu pakietu. Jeśli pobrano kod to `com.microsoft.identitysample.MSALiOS`. Jeśli tworzysz własny projekt, wybierz swój projekt w programie Xcode i Otwórz **ogólne** kartę. Identyfikator pakietu jest wyświetlany w **tożsamości** sekcji.
6. Kliknij przycisk `Configure` i Zapisz **konfiguracji biblioteki MSAL** wyświetlany w **konfiguracji systemu iOS** strony możesz wprowadzić go podczas konfigurowania aplikacji później.  Kliknij przycisk **Gotowe**.

## <a name="add-msal"></a>Dodawanie biblioteki MSAL

Wybierz jedną z następujących sposobów instalowania biblioteki MSAL w swojej aplikacji:

### <a name="cocoapods"></a>CocoaPods

1. Jeśli używasz [CocoaPods](https://cocoapods.org/), zainstaluj `MSAL` przy pierwszym tworząc pusty plik o nazwie `podfile` w folderze projektu `.xcodeproj` pliku. Dodaj następujące polecenie, aby `podfile`:

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL', '~> 0.4.0'
   end
   ```

2. Zastąp `<your-target-here>` nazwą projektu.
3. W oknie terminalu przejdź do folderu, który zawiera `podfile` utworzyć i uruchomić `pod install` zainstalować biblioteki MSAL.
4. Zamknij program Xcode i Otwórz `<your project name>.xcworkspace` ponowne załadowanie projektu w środowisku Xcode.

### <a name="carthage"></a>Carthage

Jeśli używasz [Carthage](https://github.com/Carthage/Carthage), zainstaluj `MSAL` przez dodanie jej do swojej `Cartfile`:

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

### <a name="manually"></a>Ręczne

Można również użyć narzędzia Git w module podrzędnym lub Wyewidencjonuj najnowszą wersję do użycia jako platforma aplikacji.

## <a name="add-your-app-registration"></a>Dodawanie rejestracji aplikacji

Następnie dodamy rejestrację aplikacji w kodzie. 

Najpierw dodaj następującą instrukcję importu na początku `ViewController.swift` i `AppDelegate.swift` plików:

```swift
import MSAL
```

Następnie dodaj następujący kod do `ViewController.swift` przed `viewDidLoad()`:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
let kAuthority = "https://login.microsoftonline.com/common"
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
```

Zmodyfikuj wartość przypisana do `kClientID`do identyfikatora aplikacji Ta wartość jest częścią dane konfiguracji biblioteki MSAL zapisano podczas wykonywania kroku na początku tego samouczka, aby zarejestrować aplikację w witrynie Azure portal.

## <a name="configure-url-schemes"></a>Konfigurowanie Schematy adresów URL

W tym kroku zostanie zarejestrowana `CFBundleURLSchemes` , dzięki czemu użytkownik może zostać przekierowany z powrotem do aplikacji po zalogowaniu. Przy okazji `LSApplicationQueriesSchemes` zezwala również aplikacji do korzystania z programu Microsoft Authenticator.

W środowisku Xcode Otwórz `Info.plist` jako kod źródłowy plik i Dodaj następujący kod wewnątrz `<dict>` sekcji. Zastąp `[BUNDLE_ID]` o wartości użyte w witrynie Azure portal, w której pobrano kod, czy `com.microsoft.identitysample.MSALiOS`. Jeśli tworzysz własny projekt, wybierz swój projekt w programie Xcode i Otwórz **ogólne** kartę. Identyfikator pakietu jest wyświetlany w **tożsamości** sekcji.

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

## <a name="create-your-apps-ui"></a>Tworzenie aplikacji interfejsu użytkownika

Teraz Utwórz interfejs użytkownika, który zawiera przycisk do wywołania interfejsu API Microsoft Graph, innej, aby się wylogować, i wyświetlania tekstu, aby wyświetlić niektóre dane wyjściowe, dodając następujący kod, aby `ViewController`klasy:

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

Następnie również wewnątrz `ViewController` klasy, Zastąp `viewDidLoad()` metody:

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

Dodaj następujący kod `InitMSAL` metody `ViewController` klasy:

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

### <a name="handle-the-sign-in-callback"></a>Obsługa logowania wywołania zwrotnego

Otwórz plik `AppDelegate.swift`. Aby obsłużyć wywołania zwrotnego po zalogowaniu, należy dodać `MSALPublicClientApplication.handleMSALResponse` do `appDelegate` klasy następująco:

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

#### <a name="acquire-tokens"></a>Uzyskiwanie tokenów

Teraz możemy zaimplementować logikę przetwarzania interfejsu użytkownika aplikacji i uzyskiwać tokeny interaktywnie przy użyciu biblioteki MSAL.

Biblioteka MSAL udostępnia dwie podstawowe metody uzyskiwania tokenów: `acquireTokenSilently()` i `acquireTokenInteractively()`: 

- `acquireTokenSilently()` próbuje zalogować użytkownika i uzyskiwanie tokenów, bez żadnej interakcji użytkownika, tak długo, jak konto usługi jest obecny.

- `acquireTokenInteractively()` zawsze wyświetli interfejs użytkownika, gdy próba zalogowania się użytkownika. Ona używać plików cookie sesji w przeglądarce lub konta w aplikacji Microsoft authenticator, aby zapewnić środowisko interaktywne z logowania jednokrotnego.

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

#### <a name="get-a-token-interactively"></a>Pobierz token interaktywnie

Poniższy kod pobiera token po raz pierwszy, tworząc `MSALInteractiveTokenParameters` obiektu i wywoływania `acquireToken`. Następnie dodasz kod, który:

1. Tworzy `MSALInteractiveTokenParameters` z zakresami.
2. Wywołania `acquireToken()` parametrami utworzony.
3. Obsługuje błędy. Aby uzyskać więcej szczegółów, zobacz [Przewodnik obsługi błędów dla systemu iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Error-Handling).
4. Obsługuje przypadek, pomyślne.

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



#### <a name="get-a-token-silently"></a>Uzyskać token w trybie dyskretnym

Aby nabyć zaktualizowanego tokenu w trybie dyskretnym, Dodaj następujący kod, aby `ViewController` klasy. Tworzy `MSALSilentTokenParameters` obiektów i wywołuje `acquireTokenSilent()`:

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

Po utworzeniu tokenu, aplikację można użyć go w nagłówku HTTP się z żądaniem autoryzowanego do programu Microsoft Graph:

| Klucz nagłówka    | value                 |
| ------------- | --------------------- |
| Authorization | Elementu nośnego \<token dostępu > |

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

Zobacz [interfejsu API Microsoft Graph](https://graph.microsoft.com) Aby dowiedzieć się więcej na temat interfejsu API programu Microsoft Graph.

### <a name="use-msal-for-sign-out"></a>Użycia biblioteki MSAL do wylogowania

Następnie dodaj obsługę wylogowania.

> [!Important]
> Wylogowywanie z biblioteki MSAL usuwa wszelkie informacje o użytkowniku z aplikacji, ale użytkownik nadal będzie miał aktywną sesję na swoim urządzeniu. Jeśli użytkownik spróbuje się zalogować ponownie ich może zostać wyświetlony interfejs użytkownika logowania, ale nie może być konieczne ponownie wprowadzić swoje poświadczenia, ponieważ sesja urządzenie jest nadal aktywne.

Aby dodać możliwość wyrejestrowywania, Dodaj następujący kod wewnątrz `ViewController` klasy. Ta metoda przechodzi cyklicznie przez wszystkie konta i usuwa je:

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

Domyślnie biblioteka MSAL buforuje tokenów aplikacji w pęku kluczy systemu iOS. 

Aby włączyć buforowanie tokenów:
1. Przejdź do ustawień projektu Xcode > **kartę możliwości** > **Włącz udostępnianie łańcucha kluczy**
2. Kliknij przycisk **+** i wprowadź `com.microsoft.adalcache` jako **grupy łańcucha kluczy** wpisu.

### <a name="add-helper-methods"></a>Dodaj metody pomocnika

Dodaj następujące metody pomocnika do `ViewController` klasy do ukończenia przykładu:

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

Ta aplikacja została stworzona w scenariuszu jednego konta. Biblioteka MSAL obsługuje również scenariusze wielu konta, ale wymaga wykonania dodatkowych czynności z aplikacji. Należy utworzyć interfejs użytkownika, aby pomóc użytkownika wybierz konto, które mają być użyte dla każdej akcji, które wymagają tokenów. Alternatywnie aplikację można wdrożyć heurystyki o wybranie konta do użycia za pomocą `getAccounts()` metody.

## <a name="test-your-app"></a>Testowanie aplikacji

### <a name="run-locally"></a>Uruchamianie lokalnie

Tworzenie i wdrażanie aplikacji na urządzeniu testowym lub w emulatorze. Powinna być w stanie zarejestrować się i uzyskiwać tokeny dla usługi Azure AD lub osobiste konta Microsoft.

Użytkownik loguje się do aplikacji po raz pierwszy zostanie wyświetlony monit, tożsamości firmy Microsoft do wyrażenia zgody na uprawnienia wymagane.  Mimo że większość użytkowników stanie wyrażanie zgody, niektóre dzierżaw usługi Azure AD zostało wyłączone przez zgody użytkownika, który wymaga zgody w imieniu wszystkich użytkowników, administratorów. Aby zapewnić obsługę tego scenariusza, należy zarejestrować zakresy Twojej aplikacji w witrynie Azure portal.

Po zalogowaniu się w aplikacji będą wyświetlane dane zwrócone przez program Microsoft Graph `/me` punktu końcowego.

## <a name="get-help"></a>Uzyskiwanie pomocy

Odwiedź stronę [Pomoc i obsługa techniczna](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) Jeśli masz problemy z tego samouczka lub z platformą Microsoft identity.
