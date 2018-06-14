---
title: Sposób użycia zestawu iOS SDK usługi Azure Mobile Apps
description: Sposób użycia zestawu iOS SDK usługi Azure Mobile Apps
services: app-service\mobile
documentationcenter: ios
author: ysxu
manager: yochayk
editor: ''
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
ms.openlocfilehash: 63dd283605553297a7dc8feab90c8bcbd716d5de
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2017
ms.locfileid: "26660144"
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Sposób użycia iOS biblioteki klienta usługi Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Ten przewodnik zawiera wskazówki do wykonywania typowych scenariuszy przy użyciu najnowszych [iOS Azure Mobile Apps SDK][1]. Jeśli jesteś nowym użytkownikiem usługi Azure Mobile Apps, najpierw wykonać [Azure Mobile Apps Szybki Start] tworzenie zaplecza, Utwórz tabelę i pobrać projektu wbudowanych systemu iOS w programie Xcode. W tym przewodniku możemy skupić się na zestaw SDK systemu iOS po stronie klienta. Aby dowiedzieć się więcej o zestawie SDK po stronie serwera wewnętrznej bazy danych, zobacz HOWTOs zestawu SDK serwera.

## <a name="reference-documentation"></a>Dokumentacja referencyjna
Dokumentacja referencyjna dla klientów z systemem iOS SDK jest zlokalizowana tutaj: [iOS Azure Mobile Apps odwołanie klienta][2].

## <a name="supported-platforms"></a>Obsługiwane platformy
IOS SDK obsługuje projekty języka Objective-C, Swift 2.2 projektów i Swift 2.3 projektów dla systemu iOS w wersji 8.0 lub nowszej.

Uwierzytelnianie "serwer flow" używa WebView przedstawioną interfejsu użytkownika.  Jeśli urządzenie nie jest w stanie do prezentowania WebView interfejsu użytkownika, innej metody uwierzytelniania jest wymagane, która wykracza poza zakres produktu.  
Zestaw SDK w związku z tym nie jest odpowiedni dla typu czujki lub podobnie ograniczeniami urządzeń.

## <a name="Setup"></a>Instalacji i wymagania wstępne
W tym przewodniku założono, że utworzono wewnętrznej bazie danych z tabeli. W tym przewodniku założono, że tabela ma ten sam schemat jako tabele w tych samouczkach. W tym przewodniku założono, że w kodzie, można się odwołać `MicrosoftAzureMobile.framework` i zaimportować `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

## <a name="create-client"></a>Porady: Tworzenie klienta
Aby uzyskać dostęp do zaplecza Azure Mobile Apps w projekcie, Utwórz `MSClient`. Zastąp `AppUrl` z adresem URL aplikacji. Można pozostawić `gatewayURLString` i `applicationKey` puste. Skonfigurowanie bramy uwierzytelniania wypełnić `gatewayURLString` z adresem URL bramy.

**Objective-C**:

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Kod SWIFT**:

```
let client = MSClient(applicationURLString: "AppUrl")
```


## <a name="table-reference"></a>Porady: Tworzenie tabeli odwołania
Aby uzyskać dostęp do danych lub je zaktualizować, utwórz odwołanie do tabeli zaplecza. Zastąp ciąg `TodoItem` nazwą tabeli

**Objective-C**:

```
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Kod SWIFT**:

```
let table = client.tableWithName("TodoItem")
```


## <a name="querying"></a>Porady: zapytania na danych
Aby utworzyć zapytanie do bazy danych, zapytania `MSTable` obiektu. Następujące zapytanie pobiera wszystkie elementy w `TodoItem` i rejestruje tekst każdego elementu.

**Objective-C**:

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occured
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Kod SWIFT**:

```
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

## <a name="filtering"></a>Porady: Filtr zwrócił danych
Aby filtrować wyniki, istnieje wiele dostępnych opcji.

Aby filtrować, używając predykatu, użyj `NSPredicate` i `readWithPredicate`. Poniższe filtry zwrócił dane, aby znaleźć tylko niezakończonych elementów Todo.

**Objective-C**:

```
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

```
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

## <a name="query-object"></a>Porady: Użyj MSQuery
Aby wykonać złożone kwerendy (w tym sortowanie i stronicowanie), należy utworzyć `MSQuery` obiekt bezpośrednio lub za pomocą predykat:

**Objective-C**:

```
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Kod SWIFT**:

```
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery`Umożliwia sterowanie różne zachowania zapytania.

