---
title: Konfigurowanie pęku kluczy
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak skonfigurować pęk kluczy, aby aplikacja mogła buforować tokeny w pęku kluczy.
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
ms.openlocfilehash: d94bf7ffe955c9ec9ee2a2e7f7c4dbaaa28df270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085853"
---
# <a name="configure-keychain"></a>Konfigurowanie pęku kluczy

Gdy [biblioteka uwierzytelniania firmy Microsoft dla systemu iOS i macOS](msal-overview.md) (MSAL) loguje się do użytkownika lub odświeża token, próbuje buforować tokeny w pęku kluczy. Buforowanie tokenów w pęku kluczy umożliwia msal do zapewnienia cichego logowania jednokrotnego (Logowanie jednokrotne) między wieloma aplikacjami, które są dystrybuowane przez tego samego dewelopera firmy Apple. Wpis SSO jest osiągany za pomocą funkcji grup dostępu do pęku kluczy. Aby uzyskać więcej informacji, zobacz [dokumentację elementów pęku kluczy](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)firmy Apple .

W tym artykule opisano sposób konfigurowania uprawnień aplikacji, tak aby usługa MSAL mogła zapisywać tokeny buforowane w systemach iOS i macOS.

## <a name="default-keychain-access-group"></a>Domyślna grupa dostępu do pęku kluczy

### <a name="ios"></a>iOS

Usługa MSAL w systemu `com.microsoft.adalcache` iOS domyślnie używa grupy dostępu. Jest to grupa dostępu współużytkowanego używana przez zestawy SDK biblioteki uwierzytelniania usługi Azure AD (MSAL i Biblioteki ADAL) i zapewnia najlepsze środowisko logowania jednokrotnego między wieloma aplikacjami od tego samego wydawcy.

W przypadku systemu `com.microsoft.adalcache` iOS dodaj grupę pęku kluczy do uprawnień aplikacji w programie XCode w obszarze Udostępnianie > **pęku** > **kluczy** **ustawień projektu**

### <a name="macos"></a>macOS

Usługa MSAL w `com.microsoft.identity.universalstorage` systemie macOS domyślnie używa grupy dostępu.

Ze względu na ograniczenia pęku kluczy systemu macOS program MSAL `access group` nie przekłada bezpośrednio atrybutu grupy dostępu do pęku kluczy (zobacz [kSecAttrAccessGroup)](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)w systemie macOS 10.14 i wcześniejszych. Jednak zachowuje się podobnie z punktu widzenia jednokrotnego przyśmiania, zapewniając, że wiele aplikacji dystrybuowanych przez tego samego dewelopera firmy Apple może mieć cichy identyfikator jednokrotny.

W systemie macOS 10.15 (macOS Catalina) msal używa atrybutu grupy dostępu do pęku kluczy, aby osiągnąć cichy identyfikator SSO, podobnie jak iOS.

## <a name="custom-keychain-access-group"></a>Niestandardowa grupa dostępu do pęku kluczy

Jeśli chcesz użyć innej grupy dostępu do pęku kluczy, możesz `MSALPublicClientApplicationConfig` przekazać grupę niestandardową podczas tworzenia przed utworzeniem `MSALPublicClientApplication`, w tym stylu:

# <a name="objective-c"></a>[Cel C](#tab/objc)

```objc
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                                                            redirectUri:@"your-redirect-uri"
                                                                                              authority:nil];
    
config.cacheConfig.keychainSharingGroup = @"custom-group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:nil];
    
// Now call acquiretoken. 
// Tokens will be saved into the "custom-group" access group
// and only shared with other applications declaring the same access group
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: nil)
config.cacheConfig.keychainSharingGroup = "custom-group"
        
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```

---

## <a name="disable-keychain-sharing"></a>Wyłączanie udostępniania pęku kluczy

Jeśli nie chcesz udostępniać stanu jednokrotnego między wieloma aplikacjami lub użyć dowolnej grupy dostępu do pęku kluczy, wyłącz udostępnianie pęku kluczy, przekazując identyfikator pakietu aplikacji jako grupę kluczy:

# <a name="objective-c"></a>[Cel C](#tab/objc)

```objc
config.cacheConfig.keychainSharingGroup = [[NSBundle mainBundle] bundleIdentifier];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
if let bundleIdentifier = Bundle.main.bundleIdentifier {
    config.cacheConfig.keychainSharingGroup = bundleIdentifier
}
```

---

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>Błąd uchwytu -34018 (nie można ustawić elementu w pęku kluczy)

Błąd -34018 zwykle oznacza, że pęk kluczy nie został poprawnie skonfigurowany. Upewnij się, że grupa dostępu pęku kluczy skonfigurowana w programie MSAL jest zgodna z grupą skonfigurowanej w uprawnieniach.

## <a name="ensure-your-application-is-properly-signed"></a>Upewnij się, że aplikacja jest prawidłowo podpisana

W systemie macOS aplikacje mogą być wykonywane bez podpisywane przez dewelopera. Podczas gdy większość funkcji MSAL będzie nadal działać, logować się jedno i cofnięciem za pośrednictwem dostępu do pęku kluczy wymaga podpisanej aplikacji. Jeśli masz wiele monitów o pęk kluczy, upewnij się, że podpis aplikacji jest prawidłowy.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o grupach dostępu do pęku kluczy w artykule [Udostępnianie elementów pęku kluczy firmy Apple wśród kolekcji aplikacji.](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)
