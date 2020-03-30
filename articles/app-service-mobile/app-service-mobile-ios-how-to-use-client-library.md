---
title: Korzystanie z sdk iOS
description: Jak korzystać z sdk iOS dla aplikacji mobilnych platformy Azure
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 1bf8f8e198f6c4a4a0af308262cd830685698a80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249349"
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Jak korzystać z biblioteki klienta systemu iOS dla aplikacji mobilnych platformy Azure

[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Omówienie
W tym przewodniku dowiesz się, jak wykonywać typowe scenariusze przy użyciu najnowszego [sdk azure mobile apps dla systemu iOS.][1] Jeśli jesteś nowym użytkownikiem usługi Azure Mobile Apps, najpierw ukończ [usługę Azure Mobile Apps Quick Start,] aby utworzyć zaplecze, utworzyć tabelę i pobrać wstępnie utworzony projekt Xcode systemu iOS. W tym przewodniku koncentrujemy się na sdk systemu iOS po stronie klienta. Aby dowiedzieć się więcej o sdk po stronie serwera dla wewnętrznej bazy danych, zobacz SDK serwera HOWTO.

## <a name="reference-documentation"></a>Dokumentacja referencyjna

Dokumentacja referencyjna dla sdk klienta systemu iOS znajduje się tutaj: [Odwołanie klienta usługi Azure Mobile Apps dla systemu iOS][2].

## <a name="supported-platforms"></a>Obsługiwane platformy

Zestaw SDK systemu iOS obsługuje projekty Objective-C, projekty Swift 2.2 i Swift 2.3 dla systemu iOS w wersji 8.0 lub nowszej.

Uwierzytelnianie "przepływ serwera" używa WebView dla przedstawionego interfejsu użytkownika.  Jeśli urządzenie nie jest w stanie przedstawić interfejsu użytkownika webview, wymagana jest inna metoda uwierzytelniania, która znajduje się poza zakresem produktu.  
Ten SDK nie jest zatem odpowiedni dla urządzeń typu Watch lub podobnie ograniczonych.

## <a name="setup-and-prerequisites"></a><a name="Setup"></a>Ustawienia i wymagania wstępne

W tym przewodniku przyjęto założenie, że utworzono zaplecze z tabelą. W tym przewodniku przyjęto założenie, że tabela ma taki sam schemat jak tabele w tych samouczkach. W tym przewodniku przyjęto również `MicrosoftAzureMobile.framework` założenie, `MicrosoftAzureMobile/MicrosoftAzureMobile.h`że w kodzie odwołujesz się i importujesz .

## <a name="how-to-create-client"></a><a name="create-client"></a>Jak: Tworzenie klienta

Aby uzyskać dostęp do zaplecza usługi Azure `MSClient`Mobile Apps w projekcie, utwórz plik . Zamień `AppUrl` go na adres URL aplikacji. Możesz wyjść `gatewayURLString` `applicationKey` i opróżnić. Jeśli skonfigurujesz bramę do `gatewayURLString` uwierzytelniania, wypełnij go adresem URL bramy.

**Cel C**:

```objc
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Szybki**:

```swift
let client = MSClient(applicationURLString: "AppUrl")
```

## <a name="how-to-create-table-reference"></a><a name="table-reference"></a>Jak: Tworzenie odwołania do tabeli

Aby uzyskać dostęp do danych lub je zaktualizować, utwórz odwołanie do tabeli zaplecza. Zastąp ciąg `TodoItem` nazwą tabeli

**Cel C**:

```objc
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Szybki**:

```swift
let table = client.tableWithName("TodoItem")
```

## <a name="how-to-query-data"></a><a name="querying"></a>Jak: Dane zapytania

Aby utworzyć kwerendę bazy `MSTable` danych, należy zbadać obiekt. Poniższa kwerenda pobiera `TodoItem` wszystkie elementy i rejestruje tekst każdego elementu.

**Cel C**:

```objc
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occurred
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Szybki**:

```swift
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="how-to-filter-returned-data"></a><a name="filtering"></a>Jak: Filtrowanie zwróconych danych

Aby filtrować wyniki, dostępnych jest wiele opcji.

Aby filtrować przy użyciu predykatu, użyj i `NSPredicate` `readWithPredicate`. Następujące filtry zwróciły dane, aby znaleźć tylko niekompletne elementy Todo.

**Cel C**:

```objc
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Szybki**:

```swift
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="how-to-use-msquery"></a><a name="query-object"></a>Jak: Korzystanie z MSQuery

Aby wykonać złożoną kwerendę (w tym `MSQuery` sortowanie i stronicowanie), utwórz obiekt bezpośrednio lub za pomocą predykatu:

**Cel C**:

```objc
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Szybki**:

```swift
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery`pozwala kontrolować kilka zachowań kwerend.

* Określanie kolejności wyników
* Ogranicz pola, które mają być zwracane
* Ogranicz liczbę rekordów do zwrócenia
* Określ całkowitą liczbę w odpowiedzi
* Określanie niestandardowych parametrów ciągu kwerendy w żądaniu
* Stosowanie dodatkowych funkcji

Wykonaj `MSQuery` kwerendę, `readWithCompletion` wywołując obiekt.

## <a name="how-to-sort-data-with-msquery"></a><a name="sorting"></a>Jak: Sortowanie danych za pomocą msquery

Aby posortować wyniki, przyjrzyjmy się przykładowi. Aby posortować według pola "tekst" rosnąco, a `MSQuery` następnie przez "complete" malejąco, wywołać tak:

**Cel C**:

```objc
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Szybki**:

```swift
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="how-to-limit-fields-and-expand-query-string-parameters-with-msquery"></a><a name="selecting"></a><a name="parameters"></a>Jak: Ogranicz pola i Rozwiń parametry ciągu zapytania za pomocą programu MSQuery

Aby ograniczyć pola do zwracania w kwerendzie, należy określić nazwy pól we właściwości **selectFields.** W tym przykładzie zwraca tylko tekst i pola ukończone:

**Cel C**:

```objc
query.selectFields = @[@"text", @"complete"];
```

**Szybki**:

```swift
query.selectFields = ["text", "complete"]
```

Aby uwzględnić dodatkowe parametry ciągu zapytania w żądaniu serwera (na przykład, ponieważ `query.parameters` używa ich niestandardowy skrypt po stronie serwera), wypełnij tak:

**Cel C**:

```objc
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Szybki**:

```swift
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="how-to-configure-page-size"></a><a name="paging"></a>Jak: Konfigurowanie rozmiaru strony

W usłudze Azure Mobile Apps rozmiar strony kontroluje liczbę rekordów, które są pobierane w czasie z tabel wewnętrznej bazy danych. Wywołanie `pull` danych następnie partii w górę danych, na podstawie tego rozmiaru strony, dopóki nie ma więcej rekordów do ściągnięcia.

Jest możliwe, aby skonfigurować rozmiar strony za pomocą **MSPullSettings** jak pokazano poniżej. Domyślny rozmiar strony to 50, a poniższy przykład zmienia go na 3.

Ze względu na wydajność można skonfigurować inny rozmiar strony. Jeśli masz dużą liczbę małych rekordów danych, duży rozmiar strony zmniejsza liczbę rund serwera.

To ustawienie steruje tylko rozmiar strony po stronie klienta. Jeśli klient poprosi o większy rozmiar strony niż obsługa wewnętrznej bazy danych aplikacji mobilnych, rozmiar strony jest ograniczony do maksymalnej wewnętrznej bazy danych jest skonfigurowany do obsługi.

To ustawienie jest również *liczbą* rekordów danych, a nie *rozmiarem bajtu*.

Jeśli zwiększysz rozmiar strony klienta, należy również zwiększyć rozmiar strony na serwerze. Aby uzyskać instrukcje, aby to zrobić, zobacz ["Jak: Dopasowywanie rozmiaru stronicowania tabeli".](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

**Cel C**:

```objc
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                }
                           }];
