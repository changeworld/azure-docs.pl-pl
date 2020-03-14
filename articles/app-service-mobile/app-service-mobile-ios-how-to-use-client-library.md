---
title: Korzystanie z zestawu SDK systemu iOS
description: Jak używać zestawu iOS SDK dla platformy Azure Mobile Apps
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 1bf8f8e198f6c4a4a0af308262cd830685698a80
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249349"
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Jak używać biblioteki klienckiej systemu iOS dla usługi Azure Mobile Apps

[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Omówienie
W tym przewodniku nauczysz się wykonywać typowe scenariusze przy użyciu najnowszego [zestawu Azure Mobile Apps iOS SDK][1]. Jeśli dopiero zaczynasz pracę z platformą Azure Mobile Apps, najpierw Ukończ [Szybki start Mobile Apps platformy Azure] , aby utworzyć zaplecze, utworzyć tabelę i pobrać wstępnie utworzony projekt systemu iOS Xcode. W tym przewodniku koncentrujemy się na stronie klienta zestawu iOS SDK. Aby dowiedzieć się więcej o zestawie SDK po stronie serwera dla zaplecza, zapoznaj się z zestawem SDK serwera HOWTOs.

## <a name="reference-documentation"></a>Dokumentacja referencyjna

Dokumentacja referencyjna zestawu SDK klienta systemu iOS znajduje się w tym miejscu: [informacje dotyczące klienta platformy Azure Mobile Apps systemu iOS][2].

## <a name="supported-platforms"></a>Obsługiwane platformy

Zestaw iOS SDK obsługuje projekty w języku C, szybkie projekty 2,2 i projekty SWIFT 2,3 dla systemu iOS w wersji 8,0 lub nowszej.

Uwierzytelnianie "serwer-przepływ" używa widoku WebView dla prezentowanego interfejsu użytkownika.  Jeśli urządzenie nie może przedstawić interfejsu użytkownika WebView, wymagana jest inna metoda uwierzytelniania, która jest poza zakresem produktu.  
Ten zestaw SDK jest w tym przypadku nieodpowiedni do typu czujka lub urządzeń z ograniczeniami.

## <a name="Setup"></a>Instalacja i wymagania wstępne

W tym przewodniku przyjęto założenie, że utworzono zaplecze z tabelą. W tym przewodniku przyjęto założenie, że tabela ma ten sam schemat co tabele w tych samouczkach. W tym przewodniku przyjęto również założenie, że w kodzie można przywołać się `MicrosoftAzureMobile.framework` i zaimportować `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

## <a name="create-client"></a>Instrukcje: tworzenie klienta

Aby uzyskać dostęp do zaplecza Mobile Apps platformy Azure w projekcie, Utwórz `MSClient`. Zastąp `AppUrl` adresem URL aplikacji. Możesz opuścić `gatewayURLString` i `applicationKey` puste. Jeśli skonfigurujesz bramę do uwierzytelniania, Wypełnij `gatewayURLString` przy użyciu adresu URL bramy.

**Cel-C**:

```objc
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Kod SWIFT**:

```swift
let client = MSClient(applicationURLString: "AppUrl")
```

## <a name="table-reference"></a>Instrukcje: Tworzenie odwołania do tabeli

Aby uzyskać dostęp do danych lub je zaktualizować, utwórz odwołanie do tabeli zaplecza. Zastąp ciąg `TodoItem` nazwą tabeli

**Cel-C**:

```objc
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Kod SWIFT**:

```swift
let table = client.tableWithName("TodoItem")
```

## <a name="querying"></a>Instrukcje: zapytanie o dane

Aby utworzyć zapytanie do bazy danych, wykonaj zapytanie dotyczące obiektu `MSTable`. Poniższe zapytanie pobiera wszystkie elementy w `TodoItem` i rejestruje tekst każdego elementu.

**Cel-C**:

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

## <a name="filtering"></a>Instrukcje: filtrowanie zwróconych danych

Aby przefiltrować wyniki, istnieje wiele dostępnych opcji.

Aby filtrować przy użyciu predykatu, użyj `NSPredicate` i `readWithPredicate`. Następujące filtry zwracają dane, aby znaleźć tylko niekompletne elementy do wykonania.

**Cel-C**:

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

## <a name="query-object"></a>Instrukcje: korzystanie z MSQuery

Aby wykonać złożone zapytanie (w tym sortowanie i stronicowanie), Utwórz obiekt `MSQuery`, bezpośrednio lub przy użyciu predykatu:

**Cel-C**:

```objc
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Kod SWIFT**:

```swift
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery` pozwala kontrolować kilka zachowań zapytań.

* Określ kolejność wyników
* Ograniczanie pól do zwrócenia
* Ogranicz liczbę rekordów do zwrócenia
* Określ łączną liczbę w odpowiedzi
* Określ niestandardowe parametry ciągu zapytania w żądaniu
* Zastosuj dodatkowe funkcje

Wykonaj kwerendę `MSQuery`, wywołując `readWithCompletion` dla obiektu.

## <a name="sorting"></a>Instrukcje: sortowanie danych za pomocą MSQuery

Aby posortować wyniki, przyjrzyjmy się przykładowi. Aby posortować według pola "tekst" rosnąco, następnie przez "Complete", wywołaj `MSQuery` podobne do tego:

**Cel-C**:

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

## <a name="selecting"></a><a name="parameters"></a>Instrukcje: ograniczanie pól i rozszerzanie parametrów ciągu zapytania za pomocą MSQuery

Aby ograniczyć pola, które mają być zwracane w zapytaniu, określ nazwy pól we właściwości **selectFields** . Ten przykład zwraca tylko pola tekstowe i ukończone:

**Cel-C**:

```objc
query.selectFields = @[@"text", @"complete"];
```

**Kod SWIFT**:

```swift
query.selectFields = ["text", "complete"]
```

Aby dołączać dodatkowe parametry ciągu zapytania do żądania serwera (na przykład ze względu na to, że niestandardowy skrypt po stronie serwera używa tych parametrów), Wypełnij `query.parameters` tak:

**Cel-C**:

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

## <a name="paging"></a>Instrukcje: Konfigurowanie rozmiaru strony

W przypadku usługi Azure Mobile Apps rozmiar strony określa liczbę rekordów, które są ściągane w czasie z tabel zaplecza. Wywołanie `pull` danych spowoduje następnie wsadowe przetwarzanie danych na podstawie tego rozmiaru strony, dopóki nie będzie więcej rekordów do ściągnięcia.

Istnieje możliwość skonfigurowania rozmiaru strony przy użyciu **MSPullSettings** , jak pokazano poniżej. Domyślny rozmiar strony to 50, a Poniższy przykład zmienia go na 3.

Można skonfigurować inny rozmiar strony ze względu na wydajność. Jeśli masz dużą liczbę rekordów małych danych, wysoki rozmiar strony zmniejsza liczbę operacji rundy serwerów.

To ustawienie kontroluje tylko rozmiar strony po stronie klienta. Jeśli klient poprosi o zwiększenie rozmiaru strony niż obsługuje Mobile Apps zaplecze, rozmiar strony jest limitem maksymalnym dla wewnętrznej bazy danych skonfigurowanej do obsługi.

To ustawienie jest również *liczbą* rekordów danych, a nie z *rozmiarem bajtowym*.

W przypadku zwiększenia rozmiaru strony klienta należy również zwiększyć rozmiar strony na serwerze. Aby zapoznać się z czynnościami, zobacz ["How to: Dostosuj rozmiar stronicowania tabeli"](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

**Cel-C**:

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

## <a name="inserting"></a>Instrukcje: Wstawianie danych

Aby wstawić nowy wiersz tabeli, Utwórz `NSDictionary` i Wywołaj `table insert`. Jeśli [schemat dynamiczny] jest włączony, zaplecze Azure App Service Mobile automatycznie generuje nowe kolumny na podstawie `NSDictionary`.

Jeśli nie podano `id`, zaplecze automatycznie wygeneruje nowy unikatowy identyfikator. Podaj swoje własne `id` do używania adresów e-mail, nazw użytkowników lub własnych wartości niestandardowych jako identyfikatora. Podanie własnego identyfikatora może ułatwić sprzężenie i logikę bazy danych zorientowaną na działalność biznesową.

`result` zawiera nowy element, który został wstawiony. W zależności od logiki serwera mogą istnieć dodatkowe lub zmodyfikowane dane w porównaniu z tym, co zostało przesłane do serwera.

**Cel-C**:

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

## <a name="modifying"></a>Instrukcje: modyfikowanie danych

Aby zaktualizować istniejący wiersz, zmodyfikuj element i Wywołaj `update`:

**Cel-C**:

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

Alternatywnie podaj identyfikator wiersza i zaktualizowane pole:

**Cel-C**:

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

Podczas wprowadzania aktualizacji należy określić minimalny atrybut `id`.

## <a name="deleting"></a>Instrukcje: usuwanie danych

Aby usunąć element, wywołaj `delete` z elementem:

**Cel-C**:

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

Alternatywnie Usuń, podając Identyfikator wiersza:

**Cel-C**:

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

Należy określić co najmniej atrybut `id` podczas wprowadzania usunięć.

## <a name="customapi"></a>Instrukcje: Wywoływanie niestandardowego interfejsu API

Za pomocą niestandardowego interfejsu API można uwidocznić dowolne funkcje zaplecza. Nie trzeba mapować do operacji tabeli. Nie tylko zapewniasz większą kontrolę nad obsługą komunikatów, można nawet odczytać/ustawić nagłówki i zmienić format treści odpowiedzi.

Aby wywołać niestandardowy interfejs API, wywołaj `MSClient.invokeAPI`. Zawartość żądania i odpowiedzi jest traktowana jako kod JSON. Aby użyć innych typów nośników, [Użyj innego przeciążenia `invokeAPI`][5].  Aby żądania `GET`, a nie żądania `POST`, należy ustawić `HTTPMethod` parametru na `"GET"` i `body` parametru na `nil` (ponieważ żądania GET nie mają treści wiadomości). Jeśli niestandardowy interfejs API obsługuje inne zlecenia HTTP, należy odpowiednio zmienić `HTTPMethod`.

**Cel-C**:

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

## <a name="templates"></a>Instrukcje: rejestrowanie szablonów wypychania do wysyłania powiadomień na wielu platformach

Aby zarejestrować szablony, Przekaż szablony z użyciem metody **Client. push registerDeviceToken** w aplikacji klienckiej.

**Cel-C**:

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

Szablony są typu Nsdictionary zawierający i mogą zawierać wiele szablonów w następującym formacie:

**Cel-C**:

```objc
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Kod SWIFT**:

```swift
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Wszystkie Tagi są usuwane z żądania zabezpieczeń.  Aby dodać tagi do instalacji lub szablonów w ramach instalacji, zobacz artykuł [współpraca z zestawem SDK serwera zaplecza platformy .NET dla platformy Azure Mobile Apps][4].  Aby wysyłać powiadomienia przy użyciu tych zarejestrowanych szablonów, należy korzystać z [Notification Hubs interfejsów API][3].

## <a name="errors"></a>Instrukcje: obsługa błędów

W przypadku wywołania zaplecza Azure App Service Mobile, blok uzupełniania zawiera parametr `NSError`. Gdy wystąpi błąd, ten parametr nie jest pusty. W kodzie, należy sprawdzić ten parametr i obsłużyć błąd zgodnie z potrzebami, jak pokazano w poprzednich fragmentach kodu.

[`<WindowsAzureMobileServices/MSError.h>`][6] pliku definiuje stałe `MSErrorResponseKey`, `MSErrorRequestKey`i `MSErrorServerItemKey`. Aby uzyskać więcej danych związanych z błędem:

**Cel-C**:

```objc
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Kod SWIFT**:

```swift
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Ponadto plik definiuje stałe dla każdego kodu błędu:

**Cel-C**:

```objc
if (error.code == MSErrorPreconditionFailed) {
```

**Kod SWIFT**:

```swift
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Instrukcje: uwierzytelnianie użytkowników za pomocą Active Directory Authentication Library

Active Directory Authentication Library (ADAL) służy do podpisywania użytkowników w aplikacji przy użyciu Azure Active Directory. Uwierzytelnianie przepływu klienta przy użyciu zestawu SDK dostawcy tożsamości jest preferowane przy użyciu metody `loginWithProvider:completion:`.  Uwierzytelnianie przepływu klienta zapewnia bardziej natywny sposób działania środowiska użytkownika i umożliwia dodatkowe dostosowanie.

1. Skonfiguruj zaplecze aplikacji mobilnej na potrzeby logowania do usługi AAD, wykonując czynności opisane w samouczku [jak skonfigurować App Service do Active Directory logowania][7] . Pamiętaj, aby ukończyć opcjonalny krok rejestracji natywnej aplikacji klienckiej. W przypadku systemu iOS zalecamy, aby identyfikator URI przekierowania miał postać `<app-scheme>://<bundle-id>`. Aby uzyskać więcej informacji, zobacz [Przewodnik Szybki Start dla systemu iOS][8]w systemie ADAL.
2. Zainstaluj biblioteki ADAL przy użyciu Cocoapods. Edytuj swój plik podfile, aby uwzględnić następującą definicję, zastępując **swój projekt** nazwą projektu Xcode:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   i pod:

        pod 'ADALiOS'

3. Korzystając z terminalu, uruchom `pod install` z katalogu zawierającego projekt, a następnie otwórz wygenerowany obszar roboczy Xcode (nie projekt).
4. Dodaj następujący kod do aplikacji zgodnie z używanym językiem. W każdym z tych zamierzeń:

   * Zastąp **ciąg INSERT-Authority-** in nazwą dzierżawy, w której została zainicjowana aplikacja. Format powinien być https://login.microsoftonline.com/contoso.onmicrosoft.com. Tę wartość można skopiować z karty domena w Azure Active Directory w [Azure Portal].
   * Zastąp wartość **INSERT-Resource-ID w tym miejscu** identyfikatorem klienta dla zaplecze aplikacji mobilnej. Identyfikator klienta można uzyskać z karty **Zaawansowane** w obszarze **Ustawienia Azure Active Directory** w portalu.
   * Zastąp **ciąg INSERT-Client-ID w tym miejscu** identyfikatorem klienta skopiowanym z natywnej aplikacji klienckiej.
   * Zastąp ciąg **INSERT-redirect-URI — tutaj** z punktem końcowym */.auth/login/done* Twojej witryny przy użyciu schematu https. Ta wartość powinna być podobna do *https://contoso.azurewebsites.net/.auth/login/done* .

**Cel-C**:

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

## <a name="facebook-sdk"></a>Instrukcje: uwierzytelnianie użytkowników za pomocą zestawu Facebook SDK dla systemu iOS

Za pomocą zestawu SDK usługi Facebook dla systemu iOS można rejestrować użytkowników w aplikacji za pomocą serwisu Facebook.  Użycie metody uwierzytelniania przepływu klienta jest preferowane w przypadku korzystania z `loginWithProvider:completion:`.  Uwierzytelnianie przepływu klienta zapewnia bardziej natywny sposób działania środowiska użytkownika i umożliwia dodatkowe dostosowanie.

1. Skonfiguruj zaplecze aplikacji mobilnej na potrzeby logowania do usługi Facebook, wykonując czynności opisane w temacie [jak skonfigurować App Service na potrzeby logowania do serwisu Facebook][9] .
2. Zainstaluj zestaw Facebook SDK dla systemu iOS, postępując zgodnie z dokumentacją usługi [Facebook SDK dla systemu iOS — wprowadzenie][10] . Zamiast tworzyć aplikacje, możesz dodać platformę iOS do istniejącej rejestracji.
3. Dokumentacja serwisu Facebook zawiera kod "cel-C" w Delegatze aplikacji. Jeśli używasz **SWIFT**, możesz użyć następujących tłumaczeń dla AppDelegate. SWIFT:

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
4. Oprócz dodawania `FBSDKCoreKit.framework` do projektu, należy również dodać odwołanie do `FBSDKLoginKit.framework` w taki sam sposób.
5. Dodaj następujący kod do aplikacji zgodnie z używanym językiem.

    **Cel-C**:

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

## <a name="twitter-fabric"></a>Instrukcje: uwierzytelnianie użytkowników za pomocą sieci szkieletowej Twitter dla systemu iOS

Możesz użyć sieci szkieletowej dla systemu iOS, aby zalogować użytkowników do aplikacji przy użyciu usługi Twitter. Uwierzytelnianie przepływu klienta jest preferowane przy użyciu metody `loginWithProvider:completion:`, ponieważ zapewnia bardziej natywny sposób działania środowiska użytkownika i umożliwia dodatkowe dostosowanie.

1. Skonfiguruj zaplecze aplikacji mobilnej na potrzeby logowania do usługi Twitter, postępując zgodnie z artykułem [konfigurowanie App Service na potrzeby logowania do usługi Twitter](../app-service/configure-authentication-provider-twitter.md) .
2. Dodaj sieć szkieletową do projektu, postępując zgodnie z dokumentacją [Sieć szkieletowa dla systemu iOS — Wprowadzenie] i konfigurując TwitterKit.

   > [!NOTE]
   > Domyślnie Sieć szkieletowa tworzy aplikację w usłudze Twitter. Można uniknąć tworzenia aplikacji przez zarejestrowanie klucza klienta i wpisu tajnego klienta utworzonego wcześniej przy użyciu poniższych fragmentów kodu.    Alternatywnie można zastąpić wartości klucza klienta i wpisu tajnego konsumenta, które podano w App Service z wartościami widocznymi na [Pulpit nawigacyjny sieci szkieletowej]. Jeśli wybierzesz tę opcję, pamiętaj, aby ustawić adres URL wywołania zwrotnego na wartość zastępczą, taką jak `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.

    Jeśli zdecydujesz się użyć utworzonych wcześniej wpisów tajnych, Dodaj następujący kod do delegata aplikacji:

    **Cel-C**:

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

3. Dodaj następujący kod do aplikacji zgodnie z używanym językiem.

    **Cel-C**:

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

## <a name="google-sdk"></a>Instrukcje: uwierzytelnianie użytkowników za pomocą zestawu SDK logowania Google dla systemu iOS

Możesz użyć zestawu SDK logowania Google dla systemu iOS, aby podpisać użytkowników w aplikacji przy użyciu konta Google.  Firma Google ogłosiła ostatnio zmiany zasad zabezpieczeń protokołu OAuth.  Te zmiany zasad będą wymagały używania usługi Google SDK w przyszłości.

1. Skonfiguruj zaplecze aplikacji mobilnej na potrzeby logowania do usługi Google, postępując zgodnie z samouczkiem [konfigurowanie App Service na potrzeby logowania do usługi Google login](../app-service/configure-authentication-provider-google.md) .
2. Zainstaluj zestaw Google SDK dla systemu iOS, wykonując następujące czynności: [Logowanie za pomocą usługi Google dla systemu iOS — Rozpocznij integrację](https://developers.google.com/identity/sign-in/ios/start-integrating) dokumentacji. Możesz pominąć sekcję "uwierzytelnianie za pomocą serwera zaplecza".
3. Dodaj następujący element do metody `signIn:didSignInForUser:withError:` delegata, zgodnie z używanym językiem.

    **Cel-C**:
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

4. Upewnij się, że dodano również następujące elementy do `application:didFinishLaunchingWithOptions:` w delegacie aplikacji, zastępując "SERVER_CLIENT_ID" IDENTYFIKATORem, który został użyty do skonfigurowania App Service w kroku 1.

    **Cel-C**:

    ```objc
    [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
    ```

     **Kod SWIFT**:

    ```swift
    GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"
    ```

5. Dodaj następujący kod do aplikacji w UIViewController, który implementuje protokół `GIDSignInUIDelegate`, zgodnie z używanym językiem.  Wylogowano się przed ponownym zalogowaniem, ale nie musisz ponownie wprowadzać poświadczeń, zobaczysz okno dialogowe zgody.  Wywołaj tę metodę tylko po wygaśnięciu tokenu sesji.

   **Cel-C**:

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
[Szybki start Mobile Apps platformy Azure]: app-service-mobile-ios-get-started.md

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
[Schemat dynamiczny]: https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/dynamic-schema
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: https://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: https://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: /cli/azure/get-started-with-az-cli2
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[Pulpit nawigacyjny sieci szkieletowej]: https://www.fabric.io/home
[Sieć szkieletowa dla systemu iOS — Wprowadzenie]: https://docs.fabric.io/ios/fabric/getting-started.html
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
