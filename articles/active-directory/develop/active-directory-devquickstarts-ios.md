---
title: Azure AD systemu iOS wprowadzenie | Dokumentacja firmy Microsoft
description: Sposób tworzenia aplikacji systemu iOS, która integruje się z usługą Azure AD, logowania i wywołania usługi Azure AD chronione interfejsów API przy użyciu uwierzytelniania OAuth.
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
ms.openlocfilehash: 1ceae59cca5790d9d74f72ce644e31fb0949cd49
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="azure-ad-ios-getting-started"></a>Azure AD systemu iOS wprowadzenie
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

Azure Active Directory (Azure AD) udostępnia biblioteki uwierzytelniania usługi Active Directory lub biblioteki ADAL dla klientów z systemem iOS, które wymagają dostępu do chronionych zasobów. ADAL upraszcza proces Twoja aplikacja korzysta z umożliwiające uzyskanie tokenów dostępu. Aby zademonstrować, jak łatwo jest, w tym artykule mamy utworzyć aplikację listy zadań do wykonania Objective C który:

* Pobiera dostępu tokenów do wywoływania za pomocą interfejsu API programu Azure AD Graph [protokół uwierzytelniania OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Przeszukuje katalog dla użytkowników z podanego aliasu.

Aby utworzyć pełną działającą aplikację, musisz:

1. Zarejestrować aplikację w usłudze Azure AD.
2. Instalowanie i konfigurowanie biblioteki ADAL.
3. Użyj biblioteki ADAL, aby uzyskać tokenów z usługi Azure AD.

Aby rozpocząć, [pobrać szkielet aplikacji](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) lub [Pobieranie ukończone próbki](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip). Należy również dzierżawa usługi Azure AD można tworzyć użytkowników i zarejestrowanie aplikacji. Jeśli nie masz już dzierżawę, [Dowiedz się, jak kupić](active-directory-howto-tenant.md).


> [!TIP]
> Spróbuj Podgląd nowe [portalu dla deweloperów](https://identity.microsoft.com/Docs/iOS) pomaga rozpocząć pracę z usługą Azure AD w ciągu kilku minut. Portalu dla deweloperów prowadzi użytkownika przez proces rejestracji aplikacji i Integrowanie usługi Azure AD w kodzie. Po zakończeniu, będziesz mieć prostą aplikację, która może uwierzytelniać użytkowników w dzierżawie, a kończyć kopii, która może zaakceptować tokeny i sprawdzania poprawności. 
> 
> 

## <a name="1-determine-what-your-redirect-uri-is-for-ios"></a>1. Ustalić, jakie z przekierowania URI jest przeznaczony dla systemu iOS
Można bezpiecznie uruchomić aplikacje w niektórych scenariuszach logowania jednokrotnego, musisz utworzyć *identyfikator URI przekierowania* w określonym formacie. Identyfikator URI przekierowania jest używany, aby upewnić się, że tokeny powrócić do właściwej aplikacji, która wyświetlony monit o podanie ich.


Format systemu iOS dla przekierowania jest identyfikatora URI:

```
<app-scheme>://<bundle-id>
```

* **Schemat aplikacji** -to jest zarejestrowany w projekcie XCode. Jest jak inne aplikacje zadzwonić do Ciebie. Można znaleźć, to w pliku Info.plist -> adres URL identyfikatora -> typy adresu URL. Należy utworzyć jeden, jeśli nie masz jeszcze co najmniej jeden skonfigurowany.
* **Identyfikator pakietu** — jest to identyfikator pakietu omówionych w artykule "identity" w ustawieniach projektu XCode.

Przykład dla tego kodu Szybki Start: ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="2-register-the-directorysearcher-application"></a>2. Zarejestrować aplikację DirectorySearcher
Aby skonfigurować aplikację, aby uzyskać tokeny, należy najpierw zarejestrować ją w dzierżawie usługi Azure AD i udzielić jej uprawnienia dostępu do interfejsu API programu Azure AD Graph:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na górnym pasku kliknij konto. W obszarze **katalogu** wybierz dzierżawy usługi Active Directory, które chcesz zarejestrować aplikację.
3. Kliknij przycisk **wszystkie usługi** w okienku nawigacji po lewej stronie, a następnie wybierz **usługi Azure Active Directory**.
4. Kliknij przycisk **rejestracji aplikacji**, a następnie wybierz **Dodaj**.
5. Postępuj zgodnie z monitami, aby utworzyć nową **natywną aplikację kliencką**.
  * **Nazwa** aplikacji opisuje aplikacji dla użytkowników końcowych.
  * **Identyfikator Uri przekierowania** jest kombinacją schemat i ciąg, korzystającą z usługi Azure AD w celu zwracać odpowiedzi tokenu. Wprowadź wartość, która jest specyficzna dla aplikacji i opierają się na poprzedniej informacyjne URI przekierowania.
6. Po zakończeniu rejestracji usługi Azure AD przypisuje aplikacji identyfikatora aplikacji Ta wartość jest potrzebny w kolejnych sekcjach, dlatego skopiuj go na karcie aplikacji.
7. Z **ustawienia** wybierz pozycję **wymagane uprawnienia** , a następnie wybierz **Dodaj**. Wybierz **Microsoft Graph** jako interfejsu API, a następnie dodaj **Czytaj dane katalogu** uprawnienie w obszarze **delegowane uprawnienia**. Konfiguruje aplikację do zapytania interfejsu API programu Azure AD Graph dla użytkowników.

## <a name="3-install-and-configure-adal"></a>3. Instalowanie i konfigurowanie biblioteki ADAL
Teraz, gdy masz aplikacji w usłudze Azure AD, można zainstalować biblioteki ADAL i wpisz swój kod dotyczące tożsamości. Dotyczące biblioteki ADAL do komunikowania się z usługą Azure AD należy dostarczyć informacji o rejestracji aplikacji.

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

3. Teraz załadować podfile przy użyciu programu CocoaPods. Spowoduje to utworzenie nowego obszaru roboczego XCode, że zostały załadowane.

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

4. W projekcie szybkiego startu, otwórz plik plist `settings.plist`. Zastąp wartości elementów w sekcji do wartości, które zostały wprowadzone w portalu Azure. Kod odwołuje się do tych wartości, przy każdym użyciu biblioteki ADAL.
  * `tenant` Jest domeny dzierżawy usługi Azure AD, na przykład contoso.onmicrosoft.com.
  * `clientId` Jest identyfikator klienta aplikacji, który został skopiowany z portalu.
  * `redirectUri` Jest adres URL przekierowania, który został zarejestrowany w portalu.

## <a name="4-use-adal-to-get-tokens-from-azure-ad"></a>4. Aby uzyskać tokenów z usługi Azure AD, użyj biblioteki ADAL
Zasada podstawowa za ADAL jest, że zawsze, gdy Twoja aplikacja powinna tokenu dostępu, po prostu wywołuje completionBlock `+(void) getToken : `, a reszta biblioteki ADAL. 

1. W `QuickStart` otwarciu projektu `GraphAPICaller.m` i Znajdź `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` komentarz u góry. Jest to, gdy przekazujesz ADAL współrzędne za pośrednictwem CompletionBlock, aby komunikować się z usługą Azure AD i poinformuj go, jak pamięci podręcznej tokenów.

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

2. Teraz należy użyć tego tokenu wyszukać użytkowników na wykresie. Znajdź `// TODO: implement SearchUsersList` komentarza. Ta metoda zgłasza żądanie GET interfejsu API Azure AD Graph zapytania dla użytkowników, których nazwy UPN rozpoczyna się od danego wyszukiwanego. Aby wykonać zapytania interfejsu API Azure AD Graph, należy uwzględnić ' access_token ' w `Authorization` nagłówka żądania. Jest to, gdzie ADAL jest dostarczany.

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


3. Gdy aplikacja żąda token przez wywołanie metody `getToken(...)`, ADAL próbuje zwrócić token bez monitowania użytkownika o poświadczenia. Jeśli ADAL Określa, czy użytkownik powinien logować się do pobrania tokenu, go będą wyświetlane jest okno dialogowe dla logowania, zbieranie poświadczeń użytkownika, a następnie wróć tokenu po pomyślnym uwierzytelnieniu. Jeśli ADAL nie będzie mógł zwrócić token jakiegokolwiek powodu, zgłasza `AdalException`.

> [!Note] 
> `AuthenticationResult` Zawiera obiekt `tokenCacheStoreItem` obiektu, który może służyć do zbierania informacji, które może wymagać Twojej aplikacji. Z opcją szybkiego startu `tokenCacheStoreItem` służy do określania, czy uwierzytelnianie jest już wykonywane.
>
>

## <a name="5-build-and-run-the-application"></a>5. Kompilowanie i uruchamianie aplikacji
Gratulacje! Masz teraz działającą aplikację systemu iOS, która może uwierzytelniać użytkowników, bezpiecznie wywoływania interfejsów API sieci Web przy użyciu protokołu OAuth 2.0 i uzyskać podstawowe informacje o użytkowniku. Jeśli nie jest jeszcze nadszedł czas do wypełnienia dzierżawy z niektórych użytkowników. Uruchom aplikację Szybki Start, a następnie zaloguj się przy użyciu jednej z tych użytkowników. Wyszukiwać innych użytkowników, w oparciu o ich nazwy UPN. Zamknij aplikację, a następnie uruchom go ponownie. Zwróć uwagę, że sesja pozostanie niezmieniona.

Biblioteka ADAL można łatwo zastosować wszystkie te typowe funkcje tożsamości w aplikacji. Go zajmuje się pracy dirty, takich jak zarządzanie pamięci podręcznej, obsługa protokołu OAuth, przedstawiający użytkownika z interfejsu użytkownika do zalogowania się i odświeżanie wygasła tokenów. Tak naprawdę trzeba wiedzieć jednego wywołania interfejsu API, jest `getToken`.

Odwołania, ukończonych próbka (bez wartości konfiguracji) znajduje się na [GitHub](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip). 

## <a name="next-steps"></a>Kolejne kroki
Możesz teraz przejść do dodatkowe scenariusze. Można spróbować:

* [Zabezpieczanie interfejsu API za pomocą usługi Azure AD sieci Web Node.JS](active-directory-devquickstarts-webapi-nodejs.md)
* Dowiedz się [jak włączyć logowanie Jednokrotne wielu aplikacji w systemie iOS przy użyciu biblioteki ADAL](active-directory-sso-ios.md)  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

