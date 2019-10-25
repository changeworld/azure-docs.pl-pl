---
title: Aplikacja mobilna, która wywołuje interfejsy API sieci Web — konfiguracja kodu aplikacji
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć aplikację mobilną wywołującą interfejsy API sieci Web (konfigurację kodu aplikacji)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f55e73fa1a73908d7e77bacc6af24ea1a40ba92
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803741"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>Aplikacja mobilna, która wywołuje interfejsy API sieci Web — Rejestracja aplikacji

Ten artykuł zawiera instrukcje dotyczące rejestracji aplikacji na potrzeby tworzenia aplikacji mobilnej.

## <a name="supported-accounts-types"></a>Obsługiwane typy kont

Typy kont obsługiwane w aplikacjach mobilnych zależą od środowiska, które chcesz włączyć, oraz przepływów, których chcesz użyć.

### <a name="audience-for-interactive-token-acquisition"></a>Odbiorcy na potrzeby pozyskiwania tokenów interaktywnych

Większość aplikacji mobilnych korzysta z uwierzytelniania interakcyjnego. W takim przypadku możesz zalogować użytkowników z dowolnego [typu konta](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)

### <a name="audience-for-integrated-authentication-usernamepassword-and-b2c"></a>Odbiorcy uwierzytelniania zintegrowanego, nazwy użytkownika/hasła i B2C

- Jeśli zamierzasz używać zintegrowanego uwierzytelniania systemu Windows (możliwego w aplikacjach platformy UWP) lub nazwy użytkownika/hasła, Twoja aplikacja musi zalogować użytkowników w Twojej dzierżawie (deweloper LOB) lub w organizacji usługi Azure Active Directory (scenariusz ISV). Te przepływy uwierzytelniania nie są obsługiwane w przypadku kont osobistych firmy Microsoft
- Jeśli zalogujesz się użytkowników przy użyciu tożsamości społecznościowych przekazujących B2C Urząd i zasady, możesz używać tylko uwierzytelniania interakcyjnego i nazwy użytkownika. Hasło użytkownika jest obecnie obsługiwane tylko w rozszerzeniach Xamarin. iOS, Xamarin. Android i platformy UWP.

Aby zapoznać się z dużym obrazem, zobacz [scenariusze i obsługiwane przepływy uwierzytelniania](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) oraz [scenariusze i obsługiwane platformy i języki](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="platform-configuration-and-redirect-uris"></a>Konfiguracja platformy i identyfikatory URI przekierowania  

### <a name="interactive-authentication"></a>Uwierzytelnianie interakcyjne

Podczas kompilowania aplikacji mobilnej przy użyciu uwierzytelniania interaktywnego najważniejszym krokiem rejestracji jest identyfikator URI przekierowania. Tę wartość można ustawić za pomocą [konfiguracji platformy w bloku uwierzytelnianie](https://aka.ms/MobileAppReg).

To środowisko umożliwi aplikacji uzyskanie rejestracji jednokrotnej (SSO) za pomocą Microsoft Authenticator (i Intune — Portal firmy w systemie Android), a także obsługę zasad zarządzania urządzeniami.

Należy pamiętać, że w portalu rejestracji aplikacji jest dostępne środowisko w wersji zapoznawczej, które ułatwia Obliczanie identyfikatora URI odpowiedzi obsługiwanej przez brokera dla aplikacji dla systemów iOS i Android:

1. W obszarze Rejestracja aplikacji wybierz pozycję **uwierzytelnianie** i wybór **wypróbuj nowe środowisko**
   ![obraz](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Wybierz pozycję **dodaj
   platformy**![obraz](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Gdy lista platform jest obsługiwana, wybierz pozycję **iOS**
   ![obraz](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Wprowadź żądany identyfikator pakietu, a następnie naciśnij pozycję **Register**
   ![Image](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

5. Identyfikator URI przekierowania jest obliczany dla Ciebie.
   ](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png) obrazu ![

Jeśli wolisz ręcznie skonfigurować identyfikator URI przekierowania, możesz to zrobić za pomocą manifestu aplikacji. Zalecany format jest następujący:

- ***iOS***: `msauth.<BUNDLE_ID>://auth` (na przykład "msauth. com. yourcompany. nazwa_aplikacji://auth")
- ***Android***: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Skrót sygnatury systemu Android można wygenerować przy użyciu klawiszy Zwolnij lub Debuguj za pomocą polecenia narzędzia.

### <a name="username-password"></a>Hasło użytkownika

Jeśli aplikacja używa tylko nazwy użytkownika/hasła, nie trzeba rejestrować identyfikatora URI przekierowania dla aplikacji. W rzeczywistości ten przepływ wykonuje rundy w punkcie końcowym Microsoft Identity platform v 2.0, a aplikacja nie zostanie wywołana ponownie na żadnym konkretnym identyfikatorze URI. Należy jednak pamiętać, że aplikacja jest publiczną aplikacją kliencką. Tę konfigurację można uzyskać, przechodząc do sekcji **uwierzytelnianie** dla aplikacji, a następnie w podsekcji **Ustawienia zaawansowane** wybierz pozycję **tak**, a pytanie **Traktuj aplikację jako klienta publicznego** ( **domyślnie akapit typu klient** )

## <a name="api-permissions"></a>Uprawnienia interfejsu API

Interfejsy API wywołania aplikacji mobilnych w imieniu zalogowanego użytkownika. Aplikacja musi zażądać delegowania uprawnień, nazywanych również zakresami. W zależności od wymaganego środowiska można to zrobić statycznie przez Azure Portal lub dynamicznie w czasie wykonywania. Statyczne rejestrowanie uprawnień pozwala administratorom łatwo zatwierdzać aplikację i jest zalecane.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfiguracja kodu](scenario-mobile-app-configuration.md)
