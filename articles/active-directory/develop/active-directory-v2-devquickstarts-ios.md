---
title: Dodaj logowanie do aplikacji systemu iOS przy użyciu punktu końcowego v2.0 usługi Azure AD | Dokumentacja firmy Microsoft
description: Jak utworzyć aplikację systemu iOS logujący się użytkownicy z obu osobistego konta Microsoft i kont służbowych przy użyciu bibliotek innych firm.
services: active-directory
author: CelesteDG
manager: mtillman
ms.assetid: fd3603c0-42f7-438c-87b5-a52d20d6344b
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/07/2017
ms.author: celested
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 7476417e6585976ea2404a83602a6d9aa77d9c7a
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157380"
---
# <a name="add-sign-in-to-an-ios-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Dodaj logowanie do aplikacji systemu iOS przy użyciu biblioteki innych firm z interfejsu API programu Graph przy użyciu punktu końcowego v2.0
Platforma Microsoft Identity korzysta z otwartych standardów, takich jak OAuth2 i OpenID Connect. Deweloperzy mogą używać dowolnej bibliotece potrzebnymi do integracji z naszych usług. Aby pomóc deweloperom korzystać z innych bibliotek platformy, możemy napisanych kilka wskazówki podobny do pokazują, jak skonfigurować biblioteki innych firm, aby nawiązać połączenia z platformą tożsamości Microsoft. Większość bibliotek, które implementują [specyfikację RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) można nawiązać połączenia z platformą tożsamości Microsoft.

Aplikacja, która tworzy tego przewodnika użytkownicy mogą zalogować się do organizacji i wyszukać innych użytkowników w organizacji za pomocą interfejsu API programu Graph.

Jeśli jesteś nowym użytkownikiem OAuth2 lub OpenID Connect, znacznie to przykładowa konfiguracja może nie mieć sensu do Ciebie. Zalecamy przeczytanie [protokołów v2.0 - przepływu kodu autoryzacji OAuth 2.0](active-directory-v2-protocols-oauth-code.md) w tle.

> [!NOTE]
> Niektóre funkcje platformy, które mają wyrażenia OAuth2 lub OpenID Connect standardy, takie jak dostęp warunkowy i zarządzanie zasadami usługi Intune, trzeba użyć naszych Otwórz źródło bibliotek tożsamości usługi Microsoft Azure.
> 
> 

Punktu końcowego v2.0 nie obsługuje wszystkich scenariuszy Azure Active Directory i funkcji.

