---
title: Microsoft identity platformy systemu Android Przewodnik Szybki Start | Azure
description: Dowiedz się, jak dla systemu Android aplikacje mogą wywołać interfejs API, które wymagają tokenów dostępu przez punkt końcowy platforma tożsamości firmy Microsoft.
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
ms.date: 04/26/2019
ms.author: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 547eafac8cc1acf2b60416f93804e819a1c549b0
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702762"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Szybki start: logowanie użytkowników i wywoływanie interfejsu API programu Microsoft Graph z poziomu aplikacji dla systemu Android

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Ten przewodnik Szybki start zawiera przykładowy kod, który pokazuje, jak aplikacja systemu Android umożliwia logowanie na kontach osobistych i służbowych, uzyskiwanie tokenów dostępu i wywoływanie interfejsu API programu Microsoft Graph.

![Pokazuje, jak działa przykładowej aplikacji wygenerowane przez ten przewodnik Szybki Start](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Wymagania wstępne**
> * Android Studio 
> * System android 16 i jest wymagana 


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> Istnieją dwie opcje uruchamiania aplikacji Szybki start:
> * [Ekspresowo] [Opcja 1: zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ręcznie] [Opcja 2: zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1: zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
> #### <a name="step-1-register-your-application"></a>Krok 1: Rejestrowanie aplikacji
> Aby zarejestrować aplikację,
> 1. Przejdź do nowego [witryna Azure portal — rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AndroidQuickstartPage/sourceType/docs) okienka.
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację za pomocą tylko jednego kliknięcia.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2: zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Rejestrowanie aplikacji
> Aby ręcznie zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Przejdź do platforma tożsamości firmy Microsoft dla deweloperów [rejestracje aplikacji](https://aka.ms/MobileAppReg) strony.
> 1. Wybierz **nowej rejestracji**.
> 1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
>      - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `AndroidQuickstart`.
>      - Inne konfiguracje można pominąć na tej stronie. 
>      - Trafienia `Register` przycisku.
> 1. Kliknij nową aplikację > Przejdź do `Authentication`  >  `Add Platform`  >  `Android`.    
>      - Wprowadź nazwę pakietu z projektu w programie Android studio. 
>      - Generowanie wartości skrótu podpisu. Odnoszą się do portalu, aby uzyskać instrukcje.
> 1. Wybierz `Configure` i Zapisz ***konfiguracji biblioteki MSAL*** JSON na później. 

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Krok 1: Konfigurowanie aplikacji
> Dla przykładu kodu dla tego przewodnika Szybki Start do pracy należy dodać przekierowania URI zgodna z brokera uwierzytelniania. 
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź tę zmianę automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Already configured](media/quickstart-v2-android/green-check.png) (Już skonfigurowano) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów

#### <a name="step-2-download-the-project"></a>Krok 2: Pobieranie projektu

* [Pobierz przykładowy kod](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Krok 3: Konfigurowanie projektu

> [!div renderon="docs"]
> Jeśli została wybrana opcja 1 powyżej, można pominąć tę procedurę. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Wyodrębnij i otwórz projekt w programie Android Studio.
> 1. Wewnątrz **aplikacji** > **src** > **głównego** > **res**  >   **nieprzetworzone**, otwórz **auth_config.json**.
> 1. Edytuj **auth_config.json** i Zastąp kod JSON w witrynie Azure portal. Jeśli zamiast tego chcesz ręcznie wprowadzić zmiany:
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
> 1. Inside **app** > **manifests**, open  **AndroidManifest.xml**.
> 1. Paste the following activity to the **manifest\application** node: 
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
> > 1. Uruchamianie aplikacji! 

> [!div renderon="docs"]
> 1. Wyodrębnij i otwórz projekt w programie Android Studio.
> 1. Wewnątrz **aplikacji** > **res** > **pierwotne**, otwórz **auth_config.json**.
> 1. Edytuj **auth_config.json** i Zastąp kod JSON w witrynie Azure portal. Jeśli zamiast tego chcesz ręcznie wprowadzić te zmiany:
>    ```javascript
>    "client_id" : "ENTER_YOUR_APPLICATION_ID",
>    "redirect_uri": "ENTER_YOUR_REDIRECT_URI", 
>     ```
> 1. Wewnątrz **aplikacji** > **manifesty**, otwórz **AndroidManifest.xml**.
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
> 1. Zastąp `Enter_the_Package_Name` i `Enter_the_Signature_Hash` z wartościami zarejestrowanymi w witrynie Azure portal. 
> 1. Uruchamianie aplikacji! 

## <a name="more-information"></a>Więcej informacji

Przeczytaj poniższe sekcje, aby uzyskać więcej informacji o tym przewodniku Szybki start.

### <a name="getting-msal"></a>Pobieranie biblioteki MSAL

Biblioteka MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) to biblioteka używane do logowania użytkowników i żądać tokenów, które umożliwiają dostęp do interfejsu API chronionego przez platforma tożsamości usługi Microsoft. Można użyć narzędzia Gradle 3.0 lub go zainstalować, dodając następujące **skryptów narzędzia Gradle** > **build.gradle (moduł: aplikacji)** w obszarze **zależności**:

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
> |`R.raw.auth_config` | Ten plik zawiera konfiguracje m.in. identyfikator klienta aplikacji/Sign in odbiorców, identyfikator URI przekierowania i kilka innych opcji dostosowywania aplikacji. |

### <a name="requesting-tokens"></a>Przesyłanie żądań tokenów

Biblioteka MSAL oferuje dwie metody uzyskiwania tokenów: `acquireToken` i `acquireTokenSilentAsync`

#### <a name="acquiretoken-getting-a-token-interactively"></a>acquireToken: Uzyskanie tokenu interaktywnie

Sytuacje wymagają użytkownikom na interakcję z platformą Microsoft identity. W takich przypadkach użytkownik końcowy może wymagać wybierz swoje konto, wprowadź swoje poświadczenia lub wyrazić zgodę na uprawnienia, które zażądał aplikacji. Na przykład 

* Gdy nowi użytkownicy logują się do aplikacji po raz pierwszy.
* Jeśli użytkownik resetuje hasła, należy wprowadzić swoje poświadczenia 
* Po odebraniu zgody 
* Jeśli aplikacja wymaga jawnego zgody. 
* Gdy aplikacja żąda dostępu do zasobu po raz pierwszy
* Gdy wymagane są usługi MFA lub innymi zasadami dostępu warunkowego

```java
sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
```

> |Gdzie:||
> |---------|---------|
> | `SCOPES` | Zawiera żądane zakresy (czyli `{ "user.read" }` dla programu Microsoft Graph lub `{ "<Application ID URL>/scope" }` dla niestandardowych interfejsów internetowych API, np. `api://<Application ID>/access_as_user`) |
> | `getAuthInteractiveCallback` | Wywołanie zwrotne wykonywane, gdy kontrola zostanie przekazana z powrotem do aplikacji po uwierzytelnieniu |

#### <a name="acquiretokensilent-getting-a-user-token-silently"></a>acquireTokenSilent: Dyskretne pobieranie tokenu użytkownika

Aplikacje nie wymagają użytkownikom zalogować się za każdym razem, gdy będą one żądać tokenu. Jeśli użytkownik ma już logowanie, ta metoda umożliwia aplikacji, aby żądać tokenów w trybie dyskretnym.

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
> | `getAccounts(...)` | Zawiera konta próbujesz uzyskać tokenów dla w trybie dyskretnym |
> | `getAuthSilentCallback()` | Wywołanie zwrotne wykonywane, gdy kontrola zostanie przekazana z powrotem do aplikacji po uwierzytelnieniu |

## <a name="next-steps"></a>Następne kroki

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Dowiedz się więcej na temat czynności, które są wymagane to utworzenia aplikacji użytej w tym przewodniku Szybki start

Wypróbuj samouczek systemu Android, aby uzyskać instrukcje krok po kroku dotyczące tworzenia aplikacji i nowych funkcji, w tym pełne objaśnienie informacji zawartych w tym przewodniku Szybki start.

> [!div class="nextstepaction"]
> [Samouczek: wywołanie interfejsu API programu Graph dla systemu Android](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>Strona typu wiki: biblioteka MSAL dla systemu Android

Przeczytaj więcej informacji na temat biblioteki MSAL dla systemu Android:

> [!div class="nextstepaction"]
> [Strona typu wiki: biblioteka MSAL dla systemu Android](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
