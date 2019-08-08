---
title: Microsoft Identity platform iOS — Szybki Start | Azure
description: Dowiedz się, jak logować użytkowników i Microsoft Graph zapytań w aplikacji systemu iOS.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2019
ms.author: twhitney
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e6a9dab8a2a37d7fa312b525453683fe789269b
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852949"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-app"></a>Szybki start: Logowanie użytkowników i wywołanie interfejsu API programu Microsoft Graph z aplikacji systemu iOS

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Ten przewodnik Szybki start zawiera przykładowy kod, który pokazuje, w jaki sposób można logować się na kontach osobistych i służbowych, uzyskiwać tokeny dostępu i wywoływać interfejs API programu Microsoft Graph z poziomu aplikacji natywnej dla systemu iOS.

![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten przewodnik Szybki Start](media/quickstart-v2-ios/ios-intro.svg)

> [!NOTE]
> **Wymagania wstępne**
> * XCode 10 +
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
> 1. Przejdź do nowego okienka [Azure Portal-rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs) .
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację za pomocą tylko jednego kliknięcia.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2: zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Zarejestruj swoją aplikację
> Aby ręcznie zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Przejdź do strony Microsoft Identity Platform for Developers [rejestracje aplikacji](https://aka.ms/MobileAppReg) .
> 1. Wybierz pozycję **Nowa rejestracja**.
> 1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
>      - W sekcji **Nazwa** wprowadź zrozumiałą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji podczas logowania lub wyrażania zgody na aplikację, na przykład `iOSQuickstart`.
>      - Pomiń inne konfiguracje na tej stronie. 
>      - `Register` Naciśnij przycisk.
> 1. Kliknij nową aplikację > przejdź do `Authentication`. `Add Platform`  >   >  `iOS`    
>      - Wprowadź ***Identyfikator pakietu*** dla swojej aplikacji. 
> 1. Wybierz `Configure` i Zapisz szczegóły ***konfiguracji MSAL*** w przyszłości. 

> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>Krok 1: Konfigurowanie aplikacji
> Aby przykład kodu dla tego przewodnika Szybki Start działał, należy dodać identyfikator URI przekierowania zgodny z brokerem uwierzytelniania. 
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź tę zmianę automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Already configured](media/quickstart-v2-ios/green-check.png) (Już skonfigurowano) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów

#### <a name="step-2-download-your-web-server-or-project"></a>Krok 2: Pobieranie serwera internetowego lub projektu

