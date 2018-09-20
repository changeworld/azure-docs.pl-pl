---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: c6d5fab6ff065dee336c510e3f94583cb0c4960b
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46466175"
---
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Użyj Microsoft Authentication Library (MSAL), aby uzyskać token dla interfejsu API programu Microsoft Graph

Otwórz `ViewController.swift` i Zastąp kod za pomocą:

```swift
import UIKit
import MSAL

/// 😃 A View Controller that will respond to the events of the Storyboard.
class ViewController: UIViewController, UITextFieldDelegate, URLSessionDelegate {
    
    // Update the below to your client ID you received in the portal. The below is for running the demo only
    let kClientID = "Your_Application_Id_Here"
    
    // These settings you don't need to edit unless you wish to attempt deeper scenarios with the app.
    let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
    let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
    let kAuthority = "https://login.microsoftonline.com/common"
    
    var accessToken = String()
    var applicationContext : MSALPublicClientApplication?

    @IBOutlet weak var loggingText: UITextView!
    @IBOutlet weak var signoutButton: UIButton!

    /**
        Setup public client application in viewDidLoad
    */

    override func viewDidLoad() {

        super.viewDidLoad()

        do {

            /**
             Initialize a MSALPublicClientApplication with a given clientID and authority
             - clientId:            The clientID of your application, you should get this from the app portal.
             - authority:           A URL indicating a directory that MSAL can use to obtain tokens. In Azure AD
                                    it is of the form https://<instance/<tenant>, where <instance> is the
                                    directory host (e.g. https://login.microsoftonline.com) and <tenant> is a
                                    identifier within the directory itself (e.g. a domain associated to the
                                    tenant, such as contoso.onmicrosoft.com, or the GUID representing the
                                    TenantID property of the directory)
             - error                The error that occurred creating the application object, if any, if you're
                                    not interested in the specific error pass in nil.
             */

            guard let authorityURL = URL(string: kAuthority) else {
                self.loggingText.text = "Unable to create authority URL"
                return
            }

            let authority = try MSALAuthority(url: authorityURL)
            self.applicationContext = try MSALPublicClientApplication(clientId: kClientID, authority: authority)

        } catch let error {
            self.loggingText.text = "Unable to create Application Context \(error)"
        }
    }

    override func viewWillAppear(_ animated: Bool) {

        super.viewWillAppear(animated)
        signoutButton.isEnabled = !self.accessToken.isEmpty
    }
    
    /**
     This button will invoke the authorization flow.
    */

    @IBAction func callGraphButton(_ sender: UIButton) {

        if self.currentAccount() == nil {
            // We check to see if we have a current logged in account.
            // If we don't, then we need to sign someone in.
            self.acquireTokenInteractively()
        } else {
            self.acquireTokenSilently()
        }
    }

    func acquireTokenInteractively() {

        guard let applicationContext = self.applicationContext else { return }

        applicationContext.acquireToken(forScopes: kScopes) { (result, error) in

            if let error = error {

                self.updateLogging(text: "Could not acquire token: \(error)")
                return
            }

            guard let result = result else {

                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }

            self.accessToken = result.accessToken!
            self.updateLogging(text: "Access token is \(self.accessToken)")
            self.updateSignoutButton(enabled: true)
            self.getContentWithToken()
        }
    }

    func acquireTokenSilently() {

        guard let applicationContext = self.applicationContext else { return }

        /**
         Acquire a token for an existing account silently
         - forScopes:           Permissions you want included in the access token received
                                in the result in the completionBlock. Not all scopes are
                                guaranteed to be included in the access token returned.
         - account:             An account object that we retrieved from the application object before that the
                                authentication flow will be locked down to.
         - completionBlock:     The completion block that will be called when the authentication
                                flow completes, or encounters an error.
         */

        applicationContext.acquireTokenSilent(forScopes: kScopes, account: self.currentAccount()) { (result, error) in

            if let error = error {

                let nsError = error as NSError

                // interactionRequired means we need to ask the user to sign-in. This usually happens
                // when the user's Refresh Token is expired or if the user has changed their password
                // among other possible reasons.
                if (nsError.domain == MSALErrorDomain
                    && nsError.code == MSALErrorCode.interactionRequired.rawValue) {

                    DispatchQueue.main.async {
                        self.acquireTokenInteractively()
                    }

                } else {
                    self.updateLogging(text: "Could not acquire token silently: \(error)")
                }

                return
            }

            guard let result = result else {

                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }

            self.accessToken = result.accessToken!
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignoutButton(enabled: true)
            self.getContentWithToken()
        }
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

<!--start-collapse-->
### <a name="more-information"></a>Więcej informacji
#### <a name="getting-a-user-token-interactively"></a>Pobieranie tokenu użytkownika interaktywnego
Wywoływanie `acquireToken` metoda nie powoduje monitowanie użytkownika o Zaloguj się w oknie przeglądarki. Aplikacje zwykle wymagają użytkownika do logowania interakcyjnego potrzebują dostępu do chronionego zasobu po raz pierwszy lub silent operacji można uzyskać tokenu kończy się niepowodzeniem (np. hasło tego użytkownika wygasła).

#### <a name="getting-a-user-token-silently"></a>Pobieranie tokenu użytkownika dyskretnie
`acquireTokenSilent` Obsługiwała pozyskanie tokenu i wznowienie bez żadnej interakcji użytkownika. Po `acquireToken` jest wykonywany po raz pierwszy `acquireTokenSilent` jest metodą, często używane do uzyskiwania tokenów, które umożliwiają dostęp do chronionych zasobów dla kolejnych wywołań — jak do żądania lub odnowienia tokenów wywołań dyskretnie.

Po pewnym czasie `acquireTokenSilent` zakończy się niepowodzeniem — np. użytkownik zalogował lub została zmieniona swojego hasła na innym urządzeniu. Gdy biblioteki MSAL wykryje, że ten problem można rozwiązać, wymagając akcję interaktywne, jest on uruchamiany `MSALErrorCode.interactionRequired` wyjątku. Aplikacja może obsłużyć ten wyjątek na dwa sposoby:

1.  Wywołania względem `acquireToken` natychmiast, które powoduje monitowanie użytkownika do logowania. Ten wzorzec jest zwykle używany w aplikacjach w trybie online w przypadku, gdy brak offline zawartości w aplikacji dostępne dla użytkownika. Przykładowa aplikacja generowana za pomocą tego Instalatora z przewodnikiem korzysta z tego wzorca: widoczne w czasie działania pierwszego wykonania aplikacji. Ponieważ żaden użytkownik nie jest nigdy nie użył aplikacji `applicationContext.allAccounts().first` będzie zawierać wartości null i ` MSALErrorCode.interactionRequired ` zostanie zgłoszony wyjątek. Następnie kod w przykładzie obsługuje wyjątek, wywołując `acquireToken` skutkuje monitowania użytkownika do logowania.

2.  Aplikacje może być wizualne oznaczenie do użytkownika, który interakcyjnego logowania jest wymagana, dzięki czemu użytkownik może wybrać w odpowiednim czasie, aby zalogować się lub aplikacji można ponowić próbę `acquireTokenSilent` w późniejszym czasie. Służy to zwykle po użytkownik może użyć innych funkcji aplikacji bez zakłócana — na przykład Brak dostępnej zawartości w trybie offline w aplikacji. W tym przypadku użytkownik może wybrać, gdy mają logować się do uzyskania dostępu do chronionego zasobu lub w celu odświeżenia nieaktualnych informacji lub aplikacji można zdecydować ponowić próbę `acquireTokenSilent` po przywróceniu sieci po jest tymczasowo niedostępny.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Wywołanie interfejsu API programu Microsoft Graph przy użyciu tokenu, który został uzyskany

Dodaj nową metodę poniżej, aby `ViewController.swift`. Ta metoda umożliwia `GET` żądania i porównuje przy użyciu interfejsu API Microsoft Graph *nagłówek autoryzacji HTTP*:

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

<!--start-collapse-->
### <a name="making-a-rest-call-against-a-protected-api"></a>Wywołania chronionego interfejsu API REST

W tej przykładowej aplikacji `getContentWithToken()` metoda umożliwia HTTP `GET` żądania względem chronionego zasobu, który wymaga tokenu, a następnie wróć zawartości do obiektu wywołującego. Metoda ta umożliwia dodanie uzyskano token w *nagłówek autoryzacji HTTP*. W tym przykładzie zasób jest interfejsu API programu Microsoft Graph *mnie* punktu końcowego — Wyświetla informacje o profilu użytkownika.
<!--end-collapse-->

## <a name="set-up-sign-out"></a>Konfigurowanie funkcji wylogowania

Dodaj następującą metodę do `ViewController.swift` do wylogowania użytkownika:

```swift 
 @IBAction func signoutButton(_ sender: UIButton) {

        guard let applicationContext = self.applicationContext else { return }

        guard let account = self.currentAccount() else { return }

        do {

            /**
             Removes all tokens from the cache for this application for the provided account
             - account:    The account to remove from the cache
             */

            try applicationContext.remove(account)
            self.loggingText.text = ""
            self.signoutButton.isEnabled = false

        } catch let error as NSError {

            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }

}
```
<!--start-collapse-->
### <a name="more-info-on-sign-out"></a>Więcej informacji o funkcji wylogowania

`signoutButton` Metoda usuwa użytkownika z pamięci podręcznej biblioteki MSAL użytkownika — to skutecznie poinformuje MSAL zapomnieć bieżącego użytkownika, dzięki czemu przyszłe żądania, aby uzyskać token powiodą się tylko wtedy, gdy staje się interakcyjne.

Mimo że aplikacja, w tym przykładzie obsługuje pojedynczego użytkownika, biblioteka MSAL obsługuje scenariusze, w którym może być podpisana wielu kont w tym samym czasie — przykładem jest aplikacja poczty e-mail, gdy użytkownik ma wiele kont.
<!--end-collapse-->

## <a name="register-the-callback"></a>Zarejestrowanie wywołania zwrotnego

Gdy użytkownik jest uwierzytelniany, przeglądarka przekierowuje użytkownika do aplikacji. Wykonaj poniższe kroki, aby zarejestrować to wywołanie zwrotne:

1.  Otwórz `AppDelegate.swift` i zaimportuj MSAL:

```swift
import MSAL
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Dodaj następującą metodę do swojej <code>AppDelegate</code> klasy do obsługi wywołań zwrotnych:
</li>
</ol>

```swift
// @brief Handles inbound URLs. Checks if the URL matches the redirect URI for a pending AppAuth
// authorization request and if so, will look for the code in the response.

func application(_ application: UIApplication, open url: URL, sourceApplication: String?, annotation: Any) -> Bool {
    
    print("Received callback!")
    
    MSALPublicClientApplication.handleMSALResponse(url)
    
    return true
}
```
