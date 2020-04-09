---
title: Używanie identyfikatorów URI przekierowania z usługą MSAL (iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o różnicach między biblioteką uwierzytelniania firmy Microsoft dla języka ObjectiveC (MSAL dla systemu iOS i macOS) a biblioteką uwierzytelniania usługi Azure AD dla języka ObjectiveC (ADAL). ObjC) i jak migrować między nimi.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: jak
ms.custom: aaddev
ms.openlocfilehash: 1291563a39e3cf3acd4b343302be8b150bf794ca
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883512"
---
# <a name="using-redirect-uris-with-the-microsoft-authentication-library-for-ios-and-macos"></a>Używanie identyfikatorów URI przekierowania z biblioteką uwierzytelniania firmy Microsoft dla systemu iOS i macOS

Gdy użytkownik uwierzytelnia się, usługa Azure Active Directory (Azure AD) wysyła token do aplikacji przy użyciu identyfikatora URI przekierowania zarejestrowanego w aplikacji usługi Azure AD.

Biblioteka uwierzytelniania firmy Microsoft (MSAL) wymaga zarejestrowania identyfikatora URI przekierowania w aplikacji usługi Azure AD w określonym formacie. Msal używa domyślnego identyfikatora URI przekierowania, jeśli go nie określisz. Format to `msauth.[Your_Bundle_Id]://auth`.

Domyślny format identyfikatora URI przekierowania działa w przypadku większości aplikacji i scenariuszy, w tym uwierzytelniania brokerskiego i widoku sieci Web systemu. W miarę możliwości użyj formatu domyślnego.

Jednak może być konieczna zmiana identyfikatora URI przekierowania dla zaawansowanych scenariuszy, jak opisano poniżej.

## <a name="scenarios-that-require-a-different-redirect-uri"></a>Scenariusze wymagające innego identyfikatora URI przekierowania

### <a name="cross-app-single-sign-on-sso"></a>Logowanie jednokrotne między aplikacjami (Logowanie jednokrotne)

Aby platforma Microsoft Identity mogła udostępniać tokeny między aplikacjami, każda aplikacja musi mieć ten sam identyfikator klienta lub identyfikator aplikacji. Jest to unikatowy identyfikator podany podczas rejestracji aplikacji w portalu (nie identyfikator pakietu aplikacji, który rejestrujesz na aplikację w usłudze Apple).

Identyfikatory URI przekierowania muszą być różne dla każdej aplikacji dla systemu iOS. Dzięki temu usługa tożsamości firmy Microsoft jednoznacznie identyfikować różne aplikacje, które współużytkuje identyfikator aplikacji. Każda aplikacja może mieć wiele identyfikatorów URI przekierowania zarejestrowanych w witrynie Azure portal. Każda aplikacja w pakiecie będzie miała inny identyfikator URI przekierowania. Przykład:

Biorąc pod uwagę następującą rejestrację aplikacji w witrynie Azure portal:

    Client ID: ABCDE-12345 (this is a single client ID)
    RedirectUris: msauth.com.contoso.app1://auth, msauth.com.contoso.app2://auth, msauth.com.contoso.app3://auth

App1 używa `msauth.com.contoso.app1://auth` przekierowania App2 używa `msauth.com.contoso.app2://auth` App3 używa`msauth.com.contoso.app1://auth`

### <a name="migrating-from-adal-to-msal"></a>Migracja z usługi ADAL do usługi MSAL

Podczas migracji kodu, który używał biblioteki uwierzytelniania usługi Azure AD (ADAL) do usługi MSAL, może być już skonfigurowany identyfikator URI przekierowania dla aplikacji. Możesz nadal używać tego samego identyfikatora URI przekierowania tak długo, jak aplikacja ADAL została skonfigurowana do obsługi scenariuszy obsługiwanych przez brokerów, a identyfikator URI przekierowania spełnia wymagania dotyczące formatu URI przekierowania msal.

## <a name="msal-redirect-uri-format-requirements"></a>Wymagania dotyczące formatu formatu iuri przekierowania usługi MSAL

* Identyfikator URI przekierowania MSAL musi być w formularzu`<scheme>://host`

    Gdzie `<scheme>` jest unikatowy ciąg, który identyfikuje aplikację. Jest on oparty przede wszystkim na identyfikatorze pakietu aplikacji, aby zagwarantować unikatowość. Na przykład, jeśli identyfikator pakietu aplikacji `com.contoso.myapp`jest, przekierowanie identyfikator URI `msauth.com.contoso.myapp://auth`będzie w formie: .

    Jeśli przeprowadzasz migrację z adal, identyfikator URI przekierowania prawdopodobnie będzie miał ten format: `<scheme>://[Your_Bundle_Id]`, gdzie `scheme` jest unikatowy ciąg. Ten format będzie nadal działać podczas korzystania z msal.

* `<scheme>`musi być zarejestrowana w info.plist `CFBundleURLTypes > CFBundleURLSchemes`aplikacji pod .  W tym przykładzie Info.plist został otwarty jako kod źródłowy:

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>msauth.[BUNDLE_ID]</string>
            </array>
        </dict>
    </array>
    ```
    

Msal sprawdzi, czy przekierowanie identyfikatora URI rejestruje się poprawnie i zwraca błąd, jeśli nie jest.
    
* Jeśli chcesz używać łączy uniwersalnych jako identyfikatora URI przekierowania, `<scheme>` musi być `https` i nie musi być zadeklarowany w `CFBundleURLSchemes`. Zamiast tego skonfiguruj aplikację i domenę zgodnie z instrukcjami firmy Apple w [usłudze Universal Links for Developers](https://developer.apple.com/ios/universal-links/) i wywołaj `handleMSALResponse:sourceApplication:` `MSALPublicClientApplication` metodę, gdy aplikacja jest otwierana za pośrednictwem łącza uniwersalnego.

## <a name="use-a-custom-redirect-uri"></a>Korzystanie z niestandardowego identyfikatora URI przekierowania

Aby użyć niestandardowego identyfikatora `redirectUri` URI `MSALPublicClientApplicationConfig` przekierowania, `MSALPublicClientApplication` należy przekazać parametr do i przekazać ten obiekt do podczas inicjowania obiektu. Jeśli identyfikator URI przekierowania jest nieprawidłowy, inicjator zwróci `nil` i `redirectURIError`ustawi dodatkowe informacje.  Przykład:

Cel C:

```objc
MSALPublicClientApplicationConfig *config =
        [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                        redirectUri:@"your-redirect-uri"
                                                        authority:authority];
NSError *redirectURIError;
MSALPublicClientApplication *application =
        [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&redirectURIError];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: authority)
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```



## <a name="handle-the-url-opened-event"></a>Obsługa zdarzenia otwartego adresu URL

Aplikacja powinna wywołać MSAL po otrzymaniu odpowiedzi za pośrednictwem schematów adresów URL lub uniwersalnych linków. Wywołanie `handleMSALResponse:sourceApplication:` `MSALPublicClientApplication` metody, gdy aplikacja jest otwarta. Oto przykład schematów niestandardowych:

Cel C:

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```



## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [przepływach uwierzytelniania i scenariuszach aplikacji](authentication-flows-app-scenarios.md)