- [Pobierz przykład kodu](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Krok 3: Konfigurowanie projektu

> [!div renderon="docs"]
> W przypadku wybrania opcji 1 powyżej można pominąć te kroki. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Wyodrębnij plik zip i otwórz projekt w programie XCode.
> 1. Edytuj plik **ViewController.swift** i zastąp wiersz rozpoczynający się od ciągu „let kClientID” następującym fragmentem kodu:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_here"
>    let kAuthority = "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
>    ``` 
> 1. Kliknij prawym przyciskiem myszy pozycję **info. plist** i wybierz pozycję **Otwórz jako** > **kod źródłowy**.
> 1. W węźle głównym DICT Zastąp ***identyfikatorem pakietu***:
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
> 1. Kompilacja & uruchomić aplikację. 

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Ten przewodnik Szybki Start obsługuje Enter_the_Supported_Account_Info_Here.

> [!div renderon="docs"]
>
> 1. Wyodrębnij plik zip i otwórz projekt w programie XCode.
> 1. Edytuj **plik viewcontroller. Swift** i Zastąp wiersz zaczynający się od "Let kClientID" następującym fragmentem kodu:
>
>    ```swift
>    let kClientID = "<ENTER_YOUR_APPLICATION/CLIENT_ID>"
> 
>    ```
> 1. Kliknij prawym przyciskiem myszy pozycję **info. plist** i wybierz pozycję **Otwórz jako** > **kod źródłowy**.
> 1. W węźle głównym DICT Zastąp ***identyfikatorem pakietu***:
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
> 1. Kompilacja & uruchomić aplikację. 

## <a name="more-information"></a>Więcej informacji

Zapoznaj się z następującymi sekcjami, aby dowiedzieć się więcej na temat tego przewodnika Szybki start.

### <a name="getting-msal"></a>Pobieranie MSAL

MSAL ([MSAL. Framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) to biblioteka służąca do logowania użytkowników i żądania tokenów używanych w celu uzyskania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft. Możesz dodać bibliotekę MSAL do aplikacji w następujący sposób:

```
$ vi Podfile

```
Dodaj następujący element do tego plik podfile (z celem projektu):

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
> | `authority` | Punkt końcowy platformy tożsamości firmy Microsoft. W większości przypadków będzie to adres *https<span/>://login.microsoftonline.com/common* |
> | `redirectUri` | Identyfikator URI przekierowania aplikacji. Można przekazać element "Nil", aby używał wartości domyślnej, lub niestandardowego identyfikatora URI przekierowania. |

### <a name="additional-app-requirements"></a>Dodatkowe wymagania dotyczące aplikacji  

Twoja aplikacja musi mieć również następujące elementy `AppDelegate`. Dzięki temu MSAL SDK obsługuje odpowiedzi tokenów z aplikacji brokera uwierzytelniania podczas przeprowadzania uwierzytelniania.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
         guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
             return false
         }
         
         return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
     }

```

Na koniec aplikacja musi mieć wpis `LSApplicationQueriesSchemes` ***info.*** `CFBundleURLTypes`plist obok elementu. Przykład zawiera. 

   ```xml 
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauth</string>
      <string>msauthv2</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Użytkownicy logowania & tokeny żądania

Biblioteka MSAL oferuje dwie metody uzyskiwania tokenów: `acquireToken` i `acquireTokenSilent`.

#### <a name="acquiretoken-getting-a-token-interactively"></a>acquireToken: Interaktywny uzyskiwanie tokenu

Niektóre sytuacje wymagają, aby użytkownicy mogli korzystać z platformy tożsamości firmy Microsoft. W takich przypadkach może być wymagane, aby użytkownik końcowy mógł wybrać swoje konto, wprowadzić swoje poświadczenia lub wyrazić zgodę na uprawnienia aplikacji. Na przykład 

* Gdy nowi użytkownicy logują się do aplikacji po raz pierwszy.
* Jeśli użytkownik resetuje swoje hasło, będzie musiał wprowadzić swoje poświadczenia 
* Gdy aplikacja żąda dostępu do zasobu po raz pierwszy
* Gdy wymagane jest uwierzytelnianie wieloskładnikowe lub inne zasady dostępu warunkowego

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
applicationContext.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Gdzie:||
> |---------|---------|
> | `scopes` | Zawiera żądane zakresy (czyli `[ "user.read" ]` dla Microsoft Graph lub `[ "<Application ID URL>/scope" ]` niestandardowych interfejsów API sieci Web (`api://<Application ID>/access_as_user`) |

#### <a name="acquiretokensilent-getting-an-access-token-silently"></a>acquireTokenSilent: Dyskretne uzyskiwanie tokenów dostępu

Aplikacje nie powinny wymagać od użytkowników logowania się za każdym razem, gdy żądają tokenu. Jeśli użytkownik jest już zalogowany, ta metoda zezwala aplikacjom na żądanie tokenów dyskretnie. 

```swift
let parameters = MSALSilentTokenParameters(scopes: kScopes, account: applicationContext.allAccounts().first)
applicationContext.acquireTokenSilent(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Gdzie: ||
> |---------|---------|
> | `scopes` | Zawiera żądane zakresy (czyli `[ "user.read" ]` dla Microsoft Graph lub `[ "<Application ID URL>/scope" ]` niestandardowych interfejsów API sieci Web (`api://<Application ID>/access_as_user`) |
> | `account` | Konto, dla którego jest żądany token. Ten przewodnik Szybki Start jest aplikacją o pojedynczym koncie, jeśli chcesz utworzyć aplikację wielokontową, aby określić, które konto ma być używane dla żądań tokenów`applicationContext.account(forHomeAccountId: self.homeAccountId)` |

## <a name="next-steps"></a>Następne kroki

Wypróbuj Samouczek systemu iOS, aby zapoznać się z kompletnym przewodnikiem krok po kroku dotyczącym tworzenia aplikacji, w tym pełnym objaśnieniem tego samouczka Szybki Start.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Dowiedz się więcej na temat czynności, które są wymagane to utworzenia aplikacji użytej w tym przewodniku Szybki start

> [!div class="nextstepaction"]
> [Call Graph API iOS tutorial (Samouczek dotyczący wywoływania interfejsu API programu Graph dla systemu iOS)](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Pomóż nam ulepszyć platformę tożsamości firmy Microsoft. Powiedz nam, co myślisz, wykonując krótką ankietę z dwoma pytaniami.

> [!div class="nextstepaction"]
> [Microsoft Identity platform — ankieta](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)