> [!NOTE]
> Aby ustalić, czy należy używać punktu końcowego v2.0, przeczytaj o [ograniczenia v2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="download-code-from-github"></a>Pobierz kod z usługi GitHub
Kod używany w tym samouczku jest przechowywany [ w serwisie GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-v2). Aby z niego skorzystać, można [pobrać szkielet aplikacji w formie .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) lub sklonować szkielet:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

Można także po prostu pobrać przykład i od razu rozpocząć:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

## <a name="register-an-app"></a>Rejestracja aplikacji
Utwórz nową aplikację na [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), lub wykonaj szczegółowy opis kroków w [jak zarejestrować aplikację z punktem końcowym v2.0](active-directory-v2-app-registration.md). Upewnij się, że:

* Kopiuj **identyfikator aplikacji** przypisany do aplikacji, ponieważ będzie on potrzebny wkrótce.
* Dodaj **Mobile** platformy aplikacji.
* Kopiuj **identyfikator URI przekierowania** z portalu. Należy użyć wartości domyślnej `urn:ietf:wg:oauth:2.0:oob`.

## <a name="download-the-third-party-nxoauth2-library-and-create-a-workspace"></a>Pobierz biblioteki NXOAuth2 innych firm i tworzenie obszaru roboczego
W ramach tego przewodnika skorzystasz OAuth2Client z serwisu GitHub, czyli OAuth2 biblioteki dla systemu Mac OS X i iOS (Cocoa i Cocoa touch). Ta biblioteka jest oparta na wersji 10 specyfikacji OAuth2. Ona profil aplikacji natywnej implementuje i obsługuje punktu końcowego autoryzacji użytkownika. Są to wszystkie zadania, które należy zintegrować z platformą tożsamości Microsoft.

### <a name="add-the-library-to-your-project-by-using-cocoapods"></a>Dodaj bibliotekę do projektu przy użyciu programu CocoaPods
CocoaPods to menedżer zależności dla projektów Xcode. Poprzednie kroki instalacji zarządza automatycznie.

```
$ vi Podfile
```
1. Dodaj następujący kod do tego pliku podfile:
   
    ```
     platform :ios, '8.0'
   
     target 'QuickStart' do
   
     pod 'NXOAuth2Client'
   
     end
    ```
2. Załaduj podfile przy użyciu programu CocoaPods. Spowoduje to utworzenie nowego obszaru roboczego Xcode, który zostanie załadowany.
   
    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

## <a name="explore-the-structure-of-the-project"></a>Eksploruj struktury projektu
Skonfigurowano następującą strukturę dla naszych projektu w szkielecie:

* Widok wzorca wyszukiwania nazwy UPN
* Widok szczegółów dla danych dotyczących wybranego użytkownika
* Widok logowania, gdzie użytkownik może zalogować się do aplikacji do badania wykresu

Firma Microsoft zostanie przeniesione do różnych plików w szkielecie dodać uwierzytelniania. Inne części kodu, na przykład kod visual nie odnoszą się do tożsamości, ale są udostępniane dla Ciebie.

## <a name="set-up-the-settingsplst-file-in-the-library"></a>Konfigurowanie pliku settings.plst w bibliotece
* Otwórz projekt szybkiego startu `settings.plist` pliku. Zastąp wartości elementów w sekcji do wartości, które były używane w portalu Azure. Kod będzie odwoływać tych wartości, przy każdym użyciu biblioteki uwierzytelniania usługi Active Directory.
  * `clientId` Jest identyfikator klienta aplikacji, który został skopiowany z portalu.
  * `redirectUri` Jest adres URL przekierowania, czy dostarczono portalu.

## <a name="set-up-the-nxoauth2client-library-in-your-loginviewcontroller"></a>Konfigurowanie biblioteki NXOAuth2Client w Twojej LoginViewController
Biblioteka NXOAuth2Client wymaga niektórych wartości do. Po ukończeniu zadania służy nabytych przez niego token do wywołania interfejsu API programu Graph. Ponieważ `LoginView` zostanie wywołana dowolnej chwili, musimy uwierzytelniania, warto umieścić wartości konfiguracji do tego pliku.

* Dodajmy niektórych wartości na `LoginViewController.m` plik, aby ustawić kontekst uwierzytelniania i autoryzacji. Szczegółowe informacje o wartości należy wykonać kod.
  
    ```objc
    NSString *scopes = @"openid offline_access User.Read";
    NSString *authURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/authorize";
    NSString *loginURL = @"https://login.microsoftonline.com/common/login";
    NSString *bhh = @"urn:ietf:wg:oauth:2.0:oob?code=";
    NSString *tokenURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/token";
    NSString *keychain = @"com.microsoft.azureactivedirectory.samples.graph.QuickStart";
    static NSString * const kIDMOAuth2SuccessPagePrefix = @"session_state=";
    NSURL *myRequestedUrl;
    NSURL *myLoadedUrl;
    bool loginFlow = FALSE;
    bool isRequestBusy;
    NSURL *authcode;
    ```

Oto szczegółowe informacje o kodzie.

Pierwszy ciąg jest przeznaczony dla `scopes`. `User.Read` Wartość umożliwia odczytanie profilu podstawowego zalogowanego użytkownika.

Dowiedz się więcej o wszystkie dostępne zakresy na [zakresy uprawnień Microsoft Graph](https://graph.microsoft.io/docs/authorization/permission_scopes).

Aby uzyskać `authURL`, `loginURL`, `bhh`, i `tokenURL`, należy użyć wartości podane wcześniej. Użycie typu open source bibliotek tożsamości usługi Microsoft Azure, możemy rozwiń tych danych można za pomocą naszych punktu końcowego metadanych. Włożyliśmy dużo wysiłku w wyodrębnianie tych wartości.

Wartość `keychain` to kontener, którego biblioteka NXOAuth2Client używa do tworzenia łańcucha kluczy do przechowywania tokenów. Jeśli chcesz pobrać rejestracji jednokrotnej (SSO) w wielu aplikacjach, można określić tego samego łańcucha kluczy w każdej aplikacji i żądanie stosowania tego łańcucha kluczy w Twoje uprawnienia Xcode. Wyjaśnienie jest zawarte w dokumentacji firmy Apple.

Pozostała część te wartości są wymagane do korzystania z biblioteki oraz tworzenie miejsc do przenoszenia wartości w kontekście.

### <a name="create-a-url-cache"></a>Tworzenie pamięci podręcznej adres URL
Wewnątrz `(void)viewDidLoad()`, który zawsze jest wywoływana po załadowaniu widoku, pamięć podręczną do użytku w naszym primes następujący kod.

Dodaj następujący kod:

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    self.loginView.delegate = self;
    [self setupOAuth2AccountStore];
    [self requestOAuth2Access];
    NSURLCache *URLCache = [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                                         diskCapacity:20 * 1024 * 1024
                                                             diskPath:nil];
    [NSURLCache setSharedURLCache:URLCache];

}
```

### <a name="create-a-webview-for-sign-in"></a>Tworzenie widoku sieci Web dla logowania
Widok sieci Web można monit o podanie dodatkowych czynników, takich jak wiadomość SMS (jeśli jest skonfigurowane) lub zwrócić komunikaty o błędach do użytkownika. W tym miejscu zostanie ustawiona konfigurowanie widoku sieci Web, a następnie napisać kod, aby obsłużyć wywołania zwrotne, które nastąpi w widoku sieci Web z usług tożsamości.

```objc
-(void)requestOAuth2Access {
    //to sign in to Microsoft APIs using OAuth2, we must show an embedded browser (UIWebView)
    [[NXOAuth2AccountStore sharedStore] requestAccessToAccountWithType:@"myGraphService"
                                   withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
                                       //navigate to the URL returned by NXOAuth2Client

                                       NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
                                       [self.loginView loadRequest:r];
                                   }];
}
```

### <a name="override-the-webview-methods-to-handle-authentication"></a>Przesłoń metody WebView na potrzeby obsługi uwierzytelniania
Aby przekazać widoku sieci Web, co się dzieje, gdy użytkownik musi zalogować się, jak wspomniano wcześniej, można Wklej następujący kod.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {

    // We get the auth token from a redirect so we need to handle that in the webview.

    if (![NSThread isMainThread]) {
        [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:) withObject:URL waitUntilDone:YES];
        return;
    }

    NSURLRequest *hostnameURLRequest = [NSURLRequest requestWithURL:URL cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:10.0f];
    isRequestBusy = YES;
    [self.loginView loadRequest:hostnameURLRequest];

    NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)", self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {

    NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL, (long)navigationType);

    // The webview is where all the communication happens. Slightly complicated.

    myLoadedUrl = [webView.request mainDocumentURL];
    NSLog(@"***Loaded url: %@", myLoadedUrl);

    //if the UIWebView is showing our authorization URL or consent URL, show the UIWebView control
    if ([request.URL.absoluteString rangeOfString:authURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:loginURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:bhh options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = YES;
        [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }
    else {
        self.loginView.hidden = NO;
        //read the Location from the UIWebView, this is how Microsoft APIs is returning the
        //authentication code and relation information. This is controlled by the redirect URL we chose to use from Microsoft APIs
        //continue the OAuth2 flow
       // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }

    return YES;

}
```

