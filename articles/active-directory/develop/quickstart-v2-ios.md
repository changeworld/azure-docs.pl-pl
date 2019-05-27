---
title: Przewodnik Szybki Start Microsoft tożsamości platformy systemu iOS | Azure
description: Dowiedz się, jak logować użytkowników i zapytań programu Microsoft Graph w aplikacji systemu iOS.
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2019
ms.author: brandwe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3802d8f92913e416cc6a80f899179fde80cec30
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962597"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-app"></a>Szybki start: Logowanie użytkowników i wywołanie interfejsu API programu Microsoft Graph z aplikacji systemu iOS

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Ten przewodnik Szybki start zawiera przykładowy kod, który pokazuje, w jaki sposób można logować się na kontach osobistych i służbowych, uzyskiwać tokeny dostępu i wywoływać interfejs API programu Microsoft Graph z poziomu aplikacji natywnej dla systemu iOS.

![Pokazuje, jak działa przykładowej aplikacji wygenerowane przez ten przewodnik Szybki Start](media/quickstart-v2-ios/ios-intro.svg)

> [!NOTE]
> **Wymagania wstępne**
> * Środowisko XCode 10 +
> * iOS 10+ 

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> Istnieją dwie opcje uruchamiania aplikacji Szybki start:
> * [Ekspresowo] [Opcja 1: zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ręcznie] [Opcja 2: zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1: zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
> #### <a name="step-1-register-your-application"></a>Krok 1: Zarejestruj swoją aplikację
> Aby zarejestrować aplikację,
> 1. Przejdź do nowego [witryna Azure portal — rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs) okienka.
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację za pomocą tylko jednego kliknięcia.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2: zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Zarejestruj swoją aplikację
> Aby ręcznie zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Przejdź do platforma tożsamości firmy Microsoft dla deweloperów [rejestracje aplikacji](https://aka.ms/MobileAppReg) strony.
> 1. Wybierz **nowej rejestracji**.
> 1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
>      - W **nazwa** sekcji, wprowadź nazwę opisową aplikacji, która będzie wyświetlana dla użytkowników aplikacji, podczas logowania, lub wyrazić zgodę na aplikację, na przykład `iOSQuickstart`.
>      - Pomiń innych ustawień na tej stronie. 
>      - Trafienia `Register` przycisku.
> 1. Kliknij nową aplikację > Przejdź do `Authentication`  >  `Add Platform`  >  `iOS`.    
>      - Wprowadź ***identyfikatora pakietu*** dla aplikacji. 
> 1. Wybierz `Configure` i Zapisz ***konfiguracji biblioteki MSAL*** szczegóły później. 

> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>Krok 1: Konfigurowanie aplikacji
> Dla przykładu kodu dla tego przewodnika Szybki Start do pracy należy dodać przekierowania URI zgodna z brokera uwierzytelniania. 
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź tę zmianę automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Already configured](media/quickstart-v2-ios/green-check.png) (Już skonfigurowano) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów

#### <a name="step-2-download-your-web-server-or-project"></a>Krok 2: Pobieranie serwera internetowego lub projektu

- [Pobierz przykładowy kod](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Krok 3: Konfigurowanie projektu

> [!div renderon="docs"]
> Jeśli została wybrana opcja 1 powyżej, można pominąć tę procedurę. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Wyodrębnij plik zip i otwórz projekt w programie XCode.
> 1. Edytuj plik **ViewController.swift** i zastąp wiersz rozpoczynający się od ciągu „let kClientID” następującym fragmentem kodu:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_here"
>    let kAuthority = "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
>
>    ```
> 1. Kliknij prawym przyciskiem myszy **Info.plist** i wybierz **Otwórz jako** > **kod źródłowy**.
> 1. W węźle głównym dict Zamień swoje ***identyfikator pakietu***:
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
> 
>    ```
> 1. Kompilowanie i uruchamianie aplikacji! 

> [!div renderon="docs"]
>
> 1. Wyodrębnij plik zip i otwórz projekt w programie XCode.
> 1. Edytuj **ViewController.swift** i Zastąp wiersz rozpoczynający umożliwiają kClientID następującym fragmentem kodu:
>
>    ```swift
>    let kClientID = "<ENTER_YOUR_APPLICATION/CLIENT_ID>"
> 
>    ```
> 1. Kliknij prawym przyciskiem myszy **Info.plist** i wybierz **Otwórz jako** > **kod źródłowy**.
> 1. W węźle głównym dict Zamień swoje ***identyfikator pakietu***:
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.<ENTER_YOUR_BUNDLE_ID></string>
>          </array>
>       </dict>
>    </array>
>
>    ```
> 1. Kompilowanie i uruchamianie aplikacji! 

## <a name="more-information"></a>Więcej informacji

Zapoznaj się z następującymi sekcjami, aby dowiedzieć się więcej na temat tego przewodnika Szybki start.

### <a name="getting-msal"></a>Pobieranie biblioteki MSAL

Biblioteka MSAL ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) to biblioteka używane do logowania użytkowników i żądać tokenów, które umożliwiają dostęp do interfejsu API chronionego przez platforma tożsamości usługi Microsoft. Możesz dodać bibliotekę MSAL do aplikacji w następujący sposób:

```
$ vi Podfile

```
Dodaj następujący kod do tego pliku podfile (z elementem docelowym projektu):

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL', '~> 0.4.0'
end

```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL

Aby dodać odwołanie do biblioteki MSAL, dodaj następujący kod:

```swift
import MSAL
```

Następnie zainicjuj bibliotekę MSAL, używając następującego kodu:

```swift
let authority = try MSALAADAuthority(url: URL(string: kAuthority)!)
            
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)

```

> |Gdzie: ||
> |---------|---------|
> | `clientId` | Identyfikator aplikacji z aplikacji zarejestrowanej w witrynie *portal.azure.com* |
> | `authority` | Punkt końcowy platforma tożsamości firmy Microsoft. W większości przypadków będzie to adres *https<span/>://login.microsoftonline.com/common* |
> | `redirectUri` | Przekierowania URI Identyfikatora aplikacji. Można przekazać zero, aby użyć wartości domyślne lub niestandardowy identyfikator URI przekierowania. |

### <a name="additional-app-requirements"></a>Wymagania dotyczące dodatkowych aplikacji  

Aplikacja musi także mieć następujące czynności w Twojej `AppDelegate`. Dzięki temu zestaw SDK biblioteki MSAL obsługi odpowiedzi tokenu z aplikacji brokera uwierzytelniania podczas wykonywania uwierzytelniania.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
         guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
             return false
         }
         
         return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
     }

