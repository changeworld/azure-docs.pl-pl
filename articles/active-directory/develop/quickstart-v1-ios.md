---
title: Usługa Azure AD systemu iOS wprowadzenie | Dokumentacja firmy Microsoft
description: Jak utworzyć aplikację dla systemu iOS, która integruje się z usługą Azure AD, logowania i wywołania usługi Azure AD chroniony interfejsów API przy użyciu protokołu OAuth.
services: active-directory
documentationcenter: ios
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/30/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: jmprieur
ms.openlocfilehash: cc85dbe4bc1d207f18381e2a6d85461db229d6ea
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39506598"
---
# <a name="azure-ad-ios-getting-started"></a>Usługa Azure AD systemu iOS wprowadzenie
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

Azure Active Directory (Azure AD) zapewnia biblioteki uwierzytelniania usługi Active Directory lub biblioteki ADAL dla klientów z systemem iOS, które muszą uzyskać dostęp do chronionych zasobów. Biblioteki ADAL upraszcza ten proces, który Twoja aplikacja korzysta z umożliwiające uzyskanie tokenów dostępu. Aby zademonstrować, jak łatwo jest, w tym artykule wbudowujemy aplikację listy zadań do wykonania C cel który:

* Pobiera dostępu tokenów dla wywołania interfejsu API programu Graph usługi Azure AD przy użyciu [protokołu uwierzytelniania OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Przeszukuje katalog dla użytkowników przy użyciu podanego aliasu.

Aby utworzyć pełną działającą aplikację, należy:

1. Zarejestrować aplikację w usłudze Azure AD.
2. Instalowanie i konfigurowanie biblioteki ADAL.
3. Używa biblioteki ADAL, uzyskiwanie tokenów z usługi Azure AD.

Aby rozpocząć pracę, [pobrać szkielet aplikacji](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) lub [Pobierz przykładowe ukończone](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip). Należy również dzierżawę usługi Azure AD może tworzyć użytkowników i zarejestrowanie aplikacji. Jeśli nie masz jeszcze dzierżawy, [Dowiedz się, jak je](quickstart-create-new-tenant.md).


> [!TIP]
> Wypróbuj wersję zapoznawczą naszej nowej [portalu dla deweloperów](https://identity.microsoft.com/Docs/iOS) , które ułatwiają rozpoczęcie pracy z usługą Azure AD w ciągu kilku minut. Portal dla deweloperów przeprowadzi Cię przez proces rejestracji aplikacji i Integrowanie usługi Azure AD w kodzie. Gdy skończysz, będziesz mieć prostą aplikację, która może uwierzytelniać użytkowników w dzierżawie i zaplecza, można akceptować tokeny i sprawdzenia poprawności. 
> 
> 

## <a name="1-determine-what-your-redirect-uri-is-for-ios"></a>1. Określić, jakie Twojego przekierowania URI jest dla systemu iOS
Aby bezpiecznie uruchomić aplikacji, w niektórych scenariuszach logowania jednokrotnego, musisz utworzyć *identyfikator URI przekierowania* w określonym formacie. Identyfikator URI przekierowania jest używany, aby upewnić się, że tokeny zwrócić się do właściwej aplikacji, które się z prośbą o ich.


Format dla systemu iOS przekierowanie identyfikatora URI jest następujący:

```
<app-scheme>://<bundle-id>
```

* **Schemat aplikacji** — to jest zarejestrowany w projekcie XCode. To, jak inne aplikacje zadzwonić do Ciebie. Można znaleźć w obszarze Info.plist -> typy adresów URL -> identyfikator adresu URL. Należy utworzyć katalog, jeśli nie masz jeszcze co najmniej jeden skonfigurowany.
* **Identyfikator pakietu** — jest to identyfikator pakietu, znajdującym się "tożsamość" w ustawieniach projektu XCode.

Przykład dla tego kodu szybkiego startu: ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="2-register-the-directorysearcher-application"></a>2. Rejestrowanie aplikacji DirectorySearcher
Aby skonfigurować aplikację do uzyskania tokenów, najpierw należy zarejestrować go w dzierżawie usługi Azure AD i przyznać jej uprawnienia dostępu do interfejsu API programu Graph usługi Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na górnym pasku kliknij swoje konto. W obszarze **katalogu** wybierz dzierżawę usługi Active Directory, w którym chcesz zarejestrować aplikację.
3. Kliknij przycisk **wszystkich usług** w okienku nawigacji skrajnie po lewej stronie, a następnie wybierz pozycję **usługi Azure Active Directory**.
4. Kliknij przycisk **rejestracje aplikacji**, a następnie wybierz pozycję **Dodaj**.
5. Postępuj zgodnie z monitami, aby utworzyć nową **natywną aplikację kliencką**.
  * **Nazwa** aplikacji będzie opisywać aplikację użytkownikom końcowym.
  * **Identyfikator Uri przekierowania** jest połączeniem schemat i parametry, które usługa Azure AD używa do zwracania odpowiedzi tokenu. Wprowadź wartość, która jest specyficzne dla aplikacji i zależy od wcześniejszych informacji identyfikatora URI przekierowania.
6. Po zakończeniu rejestracji usługi Azure AD przypisze aplikacji Unikatowy identyfikator aplikacji. Ta wartość będzie potrzebna w kolejnych sekcjach, więc skopiuj ją na karcie aplikacji.
7. Z **ustawienia** wybierz opcję **wymagane uprawnienia** , a następnie wybierz **Dodaj**. Wybierz **programu Microsoft Graph** jako interfejsu API, a następnie dodaj **Czytaj dane katalogu** uprawnień w ramach **delegowane uprawnienia**. Konfiguruje aplikację do wykonywania zapytań interfejsu API programu Graph usługi Azure AD dla użytkowników.

## <a name="3-install-and-configure-adal"></a>3. Instalowanie i konfigurowanie biblioteki ADAL
Teraz, gdy masz już aplikację w usłudze Azure AD, możesz zainstalować biblioteki ADAL i pisanie kodu związanych z tożsamościami. Biblioteki ADAL w celu komunikowania się z usługą Azure AD musisz udostępnić informacje dotyczące rejestracji aplikacji.

1. Rozpocznij, dodając biblioteki ADAL do projektu DirectorySearcher przy użyciu programu CocoaPods.

    ```
    $ vi Podfile
    ```
2. Dodaj następujący kod do tego pliku podfile:

    ```
    source 'https://github.com/CocoaPods/Specs.git'
    link_with ['QuickStart']
    xcodeproj 'QuickStart'

    pod 'ADALiOS'
    ```

3. Teraz Załaduj plik podfile przy użyciu programu CocoaPods. Spowoduje to utworzenie nowego obszaru roboczego XCode, który można załadować.

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

4. W projekcie Szybki Start, otwórz plik plist `settings.plist`. Zastąp wartości elementów w sekcji, aby odzwierciedlał odpowiednie wartości, które zostały wprowadzone w witrynie Azure portal. Twój kod odwołuje się do tych wartości w każdym przypadku, gdy używa biblioteki ADAL.
  * `tenant` Jest domena dzierżawy usługi Azure AD, np. contoso.onmicrosoft.com.
  * `clientId` Jest identyfikator klienta aplikacji, który został skopiowany z portalu.
  * `redirectUri` Jest adres URL przekierowania, który został zarejestrowany w portalu.

## <a name="4-use-adal-to-get-tokens-from-azure-ad"></a>4. Użyj biblioteki ADAL w celu uzyskania tokenów z usługi Azure AD
Podstawową zasadą za ADAL jest, że zawsze wtedy, gdy Twoja aplikacja wymaga tokenu dostępu, po prostu wywołuje on completionBlock `+(void) getToken : `, a ADAL zajmie się resztą. 

1. W `QuickStart` otwarty projekt `GraphAPICaller.m` i Znajdź `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` komentarz u góry. Jest to, gdzie należy przekazać biblioteki ADAL współrzędne za pośrednictwem CompletionBlock, do komunikowania się z usługą Azure AD i poinformuj go, jak tokeny pamięci podręcznej.

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

2. Teraz należy używać tego tokenu do wyszukiwania użytkowników na wykresie. Znajdź `// TODO: implement SearchUsersList` komentarz. Ta metoda zgłasza żądanie GET interfejsu API programu Graph usługi Azure AD do zapytania dla użytkowników, których nazwy UPN zaczyna się od danego wyszukiwany termin. Aby odpytać interfejsu API programu Graph usługi Azure AD, trzeba uwzględnić ' access_token ' w `Authorization` nagłówku żądania. Jest to, gdzie jest dostępna w bibliotece ADAL.

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


3. Gdy aplikacja żąda tokenu, wywołując `getToken(...)`, biblioteka ADAL próbuje zwrócić token bez monitowania użytkownika o poświadczenia. Jeśli biblioteki ADAL okaże się, że użytkownik musi zarejestrować się w celu pobrania tokenu, jego będą wyświetlane jest okno dialogowe logowania, zbieranie poświadczeń użytkownika, a następnie wróć token po pomyślnym uwierzytelnieniu. Jeśli biblioteka ADAL nie jest w stanie do zwracania tokenu dla jakiegokolwiek powodu, zgłasza `AdalException`.

> [!Note] 
> `AuthenticationResult` Obiekt zawiera `tokenCacheStoreItem` obiektu, który może służyć do zbierania informacji, które aplikacja może być konieczne. W przewodniku Szybki Start `tokenCacheStoreItem` służy do określania, jeśli uwierzytelnianie zostało już przeprowadzone.
>
>

## <a name="5-build-and-run-the-application"></a>5. Kompilowanie i uruchamianie aplikacji
Gratulacje! Masz teraz działającą aplikację systemu iOS, która może uwierzytelniać użytkowników, bezpiecznie wywoływać interfejsy API sieci Web przy użyciu protokołu OAuth 2.0 i Uzyskaj podstawowe informacje o użytkowniku. Jeśli nie jest jeszcze teraz jest czasu na wypełnienie dzierżawy niektórych użytkowników. Rozpocznij tworzenie aplikacji Szybki Start, a następnie zaloguj się przy użyciu jednego z tych użytkowników. Wyszukiwać innych użytkowników, w oparciu o ich nazwy UPN. Zamknij aplikację, a następnie uruchom go ponownie. Należy zauważyć, że sesja użytkownika pozostaną niezmienione.

Biblioteka ADAL ułatwia uwzględnienie wszystkich tych typowe funkcje obsługi tożsamości w aplikacji. Ta odpowiada za całą pracę dirty, takich jak zarządzanie pamięcią podręczną, obsługa protokołu OAuth, prezentowanie użytkownika za pomocą interfejsu użytkownika do logowania i odświeżanie wygasła tokenów. Tak naprawdę musisz wiedzieć, wystarczy jedno wywołanie interfejsu API, `getToken`.

Odwołanie, ukończone próbka (bez wartości konfiguracji) znajduje się na [GitHub](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip). 

## <a name="next-steps"></a>Kolejne kroki
Możesz teraz przejść do dodatkowych scenariuszy. Można spróbować:

* [Zabezpieczanie sieci Web środowiska Node.JS interfejsu API za pomocą usługi Azure AD](active-directory-devquickstarts-webapi-nodejs.md)
* Dowiedz się, [sposób włączenia logowania jednokrotnego dla wielu aplikacji, w systemie iOS za pomocą biblioteki ADAL](active-directory-sso-ios.md)  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

