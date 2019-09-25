---
title: Biblioteka Microsoft Authentication Library (MSAL) dla systemu iOS & macOS | Azure
description: Opisuje różnice użycia biblioteki uwierzytelniania firmy Microsoft (MSAL) między systemami iOS i macOS.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: beb6e55e83412535f29284db48fb37fd514b35d4
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71269014"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>Biblioteka uwierzytelniania firmy Microsoft dla systemów iOS i macOS — różnice

W tym artykule opisano różnice w działaniu między biblioteką uwierzytelniania firmy Microsoft (MSAL) dla systemów iOS i macOS.

> [!NOTE]
> Na komputerze Mac MSAL obsługuje tylko aplikacje macOS.

## <a name="general-differences"></a>Ogólne różnice

MSAL for macOS to podzbiór funkcji dostępnych dla systemu iOS.

MSAL dla macOS nie obsługuje:

- różne typy przeglądarek, takie `ASWebAuthenticationSession`jak `SFAuthenticationSession`, `SFSafariViewController`,.
- uwierzytelnianie przez brokera za pośrednictwem aplikacji Microsoft Authenticator nie jest obsługiwane w przypadku macOS.

Udostępnianie łańcucha kluczy między aplikacjami z tego samego wydawcy jest bardziej ograniczone na macOS 10,14 i starszych. Użyj [list kontroli dostępu](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc) , aby określić ścieżki do aplikacji, które powinny korzystać z łańcucha kluczy. Użytkownik może zobaczyć dodatkowe wiersze łańcucha kluczy.

W przypadku macOS 10.15 +, zachowanie MSAL jest takie samo jak w przypadku systemów iOS i macOS. MSAL używa [grup dostępu pęku kluczy](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) do udostępniania łańcucha kluczy. 

### <a name="conditional-access-authentication-differences"></a>Różnice dotyczące uwierzytelniania dostępu warunkowego

W przypadku scenariuszy dostępu warunkowego w przypadku korzystania z usługi MSAL dla systemu iOS będą wyświetlane mniejsze monity użytkownika. Wynika to z faktu, że system iOS używa aplikacji brokera (Microsoft Authenticator), która wyklucza konieczność monitowania użytkownika w niektórych przypadkach.

### <a name="project-setup-differences"></a>Różnice w konfiguracji projektu

**macOS**

- Podczas konfigurowania projektu w programie macOS upewnij się, że aplikacja jest podpisana przy użyciu prawidłowego certyfikatu programistycznego lub produkcyjnego. MSAL nadal działa w trybie bez znaku, ale zadziała inaczej w odniesieniu do trwałości pamięci podręcznej. Aplikacja powinna być uruchamiana tylko bez znaku na potrzeby debugowania. Jeśli aplikacja jest dystrybuowana bez znaku, będzie:
1. W dniu 10,14 i wcześniejszych MSAL wyświetli monit o podanie hasła pęku kluczy przy każdym ponownym uruchomieniu aplikacji.
2. W programie 10.15 + MSAL monituje użytkownika o podanie poświadczeń dla każdego przejęcia tokenu. 

- aplikacje macOS nie muszą implementować wywołania AppDelegate.

**iOS**

- Aby skonfigurować projekt do obsługi przepływu brokera uwierzytelniania, należy wykonać dodatkowe czynności. Kroki są wywoływane w samouczku.
- projekty systemu iOS muszą rejestrować niestandardowe schematy w info. plist. Nie jest to wymagane w macOS.
