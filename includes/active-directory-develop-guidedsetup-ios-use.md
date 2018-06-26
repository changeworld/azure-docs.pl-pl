## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Użyj biblioteki uwierzytelniania firmy Microsoft (MSAL), aby uzyskać token dla interfejsu API programu Microsoft Graph

Otwórz `ViewController.swift` i Zastąp kod:

```swift
import UIKit
import MSAL

class ViewController: UIViewController, UITextFieldDelegate, URLSessionDelegate {
    
    let kClientID = "Your_Application_Id_Here"
    let kAuthority = "https://login.microsoftonline.com/common/v2.0"

    let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
    let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
    
    var accessToken = String()
    var applicationContext = MSALPublicClientApplication.init()

    @IBOutlet weak var loggingText: UITextView!
    @IBOutlet weak var signoutButton: UIButton!

     // This button will invoke the call to the Microsoft Graph API. It uses the
     // built in Swift libraries to create a connection.
    
    @IBAction func callGraphButton(_ sender: UIButton) {
        
        
        do {
            
            // We check to see if we have a current signed-in user. If we don't, then we need to sign someone in.
            // We throw an interactionRequired so that we trigger the interactive sign-in.
            
            if  try self.applicationContext.users().isEmpty {
                throw NSError.init(domain: "MSALErrorDomain", code: MSALErrorCode.interactionRequired.rawValue, userInfo: nil)
            } else {
            
            // Acquire a token for an existing user silently
            
            try self.applicationContext.acquireTokenSilent(forScopes: self.kScopes, user: applicationContext.users().first) { (result, error) in
    
                    if error == nil {
                        self.accessToken = (result?.accessToken)!
                        self.loggingText.text = "Refreshing token silently)"
                        self.loggingText.text = "Refreshed Access token is \(self.accessToken)"
                        
                        self.signoutButton.isEnabled = true;
                        self.getContentWithToken()
    
                    } else {
                        self.loggingText.text = "Could not acquire token silently: \(error ?? "No error information" as! Error)"
    
                    }
                }
            }
        }  catch let error as NSError {
            
            // interactionRequired means we need to ask the user to sign in. This usually happens
            // when the user's Refresh Token is expired or if the user has changed their password
            // among other possible reasons.
            
            if error.code == MSALErrorCode.interactionRequired.rawValue {
                
                self.applicationContext.acquireToken(forScopes: self.kScopes) { (result, error) in
                        if error == nil {
                            self.accessToken = (result?.accessToken)!
                            self.loggingText.text = "Access token is \(self.accessToken)"
                            self.signoutButton.isEnabled = true;
                            self.getContentWithToken()
                            
                        } else  {
                            self.loggingText.text = "Could not acquire token: \(error ?? "No error information" as! Error)"
                        }
                }
                
            }
            
        } catch {
            
            // This is the catch all error.
            
            self.loggingText.text = "Unable to acquire token. Got error: \(error)"
            
        }
    }

    override func viewDidLoad() {
        super.viewDidLoad()
        
        do {
             // Initialize a MSALPublicClientApplication with a given clientID and authority
            self.applicationContext = try MSALPublicClientApplication.init(clientId: kClientID, authority: kAuthority)
        } catch {
            self.loggingText.text = "Unable to create Application Context. Error: \(error)"
        }
    }


    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    
    override func viewWillAppear(_ animated: Bool) {
        
        if self.accessToken.isEmpty {
            signoutButton.isEnabled = false; 
        }
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Więcej informacji
#### <a name="getting-a-user-token-interactively"></a>Pobieranie tokenu użytkownika interakcyjnego
Wywoływanie `acquireToken` metoda powoduje monitowanie użytkownika do logowania się w oknie przeglądarki. Aplikacje wymagają zazwyczaj użytkownika do logowania interakcyjnego potrzebnych do uzyskania dostępu do chronionego zasobu po raz pierwszy lub gdy dyskretnej operacji można uzyskać tokenu kończy się niepowodzeniem (np. hasło użytkownika wygasło).

#### <a name="getting-a-user-token-silently"></a>Pobieranie tokenu użytkownika dyskretnej
`acquireTokenSilent` Metoda obsługuje przejęć tokenu i wznowienie bez interakcji użytkownika. Po `acquireToken` jest wykonywana po raz pierwszy `acquireTokenSilent` jest metoda często używane do uzyskiwania tokenów umożliwiają dostęp do chronionych zasobów w kolejnych wywołaniach — jako wywołania żądania lub odnowić tokeny zostały wprowadzone w trybie dyskretnym.

Po pewnym czasie `acquireTokenSilent` zakończy się niepowodzeniem — np. użytkownik został wylogowany, lub zmieniono hasła na innym urządzeniu. Gdy MSAL wykryje, że problem można rozwiązać, gdyż akcję interaktywne, jego generowane `MSALErrorCode.interactionRequired` wyjątku. Aplikacja może obsłużyć tego wyjątku na dwa sposoby:

1.  Nawiązywanie połączeń z `acquireToken` natychmiast, która powoduje monitowanie użytkownika do logowania. Ten wzorzec jest zazwyczaj używany w aplikacji online w przypadku, gdy nie żadnej zawartości w trybie offline w aplikacji dostępnej dla użytkownika. Przykładowa aplikacja wygenerowane za pomocą tego Instalatora z przewodnikiem korzysta z tego wzorca: widoczny w czasie działania pierwszy wykonania aplikacji. Ponieważ żaden użytkownik nie jest nigdy korzystali z aplikacji, `applicationContext.users().first` będzie zawierać wartości null, a ` MSALErrorCode.interactionRequired ` zostanie wygenerowany wyjątek. Następnie kod w próbce obsługuje wyjątek przez wywołanie metody `acquireToken` co powoduje monitowanie użytkownika do logowania.

2.  Aplikacje można wprowadzić oznaczenia wizualne dla użytkownika, który interakcyjnego logowania jest wymagana, więc użytkownik może wybrać, właściwym czasie zalogować się lub aplikacji można ponowić próbę `acquireTokenSilent` w późniejszym czasie. To zwykle jest używana, gdy użytkownik może użyć innych funkcji aplikacji bez są zakłócone — na przykład Brak dostępnej zawartości w trybie offline w aplikacji. W takim przypadku użytkownik można wybrać, jeśli chcą Zaloguj się do uzyskania dostępu do zabezpieczonych zasobów lub Odśwież nieaktualne informacje lub aplikacji można zdecydować ponowić próbę `acquireTokenSilent` przywrócenie sieci po są tymczasowo niedostępne.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Wywołanie interfejsu API programu Microsoft Graph przy użyciu tokenu, który został uzyskany

Dodawanie nowej metody poniżej do `ViewController.swift`. Ta metoda umożliwia `GET` żądania dotyczącego przy użyciu interfejsu API programu Microsoft Graph *nagłówek autoryzacji HTTP*:

```swift
func getContentWithToken() {
    
    let sessionConfig = URLSessionConfiguration.default
    
    // Specify the Graph API endpoint
    let url = URL(string: kGraphURI)
    var request = URLRequest(url: url!)
    
    // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
    request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
    let urlSession = URLSession(configuration: sessionConfig, delegate: self, delegateQueue: OperationQueue.main)
    
    urlSession.dataTask(with: request) { data, response, error in
        let result = try? JSONSerialization.jsonObject(with: data!, options: [])
                    if result != nil {
                
                self.loggingText.text = result.debugDescription
            }
        }.resume()
}
```

<!--start-collapse-->
### <a name="making-a-rest-call-against-a-protected-api"></a>Wywołania chronionego interfejsu API REST

W tej przykładowej aplikacji `getContentWithToken()` używa metody w celu HTTP `GET` żądania względem chronionego zasobu, który wymaga tokenu, a następnie wróć zawartości do obiektu wywołującego. Ta metoda dodaje token nabyte w *nagłówek autoryzacji HTTP*. Dla tego przykładu zasób jest interfejsu API programu Microsoft Graph *mnie* punktu końcowego — Wyświetla informacje o profilu użytkownika.
<!--end-collapse-->

## <a name="set-up-sign-out"></a>Konfigurowanie wylogowania

Dodaj następującą metodę do `ViewController.swift` się wylogować użytkownika:

```swift 
@IBAction func signoutButton(_ sender: UIButton) {

    do {
        
        // Removes all tokens from the cache for this application for the provided user
        // first parameter:   The user to remove from the cache
        
        try self.applicationContext.remove(self.applicationContext.users().first)
        self.signoutButton.isEnabled = false;
        
    } catch let error {
        self.loggingText.text = "Received error signing user out: \(error)"
    }
}
```
<!--start-collapse-->
### <a name="more-info-on-sign-out"></a>Więcej informacji dotyczących wylogowania

`signoutButton` Metoda usuwa użytkownika z pamięci podręcznej użytkownika MSAL — to skutecznie poinformuje MSAL zapomnieć bieżącego użytkownika, więc żądanie przyszłych, aby uzyskać token powiodą się tylko wtedy, gdy staje się interaktywne.

Mimo że w tym przykładzie aplikacja obsługuje pojedynczego użytkownika, MSAL obsługuje scenariusze, w których może być podpisana wiele kont w tym samym czasie — przykładem jest aplikacja poczty e-mail, w których użytkownik ma wiele kont.
<!--end-collapse-->

## <a name="register-the-callback"></a>Zarejestruj wywołania zwrotnego

Gdy użytkownik jest uwierzytelniany, przeglądarka przekierowuje użytkownika do aplikacji. Wykonaj poniższe kroki, aby zarejestrować to wywołanie zwrotne:

1.  Otwórz `AppDelegate.swift` i zaimportować MSAL:

```swift
import MSAL
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Dodaj następującą metodę do Twojej <code>AppDelegate</code> klasy do obsługi wywołania zwrotne:
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

