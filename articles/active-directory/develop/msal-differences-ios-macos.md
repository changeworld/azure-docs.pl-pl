---
title: Msal dla iOS & różnice w systemie macOS | Azure
titleSuffix: Microsoft identity platform
description: W tym artykule opisano różnice między użyciem biblioteki uwierzytelniania firmy Microsoft (MSAL) między systemami iOS i macOS.
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
ms.openlocfilehash: 62b79ee7398286b8e6c8ed8612bd001595e1f6ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084968"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>Biblioteka uwierzytelniania firmy Microsoft dla systemów iOS i macOS — różnice

W tym artykule wyjaśniono różnice w funkcjonalności między biblioteką uwierzytelniania firmy Microsoft (MSAL) dla systemu iOS i macOS.

> [!NOTE]
> Na komputerze Mac usługa MSAL obsługuje tylko aplikacje systemu macOS.

## <a name="general-differences"></a>Ogólne różnice

MSAL dla systemu macOS jest podzbiorem funkcji dostępnych dla systemu iOS.

MsAL dla systemu macOS nie obsługuje:

- różnych typów przeglądarek, takich jak `ASWebAuthenticationSession`, `SFAuthenticationSession`, `SFSafariViewController`.
- uwierzytelnianie obsługiwane przez brokera za pośrednictwem aplikacji Microsoft Authenticator nie jest obsługiwane dla systemu macOS.

Udostępnianie pęku kluczy między aplikacjami tego samego wydawcy jest bardziej ograniczone w systemie macOS 10.14 lub starszym. Użyj [list kontroli dostępu,](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc) aby określić ścieżki do aplikacji, które powinny współużytkować pęk kluczy. Użytkownik może zobaczyć dodatkowe monity o pęk kluczy.

W systemie macOS 10.15+ zachowanie usługi MSAL jest takie samo w systemie iOS i macOS. Usługa MSAL używa [grup dostępu do pęku kluczy](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) do udostępniania pęku kluczy. 

### <a name="conditional-access-authentication-differences"></a>Różnice uwierzytelniania dostępu warunkowego

W scenariuszach dostępu warunkowego będzie mniej monitów użytkownika podczas korzystania z usługi MSAL dla systemu iOS. Dzieje się tak, ponieważ system iOS używa aplikacji brokera (Microsoft Authenticator), która w niektórych przypadkach neguje konieczność monitu użytkownika.

### <a name="project-setup-differences"></a>Różnice konfiguracji projektu

**Macos**

- Podczas konfigurowania projektu w systemie macOS upewnij się, że aplikacja jest podpisana przy zastosowaniu prawidłowego certyfikatu dewelopera lub produkcyjnego. MSAL nadal działa w trybie niepodpisanym, ale będzie zachowywać się inaczej w odniesieniu do trwałości pamięci podręcznej. Aplikacja powinna być uruchamiana tylko do celów debugowania. Jeśli rozpowszechniasz aplikację bez znaku, będzie to:
1. W 10.14 i wcześniejszych msal poprosi użytkownika o hasło pęku kluczy przy każdym ponownym uruchomieniu aplikacji.
2. W 10.15+, MSAL poprosi użytkownika o poświadczenia dla każdego nabycia tokenu. 

- Aplikacje systemu macOS nie muszą implementować wywołania AppDelegate.

**iOS**

- Istnieją dodatkowe kroki, aby skonfigurować projekt do obsługi przepływu brokera uwierzytelniania. Kroki są wywoływane w samouczku.
- Projekty iOS muszą rejestrować niestandardowe schematy w info.plist. Nie jest to wymagane w systemie macOS.
