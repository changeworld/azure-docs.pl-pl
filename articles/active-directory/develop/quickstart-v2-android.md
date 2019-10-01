---
title: Microsoft Identity platform Android — szybki start | Azure
description: Dowiedz się, jak aplikacje dla systemu Android mogą wywołać interfejs API, który wymaga tokenów dostępu w punkcie końcowym platformy tożsamości firmy Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2019
ms.author: twhitney
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: e11e47952f70ce0cd212ca93eff1c38f2b3993a8
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71678049"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Szybki Start: Logowanie użytkowników i wywoływanie interfejsu API Microsoft Graph z poziomu aplikacji systemu Android

Ten przewodnik Szybki Start używa przykładu kodu, aby zademonstrować, jak aplikacja systemu Android może zalogować się do konta osobistego, służbowego lub szkolnego, a następnie uzyskać token dostępu i wywołać interfejs API Microsoft Graph.

![Screenshoft aplikacji przykładowej](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Wymagania wstępne**
> * Android Studio 
> * Wymagany jest system Android 16 +

## <a name="step-1-get-the-sample-app"></a>Krok 1. Uzyskiwanie przykładowej aplikacji

[Klonuj kod](https://github.com/Azure-Samples/ms-identity-android-java.git).

## <a name="step-2-register-your-application"></a>Krok 2. rejestrowanie aplikacji

Aby zarejestrować obiekt aplikacji i ręcznie dodać informacje o rejestracji obiektu aplikacji do przykładowego projektu, wykonaj następujące kroki:

1. Przejdź do [Azure Portal](https://aka.ms/MobileAppReg).
1. Otwórz [blok rejestracje aplikacji](https://portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) i kliknij pozycję **+ Nowa rejestracja**.
1. Wprowadź **nazwę** rejestracji aplikacji, a następnie, bez ustawienia identyfikatora URI przekierowania, kliknij pozycję **zarejestruj**.
1. W sekcji **Zarządzanie** wybierz pozycję **uwierzytelnianie** >  **+ Dodaj platformę** > **Android**. (Może być konieczne wybranie pozycji **Wypróbuj nowe środowisko** w górnej części bloku, aby zobaczyć ten ekran)
1. Wprowadź **nazwę pakietu**projektu, która jest `com.azuresamples.msalandroidapp`.
1. W sekcji **skrót podpisu** na stronie **Konfigurowanie aplikacji systemu Android** kliknij pozycję **generowanie skrótu sygnatury deweloperskiej**i skopiuj polecenie Narzędzia klucza, które ma być używane w przypadku platformy używanej do tworzenia aplikacji systemu Android.

   > [!Note]
   > Narzędzie narzêdzi. exe jest instalowane w ramach zestawu Java Development Kit (JDK). Należy również zainstalować narzędzie OpenSSL, aby wykonać polecenie Narzędzia.  W ścieżce będziesz potrzebować narzędzia i katalogu OpenSSL\bin.

1. Uruchom narzędzie narzędziowe skopiowane z portalu w oknie terminalu.
1. Wprowadź wygenerowany skrót sygnatury do portalu w obszarze **skrót sygnatury**.
1. Kliknij `Configure` i Utwórz kopię **konfiguracji MSAL**. Skopiujesz go i wkleisz w pliku konfiguracji w następnym kroku. Kliknij przycisk **Gotowe**.

## <a name="step-3-add-your-app-registration"></a>Krok 3. Dodawanie rejestracji aplikacji

1. Otwórz przykładowy projekt w Android Studio.
1. W **aplikacji** > **res** > **RAW**, Otwórz plik **auth_config_multiple_account. JSON**.  Wklej zawartość konfiguracji MSAL. Spowoduje to dodanie identyfikatora klienta, identyfikatora dzierżawy i redirect_uri z portalu. Będzie wyglądać podobnie do tego, ale z wartościami wypełnionymi dla identyfikatora klienta, identyfikatora dzierżawy i redirect_uri:

    ```json
    {
      "client_id" : "<your_client_id_here>",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "<your_redirect_uri_here>",
      "account_mode" : "MULTIPLE",
      "broker_redirect_uri_registered": true,
      "authorities" : [
        {
          "type": "AAD",
          "audience": {
            "type": "AzureADandPersonalMicrosoftAccount",
            "tenant_id": "common"
          }
        }
      ]
    }
    ```

1. Otwórz **aplikację** > **res** > **RAW**, Otwórz plik **AUTH_CONFIG_SINGLE_ACCOUNT. JSON**i wklej zawartość konfiguracji MSAL. Będzie wyglądać podobnie do pliku **auth_config_multiple_account. JSON** .
1. W **aplikacji** > **manifesty** > **pliku AndroidManifest. XML**, Znajdź działanie @no__t 5. Ten wpis umożliwia firmie Microsoft wywoływanie z powrotem do aplikacji po zakończeniu uwierzytelniania:

    ```xml
    ...
    <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
                <intent-filter>
                    <action android:name="android.intent.action.VIEW" />
    
                    <category android:name="android.intent.category.DEFAULT" />
                    <category android:name="android.intent.category.BROWSABLE" />
    
                    <!--
                        Add in your scheme/host from registered redirect URI
                        note that the leading "/" is required for android:path
                        For Example:
                        <data
                            android:host="com.azuresamples.msalandroidapp"
                            android:path="/1wIqXSqBj7w+h11ZifsnqwgyKrY="
                            android:scheme="msauth" />
                    -->
    
                    <data
                        android:host="YOUR_PACKAGE_NAME - must be registered at https://aka.ms/MobileAppReg"
                        android:path="/YOUR_DECODED_SIGNATURE_HASH - must be registered at https://aka.ms/MobileAppReg"
                        android:scheme="msauth" />
                </intent-filter>
            </activity>
    ```
    
1. Zastąp nazwę pakietu identyfikatorem zarejestrowanego w Azure Portal dla wartości `android:host=`.  W takim przypadku będzie to: `com.azuresamples.msalandroidapp`.

    > [!IMPORTANT]
    > Wartość w systemie **Android: Path** **musi** mieć wiodący znak "/" lub pojawić się czerwona linia poniżej wartości, a Przykładowa aplikacja nie zostanie uruchomiona.
     
1. Zastąp skrót klucza uzyskany przez uruchomienie narzędzia wiersza wcześniej i wprowadzonym w Azure Portal, dla wartości `android:path=`. Wartość skrótu podpisu nie powinna być zakodowana w adresie URL.

## <a name="step-4-run-the-sample-app"></a>Krok 4. Uruchamianie przykładowej aplikacji

Wybierz z listy rozwijanej **dostępne urządzenia** Android Studio i uruchom aplikację.

Przykładowa aplikacja zostanie uruchomiona na ekranie **trybu pojedynczego konta** . Domyślny zakres, **User. Read**, jest dostarczany domyślnie, który jest używany podczas odczytywania własnych danych profilu podczas wywołania interfejsu API Microsoft Graph. Adres URL wywołania interfejsu API Microsoft Graph jest udostępniany domyślnie. Jeśli chcesz, możesz zmienić oba te elementy.

![Przykładowa aplikacja MSAL przedstawiająca użycie jednego i wielu kont](./media/quickstart-v2-android/quickstart-sample-app.png)

Użyj menu aplikacji do zmiany między trybami jednego i wielu kont.

W trybie jednego konta zaloguj się przy użyciu konta służbowego:

1. Wybierz pozycję **Pobierz dane grafu interaktywnie** , aby monitować użytkownika o poświadczenia. Wyniki wywołania interfejsu API Microsoft Graph będą widoczne w dolnej części ekranu.
2. Po zalogowaniu wybierz pozycję **Pobierz dane grafu w trybie dyskretnym** , aby wywołać interfejs API Microsoft Graph bez monitowania użytkownika o podanie poświadczeń ponownie. Wyniki wywołania interfejsu API Microsoft Graph będą widoczne w dolnej części ekranu.

W trybie wielu kont można powtórzyć te same czynności.  Ponadto możesz usunąć zalogowane konto, co spowoduje również usunięcie buforowanych tokenów dla tego konta.

## <a name="how-the-sample-works"></a>Jak działa przykład

Kod jest zorganizowany w fragmenty, które pokazują, jak napisać jedną i wiele kont aplikacji MSAL. Pliki kodu są zorganizowane w następujący sposób:

| Plik  | Demonstracje  |
|---------|---------|
| MainActivity | Zarządza interfejsem użytkownika |
| MSGraphRequestWrapper  | Wywołuje interfejs API Microsoft Graph przy użyciu tokenu dostarczonego przez MSAL |
| MultipleAccountModeFragment  | Inicjuje aplikację wielokontową, ładuje konto użytkownika i pobiera token do wywołania interfejsu API Microsoft Graph |
| SingleAccountModeFragment | Inicjuje aplikację pojedynczego konta, ładuje konto użytkownika i pobiera token do wywołania interfejsu API Microsoft Graph |
| res/auth_config_multiple_account. JSON  | Plik konfiguracji z wieloma kontami |
| res/auth_config_single_account. JSON  | Plik konfiguracji pojedynczego konta |
| Gradle scripts/Build. klasy (moduł: App) | Zależności biblioteki MSAL są tutaj dodawane |

Teraz będziemy bardziej szczegółowo przeglądać te pliki i wywoływać kod specyficzny dla MSAL w każdym z nich.

### <a name="add-msal-to-the-app"></a>Dodawanie MSAL do aplikacji

MSAL ([com. Microsoft. Identity. Client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) to biblioteka służąca do logowania użytkowników i żądania tokenów używanych w celu uzyskania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft. Gradle 3.0 + instaluje bibliotekę po dodaniu następującej do **Gradle skryptów** > **Build. Gradle (module: App)** w obszarze **zależności**:

```gradle  
implementation 'com.microsoft.identity.client:msal:1.0.0'
```

Można to zobaczyć w przykładowym projekcie w Build. Gradle (moduł: aplikacja):

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:1.0.0-RC7'
    ...
}
```

Powoduje to, że Gradle pobrać i skompilować MSAL z Maven Central.

### <a name="msal-imports"></a>MSAL Importy

Importy istotne dla biblioteki MSAL są `com.microsoft.identity.client.*`.  Na przykład zobaczysz `import com.microsoft.identity.client.PublicClientApplication;`, która jest przestrzenią nazw dla klasy `PublicClientApplication`, która reprezentuje publiczną aplikację kliencką.

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment. Java

Ten plik pokazuje, jak utworzyć aplikację MSAL z jednym kontem i wywołać interfejs API Microsoft Graph.

Aplikacje pojedynczego konta są używane tylko przez jednego użytkownika.  Na przykład możesz mieć tylko jedno konto, za pomocą którego możesz zalogować się do aplikacji mapowania.

#### <a name="single-account-msal-initialization"></a>Inicjalizacja MSAL jednego konta

W `onCreateView()` pojedyncze konto `PublicClientApplication` jest tworzone przy użyciu informacji konfiguracyjnych przechowywanych w pliku `auth_config_single_account.json`.  W ten sposób można zainicjować bibliotekę MSAL do użycia w aplikacji MSAL pojedynczego konta:

```java
...
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createSingleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_single_account,
        new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(ISingleAccountPublicClientApplication application) {
                /**
                    * This test app assumes that the app is only going to support one account.
                    * This requires "account_mode" : "SINGLE" in the config json file.
                    **/
                mSingleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                displayError(exception);
            }
        });
```

#### <a name="sign-in-a-user"></a>Zaloguj użytkownika

Kod służący do logowania użytkownika jest w `initializeUI()` w ramach procedury obsługi kliknij `signInButton`.

Wywołaj `signIn()` przed próbą uzyskania tokenów. `signIn()` zachowuje się tak, jakby została wywołana `acquireToken()`, co spowoduje wyświetlenie interakcyjnego monitu o zalogowanie użytkownika.

Logowanie użytkownika jest operacją asynchroniczną. Jest przesyłane wywołanie zwrotne, które wywołuje interfejs API Microsoft Graph i aktualizuje interfejs użytkownika po zalogowaniu się użytkownika:

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>Wylogowywanie użytkownika

Kod do wylogowania użytkownika jest w `initializeUI()` w ramach procedury obsługi kliknij `signOutButton`.  Podpisywanie użytkownika jest operacją asynchroniczną. Podpisywanie użytkownika spowoduje również wyczyszczenie pamięci podręcznej tokenów dla tego konta. Zostanie utworzone wywołanie zwrotne, aby zaktualizować interfejs użytkownika po wylogowaniu się konta użytkownika:

```java
mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
    @Override
    public void onSignOut() {
        updateUI(null);
        performOperationOnSignOut();
    }

    @Override
    public void onError(@NonNull MsalException exception) {
        displayError(exception);
    }
});
```

#### <a name="get-a-token-interactively-or-silently"></a>Uzyskaj token interaktywnie lub w trybie dyskretnym

Aby przedstawić najmniejszą liczbę monitów dla użytkownika, zazwyczaj otrzymujesz token w trybie dyskretnym. Następnie, jeśli wystąpi błąd, spróbuj uzyskać dostęp do tokenu interaktywnie. Podczas pierwszego wywołania aplikacji `signIn()` efektywnie działa jako wywołanie do `acquireToken()`, co spowoduje wyświetlenie monitu o podanie poświadczeń użytkownika.

Niektóre sytuacje, w których użytkownik może zostać poproszony o wybranie konta, wprowadzenie ich poświadczeń lub zgoda na uprawnienia wymagane przez aplikację:

* Przy pierwszym logowaniu użytkownika do aplikacji
* Jeśli użytkownik resetuje hasło, musi wprowadzić swoje poświadczenia
* Jeśli wyrażanie zgody zostało odwołane
* Jeśli aplikacja jawnie wymaga zgody
* Gdy aplikacja żąda dostępu do zasobu po raz pierwszy
* Gdy wymagane jest uwierzytelnianie wieloskładnikowe lub inne zasady dostępu warunkowego

Kod umożliwiający interaktywny dostęp do tokenu, który jest z INTERFEJSem użytkownika, który będzie obejmował użytkownika, jest w `initializeUI()` w ramach procedury obsługi kliknij `callGraphApiInteractiveButton`:

```java
/**
 * If acquireTokenSilent() returns an error that requires an interaction (MsalUiRequiredException),
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your Single Sign-On refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 */
mSingleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Jeśli użytkownik został już zalogowany, `acquireTokenSilentAsync()` zezwala aplikacjom na żądanie tokenów dyskretnie, jak pokazano w `initializeUI()`, w ramach procedury obsługi `callGraphApiSilentButton`:

