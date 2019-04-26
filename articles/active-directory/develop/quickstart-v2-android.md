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
ms.date: 04/11/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1f174229da565627c0e5791f53031b338880cb3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60299032"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Szybki start: logowanie użytkowników i wywoływanie interfejsu API programu Microsoft Graph z poziomu aplikacji dla systemu Android

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Ten przewodnik Szybki start zawiera przykładowy kod, który pokazuje, jak aplikacja systemu Android umożliwia logowanie na kontach osobistych i służbowych, uzyskiwanie tokenów dostępu i wywoływanie interfejsu API programu Microsoft Graph.

![Pokazuje, jak działa przykładowej aplikacji wygenerowane przez ten przewodnik Szybki Start](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Wymagania wstępne**
> * Android Studio 3+
> * System android 21 i jest wymagana 


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
> 1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
> 1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
> 1. Przejdź do platforma tożsamości firmy Microsoft dla deweloperów [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) strony.
> 1. Wybierz **nowej rejestracji**.
> 1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
>      - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `Android-Quickstart`.
>      - Trafienia `Register` przycisku.
> 1. Przejdź do `Authentication`  >  `Redirect URIs`  >  `Suggested Redirect URIs for public clients`i wybierz identyfikator URI przekierowania, format **msal {AppId} :/ / auth**. Zapisz zmianę.


> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Krok 1: Konfigurowanie aplikacji
> Aby przykładowy kod z tego przewodnika Szybki start działał, musisz dodać adres URL odpowiedzi w formie **msal{AppId}://auth** (gdzie {AppId} jest identyfikatorem Twojej aplikacji).
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź tę zmianę automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Already configured](media/quickstart-v2-android/green-check.png) (Już skonfigurowano) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów

#### <a name="step-2-download-the-project"></a>Krok 2: Pobieranie projektu

* [Pobierz projekt programu Android Studio](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Krok 3: Konfigurowanie projektu

> [!div renderon="docs"]
> Jeśli została wybrana opcja 1 powyżej, można pominąć tę procedurę. Otwórz projekt w programie Android Studio i uruchomić aplikację. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Wyodrębnij i otwórz projekt w programie Android Studio.
> 1. Wewnątrz **aplikacji** > **res** > **pierwotne**, otwórz **auth_config.json**.
> 1. Edytuj **auth_config.json** i Zastąp `client_id` i `tenant_id`:
>    ```javascript
>    "client_id" : "Enter_the_Application_Id_Here",
>    "type": "Enter_the_Audience_Info_Here",
>    "tenant_id" : "Enter_the_Tenant_Info_Here"
>    ```
> 1. Wewnątrz **aplikacji** > **manifesty**, otwórz **AndroidManifest.xml**.
> 1. Dodaj następujące działanie do węzła **manifest\application**. Ten kod umożliwia firmie Microsoft do wywołania zwrotnego do aplikacji:   
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
> 
>            <!--Add in your scheme/host from registered redirect URI-->
>            <!--By default, the scheme should be similar to 'msal[appId]' -->
>            <data android:scheme="msalEnter_The_Application_Id_Here"
>                android:host="auth" />
>        </intent-filter>
>    </activity>
>    ```

> [!div renderon="docs"]
> 1. Wyodrębnij i otwórz projekt w programie Android Studio.
> 1. Wewnątrz **aplikacji** > **res** > **pierwotne**, otwórz **auth_config.json**.
> 1. Edytuj **auth_config.json** i Zastąp `client_id` i `redirect_uri`:
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
> 
>            <!--Add in your scheme/host from registered redirect URI-->
>            <!--By default, the scheme should be similar to 'msal[appId]' -->
>            <data android:scheme="msal<ENTER_YOUR_APPLICATION_ID>"
>                android:host="auth" />
>        </intent-filter>
>    </activity>
>    ```
> 1. Zastąp ciąg `<ENTER_THE_APPLICATION_ID_HERE>` *identyfikatorem aplikacji*. Jeśli chcesz znaleźć *identyfikator aplikacji*, przejdź do strony *Omówienie*.

## <a name="more-information"></a>Więcej informacji

Przeczytaj poniższe sekcje, aby uzyskać więcej informacji o tym przewodniku Szybki start.

### <a name="msal"></a>BIBLIOTEKA MSAL

Biblioteka MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) to biblioteka używane do logowania użytkowników i żądać tokenów, które umożliwiają dostęp do interfejsu API chronionego przez platforma tożsamości usługi Microsoft. Można ją zainstalować przy użyciu programu Gradle, dodając poniższy w obszarze **Gradle Scripts** > **build.gradle (Module: app)** w sekcji **Dependencies** (Zależności):

