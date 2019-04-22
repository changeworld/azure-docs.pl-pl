---
title: Przewodnik Szybki Start Microsoft tożsamości platformy systemu iOS | Azure
description: Dowiedz się, w jaki sposób logować użytkowników i wykonywać zapytania do programu Microsoft Graph w aplikacji natywnej dla systemu iOS.
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6340e0f349d66ecf6baaca481722396a6d786c5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59496133"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-native-app"></a>Szybki start: Logowanie użytkowników i wywoływanie interfejsu API programu Microsoft Graph z poziomu aplikacji natywnej dla systemu iOS

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Ten przewodnik Szybki start zawiera przykładowy kod, który pokazuje, w jaki sposób można logować się na kontach osobistych i służbowych, uzyskiwać tokeny dostępu i wywoływać interfejs API programu Microsoft Graph z poziomu aplikacji natywnej dla systemu iOS.

![Pokazuje, jak działa przykładowej aplikacji wygenerowane przez ten przewodnik Szybki Start](media/quickstart-v2-ios/ios-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download"></a>Rejestrowanie i pobieranie
> ### <a name="register-and-configure-your-application-and-code-sample"></a>Rejestrowanie i konfigurowanie aplikacji i przykładowego kodu
> #### <a name="step-1-register-your-application"></a>Krok 1: Rejestrowanie aplikacji
> Aby zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
> 1. Przejdź do [portalu rejestracji aplikacji firmy Microsoft](https://apps.dev.microsoft.com/portal/register-app), aby zarejestrować aplikację.
> 1. W polu **Application Name** (Nazwa aplikacji) wprowadź nazwę aplikacji.
> 1. Upewnij się, że pole **Guided Setup** (Konfiguracja z przewodnikiem) jest wyczyszczone, a następnie wybierz pozycję **Create** (Utwórz).
> 1. Wybierz kolejno pozycje **Add platform** (Dodaj platformę), **Native Application** (Aplikacja natywna) i **Save** (Zapisz).

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Krok 1: Konfigurowanie aplikacji
> Dla przykładu kodu dla tego przewodnika Szybki Start do pracy, należy dodać adres URL odpowiedzi jako `msal<AppId>://auth` (gdzie msal\<AppId > jest identyfikator aplikacji).
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź tę zmianę automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Already configured](media/quickstart-v2-ios/green-check.png) (Już skonfigurowano) Twoja aplikacja została skonfigurowana za pomocą tego atrybutu

#### <a name="step-2-download-your-web-server-or-project"></a>Krok 2: Pobieranie serwera internetowego lub projektu

- [Pobieranie projektu XCode](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Krok 3: Konfigurowanie projektu

1. Wyodrębnij plik zip i otwórz projekt w programie XCode.
1. Edytuj plik **ViewController.swift** i zastąp wiersz rozpoczynający się od ciągu „let kClientID” następującym fragmentem kodu:

    > [!div renderon="portal" class="sxs-lookup"]
    > ```swift
    > let kClientID = "Enter_the_Application_Id_here"
    > ```

    > [!div renderon="docs"]
    > ```swift
    > let kClientID = "<ENTER_THE_APPLICATION_ID_HERE>"
    > ```   
1. Naciśnij klawisz Control, klikając pozycję **Info.plist**, aby wywołać menu kontekstowe, a następnie wybierz pozycję **Open As** (Otwórz jako)  > **Source Code** (Kod źródłowy).
1. W obszarze węzła głównego dict dodaj następujący kod:

    > [!div renderon="portal" class="sxs-lookup"]
    > ```xml
    > <key>CFBundleURLTypes</key>
    > <array>
    >     <dict>
    >         <key>CFBundleTypeRole</key>
    >         <string>Editor</string>
    >         <key>CFBundleURLName</key>
    >         <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
    >         <key>CFBundleURLSchemes</key>
    >         <array>
    >             <string>msalEnter_the_Application_Id_here</string>
    >         </array>
    >     </dict>
    > </array>
    > ```

    > [!div renderon="docs"]
    > ```xml
    > <key>CFBundleURLTypes</key>
    > <array>
    >     <dict>
    >         <key>CFBundleTypeRole</key>
    >         <string>Editor</string>
    >         <key>CFBundleURLName</key>
    >         <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
    >         <key>CFBundleURLSchemes</key>
    >         <array>
    >             <string>msal<ENTER_THE_APPLICATION_ID_HERE></string>
    >         </array>
    >     </dict>
    > </array>
    > ```
    
> [!div renderon="docs"]
> <span>5.</span> Zastąp ciąg `<ENTER_THE_APPLICATION_ID_HERE>` *identyfikatorem aplikacji*. Jeśli chcesz znaleźć *identyfikator aplikacji*, przejdź do strony *Omówienie*.

## <a name="more-information"></a>Więcej informacji

Zapoznaj się z następującymi sekcjami, aby dowiedzieć się więcej na temat tego przewodnika Szybki start.

### <a name="msal"></a>BIBLIOTEKA MSAL

Biblioteka MSAL ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) to biblioteka umożliwiająca logowanie użytkowników i przesyłanie żądań tokenów używanych do uzyskiwania dostępu do interfejsu API chronionego przez usługę Azure Active Directory. Możesz dodać bibliotekę MSAL do aplikacji w następujący sposób:

```
$ vi Podfile
```
Dodaj następujący kod do tego pliku podfile:

```
 target 'QuickStart' do
   use_frameworks!
 pod 'MSAL'
 end
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL

Aby dodać odwołanie do biblioteki MSAL, dodaj następujący kod:

```swift
import MSAL
```

Następnie zainicjuj bibliotekę MSAL, używając następującego kodu:

```swift
let authority = MSALAuthority(url: URL(string: kAuthority)!)
self.applicationContext = try MSALPublicClientApplication(clientId: kClientID, authority: authority)
```

> |Gdzie: ||
> |---------|---------|
> | `clientId` | Identyfikator aplikacji z aplikacji zarejestrowanej w witrynie *portal.azure.com* |
> | `authority` | Punkt końcowy platforma tożsamości firmy Microsoft. W większości przypadków będzie to adres *https<span/>://login.microsoftonline.com/common* |

### <a name="requesting-tokens"></a>Przesyłanie żądań tokenów

Biblioteka MSAL oferuje dwie metody uzyskiwania tokenów: `acquireToken` i `acquireTokenSilent`.

#### <a name="getting-an-access-token-interactively"></a>Interaktywne uzyskiwanie tokenów dostępu

Sytuacje wymagają wymuszanie użytkownikom na interakcję z końcowym platforma tożsamości firmy Microsoft, co spowoduje, że przełączenie kontekstu do przeglądarki systemu, aby to zweryfikować poświadczeń użytkownika, lub o wyrażenie zgody. Oto niektóre przykłady:

* Gdy nowi użytkownicy logują się do aplikacji po raz pierwszy.
* Gdy użytkownicy muszą ponownie wprowadzić poświadczenia, ponieważ hasło wygasło.
* Gdy aplikacja żąda dostępu do zasobów wymagającego zgody użytkownika.
* Gdy wymagane jest uwierzytelnianie dwuetapowe.

```swift
applicationContext.acquireToken(forScopes: self.kScopes) { (result, error) in /* Add your handling logic */}
```

> |Gdzie:||
> |---------|---------|
> | `forScopes` | Zawiera żądane zakresy (czyli `[ "user.read" ]` dla programu Microsoft Graph lub `[ "<Application ID URL>/scope" ]` dla niestandardowych interfejsów internetowych API — czyli `api://<Application ID>/access_as_user`) |

#### <a name="getting-an-access-token-silently"></a>Dyskretne uzyskiwanie tokenów dostępu

Nie chcesz, aby użytkownik musiał weryfikować poświadczenia za każdym razem, gdy musi uzyskać dostęp do zasobu. Przez większość czasu uzyskiwanie i odnawianie tokenów powinno odbywać się bez żadnej interakcji z użytkownikiem. Możesz użyć metody `acquireTokenSilent` do uzyskiwania tokenów umożliwiających dostęp do chronionych zasobów po początkowym skorzystaniu z metody `acquireToken`:

```swift
applicationContext.acquireTokenSilent(forScopes: self.kScopes, account: applicationContext.allAccounts().first) { (result, error) in /* Add your handling logic */}
```

> |Gdzie: ||
> |---------|---------|
> | `forScopes` | Zawiera żądane zakresy (czyli `[ "user.read" ]` dla programu Microsoft Graph lub `[ "<Application ID URL>/scope" ]` dla niestandardowych interfejsów internetowych API — czyli `api://<Application ID>/access_as_user`) |
> | `account` | Konto przesyłające żądanie tokenu (biblioteka MSAL obsługuje wiele kont w jednej aplikacji). W przypadku tego przewodnika Szybki start wartość wskazuje na pierwsze konto w pamięci podręcznej (`applicationContext.allAccounts().first`). |

## <a name="next-steps"></a>Kolejne kroki

Wypróbuj samouczek dotyczący systemu iOS, aby uzyskać instrukcje krok po kroku dotyczące tworzenia aplikacji i nowych funkcji, w tym pełne objaśnienie informacji zawartych w tym przewodniku Szybki start.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Dowiedz się więcej na temat czynności, które są wymagane to utworzenia aplikacji użytej w tym przewodniku Szybki start

> [!div class="nextstepaction"]
> [Call Graph API iOS tutorial (Samouczek dotyczący wywoływania interfejsu API programu Graph dla systemu iOS)](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