```

**Szybki**:

```swift
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    }
}
```

## <a name="how-to-insert-data"></a><a name="inserting"></a>Jak: Wstawianie danych

Aby wstawić nowy wiersz `NSDictionary` tabeli, `table insert`utwórz i wywołaj program . Jeśli [schemat dynamiczny] jest włączony, mobilna wywężona wiórka `NSDictionary`usługi Azure App Service automatycznie generuje nowe kolumny na podstawie pliku .

Jeśli `id` nie zostanie podany, wewnętrznej bazy danych automatycznie generuje nowy unikatowy identyfikator. Podaj `id` własne adresy e-mail, nazwy użytkowników lub własne wartości niestandardowe jako identyfikator. Podanie własnego identyfikatora może ułatwić sprzężenia i logikę bazy danych zorientowaną na biznes.

Zawiera `result` nowy element, który został wstawiony. W zależności od logiki serwera może mieć dodatkowe lub zmodyfikowane dane w porównaniu do tego, co zostało przekazane do serwera.

**Cel C**:

```objc
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Szybki**:

```swift
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

## <a name="how-to-modify-data"></a><a name="modifying"></a>Jak: Modyfikowanie danych

Aby zaktualizować istniejący wiersz, zmodyfikuj element i zadzwoń: `update`

**Cel C**:

```objc
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Szybki**:

