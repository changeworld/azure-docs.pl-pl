---
title: Sposób użycia zestawu iOS SDK usługi Azure Mobile Apps
description: Sposób użycia zestawu iOS SDK usługi Azure Mobile Apps
services: app-service\mobile
documentationcenter: ios
author: conceptdev
editor: ''
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: b6f93cc3c35ab18ecd50ccd6b3090985497baabf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62122459"
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Sposób użycia biblioteki klienta usługi Azure Mobile Apps-iOS

[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Tego przewodnika dowiesz się, aby wykonać typowe scenariusze za pomocą najnowszej [usługi Azure Mobile Apps dla systemu iOS SDK][1]. Jeśli jesteś nowym użytkownikiem usługi Azure Mobile Apps, najpierw wykonaj [Azure mobilnych: aplikacje Szybki Start] Aby utworzyć zaplecze, utworzyć tabelę i pobrać projekt Xcode wstępnie skompilowanych dla systemu iOS. W tym przewodniku skupimy się na zestaw SDK systemu iOS po stronie klienta. Aby dowiedzieć się więcej na temat zestawu SDK po stronie serwera wewnętrznej bazy danych, zobacz HOWTOs zestawu SDK serwera.

## <a name="reference-documentation"></a>Dokumentacja referencyjna

Dokumentacja referencyjna dla klientów z systemem iOS SDK znajduje się tutaj: [Usługa Azure Mobile Apps dla systemu iOS odwołania do klienta][2].

## <a name="supported-platforms"></a>Obsługiwane platformy

Zestaw SDK systemu iOS obsługuje projekty języka Objective-C, Swift 2.2 projektów i projektów języka Swift 2.3 dla systemu iOS w wersji 8.0 lub nowszej.

Uwierzytelnianie "serwera przepływ" używa widoku internetowego prezentowane interfejsu użytkownika.  Jeśli urządzenie nie jest w stanie prezentować interfejs użytkownika widoku sieci Web, innej metody uwierzytelniania jest wymagane jest poza zakresem produktu.  
Ten zestaw SDK nie nadaje się zatem Obejrzyj type lub podobnie urządzeń z ograniczeniami.

## <a name="Setup"></a>Instalacji i wymagania wstępne

W tym przewodniku założono, że utworzono zaplecza za pomocą tabeli. W tym przewodniku założono, że tabela ma ten sam schemat, jak tabele w tych samouczkach. W tym przewodniku założono, że w kodzie, można się odwołać `MicrosoftAzureMobile.framework` i zaimportuj `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

## <a name="create-client"></a>Jak: Tworzenie klienta

Aby uzyskać dostęp do zaplecza usługi Azure Mobile Apps w swoim projekcie, należy utworzyć `MSClient`. Zastąp `AppUrl` przy użyciu adresu URL aplikacji. Można pozostawić `gatewayURLString` i `applicationKey` puste. Po skonfigurowaniu bramy uwierzytelniania wypełnić `gatewayURLString` za pomocą adresu URL bramy.

**Objective-C**:

```objc
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Kod SWIFT**:

```swift
let client = MSClient(applicationURLString: "AppUrl")
```

## <a name="table-reference"></a>Jak: Tworzenie odwołania do tabeli

Aby uzyskać dostęp do danych lub je zaktualizować, utwórz odwołanie do tabeli zaplecza. Zastąp ciąg `TodoItem` nazwą tabeli

**Objective-C**:

```objc
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Kod SWIFT**:

```swift
let table = client.tableWithName("TodoItem")
```

## <a name="querying"></a>Jak: Zapytania o dane

Aby utworzyć zapytanie bazy danych, zapytań `MSTable` obiektu. Następujące zapytanie pobiera wszystkie elementy w `TodoItem` i rejestruje tekst każdego elementu.

**Objective-C**:

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

**Kod SWIFT**:

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

## <a name="filtering"></a>Jak: Filtr nie zwrócił danych

Aby filtrować wyniki, istnieje wiele dostępnych opcji.

Aby filtrować, używając predykatu, użyj `NSPredicate` i `readWithPredicate`. Następujące filtry zwrócone dane w celu znalezienia tylko niezakończonych elementów zadań do wykonania.

**Objective-C**:

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

**Kod SWIFT**:

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

## <a name="query-object"></a>Jak: Use MSQuery

Aby wykonać zapytanie złożone (w tym sortowanie i stronicowanie), należy utworzyć `MSQuery` obiektu bezpośrednio lub za pomocą predykat:

**Objective-C**:

```objc
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Kod SWIFT**:

```swift
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery` Umożliwia sterowanie różne zachowania zapytania.

* Określ kolejność wyników
* Limit, który pól do zwrócenia
* Ogranicz liczbę rekordów do zwrócenia
* Określ liczbę całkowitą w odpowiedzi
* Określ parametry ciągu zapytania niestandardowe w żądaniu
* Zastosowanie dodatkowych funkcji

Wykonaj `MSQuery` zapytania, wywołując `readWithCompletion` obiektu.

## <a name="sorting"></a>Jak: Sortowanie danych za pomocą MSQuery

Aby posortować wyniki, Przyjrzyjmy się przykładowi. Aby posortować według pola "text" w kolejności rosnącej, a następnie według malejących "Pełna", należy wywołać `MSQuery` w następujący sposób:

**Objective-C**:

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

**Kod SWIFT**:

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

## <a name="selecting"></a><a name="parameters"></a>Jak: Ograniczenia pola, a następnie rozwiń węzeł parametry ciągu zapytania za pomocą MSQuery

Aby ograniczyć liczbę pól do zwrócenia w zapytaniu, określ nazwy pól w **selectFields** właściwości. W tym przykładzie zwraca tylko tekst i ukończone pola:

**Objective-C**:

```objc
query.selectFields = @[@"text", @"complete"];
```

**Kod SWIFT**:

```swift
query.selectFields = ["text", "complete"]
```

Dołącz dodatkowe parametry parametry zapytania w żądaniu serwera (na przykład, ponieważ używa niestandardowego skryptu po stronie serwera, ich), wypełnij `query.parameters` w następujący sposób:

**Objective-C**:

```objc
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Kod SWIFT**:

```swift
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>Jak: Skonfiguruj rozmiar strony

Za pomocą usługi Azure Mobile Apps rozmiar strony określa liczbę rekordów, które są pobierane w czasie z tabelami w wewnętrznej bazie danych. Wywołanie `pull` następnie partii zapasowych danych, w oparciu o ten rozmiar strony, aż nie wystąpią żadne rekordy do ściągania danych.

Istnieje możliwość skonfigurować rozmiar strony przy użyciu **MSPullSettings** jak pokazano poniżej. Domyślny rozmiar strony jest 50, a w poniższym przykładzie zmienia ją na 3.

Można skonfigurować inny rozmiar strony ze względu na wydajność. Jeśli masz dużą liczbę rekordów małych danych, rozmiaru strony o wysokiej zmniejsza liczbę rund do serwera.

To ustawienie kontroluje tylko rozmiaru strony po stronie klienta. Jeśli klient żąda większy rozmiar strony, niż obsługuje zaplecza funkcji Mobile Apps, rozmiar strony jest ograniczone do maksymalnie, wewnętrznej bazy danych jest skonfigurowana do obsługi.

To ustawienie jest również *numer* rekordy danych nie *rozmiar w bajtach*.

Zwiększenie rozmiaru strony klienta, należy również zwiększyć rozmiar strony na serwerze. Zobacz ["jak: Dostosuj rozmiar stronicowania w tabeli"](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) kroki opisane w tym celu.

**Objective-C**:

```objc
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                }
                           }];
```

**Kod SWIFT**:

```swift
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    }
}
```

## <a name="inserting"></a>Jak: Wstawianie danych

Aby wstawić nowy wiersz w tabeli, należy utworzyć `NSDictionary` i wywoływać `table insert`. Jeśli [schemat dynamiczny] jest włączona, zaplecza aplikacji mobilnych w usłudze Azure App Service automatycznie generuje nowych kolumn na podstawie `NSDictionary`.

Jeśli `id` nie zostanie podana, wewnętrznej bazy danych automatycznie generuje nowy unikatowy identyfikator. Podaj własny `id` Aby użyć adresu e-mail adresów, nazwy użytkowników, lub własne wartości jako identyfikatora. Dostarczanie własnego Identyfikatora może ułatwić sprzężenia i logiki firmowe bazy danych.

`result` Zawiera nowy element, który został wstawiony. W zależności od logika serwera może mieć dodatkowe lub zmodyfikowanych danych co został przekazany do serwera.

**Objective-C**:

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

**Kod SWIFT**:

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

## <a name="modifying"></a>Jak: Modyfikowanie danych

Aby zaktualizować istniejący wiersz, zmodyfikuj element i wywołania `update`:

**Objective-C**:

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

**Kod SWIFT**:

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

Alternatywnie można podać identyfikator wiersza i zaktualizowane pole:

**Objective-C**:

```objc
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Kod SWIFT**:

```swift
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

Co najmniej `id` podczas wprowadzania aktualizacji, należy ustawić dla atrybutu.

## <a name="deleting"></a>Jak: Usuwanie danych

Aby usunąć element, należy wywołać `delete` z elementem:

**Objective-C**:

```objc
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Kod SWIFT**:

```swift
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Alternatywnie usunąć, podając identyfikator wiersza:

**Objective-C**:

```objc
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Kod SWIFT**:

```swift
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Co najmniej `id` podczas wprowadzania usuwa, należy ustawić dla atrybutu.

## <a name="customapi"></a>Jak: Wywoływanie niestandardowego interfejsu API

Za pomocą niestandardowego interfejsu API może narazić żadnych funkcji wewnętrznej bazy danych. Nie ma mapowania na operacji tabeli. Nie tylko możesz uzyskać większą kontrolę nad wiadomości, można nawet odczytu/ustawiania nagłówków i zmień format treści odpowiedzi. Aby dowiedzieć się, jak utworzyć niestandardowy interfejs API zaplecza, przeczytaj [niestandardowych interfejsów API](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)

Aby wywołać niestandardowy interfejs API, należy wywołać `MSClient.invokeAPI`. Żądanie i odpowiedź zawartości są traktowane jako dane JSON. Aby korzystać z innych typów nośników [Użyj innego przeciążenia `invokeAPI` ] [ 5].  Aby `GET` żądania zamiast `POST` żądania parametru zestawu `HTTPMethod` do `"GET"` i parametru `body` do `nil` (ponieważ żądania GET nie ma treści wiadomości.) Jeśli niestandardowy interfejs API obsługuje inne zleceń HTTP, zmień `HTTPMethod` odpowiednio.

**Objective-C**:

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

**Kod SWIFT**:

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

## <a name="templates"></a>Jak: Rejestrowanie szablonów wypychania do wysyłania powiadomień dla wielu platform

Aby zarejestrować szablony, przekazywać szablonów za pomocą usługi **client.push registerDeviceToken** metody w aplikacji klienckiej.

**Objective-C**:

```objc
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Kod SWIFT**:

```swift
client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
    if let err = error {
        print("ERROR ", err)
    }
})
```

Szablony są typu NSDictionary i może zawierać wiele szablonów w następującym formacie:

**Objective-C**:

```objc
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Kod SWIFT**:

