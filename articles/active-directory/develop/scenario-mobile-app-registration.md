---
title: Rejestrowanie aplikacji mobilnych, które dzwonią do internetowych interfejsów API | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć aplikację mobilną, która wywołuje internetowe interfejsy API (konfiguracja kodu aplikacji)
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
ms.openlocfilehash: 75cfd304869bfb63131dfd2afed9f925c86d32fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132435"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>Rejestrowanie aplikacji mobilnych, które dzwonią do internetowych interfejsów API

Ten artykuł zawiera instrukcje ułatwiające rejestrację aplikacji mobilnej, którą tworzysz.

## <a name="supported-account-types"></a>Obsługiwane typy konta

Typy kont, które obsługują aplikacje mobilne, zależą od środowiska, które chcesz włączyć i przepływów, których chcesz użyć.

### <a name="audience-for-interactive-token-acquisition"></a>Publiczność do interaktywnego pozyskiwania tokenów

Większość aplikacji mobilnych korzysta z uwierzytelniania interaktywnego. Jeśli aplikacja korzysta z tej formy uwierzytelniania, możesz zalogować użytkowników z dowolnego [typu konta](quickstart-register-app.md#register-a-new-application-using-the-azure-portal).

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>Odbiorcy zintegrowanego uwierzytelniania systemu Windows, hasła nazwy użytkownika i B2C

Jeśli masz aplikację platformy uniwersalnej systemu Windows (UWP), możesz użyć zintegrowanego uwierzytelniania systemu Windows, aby zalogować się do użytkowników. Aby używać zintegrowanego uwierzytelniania systemu Windows lub uwierzytelniania hasłem użytkownika, aplikacja musi zalogować się do użytkowników w dzierżawie deweloperów linii biznesowej (LOB). W scenariuszu niezależnego dostawcy oprogramowania (ISV) aplikacja może logować się do użytkowników w organizacjach usługi Azure Active Directory. Te przepływy uwierzytelniania nie są obsługiwane dla kont osobistych firmy Microsoft.

Można również zalogować użytkowników przy użyciu tożsamości społecznościowych, które przechodzą urząd B2C i zasady. Aby użyć tej metody, można użyć tylko uwierzytelniania interaktywnego i uwierzytelniania hasła nazwy użytkownika. Uwierzytelnianie za pomocą hasła nazwy użytkownika jest obecnie obsługiwane tylko w systemach Xamarin.iOS, Xamarin.Android i UWP.

Aby uzyskać więcej informacji, zobacz [Scenariusze i obsługiwane przepływy uwierzytelniania](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) i [scenariusze oraz obsługiwane platformy i języki](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages).

## <a name="platform-configuration-and-redirect-uris"></a>Konfiguracja platformy i przekierowywanie identyfikatorów URI  

### <a name="interactive-authentication"></a>Uwierzytelnianie interakcyjne

Podczas tworzenia aplikacji mobilnej, która używa uwierzytelniania interaktywnego, najważniejszym krokiem rejestracji jest identyfikator URI przekierowania. Uwierzytelnianie interaktywne można ustawić za pomocą [konfiguracji platformy w bloku **Uwierzytelnianie** ](https://aka.ms/MobileAppReg).

To środowisko umożliwi aplikacji uzyskanie logowania jednokrotnego za pośrednictwem usługi Microsoft Authenticator (i portalu firmy usługi Intune w systemie Android). Będzie również obsługiwać zasady zarządzania urządzeniami.

Portal rejestracji aplikacji zapewnia środowisko w wersji zapoznawczej, aby ułatwić obliczanie brokera uri odpowiedzi dla aplikacji dla systemów iOS i Android:

1. W portalu rejestracji aplikacji wybierz pozycję **Uwierzytelnianie** > **Wypróbuj nowe środowisko**.

   ![Łaz uwierzytelniania, w którym wybierasz nowe środowisko](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Wybierz **pozycję Dodaj platformę**.

   ![Dodawanie platformy](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Gdy lista platform jest obsługiwana, wybierz **iOS**.

   ![Wybieranie aplikacji mobilnej](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Wprowadź identyfikator pakietu, a następnie wybierz pozycję **Zarejestruj**.

   ![Wprowadź identyfikator pakietu](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Po wykonaniu kroków identyfikator URI przekierowania jest obliczany dla Ciebie, jak na poniższej ilustracji.

![Wynikowy przekierowanie identyfikatora URI](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Jeśli wolisz ręcznie skonfigurować identyfikator URI przekierowania, można to zrobić za pomocą manifestu aplikacji. Oto zalecany format manifestu:

- **iOS**:`msauth.<BUNDLE_ID>://auth` 
  - Na przykład, wprowadź`msauth.com.yourcompany.appName://auth`
- **Android**:`msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Skrót podpisu systemu Android można wygenerować za pomocą klucza wydania lub klucza debugowania za pomocą polecenia KeyTool.

### <a name="username-password-authentication"></a>Uwierzytelnianie za pomocą hasła nazwy użytkownika

Jeśli aplikacja używa tylko uwierzytelniania hasła nazwy użytkownika, nie trzeba rejestrować identyfikatora URI przekierowania dla aplikacji. Ten przepływ odbywa się w obie strony do punktu końcowego platformy tożsamości firmy Microsoft w wersji 2.0. Aplikacja nie zostanie wywołana z powrotem na żadnym określonym identyfikatorze URI. 

Jednak należy zidentyfikować aplikację jako publiczną aplikację kliencką. Aby to zrobić, uruchom w sekcji **Uwierzytelnianie** aplikacji. W podsekcji **Ustawienia zaawansowane** w akapicie **Domyślny typ klienta** dla pytania **Traktuj aplikację jako klienta publicznego**wybierz opcję **Tak**.

## <a name="api-permissions"></a>Uprawnienia aplikacji

Aplikacje mobilne dzwonią do interfejsów API w imieniu zalogowanego użytkownika. Aplikacja musi zażądać uprawnień delegowanych. Te uprawnienia są również nazywane zakresami. W zależności od odpowiedniego środowiska można zażądać uprawnień delegowanych statycznie za pośrednictwem witryny Azure portal. Lub można zażądać ich dynamicznie w czasie wykonywania. 

Statycznie rejestrując uprawnienia, możesz umożliwić administratorom łatwe zatwierdzanie aplikacji. Zalecana jest rejestracja statyczna.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfiguracja kodu](scenario-mobile-app-configuration.md)
