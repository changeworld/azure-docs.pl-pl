---
title: Microsoft Identity platform Android — szybki start | Azure
description: Dowiedz się, jak aplikacje dla systemu Android mogą wywołać interfejs API, który wymaga tokenów dostępu w punkcie końcowym platformy tożsamości firmy Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2019
ms.author: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c22d93d39f086aaa6e2f103d3becbe9376b49b0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324545"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Szybki start: logowanie użytkowników i wywoływanie interfejsu API programu Microsoft Graph z poziomu aplikacji dla systemu Android

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Ten przewodnik Szybki start zawiera przykładowy kod, który pokazuje, jak aplikacja systemu Android umożliwia logowanie na kontach osobistych i służbowych, uzyskiwanie tokenów dostępu i wywoływanie interfejsu API programu Microsoft Graph.

![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten przewodnik Szybki Start](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Wymagania wstępne**
> * Android Studio 
> * Wymagany jest system Android 16 + 


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> Istnieją dwie opcje uruchamiania aplikacji Szybki start:
> * [Ekspresowo] [Opcja 1: zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ręcznie] [Opcja 2: zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1: zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
> #### <a name="step-1-register-your-application"></a>Krok 1: Rejestrowanie aplikacji
> Aby zarejestrować aplikację,
> 1. Przejdź do nowego okienka [Azure Portal-rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AndroidQuickstartPage/sourceType/docs) .
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację za pomocą tylko jednego kliknięcia.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2: zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Rejestrowanie aplikacji
> Aby ręcznie zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Przejdź do strony Microsoft Identity Platform for Developers [rejestracje aplikacji](https://aka.ms/MobileAppReg) .
> 1. Wybierz pozycję **Nowa rejestracja**.
> 1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
>      - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `AndroidQuickstart`.
>      - Na tej stronie możesz pominąć inne konfiguracje. 
>      - `Register` Naciśnij przycisk.
> 1. Kliknij nową aplikację > przejdź do `Authentication`. `Add Platform`  >   >  `Android`    
>      - Wprowadź nazwę pakietu z projektu programu Android Studio. 
>      - Generuj skrót sygnatury. Aby uzyskać instrukcje, zapoznaj się z portalem.
> 1. Wybierz `Configure` i Zapisz kod JSON ***konfiguracji MSAL*** dla późniejszej wersji. 

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Krok 1: Konfigurowanie aplikacji
> Aby przykład kodu dla tego przewodnika Szybki Start działał, należy dodać identyfikator URI przekierowania zgodny z brokerem uwierzytelniania. 
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź tę zmianę automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Already configured](media/quickstart-v2-android/green-check.png) (Już skonfigurowano) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów

#### <a name="step-2-download-the-project"></a>Krok 2: Pobieranie projektu

* [Pobierz przykład kodu](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Krok 3: Konfigurowanie projektu

> [!div renderon="docs"]
> W przypadku wybrania opcji 1 powyżej można pominąć te kroki. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Wyodrębnij i otwórz projekt w programie Android Studio.
> 1. W programie **App** > **src** Main res RAW Otwórz plik auth_config. JSON. >  >  > 
> 1. Edytuj plik **auth_config. JSON** i zastąp go kodem json z Azure Portal. Jeśli zamiast tego chcesz ręcznie wprowadzić zmiany:
>    ```javascript
>    {
>       "client_id" : "Enter_the_Application_Id_Here",
>       "authorization_user_agent" : "DEFAULT",
>       "redirect_uri" : "Enter_the_Redirect_Uri_Here",
>       "authorities" : [
>          {
>             "type": "AAD",
>             "audience": {
>                "type": "Enter_the_Audience_Info_Here",
>                "tenant_id": "Enter_the_Tenant_Info_Here"
>             }
>          }
>       ]
>    }
>    ```
> 
> 1. Wewnątrz manifestów **aplikacji** > Otwórz **pliku AndroidManifest. XML**.
> 1. Wklej następujące działanie do węzła **manifest\application** : 
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
>            <data android:scheme="msauth"
>                android:host="Enter_the_Package_Name"
>                android:path="/Enter_the_Signature_Hash" />
>        </intent-filter>
>    </activity>
>    ```
> 1. Uruchom aplikację. 

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Ten przewodnik Szybki Start obsługuje Enter_the_Supported_Account_Info_Here.

> [!div renderon="docs"]
> 1. Wyodrębnij i otwórz projekt w programie Android Studio.
> 1. Wewnątrz **App** > **res** RAW Otwórz plik auth_config. JSON. > 
> 1. Edytuj plik **auth_config. JSON** i zastąp go kodem json z Azure Portal. Jeśli zamiast tego chcesz ręcznie wprowadzić te zmiany:
>    ```javascript
>    "client_id" : "ENTER_YOUR_APPLICATION_ID",
>    "redirect_uri": "ENTER_YOUR_REDIRECT_URI", 
>     ```
> 1. Wewnątrz manifestów **aplikacji** > Otwórz **pliku AndroidManifest. XML**.
> 1. Dodaj następujące działanie do węzła **manifest\application**. Ten fragment kodu rejestruje działanie **BrowserTabActivity**, aby umożliwić systemowi operacyjnemu wznowienie działania aplikacji po zakończeniu uwierzytelniania:
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
>            <data android:scheme="msauth"
>                android:host="Enter_the_Package_Name"
>                android:path="/Enter_the_Decoded_Signature_Hash" />
>        </intent-filter>
>    </activity>
>    ```
> 1. `Enter_the_Package_Name` Zastąp `Enter_the_Signature_Hash` wartości i wartościami zarejestrowanymi w Azure Portal. 
> 1. Uruchom aplikację. 

## <a name="more-information"></a>Więcej informacji

Przeczytaj poniższe sekcje, aby uzyskać więcej informacji o tym przewodniku Szybki start.

### <a name="getting-msal"></a>Pobieranie MSAL

MSAL ([com. Microsoft. Identity. Client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) to biblioteka służąca do logowania użytkowników i żądania tokenów używanych w celu uzyskania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft. Możesz użyć programu Gradle 3.0 +, aby go zainstalować, dodając następujące elementy w **Gradle scripts** > **Build. Gradle (module: App)** w obszarze **zależności**:

```gradle  
implementation 'com.android.volley:volley:1.1.1'
implementation 'com.microsoft.identity.client:msal:0.3.+'
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL

Aby dodać odwołanie do biblioteki MSAL, dodaj następujący kod:

```java
import com.microsoft.identity.client.*;
```

Następnie zainicjuj bibliotekę MSAL, używając następującego kodu:

```java
    sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);
```

> |Gdzie: ||
> |---------|---------|
> |`R.raw.auth_config` | Ten plik zawiera konfiguracje aplikacji, w tym identyfikator aplikacji/klienta, odbiorców logowania, identyfikator URI przekierowania i kilka innych opcji dostosowywania. |

### <a name="requesting-tokens"></a>Przesyłanie żądań tokenów

Biblioteka MSAL oferuje dwie metody uzyskiwania tokenów: `acquireToken` i `acquireTokenSilentAsync`

#### <a name="acquiretoken-getting-a-token-interactively"></a>acquireToken: Interaktywny uzyskiwanie tokenu

Niektóre sytuacje wymagają, aby użytkownicy mogli korzystać z platformy tożsamości firmy Microsoft. W takich przypadkach może być wymagane, aby użytkownik końcowy mógł wybrać swoje konto, wprowadzić swoje poświadczenia lub wyrazić zgodę na uprawnienia wymagane przez aplikację. Na przykład 

* Gdy nowi użytkownicy logują się do aplikacji po raz pierwszy.
* Jeśli użytkownik resetuje swoje hasło, będzie musiał wprowadzić swoje poświadczenia 
* Jeśli wyrażanie zgody zostało odwołane 
* Jeśli aplikacja jawnie wymaga zgody. 
* Gdy aplikacja żąda dostępu do zasobu po raz pierwszy
* Gdy wymagane jest uwierzytelnianie wieloskładnikowe lub inne zasady dostępu warunkowego

```java
sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
```

> |Gdzie:||
> |---------|---------|
> | `SCOPES` | Zawiera żądane zakresy (czyli `{ "user.read" }` dla programu Microsoft Graph lub `{ "<Application ID URL>/scope" }` dla niestandardowych interfejsów internetowych API, np. `api://<Application ID>/access_as_user`) |
> | `getAuthInteractiveCallback` | Wywołanie zwrotne wykonywane, gdy kontrola zostanie przekazana z powrotem do aplikacji po uwierzytelnieniu |

#### <a name="acquiretokensilent-getting-a-user-token-silently"></a>acquireTokenSilent: Dyskretne pobieranie tokenu użytkownika

Aplikacje nie powinny wymagać od użytkowników logowania się za każdym razem, gdy żądają tokenu. Jeśli użytkownik jest już zalogowany, ta metoda zezwala aplikacjom na żądanie tokenów dyskretnie.

```java
    sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
        @Override
        public void onAccountsLoaded(final List<IAccount> accounts) {

            if (!accounts.isEmpty()) {
                sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
            } else {
                /* No accounts */
            }
        }
    });
```

> |Gdzie:||
> |---------|---------|
> | `SCOPES` | Zawiera żądane zakresy (czyli `{ "user.read" }` dla programu Microsoft Graph lub `{ "<Application ID URL>/scope" }` dla niestandardowych interfejsów internetowych API, np. `api://<Application ID>/access_as_user`) |
> | `getAccounts(...)` | Zawiera konto, dla którego próbujesz uzyskać tokeny w trybie dyskretnym |
> | `getAuthSilentCallback()` | Wywołanie zwrotne wykonywane, gdy kontrola zostanie przekazana z powrotem do aplikacji po uwierzytelnieniu |

## <a name="next-steps"></a>Kolejne kroki

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Dowiedz się więcej na temat czynności, które są wymagane to utworzenia aplikacji użytej w tym przewodniku Szybki start

Wypróbuj samouczek systemu Android, aby uzyskać instrukcje krok po kroku dotyczące tworzenia aplikacji i nowych funkcji, w tym pełne objaśnienie informacji zawartych w tym przewodniku Szybki start.

> [!div class="nextstepaction"]
> [Samouczek: wywołanie interfejsu API programu Graph dla systemu Android](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>Strona typu wiki: biblioteka MSAL dla systemu Android

Przeczytaj więcej informacji na temat biblioteki MSAL dla systemu Android:

> [!div class="nextstepaction"]
> [Strona typu wiki: biblioteka MSAL dla systemu Android](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Pomóż nam ulepszyć platformę tożsamości firmy Microsoft. Powiedz nam, co myślisz, wykonując krótką ankietę z dwoma pytaniami.

> [!div class="nextstepaction"]
> [Microsoft Identity platform — ankieta](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)