---
title: Tworzenie aplikacji systemu iOS, która integruje się z usługą Azure AD na potrzeby logowania i wywołuje chronione interfejsy API przy użyciu protokołu OAuth 2.0 | Microsoft Docs
description: Dowiedz się, jak logować użytkowników i wywołać interfejs API programu Microsoft Graph z aplikacji systemu iOS.
services: active-directory
documentationcenter: ios
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: brandwe
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d986ccbf92192c1fb7375e9db1fb398ed86a829
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879968"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-app"></a>Szybki start: Logowanie użytkowników i wywołanie interfejsu API programu Microsoft Graph z aplikacji systemu iOS

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Usługa Azure Active Directory (Azure AD) udostępnia bibliotekę Active Directory Authentication Library (ADAL) dla klientów systemu iOS potrzebujących dostępu do chronionych zasobów. Biblioteka ADAL upraszcza proces uzyskiwania tokenów dostępu przez aplikację. 

W tym przewodniku Szybki Start utworzysz aplikację listy zadań w języku Objective C, która:

* pobiera tokeny dostępu do wywoływania interfejsu API programu Graph usługi Azure AD przy użyciu protokołu uwierzytelniania OAuth 2.0,
* wyszukuje w katalogu użytkowników o określonym aliasie.

Aby utworzyć gotową, działającą aplikację, musisz:

1. zarejestrować aplikację w usłudze Azure AD,
1. zainstalować i skonfigurować bibliotekę ADAL,
1. uzyskać tokeny z usługi Azure AD przy użyciu biblioteki ADAL.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, należy spełnić poniższe następujące wstępne:

* [Pobranie szkieletu aplikacji](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) lub [pobranie pełnego przykładu](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).
* Dzierżawa usługi Azure AD, w której można utworzyć użytkowników i zarejestrować aplikację. Jeśli nie masz jeszcze dzierżawy, [dowiedz się, jak ją uzyskać](quickstart-create-new-tenant.md).