```java
/**
  * Once you've signed the user in,
  * you can perform acquireTokenSilent to obtain resources without interrupting the user.
  */
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>Załaduj konto

Kod do załadowania konta jest w `loadAccount()`.  Załadowanie konta użytkownika jest operacją asynchroniczną, więc wywołania zwrotne do obsłużenia, gdy następuje załadowanie konta, zmiany lub błąd, są przesyłane do MSAL.  Poniższy kod obsługuje również `onAccountChanged()`, które występują po usunięciu konta, użytkownik zmieni inne konto i tak dalej.

```java
private void loadAccount() {
    ...

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }

        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
```

#### <a name="call-microsoft-graph"></a>Microsoft Graph wywołania

Gdy użytkownik jest zalogowany, wywołanie do Microsoft Graph jest nawiązywane za pośrednictwem żądania HTTP przez `callGraphAPI()`. Ta funkcja to otoka, która upraszcza przykład, wykonując pewne zadania, takie jak uzyskanie tokenu dostępu z `authenticationResult` i pakowanie wywołania do MSGraphRequestWrapper i wyświetlanie wyników wywołania.

```java
private void callGraphAPI(final IAuthenticationResult authenticationResult) {
    MSGraphRequestWrapper.callGraphAPIUsingVolley(
            getContext(),
            graphResourceTextView.getText().toString(),
            authenticationResult.getAccessToken(),
            new Response.Listener<JSONObject>() {
                @Override
                public void onResponse(JSONObject response) {
                    /* Successfully called graph, process data and send to UI */
                    ...
                }
            },
            new Response.ErrorListener() {
                @Override
                public void onErrorResponse(VolleyError error) {
                    ...
                }
            });
}
```

### <a name="auth_config_single_accountjson"></a>auth_config_single_account. JSON

Jest to plik konfiguracji aplikacji MSAL, który używa jednego konta.

Wyjaśnienie tych pól można znaleźć w temacie [Omówienie pliku konfiguracji MSAL systemu Android](msal-configuration.md) .

Zanotuj obecność `"account_mode" : "SINGLE"`, która konfiguruje tę aplikację w taki sposób, aby korzystała z jednego konta.

```json
{
  "client_id" : "<your_client_id_here>",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "<your_redirect_uri_here>",
  "account_mode" : "SINGLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

### <a name="multipleaccountmodefragmentjava"></a>MultipleAccountModeFragment. Java

Ten plik pokazuje, jak utworzyć aplikację MSAL z wieloma kontami i wywołać interfejs API Microsoft Graph. 

Przykładem aplikacji z wieloma kontami jest aplikacja poczty, która umożliwia współdziałanie z wieloma kontami użytkowników, takimi jak konto służbowe i konto osobiste.

#### <a name="multiple-account-msal-initialization"></a>Inicjowanie wielu kont MSAL

W `onCreateView()` obiekt aplikacji z wieloma kontami (`IMultipleAccountPublicClientApplication`) jest tworzony przy użyciu informacji konfiguracyjnych przechowywanych w `auth_config_multiple_account.json file`:

```java
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_multiple_account,
        new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(IMultipleAccountPublicClientApplication application) {
                mMultipleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                ...
            }
        });
```

Utworzony obiekt `MultipleAccountPublicClientApplication` jest przechowywany w zmiennej składowej klasy, dzięki czemu można go użyć do współdziałania z biblioteką MSAL w celu uzyskania tokenów i załadowania i usunięcia konta użytkownika.

#### <a name="load-an-account"></a>Załaduj konto

Wiele aplikacji konta zazwyczaj wywołuje `GetAccounts()`, aby wybrać konto, które ma być używane dla operacji MSAL. Kod do załadowania konta jest w `loadAccount()`.  Ładowanie konta użytkownika jest operacją asynchroniczną. Wywołanie zwrotne obsługuje sytuacje, w których konto jest załadowane, zmienia się lub występuje błąd.

```java
/**
    * Load the currently signed-in account, if there's any.
    * In the shared device mode, if the user is signed out from the device, the app can also perform the clean-up work in onAccountChanged().
    */
private void loadAccount() {
    ...
    mMultipleAccountApp.getAccounts(new IPublicClientApplication.LoadAccountsCallback() {
        @Override
        public void onTaskCompleted(final List<IAccount> result) {
            // You can use the account data to update your UI or your app database.
            accountList = result;
            updateUI(accountList);
        }

        @Override
        public void onError(MsalException exception) {
            displayError(exception);
        }
    });
}
```

#### <a name="get-a-token-interactively-or-silently"></a>Uzyskaj token interaktywnie lub w trybie dyskretnym

Niektóre sytuacje, w których użytkownik może zostać poproszony o wybranie konta, wprowadzenie ich poświadczeń lub zgoda na uprawnienia wymagane przez aplikację:

* Użytkownik po raz pierwszy loguje się do aplikacji
* Jeśli użytkownik resetuje hasło, musi wprowadzić swoje poświadczenia 
* Jeśli wyrażanie zgody zostało odwołane 
* Jeśli aplikacja jawnie wymaga zgody 
* Gdy aplikacja żąda dostępu do zasobu po raz pierwszy
* Gdy wymagane jest uwierzytelnianie wieloskładnikowe lub inne zasady dostępu warunkowego

Aplikacje z wieloma kontami powinny zwykle uzyskiwać tokeny interaktywnie, czyli interfejsem użytkownika, który obejmuje użytkownika, z wywołaniem do `acquireToken()`.  Kod umożliwiający interaktywny dostęp do tokenu znajduje się w `initializeUI()` w ramach procedury obsługi kliknij `callGraphApiInteractiveButton`:

```java
/**
 * Acquire token interactively. It will also create an account object for the silent call as a result (to be obtained by getAccount()).
 *
 * If acquireTokenSilent() returns an error that requires an interaction,
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your SSO refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 */
mMultipleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Aplikacje nie powinny wymagać od użytkownika logowania się za każdym razem, gdy żądają tokenu. Jeśli użytkownik został już zalogowany, `acquireTokenSilentAsync()` zezwala aplikacjom na żądanie tokenów bez monitowania użytkownika, jak pokazano w `initializeUI()` w programie obsługi `callGraphApiSilentButton`:

```java
/**
 * Performs acquireToken without interrupting the user.
 *
 * This requires an account object of the account you're obtaining a token for.
 * (can be obtained via getAccount()).
 */
mMultipleAccountApp.acquireTokenSilentAsync(getScopes(),
accountList.get(accountListSpinner.getSelectedItemPosition()),
AUTHORITY,
getAuthSilentCallback());
```

#### <a name="remove-an-account"></a>Usuwanie konta

Kod służący do usuwania konta i wszystkie tokeny w pamięci podręcznej dla konta jest w `initializeUI()` w programie obsługi dla przycisku Usuń konto. Aby można było usunąć konto, musisz mieć obiekt konta, który uzyskano z funkcji MSAL, takich jak `getAccounts()` i `acquireToken()`. Ponieważ usunięcie konta jest operacją asynchroniczną, podano wywołanie zwrotne `onRemoved` w celu zaktualizowania interfejsu użytkownika.

```java
/**
  * Removes the selected account and cached tokens from this app (or device, if the device is in shared mode).
  */
mMultipleAccountApp.removeAccount(accountList.get(accountListSpinner.getSelectedItemPosition()),
        new IMultipleAccountPublicClientApplication.RemoveAccountCallback() {
            @Override
            public void onRemoved() {
                ...
                /* Reload account asynchronously to get the up-to-date list. */
                loadAccount();
            }

            @Override
            public void onError(@NonNull MsalException exception) {
                displayError(exception);
            }
        });
```

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account. JSON

Jest to plik konfiguracji aplikacji MSAL, który używa wielu kont.

Wyjaśnienie tych pól można znaleźć w temacie [Omówienie pliku konfiguracji MSAL systemu Android](msal-configuration.md) .

W przeciwieństwie do pliku konfiguracji [auth_config_single_account. JSON](#auth_config_single_accountjson) , ten plik konfiguracji ma `"account_mode" : "MULTIPLE"` zamiast `"account_mode" : "SINGLE"`, ponieważ jest to aplikacja z wieloma kontami.

```json
{
  "client_id" : "<your_client_id_here>",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "<your_redirect_uri_here>",
  "account_mode" : "MULTIPLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Zapoznaj się z instrukcjami dotyczącymi tworzenia aplikacji używanej w tym przewodniku Szybki Start

Wypróbuj Samouczek systemu Android, aby zapoznać się z kompletnym przewodnikiem krok po kroku dotyczącym tworzenia aplikacji i nowych funkcji, w tym pełen opis tego przewodnika Szybki Start.

> [!div class="nextstepaction"]
> [Zadzwoń interfejs API programu Graph samouczka systemu Android](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>MSAL dla biblioteki systemu Android — wiki

Przeczytaj więcej informacji na temat biblioteki MSAL dla systemu Android:

> [!div class="nextstepaction"]
> [MSAL dla biblioteki systemu Android — wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Pomóż nam ulepszyć platformę tożsamości firmy Microsoft. Powiedz nam, co myślisz, wykonując krótką ankietę z dwoma pytaniami.

> [!div class="nextstepaction"]
> [Microsoft Identity platform — ankieta](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