```swift
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Wszystkie tagi są usuwane z żądania dotyczące zabezpieczeń.  Aby dodać tagi do instalacji lub szablonów w ramach instalacji, zobacz [pracy z zestawem SDK serwera zaplecza platformy .NET dla usługi Azure Mobile Apps][4].  Aby wysłać powiadomienia za pomocą tych szablonów zarejestrowanych, współpracować z [Notification Hubs API][3].

## <a name="errors"></a>Jak: Obsługa błędów

Podczas wywoływania zaplecza aplikacji mobilnych w usłudze Azure App Service zawiera blok uzupełniania `NSError` parametru. Gdy wystąpi błąd, ten parametr jest inne niż zero. W kodzie należy sprawdzić tego parametru i obsługi błędów, zgodnie z potrzebami, jak pokazano w poprzednim fragmenty kodu.

Plik [ `<WindowsAzureMobileServices/MSError.h>` ] [ 6] definiuje stałe `MSErrorResponseKey`, `MSErrorRequestKey`, i `MSErrorServerItemKey`. Aby wyświetlić więcej danych związane z błędem:

**Objective-C**:

```objc
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Kod SWIFT**:

```swift
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Ponadto plik definiuje stałe każdy kod błędu:

**Objective-C**:

```objc
if (error.code == MSErrorPreconditionFailed) {
```

**Kod SWIFT**:

```swift
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Jak: Uwierzytelnianie użytkowników za pomocą biblioteki uwierzytelniania usługi Active Directory

Active Directory Authentication Library (ADAL) służy do logowania się użytkowników do aplikacji za pomocą usługi Azure Active Directory. Uwierzytelnianie przepływu klienta przy użyciu dostawcy tożsamości zestawu SDK zalecane jest stosowanie `loginWithProvider:completion:` metody.  Uwierzytelnianie przepływu klienta zapewnia bardziej natywnego działania środowiska użytkownika i umożliwia dodatkowych dostosowań.

1. Skonfiguruj zaplecza aplikacji mobilnej do logowania w usłudze AAD, wykonując [sposób konfigurowania usługi App Service dla nazwy logowania usługi Active Directory] [ 7] samouczka. Upewnij się ukończyć opcjonalny krok rejestrowanie natywnej aplikacji klienckiej. Dla systemów iOS, zaleca się przekierowania URI ma postać `<app-scheme>://<bundle-id>`. Aby uzyskać więcej informacji, zobacz [Szybki Start z biblioteki ADAL dla systemu iOS][8].
2. Zainstaluj biblioteki ADAL przy użyciu Menedżera Cocoapods. Edytowanie pliku Podfile, aby uwzględnić następującą definicję zastępowanie **YOUR PROJECT** o nazwie projektu Xcode:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   i zasobnika:

        pod 'ADALiOS'

3. Za pomocą terminalu Uruchom `pod install` z katalogu zawierającego projekt, a następnie otwórz wygenerowany obszaru roboczego Xcode (nie projekt).
4. Dodaj następujący kod do aplikacji, zgodnie z językiem, którego używasz. W każdej należy wprowadzić te zmiany:

   * Zastąp **INSERT-urzędu-tutaj** nazwą dzierżawy, w której aprowizowano aplikacji. Powinna być w formacie https://login.microsoftonline.com/contoso.onmicrosoft.com. Ta wartość może zostać skopiowany z karcie domeny w usłudze Azure Active Directory w [Azure Portal].
   * Zastąp **INSERT zasobów — identyfikator — tutaj** z Identyfikatorem klienta dla zaplecza aplikacji mobilnej. Można uzyskać identyfikator klienta z **zaawansowane** karcie **ustawienia usługi Azure Active Directory** w portalu.
   * Zastąp **INSERT klienta-ID — tutaj** z Identyfikatorem klienta został skopiowany z aplikacja kliencka macierzystego.
   * Zastąp **INSERT PRZEKIEROWANIA-URI-tutaj** z witryny */.auth/login/done* punktu końcowego, przy użyciu schematu HTTPS. Ta wartość powinna być podobna do *https://contoso.azurewebsites.net/.auth/login/done* .

**Objective-C**:

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

**Kod SWIFT**:

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

## <a name="facebook-sdk"></a>Jak: Uwierzytelnianie użytkowników przy użyciu zestawu SDK usługi Facebook dla systemu iOS

Zestaw SDK usługi Facebook dla systemu iOS można użyć do logowania się użytkowników do aplikacji za pomocą usługi Facebook.  Przy użyciu uwierzytelniania przepływu klientów zalecane jest stosowanie `loginWithProvider:completion:` metody.  Uwierzytelnianie przepływu klienta zapewnia bardziej natywnego działania środowiska użytkownika i umożliwia dla dodatkowych dostosowań.

1. Skonfiguruj zaplecza aplikacji mobilnej do logowania w usłudze Facebook zgodnie z poniższymi [sposobu konfigurowania usługi App Service dla logowania do usługi Facebook] [ 9] samouczka.
2. Zainstaluj zestaw SDK usługi Facebook dla systemu iOS, wykonując [Facebook SDK dla systemu iOS — wprowadzenie] [ 10] dokumentacji. Zamiast tworzenia aplikacji, możesz dodać do istniejącej rejestracji platformy iOS.
3. Dokumentacja w serwisie Facebook zawiera jakiś kod języka Objective-C w delegacie aplikacji. Jeśli używasz **Swift**, można użyć następujących tłumaczeń dla AppDelegate.swift:

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
4. Oprócz dodawania `FBSDKCoreKit.framework` do projektu, a także dodać odwołanie do `FBSDKLoginKit.framework` w taki sam sposób.
5. Dodaj następujący kod do aplikacji, zgodnie z językiem, którego używasz.

    **Objective-C**:

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

    **Kod SWIFT**:

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

## <a name="twitter-fabric"></a>Jak: Uwierzytelnianie użytkowników za pomocą usługi Twitter Service Fabric dla systemu iOS

Service Fabric dla systemu iOS można użyć do logowania się użytkowników do aplikacji za pomocą usługi Twitter. Uwierzytelnianie przepływu klienta zalecane jest stosowanie `loginWithProvider:completion:` metody, ponieważ zapewnia bardziej natywnego działania środowiska użytkownika i umożliwia dodatkowych dostosowań.

1. Skonfiguruj zaplecza aplikacji mobilnej do logowania w usłudze Twitter, wykonując [sposobu konfigurowania usługi App Service dla logowania usługi Twitter](../app-service/configure-authentication-provider-twitter.md) samouczka.
2. Dodaj do projektu sieci szkieletowej, wykonując [Service Fabric dla systemu iOS — wprowadzenie] dokumentacji oraz definiowanie TwitterKit.

   > [!NOTE]
   > Domyślnie Service Fabric tworzy aplikację usługi Twitter. Możesz uniknąć tworzenia aplikacji przez rejestrowanie klucz klienta i klucz tajny klienta, które zostały utworzone wcześniej za pomocą poniższej wstawki kodu programu.    Alternatywnie możesz zastąpić wartości klucz klienta i klucz tajny klienta, które zapewniają usłudze App Service przy użyciu wartości, zostanie wyświetlony w [pulpit nawigacyjny sieci szkieletowej]. Jeśli wybierzesz tę opcję, należy ustawić adres URL wywołania zwrotnego wartość symbolu zastępczego, takich jak `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.

    Jeśli zdecydujesz się używać kluczy tajnych, która została utworzona wcześniej, Dodaj następujący kod do delegata Twojej aplikacji:

    **Objective-C**:

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

    **Kod SWIFT**:

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

3. Dodaj następujący kod do aplikacji, zgodnie z językiem, którego używasz.

    **Objective-C**:

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

    **Kod SWIFT**:

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

## <a name="google-sdk"></a>Jak: Uwierzytelnianie użytkowników za pomocą Google Sign-In SDK dla systemu iOS

Google Sign-In SDK dla systemu iOS można użyć do logowania się użytkowników do aplikacji za pomocą konta Google.  Google zapowiedziała niedawno zmiany z ich zasadami zabezpieczeń OAuth.  Zmiany tych zasad, trzeba użyć zestawu Google SDK w przyszłości.

1. Skonfiguruj zaplecza aplikacji mobilnej dla logowania Google, zgodnie z poniższymi [sposobu konfigurowania usługi App Service dla logowania usługi Google](../app-service/configure-authentication-provider-google.md) samouczka.
2. Zainstaluj zestaw SDK Google dla systemu iOS, wykonując [Sign In Google dla systemu iOS — Rozpocznij integrowanie](https://developers.google.com/identity/sign-in/ios/start-integrating) dokumentacji. Możesz pominąć w sekcji "Uwierzytelnianie przy użyciu serwera wewnętrznej bazy danych".
3. Dodaj następujący kod do pełnomocnika `signIn:didSignInForUser:withError:` metodę, zgodnie z językiem, którego używasz.

    **Objective-C**:
    ```objc
    NSDictionary *payload = @{
                                @"id_token":user.authentication.idToken,
                                @"authorization_code":user.serverAuthCode
                                };

    [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
        // ...
    }];
    ```

    **Kod SWIFT**:

    ```swift
    let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
    client.loginWithProvider("google", token: payload) { (user, error) in
        // ...
    }
    ```

4. Upewnij się, możesz również dodać następujące polecenie, aby `application:didFinishLaunchingWithOptions:` w swoim delegacie aplikacji, zastępując "SERVER_CLIENT_ID" o tym samym identyfikatorze, którego użyto do skonfigurowania usługi App Service w kroku 1.

    **Objective-C**:

    ```objc
    [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
    ```

     **Kod SWIFT**:

    ```swift
    GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"
    ```

5. Dodaj następujący kod do aplikacji w UIViewController, który implementuje `GIDSignInUIDelegate` protokołu, zgodnie z językiem, którego używasz.  Zalogowano przed Trwa logowanie ponownie, a mimo że nie trzeba ponownie wprowadź swoje poświadczenia, zobaczysz okno dialogowe zgody.  Tę metodę należy wywołać tylko wtedy, gdy wygasł token sesji.

   **Objective-C**:

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

   **Kod SWIFT**:

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
[Azure mobilnych: aplikacje Szybki Start]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode
[Azure Portal]: https://portal.azure.com/
[Handling Expired Tokens]: https://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: https://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: https://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Schemat dynamiczny]: https://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: https://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: https://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: /cli/azure/get-started-with-az-cli2
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[Pulpit nawigacyjny sieci szkieletowej]: https://www.fabric.io/home
[Service Fabric dla systemu iOS — wprowadzenie]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: https://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: https://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: ../app-service/configure-authentication-provider-aad.md
[8]:../active-directory/develop/quickstart-v1-ios.md
[9]: ../app-service/configure-authentication-provider-facebook.md
[10]: https://developers.facebook.com/docs/ios/getting-started