### <a name="write-code-to-handle-the-result-of-the-oauth2-request"></a>Napisz kod obsługujący wynik żądania OAuth2
Następujący kod będzie obsługiwać redirectURL, która zwraca widok sieci Web. Jeśli uwierzytelnianie nie powiodło się, kod ponowi próbę. W tym samym czasie biblioteki zapewni błędu, który można wyświetlać w konsoli lub obsługi asynchronicznie.

```objc
- (void)handleOAuth2AccessResult:(NSString *)accessResult {

    AppData* data = [AppData getInstance];

    //parse the response for success or failure
     if (accessResult)
    //if success, complete the OAuth2 flow by handling the redirect URL and obtaining a token
     {
         [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
    } else {
        //start over
        [self requestOAuth2Access];
    }
}
```

### <a name="set-up-the-oauth-context-called-account-store"></a>Skonfiguruj kontekst OAuth (nazywane konta magazynu)
W tym miejscu możesz wywołać `-[NXOAuth2AccountStore setClientID:secret:authorizationURL:tokenURL:redirectURL:forAccountType:]` magazynu udostępnionego konta dla każdej usługi, który ma dostęp do aplikacji. Typ konta jest ciąg, który jest używany jako identyfikator dla niektórych usługi. Ponieważ uzyskujesz dostęp do interfejsu API programu Graph, kod odwołuje się do niego jako `"myGraphService"`. Możesz skonfigurować obserwatora, który informuje użytkownika, gdy dowolne zmiany z tokenem. Po uzyskaniu tokenu powrocie użytkownika z powrotem do `masterView`.