* Określ kolejność wyników
* Ogranicz pola, które mają zwracany
* Ogranicz liczbę rekordów do zwrócenia
* Określ liczbę całkowitą w odpowiedzi
* Określ parametry ciągu zapytania niestandardowe w żądaniu
* Zastosuj dodatkowe funkcje

Wykonanie `MSQuery` zapytania przez wywołanie metody `readWithCompletion` obiektu.

## <a name="sorting"></a>Porady: sortowanie danych za pomocą MSQuery
Sortowanie wyników, Przyjrzyjmy się przykładem. Aby posortować według pola 'text' rosnącej, następnie według malejącej "Pełna", należy wywołać `MSQuery` w następujący sposób:

**Objective-C**:

```
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

```
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


## <a name="selecting"></a><a name="parameters"></a>Porady: ograniczyć pól i parametrów ciągu zapytania z MSQuery rozwiń
Aby ograniczyć pola ma zostać zwrócona w zapytaniu, określ nazwy pól w **selectFields** właściwości. W tym przykładzie zwraca tylko tekst i wypełnionego pola:

**Objective-C**:

```
query.selectFields = @[@"text", @"complete"];
```

**Kod SWIFT**:

```
query.selectFields = ["text", "complete"]
```

Aby objąć parametrów ciągu zapytania dodatkowe żądania serwera (na przykład, ponieważ używa niestandardowego skryptu po stronie serwera, ich), wypełnić `query.parameters` w następujący sposób:

**Objective-C**:

```
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Kod SWIFT**:

```
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>Porady: Konfigurowanie rozmiar strony
Z usługą Azure Mobile Apps rozmiar strony określa liczbę rekordów, które są pobierane w czasie z tabeli wewnętrznej bazy danych. Wywołanie `pull` zapasową danych na podstawie tej strony rozmiaru, dopóki nie ma żadnych więcej rekordów do ściągnięcia następnie partii danych.

Istnieje możliwość skonfigurować rozmiar strony za pomocą **MSPullSettings** jak pokazano poniżej. Domyślny rozmiar strony jest 50, a w poniższym przykładzie zmieni 3.

Można skonfigurować inny rozmiar strony ze względu na wydajność. Jeśli masz dużą liczbę rekordów danych mały rozmiar strony wysokiej zmniejsza liczbę operacji serwera.

To ustawienie określa tylko do rozmiaru strony po stronie klienta. Jeśli klient żąda większy rozmiar strony nie obsługuje zaplecza aplikacji mobilnej, rozmiar strony jest ograniczona do maksymalnej wewnętrznej bazy danych jest skonfigurowany do obsługi.

To ustawienie jest również *numer* rekordów danych nie *rozmiar w bajtach*.

Jeśli możesz zwiększyć rozmiar strony klienta, należy również zwiększyć rozmiar strony na serwerze. Zobacz ["porady: dopasowywanie rozmiaru stronicowania tabeli"](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) instrukcje w tym celu.

**Objective-C**:

```
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                }
                           }];
```


**Kod SWIFT**:

```
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    }
}
```

## <a name="inserting"></a>Porady: wstawianie danych
Aby wstawić nowy wiersz w tabeli, należy utworzyć `NSDictionary` i wywoływać `table insert`. Jeśli [dynamiczne schematu] jest włączona, zaplecza aplikacji mobilnych usługi aplikacji Azure automatycznie generuje nowe kolumny na podstawie `NSDictionary`.

Jeśli `id` nie zostanie podany, wewnętrznej bazy danych automatycznie generuje nowy unikatowy identyfikator. Podaj własny `id` aby Użyj adresu e-mail adresów, nazwy użytkowników, lub własne niestandardowe wartości jako identyfikatora. Własnego Identyfikatora zapewnienie może ułatwić sprzężenia i logiki firmowe bazy danych.

`result` Zawiera nowy element, który został wstawiony. W zależności od logiki serwera może mieć dodatkowe lub zmodyfikowane dane w porównaniu do co został przekazany do serwera.

**Objective-C**:

```
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

```
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

## <a name="modifying"></a>Porady: modyfikowanie danych
Aby zaktualizować istniejący wiersz, zmodyfikuj element i wywołanie `update`:

**Objective-C**:

```
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

```
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

Można również podać identyfikator wiersza i zaktualizowane pole:

**Objective-C**:

```
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Kod SWIFT**:

```
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

Co najmniej `id` należy ustawić podczas wprowadzania aktualizacji.