```gradle  
implementation 'com.android.volley:volley:1.1.1'
implementation 'com.microsoft.identity.client:msal:0.2.+'
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
> |`R.raw.auth_config` | Ten plik zawiera konfiguracje dla aplikacji w tym Twoja nazwa aplikacji/klienta, logowanie odbiorców i kilka innych opcji dostosowywania. |

### <a name="requesting-tokens"></a>Przesyłanie żądań tokenów

Biblioteka MSAL oferuje dwie metody uzyskiwania tokenów: `acquireToken` i `acquireTokenSilentAsync`

#### <a name="getting-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

Sytuacje wymagają wymuszanie użytkownikom na interakcję z końcowym platforma tożsamości firmy Microsoft, co spowodowało kontekst przełączyć się do przeglądarki systemu, aby to zweryfikować poświadczeń użytkownika, lub o wyrażenie zgody. Oto niektóre przykłady:

* Gdy nowi użytkownicy logują się do aplikacji po raz pierwszy.
* Gdy użytkownicy muszą ponownie wprowadzić poświadczenia, ponieważ hasło wygasło.
* Gdy aplikacja żąda dostępu do zasobów wymagającego zgody użytkownika.
* Gdy wymagane jest uwierzytelnianie dwuetapowe.

```java
sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
```

> |Gdzie:||
> |---------|---------|
> | `SCOPES` | Zawiera żądane zakresy (czyli `{ "user.read" }` dla programu Microsoft Graph lub `{ "<Application ID URL>/scope" }` dla niestandardowych interfejsów internetowych API, np. `api://<Application ID>/access_as_user`) |
> | `getAuthInteractiveCallback` | Wywołanie zwrotne wykonywane, gdy kontrola zostanie przekazana z powrotem do aplikacji po uwierzytelnieniu |

#### <a name="getting-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

Nie chcesz, aby użytkownik musiał weryfikować swoje poświadczenia za każdym razem, gdy musi uzyskać dostęp do zasobu. Przez większość czasu uzyskiwanie i odnawianie tokenów powinno odbywać się bez żadnej interakcji z użytkownikiem. Możesz użyć metody `AcquireTokenSilentAsync` do uzyskiwania tokenów umożliwiających dostęp do chronionych zasobów po początkowym skorzystaniu z metody `acquireToken`:

```java
List<IAccount> accounts = sampleApp.getAccounts();
if (sample.size() == 1) {
    sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
} else {
    // No or multiple accounts
}
```

> |Gdzie:||
> |---------|---------|
> | `SCOPES` | Zawiera żądane zakresy (czyli `{ "user.read" }` dla programu Microsoft Graph lub `{ "<Application ID URL>/scope" }` dla niestandardowych interfejsów internetowych API, np. `api://<Application ID>/access_as_user`) |
> | `accounts.get(0)` | Zawiera konta próbujesz uzyskać tokenów dla w trybie dyskretnym |
> | `getAuthInteractiveCallback` | Wywołanie zwrotne wykonywane, gdy kontrola zostanie przekazana z powrotem do aplikacji po uwierzytelnieniu |

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