> [!TIP]
> Odwiedź [portal dla deweloperów](https://identity.microsoft.com/Docs/iOS), aby rozpocząć pracę z usługą Azure AD w zaledwie kilka minut. Portal dla deweloperów przeprowadzi Cię przez proces rejestracji aplikacji oraz integrowania usługi Azure AD z kodem. Gdy skończysz, będziesz mieć prostą aplikację, która może uwierzytelniać użytkowników w dzierżawie, oraz zaplecze, które może akceptować tokeny i przeprowadzać weryfikację.

## <a name="step-1-determine-what-your-redirect-uri-is-for-ios"></a>Krok 1: Określenie identyfikatora URI przekierowania dla systemu iOS

Aby bezpiecznie uruchomić aplikację w niektórych scenariuszach logowania jednokrotnego, należy utworzyć *identyfikator URI przekierowania* w określonym formacie. Identyfikator URI przekierowania służy do upewniania się, że tokeny wracają do odpowiednich aplikacji, które ich zażądały.

Format systemu iOS dla identyfikatora URI przekierowania jest następujący:

```
<app-scheme>://<bundle-id>
```

* **app-scheme**— schemat aplikacji rejestrowany w projekcie XCode, przy użyciu którego inne aplikacje mogą się z Tobą komunikować. Schemat aplikacji można znaleźć w obszarze **Info.plist > Typy adresów URL > Adres URL identyfikatora**. Utwórz schemat aplikacji, jeśli jeszcze nie masz skonfigurowanego przynajmniej jednego.
* **bundle-id** — identyfikator pakietu znajdujący się w obszarze **tożsamość** w ustawieniach projektu XCode.

Przykład kodu przewodnika Szybki start:

***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="step-2-register-the-directorysearcher-application"></a>Krok 2: Rejestrowanie aplikacji DirectorySearcher

Aby skonfigurować aplikację do uzyskiwania tokenów, należy zarejestrować aplikację w dzierżawie usługi Azure AD i przyznać jej uprawnienia dostępu do interfejsu API programu Graph usługi Azure AD.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na górnym pasku wybierz swoje konto. Z listy **Katalog** wybierz dzierżawę usługi Active Directory, w której chcesz zarejestrować aplikację.
3. Wybierz pozycję **Wszystkie usługi** w okienku nawigacji po lewej stronie, a następnie wybierz pozycję **Azure Active Directory**.
4. Wybierz pozycję **Rejestracje aplikacji**, a następnie wybierz pozycję **Dodaj**.
5. Postępuj zgodnie z monitami, aby utworzyć nową **natywną** aplikację kliencką.
    * **Nazwa** to nazwa aplikacji; opisuje aplikację innym użytkownikom.
    * **Identyfikator URI przekierowania** jest połączeniem schematu i ciągu, przy użyciu którego usługa Azure AD zwraca odpowiedzi tokenów. Wprowadź wartość specyficzną dla aplikacji i opartą na wcześniejszych informacjach dotyczących identyfikatora URI przekierowania.
6. Gdy zakończysz rejestrację, usługa Azure AD przypisze aplikacji unikatowy identyfikator. Ta wartość będzie potrzebna w kolejnych sekcjach, a więc należy skopiować ją z karty aplikacji.
7. Na stronie **Ustawienia** wybierz pozycję **Wymagane uprawnienia > Dodaj >Microsoft Graph**, a następnie w obszarze **Uprawnienia delegowane** dodaj ustawienie **Odczytuj dane katalogu**. To uprawnienie skonfiguruje aplikację do wykonywania zapytań względem interfejsu API programu Graph usługi Azure AD dotyczących użytkowników.

## <a name="step-3-install-and-configure-adal"></a>Krok 3: Instalowanie i konfigurowanie biblioteki ADAL

Gdy masz już aplikację w usłudze Azure AD, możesz zainstalować bibliotekę ADAL i napisać kod dotyczący tożsamości. Aby biblioteka ADAL mogła komunikować się z usługą Azure AD, należy podać niektóre informacje dotyczące rejestracji aplikacji.

1. Zacznij od dodania biblioteki ADAL do projektu DirectorySearcher przy użyciu menedżera CocoaPods.

    ```
    $ vi Podfile
    ```
1. Dodaj następujący kod do tego pliku podfile:

    ```
    source 'https://github.com/CocoaPods/Specs.git'
    link_with ['QuickStart']
    xcodeproj 'QuickStart'

    pod 'ADALiOS'
    ```

1. Załaduj plik podfile przy użyciu menedżera CocoaPods. Ten krok spowoduje utworzenie nowego obszaru roboczego XCode, który należy załadować.

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

1. W projekcie Szybki start otwórz plik plist `settings.plist`.
1. Zastąp wartości elementów w sekcji, aby były używane te same wartości, które wprowadzono w witrynie Azure Portal. Twój kod odwołuje się do tych wartości podczas każdego użycia biblioteki ADAL.
    * `tenant` jest to domena dzierżawy usługi Azure AD, np. contoso.onmicrosoft.com.
    * `clientId` jest to identyfikator klienta Twojej aplikacji, który został skopiowany z portalu.
    * `redirectUri` jest adres URL przekierowania, który został zarejestrowany w portalu.

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>Krok 4: Uzyskiwanie tokenów z usługi Azure AD przy użyciu biblioteki ADAL

Podstawowa zasada działania biblioteki ADAL polega na tym, że za każdym razem, gdy aplikacja potrzebuje tokenu dostępu, po prostu wywołuje ona element completionBlock `+(void) getToken :`, a biblioteka ADAL zajmuje się resztą.

1. W projekcie `QuickStart` otwórz plik `GraphAPICaller.m` i zlokalizuj komentarz `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` w górnej części pliku.

    To właśnie w tym miejscu przekazuje się bibliotece ADAL współrzędne za pośrednictwem elementu CompletionBlock, aby umożliwić komunikację z usługą Azure AD i przekazać instrukcje dotyczące sposobu buforowania tokenów.

    ```ObjC
    +(void) getToken : (BOOL) clearCache
               parent:(UIViewController*) parent
    completionHandler:(void (^) (NSString*, NSError*))completionBlock;
    {
        AppData* data = [AppData getInstance];
        if(data.userItem){
            completionBlock(data.userItem.accessToken, nil);
            return;
        }

        ADAuthenticationError *error;
        authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
        authContext.parentController = parent;
        NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

        [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
        [authContext acquireTokenWithResource:data.resourceId
                                     clientId:data.clientId
                                  redirectUri:redirectUri
                               promptBehavior:AD_PROMPT_AUTO
                                       userId:data.userItem.userInformation.userId
                        extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                             completionBlock:^(ADAuthenticationResult *result) {

                                  if (result.status != AD_SUCCEEDED)
                                  {
                                     completionBlock(nil, result.error);
                                  }
                                  else
                                  {
                                      data.userItem = result.tokenCacheStoreItem;
                                      completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                                  }
                             }];
    }

    ```

2. W celu wyszukania użytkowników na wykresie należy użyć tego tokenu. Znajdź komentarz `// TODO: implement SearchUsersList`. Ta metoda wysyła żądanie GET do interfejsu API programu Graph usługi Azure AD, aby wykonać zapytanie dotyczące użytkowników, których nazwa UPN zaczyna się od danego wyszukiwanego terminu. 

    Aby wykonać zapytanie względem interfejsu API programu Graph usługi Azure AD, należy uwzględnić element access_token w nagłówku `Authorization` żądania. Do tego właśnie służy biblioteka ADAL.

    ```ObjC
    +(void) searchUserList:(NSString*)searchString
                    parent:(UIViewController*) parent
          completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
    {
        if (!loadedApplicationSettings)
       {
            [self readApplicationSettings];
        }
        
        AppData* data = [AppData getInstance];

        NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];

        [self craftRequest:[self.class trimString:graphURL]
                    parent:parent
         completionHandler:^(NSMutableURLRequest *request, NSError *error) {

             if (error != nil)
             {
                 completionBlock(nil, error);
             }
             else
             {

                 NSOperationQueue *queue = [[NSOperationQueue alloc]init];

                 [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                     if (error == nil && data != nil){

                         NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                         // We can grab the JSON node at the top to get our graph data.
                         NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                         // Don't be thrown off by the key name being "value". It really is the name of the
                         // first node. :-)

                         // Each object is a key value pair
                         NSDictionary *keyValuePairs;
                         NSMutableArray* Users = [[NSMutableArray alloc]init];

                         for(int i =0; i < graphDataArray.count; i++)
                         {
                             keyValuePairs = [graphDataArray objectAtIndex:i];

                             User *s = [[User alloc]init];
                             s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                             s.name =[keyValuePairs valueForKey:@"givenName"];

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
         }];

    }

    ```

3. Gdy aplikacja żąda tokenu przez wywołanie metody `getToken(...)`, biblioteka ADAL próbuje zwrócić token bez monitowania użytkownika o poświadczenia. Jeśli biblioteka ADAL ustali, że użytkownik musi się zalogować, aby uzyskać token, wyświetli okno dialogowe logowania, zbierze poświadczenia użytkownika, a następnie zwróci token po pomyślnym uwierzytelnieniu. Jeśli z jakiegokolwiek powodu biblioteka ADAL nie będzie w stanie zwrócić tokenu, zgłosi wyjątek `AdalException`.

> [!NOTE]
> Obiekt `AuthenticationResult` zawiera obiekt `tokenCacheStoreItem`, przy użyciu którego możesz zebrać informacje potrzebne aplikacji. W przewodniku Szybki Start obiekt `tokenCacheStoreItem` służy do określania, czy przeprowadzono już uwierzytelnianie.

## <a name="step-5-build-and-run-the-application"></a>Krok 5. Kompilowanie i uruchamianie aplikacji

Gratulacje! Masz teraz działającą aplikację systemu iOS, która może uwierzytelniać użytkowników, bezpiecznie wywoływać internetowe interfejsy API przy użyciu protokołu OAuth 2.0 oraz uzyskiwać podstawowe informacje o użytkowniku.

Jeśli jeszcze tego nie zrobiono, warto teraz wypełnić dzierżawę użytkownikami.

1. Uruchom aplikację przewodnika Szybki start, a następnie zaloguj się przy użyciu jednego z tych użytkowników.
1. Wyszukaj innych użytkowników na podstawie ich nazw UPN.
1. Zamknij aplikację, a następnie uruchom ją ponownie. Zauważ, że sesja użytkownika pozostaje niezmieniona.

Biblioteka ADAL ułatwia dodanie wszystkich tych typowych funkcji tożsamości do aplikacji. Zajmuje się ona wszystkimi żmudnymi zadaniami, takimi jak zarządzanie pamięcią podręczną, obsługa protokołu OAuth, prezentowanie użytkownikom interfejsu użytkownika logowania i odświeżanie wygasłych tokenów. Musisz znać tylko jedno wywołanie interfejsu API, `getToken`.

Gotowa próbka (bez wartości konfiguracji) jest dostępna w serwisie [GitHub](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).

## <a name="next-steps"></a>Kolejne kroki

Możesz teraz przejść do dodatkowych scenariuszy. Sugerujemy zapoznanie się z następującymi:

* [Zabezpieczanie sieci Web środowiska Node.JS interfejsu API za pomocą usługi Azure AD](quickstart-v1-nodejs-webapi.md)
* Dowiedz się, [jak włączyć logowanie jednokrotne dla wielu aplikacji w systemie iOS przy użyciu biblioteki ADAL](howto-v1-enable-sso-ios.md)  
