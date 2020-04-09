---
title: Konfigurowanie sytego systemu w systemach macOS i iOS
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak skonfigurować logowanie jednokrotne w systemach macOS i iOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 25389348476552298ddb947ccb59acb8b3d5bc57
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881252"
---
# <a name="how-to-configure-sso-on-macos-and-ios"></a>Jak: Konfigurowanie sytego systemu w systemach macOS i iOS

Biblioteka uwierzytelniania firmy Microsoft (MSAL) dla komputerów macOS i iOS obsługuje logowanie jednokrotne między aplikacjami i przeglądarkami systemu macOS/iOS. W tym artykule omówiono następujące scenariusze samego przysło:

- [Ciche jednokrotne połączenie między wieloma aplikacjami](#silent-sso-between-apps)

Ten typ samego użytkownika działa między wieloma aplikacjami dystrybuowanymi przez tego samego dewelopera Apple. Zapewnia ciche logowania sytego (oznacza to, że użytkownik nie jest monitowany o poświadczenia) przez odczyt tokenów odświeżania napisanych przez inne aplikacje z pęku kluczy i wymieniając je na tokeny dostępu w trybie dyskretnym.  

- [SSO za pośrednictwem brokera uwierzytelniania](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> Ten przepływ nie jest dostępny w systemie macOS.

Firma Microsoft udostępnia aplikacje, nazywane brokerami, które umożliwiają rejestrowanie rejestracji w systemie SSO między aplikacjami różnych dostawców, o ile urządzenie przenośne jest zarejestrowane w usłudze Azure Active Directory (AAD). Ten typ rejestracji w tym celu wymaga zainstalowania aplikacji brokera na urządzeniu użytkownika.

- **SSO między MSAL i Safari**

SSO jest osiągany za pośrednictwem [KLASY ASWebAuthenticationSession.](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc) Używa istniejącego stanu logowania z innych aplikacji i przeglądarki Safari. Nie ogranicza się do aplikacji dystrybuowanych przez tego samego programistę Apple, ale wymaga pewnej interakcji z użytkownikiem.

Jeśli do logowania użytkowników używasz domyślnego widoku sieci Web w aplikacji, otrzymasz automatyczne logowanie logowanie między aplikacjami opartymi na msal i Safari. Aby dowiedzieć się więcej o widokach sieci Web, które obsługuje msal, odwiedź stronę [Dostosowywanie przeglądarek i webviews](customize-webviews.md).

> [!IMPORTANT]
> Ten typ wpisu SSO jest obecnie niedostępny w systemie macOS. MSAL na macOS obsługuje tylko WKWebView, który nie ma obsługi jedno i administratora w przeglądarce Safari. 

- **Ciche przysłowiowe między aplikacjami ADAL i MSAL macOS/iOS**

MSAL Objective-C obsługuje migrację i funkcję SSO za pomocą aplikacji opartych na języku ADAL Objective-C. Aplikacje muszą być dystrybuowane przez tego samego dewelopera Apple.

Aby uzyskać instrukcje dotyczące sso syg jako krzyżowego między aplikacjami ADAL i MSAL, zobacz [samoustawne między aplikacjami ADAL i MSAL.](sso-between-adal-msal-apps-macos-ios.md)

## <a name="silent-sso-between-apps"></a>Ciche przysłów YW między aplikacjami

Msal obsługuje udostępnianie aplikacji SSO za pośrednictwem grup dostępu do pęku kluczy systemu iOS.

Aby włączyć funkcję SSO w aplikacjach, należy wykonać następujące kroki, które zostały szczegółowo opisane poniżej:

1. Upewnij się, że wszystkie aplikacje używają tego samego identyfikatora klienta lub identyfikatora aplikacji.
1. Upewnij się, że wszystkie aplikacje mają ten sam certyfikat podpisywania firmy Apple, dzięki czemu można udostępniać breloki kluczy.
1. Poproś o te same uprawnienie do pęku kluczy dla każdej aplikacji.
1. Powiedz sdk MSAL o udostępnionym pęku kluczy, którego chcesz użyć, jeśli różni się od domyślnego.

### <a name="use-the-same-client-id-and-application-id"></a>Używanie tego samego identyfikatora klienta i identyfikatora aplikacji

Aby platforma tożsamości firmy Microsoft wiedziała, które aplikacje mogą współużytkować tokeny, te aplikacje muszą współużytkować ten sam identyfikator klienta lub identyfikator aplikacji. Jest to unikatowy identyfikator, który został podany podczas rejestracji pierwszej aplikacji w portalu.

Sposób, w jaki platforma tożsamości firmy Microsoft informuje aplikacje, które używają tego samego identyfikatora aplikacji od siebie, jest przez ich **identyfikatory URI przekierowania.** Każda aplikacja może mieć wiele identyfikatorów URI przekierowania zarejestrowanych w portalu dołączania. Każda aplikacja w pakiecie będzie miała inny identyfikator URI przekierowania. Przykład:

Przekierowanie identyfikatora URI aplikacji1:`msauth.com.contoso.mytestapp1://auth`  
Przekierowanie URI aplikacji2:`msauth.com.contoso.mytestapp2://auth`  
Przekierowanie identyfikatora URI aplikacji3:`msauth.com.contoso.mytestapp3://auth`  

> [!IMPORTANT]
> Format przekierowania uris musi być zgodny z formatem msal obsługuje, który jest udokumentowany w [MSAL Redirect Wymagania formatu URI](redirect-uris-ios.md#msal-redirect-uri-format-requirements).

### <a name="setup-keychain-sharing-between-applications"></a>Konfigurowanie współużytkowania pęku kluczy między aplikacjami

Zapoznaj się z artykułem Apple [Dodawanie możliwości,](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) aby włączyć udostępnianie pęku kluczy. Ważne jest, aby zdecydować, co ma być wywoływane pęk kluczy i dodać tę możliwość do wszystkich aplikacji, które będą zaangażowane w samouszeńca.

Jeśli uprawnienia są poprawnie skonfigurowane, w katalogu `entitlements.plist` projektu zostanie wyświetlony plik zawierający coś podobnego:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

#### <a name="add-a-new-keychain-group"></a>Dodawanie nowej grupy pęku kluczy

Dodaj nową grupę pęku **kluczy**do możliwości projektu . Grupa pęku kluczy powinna być:
* `com.microsoft.adalcache`w iOS 
* `com.microsoft.identity.universalstorage`w systemie macOS.

![przykład pęku kluczy](media/single-sign-on-macos-ios/keychain-example.png)

Aby uzyskać więcej informacji, zobacz [grupy pęku kluczy](howto-v2-keychain-objc.md).

## <a name="configure-the-application-object"></a>Konfigurowanie obiektu aplikacji

Po włączeniu uprawnienia do pęku kluczy w każdej z aplikacji i gotowości do `MSALPublicClientApplication` użycia jednocześnie skonfiguruj za pomocą grupy dostępu do pęku kluczy, jak w poniższym przykładzie:

Cel C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<my-client-id>"];
configuration.cacheConfig.keychainSharingGroup = @"my.keychain.group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:configuration error:&error];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<my-client-id>")
config.cacheConfig.keychainSharingGroup = "my.keychain.group"

do {
   let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
} catch let error as NSError {
  // handle error here
}
```

> [!WARNING]
> Po udostępnieniu pęku kluczy w aplikacjach każda aplikacja może usunąć użytkowników lub nawet wszystkie tokeny w aplikacji.
> Jest to szczególnie istotne, jeśli masz aplikacje, które opierają się na tokeny do pracy w tle.
> Udostępnianie pęku kluczy oznacza, że należy zachować szczególną ostrożność, gdy aplikacja używa microsoft tożsamości SDK usunąć operacje.

Gotowe. Pakiet SDK tożsamości firmy Microsoft będzie teraz udostępniać poświadczenia we wszystkich aplikacjach. Lista kont będzie również współużytkowana przez wystąpienia aplikacji.

## <a name="sso-through-authentication-broker-on-ios"></a>SSO za pośrednictwem brokera uwierzytelniania w iOS

Usługa MSAL zapewnia obsługę uwierzytelniania obsługiwanego przez brokera za pomocą programu Microsoft Authenticator. Microsoft Authenticator zapewnia sso dla urządzeń zarejestrowanych przez usługę AAD, a także pomaga aplikacji przestrzegać zasad dostępu warunkowego.

Poniższe kroki obejmują sposób włączania funkcji SSO przy użyciu brokera uwierzytelniania dla aplikacji:

1. Zarejestruj w info.plist formatu URI zgodnego z brokerem brokera. Format URI z kompatybilnym z brokerem to `msauth.<app.bundle.id>://auth`. Zamień '<app.bundle.id>'' identyfikatorem pakietu aplikacji. Przykład:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. Dodaj następujące schematy do info.plist `LSApplicationQueriesSchemes`aplikacji pod:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. Dodaj do pliku `AppDelegate.m` następujące elementy, aby obsłużyć wywołania zwrotne:

    Cel C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options
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

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [przepływach uwierzytelniania i scenariuszach aplikacji](authentication-flows-app-scenarios.md)