## <a name="deleting"></a>Porady: usuwanie danych
Aby usunąć element, należy wywołać `delete` z elementem:

**Objective-C**:

```
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Kod SWIFT**:

```
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Alternatywnie usunięcia, podając identyfikator wiersza:

**Objective-C**:

```
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Kod SWIFT**:

```
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Co najmniej `id` atrybut musi być ustawiony w przypadku usunięcia wprowadzania.

## <a name="customapi"></a>Porady: wywołanie niestandardowego interfejsu API
Z niestandardowego interfejsu API pozwala udostępnić wszystkie funkcje wewnętrznej bazy danych. Nie ma do mapowania na operację tabeli. Nie tylko możesz uzyskać większą kontrolę nad wiadomości, można nawet odczytu/ustawiania nagłówków i zmień format treść odpowiedzi. Aby dowiedzieć się, jak utworzyć niestandardowego interfejsu API do wewnętrznej bazy danych, przeczytaj [niestandardowych interfejsów API](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)

Aby wywołać niestandardowego interfejsu API, należy wywołać `MSClient.invokeAPI`. Żądanie i odpowiedź zawartości są traktowane jako JSON. Aby korzystać z innych typów nośników [Użyj innego przeciążenia `invokeAPI` ] [ 5].  Aby `GET` żądania zamiast `POST` żądania, ustaw parametr `HTTPMethod` do `"GET"` i parametru `body` do `nil` (ponieważ żądania GET nie ma treści wiadomości.) Niestandardowy interfejs API obsługuje inne zleceń HTTP, zmiana `HTTPMethod` odpowiednio.

**Objective-C**:

```
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

```
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

## <a name="templates"></a>Porady: szablony wypychania rejestru do wysyłania powiadomień i platform
Aby zarejestrować szablonów, należy przekazać szablony z Twojej **client.push registerDeviceToken** metody w aplikacji klienta.

**Objective-C**:

```
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Kod SWIFT**:

```
    client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
        if let err = error {
            print("ERROR ", err)
        }
    })
```

Szablony typu NSDictionary i mogą zawierać wiele szablonów w następującym formacie:

**Objective-C**:

```
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Kod SWIFT**:

```
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Wszystkie tagi są usuwane z żądania dotyczące zabezpieczeń.  Aby dodać tagi do instalacji lub szablonów w ramach instalacji, zobacz [pracować z serwera wewnętrznej bazy danych .NET SDK usługi Azure Mobile Apps][4].  Aby wysłać powiadomienia za pomocą tych szablonów w zarejestrowany, pracy z [interfejsów API centra powiadomień][3].

## <a name="errors"></a>Porady: obsługa błędów
Podczas wywoływania zaplecze aplikacji mobilnej Azure App Service zawiera bloku ukończenia `NSError` parametru. Gdy wystąpi błąd, ten parametr jest tokenem nil. W kodzie należy sprawdzić ten parametr i obsługi błędu w razie potrzeby, jak pokazano w poprzedzających fragmentów kodu.

Plik [ `<WindowsAzureMobileServices/MSError.h>` ] [ 6] definiuje stałe `MSErrorResponseKey`, `MSErrorRequestKey`, i `MSErrorServerItemKey`. Aby wyświetlić więcej danych związane z błędem:

**Objective-C**:

```
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Kod SWIFT**:

```
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Ponadto plik definiuje stałe dla każdego kod błędu:

**Objective-C**:

```
if (error.code == MSErrorPreconditionFailed) {
```

**Kod SWIFT**:

```
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Porady: uwierzytelniać użytkowników za pomocą biblioteki uwierzytelniania usługi Active Directory
Active Directory Authentication Library (ADAL) służy do logowania się użytkowników do aplikacji za pomocą usługi Azure Active Directory. Przepływ uwierzytelnianie przy użyciu dostawcy tożsamości zestawu SDK zalecane jest stosowanie `loginWithProvider:completion:` metody.  Uwierzytelnianie klienta przepływu zapewnia bardziej natywnego działania środowiska użytkownika i zezwala na dodatkowe dostosowanie.

1. Konfigurowanie zaplecza aplikacji mobilnej dla logowania w usłudze AAD, wykonując [konfigurowania aplikacji usługi logowania usługi Active Directory] [ 7] samouczka. Upewnij się ukończyć opcjonalny krok rejestrowania aplikację native client. Dla systemu iOS, zaleca się przekierowania URI ma postać `<app-scheme>://<bundle-id>`. Aby uzyskać więcej informacji, zobacz [ADAL dla systemu iOS — Szybki Start][8].
2. Instalowanie przy użyciu programu Cocoapods biblioteki ADAL. Edytowanie użytkownika Podfile, aby uwzględnić następującą definicję zastępowanie **YOUR PROJECT** o nazwie projektu Xcode:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   i Pod:

        pod 'ADALiOS'
3. Przy użyciu terminala, uruchom `pod install` od katalogu zawierającego projektu, a następnie otwórz wygenerowany obszaru roboczego Xcode (nie projekt).
4. Dodaj następujący kod do aplikacji, zgodnie z językiem, którego używasz. W każdym należy te elementy zastępcze:

   * Zastąp **INSERT urzędu tutaj** o nazwie dzierżawy, w którym są udostępniane aplikacji. Format powinien być https://login.microsoftonline.com/contoso.onmicrosoft.com. Tę wartość można skopiować na karcie domeny w usłudze Azure Active Directory w [portalu Azure].
   * Zastąp **Wstaw zasób — identyfikator-tutaj** z Identyfikatorem klienta dla zaplecza aplikacji mobilnej. Możesz uzyskać identyfikator klienta z **zaawansowane** w obszarze **ustawień usługi Azure Active Directory** w portalu.
   * Zastąp **INSERT klienta-ID-tutaj** z Identyfikatorem klienta, którego skopiowano aplikację native client.
   * Zastąp **INSERT PRZEKIEROWANIA-URI-tutaj** z witryny */.auth/login/done* punktu końcowego, za pomocą schematu HTTPS. Ta wartość powinna być podobna do *https://contoso.azurewebsites.net/.auth/login/done*.

**Objective-C**:

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


**Kod SWIFT**:

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

## <a name="facebook-sdk"></a>Porady: uwierzytelnianie użytkowników przy użyciu zestawu SDK usługi Facebook dla systemu iOS
Zestaw SDK usługi Facebook dla systemu iOS można użyć do logowania się użytkowników do aplikacji za pomocą usługi Facebook.  Przy użyciu uwierzytelniania przepływu klienta zalecane jest stosowanie `loginWithProvider:completion:` metody.  Przepływ uwierzytelniania klienta zawiera więcej natywnego działania środowiska użytkownika i zezwala na dodatkowe dostosowanie.

1. Konfigurowanie zaplecza aplikacji mobilnej dla logowania w witrynie Facebook, wykonując [konfigurowania aplikacji usługi Facebook logowania] [ 9] samouczka.
2. Zainstaluj zestaw SDK usługi Facebook dla systemu iOS, wykonując [Facebook SDK dla systemu iOS — wprowadzenie] [ 10] dokumentacji. Zamiast tworzenia aplikacji, można dodać do istniejącej rejestracji w przypadku platformy iOS.
3. Dokumentacja w serwisie Facebook zawiera kod języka Objective-C w elemencie delegowanym aplikacji. Jeśli używasz **Swift**, można użyć następujących tłumaczenia dla AppDelegate.swift:

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
4. Oprócz dodania `FBSDKCoreKit.framework` do projektu, a także dodać odwołanie do `FBSDKLoginKit.framework` w taki sam sposób.
5. Dodaj następujący kod do aplikacji, zgodnie z językiem, którego używasz.

**Objective-C**:

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

**Kod SWIFT**:

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

## <a name="twitter-fabric"></a>Porady: uwierzytelniać użytkowników za pomocą usługi Twitter sieci szkieletowej dla systemu iOS
Sieci szkieletowej dla systemu iOS można użyć do logowania się użytkowników do aplikacji przy użyciu usługi Twitter. Uwierzytelnianie klienta przepływ zalecane jest stosowanie `loginWithProvider:completion:` metody, ponieważ zawiera więcej natywnego działania środowiska użytkownika i zezwala na dodatkowe dostosowanie.

1. Konfigurowanie zaplecza aplikacji mobilnej dla logowania w serwisie Twitter, wykonując [konfigurowania aplikacji usługi Twitter logowania](../app-service/app-service-mobile-how-to-configure-twitter-authentication.md) samouczka.
2. Dodać do projektu sieci szkieletowej, wykonując [sieci szkieletowej dla systemu iOS — wprowadzenie] dokumentacji i konfigurując TwitterKit.

   > [!NOTE]
   > Domyślnie sieci szkieletowej tworzy aplikację usługi Twitter dla Ciebie. Aby uniknąć tworzenia aplikacji przez rejestrowanie klucz klienta i klucz tajny klienta został utworzony wcześniej przy użyciu poniższej wstawki kodu.    Alternatywnie można zastąpić wartości klucz klienta i klucz tajny klienta, które świadczą usługi aplikacji przy użyciu wartości widoczne w [pulpitu nawigacyjnego sieci szkieletowej]. Jeśli wybierzesz tę opcję, należy ustawić adres URL wywołania zwrotnego na wartość symbolu zastępczego, takich jak `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.
   >
   >

    Jeśli chcesz użyć hasła, utworzony wcześniej, Dodaj następujący kod do pełnomocnika aplikacji:

    **Objective-C**:

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

    **Kod SWIFT**:

        import Fabric
        import TwitterKit
        // ...
        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
            Fabric.with([Twitter.self])
            // Add any custom logic here.
            return true
        }
3. Dodaj następujący kod do aplikacji, zgodnie z językiem, którego używasz.

**Objective-C**:

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

**Kod SWIFT**:

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

## <a name="google-sdk"></a>Porady: uwierzytelnianie użytkowników przy użyciu usługi Google logowania zestawu SDK dla systemu iOS
Google logowania SDK dla systemu iOS można użyć do logowania się użytkowników do aplikacji przy użyciu konta Google.  Google zapowiedziała niedawno zmianach zasad zabezpieczeń ich OAuth.  Te zmiany zasad wymaga użycia zestawu SDK Google w przyszłości.

1. Konfigurowanie zaplecza aplikacji mobilnej dla logowania w witrynie Google, wykonując [konfigurowania aplikacji usługi Google logowania](../app-service/app-service-mobile-how-to-configure-google-authentication.md) samouczka.
2. Zainstaluj zestaw SDK Google dla systemu iOS, wykonując [Google logowania dla systemu iOS — rozpocząć integrowanie](https://developers.google.com/identity/sign-in/ios/start-integrating) dokumentacji. Można pominąć w sekcji "Uwierzytelnianie z serwera wewnętrznej bazy danych".
3. Dodaj następujący kod do pełnomocnika `signIn:didSignInForUser:withError:` metody, zgodnie z językiem używasz.

**Objective-C**:

        NSDictionary *payload = @{
                                  @"id_token":user.authentication.idToken,
                                  @"authorization_code":user.serverAuthCode
                                  };

        [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
            // ...
        }];

**Kod SWIFT**:

        let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
        client.loginWithProvider("google", token: payload) { (user, error) in
            // ...
        }

1. Upewnij się, że możesz także dodać następujące polecenie, aby `application:didFinishLaunchingWithOptions:` w elemencie delegowanym Twojej aplikacji, zastępując "SERVER_CLIENT_ID" ten sam identyfikator, który został użyty do skonfigurowania usługi aplikacji w kroku 1.

**Objective-C**:

         [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";

 **Kod SWIFT**:

        GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"


1. Dodaj następujący kod do aplikacji w UIViewController, który implementuje `GIDSignInUIDelegate` protokołu, zgodnie z językiem używasz.  Zalogowano przed Trwa logowanie ponownie, a mimo że nie trzeba ponownie wprowadź swoje poświadczenia, zobacz okno dialogowe zgody.  Tę metodę należy wywoływać tylko wtedy, gdy wygaśnie tokenu sesji.

   **Objective-C**:

       #import <Google/SignIn.h>
       // ...
       - (void)authenticate
       {
               [GIDSignIn sharedInstance].uiDelegate = self;
               [[GIDSignIn sharedInstance] signOut];
               [[GIDSignIn sharedInstance] signIn];
        }

   **Kod SWIFT**:

       // ...
       func authenticate() {
           GIDSignIn.sharedInstance().uiDelegate = self
           GIDSignIn.sharedInstance().signOut()
           GIDSignIn.sharedInstance().signIn()
       }

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
[Azure Mobile Apps Szybki Start]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode
[portalu Azure]: https://portal.azure.com/
[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[dynamiczne schematu]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: /cli/azure/get-started-with-az-cli2
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[pulpitu nawigacyjnego sieci szkieletowej]: https://www.fabric.io/home
[sieci szkieletowej dla systemu iOS — wprowadzenie]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: http://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: http://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[8]: ../active-directory/active-directory-devquickstarts-ios.md
[9]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[10]: https://developers.facebook.com/docs/ios/getting-started