```swift
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

Alternatywnie podaj identyfikator wiersza i zaktualizowane pole:

**Cel C**:

```objc
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Szybki**:

```swift
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

Co najmniej `id` atrybut musi być ustawiony podczas dokonywania aktualizacji.

## <a name="how-to-delete-data"></a><a name="deleting"></a>Jak: Usuń dane

Aby usunąć element, `delete` wywołaj z elementem:

**Cel C**:

```objc
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Szybki**:

```swift
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Alternatywnie usuń, podając identyfikator wiersza:

**Cel C**:

```objc
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Szybki**:

```swift
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Co najmniej `id` atrybut musi być ustawiony podczas usuwania.

## <a name="how-to-call-custom-api"></a><a name="customapi"></a>Jak: Wywołanie niestandardowego interfejsu API

Za pomocą niestandardowego interfejsu API można udostępnić wszystkie funkcje wewnętrznej bazy danych. Nie trzeba mapować do operacji tabeli. Nie tylko zyskujesz większą kontrolę nad wiadomościami, możesz nawet odczytywać / ustawiać nagłówki i zmieniać format treści odpowiedzi.

Aby wywołać niestandardowy `MSClient.invokeAPI`interfejs API, zadzwoń do programu . Zawartość żądania i odpowiedzi są traktowane jako JSON. Aby użyć innych typów [nośników, `invokeAPI`użyj innego przeciążenia programu ][5].  Aby wysłać `GET` żądanie zamiast `POST` żądania, `HTTPMethod` ustaw `"GET"` parametr `body` `nil` na i parametr na (ponieważ żądania GET nie mają treści wiadomości). Jeśli niestandardowy interfejs API obsługuje `HTTPMethod` inne zlecenia HTTP, zmień odpowiednio.

**Cel C**:

```objc
[self.client invokeAPI:@"sendEmail"
                  body:@{ @"contents": @"Hello world!" }
            HTTPMethod:@"POST"
            parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
               headers:nil
            completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                if(error) {
                    NSLog(@"ERROR %@", error);
                } else {
                    // Do something with result
                }
            }];
```

**Szybki**:

```swift
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

## <a name="how-to-register-push-templates-to-send-cross-platform-notifications"></a><a name="templates"></a>Jak: Rejestrowanie szablonów wypychanych w celu wysyłania powiadomień między platformami

Aby zarejestrować szablony, przekaż szablony za pomocą **client.push registerDeviceToken** metody w aplikacji klienckiej.

**Cel C**:

```objc
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Szybki**:

```swift
client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
    if let err = error {
        print("ERROR ", err)
    }
})
```

Szablony są typu NSDictionary i mogą zawierać wiele szablonów w następującym formacie:

**Cel C**:

```objc
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Szybki**:

```swift
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Wszystkie tagi są usuwane z żądania zabezpieczeń.  Aby dodać znaczniki do instalacji lub szablonów w instalacjach, zobacz [Praca z zestawem SDK serwera wewnętrznej bazy danych .NET dla aplikacji Azure Mobile Apps][4].  Aby wysyłać powiadomienia przy użyciu tych zarejestrowanych szablonów, pracuj z [interfejsami API Centrum powiadomień][3].

## <a name="how-to-handle-errors"></a><a name="errors"></a>Jak: Obsługa błędów

Po wywołaniu mobilnej wewnętrznej bazy danych usługi `NSError` Azure App Service blok ukończenia zawiera parametr. W przypadku wystąpienia błędu ten parametr jest nie-zero. W kodzie należy sprawdzić ten parametr i obsługiwać błąd w razie potrzeby, jak pokazano w poprzednich fragmentów kodu.

Plik [`<WindowsAzureMobileServices/MSError.h>`][6] definiuje stałe `MSErrorResponseKey`, `MSErrorRequestKey`i `MSErrorServerItemKey`. Aby uzyskać więcej danych związanych z błędem:

**Cel C**:

```objc
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Szybki**:

```swift
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Ponadto plik definiuje stałe dla każdego kodu błędu:

**Cel C**:

```objc
if (error.code == MSErrorPreconditionFailed) {
```

**Szybki**:

```swift
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="how-to-authenticate-users-with-the-active-directory-authentication-library"></a><a name="adal"></a>Jak: Uwierzytelnianie użytkowników za pomocą biblioteki uwierzytelniania usługi Active Directory

Biblioteka uwierzytelniania usługi Active Directory (ADAL) służy do logowania użytkowników do aplikacji przy użyciu usługi Azure Active Directory. Uwierzytelnianie przepływu klienta przy użyciu sdk dostawcy `loginWithProvider:completion:` tożsamości jest lepsze niż przy użyciu metody.  Uwierzytelnianie przepływu klienta zapewnia bardziej natywne działanie środowiska użytkownika i umożliwia dodatkowe dostosowanie.

1. Skonfiguruj zaplecze aplikacji mobilnej dla logowania usługi AAD, wykonując samouczek [Jak skonfigurować usługę App Service dla logowania usługi Active Directory.][7] Upewnij się, aby wykonać opcjonalny krok rejestrowania natywnej aplikacji klienckiej. W przypadku systemu iOS zaleca się, aby `<app-scheme>://<bundle-id>`identyfikator URI przekierowania był formularzem . Aby uzyskać więcej informacji, zobacz [przewodnik Szybki start systemu IOS ADAL][8].
2. Zainstaluj ADAL za pomocą Cocoapods. Edytuj swój podfile, aby uwzględnić następującą definicję, zastępując **YOUR-PROJECT** nazwą projektu Xcode:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   i pod:

        pod 'ADALiOS'

3. Korzystając z terminalu, uruchom `pod install` z katalogu zawierającego projekt, a następnie otwórz wygenerowany obszar roboczy Xcode (nie projekt).
4. Dodaj następujący kod do aplikacji, zgodnie z używanym językiem. W każdym z nich wykonaj następujące zamienniki:

   * Zamień **INSERT-AUTHORITY-HERE** na nazwę dzierżawy, w której zainicjowano aprowizacji aplikacji. Format powinien https://login.microsoftonline.com/contoso.onmicrosoft.combyć . Tę wartość można skopiować z karty Domena w usłudze Azure Active Directory w [witrynie Azure portal].
   * Zamień **INSERT-RESOURCE-ID-HERE** na identyfikator klienta dla wewnętrznej bazy danych aplikacji mobilnej. Identyfikator klienta można uzyskać na karcie **Zaawansowane** w obszarze **Ustawienia usługi Azure Active Directory** w portalu.
   * Zastąp **INSERT-CLIENT-ID-HERE** identyfikatorem klienta skopiowanym z natywnej aplikacji klienckiej.
   * Zastąp **INSERT-REDIRECT-URI-HERE** punktem końcowym *witryny /.auth/login/done,* używając schematu HTTPS. Ta wartość powinna *https://contoso.azurewebsites.net/.auth/login/done*być podobna do .

**Cel C**:

```objc
#import <ADALiOS/ADAuthenticationContext.h>
#import <ADALiOS/ADAuthenticationSettings.h>
// ...
- (void) authenticate:(UIViewController*) parent
            completion:(void (^) (MSUser*, NSError*))completionBlock;
{
    NSString *authority = @"INSERT-AUTHORITY-HERE";
    NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
    NSString *clientId = @"INSERT-CLIENT-ID-HERE";
    NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
    ADAuthenticationError *error;
    ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
    authContext.parentController = parent;
    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:resourceId
                                    clientId:clientId
                                redirectUri:redirectUri
                            completionBlock:^(ADAuthenticationResult *result) {
                                if (result.status != AD_SUCCEEDED)
                                {
                                    completionBlock(nil, result.error);;
                                }
                                else
                                {
                                    NSDictionary *payload = @{
                                                            @"access_token" : result.tokenCacheStoreItem.accessToken
                                                            };
                                    [client loginWithProvider:@"aad" token:payload completion:completionBlock];
                                }
                            }];
}
```

**Szybki**:

```swift
// add the following imports to your bridging header:
//        #import <ADALiOS/ADAuthenticationContext.h>
//        #import <ADALiOS/ADAuthenticationSettings.h>

func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
    let authority = "INSERT-AUTHORITY-HERE"
    let resourceId = "INSERT-RESOURCE-ID-HERE"
    let clientId = "INSERT-CLIENT-ID-HERE"
    let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
    var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
    let authContext = ADAuthenticationContext(authority: authority, error: error)
    authContext.parentController = parent
    ADAuthenticationSettings.sharedInstance().enableFullScreen = true
    authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
            if result.status != AD_SUCCEEDED {
                completion(nil, result.error)
            }
            else {
                let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
                client.loginWithProvider("aad", token: payload, completion: completion)
            }
        }
}
```

## <a name="how-to-authenticate-users-with-the-facebook-sdk-for-ios"></a><a name="facebook-sdk"></a>Jak: Uwierzytelniać użytkowników za pomocą sdk Facebook dla systemu iOS

Możesz użyć SDK Facebook dla iOS, aby zalogować użytkowników do aplikacji za pomocą Facebooka.  Za pomocą uwierzytelniania przepływu klienta `loginWithProvider:completion:` jest korzystne przy użyciu metody.  Uwierzytelnianie przepływu klienta zapewnia bardziej natywne działanie środowiska użytkownika i umożliwia dodatkowe dostosowanie.

1. Skonfiguruj zaplecze aplikacji mobilnej dla logowania się na Facebooku, wykonując samouczek [Jak skonfigurować usługę app service dla logowania do Facebooka.][9]
2. Zainstaluj pakiet SDK Facebooka dla systemu iOS, wykonując dokumentację [SDK Facebooka dla systemu iOS — wprowadzenie.][10] Zamiast tworzyć aplikację, możesz dodać platformę systemu iOS do istniejącej rejestracji.
3. Dokumentacja Facebooka zawiera kod Objective-C w pełnomocniku aplikacji. Jeśli używasz **swift,** możesz użyć następujących tłumaczeń dla AppDelegate.swift:

    ```swift
    // Add the following import to your bridging header:
    //        #import <FBSDKCoreKit/FBSDKCoreKit.h>

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
        FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
        // Add any custom logic here.
        return true
    }

    func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
        let handled = FBSDKApplicationDelegate.sharedInstance().application(application, openURL: url, sourceApplication: sourceApplication, annotation: annotation)
        // Add any custom logic here.
        return handled
    }
    ```
4. Oprócz dodawania `FBSDKCoreKit.framework` do projektu, należy również `FBSDKLoginKit.framework` dodać odwołanie do w ten sam sposób.
5. Dodaj następujący kod do aplikacji, zgodnie z używanym językiem.

    **Cel C**:

    ```objc
    #import <FBSDKLoginKit/FBSDKLoginKit.h>
    #import <FBSDKCoreKit/FBSDKAccessToken.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
                completion:(void (^) (MSUser*, NSError*)) completionBlock;
    {
        FBSDKLoginManager *loginManager = [[FBSDKLoginManager alloc] init];
        [loginManager
            logInWithReadPermissions: @[@"public_profile"]
            fromViewController:parent
            handler:^(FBSDKLoginManagerLoginResult *result, NSError *error) {
                if (error) {
                    completionBlock(nil, error);
                } else if (result.isCancelled) {
                    completionBlock(nil, error);
                } else {
                    NSDictionary *payload = @{
                                            @"access_token":result.token.tokenString
                                            };
                    [client loginWithProvider:@"facebook" token:payload completion:completionBlock];
                }
            }];
    }
    ```

    **Szybki**:

    ```swift
    // Add the following imports to your bridging header:
    //        #import <FBSDKLoginKit/FBSDKLoginKit.h>
    //        #import <FBSDKCoreKit/FBSDKAccessToken.h>

    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let loginManager = FBSDKLoginManager()
        loginManager.logInWithReadPermissions(["public_profile"], fromViewController: parent) { (result, error) in
            if (error != nil) {
                completion(nil, error)
            }
            else if result.isCancelled {
                completion(nil, error)
            }
            else {
                let payload: [String: String] = ["access_token": result.token.tokenString]
                client.loginWithProvider("facebook", token: payload, completion: completion)
            }
        }
    }
    ```

## <a name="how-to-authenticate-users-with-twitter-fabric-for-ios"></a><a name="twitter-fabric"></a>Jak: Uwierzytelnij użytkowników za pomocą sieci szkieletowej Twittera dla systemu iOS

Za pomocą sieci szkieletowej dla systemu iOS można logować użytkowników do aplikacji za pomocą Twittera. Uwierzytelnianie przepływu klienta jest `loginWithProvider:completion:` lepsze niż przy użyciu metody, ponieważ zapewnia bardziej natywne działanie środowiska użytkownika i umożliwia dodatkowe dostosowanie.

1. Skonfiguruj zaplecze aplikacji mobilnej dla logowania się do twittera, wykonując samouczek [Jak skonfigurować usługę app service dla logowania do twittera.](../app-service/configure-authentication-provider-twitter.md)
2. Dodaj sieci szkieletowej do projektu, wykonując [dokumentację sieci szkieletowej dla systemu iOS — wprowadzenie] i konfiguruj TwitterKit.

   > [!NOTE]
   > Domyślnie fabric tworzy aplikację Twitter dla Ciebie. Można uniknąć tworzenia aplikacji, rejestrując klucz konsumenta i klucz tajny konsumenta utworzony wcześniej przy użyciu następujących fragmentów kodu.    Alternatywnie można zastąpić wartości klucza konsumenta i klucza tajnego konsumenta, które są podane do usługi App Service wartościami widocznymi na [pulpicie nawigacyjnym sieci szkieletowej]. Jeśli wybierzesz tę opcję, upewnij się, że adres URL `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`wywołania zwrotnego jest wartością zastępczą, taką jak .

    Jeśli zdecydujesz się użyć utworzonych wcześniej wpisów tajnych, dodaj następujący kod do pełnomocnika aplikacji:

    **Cel C**:

    ```objc
    #import <Fabric/Fabric.h>
    #import <TwitterKit/TwitterKit.h>
    // ...
    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
        [[Twitter sharedInstance] startWithConsumerKey:@"your_key" consumerSecret:@"your_secret"];
        [Fabric with:@[[Twitter class]]];
        // Add any custom logic here.
        return YES;
    }
    ```

    **Szybki**:

    ```swift
    import Fabric
    import TwitterKit
    // ...
    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
        Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
        Fabric.with([Twitter.self])
        // Add any custom logic here.
        return true
    }
    ```

3. Dodaj następujący kod do aplikacji, zgodnie z używanym językiem.

    **Cel C**:

    ```objc
    #import <TwitterKit/TwitterKit.h>
    // ...
    - (void)authenticate:(UIViewController*)parent completion:(void (^) (MSUser*, NSError*))completionBlock
    {
        [[Twitter sharedInstance] logInWithCompletion:^(TWTRSession *session, NSError *error) {
            if (session) {
                NSDictionary *payload = @{
                                            @"access_token":session.authToken,
                                            @"access_token_secret":session.authTokenSecret
                                        };
                [client loginWithProvider:@"twitter" token:payload completion:completionBlock];
            } else {
                completionBlock(nil, error);
            }
        }];
    }
    ```

    **Szybki**:

    ```swift
    import TwitterKit
    // ...
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let client = self.table!.client
        Twitter.sharedInstance().logInWithCompletion { session, error in
            if (session != nil) {
                let payload: [String: String] = ["access_token": session!.authToken, "access_token_secret": session!.authTokenSecret]
                client.loginWithProvider("twitter", token: payload, completion: completion)
            } else {
                completion(nil, error)
            }
        }
    }
    ```

## <a name="how-to-authenticate-users-with-the-google-sign-in-sdk-for-ios"></a><a name="google-sdk"></a>Jak: uwierzytelniaj użytkowników za pomocą sdk logowania Google dla systemu iOS

Możesz użyć sdk logowania google dla systemu iOS, aby zalogować użytkowników do aplikacji za pomocą konta Google.  Google ogłosiło niedawno zmiany w swoich zasadach bezpieczeństwa OAuth.  Te zmiany zasad będą wymagały użycia sdk Google w przyszłości.

1. Skonfiguruj zaplecze aplikacji mobilnej dla logowania google, wykonując samouczek [Jak skonfigurować usługę app service dla logowania Google.](../app-service/configure-authentication-provider-google.md)
2. Zainstaluj pakiet SDK Google dla systemu iOS, postępując zgodnie z [google sign-in dla systemu iOS — zacznij integrować](https://developers.google.com/identity/sign-in/ios/start-integrating) dokumentację. Można pominąć sekcję "Uwierzytelnij za pomocą serwera wewnętrznej bazy danych".
3. Dodaj następujące do metody `signIn:didSignInForUser:withError:` pełnomocnika, zgodnie z używanym językiem.

    **Cel C**:
    ```objc
    NSDictionary *payload = @{
                                @"id_token":user.authentication.idToken,
                                @"authorization_code":user.serverAuthCode
                                };

    [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
        // ...
    }];
    ```

    **Szybki**:

    ```swift
    let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
    client.loginWithProvider("google", token: payload) { (user, error) in
        // ...
    }
    ```

4. Upewnij się również, że `application:didFinishLaunchingWithOptions:` dodajesz następujące elementy w pełnomocniku aplikacji, zastępując "SERVER_CLIENT_ID" tym samym identyfikatorem, który został użyty do skonfigurowania usługi App Service w kroku 1.

    **Cel C**:

    ```objc
    [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
    ```

     **Szybki**:

    ```swift
    GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"
    ```

5. Dodaj następujący kod do aplikacji w UIViewController, `GIDSignInUIDelegate` który implementuje protokół, zgodnie z używanym językiem.  Użytkownik zostanie wylogowane przed ponownym zalogowaniem i chociaż nie trzeba ponownie wprowadzać poświadczeń, zostanie wyświetlone okno dialogowe zgody.  Wywołaj tę metodę tylko wtedy, gdy token sesji wygasł.

   **Cel C**:

    ```objc
    #import <Google/SignIn.h>
    // ...
    - (void)authenticate
    {
            [GIDSignIn sharedInstance].uiDelegate = self;
            [[GIDSignIn sharedInstance] signOut];
            [[GIDSignIn sharedInstance] signIn];
    }
    ```

   **Szybki**:

    ```swift
    // ...
    func authenticate() {
        GIDSignIn.sharedInstance().uiDelegate = self
        GIDSignIn.sharedInstance().signOut()
        GIDSignIn.sharedInstance().signIn()
    }
    ```

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Szybki start aplikacji mobilnych platformy Azure]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode
[Portal Azure]: https://portal.azure.com/
[Handling Expired Tokens]: https://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: https://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: https://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Schemat dynamiczny]: https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/dynamic-schema
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: https://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: https://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: /cli/azure/get-started-with-az-cli2
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[Pulpit nawigacyjny sieci szkieletowej]: https://www.fabric.io/home
[Tkanina dla systemu iOS — wprowadzenie]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: https://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: https://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: ../app-service/configure-authentication-provider-aad.md
[8]:../active-directory/develop/quickstart-v2-ios.md
[9]: ../app-service/configure-authentication-provider-facebook.md
[10]: https://developers.facebook.com/docs/ios/getting-started
