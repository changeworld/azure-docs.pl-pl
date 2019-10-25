---
title: Konfigurowanie pęku kluczy
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak skonfigurować pęku kluczy, aby aplikacja mogła buforować tokeny w pęku kluczy.
services: active-directory
documentationcenter: ''
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69991d105ff3523310f54e65596f2f379b547052
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803808"
---
# <a name="configure-keychain"></a>Konfigurowanie pęku kluczy

Gdy [Biblioteka uwierzytelniania firmy Microsoft dla systemu iOS i macOS](msal-overview.md) (MSAL) w użytkowniku lub odświeża token, próbuje buforować tokeny w łańcuchu kluczy. Tokeny buforowania w łańcuchu kluczy umożliwiają MSAL zapewnianie dyskretnego logowania jednokrotnego między wieloma aplikacjami dystrybuowanymi przez ten sam program Apple Developer. Logowanie jednokrotne jest realizowane za pośrednictwem funkcji grup dostępu łańcucha kluczy. Aby uzyskać więcej informacji, zobacz [dokumentację elementów łańcucha kluczy](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)firmy Apple.

W tym artykule opisano sposób konfigurowania uprawnień aplikacji, dzięki czemu MSAL może zapisywać buforowane tokeny w systemach iOS i macOS pęku kluczy.

## <a name="default-keychain-access-group"></a>Domyślna grupa dostępu łańcucha kluczy

### <a name="ios"></a>iOS

MSAL w systemie iOS domyślnie używa grupy dostępu `com.microsoft.adalcache`. Jest to grupa dostępu współdzielonego używana przez zestawy SDK MSAL i Azure AD Authentication Library (ADAL) i zapewnia najlepszą obsługę logowania jednokrotnego między wieloma aplikacjami z tego samego wydawcy.

W systemie iOS Dodaj grupę pęku kluczy `com.microsoft.adalcache` do uprawnienia aplikacji w XCode w obszarze **Ustawienia projektu** > **możliwości** >  funkcja**udostępniania łańcucha kluczy**

### <a name="macos"></a>macOS

MSAL on macOS domyślnie używa grupy dostępu `com.microsoft.identity.universalstorage`.

Ze względu na ograniczenia macOS pęku kluczy, MSAL `access group` nie są bezpośrednio tłumaczone na atrybut grupy dostępu pęku kluczy (zobacz [kSecAttrAccessGroup](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)) na macOS 10,14 i wcześniejszych. Jednak zachowuje się podobnie z perspektywy rejestracji jednokrotnej, zapewniając, że wiele aplikacji dystrybuowanych przez tego samego deweloperów firmy Apple może mieć dyskretne Logowanie jednokrotne.

Na macOS 10,15 (macOS Catalina), MSAL używa atrybutu grupy dostępu pęku kluczy, aby uzyskać dyskretne Logowanie jednokrotne, podobnie jak w przypadku systemu iOS.

## <a name="custom-keychain-access-group"></a>Niestandardowa Grupa dostępu pęku kluczy

Jeśli chcesz użyć innej grupy dostępu łańcucha kluczy, możesz przekazać grupę niestandardową podczas tworzenia `MSALPublicClientApplicationConfig` przed utworzeniem `MSALPublicClientApplication` w następujący sposób:

Cel-C:

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



Adres

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



## <a name="disable-keychain-sharing"></a>Wyłącz udostępnianie łańcucha kluczy

Jeśli nie chcesz udostępniać stanu logowania jednokrotnego między wieloma aplikacjami lub korzystać z dowolnej grupy dostępu łańcucha kluczy, Wyłącz udostępnianie łańcucha kluczy, przekazując identyfikator pakietu aplikacji jako grupę łańcuchową:

Cel-C:

```objc
config.cacheConfig.keychainSharingGroup = [[NSBundle mainBundle] bundleIdentifier];
```

Adres

```swift
if let bundleIdentifier = Bundle.main.bundleIdentifier {
    config.cacheConfig.keychainSharingGroup = bundleIdentifier
}
```

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>Dojście — błąd 34018 (nie można ustawić elementu na pęku kluczy)

Błąd — 34018 zwykle oznacza, że pęku kluczy nie została poprawnie skonfigurowana. Upewnij się, że Grupa dostępu pęku kluczy, która została skonfigurowana w MSAL, jest zgodna z konfiguracją w uprawnieniach.

## <a name="ensure-your-application-is-properly-signed"></a>Upewnij się, że aplikacja jest prawidłowo podpisana

W systemie macOS aplikacje mogą być wykonywane bez podpisywania przez dewelopera. Chociaż większość funkcji MSAL będzie nadal działać, logowanie jednokrotne za pomocą dostępu do łańcucha kluczy wymaga podpisania aplikacji. Jeśli występuje wiele wierszy pęku kluczy, upewnij się, że podpis aplikacji jest prawidłowy.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat grup dostępu łańcucha kluczy w [dostępie do usługi Microsoft Sharing na potrzeby udostępniania elementów łańcucha kluczy w zbiorze aplikacji](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) .
