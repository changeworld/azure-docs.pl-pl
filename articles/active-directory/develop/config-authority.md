---
title: Konfigurowanie dostawców tożsamości (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak korzystać z różnych urzędów, takich jak B2C, suwerenne chmury i użytkownicy-goście, z msal dla systemu iOS i macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 4810de772e44be22ee5bd4a9fb6ef0ef756e62f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085207"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>Jak: Konfigurowanie usługi MSAL dla systemu iOS i macOS do używania różnych dostawców tożsamości

W tym artykule pokazano, jak skonfigurować aplikację biblioteki uwierzytelniania firmy Microsoft dla systemu iOS i macOS (MSAL) dla różnych urzędów, takich jak usługa Azure Active Directory (Azure AD), Business-to-Consumer (B2C), chmury suwerenne i użytkownicy-goście.  W tym artykule można ogólnie myśleć o urzędzie jako dostawcy tożsamości.

## <a name="default-authority-configuration"></a>Domyślna konfiguracja urzędu

`MSALPublicClientApplication`jest skonfigurowany z domyślnym `https://login.microsoftonline.com/common`adresem URL urzędu , który jest odpowiedni dla większości scenariuszy usługi Azure Active Directory (AAD). Jeśli nie wdrażasz zaawansowanych scenariuszy, takich jak chmury krajowe lub pracujesz z B2C, nie musisz go zmieniać.

> [!NOTE]
> Nowoczesne uwierzytelnianie przy użyciu usług federacyjnych Active Directory jako dostawcy tożsamości (ADFS) nie jest obsługiwane (szczegółowe informacje można znaleźć w [usłudze ADFS for Developers).](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios) Usługi ADFS są obsługiwane przez federację.

## <a name="change-the-default-authority"></a>Zmienianie uprawnienia domyślnego

W niektórych scenariuszach, takich jak business-to-consumer (B2C), może być konieczna zmiana domyślnego urzędu.

### <a name="b2c"></a>B2C

Do pracy z usługą B2C [biblioteka uwierzytelniania firmy Microsoft (MSAL)](reference-v2-libraries.md) wymaga innej konfiguracji urzędu. MSAL rozpoznaje jeden format adresu URL urzędu jako B2C sam. Rozpoznawanym formatem urzędu `https://<host>/tfp/<tenant>/<policy>`B2C `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy`jest na przykład . Można jednak również użyć innych obsługiwanych adresów URL urzędu B2C, deklarując jawnie urząd jako urząd B2C.

Aby obsługiwać dowolny format adresu URL `MSALB2CAuthority` dla B2C, można ustawić za pomocą dowolnego adresu URL, takiego jak ten:

Obiektowy C
```objc
NSURL *authorityURL = [NSURL URLWithString:@"arbitrary URL"];
MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
```
Swift
```swift
guard let authorityURL = URL(string: "arbitrary URL") else {
    // Handle error
    return
}
let b2cAuthority = try MSALB2CAuthority(url: authorityURL)
```

Wszystkie urzędy B2C, które nie używają domyślnego formatu urzędu B2C, muszą być zadeklarowane jako znane władze.

Dodaj każdy inny organ B2C do listy znanych organów, nawet jeśli władze różnią się tylko polityką.

Obiektowy C
```objc
MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:b2cAuthority];
b2cApplicationConfig.knownAuthorities = @[b2cAuthority];
```
Swift
```swift
let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)
b2cApplicationConfig.knownAuthorities = [b2cAuthority]
```

Gdy aplikacja żąda nowych zasad, adres URL urzędu musi zostać zmieniony, ponieważ adres URL urzędu jest inny dla każdej zasady. 

Aby skonfigurować aplikację `@property MSALAuthority *authority` B2C, ustaw `MSALB2CAuthority` z `MSALPublicClientApplicationConfig` wystąpieniem `MSALPublicClientApplication`w przed utworzeniem , w ten sposób:

Obiektowy C
```ObjC
    // Create B2C authority URL
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy"];
    
    MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
    if (!b2cAuthority)
    {
        // Handle error
        return;
    }
    
    // Create MSALPublicClientApplication configuration
    MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                                   initWithClientId:@"your-client-id"
                                                                   redirectUri:@"your-redirect-uri"
                                                                   authority:b2cAuthority];

    // Initialize MSALPublicClientApplication
    MSALPublicClientApplication *b2cApplication =
    [[MSALPublicClientApplication alloc] initWithConfiguration:b2cApplicationConfig error:&error];
    
    if (!b2cApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    // Create B2C authority URL
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy") else {
        // Handle error
        return
    }
    let b2cAuthority = try MSALB2CAuthority(url: authorityURL)

    // Create MSALPublicClientApplication configuration
    let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)

    // Initialize MSALPublicClientApplication
    let b2cApplication = try MSALPublicClientApplication(configuration: b2cApplicationConfig)
} catch {
    // Handle error
}
```

### <a name="sovereign-clouds"></a>Suwerenne chmury

Jeśli aplikacja działa w suwerennej chmurze, może być `MSALPublicClientApplication`konieczna zmiana adresu URL urzędu w pliku . W poniższym przykładzie ustawia adres URL urzędu do pracy z niemiecką chmurą usługi AAD:

Obiektowy C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.de/common"];
    MSALAuthority *sovereignAuthority = [MSALAuthority authorityWithURL:authorityURL error:&authorityError];
    
    if (!sovereignAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:sovereignAuthority];
    
    
    MSALPublicClientApplication *sovereignApplication = [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    
    if (!sovereignApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.de/common") else {
        //Handle error
        return
    }
    let sovereignAuthority = try MSALAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: sovereignAuthority)
            
    let sovereignApplication = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

Może być konieczne przekazanie różnych zakresów do każdej suwerennej chmury. Zakresy do wysłania zależy od zasobu, którego używasz. Na przykład można `"https://graph.microsoft.com/user.read"` używać w chmurze na całym świecie i `"https://graph.microsoft.de/user.read"` w chmurze niemieckiej.

### <a name="signing-a-user-into-a-specific-tenant"></a>Podpisywanie użytkownika do określonej dzierżawy

Gdy adres URL urzędu `"login.microsoftonline.com/common"`jest ustawiony na , użytkownik zostanie zalogowany do dzierżawy domowej. Jednak niektóre aplikacje mogą wymagać zalogowania użytkownika do innej dzierżawy, a niektóre aplikacje działają tylko z jedną dzierżawą.

Aby zalogować użytkownika do określonej `MSALPublicClientApplication` dzierżawy, należy skonfigurować z określonym urzędem. Przykład:

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

Poniżej przedstawiono sposób logowania użytkownika do określonej dzierżawy:

Obiektowy C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4"];
    MSALAADAuthority *tenantedAuthority = [[MSALAADAuthority alloc] initWithURL:authorityURL error:&authorityError];
    
    if (!tenantedAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:tenantedAuthority];
    
    MSALPublicClientApplication *application =
    [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    if (!application)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4") else {
        //Handle error
        return
    }    
    let tenantedAuthority = try MSALAADAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: tenantedAuthority)
            
    let application = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

