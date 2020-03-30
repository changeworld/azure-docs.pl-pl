---
title: Przewodnik po migracji ADAL do MSAL (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Poznaj różnice między protokołem MSAL dla systemu iOS/macOS a biblioteką uwierzytelniania usługi Azure AD dla objectivec (ADAL). ObjC) i jak przeprowadzić migrację do usługi MSAL dla systemu iOS/macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 6050bdc8c2600998b9804b04b62102e74612719f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085177"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>Migrowanie aplikacji do usługi MSAL dla systemu iOS i macOS

Biblioteka uwierzytelniania usługi Azure Active Directory[(ADAL Objective-C)](https://github.com/AzureAD/azure-activedirectory-library-for-objc)została utworzona do pracy z kontami usługi Azure Active Directory za pośrednictwem punktu końcowego w wersji 1.0.

Biblioteka uwierzytelniania firmy Microsoft dla systemu iOS i macOS (MSAL) jest zbudowana do pracy ze wszystkimi tożsamościami firmy Microsoft, takimi jak konta usługi Azure Active Directory (Azure AD), osobiste konta Microsoft i konta usługi Azure AD B2C za pośrednictwem platformy tożsamości firmy Microsoft (formalnie punktu końcowego usługi Azure AD AD 2.0).

Platforma tożsamości firmy Microsoft ma kilka kluczowych różnic w usłudze Azure Active Directory w wersji 1.0. W tym artykule przedstawiono te różnice i zawiera wskazówki dotyczące migracji aplikacji z usługi ADAL do msal.

## <a name="adal-and-msal-app-capability-differences"></a>Różnice w możliwościach aplikacji ADAL i MSAL

### <a name="who-can-sign-in"></a>Kto może się zalogować

* Usługa ADAL obsługuje tylko konta służbowe i szkolne — znane również jako konta usługi Azure AD.
* Usługa MSAL obsługuje osobiste konta Microsoft (MSA), takie jak Hotmail.com, Outlook.com i Live.com.
* Usługa MSAL obsługuje konta służbowe i szkolne oraz konta usługi Azure AD B2C.

### <a name="standards-compliance"></a>Zgodność z normami

* Punkt końcowy platformy tożsamości firmy Microsoft jest zgodny ze standardami OAuth 2.0 i OpenId Connect.

### <a name="incremental-and-dynamic-consent"></a>Zgoda przyrostowa i dynamiczna

* Punkt końcowy usługi Azure Active Directory w wersji 1.0 wymaga, aby wszystkie uprawnienia były deklarowane z wyprzedzeniem podczas rejestracji aplikacji. Oznacza to, że te uprawnienia są statyczne.
* Platforma tożsamości firmy Microsoft umożliwia dynamiczne żądanie uprawnień. Aplikacje mogą prosić o uprawnienia tylko w razie potrzeby i żądać więcej, ponieważ aplikacja ich potrzebuje.

Aby uzyskać więcej informacji na temat różnic między usługą Azure Active Directory w wersji 1.0 a platformą tożsamości firmy Microsoft, zobacz [Dlaczego należy aktualizować platformę tożsamości firmy Microsoft (w wersji 2.0)?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison).

## <a name="adal-and-msal-library-differences"></a>Różnice w bibliotece ADAL i MSAL

Publiczny interfejs API msal odzwierciedla kilka kluczowych różnic między usługą Azure AD w wersji 1.0 a platformą tożsamości firmy Microsoft.

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>MSALPublicClientApplication zamiast ADAuthenticationContext

`ADAuthenticationContext`jest pierwszym obiektem, który tworzy aplikacja ADAL. Reprezentuje wystąpienie ADAL. Aplikacje tworzą nowe `ADAuthenticationContext` wystąpienie dla każdej chmury usługi Azure Active Directory i dzierżawy (urzędu) kombinacji. To `ADAuthenticationContext` samo może służyć do uzyskania tokenów dla wielu publicznych aplikacji klienckich.

W msal, główna interakcja jest za pośrednictwem obiektu, `MSALPublicClientApplication` który jest wzorowany na [OAuth 2.0 Klienta publicznego](https://tools.ietf.org/html/rfc6749#section-2.1). Jedno wystąpienie `MSALPublicClientApplication` może służyć do interakcji z wieloma chmurami usługi AAD i dzierżawami, bez konieczności tworzenia nowego wystąpienia dla każdego urzędu. W przypadku większości `MSALPublicClientApplication` aplikacji wystarczy jedno wystąpienie.

### <a name="scopes-instead-of-resources"></a>Zakresy zamiast zasobów

W programie ADAL aplikacja musiała podać identyfikator `https://graph.microsoft.com` *zasobu,* który ma na celu uzyskanie tokenów z punktu końcowego usługi Azure Active Directory w wersji 1.0. Zasób można zdefiniować szereg zakresów lub oAuth2Permissions w manifeście aplikacji, który rozumie. Dzięki temu aplikacje klienckie żądały tokenów z tego zasobu dla określonego zestawu zakresów wstępnie zdefiniowanych podczas rejestracji aplikacji.

W msal zamiast identyfikatora pojedynczego zasobu aplikacje zapewniają zestaw zakresów na żądanie. Zakres jest identyfikatorem zasobu, po którym następuje nazwa uprawnień w zasobie/uprawnieniu formularza. Na przykład: `https://graph.microsoft.com/user.read`

Istnieją dwa sposoby, aby zapewnić zakresy w MSAL:

* Podaj listę wszystkich uprawnień, które są potrzebne aplikacjom. Przykład: 

    `@[@"https://graph.microsoft.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    W takim przypadku aplikacja `directory.read` żąda `directory.write` uprawnień i. Użytkownik zostanie poproszony o wyrażenie zgody na te uprawnienia, jeśli nie wyraził na nie zgody wcześniej dla tej aplikacji. Aplikacja może również otrzymać dodatkowe uprawnienia, na które użytkownik wyraził już zgodę dla aplikacji. Użytkownik zostanie poproszony tylko o wyrażenie zgody na nowe uprawnienia lub uprawnienia, które nie zostały przyznane.

* Zakres. `/.default`

Jest to wbudowany zakres dla każdej aplikacji. Odnosi się do statycznej listy uprawnień skonfigurowanych podczas rejestracji aplikacji. Jego zachowanie jest podobne `resource`do tego z . Może to być przydatne podczas migracji, aby upewnić się, że podobny zestaw zakresów i środowiska użytkownika jest zachowywany.

Aby użyć `/.default` zakresu, `/.default` dołącz do identyfikatora zasobu. Na przykład: `https://graph.microsoft.com/.default`. Jeśli zasób kończy`/`się ukośnikiem `/.default`( ), należy nadal dołączać , w tym wiodący`//`ukośnik do przodu, co powoduje, że zakres, który ma podwójne ukośnik do przodu ( ) w nim.

Więcej informacji na temat używania zakresu "/.default" można przeczytać [tutaj](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope)

### <a name="supporting-different-webview-types--browsers"></a>Obsługa różnych typów WebView & przeglądarkach

ADAL obsługuje tylko UIWebView/WKWebView dla systemu iOS i WebView dla systemu macOS. MsAL dla systemu iOS obsługuje więcej opcji wyświetlania zawartości sieci Web `UIWebView`podczas żądania kodu autoryzacji i nie obsługuje już; które mogą poprawić komfort i bezpieczeństwo użytkownika.

Domyślnie usługa MSAL w systemu iOS używa [aswebauthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc), który jest składnikiem internetowym, który firma Apple zaleca do uwierzytelniania na urządzeniach z systemem iOS 12+. Zapewnia korzyści z logowania jednokrotnego (SSO) dzięki udostępnianiu plików cookie między aplikacjami a przeglądarką Safari.

Można użyć innego składnika sieci Web w zależności od wymagań aplikacji i środowiska użytkownika końcowego, które chcesz. Więcej opcji można znaleźć [w obsługiwanych typach widoków sieci Web.](customize-webviews.md)

Podczas migracji z usługi ADAL `WKWebView` do usługi MSAL zapewnia użytkownikowi środowisko najbardziej podobne do usługi ADAL w systemach iOS i macOS. Zachęcamy do migracji `ASWebAuthenticationSession` do systemu iOS, jeśli to możliwe. W przypadku systemu macOS `WKWebView`zachęcamy do korzystania z programu .

### <a name="account-management-api-differences"></a>Różnice interfejsu API zarządzania kontem

Po wywołaniu `acquireToken()` metody ADAL lub `acquireTokenSilent()` `ADUserInformation` , otrzymasz obiekt zawierający `id_token` listę oświadczeń z tego, który reprezentuje konto jest uwierzytelniane. Ponadto `ADUserInformation` zwraca na `userId` podstawie `upn` oświadczenia. Po początkowym interaktywnym nabyciu tokenu usługa ADAL oczekuje, że deweloper udostępni `userId` wszystkie połączenia niemące.

Usługa ADAL nie udostępnia interfejsu API do pobierania znanych tożsamości użytkowników. Polega na aplikacji, aby zapisać i zarządzać tymi kontami.

Usługa MSAL udostępnia zestaw interfejsów API do listy wszystkich kont znanych msal bez konieczności uzyskiwania tokenu.

Podobnie jak ADAL, MSAL zwraca informacje o `id_token`koncie, które przechowuje listę oświadczeń z pliku . Jest to część `MSALAccount` obiektu wewnątrz `MSALResult` obiektu.

Usługa MSAL udostępnia zestaw interfejsów API do usuwania kont, co powoduje, że usunięte konta są niedostępne dla aplikacji. Po usunięciu konta późniejsze wywołania pozyskiwania tokenów będą monitować użytkownika o akwizycję tokenu interaktywnego. Usunięcie konta dotyczy tylko aplikacji klienckiej, która go uruchomiła i nie usuwa konta z innych aplikacji uruchomionych na urządzeniu lub z przeglądarki systemowej. Gwarantuje to, że użytkownik nadal ma środowisko logowania przy logowaniu na urządzeniu nawet po wylogowaniu się z poszczególnych aplikacji.

Ponadto msal zwraca również identyfikator konta, który może służyć do żądania tokenu dyskretnie później. Jednak identyfikator konta (dostępny `identifier` za pośrednictwem `MSALAccount` właściwości w obiekcie) nie jest wyświetlany i nie można zakładać, w jakim formacie jest, ani nie należy próbować go interpretować ani analizować.

### <a name="migrating-the-account-cache"></a>Migrowanie pamięci podręcznej konta

Podczas migracji z usługi ADAL aplikacje zwykle `userId`przechowują ADAL, `identifier` które nie mają wymaganych przez MSAL. Jako krok migracji jednorazowej aplikacja może wysyłać zapytania do konta MSAL przy użyciu identyfikatora użytkownika usługi ADAL za pomocą następującego interfejsu API:

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Ten interfejs API odczytuje pamięć podręczną msal i ADAL, aby znaleźć konto według identyfikatora użytkownika ADAL (UPN).

Jeśli konto zostanie znalezione, deweloper powinien użyć konta do cichego pozyskiwania tokenów. Pierwsze ciche pozyskiwanie tokenów skutecznie uaktualni konto, a deweloper otrzyma identyfikator konta`identifier`zgodnego z MSAL w wyniku MSAL ( ). Następnie należy `identifier` używać tylko do wyszukiwania kont przy użyciu następującego interfejsu API:

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Chociaż jest możliwe, aby kontynuować `userId` korzystanie z ADAL dla `userId` wszystkich operacji w MSAL, ponieważ opiera się na upn, to podlega wielu ograniczeniom, które powodują złe środowisko użytkownika. Na przykład, jeśli nazwa UPN ulegnie zmianie, użytkownik musi zalogować się ponownie. Zalecamy, aby wszystkie aplikacje `identifier` korzystały z konta nieekserwalnego dla wszystkich operacji.

Dowiedz się więcej o [migracji stanu pamięci podręcznej](sso-between-adal-msal-apps-macos-ios.md).

### <a name="token-acquisition-changes"></a>Zmiany w pozyskiwaniu tokenów

MSAL wprowadza kilka zmian wywołania nabycia tokenu:

* Podobnie jak `acquireTokenSilent` ADAL, zawsze powoduje ciche żądanie.
* W przeciwieństwie `acquireToken` do usługi ADAL, zawsze powoduje użytkownika zasysanych interfejsu użytkownika za pośrednictwem widoku sieci web lub aplikacji Microsoft Authenticator. W zależności od stanu logowania przy użyciu logowania do dokumentów logowania w widoku sieci Web/microsoftu uwierzytelniającego użytkownik może zostać poproszony o wprowadzenie poświadczeń.
* W `acquireToken` ADAL, `AD_PROMPT_AUTO` z pierwszym próbuje cichego nabycia tokenu i pokazuje tylko interfejsu użytkownika, jeśli żądanie niedyskretne nie powiedzie się. W msal tej logiki można osiągnąć `acquireTokenSilent` przez `acquireToken` pierwsze wywołanie i tylko wywołanie, jeśli ciche nabycie nie powiedzie się. Dzięki temu deweloperzy mogą dostosować środowisko użytkownika przed rozpoczęciem interaktywnego pozyskiwania tokenów.

### <a name="error-handling-differences"></a>Różnice w obsłudze błędów

MSAL zapewnia większą przejrzystość między błędami, które mogą być obsługiwane przez aplikację i te, które wymagają interwencji przez użytkownika. Istnieje ograniczona liczba błędów, które deweloper musi obsługiwać:

* `MSALErrorInteractionRequired`: Użytkownik musi wykonać interaktywne żądanie. Może to być spowodowane z różnych powodów, takich jak wygasła sesja uwierzytelniania, zasady dostępu warunkowego uległy zmianie, token odświeżania wygasł lub został odwołany, nie ma prawidłowych tokenów w pamięci podręcznej i tak dalej.
* `MSALErrorServerDeclinedScopes`: Żądanie nie zostało w pełni ukończone, a niektóre zakresy nie uzyskały dostępu. Może to być spowodowane przez użytkownika odrzucający zgodę na jeden lub więcej zakresów.

Obsługa wszystkich innych błędów na [ `MSALError` liście](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128) jest opcjonalna. Można użyć informacji w tych błędów, aby poprawić środowisko użytkownika.

Zobacz [obsługa wyjątków i błędów przy użyciu usługi MSAL,](msal-handling-exceptions.md) aby uzyskać więcej informacji na temat obsługi błędów MSAL.

### <a name="broker-support"></a>Pomoc techniczna dla brokerów

Usługa MSAL, począwszy od wersji 0.3.0, zapewnia obsługę uwierzytelniania obsługiwanego przez brokera przy użyciu aplikacji Microsoft Authenticator. Program Microsoft Authenticator umożliwia również obsługę scenariuszy dostępu warunkowego. Przykłady scenariuszy dostępu warunkowego obejmują zasady zgodności urządzeń, które wymagają od użytkownika zarejestrowania urządzenia za pośrednictwem usługi Intune lub zarejestrowania się w usłudze AAD w celu uzyskania tokenu. I zasady zarządzania aplikacjami mobilnymi (MAM), które wymagają potwierdzenia zgodności, zanim aplikacja może uzyskać token.

Aby włączyć brokera dla aplikacji:

1. Zarejestruj format identyfikatora URI zgodnego z brokerem dla aplikacji. Format URI przekierowania `msauth.<app.bundle.id>://auth`zgodny z brokerem to . Zamień `<app.bundle.id>` identyfikator pakietu aplikacji. Jeśli przeprowadzasz migrację z usługi ADAL, a twoja aplikacja była już zdolna do brokera, nie musisz nic dodatkowego zrobić. Poprzedni identyfikator URI przekierowania jest w pełni zgodny z programem MSAL, dzięki czemu można przejść do kroku 3.

2. Dodaj schemat URI przekierowania aplikacji do pliku info.plist. W przypadku domyślnego identyfikatora URI `msauth.<app.bundle.id>`przekierowania msal format jest . Przykład:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. Dodaj następujące schematy do info.plist aplikacji pod LSApplicationQueriesSchemes:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

4. Dodaj do pliku AppDelegate.m następujące elementy do obsługi wywołań zwrotnych: Objective-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift:
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```

### <a name="business-to-business-b2b"></a>Firma-firma (B2B)

W ADAL tworzysz oddzielne `ADAuthenticationContext` wystąpienia dla każdej dzierżawy, dla której aplikacja żąda tokenów. Nie jest to już wymagane w MSAL. W msal, można utworzyć pojedyncze wystąpienie `MSALPublicClientApplication` i używać go dla dowolnej chmury IAD i organizacji, określając inny urząd acquireToken i acquireTokenSilent wywołań.

## <a name="sso-in-partnership-with-other-sdks"></a>SSO we współpracy z innymi SDK

Usługa MSAL dla systemu iOS może osiągnąć sytą pamięć SSO za pośrednictwem ujedlejerytowanej pamięci podręcznej z następującymi zestawami SDK:

- ADAL Objective-C 2.7.x+
- MSAL.NET dla platformy Xamarin 2.4.x+
- ADAL.NET dla platformy Xamarin 4.4.x+

SSO jest osiągane za pośrednictwem udostępniania pęku kluczy systemu iOS i jest dostępne tylko między aplikacjami opublikowanymi z tego samego konta Apple Developer.

Udostępnianie jednodzielnich jednokładów jednoczących dla systemu iOS jest jedynym cichym typem jednodzielczym.

W systemie macOS msal może osiągnąć sytą dla innych aplikacji MSAL dla systemu iOS i macOS oraz aplikacji opartych na celu ADAL.

MSAL na iOS obsługuje również dwa inne typy sytą:

* SSO za pośrednictwem przeglądarki internetowej. MSAL dla iOS obsługuje `ASWebAuthenticationSession`, który zapewnia SSO za pośrednictwem plików cookie współużytkowane przez inne aplikacje na urządzeniu, a w szczególności w przeglądarce Safari.
* Dane syt/r za pośrednictwem brokera uwierzytelniania. Na urządzeniu z systemem iOS program Microsoft Authenticator działa jako broker uwierzytelniania. Może przestrzegać zasad dostępu warunkowego, takich jak wymaganie zgodnego urządzenia i zapewnia sytuowanie typu SSO dla zarejestrowanych urządzeń. Msal SDK począwszy od wersji 0.3.0 domyślnie obsługuje brokera.

## <a name="intune-mam-sdk"></a>Wbudowany sdk usługi ŁOG usługi Intune

Zestaw [SDK usługi Intune MAM](https://docs.microsoft.com/intune/app-sdk-get-started) obsługuje protokół MSAL dla systemu iOS, począwszy od wersji [11.1.2](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2)

## <a name="msal-and-adal-in-the-same-app"></a>MSAL i ADAL w tej samej aplikacji

ADAL w wersji 2.7.0 i powyżej, nie może współistnieć z MSAL w tej samej aplikacji. Głównym powodem jest ze względu na wspólny kod współużytkowany podmoduł. Ponieważ Objective-C nie obsługuje obszarów nazw, jeśli dodasz do aplikacji struktury ADAL i MSAL, będą dwa wystąpienia tej samej klasy. Nie ma żadnej gwarancji, dla której zostanie pobrany w czasie wykonywania. Jeśli oba sdks są przy użyciu tej samej wersji klasy powodującej konflikt, aplikacja może nadal działać. Jednak jeśli jest to inna wersja, aplikacja może wystąpić nieoczekiwane awarie, które są trudne do zdiagnozowania.

Uruchamianie ADAL i MSAL w tej samej aplikacji produkcyjnej nie jest obsługiwane. Jeśli jednak dopiero testujesz i migrujesz użytkowników z ADAL Objective-C do MSAL dla systemu iOS i macOS, możesz kontynuować korzystanie z [ADAL Objective-C 2.6.10](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10). Jest to jedyna wersja, która działa z MSAL w tej samej aplikacji. Nie będzie żadnych nowych aktualizacji funkcji dla tej wersji ADAL, więc powinny być używane tylko do celów migracji i testowania. Twoja aplikacja nie powinna polegać na ADAL i MSAL współistnienia długoterminowego.

Współistnienie ADAL i MSAL w tej samej aplikacji nie jest obsługiwane.
Współistnienie ADAL i MSAL między wieloma aplikacjami jest w pełni obsługiwane.

## <a name="practical-migration-steps"></a>Praktyczne kroki migracji

### <a name="app-registration-migration"></a>Migracja rejestracji aplikacji

Nie trzeba zmieniać istniejącej aplikacji AAD, aby przełączyć się na msal i włączyć konta usługi AAD. Jeśli jednak aplikacja oparta na adal nie obsługuje uwierzytelniania opartego na brokerach, przed przejściem do usługi MSAL należy zarejestrować nowy identyfikator URI przekierowania dla aplikacji.

Identyfikator URI przekierowania powinien `msauth.<app.bundle.id>://auth`być w tym formacie: . Zamień `<app.bundle.id>` identyfikator pakietu aplikacji. Określ identyfikator URI przekierowania w [witrynie Azure portal](https://aka.ms/MobileAppReg).

Tylko w przypadku systemu iOS, aby obsługiwać uwierzytelnianie oparte na cert, dodatkowy identyfikator URI `msauth://code/<broker-redirect-uri-in-url-encoded-form>`przekierowania musi być zarejestrowany w aplikacji i w witrynie Azure w następującym formacie: . Na przykład: `msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth`

Zalecamy, aby wszystkie aplikacje rejestrować zarówno przekierowanie identyfikatorów URI.

Jeśli chcesz dodać obsługę przyrostowej zgody, wybierz interfejsy API i uprawnienia, do których aplikacja jest skonfigurowana tak, aby żądać dostępu do rejestracji aplikacji na karcie **Uprawnienia interfejsu API.**

Jeśli przeprowadzasz migrację z usługi ADAL i chcesz obsługiwać zarówno konta AAD, jak i MSA, istniejąca rejestracja aplikacji musi zostać zaktualizowana w celu obsługi obu tych kont. Firma Microsoft nie zaleca aktualizowania istniejącej aplikacji produkcyjnej w celu obsługi zarówno usługi AAD, jak i MSA od razu. Zamiast tego należy utworzyć inny identyfikator klienta, który obsługuje zarówno usługi AAD i MSA do testowania, a po sprawdzeniu, że wszystkie scenariusze działają, zaktualizuj istniejącą aplikację.

### <a name="add-msal-to-your-app"></a>Dodawanie usługi MSAL do aplikacji

Zestaw SDK MSAL można dodać do aplikacji za pomocą narzędzia do zarządzania preferowanym pakietem. Zobacz [szczegółowe instrukcje tutaj](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation).

### <a name="update-your-apps-infoplist-file"></a>Aktualizowanie pliku Info.plist aplikacji

Tylko w systemie iOS dodaj schemat URI przekierowania aplikacji do pliku info.plist. W przypadku aplikacji zgodnych z brokerem ADAL powinno już tam być. Domyślny schemat URI przekierowania MSAL `msauth.<app.bundle.id>`będzie w formacie: .  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

Dodaj następujące schematy do info.plist `LSApplicationQueriesSchemes`aplikacji pod .

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>Aktualizowanie kodu AppDelegate

Tylko w systemie iOS dodaj do pliku AppDelegate.m następujące elementy:

Cel C:

```objc
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
{
    return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```

**Jeśli używasz Xcode 11,** należy umieścić wywołania `SceneDelegate` zwrotnego MSAL do pliku zamiast.
Jeśli obsługujesz zarówno UISceneDelegate i UIApplicationDelegate dla zgodności ze starszymi systemami iOS, wywołania zwrotnego MSAL należy umieścić w obu plikach.

Cel C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift:

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

Dzięki temu MSAL do obsługi odpowiedzi od brokera i składnika sieci web.
Nie było to konieczne w ADAL, ponieważ automatycznie "swizzled" metody delegowania aplikacji. Dodanie go ręcznie jest mniej podatne na błędy i daje aplikacji większą kontrolę.

### <a name="enable-token-caching"></a>Włącz buforowanie tokenów

Domyślnie usługa MSAL buforuje tokeny aplikacji w pęku kluczy systemu iOS lub macOS. 

Aby włączyć buforowanie tokenów:
1. Upewnij się, że aplikacja jest prawidłowo podpisana
2. Przejdź do **karty** > Ustawienia projektu Xcode >**Włącz udostępnianie pęku kluczy**
3. Kliknij **+** i wprowadź następujący wpis **Grupy pęku kluczy:** `com.microsoft.adalcache` 3.a Dla systemu iOS, wprowadź 3.b Dla macOS enter`com.microsoft.identity.universalstorage`

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>Tworzenie msalpublicClientApplication i przełączyć się do jego acquireToken i nabyćTokeSilent połączeń

Można utworzyć `MSALPublicClientApplication` przy użyciu następującego kodu:

Cel C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
    
MSALPublicClientApplication *application =
[[MSALPublicClientApplication alloc] initWithConfiguration:configuration
                                                     error:&error];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
            
} catch let error as NSError {
  // handle error here
}
```

Następnie zadzwoń do interfejsu API zarządzania kontem, aby sprawdzić, czy w pamięci podręcznej znajdują się konta:

Cel C:

```objc
NSString *accountIdentifier = nil /*previously saved MSAL account identifier */;
NSError *error = nil;
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:&error];
```

Swift:

```swift
// definitions that need to be initialized
let application: MSALPublicClientApplication!
let accountIdentifier: String! /*previously saved MSAL account identifier */