```

Ponadto aplikacja musi ma `LSApplicationQueriesSchemes` wpisu w swojej ***Info.plist*** obok `CFBundleURLTypes`. Próbka jest powiązana z tym uwzględnione. 

   ```xml 
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauth</string>
      <string>msauthv2</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Konta użytkowników i żądać tokenów

Biblioteka MSAL oferuje dwie metody uzyskiwania tokenów: `acquireToken` i `acquireTokenSilent`.

#### <a name="acquiretoken-getting-a-token-interactively"></a>acquireToken: Uzyskanie tokenu interaktywnie

Sytuacje wymagają użytkownikom na interakcję z platformą Microsoft identity. W takich przypadkach użytkownik końcowy może być konieczne wybierz swoje konto, wprowadź swoje poświadczenia lub wyrazić zgodę na uprawnienia dotyczące Twojej aplikacji. Na przykład: 

* Gdy nowi użytkownicy logują się do aplikacji po raz pierwszy.
* Jeśli użytkownik resetuje hasła, należy wprowadzić swoje poświadczenia 
* Gdy aplikacja żąda dostępu do zasobu po raz pierwszy
* Gdy wymagane są usługi MFA lub innymi zasadami dostępu warunkowego

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
applicationContext.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Gdzie:||
> |---------|---------|
> | `scopes` | Zawiera zakresy żądanej (czyli `[ "user.read" ]` dla programu Microsoft Graph lub `[ "<Application ID URL>/scope" ]` dla niestandardowych interfejsów API sieci Web (`api://<Application ID>/access_as_user`) |

#### <a name="acquiretokensilent-getting-an-access-token-silently"></a>acquireTokenSilent: Dyskretne uzyskiwanie tokenów dostępu

Aplikacje nie wymagają użytkownikom zalogować się za każdym razem, gdy będą one żądać tokenu. Jeśli użytkownik ma już logowanie, ta metoda umożliwia aplikacji, aby żądać tokenów w trybie dyskretnym. 

```swift
let parameters = MSALSilentTokenParameters(scopes: kScopes, account: applicationContext.allAccounts().first)
applicationContext.acquireTokenSilent(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Gdzie: ||
> |---------|---------|
> | `scopes` | Zawiera zakresy żądanej (czyli `[ "user.read" ]` dla programu Microsoft Graph lub `[ "<Application ID URL>/scope" ]` dla niestandardowych interfejsów API sieci Web (`api://<Application ID>/access_as_user`) |
> | `account` | Są żądane konto tokenu. Ten przewodnik Szybki Start jest aplikacją jedno konto, jeśli chcesz utworzyć aplikację wielu konta, musisz zdefiniować logikę w celu identyfikacji konta, którego należy użyć dla żądania tokenu `applicationContext.account(forHomeAccountId: self.homeAccountId)` |

## <a name="next-steps"></a>Kolejne kroki

Wypróbuj samouczek z systemem iOS w kompletny przewodnik krok po kroku dotyczących tworzenia aplikacji, w tym pełne wyjaśnienie tego przewodnika Szybki Start.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Dowiedz się więcej na temat czynności, które są wymagane to utworzenia aplikacji użytej w tym przewodniku Szybki start

> [!div class="nextstepaction"]
> [Call Graph API iOS tutorial (Samouczek dotyczący wywoływania interfejsu API programu Graph dla systemu iOS)](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]