## <a name="supported-authorities"></a>Wspierane organy

### <a name="msalauthority"></a>MSALAuthority (Władza MSAL)

Klasa `MSALAuthority` jest podstawową klasą abstrakcyjną dla klas uprawnień MSAL. Nie próbuj tworzyć wystąpienia za `alloc` pomocą `new`lub . Zamiast tego należy utworzyć jedną z jego podklas bezpośrednio (`MSALAADAuthority`, `MSALB2CAuthority`) lub użyć metody `authorityWithURL:error:` fabrycznej do utworzenia podklas przy użyciu adresu URL urzędu.

Użyj `url` właściwości, aby uzyskać znormalizowany adres URL urzędu. Dodatkowe parametry i składniki ścieżki lub fragmenty, które nie są częścią urzędu, nie będą znajdować się w zwróconym znormalizowanym adresie URL urzędu.

Poniżej przedstawiono podklasy, `MSALAuthority` które można utworzyć wystąpienia w zależności od urzędu, którego chcesz użyć.

### <a name="msalaadauthority"></a>MSALAADAuthority

`MSALAADAuthority`reprezentuje organ AAD. Adres URL urzędu powinien być w `<port>` następującym formacie, gdzie jest opcjonalny:`https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CAuthority

`MSALB2CAuthority`reprezentuje organ B2C. Domyślnie adres URL urzędu B2C powinien być `<port>` w następującym formacie, gdzie jest opcjonalny: `https://<host>:<port>/tfp/<tenant>/<policy>`. Jednak MSAL obsługuje również inne arbitralne formaty uprawnień B2C.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [przepływach uwierzytelniania i scenariuszach aplikacji](authentication-flows-app-scenarios.md)