```objc
- (void)setupOAuth2AccountStore {


        AppData* data = [AppData getInstance];

    [[NXOAuth2AccountStore sharedStore] setClientID:data.clientId
                                             secret:data.secret
                                              scope:[NSSet setWithObject:scopes]
                                   authorizationURL:[NSURL URLWithString:authURL]
                                           tokenURL:[NSURL URLWithString:tokenURL]
                                        redirectURL:[NSURL URLWithString:data.redirectUriString]
                                      keyChainGroup: keychain
                                     forAccountType:@"myGraphService"];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      if (aNotification.userInfo) {
                                                          //account added, we have access
                                                          //we can now request protected data
                                                          NSLog(@"Success!! We have an access token.");
                                                          dispatch_async(dispatch_get_main_queue(),^ {

                                                              MasterViewController* masterViewController = [self.storyboard instantiateViewControllerWithIdentifier:@"masterView"];
                                                              [self.navigationController pushViewController:masterViewController animated:YES];
                                                          });
                                                      } else {
                                                          //account removed, we lost access
                                                      }
                                                  }];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      NSError *error = [aNotification.userInfo objectForKey:NXOAuth2AccountStoreErrorKey];
                                                      NSLog(@"Error!! %@", error.localizedDescription);
                                                  }];
}
```

## <a name="set-up-the-master-view-to-search-and-display-the-users-from-the-graph-api"></a>Ustawianie widoku głównego można wyszukiwać i wyświetlać użytkowników z interfejsu API programu Graph
Aplikacji Master-widok-kontroler (MVC), który wyświetla zwrócone dane w siatce wykracza poza zakres tego przewodnika, a wiele online samouczki wyjaśniają, jak utworzyć. Ten kod znajduje się w pliku szkielet. Jednak należy uwzględniać kilka czynności w aplikacji MVC:

* ODCIĘTA, gdy użytkownik wpisze coś w polu wyszukiwania
* Udostępniają obiektu danych do MasterView, więc można go wyświetlić wyniki w siatce

Robimy tych poniżej.

### <a name="add-a-check-to-see-if-youre-logged-in"></a>Dodaj znacznik wyboru, aby zobaczyć, czy użytkownik jest zalogowany
Aplikacja pojawia się tylko użytkownik nie jest zalogowany, więc sprawne działanie sprawdzić, czy istnieje już token w pamięci podręcznej. Jeśli nie, zostanie przekierowany do LoginView dla użytkownika do logowania. Jeśli odwołanie, jest użycie najlepszym sposobem na wykonywanie czynności po załadowaniu widoku `viewDidLoad()` metoda zapewniająca firmy Apple, NAS.

```objc
- (void)viewDidLoad {
    [super viewDidLoad];


    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];

        if (accounts.count == 0) {

        dispatch_async(dispatch_get_main_queue(),^ {

            LoginViewController* userSelectController = [self.storyboard instantiateViewControllerWithIdentifier:@"LoginUserView"];
            [self.navigationController pushViewController:userSelectController animated:YES];
        });
        }
```

### <a name="update-the-table-view-when-data-is-received"></a>Aktualizowanie widoku tabeli po odebraniu danych
Po powrocie z interfejsu API programu Graph danych należy do wyświetlania danych. Dla uproszczenia w tym miejscu jest cały kod, aby zaktualizować tabeli. Prawo wartości można wkleić tylko w kodzie umożliwiającego MVC.

```objc
#pragma mark - Table View

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {

        return [upnArray count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"TaskPrototypeCell" forIndexPath:indexPath];

    if ( cell == nil ) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"TaskPrototypeCell"];
    }

    User *user = nil;
     user = [upnArray objectAtIndex:indexPath.row];


    // Configure the cell
    cell.textLabel.text = user.name;
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];

    return cell;
}

```

### <a name="provide-a-way-to-call-the-graph-api-when-someone-types-in-the-search-field"></a>Umożliwiają wywołania interfejsu API programu Graph, gdy użytkownik wpisze w polu wyszukiwania
Jeśli użytkownik wpisze wyszukiwania w polu, musisz shove który za pośrednictwem do interfejsu API programu Graph. `GraphAPICaller` Klasy, która będzie kompilowana w poniższym kodzie, oddziela funkcji wyszukiwania z prezentacji. Na razie Napiszmy kod, który źródła znaków wyszukiwania do interfejsu API programu Graph. Firma Microsoft w tym celu udostępnia metodę o nazwie `lookupInGraph`, który przyjmuje ciąg, który chcesz wyszukać.

```objc

-(void)lookupInGraph:(NSString *)searchText {
if (searchText.length > 0) {

    };



        [GraphAPICaller searchUserList:searchText completionBlock:^(NSMutableArray* returnedUpns, NSError* error) {
            if (returnedUpns) {


                upnArray = returnedUpns;


            }
            else
            {
                UIAlertView *alertView = [[UIAlertView alloc]initWithTitle:nil message:[[NSString alloc]initWithFormat:@"Error : %@", error.localizedDescription] delegate:nil cancelButtonTitle:@"Retry" otherButtonTitles:@"Cancel", nil];

                [alertView setDelegate:self];

                dispatch_async(dispatch_get_main_queue(),^ {
                    [alertView show];
                });
            }


        }];


}
```