do {
  let account = try application.account(forIdentifier: accountIdentifier)
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



lub przeczytaj wszystkie rachunki:

Cel C:

```objc
NSError *error = nil;
NSArray<MSALAccount *> *accounts = [application allAccounts:&error];
```

Swift:

```swift
let application: MSALPublicClientApplication!
do {
  let accounts = try application.allAccounts()
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



Jeśli zostanie znalezione konto, zadzwoń `acquireTokenSilent` do interfejsu API msal:

Cel C:

```objc
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"<your-resource-here>/.default"] account:account];
    
[application acquireTokenSilentWithParameters:silentParameters
                              completionBlock:^(MSALResult *result, NSError *error)
{
    if (result)
    {
        NSString *accessToken = result.accessToken;
        // Use your token
    }
    else
    {
        // Check the error
        if ([error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
        {
            // Interactive auth will be required
        }
            
        // Other errors may require trying again later, or reporting authentication problems to the user
    }
}];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let account: MSALAccount!
        
let silentParameters = MSALSilentTokenParameters(scopes: ["<your-resource-here>/.default"], 
                                                 account: account)
application.acquireTokenSilent(with: silentParameters) {
  (result: MSALResult?, error: Error?) in
  if let accessToken = result?.accessToken {
     // use accessToken
  }
  else {
    // Check the error
    guard let error = error else {
      assert(true, "callback should contain a valid result or error")
      return
    }
    
    let nsError = error as NSError
    if (nsError.domain == MSALErrorDomain
        && nsError.code == MSALError.interactionRequired.rawValue) {
      // Interactive auth will be required
    }
                
    // Other errors may require trying again later, or reporting authentication problems to the user
  }
}
```



## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [przepływach uwierzytelniania i scenariuszach aplikacji](authentication-flows-app-scenarios.md)