## <a name="write-a-helper-class-to-access-the-graph-api"></a>Zapis klasę pomocy umożliwiającą dostęp do interfejsu API programu Graph
Jest to podstawowy naszej aplikacji. Podczas gdy pozostałe został Wstawianie kodu we wzorcu MVC domyślnej od firmy Apple, w tym miejscu pisania kodu zapytania wykresu jako typy użytkowników, a następnie wróć tych danych. Oto kod i następuje szczegółowy opis.

### <a name="create-a-new-objective-c-header-file"></a>Utwórz nowy plik nagłówka Objective C
Nadaj nazwę plikowi `GraphAPICaller.h`i Dodaj następujący kod.

```objc
@interface GraphAPICaller : NSObject<NSURLConnectionDataDelegate>

+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray*, NSError* error))completionBlock;

@end
```

Miejscu zobaczysz, że określona metoda przyjmuje ciągiem i zwraca completionBlock. Ta completionBlock jako użytkownik może mieć odgadnąć, spowoduje zaktualizowanie tabeli podając obiektu wypełnione danych w czasie rzeczywistym jako wyszukiwanie użytkowników.

### <a name="create-a-new-objective-c-file"></a>Utwórz nowy plik języka Objective C
Nadaj nazwę plikowi `GraphAPICaller.m`i dodaj następującą metodę.

```objc
+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];

    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSDictionary* params = [self convertParamsToDictionary:searchString];

    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
                           }

                           completionBlock(Users, nil);
                       }
                       else
                       {
                           completionBlock(nil, error);
                       }

                   }];
}

```

Przejdźmy za pomocą tej metody szczegółowo.

Podstawowe ten kod jest w `NXOAuth2Request`, metodę, która przyjmuje parametry, które zdefiniowano w pliku settings.plist.

Pierwszym krokiem jest skonstruowanie prawo wywołania interfejsu API programu Graph. Ponieważ wywoływania `/users`, możesz określić, że przez dołączenie go do zasobu interfejsu API programu Graph wraz z wersją. Warto umieścić je w pliku ustawień zewnętrznych, ponieważ te można zmienić w miarę rozwoju środowisko interfejsu API.

```objc
NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
```

Następnie należy określić parametry, które należy również udostępnić wywołania interfejsu API programu Graph. Jest *bardzo ważne* czy należy umieszczać parametrów w punkcie końcowym zasobu, ponieważ która zostaje wyczyszczona dla wszystkich znaków zgodnych-URI w czasie wykonywania. Cały kod zapytania należy określić w parametrach.

```objc

NSDictionary* params = [self convertParamsToDictionary:searchString];
```

Można zauważyć wymaga `convertParamsToDictionary` metodę, która jeszcze nie zostać jeszcze zapisane. Umożliwia to teraz zrobić na końcu pliku:

```objc
+(NSDictionary*) convertParamsToDictionary:(NSString*)searchString
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

        NSString *query = [NSString stringWithFormat:@"startswith(givenName, '%@')", searchString];

           [dictionary setValue:query forKey:@"$filter"];



    return dictionary;
}

```
Następnie można użyć `NXOAuth2Request` metodę, aby odzyskać dane z interfejsu API w formacie JSON.

```objc
NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
```

Na koniec Przyjrzyjmy się jak zwrócić dane do MasterViewController. Dane zwraca serializowane i musi zostać deserializacji i załadowany w obiekcie, który MainViewController, jaką może wykorzystać. W tym celu ma szkielet `User.m/h` pliku, który tworzy obiekt użytkownika. Należy wypełnić tego obiektu użytkownika o informacje z wykresu.

```objc
                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
```


## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej
Jeśli został użyty szkielet i stosować wraz z tym przewodnikiem teraz należy uruchomić aplikację. Uruchomić symulatora, a następnie kliknij przycisk **Zaloguj** korzystanie z aplikacji.

## <a name="get-security-updates-for-our-product"></a>Pobierz aktualizacje zabezpieczeń naszych produktów
Firma Microsoft zachęca do przekazywania powiadomień o występujących incydentach zabezpieczeń poprzez wizytę [Witryna TechCenter poświęcona zabezpieczeniom](https://technet.microsoft.com/security/dd252948) i subskrybowanie doradczych alertów zabezpieczeń.

